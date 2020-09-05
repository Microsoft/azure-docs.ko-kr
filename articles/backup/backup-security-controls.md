---
title: 보안 컨트롤
description: Azure Backup 서비스에서 사용 되는 보안 컨트롤에 대해 알아봅니다. 이러한 컨트롤은 서비스에서 보안 취약성을 방지, 감지 및 대응 하는 데 도움이 됩니다.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: ce1ca0a79476e4985569c677b58892fad91866a5
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892965"
---
# <a name="security-controls-for-azure-backup"></a>Azure Backup에 대 한 보안 제어

이 문서에서는 Azure Backup에 기본 제공 되는 보안 컨트롤을 설명 합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 컨트롤 | 예/아니요 | 메모 | 문서화
|---|---|--|--|
| 서비스 엔드포인트 지원| 예 |  |  |
| VNet 삽입 지원| 예 |  |  |
| 네트워크 격리 및 방화벽 지원| 예 | VM 백업에 대해 강제 터널링이 지원됩니다. 강제 터널링은 Vm 내에서 실행 되는 워크 로드에 대해 지원 되지 않습니다. |  |
| 강제 터널링 지원| 아니요 |  |  |

## <a name="monitoring--logging"></a>모니터링 및 로깅

| 보안 컨트롤 | 예/아니요 | 메모| 문서화
|---|---|--|--|
| Azure 모니터링 지원 (예: Log analytics, App insights)| 예 | Log Analytics는 리소스 로그를 통해 지원 됩니다. 자세한 내용은 [Log Analytics를 사용 하 여 Azure Backup 보호 된 워크 로드 모니터링](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)을 참조 하세요. |  |
| 제어와 관리 평면 로깅 및 감사| 예 | Azure Portal에서 모든 고객이 트리거한 작업은 활동 로그에 기록됩니다. |  |
| 데이터 평면 로깅 및 감사| 아니요 | Azure Backup 데이터 평면은 직접 연결할 수 없습니다.  |  |

## <a name="identity"></a>ID

| 보안 컨트롤 | 예/아니요 | 메모| 문서화
|---|---|--|--|
| 인증| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |  |
| 권한 부여| 예 | 고객이 만들고 Azure 기본 제공 역할을 사용 합니다. 자세한 내용은 [역할 기반 Access Control를 사용 하 여 Azure Backup 복구 지점의 관리](./backup-rbac-rs-vault.md)를 참조 하세요. |  |

## <a name="data-protection"></a>데이터 보호

| 보안 컨트롤 | 예/아니요 | 메모 | 문서화
|---|---|--|--|
| 미사용 서버 쪽 암호화: Microsoft 관리형 키 | 예 | 스토리지 계정에 대해 스토리지 서비스 암호화 사용 |  |
| 미사용 서버 쪽 암호화: 고객 관리형 키(BYOK) | 예 |  |  |
| 열 수준 암호화(Azure Data Services)| 아니요 |  |  |
| 전송 중 암호화(예: ExpressRoute 암호화, VNet 암호화 및 VNet-VNet 암호화)| 아니요 | HTTPS를 사용합니다. |  |
| API 호출 암호화| 예 |  |  |

## <a name="configuration-management"></a>구성 관리

| 보안 컨트롤 | 예/아니요 | 메모| 문서화
|---|---|--|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예|  |  |

## <a name="next-steps"></a>다음 단계

- [Azure 서비스의 기본 제공 보안 컨트롤](../security/fundamentals/security-controls.md)에 대해 자세히 알아봅니다.
