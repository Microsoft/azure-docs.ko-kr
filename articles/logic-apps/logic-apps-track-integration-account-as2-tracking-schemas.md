---
title: B2B 메시지에 대한 AS2 추적 스키마
description: Azure Logic Apps에서 AS2 메시지를 모니터링하는 추적 스키마 만들기
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "76906964"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>Azure Logic Apps에서 AS2 메시지를 추적하는 스키마 만들기

기업 간(B2B) 트랜잭션의 성공, 오류 및 메시지 속성을 모니터링하려면 통합 계정에 AS2 추적 스키마를 사용하면 됩니다.

* AS2 메시지 추적 스키마
* AS2 MDN(메시지 처리 알림) 추적 스키마

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
      "incomingHeaders": {},
      "outgoingHeaders": {},
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| 속성 | 필수 | Type | 설명 |
|----------|----------|------|-------------|
| senderPartnerName | 예 | String | AS2 메시지를 보낸 사람의 파트너 이름 |
| receiverPartnerName | 예 | String | AS2 메시지를 받는 사람의 파트너 이름 |
| as2To | 예 | String | AS2 메시지의 헤더에서 AS2 메시지를 받는 사람의 이름 |
| as2From | 예 | String | AS2 메시지의 헤더에서 AS2 메시지를 보낸 사람의 이름 |
| agreementName | 예 | String | 메시지가 확인되는 AS2 규약의 이름 |
| direction | 예 | String | `receive` 또는 `send`인 메시지 흐름 방향 |
| messageId | 예 | String | AS2 메시지 헤더의 AS2 메시지 ID |
| dispositionType | 예 | String | MDN(메시지 처리 알림) 처리 유형 값 |
| fileName | 예 | String | AS2 메시지 헤더의 파일 이름 |
| isMessageFailed | 예 | 부울 | AS2 메시지의 실패 여부 |
| isMessageSigned | 예 | 부울 | AS2 메시지의 서명 여부 |
| isMessageEncrypted | 예 | 부울 | AS2 메시지의 암호화 여부 |
| isMessageCompressed | 예 | 부울 | AS2 메시지의 압축 여부 |
| correlationMessageId | 예 | String | 메시지와 MDN의 상관 관계를 지정하기 위한 AS2 메시지 ID |
| incomingHeaders | 예 | JToken의 사전 | 들어오는 AS2 메시지 헤더 세부 정보 |
| outgoingHeaders | 예 | JToken의 사전 | 보내는 AS2 메시지 헤더 세부 정보 |
| isNrrEnabled | 예 | 부울 | 값을 알 수 없는 경우 기본값 사용 여부 |
| isMdnExpected | 예 | 부울 | 값을 알 수 없는 경우 기본값 사용 여부 |
| mdnType | 예 | 열거형 | 허용되는 값: `NotConfigured`, `Sync`, `Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN 추적 스키마

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

| 속성 | 필수 | Type | 설명 |
|----------|----------|------|-------------|
| senderPartnerName | 예 | String | AS2 메시지를 보낸 사람의 파트너 이름 |
| receiverPartnerName | 예 | String | AS2 메시지를 받는 사람의 파트너 이름 |
| as2To | 예 | String | AS2 메시지를 받는 파트너 이름 |
| as2From | 예 | String | AS2 메시지를 보내는 파트너 이름 |
| agreementName | 예 | String | 메시지가 확인되는 AS2 규약의 이름 |
| direction | 예 | String | `receive` 또는 `send`인 메시지 흐름 방향 |
| messageId | 예 | String | AS2 메시지 ID |
| OriginalMessageId | 예 | String | AS2 원본 메시지 ID |
| dispositionType | 예 | String | MDN 처리 유형 값 |
| isMessageFailed | 예 | 부울 | AS2 메시지의 실패 여부 |
| isMessageSigned | 예 | 부울 | AS2 메시지의 서명 여부 |
| isNrrEnabled | 예 | 부울 | 값을 알 수 없는 경우 기본값 사용 여부 |
| statusCode | 예 | 열거형 | 허용되는 값: `Accepted`, `Rejected`, `AcceptedWithErrors` |
| micVerificationStatus | 예 | 열거형 | 허용되는 값: `NotApplicable`, `Succeeded`, `Failed` |
| correlationMessageId | 예 | String | MDN이 구성된 원본 메시지의 ID인 상관 관계 ID |
| incomingHeaders | 예 | JToken의 사전 | 들어오는 AS2 메시지 헤더 세부 정보 |
| outgoingHeaders | 예 | JToken의 사전 | 보내는 메시지 헤더 세부 정보 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 프로토콜 추적 스키마

스키마를 추적하는 B2B 프로토콜에 대한 정보는 다음을 참조하세요.

* [X12 추적 스키마](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B 사용자 지정 추적 스키마](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>다음 단계

* [Azure Monitor 로그를 사용하여 B2B 메시지 모니터링](../logic-apps/monitor-b2b-messages-log-analytics.md)