---
title: Azure Relay .NET Standard API 개요 | Microsoft Docs
description: Azure Relay .NET Standard API 개요
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 78ad3ab49db162af060b4273deea717cd3472668
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749022"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Azure Relay 하이브리드 연결 .NET Standard API 개요

이 문서는 일부 핵심적인 Azure Relay 하이브리드 연결 .NET Standard [클라이언트 API](/dotnet/api/microsoft.azure.relay)를 요약합니다.
  
## <a name="relay-connection-string-builder-class"></a>Relay 연결 문자열 작성기 클래스

[RelayConnectionStringBuilder][RelayConnectionStringBuilder] 클래스는 Relay 하이브리드 연결에 관련된 연결 문자열의 서식을 지정합니다. 연결 문자열의 서식을 확인하거나 연결 문자열을 처음부터 작성하는 데 사용할 수 있습니다. 예제를 보려면 다음 코드를 참조하세요.

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

연결 문자열을 `RelayConnectionStringBuilder` 메서드에 직접 전달할 수도 있습니다. 이 작업을 사용하면 연결 문자열이 유효한 형식인지 확인할 수 있습니다. 매개 변수가 잘못된 경우 생성자가 `ArgumentException`을 생성합니다.

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>하이브리드 연결 스트림

[HybridConnectionStream][HCStream] 클래스는 [HybridConnectionClient][HCClient] 또는 [HybridConnectionListener][HCListener]로 작업하는지에 상관 없이 Azure Relay 엔드포인트에서 데이터를 송수신하는 데 사용되는 기본 개체입니다.

### <a name="getting-a-hybrid-connection-stream"></a>하이브리드 연결 스트림 가져오기

#### <a name="listener"></a>수신기

[HybridConnectionListener][HCListener] 개체를 사용하면 다음과 같이 `HybridConnectionStream` 개체를 가져올 수 있습니다.

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>클라이언트

[HybridConnectionClient][HCClient] 개체를 사용하면 다음과 같이 `HybridConnectionStream` 개체를 가져올 수 있습니다.

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>데이터 수신

[HybridConnectionStream][HCStream] 클래스를 사용하면 양방향 통신이 가능합니다. 대부분의 경우 스트림에서 지속적으로 수신합니다. 스트림에서 텍스트를 읽는 경우 데이터 구문 분석이 더 쉬운 [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) 개체를 사용하는 것이 좋을 수도 있습니다. 예를 들어 아닌 데이터를 `byte[]`가 아닌 텍스트로 읽을 수 있습니다.

다음 코드는 취소가 요청될 때까지 스트림에서 개별 텍스트 줄을 읽습니다.

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>데이터 전송

연결을 설정하면 Relay 엔드포인트로 메시지를 보낼 수 있습니다. 연결 개체는 [스트림](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx)을 상속하기 때문에 데이터를 `byte[]`로 전송합니다. 다음 예제에 이 작업을 수행하는 방법이 나와 있습니다.

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

단, 매번 문자열을 인코딩할 필요 없이 텍스트를 직접 전송하려는 경우 `hybridConnectionStream` 개체를 [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) 개체로 래핑할 수 있습니다.

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>다음 단계

Azure Relay에 대한 자세한 내용은 다음 링크를 방문하세요.

* [Microsoft.Azure.Relay 참조](/dotnet/api/microsoft.azure.relay)
* [Azure 릴레이란?](relay-what-is-it.md)
* [사용 가능한 Relay API](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener