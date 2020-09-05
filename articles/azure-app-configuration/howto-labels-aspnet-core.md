---
title: 환경별 구성 사용
titleSuffix: Azure App Configuration
description: 레이블을 사용하여 환경별 구성 값을 제공합니다.
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: 689fdbd444aa4b20f5bef225faa259788c47cf9d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206661"
---
# <a name="use-labels-to-enable-configurations-for-different-environments"></a>레이블을 사용하여 다양한 환경에 대한 구성을 사용할 수 있도록 설정

많은 애플리케이션은 환경마다 다른 구성을 사용해야 합니다. 애플리케이션에 백 엔드 데이터베이스에 사용할 연결 문자열을 정의하는 구성 값이 있다고 가정합니다. 애플리케이션 개발자는 프로덕션 환경에서 사용하는 것과 다른 데이터베이스를 사용합니다. 애플리케이션이 개발 환경에서 프로덕션 환경으로 이동할 때 애플리케이션에서 사용하는 데이터베이스 연결 문자열을 변경해야 합니다.

Azure App Configuration에서는 *레이블*을 사용하여 동일한 키에 대해 서로 다른 값을 정의할 수 있습니다. 예를 들어 개발 및 프로덕션에 대해 다른 값을 사용하여 단일 키를 정의할 수 있습니다. App Configuration에 연결할 때 로드할 레이블을 지정할 수 있습니다.

이 기능을 설명하기 위해 [빠른 시작: Azure App Configuration을 사용하여 ASP.NET Core 앱 만들기](./quickstart-aspnet-core-app.md)에서 만든 웹앱을 수정하여 개발 및 프로덕션 간 다른 구성 설정을 사용합니다. 계속하기 전에 빠른 시작을 완료합니다.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>구성 값을 추가할 때 레이블 지정

Azure Portal에서 **구성 탐색기**로 이동하고 빠른 시작에서 만든 *TestApp:Settings:FontColor* 키를 찾습니다. 상황에 맞는 메뉴를 선택한 다음, **값 추가**를 선택합니다.

> [!div class="mx-imgBorder"]
> ![값 추가 메뉴 항목](media/labels-add-value.png)

**값 추가** 화면에서 **값**에 **빨간색**, **레이블**에 **개발**을 입력합니다. **콘텐츠 형식**은 비워 둡니다. **적용**을 선택합니다.

## <a name="load-configuration-values-with-a-specified-label"></a>지정된 레이블을 사용하여 구성 값 로드

기본적으로 Azure App Configuration은 레이블 없이 구성 값만 로드합니다. 구성 값에 대한 레이블을 정의한 경우 App Configuration에 연결할 때 사용할 레이블을 지정해야 합니다.

이전 섹션에서는 개발 환경에 대한 다른 구성 값을 만들었습니다. `HostingEnvironment.EnvironmentName` 변수를 사용하여 앱이 현재 실행되는 환경을 동적으로 결정할 수 있습니다. 자세히 알아보려면 [ASP.NET Core에서 여러 환경 사용](/aspnet/core/fundamentals/environments)을 참조하세요.

환경 이름을 `Select` 메서드에 전달하여 현재 환경에 해당하는 레이블을 사용하여 구성 값을 로드합니다.

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> 위의 코드 조각에서는 `AppConfigConnectionString`이라는 환경 변수에서 App Configuration 연결 문자열을 로드합니다. 이 환경 변수를 올바르게 설정해야 합니다.

`Select` 메서드는 두 번 호출됩니다. 처음에는 레이블이 없는 구성 값을 로드합니다. 그런 다음, 현재 환경에 해당하는 레이블을 사용하여 구성 값을 로드합니다. 이러한 환경 관련 값은 레이블 없이 해당하는 모든 값을 재정의합니다. 모든 키에 대해 환경 특정 값을 정의할 필요는 없습니다. 현재 환경에 해당하는 레이블이 있는 값이 키에 없는 경우에는 레이블이 없는 값이 사용됩니다.

## <a name="test-in-different-environments"></a>다양한 환경에서 테스트

`Properties` 디렉터리에서 `launchSettings.json` 파일을 엽니다. `profiles`에서 `config` 항목을 찾습니다. `environmentVariables` 섹션에서 `ASPNETCORE_ENVIRONMENT` 변수를 `Production`으로 설정합니다.

새 값이 설정된 상태에서 애플리케이션을 빌드하고 실행합니다.

```dotnetcli
dotnet build
dotnet run
```

웹 브라우저를 사용하여 `http://localhost:5000`으로 이동합니다. 글꼴 색이 검정색임을 알 수 있습니다.

![프로덕션 구성으로 실행되는 웹 애플리케이션](media/labels-website-prod.png)

`ASPNETCORE_ENVIRONMENT` 변수를 `Development`로 설정하려면 `launchSettings.json`을 업데이트합니다. `dotnet run`을 다시 실행합니다. 

이제 글꼴 색은 빨간색입니다. 이제 애플리케이션에서 `Development` 레이블이 있는 `TestApp:Settings:FontColor` 값을 사용하기 때문입니다. 다른 모든 구성 값은 프로덕션 값과 동일하게 유지됩니다.

![개발 구성으로 실행되는 웹 애플리케이션](media/labels-website-dev.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [ASP.NET Core의 구성](/aspnet/core/fundamentals/configuration/)
