---
title: Webhook 구독자 인증 구성-Azure Event Grid IoT Edge | Microsoft Docs
description: 웹후크 구독자 인증 구성
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 101dcae5870322878cec48098f2efae32cc68c14
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841733"
---
# <a name="configure-webhook-subscriber-authentication"></a>웹후크 구독자 인증 구성

이 가이드는 Event Grid 모듈에 대해 가능한 webhook 구독자 구성의 예를 제공 합니다. 기본적으로 웹 후크 구독자에 대 한 HTTPS 엔드포인트만 수락 됩니다. 구독자가 자체 서명 된 인증서를 제공 하는 경우 Event Grid 모듈에서 거부 됩니다.

## <a name="allow-only-https-subscriber"></a>HTTPS 구독자만 허용

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>자체 서명 된 인증서를 사용 하 여 HTTPS 구독자 허용

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>일반적으로 자체 서명 된 인증서를 사용할 수 있는 것 처럼 테스트 환경 에서만 속성 `outbound__webhook__allowUnknownCA`를 `true`로 설정 합니다. 프로덕션 워크 로드의 경우 **false**로 설정 하는 것이 좋습니다.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>HTTPS 구독자를 허용 하지만 인증서 유효성 검사를 건너뜁니다.

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>인증 해야 하는 인증서를 제공 하지 않을 수 있으므로 테스트 환경 에서만 속성 `outbound__webhook__skipServerCertValidation`를 `true`로 설정 합니다. 프로덕션 워크 로드의 경우 **false** 로 설정 하는 것이 좋습니다.

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>자체 서명 된 인증서를 사용 하 여 HTTP 및 HTTPS 모두 허용

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>HTTP 구독자를 먼저 불러올 수 있으므로 속성 `outbound__webhook__httpsOnly`를 테스트 환경 에서만 `false`로 설정 합니다. 프로덕션 워크 로드의 경우 **true** 로 설정 하는 것이 좋습니다.
