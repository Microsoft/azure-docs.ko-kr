---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 08/09/2019
ms.author: jroth
ms.openlocfilehash: 17d6947efcbaf8a01aeca70bf7a88dcc7b65cd0a
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548222"
---
| 리소스 | 체험판 | 공유 | Basic | Standard | 프리미엄(v2) | 격리 </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Azure App Service 요금제](../articles/app-service/overview-hosting-plans.md)<sup></sup> 당 [웹, 모바일 또는 API 앱](https://azure.microsoft.com/services/app-service/) |10 |100 |무제한<sup>2</sup> |무제한<sup>2</sup> |무제한<sup>2</sup> |무제한<sup>2</sup>|
| [App Service 계획](../articles/app-service/overview-hosting-plans.md) |지역당 10개 |리소스 그룹 당 10 |리소스 그룹당 100 |리소스 그룹당 100 |리소스 그룹당 100 |리소스 그룹당 100|
| 컴퓨팅 인스턴스 유형 |공유 |공유 |전용<sup>3</sup> |전용<sup>3</sup> |전용<sup>3</sup></p> |전용<sup>3</sup>|
| [규모 확장](../articles/app-service/manage-scale-up.md) (최대 인스턴스) |1개 공유됨 |1개 공유됨 |3개 전용됨<sup>3</sup> |10개 전용됨<sup>3</sup> |30 전용<sup>3</sup>|100개 전용됨<sup> 4</sup>|
| 스토리지<sup>5</sup> |1GB<sup>5</sup> |1GB<sup>5</sup> |10GB<sup>5</sup> |50GB<sup>5</sup> |250GB<sup>5</sup></p> |1TB<sup>5</sup>|
| CPU 시간 (5 분)<sup>6</sup> |3분 |3분 |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a>|
| CPU 시간(일)<sup>6</sup> |60분 |240분 |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |
| 메모리(1시간) |App Service 요금제 당 1024 MB |앱 당 1024 MB |N/A |N/A |N/A |N/A |
| Bandwidth |165 MB |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |
| 애플리케이션 아키텍처 |32비트 |32비트 |32비트/64비트 |32비트/64비트 |32비트/64비트 |32비트/64비트 |
| 인스턴스당 웹 소켓<sup>7</sup> |5 |35 |350 |제한 없음 |제한 없음 |제한 없음 |
| IP 연결 | 600 | 600 | 인스턴스 크기<sup>8</sup> 에 따라 다름 | 인스턴스 크기<sup>8</sup> 에 따라 다름 | 인스턴스 크기<sup>8</sup> 에 따라 다름 | 16,000 |
| 애플리케이션당 동시 [디버거 연결](../articles/app-service/troubleshoot-dotnet-visual-studio.md) |1 |1 |1 |5 |5 |5 |
| 구독 당 App Service 인증서<sup>9</sup>| 지원하지 않음 | 지원하지 않음 |10 |10 |10 |10 |
| 앱당 사용자 지정 도메인</a> |0(azurewebsites.net 하위 도메인 전용)|500 |500 |500 |500 |500 |
| 사용자 지정 도메인 [SSL 지원](../articles/app-service/configure-ssl-certificate.md) |지원 되지 않음 *. azurewebsites.net에 대한 와일드 카드 인증서를 기본적으로 사용할 수 있습니다.|지원 되지 않음 *. azurewebsites.net에 대한 와일드 카드 인증서를 기본적으로 사용할 수 있습니다.|무제한 SNI SSL 연결 |무제한 SNI SSL 및 1개의 IP SSL 연결 포함 |무제한 SNI SSL 및 1개의 IP SSL 연결 포함 | 무제한 SNI SSL 및 1개의 IP SSL 연결 포함|
| 요금제 별 하이브리드 연결 | | | 5 | 25 | 200 | 200 |
| 통합 부하 분산 장치 | |X |X |X |X |X<sup>10</sup> |
| [Always On](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [예약 된 백업](../articles/app-service/manage-backup.md) | | | | 2 시간 마다 예약 된 백업, 매일 최대 12 개 백업 (수동 + 예약) | 1 시간 마다 예약 된 백업, 매일 최대 50 백업 (수동 + 예약) | 1 시간 마다 예약 된 백업, 매일 최대 50 백업 (수동 + 예약) |
| [Autoscale](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) 지원 | |X |X |X |X |X |
| [엔드포인트 모니터링](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [스테이징 슬롯](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1</sup>별도로 명시되지 않는 한 App Service 계획당 앱 및 스토리지 할당량입니다.  
<sup>2</sup>이러한 컴퓨터에서 호스트할 수 있는 실제 앱 수는 앱의 활동, 컴퓨터 인스턴스 크기 및 해당 리소스 사용률에 따라 달라집니다.  
<sup>3</sup>전용 인스턴스의 크기는 다양할 수 있습니다. 자세한 내용은 [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/)을 참조하세요.  
<sup>4</sup> 요청 시 추가가 허용 됩니다.  
<sup>5</sup> 저장소 제한은 동일한 App service 계획의 모든 앱에서 전체 콘텐츠 크기입니다. 단일 리소스 그룹 및 지역에 있는 모든 App service 계획의 모든 앱에 대한 전체 콘텐츠 크기는 500GB를 초과할 수 없습니다.  
<sup>6</sup>이러한 리소스는 전용 인스턴스의 실제 리소스(인스턴스 크기 및 인스턴스 수)에 의해 제한됩니다.  
<sup>7</sup>두 인스턴스의 Basic 계층으로 앱을 조정하면 두 인스턴스 각각에 대해 동시에 350개가 연결됩니다. 표준 계층 이상에서는 웹 소켓에 이론적 제한이 없지만 다른 요소는 웹 소켓 수를 제한할 수 있습니다. 예를 들어 허용 되는 최대 동시 요청 (`maxConcurrentRequestsPerCpu`에 의해 정의 됨)은 작은 VM 당 7500, 중간 VM 당 15000 (7500 x 2 코어) 및 큼 VM 당 75000 (18750 x 4 코어)입니다.  
<sup>8</sup> 최대 IP 연결은 인스턴스당, 인스턴스 크기: B1/S1/P1V2 instance 당 1920, B2/S2/P2V2 8064 인스턴스당 3968/S3/P3V2가 인스턴스당 인스턴스 크기에 따라 달라 집니다.  
<sup>9</sup> 지원 요청을 통해 구독 당 App Service Certificate 할당량 한도를 최대 제한인 200로 늘릴 수 있습니다.  
<sup>10</sup> App Service 격리 Sku는 Azure Load Balancer를 통해 내부적으로 ILB (부하 분산) 될 수 있으므로 인터넷에서 공용 연결이 없습니다. 그 결과, ILB 격리 App Service의 일부 기능은 ILB 네트워크 엔드포인트과 직접 액세스되는 컴퓨터에서 사용해야 합니다.  
<sup>11</sup> 요청 시, 일정에 따라 또는 App Service 인스턴스 내에서 지속적으로 백그라운드 작업으로 사용자 지정 실행 파일 및/또는 스크립트를 실행 합니다. 연속적인 WebJobs 실행을 위해서는 무중단이 필요합니다. 예약된 WebJobs에는 Azure Scheduler 무료 또는 표준이 필요합니다. App Service 인스턴스에서 실행할 수 있는 WebJobs 수에 대한 제한은 미리 정의 되어 있지 않습니다. 응용 프로그램 코드에서 수행 하려는 작업에 따라 달라 지는 실용적인 제한이 있습니다.  
