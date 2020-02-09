---
title: 보안 및 인증-Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge에서 Event Grid의 보안 및 인증.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5dfa17fd702b76e2cfaa7a91066dbc6749c1069e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844516"
---
# <a name="security-and-authentication"></a>보안 및 인증

보안 및 인증은 고급 개념 이며 먼저 Event Grid 기본 사항에 대해 잘 알고 있어야 합니다. IoT Edge에서 Event Grid 처음 접하는 경우 [여기서](concepts.md) 시작 하세요. Event Grid 모듈은 IoT Edge의 기존 보안 인프라를 기반으로 합니다. 자세한 내용과 설정은 [이 설명서](../../iot-edge/security.md) 를 참조 하세요.

다음 섹션에서는 이러한 설정을 보호 하 고 인증 하는 방법에 대해 자세히 설명 합니다.

* TLS 구성
* 인바운드 클라이언트 인증
* 아웃 바운드 서버 인증
* 아웃 바운드 클라이언트 인증

>[!IMPORTANT]
>모듈 보안 및 인증 Event Grid IoT Edge에서 사용할 수 있는 기존 인프라를 활용 합니다. IoT Edge 하위 시스템의 보안을 유지 한다고 가정 합니다.

>[!IMPORTANT]
>Event Grid 구성은 **기본적으로 안전**합니다. 다음 하위 섹션에서는 인증의 측면을 재정의 하는 데 사용할 수 있는 모든 옵션 및 가능한 값에 대해 설명 합니다. 변경을 수행 하기 전에 영향을 이해 합니다. 변경 내용이 적용 되려면 Event Grid 모듈을 다시 배포 해야 합니다.

## <a name="tls-configuration-aka-server-authentication"></a>TLS 구성 (a. k a 서버 인증)

Event Grid 모듈은 HTTP 및 HTTPS 엔드포인트을 모두 호스팅합니다. 모든 IoT Edge 모듈에는 IoT Edge 보안 데몬에 의해 서버 인증서가 할당 됩니다. 서버 인증서를 사용 하 여 엔드포인트을 보호 합니다. 만료 시 모듈은 IoT Edge 보안 디먼의 새 인증서를 사용 하 여 자동으로 새로 고쳐집니다.

기본적으로 HTTPS 통신만 허용 됩니다. **Inbound__serverAuth__tlsPolicy** 구성을 통해이 동작을 재정의할 수 있습니다. 다음 표에서는이 속성의 가능한 값을 캡처합니다.

| 가능한 값 | Description |
| ---------------- | ------------ |
| 엄격 | 기본값 HTTPS만 사용
| 사용 | HTTP와 HTTPS를 모두 사용 하도록 설정 합니다.
| 사용 안 함 | HTTP만 사용

## <a name="inbound-client-authentication"></a>인바운드 클라이언트 인증

클라이언트는 관리 및/또는 런타임 작업을 수행 하는 엔터티입니다. 클라이언트는 다른 IoT Edge 모듈, 비 IoT 응용 프로그램 일 수 있습니다.

Event Grid 모듈은 두 가지 유형의 클라이언트 인증을 지원 합니다.

* 공유 액세스 서명 (SAS) 키 기반
* 인증서 기반

기본적으로 Event Grid 모듈은 인증서 기반 인증만 허용 하도록 구성 되어 있습니다. 시작 시 Event Grid 모듈은 IoT Edge 보안 디먼에서 "TrustBundle"을 검색 하 고이를 사용 하 여 클라이언트 인증서의 유효성을 검사 합니다. 이 체인으로 확인 되지 않는 클라이언트 인증서는 `UnAuthorized`거부 됩니다.

### <a name="certificate-based-client-authentication"></a>인증서 기반 클라이언트 인증

인증서 기반 인증은 기본적으로 설정 되어 있습니다. **Inbound__clientAuth__clientCert__enabled**속성을 통해 인증서 기반 인증을 사용 하지 않도록 선택할 수 있습니다. 다음 표에서는 가능한 값을 캡처합니다.

| 가능한 값 | Description |
| ----------------  | ------------ |
| true | 기본값 클라이언트 인증서를 제공 하려면 모든 요청이 Event Grid 모듈에 있어야 합니다. 또한 **inbound__clientAuth__clientCert__source**를 구성 해야 합니다.
| false | 클라이언트에 인증서를 강제로 표시 하지 않습니다.

다음 표에서는 **inbound__clientAuth__clientCert__source** 에 대 한 가능한 값을 캡처합니다.

| 가능한 값 | Description |
| ---------------- | ------------ |
| IoT Edge | 기본값 IoT Edge의 Trustbundle을 사용 하 여 모든 클라이언트 인증서의 유효성을 검사 합니다.

클라이언트에서 자체 서명 된 경우 기본적으로 Event Grid 모듈은 이러한 요청을 거부 합니다. **Inbound__clientAuth__clientCert__allowUnknownCA** 속성을 통해 자체 서명 된 클라이언트 인증서를 허용 하도록 선택할 수 있습니다. 다음 표에서는 가능한 값을 캡처합니다.

| 가능한 값 | Description |
| ----------------  | ------------|
| true | 기본값 자체 서명 된 인증서를 성공적으로 표시할 수 있습니다.
| false | 자체 서명 된 인증서가 표시 되 면 요청이 실패 합니다.

>[!IMPORTANT]
>프로덕션 시나리오에서는 **inbound__clientAuth__clientCert__allowUnknownCA** 을 **false**로 설정 하는 것이 좋습니다.

### <a name="sas-key-based-client-authentication"></a>SAS 키 기반 클라이언트 인증

인증서 기반 인증 외에도 Event Grid 모듈은 SAS 키 기반 인증을 수행할 수 있습니다. SAS 키는 들어오는 모든 호출의 유효성을 검사 하는 데 사용 해야 하는 Event Grid 모듈에 구성 된 암호와 같습니다. 클라이언트는 HTTP 헤더 ' aaaaas-키 '에서 암호를 지정 해야 합니다. `UnAuthorized` 일치 하지 않으면 요청이 거부 됩니다.

SAS 키 기반 인증을 제어 하는 구성이 **inbound__clientAuth__sasKeys__enabled**되었습니다.

| 가능한 값 | Description  |
| ----------------  | ------------ |
| true | SAS 키 기반 인증을 허용 합니다. **Inbound__clientAuth__sasKeys__key1** 또는 **inbound__clientAuth__sasKeys__key2** 필요
| false | 기본값 SAS 키 기반 인증을 사용할 수 없습니다.

 **inbound__clientAuth__sasKeys__key1** 및 **inbound__clientAuth__sasKeys__key2** 는 들어오는 요청에 대해 확인 하도록 Event Grid 모듈을 구성 하는 키입니다. 하나 이상의 키를 구성 해야 합니다. 요청을 만드는 클라이언트는 키를 요청 헤더 '**aeg: sas-키**'의 일부로 제공 해야 합니다. 두 키가 모두 구성 된 경우 클라이언트는 키 중 하나를 제공할 수 있습니다.

> [!NOTE]
>두 인증 방법을 모두 구성할 수 있습니다. 이러한 경우 SAS 키를 먼저 확인 하 고, 실패 한 경우에만 인증서 기반 인증을 수행 합니다. 요청이 성공 하려면 인증 방법 중 하나만 성공 해야 합니다.

## <a name="outbound-client-authentication"></a>아웃 바운드 클라이언트 인증

아웃 바운드 컨텍스트의 클라이언트는 Event Grid 모듈을 참조 합니다. 수행 중인 작업에서 구독자로 이벤트를 전달 하 고 있습니다. 구독 모듈은 서버로 간주 됩니다.

모든 IoT Edge 모듈에는 IoT Edge 보안 데몬에 의해 Id 인증서가 할당 됩니다. 보내는 호출에 id 인증서를 사용 합니다. 만료 시 모듈은 IoT Edge 보안 디먼의 새 인증서를 사용 하 여 자동으로 새로 고쳐집니다.

아웃 바운드 클라이언트 인증을 제어 하는 구성이 **outbound__clientAuth__clientCert__enabled**되었습니다.

| 가능한 값 | Description |
| ----------------  | ------------ |
| true | 기본값 Event Grid 모듈의 모든 나가는 요청이 인증서를 제공 해야 합니다. **Outbound__clientAuth__clientCert__source**를 구성 해야 합니다.
| false | Event Grid 모듈에서 인증서를 제공 하지 않아도 됩니다.

인증서의 소스를 제어 하는 구성이 **outbound__clientAuth__clientCert__source**됩니다.

| 가능한 값 | Description |
| ---------------- | ------------ |
| IoT Edge | 기본값 IoT Edge 보안 데몬에 의해 구성 된 모듈의 id 인증서를 사용 합니다.

### <a name="outbound-server-authentication"></a>아웃 바운드 서버 인증

Event Grid 구독자의 대상 유형 중 하나는 "Webhook"입니다. 기본적으로 이러한 구독자에 대 한 HTTPS 엔드포인트만 허용 됩니다.

Webhook 대상 정책 **outbound__webhook__httpsOnly**을 제어 하기 위한 구성입니다.

| 가능한 값 | Description |
| ----------------  | ------------ |
| true | 기본값 HTTPS 엔드포인트을 사용 하는 구독자만 허용 합니다.
| false | HTTP 또는 HTTPS 엔드포인트을 사용 하 여 구독자를 허용 합니다.

기본적으로 Event Grid 모듈은 구독자의 서버 인증서의 유효성을 검사 합니다. **Outbound__webhook__skipServerCertValidation**를 재정의 하 여 유효성 검사를 건너뛸 수 있습니다. 가능한 값은 다음과 같습니다.

| 가능한 값 | Description |
| ----------------  | ------------ |
| true | 구독자의 서버 인증서의 유효성을 검사 하지 않습니다.
| false | 기본값 구독자의 서버 인증서 유효성을 검사 합니다.

구독자의 인증서가 자체 서명 된 경우 기본적으로 Event Grid 모듈은 이러한 구독자를 거부 합니다. 자체 서명 된 인증서를 허용 하기 위해 **outbound__webhook__allowUnknownCA**를 재정의할 수 있습니다. 다음 표에서는 가능한 값을 캡처합니다.

| 가능한 값 | Description |
| ----------------  | ------------ |
| true | 기본값 자체 서명 된 인증서를 성공적으로 표시할 수 있습니다.
| false | 자체 서명 된 인증서가 표시 되 면 요청이 실패 합니다.

>[!IMPORTANT]
>프로덕션 시나리오에서는 **outbound__webhook__allowUnknownCA** 을 **false**로 설정 해야 합니다.

> [!NOTE]
>IoT Edge 환경은 자체 서명 된 인증서를 생성 합니다. 프로덕션 작업에 대해 권한 있는 Ca에서 발급 한 인증서를 생성 하 고 인바운드 및 아웃 바운드 모두에 대해 **allowUnknownCA** 속성을 **false**로 설정 하는 것이 좋습니다.

## <a name="summary"></a>요약

Event Grid 모듈은 **기본적으로 안전**합니다. 프로덕션 배포에 대해 이러한 기본값을 유지 하는 것이 좋습니다.

다음은를 구성 하는 동안 사용 하는 지침 원칙입니다.

* 모듈에 대 한 HTTPS 요청만 허용 합니다.
* 인증서 기반 클라이언트 인증만 허용 합니다. 잘 알려진 Ca에서 발급 한 인증서만 허용 합니다. 자체 서명 된 인증서를 허용 하지 않습니다.
* SASKey 기반 클라이언트 인증을 허용 하지 않습니다.
* 나가는 호출에 Event Grid 모듈의 id 인증서를 항상 제공 합니다.
* Webhook 대상 유형에 대해서는 HTTPS 구독자만 허용 합니다.
* Webhook 대상 유형에 대해 항상 구독자의 서버 인증서의 유효성을 검사 합니다. 잘 알려진 Ca에서 발급 한 인증서만 허용 합니다. 자체 서명 된 인증서를 허용 하지 않습니다.

기본적으로 Event Grid 모듈은 다음 구성을 사용 하 여 배포 됩니다.

 ```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```
