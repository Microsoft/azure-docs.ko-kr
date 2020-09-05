---
title: Azure Cache for Redis에 대한 ASP.NET 출력 캐시 공급자
description: Redis 용 Azure Cache를 사용 하 여 ASP.NET 페이지 출력을 캐시 하는 방법을 알아봅니다. Redis 출력 캐시 공급자는 출력 캐시 데이터에 대한 out-of-process 스토리지 메커니즘입니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: cb986f1e0172c4a44381b2f9cf043025cb5abe8a
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705030"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Azure Cache for Redis에 대한 ASP.NET 출력 캐시 공급자

Redis 출력 캐시 공급자는 출력 캐시 데이터에 대한 out-of-process 스토리지 메커니즘입니다. 이 데이터는 완전한 HTTP 응답(페이지 출력 캐싱)에 특별히 사용됩니다. 공급자가 ASP.NET 4에 도입된 새로운 출력 캐시 공급자 확장 포인트에 연결됩니다. ASP.NET Core 응용 프로그램의 경우 [ASP.NET Core에서 응답 캐싱을](https://docs.microsoft.com/aspnet/core/performance/caching/response)읽습니다. 

Redis 출력 캐시 공급자를 사용하려면 먼저 캐시를 구성한 다음, Redis 출력 개시 공급자 NuGet 패키지를 사용하여 사용자의 ASP.NET 애플리케이션을 구성합니다. 이 항목에서는 Redis 출력 캐시 공급자를 사용할 수 있도록 애플리케이션을 구성하는 지침을 제공합니다. Azure Cache for Redis 인스턴스를 만들고 구성하는 방법에 대한 자세한 내용은 [캐시 만들기](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)를 참조하세요.

## <a name="store-aspnet-page-output-in-the-cache"></a>ASP.NET 페이지 출력을 캐시에 저장

Azure Cache for Redis 세션 상태 NuGet 패키지를 사용하여 Visual Studio에서 클라이언트 애플리케이션을 구성하려면 **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.

`Package Manager Console` 창에서 다음 명령을 실행합니다.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Reids Output 캐시 공급자 NuGet 패키지는 StackExchange.Redis.StrongName 패키지에 종속성을 갖습니다. StackExchange.Redis.StrongName 패키지가 프로젝트에 나타나지 않는 경우 설치됩니다. Redis 출력 캐시 공급자 NuGet 패키지에 대한 자세한 내용은 [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet 패키지를 참조하세요.

>[!NOTE]
>강력한 이름의 StackExchange.Redis.StrongName 패키지 외에도 StackExchange.Redis 강력하지 않은 이름의 버전이 있습니다. 프로젝트에서 강력한 이름의 StackExchange 버전을 사용 하는 경우 제거 해야 합니다. 그렇지 않으면 프로젝트에서 이름 충돌이 발생 합니다. 이 패키지에 대한 자세한 내용은 [.NET 캐시 클라이언트 구성](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)을 참조하세요.

NuGet 패키지에서는 필수 어셈블리 참조를 다운로드하고 추가하며 web.config 파일에 다음 섹션을 추가합니다. 이 섹션에서는 Redis 출력 캐시 공급자를 사용하기 위해 ASP.NET 애플리케이션에 필수 구성을 포함합니다.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Microsoft Azure 포털의 캐시 블레이드에서 값으로 특성을 구성하고, 필요에 따라 다른 값을 구성합니다. 캐시 속성에 액세스하는 방법에 대한 지침은 [Azure Cache for Redis 설정 구성](cache-configure.md#configure-azure-cache-for-redis-settings)을 참조하세요.

| attribute | Type | 기본값 | Description |
| --------- | ---- | ------- | ----------- |
| *호스팅하기* | 문자열 | 호스트 | Redis 서버 IP 주소 또는 호스트 이름 |
| *port* | 양의 정수 | 6379 (TLS/SSL 아님)<br/>6380 (TLS/SSL) | Redis 서버 포트 |
| *accessKey* | 문자열 | "" | Redis 권한 부여를 사용 하는 경우 서버 암호를 Redis 합니다. 기본적으로이 값은 빈 문자열입니다. 즉, Redis 서버에 연결할 때 세션 상태 공급자가 암호를 사용 하지 않습니다. **Redis 서버가 Azure Redis Cache와 같은 공개적으로 액세스할 수 있는 네트워크에 있는 경우 보안을 향상 시키기 위해 Redis 권한 부여를 사용 하도록 설정 하 고 보안 암호를 제공 해야 합니다.** |
| *ssl* | boolean | **false** | TLS를 통해 Redis 서버에 연결할지 여부를 지정 합니다. Redis는 기본적으로이 값은 **false** 입니다. **기본적으로 SSL을 지 원하는 Azure Redis Cache를 사용 하는 경우 보안을 강화 하려면이를 true로 설정 해야 합니다.**<br/><br/>TLS가 아닌 포트는 새 캐시에 대해 기본적으로 사용 하지 않도록 설정 됩니다. TLS가 아닌 포트를 사용 하려면이 설정에 대해 **true** 를 지정 합니다. TLS 포트가 아닌 포트를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [캐시 구성](cache-configure.md) 항목에서 [액세스 포트](cache-configure.md#access-ports) 섹션을 참조 하세요. |
| *databaseIdNumber* | 양의 정수 | 0 | *이 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>사용할 Redis 데이터베이스를 지정 합니다. |
| *connectionTimeoutInMilliseconds* | 양의 정수 | StackExchange에서 제공 합니다. | Redis를 만들 때 *Connecttimeout* 을 설정 하는 데 사용 됩니다. |
| *operationTimeoutInMilliseconds* | 양의 정수 | StackExchange에서 제공 합니다. | Redis를 만들 때 *Synctimeout* 을 설정 하는 데 사용 됩니다. |
| *connectionString* (올바른 stackexchange 연결 문자열) | 문자열 | *해당 없음* | AppSettings 또는 web.config에 대 한 매개 변수 참조 또는 유효한 StackExchange 연결 문자열입니다. 이 특성은 *호스트*, *포트*, *AccessKey*, *ssl*및 기타 Redis 특성에 대 한 값을 제공할 수 있습니다. *Connectionstring*에 대해 자세히 살펴보려면 [특성 메모](#attribute-notes) 섹션에서 [connectionstring 설정](#setting-connectionstring) 을 참조 하세요. |
| *settingsClassName*<br/>*settingsMethodName* | 문자열<br/>문자열 | *해당 없음* | *이러한 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>이러한 특성을 사용 하 여 연결 문자열을 제공 합니다. *Settingsclassname* 은 *settingsclassname*에 지정 된 메서드를 포함 하는 어셈블리의 정규화 된 클래스 이름 이어야 합니다.<br/><br/>*Settingsmethodname* 에 의해 지정 된 메서드는 **문자열**의 반환 형식과 함께 public, static 및 void (매개 변수를 사용 하지 않음) 여야 합니다. 이 메서드는 실제 연결 문자열을 반환 합니다. |
| *loggingClassName*<br/>*loggingMethodName* | 문자열<br/>문자열 | *해당 없음* | *이러한 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>이러한 특성을 사용 하 여 Redis의 로그와 함께 세션 상태/출력 캐시의 로그를 제공 하 여 응용 프로그램을 디버그할 수 있습니다. *loggingClassName* 는 *loggingMethodName*에 의해 지정 된 메서드를 포함 하는 어셈블리의 정규화 된 클래스 이름 이어야 합니다.<br/><br/>*LoggingMethodName* 에 지정 된 메서드는 반환 형식이 **system.object**인 public, static 및 void (매개 변수를 사용 하지 않음) 여야 합니다. |
| *applicationName* | 문자열 | 현재 프로세스의 모듈 이름 또는 "/" | *SessionStateProvider만*<br/>*이 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>Redis cache에서 사용할 앱 이름 접두사입니다. 고객은 다양 한 용도로 동일한 Redis cache를 사용할 수 있습니다. 세션 키가 충돌 하지 않도록 하기 위해 응용 프로그램 이름 앞에 접두사를 붙일 수 있습니다. |
| *throwOnError* | boolean | true | *SessionStateProvider만*<br/>*이 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>오류가 발생할 때 예외를 throw 할지 여부입니다.<br/><br/>*ThrowOnError*에 대 한 자세한 내용은 [특성 메모](#attribute-notes) 섹션에서 [ *throwOnError* 에](#notes-on-throwonerror) 대 한 메모를 참조 하세요. |>*Redis*를 다시 작성 합니다. 단, |
| *retryTimeoutInMilliseconds* | 양의 정수 | 5,000 | *SessionStateProvider만*<br/>*이 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>작업이 실패 하는 경우 다시 시도 하는 시간입니다. 이 값이 *Operationtimeoutinmilliseconds*보다 작은 경우 공급자는 다시 시도 하지 않습니다.<br/><br/>*RetryTimeoutInMilliseconds*에 대 한 자세한 내용은 [특성 메모](#attribute-notes) 섹션에서 [ *retryTimeoutInMilliseconds* 에](#notes-on-retrytimeoutinmilliseconds) 대 한 메모를 참조 하세요. |
| *redisSerializerType* | 문자열 | *해당 없음* | Redis를 구현 하는 클래스의 정규화 된 어셈블리 형식 이름을 지정 합니다. 값을 serialize 및 deserialize 하는 사용자 지정 논리를 포함 하는 ISerializer 및입니다. 자세한 내용은 [특성 메모](#attribute-notes) 섹션의 [About *redisSerializerType* ](#about-redisserializertype) 를 참조 하세요. |

## <a name="attribute-notes"></a>특성 메모

### <a name="setting-connectionstring"></a>*ConnectionString* 설정

*ConnectionString* 의 값은 appsettings에 있는 경우 appsettings에서 실제 연결 문자열을 가져오는 데 키로 사용 됩니다. AppSettings 내에서 찾을 수 없는 경우 *connectionstring* 의 값은 web.config **connectionstring** 섹션 (해당 섹션이 있는 경우)에서 실제 연결 문자열을 가져오는 키로 사용 됩니다. AppSettings 또는 web.config **connectionstring** 섹션에 연결 문자열이 없는 경우 Redis를 만들 때 *connectionstring* 의 리터럴 값이 연결 문자열로 사용 됩니다.

다음 예에서는 *connectionString* 을 사용 하는 방법을 보여 줍니다.

#### <a name="example-1"></a>예 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

에서 `web.config` 실제 값 대신 위의 키를 매개 변수 값으로 사용 합니다.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>예제 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

에서 `web.config` 실제 값 대신 위의 키를 매개 변수 값으로 사용 합니다.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>예제 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>*ThrowOnError* 에 대 한 참고 사항

현재 세션 작업 중에 오류가 발생 하는 경우 세션 상태 공급자가 예외를 throw 합니다. 그러면 응용 프로그램이 종료 됩니다.

이 동작은 기존 ASP.NET 세션 상태 제공자 사용자의 기대를 지 원하는 방식으로 수정 되었으며, 원하는 경우 예외에 대 한 작업을 수행할 수 있는 기능도 제공 합니다. 다른 ASP.NET 세션 상태 공급자와 일치 하는 오류가 발생 하는 경우 기본 동작은 여전히 예외를 throw 합니다. 기존 코드는 이전과 동일 하 게 작동 해야 합니다.

*ThrowOnError* 를 **false**로 설정 하는 경우 오류가 발생할 때 예외를 throw 하는 대신 자동으로 실패 합니다. 오류가 있는지 확인 하 고 오류가 있는지 확인 하 고, 오류가 발생 한 경우 예외를 확인 하 고 *Redis*정적 속성을 확인 합니다.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>*RetryTimeoutInMilliseconds* 에 대 한 참고 사항

이를 통해 일부 세션 작업은 네트워크 결함 등의 이유로 인해 실패 시 다시 시도 해야 하는 경우를 단순화 하는 동시에 재시도 제한 시간을 제어 하거나 재시도를 완전히 옵트아웃 (opt out) 할 수 있는 몇 가지 재시도 논리를 제공 합니다.

*RetryTimeoutInMilliseconds* 를 숫자 (예: 2000)로 설정 하는 경우 세션 작업이 실패 하는 경우 2000 밀리초를 다시 시도 하 여 오류로 처리 합니다. 따라서 세션 상태 공급자가이 재시도 논리를 적용 하도록 하려면 제한 시간을 구성 하면 됩니다. 첫 번째 다시 시도는 20 밀리초 후에 수행 되며, 대부분의 경우 네트워크 결함이 발생 하는 경우에만 충분 합니다. 그 후에는 시간이 초과 될 때까지 1 초 마다 다시 시도 됩니다. 시간이 초과 되 면 한 번 더 다시 시도 하 여 제한 시간 (최대 1 초)이 잘리지 않도록 합니다.

응용 프로그램과 동일한 컴퓨터에서 Redis 서버를 실행 하는 경우와 같이 다시 시도 해야 한다고 생각 하지 않는 경우, 다시 시도 논리를 직접 처리 하려면 *retryTimeoutInMilliseconds* 를 0으로 설정 합니다.

### <a name="about-redisserializertype"></a>*RedisSerializerType* 정보

기본적으로 Redis에 값을 저장 하는 serialization은 **Binaryformatter** 클래스에서 제공 하는 이진 형식으로 수행 됩니다. *RedisSerializerType* 를 사용 하 여 **Redis** 를 구현 하는 클래스의 정규화 된 어셈블리 형식 이름을 지정 하 고, 값을 serialize 및 deserialize 할 수 있는 사용자 지정 논리를 사용 합니다. 예를 들어 다음은 JSON.NET를 사용 하는 Json serializer 클래스입니다.

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

이 클래스가 이름이 **MyCompanyDll**인 어셈블리에 정의 된 것으로 가정 하 여 *redisSerializerType* 매개 변수를 사용 하도록 설정할 수 있습니다.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>출력 캐시 지시문

출력을 캐시하고자 하는 각 페이지에 OutputCache 지시문을 추가합니다.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

이전 예제에서는 캐시된 페이지 데이터가 캐시에 60초 동안 머물게 되며, 각 매개 변수 조합에 따라 페이지의 다른 버전이 캐시됩니다. OutputCache 지시문에 대 한 자세한 내용은를 참조 하십시오 [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837) .

이 단계를 수행하면, 애플리케이션은 Redis 출력 캐시 공급자를 사용하도록 구성됩니다.

## <a name="third-party-output-cache-providers"></a>타사 출력 캐시 공급자

* [NCache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Apache Ignite](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>다음 단계

[Azure Cache for Redis에 대한 ASP.NET 세션 상태 제공자](cache-aspnet-session-state-provider.md)를 살펴봅니다.
