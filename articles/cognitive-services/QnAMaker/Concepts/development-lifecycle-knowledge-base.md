---
title: 기술 자료 수명 주기 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker는 모델 변경, 발언 예제, 게시, 엔드포인트 쿼리의 데이터 수집으로 구성된 반복 주기에서 가장 잘 학습합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b1978e45a7554358ddd948879143411f89e4c1b2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843408"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>QnA Maker의 기술 자료 수명 주기
QnA Maker는 모델 변경, 발언 예제, 게시, 엔드포인트 쿼리의 데이터 수집으로 구성된 반복 주기에서 가장 잘 학습합니다.

![제작 주기](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>QnA Maker 기술 자료 만들기
QnA Maker KB(기술 자료) 엔드포인트는 KB의 콘텐츠를 기반으로 사용자 쿼리와 가장 일치하는 답변을 제공합니다. 기술 자료 만들기는 질문, 답변, 관련 메타데이터의 콘텐츠 리포지토리를 설정하는 일회성 작업입니다. FAQ 페이지, 제품 설명서 또는 구조화된 Q-A 쌍 같은 기존 콘텐츠를 크롤링하여 기술 자료를 만들 수 있습니다. [기술 자료 만들기](../quickstarts/create-publish-knowledge-base.md)에 대해 알아보세요.

## <a name="testing-and-updating-the-knowledge-base"></a>기술 자료 테스트 및 업데이트

편집 또는 자동 추출을 통해 기술 자료에 콘텐츠가 채워지면 기술 자료를 테스트할 수 있습니다. 대화형 테스트는 일반 사용자 쿼리를 입력 하 고 응답에 올바른 응답과 충분 한 신뢰도 점수가 반환 되었는지 확인 하 여 **테스트** 패널을 통해 QnA Maker 포털에서 수행할 수 있습니다.

* **낮은 신뢰도 점수를 수정 하려면**: 대체 질문을 추가 합니다.
* **쿼리가 잘못 된 [기본 응답](../How-to/change-default-answer.md)을 반환**하는 경우: 올바른 질문에 새 답변을 추가 합니다.

이  테스트-업데이트 반복 과정은 사용자가 결과에 만족할 때까지 계속됩니다. [기술 자료 테스트](../How-To/test-knowledge-base.md) 방법을 알아보세요.

대량 Kb 경우 [Generateanswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) 및 `isTest` body 속성으로 자동화 된 테스트를 사용 합니다 .이 속성은 게시 된 기술 자료가 아닌 `test` 기술 자료를 쿼리 합니다.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>기술 자료 게시
기술 자료 테스트를 마쳤으면 기술 자료를 게시할 수 있습니다. Publish는 테스트 된 기술 자료의 최신 버전을 **게시** 된 기술 자료를 나타내는 전용 Azure Cognitive Search 인덱스로 푸시합니다. 또한 애플리케이션 또는 챗봇에서 호출할 수 있는 엔드포인트를 만듭니다.

이러한 방식으로 테스트 버전 기술 자료를 변경하면 이미 게시된 프로덕션 응용프로그램 기술 자료에 영향을 주지 않습니다.

각각의 기술 자료를 테스트 대상으로 지정할 수 있습니다. Api를 사용 하 여 generateAnswer 호출에서 `isTest` body 속성을 사용 하 여 기술 자료의 테스트 버전을 대상으로 지정할 수 있습니다.

[기술 자료 게시](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) 방법을 알아보세요.

## <a name="monitor-usage"></a>사용량 모니터링
서비스의 채팅 로그를 기록하려면 [QnA Maker 서비스를 만들 때](../How-To/set-up-qnamaker-service-azure.md) Application Insights를 사용하도록 설정해야 합니다.

다양한 서비스 사용 분석 자료를 얻을 수 있습니다. Application Insights를 사용하여 [QnA Maker 서비스에 대한 분석](../How-To/get-analytics-knowledge-base.md) 자료를 얻는 방법을 자세히 알아보세요.

분석 자료의 내용에 따라 [기술 자료를 적절하게 업데이트](../How-To/edit-knowledge-base.md)하세요.

## <a name="version-control-for-data-in-your-knowledge-base"></a>기술 자료에서 데이터에 대 한 버전 제어

데이터에 대 한 버전 제어는 QnA Maker 포털의 **설정** 페이지에서 가져오기/내보내기 기능을 통해 제공 됩니다.

기술 자료를 `.tsv` 또는 `.xls` 형식으로 내보내 기술 자료를 백업할 수 있습니다. 내보낸 후에는이 파일을 일반 소스 제어 확인의 일부로 포함 합니다.

특정 버전으로 다시 이동 해야 하는 경우 로컬 시스템에서 해당 파일을 가져와야 합니다. 내보낸 기술 자료는 **설정** 페이지의 가져오기를 통해서만 사용 **해야 합니다** . 파일 또는 URL 문서 데이터 원본으로 사용할 수 없습니다. 이렇게 하면 현재 기술 자료의 질문과 대답을 가져온 파일의 내용으로 대체 합니다.

## <a name="test-and-production-knowledge-base"></a>테스트 및 프로덕션 기술 자료
기술 자료는 QnA Maker 통해 생성, 유지 관리 및 사용 되는 질문과 대답 집합의 리포지토리입니다. 각 QnA Maker 리소스는 여러 기술 자료를 보유할 수 있습니다.

기술 자료에는 *테스트* 및 *게시*의 두 가지 상태가 있습니다.

### <a name="test-knowledge-base"></a>기술 자료 테스트

*테스트 기술 자료* 는 응답의 정확성 및 완전성을 위해 현재 편집, 저장 및 테스트 된 버전입니다. 테스트 기술 자료에 대 한 변경 내용은 응용 프로그램이 나 채팅 봇의 최종 사용자에 게 영향을 주지 않습니다. 테스트 기술 자료는 HTTP 요청에서 `test` 이라고 합니다. `test` 지식은 QnA Maker의 포털 대화형 **테스트** 창에서 사용할 수 있습니다.

### <a name="production-knowledge-base"></a>프로덕션 기술 자료

*게시 된 기술 자료* 는 채팅 봇 또는 응용 프로그램에서 사용 되는 버전입니다. 기술 자료를 게시 하는 작업은 테스트 기술 자료의 내용을 게시 된 버전의 기술 자료에 저장 합니다. 게시 된 기술 자료가 엔드포인트을 통해 응용 프로그램에서 사용 하는 버전 이므로 콘텐츠가 올바르고 잘 테스트 되었는지 확인 합니다. 게시 된 기술 자료를 HTTP 요청에 `prod` 이라고 합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [신뢰도 점수](./confidence-score.md)