---
title: Media Services 및 VOD (주문형 비디오)를 통한 고가용성
description: 이 문서는 VOD 응용 프로그램의 고가용성을 지 원하는 데 사용할 수 있는 Azure 서비스의 개요입니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 2b20668ea08186f42eed89f82fb1ae8d85cf090d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267703"
---
# <a name="high-availability-with-media-services-and-video-on-demand-vod"></a>Media Services 및 VOD (주문형 비디오)를 통한 고가용성

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="high-availability-for-vod"></a>VOD의 고가용성

Azure 아키텍처 설명서에는 [Geodes](/azure/architecture/patterns/geodes) 라는 고가용성 디자인 패턴이 있습니다. 중복 리소스를 여러 지리적 지역에 배포 하 여 확장성과 복원 력을 제공 하는 방법을 설명 합니다.  Azure 서비스를 사용 하 여 중복성, 상태 모니터링, 부하 분산, 데이터 백업 및 복구와 같은 많은 고가용성 디자인 고려 사항을 포괄 하는 이러한 아키텍처를 만들 수 있습니다.  이러한 아키텍처 중 하나는 솔루션에서 사용 되는 각 서비스에 대 한 세부 정보 및 개별 서비스를 사용 하 여 VOD 응용 프로그램에 대 한 고가용성 아키텍처를 만드는 방법을 설명 합니다.

### <a name="sample"></a>예제

Media Services 및 VOD (주문형 비디오)를 사용 하 여 고가용성을 익히는 데 사용할 수 있는 샘플이 있습니다. 또한 VOD 시나리오에 서비스를 사용 하는 방법에 대해 자세히 설명 합니다.  이 샘플은 현재 형식의 프로덕션에서 사용 하기 위한 것이 아닙니다.  예제 코드와 추가 정보를 신중 하 게 검토 합니다. 특히, [실패 모드](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming) 에 대 한 섹션을 프로덕션 응용 프로그램에 통합 하기 전에 검토 합니다.  VOD (주문형 비디오)에 대 한 고가용성의 프로덕션 구현 에서도 CDN (Content Delivery Network) 전략을 신중 하 게 검토 해야 합니다.  [GitHub에서 코드](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)를 확인 합니다.

## <a name="overview-of-services"></a>서비스 개요

이 예제 아키텍처에서 사용 되는 서비스는 다음과 같습니다.

| 아이콘 | Name | 설명 |
| :--: | ---- | ----------- |
|![이미지](media/media-services-high-availability-encoding/azure-media-services.svg)| Media services 계정 | **설명:**<br>Media Services 계정은 Azure에서 미디어 콘텐츠를 관리, 암호화, 인코딩, 분석 및 스트리밍하는 시작점입니다. Azure Storage 계정 리소스와 연결 되어 있습니다. 계정과 연결 된 모든 저장소는 동일한 Azure 구독에 있어야 합니다.<br><br>**VOD 사용:**<br>비디오 및 오디오 자산을 인코딩하고 배달 하는 데 사용 하는 서비스입니다.  고가용성을 위해 각각 다른 지역에 두 개 이상의 Media Services 계정을 설정 합니다. [Azure Media Services에 대해 자세히 알아보세요](media-services-overview.md). |
|![이미지](media/media-services-high-availability-encoding/storage-account.svg)| 스토리지 계정 | **설명:**<br>Azure storage 계정에는 blob, 파일, 큐, 테이블 및 디스크 등 모든 Azure Storage 데이터 개체가 포함 되어 있습니다. 데이터는 HTTP 또는 HTTPS를 통해 전 세계 어디에서 나 액세스할 수 있습니다.<br><br>각 Media Services 계정의 각 지역에는 동일한 지역에 저장소 계정이 있습니다.<br><br>**VOD 사용:**<br>VOD 처리 및 스트리밍을 위한 입력 및 출력 데이터를 저장할 수 있습니다. [Azure Storage에 대해 자세히 알아보세요](../../storage/common/storage-introduction.md). |
|![이미지](media/media-services-high-availability-encoding/storage-account-queue.svg)| Azure Storage Queue | **설명:**<br>Azure Queue Storage는 HTTP 또는 HTTPS를 사용하여 인증된 호출을 통해 전 세계 어디에서나 액세스할 수 있는 다수의 메시지를 저장하기 위한 서비스입니다.<br><br>**VOD 사용:**<br>큐를 사용 하 여 여러 모듈 간에 작업을 조정 하는 메시지를 보내고 받을 수 있습니다. 이 샘플에서는 Azure Storage 큐를 사용 하지만 Azure는 사용자의 요구에 더 적합할 수 있는 Service Bus 및 Service Fabric 신뢰할 수 있는 큐와 같은 다른 유형의 큐를 제공 합니다. [Azure 큐에 대 한 자세한](../../storage/queues/storage-queues-introduction.md)정보. |
|![이미지](media/media-services-high-availability-encoding/azure-cosmos-db.svg)| Azure Cosmos DB  | **설명:**<br>Azure Cosmos DB은 전 세계의 많은 Azure 지역에서 처리량 및 저장소를 독립적으로 확장 하는 Microsoft의 전 세계적으로 분산 된 다중 모델 데이터베이스 서비스입니다.<br><br>**VOD 사용:**<br>테이블을 사용 하 여 작업 출력 상태 레코드를 저장 하 고 각 Media Services 인스턴스의 상태를 추적할 수 있습니다. Media Services API에 대 한 각 호출의 상태를 추적/기록할 수도 있습니다. [Azure Cosmos DB에 대해 자세히 알아보세요](../../cosmos-db/introduction.md).  |
|![이미지](media/media-services-high-availability-encoding/managed-identity.svg)| 관리 ID | **설명:**<br>관리 id는 azure AD에서 자동으로 관리 되는 id를 제공 하는 Azure AD의 기능입니다. 코드에 자격 증명을 저장 하지 않고 Key Vault을 포함 하 여 Azure AD 인증을 지 원하는 모든 서비스에 인증 하는 데 사용할 수 있습니다.<br><br>**VOD 사용:**<br>Azure Functions는 관리 Id를 사용 하 여 Key Vault에 연결 하기 위해 Media Services 인스턴스에 인증할 수 있습니다. [관리 id에 대해 자세히 알아보세요](../../active-directory/managed-identities-azure-resources/overview.md). |
|![이미지](media/media-services-high-availability-encoding/key-vault.svg)| Key Vault | **설명:**<br>Azure Key Vault를 사용 하 여 토큰, 암호, 인증서, API 키 및 기타 암호에 대 한 액세스를 안전 하 게 저장 하 고 긴밀 하 게 제어할 수 있습니다. 키 관리 솔루션으로도 사용할 수 있습니다. Azure Key Vault를 사용하면 데이터를 암호화하는 데 사용되는 암호화 키를 쉽게 만들고 제어할 수 있습니다. Azure 및 내부 연결 된 리소스에서 사용할 공용 및 개인 전송 계층 보안/SSL(Secure Sockets Layer) (TLS/SSL) 인증서를 쉽게 프로 비전 하 고, 관리 하 고, 배포할 수 있습니다. 소프트웨어 또는 FIPS 140-2 수준 2의 유효성을 검사 한 Hsm으로 비밀 및 키를 보호할 수 있습니다.<br><br>**VOD 사용:**<br>Key Vault를 사용 하 여 응용 프로그램의 서비스 주체에 대 한 액세스 정책을 설정할 수 있습니다.  저장소 계정에 연결 문자열을 저장 하는 데 사용할 수 있습니다. Key Vault 사용 하 여 저장소 계정 및 cosmos db에 연결 문자열을 저장 합니다. Key Vault를 사용 하 여 전체 클러스터 구성을 저장할 수도 있습니다. 각 미디어 서비스 인스턴스에 대해 구독 ID, 리소스 그룹 이름 및 계정 이름을 저장할 수 있습니다. 자세한 내용은 샘플에서 사용 하는 방법을 참조 하세요. [Key Vault에 대해 자세히 알아보세요](../../key-vault/general/overview.md). |
|![이미지](media/media-services-high-availability-encoding/function-app.svg)| Azure 기능 | **설명:**<br>Azure Functions를 사용 하는 응용 프로그램 인프라에 대해 걱정 하지 않고 작은 코드 부분 ("함수" 라고 함)을 실행 합니다. [Azure Functions에 대해 자세히 알아보세요](../../azure-functions/functions-overview.md).<br><br>**VOD 사용:**<br>Azure Functions를 사용 하 여 VOD 응용 프로그램의 모듈을 호스트에 저장할 수 있습니다.  VOD 응용 프로그램의 모듈에는 다음이 포함 될 수 있습니다.<br><br>**작업 예약 모듈**<br>작업 일정 관리 모듈은 Media Services 클러스터에 새 작업을 제출 하는 데 사용할 수 있습니다 (다른 지역에 둘 이상의 인스턴스). 각 Media Services 인스턴스의 상태를 추적 하 고 새 작업을 다음 정상 인스턴스에 제출 합니다.<br><br>**작업 상태 모듈**<br>작업 상태 모듈은 Azure Event Grid 서비스에서 들어오는 작업 출력 상태 이벤트를 수신 대기 합니다. 이벤트 저장소에 이벤트를 저장 하 여 모듈의 나머지 부분에 의해 Media Services Api에 대 한 호출 수를 최소화 합니다.<br><br>**인스턴스 상태 관리 모듈**<br>이 모듈은 제출 된 작업을 추적 하 고 각 Media Services 인스턴스의 상태를 확인 합니다. 완료 되지 않은 작업, 실패 한 작업 및 작업을 추적 합니다.<br><br>**프로 비전 모듈**<br>이 모듈은 처리 된 자산을 프로 비전 합니다. 자산 데이터를 모든 Media Services 인스턴스에 복사 하 고 Azure Front 도어 서비스를 설정 하 여 일부 Media Services 인스턴스를 사용할 수 없는 경우에도 자산을 스트리밍할 수 있도록 합니다. 스트리밍 로케이터도 설정 합니다.<br><br>**작업 확인 모듈**<br>이 모듈은 제출 된 각 작업을 추적 하 고, 실패 한 작업을 다시 전송 하 고, 작업이 성공적으로 완료 되 면 작업 데이터 정리를 수행 합니다.  |
|![이미지](media/media-services-high-availability-encoding/application-service.svg)| App Service (및 계획)  | **설명:**<br>Azure App Service는 웹 애플리케이션, REST API 및 모바일 백 엔드를 호스트하는 HTTP 기반 서비스입니다. .NET, .NET Core, Java, Ruby, Node.js, PHP 또는 Python을 지원 합니다. 응용 프로그램은 Windows 및 Linux 기반 환경에서 실행 되 고 확장 됩니다.<br><br>**VOD 사용:**<br>각 모듈은 App Service에서 호스팅됩니다. [App Service에 대해 자세히 알아보세요](../../app-service/overview.md). |
|![이미지](media/media-services-high-availability-encoding/azure-front-door.svg)| Azure Front Door | **설명:**<br>Azure Front 도어는 고가용성을 위한 최상의 성능 및 빠른 글로벌 장애 조치 (failover)를 최적화 하 여 웹 트래픽의 글로벌 라우팅을 정의, 관리 및 모니터링 하는 데 사용 됩니다.<br><br>**VOD 사용:**<br>Azure 전면 도어를 사용 하 여 트래픽을 스트리밍 끝점으로 라우팅할 수 있습니다. [Azure Front 도어에 대해 자세히 알아보세요](../../frontdoor/front-door-overview.md).  |
|![이미지](media/media-services-high-availability-encoding/event-grid-subscription.svg)| Azure Event Grid | **설명:**<br>이벤트 기반 아키텍처에 대해 생성 된 Event Grid에는 저장소 blob 및 리소스 그룹과 같은 Azure 서비스에서 들어오는 이벤트에 대 한 지원이 기본적으로 제공 됩니다. 또한 사용자 지정 토픽 이벤트를 지원 합니다. 필터를 사용 하 여 특정 이벤트를 여러 끝점으로 라우팅하고, 여러 끝점으로 멀티 캐스트 하 고, 이벤트가 안정적으로 전달 되도록 할 수 있습니다. 모든 지역 및 가용성 영역 간에 여러 장애 도메인에 분산 하 여 가용성을 극대화 합니다.<br><br>**VOD 사용:**<br>Event Grid를 사용 하 여 모든 응용 프로그램 이벤트를 추적 하 고 저장 하 여 작업 상태를 유지할 수 있습니다. [Azure Event Grid에 대해 자세히 알아보세요](../../event-grid/overview.md). |
|![이미지](media/media-services-high-availability-encoding/application-insights.svg)| Application Insights | **설명:** <br>Azure Monitor의 기능인 Application Insights는 개발자와 DevOps 전문가를 위한 확장 가능한 APM(애플리케이션 성능 관리) 서비스입니다. 라이브 응용 프로그램을 모니터링 하는 데 사용 됩니다. 성능 변칙을 검색 하 고, 문제를 진단 하 고 사용자가 앱으로 수행 하는 작업을 이해 하는 분석 도구를 포함 합니다. 성능 및 가용성을 지속적으로 향상시킬 수 있도록 설계되었습니다.<br><br>**VOD 사용:**<br>모든 로그를 Application Insights 보낼 수 있습니다. 성공적으로 생성 된 작업 메시지를 검색 하 여 각 작업을 처리 한 인스턴스를 확인할 수 있습니다. 여기에는 고유 식별자 및 인스턴스 이름 정보를 포함 하 여 제출 된 모든 작업 메타 데이터가 포함 될 수 있습니다. [Application Insights에 대해 자세히 알아보세요](../../azure-monitor/app/app-insights-overview.md). |
## <a name="architecture"></a>Architecture

이 개략적인 다이어그램은 고가용성 및 미디어 서비스를 시작 하기 위해 제공 되는 샘플의 아키텍처를 보여 줍니다.

[![VOD (주문형 비디오) 개략적인 아키텍처 다이어그램 ](media/media-services-high-availability-encoding/high-availability-architecture.svg)](media/media-services-high-availability-encoding/high-availability-architecture.svg#lightbox)

## <a name="best-practices"></a>최선의 구현 방법

### <a name="regions"></a>영역

* Azure Media Services 계정을 두 개 이상 [만듭니다](https://review.docs.microsoft.com/azure/media-services/latest/create-account-cli-how-to) . 두 계정이 서로 다른 지역에 있어야 합니다. 자세한 내용은 [Azure Media Services 서비스를 배포](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)하는 지역을 참조 하세요.
* 작업을 제출할 계획인 동일한 지역에 미디어를 업로드 합니다. 인코딩을 시작 하는 방법에 대 한 자세한 내용은 [HTTPS URL에서 작업 입력 만들기](https://review.docs.microsoft.com/azure/media-services/latest/job-input-from-http-how-to) 또는 [로컬 파일에서 작업 입력 만들기](https://review.docs.microsoft.com/azure/media-services/latest/job-input-from-local-file-how-to)를 참조 하세요.
* 그런 다음 [작업](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept) 을 다른 지역으로 다시 전송 해야 하는 경우를 사용 `JobInputHttp` 하거나를 사용 `Copy-Blob` 하 여 원본 자산 컨테이너의 데이터를 대체 지역의 자산 컨테이너로 복사할 수 있습니다.

### <a name="monitoring"></a>모니터링

* Azure Event Grid를 `JobStateChange` 통해 각 계정에서 메시지를 구독 합니다.
    * Azure Portal 또는 CLI를 통해 [이벤트를 등록](https://review.docs.microsoft.com/azure/media-services/latest/reacting-to-media-services-events) 합니다 (EVENT GRID Management SDK를 사용 하 여 수행할 수도 있음).
    * Media Services 이벤트를 기본적으로 지 원하는 [Microsoft Azure EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) 를 사용 합니다.
    * Azure Functions를 통해 Event Grid 이벤트를 사용할 수도 있습니다.

    추가 정보는 다음 항목을 참조하세요.

    * 어떤 이유로 Azure Event Grid 메시지가 지연 되는 경우 대체 (fallback) 추가를 포함 하 Azure Event Grid 작업을 모니터링 하는 방법을 보여 주는 [오디오 분석 샘플](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept) 을 참조 하세요.
    * [Media Services 이벤트에 대 한 Azure Event Grid 스키마](https://review.docs.microsoft.com/azure/media-services/latest/media-services-event-schemas)를 살펴보세요.

* [작업](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept)을 만들 때:
    * 현재 사용 중인 계정 목록에서 무작위로 계정을 선택 합니다 .이 목록에는 일반적으로 두 계정이 모두 포함 되지만 문제가 검색 되 면 계정이 하나만 포함 될 수 있습니다. 목록이 비어 있으면 운영자가 조사할 수 있도록 경고를 발생 시킵니다.
    * 각 처리 중인 작업 및 사용 된 지역/계정을 추적 하는 레코드를 만듭니다.
* `JobStateChange`작업이 예약 된 상태에 도달 했음을 처리기가 알림을 가져오는 경우 예약 된 상태와 사용 된 지역/계정에 입력 한 시간을 기록 합니다.
* 처리기가 `JobStateChange` 작업이 처리 중 상태에 도달 했음을 알리는 알림을 가져오는 경우 작업에 대 한 레코드를 처리 중으로 표시 하 고 처리 상태로 전환 되는 시간을 기록 합니다.
* 처리기에서 `JobStateChange` 작업이 최종 상태 (완료/오류 발생/취소 됨)에 도달 했음을 알리는 알림을 가져오는 경우 해당 작업에 대 한 레코드를 적절 하 게 표시 합니다.
* 작업의 레코드를 주기적으로 확인 하는 별도의 프로세스를 포함 합니다.
    * 지정 된 지역에 대 한 적절 한 시간 내에 처리 상태로 이동 하지 않는 예약 된 상태의 작업이 있는 경우 현재 사용 된 계정 목록에서 해당 지역을 제거 합니다. 비즈니스 요구 사항에 따라 해당 작업을 바로 취소 하 고 다른 지역으로 다시 전송 하도록 결정할 수 있습니다. 또는 다음 상태로 전환 하는 데 더 많은 시간을 제공할 수 있습니다.
    * 지역이 계정 목록에서 제거 된 경우 목록에 다시 추가 하기 전에 복구를 위해 모니터링 합니다. 지역 상태는 지역에서 기존 작업을 통해 모니터링 (취소 및 다시 제출 되지 않은 경우) 하 고, 일정 시간 후에 다시 목록에 계정을 추가 하 고, Azure Media Services 영향을 줄 수 있는 작동 중단에 대 한 Azure 통신을 모니터링할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [코드 샘플](/samples/browse/?products=azure-media-services) 체크 아웃
