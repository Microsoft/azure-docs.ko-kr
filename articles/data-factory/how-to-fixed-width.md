---
title: Azure Data Factory에서 데이터 흐름이 매핑되는 고정 길이 텍스트 파일 처리
description: 데이터 흐름 매핑을 사용 하 여 Azure Data Factory에서 고정 길이 텍스트 파일을 처리 하는 방법에 대해 알아봅니다.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: 23b812da8c84ebf055ac4eabdc4649828c139a7f
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051018"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Data Factory 매핑 데이터 흐름을 사용 하 여 고정 길이 텍스트 파일 처리

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Microsoft Azure Data Factory에서 매핑 데이터 흐름을 사용 하 여 고정 너비 텍스트 파일에서 데이터를 변환할 수 있습니다. 다음 태스크에서는 구분 기호 없이 텍스트 파일에 대 한 데이터 집합을 정의한 다음 서 수 위치에 따라 부분 문자열 분할을 설정 합니다.

## <a name="create-a-pipeline"></a>파이프라인 만들기

1. **+ 새 파이프라인** 을 선택 하 여 새 파이프라인을 만듭니다.

2. 고정 너비 파일을 처리 하는 데 사용 되는 데이터 흐름 작업을 추가 합니다.

    ![고정 너비 파이프라인](media/data-flow/fwpipe.png)

3. 데이터 흐름 작업에서 **새 매핑 데이터 흐름**을 선택 합니다.

4. 원본, 파생 열, 선택 및 싱크 변환을 추가 합니다.

    ![고정 너비 데이터 흐름](media/data-flow/fw2.png)

5. 새 데이터 집합을 사용 하도록 원본 변환을 구성 합니다 .이 데이터 집합은 구분 기호로 분리 된 텍스트 형식입니다.

6. 열 구분 기호 또는 헤더를 설정 하지 않습니다.

   이제이 파일의 내용에 대 한 필드 시작 지점과 길이를 설정 합니다.

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. 원본 변환의 **프로젝션** 탭에 *Column_1*라는 문자열 열이 표시 됩니다.

8. 파생 열에서 새 열을 만듭니다.

9. 열에는 *col1*과 같은 단순한 이름을 지정 합니다.

10. 식 작성기에서 다음을 입력 합니다.

    ```substring(Column_1,1,4)```

    ![파생 열(derived column)](media/data-flow/fwderivedcol1.png)

11. 구문 분석 해야 하는 모든 열에 대해 10 단계를 반복 합니다.

12. **검사** 탭을 선택 하 여 생성 될 새 열을 확인 합니다.

    ![검사](media/data-flow/fwinspect.png)

13. 변환 선택을 사용 하 여 변환에 필요 하지 않은 열을 제거 합니다.

    ![변환 선택](media/data-flow/fwselect.png)

14. 싱크를 사용 하 여 데이터를 폴더에 출력 합니다.

    ![고정 너비 싱크](media/data-flow/fwsink.png)

    출력은 다음과 같습니다.

    ![고정 폭 출력](media/data-flow/fxdoutput.png)

  이제 고정 너비 데이터가 분할 되 고 각각 4 개의 문자를 사용 하 여 Col1, Col2, Col3, Col4 등에 할당 됩니다. 위의 예제에 따라 데이터는 네 개의 열로 분할 됩니다.

## <a name="next-steps"></a>다음 단계

* 데이터 흐름 [변환](concepts-data-flow-overview.md)매핑을 사용 하 여 나머지 데이터 흐름 논리를 작성 합니다.
