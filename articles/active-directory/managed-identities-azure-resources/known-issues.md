---
title: 관리 ID 관련 FAQ 및 알려진 문제 - Azure AD
description: Azure 리소스에 대한 관리 ID 관련 알려진 문제
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 08/06/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 4bcd36a1ce38d4d9eb6a0faec470f7427852894b
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260223"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID 관련 FAQ 및 알려진 문제

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>FAQ(질문과 대답)

> [!NOTE]
> Azure 리소스에 대한 관리 ID는 이전에 MSI(관리 서비스 ID)로 알려진 서비스에 대한 새 이름입니다.


### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>관리 되는 id가 있는 리소스는 어떻게 찾을 수 있나요?

다음 Azure CLI 명령을 사용 하 여 시스템 할당 관리 id가 있는 리소스 목록을 찾을 수 있습니다. 

`az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table`




### <a name="do-managed-identities-have-a-backing-app-object"></a>관리 id에 지원 앱 개체가 있나요?

아니요. 관리 되는 id와 Azure AD 앱 등록은 디렉터리에서 동일 하지 않습니다. 

응용 프로그램 개체와 서비스 주체 개체 라는 두 가지 구성 요소가 앱 등록. Azure 리소스에 대 한 관리 되는 Id에는 서비스 주체 개체와 같은 구성 요소 중 하나만 있습니다. 

관리 id에는 디렉터리에 응용 프로그램 개체가 없으므로 MS graph에 대해 앱 사용 권한을 부여 하는 데 일반적으로 사용 됩니다. 대신 관리 되는 id에 대 한 MS graph 권한은 서비스 사용자에 게 직접 부여 해야 합니다.  

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Azure 리소스에 대한 관리 ID가 Azure Cloud Services에서 작동하나요?

아니요. Azure 리소스에 대한 관리 ID는 Azure Cloud Services에서 지원될 예정이 없습니다.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>관리 id와 연결 된 자격 증명은 무엇 인가요? 유효 기간은 얼마나 걸립니까? 얼마나 자주 회전 하나요?

> [!NOTE]
> 관리 id를 인증 하는 방법은 예 고 없이 변경 될 수 있는 내부 구현 세부 정보입니다.

관리 되는 id는 인증서 기반 인증을 사용 합니다. 관리 되는 각 id의 자격 증명은 90 일의 만료 이며 45 일 후에 롤업 됩니다.

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID의 보안 경계란 무엇인가요?

ID의 보안 경계는 연결되는 리소스입니다. 예를 들어, Azure 리소스에 대한 관리 ID를 갖는 가상 머신의 보안 경계는 바로 가상 머신입니다. 해당 VM에서 실행되는 모든 코드는 Azure 리소스에 대한 관리 ID 엔드포인트 및 요청 토큰을 호출할 수 있습니다. 이것은 Azure 리소스에 대한 관리 ID를 지원하는 다른 리소스와 비슷한 환경입니다.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>요청에 ID를 지정하지 않을 경우 IMDS 기본값은 무엇인가요?

- 시스템에서 할당된 관리 ID가 활성화되지 않은 경우 요청에 ID를 지정하지 않으면 IMDS 기본값은 시스템에서 할당된 관리 ID가 됩니다.
- 시스템에서 할당된 관리 ID가 활성화되지 않은 경우 사용자가 할당한 관리 ID 하나만 있으면 IMDS 기본값은 사용자가 할당한 관리 ID가 됩니다. 
- 시스템에서 할당된 관리 ID가 활성화되지 않은 경우 사용자가 할당한 관리 ID가 여러 개 존재하면 요청에 관리 ID를 지정해야 합니다.



### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>구독을 다른 디렉터리로 이동하면 관리 ID가 자동으로 다시 생성되나요?

아니요. 구독을 다른 디렉터리로 이동 하는 경우 수동으로 다시 만들고 Azure 역할 할당을 다시 부여 해야 합니다.
- 시스템에서 할당된 관리 ID: 비활성화하거나 재활성화합니다. 
- 사용자가 할당한 관리 ID: 삭제한 후 다시 생성하여 필요한 리소스(예: 가상 머신)에 다시 연결합니다.

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>관리 ID를 사용하여 다른 디렉터리/테넌트의 리소스에 액세스할 수 있나요?

아니요. 관리 ID는 현재 교차 디렉터리 시나리오를 지원하지 않습니다. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>리소스에서 관리 ID에 필요한 Azure RBAC 권한은 무엇인가요? 

- 시스템이 할당한 관리형 ID: 리소스에 대한 쓰기 권한이 필요합니다. 예를 들어 가상 머신의 경우 Microsoft.Compute/virtualMachines/write가 필요합니다. 이 작업은 [Virtual Machine 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)와 같은 리소스별 기본 제공 역할에 포함됩니다.
- 사용자가 할당한 관리형 ID: 리소스에 대한 쓰기 권한이 필요합니다. 예를 들어 가상 머신의 경우 Microsoft.Compute/virtualMachines/write가 필요합니다. 관리 ID에 대한 [관리 ID 운영자](../../role-based-access-control/built-in-roles.md#managed-identity-operator) 역할 할당도 필요합니다.



## <a name="known-issues"></a>알려진 문제

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Azure 리소스에 대한 관리 ID 확장에 대해 스키마 내보내기를 시도할 때 "자동화 스크립트"가 실패함

VM에서 Azure 리소스에 대한 관리 ID를 사용하도록 설정하는 경우 VM 또는 해당 리소스 그룹에 "자동화 스크립트" 기능을 사용하려고 할 때 다음과 같은 오류가 표시됩니다.

![Azure 리소스에 대한 관리 ID 자동화 스크립트 내보내기 오류](./media/msi-known-issues/automation-script-export-error.png)

현재 Azure 리소스에 대한 관리 ID VM 확장(2019년 1월에 사용 중단 예정)은 리소스 그룹 템플릿으로 해당 스키마를 내보내는 기능을 지원하지 않습니다. 결과적으로 생성된 템플릿은 리소스에서 Azure 리소스에 대한 관리 ID를 사용하도록 설정하는 구성 매개 변수를 표시하지 않습니다. [템플릿을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-template-windows-vm.md)의 예제를 수행하여 이러한 섹션을 수동으로 추가할 수 있습니다.

스키마 내보내기 기능을 Azure 리소스에 대한 관리 ID VM 확장(2019년 1월에 사용 중단 예정)에 사용할 수 있는 경우 [VM 확장을 포함하는 리소스 그룹 내보내기](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)에 나열됩니다.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>VM이 리소스 그룹 또는 구독에서 이동한 후 시작되지 않음

실행 중인 상태의 VM을 이동하면 이동 중에도 VM은 계속 실행됩니다. 그러나 이동 후 중지했다가 다시 시작하면 VM이 시작되지 않습니다. 이 문제가 발생하는 이유는 VM이 Azure 리소스에 대한 관리 ID에 대한 참조를 업데이트하지 않고 이전 리소스 그룹의 ID를 계속 가리키기 때문입니다.

**해결 방법** 
 
Azure 리소스에 대한 관리 ID의 올바른 값을 가져올 수 있도록 VM에서 업데이트를 트리거합니다. VM 속성 변경을 수행하여 Azure 리소스에 대한 관리 ID에 대한 참조를 업데이트할 수 있습니다. 예를 들어 다음 명령을 사용하여 VM에 새 태그 값을 설정할 수 있습니다.

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
이 명령은 VM에 값이 1인 새 태그 "fixVM"을 설정합니다. 
 
이 속성을 설정하면 VM은 올바른 Azure 리소스에 대한 관리 ID 리소스 URI로 업데이트됩니다. 그리고 나면 VM을 시작할 수 있습니다. 
 
VM이 시작되면 다음 명령을 사용하여 태그를 제거할 수 있습니다.

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```



### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Azure AD 디렉터리 간에 구독 전송

구독이 다른 디렉터리로 이동/전송되면 관리 ID는 업데이트되지 않습니다. 결과적으로, 존재하는 시스템에서 할당하거나 사용자가 할당한 관리 ID가 모두 손상됩니다. 

다른 디렉터리로 이동한 구독의 관리 ID에 대한 해결 방법:

 - 시스템에서 할당된 관리 ID: 비활성화하거나 재활성화합니다. 
 - 사용자가 할당한 관리 ID: 삭제한 후 다시 생성하여 필요한 리소스(예: 가상 머신)에 다시 연결합니다.

자세한 내용은 [다른 Azure AD 디렉터리로 Azure 구독 양도(미리 보기)](../../role-based-access-control/transfer-subscription.md)를 참조하세요.

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>사용자가 할당한 관리 ID를 다른 리소스 그룹/구독으로 이동

사용자가 할당한 관리 ID를 다른 리소스 그룹으로 이동하는 것은 지원되지 않습니다.