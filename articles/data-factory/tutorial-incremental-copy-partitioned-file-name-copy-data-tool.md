---
title: 분할 된 시간 파일 이름을 기반으로 새 파일 증분 복사
description: Azure 데이터 팩터리를 만든 다음 데이터 복사 도구를 사용 하 여 시간 분할 된 파일 이름에 따라 새 파일을 증분 로드 합니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 1/24/2019
ms.openlocfilehash: 38e0028efd7fbda8aa5bee497836ce1161109e03
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982622"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>데이터 복사 도구를 사용 하 여 시간 분할 된 파일 이름에 따라 새 파일을 증분 복사

이 자습서에서는 Azure Portal을 사용하여 데이터 팩터리를 만듭니다. 그런 다음 데이터 복사 도구를 사용 하 여 Azure Blob storage에서 Azure blob storage로의 시간 분할 된 파일 이름에 따라 새 파일을 증분 복사 하는 파이프라인을 만듭니다.

> [!NOTE]
> Azure Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.

이 자습서에서는 다음 단계를 수행합니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 데이터 복사 도구를 사용하여 파이프라인 만들기
> * 파이프라인 및 작업 실행을 모니터링합니다.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure storage 계정**: Blob storage를 _원본_ 및 _싱크_ 데이터 저장소로 사용 합니다. Azure Storage 계정이 없는 경우 [스토리지 계정 만들기](../storage/common/storage-account-create.md)의 지침을 참조하세요.

### <a name="create-two-containers-in-blob-storage"></a>Blob 저장소에 두 개의 컨테이너 만들기

이러한 단계를 수행 하 여 자습서에 대한 Blob 저장소를 준비 합니다.

1. **Source**라는 컨테이너를 만듭니다.  컨테이너에서 **2019/02/26/14** 로 폴더 경로를 만듭니다. 빈 텍스트 파일을 만들고 파일 이름을 **file1 .txt**로 합니다. 저장소 계정에서 폴더 경로 **원본/2019/02/26/14** 로 file1을 업로드 합니다.  [Azure Storage Explorer](https://storageexplorer.com/)와 같은 다양한 도구를 사용하여 이러한 작업을 수행할 수 있습니다.

    ![파일 업로드](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > UTC 시간으로 폴더 이름을 조정 하세요.  예를 들어 현재 UTC 시간이 26 일, 2019에서 2:03 PM 인 경우 **원본/{Year}/{Month}/{Day}/{Hour}/** 의 규칙으로 폴더 경로를 **source/2019/02/26/14/** 로 만들 수 있습니다.

2. **Destination**이라는 컨테이너를 만듭니다. [Azure Storage Explorer](https://storageexplorer.com/)와 같은 다양한 도구를 사용하여 이러한 작업을 수행할 수 있습니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. 왼쪽 메뉴에서 **리소스 만들기** > **데이터 + 분석** > **Data Factory**를 차례로 선택합니다.

   !["새로 만들기" 창에서 데이터 팩터리 선택](./media/doc-common-process/new-azure-data-factory-menu.png)

2. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory**를 입력합니다.

    데이터 팩터리 이름은 _전역적으로 고유_해야 합니다. 다음과 같은 오류 메시지가 표시될 수 있습니다.

   ![새 데이터 팩터리 오류 메시지](./media/doc-common-process/name-not-available-error.png)

   이름 값에 대한 오류 메시지가 표시되면 데이터 팩터리에 대한 다른 이름을 입력합니다. 예를 들어 _**yourname**_ **ADFTutorialDataFactory**를 사용합니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
3. 새 데이터 팩터리를 만들 Azure **구독**을 선택합니다.
4. **리소스 그룹**에 대해 다음 단계 중 하나를 사용합니다.

    a. **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.

    b. **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다. 
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요.

5. **버전** 아래에서 버전에 대해 **V2**를 선택합니다.
6. **위치** 아래에서 데이터 팩터리에 대한 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소(예: Azure Storage, SQL Database) 및 계산(예: Azure HDInsight)은 다른 위치와 지역에 있을 수 있습니다.
7. **대시보드에 고정**을 선택합니다.
8. **만들기**를 선택합니다.
9. 대시보드의 **Data Factory 배포 중** 타일에서 프로세스 상태가 표시됩니다.

    ![Data Factory 배포 중 타일](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. 만들기가 완료되면 **Data Factory** 홈페이지가 표시됩니다.

    ![데이터 팩터리 홈페이지](./media/doc-common-process/data-factory-home-page.png)
11. 별도의 탭에서 Azure Data Factory UI(사용자 인터페이스)를 시작하려면 **작성 및 모니터링** 타일을 선택합니다.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>데이터 복사 도구를 사용하여 파이프라인 만들기

1. **시작** 하기 페이지에서 **데이터 복사** 제목을 선택 하 여 데이터 복사 도구를 시작 합니다.

   ![데이터 복사 도구 타일](./media/doc-common-process/get-started-page.png)

2. **속성** 페이지에서 다음 단계를 수행 합니다.

    a. **작업 이름**아래에서 **DeltaCopyFromBlobPipeline**을 입력 합니다.

    b. **작업 흐름 또는 작업 일정**에서 **일정에 따라 정기적으로 실행**을 선택 합니다.

    다. **트리거 유형**에서 **연속 창**을 선택 합니다.

    d. **되풀이**에서 **1 시간**을 입력 합니다.

    e. **다음**을 선택합니다.

    Data Factory UI에서 지정한 작업 이름이 있는 파이프라인을 만듭니다.

    ![속성 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. **원본 데이터 저장소** 페이지에서 다음 단계를 완료합니다.

    a. **+ 새 연결 만들기**를 클릭 하 여 연결을 추가 합니다.

    ![원본 데이터 저장소 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)

    b. 갤러리에서 **Azure Blob Storage** 을 선택 하 고 **계속**을 클릭 합니다.

    ![원본 데이터 저장소 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)

    다. **새 연결 된 서비스** 페이지의 **저장소 계정 이름** 목록에서 저장소 계정을 선택 하 고 **마침**을 클릭 합니다.

    ![원본 데이터 저장소 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. 새로 만든 연결 된 서비스를 선택 하 고 **다음**을 클릭 합니다.

   ![원본 데이터 저장소 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. **입력 파일 또는 폴더 선택** 페이지에서 다음 단계를 수행합니다.

    a. **원본** 컨테이너를 찾아 선택한 다음 선택을 선택 **합니다.**

    ![입력 파일 또는 폴더 선택](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. **파일 로드 동작**에서 **증분 로드: 시간 분할 된 폴더/파일 이름**을 선택 합니다.

    ![입력 파일 또는 폴더 선택](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)

    다. 동적 폴더 경로를 **원본/{year}/{month}/{day}/{hour}/** 로 작성 하 고 다음 형식으로 변경 합니다.

    ![입력 파일 또는 폴더 선택](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)

    d. **이진 복사** 를 선택 하 고 **다음**을 클릭 합니다.

    ![입력 파일 또는 폴더 선택](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. **대상 데이터 저장소** 페이지에서 데이터 원본 저장소와 동일한 저장소 계정인 **azureblobstorage**를 선택 하 고 **다음**을 클릭 합니다.

    ![대상 데이터 저장소 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. **출력 파일 또는 폴더 선택** 페이지에서 다음 단계를 수행 합니다.

    a. **대상** 폴더를 찾아서 선택 하 **고 선택을 클릭 합니다**.

    ![출력 파일 또는 폴더 선택](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)

    b. 동적 폴더 경로를 **원본/{year}/{month}/{day}/{hour}/** 로 작성 하 고 다음 형식으로 변경 합니다.

    ![출력 파일 또는 폴더 선택](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)

    다. **다음**을 클릭합니다.

    ![출력 파일 또는 폴더 선택](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. **설정** 페이지에서 **다음**을 선택합니다.

    ![설정 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)
8. **요약** 페이지에서 설정을 검토하고 **다음**을 선택합니다.

    ![요약 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. **배포 페이지**에서 **모니터**를 선택하여 파이프라인(작업)을 모니터링합니다.
    ![배포 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다.  파이프라인 실행이 자동으로 트리거될 때까지 대기 해야 합니다 (약 1 시간 후).  **작업** 열은 실행 될 때 작업 실행 세부 정보를 보고 파이프라인을 다시 실행할 수 있는 링크를 포함 합니다. **새로 고침** 을 선택 하 여 목록을 새로 고치고 **작업** 열에서 **활동 실행 보기** 링크를 선택 합니다.

    ![파이프라인 실행 모니터링](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. 파이프라인에는 하나의 작업(복사 작업)만 있으므로 하나의 항목만 표시됩니다. 원본 **/2019/02/26/14/** to **destination/2019/02/26/14** /에서 동일한 파일 이름으로 복사 된 원본 파일 (file1 .txt)을 볼 수 있습니다.  

    ![파이프라인 실행 모니터링](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Azure Storage 탐색기 (https://storageexplorer.com/) 를 사용 하 여 파일을 검사 하 여 동일한를 확인할 수도 있습니다.

    ![파이프라인 실행 모니터링](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. 새 이름이 **file2 .txt**인 다른 빈 텍스트 파일을 만듭니다. 저장소 계정에서 파일 경로 **원본/2019/02/26/15** 폴더에 file2 파일을 업로드 합니다.   [Azure Storage Explorer](https://storageexplorer.com/)와 같은 다양한 도구를 사용하여 이러한 작업을 수행할 수 있습니다.

    ![파이프라인 실행 모니터링](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)

    > [!NOTE]
    > 새 폴더 경로를 만들어야 하는 것을 알 수 있습니다. UTC 시간으로 폴더 이름을 조정 하세요.  예를 들어 현재 UTC 시간이 26 일, 2019에서 3:20 PM 이면 **{Year}/{Month}/{Day}/{Hour}/** 규칙에 따라 **source/2019/02/26/15/** 로 폴더 경로를 만들 수 있습니다.

13. **파이프라인 실행** 보기로 돌아가려면 **모든 파이프라인 실행**을 선택 하 고 한 시간 후에 자동으로 동일한 파이프라인이 트리거될 때까지 기다립니다.  

    ![파이프라인 실행 모니터링](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. 두 번째 파이프라인 실행에 대해 **작업 실행 보기** 를 선택 하 고, 동일한 작업을 수행 하 여 세부 정보를 검토 합니다.  

    ![파이프라인 실행 모니터링](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)

    소스 파일 (file2 .txt)이 **원본/2019/02/26/15/** 에서 대상/i n t/02/ **26/15** /로 복사 되었으므로 동일한 파일 이름을 사용 하는 것을 볼 수 있습니다.

    ![파이프라인 실행 모니터링](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png)

    Azure Storage 탐색기 (https://storageexplorer.com/) 를 사용 하 여 동일한를 확인할 수도 있습니다. **대상** 컨테이너에서 파일을 검색 하려면

    ![파이프라인 실행 모니터링](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>다음 단계
Azure에서 Spark 클러스터를 사용하여 데이터를 변환하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
>[클라우드에서 Spark 클러스터를 사용하여 데이터 변환](tutorial-transform-data-spark-portal.md)
