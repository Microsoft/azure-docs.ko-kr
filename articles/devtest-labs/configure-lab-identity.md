---
title: Azure DevTest Labs에서 lab identity 구성
description: Azure DevTest에서 lab identity를 구성 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 08/20/2020
ms.openlocfilehash: a652eb5751f9b723911a1c1baaaaf9860febc5b6
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719685"
---
# <a name="configure-a-lab-identity"></a>랩 id 구성

클라우드 애플리케이션을 빌드할 때 일반적으로 직면하는 난관 중 하나는 코드에서 클라우드 서비스에 인증하는 데 사용되는 자격 증명을 관리하는 방법입니다. 자격 증명을 안전하게 보호하는 것이 중요합니다. 자격 증명이 개발자 워크스테이션에 표시되지 않고 소스 제어에 체크 인되지 않는 것이 가장 좋습니다. Azure Key Vault는 자격 증명, 암호 및 기타 키를 안전 하 게 저장 하는 방법을 제공 하지만, 코드에서이를 검색 하려면 Key Vault에 인증 해야 합니다. 

Azure AD (Azure Active Directory)의 Azure 리소스에 대 한 관리 되는 id 기능을 통해이 문제를 해결 합니다. 이 기능은 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명이 필요 없습니다. [Azure에서 관리 되는 id](../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보세요. 

두 가지 종류의 관리 ID가 있습니다. 

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID  

 **시스템 할당 관리 id**는   Azure 서비스 인스턴스에서 직접 사용 하도록 설정 됩니다. ID를 사용하도록 설정하면 Azure는 인스턴스의 구독에서 신뢰하는 Azure AD 테넌트에 인스턴스의 ID를 만듭니다. ID가 만들어지면 자격 증명이 인스턴스에 프로비전됩니다. 시스템 할당 ID의 수명 주기는 사용하도록 설정된 Azure 서비스 인스턴스와 직접적으로 연관됩니다. 인스턴스가 삭제되면 Azure는 Azure AD에서 자격 증명과 ID를 자동으로 정리합니다. 

### <a name="scenarios-for-using-labs-system-assigned-identity"></a>랩의 시스템 할당 id 사용에 대 한 시나리오  

모든 DevTest Labs는 랩의 수명 동안 유효한 상태로 유지 되는 시스템 할당 id로 생성 됩니다. 시스템 할당 id는 다음 용도로 사용 됩니다.  

- 여러 VM 및/또는 platform as a service environment를 실행 하는 데 사용 되는 모든 [Azure Resource Manager](devtest-lab-create-environment-from-arm.md) 기반 배포는 랩의 시스템 할당 id를 사용 하 여 실행 됩니다.  
- 고객 관리 키를 사용 하는 랩 디스크에 대 한 디스크 암호화는 랩의 시스템 할당 id를 통해 지원 됩니다. 랩의 id에 대 한 명시적 액세스를 제공 하 여 디스크 암호화 집합에 액세스 하면 랩에서 사용자 대신 모든 가상 머신 디스크를 암호화할 수 있습니다. 고객 관리 키를 사용 하 여 랩 디스크에 대해 [디스크 암호화를 사용 하도록 설정 하는 방법](encrypt-disks-customer-managed-keys.md) 에 대해 자세히 알아보세요.  

### <a name="configure-identity"></a>ID 구성

이 섹션에서는 랩의 id 정책을 구성 하는 방법을 보여 줍니다.

> [!NOTE]
> 8/10/2020 이전에 만든 labs의 경우 시스템 할당 id는 Off로 설정 됩니다. 이전 섹션에 나열 된 용도로 랩을 사용 하려는 경우 랩 소유자는이 기능을 켤 수 있습니다.  
>
> 8/10/2020 이후에 만들어진 새 랩의 경우 랩의 시스템 할당 id는 기본적으로 On으로 설정 되 고 랩 소유자는 랩의 수명 주기에 대해이 기능을 해제할 수 없습니다.  

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **DevTest Labs**를 검색 합니다.
1. 랩 목록에서 원하는 랩을 선택 합니다.
1. **구성 및 정책**  ->  **id (미리 보기)** 를 선택 합니다. 

> [!div class="mx-imgBorder"]
> ![ID 구성](./media/configure-lab-identity/configure-identity.png)

## <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID  

사용자 할당 관리 id는 독립 실행형 Azure 리소스로 생성 됩니다. 만들기 프로세스를 통해 Azure는 사용 중인 구독에서 신뢰하는 Azure AD 테넌트에 ID를 만듭니다. 생성된 ID는 하나 이상의 Azure 서비스 인스턴스에 할당할 수 있습니다. 사용자 할당 ID의 수명 주기는 할당된 Azure 서비스 인스턴스의 수명 주기와 별도로 관리됩니다. 

DevTest Labs는 가상 머신과 Azure Resource Manager 기반 환경 모두에 대 한 사용자 할당 id를 지원 합니다.  자세한 내용은 다음 항목을 참조하세요.

- [사용자 할당 id를 추가 하 여 랩 Azure Resource Manager 환경을 배포 합니다.](use-managed-identities-environments.md)
- [사용자 할당 id를 추가 하 여 랩 가상 컴퓨터 배포](enable-managed-identities-lab-vms.md)

## <a name="next-steps"></a>다음 단계

[비용 관리 구성](devtest-lab-configure-cost-management.md) 검토