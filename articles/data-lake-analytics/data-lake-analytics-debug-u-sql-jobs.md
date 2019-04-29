---
title: 실패한 Azure Data Lake U-SQL 작업에 대한 사용자 지정 C# 코드 디버깅
description: 이 문서에서는 Azure Data Lake Tools for Visual Studio를 사용하여 U-SQL의 실패한 꼭짓점을 디버그하는 방법을 설명합니다.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 5417f66696191cebadc2af9c6d634419a0eb8e5b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615331"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>실패한 U-SQL 작업에 대한 사용자 정의 C# 코드 디버그

U-SQL은 C#을 사용하는 확장성 모델을 제공합니다. U-SQL 스크립트에서는 간단하게 C# 함수를 호출하고 선언적 SQL 유사 언어를 지원하지 않는 분석 기능을 수행할 수 있습니다. U-SQL 확장성에 대한 자세한 내용은 [U-SQL 프로그래밍 기능 가이드](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf)를 참조하세요. 

실제로 모든 코드에는 디버깅이 필요하지만 제한된 로그 파일을 사용하여 클라우드에서 사용자 지정 코드를 포함하는 분산된 작업을 디버그하기 어렵습니다. [Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs)는 **실패한 꼭짓점 디버그**라는 기능을 제공합니다. 이를 통해 사용자 지정 코드에서 발생한 실패를 간단하게 디버그할 수 있습니다. U-SQL 작업에 실패하면 서비스는 오류 상태를 유지하며 해당 도구를 통해 클라우드 오류 환경을 디버깅할 로컬 컴퓨터에 다운로드할 수 있습니다. 로컬 다운로드는 모든 입력 데이터와 사용자 코드를 비롯한 전체 클라우드 환경을 캡처합니다.

다음 비디오에서는 Azure Data Lake Tools for Visual Studio의 실패한 꼭짓점 디버그를 보여 줍니다.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Visual Studio에는이 기능을 사용 하는 것에 대 한 업데이트가 필요 합니다. [Microsoft Visual C++ 2015 재배포 가능 패키지 업데이트 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) 하며 [Windows 용 유니버설 C 런타임](https://www.microsoft.com/download/details.aspx?id=50410)합니다.
>

## <a name="download-failed-vertex-to-local-machine"></a>로컬 컴퓨터에 실패한 꼭짓점 다운로드

Azure Data Lake Tools for Visual Studio에서 실패한 작업을 열면 오류 탭에 자세한 오류 메시지가 있는 노란 경고 막대를 볼 수 있습니다.

1. **다운로드** 를 클릭하여 모든 필수 리소스 및 입력 스트림을 다운로드합니다. 다운로드가 완료되지 않으면 **다시 시도**를 클릭합니다.

2. 다운로드가 완료되면 **열기**를 클릭하여 로컬 디버깅 환경을 생성합니다. 새 디버깅 솔루션이 공개될 예정이며, Visual Studio에서 기존 솔루션을 연 경우에는 디버깅하기 전에 저장했는지 확인하고 닫아야 합니다.

![Azure Data Lake Analytics U-SQL 디버그 Visual Studio 정점 다운로드](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>디버깅 환경 구성

> [!NOTE]
> 디버깅 전에 예외 설정 창(**Ctrl + Alt + E**)에서 **공용 언어 런타임 예외**를 선택했는지 확인해야 합니다.

![Azure Data Lake Analytics U-SQL 디버그 Visual Studio 설정](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

새로 시작된 Visual Studio 인스턴스에서 사용자 정의 C# 소스 코드를 찾거나 찾을 수 없습니다.

1. [솔루션에서 소스 코드를 찾을 수 있습니다.](#source-code-is-included-in-debugging-solution)

2. [솔루션에서 소스 코드를 찾을 수 없습니다.](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>소스 코드는 솔루션 디버깅에 포함됩니다.

C# 소스 코드가 캡처되는 두 가지 경우가 있습니다.

1. 사용자 코드는 코드 숨김 파일에 정의됩니다(일반적으로 U-SQL 프로젝트에서 `Script.usql.cs`라고 명명).

2. 사용자 코드는 U-SQL 애플리케이션의 C# 클래스 라이브러리 프로젝트에서 정의되고 **디버그 정보**를 포함하는 어셈블리로 등록됩니다.

소스 코드를 솔루션에 가져오는 경우 Visual Studio 디버깅 도구(조사식, 변수 등)를 사용하여 문제를 해결할 수 있습니다.

1. **F5** 키를 눌러 디버깅을 시작합니다. 코드는 예외로 인해 중지될 때까지 실행됩니다.

2. 소스 코드 파일을 열고 중단점을 설정한 후 **F5** 키를 눌러 단계별로 코드를 디버그합니다.

    ![Azure Data Lake Analytics U-SQL 디버그 예외](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>소스 코드는 솔루션 디버깅에 포함되지 않습니다.

사용자 코드가 코드 숨김 파일에 포함되어 있지 않거나 **디버그 정보**를 포함하는 어셈블리를 등록하지 않은 경우 소스 코드는 디버깅 솔루션에 자동으로 포함되지 않습니다. 이 경우에 소스 코드를 추가하는 추가 단계가 필요합니다.

1. **솔루션 'VertexDebug' > 추가 > 기존 프로젝트...** 를 마우스 오른쪽 단추로 클릭하여 어셈블리 소스 코드를 찾고 프로젝트를 디버깅 솔루션에 추가합니다.

    ![Azure Data Lake Analytics U-SQL 디버그 - 프로젝트 추가](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. **FailedVertexDebugHost** 프로젝트에 프로젝트 폴더 경로를 가져옵니다. 

3. **추가된 어셈블리 소스 코드 프로젝트 > 속성**을 마우스 오른쪽 단추로 클릭하고, 왼쪽의 **빌드** 탭을 선택하고, \bin\debug로 끝나는 복사된 경로를 **출력 > 출력 경로**에 붙여넣습니다. 최종 출력 경로가 같은 `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`합니다.

    ![Azure Data Lake Analytics U-SQL 디버그 - pdb 경로 설정](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

이러한 설정 이후에 **F5** 키 및 중단점을 사용하여 디버깅을 시작합니다. Visual Studio 디버깅 도구(조사식, 변수 등)를 사용하여 문제를 해결할 수도 있습니다.

> [!NOTE]
> 업데이트된 .pdb 파일을 생성하기 위해 코드를 수정한 후 매번 어셈블리 소스 코드 프로젝트를 다시 빌드합니다.

## <a name="resubmit-the-job"></a>작업 다시 제출

디버깅 후 프로젝트가 성공적으로 완료되면 출력 창에 다음 메시지가 표시됩니다.

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Azure Data Lake Analytics U-SQL 디버그 성공](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

실패한 작업을 다시 제출하려면:

1. 코드 숨김 솔루션이 있는 작업의 경우 C# 코드를 코드 숨김 원본 파일(대개 `Script.usql.cs`)로 복사합니다.

2. 어셈블리를 포함한 작업의 경우 디버깅 솔루션에서 어셈블리 소스 코드 프로젝트를 마우스 오른쪽 단추로 클릭하고 업데이트된 .dll 어셈블리를 Azure Data Lake 카탈로그에 등록합니다.

3. U-SQL 작업을 다시 제출합니다.

## <a name="next-steps"></a>다음 단계

- [U-SQL 프로그래밍 기능 가이드](data-lake-analytics-u-sql-programmability-guide.md)
- [Azure Data Lake Analytics 작업에 대한 U-SQL 사용자 정의 연산자를 개발합니다.](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [로컬 실행 및 Azure Data Lake U-SQL SDK를 사용하여 U-SQL 작업 테스트 및 디버그](data-lake-analytics-data-lake-tools-local-run.md)
- [비정상적인 되풀이 작업 문제를 해결하는 방법](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
