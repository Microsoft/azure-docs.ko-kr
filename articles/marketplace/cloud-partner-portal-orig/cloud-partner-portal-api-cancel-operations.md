---
title: 작업 취소 API | Microsoft Docs
description: 작업을 취소합니다.
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
ms.openlocfilehash: 18f00391beded0744c80eab73bb1efe1c6ab8dbc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60625043"
---
<a name="cancel-operation"></a>작업 취소 
=================

이 API는 제안에 대해 현재 진행 중인 작업을 취소합니다. [작업 검색 API](./cloud-partner-portal-api-retrieve-operations.md)를 사용하여 이 API에 전달할 `operationId`를 가져옵니다. 일반적으로 취소는 동기 작업이지만, 일부 복잡한 시나리오에서는 기존 작업을 취소하기 위해 새 작업이 필요할 수 있습니다. 이 경우 HTTP 응답 본문에는 상태 쿼리에 사용되는 작업 위치가 포함됩니다.

요청에 쉼표로 구분된 이메일 주소 목록을 제공할 수 있으며, API는 이러한 주소에 작업의 진행 상태를 알립니다.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>URI 매개 변수
--------------

|  **Name**    |      **설명**                                  |    **데이터 형식**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  게시자 식별자(예: `contoso`)입니다.         |   String          |
| offerId      |  제안 식별자입니다.                                     |   String          |
| api-version  |  API의 현재 버전입니다.                               |    Date           |
|  |  |  |


<a name="header"></a>헤더
------

|  **Name**              |  **값**         |
|  ---------             |  ----------        |
|  콘텐츠 형식          |  application/json  |
|  권한 부여         |  전달자 사용자 토큰 |
|  |  |


<a name="body-example"></a>본문 예제
------------

### <a name="request"></a>요청

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>요청 본문 속성

|  **Name**                |  **설명**                                               |
|  --------                |  ---------------                                               |
|  notification-emails     | 게시 작업의 진행률 알림을 받을 이메일 ID의 쉼표로 구분된 목록입니다. |
|  |  |


### <a name="response"></a>response

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>응답 헤더

|  **Name**             |    **값**                       |
|  ---------            |    ----------                      |
| Operation-Location    | 작업의 현재 상태를 확인하기 위해 쿼리할 수 있는 URL입니다. |
|  |  |


### <a name="response-status-codes"></a>응답 상태 코드

| **코드**  |  **설명**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | 정상. 요청이 성공적으로 처리되었으며 작업이 동기적으로 취소됩니다. |
|  202      | 수락됨 요청이 성공적으로 처리되었으며 작업 처리가 진행 중입니다. 취소 작업의 위치가 응답 헤더에 반환됩니다. |
|  400      | 잘못된 형식의 요청입니다. 오류 응답 본문이 자세한 정보를 제공할 수 있습니다.  |
|  403      | 액세스 권한 없음 클라이언트는 요청에 지정된 네임스페이스에 대해 액세스 권한이 없습니다. |
|  404      | 찾을 수 없음 지정된 엔터티가 존재하지 않습니다. |
|  |  |
