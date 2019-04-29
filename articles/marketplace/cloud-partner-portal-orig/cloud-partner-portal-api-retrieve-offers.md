---
title: 제품 API 검색 | Microsoft Docs
description: API는 게시자 네임스페이스에서 요약된 제품 목록을 검색합니다.
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
ms.openlocfilehash: de9261548ec79e206b0db87caabc1fa4c9ad6771
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094508"
---
<a name="retrieve-offers"></a>제품 검색
===============

게시자 네임스페이스에서 요약된 제품 목록을 검색합니다.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>URI 매개 변수
--------------

| **Name**         |  **설명**                         |  **데이터 형식** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | 게시자 식별자입니다(예: `contoso`). |   String    |
|  api-version     | 최신 버전 API                    |    Date        |
|  |  |


<a name="header"></a>헤더
------

|  **Name**        |         **값**       |
|  --------------- |       ----------------  |
|  콘텐츠 형식    | `application/json`      |
|  권한 부여   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>본문 예제
------------

### <a name="response"></a>response

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>응답 본문 속성

|  **Name**       |       **설명**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | 제안의 형식을 식별합니다.                                                                                           |
|  publisherId    | 게시자를 고유하게 식별하는 식별자                                                                      |
|  status         | 제안의 상태입니다. 가능한 값 목록을 보려면 아래 [제안 상태](#offer-status)를 참조하세요.                         |
|  id             | 게시자 네임스페이스에서 제품을 고유하게 식별하는 GUID                                                    |
|  버전        | 제안의 현재 버전입니다. 버전 속성은 클라이언트가 수정할 수 없습니다. 각 게시 후에 증분됩니다. |
|  정의     | 워크로드 실제 정의에 대한 요약 보기를 포함합니다. 자세한 정의를 얻으려면 [특정 제안 검색](./cloud-partner-portal-api-retrieve-specific-offer.md) API를 사용하세요. |
|  changedTime    | 제품이 마지막으로 수정된 UTC 시간                                                                              |
|  |  |


### <a name="response-status-codes"></a>응답 상태 코드

| **코드**  |  **설명**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` - 요청이 성공적으로 처리되었으며 해당 게시자의 모든 제안이 클라이언트에 반환되었습니다.  |
|  400      | `Bad/Malformed request` - 오류 응답 본문에 자세한 정보가 들어 있을 수 있습니다.                                    |
|  403      | `Forbidden` - 클라이언트는 지정된 네임스페이스에 액세스할 수 없습니다.                                          |
|  404      | `Not found` - 지정된 엔터티가 존재하지 않습니다.                                                                 |
|  |  |


### <a name="offer-status"></a>제품 상태

|  **Name**                    | **설명**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | 제안이 게시된 적이 없습니다.                  |
|  NotStarted                  | 제안이 새 제안이지만 시작되지 않았습니다.                 |
|  WaitingForPublisherReview   | 제안이 게시자 승인을 기다리고 있습니다.         |
|  실행 중                     | 제안 제출을 처리 중입니다.             |
|  Succeeded                   | 제안 제출 처리를 완료했습니다.       |
|  Canceled                    | 제안 제출이 취소되었습니다.                   |
|  실패                      | 제안 제출에 실패했습니다.                         |
|  |  |
