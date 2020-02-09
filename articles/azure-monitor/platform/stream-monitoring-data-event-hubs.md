---
title: Azure 모니터링 데이터를 이벤트 허브로 스트림
description: Azure 모니터링 데이터를 이벤트 허브로 스트리밍하 여 파트너 SIEM 또는 분석 도구로 데이터를 가져오는 방법에 대해 알아봅니다.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 6a474bdceffa07b18530250a02a9ef94159a8e35
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750328"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>Azure 모니터링 데이터를 이벤트 허브로 스트리밍
Azure Monitor는 Azure, 다른 클라우드 및 온-프레미스에서 응용 프로그램 및 서비스에 대 한 전체 스택 모니터링 솔루션을 제공 합니다. Azure Monitor 사용 하 여 해당 데이터를 분석 하 고 다양 한 모니터링 시나리오에 활용 하는 것 외에도 사용자 환경의 다른 모니터링 도구에 해당 데이터를 전송 해야 할 수 있습니다. 대부분의 경우 모니터링 데이터를 외부 도구로 스트리밍하는 가장 효과적인 방법은 [Azure Event Hubs](/azure/event-hubs/)를 사용 하는 것입니다. 이 문서에서는 다양 한 원본에서 이벤트 허브로 모니터링 데이터를 스트리밍하는 방법에 대 한 간략 한 설명을 제공 하 고 자세한 지침에 대 한 링크를 제공 합니다.


## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기

모든 데이터 원본에 대 한 스트리밍을 구성 하기 전에 [Event Hubs 네임 스페이스 및 이벤트 허브를 만들어야](../../event-hubs/event-hubs-create.md)합니다. 이 네임스페이스 및 이벤트 허브는 모든 모니터링 데이터에 대한 대상입니다. Event Hubs 네임스페이스는 같은 액세스 정책을 공유하는 Event Hubs의 논리적 그룹으로, 스토리지 계정은 해당 스토리지 계정 내에서 개별 Blob을 갖기 쉽습니다. 모니터링 데이터를 스트리밍하는 데 사용 하는 event hubs 네임 스페이스 및 event hubs에 대 한 다음 세부 정보를 고려 하세요.

* 처리량 단위 수를 사용하여 이벤트 허브에 대한 처리량 비율을 높일 수 있습니다. 일반적으로 처리량 단위는 한 개만 필요 합니다. 로그 사용량이 증가 함에 따라 규모를 확장 해야 하는 경우 네임 스페이스의 처리량 단위 수를 수동으로 늘리거나 자동 인플레이션을 사용 하도록 설정할 수 있습니다.
* 파티션 수를 사용하면 많은 소비자 간에 소비량의 균형을 이룰 수 있습니다. 단일 파티션은 초당 최대 20MBps 또는 약 2만 메시지를 지원할 수 있습니다. 데이터를 소비하는 도구에 따라, 여러 파티션을 통한 소비를 지원할 수도있 고 지원하지 않을 수도 있습니다. 설정할 파티션 수를 확실 하 게 알지 못하는 경우 4 개의 파티션이 시작 하는 것이 합리적입니다.
* 이벤트 허브에 대 한 메시지 보존 기간을 7 일 이상으로 설정 합니다. 소비 도구가 하루 이상 중단 되는 경우이 도구를 사용 하면 최대 7 일 동안 이벤트를 중단 했던 위치를 선택할 수 있습니다.
* 이벤트 허브에 대 한 기본 소비자 그룹을 사용 해야 합니다. 2개의 다른 도구에서 동일한 이벤트 허브의 동일한 데이터를 소비하려는 경우가 아니면, 다른 소비자 그룹을 만들거나 별도의 소비자 그룹을 사용할 필요가 없습니다.
* Azure 활동 로그의 경우 Event Hubs 네임 스페이스를 선택 하 고, Azure Monitor는 해당 네임 스페이스 내에서 insights- _로그-운영 로그_라는 이벤트 허브를 만듭니다. 다른 로그 유형의 경우 기존 이벤트 허브를 선택 하거나 로그 범주별로 이벤트 허브를 만들 Azure Monitor 있습니다.
* 아웃 바운드 포트 5671 및 5672은 일반적으로 이벤트 허브에서 데이터를 소비 하는 컴퓨터 또는 VNET에서 열어야 합니다.

## <a name="monitoring-data-available"></a>사용 가능한 모니터링 데이터
[Azure Monitor에 대 한 모니터링 데이터의 원본은](data-sources.md) Azure 응용 프로그램의 다양 한 데이터 계층과 각 응용 프로그램에 사용할 수 있는 모니터링 데이터의 종류를 설명 합니다. 다음 표에는 이러한 각 계층과 해당 데이터를 이벤트 허브로 스트리밍할 수 있는 방법에 대 한 설명이 나와 있습니다. 추가 정보를 보려면 제공 된 링크를 따르세요.

| 계층 | 데이터 | 방법 |
|:---|:---|:---|
| [Azure 테넌트](data-sources.md#azure-tenant) | 감사 로그 Azure Active Directory | AAD 테넌트에서 테넌트 진단 설정을 구성 합니다. 자세한 내용은 [자습서: Azure 이벤트 허브에 로그 Azure Active Directory 스트리밍을](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) 참조 하세요. |
| [Azure 구독](data-sources.md#azure-subscription) | Azure 동작 로그 | 활동 로그 이벤트를 Event Hubs으로 내보낼 로그 프로필을 만듭니다.  자세한 내용은 [Azure 활동 로그를 저장소 또는 azure Event Hubs로 내보내기를](activity-log-export.md) 참조 하세요. |
| [Azure 리소스](data-sources.md#azure-resources) | 플랫폼 메트릭<br> 리소스 로그 |두 가지 유형의 데이터는 리소스 진단 설정을 사용하여 이벤트 허브로 전송됩니다. 자세한 내용은 [이벤트 허브에 대 한 Azure 리소스 로그 스트리밍을](resource-logs-stream-event-hubs.md) 참조 하세요. |
| [운영 체제 (게스트)](data-sources.md#operating-system-guest) | Azure 가상 머신 | Azure에서 Windows 및 Linux 가상 머신에 [Azure 진단 확장](diagnostics-extension-overview.md) 을 설치 합니다. Windows Vm에 대 한 자세한 내용은 [Event Hubs를 사용 하 여 실행 부하 과다 경로의 데이터 스트리밍](diagnostics-extension-stream-event-hubs.md) 및 Linux 진단 확장을 사용 하 여 linux vm에 대 한 자세한 내용은 [메트릭 및 로그를 모니터링 하](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) 는 방법을 Azure 진단. |
| [응용 프로그램 코드](data-sources.md#application-code) | Application Insights | Application Insights는 이벤트 허브로 데이터를 스트리밍하는 직접 메서드를 제공 하지 않습니다. 논리 앱을 사용 하 [여 수동 스트리밍](#manual-streaming-with-logic-app)에 설명 된 대로 Application Insights 데이터를 저장소 계정으로 [연속 내보내고](../../azure-monitor/app/export-telemetry.md) 논리 앱을 사용 하 여 이벤트 허브로 데이터를 보낼 수 있습니다. |

## <a name="manual-streaming-with-logic-app"></a>논리 앱을 사용 하 여 수동 스트리밍
이벤트 허브로 직접 스트리밍할 수 없는 데이터의 경우 Azure storage에 작성 한 다음 [blob 저장소에서 데이터를 가져와서](../../connectors/connectors-create-api-azureblobstorage.md#add-action) [이벤트 허브에 메시지로 푸시하](../../connectors/connectors-create-api-azure-event-hubs.md#add-action)는 시간 트리거된 논리 앱을 사용할 수 있습니다. 


## <a name="partner-tools-with-azure-monitor-integration"></a>Azure Monitor 통합이 포함 된 파트너 도구

Azure Monitor를 사용 하 여 모니터링 데이터를 이벤트 허브로 라우팅하는 것은 외부 SIEM 및 모니터링 도구와 쉽게 통합할 수 있습니다. Azure Monitor 통합 도구에 대 한 예는 다음과 같습니다.

| 도구 | Azure에서 호스트 | Description |
|:---|:---| :---|
|  IBM QRadar | 아닙니다. | Microsoft Azure DSM 및 Microsoft Azure 이벤트 허브 프로토콜은 [IBM 지원 웹 사이트](https://www.ibm.com/support)에서 다운로드할 수 있습니다. [Qradar DSM 구성](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0)에서 Azure와의 통합에 대해 자세히 알아볼 수 있습니다. |
| Splunk | 아닙니다. | [Splunk에 대 한 Azure Monitor 추가 기능은](https://splunkbase.splunk.com/app/3534/) 기능은 splunkbase에서 사용할 수 있는 오픈 소스 프로젝트입니다. 설명서는 [Splunk의 Azure Monitor 추가](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk)기능에서 사용할 수 있습니다.<br><br> Splunk 인스턴스에 추가 기능을 설치할 수 없는 경우 예를 들어, 프록시를 사용 하거나 Splunk 클라우드에서 실행 하는 경우 Event hub의 새 메시지에 의해 트리거되는 [Splunk 용 Azure Function](https://github.com/Microsoft/AzureFunctionforSplunkVS)을 사용 하 여 이러한 이벤트를 Splunk HTTP 이벤트 수집기로 전달할 수 있습니다. |
| sumologic | 아닙니다. | Event hub의 데이터를 사용 하는 SumoLogic를 설정 하는 방법에 대 한 지침은 [Event hub의 Azure 감사 앱에 대 한 로그 수집](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)에서 사용할 수 있습니다. |
| ArcSight | 아닙니다. | ArcSight Azure 이벤트 허브 스마트 커넥터는 [arcsight 스마트 커넥터 컬렉션](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852)의 일부로 사용할 수 있습니다. |
| Syslog 서버 | 아닙니다. | Azure Monitor 데이터를 syslog 서버로 직접 스트리밍하려면 [Azure 함수를 기반으로](https://github.com/miguelangelopereira/azuremonitor2syslog/)하는 솔루션을 사용할 수 있습니다.
| LogRhythm | 아닙니다.| 이벤트 허브에서 로그를 수집 하도록 LogRhythm를 설정 하는 지침은 [여기](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/)에서 제공 됩니다. 
|Logz.io | 예 | 자세한 내용은 [Azure에서 실행 되는 Java 앱에 대 한 Logz.io를 사용 하 여 모니터링 및 로깅 시작](https://docs.microsoft.com/azure/java/java-get-started-with-logzio) 을 참조 하세요.


## <a name="next-steps"></a>다음 단계
* [저장소 계정에 활동 로그 보관](../../azure-monitor/platform/archive-activity-log.md)
* [Azure 활동 로그 개요 읽기](../../azure-monitor/platform/platform-logs-overview.md)
* [활동 로그 이벤트를 기반으로 경고 설정](../../azure-monitor/platform/alerts-log-webhook.md)


