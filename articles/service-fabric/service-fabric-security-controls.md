---
title: Azure Service Fabric에 대 한 보안 제어
description: Azure Service Fabric에 대 한 보안 제어에 대해 알아봅니다. 기본 제공 보안 컨트롤의 검사 목록을 포함 합니다.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645432"
---
# <a name="security-controls-for-azure-service-fabric"></a>Azure Service Fabric에 대 한 보안 제어

이 문서에서는 Azure Service Fabric에 기본 제공 되는 보안 컨트롤을 설명 합니다. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 |  |
| VNet 삽입 지원| 예 |  |
| 네트워크 격리 및 방화벽 지원| 예 | NSG(네트워크 보안 그룹) 사용 |
| 강제 터널링 지원| 예 | Azure 네트워킹은 강제 터널링을 제공합니다. |

## <a name="monitoring--logging"></a>& 로깅 모니터링

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | Azure 모니터링 지원 및 타사 지원 사용. |
| 제어 및 관리 평면 로깅 및 감사| 예 | 모든 제어 평면 작업은 감사 및 승인 프로세스를 통해 실행합니다. |
| 데이터 평면 로깅 및 감사| N/A | 고객은 클러스터를 소유합니다.  |

## <a name="identity"></a>ID

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 인증| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |
| 권한 부여| 예 | SFRP를 통한 호출의 IAM(ID 및 액세스 관리). 클러스터 엔드포인트에 대 한 직접 호출은 사용자 및 관리자의 두 가지 역할을 지원 합니다. 고객은 Api를 두 역할에 매핑할 수 있습니다. |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 쪽 암호화: Microsoft 관리 키 | 예 | 고객은 클러스터와 클러스터가 빌드되는 가상 머신 확장 집합을 소유 합니다. 가상 머신 확장 집합에서 Azure disk encryption을 사용 하도록 설정할 수 있습니다. |
| 미사용 서버 쪽 암호화: 고객 관리 키 (BYOK) | 예 | 고객은 클러스터와 클러스터가 빌드되는 가상 머신 확장 집합을 소유 합니다. 가상 머신 확장 집합에서 Azure disk encryption을 사용 하도록 설정할 수 있습니다. |
| 열 수준 암호화 (Azure Data Services)| N/A |  |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 |  |
| API 호출 암호화| 예 | Service Fabric API 호출은 Azure Resource Manager를 통해 수행됩니다. 유효한 JSON 웹 토큰(JWT)이 필요합니다. |

## <a name="configuration-management"></a>Configuration Management

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | |

## <a name="next-steps"></a>다음 단계

- [Azure 서비스에서 기본 제공 되는 보안 컨트롤](../security/fundamentals/security-controls.md)에 대해 자세히 알아보세요.