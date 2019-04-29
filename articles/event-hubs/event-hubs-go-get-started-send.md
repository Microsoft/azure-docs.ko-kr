---
title: 송신 및 수신 이동-Azure Event Hubs를 사용 하 여 이벤트 | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs에서 이벤트를 보내는 Go 애플리케이션을 만드는 연습을 제공합니다.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 823ebc985c77785f8b48d12d5919dbbd1b2b1459
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821682"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-go"></a>이벤트를 보내거나 Go를 사용 하 여 Event Hubs에서 이벤트 수신
Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 장치에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. Event Hub로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/저장소 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

이 자습서에서는 이벤트를 보내거나 이벤트 허브에서 이벤트 수신에 Go 응용 프로그램을 작성 하는 방법을 설명 합니다. 

> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs)에서 샘플로 이 빠른 시작을 다운로드하여 `EventHubConnectionString` 및 `EventHubName` 문자열을 이벤트 허브 값으로 대체하고, 실행합니다. 또는 이 자습서의 단계를 수행하여 직접 만들 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

- 로컬로 설치된 Go. 필요한 경우 [다음 지침](https://golang.org/doc/install)을 따릅니다.
- 활성 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정][]을 만듭니다.
- **Event Hubs 네임 스페이스를 만들고 event hub**합니다. 사용 된 [Azure portal](https://portal.azure.com) Event Hubs 형식의 네임 스페이스를 만들고 event hub와 통신 해야 하는 응용 프로그램 관리 자격 증명을 얻습니다. 네임스페이스 및 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 따릅니다.

## <a name="send-events"></a>이벤트 보내기
이 섹션에서는 이벤트 허브로 이벤트를 보내는 Go 응용 프로그램을 만드는 방법을 보여 줍니다. 

### <a name="install-go-package"></a>Go 패키지 설치

`go get` 또는 `dep` 명령을 사용하여 Event Hubs용 Go 패키지를 받습니다. 예를 들면 다음과 같습니다.

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>코드 파일에서 패키지 가져오기

Go 패키지를 가져오려면 다음 코드 예제를 사용합니다.

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>서비스 주체 만들기

[Azure CLI 2.0을 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)의 지침에 따라 새 서비스 주체를 만듭니다. 제공된 자격 증명을 다른 이름으로 현재 환경에 저장합니다. Go용 Azure SDK 및 Event Hubs 패키지는 다음 변수 이름을 찾도록 미리 구성됩니다.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

이제 이러한 자격 증명을 사용하는 Event Hubs 클라이언트에 대한 권한 부여 공급자를 만듭니다.

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Event Hubs 클라이언트 만들기

다음 코드는 새 Event Hubs 클라이언트를 만듭니다.

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>메시지를 보내는 코드 작성

다음 코드 조각에서 (1) 터미널에서 대화식으로 메시지를 보내거나 (2) 프로그램 내에서 메시지를 보냅니다.

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>추가 항목

이벤트 허브의 파티션 ID 가져오기:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

응용 프로그램을 실행하여 이벤트 허브에 이벤트를 보냅니다. 

축하합니다! 이제 Event Hub에 메시지를 보냈습니다.

## <a name="receive-events"></a>이벤트 수신

### <a name="create-a-storage-account-and-container"></a>Storage 계정 및 컨테이너 만들기

이벤트 스트림의 파티션 및 검사점 임대와 같은 상태는 Azure Storage 컨테이너를 사용하여 수신기 간에 공유됩니다. Go SDK를 사용하여 저장소 계정 및 컨테이너를 만들 수 있지만, [Azure 저장소 계정 정보](../storage/common/storage-create-storage-account.md)의 지침에 따라 새로 만들 수도 있습니다.

Go SDK를 사용하여 Storage 아티팩트를 만드는 샘플은 [Go 샘플 리포지토리](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) 및 이 자습서에 해당하는 샘플에서 사용할 수 있습니다.

### <a name="go-packages"></a>패키지를 이동 합니다.

메시지를 받으려면 `go get` 또는 `dep` 명령을 사용하여 Event Hubs용 Go 패키지를 받으세요.

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>코드 파일에서 패키지 가져오기

Go 패키지를 가져오려면 다음 코드 예제를 사용합니다.

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

### <a name="create-service-principal"></a>서비스 주체 만들기

[Azure CLI 2.0을 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)의 지침에 따라 새 서비스 주체를 만듭니다. 제공된 자격 증명을 다음 이름으로 현재 환경에 저장합니다. Go용 Azure SDK 및 Event Hubs 패키지는 다음 변수 이름을 찾도록 미리 구성됩니다.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

다음으로, 이제 이러한 자격 증명을 사용하는 Event Hubs 클라이언트에 대한 권한 부여 공급자를 만듭니다.

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>메타데이터 구조체 가져오기

Azure Go SDK를 사용하여 Azure 환경에 대한 메타데이터로 구조체를 가져옵니다. 이후 작업에서 이 구조체를 사용하여 올바른 엔드포인트를 찾을 것입니다.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>자격 증명 도우미 만들기 

이전 AAD(Azure Active Directory) 자격 증명을 사용하여 Storage에 대한 SAS(공유 액세스 서명) 자격 증명을 만드는 자격 증명 도우미를 만듭니다. 마지막 매개 변수는 이전에 사용한 것과 동일한 환경 변수를 이 구문에 사용하라고 지시합니다.

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

### <a name="create-a-check-pointer-and-a-leaser"></a>확인 포인터와는 leaser 만들기 

만들기는 **leaser**특정 수신기에 파티션을 임대 하는 일을 담당 하는, 및 **포인터 확인**다른 수신기를 시작할 수 있도록 메시지 스트림에 대 한 검사점을 작성 하는 일을 담당 하는, 올바른 오프셋에서 읽는 중입니다.

현재는 동일한 Storage 컨테이너를 사용하여 임대와 검사점을 관리하는 단일 **StorageLeaserCheckpointer**가 제공됩니다. **StorageLeaserCheckpointer**는 컨테이너에 올바르게 액세스하려면 스토리지 계정 및 컨테이너 이름 외에도 이전 단계에서 만든 자격 증명과 Azure 환경 구조체가 필요합니다.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

### <a name="construct-event-processor-host"></a>구문 이벤트 프로세서 호스트

이제 다음과 같이 EventProcessorHost를 작성하는 데 필요한 방법이 제공됩니다. 동일 **StorageLeaserCheckpointer** 앞에서 설명한 대로 leaser 및 검사에 대 한 포인터를 모두 사용 됩니다.

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

### <a name="create-handler"></a>처리기 만들기 

이제 처리기를 만들어 이벤트 프로세서 호스트에 등록합니다. 호스트가 시작되면 이 처리기와 지정된 기타 처리기가 들어오는 메시지에 적용됩니다.

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

### <a name="write-code-to-receive-messages"></a>메시지를 받는 코드 작성

모든 설정이 끝나면 `Start(context)` 명령으로 이벤트 프로세서 호스트를 시작하여 영구적으로 실행할 수도 있고, `StartNonBlocking(context)` 명령으로 메시지를 사용할 수 있는 만큼만 실행할 수도 있습니다.

이 자습서는 다음과 같이 시작 및 실행됩니다. `StartNonBlocking` 사용 예제는 GitHub 샘플을 참조하세요.

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>다음 단계
다음 문서를 읽어보세요.

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [기능 및 Azure Event Hubs의 용어](event-hubs-features.md)
- [Event Hubs FAQ](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[체험 계정]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
