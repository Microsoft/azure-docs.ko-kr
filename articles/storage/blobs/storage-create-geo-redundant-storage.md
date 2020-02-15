---
title: 자습서 - Blob 스토리지에서 고가용성 애플리케이션 빌드
titleSuffix: Azure Storage
description: 읽기 액세스 지역 중복 스토리지를 사용하여 애플리케이션 데이터의 고가용성을 지원하세요.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: 0eabd918b5f8f52049792ceb28ef8055945d6475
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162177"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>자습서: Blob Storage에서 고가용성 애플리케이션 빌드

이 자습서는 시리즈의 1부입니다. 여기서는 Azure에서 애플리케이션 데이터의 고가용성을 지원하는 방법을 알아봅니다.

이 자습서를 완료하면 [RA-GRS(읽기 액세스 지역 중복)](../common/storage-redundancy.md) 스토리지 계정으로 Blob을 업로드하고 검색하는 콘솔 애플리케이션을 갖게 됩니다.

RA-GRS는 주 지역에서 보조 지역으로 트랜잭션을 복제하는 방식으로 작동합니다. 복제 프로세스는 보조 지역의 데이터가 결과적으로 일치하도록 보장합니다. 이 애플리케이션은 [회로 차단기](/azure/architecture/patterns/circuit-breaker) 패턴을 사용하여 연결할 엔드포인트를 결정하고 오류 및 복구가 시뮬레이션될 때 엔드포인트를 자동으로 전환합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

시리즈 1부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 스토리지 계정 만들기
> * 연결 문자열 설정
> * 콘솔 애플리케이션 실행

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

* **Azure 개발** 워크로드를 사용하여 [Visual Studio 2019](https://www.visualstudio.com/downloads/)를 설치합니다.

  ![Azure Development(웹 및 클라우드 아래)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="pythontabpython"></a>[Python](#tab/python)

* [Python 설치](https://www.python.org/downloads/)
* [Python용 Azure Storage SDK](https://github.com/Azure/azure-storage-python) 다운로드 및 설치

# <a name="nodejstabnodejs"></a>[Node.JS](#tab/nodejs)

* [Node.js](https://nodejs.org)를 설치합니다.

---

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

스토리지 계정은 Azure Storage 데이터 개체의 저장 및 액세스를 위한 고유한 네임스페이스를 제공합니다.

아래 단계에 따라 읽기 액세스 지역 중복 스토리지 계정을 만듭니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 단추를 선택합니다.
2. **새로 만들기** 페이지에서 **스토리지**를 선택합니다.
3. **추천**에서 **스토리지 계정 - Blob, 파일, 테이블, 큐**를 선택합니다.
4. 다음 정보로 스토리지 계정 양식을 작성하고(아래 이미지 참조) **만들기**를 선택합니다.

   | 설정       | 제안 값 | Description |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **이름** | mystorageaccount | 스토리지 계정의 고유한 값 |
   | **배포 모델** | 리소스 관리자  | 리소스 관리자에는 최신 기능이 포함되어 있습니다.|
   | **계정 종류** | StorageV2 | 계정 유형에 대한 세부 정보는 [스토리지 계정 유형](../common/storage-introduction.md#types-of-storage-accounts)을 참조하세요. |
   | **성능** | Standard | Standard는 샘플 시나리오에 충분합니다. |
   | **복제**| RA-GRS(읽기 액세스 지역 중복 스토리지) | 샘플 작동에 필요합니다. |
   |**구독** | 사용자의 구독 |구독에 대한 자세한 내용은 [구독](https://account.azure.com/Subscriptions)을 참조하세요. |
   |**ResourceGroup** | myResourceGroup |유효한 리소스 그룹 이름은 [명명 규칙 및 제한 사항](/azure/architecture/best-practices/resource-naming)을 참조하세요. |
   |**위치** | 미국 동부 | 위치를 선택합니다. |

![스토리지 계정 만들기](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>샘플 다운로드

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[샘플 프로젝트를 다운로드](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)하고, storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip 파일의 압축을 풉니다. 또한 [git](https://git-scm.com/)을 사용하여 개발 환경에 애플리케이션 복사본을 다운로드할 수 있습니다. 샘플 프로젝트는 콘솔 애플리케이션을 포함합니다.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="pythontabpython"></a>[Python](#tab/python)

[샘플 프로젝트를 다운로드](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)하고, storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip 파일의 압축을 풉니다. 또한 [git](https://git-scm.com/)을 사용하여 개발 환경에 애플리케이션 복사본을 다운로드할 수 있습니다. 샘플 프로젝트에는 기본 Python 애플리케이션이 포함되어 있습니다.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="nodejstabnodejs"></a>[Node.JS](#tab/nodejs)

[샘플 프로젝트를 다운로드](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs)하고 파일 압축을 풉니다. 또한 [git](https://git-scm.com/)을 사용하여 개발 환경에 애플리케이션 복사본을 다운로드할 수 있습니다. 샘플 프로젝트에는 기본 Node.js 애플리케이션이 포함되어 있습니다.

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>샘플 구성

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

애플리케이션에서 스토리지 계정에 대한 연결 문자열을 제공해야 합니다. 애플리케이션을 실행하는 로컬 컴퓨터의 환경 변수 내에 이 연결 문자열을 저장할 수 있습니다. 운영 체제에 따라 아래 예제 중 하나를 따라 환경 변수를 만듭니다.

Azure Portal에서 스토리지 계정으로 이동합니다. 스토리지 계정의 **설정** 아래에서 **액세스 키**를 선택합니다. 기본 또는 보조 키에서 **연결 문자열**을 복사합니다. 운영 체제에 따라 다음 명령 중 하나를 실행하여 \<yourconnectionstring\>을 실제 연결 문자열로 바꿉니다. 이 명령은 로컬 컴퓨터에 환경 변수를 저장합니다. Windows에서 사용 중인 **명령 프롬프트** 또는 셸을 다시 로드할 때까지 환경 변수를 사용할 수 없습니다.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="pythontabpython"></a>[Python](#tab/python)

애플리케이션에서 스토리지 계정 자격 증명을 반드시 제공해야 합니다. 애플리케이션을 실행하는 로컬 머신의 환경 변수에 이 정보를 저장할 수 있습니다. 운영 체제에 따라 아래 예제 중 하나를 참고하여 환경 변수를 만듭니다.

Azure Portal에서 스토리지 계정으로 이동합니다. 스토리지 계정의 **설정** 아래에서 **액세스 키**를 선택합니다. **스토리지 계정 이름**과 **키** 값을 다음 명령에 붙여넣어, \<youraccountname\>과 \<youraccountkey\> 자리 표시자를 바꿉니다. 이 명령은 로컬 머신에 환경 변수를 저장합니다. Windows에서 사용 중인 **명령 프롬프트** 또는 셸을 다시 로드할 때까지 환경 변수를 사용할 수 없습니다.

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="nodejstabnodejs"></a>[Node.JS](#tab/nodejs)

이 샘플을 실행하려면 `.env.example` 파일에 스토리지 계정 자격 증명을 추가한 다음, `.env`로 이름을 변경해야 합니다.

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

스토리지 계정으로 이동하여 **설정** 섹션에서 **액세스 키**를 선택하면 Azure Portal에서 이 정보를 찾을 수 있습니다.

필수 종속성을 설치합니다. 이렇게 하려면 명령 프롬프트를 열고 샘플 폴더로 이동한 다음, `npm install`을 입력합니다.

---

## <a name="run-the-console-application"></a>콘솔 애플리케이션 실행

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Visual Studio에서 **F5** 키를 누르거나 **시작**을 클릭하여 애플리케이션 디버깅을 시작합니다. Visual Studio는 구성된 경우 누락된 NuGet 패키지를 자동으로 복원합니다. 자세한 내용은 [패키지 복원으로 패키지 설치 및 다시 설치](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview)에서 확인하세요.

콘솔 창에서 시작하고 애플리케이션이 실행을 시작합니다. 애플리케이션은 **HelloWorld.png** 이미지를 솔루션에서 스토리지 계정으로 업로드합니다. 애플리케이션은 해당 이미지를 보조 RA-GRS 엔드포인트로 복제했는지 확인합니다. 그런 다음, 이미지를 최대 999회까지 다운로드를 시작합니다. 읽기는 각각 **P** 또는 **S**로 나타납니다. 여기서 **P**는 기본 엔드포인트을 나타내고 **S**는 보조 엔드포인트을 나타냅니다.

![콘솔 앱 실행](media/storage-create-geo-redundant-storage/figure3.png)

샘플 코드에서 `Program.cs` 파일의 `RunCircuitBreakerAsync` 작업은 [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) 메서드를 사용하여 스토리지 계정에서 이미지를 다운로드하는 데 사용합니다. 다운로드하기 전에 [OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext)가 정의됩니다. 작업 컨텍스트는 다운로드가 성공적으로 완료되거나, 다운로드가 실패하고 다시 시도하는 경우 생성되는 이벤트 처리기를 정의합니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

터미널 또는 명령 프롬프트에서 애플리케이션을 실행하려면 **circuitbreaker.py** 디렉터리로 이동한 다음, `python circuitbreaker.py`를 입력합니다. 애플리케이션은 **HelloWorld.png** 이미지를 솔루션에서 스토리지 계정으로 업로드합니다. 애플리케이션은 해당 이미지를 보조 RA-GRS 엔드포인트로 복제했는지 확인합니다. 그런 다음, 이미지를 최대 999회까지 다운로드를 시작합니다. 읽기는 각각 **P** 또는 **S**로 나타납니다. 여기서 **P**는 기본 엔드포인트을 나타내고 **S**는 보조 엔드포인트을 나타냅니다.

![콘솔 앱 실행](media/storage-create-geo-redundant-storage/figure3.png)

샘플 코드에서 `circuitbreaker.py` 파일의 `run_circuit_breaker` 메서드는 [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html) 메서드를 사용하여 스토리지 계정에서 이미지를 다운로드하는 데 사용됩니다.

Storage 개체 retry 함수는 선형 다시 시도 정책으로 설정됩니다. retry 함수는 요청을 다시 시도할지 여부를 결정하고, 요청을 다시 시도할 때까지 전에 대기할 시간(초)을 지정합니다. 1차 시도에 대한 초기 요청이 실패했을 때 2차 시도에 대해 다시 요청해야 하는 경우 **retry\_to\_secondary** 값을 true로 설정합니다. 샘플 애플리케이션에서 사용자 지정 다시 시도 정책은 스토리지 개체의 `retry_callback` 함수에 정의되어 있습니다.

다운로드하기 전에 Service 개체 [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) 및 [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) 함수가 정의됩니다. 이러한 함수는 다운로드가 성공적으로 완료되거나, 다운로드가 실패하고 다시 시도할 때 발생하는 이벤트 처리기를 정의합니다.

# <a name="nodejstabnodejs"></a>[Node.JS](#tab/nodejs)

샘플을 실행하려면 명령 프롬프트를 열고 샘플 폴더로 이동한 다음, `node index.js`을 입력합니다.

샘플은 Blob 스토리지 계정에 컨테이너를 생성하고 **HelloWorld.png**를 컨테이너에 업로드한 다음, 컨테이너와 이미지가 보조 지역에 복제되었는지 여부를 반복해서 확인합니다. 복제가 완료되면 **D** 또는 **Q**를 입력하여(ENTER 키 누르기 필요) 다운로드 또는 종료하라는 메시지가 표시됩니다. 출력은 다음 예제와 비슷하게 표시됩니다.

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>샘플 코드 이해

### <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

### <a name="retry-event-handler"></a>이벤트 처리기 다시 시도

이미지 다운로드가 실패하고 다시 시도하도록 설정된 경우 `OperationContextRetrying` 이벤트 처리기가 호출됩니다. 애플리케이션에 정의된 최대 다시 시도 횟수에 도달하면 요청의 [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode)가 `SecondaryOnly`로 변경됩니다. 이 설정을 사용하면 애플리케이션이 보조 엔드포인트에서 이미지 다운로드를 강제로 시도합니다. 이 구성은 기본 엔드포인트가 무한으로 다시 시도되지 않으므로 이미지를 요청하는 데 소요되는 시간이 줄여줍니다.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>완료된 이미지 처리기 요청

이미지 다운로드가 성공하면 `OperationContextRequestCompleted` 이벤트 처리기가 호출됩니다. 애플리케이션에서 보조 엔드포인트을 사용하고 있는 경우 애플리케이션은 최대 20회까지 이 엔드포인트을 계속 사용합니다. 20회 후에 이 애플리케이션은 [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode)를 `PrimaryThenSecondary`로 다시 설정하고 기본 엔드포인트을 다시 반복합니다. 요청이 성공하면 애플리케이션은 기본 엔드포인트에서 읽기를 계속합니다.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

### <a name="pythontabpython"></a>[Python](#tab/python)

### <a name="retry-event-handler"></a>이벤트 처리기 다시 시도

이미지 다운로드가 실패하고 다시 시도하도록 설정된 경우 `retry_callback` 이벤트 처리기가 호출됩니다. 애플리케이션에 정의된 최대 다시 시도 횟수에 도달하면 요청의 [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python)가 `SECONDARY`로 변경됩니다. 이 설정을 사용하면 애플리케이션이 보조 엔드포인트에서 이미지 다운로드를 강제로 시도합니다. 이 구성은 기본 엔드포인트가 무한으로 다시 시도되지 않으므로 이미지를 요청하는 데 소요되는 시간이 줄여줍니다.

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>완료된 이미지 처리기 요청

이미지 다운로드가 성공하면 `response_callback` 이벤트 처리기가 호출됩니다. 애플리케이션에서 보조 엔드포인트을 사용하고 있는 경우 애플리케이션은 최대 20회까지 이 엔드포인트을 계속 사용합니다. 20회 후에 이 애플리케이션은 [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python)를 `PRIMARY`로 다시 설정하고 기본 엔드포인트을 다시 반복합니다. 요청이 성공하면 애플리케이션은 기본 엔드포인트에서 읽기를 계속합니다.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

### <a name="nodejstabnodejs"></a>[Node.JS](#tab/nodejs)

Node.js V10 SDK를 사용하면 콜백 처리기가 필요하지 않습니다. 대신, 샘플이 다시 시도 옵션과 보조 엔드포인트로 구성된 파이프라인을 작성합니다. 그러면 애플리케이션이 기본 파이프라인을 통해 데이터에 도달하지 못하면 보조 파이프라인으로 자동 전환될 수 있습니다.

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>다음 단계

시리즈의 파트 1에서는 RA-GRS 스토리지 계정으로 애플리케이션의 고가용성을 지원하는 방법에 대해 알아봤습니다.

시리즈의 파트 2로 진행하여 오류를 시뮬레이션하고 보조 RA-GRS 엔드포인트를 사용하도록 애플리케이션을 강제하는 방법을 알아 보세요.

> [!div class="nextstepaction"]
> [주 지역에서 읽기 오류 시뮬레이션](storage-simulate-failure-ragrs-account-app.md)
