---
title: Azure App Service에서 리소스 이벤트 가져오기
description: App Service 앱에서 활동 로그 및 Event Grid를 통해 리소스 이벤트를 가져오는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: c5c84891187c540c0b24162cf5c8c7f96e9e731a
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962301"
---
# <a name="get-resource-events-in-azure-app-service"></a>Azure App Service에서 리소스 이벤트 가져오기

Azure App Service는 리소스의 상태와 상태를 모니터링하는 기본 제공 도구를 제공합니다. 리소스 이벤트를 통해 기본 웹앱 리소스에 대한 변경 내용을 이해하고 필요에 따라 조치를 취할 수 있습니다. 이벤트 예로는 인스턴스 크기 조정, 애플리케이션 설정 업데이트, 웹앱 다시 시작 등이 있습니다. 이 문서에서는 [Azure 활동 로그](../azure-monitor/platform/activity-log.md#view-the-activity-log)를 보고 [Event Grid](../event-grid/index.yml)를 활성화하여 App Service 웹앱과 관련된 리소스 이벤트를 모니터링하는 방법에 대해 알아봅니다.

> [!NOTE]
> Event Grid와 App Service의 통합은 **미리 보기**에 있습니다. [자세한 내용은 알림을 확인하세요.](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>Azure 활동 로그 보기
Azure 활동 로그에는 구독의 리소스에서 수행된 작업에서 내보내는 리소스 이벤트가 포함됩니다. Azure Portal 및 Azure Resource Manager 템플릿의 사용자 작업은 모두 활동 로그에 의해 캡처된 이벤트에 기여합니다. 

Azure 활동 로그는 다음과 같은 App Service 세부 정보를 기록합니다.
- 리소스에서 수행된 작업(예: App Service 계획)
- 작업을 시작한 사람
- 작업이 발생한 시간
- 작업의 상태
- 작업을 조사하는 데 도움이 되는 속성 값

### <a name="what-can-you-do-with-azure-activity-logs"></a>Azure 활동 로그로 무엇을 할 수 있나요?

Azure 활동 로그는 Azure Portal, PowerShell, REST API 또는 CLI를 사용하여 쿼리할 수 있습니다. 스토리지 계정, Event Hub 및 Log Analytics로 로그를 보낼 수 있습니다. Power BI에서 분석하거나 경고를 만들어 리소스 이벤트에 대한 업데이트를 유지할 수도 있습니다.

[Azure 활동 로그 이벤트를 보고 검색합니다.](../azure-monitor/platform/activity-log.md#view-the-activity-log)

## <a name="ship-activity-logs-to-event-grid"></a>활동 로그를 Event Hubs에 제공

활동 로그는 사용자 기반이지만 사용자 작업과 자동화된 이벤트를 모두 기록하는 새 [Event Grid](../event-grid/index.yml)와 App Service(미리 보기)가 통합되었습니다. Event Grid를 사용하면 해당 이벤트에 반응하도록 처리기를 구성할 수 있습니다. 예를 들어 Event Grid를 사용하여, 새 사진이 Blob Storage 컨테이너에 추가될 때마다 이미지 분석을 실행하도록 서버를 사용하지 않는 함수를 즉시 트리거합니다.

또는 Event Grid와 Logic Apps를 사용하여, 코드를 작성할 필요 없이 어디서든 데이터를 처리할 수 있습니다. Event Grid는 데이터 원본과 이벤트 처리기를 연결합니다. 예를 들어 Event Grid를 사용하여, 새 사진이 Blob Storage 컨테이너에 추가될 때마다 이미지 분석을 실행하도록 서버를 사용하지 않는 함수를 즉시 트리거합니다.

[Azure App Service 이벤트의 속성 및 스키마를 봅니다.](../event-grid/event-schema-app-service.md)

## <a name="next-steps"></a><a name="nextsteps"></a> 다음 단계
* [Azure Monitor를 사용하여 로그 쿼리](../azure-monitor/log-query/log-query-overview.md)
* [Azure App Service에서 모니터링하는 방법](web-sites-monitor.md)
* [Visual Studio에서 Azure App Service 문제 해결](troubleshoot-dotnet-visual-studio.md)
* [HDInsight에서 앱 로그 분석](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)