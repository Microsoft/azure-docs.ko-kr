---
title: B2B 메시지에 대한 X12 추적 스키마 - Azure Logic Apps | Microsoft Docs
description: 엔터프라이즈 통합 팩을 사용하여 Azure Logic Apps 통합 계정에서 B2B 메시지를 모니터링하는 X12 추적 스키마 만들기
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.date: 01/27/2017
ms.openlocfilehash: 1db324006e1e6332b5fdd8afd28ebed8a32ac707
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845769"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Azure Logic Apps 통합 계정에서 X12 메시지를 추적하는 스키마 만들기

기업 간(B2B) 트랜잭션의 성공, 오류 및 메시지 속성을 모니터링하려면 통합 계정에 X12 추적 스키마를 사용하면 됩니다.

* X12 트랜잭션 집합 추적 스키마
* X12 트랜잭션 집합 승인 추적 스키마
* X12 교환 추적 스키마
* X12 교환 승인 추적 스키마
* X12 기능 그룹 추적 스키마
* X12 기능 그룹 승인 추적 스키마

## <a name="x12-transaction-set-tracking-schema"></a>X12 트랜잭션 집합 추적 스키마

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "transactionSetControlNumber": "",
      "CorrelationMessageId": "",
      "messageType": "",
      "isMessageFailed": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "needAk2LoopForValidMessages":  "",
      "segmentsCount": ""
   }
}
```

| 자산 | Type | 설명 |
| --- | --- | --- |
| senderPartnerName | String | X12 메시지 보낸 사람의 파트너 이름 (선택 사항) |
| receiverPartnerName | String | X12 메시지 받는 사람의 파트너 이름 (선택 사항) |
| senderQualifier | String | 송신 파트너 한정자 필수 사항입니다. |
| senderIdentifier | String | 송신 파트너 식별자 필수 사항입니다. |
| receiverQualifier | String | 수신 파트너 한정자 (필수) |
| receiverQualifier | String | 수신 파트너 식별자 필수 사항입니다. |
| agreementName | String | 메시지가 확인되는 X12 규약의 이름 (선택 사항) |
| direction | 열거형 | 메시지 흐름, 수신 또는 송신 방향 필수 사항입니다. |
| interchangeControlNumber | String | 교환 컨트롤 번호 (선택 사항) |
| functionalGroupControlNumber | String | 기능 컨트롤 번호 (선택 사항) |
| transactionSetControlNumber | String | 트랜잭션 집합 컨트롤 번호 (선택 사항) |
| CorrelationMessageId | String | 상관 관계 메시지 ID {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}의 조합입니다. (선택 사항) |
| messageType | String | 트랜잭션 집합 또는 문서 형식 (선택 사항) |
| isMessageFailed | Boolean | X12 메시지 실패 여부 필수 사항입니다. |
| isTechnicalAcknowledgmentExpected | Boolean | X12 규약에서 기술 승인이 구성되었는지 여부 필수 사항입니다. |
| isFunctionalAcknowledgmentExpected | Boolean | X12 규약에서 기능 승인이 구성되었는지 여부 필수 사항입니다. |
| needAk2LoopForValidMessages | Boolean | 유효한 메시지에 AK2 루프가 필요한지 여부 필수 사항입니다. |
| segmentsCount | Integer | X12 트랜잭션 집합의 세그먼트의 수 (선택 사항) |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 트랜잭션 집합 승인 추적 스키마

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "respondingtransactionSetControlNumber": "",
      "respondingTransactionSetId": "",
      "statusCode": "",
      "processingStatus": "",
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| 자산 | Type | 설명 |
| --- | --- | --- |
| senderPartnerName | String | X12 메시지 보낸 사람의 파트너 이름 (선택 사항) |
| receiverPartnerName | String | X12 메시지 받는 사람의 파트너 이름 (선택 사항) |
| senderQualifier | String | 송신 파트너 한정자 필수 사항입니다. |
| senderIdentifier | String | 송신 파트너 식별자 필수 사항입니다. |
| receiverQualifier | String | 수신 파트너 한정자 (필수) |
| receiverQualifier | String | 수신 파트너 식별자 필수 사항입니다. |
| agreementName | String | 메시지가 확인되는 X12 규약의 이름 (선택 사항) |
| direction | 열거형 | 메시지 흐름, 수신 또는 송신 방향 필수 사항입니다. |
| interchangeControlNumber | String | 기능 승인의 교환 컨트롤 번호. 이 값은 파트너로 전송된 메시지에 대해 기능 승인이 수신된 송신 쪽에서만 채워집니다. (선택 사항) |
| functionalGroupControlNumber | String | 기능 승인의 기능 그룹 컨트롤 번호. 이 값은 파트너로 전송된 메시지에 대해 기능 승인이 수신된 송신 쪽에서만 채워집니다. (선택 사항) |
| isaSegment | String | 메시지의 ISA 세그먼트. 이 값은 파트너로 전송된 메시지에 대해 기능 승인이 수신된 송신 쪽에서만 채워집니다. (선택 사항) |
| gsSegment | String | 메시지의 GS 세그먼트. 이 값은 파트너로 전송된 메시지에 대해 기능 승인이 수신된 송신 쪽에서만 채워집니다. (선택 사항) |
| respondingfunctionalGroupControlNumber | String | 응답 교환 컨트롤 번호 (선택 사항) |
| respondingFunctionalGroupId | String | 승인에서 AK101에 매핑되는 응답 기능 그룹 ID (선택 사항) |
| respondingtransactionSetControlNumber | String | 응답 트랜잭션 집합 컨트롤 번호 (선택 사항) |
| respondingTransactionSetId | String | 승인에서 AK201에 매핑되는 응답 트랜잭션 집합 ID (선택 사항) |
| statusCode | Boolean | 트랜잭션 집합 승인 상태 코드 필수 사항입니다. |
| segmentsCount | 열거형 | 승인 상태 코드. 허용되는 값은 **Accepted**, **Rejected** 또는 **AcceptedWithErrors**입니다. 필수 사항입니다. |
| processingStatus | 열거형 | 승인의 처리 상태. 허용되는 값은 **Received**, **Generated** 및 **Sent**입니다. 필수 사항입니다. |
| CorrelationMessageId | String | 상관 관계 메시지 ID {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}의 조합입니다. (선택 사항) |
| isMessageFailed | Boolean | X12 메시지 실패 여부 필수 사항입니다. |
| ak2Segment | String | 수신된 기능 그룹 내의 트랜잭션 집합에 대한 승인 (선택 사항) |
| ak3Segment | String | 데이터 세그먼트의 오류를 보고합니다. (선택 사항) |
| ak5Segment | String | AK2 세그먼트에 식별된 트랜잭션 집합이 허용되는지 또는 거부되는지와 그 이유를 보고합니다. (선택 사항) |
||||

## <a name="x12-interchange-tracking-schema"></a>X12 교환 추적 스키마

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "isa09": "",
      "isa10": "",
      "isa11": "",
      "isa12": "",
      "isa14": "",
      "isa15": "",
      "isa16": ""
   }
}
```

| 자산 | Type | 설명 |
| --- | --- | --- |
| senderPartnerName | String | X12 메시지 보낸 사람의 파트너 이름 (선택 사항) |
| receiverPartnerName | String | X12 메시지 받는 사람의 파트너 이름 (선택 사항) |
| senderQualifier | String | 송신 파트너 한정자 필수 사항입니다. |
| senderIdentifier | String | 송신 파트너 식별자 필수 사항입니다. |
| receiverQualifier | String | 수신 파트너 한정자 (필수) |
| receiverQualifier | String | 수신 파트너 식별자 필수 사항입니다. |
| agreementName | String | 메시지가 확인되는 X12 규약의 이름 (선택 사항) |
| direction | 열거형 | 메시지 흐름, 수신 또는 송신 방향 필수 사항입니다. |
| interchangeControlNumber | String | 교환 컨트롤 번호 (선택 사항) |
| isaSegment | String | 메시지 ISA 세그먼트 (선택 사항) |
| isTechnicalAcknowledgmentExpected | Boolean | X12 규약에서 기술 승인이 구성되었는지 여부 필수 사항입니다. |
| isMessageFailed | Boolean | X12 메시지 실패 여부 필수 사항입니다. |
| isa09 | String | X12 문서 교환 날짜 (선택 사항) |
| isa10 | String | X12 문서 교환 시간 (선택 사항) |
| isa11 | String | X12 교환 컨트롤 표준 식별자 (선택 사항) |
| isa12 | String | X12 교환 컨트롤 버전 번호 (선택 사항) |
| isa14 | String | X12 승인이 요청되었습니다. (선택 사항) |
| isa15 | String | 테스트 또는 프로덕션에 대한 표시기 (선택 사항) |
| isa16 | String | 요소 구분 기호 (선택 사항) |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 교환 승인 추적 스키마

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "respondingInterchangeControlNumber": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ta102": "",
      "ta103": "",
      "ta105": ""
   }
}
```

| 자산 | Type | 설명 |
| --- | --- | --- |
| senderPartnerName | String | X12 메시지 보낸 사람의 파트너 이름 (선택 사항) |
| receiverPartnerName | String | X12 메시지 받는 사람의 파트너 이름 (선택 사항) |
| senderQualifier | String | 송신 파트너 한정자 필수 사항입니다. |
| senderIdentifier | String | 송신 파트너 식별자 필수 사항입니다. |
| receiverQualifier | String | 수신 파트너 한정자 (필수) |
| receiverQualifier | String | 수신 파트너 식별자 필수 사항입니다. |
| agreementName | String | 메시지가 확인되는 X12 규약의 이름 (선택 사항) |
| direction | 열거형 | 메시지 흐름, 수신 또는 송신 방향 필수 사항입니다. |
| interchangeControlNumber | String | 파트너로부터 수신된 기술 승인의 교환 컨트롤 번호 (선택 사항) |
| isaSegment | String | 파트너로부터 수신된 기술 승인의 ISA 세그먼트 (선택) |
| respondingInterchangeControlNumber |String | 파트너로부터 수신된 기술 승인의 교환 컨트롤 번호 (선택 사항) |
| isMessageFailed | Boolean | X12 메시지 실패 여부 필수 사항입니다. |
| statusCode | 열거형 | 교환 승인 상태 코드. 허용되는 값은 **Accepted**, **Rejected** 및 **AcceptedWithErrors**입니다. 필수 사항입니다. |
| processingStatus | 열거형 | 승인 상태. 허용되는 값은 **Received**, **Generated** 및 **Sent**입니다. 필수 사항입니다. |
| ta102 | String | 교환 날짜 (선택 사항) |
| ta103 | String | 교환 시간 (선택 사항) |
| ta105 | String | 교환 노트 코드 (선택 사항) |
||||

## <a name="x12-functional-group-tracking-schema"></a>X12 기능 그룹 추적 스키마

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "gsSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "gs01": "",
      "gs02": "",
      "gs03": "",
      "gs04": "",
      "gs05": "",
      "gs07": "",
      "gs08": ""
   }
}
```

| 자산 | Type | 설명 |
| --- | --- | --- |
| senderPartnerName | String | X12 메시지 보낸 사람의 파트너 이름 (선택 사항) |
| receiverPartnerName | String | X12 메시지 받는 사람의 파트너 이름 (선택 사항) |
| senderQualifier | String | 송신 파트너 한정자 필수 사항입니다. |
| senderIdentifier | String | 송신 파트너 식별자 필수 사항입니다. |
| receiverQualifier | String | 수신 파트너 한정자 (필수) |
| receiverQualifier | String | 수신 파트너 식별자 필수 사항입니다. |
| agreementName | String | 메시지가 확인되는 X12 규약의 이름 (선택 사항) |
| direction | 열거형 | 메시지 흐름, 수신 또는 송신 방향 필수 사항입니다. |
| interchangeControlNumber | String | 교환 컨트롤 번호 (선택 사항) |
| functionalGroupControlNumber | String | 기능 컨트롤 번호 (선택 사항) |
| gsSegment | String | 메시지 GS 세그먼트 (선택 사항) |
| isTechnicalAcknowledgmentExpected | Boolean | X12 규약에서 기술 승인이 구성되었는지 여부 필수 사항입니다. |
| isFunctionalAcknowledgmentExpected | Boolean | X12 규약에서 기능 승인이 구성되었는지 여부 필수 사항입니다. |
| isMessageFailed | Boolean | X12 메시지 실패 여부 필수 사항입니다.|
| gs01 | String | 기능 식별자 코드 (선택 사항) |
| gs02 | String | 애플리케이션 보낸 사람 코드 (선택 사항) |
| gs03 | String | 애플리케이션 받는 사람 코드 (선택 사항) |
| gs04 | String | 기능 그룹 날짜 (선택 사항) |
| gs05 | String | 기능 그룹 시간 (선택 사항) |
| gs07 | String | 담당 에이전시 코드 (선택 사항) |
| gs08 | String | 버전/릴리스/산업 식별자 코드 (선택 사항) |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12 기능 그룹 승인 추적 스키마

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ak903": "",
      "ak904": "",
      "ak9Segment": ""
   }
}
```

| 자산 | Type | 설명 |
| --- | --- | --- |
| senderPartnerName | String | X12 메시지 보낸 사람의 파트너 이름 (선택 사항) |
| receiverPartnerName | String | X12 메시지 받는 사람의 파트너 이름 (선택 사항) |
| senderQualifier | String | 송신 파트너 한정자 필수 사항입니다. |
| senderIdentifier | String | 송신 파트너 식별자 필수 사항입니다. |
| receiverQualifier | String | 수신 파트너 한정자 (필수) |
| receiverQualifier | String | 수신 파트너 식별자 필수 사항입니다. |
| agreementName | String | 메시지가 확인되는 X12 규약의 이름 (선택 사항) |
| direction | 열거형 | 메시지 흐름, 수신 또는 송신 방향 필수 사항입니다. |
| interchangeControlNumber | String | 기술 승인이 파트너로부터 수신될 때 송신 쪽에 대해 입력되는 교환 컨트롤 번호 (선택 사항) |
| functionalGroupControlNumber | String | 기술 승인이 파트너로부터 수신될 때 송신 쪽에 대해 입력되는 기술 승인의 기능 그룹 컨트롤 번호 (선택 사항) |
| isaSegment | String | 교환 컨트롤 번호와 같으나 특정 경우에만 채워집니다. (선택 사항) |
| gsSegment | String | 기능 그룹 컨트롤 번호와 같으나 특정 경우에만 채워집니다. (선택 사항) |
| respondingfunctionalGroupControlNumber | String | 원래 기능 그룹의 컨트롤 번호 (선택 사항) |
| respondingFunctionalGroupId | String | 승인 기능 그룹 ID에서 AK101에 매핑됩니다. (선택 사항) |
| isMessageFailed | Boolean | X12 메시지 실패 여부 필수 사항입니다. |
| statusCode | 열거형 | 승인 상태 코드. 허용되는 값은 **Accepted**, **Rejected** 또는 **AcceptedWithErrors**입니다. 필수 사항입니다. |
| processingStatus | 열거형 | 승인의 처리 상태. 허용되는 값은 **Received**, **Generated** 및 **Sent**입니다. 필수 사항입니다. |
| ak903 | String | 수신된 트랜잭션 집합 수 (선택 사항) |
| ak904 | String | 식별된 기능 그룹에서 승인된 트랜잭션 집합 수 (선택 사항) |
| ak9Segment | String | AK1 세그먼트에 식별된 기능 그룹이 허용되는지 또는 거부되는지와 그 이유 (선택 사항) |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>B2B 프로토콜 추적 스키마

스키마를 추적하는 B2B 프로토콜에 대한 정보는 다음을 참조하세요.

* [AS2 추적 스키마](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [B2B 사용자 지정 추적 스키마](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>다음 단계

* [B2B 메시지 모니터링](logic-apps-monitor-b2b-message.md)에 대해 자세히 알아봅니다.
* 에 대 한 자세한 [Azure Monitor 로그에서 B2B 메시지 추적](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)합니다.
