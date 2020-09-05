---
title: 콘텐츠 형식-QnA Maker
description: 콘텐츠 형식에는 PDF, .DOCX 및 TXT와 같은 여러 표준 구조적 문서가 포함 됩니다.
services: cognitive-services
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 04d7c46a7aa347718cf6bc593a92a2f0430eb6fd
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263166"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>기술 자료에 추가할 수 있는 문서 내용 유형
콘텐츠 형식에는 PDF, DOC 및 TXT와 같은 많은 표준 구조적 문서가 포함 됩니다.

## <a name="file-and-url-data-types"></a>파일 및 URL 데이터 형식

다음 표에는 QnA Maker에서 지원되는 콘텐츠 형식 및 파일 형식이 요약되어 있습니다.

|원본 유형|콘텐츠 유형| 예|
|--|--|--|
|URL|FAQ<br> (플랫, 섹션 또는 토픽 홈페이지 포함)<br>지원 페이지 <br> (단일 페이지 방법 문서, 문제 해결 문서 등)|[일반 FAQ](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[하이퍼링크가 있는 FAQ](https://www.microsoft.com/en-us/software-download/faq),<br> [토픽 홈페이지가 있는 FAQ](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[지원 문서](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|FAQ,<br> 제품 설명서,<br> 브로슈어,<br> 페이퍼,<br> 전단 정책,<br> 지원 가이드,<br> 구조화된 QnA,<br> 기타|**다중 전환 하지 않음**<br>[구조화 된 QnA.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [샘플 semi-structured.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)<br> [샘플 흰색 paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**다중 전환**:<br>[.Docx (Surface Pro)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso 이점 (.docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso 이점 (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|구조화된 QnA 파일<br> (RTF, HTML 지원 포함)|**다중 전환 하지 않음**:<br>[샘플 QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**다중 전환**:<br>[구조적 단순 FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[Surface 노트북 FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|* TXT/TSV|구조화된 QnA 파일|[샘플 chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

데이터 원본에 대 한 인증이 필요한 경우 다음 방법을 고려 하 여 해당 콘텐츠를 QnA Maker으로 가져와야 합니다.

* 수동으로 파일 다운로드 및 QnA Maker로 가져오기
* 인증 된 [Sharepoint 위치의](../how-to/add-sharepoint-datasources.md) 파일 추가

## <a name="url-content"></a>URL 콘텐츠

QnA Maker에서 **URL** 을 통해 두 가지 형식의 문서를 가져올 수 있습니다.

* FAQ URL
* 지원 URL

각 형식은 필요한 형식을 나타냅니다.

## <a name="file-based-content"></a>파일 기반 콘텐츠

[QnA Maker 포털](https://www.qnamaker.ai)에서 공용 원본 또는 로컬 파일 시스템의 기술 자료에 파일을 추가할 수 있습니다.

## <a name="content-format-guidelines"></a>콘텐츠 형식 지침

여러 파일에 대 한 [서식 지침](../reference-document-format-guidelines.md) 에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

[질문 및 답변 (QnA) 쌍](question-answer-set.md)에 저장 되는 정보를 이해 합니다.
