---
title: Azure Application Insights에서 HockeyApp 데이터 탐색 | Microsoft Docs
description: Application Insights를 사용하여 Azure 앱의 사용 현황 및 성능을 분석합니다.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 97783cc6-67d6-465f-9926-cb9821f4176e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/30/2017
ms.author: mbullwin
ms.openlocfilehash: 79adfbfde25903bfe92c94507071c9d0fe303ef1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60898755"
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Application Insights에서 HockeyApp 데이터 탐색

> [!NOTE]
> HockeyApp을 더 이상 새 애플리케이션에 사용할 수 없습니다. 기존 HockeyApp 배포는 계속 작동합니다. Visual Studio App Center는 이제 새로운 모바일 앱을 모니터링하기 위해 Microsoft에서 권장하는 서비스입니다. [App Center 및 Application Insights를 사용하여 앱을 설정하는 방법에 대해 알아보세요](../../azure-monitor/learn/mobile-center-quickstart.md).

[HockeyApp](https://azure.microsoft.com/services/hockeyapp/)은 데스크톱 및 Mobile Apps를 실시간으로 모니터링하는 서비스입니다. HockeyApp에서 사용자 지정 및 추적 원격 분석을 전송하여(충돌 데이터를 가져오는 것 외에도) 사용량을 모니터링하고 진단에 도움을 줄 수 있습니다. 원격 분석의 이 스트림은 [Azure Application Insights](../../azure-monitor/app/analytics.md)의 강력한 [분석](../../azure-monitor/app/app-insights-overview.md) 기능을 사용하여 쿼리될 수 있습니다. 또한 [사용자 지정 및 추적 원격 분석을 내보낼](export-telemetry.md)수 있습니다. 이러한 기능을 활성화하기 위해 Application Insights에 HockeyApp 사용자 지정 데이터를 릴레이하는 브리지를 설정합니다.

## <a name="the-hockeyapp-bridge-app"></a>HockeyApp 브리지 앱
HockeyApp 브리지 앱은 분석 및 연속 내보내기 기능을 통해 Application Insights에서 HockeyApp 사용자 지정 및 추적 원격 분석에 액세스할 수 있는 핵심 기능입니다. HockeyApp 브리지 앱을 만든 후에 HockeyApp에서 수집한 사용자 지정 및 추적 이벤트를 이러한 기능에서 액세스할 수 있습니다. 이러한 브리지 앱 중 하나를 설정하는 방법을 살펴보겠습니다.

HockeyApp에서 계정 설정, [API 토큰](https://rink.hockeyapp.net/manage/auth_tokens)을 엽니다. 새 토큰을 만들거나 기존 토큰을 다시 사용합니다. 필요한 최소 권한은 "읽기 전용"입니다. API 토큰의 복사본을 사용합니다.

![HockeyApp API 토큰 가져오기](./media/hockeyapp-bridge-app/01.png)

Microsoft Azure Portal을 열고 [Application Insights 리소스를 만듭니다](../../azure-monitor/app/create-new-resource.md ). 애플리케이션 유형을 “HockeyApp 브리지 애플리케이션”으로 설정합니다.

![새 Application Insights 리소스](./media/hockeyapp-bridge-app/02.png)

이름을 설정하지 않아도 됩니다. - HockeyApp 이름에서 자동으로 설정됩니다.

HockeyApp 브리지 필드가 나타납니다. 

![브리지 필드 입력](./media/hockeyapp-bridge-app/03.png)

앞에서 설명한 HockeyApp 토큰을 입력합니다. 이 작업에서는 모든 HockeyApp 애플리케이션으로 “HockeyApp 애플리케이션” 드롭다운 메뉴를 채웁니다. 사용하려는 필드를 선택하고 필드의 나머지 부분을 완료합니다. 

새 리소스를 엽니다. 

데이터가 흐르기 시작하는 데 약간의 시간이 걸립니다.

![데이터를 기다리는 Application Insights 리소스](./media/hockeyapp-bridge-app/04.png)

이것으로 끝입니다. 지금부터 HockeyApp 계측 앱에서 수집된 사용자 지정 및 추적 데이터는 이제 Application Insights의 분석 및 연속 내보내기 기능에서 사용할 수 있습니다.

이제 사용할 수 있는 이러한 기능을 각각 간단히 검토하겠습니다.

## <a name="analytics"></a>분석
Analytics는 임시 쿼리를 진단 및 분석을 신속 하 게 근본 원인 및 패턴을 검색할 수 있도록 데이터를 위한 강력한 도구입니다.

![분석](./media/hockeyapp-bridge-app/05.png)

* [분석에 대해 자세히 알아보기](../../azure-monitor/log-query/get-started-portal.md)

## <a name="continuous-export"></a>연속 내보내기
연속 내보내기를 사용하면 Azure Blob Storage 컨테이너에 데이터를 내보낼 수 있습니다. 현재 Application Insights에서 제공하는 보존 기간보다 오래 데이터를 유지해야 하는 경우 매우 유용합니다. Blob Storage에 데이터를 유지하고 SQL Database 또는 기본 데이터 웨어하우스 솔루션으로 처리할 수 있습니다.

[연속 내보내기에 대해 자세히 알아보기](export-telemetry.md)

## <a name="next-steps"></a>다음 단계
* [데이터에 분석 적용](../../azure-monitor/log-query/get-started-portal.md)

