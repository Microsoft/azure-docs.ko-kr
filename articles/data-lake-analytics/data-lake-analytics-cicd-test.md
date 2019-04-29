---
title: Azure Data Lake Analytics 코드를 테스트하는 방법
description: Azure Data Lake Analytics용 U-SQL 및 확장 C# 코드에 대한 테스트 사례 추가 방법을 알아봅니다.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 4532e0c6e8095c9d64897410e0492e2135d8a478
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630107"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Azure Data Lake Analytics 코드 테스트

Azure Data Lake는 선언적 SQL을 명령적 C#에 결합하여 규모에 관계 없이 데이터를 처리할 수 있도록 하는 U-SQL 언어를 제공합니다. 이 문서에서는 U-SQL 및 확장 C# UDO(사용자 정의 연산자) 코드에 대해 테스트 사례를 만드는 방법을 알아봅니다.

## <a name="test-u-sql-scripts"></a>U-SQL 스크립트 테스트

U-SQL 스크립트는 실행 코드로 컴파일되고 최적화되어 클라우드의 컴퓨터 또는 로컬 컴퓨터에서 실행됩니다. 컴파일 및 최적화 프로세스는 전체 U-SQL 스크립트를 통째로 처리합니다. 모든 문에 대해 기존의 "단위 테스트"를 수행할 수는 없습니다. 그러나 U-SQL 테스트 SDK 및 로컬 실행 SDK를 사용하여 스크립트 수준 테스트를 수행할 수 있습니다.

### <a name="create-test-cases-for-u-sql-script"></a>U-SQL 스크립트에 대한 테스트 사례 만들기

Azure Data Lake Tools for Visual Studio를 사용하면 U-SQL 스크립트 테스트 사례를 만들 수 있습니다.

1.  솔루션 탐색기에서 U-SQL 스크립트를 마우스 오른쪽 단추로 클릭한 다음, **단위 테스트 만들기**를 선택합니다.
2.  새 테스트 프로젝트를 만들거나 기존 테스트 프로젝트에 테스트 사례를 삽입합니다.

    ![Data Lake Tools for Visual Studio -- U-SQL 테스트 프로젝트 만들기](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![Data Lake Tools for Visual Studio -- U-SQL 테스트 프로젝트 구성 만들기](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-the-test-data-source"></a>테스트 데이터 원본 관리

U-SQL 스크립트를 테스트하는 경우 입력 파일을 테스트해야 합니다. U-SQL 프로젝트 속성에서 **테스트 데이터 원본**을 구성하여 이러한 테스트 데이터를 관리할 수 있습니다. 

U-SQL 테스트 SDK에서 `Initialize()` 인터페이스를 호출할 때 임시 로컬 데이터 루트 폴더가 테스트 프로젝트의 작업 디렉터리 아래에 생성되고, 테스트 데이터 원본 폴더의 모든 파일 및 하위 폴더(및 하위 폴더 아래에 있는 파일)가 U-SQL 스크립트 테스트 사례를 실행하기 전에 임시 로컬 데이터 루트 폴더에 복사됩니다. 세미콜론으로 테스트 데이터 폴더 경로를 분리하여 더 많은 테스트 데이터 원본 폴더를 추가할 수 있습니다.

![Data Lake Tools for Visual Studio -- 프로젝트 테스트 데이터 원본 구성](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-the-database-environment-for-testing"></a>테스트를 위한 데이터베이스 환경 관리

U-SQL 스크립트가 U-SQL 데이터베이스 개체를 사용하거나 이 개체로 쿼리를 수행하는 경우(예: 저장 프로시저 호출) U-SQL 테스트 사례를 실행하기 전에 데이터베이스 환경을 초기화해야 합니다. U-SQL 테스트 SDK의 `Initialize()` 인터페이스는 U-SQL 프로젝트에서 참조하는 모든 데이터베이스를 테스트 프로젝트의 작업 디렉터리에 있는 임시 로컬 데이터 루트 폴더에 배포하도록 도와줍니다. 

[U-SQL 프로젝트에 대한 U-SQL 데이터베이스 프로젝트 참조 관리 방법](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)을 자세히 알아봅니다.

### <a name="verify-test-results"></a>테스트 결과 확인

`Run()` 인터페이스는 작업 실행 결과를 반환합니다. 0은 성공, 1은 실패를 의미합니다. 또한 C# 어설션 함수를 사용하여 출력을 확인할 수도 있습니다. 

### <a name="run-test-cases-in-visual-studio"></a>Visual Studio에서 테스트 사례 실행

U-SQL 스크립트 테스트 프로젝트는 C# 단위 테스트 프레임워크에서 빌드됩니다. 프로젝트를 빌드한 후 **테스트 탐색기 > 재생 목록**을 통해 모든 테스트 사례를 실행할 수 있습니다. 또는 .cs 파일을 마우스 오른쪽 단추로 클릭하고 **테스트 실행**을 선택합니다.

## <a name="test-c-udos"></a>C# UDO 테스트

### <a name="create-test-cases-for-c-udos"></a>C# UDO에 대한 테스트 사례 만들기

C# 단위 테스트 프레임워크를 사용하여 C# UDO(사용자 정의 연산자)를 테스트할 수 있습니다. UDO를 테스트할 때 해당 **IRowset** 개체를 입력으로 준비해야 합니다.

IRowset 개체를 만드는 방법에는 다음 두 가지가 있습니다.

- 파일에서 데이터를 로드하여 IRowset 만들기

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- 데이터 컬렉션의 데이터를 사용하여 IRowset 만들기

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>테스트 결과 확인

UDO 함수를 호출한 후 C# 어설션 함수를 사용하여 스키마 및 행 집합 값 확인을 통해 결과를 확인할 수 있습니다. Visual Studio의 **파일 > 새로 만들기 > 프로젝트**를 통해 U-SQL C# UDO 단위 테스트 샘플 프로젝트에서 샘플 코드를 사용할 수 있습니다.

### <a name="run-test-cases-in-visual-studio"></a>Visual Studio에서 테스트 사례 실행

테스트 프로젝트를 빌드한 후, **테스트 탐색기 > 재생 목록**을 통해 또는 .cs 파일을 마우스 오른쪽 단추로 클릭한 후 **테스트 실행**을 선택하여 모든 테스트 사례를 실행할 수 있습니다.

## <a name="run-test-cases-in-azure-devops"></a>Azure DevOps에서 테스트 사례 실행

**U-SQL 스크립트 테스트 프로젝트** 및 **C# UDO 테스트 프로젝트** 둘 다 C# 단위 테스트 프로젝트를 상속합니다. Azure DevOps의 [Visual Studio 테스트 작업](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts)은 이러한 테스트 사례를 실행할 수 있습니다. 

### <a name="run-u-sql-test-cases-in-azure-devops"></a>Azure DevOps에서 U-SQL 테스트 사례 실행

U-SQL 테스트의 경우 빌드 머신에 `CPPSDK`를 로드하고 USqlScriptTestRunner에 대한 `CPPSDK` 경로(cppSdkFolderFullPath: \@"")를 제공해야 합니다.

**CPPSDK란?**

CPPSDK는 Microsoft Visual C++ 14 및 Windows SDK 10.0.10240.0을 포함하는 패키지입니다. U-SQL 런타임에 필요한 환경입니다. Azure Data Lake Tools for Visual Studio 설치 폴더 아래에 이 패키지를 가져올 수 있습니다.

- Visual Studio 2015의 경우 `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK` 아래에 있습니다.
- Visual Studio 2017의 경우 `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK` 아래에 있습니다.

**Azure DevOps 빌드 에이전트에서 CPPSDK 준비**

Azure DevOps에서 CPPSDK 종속성을 준비하기 위한 가장 일반적인 방법은 다음과 같습니다.

1.  CPPSDK 라이브러리가 포함된 폴더를 zip으로 압축합니다.
2.  zip 파일을 원본 제어 시스템으로 체크 인합니다. (.zip 파일은 CPPSDK 폴더 아래에 있는 모든 라이브러리를 체크 인하여 일부 파일이 ".gitignore"에 의해 무시되지 않도록 합니다.)   
3.  빌드 파이프라인에서 .zip 파일의 압축을 풉니다.
4.  `USqlScriptTestRunner`가 빌드 컴퓨터에서 압축을 푼 이 폴더를 가리키도록 합니다.

### <a name="run-c-udo-test-cases-in-azure-devops"></a>Azure DevOps에서 C# UDO 테스트 사례 실행

C# UDO 테스트의 경우, UDO에 필요한 다음 어셈블리를 참조해야 합니다. [Nuget 패키지 Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/)를 통해 참조하는 경우 빌드 파이프라인에 NuGet 복원 태스크를 추가해야 합니다.

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>다음 단계

- [Azure Data Lake Analytics에 대해 CI/CD 파이프라인을 설정하는 방법](data-lake-analytics-cicd-overview.md)
- [로컬 컴퓨터에서 U-SQL 스크립트 실행](data-lake-analytics-data-lake-tools-local-run.md)
- [U-SQL 데이터베이스 프로젝트를 사용하여 U-SQL 데이터베이스 개발](data-lake-analytics-data-lake-tools-develop-usql-database.md)

