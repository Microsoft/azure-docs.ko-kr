---
title: 조건부 액세스 정책 마이그레이션 - Azure Active Directory
description: Azure Portal에서 클래식 정책을 마이그레이션하기 위해 알아야 하는 작업을 자세히 살펴봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33b1f76dd1489e00115d0f805add8d754038df84
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185920"
---
# <a name="conditional-access-classic-policy-migration"></a>조건부 액세스 클래식 정책 마이그레이션

조건부 액세스는 Azure Active Directory에서 신호를 주고 결정을 내리고 조직 정책을 적용하기 위해 사용하는 도구입니다. 조건부 액세스는 새로운 ID 중심 제어 평면의 핵심입니다. 용도는 동일 하지만, 새 Azure Portal의 릴리스는 조건부 액세스의 작동 방식에 대 한 상당한 개선 사항을 도입 했습니다.

Azure Portal에서 만들지 않은 정책을 마이그레이션하는 것을 고려해야 하는 이유는 다음과 같습니다.

- 이전에는 처리할 수 없었던 시나리오를 이제 처리할 수 있습니다.
- 통합하여 관리해야 하는 정책의 수를 줄일 수 있습니다.
- 모든 조건부 액세스 정책을 하나의 중앙 위치에서 관리할 수 있습니다.
- Azure 클래식 포털의 사용이 중지됩니다.

이 문서에서는 기존 조건부 액세스 정책을 새 프레임 워크로 마이그레이션하기 위해 알아야 할 사항을 설명 합니다.

## <a name="classic-policies"></a>클래식 정책

[Azure Portal](https://portal.azure.com)에서 조건부 액세스 정책은 **Azure Active Directory** > **보안** > **조건부 액세스**에서 찾을 수 있습니다. 조직에서이 페이지를 사용 하 여 오래 된 조건부 액세스 정책을 만들지 않았을 수도 있습니다. 이러한 정책은 *클래식 정책*이라고 합니다. 클래식 정책은 다음에서 만든 조건부 액세스 정책입니다.

- Azure 클래식 포털
- Intune 클래식 포털
- Intune 앱 보호 포털

**조건부 액세스** 페이지의 **관리** 섹션에서 [**클래식 정책**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) 을 클릭 하 여 클래식 정책에 액세스할 수 있습니다. 

![클래식 정책 보기를 표시 하는 Azure AD의 조건부 액세스](./media/policy-migration/71.png)

**클래식 정책** 보기는 다음과 같은 옵션을 제공합니다.

- 클래식 정책을 필터링합니다.
- 클래식 정책을 사용하지 않도록 설정합니다.
- 클래식 정책의 설정을 검토 하 고 사용 하지 않도록 설정 합니다.

   ![기존 정책 구성을 포함 한 클래식 정책 세부 정보](./media/policy-migration/74.png)

> [!WARNING]
> 비활성화 한 후에는 클래식 정책을 다시 사용 하도록 설정할 수 없습니다.

클래식 정책의 자세히 보기를 사용 하면 설정을 문서화 하 고, 포함 된 그룹 또는 제외 된 그룹을 수정 하 고, 정책을 사용 하지 않도록 설정할 수 있습니다.

![정책 세부 정보-포함 하거나 제외할 그룹](./media/policy-migration/75.png)

선택한 그룹을 변경 하거나 특정 그룹을 제외 하 여 포함 된 모든 사용자 및 그룹에 대 한 정책을 사용 하지 않도록 설정 하기 전에 일부 테스트 사용자에 대해 사용 하지 않도록 설정 된 클래식 정책의 효과를 테스트할 수 있습니다.
 
## <a name="migration-considerations"></a>마이그레이션 고려 사항

이 문서에서는 Azure AD 조건부 액세스 정책을 *새 정책이*라고도 합니다.
클래식 정책도 사용하지 않도록 설정하거나 제거할 때까지 새 정책과 함께 계속 작동합니다. 

정책 통합 컨텍스트에서 중요한 사항은 다음과 같습니다.

- 클래식 정책의 경우 특정 클라우드 앱과 연결되어 있지만 새 정책에서는 필요한 만큼 많은 클라우드 앱을 선택할 수 있습니다.
- 클라우드 앱에 대해 클래식 정책 및 새 정책을 제어하는 경우 모든 제어(*AND*)가 이행되어야 합니다. 
- 새 정책에서는 다음을 수행할 수 있습니다.
   - 시나리오에 필요한 경우 여러 조건을 결합합니다. 
   - 여러 권한 부여 요구 사항을 액세스 제어로 선택하고 논리 *OR*(선택한 제어 중 하나가 필요함) 또는 논리 *AND*(선택한 모든 제어가 필요함)로 결합합니다.

### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

**Exchange Active Sync**가 클라이언트 앱 조건으로 포함된 **Office 365 Exchange Online** 클래식 정책을 마이그레이션하는 경우 하나의 새 정책으로 통합하지 못할 수 있습니다. 

예를 들어 모든 클라이언트 앱 유형을 지원하려는 경우에 해당합니다. **Exchange Active Sync**가 클라이언트 앱 조건으로 있는 새 정책에서는 다른 클라이언트 앱을 선택할 수 없습니다.

![클라이언트 앱을 선택 하는 조건부 액세스](./media/policy-migration/64.png)

클래식 정책에 여러 조건이 포함되어 있는 경우에도 하나의 새 정책으로 통합할 수 없습니다. **Exchange Active Sync**가 클라이언트 앱 조건으로 구성되어 있는 새 정책은 다른 조건을 지원하지 않습니다.   

![Exchange ActiveSync는 선택한 조건을 지원 하지 않습니다.](./media/policy-migration/08.png)

**Exchange Active Sync**가 클라이언트 앱 조건으로 구성된 새 정책이 있는 경우 다른 모든 조건이 구성되지 않도록 해야 합니다. 

![조건부 액세스 조건](./media/policy-migration/16.png)
 
클라이언트 앱 조건으로 **Exchange Active Sync** 를 포함 하는 Office 365 Exchange Online에 대 한 앱 기반 클래식 정책은 **지원** 되거나 **지원 되지 않는** 장치 플랫폼을 허용 합니다. 관련된 새 정책에서는 개별 디바이스 플랫폼을 구성할 수 없으며 [지원되는 디바이스 플랫폼](concept-conditional-access-conditions.md#device-platforms)으로만 지원을 제한할 수 있습니다. 

![조건부 액세스에서 Exchange ActiveSync를 선택 합니다.](./media/policy-migration/65.png)

다음과 같은 경우에는 **Exchange Active Sync**가 클라이언트 앱 조건으로 포함된 여러 클래식 정책을 통합할 수 있습니다.

- **Exchange Active Sync**만 조건으로 포함된 경우 
- 여러 액세스 권한 부여 조건이 구성되어 있는 경우

한 가지 일반적인 시나리오는 다음과 같은 통합입니다.

- Azure 클래식 포털에서 디바이스 기반 클래식 정책 통합 
- Intune 앱 보호 포털에서 앱 기반 클래식 정책 통합 
 
이 경우 두 요구 사항이 모두 선택된 하나의 새 정책으로 클래식 정책을 통합할 수 있습니다.

![조건부 액세스 권한 부여 컨트롤](./media/policy-migration/62.png)

### <a name="device-platforms"></a>디바이스 플랫폼

앱 기반 컨트롤을 사용 하는 클래식 정책은 장치 플랫폼 조건으로 iOS 및 Android를 사용 하 여 미리 구성 됩니다. 

새 정책에서는 개별적으로 지원할 [디바이스 플랫폼](concept-conditional-access-conditions.md#device-platforms)을 선택해야 합니다.

![조건부 액세스 장치 플랫폼 선택](./media/policy-migration/41.png)

## <a name="next-steps"></a>다음 단계

- [조건부 액세스에 대 한 보고서 전용 모드를 사용 하 여 새로운 정책 결정의 영향을 확인 합니다.](concept-conditional-access-report-only.md)
- 조건부 액세스 정책을 구성 하는 방법을 알아보려면 [조건부 액세스 공용 정책](concept-conditional-access-policy-common.md)을 참조 하세요.
- 사용자 환경에 대 한 조건부 액세스 정책을 구성할 준비가 되 면 [방법: Azure Active Directory에서 조건부 액세스 배포 계획](plan-conditional-access.md)문서를 참조 하세요. 
