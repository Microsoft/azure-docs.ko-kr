---
title: Azure Event Hubs-리소스 관리자 예외 | Microsoft Docs
description: Azure Resource Manager 및 제안 된 작업으로 표시 되는 Azure Event Hubs 예외 목록입니다.
services: service-bus-messaging
documentationcenter: na
author: spelluru
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2019
ms.author: spelluru
ms.openlocfilehash: e6ee1137fce97cbe5a64aa5287223f6ba09dcf47
ms.sourcegitcommit: 6e42ce0ca0a7ac572398e9d024fcf69906670d74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74936088"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Azure Event Hubs-리소스 관리자 예외
이 문서에서는 Azure Resource Manager via 템플릿 또는 직접 호출을 사용 하 여 Azure Event Hubs와 상호 작용할 때 생성 되는 예외를 나열 합니다.

> [!IMPORTANT]
> 이 문서는 자주 업데이트 됩니다. 업데이트를 다시 확인 하세요.

다음 섹션에서는 Azure Resource Manager를 통해 표시 되는 다양 한 예외/오류를 제공 합니다.

## <a name="error-code-conflict"></a>오류 코드: 충돌

| 오류 코드 | 오류 하위 코드 | 오류 메시지 | 설명 | 권장 사항 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 충돌 | 40300 | EventHub 형식의 최대 리소스 수에 도달 했거나이를 초과 했습니다. 실제: #, 허용 된 최대: # | 네임 스페이스가 포함할 수 있는 Event Hubs 수에 대한 [할당량](event-hubs-quotas.md) 에 도달 했습니다. | 네임 스페이스에서 사용 되지 않거나 불필요 한 event hubs를 삭제 하거나 [전용 클러스터](event-hubs-dedicated-overview.md)로 업그레이드 하는 것이 좋습니다. |
| 충돌 | 없음 | 복제를 진행 중 이므로 DR (재해 복구) 구성을 삭제할 수 없습니다. DR 구성 삭제를 시도 하기 전에 페어링을 장애 조치 (failover) 하거나 중단 합니다. | [Geodr 복제가](event-hubs-geo-dr.md) 진행 중 이므로 지금은 구성을 삭제할 수 없습니다. | 구성 삭제를 차단 해제 하려면 복제가 완료 될 때까지 기다리거나 장애 조치 (failover)를 트리거하거나 GeoDR 페어링을 중단 합니다. |
| 충돌 | 없음 | 백 엔드에서 충돌 하 여 네임 스페이스를 업데이트 하지 못했습니다. | 이 네임 스페이스에서 현재 다른 작업을 수행 하 고 있습니다. | 현재 작업이 완료 될 때까지 기다렸다가 다시 시도 하세요. |

## <a name="error-code-429"></a>오류 코드: 429

| 오류 코드 | 오류 하위 코드 | 오류 메시지 | 설명 | 권장 사항 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 없음 | 전환에서 네임 스페이스 프로 비전 | 이 네임 스페이스에서 현재 다른 작업을 수행 하 고 있습니다. | 현재 작업이 완료 될 때까지 기다렸다가 다시 시도 하세요. |
| 429 | 없음 | 재해 복구 작업을 진행 중입니다. | 현재이 네임 스페이스 또는 페어링에서 [Geodr](event-hubs-geo-dr.md) 작업을 수행 하 고 있습니다. | 현재 GeoDR 작업이 완료 될 때까지 기다렸다가 다시 시도 하세요. |

## <a name="error-code-badrequest"></a>오류 코드: BadRequest

| 오류 코드 | 오류 하위 코드 | 오류 메시지 | 설명 | 권장 사항 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40,000 | 이벤트 허브에 대한 파티션 수를 변경할 수 없습니다. | Azure Event Hubs의 기본 또는 표준 계층은 파티션 변경을 지원 하지 않습니다. | 기본 또는 표준 계층 네임 스페이스에서 원하는 수의 파티션을 사용 하 여 새 이벤트 허브를 만듭니다. 파티션 확장은 [전용 클러스터](event-hubs-dedicated-overview.md)에 대해 지원 됩니다. |
| BadRequest | 40,000 | MessageRetentionInDays의 ' # ' 값은 기본 계층에 대해 유효 하지 않습니다. 값은 ' 1 ' 일을 초과할 수 없습니다. | 기본 계층 Event Hubs 네임 스페이스는 최대 1 일의 메시지 보존만 지원 합니다. | 하나 이상의 메시지 보존이 필요한 경우 [표준 Event Hubs 네임 스페이스를 만듭니다](event-hubs-create.md). | 
| BadRequest | 없음 | 지정 된 이름을 사용할 수 없습니다. | 네임 스페이스 이름은 고유 해야 하며 지정 된 이름이 이미 사용 되었습니다. | 지정 된 이름을 사용 하 여 기존 네임 스페이스의 소유자 인 경우 데이터 손실을 초래 하는 삭제할 수 있습니다. 그런 다음 같은 이름으로 다시 시도 합니다. 네임 스페이스를 삭제 하는 것이 안전 하지 않은 경우 (또는 소유자가 아닌 경우) 다른 네임 스페이스 이름을 선택 합니다. |
| BadRequest | 없음 | 지정 된 구독이 네임 스페이스의 할당량에 도달 했습니다. | 구독이 보유할 수 있는 네임 스페이스 수에 대한 [할당량](event-hubs-quotas.md) 에 도달 했습니다. | 이 구독에서 사용 하지 않는 네임 스페이스를 삭제 하거나 다른 구독을 만들거나 [전용 클러스터](event-hubs-dedicated-overview.md)로 업그레이드 하는 것이 좋습니다. |
| BadRequest | 없음 | 보조 네임 스페이스를 업데이트할 수 없습니다. | 네임 스페이스는 [Geodr 페어링](event-hubs-geo-dr.md)의 보조 네임 스페이스 이므로 업데이트할 수 없습니다. | 해당 하는 경우이 페어링의 기본 네임 스페이스를 대신 변경 합니다. 그렇지 않으면 GeoDR 페어링을 중단 하 여 변경 합니다. |
| BadRequest | 없음 | 기본 SKU에서 자동 확장을 설정할 수 없습니다. | 기본 계층 Event Hubs 네임 스페이스에서 자동 확장을 사용 하도록 설정할 수 없습니다. | 네임 스페이스에서 자동 확장을 [사용](event-hubs-auto-inflate.md) 하려면 표준 계층 인지 확인 합니다. |
| BadRequest | 없음 | 용량이 부족 하 여 네임 스페이스를 만들 수 없습니다. Event Hubs 관리자에 게 문의 하십시오. | 선택한 지역이 용량에 있고 더 이상 네임 스페이스를 만들 수 없습니다. | 네임 스페이스를 저장할 다른 지역을 선택 합니다. |
| BadRequest | 없음 | ' Namespace name ' 네임 스페이스에서 ' Basic ' 계층을 사용 하 고 있으므로 엔터티 형식 ' ConsumerGroup '에 대한 작업을 수행할 수 없습니다.  | 기본 계층 Event Hubs 네임 스페이스에는 소비자 그룹 하나 (기본값)의 [quota] ((event hubs-할당량과 # event-and-standard---할당량 및 제한)가 있습니다. 추가 소비자 그룹을 만드는 것은 지원 되지 않습니다. | 기본 소비자 그룹 ($Default)을 계속 사용 하거나, 필요한 경우 표준 계층 Event Hubs 네임 스페이스를 대신 사용 하는 것이 좋습니다. | 
| BadRequest | 없음 | 네임 스페이스 ' 네임 스페이스 이름 '이 없습니다. | 제공 된 네임 스페이스를 찾을 수 없습니다. | 네임 스페이스 이름이 올바르고 구독에서 찾을 수 있는지 확인 합니다. 그렇지 않으면 [Event Hubs 네임 스페이스를 만듭니다](event-hubs-create.md). | 
| BadRequest | 없음 | 리소스의 location 속성이 포함 하는 네임 스페이스와 일치 하지 않습니다. | 특정 지역에 이벤트 허브를 만들지 못했습니다 .이는 네임 스페이스의 지역과 일치 하지 않기 때문입니다. | 네임 스페이스와 동일한 지역에 이벤트 허브를 만들어 보세요. | 

## <a name="error-code-internal-server-error"></a>오류 코드: 내부 서버 오류

| 오류 코드 | 오류 하위 코드 | 오류 메시지 | 설명 | 권장 사항 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 내부 서버 오류 | 없음 | 내부 서버 오류. | Event Hubs 서비스에 내부 오류가 발생 했습니다. | 실패 한 작업을 다시 시도 합니다. 작업이 계속 실패 하면 지원 담당자에 게 문의 하세요. |