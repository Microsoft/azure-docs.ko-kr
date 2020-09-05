---
title: Visual Studio를 사용하여 Azure Functions 개발
description: Visual Studio 2019 용 Azure Functions 도구를 사용 하 여 Azure Functions를 개발 하 고 테스트 하는 방법을 알아봅니다.
ms.custom: vs-azure, devx-track-csharp
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 0ee5d270db2149be0cfbf6bf06f87a5d0133c6ef
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612812"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Visual Studio를 사용하여 Azure Functions 개발  

Visual Studio를 사용 하면 c # 클래스 라이브러리 함수를 개발, 테스트 및 Azure에 배포할 수 있습니다. 이 환경이 Azure Functions 처음 인 경우 [Azure Functions 소개](functions-overview.md)를 참조 하세요.

Visual Studio는 함수를 개발할 때 다음과 같은 이점을 제공 합니다. 

* 로컬 개발 컴퓨터에서 함수를 편집, 빌드 및 실행합니다. 
* Azure Functions 프로젝트를 Azure에 직접 게시 하 고 필요에 따라 Azure 리소스를 만듭니다. 
* C # 특성을 사용 하 여 c # 코드에서 함수 바인딩을 직접 선언 합니다.
* 미리 컴파일된 C# 함수를 개발하고 배포합니다. 미리 컴파일된 함수는 C# 스크립트 기반 함수보다 더 뛰어난 콜드 부팅 성능을 제공합니다. 
* Visual Studio 개발의 모든 이점을 누리면서 C#에서 함수를 코딩합니다. 

이 문서에서는 Visual Studio를 사용 하 여 c # 클래스 라이브러리 함수를 개발 하 고 Azure에 게시 하는 방법에 대해 자세히 설명 합니다. 이 문서를 읽기 전에 [Visual Studio의 함수 빠른](functions-create-your-first-function-visual-studio.md)시작을 완료 하는 것이 좋습니다. 

별도로 언급 하지 않는 한, 표시 되는 프로시저와 예제는 Visual Studio 2019에 대 한 것입니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Azure Functions 도구. Azure 함수 도구를 추가 하려면 Visual Studio 설치에 **azure 개발** 워크 로드를 포함 합니다. Azure Functions 도구는 Visual Studio 2017부터 Azure 개발 워크 로드에서 사용할 수 있습니다.

- Azure Storage 계정과 같이 필요한 다른 리소스는 게시 프로세스 중에 구독에서 생성 됩니다.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Visual Studio 2017에서 Azure 개발 워크 로드는 Azure Functions 도구를 별도의 확장으로 설치 합니다. Visual Studio 2017 설치를 업데이트할 때 Azure Functions 도구의 [최신 버전](#check-your-tools-version) 을 사용 하 고 있는지 확인 합니다. 다음 섹션에서는 Visual Studio 2017에서 Azure Functions 도구 확장을 확인 하 고 필요한 경우 업데이트 하는 방법을 보여 줍니다. 
>
> Visual Studio 2019을 사용 하는 경우이 섹션을 건너뜁니다.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Visual Studio 2017에서 도구 버전 확인

1. **도구** 메뉴에서 **확장 및 업데이트**를 선택합니다. **설치 된**  >  **도구**를 확장 한 다음 **Azure Functions 및 웹 작업 도구**를 선택 합니다.

    ![함수 도구 버전 확인](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. 설치 된 **버전** 을 확인 하 고이 버전과 [릴리스 정보](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md)에 나열 된 최신 버전을 비교 합니다. 

1. 최신 버전이 아닌 경우 다음 섹션에 표시된 대로 Visual Studio에서 도구를 업데이트합니다.

### <a name="update-your-tools-in-visual-studio-2017"></a>Visual Studio 2017에서 도구 업데이트

1. **확장명 및 업데이트** 대화 상자에서 **업데이트** > **Visual Studio Marketplace**를 확장하고, **Azure Functions 및 웹 작업 도구**를 선택하고 **업데이트**를 선택합니다.

    ![함수 도구 버전 업데이트](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. 도구 업데이트가 다운로드 되 면 **닫기**를 선택 하 고 Visual Studio를 닫고 VSIX 설치 관리자를 사용 하 여 도구 업데이트를 트리거합니다.

1. VSIX 설치 관리자에서 **수정** 을 선택 하 여 도구를 업데이트 합니다. 

1. 업데이트가 완료 되 면 **닫기**를 선택 하 고 Visual Studio를 다시 시작 합니다.

> [!NOTE]  
> Visual Studio 2019 이상에서 Azure Functions 도구 확장은 Visual Studio의 일부로 업데이트 됩니다.  

## <a name="create-an-azure-functions-project"></a>Azure Functions 프로젝트 만들기

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Azure Functions 프로젝트를 만든 후 프로젝트 템플릿은 c # 프로젝트를 만들고, `Microsoft.NET.Sdk.Functions` NuGet 패키지를 설치 하 고, 대상 프레임 워크를 설정 합니다. 새 프로젝트에는 다음 파일이 포함됩니다.

* **host.json**: 함수 호스트를 구성할 수 있습니다. 이러한 설정은 로컬 및 Azure에서 실행할 때 모두 적용됩니다. 자세한 내용은 [host.json 참조](functions-host-json.md)를 참조하세요.

* **local.settings.json**: 함수를 로컬로 실행할 때 사용되는 설정을 유지합니다. 이러한 설정은 Azure에서 실행 하는 경우에는 사용 되지 않습니다. 자세한 내용은 [로컬 설정 파일](#local-settings-file)을 참조 하세요.

    >[!IMPORTANT]
    >파일의 local.settings.js는 암호를 포함할 수 있으므로 프로젝트 소스 제어에서 제외 해야 합니다. 이 파일의 **출력 디렉터리로 복사** 설정이 **새 버전이 면 복사**로 설정 되어 있는지 확인 합니다. 

자세한 내용은 [Functions 클래스 라이브러리 프로젝트](functions-dotnet-class-library.md#functions-class-library-project)를 참조하세요.

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Visual Studio는 프로젝트를 게시할 때 local.settings.js의 설정을 자동으로 업로드 하지 않습니다. 이러한 설정이 Azure의 함수 앱에도 존재 하는지 확인 하려면 프로젝트를 게시 한 후에이를 업로드 합니다. 자세한 내용은 [함수 앱 설정](#function-app-settings)을 참조 하세요. 컬렉션의 값은 `ConnectionStrings` 게시 되지 않습니다.

코드에서 함수 앱 설정 값을 환경 변수로 읽을 수도 있습니다. 자세한 내용은 [환경 변수](functions-dotnet-class-library.md#environment-variables)를 참조하세요.

## <a name="configure-the-project-for-local-development"></a>로컬 개발에 대한 프로젝트 구성

함수 런타임에서 Azure Storage 계정을 내부적으로 사용합니다. HTTP 및 웹 후크가 아닌 모든 트리거 형식에 대해 `Values.AzureWebJobsStorage` 키를 유효한 Azure Storage 계정 연결 문자열로 설정 합니다. 함수 앱은 프로젝트에 필요한 연결 설정에 [Azure Storage 에뮬레이터](../storage/common/storage-use-emulator.md) 를 사용할 수도 있습니다 `AzureWebJobsStorage` . 에뮬레이터를 사용 하려면 값을 `AzureWebJobsStorage` 로 설정 `UseDevelopmentStorage=true` 합니다. 배포 하기 전에이 설정을 실제 저장소 계정 연결 문자열로 변경 합니다.

스토리지 계정 연결 문자열을 설정하려면 다음을 수행합니다.

1. Visual Studio에서 **뷰**  >  **클라우드 탐색기**를 선택 합니다.

2. **클라우드 탐색기**에서 **저장소 계정**을 확장 하 고 저장소 계정을 선택 합니다. **속성** 탭에서 **기본 연결 문자열** 값을 복사 합니다.

2. 프로젝트에서 파일에 local.settings.js을 열고 키 값을 `AzureWebJobsStorage` 복사한 연결 문자열로 설정 합니다.

3. 이전 단계를 반복 하 여 `Values` 함수에 필요한 다른 연결에 대 한 고유 키를 배열에 추가 합니다. 

## <a name="add-a-function-to-your-project"></a>프로젝트에 함수 추가

C # 클래스 라이브러리 함수에서 함수에 사용 되는 바인딩은 코드에서 특성을 적용 하 여 정의 됩니다. 제공 된 템플릿에서 함수 트리거를 만들 때 트리거 특성이 적용 됩니다. 

1. **솔루션 탐색기**에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭 하 고 **Add**  >  **새 항목**추가를 선택 합니다. 

2. **Azure Function**을 선택 하 고 클래스의 **이름을** 입력 한 다음 **추가**를 선택 합니다.

3. 트리거를 선택 하 고 바인딩 속성을 설정한 다음 **확인**을 선택 합니다. 다음 예에서는 Queue storage 트리거 함수를 만드는 설정을 보여 줍니다. 

    ![Queue storage 트리거 함수 만들기](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    이 트리거 예제에서는 라는 키가 있는 연결 문자열을 사용 `QueueStorage` 합니다. [파일의local.settings.js](functions-run-local.md#local-settings-file)에서이 연결 문자열 설정을 정의 합니다.

4. 새로 추가된 클래스를 검사합니다. `Run()`특성을 사용 하 여 특성을 사용 하는 정적 메서드가 표시 `FunctionName` 됩니다. 이 특성은 메서드가 함수에 대한 진입점임을 나타냅니다.

    예를 들어 다음 c # 클래스는 기본 Queue storage 트리거 함수를 나타냅니다.

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", 
                Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```

바인딩 고유 특성은 진입점 메서드에 적용되는 각 바인딩 매개 변수에 적용됩니다. 특성은 매개 변수로 바인딩 정보를 사용합니다. 이전 예제에서 첫 번째 매개 변수는 `QueueTrigger` 큐 저장소 트리거 함수를 나타내는 특성이 적용 됩니다. 큐 이름 및 연결 문자열 설정 이름은 특성에 매개 변수로 전달 됩니다 `QueueTrigger` . 자세한 내용은 [Azure Functions의 Azure Queue Storage 바인딩](functions-bindings-storage-queue-trigger.md)을 참조하세요.

위의 절차를 사용 하 여 함수 앱 프로젝트에 함수를 추가 합니다. 프로젝트의 각 함수는 다른 트리거를 가질 수 있지만 함수에는 정확히 하나의 트리거가 있어야 합니다. 자세한 내용은 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md)을 참조하세요.

## <a name="add-bindings"></a>바인딩 추가

트리거와 마찬가지로 입력 및 출력 바인딩이 바인딩 특성으로 함수에 추가됩니다. 다음과 같이 함수에 바인딩을 추가합니다.

1. [로컬 개발에 대 한 프로젝트를 구성](#configure-the-project-for-local-development)했는지 확인 합니다.

2. 특정 바인딩에 대한 적절한 NuGet 확장 패키지를 추가합니다. 

   자세한 내용은 [Visual Studio를 사용 하는 c # 클래스 라이브러리](./functions-bindings-register.md#local-csharp)를 참조 하세요. 바인딩에 대 한 참조 문서에서 바인딩 별 NuGet 패키지 요구 사항을 찾습니다. 예를 들어 [Event Hubs 바인딩 참조 문서](functions-bindings-event-hubs.md)에서 Event Hubs 트리거에 대한 패키지 요구 사항을 찾습니다.

3. 바인딩에 필요한 앱 설정이 있으면 `Values` [로컬 설정 파일](functions-run-local.md#local-settings-file)의 컬렉션에 추가 합니다. 

   함수는 로컬로 실행 될 때 이러한 값을 사용 합니다. 함수가 Azure의 함수 앱에서 실행 되는 경우 함수 [앱 설정을](#function-app-settings)사용 합니다.

4. 메서드 서명에 적절한 바인딩 특성을 추가합니다. 다음 예제에서 큐 메시지는 함수를 트리거하고, 출력 바인딩은 다른 큐와 동일한 텍스트가 있는 새 큐 메시지를 만듭니다.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
   Queue Storage에 대한 연결은 `AzureWebJobsStorage` 설정에서 가져옵니다. 자세한 내용은 특정 바인딩에 대한 참조 문서를 참조하세요. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>테스팅 함수

Azure Functions Core 도구를 사용하면 로컬 개발 컴퓨터에서 Azure Functions 프로젝트를 실행할 수 있습니다. 자세한 내용은 [Azure Functions Core Tools 사용](functions-run-local.md)을 참조 하세요. Visual Studio에서 함수를 처음 시작할 때 이러한 도구를 설치 하 라는 메시지가 표시 됩니다. 

Visual Studio에서 함수를 테스트 하려면 다음을 수행 합니다.

1. F5 키를 누릅니다. 메시지가 표시되면 Visual Studio에서 Azure Functions Core(CLI) 도구를 다운로드하여 설치하도록 요구하는 요청을 수락합니다. 또한 도구에서 HTTP 요청을 처리할 수 있도록 방화벽 예외를 사용하도록 설정해야 할 수도 있습니다.

2. 프로젝트를 실행 하는 동안 배포 된 함수를 테스트 하는 것 처럼 코드를 테스트 합니다. 

   자세한 내용은 [Azure Functions에서 코드를 테스트하기 위한 전략](functions-test-a-function.md)을 참조하세요. 디버그 모드에서 Visual Studio를 실행 하는 경우 중단점은 예상 대로 적중 됩니다.

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->


## <a name="publish-to-azure"></a>Azure에 게시

Visual Studio에서 게시 하는 경우 다음 두 가지 배포 방법 중 하나를 사용 합니다.

* [웹 배포](functions-deployment-technologies.md#web-deploy-msdeploy): Windows 앱을 패키지 하 고 모든 IIS 서버에 배포 합니다.
* [패키지 실행을 사용 하는 Zip 배포](functions-deployment-technologies.md#zip-deploy): Azure Functions 배포에 권장 됩니다.

다음 단계를 사용 하 여 프로젝트를 Azure의 함수 앱에 게시 합니다.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>함수 앱 설정

Visual Studio는 프로젝트를 게시할 때 이러한 설정을 자동으로 업로드 하지 않으므로 local.settings.js에 추가 하는 모든 설정은 Azure의 함수 앱에도 추가 해야 합니다.

Azure에서 함수 앱에 필요한 설정을 업로드 하는 가장 쉬운 방법은 프로젝트를 성공적으로 게시 한 후에 표시 되는 **Azure App Service 설정 관리** 링크를 선택 하는 것입니다.

:::image type="content" source="./media/functions-develop-vs/functions-vstools-app-settings.png" alt-text="게시 창의 설정":::

이 링크를 선택 하면 새 응용 프로그램 설정을 추가 하거나 기존 응용 프로그램 설정을 수정할 수 있는 함수 앱에 대 한 **응용 프로그램 설정** 대화 상자가 표시 됩니다.

![애플리케이션 설정](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Local** 은 파일에 대 한 local.settings.js설정 값을 표시 하 고, **원격** 은 Azure의 함수 앱에 현재 설정 값을 표시 합니다. **설정 추가**를 선택하여 새로운 앱 설정을 만듭니다. **로컬 값 삽입** 링크를 사용하여 **원격** 필드에 설정 값을 복사합니다. **확인**을 선택하면 보류 중인 변경 내용이 로컬 설정 파일 및 함수 앱에 기록됩니다.

> [!NOTE]
> 기본적으로 파일에 local.settings.js는 소스 제어에 체크 인 되지 않습니다. 즉, 소스 제어에서 로컬 함수 프로젝트를 복제 하는 경우 프로젝트에는 파일에 대 한 local.settings.js없습니다. 이 경우 **응용 프로그램 설정** 대화 상자가 정상적으로 작동 하도록 프로젝트 루트에서 파일에 local.settings.js를 수동으로 만들어야 합니다. 

또한 다음 다른 방법 중 하나로 애플리케이션 설정을 관리할 수도 있습니다.

* [Azure 포털을 사용합니다](functions-how-to-use-azure-function-app-settings.md#settings).
* [ `--publish-local-settings` Azure Functions Core Tools에서 게시 옵션을 사용](functions-run-local.md#publish)합니다.
* [Azure CLI를 사용](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)합니다.

## <a name="monitoring-functions"></a>함수 모니터링

함수 실행을 모니터링할 때는 Azure Application Insights와 함수 앱을 통합하는 방식을 사용하는 것이 좋습니다. Azure Portal에서 함수 앱을 만들 때는 이 통합이 기본적으로 자동 수행됩니다. 그러나 Visual Studio 게시 중에 함수 앱을 만들 때는 Azure에서 함수 앱 통합이 수행되지 않습니다.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

자세히 알아보려면 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Functions Core Tools에 대 한 자세한 내용은 [Azure Functions Core Tools 사용](functions-run-local.md)을 참조 하세요.

.NET 클래스 라이브러리로 함수를 개발 하는 방법에 대 한 자세한 내용은 [Azure Functions c # 개발자 참조](functions-dotnet-class-library.md)를 참조 하세요. 이 문서에서는 특성을 사용하여 Azure Functions에서 지원하는 다양한 유형의 바인딩을 선언하는 방법의 예제로 연결합니다.    
