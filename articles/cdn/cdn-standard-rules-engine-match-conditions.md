---
title: Azure CDN에 대한 표준 규칙 엔진의 일치 조건 | Microsoft Docs
description: Azure Content Delivery Network (Azure CDN)에 대한 표준 규칙 엔진의 일치 조건에 대한 참조 설명서입니다.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 425266e2a7ca42bb17ca598ddfc2f2b86591f32e
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900188"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Azure CDN에 대한 표준 규칙 엔진의 일치 조건

Azure Content Delivery Network (Azure CDN)에 대한 [표준 규칙 엔진](cdn-standard-rules-engine.md) 에서 규칙은 하나 이상의 일치 조건 및 동작으로 구성 됩니다. 이 문서에서는 Azure CDN에 대한 표준 규칙 엔진에서 사용할 수 있는 일치 조건에 대해 자세히 설명 합니다.

규칙의 첫 번째 부분은 일치 조건 또는 일치 조건 집합입니다. Azure CDN에 대한 표준 규칙 엔진에서 각 규칙에는 최대 4 개의 일치 조건이 있을 수 있습니다. 일치 조건은 정의 된 작업이 수행 되는 특정 유형의 요청을 식별 합니다. 여러 일치 조건을 사용 하는 경우 및 논리를 사용 하 여 일치 조건을 그룹화 합니다.

예를 들어 다음을 수행하는 데 일치 조건을 사용할 수 있습니다.

- 특정 IP 주소, 국가 또는 지역을 기준으로 요청을 필터링 합니다.
- 헤더 정보에 의한 필터 요청
- 모바일 장치 또는 데스크톱 장치에서 요청을 필터링 합니다.

## <a name="match-conditions"></a>일치 조건

Azure CDN에 대한 표준 규칙 엔진에서 다음 일치 조건을 사용할 수 있습니다. 

### <a name="device-type"></a>디바이스 유형 

모바일 장치 또는 데스크톱 장치에서 수행 된 요청을 식별 합니다.  

#### <a name="required-fields"></a>필수 필드

연산자 | 지원되는 값
---------|----------------
Equals, 같지 않음 | 모바일, 데스크톱

### <a name="http-version"></a>HTTP 버전

요청의 HTTP 버전에 따라 요청을 식별 합니다.

#### <a name="required-fields"></a>필수 필드

연산자 | 지원되는 값
---------|----------------
Equals, 같지 않음 | 2.0, 1.1, 1.0, 0.9, 모두

### <a name="request-cookies"></a>요청 쿠키입니다.

들어오는 요청의 쿠키 정보를 기반으로 요청을 식별 합니다.

#### <a name="required-fields"></a>필수 필드

쿠키 이름 | 연산자 | 쿠키 값 | 사례 변환
------------|----------|--------------|---------------
string | [표준 연산자 목록](#standard-operator-list) | 문자열, Int | 변환 없음, 대문자, 소문자

#### <a name="key-information"></a>주요 정보

- 쿠키 이름을 지정할 때 와일드 카드 값 (별표 (\*) 포함)을 사용할 수 없습니다. 정확한 쿠키 이름을 사용 해야 합니다.
- 이 일치 조건의 인스턴스당 하나의 쿠키 이름만 지정할 수 있습니다.
- 쿠키 이름 비교는 대/소문자를 구분하지 않습니다.
- 여러 쿠키 값을 지정 하려면 각 쿠키 값 사이에 단일 공백을 사용 합니다. 
- 쿠키 값은 와일드 카드 값을 사용할 수 있습니다.
- 와일드 카드 값을 지정 하지 않은 경우 정확히 일치 하는 항목만이 일치 조건을 충족 합니다. 예를 들어 "Value"는 "Value"와 일치 하지만 "Value1"과는 일치 하지 않습니다. 

### <a name="post-argument"></a>Post 인수

요청에 사용 되는 POST 요청 메서드에 대해 정의 된 인수를 기반으로 요청을 식별 합니다. 

#### <a name="required-fields"></a>필수 필드

인수 이름 | 연산자 | 인수 값 | 사례 변환
--------------|----------|----------------|---------------
string | [표준 연산자 목록](#standard-operator-list) | 문자열, Int | 변환 없음, 대문자, 소문자

### <a name="query-string"></a>쿼리 문자열

특정 쿼리 문자열 매개 변수를 포함 하는 요청을 식별 합니다. 이 매개 변수는 특정 패턴과 일치 하는 값으로 설정 됩니다. 요청 URL의 쿼리 문자열 매개 변수 (예: **parameter = value**)는이 조건이 충족 되는지 여부를 결정 합니다. 이 일치 조건은 쿼리 문자열 매개 변수를 이름으로 식별하고 매개 변수 값에 하나 이상의 값을 허용합니다.

#### <a name="required-fields"></a>필수 필드

연산자 | 쿼리 문자열 | 사례 변환
---------|--------------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 변환 없음, 대문자, 소문자

### <a name="remote-address"></a>원격 주소

요청자의 위치 또는 IP 주소에 따라 요청을 식별 합니다.

#### <a name="required-fields"></a>필수 필드

연산자 | 지원되는 값
---------|-----------------
모두 | N/A
지리적 일치 | 국가 코드
IP 일치 | IP 주소 (공백으로 구분)
모두 아님 | N/A
지리적으로 일치 하지 않음 | 국가 코드
IP 일치 하지 않음 | IP 주소 (공백으로 구분)

#### <a name="key-information"></a>주요 정보

- CIDR 표기법을 사용합니다.
- 여러 IP 주소와 IP 주소 블록을 지정 하려면 값 사이에 단일 공백을 사용 합니다.
  - **IPv4 예**: *1.2.3.4 10.20.30.40* 는 주소 1.2.3.4 또는 10.20.30.40에서 도착 하는 모든 요청을 일치 시킵니다.
  - **IPv6 예**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* 는 두 주소 1:2:3:4:5:6:7:8 또는 10:20:30:40:50:60:70:80에서 도착 하는 모든 요청을 일치 시킵니다.
- IP 주소 블록에 대한 구문은 뒤에 슬래시와 접두사 크기가 오는 기본 IP 주소입니다. 다음은 그 예입니다.
  - **IPv4 예**: *5.5.5.64/26* 은 요청과 일치를 통해 주소 5.5.5.64에서 도착 하는 모든 요청을 일치 시킵니다.
  - **IPv6 예**: *1:2:3:/48* 는 주소 1:2:3:0:0:0:0:0에서 1:2: 3: ffff: ffff: ffff: ffff: ffff에서 도착 하는 모든 요청과 일치 합니다.

### <a name="request-body"></a>요청 본문

요청의 본문에 표시 되는 특정 텍스트를 기준으로 요청을 식별 합니다.

#### <a name="required-fields"></a>필수 필드

연산자 | 요청 본문 | 사례 변환
---------|--------------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 변환 없음, 대문자, 소문자

### <a name="request-header"></a>요청 헤더

요청에서 특정 헤더를 사용 하는 요청을 식별 합니다.

#### <a name="required-fields"></a>필수 필드

헤더 이름 | 연산자 | 헤더 값 | 사례 변환
------------|----------|--------------|---------------
string | [표준 연산자 목록](#standard-operator-list) | 문자열, Int | 변환 없음, 대문자, 소문자

### <a name="request-method"></a>요청 메서드

지정 된 요청 메서드를 사용 하는 요청을 식별 합니다.

#### <a name="required-fields"></a>필수 필드

연산자 | 지원되는 값
---------|----------------
Equals, 같지 않음 | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>주요 정보

- GET 요청 메서드만 Azure CDN 캐시 된 콘텐츠를 생성할 수 있습니다. 다른 모든 요청 메서드는 네트워크를 통해 프록시 설정됩니다. 

### <a name="request-protocol"></a>요청 프로토콜

사용 된 지정 된 프로토콜을 사용 하는 요청을 식별 합니다.

#### <a name="required-fields"></a>필수 필드

연산자 | 지원되는 값
---------|----------------
Equals, 같지 않음 | HTTP, HTTPS

### <a name="request-url"></a>요청 URL

지정 된 URL과 일치 하는 요청을 식별 합니다.

#### <a name="required-fields"></a>필수 필드

연산자 | 요청 URL | 사례 변환
---------|-------------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 변환 없음, 대문자, 소문자

#### <a name="key-information"></a>주요 정보

- 이 규칙 조건을 사용 하는 경우 프로토콜 정보를 포함 해야 합니다. 예: *https://www.\<yourdomain\>.com* .

### <a name="url-file-extension"></a>URL 파일 확장명

요청 URL의 파일 이름에 지정 된 파일 확장명을 포함 하는 요청을 식별 합니다.

#### <a name="required-fields"></a>필수 필드

연산자 | 확장 | 사례 변환
---------|-----------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 변환 없음, 대문자, 소문자

#### <a name="key-information"></a>주요 정보

- 확장의 경우 선행 기간을 포함 하지 않습니다. 예를 들어 *.html*대신 *html* 을 사용 합니다.

### <a name="url-file-name"></a>URL 파일 이름

요청 URL에 지정 된 파일 이름을 포함 하는 요청을 식별 합니다.

#### <a name="required-fields"></a>필수 필드

연산자 | 파일 이름 | 사례 변환
---------|-----------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 변환 없음, 대문자, 소문자

#### <a name="key-information"></a>주요 정보

- 여러 파일 이름을 지정 하려면 각 파일 이름을 하나의 공백으로 구분 합니다. 

### <a name="url-path"></a>URL 경로

요청 URL에 지정 된 경로를 포함 하는 요청을 식별 합니다.

#### <a name="required-fields"></a>필수 필드

연산자 | Value | 사례 변환
---------|-------|---------------
[표준 연산자 목록](#standard-operator-list) | 문자열, Int | 변환 없음, 대문자, 소문자

#### <a name="key-information"></a>주요 정보

- 파일 이름 값은 와일드 카드 값을 사용할 수 있습니다. 예를 들어 각각의 파일 이름 패턴은 하나 이상의 별표(*)로 구성될 수 있으며 각 별표는 하나 이상의 문자 시퀀스와 일치합니다.

## <a name="reference-for-rules-engine-match-conditions"></a>규칙 엔진 일치 조건에 대한 참조

### <a name="standard-operator-list"></a>표준 연산자 목록

표준 연산자 목록의 값을 허용 하는 규칙의 경우 다음 연산자를 사용할 수 있습니다.

- 모두
- 같음 
- contains 
- 시작 문자 
- 다음으로 끝 
- 보다 작음
- 작거나 같음
- 보다 큼
- 크거나 같음
- 모두 아님
- 포함하지 않음
- 다음으로 시작 안 함 
- 다음으로 끝나지 않음 
- 보다 작지 않음
- 작거나 같음
- 보다 크지 않음
- 작거나 같음

*보다 작거나* *같음*과 같은 숫자 연산자의 경우 사용 되는 비교는 길이를 기준으로 합니다. 이 경우 일치 조건의 값은 비교 하려는 길이와 같은 정수 여야 합니다. 

## <a name="next-steps"></a>다음 단계

- [Azure CDN 개요](cdn-overview.md)
- [표준 규칙 엔진 참조](cdn-standard-rules-engine-reference.md)
- [표준 규칙 엔진의 작업](cdn-standard-rules-engine-actions.md)
- [표준 규칙 엔진을 사용 하 여 HTTPS 적용](cdn-standard-rules-engine.md)
