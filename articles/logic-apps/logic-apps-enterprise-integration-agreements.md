---
title: 거래 업체 규약-Azure Logic Apps를 만들고 설정 합니다.
description: Azure Logic Apps 및 엔터프라이즈 통합 팩을 사용 하 여 거래 파트너 간의 규약을 만들고 설정 합니다.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 447ffb8e-3e91-4403-872b-2f496495899d
ms.date: 04/05/2019
ms.openlocfilehash: 26d653b873e959f0804e0456ed87ee68c39413e5
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761296"
---
# <a name="create-and-manage-trading-partner-agreements-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps 및 엔터프라이즈 통합 팩을 사용 하 여 거래 업체 규약을 만들고

A [거래](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*계약* 조직 및 비즈니스가 교환할 때 사용할 특정 산업 표준 프로토콜을 정의 하 여 서로 원활 하 게 통신할 수 있습니다. 기업 간 B2B 메시지입니다. 계약 예를 들어 일반적인 혜택을 제공합니다.

* 조직 정보를 교환 하는 잘 알려진 형식을 사용 하 여 사용 하도록 설정 합니다.
* B2B 트랜잭션을 수행할 경우 효율성을 개선 합니다.
* 생성, 관리 및 엔터프라이즈 통합 솔루션을 구축 하기 위한 사용 하기 쉽습니다.

이 문서에서는 AS2, EDIFACT 또는 X12를 만드는 방법을 보여 줍니다.를 사용 하 여 엔터프라이즈 B2B 시나리오에 대 한 통합 솔루션을 빌드할 때 사용할 수 있는 계약을 [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md) 및 [Azure Logic Apps](../logic-apps/logic-apps-overview.md). 규약을 만든 후 사용할 수 있습니다 다음 AS2, EDIFACT 또는 X12 B2B 메시지를 교환 하는 것에 대 한 커넥터.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 규약 및 다른 B2B 아티팩트를 저장 합니다. 이 통합 계정을 통해 Azure 구독과 연결 되어야 합니다.

* 두 개 이상의 [거래](../logic-apps/logic-apps-enterprise-integration-partners.md) 통합 계정에 이미 만든입니다. 규약에는 호스트 파트너와 게스트 파트너가 필요합니다. 두 파트너는 만들려는, 예: AS2, x12 또는 EDIFACT 규약으로 동일한 "비즈니스 id" 한정자를 사용 해야 합니다.

* 선택 사항: 규약 및 논리 앱 워크플로 시작 하는 트리거를 사용 하려는 논리 앱. 통합 계정 및 B2B 아티팩트를 만드는 것, 논리 앱을 필요는 없습니다. 그러나 논리 앱에 통합 계정에서 B2B 아티팩트를 사용할 수, 통합 계정을 논리 앱에 연결 해야 합니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

## <a name="create-agreements"></a>규약 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
Azure 주 메뉴에서 **모든 서비스**를 선택합니다. 검색 상자에서 필터로 "통합"을 입력 합니다. 결과에서이 리소스를 선택 합니다. **통합 계정**

   ![통합 계정 찾기](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. 아래 **통합 계정**, 규약을 만들려는 통합 계정을 선택 합니다.

   ![규약을 만들 통합 계정 선택](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. 오른쪽 창에서 아래 **구성 요소**를 선택 합니다 **계약** 타일입니다.

   !["규약"를 선택 합니다.](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. **규약**에서 **추가**를 선택합니다. 에 **추가** 창 예를 들어 계약에 대 한 정보를 제공 합니다.

   !["추가" 선택](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | 자산 | 필수 | Value | 설명 |
   |----------|----------|-------|-------------|
   | **Name** | 예 | <*agreement-name*> | 규약 이름 |
   | **계약 유형** | 예 | **AS2**하십시오 **X12**, 또는 **EDIFACT** | 규약에 대 한 프로토콜 형식입니다. 프로그램 계약 파일을 만들 때 해당 파일의 콘텐츠는 규약 유형이 일치 해야 합니다. | |  
   | **호스트 파트너** | 예 | <*host-partner-name*> | 호스트 파트너 규약을 지정 하는 조직을 나타냅니다. |
   | **호스트 Id** | 예 | <*host-partner-identifier*> | 호스트 파트너의 식별자 |
   | **게스트 파트너** | 예 | <*guest-partner-name*> | 게스트 파트너는 호스트 파트너와 비즈니스를 수행하는 조직을 나타냅니다. |
   | **게스트 Id** | 예 | <*guest-partner-identifier*> | 게스트 파트너의 식별자 |
   | **수신 설정** | 다름 | 다름 | 이러한 속성에는 규약을 통해 받는 모든 들어오는 메시지를 처리 하는 방법을 지정 합니다. 자세한 내용은 각 계약 형식을 참조 하십시오. <p>- [AS2 메시지 설정](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 메시지 설정](logic-apps-enterprise-integration-edifact.md) <br>- [X12 메시지 설정](logic-apps-enterprise-integration-x12.md) |
   | **송신 설정** | 다름 | 다름 | 이러한 속성에는 규약에서 보낸 모든 나가는 메시지를 처리 하는 방법을 지정 합니다. 자세한 내용은 각 계약 형식을 참조 하십시오. <p>- [AS2 메시지 설정](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 메시지 설정](logic-apps-enterprise-integration-edifact.md) <br>- [X12 메시지 설정](logic-apps-enterprise-integration-x12.md) |
   |||||

1. 완료 되 면에서 규약을 만드는 합니다 **추가** 페이지에서 **확인**, 통합 계정으로 돌아갑니다.

   합니다 **계약** 이제 목록에 새 계약을 표시 합니다.

## <a name="edit-agreements"></a>계약 편집

1. 에 [Azure portal](https://portal.azure.com), 기본 Azure 메뉴에서 선택 **모든 서비스**합니다.

1. 검색 상자에서 필터로 "통합"을 입력 합니다. 결과에서이 리소스를 선택 합니다. **통합 계정**

1. 아래 **통합 계정**, 편집할 규약이 있는 통합 계정을 선택 합니다.

1. 오른쪽 창에서 아래 **구성 요소**를 선택 합니다 **계약** 타일입니다.

1. 아래 **계약**에 규약을 선택 하 고 선택 **편집**합니다.

1. 확인 하 고 변경 내용을 저장 합니다.

## <a name="delete-agreements"></a>계약 삭제

1. 에 [Azure portal](https://portal.azure.com), 기본 Azure 메뉴에서 선택 **모든 서비스**합니다.

1. 검색 상자에서 필터로 "통합"을 입력 합니다. 결과에서이 리소스를 선택 합니다. **통합 계정**

1. 아래 **통합 계정**를 삭제 하려는 규약을 포함 하는 통합 계정을 선택 합니다.

1. 오른쪽 창에서 아래 **구성 요소**를 선택 합니다 **계약** 타일입니다.

1. 아래 **계약**에 규약을 선택 하 고 선택 **삭제**합니다.

1. 선택한 규약을 삭제할지 확인합니다.

## <a name="next-steps"></a>다음 단계

* [AS2 메시지 교환](logic-apps-enterprise-integration-as2.md)
* [EDIFACT 메시지 교환](logic-apps-enterprise-integration-edifact.md)
* [X12 메시지 교환](logic-apps-enterprise-integration-x12.md)
