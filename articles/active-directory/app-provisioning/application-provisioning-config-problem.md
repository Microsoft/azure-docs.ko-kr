---
title: Azure AD 갤러리 앱에 사용자 프로 비전을 구성 하는 문제
description: Azure AD 애플리케이션 갤러리에 이미 나열된 애플리케이션에 대한 사용자 프로비전을 구성할 때 발생하는 일반적인 문제를 해결하는 방법
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: d12195b3695d092b421ce6363a2f9b426f52a1e4
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77067150"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Azure AD 갤러리 애플리케이션에 대해 사용자 프로비전 구성 문제

앱에 대해 [자동 사용자 프로비전](user-provisioning.md)을 구성하려면(지원되는 경우) 자동 프로비전에 대해 애플리케이션을 준비하는 특정 지침을 따라야 합니다. 그런 다음, Azure Portal에서 프로비전 서비스를 구성하여 사용자 계정을 애플리케이션에 동기화할 수 있습니다.

항상 애플리케이션의 프로비전 설정에 대한 설정 자습서를 찾는 것부터 시작해야 합니다. 그런 다음 해당 단계에 따라 앱과 Azure AD를 구성하여 프로비전 연결을 만듭니다. 앱 자습서 목록은 [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)에서 찾을 수 있습니다.

## <a name="how-to-see-if-provisioning-is-working"></a>프로비전이 작동하는지 확인하는 방법 

서비스가 구성되면 서비스 작업에 대한 대부분의 정보는 다음 두 곳에서 확인할 수 있습니다.

-   **프로 비전 로그 (미리 보기)** – 프로 비전 [로그](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 는 프로 비전 범위에 있는 할당 된 사용자에 대해 Azure AD 쿼리를 비롯 하 여 프로 비전 서비스에서 수행 하는 모든 작업을 기록 합니다. 시스템 간 사용자 개체를 비교하여 해당 사용자의 존재에 대해 대상 앱을 쿼리합니다. 그런 다음 비교를 기반으로 대상 시스템에서 사용자 계정을 추가, 업데이트 또는 비활성화합니다. **작업** 섹션에서 **Azure Active Directory** &gt; **Enterprise Apps** &gt; **프로 비전 로그 (미리 보기)** 를 선택 하 여 Azure Portal에서 프로 비전 로그에 액세스할 수 있습니다.

-   **현재 상태 –** 지정 된 앱에 대한 마지막 프로 비전 실행의 요약은 서비스 설정 아래의 화면 아래쪽에 있는 **Azure Active Directory &gt; Enterprise Apps &gt; \[응용 프로그램 이름\] &gt;프로 비전** 섹션에서 볼 수 있습니다. 현재 상태 섹션에서는 프로 비전 주기가 사용자 계정 프로 비전을 시작 했는지 여부를 표시 합니다. 주기의 진행 상태를 보고, 프로 비전 된 사용자 및 그룹의 수를 확인 하 고, 생성 된 역할 수를 확인할 수 있습니다. 오류가 있는 경우 세부 정보는 [프로 비전 로그 (.. /reports-monitoring/concept-provisioning-logs.md? context = azure/active directory/앱/컨텍스트/앱 관리-컨텍스트).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>고려해야 할 프로비전 관련 일반적인 문제 영역

다음은 시작 위치를 파악한 경우 검색할 수 있는 일반적인 문제 영역 목록입니다.

* [프로비전 서비스가 시작하지 않는 것 같음](#provisioning-service-does-not-appear-to-start)
* 앱 자격 증명이 작동하지 않아 구성을 저장할 수 없음
* [사용자가 할당 된 경우에도 프로 비전 로그를 통해 사용자가 "생략" 되 고 프로 비전 되지 않았다고 가정 합니다.](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>프로비전 서비스가 시작하지 않는 것 같음

**프로 비전 상태** 를 **Azure Active Directory &gt; Enterprise Apps &gt; \[응용 프로그램 이름\] &gt;프로 비전** **섹션에 설정** 합니다. 그러나 이후 다시 로드 후 기타 상태 세부 정보가 표시되지 않습니다. 서비스가 실행 중이지만 초기 주기를 아직 완료 하지 않았을 가능성이 높습니다. 위에서 설명한 **프로 비전 로그** 를 확인 하 여 서비스가 수행 하는 작업을 확인 하 고 오류가 있는지 확인 합니다.

>[!NOTE]
>초기 주기는 Azure AD 디렉터리의 크기와 프로 비전 범위에 있는 사용자 수에 따라 20 분에서 몇 시간까지 걸릴 수 있습니다. 프로 비전 서비스는 초기 주기 후 두 시스템의 상태를 나타내는 워터 마크를 저장 하므로 초기 주기가 끝난 후에도 이후 동기화는 이후 동기화의 성능이 향상 됩니다.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>앱 자격 증명이 작동하지 않아 구성을 저장할 수 없음

프로비전을 작동하도록 하려면 Azure AD에서 해당 앱에서 제공한 사용자 관리 API에 연결할 수 있도록 유효한 자격 증명이 필요합니다. 이러한 자격 증명이 작동하지 않거나 자격 증명이 무엇인지 모르는 경우 이전에 설명한 이 앱 설정에 대한 자습서를 검토하세요.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>사용자가 할당 된 경우에도 사용자를 건너뛰고 프로 비전 되지 않은 경우 로그를 프로 비전 합니다.

프로 비전 로그에 사용자가 "건너뜀"으로 표시 되 면 로그 메시지의 확장 된 세부 정보를 읽고 이유를 확인 하는 것이 매우 중요 합니다. 다음은 일반적인 원인과 해결 방법입니다.

- **특성 값에 따라 사용자를 필터링 하**는 **범위 지정 필터가 구성** 되었습니다. 자세한 내용은 [범위 지정 필터를 사용한 특성 기반 응용 프로그램 프로 비전](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)을 참조 하세요.

- **사용자가 “실질적으로 권한을 부여받지 않았습니다.”** 이 특정 오류 메시지가 표시되는 경우 Azure AD에 저장된 사용자 할당 레코드에 문제가 있기 때문입니다. 이 문제를 해결하려면 앱에서 사용자(또는 그룹)의 할당을 취소하고 다시 할당하세요. 자세한 내용은 [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)을 참조 하세요.

- **사용자에게 필요한 특성이 누락되었거나 입력되지 않았습니다.** 프로비전을 설정할 때 고려해야 할 중요한 사항은 Azure AD에서 애플리케이션으로 이동하는 사용자(또는 그룹)를 정의하는 특성 매핑 및 워크플로를 검토하고 구성하는 것입니다. 여기에는 두 시스템 간 사용자/그룹을 고유하게 식별하고 일치하는 데 사용되는 “일치하는 속성” 설정이 포함됩니다. 이 중요 한 프로세스에 대한 자세한 내용은 [사용자 프로 비전 특성 매핑 사용자 지정](../app-provisioning/customize-application-attributes.md)을 참조 하세요.

  * **그룹에 대한 특성 매핑:** 일부 애플리케이션에 대해 지원되는 경우, 구성원 외에 그룹 이름 및 그룹 세부 정보 프로비전. **프로 비전** 탭에 표시 된 그룹 개체에 대한 **매핑을** 사용 하거나 사용 하지 않도록 설정 하 여이 기능을 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 프로 비전 그룹을 사용 하는 경우 특성 매핑을 검토 하 여 "일치 하는 ID"에 적절 한 필드가 사용 되는지 확인 해야 합니다. 이는 표시 이름이나 메일 별칭일 수 있습니다. Azure AD에 일치하는 속성이 비어 있거나 그룹에 대해 입력되어 있지 않은 경우 그룹 및 구성원이 프로비전되지 않습니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](user-provisioning.md)
