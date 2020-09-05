---
title: Azure Database for PostgreSQL-Hyperscale의 Azure 보안 기준
description: Azure Database for PostgreSQL-Hyperscale 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 37ebd1702ed8ef059b38378b89f5a5cb61748568
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231302"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql---hyperscale"></a>Azure Database for PostgreSQL-Hyperscale의 Azure 보안 기준

Azure Database for PostgreSQL-Hyperscale의 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](/azure/security/benchmarks/security-control-network-security)을 참조하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: Azure Database for PostgreSQL 서버 방화벽은 사용 권한이 있는 컴퓨터를 지정할 때까지 Citus (hyperscale) 코디네이터 노드에 대 한 모든 액세스를 차단 합니다. 방화벽은 각 요청이 시작된 IP 주소의 서버에 대한 액세스를 허용합니다. 방화벽을 구성하려면 허용 가능한 IP 주소 범위를 지정하는 방화벽 규칙을 생성해야 합니다. 서버 수준의 방화벽 규칙을 만들 수 있습니다.

- [Azure Database for PostgreSQL-Hyperscale에서 방화벽 규칙을 구성 하는 방법 (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-firewall-rules)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy를 사용하여 Azure Database for PostgreSQL 인스턴스에 연결된 네트워크 설정 및 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft. Network" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure Database for PostgreSQL 인스턴스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다.

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [네트워킹에 대 한 Azure Policy 샘플](https://docs.microsoft.com/azure/governance/policy/samples/#network)

- [Azure Blueprint를 만드는 방법](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](/azure/security/benchmarks/security-control-logging-monitoring)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 제어 평면 감사 로깅의 경우 Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역, azure 이벤트 허브 또는 보관을 위해 azure storage 계정으로 로그를 보냅니다. Azure 활동 로그 데이터를 사용하면 Azure 리소스의 컨트롤 플레인 수준에서 수행되는 모든 쓰기 작업(PUT, POST, DELETE)에 대한 "무엇을, 누가, 언제"를 판단할 수 있습니다.

또한 Azure Monitor를 통해 로그를 수집 하 여 Hyperscale (Citus)으로 생성 된 보안 데이터를 집계 합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고 장기/보관 저장소에 저장소 계정을 사용 합니다. 또는 Azure Sentinel 또는 타사 SIEM(Security Incident and Event Management)을 사용하도록 설정하고 데이터를 온보딩할 수 있습니다. 

- [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Hyperscale의 메트릭 (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-monitoring)

- [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: hyperscale (Citus)은 서버 그룹의 각 노드에 대 한 메트릭을 제공 합니다. 메트릭은 지원 리소스의 동작에 대 한 통찰력을 제공 합니다. 각 메트릭은 1분 빈도로 내보내지며, 최대 30일 동안 기록됩니다.

제어 평면 감사 로깅의 경우 Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역, Azure 이벤트 허브 또는 보관을 위해 Azure storage 계정으로 로그를 보냅니다. Azure 활동 로그 데이터를 사용하면 Azure 리소스의 컨트롤 플레인 수준에서 수행되는 모든 쓰기 작업(PUT, POST, DELETE)에 대한 "무엇을, 누가, 언제"를 판단할 수 있습니다.

또한 Azure Monitor를 통해 로그를 수집 하 여 Hyperscale (Citus)으로 생성 된 보안 데이터를 집계 합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고 장기/보관 저장소에 저장소 계정을 사용 합니다. 또는 Azure Sentinel 또는 타사 SIEM(Security Incident and Event Management)을 사용하도록 설정하고 데이터를 온보딩할 수 있습니다. 

- [Hyperscale의 메트릭 (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-monitoring)

- [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure Monitor 내에서 Citus (hyperscale) 로그를 저장 하는 데 사용 되는 Log Analytics 작업 영역에 대해 조직의 규정 준수 규정에 따라 보존 기간을 설정 합니다. Azure Storage 계정을 장기/보관 스토리지에 사용합니다.

- [Log Analytics 작업 영역에 대한 로그 보존 기간 매개 변수를 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Azure Storage 계정에 리소스 로그 저장](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: 비정상적인 동작에 대 한 Citus (hyperscale) 인스턴스의 로그를 분석 하 고 모니터링 합니다. Azure Monitor의 Log Analytics를 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다. 또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

- [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Log Analytics에 대 한 자세한 내용은](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: Citus (hyperscale)에 대 한 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역으로 로그를 보낼 수 있습니다. Azure 서비스에 대 한 모니터링 메트릭을 기반으로 경고를 구성 하 고 받을 수 있습니다. Azure Monitor의 Log Analytics를 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다. 또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

Log Analytics 작업 영역을 Azure Sentinel에 등록하여 SOAR(보안 오케스트레이션 자동화 응답) 솔루션을 제공합니다. 이를 통해 플레이북(자동화된 솔루션)을 만들어 보안 문제를 수정하는 데 사용할 수 있습니다.

- [Hyperscale의 메트릭 (Citus)](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-alert-on-metric)

- [Azure 활동 로그에 대 한 진단 설정을 구성 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [보안 제어: ID 및 액세스 제어](/azure/security/benchmarks/security-control-identity-access-control)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: Citus (hyperscale) 인스턴스의 제어 평면 (예: Azure Portal)에 대 한 관리 권한이 있는 사용자 계정의 인벤토리를 유지 관리 합니다. 또한 Citus (Hyperscale) 인스턴스의 데이터 평면에 대 한 액세스 권한이 있는 관리 계정의 인벤토리를 유지 관리 합니다.

Citus (hyperscale)는 기본 제공 역할 기반 액세스 제어를 지원 하지 않지만 특정 리소스 공급자 작업을 기반으로 사용자 지정 역할을 만들 수 있습니다.

또한 PostgreSQL 엔진은 역할을 사용 하 여 데이터베이스 개체에 대 한 액세스를 제어 하 고, 새로 만든 Citus (Hyperscale) 서버 그룹에는 미리 정의 된 여러 역할이 있습니다. 사용자 권한을 수정 하려면 PgAdmin 또는 psql과 같은 도구를 사용 하 여 표준 PostgreSQL 명령을 사용 합니다.

- [Azure 구독에 대 한 사용자 지정 역할 이해](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) 

- [Azure Database for PostgreSQL 리소스 공급자 작업 이해](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbforpostgresql) 

- [Azure Database for PostgreSQL에 대 한 액세스 관리 이해](https://docs.microsoft.com/azure/postgresql/concepts-security#access-management])

- [Azure Database for PostgreSQL-Hyperscale (Citus)에서 사용자를 만드는 방법](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

- [Psql을 사용 하 여 PostgreSQL-Hyperscale (Citus)에 연결 하는 방법](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal#connect-to-the-database-using-psql)


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 해당하는 경우 기본 암호 변경

**지침**: Azure AD에는 기본 암호 개념이 없습니다. 암호를 요구 하는 다른 Azure 리소스는 복잡성 요구 사항과 최소 암호 길이를 사용 하 여 암호를 강제로 만들도록 합니다 .이는 서비스에 따라 다릅니다. 기본 암호를 사용할 수 있는 타사 응용 프로그램 및 marketplace 서비스를 담당 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: Citus (hyperscale) 인스턴스에 액세스 하는 데 사용 되는 전용 관리 계정 사용과 관련 하 여 표준 운영 절차를 만듭니다. Azure 리소스를 관리 하기 위한 관리자 계정은 Azure Active Directory 연결 되며, 데이터베이스 액세스 권한을 관리 하기 위해 Citus (Hyperscale) 서버 그룹 내에 존재 하는 로컬 서버 관리자 계정도 있습니다. Azure Security Center Id 및 액세스 관리를 사용 하 여 Azure Active Directory 내의 관리 계정 수를 모니터링할 수 있습니다.

- [Azure Security Center Id 및 액세스 이해](https://docs.microsoft.com/azure/security-center/security-center-identity-access) 

- [Azure Database for PostgreSQL-Hyperscale (Citus)에서 사용자를 만드는 방법](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Portal에 액세스 하려면 MFA (Azure Active Directory Multi-Factor Authentication)를 사용 하도록 설정 하 고 Azure Security Center Id 및 액세스 관리 권장 사항을 따릅니다.

- [Azure에서 MFA를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](https://docs.microsoft.com/azure/security-center/security-center-identity-access)


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: Azure 리소스에 로그인하여 구성하도록 구성된 MFA(Multi-Factor Authentication)를 통해 PAW(Privileged Access Workstation)를 사용합니다.

- [Privileged Access Workstation에 대한 자세한 정보](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Azure에서 MFA를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-alert-on-account-login-behavior-deviation"></a>3.7: 계정 로그인 동작 편차에 대 한 경고

**지침**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생하는 경우 로그와 경고를 생성하려면 Azure AD(Active Directory) PIM(Privileged Identity Management)을 사용합니다.

Azure AD 위험 검색을 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 봅니다.

- [PIM(Privileged Identity Management)을 배포하는 방법](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

- [Azure AD 위험 탐지 이해](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹화에서만 포털 및 Azure Resource Manager에 액세스할 수 있도록 허용합니다.

- [Azure에서 명명된 위치를 구성하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: PostgreSQL 리소스를 관리 하려면 중앙 인증 및 권한 부여 시스템으로 AD (Azure Active Directory)를 사용 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

Citus (Hyperscale) 서버 그룹 내의 사용자는 Azure Active Directory 계정에 직접 연결할 수 없습니다. 데이터베이스 개체 액세스에 대 한 사용자 권한을 수정 하려면 PgAdmin 또는 psql과 같은 도구를 사용 하 여 표준 PostgreSQL 명령을 사용 합니다.

- [사용자 역할의 권한 수정](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users#how-to-modify-privileges-for-user-role)

- [AAD 인스턴스를 만들고 구성하는 방법](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: PostgreSQL 리소스를 관리 하기 위해 Azure Active Directory를 통해 로컬 데이터베이스에 대 한 액세스 권한이 있는 사용자에 대 한 액세스를 검토 하 고 조정 합니다.

데이터베이스 Azure 리소스 관리에 대 한 액세스 권한이 있는 사용자의 경우 AD (Azure Active Directory) 로그를 검토 하 여 부실 계정을 검색 하는 데 도움을 줍니다. 또한 Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격을 효율적으로 관리 하 고, Citus (Hyperscale)에 액세스 하는 데 사용할 수 있는 엔터프라이즈 응용 프로그램에 액세스 하 고, 역할 할당을 사용 합니다. 사용자 액세스는 90 일 마다 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 해야 합니다.

- [PostgreSQL 사용자 및 할당 된 역할 검토](https://www.postgresql.org/docs/current/database-roles.html)

- [Azure AD 보고 이해](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Azure ID 액세스 검토를 사용하는 방법](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: ad (Azure Active Directory) 내에서 siem/모니터링 도구와 통합할 수 있도록 하는 Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있습니다. 

Azure Active Directory 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics Workspace로 보내면 이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다. 

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대 한 경고

**지침**: Azure Active Directory의 id 보호 및 위험 검색 기능을 사용 하 여 AZURE ACTIVE DIRECTORY (AD) 수준에서 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성할 수 있습니다. 조직의 보안 대응을 구현하기 위해 Azure Sentinel을 통해 자동화된 대응을 사용하도록 설정할 수 있습니다.

추가 조사를 위해 로그를 Azure Sentinel로 수집할 수도 있습니다.

- [Azure AD ID 보호 개요](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)

- [Azure AD 위험한 로그인을 확인하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

- [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 현재 사용할 수 없음 고객 Lockbox는 Citus (Hyperscale)에 대해 아직 지원 되지 않습니다.

- [지원 되 고객 Lockbox 서비스 목록](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 현재 사용할 수 없음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](/azure/security/benchmarks/security-control-data-protection)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용 하 여 중요 한 정보를 저장 하거나 처리 하는 Citus (hyperscale) 인스턴스 또는 관련 리소스를 추적 하는 데 도움을 줍니다.

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. 관리 역할 및 방화벽 규칙의 조합을 사용 하 여 Azure Database for PostgreSQL 인스턴스에 대 한 네트워크 액세스를 격리 하 고 제한 합니다.

- [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Azure Database for PostgreSQL-Hyperscale의 방화벽 규칙 이해 (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-firewall-rules)

- [Hyperscale의 역할 이해 (Citus)](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: Citus (hyperscale) 코디네이터 노드에 대 한 클라이언트 응용 프로그램 연결에는 TLS (Transport Layer Security) 1.2가 필요 합니다. 데이터베이스 서버와 클라이언트 응용 프로그램 간에 TLS 연결을 적용 하면 서버와 응용 프로그램 간에 데이터 스트림을 암호화 하 여 "메시지 가로채기 (man-in-the-middle)" 공격 으로부터 보호 하는 데 도움이 됩니다.

Azure Portal를 통해 프로 비전 된 모든 Azure Database for PostgreSQL 서버의 경우 TLS 연결 적용이 기본적으로 사용 하도록 설정 됩니다.

경우에 따라 타사 응용 프로그램은 신뢰할 수 있는 CA (인증 기관) 인증서 파일 (.cer)에서 생성 된 로컬 인증서 파일을 안전 하 게 연결 해야 합니다.

- [Azure Database for PostgreSQL-Hyperscale에서 TLS를 구성 하는 방법 (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-ssl-connection-security)

- [TLS 연결에 대 한 인증서 확인이 필요한 응용 프로그램](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-ssl-connection-security)



**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: azure RBAC (역할 기반 액세스 제어)를 사용 하 여 Citus (hyperscale) 제어 평면 (예: Azure Portal)에 대 한 액세스를 제어 합니다. Azure RBAC는 데이터베이스 내의 사용자 권한에 영향을 주지 않습니다.

데이터베이스 수준에서 사용자 권한을 수정 하려면 PgAdmin 또는 psql과 같은 도구를 사용 하 여 표준 PostgreSQL 명령을 사용 합니다.

- [Azure RBAC를 구성 하는 방법](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

- [Azure Database for PostgreSQL 용 SQL을 사용 하 여 사용자 액세스를 구성 하는 방법](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**:  
Azure Database for PostgreSQL 하루에 한 번 Citus ()는 데이터 파일과 데이터베이스 트랜잭션 로그의 스냅숏 백업을 수행 합니다. 백업을 사용 하면 보존 기간 내의 특정 시점으로 서버를 복원할 수 있습니다. (보존 기간은 현재 모든 클러스터에 대해 35 일입니다.) 모든 백업은 AES 256 비트 암호화를 사용 하 여 암호화 됩니다. Citus (PostgreSQL Hyperscale) 제품은 암호화에 Microsoft 관리 키를 사용 합니다.

- [Azure PostgreSQL-Hyperscale (Citus) 백업에 대 한 암호화 이해](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)



**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 Citus (hyperscale)의 프로덕션 인스턴스 및 기타 중요 하거나 관련 된 리소스에 대 한 변경 내용이 발생 하는 경우에 대 한 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [보안 제어: 취약성 관리](/azure/security/benchmarks/security-control-vulnerability-management)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: 현재 사용할 수 없음 Azure Security Center는 Citus (Azure Database for PostgreSQL)에 대 한 취약성 평가를 아직 지원 하지 않습니다.

- [Azure Security Center의 Azure PaaS 서비스에 대 한 기능 검사](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 현재 사용할 수 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 제어: 인벤토리 및 자산 관리](/azure/security/benchmarks/security-control-inventory-asset-management)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내에서 모든 리소스 (Citus (hyperscale) 인스턴스 포함)를 쿼리하고 검색 합니다. 테넌트에서 적절한 권한(읽기)이 있는지 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거할 수 있습니다.

- [Azure Graph를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Azure 구독을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Azure RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: Citus (hyperscale) 인스턴스 및 기타 관련 리소스에 태그를 적용 하 여 논리적으로 분류로 구성 하는 메타 데이터를 제공 합니다.

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 태깅, 관리 그룹 및 별도의 구독 (해당 하는 경우)을 사용 하 여 Citus (hyperscale) 인스턴스 및 관련 리소스를 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용한 고객 구독에 만들 수 있는 리소스 종류에 대한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

또한 Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다.

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용한 고객 구독에 만들 수 있는 리소스 종류에 대한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

또한 Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다.

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용한 고객 구독에 만들 수 있는 리소스 종류에 대한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하려면 Azure 조건부 액세스를 사용합니다. 이로 인해 중요 한 정보를 포함 하는 Citus (Hyperscale) 인스턴스와 같이 높은 수준의 보안 환경에서 리소스를 만들고 변경할 수 없습니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](/azure/security/benchmarks/security-control-secure-configuration)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy을 사용 하 여 Citus (hyperscale) 인스턴스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. Azure Policy를 사용 하 여 Azure Database for PostgreSQL 인스턴스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만들 수 있습니다.

또한 Azure Resource Manager은 구성이 조직의 보안 요구 사항을 충족 하거나 초과 하는지 확인 하기 위해 검토 해야 하는 JavaScript Object Notation (JSON)에서 템플릿을 내보낼 수 있습니다. 

- [사용 가능한 Azure 정책 별칭을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal) 



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: azure 정책 [거부] 및 [배포 되지 않은 경우 배포]를 사용 하 여 azure 리소스에서 보안 설정을 적용 합니다.  또한 Azure Resource Manager 템플릿을 사용 하 여 조직에 필요한 Azure 리소스의 보안 구성을 유지 관리할 수 있습니다. 

- [Azure Policy 효과 이해](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [규정 준수를 적용 하는 정책 만들기 및 관리](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Resource Manager 템플릿 개요](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Citus (hyperscale) 인스턴스 및 관련 리소스에 대 한 사용자 지정 Azure 정책 정의를 사용 하는 경우 Azure Repos를 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다.

- [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: azure 정책 [거부] 및 [배포 되지 않은 경우 배포]를 사용 하 여 azure 리소스에서 보안 설정을 적용 합니다.  또한 Azure Resource Manager 템플릿을 사용 하 여 조직에 필요한 Azure 리소스의 보안 구성을 유지 관리할 수 있습니다. 

- [Azure Policy 효과 이해](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [규정 준수를 적용 하는 정책 만들기 및 관리](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Resource Manager 템플릿 개요](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: "Microsoft. DBforPostgreSQL" 네임스페이스에서 Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. Azure Database for PostgreSQL 인스턴스 및 관련 리소스에 대 한 구성을 자동으로 적용 하려면 Azure 정책 [감사], [거부] 및 [배포 안 함]을 사용 합니다.

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: Citus (Azure Database for PostgreSQL-hyperscale)는 현재 관리 되는 id를 직접 지원 하지 않습니다. Azure Database for PostgreSQL 서버를 만드는 동안 관리자 사용자에 대 한 자격 증명을 제공 해야 합니다. Azure Portal 인터페이스에서 추가 사용자 역할을 만들 수 있습니다.

- [Azure Database for PostgreSQL 만들기 – 하이퍼스케일(Citus)](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal#create-an-azure-database-for-postgresql---hyperscale-citus)

- [추가 사용자 역할 만들기](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users#how-to-create-additional-user-roles)


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 현재 사용할 수 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](/azure/security/benchmarks/security-control-malware-defense)를 참조하세요.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지 프로그램은 Azure 서비스를 지 원하는 기본 호스트 (예: Citus)에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

비 컴퓨팅 Azure 리소스(예: App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL 등)로 업로드되는 콘텐츠를 미리 검사합니다. Microsoft는 이러한 인스턴스의 데이터에 액세스할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](/azure/security/benchmarks/security-control-data-recovery)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Azure Database for PostgreSQL – hyperscale (Citus)은 각 노드의 백업을 자동으로 만들고 로컬 중복 저장소에 저장 합니다. 백업을 사용 하 여 Citus (Hyperscale) 클러스터를 지정 된 시간으로 복원할 수 있습니다.

- [Azure Database for PostgreSQL-Hyperscale에서 백업 및 복원 하는 방법 (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: 하루에 한 번, Azure Database for PostgreSQL 데이터 파일 및 데이터베이스 트랜잭션 로그의 스냅숏 백업을 수행 합니다. 백업을 사용 하면 보존 기간 내의 특정 시점으로 서버를 복원할 수 있습니다. 보존 기간은 현재 모든 클러스터에 대해 35 일입니다. 모든 백업은 AES 256비트 암호화를 사용하여 암호화됩니다.

가용성 영역을 지 원하는 Azure 지역에서 백업 스냅숏은 세 가지 가용성 영역에 저장 됩니다. 하나 이상의 가용성 영역이 온라인 상태 이면 Citus (Hyperscale) 클러스터는 복원 가능한입니다.

- [Azure Database for PostgreSQL-Hyperscale에서 백업 및 복원 하는 방법 (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: Azure Database for PostgreSQL에서 hyperscale (Citus) 클러스터를 복원 하면 원래 노드의 백업에서 새 클러스터가 만들어집니다. 클러스터를 최근 35 일 내의 특정 시점으로 복원할 수 있습니다. 복원 프로세스는 원래와 동일한 Azure 지역, 구독 및 리소스 그룹에 새 클러스터를 만듭니다. 새 클러스터 구성은 원래 클러스터 구성과 동일한 노드 수, vCores 수, 저장소 크기 및 사용자 역할과 동일 합니다.

방화벽 설정 및 PostgreSQL 서버 매개 변수는 원래 서버 그룹에서 유지 되지 않습니다. 이러한 값은 기본값으로 다시 설정 됩니다. 방화벽에서 모든 연결을 차단 합니다. 복원 후에는 이러한 설정을 수동으로 조정 해야 합니다.

- [Azure Database for PostgreSQL-Hyperscale에서 백업 및 복원 하는 방법 (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: 삭제 된 Citus (hyperscale) 클러스터는 복원할 수 없습니다. 클러스터를 삭제 하면 해당 클러스터에 속하는 모든 노드가 삭제 되며 복구할 수 없습니다. 클러스터 리소스의 배포 후 실수로 삭제 하거나 예기치 않은 변경 내용을 방지 하기 위해 관리자는 관리 잠금을 활용할 수 있습니다.

- [Azure Database for PostgreSQL-Hyperscale에서 백업 및 복원 하는 방법 (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 대응](/azure/security/benchmarks/security-control-incident-response)을 참조하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 검색에서 사후 검토에 이르는 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할을 정의하는 인시던트 대응 계획이 있는지 확인합니다. 

- [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide) 

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [또한 고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 될 수 있습니다.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다. 

또한 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다. 

- [NIST의 게시물을 참조하세요. IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 안내](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 고객 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다.  문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고 Sentinel을 스트리밍할 수 있습니다. 

- [연속 내보내기를 구성하는 방법](https://docs.microsoft.com/azure/security-center/continuous-export) 

- [경고를 Azure Sentinel로 스트림하는 방법](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다. 

- [워크플로 자동화와 Logic Apps를 구성하는 방법](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 제어: 침투 테스트 및 레드 팀 연습](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: 다음 Microsoft 시행 규칙에 따라 침투 테스트에서 Microsoft 정책을 위반하지 않는지 확인합니다. https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- [Microsoft에서 관리하는 클라우드 인프라, 서비스 및 애플리케이션에 대한 레드 팀 및 라이브 사이트 침투 테스트 실행에 대한 자세한 내용은 ](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)를 참조하세요.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
