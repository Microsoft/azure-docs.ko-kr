---
title: Azure Site Recovery를 사용하여 다중 계층 SAP NetWeaver 앱 배포를 위한 재해 복구 설정 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 SAP NetWeaver 애플리케이션 배포를 위한 재해 복구를 설정하는 방법에 대해 설명합니다.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 68efc039c5de5d7f61b7ce34e74c6c2cf4bad027
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471604"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>다중 계층 SAP NetWeaver 앱 배포를 위한 재해 복구 설정

대부분의 중대형 SAP 배포에는 어떤 형태로든 재해 복구 솔루션이 사용됩니다. 점점 더 많은 핵심 비즈니스 프로세스가 SAP과 같은 애플리케이션으로 옮겨가면서 강력하고 테스트 가능한 재해 복구 솔루션의 중요성이 높아지고 있습니다. Azure Site Recovery는 수많은 테스트를 거쳐 SAP 애플리케이션과 통합되었습니다. Site Recovery는 대부분의 온-프레미스 재해 복구 솔루션의 역량을 확장하며, 경쟁 솔루션 대비 총 소유 비용(TCO)이 낮습니다.

Site Recovery의 기능은 다음과 같습니다.
* 구성 요소를 Azure에 복제하여 **온-프레미스에서 실행 중인 SAP NetWeaver 및 비-NetWeaver 프로덕션 애플리케이션 보호를 활성화**합니다.
* 구성 요소를 다른 Azure 데이터 센터에 복사하여 **Azure에서 실행 중인 SAP NetWeaver 및 비-NetWeaver 프로덕션 애플리케이션 보호를 활성화**합니다.
* Site Recovery를 사용하여 **클라우드 마이그레이션을 간소화**하여 Azure에 SAP 배포를 마이그레이션합니다.
* SAP 애플리케이션 테스트를 위해 온디맨드로 프로덕션 클론을 만들어 **SAP 프로젝트 업그레이드, 테스트 및 프로토타입 생성을 단순화**합니다.

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 SAP NetWeaver 애플리케이션 배포를 보호하는 방법을 설명합니다. Site Recovery를 사용하여 다른 Azure 데이터 센터로 복제하여 3계층 SAP NetWeaver 배포를 보호하는 모범 사례도 보여줍니다. 또한, 지원되는 시나리오와 구성을 설명하고, 테스트 장애 조치(failover)(재해 복구 연습)와 실제 장애 조치(failover)를 수행하는 방법을 안내합니다.

## <a name="prerequisites"></a>필수 조건
시작하기 전에 다음 작업을 수행하는 방법을 알고 있어야 합니다.

* [Azure에 가상 머신 복제](azure-to-azure-walkthrough-enable-replication.md)
* [복구 네트워크 디자인](site-recovery-azure-to-azure-networking-guidance.md)
* [Azure로 테스트 장애 조치(failover) 수행](azure-to-azure-walkthrough-test-failover.md)
* [Azure로 장애 조치(failover) 수행](site-recovery-failover.md)
* [도메인 컨트롤러 복제](site-recovery-active-directory.md)
* [SQL Server 복제](site-recovery-sql.md)

## <a name="supported-scenarios"></a>지원되는 시나리오
다음과 같은 시나리오에서 Site Recovery를 사용하여 재해 복구 솔루션을 구현할 수 있습니다.
* 하나의 Azure 데이터 센터에서 실행되고 다른 Azure 데이터 센터로 복제되는 SAP 시스템(Azure-to-Azure 재해 복구). 자세한 내용은 [Azure-to-Azure 복제 아키텍처](https://aka.ms/asr-a2a-architecture)를 참조하세요.
* 온-프레미스 VMware(또는 물리적) 서버에서 실행되고 Azure 데이터 센터의 재해 복구 사이트로 복제되는 SAP 시스템(VMware-to-Azure 재해 복구). 이 시나리오에는 일부 추가 구성 요소가 필요합니다. 자세한 내용은 [VMware-to-Azure 복제 아키텍처](https://aka.ms/asr-v2a-architecture)를 참조하세요.
* 온-프레미스 Hyper-V에서 실행되고 Azure 데이터 센터의 재해 복구 사이트로 복제되는 SAP 시스템(Hyper-V-to-Azure 재해 복구). 이 시나리오에는 일부 추가 구성 요소가 필요합니다. 자세한 내용은 [Hyper-V-to-Azure 복제 아키텍처](https://aka.ms/asr-h2a-architecture)를 참조하세요.

이 문서에서는 **Azure-to-Azure** 재해 복구 시나리오를 바탕으로 Site Recovery의 SAP 재해 복구 기능을 보여줍니다. Site Recovery 복제는 애플리케이션마다 달라지지 않으므로 여기에서 설명하는 프로세스를 다른 시나리오에도 적용할 수 있습니다.

### <a name="required-foundation-services"></a>필요한 기반 서비스
이 문서에서 설명하는 시나리오에서는 다음과 같은 기반 서비스가 배포되어 있습니다.
* Azure ExpressRoute 또는 Azure VPN Gateway
* Azure에서 실행되는 하나 이상의 Active Directory 도메인 컨트롤러와 DNS 서버

Site Recovery를 배포하기 전에 위와 같은 인프라를 구현하는 것이 권장됩니다.

## <a name="reference-sap-application-deployment"></a>참조 SAP 애플리케이션 배포

이 참조 아키텍처는 Azure의 Windows 환경에서 고가용성을 통해 SAP NetWeaver를 실행하는 것을 보여 줍니다.  이 아키텍처는 조직의 요구 사항에 맞게 변경할 수 있는 특정 VM(가상 머신) 크기로 배포됩니다.

![일반적인 SAP 배포 패턴 다이어그램](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>재해 복구 고려 사항

DR(재해 복구)의 경우 장애 조치를 보조 지역으로 수행할 수 있어야 합니다. 각 계층은 서로 다른 전략을 사용하여 재해 복구(DR) 보호를 제공합니다.

#### <a name="vms-running-sap-web-dispatcher-pool"></a>SAP Web Dispatcher 풀을 실행하는 VM 
Web Dispatcher 구성 요소가 SAP 애플리케이션 서버 간의 SAP 트래픽을 위한 부하 분산 장치로 사용됩니다. Web Dispatcher 구성 요소의 고가용성을 달성하기 위해 Azure Load Balancer를 사용하여 분산 풀에서 사용할 수 있는 Web Dispatcher 간에 HTTP(S) 트래픽 분산에 대한 라운드 로빈 구성에서 병렬 Web Dispatcher 설정을 구현합니다. ASR(Azure Site Recovery)을 사용하여 복제되며, 자동화 스크립트는 재해 복구 영역에서 부하 분산 장치를 구성하는 데 사용됩니다. 

#### <a name="vms-running-application-servers-pool"></a>애플리케이션 서버 풀을 실행하는 VM
ABAP 애플리케이션 서버에 대한 로그온 그룹을 관리하기 위해 SMLG 트랜잭션이 사용됩니다. Central Services의 메시지 서버 내에서 부하 분산 기능을 사용하여 SAPGUI 및 RFC 트래픽용 SAP 애플리케이션 서버 풀 간의 워크로드를 분산합니다. Azure Site Recovery를 사용하여 복제 

#### <a name="vms-running-sap-central-services-cluster"></a>SAP Central Services 클러스터를 실행하는 VM
이 참조 아키텍처는 애플리케이션 계층의 VM에서 Central Services를 실행합니다. 단일 VM에 배포하는 경우(고가용성이 요구 사항이 아닌 경우의 일반적인 배포) Central Services는 잠재적인 SPOF(단일 실패 지점)입니다.<br>

고가용성 솔루션을 구현하려면 공유 디스크 클러스터 또는 파일 공유 클러스터 중 하나를 사용할 수 있습니다. 공유 디스크 클러스터에 대해 VM을 구성하려면 Windows Server 장애 조치(failover) 클러스터를 사용합니다. 클라우드 감시는 쿼럼 감시로 사용하는 것이 좋습니다. 
 > [!NOTE]
 > Azure Site Recovery는 클라우드 감시를 복제하지 않으므로 재해 복구 지역에서 클라우드 감시를 배포하는 것이 좋습니다.

장애 조치 클러스터 환경을 지원하기 위해 [SIOS DataKeeper 클러스터 버전](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8)에서 클러스터 노드가 소유한 독립 디스크를 복제하여 클러스터 공유 볼륨 기능을 수행합니다. Azure는 기본적으로 공유 디스크를 지원하지 않으므로 SIOS에서 제공하는 솔루션이 필요합니다. 

클러스터링을 처리하는 또 다른 방법은 파일 공유 클러스터를 구현하는 것입니다. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster)는 UNC 경로를 통해 /sapmnt 전역 디렉터리에 액세스하도록 Central Services 배포 패턴을 수정했습니다. 그래도 /sapmnt UNC 공유가 고가용성인지 확인하는 것이 좋습니다. 이 작업은 Central Services 인스턴스에서 Windows Server 2016의 SOFS(스케일 아웃 파일 서버) 및 S2D(저장소 공간 다이렉트) 기능이 있는 Windows Server 장애 조치 클러스터를 사용하여 수행할 수 있습니다. 
 > [!NOTE]
 > Azure Site Recovery 지원 저장소 공간 다이렉트 및 수동 노드의 SIOS Datakeeper를 사용 하 여 가상 머신의 복제 일치 시점만 충돌 되는 현재


## <a name="disaster-recovery-considerations"></a>재해 복구 고려 사항

Azure Site Recovery를 사용하여 Azure 지역에 걸쳐 전체 SAP 배포의 장애 조치(failover)를 오케스트레이션할 수 있습니다.
재해 복구를 설정하기 위한 단계는 아래와 같습니다. 

1. 가상 머신 복제 
2. 복구 네트워크 디자인
3.  도메인 컨트롤러 복제
4.  데이터 베이스 계층 복제 
5.  테스트 장애 조치(failover) 실행 
6.  장애 조치(failover) 수행 

다음은 이 예제에 사용된 각 계층의 재해 복구를 위한 권장 사항입니다. 

 **SAP 계층** | **권장 사항**
 --- | ---
**SAP Web Dispatcher 풀** |  Site Recovery를 사용하여 복제 
**SAP 애플리케이션 서버 풀** |  Site Recovery를 사용하여 복제 
**SAP Central Services 클러스터** |  Site Recovery를 사용하여 복제 
**Active Directory 가상 머신** |  Active Directory 복제 
**SQL Database 서버** |  SQL Always On 복제

## <a name="replicate-virtual-machines"></a>가상 머신 복제

모든 SAP 애플리케이션 가상 머신을 Azure 재해 복구 데이터 센터로 복제하는 작업을 시작하려면 [Azure로 가상 머신 복제](azure-to-azure-walkthrough-enable-replication.md)의 지침을 따릅니다.


* Active Directory 및 DNS 보호에 대한 지침은 [Active Directory 및 DNS 보호](site-recovery-active-directory.md) 문서를 참조하세요.

* SQL Server에서 실행되는 데이터베이스 계층 보호에 관한 지침은 [SQL Server 보호](site-recovery-active-directory.md) 문서를 참조하세요.

## <a name="networking-configuration"></a>네트워킹 구성

고정 IP 주소를 사용하는 경우에는 가상 머신에 부여할 IP 주소를 지정할 수 있습니다. IP 주소를 설정하려면 **계산 및 네트워크 설정** > **네트워크 인터페이스 카드**로 이동합니다.

![Site Recovery 네트워크 인터페이스 카드 창에서 사설 IP 주소를 설정하는 방법을 보여주는 스크린샷](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>복구 계획 만들기
복구 계획은 장애 조치(failover) 시에 다중 계층 애플리케이션에서 여러 계층의 시퀀싱을 지원합니다. 시퀀싱은 애플리케이션의 일관성을 유지하는 데 도움이 됩니다. 다중 계층 웹 애플리케이션에 대한 복구 계획을 생성할 때는 [Site Recovery를 사용하여 복구 계획 만들기](site-recovery-create-recovery-plans.md)에서 설명하는 단계를 수행합니다.

### <a name="adding-virtual-machines-to-failover-groups"></a>장애 조치 그룹에 가상 머신 추가

1.  애플리케이션 서버, 웹 디스패처 및 SAP Central Service VM을 추가하여 복구 계획을 만듭니다.
2.  ‘사용자 지정’을 클릭하여 VM을 그룹화합니다. 기본적으로 모든 VM은 ‘그룹 1’에 속합니다.



### <a name="add-scripts-to-the-recovery-plan"></a>복구 계획에 스크립트 추가
애플리케이션이 제대로 작동하려면 장애 조치(failover) 후에 또는 테스트 장애 조치(failover) 중에 Azure 가상 머신에서 일부 작업을 수행해야 할 수 있습니다. 일부 장애 조치(failover) 사후 작업은 자동화할 수 있습니다. 예를 들어, 복구 계획에 해당 스크립트를 추가하여 DNS 항목을 업데이트하고 바인딩과 연결을 변경할 수 있습니다.


아래 ‘Azure로 배포’ 단추를 클릭하면 가장 일반적으로 사용되는 Azure Site Recovery 스크립트를 Automation 계정에 배포할 수 있습니다. 게시된 스크립트를 사용하는 경우 스크립트에 있는 지침을 따라야 합니다.

[![Azure에 배포](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. ‘그룹 1’에 사전 작업 스크립트를 추가하여 SQL 가용성 그룹을 장애 조치(Failover)합니다. 샘플 스크립트에 게시된 ‘ASR-SQL-FailoverAG’ 스크립트를 사용합니다. 반드시 스크립트에서 제공된 지침을 따르고 스크립트에 필요한 사항을 적절히 변경합니다.
2. 사후 작업 스크립트를 추가하여 웹 계층(그룹 1)의 장애 조치된 가상 머신에 부하 분산 장치를 연결합니다. 샘플 스크립트에 게시된 ‘ASR-AddSingleLoadBalancer’ 스크립트를 사용합니다. 반드시 스크립트에서 제공된 지침을 따르고 스크립트에 필요한 사항을 적절히 변경합니다.

![SAP 복구 계획](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>테스트 장애 조치(failover) 실행

1.  Azure Portal에서 Recovery Services 자격 증명 모음을 선택합니다.
2.  SAP 애플리케이션용으로 생성한 복구 계획을 선택합니다.
3.  **테스트 장애 조치**를 선택합니다.
4.  테스트 장애 조치(failover) 프로세스를 시작하려면 복구 지점과 Azure 가상 네트워크를 선택합니다.
5.  보조 환경이 가동 중인 경우 유효성 검사를 수행할 수 있습니다.
6.  유효성 검사가 완료되면 장애 조치(failover) 환경을 정리하기 위해 **테스트 장애 조치(failover) 정리**를 선택합니다.

자세한 내용은 [Site Recovery에서 Azure로 테스트 장애 조치(failover)](site-recovery-test-failover-to-azure.md)를 참조하세요.

## <a name="run-a-failover"></a>장애 조치(Failover) 실행

1.  Azure Portal에서 Recovery Services 자격 증명 모음을 선택합니다.
2.  SAP 애플리케이션용으로 생성한 복구 계획을 선택합니다.
3.  **장애 조치(failover)** 를 선택합니다.
4.  복구 지점을 선택하여 장애 조치(failover) 프로세스를 시작합니다.

자세한 내용은 [Site Recovery에서 장애 조치(failover)](site-recovery-failover.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* Site Recovery를 사용하여 SAP NetWeaver 배포를 위한 재해 복구 솔루션을 빌드하는 방법을 자세히 알아보려면 다운로드 가능한 백서인 [SAP NetWeaver: Azure Site Recovery를 사용하여 재해 복구 솔루션 빌드](https://aka.ms/asr-sap)를 참조하세요. 이 백서에서는 다양한 SAP 아키텍처의 권장 사항에 대해 설명하고, Azure의 SAP에서 지원되는 애플리케이션 및 VM 유형을 나열하며, 재해 복구 솔루션에서 사용할 수 있는 테스트 계획 옵션에 대해 설명합니다.
* Site Recovery를 사용하여 [다른 워크로드를 복제](site-recovery-workload.md)하는 방법에 대해 알아봅니다.
