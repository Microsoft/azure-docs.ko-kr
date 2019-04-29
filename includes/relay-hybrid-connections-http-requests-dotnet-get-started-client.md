---
title: 포함 파일
description: 포함 파일
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 08/16/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 8d73a22473ffff358c7424249c7581f6af740718
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749518"
---
### <a name="create-a-console-application"></a>콘솔 애플리케이션 만들기

릴레이를 만들 때 [클라이언트 인증 필요] 옵션을 사용하지 않도록 설정한 경우 임의 브라우저로 요청을 하이브리드 연결 URL에 보낼 수 있습니다. 보호된 엔드포인트에 액세스하기 위해서는 여기 표시된 `ServiceBusAuthorization` 헤더에 토큰을 만들고 전달해야 합니다.

Visual Studio에서 새 **콘솔 앱(.NET Framework)** 프로젝트를 만듭니다.

### <a name="add-the-relay-nuget-package"></a>릴레이 NuGet 패키지 추가

1. 마우스 오른쪽 단추로 새로 만든 프로젝트를 클릭한 다음 **NuGet 패키지 관리**를 선택합니다.
2. **시험판 포함** 옵션을 선택합니다. 
3. **찾아보기**를 선택한 다음 **Microsoft.Azure.Relay**를 검색합니다. 검색 결과에서 **Microsoft Azure Relay**를 선택합니다.
4. 버전은 **2.0.0-preview1-20180523**을 선택합니다. 
5. **설치**을 선택하여 설치를 완료합니다. 대화 상자를 닫습니다.

### <a name="write-code-to-send-requests"></a>요청을 보내는 코드 작성

1. Program.cs 파일의 맨 위에 있는 기존 `using` 문을 다음 `using` 문으로 바꿉니다.
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Net.Http;
    using Microsoft.Azure.Relay;
    ```
2. 상수를 하이브리드 연결 정보의 `Program` 클래스에 추가합니다. 대괄호에 포함된 자리 표시자를 하이브리드 연결을 만들 때 가져온 값으로 바꿉니다. 정규화된 네임스페이스 이름을 사용해야 합니다.
   
    ```csharp
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
    private const string ConnectionName = "{HybridConnectionName}";
    private const string KeyName = "{SASKeyName}";
    private const string Key = "{SASKey}";
    ```
3. `Program` 클래스에 다음 메서드를 추가합니다.
   
    ```csharp
    private static async Task RunAsync()
    {
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
        var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
        var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
        var client = new HttpClient();
        var request = new HttpRequestMessage()
        {
            RequestUri = uri,
            Method = HttpMethod.Get,
        };
        request.Headers.Add("ServiceBusAuthorization", token);
        var response = await client.SendAsync(request);
        Console.WriteLine(await response.Content.ReadAsStringAsync());        Console.ReadLine();
    }
    ```
4. `Program` 클래스의 `Main` 메서드에 다음 코드 줄을 추가합니다.
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    Program.cs는 다음과 같이 표시됩니다.
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
   
    namespace Client
    {
        class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
               var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
                var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
                var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
                var client = new HttpClient();
                var request = new HttpRequestMessage()
                {
                    RequestUri = uri,
                    Method = HttpMethod.Get,
                };
                request.Headers.Add("ServiceBusAuthorization", token);
                var response = await client.SendAsync(request);
                Console.WriteLine(await response.Content.ReadAsStringAsync());
            }
        }
    }
    ```

