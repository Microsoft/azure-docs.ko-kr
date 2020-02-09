---
title: Verizon 프리미엄 규칙 엔진 일치 조건의 Azure CDN | Microsoft Docs
description: Verizon 프리미엄 규칙 엔진 일치 조건의 Azure Content Delivery Network에 대한 참조 설명서입니다.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 1660dca34b2f128ef5889145fcdeed0d2523b9bb
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2019
ms.locfileid: "67593212"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Verizon 프리미엄 규칙 엔진 일치 조건의 Azure CDN

이 문서에서는 Verizon Premium [규칙 엔진](cdn-verizon-premium-rules-engine.md)의 Azure CONTENT DELIVERY NETWORK (CDN)에 대해 사용 가능한 일치 조건에 대한 자세한 설명을 나열 합니다.

규칙의 두 번째 부분이 일치 조건입니다. 일치 조건은 기능 집합에 대해 수행할 특정 요청 유형을 식별합니다.

예를 들어 다음을 수행하는 데 일치 조건을 사용할 수 있습니다.

- 특정 위치에서 콘텐츠에 대한 필터 요청
- 특정 IP 주소 또는 국가/지역에서 생성 된 필터 요청
- 헤더 정보에 의한 필터 요청

## <a name="always-match-condition"></a>항상 일치 조건

항상 일치 조건은 모든 요청에 기본 기능 집합을 적용합니다.

이름 | 용도
-----|--------
[항상](#always) | 모든 요청에 기본 기능 집합을 적용합니다.

## <a name="device-match-condition"></a>디바이스 일치 조건

디바이스 일치 조건은 속성에 따라 모바일 디바이스에서 생성되는 요청을 식별합니다.  

이름 | 용도
-----|--------
[장치](#device) | 속성에 따라 모바일 디바이스에서 생성되는 요청을 식별합니다.

## <a name="location-match-conditions"></a>위치 일치 조건

위치 일치 조건은 요청자의 위치에 따라 요청을 식별합니다.

이름 | 용도
-----|--------
[AS 숫자](#as-number) | 특정 네트워크에서 발생하는 요청을 식별합니다.
[Country](#country) | 지정 된 국가/지역에서 시작 되는 요청을 식별 합니다.

## <a name="origin-match-conditions"></a>원본 일치 조건

원본 일치 조건은 Content Delivery Network 스토리지 또는 고객 원본 서버를 가리키는 요청을 식별합니다.

이름 | 용도
-----|--------
[CDN 원본](#cdn-origin) | Content Delivery Network 스토리지에 저장된 콘텐츠에 대한 요청을 식별합니다.
[고객 원본](#customer-origin) | 특정 고객 원본 서버에 저장된 콘텐츠에 대한 요청을 식별합니다.

## <a name="request-match-conditions"></a>요청 일치 조건

요청 일치 조건은 해당 속성에 따라 요청을 식별합니다.

이름 | 용도
-----|--------
[클라이언트 IP 주소](#client-ip-address) | 특정 IP 주소에서 발생하는 요청을 식별합니다.
[쿠키 매개 변수](#cookie-parameter) | 지정된 값에 대한 각 요청과 관련된 쿠키를 확인합니다.
[쿠키 매개 변수 Regex](#cookie-parameter-regex) | 지정된 정규식에 대한 각 요청과 관련된 쿠키를 확인합니다.
[에지 Cname](#edge-cname) | 특정 에지 CNAME을 가리키는 요청을 식별합니다.
[참조 도메인](#referring-domain) | 지정된 호스트 이름에서 참조된 요청을 식별합니다.
[요청 헤더 리터럴](#request-header-literal) | 지정된 값으로 설정된 지정된 헤더를 포함하는 요청을 식별합니다.
[요청 헤더 Regex](#request-header-regex) | 지정된 정규식과 일치하는 값으로 설정된 지정된 헤더를 포함하는 요청을 식별합니다.
[요청 헤더 와일드카드](#request-header-wildcard) | 지정된 패턴과 일치하는 값으로 설정된 지정된 헤더를 포함하는 요청을 식별합니다.
[요청 메서드](#request-method) | 해당 HTTP 메서드로 요청을 식별합니다.
[요청 스키마](#request-scheme) | 해당 HTTP 프로토콜로 요청을 식별합니다.

## <a name="url-match-conditions"></a>URL 일치 조건

URL 일치 조건은 해당 URL에 따라 요청을 식별합니다.

이름 | 용도
-----|--------
[URL 경로 디렉터리](#url-path-directory) | 해당 상대 경로로 요청을 식별합니다.
[URL 경로 확장](#url-path-extension) | 해당 파일 이름 확장명으로 요청을 식별합니다.
[URL 경로 파일 이름](#url-path-filename) | 해당 파일 이름으로 요청을 식별합니다.
[URL 경로 리터럴](#url-path-literal) | 요청의 상대 경로를 지정된 값과 비교합니다.
[URL 경로 Regex](#url-path-regex) | 요청의 상대 경로를 지정된 정규식과 비교합니다.
[URL 경로 와일드카드](#url-path-wildcard) | 요청의 상대 경로를 지정된 패턴과 비교합니다.
[URL 쿼리 리터럴](#url-query-literal) | 요청의 쿼리 문자열을 지정된 값과 비교합니다.
[URL 쿼리 매개 변수](#url-query-parameter) | 지정된 패턴과 일치하는 값으로 설정된 지정된 쿼리 문자열 매개 변수를 포함하는 요청을 식별합니다.
[URL 쿼리 Regex](#url-query-regex) | 지정된 정규식과 일치하는 값으로 설정된 지정된 쿼리 문자열 매개 변수를 포함하는 요청을 식별합니다.
[URL 쿼리 와일드카드](#url-query-wildcard) | 지정된 값을 요청의 쿼리 문자열에 대해 비교합니다.

## <a name="reference-for-rules-engine-match-conditions"></a>규칙 엔진 일치 조건에 대한 참조

---

### <a name="always"></a>Always

항상 일치 조건은 모든 요청에 기본 기능 집합을 적용합니다.

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>AS 숫자

AS 숫자 네트워크는 해당 ASN(자치 시스템 번호)으로 정의됩니다. 

**일치**/**일치하지 않음** 옵션은 AS 숫자 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 클라이언트 네트워크의 ASN이 지정된 ASN 중 하나와 일치해야 합니다. 
- **일치하지 않음**: 클라이언트 네트워크의 ASN이 지정된 ASN과 일치하지 않아야 합니다.

주요 정보:

- 각각을 하나의 공백으로 구분하여 여러 ASN을 지정합니다. 예를 들어 64514 64515는 64514 또는 64515에서 도착하는 요청과 일치합니다.
- 특정 요청은 유효한 ASN을 반환하지 않을 수도 있습니다. 물음표(?)는 확인할 수 없는 유효한 ASN에 대한 요청과 일치합니다.
- 원하는 네트워크에 대한 전체 ASN을 지정합니다. 부분 값은 일치하지 않습니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>CDN 원본

다음 조건이 모두 충족될 때 CDN 원본 일치 조건이 충족됩니다.

- CDN 스토리지의 콘텐츠가 요청되었습니다.
- 요청 URI는 이 일치 조건에서 정의된 콘텐츠 액세스 포인트 유형(예: /000001)을 사용합니다.
  - CDN URL: 요청 URI에 선택한 콘텐츠 액세스 포인트가 포함되어야 합니다.
  - 에지 CNAME URL: 해당 에지 CNAME 구성은 선택한 콘텐츠 액세스 포인트를 가리켜야 합니다.
  
주요 정보:

- 콘텐츠 액세스 포인트는 요청한 콘텐츠를 제공해야 하는 서비스를 식별합니다.
- 특정 일치 조건을 결합하는 데 AND IF문을 사용하지 마십시오. 예를 들어 고객 원본 일치 조건과 CDN 원본 일치 조건을 결합하면 일치될 수 없는 일치 패턴을 만듭니다. 이러한 이유로 두 개의 CDN 원본 일치 조건은 AND IF문을 통해 결합될 수 없습니다.

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>클라이언트 IP 주소

**일치**/**일치하지 않음** 옵션은 클라이언트 IP 주소 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 클라이언트의 IP 주소가 지정된 IP 주소 중 하나와 일치해야 합니다. 
- **일치하지 않음**: 클라이언트의 IP 주소가 지정된 IP 주소와 일치하지 않아야 합니다. 

주요 정보:

- CIDR 표기법을 사용합니다.
- 각각을 단일 공백으로 구분하여 여러 IP 주소 및/또는 IP 주소 블록을 지정합니다. 예:
  - **IPv4 예제**: 1.2.3.4 10.20.30.40은 1.2.3.4 또는 10.20.30.40 주소에서 도착하는 요청과 일치합니다.
  - **IPv6 예제**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80은 1:2:3:4:5:6:7:8 또는 10:20:30:40:50:60:70:80 주소에서 도착하는 요청과 일치합니다.
- IP 주소 블록에 대한 구문은 뒤에 슬래시와 접두사 크기가 오는 기본 IP 주소입니다. 예를 들어:
  - **IPv4 예제**: 5.5.5.64/26은 5.5.5.64에서 5.5.5.127까지의 주소에서 도착하는 요청과 일치합니다.
  - **IPv6 예제**: 1:2:3:/48은 1:2:3:0:0:0:0:0에서 1:2:3:ffff:ffff:ffff:ffff:ffff까지의 주소에서 도착한 요청과 일치합니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>쿠키 매개 변수

**일치**/**일치하지 않음** 옵션은 쿠키 매개 변수 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 이 일치 조건에 정의된 값 중 하나 이상과 일치하는 값으로 지정된 쿠키를 포함하는 요청이 필요합니다.
- **일치하지 않음**: 요청이 다음 조건 중 하나를 충족해야 합니다.
  - 지정된 쿠키를 포함하지 않습니다.
  - 지정된 쿠키를 포함하지만 해당 값은 이 일치 조건에 정의된 값과 일치하지 않습니다.
  
주요 정보:

- 쿠키 이름:
  - 쿠키 이름을 지정할 때 별표(*)를 포함한 와일드카드 값은 지원되지 않으므로 정확히 일치하는 쿠키 이름만 비교할 수 있습니다.
  - 이 일치 조건의 인스턴스당 단일 쿠키 이름만 지정할 수 있습니다.
  - 쿠키 이름 비교는 대/소문자를 구분하지 않습니다.
- 쿠키 값:
  - 각각을 하나의 공백으로 구분하여 여러 쿠키 값을 지정합니다.
  - 쿠키 값은 [와일드카드 값](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)을 활용할 수 있습니다.
  - 와일드카드 값이 지정되지 않은 경우 정확히 일치해야만 일치 조건이 충족됩니다. 예를 들어 "Value"를 지정하면 "Value"와 일치하지만 "Value1" 또는 "Value2"와 일치하지 않습니다.
  - **대/소문자 무시** 옵션을 사용하여 요청의 쿠키 값에 대해 대/소문자 비교를 수행할지 여부를 제어합니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>쿠키 매개 변수 Regex

쿠키 매개 변수 Regex 일치 조건은 쿠키 이름과 값을 정의합니다. [정규식](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)을 사용하여 원하는 쿠키 값을 정의할 수 있습니다.

**일치**/**일치하지 않음** 옵션은 쿠키 매개 변수 Regex 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 지정된 정규식과 일치하는 값으로 지정된 쿠키를 포함하는 요청이 필요합니다.
- **일치하지 않음**: 요청이 다음 조건 중 하나를 충족해야 합니다.
  - 지정된 쿠키를 포함하지 않습니다.
  - 지정된 쿠키를 포함하지만 해당 값은 지정된 정규식과 일치하지 않습니다.
  
주요 정보:

- 쿠키 이름:
  - 쿠키 이름을 지정할 때 별표(*)를 포함한 와일드카드 값 및 정규식이 지원되지 않으므로 정확히 일치하는 쿠키 이름만 비교할 수 있습니다.
  - 이 일치 조건의 인스턴스당 단일 쿠키 이름만 지정할 수 있습니다.
  - 쿠키 이름 비교는 대/소문자를 구분하지 않습니다.
- 쿠키 값:
  - 쿠키 값은 정규식을 활용할 수 있습니다.
  - **대/소문자 무시** 옵션을 사용하여 요청의 쿠키 값에 대해 대/소문자 비교를 수행할지 여부를 제어합니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Country

해당 국가 번호를 통해 국가를 지정할 수 있습니다. 

**일치**/**일치하지 않음** 옵션은 국가 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 지정된 국가 코드 값이 요청에 포함되어야 합니다. 
- **일치하지 않음**: 지정된 국가 코드 값이 요청에 포함되지 않아야 합니다.

주요 정보:

- 각각을 하나의 공백으로 구분하여 여러 국가 번호를 지정합니다.
- 국가 번호를 지정하는 경우 와일드카드는 지원되지 않습니다.
- "EU"와 "AP" 국가 번호는 해당 영역에서 모든 IP 주소를 포함하지 않습니다.
- 특정 요청은 유효한 국가 번호를 반환하지 않을 수도 있습니다. 물음표(?)는 확인할 수 없는 유효한 국가 번호에 대한 요청과 일치합니다.
- 국가 번호는 대/소문자를 구분합니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>규칙 엔진을 사용하여 국가 필터링 구현

이 일치 조건을 사용하면 요청이 시작된 위치를 기반으로 다수의 사용자 지정을 수행할 수 있습니다. 예를 들어 국가 필터링 기능의 동작은 다음 구성을 통해 복제될 수 있습니다.

- URL 경로 와일드카드 일치: [URL 경로 와일드카드 일치 조건](#url-path-wildcard)을 보안이 적용될 디렉터리로 설정합니다. 
    상대 경로 끝에 별표를 추가하여 이 규칙에 의해 모든 자식에 대한 액세스가 제한되도록 합니다.

- 국가 일치: 국가 일치 조건을 원하는 국가 집합으로 설정합니다.
  - 허용: URL 경로 와일드카드 일치 조건에 정의된 위치에 저장된 콘텐츠에 특정 국가만 액세스하도록 허용하려면 국가 일치 조건을 **일치하지 않음**으로 설정합니다.
  - 차단: URL 경로 와일드카드 일치 조건에 정의된 위치에 저장된 콘텐츠에 특정 국가가 액세스하지 못하도록 차단하려면 국가 일치 조건을 **일치**로 설정합니다.

- 액세스 거부(403) 기능: 국가 필터링 기능의 허용 또는 차단 부분을 복제하려면 [액세스 거부(403) 기능](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403)을 사용하도록 설정합니다.

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>고객 원본

주요 정보:

- 고객 원본 일치 조건은 선택된 고객 원본을 가리키는 에지 CNAME URL을 통해 콘텐츠를 요청했는지 또는 CDN URL을 통해 콘텐츠를 요청했는지 여부에 관계없이 충족됩니다.
- 규칙에서 참조하는 고객 원본 구성은 고객 원본 페이지에서 삭제할 수 없습니다. 고객 원본 구성을 삭제하려고 하기 전에 다음 구성이 이를 참조하지 않는지 확인합니다.
  - 고객 원본 일치 조건
  - 에지 CNAME 구성
- 특정 일치 조건을 결합하는 데 AND IF문을 사용하지 마십시오. 예를 들어 CDN 원본 일치 조건과 고객 원본 일치 조건을 결합하면 일치될 수 없는 일치 패턴을 만듭니다. 이러한 이유로 두 개의 고객 원본 일치 조건은 AND IF문을 통해 결합될 수 없습니다.

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>디바이스

디바이스 일치 조건은 속성에 따라 모바일 디바이스에서 생성되는 요청을 식별합니다. 모바일 디바이스 검색은 [WURFL](http://wurfl.sourceforge.net/)을 통해 이루어집니다. 

**일치**/**일치하지 않음** 옵션은 디바이스 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 요청자의 디바이스가 지정된 값과 일치해야 합니다. 
- **일치하지 않음**: 요청자의 디바이스가 지정된 값과 일치하지 않아야 합니다.

주요 정보:

- **대/소문자 무시** 옵션을 사용하여 지정된 값의 대/소문자 구분 여부를 지정합니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

#### <a name="string-type"></a>문자열 형식

WURFL 기능은 일반적으로 숫자, 문자 및 기호의 모든 조합을 허용합니다. 이 기능이 유연하기 때문에 이 일치 조건과 연결된 값이 해석되는 방식을 선택해야 합니다. 다음 테이블에는 사용 가능한 옵션 집합이 설명되어 있습니다.

형식     | Description
---------|------------
리터럴  | 대부분의 문자가 [리터럴 값](cdn-verizon-premium-rules-engine-reference.md#literal-values)을 사용하여 특별한 의미를 갖지 못하게 하려면 이 옵션을 선택합니다.
와일드카드 | 모든 [와일드카드 문자]([와일드카드 값](cdn-verizon-premium-rules-engine-reference.md#wildcard-values))을 활용하려면 이 옵션을 선택합니다.
Regex    | [정규식](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)을 사용하려면 이 옵션을 선택합니다. 정규식은 문자 패턴을 정의할 때 유용합니다.

#### <a name="wurfl-capabilities"></a>WURFL 기능

WURFL 기능은 모바일 디바이스를 설명하는 범주를 나타냅니다. 선택된 기능은 요청을 식별하는 데 사용되는 모바일 디바이스 설명의 형식을 결정합니다.

다음 테이블에는 규칙 엔진에 대한 WURFL 기능 및 해당 변수가 나열되어 있습니다.

> [!NOTE]
> 다음 변수는 **클라이언트 요청 헤더 수정** 및 **클라이언트 응답 헤더 수정** 기능에서 지원됩니다.

기능 | 변수 | Description | 샘플 값
-----------|----------|-------------|----------------
브랜드 이름 | %{wurfl_cap_brand_name} | 디바이스의 브랜드 이름을 나타내는 문자열입니다. | Samsung
디바이스 OS | %{wurfl_cap_device_os} | 디바이스에 설치된 운영 체제를 나타내는 문자열입니다. | IOS
디바이스 OS 버전 | %{wurfl_cap_device_os_version} | 디바이스에 설치된 운영 체제의 버전 번호를 나타내는 문자열입니다. | 1.0.1
이중 방향 | %{wurfl_cap_dual_orientation} | 디바이스가 이중 방향을 지원하는지 여부를 나타내는 부울입니다. | true
HTML 기본 DTD | %{wurfl_cap_html_preferred_dtd} | HTML 콘텐츠에 대한 모바일 디바이스의 기본 DTD(문서 종류 정의)를 나타내는 문자열입니다. | 없음<br/>xhtml_basic<br/>html5
이미지 인라인 처리 | %{wurfl_cap_image_inlining} | 디바이스가 Base64로 인코딩된 이미지를 지원하는지 여부를 나타내는 부울입니다. | false
Android 여부 | %{wurfl_vcap_is_android} | 디바이스가 Android OS를 사용하는지 여부를 나타내는 부울입니다. | true
IOS 여부 | %{wurfl_vcap_is_ios} | 디바이스에서 iOS를 사용하는지 여부를 나타내는 부울입니다. | false
스마트 TV 여부 | %{wurfl_cap_is_smarttv} | 디바이스가 스마트 TV인지 여부를 나타내는 부울입니다. | false
스마트폰 여부 | %{wurfl_vcap_is_smartphone} | 디바이스가 스마트폰인지 여부를 나타내는 부울입니다. | true
태블릿 여부 | %{wurfl_cap_is_tablet} | 디바이스가 태블릿인지 여부를 나타내는 부울입니다. 이 설명은 OS와 별개입니다. | true
무선 디바이스 여부 | %{wurfl_cap_is_wireless_device} | 디바이스가 무선 디바이스로 간주되는지 여부를 나타내는 부울입니다. | true
마케팅 이름 | %{wurfl_cap_marketing_name} | 디바이스의 마케팅 이름을 나타내는 문자열입니다. | BlackBerry 8100 Pearl
모바일 브라우저 | %{wurfl_cap_mobile_browser} | 디바이스에서 콘텐츠를 요청하는 데 사용되는 브라우저를 나타내는 문자열입니다. | Chrome
모바일 브라우저 버전 | %{wurfl_cap_mobile_browser_version} | 디바이스에서 콘텐츠를 요청하는 데 사용되는 브라우저의 버전을 나타내는 문자열입니다. | 31
모델 이름 | %{wurfl_cap_model_name} | 디바이스의 모델 이름을 나타내는 문자열입니다. | s3
점진적 다운로드 | %{wurfl_cap_progressive_download} | 디바이스가 오디오 및 비디오를 다운로드하는 중에 오디오 및 비디오 재생을 지원하는지 여부를 나타내는 부울입니다. | true
릴리스 날짜 | %{wurfl_cap_release_date} | 디바이스가 WURFL 데이터베이스에 추가된 연도와 월을 나타내는 문자열입니다.<br/><br/>형식: `yyyy_mm` | 2013_december
해상도 높이 | %{wurfl_cap_resolution_height} | 디바이스의 높이(픽셀)를 나타내는 정수입니다. | 768
해상도 너비 | %{wurfl_cap_resolution_width} | 디바이스의 너비(픽셀)를 나타내는 정수입니다. | 1024

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>에지 Cname

주요 정보:

- 사용 가능한 에지 CNAME 목록은 규칙 엔진이 구성되는 플랫폼의 에지 CNAME 페이지에 구성된 에지 CNAME으로 제한됩니다.
- 에지 CNAME 구성을 삭제하려고 하기 전에 에지 Cname 일치 조건이 이를 참조하지 않는지 확인합니다. 규칙에 정의된 에지 CNAME 구성은 에지 CNAME 페이지에서 삭제할 수 없습니다.
- 특정 일치 조건을 결합하는 데 AND IF문을 사용하지 마십시오. 예를 들어 고객 원본 일치 조건과 에지 Cname 일치 조건을 결합하면 일치될 수 없는 일치 패턴을 만듭니다. 이러한 이유로 두 개의 에지 Cname 일치 조건은 AND IF문을 통해 결합될 수 없습니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>참조 도메인

콘텐츠가 요청되었던 참조 페이지와 연결된 호스트 이름은 참조 도메인 조건이 충족되는지 여부를 결정합니다.

**일치**/**일치하지 않음** 옵션은 참조 도메인 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 참조하는 호스트 이름이 지정된 값과 일치해야 합니다. 
- **일치하지 않음**: 참조하는 호스트 이름이 지정된 값과 일치하지 않아야 합니다.

주요 정보:

- 각각을 하나의 공백으로 구분하여 여러 호스트 이름을 지정합니다.
- 이 일치 조건은 [와일드카드 값](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)을 지원합니다.
- 지정된 값이 별표를 포함하지 않는 경우 참조 페이지의 호스트 이름에 대해 정확히 일치해야 합니다. 예를 들어 "mydomain.com"을 지정하면 "www.mydomain.com"과 일치하지 않습니다.
- **대/소문자 무시** 옵션을 사용하여 대/소문자를 구분하여 비교할지 여부를 제어합니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>요청 헤더 리터럴

**일치**/**일치하지 않음** 옵션은 요청 헤더 리터럴 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 지정된 헤더를 포함하는 요청이 필요합니다. 해당 값은 이 일치 조건에 정의된 것과 일치해야 합니다.
- **일치하지 않음**: 요청이 다음 조건 중 하나를 충족해야 합니다.
  - 지정된 헤더를 포함하지 않습니다.
  - 지정된 헤더를 포함하지만 해당 값은 이 일치 조건에 정의된 값과 일치하지 않습니다.
  
주요 정보:

- 헤더 이름 비교는 항상 대/소문자를 구분하지 않습니다. **대/소문자 무시** 옵션을 사용하여 헤더 값을 비교할 때 대/소문자를 구분할지 여부를 제어합니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>요청 헤더 Regex

**일치**/**일치하지 않음** 옵션은 요청 헤더 Regex 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 지정된 헤더를 포함하는 요청이 필요합니다. 해당 값은 지정된 [정규식](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)에 정의된 패턴과 일치해야 합니다.
- **일치하지 않음**: 요청이 다음 조건 중 하나를 충족해야 합니다.
  - 지정된 헤더를 포함하지 않습니다.
  - 지정된 헤더를 포함하지만 해당 값은 지정된 정규식과 일치하지 않습니다.

주요 정보:

- 헤더 이름:
  - 헤더 이름 비교는 대/소문자를 구분하지 않습니다.
  - 헤더 이름에 있는 공백은 "%20"으로 대체합니다.
- 헤더 값:
  - 헤더 값은 정규식을 활용할 수 있습니다.
  - **대/소문자 무시** 옵션을 사용하여 헤더 값을 비교할 때 대/소문자를 구분할지 여부를 제어합니다.
  - 헤더 값이 지정된 패턴 중 적어도 하나와 정확히 일치하는 경우에만 일치 조건이 충족됩니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>요청 헤더 와일드카드

**일치**/**일치하지 않음** 옵션은 요청 헤더 와일드카드 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 지정된 헤더를 포함하는 요청이 필요합니다. 해당 값은 이 일치 조건에 정의된 값 중 하나 이상과 일치해야 합니다.
- **일치하지 않음**: 요청이 다음 조건 중 하나를 충족해야 합니다.
  - 지정된 헤더를 포함하지 않습니다.
  - 지정된 헤더를 포함하지만 해당 값은 지정된 값과 일치하지 않습니다.
  
주요 정보:

- 헤더 이름:
  - 헤더 이름 비교는 대/소문자를 구분하지 않습니다.
  - 헤더 이름의 공백은 "%20"으로 대체되어야 합니다. 또한 이 값을 사용하여 헤더 값에 공백을 지정할 수도 있습니다.
- 헤더 값:
  - 헤더 값은 [와일드카드 값](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)을 활용할 수 있습니다.
  - **대/소문자 무시** 옵션을 사용하여 헤더 값을 비교할 때 대/소문자를 구분할지 여부를 제어합니다.
  - 헤더 값이 지정된 패턴 중 적어도 하나와 정확히 일치하는 경우에 일치 조건이 충족됩니다.
  - 각각을 하나의 공백으로 구분하여 여러 값을 지정합니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>요청 메서드

요청 메서드 일치 조건은 선택한 요청 메서드를 통해 자산을 요청한 경우에만 충족됩니다. 사용 가능한 요청 메서드는 다음과 같습니다.

- 가져오기
- HEAD
- 올리기
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

주요 정보:

- 기본적으로 GET 요청 메서드만 네트워크에서 캐시된 콘텐츠를 생성할 수 있습니다. 다른 모든 요청 메서드는 네트워크를 통해 프록시 설정됩니다.
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>요청 스키마

요청 스키마 일치 조건은 선택한 프로토콜을 통해 자산을 요청한 경우에만 충족됩니다. 사용 가능한 프로토콜은 다음과 같습니다.

- HTTP
- HTTPS

주요 정보:

- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
  - 전체 캐시 채우기
  - 기본 내부 Max-Age
  - 강제 내부 Max-Age
  - 원본 No-Cache 무시
  - 내부 Max-Stale

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>URL 경로 디렉터리

상대 경로로 요청을 식별하며 요청된 자산의 파일 이름은 제외됩니다.

**일치**/**일치하지 않음** 옵션은 URL 경로 디렉터리 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 지정된 URL 패턴과 일치하는 상대 URL 경로(파일 이름 제외)가 요청에 포함되어야 합니다.
- **일치하지 않음**: 지정된 URL 패턴과 일치하지 않는 상대 URL 경로(파일 이름 제외)가 요청에 포함되어야 합니다.

주요 정보:

- **기준** 옵션을 사용하여 URL 비교를 콘텐츠 액세스 포인트 전에 시작할지 또는 후에 시작할지 여부를 지정합니다. 콘텐츠 액세스 포인트는 Verizon CDN 호스트 이름과 요청된 자산의 상대 경로(예: /800001/CustomerOrigin) 사이에 나타나는 경로일 일부분입니다. 서버 유형(예: CDN 또는 고객 원본) 및 고객 계정 번호로 위치를 식별합니다.

   **기준** 옵션에는 다음 값을 사용할 수 있습니다.
  - **루트**: URL 비교 지점이 CDN 호스트 이름 바로 뒤에 시작됩니다. 

  예: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **원본**: URL 비교 지점이 콘텐츠 액세스 포인트 다음에 시작됩니다(예: /000001 또는 /800001/myorigin). \*.azureedge.net CNAME은 기본적으로 Verizon CDN 호스트 이름의 원본 디렉터리를 기준으로 생성되기 때문에 Azure CDN 사용자는 **원본** 값을 사용해야 합니다. 

  예: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  이 URL은 Verizon CDN 호스트 이름 http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm을 가리킵니다.

- 에지 CNAME URL은 URL을 비교하기 전에 CDN URL로 다시 작성됩니다.

    예를 들어 다음 URL은 모두 동일한 자산을 가리키기 때문에 URL 경로가 동일합니다.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - 에지 CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    추가 정보:
  - 사용자 지정 도메인: https:\//my.domain.com/path/asset.htm
    
    - URL 경로(루트 기준): /800001/CustomerOrigin/path/
    
    - URL 경로(원본 기준): /path/

- URL 비교에 사용되는 URL 부분은 요청된 자산의 파일 이름 바로 앞에 끝납니다. 이런 형식의 경로에서는 후행 슬래시가 마지막 문자입니다

- URL 경로 패턴에 공백이 있으면 "%20"으로 대체합니다.

- 각 URL 경로 패턴에는 하나 이상의 별표(*)가 포함될 수 있으며 각 별표는 하나 이상의 문자 시퀀스와 일치합니다.

- 각 항목을 하나의 공백으로 구분하여 패턴에 여러 URL 경로를 지정합니다.

    예: */sales/ */marketing/

- URL 경로 지정은 [와일드카드 값](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)을 활용할 수 있습니다.

- **대/소문자 무시** 옵션을 사용하여 대/소문자를 구분하여 비교를 수행할지 여부를 제어합니다.

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>URL 경로 확장

요청된 자산의 파일 확장명으로 요청을 식별합니다.

**일치**/**일치하지 않음** 옵션은 URL 경로 확장 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 지정된 패턴과 정확히 일치하는 파일 확장명이 요청의 URL에 포함되어야 합니다.

   예를 들어 "htm"을 지정하면, "htm" 자산은 일치하고 "html" 자산은 일치하지 않습니다.  

- **일치하지 않음**: 지정된 패턴과 일치하지 않는 파일 확장명이 URL 요청에 포함되어야 합니다.

주요 정보:

- **값** 상자에 일치시킬 파일 확장명을 지정합니다. 앞의 마침표를 포함하지 않습니다. 예를 들어, .htm이 아닌 htm을 사용합니다.

- **대/소문자 무시** 옵션을 사용하여 대/소문자를 구분하여 비교를 수행할지 여부를 제어합니다.

- 각 확장명을 하나의 공백으로 구분하여 파일 확장명을 여러 개 지정합니다. 

    예: htm html

- 예를 들어 "htm"을 지정하면 "htm" 자산은 일치하고 "html" 자산은 일치하지 않습니다.

#### <a name="sample-scenario"></a>샘플 시나리오

다음 샘플 구성에서는 지정된 확장명 중 하나와 요청이 일치할 때 이 일치 조건이 충족된다고 가정합니다.

값 사양: asp aspx php html

이 일치 조건은 다음 확장명으로 끝나는 URL을 찾으면 충족됩니다.

- .asp
- .aspx
- .php
- .html

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>URL 경로 파일 이름

요청된 자산의 파일 이름으로 요청을 식별합니다. 이 일치 조건의 경우, 파일 이름은 요청된 자산의 이름, 마침표 및 파일 확장명으로 구성됩니다(예: index.html).

**일치**/**일치하지 않음** 옵션은 URL 경로 파일 이름 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 지정된 패턴과 일치하는 파일 이름이 요청의 URL 경로에 포함되어야 합니다.
- **일치하지 않음**: 지정된 패턴과 일치하지 않는 파일 이름이 요청의 URL 경로에 포함되어야 합니다.

주요 정보:

- **대/소문자 무시** 옵션을 사용하여 대/소문자를 구분하여 비교를 수행할지 여부를 제어합니다.

- 파일 확장명을 여러 개 지정하려면 각 확장명을 하나의 공백으로 구분합니다.

    예: index.htm index.html

- 파일 이름 값의 공백은 "%20"으로 대체합니다.

- 파일 이름 값은 [와일드카드 값](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)을 활용할 수 있습니다. 예를 들어 각각의 파일 이름 패턴은 하나 이상의 별표(*)로 구성될 수 있으며 각 별표는 하나 이상의 문자 시퀀스와 일치합니다.

- 와일드카드 문자를 지정하지 않으면 정확한 일치해야만 일치 조건이 충족됩니다.

    예를 들어 "presentation.ppt"를 지정하면 "presentation.ppt"라는 자산은 일치하지만 "presentation.pptx"라는 자산은 일치하지 않습니다.

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>URL 경로 리터럴

요청의 URL 경로(파일 이름 포함)를 지정된 값과 비교합니다.

**일치**/**일치하지 않음** 옵션은 URL 경로 리터럴 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 지정된 패턴과 일치하는 URL 경로가 요청에 포함되어야 합니다.
- **일치하지 않음**: 지정된 패턴과 일치하지 않는 URL 경로가 요청에 포함되어야 합니다.

주요 정보:

- **기준** 옵션을 사용하여 URL 비교 지점을 콘텐츠 액세스 포인트 전에 시작할지 또는 후에 시작할지 여부를 지정합니다. 

    **기준** 옵션에는 다음 값을 사용할 수 있습니다.
  - **루트**: URL 비교 지점이 CDN 호스트 이름 바로 뒤에 시작됩니다.

    예: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **원본**: URL 비교 지점이 콘텐츠 액세스 포인트 다음에 시작됩니다(예: /000001 또는 /800001/myorigin). \*.azureedge.net CNAME은 기본적으로 Verizon CDN 호스트 이름의 원본 디렉터리를 기준으로 생성되기 때문에 Azure CDN 사용자는 **원본** 값을 사용해야 합니다. 

    예: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  이 URL은 Verizon CDN 호스트 이름 http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm을 가리킵니다.

- 에지 CNAME URL은 URL을 비교하기 전에 CDN URL로 다시 작성됩니다.

예를 들어 다음 URL은 모두 동일한 자산을 가리키기 때문에 URL 경로가 동일합니다.

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- 에지 CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    추가 정보:
    
    - URL 경로(루트 기준): /800001/CustomerOrigin/path/asset.htm
   
    - URL 경로(원본 기준): /path/asset.htm

- URL의 쿼리 문자열은 무시됩니다.
- **대/소문자 무시** 옵션을 사용하여 대/소문자를 구분하여 비교를 수행할지 여부를 제어합니다.
- 이 일치 조건에 지정된 값은 클라이언트가 작성한 정확한 요청의 상대 경로와 비교됩니다.

- 특정 디렉터리에 대한 모든 요청을 일치시키려면 [URL 경로 디렉터리](#url-path-directory) 또는 [URL 경로 와일드카드](#url-path-wildcard) 일치 조건을 사용합니다.

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>URL 경로 Regex

요청의 URL 경로를 지정된 [정규식](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)과 비교합니다.

**일치**/**일치하지 않음** 옵션은 URL 경로 Regex 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 지정된 정규식과 일치하는 URL 경로가 요청에 포함되어야 합니다.
- **일치하지 않음**: 지정된 정규식과 일치하지 않는 URL 경로가 요청에 포함되어야 합니다.

주요 정보:

- 에지 CNAME URL은 URL을 비교하기 전에 CDN URL로 다시 작성됩니다.

    예를 들어 두 URL이 모두 동일한 자산을 가리키기 때문에 URL 경로가 동일합니다.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - 에지 CNAME URL: http:\//my.domain.com/path/asset.htm

    추가 정보:
    
     - URL 경로: /800001/CustomerOrigin/path/asset.htm

- URL의 쿼리 문자열은 무시됩니다.
    
- **대/소문자 무시** 옵션을 사용하여 대/소문자를 구분하여 비교를 수행할지 여부를 제어합니다.
    
- URL 경로의 공백은 "%20"으로 대체되어야 합니다.

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>URL 경로 와일드카드

요청의 상대 URL 경로를 지정된 와일드카드 패턴과 비교합니다.

**일치**/**일치하지 않음** 옵션은 URL 경로 와일드카드 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 지정된 와일드카드 패턴과 일치하는 URL 경로가 요청에 포함되어야 합니다.
- **일치하지 않음**: 지정된 와일드카드 패턴과 일치하지 않는 URL 경로가 요청에 포함되어야 합니다.

주요 정보:

- **기준** 옵션: 이 옵션은 URL 비교 지점을 콘텐츠 액세스 포인트 전에 시작할지 또는 후에 시작할지 여부를 결정합니다.

   이 옵션은 다음 값을 가질 수 있습니다.
     - **루트**: URL 비교 지점이 CDN 호스트 이름 바로 뒤에 시작됩니다.

       예: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **원본**: URL 비교 지점이 콘텐츠 액세스 포인트 다음에 시작됩니다(예: /000001 또는 /800001/myorigin). \*.azureedge.net CNAME은 기본적으로 Verizon CDN 호스트 이름의 원본 디렉터리를 기준으로 생성되기 때문에 Azure CDN 사용자는 **원본** 값을 사용해야 합니다. 

       예: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     이 URL은 Verizon CDN 호스트 이름 http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm을 가리킵니다.

- 에지 CNAME URL은 URL을 비교하기 전에 CDN URL로 다시 작성됩니다.

    예를 들어 다음 URL은 모두 동일한 자산을 가리키기 때문에 URL 경로가 동일합니다.
     - CDN URL: http://wpc.0001.&lt ;domain&gt; /800001/CustomerOrigin/path/asset.htm
     - 에지 CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    추가 정보:
    
     - URL 경로(루트 기준): /800001/CustomerOrigin/path/asset.htm
    
     - URL 경로(원본 기준): /path/asset.htm
    
- 각 항목을 하나의 공백으로 구분하여 여러 URL 경로를 지정합니다.

   예: /marketing/asset.* /sales/\*.htm

- URL의 쿼리 문자열은 무시됩니다.
    
- **대/소문자 무시** 옵션을 사용하여 대/소문자를 구분하여 비교를 수행할지 여부를 제어합니다.
    
- URL 경로의 공백은 "%20"으로 대체합니다.
    
- URL 경로에 지정된 값은 [와일드카드 값](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)을 활용할 수 있습니다. 각 URL 경로 패턴에는 하나 이상의 별표(*)가 포함될 수 있으며 각 별표는 하나 이상의 문자 시퀀스와 일치할 수 있습니다.

#### <a name="sample-scenarios"></a>샘플 시나리오

다음 테이블의 샘플 구성에서는 요청이 지정된 URL 패턴과 일치할 때 이 일치 조건이 충족된다고 가정합니다.

값                   | 기준    | 결과 
------------------------|----------------|-------
\*/test.html \*/test.php  | 루트 또는 원본 | 이 패턴은 모든 폴더의 "test.html" 또는 "test.php"라는 자산에 대한 요청과 일치합니다.
/80ABCD/origin/text/*   | 루트           | 이 패턴은 요청된 자산이 다음 조건을 충족하는 경우에 일치합니다. <br />- "원본"이라는 고객 원본에 있어야 합니다. <br />- 상대 경로가 "text"라는 폴더로 시작해야 합니다. 즉, 요청된 자산이 "text" 폴더 또는 해당 하위 폴더 중 하나에 있어야 합니다.
*/css/* */js/*          | 루트 또는 원본 | 이 패턴은 css 또는 js 폴더가 포함된 모든 CDN 또는 에지 CNAME URL과 일치합니다.
*.jpg *.gif *.png       | 루트 또는 원본 | 이 패턴은 .jpg, .gif 또는 .png로 끝나는 모든 CDN 또는 에지 CNAME URL과 일치합니다. 이 패턴을 지정하는 다른 방법은 [URL 경로 확장 일치 조건](#url-path-extension)을 사용하는 것입니다.
/images/\* /media/\*      | 원본         | 이 패턴은 상대 경로가 "images" 또는 "media" 폴더로 시작하는 CDN 또는 에지 CNAME URL과 일치합니다. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- 샘플 에지 CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>URL 쿼리 리터럴

요청의 쿼리 문자열을 지정된 값과 비교합니다.

**일치**/**일치하지 않음** 옵션은 URL 쿼리 리터럴 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 지정된 쿼리 문자열과 일치하는 URL 쿼리 문자열이 요청에 포함되어야 합니다.
- **일치하지 않음**: 지정된 쿼리 문자열과 일치하지 않는 URL 쿼리 문자열이 요청에 포함되어야 합니다.

주요 정보:

- 쿼리 문자열이 정확히 일치해야만 이 일치 조건이 충족됩니다.
    
- **대/소문자 무시** 옵션을 사용하여 쿼리 문자열을 비교할 때 대/소문자를 구분할지 여부를 제어합니다.
    
- 쿼리 문자열 값 텍스트에 선행 물음표(?)를 포함하지 않습니다.
    
- 특정 문자는 URL 인코딩이 필요합니다. 다음 문자를 URL 인코딩하려면 백분율 기호를 사용합니다.

   문자 | URL 인코딩
   ----------|---------
   Space     | %20
   &         | %25

- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
   - 전체 캐시 채우기
   - 기본 내부 Max-Age
   - 강제 내부 Max-Age
   - 원본 No-Cache 무시
   - 내부 Max-Stale

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>URL 쿼리 매개 변수

지정된 쿼리 문자열 매개 변수를 포함하는 요청을 식별합니다. 이 매개 변수는 지정된 패턴과 일치하는 값으로 설정됩니다. 요청 URL의 쿼리 문자열 매개 변수(예: parameter=value)가 이 조건이 충족되는지 여부를 결정합니다. 이 일치 조건은 쿼리 문자열 매개 변수를 이름으로 식별하고 매개 변수 값에 하나 이상의 값을 허용합니다. 

**일치**/**일치하지 않음** 옵션은 URL 쿼리 매개 변수 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 이 일치 조건에 정의된 값 중 하나 이상과 일치하는 값으로 지정된 매개 변수가 요청에 포함되어야 합니다.
- **일치하지 않음**: 요청이 다음 조건 중 하나를 충족해야 합니다.
  - 지정된 매개 변수를 포함하지 않습니다.
  - 지정된 매개 변수를 포함하지만 해당 값은 이 일치 조건에 정의된 값과 일치하지 않습니다.

이 일치 조건을 사용하면 매개 변수 이름/값 조합을 쉽게 지정할 수 있습니다. 쿼리 문자열 매개 변수를 일치시키는 경우 유연성을 높이려면 [URL 쿼리 와일드카드](#url-query-wildcard) 일치 조건을 사용하는 것이 좋습니다.

주요 정보:

- 이 일치 조건의 인스턴스마다 URL 쿼리 매개 변수 이름을 하나만 지정할 수 있습니다.
    
- 매개 변수 이름이 지정되면 와일드카드 값이 지원되지 않으므로, 매개 변수 이름이 정확히 일치해야만 비교할 수 있습니다.
- 매개 변수 값에는 [와일드카드 값](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)을 포함할 수 있습니다.
   - 각 매개 변수 값 패턴은 하나 이상의 별표(*)로 구성될 수 있으며 각 별표는 하나 이상의 문자 시퀀스와 일치할 수 있습니다.
   - 특정 문자는 URL 인코딩이 필요합니다. 다음 문자를 URL 인코딩하려면 백분율 기호를 사용합니다.

       문자 | URL 인코딩
       ----------|---------
       Space     | %20
       &         | %25

- 각 항목을 하나의 공백으로 구분하여 쿼리 문자열 매개 변수 값을 여러 개 지정합니다. 이 일치 조건은 지정된 이름/값 조합 중 하나가 요청에 포함되어 있는 경우에 충족됩니다.

   - 예제 1:

     - 구성:

       ValueA ValueB

     - 이 구성은 다음 쿼리 문자열 매개 변수와 일치합니다.

       Parameter1=ValueA
    
       Parameter1=ValueB

   - 예제 2:

     - 구성: 

        Value%20A Value%20B

     - 이 구성은 다음 쿼리 문자열 매개 변수와 일치합니다.

       Parameter1=Value%20A

       Parameter1=Value%20B

- 이 일치 조건은 지정된 쿼리 문자열 이름/값 조합 중 하나 이상과 정확히 일치하는 항목이 있는 경우에만 충족됩니다.

   예를 들어 이전 예제의 구성을 사용하면, 매개 변수 이름/값 조합 "Parameter1=ValueAdd"는 일치하는 것으로 간주되지 않습니다. 하지만 다음 값 중 하나를 지정하면 해당 이름/값 조합과 일치합니다.

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- **대/소문자 무시** 옵션을 사용하여 쿼리 문자열을 비교할 때 대/소문자를 구분할지 여부를 제어합니다.
    
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
   - 전체 캐시 채우기
   - 기본 내부 Max-Age
   - 강제 내부 Max-Age
   - 원본 No-Cache 무시
   - 내부 Max-Stale

#### <a name="sample-scenarios"></a>샘플 시나리오

다음 예제는 이 옵션이 특정 상황에서 작동하는 방식을 보여줍니다.

이름  | 값 |  결과
------|-------|--------
사용자  | Joe   | 이 패턴은 요청된 URL의 쿼리 문자열이 "?user=joe"인 경우에 일치합니다.
사용자  | *     | 이 패턴은 요청된 URL의 쿼리 문자열에 User 매개 변수가 포함된 경우에 일치합니다.
EMail | Joe\* | 이 패턴은 요청된 URL의 쿼리 문자열에 "Joe"로 시작되는 Email 매개 변수가 포함된 경우에 일치합니다.

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>URL 쿼리 Regex

지정된 쿼리 문자열 매개 변수를 포함하는 요청을 식별합니다. 이 매개 변수는 지정된 [정규식](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)과 일치하는 값으로 설정됩니다.

**일치**/**일치하지 않음** 옵션은 URL 쿼리 Regex 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 지정된 정규식과 일치하는 URL 쿼리 문자열이 요청에 포함되어야 합니다.
- **일치하지 않음**: 지정된 정규식과 일치하지 않는 URL 쿼리 문자열이 요청에 포함되어야 합니다.

주요 정보:

- 지정된 정규식과 정확히 일치해야만 이 일치 조건이 충족됩니다.
    
- **대/소문자 무시** 옵션을 사용하여 쿼리 문자열을 비교할 때 대/소문자를 구분할지 여부를 제어합니다.
    
- 이 옵션의 경우, 쿼리 문자열은 쿼리 문자열의 물음표(?) 구분 기호 뒤에 나오는 첫 번째 문자로 시작됩니다.
    
- 특정 문자는 URL 인코딩이 필요합니다. 다음 문자를 URL 인코딩하려면 백분율 기호를 사용합니다.

   문자 | URL 인코딩 | 값
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   백분율 기호는 이스케이프해야 합니다.

- 정규식에 백슬래시를 포함하려면 특수 정규식 문자(예: \^$.+)를 두 번 이스케이프합니다.

   예를 들어:

   값 | 해석되는 값 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
   - 전체 캐시 채우기
   - 기본 내부 Max-Age
   - 강제 내부 Max-Age
   - 원본 No-Cache 무시
   - 내부 Max-Stale

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>URL 쿼리 와일드카드

지정된 값을 요청의 쿼리 문자열에 대해 비교합니다.

**일치**/**일치하지 않음** 옵션은 URL 쿼리 와일드카드 일치 조건이 충족되는 조건을 결정합니다.

- **일치**: 지정된 와일드카드 값과 일치하는 URL 쿼리 문자열이 요청에 포함되어야 합니다.
- **일치하지 않음**: 지정된 와일드카드 값과 일치하지 않는 URL 쿼리 문자열이 요청에 포함되어야 합니다.

주요 정보:

- 이 옵션의 경우, 쿼리 문자열은 쿼리 문자열의 물음표(?) 구분 기호 뒤에 나오는 첫 번째 문자로 시작됩니다.
- 매개 변수 값에는 [와일드카드 값](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)을 포함할 수 있습니다.
   - 각 매개 변수 값 패턴은 하나 이상의 별표(*)로 구성될 수 있으며 각 별표는 하나 이상의 문자 시퀀스와 일치할 수 있습니다.
   - 특정 문자는 URL 인코딩이 필요합니다. 다음 문자를 URL 인코딩하려면 백분율 기호를 사용합니다.

     문자 | URL 인코딩
     ----------|---------
     Space     | %20
     &         | %25

- 각각을 하나의 공백으로 구분하여 여러 값을 지정합니다.

   예를 들어: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- 지정된 쿼리 문자열 패턴 중 하나 이상과 정확히 일치해야만 일치 조건이 충족됩니다.
    
- **대/소문자 무시** 옵션을 사용하여 쿼리 문자열을 비교할 때 대/소문자를 구분할지 여부를 제어합니다.
    
- 캐시 설정을 추적하는 방식으로 인해 이 일치 조건은 다음 기능과 호환되지 않습니다.
   - 전체 캐시 채우기
   - 기본 내부 Max-Age
   - 강제 내부 Max-Age
   - 원본 No-Cache 무시
   - 내부 Max-Stale

#### <a name="sample-scenarios"></a>샘플 시나리오

다음 예제는 이 옵션이 특정 상황에서 작동하는 방식을 보여줍니다.

 이름                 | Description
 ---------------------|------------
user=joe              | 이 패턴은 요청된 URL의 쿼리 문자열이 "?user=joe"인 경우에 일치합니다.
\*user=\* \*optout=\* | 이 패턴은 CDN URL 쿼리에 user 또는 optout 매개 변수가 포함되는 경우에만 일치합니다.

[맨 위로 이동](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>다음 단계

- [Azure Content Delivery Network 개요](cdn-overview.md)
- [규칙 엔진 참조](cdn-verizon-premium-rules-engine-reference.md)
- [규칙 엔진 조건식](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [규칙 엔진 기능](cdn-verizon-premium-rules-engine-reference-features.md)
- [규칙 엔진을 사용하여 기본 HTTP 동작 재정의](cdn-verizon-premium-rules-engine.md)
