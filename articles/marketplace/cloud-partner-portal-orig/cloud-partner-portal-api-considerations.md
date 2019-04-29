---
title: API 고려 사항 | Microsoft Docs
description: Marketplace API를 사용하는 경우의 버전 관리, 오류 처리 및 권한 부여 문제
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
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: c6cfb41cb6254145821ab3fef662e9a5e54f6298
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60625060"
---
<a name="api-considerations"></a>API 고려 사항
=================

<a name="api-versioning"></a>API 버전 관리
--------------

동시에 사용할 수 있는 여러 API 버전이 있을 수 있습니다. 클라이언트는 쿼리 문자열의 일부로 `api-version` 매개 변수를 제공하여 호출하려는 버전을 나타내야 합니다.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

알 수 없거나 잘못된 API 버전을 사용할 때의 요청에 대한 응답은 HTTP 코드 400입니다. 이 오류는 응답 본문의 알려진 API 버전 컬렉션을 반환합니다.

``` json
    {
        "error”: { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>오류
------

API는 해당 HTTP 상태 코드와 경우에 따라 JSON으로 serialize된 응답의 추가 정보를 사용하여 오류에 응답합니다.
오류가 수신되면(특히 400 클래스 오류) 기본 원인을 해결한 후에 요청을 다시 시도하세요. 예를 들어, 위의 샘플 응답에서 요청을 다시 보내기 전에 API 버전 매개 변수를 수정하세요.

<a name="authorization-header"></a>권한 부여 헤더
--------------------

이 참조의 모든 API에 대해 Azure AD(Azure Active Directory)에서 가져온 전달자 토큰과 함께 인증 헤더를 전달해야 합니다. 이 헤더는 유효한 응답을 수신하는 데 필요합니다. 이 헤더가 없으면 `401 Unauthorized` 오류가 반환됩니다. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
