---
title: Power BI를 사용 하 여 Azure Cognitive Search 로그 및 메트릭 시각화
description: Power BI를 사용 하 여 Azure Cognitive Search 로그 및 메트릭 시각화
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 2caf4c66cdbb11ed42c4131ef1584c372ec10332
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935112"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Power BI를 사용 하 여 Azure Cognitive Search 로그 및 메트릭 시각화
[Azure Cognitive Search](./search-what-is-azure-search.md) 를 사용 하면 Azure Storage 계정에서 검색 서비스에 대 한 작업 로그 및 서비스 메트릭을 저장할 수 있습니다. 이 페이지에서는 Power BI 템플릿 앱을 통해 해당 정보를 시각화할 수 있는 방법에 대 한 지침을 제공 합니다. 앱은 검색, 인덱싱, 작업 및 서비스 메트릭에 대 한 정보를 포함 하 여 검색 서비스에 대 한 자세한 정보를 제공 합니다.

Azure Cognitive Search: [Power BI Apps marketplace](https://appsource.microsoft.com/marketplace/apps)에서 **로그 및 메트릭 분석** Power BI 템플릿 앱을 찾을 수 있습니다.

## <a name="how-to-get-started-with-the-app"></a>앱을 시작 하는 방법

1. 검색 서비스에 대 한 메트릭 및 리소스 로깅 사용:

    1. 로그를 보관할 수 있는 기존 [Azure Storage 계정을](../storage/common/storage-account-create.md) 만들거나 확인 합니다.
    1. Azure Portal에서 Azure Cognitive Search 서비스로 이동 합니다.
    1. 왼쪽 열의 모니터링 섹션에서 **진단 설정** 을 선택 합니다.

        ![Azure Cognitive Search 서비스의 모니터링 섹션에서 진단 설정을 선택 하는 방법을 보여 주는 스크린샷](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. **+ 진단 설정 추가** 를 선택 합니다.
    1. **저장소 계정에 보관**을 선택 하 고, 저장소 계정 정보를 제공 하 고, **Operationlogs** 및 **allmetrics** 확인 합니다.

        ![진단 설정 페이지에서 메트릭 및 리소스 로깅을 선택 하는 방법을 보여 주는 스크린샷](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. **저장**을 선택합니다.

1. 로깅을 사용 하도록 설정한 후에는 검색 서비스를 사용 하 여 로그 및 메트릭 생성을 시작 합니다. 컨테이너가 이러한 로그를 사용 하 여 Blob storage에 표시 되기까지 최대 한 시간이 소요 됩니다. 검색 트래픽 로그에 대 한 pt1m **-operationlogs** 컨테이너와 메트릭에 대 한 통찰력- **메트릭-** 컨테이너가 표시 됩니다.

1. [Power BI Apps marketplace](https://appsource.microsoft.com/marketplace/apps) 에서 Azure Cognitive Search Power BI 앱을 찾아 새 작업 영역 또는 기존 작업 영역에 설치 합니다. 앱을 **Azure Cognitive Search: 로그 및 메트릭 분석**이라고 합니다.

1. 앱을 설치한 후 Power BI의 앱 목록에서 앱을 선택 합니다.

    ![앱 목록에서 선택할 Azure Cognitive Search 앱을 보여 주는 스크린샷](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. **연결** 을 선택 하 여 데이터를 연결 합니다.

    ![Azure Cognitive Search 앱에서 데이터에 연결 하는 방법을 보여 주는 스크린샷](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. 로그 및 메트릭이 포함 된 저장소 계정의 이름을 입력 합니다. 기본적으로 앱은 지난 10 일간의 데이터를 확인 하지만이 값은 **days** 매개 변수를 사용 하 여 변경할 수 있습니다.

    ![Azure Cognitive Search에 연결 페이지에서 저장소 계정 이름 및 쿼리할 일 수를 입력 하는 방법을 보여 주는 스크린샷](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. **키** 를 인증 방법으로 선택 하 고 저장소 계정 키를 제공 합니다. 개인 정보 수준으로 **개인** 을 선택 합니다. 로그인 을 클릭하여 로드 프로세스를 시작합니다.

    ![Azure Cognitive Search에 연결 페이지에서 인증 방법, 계정 키 및 개인 정보 수준을 입력 하는 방법을 보여 주는 스크린샷](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. 데이터를 새로 고칠 때까지 기다립니다. 보유 한 데이터 양에 따라 다소 시간이 걸릴 수 있습니다. 아래 표시기를 기준으로 데이터를 아직 새로 고치는 중인지 확인할 수 있습니다.

    ![데이터 새로 고침 페이지에서 정보를 읽는 방법을 보여 주는 스크린샷](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. 데이터 새로 고침이 완료 되 면 **Azure Cognitive Search 보고서** 를 선택 하 여 보고서를 봅니다.

    ![데이터 새로 고침 페이지에서 Azure Cognitive Search 보고서를 선택 하는 방법을 보여 주는 스크린샷](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. 보고서를 연 후 데이터와 함께 열리도록 페이지를 새로 고쳐야 합니다.

    ![Azure Cognitive Search Power BI 보고서의 스크린샷](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>앱 매개 변수를 변경 하는 방법
다른 저장소 계정에서 데이터를 시각화 하거나 쿼리할 데이터의 일 수를 변경 하려는 경우 아래 단계에 따라 **일** 및 **storageaccount** 매개 변수를 변경 합니다.

1. Power BI apps로 이동 하 여 Azure Cognitive Search 앱을 찾고 **앱 편집** 단추를 선택 하 여 작업 영역을 확인 합니다.

    ![Azure Cognitive Search 앱에 대 한 앱 편집 단추를 선택 하는 방법을 보여 주는 스크린샷](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. 데이터 집합 옵션에서 **설정을** 선택 합니다.

    ![Azure Congnitive Search 데이터 집합 옵션에서 설정을 선택 하는 방법을 보여 주는 스크린샷](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. 데이터 집합 탭에서 매개 변수 값을 변경 하 고 **적용**을 선택 합니다. 연결에 문제가 있는 경우 동일한 페이지에서 데이터 원본 자격 증명을 업데이트 합니다.

1. 작업 영역으로 다시 이동 하 고 데이터 집합 옵션에서 **지금 새로 고침** 을 선택 합니다.

    ![Azure Congnitive Search 데이터 집합 옵션에서 지금 새로 고침을 선택 하는 방법을 보여 주는 스크린샷](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. 업데이트 된 데이터를 보려면 보고서를 엽니다. 최신 데이터를 보려면 보고서를 새로 고쳐야 할 수도 있습니다.

## <a name="troubleshooting"></a>문제 해결
데이터를 볼 수 없는 경우 다음 문제 해결 단계를 수행 합니다.

1. 보고서를 열고 페이지를 새로 고쳐 최신 데이터를 볼 수 있는지 확인 합니다. 보고서에는 데이터를 새로 고치는 옵션이 있습니다. 최신 데이터를 가져오려면이를 선택 합니다.

1. 제공한 저장소 계정 이름 및 액세스 키가 올바른지 확인 합니다. 저장소 계정 이름은 검색 서비스 로그로 구성 된 계정과 일치 해야 합니다.

1. 저장소 계정에 pt1m **-operationlogs** 및 insights-------- **insights-metrics-pt1m** 및 각 컨테이너의 데이터가 포함 되어 있는지 확인 합니다. 로그 및 메트릭은 폴더의 몇 계층에 포함 됩니다.

1. 데이터 집합을 계속 새로 고치는 지 확인 합니다. 새로 고침 상태 표시기는 위의 8 단계에서 표시 됩니다. 그래도 새로 고치는 경우 새로 고침이 완료 될 때까지 기다렸다가 보고서를 열고 새로 고칩니다.

## <a name="next-steps"></a>다음 단계
[Azure Cognitive Search에 대 한 자세한 정보](./index.yml)

[Power BI란?](/power-bi/fundamentals/power-bi-overview)

[Power BI 서비스의 디자이너를 위한 기본 개념](/power-bi/service-basic-concepts)