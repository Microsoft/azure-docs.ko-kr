---
title: Azure Durable Functions 단위 테스트
description: Durable Functions를 단위 테스트하는 방법을 알아봅니다.
services: functions
author: kadimitr
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 12/11/2018
ms.date: 03/25/2019
ms.author: v-junlch
ms.openlocfilehash: 69cf91f1448e36353f83de7a271abb3b53858bb0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648468"
---
# <a name="durable-functions-unit-testing"></a>Durable Functions 단위 테스트

단위 테스트는 최신 소프트웨어 개발 방법의 중요한 부분입니다. 단위 테스트는 비즈니스 논리 동작을 확인하고 향후 중요하지 않은 변경 내용을 도입하지 못하게 방지합니다. Durable Functions는 복잡성이 쉽게 증가할 수 있으므로 단위 테스트를 도입하면 변경 내용을 방지하는 데 도움이 됩니다. 다음 섹션에서는 세 가지 함수 형식 오케스트레이션 클라이언트, 오케스트레이터 및 작업 함수를 단위 테스트하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 예제를 살펴보려면 다음과 같은 개념과 프레임워크에 대한 지식이 필요합니다.

* 단위 테스트

* 지속성 함수

* [xUnit](https://xunit.github.io/) - 프레임워크 테스트

* [moq](https://github.com/moq/moq4) - 모의 프레임워크 만들기

## <a name="base-classes-for-mocking"></a>모의 동작에 대한 기본 클래스

모의 동작은 Durable Functions의 세 가지 추상 클래스를 통해 지원됩니다.

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

* [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html)

이러한 클래스는 오케스트레이션 클라이언트, 오케스트레이터 및 작업 메서드를 정의하는 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html), [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) 및 [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html)에 대한 기본 클래스입니다. 모의 동작은 기본 클래스 메서드에 대한 예상 동작을 설명하므로 단위 테스트로 비즈니스 논리를 확인할 수 있습니다. 오케스트레이션 클라이언트 및 오케스트레이터에서 비즈니스 논리를 단위 테스트하는 2단계 워크플로가 있습니다.

1. 오케스트레이션 클라이언트 및 오케스트레이터의 서명을 정의할 때 구체적인 구현 대신 기본 클래스를 사용합니다.
2. 단위 테스트에서 모의 기본 클래스 동작을 만들어서 비즈니스 논리를 확인합니다.

자세한 내용은 오케스트레이션 클라이언트 바인딩 및 오케스트레이터 트리거 바인딩을 사용하는 함수를 테스트하는 방법에 대한 다음 그래프를 참조하세요.

## <a name="unit-testing-trigger-functions"></a>트리거 함수 단위 테스트

이 섹션에서는 단위 테스트로 새 오케스트레이션을 시작하는 다음 HTTP 트리거 함수의 논리를 확인합니다.

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace VSSample
{
    public static class HttpStart
    {
        [FunctionName("HttpStart")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
            [OrchestrationClient] DurableOrchestrationClientBase starter,
            string functionName,
            ILogger log)
        {
            // Function input comes from the request content.
            dynamic eventData = await req.Content.ReadAsAsync<object>();
            string instanceId = await starter.StartNewAsync(functionName, eventData);

            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

단위 테스트 작업에서는 응답 페이로드에 제공되는 `Retry-After` 헤더의 값을 확인합니다. 따라서 단위 테스트에서는 모의 [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) 메서드를 만들어서 동작의 예측 가능성을 보장합니다.

첫 번째로, 모의 기본 클래스 [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)가 필요합니다. 모의 동작은 [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)를 구현하는 새 클래스일 수 있습니다. 그러나 [moq](https://github.com/moq/moq4) 같은 모의 프레임워크를 사용하면 프로세스가 간단해집니다.

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

그런 후 잘 알려진 인스턴스 ID를 반환하는 모의 `StartNewAsync` 메서드를 만듭니다.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

다음으로 항상 빈 HTTP 200 응답을 반환하는 모의 `CreateCheckStatusResponse`를 만듭니다.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
            Headers =
            {
                RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
            }
        });
```

모의 `TraceWriter`도 만듭니다.

```csharp
    // Mock TraceWriter
    var traceWriterMock = new Mock<TraceWriter>(TraceLevel.Info);

```  

이제 `Run` 메서드는 단위 테스트에서 호출됩니다.

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object,
        functionName,
        traceWriterMock.Object);
 ```

 마지막 단계는 출력을 예상 값과 비교하는 것입니다.

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

모든 단계를 결합하면 단위 테스트가 다음과 같은 코드를 갖게 됩니다.

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

namespace VSSample.Tests
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using System.Net.Http.Headers;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Extensions.Logging;
    using Moq;
    using Xunit;

    public class HttpStartTests
    {
        [Fact]
        public async Task HttpStart_returns_retryafter_header()
        {
            // Define constants
            const string functionName = "SampleFunction";
            const string instanceId = "7E467BDB-213F-407A-B86A-1954053D3C24";

            // Mock TraceWriter
            var loggerMock = new Mock<ILogger>();

            // Mock DurableOrchestrationClientBase
            var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();

            // Mock StartNewAsync method
            durableOrchestrationClientBaseMock.
                Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
                ReturnsAsync(instanceId);

            // Mock CreateCheckStatusResponse method
            durableOrchestrationClientBaseMock
                .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
                .Returns(new HttpResponseMessage
                {
                    StatusCode = HttpStatusCode.OK,
                    Content = new StringContent(string.Empty),
                    Headers =
                    {
                        RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
                    }
                });

            // Call Orchestration trigger function
            var result = await HttpStart.Run(
                new HttpRequestMessage()
                {
                    Content = new StringContent("{}", Encoding.UTF8, "application/json"),
                    RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
                },
                durableOrchestrationClientBaseMock.Object,
                functionName,
                loggerMock.Object);

            // Validate that output is not null
            Assert.NotNull(result.Headers.RetryAfter);

            // Validate output's Retry-After header value
            Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
        }
    }
}
```

## <a name="unit-testing-orchestrator-functions"></a>오케스트레이터 함수 단위 테스트

오케스트레이터 함수는 일반적으로 훨씬 많은 비즈니스 논리를 갖고 있기 때문에 훨씬 흥미로운 단위 테스트 대상입니다.

이 섹션에서는 단위 테스트로 `E1_HelloSequence` 오케스트레이터 함수 출력의 유효성을 검사합니다.

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;

namespace VSSample
{
    public static class HelloSequence
    {
        [FunctionName("E1_HelloSequence")]
        public static async Task<List<string>> Run(
            [OrchestrationTrigger] DurableOrchestrationContextBase context)
        {
            var outputs = new List<string>();

            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello_DirectInput", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("E1_SayHello")]
        public static string SayHello([ActivityTrigger] DurableActivityContextBase context)
        {
            string name = context.GetInput<string>();
            return $"Hello {name}!";
        }

        [FunctionName("E1_SayHello_DirectInput")]
        public static string SayHelloDirectInput([ActivityTrigger] string name)
        {
            return $"Hello {name}!";
        }
    }
 }
```

단위 테스트 코드는 가장 먼저 모의 동작을 만듭니다.

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

그런 후 모의 작업 메서드 호출을 만듭니다.

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

다음으로 단위 테스트에서 `HelloSequence.Run` 메서드를 호출합니다.

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

마지막으로 출력의 유효성을 검사합니다.

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

모든 단계를 결합하면 단위 테스트가 다음과 같은 코드를 갖게 됩니다.

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

namespace VSSample.Tests
{
    using System.Threading.Tasks;
    using Microsoft.Azure.WebJobs;
    using Moq;
    using Xunit;

    public class HelloSequenceTests
    {
        [Fact]
        public async Task Run_returns_multiple_greetings()
        {
            var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
            durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
            durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
            durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello_DirectInput", "London")).ReturnsAsync("Hello London!");

            var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);

            Assert.Equal(3, result.Count);
            Assert.Equal("Hello Tokyo!", result[0]);
            Assert.Equal("Hello Seattle!", result[1]);
            Assert.Equal("Hello London!", result[2]);
        }
    }
}
```

## <a name="unit-testing-activity-functions"></a>작업 함수 단위 테스트

작업 함수는 비 내구성 함수와 동일한 방식으로 단위 테스트할 수 있습니다.

이 섹션에서는 단위 테스트로 `E1_SayHello` 작업 함수 동작의 유효성을 검사합니다.

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;

namespace VSSample
{
    public static class HelloSequence
    {
        [FunctionName("E1_HelloSequence")]
        public static async Task<List<string>> Run(
            [OrchestrationTrigger] DurableOrchestrationContextBase context)
        {
            var outputs = new List<string>();

            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello_DirectInput", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("E1_SayHello")]
        public static string SayHello([ActivityTrigger] DurableActivityContextBase context)
        {
            string name = context.GetInput<string>();
            return $"Hello {name}!";
        }

        [FunctionName("E1_SayHello_DirectInput")]
        public static string SayHelloDirectInput([ActivityTrigger] string name)
        {
            return $"Hello {name}!";
        }
    }
 }
```

그리고 단위 테스트에서 출력의 형식을 확인합니다. 단위 테스트는 매개 변수 유형을 직접 사용하거나 모의 [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html) 클래스 동작을 만들 수 있습니다.

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

namespace VSSample.Tests
{
    using Microsoft.Azure.WebJobs;
    using Xunit;
    using Moq;

    public class HelloSequenceActivityTests
    {
        [Fact]
        public void SayHello_returns_greeting()
        {
            var durableActivityContextMock = new Mock<DurableActivityContextBase>();
            durableActivityContextMock.Setup(x => x.GetInput<string>()).Returns("John");
            var result = HelloSequence.SayHello(durableActivityContextMock.Object);
            Assert.Equal("Hello John!", result);
        }

        [Fact]
        public void SayHello_returns_greeting_direct_input()
        {
            var result = HelloSequence.SayHelloDirectInput("John");
            Assert.Equal("Hello John!", result);
        }
    }
}
```
## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [xUnit에 대해 자세히 알아보기](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [moq에 대해 자세히 알아보기 ](https://github.com/Moq/moq4/wiki/Quickstart)

<!-- Update_Description: wording update -->