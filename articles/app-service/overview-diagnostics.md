---
title: 진단 개요 - Azure App Service | Microsoft Docs
description: App Service 진단을 사용하여 웹앱 관련 문제를 해결하는 방법을 알아봅니다.
keywords: App Service, Azure App Service, 진단, 지원, 웹앱, 문제 해결, 자가 진단
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: a8b256f43d8e4103404ab4276431ceb06d9ed36a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60839438"
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure App Service 진단 개요 

웹 애플리케이션을 실행할 때 500개 오류부터 사용자가 사이트의 작동이 중단되었음을 알리는 사용자에 이르기까지 발생할 수 있는 문제에 대비해야 합니다. App Service 진단은 구성할 필요 없이 웹앱 문제를 해결하는 데 도움이 되는 지능적인 대화형 환경입니다. 웹앱에서 문제가 발생하면 App Service 진단은 문제를 더 쉽고 빠르게 확인하고 해결하기 위해 올바른 정보로 안내하여 무엇이 문제인지를 지적합니다. 
 
이 환경은 지난 24시간 동안 웹앱에 문제가 있을 때 가장 유용하지만, 언제든지 분석할 수 있도록 모든 진단 그래프가 제공됩니다. 추가 문제 해결 도구 및 유용한 문서와 포럼에 대한 링크는 오른쪽 열에 있습니다.

App Service 진단은 Windows의 앱 뿐만 아니라 [Linux/컨테이너](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [App Service 환경](https://docs.microsoft.com/azure/app-service/environment/intro) 및 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)의 앱에도 작용합니다. 

## <a name="open-app-service-diagnostics"></a>App Service 진단 열기

App Service 진단에 액세스하려면 [Azure Portal](https://portal.azure.com)의 App Service 앱 또는 App Service 환경으로 이동합니다. 왼쪽 탐색 메뉴에서 **문제 진단 및 해결**을 클릭합니다. 

Azure Functions의 경우 함수 앱으로 이동한 후 위쪽 탐색 영역에서 **플랫폼 기능**을 클릭하고 **모니터링**섹션에서 **진단 및 문제 해결**을 선택합니다. 

![홈페이지](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>상태 검사

웹앱 문제를 알지 못하거나 문제 해결을 시작할 수 있는 위치를 모르는 경우 상태 검사를 시작하는 것이 좋습니다. 상태 검사는 웹 애플리케이션을 분석하여 정상 및 비정상 문제를 지적하고 해당 문제를 조사할 위치를 알려주는 대화형 개요를 빠르게 제공합니다. 지능적인 대화형 인터페이스는 문제 해결 프로세스를 통해 지침을 제공합니다.  

![상태 검사](./media/app-service-diagnostics/HealthCheckup2.png)

지난 24시간 동안 특정 문제 범주에 속한 문제가 검색되면, 전체 진단 보고서를 볼 수 있으며, App Service 진단에서 문제 해결에 대한 자세한 권장 사항과 자세한 단계별 환경의 다음 단계를 확인하도록 요구할 수 있습니다.

![문제 해결 및 다음 단계](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>타일 바로 가기

찾고 있는 문제 해결 정보의 유형을 정확히 알고 있는 경우 타일 바로 가기를 사용하면 관심 있는 문제 범주에 속하는 전체 진단 보고서로 바로 이동할 수 있습니다. 상태 검사와 비교할 때, 타일 바로 가기는 직접적이지만 비교적 간단한 단계별 안내 방식으로 진단 메트릭에 액세스할 수 있습니다. 애플리케이션 코드 문제, 속도 저하, 연결 문자열 등과 관련된 문제를 조사하는 데 도움이 되는 고급 도구인 **진단 도구**도 타일 바로 가기의 일부로 포함되어 있습니다. 

![타일 바로 가기](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>진단 보고서

[상태 검사](#health-checkup)를 실행한 후에 자세한 정보가 필요하든지 또는 [타일 바로 가기](#tile-shortcuts) 중 하나를 클릭했든지 간에 진단 보고서에는 지난 24시간 동안의 관련 그래프 메트릭이 표시됩니다. 앱에 가동 중지가 발생하면 타임라인 아래에 주황색 막대로 표시됩니다. 주황색 막대 중 하나를 선택하여 가동 중지 시간을 선택하면 해당 가동 중지 시간에 대해 관찰된 정보와 제안되는 문제 해결 단계를 확인할 수 있습니다. 

![진단 보고서](./media/app-service-diagnostics/DiagnosticReport5.png)


## <a name="investigating-application-code-issues"></a>애플리케이션 코드 문제 조사

많은 앱 문제가 애플리케이션 코드의 문제와 관련되어 있으므로 App Service 진단은 [Application Insights](https://azure.microsoft.com/services/application-insights/)와 통합되어 예외 및 종속성 문제를 강조 표시하고, 선택된 작동 중단 시간과의 상관 관계를 표시합니다. Application Insights는 사용하도록 별도로 설정해야 합니다. 

Application Insights 예외 및 종속성을 보려면 **웹앱 작동 중단** 또는 **느린 웹앱** 타일 바로 가기를 선택합니다. 

![Application insights](./media/app-service-diagnostics/AppInsights6.png)

