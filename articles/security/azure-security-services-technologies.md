---
title: Azure 보안 서비스 및 기술 | Microsoft Docs
description: 이 문서는 엄선된 Azure 보안 서비스 및 기술 목록입니다.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: barclayn
ms.openlocfilehash: 13183282e5e607f0052194a474203f97e0160adb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610907"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Azure에서 사용 가능한 보안 서비스 및 기술

현재 Azure 고객 및 잠재 고객과의 대화에서 자주 받는 질문 중 하나가 "Azure에서 제공하는 모든 보안 관련 서비스 및 기술 목록이 있나요?"입니다.

클라우드 서비스 공급자 옵션을 평가할 때 이러한 정보가 있으면 유용합니다. 이를 위해 Microsoft에서 사용자의 시작을 도울 수 있도록 이 목록을 준비했습니다.

시간이 지남에 따라 이 목록은 Azure와 마찬가지로 변경되고 증가합니다. 보안 관련 서비스 및 기술을 최신 상태로 유지할 수 있도록 정기적으로 이 페이지를 확인하세요.

## <a name="general-azure-security"></a>일반 Azure 보안
|서비스|설명|
|--------|--------|
|[Azure&nbsp;Security&nbsp;Center](../security-center/security-center-intro.md)| 하이브리드 클라우드 워크로드에서 보안 관리 및 고급 위협 방지를 제공하는 클라우드 워크로드 보호 솔루션입니다.|
|[Azure Key Vault](../key-vault/key-vault-overview.md)| 암호, 연결 문자열 및 앱 작동을 유지하는 데 필요한 기타 정보에 대한 보안 비밀 저장소입니다. |
|[Azure Monitor 로그](../log-analytics/log-analytics-overview.md)|원격 분석 및 기타 데이터를 수집하고, 쿼리 언어 및 분석 엔진을 제공하여 앱 및 리소스에 대한 Operational Insights를 전달하는 모니터링 서비스입니다. 단독으로 사용하거나 Security Center와 같은 다른 서비스와 함께 사용할 수 있습니다. |
|[Azure Dev/Test Labs](../devtest-lab/devtest-lab-overview.md)|개발자와 테스터가 낭비를 최소화하고 비용을 제어하면서 Azure에서 빠르게 환경을 만들 수 있도록 돕는 서비스입니다.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](azure-security-disk-encryption-overview.md)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>저장소 보안
|서비스|설명|
|------|--------|
| [Azure&nbsp;Storage&nbsp;서비스&nbsp;암호화](../storage/common/storage-service-encryption.md)|Azure Storage의 데이터를 자동으로 암호화하는 보안 기능입니다.   |
|[StorSimple 암호화된 하이브리드 저장소](../storsimple/storsimple-ova-overview.md)| 온-프레미스 디바이스 및 Azure 클라우드 저장소 간의 저장소 태스크를 관리하는 통합된 저장소 솔루션입니다.|
|[Azure 클라이언트 쪽 암호화](../storage/common/storage-client-side-encryption.md)| Azure Storage에 업로드하기 전에 클라이언트 애플리케이션 내의 데이터를 암호화하고, 다운로드하는 동안 데이터를 해독하는 클라이언트 쪽 암호화 솔루션입니다. |
| [Azure Storage 공유 액세스 서명](../storage/common/storage-dotnet-shared-access-signature-part-1.md)|공유 액세스 서명은 저장소 계정의 리소스에 대한 위임된 권한을 제공합니다.  |
|[Azure Storage 계정 키](../storage/common/storage-create-storage-account.md)| 스토리지 계정에 액세스할 때 인증에 사용되는 Azure Storage에 대한 액세스 제어 방법입니다. |
|[SMB 3.0 암호화를 사용한 Azure 파일 공유](../storage/files/storage-files-introduction.md)|SMB(서버 메시지 블록) 파일 공유 프로토콜에 자동 네트워크 암호화를 사용하는 네트워크 보안 기술입니다. |
|[Azure Storage 분석](https://docs.microsoft.com/rest/api/storageservices/Storage-Analytics)| 저장소 계정의 데이터에 대한 로깅 및 메트릭 생성 기술입니다. |

<!------>

## <a name="database-security"></a>데이터베이스 보안
|서비스|설명|
|------|--------|
| [Azure&nbsp;SQL&nbsp;방화벽](../sql-database/sql-database-firewall-configure.md)|데이터베이스에 대한 네트워크 기반 공격으로부터 보호하는 네트워크 액세스 제어 기능입니다. |
|[Azure&nbsp;SQL&nbsp;셀&nbsp;수준 암호화](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| 세부적인 수준에서 암호화를 제공하는 데이터베이스 보안 기술입니다.  |
| [Azure&nbsp;SQL&nbsp;연결 암호화](../sql-database/sql-database-control-access.md)|SQL Database는 보안을 제공하기 위해 IP 주소로 연결을 제한하는 방화벽 규칙, 사용자가 해당 ID를 증명하도록 하는 인증 메커니즘 및 특정 작업 및 데이터에 대한 사용자를 제한하는 권한 부여 메커니즘을 사용하여 액세스를 제어합니다. |
| [Azure SQL 항상 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Azure SQL Database 또는 SQL Server 데이터베이스에 저장된 신용 카드 번호 또는 주민 등록 번호(예: 미국 사회 보장 번호)와 같은 중요한 데이터를 보호합니다.  |
| [Azure&nbsp;SQL&nbsp;투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| 전체 데이터베이스의 저장소를 암호화하는 데이터베이스 보안 기능입니다. |
| [Azure SQL Database 감사](../sql-database/sql-database-auditing.md)|데이터베이스 이벤트를 추적하고 이를 Azure Storage 계정의 감사 로그에 기록하는 데이터베이스 감사 기능입니다.  |


## <a name="identity-and-access-management"></a>ID 및 액세스 관리
|서비스|설명|
|------|--------|
| [Azure&nbsp;역할&nbsp;기반&nbsp;액세스 제어](../active-directory/role-based-access-control-configure.md)|사용자가 조직 내 역할을 기반으로 액세스하는 데 필요한 리소스에만 액세스하도록 허용하는 액세스 제어 기능입니다.  |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)|Azure 내에서 다중 테넌트, 클라우드 기반 디렉터리 및 여러 ID 관리 서비스를 지원하는 클라우드 기반 인증 리포지토리입니다.  |
| [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-overview.md)|Azure 애플리케이션을 사용할 때 고객이 자신의 프로필을 등록, 로그인 및 관리하는 방법을 제어할 수 있도록 해주는 ID 관리 서비스입니다.   |
| [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md)| Active Directory Domain Services의 클라우드 기반 및 관리되는 버전입니다. |
| [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)| 보안 정보 액세스를 허용하기 전에 여러 가지 형식의 인증 및 확인을 사용하는 보안 프로비전입니다. |

## <a name="backup-and-disaster-recovery"></a>백업 및 재해 복구
|서비스|설명|
|------|--------|
| [Azure&nbsp;Backup](../backup/backup-introduction-to-azure-backup.md)| Azure Cloud에서 데이터를 백업 및 복원하는 데 사용되는 Azure 기반 서비스입니다. |
| [Azure&nbsp;Site&nbsp;Recovery](../site-recovery/site-recovery-overview.md)|물리적 컴퓨터와 VM(가상 머신)에서 실행되는 워크로드를 기본 사이트에서 보조 위치로 복제하여 오류 후 서비스 복구가 가능하도록 하는 온라인 서비스입니다. |

## <a name="networking"></a>네트워킹
|서비스|설명|
|------|--------|
| [네트워크&nbsp;보안&nbsp;그룹](../virtual-network/virtual-networks-nsg.md)| 5튜플을 사용하여 의사 결정을 허용하거나 거부하는 네트워크 기반 액세스 제어 기능입니다.  |
| [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)| Azure 가상 네트워크에 대한 크로스-프레미스 액세스를 허용하기 위해 VPN 엔드포인트로 사용되는 네트워크 디바이스입니다.  |
| [Azure Application Gateway](../application-gateway/application-gateway-introduction.md)|URL을 기반으로 라우팅하고 SSL 오프로딩을 수행할 수 있는 고급 웹 애플리케이션 부하 분산 장치입니다. |
|[WAF](../application-gateway/waf-overview.md)(웹 애플리케이션 방화벽)|일반적인 악용 및 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호하는 Application Gateway의 기능입니다.|
| [Azure Load Balancer](../load-balancer/load-balancer-overview.md)|TCP/UDP 애플리케이션 네트워크 부하 분산 장치입니다. |
| [Azure ExpressRoute](../expressroute/expressroute-introduction.md)| 온-프레미스 네트워크와 Azure 가상 네트워크 간의 전용 WAN 링크입니다. |
| [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)| 전역 DNS 부하 분산 장치입니다.|
| [Azure 애플리케이션 프록시](../active-directory/active-directory-application-proxy-get-started.md)| 온-프레미스에서 호스트되는 웹 애플리케이션에 대한 원격 액세스를 보호하는 데 사용되는 인증 프런트 엔드입니다. |
|[Azure Firewall](../firewall/overview.md)|Azure Virtual Network 리소스를 보호하는 관리형 클라우드 기반 네트워크 보안 서비스입니다.|
|[Azure DDoS Protection](../virtual-network/ddos-protection-overview.md)|애플리케이션 설계 모범 사례와 결합하여 DDoS 공격을 방어합니다.|
|[Virtual Network 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)|직접 연결을 통해 가상 네트워크 사설 주소 공간과 VNet의 ID를 Azure 서비스로 확장합니다.|