---
title: Go Live | Microsoft Docs
description: Go Live API는 제품 라이브 목록 추가 프로세스를 시작합니다.
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
ms.openlocfilehash: ea3f26d70c4a4ce07c988612890687504a4cf5ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60624975"
---
<a name="go-live"></a>Go Live
=======

이 API는 앱을 프로덕션 환경으로 푸시하는 프로세스를 시작합니다. 이 작업은 대개 오랫동안 실행됩니다. 이 호출에서는 [게시](./cloud-partner-portal-api-publish-offer.md) API 작업의 알림 전자 메일 목록을 사용합니다.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>URI 매개 변수
--------------

|  **Name**      |   **설명**                                                           | **데이터 형식** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | 검색할 제품의 게시자 식별자입니다(예: `contoso`).       |  String       |
| offerId        | 검색할 제품의 제품 식별자입니다.                                   |  String       |
| api-version    | 최신 버전의 API입니다.                                                   |  Date         |
|  |  |  |


<a name="header"></a>헤더
------

|  **Name**       |     **값**       |
|  ---------      |     ----------      |
| 콘텐츠 형식    | `application/json`  |
| 권한 부여   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>본문 예제
------------

### <a name="response"></a>response

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>응답 헤더

|  **Name**             |      **값**                                                            |
|  --------             |      ----------                                                           |
| Operation-Location    |  작업의 현재 상태를 확인하기 위해 쿼리할 URL입니다.            |
|  |  |


### <a name="response-status-codes"></a>응답 상태 코드

| **코드** |  **설명**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` - 요청이 성공적으로 수락되었습니다. 응답에는 작업 상태 추적을 위한 위치가 포함됩니다. |
|  400     | `Bad/Malformed request` -응답 본문 내에서 추가 오류 정보가 확인되었습니다. |
|  404     |  `Not found` - 지정한 엔터티가 없습니다.                                       |
|  |  |
