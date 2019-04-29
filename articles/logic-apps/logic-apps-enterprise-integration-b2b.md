---
title: B2B 엔터프라이즈 통합 만들기 - Azure Logic Apps | Microsoft Docs
description: 엔터프라이즈 통합 팩을 사용하여 Azure Logic Apps에서 B2B 데이터 수신
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.date: 07/08/2016
ms.openlocfilehash: 05368f627c5e9482a43d5e30b0e16b1d47f6217c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60999179"
---
# <a name="receive-b2b-data-with-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps 및 엔터프라이즈 통합 팩을 사용하여 B2B 데이터 수신

파트너와 규약이 있는 통합 계정을 만들면 [엔터프라이즈 통합 팩](logic-apps-enterprise-integration-overview.md)을 사용하여 논리 앱을 위한 B2B 워크플로를 만들 준비가 되었습니다.

## <a name="prerequisites"></a>필수 조건

AS2 및 X12 작업을 사용하려면 엔터프라이즈 통합 계정이 있어야 합니다. [엔터프라이즈 통합 계정을 만드는 방법](../logic-apps/logic-apps-enterprise-integration-accounts.md)에 대해 알아보세요.

## <a name="create-a-logic-app-with-b2b-connectors"></a>B2B 커넥터를 사용하여 논리 앱 만들기

다음 단계에 따라 AS2 및 X12 작업을 사용하여 거래 업체로부터 데이터를 받는 B2B 논리 앱을 만듭니다.

1. 논리 앱을 만든 다음 [앱을 통합 계정에 연결](../logic-apps/logic-apps-enterprise-integration-accounts.md)합니다.

2. **요청 - HTTP 요청을 받은 경우** 트리거를 논리 앱에 추가합니다.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. **AS2 디코딩** 작업을 추가하려면 **작업 추가**를 선택합니다.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. 모든 작업에서 원하는 작업을 필터링하려면 검색 상자에서 **as2** 단어를 입력합니다.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. **AS2 - AS2 메시지 디코딩** 작업을 선택합니다.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. 입력으로 사용할 **본문**을 추가합니다. 
   이 예제에서는 논리 앱을 트리거하는 HTTP 요청의 본문을 선택합니다. 또는 **헤더** 필드에서 헤더를 입력하는 식을 입력합니다.

    @triggerOutputs()['헤더']

7. HTTP 요청 헤더에서 찾을 수 있는 AS2에 필요한 **헤더**를 추가합니다. 
   이 예제에서는 논리 앱을 트리거하는 HTTP 요청의 헤더를 선택합니다.

8. 이제 X12 메시지 디코딩 작업을 추가합니다. **작업 추가**를 선택합니다.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. 모든 작업에서 원하는 작업을 필터링하려면 검색 상자에서 **x12** 단어를 입력합니다.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. **X12 - X12 메시지 디코딩** 작업을 선택합니다.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. 이제 이 작업의 입력을 지정해야 합니다. 
    이 입력은 이전 AS2 작업의 출력입니다.

    실제 메시지 내용은 JSON 개체에 있고 base64로 인코딩되어 있으므로 식을 입력으로 지정해야 합니다. 
    **디코딩할 X12 플랫 파일 메시지** 입력 필드에서 다음 식을 입력합니다.
    
    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])

    이제 거래 업체로부터 받은 X12 데이터를 디코딩하고 JSON 개체의 항목을 출력하는 단계를 추가합니다. 
    데이터를 받았음을 파트너에게 알리려면 HTTP 응답 작업에서 AS2 MDN(메시지 처리 알림)을 포함하는 응답을 다시 보낼 수 있습니다.

12. **응답** 작업을 추가하려면 **작업 추가**를 선택합니다.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. 모든 작업에서 원하는 작업을 필터링하려면 검색 상자에서 **응답** 단어를 입력합니다.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. **응답** 작업을 선택합니다.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. **X12 메시지 디코딩** 작업의 출력에서 MDN에 액세스하려면 다음 식으로 응답 **본문** 필드를 설정합니다.

    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. 작업을 저장합니다.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

이제 B2B 논리 앱 설정이 완료되었습니다. 실제 애플리케이션에서는 디코딩된 X12 데이터를 LOB(기간 업무) 앱 또는 데이터 스토리지에 저장하려고 할 수도 있습니다. 자신의 LOB 앱을 연결하고 논리 앱에서 이러한 API를 사용하려면 추가 작업을 추가하거나 사용자 지정 API를 작성하면 됩니다.

## <a name="features-and-use-cases"></a>기능 및 사용 사례

* AS2 및 X12 디코딩 및 인코딩 작업을 통해 논리 앱에서 업계 표준 프로토콜을 사용하여 거래 업체 간에 데이터를 교환할 수 있습니다.
* 거래 업체와 데이터를 교환하려면 AS2와 X12를 함께 또는 개별로 사용할 수 있습니다.
* B2B 작업을 사용하면 통합 계정에서 파트너와 규약을 쉽게 만들고 논리 앱에서 사용할 수 있습니다.
* 논리 앱을 다른 작업으로 확장하면 다른 앱과 서비스(예: SalesForce) 간에 데이터를 보내고 받을 수 있습니다.

## <a name="learn-more"></a>자세한 정보
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](logic-apps-enterprise-integration-overview.md)
