---
title: Azure Cache for Redis 프리미엄 계층 소개
description: 프리미엄 계층 Azure Cache for Redis 인스턴스에 대한 Redis 지속성, Redis 클러스터링 및 VNET 지원을 만들고 관리하는 방법을 알아봅니다.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: aadcc13d2397f10ea40f06d1259c86b9a179c38b
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121661"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Azure Cache for Redis 프리미엄 계층 소개
Azure Cache for Redis는 분산되고 관리되는 캐시입니다. 이 캐시는 데이터에 매우 빠르게 액세스하여 확장성과 응답성이 뛰어난 애플리케이션을 빌드하는 데 도움을 줍니다. 

새로운 프리미엄 계층은 모든 표준 계층 기능과 추가 기능(예: 성능 향상, 더 큰 작업, 재해 복구, 가져오기/내보내기 및 강화된 보안)이 포함된 엔터프라이즈급 계층입니다. 프리미엄 캐시 계층의 추가 기능에 대해 자세히 알아보려면 계속 읽습니다.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>표준 또는 기본 계층에 비해 향상된 성능
**표준 또는 기본 계층에 대해 향상된 성능.** 프리미엄 계층의 캐시는 더 빠른 프로세서가 포함되고 기본 또는 표준 계층에 비해 더 나은 성능을 제공하는 하드웨어에 배포됩니다. 프리미엄 계층 캐시는 처리량은 더 높고 대기 시간은 더 짧습니다. 

**동일한 크기의 캐시에 대한 처리량이 표준 계층에 비해 프리미엄에서 더 높습니다.** 예를 들어 53GB P4(프리미엄) 캐시의 처리량은 초당 250K의 요청인 반면 C6(표준)은 150K입니다.

프리미엄 캐시의 크기, 처리량 및 대역폭에 대한 자세한 내용은 [Azure Cache for Redis FAQ](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)를 참조하세요.

## <a name="redis-data-persistence"></a>Redis 데이터 지속성
프리미엄 계층을 사용하면 Azure Storage 계정에서 캐시 데이터를 유지할 수 있습니다. 기본/표준 캐시에서 모든 데이터는 메모리에만 저장됩니다. 기본 인프라의 경우 문제는 잠재적인 데이터 손실이 있을 수 있다는 점입니다. 데이터 손실에 대한 복원력을 늘리기 위해 프리미엄 계층에서 Redis 데이터 지속성 기능을 사용하는 것이 좋습니다. Azure Cache for Redis는 [Redis 지속성](https://redis.io/topics/persistence)에서 RDB 및 AOF(출시 예정) 옵션을 제공합니다. 

지속성을 구성하는 방법에 대한 지침은 [프리미엄 Azure Redis Cache에 대한 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)을 참조하세요.

## <a name="redis-cluster"></a>Redis 클러스터
53GB보다 큰 캐시를 만들거나 여러 Redis 노드에서 데이터를 분할하려는 경우 프리미엄 계층에서 사용 가능한 Redis 클러스터링을 사용할 수 있습니다. 각 노드는 고가용성을 위해 Azure에 의해 관리되는 주/복제본 캐시 쌍으로 구성됩니다. 

**Redis 클러스터링은 최대 배율 및 처리량을 제공합니다.** 클러스터에서 분할된 데이터베이스(노드) 수를 늘림에 따라 처리량이 선형으로 늘어납니다. 예: 10개의 분할된 데이터베이스에 대해 P4 클러스터를 만드는 경우 가능한 처리량은 250K * 10 = 초당 250만 요청 수입니다. 프리미엄 캐시의 크기, 처리량 및 대역폭에 대한 자세한 내용은 [Azure Cache for Redis FAQ](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)를 참조하세요.

클러스터링을 시작하려면 [프리미엄 Azure Cache for Redis에 대한 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)을 참조하세요.

## <a name="enhanced-security-and-isolation"></a>강화된 보안 및 격리
기본 또는 표준 계층에서 만든 캐시는 공용 인터넷에서 액세스할 수 있습니다. 캐시에 대한 액세스는 선택키에 따라 제한됩니다. 프리미엄 계층을 사용하면 지정된 네트워크 내의 클라이언트만 캐시에 액세스할 수 있는지 추가로 확인할 수 있습니다. Azure Cache for Redis는 [Azure VNet(Virtual Network)](https://azure.microsoft.com/services/virtual-network/)에 배포할 수 있습니다. 서브넷, 액세스 제어 정책과 같은 VNet의 모든 기능 및 다른 기능을 사용하여 추가로 Redis에 대한 액세스를 제한할 수 있습니다.

자세한 내용은 [프리미엄 Azure Cache for Redis에 대한 Virtual Network 지원을 구성하는 방법](cache-how-to-premium-vnet.md)을 참조하세요.

## <a name="importexport"></a>Import/Export
가져오기/내보내기는 Azure Cache for Redis 데이터 관리 작업입니다. 즉 프리미엄 캐시에서 Azure Storage 계정의 페이지 Blob으로 Azure Cache for Redis 데이터베이스(RDB) 스냅샷을 가져오고 내보내는 방식으로 Azure Cache for Redis에서 데이터를 가져오고 내보낼 수 있습니다. 이를 통해 다양한 Azure Cache for Redis 인스턴스 간에 마이그레이션하거나 데이터를 사용하기 전에 캐시에 채울 수 있습니다.

가져오기는 Linux, Windows 또는 Amazon Web Services 및 기타 클라우드 공급자에서 실행되는 Redis를 비롯한 환경이나 클라우드에서 실행되는 Redis 서버로부터 Redis 호환 RDB 파일을 가져오는 데 사용됩니다. 데이터 가져오기는 미리 채워진 데이터로 캐시를 만드는 손쉬운 방법입니다. 가져오기 프로세스 중에는 Azure Cache for Redis에서 RDB 파일을 Azure Storage에서 메모리로 로드한 다음, 키를 캐시에 삽입합니다.

내보내기를 사용하면 Azure Cache for Redis에 저장된 데이터를 Redis 호환 RDB 파일로 내보낼 수 있습니다. 이 기능을 사용하여 데이터를 Azure Cache for Redis 인스턴스에서 다른 인스턴스 또는 다른 Redis 서버로 이동할 수 있습니다. 내보내기 프로세스 중에는 임시 파일이 Azure Cache for Redis 서버 인스턴스를 호스팅하는 VM에 만들어지고, 지정된 스토리지 계정에 업로드됩니다. 성공 또는 실패 상태로 내보내기 작업이 완료되면, 임시 파일은 삭제됩니다.

자세한 내용은 [Azure Cache for Redis에서 데이터를 가져오고 내보내는 방법](cache-how-to-import-export-data.md)을 참조하세요.

## <a name="reboot"></a>다시 부팅
이 프리미엄 계층에서는 요청이 있을 때 하나 이상의 캐시 노드를 다시 부팅할 수 있습니다. 따라서 오류 발생 시 애플리케이션의 복원력을 테스트할 수 있습니다. 다음 노드를 다시 부팅할 수 있습니다.

* 캐시의 마스터 노드
* 캐시의 보조 노드
* 캐시의 기본 및 보조 노드 모두
* 클러스터링이 포함 된 프리미엄 캐시를 사용 하는 경우 캐시의 개별 분할에 대한 기본, 보조 또는 두 노드를 모두 다시 부팅할 수 있습니다.

자세한 내용은 [다시 부팅](cache-administration.md#reboot) 및 [다시 부팅 FAQ](cache-administration.md#reboot-faq)를 참조하세요.

>[!NOTE]
>다시 부팅 기능은 이제 모든 Azure Cache for Redis 계층에서 사용할 수 있습니다.
>
>

## <a name="schedule-updates"></a>업데이트 예약
예약된 업데이트 기능을 사용하여 캐시 유지 관리 기간을 지정할 수 있습니다. 유지 관리 기간이 지정되면 이 기간 동안 Redis 서버 업데이트가 진행됩니다. 유지 관리 기간을 지정하려면 원하는 요일을 선택하고 각 요일의 유지 관리 기간 시작 시간을 선택합니다. 유지 관리 기간 시간은 UTC로 나타냅니다. 

자세한 내용은 [업데이트 예약](cache-administration.md#schedule-updates) 및 [업데이트 예약 FAQ](cache-administration.md#schedule-updates-faq)를 참조하세요.

> [!NOTE]
> 예약된 유지 관리 기간 동안에는 Redis 서버 업데이트만 수행됩니다. 유지 관리 기간이 Azure 업데이트 또는 VM 운영 체제에 대한 업데이트에는 적용되지 않습니다.
> 
> 

## <a name="geo-replication"></a>지역에서 복제

**지역 복제**는 두 개의 프리미엄 계층 Azure Cache for Redis 인스턴스를 연결하는 메커니즘을 제공합니다. 한 캐시는 주 연결된 캐시로 지정하고 다른 캐시는 보조 연결된 캐시로 지정합니다. 보조 연결된 캐시는 읽기 전용이 되고 주 캐시에 쓴 데이터는 보조 연결된 캐시에 복제됩니다. 이 기능은 Azure 지역 간에 캐시를 복제하는 데 사용할 수 있습니다.

자세한 내용은 [Azure Cache for Redis에 대한 지역 복제를 구성하는 방법](cache-how-to-geo-replication.md)을 참조하세요.


## <a name="to-scale-to-the-premium-tier"></a>프리미엄 계층으로 크기를 조정하려면
프리미엄 계층으로 크기를 조정하려면 **가격 책정 계층 변경** 블레이드에서 프리미엄 계층 중 하나를 선택하기만 하면 됩니다. 또한 PowerShell 및 CLI를 사용하여 프리미엄 계층으로 캐시를 확장할 수 있습니다. 단계별 지침은 [Azure Cache for Redis 크기를 조정하는 방법](cache-how-to-scale.md) 및 [크기 조정 작업을 자동화하는 방법](cache-how-to-scale.md#how-to-automate-a-scaling-operation)을 참조하세요.

## <a name="next-steps"></a>다음 단계
캐시를 만들고 새로운 프리미엄 계층 기능을 탐색합니다.

* [프리미엄 Azure Redis Cache에 대한 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)
* [프리미엄 Azure Cache for Redis에 대한 Virtual Network 지원을 구성하는 방법](cache-how-to-premium-vnet.md)
* [프리미엄 Azure Cache for Redis에 대한 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)
* [Azure Cache for Redis에서 데이터를 가져오고 내보내는 방법](cache-how-to-import-export-data.md)
* [Azure Cache for Redis를 관리하는 방법](cache-administration.md)

