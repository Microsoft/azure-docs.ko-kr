---
title: 제품 게시 | Microsoft Docs
description: 지정한 제품을 게시하는 API를 소개합니다.
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
ms.openlocfilehash: cb1293a771a137f4df7e36a2b412f68b384f16ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094368"
---
<a name="publish-an-offer"></a>제안 게시
================

지정한 제품의 게시 프로세스를 시작합니다. 이 호출은 장기 실행 작업입니다.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>URI 매개 변수
--------------

|  **Name**      |    **설명**                               |  **데이터 형식** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | 게시자 식별자입니다(예: `contoso`).      |   String       |
|  offerId       | 제안 식별자입니다.                                 |   String       |
|  api-version   | 최신 버전의 API입니다.                        |   Date         |
|  |  |


<a name="header"></a>헤더
------

|  **Name**        |    **값**          |
|  --------        |    ---------          |
|  콘텐츠 형식    | `application/json`    |
|  권한 부여   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>본문 예제
------------

### <a name="request"></a>요청

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>요청 본문 속성

|  **Name**               |   **설명**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  notification-emails    | 게시 작업의 진행률 알림을 받을 전자 메일 주소의 쉼표로 구분된 목록입니다. |
|  |  |


### <a name="response"></a>response

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>응답 헤더

|  **Name**             |    **값**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operation-Location    | 작업의 현재 상태를 확인하기 위해 쿼리할 수 있는 URL입니다.    |
|  |  |


### <a name="response-status-codes"></a>응답 상태 코드

| **코드** |  **설명**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` - 요청이 성공적으로 수락되었습니다. 응답에는 시작된 작업을 추적하는 데 사용할 수 있는 위치가 포함됩니다. |
| 400   | `Bad/Malformed request` - 오류 응답 본문이 자세한 정보를 제공할 수 있습니다.                                                               |
| 422   | `Un-processable entity` - 게시할 엔터티의 유효성 검사가 실패했음을 나타냅니다.                                                        |
| 404   | `Not found` - 클라이언트가 지정한 엔터티가 없습니다.                                                                              |
|  |  |
