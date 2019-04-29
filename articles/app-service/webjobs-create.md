---
title: WebJobs로 백그라운드 작업 실행 - Azure App Service
description: WebJobs를 사용하여 Azure App Service 웹앱, API 앱 또는 모바일 앱에서 백그라운드 작업을 실행하는 방법을 알아봅니다.
services: app-service
documentationcenter: ''
author: ggailey777
manager: jeconnoc
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: glenga;msangapu;david.ebbo;suwatch;pbatum;naren.soni;
ms.custom: seodec18
ms.openlocfilehash: 0f2053e978b7c890f4e175515ed54f69694950c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60833585"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Azure App Service에서 WebJobs로 백그라운드 작업 실행

## <a name="overview"></a>개요
WebJobs는 웹앱, API 앱 또는 모바일 앱과 동일한 컨텍스트에서 프로그램이나 스크립트를 실행할 수 있도록 하는 [Azure App Service](https://docs.microsoft.com/azure/app-service/)의 기능입니다. 웹 작업을 사용하는 데 추가 비용은 없습니다.

> [!IMPORTANT]
> WebJobs는 Linux의 App Service에 대해서는 아직 지원되지 않습니다.

이 문서에서는 실행 파일 또는 스크립트를 업로드하기 위해 [Azure Portal](https://portal.azure.com)을 사용하여 WebJobs를 배포하는 방법을 보여줍니다. Visual Studio를 사용하여 WebJobs를 개발하고 배포하는 방법에 대한 정보는 [Visual Studio를 사용하여 WebJobs 배포](webjobs-dotnet-deploy-vs.md)를 참조하세요.

여러 프로그래밍 작업을 간소화하기 위해 Azure WebJobs SDK를 WebJobs로 사용할 수 있습니다. 자세한 내용은 [WebJobs SDK 정의](https://github.com/Azure/azure-webjobs-sdk/wiki)를 참조하세요.

Azure Functions는 프로그램 및 스크립트를 실행하는 다른 방법을 제공합니다. WebJobs와 Functions 간에 비교는 [Flow, Logic Apps, Functions 및 WebJobs 중에서 선택](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)을 참조하세요.

## <a name="webjob-types"></a>WebJob 형식

다음 표에서는 *연속* 및 *트리거* WebJobs 간의 차이점을 설명합니다.


|연속  |트리거  |
|---------|---------|
| WebJob이 만들어질 때 즉시 시작합니다. 작업을 종료하지 않으려면 프로그램 또는 스크립트는 일반적으로 무한 루프 내에서 작업을 수행합니다. 작업을 종료하는 경우 다시 시작할 수 있습니다. | 수동으로 또는 일정에서 트리거된 경우에만 시작합니다. |
| 웹앱이 실행되는 모든 인스턴스에서 실행됩니다. 필요에 따라 WebJob을 단일 인스턴스로 제한할 수 있습니다. |Azure에서 부하 분산을 위해 선택한 단일 인스턴스에서 실행합니다.|
| 원격 디버깅을 지원합니다. | 원격 디버깅을 지원하지 않습니다.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="acceptablefiles"></a>스크립트 또는 프로그램에 지원되는 파일 형식

다음 파일 형식이 지원됩니다.

* .cmd, .bat, .exe(Windows cmd 사용)
* .ps1(PowerShell 사용)
* .sh(Bash 사용)
* .php(PHP 사용)
* .py(Python 사용)
* .js(Node.js 사용)
* .jar(Java 사용)

## <a name="CreateContinuous"></a>연속 WebJob 만들기

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. [Azure Portal](https://portal.azure.com)에서 App Service 웹앱, API 앱 또는 모바일 앱의 **App Service** 페이지로 이동합니다.

2. **WebJobs**를 선택합니다.

   ![WebJobs 선택](./media/web-sites-create-web-jobs/select-webjobs.png)

2. **WebJobs** 페이지에서 **추가**를 선택합니다.

    ![WebJob 페이지](./media/web-sites-create-web-jobs/wjblade.png)

3. 표에 지정된 대로 **WebJob 추가** 설정을 사용합니다.

   ![WebJob 페이지 추가](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | 설정      | 샘플 값   | 설명  |
   | ------------ | ----------------- | ------------ |
   | **이름** | myContinuousWebJob | App Service 앱 내에서 고유한 이름입니다. 문자 또는 숫자로 시작해야 하며 "-" 및 "_"을 제외한 다른 특수 문자를 포함할 수 없습니다. |
   | **파일 업로드** | ConsoleApp.zip | 실행 파일 또는 스크립트 파일뿐만 아니라 프로그램 또는 스크립트를 실행하는 데 필요한 지원 파일이 포함되는 *.zip* 파일입니다. 지원되는 실행 파일 또는 스크립트 파일 형식은 [지원되는 파일 형식](#acceptablefiles) 섹션에 나열됩니다. |
   | **형식** | 연속 | [WebJob 형식](#webjob-types)은 이 문서의 앞부분에서 설명됩니다. |
   | **규모** | 다중 인스턴스 | 연속 WebJobs에 대해서만 사용할 수 있습니다. 프로그램 또는 스크립트가 모든 인스턴스 또는 하나의 인스턴스에서 실행되는지를 결정합니다. 여러 인스턴스에서 실행하는 옵션은 무료 또는 공유 [가격 책정 계층](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)에 적용되지 않습니다. | 

4. **확인**을 클릭합니다.

   새 WebJob이 **WebJobs** 페이지에 표시됩니다.

   ![WebJobs 목록](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. 연속 WebJob을 중지하거나 시작하려면 목록에서 WebJob을 마우스 오른쪽 단추로 클릭하고 **중지** 또는 **시작**을 클릭합니다.

    ![연속 WebJob 중지](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a>수동으로 트리거된 WebJob 만들기

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. [Azure Portal](https://portal.azure.com)에서 App Service 웹앱, API 앱 또는 모바일 앱의 **App Service** 페이지로 이동합니다.

2. **WebJobs**를 선택합니다.

   ![WebJobs 선택](./media/web-sites-create-web-jobs/select-webjobs.png)

2. **WebJobs** 페이지에서 **추가**를 선택합니다.

    ![WebJob 페이지](./media/web-sites-create-web-jobs/wjblade.png)

3. 표에 지정된 대로 **WebJob 추가** 설정을 사용합니다.

   ![WebJob 페이지 추가](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | 설정      | 샘플 값   | 설명  |
   | ------------ | ----------------- | ------------ |
   | **이름** | myTriggeredWebJob | App Service 앱 내에서 고유한 이름입니다. 문자 또는 숫자로 시작해야 하며 "-" 및 "_"을 제외한 다른 특수 문자를 포함할 수 없습니다.|
   | **파일 업로드** | ConsoleApp.zip | 실행 파일 또는 스크립트 파일뿐만 아니라 프로그램 또는 스크립트를 실행하는 데 필요한 지원 파일이 포함되는 *.zip* 파일입니다. 지원되는 실행 파일 또는 스크립트 파일 형식은 [지원되는 파일 형식](#acceptablefiles) 섹션에 나열됩니다. |
   | **형식** | 트리거 | [WebJob 형식](#webjob-types)은 이 문서의 앞부분에서 설명됩니다. |
   | **트리거** | 설명서 | |

4. **확인**을 클릭합니다.

   새 WebJob이 **WebJobs** 페이지에 표시됩니다.

   ![WebJobs 목록](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. 웹 작업을 실행하려면 목록에서 해당 이름을 마우스 오른쪽 단추로 클릭하고 **실행**을 클릭합니다.
   
    ![WebJob 실행](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a>예약된 WebJob 만들기

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. [Azure Portal](https://portal.azure.com)에서 App Service 웹앱, API 앱 또는 모바일 앱의 **App Service** 페이지로 이동합니다.

2. **WebJobs**를 선택합니다.

   ![WebJobs 선택](./media/web-sites-create-web-jobs/select-webjobs.png)

2. **WebJobs** 페이지에서 **추가**를 선택합니다.

   ![WebJob 페이지](./media/web-sites-create-web-jobs/wjblade.png)

3. 표에 지정된 대로 **WebJob 추가** 설정을 사용합니다.

   ![WebJob 페이지 추가](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | 설정      | 샘플 값   | 설명  |
   | ------------ | ----------------- | ------------ |
   | **이름** | myScheduledWebJob | App Service 앱 내에서 고유한 이름입니다. 문자 또는 숫자로 시작해야 하며 "-" 및 "_"을 제외한 다른 특수 문자를 포함할 수 없습니다. |
   | **파일 업로드** | ConsoleApp.zip | 실행 파일 또는 스크립트 파일뿐만 아니라 프로그램 또는 스크립트를 실행하는 데 필요한 지원 파일이 포함되는 *.zip* 파일입니다. 지원되는 실행 파일 또는 스크립트 파일 형식은 [지원되는 파일 형식](#acceptablefiles) 섹션에 나열됩니다. |
   | **형식** | 트리거 | [WebJob 형식](#webjob-types)은 이 문서의 앞부분에서 설명됩니다. |
   | **트리거** | 예약됨 | 안정적으로 실행되도록 예약하기 위해 Always On 기능을 사용하도록 설정합니다. Always On은 기본, 표준 및 프리미엄 가격 책정 계층에서만 사용할 수 있습니다.|
   | **CRON 식** | 0 0/20 * * * * | [CRON 식](#cron-expressions)이 다음 섹션에 설명되어 있습니다. |

4. **확인**을 클릭합니다.

   새 WebJob이 **WebJobs** 페이지에 표시됩니다.

   ![WebJobs 목록](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="cron-expressions"></a>CRON 식

다음 예제와 같이 포털에 [CRON 식](../azure-functions/functions-bindings-timer.md#cron-expressions)을 입력하거나 WebJob *.zip* 파일의 루트에 `settings.job` 파일을 포함할 수 있습니다.

```json
{
    "schedule": "0 */15 * * * *"
}
```

자세한 내용은 참조 하세요 [트리거된 WebJob 예약](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob)합니다.

## <a name="ViewJobHistory"></a>작업 기록 보기

1. 기록을 확인하려는 WebJob을 선택하고 **로그** 단추를 선택합니다.
   
   ![로그 단추](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. **WebJob 세부 정보** 페이지에서 하나를 실행하는 세부 내용을 확인하려면 시간을 선택합니다.
   
   ![WebJob 세부 정보](./media/web-sites-create-web-jobs/webjobdetails.png)

3. **WebJob 실행 세부 정보** 페이지에서 **토글 출력**을 선택하여 로그 내용의 텍스트를 확인합니다.
   
    ![WebJob 실행 세부 작업](./media/web-sites-create-web-jobs/webjobrundetails.png)

   별도의 브라우저 창에서 출력 텍스트를 보려면 **다운로드**를 선택합니다. 텍스트 자체를 다운로드하려면 **다운로드**를 마우스 오른쪽 단추로 클릭하고 브라우저 옵션을 사용하여 파일 내용을 저장합니다.
   
5. 페이지 맨 위에 있는 **WebJobs** 이동 경로 탐색 링크를 선택하여 WebJobs의 목록으로 이동합니다.

    ![WebJob 이동 경로 탐색](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![기록 대시보드의 작업 목록](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a> 다음 단계

여러 프로그래밍 작업을 간소화하기 위해 Azure WebJobs SDK를 WebJobs로 사용할 수 있습니다. 자세한 내용은 [WebJobs SDK 정의](https://github.com/Azure/azure-webjobs-sdk/wiki)를 참조하세요.
