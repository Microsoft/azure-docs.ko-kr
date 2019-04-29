---
title: Azure 서비스에 대 한 보안 특성
description: Azure Service Fabric을 평가하기 위한 일반적인 보안 특성에 대한 검사 목록입니다.
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 2eb480e10ca3b674895d2d22cc44fb52f305f988
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610002"
---
# <a name="common-security-attributes-for-azure-services"></a>Azure 서비스에 대 한 일반적인 보안 특성

보안은 Azure 서비스의 모든 측면에 통합됩니다. 이 문서는 선택한 Azure 서비스에 대 한 일반적인 보안 특성을 수집합니다. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 | 스토리지 계정에 대해 스토리지 서비스 암호화 사용 |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>Vnet 내부 암호화</li><li>VNet 간 암호화</ul>| 아닙니다. | HTTPS 사용 |
| 암호화 키 처리(CMK, BYOK 등)| 아닙니다. |  |
| 열 수준 암호화(Azure Data Services)| 아닙니다. |  |
| API 호출 암호화| 예 |  |

### <a name="network-segmentation"></a>네트워크 분할

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 아닙니다. |  |
| vNET 삽입 지원| 아닙니다. |  |
| 네트워크 격리/방화벽 지원| 예 | VM 백업에 대해 강제 터널링이 지원됩니다. VM 내부에서 실행되는 워크로드에는 강제 터널링이 지원되지 않습니다. |
| 강제 터널링에 대한 지원 | 아닙니다. |  |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | Log Analytics는 진단 로그를 통해 지원됩니다. 자세한 내용은 Log Analytics를 사용하여 Azure Backup 보호 워크로드 모니터링(https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)을 참조하세요. |

### <a name="iam-support"></a>IAM 지원

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 액세스 관리 - 인증| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |
| 액세스 관리 - 권한 부여| 예 | 고객이 생성하고 기본 제공된 RBAC 역할이 사용됩니다. Azure Backup 복구 지점 관리에 관한 액세스 제어를 참조 하세요. (/ azure/backup/백업-rbac-rs-자격 증명 모음)에 대 한 자세한 내용은 합니다. |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 컨트롤/관리 계획 로깅 및 감사| 예 | Azure Portal에서 모든 고객이 트리거한 작업은 활동 로그에 기록됩니다. |
| 데이터 평면 로깅 및 감사| 아닙니다. | Azure Backup 데이터 평면은 직접 연결할 수 없습니다.  |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예|  |

## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Azure Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 | 모든 개체가 암호화됩니다. |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>Vnet 내부 암호화</li><li>VNet 간 암호화</ul>| 예 | 모든 통신은 암호화된 API 호출을 통해 수행됩니다. |
| 암호화 키 처리(CMK, BYOK 등)| 예 | 고객이 Key Vault의 모든 키를 제어합니다. 하드웨어 보안 모듈 (HSM) 지원 키를 지정한 경우에 FIPS 수준 2 HSM 키, 인증서 또는 암호를 보호 합니다. |
| 열 수준 암호화(Azure Data Services)| N/A |  |
| API 호출 암호화| 예 | HTTPS를 사용합니다. |

### <a name="network-segmentation"></a>네트워크 분할

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 | Vnet(Virtual Network) 서비스 엔드포인트를 사용합니다. |
| vNET 삽입 지원| 아닙니다. |  |
| 네트워크 격리/방화벽 지원| 예 | Vnet 방화벽 규칙을 사용합니다. |
| 강제 터널링 지원 | 아닙니다. |  |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | Log Analytics를 사용합니다. |

### <a name="iam-support"></a>IAM 지원

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 액세스 관리 - 인증| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |
| 액세스 관리 - 권한 부여| 예 | Key Vault 액세스 정책을 사용합니다. |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 컨트롤/관리 평면 로깅 및 감사| 예 | Log Analytics를 사용합니다. |
| 데이터 평면 로깅 및 감사| 예 | Log Analytics를 사용합니다. |

### <a name="access-controls"></a>액세스 제어

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 컨트롤/관리 평면 액세스 컨트롤 | 예 | Azure Resource Manager 역할 기반 Access Control(RBAC) |
| 데이터 평면 액세스 컨트롤(모든 서비스 수준에서) | 예 | Key Vault 액세스 정책 |

## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 | 고객은 클러스터 및 클러스터가 빌드된 VM(가상 머신) 확장 세트를 소유합니다. 가상 머신 확장 집합에서 azure 디스크 암호화를 사용할 수 있습니다. |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>Vnet 내부 암호화</li><li>VNet 간 암호화</ul>| 예 |  |
| 암호화 키 처리(CMK, BYOK 등)| 예 | 고객은 클러스터 및 클러스터가 빌드된 VM(가상 머신) 확장 세트를 소유합니다. 가상 머신 확장 집합에서 azure 디스크 암호화를 사용할 수 있습니다. |
| 열 수준 암호화(Azure Data Services)| N/A |  |
| API 호출 암호화| 예 | Service Fabric API 호출은 Azure Resource Manager를 통해 수행됩니다. 유효한 JSON 웹 토큰(JWT)이 필요합니다. |

### <a name="network-segmentation"></a>네트워크 분할

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 |  |
| vNET 삽입 지원| 예 |  |
| 네트워크 격리 / 방화벽 지원| 예 | NSG(네트워크 보안 그룹) 사용 |
| 강제 터널링에 대한 지원 | 예 | Azure 네트워킹은 강제 터널링을 제공합니다. |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | Azure 및 타사 지원 모니터링을 사용 합니다. |

### <a name="iam-support"></a>IAM 지원

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 액세스 관리 - 인증| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |
| 액세스 관리 - 권한 부여| 예 | SFRP를 통한 호출의 IAM(ID 및 액세스 관리). 클러스터 엔드포인트에 대한 직접 호출은 다음 두 역할을 지원합니다. 사용자 및 관리자. 고객은 API를 두 역할 중 하나에 매핑할 수 있습니다. |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 컨트롤/관리 계획 로깅 및 감사| 예 | 모든 제어 평면 작업은 감사 및 승인 프로세스를 통해 실행합니다. |
| 데이터 평면 로깅 및 감사| N/A | 고객은 클러스터를 소유합니다.  |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예 | 서비스 구성은 Azure 배포를 사용하여 버전 지정되고 배포됩니다. 코드(애플리케이션 및 런타임)는 Azure 빌드를 사용하여 버전 지정됩니다.
 |

## <a name="azure-storage"></a>Azure Storage

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 |  |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>Vnet 내부 암호화</li><li>VNet 간 암호화</ul>| 예 | 표준 HTTPS/TLS 메커니즘을 지원 합니다.  사용자가 서비스에 전송 되기 전에 데이터를 암호화할 수도 있습니다. |
| 암호화 키 처리(CMK, BYOK 등)| 예 | 참조 [Azure Key Vault에서 고객 관리 키를 사용 하 여 Storage 서비스 암호화](../storage/common/storage-service-encryption-customer-managed-keys.md)합니다.|
| 열 수준 암호화(Azure Data Services)| N/A |  |
| API 호출 암호화| 예 |  |

### <a name="network-segmentation"></a>네트워크 분할

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 |  |
| vNET 삽입 지원| N/A |  |
| 네트워크 격리/방화벽 지원| 예 | |
| 강제 터널링 지원 | N/A |  |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | 사용 가능한 azure Monitor 메트릭 시작 미리 보기를 이제 로그 |

### <a name="iam-support"></a>IAM 지원

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 액세스 관리 - 인증| 예 | Azure Active Directory: 공유 키, 공유 액세스 토큰. |
| 액세스 관리 - 권한 부여| 예 | RBAC, POSIX Acl 및 SAS 토큰을 통해 권한 부여를 지원 합니다. |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 컨트롤/관리 계획 로깅 및 감사 | 예 | Azure Resource Manager 활동 로그 |
| 데이터 평면 로깅 및 감사| 예 | 서비스 진단 로그 및 Azure Monitor 로깅 시작 미리 보기  |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예 | Azure Resource Manager Api를 통해 리소스 공급자 버전 관리를 지원 합니다. |