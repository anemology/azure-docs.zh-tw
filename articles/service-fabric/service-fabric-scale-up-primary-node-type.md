---
title: 相應增加 Azure Service Fabric 主要節點類型
description: 瞭解如何藉由新增節點類型來調整 Service Fabric 叢集。
ms.topic: article
ms.date: 08/06/2020
ms.author: pepogors
ms.openlocfilehash: eecf398359470f6e5e151c53eb63b3cb56efbe39
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056749"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type-by-adding-a-node-type"></a>藉由新增節點類型來相應增加 Service Fabric 叢集主要節點類型
本文說明如何藉由將額外的節點類型新增至叢集，來相應增加 Service Fabric 叢集主要節點類型。 Service Fabric 叢集是一組由網路連接的虛擬或實體機器，可用來將您的微服務部署到其中並進行管理。 屬於叢集一部分的機器或 VM 都稱為節點。 虛擬機器擴展集是一個 Azure 計算資源，可以用來將一組虛擬機器當做一個集合加以部署和管理。 在 Azure 叢集中定義的每個節點類型，會[設定為不同的擴展集](service-fabric-cluster-nodetypes.md)。 隨後，您即可個別管理每個節點類型。

下列教學課程中的範例範本可以在這裡找到： [Service Fabric 主要節點類型調整範例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Primary-NodeType-Scaling-Sample)

> [!WARNING]
> 如果叢集狀態狀況不良，請勿嘗試主要節點類型相應增加程式，因為這只會進一步使叢集變得不穩定。
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type"></a>升級主要節點類型的大小和作業系統的程式
以下是更新主要節點類型 Vm 的 VM 大小和作業系統的程式。  升級之後，主要節點類型 Vm 會是大小標準 D4_V2 並執行具有容器的 Windows Server 2019 Datacenter。

> [!WARNING]
> 在對生產叢集嘗試此程序之前，建議您先研究範本範例，並確認要對測試叢集所進行的程序。 叢集可能也會在短時間內無法使用。 

### <a name="deploy-the-initial-service-fabric-cluster"></a>部署初始 Service Fabric 叢集 
如果您想要遵循範例，請部署具有單一主要節點類型的初始叢集，以及單一擴展集[Service Fabric-初始](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-1.json)叢集。 如果您已經部署現有的 Service Fabric 叢集，可以略過此步驟。 

1. 登入您的 Azure 帳戶。 
```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```
2. 建立新的資源群組。 
```powershell
# create a resource group for your cluster deployment
$resourceGroupName = "myResourceGroup"
$location = "WestUS"

New-AzResourceGroup `
    -Name $resourceGroupName
    -Location $location
```
3. 填寫範本檔案中的參數值。 
4. 將叢集部署到步驟2中建立的資源群組。 
```powershell
# deploy the template files to the resource group created above
$templateFilePath = "C:\AzureDeploy-1.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

### <a name="add-a-new-primary-node-type-to-the-cluster"></a>將新的主要節點類型新增至叢集
> [!Note]
> 完成調整作業之後，在下列步驟中建立的資源將成為叢集中的新主要節點類型。 請確定您使用的是初始子網、公用 IP、Load Balancer、虛擬機器擴展集和節點類型中唯一的名稱。 

您可以在這裡找到已完成下列所有步驟的範本： [Service Fabric-新的節點類型](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-2.json)叢集。 下列步驟包含部分資原始程式碼段，可反白顯示新資源中的變更。  

1. 在您現有的虛擬網路中建立新的子網。
```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```
2. 使用唯一的 domainNameLabel 建立新的公用 IP 資源。 
```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt2')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```
3. 建立新的 Load Balancer 資源，這取決於上面建立的公用 IP。 
```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```
4. 建立新的虛擬機器擴展集，以使用新的 VM SKU 和您想要相應增加的 OS SKU。 

節點類型參考 
```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

VM SKU
```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

OS SKU 
```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```
5. 將新的節點類型新增至叢集，此叢集會參考先前建立的虛擬機器擴展集。 此節點類型上的**isPrimary**屬性應該設定為 true。 
```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```
6. 部署已更新的 ARM 範本。 
```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-2.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

當部署完成時，Service Fabric 叢集現在會有兩個節點類型。 

### <a name="remove-the-existing-node-type"></a>移除現有的節點類型 
資源完成部署之後，您就可以開始停用原始主要節點類型中的節點。 當節點停用時，系統服務將會遷移至先前步驟中已部署的新主要節點類型。

1. 將 Service Fabric 叢集資源中的 [主要節點類型] 屬性設定為 [false]。 
```json
{
    "name": "[variables('vmNodeType0Name')]",
    "applicationPorts": {
        "endPort": "[variables('nt0applicationEndPort')]",
        "startPort": "[variables('nt0applicationStartPort')]"
    },
    "clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
    "durabilityLevel": "Bronze",
    "ephemeralPorts": {
        "endPort": "[variables('nt0ephemeralEndPort')]",
        "startPort": "[variables('nt0ephemeralStartPort')]"
    },
    "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
    "isPrimary": false,
    "reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
    "vmInstanceCount": "[parameters('nt0InstanceCount')]"
}
```
2. 使用原始節點類型上已更新的 isPrimary 屬性來部署範本。 您可以在這裡的原始節點類型上，找到主要旗標設為 false 的範本： [Service Fabric-主要節點類型 false](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-3.json)。

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-3.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

3. 停用節點類型0中的節點。 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterConnectionEndpoint `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"


$nodeType = "nt1vm" # specify the name of node type
$nodes = Get-ServiceFabricNode 

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```
* 若是銅級持久性，請等候所有節點進入停用狀態。
* 對於銀級和金級耐久性，某些節點會進入停用狀態，而其餘節點則會處於停用狀態。 檢查處于停用狀態之節點的 [詳細資料] 索引標籤，如果它們全都停滯在確保基礎結構服務分割區的仲裁，則可以安全地繼續進行。

> [!Note]
> 此步驟可能需要一段時間才能完成。 

4. 停止節點類型0上的資料。 
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```
5. 解除配置原始虛擬機器擴展集中的節點 
```powershell
$scaleSetName="nt1vm"
$scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```
> [!Note]
> 如果您已經使用標準 SKU 公用 IP 和標準 SKU 負載平衡器，步驟6和7是選擇性的。 在此情況下，您可以在相同的負載平衡器下擁有多個虛擬機器擴展集/節點類型。 

6. 您現在可以刪除原始 IP，並 Load Balancer 資源。 在此步驟中，您也會更新 DNS 名稱。 

```powershell
$lbname="LB-cluster-name-nt1vm"
$lbResourceType="Microsoft.Network/loadBalancers"
$ipResourceType="Microsoft.Network/publicIPAddresses"
$oldPublicIpName="PublicIP-LB-FE-nt1vm"
$newPublicIpName="PublicIP-LB-FE-nt2vm"

$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbname -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
``` 

7. 更新叢集上的管理端點，以參考新的 IP。 
```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```
8. 從節點類型0移除節點狀態。
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```
9. 從 ARM 範本中的 Service Fabric 資源移除原始的節點類型參考。 
```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```
僅適用于銀級和更高的耐久性叢集，請更新範本中的叢集資源，並設定健康原則以忽略 fabric：/系統應用程式健康狀態，方法是在 [叢集資源屬性] 底下新增 applicationDeltaHealthPolicies，如下所示。 下列原則應忽略現有的錯誤，但不允許新的健全狀況錯誤。
```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```
10. 從 ARM 範本移除所有與原始節點類型相關的其他資源。 如需移除所有這些原始資源的範本，請參閱[Service Fabric-新的節點類型](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-4.json)叢集。

11. 部署已修改的 Azure Resource Manager 範本。 * * 此步驟需要一些時間，通常最多兩小時。 此升級會將設定變更為 InfrastructureService，因此需要重新開機節點。 在此情況下，會忽略 forceRestart。 參數 upgradeReplicaSetCheckTimeout 會指定 Service Fabric 等待分割區處於安全狀態的最長時間（如果尚未處於安全狀態）。 一旦對節點上的所有資料分割進行安全檢查，Service Fabric 會在該節點上繼續進行升級。 參數 upgradeTimeout 的值可以縮短為6小時，但應使用最大安全12小時。
然後驗證入口網站中的 Service Fabric 資源顯示為 [就緒]。 

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-4.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

叢集的主要節點類型現已升級。 確認所有已部署的應用程式都運作正常，且叢集健康狀態良好。

## <a name="next-steps"></a>後續步驟
* 了解如何[向群集新增節點類型](virtual-machine-scale-set-scale-node-type-scale-out.md)
* 深入了解[應用程式延展性](service-fabric-concepts-scalability.md)。
* [將 Azure 叢集相應縮小或相應放大](service-fabric-tutorial-scale-cluster.md)。
* 使用 Fluent Azure 計算 SDK，[以程式設計方式調整 Azure 叢集](service-fabric-cluster-programmatic-scaling.md)。
* [將獨立叢集相應縮小或相應放大](service-fabric-cluster-windows-server-add-remove-nodes.md)。
