---
title: Azure로 마이그레이션된 워크로드에 대한 비용 계산 및 크기 조정 모범 사례 | Microsoft Docs
description: Azure로 마이그레이션된 워크로드의 비용을 계산하고 크기를 조정하는 모범 사례를 확인합니다.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: 48ce99bd830d2c35e5cb9703d2ef754a602d534b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926160"
---
# <a name="best-practices-for-costing-and-sizing-workloads-migrated-to-azure"></a>Azure로 마이그레이션된 워크로드에 대한 비용 계산 및 크기 조정 모범 사례

마이그레이션을 계획하고 설계할 때 비용에 중점을 두면 Azure 마이그레이션의 장기적인 성공을 보장할 수 있습니다. 마이그레이션 프로젝트를 진행하는 동안 모든 팀(재무, 관리, 애플리케이션 팀 등)에서 관련 비용을 이해해야 합니다.
- 마이그레이션을 수행하기 전에 월별, 분기별, 연도별 예산 목표의 초기 계획을 사용하여 마이그레이션 비용을 예측하는 것이 성공에 중요합니다.
- 마이그레이션 후에는 비용을 최적화하고, 워크로드를 지속적으로 모니터링하며, 향후 사용 패턴을 계획해야 합니다. 마이그레이션된 리소스는 한 가지 유형의 워크로드로 시작될 수 있지만, 시간이 지남에 따라 사용량, 비용 및 비즈니스 요구 사항의 변화에 기반하여 다른 유형으로 발전할 수 있습니다.

이 문서에서는 마이그레이션 전후의 비용 계산 및 크기 조정 모범 사례에 대해 설명합니다.  

> [!IMPORTANT]
> 이 문서에서 설명하는 모범 사례와 의견은 작성 당시의 사용 가능한 Azure 플랫폼과 서비스 기능을 기반으로 합니다. 특징과 기능은 시간이 지남에 따라 변경됩니다. 일부 추천 사항이 배포에 적용되지 않을 수 있으므로 적합한 추천 사항을 선택합니다.


## <a name="before-migration"></a>마이그레이션 전 

워크로드를 클라우드로 이동하기 전에 Azure에서 실행하는 월별 비용을 추정합니다. 클라우드 비용을 사전에 관리하면 운영 비용(OpEx) 예산을 준수할 수 있습니다. 예산이 부족하면 마이그레이션하기 전에 이를 고려합니다.  적절한 경우 워크로드를 Azure 서버리스 기술로 전환하여 비용을 절감합니다.

이 섹션의 모범 사례는 비용을 예측하고, VM 및 스토리지의 크기를 적절히 조정하고, Azure 하이브리드 혜택을 활용하고, 예약된 VM을 사용하고, 구독에 대한 클라우드 지출을 예측하는 데 도움이 됩니다.



## <a name="best-practice-estimate-monthly-workload-costs"></a>모범 사례: 월별 워크로드 비용 예측
 
마이그레이션된 워크로드에 대한 월별 요금 청구를 예측하기 위해 사용할 수 있는 여러 도구가 있습니다.

- **Azure 가격 계산기**: VM 및 스토리지와 같이 예상하려는 제품을 선택합니다. 가격 계산기에 비용을 입력하여 추정 비용을 산출합니다.

  ![Azure 가격 계산기](./media/migrate-best-practices-costs/pricing.png) 
  
  *Azure 가격 계산기*

- **Azure Migrate**: 비용을 추정하려면 Azure에서 워크로드를 실행하는 데 필요한 모든 리소스를 검토하고 설명해야 합니다. 이 데이터를 획득하려면 서버, VM, 데이터베이스 및 스토리지를 포함한 자산의 인벤토리를 만듭니다. Azure Migrate를 사용하여 이 정보를 수집할 수 있습니다.

  - Azure Migrate는 온-프레미스 환경을 검색 및 평가하여 인벤토리를 제공합니다.
  - Azure Migrate는 VM 간의 종속성을 매핑하고 표시하여 전체 그림을 제공할 수 있습니다.
  - Azure Migrate 평가에는 예상 비용이 포함됩니다.
    - 컴퓨팅 비용: Azure Migrate는 평가를 만들 때 추천되는 Azure VM 크기를 사용하여 청구 API를 통해 월별 예상 VM 비용을 계산합니다. 추정에서는 운영 체제, 소프트웨어 보증, 예약 인스턴스, VM 가동 시간, 위치 및 통화 설정을 고려합니다. 평가에서 모든 VM의 비용을 집계하고 월별 총 컴퓨팅 비용을 계산합니다.
    - 스토리지 비용: Azure Migrate는 평가에서 모든 VM의 스토리지 비용을 집계하여 월별 총 스토리지 비용을 계산합니다. 특정 머신의 월별 스토리지 비용은 연결된 모든 디스크의 월별 비용을 집계하여 계산할 수 있습니다. 

    ![Azure Migrate](./media/migrate-best-practices-costs/assess.png)
    
    *Azure Migrate 평가*

**자세한 정보:**
- Azure 가격 계산기 [사용](https://azure.microsoft.com/pricing/calculator/)
- Azure Migrate의 [개요 살펴보기](https://docs.microsoft.com/azure/migrate/migrate-overview).
- Azure Migrate 평가에 대한 [참조](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)
- DMS(Database Migration Service)에 대해 [자세히 알아보기](https://docs.microsoft.com/azure/dms/dms-overview)

## <a name="best-practice-right-size-vms"></a>모범 사례: 적합한 VM 크기 조정

Azure VM을 배포하여 워크로드를 지원할 때 여러 가지 옵션을 선택할 수 있습니다. 각 VM 유형에는 특정 기능과 CPU, 메모리 및 디스크의 다양한 조합이 있습니다. VM은 다음과 같이 그룹화됩니다.

**형식** | **세부 정보** | **사용**
--- | --- | ---
**범용** | CPU 대 메모리 비율이 적당합니다. | 테스트 및 개발, 중/소형 데이터베이스 및 트래픽이 적거나 중간 정도인 웹 서버에 적합합니다.
**컴퓨팅 최적화** | CPU 대 메모리 비율이 높습니다. | 트래픽이 중간 정도인 웹 서버, 네트워크 어플라이언스, 일괄 처리 프로세스 및 애플리케이션 서버에 적합합니다.
**메모리 최적화** | 메모리 대 CPU 비율이 높습니다. | 관계형 데이터베이스, 중/대형 캐시 및 메모리 내 분석에 적합합니다.
**Storage에 최적화** | 높은 디스크 처리량 및 IO | 빅 데이터, SQL, NoSQL 데이터베이스에 적합합니다.
**GPU에 최적화** | 특수화된 VM입니다. 하나 이상의 GPU가 있습니다. | 고급 그래픽 및 비디오 편집 기능을 사용합니다.
**고성능** | 가장 빠르고 강력한 CPU입니다. 처리량이 높은 선택적 네트워크 인터페이스(RDMA)를 갖춘 VM입니다. | 중요한 고성능 애플리케이션입니다.

- 이러한 VM 간의 가격 차이와 장기적인 예산 효과를 이해해야 합니다.
- 각 유형에는 여러 개의 VM 시리즈가 있습니다.
- 또한 시리즈 내의 VM을 선택하면 해당 시리즈 내에서 VM을 확장하거나 축소할 수 있습니다. 예를 들어 DSv2\_2는 DSv2\_4까지 확장할 수 있지만 Fsv2\_2와 같은 다른 시리즈로 변경할 수는 없습니다.

**자세한 정보:**
- VM 유형 및 크기 조정에 대해 [자세히 알아보기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) 및 크기를 유형에 매핑
- VM 크기 조정 [계획](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs)
- 가상의 Contoso 회사에 대한 평가 샘플 [검토](https://docs.microsoft.com/azure/migrate/contoso-migration-assessment)

## <a name="best-practice-select-the-right-storage"></a>모범 사례: 적합한 스토리지 선택

온-프레미스 스토리지(SAN 또는 NAS) 및 이를 지원하는 네트워크를 튜닝하고 유지 관리하는 데 많은 비용과 시간이 소요될 수 있습니다. 파일(스토리지) 데이터는 일반적으로 운영 및 관리 문제를 완화하기 위해 클라우드로 마이그레이션됩니다. Microsoft는 데이터를 Azure로 이동하기 위한 몇 가지 옵션을 제공하며, 사용자는 이러한 옵션을 결정해야 합니다. 데이터에 적합한 스토리지 유형을 선택하면 조직에서 매월 수천 달러의 비용을 절약할 수 있습니다. 몇 가지 고려 사항:

- 많이 액세스하지 않고 업무에 중요하지 않은 데이터는 가장 비싼 스토리지에 배치할 필요가 없습니다.
- 반대로, 중요 업무용 데이터는 더 높은 계층의 스토리지 옵션에 배치해야 합니다.
- 마이그레이션 계획 중에 가장 적합한 스토리지에 매핑하기 위해 데이터의 인벤토리를 사용하고 중요도에 따라 분류합니다. 예산 및 비용 외에도 성능을 고려합니다. 비용이 반드시 주요 의사 결정 요소가 될 필요는 없습니다. 가장 저렴한 옵션을 선택하면 워크로드가 성능 및 가용성 위험에 노출될 수 있습니다. 

### <a name="storage-data-types"></a>스토리지 데이터 형식

Azure는 다양한 형식의 스토리지 데이터를 제공합니다.

| **데이터 형식** | **세부 정보** | **사용 현황** |
|--- | --- |  --- |
|**Blob** | 텍스트 또는 이진 데이터와 같은 비정형 개체를 대량으로 저장하도록 최적화되었습니다.<br/>어디서나 HTTP/HTTPS를 통해 데이터에 액세스합니다. | 스트리밍 및 임의 액세스 시나리오에 사용합니다. 예를 들어 이미지와 문서를 브라우저에 직접 제공하고, 비디오와 오디오를 스트림하며, 백업 및 재해 복구 데이터를 저장합니다.|
|**파일** | SMB 3.0을 통해 관리 파일 공유에 액세스합니다. | 온-프레미스 파일 공유를 마이그레이션할 때 사용하여 파일 데이터에 대한 여러 액세스/연결을 제공합니다.|
|**디스크** | 페이지 Blob을 기반으로 합니다.<br/><br/> 디스크 유형(속도): 표준(HDD 또는 SSD) 또는 프리미엄(SSD).<br/><br/>디스크 관리: 비관리(디스크 설정 및 스토리지 관리) 또는 관리(디스크 유형 선택 및 Azure에서 디스크 관리). | VM용 프리미엄 디스크를 사용합니다. 간단한 관리 및 크기 조정을 위해 관리 디스크를 사용합니다.|
|**큐** | 인증된 호출(HTTP 또는 HTTPS)을 통해 액세스되는 많은 수의 메시지를 저장 및 검색합니다. | 비동기 메시지 큐를 사용하여 애플리케이션 구성 요소를 연결합니다.|
|**테이블** | 테이블을 저장합니다. | 이제는 Azure Cosmos DB Table API의 일부입니다.|



### <a name="access-tiers"></a>액세스 계층
Azure 스토리지에서 블록 Blob 데이터에 액세스하기 위한 여러 가지 옵션을 제공합니다. 적합한 액세스 계층을 선택하면 블록 Blob 데이터를 가장 비용 효율적인 방식으로 저장할 수 있습니다.

**형식** | **세부 정보** | **사용 현황**
--- | --- | ---
**핫** | 스토리지 비용은 쿨보다 높지만, 액세스 비용은 쿨보다 낮습니다.<br/><br/>기본 계층입니다. | 자주 액세스하는 활성 사용 상태의 데이터에 사용합니다.
**쿨** | 스토리지 비용은 핫보다 높지만, 액세스 비용은 핫보다 낮습니다.<br/><br/> 최소 30일 동안 저장합니다. | 단기간 동안 저장하며, 데이터는 사용할 수 있지만 자주 액세스하지 않습니다.
**보관** | 개별 블록 Blob에 사용됩니다.<br/><br/> 가장 비용 효율적인 스토리지 옵션입니다. 데이터 액세스 비용이 핫 또는 쿨 계층보다 비쌉니다. | 검색 대기 시간의 서버 시간을 허용할 수 있는 데이터에 사용하며, 해당 계층에서 최소 180일 동안 유지됩니다. 

### <a name="storage-account-types"></a>스토리지 계정 유형

Azure에서 여러 유형의 스토리지 계정과 성능 계층을 제공합니다.

**계정 유형** | **세부 정보** | **사용 현황**
--- | --- | ---
**범용 v2 표준** | Blob(블록, 페이지, 추가), 파일, 디스크, 큐 및 테이블을 지원합니다.<br/><br/> 핫, 쿨 및 보관 액세스 계층을 지원합니다. ZRS를 지원합니다. | 대부분의 시나리오 및 대부분의 데이터 형식에 사용합니다. 표준 스토리지 계정은 HDD 또는 SSD를 기준으로 할 수 있습니다.
**범용 v2 프리미엄** | Blob 스토리지 데이터(페이지 Blob)를 지원합니다. 핫, 쿨 및 보관 액세스 계층을 지원합니다. ZRS를 지원합니다.<br/><br/> SSD에 저장됩니다. | 모든 VM에 사용하는 것이 좋습니다.
**범용 v1** | 액세스 계층은 지원되지 않습니다. ZRS를 지원하지 않습니다. | 애플리케이션에 Azure 클래식 배포 모델이 필요한 경우에 사용합니다.
**Blob** | 비정형 개체를 저장하도록 특수화된 스토리지 계정입니다. 블록 Blob을 제공하고 Blob만 추가합니다(파일, 큐, 테이블 또는 디스크 스토리지 서비스는 없음). 범용 v2와 동일한 내구성, 가용성, 확장성 및 성능을 제공합니다. | 이러한 계정에는 페이지 Blob을 저장할 수 없으므로 VHD 파일을 저장할 수 없습니다. 액세스 계층을 핫 또는 쿨로 설정할 수 있습니다.

### <a name="storage-redundancy-options"></a>저장소 중복 옵션

스토리지 계정은 복원력과 고가용성을 위해 여러 유형의 중복성을 사용할 수 있습니다.

**형식** | **세부 정보** | **사용 현황**
--- | --- | ---
**LRS(로컬 중복 스토리지)** | 단일 스토리지 단위 내에서 별도의 장애 도메인 및 업데이트 도메인으로 복제하여 로컬 가동 중단으로부터 보호합니다. 하나의 데이터 센터에 여러 개의 데이터 복사본을 유지합니다. 지정된 연도 동안 99.999999999%(11개의 9) 이상의 내구성을 제공합니다. | 애플리케이션에서 쉽게 재구성할 수 있는 데이터를 저장하는지 여부를 고려합니다.
**ZRS(영역 중복 스토리지)** | 단일 지역의 3개 스토리지 클러스터 간에 복제하여 데이터 센터의 가동 중단으로부터 다시 보호합니다. 각 스토리지 클러스터는 물리적으로 구분되며 자체의 가용성 영역에 있습니다. 여러 데이터 센터 또는 지역에서 여러 데이터 복사본을 유지하여 지정된 연도 동안 99.9999999999%(12개의 9) 이상의 내구성을 제공합니다. | 일관성, 내구성 및 고가용성이 필요한지 여부를 고려합니다. 여러 영역에 영구적인 영향을 주는 경우 지역 재해로부터 보호하지 못할 수도 있습니다.
**GRS(지역 중복 스토리지)** | 주 지역에서 수백만 마일 떨어진 보조 지역에 데이터를 복제하여 전체 지역 가동 중단으로부터 보호합니다. 지정된 연도 동안 99.99999999999999%(16개의 9) 이상의 내구성을 제공합니다. | Microsoft에서 장애 조치를 보조 지역으로 시작하지 않으면 복제본 데이터를 사용할 수 없습니다. 장애 조치가 발생하면 읽기 및 쓰기 액세스를 사용할 수 있습니다.
**RA-GRS(읽기 액세스 지역 중복 스토리지)** | GRS와 비슷합니다. 지정된 연도 동안 99.99999999999999%(16개의 9) 이상의 내구성을 제공합니다. | GRS에 사용되는 두 번째 지역에서 읽기 액세스를 허용하여 99.99%의 읽기 가용성을 제공합니다.

**자세한 정보:**
- Azure Storage 가격 책정 [검토](https://azure.microsoft.com/pricing/details/storage/)
- 많은 양의 데이터를 Azure Blob 및 파일로 마이그레이션하는 Azure Import/Export에 대해 [알아보기](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) 
- Blob, 파일 및 디스크 스토리지의 데이터 형식 [비교](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- 액세스 계층에 대해 [알아보기](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)
- 다른 유형의 스토리지 계정 [검토](https://docs.microsoft.com/azure/storage/common/storage-account-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [스토리지 중복성](https://docs.microsoft.com/azure/storage/common/storage-redundancy), [LRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), [ZRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), [GRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) 및 [읽기 액세스 GRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#read-access-geo-redundant-storage)에 대해 알아보기
- Azure Files에 대해 [자세히 알아보기](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)



## <a name="best-practice-leverage-azure-hybrid-benefits"></a>모범 사례: Azure 하이브리드 혜택 활용

Windows Server 및 SQL Server와 같은 시스템의 소프트웨어에 수년간 투자한 결과, Microsoft는 다른 클라우드 공급 기업에서 제공할 수 없는 상당한 할인을 통해 클라우드에서 고객에게 가치를 제공하는 독보적인 위치에 있습니다. 

통합된 Microsoft 온-프레미스/Azure 제품 포트폴리오는 경쟁력과 비용상의 이점을 제공합니다. 현재 SA(Software Assurance)를 통해 운영 체제 또는 기타 소프트웨어 라이선스를 보유하고 있으면 Azure 하이브리드 혜택을 통해 해당 라이선스를 클라우드로 가져갈 수 있습니다.

**자세한 정보:**

- 하이브리드 혜택 절감액 계산기 [살펴보기](https://azure.microsoft.com/pricing/hybrid-benefit/)
- Windows Server에 대한 하이브리드 혜택 [자세히 알아보기](https://azure.microsoft.com/pricing/hybrid-benefit/)
- SQL Server Azure VM에 대한 가격 책정 지침 [검토](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)


## <a name="best-practice-use-reserved-vm-instances"></a>모범 사례: 예약 VM 인스턴스 사용

대부분의 클라우드 플랫폼은 종량제로 설정됩니다. 동적 워크로드에 대한 상황을 반드시 알고 있을 필요가 없으므로 이 모델에서는 단점만 제시합니다. 워크로드에 대한 명확한 의도를 지정하면 인프라를 계획하는 데 기여할 수 있습니다.

예약 Azure VM 인스턴스를 사용하면 1년 또는 3년 기간의 VM 인스턴스에 대해 선불로 결제할 수 있습니다. 

- 선불의 경우 사용하는 리소스에 대한 할인이 제공됩니다.
- VM, SQL 데이터베이스 컴퓨팅, Azure Cosmos DB 또는 기타 리소스 비용은 종량제 가격의 최대 72%까지 대폭 절감할 수 있음 
- 예약의 경우 청구 할인이 제공되며 리소스의 런타임 상태에 영향을 주지 않습니다.
- 예약 인스턴스는 취소할 수 있습니다.

![예약 인스턴스](./media/migrate-best-practices-costs/reserve.png)

*Azure 예약 VM*

**자세한 정보:**
- Azure 예약에 대해 [알아보기](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
- 예약 인스턴스 FAQ [참조](https://azure.microsoft.com/pricing/reserved-vm-instances/#faq)
- SQL Server Azure VM에 대한 [가격 책정 지침 살펴보기](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)


## <a name="best-practice-aggregate-cloud-spend-across-subscriptions"></a>모범 사례: 구독 전체에 대한 클라우드 지출 집계

결국에는 둘 이상의 Azure 구독이 있을 수 밖에 없습니다. 예를 들어 개발 및 프로덕션 경계를 분리하기 위해 추가 구독이 필요하거나 각 클라이언트마다 별도의 구독이 필요한 플랫폼이 있을 수도 있습니다. 모든 구독에서 데이터 보고를 단일 플랫폼으로 집계하는 기능은 유용한 기능입니다.

이를 위해 Azure Cost Management API를 사용할 수 있습니다. 그러면 데이터를 Azure SQL과 같은 단일 원본에 통합한 후에 Power BI와 같은 도구를 사용하여 집계된 데이터를 화면에 표시할 수 있습니다. 집계된 구독 보고서 및 세부적인 보고서를 만들 수 있습니다. 예를 들어 비용 관리에 대한 사전 인사이트가 필요한 사용자의 경우 부서, 리소스 그룹 등에 따라 특정 비용 보기를 만들 수 있습니다. Azure 청구 데이터에 대한 완전한 액세스 권한을 제공할 필요는 없습니다.

**자세한 정보:**

- Azure 사용량 API [개요 살펴보기](https://docs.microsoft.com/azure/billing/billing-consumption-api-overview)
- Power BI Desktop에서 Azure Consumption Insights에 연결하는 방법에 대해 [알아보기](https://docs.microsoft.com/power-bi/desktop-connect-azure-consumption-insights)
- RBAC(역할 기반 액세스 제어)를 사용하여 Azure 청구 정보에 대한 액세스를 관리하는 방법에 대해 [알아보기](https://docs.microsoft.com/azure/billing/billing-manage-access)
 
## <a name="after-migration"></a>마이그레이션 후

워크로드가 성공적으로 마이그레이션되고 몇 주 동안의 사용량 데이터가 수집되면 리소스 비용을 명확하게 파악할 수 있습니다.
- 데이터를 분석할 때 Azure 리소스 그룹 및 리소스에 대한 예산 초기 계획을 생성할 수 있습니다.
- 그런 다음, 클라우드 예산이 지출되는 위치를 파악하면 비용을 추가로 절감할 수 있는 방법을 분석할 수 있습니다.

이 섹션의 모범 사례로, Azure Cost Management를 사용하여 비용 예산 편성 및 분석, 리소스 모니터링, 리소스 그룹 예산 구현, 모니터링, 스토리지 및 VM 최적화를 수행합니다.

## <a name="best-practice-use-azure-cost-management"></a>모범 사례: Azure Cost Management 사용

Microsoft는 다음과 같이 지출을 추적하는 데 도움이 되는 Azure Cost Management를 제공합니다.

- Azure 지출을 모니터링 및 제어하고 리소스 사용을 최적화하도록 지원합니다.
- 전체 구독 및 해당하는 모든 리소스를 검토하고 추천 사항을 제공합니다.
- 보고를 위해 외부 도구와 재무 시스템을 통합하기 위한 완전한 API를 제공합니다.
- 통합된 단일 보기를 통해 리소스 사용량을 추적하고 클라우드 비용을 관리합니다.
- 정보에 기반하여 합리적인 의사 결정을 내릴 수 있도록 다양한 운영 및 재무 인사이트를 제공합니다.

Cost Management에서 수행할 수 있는 작업은 다음과 같습니다.


- **예산 만들기**: 독립 채산 방식의 예산을 만듭니다.
  - 특정 기간(월별, 분기별, 연도별) 및 범위(구독/리소스 그룹)에 대해 사용하거나 구독하는 서비스를 설명할 수 있습니다. 예를 들어 월별, 분기별 또는 연도별 기간에 대한 Azure 구독 예산을 만들 수 있습니다.
  - 예산이 만들어지면 비용 분석에 표시됩니다. 현재 지출에 대비하여 예산을 확인하는 것은 비용과 지출을 분석할 때 필요한 첫 번째 단계 중 하나입니다.
  - 예산 임계값에 도달하면 이메일 알림을 보낼 수 있습니다.
  - 분석을 위해 비용 관리 데이터를 Azure 스토리지에 내보낼 수 있습니다.

    ![비용된 관리 예산](./media/migrate-best-practices-costs/budget.png)
    
    *Azure Cost Management 예산*

- **비용 분석 수행**: 비용 분석을 통해 조직 비용을 검색 및 분석하여 비용이 발생하는 방식을 이해하고 지출 추세를 파악할 수 있습니다.
  - EA 사용자가 비용 분석을 사용할 수 있습니다.
  - 부서, 계정, 구독 또는 리소스 그룹별로 다양한 범위의 비용 분석 데이터를 볼 수 있습니다.
  - 당월의 총 비용과 누적된 일별 비용을 보여 주는 비용 분석을 얻을 수 있습니다. 

    ![비용 관리 분석](./media/migrate-best-practices-costs/analysis.png)
    
    *Azure Cost Management 분석*
    
- **추천 사항 가져오기**: 효율성을 최적화하고 향상시킬 수 는 방법을 보여 주는 Advisor 추천 사항을 가져옵니다.


**자세한 정보:**

- Azure Cost Management [개요 살펴보기](https://docs.microsoft.com/azure/cost-management/overview)
- Azure Cost Management를 통해 클라우드 투자를 최적화하는 방법에 대해 [알아보기](https://docs.microsoft.com/azure/cost-management/cost-mgt-best-practices)
- Azure Cost Management 보고서를 사용하는 방법에 대해 [알아보기](https://docs.microsoft.com/azure/cost-management/use-reports)
- 추천 사항에서 비용을 최적화하는 [자습서 살펴보기](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json)
- Azure 사용량 API [검토](https://docs.microsoft.com/rest/api/consumption/budgets)

## <a name="best-practice-monitor-resource-utilization"></a>모범 사례: 리소스 사용률 모니터링

Azure에서는 리소스를 사용할 때 사용하는 항목에 대해 비용을 지불하지만, 리소스를 사용하지 않을 때는 지불하지 않습니다. VM의 경우 VM 할당 시 요금 청구가 발생하며, VM 할당이 취소되면 요금이 청구되지 않습니다. 이를 염두에 두고서 사용 중인 VM을 모니터링하고 VM 크기를 확인해야 합니다.

- VM 워크로드를 지속적으로 평가하여 초기 계획을 결정합니다.
- 예를 들어 워크로드가 월요일에서 금요일까지, 오전 8시에서 오후 6시까지 많이 사용되지만 이러한 시간 외에는 거의 사용되지 않는 경우 피크 시간 이외의 시간에 VM을 다운그레이드할 수 있습니다. 즉 VM 크기를 변경하거나 가상 머신 확장 세트를 사용하여 VM의 크기를 자동으로 조정할 수 있습니다.
- 일부 회사에서는 VM을 사용할 수 있어야 하는 시기와 필요하지 않은 시기를 지정하는 일정에 배치하여 "다시 알려줍니다(snooze)". 
- VM 모니터링 외에도 ExpressRoute 및 가상 네트워크 게이트웨이와 같은 다른 네트워킹 리소스를 모니터링하여 과소하거나 과다하게 사용하지 않도록 방지합니다.
- Azure Cost Management, Azure Monitor 및 Azure Advisor를 포함한 다양한 Microsoft 도구를 사용하여 VM 사용량을 모니터링할 수 있습니다. 타사 도구도 사용할 수 있습니다.  

**자세한 정보:**
- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 및 [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview)에 대한 개요 살펴보기
- Advisor 비용 추천 사항 [살펴보기](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations)
- [추천 사항에서 비용을 최적화](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json)하고 [예기치 않은 요금을 방지](https://docs.microsoft.com/azure/billing/billing-getting-started)하는 방법에 대해 알아보기
- ARO(Azure Resource Optimization) 도구 키트에 대해 [알아보기](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/)

## <a name="best-practice-implement-resource-group-budgets"></a>모범 사례: 리소스 그룹 예산 구현

리소스 그룹이 비용 경계를 나타내는 데 사용되는 경우가 많습니다. Azure 팀에서는 이 사용 패턴과 함께 리소스 그룹 및 리소스에서 예산을 만들 수 있는 기능을 포함하여, 다양한 수준에서 리소스 지출을 추적하고 분석하는 새롭고 향상된 방법을 지속적으로 개발하고 있습니다.  

- 리소스 그룹 예산은 리소스 그룹과 연결된 비용을 추적하는 데 도움이 됩니다.
- 예산에 도달하거나 초과되면 경고를 트리거하고 다양한 플레이북을 실행할 수 있습니다. 

**자세한 정보:**

- Azure 예산을 사용하여 비용을 관리하는 방법에 대해 [알아보기](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)
- [자습서에 따라](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?toc=/azure/billing/TOC.json) Azure 예산 만들기 및 관리


## <a name="best-practice-optimize-azure-monitor-retention"></a>모범 사례: Azure Monitor 보존 기간 최적화

리소스를 Azure로 이동하고 진단 로깅을 사용하도록 설정하면 많은 로그 데이터가 생성됩니다. 일반적으로 이 로그 데이터는 Log Analytics 작업 영역에 매핑된 스토리지 계정으로 보내집니다.

- 로그 데이터 보존 기간이 길수록 더 많은 데이터를 보유하게 됩니다.
- 모든 로그 데이터가 동일한 것은 아니며, 일부 리소스는 다른 리소스보다 더 많은 로그 데이터를 생성합니다.
- 규정과 규정 준수로 인해 일부 리소스에 대한 로그 데이터를 다른 리소스보다 더 오래 유지해야 할 수 있습니다.
- 로그 스토리지 비용의 최적화와 필요한 로그 데이터의 유지 간에는 신중한 경계가 있어야 합니다.
- 중요하지 않은 로그를 유지하는 데 비용을 지출하지 않도록 마이그레이션이 완료되는 즉시 로깅을 평가하고 설정하는 것이 좋습니다.

**자세한 정보:**

- 사용량 모니터링 및 예상 비용에 대해 [알아보기](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs)
 
## <a name="best-practice-optimize-storage"></a>모범 사례: 스토리지 최적화

마이그레이션하기 전에 스토리지 선택에 대한 모범 사례를 따른 경우 몇 가지 이점을 누릴 수 있습니다. 그러나 여전히 최적화할 수 있는 추가 스토리지 비용이 있을 수도 있습니다. 시간이 지남에 따라 Blob과 파일이 부실하게 됩니다. 데이터는 더 이상 사용되지 않을 수 있지만, 규정 요구 사항은 특정 기간 동안 데이터를 유지해야 한다는 것을 의미할 수 있습니다. 따라서 원래 마이그레이션에 사용한 고성능 스토리지에 저장할 필요가 없습니다.

부실 데이터를 식별하고 저렴한 스토리지 영역으로 이동하는 것은 월별 스토리지 예산 및 비용 절감에 큰 영향을 줄 수 있습니다. Azure는 이 부실 데이터를 확인하고 저장하는 데 도움이 되는 여러 가지 방법을 제공합니다.

- 범용 v2 스토리지에 대한 액세스 계층을 활용하여 중요도가 낮은 데이터를 핫 계층에서 쿨 및 보관 계층으로 이동합니다.
- StorSimple을 사용하여 사용자 지정 정책에 따라 부실 데이터를 이동할 수 있도록 지원합니다. 

**자세한 정보:**

- 액세스 계층에 대해 [알아보기](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)
- StorSimple 개요 및 [StorSimple 가격 책정](https://azure.microsoft.com/pricing/details/storsimple/) [살펴보기](https://docs.microsoft.com/azure/azure-monitor/overview)

## <a name="best-practice-automate-vm-optimization"></a>모범 사례: VM 최적화 자동화

클라우드에서 VM을 실행하는 궁극적인 목표는 사용하는 CPU, 메모리 및 디스크를 최대화하는 것입니다. 최적화되지 않았거나 사용되지 않은 경우가 많은 VM이 검색되면 해당 VM을 종료하거나 VM 확장 세트를 사용하여 VM을 축소하는 것이 좋습니다.

Azure Automation, VM 확장 세트, 자동 종료 및 스크립트로 작성된 솔루션 또는 타사 솔루션을 사용하여 VM을 최적화할 수 있습니다. 

**자세한 정보:**

- 수직 자동 크기 조정을 사용하는 방법에 대해 [알아보기](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision)
- VM 자동 시작 [예약](https://azure.microsoft.com/updates/azure-devtest-labs-schedule-vm-auto-start/)
- Azure Automation에서 VM을 시작하거나 중지하는 방법에 대해 [알아보기](https://docs.microsoft.com/azure/automation/automation-solution-vm-management)
- [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) 및 [ARO(Azure Resource Optimization) 도구 키트](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/)에 대해 [자세히 살펴보기]

## <a name="best-practices-use-logic-apps-and-runbooks-with-budgets-api"></a>모범 사례: 예산 API에서 Logic Apps 및 Runbook 사용

Azure는 테넌트 청구 정보에 액세스할 수 있는 REST API를 제공합니다.

- 예산 API를 사용하여 API 데이터에서 작성한 메트릭으로 트리거되는 외부 시스템과 워크플로를 통합할 수 있습니다.
- 사용량 및 리소스 데이터를 기본 설정 데이터 분석 도구로 가져올 수 있습니다.
- Azure 리소스 사용량 및 RateCard API를 통해 비용을 정확하게 예측하고 관리할 수 있습니다.
- API는 리소스 공급자로 구현되며, Azure Resource Manager에서 공개하는 API에 포함됩니다.
- 예산 API는 Azure Logic Apps 및 Runbook과 통합할 수 있습니다.

**자세한 정보:**

- 예산 API에 대해 [자세히 알아보기](https://docs.microsoft.com/rest/api/consumption/budgets).
- 청구 API를 사용하여 Azure 사용량에 대한 [인사이트 얻기](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview)


## <a name="best-practice-implement-serverless-technologies"></a>모범 사례: 서버리스 기술 구현

가동 중지 시간을 방지하기 위해 VM 워크로드가 "있는 그대로" 마이그레이션되는 경우가 많습니다. 종종 VM에서 짧은 시간 또는 많은 시간이 걸리는 간헐적인 작업을 호스팅할 수 있습니다. 예를 들어 Windows 작업 스케줄러 또는 PowerShell 스크립트와 같이 예약된 작업을 실행하는 VM이 있습니다. 이러한 작업이 실행되고 있지 않은 경우에도 VM 및 디스크 스토리지 비용을 절감할 수 있습니다.

마이그레이션되면 이러한 유형의 작업을 철저히 검토한 후에 Azure Functions 또는 Azure Batch 작업과 같은 서버리스 기술로 마이그레이션하는 것이 좋습니다. 이 솔루션을 사용하면 VM을 더 이상 관리하고 유지 관리할 필요가 없으므로 비용을 추가로 절감할 수 있습니다. 

**자세한 정보:**
- Azure Functions에 대해 [알아보기](https://azure.microsoft.com/services/functions/)
- Azure Batch에 대해 [알아보기](https://azure.microsoft.com/services/batch/)
  
## <a name="next-steps"></a>다음 단계 

다른 모범 사례를 검토합니다.

- 마이그레이션 이후의 보안 및 관리에 대한 [모범 사례](migrate-best-practices-security-management.md)
- 마이그레이션 이후의 네트워킹에 대한 [모범 사례](migrate-best-practices-networking.md)

