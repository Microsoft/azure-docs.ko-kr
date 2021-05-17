---
title: Form Recognizer의 새로운 기능
titleSuffix: Azure Cognitive Services
description: 양식 인식기 API에 대 한 최신 변경 내용을 이해 합니다.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 81115f5a9ed802f1d07c45ec928dc4b84ea2917b
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048751"
---
<!-- markdownlint-disable MD024 -->
# <a name="whats-new-in-form-recognizer"></a>Form Recognizer의 새로운 기능

양식 인식기 서비스는 지속적으로 업데이트 됩니다. 이 문서를 사용 하면 향상 된 기능, 수정 사항 및 설명서 업데이트를 최신 상태로 유지할 수 있습니다.

## <a name="march-2021"></a>2021년 3월

**이제 폼 인식기 v 2.1 공개 미리 보기 3을 사용할 수 있습니다.** 다음 기능을 포함 하 여 v 2.1-preview. 3이 릴리스 되었습니다.

- **새 미리 작성 된 ID 모델** 새 미리 작성 된 ID 모델을 통해 고객은 Id를 가져와서 구조화 된 데이터를 반환 하 여 처리를 자동화할 수 있습니다. 또한 강력한 OCR (광학 인식) 기능을 ID 이해 모델과 결합 하 여 이름, 생년월일, 문제점 날짜, 만료 날짜 등의 여권 및 미국 드라이버 라이선스에서 핵심 정보를 추출 합니다.

  [미리 빌드된 ID 모델에 대 한 자세한 정보](concept-identification-cards.md)

   :::image type="content" source="./media/id-canada-passport-example.png" alt-text="passport 예제" lightbox="./media/id-canada-passport-example.png":::

- **미리 빌드된 송장 모델에 대 한 줄 항목 추출** -이제 미리 작성 한 송장 모델에서 줄 항목 추출을 지원 합니다. 이제 전체 항목과 해당 부분 (설명, 수량, 수량, 제품 ID, 날짜 등)을 추출 합니다. 간단한 API/SDK 호출을 사용 하 여 송장에서 텍스트, 테이블, 키-값 쌍 및 줄 항목의 유용한 데이터를 추출할 수 있습니다.

   [미리 작성 된 송장 모델에 대 한 자세한 정보](concept-invoices.md)

- **감독 된 테이블 레이블 지정 및 학습, 빈 값 레이블** 지정-양식 인식기의 [최신 심층 학습 자동 테이블 추출 기능](https://techcommunity.microsoft.com/t5/azure-ai/enhanced-table-extraction-from-documents-with-form-recognizer/ba-p/2058011)외에도 이제는 고객이 테이블에 레이블을 지정 하 고 학습할 수 있습니다. 이 새로운 릴리스에는 줄 항목/테이블 (동적 및 고정)에 레이블을 지정 하 고 학습 하는 기능이 포함 되며 키-값 쌍 및 줄 항목을 추출 하는 사용자 지정 모델을 학습 하는 기능이 포함 됩니다. 모델이 학습 되 면 모델은 documentResults 섹션에서 JSON 출력의 일부로 줄 항목을 추출 합니다.

    :::image type="content" source="./media/table-labeling.png" alt-text="테이블 레이블 지정" lightbox="./media/table-labeling.png":::

    테이블에 레이블을 지정 하는 것 외에도 이제 빈 값과 영역에 레이블을 지정 합니다. 학습 집합의 일부 문서에 특정 필드에 대 한 값이 없는 경우이를 사용 하 여 모델에서 분석 된 문서 로부터 값을 올바르게 추출 하는 것을 알 수 있습니다.

- **66 새로운 언어 지원** -양식 인식기의 레이아웃 API 및 사용자 지정 모델은 이제 73 언어를 지원 합니다.

  [양식 인식기의 언어 지원에 대 한 자세한 정보](language-support.md)

- **자연 스러운 읽기 순서, 필기 분류 및 페이지 선택** -이 업데이트를 사용 하는 경우 기본 왼쪽에서 오른쪽 및 아래 방향으로 정렬 하는 대신 일반 읽기 순서로 텍스트 줄 출력을 가져오도록 선택할 수 있습니다. 새 readingOrder 쿼리 매개 변수를 사용 하 여 사용자에 게 친숙 한 읽기 주문 출력에 대해 "자연" 값으로 설정 합니다. 또한 라틴어 언어의 경우 양식 인식기는 텍스트 줄을 필기 스타일로 분류 하 고 신뢰 점수를 제공 합니다.

- **미리 작성 된 수신 모델 품질 향상** 이 업데이트에는 미리 작성 된 수신 모델에 대 한 많은 품질 향상, 특히 줄 항목 추출이 포함 됩니다.

## <a name="november-2020"></a>2020년 11월

### <a name="new-features"></a>새로운 기능

**이제 폼 인식기 v 2.1 공개 미리 보기 2를 사용할 수 있습니다.** 다음 기능을 포함 하 여 v 2.1-preview. 2가 릴리스 되었습니다. 

- **새로 작성 된 송장 모델** -새 미리 작성 된 송장 모델을 통해 고객은 다양 한 형식으로 송장을 작성 하 고 구조화 된 데이터를 반환 하 여 송장 처리를 자동화할 수 있습니다. 또한 강력한 OCR (광학 인식) 기능을 송장과 결합 하 여 심층 학습 모델을 이해 하 여 영어로 된 청구서에서 핵심 정보를 추출 합니다. 이 도구는 고객, 공급 업체, 청구서 ID, 청구서 기한, 총액, 세금, 세금 금액, 배송 대상, 청구서 받는 사람 등의 텍스트, 테이블 및 정보를 추출 합니다.

  > [미리 작성 된 송장 모델에 대 한 자세한 정보](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="송장 예" lightbox="./media/invoice-example.jpg":::

- **향상 된 테이블 추출** -이제 강력한 OCR (광학 문자 인식) 기능과 심층 학습 테이블 추출 모델을 결합 하는 향상 된 테이블 추출을 제공 합니다. 양식 인식기는 병합 된 열, 행, 테두리 없음 등을 포함 하는 복잡 한 테이블을 포함 하 여 테이블에서 데이터를 추출할 수 있습니다. 
 
  :::image type="content" source="./media/tables-example.jpg" alt-text="표 예" lightbox="./media/tables-example.jpg":::

 
  > [레이아웃 추출에 대 한 자세한 정보](concept-layout.md)

- **클라이언트 라이브러리 업데이트** -.Net, Python, Java 및 JavaScript 용 [클라이언트 라이브러리](quickstarts/client-library.md) 의 최신 버전은 폼 인식기 2.1 API를 지원 합니다.
- **지원 되는 새 언어: 일본어** - `AnalyzeLayout` 및 `AnalyzeCustomForm` : 일본어 ( `ja` )의 경우 다음과 같은 새로운 언어가 지원 됩니다. [언어 지원](language-support.md)
- **텍스트 줄 스타일 표시 (필기/기타) (라틴어만 해당)** -이제 양식 인식기는 `appearance` 각 텍스트 줄에 대 한 입력 여부를 분류 하는 개체를 신뢰 점수와 함께 출력 합니다. 이 기능은 라틴어 언어에 대해서만 지원 됩니다.
- **품질 향상** -단일 자릿수 추출 기능을 포함 하 여 추출 기능이 향상 되었습니다.
- 양식 인식기 샘플 및 레이블 지정 도구-양식 인식기 샘플 레이블 지정 도구를 사용 하 여 미리 작성 된 청구서, 수신 및 비즈니스 카드 모델 및 레이아웃 API를 사용해 볼 수 있는 **새로운 실습 기능** 을 제공 합니다. 코드를 작성 하지 않고 데이터를 추출 하는 방법을 확인 합니다.

  > [양식 인식기 샘플 도구 사용해 보기](https://fott-preview.azurewebsites.net/)

  ![FOTT 예제](./media/ui-preview.jpg)
  
- **피드백 루프** -샘플 레이블 지정 도구를 통해 파일을 분석 하는 경우, 이제 학습 집합에 추가 하 고 필요한 경우 레이블을 조정 하 고 모델을 향상 시킬 수 있습니다.
- **문서 자동 레이블** 지정-프로젝트에서 이전에 레이블이 지정 된 문서에 따라 추가 문서에 자동으로 레이블을 지정 합니다.

## <a name="august-2020"></a>2020년 8월

### <a name="new-features"></a>새로운 기능

**이제 폼 인식기 v 2.1 공개 미리 보기를 사용할 수 있습니다.** V 2.1-preview. 1은 다음과 같은 기능을 포함 하 여 릴리스 되었습니다. 


- **REST API 참조를 사용할 수** 있습니다 [. v 2.1-preview. 1 참조를 확인 하세요.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) 
- **영어 외에도 지원 되는 새 언어** 는 이제 다음 [언어가](language-support.md) 지원 됩니다. `Layout` : `Train Custom Model` 영어 ( `en` ), 중국어 (간체) ( `zh-Hans` ), 네덜란드어 ( `nl` ), 프랑스어 ( `fr` ), 독일어 ( `de` ), 이탈리아어 ( `it` ), 포르투갈어 ( `pt` ) 및 스페인어 ( `es` )
- 확인란 **/선택 표시 검색** – 양식 인식기는 확인란 및 라디오 단추와 같은 선택 표시의 검색 및 추출을 지원 합니다. 선택 표시는에서 추출 되며 `Layout` `Train Custom Model`  -  _레이블을 사용 하 여 학습을 통해_ 선택 표시에 대 한 키 값 쌍을 추출할 수도 있습니다. 
- **모델 작성** -단일 모델 ID를 사용 하 여 여러 모델을 구성 하 고 호출할 수 있습니다. 구성 된 모델 ID를 사용 하 여 분석 하기 위해 문서를 제출 하면 먼저 분류 단계를 수행 하 여 올바른 사용자 지정 모델로 라우팅합니다. 모델 작성은 `Train Custom Model`  -  _레이블로 학습_ 에 사용할 수 있습니다.
- **모델 이름** -더 쉽게 관리 하 고 추적할 수 있도록 사용자 지정 모델에 친숙 한 이름을 추가 합니다.
- 영어, 언어 비즈니스 카드로 일반 필드를 추출 하는 데 사용할 **[미리 빌드된 새로운 비즈니스 카드 모델](concept-business-cards.md)** 입니다.
- En-us 외에도 **[미리 작성 된 수신 확인에 대 한 새 로캘이](concept-receipts.md)** 이제 EN-US, en-us, en-us, en-us에서 지원 됩니다.
- 의 **품질 향상** `Layout` , `Train Custom Model`  -  _레이블 없이 학습_ 및 _레이블로 학습_

**v 2.0** 에는 다음 업데이트가 포함 됩니다.

- NET, Python, Java 및 JavaScript 용 [클라이언트 라이브러리](quickstarts/client-library.md) 는 일반 공급으로 전환 되었습니다. 

**새 샘플** 은 GitHub에서 사용할 수 있습니다. 

- [지식 추출 조리법-Forms 플레이 북](https://github.com/microsoft/knowledge-extraction-recipes-forms) 는 실제 양식 인식기 고객 계약의 모범 사례를 수집 하 고, 이러한 프로젝트를 개발 하는 데 사용 되는 코드 샘플, 검사 목록 및 샘플 파이프라인을 제공 합니다. 
- [샘플 레이블 지정 도구](https://github.com/microsoft/OCR-Form-Tools) 는 새로운 v 2.1 기능을 지원 하도록 업데이트 되었습니다. 도구를 시작 하려면이 [빠른](quickstarts/label-tool.md) 시작을 참조 하세요. 
- [Intelligent 키오스크](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) 양식 인식기 샘플은 `Analyze Receipt` `Train Custom Model`  -  _레이블 없이_ 통합 및 학습 하는 방법을 보여 줍니다.

## <a name="july-2020"></a>2020년 7월

### <a name="new-features"></a>새로운 기능
<!-- markdownlint-disable MD004 -->
* v2.0 **참조 사용 가능** - [.net](/dotnet/api/overview/azure/ai.formrecognizer-readme), [Python](/python/api/overview/azure/), [Java](/java/api/overview/azure/ai-formrecognizer-readme)및 [JavaScript](/javascript/api/overview/azure/)용 v2.0 [API 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) 및 업데이트 된 sdk를 확인 합니다.
* **테이블 향상 및 추출 향상** -향상 된 정확도 및 테이블 추출 향상 된 기능, 특히 _레이블 없이 사용자 지정 학습_ 에서 테이블 헤더와 구조를 학습 하는 기능을 포함 합니다. 

* **통화 지원** -전역 통화 기호의 검색 및 추출을 지원 합니다.
* Azure **.gov** 는 이제 azure .gov 에서도 사용할 수 있습니다.
* **향상 된 보안 기능**: 
  * 사용자 **고유의 키** -양식 인식기를 가져오면 데이터를 클라우드에 보관 하 여 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정에 맞게 자동으로 암호화 합니다. 기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. 이제 사용자 고유의 암호화 키를 사용 하 여 구독을 관리할 수도 있습니다. [고객 관리 키 (BYOK 라고도 함)](./encrypt-data-at-rest.md)는 액세스 제어를 보다 유연 하 게 만들고, 회전 하 고, 사용 하지 않도록 설정 하 고, 취소할 수 있는 유연성을 제공 합니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다.  
  * **개인 끝점** – 가상 네트워크 (VNet)에서 [개인 링크를 통해 안전](../../private-link/private-link-overview.md) 하 게 데이터에 액세스할 수 있습니다.

## <a name="june-2020"></a>2020년 6월

### <a name="new-features"></a>새로운 기능

* **클라이언트 sdk에 추가 된 Copymodel API** -이제 클라이언트 sdk를 사용 하 여 한 구독에서 다른 구독으로 모델을 복사할 수 있습니다. 이 기능에 대 한 일반 정보는 [모델 백업 및 복구](./disaster-recovery.md) 를 참조 하세요.
* **Azure Active Directory 통합** -이제 Azure AD 자격 증명을 사용 하 여 Sdk에서 양식 인식기 클라이언트 개체를 인증할 수 있습니다.
* **SDK 관련 변경** 내용-여기에는 보조 기능 추가 및 주요 변경 내용이 모두 포함 됩니다. 자세한 내용은 SDK changelogs를 참조 하십시오.
  * [C # SDK Preview 3 변경 로그](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK Preview 3 변경 로그](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java SDK Preview 3 변경 로그](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [JavaScript SDK Preview 3 변경 로그](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>2020년 4월

### <a name="new-features"></a>새로운 기능

* **SDK 지원 양식 인식기 API V2.0 공개 미리 보기** -이번 달에는 폼 인식기 v2.0 (미리 보기) 릴리스에 대 한 미리 보기 SDK를 포함 하도록 서비스 지원이 확장 되었습니다. 아래 링크를 사용 하 여 원하는 언어로 시작 하세요. 
  * [.NET SDK](/dotnet/api/overview/azure/ai.formrecognizer-readme)
  * [Java SDK](/java/api/overview/azure/ai-formrecognizer-readme)
  * [Python SDK](/python/api/overview/azure/ai-formrecognizer-readme)
  * [JavaScript SDK](/javascript/api/overview/azure/ai-form-recognizer-readme)

  새 SDK는 폼 인식기에 대 한 v2.0 REST API의 모든 기능을 지원 합니다. 예를 들어 레이블을 사용 하거나 사용 하지 않고 모델을 학습 하 고, 폼에서 텍스트, 키 값 쌍 및 테이블을 추출 하 고, 미리 작성 된 수신 서비스를 사용 하 여 수신 확인에서 데이터를 추출 하 고, 문서의 레이아웃 서비스를 사용 하 여 텍스트와 테이블을 추출할 수 있습니다. [Sdk 피드백 양식](https://aka.ms/FR_SDK_v1_feedback)을 통해 sdk에서 사용자 의견을 공유할 수 있습니다.

* **사용자 지정 모델 복사** 이제 새 사용자 지정 모델 복사 기능을 사용 하 여 지역과 구독 간에 모델을 복사할 수 있습니다. 사용자 지정 모델 복사 API를 호출 하기 전에 먼저 대상 리소스 끝점에 대해 복사 권한 부여 작업을 호출 하 여 대상 리소스에 복사할 권한 부여를 가져와야 합니다.

  * [복사본 권한 부여 생성](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REST API
  * [사용자 지정 모델 복사](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REST API 

### <a name="security-improvements"></a>향상된 보안 기능

* 이제 FormRecognizer에서 Customer-Managed 키를 사용할 수 있습니다. 자세한 내용은 [폼 인식기에 대 한 저장소 데이터 암호화](./encrypt-data-at-rest.md)를 참조 하세요.
* Azure Active Directory를 사용 하 여 Azure 리소스에 액세스 하기 위해 관리 되는 Id를 사용 합니다. 자세한 내용은 [관리 되는 id에 대 한 액세스 권한 부여](../authentication.md#authorize-access-to-managed-identities)를 참조 하세요.

## <a name="march-2020"></a>2020년 3월

### <a name="new-features"></a>새로운 기능

* **레이블 지정을 위한 값 형식** 이제 폼 인식기 샘플 레이블 지정 도구를 사용 하 여 레이블을 지정 하는 값의 형식을 지정할 수 있습니다. 현재 지원되는 값 형식 및 변형은 다음과 같습니다.
  * `string`
    * 기본값, `no-whitespaces`, `alphanumeric`
  * `number`
    * 기본값, `currency`
  * `date` 
    * 기본값, `dmy`, `mdy`, `ymd`
  * `time`
  * `integer`

  이 기능을 사용 하는 방법에 대 한 자세한 내용은 [샘플 레이블 도구](./quickstarts/label-tool.md#specify-tag-value-types) 가이드를 참조 하세요.


* **테이블 시각화** 이제 샘플 레이블 지정 도구는 문서에서 인식 된 테이블을 표시 합니다. 이 기능을 사용 하면 레이블 지정 및 분석 전에 문서에서 인식 되 고 추출 된 테이블을 볼 수 있습니다. 계층 옵션을 사용 하 여이 기능을 설정/해제할 수 있습니다.

  다음 이미지는 테이블을 인식 하 고 추출 하는 방법의 예입니다.

  > [!div class="mx-imgBorder"]
  > ![샘플 레이블 지정 도구를 사용 하는 테이블 시각화](./media/whats-new/table-viz.png)

    추출 된 테이블은의 JSON 출력에서 사용할 수 있습니다 `"pageResults"` .

  > [!IMPORTANT]
  > 테이블 레이블 지정은 지원 되지 않습니다. 테이블이 인식 되지 않고 자동으로 extrated 키/값 쌍 으로만 레이블을 지정할 수 있습니다. 테이블에 키/값 쌍으로 레이블을 지정 하는 경우 각 셀에 고유한 값으로 레이블을 지정 합니다.

### <a name="extraction-enhancements"></a>향상 된 압축 풀기

이 릴리스에는 추출 향상 및 정확성 향상, 특히 동일한 텍스트 줄에서 여러 키/값 쌍에 대 한 레이블을 만들고 추출 하는 기능이 포함 되어 있습니다. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>이제 샘플 레이블 지정 도구는 오픈 소스입니다.

이제 폼 인식기 샘플 레이블 지정 도구를 오픈 소스 프로젝트로 사용할 수 있습니다. 솔루션 내에서 통합 하 고 요구 사항에 맞게 고객 관련 변경을 수행할 수 있습니다.

폼 인식기 샘플 레이블 지정 도구에 대 한 자세한 내용은 [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)에서 제공 되는 설명서를 참조 하세요.

### <a name="tls-12-enforcement"></a>TLS 1.2 적용

이제 TLS 1.2는 이 서비스에 대한 모든 HTTP 요청에 적용됩니다. 자세한 내용은 [Azure Cognitive Services 보안](../cognitive-services-security.md)을 참조하세요.

## <a name="january-2020"></a>2020년 1월

이 릴리스에는 폼 인식기 2.0 (미리 보기)가 도입 되었습니다. 아래 섹션에는 새로운 기능, 향상 된 기능 및 변경 내용에 대 한 자세한 내용이 나와 있습니다. 

### <a name="new-features"></a>새로운 기능

* **사용자 지정 모델**
  * **레이블로 학습** 이제 수동으로 레이블이 지정 된 데이터를 사용 하 여 사용자 지정 모델을 학습 시킬 수 있습니다. 이 방법을 사용 하면 더 나은 모델을 사용할 수 있고 키가 없는 값을 포함 하는 복잡 한 폼 또는 폼을 사용 하는 모델을 생성할 수 있습니다.
  * **비동기 API** 비동기 API 호출을 사용 하 여 대량 데이터 집합 및 파일을 학습 하 고 분석할 수 있습니다.
  * **TIFF 파일 지원** 이제 TIFF 문서에서 데이터를 학습 하 고 추출할 수 있습니다.
  * **추출 정확도 향상**

* **미리 작성 한 수신 모델**
  * **팁 금액** 이제 팁 양과 기타 필기 값을 추출할 수 있습니다.
  * **줄 항목 추출** 수신 확인에서 품목 값을 추출할 수 있습니다.
  * **신뢰도 값** 추출 된 각 값에 대 한 모델의 신뢰도를 볼 수 있습니다.
  * **추출 정확도 향상**

* **레이아웃 추출** 이제 레이아웃 API를 사용 하 여 폼에서 텍스트 데이터 및 테이블 데이터를 추출할 수 있습니다.

### <a name="custom-model-api-changes"></a>사용자 지정 모델 API 변경

사용자 지정 모델을 학습 하 고 사용 하는 모든 Api의 이름이 변경 되었으며, 일부 동기 메서드는 비동기입니다. 주요 변경 내용은 다음과 같습니다.

* 모델 학습 프로세스는 이제 비동기식입니다. **/Dev/sd** API 호출을 통해 학습을 시작 합니다. 이 호출은 **사용자 지정/모델/{modelID}** 에 전달 하 여 학습 결과를 반환할 수 있는 작업 ID를 반환 합니다.
* 이제 키/값 추출이 **/Custom/models/{modelID}/analyze** API 호출에 의해 시작 됩니다. 이 호출은 **사용자 지정/모델/{modelID}/analyzeResults/{resultID}** 에 전달 하 여 추출 결과를 반환할 수 있는 작업 ID를 반환 합니다.
* 이제 학습 작업에 대 한 작업 Id가 **작업 위치** 헤더가 아니라 HTTP 응답의 **Location** 헤더에 있습니다.

### <a name="receipt-api-changes"></a>수신 API 변경

판매 영수증을 읽기 위한 Api의 이름이 변경 되었습니다.

* 이제 **/Prebuilt/receipt/analyze** API 호출을 통해 수신 데이터 추출을 시작 합니다. 이 호출은 **/prebuilt/receipt/analyzeResults/{resultID}** 에 전달 하 여 추출 결과를 반환할 수 있는 작업 ID를 반환 합니다.

### <a name="output-format-changes"></a>출력 형식 변경

모든 API 호출에 대 한 JSON 응답에는 새 형식이 있습니다. 일부 키 및 값이 추가, 제거 또는 이름이 변경 되었습니다. 현재 JSON 형식의 예제는 빠른 시작을 참조 하세요.

## <a name="next-steps"></a>다음 단계

사용자가 선택한 개발 언어로 Form Recognizer를 사용하여 양식 처리 앱 작성을 시작하려면 [빠른 시작](quickstarts/client-library.md)을 완료하세요.

## <a name="see-also"></a>참조

* [Form Recognizer란?](./overview.md)