---
title: '빠른 시작: 샘플 레이블 지정 도구를 사용하여 양식 레이블 지정, 모델 학습 및 양식 분석 - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Form Recognizer 샘플 레이블 지정 도구를 사용하여 레이블을 양식 문서에 수동으로 지정합니다. 그런 다음, 레이블이 지정된 문서를 사용하여 사용자 지정 모델을 학습시키고, 모델을 사용하여 키/값 쌍을 추출합니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 08/25/2020
ms.author: pafarley
ms.openlocfilehash: 6118f8109f44081c797cb09a6157abaf4044965e
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89377815"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>샘플 레이블 지정 도구를 사용하여 레이블로 Form Recognizer 모델 학습

이 빠른 시작에서는 샘플 레이블 지정 도구에서 Form Recognizer REST API를 사용하여 수동으로 레이블이 지정된 데이터로 사용자 지정 모델을 학습시킵니다. 이 기능에 대한 자세한 내용은 개요의 [레이블로 학습](../overview.md#train-with-labels) 섹션을 참조하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer 리소스 만들기"  target="_blank">Form Recognizer 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
    * 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* 동일한 형식의 양식 6개 이상으로 구성된 세트. 이 데이터를 사용하여 모델을 학습시키고 양식을 테스트합니다. 이 빠른 시작에서는 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)를 사용할 수 있습니다. 표준 성능 계층 Azure Storage 계정의 Blob 스토리지 컨테이너 루트에 학습 파일을 업로드합니다.

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer 리소스 만들기

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-the-sample-labeling-tool"></a>샘플 레이블 지정 도구 설정

Docker 엔진을 사용하여 샘플 레이블 지정 도구를 실행합니다. 다음 단계에 따라 Docker 컨테이너를 설정합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.

> [!TIP]
> OCR 양식 레이블 지정 도구는 GitHub에서 오픈 소스 프로젝트로도 사용할 수 있습니다. 도구는 React + Redux를 사용하여 빌드한 TypeScript 웹 애플리케이션입니다. 자세히 알아보거나 기여하려면 [OCR 양식 레이블 지정 도구](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application) 리포지토리를 참조하세요. 온라인으로 도구를 실행하려면 [FOTT 웹 사이트](https://fott.azurewebsites.net/)로 이동합니다.   

1. 먼저 Docker를 호스트 컴퓨터에 설치합니다. 이 가이드에서는 로컬 컴퓨터를 호스트로 사용하는 방법을 보여줍니다. Azure에서 Docker 호스팅 서비스를 사용하려면 [샘플 레이블 지정 도구 배포](../deploy-label-tool.md) 방법 가이드를 참조하세요. 

   호스트 컴퓨터는 다음 하드웨어 요구 사항을 충족해야 합니다.

    | 컨테이너 | 최소 | 권장|
    |:--|:--|:--|
    |샘플 레이블 지정 도구|2개 코어, 4GB 메모리|4개 코어, 8GB 메모리|

   운영 체제에 적합한 지침에 따라 해당 머신에 Docker를 설치합니다. 
   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)





1. `docker pull` 명령을 사용하여 샘플 레이블 지정 도구 컨테이너를 가져옵니다.

    # <a name="v20"></a>[v2.0](#tab/v2-0)    
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
    # <a name="v21-preview"></a>[v2.1 미리 보기](#tab/v2-1)    
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:2.1.012970002-amd64-preview
    ```

    ---

1. 이제 `docker run`을 사용하여 컨테이너를 실행할 준비가 되었습니다.

    # <a name="v20"></a>[v2.0](#tab/v2-0)    
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```
    # <a name="v21-preview"></a>[v2.1 미리 보기](#tab/v2-1)    
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:2.1.012970002-amd64-preview eula=accept    
    ```

    --- 

   이 명령을 사용하면 웹 브라우저를 통해 샘플 레이블 지정 도구를 사용할 수 있습니다. `http://localhost:3000` 로 이동합니다.

> [!NOTE]
> Form Recognizer REST API를 사용하여 레이블을 문서에 지정하고 모델을 학습시킬 수도 있습니다. REST API를 사용하여 학습시키고 분석하려면 [REST API 및 Python을 사용하여 레이블로 학습](./python-labeled-data.md)을 참조하세요.

## <a name="set-up-input-data"></a>입력 데이터 설정

먼저 모든 학습 문서의 형식이 동일한지 확인합니다. 여러 형식의 양식이 있으면 공용 형식에 따라 하위 폴더로 구성합니다. 학습시키는 경우 API를 하위 폴더로 전달해야 합니다.

### <a name="configure-cross-domain-resource-sharing-cors"></a>CORS(도메인 간 리소스 공유) 구성

스토리지 계정에서 CORS를 사용하도록 설정합니다. Azure Portal에서 스토리지 계정을 선택하고, 왼쪽 창에서 **CORS** 탭을 클릭합니다. 아래쪽 줄에서 다음 값을 입력합니다. 그런 다음, 위쪽에서 **저장**을 클릭합니다.

* 허용된 원본 = * 
* 허용된 메서드 = \[모두 선택\]
* 허용된 헤더 = *
* 공개된 헤더 = * 
* 최대 기간 = 200

> [!div class="mx-imgBorder"]
> ![Azure Portal에서 CORS 설정](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>샘플 레이블 지정 도구에 연결

샘플 레이블 지정 도구는 원본(원본 양식이 있는 위치) 및 대상(만든 레이블 및 출력 데이터를 내보내는 위치)에 연결됩니다.

프로젝트 간에 연결을 설정하고 공유할 수 있습니다. 확장 가능한 공급자 모델을 사용하므로 새 원본/대상 공급자를 쉽게 추가할 수 있습니다.

새 연결을 만들려면 왼쪽 탐색 모음에서 **새 연결**(플러그) 아이콘을 클릭합니다.

필드에서 다음 값을 입력합니다.

* **표시 이름** - 연결 표시 이름
* **설명** - 프로젝트 설명
* **SAS URL** - Azure Blob Storage 컨테이너의 SAS(공유 액세스 서명) URL SAS URL를 검색하려면 Microsoft Azure Storage Explorer를 열고, 컨테이너를 마우스 오른쪽 단추로 클릭하고, **공유 액세스 서명 가져오기**를 선택합니다. 서비스를 사용한 후 만료 시간을 특정 시간으로 설정합니다. **읽기**, **쓰기**, **삭제**및 **목록** 권한이 선택되어 있는지 확인하고 **만들기**를 클릭합니다. 그런 다음 **URL** 섹션의 값을 복사합니다. `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 형식이어야 합니다.

:::image type="content" source="../media/label-tool/connections.png" alt-text="샘플 레이블 지정 도구의 연결 설정.":::


## <a name="create-a-new-project"></a>새 프로젝트 만들기

샘플 레이블 지정 도구에서 구성과 설정은 프로젝트에 저장됩니다. 새 프로젝트를 만들고, 필드에서 다음 값을 입력합니다.

* **표시 이름** - 프로젝트 표시 이름입니다.
* **보안 토큰** - 일부 프로젝트 설정에는 API 키 또는 다른 공유 비밀과 같은 중요한 값이 포함될 수 있습니다. 각 프로젝트는 중요한 프로젝트 설정을 암호화/암호 해독하는 데 사용할 수 있는 보안 토큰을 생성합니다. 왼쪽 탐색 모음 아래쪽에 있는 기어 아이콘을 클릭하여 애플리케이션 설정에서 보안 토큰을 찾을 수 있습니다.
* **원본 연결** - 이 프로젝트에 사용하려는 이전 단계에서 만든 Azure Blob Storage 연결입니다.
* **폴더 경로**(선택 사항) - 원본 양식이 Blob 컨테이너의 폴더에 있는 경우 여기에 폴더 이름을 지정합니다.
* **Form Recognizer 서비스 URI** - Form Recognizer 엔드포인트 URL
* **API 키** - Form Recognizer 구독 키
* **설명**(선택 사항) - 프로젝트 설명

:::image type="content" source="../media/label-tool/new-project.png" alt-text="샘플 레이블 지정 도구의 새 프로젝트 페이지.":::

## <a name="label-your-forms"></a>양식 레이블 지정

프로젝트를 만들거나 열면 주 태그 편집기 창이 열립니다. 텍스트 편집기는 다음 세 부분으로 구성되어 있습니다.

* 미리 보기 창(크기 조정 가능) - 원본 연결에서 스크롤 가능한 양식 목록을 포함합니다.
* 주 편집기 창 - 태그를 적용하는 데 사용할 수 있습니다.
* 태그 편집기 창 - 사용자가 태그에 대한 수정, 잠금, 다시 정렬 및 삭제를 수행할 수 있습니다. 

### <a name="identify-text-elements"></a>텍스트 요소 식별

왼쪽 창에서 **모든 파일에 대해 OCR 실행**을 클릭하여 각 문서에 대한 텍스트 레이아웃 정보를 가져옵니다. 레이블 지정 도구는 각 텍스트 요소 주위에 경계 상자를 그립니다.

또한 자동으로 추출된 테이블을 보여줍니다. 문서의 왼쪽에 있는 테이블/그리드 아이콘을 클릭하여 추출된 테이블을 확인합니다. 이 빠른 시작에서는 테이블 콘텐츠가 자동으로 추출되므로 테이블 콘텐츠의 레이블을 지정하지 않고 자동화된 추출을 사용합니다.

:::image type="content" source="../media/label-tool/table-extraction.png" alt-text="샘플 레이블 지정 도구의 테이블 시각화.":::

### <a name="apply-labels-to-text"></a>텍스트에 레이블 적용

다음으로, 태그(레이블)를 만들어 모델에서 인식할 텍스트 요소에 적용합니다.

# <a name="v20"></a>[v2.0](#tab/v2-0)  
1. 먼저 태그 편집기 창을 사용하여 식별할 태그를 만듭니다.
   1. **+** 를 클릭하여 새 태그를 만듭니다.
   1. 태그 이름을 입력합니다.
   1. Enter 키를 눌러 태그를 저장합니다.
1. 주 편집기에서 강조 표시된 텍스트 요소에서 단어를 선택하려면 클릭합니다.
1. 적용하려는 태그를 클릭하거나 해당 키보드 키를 누릅니다. 숫자 키는 처음 10개 태그에 대한 바로 가기 키로 할당됩니다. 태그 편집기 창에서 위쪽 및 아래쪽 화살표 아이콘을 사용하여 태그를 다시 정렬할 수 있습니다.
    > [!Tip]
    > 양식에 레이블을 지정할 때 다음 팁을 참조하세요.
    > * 선택한 텍스트 요소마다 태그를 하나만 적용할 수 있습니다.
    > * 각 태그는 페이지당 한 번만 적용할 수 있습니다. 동일한 양식에 값이 여러 번 표시되는 경우 각 인스턴스마다 서로 다른 태그를 만듭니다. 예: "invoice# 1", "invoice# 2" 등.
    > * 태그는 여러 페이지에 걸쳐 있을 수 없습니다.
    > * 양식에 나타나는 대로 값에 레이블을 지정합니다. 두 개의 다른 태그를 사용하여 값을 두 부분으로 분할하지 마십시오. 예를 들어, 주소 필드가 여러 줄에 걸쳐 있더라도 단일 태그로 레이블을 지정해야 합니다.
    > * 태그가 지정된 필드에 키를 포함하지 말고&mdash;값만 포함합니다.
    > * 테이블 데이터는 자동으로 검색되어야 하며 최종 출력 JSON 파일에서 사용이 가능합니다. 하지만 모델이 테이블 데이터를 모두 검색하지 못하면 해당 필드에 수동으로 태그를 지정할 수도 있습니다. 테이블의 각 셀에 다른 레이블로 태그를 지정합니다. 양식에 행 수가 다양한 테이블이 있는 경우, 가장 큰 테이블이 있는 하나 이상의 양식에 태그를 지정합니다.
    > * **+** 의 오른쪽에 있는 단추를 사용하여 태그를 검색하고, 이름을 바꾸고, 순서를 변경하고, 삭제합니다.
    > * 태그 자체를 삭제하지 않고 적용된 태그를 제거하려면 문서 보기에서 태그가 지정된 사각형을 선택하고 delete 키를 누릅니다.


# <a name="v21-preview"></a>[v2.1 미리 보기](#tab/v2-1) 
1. 먼저 태그 편집기 창을 사용하여 식별할 태그를 만듭니다.
   1. **+** 를 클릭하여 새 태그를 만듭니다.
   1. 태그 이름을 입력합니다.
   1. Enter 키를 눌러 태그를 저장합니다.
1. 주 편집기에서 강조 표시된 텍스트 요소에서 단어를 선택하려면 클릭합니다. _v2.1 미리 보기_에서 클릭하여 라디오 단추 및 확인란과 같은 _선택 표시_를 키 값 쌍으로 선택할 수도 있습니다. Form Recognizer는 선택 표시가 값으로 "선택됨" 또는 "선택되지 않음"인지 여부를 식별합니다.
1. 적용하려는 태그를 클릭하거나 해당 키보드 키를 누릅니다. 숫자 키는 처음 10개 태그에 대한 바로 가기 키로 할당됩니다. 태그 편집기 창에서 위쪽 및 아래쪽 화살표 아이콘을 사용하여 태그를 다시 정렬할 수 있습니다.
    > [!Tip]
    > 양식에 레이블을 지정할 때 다음 팁을 참조하세요.
    > * 선택한 텍스트 요소마다 태그를 하나만 적용할 수 있습니다.
    > * 각 태그는 페이지당 한 번만 적용할 수 있습니다. 동일한 양식에 값이 여러 번 표시되는 경우 각 인스턴스마다 서로 다른 태그를 만듭니다. 예: "invoice# 1", "invoice# 2" 등.
    > * 태그는 여러 페이지에 걸쳐 있을 수 없습니다.
    > * 양식에 나타나는 대로 값에 레이블을 지정합니다. 두 개의 다른 태그를 사용하여 값을 두 부분으로 분할하지 마십시오. 예를 들어, 주소 필드가 여러 줄에 걸쳐 있더라도 단일 태그로 레이블을 지정해야 합니다.
    > * 태그가 지정된 필드에 키를 포함하지 말고&mdash;값만 포함합니다.
    > * 테이블 데이터는 자동으로 검색되어야 하며 최종 출력 JSON 파일에서 사용이 가능합니다. 하지만 모델이 테이블 데이터를 모두 검색하지 못하면 해당 필드에 수동으로 태그를 지정할 수도 있습니다. 테이블의 각 셀에 다른 레이블로 태그를 지정합니다. 양식에 행 수가 다양한 테이블이 있는 경우, 가장 큰 테이블이 있는 하나 이상의 양식에 태그를 지정합니다.
    > * **+** 의 오른쪽에 있는 단추를 사용하여 태그를 검색하고, 이름을 바꾸고, 순서를 변경하고, 삭제합니다.
    > * 태그 자체를 삭제하지 않고 적용된 태그를 제거하려면 문서 보기에서 태그가 지정된 사각형을 선택하고 delete 키를 누릅니다.


---

:::image type="content" source="../media/label-tool/main-editor-2-1.png" alt-text="샘플 레이블 지정 도구의 주 편집기 창.":::


위의 단계에 따라 5개 이상의 양식에 레이블을 지정합니다.

### <a name="specify-tag-value-types"></a>태그 값 형식 지정

필요에 따라 각 태그의 예상 데이터 형식을 설정할 수 있습니다. 태그 오른쪽에 있는 바로 가기 메뉴를 열고 메뉴에서 형식을 선택합니다. 이 기능을 사용하면 검색 알고리즘에서 텍스트 검색 정확도를 향상시키는 특정 가정을 수행할 수 있습니다. 또한 검색된 값이 최종 JSON 출력에서 표준화된 형식으로 반환되도록 합니다. 

> [!div class="mx-imgBorder"]
> ![샘플 레이블 도구를 사용한 값 형식 선택](../media/whats-new/formre-value-type.png)

현재 지원되는 값 형식 및 변형은 다음과 같습니다.
* `string`
    * 기본값, `no-whitespaces`, `alphanumeric`
* `number`
    * 기본값, `currency`
* `date` 
    * 기본값, `dmy`, `mdy`, `ymd`
* `time`
* `integer`
* `selectionMark` – _v2.1-preview.1의 새로운 기능!_

> [!NOTE]
> 날짜 형식 지정은 다음 규칙을 참조하세요.
> 
> 다음(`, - / . \`) 문자를 DMY 날짜 구분 기호로 사용할 수 있습니다. 공백은 구분 기호로 사용할 수 없습니다. 예를 들면 다음과 같습니다.
> * 2020,01,01
> * 2020-01-01
> * 2020/01/01
>
> 일과 월은 각각 한 자리 또는 두 자리 숫자로 작성될 수 있으며 연도는 두 자리 또는 네 자리 숫자일 수 있습니다.
> * 2020-1-1
> * 20-1-01
>
> DMY 날짜 문자열에 8자리 숫자가 있는 경우 구분 기호는 선택 사항입니다.
> * 20200101
> * 2020 01 01
>
> 월은 전체 또는 짧은 이름으로 작성할 수도 있습니다. 이름을 사용하는 경우 구분 기호 문자는 선택 사항입니다.
> * 2020/Jan/01
> * 2020Jan01
> * 2020 Jan 01

## <a name="train-a-custom-model"></a>사용자 지정 모델 학습

왼쪽 창에서 학습 아이콘을 클릭하여 학습 페이지를 엽니다. 그런 다음, **학습** 단추를 클릭하여 모델 학습을 시작합니다. 학습 프로세스가 완료되면 다음 정보가 표시됩니다.

* **모델 ID** - 만들어지고 학습된 모델의 ID입니다. 각 학습 호출은 고유한 ID를 사용하여 새 모델을 만듭니다. [REST API](./curl-train-extract.md) 또는 [클라이언트 라이브러리](./client-library.md)를 통해 예측 호출을 수행하려면 이 문자열이 필요하므로 안전한 위치에 복사합니다.
* **평균 정확도** - 모델의 평균 정확도입니다. 추가 양식에 레이블을 지정하고 다시 학습하여 새 모델을 만들면 모델 정확도를 향상시킬 수 있습니다. 먼저 5개의 양식에 레이블을 지정하고, 필요에 따라 더 많은 양식을 추가하는 것이 좋습니다.
* 태그 목록 및 태그당 예상 정확도


:::image type="content" source="../media/label-tool/train-screen.png" alt-text="학습 보기.":::

학습이 완료되면 **평균 정확도** 값을 검사합니다. 낮은 경우 더 많은 입력 문서를 추가하고 위의 단계를 반복해야 합니다. 이미 레이블이 지정된 문서는 프로젝트 인덱스에서 유지됩니다.

> [!TIP]
> 학습 프로세스는 REST API 호출을 사용하여 실행할 수도 있습니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [Python을 사용하여 레이블로 학습](./python-labeled-data.md)을 참조하세요.

## <a name="compose-trained-models"></a>학습된 모델 작성

# <a name="v20"></a>[v2.0](#tab/v2-0)  

이 기능은 현재 v2.1에서 사용할 수 있습니다. 미리 보기. 

# <a name="v21-preview"></a>[v2.1 미리 보기](#tab/v2-1) 

모델 작성을 사용하면 단일 모델 ID에 최대 100개의 모델을 작성할 수 있습니다. 이 작성된 모델 ID를 사용하여 분석을 호출하면 Form Recognizer는 먼저 제출한 양식을 분류하여 가장 일치하는 모델과 일치시킨 다음, 해당 모델에 대한 결과를 반환합니다. 이는 들어오는 양식이 여러 템플릿 중 하나에 속할 수 있는 경우에 유용합니다.

샘플 레이블 지정 도구에서 모델을 작성하려면 왼쪽에 있는 모델 작성(병합 화살표) 아이콘을 클릭합니다. 왼쪽에서 함께 작성할 모델을 선택합니다. 화살표 아이콘이 있는 모델은 이미 작성된 모델입니다. "작성" 단추를 클릭합니다. 팝업에서 새로 작성된 모델의 이름을 지정하고 "작성"을 클릭합니다. 작업이 완료되면 새로 작성된 모델이 목록에 표시됩니다. 

:::image type="content" source="../media/label-tool/model-compose.png" alt-text="모델 작성 UX 뷰.":::

---

## <a name="analyze-a-form"></a>양식 분석 

왼쪽에 있는 예측(전구) 아이콘을 클릭하여 모델을 테스트합니다. 학습 프로세스에서 사용하지 않은 양식 문서를 업로드합니다. 그런 다음, 오른쪽의 **예측** 단추를 클릭하여 양식에 대한 키/값 예측을 가져옵니다. 이 도구는 태그를 경계 상자에 적용하고, 각 태그의 신뢰도를 보고합니다.

> [!TIP]
> 분석 API는 REST 호출을 사용하여 실행할 수도 있습니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [Python을 사용하여 레이블로 학습](./python-labeled-data.md)을 참조하세요.

## <a name="improve-results"></a>결과 향상

보고된 정확도에 따라 모델을 향상시키기 위해 추가 학습을 수행하는 것이 좋습니다. 예측이 완료되면 적용된 각 태그의 신뢰도 값을 검사합니다. 평균 정확도 학습 값이 높지만 신뢰도 점수가 낮거나 결과가 정확하지 않은 경우, 예측에 사용되는 파일을 학습 세트에 추가하고 레이블을 지정하고 다시 학습시켜야 합니다.

분석된 문서가 학습에 사용된 문서와 다를 경우 보고된 평균 정확도, 신뢰도 점수 및 실제 정확도가 일치하지 않을 수 있습니다. 사용자가 볼 때 일부 문서는 비슷하게 보이지만 AI 모델과는 다르게 보일 수 있습니다. 예를 들어 두 가지 변형이 있는 양식 유형을 사용하여 학습할 수 있습니다. 여기서 학습 세트는 변형 A(20%)와 변형 B(80%)로 구성됩니다. 예측하는 동안 변형 A 문서의 신뢰도 점수가 낮아질 수 있습니다.

## <a name="save-a-project-and-resume-later"></a>프로젝트 저장 및 나중에 다시 시작

다른 시간 또는 다른 브라우저에서 프로젝트를 다시 시작하려면 프로젝트의 보안 토큰을 저장하고 나중에 다시 입력해야 합니다. 

### <a name="get-project-credentials"></a>프로젝트 자격 증명 가져오기
프로젝트 설정 페이지(슬라이더 아이콘)로 이동하여 보안 토큰 이름을 기록합니다. 그런 다음, 애플리케이션 설정(기어 아이콘)으로 이동합니다. 그러면 현재 브라우저 인스턴스의 모든 보안 토큰이 표시됩니다. 프로젝트의 보안 토큰을 찾아서 해당 이름과 키 값을 안전한 위치에 복사합니다.

### <a name="restore-project-credentials"></a>프로젝트 자격 증명 복원
프로젝트를 다시 시작하려면 먼저 동일한 Blob 스토리지 컨테이너에 대한 연결을 만들어야 합니다. 위의 단계 반복하여 이 작업을 수행합니다. 그런 다음, 애플리케이션 설정 페이지(기어 아이콘)로 이동하여 프로젝트의 보안 토큰이 있는지 확인합니다. 그렇지 않으면 새 보안 토큰을 추가하고, 이전 단계의 토큰 이름과 키를 다시 복사합니다. 그런 다음, [설정 저장]을 클릭합니다. 

### <a name="resume-a-project"></a>프로젝트 다시 시작

마지막으로, 기본 페이지(집 아이콘)로 이동하여 [클라우드 프로젝트 열기]를 클릭합니다. 그런 다음, Blob 스토리지 연결을 선택하고 프로젝트의 *.fott* 파일을 선택합니다. 보안 토큰이 있으므로 애플리케이션에서 프로젝트의 모든 설정을 로드합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Form Recognizer 샘플 레이블 지정 도구를 사용하여 수동으로 레이블이 지정된 데이터로 모델을 학습하는 방법을 알아보았습니다. 레이블 지정 도구를 사용자 고유의 애플리케이션에 통합하려면 레이블이 지정된 데이터 학습을 처리하는 REST API를 사용합니다.

> [!div class="nextstepaction"]
> [Python을 사용하여 레이블로 학습](./python-labeled-data.md)
