---
title: Azure Active Directory 액세스 검토 배포 계획
description: 성공적인 액세스 검토 배포에 대 한 계획 가이드
services: active-directory
documentationCenter: ''
author: BarbaraSelden
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 08/14/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7eb39f1053abeb201c413db7c6bbd3e9f261bd95
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011347"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>Azure Active Directory 액세스 검토 배포 계획

[AZURE AD (Azure Active Directory) 액세스 검토](access-reviews-overview.md) 는 조직의 [리소스 액세스 수명 주기](identity-governance-overview.md)를 관리 하 여 네트워크를 보다 안전 하 게 유지 하는 데 도움이 됩니다. 액세스 검토를 통해 다음을 수행할 수 있습니다.

* 정기적으로 검토를 예약 하거나 임시 검토를 수행 하 여 응용 프로그램 및 그룹과 같은 특정 리소스에 대 한 액세스 권한이 있는 사용자를 확인 합니다.

* 정보, 규정 준수 또는 정책 이유에 대 한 검토 추적

* 지속적인 액세스에 대 한 요구 사항을 자체 증명할 수 있는 특정 관리자, 비즈니스 소유자 또는 최종 사용자에 게 검토를 위임 합니다.

* 정보를 사용 하 여 사용자가 계속 액세스 해야 하는지 여부를 효율적으로 결정

* 리소스에 대 한 사용자 액세스를 제거 하는 등의 검토 결과 자동화

  ![액세스 검토 계획](./media/deploy-access-review/1-planning-review.png)

액세스 검토는 [Azure AD Identity Governance](identity-governance-overview.md) 기능입니다. 다른 기능은 [권한 관리](entitlement-management-overview.md), [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 및 [사용 약관](../conditional-access/terms-of-use.md)입니다. 이를 통해 조직은 다음 네 가지 질문을 해결할 수 있습니다.

* 액세스할 수 있는 리소스 및 사용자는 무엇인가요?

* 해당 사용자가 해당 액세스를 사용하여 수행하는 작업은 무엇인가요?

* 액세스 관리를 위한 효과적인 조직 컨트롤이 있나요?

* 감사자는 컨트롤이 작동하는지 확인할 수 있나요?

조직의 사용자에 대 한 원하는 거 버 넌 스 전략을 달성할 수 있도록 액세스 검토 배포 계획을 세우는 것이 중요 합니다.

### <a name="key-benefits"></a>주요 이점

액세스 검토를 사용 하는 경우의 주요 이점은 다음과 같습니다.

* **공동 작업 제어**. 액세스 검토를 통해 조직에서는 사용자가 필요로 하는 모든 리소스에 대 한 액세스를 관리할 수 있습니다. 사용자가 공유 하 고 공동 작업 하는 경우 조직은 정보를 인증 된 사용자로만 지정할 수 있습니다.

* **위험을 관리**합니다. 액세스 검토를 통해 조직에서는 데이터와 응용 프로그램에 대 한 액세스를 검토 하 여 데이터 누출 및 데이터 분산의 위험을 낮출 수 있습니다. 여기에는 회사 리소스에 대 한 외부 파트너의 액세스를 정기적으로 검토 하는 기능이 포함 됩니다. 

* **주소 규정 준수 및 거 버 넌 스**. 액세스 검토를 통해 그룹, 앱 및 사이트에 대 한 액세스 수명 주기를 제어 하 고 다시 인증할 수 있습니다. 조직과 관련 한 규정 준수 또는 위험에 민감한 응용 프로그램에 대 한 추적 검토를 제어할 수 있습니다. 

* **비용 절감**. 액세스 검토는 클라우드에서 빌드되고, 기본적으로 그룹, 응용 프로그램 및 액세스 패키지와 같은 클라우드 리소스와 함께 작동 합니다. 액세스 검토를 사용 하면 사용자 고유의 도구를 빌드하거나 온-프레미스 도구 집합을 업그레이드 하는 것 보다 비용이 저렴 합니다.

### <a name="training-resources"></a>학습 리소스

다음 비디오는 액세스 검토에 대해 자세히 알아볼 때 유용할 수 있습니다.

* [Azure AD에서 액세스 검토는 무엇 인가요?](https://youtu.be/kDRjQQ22Wkk)

* [Azure AD에서 액세스 검토를 만드는 방법](https://youtu.be/6KB3TZ8Wi40)

* [Azure AD에서 액세스 검토를 사용 하도록 설정 하는 방법](https://youtu.be/X1SL2uubx9M)

* [내 액세스를 사용 하 여 액세스를 검토 하는 방법](https://youtu.be/tIKdQhdHLXU)

### <a name="licenses"></a>라이선스

전역 관리자 또는 사용자 관리자 외에도 액세스 검토를 만들거나 수행할 수 있는 유효한 P2 (Azure AD Premium) 라이선스가 필요 합니다. 자세한 내용은 [액세스 검토 라이선스 요구 사항](access-reviews-overview.md)을 참조 하세요.

또한 [자격 수명 주기 관리](entitlement-management-overview.md) 또는 특권 id 관리 같은 다른 Id 거 버 넌 스 기능이 필요할 수 있습니다. 이 경우 관련 라이선스가 필요할 수도 있습니다. 자세한 내용은 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)을 참조하세요.

## <a name="plan-the-access-reviews-deployment-project"></a>액세스 검토 배포 프로젝트 계획

사용자 환경에서 액세스 검토를 배포 하기 위한 전략을 결정 하려면 조직의 요구 사항을 고려해 야 합니다.

### <a name="engage-the-right-stakeholders"></a>올바른 관련자 참여

기술 프로젝트가 실패하는 이유는 일반적으로 영향, 결과 및 책임에 대한 기대 수준이 일치하지 않기 때문입니다. 이러한 문제를 방지하려면 [올바른 관련자를 참여](https://aka.ms/deploymentplans)시키고 프로젝트 역할을 명확하게 해야 합니다.

액세스 검토의 경우 조직 내에서 다음 팀의 담당자를 포함할 가능성이 높습니다.

* **It 관리** 는 it 인프라를 관리 하 고 클라우드 투자 및 SaaS (Software As a Service) 앱을 관리 합니다. 이 팀은 다음 작업을 수행 합니다.

   * Office 365 및 Azure AD를 포함 하 여 인프라 및 앱에 대 한 권한 있는 액세스를 검토 합니다.

   * 최신 액세스 목록을 유지 하기 위해 예외 목록 또는 IT 파일럿 프로젝트를 유지 관리 하는 데 사용 되는 그룹에 대 한 액세스 검토를 예약 하 고 실행 합니다.

   * 서비스 주체를 통해 리소스에 대 한 프로그래밍 (스크립팅된) 액세스를 제어 하 고 검토 해야 합니다.

* **개발 팀** 은 조직의 응용 프로그램을 작성 하 고 유지 관리 합니다. 이 팀은 다음 작업을 수행 합니다.

   * 개발 된 솔루션을 구성 하는 SaaS, PaaS 및 IaaS 리소스의 구성 요소에 액세스 하 고 관리할 수 있는 사용자를 제어 합니다.

   * 내부 응용 프로그램 개발을 위해 응용 프로그램 및 도구에 액세스할 수 있는 그룹을 관리 합니다.

   * 고객을 위해 호스트 되는 프로덕션 소프트웨어 또는 솔루션에 대 한 액세스 권한이 있는 권한 있는 id 필요

* **비즈니스 단위** 는 프로젝트 및 응용 프로그램을 관리 합니다. 이 팀은 다음 작업을 수행 합니다. 

   * 내부 및 외부 사용자의 그룹 및 응용 프로그램에 대 한 액세스를 검토 하 고 승인 하거나 거부 합니다.

   * 비즈니스 파트너와 같은 직원 및 외부 id에 대 한 지속적인 액세스를 증명 하기 위해 검토를 예약 하 고 수행 합니다.

* **회사 거 버 넌 스** 는 조직이 내부 정책을 준수 하 고 규정 준수를 준수 하는지 확인 합니다. 이 팀은 다음 작업을 수행 합니다.

   * 새 액세스 검토를 요청 하거나 예약 합니다.

   * 규정 준수에 대 한 설명서 및 기록을 포함 하 여 액세스를 검토 하기 위한 프로세스 및 절차를 평가 합니다.

   * 가장 중요 한 리소스에 대 한 이전 리뷰의 결과를 검토 합니다.

> [!NOTE]
> 수동 평가가 필요한 리뷰의 경우 적절 한 검토자를 계획 하 고 정책 및 규정 준수 요구 사항을 충족 하는 주기를 검토 해야 합니다. 검토 주기가 너무 자주 발생 하거나 검토자가 너무 적은 경우 품질이 손실 될 수 있으며, 너무 많거나 너무 많은 사용자가 액세스할 수 없습니다. 

### <a name="plan-communications"></a>통신 계획

통신은 새로운 비즈니스 프로세스의 성공에 중요 합니다. 사용자가 경험을 변경 하는 방법과 시기 및 문제가 발생 하는 경우 지원을 얻는 방법에 대해 사전에 전달 합니다. 

#### <a name="communicate-changes-in-accountability"></a>책임의 변경 내용 전달

액세스 검토는 비즈니스 소유자에 게 지속적으로 액세스를 검토 하 고이에 대 한 동작을 수행 하도록 지원 합니다. IT에서 액세스 결정을 분리 하면 보다 정확한 액세스 결정을 내리는 데 도움이 됩니다. 이는 리소스 소유자의 책임 및 책임에 대 한 문화적 변화입니다. 이 변경 사항을 사전에 전달 하 고 리소스 소유자가 교육을 받고 정보를 활용 하 여 적절 한 결정을 내릴 수 있도록 합니다.

분명히, 모든 인프라 관련 액세스 의사 결정 및 권한 있는 역할 할당에 대 한 제어를 유지 하 고 싶을 것입니다. 

#### <a name="customize-email-communication"></a>전자 메일 통신 사용자 지정

검토를 예약할 때이 검토를 수행 하는 사용자를 추천 합니다. 그러면 이러한 검토자는 할당 된 검토가 만료 되기 전에 미리 알림이 할당 된 새 리뷰에 대 한 전자 메일 알림을 받습니다.

관리자는 검토를 만료 하기 전에 반 방향으로 또는 만료 되기 전에 매일이 알림을 보내도록 선택할 수 있습니다. 

검토자에 게 보낸 전자 메일을 사용자 지정 하 여 검토에 대 한 작업을 수행 하는 사용자 지정 간단한 메시지를 포함할 수 있습니다. 추가 텍스트를 사용 하 여 다음을 수행 하는 것이 좋습니다.

* 검토자에 게 개인 메시지를 포함 하 여 규정 준수 또는 IT 부서에서 해당 메시지를 전송 하는지 파악 합니다.

* 검토의 기대와 추가 참조 또는 학습 자료에 대 한 내부 정보에 대 한 하이퍼링크 또는 참조를 포함 합니다.

* 액세스에 대 한 [자체 검토를 수행 하는 방법](review-your-access.md) 에 대 한 지침을 제공 하는 링크를 포함 합니다. 

  ![검토자 전자 메일](./media/deploy-access-review/2-plan-reviewer-email.png)

검토 시작을 선택 하는 경우 검토자는 그룹 및 응용 프로그램 액세스 검토를 위해 [Myaccess 포털로](https://myapplications.microsoft.com/) 리디렉션됩니다. 포털에서는 검토 중인 리소스에 대 한 액세스 권한이 있는 모든 사용자와 마지막 로그인 및 액세스 정보를 기반으로 하는 시스템 권장 사항에 대 한 개요를 제공 합니다.

### <a name="plan-a-pilot"></a>파일럿 계획

처음에는 작은 그룹을 사용 하 여 액세스 검토를 파일럿 하 고 중요 하지 않은 리소스를 대상으로 하는 것이 좋습니다. 파일럿을 통해 필요에 따라 프로세스와 통신을 조정 하 고 보안 및 규정 준수 요구 사항을 충족 하는 사용자 및 검토자의 기능을 높일 수 있습니다.

파일럿에서 다음을 수행 하는 것이 좋습니다.

* 결과가 자동으로 적용 되지 않는 리뷰부터 시작 하 여 영향을 제어할 수 있습니다.

* 모든 사용자에 게 Azure AD에 유효한 전자 메일 주소가 표시 되어 있는지 확인 하 고 적절 한 조치를 취할 수 있도록 전자 메일 통신을 받습니다. 

* 신속 하 게 복원 해야 하는 경우 파일럿의 일부로 제거 된 모든 액세스를 문서화 합니다.

* 감사 로그를 모니터링 하 여 모든 이벤트가 제대로 감사 되는지 확인 합니다.

자세한 내용은 [파일럿 모범 사례](../fundamentals/active-directory-deployment-plans.md)를 참조 하세요.

## <a name="introduction-to-access-reviews"></a>액세스 검토 소개

이 섹션에서는 검토를 계획 하기 전에 알고 있어야 하는 액세스 검토 개념을 소개 합니다.

### <a name="what-resource-types-can-be-reviewed"></a>검토할 수 있는 리소스 종류는 무엇 인가요?

조직의 리소스를 Azure AD와 통합 하면 (예: 사용자, 응용 프로그램 및 그룹) 관리 및 검토할 수 있습니다. 

검토할 일반적인 목표는 다음과 같습니다.

* [Single sign-on을 위해 AZURE AD와 통합 된 응용 프로그램](../manage-apps/what-is-application-management.md) (예: SaaS, lob (기간 업무)).

* 그룹 [멤버 자격](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) (azure ad에 동기화 되었거나 Microsoft 팀을 포함 하 여 azure Ad 또는 Office 365에서 만들어짐).

* 리소스 (그룹, 앱 및 사이트)를 단일 패키지로 그룹화 하 여 액세스를 관리 하는 [패키지에 액세스](/azure/active-directory/governance/entitlement-management-overview) 합니다.

* [AZURE AD 역할 및 Azure 리소스 역할](../privileged-identity-management/pim-resource-roles-assign-roles.md) 은 Privileged Identity Management에 정의 되어 있습니다.

### <a name="who-will-create-and-manage-access-reviews"></a>액세스 검토를 만들고 관리 하는 사람은 누구 인가요?

액세스 검토를 만들거나, 관리 하거나, 읽는 데 필요한 관리 역할은 검토 중인 리소스의 유형에 따라 달라 집니다. 다음 표에서는 각 리소스 유형에 필요한 역할을 나타냅니다.

| 리소스 종류| 액세스 검토 만들기 및 관리 (작성자)| 액세스 검토 결과 읽기 |
| - | - | -|
| 그룹 또는 응용 프로그램| 전역 관리자 <p>사용자 관리자| 작성자 및 보안 관리자 |
| Azure AD의 권한 있는 역할| 전역 관리자 <p>권한 있는 역할 관리자| 작성자 <p>보안 Reader<p>보안 관리자 |
| Azure의 권한 있는 역할 (리소스)| 전역 관리자<p>사용자 관리자<p>리소스 소유자| 작성자 |
| 액세스 패키지| 전역 관리자<p>액세스 패키지 작성자| 전역 관리자만 |


자세한 내용은 [Azure Active Directory의 관리자 역할 권한](../users-groups-roles/directory-assign-admin-roles.md)을 참조하세요.

### <a name="who-will-review-the-access-to-the-resource"></a>리소스에 대 한 액세스를 검토 하는 사람은 누구 인가요?

액세스 검토 작성자는 검토를 수행 하는 사용자를 만들 때 결정 합니다. 검토가 시작 된 후에는이 설정을 변경할 수 없습니다. 검토자는 세 가지 가상 사용자로 표시 됩니다.

* 리소스 소유자는 리소스의 비즈니스 소유자입니다.

* 액세스 검토 관리자가 선택 하는 대로 개별적으로 선택 된 대리인 집합입니다.

* 최종 사용자는 각 사용자에 게 지속적으로 액세스 하는 데 필요한 사항을 자체 증명 합니다.

액세스 검토를 만들 때 관리자는 하나 이상의 검토자를 선택할 수 있습니다. 모든 검토자는 사용자를 선택 하 여 리소스에 계속 액세스 하거나 제거 하는 검토를 시작 하 고 수행할 수 있습니다. 

### <a name="components-of-an-access-review"></a>액세스 검토의 구성 요소

액세스 검토를 구현 하기 전에 조직과 관련 된 검토 유형을 계획 해야 합니다. 이렇게 하려면 검토할 항목과 해당 검토에 따라 수행할 작업에 대 한 비즈니스 의사 결정을 해야 합니다.

액세스 검토 정책을 만들려면 다음 정보가 필요 합니다.

* 검토할 리소스는 무엇 인가요?

* 액세스를 검토 합니다.

* 검토가 얼마나 자주 발생 하나요?

* 검토를 수행 하는 사람은 누구 인가요?

   * 검토에 대 한 통지는 어떻게 되나요?

   * 검토를 위해 적용 되는 타임 라인은 무엇 인가요?

* 검토에 따라 적용 해야 하는 자동 작업은 무엇 인가요?

   * 검토자가 시간 내에 응답 하지 않으면 어떻게 되나요?

* 검토에 따라 어떤 수동 작업을 수행 해야 합니까?

* 수행 된 작업에 따라 전송 되는 통신은 무엇 인가요?


**액세스 검토 계획 예제**

| 구성 요소| 값 |
| - | - |
| **검토할 리소스**| Microsoft Dynamics에 대 한 액세스 |
| **검토 빈도**| 매월 |
| **검토를 수행 하는 사람**| Dynamics 비즈니스 그룹 프로그램 관리자 |
| **알림**| Pms 별칭을 검토 하기 전에 24 시간 동안 전자 메일로 보내기<p>검토자에 게 사용자 지정 메시지를 포함 하 여 자신의 구매 보호 |
| **타임라인**| 48 시간 (알림) |
|**자동 작업**| 보안 그룹 dynamics 액세스에서 사용자를 제거 하 여 90 일 이내에 대화형 로그인이 없는 모든 계정에서 액세스 권한을 제거 합니다. <p>*타임 라인 내에서 검토 되지 않은 경우 작업을 수행 합니다.* |
| **수동 작업**| 검토자는 원하는 경우 자동화 된 작업 전에 승인 제거를 수행할 수 있습니다. |
| **통신**| 제거를 설명 하는 전자 메일을 제거 하는 내부 (구성원) 사용자와 액세스 권한을 다시 얻는 방법을 보냅니다. |


 

### <a name="automate-actions-based-on-access-reviews"></a>액세스 검토에 따라 작업 자동화

리소스에 결과 자동 적용 옵션을 사용으로 설정 하 여 액세스 제거를 자동화 하도록 선택할 수 있습니다.

  ![액세스 검토 계획](./media/deploy-access-review/3-automate-actions-settings.png)

검토가 완료 되 고 종료 된 후 검토자가 승인 하지 않은 사용자는 자동으로 리소스에서 제거 되거나 계속 액세스를 유지 합니다. 이는 그룹 멤버 자격, 해당 응용 프로그램 할당을 제거 하거나 권한 있는 역할로의 권한을 상승 시킬 수 있는 권한을 취소 하는 것을 의미할 수 있습니다.

권장 사항 사용

권장 사항은 검토자 환경의 일부로 검토자에 게 표시 되 고 테 넌 트에 대 한 사용자의 마지막 로그인 또는 응용 프로그램에 대 한 마지막 액세스를 표시 합니다. 이 정보는 검토자에 게 올바른 액세스 결정을 내리는 데 도움이 됩니다. 권장 사항 적용을 선택 하면 액세스 검토의 권장 사항을 따릅니다. 액세스 검토가 끝나면 시스템은 검토자가 응답 하지 않은 사용자에 대해 이러한 권장 사항을 자동으로 적용 합니다.

권장 사항은 액세스 검토의 조건을 기반으로 합니다. 예를 들어 90 일 동안 대화형 로그인을 사용 하지 않고 액세스를 제거 하도록 검토를 구성 하는 경우 해당 조건에 맞는 모든 사용자를 제거 하는 것이 좋습니다. Microsoft는 계속 해 서 권장 사항을 개선 하기 위해 노력 하 고 있습니다. 

### <a name="review-guest-user-access"></a>게스트 사용자 액세스 검토

액세스 검토를 사용 하 여 외부 조직에서 공동 작업 파트너의 id를 검토 하 고 정리 합니다. 파트너 당 검토의 구성은 규정 준수 요구 사항을 충족할 수 있습니다.

외부 id는 다음 작업 중 하나를 통해 회사 리소스에 대 한 액세스 권한을 부여할 수 있습니다.

* 그룹에 추가 됨 

* 팀에 초대 

* 엔터프라이즈 응용 프로그램 또는 액세스 패키지에 할당 됨

* Azure AD 또는 Azure 구독에 권한 있는 역할이 할당 됨

[샘플 스크립트](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)를 참조 하세요. 이 스크립트는 테 넌 트에 초대 된 외부 id가 사용 되는 위치를 보여 줍니다. Azure AD에서 외부 사용자의 그룹 멤버 자격, 역할 할당 및 응용 프로그램 할당을 확인할 수 있습니다. 스크립트는 그룹을 사용 하지 않고 Sharepoint 리소스에 대 한 직접 권한 할당 등 Azure AD 외부의 할당을 표시 하지 않습니다.

그룹이 나 응용 프로그램에 대 한 액세스 검토를 만들 때 검토자가 액세스 권한이 있는 모든 사용자 또는 게스트 사용자에 게 집중 하도록 선택할 수 있습니다. 게스트 사용자만을 선택 하 여 검토자에 게 리소스에 대 한 액세스 권한이 있는 Azure AD B2B의 외부 id에 대 한 포커스가 있는 목록이 제공 됩니다.

 ![게스트 사용자 검토](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> UserType 멤버의 외부 멤버는 포함 되지 않습니다. 또한 Azure AD B2B 공동 작업 외부에서 초대 받은 사용자 (예: SharePoint를 통해 직접 공유 콘텐츠에 액세스할 수 있는 사용자)는 포함 되지 않습니다.

## <a name="plan-access-reviews-for-access-packages"></a>액세스 패키지에 대 한 액세스 검토 계획

[액세스 패키지](entitlement-management-overview.md) 는 거 버 넌 스 및 액세스 검토 전략을 크게 간소화할 수 있습니다. 액세스 패키지는 사용자가 프로젝트에서 작업을 수행 하거나 작업을 수행 하는 데 필요한 액세스 권한이 있는 모든 리소스의 번들입니다. 예를 들어 조직의 개발자가 필요로 하는 모든 응용 프로그램이 나 외부 사용자가 액세스할 수 있는 모든 응용 프로그램을 포함 하는 액세스 패키지를 만들 수 있습니다. 그런 다음 관리자 또는 위임 된 액세스 패키지 관리자는 리소스 (그룹 또는 앱)와 해당 리소스에 대해 사용자가 필요로 하는 역할을 그룹화 합니다.

[액세스 패키지를 만들](entitlement-management-access-package-create.md)때 사용자가 액세스 패키지를 요청할 수 있는 조건, 승인 프로세스의 모양 및 사용자가 액세스를 다시 요청 해야 하는 빈도를 설정 하는 액세스 정책을 하나 이상 만들 수 있습니다. 액세스 검토는 액세스 패키지 정책을 만들거나 편집 하는 동안 구성 됩니다.

수명 주기 탭을 열어 아래로 스크롤하여 검토에 액세스 합니다.

 ![정책 편집](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>그룹에 대 한 액세스 검토 계획

액세스 패키지 외에도 그룹 멤버 자격 검토는 액세스를 관리 하는 가장 효과적인 방법입니다. 리소스에 대 한 액세스는 [보안 그룹 또는 Office 365 그룹](../fundamentals/active-directory-manage-groups.md)을 통해 할당 되 고 사용자가 해당 그룹에 추가 되어 액세스 권한을 얻을 수 있도록 하는 것이 좋습니다.

단일 그룹에 해당 하는 모든 리소스에 대 한 액세스 권한을 부여할 수 있습니다. 개별 리소스 또는 응용 프로그램 및 기타 리소스를 그룹화 하는 액세스 패키지에 그룹 액세스 권한을 할당할 수 있습니다. 이 방법을 사용 하 여 각 응용 프로그램에 대 한 개별 액세스 권한이 아닌 그룹에 대 한 액세스를 검토할 수 있습니다. 

다음을 통해 그룹 멤버 자격을 검토할 수 있습니다. 

* 관리자

* 그룹 소유자

* 검토가 생성 될 때 선택한 사용자, 위임 된 검토 기능

* 증명 그룹의 멤버

### <a name="group-ownership"></a>그룹 소유권

액세스 해야 하는 사용자를 파악 하는 것이 가장 좋습니다. 그룹 소유자는 구성원 자격을 검토 하는 것이 좋습니다. 그룹의 소유권은 그룹의 유형에 따라 다릅니다.

Office 365 및 Azure AD에서 만든 그룹에는 잘 정의 된 소유자가 하나 이상 있습니다. 대부분의 경우 이러한 소유자는 액세스할 수 있는 사용자를 파악 하는 그룹에 대해 완벽 한 검토자를 만듭니다. 

예를 들어 Microsoft 팀은 Office 365 그룹을 기본 권한 부여 모델로 사용 하 여 사용자에 게 SharePoint, Exchange, OneNote 또는 기타 Office 365 서비스에 있는 리소스에 대 한 액세스 권한을 부여 합니다. 팀의 작성자는 자동으로 소유자가 되며 해당 그룹의 멤버 자격을 증명 책임이 있습니다. 

Azure AD 포털에서 수동으로 만들거나 Microsoft Graph를 통한 스크립팅을 통해 그룹을 반드시 정의 해야 하는 것은 아닙니다. 그룹의 "소유자" 섹션에서 또는 그래프를 통해 Azure AD 포털을 통해 정의 하는 것이 좋습니다.

온-프레미스 Active Directory에서 동기화 된 그룹에는 Azure AD의 소유자가 있을 수 없습니다. 이러한 항목에 대 한 액세스 검토를 만들 때 멤버 자격을 결정 하는 데 가장 적합 한 개인을 선택 해야 합니다.

> [!NOTE]
> 그룹을 만드는 방법을 정의 하는 비즈니스 정책을 정의 하 여 멤버 자격에 대 한 정기적인 검토를 위해 그룹 소유권과 책임이 명확 하 게 유지 되도록 하는 것이 좋습니다. 

### <a name="review-membership-of-exclusion-groups-in-ca-policies"></a>CA 정책에서 제외 그룹의 멤버 자격 검토 

네트워크 보안을 유지 하도록 설계 된 조건부 액세스 (CA) 정책이 모든 사용자에 게 적용 되지 않아야 하는 경우가 있습니다. 예를 들어 회사 네트워크에서 사용자만 로그인 할 수 있도록 허용 하는 CA 정책은 광범위 하 게 이동 하는 영업 팀에 적용 되지 않을 수 있습니다. 이 경우 영업 팀 구성원이 그룹에 추가 되 고 해당 그룹이 CA 정책에서 제외 됩니다. 

잘못 된 구성원이 요구 사항에서 제외 되는 경우 예외는 잠재적 위험을 나타내며 이러한 그룹 멤버 자격은 정기적으로 확인 합니다.

[AZURE AD 액세스 검토를 사용 하 여 조건부 액세스 정책에서 제외 된 사용자를 관리할](conditional-access-exclusion.md)수 있습니다.

### <a name="review-external-users-group-memberships"></a>외부 사용자의 그룹 멤버 자격 검토

수동 작업 및 연결 된 잠재적 오류를 최소화 하려면 [동적 그룹](../users-groups-roles/groups-create-rule.md) 을 사용 하 여 사용자의 특성에 따라 그룹 멤버 자격을 할당 하는 것이 좋습니다. 외부 사용자에 대해 하나 이상의 동적 그룹을 만들려고 할 수 있습니다. 내부 스폰서는 그룹의 멤버 자격에 대 한 검토자 역할을 할 수 있습니다. 

참고: 액세스 검토의 결과로 그룹에서 제거 된 외부 사용자는 테 넌 트에서 삭제 되지 않습니다. 

수동으로 제거 하거나 스크립트를 통해 테 넌 트에서 삭제할 수 있습니다.

### <a name="review-access-to-on-premises-groups"></a>온-프레미스 그룹에 대 한 액세스 검토

액세스 검토는 [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)를 사용 하 여 온-프레미스에서 동기화 하는 그룹의 그룹 구성원을 변경할 수 없습니다. 이는 인증 원본이 온-프레미스 이기 때문입니다.

여전히 액세스 검토를 사용 하 여 온-프레미스 그룹의 정기 검토를 예약 하 고 유지 관리할 수 있습니다. 그러면 검토자가 온-프레미스 그룹에서 작업을 수행 합니다. 이 전략은 모든 검토에 대 한 도구로 액세스 검토를 유지 합니다.

온-프레미스 그룹에 대 한 액세스 검토의 결과를 사용 하 고 다음을 수행 하 여 그 결과를 추가로 처리할 수 있습니다.

* 액세스 검토에서 CSV 보고서를 다운로드 하 고 수동으로 작업을 수행 합니다.

* Microsoft Graph를 사용 하 여 완료 된 액세스 검토에서 결과 및 결정 사항에 프로그래밍 방식으로 액세스 합니다.

예를 들어 Windows AD 관리 그룹에 대 한 결과에 액세스 하려면이 [PowerShell 샘플 스크립트](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)를 사용 합니다. 이 스크립트는 필요한 그래프 호출을 간략하게 설명 하 고 변경 내용을 수행 하기 위해 Windows AD-PowerShell 명령을 내보냅니다.

## <a name="plan-access-reviews-for-applications"></a>응용 프로그램에 대 한 액세스 검토 계획 

응용 프로그램에 대 한 액세스를 검토 하는 경우 응용 프로그램 내의 정보 및 데이터에 대 한 직원 및 외부 id 액세스를 검토 하 게 됩니다. 액세스 패키지 또는 그룹 대신 특정 응용 프로그램에 대 한 액세스 권한이 있는 사용자를 알고 있어야 하는 경우 응용 프로그램을 검토 하도록 선택 합니다. 

다음 시나리오에서 응용 프로그램에 대 한 검토를 계획 하는 것이 좋습니다.

* 사용자에 게는 그룹 또는 액세스 패키지 외부의 응용 프로그램에 직접 액세스할 수 있는 권한이 부여 됩니다.

* 응용 프로그램은 중요 한 정보나 중요 한 정보를 노출 합니다.

* 응용 프로그램에는 증명 해야 할 특정 규정 준수 요구 사항이 있습니다.

* 부적합 한 액세스를 의심 합니다.

응용 프로그램에 대 한 액세스 검토를 만들려면 필요한 사용자 할당을 설정 합니다. 속성을 예로 합니다. No로 설정 된 경우 외부 id를 포함 하 여 디렉터리의 모든 사용자가 응용 프로그램에 액세스할 수 있으며 응용 프로그램에 대 한 액세스를 검토할 수 없습니다. 

 ![앱 할당 계획](./media/deploy-access-review/6-plan-applications-assignment-required.png)

그런 다음 액세스 하려는 [사용자 및 그룹을 할당](../manage-apps/assign-user-or-group-access-portal.md) 해야 합니다. 

### <a name="reviewers-for-an-application"></a>응용 프로그램에 대 한 검토자

액세스 검토는 애플리케이션에 할당된 그룹 구성원이거나 사용자일 수 있습니다. Azure AD의 응용 프로그램에는 소유자가 없어도 됩니다. 따라서 응용 프로그램 소유자를 검토자로 선택 하는 옵션을 사용할 수 없습니다. 모든 액세스를 검토 하는 대신 응용 프로그램에 할당 된 게스트 사용자만 검토 하도록 검토 범위를 추가로 지정할 수 있습니다.

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>Azure AD 및 Azure 리소스 역할의 계획 검토

[PIM (Privileged Identity Management)](../privileged-identity-management/pim-configure.md) 은 기업이 Azure AD의 리소스에 대 한 권한 있는 액세스를 관리 하는 방법을 간소화 합니다. 이렇게 하면 [AZURE AD](../users-groups-roles/directory-assign-admin-roles.md) 와 [azure 리소스](../../role-based-access-control/built-in-roles.md) 모두에서 권한 있는 역할의 목록을 훨씬 더 작게 유지 하 고 디렉터리의 전반적인 보안을 향상 시킵니다.

액세스 검토를 통해 검토자는 사용자가 여전히 역할에 있어야 하는지 여부를 증명할 수 있습니다. 액세스 패키지에 대 한 액세스 검토와 마찬가지로 Azure AD 역할 및 Azure 리소스에 대 한 검토가 PIM admin 사용자 환경에 통합 되어 있습니다. 정기적으로 다음 역할 할당을 검토 하는 것이 좋습니다.

* 전역 관리자

* 사용자 관리자

* 권한 있는 인증 관리자

* 조건부 액세스 관리자

* 보안 관리자

* 모든 Office 365 및 Dynamics 서비스 관리 역할

여기에서 선택한 역할에는 영구 및 적격 역할이 포함 됩니다. 

검토자 섹션에서 모든 사용자를 검토할 한 명 이상의 사용자를 선택합니다. 또는 구성원이 자신의 액세스 권한을 검토하도록 할 수도 있습니다.

 ![정책 편집](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>액세스 검토 배포

전략을 준비 하 고 Azure AD와 통합 된 리소스에 대 한 액세스를 검토 하는 계획을 완료 한 후에는 아래 리소스를 사용 하 여 리뷰를 배포 하 고 관리 합니다.

### <a name="review-access-packages"></a>액세스 패키지 검토

관리자는 부실 액세스의 위험을 줄이기 위해 액세스 패키지에 대 한 활성 할당을 가진 사용자를 정기적으로 검토할 수 있습니다. 아래 링크의 지침을 따르세요.

| 방법 문서| 설명 |
| - | - |
| [액세스 검토 만들기](entitlement-management-access-reviews-create.md)| 액세스 패키지의 검토를 사용 하도록 설정 합니다. |
| [액세스 검토 수행](entitlement-management-access-reviews-review-access.md)| 액세스 패키지에 할당 된 다른 사용자에 대 한 액세스 검토를 수행 합니다. |
| [자동으로 할당 된 액세스 패키지를 검토 합니다.](entitlement-management-access-reviews-self-review.md)| 할당 된 액세스 패키지 자체 검토 |


> [!NOTE]
> 자신을 검토 하 고 더 이상 액세스 하지 않아도 되는 최종 사용자는 액세스 패키지에서 즉시 제거 되지 않습니다. 이는 검토가 종료 되거나 관리자가 검토를 중지 한 경우 액세스 패키지에서 제거 됩니다.

### <a name="review-groups-and-apps"></a>그룹 및 앱 검토

직원 및 게스트의 그룹 및 응용 프로그램에 대 한 액세스는 시간이 지남에 따라 변경 될 수 있습니다. 오래 된 액세스 할당과 관련 된 위험을 줄이기 위해 관리자는 그룹 구성원 또는 응용 프로그램 액세스에 대 한 액세스 검토를 만들 수 있습니다. 아래 링크의 지침을 따르세요.

| 방법 문서| 설명 |
| - | - |
| [액세스 검토 만들기](create-access-review.md)| 그룹 구성원 또는 응용 프로그램 액세스에 대 한 액세스 검토를 하나 이상 만듭니다. |
| [액세스 검토 수행](perform-access-review.md)| 응용 프로그램에 대 한 액세스 권한이 있는 그룹 구성원 또는 사용자에 대 한 액세스 검토를 수행 합니다. |
| [액세스 권한 자체 검토](review-your-access.md)| 멤버는 그룹 또는 응용 프로그램에 대 한 자신의 액세스를 검토 합니다. |
| [액세스 검토 완료](complete-access-review.md)| 액세스 검토 보기 및 결과 적용 |
| [온-프레미스 그룹에 대 한 작업 수행](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| 온-프레미스 그룹에 대 한 액세스 검토를 수행 하는 샘플 PowerShell 스크립트입니다. |


### <a name="review-azure-ad-roles"></a>Azure AD 역할 검토

부실 역할 할당과 관련 된 위험을 줄이려면 권한 있는 Azure AD 역할에 대 한 액세스를 정기적으로 검토 해야 합니다.

![azure ad 역할 검토](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

아래 링크의 지침을 따르세요.

| 방법 문서 | 설명 |
| - | - |
 [액세스 검토 만들기](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| PIM의 권한 있는 Azure AD 역할에 대 한 액세스 검토 만들기 |
| [액세스 권한 자체 검토](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 관리 역할에 할당 된 경우 역할에 대 한 액세스를 승인 하거나 거부 합니다. |
| [액세스 검토 완료](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 액세스 검토 보기 및 결과 적용 |


### <a name="review-azure-resource-roles"></a>Azure 리소스 역할 검토

부실 역할 할당과 관련 된 위험을 줄이려면 권한 있는 Azure 리소스 역할에 대 한 액세스를 정기적으로 검토 해야 합니다. 

![azure ad 역할 검토](./media/deploy-access-review/9-review-azure-roles-picker.png)

아래 링크의 지침을 따르세요.

| 방법 문서| 설명 |
| - | -|
| [액세스 검토 만들기](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| PIM의 권한 있는 Azure 리소스 역할에 대 한 액세스 검토 만들기 |
| [액세스 권한 자체 검토](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 관리 역할에 할당 된 경우 역할에 대 한 액세스를 승인 하거나 거부 합니다. |
| [액세스 검토 완료](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 액세스 검토 보기 및 결과 적용 |


## <a name="use-the-access-reviews-api"></a>액세스 검토 API 사용

다시 볼 수 있는 리소스와 상호 작용 하 고이를 관리 하려면 [GRAPH API 메서드](/graph/api/resources/accessreviews-root?view=graph-rest-beta) 및 [역할 및 응용 프로그램 권한 권한 부여 검사](/graph/api/resources/accessreviews-root?view=graph-rest-beta) 를 참조 하세요. Microsoft Graph API의 액세스 검토 방법은 응용 프로그램 및 사용자 컨텍스트에 모두 사용할 수 있습니다. 응용 프로그램 컨텍스트에서 스크립트를 실행 하는 경우 API를 실행 하는 데 사용 되는 계정 (서비스 사용자)에 게 액세스 검토 정보를 쿼리 하는 "AccessReview. 전체" 사용 권한을 부여 해야 합니다.

액세스 검토를 위해 Graph API를 사용 하 여 자동화 하는 인기 있는 액세스 검토 작업은 다음과 같습니다.

* 액세스 검토 만들기 및 시작

* 예약 된 종료 전에 액세스 검토를 수동으로 종료

* 실행 중인 모든 액세스 검토 및 해당 상태 나열

* 검토 시리즈의 기록과 각 검토에서 수행 된 결정 및 작업을 참조 하세요.

* 액세스 검토에서 결정 수집

* 검토자가 시스템에서 권장 하는 것과 다른 결정을 취한 완료 된 검토에서 결정 사항을 수집 합니다.

자동화를 위해 새 Graph API 쿼리를 만들 때 [Graph 탐색기](https://developer.microsoft.com/en-us/graph/graph-explorer)를 사용 하는 것이 좋습니다. Graph 쿼리를 스크립트 및 코드에 배치 하기 전에 빌드하고 탐색할 수 있습니다. 이를 통해 스크립트 코드를 변경 하지 않고도 원하는 결과를 정확 하 게 얻을 수 있도록 쿼리를 신속 하 게 반복할 수 있습니다.

## <a name="monitor-access-reviews"></a>액세스 검토 모니터링

액세스 검토 작업은 [AZURE AD의 감사 로그](../reports-monitoring/concept-audit-logs.md)에서 기록 되 고 사용할 수 있습니다. 범주, 활동 유형 및 날짜 범위에서 감사 데이터를 필터링 할 수 있습니다. 예제 쿼리는 다음과 같습니다.

| 범주| Policy |
| - | - |
| 활동 유형| 액세스 검토 만들기 |
| | 액세스 검토 업데이트 |
| | 액세스 검토가 종료 되었습니다. |
| | 액세스 검토 삭제 |
| | 결정 승인 |
| | 거부 결정 |
| | 결정 다시 설정 |
| | 결정 적용 |
| 날짜 범위| 7일 |


액세스 검토에 대 한 고급 쿼리 및 분석을 위해 그리고 검토의 변경 및 완료를 추적 하려면 azure AD 감사 로그를 [azure Log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) 또는 Azure 이벤트 허브로 내보내는 것이 좋습니다. Azure Log Analytics에 저장 된 경우 [강력한 분석 언어](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) 를 사용 하 고 고유한 대시보드를 빌드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

아래 관련 기술에 대해 알아보세요.

* [Azure AD 자격 관리 란?](entitlement-management-overview.md)

* [Azure AD Privileged Identity Management란?](../privileged-identity-management/pim-configure.md)

