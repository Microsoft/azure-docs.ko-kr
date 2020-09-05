---
title: 콘텐츠 참조 구성 & 관리
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance를 구성 하 고 관리 하는 방법을 설명 하는 콘텐츠의 참조 가이드입니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: de8d1b7aba7763799228c831af61bf83a79937bb
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167983"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Azure SQL Managed Instance 콘텐츠 참조
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 Azure SQL Managed Instance를 관리 및 구성 하는 데 도움이 되는 다양 한 가이드, 스크립트 및 설명에 대 한 콘텐츠 참조를 찾을 수 있습니다.

## <a name="load-data"></a>데이터 로드

- [AZURE sql Managed Instance로 마이그레이션](migrate-to-instance-from-sql-server.md): azure sql Managed Instance로 마이그레이션하기 위한 권장 마이그레이션 프로세스 및 도구에 대해 알아봅니다.
- [Tde 인증서를 AZURE SQL Managed Instance로 마이그레이션](tde-certificate-migrate.md): SQL Server 데이터베이스가 tde (투명 한 데이터 암호화)를 사용 하 여 보호 되는 경우 SQL Managed Instance에서 azure에 복원 하려는 백업을 암호 해독 하는 데 사용할 수 있는 인증서를 마이그레이션해야 합니다.
- [BACPAC에서 DB 가져오기](../database/database-import.md)
- [BACPAC로 DB 내보내기](../database/database-export.md)
- [BCP를 사용하여 데이터 로드](../load-from-csv-with-bcp.md)
- [Azure Data Factory를 사용 하 여 데이터 로드](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>네트워크 구성

- [서브넷 크기 확인](vnet-subnet-determine-size.md): SQL Managed Instance를 배포한 후 서브넷의 크기를 조정할 수 없으므로 서브넷에 배포 하려는 관리 되는 인스턴스의 수와 유형에 필요한 IP 범위를 계산 해야 합니다. 
- [새 VNet 및 서브넷 만들기](virtual-network-subnet-create-arm-template.md): [네트워크 요구 사항](connectivity-architecture-overview.md#network-requirements)에 따라 가상 네트워크 및 서브넷을 구성 합니다. 
- [기존 VNet 및 서브넷 구성](vnet-existing-add-subnet.md): 네트워크 요구 사항을 확인 하 고 기존 가상 네트워크 및 서브넷을 구성 하 여 SQL Managed Instance를 배포 합니다. 
- [사용자 지정 Dns 구성](custom-dns-configure.md): db 메일 프로필의 연결 된 서버를 통해 SQL Managed Instance에서 사용자 지정 도메인에 대 한 외부 리소스 액세스 권한을 부여 하도록 사용자 지정 dns를 구성 합니다. 
- [동기화 네트워크 구성](azure-app-sync-network-configuration.md): [Azure 가상 네트워크와 앱을 통합](../../app-service/web-sites-integrate-with-vnet.md)한 후 연결을 설정할 수 없는 경우 네트워킹 구성 계획을 새로 고칩니다.
- [관리 끝점 IP 주소 찾기](management-endpoint-find-ip-address.md): SQL Managed Instance 관리 목적으로 사용 하는 공용 끝점을 결정 합니다. 
- [기본 제공 방화벽 보호 확인](management-endpoint-verify-built-in-firewall.md): SQL Managed Instance가 필요한 포트 및 기타 기본 제공 방화벽 규칙에 대 한 트래픽만 허용 하는지 확인 합니다. 
- [응용 프로그램 연결](connect-application-instance.md): SQL Managed Instance에 응용 프로그램을 연결 하기 위한 다양 한 패턴에 대해 알아봅니다.

## <a name="feature-configuration"></a>기능 구성

- [Azure AD 인증 구성](../database/authentication-aad-configure.md)
- [조건부 액세스 구성](../database/conditional-access-configure.md)
- [Multi-factor Azure AD 인증](../database/authentication-mfa-ssms-overview.md)
- [Multi-Factor Auth 구성](../database/authentication-mfa-ssms-configure.md)
- [임시 보존 정책 구성](../database/temporal-tables-retention-policy.md)
- [BYOK를 사용하여 TDE 구성](../database/transparent-data-encryption-byok-configure.md)
- [TDE BYOK 키 순환](../database/transparent-data-encryption-byok-key-rotation.md)
- [TDE 보호기 제거](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [메모리 내 OLTP 구성](../in-memory-oltp-configure.md)
- [Azure Automation 구성](../database/automation-manage.md)
- [트랜잭션 복제](replication-between-two-instances-configure-tutorial.md) 를 사용 하면 관리 되는 인스턴스 간에 또는 온-프레미스 SQL SERVER에서 SQL Managed Instance로 또는 그 반대로 데이터를 복제할 수 있습니다.
- [위협 감지 구성](threat-detection-configure.md) – [위협 검색](../database/threat-detection-overview.md) 은 sql 삽입 또는 의심 스러운 위치에서의 액세스 등 다양 한 잠재적인 공격을 검색 하는 기본 제공 Azure SQL Managed Instance 기능입니다. 
- [경고를 만들면](alerts-create.md) CPU 사용률, 저장소 공간 소비, IOPS 등의 모니터링 되는 메트릭에 대 한 경고를 설정 하 고 SQL Managed Instance에 대 한 경고를 설정할 수 있습니다. 

## <a name="monitoring-and-tuning"></a>모니터링 및 튜닝

- [수동 조정](../database/performance-guidance.md)
- [DMV를 사용하여 성능 모니터링](../database/monitoring-with-dmvs.md)
- [쿼리 저장소를 사용 하 여 성능 모니터링](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Intelligent Insights로 성능 문제 해결](../database/intelligent-insights-troubleshoot-performance.md)
- [Intelligent Insights 진단 로그 사용](../database/intelligent-insights-use-diagnostics-log.md)
- [메모리 내 OLTP 공간 모니터링](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>확장 이벤트

- [확장 이벤트](../database/xevent-db-diff-from-svr.md)
- [이벤트 파일에 확장 이벤트 저장](../database/xevent-code-event-file.md)
- [확장 이벤트를 링 버퍼에 저장 합니다.](../database/xevent-code-ring-buffer.md)

### <a name="alerting"></a>경고

- [관리 되는 인스턴스에 대 한 경고 만들기](alerts-create.md)

## <a name="operations"></a>작업

- [SQL Managed Instance에서 사용자가 시작한 수동 장애 조치](user-initiated-failover.md)

## <a name="develop-applications"></a>애플리케이션 개발

- [연결](../database/connect-query-content-reference-guide.md#libraries)
- [Spark 커넥터 사용](../../cosmos-db/spark-connector.md)
- [앱 인증](../database/application-authentication-get-client-id-keys.md)
- [성능 향상을 위해 일괄 처리 사용](../performance-improve-use-batching.md)
- [연결 지침](../database/troubleshoot-common-connectivity-issues.md)
- [DNS 별칭](../database/dns-alias-overview.md)
- [PowerShell을 사용 하 여 DNS 별칭 설정](../database/dns-alias-powershell-create.md)
- [포트 - ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C 및 C ++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>애플리케이션 디자인

- [재해 복구를 위한 디자인](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [탄력적 풀을 위한 디자인](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [앱 업그레이드를 위한 디자인](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>다중 테넌트 SaaS 애플리케이션 디자인

- [SaaS 디자인 패턴](../database/saas-tenancy-app-design-patterns.md)
- [SaaS 비디오 사용](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS 앱 보안](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>다음 단계

[SQL Managed Instance를 배포](instance-create-quickstart.md)하 여 시작 하세요.
