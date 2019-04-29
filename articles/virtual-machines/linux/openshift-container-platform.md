---
title: Azure에 OpenShift Container Platform 배포 | Microsoft Docs
description: Azure에서 OpenShift Container Platform을 배포합니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2019
ms.author: haroldw
ms.openlocfilehash: 296bc42313ef80425004d3c9b43c6792cbaf97f4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127007"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Azure에서 OpenShift Container Platform 배포

Azure에서 OpenShift Container Platform을 배포하는 몇 가지 방법 중 하나를 사용할 수 있습니다.

- 필요한 Azure 인프라 구성 요소를 수동으로 배포한 다음, [OpenShift Container Platform 설명서](https://docs.openshift.com/container-platform)를 따를 수 있습니다.
- OpenShift Container Platform 클러스터의 배포를 간소화하는 기존 [Resource Manager 템플릿](https://github.com/Microsoft/openshift-container-platform/)을 사용할 수도 있습니다.
- 다른 방법은 [Azure Marketplace 제품](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)을 사용하는 것입니다.

모든 옵션에 Red Hat 구독이 필요합니다. 배포하는 동안 Red Hat Enterprise Linux 인스턴스는 Red Hat 구독에 등록되고 OpenShift Container Platform에 대한 자격이 포함된 풀 ID에 연결됩니다.
유효한 RHSM(Red Hat Subscription Manager) 사용자 이름, 암호 및 풀 ID가 있어야 합니다. 활성화 키, 조직 ID 및 풀 ID를 사용할 수 있습니다. 이 정보는 https://access.redhat.com에 로그인하여 확인할 수 있습니다.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>OpenShift Container Platform Resource Manager 템플릿을 사용하여 배포

### <a name="private-clusters"></a>전용 클러스터

마스터 부하 분산 장치 (웹 콘솔) 또는 연결 된 공용 IP가 아니라 두 개 있어야 개인 OpenShift 클러스터를 배포의 인프라 부하 분산 장치 (라우터).  일반적으로 개인 클러스터를 사용자 지정 DNS 서버 하지 기본값인 Azure DNS, 사용자 지정 도메인 이름 (예: contoso.com) 및 미리 정의 된 가상 네트워크를 사용합니다.  개인 클러스터에 대 한 모든 적절 한 서브넷 및 DNS 서버 설정을 사용 하 여 가상 네트워크를 미리 구성 해야 합니다.  사용 하 여 **existingMasterSubnetReference**를 **existingInfraSubnetReference**하십시오 **existingCnsSubnetReference**, 및  **existingNodeSubnetReference** 클러스터에서 사용 하기 위해 기존 서브넷을 지정 합니다.

전용 마스터를 선택한 경우 (**masterClusterType**= 개인), 고정 개인 IP에 대 한 지정 해야 합니다. **masterPrivateClusterIp**합니다.  이 IP 마스터 부하 분산 장치의 프런트 엔드에 할당 됩니다.  IP 사용 및 마스터 서브넷의 CIDR 여야 합니다.  **masterClusterDnsType** "custom" 및 DNS 이름을 제공 해야 하는 마스터도 설정 되어 있어야 **masterClusterDns**합니다.  DNS 이름은 정적 개인 IP에 매핑되어야 하며 마스터 노드에서 콘솔에 액세스 하는 데 사용할 합니다.

개인 라우터를 선택 하는 경우 (**routerClusterType**= 개인), 고정 개인 IP에 대 한 지정 해야 합니다. **routerPrivateClusterIp**합니다.  이 IP의 프런트 엔드에 할당할의 인프라 부하 분산 장치.  IP에 대 한 CIDR 내에 있어야 합니다.는 인프라 서브넷과 사용 되지 않습니다.  **routingSubDomainType** 라우팅에 대 한 제공 해야 합니다 "custom" 및 와일드 카드 DNS 이름으로 설정 되어 있어야 **routingSubDomain**합니다.  

전용 마스터 및 개인 라우터를 선택 하는 경우 사용자 지정 도메인 이름을 입력 해야에 대 한 **domainName**

배포 후 요새 노드는 수 있는 ssh에 공용 IP가 있는 유일한 노드입니다.  마스터 노드에 대 한 공용 액세스도 구성 된 경우에 이러한 노출 되지 않습니다에 대 한 ssh 액세스 합니다.

Resource Manager 템플릿을 사용하여 배포하려면 매개 변수 파일을 사용하여 입력 매개 변수를 제공합니다. 배포를 추가로 사용자 지정하려면 GitHub 리포지토리를 포크하고 적절한 항목을 변경합니다.

일반적인 사용자 지정 옵션에는 다음이 포함됩니다(이에 국한되지 않음).

- 요새 VM 크기(azuredeploy.json의 변수)
- 명명 규칙(azuredeploy.json의 변수)
- OpenShift 클러스터 세부 정보, 호스트 파일을 통해 수정 됨(deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>매개 변수 파일 구성

[OpenShift Container Platform 템플릿](https://github.com/Microsoft/openshift-container-platform)에는 각기 다른 OpenShift Container Platform 버전에 사용할 수 있는 여러 분기가 있습니다.  필요에 따라, 리포지토리에서 직접 배포하거나, 배포하기 전에 리포지토리를 포크하고 템플릿 또는 스크립트를 사용자 지정할 수 있습니다.

`aadClientId` 매개 변수에 대해 이전에 만든 서비스 주체에서 `appId` 값을 사용합니다.

다음 예제에서는 모든 필수 입력이 포함된 azuredeploy.parameters.json이라는 매개 변수 파일을 보여 줍니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

매개 변수를 자신의 특정 정보로 바꿉니다.

릴리스마다 다른 매개 변수를 사용할 수 있으므로, 사용하는 분기에 필요한 매개 변수를 확인합니다.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy 합니다. Parameters.json 파일 설명

| 자산 | 설명 | 유효한 옵션 | 기본값 |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | 아티팩트 (json, 스크립트 등)에 대 한 URL |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | 리소스를 배포할 azure 지역 |  |  |
| `masterVmSize` | 마스터 VM의 크기입니다. Azuredeploy.json 파일에 나열 된 허용 되는 VM 크기 중 하나를 선택 합니다. |  | Standard_E2s_v3 |
| `infraVmSize` | 크기는 인프라 VM. Azuredeploy.json 파일에 나열 된 허용 되는 VM 크기 중 하나를 선택 합니다. |  | Standard_D4s_v3 |
| `nodeVmSize` | 앱 노드 VM의 크기입니다. Azuredeploy.json 파일에 나열 된 허용 되는 VM 크기 중 하나를 선택 합니다. |  | Standard_D4s_v3 |
| `cnsVmSize` | 컨테이너 (CN) 한 네이티브 저장소 노드 VM의 크기입니다. Azuredeploy.json 파일에 나열 된 허용 되는 VM 크기 중 하나를 선택 합니다. |  | Standard_E4s_v3 |
| `osImageType` | RHEL 이미지 사용입니다. defaultgallery: 주문형; marketplace: 제 3 자 이미지 | defaultgallery <br> Marketplace | defaultgallery |
| `marketplaceOsImage` | 경우 `osImageType` marketplace 않으면 'publisher', '제품', 'sku', '버전'의 marketplace 제품에 대 한 적절 한 값을 입력 합니다. 이 매개 변수는 개체 형식 |  |  |
| `storageKind` | 사용할 저장소 유형  | 관리<br> unmanaged | 관리 |
| `openshiftClusterPrefix` | 모든 노드에 대 한 호스트 이름을 구성 하는 데 사용 되는 접두사를 클러스터 합니다.  1 ~ 20 자 사이의 |  | mycluster |
| `minoVersion` | OpenShift 컨테이너 플랫폼 3.11 배포의 부 버전 |  | 69 |
| `masterInstanceCount` | 배포 하려면 마스터 노드 수 | 1, 3, 5 | 3 |
| `infraInstanceCount` | 수가 인프라를 배포 하는 노드 | 1, 2, 3 | 3 |
| `nodeInstanceCount` | 배포할 노드 수 | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | CN 배포할 노드 수 | 3, 4 | 3 |
| `osDiskSize` | OS 디스크 (GB)에서 vm의 크기 | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Docker 볼륨 (GB)에 대 한 노드를 연결할 데이터 디스크의 크기 | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | GB (에서 glusterfs 하 여 사용에 대 한 CN 노드에 연결할 데이터 디스크의 크기 | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | OS (VM) 로그인 및 초기 OpenShift 사용자 모두에 대 한 관리자 사용자 이름 |  | ocpadmin |
| `enableMetrics` | 메트릭을 사용 하도록 설정 합니다. 더 많은 리소스 이므로 VM 인프라에 대 한 적절 한 크기를 선택 해야 하는 메트릭 | true <br> false | false |
| `enableLogging` | 로깅을 사용 하도록 설정 합니다. elasticsearch pod 8GB RAM 인프라 VM에 대 한 적절 한 크기를 선택 하므로 필요 | true <br> false | false |
| `enableCNS` | 기본 저장소 컨테이너를 사용 하도록 설정 | true <br> false | false |
| `rhsmUsernameOrOrgId` | Red Hat 구독 관리자 사용자 이름 또는 조직 ID |  |  |
| `rhsmPoolId` | 계산 노드에 대 한 OpenShift 자격을 포함 하는 Red Hat Subscription Manager 풀 ID |  |  |
| `rhsmBrokerPoolId` | OpenShift 자격 및 인프라 마스터에 대 한 노드를 포함 하는 Red Hat Subscription Manager 풀 ID입니다. 다른 풀 Id가 없는 경우 'rhsmPoolId'와 같은 풀 ID 입력 |  |
| `sshPublicKey` | SSH 공용 여기에 키를 복사 합니다. |  |  |
| `keyVaultSubscriptionId` | Key Vault를 포함 하는 구독의 구독 ID |  |  |
| `keyVaultResourceGroup` | Key Vault를 포함 하는 리소스 그룹의 이름 |  |  |
| `keyVaultName` | 만든 Key Vault의 이름 |  |  |
| `enableAzure` | Azure 클라우드 공급자를 사용 하도록 설정 | true <br> false | true |
| `aadClientId` | 응용 프로그램 ID로 알려진 된 또한 서비스 주체에 대 한 azure Active Directory 클라이언트 ID |  |  |
| `domainName` | (있는 경우)를 사용 하 여 사용자 지정 도메인의 이름입니다. "None" 되지 않은 경우 배포 완벽 하 게 개인 클러스터를 설정 합니다. |  | 없음 |
| `masterClusterDnsType` | OpenShift 웹 콘솔에 대 한 도메인 형식입니다. 'default'를 사용 하 여 마스터의 DNS 레이블을 인프라 공용 IP입니다. '사용자 지정'에서는 고유한 이름을 정의할 수 있습니다. | 기본값 <br> 사용자 지정 | 기본값 |
| `masterClusterDns` | 에 대 한 ' 사용자 지정'을 선택한 경우 OpenShift 웹 콘솔에 액세스 하는 데 사용자 지정 DNS 이름 `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | 경우 'nipio'로 `routingSubDomain` nip.io를 사용 합니다.  라우팅에 사용 하려는 고유한 도메인이 있는 경우에 'custom'를 사용 하 여 | nipio <br> 사용자 지정 | nipio |
| `routingSubDomain` | 라우팅에 대 한 ' 사용자 지정'을 선택한 경우에 사용 하려는 와일드 카드 DNS 이름 `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | 기존 가상 네트워크를 사용 하거나 새 가상 네트워크를 만들 것인지 선택 | 기존 <br> 신규 | 신규 |
| `virtualNetworkResourceGroupName` | 새 가상 네트워크에 대 한 'new' 선택한 경우에 대 한 리소스 그룹의 이름 `virtualNetworkNewOrExisting` |  | resourceGroup().name |
| `virtualNetworkName` | 에 대 한 'new' 선택한 경우 만드는 새 가상 네트워크의 이름 `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | 새 가상 네트워크의 주소 접두사 |  | 10.0.0.0/14 |
| `masterSubnetName` | 마스터 서브넷의 이름 |  | mastersubnet |
| `masterSubnetPrefix` | -마스터 서브넷에 사용 되는 CIDR의 addressPrefix의 하위 집합일 수 해야 합니다. |  | 10.1.0.0/16 |
| `infraSubnetName` | 이름을 인프라 서브넷 |  | infrasubnet |
| `infraSubnetPrefix` | 에 사용 되는 CIDR는 서브넷-가 addressPrefix의 하위 집합일 수 해야 하는 인프라 |  | 10.2.0.0/16 |
| `nodeSubnetName` | 노드 서브넷의 이름 |  | nodesubnet |
| `nodeSubnetPrefix` | 노드 서브넷-에 사용 되는 CIDR의 addressPrefix의 하위 집합일 수 해야 합니다. |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | 마스터 노드에 대 한 기존 서브넷에 대 한 전체 참조입니다. 새 vNet을 만드는 경우 필요 하지 않습니다 / 서브넷 |  |  |
| `existingInfraSubnetReference` | 노드 인프라에 대 한 기존 서브넷에 대 한 참조를 전체. 새 vNet을 만드는 경우 필요 하지 않습니다 / 서브넷 |  |  |
| `existingCnsSubnetReference` | CN 노드에 대 한 기존 서브넷에 대 한 전체 참조입니다. 새 vNet을 만드는 경우 필요 하지 않습니다 / 서브넷 |  |  |
| `existingNodeSubnetReference` | 계산 노드에 대 한 기존 서브넷에 대 한 전체 참조입니다. 새 vNet을 만드는 경우 필요 하지 않습니다 / 서브넷 |  |  |
| `masterClusterType` | 클러스터는 사설 또는 공용 마스터 노드를 사용 하는지 여부를 지정 합니다. 개인을 선택 하면 공용 IP 통해 인터넷에 마스터 노드를 노출 하지 않습니다. 에 지정 된 개인 IP 대신 사용 합니다 `masterPrivateClusterIp` | 공공 <br> 개인 | 공공 |
| `masterPrivateClusterIp` | 전용 마스터 노드를 선택 하는 경우 개인 IP 주소를 지정 해야 합니다 사용에 대 한 마스터 노드에 대 한 내부 부하 분산 장치가 있습니다. 이 정적 IP 및 아직 사용 하 여 마스터 서브넷의 CIDR 블록 내에 있어야 합니다. 이 값 사용 되지 않지만 계속 지정 해야 합니다 공용 마스터 노드를 선택 하는 경우 |  | 10.1.0.200 |
| `routerClusterType` | 클러스터 사용할지 개인 또는 공용 인프라 노드를 지정 합니다. 개인을 선택한 경우는 노드 공용 IP 통해 인터넷에 노출 되지 않는다는 인프라입니다. 에 지정 된 개인 IP 대신 사용 합니다 `routerPrivateClusterIp` | 공공 <br> 개인 | 공공 |
| `routerPrivateClusterIp` | 개인 인프라 노드는 선택한 개인 IP 주소를 지정 해야 하는 경우에 대 한 내부 부하 분산 장치에서 인프라 노드 사용 합니다. 이 정적 IP 및 아직 사용 하 여 마스터 서브넷의 CIDR 블록 내에 있어야 합니다. 공용 노드를 선택 하는 인프라,이 값이 사용 되지 않습니다 되지만 계속 지정 해야 합니다. |  | 10.2.0.200 |
| `routingCertType` | 사용자 지정 인증서를 사용 하 여 라우팅 도메인 또는 기본 자체 서명 된 인증서에 대 한-지침을 따릅니다 **사용자 지정 인증서** 섹션 | selfsigned <br> 사용자 지정 | selfsigned |
| `masterCertType` | 사용자 지정 인증서를 사용 하 여 마스터 도메인 또는 기본 자체 서명 된 인증서에 대 한-지침을 따릅니다 **사용자 지정 인증서** 섹션 | selfsigned <br> 사용자 지정 | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Azure CLI를 사용하여 배포

> [!NOTE] 
> 다음 명령은 Azure CLI 2.0.8 이상이 필요합니다. CLI 버전은 `az --version` 명령으로 확인할 수 있습니다. CLI 버전을 업데이트하려면 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti)를 참조하세요.

다음 예제에서는 openshiftrg라는 리소스 그룹에 OpenShift 클러스터 및 모든 관련 리소스를 배포하고 배포 이름을 myOpenShiftCluster로 지정합니다. 템플릿은 GitHub 리포지토리에서 직접 참조되며, azuredeploy.parameters.json이라는 로컬 매개 변수 파일이 사용됩니다.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

배포 노드 배포 및 구성 옵션의 총 수에 따라를 완료 하려면 적어도 60 분이 걸립니다. 배포를 마치면 OpenShift 콘솔의 Bastion DNS FQDN 및 URL이 터미널에 출력됩니다.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

배포가 완료될 때까지 명령줄을 대기하지 않으려면 그룹 배포에 대한 옵션 중 하나로 `--no-wait`를 추가합니다. 배포의 출력은 Azure Portal의 리소스 그룹에 대한 배포 섹션에서 검색할 수 있습니다.

## <a name="connect-to-the-openshift-cluster"></a>OpenShift 클러스터에 연결

배포가 완료되면 배포의 출력 섹션에서 연결을 검색합니다. 사용 하 여 브라우저를 사용 하 여 OpenShift 콘솔에 연결 합니다 **OpenShift 콘솔 URL**합니다. 요새 호스트에 SSH 수도 있습니다. 다음은 관리자 사용자 이름이 clusteradmin이고 bastion 공용 IP DNS FQDN이 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com인 예제입니다.

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, OpenShift 클러스터 및 모든 관련된 리소스가 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group) 명령을 사용하여 제거합니다.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>다음 단계

- [배포 후 작업](./openshift-post-deployment.md)
- [Azure에서 OpenShift 배포 문제 해결](./openshift-troubleshooting.md)
- [OpenShift Container Platform 시작](https://docs.openshift.com/container-platform)
