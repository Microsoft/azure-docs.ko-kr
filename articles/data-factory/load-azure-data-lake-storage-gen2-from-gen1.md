---
title: Azure Data Lake Storage Gen1에서 Gen2로 데이터 복사
description: Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1에서 Gen2로 데이터 복사
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2019
ms.openlocfilehash: 5809307ff8e047ebc6120cb5ebf36590f2a2a51a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444016"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1에서 Gen2로 데이터 복사

Azure Data Lake Storage Gen2은 [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)에 기본 제공 되는 빅 데이터 분석 전용 기능 집합입니다. 파일 시스템 및 개체 저장소 패러다임을 모두 사용 하 여 데이터와 상호 작용 하는 데 사용할 수 있습니다.

현재 Azure Data Lake Storage Gen1를 사용 하는 경우 Azure Data Factory를 사용 하 여 Data Lake Storage Gen1에서 Gen2로 데이터를 복사 하 여 Azure Data Lake Storage Gen2를 평가할 수 있습니다.

Azure Data Factory는 완전히 관리되는 클라우드 기반 데이터 통합 서비스입니다. 서비스를 사용 하 여 다양 한 온-프레미스 및 클라우드 기반 데이터 저장소 집합의 데이터로 lake를 채우고 분석 솔루션을 빌드할 때 시간을 절약할 수 있습니다. 지원 되는 커넥터 목록은 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)표를 참조 하세요.

Azure Data Factory는 스케일 아웃, 관리되는 데이터 이동 솔루션을 제공합니다. Data Factory의 스케일 아웃 아키텍처로 인해 높은 처리량으로 데이터를 수집할 수 있습니다. 자세한 내용은 [복사 작업 성능](copy-activity-performance.md)을 참조 하세요.

이 문서에서는 Data Factory 데이터 복사 도구를 사용 하 여 Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 데이터를 복사 하는 방법을 보여 줍니다. 다른 데이터 저장소 유형에서 데이터를 복사할 때도 이와 유사한 단계를 따를 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 데이터가 있는 Azure Data Lake Storage Gen1 계정
* Data Lake Storage Gen2 사용 하도록 설정 된 Azure Storage 계정 저장소 계정이 없는 경우 [계정을 만듭니다](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. 왼쪽 메뉴에서 **리소스 만들기** > **데이터 + 분석** > **Data Factory**를 선택 합니다.
   
   ![새 창에서 Data Factory 선택](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **새 데이터 팩터리** 페이지에서 다음 이미지에 표시 된 필드에 대한 값을 제공 합니다. 
      
   ![새 데이터 팩터리 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **이름**: Azure 데이터 팩터리의 전역 고유 이름을 입력합니다. "데이터 팩터리 이름 \"LoadADLSDemo\"를 사용할 수 없습니다" 오류가 발생하면 데이터 팩터리의 다른 이름을 입력합니다. 예를 들어 _**yourname**_ **ADFTutorialDataFactory**를 사용합니다. 데이터 팩터리를 다시 만듭니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
    * **구독**: 데이터 팩터리를 만들 Azure 구독을 선택합니다. 
    * **리소스 그룹**: 드롭다운 목록에서 기존 리소스 그룹을 선택 합니다. **새로 만들기** 옵션을 선택 하 고 리소스 그룹의 이름을 입력할 수도 있습니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요. 
    * **버전**: **V2**를 선택합니다.
    * **위치**: 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소가 다른 위치 및 지역에 있어도 됩니다. 

3. **만들기**를 선택합니다.
4. 만들기가 완료 되 면 데이터 팩터리로 이동 합니다. 다음 그림과 같이 **데이터 팩터리** 홈페이지가 표시됩니다. 
   
   ![데이터 팩터리 홈페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. **작성자 & 모니터** 타일을 선택 하 여 별도의 탭에서 데이터 통합 응용 프로그램을 시작 합니다.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에 데이터 로드

1. **시작** 페이지에서 **데이터 복사** 타일을 선택 하 여 데이터 복사 도구를 시작 합니다. 

   ![데이터 복사 도구 타일](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. **속성** 페이지에서 **작업 이름** 필드에 대해 **CopyFromADLSGen1ToGen2** 를 지정 합니다. **다음**을 선택합니다.

    ![속성 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. **원본 데이터 저장소** 페이지에서 **+ 새 연결 만들기**를 선택 합니다.

    ![원본 데이터 저장소 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. 커넥터 갤러리에서 **Azure Data Lake Storage Gen1**을 선택하고 **계속**을 선택합니다.
    
    ![원본 데이터 저장소 Azure Data Lake Storage Gen1 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. **Azure Data Lake Storage Gen1 연결 지정** 페이지에서 다음 단계를 수행 합니다.

   a. 계정 이름으로 Data Lake Storage Gen1을 선택하고 **Tenant**를 지정하거나 유효한지 검사합니다.
  
   b. **연결 테스트** 를 선택 하 여 설정의 유효성을 검사 합니다. 그런 다음, **마침**을 선택합니다.
  
   다. 새 연결이 생성 된 것을 볼 수 있습니다. **다음**을 선택합니다.
   
   > [!IMPORTANT]
   > 이 연습에서는 Azure 리소스에 관리 되는 id를 사용 하 여 Azure Data Lake Storage Gen1을 인증 합니다. Azure Data Lake Storage Gen1에서 관리 되는 id에 적절 한 권한을 부여 하려면 [다음 지침](connector-azure-data-lake-store.md#managed-identity)을 따르세요.
   
   ![Azure Data Lake Storage Gen1 계정 지정](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. **입력 파일 또는 폴더 선택** 페이지에서 복사 하려는 폴더와 파일을 찾습니다. 폴더나 파일을 선택 하 **고 선택을 선택 합니다.**

    ![입력 파일 또는 폴더 선택](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. **재귀적으로 파일 복사** 및 **이진 복사** 옵션을 선택 하 여 복사 동작을 지정 합니다. **다음**을 선택합니다.

    ![출력 폴더 지정](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. **대상 데이터 저장소** 페이지에서 **+ 새 연결 만들기** > **Azure Data Lake Storage Gen2** 선택 하 > **계속**합니다.

    ![대상 데이터 저장소 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. **Azure Data Lake Storage Gen2 연결 지정** 페이지에서 다음 단계를 수행 합니다.

   a. **저장소 계정 이름** 드롭다운 목록에서 Data Lake Storage Gen2 사용할 수 있는 계정을 선택 합니다.
   
   b. **마침**을 선택하여 연결을 만듭니다. 그런 후 **다음**을 선택합니다.
   
   ![Azure Data Lake Storage Gen2 계정 지정](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. **출력 파일 또는 폴더 선택** 페이지에서 출력 폴더 이름으로 **copyfromadlsgen1** 를 입력 하 고 **다음**을 선택 합니다. Data Factory은 복사 하는 동안 해당 Azure Data Lake Storage Gen2 파일 시스템 및 하위 폴더를 만듭니다 (없는 경우).

    ![출력 폴더 지정](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. **설정** 페이지에서 **다음**을 선택하여 기본 설정을 사용합니다.

12. **요약** 페이지에서 설정을 검토 하 고 **다음**을 선택 합니다.

    ![요약 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. **배포 페이지**에서 **모니터** 를 선택 하 여 파이프라인을 모니터링 합니다.

    ![배포 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. **작업** 열에는 활동 실행 세부 정보를 보고 파이프라인을 다시 실행하기 위한 링크가 있습니다.

    ![파이프라인 실행 모니터링](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기** 링크를 선택합니다. 파이프라인에는 하나의 작업(복사 작업)만 있으므로 하나의 항목만 표시됩니다. 파이프라인 실행 보기로 전환하려면 위쪽의 **파이프라인** 링크를 선택합니다. **새로 고침**을 선택하여 목록을 새로 고칩니다. 

    ![작업 실행 모니터링](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. 각 복사 작업의 실행 세부 정보를 모니터링하려면 작업 모니터링 보기의 **작업** 아래에서 **세부 정보** 링크(안경 이미지)를 선택합니다. 원본에서 싱크로 복사 되는 데이터 볼륨, 데이터 처리량, 해당 기간에 대한 실행 단계 및 사용 된 구성과 같은 세부 정보를 모니터링할 수 있습니다.

    ![작업 실행 세부 정보 모니터링](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. 데이터가 Azure Data Lake Storage Gen2 계정에 복사 되었는지 확인 합니다.

## <a name="best-practices"></a>모범 사례

Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2 일반으로 업그레이드를 평가 하려면 [빅 데이터 분석 솔루션을 Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 업그레이드](../storage/blobs/data-lake-storage-upgrade.md)를 참조 하세요. 다음 섹션에서는 Data Lake Storage Gen1에서 Data Lake Storage Gen2으로 데이터를 업그레이드 하는 데 Data Factory를 사용 하는 모범 사례를 소개 합니다.

### <a name="data-partition-for-historical-data-copy"></a>기록 데이터 복사를 위한 데이터 파티션

- Data Lake Storage Gen1의 총 데이터 크기가 30tb 미만이 고 파일 수가 100만 보다 작은 경우 단일 복사 작업 실행에서 모든 데이터를 복사할 수 있습니다.
- 복사할 데이터가 많은 경우 일괄 처리로 데이터 마이그레이션을 관리 하 고 각 작업이 특정 시간 프레임 내에서 완료 되도록 하려면 데이터를 분할 합니다. 또한 분할을 통해 예기치 않은 문제가 발생 하는 위험을 줄일 수 있습니다.

개념 증명을 사용 하 여 종단 간 솔루션을 확인 하 고 사용자 환경에서 복사 처리량을 테스트 합니다. 주요 개념 증명 단계: 

1. 단일 복사 작업을 사용 하 여 하나의 Data Factory 파이프라인을 만들어 Data Lake Storage Gen1에서 Data Lake Storage Gen2로 여러 개의 데이터를 복사 하 여 복사 성능 기준선을 가져옵니다. [DIUs (data integration unit)](copy-activity-performance.md#data-integration-units) 를 128로 시작 합니다. 
2. 1 단계에서 얻은 복사 처리량에 따라 전체 데이터 마이그레이션에 필요한 예상 시간을 계산 합니다. 
3. 필드 컨트롤 테이블을 만들고 마이그레이션할 파일을 분할 하는 파일 필터를 정의 합니다. 파일을 분할 하는 방법은 다음과 같습니다. 

    - 와일드 카드 필터를 사용 하 여 폴더 이름 또는 폴더 이름으로 분할 합니다. 연결합니다(권장).
    - 파일의 마지막 수정 시간을 기준으로 파티션입니다.

### <a name="network-bandwidth-and-storage-io"></a>네트워크 대역폭 및 저장소 i/o 

Data Lake Storage Gen1에서 데이터를 읽고 Data Lake Storage Gen2에 데이터를 쓰는 Data Factory 복사 작업의 동시성을 제어할 수 있습니다. 이러한 방식으로 해당 저장소 i/o에서 사용을 관리 하 여 마이그레이션하는 동안 Data Lake Storage Gen1의 정상적인 비즈니스 작업에 영향을 주지 않도록 할 수 있습니다.

### <a name="permissions"></a>권한 

Data Factory에서 [Data Lake Storage Gen1 커넥터](connector-azure-data-lake-store.md) 는 Azure 리소스 인증에 대한 서비스 주체 및 관리 id를 지원 합니다. [Data Lake Storage Gen2 커넥터](connector-azure-data-lake-storage.md) 는 Azure 리소스 인증에 대한 계정 키, 서비스 주체 및 관리 id를 지원 합니다. 필요한 모든 파일 또는 Acl (액세스 제어 목록)을 탐색 하 고 복사할 수 Data Factory 있도록 하려면 사용자가 제공 하는 계정에 대한 충분 한 권한을 부여 하 여 모든 파일을 액세스, 읽기 또는 작성 하 고 원하는 경우 Acl을 설정 합니다. 마이그레이션 기간 중에 슈퍼 사용자 또는 소유자 역할을 부여 합니다. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Data Lake Storage Gen1에서 Acl 유지

Data Lake Storage Gen1에서 Data Lake Storage Gen2로 업그레이드 하는 경우 데이터 파일과 함께 Acl을 복제 하려면 [Data Lake Storage Gen1에서 Acl 유지](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)를 참조 하세요. 

### <a name="incremental-copy"></a>증분 복사 

여러 가지 방법을 사용 하 여 Data Lake Storage Gen1에서 새로운 파일 또는 업데이트 된 파일만 로드할 수 있습니다.

- 시간이 분할 된 폴더 또는 파일 이름으로 새 파일 또는 업데이트 된 파일을 로드 합니다. 예를 들면/2019/05/13/*입니다.
- LastModifiedDate에서 새 파일 또는 업데이트 된 파일을 로드 합니다.
- 타사 도구나 솔루션을 통해 새 파일 또는 업데이트 된 파일을 식별 합니다. 그런 다음 매개 변수 또는 테이블 또는 파일을 통해 파일 또는 폴더 이름을 Data Factory 파이프라인으로 전달 합니다. 

증분 로드를 수행 하는 적절 한 빈도는 Azure Data Lake Storage Gen1의 총 파일 수와 매번 로드 되는 새 파일 또는 업데이트 된 파일의 양에 따라 달라 집니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [복사 작업 개요](copy-activity-overview.md)
> [Azure Data Lake Storage Gen1 커넥터](connector-azure-data-lake-store.md)
> [Azure Data Lake Storage Gen2 커넥터](connector-azure-data-lake-storage.md)