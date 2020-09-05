---
title: Azure 구독 제한 및 할당량
description: 일반적인 Azure 구독 및 서비스 제한, 할당량 및 제약 조건 목록을 제공합니다. 이 문서에는 최대 값으로 제한을 늘리는 방법에 대 한 정보가 포함 되어 있습니다.
ms.topic: conceptual
author: davidsmatlak
ms.date: 06/04/2020
ms.openlocfilehash: 86c9958818b5439502ab37471ed7a51fb3f21bf9
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854056"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure 구독 및 서비스 제한, 할당량 및 제약 조건

이 문서는 때때로 할당량이라고도 하는 가장 일반적인 Microsoft Azure 제한의 일부를 나열합니다.

Azure 가격 책정에 대해 자세히 알아보려면 [azure 가격 책정 개요](https://azure.microsoft.com/pricing/)를 참조 하세요. 여기서 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용 하 여 비용을 예측할 수 있습니다. 특정 서비스 (예: [Windows vm](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows))에 대 한 가격 정보 페이지로 이동할 수도 있습니다. 비용 관리에 대한 팁은 [Azure 청구 및 비용 관리를 사용하여 예상치 못한 비용 방지](../../cost-management-billing/manage/getting-started.md)를 참조하세요.

## <a name="managing-limits"></a>제한 관리

> [!NOTE]
> 일부 서비스에는 제한 사항이 있습니다.
>
> 서비스의 제한이 없는 경우 다음 표에서는 헤더 **제한을**사용 합니다. 이러한 경우에는 기본값과 최대 한도가 동일 합니다.
>
> 제한을 조정할 수 있는 경우 테이블에는 **기본 제한** 및 **최대 제한** 헤더가 포함 됩니다. 제한은 기본 한도를 초과 하는 경우에만 발생할 수 있으며 최대 한도를 초과 하면 안 됩니다.
>
> 제한 또는 할당량을 기본 한도 이상으로 올리려면 [무료로 온라인 고객 지원 요청을 여세요](../templates/error-resource-quota.md).

[무료 평가판 구독](https://azure.microsoft.com/offers/ms-azr-0044p) 에는 제한 또는 할당량 증가가 제공 되지 않습니다. [평가판 구독](https://azure.microsoft.com/offers/ms-azr-0044p)을 사용하는 경우 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 구독으로 업그레이드할 수 있습니다. 자세한 내용은 [Azure 무료 평가판 구독을 종 량 제 구독](../../cost-management-billing/manage/upgrade-azure-subscription.md) 및 [무료 평가판 구독 FAQ](https://azure.microsoft.com/free/free-account-faq)로 업그레이드를 참조 하세요.

일부 제한은 지역 수준에서 관리 됩니다.

vCPU 할당량을 한 예로 살펴보겠습니다. VCPUs를 지 원하는 할당량 증가를 요청 하려면 어떤 지역에서 사용 하려는 vCPUs 수를 결정 해야 합니다. 그런 다음 원하는 금액 및 지역에 대 한 Azure 리소스 그룹 vCPU 할당량에 대 한 특정 요청을 만듭니다. 유럽 서부에서 30 개의 vCPUs를 사용 하 여 응용 프로그램을 실행 해야 하는 경우 유럽 서부에서 30 개의 vCPUs를 구체적으로 요청 합니다. VCPU 할당량은 다른 지역에서 증가 되지 않습니다. 전용 유럽 서부 30 vCPU 할당량이 있습니다.

따라서 한 지역에서 워크 로드에 대 한 Azure 리소스 그룹 할당량이 무엇 인지 결정 합니다. 그런 다음 배포 하려는 각 지역에서 해당 금액을 요청 합니다. 특정 지역에 대 한 현재 할당량을 확인 하는 방법에 대 한 도움말은 [리소스 할당량에 대 한 오류 해결](../templates/error-resource-quota.md)을 참조 하세요.

## <a name="general-limits"></a>일반 제한

리소스 이름에 대 한 제한은 [Azure 리소스에 대 한 명명 규칙 및 제한 사항](resource-name-rules.md)을 참조 하세요.

Resource Manager API 읽기 및 쓰기 제한은 [Resource Manager 요청 제한](request-limits-and-throttling.md)을 참조하세요.

### <a name="management-group-limits"></a>관리 그룹 제한

[관리 그룹](../../governance/management-groups/overview.md)에는 다음과 같은 제한이 적용 됩니다.

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>구독 제한

Azure Resource Manager 및 Azure 리소스 그룹을 사용 하는 경우 다음 제한이 적용 됩니다.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>리소스 그룹 제한

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Active Directory 제한

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>API Management 제한

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>App Service 제한

다음 App Service 제한에는 Web Apps, Mobile Apps 및 API Apps에 대한 제한이 포함됩니다.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Automation 한도

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Azure Cache for Redis 제한

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Azure Cloud Services 제한

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Azure Cognitive Search 제한

가격 책정 계층은 검색 서비스의 용량 및 제한을 결정합니다. 계층은 다음을 포함합니다.

* 다른 Azure 구독자와 공유 되는 **무료** 다중 테 넌 트 서비스는 평가 및 소규모 개발 프로젝트를 위한 것입니다.
* **기본** 은 높은 가용성의 쿼리 작업에 대한 최대 3개의 복제본과 함께 프로덕션 워크로드 전용 컴퓨팅 리소스를 더 작은 규모로 제공합니다.
* S1, S2, S3 및 S3 고밀도를 포함 하는 **표준**은 대규모 프로덕션 워크 로드를 위한 것입니다. 표준 계층 내에는 여러 수준이 있으므로 워크 로드 프로필과 가장 일치 하는 리소스 구성을 선택할 수 있습니다.

**구독당 제한**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**검색 서비스당 제한**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

문서 크기, 초당 쿼리, 키, 요청 및 응답과 같이 보다 세분화 된 수준에 대 한 제한에 대 한 자세한 내용은 [Azure Cognitive Search의 서비스 제한](../../search/search-limits-quotas-capacity.md)을 참조 하세요.

## <a name="azure-cognitive-services-limits"></a>Azure Cognitive Services 제한

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB 제한

Azure Cosmos DB 제한에 대해서는 [Azure Cosmos DB 제한](../../cosmos-db/concepts-limits.md)을 참조 하세요.

## <a name="azure-data-explorer-limits"></a>Azure 데이터 탐색기 제한

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Azure Database for MySQL 제한은 [Azure Database for MySQL의 제한 사항](../../mysql/concepts-limits.md)을 참조하세요.

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

Azure Database for PostgreSQL 제한은 [Azure Database for PostgreSQL의 제한 사항](../../postgresql/concepts-limits.md)을 참조하세요.

## <a name="azure-functions-limits"></a>Azure Functions 제한

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

자세한 내용은 [함수 호스팅 계획 비교](../../azure-functions/functions-scale.md#hosting-plans-comparison)를 참조 하세요.

## <a name="azure-kubernetes-service-limits"></a>Azure Kubernetes 서비스 제한

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Azure Machine Learning 제한

Azure Machine Learning 계산 할당량의 최신 값은 [Azure Machine Learning 할당량 페이지](../../machine-learning/how-to-manage-quotas.md) 에서 찾을 수 있습니다.

## <a name="azure-maps-limits"></a>Azure Maps 제한

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Azure Monitor 제한

### <a name="alerts"></a>경고

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>작업 그룹

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="log-queries-and-language"></a>로그 쿼리 및 언어

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Log Analytics 작업 영역

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Azure Policy 제한

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-signalr-service-limits"></a>Azure SignalR 서비스 제한

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>Backup 제한

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Batch 제한

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>클래식 배포 모델 제한

Azure Resource Manager 배포 모델 대신 클래식 배포 모델을 사용 하는 경우 다음과 같은 제한이 적용 됩니다.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Container Instances 제한

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Container Registry 제한

다음 표에서는 Basic, Standard 및 Premium [서비스 계층](../../container-registry/container-registry-skus.md)의 기능 및 제한 사항에 대해 자세히 설명 합니다.

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Content Delivery Network 제한

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>데이터 팩터리 제한

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Data Lake Analytics 제한

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-store-limits"></a>Data Lake Store 제한

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>데이터 공유 제한

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Database Migration Service 제한

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="digital-twins-limits"></a>Digital Twins 제한

> [!NOTE]
> 이 서비스의 일부 영역에는 제한이 적용 되지 않습니다. 이는 아래 표에는 *조정 가능한?* 열이 표시 됩니다. 제한을 조정할 수 있는 경우 *조정 가능한?* 값은 *예*입니다.

[!INCLUDE [digital-twins-limits](../../../includes/digital-twins-limits.md)]

## <a name="event-grid-limits"></a>Event Grid 제한

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Event Hubs 제한

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="identity-manager-limits"></a>Identity Manager 제한

[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

## <a name="iot-central-limits"></a>IoT Central 제한
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>IoT Hub 제한

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>IoT Hub Device Provisioning Service 제한

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>키 값 제한

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="media-services-limits"></a>Media Services 제한

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Media Services v2(레거시)

Media Services v2 (레거시)에 한정 되는 한도 [Media Services v2 (레거시)](../../media-services/previous/media-services-quotas-and-limitations.md) 를 참조 하세요.

## <a name="mobile-services-limits"></a>Mobile Services 제한

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Multi-Factor Authentication 제한

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>네트워킹 제한

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>Express 경로 제한

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-network-gateway-limits"></a>Virtual Network 게이트웨이 제한

[!INCLUDE [virtual-wan-limits](../../../includes/azure-virtual-network-gateway-limits.md)]

### <a name="virtual-wan-limits"></a>가상 WAN 제한

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Application Gateway 제한

다음 표는 달리 언급하지 않는 한 v1, v2, 표준 및 WAF SKU에 적용됩니다.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Network Watcher 제한

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Private Link 제한

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

### <a name="traffic-manager-limits"></a>Traffic Manager 제한

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Azure 방호 제한

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Azure DNS 제한

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Azure Firewall 제한

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Azure Front Door Service 제한

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Notification Hubs 제한

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="role-based-access-control-limits"></a>역할 기반 액세스 제어 한도

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control-limits.md)]

## <a name="service-bus-limits"></a>Service Bus 제한

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>사이트 복구 제한

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>SQL Database 제한

SQL Database 한도에 대해서는 [단일 데이터베이스에 대 한 리소스 제한 SQL Database](../../azure-sql/database/resource-limits-vcore-single-databases.md) [SQL Database, 탄력적 풀 및 풀링된 데이터베이스에](../../azure-sql/database/resource-limits-vcore-elastic-pools.md)대 한 리소스 제한 및 [SQL Managed Instance에 대 한 SQL Database 리소스 제한](../../azure-sql/managed-instance/resource-limits.md)을 참조 하세요.

## <a name="azure-synapse-analytics-limits"></a>Azure Synapse Analytics 제한

Azure Synapse Analytics 제한에 대해서는 [Azure Synapse 리소스 제한](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)을 참조 하세요.

## <a name="storage-limits"></a>스토리지 제한

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Standard storage 계정에 대 한 제한 사항에 대 한 자세한 내용은 [standard storage 계정에 대 한 확장성 목표](../../storage/common/scalability-targets-standard-account.md)를 참조 하세요.

### <a name="storage-resource-provider-limits"></a>Storage 리소스 공급자 제한

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Azure Blob Storage 용량 한도

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-files-limits"></a>Azure Files 한도

Azure Files 제한에 대 한 자세한 내용은 [Azure Files 확장성 및 성능 목표](../../storage/files/storage-files-scale-targets.md)를 참조 하세요.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-limits"></a>Azure 파일 동기화 한도

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Azure Queue storage 용량 한도

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Azure Table Storage 용량 한도

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>가상 머신 디스크 제한

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

자세한 내용은 [가상 머신 크기](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조 하세요.

#### <a name="disk-encryption-sets"></a>디스크 암호화 집합

구독 당 지역 당 50 디스크 암호화 집합의 제한이 있습니다. 자세한 내용은 [Linux](../../virtual-machines/linux/disk-encryption.md#restrictions) 또는 [Windows](../../virtual-machines/windows/disk-encryption.md#restrictions) 가상 머신에 대 한 암호화 설명서를 참조 하세요. 할당량을 늘려야 하는 경우 Azure 지원에 문의 하세요.

### <a name="managed-virtual-machine-disks"></a>관리되는 가상 머신 디스크

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>관리되지 않는 가상 머신 디스크

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>StorSimple 시스템 제한

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Stream Analytics 제한

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Virtual Machines 제한

### <a name="virtual-machines-limits"></a>Virtual Machines 제한

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtual Machines 제한 - Azure 리소스 관리자

Azure Resource Manager 및 Azure 리소스 그룹을 사용 하는 경우 다음 제한이 적용 됩니다.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>공유 이미지 갤러리 제한

Shared Image Gallery를 사용하여 리소스를 배포할 때 구독당 제한이 있습니다.

- 구독마다, 지역마다 공유 이미지 갤러리 100개
- 구독마다, 지역마다 이미지 정의 1,000개
- 구독마다, 지역마다 이미지 버전 10,000개

## <a name="virtual-machine-scale-sets-limits"></a>가상 머신 확장 집합 제한

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>참고 항목

* [Azure 제한 및 향상 이해](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Azure의 가상 컴퓨터 및 클라우드 서비스 크기](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure Cloud Services의 크기](../../cloud-services/cloud-services-sizes-specs.md)
* [Azure 리소스에 대한 명명 규칙 및 제한 사항](resource-name-rules.md)