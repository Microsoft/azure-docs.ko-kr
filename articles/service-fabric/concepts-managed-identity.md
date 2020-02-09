---
title: Azure에 대 한 관리 id
description: Service Fabric에서 Azure에 관리 되는 id를 사용 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 06ebcfdf3d6a3815908752153acb09437d745d15
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986753"
---
# <a name="using-managed-identities-for-azure-with-service-fabric-preview"></a>Service Fabric에서 Azure에 관리 id 사용 (미리 보기)

클라우드 응용 프로그램을 빌드할 때 일반적인 문제는 개발자 워크스테이션 또는 소스 제어에서 로컬로 저장 하지 않고도 다양 한 서비스를 인증 하기 위해 코드에서 자격 증명을 안전 하 게 관리 하는 방법입니다. Azure *에 대 한 관리 되는 id* 는 azure ad 내에서 자동으로 관리 되는 id를 제공 하 여 Azure Active Directory (azure ad)의 모든 리소스에 대해이 문제를 해결 합니다. 서비스의 id를 사용 하 여 코드에 저장 된 자격 증명 없이 Key Vault를 포함 하 여 Azure AD 인증을 지 원하는 모든 서비스에 인증할 수 있습니다.

Azure *리소스에 대 한 관리 id* 는 azure 구독에 대 한 azure AD에서 무료로 제공 됩니다. 추가 비용은 없습니다.

> [!NOTE]
> *Azure에 대 한 관리 id* 는 이전에 MSI (관리 서비스 ID)로 알려진 서비스의 새 이름입니다.

## <a name="concepts"></a>개념

Azure에 대 한 관리 되는 id는 다음과 같은 몇 가지 주요 개념을 기반으로 합니다.

- **클라이언트 ID** -초기 프로 비전 중에 응용 프로그램 및 서비스 주체에 연결 된 Azure AD에서 생성 되는 고유 식별자 ( [응용 프로그램 ID](/azure/active-directory/develop/developer-glossary#application-id-client-id)도 참조)

- **보안 주체 id** -Azure 리소스에 대 한 역할 기반 액세스 권한을 부여 하는 데 사용 되는 관리 id의 서비스 주체 개체에 대 한 개체 id입니다.

- **서비스 사용자** -지정 된 테넌트에서 AAD 응용 프로그램의 프로젝션을 나타내는 Azure Active Directory 개체입니다 ( [서비스 주체](../active-directory/develop/developer-glossary.md#service-principal-object)도 참조).

두 가지 종류의 관리 ID가 있습니다.

- **시스템 할당 관리 id** 는 Azure 서비스 인스턴스에서 직접 사용 하도록 설정 됩니다.  시스템 할당 id의 수명 주기는 사용 하도록 설정 된 Azure 서비스 인스턴스에 고유 합니다.
- **사용자 할당 관리 ID**는 독립 실행형 Azure 리소스로 생성됩니다. Id는 하나 이상의 Azure 서비스 인스턴스에 할당 될 수 있으며 해당 인스턴스의 주기와 별도로 관리 됩니다.

관리 id 유형 간의 차이점을 추가로 이해 하려면 [Azure 리소스에 대 한 관리 되는 Id는 어떻게 작동 하나요?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) 를 참조 하세요.

## <a name="supported-scenarios-for-service-fabric-applications"></a>Service Fabric 응용 프로그램에 대해 지원 되는 시나리오

Service Fabric에 대 한 관리 되는 id는 azure 리소스로 배포 된 응용 프로그램에 대해서만 Azure에서 배포 된 Service Fabric 클러스터에서 지원 됩니다. Azure 리소스로 배포 되지 않은 응용 프로그램에는 id를 할당할 수 없습니다. 개념적으로 말하면 Azure Service Fabric 클러스터에서 관리 되는 id에 대 한 지원은 다음 두 단계로 구성 됩니다.

1. 응용 프로그램 리소스에 관리 되는 id를 하나 이상 할당 합니다. 응용 프로그램에는 시스템에 할당 된 단일 id 및/또는 최대 32 사용자 할당 id가 각각 할당 될 수 있습니다.

2. 응용 프로그램의 정의 내에서 응용 프로그램에 할당 된 id 중 하나를 응용 프로그램을 구성 하는 개별 서비스에 매핑합니다.

응용 프로그램의 시스템 할당 id는 해당 응용 프로그램에 대해 고유 합니다. 사용자 할당 id는 여러 응용 프로그램에 할당 될 수 있는 독립 실행형 리소스입니다. 응용 프로그램 내에서 단일 id (시스템 할당 또는 사용자 할당 여부)는 응용 프로그램의 여러 서비스에 할당 될 수 있지만 각 개별 서비스에는 하나의 id만 할당할 수 있습니다. 마지막으로, 서비스에이 기능에 대 한 액세스 권한이 있는 id를 명시적으로 할당 해야 합니다. 실제로 응용 프로그램 id를 해당 구성 서비스에 매핑하면 응용 프로그램 격리를 사용할 수 있습니다. 즉, 서비스는 매핑된 id만 사용할 수 있습니다.  

현재이 미리 보기 기능에 대해 지원 되는 시나리오는 다음과 같습니다.

- 하나 이상의 서비스 및 하나 이상의 할당 된 id를 사용 하 여 새 응용 프로그램 배포

- Azure 리소스에 액세스 하기 위해 기존 (Azure 배포 된) 응용 프로그램에 관리 되는 id를 하나 이상 할당 합니다.

다음 시나리오는 지원 되지 않거나 권장 되지 않습니다. 이러한 작업은 차단 되지 않을 수 있지만 응용 프로그램에서 중단이 발생할 수 있습니다.

- 응용 프로그램에 할당 된 id를 제거 하거나 변경 합니다. 변경 해야 하는 경우 별도의 배포를 제출 하 여 먼저 새 id 할당을 추가 하 고 이전에 할당 된 id를 제거 합니다. 기존 응용 프로그램에서 id를 제거 하면 응용 프로그램을 업그레이드할 수 없는 상태로 유지 하는 것을 포함 하 여 원치 않는 결과가 발생할 수 있습니다. Id를 제거 해야 하는 경우 응용 프로그램을 완전히 삭제 하는 것이 안전 합니다. 이는 응용 프로그램과 연결 된 시스템 할당 id (정의 된 경우)를 삭제 하 고 응용 프로그램에 할당 된 사용자 할당 id를 사용 하 여 모든 연결을 제거 합니다.

- 관리 id에 대 한 Service Fabric 지원은 현재 [AzureServiceTokenProvider](../key-vault/service-to-service-authentication.md)에 통합 되지 않습니다. 관리 되는 id 기능에 대 한 미리 보기 기간이 끝나면 통합이 이루어집니다.

>
> [!NOTE]
>
> 이 기능은 미리 보기로 제공됩니다. 자주 변경 될 수 있으며 프로덕션 배포에는 적합 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [관리 되는 id를 지 원하는 새 Azure Service Fabric 클러스터 배포](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [기존 Azure Service Fabric 클러스터에서 관리 되는 id 지원 사용](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [시스템 할당 관리 id를 사용 하 여 Azure Service Fabric 응용 프로그램 배포](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [사용자 할당 관리 id를 사용 하 여 Azure Service Fabric 응용 프로그램 배포](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [서비스 코드에서 Service Fabric 응용 프로그램의 관리 되는 id 활용](./how-to-managed-identity-service-fabric-app-code.md)
- [Azure Service Fabric 응용 프로그램에 다른 Azure 리소스에 대 한 액세스 권한 부여](./how-to-grant-access-other-resources.md)
- [KeyVaultReferences로 응용 프로그램 비밀 선언 및 사용](./service-fabric-keyvault-references.md)
