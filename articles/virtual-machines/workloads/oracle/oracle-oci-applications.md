---
title: Azure Virtual Machines에 Oracle 앱을 배포 하는 아키텍처 Microsoft Docs
description: Azure 또는 Oracle 클라우드 인프라 (OCI)에서 데이터베이스를 사용 하 여 Microsoft Azure 가상 머신에서 E-비즈니스 제품군, JD Edwards EnterpriseOne 및 PeopleSoft를 포함 하는 Oracle 앱을 배포 하는 응용 프로그램 아키텍처입니다.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: b183a4d4922c89f60ccb19b3e3e978216f33cc9a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100082"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Azure에서 Oracle 응용 프로그램을 배포 하는 아키텍처

Microsoft와 Oracle은 고객이 Oracle E-비즈니스 제품군, JD Edwards EnterpriseOne, PeopleSoft 등의 Oracle 응용 프로그램을 클라우드에 배포할 수 있도록 함께 작업 했습니다. Microsoft Azure와 Oracle 클라우드 인프라 (OCI) 간의 preview [개인 네트워크 상호 연결과](configure-azure-oci-networking.md) 도입 되면서 이제 Azure에서 AZURE 또는 oci의 백 엔드 데이터베이스를 사용 하 여 Oracle 응용 프로그램을 배포할 수 있습니다. Oracle 응용 프로그램을 Azure Active Directory와 통합 하 여 사용자가 Azure Active Directory (Azure AD) 자격 증명을 사용 하 여 Oracle 응용 프로그램에 로그인 할 수 있도록 Single Sign-On를 설정할 수도 있습니다.

OCI는 DBaaS, Exadata 클라우드 서비스, Oracle RAC 및 IaaS (Infrastructure as a Service)를 포함 하 여 Oracle 응용 프로그램에 대 한 여러 Oracle 데이터베이스 옵션을 제공 합니다. 현재, 자치 데이터베이스는 Oracle 응용 프로그램에 대해 지원 되는 백 엔드가 아닙니다.

Azure에서 Oracle 응용 프로그램을 배포 하기 위한 [여러 가지 옵션이](oracle-overview.md) 있으며,이는 항상 사용 가능 하 고 안전한 방식으로 포함 됩니다. Azure는 Oracle 응용 프로그램을 Azure에서 완전히 실행 하도록 선택한 경우 배포할 수 있는 [oracle 데이터베이스 VM 이미지](oracle-vm-solutions.md) 도 제공 합니다.

다음 섹션에서는 Microsoft와 Oracle이 모두 클라우드 간 구성 또는 전체 Azure에서 Oracle E-비즈니스 제품군, JD Edwards EnterpriseOne 및 PeopleSoft를 배포 하기 위한 아키텍처 권장 사항을 간략하게 설명 합니다. Microsoft와 Oracle은 이러한 응용 프로그램을 테스트 하 고 성능이 이러한 응용 프로그램에 대해 Oracle에서 설정한 표준을 충족 하는지 확인 했습니다.

## <a name="architecture-considerations"></a>아키텍처 고려 사항

Oracle 응용 프로그램은 여러 서비스로 구성 되며,이는 Azure의 동일한 가상 컴퓨터 또는 여러 가상 컴퓨터에서 호스트 될 수 있으며, 선택적으로 OCI에서 사용할 수 있습니다. 

응용 프로그램 인스턴스는 개인 또는 공용 엔드포인트을 사용 하 여 설정할 수 있습니다. Microsoft 및 Oracle에서는 응용 프로그램 관리를 위해 별도의 서브넷에 공용 IP 주소를 사용 하 여 *방호 호스트 VM* 을 설정 하는 것이 좋습니다. 그런 다음 데이터베이스 계층을 포함 하 여 다른 컴퓨터에 개인 IP 주소만 할당 합니다. 

클라우드 간 아키텍처에서 응용 프로그램을 설정할 때 계획은 Azure virtual network의 IP 주소 공간이 OCI 가상 클라우드 네트워크의 개인 IP 주소 공간과 겹치지 않도록 하는 데 필요 합니다.

보안을 강화 하려면 특정 포트 및 IP 주소의 트래픽만 허용 되도록 서브넷 수준에서 네트워크 보안 그룹을 설정 합니다. 예를 들어 중간 계층의 컴퓨터는 가상 네트워크 내 에서만 트래픽을 수신 해야 합니다. 외부 트래픽이 중간 계층 컴퓨터에 직접 도달 해서는 안 됩니다.

고가용성을 위해 동일한 가용성 집합 또는 다른 가용성 영역에 있는 여러 서버의 중복 인스턴스를 설정할 수 있습니다. 가용성 영역을 통해 99.99% 가동 시간 SLA를 달성할 수 있으며 가용성 집합을 통해 지역에서 99.95% 작동 시간 SLA를 달성할 수 있습니다. 이 문서에 표시 된 샘플 아키텍처는 두 개의 가용성 영역에 배포 됩니다.

클라우드 간 상호 연결을 사용 하 여 응용 프로그램을 배포 하는 경우 기존 Express 경로 회로를 계속 사용 하 여 Azure 환경을 온-프레미스 네트워크에 연결할 수 있습니다. 그러나 온-프레미스 네트워크에 연결 하는 것 보다 OCI와의 상호 연결에 대 한 별도의 Express 경로 회로가 필요 합니다.

## <a name="e-business-suite"></a>E-비즈니스 제품군

Oracle EBS (E-비즈니스 제품군)는 SCM (공급망 Management) 및 CRM (고객 관계 관리)을 포함 하는 응용 프로그램 모음입니다. OCI의 관리 되는 데이터베이스 포트폴리오를 활용 하려면 Microsoft Azure와 OCI 간의 클라우드 간 상호 연결을 사용 하 여 EBS를 배포할 수 있습니다. 이 구성에서 프레젠테이션 및 응용 프로그램 계층은 다음 아키텍처 다이어그램에 표시 된 것 처럼 Azure에서 실행 되 고 OCI의 데이터베이스 계층에 실행 됩니다 (그림 1).

![E-비즈니스 제품군 클라우드 간 아키텍처](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*그림 1: E-비즈니스 제품군 클라우드 간 아키텍처* 

이 아키텍처에서 Azure의 가상 네트워크는 클라우드 간 상호 연결을 사용 하 여 OCI의 가상 클라우드 네트워크에 연결 됩니다. 응용 프로그램 계층은 Azure에서 설정 되는 반면 데이터베이스는 OCI로 설정 됩니다. 특정 포트에서 특정 서브넷의 트래픽만 허용 하도록 네트워크 보안 그룹을 사용 하 여 각 구성 요소를 자체 서브넷에 배포 하는 것이 좋습니다.

또한 한 지역의 두 가용성 영역에서 Oracle Data Guard를 사용 하 여 구성 된 항상 사용 가능한 Oracle 데이터베이스를 사용 하 여 Azure에서 배포 하기 위해 아키텍처를 완전히 적용할 수 있습니다. 다음 다이어그램 (그림 2)은이 아키텍처 패턴의 예입니다.

![E-비즈니스 제품군 Azure 전용 아키텍처](media/oracle-oci-applications/ebs-arch-azure.png)

*그림 2: E-비즈니스 제품군 Azure 전용 아키텍처*

다음 섹션에서는 높은 수준에서 다양 한 구성 요소에 대해 설명 합니다.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>응용 프로그램 (중간) 계층

응용 프로그램 계층은 자체 서브넷에서 격리 됩니다. 내결함성 및 간편한 패치 관리를 위해 여러 가상 머신이 설정 되어 있습니다. 이러한 Vm은 Azure NetApp Files 및 Ultra Ssd에서 제공 되는 공유 저장소에서 지원 될 수 있습니다. 이 구성을 사용 하면 가동 중지 시간 없이 패치를 보다 쉽게 배포할 수 있습니다. 응용 프로그램 계층의 컴퓨터는 공용 부하 분산 장치에 의해 제어 되 되어야 합니다. 따라서 EBS 응용 프로그램 계층에 대 한 요청은 장애 때문에 계층의 한 컴퓨터가 오프 라인 상태인 경우에도 처리 됩니다.

### <a name="load-balancer"></a>부하 분산 장치

Azure 부하 분산 장치를 사용 하면 여러 작업 인스턴스에 트래픽을 분산 하 여 고가용성을 보장할 수 있습니다. 이 경우 사용자가 웹을 통해 EBS 응용 프로그램에 액세스할 수 있으므로 공용 부하 분산 장치가 설정 됩니다. 부하 분산 장치는 중간 계층의 두 컴퓨터에 부하를 분산 합니다. 보안을 강화 하기 위해 사이트 간 VPN 또는 Express 경로 및 네트워크 보안 그룹을 사용 하 여 회사 네트워크에서 시스템에 액세스 하는 사용자의 트래픽만 허용 합니다.

### <a name="database-tier"></a>데이터베이스 계층

이 계층은 Oracle 데이터베이스를 호스팅하고 해당 서브넷으로 구분 됩니다. 응용 프로그램 계층의 트래픽만 Oracle 관련 데이터베이스 포트 1521의 데이터베이스 계층으로 허용 하는 네트워크 보안 그룹을 추가 하는 것이 좋습니다.

Microsoft 및 Oracle에서는 고가용성 설정을 사용 하는 것이 좋습니다. Azure의 고가용성은 Oracle Data Guard를 사용 하 여 두 개의 Oracle 데이터베이스를 설정 하거나 OCI의 Oracle Database Exadata 클라우드 서비스를 사용 하 여 구현할 수 있습니다. Oracle Database Exadata 클라우드 서비스를 사용 하는 경우 데이터베이스는 두 개의 서브넷에 배포 됩니다. Oracle Data Guard를 사용 하는 두 개의 가용성 도메인에서 OCI의 Vm에 Oracle Database를 설정할 수도 있습니다.


### <a name="identity-tier"></a>Id 계층

Id 계층에는 EBS Asserter VM이 포함 됩니다. EBS Asserter를 사용 하 여 Oracle Identity Cloud Service (IDCS) 및 Azure AD에서 id를 동기화 할 수 있습니다. EBS Asserter는 EBS가 SAML 2.0 또는 Openid connect Connect와 같은 Single Sign-On 프로토콜을 지원 하지 않기 때문에 필요 합니다. EBS Asserter는 IDCS에 의해 생성 된 Openid connect connect 토큰을 사용 하 고 유효성을 검사 한 다음 EBS에서 사용자에 대 한 세션을 만듭니다. 

이 아키텍처는 IDCS 통합을 표시 하지만 Azure AD 통합 액세스 및 Single Sign-On oracle Access Manager (oracle 인터넷 디렉터리 또는 Oracle 통합 디렉터리)를 사용 하 여 사용 하도록 설정할 수도 있습니다. 자세한 내용은 [IDCS 통합을 사용 하 여 ORACLE EBS 배포](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) 또는 [Oam 통합으로 oracle EBS 배포](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf)에 대 한 백서를 참조 하세요.

고가용성을 위해 부하 분산 장치를 사용 하 여 여러 가용성 영역에 걸쳐 EBS Asserter의 중복 서버를 배포 하는 것이 좋습니다.

인프라가 설정 되 면 Oracle에서 제공 하는 설치 가이드에 따라 E-Business Suite를 설치할 수 있습니다.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

Oracle의 JD Edwards EnterpriseOne은 포괄적인 엔터프라이즈 리소스 계획 소프트웨어의 통합 된 응용 프로그램 집합입니다. Oracle 또는 SQL Server 데이터베이스 백 엔드로 설정할 수 있는 다중 계층 응용 프로그램입니다. 이 섹션에서는 OCI 또는 Azure에서 Oracle 데이터베이스 백 엔드를 사용 하 여 JD Edwards EnterpriseOne을 배포 하는 방법에 대해 자세히 설명 합니다.

다음 권장 아키텍처 (그림 3)에서 관리, 프레젠테이션 및 중간 계층은 Azure의 가상 네트워크에 배포 됩니다. 데이터베이스는 OCI의 가상 클라우드 네트워크에 배포 됩니다.

E-비즈니스 제품군과 마찬가지로 보안 관리를 위해 선택적 요새 계층을 설정할 수 있습니다. 응용 프로그램 및 데이터베이스 인스턴스에 액세스 하려면 방호 VM 호스트를 점프 서버로 사용 합니다.

![JD Edwards EnterpriseOne 클라우드 간 아키텍처](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*그림 3: JD Edwards EnterpriseOne 클라우드 간 아키텍처*

이 아키텍처에서 Azure의 가상 네트워크는 클라우드 간 상호 연결을 사용 하 여 OCI의 가상 클라우드 네트워크에 연결 됩니다. 응용 프로그램 계층은 Azure에서 설정 되는 반면 데이터베이스는 OCI로 설정 됩니다. 특정 포트에서 특정 서브넷의 트래픽만 허용 하도록 네트워크 보안 그룹을 사용 하 여 각 구성 요소를 자체 서브넷에 배포 하는 것이 좋습니다.

또한 한 지역의 두 가용성 영역에서 Oracle Data Guard를 사용 하 여 구성 된 항상 사용 가능한 Oracle 데이터베이스를 사용 하 여 Azure에서 배포 하기 위해 아키텍처를 완전히 적용할 수 있습니다. 다음 다이어그램 (그림 4)은이 아키텍처 패턴의 예입니다.

![JD Edwards EnterpriseOne Azure 전용 아키텍처](media/oracle-oci-applications/jdedwards-arch-azure.png)

*그림 4: JD Edwards EnterpriseOne Azure 전용 아키텍처*

다음 섹션에서는 높은 수준에서 다양 한 구성 요소에 대해 설명 합니다.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>관리 계층

이름에서 알 수 있듯이이 계층은 관리 작업에 사용 됩니다. 관리 계층에 대해 별도의 서브넷을 일정 수 있습니다. 이 계층의 서비스 및 서버는 응용 프로그램의 설치 및 관리에 주로 사용 됩니다. 따라서 이러한 서버의 단일 인스턴스도 충분 합니다. 응용 프로그램의 고가용성에는 중복 인스턴스가 필요 하지 않습니다.

이 계층의 구성 요소는 다음과 같습니다.
    
 - **프로 비전 서버** -이 서버는 응용 프로그램의 다양 한 구성 요소에 대 한 종단 간 배포에 사용 됩니다. 포트 22를 통해 데이터베이스 계층의 인스턴스를 포함 하 여 다른 계층의 인스턴스와 통신 합니다. JD Edwards EnterpriseOne에 대 한 서버 관리자 콘솔을 호스팅합니다.
 - **배포 서버** -이 서버는 기본적으로 Jd Edwards enterpriseone을 설치 하는 데 필요 합니다. 설치 과정에서이 서버는 필수 파일 및 설치 패키지의 중앙 저장소 역할을 합니다. 이 소프트웨어는이 서버의 다른 서버 및 클라이언트에 배포 되거나 배포 됩니다.
 - **개발 클라이언트** -이 서버는 네이티브 응용 프로그램 뿐만 아니라 웹 브라우저에서 실행 되는 구성 요소를 포함 합니다.

### <a name="presentation-tier"></a>프레젠테이션 계층

이 계층에는 AIS (Application Interface Services), ADF (응용 프로그램 개발 프레임 워크) 및 JAS (Java 응용 프로그램 서버)와 같은 다양 한 구성 요소가 포함 되어 있습니다. 이 계층의 서버는 중간 계층의 서버와 통신 합니다. 트래픽을 수신 하는 포트 번호 및 URL에 따라 필요한 서버로 트래픽을 라우팅하는 부하 분산 장치에 의해 제어 되 됩니다. 고가용성을 위해 각 서버 유형의 인스턴스를 여러 개 배포 하는 것이 좋습니다.

이 계층의 구성 요소는 다음과 같습니다.
    
- **Ais (Application Interface Services** )-ais 서버는 Jd Edwards enterpriseone 모바일 엔터프라이즈 응용 프로그램과 Jd Edwards enterpriseone 간에 통신 인터페이스를 제공 합니다.
- **JAS (Java 응용 프로그램 서버)** -jas는 부하 분산 장치에서 요청을 수신 하 고이를 중간 계층에 전달 하 여 복잡 한 작업을 실행 합니다. JAS에는 간단한 비즈니스 논리를 실행 하는 기능이 있습니다.
- **BIP (BI Publisher server** )-이 서버는 Jd Edwards enterpriseone 응용 프로그램에 의해 수집 된 데이터를 기반으로 하는 보고서를 제공 합니다. 보고서에서 다양 한 템플릿을 기반으로 데이터를 표시 하는 방법을 디자인 하 고 제어할 수 있습니다.
- **Bss (비즈니스 서비스 서버** )-smi-s를 사용 하면 정보 교환 및 다른 Oracle 응용 프로그램과의 상호 운용성을 지원 합니다.
- **실시간 이벤트 서버 (rte)** -rte 서버를 사용 하면 JDE enterpriseone 시스템에서 발생 하는 트랜잭션에 대 한 외부 시스템에 대 한 알림을 설정할 수 있습니다. 구독자 모델을 사용 하며 타사 시스템이 이벤트를 구독할 수 있도록 합니다. 두 RTE 서버 모두에 대 한 요청 부하를 분산 하려면 서버가 클러스터에 있는지 확인 합니다.
- **Adf (응용 프로그램 개발 프레임 워크) 서버** -adf 서버는 Oracle adf를 사용 하 여 개발 된 Jd Edwards enterpriseone 응용 프로그램을 실행 하는 데 사용 됩니다. 이는 ADF 런타임을 사용 하는 Oracle WebLogic 서버에 배포 됩니다.

### <a name="middle-tier"></a>중간 계층

중간 계층에는 논리 서버와 일괄 처리 서버가 포함 되어 있습니다. 이 경우 두 서버는 동일한 가상 컴퓨터에 설치 됩니다. 그러나 프로덕션 시나리오의 경우에는 논리 서버와 batch 서버를 별도의 서버에 배포 하는 것이 좋습니다. 더 높은 가용성을 위해 여러 서버가 두 가용성 영역에 걸쳐 중간 계층에 배포 됩니다. Azure 부하 분산 장치를 만들고 이러한 서버를 백 엔드 풀에 배치 하 여 두 서버가 모두 활성 상태이 고 요청을 처리 하 고 있는지 확인 해야 합니다.

중간 계층의 서버는 프레젠테이션 계층 및 공용 부하 분산 장치에 있는 서버에서 요청을 받습니다. 프레젠테이션 계층 서브넷 및 부하 분산 장치 외의 모든 주소에서 트래픽을 거부 하도록 네트워크 보안 그룹 규칙을 설정 해야 합니다. 관리 목적으로 방호 호스트의 포트 22에서 트래픽을 허용 하도록 NSG 규칙을 설정할 수도 있습니다. 공용 부하 분산 장치를 사용 하 여 중간 계층의 Vm 간 요청 부하를 분산할 수 있습니다.

다음 두 구성 요소는 중간 계층에 있습니다.

- **논리 서버** -비즈니스 논리 또는 비즈니스 기능을 포함 합니다.
- Batch **서버** -일괄 처리에 사용 됨

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

Oracle의 PeopleSoft 응용 프로그램 제품군에는 인적 자원 및 금융 관리를 위한 소프트웨어가 포함 되어 있습니다. 응용 프로그램 제품군은 다중 계층화 되며 응용 프로그램에는 HRMS (인적 자원 관리 시스템), CRM (고객 관계 관리), FSCM (financials and 공급망 management) 및 EPM (enterprise performance management)이 포함 됩니다.

소프트웨어 제품군의 각 계층을 자체 서브넷에 배포 하는 것이 좋습니다. 응용 프로그램의 백 엔드 데이터베이스로 Oracle 데이터베이스 또는 Microsoft SQL Server 필요 합니다. 이 섹션에서는 Oracle 데이터베이스 백 엔드와 함께 PeopleSoft를 배포 하는 방법에 대해 자세히 설명 합니다.

다음은 클라우드 간 아키텍처에서 PeopleSoft 응용 프로그램 제품군을 배포 하기 위한 정식 아키텍처입니다 (그림 5).

![PeopleSoft 간 아키텍처](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*그림 5: PeopleSoft 간 아키텍처*

이 샘플 아키텍처에서 Azure의 가상 네트워크는 클라우드 간 상호 연결을 사용 하 여 OCI의 가상 클라우드 네트워크에 연결 됩니다. 응용 프로그램 계층은 Azure에서 설정 되는 반면 데이터베이스는 OCI로 설정 됩니다. 특정 포트에서 특정 서브넷의 트래픽만 허용 하도록 네트워크 보안 그룹을 사용 하 여 각 구성 요소를 자체 서브넷에 배포 하는 것이 좋습니다.

또한 한 지역의 두 가용성 영역에서 Oracle Data Guard를 사용 하 여 구성 된 항상 사용 가능한 Oracle 데이터베이스를 사용 하 여 Azure에서 배포 하기 위해 아키텍처를 완전히 적용할 수 있습니다. 다음 다이어그램 (그림 6)은이 아키텍처 패턴의 예입니다.

![PeopleSoft Azure 전용 아키텍처](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*그림 6: PeopleSoft Azure 전용 아키텍처*

다음 섹션에서는 높은 수준에서 다양 한 구성 요소에 대해 설명 합니다.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>애플리케이션 계층

응용 프로그램 계층에는 PeopleSoft 응용 프로그램 서버, PeopleSoft 웹 서버, 탄력적 검색 및 PeopleSoft Process Scheduler 인스턴스가 포함 됩니다. Azure 부하 분산 장치는 응용 프로그램 계층의 해당 서버에 라우팅되는 사용자의 요청을 허용 하도록 설정 됩니다.

고가용성을 위해 여러 가용성 영역에 걸쳐 응용 프로그램 계층에 있는 각 서버의 중복 인스턴스를 설정 하는 것이 좋습니다. Azure 부하 분산 장치는 여러 백 엔드 풀을 설정 하 여 각 요청을 올바른 서버로 보낼 수 있습니다.

### <a name="peopletools-client"></a>PeopleTools 클라이언트

PeopleTools Client는 개발, 마이그레이션 및 업그레이드와 같은 관리 작업을 수행 하는 데 사용 됩니다. PeopleTools Client는 응용 프로그램의 고가용성을 달성 하는 데 필요 하지 않기 때문에 PeopleTools Client의 중복 서버는 필요 하지 않습니다.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>다음 단계

[Terraform 스크립트](https://github.com/microsoft/azure-oracle) 를 사용 하 여 Azure에서 Oracle 앱을 설정 하 고 OCI를 사용 하 여 클라우드 간 연결을 설정 합니다.

OCI에 대 한 자세한 내용 및 백서는 [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) 설명서를 참조 하세요.
