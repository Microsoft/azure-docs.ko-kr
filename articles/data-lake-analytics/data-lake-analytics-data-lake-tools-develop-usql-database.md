---
title: U-SQL 데이터베이스 프로젝트를 사용하여 Azure Data Lake용 U-SQL 데이터베이스 개발
description: Azure Data Lake Tools for Visual Studio를 사용하여 U-SQL 데이터베이스를 개발하는 방법을 알아봅니다.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 47235fa5676acd8de8a7cc0d969b813837faf0af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628849"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>U-SQL 데이터베이스 프로젝트를 사용하여 Azure Data Lake용 U-SQL 데이터베이스 개발

U-SQL 데이터베이스는 테이블의 구조화되지 않은 데이터 및 관리되는 구조화된 데이터에 대해 구조화된 보기를 제공합니다. 또한 구조화된 데이터 및 사용자 지정 코드를 구성하기 위한 일반적인 메타데이터 카탈로그 시스템을 제공합니다. 이 데이터베이스는 이러한 관련된 개체를 함께 그룹화하는 개념입니다.

[U-SQL 데이터베이스 및 DDL(데이터 기술 언어)](/u-sql/data-definition-language-ddl-statements)에 대해 자세히 알아봅니다. 

U-SQL 데이터베이스 프로젝트는 개발자가 U-SQL 데이터베이스를 빠르고 쉽게 개발, 관리 및 배포하도록 도와주는 Visual Studio의 프로젝트 형식입니다.

## <a name="create-a-u-sql-database-project"></a>U-SQL 데이터베이스 프로젝트 만들기

Azure Data Lake Tools for Visual Studio는 버전 2.3.3000.0 이후부터 U-SQL 데이터베이스 프로젝트라고 하는 새 프로젝트 템플릿이 추가되었습니다. U-SQL 프로젝트를 만들려면 **파일 > 새로 만들기 > 프로젝트**를 선택합니다. U-SQL 데이터베이스 프로젝트는 **Azure Data Lake > U-SQL 노드**에서 찾을 수 있습니다.

![Data Lake Tools for Visual Studio--U-SQL 데이터베이스 프로젝트 생성](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>데이터베이스 프로젝트를 사용하여 U-SQL 데이터베이스 개체 개발

U-SQL 데이터베이스 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가 > 새 항목**을 선택합니다. **새 항목 추가** 마법사에서 지원되는 모든 새 개체 형식을 찾을 수 있습니다. 

비어셈블리 개체의 경우(예: 테이블 반환 함수) 새 항목을 추가한 후 새 U-SQL 스크립트가 만들어집니다. 먼저 편집기에서 해당 개체에 대한 DDL 문을 개발할 수 있습니다.

어셈블리 개체의 경우 이 도구는 어셈블리를 등록하고 DLL 파일과 기타 추가 파일을 배포하도록 도와주는 친숙한 UI 편집기를 제공합니다. 다음 단계에서는 U-SQL 데이터베이스 프로젝트에 어셈블리 개체 정의를 추가하는 방법을 보여 줍니다.

1.  UDO/UDAG/UDF를 포함하는 C# 프로젝트에 대한 참조를 U-SQL 데이터베이스 프로젝트에 추가합니다.

    ![Data Lake Tools for Visual Studio--U-SQL 데이터베이스 프로젝트 참조 추가](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Data Lake Tools for Visual Studio--U-SQL 데이터베이스 프로젝트 참조 추가](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  어셈블리 디자인 보기의 **참조에서 어셈블리 만들기** 드롭다운 메뉴에서 참조된 어셈블리를 선택합니다.

    ![Data Lake Tools for Visual Studio--참조에서 어셈블리 만들기](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  **관리되는 종속성** 및 **추가 파일**(있는 경우)을 추가합니다. 추가 파일을 추가하는 경우 이 도구는 나중에 상대 경로를 사용하여 로컬 컴퓨터 및 빌드 컴퓨터 둘 다에서 어셈블리를 찾을 수 있습니다. 

@_DeployTempDirectory는 도구가 빌드 출력 폴더를 가리키도록 하는 미리 정의된 변수입니다. 모든 어셈블리는 빌드 출력 폴더 아래에 어셈블리 이름의 하위 폴더가 있습니다. 모든 DLL 및 추가 파일이 해당 하위 폴더에 있습니다. 
 
## <a name="build-a-u-sql-database-project"></a>U-SQL 데이터베이스 프로젝트 빌드

U-SQL 데이터베이스 프로젝트에 대한 빌드 출력은 접미사 `.usqldbpack`으로 명명된 U-SQL 데이터베이스 배포 패키지입니다. `.usqldbpack` 패키지는 단일 U-SQL 스크립트의 모든 DDL 문을 **DDL** 폴더에 포함하고, 어셈블리에 대한 모든 DLL 및 추가 파일을 **Temp** 폴더에 포함하는 zip 파일입니다.

에 대해 자세히 알아보세요 [MSBuild 사용 하 여 U-SQL 데이터베이스 프로젝트를 빌드하려면 명령줄 Azure DevOps Services 빌드 및 작업 방식](data-lake-analytics-cicd-overview.md)합니다.

## <a name="deploy-a-u-sql-database"></a>U-SQL 데이터베이스 배포

.usqldbpack 패키지는 Visual Studio 또는 배포 SDK를 사용하여 로컬 계정 또는 Azure Data Lake Analytics 계정 둘 다에 배포될 수 있습니다. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Visual Studio에서 U-SQL 데이터베이스 배포

Visual Studio에서 U-SQL 데이터베이스 프로젝트 또는 .usqldbpack 패키지를 통해 U-SQL 데이터베이스를 배포할 수 있습니다.

#### <a name="deploy-through-a-u-sql-database-project"></a>U-SQL 데이터베이스 프로젝트를 통해 배포

1.  U-SQL 데이터베이스 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **배포**를 선택합니다.
2.  **U-SQL 데이터베이스 배포 마법사**에서 데이터베이스를 배포하려는 **ADLA 계정**을 선택합니다. 로컬 계정과 ADLA 계정이 둘 다 지원됩니다.
3.  **데이터베이스 원본**은 자동으로 채워지며, 프로젝트의 빌드 출력 폴더에 있는 .usqldbpack 패키지를 가리킵니다.
4.  **데이터베이스 이름**에 이름을 입력하여 데이터베이스를 만듭니다. 같은 이름의 데이터베이스가 대상 Azure Data Lake Analytics 계정에 이미 있는 경우 데이터베이스 프로젝트에 정의된 모든 개체는 데이터베이스를 다시 만들지 않고도 생성됩니다.
5.  U-SQL 데이터베이스를 배포하려면 **제출**을 선택합니다. 모든 리소스(어셈블리 및 추가 파일)가 업로드되고 모든 DDL 문을 포함하는 U-SQL 작업이 제출됩니다.

    ![Data Lake Tools for Visual Studio--U-SQL 데이터베이스 프로젝트 배포](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Data Lake Tools for Visual Studio--U-SQL 데이터베이스 프로젝트 배포 마법사](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>U-SQL 데이터베이스 배포 패키지를 통해 배포

1.  **서버 탐색기**를 엽니다. 그런 다음, 데이터베이스를 배포할 **Azure Data Lake Analytics 계정**을 확장합니다.
2.  **U-SQL 데이터베이스**를 마우스 오른쪽 단추로 클릭하고 **데이터베이스 배포**를 선택합니다.
3.  **데이터베이스 원본**을 U-SQL 데이터베이스 배포 패키지(.usqldbpack 파일) 경로로 설정합니다.
4.  **데이터베이스 이름**을 입력하여 데이터베이스를 만듭니다. 같은 이름의 데이터베이스가 대상 Azure Data Lake Analytics 계정에 이미 있는 경우 데이터베이스 프로젝트에 정의된 모든 개체는 데이터베이스를 다시 만들지 않고도 생성됩니다.

    ![Data Lake Tools for Visual Studio--U-SQL 데이터베이스 패키지 배포](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Data Lake Tools for Visual Studio--U-SQL 데이터베이스 패키지 배포 마법사](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>SDK를 사용하여 U-SQL 데이터베이스 배포

`PackageDeploymentTool.exe`는 U-SQL 데이터베이스를 배포하는 데 도움이 되는 프로그래밍 및 명령줄 인터페이스를 제공합니다. 이 SDK는 `build/runtime/PackageDeploymentTool.exe`에 있는 [U-SQL SDK Nuget 패키지](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)에 포함되어 있습니다.

[SDK 및 U-SQL 데이터베이스 배포에 대해 CI/CD 파이프라인을 설정하는 방법에 대해 자세히 알아봅니다](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>U-SQL 데이터베이스 프로젝트 참조

U-SQL 프로젝트는 U-SQL 데이터베이스 프로젝트를 참조할 수 있습니다. 이러한 참조는 다음 두 워크로드를 영향을 줍니다.

- *프로젝트를 빌드하지*: U-SQL 스크립트를 작성 하기 전에 참조 된 데이터베이스 환경을 설정 합니다. 
- *로컬 (로컬-프로젝트를)에 대 한 실행된 계정*: 참조 된 데이터베이스 환경 (로컬-프로젝트를)에 배포 된 U-SQL 스크립트를 실행 하기 전에 계정. [여기에서 로컬 실행과 (local-machine) 및 (local-project) 계정 간 차이점에 대해 자세히 알아봅니다](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>U-SQL 데이터베이스 참조를 추가하는 방법

1. **솔루션 탐색기**에서 U-SQL 프로젝트를 마우스 오른쪽 단추로 클릭하고 **U-SQL 데이터베이스 참조 추가...** 를 선택합니다.

    ![Data Lake Tools for Visual Studio -- 데이터베이스 프로젝트 참조 추가](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. 현재 솔루션 또는 U-SQL 데이터베이스 패키지 파일에 있는 U-SQL 데이터베이스 프로젝트에서 데이터베이스 참조를 구성합니다.
3. 데이터베이스의 이름을 제공합니다.

    ![Data Lake Tools for Visual Studio의 데이터베이스 프로젝트 참조 추가 마법사](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>다음 단계

- [Azure Data Lake Analytics에 대해 CI/CD 파이프라인을 설정하는 방법](data-lake-analytics-cicd-overview.md)
- [Azure Data Lake Analytics 코드를 테스트하는 방법](data-lake-analytics-cicd-test.md)
- [로컬 컴퓨터에서 U-SQL 스크립트 실행](data-lake-analytics-data-lake-tools-local-run.md)
