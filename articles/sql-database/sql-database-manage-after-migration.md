---
title: 마이그레이션 후 단일 데이터베이스 및 풀링된 데이터베이스 관리 - Azure SQL Database | Microsoft Docs
description: Azure SQL Database로 마이그레이션 후 데이터베이스를 관리하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
manager: craigg
ms.date: 02/13/2019
ms.openlocfilehash: a83bc6518409add8a0732e5a0b17ab46c36564af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703374"
---
# <a name="new-dba-in-the-cloud--managing-your-single-and-pooled-databases-in-azure-sql-database"></a>클라우드의 새 DBA - Azure SQL Database의 단일 데이터베이스 및 풀링된 데이터베이스 관리

기존의 자체 관리되고 자체 제어되는 환경에서 PaaS 환경으로 이동은 언뜻 보면 매우 어려울 것 같을 수 있습니다. 앱 개발자 또는 DBA는 애플리케이션을 언제나 사용 가능하고, 성능이 뛰어나고, 안전하고, 복원력이 있게 유지하는 데 도움이 되는 플랫폼의 코어 기능을 알아야 할 것입니다. 이 문서의 목적이 바로 그것입니다. 이 문서는 리소스를 간결하게 구성하며 단일 데이터베이스 및 풀링된 데이터베이스로 SQL Database의 주요 기능을 사용하여 클라우드에서 애플리케이션이 효율적으로 실행되도록 관리 및 유지하고 최적의 결과를 달성하는 최선의 방법에 관한 몇 가지 지침을 제공합니다. 이 문서의 대표적인 대상 독자는 다음과 같은 작업을 수행하는 사용자입니다.

- Azure SQL Database로 애플리케이션 마이그레이션을 평가 중인 경우 - 애플리케이션 현대화.
- 애플리케이션을 마이그레이션하는 과정에 있는 경우 - 상시적인 마이그레이션 시나리오.
- 최근에 Azure SQL DB로 마이그레이션을 완료한 경우 - 클라우드의 새 DBA.

이 문서에서는 탄력적 풀에 풀링된 데이터베이스와 단일 데이터베이스로 작업할 때 쉽게 활용할 수 있는 플랫폼인 Azure SQL Database의 몇 가지 핵심 특징을 설명합니다. 다음과 같은 특성을 설명합니다.

- 비즈니스 연속성 및 재해 복구(BCDR)
- 보안 및 규정 준수
- 지능형 데이터베이스 모니터링 및 유지 관리
- 데이터 이동

> [!NOTE]
> 이 문서는 Azure SQL Database의 배포 옵션인 단일 데이터베이스 및 탄력적 풀에 적용됩니다. SQL Database의 관리되는 인스턴스 배포 옵션에는 적용되지 않습니다.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>비즈니스 연속성 및 재해 복구(BCDR)

비즈니스 연속성 및 재해 복구 기능을 사용하면 재해가 발생한 경우에도 비즈니스를 평소처럼 계속할 수 있습니다. 데이터베이스 수준 이벤트(예를 들어 누군가가 실수로 중요 테이블을 삭제한 경우) 또는 데이터 센터 수준 이벤트(지역의 재해, 예: 쓰나미) 등의 재해가 있을 수 있습니다.

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>SQL Database에서 백업을 만들고 관리하려면 어떻게 할까요?

Azure SQL DB에 백업을 만들지는 않으며 그럴 필요가 없습니다. SQL Database가 자동으로 데이터베이스를 백업해 주므로 더 이상 일정 계획, 백업 실행 및 관리를 걱정하지 않아도 됩니다. 플랫폼이 매주 전체 백업, 몇 시간마다 차등 백업 및 5분마다 로그 백업을 실행하여 재해 복구가 효율적으로 이루어지도록 하며 데이터 손실을 최소화합니다. 첫 번째 전체 백업은 데이터베이스를 생성하자마자 이루어집니다. 이러한 백업은 “보존 기간”이라고 하는 특정 기간 동안 사용할 수 있으며, 선택한 서비스 계층에 따라 달라집니다. SQL Database는 [지정 시간 복구(PITR)](sql-database-recovery-using-backups.md#point-in-time-restore)를 사용하여 이 보존 기간 이내의 특정 시점으로 복원하는 기능을 제공합니다.

|서비스 계층|보존 기간(일)|
|---|:---:|
|Basic|7|
|Standard|35|
|Premium|35|
|||

또한 [LTR(장기 보존)](sql-database-long-term-retention.md) 기능을 사용하면 훨씬 더 긴 기간(최대 10년) 동안 백업 파일을 보유하고 해당 기간 내의 어느 시점이든 이러한 백업에서 데이터를 복원할 수 있습니다. 뿐만 아니라 데이터베이스 백업은 지역 복제 스토리지에 보관되므로 지역 참사에서 복원력이 확보됩니다. 또한 이러한 백업을 보존 기간 내의 어느 시점이든 Azure 지역에서 복원할 수 있습니다. [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>데이터 센터 수준의 재해 또는 지역 참사 발생 시 비즈니스 연속성을 확인하려면 어떻게 할까요?

데이터베이스 백업은 지역 복제 스토리지 하위 시스템에 저장되므로 지역 재해가 발생한 경우 백업을 다른 Azure 지역으로 확실히 복원할 수 있습니다. 이 기능을 지리적 복원이라 합니다. 이 기능의 RPO(복구 지점 목표)는 일반적으로 1시간 미만이며 ERT(예상 복구 시간)는 수 분에서 수 시간까지입니다.

중요 업무용 데이터베이스의 경우 Azure SQL DB는 활성 지역 복제를 제공합니다. 이 기능이 수행하는 것은 기본적으로 다른 지역에 원본 데이터베이스의 지역 복제된 두 번째 복사본을 만드는 것입니다. 예를 들어 데이터베이스가 처음에 Azure 미국 서부 지역에서 호스팅되는데 지역 재해 복원력을 원한다고 가정해 보겠습니다. 이를 테면 미국 동부에 미국 서부 데이터베이스의 활성 지역 복제본을 만들었습니다. 미국 서부에서 재난이 발생한 경우 미국 동부 지역으로 장애 조치(failover)할 수 있습니다. 이러한 기능을 자동 장애 조치(failover) 그룹에 구성하면 재해가 발생한 경우 데이터베이스가 미국 동부의 두 번째 복사본으로 자동 장애 조치되므로 훨씬 더 유리합니다. 이 기능의 RPO는 5초 미만이며 ERT는 30초 미만입니다.

자동 장애 조치 그룹이 구성되지 않은 경우 애플리케이션은 재해에 관해 적극적으로 모니터링하고 보조에 대해 장애 조치를 시작해야 합니다. 그러한 활성 지역 복제본을 서로 다른 Azure 지역에 최대 4개까지 만들 수 있습니다. 물론 이렇게 하면 훨씬 더 낫습니다. 또한 읽기 전용 액세스를 위해 이러한 두 번째 활성 지역 복제본에 액세스할 수도 있습니다. 그러면 지리적으로 분산된 적용 시나리오에서 대기 시간을 줄이기에 매우 편리합니다.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>재해 복구 플랜이 온-프레미스에서 SQL Database로 어떻게 변경되나요?

요약해서, 기존 온-프레미스 SQL Server 설정에서는 비즈니스 연속성을 보장하기 위해 장애 조치(failover) 클러스터링, 데이터베이스 미러링, 트랜잭션 복제, 로그 전달 등과 같은 기능을 사용하여 적극적으로 가용성을 관리하고 백업을 유지 및 관리해야 했습니다. SQL Database를 사용하면 플랫폼이 이러한 작업을 모두 관리하므로 사용자는 데이터베이스 애플리케이션 개발 및 최적화에 집중할 수 있으며 재해 관리에 대해서는 크게 걱정하지 않아도 됩니다. 사용자는 Azure Portal에서 단 몇 번의 클릭(또는 PowerShell API를 사용하여 몇 가지 명령)으로 백업 및 재해 복구 계획을 구성하고 작동하도록 할 수 있습니다.

재해 복구에 대한 자세한 내용은: [Azure SQL DB 재해 복구 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)을 참조하세요.

## <a name="security-and-compliance"></a>보안 및 규정 준수

SQL Database는 보안 및 개인 정보 보호를 매우 중대하게 실행합니다. SQL Database의 보안은 데이터베이스 수준 및 플랫폼 수준에서 사용할 수 있으며 여러 레이어로 분류된 경우 가장 이해하기 쉽습니다. 각 레이어에서 용도에 맞는 최적의 보안을 제어 및 제공합니다. 레이어는 다음과 같습니다.

- ID 및 인증([Windows/SQL 인증 및 AAD[Azure Active Directory] 인증](sql-database-control-access.md)).
- 활동 모니터링([감가](sql-database-auditing.md) 및 [위협 검색](sql-database-threat-detection.md)).
- 실제 데이터 보호([TDE[투명한 데이터 암호화]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) 및 [AE[Always Encrypted]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- 중요 및 권한 있는 데이터에 대한 액세스 제어([행 수준 보안](/sql/relational-databases/security/row-level-security) 및 [동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking)).

[Azure Security Center](https://azure.microsoft.com/services/security-center/)는 Azure, 온-프레미스 및 기타 클라우드에서 실행되는 작업 전반에 걸친 중앙 집중식 보안 관리를 제공합니다. [감사](sql-database-auditing.md) 및 [TDE[투명한 데이터 암호화]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)와 같은 필수 SQL Database 보호가 모든 리소스에 구성되었는지 보고, 고유의 요구 사항에 따라 정책을 만들 수 있습니다.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>어떤 사용자 인증 방법이 SQL Database에서 제공되나요?

SQL Database에서 [두 가지 사용자 인증 방법](sql-database-control-access.md#authentication)이 제공됩니다.

- [Azure Active Directory 인증](sql-database-aad-authentication.md)
- SQL 인증

기존 Windows 인증은 지원되지 않습니다. Azure Active Directory(AD)는 중앙 집중식 ID 및 액세스 관리 서비스입니다. 이 서비스를 사용하면 조직의 모든 인원에게 SSO(Single Sign-On)를 아주 편리하게 제공할 수 있습니다. 즉, 더 간단한 인증을 위해 자격 증명이 모든 Azure 서비스에 걸쳐 공유됩니다. AAD는 [MFA(Multi-Factor Authentication)](sql-database-ssms-mfa-authentication.md)을 지원하며 [클릭 몇 번](../active-directory/hybrid/how-to-connect-install-express.md)으로 AAD를 Windows Server Active Directory와 통합할 수 있습니다. SQL 인증은 과거에 사용하던 것과 똑같이 작동합니다. 즉, 사용자 이름/암호를 제공하고 사용자를 지정된 SQL Database 서버의 임의 데이터베이스에 대해 인증할 수 있습니다. 또한 Microsoft Azure SQL Database 및 SQL Data Warehouse에서 Azure AD 도메인 내에서 다단계 인증 및 게스트 사용자 계정을 제공할 수 있습니다. 이미 Active Directory 온-프레미스가 있는 경우, Azure Active Directory로 디렉터리를 페더레이션하여 디렉터리를 Azure로 확장할 수 있습니다.

|**다음과 같은 경우...**|**SQL Database / SQL Data Warehouse**|
|---|---|
|Azure에서 Azure Active Directory(AD)를 사용하지 않는 것을 선호함|[SQL 인증](sql-database-security-overview.md) 사용|
|AD를 SQL Server 온-프레미스에 사용함|[AD를 Microsoft Azure Active Directory와 페더레이션](../active-directory/hybrid/whatis-hybrid-identity.md)하고, Azure AD 인증을 사용합니다. 이런 경우에 Single Sign-On을 사용할 수 있습니다.|
|다단계 인증(MFA)을 강제해야 합니다.|MFA를 [Microsoft Conditional Access](sql-database-conditional-access.md)를 통한 정책으로 필요하며 [MFA를 지원하는 Azure AD Universal 인증](sql-database-ssms-mfa-authentication.md)을 사용합니다.|
|Microsoft 계정(live.com, outlook.com) 또는 다른 도메인(gmail.com)의 게스트 계정이 있습니다.|Microsoft Azure SQL Database/Data Warehouse에서 [Azure AD Universal 인증](sql-database-ssms-mfa-authentication.md)을 사용하며, 이는 [Azure AD B2B Collaboration](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)을 활용합니다.|
|페더레이션된 도메인에서 Microsoft Azure Active Directory 자격 증명을 사용하여 Windows에 로그인|[Azure AD 통합 인증](sql-database-aad-authentication-configure.md)을 사용합니다.|
|Azure와 페더레이션되지 않은 도메인에서 자격 증명을 사용하여 Windows에 로그인|[Azure AD 통합 인증](sql-database-aad-authentication-configure.md)을 사용합니다.|
|SQL Database 또는 SQL Data Warehouse에 연결해야 하는 중간 계층 서비스 포함|[Azure AD 통합 인증](sql-database-aad-authentication-configure.md)을 사용합니다.|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>내 데이터베이스에 대한 연결 액세스를 제한 또는 제어하려면 어떻게 할까요?

용도에 맞는 최적의 연결 구성을 획득하기 위해 원하는 대로 사용할 수 있는 여러 기술이 있습니다.

- 방화벽 규칙
- VNet 서비스 엔드포인트
- 예약된 IP

#### <a name="firewall"></a>방화벽

방화벽은 특정 엔터티에 대해서만 SQL Database 서버에 액세스를 허용하여 외부 엔터티에서의 서버 액세스를 방지합니다. 기본적으로 다른 Azure 서비스의 연결을 제외하고 SQL Database 서버 내의 모든 연결 및 데이터베이스는 허용되지 않습니다. 방화벽 규칙을 사용하여 방화벽을 통해 개발자 컴퓨터의 IP 주소를 허용하면 직접 승인하는 엔터티(예: 개발자 머신)에 대해서만 서버 액세스를 개방할 수 있습니다. 또한 SQL Database 서버에 대한 액세스를 허용할 IP 범위를 지정할 수도 있습니다. 예를 들어 방화벽 설정 페이지에서 범위를 지정하여 조직의 개발자 머신 IP 주소들을 동시에 추가할 수 있습니다.

서버 및 데이터베이스 수준의 방화벽 규칙을 만들 수 있습니다. Azure Portal 또는 SSMS를 사용하여 서버 수준 IP 방화벽 규칙을 만들 수 있습니다. 서버 수준 및 데이터베이스 수준 방화벽 규칙을 설정하는 방법에 대한 자세한 내용은 [SQL Database에서 IP 방화벽 규칙 만들기](sql-database-security-tutorial.md#create-firewall-rules)를 참조하세요.

#### <a name="service-endpoints"></a>서비스 엔드포인트

기본적으로 SQL 데이터베이스는 "Azure 서비스의 서버 액세스 허용"으로 구성되며, 이는 Azure의 모든 가상 머신에서 데이터베이스에 연결하려고 시도한다는 것을 의미합니다. 이러한 시도는 여전히 인증을 받아야 합니다. 그러나 모든 Azure IP에서 데이터베이스에 액세스하지 않도록 하려면 "Azure 서비스의 서버 액세스 허용"을 사용하지 않도록 설정할 수 있습니다. 또한 [VNet 서비스 엔드포인트](sql-database-vnet-service-endpoint-rule-overview.md)를 구성할 수 있습니다.

서비스 엔드포인트(SE)를 사용하면 중요 Azure 자원을 Azure의 자신의 개인 가상 네트워크에 대해서만 노출할 수 있습니다. 그렇게 하면 기본적으로 사용자의 리소스에 대한 공용 액세스가 제거됩니다. 가상 네트워크와 Azure 간의 트래픽은 Azure 백본 네트워크에서 유지됩니다. SE가 없으면 강제 적용 터널링 패킷 라우팅을 하게 됩니다. 사용자의 가상 네트워크는 인터넷 트래픽을 강제로 사용자의 조직으로 이동하며 Azure 서비스 트래픽은 같은 경로를 통해 이동합니다. 서비스 엔드포인트를 사용하면 패킷이 사용자의 가상 네트워크에서 Azure 백본 네트워크의 서비스로 곧장 흐르므로 이 작업을 최적화할 수 있습니다.

![VNet 서비스 엔드포인트](./media/sql-database-manage-after-migration/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>예약된 IP

다른 옵션은 VM에 대해 [예약된 IP](../virtual-network/virtual-networks-reserved-public-ip.md)를 프로비전하고, 서버 방화벽 설정에 있는 특정 VM IP 주소를 허용목록에 넣습니다. 예약된 IP를 할당하면 IP 주소 변경에 따라 방화벽 규칙을 직접 업데이트해야 할 필요가 없습니다.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>어떤 포트를 통해 SQL Database에 연결하나요?

포트 1433입니다. SQL Database는 이 포트를 통해 통신합니다. 회사 네트워크 내에서 연결하려면 조직의 방화벽 설정에 아웃바운드 규칙을 추가해야 합니다. 참고로 포트 1433을 Azure 경계 외부에 노출하지 마세요. [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps)을 사용하여 Azure에서 SSMS를 실행할 수 있습니다. 포트 1433에 대한 나가는 연결을 시작할 필요가 없습니다. IP가 고정적이라 DB가 RemoteApp에만 열릴 수 있으며, MFA(다단계 인증)을 지원합니다.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>SQL Database에서 내 서버 및 데이터베이스의 활동을 어떻게 모니터링 및 조절할 수 있나요?

#### <a name="sql-database-auditing"></a>SQL Database 감사

SQL Database를 사용하면 감사를 켜서 데이터베이스 이벤트를 추적할 수 있습니다. [QL Database 감사](sql-database-auditing.md)는 데이터베이스 이벤트를 기록하고 Azure Storage 계정의 감사 로그 파일에 이를 기록합니다. 감사는 잠재적 보안 및 정책 위반 정보를 얻고 규정 준수 등을 유지하고자 하는 경우 특히 유용합니다. 즉, 감사가 필요하다고 생각하는 이벤트의 특정 범주를 정의 및 구성하고 이를 기반으로 미리 구성된 보고서 및 대시보드를 가져와서 데이터베이스에서 발생하는 이벤트의 개요를 파악할 수 있습니다. 이러한 감사 정책을 데이터베이스 수준 또는 서버 수준에서 적용할 수 있습니다. 서버/데이터베이스에 대해 감사를 켜는 방법에 대한 지침은: [SQL Database 감사 사용](sql-database-security-tutorial.md#enable-security-features)을 참조하세요.

#### <a name="threat-detection"></a>위협 감지

[위협 감지](sql-database-threat-detection.md)를 사용하여 감사에 의해 감지된 보안 또는 정책 위반에 대해 매우 쉽게 행동하는 기능을 얻습니다. 시스템의 잠재적 위협 또는 위반을 해결하기 위해 보안 전문가가 될 필요가 없습니다. 위협 감지는 SQL 삽입 감지 같은 몇 가지 기본 제공 기능도 포함합니다. SQL 삽입은 데이터 변경 또는 훼손 시도이며 일반적으로 데이터베이스 애플리케이션을 공격할 때 아주 많이 사용되는 방법입니다. 위협 탐지는 잠재적인 취약점 및 SQL 삽입 공격뿐만 아니라 비정상 데이터베이스 액세스 패턴(예: 비정상적인 위치에서나 알 수 없는 보안 주체의 액세스)을 탐지하는 여러 알고리즘 세트를 실행합니다. 보안 책임자 또는 지정된 다른 관리자는 데이터베이스에서 위협이 감지되면 전자 메일 알림을 받게 됩니다. 각 알림에서는 의심스러운 활동에 대한 세부 정보와 해당 위협을 자세히 조사하고 완화하는 방법에 대한 권장 사항을 제공합니다. 위협 탐지를 켜는 방법에 대해 알아보려면: [위협 탐지 사용](sql-database-security-tutorial.md#enable-security-features)을 참조하세요.

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>일반적으로 SQL Database에서 내 데이터를 보호하려면 어떻게 할까요?

암호화는 중요 데이터를 침입자로부터 보호하고 안전하게 지키는 강력한 메커니즘을 제공합니다. 암호화된 데이터는 암호 해독 키가 없으면 침입자에게 아무 소용이 없습니다. 그러므로 SQL Database에 기본 제공되는 기존 보안 레이어의 맨 위에 추가 보호 레이어를 추가합니다. SQL Database의 데이터 보호에는 두 가지 측면이 있습니다.

- 데이터 및 로그 파일의 미사용 데이터
- 이동 중인 데이터.

SQL Database에서 기본적으로 저장소 하위 시스템에 있는 데이터 및 로그 파일의 미사용 데이터는 [TDE[투명한 데이터 암호화]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 통해 완전하게 항상 암호화됩니다. 백업도 암호화됩니다. TDE를 사용하면 이 데이터에 액세스 중인 애플리케이션 쪽에서 변경이 필요하지 않습니다. 암호화 및 암호 해독은 투명하게 이루어지며, 이름도 마찬가지입니다.
이동 중 및 미사용 중요 데이터를 보호하기 위해 SQL Database는 [AE(Always Encrypted)](/sql/relational-databases/security/encryption/always-encrypted-database-engine)라는 기능을 제공합니다. AE는 데이터베이스의 중요한 열을 암호화하는 클라이언트 쪽 암호화의 양식입니다. (따라서 데이터베이스 관리자와 권한이 없는 사용자에게 그것은 ciphertext에 있습니다.) 서버는 시작하려면 암호화된 데이터를 수신합니다. Always Encrypted를 위한 키는 클라이언트 쪽에도 저장되어 권한 있는 클라이언트만이 중요한 열을 암호 해독할 수 있습니다. 암호화 키가 클라이언트에 저장되기 때문에 서버 관리자 및 데이터 관리자는 중요한 데이터를 볼 수 없습니다. AE는 권한이 없는 클라이언트부터 실제 디스크에 이르기까지, 테이블 종단 간에 중요한 열을 암호화합니다. AE는 같음 비교를 지원하므로 DBA는 계속해서 암호화된 열을 해당 SQL 명령의 일환으로 쿼리할 수 있습니다. Always Encrypted는 [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), Windows 인증서 저장소, 및 로컬 하드웨어 보안 모듈과 같은 다양한 키 저장소 옵션과 함께 사용될 수 있습니다.

|**특성**|**Always Encrypted**|**투명한 데이터 암호화**|
|---|---|---|
|**암호화 범위**|종단간|미사용 데이터|
|**데이터베이스 서버는 중요 데이터에 액세스 가능**|아닙니다.|예, 암호화는 미사용 데이터를 위한 것이므로|
|**허용되는 T-SQL 작업**|같음 비교|모든 T-SQL 노출 영역을 사용할 수 있음|
|**기능을 사용하려면 앱 변경이 필요함**|최소|아주 미미함|
|**암호화 세분성**|열 수준|데이터베이스 수준|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>데이터베이스 내의 중요한 데이터에 대한 액세스를 어떻게 제한할 수 있나요?

모든 애플리케이션은 데이터베이스에 아무나 볼 수 없도록 보호해야 하는 중요 데이터를 어느 정도 가지고 있습니다. 조직의 특정 인원은 이 데이터를 보아야 하지만, 다른 인원은 이 데이터를 볼 수 없어야 합니다. 한 예로 직원의 임금이 있습니다. 관리자는 자신의 직접 보고서의 임금 정보에 액세스해야 할 수 있지만, 개인 팀원은 자신의 동료의 임금 정보에 대한 액세스 권한이 없어야 합니다. 다른 시나리오로 개발 단계 또는 시험 중에 중요 데이터(예: 고객의 SSN)로 상호 작용할 수 있는 데이터 개발자가 있습니다. 이 정보 역시 개발자에게 노출되지 않아야 합니다. 그러한 경우 중요 데이터를 마스킹하거나 전혀 노출하지 않아야 합니다. SQL Database는 권한이 없는 사용자가 중요한 데이터를 보지 못하도록 방지하기 위한 몇 가지 방법을 제공합니다.

[동적 데이터 마스킹](sql-database-dynamic-data-masking-get-started.md)은 애플리케이션 레이어에서 권한이 없는 사용자로 마스킹하여 중요한 데이터 노출을 제한할 수 있도록 하는 데이터 마스킹 기능입니다. 마스킹 패턴(예를 들어, SSN: XXX-XX-0000과 같이 국적 ID의 마지막 4자리만을 보여주고 나머지를 X로 표시함)을 만들 수 있는 마스킹 규칙을 정의하고 마스킹 규칙에서 어떤 사용자를 제외해야 할지 식별합니다. 마스킹은 즉시 이루어지며 여러 데이터 범주에 사용할 수 있는 다양한 마스킹 기능이 있습니다. 동적 데이터 마스킹을 사용하면 데이터베이스에서 자동으로 중요 데이터를 검색하고 해당 데이터에 마스킹을 적용합니다.

[행 수준 보안](/sql/relational-databases/security/row-level-security)을 사용하면 행 수준에서 액세스를 제어할 수 있습니다. 즉, 쿼리를 실행하는 사용자(그룹 멤버 자격 또는 실행 컨텍스트)에 기초하여 데이터베이스 테이블의 특정 행을 숨깁니다. 액세스 제한은 앱 논리를 간소화하기 위해 애플리케이션 계층 대신에 데이터베이스 계층에서 수행됩니다. 먼저 필터 조건자를 만들고 노출되지 않는 행 및 보안 정책을 필터링한 다음 이러한 행에 액세스 권한을 가진 사용자를 정의합니다. 마지막으로 최종 사용자가 자신의 쿼리를 실행하면 사용자의 권한에 따라 제한된 행을 보거나 해당 행을 전혀 볼 수 없습니다.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>클라우드의 암호화 키를 관리하려면 어떻게 할까요?

Always Encrypted (클라이언트 쪽 암호화) 및 투명한 데이터 암호화 (휴지 상태의 암호화)에 대한 키 관리 옵션이 있습니다. 암호화 키를 정기적으로 회전하는 것이 좋습니다. 회전 빈도는 내부 조직 규정은 물론 규정 요구 사항과도 일치해야 합니다.

#### <a name="transparent-data-encryption-tde"></a>TDE(투명한 데이터 암호화)

TDE에는 두 키 계층이 있습니다 – 각 사용자 데이터베이스의 데이터는 대칭 AES-256 데이터베이스 고유 DEK(데이터베이스 암호화 키)로 암호화되고, 이 키는 서버 고유 비대칭 RSA 2048 마스터 키로 암호화됩니다. 마스터 키를 다음 방법으로 관리할 수 있습니다.

- 플랫폼에 의해 자동으로 - SQL Database.
- 또는 [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)를 키 저장소로 사용하여.

기본적으로 투명한 데이터 암호화를 위한 마스터 키는 편의를 위해 Microsoft Azure SQL Database 서비스에서 관리합니다. 조직에서 마스터 키를 제어하고자 하는 경우, Azure Key Vault(sql-database-always-encrypted-azure-key-vault.md)를 키 저장소로 사용하는 옵션이 있습니다. Azure Key Vault를 사용하여 조직은 키 프로비저닝, 회전 및 권한 제어에 관한 제어권을 갖습니다. [회전 또는 TDE 마스터 키 형식 전환](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation)은 DEK을 다시 암호화함으로 빠릅니다. 보안과 데이터 관리 간의 역할 구분이 있는 조직인 경우, 보안 관리자는 Azure Key Vault에서 TDE 마스터 키에 대한 핵심 자료를 프로비전하고, 서버에서 휴지 상태의 암호화를 사용하도록 데이터베이스 관리자에게 Azure Key Vault 키 식별자를 제공합니다. Key Vault는 Microsoft에서 암호화 키를 보거나 추출할 수 없게 설계되어 있습니다. 조직에 대한 중앙 집중식 키 관리도 얻게 됩니다.

#### <a name="always-encrypted"></a>상시 암호화

또한 Always Encrypted에는 [두 키 계층 구조](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted)가 있습니다. 중요한 데이터의 열은 AES 256 CEK(열 암호화 키)로 암호화되고, 이 키는 다시 CMK(열 마스터 키)로 암호화됩니다. Always Encrypted에 대해 제공된 클라이언트 드라이버는 CMK 길이에 제한이 없습니다. 암호화된 CEK 값은 데이터베이스에 저장되고, CMK는 Windows Certificate Store, Azure Key Vault 또는 하드웨어 보안 모듈 등과 같은 신뢰할 수 있는 키 저장소에 저장됩니다.

- [CEK와 CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell)는 모두 회전할 수 있습니다.
- CEK 회전은 데이터 작업 크기이며 암호화된 열을 포함하는 테이블의 크기에 따라 시간이 많이 걸릴 수 있습니다. 따라서 CEK 회전을 그에 따라 계획하는 것이 좋습니다.
- 그러나 CMK 회전은 데이터베이스 성능을 방해하지 않으며, 분리된 역할과 함께 수행될 수 있습니다.

다음 다이어그램은 Always Encrypted에서 열 마스터 키에 대한 키 저장소 옵션을 보여줍니다.

![Always Encrypted CMK 저장소 공급자](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>조직과 SQL Database 간의 트래픽을 어떻게 최적화 및 보호할 수 있나요?

조직과 SQL Database 간의 네트워크 트래픽은 일반적으로 공용 네트워크를 통해 경로 설정됩니다. 그러나 이 경로를 최적화하도록 선택하고 더 안전하게 만들면 Express Route를 확인할 수 있습니다. 기본적으로 Express 경로를 사용하면 회사 네트워크를 개인 연결의 Azure 플랫폼으로 확장할 수 있습니다. 그렇게 하면 공용 인터넷을 통해 이동하지 않게 됩니다. 또한 더 높은 보안, 신뢰성 및 경로 설정 최적화를 얻게 되므로 공용 인터넷 네트워크를 통한 일반적인 환경보다 대기 시간이 감소하고 속도가 훨씬 더 빠릅니다. 조직과 Azure 간에 중요 데이터 청크를 전송하도록 계획하는 경우 Express Route를 사용하면 비용 면에서 유리할 수 있습니다. 조직에서 Azure로 연결하기 위한 세 가지 연결 모델 중에서 선택할 수 있습니다.

- [클라우드 교환 공동 배치](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [보편적 연결(Any-to-Any)](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [지점 간](../expressroute/expressroute-connectivity-models.md#Ethernet)

또한 Express Route를 사용하면 추가 요금 없이 구입하는 대역폭 제한을 최대 2배까지 증가시킬 수 있습니다. 또한 기본 경로를 사용하여 크로스 영역 연결을 구성하는 것도 가능합니다. ER 연결 공급자 목록을 보려면: [Express 경로 파트너 및 피어링 위치](../expressroute/expressroute-locations.md)를 참조하세요. 다음 문서에서 Express Route를 자세히 설명합니다.

- [기본 경로 소개](../expressroute/expressroute-introduction.md)
- [필수 구성 요소](../expressroute/expressroute-prerequisites.md)
- [워크플로](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>SQL Database가 규정 요구 사항을 준수하나요? 그리고 이것이 조직의 규정 준수에 어떤 도움이 되나요?

SQL Database는 다양한 규격을 준수합니다. 최신 SQL Database에서 충족 된 규격 집합을 보려면 합니다 [Microsoft 보안 센터](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 및 SQL Database가 되는 준수를 포함 하는 경우 참조 하기 위해 조직에 중요 한 규정 준수에 대해 드릴 다운 Azure 서비스입니다. SQL Database가 규격 서비스로 인증된다 하더라도, 조직 서비스의 준수를 지원하지만 자동으로 보장하지는 않는다는 점을 확인하는 것이 중요합니다.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>마이그레이션 후 지능형 데이터베이스 모니터링 및 유지 관리

데이터베이스를 SQL Database로 마이그레이션한 후 데이터베이스를 모너티링하거나(예: 리소스 사용률이 얼마나 유사한지 또는 DBCC CHECK인지 확인) 정기 유지 관리를 수행하려고 할 것입니다(예를 들어 인덱스, 통계 등을 다시 빌드 또는 재구성). 다행히도 SQL Database는 기록 추세 및 기록된 메트릭과 통계를 사용하여 사전에 애플리케이션이 언제나 최적으로 실행되도록 데이터베이스를 모니터링 및 유지 관리하도록 도와 주는 감각에서 지능적입니다. 경우에 따라 Azure SQL DB는 구성 설정에 따라 자동으로 유지 관리 작업을 수행합니다. SQL Database에서 데이터베이스 모니터링에는 세 가지 패싯이 있습니다.

- 성능 모니터링 및 최적화.
- 보안 최적화.
- 비용 최적화.

### <a name="performance-monitoring-and-optimization"></a>성능 모니터링 및 최적화

Query Performance Insights를 사용하면 애플리케이션이 언제나 최적의 수준으로 실행을 유지할 수 있도록 데이터베이스 워크로드에 대해 조정된 권장 사항을 얻을 수 있습니다. 또한 이러한 권장 사항이 자동으로 적용되도록 설정하여 유지 관리 작업을 수행하는 번거로움을 덜 수도 있습니다. Index Advisor를 사용하면 워크로드에 기초하여 인덱스 권장 사항을 자동으로 구현할 수 있으며, 이 기능을 자동 조정이라 합니다. 권장 사항은 가장 유용한 권장 사항을 제공하기 위해 애플리케이션 워크로드가 변화함에 따라 변화합니다. 또한 이러한 권장 사항을 수동으로 검토하고 원하는 대로 적용하는 옵션이 있습니다.  

### <a name="security-optimization"></a>보안 최적화

SQL Database는 데이터를 보호하도록 도와주는 조치 가능 보안 권장 사항 및 데이터베이스에 잠재적 스레드를 노출할 수 있는 의심스러운 데이터베이스 활동을 식별 및 조사하기 위한 위협 탐지를 제공합니다. [취약성 평가](sql-vulnerability-assessment.md)는 데이터베이스의 보안 상태를 대규모로 모니터링하고, 보안 위험을 파악하고, 사용자가 정의한 보안 기준에서 드리프트할 수 있는 데이터베이스 검사 및 보고 서비스입니다. 모든 검사 후 실행 가능한 단계 및 재구성 스크립트의 사용자 지정 목록이 규정 준수 요구 사항을 충족하는 데 사용할 수 있는 평가 보고서와 함께 제공됩니다.

Azure Security Center를 사용하여 한 번의 클릭으로 보드에서 보안 권장 사항을 식별하고 적용합니다.

### <a name="cost-optimization"></a>비용 최적화

Azure SQL 플랫폼은 서버의 데이터베이스에 대해 사용률 기록을 분석하여 비용 최적화 옵션을 평가하고 권고합니다. 이 분석은 일반적으로 조치 가능 권장 사항을 분석하고 구축하는 데 2주일이 걸립니다. 탄력적 풀은 그러한 옵션의 하나입니다. 권장 사항은 포털에 배너로 나타납니다.

![탄력적 풀 권장 사항](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png)

또한 “Advisor” 섹션 아래에서 이 분석을 볼 수도 있습니다.

![탄력적 풀 권장 사항 - 관리자](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>SQL Database에서 성능 및 리소스 사용률을 모니터링하려면 어떻게 할까요?

SQL Database에서는 플랫폼의 지능적인 정보를 활용하여 성능을 모니터링하고 그에 따라 조정할 수 있습니다. 다음 방법을 사용하여 SQL Database에서 성능 및 리소스 사용률을 모니터링할 수 있습니다.

#### <a name="azure-portal"></a>Azure portal

Azure Portal은 개요 창에서 데이터베이스를 선택하고 차트를 클릭하면 데이터베이스의 사용률을 보여 줍니다. CPU 비율, DTU 비율, 데이터 IO 비율, 세션 비율 및 데이터베이스 크기 비율을 포함하는, 여러 가지 메트릭을 표시하도록 차트를 수정할 수 있습니다.

![모니터링 차트](./media/sql-database-manage-after-migration/monitoring-chart.png)

![모니터링 차트 2](./media/sql-database-manage-after-migration/chart.png)

또한 이 차트로부터 리소스로 경고를 구성할 수도 있습니다. 이러한 경고를 통해 사용자는 전자 메일로 리소스 조건에 대응하거나 HTTPS/HTTP 엔드포인트에 쓰거나 작업을 수행할 수 있습니다. 자세한 내용은 [경고 만들기](sql-database-insights-alerts-portal.md)를 참조하세요.

#### <a name="dynamic-management-views"></a>동적 관리 뷰

지난 1시간 동안의 리소스 소비 통계 기록을 반환하려면 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 동적 관리 뷰를, 지난 14일에 대한 기록을 반환하려면 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 시스템 카탈로그 뷰를 쿼리하면 됩니다.

#### <a name="query-performance-insight"></a>쿼리

[Query Performance Insight](sql-database-query-performance.md)를 사용하면 상위 리소스 소비량 쿼리 및 특정 데이터베이스에 대한 장기 실행 쿼리 기록을 볼 수 있습니다. 리소스 사용률, 기간 및 실행 빈도별로 최상위 쿼리를 신속하게 식별할 수 있습니다. 쿼리를 추적하고 재발을 검색할 수 있습니다. 이 기능을 사용하려면 [쿼리 저장소](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)가 데이터베이스에 대해 사용할 수 있도록 설정되고 활성화되어야 합니다.

![쿼리](./media/sql-database-manage-after-migration/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Azure Monitor 로그에서 azure SQL 분석 (미리 보기)

[Azure Monitor 로그](../azure-monitor/insights/azure-sql.md) 150,000 SQL Database 및 5,000 SQL 탄력적 풀 작업 영역당 최대 지원 수집 하 고 주요 Azure SQL Azure 성능 메트릭을 시각화할 수 있습니다. 그것을 사용하여 알림을 모니터링하고 받을 수 있습니다. 여러 Azure 구독 및 탄력적 풀에서 SQL Database 및 탄력적 풀 메트릭을 모니터링할 수 있으며 애플리케이션 스택의 각 레이어에서 문제를 식별하는 데 사용할 수 있습니다.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>성능 문제를 발견했습니다. SQL Database 문제 해결 방법은 SQL Server와는 어떻게 다른가요?

문제 해결 기술의 주요 부분은 진단 쿼리에 사용되며 데이터베이스 성능 문제는 동일하게 유지됩니다. 결국 동일한 SQL Server 엔진이 클라우드를 구동합니다. 그러나 플랫폼, 즉 Azure SQL DB가 기본 제공 '지능'을 포함합니다. 이는 성능 문제를 훨씬 더 쉽게 문제 해결 및 진단하는 데 도움이 될 수 있습니다. 또한 사용자를 대신하여 이러한 정정 작업 중 일부를 수행할 수 있으며, 경우에 따라 사전에 자동 해결할 수 있습니다.

사용자의 성능 문제 해결 방법은 [QCP(Query Performance Insight)](sql-database-query-performance.md) 및 [Database Advisor](sql-database-advisor.md)를 함께 사용하면 상당히 유리할 수 있으므로 방법의 차이점은 이러한 점에서 서로 다릅니다. - 즉, 문제를 직접 해결하는 데 도움이 되는 필수 세부 정보를 찾아내는 수작업이 더 이상 필요 없습니다. 플랫폼이 어려운 작업을 모두 수행해 줍니다. 그러한 기능의 한 예로 QPI가 있습니다. QPI를 사용하면 모든 사항을 쿼리 수준까지 집중 분석하여 기록 추세를 확인하고 쿼리가 회귀되는 정확한 시기를 파악할 수 있습니다. Database Advisor는 누락된 인덱스, 인덱스 삭제, 쿼리 매개 변수화 등 일반적으로 전반적인 성능을 개선하는 데 도움이 될 수 있는 사항에 관한 권장 사항을 제공합니다.

성능 문제 해결의 경우 애플리케이션 성능에 영향을 주는 것이 문제 해결을 지원하는 애플리케이션 또는 데이터베이스뿐인지 여부를 식별하는 것이 중요합니다. 성능 문제는 애플리케이션 레이어에 있는 경우가 많습니다. 즉, 아키텍처 또는 데이터 액세스 패턴이 문제일 수 있습니다. 예를 들어 네트워크 대기 시간에 중요한 대화 애플리케이션이 있다고 생각해 보겠습니다. 이 경우 애플리케이션은 애플리케이션과 서버 사이를 오가는 짧은 요청("대화")이 많이 있고 혼잡한 네트워크에서는 이러한 왕복이 빠르게 증가하므로 문제가 발생합니다. 이 경우 성능을 개선하려면 [일괄 쿼리](sql-database-performance-guidance.md#batch-queries)를 사용할 수 있습니다. 일괄 처리를 사용하면 이제 요청이 일괄 처리되므로 크게 도움이 되며, 따라서 왕복 대기 시간을 줄이고 애플리케이션 성능을 개선하는 데 도움이 됩니다.

또한 데이터베이스의 전반적인 성능이 저하되는 것을 발견한 경우 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 및 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 동적 관리 뷰를 모니터링하여 CPU, IO 및 메모리 사용을 이해할 수 있습니다. 데이터베이스가 사용할 리소스가 부족하여 성능에 영향을 줄 수 있습니다. 증가하고 줄어드는 워크로드 수요에 따라 계산 크기 및/또는 서비스 계층을 변경해야 할 수 있습니다.

성능 문제 조정에 대한 포괄적인 권장 사항은: [데이터베이스 조정](sql-database-performance-guidance.md#tune-your-database)을 참조하세요.

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>적절한 서비스 계층 및 컴퓨팅 크기를 사용하고 있는지 확인하려면 어떻게 할까요?

SQL Database는 여러 가지 서비스 계층, 즉, Basic, Standard, Premium을 제공합니다. 각 서비스 계층은 해당 서비스 계층과 연결된 예측 가능한 성능을 보장합니다. 워크로드에 따라 리소스 사용률이 현재 사용 중인 계산 크기의 한계에 도달할 수 있는 활동이 폭발적으로 발생할 수 있습니다. 그러한 경우 먼저 조정(예: 인덱스 추가 또는 변경 등)이 도움이 될 수 있는지 평가하는 것이 좋습니다. 제한 문제가 여전히 발생하는 경우 더 높은 수준의 서비스 계층 또는 계산 크기로 전환하는 것이 좋습니다.

|**서비스 계층**|**일반적인 사용 사례 시나리오**|
|---|---|
|**Basic**|소수의 사용자 및 높은 동시성, 배율 및 성능 요구 사항이 없는 데이터베이스를 가진 애플리케이션입니다. |
|**Standard**|낮음에서 중간 정도의 IO 수요와 함께 상당한 동시성, 배율 및 성능 요구 사항을 가진 애플리케이션입니다. |
|**Premium**|많은 동시 사용자, 높은 CPU/메모리 및 높은 IO 수요를 가진 애플리케이션입니다. 높은 동시성, 높은 처리량 및 대기 시간에 민감한 앱은 Premium 수준을 이용할 수 있습니다. |
|||

적절한 계산 크기에 있는지 확인하려면 위의 "SQL Database에서 성능 및 리소스 사용률을 모니터링하려면 어떻게 해야 하나요?"에서 설명한 방법 중 하나를 통해 쿼리 및 데이터베이스 리소스 사용을 모니터링할 수 있습니다. 쿼리/데이터베이스가 CPU/메모리 등에서 일관되게 많이 실행되고 있다고 확인되면 더 큰 계산 크기로 강화하도록 고려할 수 있습니다. 마찬가지로, 최고 사용량 시간에도 리소스를 그리 많이 사용하지 않는 것으로 확인되면 현재 계산 크기에서 축소하는 것이 좋습니다.

SaaS 앱 패턴 또는 데이터베이스 통합 시나리오가 있으면 비용 최적화를 위해 탄력적 풀을 사용하는 것이 좋습니다. 탄력적 풀은 데이터베이스 통합 및 비용 최적화를 달성하는 훌륭한 방법입니다. 탄력적 풀을 사용한 다중 데이터베이스 관리에 대해 더 자세히 읽으려면: [풀 및 데이터베이스 관리](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)를 참조하세요.

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>내 데이터베이스에 대해 데이터베이스 무결성 검사를 얼마나 자주 실행해야 하나요?

SQL Database는 특정 부류의 데이터 손상을 자동으로 데이터 손실 없이 처리할 수 있는 몇 가지 스마트 기술을 사용합니다. 이러한 기법은 서비스에 기본 제공되며 필요한 경우 서비스에 의해 활용됩니다. 정기적으로 서비스에 대한 데이터베이스 백업을 복원하고 그에 대해 DBCC CHECKDB를 실행하여 테스트합니다. 문제가 있으면 SQL Database가 미리 해결합니다. [자동 페이지 수리](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)는 손상되거나 데이터 무결성 문제가 있는 페이지를 수정하기 위해 이용됩니다. 데이터베이스 페이지는 언제나 페이지의 무결성을 확인하는 기본 체크섬 설정을 사용하여 확인됩니다. SQL Database는 데이터베이스의 데이터 무결성을 사전에 모니터링하고 검토하며 문제가 발생하면 가장 높은 우선 순위로 해결합니다. 이에 더하여 원한다면 선택적으로 사용자 자신의 무결성 검사를 실행하도록 선택할 수 있습니다.  자세한 내용은 [SQL Database의 데이터 무결성](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) 참조

## <a name="data-movement-after-migration"></a>마이그레이션 후 데이터 이동

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>SQL Database에서 데이터를 BACPAC 파일로 내보내고 가져오려면 어떻게 할까요?

- **내보내기**: Azure SQL Database를 Azure Portal에서 BACPAC 파일로 내보낼 수 있습니다.

   ![데이터베이스 내보내기](./media/sql-database-export/database-export1.png)

- **가져오기**: 또한 Azure Portal을 사용하여 데이터를 BACPAC 파일로 데이터베이스에 가져올 수도 있습니다.

   ![데이터베이스 가져오기](./media/sql-database-import/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>SQL Database와 SQL Server 사이에 데이터를 동기화하려면 어떻게 할까요?

이 목적을 달성하는 여러 가지 방법이 있습니다.

- **[Data Sync](sql-database-sync-data.md)** – 이 기능은 여러 온-프레미스 SQL Server 데이터베이스와 SQL Database 사이에 양방향으로 데이터를 동기화하도록 도와 줍니다. 온-프레미스 SQL Server 데이터베이스와 동기화하려면 로컬 컴퓨터에 동기화 에이전트를 설치 및 구성하고 아웃바운드 TCP 포트 1433을 열어야 합니다.
- **[트랜잭션 복제](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** – 트랜잭션 복제를 사용하여 온-프레미스에서 Azure SQL DB까지의 데이터를 게시자인 온-프레미스 및 구독자인 Azure SQL DB와 동기화할 수 있습니다. 현재 이 설정만 지원됩니다. 가동 중지 시간을 최소화하면서 온-프레미스 데이터를 Azure SQL로 마이그레이션하는 방법은: [트랜잭션 복제 사용](sql-database-single-database-migrate.md#method-2-use-transactional-replication)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[SQL Database](sql-database-technical-overview.md)에 대해 알아봅니다.
