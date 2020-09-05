---
title: Azure AD 외부 Id에 대 한 MAU 청구 모델
description: Azure AD에서 B2B (게스트 사용자 공동 작업)에 대 한 Azure AD 외부 Id MAU (월간 활성 사용자) 청구 모델에 대해 알아봅니다. Azure AD 테 넌 트를 Azure 구독에 연결 하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12aa400d6ca44043d3d90e78a93ae49d97a927e8
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270299"
---
# <a name="billing-model-for-azure-ad-external-identities"></a>Azure AD 외부 Id에 대 한 청구 모델

Azure AD (Azure Active Directory) 외부 Id 가격은 월별 활성 사용자 (MAU)를 기반으로 합니다 .이는 일정 월 내에 인증 활동이 있는 고유한 사용자의 수입니다. 이 청구 모델은 Azure AD 게스트 B2B (사용자 공동 작업) 및 [Azure AD B2C 테 넌 트](https://docs.microsoft.com/azure/active-directory-b2c/billing)에 모두 적용 됩니다. MAU 청구는 무료 계층과 유연 하 고 예측 가능한 가격을 제공 하 여 비용을 절감 하는 데 도움이 됩니다. 이 문서에서는 MAU 청구에 대해 알아보고 구독에 Azure AD 테 넌 트를 연결 합니다.

> [!IMPORTANT]
> 이 문서에는 가격 정보가 포함 되어 있지 않습니다. 사용량 청구 및 가격 책정에 대 한 최신 정보는 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)을 참조 하세요.

## <a name="what-do-i-need-to-do"></a>무엇을 해야 하나요?

MAU 청구 기능을 활용 하려면 Azure AD 테 넌 트가 Azure 구독에 연결 되어 있어야 합니다.

|테 넌 트가 다음과 같은 경우:  |그러려면 다음 작업을 수행해야 합니다.  |
|---------|---------|
| Azure AD 테 넌 트가 구독에 이미 연결 되어 있습니다.     | 아무 작업도 하지 않습니다. 외부 Id 기능을 사용 하 여 게스트 사용자와 공동 작업 하는 경우 MAU 모델을 사용 하 여 자동으로 청구 됩니다.        |
| Azure AD 테 넌 트가 아직 구독에 연결 되지 않았습니다.     | [구독에 AZURE AD 테 넌 트를 연결](#link-your-azure-ad-tenant-to-a-subscription) 하 여 mau 청구를 활성화 합니다.        |
|  |  |

## <a name="about-monthly-active-users-mau-billing"></a>MAU (월간 활성 사용자) 청구

Azure AD 테 넌 트에서 게스트 사용자 공동 작업 사용은 일정 월 내에 인증 활동을 사용 하는 고유 게스트 사용자 수를 기준으로 요금이 청구 됩니다. 이 모델은 테 넌 트의 각 Azure AD Premium 라이선스에 대해 최대 5 명의 게스트 사용자를 허용 하는 1:5 비율 청구 모델을 대체 합니다. 테 넌 트가 구독에 연결 되어 있고 외부 Id 기능을 사용 하 여 게스트 사용자와 공동 작업 하는 경우 MAU 기반 청구 모델을 사용 하 여 자동으로 청구 됩니다.
  
게스트 사용자에 게 적용 되는 가격 책정 계층은 Azure AD 테 넌 트에 할당 된 최고 가격 책정 계층을 기반으로 합니다. 예를 들어 테 넌 트의 최고 가격 책정 계층이 p 1을 Azure AD Premium 하는 경우 프리미엄 P1 가격 책정 계층도 게스트 사용자에 게 적용 됩니다. 가장 높은 가격은 Azure AD Free 경우 게스트 사용자에 게 프리미엄 기능을 사용 하려고 할 때 프리미엄 가격 책정 계층으로 업그레이드 하 라는 메시지가 표시 됩니다.

## <a name="link-your-azure-ad-tenant-to-a-subscription"></a>구독에 Azure AD 테 넌 트 연결

Azure AD 테 넌 트는 적절 한 청구 및 기능에 대 한 액세스를 위해 Azure 구독에 연결 되어야 합니다.

1. 구독 내에서 적어도 [참가자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) 역할이 할당 된 Azure 계정 또는 구독 내의 리소스 그룹을 사용 하 여 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.

2. 구독을 포함 하는 디렉터리를 선택 합니다. Azure Portal 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택 하 고 구독을 포함 하는 디렉터리를 선택 합니다.

    ![디렉터리 + 구독 아이콘을 선택 합니다.](media/external-identities-pricing/portal-mau-pick-directory.png)

3. **Azure 서비스**에서 **Azure Active Directory**를 선택 합니다.

4. 왼쪽 메뉴에서 **외부 ID**를 선택합니다.

5. **구독**아래에서 **연결 된 구독**을 선택 합니다.

6. 테 넌 트 목록에서 테 넌 트 옆의 확인란을 선택 하 고 **구독 연결**을 선택 합니다.

    ![테 넌 트를 선택 하 고 구독 연결](media/external-identities-pricing/linked-subscriptions.png)

7. 구독 링크 창에서 **구독** 및 **리소스 그룹**을 선택 합니다. 그런 다음, **적용**을 선택합니다.

    ![구독 및 리소스 그룹 선택](media/external-identities-pricing/link-subscription-resource.png)

이러한 단계를 완료 한 후에는 azure Direct 또는 기업계약 세부 정보 (해당 하는 경우)를 기준으로 Azure 구독에 대 한 요금이 청구 됩니다.

## <a name="next-steps"></a>다음 단계

최신 가격 책정 정보는 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)을 참조 하세요.
