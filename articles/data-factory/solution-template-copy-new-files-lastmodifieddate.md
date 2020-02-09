---
title: LastModifiedDate를 기준으로 새 파일 및 변경 된 파일 복사
description: 솔루션 템플릿을 사용 하 여 LastModifiedDate Azure Data Factory으로 새 파일 및 변경 된 파일을 복사 하는 방법에 대해 알아봅니다.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 971d311dfb54d417a8f66f504d01f08f8bcfc48b
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74942096"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>LastModifiedDate를 사용 하 여 새 파일 및 변경 된 파일 복사 Azure Data Factory

이 문서에서는 파일 기반 저장소에서 대상 저장소로 LastModifiedDate 새 파일 및 변경 된 파일만 복사 하는 데 사용할 수 있는 솔루션 템플릿에 대해 설명 합니다. 

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 먼저 **LastModifiedDate**특성 으로만 새 파일 및 변경 된 파일을 선택 하 고 선택한 파일을 데이터 원본 저장소에서 데이터 대상 저장소로 복사 합니다.

템플릿에는 하나의 작업이 포함 됩니다.
- 파일 저장소에서 대상 저장소로 LastModifiedDate 새 파일 및 변경 된 파일만 복사 하려면 **복사** 합니다.

템플릿은 다음과 같은 6 개의 매개 변수를 정의 합니다.
-  *FolderPath_Source* 은 원본 저장소에서 파일을 읽을 수 있는 폴더 경로입니다. 기본 값을 고유한 폴더 경로로 바꾸어야 합니다.
-  *Directory_Source* 은 원본 저장소에서 파일을 읽을 수 있는 하위 폴더 경로입니다. 기본 값을 사용자의 하위 폴더 경로로 바꾸어야 합니다.
-  *FolderPath_Destination* 은 대상 저장소에 파일을 복사할 폴더 경로입니다. 기본 값을 고유한 폴더 경로로 바꾸어야 합니다.
-  *Directory_Destination* 은 대상 저장소에 파일을 복사 하려는 하위 폴더 경로입니다. 기본 값을 사용자의 하위 폴더 경로로 바꾸어야 합니다.
-  *LastModified_From* 는 LastModifiedDate 특성이이 datetime 값 보다 크거나 같은 파일을 선택 하는 데 사용 됩니다.  마지막으로 복사 되지 않은 새 파일만 선택 하기 위해이 datetime 값은 파이프라인이 마지막으로 트리거된 시간 일 수 있습니다. 기본 값인 ' 2019-02-01T00:00:00Z '를 UTC 표준 시간대의 예상 LastModifiedDate 바꿀 수 있습니다. 
-  이 날짜/시간 값 이전의 LastModifiedDate 특성을 가진 파일을 선택 하는 데 사용 됩니다. *LastModified_To* 마지막으로 복사 되지 않은 새 파일만 선택 하기 위해이 datetime 값이 현재 시간 일 수 있습니다.  기본 값인 ' 2019-02-01T00:00:00Z '를 UTC 표준 시간대의 예상 LastModifiedDate 바꿀 수 있습니다. 

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. 템플릿으로 이동 **LastModifiedDate에서 새 파일만 복사**합니다. 원본 저장소 저장소에 대한 **새** 연결을 만듭니다. 원본 저장소 저장소는 파일을 복사할 위치입니다.

    ![원본에 대한 새 연결 만들기](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. 대상 저장소에 대한 **새** 연결을 만듭니다. 대상 저장소는 파일을 복사할 위치입니다. 

    ![대상에 대한 새 연결 만들기](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. **이 템플릿 사용**을 선택합니다.

    ![이 템플릿 사용](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. 다음 예제와 같이 패널에서 사용할 수 있는 파이프라인이 표시됩니다.

    ![파이프라인 표시](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. **디버그**를 선택 하 고 **매개 변수에** 대한 값을 작성 한 다음 **마침**을 선택 합니다.  아래 그림에서 다음과 같이 매개 변수를 설정 합니다.
   - **FolderPath_Source** = sourcefolder
   - **Directory_Source** = 하위 폴더
   - **FolderPath_Destination** = destinationfolder
   - **Directory_Destination** = 하위 폴더
   - **LastModified_From** = 2019-02-01T00:00:00z
   - **LastModified_To** = 2019-03-01T00:00:00z
    
    예는 timespan (**2019-02-01T00:00:00Z** 에서 **2019-03-01t00:00:00z**) 내에 마지막으로 수정 된 파일이 원본 경로 **sourcefolder/하위** 폴더에서 대상 경로 **destinationfolder/하위**폴더로 복사 됨을 나타냅니다.  사용자 고유의 매개 변수로 바꿀 수 있습니다.

    ![파이프라인 실행](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. 결과를 검토합니다. 구성 된 timespan 내에서 마지막으로 수정 된 파일만 대상 저장소에 복사 된 것을 볼 수 있습니다.

    ![결과 검토](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. 이제 연속 windows 트리거를 추가 하 여이 파이프라인을 자동화할 수 있습니다. 그러면 파이프라인이 항상 새 파일 및 변경 된 파일을 LastModifiedDate에 의해 주기적으로 복사할 수 있습니다.  **트리거 추가**를 선택 하 고 **새로 만들기/편집**을 선택 합니다.

    ![결과 검토](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. **트리거 추가** 창에서 **+ 새로 만들기**를 선택합니다.

9. 트리거 유형에 대해 **연속 창을** 선택 하 고 **15 분 마다** 되풀이로 설정 합니다 (간격 시간으로 변경할 수 있음). 활성화 됨 상자에서 **예** 를 선택한 다음 **확인**을 선택 합니다.

    ![트리거 만들기](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. **트리거 실행 매개 변수의** 값을 다음과 같이 설정 하 고 **마침**을 선택 합니다.
    -  = **sourcefolder**를 **FolderPath_Source** 합니다.  를 원본 데이터 저장소의 폴더로 바꿀 수 있습니다.
    -  = **하위 폴더**를 **Directory_Source** 합니다.  를 원본 데이터 저장소의 하위 폴더로 바꿀 수 있습니다.
    - **Destinationfolder** = **FolderPath_Destination** 합니다.  를 대상 데이터 저장소의 폴더로 바꿀 수 있습니다.
    -  = **하위 폴더**를 **Directory_Destination** 합니다.  를 대상 데이터 저장소의 하위 폴더로 바꿀 수 있습니다.
    - **트리거 () =  \@** **LastModified_From** 합니다.  이 변수는 파이프라인이 마지막으로 트리거된 시간을 확인 하는 트리거의 시스템 변수입니다.
    - **트리거 () = \@** **LastModified_To** 합니다.  이 시간 동안 파이프라인이 트리거되는 시간을 결정 하는 트리거의 시스템 변수입니다.
    
    ![입력 매개 변수](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. **모두 게시**를 선택합니다.
    
    ![모두 게시](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. 데이터 원본 저장소의 원본 폴더에 새 파일을 만듭니다.  이제 파이프라인이 자동으로 트리거되어 새 파일만 대상 저장소에 복사 될 때까지 기다립니다.

13. 왼쪽 탐색 패널에서 **모니터** 탭을 선택 하 고 트리거 되풀이가 15 분 마다 설정 된 경우 약 15 분 동안 기다립니다. 

14. 결과를 검토합니다. 15 분 마다 파이프라인이 자동으로 트리거되고 각 파이프라인 실행 시 원본 저장소의 새 파일 또는 변경 된 파일만 대상 저장소로 복사 되는 것을 볼 수 있습니다.

    ![결과 검토](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)
