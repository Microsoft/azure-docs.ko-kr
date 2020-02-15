---
title: '빠른 시작: 샘플 레이블 지정 도구를 사용하여 양식 레이블 지정, 모델 학습 및 양식 분석 - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Form Recognizer 샘플 레이블 지정 도구를 사용하여 레이블을 양식 문서에 수동으로 지정합니다. 그런 다음, 레이블이 지정된 문서를 사용하여 사용자 지정 모델을 학습시키고, 모델을 사용하여 키/값 쌍을 추출합니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 8ab673c1a268f5ab663e8f423dd9b60cdfde14ab
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118381"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>샘플 레이블 지정 도구를 사용하여 레이블로 Form Recognizer 모델 학습

이 빠른 시작에서는 샘플 레이블 지정 도구에서 Form Recognizer REST API를 사용하여 수동으로 레이블이 지정된 데이터로 사용자 지정 모델을 학습시킵니다. 이 기능에 대한 자세한 내용은 개요의 [레이블로 학습](../overview.md#train-with-labels) 섹션을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.

- 동일한 형식의 양식 6개 이상으로 구성된 세트. 이 데이터를 사용하여 모델을 학습시키고 양식을 테스트합니다. 이 빠른 시작에서는 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)를 사용할 수 있습니다. Azure Storage 계정의 Blob 스토리지 컨테이너 루트에 학습 파일을 업로드합니다.

## <a name="set-up-the-sample-labeling-tool"></a>샘플 레이블 지정 도구 설정

Docker 엔진을 사용하여 샘플 레이블 지정 도구를 실행합니다. 다음 단계에 따라 Docker 컨테이너를 설정합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.
1. 먼저 Docker를 호스트 컴퓨터에 설치합니다. 호스트 컴퓨터는 로컬 컴퓨터([Windows](https://docs.docker.com/docker-for-windows/), [MacOS](https://docs.docker.com/docker-for-mac/) 또는 [Linux](https://docs.docker.com/install/))일 수 있습니다. 또는 Azure에서 [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/index), [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/index) 또는 [Azure Stack에 배포된](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy?view=azs-1910) Kubernetes 클러스터와 같은 Docker 호스팅 서비스를 사용할 수 있습니다. 호스트 컴퓨터는 다음 하드웨어 요구 사항을 충족해야 합니다.

    | 컨테이너 | 최소 | 권장|
    |:--|:--|:--|
    |샘플 레이블 지정 도구|2개 코어, 4GB 메모리|4개 코어, 8GB 메모리|
    
1. `docker pull` 명령을 사용하여 샘플 레이블 지정 도구 컨테이너를 가져옵니다.
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
1. 이제 `docker run`을 사용하여 컨테이너를 실행할 준비가 되었습니다.
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```

   이 명령을 사용하면 웹 브라우저를 통해 샘플 레이블 지정 도구를 사용할 수 있습니다. [http://localhost:3000](http://localhost:3000)로 이동합니다.

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

![샘플 레이블 지정 도구의 연결 설정](../media/label-tool/connections.png)

## <a name="create-a-new-project"></a>새 프로젝트 만들기

샘플 레이블 지정 도구에서 구성과 설정은 프로젝트에 저장됩니다. 새 프로젝트를 만들고, 필드에서 다음 값을 입력합니다.

* **표시 이름** - 프로젝트 표시 이름입니다.
* **보안 토큰** - 일부 프로젝트 설정에는 API 키 또는 다른 공유 비밀과 같은 중요한 값이 포함될 수 있습니다. 각 프로젝트는 중요한 프로젝트 설정을 암호화/암호 해독하는 데 사용할 수 있는 보안 토큰을 생성합니다. 보안 토큰은 애플리케이션 설정에서 왼쪽 탐색 모음의 아래쪽 모서리에 있는 기어 아이콘을 클릭하여 찾을 수 있습니다.
* **원본 연결** - 이 프로젝트에 사용하려는 이전 단계에서 만든 Azure Blob Storage 연결입니다.
* **폴더 경로**(선택 사항) - 원본 양식이 Blob 컨테이너의 폴더에 있는 경우 여기에 폴더 이름을 지정합니다.
* **Form Recognizer 서비스 URI** - Form Recognizer 엔드포인트 URL
* **API 키** - Form Recognizer 구독 키
* **설명**(선택 사항) - 프로젝트 설명

![샘플 레이블 지정 도구의 새 프로젝트 페이지](../media/label-tool/new-project.png)

## <a name="label-your-forms"></a>양식 레이블 지정

프로젝트를 만들거나 열면 주 태그 편집기 창이 열립니다. 텍스트 편집기는 다음 세 부분으로 구성되어 있습니다.

* 미리 보기 창(크기 조정 가능) - 원본 연결에서 스크롤 가능한 양식 목록을 포함합니다.
* 주 편집기 창 - 태그를 적용하는 데 사용할 수 있습니다.
* 태그 편집기 창 - 사용자가 태그에 대한 수정, 잠금, 다시 정렬 및 삭제를 수행할 수 있습니다. 

### <a name="identify-text-elements"></a>텍스트 요소 식별

왼쪽 창에서 **모든 파일에 대해 OCR 실행**을 클릭하여 각 문서에 대한 텍스트 레이아웃 정보를 가져옵니다. 레이블 지정 도구는 각 텍스트 요소 주위에 경계 상자를 그립니다.

### <a name="apply-labels-to-text"></a>텍스트에 레이블 적용

다음으로, 레이블을 만들어 모델에서 인식할 텍스트 요소에 적용합니다.

1. 먼저 태그 편집기 창을 사용하여 식별할 태그(레이블)를 만듭니다.
1. 주 편집기에서 클릭하고 끌어서 강조 표시된 텍스트 요소에서 하나 이상의 단어를 선택합니다.

    > [!NOTE]
    > 현재 여러 페이지에 걸쳐 있는 텍스트는 선택할 수 없습니다.
1. 적용하려는 태그를 클릭하거나 해당 키보드 키를 누릅니다. 선택한 각 텍스트 요소에는 하나의 태그만 적용할 수 있으며, 각 태그는 페이지마다 한 번만 적용할 수 있습니다.

    > [!TIP]
    > 숫자 키는 처음 10개 태그에 대한 바로 가기 키로 할당됩니다. 태그 편집기 창에서 위쪽 및 아래쪽 화살표 아이콘을 사용하여 태그를 다시 정렬할 수 있습니다.

위의 단계를 수행하여 5개의 양식에 레이블을 지정한 후 다음 단계로 이동합니다.

![샘플 레이블 지정 도구의 주 편집기 창](../media/label-tool/main-editor.png)


## <a name="train-a-custom-model"></a>사용자 지정 모델 학습

왼쪽 창에서 학습 아이콘(열차)을 클릭하여 [학습] 페이지를 엽니다. 그런 다음, **학습** 단추를 클릭하여 모델 학습을 시작합니다. 학습 프로세스가 완료되면 다음 정보가 표시됩니다.

* **모델 ID** - 만들어지고 학습된 모델의 ID입니다. 각 학습 호출은 고유한 ID를 사용하여 새 모델을 만듭니다. REST API를 통해 예측 호출을 수행하려면 이 문자열이 필요하므로 안전한 위치에 복사합니다.
* **평균 정확도** - 모델의 평균 정확도입니다. 추가 양식에 레이블을 지정하고 다시 학습하여 새 모델을 만들면 모델 정확도를 향상시킬 수 있습니다. 먼저 5개의 양식에 레이블을 지정하고, 필요에 따라 더 많은 양식을 추가하는 것이 좋습니다.
* 태그 목록 및 태그당 예상 정확도

![학습 보기](../media/label-tool/train-screen.png)

학습이 완료되면 **평균 정확도** 값을 검사합니다. 낮은 경우 더 많은 입력 문서를 추가하고 위의 단계를 반복해야 합니다. 이미 레이블이 지정된 문서는 프로젝트 인덱스에서 유지됩니다.

> [!TIP]
> 학습 프로세스는 REST API 호출을 사용하여 실행할 수도 있습니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [Python을 사용하여 레이블로 학습](./python-labeled-data.md)을 참조하세요.

## <a name="analyze-a-form"></a>양식 분석

왼쪽에 있는 예측(사각형) 아이콘을 클릭하여 모델을 테스트합니다. 학습 프로세스에서 사용하지 않은 양식 문서를 업로드합니다. 그런 다음, 오른쪽의 **예측** 단추를 클릭하여 양식에 대한 키/값 예측을 가져옵니다. 이 도구는 태그를 경계 상자에 적용하고, 각 태그의 신뢰도를 보고합니다.

> [!TIP]
> 분석 API는 REST 호출을 사용하여 실행할 수도 있습니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [Python을 사용하여 레이블로 학습](./python-labeled-data.md)을 참조하세요.

## <a name="improve-results"></a>결과 향상

보고된 정확도에 따라 모델을 향상시키기 위해 추가 학습을 수행하는 것이 좋습니다. 예측이 완료되면 적용된 각 태그의 신뢰도 값을 검사합니다. 평균 정확도 학습 값이 높지만 신뢰도 점수가 낮거나 결과가 정확하지 않은 경우, 예측에 사용되는 파일을 학습 세트에 추가하고 레이블을 지정하고 다시 학습시켜야 합니다.

분석되는 문서가 학습에 사용된 문서와 다를 경우 보고된 평균 정확도, 신뢰도 점수 및 실제 정확도가 일치하지 않을 수 있습니다. 사용자가 볼 때 일부 문서는 비슷하게 보이지만 AI 모델과는 다르게 보일 수 있습니다. 예를 들어 두 가지 변형이 있는 양식 유형을 사용하여 학습할 수 있습니다. 여기서 학습 세트는 변형 A(20%)와 변형 B(80%)로 구성됩니다. 예측하는 동안 변형 A 문서의 신뢰도 점수가 낮아질 수 있습니다.

## <a name="save-a-project-and-resume-later"></a>프로젝트 저장 및 나중에 다시 시작

다른 시간 또는 다른 브라우저에서 프로젝트를 다시 시작하려면 프로젝트의 보안 토큰을 저장하고 나중에 다시 입력해야 합니다. 

### <a name="get-project-credentials"></a>프로젝트 자격 증명 가져오기
프로젝트 설정 페이지(슬라이더 아이콘)로 이동하여 보안 토큰 이름을 기록합니다. 그런 다음, 애플리케이션 설정(기어 아이콘)으로 이동합니다. 그러면 현재 브라우저 인스턴스의 모든 보안 토큰이 표시됩니다. 프로젝트의 보안 토큰을 찾아서 해당 이름과 키 값을 안전한 위치에 복사합니다.

### <a name="restore-project-credentials"></a>프로젝트 자격 증명 복원
프로젝트를 다시 시작하려면 먼저 동일한 Blob 스토리지 컨테이너에 대한 연결을 만들어야 합니다. 이렇게 하려면 위의 단계를 수행합니다. 그런 다음, 애플리케이션 설정 페이지(기어 아이콘)로 이동하여 프로젝트의 보안 토큰이 있는지 확인합니다. 그렇지 않으면 새 보안 토큰을 추가하고, 이전 단계의 토큰 이름과 키를 다시 복사합니다. 그런 다음, [설정 저장]을 클릭합니다. 

### <a name="resume-a-project"></a>프로젝트 다시 시작
마지막으로, 기본 페이지(집 아이콘)로 이동하여 [클라우드 프로젝트 열기]를 클릭합니다. 그런 다음, Blob 스토리지 연결을 선택하고, 프로젝트의 *.vott* 파일을 선택합니다. 보안 토큰이 있으므로 애플리케이션에서 프로젝트의 모든 설정을 로드합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Form Recognizer 샘플 레이블 지정 도구를 사용하여 수동으로 레이블이 지정된 데이터로 모델을 학습시키는 방법을 알아보았습니다. 레이블 지정 도구를 사용자 고유의 애플리케이션에 통합하려면 레이블이 지정된 데이터 학습을 처리하는 REST API를 사용합니다.

> [!div class="nextstepaction"]
> [Python을 사용하여 레이블로 학습](./python-labeled-data.md)
