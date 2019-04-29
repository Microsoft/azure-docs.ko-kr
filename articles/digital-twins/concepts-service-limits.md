---
title: Azure Digital Twins 공개 미리 보기 서비스 제한 | Microsoft Docs
description: Azure Digital Twins 공개 미리 보기 서비스 제한 이해
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: dwalthermsft
ms.openlocfilehash: cc873ad441c93a7fce54c275e9f7d52f0b044319
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60927556"
---
# <a name="public-preview-service-limits"></a>공개 미리 보기 서비스 제한

공개 미리 보기 중에 Azure Digital Twins에는 다음과 같은 임시 구독, 인스턴스 및 요금 제한이 있습니다.

이러한 제약 조건은 새 서비스 및 서비스의 다양한 기능에 대한 학습을 단순화하는 데 도움이 되도록 존재합니다.

> [!NOTE]
> 이러한 제한은 GA(일반 공급)에 의해 많아지거나 제거됩니다.

## <a name="per-subscription-limits"></a>구독당 제한

공개 미리 보기 중에 각 Azure 구독은 한 번에 하나의 Azure Digital Twins 인스턴스만 만들거나 실행할 수 있습니다.

> [!TIP]
> 인스턴스를 삭제하는 경우 새로 만들 수 있습니다.

## <a name="per-instance-limits"></a>인스턴스당 제한

차례로 각 Azure Digital Twins 인스턴스에는 다음이 포함될 수 있습니다.

- 서비스 프로비전 중에 자동으로 생성된 포함된 정확히 하나의 **IoTHub** 리소스
- 이벤트 유형 **DeviceMessage**에 대해 정확히 하나의 **EventHub** 엔드포인트
- 이벤트 유형 **SensorChange**, **SpaceChange**, **TopologyOperation** 또는 **UdfCustom**의 최대 3개의 **EventHub**, **ServiceBus** 또는 **EventGrid** 엔드포인트.

> [!NOTE]
> 일반적으로 위의 Azure IoT 엔터티를 만들 때 정의되는 일부 매개 변수가 공개 미리 보기 동안 필요하지 않습니다.
> - 최신 API 사양에 대해서는 [Swagger 참조 설명서](./how-to-use-swagger.md)를 참조하세요.

## <a name="azure-digital-twins-management-api-limits"></a>Azure Digital Twins 관리 API 제한

Azure Digital Twins 관리 API의 요청 빈도 제한은 다음과 같습니다.

- Azure Digital Twins 관리 API에 대한 초당 100개의 요청
- 단일 Azure Digital Twins 관리 API 쿼리에서 반환되는 최대 1,000개의 개체

> [!IMPORTANT]
> 1,000개 개체 제한을 초과하면 오류가 수신되며 쿼리를 단순화해야 합니다.

## <a name="user-defined-functions-rate-limits"></a>사용자 정의 함수 속도 제한

다음 제한은 Azure Digital Twins 인스턴스에 요청한 모든 사용자 정의 함수 호출의 총 수를 설정합니다.

- 초당 400개의 클라이언트 라이브러리 호출
- 초당 100개의 **SendNotification** 호출

> [!NOTE]
> 다음 작업을 수행하면 임시로 추가 빈도 제한이 적용될 수 있습니다.
> - 토폴로지 개체 메타데이터에 대한 편집
> - 사용자 정의 함수 정의에 대한 업데이트
> - 처음으로 원격 분석을 전송한 디바이스

## <a name="device-telemetry-limits"></a>디바이스 원격 분석 제한

다음 제한은 디바이스가 Azure Digital Twins 인스턴스에 보낼 수 있는 모든 메시지의 총 수를 제한합니다.

- 초당 100개의 메시지

## <a name="next-steps"></a>다음 단계

- Azure Digital Twins 샘플을 사용하려면 [사용 가능한 회의실을 찾는 빠른 시작](./quickstart-view-occupancy-dotnet.md)으로 이동하세요.