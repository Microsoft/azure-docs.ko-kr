---
title: Azure Data Factory에서 Azure 데이터 탐색기로 데이터 복사
description: 이 문서에서는 Azure Data Factory 복사 도구를 사용 하 여 데이터를 Azure 데이터 탐색기 수집 (로드) 하는 방법에 대해 알아봅니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 860b1a579d9c8cee6c6e80ae4c4e7fdd7949d5c7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300593"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Azure 데이터 탐색기에 데이터 복사 

Azure 데이터 탐색기는 빠르고 완전히 관리 되는 데이터 분석 서비스입니다. 응용 프로그램, 웹 사이트 및 IoT 장치와 같은 많은 원본에서 스트리밍하는 대용량 데이터에 대한 실시간 분석을 제공 합니다. Azure 데이터 탐색기를 통해 데이터를 반복적으로 탐색 하 고 패턴 및 비정상을 식별 하 여 제품을 개선 하 고, 고객 환경을 개선 하 고, 장치를 모니터링 하 고, 작업을 향상 시킬 수 새 질문을 탐색 하 고 몇 분 안에 답변을 얻을 수 있습니다. 

Azure Data Factory는 완전히 관리 되는 클라우드 기반 데이터 통합 서비스입니다. 이를 사용 하 여 Azure 데이터 탐색기 데이터베이스를 기존 시스템의 데이터로 채울 수 있습니다. 분석 솔루션을 빌드할 때 시간을 절약 하는 데 도움이 될 수 있습니다.

Azure 데이터 탐색기에 데이터를 로드 하는 경우 Data Factory는 다음과 같은 이점을 제공 합니다.

* **간편한 설치**: 스크립팅이 필요 없는 직관적인 5 단계 마법사를 다운로드 합니다.
* **다양한 데이터 저장소 지원**: 다양 한 온-프레미스 및 클라우드 기반 데이터 저장소 집합에 대한 기본 제공 지원을 받으세요. 자세한 목록은 [지원되는 데이터 저장소](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) 표를 참조하세요.
* **보안 및 규정 준수**: 데이터는 HTTPS 또는 Azure Express 경로를 통해 전송 됩니다. 글로벌 서비스가 제공되므로 데이터가 지리적 경계를 벗어나지 않습니다.
* **고성능**: 데이터 로드 속도는 Azure 데이터 탐색기 최대 1Gbps (초당 기가바이트)입니다. 자세한 내용은 [복사 작업 성능](/azure/data-factory/copy-activity-performance)을 참조 하세요.

이 문서에서는 Data Factory 데이터 복사 도구를 사용 하 여 Amazon Simple Storage Service (S3)에서 Azure 데이터 탐색기로 데이터를 로드 합니다. 유사한 프로세스를 따라 다른 데이터 저장소의 데이터를 복사할 수 있습니다. 예를 들면 다음과 같습니다.
* [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [파일 시스템](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Data Explorer 클러스터 및 데이터베이스](create-cluster-database-portal.md).
* 데이터 원본입니다.

## <a name="create-a-data-factory"></a>data factory 만들기

1. [Azure Portal](https://ms.portal.azure.com)에 로그인합니다.

1. 왼쪽 창에서 **리소스** > **분석** > **Data Factory**만들기를 선택 합니다.

   ![Azure Portal에서 데이터 팩터리 만들기](media/data-factory-load-data/create-adf.png)

1. **새 데이터 팩터리** 창에서 다음 테이블의 필드에 대한 값을 제공 합니다.

   !["새 데이터 팩터리" 창](media/data-factory-load-data/my-new-data-factory.png)  

   | 설정  | 입력할 값  |
   |---|---|
   | **이름** | 상자에 데이터 팩터리에 대한 전역적으로 고유한 이름을 입력 합니다. 오류가 발생 하는 경우 *data factory 이름 \"\" loadadxdemo를 사용할 수 없습니다*. 데이터 팩터리에 대해 다른 이름을 입력 합니다. Data Factory 아티팩트 명명에 대한 규칙은 [Data Factory 명명 규칙](/azure/data-factory/naming-rules)을 참조 하세요.|
   | **구독** | 드롭다운 목록에서 데이터 팩터리를 만들 Azure 구독을 선택 합니다. |
   | **리소스 그룹** | **새로 만들기**를 선택한 다음 새 리소스 그룹의 이름을 입력 합니다. 리소스 그룹이 이미 있는 경우 **기존 사용**을 선택 합니다. |
   | **버전(Version)** | 드롭다운 목록에서 **V2**를 선택 합니다. |  
   | **Location**: | 드롭다운 목록에서 데이터 팩터리의 위치를 선택 합니다. 지원 되는 위치만 목록에 표시 됩니다. 데이터 팩터리에서 사용 하는 데이터 저장소는 다른 위치나 지역에 있을 수 있습니다. |

1. **만들기**를 선택합니다.

1. 만들기 프로세스를 모니터링 하려면 도구 모음에서 **알림** 을 선택 합니다. 데이터 팩터리를 만든 후 선택 합니다.
   
   **Data Factory** 창이 열립니다.

   ![Data Factory 창](media/data-factory-load-data/data-factory-home-page.png)

1. 별도의 창에서 응용 프로그램을 열려면 **Author & 모니터** 타일을 선택 합니다.

## <a name="load-data-into-azure-data-explorer"></a>Azure 데이터 탐색기에 데이터 로드

여러 유형의 [데이터 저장소](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) 에서 Azure 데이터 탐색기로 데이터를 로드할 수 있습니다. 이 문서에서는 Amazon s 3에서 데이터를 로드 하는 방법을 설명 합니다.

다음 중 한 가지 방법으로 데이터를 로드할 수 있습니다.

* Azure Data Factory 사용자 인터페이스의 왼쪽 창에서 [AZURE DATA FACTORY UI를 사용 하 여 데이터 팩터리 만들기](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)의 "데이터 팩터리 만들기" 섹션에 표시 된 대로 **작성자** 아이콘을 선택 합니다.
* Azure Data Factory 데이터 복사 도구에서 [데이터 복사 도구를 사용 하 여 데이터 복사](/azure/data-factory/quickstart-create-data-factory-copy-data-tool)에 표시 된 것과 같습니다.

### <a name="copy-data-from-amazon-s3-source"></a>Amazon S3 (원본)에서 데이터 복사

1. **시작 하기** 창에서 **데이터 복사**를 선택 하 여 데이터 복사 도구를 엽니다.

   ![데이터 복사 도구 단추](media/data-factory-load-data/copy-data-tool-tile.png)

1. **속성** 창의 **작업 이름** 상자에 이름을 입력 하 고 **다음**을 선택 합니다.

    ![데이터 복사 속성 창](media/data-factory-load-data/copy-from-source.png)

1. **원본 데이터 저장소** 창에서 **새 연결 만들기**를 선택 합니다.

    ![데이터 복사 "원본 데이터 저장소" 창](media/data-factory-load-data/source-create-connection.png)

1. **Amazon S3**을 선택 하 고 **계속**을 선택 합니다.

    ![새 연결 된 서비스 창](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. **새 연결 된 서비스 (Amazon S3)** 창에서 다음을 수행 합니다.

    ![Amazon S3 연결 된 서비스 지정](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. **이름** 상자에 새 연결 된 서비스의 이름을 입력 합니다.

    b. **Integration runtime을 통해 연결** 드롭다운 목록에서 값을 선택 합니다.

    c. **액세스 키 ID** 상자에 값을 입력 합니다.
    
    > [!NOTE]
    > Amazon S3에서 액세스 키를 찾으려면 탐색 모음에서 Amazon 사용자 이름을 선택 하 고 **내 보안 자격 증명**을 선택 합니다.
    
    d. **비밀 액세스 키** 상자에 값을 입력 합니다.

    e. 만든 연결 된 서비스 연결을 테스트 하려면 **연결 테스트**를 선택 합니다.

    f. **마침**을 선택합니다.
    
      **원본 데이터 저장소** 창에 새 AmazonS31 연결이 표시 됩니다. 

1. **다음**을 선택합니다.

   ![원본 데이터 저장소에서 연결을 만듦](media/data-factory-load-data/source-data-store-created-connection.png)

1. **입력 파일 또는 폴더 선택** 창에서 다음을 수행 합니다.

    a. 복사할 파일 또는 폴더로 이동한 다음 해당 파일을 선택 합니다.

    b. 원하는 복사 동작을 선택 합니다. **이진 복사** 확인란이 선택 취소 되어 있는지 확인 합니다.

    c. **다음**을 선택합니다.

    ![입력 파일 또는 폴더 선택](media/data-factory-load-data/source-choose-input-file.png)

1. **파일 형식 설정** 창에서 파일에 대한 관련 설정을 선택 합니다. 그리고 **다음을 선택 합니다**.

   !["파일 형식 설정" 창](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Azure 데이터 탐색기 (대상)에 데이터 복사

새 Azure 데이터 탐색기 연결 된 서비스는이 섹션에 지정 된 Azure 데이터 탐색기 대상 테이블 (싱크)에 데이터를 복사 하기 위해 생성 됩니다.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Azure 데이터 탐색기 연결 된 서비스 만들기

Azure 데이터 탐색기 연결 된 서비스를 만들려면 다음을 수행 합니다.

1. 기존 데이터 저장소 연결을 사용 하거나 새 데이터 저장소를 지정 하려면 **대상 데이터 저장소** 창에서 **새 연결 만들기**를 선택 합니다.

    ![대상 데이터 저장소 창](media/data-factory-load-data/destination-create-connection.png)

1. **새 연결 된 서비스** 창에서 **Azure 데이터 탐색기**를 선택 하 고 **계속**을 선택 합니다.

    ![새 연결 된 서비스 창](media/data-factory-load-data/adx-select-new-linked-service.png)

1. **새 연결 된 서비스 (Azure 데이터 탐색기)** 창에서 다음을 수행 합니다.

    ![Azure 데이터 탐색기 새 연결 된 서비스 창](media/data-factory-load-data/adx-new-linked-service.png)

   a. **이름** 상자에 Azure 데이터 탐색기 연결 된 서비스의 이름을 입력 합니다.

   b. **계정 선택 방법**에서 다음 중 하나를 수행 합니다. 

    * **Azure 구독에서** 를 선택 하 고 드롭다운 목록에서 **Azure 구독** 및 **클러스터**를 선택 합니다. 

        > [!NOTE]
        > **클러스터** 드롭다운 컨트롤은 구독과 연결 된 클러스터만 나열 합니다.

    * **수동으로 입력**을 선택 하 고 **엔드포인트**을 입력 합니다.

   c. **테넌트** 상자에 테넌트 이름을 입력 합니다.

   d. **서비스 사용자 id** 상자에 서비스 사용자 id를 입력 합니다.

   e. **서비스 사용자 키** 를 선택 하 고 **서비스 사용자 키** 상자에 키의 값을 입력 합니다.

   f. **데이터베이스** 드롭다운 목록에서 데이터베이스 이름을 선택 합니다. 또는 **편집** 확인란을 선택 하 고 데이터베이스 이름을 입력 합니다.

   g. 만든 연결 된 서비스 연결을 테스트 하려면 **연결 테스트**를 선택 합니다. 연결 된 서비스에 연결할 수 있는 경우 창에 녹색 확인 표시와 **연결 성공** 메시지가 표시 됩니다.

   h. **마침** 을 선택 하 여 연결 된 서비스 만들기를 완료 합니다.

    > [!NOTE]
    > 서비스 주체는 Azure Data Factory에서 Azure 데이터 탐색기 서비스에 액세스 하는 데 사용 됩니다. 서비스 주체를 만들려면 [Azure Active Directory (AZURE AD) 서비스 사용자 만들기](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal)로 이동 합니다. Azure Key Vault 메서드를 사용 하지 마십시오.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Azure 데이터 탐색기 데이터 연결 구성

연결 된 서비스 연결을 만든 후에는 **대상 데이터 저장소** 창이 열리고 만든 연결을 사용할 수 있습니다. 연결을 구성 하려면 다음을 수행 합니다.

1. **다음**을 선택합니다.

    ![Azure 데이터 탐색기 "대상 데이터 저장소" 창](media/data-factory-load-data/destination-data-store.png)

1. **테이블 매핑** 창에서 대상 테이블 이름을 설정한 후 **다음**을 선택 합니다.

    ![대상 데이터 집합 "테이블 매핑" 창](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. **열 매핑** 창에 다음 매핑이 수행 됩니다.

    a. 첫 번째 매핑은 [Azure Data Factory 스키마 매핑에](/azure/data-factory/copy-activity-schema-and-type-mapping)따라 Azure Data Factory에 의해 수행 됩니다. 다음을 수행합니다.

    * Azure Data Factory 대상 테이블에 대한 **열 매핑을** 설정 합니다. 기본 매핑은 원본에서 Azure Data Factory 대상 테이블에 표시 됩니다.

    * 열 매핑을 정의 하지 않아도 되는 열 선택을 취소 합니다.

    b. 두 번째 매핑은이 테이블 형식 데이터를 Azure 데이터 탐색기로 수집 때 발생 합니다. 매핑은 [CSV 매핑 규칙](/azure/kusto/management/mappings#csv-mapping)에 따라 수행 됩니다. 원본 데이터가 CSV 형식이 아닌 경우에도 Azure Data Factory는 데이터를 테이블 형식으로 변환 합니다. 따라서이 단계에서 CSV 매핑은 유일 하 게 관련 된 매핑입니다. 다음을 수행합니다.

    * 필드 **Azure 데이터 탐색기 (Kusto) 싱크 속성**에서 열 매핑을 사용할 수 있도록 관련 수집 **매핑 이름을** 추가 합니다.

    * 수집 **매핑 이름을** 지정 하지 않으면 **열 매핑** 섹션에 정의 된 *이름으로* 매핑 순서가 사용 됩니다. *이름으로* 의 매핑이 실패 하는 경우 Azure 데이터 탐색기는 *열을 기준* 으로 데이터를 수집 하려고 시도 합니다. 즉, 기본적으로 위치를 기준으로 매핑됩니다.

    * **다음**을 선택합니다.

    ![대상 데이터 집합 "열 매핑" 창](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. **설정** 창에서 다음을 수행 합니다.

    a. **내결함성 설정**에서 관련 설정을 입력 합니다.

    b. **성능 설정**에서 **준비 사용** 은 적용 되지 않으며 **고급 설정** 에는 비용 고려 사항이 포함 됩니다. 특정 요구 사항이 없는 경우에는이 설정을 그대로 둡니다.

    c. **다음**을 선택합니다.

    ![데이터 복사 "설정" 창](media/data-factory-load-data/copy-data-settings.png)

1. **요약** 창에서 설정을 검토 하 고 **다음**을 선택 합니다.

    ![데이터 복사 "요약" 창](media/data-factory-load-data/copy-data-summary.png)

1. **배포 완료** 창에서 다음을 수행 합니다.

    a. **모니터** 탭으로 전환 하 고 파이프라인 상태 (진행률, 오류 및 데이터 흐름)를 보려면 **모니터**를 선택 합니다.

    b. 연결 된 서비스, 데이터 집합 및 파이프라인을 편집 하려면 **파이프라인 편집**을 선택 합니다.

    c. **마침** 을 선택 하 여 데이터 복사 태스크를 완료 합니다.

    !["배포 완료" 창](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>다음 단계

* Azure Data Factory의 [Azure 데이터 탐색기 커넥터](/azure/data-factory/connector-azure-data-explorer) 에 대해 알아봅니다.
* [DATA FACTORY UI](/azure/data-factory/quickstart-create-data-factory-portal)에서 연결 된 서비스, 데이터 집합 및 파이프라인을 편집 하는 방법에 대해 자세히 알아보세요.
* 데이터 쿼리를 위한 [Azure 데이터 탐색기 쿼리](/azure/data-explorer/web-query-data) 에 대해 알아봅니다.
