---
title: 제안 상태 검색 | Microsoft Docs
description: API는 제안의 현재 상태를 검색합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 9233a5919ad86adcbb7947cd095945654ed015a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093984"
---
<a name="retrieve-offer-status"></a>제안 상태 검색 
=====================

제안의 현재 상태를 검색합니다.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>URI 매개 변수
--------------

|  **Name**       |   **설명**                            |  **데이터 형식** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | 게시자 식별자입니다(예: `Contoso`).  |     String     |
|  offerId        | 제안을 고유하게 식별하는 GUID입니다.      |     String     |
|  api-version    | 최신 버전 API                        |     Date       |
|  |  |


<a name="header"></a>헤더
------

|  이름           |  값               |
|  -------------  | -------------------  |
|  콘텐츠 형식   |  `application/json`  |
|  권한 부여  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>본문 예제
------------

### <a name="response"></a>response

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 day",
          "id": "displayprovision",
          "stepName": "Provisioning",
          "description": "Your virtual machine is being replicated in our production systems.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "displaypackage",
          "stepName": "Packaging and Lead Generation Registration",
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
      "notificationEmails": "jdoe@contoso.com"
  } 
```


### <a name="response-body-properties"></a>응답 본문 속성

|  **Name**             |    **설명**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | 제안의 상태입니다. 가능한 값 목록을 보려면 아래 [제안 상태](#offer-status)를 참조하세요. |
|  메시지의 최대 전달 수             | 제안과 연결된 메시지 배열                                                    |
|  단계                | 제안 게시 동안 제안이 거치는 단계의 배열입니다.                      |
|  estimatedTimeFrame   | 이 단계를 완료하는 데 걸리는 예상 시간(일반 형식)입니다.                       |
|  id                   | 단계의 식별자입니다.                                                                         |
|  stepName             | 단계의 이름입니다.                                                                               |
|  description          | 단계에 대한 설명입니다.                                                                        |
|  status               | 단계의 상태입니다. 가능한 값 목록을 보려면 아래의 [단계 상태](#step-status)를 참조하세요.    |
|  메시지의 최대 전달 수             | 단계와 관련된 메시지의 배열입니다.                                                          |
|  processPercentage    | 단계의 완료율입니다.                                                              |
|  previewLinks         | *현재 구현되지 않음*                                                                    |
|  liveLinks            | *현재 구현되지 않음*                                                                    |
|  notificationEmails   | 작업의 진행률 알림을 받을 이메일 주소의 쉼표로 구분된 목록입니다.        |
|  |  |


### <a name="response-status-codes"></a>응답 상태 코드

| **코드** |   **설명**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` - 요청이 성공적으로 처리되었으며 제안의 현재 상태가 반환되었습니다. |
|  400     | `Bad/Malformed request` - 오류 응답 본문에 자세한 정보가 들어 있을 수 있습니다.                 |
|  404     | `Not found` - 지정된 엔터티가 존재하지 않습니다.                                                |
|  |  |


### <a name="offer-status"></a>제안 상태

|  **Name**                    |    **설명**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | 제안이 게시된 적이 없습니다.                          |
|  NotStarted                  | 새 제안이지만 시작되지 않았습니다.                            |
|  WaitingForPublisherReview   | 제안이 게시자 승인을 기다리고 있습니다.                 |
|  실행 중                     | 제안 제출을 처리 중입니다.                     |
|  Succeeded                   | 제안 제출 처리를 완료했습니다.               |
|  Canceled                    | 제안 제출이 취소되었습니다.                           |
|  실패                      | 제안 제출에 실패했습니다.                                 |
|  |  |


### <a name="step-status"></a>단계 상태

|  **Name**                    |    **설명**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | 단계가 시작되지 않았습니다.                        |
|  InProgress                  | 단계를 실행 중입니다.                             |
|  WaitingForPublisherReview   | 단계가 게시자 승인을 기다리고 있습니다.      |
|  WaitingForApproval          | 단계가 프로세스 승인을 기다리고 있습니다.        |
|  Blocked                     | 단계가 차단되었습니다.                             |
|  거부됨                    | 단계가 거부되었습니다.                            |
|  완료                    | 단계가 완료되었습니다.                            |
|  Canceled                    | 단계가 취소되었습니다.                           |
|  |  |
