---
title: Form Recognizer란?
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Form Recognizer를 사용하면 양식 문서에서 키/값 쌍과 테이블 데이터를 식별하고 추출할 수 있습니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 0df61c2ee42d468562efd67a2a66a90a5e4fda53
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723567"
---
# <a name="what-is-form-recognizer"></a>Form Recognizer란?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recognizer는 기계 학습 기술을 사용하여 양식 문서에서 텍스트, 키-값 쌍 및 테이블 데이터를 식별하고 추출하는 인지 서비스입니다. 그리고 양식에서 텍스트를 수집하고 원본 파일의 관계가 포함된 정형 데이터를 출력합니다. 많은 수동 작업 또는 광범위한 데이터 과학 전문 지식 없이도 특정 콘텐츠에 맞게 조정된 정확한 결과를 빠르게 얻을 수 있습니다. Form Recognizer는 사용자 지정 모델, 미리 작성된 영수증 모델 및 레이아웃 API로 구성됩니다. REST API를 통해 Form Recognizer 모델을 호출하여 복잡성을 줄이고 워크플로 또는 애플리케이션에 통합할 수 있습니다.

Form Recognizer는 다음과 같은 서비스로 구성됩니다.
* **사용자 지정 모델** - 양식에서 키-값 쌍 및 테이블 데이터를 추출합니다. 이러한 모델은 사용자 고유의 데이터로 학습되므로 사용자의 양식에 맞게 조정됩니다.
* **미리 빌드된 모델** - 미리 빌드된 모델을 사용하여 고유한 양식 유형에서 데이터를 추출합니다. 현재 영어로 된 판매 영수증과 명함용으로 미리 빌드된 모델을 사용할 수 있습니다.
* **레이아웃 API** - 문서에서 경계 상자 좌표와 함께 텍스트 및 테이블 구조를 추출합니다.

<!-- add diagram -->

## <a name="custom-models"></a>사용자 지정 모델

Form Recognizer 사용자 지정 모델은 사용자 고유의 데이터로 학습하며, 샘플 입력 양식 5개만 있으면 시작할 수 있습니다. 학습된 모델은 원본 양식 문서의 관계가 포함된 정형 데이터를 출력할 수 있습니다. 모델이 학습되면 데이터를 더 많은 양식에서 안정적으로 추출하기 위해 필요에 따라 모델을 테스트, 재학습 및 최종적으로 사용할 수 있습니다.

사용자 지정 모델을 학습할 때 레이블 지정 데이터를 사용할 수도 있고 사용하지 않을 수도 있습니다.

### <a name="train-without-labels"></a>레이블 없이 학습

기본적으로 Form Recognizer는 자율 학습을 사용하여 양식의 필드와 항목 간의 레이아웃 및 관계를 이해합니다. 입력 양식을 제출하면 알고리즘에서 형식별로 양식을 클러스터링하고, 존재하는 키와 테이블을 검색하고, 값을 키에 연결하고, 항목을 테이블에 연결합니다. 이 과정에서 수동으로 데이터 레이블을 지정하거나 과도한 코딩 및 유지 관리가 필요 없으므로 이 방법을 먼저 시도해 볼 것을 권장합니다.

### <a name="train-with-labels"></a>레이블을 사용하여 학습

레이블 지정 데이터로 학습하는 경우 모델은 감독 학습을 통해 관심 있는 값을 추출하며, 사용자가 제공하는 레이블 지정 양식을 사용합니다. 이 방법은 모델 성능이 향상되며, 복잡한 양식 또는 키 없는 값을 포함하는 양식과 함께 작동하는 모델을 생성할 수 있습니다.

Form Recognizer는 [레이아웃 API](#layout-api)를 사용하여 인쇄 및 필기된 텍스트 요소의 예상 크기와 위치를 학습합니다. 그 후 사용자 지정 레이블을 사용하여 문서의 키/값 연결을 학습합니다. 새 모델을 학습시킬 때 수동 레이블 지정 양식 5개를 사용하여 시작한 후 필요에 따라 레이블 지정 데이터를 추가하여 모델 정확도를 개선하는 것이 좋습니다.

## <a name="prebuilt-models"></a>미리 작성된 모델

Form Recognizer에는 고유한 양식 유형을 위한 미리 빌드된 모델도 포함되어 있습니다.
### <a name="prebuilt-receipt-model"></a>미리 빌드된 영수증 모델
미리 빌드된 영수증 모델은 오스트레일리아, 캐나다, 영국, 인도, 미국&mdash;(식당, 주유소, 소매점 등)에서 사용되는 유형의 영어 판매 영수증을 읽는 데 사용됩니다. 이 모델은 트랜잭션 시간 및 날짜, 판매자 정보, 세금의 합계, 줄 항목, 총액 등과 같은 주요 정보를 추출합니다. 또한 미리 작성된 영수증 모델은 영수증의 모든 텍스트를 인식하고 반환하도록 학습됩니다. 

![샘플 영수증](./media/contoso-receipt-small.png)

### <a name="prebuilt-business-cards-model"></a>미리 빌드된 명함 모델
명함 모델을 사용하면 영어로 된 명함에서 사람의 이름, 직함, 주소, 이메일, 회사 및 전화번호와 같은 정보를 추출할 수 있습니다. 

![샘플 명함](./media/business-card-english.jpg)

## <a name="layout-api"></a>레이아웃 API

또한 Form Recognizer는 고화질 OCR(광학 문자 인식)을 사용하여 텍스트 및 테이블 구조(텍스트와 연결된 행 및 열 번호)를 추출할 수 있습니다.

## <a name="get-started"></a>시작하기

빠른 시작에 따라 양식에서 데이터 추출을 시작합니다. 기술을 학습할 때 체험판 서비스를 이용하는 것이 좋습니다. 체험판 페이지는 한 달에 500페이지로 제한됩니다.

* [클라이언트 라이브러리 빠른 시작](./quickstarts/client-library.md)(모든 언어, 여러 시나리오)
* 웹 UI 빠른 시작
  * [레이블을 사용하여 학습 - 샘플 레이블 지정 도구](quickstarts/label-tool.md)
* REST 빠른 시작
  * 사용자 지정 모델 학습 및 양식 데이터 추출
    * [레이블 없이 학습 - cURL](quickstarts/curl-train-extract.md)
    * [레이블 없이 학습 - Python](quickstarts/python-train-extract.md)
    * [레이블을 사용하여 학습 - Python](quickstarts/python-labeled-data.md)
  * USA 판매 영수증에서 데이터 추출
    * [영수증 데이터 추출 - cURL](quickstarts/curl-receipts.md)
    * [영수증 데이터 추출 - Python](quickstarts/python-receipts.md)
  * 양식에서 텍스트 및 테이블 구조 추출
    * [레이아웃 데이터 추출 - Python](quickstarts/python-layout.md)


### <a name="review-the-rest-apis"></a>REST API 검토

다음 API를 사용하여 모델을 학습시키고 양식에서 정형 데이터를 추출합니다.

|Name |Description |
|---|---|
| **사용자 지정 모델 학습**| 동일한 형식의 5개 양식을 사용하여 양식을 분석하는 새 모델을 학습시킵니다. _useLabelFile_ 매개 변수를 `true`로 설정하여 수동 레이블 지정 데이터로 학습합니다. |
| **양식 분석** |스트림으로 전달된 단일 문서를 분석하여 사용자 지정 모델을 통해 양식에서 텍스트, 키/값 쌍 및 테이블을 추출합니다.  |
| **영수증 분석** |단일 영수증 문서를 분석하여 키 정보 및 다른 영수증 텍스트를 추출합니다.|
| **레이아웃 분석** |양식의 레이아웃을 분석하여 텍스트 및 테이블 구조를 추출합니다.|

자세히 알아보려면 [REST API 참조 설명서](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)를 확인하세요. 이전 버전의 API에 대해 잘 알고 있는 경우에는 [새로운 기능](./whats-new.md) 문서를 읽고 최신 변경 내용에 대해 알아보세요.

## <a name="input-requirements"></a>입력 요구 사항
### <a name="custom-model"></a>사용자 지정 모델

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt"></a>미리 빌드됨

영수증 모델에 대한 입력 요구 사항은 약간 다릅니다.

* 형식은 JPEG, PNG, PDF(텍스트 또는 스캔) 또는 TIFF여야 합니다.
* 파일 크기는 20MB 미만이어야 합니다.
* 이미지 크기는 50x50 픽셀에서 10,000x10,000 픽셀 사이여야 합니다.
* PDF 크기는 최대 17x17인치(Legal 또는 A3 용지 크기 이하에 해당)여야 합니다.
* PDF 및 TIFF의 경우 처음 200페이지만 처리됩니다(체험 계층 구독의 경우 처음 2페이지만 처리됨).

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

모든 Cognitive Services와 마찬가지로 Form Recognizer 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft 정책을 알고 있어야 합니다. Microsoft Trust Center의 [Cognitive Services 페이지](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

[빠른 시작](quickstarts/curl-train-extract.md)을 수행하여 [Form Recognizer API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)를 시작합니다.