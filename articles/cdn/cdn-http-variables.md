---
title: Azure CDN 규칙 엔진에 대한 HTTP 변수 | Microsoft Docs
description: HTTP 변수를 사용하면 HTTP 요청 및 응답 메타데이터를 검색할 수 있습니다.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 8d4fc5fbdc3185c46f00d94537b197ec03f66755
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709923"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Azure CDN 규칙 엔진에 대한 HTTP 변수
HTTP 변수는 HTTP 요청 및 응답 메타데이터를 검색할 수 있는 수단을 제공합니다. 이 메타데이터는 요청 또는 응답을 동적으로 변경하는 데 사용할 수 있습니다. HTTP 변수의 사용은 다음 규칙 엔진 기능으로 제한됩니다.

- [Cache-Key 다시 쓰기](cdn-rules-engine-reference-features.md#cache-key-rewrite)
- [클라이언트 요청 헤더 수정](cdn-rules-engine-reference-features.md#modify-client-request-header)
- [클라이언트 응답 헤더 수정](cdn-rules-engine-reference-features.md#modify-client-response-header)
- [URL 리디렉션](cdn-rules-engine-reference-features.md#url-redirect)
- [URL 다시 쓰기](cdn-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>정의
다음 표에 지원되는 HTTP 변수가 설명되어 있습니다. 지역 메타데이터(예: 우편 번호)를 특정 요청에 대해 사용할 수 없는 경우에는 빈 값이 반환됩니다.


| 이름 | 변수 | 설명 | 샘플 값 |
| ---- | -------- | ----------- | ------------ |
| ASN(요청자) | %{geo_asnum} | 요청자의 AS 번호를 나타냅니다. <br /><br />**사용되지 않음:** %{virt_dst_asnum}. <br />이 변수는 %{geo_asnum}에 대해 사용되지 않습니다. 이 사용되지 않는 변수를 사용하는 규칙을 계속 사용할 수 있더라도, 새 변수를 사용하도록 업데이트해야 합니다. | AS15133 |
| 도시(요청자) | %{geo_city} | 요청자의 도시를 나타냅니다. | 로스앤젤레스 |
| 대륙(요청자) | %{geo_continent} | 해당 약어를 통해 요청자의 대륙을 나타냅니다. <br />유효한 값은 다음과 같습니다. <br />AF: 아프리카<br />AS: 아시아<br />EU: 유럽<br />NA: 북아메리카<br />OC: 오세아니아<br />SA: 남미<br /><br />**사용되지 않음:** %{virt_dst_continent}. <br />% {Geo_continent (를) 대신이 변수는 사용 되지 합니다. <br />이 사용되지 않는 변수를 사용하는 규칙을 계속 사용할 수 있더라도, 새 변수를 사용하도록 업데이트해야 합니다.| N/A |
| 쿠키 값 | %{cookie_Cookie} | 쿠키 용어에 의해 식별된 쿠키 키에 해당하는 값을 반환합니다. | 사용법 예제: <br />%{cookie__utma}<br /><br />샘플 값:<br />111662281.2.10.1222100123 |
| 국가(요청자) | %{geo_country} | 해당 국가 코드를 통해 요청자의 국가를 나타냅니다. <br />**사용되지 않음:** %{virt_dst_country}. <br /><br />이 변수는 %{geo_country}에 대해 사용되지 않습니다. 이 사용되지 않는 변수를 사용하는 규칙을 계속 사용할 수 있더라도, 새 변수를 사용하도록 업데이트해야 합니다. | US |
| 지정된 시장 지역(요청자) | %{geo_dma_code} |해당 지역 코드를 통해 요청자의 미디어 시장을 나타냅니다. <br /><br />이 필드는 미국에서 발생하는 요청에만 적용됩니다.| 745 |
| HTTP 요청 메서드 | %{request_method} | HTTP 요청 메서드를 나타냅니다. | GET |
| HTTP 상태 코드 | %{status} | 응답에 대한 HTTP 상태 코드를 나타냅니다. | 200 |
| IP 주소(요청자) | %{virt_dst_addr} | 요청자의 IP 주소를 나타냅니다. | 192.168.1.1 |
| 위도(요청자) | %{geo_latitude} | 요청자의 위도를 나타냅니다. | 34.0995 |
| 경도(요청자) | %{geo_longitude} | 요청자의 경도를 나타냅니다. | -118.4143 |
| 대도시 통계 지역(요청자) | %{geo_metro_code} | 요청자의 대도시 지역을 나타냅니다. <br /><br />이 필드는 미국에서 발생하는 요청에만 적용됩니다.<br />| 745 |
| 포트(요청자) | %{virt_dst_port} | 요청자의 임시 포트를 나타냅니다. | 55885 |
| 우편 번호(요청자) | %{geo_postal_code} | 요청자의 우편 번호를 나타냅니다. | 90210 |
| 쿼리 문자열 있음 | %{is_args} | 이 변수에 대한 값은 요청에 쿼리 문자열이 포함되어 있는지 여부에 따라 달라집니다.<br /><br />- 쿼리 문자열 있음: ?<br />-쿼리 문자열: NULL | ? |
| 쿼리 문자열 매개 변수 있음 | %{is_amp} | 이 변수에 대한 값은 요청에 최소한 하나의 쿼리 문자열 매개 변수가 포함되어 있는지 여부에 따라 달라집니다.<br /><br />- 매개 변수 있음: &<br />-매개 변수: NULL | & |
| 쿼리 문자열 매개 변수 값 | %{arg_&lt;parameter&gt;} | &lt;매개 변수&gt; 용어에 의해 식별되는 쿼리 문자열 매개 변수에 해당하는 값을 반환합니다. | 사용법 예제: <br />%{arg_language}<br /><br />샘플 쿼리 문자열 매개 변수: <br />?language=en<br /><br />샘플 값: en |
| 쿼리 문자열 값 | %{query_string} | 요청 URL에서 정의된 전체 쿼리 문자열 값을 나타냅니다. |key1=val1&key2=val2&key3=val3 |
| 참조자 도메인 | %{referring_domain} | 참조 페이지 요청 헤더에 정의 된 도메인을 나타냅니다. | <www.google.com> |
| 지역(요청자) | %{geo_region} | 영숫자의 약어를 통해 요청자의 지역(예: 시/도)을 나타냅니다. | CA |
| 요청 헤더 값 | %{http_RequestHeader} | RequestHeader 용어에 의해 식별되는 요청 헤더에 해당하는 값을 반환합니다. <br /><br />요청 헤더의 이름에 대시가 포함되는 경우(예: User-Agent) 밑줄로 바꿉니다(예: User_Agent).| 사용법 예제: %{http_Connection}<br /><br />샘플 값: 연결 유지 | 
| 요청 호스트 | %{host} | 요청 URL에 정의된 호스트를 나타냅니다. | <www.mydomain.com> |
| 요청 프로토콜 | %{request_protocol} | 요청의 프로토콜을 나타냅니다. | HTTP/1.1 |
| 요청 스키마 | %{scheme} | 요청 스키마를 나타냅니다. |http |
| 요청 URI(상대값) | %{request_uri} | 요청 URI에 정의된 쿼리 문자열을 포함하는 상대 경로를 나타냅니다. | /marketing/foo.js?loggedin=true |
| 요청 URI(쿼리 문자열을 사용하지 않는 상대값) | %{uri} | 요청된 콘텐츠에 대한 상대 경로를 나타냅니다. <br /><br/>주요 정보:<br />- 이 상대 경로는 쿼리 문자열을 제외합니다.<br />- 이 상대 경로는 URL 다시 작성을 반영합니다. URL은 다음과 같은 조건에서 다시 작성됩니다.<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL 다시 쓰기 기능: 이 기능은 요청 URI에서에서 정의 된 상대 경로 다시 작성 합니다.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;에 지 CNAME URL: 이 유형의 요청은 해당 CDN URL로 다시 작성 됩니다. |/800001/corigin/rewrittendir/foo.js |
| 요청 URI | %{request} | 요청을 설명합니다. <br />구문 &lt;HTTP 메서드&gt; &lt;상대 경로&gt; &lt;HTTP 프로토콜&gt; | /marketing/foo.js?loggedin=true HTTP/1.1 가져오기 |
| 응답 헤더 값 | %{resp_&lt;ResponseHeader&gt;} | &lt;ResponseHeader&gt; 용어에 의해 식별되는 응답 헤더에 해당하는 값을 반환합니다. <br /><br />응답 헤더의 이름에 대시가 포함되는 경우(예: User-Agent) 밑줄로 바꿉니다(예: User_Agent). | 사용법 예제: %{resp_Content_Length}<br /><br />샘플 값: 100 |

## <a name="usage"></a>사용 현황
다음 표에서 HTTP 변수를 지정하는 데 적절한 구문을 설명합니다.


| 구문 | 예 | 설명 |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | 이 구문을 사용하여 지정된 &lt;HTTPVariable&gt;에 해당하는 전체 값을 가져옵니다. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | 이 구문을 사용하여 지정된 &lt;HTTPVariableDelimiter&gt;에 해당하는 전체 값에 관한 사례를 설정합니다. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | &lt;HTTPVariableDelimiterExpression&gt;에 대한 정규식을 사용하여 HTTP 변수의 값을 바꾸기, 삭제 또는 조작합니다. |

HTTP 변수 이름은 영문자 및 밑줄만 지원합니다. 지원되지 않는 문자는 밑줄로 변환합니다.

## <a name="delimiter-reference"></a>구분 기호 참조
구분 기호는 다음과 같은 효과를 수행하기 위해 HTTP 변수 뒤에 지정할 수 있습니다.

- 변수와 연결된 값을 변환합니다.

     예제: 전체 값을 소문자로 변환합니다.

- 변수와 연결된 값을 삭제합니다.

- 변수와 연결된 값을 조작합니다.

     예제: HTTP 변수를 사용 하 여 연결 된 값을 변경 하려면 정규식을 사용 합니다.

구분 기호는 다음 표에 설명되어 있습니다.

| 구분 기호 | 설명 |
| --------- | ----------- |
| := | 다음 중 하나인 경우 기본값이 변수에 할당될 것을 나타냅니다. <br />- 누락됨 <br />- NULL로 설정됨. |
| :+ | 값이 할당된 경우 기본값이 변수에 할당될 것을 나타냅니다. |
| : | 변수에 할당된 값의 하위 문자열이 확장될 것을 나타냅니다. |
| # | 변수와 연결된 값의 시작 부분에서 발견되면 이 구분 기호 뒤에 지정된 패턴을 삭제해야 함을 나타냅니다. |
| % | 변수와 연결된 값의 끝 부분에서 발견되면 이 구분 기호 뒤에 지정된 패턴을 삭제해야 함을 나타냅니다. <br />이 정의는 % 기호가 구분 기호로 사용되는 경우에만 적용됩니다. |
| / | HTTP 변수 또는 패턴을 구분합니다. |
| // | 지정된 패턴의 모든 인스턴스를 찾아서 바꿉니다. |
| /= | 지정된 패턴의 모든 발생을 찾고, 복사하고, 다시 작성합니다. |
| . | HTTP 변수와 연결된 값을 소문자로 변환합니다. |
| ^ | HTTP 변수와 연결된 값을 대문자로 변환합니다. |
| ,, | HTTP 변수와 연결된 값에서 지정된 문자의 모든 인스턴스를 소문자로 변환합니다. |
| ^^ | HTTP 변수와 연결된 값에서 지정된 문자의 모든 인스턴스를 대문자로 변환합니다. |

## <a name="exceptions"></a>예외
다음 표에서 지정된 텍스트는 HTTP 변수로 처리되지 않는 상황을 설명합니다.

| 조건 | 설명 | 예 |
| --------- | ----------- | --------|
| % 기호 이스케이프 | 백슬래시를 사용하여 백분율 기호를 이스케이프할 수 있습니다. <br />오른쪽의 샘플 값은 HTTP 변수가 아닌 리터럴 값으로 처리됩니다.| \%{host} |
| 알 수 없는 변수 | 빈 문자열은 항상 알 수 없는 변수에 대해 반환됩니다. | %{unknownvariable} |
| 잘못된 문자 또는 구문 | 잘못된 문자 또는 구문을 포함하는 변수는 리터럴 값으로 처리됩니다. <br /><br />예제 #1: 지정된 된 값에 잘못 된 문자 (예를 들어,-). <br /><br />예제 #2: 지정된 된 값에는 이중 중괄호 집합을 포함합니다. <br /><br />예제 #3: 지정된 된 값에 닫는 중괄호를 누락 되었습니다.<br /> | 예제 #1: %{resp_user-agent} <br /><br />예제 #2: %{{host}} <br /><br />예제 #3: %{host |
| 변수 이름 누락 | 변수를 지정하지 않은 경우 항상 NULL 값이 반환됩니다. | %{} |
| 후행 문자 | 변수를 추적하는 문자는 리터럴 값으로 처리됩니다. <br />오른쪽의 샘플 값에는 리터럴 값으로 처리되는 추적 중괄호가 포함되어 있습니다. | %{host}} |

## <a name="setting-default-header-values"></a>기본 헤더 값 설정
다음 조건 중 하나라도 충족할 경우 기본값을 헤더에 할당할 수 있습니다.
- 누락/설정 해제
- NULL로 설정됨.

다음 표에서 기본값을 정의하는 방법을 설명합니다.

| 조건 | 구문 | 예 | 설명 |
| --------- | ------ | --------| ----------- |
| 다음 조건 중 하나라도 충족할 경우 헤더를 기본값으로 설정합니다. <br /><br />- 헤더 누락 <br /><br />- 헤더 값이 NULL로 설정됨.| %{Variable:=Value} | %{http_referer:=unspecified} | 누락되었거나 NULL로 설정한 경우에만 참조자 헤더가 *unspecified*로 설정됩니다. 설정된 경우 아무 작업도 수행되지 않습니다. |
| 누락된 경우 헤더를 기본값으로 설정합니다. | %{Variable=Value} | %{http_referer=unspecified} | 누락된 경우에만 참조자 헤더가 *unspecified*로 설정됩니다. 설정된 경우 아무 작업도 수행되지 않습니다. |
| 다음 조건 중 하나라도 충족하지 않을 경우 헤더를 기본값으로 설정합니다. <br /><br />- 누락됨<br /><br /> - NULL로 설정됨. | %{Variable:+Value} | %{http_referer:+unspecified} | 값이 할당된 경우에만 참조자 헤더가 *unspecified*로 설정됩니다. 누락되거나 NULL로 설정되지 않은 경우 아무 작업도 수행되지 않습니다. |

## <a name="manipulating-variables"></a>변수 조작
변수는 다음과 같은 방법으로 조작할 수 있습니다.

- 부분 문자열 확장
- 패턴 제거

### <a name="substring-expansion"></a>부분 문자열 확장
기본적으로 변수는 해당 전체 값으로 확장됩니다. 변수 값의 부분 문자열만 확장하려면 다음 구문을 사용합니다.

`%<Variable>:<Offset>:<Length>}`

주요 정보:

- 오프셋 용어에 할당된 값은 부분 문자열의 시작 문자를 결정합니다.

     - 양의: 부분 문자열의 시작 문자는 문자열의 첫 번째 문자에서 계산 됩니다.
     - 0: 부분 문자열의 시작 문자는 문자열의 첫 번째 문자입니다.
     - 음수: 부분 문자열의 시작 문자는 문자열의 마지막 문자에서 계산 됩니다.

- 부분 문자열의 길이는 *길이* 용어에 의해 결정됩니다.

     - 생략 됩니다. 길이 용어를 생략 하면 부분 문자열을 시작 문자 및 문자열의 끝 사이 모든 문자를 포함할 수 있습니다.
     - 양의: 오른쪽에서 시작 문자의 부분 문자열의 길이 결정합니다.
     - 음수: 시작 문자에서 왼쪽 부분 문자열의 길이 결정합니다.

#### <a name="example"></a>예제:

다음 예제에서는 다음 샘플 요청 URL를 사용합니다.

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

다음 문자열은 변수를 조작하기 위한 다양한 메서드를 보여 줍니다.

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

샘플 요청 URL에 따라 위의 변수 조작은 다음 값을 생성합니다.

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>패턴 제거
변수 값의 시작 부분이나 끝에서 특정 패턴에 일치하는 텍스트를 제거할 수 있습니다.

| 구문 | 액션(Action) |
| ------ | ------ |
| %{Variable#Pattern} | 변수 값의 시작 부분에서 지정된 패턴이 발견되면 텍스트를 제거합니다. |
| %{Variable%Pattern} | 변수 값의 마지막 부분에서 지정된 패턴이 발견되면 텍스트를 제거합니다. |

#### <a name="example"></a>예제:

이 샘플 시나리오에서 *request_uri* 변수는 다음으로 설정됩니다.

`/800001/myorigin/marketing/product.html?language=en-US`

다음 표에서 이 구문의 작동 방식을 설명합니다.

| 샘플 구문 | 결과 | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | 변수가 패턴으로 시작하기 때문에 대체되었습니다. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | 변수가 패턴으로 끝나지 않으므로 변경된 내용이 없습니다.|

### <a name="find-and-replace"></a>찾기 및 바꾸기
찾기 및 바꾸기 구문은 다음 표에 설명되어 있습니다.

| 구문 | 액션(Action) |
| ------ | ------ |
| %{Variable/Find/Replace} | 지정된 패턴의 첫 번째 발생을 찾아서 바꿉니다. |
| %{Variable//Find/Replace} | 지정된 패턴의 모든 발생을 찾아서 바꿉니다. |
| %{Variable^} |전체 값을 대문자로 변환합니다. |
| %{Variable^Find} | 지정된 패턴의 첫 번째 발생을 대문자로 변환합니다. |
| %{Variable,} | 전체 값을 소문자로 변환합니다. |
| %{Variable,Find} | 지정된 패턴의 첫 번째 발생을 소문자로 변환합니다. |

### <a name="find-and-rewrite"></a>찾기 및 다시 쓰기
찾기 및 바꾸기에 대한 변형의 경우 다시 작성하는 경우 지정된 패턴과 일치하는 텍스트를 사용합니다. 찾기 및 다시 쓰기 구문은 다음 표에 설명되어 있습니다.

| 구문 | 액션(Action) |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | 지정된 패턴의 모든 발생을 찾고, 복사하고, 다시 작성합니다. |
| %{Variable/^Find/Rewrite} | 변수의 시작에서 발생하는 경우 지정된 패턴을 찾기, 복사 및 다시 씁니다. |
| %{Variable/$Find/Rewrite} | 변수의 마지막에서 발생하는 경우 지정된 패턴을 찾기, 복사 및 다시 씁니다. |
| %{Variable/Find} | 지정된 패턴의 모든 발생을 찾아서 삭제합니다. |

주요 정보:

- 정수 뒤에 달러 기호를 지정하여(예: $1) 지정된 패턴과 일치하는 텍스트를 확장합니다.

- 여러 개의 패턴을 지정할 수 있습니다. 패턴이 지정되는 순서는 할당되는 정수를 결정합니다. 다음 예제에서는 첫 번째 패턴이 “www.”에 일치하며 두 번째 패턴은 두 번째 수준 도메인에 일치하고 세 번째 패턴은 최상위 도메인에 일치합니다.

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- 다시 쓴 값은 텍스트 및 이러한 자리 표시자의 조합으로 구성할 수 있습니다.

    이전 예제에서는 `cdn.$2.$3:80`에 호스트 이름을 다시 씁니다(예: cdn.mydomain.com:80).

- 패턴 자리 표시자의 경우(예: $1) 다음 플래그를 통해 수정할 수 있습니다.
     - U: 확장된 값을 대문자입니다.

         샘플 구문:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: 확장된 값을 소문자입니다.

         샘플 구문:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- 연산자는 패턴 앞에 지정되어야 합니다. 지정된 연산자는 패턴 캡처 동작을 결정합니다.

     - `=`: 지정 된 패턴의 모든 항목을 캡처하고 다시 작성할 수 있어야 합니다를 나타냅니다.
     - `^`: 지정된 된 패턴을 사용 하 여 시작 하는 텍스트만 캡처된다는 것을 나타냅니다.
     - `$`: 지정된 된 패턴으로 끝나는 텍스트만 캡처 된다는 것을 나타냅니다.
 
- */Rewrite* 값을 생략하면 패턴과 일치하는 텍스트가 삭제됩니다.


