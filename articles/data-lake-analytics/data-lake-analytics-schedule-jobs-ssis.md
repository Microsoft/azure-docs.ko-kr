---
title: SSIS를 사용하여 Azure Data Lake Analytics U-SQL 작업 예약
description: SQL Server Integration Services를 사용하여 U-SQL 작업을 예약하는 방법을 알아봅니다.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/17/2018
ms.openlocfilehash: 6894486118f69e682353142be04821e1d28440e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814670"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>SSIS(SQL Server Integration Services)를 사용하여 U-SQL 작업 예약

이 문서에서는 SSIS(SQL Server Integration Services)를 사용하여 U-SQL 작업을 오케스트레이션하고 만드는 방법을 배웁니다. 

## <a name="prerequisites"></a>필수 조건

[Azure Feature Pack for Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)에서는 Azure Data Lake Analytics 서비스에 연결하는 데 유용한 [Azure Data Lake Analytics 연결 관리자](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017) 및 [Azure Data Lake Analytics 태스크](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017)를 제공합니다. 이 태스크를 사용하려면 다음을 설치해야 합니다.

- [Visual Studio용 SSDT(SQL Server Data Tools) 다운로드 및 설치](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [Azure Feature Pack for Integration Services(SSIS) 설치](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Azure Data Lake Analytics 태스크

Azure Data Lake Analytics 태스크를 사용하면 사용자가 U-SQL 작업을 Azure Data Lake Analytics 계정에 제출할 수 있습니다. 

[Azure Data Lake Analytics 태스크를 구성하는 방법을 알아봅니다](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

![SSIS의 Azure Data Lake Analytics 태스크](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

SSIS 기본 제공 함수 및 태스크를 사용하여 여러 위치에서 U-SQL 스크립트를 가져올 수 있습니다. 아래 시나리오에서는 다양한 사용자 사례에 대해 U-SQL 스크립트를 구성하는 방법을 보여 줍니다.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>시나리오 1 - 인라인 스크립트 호출 TVF 및 저장 프로시저 사용

Azure Data Lake Analytics 태스크 편집기에서 **SourceType**을 **DirectInput**으로 구성하고 U-SQL 문을 **USQLStatement**에 배치합니다.

유지 관리와 코드 관리가 용이하도록, 짧은 U-SQL 스크립트만 인라인 스크립트로 배치합니다. 예를 들어, U-SQL 데이터베이스에서 기존 테이블 반환 함수 및 저장 프로시저를 호출할 수 있습니다. 

![SSIS 태스크에서 인라인 U-SQL 스크립트 편집](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

관련 문서: [저장 프로시저에 매개 변수를 전달하는 방법](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>시나리오 2 - Azure Data Lake Store의 U-SQL 파일 사용

Azure Feature Pack의 **Azure Data Lake Store 파일 시스템 태스크**를 사용하여 Azure Data Lake Store의 U-SQL 파일을 사용할 수도 있습니다. 이 방법을 사용하면 클라우드에 저장된 스크립트를 사용할 수 있습니다.

아래 단계에 따라 Azure Data Lake Store 파일 시스템 태스크와 Azure Data Lake Analytics 태스크 간의 연결을 설정합니다.

### <a name="set-task-control-flow"></a>태스크 제어 흐름 설정

SSIS 패키지 디자인 뷰에서 **Azure Data Lake Store 파일 시스템 태스크**, **Foreach 루프 컨테이너** 및 Foreach 루프 컨테이너 내의 **Azure Data Lake Analytics 태스크**를 추가합니다. Azure Data Lake Store 파일 시스템 태스크는 ADLS 계정의 U-SQL 파일을 임시 폴더로 다운로드하는 데 도움이 됩니다. Foreach 루프 컨테이너 및 Azure Data Lake Analytics 태스크는 임시 폴더 아래의 모든 U-SQL 파일을 Azure Data Lake Analytics 계정에 U-SQL 작업으로 제출하는 데 도움이 됩니다.

![Azure Data Lake Store의 U-SQL 파일 사용](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Azure Data Lake Store 파일 시스템 태스크 구성

1. **작업**을 **CopyFromADLS**로 설정합니다.
2. **AzureDataLakeConnection**을 설정하고, [Azure Data Lake Store 연결 관리자](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017)에 대해 자세히 알아보세요.
3. **AzureDataLakeDirectory**를 설정합니다. U-SQL 스크립트를 저장하는 폴더를 가리킵니다. Azure Data Lake Store 계정 루트 폴더를 기준으로 하는 상대 경로를 사용합니다.
4. **대상**을 다운로드한 U-SQL 스크립트를 캐시하는 폴더로 설정합니다. 이 폴더 경로는 U-SQL 작업 제출을 위해 Foreach 루프 컨테이너에서 사용됩니다. 

![Azure Data Lake Store 파일 시스템 태스크 구성](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Azure Data Lake Store 파일 시스템 태스크에 대해 자세히 알아보세요](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017).

### <a name="configure-foreach-loop-container"></a>Foreach 루프 컨테이너 구성

1. **컬렉션** 페이지에서 **열거자**를 **Foreach File 열거자**로 설정합니다.

2. **열거자 구성** 그룹 아래의 **폴더**를 다운로드한 U-SQL 스크립트가 포함된 임시 폴더로 설정합니다.

3. 루프 컨테이너가 `.usql`로 끝 나는 파일만 캐시하도록 **열거자 구성** 아래에 있는 **파일**을 `*.usql`로 설정합니다.

    ![Foreach 루프 컨테이너 구성](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. **변수 매핑** 페이지에서 사용자 정의 변수를 추가하여 각 U-SQL 파일의 파일 이름을 가져옵니다. 파일 이름을 가져오려면 **인덱스**를 0으로 설정합니다. 이 예제에서는 `User::FileName`이라는 변수를 정의합니다. 이 변수는 U-SQL 스크립트 파일 연결을 동적으로 가져오고 Azure Data Lake Analytics 태스크에서 U-SQL 작업 이름을 설정하는 데 사용됩니다.

    ![파일 이름을 가져오도록 Foreach 루프 컨테이너 구성](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Azure Data Lake Analytics 태스크 구성 

1. **SourceType**을 **FileConnection**으로 설정합니다.

2. **FileConnection**을 Foreach 루프 컨테이너에서 반환된 파일 개체를 가리키는 파일 연결로 설정합니다.
    
    이 파일 연결을 만들려면

   1. 선택할  **\<새 연결... >** FileConnection 설정에서 합니다.
   2. **사용 유형**을 **기존 파일**로 설정하고, **파일**을 기존 파일의 파일 경로로 설정합니다.

       ![Foreach 루프 컨테이너 구성](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. **연결 관리자** 보기에서 방금 만든 파일 연결을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

   4. **속성** 창에서 **식**을 확장하고 **ConnectionString**을 Foreach 루프 컨테이너에 정의된 변수(예: `@[User::FileName]`)로 설정합니다.

       ![Foreach 루프 컨테이너 구성](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. **AzureDataLakeAnalyticsConnection**을 작업이 제출될 Azure Data Lake Analytics 계정으로 설정합니다. [Azure Data Lake Analytics 연결 관리자](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017)에 대해 자세히 알아보세요.

4. 기타 작업 구성을 설정합니다. [자세한 정보](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

5. **식**을 사용하여 U-SQL 작업 이름을 동적으로 설정합니다.

    1. **식** 페이지에서 **JobName**에 대해 새로운 식 키-값 쌍을 추가합니다.
    2. JobName의 값을 Foreach 루프 컨테이너에 정의된 변수(예: `@[User::FileName]`)로 설정합니다.
    
        ![U-SQL 작업 이름에 대한 SSIS 식 구성](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>시나리오 3 - Azure Blob Storage의 U-SQL 파일 사용

Azure Feature Pack의 **Azure Blob 다운로드 태스크**를 사용하여 Azure Blob Storage의 U-SQL 파일을 사용할 수 있습니다. 이 방법을 사용하면 클라우드에 있는 스크립트를 사용할 수 있습니다.

단계는 [시나리오 2: Azure Data Lake Store의 U-SQL 파일 사용](#scenario-2-use-u-sql-files-in-azure-data-lake-store)과 비슷합니다. Azure Data Lake Store 파일 시스템 태스크를 Azure Blob 다운로드 태스크로 변경합니다. [Azure Blob 다운로드 태스크에 대해 자세히 알아보세요](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017).

제어 흐름은 아래와 같습니다.

![Azure Data Lake Store의 U-SQL 파일 사용](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>시나리오 4 - 로컬 머신의 U-SQL 파일 사용

클라우드에 저장된 U-SQL 파일을 사용하는 것 외에도, SSIS 패키지와 함께 배포된 파일 또는 로컬 머신의 파일을 사용할 수도 있습니다.

1. SSIS 프로젝트에서 **연결 관리자**를 마우스 오른쪽 단추로 클릭하고 **새 연결 관리자**를 선택합니다.

2. **파일** 유형을 선택하고 **추가...** 를 클릭합니다.

3. **사용 유형**을 **기존 파일**로 설정하고, **파일**을 로컬 머신의 파일로 설정합니다.

    ![로컬 파일에 대한 파일 연결 추가](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. **Azure Data Lake Analytics** 태스크를 추가하고 다음을 수행합니다.
    1. **SourceType**을 **FileConnection**으로 설정합니다.
    2. **FileConnection**을 방금 만든 파일 연결로 설정합니다.

5. Azure Data Lake Analytics 태스크에 대한 기타 구성을 완료합니다.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>시나리오 5 - SSIS 변수에 U-SQL 문 사용

U-SQL 문을 동적으로 생성해야 하는 경우도 있습니다. **SSIS 식** 및 스크립트 태스크와 같은 기타 SSIS 태스크와 함께 **SSIS 변수**를 사용하면 U-SQL 문을 동적으로 생성할 수 있습니다.

1. **SSIS > 변수** 최상위 메뉴를 통해 변수 도구 창을 엽니다.

2. SSIS 변수를 추가하고 값을 직접 설정하거나, **식**을 사용하여 값을 생성합니다.

3. **Azure Data Lake Analytics 태스크**를 추가하고 다음을 수행합니다.
    1. **SourceType**을 **변수**로 설정합니다.
    2. **SourceVariable**을 방금 만든 SSIS 변수로 설정합니다.

4. Azure Data Lake Analytics 태스크에 대한 기타 구성을 완료합니다.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>시나리오 6 - U-SQL 스크립트에 매개 변수 전달

U-SQL 스크립트에서 U-SQL 변수 값을 동적으로 설정하려는 경우도 있습니다. 이 시나리오에서는 Azure Data Lake Analytics 태스크의 **매개 변수 매핑** 기능을 사용할 수 있습니다. 대체로 다음 두 가지 일반적인 사용 사례가 있습니다.

- 입력 및 출력 파일 경로 변수를 현재 날짜 및 시간에 따라 동적으로 설정합니다.
- 저장 프로시저의 매개 변수를 설정합니다.

[U-SQL 스크립트의 매개 변수를 설정하는 방법을 자세히 알아보세요](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration).

## <a name="next-steps"></a>다음 단계

- [Azure에서 SSIS 패키지 실행](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [Azure Feature Pack for Integration Services(SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [Azure Data Factory를 사용하여 U-SQL 작업 예약](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)

