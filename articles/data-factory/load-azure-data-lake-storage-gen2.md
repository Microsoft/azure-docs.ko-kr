---
title: Azure Data Lake Storage Gen2에 데이터 로드
description: Azure Data Factory를 사용하여 Azure Data Lake Storage Gen2에 데이터 복사
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 075778e2650d3b1f67447078eb6cb130849bbb35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593784"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Data Lake Storage Gen2에 데이터 로드

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2는 [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)를 기반으로 하는 빅 데이터 분석 전용의 기능 세트입니다. 이를 사용하면 파일 시스템 및 개체 스토리지 패러다임을 모두 사용하여 데이터를 조작할 수 있습니다.

ADF(Azure Data Factory)는 완전 관리형 클라우드 기반 데이터 통합 서비스입니다. 분석 솔루션을 빌드할 때 서비스를 사용하여 풍부한 온-프레미스 및 크라우드 기반 데이터 저장소의 데이터로 레이크를 채우고 시간을 절약할 수 있습니다. 지원되는 커넥터의 자세한 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

Azure Data Factory는 스케일 아웃, 관리되는 데이터 이동 솔루션을 제공합니다. ADF의 스케일 아웃 아키텍처로 인해 높은 처리량으로 데이터를 수집할 수 있습니다. 자세한 내용은 [복사 작업 성능](copy-activity-performance.md)을 참조하세요.

이 아티클에서는 Data Factory 복사 데이터 도구를 사용하여 _Amazon Web Services S3 서비스_ 의 데이터를 _Azure Data Lake Storage Gen2_ 로 로드하는 방법을 설명합니다. 다른 데이터 저장소 유형에서 데이터를 복사할 때도 이와 유사한 단계를 따를 수 있습니다.

>[!TIP]
>Azure Data Lake Storage Gen1에서 Gen2로 데이터를 복사하는 방법은 [이 연습](load-azure-data-lake-storage-gen2-from-gen1.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Data Lake Storage Gen2를 사용하는 Azure Storage 계정: Storage 계정이 없는 경우 [계정을 만듭니다](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* 데이터를 포함하는 S3 버킷을 포함한 AWS 계정: 이 아티클에서는 Amazon S3에서 데이터를 복사하는 방법을 보여줍니다. 다음과 같은 유사한 단계를 수행하여 다른 데이터 저장소를 사용할 수 있습니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. 왼쪽 메뉴에서 **리소스 만들기** > **통합** > **Data Factory** 를 선택합니다.
   
   !["새로 만들기" 창에서 데이터 팩터리 선택](./media/doc-common-process/new-azure-data-factory-menu.png)

2. **새 데이터 팩터리** 페이지에서 다음 필드에 대한 값을 제공합니다.
 
    * **Name**: Azure Data Factory의 전역적으로 고유 이름을 입력합니다. “데이터 팩터리 이름 *YourDataFactoryName* 을 사용할 수 없습니다” 오류가 발생하면 데이터 팩터리의 다른 이름을 입력합니다. 예를 들어 _**yourname**_**ADFTutorialDataFactory** 라는 이름을 사용할 수 있습니다. 데이터 팩터리를 다시 만들어 봅니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
    * **구독**: 데이터 팩터리를 만들 Azure 구독을 선택합니다. 
    * **리소스 그룹**: 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 옵션을 선택하고 리소스 그룹의 이름을 입력합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요.  
    * **버전**: **V2** 를 선택합니다.
    * **위치**: 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소가 다른 위치 및 지역에 있어도 됩니다. 

3. **만들기** 를 선택합니다.

4. 만들기가 완료되면 데이터 팩터리로 이동합니다. 다음 그림과 같이 **데이터 팩터리** 홈페이지가 표시됩니다. 
   
   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="작성자 및 모니터링 타일이 있는 Azure Data Factory의 홈페이지.":::

   **작성 및 모니터링** 타일을 선택하여 별도의 탭에서 데이터 통합 애플리케이션을 시작합니다.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에 데이터 로드

1. **시작** 페이지에서 **데이터 복사** 타일을 선택하여 데이터 복사 도구를 시작합니다.

2. **속성** 페이지에서 **작업 이름** 필드를 **CopyFromAmazonS3ToADLS** 로 지정하고 **다음** 을 선택합니다.

    ![속성 페이지](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. **원본 데이터 저장소** 페이지에서 **+ 새 연결 만들기** 를 클릭합니다. 커넥터 갤러리에서 **Amazon S3** 을 선택하고 **계속** 을 선택합니다.
    
    ![원본 데이터 저장소 s3 페이지](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. **새 연결된 서비스(Amazon S3)** 페이지에서 다음 단계를 수행합니다.

   1. **액세스 키 ID** 값을 지정합니다.
   2. **비밀 액세스 키** 값을 지정합니다.
   3. **연결 테스트** 를 클릭하여 설정의 유효성을 검사한 다음, **만들기** 를 선택합니다.

      ![Amazon S3 계정 지정](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   4. 새 AmazonS3 연결이 만들어진 것을 볼 수 있습니다. **다음** 을 선택합니다. 

5. **입력 파일 또는 폴더 선택** 페이지에서, 복사하려는 폴더 및 파일로 이동합니다. 폴더/파일을 선택한 다음 **선택** 을 선택합니다.

    ![입력 파일 또는 폴더 선택](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. **재귀적으로** 및 **이진 복사** 옵션을 선택하여 복사 동작을 지정합니다. **다음** 을 선택합니다.

    ![스크린샷은 ‘이진 복사’ 및 ‘재귀적으로’를 선택할 수 있는 입력 파일 또는 폴더를 선택을 보여 줍니다.](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. **대상 데이터 스토리지** 페이지에서 **+ 새 연결 만들기** 를 클릭한 다음, **Azure Data Lake Storage Gen2** 를 선택하고 **계속** 을 선택합니다.

    ![대상 데이터 저장소 페이지](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. **New Linked Service(Azure Data Lake Storage Gen2)** [새 연결된 서비스(Azure Data Lake Storage Gen2)] 페이지에서 다음 단계를 수행합니다.

   1. "스토리지 계정 이름" 드롭다운 목록에서 Data Lake Storage Gen2 지원 계정을 선택합니다.
   2. **만들기** 를 선택하여 연결을 만듭니다. 그런 후 **다음** 을 선택합니다.   

        ![Azure Data Lake Storage Gen2 계정 지정](./media/load-azure-data-lake-storage-gen2/specify-azure-data-lake-storage.png)

9. **출력 파일 또는 폴더 선택** 페이지에서 출력 폴더 이름으로 **copyfroms3** 를 입력하고 **다음** 을 선택합니다. ADF에서 복사 중 해당 ADLS Gen2 파일 시스템 및 하위 폴더를 만듭니다(없는 경우).

    ![스크린샷은 입력한 폴더 경로를 보여 줍니다.](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. **설정** 페이지에서 **다음** 을 선택하여 기본 설정을 사용합니다.

    ![설정 페이지](./media/load-azure-data-lake-storage-gen2/copy-settings.png)

11. **요약** 페이지에서 설정을 검토하고, **다음** 을 선택합니다.

    ![요약 페이지](./media/load-azure-data-lake-storage-gen2/copy-summary.png)

12. **배포 페이지** 에서 **모니터** 를 선택하여 파이프라인(작업)을 모니터링합니다. 
 
13. 파이프라인 실행이 성공적으로 완료되면 수동 트리거로 트리거된 파이프라인 실행이 표시됩니다. **파이프라인 이름** 열 아래의 링크를 사용하여 활동 세부 정보를 보고 파이프라인을 다시 실행할 수 있습니다.

    ![파이프라인 실행 모니터링](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. 파이프라인 실행과 관련된 활동 실행을 보려면 파이프라인 이름 열에서 **CopyFromAmazonS3ToADLS** 링크를 선택합니다. 복사 작업에 대한 자세한 내용을 보려면 활동 이름 열에서 **세부 정보** 링크(안경 아이콘)를 선택합니다. 원본에서 싱크로 복사된 데이터 양, 데이터 처리량, 해당 기간의 실행 단계, 사용된 구성과 같은 세부 정보를 모니터링할 수 있습니다.
 
    ![작업 실행 모니터링](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)
    
    ![작업 실행 세부 정보 모니터링](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

15. 보기를 새로 고치려면 새로 고침을 선택합니다. 파이프라인 실행 보기로 돌아가려면 위쪽에 있는 **모든 파이프라인 실행** 을 선택합니다.

16. 데이터가 Data Lake Storage Gen2 계정에 복사되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

* [복사 작업 개요](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2 커넥터](connector-azure-data-lake-storage.md)
