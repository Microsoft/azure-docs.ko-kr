---
title: 'Azure Express 경로: 보안 제어'
description: 보안 취약성을 예방, 감지 및 대응 하는 데 도움이 되는 품질 또는 기능인 Azure Express 경로의 보안 제어에 대해 알아봅니다.
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 6a4589d1aa768548f2ae9ffa01f289c823c0b2e5
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192419"
---
# <a name="security-controls-for-azure-expressroute"></a>Azure Express 경로에 대 한 보안 제어

이 문서에서는 Azure Express 경로에 기본 제공 되는 보안 컨트롤을 설명 합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 컨트롤 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| N/A |  |
| VNet 삽입 지원| N/A | |
| 네트워크 격리 및 방화벽 지원| 예 | 각 고객은 자체 라우팅 도메인에 포함 되 고 자체 VNet으로 터널링 됩니다. |
| 강제 터널링 지원| 해당 없음 | Via Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>모니터링 및 로깅

| 보안 컨트롤 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원(Log Analytics, App insights 등)| 예 | [Express 경로 모니터링, 메트릭 및 경고](expressroute-monitoring-metrics-alerts.md)를 참조 하세요.|
| 제어와 관리 평면 로깅 및 감사| 예 |  |
| 데이터 평면 로깅 및 감사| 아니요 |   |

## <a name="identity"></a>ID

| 보안 컨트롤 | 예/아니요 | 메모|
|---|---|--|
| 인증| 예 | Microsoft 용 게이트웨이 서비스 계정 (GWM) (컨트롤러) Dev 및 OP에 대 한 JIT (just-in-time) 액세스 |
| 권한 부여|  예 |Microsoft 용 게이트웨이 서비스 계정 (GWM) (컨트롤러) Dev 및 OP에 대 한 JIT (just-in-time) 액세스 |

## <a name="data-protection"></a>데이터 보호

| 보안 컨트롤 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 쪽 암호화: Microsoft 관리형 키 |  해당 없음 | Express 경로는 고객 데이터를 저장 하지 않습니다. |
| 미사용 서버 쪽 암호화: 고객 관리형 키(BYOK) | N/A |  |
| 열 수준 암호화(Azure Data Services)| 해당 없음 | |
| 전송 중 암호화(예: ExpressRoute 암호화, VNet 암호화 및 VNet-VNet 암호화)| 아니요 | |
| API 호출 암호화| 예 | [Azure Resource Manager](../azure-resource-manager/index.yml) 및 HTTPS를 통해 |


## <a name="configuration-management"></a>구성 관리

| 보안 컨트롤 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| 예 | 네트워크 리소스 공급자 (NRP)를 통해. |

## <a name="next-steps"></a>다음 단계

- [Azure 서비스의 기본 제공 보안 컨트롤](../security/fundamentals/security-controls.md)에 대해 자세히 알아봅니다.