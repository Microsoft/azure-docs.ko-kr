---
title: 전용 이벤트 허브 개요 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Event Hubs의 단일 테넌트 배포를 제공 하는 전용 Azure Event Hubs에 대한 개요를 제공 합니다.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: f67be1d31125b21048deca4d9cafcc76f4ffc3b1
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516752"
---
# <a name="overview-of-event-hubs-dedicated"></a>Event Hubs Dedicated의 개요

*Event Hubs 클러스터* 는 요구 사항이 가장 까다로운 고객을 위해 단일 테넌트 배포를 제공 합니다. 이 단일 테넌트 제품은 99.99% SLA를 보장하며 Microsoft의 Dedicated 가격 책정 계층에서만 사용할 수 있습니다. Event Hubs 클러스터는 보장 된 용량과 초 초 대기 시간을 사용 하 여 초당 수백만 개의 이벤트를 수신 합니다. 전용 클러스터 내에서 생성 되는 네임 스페이스 및 event hubs에는 표준 제공 및 기타 모든 기능 (수신 제한 없음)이 포함 됩니다. 또한 자주 사용 하는 [Event Hubs 캡처](event-hubs-capture-overview.md) 기능을 추가 비용 없이 포함 하 여 자동으로 Azure Storage 또는 Azure Data Lake 데이터 스트림을 일괄 처리 하 고 기록할 수 있습니다. 

클러스터는 사전 할당 된 CPU 및 메모리 리소스 용량의 **CUs (용량 단위)** 로 프로 비전 되 고 청구 됩니다. 각 클러스터에 1, 2, 4, 8, 12, 16 또는 20개 CU를 구매할 수 있습니다. CU 당 수집 하 고 스트리밍할 수 있는 양은 생산자와 소비자의 수, 페이로드 모양, 송신 요금 등 다양 한 요인에 따라 달라 집니다. 자세한 내용은 아래의 벤치 마크 결과를 참조 하세요. 

> [!NOTE]
> 모든 Event Hubs 클러스터는 기본적으로 사용 하도록 설정 되며, 기존 Kafka 기반 응용 프로그램에서 사용할 수 있는 Kafka 엔드포인트을 지원 합니다. 클러스터에서 Kafka을 사용 하도록 설정 하면 Kafka 사용 사례에 영향을 주지 않습니다. 클러스터에서 Kafka을 사용 하지 않도록 설정 하거나 옵션을 사용 하지 않도록 설정 해야 합니다.

## <a name="why-dedicated"></a>전용 이유

전용 Event Hubs는 엔터프라이즈급 용량을 필요로 하는 고객을 위한 세 가지 강력한 혜택을 제공 합니다.

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>성능 향상을 위해 단일 테넌트의 용량 보장

전용 클러스터는 전체 규모에서 용량을 보장 하 고 모든 트래픽 버스트를 수용 하기 위해 완전 내구성이 뛰어난 저장소 및 1 초 대기 시간을 사용 하 여 최대 기가바이트의 스트리밍 데이터를 수신 합니다. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>기능에 대한 포함 및 배타적 액세스 
전용 제품에는 추가 비용 없이 캡처와 같은 기능을 포함 하 고 Bring Your Own Key (BYOK)와 같은 예정 된 기능에 대한 배타적 액세스를 포함 합니다. 또한 서비스는 인프라 유지 관리에 대한 시간을 줄이고 클라이언트 쪽 기능을 구축 하는 데 더 많은 시간을 소비할 수 있도록 고객에 대한 부하 분산, OS 업데이트, 보안 패치 및 분할을 관리 합니다.  

#### <a name="cost-savings"></a>비용 절감
대량 수신 볼륨 (> 100 Tu)에서는 표준 제품에서 비슷한 처리량 단위를 구입 하는 것 보다 클러스터 비용이 시간당 크게 줄어듭니다.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>할당량 및 한도 Event Hubs Dedicated

Event Hubs Dedicated 제품은 최소 4 시간 사용으로 고정 된 월별 가격으로 청구 됩니다. 전용 계층은 표준 요금제의 모든 기능을 제공 하지만, 엔터프라이즈 규모 용량과 워크 로드가 까다로운 고객에 대한 제한을 제공 합니다. 

| 기능 | Standard | 전용 |
| --- |:---:|:---:|
| Bandwidth | 20 Tu (최대 40 Tu) | 20 CUs |
| 네임스페이스 |  1 | CU 당 50 |
| Event Hubs(영문) |  네임 스페이스 당 10 | 1000 네임 스페이스 |
| 수신 이벤트 | 100만 이벤트당 요금 부과 | 포함 항목 |
| 메시지 크기 | 100만 바이트 | 100만 바이트 |
| 파티션 | 32 이벤트 허브 당 | 1024 이벤트 허브 당 |
| 소비자 그룹 | 이벤트 허브 당 20 | CU 당 제한 없음, 이벤트 허브 당 1000 |
| 조정된 연결 | 1000 포함, 최대 5000 | 100 K 포함 및 최대 |
| 메시지 보존 | TU 당 7 일, 84 GB 포함 | CU 당 90 일, 10TB 포함 |
| 캡처 | 시간당 요금 부과 | 포함 항목 |

## <a name="how-to-onboard"></a>등록 방법

[Azure Portal](https://aka.ms/eventhubsclusterquickstart) 을 통해 [Event Hubs 클러스터를 만드는](event-hubs-dedicated-cluster-create-portal.md) 셀프 서비스 환경은 이제 미리 보기로 제공 됩니다. 질문이 있거나 Event Hubs Dedicated 등록 하는 데 도움이 필요한 경우 [Event Hubs 팀](mailto:askeventhubs@microsoft.com)에 문의 하세요.

## <a name="faqs"></a>FAQ

#### <a name="what-can-i-achieve-with-a-cluster"></a>클러스터로 무엇을 할 수 있나요?

Event Hubs 클러스터의 경우 수집 하 고 스트리밍할 수 있는 양은 생산자, 소비자, 수집 및 처리 속도 등 다양 한 요인에 따라 달라 집니다. 

다음 표에는 테스트를 통해 실현한 벤치마크 결과가 나와 있습니다.

| 페이로드 셰이프 | 수신기 | 수신 대역폭| 수신 메시지 | 송신 대역폭 | 송신 메시지 | 총 TU | CU당 TU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB의 일괄 처리 | 2 | 400MB/초 | 초당 400k 메시지 | 800MB/초 | 800k messages/sec | 400TU | 100TU | 
| 10x10KB의 일괄 처리 | 2 | 666MB/초 | 66.6 k messages/sec | 1.33GB/초 | 133k 메시지/초 | 666TU | 166TU |
| 6x32KB의 일괄 처리 | 1 | 1.05GB/초 | 34k 메시지/초 | 1.05GB/초 | 34k 메시지/초 | 1,000TU | 250TU |

테스트에 사용된 조건은 다음과 같습니다.

- CUs (용량 단위)가 4 개인 전용 계층 Event Hubs 클러스터가 사용 되었습니다. 
- 수집에 사용된 이벤트 허브에는 200개의 파티션이 있었습니다. 
- 데이터는 모든 파티션으로부터 받는 두 개의 수신기 애플리케이션에서 수집되었습니다.

#### <a name="can-i-scale-updown-my-cluster"></a>클러스터를 확장/축소할 수 있나요?

만든 후에는 최소 4 시간 사용에 대한 클러스터가 청구 됩니다. 셀프 서비스 환경의 Preview 릴리스에서는 *기술 > 할당량 > 요청* 하는 Event Hubs 팀에 [지원 요청](https://ms.portal.azure.com/#create/Microsoft.Support) 을 제출 하 여 클러스터를 확장 하거나 축소할 수 있도록 전용 클러스터를 강화 하거나 규모를 확장할 수 있습니다. 클러스터 규모를 축소 하는 요청을 완료 하는 데 최대 7 일까 지 걸릴 수 있습니다. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>지리적 DR은 내 클러스터와 어떻게 작동 하나요?

전용 계층 클러스터 아래의 다른 네임 스페이스를 사용 하 여 전용 계층 클러스터에서 네임 스페이스를 지리적으로 쌍으로 연결할 수 있습니다. 처리량 제한이 호환 되지 않아 오류가 발생 하므로 표준 제품에서 전용 계층 네임 스페이스를 네임 스페이스와 페어링 하지 않는 것이 좋습니다. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>표준 네임 스페이스를 전용 계층 클러스터에 속하도록 마이그레이션할 수 있나요?
현재는 표준 네임 스페이스에서 전용으로 event hubs 데이터를 마이그레이션하기 위한 자동 마이그레이션 프로세스를 지원 하지 않습니다. 

## <a name="next-steps"></a>다음 단계

Event Hubs Dedicated에 대한 자세한 내용을 보려면 Microsoft 영업 담당자 또는 Microsoft 지원에 문의 하세요. 다음 링크를 방문 하 여 클러스터를 만들거나 Event Hubs 가격 책정 계층에 대한 자세한 정보를 확인할 수도 있습니다.

- [Azure Portal을 통해 Event Hubs 클러스터 만들기](https://aka.ms/eventhubsclusterquickstart) 
- [Event Hubs Dedicated 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/) Microsoft 영업 담당자 또는 Microsoft 지원 서비스에서 Event Hubs Dedicated 용량에 대한 추가 정보를 얻을 수도 있습니다.
- [Event Hubs FAQ](event-hubs-faq.md)에서는 가격 책정 정보를 설명하고 이벤트 허브에 대해 일부 자주 묻는 질문에 대한 답변을 제공합니다.
