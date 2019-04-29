---
title: Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1에 데이터 로드 | Microsoft Docs
description: Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1에 데이터 복사
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 522b9743af28dedb2aec5682a1ae95b9d52ad2d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549118"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1에 데이터 로드

[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)(이전의 Azure Data Lake Store)은 빅 데이터 분석 워크로드를 위한 엔터프라이즈 수준의 하이퍼스케일 리포지토리입니다. Data Lake Storage Gen1을 사용하면 크기, 유형 및 수집 속도에 관계없이 모든 데이터를 캡처할 수 있습니다. 데이터는 운영 및 탐색적 분석을 위해 단일 위치에서 캡처됩니다.

Azure Data Factory는 완전히 관리되는 클라우드 기반 데이터 통합 서비스입니다. 분석 솔루션을 빌드할 때 서비스를 사용하여 레이크를 기존 시스템의 데이터로 채우면 시간을 절약할 수 있습니다.

Azure Data Factory를 사용하여 Data Lake Storage Gen1으로 데이터를 로드하면 다음과 같은 이점이 있습니다.

* **간편한 설정**: 스크립팅이 필요 없는 직관적인 5 단계 마법사.
* **다양 한 데이터 저장소 지원**: 다양 한 온-프레미스 및 클라우드 기반 데이터 저장소에 대 한 기본 제공 지원 합니다. 자세한 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.
* **보안 및 규정 준수**: 데이터가는 HTTPS 또는 ExpressRoute를 통해 전송 됩니다. 글로벌 서비스가 제공되므로 데이터가 지리적 경계를 벗어나지 않습니다.
* **고성능**: 최대 데이터 레이크 저장소 Gen1에 1-g B/s 데이터 로드 속도입니다. 자세한 내용은 [복사 작업 성능](copy-activity-performance.md)을 참조하세요.

이 문서에서는 Data Factory 복사 데이터 도구를 사용하여 _Amazon S3의 데이터를 Data Lake Storage Gen1으로 로드_하는 방법을 설명합니다. 다른 데이터 저장소 유형에서 데이터를 복사할 때도 이와 유사한 단계를 따를 수 있습니다.

> [!NOTE]
> 자세한 내용은 [Azure Data Factory를 사용하여 Data Lake Storage Gen1 간에 데이터 복사](connector-azure-data-lake-store.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 데이터 레이크 저장소 Gen1 계정: 지침을 참조 하는 Data Lake 저장소 Gen1 계정이 없으면 [Data Lake 저장소 Gen1 계정을 만들려면](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account)합니다.
* Amazon S3: 이 문서는 Amazon S3에서 데이터를 복사하는 방법을 보여 줍니다. 다음과 같은 유사한 단계를 수행하여 다른 데이터 저장소를 사용할 수 있습니다.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. 왼쪽 메뉴에서 **리소스 만들기** > **분석** > **Data Factory**를 선택합니다.
   
   !["새로 만들기" 창에서 데이터 팩터리 선택](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **새 데이터 팩터리** 페이지에서 다음 그림에 표시된 필드의 값을 제공합니다. 
      
   ![새 데이터 팩터리 페이지](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **이름**: Azure Data Factory의 전역적으로 고유 이름을 입력합니다. "데이터 팩터리 이름 \"LoadADLSG1Demo\"를 사용할 수 없습니다" 오류가 발생하면 데이터 팩터리의 다른 이름을 입력합니다. 예를 들어 _**yourname**_**ADFTutorialDataFactory**라는 이름을 사용할 수 있습니다. 데이터 팩터리를 다시 만들어 봅니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
    * **구독**: 데이터 팩터리를 만들 Azure 구독을 선택합니다. 
    * **리소스 그룹**: 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 옵션을 선택하고 리소스 그룹의 이름을 입력합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
    * **버전**: **V2**를 선택합니다.
    * **위치**: 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소가 다른 위치 및 지역에 있어도 됩니다. 이러한 데이터 저장소는 Azure Data Lake Storage Gen1, Azure Storage, Azure SQL Database 등을 포함합니다.

3. **만들기**를 선택합니다.
4. 만들기가 완료되면 데이터 팩터리로 이동합니다. 다음 그림과 같이 **데이터 팩터리** 홈페이지가 표시됩니다. 
   
   ![데이터 팩터리 홈페이지](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   **작성 및 모니터링** 타일을 선택하여 별도의 탭에서 데이터 통합 애플리케이션을 시작합니다.

## <a name="load-data-into-data-lake-storage-gen1"></a>Data Lake Storage Gen1에 데이터 로드

1. **시작** 페이지에서 **데이터 복사** 타일을 선택하여 데이터 복사 도구를 시작합니다. 

   ![데이터 복사 도구 타일](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. **속성** 페이지에서 **작업 이름** 필드를 **CopyFromAmazonS3ToADLS**로 지정하고 **다음**을 선택합니다.

    ![속성 페이지](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. **원본 데이터 저장소** 페이지에서 **+ 새 연결 만들기**를 클릭합니다.

    ![원본 데이터 저장소 페이지](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    **Amazon S3**, **계속**을 차례로 선택합니다.
    
    ![원본 데이터 저장소 s3 페이지](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. **Amazon S3 연결 지정** 페이지에서 다음 단계를 수행합니다. 
   1. **액세스 키 ID** 값을 지정합니다.
   2. **비밀 액세스 키** 값을 지정합니다.
   3. **마침**을 선택합니다.
   
      ![Amazon S3 계정 지정](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. 새 연결이 표시됩니다. **다음**을 선택합니다.
   
   ![Amazon S3 계정 지정](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. **입력 파일 또는 폴더 선택** 페이지에서, 복사하려는 폴더 및 파일로 이동합니다. 폴더/파일을 선택하고 **선택**, **다음**을 차례로 선택합니다.

    ![입력 파일 또는 폴더 선택](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. **재귀적으로 파일 복사** 및 **이진 복사**(있는 그대로 파일 복사) 옵션을 선택하여 복사 동작을 선택합니다. **다음**을 선택합니다.

    ![출력 폴더 지정](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. **대상 데이터 저장소** 페이지에서 **+ 새 연결 만들기**를 클릭한 다음, **Azure Data Lake Storage Gen1**을 선택하고 **계속**을 선택합니다.

    ![대상 데이터 저장소 페이지](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. **New Linked Service (Azure Data Lake Storage Gen1)**[새 연결된 서비스(Azure Data Lake Storage Gen1)] 페이지에서 다음 단계를 수행합니다. 

   1. **Data Lake Store 계정 이름**으로 Data Lake Storage Gen1 계정을 선택합니다.
   2. **테넌트**를 지정하고 마침을 선택합니다.
   3. **다음**을 선택합니다.
   
   > [!IMPORTANT]
   > 이 연습에서는 Azure 리소스용 관리 ID를 사용하여 Data Lake Storage Gen1 계정을 인증합니다. [다음 지침](connector-azure-data-lake-store.md#managed-identity)에 따라 Data Lake Storage Gen1에서 MSI에 적절한 권한을 부여합니다.
   
   ![Data Lake Storage Gen1 계정 지정](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. **출력 파일 또는 폴더 선택** 페이지에서 출력 폴더 이름으로 **copyfroms3**를 입력하고 **다음**을 선택합니다. 

    ![출력 폴더 지정](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. **설정** 페이지에서 **다음**을 선택합니다.

    ![설정 페이지](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. **요약** 페이지에서 설정을 검토하고, **다음**을 선택합니다.

    ![요약 페이지](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. **배포 페이지**에서 **모니터**를 선택하여 파이프라인(작업)을 모니터링합니다.

    ![배포 페이지](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. **작업** 열에는 활동 실행 세부 정보를 보고 파이프라인을 다시 실행하기 위한 링크가 있습니다.

    ![파이프라인 실행 모니터링](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기** 링크를 선택합니다. 파이프라인에는 하나의 작업(복사 작업)만 있으므로 하나의 항목만 표시됩니다. 파이프라인 실행 보기로 전환하려면 위쪽의 **파이프라인** 링크를 선택합니다. **새로 고침**을 선택하여 목록을 새로 고칩니다. 

    ![작업 실행 모니터링](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. 각 복사 작업의 실행 세부 정보를 모니터링하려면 작업 모니터링 보기의 **작업** 아래에서 **세부 정보** 링크를 선택합니다. 원본에서 싱크로 복사되는 데이터 볼륨, 데이터 처리량, 해당 기간의 실행 단계, 사용되는 구성 등의 세부 정보를 모니터링할 수 있습니다.

    ![작업 실행 세부 정보 모니터링](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. 데이터가 Data Lake Storage Gen1 계정에 복사되었는지 확인합니다. 

    ![Data Lake Storage Gen1 출력 확인](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>다음 단계

다음 문서로 이동하여 Data Lake Storage Gen1 지원에 대해 알아봅니다. 

> [!div class="nextstepaction"]
>[Azure Data Lake Storage Gen1 커넥터](connector-azure-data-lake-store.md).
