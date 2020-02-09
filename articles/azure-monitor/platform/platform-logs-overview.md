---
title: Azure platform logs 개요 | Microsoft Docs
description: Azure 리소스 작업에 대 한 풍부 하 고 빈번한 데이터를 제공 하는 Azure Monitor 로그의 개요입니다.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 5f02368bfb0c084691376300980d4cdee0d9b3be
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530887"
---
# <a name="overview-of-azure-platform-logs"></a>Azure platform 로그 개요
플랫폼 로그는 Azure 리소스 및 해당 리소스가 종속 된 Azure 플랫폼에 대 한 자세한 진단 및 감사 정보를 제공 합니다. 보존 하기 위해 특정 플랫폼 로그를 하나 이상의 대상으로 전달 하도록 구성 해야 하는 경우에도 자동으로 생성 됩니다. 이 문서에서는 제공 하는 정보와 수집 및 분석을 위해 구성 하는 방법을 비롯 하 여 플랫폼 로그의 개요를 제공 합니다.

## <a name="types-of-platform-logs"></a>플랫폼 로그 유형
다음 표에는 다양 한 Azure 계층에서 사용할 수 있는 특정 플랫폼 로그가 나열 되어 있습니다.

| 로그 | 계층 | Description |
|:---|:---|:---|
| 리소스 로그 | Azure 리소스 | Azure 리소스 ( *데이터 평면*) 내에서 수행 된 작업에 대 한 통찰력을 제공 합니다. 예를 들어 Key Vault에서 비밀을 가져오거나 데이터베이스를 요청 하는 등의 작업을 수행할 수 있습니다. 리소스 로그의 콘텐츠는 Azure 서비스 및 리소스 유형에 따라 달라 집니다.<br><br>*리소스 로그는 이전에 진단 로그 라고 합니다.*  |
| 활동 로그 | Azure 구독 | Service Health 이벤트에 대 한 업데이트 외에도 외부 (*관리 평면*)에서 구독의 각 Azure 리소스에 대 한 작업에 대 한 통찰력을 제공 합니다. 활동 로그를 사용 하 여 구독의 리소스에 대해 수행 되는 쓰기 작업 (PUT, POST, DELETE)에 대 한 _내용_, 대상 및 _시기_ _를 결정_합니다. 또한 작업 및 기타 관련 속성의 상태도 이해할 수 있습니다.  각 Azure 구독에 대 한 단일 활동 로그가 있습니다. |
| Azure Active Directory 로그 | Azure 테넌트 |  특정 테넌트에 대해 Azure Active Directory의 로그인 활동 기록과 변경 내용의 감사 내역을 포함 합니다. Azure Active Directory 로그에 대 한 전체 설명은 [Azure Active Directory 보고서 란?](../../active-directory/reports-monitoring/overview-reports.md) 을 참조 하세요.   |

> [!NOTE]
> Azure 활동 로그는 주로 Azure Resource Manager에서 발생하는 활동을 대상으로 합니다. 클래식/RDFE 모델을 사용하여 리소스를 추적하지 않습니다. 일부 클래식 리소스 유형(예: Microsoft.ClassicCompute)에는 Azure Resource Manager의 프록시 리소스 공급자가 있습니다. 이러한 프록시 리소스 공급자를 사용하는 Azure Resource Manager를 통해 클래식 리소스 유형과 상호 작용하면 작업이 활동 로그에 표시됩니다. Azure Resource Manager 프록시 외부의 클래식 리소스 유형과 상호 작용할 경우 사용자 작업은 작업 로그에만 기록됩니다. 작업 로그는 포털의 별도 섹션에서 탐색할 수 있습니다.

![플랫폼 로그 개요](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>플랫폼 로그 보기
다양 한 Azure 플랫폼 로그를 보고 분석 하는 다양 한 옵션이 있습니다.

- Azure Portal에서 활동 로그를 보고 PowerShell 및 CLI에서 이벤트에 액세스 합니다. 자세한 내용은 [Azure 활동 로그 이벤트 보기 및 검색](activity-log-view.md) 을 참조 하세요. 
- Azure Portal에서 Azure Active Directory 보안 및 작업 보고서를 봅니다. [Azure Active Directory 보고서 란?을](../../active-directory/reports-monitoring/overview-reports.md) 참조 하세요.  참조하세요.
- 리소스 로그는 지원 되는 Azure 리소스에 의해 자동으로 생성 되지만 [대상](#destinations)에 전송 하지 않는 한 볼 수 없습니다. 

## <a name="destinations"></a>Destinations
모니터링 요구 사항에 따라 다음 표에 있는 하나 이상의 대상에 플랫폼 로그를 보낼 수 있습니다. [진단 설정을 만들어](diagnostic-settings.md)플랫폼 로그의 대상을 구성 합니다.

| 대상 | 시나리오 | 참조 |
|:---|:---|:---|:---|
| Log Analytics 작업 영역 | 다른 모니터링 데이터를 사용 하 여 로그를 분석 하 고 로그 쿼리 및 경고와 같은 Azure Monitor 기능을 활용 합니다. | [활동 로그 및 리소스 로그](resource-logs-collect-workspace.md)<br>[Azure 활동 디렉터리 로그](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | 감사, 정적 분석 또는 백업용 로그를 보관 합니다. |[활동 로그 및 리소스 로그](archive-diagnostic-logs.md)<br>[Azure 활동 디렉터리 로그](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| 이벤트 허브 | 로그를 타사 로깅 및 원격 분석 시스템으로 스트리밍합니다.  |[활동 로그 및 리소스 로그](resource-logs-stream-event-hubs.md)<br>[Azure 활동 디렉터리 로그](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>다음 단계

* [다른 범주에 대 한 활동 로그 스키마 보기](activity-log-schema.md)
* [다른 Azure 서비스에 대 한 리소스 로그 스키마 보기](diagnostic-logs-schema.md)
