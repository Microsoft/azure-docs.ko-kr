---
title: '자습서: MongoDB에 대한 MongoDB online을 Azure Cosmos DB API로 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용 하 여 MongoDB 온-프레미스에서 Azure Cosmos DB API for MongoDB online으로 마이그레이션하는 방법에 대해 알아봅니다.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 896ae35e1039548ea56967ff73d6a1781aa3c8a6
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751386"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms"></a>자습서: DMS를 사용 하 여 Azure Cosmos DB의 MongoDB 용 API online으로 MongoDB 마이그레이션

Azure Database Migration Service를 사용하여 데이터베이스를 MongoDB 온-프레미스 또는 클라우드 인스턴스에서 Azure Cosmos DB의 API for MongoDB로 온라인(가동 중지 시간 최소화) 마이그레이션할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
>
> * Azure Database Migration Service 인스턴스를 만듭니다.
> * Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.
> * 마이그레이션을 모니터링합니다.
> * 준비가 되면 마이그레이션을 완료합니다.

이 자습서에서는 Azure Database Migration Service를 사용하여 최소한의 가동 중지 시간으로 Azure Virtual Machine에 호스팅되는 MongoDB의 데이터 세트를 Azure Cosmos DB의 API for MongoDB로 마이그레이션합니다. MongoDB 원본을 설정하지 않은 경우 [Azure의 Windows VM에서 MongoDB 설치 및 구성](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb) 문서를 참조하세요.

> [!NOTE]
> Azure Database Migration Service를 사용하여 온라인 마이그레이션을 수행하려면 프리미엄 가격 책정 계층에 따라 인스턴스를 만들어야 합니다.

> [!IMPORTANT]
> 최적의 마이그레이션 환경을 위해 Microsoft는 대상 데이터베이스와 동일한 Azure 지역에서 Azure Database Migration Service의 인스턴스를 만드는 것을 권장합니다. 지역 또는 지리적 영역 간에 데이터를 이동하면 마이그레이션 프로세스가 느려질 수 있습니다.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

이 문서에서는 온라인 방식으로 MongoDB에서 Azure Cosmos DB의 MongoDB API로 마이그레이션하는 방법에 대해 설명합니다. 오프라인 마이그레이션은 [DMS를 사용하여 오프라인에서 MongoDB를 Azure Cosmos DB의 MongoDB API로 마이그레이션](tutorial-mongodb-cosmos-db.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 처리량 추정, 파티션 키 선택 및 인덱싱 정책과 같은 [사전 마이그레이션 완료](../cosmos-db/mongodb-pre-migration.md) 단계.
* [Azure Cosmos DB의 API for MongoDB 계정을 만듭니다](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* [Express](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 경로 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용 하 여 온-프레미스 원본 서버에 사이트 간 연결을 제공 하는 Azure Resource Manager 배포 모델을 사용 하 여 Azure Database Migration Service에 대한 Microsoft Azure Virtual Network를 만듭니다.

    > [!NOTE]
    > 가상 네트워크를 설정 하는 동안 Microsoft에 네트워크 피어 링을 사용 하는 Express 경로를 사용 하는 경우 서비스가 프로 비전 될 서브넷에 다음 서비스 [엔드포인트](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) 을 추가 합니다.
    >
    > * 대상 데이터베이스 엔드포인트(예: SQL 엔드포인트, Cosmos DB 엔드포인트 등)
    > * 스토리지 엔드포인트
    > * Service Bus 엔드포인트
    >
    > Azure Database Migration Service에는 인터넷 연결이 없으므로 이 구성이 필요합니다.

* 가상 네트워크 NSG (네트워크 보안 그룹) 규칙이 다음 통신 포트 (53, 443, 445, 9354 및 10000-20000)를 차단 하지 않는지 확인 합니다. Virtual network NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용 하 여 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)문서를 참조 하세요.
* Azure Database Migration Service에서 기본적으로 27017 TCP 포트인 원본 MongoDB 서버에 액세스할 수 있도록 Windows 방화벽을 엽니다.
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우 Azure Database Migration Service에서 원본 데이터베이스에 액세스하여 마이그레이션할 수 있도록 허용하는 방화벽 규칙을 추가해야 합니다.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

1. Azure Portal에 로그인하고, **모든 서비스**를 선택한 다음, **구독**을 선택합니다.

   ![포털 구독 표시](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)

2. Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음, **리소스 공급자**를 선택합니다.

    ![리소스 공급자 보기](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)

3. 마이그레이션을 검색한 다음 **Microsoft.DataMigration**의 오른쪽에서 **등록**을 선택합니다.

    ![리소스 공급자 등록](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>인스턴스 만들기

1. Azure Portal에서 **+ 리소스 만들기**를 선택하고, Azure Database Migration Service를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service**를 선택합니다.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기**를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3. **Migration Service 만들기** 화면에서 서비스, 구독, 신규 또는 기존 리소스 그룹의 이름을 지정합니다.

4. Azure Database Migration Service 인스턴스를 만들 위치를 선택합니다.

5. 기존 가상 네트워크를 선택 하거나 새 가상 네트워크를 만듭니다.

   가상 네트워크는 원본 MongoDB 인스턴스 및 대상 Azure Cosmos DB 계정에 대한 액세스 권한이 있는 Azure Database Migration Service 제공 합니다.

   Azure Portal에서 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Portal를 사용 하 여 가상 네트워크 만들기](https://aka.ms/DMSVnet)문서를 참조 하세요.

6. 프리미엄 가격 책정 계층에서 SKU를 선택합니다.

    > [!NOTE]
    > 온라인 마이그레이션은 프리미엄 계층을 사용하는 경우에만 지원됩니다. 비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요.

    ![Azure Database Migration Service 인스턴스 설정 구성](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7. **만들기**를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스**를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services**를 선택합니다.

    ![Azure Database Migration Service의 모든 인스턴스 찾기](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. **Azure Database Migration Services** 화면에서 방금 만든 Azure Database Migration Service 인스턴스의 이름을 검색한 다음, 인스턴스를 선택합니다.

    또는 Azure Portal의 검색 창에서 Azure Database Migration Service 인스턴스를 검색할 수 있습니다.

    ![Azure Portal의 검색 창 사용](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. **+ 새 마이그레이션 프로젝트**를 선택합니다.

4. **새 마이그레이션 프로젝트** 화면에서 프로젝트 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **MongoDB**를 선택하고, **대상 서버 유형** 텍스트 상자에서 **CosmosDB(MongoDB API)** 를 선택한 다음, **활동 유형 선택**에 대해 **온라인 데이터 마이그레이션[미리 보기]** 을 선택합니다.

    ![Database Migration Service 프로젝트 만들기](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5. **저장**을 선택한 다음, **활동 만들기 및 실행**을 선택하여 프로젝트를 만들고 마이그레이션 작업을 실행합니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

1. **원본 세부 정보** 화면에서 원본 MongoDB 서버에 대한 연결 세부 정보를 지정합니다.

   > [!IMPORTANT]
   > Azure Database Migration Service는 Azure Cosmos DB를 원본으로 지원하지 않습니다.

    원본에 연결하는 세 가지 모드가 있습니다.
   * **표준 모드** - 정규화된 도메인 이름이나 IP 주소, 포트 번호 및 연결 자격 증명을 수락합니다.
   * **연결 문자열 모드** - [연결 문자열 URI 형식](https://docs.mongodb.com/manual/reference/connection-string/) 문서에서 설명한 대로 MongoDB 연결 문자열을 수락합니다.
   * **Azure Storage의 데이터** - Blob 컨테이너 SAS URL을 수락합니다. MongoDB [bsondump 도구](https://docs.mongodb.com/manual/reference/program/bsondump/)에서 생성된 BSON 덤프가 Blob 컨테이너에 있는 경우 **Blob이 BSON 덤프를 포함합니다**를 선택하고, JSON 파일이 컨테이너에 있으면 이를 선택 취소합니다.

     이 옵션을 선택할 경우 해당 스토리지 계정 연결 문자열이 다음 형식으로 나타나는지 확인하세요.

     ```
     https://blobnameurl/container?SASKEY
     ```

     또한 Azure Storage의 형식 덤프 정보에 따라 다음 정보를 염두에 두어야 합니다.

     * BSON 덤프의 경우 Blob 컨테이너 내 데이터는 데이터 파일이 collection.bson 형식의 포함한 데이터베이스를 따라 이름이 지정된 폴더에 배치되는 bsondump 형식이어야 합니다. 메타데이터 파일(있는 경우)의 이름은 *collection*.metadata.json 형식을 사용하여 이름이 지정되어야 합니다.

     * JSON 덤프의 경우 Blob 컨테이너의 파일은 포함한 데이터베이스를 따라 이름이 지정된 폴더에 배치되어야 합니다. 각 데이터베이스 폴더 내에서 데이터 파일은 "data"라고 하는 하위 폴더에 배치되고 *collection*.json 형식을 사용하여 이름이 지정되어야 합니다. 메타데이터 파일(있는 경우)은 "metadata"라고 하는 하위 폴더에 배치되고 *collection*.json과 동일한 형식을 사용하여 이름이 지정되어야 합니다. 메타데이터 파일은 MongoDB bsondump 도구에서 생성한 것과 동일한 형식이어야 합니다.

    > [!IMPORTANT]
    > mongo 서버에서 자체 서명된 인증서를 사용하는 것은 바람직하지 않습니다. 그러나 하나를 사용할 경우 **연결 문자열 모드**를 사용하여 서버에 연결하고 연결 문자열에 “”가 있는지 확인합니다.
    >
    >```
    >&sslVerifyCertificate=false
    >```

    DNS 이름을 확인할 수 없는 경우에는 IP 주소를 사용할 수 있습니다.

   ![원본 세부 정보 지정](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. **저장**을 선택합니다.

   > [!NOTE]
   > 원본이 복제본 세트인 경우 원본 서버 주소는 주 복제본의 주소여야 하며, 원본이 분할된 MongoDB 클러스터인 경우 라우터여야 합니다. 분할된 MongoDB 클러스터의 경우 Azure Database Migration Service는 클러스터에서 개별적으로 분할된 데이터베이스에 연결할 수 있어야 하며, 이 경우 더 많은 머신에서 방화벽을 열어야 할 수도 있습니다.

## <a name="specify-target-details"></a>대상 세부 정보 지정

1. **마이그레이션 대상 세부 정보** 화면에서 대상 Azure Cosmos DB 계정에 대한 연결 세부 정보를 지정합니다. 이 계정은 MongoDB 데이터를 마이그레이션할 미리 프로비전된 Azure Cosmos DB의 API for MongoDB 계정입니다.

    ![대상 세부 정보 지정](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. **저장**을 선택합니다.

## <a name="map-to-target-databases"></a>대상 데이터베이스에 매핑

1. **대상 데이터베이스에 매핑** 화면에서 마이그레이션하기 위해 원본 및 대상 데이터베이스를 매핑합니다.

   대상 데이터베이스의 이름이 원본 데이터베이스와 동일하면 Azure Database Migration Service는 기본적으로 이 대상 데이터베이스를 선택합니다.

   **만들기** 문자열이 데이터베이스 이름 옆에 나타나는 경우 Azure Database Migration Service가 대상 데이터베이스를 찾을 수 없으므로 해당 서비스가 데이터베이스를 만든다는 것을 나타냅니다.

   마이그레이션의 이 시점에서 데이터베이스의 처리량을 공유하려면 처리량 RU를 지정합니다. Cosmos DB에서 데이터베이스 수준에서 또는 컬렉션 수준에서 개별적으로 처리량을 프로비전할 수 있습니다. 처리량은 RU([요청 단위](https://docs.microsoft.com/azure/cosmos-db/request-units))로 측정됩니다. [Azure Cosmos DB 가격 책정](https://azure.microsoft.com/pricing/details/cosmos-db/)에 대해 자세히 알아봅니다.

   ![대상 데이터베이스에 매핑](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. **저장**을 선택합니다.

3. **컬렉션 설정** 화면에서 컬렉션 목록을 확장한 다음, 마이그레이션할 컬렉션 목록을 검토합니다.

   Azure Database Migration Service는 대상 Azure Cosmos DB 계정에 존재하지 않는 원본 MongoDB 인스턴스에 존재하는 모든 컬렉션을 자동으로 선택합니다. 이미 데이터가 포함된 컬렉션을 다시 마이그레이션하려면 이 화면에서 컬렉션을 명시적으로 선택해야 합니다.

   컬렉션에서 사용할 RU의 수를 지정할 수 있습니다. 대부분의 경우 500(분할된 컬렉션의 경우 최소 1000)과 4000 사이의 값이면 충분합니다. Azure Database Migration Service는 컬렉션 크기에 따라 스마트 기본값을 제공합니다.

    > [!NOTE]
    > 필요에 따라 Azure Database Migration Service 인스턴스를 여러 개 사용하여 데이터베이스 마이그레이션 및 컬렉션을 병렬로 수행하여 속도를 높일 수 있습니다.

   확장성을 최적화하기 위해 [Azure Cosmos DB에서 분할](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview)을 활용하도록 분할된 데이터베이스 키를 지정할 수도 있습니다. [분할된 데이터베이스/파티션 키를 선택하는 모범 사례](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)를 검토해야 합니다. 파티션 키가 없으면 처리량을 향상시키기 위해 항상 **_id**를 분할 키로 사용할 수 있습니다.

   ![컬렉션 선택 테이블](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. **저장**을 선택합니다.

5. **마이그레이션 요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정합니다.

    ![마이그레이션 요약](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>마이그레이션 실행

* **마이그레이션 실행**을 선택합니다.

   마이그레이션 작업 창이 나타나고, 작업 **상태**가 표시됩니다.

   ![작업 상태](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

* 마이그레이션 작업 화면에서 **새로 고침**을 선택하여 마이그레이션 **상태**가 **재생 중**으로 표시될 때까지 표시 화면을 업데이트합니다.

   > [!NOTE]
   > 데이터베이스 및 컬렉션 수준 마이그레이션 메트릭의 세부 정보를 가져오는 작업을 선택할 수 있습니다.

   ![재생 중 작업 상태](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Cosmos DB에서 데이터 확인

1. 원본 MongoDB 데이터베이스를 변경합니다.
2. COSMOS DB에 연결하여 데이터가 원본 MongoDB 서버에서 복제되었는지 확인합니다.

    ![재생 중 작업 상태](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)

## <a name="complete-the-migration"></a>마이그레이션 완료

* COSMOS DB 대상에서 원본의 모든 문서를 사용할 수 있게 되면 마이그레이션 작업의 상황에 맞는 메뉴에서 **마침**을 선택하여 마이그레이션을 완료합니다.

    이 작업은 보류 중인 모든 변경 내용의 재생을 끝내고 마이그레이션을 완료합니다.

    ![재생 중 작업 상태](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="post-migration-optimization"></a>마이그레이션 후 최적화

MongoDB 데이터베이스에 저장된 데이터를 MongoDB용 Azure Cosmos DB의 API로 마이그레이션한 후 Azure Cosmos DB에 연결하고 데이터를 관리할 수 있습니다. 인덱싱 정책 최적화와 같은 다른 마이그레이션 후 최적화 단계를 수행하거나, 기본 일관성 수준을 업데이트하거나, Azure Cosmos DB 계정에 대한 글로벌 배포를 구성할 수도 있습니다. 자세한 내용은 [마이그레이션 후 최적화](../cosmos-db/mongodb-post-migration.md) 문서를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Cosmos DB 서비스 정보](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>다음 단계

* Microsoft [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)에서 추가 시나리오에 대한 마이그레이션 지침을 검토합니다.
