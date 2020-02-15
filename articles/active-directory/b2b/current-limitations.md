---
title: B2B 공동 작업의 제한 사항-Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B 협업의 현재 제한 사항
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1717897261404a2ab8df723c280c9be6a2dacea4
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77196136"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure AD B2B 협업의 제한 사항
Azure AD(Azure Active Directory) B2B 협업에는 이 문서에 설명된 제한 사항이 적용됩니다.

## <a name="possible-double-multi-factor-authentication"></a>가능한 이중 다단계 인증
Azure AD B2B를 통해 리소스 조직(초대하는 조직)에서 다단계 인증을 적용할 수 있습니다. 이 접근 방식에 대 한 이유는 [B2B 공동 작업 사용자에 대 한 조건부 액세스](conditional-access.md)에 자세히 설명 되어 있습니다. 파트너가 이미 Multi-Factor Authentication을 설정하여 적용 중인 경우 해당 사용자는 소속된 조직에서 인증을 한 번 수행한 후 여러분의 조직에서 다시 한 번 인증을 수행해야 할 수도 있습니다.

## <a name="instant-on"></a>인스턴트 온
B2B 협업 흐름에서 해당 디렉터리에 사용자를 추가하고 초대 상환, 앱 할당 등에서 사용자를 동적으로 업데이트합니다. 업데이트 및 쓰기는 일반적으로 한 디렉터리 인스턴스에서 발생하며 모든 인스턴스에 걸쳐 복제해야 합니다. 모든 인스턴스가 업데이트되면 복제가 완료됩니다. 개체가 한 인스턴스에서 기록 또는 업데이트되고 이 개체를 검색하기 위한 호출이 다른 인스턴스에 대해 수행될 경우 복제 대기 시간이 발생할 수 있습니다. 이 문제가 발생할 경우 새로 고치거나 다시 시도하면 도움이 됩니다. API를 사용하여 앱을 작성할 경우 백오프를 사용하여 다시 시도하는 것은 이 문제를 완화하기 위한 좋은 방어 사례입니다.

## <a name="azure-ad-directories"></a>Azure AD 디렉터리
Azure AD B2B에는 Azure AD 서비스 디렉터리 제한이 적용됩니다. 사용자가 만들 수 있는 디렉터리 수와 사용자 또는 게스트 사용자가 속할 수 있는 디렉터리 수에 대한 자세한 내용은 [Azure AD 서비스 한도 및 제한](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)을 참조하세요.

## <a name="national-clouds"></a>국가별 클라우드
[국가별 클라우드](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) 는 실제로 격리 된 Azure 인스턴스입니다. B2B 공동 작업은 국가별 클라우드 경계에서 지원 되지 않습니다. 예를 들어 Azure 테 넌 트가 공용 글로벌 클라우드에 있는 경우 해당 계정이 국가 클라우드에 있는 사용자를 초대할 수 없습니다. 사용자와 공동 작업을 하려면 다른 전자 메일 주소를 요청 하거나 디렉터리에서 구성원 사용자 계정을 만들어야 합니다.

## <a name="azure-us-government-clouds"></a>Azure 미국 정부 클라우드
Azure 미국 정부 클라우드 내에서 B2B 공동 작업은 현재 Azure 미국 정부 클라우드 내에 있고 B2B 공동 작업을 지 원하는 테 넌 트 간에만 지원 됩니다. Azure US 정부 클라우드의 일부가 아니거나 아직 B2B 공동 작업을 지원 하지 않는 테 넌 트에 사용자를 초대 하는 경우 초대가 실패 하거나 사용자가 초대를 교환할 수 없습니다. 기타 제한 사항에 대 한 자세한 내용은 [Azure Active Directory Premium P1 및 P2 변형](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2)을 참조 하세요.

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Azure US 정부 테 넌 트에서 B2B 공동 작업을 사용할 수 있는지 어떻게 알 수 있나요?
Azure 미국 정부 클라우드 테 넌 트가 B2B 공동 작업을 지원 하는지 확인 하려면 다음을 수행 합니다.

1. 브라우저에서 다음 URL로 이동 하 여 *&lt;tenantname&gt;* 에 대 한 테 넌 트 이름을 대체 합니다.

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. JSON 응답에서 `"tenant_region_scope"`를 찾습니다.

   - `"tenant_region_scope":"USGOV”` 표시 되는 경우 B2B가 지원 됩니다.
   - `"tenant_region_scope":"USG"` 표시 되는 경우 B2B는 지원 되지 않습니다.
 

## <a name="next-steps"></a>다음 단계

Azure AD B2B 협업에 대한 다음 문서를 살펴보세요.

- [Azure AD B2B 협업이란?](what-is-b2b.md)
- [B2B 협업 초대 위임](delegate-invitations.md)

