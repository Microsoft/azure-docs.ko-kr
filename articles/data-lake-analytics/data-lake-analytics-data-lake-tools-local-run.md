---
title: 로컬 머신에서 Azure Data Lake U-SQL 스크립트 실행
description: 로컬 컴퓨터에서 Azure Data Lake Tools for Visual Studio를 사용하여 U-SQL 작업을 실행하는 방법을 알아봅니다.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 42e58125fcbc3ab411c0d7503c42c14c28178428
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62113938"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>로컬 머신에서 U-SQL 스크립트 실행

U-SQL 스크립트를 개발하는 경우 스크립트를 로컬로 실행하여 시간과 비용을 절약할 수 있습니다. Azure Data Lake Tools for Visual Studio는 로컬 머신에서 U-SQL 스크립트를 실행하도록 지원합니다. 

## <a name="basic-concepts-for-local-runs"></a>로컬 실행에 대한 기본 개념

아래 차트에서는 로컬 실행을 위한 구성 요소와 이러한 구성 요소가 클라우드 실행에 매핑되는 방식을 보여 줍니다.

|구성 요소|로컬 실행|클라우드 실행|
|---------|---------|---------|
|Storage|로컬 데이터 루트 폴더|기본 Azure Data Lake Store 계정|
|컴퓨팅|U-SQL 로컬 실행 엔진|Azure Data Lake Analytics 서비스|
|실행 환경|로컬 컴퓨터의 작업 디렉터리|Azure Data Lake Analytics 클러스터|

다음 섹션에서는 로컬 실행 구성 요소에 대한 자세한 정보를 제공합니다.

### <a name="local-data-root-folders"></a>로컬 데이터 루트 폴더

로컬 데이터 루트 폴더는 로컬 계산 계정의 **로컬 저장소**입니다. 로컬 머신의 로컬 파일 시스템에 있는 모든 폴더는 로컬 데이터 루트 폴더일 수 있습니다. 이것은 Data Lake Analytics 계정의 기본 Azure Data Lake Store 계정과 동일합니다. 다른 데이터 루트 폴더로 전환하는 것은 다른 기본 저장소 계정으로 전환하는 것과 같습니다. 

데이터 루트 폴더는 다음과 같이 사용됩니다.
- 저장소 메타데이터. 예로 데이터베이스, 테이블, 테이블 반환 함수 및 어셈블리가 있습니다.
- U-SQL 스크립트에서 상대 경로로 정의된 입력 및 출력 경로를 조회합니다. 상대 경로를 사용하면 U-SQL 스크립트를 Azure에 더 쉽게 배포할 수 있습니다.

### <a name="u-sql-local-run-engines"></a>U-SQL 로컬 실행 엔진

U-SQL 로컬 실행 엔진은 U-SQL 작업에 대한 **로컬 계산 계정**입니다. 사용자는 Azure Data Lake Tools for Visual Studio를 통해 U-SQL 작업을 로컬로 실행할 수 있습니다. Azure Data Lake U-SQL SDK 명령줄 및 프로그래밍 인터페이스를 통한 로컬 실행도 지원됩니다. [Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)에 대해 자세히 알아봅니다.

### <a name="working-directories"></a>작업 디렉터리

U-SQL 스크립트를 실행하는 경우 작업 디렉터리 폴더는 컴파일 결과를 캐시하고, 로그를 실행하고, 다른 기능을 수행해야 합니다. Azure Data Lake Tools for Visual Studio에서 작업 디렉터리는 U-SQL 프로젝트의 작업 디렉터리입니다. `<U-SQL project root path>/bin/debug>`에 있습니다. 새 실행이 트리거될 때마다 작업 디렉터리가 정리됩니다.

## <a name="local-runs-in-microsoft-visual-studio"></a>Microsoft Visual Studio의 로컬 실행

Azure Data Lake Tools for Visual Studio에는 기본 제공 로컬 실행이 있습니다. 도구는 로컬 계산 계정으로 엔진을 노출합니다. U-SQL 스크립트를 로컬로 실행하려면 스크립트의 편집기 여백 드롭다운 메뉴에서 **Local-machine** 또는 **Local-project** 계정을 선택합니다. 그런 다음, **제출**을 선택합니다.

![로컬 계정에 U-SQL 스크립트 제출](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Local-machine 계정을 사용한 로컬 실행

**Local-machine** 계정은 로컬 저장소 계정으로서 단일 로컬 데이터 루트 폴더를 갖는 공유 로컬 계산 계정입니다. 기본적으로 데이터 루트 폴더의 위치는 **C:\Users\<username>\AppData\Local\USQLDataRoot**입니다. 또한 **도구** > **Data Lake** > **옵션 및 설정**을 통해 구성할 수 있습니다.

![로컬 데이터 루트 폴더 구성](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
U-SQL 프로젝트는 로컬 실행에 필요합니다. U-SQL 프로젝트의 작업 디렉터리는 U-SQL 로컬 실행 작업 디렉터리에 사용됩니다. 로컬 실행을 수행하는 동안 컴파일 결과, 실행 로그 및 기타 작업 실행 관련 파일이 생성된 후 작업 디렉터리 폴더에 저장됩니다. 스크립트를 다시 실행할 때마다 작업 디렉터리의 모든 파일이 정리되고 다시 생성됩니다.

## <a name="local-runs-with-a-local-project-account"></a>Local-project 계정을 사용한 로컬 실행

**Local-project** 계정은 격리된 로컬 데이터 루트 폴더가 있는 각 프로젝트에 대한 프로젝트 격리 로컬 계산 계정입니다. Visual Studio의 솔루션 탐색기에서 열리는 모든 활성 U-SQL 프로젝트에는 해당하는 `(Local-project: <project name>)` 계정이 있습니다. 계정은 Visual Studio의 서버 탐색기 및 U-SQL 스크립트 편집기 여백 모두에 나열됩니다.  

**Local-project** 계정은 정리되고 격리된 개발 환경을 제공합니다. **Local-machine** 계정에는 모든 로컬 작업에 대한 메타데이터와 입력 및 출력 데이터를 저장하는 공유 로컬 데이터 루트 폴더가 있습니다. 단, **Local-project** 계정은 U-SQL 스크립트를 실행할 때마다 U-SQL 프로젝트 작업 디렉터리에 임시 로컬 데이터 루트 폴더를 만듭니다. 이 임시 데이터 루트 폴더는 다시 빌드 또는 다시 실행이 발생하면 정리됩니다. 

U-SQL 프로젝트는 프로젝트 참조 및 속성을 통해 격리된 로컬 실행 환경을 관리합니다. 프로젝트 및 참조된 데이터베이스 환경 모두에서 U-SQL 스크립트에 대한 입력 데이터 원본을 구성할 수 있습니다.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Local-project 계정에 대한 입력 데이터 원본 관리 

U-SQL 프로젝트는 **Local-project** 계정에 대해 로컬 데이터 루트 폴더를 만들고 데이터를 설정합니다. 다시 빌드 및 로컬 실행이 발생할 때마다 U-SQL 프로젝트 작업 디렉터리에 임시 데이터 루트 폴더가 정리된 후 다시 생성됩니다. U-SQL 프로젝트에서 구성되는 모든 데이터 원본은 로컬 작업 실행 전에 이 임시 로컬 데이터 루트 폴더에 복사됩니다. 

데이터 원본의 루트 폴더를 구성할 수 있습니다. **U-SQL 프로젝트** > **속성** > **테스트 데이터 원본**을 마우스 오른쪽 단추로 클릭합니다. **Local-project** 계정에서 U-SQL 스크립트를 실행할 경우 **테스트 데이터 원본** 폴더의 모든 파일과 하위 폴더가 임시 로컬 데이터 루트 폴더에 복사됩니다. 하위 폴더에 있는 파일이 포함됩니다. 로컬 작업이 실행된 후, 프로젝트 작업 디렉터리의 임시 로컬 데이터 루트 폴더에서 출력 결과를 찾을 수도 있습니다. 프로젝트가 다시 빌드되고 정리되면 이 모든 출력이 삭제되고 정리됩니다. 

![프로젝트의 테스트 데이터 원본 구성](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>**Local-project** 계정에 대한 참조된 데이터베이스 환경 관리 

U-SQL 쿼리가 U-SQL 데이터베이스 개체를 사용하거나 이 개체로 쿼리를 수행하는 경우 U-SQL 스크립트를 로컬로 실행하기 전에 로컬에서 데이터베이스 환경을 사용할 수 있게 준비해야 합니다. **Local-project** 계정의 경우, U-SQL 프로젝트 참조로 U-SQL 데이터베이스 종속성을 관리할 수 있습니다. U-SQL 데이터베이스 프로젝트 참조를 U-SQL 프로젝트에 추가할 수 있습니다. **Local-project** 계정에서 U-SQL 스크립트를 실행하기 전에 참조된 모든 데이터베이스가 임시 로컬 데이터 루트 폴더에 배포됩니다. 또한 실행될 때마다 임시 데이터 루트 폴더가 새로운 격리 환경으로 정리됩니다.

다음 관련 문서를 참조하세요.
* [U-SQL 데이터베이스 프로젝트](data-lake-analytics-data-lake-tools-develop-usql-database.md)에서 U-SQL 데이터베이스 정의 및 참조를 관리하는 방법을 알아봅니다.

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>**Local-machine**과 **Local-project** 계정 간 차이점

**Local-machine** 계정은 사용자의 로컬 머신에서 Azure Data Lake Analytics 계정을 시뮬레이트합니다. 이 계정은 Azure Data Lake Analytics 계정과 동일한 환경을 공유합니다. **Local-project** 계정은 사용자에게 친숙한 개발 환경을 제공합니다. 이 환경은 사용자가 스크립트를 로컬로 실행하기 전에 데이터베이스 참조와 입력 데이터를 배포하기에 유용합니다. **Local-machine** 계정은 모든 프로젝트를 통해 액세스할 수 있는 공유 영구 환경을 제공합니다. **Local-project** 계정은 각 프로젝트에 대해 격리된 개발 환경을 제공하고 실행될 때마다 새로 고쳐집니다. **Local-project** 계정은 새 변경 내용을 빠르게 적용하여 더 빠른 개발 환경을 제공합니다.

더 많은 **Local-machine**과 **Local-project** 계정 간 차이점이 다음 표에 나와 있습니다.

|차이 관점|Local-machine|Local-project|
|----------------|---------------|---------------|
|로컬 액세스|모든 프로젝트에서 액세스할 수 있습니다.|해당 프로젝트만 이 계정에 액세스할 수 있습니다.|
|로컬 데이터 루트 폴더|영구 로컬 폴더입니다. **도구** > **Data Lake** > **옵션 및 설정**을 통해 구성됩니다.|U-SQL 프로젝트 작업 디렉터리에서 각 로컬 실행에 대해 만들어지는 임시 폴더입니다. 폴더는 다시 빌드 또는 다시 실행이 발생하면 정리됩니다.|
|U-SQL 스크립트에 대한 입력 데이터|영구 로컬 데이터 루트 폴더에 있는 상대 경로입니다.|**U-SQL 프로젝트 속성** > **테스트 데이터 원본**을 통해 설정합니다. 모든 파일 및 하위 폴더가 로컬 실행 전에 임시 데이터 루트 폴더로 복사됩니다.|
|U-SQL 스크립트에 대한 출력 데이터|영구 로컬 데이터 루트 폴더의 상대 경로입니다.|임시 데이터 루트 폴더로 출력합니다. 결과는 다시 빌드 또는 다시 실행이 발생하면 정리됩니다.|
|참조된 데이터베이스 배포|참조된 데이터베이스는 **Local-machine** 계정에 대해 실행될 경우 자동으로 배포되지 않습니다. Azure Data Lake Analytics 계정으로 제출하는 것과 동일합니다.|참조된 데이터베이스는 로컬 실행 전에 자동으로 **Local-project** 계정에 배포됩니다. 다시 빌드 또는 다시 실행이 발생하면 모든 데이터베이스 환경이 정리되고 다시 배포됩니다.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>U-SQL SDK를 사용한 로컬 실행

Visual Studio에서 U-SQL 스크립트를 로컬로 실행하고, Azure Data Lake U-SQL SDK를 사용하여 명령줄 및 프로그래밍 인터페이스로 U-SQL 스크립트를 로컬로 실행할 수 있습니다. 이러한 인터페이스를 통해 U-SQL 로컬 실행 및 테스트를 자동화할 수 있습니다.

[Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md)에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

- [Azure Data Lake Analytics에 대해 CI/CD 파이프라인을 설정하는 방법](data-lake-analytics-cicd-overview.md).
- [Azure Data Lake Analytics 코드를 테스트하는 방법](data-lake-analytics-cicd-test.md).
