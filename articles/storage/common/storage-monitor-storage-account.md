---
title: Azure Storage 계정을 모니터링하는 방법 | Microsoft Docs
description: Azure 포털을 사용하여 Azure에서 저장소 계정을 모니터링하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/31/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 64cfac0d689df88c4d432e772bcd0a0cc7ab4ade
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478198"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Azure 포털에서 저장소 계정 모니터링

[Azure Storage 분석](storage-analytics.md)은 모든 Storage 서비스에 대한 메트릭과 Blob, 큐 및 테이블에 대한 로그를 제공합니다. [Azure Portal](https://portal.azure.com)을 사용하여 계정에 기록되는 메트릭 및 로그를 구성하고, 메트릭 데이터를 시각적으로 표현하는 차트를 구성할 수 있습니다.

> [!NOTE]
> Azure Portal에서 모니터링 데이터를 검사하는 데 관련된 비용이 있습니다. 자세한 정보는 [저장소 분석](storage-analytics.md)을 참조하세요.
>
> Azure Files는 현재 저장소 분석 메트릭을 지원하지만 아직 로깅을 지원하지 않습니다.
>
> 스토리지 분석 및 기타 도구를 사용하여 Azure Storage 관련 문제를 식별, 진단 및 해결하는 방법에 대한 자세한 지침은 [Microsoft Azure Storage 모니터링, 진단 및 문제 해결](storage-monitoring-diagnosing-troubleshooting.md)을 참조하세요.
>

## <a name="configure-monitoring-for-a-storage-account"></a>저장소 계정에 대한 모니터링 구성

1. [Azure Portal](https://portal.azure.com)에서 **Storage 계정**, Storage 계정 이름을 차례로 선택하여 계정 대시보드를 엽니다.
1. 메뉴 블레이드의 **모니터링** 섹션에서 **진단**을 선택합니다.

    ![모니터링 옵션](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. 모니터링하려는 각 **서비스**에 대한 메트릭 데이터 **유형**과 데이터에 대한 **보존 정책**을 선택합니다. 또한 **상태**를 **해제**(Off)로 설정하여 모니터링을 사용하지 않도록 설정할 수 있습니다.

    ![모니터링 옵션](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   데이터 보존 정책을 설정하려면 1-365일 중에서 **보존(일)** 슬라이더를 이동하거나 데이터 보존 기간(일)을 입력합니다. 새 저장소 계정의 기본값은 7일입니다. 보존 정책을 설정하지 않으려면 0을 입력합니다. 보존 정책이 없는 경우 언제든 모니터링 데이터를 삭제할 수 있습니다.

   > [!WARNING]
   > 메트릭 데이터를 수동으로 삭제하는 경우 요금이 부과되지만, 오래된 분석 데이터(보존 정책보다 오래된 데이터)는 무료로 시스템에서 삭제됩니다. 따라서 계정에 대한 저장소 분석 데이터를 보존할 기간에 따라 보존 정책을 설정하는 것이 좋습니다. 참조 [저장소 메트릭에 대 한 청구](storage-analytics-metrics.md#billing-on-storage-metrics) 자세한 내용은 합니다.
   >

1. 모니터링 구성을 완료하면 **저장**을 선택합니다.

기본 메트릭 집합은 개별 서비스 블레이드(Blob, 큐, 테이블 및 파일)뿐만 아니라 저장소 계정 블레이드의 차트에도 표시됩니다. 서비스에 대한 메트릭을 사용하도록 설정하면 차트에 데이터를 표시하는 데 최대 1시간이 걸릴 수 있습니다. 메트릭 차트에서 **편집**을 선택하여 차트에 표시되는 메트릭을 구성할 수 있습니다.

**상태**를 **해제**로 설정하여 메트릭 수집 및 로깅을 사용하지 않도록 설정할 수 있습니다.

> [!NOTE]
> Azure Storage는 [테이블 스토리지](storage-introduction.md#table-storage)를 사용하여 스토리지 계정에 대한 메트릭을 저장하고, 계정의 테이블에 메트릭을 저장합니다. 자세한 내용은 다음을 참조하세요. [메트릭 저장 방법](storage-analytics-metrics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>메트릭 차트 사용자 지정

다음 절차를 사용하여 메트릭 차트에 표시할 저장소 메트릭을 선택합니다.

1. Azure Portal에서 저장소 메트릭 차트를 표시하여 시작합니다. 차트는 **저장소 계정 블레이드** 및 개별 서비스(Blob, 큐, 테이블, 파일)의 **메트릭** 블레이드에서 찾을 수 있습니다.

   여기서는 **저장소 계정 블레이드**에 나타나는 다음과 같은 차트를 사용합니다.

   ![Azure Portal에서 차트 선택](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. 차트를 편집하려면 차트 내의 아무 곳이나 클릭합니다.

1. 다음으로, 차트에 표시할 메트릭의 **시간 범위**와 표시하려는 메트릭의 **서비스**(Blob, 큐, 테이블, 파일)를 선택합니다. 여기서는 다음과 같이 Blob 서비스에 대해 지난 주의 메트릭을 표시하도록 선택했습니다.

   ![차트 편집 블레이드의 시간 범위 및 서비스 선택](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. 차트에 표시하려는 개별 **메트릭**을 선택한 다음 **확인**을 클릭합니다.

   ![차트 편집 블레이드의 개별 메트릭 선택](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

차트 설정은 저장소 계정에서 모니터링 데이터의 컬렉션, 집계 또는 저장소에 영향을 주지 않습니다.

### <a name="metrics-availability-in-charts"></a>차트의 메트릭 가용성

사용 가능한 메트릭 목록은 드롭다운에서 선택한 서비스와 편집 중인 차트의 단위 유형에 따라 달라집니다. 예를 들어 단위를 백분율로 표시하는 차트를 편집하는 경우에만 *PercentNetworkError* 및 *PercentThrottlingError*와 같은 백분율 메트릭을 선택할 수 있습니다.

![Azure Portal에서 오류 백분율 차트 요청](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>메트릭 해상도

**진단**에서 선택한 메트릭에 따라 계정에서 사용할 수 있는 메트릭의 해상도가 결정됩니다.

* **집계** 모니터링은 수신/송신, 가용성, 대기 시간 및 성공 비율과 같은 메트릭을 제공합니다. 이러한 메트릭은 Blob, 테이블, 파일 및 큐 서비스에서 집계됩니다.
* **API당**은 서비스 수준 집계 외에도 개별 저장소 작업에 사용할 수 있는 메트릭과 함께 보다 세밀한 해상도를 제공합니다.

## <a name="configure-metrics-alerts"></a>메트릭 경고 구성

저장소 리소스 메트릭의 임계값에 도달하면 사용자에게 알리도록 경고를 만들 수 있습니다.

1. **경고 규칙 블레이드**를 열려면 **메뉴 블레이드**의 **모니터링** 섹션으로 스크롤하여 **경고(클래식)** 를 선택합니다.
2. **메트릭 경고 추가(클래식)** 을 선택하여 **경고 규칙 추가** 블레이드를 엽니다.
3. 새 경고 규칙에 대해 **이름** 및 **설명**을 입력합니다.
4. 경고, 경고 **조건** 및 **임계값**을 추가하려는 **메트릭**을 선택합니다. 임계값 단위 유형은 선택한 메트릭에 따라 달라집니다. 예를 들어 "개수"는 *ContainerCount*의 단위 유형이지만, "백분율"은 *PercentNetworkError* 메트릭의 단위 유형입니다.
5. **기간**을 선택합니다. 해당 기간 내에 임계값에 도달하거나 초과하는 메트릭은 경고를 트리거합니다.
6. (선택 사항) **전자 메일** 및 **웹후크** 알림을 구성합니다. 웹후크에 대한 자세한 내용은 [Azure 메트릭 경고에 대한 웹후크 구성](../../azure-monitor/platform/alerts-webhooks.md)을 참조하세요. 전자 메일 또는 웹후크 알림을 구성하지 않으면 경고가 Azure Portal에서만 표시됩니다.

![Azure Portal에서 '경고 규칙 추가' 블레이드 추가](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>포털 대시보드에 메트릭 차트 추가

스토리지 계정에 대한 Azure Storage 메트릭 차트를 포털 대시보드에 추가할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 대시보드를 보면서 **대시보드 편집**을 클릭합니다.
1. **타일 갤러리**에서 **타일 찾기 기준** > **유형**을 차례로 선택합니다.
1. **유형** > **저장소 계정**을 차례로 선택합니다.
1. **리소스**에서 대시보드에 추가하려는 메트릭의 저장소 계정을 선택합니다.
1. **범주** > **모니터링**을 차례로 선택합니다.
1. 표시하려는 메트릭에 대한 차트 타일을 대시보드로 끌어서 놓습니다. 대시보드에 표시하려는 모든 메트릭에 대해 반복합니다. 다음 이미지에서는 한 예로 "Blob - 총 요청 수" 차트를 강조 표시했지만 모든 차트를 대시보드에 배치할 수 있습니다.

   ![Azure Portal의 타일 갤러리](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. 차트 추가를 완료했으면 대시보드 위쪽의 **사용자 지정 완료**를 선택합니다.

대시보드에 차트를 추가하면 메트릭 차트 사용자 지정에서 설명한 대로 차트를 추가로 사용자 지정할 수 있습니다.

## <a name="configure-logging"></a>로깅 구성

Azure Storage에서 Blob, 테이블 및 큐 서비스에 대한 읽기, 쓰기 및 삭제 요청에 대한 진단 로그를 저장하도록 지시할 수 있습니다. 설정한 데이터 보존 정책도 이러한 로그에 적용됩니다.

> [!NOTE]
> Azure Files는 현재 저장소 분석 메트릭을 지원하지만 아직 로깅을 지원하지 않습니다.
>

1. [Azure Portal](https://portal.azure.com)에서 **Storage 계정**, Storage 계정 이름을 차례로 선택하여 Storage 계정 블레이드를 엽니다.
1. 메뉴 블레이드의 **모니터링** 섹션에서 **진단**을 선택합니다.

    ![Azure Portal에서 모니터링 아래의 진단 메뉴 항목](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. **상태**가 **사용**(On)으로 설정되어 있는지 확인하고, 로깅을 사용하도록 설정하려는 **서비스**를 선택합니다.

    ![Azure Portal에서 로깅 구성](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. **저장**을 클릭합니다.

진단 로그는 스토리지 계정의 이름이 *$logs*인 Blob 컨테이너에 저장됩니다. [Microsoft 스토리지 탐색기](https://storageexplorer.com)와 같은 스토리지 탐색기를 사용하거나 Storage 클라이언트 라이브러리 또는 PowerShell을 프로그래밍 방식으로 사용하여 로그 데이터를 볼 수 있습니다.

$Logs 컨테이너 액세스에 대 한 자세한 내용은 [저장소 분석 로깅](storage-analytics-logging.md)합니다.

## <a name="next-steps"></a>다음 단계

* 저장소 분석의 [메트릭, 로깅 및 청구](storage-analytics.md)에 대해 자세히 알아봅니다.
