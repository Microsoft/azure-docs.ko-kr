---
title: 매핑 데이터 흐름을 사용 하 여 데이터 변환
description: 이 자습서에서는 Azure Data Factory 사용 하 여 데이터 흐름 매핑 데이터를 변환 하는 방법에 대한 단계별 지침을 제공 합니다.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: e6ca8007a96cc63b51b4f79b69029cbf0799e71c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979188"
---
# <a name="transform-data-using-mapping-data-flows"></a>매핑 데이터 흐름을 사용 하 여 데이터 변환

Azure Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.

이 자습서에서는 Azure Data Factory 사용자 인터페이스 (UX)를 사용 하 여 데이터 흐름 매핑을 사용 하 여 Azure Data Lake Storage (ADLS) Gen2 원본에서 ADLS Gen2 싱크로 데이터를 복사 하 고 변환 하는 파이프라인을 만듭니다. 이 자습서의 구성 패턴은 데이터 흐름 매핑을 사용 하 여 데이터를 변환할 때 확장 될 수 있습니다.

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 데이터 흐름 작업을 사용 하 여 파이프라인을 만듭니다.
> * 네 가지 변환으로 매핑 데이터 흐름을 작성 합니다.
> * 파이프라인 실행 테스트
> * 데이터 흐름 작업 모니터링

## <a name="prerequisites"></a>필수 조건
* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Storage 계정**. ADLS 저장소를 *원본* 및 *싱크* 데이터 저장소로 사용 합니다. 스토리지 계정이 없는 경우 [Azure Storage 계정 만들기](../storage/common/storage-account-create.md)를 참조하세요.

이 자습서에서 변형할 파일은 MoviesDB입니다 .이 파일은 [여기](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)에서 찾을 수 있습니다. GitHub에서 파일을 검색 하려면 해당 내용을 원하는 텍스트 편집기에 복사 하 여 로컬에 .csv 파일로 저장 합니다. 저장소 계정에 파일을 업로드 하려면 [Azure Portal을 사용 하 여 Blob 업로드](../storage/blobs/storage-quickstart-blobs-portal.md)를 참조 하세요. 예제는 ' sample-s t r i n s ' 라는 컨테이너를 참조 합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

이 단계에서는 데이터 팩터리를 만들고 Data Factory UX를 열어 데이터 팩터리에 파이프라인을 만듭니다.

1. **Microsoft Edge** 또는 **Google Chrome**을 엽니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저 에서만 지원 됩니다.
2. 왼쪽 메뉴에서 **리소스 만들기** > **분석** > **Data Factory**를 선택합니다.

   !["새로 만들기" 창에서 데이터 팩터리 선택](./media/doc-common-process/new-azure-data-factory-menu.png)

3. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory**를 입력합니다.

   Azure Data Factory의 이름은 *전역적으로 고유*해야 합니다. 이름 값에 대한 오류 메시지가 표시되면 데이터 팩터리에 대한 다른 이름을 입력합니다. (예: yournameADFTutorialDataFactory). Data Factory 아티팩트에 대한 명명 규칙은 [Data Factory 명명 규칙](naming-rules.md)을 참조하세요.

     ![새 data factory](./media/doc-common-process/name-not-available-error.png)
4. 데이터 팩터리를 만들 Azure **구독**을 선택합니다.
5. **리소스 그룹**에 대해 다음 단계 중 하나를 사용합니다.

    a. **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.

    b. **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다. 
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요. 
6. **버전**에서 **V2**를 선택합니다.
7. **위치**에서 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용 하는 데이터 저장소 (예: Azure Storage 및 SQL Database) 및 계산 (예: Azure HDInsight)은 다른 지역에 있을 수 있습니다.
8. **만들기**를 선택합니다.
9. 만들기가 완료되면 알림 센터에 알림이 표시됩니다. **리소스로 이동**을 선택하여 Data Factory 페이지로 이동합니다.
10. **작성 및 모니터링**을 선택하여 별도의 탭에서 Data Factory UI를 선택합니다.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>데이터 흐름 작업을 사용 하 여 파이프라인 만들기

이 단계에서는 데이터 흐름 작업을 포함 하는 파이프라인을 만듭니다.

1. **시작** 페이지에서 **파이프라인 만들기**를 선택합니다.

   ![파이프라인 만들기](./media/doc-common-process/get-started-page.png)

1. 파이프라인의 **일반** 탭에서 파이프라인 **이름** 에 **TransformMovies** 을 입력 합니다.
1. 팩터리 위쪽 막대에서의 **데이터 흐름 디버그** 슬라이더를 밉니다. 디버그 모드에서는 라이브 Spark 클러스터에 대한 변환 논리의 대화형 테스트를 수행할 수 있습니다. 데이터 흐름 클러스터는 준비 하는 데 5-7 분 정도 걸리며, 사용자가 데이터 흐름 개발을 수행할 계획인 경우 먼저 디버그를 설정 하는 것이 좋습니다. 자세한 내용은 [디버그 모드](concepts-data-flow-debug-mode.md)를 참조 하세요.

    ![데이터 흐름 작업](media/tutorial-data-flow/dataflow1.png)
1. **작업** 창에서 **이동 및 변환** 아코디언을 확장 합니다. **데이터 흐름** 활동을 창에서 파이프라인 캔버스로 끌어다 놓습니다.

    ![데이터 흐름 작업](media/tutorial-data-flow/activity1.png)
1. **데이터 흐름 추가** 팝업에서 **새 데이터 흐름 만들기** 를 선택 하 고 데이터 흐름의 이름을 **TransformMovies**로 설정 합니다. 완료되었으면 마침을 클릭합니다.

    ![데이터 흐름 작업](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>데이터 흐름 캔버스의 빌드 변환 논리

데이터 흐름을 만들면 데이터 흐름 캔버스로 자동으로 전송 됩니다. 이 단계에서는 ADLS 저장소에서 moviesDB를 사용 하는 데이터 흐름을 빌드하고 1910에서 2000로의 평균 등급을 집계 합니다. 그런 다음이 파일을 ADLS 저장소에 다시 작성 합니다.

1. 데이터 흐름 캔버스에서 **원본 추가** 상자를 클릭 하 여 원본을 추가 합니다.

    ![데이터 흐름 캔버스](media/tutorial-data-flow/dataflow2.png)
1. 원본 이름을 **MoviesDB**로 합니다. **새로 만들기를 클릭 하 여** 새 원본 데이터 집합을 만듭니다.

    ![데이터 흐름 캔버스](media/tutorial-data-flow/dataflow3.png)
1. **Azure Data Lake Storage Gen2**를 선택 합니다. 계속을 클릭합니다.

    ![데이터 세트](media/tutorial-data-flow/dataset1.png)
1. **DelimitedText**를 선택 합니다. 계속을 클릭합니다.

    ![데이터 세트](media/tutorial-data-flow/dataset2.png)
1. 데이터 집합의 이름을 **MoviesDB**로 합니다. 연결 된 서비스 드롭다운에서 **새로 만들기**를 선택 합니다.

    ![데이터 세트](media/tutorial-data-flow/dataset3.png)
1. 연결 된 서비스 만들기 화면에서 ADLS gen2 연결 된 서비스 **ADLSGen2** 의 이름을 지정 하 고 인증 방법을 지정 합니다. 그런 다음 연결 자격 증명을 입력 합니다. 이 자습서에서는 계정 키를 사용 하 여 저장소 계정에 연결 합니다. **연결 테스트** 를 클릭 하 여 자격 증명이 올바르게 입력 되었는지 확인할 수 있습니다. 완료 되 면 만들기를 클릭 합니다.

    ![연결된 서비스](media/tutorial-data-flow/ls1.png)
1. 데이터 집합 만들기 화면으로 돌아가서 파일 **경로** 필드 아래에 있는 파일을 입력 합니다. 이 자습서에서 moviesDB 파일은 컨테이너 샘플 데이터에 있습니다. 파일에 헤더가 있으므로 **첫 번째 행을 헤더로**선택 합니다. 저장소에 있는 파일에서 직접 헤더 스키마를 가져오려면 **연결/저장소에서를** 선택 합니다. 작업이 완료 되 면 확인을 클릭 합니다.

    ![데이터 세트](media/tutorial-data-flow/dataset4.png)
1. 디버그 클러스터가 시작 된 경우 원본 변환의 **데이터 미리 보기** 탭으로 이동 하 고 **새로 고침** 을 클릭 하 여 데이터의 스냅숏을 가져옵니다. 데이터 미리 보기를 사용 하 여 변환이 올바르게 구성 되었는지 확인할 수 있습니다.

    ![데이터 흐름 캔버스](media/tutorial-data-flow/dataflow4.png)
1. 데이터 흐름 캔버스의 원본 노드 옆에 있는 더하기 아이콘을 클릭 하 여 새 변환을 추가 합니다. 추가 하는 첫 번째 변환은 **필터**입니다.

    ![데이터 흐름 캔버스](media/tutorial-data-flow/dataflow5.png)
1. 필터 변환의 이름을 **Filteryears**로 합니다. **필터 설정** 옆의 식 상자를 클릭 하 여 식 작성기를 엽니다. 여기서 필터링 조건을 지정 합니다.

    ![필터링](media/tutorial-data-flow/filter1.png)
1. 데이터 흐름 식 작성기를 사용 하면 다양 한 변환에서 사용할 식을 대화형으로 작성할 수 있습니다. 식에는 기본 제공 함수, 입력 스키마의 열 및 사용자 정의 매개 변수가 포함 될 수 있습니다. 식을 작성 하는 방법에 대한 자세한 내용은 [데이터 흐름 식 작성기](concepts-data-flow-expression-builder.md)를 참조 하세요.

    이 자습서에서는 1910 년에서 2000 년 사이에 제공 된 장르 코미디의 영화를 필터링 하려고 합니다. 현재 연도는 문자열 이므로 ```toInteger()``` 함수를 사용 하 여 정수로 변환 해야 합니다. 다음 보다 크거나 같음 (> =) 및 작거나 같음 (< =) 연산자를 사용 하 여 리터럴 연도 값 1910 및 200을 비교 합니다. And (& &) 연산자를 사용 하 여 이러한 식을 결합 합니다. 식은 다음과 같이 제공 됩니다.

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    ```rlike()``` 함수를 사용 하 여 영화 열에서 ' 코미디 ' 패턴을 찾을 수 있습니다. Get과 같은 연도 비교를 사용 하 여 rlike 식을 결합 합니다.

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    디버그 클러스터가 활성 상태 이면 **새로 고침** 을 클릭 하 여 논리를 확인할 수 있습니다. 사용 된 입력에 대한 식 출력을 볼 수 있습니다. 데이터 흐름 식 언어를 사용 하 여이 논리를 수행 하는 방법에는 두 개 이상의 올바른 대답이 있습니다.

    ![필터링](media/tutorial-data-flow/filter2.png)

    **저장을** 클릭 하 고 식을 사용 하 여 작업을 완료 합니다.

1. **데이터 미리 보기** 를 가져와 필터가 제대로 작동 하는지 확인 합니다.

    ![필터링](media/tutorial-data-flow/filter3.png)
1. 추가할 다음 변환은 **Schema modifier**의 **집계** 변환입니다.

    ![집계](media/tutorial-data-flow/agg1.png)
1. 집계 변환의 이름을 **AggregateComedyRatings**로 합니다. **묶는 방법** 탭의 드롭다운에서 **year** 를 선택 하 여 해당 집계가 생성 된 연도를 기준으로 집계를 그룹화 합니다.

    ![집계](media/tutorial-data-flow/agg2.png)
1. **집계** 탭으로 이동 합니다. 왼쪽 텍스트 상자에서 집계 열의 이름을 **AverageComedyRating**로 합니다. 오른쪽 식 상자를 클릭 하 여 식 작성기를 통해 집계 식을 입력 합니다.

    ![집계](media/tutorial-data-flow/agg3.png)
1. 열 **등급**의 평균을 얻으려면 ```avg()``` 집계 함수를 사용 합니다. **등급** 은 문자열이 고 숫자 입력을 사용 ```avg()``` ```toInteger()``` 함수를 통해 값을 숫자로 변환 해야 합니다. 식은 다음과 같습니다.

    ```avg(toInteger(Rating))```

    **저장을 클릭 하 고** 완료 되 면 마침을 클릭 합니다.

    ![집계](media/tutorial-data-flow/agg4.png)
1. **데이터 미리 보기** 탭으로 이동 하 여 변환 출력을 볼 수 있습니다. 두 개의 열만 **year** 및 **AverageComedyRating**입니다.

    ![집계](media/tutorial-data-flow/agg3.png)
1. 다음으로 **대상**아래에 **싱크** 변환을 추가 하려고 합니다.

    ![sink](media/tutorial-data-flow/sink1.png)
1. 싱크 **싱크의**이름을로 합니다. **새로** 만들기를 클릭 하 여 싱크 데이터 집합을 만듭니다.

    ![sink](media/tutorial-data-flow/sink2.png)
1. **Azure Data Lake Storage Gen2**를 선택 합니다. 계속을 클릭합니다.

    ![데이터 세트](media/tutorial-data-flow/dataset1.png)
1. **DelimitedText**를 선택 합니다. 계속을 클릭합니다.

    ![데이터 세트](media/tutorial-data-flow/dataset2.png)
1. 싱크 데이터 집합의 이름을 **MoviesSink**합니다. 연결 된 서비스의 경우 6 단계에서 만든 ADLS gen2 연결 된 서비스를 선택 합니다. 데이터를 쓸 출력 폴더를 입력 합니다. 이 자습서에서는 ' sample-data ' 컨테이너의 ' output ' 폴더에 쓰고 있습니다. 폴더는 미리 존재 하지 않아도 되며 동적으로 만들 수 있습니다. **첫 번째 행을 머리글로** true로 설정 하 고 **스키마 가져오기**에 대해 **없음** 을 선택 합니다. 마침을 클릭합니다.

    ![sink](media/tutorial-data-flow/sink3.png)

이제 데이터 흐름 빌드를 완료 했습니다. 파이프라인에서 실행할 준비가 되었습니다.

## <a name="running-and-monitoring-the-data-flow"></a>데이터 흐름 실행 및 모니터링

파이프라인을 게시 하기 전에 디버그할 수 있습니다. 이 단계에서는 데이터 흐름 파이프라인의 디버그 실행을 트리거합니다. 데이터 미리 보기에서 데이터를 쓰지는 않지만 디버그 실행은 싱크 대상에 데이터를 씁니다.

1. 파이프라인 캔버스로 이동 합니다. 디버그 **를 클릭 하 여 디버그** 실행을 트리거합니다.

    ![파이프라인](media/tutorial-data-flow/pipeline1.png)
1. 데이터 흐름 작업의 파이프라인 디버그는 활성 디버그 클러스터를 사용 하지만 초기화 하는 데 적어도 1 분이 걸립니다. **출력** 탭을 통해 진행률을 추적할 수 있습니다. 성공적으로 실행 되 면 안경 아이콘을 클릭 하 여 모니터링 창을 엽니다.

    ![파이프라인](media/tutorial-data-flow/pipeline2.png)
1. 모니터링 창에서 각 변환 단계에서 소요 된 행 수와 시간을 확인할 수 있습니다.

    ![모니터링](media/tutorial-data-flow/pipeline3.png)
1. 데이터의 열 및 분할에 대한 자세한 정보를 보려면 변환을 클릭 합니다.

    ![모니터링](media/tutorial-data-flow/pipeline4.png)

이 자습서를 올바르게 수행한 경우 싱크 폴더에 83 개의 행과 2 개의 열을 작성 해야 합니다. Blob 저장소를 확인 하 여 데이터가 올바른지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서의 파이프라인은 1910에서 2000 사이의 평균 등급을 집계 하 고 ADLS에 데이터를 기록 하는 데이터 흐름을 실행 합니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 데이터 흐름 작업을 사용 하 여 파이프라인을 만듭니다.
> * 네 가지 변환으로 매핑 데이터 흐름을 작성 합니다.
> * 파이프라인 실행 테스트
> * 데이터 흐름 작업 모니터링

[데이터 흐름 식 언어](data-flow-expression-functions.md)에 대해 자세히 알아보세요.
