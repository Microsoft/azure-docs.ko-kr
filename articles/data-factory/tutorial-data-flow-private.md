---
title: Azure Data Factory 관리 되는 가상 네트워크 매핑 데이터 흐름을 사용 하 여 데이터 변환
description: 이 자습서에서는 Azure Data Factory 사용 하 여 데이터 흐름이 매핑되는 데이터를 변환 하는 방법에 대 한 단계별 지침을 제공 합니다.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/19/2019
ms.openlocfilehash: d752b747a0156bcef587f81ee421c55a6de81e17
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079475"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>매핑 데이터 흐름을 사용 하 여 데이터를 안전 하 게 변환

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](https://docs.microsoft.com/azure/data-factory/introduction)를 참조하세요.

이 자습서에서는 Data Factory UI (사용자 인터페이스)를 사용 하 여 Azure Data Lake Storage Gen2 원본에서 Data Lake Storage Gen2 싱크로 데이터를 복사 하 고 변환 하는 파이프라인을 만듭니다 .이 파이프라인을 사용 하 여 [Data Factory 관리 Virtual Network](managed-virtual-network-private-endpoint.md)의 매핑 데이터 흐름을 통해 *선택한 네트워크에만 액세스할 수* 있습니다. 매핑 데이터 흐름을 사용 하 여 데이터를 변환 하는 경우이 자습서의 구성 패턴을 확장할 수 있습니다.

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
>
> * 데이터 팩터리를 만듭니다.
> * 데이터 흐름 작업을 사용 하 여 파이프라인을 만듭니다.
> * 네 가지 변환으로 매핑 데이터 흐름을 작성 합니다.
> * 파이프라인 실행 테스트
> * 데이터 흐름 작업을 모니터링 합니다.

## <a name="prerequisites"></a>필수 구성 요소
* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Storage 계정**. Data Lake Storage를 *원본* 및 *싱크* 데이터 저장소로 사용 합니다. 스토리지 계정이 없는 경우 [Azure Storage 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)를 참조하세요. *저장소 계정에서 선택한 네트워크 에서만 액세스할 수 있도록 합니다.* 

이 자습서에서 변형할 파일은이 [GitHub 콘텐츠 사이트](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)에서 찾을 수 있는 moviesDB.csv입니다. GitHub에서 파일을 검색 하려면 콘텐츠를 원하는 텍스트 편집기에 복사 하 여 로컬에 .csv 파일로 저장 합니다. 저장소 계정에 파일을 업로드 하려면 [Azure Portal을 사용 하 여 Blob 업로드](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)를 참조 하세요. 예제는 **샘플 데이터**라는 컨테이너를 참조 합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

이 단계에서는 데이터 팩터리를 만들고 Data Factory UI를 열어 데이터 팩터리에 파이프라인을 만듭니다.

1. Microsoft Edge 또는 Google Chrome을 엽니다. 현재 Microsoft Edge 및 Google Chrome 웹 브라우저만 Data Factory UI를 지원 합니다.
1. 왼쪽 메뉴에서 **리소스 만들기** > **분석** > **Data Factory**를 차례로 선택합니다.
1. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory**를 입력합니다.

   데이터 팩터리 이름은 *전역적으로 고유*해야 합니다. 이름 값에 대 한 오류 메시지가 표시 되 면 데이터 팩터리에 다른 이름 (예: yournameADFTutorialDataFactory)을 입력 합니다. Data Factory 아티팩트에 대한 명명 규칙은 [Data Factory 명명 규칙](naming-rules.md)을 참조하세요.

1. 데이터 팩터리를 만들 Azure **구독**을 선택합니다.
1. **리소스 그룹**에 대해 다음 단계 중 하나를 사용합니다.

    * **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.
    * **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다. 
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요. 
1. **버전**에서 **V2**를 선택합니다.
1. **위치**에서 데이터 팩터리의 위치를 선택합니다. 지원 되는 위치만 드롭다운 목록에 표시 됩니다. 데이터 팩터리에서 사용 하는 데이터 저장소 (예: Azure Storage 및 Azure SQL Database) 및 계산 (예: Azure HDInsight)은 다른 지역에 있을 수 있습니다.

1. **만들기**를 선택합니다.
1. 만들기가 완료 되 면 알림 센터에 알림이 표시 됩니다. **리소스로 이동** 을 선택 하 여 **Data Factory** 페이지로 이동 합니다.
1. **작성 및 모니터링**을 선택하여 별도의 탭에서 Data Factory UI를 선택합니다.

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Data Factory 관리 되는 Virtual Network에서 Azure IR 만들기
이 단계에서는 Azure IR 만들고 관리 되는 Data Factory Virtual Network를 사용 하도록 설정 합니다.

1. Data Factory 포털에서 **관리**로 이동 하 고 **새로** 만들기를 선택 하 여 새 Azure IR을 만듭니다.

   ![새 Azure IR을 만드는 과정을 보여 주는 스크린샷](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. **Azure** IR 옵션을 선택 합니다.

   ![새 Azure IR을 보여 주는 스크린샷](./media/tutorial-copy-data-portal-private/azure-ir.png)

1. **Virtual network 구성 (미리 보기)** 에서 **사용**을 선택 합니다.

   ![새 Azure IR을 사용 하도록 설정 하는 것을 보여 주는 스크린샷](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)

1. **만들기**를 선택합니다.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>데이터 흐름 작업을 사용 하 여 파이프라인 만들기

이 단계에서는 데이터 흐름 작업을 포함 하는 파이프라인을 만듭니다.

1. **시작** 페이지에서 **파이프라인 만들기**를 선택합니다.

   ![파이프라인을 만드는 과정을 보여 주는 스크린샷](./media/doc-common-process/get-started-page.png)

1. 파이프라인의 속성 창에서 파이프라인 이름에 **TransformMovies** 를 입력 합니다.
1. 팩터리 위쪽 막대에서의 **데이터 흐름 디버그** 슬라이더를 밉니다. 디버그 모드에서는 라이브 Spark 클러스터에 대 한 변환 논리의 대화형 테스트를 수행할 수 있습니다. 데이터 흐름 클러스터는 준비 하는 데 5 ~ 7 분 정도 걸립니다. 데이터 흐름 개발을 수행 하려는 경우 먼저 **데이터 흐름 디버그** 를 설정 합니다. 자세한 내용은 [디버그 모드](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-debug-mode)를 참조 하세요.

    ![데이터 흐름 디버그 슬라이더를 보여 주는 스크린샷](media/tutorial-data-flow-private/dataflow-debug.png)
1. **작업** 창에서 **이동 및 변환**을 확장 합니다. 창에서 파이프라인 캔버스로 **데이터 흐름** 활동을 끌어 옵니다.

1. **데이터 흐름 추가** 팝업에서 **새 데이터 흐름 만들기** 를 선택 하 고 **데이터 흐름 매핑**을 선택 합니다. 완료 되 면 **확인을** 선택 합니다.

    ![매핑 데이터 흐름을 보여 주는 스크린샷](media/tutorial-data-flow-private/mapping-dataflow.png)

1. 속성 창에서 데이터 흐름의 이름을 **TransformMovies** 로 합니다.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>데이터 흐름 캔버스의 빌드 변환 논리

데이터 흐름을 만든 후에는 데이터 흐름 캔버스로 자동으로 전송 됩니다. 이 단계에서는 Data Lake Storage에서 moviesDB.csv 파일을 사용 하 고 1910에서 2000로의 평균 등급을 집계 하는 데이터 흐름을 작성 합니다. 그런 다음이 파일을 Data Lake Storage에 다시 작성 합니다.

### <a name="add-the-source-transformation"></a>원본 변환 추가

이 단계에서는 Data Lake Storage Gen2를 원본으로 설정 합니다.

1. 데이터 흐름 캔버스에서 **원본 추가** 상자를 선택 하 여 원본을 추가 합니다.

1. 원본 이름을 **MoviesDB**로 합니다. 새로 **만들기를 선택 하** 여 새 원본 데이터 집합을 만듭니다.

1. **Azure Data Lake Storage Gen2**를 선택 하 고 **계속**을 선택 합니다.

1. **DelimitedText**을 선택 하 고 **계속**을 선택 합니다.

1. 데이터 집합의 이름을 **MoviesDB**로 합니다. 연결 된 서비스 드롭다운에서 **새로 만들기**를 선택 합니다.

1. 연결 된 서비스 만들기 화면에서 Data Lake Storage Gen2 연결 된 서비스 **ADLSGen2** 의 이름을 지정 하 고 인증 방법을 지정 합니다. 그런 다음 연결 자격 증명을 입력 합니다. 이 자습서에서는 **계정 키** 를 사용 하 여 저장소 계정에 연결 합니다. 

1. **대화형 제작**을 사용 하도록 설정 해야 합니다. 사용 하도록 설정 하는 데 1 분 정도 걸릴 수 있습니다.

    ![대화형 제작을 보여 주는 스크린샷](./media/tutorial-data-flow-private/interactive-authoring.png)

1. **연결 테스트**를 클릭합니다. 저장소 계정에 대 한 액세스를 허용 하지 않으므로 개인 끝점을 만들고 승인 하지 않아도 됩니다. 오류 메시지에는 관리형 프라이빗 엔드포인트를 만들기 위해 따를 수 있는 프라이빗 엔드포인트 만들기에 대한 링크가 표시됩니다. 대신 **관리** 탭으로 직접 이동 하 여 [이 섹션](#create-a-managed-private-endpoint) 의 지침에 따라 관리 되는 개인 끝점을 만들 수 있습니다.

1. 대화 상자를 열어 두고 저장소 계정으로 이동 합니다.

1. [이 섹션](#approval-of-a-private-link-in-a-storage-account)의 지침에 따라 프라이빗 링크를 승인합니다.

1. 대화 상자로 돌아갑니다. **연결 테스트** 를 다시 선택 하 고 **만들기** 를 선택 하 여 연결 된 서비스를 배포 합니다.

1. 데이터 집합 만들기 화면에서 파일 **경로** 필드 아래에 있는 파일을 입력 합니다. 이 자습서에서 파일 moviesDB.csv는 컨테이너 **샘플 데이터**에 있습니다. 파일에 머리글이 있으므로 **첫 번째 행을 헤더로** 선택 합니다. 확인란을 선택 합니다. 저장소에 있는 파일에서 직접 헤더 스키마를 가져오려면 **연결/저장소에서를** 선택 합니다. 완료 되 면 **확인을** 선택 합니다.

    ![원본 경로를 보여 주는 스크린샷](media/tutorial-data-flow-private/source-file-path.png)

1. 디버그 클러스터가 시작 된 경우 원본 변환의 **데이터 미리 보기** 탭으로 이동 하 고 **새로 고침** 을 선택 하 여 데이터의 스냅숏을 가져옵니다. 데이터 미리 보기를 사용 하 여 변환이 올바르게 구성 되었는지 확인할 수 있습니다.

    ![데이터 미리 보기 탭을 보여 주는 스크린샷](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>관리형 프라이빗 엔드포인트 만들기

이전 연결을 테스트할 때 하이퍼링크를 사용 하지 않은 경우 경로를 따릅니다. 이제 만든 연결 된 서비스에 연결할 관리 되는 개인 끝점을 만들어야 합니다.

1. **관리** 탭으로 이동 합니다.

   > [!NOTE]
   > 모든 Data Factory 인스턴스에 대해 **관리** 탭을 사용 하지 못할 수 있습니다. 표시 되지 않는 경우 **작성자**  >  **연결**  >  **개인 끝점**을 선택 하 여 개인 끝점에 액세스할 수 있습니다.

1. **관리 되는 전용 끝점** 섹션으로 이동 합니다.
1. **관리 되는 개인 끝점**에서 **+ 새로 만들기** 를 선택 합니다.

    ![관리 되는 전용 끝점 새로 만들기 단추를 보여 주는 스크린샷](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. 목록에서 **Azure Data Lake Storage Gen2** 타일을 선택 하 고 **계속**을 선택 합니다.
1. 만든 스토리지 계정의 이름을 입력합니다.
1. **만들기**를 선택합니다.
1. 몇 초 후에 만들어진 개인 링크에 승인이 필요 합니다.
1. 만든 개인 끝점을 선택 합니다. 저장소 계정 수준에서 개인 끝점을 승인 하도록 안내 하는 하이퍼링크를 볼 수 있습니다.

    ![개인 끝점 관리 창을 보여 주는 스크린샷](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>저장소 계정의 개인 링크 승인

1. 저장소 계정의 **설정** 섹션에서 **개인 끝점 연결** 로 이동 합니다.

1. 만든 개인 끝점의 확인란을 선택 하 고 **승인**을 선택 합니다.

    ![개인 끝점 승인 단추를 보여 주는 스크린샷](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. 설명을 추가 하 고 **예**를 선택 합니다.
1. Data Factory **관리** 탭의 **관리 되는 개인 끝점** 섹션으로 돌아갑니다.
1. 약 1 분 후에 개인 끝점에 대 한 승인이 표시 되는 것을 볼 수 있습니다.

### <a name="add-the-filter-transformation"></a>필터 변환 추가

1. 데이터 흐름 캔버스의 원본 노드 옆에 있는 더하기 아이콘을 선택 하 여 새 변환을 추가 합니다. 추가 하는 첫 번째 변환은 **필터**입니다.

    ![필터를 추가 하는 방법을 보여 주는 스크린샷](media/tutorial-data-flow-private/add-filter.png)
1. 필터 변환의 이름을 **Filteryears**로 합니다. **필터 켜기** 옆의 식 상자를 선택 하 여 식 작성기를 엽니다. 여기서 필터링 조건을 지정 합니다.

    ![FilterYears를 표시 하는 스크린샷](media/tutorial-data-flow-private/filter-years.png)
1. 데이터 흐름 식 작성기를 사용 하면 다양 한 변환에서 사용할 식을 대화형으로 작성할 수 있습니다. 식에는 기본 제공 함수, 입력 스키마의 열 및 사용자 정의 매개 변수가 포함 될 수 있습니다. 식을 작성 하는 방법에 대 한 자세한 내용은 [데이터 흐름 식 작성기](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder)를 참조 하세요.

    * 이 자습서에서는 1910 년과 2000 년 사이에 코미디 장르에서 영화를 필터링 하려고 합니다. 현재 연도는 문자열 이므로 함수를 사용 하 여 정수로 변환 해야 ```toInteger()``` 합니다. 크거나 같음 (>=) 및 작거나 같음 (<=) 연산자를 사용 하 여 리터럴 연도 값 1910 및 2000을 비교 합니다. 이러한 식을 and (&&) 연산자와 결합 합니다. 식은 다음과 같이 제공 됩니다.

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Comefa영화를 찾으려면 함수를 사용 하 여 ```rlike()``` 장르 열에서 ' 코미디 ' 패턴을 찾을 수 있습니다. Get과 같은 연도 비교를 사용 하 여 rlike 식을 결합 합니다.

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * 디버그 클러스터가 활성 상태 이면 **새로 고침** 을 선택 하 여 논리를 확인 하 고 사용 된 입력과 비교 하 여 식 출력을 볼 수 있습니다. 데이터 흐름 식 언어를 사용 하 여이 논리를 수행 하는 방법에는 두 개 이상의 올바른 대답이 있습니다.

        ![필터 식을 보여 주는 스크린샷](media/tutorial-data-flow-private/filter-expression.png)

    * 식 사용을 완료 한 후 **저장을 선택 하 고 마침을** 선택 합니다.

1. **데이터 미리 보기** 를 가져와 필터가 제대로 작동 하는지 확인 합니다.

    ![필터링 된 데이터 미리 보기를 보여 주는 스크린샷](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>집계 변환 추가

1. 추가할 다음 변환은 **Schema modifier**의 **집계** 변환입니다.

    ![집계를 추가 하는 방법을 보여 주는 스크린샷](media/tutorial-data-flow-private/add-aggregate.png)
1. 집계 변환의 이름을 **AggregateComedyRating**로 합니다. **그룹화 기준** 탭의 드롭다운 상자에서 **연도** 를 선택 하 여 해당 집계가 생성 된 연도를 기준으로 집계를 그룹화 합니다.

    ![집계 그룹을 보여 주는 스크린샷](media/tutorial-data-flow-private/group-by-year.png)
1. **집계** 탭으로 이동 합니다. 왼쪽 텍스트 상자에서 집계 열의 이름을 **AverageComedyRating**로 합니다. 오른쪽 식 상자를 선택 하 여 식 작성기를 통해 집계 식을 입력 합니다.

    ![집계 열 이름을 보여 주는 스크린샷](media/tutorial-data-flow-private/name-column.png)
1. 열 **등급**의 평균을 얻으려면 집계 함수를 사용 합니다 ```avg()``` . **등급** 은 문자열이 고 숫자 입력을 사용 하기 때문에 ```avg()``` 함수를 통해 값을 숫자로 변환 해야 합니다 ```toInteger()``` . 이 식은 다음과 같습니다.

    ```avg(toInteger(Rating))```

1. 완료 되 면 **저장을 선택 하 고 마침** 을 선택 합니다.

    ![집계를 저장 하는 방법을 보여 주는 스크린샷](media/tutorial-data-flow-private/save-aggregate.png)
1. **데이터 미리 보기** 탭으로 이동 하 여 변환 출력을 볼 수 있습니다. 두 개의 열만 **year** 및 **AverageComedyRating**입니다.

### <a name="add-the-sink-transformation"></a>싱크 변환 추가

1. 다음으로 **대상**아래에 **싱크** 변환을 추가 하려고 합니다.

    ![싱크를 추가 하는 방법을 보여 주는 스크린샷](media/tutorial-data-flow-private/add-sink.png)
1. 싱크 **싱크의**이름을로 합니다. **새로** 만들기를 선택 하 여 싱크 데이터 집합을 만듭니다.

    ![싱크를 만드는 과정을 보여 주는 스크린샷](media/tutorial-data-flow-private/create-sink.png)
1. **새 데이터 집합** 페이지에서 **Azure Data Lake Storage Gen2** 를 선택한 다음 **계속**을 선택 합니다.

1. **형식 선택** 페이지에서 **DelimitedText** 를 선택 하 고 **계속**을 선택 합니다.

1. 싱크 데이터 집합의 이름을 **MoviesSink**합니다. 연결 된 서비스에 대해 원본 변환을 위해 만든 것과 동일한 **ADLSGen2** 연결 된 서비스를 선택 합니다. 데이터를 쓸 출력 폴더를 입력 합니다. 이 자습서에서는 컨테이너 **샘플 데이터**의 폴더 **출력** 에 쓰고 있습니다. 폴더는 미리 존재 하지 않아도 되며 동적으로 만들 수 있습니다. **첫 번째 행을 머리글로** 선택 확인란을 선택 하 고 **스키마 가져오기**에 대해 **없음** 을 선택 합니다. **확인**을 선택합니다.

    ![싱크 경로를 표시 하는 스크린샷](media/tutorial-data-flow-private/sink-file-path.png)

이제 데이터 흐름 빌드를 완료 했습니다. 파이프라인에서 실행할 준비가 되었습니다.

## <a name="run-and-monitor-the-data-flow"></a>데이터 흐름 실행 및 모니터링

파이프라인을 게시 하기 전에 디버그할 수 있습니다. 이 단계에서는 데이터 흐름 파이프라인의 디버그 실행을 트리거합니다. 데이터 미리 보기에서 데이터를 쓰지는 않지만 디버그 실행은 싱크 대상에 데이터를 씁니다.

1. 파이프라인 캔버스로 이동 합니다. 디버그 **를 선택 하 여 디버그** 실행을 트리거합니다.

1. 데이터 흐름 작업의 파이프라인 디버깅은 활성 디버그 클러스터를 사용 하지만 초기화 하는 데 적어도 1 분이 걸립니다. **출력** 탭을 통해 진행률을 추적할 수 있습니다. 실행이 완료 되 면 실행 정보에 대 한 안경 아이콘을 선택 합니다.

1. 세부 정보 페이지에서 각 변환 단계에서 소요 된 시간 및 행 수를 확인할 수 있습니다.

    ![모니터링 실행을 보여 주는 스크린샷](media/tutorial-data-flow-private/run-details.png)
1. 데이터의 열 및 분할에 대 한 자세한 정보를 보려면 변환을 선택 합니다.

이 자습서를 올바르게 수행한 경우 싱크 폴더에 83 개의 행과 2 개의 열을 작성 해야 합니다. Blob 저장소를 확인 하 여 데이터가 올바른지 확인할 수 있습니다.

## <a name="summary"></a>요약

이 자습서에서는 Data Factory UI를 사용 하 여 Data Lake Storage Gen2 원본에서 Data Lake Storage Gen2 싱크로 데이터를 복사 하 고 변환 하는 파이프라인을 만듭니다 .이 파이프라인을 사용 하 여 [Data Factory 관리 Virtual Network](managed-virtual-network-private-endpoint.md)의 매핑 데이터 흐름을 사용 하 여 선택한 네트워크에만 액세스를 허용 합니다.
