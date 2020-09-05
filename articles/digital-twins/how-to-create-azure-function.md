---
title: 데이터 처리를 위한 Azure 함수 설정
titleSuffix: Azure Digital Twins
description: 디지털 쌍으로 액세스 하 여 트리거할 수 있는 Azure 함수를 만드는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 66f514f4c5d299ef11efda541f16f4ef2fe61aed
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930165"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>데이터 처리를 위해 Azure Functions 앱 연결

미리 보기 중에 데이터를 기반으로 하는 디지털 쌍 업데이트는 [Azure Functions](../azure-functions/functions-overview.md)와 같은 계산 리소스를 통해 [**이벤트 경로**](concepts-route-events.md) 를 사용 하 여 처리 됩니다. Azure 함수는 다음에 대 한 응답으로 디지털 쌍을 업데이트 하는 데 사용할 수 있습니다.
* IoT Hub에서 들어오는 장치 원격 분석 데이터
* 쌍 그래프 내에서 다른 디지털 쌍의 속성 변경 또는 기타 데이터

이 문서에서는 Azure Digital Twins에서 사용할 Azure 함수를 만드는 과정을 안내 합니다. 

다음은 포함 된 단계에 대 한 개요입니다.

1. Visual Studio에서 Azure Functions 앱 만들기
2. [Event Grid](../event-grid/overview.md) 트리거를 사용 하 여 Azure 함수 작성
3. 함수에 인증 코드 추가 (Azure Digital Twins에 액세스할 수 있음)
4. Azure에 함수 앱 게시
5. [보안](concepts-security.md) 액세스를 설정 합니다. Azure 함수에서 런타임에 서비스에 액세스 하기 위해 액세스 토큰을 받도록 하려면 함수 앱에 대 한 관리 서비스 ID를 구성 해야 합니다.

이 문서의 나머지 부분에서는 Azure function 설정 단계를 한 번에 하나씩 안내 합니다.

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Visual Studio에서 Azure Functions 앱 만들기

Visual Studio 2019에서 *파일 > 새 프로젝트*를 선택 합니다. *Azure Functions* 템플릿을 검색 하 고, 선택 하 고, "다음"을 누릅니다.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-new-project.png" alt-text="Visual Studio: 새 프로젝트 대화 상자":::

함수 앱의 이름을 지정 하 고 "만들기"를 누릅니다.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-config.png" alt-text="Visual Studio: 프로젝트 구성 대화 상자":::

*Event Grid 트리거* 를 선택 하 고 "만들기"를 누릅니다.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-trigger.png" alt-text="Visual Studio: Azure 함수 프로젝트 트리거 대화 상자":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Event Grid 트리거를 사용 하 여 Azure 함수 작성

다음 코드는 이벤트를 기록 하는 데 사용할 수 있는 간단한 Azure 함수를 만듭니다. 

```csharp
// Default URL for triggering Event Grid function in the local environment
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;

namespace FunctionSample
{
    public static class FooFunction    {
        [FunctionName("Foo")]
        public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

기본 Azure 함수입니다.

### <a name="run-and-debug-the-azure-function-app"></a>Azure 함수 앱 실행 및 디버그

이제 함수를 컴파일하고 실행할 수 있습니다. Azure 함수는 궁극적으로 클라우드에서 실행 하기 위한 것 이지만, Azure 함수를 로컬로 실행 하 고 디버그할 수도 있습니다.

이에 대 한 자세한 내용은 [*로컬에서 디버그 Event Grid 트리거*](../azure-functions/functions-debug-event-grid-trigger-local.md)를 참조 하세요.

### <a name="add-the-azure-digital-twins-sdk-to-your-azure-function-app"></a>Azure 함수 앱에 Azure Digital Twins SDK 추가

함수 앱은 [.net 용 Azure IoT 디지털 쌍 클라이언트 라이브러리 (c #)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)를 사용 하 여 Azure Digital twins와 상호 작용 합니다. SDK를 사용 하려면 프로젝트에 다음 패키지를 포함 해야 합니다.
* `Azure.DigitalTwins.Core` (버전 `1.0.0-preview.2` )
* `Azure.Identity`

Azure Functions에 대해 올바르게 설정 하도록 Azure SDK 파이프라인을 구성 하려면 다음이 필요 합니다.
* `Azure.Net.Http`
* `Azure.Core`

선택한 도구에 따라 Visual Studio 패키지 관리자 또는 명령줄 도구를 사용 하 여이 작업을 수행할 수 있습니다 `dotnet` . 

Azure 함수에 다음 using 문을 추가 합니다.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```

## <a name="add-authentication-code-to-the-azure-function"></a>Azure function에 인증 코드 추가

다음으로, 함수가 Azure Digital Twins에 액세스할 수 있게 하는 인증 코드를 추가 합니다.

다음 값에 대 한 함수 클래스에 변수를 추가 합니다. 
* Azure Digital Twins 앱 ID
* Azure Digital Twins 인스턴스의 URL입니다. 함수에 하드 코딩 하는 대신 환경 변수에서 서비스 URL을 읽는 것이 좋습니다.
* HttpClient 인스턴스를 보유 하는 정적 변수입니다. HttpClient는 생성 하는 데 비교적 비용이 많이 들고 모든 함수 호출에 대해이 작업을 수행 하지 않으려고 합니다.

또한 함수 내에서 지역 변수를 추가 하 여 함수 프로젝트에 Azure Digital Twins 클라이언트 인스턴스를 포함 합니다. 클래스 내에서 정적 변수로 설정 *하지* 마십시오.

마지막으로 함수 시그니처를 비동기로 변경 합니다.

이러한 변경 후 함수 코드는 다음과 비슷해야 합니다.

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

함수 앱이 Azure Digital Twins에 액세스할 수 있으려면 시스템에서 관리 하는 id가 있어야 하 고 Azure Digital Twins 인스턴스에 액세스할 수 있는 권한이 있어야 합니다.

다음 명령을 사용하여 시스템 관리 ID를 만듭니다. 출력에서 *principalId* 필드를 기록해 둡니다.

```azurecli
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

다음 명령에서 *Principalid* 값을 사용 하 여 Azure Digital twins 인스턴스의 *Azure 디지털 쌍 소유자 (미리 보기)* 역할에 함수 앱의 id를 할당 합니다. 이렇게 하면 인스턴스에서 데이터 평면 작업을 수행할 수 있는 함수 앱 권한이 제공 됩니다.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

관리 id에 대 한 자세한 내용은 [*App Service 및 Azure Functions에 대해 관리 id를 사용 하는 방법*](../app-service/overview-managed-identity.md)을 참조 하세요.

마지막으로 환경 변수를 설정 하 여 함수에서 Azure Digital Twins 인스턴스의 URL에 액세스할 수 있도록 설정할 수 있습니다. 이에 대 한 자세한 내용은 [*환경 변수*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables)를 참조 하세요.

> [!TIP]
> Azure Digital Twins 인스턴스의 URL은 Azure Digital Twins 인스턴스의 *호스트 이름*앞에 *https://* 를 추가 하 여 수행 됩니다. 인스턴스의 모든 속성과 함께 호스트 이름을 보려면를 실행할 수 있습니다 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-URL>"
```

## <a name="publish-the-azure-function-app"></a>Azure 함수 앱 게시

함수 앱을 Azure에 게시 하려면 솔루션 탐색기의 함수 프로젝트 (솔루션 아님)를 마우스 오른쪽 단추로 선택 하 고 *게시 ()* 를 선택 합니다.

다음 탭이 표시 됩니다.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-1.png" alt-text="Visual Studio: 게시 함수 대화 상자, 1 페이지":::

Azure Functions에서 사용할 App Service 계획을 선택 하거나 만듭니다. 확실 하지 않은 경우 기본 소비 계획을 사용 하 여 시작 합니다.

> [!IMPORTANT] 
> Azure function을 게시 하면 Azure Digital Twins와 무관 하 게 구독에 대 한 추가 요금이 발생 합니다.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-2.png" alt-text="Visual Studio: 게시 함수 대화 상자, 2 페이지":::

다음 페이지에서 새 함수 앱, 리소스 그룹 및 기타 세부 정보에 대해 원하는 이름을 입력 합니다.

## <a name="set-up-security-access-for-the-azure-function-app"></a>Azure 함수 앱에 대 한 보안 액세스 설정

이전 예제의 Azure function 기초에서는 Azure 디지털 쌍으로 인증 하기 위해 전달자 토큰을 전달 해야 합니다. 이 전달자 토큰이 전달 되었는지 확인 하려면 함수 앱에 대해 [MSI (관리 서비스 ID)](../active-directory/managed-identities-azure-resources/overview.md) 를 설정 해야 합니다. 이 작업은 각 함수 앱에 대해 한 번만 수행 해야 합니다.

이를 설정 하려면 [Azure Portal](https://portal.azure.com/) 으로 이동 하 여 함수 앱으로 이동 합니다.

*플랫폼 기능* 탭에서 *id*:를 선택 합니다.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-1.png" alt-text="Azure Portal: Azure function에 대 한 Id 선택":::

Id 페이지에서 *상태* 전환을 *켜기*로 설정 합니다. 

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-2.png" alt-text="Azure Portal: id 상태 켜기":::

또한 다음 섹션에서 사용 되므로이 페이지에 표시 된 **개체 ID** 를 확인 합니다.

### <a name="assign-access-roles"></a>액세스 역할 할당

Azure Digital Twins는 역할 기반 액세스 제어를 사용 하 여 액세스를 관리 하기 때문에 (이에 대 한 자세한 내용은 [*개념: Azure Digital twins 솔루션에 대 한 보안*](concepts-security.md) 참조) Azure Digital twins에 액세스 하도록 허용 하려는 각 함수 앱에 대 한 역할을 추가 해야 합니다.

역할을 할당 하려면 만든 Azure Digital Twins 인스턴스의 **리소스 ID가** 필요 합니다. 인스턴스를 만들 때 이전에 기록 하지 않은 경우 다음 명령을 사용 하 여 검색할 수 있습니다.

```bash
az dt show --name <your-instance-name> -g <your-resource-group-name>
```
리소스 ID는 "/subscriptions/..." 문자로 시작 하는 "ID" 라는 긴 문자열로 출력의 일부가 됩니다.

아래 명령의 앞에서 설명한 Azure 함수의 개체 ID와 함께 리소스 ID를 사용 합니다.

```azurecli
az role assignment create --role "Azure Digital Twins Owner (Preview)" --assignee <object-ID> --scope <resource-ID>
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Digital Twins에서 사용할 Azure 함수를 설정 하는 단계를 수행 했습니다. 그런 다음 Event Grid에 Azure 함수를 구독 하 여 끝점에서 수신 대기할 수 있습니다. 이 끝점은 다음과 같을 수 있습니다.
* Azure digital 쌍에 연결 된 Event Grid 끝점 (예: 속성 변경 메시지, 쌍의 [디지털](concepts-twins-graph.md) 쌍에 의해 생성 된 원격 분석 메시지 또는 수명 주기 메시지)을 처리 하기 위해 azure digital 쌍에 연결 된 끝점
* IoT Hub에서 원격 분석 및 기타 장치 이벤트를 전송 하는 데 사용 하는 IoT 시스템 항목입니다.
* 다른 서비스에서 메시지를 수신 하는 Event Grid 끝점

다음으로, Azure Digital Twins에 IoT Hub 데이터를 수집 하는 기본 Azure 함수를 빌드하는 방법을 참조 하세요.
* [*방법: IoT Hub에서 원격 분석 수집*](how-to-ingest-iot-hub-data.md)