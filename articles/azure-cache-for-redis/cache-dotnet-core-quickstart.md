---
title: '빠른 시작: .NET Core 앱에서 Azure Cache for Redis 사용'
description: 이 빠른 시작에서는 .NET Core 앱에서 Azure Cache for Redis에 액세스하는 방법을 알아봅니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: dotnet
ms.custom: devx-track-csharp, mvc
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: bc045eba331c861801dceef7f00db42e851f8bb2
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88211373"
---
# <a name="quickstart-use-azure-cache-for-redis-with-a-net-core-app"></a>빠른 시작: .NET Core 앱에서 Azure Cache for Redis 사용

이 빠른 시작에서는 Azure 내 모든 애플리케이션에서 액세스할 수 있는 안전한 전용 캐시에 액세스할 수 있도록 Azure Cache for Redis를 .NET Core 앱에 통합합니다. 구체적으로 .NET Core 콘솔 앱에서 C# 코드를 통해 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) 클라이언트를 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)
- [.NET Framework 4 이상](https://www.microsoft.com/net/download/dotnet-framework-runtime) - StackEdchange.Redis 클라이언트를 사용하려면 필요합니다.

## <a name="create-a-cache"></a>캐시 만들기
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

**호스트 이름** 및 **기본** 액세스 키를 적어 둡니다. 나중에 이러한 값을 사용하여 *CacheConnection* 비밀을 구성합니다.



## <a name="create-a-console-app"></a>콘솔 앱 만들기

새 명령 창을 열고, 다음 명령을 실행하여 새 .NET Core 콘솔 앱을 만듭니다.

```
dotnet new console -o Redistest
```

명령 창에서 새 *Redistest* 프로젝트 디렉터리로 변경합니다.



## <a name="add-secret-manager-to-the-project"></a>프로젝트에 암호 관리자 추가

이 섹션에서는 프로젝트에 [암호 관리자 도구](https://docs.microsoft.com/aspnet/core/security/app-secrets)를 추가합니다. 암호 관리자 도구는 개발 작업에 대한 중요한 데이터를 프로젝트 트리 외부에 저장합니다. 이 방법을 사용하면 소스 코드 내에서 앱 암호를 실수로 공유하는 경우를 방지할 수 있습니다.

*Redistest.csproj* 파일을 엽니다. *Microsoft.Extensions.SecretManager.Tools*를 포함할 `DotNetCliToolReference` 요소를 추가합니다. 또한 아래와 같이 `UserSecretsId` 요소도 추가한 후 파일을 저장합니다.

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <UserSecretsId>Redistest</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
</Project>
```

다음 명령을 실행하여 프로젝트에 *Microsoft.Extensions.Configuration.UserSecrets* 패키지를 추가합니다.

```
dotnet add package Microsoft.Extensions.Configuration.UserSecrets
```

다음 명령을 실행하여 패키지를 복원합니다.

```
dotnet restore
```

명령 창에서 캐시 이름과 기본 액세스 키에 대한 자리 표시자(꺾쇠 괄호 포함)를 바꾼 다음, 다음 명령을 실행하여 *CacheConnection*이라는 새 비밀을 저장합니다.

```
dotnet user-secrets set CacheConnection "<cache name>.redis.cache.windows.net,abortConnect=false,ssl=true,password=<primary-access-key>"
```

다음 `using` 문을 *Program.cs*에 추가합니다.

```csharp
using Microsoft.Extensions.Configuration;
```

다음 멤버를 *Program.cs*의 `Program` 클래스에 추가합니다. 다음 코드에서는 구성을 초기화하여 Azure Cache for Redis 연결 문자열에 대한 사용자 비밀에 액세스합니다.

```csharp
private static IConfigurationRoot Configuration { get; set; }
const string SecretName = "CacheConnection";

private static void InitializeConfiguration()
{
    var builder = new ConfigurationBuilder()
        .AddUserSecrets<Program>();

    Configuration = builder.Build();
}
```

## <a name="configure-the-cache-client"></a>캐시 클라이언트 구성

이 섹션에서는 .NET용 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) 클라이언트를 사용하도록 콘솔 애플리케이션을 구성합니다.

명령 창에서 *Redistest* 프로젝트 디렉터리에서 다음 명령을 실행합니다.

```
dotnet add package StackExchange.Redis
```

설치가 완료되면 *StackExchange.Redis* 캐시 클라이언트를 프로젝트에 사용할 수 있습니다.


## <a name="connect-to-the-cache"></a>캐시에 연결

다음 `using` 문을 *Program.cs*에 추가합니다.

```csharp
using StackExchange.Redis;
```

Azure Cache for Redis 연결은 `ConnectionMultiplexer` 클래스로 관리됩니다. 이 클래스는 클라이언트 애플리케이션 전체에서 공유하고 다시 사용해야 합니다. 각 작업에 대해 새 연결을 만들지 마세요. 

*Program.cs*에서 콘솔 애플리케이션의 `Program` 클래스에 다음 멤버를 추가합니다.

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    string cacheConnection = Configuration[SecretName];
    return ConnectionMultiplexer.Connect(cacheConnection);
});

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

애플리케이션에서 `ConnectionMultiplexer` 인스턴스를 공유하는 이 방법은 연결된 인스턴스를 반환하는 정적 속성을 사용합니다. 이 코드에서는 연결된 단일 `ConnectionMultiplexer` 인스턴스만 초기화하는 스레드로부터 안전한 방법을 제공합니다. `abortConnect`는 false로 설정되며, 이는 Azure Cache for Redis에 연결이 설정되지 않은 경우에도 호출이 성공한다는 것을 의미합니다. `ConnectionMultiplexer`의 한 가지 주요 기능은 네트워크 문제 또는 다른 원인이 해결되면 캐시에 연결이 자동으로 복원된다는 점입니다.

*CacheConnection* 비밀의 값은 비밀 관리자 구성 공급자를 사용하여 액세스하고 암호 매개 변수로 사용됩니다.

## <a name="executing-cache-commands"></a>캐시 명령 실행

*Program.cs*에서 콘솔 애플리케이션의 `Program` 클래스에 대한 `Main` 프로시저에 다음 코드를 추가합니다.

```csharp
static void Main(string[] args)
{
    InitializeConfiguration();

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = lazyConnection.Value.GetDatabase();

    // Perform cache operations using the cache object...

    // Simple PING command
    string cacheCommand = "PING";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

    // Simple get and put of integral data types into the cache
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    cacheCommand = "SET Message \"Hello! The cache is working from a .NET Core console app!\"";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
    Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET Core console app!").ToString());

    // Demonstrate "SET Message" executed as expected...
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    // Get the client list, useful to see if connection list is growing...
    cacheCommand = "CLIENT LIST";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : \n" + cache.Execute("CLIENT", "LIST").ToString().Replace("id=", "id="));

    lazyConnection.Value.Dispose();
}
```

*Program.cs*를 저장합니다.

Azure Cache for Redis에는 Azure Cache for Redis 내에서 데이터를 논리적으로 분리하는 데 사용할 수 있는 구성 가능한 수의 데이터베이스(기본값 16)가 있습니다. 이 코드에서는 기본 데이터베이스인 DB 0에 연결합니다. 자세한 내용은 [Redis 데이터베이스란?](cache-development-faq.md#what-are-redis-databases) 및 [기본 Redis 서버 구성](cache-configure.md#default-redis-server-configuration)을 참조하세요.

캐시 항목은 `StringSet` 및 `StringGet` 메서드를 사용하여 저장하고 검색할 수 있습니다.

Redis는 대부분의 데이터를 Redis 문자열로 저장하지만, 이 문자열은 캐시에 .NET 개체를 저장할 때 사용할 수 있는 직렬화된 이진 데이터를 포함하여 다양한 데이터 유형을 포함할 수 있습니다.

명령 창에서 다음 명령을 실행하여 앱을 빌드합니다.

```
dotnet build
```

그런 다음, 다음 명령을 사용하여 앱을 실행합니다.

```
dotnet run
```

아래 예제에서는 이전에 Azure Portal에서 Redis 콘솔을 사용하여 설정된 캐시된 값이 있는 `Message` 키를 볼 수 있습니다. 앱에서 캐시된 값을 업데이트했습니다. 또한 앱에서 `PING` 및 `CLIENT LIST` 명령을 실행했습니다.

![콘솔 앱 부분](./media/cache-dotnet-core-quickstart/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>캐시의 .NET 개체 사용

Azure Cache for Redis는 .NET 개체 및 기본 데이터 유형을 캐시할 수 있지만 .NET 개체를 캐시하려면 먼저 직렬화해야 합니다. .NET 개체 직렬화는 애플리케이션 개발자의 책임이며 개발자는 유연하게 직렬 변환기를 선택할 수 있습니다.

개체를 직렬화하는 간단한 방법 중 하나는 [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)에서 `JsonConvert` 직렬화 메서드를 사용하고 JSON 간에 직렬화하는 것입니다. 이 섹션에서는 .NET 개체를 캐시에 추가합니다.

다음 명령을 실행하여 *Newtonsoft.json* 패키지를 앱에 추가합니다.

```
dotnet add package Newtonsoft.json
```

다음 `using` 문을 *Program.cs*의 맨 위쪽에 추가합니다.

```csharp
using Newtonsoft.Json;
```

다음 `Employee` 클래스 정의를 *Program.cs*에 추가합니다.

```csharp
class Employee
{
    public string Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }

    public Employee(string EmployeeId, string Name, int Age)
    {
        this.Id = EmployeeId;
        this.Name = Name;
        this.Age = Age;
    }
}
```

*Program.cs*의 `Main()` 프로시저의 맨 아래쪽에서 `Dispose()`에 대한 호출 앞에 직렬화된 .NET 개체를 캐시하고 검색하도록 다음 코드 줄을 추가합니다.

```csharp
    // Store .NET object to cache
    Employee e007 = new Employee("007", "Davide Columbo", 100);
    Console.WriteLine("Cache response from storing Employee .NET object : " + 
    cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

    // Retrieve .NET object from cache
    Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
    Console.WriteLine("Deserialized Employee .NET object :\n");
    Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
    Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
    Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

*Program.cs*를 저장하고 다음 명령을 사용하여 앱을 다시 빌드합니다.

```
dotnet build
```

다음 명령으로 앱을 실행하여 .NET 개체의 직렬화를 테스트합니다.

```
dotnet run
```

![완료된 콘솔 앱](./media/cache-dotnet-core-quickstart/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>리소스 정리

다음 자습서를 계속 진행하려는 경우 이 빠른 시작에서 만든 리소스를 그대로 두었다가 다시 사용할 수 있습니다.

그렇지 않고, 빠른 시작 샘플 애플리케이션 사용이 끝나면 이 빠른 시작에서 만든 Azure 리소스를 삭제하여 요금이 청구되는 것을 방지할 수 있습니다. 

> [!IMPORTANT]
> 리소스 그룹 삭제는 취소할 수 없으며 해당 리소스 그룹 및 해당 그룹 안에 있는 모든 리소스는 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 유지하려는 리소스가 포함된 기존 리소스 그룹 내에 이 샘플을 호스트하기 위한 리소스를 만든 경우 리소스 그룹을 삭제하는 대신, 해당 블레이드에서 각 리소스를 개별적으로 삭제할 수 있습니다.
>

[Azure 포털](https://portal.azure.com) 에 로그인하고 **리소스 그룹**을 클릭합니다.

**이름을 기준으로 필터링...** 텍스트 상자에 리소스 그룹의 이름을 입력합니다. 이 문서의 지침에서는 *TestResources*라는 리소스 그룹을 사용했습니다. 결과 목록의 리소스 그룹에서 **...** 를 클릭한 다음, **리소스 그룹 삭제**를 클릭합니다.

![DELETE](./media/cache-dotnet-core-quickstart/cache-delete-resource-group.png)

리소스 그룹을 삭제할지 확인하는 메시지가 표시됩니다. 리소스 그룹의 이름을 입력하여 확인한 후 **삭제**를 클릭합니다.

잠시 후, 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 삭제됩니다.



<a name="next-steps"></a>

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 .NET Core 애플리케이션에서 Azure Cache for Redis를 사용하는 방법을 알아보았습니다. ASP.NET 웹앱에서 Azure Cache for Redis를 사용하려면 다음 빠른 시작으로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Cache for Redis를 사용하는 ASP.NET 웹앱 만들기](./cache-web-app-howto.md)

클라우드 비용을 최적화하여 비용을 절감하고 싶습니까?

> [!div class="nextstepaction"]
> [Cost Management를 통한 비용 분석 시작](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)