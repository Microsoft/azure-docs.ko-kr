---
title: Azure managed disks를 사용 하도록 클러스터 노드 업그레이드
description: 클러스터를 거의 또는 전혀 가동 중지 하지 않고 Azure managed disks를 사용 하도록 기존 Service Fabric 클러스터를 업그레이드 하는 방법은 다음과 같습니다.
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: 152bdaea121e65de8332fcde8543b8158ff11714
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717526"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Azure managed disks를 사용 하도록 클러스터 노드 업그레이드

[Azure managed disks](../virtual-machines/managed-disks-overview.md) 는 데이터 영구 저장을 위해 azure virtual machines와 함께 사용 하기 위해 권장 되는 디스크 저장소 제품입니다. 관리 디스크를 사용 하도록 노드 형식의 기반이 되는 가상 머신 확장 집합을 업그레이드 하 여 Service Fabric 워크 로드의 복원 력을 향상 시킬 수 있습니다. 클러스터를 거의 또는 전혀 가동 중지 하지 않고 Azure managed disks를 사용 하도록 기존 Service Fabric 클러스터를 업그레이드 하는 방법은 다음과 같습니다.

관리 디스크를 사용 하도록 Service Fabric 클러스터 노드를 업그레이드 하는 일반적인 전략은 다음과 같습니다.

1. 해당 노드 형식의 중복 된 가상 머신 확장 집합을 배포 하 고, 가상 머신 확장 집합 배포 템플릿의 섹션에 [Manageddisk](/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) 개체를 추가 합니다 `osDisk` . 새 확장 집합은 원본으로 동일한 부하 분산 장치/i d에 바인딩되어야 하므로 마이그레이션 중에 고객이 서비스 중단을 경험 하지 않습니다.

2. 원본 및 업그레이드 된 확장 집합을 모두 나란히 실행 하는 경우에는 원래 노드 인스턴스를 한 번에 하나씩 사용 하지 않도록 설정 하 여 시스템 서비스 (또는 상태 저장 서비스의 복제본)를 새 확장 집합으로 마이그레이션합니다.

3. 클러스터 및 새 노드가 정상 인지 확인 하 고 삭제 된 노드에 대 한 원래 크기 집합 및 노드 상태를 제거 합니다.

이 문서에서는 클러스터 가동 중지 시간을 방지 하는 동시에 관리 디스크를 사용 하도록 예제 클러스터의 주 노드 유형을 업그레이드 하는 단계를 안내 합니다 (아래 참고 참조). 예제 테스트 클러스터의 초기 상태는 노드 5 개로 구성 된 단일 확장 집합에 의해 지원 되는 [실버 내구성](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)의 한 노드 형식으로 구성 됩니다.

> [!NOTE]
> 기본 SKU 부하 분산 장치에 대 한 제한으로 인해 추가 확장 집합이 추가 되지 않습니다. 대신 표준 SKU 부하 분산 장치를 사용 하는 것이 좋습니다. 자세한 내용은 [두 sku의 비교](/azure/load-balancer/skus)를 참조 하세요.

> [!CAUTION]
> 클러스터 DNS에 종속성이 있는 경우 (예: [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)에 액세스할 때)에만이 절차를 중단 합니다. [프런트 엔드 서비스의 아키텍처 모범 사례](/azure/architecture/microservices/design/gateway) 는 중단 없이 노드 교환을 가능 하 게 하기 위해 노드 형식 앞에 일종의 [부하 분산 장치](/azure/architecture/guide/technology-choices/load-balancing-overview) 를 포함 하는 것입니다.

다음은 업그레이드 시나리오를 완료 하는 데 사용 하는 Azure Resource Manager에 대 한 [템플릿 및 cmdlet](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) 입니다. 템플릿의 변경 내용은 아래 [주 노드 유형의 업그레이드 된 확장 집합 배포](#deploy-an-upgraded-scale-set-for-the-primary-node-type)  에서 설명 합니다.

## <a name="set-up-the-test-cluster"></a>테스트 클러스터 설정

초기 Service Fabric 테스트 클러스터를 설정 해 보겠습니다. 먼저이 시나리오를 완료 하는 데 사용할 Azure Resource Manager 샘플 템플릿을 [다운로드](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) 합니다.

다음으로, Azure 계정에 로그인합니다.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

다음 명령은 자체 서명 된 새 인증서를 생성 하 고 테스트 클러스터를 배포 하는 과정을 안내 합니다. 사용 하려는 인증서가 이미 있는 경우 [기존 인증서를 사용](#use-an-existing-certificate-to-deploy-the-cluster)하 여 클러스터를 배포 하는 것으로 건너뜁니다.

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>자체 서명된 인증서 생성 및 클러스터 배포

먼저 Service Fabric 클러스터 배포에 필요한 변수를 할당합니다. 특정 계정 및 환경에 대한 `resourceGroupName`, `certSubjectName`, `parameterFilePath` 및 `templateFilePath`의 값을 조정합니다.

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> 새 Service Fabric 클러스터를 배포하는 명령을 실행하기 전에 로컬 머신에 `certOutputFolder` 위치가 있는지 확인합니다.

다음으로 파일 [* 에*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) 대 한Initial-1NodeType-UnmanagedDisks.parameters.js를 열고 `clusterName` `dnsName` PowerShell에서 설정한 동적 값에 해당 하는 및에 대 한 값을 조정 하 고 변경 내용을 저장 합니다.

그런 다음, Service Fabric 테스트 클러스트를 배포합니다.

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

배포가 완료되면 로컬 머신에서 *.pfx* 파일(`$certPfx`)을 찾아서 인증서 저장소로 가져옵니다.

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

작업은 [새 클러스터에 연결](#connect-to-the-new-cluster-and-check-health-status) 하 고 상태를 확인 하는 데 사용 하는 인증서 지문을 반환 합니다. 클러스터 배포에 대 한 대체 방법인 다음 섹션을 건너뜁니다.

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>기존 인증서를 사용 하 여 클러스터 배포

기존 Azure Key Vault 인증서를 사용 하 여 테스트 클러스터를 배포할 수도 있습니다. 이렇게 하려면 Key Vault 및 인증서 지문에 대 한 [참조를 가져와야](#obtain-your-key-vault-references) 합니다.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

파일 [* 에서Initial-1NodeType-UnmanagedDisks.parameters.js*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) 을 열고 및의 값을 고유한 값으로 변경 합니다 `clusterName` `dnsName` .

마지막으로 클러스터에 대 한 리소스 그룹 이름을 지정 하 고 `templateFilePath` `parameterFilePath` *초기-1Nodetype-UnmanagedDisks* 파일의 및 위치를 설정 합니다.

> [!NOTE]
> 지정 된 리소스 그룹이 이미 존재 하 고 Key Vault와 동일한 지역에 있어야 합니다.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

마지막으로 다음 명령을 실행 하 여 초기 테스트 클러스터를 배포 합니다.

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>새 클러스터에 연결하고 성능 상태 확인

클러스터에 연결 하 고 5 개의 노드가 모두 정상 상태 인지 확인 합니다 ( `clusterName` `thumb` 클러스터에 대 한 및 변수 대체).

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

그러면 업그레이드 절차를 시작할 준비가 된 것입니다.

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>주 노드 유형에 대해 업그레이드 된 확장 집합 배포

노드 유형을 업그레이드 하거나 *수직 확장*하려면 `nodeTypeRef` `subnet` `loadBalancerBackendAddressPools` 원하는 업그레이드/변경 내용 및 별도의 서브넷과 인바운드 NAT 주소 풀을 포함 하는 경우를 제외 하 고는 원래 확장 집합 (동일한, 및에 대 한 참조 포함)과 동일한 해당 노드 형식의 가상 머신 확장 집합의 복사본을 배포 해야 합니다. 주 노드 유형을 업그레이드 하는 중 이므로 새 확장 집합은 원래 확장 집합과 마찬가지로 주 ()로 표시 됩니다 `isPrimary: true` . 주 노드가 아닌 형식 업그레이드의 경우에는이를 생략 하면 됩니다.

편의를 위해 *업그레이드-1NodeType-2ScaleSets-ManagedDisks* [템플릿](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) 및 [매개 변수](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json) 파일에 필요한 변경 내용이 이미 적용 되었습니다.

다음 섹션에서는 템플릿 변경 내용에 대해 자세히 설명 합니다. 원하는 경우 설명을 건너뛰고 [업그레이드 절차의 다음 단계로](#obtain-your-key-vault-references)진행할 수 있습니다.

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>업그레이드 된 확장 집합으로 클러스터 템플릿 업데이트

주 노드 유형에 대해 업그레이드 된 확장 집합을 추가 하기 위한 원본 클러스터 배포 템플릿의 섹션 별 수정 사항은 다음과 같습니다.

#### <a name="parameters"></a>매개 변수

새 확장 집합의 인스턴스 이름, 개수 및 크기에 대 한 매개 변수를 추가 합니다. 는 `vmNodeType1Name` 새 확장 집합에 대해 고유 하지만 개수 및 크기 값은 원래 확장 집합과 동일 합니다.

**템플릿 파일**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**매개 변수 파일**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>variables

배포 템플릿 섹션에서 `variables` 새 확장 집합의 인바운드 NAT 주소 풀에 대 한 항목을 추가 합니다.

**템플릿 파일**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>리소스

배포 템플릿 *리소스* 섹션에서 다음 사항을 염두에 둔 새 가상 머신 확장 집합을 추가 합니다.

* 새 확장 집합은 원래와 동일한 노드 형식을 참조 합니다.

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* 새 확장 집합은 동일한 부하 분산 장치 백 엔드 주소 및 서브넷을 참조 하지만 다른 부하 분산 장치 인바운드 NAT 풀을 사용 합니다.

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* 원래 확장 집합과 마찬가지로 새 확장 집합이 주 노드 형식으로 표시 됩니다. 주 노드가 아닌 노드 유형을 업그레이드 하는 경우에는이 변경을 생략 합니다.

    ```json
    "isPrimary": true,
    ```

* 원래 확장 집합과 달리 새 확장 집합은 관리 디스크를 사용 하도록 업그레이드 됩니다.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

템플릿 및 매개 변수 파일의 모든 변경 내용을 구현한 후 다음 섹션으로 이동 하 여 Key Vault 참조를 가져오고 클러스터에 업데이트를 배포 합니다.

### <a name="obtain-your-key-vault-references"></a>Key Vault 참조 가져오기

업데이트 된 구성을 배포 하려면 먼저 Key Vault에 저장 된 클러스터 인증서에 대 한 여러 참조를 가져옵니다. 이러한 값을 찾는 가장 쉬운 방법은 Azure Portal을 통하는 것입니다. 다음 도구가 필요합니다.

* **클러스터 인증서의 Key Vault URL입니다.** Azure Portal의 Key Vault에서 **Certificates**  >  *원하는 인증서*  >  **비밀 식별자**를 선택 합니다.

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **클러스터 인증서의 지문입니다.** [초기 클러스터에 연결](#connect-to-the-new-cluster-and-check-health-status) 하 여 상태를 확인할 수 있습니다. Azure Portal의 동일한 인증서 블레이드 (**Certificates**  >  *원하는 인증서*의 인증서)에서 **x.509 sha-1 지문 (16 진수)** 을 복사 합니다.

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **Key Vault의 리소스 ID입니다.** Azure Portal의 Key Vault에서 **속성**  >  **리소스 ID**를 선택 합니다.

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>업데이트된 템플릿 배포

필요에 따라 `parameterFilePath` 및 `templateFilePath`를 조정한 후, 다음 명령을 실행합니다.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

배포가 완료 되 면 클러스터 상태를 다시 확인 하 고 10 개 노드 (원래 크기 집합에서 5 개)가 모두 정상 상태 인지 확인 합니다.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>초기값 노드를 새 확장 집합으로 마이그레이션

이제 원래 크기 집합의 노드 비활성화를 시작할 준비가 되었습니다. 이러한 노드가 비활성화되면 시스템 서비스 및 시드 노드가 기본 노드 유형으로 표시되기 때문에 새 확장 집합의 VM으로 마이그레이션됩니다.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Service Fabric Explorer를 사용하여 시드 노드를 새 확장 집합으로 마이그레이션하고 원래 확장 집합의 노드 진행을 *사용하지 않도록 설정 중*에서 *사용 안 함* 상태로 모니터링합니다.

![비활성 노드의 상태를 보여 주는 Service Fabric Explorer](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> 원래 확장 집합의 모든 노드에서 비활성화 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다. 데이터 일관성을 보장하기 위해 한 번에 하나의 시드 노드만 변경할 수 있습니다. 각 시드 노드 변경에는 클러스터 업데이트가 필요합니다. 따라서 시드 노드를 교체하려면 두 개의 클러스터 업그레이드가 필요합니다 (노드 추가 및 제거 시 각각 하나씩). 이 샘플 시나리오에서 5개의 시드 노드를 업그레이드하면 10개의 클러스터 업그레이드가 발생합니다.

## <a name="remove-the-original-scale-set"></a>원래 확장 집합 제거

비활성화 작업이 완료되면 확장 집합을 제거합니다.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

Service Fabric Explorer에서 제거된 노드(따라서 *Cluster Health State*)가 이제 *오류* 상태로 표시됩니다.

![오류 상태의 비활성 노드를 보여 주는 Service Fabric Explorer](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

클러스터 상태를 *정상*상태로 복원 하려면 Service Fabric 클러스터에서 사용 되지 않는 노드를 제거 합니다.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![오류 상태에서 중단 노드가 제거 된 Service Fabric Explorer](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>다음 단계

이 연습에서는 프로세스 중에 서비스가 중단 되는 것을 방지 하면서 관리 디스크를 사용 하도록 Service Fabric 클러스터의 가상 머신 확장 집합을 업그레이드 하는 방법을 배웠습니다. 관련 항목에 대 한 자세한 내용은 다음 리소스를 확인 하세요.

방법 배우기:

* [Service Fabric 클러스터 주 노드 형식 강화](service-fabric-scale-up-primary-node-type.md)

* [관리 디스크를 사용 하도록 확장 집합 템플릿 변환](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Service Fabric 노드 유형 제거](service-fabric-how-to-remove-node-type.md)

참고 항목:

* [샘플: Azure managed disks를 사용 하도록 클러스터 노드 업그레이드](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)

* [수직 크기 조정 관련 고려 사항](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)
