---
title: B2B 엔터프라이즈 통합용-Azure Logic Apps에 대 한 AS2 메시지
description: 엔터프라이즈 통합 팩을 사용 하 여 Azure Logic Apps에서 AS2 메시지 교환
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: b494f6524e5105a95bc8a24a6fa2521abcca3f7b
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760192"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 B2B 엔터프라이즈 통합용 AS2 메시지 교환

Azure Logic Apps에서 AS2 메시지를 사용 하려면 트리거 및 AS2 통신을 관리 하는 것에 대 한 작업을 제공 하는 AS2 커넥터를 사용할 수 있습니다. 예를 들어, 메시지를 전송할 때 보안 및 안정성을 설정, 이러한 작업을 사용할 수 있습니다.

* [**AS2 메시지 인코딩** 동작](#encode) 암호화, 디지털 서명 및 승인을 통해 알림을 MDN (Message Disposition)를 제공 하는 도움말을 부인 방지를 지원 합니다. 예를 들어,이 작업은 AS2/HTTP 헤더를 적용 하 고 구성 된 경우 이러한 작업을 수행 합니다.

  * 나가는 메시지를 서명 합니다.
  * 보내는 메시지를 암호화합니다.
  * 메시지를 압축합니다.
  * MIME 헤더로 파일 이름을 전송합니다.

* [**AS2 메시지 디코딩** 동작](#decode) 암호 해독, 디지털 서명 및 승인을 통해 알림을 MDN (Message Disposition)를 제공 합니다. 예를 들어,이 동작은 이러한 작업을 수행합니다. 

  * AS2/HTTP 헤더를 처리합니다.
  * 원래 아웃 바운드 메시지와 함께 받은 Mdn을 조정합니다.
  * 업데이트 하 고 비 거부 데이터베이스에 레코드를 서로 관련 시킵니다.
  * AS2 상태 보고에 대 한 레코드를 씁니다.
  * Base64로 인코딩된으로 출력 페이로드 콘텐츠입니다.
  * Mdn이 필요한 지 여부를 결정 합니다. 계약을 AS2에 따라 동기 또는 비동기 Mdn을 해야 하는지 여부를 결정 합니다.
  * AS2 규약에 따라 동기 또는 비동기 Mdn을 생성 합니다.
  * Mdn의 상관 관계 토큰과 속성을 설정합니다.

  이 이렇게 구성 된 경우 이러한 작업을 수행 합니다.

  * 서명을 확인합니다.
  * 메시지를 해독합니다.
  * 메시지를 압축을 풉니다. 
  * 확인 하 고 메시지 ID 중복 허용 안 함.

이 문서는 AS2 인코딩 추가 하는 방법 및 기존 논리 앱에 디코딩 작업을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* AS2 커넥터 및 논리 앱 워크플로 시작 하는 트리거를 사용 하려는 논리 앱. AS2 커넥터 작업을 하지 트리거를 제공합니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

* [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) Azure 구독과 연결 되어 있는를 AS2 커넥터를 사용 하려는 논리 앱에 연결 합니다. 논리 앱과 통합 계정 모두 동일한 위치 또는 Azure 지역에 있어야 합니다.

* 두 개 이상의 [거래](../logic-apps/logic-apps-enterprise-integration-partners.md) 는 이미 정의 했으므로 통합 계정에 AS2 id 한정자를 사용 하 여 합니다.

* AS2 AS2 커넥터를 사용 하려면 먼저 만들어야 [계약](../logic-apps/logic-apps-enterprise-integration-agreements.md) 거래 파트너와 저장소 통합 계정에는 계약입니다.

* 사용 하는 경우 [Azure Key Vault](../key-vault/key-vault-overview.md) 인증서 관리에 대 한 키 자격 증명 모음을 허용 하는지 확인 합니다 **Encrypt** 및 **암호를 해독** 작업 합니다. 그렇지 않은 경우 인코딩 및 디코딩 작업 실패 합니다.

  Azure portal에서 키 자격 증명 모음으로 이동, 사용자의 자격 증명 모음 키를 볼 **허용 된 작업**, 있는지 확인 합니다 **암호화** 및 **암호를 해독** 작업을 선택한 상태입니다.

  ![자격 증명 모음 키 작업 확인](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>AS2 메시지 인코딩

1. 아직 하지 않은 경우에 [Azure portal](https://portal.azure.com), Logic App Designer에서 논리 앱을 엽니다.

1. 디자이너에서 논리 앱에 새 작업을 추가 합니다. 

1. 아래 **작업 선택** 검색 상자를 선택 하 고 **모든**합니다. 검색 상자에 "as2 인코딩"를 입력 하 고이 작업을 선택 합니다. **AS2 메시지 인코딩**합니다.

   !["AS2 메시지 인코딩"를 선택 합니다.](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. 통합 계정에 기존 연결이 없으면 이제 해당 연결을 만들려면 묻는 메시지가 나타납니다. 연결 이름, 연결 하 고 선택 하려는 통합 계정을 선택 **만들기**합니다.

   ![통합 계정에 대 한 연결 만들기](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  
 
1. 이제 이러한 속성에 대 한 정보를 제공 합니다.

   | 자산 | 설명 |
   |----------|-------------|
   | **AS2-From** | AS2 규약에 지정 된 대로 메시지를 보낸 사람 식별자 |
   | **AS2-To** | AS2 규약에 지정 된 대로 메시지 받는 사람 식별자 |
   | **body** | 메시지 페이로드 |
   |||

   예를 들면 다음과 같습니다.

   ![메시지 인코딩 속성](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>AS2 메시지 디코딩

1. 아직 하지 않은 경우에 [Azure portal](https://portal.azure.com), Logic App Designer에서 논리 앱을 엽니다.

1. 디자이너에서 논리 앱에 새 작업을 추가 합니다. 

1. 아래 **작업 선택** 검색 상자를 선택 하 고 **모든**합니다. 검색 상자에 "as2 디코딩"를 입력 하 고이 작업을 선택 합니다. **AS2 메시지 디코딩**

   !["AS2 디코딩 메시지"를 선택 합니다.](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. 통합 계정에 기존 연결이 없으면 이제 해당 연결을 만들려면 묻는 메시지가 나타납니다. 연결 이름, 연결 하 고 선택 하려는 통합 계정을 선택 **만들기**합니다.

   ![통합 계정에 대 한 연결 만들기](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  

1. 에 대 한 **본문** 하 고 **헤더**, 이전 트리거나 작업 출력에서 이러한 값을 선택 합니다.

   예를 들어, 논리 앱 요청 트리거를 통해 메시지를 수신 합니다. 해당 트리거에서 출력을 선택할 수 있습니다.

   ![요청 출력에서 본문 및 헤더를 선택합니다.](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png) 

## <a name="sample"></a>샘플

완벽하게 작동하는 논리 앱 및 샘플 AS2 시나리오를 배포하려면 [AS2 논리 앱 템플릿 및 시나리오](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)를 참조하세요.

## <a name="connector-reference"></a>커넥터 참조

트리거, 작업 및 커넥터의 OpenAPI에 설명 된 대로 제한 등의 기술 세부 정보에 대 한 (이전의 Swagger) 파일, 참조를 [커넥터의 참조 페이지](/connectors/as2/)합니다.

## <a name="next-steps"></a>다음 단계

에 대 한 자세한 정보는 [엔터프라이즈 통합 팩](logic-apps-enterprise-integration-overview.md)
