---
title: 파일에서 데이터베이스로 대량 복사
description: 솔루션 템플릿을 사용 하 여 Azure Data Lake Storage Gen2에서 Azure Synapse Analytics/Azure SQL Database로 데이터를 대량으로 복사 하는 방법을 알아봅니다.
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: ae250c7d15801789ad22955845cfa535ed91f2c1
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921147"
---
# <a name="bulk-copy-from-files-to-database"></a>파일에서 데이터베이스로 대량 복사

이 문서에서는 Azure Data Lake Storage Gen2에서 Azure Synapse Analytics/Azure SQL Database로 대량으로 데이터를 복사 하는 데 사용할 수 있는 솔루션 템플릿에 대해 설명 합니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 Azure Data Lake Storage Gen2 원본에서 파일을 검색 합니다. 그런 다음 원본에 있는 각 파일을 반복 하 고 대상 데이터 저장소에 파일을 복사 합니다. 

현재이 템플릿은 **DelimitedText** 형식으로 데이터를 복사 하는 것만 지원 합니다. 다른 데이터 형식의 파일은 원본 데이터 저장소에서 검색할 수도 있지만 대상 데이터 저장소에 복사할 수는 없습니다.  

이 템플릿은 다음 세 가지 작업을 포함합니다.
- **메타 데이터 가져오기** 작업은 Azure Data Lake Storage Gen2에서 파일을 검색 하 고 후속 *ForEach* 활동에 전달 합니다.
- **ForEach** 작업은 *메타 데이터 가져오기* 작업에서 파일을 가져오고 각 파일을 *복사* 작업으로 반복 합니다.
- **복사** 작업은 각 파일을 원본 데이터 저장소에서 대상 데이터 저장소로 복사 하기 위해 *ForEach* 작업에 상주 합니다.

템플릿은 다음과 같은 두 개의 매개 변수를 정의 합니다.
- *SourceContainer* 는 Azure Data Lake Storage Gen2에서 데이터가 복사 되는 루트 컨테이너 경로입니다. 
- *Sourcedirectory* 는 Azure Data Lake Storage Gen2의에서 데이터를 복사 하는 루트 컨테이너의 디렉터리 경로입니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. **파일에서 데이터베이스로 대량 복사** 템플릿으로 이동 합니다. 원본 Gen2 저장소에 대한 **새** 연결을 만듭니다. "GetMetadataDataset" 및 "SourceDataset"은 원본 파일 저장소의 동일한 연결에 대한 참조입니다.

    ![원본 데이터 저장소에 대한 새 연결 만들기](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. 데이터를 복사 하 고 있는 싱크 데이터 저장소에 대한 **새** 연결을 만듭니다.

    ![싱크 데이터 저장소에 대한 새 연결 만들기](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. **이 템플릿 사용**을 선택합니다.

    ![이 템플릿 사용](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. 다음 예제와 같이 생성 된 파이프라인이 표시 됩니다.

    ![파이프라인 검토](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > 위에서 설명한 **2 단계** 에서 **azure Synapse ANALYTICS (이전의 SQL DW)** 를 데이터 대상으로 선택한 경우 Polybase SQL Data Warehouse 필요에 따라 스테이징을 위해 azure Blob 저장소에 대한 연결을 입력 해야 합니다. 다음 스크린샷에 표시 된 것 처럼 템플릿은 Blob 저장소에 대한 *저장소 경로* 를 자동으로 생성 합니다. 파이프라인이 실행 된 후 컨테이너를 만들었는지 확인 합니다.
        
    ![Polybase 설정](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. **디버그**를 선택 하 고 **매개 변수**를 입력 한 다음 **마침**을 선택 합니다.

    ![\* * 디버그 * *를 클릭 합니다.](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. 파이프라인 실행이 성공적으로 완료 되 면 다음 예제와 유사한 결과가 표시 됩니다.

    ![결과 검토](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)