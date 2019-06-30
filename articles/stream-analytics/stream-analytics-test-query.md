---
title: 샘플 데이터를 사용하여 Azure Stream Analytics 작업 테스트
description: 이 문서에서는 Azure Portal을 사용하여 Azure Stream Analytics 작업을 테스트하고, 입력을 샘플링하고, 샘플 데이터를 업로드하는 방법을 설명합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2019
ms.custom: seodec18
ms.openlocfilehash: c0a76ecd12143e0bbaa9997bfc6d7295df9c4ec7
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340872"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>샘플 데이터로 Stream Analytics 쿼리 테스트

Azure Stream Analytics를 사용 하 여 입력에서 데이터를 샘플링 하거나 시작 하거나 작업을 중지 하지 않고 Azure portal에서 쿼리를 테스트 하려면 샘플 데이터 업로드 수 있습니다.

## <a name="upload-sample-data-and-test-the-query"></a>샘플 데이터 업로드 및 쿼리 테스트

1. Azure 포털에 로그인합니다. 

2. 기존 Stream Analytics 작업을 찾아서 선택합니다.

3. Stream Analytics 작업 페이지의 **작업 토폴로지** 제목 아래에서 **쿼리**를 선택하여 쿼리 편집기 창을 엽니다. 

4. 쿼리를 테스트 하려면 샘플링할 수 있습니다 하거나 데이터에서 라이브 입력 또는 파일에서 업로드 합니다. 데이터는 JSON, CSV 또는 AVRO로 직렬화되어야 합니다. 샘플 입력은 압축되지 않고 UTF-8로 인코딩되어야 합니다. 포털의 CSV 입력 테스트에는 쉼표(,) 구분 기호만 지원됩니다.

    1. 라이브 입력을 사용 하 여: 사용자 입력 중 하나를 마우스 오른쪽 단추로 클릭 합니다. 선택한 **입력의 샘플 데이터**입니다. 다음 화면에서 샘플의 기간을 설정할 수 있습니다.

    1. 파일을 사용 하 여: 사용자 입력 중 하나를 마우스 오른쪽 단추로 클릭 합니다. **파일에서 샘플 데이터 업로드**를 선택합니다. 

    ![Stream Analytics 쿼리 편집기 쿼리 테스트](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. 샘플링 또는 업로드를 완료 한 후 선택 **테스트** 제공한 샘플 데이터에 대해이 쿼리를 테스트 합니다.

    ![Stream Analytics 쿼리 편집기 샘플 데이터 테스트](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. 나중에 필요할 때 테스트 출력을 사용할 수 있도록 사용자 쿼리의 출력이 다운로드 결과에 대한 링크와 함께 브라우저에 표시됩니다. 

7. 반복해서 쿼리를 수정하고 테스트하여 출력이 어떻게 변하는지 확인합니다.

   ![Stream Analytics 쿼리 편집기 샘플 출력](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   쿼리에 여러 출력을 사용하면 각 결과가 별도의 탭에 표시되며, 간편하게 탭을 전환할 수 있습니다.

8. 브라우저에 표시된 결과를 확인했으면 쿼리를 **저장**합니다. 그런 다음, 작업을 **시작**하여 들어오는 이벤트를 처리합니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
