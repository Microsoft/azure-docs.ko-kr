---
title: Azure AD Connect Health - 중복 특성 동기화 오류 진단 | Microsoft Docs
description: 이 문서에서는 중복 특성 동기화 오류의 진단 프로세스 및 Azure Portal에서 직접 분리된 개체 시나리오의 잠재적 해결을 설명합니다.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: billmath
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/11/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d48483697970333c542f140567ca6a6d3fcf7d2
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179150"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>중복된 특성 동기화 오류 진단 및 수정

## <a name="overview"></a>개요
한 단계 더 나아가서 동기화 오류를 강조하는 Azure AD(Azure Active Directory) Connect Health는 셀프 서비스 업데이트 관리를 도입했습니다. 중복된 특성 동기화 오류를 해결하고 Azure AD에서 분리된 개체를 수정합니다.
이 진단 기능은 다음과 같은 장점이 있습니다.
- 중복된 특성 동기화 오류의 범위를 좁히는 진단 프로시저를 제공합니다. 관련 픽스를 제공합니다.
- 한 단계로 오류를 해결하는 Azure AD의 전용 시나리오에 대한 픽스를 적용합니다.
- 이 기능을 사용하기위 해 업그레이드 또는 구성이 필요하지 않습니다.
Azure AD에 대한 자세한 내용은 [ID 동기화 및 중복 특성 복원력](how-to-connect-syncservice-duplicate-attribute-resiliency.md)을 참조하세요.

## <a name="problems"></a>문제
### <a name="a-common-scenario"></a>일반적인 시나리오
**QuarantinedAttributeValueMustBeUnique** 및 **AttributeValueMustBeUnique** 동기화 오류가 발생하는 경우 일반적으로 Azure AD에서 **UserPrincipalName** 또는 **프록시 주소** 충돌이 발생합니다. 온-프레미스 쪽에서 충돌하는 원본 개체를 업데이트하여 동기화 오류를 해결할 수 있습니다. 다음 동기화 이후에 동기화 오류가 해결 됩니다. 예를 들어이 이미지는 두 사용자가 **UserPrincipalName**충돌이 있음을 나타냅니다. 둘 다 **Joe. J \@ contoso.com**입니다. 충돌하는 개체는 Azure AD에서 격리됩니다.

![동기화 오류 진단 일반적인 시나리오](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>분리된 개체 시나리오
때때로 기존 사용자가 **원본 앵커**를 잃을 수 있습니다. 원본 개체 삭제는 온-프레미스 Active Directory에서 발생했습니다. 하지만 삭제 신호 변경 내용이 Azure AD와 동기화되지 않았습니다. 동기화 엔진 문제 또는 도메인 마이그레이션 같은 이유로 이 손실이 발생합니다. 동일한 개체가 복원되거나 다시 생성되는 경우 논리적으로 기존 사용자는 **원본 앵커**로부터 동기화하는 사용자여야 합니다. 

기존 사용자가 클라우드 전용 개체인 경우 Azure AD로 동기화되는 사용자가 충돌하는 것을 볼 수 있습니다. 이 사용자는 기존 개체와 동기화할 수 없습니다. **원본 앵커**를 다시 매핑하는 직접적인 방법은 없습니다. 자세한 내용은 [기존 기술 자료](https://support.microsoft.com/help/2647098)를 참조하세요. 

예를 들어 Azure AD의 기존 개체는 Joe의 라이선스를 유지합니다. 다른 **원본 앵커**와 새로 동기화된 개체는 Azure AD의 중복 특성 상태에서 발생합니다. 온-프레미스 Active Directory에서 Joe의 변경 내용은 Azure AD에서 Joe의 원래 사용자(기존 개체)에 적용되지 않습니다.  

![동기화 오류 분리된 개체 진단 시나리오](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Connect Health의 진단 및 문제 해결 단계 
진단 기능은 다음과 같은 중복 특성이 있는 사용자 개체를 지원합니다.

| 특성 이름 | 동기화 오류 형식|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique 또는 AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique 또는 AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> 이 기능에 액세스 하려면 Azure RBAC의 **전역 관리자** 권한 또는 **참가자** 권한이 필요 합니다.
>

Azure Portal의 단계에 따라 동기화 오류 세부 정보 범위를 좁히고 좀 더 구체적인 솔루션을 제공하세요.

![동기화 오류 진단 단계](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

Azure Portal에서 몇 가지 단계를 수행하여 수정 가능한 시나리오를 식별합니다.  
1.  **상태 진단** 열을 확인합니다. 상태를 보면 Azure Active Directory에서 직접 동기화 오류를 수정할 수 있는지 알 수 있습니다. 즉, 오류 사례 범위를 좁히고 오류를 해결할 수 있는 문제 해결 흐름이 있습니다.

| Status | 무엇을 의미하나요? |
| ------------------ | -----------------|
| 시작 안함 | 이 진단 프로세스를 방문하지 않았습니다. 진단 결과에 따라 포털에서 바로 동기화 오류를 해결하는 방법이 있습니다. |
| 수동 수정 필요 | 이 오류는 포털에서 사용할 수 있는 수정 기준에 맞지 않습니다. 충돌하는 개체 형식이 사용자가 아니거나, 이미 진단 단계를 진행했지만 포털에서 사용할 수 있는 해결 방법이 없습니다. 후자에 속하는 경우 온-프레미스 쪽에서의 수정은 여전히 솔루션 중 하나입니다. [온-프레미스 수정에 대해 자세히 알아보세요](https://support.microsoft.com/help/2647098). | 
| 동기화 보류 중 | 수정이 적용되었습니다. 그 다음 동기화 주기에서 오류가 수정될 때까지 포털이 대기 중입니다. |

  >[!IMPORTANT]
  > 진단 상태 열은 각 동기화 주기 후에 재설정됩니다. 
  >

1. 오류 세부 정보 아래에서 **진단** 단추를 선택하세요. 그리고 몇 가지 질문에 답변하고 동기화 오류 세부 정보를 식별합니다. 질문에 답하여 분리된 개체 사례를 식별할 수 있습니다.

1. 진단 마지막에 **닫기** 단추가 표시되면 사용자의 답변에 따라 포털에서 사용할 수 있는 빠른 수정이 없습니다. 마지막 단계에 표시된 솔루션을 참조하세요. 여전히 온-프레미스에서 수정할 수 있습니다. **닫기** 단추를 선택하세요. 현재 동기화 오류 상태가 **수동 수정 필요**로 전환됩니다. 현재 동기화 주기가 끝날 때까지 상태가 유지됩니다.

1. 분리된 개체 사례가 식별되면 포털에서 바로 중복된 특성 동기화 오류를 해결할 수 있습니다. 프로세스를 트리거하려면 **해결 적용** 단추를 클릭합니다. 현재 동기화 오류 상태가 **동기화 보류 중**으로 업데이트됩니다.

1. 그 다음 동기화 주기가 완료되면 목록에서 오류가 제거됩니다.

## <a name="how-to-answer-the-diagnosis-questions"></a>진단 질문에 답변하는 방법 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>온-프레미스 Active Directory에 사용자가 있나요?

이 질문의 목적은 온-프레미스 Active Directory에서 기존 사용자의 원본 개체를 식별하는 것입니다.  
1. Azure Active Directory에 제공된 **UserPrincipalName**이 포함된 개체가 있는지 확인합니다. 없으면 **아니요**를 선택합니다.
2. 있으면 해당 개체가 여전히 동기화 범위 내에 있는지 확인합니다.  
   - DN을 사용하여 Azure AD Connector 공간을 검색합니다.
   - 개체 상태가 **추가 보류 중**이면 **아니요**를 선택합니다. Azure AD Connect가 개체를 올바른 AD 개체에 연결할 수 없습니다.
   - 개체를 찾을 수 없는 경우 **예**를 선택합니다.

이 예제에서 질문의 목적은 **Joe Jackson**이 여전히 온-프레미스 Active Directory에 있는지 확인하는 것입니다.
**일반적인 시나리오**에서는 두 사용자 **Joe Johnson** 및 **Joe Jackson**이 온-프레미스 Active Directory에 있습니다. 격리된 개체는 두 명의 다른 사용자입니다.

![동기화 오류 진단 일반적인 시나리오](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

**분리된 개체 시나리오**에서는 사용자 **Joe Johnson**만이 온-프레미스 Active Directory에 있습니다.

![동기화 오류 분리된 개체 진단 *사용자 있음* 시나리오](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>두 계정이 동일한 사용자에게 속해 있나요?
이 질문의 목적은 들어오는 충돌하는 사용자와 Azure AD의 기존 사용자 개체를 확인하여 둘 다 같은 사용자에게 속해 있는지 확인하는 것입니다.  
1. 충돌하는 개체는 Azure Active Directory에 새로 동기화됩니다. 개체의 특성을 비교합니다.  
   - 표시 이름
   - 사용자 계정 이름
   - 개체 ID입니다.
2. Azure AD가 개체 비교에 실패할 경우 제공된 **UserPrincipalNames**가 포함된 개체가 Active Directory에 있는지 확인합니다. 둘 다 있으면 **아니요**를 선택합니다.

다음 예에서 두 개체는 동일한 사용자 **Joe Johnson**에게 속합니다.

![동기화 오류 분리된 개체 진단 *동일한 사용자* 시나리오](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>분리된 개체 시나리오에서 수정이 적용되면 발생하는 일
앞의 질문에 대한 답변에 따라 Azure AD에서 사용할 수 있는 수정 내용이 있는 경우 **수정 적용** 단추가 표시됩니다. 이 경우 온-프레미스 개체가 예기치 않은 Azure AD 개체와 동기화됩니다. 두 개체는 **원본 앵커**를 사용하여 매핑됩니다. **수정 적용** 변경은 다음과 비슷한 단계를 수행합니다.
1. **원본 앵커**를 Azure AD에서 올바른 개체로 업데이트합니다.
2. Azure AD에 충돌하는 개체가 있는 경우 해당 개체를 삭제합니다.

![수정 후 동기화 오류 진단](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> **수정 적용** 변경은 분리된 개체 사례에만 적용됩니다.
>

위의 단계를 수행한 후에는 사용자가 기존 개체에 대 한 링크인 원래 리소스에 액세스할 수 있습니다. 목록 뷰의 **진단 상태** 값은 **보류 중인 동기화**로 업데이트 됩니다. 다음 동기화 이후에 동기화 오류가 해결 됩니다. 연결 상태는 더 이상 목록 보기에 확인 되지 않은 동기화 오류를 표시 하지 않습니다.

## <a name="failures-and-error-messages"></a>오류 및 오류 메시지
**충돌 하는 특성이 있는 사용자는 Azure Active Directory에서 일시 삭제 됩니다. 다시 시도 하기 전에 사용자가 하드 삭제 되었는지 확인 합니다.**  
수정을 적용하기 전에 Azure AD에서 충돌하는 특성이 있는 사용자를 정리해야 합니다. 해당 수정을 다시 시도 하기 전에 [Azure AD에서 사용자를 영구 삭제하는 방법](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore)을 확인합니다. 사용자는 일시 삭제된 상태에서 30일이 지나면 자동으로 영구 삭제됩니다. 

**원본 앵커에서 테넌트의 클라우드 기반 사용자로 업데이트하는 것은 지원되지 않습니다.**  
Azure AD의 클라우드 기반 사용자에게는 원본 앵커가 없어야 합니다. 이 경우에 원본 앵커의 업데이트는 지원되지 않습니다. 온-프레미스에서 수동 수정이 필요합니다. 

## <a name="faq"></a>FAQ
**Q.** **수정 적용** 실행이 실패하면 어떻게 되나요?  
**A.** 실행이 실패한 경우 Azure AD Connect가 내보내기 오류를 실행 중일 수 있습니다. 포털 페이지를 새로 고치고 다음 동기화 후 다시 시도 합니다. 기본 동기화 주기는 30 분입니다. 


**Q.** **기존 개체**가 삭제할 개체여야 하는 경우 어떻게 하나요?  
**A.** **기존 개체**를 삭제해야 하는 경우 프로세스에 **원본 앵커**의 변경이 포함되지 않습니다. 일반적으로 온-프레미스 Active Directory에서 수정할 수 있습니다. 


**Q.** 수정을 적용하려면 사용자에게 어떤 권한이 필요한가요?  
**A.** Azure RBAC의 **전역 관리자**또는 **참가자** 에 게는 진단 및 문제 해결 프로세스에 액세스할 수 있는 권한이 있습니다.


**Q.** 이 기능에 대해 Azure AD Connect를 구성하거나 Azure AD Connect Health 에이전트를 업데이트해야 하나요?  
**A.** 아니요, 진단 프로세스는 완전한 클라우드 기반 기능입니다.


**Q.** 기존 개체가 일시 삭제되면 진단 프로세스에서 개체를 다시 활성 상태로 만드나요?  
**A.** 아니요, 수정은 **원본 앵커** 이외의 개체 특성을 업데이트하지 않습니다.
