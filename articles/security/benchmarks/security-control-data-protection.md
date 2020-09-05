---
title: Azure 보안 제어-데이터 보호
description: Azure 보안 제어 데이터 보호
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2622d7a2e54dac710ac09d61dee639ab1d104f02
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078999"
---
# <a name="security-control-data-protection"></a>보안 제어: 데이터 보호

데이터 보호 권장 사항은 암호화, 액세스 제어 목록, id 기반 액세스 제어 및 데이터 액세스에 대 한 감사 로깅과 관련 된 문제를 해결 하는 데 중점을 둡니다.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.1 | 13.1 | 고객 |

태그를 사용 하 여 중요 한 정보를 저장 하거나 처리 하는 Azure 리소스 추적을 지원 합니다.

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.2 | 13.2, 2.10 | 고객 |

환경 유형 및 데이터 민감도 수준과 같은 개별 보안 도메인에 대해 별도의 구독 및 관리 그룹을 사용 하 여 격리를 구현 합니다. 응용 프로그램 및 엔터프라이즈 환경에서 요구 하는 Azure 리소스에 대 한 액세스 수준을 제한할 수 있습니다. Azure RBAC (역할 기반 액세스 제어)를 통해 Azure 리소스에 대 한 액세스를 제어할 수 있습니다. 

- [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.3 | 13.3 | 공유됨 |

중요 한 정보를 무단으로 전송 하는 것을 모니터링 하 고 정보 보안 전문가를 경고 하는 동안 이러한 전송을 차단 하는 네트워크 경계의 Azure Marketplace에서 타사 솔루션을 활용 합니다.

Microsoft에서 관리 하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.4. | 14.4 | 공유됨 |

전송 중인 모든 중요 한 정보를 암호화 합니다. Azure 리소스에 연결 하는 모든 클라이언트가 TLS 1.2 이상을 협상할 수 있는지 확인 합니다.

해당 하는 경우 미사용 암호화 및 전송 중인 암호화에 대 한 Azure Security Center 권장 사항을 따릅니다.

- [Azure를 사용 하 여 전송 중인 암호화 이해](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.5 | 14.5 | 공유됨 |

Azure에서 특정 서비스에 대 한 기능을 사용할 수 없는 경우 타사 활성 검색 도구를 사용 하 여 사이트 또는 원격 서비스 공급자를 비롯 하 여 조직의 기술 시스템에서 저장, 처리 또는 전송 하는 중요 한 정보를 모두 식별 하 고 조직의 중요 한 정보 인벤토리를 업데이트 합니다.

Azure Information Protection를 사용 하 여 Office 365 문서 내에서 중요 한 정보를 식별 합니다.

Azure SQL Information Protection를 사용 하 여 Azure SQL Database에 저장 된 정보의 분류 및 레이블 지정을 지원할 수 있습니다.

- [Azure SQL 데이터 검색을 구현 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

- [Azure Information Protection 구현 방법](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)

- [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.6 | 14.6 | 고객 |

Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 데이터 및 리소스에 대 한 액세스를 제어 하 고, 그렇지 않으면 서비스별 액세스 제어 방법을 사용 합니다.

- [Azure RBAC를 구성 하는 방법](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.7 | 14.7 | 공유됨 |

계산 리소스에 대 한 준수를 위해 필요한 경우 시스템에서 데이터를 복사 하는 경우에도 데이터에 대 한 액세스 제어를 적용 하기 위해 자동화 된 호스트 기반 데이터 손실 방지 솔루션과 같은 타사 도구를 구현 합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.8 | 14.8 | 고객 |

모든 Azure 리소스에서 미사용 암호화를 사용 합니다. Azure에서 암호화 키를 관리 하도록 허용 하는 것이 좋지만 일부 인스턴스에서는 사용자 고유의 키를 관리할 수 있는 옵션이 있습니다. 

- [Azure에서 미사용 암호화 이해](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [고객 관리 암호화 키를 구성 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 4.9 | 14.9 | 고객 |

Azure 활동 로그에 Azure Monitor를 사용 하 여 중요 한 Azure 리소스에 변경 내용이 발생 하는 경우에 대 한 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)


## <a name="next-steps"></a>다음 단계

- 다음 보안 제어: [취약성 관리](security-control-vulnerability-management.md) 를 참조 하세요.