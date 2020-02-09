---
title: Batch 테스트 TSV 형식-QnA Maker
titleSuffix: Azure Cognitive Services
description: 일괄 테스트를 위한 TSV 형식 이해
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501033"
---
# <a name="batch-testing-tsv-format"></a>일괄 처리 테스트 TSV 형식

일괄 처리 테스트는 [소스 코드](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) 또는 [다운로드 가능한 실행 파일 압축](https://aka.ms/qna_btzip)으로 사용할 수 있습니다. 일괄 처리 테스트를 실행 하는 명령 형식은 다음과 같습니다.

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|매개 변수|예상 값|
|--|--|
|1|[tsv 입력 필드](#tsv-input-fields) 를 사용 하 여 형식이 지정 된 tsv 파일의 이름|
|2|QnA Maker 포털의 게시 페이지에서-호스트를 사용 하는 엔드포인트에 대 한 URI입니다.|
|3|엔드포인트-키-QnA Maker 포털의 게시 페이지에 있습니다.|
|4|결과에 대해 일괄 테스트에서 만든 tsv 파일의 이름입니다.|

일괄 테스트를 위한 TSV 형식을 이해 하 고 구현 하려면 다음 정보를 사용 합니다. 

## <a name="tsv-input-fields"></a>TSV 입력 필드

|TSV 입력 파일 필드|참고 사항|
|--|--|
|KBID|게시 페이지에 KB ID가 있습니다.|
|질문|사용자가 입력 하는 질문입니다.|
|메타데이터 태그|선택 사항|
|Top 매개 변수|선택 사항| 
|예상 응답 ID|선택 사항|

![일괄 테스트에 대 한 TSV 파일의 입력 형식입니다.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>TSV 출력 필드 

|TSV 출력 파일 매개 변수|참고 사항|
|--|--|
|KBID|게시 페이지에 KB ID가 있습니다.|
|질문|입력 파일에서 입력 한 질문입니다.|
|응답|기술 자료의 최고 답입니다.|
|응답 ID|응답 ID|
|Score|답변에 대 한 예측 점수입니다. |
|메타데이터 태그|반환 된 대답과 연결 됨|
|예상 응답 ID|선택 사항 (예상 답변 ID가 지정 된 경우에만)|
|판단 레이블|선택 사항입니다. 값은 올바른 대답이 지정 된 경우에만 올바른 또는 잘못 된 것일 수 있습니다.|
