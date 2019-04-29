---
title: 증분 방식으로 데이터 복사 도구를 사용 하 여 LastModifiedDate에 기반 하는 새로운 기능과 변경 된 파일을 복사 | Microsoft Docs
description: Azure data factory를 만들고 데이터 복사 도구를 사용 하 여 증분 방식으로 LastModifiedDate를 기반으로 새 파일을 로드 합니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/24/2019
ms.openlocfilehash: 8308190e0e68365343fb50ca33f9bea75c3e4e66
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61099041"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>증분 방식으로 데이터 복사 도구를 사용 하 여 LastModifiedDate에 기반 하는 새로운 기능과 변경 된 파일을 복사

이 자습서에서는 Azure portal 데이터 팩터리를 만듭니다. 증분 방식으로 기반으로 새로운 기능과 변경 된 파일에만 복사 하는 파이프라인을 만드는 데 데이터 복사 도구를 사용 하는 한 해당 **LastModifiedDate** Azure Blob storage로 Azure Blob storage에서.

> [!NOTE]
> Azure Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.

이 자습서에서는 다음 작업을 수행 합니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 데이터 복사 도구를 사용하여 파이프라인 만들기
> * 파이프라인 및 작업 실행을 모니터링합니다.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Storage 계정**: Blob storage를 사용 하 여는 _소스_ 하 고 _싱크_ 데이터 저장소입니다. Azure 저장소 계정이 없는 경우 [저장소 계정 만들기](../storage/common/storage-quickstart-create-account.md)의 지침을 참조하세요.

### <a name="create-two-containers-in-blob-storage"></a>Blob storage에 두 개의 컨테이너 만들기

자습서의이 단계를 수행 하 여 Blob 저장소를 준비 합니다.

1. 라는 컨테이너를 만듭니다 **원본**합니다. 다양 한 도구를 사용 하 여이 작업을 수행할 수 있습니다 [Azure Storage 탐색기](https://storageexplorer.com/)합니다.

2. 라는 컨테이너를 만듭니다 **대상**합니다. 

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. 왼쪽 메뉴에서 **리소스 만들기** > **데이터 + 분석** > **Data Factory**를 차례로 선택합니다. 
   
   !["새로 만들기" 창에서 데이터 팩터리 선택](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory**를 입력합니다. 
      
     ![새 data factory](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   데이터 팩터리 이름은 _전역적으로 고유_해야 합니다. 다음과 같은 오류 메시지가 표시될 수 있습니다.
   
   ![새 데이터 팩터리 오류 메시지](./media/tutorial-copy-data-tool/name-not-available-error.png)

   이름 값에 대한 오류 메시지가 표시되면 데이터 팩터리에 대한 다른 이름을 입력합니다. 예를 들어 _**yourname**_**ADFTutorialDataFactory**를 사용합니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
3. Azure를 선택 **구독** 새 데이터 팩터리를 만들 수 있습니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 사용합니다.
     
    * **기존 항목 사용**을 선택하고, 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.

    * **새로 만들기**를 선택하고, 리소스 그룹의 이름을 입력합니다. 
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.

5. 아래 **버전**를 선택 **V2**합니다.
6. **위치** 아래에서 데이터 팩터리에 대한 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 다른 위치 및 지역에서 데이터 팩터리를 사용 하는 데이터 저장소 (예: Azure Storage 및 SQL Database) 및 계산 (예: Azure HDInsight) 수 있습니다.
7. **대시보드에 고정**을 선택합니다. 
8. **만들기**를 선택합니다.
9. 대시보드를 참조 합니다 **Data Factory 배포 중** 프로세스 상태를 보려면 타일을 합니다.

    ![데이터 팩터리의 타일 배포](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. 만들기가 완료되면 **Data Factory** 홈페이지가 표시됩니다.
   
    ![데이터 팩터리 홈페이지](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. 별도 탭에서 Azure Data Factory 사용자 인터페이스 (UI)를 열려면 선택 합니다 **작성자 및 모니터** 타일입니다. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>데이터 복사 도구를 사용하여 파이프라인 만들기

1. 에 **시작 해 보겠습니다** 페이지에서를 **데이터 복사** 제목 데이터 복사 도구를 엽니다. 

   ![데이터 복사 도구 타일](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. 에 **속성** 페이지에서 다음 단계를 수행 합니다.

    a. 아래 **작업 이름**를 입력 **DeltaCopyFromBlobPipeline**합니다.

    b. 아래 **작업 일정** 또는 **작업 일정**를 선택 **일정에 따라 정기적으로 실행**합니다.

    다. 아래 **트리거 형식**를 선택 **Tumbling Window**합니다.
    
    d. 아래 **되풀이**를 입력 **15 분**합니다. 
    
    e. **다음**을 선택합니다. 
    
    Data Factory UI에서 지정한 작업 이름이 있는 파이프라인을 만듭니다. 

    ![속성 페이지](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. **원본 데이터 저장소** 페이지에서 다음 단계를 완료합니다.

    a. 선택 **+ 새 연결 만들기**, 연결을 추가 합니다.
    
    ![원본 데이터 저장소 페이지](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. 선택 **Azure Blob Storage** 갤러리를 선택 합니다 **계속**합니다.
    
    ![원본 데이터 저장소 페이지](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    다. 에 **새 연결 된 서비스** 페이지에서 저장소 계정을 선택 합니다 **저장소 계정 이름** 목록 및 선택한 **완료**.
    
    ![원본 데이터 저장소 페이지](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. 새로 만든된 연결 된 서비스를 선택 하 고 선택한 **다음**합니다. 
    
   ![원본 데이터 저장소 페이지](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. **입력 파일 또는 폴더 선택** 페이지에서 다음 단계를 완료합니다.
    
    a. 찾아 선택 합니다 **소스** 폴더를 선택한 후 **선택**합니다.
    
    ![입력 파일 또는 폴더 선택](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. 아래 **파일 로드 동작**, 선택 **증분 로드: LastModifiedDate**.
    
    ![입력 파일 또는 폴더 선택](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    다. 확인할 **이진 복사** 선택한 **다음**합니다.
    
     ![입력 파일 또는 폴더 선택](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. 에 **대상 데이터 저장소** 페이지에서 **AzureBlobStorage**합니다. 원본 데이터 저장소로 동일한 저장소 계정입니다. 그런 후 **다음**을 선택합니다.

    ![대상 데이터 저장소 페이지](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. **출력 파일 또는 폴더 선택** 페이지에서 다음 단계를 완료합니다.
    
    a. 찾아 선택 합니다 **대상** 폴더를 선택한 후 **선택**합니다.
    
    ![출력 파일 또는 폴더 선택](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. **다음**을 선택합니다.
    
     ![출력 파일 또는 폴더 선택](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. **설정** 페이지에서 **다음**을 선택합니다. 

    ![설정 페이지](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. 에 **요약** 페이지에서 설정을 검토 하 고 선택한 **다음**합니다.

    ![요약 페이지](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. **배포 페이지**에서 **모니터**를 선택하여 파이프라인(작업)을 모니터링합니다.

    ![배포 페이지](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. **작업** 열에는 활동 실행 세부 정보를 보고 파이프라인을 다시 실행하기 위한 링크가 있습니다. 선택 **새로 고침** 목록 새로 고침을 선택 합니다 **활동 실행 보기** 링크를 **작업** 열입니다. 

    ![목록을 새로 고치고 활동 실행 보기를 선택 합니다.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. 하나의 활동 (복사 활동) 파이프라인에서 하나의 항목을 참조 하세요. 복사 활동에 대한 자세한 내용은 **작업** 열에서 **세부 정보** 링크(안경 아이콘)를 선택합니다. 

    ![파이프라인의 복사 작업은](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    에 파일이 있으므로 **원본** Blob 저장소 계정의 컨테이너에 표시 되지 것입니다 파일에 복사 합니다 **대상** Blob 저장소 계정의 컨테이너에에서 합니다.
    
    ![소스 컨테이너 또는 대상 컨테이너에 없는 파일](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. 빈 텍스트 파일을 만들고 이름을 **file1.txt**합니다. 이 텍스트 파일을 업로드 합니다 **원본** 저장소 계정의 컨테이너입니다. [Azure Storage 탐색기](https://storageexplorer.com/)와 같은 다양한 도구를 사용하여 이러한 작업을 수행할 수 있습니다.   

    ![소스 컨테이너에 file1.txt 만들기 및 업로드](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. 돌아가려면 합니다 **파이프라인 실행** 뷰의 **모든 파이프라인 실행**, 동일한 파이프라인 다시 자동으로 트리거될 때까지 기다립니다.  

    ![모든 파이프라인 실행을 선택 합니다.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. 선택 **활동 실행 보기** 참조 될 때 실행 되는 두 번째 파이프라인에 대 한 합니다. 첫 번째 파이프라인 실행에 대해 수행한 동일한 방식으로 세부 정보를 검토 합니다.  

    ![활동 실행 보기를 선택 하 고 세부 정보를 검토 합니다.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    하나를 해당 참조는에서 복사 된 파일 (file1.txt)를 **원본** 컨테이너를 **대상** Blob storage 계정의 컨테이너.
    
    ![File1.txt 원본 컨테이너에서 대상 컨테이너에 복사 되었습니다.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. 다른 빈 텍스트 파일을 만들고 이름을 **file2.txt**합니다. 이 텍스트 파일을 업로드 합니다 **원본** Blob 저장소 계정의 컨테이너입니다.   
    
16. 이 두 번째 텍스트 파일에 대 한 13 및 14 단계를 반복 합니다. 새 파일 (file2.txt)에서 복사 되었다는 것을 보면 합니다 **원본** 컨테이너를 **대상** 다음 번 파이프라인 실행에 대 한 저장소 계정의 컨테이너.  
    
    ![File2.txt 원본 컨테이너에서 대상 컨테이너에 복사 되었습니다.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    이 사용 하 여에 확인할 수 있습니다 [Azure Storage 탐색기](https://storageexplorer.com/) 파일을 검색 합니다.
    
    ![Azure Storage 탐색기를 사용 하 여 파일 검색](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>다음 단계
Azure에서 Apache Spark 클러스터를 사용 하 여 데이터를 변환 하는 방법에 대해 알아보려면 다음 자습서로 계속 진행 합니다.

> [!div class="nextstepaction"]
>[Apache Spark 클러스터를 사용 하 여 클라우드의 데이터 변환](tutorial-transform-data-spark-portal.md)