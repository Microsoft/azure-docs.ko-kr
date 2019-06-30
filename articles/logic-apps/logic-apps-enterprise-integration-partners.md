---
title: B2B 통합-Azure Logic Apps에 대 한 거래 파트너를 추가 합니다.
description: Azure Logic Apps를 사용 하 여 통합 계정에서 거래 업체 만들기
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 681f16132c1de2ec5f3b27f80633d32879b0746c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330210"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Azure Logic apps 통합 계정에 거래 파트너 추가

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용 하 여 자동화 된 비즈니스-B2B 통합 워크플로 만들 수 있습니다는 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) logic apps를 사용 하 여 합니다. 조직 및 기타을 나타내기 위해 만들고 통합 계정에 아티팩트를 거래 파트너를 추가 합니다. 파트너는 B2B 트랜잭션에 참여 하 고 서로 메시지를 교환 하는 엔터티입니다.

이러한 파트너를 만들기 전에 논의 하 고 식별 하 고는 다른 보내는 메시지의 유효성을 검사 하는 방법에 대 한 파트너와 정보를 공유 하는지 확인 합니다. 이러한 세부 정보에 동의 하면 통합 계정에서 파트너를 만들려면 준비가 된 것입니다.

## <a name="partner-roles-in-integration-accounts"></a>통합 계정에서 파트너 역할

파트너와 교환 되는 메시지에 대 한 세부 정보를 정의 하려면 만들고 추가 [계약](../logic-apps/logic-apps-enterprise-integration-agreements.md) 로 통합 계정 아티팩트입니다. 계약에는 통합 계정에 두 개 이상의 파트너가 필요합니다. 조직의 사용자는 항상을 *호스트 파트너* 규약에서 합니다. 사용자 조직과 메시지를 교환 하는 조직이 합니다 *게스트 파트너*합니다. 게스트 파트너는 다른 회사 또는 조직의 부서도 수 있습니다. 이러한 파트너를 추가한 후에 규약을 만들 수 있습니다.

규약에는 호스트 파트너의 관점에서 들어오고 나가는 메시지를 처리 하는 것에 대 한 세부 정보를 지정 합니다. 들어오는 메시지를 **수신 설정** 호스트 파트너가 규약의 게스트 파트너 로부터 메시지를 수신 하는 방법을 지정 합니다. 보내는 메시지에 대해 합니다 **송신 설정** 호스트 파트너에서 게스트 파트너로 메시지를 보내는 방법 지정 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 파트너, 규약 및 다른 B2B 아티팩트를 저장 합니다. 이 통합 계정을 통해 Azure 구독과 연결 되어야 합니다.

## <a name="create-partner"></a>파트너 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure 주 메뉴에서 **모든 서비스**를 선택합니다. 검색 상자에 "통합"을 입력 하 고 선택 **통합 계정**합니다.

   !["통합 계정" 선택](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. **통합 계정**에서 파트너를 추가하려는 통합 계정을 선택합니다.

   ![통합 계정 선택](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. **파트너** 타일을 선택합니다.

   ![“파트너” 타일 선택](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. **파트너**에서 **추가**를 선택합니다. 아래 **파트너 추가**, 아래 표에 설명 된 대로 파트너의 세부 정보를 제공 합니다.

   !["추가"를 선택 하 고 파트너 세부 정보를 제공 합니다.](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | 자산 | 필수 | 설명 |
   |----------|----------|-------------|
   | **Name** | 예 | 파트너의 이름 |
   | **한정자** | 예 | 예를 들어 조직에 고유한 비즈니스 id를 제공 하는 인증 본문 **D-U-N-S (Dun & Bradstreet)** 합니다. <p>상호 정의 된 비즈니스 id에 대 한 파트너를 선택할 수 있습니다. 이러한 시나리오에 대 한 선택 **상호 정의 됨** edifact 또는 **(X12) 상호 정의** X12에 대 한 합니다. <p>만 RosettaNet 선택 **DUNS**, 표준 인 합니다. |
   | **값** | 예 | 논리 앱에서 수신 하는 문서를 식별 하는 값입니다. <p>이 값 for RosettaNet, DUNS 번호에 해당 하는 9 자리 숫자 여야 합니다. |
   ||||

   > [!NOTE]
   > RosettaNet을 사용 하는 파트너에 대 한 첫 번째 이러한 파트너를 만들어 추가 정보를 지정할 수 있습니다 차례로 [나중에 편집 하](#edit-partner)합니다.

1. 작업을 완료하면 **확인**을 선택합니다.

   새 파트너는 이제에 표시 합니다 **파트너** 목록입니다. 또한 합니다 **파트너** 타일 파트너의 현재 수를 업데이트 합니다.

   ![새 파트너](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>파트너 편집

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 찾고 선택합니다.
**파트너** 타일을 선택합니다.

   ![“파트너” 타일 선택](./media/logic-apps-enterprise-integration-partners/edit.png)

1. 아래 **파트너**을 편집 하 고 선택 하려는 파트너를 선택 **편집**합니다. 아래 **편집**를 변경 합니다.

   ![변경 후 저장](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   For RosettaNet, 아래 **RosettaNet 파트너 속성**,이 추가 정보를 지정할 수 있습니다.

   | 자산 | 필수 | 설명 |
   |----------|----------|-------------|
   | **파트너 분류** | 아닙니다. | 파트너의 한 조직 유형 |
   | **공급망 코드** | 아닙니다. | 예를 들어, "Information Technology" 또는 "전자 부품" 파트너의 공급 체인 코드 사용 |
   | **연락처 이름** | 아닙니다. | 파트너의 연락처 이름 |
   | **Email** | 아닙니다. | 파트너의 전자 메일 주소 |
   | **Fax** | 아닙니다. | 파트너의 팩스 번호 |
   | **전화** | 아닙니다. | 파트너의 전화 번호 |
   ||||

1. 완료 되 면 선택 **확인** 변경 내용을 저장 합니다.

## <a name="delete-partner"></a>파트너 삭제

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 찾고 선택합니다. **파트너** 타일을 선택합니다.

   ![“파트너” 타일 선택](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. **파트너**에서 삭제할 파트너를 선택합니다. **삭제**를 선택합니다.

   ![파트너 삭제](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>다음 단계

* 자세한 내용은 [계약](../logic-apps/logic-apps-enterprise-integration-agreements.md)