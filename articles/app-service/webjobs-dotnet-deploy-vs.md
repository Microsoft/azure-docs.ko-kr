---
title: Visual Studio를 사용 하 여 WebJobs 개발 및 배포
description: Visual Studio에서 Azure WebJobs를 개발 하 고 예약 된 작업 만들기를 포함 하 여 Azure App Service에 배포 하는 방법을 알아봅니다.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: devx-track-csharp, vs-azure
ms.date: 07/30/2020
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: de10903be86b52b3415b57a53be81e7fd1661f63
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226032"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio"></a>Visual Studio를 사용 하 여 WebJobs 개발 및 배포

이 문서에서는 Visual Studio를 사용 하 여 콘솔 앱 프로젝트를 [Azure App Service](overview.md) 의 웹 앱에 [Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226)으로 배포 하는 방법을 설명 합니다. [Azure Portal](https://portal.azure.com)를 사용 하 여 WebJobs를 배포 하는 방법에 대 한 자세한 내용은 [Azure App Service에서 WebJobs를 사용 하 여 백그라운드 작업 실행](webjobs-create.md)을 참조 하세요.

[.Net Core 앱](#webjobs-as-net-core-console-apps) 또는 [.NET Framework 앱](#webjobs-as-net-framework-console-apps)으로 실행 되는 WebJob을 개발 하도록 선택할 수 있습니다. [AZURE WEBJOBS SDK](webjobs-sdk-how-to.md) 버전 3.x를 사용 하면 .net Core 앱 또는 .NET Framework 앱으로 실행 되는 WebJobs를 개발할 수 있으며, 버전 2.x는 .NET Framework만 지원 합니다. WebJobs 프로젝트를 배포 하는 방법은 .NET Framework 프로젝트에 대 한 .NET Core 프로젝트의 경우와 다릅니다.

웹 앱의 각 WebJob에 고유한 이름이 있으면 단일 웹 앱에 여러 WebJobs를 게시할 수 있습니다.

## <a name="webjobs-as-net-core-console-apps"></a>.NET Core 콘솔 앱으로 WebJobs

Azure WebJobs SDK 버전 3.x를 사용 하 여 WebJobs를 .NET Core 콘솔 앱으로 만들고 게시할 수 있습니다. .NET Core 콘솔 앱을 만들고 WebJob으로 Azure에 게시 하는 단계별 지침은 [이벤트 중심 백그라운드 처리를 위한 AZURE WEBJOBS SDK 시작](webjobs-sdk-get-started.md)을 참조 하세요.

> [!NOTE]
> .NET Core WebJobs는 웹 프로젝트와 연결할 수 없습니다. 웹 앱을 사용 하 여 WebJob을 배포 해야 하는 경우 [.NET Framework 콘솔 앱으로 WebJobs을 만듭니다](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Azure App Service에 배포

Visual Studio에서 Azure App Service에 .NET Core WebJob을 게시 하면 ASP.NET Core 앱을 게시 하는 것과 동일한 도구가 사용 됩니다.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

## <a name="webjobs-as-net-framework-console-apps"></a>.NET Framework 콘솔 앱으로 WebJobs  

Visual Studio를 사용 하 여 WebJobs를 사용 하는 .NET Framework 콘솔 앱 프로젝트를 배포 하면 런타임 파일이 웹 앱의 해당 폴더에 복사 됩니다 (*App_Data/jobs/continuous* For 연속 WebJobs 및 *App_Data/Jobs/triggered* for demand WebJobs).

Visual Studio는 WebJobs 사용 프로젝트에 다음 항목을 추가 합니다.

* [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 패키지
* 배포 및 스케줄러 설정을 포함하는 [webjob-publish-settings.json](#publishsettings) 파일 

![WebJob으로 배포를 사용 하도록 설정 하기 위해 콘솔 앱에 추가 되는 내용을 보여 주는 다이어그램](./media/webjobs-dotnet-deploy-vs/convert.png)

기존 콘솔 앱 프로젝트에 이러한 항목을 추가 하거나 템플릿을 사용 하 여 새 WebJobs 사용 콘솔 앱 프로젝트를 만들 수 있습니다. 

프로젝트를 WebJob으로 배포 하거나 웹 프로젝트를 배포할 때마다 자동으로 배포 되도록 웹 프로젝트에 연결 합니다. 프로젝트를 연결할 수 있게 Visual Studio는 웹 프로젝트의 [webjobs-list.json](#webjobslist) 파일에 WebJob 지원 프로젝트의 이름을 포함합니다.

![웹 프로젝트에 연결된 WebJob 프로젝트를 보여 주는 다이어그램](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>필수 구성 요소

[Azure 개발 워크 로드](/visualstudio/install/install-visual-studio#step-4---choose-workloads)를 사용 하 여 visual studio 2017 또는 visual studio 2019를 설치 합니다.

### <a name="enable-webjobs-deployment-for-an-existing-console-app-project"></a><a id="convert"></a> 기존 콘솔 응용 프로그램 프로젝트에 WebJobs 배포 사용

다음 두 가지 옵션을 사용할 수 있습니다.

* [웹 프로젝트를 사용하여 자동 배포 사용](#convertlink).

  웹 프로젝트를 배포할 때 자동으로 WebJob으로 배포 되도록 기존 콘솔 앱 프로젝트를 구성 합니다. 관련 웹 애플리케이션을 실행하는 동일한 웹 앱에서 WebJob을 실행하려는 경우에 이 옵션을 사용합니다.

* [웹 프로젝트를 제외하고 배포](#convertnolink).

  웹 프로젝트에 대 한 링크 없이 WebJob으로 배포 하도록 기존 콘솔 앱 프로젝트를 구성 합니다. 웹 앱에서 웹 애플리케이션은 실행하지 않고 WebJob만 실행하려는 경우에 이 옵션을 사용합니다. 웹 응용 프로그램 리소스와 별개로 WebJob 리소스의 크기를 조정 하기 위해이 작업을 수행 하는 것이 좋습니다.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> 웹 프로젝트와 함께 자동 WebJob 배포 사용

1. **솔루션 탐색기**에서 웹 프로젝트를 마우스 오른쪽 단추로 클릭 한 다음 **Add**  >  **Azure WebJob으로 기존 프로젝트**추가를 선택 합니다.
   
    ![Existing Project as Azure WebJob(기존 프로젝트를 Azure WebJob으로)](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    [Add Azure WebJob(Azure WebJob 추가)](#configure) 대화 상자가 나타납니다.
2. **프로젝트 이름** 드롭다운 목록에서 WebJob으로 추가할 콘솔 앱 프로젝트를 선택 합니다.
   
    ![Azure WebJob 추가 대화 상자에서 프로젝트 선택](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. [Azure WebJob 추가](#configure) 대화 상자를 완료 하 고 **확인**을 선택 합니다. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> 웹 프로젝트를 제외한 WebJob 배포 사용
1. **솔루션 탐색기**에서 콘솔 응용 프로그램 프로젝트를 마우스 오른쪽 단추로 클릭 한 다음 **Azure WebJob으로 게시**를 선택 합니다. 
   
    ![Publish as Azure WebJob(Azure WebJob으로 게시)](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    [프로젝트 이름](#configure) 상자에서 프로젝트가 선택된 상태로 **Add Azure WebJob(Azure WebJob 추가)** 대화 상자가 나타납니다.
2. [Azure WebJob 추가](#configure) 대화 상자를 완료 하 고 **확인**을 선택 합니다.
   
   **웹 게시** 마법사가 나타납니다. 바로 게시하지 않으려면 마법사를 닫습니다. 입력한 설정은 [프로젝트를 배포](#deploy)할 때 사용할 수 있게 저장됩니다.

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>새 WebJob 지원 프로젝트 만들기
새 WebJobs 사용 프로젝트를 만들려면 콘솔 앱 프로젝트 템플릿을 사용 하 고 [이전 섹션](#convert)에서 설명한 대로 WebJobs 배포를 사용 하도록 설정 합니다. 또는 WebJob new-project 템플릿을 사용할 수도 있습니다.

* [독립적인 WebJob에 WebJobs 새 프로젝트 템플릿 사용](#createnolink)
  
    프로젝트를 만든 다음 웹 프로젝트에 연결되지 않고 WebJob 자체로 배포되도록 구성합니다. 웹 앱에서 웹 애플리케이션은 실행하지 않고 WebJob만 실행하려는 경우에 이 옵션을 사용합니다. 웹 응용 프로그램 리소스와 별개로 WebJob 리소스의 크기를 조정 하기 위해이 작업을 수행 하는 것이 좋습니다.
* [웹 프로젝트에 연결 된 WebJob에 대해 WebJobs 새 프로젝트 템플릿 사용](#createlink)
  
    동일한 솔루션에서 웹 프로젝트를 배포할 때 WebJob으로 자동 배포 하도록 구성 된 프로젝트를 만듭니다. 관련 웹 애플리케이션을 실행하는 동일한 웹 앱에서 WebJob을 실행하려는 경우에 이 옵션을 사용합니다.

> [!NOTE]
> WebJobs new-project 새 프로젝트 템플릿은 NuGet 패키지를 자동으로 설치하고 *WebJobs SDK* 에 대한 [Program.cs](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)에 코드를 포함합니다. WebJobs SDK를 사용하지 않으려면 *Program.cs*에서 `host.RunAndBlock` 문을 제거하거나 변경합니다.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> 독립 WebJob을 위해 WebJob new-project 템플릿 사용
1. **파일** > **새로 만들기** > **프로젝트**를 선택합니다. **Crete a 새 프로젝트** 대화 상자에서 c #에 대 한 **Azure WebJob (.NET Framework)** 을 검색 하 고 선택 합니다.
   
2. 이전 지침에 따라 [콘솔 앱 프로젝트를 독립 WebJobs 프로젝트로 만듭니다](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> 웹 프로젝트에 연결된 WebJob을 위해 WebJob new-project 템플릿 사용
1. **솔루션 탐색기**에서 웹 프로젝트를 마우스 오른쪽 단추로 클릭 한 다음 **Add**  >  **새 Azure WebJob 프로젝트**추가를 선택 합니다.
   
    ![New Azure WebJob Project(새 Azure WebJob 프로젝트) 메뉴 항목](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    [Add Azure WebJob(Azure WebJob 추가)](#configure) 대화 상자가 나타납니다.
2. [Azure WebJob 추가](#configure) 대화 상자를 완료 하 고 **확인**을 선택 합니다.


### <a name="webjob-publish-settingsjson-file"></a><a id="publishsettings"></a> 파일에webjob-publish-settings.js
WebJobs 배포용 콘솔 앱을 구성 하는 경우 Visual Studio는 [WebJobs](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 패키지를 설치 하 고 WebJobs 프로젝트의 프로젝트 *속성* 폴더에 있는 파일 *에 대 한webjob-publish-settings.js* 에 일정 정보를 저장 합니다. 다음은 이 파일의 예입니다.

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
  "webJobName": "WebJob1",
  "startTime": "null",
  "endTime": "null",
  "jobRecurrenceFrequency": "null",
  "interval": null,
  "runMode": "Continuous"
}
```

이 파일을 직접 편집할 수도 있고 Visual Studio에 제공되는 IntelliSense를 사용할 수도 있습니다. 파일 스키마는에 저장 되며 [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) 여기에서 볼 수 있습니다.  

### <a name="webjobs-listjson-file"></a><a id="webjobslist"></a> 파일에webjobs-list.js
WebJob 지원 프로젝트를 웹 프로젝트에 연결하면 Visual Studio는 WebJob 프로젝트의 이름을 웹 프로젝트의 *Properties* 폴더에 있는 *webjobs-list.json* 파일에 저장합니다. 이 목록에는 다음 예와 같은 여러 WebJob 프로젝트가 포함될 수 있습니다.

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
  "WebJobs": [
    {
      "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
    },
    {
      "filePath": "../WebJob1/WebJob1.csproj"
    }
  ]
}
```

IntelliSense를 사용 하 여 Visual Studio에서이 파일을 직접 편집할 수 있습니다. 파일 스키마는에 저장 됩니다 [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) .

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>WebJob 프로젝트 배포
웹 프로젝트에 연결 된 WebJobs 프로젝트는 웹 프로젝트와 함께 자동으로 배포 됩니다. 웹 프로젝트 배포에 대 한 자세한 내용은 **How-to guides**  >  왼쪽 탐색에서**앱 배포** 방법 가이드를 참조 하세요.

WebJobs 프로젝트를 단독으로 배포 하려면 **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **Azure WebJob으로 게시**를 선택 합니다. 

![Publish as Azure WebJob(Azure WebJob으로 게시)](./media/webjobs-dotnet-deploy-vs/paw.png)

독립 WebJob의 경우 웹 프로젝트에 사용되는 것과 동일한 **웹 게시** 마법사가 나타나지만 변경할 수 있는 설정은 더 적습니다.

### <a name="add-azure-webjob-dialog-box"></a><a id="configure"></a>Azure WebJob 추가 대화 상자
**Azure Webjob 추가** 대화 상자를 사용 하 여 WebJob의 webjob 이름 및 실행 모드 설정을 입력할 수 있습니다. 

![Azure WebJob 추가 대화 상자](./media/webjobs-dotnet-deploy-vs/aaw2.png)

이 대화 상자의 일부 필드는 Azure Portal의 **WebJob 추가** 대화 상자에 있는 필드에 해당 합니다. 자세한 내용은 [Azure App Service에서 WebJobs를 사용 하 여 백그라운드 작업 실행](webjobs-create.md)을 참조 하세요.

WebJob 배포 정보:

* 명령줄 배포에 대한 자세한 내용은 [Azure WebJob의 명령줄 또는 지속적인 전송 사용](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)을 참조하세요.

* WebJob을 배포 하 고 WebJob의 유형을 변경 하 고 다시 배포 하는 경우 파일 * 에서webjobs-publish-settings.js* 을 삭제 합니다. 이렇게 하면 Visual Studio에서 게시 옵션을 다시 표시 하므로 WebJob의 유형을 변경할 수 있습니다.

* WebJob을 배포하고 나중에 실행 모드를 연속에서 비연속으로 또는 그 반대로 변경하면 Visual Studio는 사용자가 WebJob을 다시 배포할 때 Azure에서 새 WebJob을 만듭니다. 다른 일정 설정을 변경 하는 경우 실행 모드를 동일 하거나 예약 된 요청 사이에서 전환 하는 경우 Visual Studio는 새 작업을 만드는 대신 기존 작업을 업데이트 합니다.

## <a name="webjob-types"></a>WebJob 형식

WebJob의 형식은 *트리거됨* 또는 *연속*일 수 있습니다.

- 트리거됨 (기본값): 트리거된 WebJob은 바인딩 이벤트 또는 [일정](#scheduling-a-triggered-webjob)에 따라 시작 하거나 요청 시 수동으로 트리거할 때 시작 됩니다. 웹 앱이 실행 되는 단일 인스턴스에서 실행 됩니다.

- 연속: WebJob을 만들 때 [연속](#continuous-execution) webjob이 즉시 시작 됩니다. 기본적으로 모든 웹 앱 크기 조정 된 인스턴스에서 실행 되지만 *설정. 작업*을 통해 단일 인스턴스로 실행 되도록 구성할 수 있습니다.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="scheduling-a-triggered-webjob"></a>트리거형 웹 작업 일정 예약

콘솔 앱을 Azure에 게시 하는 경우 Visual Studio는 기본적으로 WebJob의 유형을 트리거하고 새 *설정 작업* **파일을 프로젝트** 에 추가 합니다. 트리거된 WebJob 형식의 경우이 파일을 사용 하 여 WebJob의 실행 일정을 설정할 수 있습니다.

*설정. 작업* 파일을 사용 하 여 WebJob의 실행 일정을 설정 합니다. 다음 예에서는 매시간 오전 9 시에서 오후 5 시까지 실행 됩니다.

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

이 파일은 WebJob의 스크립트 (예: 또는)를 사용 하 여 WebJobs 폴더의 루트에 있습니다 `wwwroot\app_data\jobs\triggered\{job name}` `wwwroot\app_data\jobs\continuous\{job name}` . Visual Studio에서 WebJob을 배포 하는 경우 Visual Studio의 작업 파일 속성을 **새 버전으로 복사**로 표시 *합니다.*

[Azure Portal에서 WebJob을 만드는](webjobs-create.md)경우에는 *설정. 작업* 파일이 만들어집니다.

#### <a name="cron-expressions"></a>CRON 식

WebJobs는 Azure Functions에서 타이머 트리거로 일정을 예약 하는 데 동일한 CRON 식을 사용 합니다. CRON 지원에 대 한 자세한 내용은 [Azure Functions에 대 한 타이머 트리거](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)를 참조 하세요.

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

#### <a name="settingsjob-reference"></a>설정. 작업 참조

WebJobs에서 지원 되는 설정은 다음과 같습니다.

| **설정** | **형식**  | **설명** |
| ----------- | --------- | --------------- |
| `is_in_place` | 모두 | WebJob을 임시 폴더에 먼저 복사 하지 않고 대신 실행할 수 있습니다. 자세한 내용은 [WebJob 작업 디렉터리](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory)를 참조 하세요. |
| `is_singleton` | 계속 | 규모 확장 시 단일 인스턴스에 대해 WebJob을 실행 합니다. 자세한 내용은 [연속 작업을 singleton으로 설정](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton)을 참조 하세요. |
| `schedule` | 트리거 | CRON 기반 일정에 따라 WebJob을 실행 합니다. 자세한 내용은 [NCRONTAB expressions](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)을 참조 하세요. |
| `stopping_wait_time`| 모두 | 종료 동작을 제어할 수 있습니다. 자세한 내용은 [정상 종료](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown)를 참조하세요. |

### <a name="continuous-execution"></a>연속 실행

Azure에서 **Always on** 을 사용 하도록 설정 하는 경우 Visual Studio를 사용 하 여 WebJob을 지속적으로 실행 하도록 변경할 수 있습니다.

1. 아직 수행 하지 않은 경우 프로젝트를 [Azure에 게시](#deploy-to-azure-app-service)합니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

1. **게시** 탭에서 **편집**을 선택 합니다. 

1. **프로필 설정** 대화 상자에서 **WebJob 유형**으로 **연속** 을 선택 하 고 **저장**을 선택 합니다.

    ![WebJob의 게시 설정 대화 상자](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. **게시** 탭에서 **게시** 를 선택 하 여 업데이트 된 설정으로 WebJob을 다시 게시 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [WebJobs SDK에 대한 자세한 정보](webjobs-sdk-how-to.md)