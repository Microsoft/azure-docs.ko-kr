---
title: Visual Studio를 사용하여 WebJobs 개발 및 배포 - Azure
description: Visual Studio를 사용하여 Azure App Service에 Azure WebJobs를 개발하고 배포하는 방법에 대해 알아봅니다.
services: app-service
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 02/18/2019
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: ede7e2fe3a2ab4c0dfd4efaea5ec789924968194
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832412"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Visual Studio를 사용하여 WebJobs 개발 및 배포 - Azure App Service

이 문서에서는 Visual Studio에서 웹 앱에 콘솔 응용 프로그램 프로젝트 배포를 사용 하는 방법에 설명 [App Service](overview.md) 으로 [Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226)합니다. [Azure Portal](https://portal.azure.com)을 사용하여 WebJobs를 배포하는 방법에 대한 내용은 [WebJobs로 백그라운드 작업 실행](webjobs-create.md)을 참조하세요.

단일 웹 앱에 여러 WebJobs를 게시할 수 있습니다. 웹 앱에서 각 WebJob 이름이 고유 해야 합니다.

버전 3.x를는 [Azure WebJobs SDK](webjobs-sdk-how-to.md) .NET Core 앱을 실행 하는 WebJobs를 개발 하면 while 2.x가 지 원하는.NET Framework 버전입니다. Webjob 프로젝트를 배포 하는 방법은 다양 한.NET Core 프로젝트와.NET Framework 것입니다.

## <a name="webjobs-as-net-core-console-apps"></a>.NET Core 콘솔 앱으로 WebJobs

버전을 사용 하는 경우 3.x는 WebJobs의 만들기 및.NET Core 콘솔 앱으로 WebJobs를 게시할 수 있습니다. .NET Core 콘솔 응용 프로그램을 Azure WebJob으로 게시 하는 단계별 지침을 참조 하세요 [이벤트 중심 백그라운드 처리를 위해 Azure WebJobs SDK 시작](webjobs-sdk-get-started.md)합니다.

> [!NOTE]
> .NET core WebJobs는 웹 프로젝트를 사용 하 여 연결할 수 없습니다. 웹 앱을 사용 하 여 WebJob을 배포 해야 하는 경우 [만드는.net 콘솔 앱으로 WebJob](#webjobs-as-net-framework-console-apps)합니다.  

### <a name="deploy-to-azure-app-service"></a>Azure App Service에 배포

Visual Studio에서 App Service에.NET Core WebJob 게시 동일한 도구를 사용 하 여 ASP.NET Core 앱을 게시 하는 것입니다.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>WebJob 형식

기본적으로 WebJob 트리거된 경우에 콘솔 프로젝트를 실행 하는.NET Core 또는 요청 시 게시 합니다. 프로젝트를 업데이트할 수도 있습니다 [일정에 따라 실행](#scheduled-execution) 되거나 지속적으로 실행 합니다.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>예약 된 실행

Azure에.NET Core 콘솔 응용 프로그램을 게시할 때 새 *settings.job* 파일이 프로젝트에 추가 됩니다. 이 파일을 사용 하 여 WebJob의 실행 일정을 설정 합니다. 자세한 내용은 [트리거된 WebJob 예약](#scheduling-a-triggered-webjob)합니다.

#### <a name="continuous-execution"></a>연속 실행

WebJob을 지속적으로 Always On 사용 하는 경우 Azure에서 실행 하기를 변경 하려면 Visual Studio를 사용할 수 있습니다.

1. 따라서 아직 수행 하지 않은 경우 [Azure에 프로젝트 게시](#deploy-to-azure-app-service)합니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

1. 에 **게시** 탭에서 **설정**합니다. 

1. 에 **프로필 설정** 대화 상자에서 선택 **연속** 에 대 한 **WebJob 형식**를 선택 하 고 **저장**.

    ![WebJob에 대 한 게시 설정 대화 상자](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. 선택 **게시** 업데이트 된 설정 사용 하 여 WebJob을 다시 게시 합니다.

## <a name="webjobs-as-net-framework-console-apps"></a>.NET Framework 콘솔 응용 프로그램으로 WebJobs  

Visual Studio.NET Framework 콘솔 응용 프로그램 Webjob 지원 프로젝트를 배포 하는 경우 두 가지 작업을 수행 합니다.

* 웹 앱에서 해당 폴더로 런타임 파일을 복사 (*App_Data/작업/continuous* 연속 webjob 및 *App_Data/작업/triggered* 예약 또는 주문형 Webjob에 대 한).
* 특정 시간에 실행되도록 예약된 WebJob에 대해 [Azure Scheduler](https://docs.microsoft.com/azure/scheduler/) 작업을 설정합니다. (연속 WebJob에는 필요하지 않습니다.)

WebJob 지원 프로젝트에는 다음 항목이 추가됩니다.

* [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 패키지
* 배포 및 스케줄러 설정을 포함하는 [webjob-publish-settings.json](#publishsettings) 파일 

![배포를 WebJob으로 설정하기 위해 콘솔 앱에 추가되는 내용을 보여 주는 다이어그램](./media/webjobs-dotnet-deploy-vs/convert.png)

이러한 항목을 기존 콘솔 애플리케이션 프로젝트에 추가하거나 템플릿을 사용하여 새 WebJob 지원 콘솔 애플리케이션 프로젝트를 만들 수 있습니다. 

프로젝트를 WebJob 자체로 배포하거나 웹 프로젝트를 배포할 때마다 자동으로 배포되도록 웹 프로젝트에 연결할 수 있습니다. 프로젝트를 연결할 수 있게 Visual Studio는 웹 프로젝트의 [webjobs-list.json](#webjobslist) 파일에 WebJob 지원 프로젝트의 이름을 포함합니다.

![웹 프로젝트에 연결된 WebJob 프로젝트를 보여 주는 다이어그램](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>필수 조건

Visual Studio 2015를 사용하는 경우 [Azure SDK for .NET(Visual Studio 2015)](https://azure.microsoft.com/downloads/)을 설치합니다.

Visual Studio 2017을 사용하는 경우 [Azure 개발 워크로드](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads)를 설치합니다.

### <a id="convert"></a>기존 콘솔 애플리케이션 프로젝트에 WebJobs 배포 사용

다음 두 가지 옵션을 사용할 수 있습니다.

* [웹 프로젝트를 사용하여 자동 배포 사용](#convertlink).

  기존 콘솔 애플리케이션 프로젝트가 웹 프로젝트를 배포할 때 WebJob으로 자동 배포되도록 구성합니다. 관련 웹 애플리케이션을 실행하는 동일한 웹 앱에서 WebJob을 실행하려는 경우에 이 옵션을 사용합니다.

* [웹 프로젝트를 제외하고 배포](#convertnolink).

  기존 콘솔 애플리케이션 프로젝트가 웹 프로젝트에 연결되지 않고 WebJob 자체로 배포되도록 구성합니다. 웹 앱에서 웹 애플리케이션은 실행하지 않고 WebJob만 실행하려는 경우에 이 옵션을 사용합니다. 웹 애플리케이션 리소스와는 별도로 WebJob 리소스를 확장하기 위해서도 이러한 방식을 원할 수 있습니다.

#### <a id="convertlink"></a> 웹 프로젝트와 함께 자동 WebJob 배포 사용

1. **솔루션 탐색기**에서 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **Existing Project as Azure WebJob(기존 프로젝트를 Azure WebJob으로)** 을 클릭합니다.
   
    ![Existing Project as Azure WebJob(기존 프로젝트를 Azure WebJob으로)](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    [Add Azure WebJob(Azure WebJob 추가)](#configure) 대화 상자가 나타납니다.
2. **프로젝트 이름** 드롭다운 목록에서 WebJob으로 추가할 콘솔 애플리케이션 프로젝트를 선택합니다.
   
    ![Add Azure WebJob(Azure WebJob 추가) 대화 상자에서 프로젝트 선택](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. [Add Azure WebJob(Azure WebJob 추가)](#configure) 대화 상자를 완료하고 **확인**을 클릭합니다. 

#### <a id="convertnolink"></a> 웹 프로젝트를 제외한 WebJob 배포 사용
1. **솔루션 탐색기**에서 콘솔 애플리케이션 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **Azure WebJob 게시...** 를 클릭합니다. 
   
    ![Publish as Azure WebJob(Azure WebJob으로 게시)](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    [프로젝트 이름](#configure) 상자에서 프로젝트가 선택된 상태로 **Add Azure WebJob(Azure WebJob 추가)** 대화 상자가 나타납니다.
2. [Add Azure WebJob(Azure WebJob 추가)](#configure) 대화 상자를 완료하고 **확인**을 클릭합니다.
   
   **웹 게시** 마법사가 나타납니다.  바로 게시하지 않으려면 마법사를 닫습니다. 입력한 설정은 [프로젝트를 배포](#deploy)할 때 사용할 수 있게 저장됩니다.

### <a id="create"></a>새 WebJob 지원 프로젝트 만들기
새 WebJob 지원 프로젝트를 만들려면 [이전 섹션](#convert)에 설명된 대로 콘솔 애플리케이션 프로젝트 템플릿을 사용하고 WebJob 배포를 사용하도록 설정할 수 있습니다. 또는 WebJob new-project 템플릿을 사용할 수도 있습니다.

* [독립 WebJob을 위해 WebJob new-project 템플릿 사용](#createnolink)
  
    프로젝트를 만든 다음 웹 프로젝트에 연결되지 않고 WebJob 자체로 배포되도록 구성합니다. 웹 앱에서 웹 애플리케이션은 실행하지 않고 WebJob만 실행하려는 경우에 이 옵션을 사용합니다. 웹 애플리케이션 리소스와는 별도로 WebJob 리소스를 확장하기 위해서도 이러한 방식을 원할 수 있습니다.
* [웹 프로젝트에 연결된 WebJob을 위해 WebJob new-project 템플릿 사용](#createlink)
  
    동일한 솔루션의 웹 프로젝트가 배포될 때 자동으로 WebJob으로 배포되도록 구성된 프로젝트를 만듭니다. 관련 웹 애플리케이션을 실행하는 동일한 웹 앱에서 WebJob을 실행하려는 경우에 이 옵션을 사용합니다.

> [!NOTE]
> WebJobs new-project 새 프로젝트 템플릿은 NuGet 패키지를 자동으로 설치하고 *WebJobs SDK* 에 대한 [Program.cs](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)에 코드를 포함합니다. WebJobs SDK를 사용하지 않으려면 *Program.cs*에서 `host.RunAndBlock` 문을 제거하거나 변경합니다.
> 
> 

#### <a id="createnolink"></a> 독립 WebJob을 위해 WebJob new-project 템플릿 사용
1. **파일** > **새 프로젝트**를 클릭한 다음 **새 프로젝트** 대화 상자에서 **클라우드** > **Azure WebJob(.NET Framework)** 을 클릭합니다.
   
    ![WebJob 템플릿을 표시하는 새 프로젝트 대화 상자](./media/webjobs-dotnet-deploy-vs/np.png)
2. 앞에 표시된 지침에 따라 [콘솔 애플리케이션 프로젝트를 독립 WebJob 프로젝트로 만듭니다](#convertnolink).

#### <a id="createlink"></a> 웹 프로젝트에 연결된 WebJob을 위해 WebJob new-project 템플릿 사용
1. **솔루션 탐색기**에서 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **New Azure WebJob Project(새 Azure WebJob 프로젝트)** 를 클릭합니다.
   
    ![New Azure WebJob Project(새 Azure WebJob 프로젝트) 메뉴 항목](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    [Add Azure WebJob(Azure WebJob 추가)](#configure) 대화 상자가 나타납니다.
2. [Add Azure WebJob(Azure WebJob 추가)](#configure) 대화 상자를 완료하고 **확인**을 클릭합니다.

### <a id="configure"></a>Azure WebJob 추가 대화 상자
**Azure WebJob 추가** 대화 상자에서 WebJob 이름을 입력하고 WebJob에 대한 모드 설정을 실행할 수 있습니다. 

![Add Azure WebJob(Azure WebJob 추가) 대화 상자](./media/webjobs-dotnet-deploy-vs/aaw2.png)

이 대화 상자의 필드는 Azure Portal의 **WebJob 추가** 대화 상자에 있는 필드에 해당합니다. 자세한 내용은 [WebJobs를 사용하여 백그라운드 작업 실행](webjobs-create.md)을 참조하세요.

> [!NOTE]
> * 명령줄 배포에 대한 자세한 내용은 [Azure WebJob의 명령줄 또는 지속적인 전송 사용](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)을 참조하세요.
> * WebJob을 배포한 다음 WebJob의 유형을 변경하고 재배포하는 경우, *webjobs-publish-settings.json* 파일을 삭제해야 합니다. 이렇게 하면 Visual Studio가 게시 옵션을 다시 표시하므로, 웹 작업의 유형을 변경할 수 있습니다.
> * WebJob을 배포하고 나중에 실행 모드를 연속에서 비연속으로 또는 그 반대로 변경하면 Visual Studio는 사용자가 WebJob을 다시 배포할 때 Azure에서 새 WebJob을 만듭니다. 다른 일정 설정을 변경하고 실행 모드를 그대로 두거나 예약형 및 주문형 간을 전환하면 Visual Studio는 새 작업을 만들지 않고 기존 작업을 업데이트합니다.
> 
> 

### <a id="publishsettings"></a>webjob-publish-settings.json
WebJob 배포를 위해 콘솔 애플리케이션을 구성하는 경우, Visual Studio는 [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 패키지를 설치하고 WebJob 프로젝트의 프로젝트 *Properties* 폴더에 있는 *webjob-publish-settings.json* 파일에 일정 정보를 저장합니다. 다음은 이 파일의 예입니다.

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

이 파일을 직접 편집할 수도 있고 Visual Studio에 제공되는 IntelliSense를 사용할 수도 있습니다. 파일 스키마는 [https://schemastore.org](https://schemastore.org/schemas/json/webjob-publish-settings.json)에 저장되며 이 위치에서 볼 수 있습니다.  

### <a id="webjobslist"></a>webjobs-list.json
WebJob 지원 프로젝트를 웹 프로젝트에 연결하면 Visual Studio는 WebJob 프로젝트의 이름을 웹 프로젝트의 *Properties* 폴더에 있는 *webjobs-list.json* 파일에 저장합니다. 이 목록에는 다음 예와 같은 여러 WebJob 프로젝트가 포함될 수 있습니다.

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

이 파일을 직접 편집할 수도 있고 Visual Studio에 제공되는 IntelliSense를 사용할 수도 있습니다. 파일 스키마는 [https://schemastore.org](https://schemastore.org/schemas/json/webjobs-list.json)에 저장되며 이 위치에서 볼 수 있습니다.

### <a id="deploy"></a>WebJob 프로젝트 배포
웹 프로젝트에 연결한 WebJob 프로젝트는 웹 프로젝트와 함께 자동으로 배포됩니다. 웹 프로젝트 배포에 대한 자세한 내용은 **방법 가이드** > **앱 배포**를 참조하세요.

WebJob 프로젝트 자체적으로 배포하려면 **솔루션 탐색기**에서 이 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Azure WebJob 게시...** 를 클릭합니다. 

![Publish as Azure WebJob(Azure WebJob으로 게시)](./media/webjobs-dotnet-deploy-vs/paw.png)

독립 WebJob의 경우 웹 프로젝트에 사용되는 것과 동일한 **웹 게시** 마법사가 나타나지만 변경할 수 있는 설정은 더 적습니다.

## <a name="scheduling-a-triggered-webjob"></a>트리거된 WebJob 예약

WebJobs를 사용 하는 *settings.job* WebJob이 실행 되는 시점을 결정 하는 파일입니다. 이 파일을 사용 하 여 WebJob의 실행 일정을 설정 합니다. 다음 예에서는 오전 9 시에서 오후 5 시 매시간을 실행합니다.

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

이 파일을 따라 쪽을 WebJob의 스크립트와 같은, WebJobs 폴더의 루트에 있을 수 해야 합니다 `wwwroot\app_data\jobs\triggered\{job name}` 또는 `wwwroot\app_data\jobs\continuous\{job name}`합니다. Visual Studio에서 WebJob을 배포하는 경우 `settings.job` 파일 속성을 **변경된 내용만 복사**로 표시합니다. 

경우 있습니다 [Azure portal에서 WebJob을 만들려면](webjobs-create.md), settings.job 파일 생성 됩니다.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>CRON 식

WebJobs는 Azure Functions에서 타이머 트리거를 예약 하는 데 동일한 CRON 식을 사용 합니다. CRON 지원에 대 한 자세한 내용은 참조는 [타이머 트리거 참조 문서](../azure-functions/functions-bindings-timer.md#cron-expressions)합니다.

### <a name="settingjob-reference"></a>setting.job 참조

다음 설정은 WebJobs에서 지원 됩니다.

| **설정** | **형식**  | **설명** |
| ----------- | --------- | --------------- |
| `is_in_place` | 모두 | 작업을을 먼저 임시 폴더에 복사 되지 않고 현재 위치에서 실행할 수 있습니다. 자세한 내용은 참조 하세요 [WebJobs 작업 디렉터리](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory)합니다. |
| `is_singleton` | 연속 | 만 확장 되는 경우 단일 인스턴스 WebJobs를 실행 합니다. 자세한 내용은 참조 하세요 [연속 작업을 단일 항목으로 설정](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton)합니다. |
| `schedule` | 트리거 | CRON 기반 일정에서 WebJob을 실행 합니다. 자세한 내용은 참조는 [타이머 트리거 참조 문서](../azure-functions/functions-bindings-timer.md#cron-expressions)합니다. |
| `stopping_wait_time`| 모두 | 종료 문제의 제어할 수 있습니다. 자세한 내용은 참조 하세요 [정상적으로 종료](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown)합니다. |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [WebJobs SDK에 대한 자세한 정보](webjobs-sdk-how-to.md)
