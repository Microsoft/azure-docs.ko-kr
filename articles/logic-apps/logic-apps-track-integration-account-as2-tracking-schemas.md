---
title: B2B 메시지에 대한 AS2 추적 스키마 - Azure Logic Apps | Microsoft Docs
description: 엔터프라이즈 통합 팩을 사용하여 Azure Logic Apps 통합 계정에서 B2B 메시지를 모니터링하는 AS2 추적 스키마 만들기
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.date: 01/27/2017
ms.openlocfilehash: 180d90450497b38f107f3601944385a003f50282
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845786"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Azure Logic Apps 통합 계정에서 AS2 메시지 및 MDN을 추적하는 스키마 만들기

기업 간(B2B) 트랜잭션의 성공, 오류 및 메시지 속성을 모니터링하려면 통합 계정에 AS2 추적 스키마를 사용하면 됩니다.

* AS2 메시지 추적 스키마
* AS2 MDN 추적 스키마

## <a name="as2-message-tracking-schema"></a>AS2 메시지 추적 스키마

```json
{
   "agreementProperties": {  
      "senderPartnerName": "",  
      "receiverPartnerName": "",  
      "as2To": "",  
      "as2From": "",  
      "agreementName": ""  
   },  
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {
       },
      "outgoingHeaders": {
       },
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| 자산 | Type | 설명 |
| --- | --- | --- |
| senderPartnerName | String | AS2 메시지 보낸 사람의 파트너 이름입니다. (선택 사항) |
| receiverPartnerName | String | AS2 메시지 받는 사람의 파트너 이름입니다. (선택 사항) |
| as2To | String | AS2 메시지의 헤더에서 AS2 메시지 받는 사람의 이름입니다. 필수 사항입니다. |
| as2From | String | AS2 메시지의 헤더에서 AS2 메시지 보낸 사람의 이름입니다. 필수 사항입니다. |
| agreementName | String | 메시지가 확인되는 AS2 규약의 이름입니다. 선택 사항입니다. |
| direction | String | 메시지 흐름, 수신 또는 송신 방향입니다. 필수 사항입니다. |
| messageId | String | AS2 메시지의 헤더에서 AS2 메시지 ID입니다. 선택 사항입니다. |
| dispositionType |String | MDN(메시지 처리 알림) 처리 유형 값입니다. (선택 사항) |
| fileName | String | AS2 메시지의 헤더에서 파일 이름입니다. (선택 사항) |
| isMessageFailed |Boolean | AS2 메시지의 실패 여부입니다. 필수 사항입니다. |
| isMessageSigned | Boolean | AS2 메시지의 서명 여부입니다. 필수 사항입니다. |
| isMessageEncrypted | Boolean | AS2 메시지의 암호화 여부입니다. 필수 사항입니다. |
| isMessageCompressed |Boolean | AS2 메시지의 압축 여부입니다. 필수 사항입니다. |
| correlationMessageId | String | MDN과 메시지를 상호 연관 짓기 위한 AS2 메시지 ID입니다. (선택 사항) |
| incomingHeaders |JToken의 사전 | 들어오는 AS2 메시지 헤더 세부 정보입니다. (선택 사항) |
| outgoingHeaders |JToken의 사전 | 보내는 AS2 메시지 헤더 세부 정보입니다. (선택 사항) |
| isNrrEnabled | Boolean | 값을 알 수 없는 경우 기본값을 사용합니다. 필수 사항입니다. |
| isMdnExpected | Boolean | 값을 알 수 없는 경우 기본값을 사용합니다. 필수 사항입니다. |
| mdnType | 열거형 | 허용되는 값은 **NotConfigured**, **Sync** 또는 **Async**입니다. 필수 사항입니다. |
||||

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN 추적 스키마

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": "g"
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| 자산 | Type | 설명 |
| --- | --- | --- |
| senderPartnerName | String | AS2 메시지 보낸 사람의 파트너 이름입니다. (선택 사항) |
| receiverPartnerName | String | AS2 메시지 받는 사람의 파트너 이름입니다. (선택 사항) |
| as2To | String | AS2 메시지를 받는 파트너 이름입니다. 필수 사항입니다. |
| as2From | String | AS2 메시지를 보내는 파트너 이름입니다. 필수 사항입니다. |
| agreementName | String | 메시지가 확인되는 AS2 규약의 이름입니다. 선택 사항입니다. |
| direction |String | 메시지 흐름, 수신 또는 송신 방향입니다. 필수 사항입니다. |
| messageId | String | AS2 메시지 ID입니다. (선택 사항) |
| OriginalMessageId |String | AS2 원래 메시지 ID입니다. (선택 사항) |
| dispositionType | String | MDN 처리 유형 값입니다. (선택 사항) |
| isMessageFailed |Boolean | AS2 메시지의 실패 여부입니다. 필수 사항입니다. |
| isMessageSigned |Boolean | AS2 메시지의 서명 여부입니다. 필수 사항입니다. |
| isNrrEnabled | Boolean | 값을 알 수 없는 경우 기본값을 사용합니다. 필수 사항입니다. |
| statusCode | 열거형 | 허용되는 값은 **Accepted**, **Rejected** 또는 **AcceptedWithErrors**입니다. 필수 사항입니다. |
| micVerificationStatus | 열거형 | 허용되는 값은 **NotApplicable**, **Succeeded** 또는 **Failed**입니다. 필수 사항입니다. |
| correlationMessageId | String | 상관 관계 ID입니다. 원래 메시지 ID(MDN이 구성된 메시지의 메시지 ID)입니다. (선택 사항) |
| incomingHeaders | JToken의 사전 | 들어오는 메시지 헤더 세부 정보를 나타냅니다. (선택 사항) |
| outgoingHeaders |JToken의 사전 | 보내는 메시지 헤더 세부 정보를 나타냅니다. (선택 사항) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 프로토콜 추적 스키마

스키마를 추적하는 B2B 프로토콜에 대한 정보는 다음을 참조하세요.

* [X12 추적 스키마](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B 사용자 지정 추적 스키마](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>다음 단계

* [B2B 메시지 모니터링](logic-apps-monitor-b2b-message.md)에 대해 알아봅니다.
* 에 대 한 자세한 [Azure Monitor 로그에서 B2B 메시지 추적](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)