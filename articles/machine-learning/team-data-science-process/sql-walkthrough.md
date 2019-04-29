---
title: SQL Server VM에서 모델 빌드 및 배포 - Team Data Science Process
description: 공개적으로 사용 가능한 데이터 세트가 있는 Azure VM에서 SQL Server를 사용하여 기계 학습 모델을 빌드하고 배포합니다.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c9d707d1a76b3b5913d66745767df8e84362a192
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61045785"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>실행 중인 팀 데이터 과학 프로세스: SQL Server 사용
이 자습서에서는 SQL Server 및 공개적으로 사용할 수 있는 데이터 세트([NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) 데이터 세트)를 사용하여 Machine Learning 모델의 배포 및 빌드 처리를 연습합니다. 이 절차는 표준 데이터 과학 워크플로를 따릅니다. 데이터를 수집 및 탐색하고 학습이 용이하도록 기능을 엔지니어링한 후 모델을 빌드 및 배포합니다.

## <a name="dataset"></a>NYC Taxi Trips 데이터 세트 설명
NYC Taxi Trip 데이터는 1억 7,300만 개가 넘는 개별 여정 및 각 여정의 요금으로 구성된 약 20GB의 압축된 CSV 파일(압축되지 않은 경우 약 48GB)입니다. 각 여정 레코드는 승차 및 하차 위치, 익명 처리된 hack(기사) 면허증 번호 및 medallion(택시의 고유 ID) 번호를 포함합니다. 데이터는 2013년의 모든 여정을 포괄하며, 매월 다음 두 개의 데이터 세트로 제공됩니다.

1. 'trip_data' CSV는 승객 수, 승차 및 하차 지점, 여정 기간, 여정 거리 등 여정 세부 정보를 포함합니다. 다음은 몇 가지 샘플 레코드입니다.
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. 'trip_fare' CSV는 지불 유형, 금액, 추가 요금 및 세금, 팁 및 통행료, 총 지불 금액 등 각 여정의 요금에 대한 세부 정보를 포함합니다. 다음은 몇 가지 샘플 레코드입니다.
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

trip\_data와 trip\_fare를 조인할 고유 키는 medallion, hack\_licence 및 pickup\_datetime 필드로 구성됩니다.

## <a name="mltasks"></a>예측 작업의 예제
*tip\_amount*를 기반으로 다음 세 가지 예측 문제를 작성해 보겠습니다.

1. 이진 분류: 여정에 대해 팁이 지불되었는지 여부를 예측합니다. 즉, *tip\_amount*가 $0보다 크면 지불된 것이고 *tip\_amount*가 $0이면 지불되지 않은 것입니다.
2. 다중 클래스 분류: 여정에 대해 지불된 팁의 범위를 예측합니다. *tip\_amount*를 5개의 bin 또는 클래스로 나눕니다.
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. 회귀 작업: 여정에 대해 지불된 팁의 금액을 예측합니다.  

## <a name="setup"></a>고급 분석을 위한 Azure 데이터 과학 환경 설정
[환경 계획 가이드](plan-your-environment.md)에서 볼 수 있듯이 Azure에서 NYC Taxi Trips 데이터 세트로 작업할 수 있는 여러 가지 옵션이 있습니다.

* Azure Blob의 데이터로 작업한 다음 Azure Machine Learning에서 모델링
* SQL Server 데이터베이스로 데이터를 로드한 다음 Azure Machine Learning에서 모델링

이 자습서에서는 SQL Server Management Studio 및 IPython Notebook을 사용하여 대량의 데이터를 SQL Server로 병렬로 가져오기, 데이터 탐색, 기능 엔지니어링 및 다운 샘플링을 수행하는 방법을 보여 줍니다. [샘플 스크립트](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) 및 [IPython Notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)은 GitHub에서 공유됩니다. Azure Blob의 데이터로 작업하는 샘플 IPython Notebook도 같은 위치에서 사용할 수 있습니다.

Azure 데이터 과학 환경을 설정하려면

1. [저장소 계정 만들기](../../storage/common/storage-quickstart-create-account.md)
2. [Azure Machine Learning 작업 영역 만들기](../studio/create-workspace.md)
3. [데이터 과학 Virtual Machine 프로비전](../data-science-virtual-machine/setup-sql-server-virtual-machine.md)(SQL Server 및 IPython Notebook 서버 제공)
   
   > [!NOTE]
   > 샘플 스크립트와 IPython Notebook은 설정 프로세스 중에 데이터 과학 가상 머신으로 다운로드됩니다. VM 사후 설치 스크립트가 완료되면 샘플이 VM의 문서 라이브러리에 배치됩니다.  
   > 
   > * 샘플 스크립트: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * 샘플 IPython Notebook: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   where `<user_name>` 은(는) VM의 Windows 로그인 이름입니다. 샘플 폴더는 **Sample Scripts** 및 **Sample IPython Notebooks**이라고 합니다.
   > 
   > 

데이터 세트 크기, 데이터 원본 위치 및 선택한 Azure 대상 환경에 따라 이 시나리오는 [시나리오 \#5: 로컬 파일에서 큰 데이터 세트, Azure VM에서 대상 SQL Server](plan-sample-scenarios.md#largelocaltodb)와 비슷합니다.

## <a name="getdata"></a>공용 원본에서 데이터 가져오기
해당 공용 위치에서 [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) 데이터 세트를 가져오려면 [Azure Blob Storage에서 데이터 이동](move-azure-blob.md)에 설명된 방법 중 하나를 사용하여 데이터를 새 가상 머신에 복사하면 됩니다.

AzCopy를 사용하여 데이터를 복사하려면

1. VM(가상 컴퓨터)에 로그인합니다.
2. VM의 데이터 디스크에 새 디렉터리를 만듭니다.(참고: 데이터 디스크로 VM과 함께 제공되는 임시 디스크를 사용하지 마세요.)
3. 명령 프롬프트 창에서 <path_to_data_folder>를 (2)에서 만든 데이터 폴더로 바꿔 다음 Azcopy 명령줄을 실행합니다.
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    AzCopy가 완료되면 데이터 폴더에 총 24개의 압축된 CSV 파일(trip\_data 파일 12개와 trip\_fare 12개)이 있어야 합니다.
4. 다운로드한 파일의 압축을 풉니다. 압축을 푼 파일이 있는 폴더를 적어 둡니다. 이 폴더를 <path\_to\_data\_files\>라고 합니다.

## <a name="dbload"></a>SQL Server 데이터베이스로 대량 데이터 가져오기
*분할된 테이블 및 뷰*를 사용하여 대량의 데이터를 SQL Database로 로드/전송하고 이후에 쿼리하는 성능을 개선할 수 있습니다. 이 섹션에서는 [SQL 파티션 테이블을 사용하여 병렬로 대량 데이터 가져오기](parallel-load-sql-partitioned-tables.md) 에 설명된 지침에 따라 새 데이터베이스를 만들고 데이터를 분할된 테이블로 병렬로 로드합니다.

1. VM에 로그인한 상태로 **SQL Server Management Studio**를 시작합니다.
2. Windows 인증을 사용하여 연결
   
    ![SSMS 연결][12]
3. SQL Server 인증 모드를 변경하여 새 SQL 로그인 사용자를 만드는 작업을 아직 수행하지 않은 경우 **Sample Scripts** 폴더에서 **change\_auth.sql**이라는 스크립트 파일을 엽니다. 기본 사용자 이름 및 암호를 변경합니다. 도구 모음에서 **!실행** 을 클릭하여 스크립트를 실행합니다.
   
    ![스크립트 실행][13]
4. SQL Server 기본 데이터베이스 및 로그 폴더를 확인하거나 변경하여 새로 만든 데이터베이스가 데이터 디스크에 저장되도록 합니다. 데이터 웨어하우징 로드에 최적화된 SQL Server VM 이미지는 데이터 및 로그 디스크로 미리 구성됩니다. VM에 데이터 디스크가 포함되지 않은 상태에서 VM 설정 프로세스 중에 새 가상 하드 디스크를 추가한 경우 다음과 같이 기본 폴더를 변경합니다.
   
   * 왼쪽 패널에서 SQL Server 이름을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
     
       ![SQL Server 속성][14]
   * 왼쪽의 **페이지 선택** 목록에서 **데이터베이스 설정**을 선택합니다.
   * **데이터베이스 기본 위치**가 선택한 **데이터 디스크** 위치인지 확인하고 아닌 경우 변경합니다. 기본 위치 설정을 사용하여 만든 새 데이터베이스는 여기에 상주합니다.
     
       ![SQL Database 기본값][15]  
5. 새 데이터베이스 및 파일 그룹 집합을 만들어 분할된 테이블을 유지하려면 **create\_db\_default.sql** 샘플 스크립트를 엽니다. 이 스크립트는 **TaxiNYC** 라는 새 데이터베이스를 만들고 기본 데이터 위치에 12개의 파일 그룹을 만듭니다. 각 파일 그룹에는 한 달 분량의 trip\_data 및 trip\_fare 데이터가 유지됩니다. 필요한 경우 데이터베이스 이름을 수정합니다. **!실행** 을 클릭하여 스크립트를 실행합니다.
6. 이제 trip\_data와 trip\_fare에 대해 각각 하나씩 두 개의 파티션 테이블을 만듭니다. 다음 작업을 수행하는 **create\_partitioned\_table.sql** 샘플 스크립트를 엽니다.
   
   * 데이터를 월별로 분할하는 파티션 함수를 만듭니다.
   * 각 월의 데이터를 다른 파일 그룹에 매핑하는 파티션 구성표를 만듭니다.
   * 파티션 구성표에 매핑된 분할된 테이블 두 개를 만듭니다. **nyctaxi\_trip**은 trip\_data 보유하고 **nyctaxi\_fare**는 trip\_fare 데이터를 보관합니다.
     
     **!실행** 을 클릭하여 스크립트를 실행하고 분할된 테이블을 만듭니다.
7. **Sample Scripts** 폴더에는 대량의 데이터를 SQL Server 테이블로 병렬로 가져오는 방법을 보여 주는 두 개의 샘플 PowerShell 스크립트가 제공되어 있습니다.
   
   * **bcp\_parallel\_generic.ps1**은 대량의 데이터를 테이블로 병렬로 가져오는 일반 스크립트입니다. 이 스크립트를 수정하여 스크립트의 명령줄에 표시된 대로 입력 및 대상 변수를 설정합니다.
   * **bcp\_parallel\_nyctaxi.ps1**은 미리 구성된 버전의 일반 스크립트로서, NYC Taxi Trips 데이터의 두 테이블을 모두 로드하는 데 사용될 수 있습니다.  
8. **bcp\_parallel\_nyctaxi.ps1** 스크립트 이름을 마우스 오른쪽 단추로 클릭하고 **편집**을 클릭하여 PowerShell에서 엽니다. 사전 설정 변수를 검토하고 선택한 데이터베이스 이름, 입력 데이터 폴더, 대상 로그 폴더 및 샘플 형식파일 **nyctaxi_trip.xml** 및 **nyctaxi\_fare.xml**(**Sample Scripts** 폴더에 제공)의 경로에 따라 수정합니다.
   
    ![대용량 데이터 가져오기][16]
   
    인증 모드를 선택할 수도 있습니다. 기본값은 Windows 인증입니다. 도구 모음에서 녹색 화살표를 클릭하여 실행합니다. 스크립트는 분할된 테이블에 대해 12개씩 24개의 대량 가져오기 작업을 시작합니다. 위에 설정된 대로 SQL Server 기본 데이터 폴더를 열어 데이터 가져오기 진행률을 모니터링할 수 있습니다.
9. PowerShell 스크립트는 시작 및 종료 시간을 보고합니다. 모든 대량 가져오기가 완료되면 종료 시간이 보고됩니다. 대상 로그 폴더를 확인하여 대량 가져오기에 성공했는지, 즉 대상 로그 폴더에 보고된 오류가 없는지 확인합니다.
10. 이제 데이터베이스에서 탐색, 기능 엔지니어링 및 필요에 따라 다른 작업을 수행할 준비가 완료되었습니다. 테이블은 **pickup\_datetime** 필드에 따라 분할되어 있으므로 **WHERE** 절에 **pickup\_datetime** 조건이 포함된 쿼리에서 파티션 구성테이블을 활용합니다.
11. **SQL Server Management Studio**에서 제공된 샘플 스크립트인 **sample\_queries.sql**을 탐색합니다. 샘플 쿼리를 실행하려면 쿼리 줄을 강조 표시한 다음 도구 모음에서 **!실행** 을 클릭합니다.
12. NYC Taxi Trips 데이터가 별도 두 테이블에 로드됩니다. 조인 작업을 향상시키려면 테이블을 인덱싱하는 것이 좋습니다. 샘플 스크립트 **create\_partitioned\_index.sql**은 복합 조인 키 **medallion, hack\_license 및 pickup\_datetime**에 대한 분할된 인덱스를 만듭니다.

## <a name="dbexplore"></a>SQL Server에서 데이터 탐색 및 기능 엔지니어링
이 섹션에서는 이전에 만든 SQL Server 데이터베이스를 사용하여 **SQL Server Management Studio**에서 직접 SQL 쿼리를 실행해 데이터 탐색 및 기능 생성을 수행합니다. **sample\_queries.sql**이라는 샘플 스크립트는 **Sample Scripts** 폴더에서 제공됩니다. 기본값과 다른 경우 스크립트를 수정하여 데이터베이스 이름을 변경합니다. **TaxiNYC**

이 연습에서는 다음을 수행합니다.

* Windows 인증 또는 SQL 인증과 SQL 로그인 이름 및 암호를 사용하여 **SQL Server Management Studio** 에 연결합니다.
* 다양한 기간에 걸쳐 몇몇 필드의 데이터 분포를 탐색합니다.
* 경도 및 위도 필드의 데이터 품질을 조사합니다.
* **tip\_amount**에 따라 이진 및 다중 클래스 분류 레이블을 생성합니다.
* 기능을 생성하고 여정 거리를 계산/비교합니다.
* 두 테이블을 조인하고 모델을 빌드하는 데 사용할 무작위 샘플을 추출합니다.

Azure Machine Learning을 진행할 준비가 되었으면 다음을 수행할 수 있습니다.  

1. 데이터를 추출 및 샘플링할 최종 SQL 쿼리를 저장하고 Azure Machine Learning의 [데이터 가져오기][import-data] 모듈에 쿼리를 직접 복사하여 붙여 넣습니다. 또는
2. 모델을 빌드하는 데 사용할 샘플링 및 엔지니어링된 데이터를 새 데이터베이스 테이블에 유지하고 Azure Machine Learning의 [데이터 가져오기][import-data] 모듈에서 새 테이블을 사용합니다.

이 섹션에서는 데이터를 추출 및 샘플링할 최종 SQL 쿼리를 저장합니다. 두 번째 방법은 [IPython Notebook에서 데이터 탐색 및 기능 엔지니어](#ipnb) 섹션에 설명되어 있습니다.

이전에 병렬 대량 가져오기를 사용하여 채운 테이블에서 행 및 열 수를 신속하게 확인하려면 다음을 실행합니다.

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>탐색: medallion별 여정 분포
이 예제에서는 지정된 기간 내의 여정이 100개가 넘는 medallion(택시 번호)을 식별합니다. 쿼리는 **pickup\_datetime** 파티션 구성표를 조건으로 하므로 분할된 테이블 액세스를 활용합니다. 전체 데이터 세트를 쿼리할 때도 분할된 테이블 및/또는 인덱스 검색을 사용합니다.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>탐색: medallion 및 hack_license별 여정 분포
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>데이터 품질 평가: 경도 및/또는 위도가 잘못된 레코드 확인
이 예제에서는 경도 및/또는 위도 필드에 유효하지 않은 값(라디안이 -90도~90도에 속해야 함)이 포함되거나 (0, 0) 좌표가 있는지 조사합니다.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>탐색: 왕복 vs. 비왕복 여정 분포
이 예제에서는 지정된 기간 동안(또는 전체 연도를 포괄하는 경우 전체 데이터 세트에서) 왕복 여정 수와 비왕복 여정 수를 확인합니다. 이 분포는 나중에 이진 분류 모델링에 사용할 이진 레이블 분포를 반영합니다.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>탐색: 팁 클래스/범위 분포
이 예제에서는 지정된 기간 동안(또는 전체 연도를 포괄하는 경우 전체 데이터 세트에서) 팁 범위 분포를 계산합니다. 이는 나중에 다중 클래스 분류 모델링에 사용할 레이블 클래스의 분포입니다.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

#### <a name="exploration-compute-and-compare-trip-distance"></a>탐색: 여정 거리 컴퓨팅 및 비교
이 예제에서는 승차 및 하차 경도/위도를 SQL 지리 지점으로 변환하고, SQL 지리 지점 차이를 사용하여 여정 거리를 계산한 다음, 비교를 위해 무작위 결과 샘플을 반환합니다. 앞서 설명한 데이터 품질 평가 쿼리를 사용하여 유효한 좌표로만 결과가 제한됩니다.

    SELECT
    pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
    ,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
    ,trip_distance
    ,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
    FROM nyctaxi_trip
    tablesample(0.01 percent)
    WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### <a name="feature-engineering-in-sql-queries"></a>SQL 쿼리의 기능 엔지니어링
레이블 생성 및 지리 변환 탐색 쿼리는 계산 부분을 제거하여 레이블/기능을 생성하는 데에도 사용될 수 있습니다. 추가적인 기능 엔지니어링 SQL 예제는 [IPython Notebook에서 데이터 탐색 및 기능 엔지니어링](#ipnb) 섹션에서 제공됩니다. SQL Server 데이터베이스 인스턴스에서 직접 실행되는 SQL 쿼리를 사용하여 전체 데이터 세트 또는 전체 데이터 세트의 큰 하위 집합에서 기능 생성 쿼리를 실행하는 것이 보다 효율적입니다. **SQL Server Management Studio**, IPython Notebook 또는 로컬이나 원격으로 데이터베이스에 액세스할 수 있는 모든 개발 도구/환경에서 쿼리를 실행할 수 있습니다.

#### <a name="preparing-data-for-model-building"></a>모델 빌드를 위한 데이터 준비
다음 쿼리는 **nyctaxi\_trip** 및 **nyctaxi\_fare** 테이블을 조인하고, 이진 분류 레이블 **tipped**와 다중 클래스 분류 레이블 **tip\_class**를 생성하며, 조인된 전체 데이터 세트에서 1% 무작위 샘플을 추출합니다. Azure의 SQL Server 데이터베이스 인스턴스에서 데이터를 직접 수집하기 위해 이 쿼리를 복사한 다음 [Azure Machine Learning Studio](https://studio.azureml.net)[데이터 가져오기][import-data] 모듈에 직접 붙여넣을 수 있습니다. 잘못된 (0, 0) 좌표가 있는 레코드는 쿼리에서 제외됩니다.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 percent)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>IPython Notebook에서 데이터 탐색 및 기능 엔지니어링
이 섹션에서는 Python과 SQL 쿼리를 모두 사용하여 이전에 만든 SQL Server 데이터베이스에 대해 데이터 탐색 및 기능 생성을 수행합니다. **machine-Learning-data-science-process-sql-story.ipynb**라는 샘플 IPython Notebook은 **Sample IPython Notebooks** 폴더에서 제공됩니다. [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)에서 이 Notebook을 사용할 수도 있습니다.

빅 데이터로 작업하는 권장 시퀀스는 다음과 같습니다.

* 소량의 데이터 샘플을 메모리 내 데이터 프레임으로 읽습니다.
* 샘플링된 데이터를 사용하여 일부 시각화 및 탐색을 수행합니다.
* 샘플링된 데이터를 사용하여 기능 엔지니어링을 실험합니다.
* 더 큰 데이터 탐색, 데이터 조작 및 기능 엔지니어링의 경우 Python을 사용하여 Azure VM의 SQL Server 데이터베이스에 대해 SQL 쿼리를 직접 실행합니다.
* Azure Machine Learning 모델 빌드에 사용할 샘플 크기를 결정합니다.

Azure Machine Learning을 진행할 준비가 되었으면 다음을 수행할 수 있습니다.  

1. 데이터를 추출 및 샘플링할 최종 SQL 쿼리를 저장하고 Azure Machine Learning의 [데이터 가져오기][import-data] 모듈에 쿼리를 직접 복사하여 붙여 넣습니다. 이 방법은 [Azure Machine Learning에서 모델 빌드](#mlmodel) 섹션에 설명되어 있습니다.    
2. 모델을 빌드하는 데 사용할 샘플링 및 엔지니어링된 데이터를 새 데이터베이스 테이블에 유지한 다음 [데이터 가져오기][import-data] 모듈에서 새 테이블을 사용합니다.

다음은 데이터 탐색, 데이터 시각화 및 기능 엔지니어링에 대한 몇 가지 예제입니다. 더 많은 예제는 **Sample IPython Notebooks** 폴더에 있는 샘플 SQL IPython Notebook을 참조하세요.

#### <a name="initialize-database-credentials"></a>데이터베이스 자격 증명 초기화
다음 변수에서 데이터베이스 연결 설정을 초기화합니다.

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>데이터베이스 연결 만들기
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>nyctaxi_trip 테이블의 행 및 열 수 보고
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('nyctaxi_trip')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('nyctaxi_trip')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* 총 행 수 = 173179759  
* 총 열 수 = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>SQL Server 데이터베이스에서 소량의 데이터 샘플 읽기
    t0 = time.time()

    query = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (0.05 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

샘플 테이블을 읽은 시간 = 6.492000초  
검색된 행 및 열 수 = (84952, 21)

#### <a name="descriptive-statistics"></a>기술 통계
이제 샘플링된 데이터를 탐색할 준비가 완료되었습니다. **trip\_distance**(또는 다른 모든) 필드에 대한 기술 통계부터 살펴봅니다.

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>시각화: 상자 그림 예제
다음으로, 여정 거리에 대한 상자 그림을 확인하여 사분위수를 시각화합니다.

    df1.boxplot(column='trip_distance',return_type='dict')

![그릴 #1][1]

#### <a name="visualization-distribution-plot-example"></a>시각화: 분포 그림 예제
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![그릴 #2][2]

#### <a name="visualization-bar-and-line-plots"></a>시각화: 가로 막대형 또는 꺾은선형 그림
이 예에서는 여정 거리를 5개의 bin으로 범주화하고 범주화 결과를 시각화합니다.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

위의 bin 분포를 아래와 같이 가로 막대형 또는 꺾은선형 차트로 그릴 수 있습니다.

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![그릴 #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![그릴 #4][4]

#### <a name="visualization-scatterplot-example"></a>시각화: 산점도 예제
**trip\_time\_in\_secs** 및 **trip\_distance** 사이의 산점도를 표시하여 상관관계가 있는지 확인합니다.

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![그릴 #6][6]

마찬가지로 **rate\_code** 및 **trip\_distance** 간의 관계를 확인할 수 있습니다.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![그릴 #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>SQL에서 데이터 하위 샘플링
[Azure Machine Learning 스튜디오](https://studio.azureml.net)에서 모델을 빌드하기 위해 데이터를 준비할 때 **데이터 가져오기 모듈에서 직접 사용할 SQL 쿼리**를 결정하거나, 간단한 **SELECT * FROM <your\_new\_table\_name>** 을 사용하여 [데이터 가져오기][import-data] 모듈에서 사용할 수 있는 엔지니어링 및 샘플링된 데이터를 새 테이블에 유지할 수 있습니다.

이 섹션에서는 샘플링 및 엔지니어링된 데이터를 유지할 새 테이블을 만듭니다. 모델 빌드를 위한 직접 SQL 쿼리 예제는 SQL Server에서 [데이터 탐색 및 기능 엔지니어링 섹션](#dbexplore) 에서 제공됩니다.

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>샘플 테이블을 만들고 조인된 테이블의 1%로 채우기(테이블이 있는 경우 먼저 해당 테이블 삭제)
이 섹션에서는 **nyctaxi\_trip** 및 **nyctaxi\_fare**를 조인하고, 1% 무작위 샘플을 추출하며, 이름이 **nyctaxi\_one\_percent**인 새 테이블에 샘플링된 데이터를 유지합니다.

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
        INTO nyctaxi_one_percent
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (1 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
        AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>IPython Notebook에서 SQL 쿼리를 사용하여 데이터 탐색
이 섹션에서는 위에서 만든 새 테이블에 유지되는 1%의 샘플링된 데이터를 사용하여 데이터 분포를 탐색합니다. [SQL Server에서 데이터 탐색 및 기능 엔지니어링](#dbexplore) 섹션에 설명된 대로 선택적으로 **TABLESAMPLE**을 사용하여 탐색 샘플을 제한하거나, **pickup\_datetime** 파티션을 사용하여 결과를 지정된 기간으로 제한하여 원래 테이블로 유사한 탐색을 수행할 수 있습니다.

#### <a name="exploration-daily-distribution-of-trips"></a>탐색: 일일 여정 분포
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>탐색: medallion당 여정 분포
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>IPython Notebook에서 SQL 쿼리를 사용하여 기능 생성
이 섹션에서는 이전 섹션에서 만든 1% 샘플 테이블에서 작동하는 SQL 쿼리를 직접 사용하여 새 레이블 및 기능을 생성합니다.

#### <a name="label-generation-generate-class-labels"></a>레이블 생성: 클래스 레이블 생성
다음 예제에서는 모델링에 사용할 두 개의 레이블 집합을 생성합니다.

1. 이진 클래스 레이블 **tipped** (팁이 제공되는지 예측)
2. 다중 클래스 레이블 **tip\_class**(팁 bin 또는 범위 예측)
   
        nyctaxi_one_percent_add_col = '''
            ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
        '''
   
        cursor.execute(nyctaxi_one_percent_add_col)
        cursor.commit()
   
        nyctaxi_one_percent_update_col = '''
            UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''
   
        cursor.execute(nyctaxi_one_percent_update_col)
        cursor.commit()

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>기능 엔지니어링: 범주 열에 대한 개수 기능
이 예제에서는 각 범주를 데이터에서 발생한 횟수로 바꿔 범주 필드를 숫자 필드로 변환합니다.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B AS
        (
            SELECT medallion, hack_license,
                SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
                SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
            FROM nyctaxi_one_percent
            GROUP BY medallion, hack_license
        )

        UPDATE nyctaxi_one_percent
        SET nyctaxi_one_percent.cmt_count = B.cmt_count,
            nyctaxi_one_percent.vts_count = B.vts_count
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion AND A.hack_license = B.hack_license
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>기능 엔지니어링: 숫자 열에 대한 Bin 기능
이 예제에서는 연속 숫자 필드를 사전 설정된 범주 범위로 변환합니다. 즉, 숫자 필드를 범주 필드로 변환합니다.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
        (
            SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
            NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
        )

        UPDATE nyctaxi_one_percent
        SET trip_time_bin = B.BinNumber
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion
        AND A.hack_license = B.hack_license
        AND A.pickup_datetime = B.pickup_datetime
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>기능 엔지니어링: 10진수 위도/경도에서 위치 기능 추출
이 예제에서는 위도 및/또는 경도 필드의 10진수 표현을 세분성(예: 국가, 구/군/시, 동/면, 번지 등)이 서로 다른 지역 필드로 분류합니다. 새 지리 필드는 실제 위치에 매핑되지 않습니다. 지오코드 위치 매핑에 대한 자세한 내용은 [Bing Maps REST 서비스](https://msdn.microsoft.com/library/ff701710.aspx)를 참조하세요.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent
        ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
            l5 varchar(3), l6 varchar(3), l7 varchar(3)
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET l1=round(pickup_longitude,0)
            , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
            , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
            , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
            , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
            , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
            , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="verify-the-final-form-of-the-featurized-table"></a>기능화한 테이블의 최종 양식 확인
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

이제 [Azure Machine Learning](https://studio.azureml.net)에서 모델 빌드 및 모델 배포를 진행할 준비가 완료되었습니다. 이전에 식별된 다음과 같은 예측 문제에 데이터를 사용할 수 있습니다.

1. 이진 분류: 여정에 대해 팁이 지불되었는지 여부를 예측합니다.
2. 다중 클래스 분류: 이전에 정의한 클래스에 따라 지불된 팁의 범위를 예측합니다.
3. 회귀 작업: 여정에 대해 지불된 팁의 금액을 예측합니다.  

## <a name="mlmodel"></a>Azure 기계 학습에서 모델 빌드
모델링 연습을 시작하려면 Azure Machine Learning 작업 영역에 로그인합니다. 기계 학습 작업 영역을 아직 만들지 않은 경우 [Azure Machine Learning 작업 영역 만들기](../studio/create-workspace.md)를 참조하세요.

1. Azure Machine Learning을 시작하려면 [Azure Machine Learning Studio란?](../studio/what-is-ml-studio.md)
2. [Azure Machine Learning Studio](https://studio.azureml.net)에 로그인합니다.
3. 스튜디오 홈 페이지에서는 다양한 정보, 비디오, 자습서, 모듈 참조 링크 및 기타 리소스를 제공합니다. Azure Machine Learning에 대한 자세한 내용은 [Azure Machine Learning 설명서 센터](https://azure.microsoft.com/documentation/services/machine-learning/)를 참조하세요.

일반적인 학습 실험은 다음으로 구성됩니다.

1. **+새** 실험 만들기
2. Azure Machine Learning으로 데이터를 이동합니다.
3. 필요에 따라 데이터를 전처리, 변환 및 조작합니다.
4. 필요에 따라 기능을 생성합니다.
5. 데이터를 학습/유효성 검사/테스트 데이터 세트로 분할하거나, 각각에 대한 별도의 데이터 세트를 만듭니다.
6. 해결할 학습 문제에 따라 하나 이상의 기계 학습 알고리즘을 선택하세요. 이진 분류, 다중 클래스 분류, 회귀)을 선택합니다.
7. 학습 데이터 세트를 사용하여 하나 이상의 모델을 학습합니다.
8. 학습된 모델을 사용하여 유효성 검사 데이터 세트의 점수를 매깁니다.
9. 모델을 평가하여 학습 문제에 대한 관련 메트릭을 계산합니다.
10. 모델을 미세 조정하고 배포할 가장 적합한 모델을 선택합니다.

이 연습에서는 이미 SQL Server에서 데이터를 탐색 및 엔지니어링하고 Azure Machine Learning에서 수집할 샘플 크기를 결정했습니다. 결정한 예측 모델 중 하나 이상을 빌드하려면 다음을 수행합니다.

1. **데이터 입력 및 출력** 섹션에서 제공되는 [데이터 가져오기][import-data] 모듈을 사용하여 Azure Machine Learning으로 데이터를 가져옵니다. 자세한 내용은 [데이터 가져오기][import-data] 참조 페이지를 참조하세요.
   
    ![Azure Machine Learning 데이터 가져오기][17]
2. **속성** 패널에서 **Azure SQL Database**를 **데이터 원본**으로 선택합니다.
3. **데이터베이스 서버 이름** 필드에 데이터베이스 DNS 이름을 입력합니다. 형식: `tcp:<your_virtual_machine_DNS_name>,1433`
4. **데이터베이스 이름** 을 해당 필드에 입력합니다.
5. **서버 사용자 계정 이름**에 **SQL 사용자 이름**을 입력하고, **서버 사용자 계정 암호**에 **암호**를 입력합니다.
7. **데이터베이스 쿼리** 편집 텍스트 영역에서 필요한 데이터베이스 필드를 추출하는 쿼리(레이블과 같은 모든 계산된 필드 포함)를 붙여 넣고 데이터를 원하는 샘플 크기로 다운 샘플링합니다.

SQL Server 데이터베이스에서 직접 데이터를 읽는 이진 분류 실험 예제는 아래 그림에 나와 있습니다. 다중 클래스 분류 및 회귀 문제에 대한 유사한 실험을 생성할 수 있습니다.

![Azure Machine Learning 학습][10]

> [!IMPORTANT]
> 이전 섹션에 제공된 모델링 데이터 추출 및 샘플링 쿼리 예제에서는 **세 가지 모델링 연습에 대한 모든 레이블이 쿼리에 포함되어 있습니다**. 각 모델링 연습의 중요한(필수) 단계는 다른 두 문제에 대한 필요 없는 레이블 및 다른 모든 **목표 누설**을 **제외**하는 것입니다. 예를 들어, 이진 분류를 사용할 때는 레이블 **tipped**를 사용하고, **tip\_class**, **tip\_amount** 및 **total\_amount** 필드를 제외합니다. 이러한 필드는 지불된 팁을 의미하므로 목표 누설입니다.
> 
> 필요 없는 열 또는 목표 누설을 제외하려면 [데이터 세트의 열 선택][select-columns] 모듈 또는 [메타데이터 편집][edit-metadata]을 사용하면 됩니다. 자세한 내용은 [데이터 세트의 열 선택][select-columns] 및 [메타데이터 편집][edit-metadata] 참조 페이지를 참조하세요.
> 
> 

## <a name="mldeploy"></a>Azure 기계 학습에서 모델 배포
모델이 준비된 경우 실험에서 직접 웹 서비스로 쉽게 배포할 수 있습니다. Azure Machine Learning 웹 서비스 배포에 대한 자세한 내용은 [Azure Machine Learning 웹 서비스 배포](../studio/publish-a-machine-learning-web-service.md)를 참조하세요.

새 웹 서비스를 배포하려면 다음을 수행해야 합니다.

1. 점수 매기기 실험을 만듭니다.
2. 웹 서비스를 배포합니다.

**완료된** 학습 실험에서 점수 매기기 실험을 만들려면 아래쪽 작업 모음에서 **점수 매기기 실험 만들기**를 클릭합니다.

![Azure 점수 매기기][18]

Azure Machine Learning에서는 학습 실험의 구성 요소를 기반으로 점수 매기기 실험을 만듭니다. 특히 다음 작업을 수행합니다.

1. 학습된 모델을 저장하고 모델 학습 모듈을 제거합니다.
2. 필요한 입력 데이터 스키마를 나타내는 논리적 **입력 포트** 를 식별합니다.
3. 필요한 웹 서비스 출력 스키마를 나타내는 논리적 **출력 포트** 를 식별합니다.

점수 매기기 실험을 만들 때 필요에 따라 검토하고 조정합니다. 일반적인 조정은 입력 데이터 세트 및/또는 쿼리를 레이블 필드를 제외한 것으로 바꾸는 것입니다. 레이블 필드는 서비스를 호출할 때 사용할 수 없기 때문입니다. 또한 입력 데이터 세트 및/또는 쿼리 크기를 입력 스키마를 나타내는 데 충분한 정도의 몇몇 레코드로 줄이는 것이 좋습니다. 출력 포트의 경우 일반적으로 [데이터 세트의 열 선택][select-columns] 모듈을 사용하여 모든 입력 필드를 제외하고 **점수가 매겨진 레이블** 및 **점수가 매겨진 확률**만 출력에 포함합니다.

샘플 점수 매기기 실험은 아래 그림에 나와 있습니다. 배포할 준비가 되면 아래쪽 작업 모음에서 **웹 서비스 게시** 단추를 클릭합니다.

![Azure Machine Learning 게시][11]

이 연습 자습서에서는 Azure 데이터 과학 환경을 만들고, 대용량 공용 데이터 세트로 데이터 취득에서 모델 학습 및 Azure Machine Learning 웹 서비스 배포에 이르는 모든 과정을 수행했습니다.

### <a name="license-information"></a>라이선스 정보
이 샘플 연습 및 이와 함께 제공되는 스크립트와 IPython Notebook은 MIT 라이선스에 따라 Microsoft에서 공유한 것입니다. 자세한 내용은 GitHub의 샘플 코드 디렉터리에 있는 LICENSE.txt 파일을 참조하세요.

### <a name="references"></a>참조
• [Andrés Monroy NYC Taxi Trips 다운로드 페이지](https://www.andresmh.com/nyctaxitrips/)  
• [Chris Whong의 FOILing NYC Taxi Trip 데이터](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi 및 Limousine 수수료 연구 및 통계](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
