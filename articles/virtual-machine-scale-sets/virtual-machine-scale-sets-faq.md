---
title: Azure 가상 머신 확장 집합에 대한 FAQ | Microsoft Docs
description: 가상 머신 확장 집합에 대한 FAQ(질문과 대답)에 대해 알아봅니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2019
ms.author: manayar
ms.custom: na
ms.openlocfilehash: 56a31770c374cdccaec4dbee751925a6da00fa59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620246"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Azure 가상 머신 확장 집합에 대한 FAQ

Azure의 가상 머신 확장 집합에 대한 FAQ(질문과 대답)에 대해 알아봅니다.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>확장 집합에 대한 상위 질문과 대답

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>크기 집합에 포함할 수 있는 VM 수는 몇 개인가요?

확장 집합에는 플랫폼 이미지에 기반하여 0~1,000대의 VM, 사용자 지정 이미지에 기반하여 0~600대의 VM을 포함할 수 있습니다.

### <a name="are-data-disks-supported-within-scale-sets"></a>크기 집합 내에서 데이터 디스크가 지원되나요?

예. 크기 집합은 집합에서 모든 VM에 적용되는 연결된 데이터 디스크 구성을 정의할 수 있습니다. 자세한 내용은 [}Azure 확장 집합 및 연결된 데이터 디스크](virtual-machine-scale-sets-attached-disks.md)를 참조하세요. 데이터를 저장하는 기타 옵션은 다음과 같습니다.

* Azure 파일(SMB 공유 드라이브)
* OS 드라이브
* 임시 드라이브(Azure Storage에서 지원되지 않는 로컬 드라이브)
* Azure 데이터 서비스(예: Azure 테이블, Azure Blob)
* 외부 데이터 서비스(예: 원격 데이터베이스)

### <a name="which-azure-regions-support-scale-sets"></a>크기 집합을 지원하는 Azure 지역은 어디인가요?

모든 지역에서 확장 집합을 지원합니다.

### <a name="how-do-i-create-a-scale-set-by-using-a-custom-image"></a>사용자 지정 이미지를 사용하여 크기 집합을 어떻게 만드나요?

VM 이미지를 만들고 캡처한 다음, 확장 집합에 대한 원본으로 사용합니다. 사용자 지정 VM 이미지를 만들고 사용하는 방법에 대한 자습서의 경우 [Azure CLI](tutorial-use-custom-image-cli.md) 또는 [Azure PowerShell](tutorial-use-custom-image-powershell.md)을 사용할 수 있습니다.

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>내 크기 집합 용량을 20에서 15로 줄이면, 어떤 VM이 제거되나요?

가용성을 최대화하기 위해 업데이트 도메인과 장애 도메인이 균등하도록 가상 머신이 확장 집합에서 제거됩니다. ID가 가장 높은 VM이 먼저 제거됩니다.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>그런 다음 용량을 15에서 18로 늘리면 어떻게 되나요?

용량을 18로 늘리면 3개의 새로운 VM 생성됩니다. VM 인스턴스 ID는 이전의 가장 큰 값에서 증가합니다(예: 20, 21, 22). VM은 장애 도메인과 업데이트 도메인에 균등하게 분배됩니다.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>크기 집합에서 여러 확장을 사용하는 경우, 실행 순서를 강제로 적용할 수 있나요?

예, [확장 시퀀싱](virtual-machine-scale-sets-extension-sequencing.md) 확장 집합을 사용할 수 있습니다.

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>크기 집합은 Azure 가용성 집합과 작업이 가능한가요?

지역(비 영역) 확장 집합은 *배치 그룹*을 사용합니다. 5개의 장애 도메인과 5개의 업데이트 도메인이 있는 암시적 가용성 집합으로 역할을 합니다. 100개 이상의 VM 확장 집합은 여러 배치 그룹으로 확장합니다. 배치 그룹에 대한 자세한 내용은 [대규모 가상 머신 크기 집합과 작동](virtual-machine-scale-sets-placement-groups.md)을 참조하세요. VM의 가용성 집합은 동일한 가상 네트워크에 VM의 확장 집합으로 존재할 수 있습니다. 일반적인 구성은 가용성 집합에서 고유한 구성이 필요한 제어 노드 VM을 배치하고 확장 집합에 데이터 노드를 배치하는 것입니다.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>확장 집합은 Azure 가용성 영역과 작업이 가능한가요?

예! 자세한 내용은 [확장 집합 영역 문서](./virtual-machine-scale-sets-use-availability-zones.md)를 참조하세요.


## <a name="autoscale"></a>자동 크기 조정

### <a name="what-are-best-practices-for-azure-autoscale"></a>Azure 자동 크기 조정에 대한 모범 사례는 무엇인가요?

자동 크기 조정에 대한 모범 사례는 [가상 머신 자동 크기 조정에 대한 모범 사례](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices)를 참조하세요.

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>호스트 기반 메트릭을 사용하는 자동 크기 조정에 대한 메트릭 이름은 어디에서 찾을 수 있나요?

호스트 기반 메트릭을 사용하는 자동 크기 조정에 대한 메트릭 이름은 [Azure Monitor에서 지원되는 메트릭](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/)을 참조하세요.

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Azure Service Bus 토픽과 큐 길이를 기준으로 하는 자동 크기 조정의 예가 있나요?

예. Azure Service Bus 토픽과 큐 길이를 기준으로 하는 자동 크기 조정의 예는 [Azure Monitor 자동 크기 조정 공용 메트릭](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)을 참조하세요.

Service Bus 큐를 모니터링하려면 다음 JSON을 수행합니다.

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

저장소 큐의 경우 다음 JSON을 사용합니다.

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

예제 값을 리소스 URI(Uniform Resource Identifier)로 바꿉니다.


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>자동 크기 조정을 수행할 때 호스트 기반 메트릭과 진단 확장 중에서 어떤 방식을 사용하는 것이 바람직한가요?

VM에 자동 크기 조정 설정을 만들면 호스트 수준 메트릭 또는 게스트 OS 기반 메트릭을 사용할 수 있습니다.

지원되는 메트릭 목록에 대해서는 [Azure Monitor 자동 크기 조정 공용 메트릭](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics)을 참조하세요.

가상 머신 확장 집합의 전체 샘플을 보려면 [가상 머신 확장 집합에 대해 Resource Manager 템플릿을 사용하여 고급 자동 크기 조정 구성](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets)을 참조하세요.

샘플에서는 호스트 수준의 CPU 메트릭 및 메시지 개수 메트릭을 사용합니다.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에 대해 경고 규칙을 설정하려면 어떻게 해야 하나요?

PowerShell 또는 Azure CLI를 통해 가상 머신 확장 집합의 메트릭에 대해 경고를 만들 수 있습니다. 자세한 내용은 [Azure Monitor PowerShell 빠른 시작 샘플](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) 및 [Azure Monitor 플랫폼 간 CLI 빠른 시작 샘플](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts)을 참조하세요.

가상 머신 확장 집합의 TargetResourceId는 다음과 같습니다.

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

경고를 설정할 메트릭으로 어떤 VM 성능 카운터도 선택할 수 있습니다. 자세한 내용은 [Azure Monitor 자동 크기 조정 공용 메트릭](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/) 문서의 [Resource Manager 기반 Windows VM에 대한 게스트 OS 메트릭](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) 및 [Linux VM에 대한 게스트 OS 메트릭](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms)을 참조하세요.

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>PowerShell을 사용하여 가상 머신 확장 집합에 대해 자동 크기 조정을 설정하려면 어떻게 하나요?

PowerShell을 사용하여 가상 머신 확장 집합에 대해 자동 크기 조정을 설정하려면 [가상 머신 확장 집합의 크기를 자동으로 조정](tutorial-autoscale-powershell.md)을 참조하세요. [Azure CLI](tutorial-autoscale-cli.md) 및 [Azure 템플릿](tutorial-autoscale-template.md)으로 자동 크기 조정을 구성할 수도 있습니다.


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>VM을 중지(할당 취소)한 경우 VM이 자동 크기 조정 작업의 일부로 시작되었나요?

아니요. 자동 크기 조정 규칙에 확장 집합의 일부로 추가 VM 인스턴스가 필요한 경우 새 VM 인스턴스가 생성됩니다. 중지(할당 취소)된 VM 인스턴스는 자동 크기 조정 이벤트의 일부로 시작되지 않습니다. 그러나 중지(할당 취소)된 VM은 VM 인스턴스 ID의 순서에 따라 모든 VM 인스턴스를 삭제할 수 있는 것과 같은 방식으로, 인스턴스 수에 따라 자동 크기 조정 이벤트의 일부로 삭제할 수 있습니다.



## <a name="certificates"></a>인증서

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm-how-do-i-provision-a-virtual-machine-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration-the-common-certificate-rotation-operation-would-be-almost-the-same-as-a-configuration-update-operation-do-you-have-an-example-of-how-to-do-this"></a>VM에 인증서를 안전하게 배달하려면 어떻게 해야 하나요? 인증서 구성에서 가상 머신 확장 집합을 프로비전하여 웹 사이트의 SSL이 안전하게 배달되는 웹 사이트를 실행하려면 어떻게 해야 하나요? (일반적인 인증서 순환 작업은 구성 업데이트 작업과 거의 동일합니다.) 이 작업을 수행하는 방법을 보여 주는 예제가 있나요?

VM에 인증서를 안전하게 전달하기 위해 고객의 Key Vault에서 Windows 인증서 저장소로 직접 고객 인증서를 설치할 수 있습니다.

다음 JSON을 사용합니다.

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

코드는 Windows와 Linux를 모두 지원합니다.

자세한 내용은 [가상 머신 확장 집합 만들기 또는 업데이트](https://msdn.microsoft.com/library/mt589035.aspx)를 참조하세요.


### <a name="example-of-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Azure Service Fabric 클러스터용으로 프로비전된 자체 서명 인증서 예제입니다.
최신 예제의 경우 azure shell에서 다음 azure CLI 문을 사용하고, stdout으로 출력되는 Service Fabric CLI 모듈 예제 설명서를 읽어보세요.

```bash
az sf cluster create -h
```

Azure의 최신 API 지원 인증서 작업에 대해서는 keyvaults 설명서를 검토하세요.

자체 서명 인증서는 인증 기관에서 제공하는 분산 트러스트에 사용할 수 없으며, 엔터프라이즈 프로덕션 솔루션을 호스트하기 위한 어떤 Service Fabric 클러스터에도 사용하지 말아야 합니다. 추가 Service Fabric 보안 지침에 대해서는 [Azure Service Fabric 보안 모범 사례](https://docs.microsoft.com/azure/security/azure-service-fabric-security-best-practices) 및 [Service Fabric 클러스터 보안 시나리오](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)를 검토하세요.

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Resource Manager 템플릿에서 Linux 가상 머신 확장 집합으로 SSH 인증에 사용하려는 SSH 키 쌍을 지정할 수 있나요?

예. **osProfile**에 대한 REST API는 표준 VM REST API와 비슷합니다.

템플릿에 **osProfile**을 포함합니다.

```json
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```

이 JSON 블록은 [101-vm-sshkey GitHub 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json)에서 사용됩니다.

또한 OS 프로필은 [grelayhost.json GitHub 빠른 시작 템플릿](https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json)에서도 사용됩니다.

자세한 내용은 [가상 머신 확장 집합 만들기 또는 업데이트](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration)를 참조하세요.

### <a name="how-do-i-remove-deprecated-certificates"></a>사용되지 않는 인증서를 제거하려면 어떻게 합니까?

사용되지 않는 인증서를 제거하려면 자격 증명 모음 인증서 목록에서 이전 인증서를 제거합니다. 목록에서 컴퓨터에 유지하려는 모든 인증서를 그대로 둡니다. 이렇게 하면 모든 VM에서 인증서가 제거되지 않습니다. 또한 가상 머신 확장 집합에서 만들어진 새 VM에 인증서가 추가되지 않습니다.

기존 VM에서 인증서를 제거하려면 인증서 저장소에서 인증서를 수동으로 제거하는 사용자 지정 스크립트 확장을 작성합니다.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning-i-want-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-use-them-in-my-resource-manager-template"></a>프로비전하는 동안 기존 SSH 공개 키를 가상 머신 확장 집합 SSH 계층에 삽입하려면 어떻게 하나요? SSH 공개 키 값을 Azure Key Vault에 저장하고 이를 내 Resource Manager 템플릿에서 활용하고 싶습니다.

VM에 공개 SSH 키만 프로비전하는 경우 Key Vault에 공개 키를 적용할 필요가 없습니다. 공개 키는 비밀이 아닙니다.

Linux VM을 만들 때 일반 텍스트로 SSH 공개 키를 제공할 수 있습니다.

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
}
```

linuxConfiguration 요소 이름 | 필수 | 형식 | 설명
--- | --- | --- | --- 
ssh | 아닙니다. | 컬렉션 | Linux OS용 SSH 키 구성을 지정합니다.
경로 | 예 | 문자열 | SSH 키 또는 인증서를 배치해야 하는 Linux 파일 경로를 지정합니다.
keyData | 예 | 문자열 | base64로 인코딩된 SSH 공개 키를 지정합니다.

예제는 [101-vm-sshkey GitHub 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json)을 참조하세요.

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>동일한 Key Vault에서 둘 이상의 인증서를 추가한 후에 `Update-AzVmss`를 실행하면 다음과 같은 메시지가 나타납니다.

>업데이트-AzVmss: 반복 된 인스턴스를 포함 하는 비밀을 나열\<내 구독 id > / resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev 허용 되지 않습니다.

이 기존 원본 자격 증명 모음에 대해 새 자격 증명 모음 인증서를 사용하는 대신, 동일한 자격 증명 모음을 다시 추가하려고 하면 이러한 현상이 발생할 수 있습니다. 다른 비밀을 더 추가하는 경우 `Add-AzVmssSecret` 명령은 제대로 작동하지 않습니다.

동일한 키 자격 증명 모음에서 더 많은 비밀을 추가하려면 $vmss.properties.osProfile.secrets[0].vaultCertificates 목록을 업데이트해야 합니다.

필요한 입력 구조에 대한 자세한 내용은 [가상 컴퓨터 설정 만들기 또는 업데이트](https://msdn.microsoft.com/library/azure/mt589035.aspx)를 참조하세요.

Key Vault에 있는 가상 머신 확장 집합 개체에서 암호를 찾습니다. 그런 다음 인증서 참조(비밀 저장소 이름 및 URL)를 자격 증명 모음과 관련된 목록에 추가합니다.

> [!NOTE]
> 현재 가상 머신 확장 집합 API를 사용하여 VM에서 인증서를 제거할 수 없습니다.
>

새 VM은 이전 인증서를 갖지 않게 됩니다. 그러나 인증서가 있는 있고 이미 배포된 VM은 이전 인증서를 갖게 됩니다.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>인증서가 현재 SecretStore에 있을 때 암호를 제공하지 않고 인증서를 가상 머신 확장 집합에 푸시할 수 있나요?

스크립트에 하드 코드된 암호를 포함할 필요는 없습니다. 배포 스크립트를 실행하는 데 사용하는 권한으로 암호를 동적으로 검색할 수 있습니다. 인증서를 비밀 저장소에서 Key Vault로 이동하는 스크립트가 있는 경우 secret store `get certificate` 명령을 실행하면 .pfx 파일의 암호도 출력됩니다.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>가상 머신 확장 집합에 대한 virtualMachineProfile.osProfile의 Secrets 속성은 어떻게 작동합니까? certificateUrl 속성을 사용하여 인증서에 대한 절대 URI를 지정해야 하는 경우 sourceVault 값이 필요한 이유는 무엇인가요?

Win RM(Windows 원격 관리) 인증서 참조는 OS 프로필의 Secrets 속성에 있어야 합니다.

원본 자격 증명 모음을 지정하는 이유는 사용자의 Azure 클라우드 서비스 모델에 존재하는 ACL(액세스 제어 목록) 정책을 적용한다는 것입니다. 원본 자격 증명 모음을 지정하지 않으면 Key Vault에 비밀을 배포하고 액세스할 수 있는 권한이 없는 사용자는 CRP(Compute 리소스 공급자)를 통해 배포하고 액세스할 수 있습니다. ACL은 존재하지 않는 리소스에 대해서도 존재합니다.

잘못된 sourceVault ID를 제공했지만 유효한 Key Vault URL을 제공한 경우 작업을 폴링할 때 오류가 보고됩니다.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>기존 가상 머신 확장 집합에 비밀을 추가하면 비밀이 기존 VM에 삽입되나요? 아니면 새 VM에만 삽입되나요?

인증서는 기존 VM을 포함하여 모든 VM에 추가됩니다. 가상 머신 확장 집합 upgradePolicy 속성을 **manual**로 설정하면 VM에서 수동 업데이트를 수행할 때 인증서가 해당 VM에 추가됩니다.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Linux VM에 대한 인증서는 어디에서 추가하나요?

Linux VM에 대한 인증서를 배포하는 방법을 알아보려면 [고객이 관리하는 Key Vault에서 VM에 인증서 배포](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/)를 참조하세요.

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>새 자격 증명 모음 인증서는 새 인증서 개체에 추가하려면 어떻게 하나요?

기존 비밀에 자격 증명 모음 인증서를 추가하려면 다음 PowerShell 예제를 참조하세요. 비밀 개체를 하나만 사용합니다.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>VM을 이미지로 다시 설치하면 인증서는 어떻게 됩니까?

VM을 이미지로 다시 설치할 경우 인증서는 삭제됩니다. 이미지로 다시 설치하면 전체 OS 디스크가 삭제됩니다.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>키 자격 증명 모음에서 인증서를 삭제하면 어떻게 됩니까?

키 자격 증명 모음에서 비밀을 삭제하고 모든 VM의 `stop deallocate`를 실행한 다음, 다시 시작하면 오류가 발생합니다. 이 오류는 CRP가 Key Vault에서 비밀을 검색해야 하기 때문에 발생합니다. 이 시나리오에서는 가상 머신 확장 집합 모델에서 인증서를 삭제할 수 있습니다.

CRP 구성 요소는 고객 비밀을 유지하지 않습니다. 가상 머신 확장 집합의 모든 VM에 대해 `stop deallocate`를 실행하면 캐시가 삭제됩니다. 이 시나리오에서는 Key Vault에서 비밀이 검색됩니다.

Azure Service Fabric에 비밀의 캐시 복사본이 있으므로 확장할 때 이 문제가 발생하지 않습니다(단일 패브릭 테넌트 모델).

### <a name="why-do-i-have-to-specify-the-exact-location-for-the-certificate-url-httpsname-of-the-vaultvaultazurenet443secretsexact-location-as-indicated-in-service-fabric-cluster-security-scenarioshttpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>인증서 URL에 대 한 정확한 위치를 지정 해야 하 수행 이유 (https:\/\/\<자격 증명 모음 이름 >.vault.azure.net:443/secrets/\<정확한 위치 >)에 표시 된 대로, [Service Fabric 클러스터 보안 시나리오](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)?

Azure Key Vault 설명서에 따르면, 버전이 지정되지 않은 경우 Get Secret REST API는 최신 버전의 암호를 반환해야 합니다.

방법 | URL
--- | ---
GET | <https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}>

{*secret-name*}을 검색하려는 비밀의 이름으로 바꾸고 {*secret-version*}을 해당 버전으로 바꿉니다. 암호 버전은 제외할 수도 있습니다. 이 경우 현재 버전이 검색됩니다.

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Key Vault를 사용할 때 인증서 버전을 지정해야 하는 이유는 무엇입니까?

Key Vault에서 인증서 버전을 지정하도록 요구하는 이유는 해당 VM에 배포된 인증서를 사용자에게 명확히 알리기 위한 것입니다.

VM을 만든 다음 Key Vault에서 비밀을 업데이트하면 새 인증서가 VM에 다운로드되지 않습니다. 그러나 VM에서 새 인증서를 참조하는 것처럼 보일 것이며, 새 VM에서 새 비밀을 가져오게 됩니다. 이를 방지하려면 비밀 버전을 참조해야 합니다.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>여기서는 .cer 공개 키로 배포되는 여러 인증서로 작업합니다. 가상 머신 확장 집합에 이러한 인증서를 배포하는 권장 방법은 무엇인가요?

.cer 공개 키를 가상 머신 확장 집합에 배포하려면 .cer 파일만 포함하는 .pfx 파일을 생성할 수 있습니다. 이렇게 하려면 `X509ContentType = Pfx`를 사용합니다. 예를 들어 C# 또는 PowerShell에서 .cer 파일을 x509Certificate2 개체로 로드하고 이 메서드를 호출합니다.

자세한 내용은 [X509Certificate.Export 메서드(X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx))를 참조하세요.

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-most-other-resource-providers-have-this-option"></a>사용자가 인증서를 base64 문자열로 전달하는 옵션이 표시되지 않습니다. 대부분의 다른 리소스 공급자는 이 옵션을 제공합니다.

인증서를 base64 문자열로 전달하는 것을 에뮬레이트하려면 Resource Manager 템플릿에서 최신 버전이 지정된 URL을 추출할 수 있습니다. Resource Manager 템플릿에 다음 JSON 속성을 포함합니다.

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Key Vault의 JSON 개체에서 인증서를 래핑해야 하나요?

가상 머신 확장 집합 및 VM에서 인증서가 JSON 개체에 래핑되어야 합니다.

또한 콘텐츠 형식 application/x-pkcs12도 지원됩니다.

현재 .cer 파일은 지원하지 않습니다. .cer 파일을 사용하려면 .pfx 컨테이너로 내보냅니다.



## <a name="compliance-and-security"></a>규정 준수 및 보안

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>가상 머신 확장 집합은 PCI 규정을 준수하나요?

가상 머신 확장 집합은 CRP 위의 얇은 API 계층에 해당합니다. 두 구성 요소 모두 Azure 서비스 트리에서 Compute 플랫폼에 속합니다.

따라서 규정 준수 관점에서 볼 때 가상 머신 확장 집합은 Azure Compute 플랫폼의 기본 요소입니다. 이들은 CRP 자체와 팀, 도구, 프로세스, 배포 방법, 보안 제어, JIT(Just-In-Time) 컴파일, 모니터링, 경고 등을 공유합니다. 가상 머신 확장 집합은 CRP가 현재 PCI DSS(데이터 보안 표준) 증명의 일부이므로 PCI(Payment Card Industry) 규격입니다.

자세한 내용은 [Microsoft 보안 센터](https://www.microsoft.com/TrustCenter/Compliance/PCI)를 참조하세요.

### <a name="does-managed-identities-for-azure-resourceshttpsdocsmicrosoftcomazureactive-directorymsi-overview-work-with-virtual-machine-scale-sets"></a>[Azure 리소스에 대한 관리 ID](https://docs.microsoft.com/azure/active-directory/msi-overview)는 가상 머신 확장 집합과 함께 작동하나요?

예. Azure 빠른 시작 템플릿에서 일부 예제 MSI 템플릿을 확인할 수 있습니다. Linux: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi). Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi).


## <a name="extensions"></a>확장

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>가상 머신 확장 집합 확장을 삭제하려면 어떻게 해야 하나요?

가상 머신 확장 집합 확장을 삭제하려면 다음 PowerShell 예제를 사용합니다.

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

`$vmss`에서 extensionName 값을 찾을 수 있습니다.

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>가상 머신 확장 집합 Azure Monitor 로그와 통합 되는 템플릿 예제가 있나요?

가상 머신 확장 집합 템플릿 예제의 Azure Monitor 로그와 통합 되는의 두 번째 예제를 참조 하세요 [Azure Service Fabric 클러스터를 배포 하 고 Azure Monitor 로그를 사용 하 여 모니터링을 사용 하도록 설정](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric)합니다.

### <a name="extensions-seem-to-run-in-parallel-on-virtual-machine-scale-sets-this-causes-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this"></a>여러 확장이 가상 머신 확장 집합에서 병렬로 실행되는 것 같습니다. 이로 인해 사용자 지정 스크립트 확장이 실패합니다. 이 문제를 해결하려면 어떻게 해야 하나요?

가상 머신 확장 집합에서 확장 시퀀싱에 대한 자세한 내용은 [Azure Virtual Machine Scale Sets의 확장 시퀀싱](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)을 참조하세요.

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 VM에 대한 암호를 다시 설정하려면 어떻게 하나요?

확장 집합에서 VM의 암호를 변경하는 두 가지 기본 방법이 있습니다.

- 가상 머신 확장 집합 모델을 직접 변경합니다. Compute API 2017-12-01 이상에서 사용할 수 있습니다.

    확장 집합 모델에서(예: Azure Resource Explorer, PowerShell 또는 CLI 사용) 직접 관리 자격 증명을 업데이트합니다. 확장 집합이 업데이트되면 모든 새 VM에 새 자격 증명이 포함됩니다. 기존 VM은 이미지로 다시 설치되는 경우에만 새 자격 증명이 포함됩니다.

- VM 액세스 확장을 사용하여 암호를 다시 설정합니다.

    다음 PowerShell 예제를 사용합니다.

    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}

    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>가상 머신 확장 집합의 모든 VM에 확장을 추가하려면 어떻게 하나요?

업데이트 정책이 **자동**으로 설정된 경우 템플릿을 새 확장 속성으로 다시 배포하면 모든 VM이 업데이트됩니다.

업데이트 정책이 **수동**으로 설정된 경우 먼저 확장을 업데이트한 다음 VM의 모든 인스턴스를 수동으로 업데이트합니다.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected-that-is-will-the-vms-not-match-the-virtual-machine-scale-set-model-or-are-they-ignored-when-an-existing-machine-is-service-healed-or-reimaged-are-the-scripts-that-are-currently-configured-on-the-virtual-machine-scale-set-executed-or-are-the-scripts-that-were-configured-when-the-vm-was-first-created-used"></a>기존 가상 머신 확장 집합과 연결된 확장이 업데이트되면 기존 VM에 영향을 주나요? (즉, VM이 가상 머신 확장 집합 모델과 일치하지 *않나요*?) 또는 무시되나요? 기존 컴퓨터에 대해 서비스 복구, 이미지로 다시 설치 등을 수행할 때 현재 가상 머신 확장 집합에 구성된 스크립트를 실행하나요? 아니면 VM에서 처음 만들었을 때 구성된 스크립트를 사용하나요?

가상 머신 확장 집합 모델의 확장 정의가 업데이트되고 upgradePolicy 속성이 **자동**으로 설정되면 VM이 업데이트됩니다. UpgradePolicy 속성을 **수동**으로 설정하면 확장은 모델과 일치하지 않는 것으로 플래그가 지정됩니다.

기존 VM이 서비스 복구되면 다시 부팅하는 것처럼 보이며 확장이 다시 실행되지 않습니다. 이미지로 다시 설치되는 경우 OS 드라이브를 원본 이미지로 바꾸는 것과 같습니다. 확장과 같은 최신 모델의 모든 특수화가 실행됩니다.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>가상 머신 확장 집합을 Active Directory 도메인에 가입하려면 어떻게 하나요?

가상 머신 확장 집합을 AD(Active Directory) 도메인에 가입하려면 확장을 정의할 수 있습니다.

확장을 정의하려면 JsonADDomainExtension 속성을 사용합니다.

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-example-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>내 가상 머신 확장 집합은 설치를 시도하며 이로 인해 재부팅이 필요합니다. 예: "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted Install-WindowsFeature –Name FS-Resource-Manager –IncludeManagementTools"

가상 머신 확장 집합 확장이 설치를 시도하며 이로 인해 재부팅이 필요한 경우 Azure Automation DSC(필요한 상태 구성) 확장을 사용할 수 있습니다. 운영 체제가 Windows Server 2012 R2인 경우 Azure는 WMF(Windows Management Framework) 5.0 설치를 시작하고, 재부팅한 후 구성을 계속 진행합니다.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>가상 머신 확장 집합에 대해 맬웨어 방지를 설정하려면 어떻게 해야 하나요?

가상 머신 확장 집합에 대해 맬웨어 방지를 설정하려면 다음 PowerShell 예제를 사용합니다.

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'

# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]

$VMSS = Get-AzVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS
```

### <a name="i-need-to-execute-a-custom-script-thats-hosted-in-a-private-storage-account-the-script-runs-successfully-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signature-sas-it-fails-this-message-is-displayed-missing-mandatory-parameters-for-valid-shared-access-signature-linksas-works-fine-from-my-local-browser"></a>개인 저장소 계정에서 호스트되는 사용자 지정 스크립트를 실행해야 합니다. 이 스크립트는 저장소가 공용일 때 성공적으로 실행되지만 SAS(공유 액세스 서명)을 사용하려고 하면 실패합니다. 다음 메시지가 표시됩니다. “올바른 공유 액세스 서명에 대한 필수 매개 변수가 없음”. Link+SAS는 로컬 브라우저에서 잘 작동합니다.

개인 저장소 계정에 호스트되는 사용자 지정 스크립트를 실행하려면 저장소 계정 키 및 이름을 사용하여 보호 설정을 지정합니다. 자세한 내용은 [Windows용 사용자 지정 스크립트 확장](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings)을 참조하세요.

## <a name="networking"></a>네트워킹

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>집합의 모든 VM NIC에 적용되도록 확장 집합에 NSG(네트워크 보안 그룹)를 할당할 수 있나요?

예. 네트워크 프로필의 networkInterfaceConfigurations 섹션에서 참조하여 네트워크 보안 그룹을 확장 집합에 직접 적용할 수 있습니다. 예제:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>동일한 구독 및 동일한 지역에서 가상 머신 확장 집합에 대해 VIP 교환을 수행하려면 어떻게 합니까?

Azure Load Balancer 프런트 엔드가 포함된 두 개의 가상 머신 확장 집합이 있고 해당 항목이 동일한 구독 및 지역에 있는 경우 각 항목의 공용 IP 주소 할당을 취소하고 다른 항목에 할당할 수 있습니다. [VIP 교체: Azure Resource Manager에서 청록색 배포](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/)에서 예제를 참조하세요. 리소스가 네트워크 수준에서 할당 취소/할당되지만 지연되지는 않습니다. 더 빠른 옵션은 두 개의 백 엔드 풀 및 회람 규칙과 함께 Azure Application Gateway를 사용하는 것입니다. 또는 스테이징 및 프로덕션 슬롯 간의 빠른 전환을 지원하는 [Azure App service](https://azure.microsoft.com/services/app-service/)를 사용하여 애플리케이션을 호스트할 수도 있습니다.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>정적 개인 IP 주소를 할당하는 데 사용할 개인 IP 주소의 범위를 지정하려면 어떻게 하나요?

IP 주소는 사용자가 지정한 서브넷에서 선택됩니다.

가상 머신 확장 집합 설정 IP 주소를 할당하는 방법은 항상 "동적"이지만 이러한 IP 주소를 변경할 수 있다는 뜻은 아닙니다. 이 경우 "동적"은 PUT 요청에서 IP 주소를 지정하지 않는다는 것을 의미할 뿐입니다. 서브넷을 사용하여 정적 집합을 지정합니다.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>기존 Azure Virtual Network에 가상 머신 확장 집합을 배포하려면 어떻게 하나요?

기존 Azure Virtual Network에 가상 머신 확장 집합을 배포하려면 [기존 가상 네트워크에 가상 머신 확장 집합 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet)를 참조하세요.

### <a name="how-do-i-add-the-ip-address-of-the-first-vm-in-a-virtual-machine-scale-set-to-the-output-of-a-template"></a>가상 머신 확장 집합에 포함된 첫 번째 VM의 IP 주소를 템플릿의 출력에 추가하려면 어떻게 하나요?

가상 머신 확장 집합에 포함된 첫 번째 VM의 IP 주소를 템플릿의 출력에 추가하려면 [Azure Resource Manager: 가상 머신 확장 집합 개인 IP 가져오기](https://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips)를 참조하세요.

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>가속 네트워킹을 포함한 확장 집합을 사용할 수 있나요?

예. 가속된 네트워킹을 사용하려면 확장 집합의 networkInterfaceConfigurations 설정에서 enableAcceleratedNetworking을 true로 설정합니다. 예를 들면 다음과 같습니다.
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "niconfig1",
            "properties": {
                "primary": true,
                "enableAcceleratedNetworking" : true,
                "ipConfigurations": [
                ]
            }
        }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>확장 집합에서 사용하는 DNS 서버를 구성하려면 어떻게 해야 하나요?

사용자 지정 DNS 구성을 포함한 가상 머신 확장 집합을 만들려면 dnsSettings JSON 패킷을 확장 집합 networkInterfaceConfigurations 섹션에 추가합니다. 예제:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>각 VM에 공용 IP 주소를 할당하도록 확장 집합을 구성하려면 어떻게 해야 하나요?

각 VM에 공용 IP 주소를 할당하는 가상 머신 확장 집합을 만들려면 Microsoft.Compute/virtualMachineScaleSets 리소스의 API 버전이 2017-03-30인지 확인하고 _publicipaddressconfiguration_ JSON 패킷을 확장 집합 ipConfigurations 섹션에 추가합니다. 예제:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>여러 Application Gateway를 사용하도록 확장 세트을 구성할 수 있나요?

예. 여러 Application Gateway 백 엔드 주소 풀에 대한 리소스 ID를 확장 세트 네트워크 프로필의 _ipConfigurations_ 섹션에 있는 _applicationGatewayBackendAddressPools_ 목록에 추가할 수 있습니다.

## <a name="scale"></a>확장

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>어떤 경우에 VM이 2개 미만인 가상 머신 확장 집합을 만들어야 하나요?

VM이 2개 미만인 가상 머신 확장 집합을 만드는 한 가지 이유는 가상 머신 확장 집합의 탄력적 속성을 사용해야 하는 경우입니다. 예를 들어 VM 실행 비용을 지불하지 않고 인프라를 정의하기 위해 VM이 없는 가상 머신 확장 집합을 배포할 수 있습니다. 그런 다음 VM을 배포할 준비가 되면 가상 머신 확장 집합의 "용량"을 프로덕션 인스턴스 수로 늘립니다.

VM이 2개 미만인 가상 머신 확장 집합을 만드는 또 다른 경우는 개별 VM이 있는 가용성 집합을 사용하는 것보다 가용성에 별 관심이 없을 때입니다. 가상 머신 확장 집합은 대체할 수 있는 미분화된 컴퓨팅 단위로 작업하는 방법을 제공합니다. 이 일관성은 가상 머신 확장 집합과 가용성 집합의 주요 차별 요인이기도 합니다. 다양한 상태 비저장 워크로드는 개별 단위를 추적하지 않습니다. 워크로드가 감소하면 하나의 컴퓨팅 단위로 축소하고, 워크로드가 증가하면 여러 단위로 확장합니다.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>가상 머신 확장 집합의 VM 수를 변경하려면 어떻게 해야 하나요?

Azure Portal의 가상 머신 확장 집합에서 VM 수를 변경하려면 가상 머신 확장 집합 속성 섹션에서 “크기 조정” 블레이드를 설정하고 슬라이더 막대를 사용합니다. 다른 방법으로 인스턴스 수를 변경하려면 [가상 머신 확장 집합의 인스턴스 수 변경](https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/)을 참조하세요.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>특정 임계값에 도달하는 경우 사용자 지정 경고를 정의하려면 어떻게 해야 하나요?

지정된 임계값에 대한 경고를 처리하는 방법은 다소 유연한 편입니다. 예를 들어 사용자 지정 웹후크를 정의할 수 있습니다. 다음 웹후크 예제는 Resource Manager 템플릿에서 가져옵니다.

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ]
    }
}
```

이 예제에서는 임계값에 도달될 경우 경고가 Pagerduty.com으로 이동됩니다.

## <a name="patching-and-operations"></a>패치 및 작업

### <a name="how-do-i-create-a-scale-set-in-an-existing-resource-group"></a>기존 리소스 그룹에서 확장 집합을 만들려면 어떻게 하나요?

기존 리소스 그룹에서 확장 집합을 만드는 것은 아직 Azure Portal에서 할 수는 없지만 Azure Resource Manager 템플릿에서 확장 집합을 배포할 때에는 기존 리소스 그룹을 지정할 수 있습니다. 또한 Azure PowerShell 또는 CLI를 사용하여 확장 집합을 만들 때에도 기존 리소스 그룹을 지정할 수 있습니다.

### <a name="can-we-move-a-scale-set-to-another-resource-group"></a>확장 집합을 다른 리소스 그룹으로 이동할 수 있나요?

예. 확장 집합 리소스를 새 구독 또는 리소스 그룹에 이동할 수 있습니다.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>내 가상 머신 크기 집합을 새 이미지로 업데이트하는 방법은 무엇인가요? 패치는 어떻게 관리해야 하나요?

가상 머신 확장 집합을 새 이미지로 업데이트하고 패치를 관리하려면 [가상 머신 확장 집합 업그레이드](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set)를 참조하세요.

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>이미지로 다시 설치 작업을 사용하여 이미지를 변경하지 않고 VM을 다시 설정할 수 있나요? (즉 VM을 새 이미지가 아닌 출하 시 설정으로 다시 설정하려고 합니다.)

예. 이미지로 다시 설치 작업을 사용하여 이미지를 변경하지 않고 VM을 다시 설정할 수 있습니다. 그러나 가상 머신 확장 집합이 `version = latest`인 플랫폼 이미지를 참조하면 `reimage`를 호출할 때 VM에서 최신 OS 이미지로 업데이트할 수 있습니다.

자세한 내용은 [가상 머신 크기 집합의 모든 VM 관리](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set)를 참조하세요.

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용 하 여 확장 집합을 통합할 수는?

예, 설정할 수 있습니다 확장에서 Azure 모니터링 확장을 설치 하 여 Vm. Azure CLI 예는 다음과 같습니다.
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
Azure Portal의 Log Analytics 작업 영역에서 workspaceId 및 workspaceKey를 찾을 수 있습니다. 개요 페이지에서 설정 타일을 클릭합니다. 위쪽에서 연결된 원본 탭을 클릭합니다.

> [!NOTE]
> 크기 집합이 _upgradePolicy_ 설정할지를 수동으로에 업그레이드를 호출 하 여 확장 집합의 모든 Vm을 적용 해야 합니다. CLI에서는 _az vmss update-instances_입니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>문제 해결

### <a name="how-do-i-turn-on-boot-diagnostics"></a>부팅 진단을 켜려면 어떻게 하나요?

부팅 진단을 켜려면 먼저 저장소 계정을 만듭니다. 그런 후 이 JSON 블록을 가상 머신 확장 집합 **virtualMachineProfile**에 배치하고 해당 가상 머신 확장 집합을 업데이트합니다.

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

새 VM이 만들어지면 VM의 InstanceView 속성에 스크린샷 등에 대한 세부 정보가 표시됩니다. 예를 들면 다음과 같습니다.

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>가상 머신 속성

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>여러 번 호출하지 않고도 각 VM에 대한 속성 정보를 얻으려면 어떻게 합니까? 예를 들어 가상 머신 확장 집합에서 100개의 VM 각각에 대해 장애 도메인을 얻으려면 어떻게 해야 할까요?

여러 번 호출하지 않고 각 VM에 대한 속성 정보를 가져오려면 다음 리소스 URI에 대해 REST API `GET`을 수행하여 `ListVMInstanceViews`를 호출할 수 있습니다.

/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>가상 머신 확장 집합의 다른 VM에 다른 확장 인수를 전달할 수 있나요?

아니요. 가상 머신 확장 집합의 다른 VM에 다른 확장 인수를 전달할 수 없습니다. 그렇지만 확장은 컴퓨터 이름과 같이 실행 중인 VM의 고유한 속성에 따라 작동할 수 있습니다. 또한 확장은 http://169.254.169.254에 있는 인스턴스 메타데이터를 쿼리하여 VM에 대한 자세한 정보를 가져올 수 있습니다.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>가상 머신 확장 집합 VM 컴퓨터 이름과 VM ID 간에 차이가 발생하는 이유는 무엇인가요? 예를 들면 다음과 같습니다. 0, 1, 3...

가상 머신 확장 집합 **overprovision** 속성은 기본값인 **true**로 설정되어 있으므로 가상 머신 확장 집합 VM 컴퓨터 이름 간에는 차이가 있습니다. 과도 프로비저닝이 **true**로 설정되면 요청된 것보다 더 많은 VM이 생성됩니다. 그런 후에 추가 VM은 삭제됩니다. 이 경우 배포 안정성은 향상되지만 연속된 이름 지정 및 연속된 NAT(Network Address Translation) 규칙은 준수되지 못합니다.

이 속성을 **false**로 설정할 수 있습니다. 소규모 가상 머신 확장 집합의 경우 이렇게 해도 배포 안정성에 큰 영향을 주지는 않습니다.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>가상 머신 확장 집합에서 VM을 삭제하는 것과 VM을 할당 취소하는 것 사이의 차이점은 무엇입니까? 언제 다른 하나를 선택해야 합니까?

가상 머신 확장 집합에서 VM을 삭제하는 것과 VM을 할당 취소하는 것 사이의 주요 차이점은 `deallocate`로는 VHD(가상 하드 디스크)가 삭제되지 않는다는 것입니다. `stop deallocate` 실행과 관련된 저장소 비용이 있습니다. 다음과 같은 이유 중 하나로 두 가지 방법 중 하나를 사용할 수 있습니다.

- Compute 비용은 더 이상 지불하지 않고, VM의 디스크 상태는 유지하려고 합니다.
- 가상 머신 확장 집합을 확장하는 것보다 더 빠르게 VM 집합을 시작하려고 합니다.
  - 이 시나리오와 관련하여 사용자 고유의 자동 크기 조정 엔진을 만들고 더 빠른 종단 간 확장을 원했을 수 있습니다.
- 장애 도메인이나 업데이트 도메인 간에 고르게 분산되지 않은 가상 머신 확장 집합이 있습니다. 이러한 상황은 과도한 프로비저닝 후에 선택적으로 VM을 삭제했거나 VM이 삭제되었기 때문에 발생할 수 있습니다. 가상 머신 확장 집합에 대해 `stop deallocate`를 실행한 후 `start`를 실행하면 장애 도메인 또는 업데이트 도메인 간에 VM이 균일하게 분산됩니다.

### <a name="how-do-i-take-a-snapshot-of-a-vmss-instance"></a>스냅숏으로 VMSS 인스턴스를 가져올 방법 있나요?
VMSS 인스턴스에서 스냅숏을 만듭니다.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"
 
$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
``` 
 
스냅숏에서 관리 디스크를 만듭니다.

```azurepowershell-interactive
$snapshotName = "myShapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk') 
```
