---
title: Azure Cache for Redis FAQ | Microsoft Docs
description: Azure Cache for Redis에 대한 일반적인 질문과 대답, 패턴 및 모범 사례를 알아봅니다.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: c2c52b7d-b2d1-433a-b635-c20180e5cab2
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: yegu
ms.openlocfilehash: 65e8553969aa92848b1c4496724a7b7754b5d659
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60552063"
---
# <a name="azure-cache-for-redis-faq"></a>Azure Cache for Redis FAQ
Azure Cache for Redis에 대한 일반적인 질문과 대답, 패턴 및 모범 사례를 알아봅니다.

## <a name="what-if-my-question-isnt-answered-here"></a>여기서 내 질문에 대답하지 않으면 어떻게 하나요?
질문하려는 내용이 아래 목록에 나와 있지 않으면 알려 주세요. 대답을 확인하는 방법을 알려 드리겠습니다.

* 이 FAQ의 끝에 있는 의견란에 질문을 게시하면 Azure 캐시 팀 및 다른 커뮤니티 구성원과 이 문서에 대해 의견을 교환할 수 있습니다.
* [Azure 캐시 MSDN 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) 에 질문을 게시하면 Azure 캐시 팀 및 커뮤니티의 다른 구성원이 참여할 수 있습니다.
* 기능을 요청하려면 [Azure Cache for Redis 사용자 의견](https://feedback.azure.com/forums/169382-cache)에 요청 및 아이디어를 제출하면 됩니다.
* [Azure 캐시 외부 피드백](mailto:azurecache@microsoft.com)에서 전자 메일을 보낼 수도 있습니다.

## <a name="azure-cache-for-redis-basics"></a>Azure Cache for Redis 기본 사항
이 섹션의 FAQ에서는 Azure Cache for Redis의 기본 사항 중 일부에 대해 설명합니다.

* [Azure Cache for Redis란?](#what-is-azure-cache-for-redis)
* [Azure Cache for Redis를 시작하려면 어떻게 해야 하나요?](#how-can-i-get-started-with-azure-cache-for-redis)

다음 FAQ에서는 Azure Cache for Redis에 대한 기본 개념과 질문을 설명하고 다른 FAQ 섹션 중 하나에서 답변을 제공합니다.

* [사용해야 하는 Azure Cache for Redis 제안 및 크기는 어떻게 되나요?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [사용할 수 있는 Azure Cache for Redis 클라이언트는 어떻게 되나요?](#what-azure-cache-for-redis-clients-can-i-use)
* [Azure Cache for Redis에 대한 로컬 에뮬레이터가 있나요?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [내 캐시의 상태 및 성능을 모니터링하려면 어떻게 해야 하나요?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>계획 FAQ
* [사용해야 하는 Azure Cache for Redis 제안 및 크기는 어떻게 되나요?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Azure Cache for Redis 성능](#azure-cache-for-redis-performance)
* [어떤 영역에 내 캐시를 배치해야 하나요?](#in-what-region-should-i-locate-my-cache)
* [Azure Cache for Redis에 대한 요금은 어떻게 청구되나요?](#how-am-i-billed-for-azure-cache-for-redis)
* [Azure Government 클라우드, Azure 중국 클라우드 또는 Microsoft Azure 독일에서 Azure Cache for Redis를 사용할 수 있나요?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>개발 FAQ
* [StackExchange.Redis 구성 옵션은 어떤 기능을 수행하나요?](#what-do-the-stackexchangeredis-configuration-options-do)
* [사용할 수 있는 Azure Cache for Redis 클라이언트는 어떻게 되나요?](#what-azure-cache-for-redis-clients-can-i-use)
* [Azure Cache for Redis에 대한 로컬 에뮬레이터가 있나요?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [어떻게 Redis 명령을 실행할 수 있나요?](#how-can-i-run-redis-commands)
* [다른 일부 Azure 서비스와 달리 Azure Cache for Redis에는 MSDN 클래스 라이브러리 참조가 왜 없나요?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Azure Cache for Redis는 PHP 세션 캐시로 사용할 수 있나요?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Redis 데이터베이스란?](#what-are-redis-databases)

## <a name="security-faqs"></a>보안 FAQ
* [언제 비 SSL 포트를 사용하여 Redis에 연결할 수 있도록 해야 하나요?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>프로덕션 FAQ
* [프로덕션 모범 사례에는 어떤 것이 있나요?](#what-are-some-production-best-practices)
* [일반적인 Redis 명령을 사용할 때 고려해야 하는 몇 가지 사항은 무엇인가요?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [ThreadPool 증가에 대한 중요한 세부 정보](#important-details-about-threadpool-growth)
* [StackExchange.Redis를 사용하는 경우 클라이언트에서 더 많은 처리량을 가져오는 서버 GC를 사용하도록 설정](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [성능 고려 사항 및 연결](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>모니터링 및 문제 해결 FAQ
이 섹션의 FAQ는 일반적인 모니터링 및 문제 해결 질문을 다룹니다. Azure Cache for Redis 인스턴스를 모니터링하고 관련 문제를 해결하는 방법에 대한 자세한 내용은 [Azure Cache for Redis를 모니터링하는 방법](cache-how-to-monitor.md) 및 [Azure Cache for Redis 문제를 해결하는 방법](cache-how-to-troubleshoot.md)을 참조하세요.

* [내 캐시의 상태 및 성능을 모니터링하려면 어떻게 해야 하나요?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [왜 시간 초과가 표시되나요?](#why-am-i-seeing-timeouts)
* [내 클라이언트가 캐시에서 연결이 끊어진 것은 무엇 때문인가요?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>이전 캐시 제공 FAQ
* [내게 적합한 Azure 캐시 기능](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>Azure Cache for Redis란?
Azure Cache for Redis는 널리 사용되는 오픈 소스 소프트웨어 [Redis](https://redis.io/)를 기반으로 합니다. Microsoft에서 관리하여 Azure 내의 모든 애플리케이션에서 액세스할 수 있는 안전한 전용 Azure Cache for Redis에 대한 액세스를 제공합니다. 자세한 개요는 Azure.com의 [Azure Cache for Redis](https://azure.microsoft.com/services/cache/) 제품 페이지를 참조하세요.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Azure Cache for Redis를 시작하려면 어떻게 해야 하나요?
Azure Cache for Redis는 몇 가지 방법으로 시작할 수 있습니다.

* [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md) 및 [Python](cache-python-get-started.md)에 대해 사용할 수 있는 자습서 중 하나를 확인해볼 수 있습니다.
* [Microsoft Azure Cache for Redis를 사용하여 고성능 앱을 빌드하는 방법](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/)을 참조할 수 있습니다.
* Redis 사용 방법을 확인하려면 사용 중인 프로젝트의 개발 언어와 일치하는 클라이언트용 클라이언트 설명서를 확인해 보세요. Azure Cache for Redis에서 사용할 수 있는 많은 Redis 클라이언트가 있습니다. Redis 클라이언트 목록은 [https://redis.io/clients](https://redis.io/clients)에서 참조할 수 있습니다.

Azure 계정이 없는 경우 다음을 수행할 수 있습니다.

* [Azure 계정을 무료로 개설할 수 있습니다](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). 유료 Azure 서비스를 사용해볼 수 있는 크레딧을 받게 됩니다. 크레딧을 모두 사용한 후에도 계정을 유지하고 무료 Azure 서비스 및 기능을 사용할 수 있습니다.
* [Visual Studio 구독자 혜택 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero) MSDN 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>사용해야 하는 Azure Cache for Redis 제안 및 크기는 어떻게 되나요?
각 Azure Cache for Redis 제안은 서로 다른 수준의 **크기**, **대역폭**, **고가용성** 및 **SLA** 옵션을 제공합니다.

캐시 제품을 선택할 때는 다음을 고려해야 합니다.

* **메모리**: 기본 및 표준 계층에서는 250MB-53GB를 제공합니다. 프리미엄 계층은 최대 530GB를 제공합니다. 자세한 내용은 [Azure Cache for Redis 가격](https://azure.microsoft.com/pricing/details/cache/)을 참조하세요.
* **네트워크 성능**: 높은 처리량이 필요한 워크로드가 있는 경우 프리미엄 계층에서 표준 또는 기본 계층보다 더 높은 대역폭을 제공합니다. 또한 각 계층 안에서는 캐시를 호스팅하는 기본 VM으로 인해 캐시 크기가 클수록 대역폭이 큽니다. 자세한 내용은 [다음 테이블](#cache-performance)을 참조하세요.
* **처리량**: 프리미엄 계층에서 사용 가능한 최대 처리량을 제공합니다. 캐시 서버 또는 클라이언트가 대역폭 제한에 도달하면 클라이언트 측에서 시간 초과를 수신할 수 있습니다. 자세한 내용은 다음 표를 참조하세요.
* **고가용성/SLA**: Azure Cache for Redis는 표준/프리미엄 캐시를 99.9% 이상의 시간 동안 사용할 수 있도록 보장합니다. SLA에 대한 자세한 내용은 [Azure Cache for Redis 가격](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)을 참조하세요. SLA에서는 캐시 엔드포인트에 대한 연결만 다룹니다. SLA는 데이터 손실로부터의 보호는 다루지 않습니다. 데이터 손실에 대한 복원력을 늘리기 위해 프리미엄 계층에서 Redis 데이터 지속성 기능을 사용하는 것이 좋습니다.
* **Redis 데이터 지속성**: 프리미엄 계층을 사용하면 Azure Storage 계정에서 캐시 데이터를 유지할 수 있습니다. 기본/표준 캐시에서 모든 데이터는 메모리에만 저장됩니다. 기본 인프라 문제가 있는 경우 잠재적인 데이터 손실이 있을 수 있습니다. 데이터 손실에 대한 복원력을 늘리기 위해 프리미엄 계층에서 Redis 데이터 지속성 기능을 사용하는 것이 좋습니다. Azure Cache for Redis는 Redis 지속성에서 RDB 및 AOF(출시 예정) 옵션을 제공합니다. 자세한 내용은 [프리미엄 Azure Redis Cache에 대한 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)을 참조하세요.
* **Redis 클러스터**: 53GB보다 큰 캐시를 만들거나 여러 Redis 노드에 걸쳐 데이터를 분할하려면 프리미엄 계층에서 사용할 수 있는 Redis 클러스터링을 사용할 수 있습니다. 각 노드는 고가용성을 위해 주/복제본 캐시 쌍으로 구성됩니다. 자세한 내용은 [프리미엄 Azure Cache for Redis에 대한 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)을 참조하세요.
* **향상된 보안 및 네트워크 격리**: Azure VNET(Virtual Network) 배포는 액세스를 추가로 제한하는 서브넷, 액세스 제어 정책 및 기타 기능을 포함하여 Azure Cache for Redis에 대한 향상된 보안 및 격리를 제공합니다. 자세한 내용은 [프리미엄 Azure Cache for Redis에 대한 Virtual Network 지원을 구성하는 방법](cache-how-to-premium-vnet.md)을 참조하세요.
* **Redis 구성**: 표준과 프리미엄 계층 모두에서 Redis for Keyspace 알림을 구성할 수 있습니다.
* **최대 클라이언트 연결 수**: 프리미엄 계층에서는 Redis에 연결할 수 있는 최대 클라이언트 수를 제공하여 더 큰 크기의 캐시에 대해 더 많은 수의 연결을 제공할 수 있습니다. 클러스터링은 클러스터형 캐시에 사용할 수 있는 연결 수를 늘리지 않습니다. 자세한 내용은 [Azure Cache for Redis 가격](https://azure.microsoft.com/pricing/details/cache/)을 참조하세요.
* **Redis 서버 전용 코어**: 프리미엄 계층의 모든 캐시 크기에는 Redis 전용 코어가 있습니다. 기본/표준 계층에서는 C1 크기 이상에 Redis 서버 전용 코어가 있습니다.
* **Redis는 단일 스레드** 이므로 3개 이상의 코어를 사용해도 2개 코어만 사용하는 경우에 비해 추가적인 이점이 없지만 VM 크기가 더 크면 일반적으로 작은 크기보다 대역폭이 더 큽니다. 캐시 서버 또는 클라이언트가 대역폭 제한에 도달하면 클라이언트 쪽에 시간 초과가 수신됩니다.
* **향상된 성능**: 프로세서가 더 빠른 하드웨어에 프리미엄 계층의 캐시가 배포되어 기본 또는 표준 계층에 비해 더 나은 성능을 제공합니다. 프리미엄 계층 캐시는 처리량은 더 높고 대기 시간은 더 짧습니다.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Azure Cache for Redis 성능
다음 표에는 Azure Cache for Redis 엔드포인트에 대해 Iaas VM에서 `redis-benchmark.exe`를 사용하여 다양한 크기의 표준 및 프리미엄 캐시를 테스트하는 동안 관찰되는 최대 대역폭 값이 나와 있습니다. SSL 처리량의 경우 redis-benchmark를 stunnel과 함께 사용하여 Azure Cache for Redis 엔드포인트에 연결합니다.

>[!NOTE] 
>이러한 값은 보장되지 않으며 해당 수치에 대한 SLA는 없지만 일반적인 수치입니다. 애플리케이션에 적합한 캐시 크기를 확인하려면 사용자 고유의 애플리케이션을 부하 테스트해야 합니다.
>이러한 수치는 새 결과가 주기적으로 게시됨에 따라 변경될 수 있습니다.
>

이 테이블에서 다음과 같은 결론을 내릴 수 있습니다.

* 동일한 크기의 캐시 처리량은 표준 계층과 비교할 때 프리미엄 계층에서 더 높습니다. 예를 들어 6GB 캐시를 사용할 경우 C3의 처리량은 100,000인 데 비해 P1의 처리량은 180,000 RPS입니다.
* Redis 클러스터를 사용하여 클러스터에서 분할된 데이터베이스(노드) 수를 늘림에 따라 처리량이 선형으로 늘어납니다. 예를 들어 10개의 분할된 데이터베이스에 P4 클러스터를 만드는 경우 가능한 처리량은 400,000 * 10 = 4백만 RPS입니다.
* 큰 크기의 키에 대한 처리량이 표준 계층에 비해 프리미엄 계층에서 더 높습니다.

| 가격 책정 계층  | 크기 | CPU 코어 | 사용 가능한 대역폭 | 1KB 값 크기 | 1KB 값 크기 |
| --- | --- | --- | --- | --- | --- |
| **표준 캐시 크기** | | |**Mb/s(초당 메가비트) / MB/s(초당 메가바이트)** |**RPS(초당 요청 수) 비 SSL** |**RPS(초당 요청 수) SSL** |
| C0 |250MB |공유됨 |100/12.5 |15,000 |7,500 |
| C1 |1 GB |1 |500/62.5 |38,000 |20,720 |
| C2 |2.5GB |2 |500/62.5 |41,000 |37,000 |
| C3 |6GB |4 |1000/125 |100,000 |90,000 |
| C4 |13GB |2 |500/62.5 |60,000 |55,000 |
| C5 |26GB |4 |1,000 / 125 |102,000 |93,000 |
| C6 |53GB |8 |2,000 / 250 |126,000 |120,000 |
| **프리미엄 캐시 크기** | |**분할 영역당 CPU 코어** | **Mb/s(초당 메가비트) / MB/s(초당 메가바이트)** |**RPS(초당 요청 수) 비 SSL, 분할당** |**RPS(초당 요청 수) SSL, 분할당** |
| P1 |6GB |2 |1,500 / 187.5 |180,000 |172,000 |
| P2 |13GB |4 |3,000 / 375 |350,000 |341,000 |
| P3 |26GB |4 |3,000 / 375 |350,000 |341,000 |
| P4 |53GB |8 |6,000 / 750 |400,000 |373,000 |

stunnel 설정 또는 `redis-benchmark.exe`와 같은 Redis 도구 다운로드에 대한 지침은 [어떻게 Redis 명령을 실행할 수 있나요?](#cache-commands) 섹션을 참조하세요.

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>어떤 영역에 내 캐시를 배치해야 하나요?
성능을 최적화하고 대기 시간을 최소화하려면 Azure Cache for Redis를 캐시 클라이언트 애플리케이션과 동일한 지역에 배치합니다.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Azure Cache for Redis에 대한 요금은 어떻게 청구되나요?
Azure Cache for Redis 가격은 [여기](https://azure.microsoft.com/pricing/details/cache/)서 책정합니다. 가격 책정 페이지에는 시간 단위로 가격이 나와 있습니다. 캐시는 캐시가 만들어지는 시간부터 삭제되는 시간까지 분 단위로 요금이 청구됩니다. 캐시 요금 청구를 중지 또는 일시 중지하는 옵션은 없습니다.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Azure Government 클라우드, Azure 중국 클라우드 또는 Microsoft Azure 독일에서 Azure Cache for Redis를 사용할 수 있나요?
예, Azure Cache for Redis는 Azure Government 클라우드, Azure 중국 클라우드 및 Microsoft Azure 독일에서 사용할 수 있습니다. Azure 공용 클라우드와 비교할 때 이러한 클라우드에서 Azure Cache for Redis에 액세스하고 관리하기 위한 URL은 다릅니다. 

| 클라우드   | Redis에 대한 Dns 접미사            |
|---------|---------------------------------|
| 공용  | *.redis.cache.windows.net       |
| 미국 정부  | *.redis.cache.usgovcloudapi.net |
| 독일 | *.redis.cache.cloudapi.de       |
| 중국   | *.redis.cache.chinacloudapi.cn  |

다른 클라우드에서 Azure Cache for Redis를 사용할 때 고려해야 할 사항에 대한 자세한 내용은 다음 링크를 참조하세요.

- [Azure Government 데이터베이스 - Azure Cache for Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure 중국 클라우드 - Azure Cache for Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)

Azure Government 클라우드, Azure 중국 클라우드 및 Microsoft Azure 독일에서 PowerShell을 통해 Azure Cache for Redis를 사용하는 방법에 대한 자세한 내용은 [다른 클라우드에 연결하는 방법 - Azure Cache for Redis PowerShell](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds)을 참조하세요.

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>StackExchange.Redis 구성 옵션은 어떤 기능을 수행하나요?
StackExchange.Redis에는 많은 옵션이 있습니다. 이 섹션에서는 몇 가지 일반적인 설정에 대해 설명합니다. StackExchange.Redis 옵션에 대한 자세한 내용은 [StackExchange.Redis 구성](https://stackexchange.github.io/StackExchange.Redis/Configuration)을 참조하세요.

| ConfigurationOptions | 설명 | 권장 사항 |
| --- | --- | --- |
| AbortOnConnectFail |true로 설정하면 네트워크 오류가 발생한 후 연결이 다시 연결되지 않습니다. |false로 설정하여 StackExchange.Redis가 자동으로 다시 연결하도록 합니다. |
| ConnectRetry |초기 연결 중에 연결 시도를 반복할 횟수입니다. |지침은 다음 사항을 참조하세요. |
| ConnectTimeout |연결 작업의 시간 제한(ms)입니다. |지침은 다음 사항을 참조하세요. |

보통은 클라이언트의 기본값으로 충분합니다. 워크로드에 따라 옵션을 미세 조정할 수 있습니다.

* **재시도**
  * ConnectRetry 및 ConnectTimeout에 대한 일반적인 지침은 페일 패스트 및 다시 시도입니다. 이 지침은 클라이언트가 Redis 명령을 실행하고 응답을 수신하는 데 걸리는 평균 시간 및 워크로드에 따라 달라집니다.
  * 연결 상태를 확인하고 직접 다시 연결하는 대신 StackExchange.Redis가 자동으로 다시 연결하도록 합니다. **ConnectionMultiplexer.IsConnected 속성을 사용하지 마세요**.
  * 때로는 사용자가 다시 시도하면 사태가 더욱 심각해지고 결코 복구되지 않는 문제가 발생할 수도 있습니다. 이 경우 Microsoft Patterns & Practices 그룹이 게시한 [다시 시도 일반 지침](../best-practices-retry-general.md)의 설명에 따라 지수 백오프 다시 시도 알고리즘 사용을 고려해야 합니다.
* **시간 제한 값**
  * 작업을 고려하여 적절하게 값을 설정합니다. 큰 값을 저장하는 경우 시간 제한을 더 큰 값으로 설정합니다.
  * `AbortOnConnectFail`을 false로 설정하여 StackExchange.Redis가 다시 연결하도록 합니다.
  * 애플리케이션에 단일 ConnectionMultiplexer 인스턴스를 사용합니다. [ConnectionMultiplexer 클래스를 사용하여 캐시에 연결](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)에 표시된 대로, LazyConnection을 사용하여 Connection 속성에 의해 반환되는 단일 인스턴스를 만들 수 있습니다.
  * 진단 목적으로 `ConnectionMultiplexer.ClientName` 속성을 앱 인스턴스 고유 이름으로 설정합니다.
  * 사용자 지정 작업에 여러 개의 `ConnectionMultiplexer` 인스턴스를 사용합니다.
      * 애플리케이션에 다양한 부하가 있는 경우 이 모델을 따를 수 있습니다. 예를 들면 다음과 같습니다.
      * 큰 키를 처리하기 위한 멀티플렉서 1개가 있습니다.
      * 작은 키를 처리하기 위한 멀티플렉서 1개가 있습니다.
      * 사용하는 각 ConnectionMultiplexer의 연결 시간 제한 및 다시 시도 논리에 대해 다른 값을 설정할 수 있습니다.
      * 진단에 도움이 되도록 각 멀티플렉서의 `ClientName` 속성을 설정합니다.
      * 이 지침을 따르면 `ConnectionMultiplexer`당 대기 시간이 감소할 수 있습니다.

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>사용할 수 있는 Azure Cache for Redis 클라이언트는 어떻게 되나요?
Redis의 장점 중 하나는 여러 가지 개발 언어를 지원하는 많은 클라이언트가 있다는 것입니다. 클라이언트의 현재 목록에 대해서는 [Redis 클라이언트](https://redis.io/clients)를 참조하세요. 여러 다른 언어 및 클라이언트를 포함하는 자습서에 대해서는 [Azure Cache for Redis 사용 방법](cache-dotnet-how-to-use-azure-redis-cache.md)과 목차에 포함된 관련 문서를 참조하세요.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Azure Cache for Redis에 대한 로컬 에뮬레이터가 있나요?
Azure Cache for Redis에 대한 로컬 에뮬레이터는 없지만 다음 예제와 같이 로컬 머신의 [Redis 명령줄 도구](https://github.com/MSOpenTech/redis/releases/)에서 redis-server.exe의 MSOpenTech 버전을 실행하고 이에 연결하여 로컬 캐시 에뮬레이터와 비슷한 환경을 얻을 수 있습니다.

    private static Lazy<ConnectionMultiplexer>
          lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


원하는 경우 선택적으로 온라인 Azure Cache for Redis의 [기본 캐시 설정](cache-configure.md#default-redis-server-configuration)과 더 가깝게 일치하도록 [redis.conf](https://redis.io/topics/config) 파일을 구성할 수 있습니다.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>어떻게 Redis 명령을 실행할 수 있나요?
[Azure Cache for Redis에서 지원되지 않는 Redis 명령](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis)에 나열된 명령을 제외하고는 [Redis 명령](https://redis.io/commands#)에 나열된 명령 중 하나를 사용할 수 있습니다. Redis 명령을 실행하는 여러 가지 옵션이 있습니다.

* 표준 또는 프리미엄 캐시를 사용하는 경우 [Redis 콘솔](cache-configure.md#redis-console)을 사용하여 Redis 명령을 실행할 수 있습니다. Redis 콘솔은 Azure Portal에서 Redis 명령을 안전하게 실행하는 방법을 제공합니다.
* Redis 명령줄 도구를 사용할 수도 있습니다. 이 도구를 사용하려면 다음 단계를 수행합니다.
* [Redis 명령줄 도구](https://github.com/MSOpenTech/redis/releases/)를 다운로드합니다.
* `redis-cli.exe`를 사용하여 캐시에 연결합니다. 다음 예제와 같이 -h 스위치를 사용하여 캐시 엔드포인트를 전달하고 -a를 사용하여 키를 전달합니다.
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Redis 명령줄 도구는 SSL 포트에서 작동하지 않지만, [Redis용 ASP.NET 세션 상태 제공자 미리 보기 릴리스 발표](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) 블로그 게시물의 지침에 따라 `stunnel`과 같은 유틸리티를 사용하여 도구를 SSL 포트에 안전하게 연결할 수 있습니다.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>다른 일부 Azure 서비스와 달리 Azure Cache for Redis에는 MSDN 클래스 라이브러리 참조가 왜 없나요?
Microsoft Azure Cache for Redis는 인기 있는 오픈 소스 Azure Cache for Redis를 기반으로 하며, 다양한 [Redis 클라이언트](https://redis.io/clients)에서 다양한 프로그래밍 언어로 액세스할 수 있습니다. 각 클라이언트에는 [Redis 명령](https://redis.io/commands)을 사용하여 Azure Cache for Redis 인스턴스를 호출하는 자체 API가 있습니다.

클라이언트마다 다르기 때문에 MSDN에 하나의 중앙 집중식 클래스 참조는 없고, 각 클라이언트가 자체 참조 설명서를 유지 관리합니다. 참조 설명서 외에도 다양한 언어와 캐시 클라이언트를 사용하여 Azure Cache for Redis를 시작하는 방법을 보여 주는 몇 가지 자습서가 있습니다. 이 자습서에 액세스하려면 [Azure Cache for Redis 사용 방법](cache-dotnet-how-to-use-azure-redis-cache.md)과 목차에 포함된 관련 문서를 참조하세요.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Azure Cache for Redis는 PHP 세션 캐시로 사용할 수 있나요?
예, Azure Cache for Redis를 PHP 세션 캐시로 사용하려면 `session.save_path`에서 Azure Cache for Redis 인스턴스에 대한 연결 문자열을 지정합니다.

> [!IMPORTANT]
> Azure Cache for Redis를 PHP 세션 캐시로 사용하는 경우 다음 예제와 같이 캐시에 연결하는 데 사용되는 보안 키를 URL로 인코딩해야 합니다.
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> 키가 URL로 인코드되지 않으면 `Failed to parse session.save_path`와 비슷한 메시지와 함께 예외가 발생할 수 있습니다.
>
>

PhpRedis 클라이언트에서 Azure Cache for Redis를 PHP 세션 캐시로 사용하는 방법에 대한 자세한 내용은 [PHP 세션 처리기](https://github.com/phpredis/phpredis#php-session-handler)를 참조하세요.

### <a name="what-are-redis-databases"></a>Redis 데이터베이스란?

Redis 데이터베이스는 동일한 Redis 인스턴스 내에 있는 데이터를 논리적으로 분리한 단위입니다. 캐시 메모리는 모든 데이터베이스와 해당 데이터베이스에 저장된 키/값에 따라 지정된 데이터베이스의 실제 메모리 소비 간에 공유됩니다. 예를 들어 C6 캐시에는 53GB의 메모리가 있습니다. 하나의 데이터베이스에 53GB를 모두 사용하거나 여러 데이터베이스 간에 분할할 수 있습니다. 

> [!NOTE]
> 클러스터링을 사용하도록 설정된 프리미엄 Azure Cache for Redis를 사용하는 경우 데이터베이스 0만 사용할 수 있습니다. 이 제한은 본질적인 Redis 제한이므로 Azure Cache for Redis에만 한정되지는 않습니다. 자세한 내용은 [클러스터링을 사용하려면 클라이언트 애플리케이션을 변경해야 합니까?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>언제 비 SSL 포트를 사용하여 Redis에 연결할 수 있도록 해야 하나요?
Redis 서버는 기본적으로 SSL을 지원하지 않지만 Azure Cache for Redis는 이를 지원합니다. Azure Cache for Redis에 연결하고 클라이언트에서 StackExchange.Redis와 같은 SSL을 지원하는 경우 SSL을 사용해야 합니다.

>[!NOTE]
>SSL이 아닌 포트는 기본적으로 새 Azure Cache for Redis 인스턴스에 사용되지 않습니다. 클라이언트에서 SSL을 지원하지 않는 경우 [Azure Cache for Redis에서 캐시 구성](cache-configure.md) 문서에 있는 [액세스 포트](cache-configure.md#access-ports) 섹션의 지침에 따라 SSL이 아닌 포트를 사용하도록 설정해야 합니다.
>
>

`redis-cli`와 같은 Redis 도구는 SSL 포트에서 작동하지 않지만, [Redis용 ASP.NET 세션 상태 제공자 미리 보기 릴리스 발표](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) 블로그 게시물의 지침에 따라 `stunnel`과 같은 유틸리티를 사용하면 SSL 포트에 도구를 안전하게 연결할 수 있습니다.

Redis 도구 다운로드에 대한 지침은 [어떻게 Redis 명령을 실행할 수 있나요?](#cache-commands) 섹션을 참조하세요.

### <a name="what-are-some-production-best-practices"></a>프로덕션 모범 사례에는 어떤 것이 있나요?
* [StackExchange.Redis 모범 사례](#stackexchangeredis-best-practices)
* [구성 및 개념](#configuration-and-concepts)
* [성능 테스트](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange.Redis 모범 사례
* `AbortConnect` 를 false로 설정하고 ConnectionMultiplexer가 자동으로 다시 연결되도록 합니다. [자세한 내용을 보려면 여기를 참조하세요](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* ConnectionMultiplexer를 다시 사용합니다. 요청마다 새로 만들지 마세요. [여기에 표시된](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) `Lazy<ConnectionMultiplexer>` 패턴을 사용하는 것이 좋습니다.
* Redis는 더 작은 값에서 가장 잘 작동하므로 더 큰 데이터를 여러 개의 키로 분할하는 것을 고려합니다. [이 Redis 토론](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)에서 100KB는 큰 것으로 간주합니다. 큰 값을 사용할 때 야기될 수 있는 문제 예에 대해서는 [이 문서](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) 를 읽어보세요.
* 시간 초과를 방지하도록 [ThreadPool 설정](#important-details-about-threadpool-growth) 을 구성합니다.
* 기본 connectTimeout인 5초 이상을 사용합니다. 이렇게 하면 네트워크 문제가 발생할 경우 StackExchange.Redis에서 연결을 다시 설정할 충분한 시간을 얻게 됩니다.
* 실행 중인 다른 작업을 관련된 성능 비용을 고려해야 합니다. 예를 들어 `KEYS` 명령은 O(n) 작업이므로 피해야 합니다. [redis.io 사이트](https://redis.io/commands/) 에는 지원되는 각 작업에 대한 시간 복잡도와 관련된 세부 정보가 제공됩니다. 각 작업에 대한 복잡성을 확인하려면 각 명령을 클릭합니다.

#### <a name="configuration-and-concepts"></a>구성 및 개념
* 프로덕션 시스템에 대해 표준 또는 프리미엄 계층을 사용합니다. 기본 계층은 데이터 복제 및 SLA가 없는 단일 노드 시스템입니다. 또한 C1 이상의 캐시를 사용합니다. C0 캐시는 일반적으로 간단한 개발/테스트 시나리오에 사용됩니다.
* Redis는 **메모리 내** 데이터 저장소입니다. 데이터 손실이 발생할 수 있는 시나리오에 대해서는 [이 문서](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) 를 읽어보세요.
* [패치 및 장애 조치(failover)로 인한](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)연결 문제를 처리할 수 있도록 시스템을 개발하세요.

#### <a name="performance-testing"></a>성능 테스트
* 자체 성능 테스트를 작성하기 전에 먼저 `redis-benchmark.exe`를 사용하여 가능한 처리량을 확인하세요. `redis-benchmark`에서는 SSL이 지원되지 않으므로 테스트를 실행하기 전에 [Azure Portal을 통해 비 SSL 포트를 사용하도록 설정](cache-configure.md#access-ports)해야 합니다. 예를 들어 [내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* 테스트에 사용되는 클라이언트 VM은 Azure Cache for Redis 인스턴스와 동일한 지역에 있어야 합니다.
* Dv2 VM 시리즈는 하드웨어 성능이 더 우수하고 최상의 결과를 제공하므로 클라이언트에 이 시리즈를 사용하는 것이 좋습니다.
* 선택하는 클라이언트 VM에는 적어도 테스트 중인 캐시만큼의 계산 및 대역폭 성능이 필요합니다.
* Windows에서 작업하는 경우 클라이언트 컴퓨터에서 VRSS를 사용하도록 설정합니다. [자세한 내용을 보려면 여기를 참조하세요](https://technet.microsoft.com/library/dn383582.aspx).
* 프리미엄 계층 Redis 인스턴스는 CPU와 네트워크 모두 더 나은 하드웨어에서 실행되므로 더 우수한 네트워크 대기 시간 및 처리량을 제공합니다.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>일반적인 Redis 명령을 사용할 때 고려해야 하는 몇 가지 사항은 무엇인가요?
* 완료하는 데 시간이 오래 걸리는 특정 Redis 명령은 명령의 영향을 알고 있는 경우에만 실행해야 합니다.
  * 예를 들어 [KEYS](https://redis.io/commands/keys) 명령은 키 수에 따라 반환되는 데 시간이 오래 걸릴 수 있으므로 프로덕션에서 실행하지 마세요. Redis는 단일 스레드 서버이며 한 번에 하나씩 명령을 처리합니다. KEYS 후에 실행된 다른 명령이 있는 경우 Redis가 KEYS 명령을 처리할 때까지 처리되지 않습니다. [redis.io 사이트](https://redis.io/commands/) 에는 지원되는 각 작업에 대한 시간 복잡도와 관련된 세부 정보가 제공됩니다. 각 작업에 대한 복잡성을 확인하려면 각 명령을 클릭합니다.
* 키 크기 - 작은 키/값을 사용해야 하나요, 아니면 큰 키/값을 사용해야 하나요? 일반적으로 시나리오에 따라 다릅니다. 시나리오에서 큰 키가 필요한 경우 ConnectionTimeout 및 다시 시도 값을 조정하고 다시 시도 논리를 조정할 수 있습니다. Redis 서버 관점에서는 값이 작을수록 더 나은 성능이 관찰됩니다.
* 이러한 고려 사항이 Redis에서 큰 값을 저장할 수 없다는 의미는 아닙니다. 다음과 같은 고려 사항에 주의해야 합니다. 대기 시간이 더 길어집니다. 큰 데이터 집합 1개와 작은 데이터 집합 1개가 있는 경우, 이전의 [StackExchange.Redis 구성 옵션은 어떤 기능을 수행하나요?](#cache-configuration) 섹션에 설명된 대로 각각 다른 시간 제한 및 재시도 값으로 구성된 여러 개의 ConnectionMultiplexer 인스턴스를 사용할 수 있습니다.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?
* [캐시 진단을 사용](cache-how-to-monitor.md#enable-cache-diagnostics)하도록 설정하면 캐시의 상태를 [모니터링](cache-how-to-monitor.md)할 수 있습니다. Azure 포털에서 메트릭을 볼 수 있으며 선택한 도구를 사용하여 메트릭을 [다운로드 및 검토](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) 할 수도 있습니다.
* redis-benchmark.exe를 사용하여 Redis 서버를 부하 테스트할 수 있습니다.
* 부하 테스트 클라이언트와 Azure Cache for Redis가 동일한 지역에 있는지 확인합니다.
* redis-cli.exe를 사용하고 INFO 명령을 통해 캐시를 모니터링합니다.
* 부하로 인해 높은 메모리 조각화가 발생하는 경우 더 큰 캐시 크기로 확장해야 합니다.
* Redis 도구 다운로드에 대한 지침은 [어떻게 Redis 명령을 실행할 수 있나요?](#cache-commands) 섹션을 참조하세요.

다음 명령은 redis-benchmark.exe를 사용하는 예를 보여줍니다. 정확한 결과를 위해 캐시와 같은 지역에 있는 VM에서 이 명령을 실행합니다.

* 1K 페이로드를 사용하여 파이프라인 SET 요청 테스트

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* 1K 페이로드를 사용하여 파이프라인 GET 요청 테스트
  참고:  먼저 위에 표시된 SET 테스트를 실행하여 캐시를 채웁니다.

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>ThreadPool 증가에 대한 중요한 세부 정보
CLR ThreadPool에는 두 가지 유형의 스레드 - "작업자" 및 "I/O 완료 포트"(즉, IOCP) 스레드가 있습니다.

* 작업자 스레드는 `Task.Run(…)` 또는 `ThreadPool.QueueUserWorkItem(…)` 메서드를 처리하는 등의 작업에 사용됩니다. 이러한 스레드는 작업을 백그라운드 스레드에서 수행해야 할 경우에 CLR에서 다양한 구성 요소에서 사용됩니다.
* IOCP 스레드는 비동기 IO가 발생하는 경우(예: 네트워크에서 읽기) 사용됩니다.

스레드 풀은 스레드의 각 형식에 대한 "최소" 설정에 도달할 때까지 새 작업자 스레드 또는 주문형 I/O 완료 스레드(제한 없이)를 제공합니다. 기본적으로 최소 스레드 수는 시스템에서 프로세서의 수로 설정됩니다.

기존(사용 중) 스레드의 수가 스레드의 "최소" 수에 도달하면 ThreadPool은 500밀리초당 하나의 스레드에 새 스레드를 삽입하는 비율을 제한합니다. 일반적으로 시스템에서는 IOCP 스레드가 필요한 작업이 폭주하면 해당 작업을 매우 신속하게 처리합니다. 그러나 작업 폭주가 구성된 "최소" 설정보다 큰 경우 ThreadPool은 두 가지 중 하나가 발생하기를 기다리므로 일부 작업 처리에 약간의 지연이 있을 수 있습니다.

1. 기존 스레드는 작업을 처리할 수 있는 상태가 됩니다.
2. 500ms에 대해 기존 스레드를 사용할 수 없으므로 새 스레드가 만들어집니다.

기본적으로 사용 중인 스레드의 수가 최소 스레드보다 큰 경우 네트워크 트래픽이 애플리케이션에서 처리되기 전에 500ms 지연을 지불할 가능성이 있음을 의미합니다. 또한 기존 스레드가 15초보다 오랫동안 유휴 상태로 유지되는 경우(기억에 기반) 정리되고 증가 및 감소의 주기를 반복할 수 있다는 것에 유의해야 합니다.

StackExchange.Redis(빌드 1.0.450 이상)의 예제 오류 메시지를 살펴보는 경우 ThreadPool 통계를 인쇄하는 것을 볼 수 있습니다(아래 IOCP 및 작업자 세부 정보 참조).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

이전 예에서 IOCP 스레드의 경우 사용 중인 6개의 스레드가 있고 시스템이 최소 4개의 스레드를 허용하도록 구성되어 있는 것을 확인할 수 있습니다. 이 경우 클라이언트는 6 > 4로 인해 두 500ms 지연을 볼 수 있습니다.

IOCP 또는 작업자 스레드의 증가에 제한이 있는 경우 StackExchange.Redis는 시간 제한에 도달할 수 있습니다.

### <a name="recommendation"></a>권장 사항
주어진 이 정보로 고객은 IOCP 및 작업자 스레드에 대해 기본값보다 큰 값으로 최소 구성 값을 설정하는 것이 좋습니다. 하나의 애플리케이션에 대한 올바른 값은 다른 애플리케이션에 대해 너무 높거나/낮을 수 있으므로 이 값에 대해 모든 지침에 맞는 하나의 크기를 제공할 수 없습니다. 이 설정은 복잡한 애플리케이션의 다른 부분의 성능에 영향을 미칠 수 있으므로 각 고객은 특정 요구에 맞게 이 설정을 미세 조정해야 합니다. 좋은 시작 지점은 200 또는 300이며 필요에 따라 테스트 및 조정합니다.

이 설정을 구성하는 방법

* ASP.NET에서 web.config의 `<processModel>` 구성 요소에 있는 ["minIoThreads" 또는 "minWorkerThreads" 구성 설정]["minIoThreads" configuration setting]을 사용합니다. Azure 웹 사이트 내에서 실행하는 경우 이 설정은 구성 옵션을 통해 노출되지 않습니다. 그러나 여전히 global.asax.cs의 Application_Start 메서드에서 이 설정을 프로그래밍 방식으로 구성할 수 있습니다(아래 참조).

  > [!NOTE] 
  > 이 구성 요소에 지정된 값은 *코어당* 설정입니다. 예를 들어 4코어 컴퓨터가 있고 minIOThreads 설정을 런타임 시 200으로 지정하려는 경우 `<processModel minIoThreads="50"/>`를 사용합니다.
  >

* ASP.NET 외부 및 Azure WebSites global.asax에서는 [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) API를 사용합니다.

  > [!NOTE]
  > 이 API로 지정된 값은 전역 설정으로, 전체 AppDomain에 영향을 미칩니다. 4개의 코어 컴퓨터가 있으며 런타임 중에 minWorkerThreads 및 minIOThreads를 CPU당 50으로 설정하려는 경우 ThreadPool.SetMinThreads (200, 200)을 사용하면 됩니다.

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>StackExchange.Redis를 사용하는 경우 클라이언트에서 더 많은 처리량을 가져오는 서버 GC를 사용하도록 설정
서버 GC를 사용하면 클라이언트를 최적화하고 StackExchange.Redis를 사용하는 경우 더 나은 성능 및 처리량을 제공할 수 있습니다. 서버 GC 및 사용하도록 설정하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [서버 GC를 사용하도록 설정하려면](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [가비지 수집 기본 사항](/dotnet/standard/garbage-collection/fundamentals)
* [가비지 수집 및 성능](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>연결에 대한 성능 고려 사항

가격 책정 계층마다 클라이언트 연결, 메모리 및 대역폭에 대한 제한이 다릅니다. 각 캐시 크기는 특정 횟수의 연결*까지* 허용하지만 Redis에 대한 각 연결에는 오버헤드가 연결되어 있습니다. 이러한 오버헤드의 예로 TLS/SSL 암호화의 결과인 CPU 및 메모리 사용량이 있습니다. 특정 캐시 크기에 대한 최대 연결 제한은 부하가 적은 캐시를 가정합니다. 연결 오버헤드의 부하 *그리고* 클라이언트 작업의 부하가 시스템의 용량을 초과하면 현재 캐시 크기에 대한 연결 제한을 초과하지 않은 경우에도 캐시에 용량 문제가 발생할 수 있습니다.

각 계층의 다양한 연결 제한에 대한 자세한 내용은 [Azure Cache for Redis 가격](https://azure.microsoft.com/pricing/details/cache/)을 참조하세요. 연결 및 기타 기본 구성에 대한 정보는 [기본 Redis 서버 구성](cache-configure.md#default-redis-server-configuration)을 참조하세요.

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>내 캐시의 상태 및 성능을 모니터링하려면 어떻게 해야 하나요?
Microsoft Azure Cache for Redis 인스턴스는 [Azure Portal](https://portal.azure.com)에서 모니터링할 수 있습니다. 메트릭을 보고, 메트릭 차트를 시작 보드에 고정하고, 모니터링 차트의 날짜 및 시간 범위를 사용자 지정하고, 차트에서 메트릭을 추가 및 제거하고, 특정 조건이 충족될 경우의 경고를 설정할 수 있습니다. 자세한 내용은 [Azure Cache for Redis 모니터링](cache-how-to-monitor.md)을 참조하세요.

또한 Azure Cache for Redis **리소스 메뉴**에는 캐시를 모니터링하고 문제를 해결할 수 있는 몇 가지 도구가 포함되어 있습니다.

* **문제 진단 및 해결**에서는 일반적인 문제 및 문제 해결 전략에 대한 정보를 제공합니다.
* **리소스 상태** 기능은 리소스를 감시하고 예상대로 실행되는지를 알려줍니다. Azure 리소스 상태 관리 서비스에 대한 자세한 내용은 [Azure 리소스 상태 개요](../resource-health/resource-health-overview.md)를 참조하세요.
* **새 지원 요청** 을 클릭하여 캐시에 대한 지원 요청을 엽니다.

이러한 도구를 사용하면 Azure Cache for Redis 인스턴스의 상태를 모니터링하고 캐싱 애플리케이션을 관리할 수 있습니다. 자세한 내용은 [Azure Cache for Redis를 구성하는 방법](cache-configure.md)의 "지원 및 문제 해결 설정" 섹션을 참조하세요.

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>왜 시간 초과가 표시되나요?
시간 초과는 Redis와 통신하는 데 사용하는 클라이언트에서 발생합니다. Redis 서버에 명령이 전송될 때 명령은 큐에 배치되며 Redis 서버가 결국 명령을 선택하여 실행합니다. 그러나 이 프로세스 중에 클라이언트가 시간 초과될 수 있으며, 이 경우 호출 쪽에서 예외가 발생합니다. 시간 초과 문제를 해결하는 방법에 대한 자세한 내용은 [클라이언트 쪽 문제 해결](cache-how-to-troubleshoot.md#client-side-troubleshooting) 및 [StackExchange.Redis 시간 초과 예외](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions)를 참조하세요.

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>내 클라이언트가 캐시에서 연결이 끊어진 것은 무엇 때문인가요?
캐시 연결 끊김의 몇 가지 일반적인 이유는 다음과 같습니다.

* 클라이언트 쪽 원인
  * 클라이언트 애플리케이션이 다시 배포되었습니다.
  * 클라이언트 애플리케이션이 크기 조정 작업을 수행했습니다.
    * Cloud Services나 Web Apps의 경우 자동 크기 조정 때문일 수 있습니다.
  * 클라이언트 쪽의 네트워킹 계층이 변경되었습니다.
  * 클라이언트 또는 클라이언트와 서버 간의 네트워크 노드에서 일시적인 오류가 발생했습니다.
  * 대역폭 임계값 제한에 도달했습니다.
  * CPU 바인딩된 작업을 완료하는 데 시간이 너무 오래 걸렸습니다.
* 서버 쪽 원인
  * 표준 캐시 제안의 Azure Cache for Redis 서비스가 주 노드에서 보조 노드로 장애 조치를 시작했습니다.
  * Azure에서 캐시가 배포된 인스턴스에 패치를 적용하고 있었습니다.
    * 이 작업은 Redis 서버 업데이트 또는 일반적인 VM 유지 관리를 위한 것일 수 있습니다.

### <a name="which-azure-cache-offering-is-right-for-me"></a>내게 적합한 Azure 캐시 기능은?
> [!IMPORTANT]
> 작년 [공지](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)에 따라 Azure Managed Cache Service 및 Azure In-Role Cache Service가 2016년 11월 30일에 **중지되었습니다**. [Azure Cache for Redis](https://azure.microsoft.com/services/cache/)를 사용하는 것이 좋습니다. 마이그레이션에 대한 자세한 내용은 [Managed Cache Service에서 Azure Cache for Redis로 마이그레이션](cache-migrate-to-redis.md)을 참조하세요.
>
>

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure Cache for Redis는 최대 53GB의 크기로 일반 공급되며, 가용성 SLA는 99.9%입니다. 새 [프리미엄 계층](cache-premium-tier-intro.md)은 99.9%의 SLA와 함께 최대 530GB 크기를 제공하며 클러스터링, VNET, 지속성 등을 지원합니다.

Azure Cache for Redis는 Microsoft에서 관리하는 안전한 전용 Azure Cache for Redis를 사용할 수 있는 기능을 고객에게 제공합니다. Redis에서 제공하는 다양한 기능 집합 및 에코시스템을 활용하면서 Microsoft의 안정적인 호스팅 및 모니터링 기능을 이용할 수 있습니다.

Redis는 키 값 쌍만 다루는 기존 캐시와 달리 높은 성능의 데이터 형식 때문에 널리 사용됩니다. 또한 Redis는 이러한 형식에서의 원자성 작업 실행도 지원합니다(예: 문자열에 추가, 해시에서 값 증분, 목록에 푸시, 교집합, 합집합 및 차집합 계산 또는 정렬된 집합에서 가장 높은 순위의 구성원 가져오기). 기타 기능으로는 트랜잭션, 게시/알림, Lua 스크립팅, TTL(Time-to-Live)이 제한된 키 및 보다 일반적인 캐시 역할을 하도록 Redis를 설정하는 구성 설정이 있습니다.

Redis 성공의 또 다른 중요한 측면은 이를 기반으로 구축된 정상적이고 활발한 오픈 소스 에코시스템입니다. 이는 여러 언어로 사용할 수 있는 다양한 Redis 클라이언트의 집합에 반영됩니다. 이 에코시스템과 다양한 클라이언트는 Azure 내에 구축할 거의 모든 워크로드에서 Azure Cache for Redis를 사용할 수 있도록 허용합니다.

Azure Cache for Redis를 시작하는 방법에 대한 자세한 내용은 [Azure Cache for Redis를 사용하는 방법](cache-dotnet-how-to-use-azure-redis-cache.md) 및 [Azure Cache for Redis 설명서](index.md)를 참조하세요.

### <a name="managed-cache-service"></a>관리된 캐시 서비스
[Managed Cache Service는 2016년 11월 30일에 사용이 중지되었습니다.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)(영문)

보관된 설명서를 보려면 [보관된 Managed Cache Service 설명서](/previous-versions/azure/azure-services/dn386094(v=azure.100))를 참조하세요.

### <a name="in-role-cache"></a>In-Role Cache
[In-Role Cache는 2016년 11월 30일에 사용이 중지되었습니다.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)(영문)

보관된 설명서를 보려면 [보관된 In-Role Cache 설명서](/previous-versions/azure/azure-services/dn386103(v=azure.100))를 참조하세요.

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
