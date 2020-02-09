---
title: Azure Data Factory에서 Azure 데이터 탐색기 제어 명령을 사용 합니다.
description: 이 항목에서는 Azure Data Factory에서 Azure 데이터 탐색기 제어 명령을 사용 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 20da2d54ea54674656b2c1006d094c63133baf79
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264497"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Azure Data Factory 명령 작업을 사용 하 여 Azure 데이터 탐색기 제어 명령 실행

ADF ( [Azure Data Factory](/azure/data-factory/) )는 데이터에 대한 작업의 조합을 수행할 수 있도록 하는 클라우드 기반 데이터 통합 서비스입니다. ADF를 사용 하 여 데이터 이동 및 데이터 변환을 오케스트레이션 하 고 자동화 하기 위한 데이터 기반 워크플로를 만듭니다. Azure Data Factory의 **azure 데이터 탐색기 명령** 작업을 사용 하면 ADF 워크플로 내에서 [azure 데이터 탐색기 제어 명령을](/azure/kusto/concepts/#control-commands) 실행할 수 있습니다. 이 문서에서는 Azure 데이터 탐색기 명령 작업을 포함 하는 ForEach 작업 및 조회 작업을 사용 하 여 파이프라인을 만드는 방법을 배웁니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure 데이터 탐색기 클러스터 및 데이터베이스](create-cluster-database-portal.md)
* 데이터 원본입니다.
* [데이터 팩터리](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>새 파이프라인 만들기

1. **작성자** 연필 도구를 선택 합니다. 
1. **@No__t-1** 을 선택한 다음 드롭다운에서 **파이프라인** 을 선택 하 여 새 파이프라인을 만듭니다.

   ![새 파이프라인 만들기](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>조회 작업 만들기

[조회 작업](/azure/data-factory/control-flow-lookup-activity) 은 Azure Data Factory 지원 되는 데이터 원본에서 데이터 집합을 검색할 수 있습니다. 조회 작업의 출력은 ForEach 또는 다른 작업에서 사용할 수 있습니다.

1. **활동** 창의 **일반**에서 **조회** 활동을 선택 합니다. 오른쪽의 주 캔버스로 끌어다 놓습니다.
 
    ![조회 작업 선택](media/data-factory-command-activity/select-activity.png)

1. 이제 캔버스에는 사용자가 만든 조회 작업이 포함 됩니다. 캔버스 아래에 있는 탭을 사용 하 여 관련 매개 변수를 변경 합니다. **일반적**으로 작업의 이름을 바꿉니다. 

    ![조회 작업 편집](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > 빈 캔버스 영역을 클릭 하면 파이프라인 속성을 볼 수 있습니다. **일반** 탭을 사용 하 여 파이프라인의 이름을 바꿀 수 있습니다. 파이프라인의 이름은 *파이프라인-4-문서*입니다.

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>조회 작업에서 Azure 데이터 탐색기 데이터 집합 만들기

1. **설정**에서 미리 만든 Azure 데이터 탐색기 **원본 데이터 집합**을 선택 하거나 **+ 새로** 만들기를 선택 하 여 새 데이터 집합을 만듭니다.
 
    ![조회 설정에서 데이터 집합 추가](media/data-factory-command-activity/lookup-settings.png)

1. **새 데이터 집합** 창에서 **Azure 데이터 탐색기 (kusto)** 데이터 집합을 선택 합니다. **계속** 을 선택 하 여 새 데이터 집합을 추가 합니다.

   ![새 데이터 집합 선택](media/data-factory-command-activity/select-new-dataset.png) 

1. 새 Azure 데이터 탐색기 데이터 집합 매개 변수는 **설정**에 표시 됩니다. 매개 변수를 업데이트 하려면 **편집**을 선택 합니다.

    ![Azure 데이터 탐색기 데이터 집합을 사용 하 여 조회 설정](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. **AzureDataExplorerTable** 새 탭이 주 캔버스에 열립니다. 
    * **일반** 을 선택 하 고 데이터 집합 이름을 편집 합니다. 
    * **연결** 을 선택 하 여 데이터 집합 속성을 편집 합니다. 
    * 드롭다운에서 **연결 된 서비스** 를 선택 하거나 **+ 새로** 만들기를 선택 하 여 새 연결 된 서비스를 만듭니다.

    ![Azure 데이터 탐색기 데이터 집합 속성 편집](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. 새 연결 된 서비스를 만들 때 **새 연결 된 서비스 (Azure 데이터 탐색기)** 페이지가 열립니다.

    ![ADX 새 연결 된 서비스](media/data-factory-command-activity/adx-new-linked-service.png)

   * Azure 데이터 탐색기 연결 된 서비스의 **이름을** 선택 합니다. 필요한 경우 **설명을** 추가 합니다.
   * **Integration runtime을 통해 연결**에서 필요한 경우 현재 설정을 변경 합니다. 
   * **계정 선택 방법** 에서 다음 두 가지 방법 중 하나를 사용 하 여 클러스터를 선택 합니다. 
        * **Azure 구독에서** 라디오 단추를 선택 하 고 **azure 구독** 계정을 선택 합니다. 그런 다음 **클러스터**를 선택 합니다. 드롭다운은 사용자에 속하는 클러스터만 나열 합니다.
        * 대신 **수동으로 입력** 라디오 단추를 선택 하 고 **엔드포인트** (클러스터 URL)을 입력 합니다.
    * **테넌트**를 지정 합니다.
    * **서비스 사용자 ID**를 입력 합니다. 사용 중인 명령에 필요한 사용 권한 수준에 따라 보안 주체 ID에 적절 한 권한이 있어야 합니다.
    * **서비스 사용자 키** 단추를 선택 하 고 **서비스 사용자 키**를 입력 합니다.
    * 드롭다운 메뉴에서 **데이터베이스** 를 선택 합니다. 또는 **편집** 확인란을 선택 하 고 데이터베이스 이름을 입력 합니다.
    * **연결 테스트** 를 선택 하 여 만든 연결 된 서비스 연결을 테스트 합니다. 설치 프로그램에 연결할 수 있는 경우 녹색 확인 표시 **연결 성공** 이 표시 됩니다.
    * **마침** 을 선택 하 여 연결 된 서비스 만들기를 완료 합니다.

1. 연결 된 서비스를 설정한 후 **AzureDataExplorerTable** > **연결**에서 **테이블** 이름을 추가 합니다. 데이터 **미리 보기**를 선택 하 여 데이터가 올바르게 표시 되는지 확인 합니다.

   이제 데이터 집합이 준비 되었으므로 파이프라인을 계속 편집할 수 있습니다.

### <a name="add-a-query-to-your-lookup-activity"></a>조회 활동에 쿼리 추가

1. **파이프라인-4-문서** > **설정** **쿼리** 텍스트 상자에 쿼리를 추가 합니다. 예를 들면 다음과 같습니다.

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. 필요에 따라 **쿼리 제한 시간** 을 변경 하거나 **잘림** 및 **첫 행만** 속성을 변경 합니다. 이 흐름에서는 기본 **쿼리 제한 시간** 을 유지 하 고 확인란의 선택을 취소 합니다. 

    ![조회 작업의 최종 설정](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>For Each 활동 만들기 

[For each](/azure/data-factory/control-flow-for-each-activity) 작업은 컬렉션을 반복 하 고 루프에서 지정 된 작업을 실행 하는 데 사용 됩니다. 

1. 이제 각 작업을 파이프라인에 추가 합니다. 이 활동은 조회 활동에서 반환 된 데이터를 처리 합니다. 
    * **활동** 창의 **반복 & 조건**에서 **ForEach** 활동을 선택 하 고 캔버스에 끌어 놓습니다.
    * 조회 활동의 출력과 캔버스에 있는 ForEach 활동의 입력 사이에 선을 그려 연결 합니다.

        ![ForEach 작업](media/data-factory-command-activity/for-each-activity.png)

1.  캔버스에서 ForEach 활동을 선택 합니다. **설정** 탭에서 다음을 수행 합니다.
    * **순차** 확인란을 선택 하 여 조회 결과를 순차적으로 처리 하거나 선택 취소 된 상태로 두고 병렬 처리를 만듭니다.
    * **일괄 처리 횟수**를 설정 합니다.
    * **항목**에서 출력 값 *@activity (' Lookup1 '). 값* 에 대한 참조를 제공 합니다.

       ![ForEach 활동 설정](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>ForEach 활동 내에서 Azure 데이터 탐색기 명령 활동 만들기

1. 캔버스에서 ForEach 활동을 두 번 클릭 하 여 새 캔버스로 열고 ForEach 내의 활동을 지정 합니다.
1. **활동** 창의 **Azure 데이터 탐색기**에서 **azure 데이터 탐색기 명령** 활동을 선택 하 고 캔버스에 끌어 놓습니다.

    ![Azure 데이터 탐색기 명령 작업](media/data-factory-command-activity/adx-command-activity.png)

1.  **연결** 탭에서 이전에 만든 것과 동일한 연결 된 서비스를 선택 합니다.

    ![azure 데이터 탐색기 명령 작업 연결 탭](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. **명령** 탭에서 다음 명령을 제공 합니다.

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    **명령은** 지정 된 쿼리의 결과를 압축 된 형식의 blob 저장소로 내보내도록 Azure 데이터 탐색기에 지시 합니다. 비동기 한정자를 사용 하 여 비동기적으로 실행 됩니다.
    쿼리는 조회 작업 결과에서 각 행의 데이터베이스 열을 해결 합니다. **명령 제한 시간** 을 변경 되지 않은 상태로 유지할 수 있습니다.

    ![명령 작업](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > 명령 활동에는 다음과 같은 제한 사항이 있습니다.
    > * 크기 제한: 1mb 응답 크기
    > * 시간 제한: 20 분 (기본값), 1 시간 (최대)
    > * 필요한 경우 [AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands)를 사용 하 여 결과에 쿼리를 추가 하 여 결과 크기/시간을 줄일 수 있습니다.

1.  이제 파이프라인이 준비 되었습니다. 파이프라인 이름을 클릭 하 여 주 파이프라인 보기로 돌아갈 수 있습니다.

    ![Azure 데이터 탐색기 명령 파이프라인](media/data-factory-command-activity/adx-command-pipeline.png)

1. 파이프라인을 게시 하기 전에 **디버그** 를 선택 합니다. 파이프라인 진행률은 **출력** 탭에서 모니터링할 수 있습니다.

    ![azure 데이터 탐색기 명령 활동 출력](media/data-factory-command-activity/command-activity-output.png)

1. 모든를 **게시** 한 다음 **트리거를 추가** 하 여 파이프라인을 실행할 수 있습니다. 

## <a name="control-command-outputs"></a>제어 명령 출력

명령 작업 출력의 구조는 아래에 자세히 설명 되어 있습니다. 이 출력은 파이프라인의 다음 작업에서 사용할 수 있습니다.

### <a name="returned-value-of-a-non-async-control-command"></a>비동기 제어 명령이 아닌 명령의 값을 반환 했습니다.

비 비동기 제어 명령에서 반환 된 값의 구조는 조회 작업 결과의 구조와 유사 합니다. @No__t-0 필드는 반환 된 레코드 수를 나타냅니다. 고정 배열 필드 `value`은 레코드 목록을 포함 합니다. 

```json
{ 
    "count": "2", 
    "value": [ 
        { 
            "ExtentId": "1b9977fe-e6cf-4cda-84f3-4a7c61f28ecd", 
            "ExtentSize": 1214.0, 
            "CompressedSize": 520.0 
        }, 
        { 
            "ExtentId": "b897f5a3-62b0-441d-95ca-bf7a88952974", 
            "ExtentSize": 1114.0, 
            "CompressedSize": 504.0 
        } 
    ] 
} 
```
 
### <a name="returned-value-of-an-async-control-command"></a>비동기 제어 명령의 반환 값

비동기 제어 명령에서 작업은 비동기 작업이 완료 되거나 제한 시간이 초과 될 때까지 백그라운드에서 작업 테이블을 폴링합니다. 따라서 반환 된 값에는 해당 **OperationId** 속성에 대해 `.show operations OperationId`의 결과가 포함 됩니다. **상태** 및 **상태** 속성의 값을 확인 하 여 작업이 성공적으로 완료 되었는지 확인 합니다.

```json
{ 
    "count": "1", 
    "value": [ 
        { 
            "OperationId": "910deeae-dd79-44a4-a3a2-087a90d4bb42", 
            "Operation": "TableSetOrAppend", 
            "NodeId": "", 
            "StartedOn": "2019-06-23T10:12:44.0371419Z", 
            "LastUpdatedOn": "2019-06-23T10:12:46.7871468Z", 
            "Duration": "00:00:02.7500049", 
            "State": "Completed", 
            "Status": "", 
            "RootActivityId": "f7c5aaaf-197b-4593-8ba0-e864c94c3c6f", 
            "ShouldRetry": false, 
            "Database": "MyDatabase", 
            "Principal": "<some principal id>", 
            "User": "<some User id>" 
        } 
    ] 
}
``` 

## <a name="next-steps"></a>다음 단계

* [Azure Data Factory를 사용 하 여 Azure 데이터 탐색기에 데이터를 복사](data-factory-load-data.md)하는 방법에 대해 알아봅니다.
* [데이터베이스에서 Azure 데이터 탐색기 대량 복사를 위해 Azure Data Factory 템플릿](data-factory-template.md)사용에 대해 알아봅니다.
