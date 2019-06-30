---
title: B2B 엔터프라이즈 통합용-Azure Logic Apps에 대 한 RosettaNet 메시지
description: Azure Logic Apps 엔터프라이즈 통합 팩을 사용 하 여 RosettaNet 메시지를 교환 합니다.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 88e02f3fbbca8007fdf479bb973f50c42a878d6e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333188"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Azure Logic Apps에서 B2B 엔터프라이즈 통합용 RosettaNet 메시지를 교환 합니다. 

[RosettaNet](https://resources.gs1us.org) 비영리 협회 비즈니스 정보를 공유 하기 위한 표준 프로세스 설정 됩니다. 이러한 표준은 일반적으로 공급 체인 프로세스에 대 한 사용 및 반도체, 전자 부품 및 물류 산업 분야에서 광범위 하 게 됩니다. RosettaNet 컨소시엄 만들고 Partner Interface Process (Pip), 모든 RosettaNet 메시지 교환에 대 한 일반적인 비즈니스 프로세스 정의 제공 하는 유지 관리 합니다. RosettaNet은 XML 기반으로 하 고 메시지 지침, 비즈니스 프로세스에 대 한 인터페이스 및 회사 간의 통신에 대 한 구현 프레임 워크를 정의 합니다.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md), RosettaNet 커넥터를 통해 RosettaNet 표준을 지 원하는 통합 솔루션을 만듭니다. 커넥터는 RosettaNet 구현 프레임 워크 (RNIF) 2.0.01 버전에 기반 합니다. RNIF는 비즈니스 파트너 공동으로 RosettaNet Pip를 실행할 수 있도록 개방 된 네트워크 응용 프로그램 프레임 워크입니다. 이 프레임 워크는 메시지 구조, 승인, 메일 확장 MIME (Multipurpose Internet) 인코딩 및 디지털 서명이 필요를 정의 합니다.

특히 커넥터는 이러한 기능을 제공 합니다.

* 인코딩 또는 RosettaNet 메시지를 수신 합니다.
* RosettaNet 메시지를 보내거나 디코딩.
* 차세대 실패 알림을 확인 하 고 응답을 기다립니다.

이러한 기능에 대 한 커넥터는 RNIF 2.0.01 정의한 모든 Pip를 지원 합니다. 파트너와의 통신에는 동기 또는 비동기 수 있습니다.

## <a name="rosettanet-concepts"></a>RosettaNet 개념

다음은 몇 가지 개념과 RosettaNet 사양을 고유 하 고는 RosettaNet 기반 통합을 빌드할 때 중요 한 용어입니다.

* **PIP**

  RosettaNet 조직이 만들고 Partner Interface Process (Pip), 모든 RosettaNet 메시지 교환에 대 한 일반적인 비즈니스 프로세스 정의 제공 하는 유지 관리 합니다. 각 PIP 사양 문서 형식 정의 (DTD) 파일 및 메시지 지침 문서를 제공합니다. DTD 파일 서비스-콘텐츠 메시지 구조를 정의 합니다. HTML 파일을 읽을 인 메시지 지침 문서 요소-수준 제약 조건을 지정 합니다. 함께 이러한 파일에는 비즈니스 프로세스의 전체 정의 제공합니다.

   Pip는 높은 수준의 비즈니스 함수 또는 클러스터 및 subfunction, 또는 세그먼트로 분류 됩니다. 예를 들어, "3A4" 구매 주문에 대 한 PIP는, "3" 하는 동안 주문 관리 함수 이며 "3A" 따옴표 및 주문 항목 하위 기능입니다. 자세한 내용은 참조는 [RosettaNet 사이트](https://resources.gs1us.org)합니다.

* **작업**

  파트는 PIP의 동작 메시지는 파트너 간에 교환 되는 비즈니스 메시지입니다.

* **Signal**

   파트를 PIP 신호 메시지는 작업 메시지에 대 한 응답으로 전송 되는 승인 합니다.

* **단일 작업 및 이중 동작**

  단일 동작 PIP를 유일한 응답 승인 신호 메시지입니다. 이중 동작 pip를 초기자는 응답 메시지를 받고 승인을 단일 동작 메시지 흐름 외에도 응답 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 규약 및 다른 B2B 아티팩트를 저장 합니다. 이 통합 계정을 통해 Azure 구독과 연결 되어야 합니다.

* 두 개 이상의 [파트너](../logic-apps/logic-apps-enterprise-integration-partners.md) 통합 계정에 정의 되 고 아래에서 "DUNS" 한정자를 사용 하 여 구성 **비즈니스 Id**

* PIP 프로세스 구성, 송신 또는 통합 계정에 RosettaNet 메시지를 수신 하는 데 필요한입니다. 프로세스 구성에는 PIP 구성 특성을 모두 저장합니다. 파트너와 규약을 만들 때이 구성을 참조할 수 있습니다. 통합 계정에 PIP 프로세스 구성을 만들려면 참조 [PIP 추가 프로세스 구성을](#add-pip)합니다.

* 선택적 [인증서](../logic-apps/logic-apps-enterprise-integration-certificates.md) 암호화, 암호 해독 또는 통합 계정에 업로드 하는 메시지에 서명 합니다. 인증서가 사용 하 여 서명 또는 암호화 하는 경우에 필요 합니다.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>PIP 프로세스 구성 추가

통합 계정에 PIP 프로세스 구성을 추가 하려면 다음이 단계를 수행 합니다.

1. 에 [Azure portal](https://portal.azure.com)을 찾아서 통합 계정을 엽니다.

1. 에 **개요** 창 합니다 **RosettaNet PIP** 타일입니다.

   ![RosettaNet 타일 선택](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. 아래 **RosettaNet PIP**, 선택 **추가**합니다. PIP 세부 정보를 제공 합니다.

   ![RosettaNet PIP 세부 정보 추가](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | 자산 | 필수 | 설명 |
   |----------|----------|-------------|
   | **Name** | 예 | PIP 이름 |
   | **PIP 코드** | 예 | PIP 3 자리 코드입니다. 자세한 내용은 [RosettaNet Pip](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips)합니다. |
   | **PIP 버전** | 예 | 선택한 PIP 코드를 기반으로 사용할 수 있는 PIP 버전 번호를 |
   ||||

   이러한 PIP 속성에 대 한 자세한 내용은 참조는 [RosettaNet 웹 사이트](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)합니다.

1. 완료 되 면 선택 **확인**, PIP 구성을 만듭니다.

1. 을 확인 하거나 프로세스 구성을 편집 하려면 PIP를 선택 하 고 선택 **JSON으로 편집**합니다.

   모든 프로세스를 PIP 사양에서 제공 되는 설정을 구성 합니다. Logic Apps는 대부분을의 기본값은 이러한 속성에 대 한 가장 일반적으로 사용 되는 값을 사용 하 여 설정 채웁니다.

   ![RosettaNet PIP 구성 편집](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. 설정을 적절 한 PIP 사양에 값을 해당 비즈니스 요구를 충족 하는지 확인 합니다. 필요한 경우 JSON에서 값을 업데이트 하 고 해당 변경 내용을 저장 합니다.

## <a name="create-rosettanet-agreement"></a>RosettaNet 규약 만들기

1. [Azure Portal](https://portal.azure.com)에서 통합 계정이 아직 열려 있지 않으면 찾아서 엽니다.

1. 에 **개요** 창 합니다 **계약** 타일입니다.

   ![규약 타일 선택](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. **규약**에서 **추가**를 선택합니다. 계약 세부 정보를 제공 합니다.

   ![계약 정보 추가](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | 자산 | 필수 | 설명 |
   |----------|----------|-------------|
   | **Name** | 예 | 규약의 이름 |
   | **계약 유형** | 예 | 선택 **RosettaNet**합니다. |
   | **호스트 파트너** | 예 | 규약에는 호스트와 게스트 파트너가 모두 필요합니다. 호스트 파트너는 규약을 구성하는 조직을 나타냅니다. |
   | **호스트 Id** | 예 | 호스트 파트너의 식별자입니다. |
   | **게스트 파트너** | 예 | 규약에는 호스트와 게스트 파트너가 모두 필요합니다. 게스트 파트너는 호스트 파트너와 함께 일하는 조직을 나타냅니다. |
   | **게스트 Id** | 예 | 게스트 파트너의 식별자입니다. |
   | **수신 설정** | 다름 | 이러한 속성은 호스트 파트너에서 받은 모든 메시지에 적용 |
   | **송신 설정** | 다름 | 이러한 속성은 호스트 파트너에서 보낸 모든 메시지에 적용 |  
   | **RosettaNet PIP 참조** | 예 | 규약에 대 한 PIP 참조입니다. 모든 RosettaNet 메시지에는 PIP 구성이 필요 합니다. |
   ||||

1. 게스트 파트너 로부터 들어오는 메시지를 수신 하기 위한 계약을 설정 하려면 선택 **수신 설정**합니다.

   ![수신 설정](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. 서명 또는 들어오는 메시지에 대해 암호화를 사용 하도록 설정 **메시지**를 선택 **메시지에 서명 해야** 또는 **메시지를 암호화할지** 각각.

      | 자산 | 필수 | 설명 |
      |----------|----------|-------------|
      | **메시지 서명 필요** | 아닙니다. | 선택한 인증서를 사용 하 여 들어오는 메시지에 서명 합니다. |
      | **인증서** | 예, 로그인을 사용 하는 경우 | 서명에 사용할 인증서를 |
      | **메시지 암호화 사용** | 아닙니다. | 선택한 인증서를 사용 하 여 들어오는 메시지를 암호화 합니다. |
      | **인증서** | 예, 암호화를 사용 하는 경우 | 암호화에 사용할 인증서 |
      ||||

   1. 각 선택은 각 선택 [인증서](./logic-apps-enterprise-integration-certificates.md), 이전 서명 또는 암호화를 위해 사용 하 여 통합 계정에 추가 합니다.

1. 게스트 파트너에 게 메시지를 보내기 위한 규약 설정 선택 **송신 설정**합니다.

   ![송신 설정](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. 서명 또는 보내는 메시지에 대해 암호화를 사용 하도록 설정 **메시지**를 선택 **메시지 서명 사용** 하거나 **메시지 암호화 사용** 각각. 선택할 때마다 해당 알고리즘을 선택 하 고 [인증서](./logic-apps-enterprise-integration-certificates.md), 이전 서명 또는 암호화를 위해 사용 하 여 통합 계정에 추가 합니다.

      | 자산 | 필수 | 설명 |
      |----------|----------|-------------|
      | **메시지 서명 사용** | 아닙니다. | 선택한 서명 알고리즘 및 인증서를 사용 하 여 보내는 메시지에 서명 합니다. |
      | **서명 알고리즘** | 예, 로그인을 사용 하는 경우 | 선택한 인증서를 기반으로 사용 하는 서명 알고리즘 |
      | **인증서** | 예, 로그인을 사용 하는 경우 | 서명에 사용할 인증서를 |
      | **메시지 암호화 사용** | 아닙니다. | 선택한 암호화 알고리즘과 인증서를 사용 하 여 나가는 암호화 합니다. |
      | **암호화 알고리즘** | 예, 암호화를 사용 하는 경우 | 선택한 인증서를 기반으로 한 암호화 알고리즘을 사용 하려면 |
      | **인증서** | 예, 암호화를 사용 하는 경우 | 암호화에 사용할 인증서 |
      ||||

   1. 아래 **끝점**, 작업 메시지 및 승인을 보내는 데 필요한 Url을 지정 합니다.

      | 자산 | 필수 | 설명 |
      |----------|----------|-------------|
      | **작업 URL** |  예 | 동작 메시지를 보내는 데 사용할 URL입니다. URL에는 동기 및 비동기 메시지에 대 한 필수 필드입니다. |
      | **승인 URL** | 예 | 승인 메시지를 보내는 데 사용할 URL입니다. URL에는 비동기 메시지에 대 한 필수 필드입니다. |
      ||||

1. 파트너에 대 한 참조를 RosettaNet PIP를 사용 하 여 규약을 설정 하려면 선택 **RosettaNet PIP 참조**합니다. 아래 **PIP 이름**, 이전에 만든된 PIP 프로그램의 이름을 선택 합니다.

   ![PIP 참조](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   선택 항목은 통합 계정에 설정 하는 PIP를 기반으로 하는 나머지 속성을 채웁니다. 필요에 따라 변경할 수 있습니다 합니다 **PIP 역할**입니다.

   ![선택한 PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

다음이 단계를 완료 하면 보내거나 RosettaNet 메시지를 받을 준비가 된 것입니다.

## <a name="rosettanet-templates"></a>RosettaNet 템플릿

개발을 가속화 하 고 통합 패턴을 권장 합니다를 디코딩 및 인코딩 RosettaNet 메시지에 대 한 논리 앱 템플릿을 사용할 수 있습니다. 논리 앱을 만들 때 논리 앱 디자이너에서 템플릿 갤러리에서 선택할 수 있습니다. 이 템플릿의 찾을 수도 있습니다는 [Azure Logic Apps에 대 한 GitHub 리포지토리](https://github.com/Azure/logicapps)합니다.

![RosettaNet 템플릿](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>받거나 RosettaNet 메시지 디코딩

1. [빈 논리 앱 만들기](quickstart-create-first-logic-app-workflow.md)합니다.

1. [통합 계정을 연결](logic-apps-enterprise-integration-create-integration-account.md#link-account) 논리 앱에 있습니다.

1. RosettaNet 메시지를 디코딩하는 작업을 추가할 수 있습니다, 요청 트리거와 같은 논리 앱을 시작 하는 것에 대 한 트리거를 추가 해야 합니다.

1. 트리거를 추가한 후 선택할 **새 단계**합니다.

   ![요청 트리거 추가](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 검색 상자에 "rosettanet"를 입력 하 고이 작업을 선택 합니다. **RosettaNet 디코딩**

   ![찾아 "RosettaNet 디코딩" 작업 선택](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. 작업의 속성에 대 한 정보를 제공 합니다.

   ![작업 세부 정보를 제공 합니다.](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | 자산 | 필수 | 설명 |
   |----------|----------|-------------|
   | **메시지** | 예 | RosettaNet 메시지를 디코딩하는 데  |
   | **헤더** | 예 | RNIF 버전 버전 및 파트너 간의 통신 형식을 나타내는 동기 또는 비동기 수 있으며 응답 형식에 대 한 값을 제공 하는 HTTP 헤더 |
   | **역할** | 예 | PIP에서 호스트 파트너의 역할 |
   ||||

   RosettaNet 디코딩 작업에서 다른 속성을 함께 출력을 포함 **아웃 바운드 신호**는 인코딩 및 파트너에 다시 반환 또는 출력에서 다른 작업을 수행 하도록 선택할 수 있습니다.

## <a name="send-or-encode-rosettanet-messages"></a>보내거나 RosettaNet 메시지 인코딩

1. [빈 논리 앱 만들기](quickstart-create-first-logic-app-workflow.md)합니다.

1. [통합 계정을 연결](logic-apps-enterprise-integration-create-integration-account.md#link-account) 논리 앱에 있습니다.

1. RosettaNet 메시지를 인코딩하는 작업을 추가할 수 있습니다, 요청 트리거와 같은 논리 앱을 시작 하는 것에 대 한 트리거를 추가 해야 합니다.

1. 트리거를 추가한 후 선택할 **새 단계**합니다.

   ![요청 트리거 추가](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 검색 상자에 "rosettanet"를 입력 하 고이 작업을 선택 합니다. **RosettaNet 인코딩**

   ![찾아 "RosettaNet 인코딩" 작업 선택](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. 작업의 속성에 대 한 정보를 제공 합니다.

   ![작업 세부 정보를 제공 합니다.](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | 자산 | 필수 | 설명 |
   |----------|----------|-------------|
   | **메시지** | 예 | 인코딩할 RosettaNet 메시지  |
   | **호스트 파트너** | 예 | 호스트 파트너 이름 |
   | **게스트 파트너** | 예 | 게스트 파트너 이름 |
   | **PIP 코드** | 예 | PIP 코드 |
   | **PIP 버전** | 예 | PIP 버전 |  
   | **PIP 인스턴스 id** | 예 | 이 PIP 메시지에 대 한 고유 식별자 |  
   | **메시지 유형** | 예 | 인코딩할 메시지의 유형 |  
   | **역할** | 예 | 호스트 파트너의 역할 |
   ||||

   인코딩된 메시지를 파트너에 게 보낼 준비가 되었습니다.

1. 이 예제에서는 인코딩된 메시지를 보내려고 합니다 **HTTP** 작업은 이름이 "HTTP-파트너 송신 인코딩된 메시지".

   ![RosettaNet 메시지를 전송 하는 것에 대 한 HTTP 작업](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   RosettaNet 표준에 따라 비즈니스 트랜잭션은 PIP 정의한 모든 단계를 완료 하는 경우에 전체 간주 됩니다.

1. 호스트 파트너에 인코딩된 메시지를 보내면, 호스트 신호 및 승인을 기다립니다. 이 작업을 수행 하려면 추가 합니다 **RosettaNet 대기 응답에 대 한** 작업 합니다.

   !["RosettaNet 대기 응답에 대 한" 작업 추가](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   대기 및 재시도 횟수에 사용 하는 통합 계정에서 PIP 구성을 기반으로 합니다. 응답이 수신 되지 않으면이 작업에 실패 알림을 생성 합니다. 항상 배치를 처리 하기 위해 재시도 **인코딩** 및 **응답을 기다리는** 작업에는 **때까지** 루프.

   ![Until 루프 RosettaNet 작업과](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>다음 단계

* [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)을 사용하여 유효성 검사, 변환 및 기타 메시지 작업을 수행하는 방법을 알아봅니다.
* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
