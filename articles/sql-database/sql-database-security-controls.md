---
title: 보안 컨트롤
description: Azure SQL Database 평가를 위한 보안 컨트롤의 검사 목록
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: ce7f3eafa57cbd993be98f4a2da3d89cb312f9b7
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190686"
---
# <a name="security-controls-for-azure-sql-database"></a>Azure SQL Database에 대 한 보안 제어

이 문서에서는 Azure SQL Database에 기본 제공 되는 보안 컨트롤을 설명 합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

SQL Database에는 [단일 데이터베이스](sql-database-single-index.yml) 와 [관리 되는 인스턴스가](sql-database-managed-instance.md)모두 포함 됩니다. 다음 항목은 별도로 명시 된 경우를 제외 하 고 두 제품 모두에 적용 됩니다.

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| yes | [단일 데이터베이스](sql-database-single-index.yml) 에만 적용 됩니다. |
| Azure Virtual Network 주입 지원| yes | [관리 되는 인스턴스에만](sql-database-managed-instance.md) 적용 됩니다. |
| 네트워크 격리 및 방화벽 지원| yes | 데이터베이스 수준 및 서버 수준 모두에서의 방화벽. 네트워크 격리는 관리 되는 [인스턴스에만](sql-database-managed-instance.md) 해당 됩니다. |
| 강제 터널링 지원| yes | [Express](../expressroute/index.yml) 경로 VPN을 통해 [관리 되는 인스턴스입니다](sql-database-managed-instance.md) . |

## <a name="monitoring--logging"></a>& 로깅 모니터링

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| Log Analytics 또는 Application Insights와 같은 Azure 모니터링 지원| yes | Imperva의 SIEM 솔루션인 SecureSphere는 [SQL 감사](sql-database-auditing.md)를 통해 [Azure Event Hubs](../event-hubs/index.yml) 통합을 통해서도 지원 됩니다. |
| 제어 평면 및 관리-평면 로깅 및 감사| yes | 일부 이벤트에 대해서만 예 |
| 데이터 평면 로깅 및 감사 | yes | [SQL 감사](sql-database-auditing.md) 를 통해 |

## <a name="identity"></a>ID

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 인증| yes | Azure AD(Azure Active Directory) |
| 권한 부여| yes | None |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 쪽 암호화: Microsoft 관리 키 | yes | [Always Encrypted](sql-database-always-encrypted.md)문서에 설명 된 대로 "사용 중인 암호화" 라고 합니다. 서버 쪽 암호화는 [투명 한 데이터 암호화](transparent-data-encryption-azure-sql.md)를 사용 합니다.|
| 전송 중 암호화:<ul><li>Azure Express 경로 암호화</li><li>가상 네트워크의 암호화</li><li>가상 네트워크 간 암호화</ul>| yes | HTTPS를 사용합니다. |
| 암호화-키 처리 (예: CMK 또는 BYOK)| yes | 서비스 관리 및 고객 관리 키 처리가 모두 제공 됩니다. 후자는 [Azure Key Vault](../key-vault/index.yml)를 통해 제공 됩니다. |
| Azure 데이터 서비스에서 제공 하는 열 수준 암호화| yes | [Always Encrypted](sql-database-always-encrypted.md). |
| 암호화 된 API 호출| yes | HTTPS/TLS를 사용 합니다. |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (예: 구성의 버전 관리)| 예  | None |

## <a name="additional-security-controls-for-sql-database"></a>SQL Database에 대 한 추가 보안 제어

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 예방: 취약성 평가 | yes | [SQL 취약성 평가 서비스를 참조 하 여 데이터베이스 취약성을 식별할 수 있습니다](sql-vulnerability-assessment.md). |
| 예방: 데이터 검색 및 분류  | yes | [Azure SQL Database 및 SQL Data Warehouse 데이터 검색 & 분류를](sql-database-data-discovery-and-classification.md)참조 하세요. |
| 검색: 위협 검색 | yes | [Azure SQL Database에 대 한 Advanced Threat Protection을](sql-database-threat-detection-overview.md)참조 하세요. |

## <a name="next-steps"></a>다음 단계

- [Azure 서비스에서 기본 제공 되는 보안 컨트롤](../security/fundamentals/security-controls.md)에 대해 자세히 알아보세요.
