---
title: 포함 파일
description: 포함 파일
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 5c7c2fe101315959d07ce4912905bbf59a7ee664
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749519"
---
### <a name="create-a-console-application"></a>콘솔 애플리케이션 만들기

Visual Studio에서 새 **콘솔 앱(.NET Framework)** 프로젝트를 만듭니다.

### <a name="add-the-relay-nuget-package"></a>릴레이 NuGet 패키지 추가

1. 마우스 오른쪽 단추로 새로 만든 프로젝트를 클릭한 다음 **NuGet 패키지 관리**를 선택합니다.
2. **시험판 포함** 옵션을 선택합니다. 
3. **찾아보기**를 선택한 다음 **Microsoft.Azure.Relay**를 검색합니다. 검색 결과에서 **Microsoft Azure Relay**를 선택합니다.
4. 버전은 **2.0.0-preview1-20180523**을 선택합니다. 
5. **설치**을 선택하여 설치를 완료합니다. 대화 상자를 닫습니다.

### <a name="write-code-to-receive-messages"></a>메시지를 받는 코드 작성

1. Program.cs 파일의 맨 위에 있는 기존 `using` 문을 다음 `using` 문으로 바꿉니다.
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
    using System.Net;
    ```
2. 상수를 하이브리드 연결 정보의 `Program` 클래스에 추가합니다. 대괄호에 포함된 자리 표시자를 하이브리드 연결을 만들 때 가져온 값으로 바꿉니다. 정규화된 네임스페이스 이름을 사용해야 합니다.
   
    ```csharp
    // replace {RelayNamespace} with the name of your namespace
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";

    // replace {HybridConnectionName} with the name of your hybrid connection
    private const string ConnectionName = "{HybridConnectionName}";

    // replace {SAKKeyName} with the name of your Shared Access Policies key, which is RootManageSharedAccessKey by default
    private const string KeyName = "{SASKeyName}";

    // replace {SASKey} with the primary key of the namespace you saved earlier
    private const string Key = "{SASKey}";
    ```

3. `Program` 클래스에 `RunAsync` 메서드를 추가합니다.
   
    ```csharp
    private static async Task RunAsync()
    {
        var cts = new CancellationTokenSource();
   
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
        var listener = new HybridConnectionListener(new Uri(string.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
        // Subscribe to the status events.
        listener.Connecting += (o, e) => { Console.WriteLine("Connecting"); };
        listener.Offline += (o, e) => { Console.WriteLine("Offline"); };
        listener.Online += (o, e) => { Console.WriteLine("Online"); };

        // Provide an HTTP request handler
        listener.RequestHandler = (context) =>
        {
            // Do something with context.Request.Url, HttpMethod, Headers, InputStream...
            context.Response.StatusCode = HttpStatusCode.OK;
            context.Response.StatusDescription = "OK, This is pretty neat";
            using (var sw = new StreamWriter(context.Response.OutputStream))
            {
                sw.WriteLine("hello!");
            }
            
            // The context MUST be closed here
            context.Response.Close();
        };
            
        // Opening the listener establishes the control channel to
        // the Azure Relay service. The control channel is continuously 
        // maintained, and is reestablished when connectivity is disrupted.
        await listener.OpenAsync();
        Console.WriteLine("Server listening");
    
        // Start a new thread that will continuously read the console.
        await Console.In.ReadLineAsync();
        
        // Close the listener after you exit the processing loop.
        await listener.CloseAsync();
    }
    ```
5. `Program` 클래스의 `Main` 메서드에 다음 코드 줄을 추가합니다.
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    완성된 Program.cs 파일은 다음과 같이 표시됩니다.
   
    ```csharp
    namespace Server
    {
        using System;
        using System.IO;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Relay;
   
        public class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            public static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
                var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
                var listener = new HybridConnectionListener(new Uri(string.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
           
                // Subscribe to the status events.
                listener.Connecting += (o, e) => { Console.WriteLine("Connecting"); };
                listener.Offline += (o, e) => { Console.WriteLine("Offline"); };
                listener.Online += (o, e) => { Console.WriteLine("Online"); };
        
                // Provide an HTTP request handler
                listener.RequestHandler = (context) =>
                {
                    // Do something with context.Request.Url, HttpMethod, Headers, InputStream...
                    context.Response.StatusCode = HttpStatusCode.OK;
                    context.Response.StatusDescription = "OK";
                    using (var sw = new StreamWriter(context.Response.OutputStream))
                    {
                        sw.WriteLine("hello!");
                    }
                    
                    // The context MUST be closed here
                    context.Response.Close();
                };
           
                // Opening the listener establishes the control channel to
                // the Azure Relay service. The control channel is continuously 
                // maintained, and is reestablished when connectivity is disrupted.
                await listener.OpenAsync();
                Console.WriteLine("Server listening");
           
                // Start a new thread that will continuously read the console.
                await Console.In.ReadLineAsync();
               
                // Close the listener after you exit the processing loop.
                await listener.CloseAsync();
            }
        }
    }
    ```

