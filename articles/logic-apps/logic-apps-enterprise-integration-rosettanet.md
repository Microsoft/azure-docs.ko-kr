---
title: B2B 통합을 위한 RosettaNet 메시지
description: Azure Logic Apps에서 엔터프라이즈 통합 팩를 사용 하 여 RosettaNet 메시지 교환
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 2cc2ac08b9624c1d1d9bee9ce91a7c91189d7f2c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792425"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Azure Logic Apps에서 B2B 엔터프라이즈 통합에 대 한 Exchange RosettaNet 메시지

[RosettaNet](https://resources.gs1us.org) 는 비즈니스 정보를 공유 하기 위한 표준 프로세스를 설정한 비영리 컨소시엄입니다. 이러한 표준은 공급망 프로세스에 일반적으로 사용 되며 반도체, 전자 제품 및 물류 산업에서 널리 사용 됩니다. RosettaNet 컨소시엄은 모든 RosettaNet 메시지 교환의 공통 비즈니스 프로세스 정의를 제공 하는 Pip (파트너 인터페이스 프로세스)를 만들고 유지 관리 합니다. RosettaNet은 XML을 기반으로 하며, 메시지 지침, 비즈니스 프로세스에 대 한 인터페이스 및 회사 간 통신을 위한 구현 프레임 워크를 정의 합니다.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)RosettaNet 커넥터를 사용 하면 RosettaNet 표준을 지 원하는 통합 솔루션을 만들 수 있습니다. 커넥터는 RNIF (RosettaNet 구현 프레임 워크) 버전 2.0.01를 기반으로 합니다. RNIF은 비즈니스 파트너가 공동으로 RosettaNet Pip를 실행할 수 있도록 하는 개방형 네트워크 응용 프로그램 프레임 워크입니다. 이 프레임 워크는 메시지 구조, 승인 필요성, MIME (다목적 Internet Mail Extensions) 인코딩 및 디지털 서명을 정의 합니다.

특히 커넥터는 다음과 같은 기능을 제공 합니다.

* RosettaNet 메시지를 인코딩하거나 받습니다.
* RosettaNet 메시지를 디코드 하거나 보냅니다.
* 응답 및 실패 알림이 생성 될 때까지 기다립니다.

이러한 기능의 경우 커넥터는 RNIF 2.0.01에 정의 된 모든 Pip을 지원 합니다. 파트너와의 통신은 동기식 이거나 비동기식 일 수 있습니다.

## <a name="rosettanet-concepts"></a>RosettaNet 개념

RosettaNet 사양에 고유한 몇 가지 개념과 용어는 다음과 같습니다. RosettaNet 기반 통합을 빌드할 때 중요 합니다.

* **주사위**

  RosettaNet 조직은 모든 RosettaNet 메시지 교환의 공통 비즈니스 프로세스 정의를 제공 하는 Pip (파트너 인터페이스 프로세스)를 만들고 유지 관리 합니다. 각 PIP 사양은 DTD (문서 종류 정의) 파일 및 메시지 지침 문서를 제공 합니다. DTD 파일은 서비스 콘텐츠 메시지 구조를 정의 합니다. 사람이 읽을 수 있는 HTML 파일인 메시지 지침 문서는 요소 수준 제약 조건을 지정 합니다. 이러한 파일은 모두 비즈니스 프로세스의 전체 정의를 제공 합니다.

   Pip는 상위 수준의 비즈니스 기능, 클러스터 및 하위 함수 또는 세그먼트로 분류 됩니다. 예를 들어 "3A4"는 구매 주문의 PIP이 고, "3"은 주문 관리 함수 이며, "3A"는 견적 & Order Entry 하위 함수입니다. 자세한 내용은 [RosettaNet 사이트](https://resources.gs1us.org)를 참조 하세요.

* **작업**

  PIP의 일부 이며, 작업 메시지는 파트너 간에 교환 되는 비즈니스 메시지입니다.

* **알립니다**

   PIP의 일부 이며, 신호 메시지는 작업 메시지에 대 한 응답으로 전송 되는 승인입니다.

* **단일 작업 및 이중 작업**

  단일 작업 PIP의 경우 유일한 응답은 승인 신호 메시지입니다. 이중 작업 PIP의 경우 개시자는 응답 메시지를 받고 단일 동작 메시지 흐름 외에 승인으로 응답 합니다.

## <a name="prerequisites"></a>전제 조건

* Azure 구독. 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 규약 및 기타 B2B 아티팩트를 저장 하는 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 입니다. 이 통합 계정은 Azure 구독과 연결 되어야 합니다.

* 통합 계정에 정의 되 고 **비즈니스 id** 에서 "DUNS" 한정자를 사용 하 여 구성 된 두 개 이상의 [파트너](../logic-apps/logic-apps-enterprise-integration-partners.md)

* 통합 계정에서 RosettaNet 메시지를 전송 하거나 수신 하는 데 필요한 PIP 프로세스 구성 프로세스 구성은 모든 PIP 구성 특성을 저장 합니다. 그런 다음 파트너와 규약을 만들 때이 구성을 참조할 수 있습니다. 통합 계정에서 PIP 프로세스 구성을 만들려면 [pip 프로세스 구성 추가](#add-pip)를 참조 하세요.

* 통합 계정에 업로드 하는 메시지를 암호화, 암호 해독 또는 서명 하는 데 사용할 수 있는 선택적 [인증서](../logic-apps/logic-apps-enterprise-integration-certificates.md) 입니다. 서명 또는 암호화를 사용 하는 경우에만 인증서가 필요 합니다.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>PIP 프로세스 구성 추가

통합 계정에 PIP 프로세스 구성을 추가 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 찾아 엽니다.

1. **개요** 창에서 **RosettaNet PIP** 타일을 선택 합니다.

   ![RosettaNet 타일 선택](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. **ROSETTANET PIP**에서 **추가**를 선택 합니다. PIP 세부 정보를 제공 합니다.

   ![RosettaNet PIP 세부 정보 추가](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | 자산 | 필수 | 설명 |
   |----------|----------|-------------|
   | **Name** | yes | PIP 이름 |
   | **PIP 코드** | yes | PIP 3 자리 코드입니다. 자세한 내용은 [RosettaNet pip](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips)를 참조 하세요. |
   | **PIP 버전** | yes | 선택한 PIP 코드에 따라 사용할 수 있는 PIP 버전 번호 |
   ||||

   이러한 PIP 속성에 대 한 자세한 내용은 [RosettaNet 웹 사이트](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)를 참조 하세요.

1. 완료 되 면 PIP 구성을 만드는 **확인**을 선택 합니다.

1. 프로세스 구성을 보거나 편집 하려면 PIP를 선택 하 고 **JSON으로 편집**을 선택 합니다.

   모든 프로세스 구성 설정은 PIP의 사양에서 제공 됩니다. Logic Apps 대부분의 설정을 이러한 속성에 가장 일반적으로 사용 되는 값의 기본값으로 채웁니다.

   ![RosettaNet PIP 구성 편집](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. 설정이 적절 한 PIP 사양의 값에 해당 하 고 비즈니스 요구 사항을 충족 하는지 확인 합니다. 필요한 경우 JSON에서 값을 업데이트 하 고 변경 내용을 저장 합니다.

## <a name="create-rosettanet-agreement"></a>RosettaNet 규약 만들기

1. [Azure Portal](https://portal.azure.com)에서 통합 계정이 아직 열려 있지 않으면 찾아서 엽니다.

1. **개요** 창에서 **규약** 타일을 선택 합니다.

   ![규약 타일 선택](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. **규약**에서 **추가**를 선택합니다. 계약 정보를 제공 합니다.

   ![계약 정보 추가](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | 자산 | 필수 | 설명 |
   |----------|----------|-------------|
   | **Name** | yes | 규약의 이름 |
   | **계약 유형** | yes | **RosettaNet**를 선택 합니다. |
   | **호스트 파트너** | yes | 규약에는 호스트와 게스트 파트너가 모두 필요합니다. 호스트 파트너는 규약을 구성하는 조직을 나타냅니다. |
   | **호스트 Id** | yes | 호스트 파트너의 식별자입니다. |
   | **게스트 파트너** | yes | 규약에는 호스트와 게스트 파트너가 모두 필요합니다. 게스트 파트너는 호스트 파트너와 함께 일하는 조직을 나타냅니다. |
   | **게스트 Id** | yes | 게스트 파트너의 식별자입니다. |
   | **수신 설정** | 다름 | 이러한 속성은 호스트 파트너가 받은 모든 메시지에 적용 됩니다. |
   | **송신 설정** | 다름 | 이러한 속성은 호스트 파트너가 보낸 모든 메시지에 적용 됩니다. |  
   | **RosettaNet PIP 참조** | yes | 규약에 대 한 PIP 참조입니다. 모든 RosettaNet 메시지에는 PIP 구성이 필요 합니다. |
   ||||

1. 게스트 파트너 로부터 들어오는 메시지를 수신 하기 위한 규약을 설정 하려면 **수신 설정**을 선택 합니다.

   ![수신 설정](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. 들어오는 메시지에 서명 또는 암호화를 사용 하도록 설정 **하려면 메시지에서**메시지 **서명** 필요 또는 **메시지 암호화** 필요를 각각 선택 합니다.

      | 자산 | 필수 | 설명 |
      |----------|----------|-------------|
      | **메시지 서명 필요** | 아닙니다. | 선택한 인증서를 사용 하 여 들어오는 메시지에 서명 합니다. |
      | **인증서** | 예, 서명이 사용 되는 경우 | 서명에 사용할 인증서 |
      | **메시지 암호화 사용** | 아닙니다. | 선택한 인증서를 사용 하 여 들어오는 메시지를 암호화 합니다. |
      | **인증서** | 예, 암호화가 사용 되는 경우 | 암호화에 사용할 인증서 |
      ||||

   1. 각 선택에서 서명 또는 암호화에 사용할 이전에 통합 계정에 추가한 각 [인증서](./logic-apps-enterprise-integration-certificates.md)를 선택 합니다.

1. 게스트 파트너에 게 메시지를 보내기 위한 규약을 설정 하려면 **송신 설정**을 선택 합니다.

   ![송신 설정](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. 보내는 메시지에 대 한 서명 또는 암호화를 사용 하도록 설정 **하려면 메시지에서** **메시지 서명 사용** 또는 **메시지 암호화 사용** 을 각각 선택 합니다. 각 선택에서 서명 또는 암호화에 사용할 이전에 통합 계정에 추가한 각 알고리즘과 [인증서](./logic-apps-enterprise-integration-certificates.md)를 선택 합니다.

      | 자산 | 필수 | 설명 |
      |----------|----------|-------------|
      | **메시지 서명 사용** | 아닙니다. | 선택한 서명 알고리즘 및 인증서를 사용 하 여 보내는 메시지에 서명 합니다. |
      | **서명 알고리즘** | 예, 서명이 사용 되는 경우 | 선택한 인증서를 기준으로 사용할 서명 알고리즘입니다. |
      | **인증서** | 예, 서명이 사용 되는 경우 | 서명에 사용할 인증서 |
      | **메시지 암호화 사용** | 아닙니다. | 선택한 암호화 알고리즘 및 인증서를 사용 하 여 나가는 암호화 |
      | **암호화 알고리즘** | 예, 암호화가 사용 되는 경우 | 선택한 인증서를 기준으로 사용할 암호화 알고리즘입니다. |
      | **인증서** | 예, 암호화가 사용 되는 경우 | 암호화에 사용할 인증서 |
      ||||

   1. **엔드포인트**에서 작업 메시지 및 승인을 보내는 데 사용할 필수 url을 지정 합니다.

      | 자산 | 필수 | 설명 |
      |----------|----------|-------------|
      | **작업 URL** |  yes | 작업 메시지를 보내는 데 사용할 URL입니다. URL은 동기 및 비동기 메시지에 대 한 필수 필드입니다. |
      | **승인 URL** | yes | 승인 메시지를 보내는 데 사용할 URL입니다. URL은 비동기 메시지에 대 한 필수 필드입니다. |
      ||||

1. 파트너에 대 한 RosettaNet PIP 참조를 사용 하 여 규약을 설정 하려면 **ROSETTANET pip 참조**를 선택 합니다. **Pip 이름**에서 이전에 만든 PIP의 이름을 선택 합니다.

   ![PIP 참조](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   선택 항목은 통합 계정에 설정 된 PIP를 기준으로 나머지 속성을 채웁니다. 필요한 경우 **PIP 역할**을 변경할 수 있습니다.

   ![선택한 PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

이러한 단계를 완료 한 후에는 RosettaNet 메시지를 보내거나 받을 준비가 된 것입니다.

## <a name="rosettanet-templates"></a>RosettaNet 템플릿

개발을 가속화 하 고 통합 패턴을 권장 하기 위해 논리 앱 템플릿을 사용 하 여 RosettaNet 메시지를 디코딩하고 인코딩할 수 있습니다. 논리 앱을 만들 때 논리 앱 디자이너의 템플릿 갤러리에서 선택할 수 있습니다. [Azure Logic Apps에 대 한 GitHub 리포지토리에서](https://github.com/Azure/logicapps)이러한 템플릿을 찾을 수도 있습니다.

![RosettaNet 템플릿](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>RosettaNet 메시지 수신 또는 디코드

1. [빈 논리 앱을 만듭니다](quickstart-create-first-logic-app-workflow.md).

1. [통합 계정을](logic-apps-enterprise-integration-create-integration-account.md#link-account) 논리 앱에 연결 합니다.

1. RosettaNet 메시지를 디코딩하는 작업을 추가 하려면 먼저 요청 트리거와 같은 논리 앱을 시작 하는 트리거를 추가 해야 합니다.

1. 트리거를 추가한 후에 **새 단계**를 선택 합니다.

   ![요청 트리거 추가](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 검색 상자에 "rosettanet"를 입력 하 고이 작업을 선택 합니다. **Rosettanet 디코드**

   !["RosettaNet 디코드" 작업을 찾아 선택 합니다.](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. 작업의 속성에 대 한 정보를 제공 합니다.

   ![작업 세부 정보 제공](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | 자산 | 필수 | 설명 |
   |----------|----------|-------------|
   | **메시지** | yes | 디코딩할 RosettaNet 메시지입니다.  |
   | **헤더** | yes | 버전에 대 한 값을 제공 하는 HTTP 헤더 (RNIF 버전) 및 파트너 간의 통신 형식을 나타내는 응답 형식 (동기 또는 비동기 일 수 있음) |
   | **역할** | yes | PIP에서 호스트 파트너의 역할 |
   ||||

   RosettaNet 디코드 작업에서 다른 속성과 함께 출력에는 **아웃 바운드 신호가**포함 되며,이 신호를 사용 하 여 파트너에 게 다시 인코드 및 반환 하거나 해당 출력에 대해 다른 작업을 수행할 수 있습니다.

## <a name="send-or-encode-rosettanet-messages"></a>RosettaNet 메시지 보내기 또는 인코딩

1. [빈 논리 앱을 만듭니다](quickstart-create-first-logic-app-workflow.md).

1. [통합 계정을](logic-apps-enterprise-integration-create-integration-account.md#link-account) 논리 앱에 연결 합니다.

1. RosettaNet 메시지를 인코딩하기 위한 작업을 추가 하려면 먼저 요청 트리거와 같은 논리 앱을 시작 하는 트리거를 추가 해야 합니다.

1. 트리거를 추가한 후에 **새 단계**를 선택 합니다.

   ![요청 트리거 추가](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 검색 상자에 "rosettanet"를 입력 하 고이 작업을 선택 합니다. **Rosettanet 인코드**

   !["RosettaNet 인코드" 작업을 찾아 선택 합니다.](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. 작업의 속성에 대 한 정보를 제공 합니다.

   ![작업 세부 정보 제공](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | 자산 | 필수 | 설명 |
   |----------|----------|-------------|
   | **메시지** | yes | 인코딩할 RosettaNet 메시지입니다.  |
   | **호스트 파트너** | yes | 호스트 파트너 이름 |
   | **게스트 파트너** | yes | 게스트 파트너 이름 |
   | **PIP 코드** | yes | PIP 코드 |
   | **PIP 버전** | yes | PIP 버전 |  
   | **PIP 인스턴스 id** | yes | 이 PIP 메시지의 고유 식별자입니다. |  
   | **메시지 유형** | yes | 인코딩할 메시지의 형식입니다. |  
   | **역할** | yes | 호스트 파트너의 역할입니다. |
   ||||

   인코딩된 메시지는 이제 파트너에 게 보낼 준비가 되었습니다.

1. 인코딩된 메시지를 보내기 위해이 예제에서는 "HTTP-인코딩된 메시지를 파트너에 게 보내기"로 이름이 바뀐 **http** 동작을 사용 합니다.

   ![RosettaNet 메시지를 보내기 위한 HTTP 동작](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   RosettaNet 표준에 따라 비즈니스 트랜잭션은 PIP에서 정의한 모든 단계가 완료 된 경우에만 완료 된 것으로 간주 됩니다.

1. 호스트는 인코딩된 메시지를 파트너에 게 보낸 후 신호 및 승인을 기다립니다. 이 작업을 수행 하려면 **RosettaNet wait for response** 동작을 추가 합니다.

   !["RosettaNet wait for response" 작업 추가](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   대기에 사용할 기간과 재시도 횟수는 통합 계정의 PIP 구성에 따라 결정 됩니다. 응답이 수신 되지 않으면이 작업은 실패 알림을 생성 합니다. 재시도를 처리 하려면 항상 **인코드** 를 설정 하 고 **until** 루프에서 응답 작업을 **대기** 합니다.

   ![RosettaNet 작업을 사용 하는 until 루프](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>다음 단계

* [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)을 사용하여 유효성 검사, 변환 및 기타 메시지 작업을 수행하는 방법을 알아봅니다.
* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
