---
title: Advisor를 사용 하 여 응용 프로그램의 안정성 향상
description: Azure Advisor를 사용 하 여 업무상 중요 한 Azure 배포에서 안정성을 보장 하 고 향상 시킵니다.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 0d96974e53f24d5a01eeee8b08eee578177a9ad2
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258492"
---
# <a name="improve-the-reliability-of-your-application-by-using-azure-advisor"></a>Azure Advisor를 사용 하 여 응용 프로그램의 안정성 향상

Azure Advisor는 업무상 중요한 애플리케이션의 연속성을 보장하고 향상시키는 데 도움이 됩니다. Advisor 대시보드의 **안정성** 탭에서 advisor의 안정성 권장 사항을 확인할 수 있습니다.

## <a name="check-the-version-of-your-check-point-network-virtual-appliance-image"></a>Check Point 네트워크 가상 어플라이언스 이미지의 버전 확인

Advisor는 플랫폼 서비스 작업 중에 네트워크 연결이 끊어진 것으로 알려진 버전의 검사점 이미지가 가상 컴퓨터에서 실행 되 고 있는지 여부를 확인할 수 있습니다. Advisor 권장 사항은이 문제를 해결 하는 최신 버전의 이미지로 업그레이드 하는 데 도움이 됩니다. 이 검사는 네트워크 연결을 개선 하 여 비즈니스 연속성을 보장 합니다.

## <a name="ensure-application-gateway-fault-tolerance"></a>애플리케이션 게이트웨이 내결함성 보장

이 권장 사항은 애플리케이션 게이트웨이에서 제공하는 중요 업무용 애플리케이션의 비즈니스 연속성을 보장합니다. Advisor는 내결함성을 위해 구성 되지 않은 application gateway 인스턴스를 식별 합니다. 그런 다음 수행할 수 있는 수정 작업을 제안 합니다. Advisor는 중간 규모 또는 대량 단일 인스턴스 응용 프로그램 게이트웨이를 식별 하 고 하나 이상의 인스턴스를 추가 하는 것을 권장 합니다. 또한 단일 인스턴스 및 다중 인스턴스 소형 응용 프로그램 게이트웨이를 식별 하 고 중간 또는 대기업으로 마이그레이션하는 것이 좋습니다. Advisor는 이러한 작업을 권장 하 여 이러한 리소스에 대 한 현재 SLA 요구 사항을 충족 하도록 응용 프로그램 게이트웨이 인스턴스를 구성 해야 합니다.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>가상 머신 데이터를 실수로 삭제되지 않도록 보호

가상 머신 백업 설정은 비즈니스에 중요한 데이터의 가용성을 보장하고 실수로 인한 삭제 또는 손상에 대한 보호를 제공합니다. Advisor는 백업이 사용 하도록 설정 되지 않은 가상 컴퓨터를 식별 하 고 백업 사용을 권장 합니다. 

## <a name="ensure-you-have-access-to-azure-experts-when-you-need-it"></a>필요할 때 Azure 전문가에 게 액세스 권한이 있는지 확인

업무상 중요 한 워크 로드를 실행 하는 경우 필요할 때 기술 지원에 액세스 하는 것이 중요 합니다. Advisor는 지원 계획에 기술 지원이 포함 되지 않은 잠재적 업무상 중요 한 구독을 식별 합니다. 기술 지원이 포함 된 옵션으로 업그레이드 하는 것이 좋습니다.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Azure 문제가 사용자에 게 영향을 주는 경우 알림을 받을 Azure Service Health 경고 만들기

Azure 서비스 문제가 사용자에 게 영향을 줄 때 알림을 받도록 Azure Service Health 경고를 설정 하는 것이 좋습니다. [Azure Service Health](https://azure.microsoft.com/features/service-health/) 는 Azure 서비스 문제의 영향을 받는 경우 개인 설정 된 지침 및 지원을 제공 하는 무료 서비스입니다. Advisor는 경고가 구성 되지 않은 구독을 식별 하 고 구성 하는 것을 권장 합니다.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>복원력을 위해 Traffic Manager 엔드포인트 구성

끝점을 둘 이상 사용 하는 Azure Traffic Manager 프로필은 지정 된 끝점이 실패 하는 경우 더 높은 가용성을 경험 합니다. 서로 다른 지역에 엔드포인트를 배치하면 서비스 복원력을 더 높일 수 있습니다. Advisor는 하나 이상의 끝점이 있는 트래픽 관리자 프로필을 식별 하 고 다른 지역에 하나 이상의 끝점을 추가할 것을 권장 합니다.

근접 라우팅으로 구성 된 Traffic Manager 프로필의 모든 끝점이 동일한 지역에 있는 경우 다른 지역의 사용자는 연결 지연이 발생할 수 있습니다. 엔드포인트를 다른 지역에 추가하거나 다른 지역으로 이동하면 한 지역의 모든 엔드포인트가 실패하는 경우 가용성을 더 높일 수 있습니다. Advisor는 모든 엔드포인트가 같은 지역에 있는 근접 라우팅에 대해 구성된 Traffic Manager 프로필을 식별합니다. 다른 Azure 지역에 엔드포인트를 추가하거나 이동하는 것이 좋습니다.

지리적 라우팅에 대 한 Traffic Manager 프로필이 구성 된 경우 트래픽이 정의 된 지역에 따라 끝점으로 라우팅됩니다. 지역이 실패 하는 경우에는 미리 정의 된 장애 조치 (failover)가 수행 되지 않습니다. 지역 그룹화를 **모두 (세계)** 로 구성 하는 끝점이 있는 경우, 삭제 된 트래픽을 방지 하 고 서비스 가용성을 향상 시킬 수 있습니다. Advisor는 지리적 라우팅에 대해 구성 된 Traffic Manager 프로필을 식별 합니다 .이 경우 지역 그룹화를 **모두 (세계)** 로 구성 하는 끝점이 없습니다. 끝점을 **모든 (세계)** 로 만들도록 구성을 변경 하는 것이 좋습니다.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>실수로 덮어쓰거나 삭제 한 후 Azure storage 계정에서 일시 삭제를 사용 하 여 데이터 저장 및 복구

삭제한 Blob가 영구적으로 삭제되지 않고 일시 삭제된 상태로 전환되도록 스토리지 계정에 대해 [일시 삭제](../storage/blobs/soft-delete-overview.md)를 사용하도록 설정합니다. 데이터를 덮어쓰는 경우 덮어쓴 데이터의 상태를 저장하기 위해 일시 삭제된 스냅샷이 생성됩니다. 일시 삭제를 사용 하면 실수로 삭제 하거나 덮어쓰는 복구를 수행할 수 있습니다. Advisor는 일시 삭제를 사용 하도록 설정 하지 않은 Azure storage 계정을 식별 하 고 사용 하도록 설정 하는 것을 제안 합니다.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>연결 복원을 위해 VPN 게이트웨이를 활성-활성으로 구성

활성-활성 구성에서 VPN 게이트웨이의 두 인스턴스는 모두 온-프레미스 VPN 장치에 S2S VPN 터널을 설정 합니다. 계획 된 유지 관리 이벤트 또는 계획 되지 않은 이벤트가 하나의 게이트웨이 인스턴스에 발생 하면 트래픽은 자동으로 다른 활성 IPsec 터널로 전환 됩니다. Azure Advisor은 활성-활성으로 구성 되지 않은 VPN 게이트웨이를 식별 하 고 고가용성을 위해 구성 하는 것을 제안 합니다.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>프로덕션 VPN gateway를 사용 하 여 프로덕션 워크 로드 실행

Azure Advisor는 기본 SKU를 사용 하는 VPN gateway를 확인 하 고 대신 프로덕션 SKU를 사용 하는 것이 좋습니다. 기본 SKU는 개발 및 테스트를 위해 설계 되었습니다. 프로덕션 Sku 제품:
- 추가 터널. 
- BGP 지원. 
- 활성-활성 구성 옵션입니다. 
- 사용자 지정 Ipsec/IKE 정책. 
- 더 높은 안정성 및 가용성.

## <a name="ensure-virtual-machine-fault-tolerance-temporarily-disabled"></a>가상 머신 내결함성 확인 (일시적으로 사용 안 함)

응용 프로그램에 중복성을 제공 하려면 가용성 집합에서 두 개 이상의 가상 머신을 그룹화 하는 것이 좋습니다. Advisor는 가용성 집합에 속하지 않는 가상 머신을 식별 하 고이를 하나로 이동 하는 것을 권장 합니다. 이 구성은 계획 된 유지 관리 또는 계획 되지 않은 유지 관리 중에 하나 이상의 가상 머신을 사용할 수 있고 Azure virtual machine SLA를 충족 하도록 보장 합니다. 가상 머신에 대한 가용성 집합을 만들거나 기존 가용성 집합에 가상 머신을 추가할 수도 있습니다.

> [!NOTE]
> 가용성 집합을 만들도록 선택 하는 경우 가상 머신을 하나 이상 추가 해야 합니다. 중단 시간 동안 하나 이상의 컴퓨터를 사용할 수 있도록 하기 위해 가용성 집합에 둘 이상의 가상 머신을 그룹화하는 것이 좋습니다.

## <a name="ensure-availability-set-fault-tolerance-temporarily-disabled"></a>가용성 집합 내결함성 보장 (일시적으로 사용 안 함)

응용 프로그램에 중복성을 제공 하려면 가용성 집합에서 두 개 이상의 가상 머신을 그룹화 하는 것이 좋습니다. Advisor는 단일 가상 머신을 포함하는 가용성 집합을 식별하고 여기에 하나 이상의 가상 머신을 추가할 것을 권장합니다.이 구성은 계획 된 유지 관리 또는 계획 되지 않은 유지 관리 중에 하나 이상의 가상 머신을 사용할 수 있고 Azure virtual machine SLA를 충족 하도록 보장 합니다.가상 머신을 만들거나 기존 가상 머신을 가용성 집합에 추가할 수 있습니다.  

## <a name="use-managed-disks-to-improve-data-reliability-temporarily-disabled"></a>관리 디스크를 사용 하 여 데이터 안정성 향상 (일시적으로 사용 안 함)

저장소 계정 또는 저장소 크기 조정 단위를 공유 하는 디스크를 사용 하는 가용성 집합에 있는 가상 컴퓨터는 작동 중단 시 단일 저장소 크기 조정 단위로 복원 되지 않습니다. Advisor는 이러한 가용성 집합을 식별 하 고 Azure managed disks로의 마이그레이션을 권장 합니다. 이 마이그레이션은 가용성 집합에 있는 가상 머신의 디스크가 단일 실패 지점을 방지 하기 위해 충분히 격리 되도록 합니다. 

## <a name="repair-invalid-log-alert-rules"></a>잘못 된 로그 경고 규칙 복구

Azure Advisor는 조건 섹션에 잘못 된 쿼리가 지정 된 경고 규칙을 검색 합니다. Azure Monitor에서 로그 경고 규칙을 만들고이 규칙을 사용 하 여 지정 된 간격으로 분석 쿼리를 실행할 수 있습니다. 쿼리의 결과는 경고를 트리거해야 하는지 여부를 결정 합니다. 분석 쿼리는 참조 된 리소스, 테이블 또는 명령의 변경 내용 때문에 시간이 지남에 따라 무효화 될 수 있습니다. Advisor는 자동으로 사용 하지 않도록 설정할 수 없도록 경고 규칙의 쿼리를 수정 하 고 Azure에서 리소스의 모니터링 적용 범위를 확인 하는 것이 좋습니다. [경고 규칙 문제 해결에 대해 자세히 알아보세요.](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-azure-cosmos-db-collection"></a>Azure Cosmos DB 컬렉션에서 일관 된 인덱싱 모드 구성

지연 인덱싱 모드를 사용 하 여 Azure Cosmos DB 컨테이너를 구성 하면 쿼리 결과의 새로 고침에 영향을 줄 수 있습니다. Advisor는 이러한 방식으로 구성 된 컨테이너를 검색 하 고 일관성 모드로 전환 하는 것을 권장 합니다. [Azure Cosmos DB에서 인덱싱 정책에 대해 자세히 알아보세요.](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>파티션 키를 사용하여 Azure Cosmos DB 컨테이너 구성

Azure Advisor는 프로 비전 된 저장소 할당량에 근접 하는 분할 되지 않은 컬렉션 Azure Cosmos DB를 식별 합니다. 이러한 컬렉션은 서비스에 의해 자동으로 확장 될 수 있도록 파티션 키 정의를 사용 하 여 새 컬렉션으로 마이그레이션하는 것이 좋습니다. [파티션 키 선택에 대해 자세히 알아보세요.](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>NuGet에서 Azure Cosmos DB .NET SDK를 최신 버전으로 업그레이드

Azure Advisor는 이전 버전의 .NET SDK를 사용 하는 Azure Cosmos DB 계정을 식별 합니다. 최신 수정, 성능 향상 및 기능 기능을 위해 NuGet에서 최신 버전으로 업그레이드 하는 것이 좋습니다. [Azure Cosmos DB .NET SDK에 대해 자세히 알아보세요.](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Azure Cosmos DB Java SDK를 최신 버전의 Maven으로 업그레이드

Azure Advisor는 이전 버전의 Java SDK를 사용 하는 Azure Cosmos DB 계정을 식별 합니다. 최신 수정, 성능 향상 및 기능 기능을 위해 Maven에서 최신 버전으로 업그레이드 하는 것이 좋습니다. [Azure Cosmos DB Java SDK에 대해 자세히 알아보세요.](https://aka.ms/cosmosdb/sql-api-sdk-async-java)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Maven에서 Azure Cosmos DB Spark 커넥터를 최신 버전으로 업그레이드

Azure Advisor는 이전 버전의 Azure Cosmos DB Spark 커넥터를 사용 하는 Azure Cosmos DB 계정을 식별 합니다. 최신 수정, 성능 향상 및 기능 기능을 위해 Maven에서 최신 버전으로 업그레이드 하는 것이 좋습니다. [Azure Cosmos DB Spark 커넥터에 대해 자세히 알아보세요.](https://aka.ms/cosmosdb/spark-connector)

## <a name="consider-moving-to-kafka-21-on-hdinsight-40"></a>HDInsight 4.0에서 Kafka 2.1로 이동 하는 것이 좋습니다.

2020 년 7 월 1 일부 터 Azure HDInsight 4.0에서 Kafka 1.1를 사용 하 여 새 Kafka 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 잠재적 시스템/지원 중단을 방지 하려면 2.1 년 6 월 30 2020 일을 사용 하 여 HDInsight 4.0에서 Kafka로 이동 하는 것이 좋습니다.

## <a name="consider-upgrading-older-spark-versions-in-hdinsight-spark-clusters"></a>HDInsight Spark 클러스터에서 이전 Spark 버전을 업그레이드 하는 것이 좋습니다.

2020 년 7 월 1 일부 터 HDInsight 3.6에서 Spark 2.1 또는 2.2을 사용 하 여 새 Spark 클러스터를 만들 수 없습니다. HDInsight 4.0에서 Spark 2.3를 사용 하 여 새 Spark 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 

## <a name="enable-virtual-machine-replication"></a>가상 머신 복제 사용
다른 지역에 복제를 사용 하도록 설정 하지 않은 가상 머신은 지역 가동 중단으로 복원 되지 않습니다. 가상 컴퓨터를 복제 하면 Azure 지역 가동 중단 시 부정적인 비즈니스 영향이 줄어듭니다. Advisor는 복제를 사용 하도록 설정 하지 않은 Vm을 검색 하 고 사용 하도록 권장 합니다. 복제를 사용 하도록 설정 하는 경우 가동 중단이 발생 하면 원격 Azure 지역에서 가상 머신을 신속 하 게 가져올 수 있습니다. [가상 컴퓨터 복제에 대해 자세히 알아보세요.](../site-recovery/azure-to-azure-quickstart.md)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Advisor의 고가용성 권장 사항에 액세스 하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

2.  Advisor 대시보드에서 **고가용성** 탭을 선택 합니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor 작동의 뛰어난 권장 사항](advisor-operational-excellence-recommendations.md)
