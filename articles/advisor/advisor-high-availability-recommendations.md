---
title: Azure Advisor를 사용하여 애플리케이션의 가용성 향상
description: Azure Advisor를 사용하여 Azure 배포의 가용성을 향상시킵니다.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 997681ed62fa9985e3122ece22565dbae0e65b53
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443102"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Azure Advisor를 사용하여 애플리케이션의 가용성 향상

Azure Advisor는 업무상 중요한 애플리케이션의 연속성을 보장하고 향상시키는 데 도움이 됩니다. Advisor 대시보드의 **고가용성** 탭에서 Advisor의 고가용성 권장 사항을 가져올 수 있습니다.

## <a name="ensure-virtual-machine-fault-tolerance"></a>가상 머신 내결함성 보장

애플리케이션에 중복성을 제공하기 위해 여러 개의 가상 머신을 가용성 집합으로 그룹화하는 것이 좋습니다. Advisor는 가용성 집합에 속하지 않는 가상 머신을 식별하고 이러한 가상 머신을 가용성 집합으로 이동할 것을 권장합니다. 이 구성은 계획된 유지 관리 또는 계획되지 않은 유지 관리 이벤트 중에 적어도 하나의 가상 머신을 사용할 수 있고 Azure 가상 머신 SLA가 충족되도록 합니다. 가상 머신에 대한 가용성 집합을 만들거나 기존 가용성 집합에 가상 머신을 추가할 수도 있습니다.

> [!NOTE]
> 가용성 집합을 만들도록 선택한 경우 해당 가용성 집합에 적어도 하나 이상의 가상 컴퓨터를 추가해야 합니다. 중단 시간 동안 하나 이상의 컴퓨터를 사용할 수 있도록 하기 위해 가용성 집합에 둘 이상의 가상 머신을 그룹화하는 것이 좋습니다.

## <a name="ensure-availability-set-fault-tolerance"></a>가용성 집합 내결함성 보장

애플리케이션에 중복성을 제공하기 위해 여러 개의 가상 머신을 가용성 집합으로 그룹화하는 것이 좋습니다. Advisor는 단일 가상 머신을 포함하는 가용성 집합을 식별하고 여기에 하나 이상의 가상 머신을 추가할 것을 권장합니다. 이 구성은 계획 된 유지 관리 또는 계획 되지 않은 유지 관리 이벤트 중에 하나 이상의 가상 머신을 사용할 수 있고 Azure virtual machine SLA를 충족 하도록 합니다. 가상 컴퓨터를 만들거나 기존 가상 컴퓨터를 가용성 집합에 추가 하도록 선택할 수 있습니다.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Managed Disks를 사용하여 데이터 안정성 개선

여러 디스크가 스토리지 계정 또는 스토리지 배율 단위를 공유하는 가용성 세트의 가상 머신은 중단 시 단일 스토리지 배율 단위 오류에서 복원되지 않습니다. Advisor는 이러한 가용성 세트를 식별하여 Azure Managed Disks로 마이그레이션하는 것이 좋다는 메시지를 표시합니다. 따라서 단일 실패 지점이 발생하지 않도록 가용성 세트의 서로 다른 가상 머신을 포함하는 디스크를 충분히 격리해야 합니다. 

## <a name="ensure-application-gateway-fault-tolerance"></a>애플리케이션 게이트웨이 내결함성 보장

이 권장 사항은 애플리케이션 게이트웨이에서 제공하는 중요 업무용 애플리케이션의 비즈니스 연속성을 보장합니다. Advisor는 내결함성을 위해 구성되지 않은 애플리케이션 게이트웨이 인스턴스를 식별하고 수행할 수 있는 수정 작업을 제안합니다. Advisor는 중간 규모 또는 대규모 단일 인스턴스 애플리케이션 게이트웨이를 식별하고 하나 이상의 인스턴스 추가를 권장합니다. 또한 단일 또는 다중 인스턴스 소규모 애플리케이션 게이트웨이를 식별하고 중간 규모 또는 대규모 SKU로 마이그레이션을 권장합니다. Advisor는 애플리케이션 게이트웨이 인스턴스가 이러한 리소스에 대한 현재 SLA 요구 사항을 만족하도록 구성되어 있는지 확인하는 데 이러한 작업을 권장합니다.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>가상 머신 데이터를 실수로 삭제되지 않도록 보호

가상 머신 백업 설정은 비즈니스에 중요한 데이터의 가용성을 보장하고 실수로 인한 삭제 또는 손상에 대한 보호를 제공합니다. Advisor는 백업이 활성화되지 않은 가상 머신을 식별하고 백업을 활성화할 것을 권장합니다. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>필요할 때 Azure 클라우드 전문가에 대한 액세스 권한이 있는지 확인

중요 비즈니스용 워크로드를 실행할 때 필요한 경우 기술 지원에 대한 액세스 권한이 있어야 합니다. Advisor는 해당 지원 플랜에 포함된 기술 지원이 없는 잠재적 중요 비즈니스용 구독을 식별하고 기술 지원을 포함하는 옵션으로 업그레이드할 것을 권장합니다.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Azure 문제가 영향을 미칠 때 알림을 받도록 Azure Service Health 경고 만들기

Azure 문제가 영향을 미칠 때 알림을 받도록 Azure Service Health 경고를 만드는 것이 좋습니다. [Azure Service Health](https://azure.microsoft.com/features/service-health/)는 Azure 서비스 문제가 영향을 미칠 경우 개인화된 지침과 지원을 제공하는 무료 서비스입니다. Advisor는 경고가 구성되지 않은 구독을 식별하고 구독을 생성하도록 권장합니다.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>복원력을 위해 Traffic Manager 엔드포인트 구성

여러 엔드포인트가 있는 Traffic Manager 프로필은 특정 엔드포인트가 실패할 경우 가용성이 더 높습니다. 서로 다른 지역에 엔드포인트를 배치하면 서비스 복원력을 더 높일 수 있습니다. Advisor는 엔드포인트가 하나뿐인 Traffic Manger 프로필을 식별하고 다른 지역에 하나 이상의 엔드포인트를 더 추가하도록 권장합니다.

근접 라우팅에 대해 구성된 Traffic Manger 프로필의 모든 엔드포인트가 같은 지역에 있는 경우 다른 지역의 사용자는 연결 지연을 경험할 수 있습니다. 엔드포인트를 다른 지역에 추가하거나 다른 지역으로 이동하면 한 지역의 모든 엔드포인트가 실패하는 경우 가용성을 더 높일 수 있습니다. Advisor는 모든 엔드포인트가 같은 지역에 있는 근접 라우팅에 대해 구성된 Traffic Manager 프로필을 식별합니다. 다른 Azure 지역에 엔드포인트를 추가하거나 이동하는 것이 좋습니다.

Traffic Manager 프로필이 지리적 라우팅에 대해 구성된 경우 트래픽은 정의된 지역에 따라 엔드포인트로 라우팅됩니다. 한 지역이 실패하면 미리 정의된 장애 조치(failover)가 없습니다. 자역 그룹화가 “모두(세계)”로 구성된 엔드포인트가 있으면 트래픽 폐기를 방지하고 서비스 가용성을 높입니다. Advisor는 지역 그룹화가 “모두(세계)”로 구성된 엔드포인트가 없으며 지리적 라우팅에 대해 구성된 Traffic Manager 프로필을 식별합니다. 엔드포인트를 “모두(세계)”로 만들도록 구성을 변경하는 것이 좋습니다.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>실수로 데이터를 덮어쓰거나 삭제한 후에 데이터를 저장하고 복구할 수 있도록 Azure Storage 계정에 대해 일시 삭제 기능 사용

삭제한 Blob가 영구적으로 삭제되지 않고 일시 삭제된 상태로 전환되도록 스토리지 계정에 대해 [일시 삭제](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)를 사용하도록 설정합니다. 데이터를 덮어쓰는 경우 덮어쓴 데이터의 상태를 저장하기 위해 일시 삭제된 스냅샷이 생성됩니다. 일시 삭제 기능을 사용하면 실수로 삭제하거나 덮어쓰는 경우 복구할 수 있습니다. Advisor는 일시 삭제 기능이 사용하도록 설정되지 않은 Azure Storage 계정을 식별하여 해당 기능을 사용하도록 제안합니다.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>연결 복원을 위해 VPN 게이트웨이를 활성-활성으로 구성

활성-활성 구성에서 VPN 게이트웨이의 두 인스턴스는 모두 온-프레미스 VPN 장치에 대한 S2S VPN 터널을 설정 합니다. 게이트웨이 인스턴스 하나에서 계획된 유지 관리 이벤트 또는 계획되지 않은 이벤트가 발생하면 트래픽이 다른 활성 IPsec 터널로 전송되도록 자동 전환됩니다. Azure Advisor는 활성-활성으로 구성되지 않은 VPN 게이트웨이를 식별하여 고가용성을 유지할 수 있도록 해당 게이트웨이를 구성하라는 제안 메시지를 표시합니다.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>프로덕션 VPN gateway를 사용 하 여 프로덕션 워크 로드 실행

Azure Advisor는 기본 SKU 인 VPN gateway를 확인 하 고 대신 프로덕션 SKU를 사용 하는 것이 좋습니다. 기본 SKU는 개발 및 테스트 목적으로 설계 되었습니다. 프로덕션 Sku는 더 높은 수의 터널, BGP 지원, 활성-활성 구성 옵션, 사용자 지정 Ipsec/IKE 정책, 높은 안정성 및 가용성을 제공 합니다.

## <a name="repair-invalid-log-alert-rules"></a>잘못 된 로그 경고 규칙 복구

Azure Advisor는 해당 조건 섹션에 잘못 된 쿼리가 지정 된 경고 규칙을 검색 합니다. 로그 경고 규칙은 Azure Monitor에서 생성되며 지정된 간격으로 분석 쿼리를 실행하는 데 사용됩니다. 쿼리 결과에 따라 경고를 트리거할 것인지 여부가 결정됩니다. 시간이 지나 참조되는 리소스, 테이블 또는 명령이 변경되면 분석 쿼리가 유효하지 않게 될 수 있습니다. Advisor는 자동 사용 하지 않도록 설정 하 고 Azure에서 리소스의 모니터링 범위를 확인 하는 것을 방지 하기 위해 경고 규칙의 쿼리를 수정 하는 것을 권장 합니다. [경고 규칙 문제 해결에 대한 자세한 정보](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Cosmos DB 컬렉션에서 일관 된 인덱싱 모드 구성

지연 인덱싱 모드로 구성 된 Azure Cosmos DB 컨테이너는 쿼리 결과의 새로 고침에 영향을 줄 수 있습니다. Advisor는 이러한 방식으로 구성 된 컨테이너를 검색 하 고 일관 된 모드로 전환 하는 것을 권장 합니다. [Cosmos DB에서 인덱싱 정책에 대해 자세히 알아보세요.](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>파티션 키를 사용하여 Azure Cosmos DB 컨테이너 구성

Azure Advisor는 프로 비전 된 저장소 할당량에 근접 하는 분할 되지 않은 컬렉션 Azure Cosmos DB를 식별 합니다. 이러한 컬렉션은 서비스에 의해 자동으로 확장 될 수 있도록 파티션 키 정의를 사용 하 여 새 컬렉션으로 마이그레이션하는 것이 좋습니다. [파티션 키를 선택 하는 방법에 대한 자세한 정보](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Azure Cosmos DB .NET SDK를 최신 버전의 Nuget으로 업그레이드

Azure Advisor는 이전 버전의 .NET SDK를 사용 하는 Azure Cosmos DB 계정을 식별 하 고 최신 수정, 성능 향상 및 새로운 기능 기능을 위해 Nuget에서 최신 버전으로 업그레이드 하는 것을 권장 합니다. [Cosmos DB .NET SDK에 대해 자세히 알아보기](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Azure Cosmos DB Java SDK를 최신 버전의 Maven으로 업그레이드

Azure Advisor는 이전 버전의 Java SDK를 사용 하는 Azure Cosmos DB 계정을 식별 하 고 최신 수정, 성능 향상 및 새로운 기능 기능을 위해 Maven에서 최신 버전으로 업그레이드 하는 것이 좋습니다. [Cosmos DB Java SDK에 대한 자세한 정보](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Azure Cosmos DB Spark Connector를 최신 버전의 Maven으로 업그레이드

Azure Advisor는 이전 버전의 Cosmos DB Spark 커넥터를 사용 하는 Azure Cosmos DB 계정을 식별 하 고 최신 수정, 성능 향상 및 새로운 기능 기능을 위해 Maven에서 최신 버전으로 업그레이드 하는 것이 좋습니다. [Cosmos DB Spark 커넥터에 대한 자세한 정보](https://aka.ms/cosmosdb/spark-connector)

## <a name="enable-virtual-machine-replication"></a>가상 머신 복제 사용
다른 지역에 복제를 사용 하도록 설정 하지 않은 가상 컴퓨터는 지역 가동 중단으로 복원 되지 않습니다. 가상 컴퓨터를 복제 하면 Azure 지역 가동 중단 시 부정적인 비즈니스 영향을 줄일 수가 줄어듭니다. Advisor는 복제를 사용 하도록 설정 되지 않은 Vm을 검색 하 고, 가동 중단이 발생할 경우 원격 Azure 지역에서 가상 컴퓨터를 신속 하 게 가져올 수 있도록 복제를 사용 하도록 설정 하는 것이 좋습니다. [가상 컴퓨터 복제에 대한 자세한 정보](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Advisor의 고가용성 권장 사항에 액세스하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

2.  Advisor 대시보드에서 **고가용성** 탭을 클릭합니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Azure Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor 작동의 뛰어난 권장 사항](advisor-operational-excellence-recommendations.md)
