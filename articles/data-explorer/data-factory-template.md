---
title: Azure Data Factory 템플릿을 사용 하 여 데이터베이스에서 Azure 데이터 탐색기로 대량 복사
description: 이 문서에서는 Azure Data Factory 템플릿을 사용 하 여 데이터베이스에서 Azure로 대량 복사 하는 방법에 대해 알아봅니다 데이터 탐색기
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293558"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Azure Data Factory 템플릿을 사용 하 여 데이터베이스에서 Azure 데이터 탐색기로 대량 복사 

Azure 데이터 탐색기는 빠르고 완전히 관리 되는 데이터 분석 서비스입니다. 응용 프로그램, 웹 사이트 및 IoT 장치와 같은 많은 원본에서 스트리밍하는 대용량 데이터에 대한 실시간 분석을 제공 합니다. 

Oracle Server, Netezza, Teradata 또는 SQL Server의 데이터베이스에서 Azure 데이터 탐색기로 데이터를 복사 하려면 여러 테이블에서 대량의 데이터를 로드 해야 합니다. 일반적으로 단일 테이블에서 여러 스레드가 동시에 행을 로드할 수 있도록 각 테이블에서 데이터를 분할 해야 합니다. 이 문서에서는 이러한 시나리오에서 사용할 템플릿을 설명 합니다.

[Azure Data Factory 템플릿은](/azure/data-factory/solution-templates-introduction) 파이프라인 Data Factory 미리 정의 되어 있습니다. 이러한 템플릿을 통해 신속 하 게 Data Factory를 시작 하 고 데이터 통합 프로젝트에 대한 개발 시간을 줄일 수 있습니다. 

*조회* 및 *ForEach* 활동을 사용 하 여 *데이터베이스에서 Azure 데이터 탐색기 템플릿으로 대량 복사* 를 만듭니다. 더 빠른 데이터 복사를 위해 템플릿을 사용 하 여 데이터베이스당 또는 테이블당 많은 파이프라인을 만들 수 있습니다. 

> [!IMPORTANT]
> 복사 하려는 데이터의 양에 적합 한 도구를 사용 해야 합니다.
> * *데이터베이스에서 azure 데이터 탐색기로 대량 복사* 를 사용 하 여 SQL Server 및 Google 이상 쿼리와 같은 데이터베이스의 많은 양의 데이터를 azure 데이터 탐색기에 복사 합니다. 
> * [*Data Factory 데이터 복사 도구*](data-factory-load-data.md) 를 사용 하 여 적은 양의 데이터를 포함 하는 몇 개의 테이블을 Azure 데이터 탐색기에 복사 합니다. 

## <a name="prerequisites"></a>필수 조건

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Data Explorer 클러스터 및 데이터베이스](create-cluster-database-portal.md).
* [데이터 팩터리를 만듭니다](data-factory-load-data.md#create-a-data-factory).
* 데이터베이스의 데이터 원본입니다.

## <a name="create-controltabledataset"></a>ControlTableDataset 만들기

*Controltabledataset* 파이프라인의 원본에서 대상으로 복사할 데이터를 나타냅니다. 행 수는 데이터를 복사 하는 데 필요한 총 파이프라인 수를 나타냅니다. ControlTableDataset을 원본 데이터베이스의 일부로 정의 해야 합니다.

SQL Server 원본 테이블 형식의 예는 다음 코드에 나와 있습니다.
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

다음 표에서는 코드 요소에 대해 설명 합니다.

|속성  |Description  | 예
|---------|---------| ---------|
|PartitionId   |  복사 순서 | 1  |  
|SourceQuery   |  파이프라인 런타임 중에 복사할 데이터를 나타내는 쿼리입니다. | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  대상 테이블 이름입니다. | MyAdxTable       |  

ControlTableDataset의 형식이 다른 경우 형식에 대한 비교할 수 있는 ControlTableDataset을 만듭니다.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>데이터베이스에서 Azure 데이터 탐색기 템플릿으로 대량 복사를 사용 합니다.

1. **시작** 하기 창에서 **템플릿에서 파이프라인 만들기** 를 선택 하 여 **템플릿 갤러리** 창을 엽니다.

    ![Azure Data Factory "시작 하기" 창](media/data-factory-template/adf-get-started.png)

1. **데이터베이스에서 Azure로 대량 복사 데이터 탐색기** 템플릿을 선택 합니다.
 
    !["데이터베이스에서 Azure 데이터 탐색기로 대량 복사" 템플릿](media/data-factory-template/pipeline-from-template.png)

1.  **데이터베이스에서 Azure로 대량 복사 데이터 탐색기** 창의 **사용자 입력**에서 다음을 수행 하 여 데이터 집합을 지정 합니다. 

    a. **Controltabledataset** 드롭다운 목록에서 원본에서 대상으로 복사 되는 데이터와 대상에 저장 되는 데이터를 나타내는 컨트롤 테이블의 연결 된 서비스를 선택 합니다. 

    b. **Sourcedataset** 드롭다운 목록에서 원본 데이터베이스에 대한 연결 된 서비스를 선택 합니다. 

    다. **AzureDataExplorerTable** 드롭다운 목록에서 Azure 데이터 탐색기 테이블을 선택 합니다. 데이터 집합이 없는 경우 [Azure 데이터 탐색기 연결 된 서비스를 만들어](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) 데이터 집합을 추가 합니다.

    d. **이 템플릿 사용**을 선택합니다.

    !["데이터베이스에서 Azure 데이터 탐색기로 대량 복사" 창](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. 활동 외부에서 캔버스의 영역을 선택 하 여 템플릿 파이프라인에 액세스 합니다. **이름** (컨트롤 테이블 이름) 및 **기본값** (열 이름)을 포함 하 여 테이블에 대한 매개 변수를 입력 하려면 **매개 변수** 탭을 선택 합니다.

    ![파이프라인 매개 변수](media/data-factory-template/pipeline-parameters.png)

1.  **조회**아래에서 **get파티션 목록을** 선택 하 여 기본 설정을 확인 합니다. 쿼리가 자동으로 만들어집니다.
1.  명령 작업 **ForEachPartition**를 선택 하 고 **설정** 탭을 선택한 후 다음을 수행 합니다.

    a. **일괄 처리 횟수** 상자에 1에서 50 사이의 숫자를 입력 합니다. 이 선택은 *Controltabledataset* 행 수에 도달할 때까지 병렬로 실행 되는 파이프라인 수를 결정 합니다. 

    b. 파이프라인 일괄 처리가 병렬로 실행 되도록 하려면 **순차** 확인란을 선택 *하지 마십시오* .

    ![ForEachPartition 설정](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > 가장 좋은 방법은 데이터를 더 빠르게 복사할 수 있도록 많은 파이프라인을 병렬로 실행 하는 것입니다. 효율성을 높이려면 원본 테이블의 데이터를 분할 하 고 날짜 및 테이블에 따라 파이프라인 당 하나의 파티션을 할당 합니다.

1. **모두 유효성 검사** 를 선택 하 Azure Data Factory 파이프라인의 유효성을 검사 한 다음 **파이프라인 유효성 검사 출력** 창에서 결과를 확인 합니다.

    ![템플릿 파이프라인 유효성 검사](media/data-factory-template/validate-template-pipelines.png)

1. 필요한 경우 **디버그**를 선택한 다음 **트리거 추가** 를 선택 하 여 파이프라인을 실행 합니다.

    !["디버그" 및 "파이프라인 실행" 단추](media/data-factory-template/trigger-run-of-pipeline.png)    

이제 템플릿을 사용 하 여 데이터베이스 및 테이블에서 대량의 데이터를 효율적으로 복사할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Data Factory를 사용 하 여 Azure 데이터 탐색기에 데이터를 복사](data-factory-load-data.md)하는 방법을 알아봅니다.
* Azure Data Factory의 [Azure 데이터 탐색기 커넥터](/azure/data-factory/connector-azure-data-explorer) 에 대해 알아봅니다.
* 데이터 쿼리를 위한 [Azure 데이터 탐색기 쿼리](/azure/data-explorer/web-query-data) 에 대해 알아봅니다.






