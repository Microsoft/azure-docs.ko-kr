---
title: Application Insights를 사용하여 라이브 Azure App Service 앱 프로파일링 | Microsoft Docs
description: Application Insights Profiler를 사용하여 Azure App Service에 라이브 앱을 프로파일링합니다.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: b8f6a2d12e1a9920421e6491432b516520ae110b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730193"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Application Insights를 사용하여 라이브 Azure App Service 앱 프로파일링

기본 서비스 계층을 사용 하 여 Azure App Service에서 실행 중인 앱에 ASP.NET 및 ASP.NET Core에서 Profiler를 실행할 수 있습니다 이상. Linux에서 Profiler를 사용하도록 설정하려면 반드시 [이 방법](profiler-aspnetcore-linux.md)을 사용해야 합니다.

## <a id="installation"></a> 앱에 대해 Profiler 사용
앱에 대해 Profiler를 사용하도록 설정하려면 아래 지침을 따르세요. 다른 유형의 Azure 서비스를 실행 하는 경우 지원 되는 다른 플랫폼에서 Profiler를 사용 하도록 설정 하는 것에 대 한 지침은 다음과 같습니다.
* [Cloud Services](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 애플리케이션](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler App Services 런타임의 일부로 미리 설치 됩니다. 다음 단계는 App Service에 사용 하는 방법을 보여 줍니다. 빌드 시 응용 프로그램에서 App Insights SDK를 포함 한 경우에 이러한 단계를 수행 합니다.

1. App service에 대 한 "무중단" 설정을 사용 하도록 설정 합니다. 일반 설정에서 App Service의 구성 페이지에서 설정을 업데이트할 수 있습니다.
1. Azure Portal에서 **App Services** 창으로 이동합니다.
1. **설정 > Application Insights** 창으로 이동합니다.

   ![App Services 포털에서 App Insights를 사용하도록 설정](./media/profiler/AppInsights-AppServices.png)

1. 창의 지침에 따라 새 리소스를 만들거나 기존 App Insights 리소스를 선택하여 앱을 모니터링합니다. 또한 Profiler가 **켜기** 상태인지 확인합니다. Application Insights 리소스에 있는 경우 App Service에서 다른 구독에는 Application Insights를 구성 하려면이 페이지를 사용할 수 없습니다. 계속 하면 것 수동으로 있지만 필요한 앱 설정을 만들고 수동으로. [다음 섹션에서는 수동으로 Profiler를 사용 하기 위한 지침을 포함 합니다.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![App Insights 사이트 확장 추가][Enablement UI]

1. 이제 Profiler가 App Services 앱 설정을 사용하여 활성화됩니다.

    ![Profiler에 대한 앱 설정][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>수동으로 또는 Azure Resource Manager를 사용 하 여 Profiler를 사용 하도록 설정
Azure App Service에 대 한 앱 설정을 만들어 Application Insights Profiler 사용할 수 있습니다. 위에 표시 된 옵션을 사용 하 여 페이지에는 이러한 앱 설정을 만듭니다. 하지만 템플릿 또는 다른 수단을 사용 하 여 이러한 설정 생성을 자동화할 수 있습니다. 이러한 설정은 Application Insights 리소스는 Azure App Service에서 다른 구독에 있는 경우에 작동 합니다.
프로파일러를 사용 하도록 설정 하는 데 필요한 설정은 다음과 같습니다.

|앱 설정    | 값    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights 리소스의 iKey    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


사용 하 여 이러한 값을 설정할 수 있습니다 [Azure Resource Manager 템플릿](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager)를 [Azure Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp)합니다 [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest)합니다.


## <a name="disable-profiler"></a>Profiler 사용 안 함

개별 앱의 인스턴스에 대해 Profiler를 중지하거나 다시 시작하려면 **Web Jobs**에서 앱 리소스로 이동합니다. Profiler를 삭제하려면 **확장**으로 이동합니다.

![웹 작업에 대한 Profiler 사용 안 함][disable-profiler-webjob]

성능 문제를 가능한 한 빨리 검색하려면 모든 앱에서 Profiler를 사용하도록 설정하는 것이 좋습니다.

WebDeploy를 사용 하 여 변경 사항을 웹 응용 프로그램에 배포할 때 Profiler의 파일을 삭제할 수 있습니다. 배포 하는 동안 삭제 되 고 App_Data 폴더를 제외 하 여 삭제를 방지할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

* [Visual Studio에서 Application Insights로 작업](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
