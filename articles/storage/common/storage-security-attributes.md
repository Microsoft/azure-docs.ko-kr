---
title: Azure Storage에 대 한 일반적인 보안 특성
description: Azure Storage를 평가 하기 위한 일반적인 보안 특성에 대 한 검사 목록
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 8fa43998d0b10eddacdc9e0dd512295559814255
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101965"
---
# <a name="common-security-attributes-for-azure-storage"></a>Azure Storage에 대 한 일반적인 보안 특성

보안은 Azure 서비스의 모든 측면에 통합됩니다. 이 문서에서는 Azure Storage에 기본 제공 되는 일반적인 보안 특성을 설명 합니다. 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 |  |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>Vnet 내부 암호화</li><li>VNet 간 암호화</ul>| 예 | 표준 HTTPS/TLS 메커니즘을 지원 합니다.  사용자가 서비스에 전송 되기 전에 데이터를 암호화할 수도 있습니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 예 | 참조 [Azure Key Vault에서 고객 관리 키를 사용 하 여 Storage 서비스 암호화](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)합니다.|
| 열 수준 암호화 (Azure Data Services)| N/A |  |
| API 호출 암호화| 예 |  |

## <a name="network-segmentation"></a>네트워크 분할

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 예 |  |
| vNET 삽입 지원| N/A |  |
| 네트워크 격리 및 방화벽 지원| 예 | |
| 강제 터널링 지원| N/A |  |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | 사용 가능한 azure Monitor 메트릭 시작 미리 보기를 이제 로그 |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | Azure Active Directory: 공유 키, 공유 액세스 토큰. |
| 권한 부여| 예 | RBAC, POSIX Acl 및 SAS 토큰을 통해 권한 부여를 지원 합니다. |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사 | 예 | Azure Resource Manager 활동 로그 |
| 데이터 평면 로깅 및 감사| 예 | 서비스 진단 로그 및 Azure Monitor 로깅 시작 미리 보기  |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예 | Azure Resource Manager Api를 통해 리소스 공급자 버전 관리를 지원 합니다. |