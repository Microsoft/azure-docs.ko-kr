---
title: Azure Advisor를 사용하여 서비스 비용 절감
description: Azure Advisor를 사용하여 Azure 배포 비용을 최적화합니다.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: d234e89d0d042999805fae73d3df24c03d1027c9
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654041"
---
# <a name="reduce-service-costs-by-using-azure-advisor"></a>Azure Advisor를 사용 하 여 서비스 비용 절감

Azure Advisor를 사용 하면 유휴 리소스와 미달 사용 리소스를 식별 하 여 전체 Azure 사용을 최적화 하 고 절감할 수 있습니다.Advisor 대시보드의 **비용** 탭에서 비용 관련 권장 지침을 얻을 수 있습니다.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>사용량이 낮은 인스턴스의 크기를 조정하거나 종료하여 가상 머신 소비 최적화 

특정 애플리케이션 시나리오에서는 기본적으로 사용률이 낮을 수 있으나 가상 머신의 크기와 수를 관리하여 비용을 절감할 수도 있습니다. 

권장 되는 작업은 평가 중인 리소스와 관련 하 여 종료 하거나 크기를 조정 하는 것입니다.

Advisor의 고급 평가 모델은 두 문이 모두 true 인 경우 가상 머신을 종료 하는 것으로 간주 합니다. 
- 최대 CPU 사용률 값의 P95th 3% 미만입니다. 
- 7 일 동안 네트워크 사용률이 2% 미만입니다.
- 메모리가 중 값이 임계값 보다 낮습니다.

Advisor는 다음과 같이 더 작은 SKU (동일한 SKU 제품군 내) 또는 더 적은 수의 인스턴스를 현재 부하에 맞출 수 있는 경우 가상 컴퓨터의 크기를 조정 하는 것으로 간주 합니다.
- 현재 부하가 사용자가 연결 되지 않은 작업에 대해 80%의 사용률을 초과 하지 않습니다. 
- 사용자 지향 작업의 경우 로드는 40% 이상으로 이동 하지 않습니다. 

여기에서 Advisor는 워크 로드의 CPU 사용률 특성을 분석 하 여 워크 로드의 유형을 결정 합니다.

Advisor는 크기 조정 또는 종료의 권장 조치에 대 한 예상 비용 절감을 보여 줍니다. 크기 조정의 경우 Advisor는 현재 및 대상 SKU 정보를 제공 합니다.

미달 사용 가상 컴퓨터를 식별 하는 방법에 대 한 자세한 내용을 보려면 구독 별로 CPU 사용률 규칙을 조정할 수 있습니다.

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>적절 한 크기 조정으로 Aadb, MySQL 및 PostgreSQL 서버에 대 한 비용 최적화 
Advisor는 사용자의 사용량을 분석 하 고, MariaDB, MySQL 또는 PostgreSQL database 서버 리소스가 지난 7 일 동안 오랜 시간 동안 미달 사용 되었는지 평가 합니다. 리소스 사용률이 낮으면 원치 않는 비용이 발생 하 여 심각한 성능 영향 없이 해결할 수 있습니다. 비용을 줄이고 리소스를 효율적으로 관리 하려면 계산 크기 (vCores)를 절반으로 줄이는 것이 좋습니다.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>프로비저닝되지 않은 ExpressRoute 회로를 제거하여 비용 절감

Advisor는 두 달 이상 **프로 비전 되지 않음** 의 공급자 상태에 있는 Azure express 경로 회로를 식별 합니다. 연결 공급자로 회로를 프로 비전 하지 않으려는 경우 회로를 삭제 하는 것이 좋습니다.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>유휴 상태의 가상 네트워크 게이트웨이를 삭제하거나 다시 구성하여 비용 절감

Advisor는 90 일 넘게 유휴 상태 였던 가상 네트워크 게이트웨이를 식별 합니다. 이러한 게이트웨이는 매시간 청구 되므로 더 이상 사용 하지 않으려는 경우 다시 구성 하거나 삭제 하는 것을 고려해 야 합니다. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>예약 가상 머신 인스턴스를 구매하여 종량제 비용보다 비용 절약

Advisor는 지난 30 일 동안 가상 머신 사용량을 검토 하 여 Azure 예약을 구매 하 여 비용을 절감할 수 있는지 확인 합니다. Advisor는 절감 액이 가장 높고 구매 예약의 예상 절감 액을 보여 주는 지역과 크기를 보여 줍니다. Azure 예약을 사용하여 가상 머신에 대한 기본 비용을 미리 구입할 수 있습니다. 할인은 예약에 따라 크기와 지역이 동일한 신규 또는 기존 Vm에 자동으로 적용 됩니다. [Azure Reserved VM Instances에 대한 자세한 정보](https://azure.microsoft.com/pricing/reserved-vm-instances/)

또한 Advisor는 30 일 후에 만료 되는 예약 인스턴스를 알려 줍니다. 종 량 제 가격을 방지 하기 위해 새 예약 인스턴스를 구매 하는 것이 좋습니다.

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>여러 리소스 유형에 대 한 예약 인스턴스를 구매 하 여 종 량 제 비용을 절약 하세요.

Advisor는 다음 리소스에 대 한 지난 30 일간의 사용 패턴을 분석 하 고 비용을 최적화 하는 예약 된 용량 구매를 권장 합니다.

### <a name="azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB 예약 된 용량
Advisor는 지난 30 일간의 Azure Cosmos DB 사용 패턴을 분석 하 고 비용을 최적화 하기 위해 예약 된 용량 구매를 권장 합니다. 예약 된 용량을 사용 하 여 시간별 사용량 Azure Cosmos DB 사전 구매 하 고 종 량 제 비용을 절감할 수 있습니다. 예약 된 용량은 청구 혜택이 며 새 배포 및 기존 배포에 자동으로 적용 됩니다. Advisor는 지난 30 일 동안 관찰 된 사용 패턴을 추정 하 여 각 구독에 대 한 비용 절감 액을 계산 합니다. 공유 범위 권장 사항은 예약 된 용량 구매에 사용할 수 있으며 절감 액을 높일 수 있습니다.

### <a name="sql-paas-reserved-capacity"></a>SQL PaaS 예약 용량
Advisor는 지난 30 일 동안 SQL PaaS 탄력적 데이터베이스 풀 및 SQL Managed Instance 사용 패턴을 분석 합니다. 그런 다음 비용을 최적화 하는 예약 된 용량 구매를 권장 합니다. 예약 된 용량을 사용 하 여 SQL DB 시간별 사용량을 미리 구매 하 고 SQL 계산 비용을 절감할 수 있습니다. SQL 라이선스는 별도로 청구 되며 예약에 의해 할인 되지 않습니다. 예약 된 용량은 청구 혜택이 며 새 배포 및 기존 배포에 자동으로 적용 됩니다. Advisor는 지난 30 일 동안 관찰 된 사용 패턴을 추정 하 여 각 구독에 대 한 비용 절감 액을 계산 합니다. 공유 범위 권장 사항은 예약 된 용량 구매에 사용할 수 있으며 절감 액을 높일 수 있습니다.

### <a name="app-service-stamp-fee-reserved-capacity"></a>App Service 스탬프 요금 예약 용량
Advisor는 지난 30 일 동안 Azure App Service 격리 된 환경에 대 한 스탬프 요금 사용 패턴을 분석 하 고 비용을 최적화 하는 예약 된 용량 구매를 권장 합니다. 예약 된 용량을 사용 하면 격리 된 환경 스탬프 요금에 대 한 시간별 사용량을 사전에 구매 하 여 종 량 제 비용을 절감할 수 있습니다. 예약 된 용량은 스탬프 요금에만 적용 되 고 App Service 인스턴스에는 적용 되지 않습니다. 예약 된 용량은 청구 혜택이 며 새 배포 및 기존 배포에 자동으로 적용 됩니다. Advisor는 지난 30 일 동안의 사용 패턴을 기반으로 3 년간의 예약 가격 책정을 사용 하 여 개별 구독에 대 한 예상 절감 액을 계산 합니다.

### <a name="blob-storage-reserved-capacity"></a>Blob storage 예약 용량
Advisor는 지난 30 일 동안 Azure Blob 저장소 및 Azure Data Lake 저장소 사용량을 분석 합니다. 그런 다음 비용을 최적화 하는 예약 된 용량 구매를 계산 합니다. 예약 된 용량을 사용 하면 시간별 사용량을 미리 구매 하 고 현재 주문형 비용을 절감할 수 있습니다. Blob storage 예약 용량은 Azure Blob 범용 v2 및 Azure Data Lake Storage Gen2 계정에 저장 된 데이터에만 적용 됩니다. 예약 된 용량은 청구 혜택이 며 새 배포 및 기존 배포에 자동으로 적용 됩니다. Advisor는 지난 30 일 동안 관찰 된 3 년 예약 가격 및 사용 패턴을 사용 하 여 개별 구독에 대 한 비용 절감 액을 계산 합니다. 공유 범위 권장 사항은 예약 된 용량 구매에 사용할 수 있으며 절감 액을 높일 수 있습니다.

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>MariaDB, MySQL 및 PostgreSQL reserved capacity
Advisor는 지난 30 일 동안 Azure Database for MariaDB, Azure Database for MySQL 및 Azure Database for PostgreSQL에 대 한 사용 패턴을 분석 합니다. 그런 다음 비용을 최적화 하는 예약 된 용량 구매를 권장 합니다. 예약 된 용량을 사용 하 여 Aadb, MySQL 및 PostgreSQL 시간별 사용량을 사전 구매 하 고 현재 비용을 절감할 수 있습니다. 예약 된 용량은 청구 혜택이 며 새 배포 및 기존 배포에 자동으로 적용 됩니다. Advisor는 지난 30 일 동안 관찰 된 3 년 예약 가격 및 사용 패턴을 사용 하 여 개별 구독에 대 한 비용 절감 액을 계산 합니다. 공유 범위 권장 사항은 예약 된 용량 구매에 사용할 수 있으며 절감 액을 높일 수 있습니다.

### <a name="azure-synapse-analytics-formerly-sql-data-warehouse-reserved-capacity"></a>Azure Synapse Analytics (이전의 SQL Data Warehouse) reserved capacity
Advisor는 지난 30 일 동안 Azure Synapse Analytics 사용 패턴을 분석 하 고 비용을 최적화 하는 예약 된 용량 구매를 권장 합니다. 예약 된 용량을 사용 하 여 Synapse Analytics 시간별 사용량을 사전에 구매 하 고 주문형 비용을 절감할 수 있습니다. 예약 된 용량은 청구 혜택이 며 새 배포 및 기존 배포에 자동으로 적용 됩니다. Advisor는 지난 30 일 동안 관찰 된 3 년 예약 가격 및 사용 패턴을 사용 하 여 개별 구독에 대 한 비용 절감 액을 계산 합니다. 공유 범위 권장 사항은 예약 된 용량 구매에 사용할 수 있으며 절감 액을 높일 수 있습니다.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>연결 되지 않은 공용 IP 주소를 삭제 하 여 비용 절감

Advisor는 부하 분산 장치 및 Vm과 같은 Azure 리소스와 연결 되지 않은 공용 IP 주소를 식별 합니다. 명목상 요금은 이러한 공용 IP 주소와 연결 됩니다. 이러한 옵션을 사용 하지 않으려는 경우 삭제 하 여 비용을 절감할 수 있습니다.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>실패한 Azure Data Factory 파이프라인 삭제

Advisor는 반복적으로 실패 하는 Azure Data Factory 파이프라인을 검색 합니다. 필요 하지 않은 경우 문제를 해결 하거나 파이프라인을 삭제 하는 것이 좋습니다. 오류가 발생 하는 동안에는 제공 하지 않더라도 이러한 파이프라인에 대 한 요금이 청구 됩니다.

## <a name="use-standard-snapshots-for-managed-disks"></a>관리 디스크에 표준 스냅숏 사용
60%의 비용을 절감 하려면 부모 디스크의 저장소 유형과 관계 없이 standard storage에 스냅숏을 저장 하는 것이 좋습니다. 이 옵션은 관리 디스크 스냅숏의 기본 옵션입니다. Advisor는 premium storage에 저장 된 스냅숏을 식별 하 고 premium에서 standard storage로 마이그레이션 하는 것을 권장 합니다. [관리 디스크 가격 책정에 대해 자세히 알아보세요.](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="use-lifecycle-management"></a>수명 주기 관리 사용
Advisor는 Azure Blob 저장소 개체 수, 총 크기 및 트랜잭션에 대 한 인텔리전스를 사용 하 여 하나 이상의 저장소 계정에서 데이터를 계층화 하는 주기 관리를 사용 하도록 설정 해야 하는지 여부를 검색 합니다. 응용 프로그램 호환성을 위해 Azure Blob storage에 데이터를 유지 하면서 데이터를 쿨 또는 보관 저장소로 자동으로 계층화 하 여 저장소 비용을 최적화 하는 수명 주기 관리 규칙을 만드는 메시지를 표시 합니다.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>사용 후 삭제되는 OS 디스크 만들기 권장 사항
사용 [후 삭제 OS 디스크](../virtual-machines/ephemeral-os-disks.md) 를 사용 하 여 다음을 수행할 수 있습니다. 
- OS 디스크에 대 한 저장소 비용을 절약 합니다. 
- OS 디스크에 대 한 읽기/쓰기 대기 시간을 줄입니다. 
- OS 및 임시 디스크를 원래 상태로 다시 설정 하 여 VM 이미지로 다시 설치 작업을 빠르게 수행 합니다.

수명이 짧은 IaaS Vm 또는 상태 비저장 워크 로드를 사용 하는 Vm에 대해서는 사용 후 삭제 OS 디스크를 사용 하는 것이 좋습니다. Advisor는 임시 OS 디스크를 사용할 수 있는 리소스에 대 한 권장 사항을 제공 합니다.

## <a name="reduce-azure-data-explorer-table-cache-period-policy-for-cluster-cost-optimization-preview"></a>클러스터 비용 최적화 (미리 보기)에 대 한 Azure 데이터 탐색기 테이블 캐시-기간 (정책) 줄이기
Advisor는 테이블 캐시 정책을 줄이면 CPU 사용률, 메모리 및 높은 캐시 크기 구성이 있는 Azure 데이터 탐색기 클러스터 노드를 해제할 수 있는 리소스를 식별 합니다.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Azure Advisor에서 비용 권장 사항에 액세스하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 모든 페이지에서 [**Advisor**](https://aka.ms/azureadvisordashboard) 를 검색 하 고 선택 합니다.

1. **Advisor** 대시보드에서 **비용** 탭을 선택 합니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor 작동의 뛰어난 권장 사항](advisor-operational-excellence-recommendations.md)
