---
title: 관리형 인스턴스 FAQ
description: SQL Database 관리 되는 인스턴스 FAQ (질문과 대답)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 1c1995b4daf3b76abf7663d8d6c1f4cb7b1d6e2b
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201682"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL Database 관리 되는 인스턴스 FAQ (질문과 대답)

이 문서에는 [SQL Database 관리 되는 인스턴스에](sql-database-managed-instance.md)대해 가장 일반적인 질문 중 상당수가 포함 되어 있습니다.

## <a name="supported-features"></a>지원되는 기능

**관리 되는 인스턴스에서 지원 되는 기능 목록은 어디에서 찾을 수 있나요?**

관리 되는 인스턴스에서 지원 되는 기능 목록은 Azure SQL Database 대 [SQL Server](sql-database-features.md)을 참조 하세요.

Azure SQL Database 관리 되는 인스턴스와 온-프레미스 SQL Server 간의 구문 및 동작 차이에 대 한 자세한 내용은 [SQL Server의 t-sql 차이점](sql-database-managed-instance-transact-sql-information.md)을 참조 하세요.


## <a name="tech-spec--resource-limits"></a>기술 사양 & 리소스 제한
 
**관리 되는 인스턴스의 기술 특성 및 리소스 제한은 어디에서 확인할 수 있나요?**

사용 가능한 하드웨어 생성 특성은 [하드웨어 세대의 기술적 차이점](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)을 참조 하세요.
사용 가능한 서비스 계층 및 해당 특성에 대 한 자세한 내용은 [서비스 계층 간의 기술적 차이점](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)을 참조 하세요.

## <a name="known-issues--bugs"></a>알려진 문제 & 버그

**알려진 문제 및 버그는 어디서 찾을 수 있나요?**

버그 및 알려진 문제에 대해서는 [알려진 문제](sql-database-managed-instance-transact-sql-information.md#Issues)를 참조 하세요.

## <a name="new-features"></a>새로운 기능

**공개 미리 보기에서 최신 기능과 기능을 어디에서 찾을 수 있나요?**

새 기능 및 미리 보기 기능에 대 한 자세한 내용은 [릴리스 정보](/azure/sql-database/sql-database-release-notes?tabs=managed-instance)를 참조 하세요.

## <a name="deployment-times"></a>배포 시간 

**인스턴스를 만들거나 업데이트 하는 데 소요 되는 시간 또는 데이터베이스를 복원 하는 데 소요 되는 시간**

새 관리 되는 인스턴스를 만들거나 서비스 계층 (vCores, 저장소)을 변경 하는 데 필요한 시간은 여러 요소에 따라 달라 집니다. [관리 작업](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 살펴보기 

## <a name="naming-convention"></a>명명 규칙

**관리 되는 인스턴스의 이름이 온-프레미스 SQL Server와 동일할 수 있나요?**

관리 되는 인스턴스의 이름은 *database.windows.net*로 끝나야 합니다. 기본값 대신 다른 DNS 영역을 사용 하려면 (예: **mi-이름**. contoso.com) 
- CliConfig를 사용 하 여 별칭을 정의 합니다. 이 도구는 단지 레지스트리 설정 래퍼 이므로 그룹 정책 또는 스크립트를 사용 하 여 수행할 수도 있습니다.
- *Trustservercertificate = true* 옵션과 함께 *CNAME* 을 사용 합니다.

## <a name="move-db-from-mi"></a>MI에서 DB 이동 

**관리 되는 인스턴스에서 다시 SQL Server 또는 Azure SQL Database로 데이터베이스를 이동 하려면 어떻게 해야 하나요?**

[데이터베이스를 bacpac로 내보낸](sql-database-export.md) 다음 [bacpac 파일을 가져올]( sql-database-import.md)수 있습니다. 데이터베이스가 100 GB 보다 작은 경우에 권장 되는 방법입니다.

데이터베이스의 모든 테이블에 기본 키가 있는 경우 트랜잭션 복제를 사용할 수 있습니다.

관리 되는 인스턴스에서 SQL Server와 비교 하 여 더 높은 버전의 데이터베이스를 사용 하므로 관리 되는 인스턴스에서 가져온 네이티브 `COPY_ONLY` 백업을 SQL Server로 복원할 수 없습니다.

## <a name="migrate-instance-db"></a>인스턴스 DB 마이그레이션

**내 인스턴스 데이터베이스를 단일 Azure SQL Database 마이그레이션하려면 어떻게 해야 하나요?**

한 가지 옵션은 [데이터베이스를 bacpac로 내보낸](sql-database-export.md) 다음 [bacpac 파일을 가져오는](sql-database-import.md)것입니다. 

데이터베이스가 100 GB 보다 작은 경우에 권장 되는 방법입니다. 데이터베이스의 모든 테이블에 기본 키가 있는 경우 트랜잭션 복제를 사용할 수 있습니다.

## <a name="switch-hardware-generation"></a>하드웨어 생성 전환 

**관리 되는 인스턴스 하드웨어 생성을 Gen 4와 Gen 5 online 간에 전환할 수 있나요?**

관리 되는 인스턴스가 프로 비전 되는 지역에서 두 하드웨어 생성을 모두 사용할 수 있는 경우 하드웨어 세대 간에 자동 온라인 전환이 가능 합니다. 이 경우 하드웨어 세대를 전환 하는 방법을 설명 하는 [Vcore 모델 개요 페이지](sql-database-service-tiers-vcore.md) 를 확인할 수 있습니다.

새 관리 되는 인스턴스가 백그라운드에서 프로 비전 되 고 프로세스가 끝날 때 빠른 장애 조치 (failover)를 사용 하 여 이전 인스턴스와 새 인스턴스 간에 데이터베이스가 자동으로 전송 되기 때문에이 작업은 장기 실행 작업입니다. 

동일한 지역에서 두 하드웨어 생성이 모두 지원 되지 않는 경우 하드웨어 생성을 변경 하는 것이 가능 하지만이를 수동으로 수행 해야 합니다. 이렇게 하려면 원하는 하드웨어 생성을 사용할 수 있는 지역에 새 인스턴스를 프로 비전 하 고 이전 인스턴스와 새 인스턴스 간에 데이터를 수동으로 백업 하 고 복원 해야 합니다.


## <a name="tune-performance"></a>성능 조정

**관리 되는 인스턴스의 성능을 조정할 어떻게 할까요? 있나요?**

범용 관리 되는 인스턴스는 성능에 중요 한 데이터 및 로그 파일의 크기 때문에 원격 저장소를 사용 합니다. 자세한 내용은 범용 [Managed Instance 성능에 대 한 로그 파일 크기의 영향](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)을 참조 하세요.

워크 로드가 많은 작은 트랜잭션으로 구성 된 경우 연결 유형을 프록시에서 리디렉션 모드로 전환 하는 것이 좋습니다.

## <a name="maximum-storage-size"></a>최대 스토리지 크기

**관리 되는 인스턴스의 최대 저장소 크기는 얼마 인가요?**

관리 되는 인스턴스의 저장소 크기는 선택한 서비스 계층 (범용 또는 중요 비즈니스용)에 따라 달라 집니다. 이러한 서비스 계층의 저장소 제한 사항은 [서비스 계층 특성](sql-database-service-tiers-general-purpose-business-critical.md)을 참조 하세요.

## <a name="back-up-storage-cost"></a>저장소 비용 백업 

**백업 저장소가 관리 되는 인스턴스 저장소에서 공제?**

아니요, 백업 저장소는 관리 되는 인스턴스 저장소 공간에서 공제 되지 않습니다. 백업 저장소는 인스턴스 저장소 공간과 독립적 이며 크기가 제한 되지 않습니다. 백업 저장소는 7 일에서 35 일까지 구성할 수 있는 인스턴스 데이터베이스의 백업을 보존 하는 기간으로 제한 됩니다. 자세한 내용은 자동화 된 [백업](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)을 참조 하세요.

## <a name="track-billing"></a>청구 추적

**관리 되는 인스턴스의 청구 비용을 추적 하는 방법이 있나요?**

[Azure Cost Management 솔루션](/azure/cost-management/)을 사용 하 여이 작업을 수행할 수 있습니다. [Azure Portal](https://portal.azure.com) **구독** 으로 이동 하 여 **비용 분석**을 선택 합니다. 

**누적 비용** 옵션을 사용한 다음 **리소스 유형** 으로 `microsoft.sql/managedinstances`필터링 합니다. 
  
## <a name="inbound-nsg-rules"></a>인바운드 NSG 규칙

**관리 포트에서 인바운드 NSG 규칙을 설정 하려면 어떻게 해야 하나요?**

기본 제공 방화벽 기능을 사용 하면 클러스터의 모든 가상 컴퓨터에서 Windows 방화벽을 구성 하 여 Microsoft 관리/배포 컴퓨터에만 연결 되는 IP 범위에서 인바운드 연결을 허용 하 고, 보안 관리 워크스테이션은 네트워크 계층을 통해 침입을 효과적으로 방지 합니다.

에 사용 되는 포트는 다음과 같습니다.

포트 9000 및 9003은 Service Fabric 인프라에서 사용 됩니다. Service Fabric 주 역할은 가상 클러스터를 정상 상태로 유지 하 고 구성 요소 복제본의 수 측면에서 목표 상태를 유지 하는 것입니다.

노드 에이전트에서 1438, 1440 및 1452 포트를 사용 합니다. 노드 에이전트는 클러스터 내에서 실행 되는 응용 프로그램이 며, 제어 평면에서 관리 명령을 실행 하는 데 사용 됩니다.

네트워크 계층의 기본 제공 방화벽 외에도 통신은 인증서로 보호 됩니다.
  
기본 제공 방화벽을 확인 하는 방법 및 기본 제공 방화벽을 확인 하는 방법에 대 한 자세한 내용은 [Azure SQL Database managed instance 기본 제공 방화벽](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)을 참조 하세요.


## <a name="mitigate-network-risks"></a>네트워크 위험 완화  

**네트워킹 위험을 완화 하려면 어떻게 해야 하나요?**

네트워킹 위험을 완화 하기 위해 고객은 보안 설정 및 컨트롤 집합을 적용 하는 것이 좋습니다.

- 모든 데이터베이스에서 [TDE (투명한 데이터 암호화)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) 를 설정 합니다.
- CLR (공용 언어 런타임)을 해제 합니다. 이는 온-프레미스에도 권장 됩니다.
- AAD (Azure Active Directory) 인증만 사용 합니다.
- 권한이 낮은 DBA 계정으로 인스턴스에 액세스 합니다.
- Sysadmin 계정에 대 한 JiT jumpbox 액세스를 구성 합니다.
- [SQL 감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)를 켜고 경고 메커니즘과 통합 합니다.
- [광고 (Advanced Data Security)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) 도구 모음에서 [위협 감지](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) 를 켭니다.


## <a name="cost-saving-use-cases"></a>사용 사례 절약 비용

**관리 되는 인스턴스를 사용 하 여 비용을 절감 하는 사용 사례를 찾을 수 있는 위치**

관리 되는 인스턴스 사례 연구:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
Azure SQL Database 관리 되는 인스턴스를 배포 하는 것과 관련 된 이점, 비용 및 위험에 대 한 이해를 높이기 위해 Forrester의 연구: [MI의 총 경제 영향](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)도 있습니다.


## <a name="dns-refresh"></a>DNS 새로 고침 

**DNS 새로 고침을 수행할 수 있나요?**

현재 관리 되는 인스턴스에 대 한 DNS 서버 구성을 새로 고치는 기능을 제공 하지 않습니다.

DNS 구성은 결국 새로 고쳐집니다.

- DHCP 임대가 만료 되는 경우
- 플랫폼 업그레이드 시.

이 문제를 해결 하려면 관리 되는 인스턴스를 4 vCore로 다운 그레이드 하 고 나중에 다시 업그레이드 합니다. DNS 구성을 새로 고치면 부작용이 발생 합니다.


## <a name="static-ip-address"></a>고정 IP 주소

**관리 되는 인스턴스에 고정 IP 주소가 있을 수 있나요?**

드물지만 필요한 경우에는 관리 되는 인스턴스를 새 가상 클러스터로 온라인으로 마이그레이션해야 할 수도 있습니다. 필요한 경우이 마이그레이션은 서비스의 보안 및 안정성을 향상 시키기 위한 기술 스택의 변경 내용 때문에 발생 합니다. 새 가상 클러스터로 마이그레이션하면 관리 되는 인스턴스 호스트 이름에 매핑되는 IP 주소가 변경 됩니다. 관리 되는 인스턴스 서비스는 고정 IP 주소 지원을 주장 하지 않으며 정기 유지 관리 주기 중에 통지 없이 변경할 권리를 보유 합니다.

따라서 불필요 한 가동 중지 시간이 발생할 수 있으므로 IP 주소의 불변성에 의존 하지 않는 것이 좋습니다.

## <a name="moving-mi"></a>MI 이동

**관리 되는 인스턴스 또는 해당 VNet을 다른 리소스 그룹으로 이동할 수 있나요?**

아니요, 현재 플랫폼 제한입니다. 관리 되는 인스턴스를 만든 후에는 관리 되는 인스턴스 또는 VNet을 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다.

## <a name="change-time-zone"></a>표준 시간대 변경

**기존 관리 되는 인스턴스의 표준 시간대를 변경할 수 있나요?**

관리 되는 인스턴스가 처음으로 프로 비전 되 면 표준 시간대 구성을 설정할 수 있습니다. 기존 관리 되는 인스턴스의 표준 시간대를 변경 하는 것은 지원 되지 않습니다. 자세한 내용은 [표준 시간대 제한 사항](sql-database-managed-instance-timezone.md#limitations)을 참조 하세요.

해결 방법에는 적절 한 표준 시간대를 사용 하 여 관리 되는 인스턴스를 새로 만든 다음 수동 백업 및 복원을 수행 하거나 권장 사항을 수행 [하는 것](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)이 포함 됩니다.


## <a name="resolve-performance-issues"></a>성능 문제 해결

**관리 되는 인스턴스를 사용 하 여 성능 문제를 해결할 어떻게 할까요? 있나요?**

관리 되는 인스턴스와 SQL Server 간의 성능 비교를 위해 [AZURE SQL 관리 되는 인스턴스와 SQL Server 문서 간의 성능 비교를 위한](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) 좋은 출발점은 모범 사례입니다.

데이터 로드는 필수적인 전체 복구 모델 및 트랜잭션 로그 쓰기 처리량 [제한](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) 으로 인해 SQL Server 보다 관리 되는 인스턴스에서 속도가 느립니다. 경우에 따라 사용자 데이터베이스 대신 tempdb로 임시 데이터를 로드 하거나 클러스터형 columnstore 또는 메모리 최적화 테이블을 사용 하 여이 작업을 수행할 수 있습니다.


## <a name="restore-encrypted-backup"></a>암호화 된 백업 복원

**암호화 된 데이터베이스를 관리 되는 인스턴스로 복원할 수 있나요?**

예, 관리 되는 인스턴스로 복원 하기 위해 데이터베이스 암호를 해독할 필요는 없습니다. 암호화 된 백업 파일에서 데이터를 읽을 수 있으려면 원본 시스템에서 암호화 키 보호기로 사용 되는 인증서/키를 관리 되는 인스턴스에 제공 해야 합니다. 입력하는 방법에는 다음 두 가지가 있습니다.

- *인증서-보호기를 관리 되는 인스턴스에 업로드*합니다. PowerShell을 사용 하 여이 작업을 수행할 수 있습니다. [샘플 스크립트](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) 는 전체 프로세스를 설명 합니다.
- *비대칭 키-보호기를 Azure Key Vault (AKV)에 업로드 하 고 관리 되는 인스턴스를 가리키도록*합니다. 이 방법은 AKV 통합을 사용 하 여 암호화 키를 저장 하는 BYOK (사용자 소유의 키) TDE 사용 사례와 비슷합니다. 키를 암호화 키 보호기로 사용 하지 않고 관리 되는 인스턴스에서 암호화 된 데이터베이스를 복원 하는 데 키를 사용할 수 있도록 하려는 경우 [BYOK TDE 설정](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)지침을 따르고 *선택 된 키를 기본 tde 보호기*로 설정 확인란을 선택 하지 않습니다.

관리 되는 인스턴스에서 암호화 보호기를 사용할 수 있게 되 면 표준 데이터베이스 복원 절차를 진행할 수 있습니다.

## <a name="migrate-from-single-db"></a>단일 DB에서 마이그레이션 

**Azure SQL Database 단일 또는 탄력적 풀에서 관리 되는 인스턴스로 마이그레이션하려면 어떻게 해야 하나요?**

관리 되는 인스턴스는 Azure SQL Database의 다른 배포 옵션으로 계산 및 저장소 크기에 따라 동일한 성능 수준을 제공 합니다. 단일 인스턴스에서 데이터를 통합 하거나 관리 되는 인스턴스에서 독점적 으로만 지원 되는 기능만 필요한 경우에는 BACPAC (내보내기/가져오기) 기능을 사용 하 여 데이터를 마이그레이션할 수 있습니다.
