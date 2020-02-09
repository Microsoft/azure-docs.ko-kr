---
title: Azure Key Vault에 대 한 보안 제어
description: Azure Key Vault 평가를 위한 보안 컨트롤의 검사 목록
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: d7f92a309a3d0b5d8e85f1e270d5590f46496a77
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886478"
---
# <a name="security-controls-for-azure-key-vault"></a>Azure Key Vault에 대 한 보안 제어

이 문서에서는 Azure Key Vault에 기본 제공 되는 보안 컨트롤을 설명 합니다. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 | VNet (Virtual Network) 서비스 엔드포인트을 사용 합니다. |
| VNet 삽입 지원| 아니요 |  |
| 네트워크 격리 및 방화벽 지원| 예 | VNet 방화벽 규칙을 사용 합니다. |
| 강제 터널링 지원| 아니요 |  |

## <a name="monitoring--logging"></a>& 로깅 모니터링

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | Log Analytics를 사용합니다. |
| 컨트롤/관리 평면 로깅 및 감사| 예 | Log Analytics를 사용합니다. |
| 데이터 평면 로깅 및 감사| 예 | Log Analytics를 사용합니다. |

## <a name="identity"></a>클레임

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |
| Authorization| 예 | Key Vault 액세스 정책을 사용합니다. |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 서버 쪽 암호화: Microsoft에서 관리 하는 키 | 예 | 모든 개체가 암호화됩니다. |
| 미사용 서버 쪽 암호화: 고객 관리 키 (BYOK) | 예 | 고객은 해당 Key Vault의 모든 키를 제어 합니다. HSM (하드웨어 보안 모듈) 지원 키가 지정 된 경우 FIPS 수준 2 HSM은 키, 인증서 또는 암호를 보호 합니다. |
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 |  |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | 모든 통신은 암호화된 API 호출을 통해 수행됩니다. |
| API 호출 암호화| 예 | HTTPS를 사용합니다. |

## <a name="access-controls"></a>액세스 제어

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| 컨트롤/관리 평면 액세스 컨트롤 | 예 | Azure Resource Manager 역할 기반 Access Control(RBAC) |
| 데이터 평면 액세스 컨트롤(모든 서비스 수준에서) | 예 | Key Vault 액세스 정책 |

## <a name="next-steps"></a>다음 단계

- [Azure 서비스에서 기본 제공 되는 보안 컨트롤](../security/fundamentals/security-controls.md)에 대해 자세히 알아보세요.