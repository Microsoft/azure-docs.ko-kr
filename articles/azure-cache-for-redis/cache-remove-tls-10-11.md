---
title: Redis 용 Azure Cache를 사용 하 여 TLS 1.0 및 1.1을 제거 합니다.
description: Redis 용 Azure Cache와 통신할 때 응용 프로그램에서 TLS 1.0 및 1.1을 제거 하는 방법에 대해 알아봅니다.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 77f526470204204ef2a801575bb4e8d7e364ffed
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260159"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Redis 용 Azure Cache를 사용 하 여 TLS 1.0 및 1.1을 제거 합니다.

TLS (전송 계층 보안) 버전 1.2 이상에 대한 독점 사용에 대한 업계 전체의 푸시가 있습니다. TLS 버전 1.0 및 1.1은 비스 트 및 POODLE 같은 공격에 취약 하 고 다른 일반적인 취약성 및 노출 (CVE) 약점을 보유 하는 것으로 알려져 있습니다. 또한 PCI (결제 카드 산업) 규정 준수 표준에 의해 권장 되는 최신 암호화 방법 및 암호 그룹을 지원 하지 않습니다. 이 [TLS 보안 블로그](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) 는 이러한 취약성에 대해 자세히 설명 합니다.

이러한 노력의 일환으로 Redis 용 Azure Cache를 다음과 같이 변경 합니다.

* **1 단계:** 새로 만든 캐시 인스턴스에 대해 기본 최소 TLS 버전을 1.2로 구성 합니다.  이 시점에서 기존 캐시 인스턴스는 업데이트 되지 않습니다.  필요한 경우 이전 버전과의 호환성을 위해 [최소 TLS 버전](cache-configure.md#access-ports) 을 1.0 또는 1.1로 다시 변경할 수 있습니다.  이러한 변경은 Azure Portal 또는 다른 관리 Api를 통해 수행할 수 있습니다.
* **2 단계:** TLS 버전 1.0 및 1.1은 지원 하지 않습니다. 이렇게 변경한 후에는 응용 프로그램에서 TLS 1.2 이상 버전을 사용 하 여 캐시와 통신 해야 합니다.

또한이 변경의 일환으로, 안전 하지 않은 이전 암호 제품군에 대한 지원이 제거 될 예정입니다.  최소 TLS 버전 1.2를 사용 하 여 캐시를 구성 하는 경우 지원 되는 암호 제품군은 다음으로 제한 됩니다.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

이 문서에서는 이러한 이전 TLS 버전에 대한 종속성을 검색 하 고 응용 프로그램에서 제거 하는 방법에 대한 일반적인 지침을 제공 합니다.

이러한 변경 내용이 적용 되는 날짜는 다음과 같습니다.

| 클라우드               | 1 단계 시작 날짜 | 2 단계 시작 날짜 |
|---------------------|--------------------|--------------------|
| Azure (전역)      |  2020 년 1 월 13 일  | 2020 년 3 월 31 일     |
| Azure Government    |  3 월 13 일, 2020    | 5 월 11 일, 2020       |
| Azure Germany       |  3 월 13 일, 2020    | 5 월 11 일, 2020       |
| Azure China         |  3 월 13 일, 2020    | 5 월 11 일, 2020       |

## <a name="check-whether-your-application-is-already-compliant"></a>응용 프로그램이 이미 호환 되는지 확인

응용 프로그램이 TLS 1.2에서 작동 하는지 여부를 확인 하는 가장 쉬운 방법은 사용 하는 테스트 또는 스테이징 캐시에서 **최소 tls 버전** 값을 tls 1.2로 설정 하는 것입니다. **최소 TLS 버전** 설정은 Azure Portal의 캐시 인스턴스에 대한 [고급 설정](cache-configure.md#advanced-settings) 에 있습니다. 이러한 변경 후 응용 프로그램이 계속 해 서 작동 하는 경우에는 정책을 준수 하는 것입니다. 응용 프로그램에서 특별히 TLS 1.2을 사용 하도록 설정 하기 위해 사용 하는 일부 Redis 클라이언트 라이브러리를 구성 해야 할 수도 있으므로 해당 보안 프로토콜을 통해 Redis 용 Azure 캐시에 연결할 수 있습니다.

## <a name="configure-your-application-to-use-tls-12"></a>TLS 1.2를 사용 하도록 응용 프로그램 구성

대부분의 응용 프로그램은 Redis 클라이언트 라이브러리를 사용 하 여 해당 캐시와의 통신을 처리 합니다. 다음은 다양 한 프로그래밍 언어와 프레임 워크에서 TLS 1.2을 사용 하기 위해 널리 사용 되는 일부 클라이언트 라이브러리를 구성 하는 지침입니다.

### <a name="net-framework"></a>.NET Framework

Redis .NET 클라이언트는 .NET Framework 4.5.2 이전 버전에서 기본적으로 가장 이른 TLS 버전을 사용 하 고 .NET Framework 4.6 이상에서 최신 TLS 버전을 사용 합니다. 이전 버전의 .NET Framework 사용 하는 경우 TLS 1.2을 수동으로 사용 하도록 설정할 수 있습니다.

* **Stackexchange:** 연결 문자열에 `ssl=true` 및 `sslprotocols=tls12`를 설정 합니다.
* **Servicestack. Redis:** [Servicestack 지침](https://github.com/ServiceStack/ServiceStack.Redis/pull/247)을 따릅니다.

### <a name="net-core"></a>.NET Core

Redis .NET Core 클라이언트는 기본적으로 최신 TLS 버전을 사용 합니다.

### <a name="java"></a>Java

Redis Java 클라이언트는 Java 버전 6 또는 이전 버전에서 TLS 1.0를 사용 합니다. 캐시에서 TLS 1.0이 사용 하지 않도록 설정 된 경우 jedis, Redis, Redisson은 Azure Cache for에 연결할 수 없습니다. 새 TLS 버전을 사용 하도록 Java framework를 업그레이드 합니다.

Java 7의 경우 Redis 클라이언트는 기본적으로 TLS 1.2을 사용 하지 않지만이에 대해 구성할 수 있습니다. Jedis를 사용 하면 다음 코드 조각을 사용 하 여 기본 TLS 설정을 지정할 수 있습니다.

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

이 클라이언트는 tls 버전 지정을 지원 하지 않으므로, 캐시가 TLS 1.2 연결만 수락 하는 경우 중단 됩니다. 이러한 클라이언트에 대한 픽스를 검토 하 고 있으므로이 지원으로 업데이트 된 버전에 대한 패키지를 확인 합니다.

Java 8에서 TLS 1.2은 기본적으로 사용 되며 대부분의 경우 클라이언트 구성에 대한 업데이트가 필요 하지 않습니다. 안전을 위해 응용 프로그램을 테스트 합니다.

### <a name="nodejs"></a>Node.js

Redis 및 IORedis 노드는 기본적으로 TLS 1.2를 사용 합니다.

### <a name="php"></a>PHP

Php 7은 TLS 1.0만 지원 하기 때문에 PHP 7의 Predis 작동 하지 않습니다. PHP 7.2.1 또는 이전 버전에서 Predis는 기본적으로 TLS 1.0 또는 1.1를 사용 합니다. 클라이언트 인스턴스를 만들 때 TLS 1.2을 지정할 수 있습니다.

``` PHP
$redis=newPredis\Client([
    'scheme'=>'tls',
    'host'=>'host',
    'port'=>6380,
    'password'=>'password',
    'ssl'=>[
        'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
    ],
]);
```

PHP 7.3 이상에서 Predis은 최신 TLS 버전을 사용 합니다.

PhpRedis는 PHP 버전에서 TLS를 지원 하지 않습니다.

### <a name="python"></a>Python

Redis-py는 기본적으로 TLS 1.2를 사용 합니다.

### <a name="go"></a>이동

Redigo은 기본적으로 TLS 1.2를 사용 합니다.

## <a name="additional-information"></a>추가 정보

- [Redis에 대한 Azure 캐시를 구성 하는 방법](cache-configure.md)
