---
title: 한도 및 경계 - QnA Maker
description: QnA Maker에는 기술 자료 및 서비스 부분에 대해 제한이 있습니다. 테스트 및 게시하기 위해서는 기술 자료를 그러한 제한 내로 유지하는 것이 중요합니다.
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 7fdf45a4a22f6d9ffe123f5998592739402be55f
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252011"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker 기술 자료 제한 및 경계

아래에 제공 된 QnA Maker 제한은 [Azure Cognitive Search 가격 책정 계층 제한과](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) [QnA Maker 가격 책정 계층 제한](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)의 조합입니다. 리소스 당 만들 수 있는 기술 자료의 수와 각 기술 자료의 증가 정도를 이해 하려면 두 가지 제한 집합을 알고 있어야 합니다.

## <a name="knowledge-bases"></a>기술 자료

기술 자료의 최대 수는 [Azure Cognitive Search 계층 제한을](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)기반으로 합니다.

|**Azure Cognitive Search 계층** | **Free** | **기본** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|게시할 수 있는 기술 자료의 최대 수|2|14|49|199|199|2,999|

 예를 들어 계층에 허용되는 인덱스가 15개 있으면 기술 자료 14개를 게시할 수 있습니다(게시되는 기술 자료당 인덱스 1개). 15번째 인덱스(`testkb`)는 모든 기술 자료에서 작성 및 테스트용으로 사용됩니다.

## <a name="extraction-limits"></a>추출 제한

### <a name="file-naming-constraints"></a>파일 명명 제약 조건

파일 이름에는 다음 문자가 포함 될 수 없습니다.

|문자 사용 안 함|
|--|
|작은따옴표 `'`|
|큰따옴표 `"`|

### <a name="maximum-file-size"></a>최대 파일 크기

|형식|최대 파일 크기 (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>최대 파일 수

추출할 수 있는 최대 파일 수 및 최대 파일 크기는 **[QnA Maker 가격 책정 계층 제한](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** 에 따라 달라 집니다.

### <a name="maximum-number-of-deep-links-from-url"></a>URL의 최대 딥 링크 수

URL 페이지에서 QnAs를 추출 하기 위해 크롤링할 수 있는 딥 링크의 최대 수는 **20 개입니다**.

## <a name="metadata-limits"></a>메타데이터 제한

메타 데이터는 소문자로 저장 되 고 비교 됩니다.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Azure Cognitive Search 가격 책정 계층

기술 자료 당 최대 메타 데이터 필드 수는 **[Azure Cognitive Search 계층 제한을](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** 기반으로 합니다.

|**Azure Cognitive Search 계층** | **Free** | **기본** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|QnA Maker 서비스별 최대 메타데이터 필드 수(모든 기술 자료에서)|1,000|100*|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>이름 및 값으로

다음 표에서는 메타 데이터 이름 및 값에 대 한 길이와 허용 가능한 문자를 나열 합니다.

|항목|허용 되는 문자|Regex 패턴 일치|최대 문자|
|--|--|--|--|
|속성|수 있습니다<br>영숫자 (문자와 숫자)<br>`_` (밑줄)|`^[a-zA-Z0-9_]+$`|100|
|값|제외한 모든 항목 허용<br>`:` (콜론)<br>`|` (수직 파이프)|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>기술 자료 콘텐츠 제한
기술 자료에서 전반적인 콘텐츠 제한 사항은 아래와 같습니다.
* 답변 텍스트 길이: 25,000
* 질문 텍스트 길이: 1,000
* 메타데이터 키/값 텍스트 길이: 100
* 메타 데이터 이름에 대해 지원 되는 문자: 영문자, 숫자 및 `_`
* 메타 데이터 값에 대해 지원 되는 문자: `:` 및 `|`를 제외한 모든 값
* 파일 이름 길이: 200
* 지원되는 파일 형식: “.tsv”, “.pdf”, “.txt”, “.docx”, “.xlsx”
* 최대 대체 질문 수: 300
* 질문과 대답 쌍의 최대 수: 선택한 **[Azure Cognitive Search 계층](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** 에 따라 달라 집니다. 질문 및 답변 쌍은 Azure Cognitive Search 인덱스의 문서에 매핑됩니다.
* URL/HTML 페이지: 100만 문자

## <a name="create-knowledge-base-call-limits"></a>기술 자료 호출 제한 만들기
이는 각 기술 자료 만들기 작업(‘기술 자료 만들기’ 클릭 또는 CreateKnowledgeBase API 호출)에 대한 제한을 나타냅니다.
* 답변 당 최대 대체 질문 수: 300
* 최대 URL 수: 10
* 최대 파일 수: 10

## <a name="update-knowledge-base-call-limits"></a>기술 자료 호출 제한 업데이트
이는 각 업데이트 작업(‘저장 및 학습’ 클릭 또는 UpdateKnowledgeBase API 호출)에 대한 제한을 나타냅니다.
* 각 원본 이름 길이: 300
* 추가 되거나 삭제 된 대체 질문의 최대 수: 300
* 추가 또는 삭제된 최대 메타데이터 필드 수: 10
* 새로 고칠 수 있는 최대 URL 수: 5

## <a name="next-steps"></a>다음 단계

[서비스 가격 책정 계층](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)을 변경 하는 시기 및 방법에 대해 알아봅니다.
