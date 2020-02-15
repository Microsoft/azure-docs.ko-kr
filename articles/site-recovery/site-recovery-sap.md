---
title: Azure Site Recovery를 사용 하 여 SAP NetWeaver 재해 복구 설정
description: Azure Site Recovery를 사용 하 여 SAP NetWeaver에 대 한 재해 복구를 설정 하는 방법을 알아봅니다.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 29acd1b00d23e4f1c2f241027dadbbb406e5e049
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190787"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>다중 계층 SAP NetWeaver 앱 배포를 위한 재해 복구 설정

대부분의 중대형 SAP 배포에는 어떤 형태로든 재해 복구 솔루션이 사용됩니다. 점점 더 많은 핵심 비즈니스 프로세스가 SAP과 같은 애플리케이션으로 옮겨가면서 강력하고 테스트 가능한 재해 복구 솔루션의 중요성이 높아지고 있습니다. Azure Site Recovery는 수많은 테스트를 거쳐 SAP 애플리케이션과 통합되었습니다. Site Recovery은 대부분의 온-프레미스 재해 복구 솔루션의 기능을 초과 하 고 경쟁 솔루션 보다 총 소유 비용을 낮춥니다.

Site Recovery의 기능은 다음과 같습니다.
* 구성 요소를 Azure로 복제 하 여 온-프레미스에서 실행 되는 SAP NetWeaver 및 비 NetWeaver 프로덕션 응용 프로그램의 보호를 사용 하도록 설정 합니다.
* 구성 요소를 다른 Azure 데이터 센터에 복제 하 여 Azure에서 실행 되는 SAP NetWeaver 및 비 NetWeaver 프로덕션 응용 프로그램에 대 한 보호를 사용 하도록 설정 합니다.
* 사이트 복구를 사용하여 클라우드 마이그레이션을 간소화하여 Azure에 SAP 배포를 마이그레이션합니다.
* SAP 응용 프로그램 테스트를 위해 주문형 프로덕션 클론을 만들어 SAP 프로젝트 업그레이드, 테스트 및 프로토타입 생성을 간소화 합니다.

[Azure Site Recovery](site-recovery-overview.md)를 사용 하 여 SAP NetWeaver 응용 프로그램 배포를 보호할 수 있습니다. 이 문서에서는 Site Recovery를 사용 하 여 다른 Azure 데이터 센터에 복제 하는 경우 Azure에서 3 계층 SAP NetWeaver 배포를 보호 하기 위한 모범 사례를 설명 합니다. 이 문서에서는 지원 되는 시나리오 및 구성을 설명 하 고 테스트 장애 조치 (failover) 및 실제 장애 조치 (failover)를 수행 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 작업을 수행하는 방법을 알고 있어야 합니다.

* [Azure에 가상 머신 복제](azure-to-azure-walkthrough-enable-replication.md)
* [복구 네트워크 디자인](site-recovery-azure-to-azure-networking-guidance.md)
* [Azure로 테스트 장애 조치(failover) 수행](azure-to-azure-walkthrough-test-failover.md)
* [Azure로 장애 조치(failover) 수행](site-recovery-failover.md)
* [도메인 컨트롤러 복제](site-recovery-active-directory.md)
* [SQL Server 인스턴스 복제](site-recovery-sql.md)

## <a name="supported-scenarios"></a>지원되는 시나리오

다음과 같은 시나리오에서 Site Recovery를 사용하여 재해 복구 솔루션을 구현할 수 있습니다.
* 하나의 Azure 데이터 센터에서 실행 되는 SAP 시스템을 보유 하 고 있으며 다른 Azure 데이터 센터 (Azure-Azure 재해 복구)에 복제 하 고 있습니다. 
   자세한 내용은 [Azure-to-Azure 복제 아키텍처](https://aka.ms/asr-a2a-architecture)를 참조하세요.
* 온-프레미스 VMware (또는 물리적) 서버에서 실행 되는 SAP 시스템을 보유 하 고 있습니다. 또한 SAP 시스템을 Azure 데이터 센터의 재해 복구 사이트로 복제 합니다 (VMware에서 Azure로의 재해 복구). 
   이 시나리오에는 일부 추가 구성 요소가 필요합니다. 자세한 내용은 [VMware-to-Azure 복제 아키텍처](https://aka.ms/asr-v2a-architecture)를 참조하세요.
* Hyper-v 온-프레미스에서 실행 되는 SAP 시스템을 보유 하 고 있습니다. 또한 SAP 시스템을 Azure 데이터 센터 (Hyper-v-Azure 재해 복구)의 재해 복구 사이트로 복제 하 고 있습니다.
   이 시나리오에는 일부 추가 구성 요소가 필요합니다. 자세한 내용은 [Hyper-V-to-Azure 복제 아키텍처](https://aka.ms/asr-h2a-architecture)를 참조하세요.

이 문서에서는 **azure-azure** 재해 복구 시나리오를 사용 합니다. 이 시나리오에서는 Site Recovery의 SAP 재해 복구 기능을 보여 줍니다. Site Recovery 복제는 애플리케이션마다 달라지지 않으므로 여기에서 설명하는 프로세스를 다른 시나리오에도 적용할 수 있습니다.

### <a name="required-foundation-services"></a>필요한 기반 서비스
이 문서에서 설명하는 시나리오에서는 다음과 같은 기반 서비스가 배포되어 있습니다.
* Azure ExpressRoute 또는 Azure VPN Gateway
* Azure에서 실행 되는 하나 이상의 Azure Active Directory 도메인 컨트롤러 및 DNS 서버

Site Recovery를 배포하기 전에 위와 같은 인프라를 구현하는 것이 권장됩니다.

## <a name="reference-sap-application-deployment"></a>참조 SAP 애플리케이션 배포

이 참조 아키텍처는 Azure의 Windows 환경에서 고가용성을 제공 하는 SAP NetWeaver를 실행 합니다. 이 아키텍처는 조직의 요구 사항에 맞게 변경할 수 있는 특정 VM (가상 머신) 크기와 함께 배포 됩니다.

![일반적인 SAP 배포 패턴 다이어그램](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>재해 복구 고려 사항

재해 복구의 경우 보조 지역으로 장애 조치 (failover) 할 수 있어야 합니다. 각 계층은 다른 전략을 사용 하 여 재해 복구 보호를 제공 합니다.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>SAP 웹 디스패처 풀을 실행 하는 Vm

웹 디스패처 구성 요소는 SAP 응용 프로그램 서버 간의 SAP 트래픽에 대 한 부하 분산 장치로 작동 합니다. 웹 디스패처 구성 요소에 대 한 고가용성을 얻기 위해 Azure Load Balancer는 병렬 웹 디스패처 설정을 구현 합니다. 웹 디스패처는 분산 장치 풀에서 사용 가능한 웹 디스패처 간에 HTTP (S) 트래픽 분산을 위해 라운드 로빈 구성을 사용 합니다.

#### <a name="vms-running-application-servers-pools"></a>응용 프로그램 서버 풀을 실행 하는 Vm
SMLG 트랜잭션은 ABAP 응용 프로그램 서버에 대 한 로그인 그룹을 관리 합니다. 중앙 서비스의 메시지 서버 내에서 부하 분산 함수를 사용 하 여 SAPGUIs 및 RFC 트래픽에 대 한 SAP 응용 프로그램 서버 풀 간에 작업 부하를 분산 합니다. Site Recovery를 사용 하 여이 관리를 복제할 수 있습니다.

#### <a name="vms-running-sap-central-services-clusters"></a>SAP 중앙 서비스 클러스터를 실행 하는 Vm
이 참조 아키텍처는 애플리케이션 계층의 VM에서 Central Services를 실행합니다. 중앙 서비스는 단일 VM에서 잠재적으로 단일 실패 지점입니다. 일반적인 배포 및 고가용성은 요구 사항이 아닙니다.

고가용성 솔루션을 구현 하기 위해 공유 디스크 클러스터 또는 파일 공유 클러스터를 사용할 수 있습니다. 공유 디스크 클러스터에 대 한 Vm을 구성 하려면 Windows Server 장애 조치 (Failover) 클러스터를 사용 합니다. 쿼럼 감시로 클라우드 감시를 사용 하는 것이 좋습니다.

 > [!NOTE]
 > Site Recovery는 클라우드 감시를 복제 하지 않으므로 재해 복구 지역에 클라우드 감시를 배포 하는 것이 좋습니다.

[Sios DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) 은 장애 조치 (failover) 클러스터 환경을 지원 하기 위해 클러스터 공유 볼륨 함수를 수행 합니다. 함수에서 SIOS DataKeeper 클러스터는 클러스터 노드에서 소유 하는 독립 디스크를 복제 합니다. Azure는 기본적으로 공유 디스크를 지원 하지 않으므로 SIOS에서 제공 하는 솔루션이 필요 합니다.

파일 공유 클러스터를 구현 하 여 클러스터링을 처리할 수도 있습니다. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster)는 UNC 경로를 통해 /sapmnt 전역 디렉터리에 액세스하도록 Central Services 배포 패턴을 수정했습니다. /Sapmnt UNC 공유를 항상 사용 가능한 상태로 유지 하는 것이 좋습니다. 중앙 서비스 인스턴스를 확인할 수 있습니다. SOFS (Scale Out File Server)와 windows server 장애 조치 (Failover) 클러스터와 Windows Server 2016의 S2D (스토리지 공간 다이렉트) 기능을 사용 합니다.

 > [!NOTE]
 > 현재 Site Recovery는 저장소 공간 다이렉트를 사용 하는 가상 컴퓨터의 크래시 일관성 지점 복제와 SIOS Datakeeper의 수동 노드를 지원 합니다.


## <a name="more-disaster-recovery-considerations"></a>더 많은 재해 복구 고려 사항

Site Recovery를 사용 하 여 Azure 지역에서 전체 SAP 배포의 장애 조치를 오케스트레이션 할 수 있습니다.
재해 복구를 설정 하는 단계는 다음과 같습니다.

1. 가상 머신 복제
1. 복구 네트워크 디자인
1. 도메인 컨트롤러 복제
1. 데이터 베이스 계층 복제
1. 테스트 장애 조치(failover) 실행
1. 장애 조치(failover) 수행

다음은이 예에 사용 된 각 계층의 재해 복구에 대 한 권장 사항입니다.

 **SAP 계층** | **권장**
 --- | ---
**SAP Web Dispatcher 풀** |  Site Recovery를 사용 하 여 복제 
**SAP 애플리케이션 서버 풀** |  Site Recovery를 사용 하 여 복제 
**SAP Central Services 클러스터** |  Site Recovery를 사용 하 여 복제 
**Active Directory 가상 머신** |  Active directory 복제 사용 
**SQL Database 서버** |  SQL Server Always On 복제 사용

## <a name="replicate-virtual-machines"></a>가상 머신 복제

모든 SAP 애플리케이션 가상 머신을 Azure 재해 복구 데이터 센터로 복제하는 작업을 시작하려면 [Azure로 가상 머신 복제](azure-to-azure-walkthrough-enable-replication.md)의 지침을 따릅니다.

* Active Directory 및 DNS를 보호 하는 방법에 대 한 지침은 [Active Directory 및 dns를 보호 하는 방법을](site-recovery-active-directory.md)알아봅니다.

* SQL Server에서 실행 되는 데이터베이스 계층을 보호 하는 방법에 대 한 지침은 [SQL Server를 보호 하는 방법을](site-recovery-sql.md)알아봅니다.

## <a name="networking-configuration"></a>네트워킹 구성

고정 IP 주소를 사용하는 경우에는 가상 머신에 부여할 IP 주소를 지정할 수 있습니다. IP 주소를 설정하려면 **컴퓨팅 및 네트워크 설정** > **네트워크 인터페이스 카드**로 이동합니다.

![Site Recovery 네트워크 인터페이스 카드 창에서 개인 IP 주소를 설정하는 방법을 보여주는 스크린샷](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>복구 계획 만들기

복구 계획은 장애 조치(failover) 시에 다중 계층 애플리케이션에서 여러 계층의 시퀀싱을 지원합니다. 시퀀싱은 애플리케이션의 일관성을 유지하는 데 도움이 됩니다. 다중 계층 웹 애플리케이션에 대한 복구 계획을 생성할 때는 [Site Recovery를 사용하여 복구 계획 만들기](site-recovery-create-recovery-plans.md)에서 설명하는 단계를 수행합니다.

### <a name="add-virtual-machines-to-failover-groups"></a>장애 조치 그룹에 가상 머신 추가

1. 응용 프로그램 서버, 웹 디스패처 및 SAP Central services Vm을 추가 하 여 복구 계획을 만듭니다.
1. **사용자 지정** 을 선택 하 여 vm을 그룹화 합니다. 기본적으로 모든 Vm은 그룹 1의 일부입니다.

### <a name="add-scripts-to-the-recovery-plan"></a>복구 계획에 스크립트 추가
응용 프로그램이 제대로 작동 하려면 Azure 가상 머신에서 일부 작업을 수행 해야 할 수 있습니다. 장애 조치 (failover) 후 또는 테스트 장애 조치 (failover) 중에 이러한 작업을 수행 합니다. 또한 일부 사후 장애 조치 (failover) 작업을 자동화할 수 있습니다. 예를 들어, DNS 항목을 업데이트 하 고 복구 계획에 해당 스크립트를 추가 하 여 바인딩과 연결을 변경 합니다.

**Azure에 배포를**선택 하 여 가장 많이 사용 되는 Site Recovery 스크립트를 Azure Automation 계정에 배포할 수 있습니다. 게시 된 스크립트를 사용 하는 경우 스크립트의 지침을 따르세요.

[![Azure에 배포](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. 그룹 1에 사전 작업 스크립트를 추가 하 여 SQL Server 가용성 그룹을 장애 조치 (failover) 합니다. 샘플 스크립트에 게시 된 ASR-SQL-FailoverAG 스크립트를 사용 합니다. 스크립트의 지침에 따라 적절 하 게 스크립트를 변경 합니다.
1. 웹 계층 (그룹 1)의 장애 조치 (failover) 된 가상 컴퓨터에 부하 분산 장치를 연결 하는 사후 작업 스크립트를 추가 합니다. 샘플 스크립트에 게시 된 ASR-AddSingleLoadBalancer 스크립트를 사용 합니다. 스크립트의 지침에 따라 필요에 따라 스크립트에서 필요한 내용을 변경 합니다.

![SAP 복구 계획](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>테스트 장애 조치(failover) 실행

1. Azure Portal에서 Recovery Services 자격 증명 모음을 선택합니다.
1. SAP 애플리케이션용으로 생성한 복구 계획을 선택합니다.
1. **테스트 장애 조치**를 선택합니다.
1. 테스트 장애 조치(failover) 프로세스를 시작하려면 복구 지점과 Azure 가상 네트워크를 선택합니다.
1. 보조 환경이 가동 중인 경우 유효성 검사를 수행할 수 있습니다.
1. 유효성 검사가 완료 되 면 **테스트 장애 조치 정리**를 선택 하 여 장애 조치 (failover) 환경을 정리 합니다.

자세한 내용은 [Site Recovery에서 Azure로 테스트 장애 조치(failover)](site-recovery-test-failover-to-azure.md)를 참조하세요.

## <a name="run-a-failover"></a>장애 조치(Failover) 실행

1. Azure Portal에서 Recovery Services 자격 증명 모음을 선택합니다.
1. SAP 애플리케이션용으로 생성한 복구 계획을 선택합니다.
1. **장애 조치(failover)** 를 선택합니다.
1. 복구 지점을 선택하여 장애 조치(failover) 프로세스를 시작합니다.

자세한 내용은 [Site Recovery에서 장애 조치(failover)](site-recovery-failover.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* Site Recovery를 사용 하 여 SAP NetWeaver 배포에 대 한 재해 복구 솔루션을 구축 하는 방법에 대해 자세히 알아보세요. 다운로드 가능한 백서 [SAP NetWeaver: Site Recovery를 사용 하 여 재해 복구 솔루션 빌드](https://aka.ms/asr_sap)를 참조 하세요. 백서에서는 다양 한 SAP 아키텍처에 대 한 권장 사항을 설명 합니다. Azure에서 SAP에 대해 지원 되는 응용 프로그램 및 VM 유형을 볼 수 있습니다. 재해 복구 솔루션을 테스트 하기 위한 계획 옵션도 있습니다.
* Site Recovery를 사용하여 [다른 워크로드를 복제](site-recovery-workload.md)하는 방법에 대해 알아봅니다.
