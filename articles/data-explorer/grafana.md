---
title: Grafana를 사용 하 여 Azure 데이터 탐색기에서 데이터 시각화
description: 이 문서에서는 Grafana에 대한 데이터 원본으로 Azure 데이터 탐색기를 설정 하 고 샘플 클러스터에서 데이터를 시각화 하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: a1c52007ea86ca0812c4a73a92ce81db6ddadc7b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037981"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Grafana의 Azure Data Explorer에서 데이터 시각화

Grafana는 데이터를 쿼리하고 시각화한 다음, 시각화에 따라 대시보드를 만들고 공유할 수 있도록 해주는 분석 플랫폼입니다. Grafana는 Azure Data Explorer *플러그 인*을 제공하여 Azure Data Explorer에서 데이터에 연결하고 데이터를 시각화할 수 있습니다. 이 문서에서는 Grafana에 대한 데이터 원본으로 Azure 데이터 탐색기를 설정 하 고 샘플 클러스터에서 데이터를 시각화 하는 방법을 알아봅니다.

다음 비디오를 사용 하 여 Grafana의 Azure 데이터 탐색기 플러그 인을 사용 하 고, Grafana에 대한 데이터 원본으로 Azure 데이터 탐색기를 설정한 다음, 데이터를 시각화 하는 방법을 알아보세요. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

또는 아래 문서에 설명 된 대로 [데이터 원본을 구성](#configure-the-data-source) 하 고 [데이터를 시각화할](#visualize-data) 수 있습니다.

## <a name="prerequisites"></a>선행 조건

이 문서를 완료 하려면 다음이 필요 합니다.

* 운영 체제용 [Grafana 버전 5.3.0 이상](https://docs.grafana.org/installation/)

* Grafana용 [Azure Data Explorer 플러그 인](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation)

* StormEvents 샘플 데이터가 포함된 클러스터입니다. 자세한 내용은 [빠른 시작: Azure 데이터 탐색기 클러스터 및 데이터베이스 만들기](create-cluster-database-portal.md) 및 [Azure 데이터 탐색기로 샘플 데이터 수집](ingest-sample-data.md)을 참조하세요.

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>속성 지정 및 연결 테스트

서비스 주체를 *뷰어* 역할에 할당한 상태에서, 이제 Grafana 인스턴스에서 속성을 지정하고 Azure Data Explorer에 대한 연결을 테스트합니다.

1. Grafana의 왼쪽 메뉴에서 기어 아이콘을 선택한 다음, **데이터 원본**을 선택합니다.

    ![데이터 원본](media/grafana/data-sources.png)

1. **데이터 원본 추가**를 선택합니다.

1. **데이터 원본/새로 만들기** 페이지에서, 데이터 원본에 대한 이름을 입력한 다음, **Azure Data Explorer 데이터 원본** 형식을 선택합니다.

    ![연결 이름 및 형식](media/grafana/connection-name-type.png)

1. https://{ClusterName}.{Region}.kusto.windows.net 형식으로 클러스터 이름을 입력합니다. Azure Portal 또는 CLI에서 다른 값을 입력합니다. 매핑에 대한 다음 이미지는 아래 표를 참조하세요.

    ![연결 속성](media/grafana/connection-properties.png)

    | Grafana UI | Azure 포털 | Azure CLI |
    | --- | --- | --- |
    | 구독 ID | 구독 ID | SubscriptionId |
    | 테넌트 ID | 디렉터리 ID | 테넌트 |
    | 클라이언트 ID | 애플리케이션 UI | appId |
    | 클라이언트 암호 | 암호 | password |
    | | | |

1. **저장 & 테스트**를 선택합니다.

    테스트에 성공한 경우 다음 섹션으로 이동합니다. 모든 문제가 발생 한 경우 Grafana에서 지정한 값을 확인 하 고 이전 단계를 검토 합니다.

## <a name="visualize-data"></a>데이터 가상화

Grafana에 대한 데이터 원본으로 Azure Data Explorer 구성을 완료했다면, 이제 데이터를 시각화할 차례입니다. 여기에서는 기본적인 예제만 살펴보지만 다양한 작업을 수행할 수 있습니다. 샘플 데이터 세트에 대해 실행할 다른 쿼리의 예제로 [Azure Data Explorer에 대한 쿼리 작성](write-queries.md)을 볼 것을 권장합니다.

1. Grafana의 왼쪽 메뉴에서 더하기 아이콘을 선택한 다음, **대시 보드**를 선택합니다.

    ![대시보드 만들기](media/grafana/create-dashboard.png)

1. **추가** 탭 아래에 있는 **그래프**를 선택합니다.

    ![그래프 추가](media/grafana/add-graph.png)

1. 그래프 패널에서 **패널 제목**을 선택한 다음, **편집**을 선택합니다.

    ![패널 편집](media/grafana/edit-panel.png)

1. 패널의 맨 아래에서 **데이터 원본**을 선택한 다음, 사용자가 구성한 데이터 원본을 선택 합니다.

    ![데이터 원본 선택](media/grafana/select-data-source.png)

1. 쿼리 창에서 다음 쿼리를 복사한 다음, 선택 **실행**을 선택합니다. 쿼리는 샘플 데이터 세트에 대한 일별 이벤트 수를 버킷합니다.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![쿼리 실행](media/grafana/run-query.png)

1. 기본적으로 지난 6시간의 데이터가 범위로 지정되므로 그래프에는 어떤 결과도 표시되지 않습니다. 상단 메뉴에서 **지난 6시간**을 선택합니다.

    ![지난 6시간](media/grafana/last-six-hours.png)

1. StormEvents 샘플 데이터 세트에 포함된 연도인 2007년을 포함하는 사용자 지정 범위를 지정합니다. **적용**을 선택합니다.

    ![사용자 지정 날짜 범위](media/grafana/custom-date-range.png)

    이제 그래프에서는 일별로 버킷된 2007년의 데이터를 보여줍니다.

    ![완성된 그래프](media/grafana/finished-graph.png)

1. 상단 메뉴에서 저장 아이콘을 선택합니다. ![저장 아이콘](media/grafana/save-icon.png)가 필요합니다.

## <a name="create-alerts"></a>경고 만들기

1. 홈 대시보드에서 **경고** > **알림 채널** 을 선택 하 여 새 알림 채널을 만듭니다.

    ![알림 채널 만들기](media/grafana/create-notification-channel.png)

1. 새 **알림 채널**을 만든 다음 **저장**을 클릭 합니다.

    ![새 알림 채널 만들기](media/grafana/new-notification-channel-adx.png)

1. **대시보드에서**드롭다운에서 **편집** 을 선택 합니다.

    ![대시보드에서 편집 선택](media/grafana/edit-panel-4-alert.png)

1. 경고 벨 아이콘을 선택 하 여 **경고** 창을 엽니다. **경고 만들기**를 선택 합니다. **경고** 창에서 다음 속성을 완료 합니다.

    ![경고 속성](media/grafana/alert-properties.png)

1. **대시보드 저장** 아이콘을 선택 하 여 변경 내용을 저장 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기용 쿼리 작성](write-queries.md)
