---
title: Azure AD Domain Services의 일반적인 경고 및 해결 방법 | Microsoft Docs
description: 상태 Azure Active Directory Domain Services의 일부로 생성 된 일반적인 경고를 해결 하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 29bd7490ea9a03118f134ba11923ede2075e6fd5
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722588"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>알려진 문제: Azure Active Directory Domain Services 일반적인 경고 및 해결 방법

응용 프로그램에 대 한 id 및 인증의 핵심 요소로 Azure Active Directory Domain Services (Azure AD DS)에 문제가 있을 수도 있습니다. 문제가 발생 하는 경우 몇 가지 일반적인 경고 및 관련 문제 해결 단계를 수행 하 여 작업을 다시 실행 하는 데 도움을 얻을 수 있습니다. 언제 든 지 추가 문제 해결 지원을 위해 [Azure 지원 요청을 열][azure-support] 수도 있습니다.

이 문서에서는 Azure AD DS의 일반적인 경고에 대 한 문제 해결 정보를 제공 합니다.

## <a name="aadds100-missing-directory"></a>AADDS100: 누락된 디렉터리

### <a name="alert-message"></a>경고 메시지

*관리 되는 도메인과 연결 된 Azure AD 디렉터리가 삭제 되었을 수 있습니다. 관리 되는 도메인은 더 이상 지원 되지 않는 구성입니다. Microsoft는 관리 되는 도메인을 모니터링, 관리, 패치 및 동기화 할 수 없습니다.*

### <a name="resolution"></a>해결 방법

이 오류는 일반적으로 Azure 구독을 새 Azure AD 디렉터리로 이동 하 고 Azure AD DS와 연결 된 이전 Azure AD 디렉터리를 삭제 한 경우에 발생 합니다.

이 오류는 복구할 수 없습니다. 이 경고를 해결 하려면 [기존 관리 되는 도메인을 삭제](delete-aadds.md) 하 고 새 디렉터리에서 다시 만듭니다. 관리 되는 도메인을 삭제 하는 데 문제가 있으면 추가 문제 해결 지원을 위해 [Azure 지원 요청을 여세요][azure-support] .

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C가 이 디렉터리에서 실행 중임

### <a name="alert-message"></a>경고 메시지

*Azure AD Domain Services를 Azure AD B2B 디렉터리에서 사용 가능하게 설정할 수 없습니다.*

### <a name="resolution"></a>해결 방법

Azure AD DS는 Azure AD 디렉터리와 자동으로 동기화 됩니다. Azure AD 디렉터리가 B2C에 대해 구성 된 경우 Azure AD DS를 배포 하 고 동기화 할 수 없습니다.

Azure AD DS을 사용 하려면 다음 단계를 사용 하 여 Azure AD B2C 없는 디렉터리에서 관리 되는 도메인을 다시 만들어야 합니다.

1. 기존 Azure AD 디렉터리에서 [관리 되는 도메인을 삭제](delete-aadds.md) 합니다.
1. Azure AD B2C 디렉터리가 아닌 새 Azure AD 디렉터리를 만듭니다.
1. [대체 관리 되는 도메인을 만듭니다](tutorial-create-instance.md).

관리 되는 도메인의 상태는 2 시간 내에 자동으로 업데이트 되 고 경고를 제거 합니다.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: 주소가 공용 IP 범위에 있음

### <a name="alert-message"></a>경고 메시지

*Azure AD Domain Services 사용 하도록 설정한 가상 네트워크에 대 한 IP 주소 범위가 공용 IP 범위에 있습니다. Azure AD Domain Services은 개인 IP 주소 범위를 가진 가상 네트워크에서 사용 하도록 설정 해야 합니다. 이 구성은 관리 되는 도메인을 모니터링, 관리, 패치, 동기화 하는 Microsoft의 기능에 영향을 줍니다.*

### <a name="resolution"></a>해결 방법

시작 하기 전에 [개인 IP v4 주소 공간](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)을 이해 하 고 있는지 확인 합니다.

가상 네트워크 내에서 Vm은 서브넷에 대해 구성 된 것과 동일한 IP 주소 범위에서 Azure 리소스에 대 한 요청을 수행할 수 있습니다. 서브넷에 대 한 공용 IP 주소 범위를 구성 하는 경우 가상 네트워크 내에서 라우팅되는 요청이 의도 한 웹 리소스에 도달 하지 못할 수 있습니다. 이 구성을 사용 하면 Azure AD DS에서 예기치 않은 오류가 발생할 수 있습니다.

> [!NOTE]
> 가상 네트워크에서 구성된 인터넷의 IP 주소 범위가 있는 경우 이 경고는 무시될 수 있습니다. 그러나 Azure AD Domain Services 예기치 않은 오류가 발생할 수 있으므로이 구성으로 [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) 에 커밋할 수 없습니다.

이 경고를 해결 하려면 기존 관리 되는 도메인을 삭제 하 고 개인 IP 주소 범위를 사용 하는 가상 네트워크에서 다시 만듭니다. 이 프로세스는 관리 되는 도메인을 사용할 수 없고 Ou 또는 서비스 계정 처럼 만든 사용자 지정 리소스가 손실 됨에 따라 중단 됩니다.

1. 디렉터리에서 [관리 되는 도메인을 삭제](delete-aadds.md) 합니다.
1. 가상 네트워크 IP 주소 범위를 업데이트 하려면 Azure Portal에서 *가상 네트워크* 를 검색 하 고 선택 합니다. 공용 IP 주소 범위 집합이 잘못 된 Azure AD DS의 가상 네트워크를 선택 합니다.
1. **설정**아래에서 *주소 공간*을 선택 합니다.
1. 기존 주소 범위를 선택 하 고 편집 하거나 추가 주소 범위를 추가 하 여 주소 범위를 업데이트 합니다. 새 IP 주소 범위가 개인 IP 범위에 있는지 확인 하세요. 준비가 되 면 변경 내용을 **저장** 합니다.
1. 왼쪽 탐색 영역에서 **서브넷** 을 선택 합니다.
1. 편집 하려는 서브넷을 선택 하거나 추가 서브넷을 만듭니다.
1. 개인 IP 주소 범위를 업데이트 하거나 지정 하 고 변경 내용을 **저장** 합니다.
1. [대체 관리 되는 도메인을 만듭니다](tutorial-create-instance.md). 개인 IP 주소 범위를 사용 하 여 업데이트 된 가상 네트워크 서브넷을 선택 했는지 확인 합니다.

관리 되는 도메인의 상태는 2 시간 내에 자동으로 업데이트 되 고 경고를 제거 합니다.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Azure 구독을 찾을 수 없음

### <a name="alert-message"></a>경고 메시지

*관리 되는 도메인과 연결 된 Azure 구독이 삭제 되었습니다.  제대로 작동 하려면 활성 구독이 필요 Azure AD Domain Services.*

### <a name="resolution"></a>해결 방법

Azure AD DS에는 활성 구독이 필요 하며 다른 구독으로 이동할 수 없습니다. 관리 되는 도메인이 연결 된 Azure 구독이 삭제 된 경우 Azure 구독 및 관리 되는 도메인을 다시 만들어야 합니다.

1. [Azure 구독을 만듭니다](../cost-management-billing/manage/create-subscription.md).
1. 기존 Azure AD 디렉터리에서 [관리 되는 도메인을 삭제](delete-aadds.md) 합니다.
1. [대체 관리 되는 도메인을 만듭니다](tutorial-create-instance.md).

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Azure 구독이 비활성화됨

### <a name="alert-message"></a>경고 메시지

*관리 되는 도메인과 연결 된 Azure 구독이 활성 상태가 아닙니다.  제대로 작동 하려면 활성 구독이 필요 Azure AD Domain Services.*

### <a name="resolution"></a>해결 방법

Azure AD DS에는 활성 구독이 필요 합니다. 관리 되는 도메인이 연결 된 Azure 구독이 활성 상태가 아닌 경우 갱신 하 여 구독을 다시 활성화 해야 합니다.

1. [Azure 구독을 갱신합니다](../cost-management-billing/manage/subscription-disabled.md).
2. 구독이 갱신 되 면 Azure AD DS 알림을 사용 하 여 관리 되는 도메인을 다시 사용 하도록 설정할 수 있습니다.

관리 되는 도메인을 다시 사용 하도록 설정 하면 관리 되는 도메인의 상태가 2 시간 내에 자동으로 업데이트 되 고 경고가 제거 됩니다.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: 구독 이동 디렉터리

### <a name="alert-message"></a>경고 메시지

*Azure AD Domain Services에서 사용 하는 구독이 다른 디렉터리로 이동 되었습니다. Azure AD Domain Services 제대로 작동 하려면 동일한 디렉터리에 활성 구독이 있어야 합니다.*

### <a name="resolution"></a>해결 방법

Azure AD DS에는 활성 구독이 필요 하며 다른 구독으로 이동할 수 없습니다. 관리 되는 도메인이 연결 된 Azure 구독이 이동 된 경우 구독을 이전 디렉터리로 다시 이동 하거나 기존 디렉터리에서 관리 되는 [도메인을 삭제](delete-aadds.md) 하 고 [선택한 구독에 대체 관리 되는 도메인을 만듭니다](tutorial-create-instance.md).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: 관리되는 도메인에 대한 리소스를 찾을 수 없음

### <a name="alert-message"></a>경고 메시지

*관리 되는 도메인에 사용 되는 리소스가 삭제 되었습니다. 이 리소스는 Azure AD Domain Services 제대로 작동 하는 데 필요 합니다.*

### <a name="resolution"></a>해결 방법

Azure AD DS는 공용 IP 주소, 가상 네트워크 인터페이스, 부하 분산 장치 등 제대로 작동 하는 추가 리소스를 만듭니다. 이러한 리소스 중 하나라도 삭제 되 면 관리 되는 도메인은 지원 되지 않는 상태가 되며 도메인이 관리 되지 않습니다. 이러한 리소스에 대 한 자세한 내용은 [Azure AD DS에서 사용 하는 네트워크 리소스](network-considerations.md#network-resources-used-by-azure-ad-ds)를 참조 하세요.

이 경고는 이러한 필수 리소스 중 하나를 삭제할 때 생성 됩니다. 리소스가 4 시간 이내에 삭제 된 경우 Azure 플랫폼에서 삭제 된 리소스를 자동으로 다시 만들 수 있습니다. 다음 단계에서는 리소스 삭제에 대 한 상태 및 타임 스탬프를 확인 하는 방법을 간략하게 설명 합니다.

1. Azure Portal에서 **도메인 서비스**를 검색 하 고 선택 합니다. 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. 왼쪽 탐색 영역에서 **상태**를 선택 합니다.
1. 상태 페이지에서 ID *AADDS109*를 사용 하 여 경고를 선택 합니다.
1. 경고에는 처음 발견 된 타임 스탬프가 있습니다. 해당 타임 스탬프가 4 시간 이전인 경우 Azure 플랫폼에서 자동으로 리소스를 다시 만들고 경고를 자체적으로 해결할 수 있습니다.

    경고가 4시간을 초과하면 관리되는 도메인은 복구할 수 없는 상태입니다. [관리](delete-aadds.md) 되는 도메인을 삭제 하 고 [대체 관리 되는 도메인을 만듭니다](tutorial-create-instance.md).

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: 관리되는 도메인에 연결된 서브넷이 꽉 찼음

### <a name="alert-message"></a>경고 메시지

*Azure AD Domain Services의 배포에 선택된 서브넷이 꽉 차서 만들어야 하는 추가 도메인 컨트롤러에 사용할 수 있는 공간이 없습니다.*

### <a name="resolution"></a>해결 방법

Azure AD DS의 가상 네트워크 서브넷에는 자동으로 생성 된 리소스에 대 한 IP 주소가 충분 해야 합니다. 이 IP 주소 공간에는 유지 관리 이벤트가 있는 경우 대체 리소스를 만들어야 하는 필요성이 포함 됩니다. 사용 가능한 IP 주소가 부족 하 게 되는 위험을 최소화 하려면 자체 Vm과 같은 추가 리소스를 관리 되는 도메인과 동일한 가상 네트워크 서브넷에 배포 하지 마세요.

이 오류는 복구할 수 없습니다. 이 경고를 해결 하려면 [기존 관리 되는 도메인을 삭제](delete-aadds.md) 하 고 다시 만듭니다. 관리 되는 도메인을 삭제 하는 데 문제가 있으면 추가 문제 해결 지원을 위해 [Azure 지원 요청을 여세요][azure-support] .

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: 서비스 주체 권한 없음

### <a name="alert-message"></a>경고 메시지

*도메인에 서비스를 사용 하는 데 사용 하 Azure AD Domain Services는 서비스 주체는 Azure 구독에서 리소스를 관리할 수 있는 권한이 없습니다. 서비스 사용자는 관리 되는 도메인을 서비스 하기 위한 권한을 얻어야 합니다.*

### <a name="resolution"></a>해결 방법

자동으로 생성 된 일부 서비스 주체는 관리 되는 도메인에 대 한 리소스를 관리 하 고 만드는 데 사용 됩니다. 이러한 서비스 사용자 중 한 명에 대 한 액세스 권한이 변경 되 면 도메인은 리소스를 올바르게 관리할 수 없습니다. 다음 단계에서는 서비스 주체에 대 한 액세스 권한을 이해 하 고 부여 하는 방법을 보여 줍니다.

1. [역할 기반 액세스 제어 및 Azure Portal 응용 프로그램에 대 한 액세스 권한을 부여 하는 방법](../role-based-access-control/role-assignments-portal.md)에 대해 알아봅니다.
2. ID *abba844e-bc0e-44b0-947a-dc74e5d09022* 를 사용 하는 서비스 주체에 대 한 액세스를 검토 하 고 이전 날짜에 거부 된 액세스 권한을 부여 합니다.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: 관리되는 도메인에 IP 주소가 충분하지 않음

### <a name="alert-message"></a>경고 메시지

*이 도메인에 있는 가상 네트워크의 서브넷에 IP 주소가 충분 하지 않은 것으로 확인 되었습니다. Azure AD Domain Services 사용 하도록 설정 된 서브넷 내에서 사용 가능한 IP 주소가 두 개 이상 필요 합니다. 서브넷 내에 최소 3-5의 예비 IP 주소가 있는 것이 좋습니다. 이는 서브넷 내에 다른 가상 컴퓨터를 배포 하 여 사용 가능한 IP 주소 수가 고갈 되었거나 서브넷에서 사용 가능한 IP 주소 수가 제한 된 경우에 발생할 수 있습니다.*

### <a name="resolution"></a>해결 방법

Azure AD DS의 가상 네트워크 서브넷에는 자동으로 생성 된 리소스에 대 한 충분 한 IP 주소가 필요 합니다. 이 IP 주소 공간에는 유지 관리 이벤트가 있는 경우 대체 리소스를 만들어야 하는 필요성이 포함 됩니다. 사용 가능한 IP 주소가 부족 하 게 되는 위험을 최소화 하려면 자체 Vm과 같은 추가 리소스를 관리 되는 도메인과 동일한 가상 네트워크 서브넷에 배포 하지 마세요.

이 경고를 해결 하려면 기존 관리 되는 도메인을 삭제 하 고 충분 한 IP 주소 범위를 가진 가상 네트워크에서 다시 만듭니다. 이 프로세스는 관리 되는 도메인을 사용할 수 없고 Ou 또는 서비스 계정 처럼 만든 사용자 지정 리소스가 손실 됨에 따라 중단 됩니다.

1. 디렉터리에서 [관리 되는 도메인을 삭제](delete-aadds.md) 합니다.
1. 가상 네트워크 IP 주소 범위를 업데이트 하려면 Azure Portal에서 *가상 네트워크* 를 검색 하 고 선택 합니다. IP 주소 범위가 작은 관리 되는 도메인에 대 한 가상 네트워크를 선택 합니다.
1. **설정**아래에서 *주소 공간*을 선택 합니다.
1. 기존 주소 범위를 선택 하 고 편집 하거나 추가 주소 범위를 추가 하 여 주소 범위를 업데이트 합니다. 새 IP 주소 범위가 관리 되는 도메인의 서브넷 범위에 대해 충분히 큰지 확인 합니다. 준비가 되 면 변경 내용을 **저장** 합니다.
1. 왼쪽 탐색 영역에서 **서브넷** 을 선택 합니다.
1. 편집 하려는 서브넷을 선택 하거나 추가 서브넷을 만듭니다.
1. 충분 한 IP 주소 범위를 업데이트 하거나 지정 하 고 변경 내용을 **저장** 합니다.
1. [대체 관리 되는 도메인을 만듭니다](tutorial-create-instance.md). IP 주소 범위가 충분히 많은 업데이트 된 가상 네트워크 서브넷을 선택 해야 합니다.

관리 되는 도메인의 상태는 2 시간 내에 자동으로 업데이트 되 고 경고를 제거 합니다.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: 리소스를 복구할 수 없음

### <a name="alert-message"></a>경고 메시지

*Azure AD Domain Services에서 사용되는 리소스가 예기치 않은 상태로 발견되었으며 복구할 수 없습니다.*

### <a name="resolution"></a>해결 방법

이 오류는 복구할 수 없습니다. 이 경고를 해결 하려면 [기존 관리 되는 도메인을 삭제](delete-aadds.md) 하 고 다시 만듭니다. 관리 되는 도메인을 삭제 하는 데 문제가 있으면 추가 문제 해결 지원을 위해 [Azure 지원 요청을 여세요][azure-support] .

## <a name="aadds114-subnet-invalid"></a>AADDS114: 잘못된 서브넷

### <a name="alert-message"></a>경고 메시지

*Azure AD Domain Services 배포를 위해 선택한 서브넷이 잘못 되었으므로 사용할 수 없습니다.*

### <a name="resolution"></a>해결 방법

이 오류는 복구할 수 없습니다. 이 경고를 해결 하려면 [기존 관리 되는 도메인을 삭제](delete-aadds.md) 하 고 다시 만듭니다. 관리 되는 도메인을 삭제 하는 데 문제가 있으면 추가 문제 해결 지원을 위해 [Azure 지원 요청을 여세요][azure-support] .

## <a name="aadds115-resources-are-locked"></a>AADDS115: 리소스가 잠김

### <a name="alert-message"></a>경고 메시지

*대상 범위가 잠겨 관리되는 도메인에 사용되는 하나 이상의 네트워크 리소스를 사용할 수 없습니다.*

### <a name="resolution"></a>해결 방법

변경 또는 삭제를 방지 하기 위해 Azure 리소스에 리소스 잠금을 적용할 수 있습니다. Azure AD DS는 관리 되는 서비스 이므로 Azure 플랫폼에서 구성 변경을 수행 하는 기능이 필요 합니다. 리소스 잠금이 일부 Azure AD DS 구성 요소에 적용 되는 경우 Azure 플랫폼은 해당 관리 작업을 수행할 수 없습니다.

Azure AD DS 구성 요소에 대 한 리소스 잠금을 확인 하 고 제거 하려면 다음 단계를 완료 합니다.

1. 리소스 그룹에서 가상 네트워크, 네트워크 인터페이스, 공용 IP 주소와 같은 각 관리 되는 도메인의 네트워크 구성 요소에 대해 Azure Portal의 작업 로그를 확인 합니다. 이러한 작업 로그에는 작업이 실패 하 고 리소스 잠금이 적용 되는 이유가 표시 됩니다.
1. 잠금이 적용 되는 리소스를 선택 하 고 **잠금 아래에서**잠금을 선택 하 고 제거 합니다.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: 리소스를 사용할 수 없음

### <a name="alert-message"></a>경고 메시지

*정책 제한으로 인해 관리되는 도메인에 사용되는 하나 이상의 네트워크 리소스를 사용할 수 없습니다.*

### <a name="resolution"></a>해결 방법

정책은 허용 되는 구성 작업을 제어 하는 Azure 리소스 및 리소스 그룹에 적용 됩니다. Azure AD DS는 관리 되는 서비스 이므로 Azure 플랫폼에서 구성 변경을 수행 하는 기능이 필요 합니다. 일부 Azure AD DS 구성 요소에 정책이 적용 되는 경우 Azure 플랫폼은 관리 작업을 수행 하지 못할 수 있습니다.

Azure AD DS 구성 요소에서 적용 된 정책을 확인 하 고 업데이트 하려면 다음 단계를 완료 합니다.

1. 리소스 그룹에서 가상 네트워크, NIC 또는 공용 IP 주소와 같은 관리 되는 도메인의 각 네트워크 구성 요소에 대해 Azure Portal의 작업 로그를 확인 합니다. 이러한 작업 로그에는 작업이 실패 하 고 제한 된 정책이 적용 되는 이유가 표시 됩니다.
1. 정책이 적용 되는 리소스를 선택한 다음 **정책에서 정책을**선택 하 고 편집 하 여 덜 제한적입니다.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: 잠시 후에 동기화가 완료되지 않았습니다.

### <a name="alert-message"></a>경고 메시지

*관리 되는 도메인은 [date]에서 Azure AD와 마지막으로 동기화 되었습니다. 사용자가 관리 되는 도메인에 로그인 할 수 없거나 그룹 구성원이 Azure AD와 동기화 되지 않았을 수 있습니다.*

### <a name="resolution"></a>해결 방법

관리 되는 도메인의 구성에서 문제를 나타내는 모든 경고에 대해 [Azure AD DS 상태를 확인 합니다](check-health.md) . 네트워크 구성 문제로 인해 Azure AD에서 동기화가 차단 될 수 있습니다. 구성 문제를 나타내는 경고를 해결할 수 있는 경우 2 시간 동안 기다렸다가 동기화가 성공적으로 완료 되었는지 확인 합니다.

다음과 같은 일반적인 이유 때문에 관리 되는 도메인에서 동기화가 중지 됩니다.

* 필요한 네트워크 연결이 차단 되었습니다. Azure virtual network에 문제가 있는지 확인 하 고 필요한 사항을 확인 하는 방법에 대 한 자세한 내용은 [네트워크 보안 그룹 문제 해결](alert-nsg.md) 및 [azure AD DS에 대 한 네트워크 요구 사항](network-considerations.md)을 참조 하세요.
*  관리 되는 도메인이 배포 될 때 암호 동기화가 설정 되지 않았거나 완료 되었습니다. [온-프레미스에서](tutorial-configure-password-hash-sync.md) [클라우드 전용 사용자](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) 또는 하이브리드 사용자에 대 한 암호 동기화를 설정할 수 있습니다.

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: 잠시 후에 백업이 수행되지 않았습니다.

### <a name="alert-message"></a>경고 메시지

*관리되는 도메인은 마지막으로 [date]에 백업되었습니다.*

### <a name="resolution"></a>해결 방법

[Azure AD DS 상태에서](check-health.md) 관리 되는 도메인의 구성에 문제가 있음을 나타내는 경고를 확인 합니다. 네트워크 구성과 관련 된 문제는 Azure 플랫폼이 백업을 성공적으로 수행 하지 못하도록 차단할 수 있습니다. 구성 문제를 나타내는 경고를 해결할 수 있는 경우 2 시간 동안 기다렸다가 동기화가 성공적으로 완료 되었는지 확인 합니다.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: 비활성화된 구독으로 인한 일시 중단

### <a name="alert-message"></a>경고 메시지

*해당 도메인과 연결된 Azure 구독이 활성 상태가 아니기 때문에 관리되는 도메인은 일시 중단됩니다.*

### <a name="resolution"></a>해결 방법

> [!WARNING]
> 관리 되는 도메인이 오랫동안 일시 중단 되 면 삭제 될 위험이 있습니다. 가능한 한 빨리 일시 중단의 원인을 해결 합니다. 자세한 내용은 [Azure AD DS의 일시 중단 된 상태 이해](suspension.md)를 참조 하세요.

Azure AD DS에는 활성 구독이 필요 합니다. 관리 되는 도메인이 연결 된 Azure 구독이 활성 상태가 아닌 경우 갱신 하 여 구독을 다시 활성화 해야 합니다.

1. [Azure 구독을 갱신합니다](../cost-management-billing/manage/subscription-disabled.md).
2. 구독이 갱신 되 면 Azure AD DS 알림을 사용 하 여 관리 되는 도메인을 다시 사용 하도록 설정할 수 있습니다.

관리 되는 도메인을 다시 사용 하도록 설정 하면 관리 되는 도메인의 상태가 2 시간 내에 자동으로 업데이트 되 고 경고가 제거 됩니다.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: 잘못된 구성으로 인한 일시 중단

### <a name="alert-message"></a>경고 메시지

*잘못 된 구성으로 인해 관리 되는 도메인이 일시 중단 되었습니다. 서비스에서 오랜 시간 동안 관리 되는 도메인에 대 한 도메인 컨트롤러를 관리, 패치 또는 업데이트할 수 없습니다.*

### <a name="resolution"></a>해결 방법

> [!WARNING]
> 관리 되는 도메인이 오랫동안 일시 중단 되 면 삭제 될 위험이 있습니다. 가능한 한 빨리 일시 중단의 원인을 해결 합니다. 자세한 내용은 [Azure AD DS의 일시 중단 된 상태 이해](suspension.md)를 참조 하세요.

[Azure AD DS 상태에서](check-health.md) 관리 되는 도메인의 구성에 문제가 있음을 나타내는 경고를 확인 합니다. 구성 문제를 나타내는 경고를 해결할 수 있는 경우 2 시간 동안 기다렸다가 동기화가 완료 되었는지 확인 합니다. 준비가 되 면 [Azure 지원 요청을 열어][azure-support] 관리 되는 도메인을 다시 사용 하도록 설정 합니다.

## <a name="next-steps"></a>다음 단계

문제가 여전히 발생하는 경우 추가 문제 해결 지원을 위해 [Azure 지원 요청을 엽니다][azure-support].

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md