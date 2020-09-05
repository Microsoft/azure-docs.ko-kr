---
title: Azure Cache for Redis란?
description: 캐시 배제, 콘텐츠 캐싱, 사용자 세션 캐싱, 작업 및 메시지 큐, 분산 트랜잭션을 활성화하는 Azure Cache for Redis에 대해 알아봅니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 3751560125ea8ac6cc00ed63521bff30b751e688
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009599"
---
# <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure Cache for Redis는 오픈 소스 소프트웨어 [Redis](https://redis.io/)를 기반으로 하는 메모리 내 데이터 저장소를 제공합니다. Redis는 백 엔드 데이터 저장소에서 많이 사용하는 애플리케이션의 성능과 확장성을 개선합니다. 자주 액세스하는 데이터를 빠르게 쓰고 읽을 수 있는 서버 메모리에 보관하여 대량의 애플리케이션 요청을 처리할 수 있습니다. Redis는 최신 애플리케이션에 매우 짧은 대기 시간 및 높은 처리량의 데이터 스토리지 솔루션을 제공합니다.

Azure Cache for Redis는 Redis를 관리되는 서비스로 제공합니다. 안전한 전용 Redis 서버 인스턴스와 전체 Redis API 호환성을 제공합니다. 이 서비스는 Microsoft에서 운영되고, Azure에서 호스팅되며, Azure 내부 또는 외부의 모든 애플리케이션에서 액세스할 수 있습니다.

Azure Cache for Redis는 분산 데이터 또는 콘텐츠 캐시, 세션 저장소, 메시지 브로커 등으로 사용할 수 있습니다. 독립 실행형으로 배포하거나 Azure SQL 또는 Cosmos DB와 같은 다른 Azure 데이터베이스 서비스와 함께 배포할 수 있습니다.

## <a name="key-scenarios"></a>주요 시나리오
Azure Cache for Redis는 일반적인 애플리케이션 아키텍처 패턴을 지원하여 애플리케이션 성능을 향상시킵니다. 가장 일반적인 패턴의 일부는 다음과 같습니다.

| 패턴      | 설명                                        |
| ------------ | -------------------------------------------------- |
| [데이터 캐시](cache-web-app-cache-aside-leaderboard.md) | 데이터베이스는 너무 커서 캐시로 직접 로드할 수 없습니다. [캐시 배제](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) 패턴을 사용하여 필요할 때만 캐시에 데이터를 로드하는 것이 일반적입니다. 시스템에서 데이터를 변경하는 경우 시스템은 바로 그때 다른 클라이언트로 배포되는 캐시를 업데이트할 수도 있습니다. 또한 시스템에서 데이터에 대한 만료 날짜를 설정하거나 제거 정책을 사용하여 데이터 업데이트를 캐시로 트리거할 수 있습니다.|
| [콘텐츠 캐시](cache-aspnet-output-cache-provider.md) | 헤더, 바닥글, 배너와 같은 정적 콘텐츠를 사용하는 템플릿에서 많은 웹 페이지가 생성됩니다. 이러한 정적 항목은 자주 변경되지 않습니다. 메모리 내 캐시를 사용하면 백 엔드 데이터 저장소와 비교할 때 정적 콘텐츠에 빠르게 액세스할 수 있습니다. 이 패턴은 처리 시간 및 서버 부하를 줄여 웹 서버 응답성을 높일 수 있습니다. 이를 통해 로드를 처리하는 데 필요한 서버 수를 줄일 수 있습니다. Azure Cache for Redis는 ASP.NET을 사용하여 이 패턴을 지원할 수 있도록 Redis 출력 캐시 공급자를 제공합니다.|
| [세션 저장소](cache-aspnet-session-state-provider.md) | 이 패턴은 일반적으로 웹 애플리케이션에서 사용자 쿠키와 연결할 수 있는 쇼핑 카트 및 다른 사용자 기록 데이터와 함께 사용됩니다. 쿠키에 너무 많이 저장하면 쿠키 크기가 증가하고 모든 요청에서 전달 및 유효성 검사가 수행되므로 성능이 저하될 수 있습니다. 일반적인 해결책은 쿠키를 키로 사용하여 데이터베이스에 데이터를 쿼리하는 것입니다. Azure Cache for Redis와 같은 메모리 내 캐시를 사용하면 전체 관계형 데이터베이스와의 상호 작용보다 훨씬 더 빠르게 사용자와 정보를 연결할 수 있습니다. |
| 작업 및 메시지 큐 | 애플리케이션은 요청과 연결된 작업을 실행하는 데 시간이 걸릴 때 종종 작업을 추가합니다. 오래 실행되는 작업은 종종 다른 서버에 의해 순서대로 처리되도록 큐에 대기됩니다.  이러한 작업 지연 메서드를 태스크 큐라고 합니다. Azure Cache for Redis는 애플리케이션에서 이 패턴을 사용하도록 설정하는 분산 큐를 제공합니다.|
| 분산 트랜잭션 | 애플리케이션에는 단일 원자성 작업으로 실행하기 위해 백 엔드 데이터 저장소에 대한 일련의 명령이 필요할 수 있습니다. 모든 명령은 성공하거나 모두 초기 상태로 롤백되어야 합니다. Azure Cache for Redis는 명령의 일괄 처리 실행을 단일 [트랜잭션](https://redis.io/topics/transactions)으로 지원합니다. |

## <a name="service-tiers"></a>서비스 계층
Azure Cache for Redis는 다음 계층에서 사용할 수 있습니다.

| 계층 | Description |
|---|---|
Basic | 단일 노드 캐시. 이 계층은 여러 메모리 크기(250MB - 53GB)를 지원하며 개발/테스트 및 중요하지 않은 작업에 적합합니다. 기본 계층에는 SLA(서비스 수준 계약)가 없습니다. |
| Standard | 고가용성 [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)를 사용하여 Azure에서 관리하는 2노드 기본/복제 구성의 복제된 캐시입니다. |
| Premium | Premium 계층은 엔터프라이즈급 계층입니다. Premium 계층 캐시는 더 많은 기능을 지원하며 더 낮은 대기 시간으로 더 많은 처리가 가능합니다. Premium 계층의 캐시는 Basic 또는 Standard 계층에 비해 더 나은 성능을 제공하는 더 강력한 하드웨어에 배포됩니다. 이러한 이점은 Standard 계층에 비해 Premium에서 동일한 크기의 캐시에 대한 처리량이 많음을 의미합니다. |

### <a name="feature-comparison"></a>기능 비교
[Azure Cache for Redis 가격 책정](https://azure.microsoft.com/pricing/details/cache/)에서는 각 계층의 자세한 비교를 제공합니다. 다음 표는 계층별로 지원하는 기능 몇 가지를 설명하는 데 도움이 됩니다.

| 기능 설명 | Premium | Standard | Basic |
| ------------------- | :-----: | :------: | :---: |
| [Service Level Agreement(서비스 수준 약정)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis 데이터 지속성](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis 클러스터](cache-how-to-premium-clustering.md) |✔|-|-|
| [방화벽 규칙을 통한 보안](cache-configure.md#firewall) |✔|✔|✔|
| 전송 중 암호화 |✔|✔|✔|
| [VNet으로 강화된 보안 및 격리](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/Export](cache-how-to-import-export-data.md) |✔|-|-|
| [예약된 업데이트](cache-administration.md#schedule-updates) |✔|✔|✔|
| [지역에서 복제](cache-how-to-geo-replication.md) |✔|-|-|
| [Reboot](cache-administration.md#reboot) |✔|✔|✔|

### <a name="choosing-the-right-tier"></a>올바른 계층 선택
Azure Cache for Redis 계층을 선택하는 경우 다음 사항을 고려해야 합니다.

* **메모리**: 기본 및 표준 계층에서는 250MB-53GB를 제공합니다. 프리미엄 계층은 최대 1.2TB(클러스터) 또는 120GB(비클러스터형)를 제공합니다. 자세한 내용은 [Azure Cache for Redis 가격](https://azure.microsoft.com/pricing/details/cache/)을 참조하세요.
* **네트워크 성능**: 높은 처리량이 필요한 워크로드가 있는 경우 프리미엄 계층에서 표준 또는 기본 계층보다 더 높은 대역폭을 제공합니다. 또한 각 계층 안에서는 캐시를 호스팅하는 기본 VM으로 인해 캐시 크기가 클수록 대역폭이 큽니다. 자세한 내용은 [Azure Cache for Redis 성능](cache-planning-faq.md#azure-cache-for-redis-performance)을 참조하세요.
* **처리량**: 프리미엄 계층에서 사용 가능한 최대 처리량을 제공합니다. 캐시 서버 또는 클라이언트가 대역폭 제한에 도달하면 클라이언트 측에서 시간 초과를 수신할 수 있습니다. 자세한 내용은 다음 표를 참조하세요.
* **고가용성**: Azure Cache for Redis는 [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)에 따라 표준/프리미엄 캐시를 사용할 수 있도록 보장합니다. SLA에서는 캐시 엔드포인트에 대한 연결만 다룹니다. SLA는 데이터 손실로부터의 보호는 다루지 않습니다. 데이터 손실에 대한 복원력을 늘리기 위해 프리미엄 계층에서 Redis 데이터 지속성 기능을 사용하는 것이 좋습니다.
* **Redis 데이터 지속성**: 프리미엄 계층을 사용하면 Azure Storage 계정에서 캐시 데이터를 유지할 수 있습니다. 기본/표준 캐시에서 모든 데이터는 메모리에만 저장됩니다. 기본 인프라 문제로 인해 데이터 손실이 발생할 수 있습니다. 데이터 손실에 대한 복원력을 늘리기 위해 프리미엄 계층에서 Redis 데이터 지속성 기능을 사용하는 것이 좋습니다. Azure Cache for Redis는 Redis 지속성에서 RDB 및 AOF(미리 보기) 옵션을 제공합니다. 자세한 내용은 [프리미엄 Azure Redis Cache에 대한 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)을 참조하세요.
* **Redis 클러스터**: 120GB보다 큰 캐시를 만들거나 여러 Redis 노드에 걸쳐 데이터를 분할하려면 프리미엄 계층에서 사용할 수 있는 Redis 클러스터링을 사용할 수 있습니다. 각 노드는 고가용성을 위해 주/복제본 캐시 쌍으로 구성됩니다. 자세한 내용은 [프리미엄 Azure Cache for Redis에 대한 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)을 참조하세요.
* **향상된 보안 및 네트워크 격리**: Azure VNET(Virtual Network) 배포는 액세스를 추가로 제한하는 서브넷, 액세스 제어 정책 및 기타 기능을 포함하여 Azure Cache for Redis에 대한 향상된 보안 및 격리를 제공합니다. 자세한 내용은 [프리미엄 Azure Cache for Redis에 대한 Virtual Network 지원을 구성하는 방법](cache-how-to-premium-vnet.md)을 참조하세요.
* **Redis 구성**: 표준과 프리미엄 계층 모두에서 Redis for Keyspace 알림을 구성할 수 있습니다.
* **최대 클라이언트 연결 수**: 프리미엄 계층에서는 Redis에 연결할 수 있는 최대 클라이언트 수를 제공하여 더 큰 크기의 캐시에 대해 더 많은 수의 연결을 제공할 수 있습니다. 클러스터링은 클러스터형 캐시에 사용할 수 있는 연결 수를 늘리지 않습니다. 자세한 내용은 [Azure Cache for Redis 가격](https://azure.microsoft.com/pricing/details/cache/)을 참조하세요.
* **Redis 서버 전용 코어**: 프리미엄 계층의 모든 캐시 크기에는 Redis 전용 코어가 있습니다. 기본/표준 계층에서는 C1 크기 이상에 Redis 서버 전용 코어가 있습니다.
* **단일 스레드 처리**: Redis는 의도적으로 명령 처리에 하나의 스레드만 사용합니다. Azure Cache for Redis는 I/O 처리를 위해 추가 코어도 활용합니다. 코어 수가 많을수록 선형 크기 조정이 생성되지 않더라도 처리량 성능이 향상됩니다. 또한 일반적으로 VM 크기가 클수록 작은 크기보다 대역폭 제한이 높습니다. 이를 통해 애플리케이션에서 시간 초과를 유발하는 네트워크 포화를 방지할 수 있습니다.
* **향상된 성능**: 프로세서가 더 빠른 하드웨어에 프리미엄 계층의 캐시가 배포되어 기본 또는 표준 계층에 비해 더 나은 성능을 제공합니다. 프리미엄 계층 캐시는 처리량은 더 높고 대기 시간은 더 짧습니다. 자세한 내용은 [Azure Cache for Redis 성능](cache-planning-faq.md#azure-cache-for-redis-performance)을 참조하세요.

만든 후에 더 높은 계층으로 사용자 캐시를 확장할 수 있습니다. 더 낮은 계층으로의 축소는 지원되지 않습니다. 단계별 크기 조정 지침에 대해서는 [Azure Cache for Redis 크기를 조정하는 방법](cache-how-to-scale.md) 및 [크기 조정 작업을 자동화하는 방법](cache-how-to-scale.md#how-to-automate-a-scaling-operation)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [ASP.NET 웹앱 빠른 시작](cache-web-app-howto.md) Azure Cache for Redis를 사용하는 간단한 ASP.NET 웹앱을 만듭니다.
* [.NET 빠른 시작](cache-dotnet-how-to-use-azure-redis-cache.md) Azure Cache for Redis를 사용하는 .NET 앱을 만듭니다.
* [.NET Core 빠른 시작](cache-dotnet-core-quickstart.md) Azure Cache for Redis를 사용하는 .NET Core 앱을 만듭니다.
* [Node.js 빠른 시작](cache-nodejs-get-started.md) Azure Cache for Redis를 사용하는 간단한 Node.js 앱을 만듭니다.
* [Java 빠른 시작](cache-java-get-started.md) Azure Cache for Redis를 사용하는 간단한 Java 앱을 만듭니다.
* [Python 빠른 시작](cache-python-get-started.md) Azure Cache for Redis를 사용하는 Python 앱을 만듭니다.
