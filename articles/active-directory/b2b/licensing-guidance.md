---
title: B2B 공동 작업 라이선스 지침-Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B 협업에는 유료 Azure AD 라이선스가 필요하지 않지만 B2B 게스트 사용자를 위한 유료 기능도 얻을 수 있습니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28d02160163da4081ad8adbe233b27fee970a0df
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74868850"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Azure Active Directory B2B collaboration 라이선싱 지침

Azure AD(Active Directory) B2B(기업 간) 협업 시에는 유료 Azure AD 서비스를 사용하도록 외부 사용자("게스트 사용자")를 초대할 수 있습니다. 일부 기능은 무료 이지만 유료 Azure AD 기능에 대해 테넌트의 직원 또는 비 게스트 사용자에 대해 소유 하 고 있는 각 Azure AD 버전 라이선스에 대해 최대 5 명의 게스트 사용자를 초대할 수 있습니다.

> [!NOTE]
> Azure AD 가격 책정 및 B2B 공동 작업 기능에 대 한 자세한 내용은 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/) 을 참조 하세요.

B2B 게스트 사용자 라이선싱은 1:5 비율을 기준으로 자동 계산되어 보고됩니다. 

또한 게스트 사용자는 추가 라이선싱 요구 사항 없이 무료 Azure AD 기능을 사용할 수 있습니다. 유료 Azure AD 라이선스가 없어도 게스트 사용자는 무료 Azure AD 기능에 액세스할 수 있습니다. 

## <a name="examples-calculating-guest-user-licenses"></a>예제: 게스트 사용자 라이선스 계산
유료 Azure AD 서비스에 액세스해야 하는 게스트 사용자의 수를 확인한 후에는 필요한 1:5 비율로 게스트 사용자에게 제공하기에 충분한 Azure AD 유료 라이선스가 있는지 확인합니다. 예를 들어 다음과 같은 노래를 선택할 수 있다.

- 100 게스트 사용자를 Azure AD 앱 또는 서비스에 초대 하 고 액세스 관리 및 프로 비전을 제공 하려고 합니다. 이러한 게스트 사용자 중 50의 경우 MFA 및 조건부 액세스를 요구 하 여 해당 기능에 대해 10 Azure AD Premium P1 라이선스가 필요 합니다. 게스트 사용자에게 Identity Protection 기능을 사용하려는 경우에는 게스트 사용자에게 적용되는 것과 같은 1:5 비율에 해당하는 Azure AD Premium P2 라이선스가 필요합니다.
- 게스트 사용자 60명을 초대하고 모든 게스트 사용자가 MFA를 사용해야 하도록 설정하려는 경우 Azure AD Premium P1 라이선스가 12개 이상 필요합니다. Azure AD Premium P1 라이선스 보유 직원 수가 10명이라면 1:5 비율에 따라 게스트 사용자를 50명까지 초대할 수 있습니다. 이 경우 게스트 사용자 10명을 더 초대하려면 Premium P1 라이선스 2개를 추가로 구매해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 협업에 대한 다음 리소스를 확인합니다.

* [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure AD B2B 협업이란?](what-is-b2b.md)
* [Azure Active Directory B2B 협업 자주 묻는 질문(FAQ)](faq.md)
