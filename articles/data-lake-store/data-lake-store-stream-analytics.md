---
title: Stream Analytics에서 Data Lake Storage Gen1로 데이터 스트리밍-Azure
description: Azure Storage blob에서 데이터를 읽는 간단한 시나리오를 사용 하 여 Azure Stream Analytics 작업에 대 한 출력으로 Azure Data Lake Storage Gen1를 사용 하는 방법에 대해 알아봅니다.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: 8ef3d7400b64617df8d81c7b84e680496776b5bd
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192146"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Azure Stream Analytics를 사용하여 Azure Storage Blob에서 Azure Data Lake Storage Gen1에 데이터 스트리밍
이 문서에서는 Azure Stream Analytics 작업에 대 한 출력으로 Azure Data Lake Storage Gen1를 사용 하는 방법에 대해 알아봅니다. 이 문서에서는 Azure Storage Blob(입력)에서 데이터를 읽고 Data Lake Storage Gen1(출력)에 데이터를 기록하는 간단한 시나리오를 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항
이 자습서를 시작하기 전에 다음이 있어야 합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure Storage 계정**. 이 계정에서 Blob 컨테이너를 사용하여 Stream Analytics 작업에 대한 데이터를 입력합니다. 이 자습서의 경우 **storageforasa**라는 스토리지 계정 및 **storageforasacontainer**라는 계정 내의 컨테이너가 있다고 가정합니다. 컨테이너를 만든 후에 샘플 데이터 파일을 거기에 업로드합니다. 
  
* **Data Lake Storage Gen1 계정**. [Azure Portal을 사용하여 Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)에 있는 지침을 따릅니다. **myadlsg1**라는 Data Lake Storage Gen1 계정이 있다고 가정합니다. 

## <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기
입력 원본 및 출력 대상을 포함하는 Stream Analytics 작업을 만들어 시작합니다. 이 자습서의 경우 원본은 Azure blob 컨테이너이고 대상은 Data Lake Storage Gen1입니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 창에서 **Stream Analytics 작업**을 클릭한 후 **추가**를 클릭합니다.

    ![Stream Analytics 작업 만들기](./media/data-lake-store-stream-analytics/create.job.png "Stream Analytics 작업 만들기")

    > [!NOTE]
    > 스토리지 계정과 동일한 지역에 작업을 만들지 않으면 지역 간에 데이터를 이동하는 데 추가 비용이 발생합니다.
    >

## <a name="create-a-blob-input-for-the-job"></a>작업에 대한 Blob 입력 만들기

1. Stream Analytics 작업 페이지를 열고 왼쪽 창에서 **입력** 탭을 클릭한 다음 **추가**를 클릭합니다.

    ![작업에 입력 추가](./media/data-lake-store-stream-analytics/create.input.1.png "작업에 입력 추가")

2. **새 입력** 블레이드에서 다음 값을 제공합니다.

    ![작업에 입력 추가](./media/data-lake-store-stream-analytics/create.input.2.png "작업에 입력 추가")

   * **입력 별칭**에 작업 입력에 대한 고유한 이름을 입력합니다.
   * **원본 형식**으로 **데이터 스트림**을 선택합니다.
   * **원본**으로 **Blob Storage**를 선택합니다.
   * **구독**에 대해 **현재 구독의 Blob Storage 사용**을 선택합니다.
   * **Storage 계정**에서 필수 조건의 일부로 만든 Storage 계정을 선택합니다. 
   * **컨테이너**에 대해 선택한 스토리지 계정에서 만든 컨테이너를 선택합니다.
   * **이벤트 직렬화 형식**으로 **CSV**를 선택합니다.
   * **구분 기호**로 **탭**을 선택합니다.
   * **Encoding**으로 **UTF-8**을 선택합니다.

     **만들기**를 클릭합니다. 이제 포털에 입력이 추가되고 연결을 테스트합니다.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>작업에 대한 Data Lake Storage Gen1 출력 만들기

1. Stream Analytics 작업에 대한 페이지를 열고 **출력** 탭을 클릭한 다음, **추가**를 클릭하고 **Data Lake Storage Gen1**을 선택합니다.

    ![작업에 출력 추가](./media/data-lake-store-stream-analytics/create.output.1.png "작업에 출력 추가")

2. **새 출력** 블레이드에서 다음 값을 제공합니다.

    ![작업에 출력 추가](./media/data-lake-store-stream-analytics/create.output.2.png "작업에 출력 추가")

    * **입력 별칭**에 작업 출력에 대한 고유한 이름을 입력합니다. 쿼리 출력을 이 Data Lake Storage Gen1 계정으로 직접 보내기 위해 쿼리에서 사용되는 식별 이름입니다.
    * Data Lake Storage Gen1 계정에 대한 액세스 권한을 부여하라는 메시지가 표시됩니다. **권한 부여**를 클릭합니다.

3. **새 출력** 블레이드에서 계속 다음 값을 제공합니다.

    ![작업에 출력 추가](./media/data-lake-store-stream-analytics/create.output.3.png "작업에 출력 추가")

   * **계정 이름**에는 작업 출력을 전송하려는 위치에 미리 만든 Data Lake Storage Gen1 계정을 선택합니다.
   * **경로 접두사 패턴**에는 지정된 Data Lake Storage Gen1 계정 내에서 파일을 작성하는 데 사용되는 파일 경로를 입력합니다.
   * **날짜 형식**의 경우, 접두사 경로에 날짜 토큰을 사용한 경우 파일을 구성하는 날짜 형식을 선택할 수 있습니다.
   * **시간 형식**의 경우, 접두사 경로에 시간 토큰을 사용한 경우 파일을 구성하는 시간 형식을 지정합니다.
   * **이벤트 직렬화 형식**으로 **CSV**를 선택합니다.
   * **구분 기호**로 **탭**을 선택합니다.
   * **Encoding**으로 **UTF-8**을 선택합니다.
    
     **만들기**를 클릭합니다. 이제 포털에 출력이 추가되고 연결을 테스트합니다.
    
## <a name="run-the-stream-analytics-job"></a>Stream Analytics 작업 실행

1. Stream Analytics 작업을 실행 하려면 **쿼리** 탭에서 쿼리를 실행 해야 합니다. 이 자습서에서는 아래 화면 캡처에 표시 된 것 처럼 자리 표시자를 작업 입력 및 출력 별칭으로 바꿔서 샘플 쿼리를 실행할 수 있습니다.

    ![쿼리 실행](./media/data-lake-store-stream-analytics/run.query.png "쿼리 실행")

2. 화면 맨 위에서 **저장**을 클릭한 다음 **개요** 탭에서 **시작**을 클릭합니다. 대화 상자에서 **사용자 지정 시간**을 선택한 다음 현재 날짜 및 시간을 설정합니다.

    ![작업 시간 설정](./media/data-lake-store-stream-analytics/run.query.2.png "작업 시간 설정")

    **시작**을 클릭하여 작업을 시작합니다. 작업을 시작하는 데 최대 몇 분이 걸릴 수 있습니다.

3. Blob에서 데이터를 선택하는 작업을 트리거하려면 샘플 데이터 파일을 Blob 컨테이너에 복사합니다. [Azure Data Lake Git 리포지토리](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)에서 샘플 데이터 파일을 가져올 수 있습니다. 이 자습서에서는 **vehicle1_09142014.csv** 파일을 복사하겠습니다. [Azure Storage Explorer](https://storageexplorer.com/)와 같은 다양한 클라이언트를 사용하여 Blob 컨테이너에 데이터를 업로드할 수 있습니다.

4. **개요** 탭의 **모니터링** 아래에서 데이터가 처리된 방식을 확인합니다.

    ![작업 모니터링](./media/data-lake-store-stream-analytics/run.query.3.png "작업 모니터링")

5. 마지막으로 Data Lake Storage Gen1 계정에서 작업 출력 데이터가 제공되는 것을 확인할 수 있습니다. 

    ![출력 확인](./media/data-lake-store-stream-analytics/run.query.4.png "출력 확인")

    데이터 탐색기 창에서 Data Lake Storage Gen1 출력 설정(`streamanalytics/job/output/{date}/{time}`)에 지정된 대로 출력이 폴더 경로에 기록됩니다.  

## <a name="see-also"></a>참조
* [HDInsight 클러스터를 만들어 Data Lake Storage Gen1 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
