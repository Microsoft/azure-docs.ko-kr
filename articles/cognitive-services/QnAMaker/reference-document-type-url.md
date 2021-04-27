---
title: 가져오기에 대해 지원되는 URL 형식 - QnA Maker
description: URL 형식을 사용하여 QnA 쌍을 가져오고 만드는 방법을 이해합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 8bf50c1ea81cdf5246c47646d1a55926fe7d58d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91776700"
---
# <a name="urls-supported-for-importing-documents"></a>문서 가져오기에 대해 지원되는 URL

URL 형식을 사용하여 QnA 쌍을 가져오고 만드는 방법을 이해합니다.

## <a name="faq-urls"></a>FAQ URL

QnA Maker는 3가지 형식으로 FAQ 웹 페이지를 지원할 수 있습니다.

* 일반 FAQ 페이지
* 링크가 포함된 FAQ 페이지
* 항목 홈페이지가 포함된 FAQ 페이지

### <a name="plain-faq-pages"></a>일반 FAQ 페이지

가장 일반적인 형식의 FAQ 페이지로, 동일한 페이지에서 답변이 질문 바로 뒤에 표시됩니다.

아래는 일반 FAQ 페이지의 예입니다.

![기술 자료를 위한 일반 FAQ 페이지 예제](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>링크가 포함된 FAQ 페이지

이러한 종류의 FAQ 페이지에서는 질문이 함께 집계되어 동일한 페이지의 다른 섹션 또는 다른 페이지에 있는 답변에 연결됩니다.

아래는 동일한 페이지에 있는 섹션의 링크가 포함된 FAQ 페이지의 예입니다.

 ![기술 자료를 위한 섹션 링크 FAQ 페이지 예제](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>부모 항목 페이지와 자식 응답 페이지 연결

이 유형의 FAQ에는 각 항목이 다른 페이지에 있는 해당 질문과 대답 집합이 연결되는 항목 페이지가 있습니다. QnA Maker는 연결된 모든 페이지를 크롤링하여 해당하는 질문과 답변을 추출합니다.

여러 페이지의 FAQ 섹션에 대한 링크가 포함된 항목 페이지의 예는 아래와 같습니다.

 ![기술 자료를 위한 딥 링크 FAQ 페이지 예제](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>지원 URL

QnA Maker는 지정된 태스크를 수행하는 방법, 지정된 문제를 진단하고 해결하는 방법 및 지정된 프로세스에 대한 어떤 모범 사례가 있는지를 설명하는 웹 문서와 같은 반정형화된 지원 웹 페이지를 처리할 수 있습니다. 추출은 계층 제목이 포함된 명확한 구조를 가진 콘텐츠에서 최적으로 작동합니다.

> [!NOTE]
> 지원 문서에 대한 추출은 새 기능이며 초기 단계에 있습니다. 잘 구조화된 단순 페이지에서 최적으로 작동하며 복잡한 헤더/바닥글을 포함하지 않습니다.

![QnA Maker는 계층 제목과 함께 명확한 구조가 제시되는 반정형화된 웹 페이지에서 추출을 지원합니다.](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)