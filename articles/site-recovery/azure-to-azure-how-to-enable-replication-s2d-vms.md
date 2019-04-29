---
title: Azure Site Recovery에서 S2D(스토리지 공간 다이렉트) VM에 대한 복제 구성 | Microsoft Docs
description: 이 문서에서는 Site Recovery를 사용하여 Azure 지역 간에 S2D를 사용하는 VM에 대한 복제를 구성하는 방법을 설명합니다.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/29/2019
ms.author: asgang
ms.openlocfilehash: 6c639d4503b170660abed5767e3571c8a2bf24b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790336"
---
# <a name="replicate-azure-virtual-machines-using-storage-spaces-direct-to-another-azure-region"></a>스토리지 공간 다이렉트를 사용하는 Azure 가상 머신을 다른 Azure 지역에 복제

이 문서에서는 스토리지 공간 다이렉트를 실행하는 Azure VM의 재해 복구를 사용하도록 설정하는 방법에 대해 설명합니다.

>[!NOTE]
>스토리지 공간 다이렉트 클러스터에는 크래시 일관성 복구 지점만 지원됩니다.
>

## <a name="introduction"></a>소개 
[S2D(스토리지 공간 다이렉트)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct)는 Azure에서 [게스트 클러스터](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure)를 만드는 방법을 제공하는 소프트웨어 정의 스토리지입니다.  Microsoft Azure의 게스트 클러스터는 IaaS VM으로 구성된 장애 조치 클러스터입니다. 이를 통해 호스팅된 VM 워크로드가 게스트 클러스터에서 장애 조치되어 단일 Azure VM에서 제공할 수 있는 것보다 더 높은 가용성의 애플리케이션 SLA를 달성할 수 있습니다. 이는 VM에서 SQL 또는 스케일 아웃 파일 서버 등과 같은 중요한 애플리케이션을 호스팅하는 시나리오에서 유용합니다.

## <a name="disaster-recovery-of-azure-virtual-machines-using-storage-spaces-direct"></a>스토리지 공간 다이렉트를 사용하는 Azure Virtual Machines의 재해 복구
일반적인 시나리오에서는 스케일 아웃 파일 서버와 같은 애플리케이션의 복원력을 높이기 위해 Azure에 가상 머신 게스트 클러스터가 있을 수 있습니다. 이렇게 하면 애플리케이션의 고가용성을 높일 수 있지만 모든 지역 수준 장애에 대해 Site Recovery를 사용하여 이러한 애플리케이션을 보호할 수 있습니다. Site Recovery는 데이터를 한 지역에서 다른 Azure 지역으로 복제하고 장애 조치 시 재해 복구 지역에 클러스터를 제공합니다.

아래 다이어그램에서는 스토리지 공간 다이렉트를 사용하는 두 개의 Azure VM 장애 조치 클러스터를 보여 줍니다.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)

 
- Windows 장애 조치 클러스터에 두 개의 Azure 가상 머신이 있으며, 각 가상 머신에는 둘 이상의 데이터 디스크가 있습니다.
- S2D는 데이터 디스크의 데이터를 동기화하고 저장소 풀로 동기화된 저장소를 제공합니다.
- 스토리지 풀은 장애 조치 클러스터에 CSV(클러스터 공유 볼륨)로 제공됩니다.
- 장애 조치 클러스터에서 데이터 드라이브에 CSV를 사용합니다.

**재해 복구 고려 사항**

1. 클러스터에 대해 [클라우드 감시](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp)를 설정하는 경우 재해 복구 지역에서 감시를 계속 유지합니다.
2. 가상 머신을 원본 지역과 다른 DR 지역의 서브넷으로 장애 조치하려면 장애 조치 후에 클러스터 IP 주소를 변경해야 합니다.  클러스터의 IP를 변경하려면 ASR [복구 계획 스크립트](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)를 사용해야 합니다.</br>
사용자 지정 스크립트 확장을 사용하여 VM 내에서 명령을 실행하는 [스크립트 샘플](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1)이 있습니다. 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>S2D 클러스터에 대한 Site Recovery 사용

1. 복구 서비스 자격 증명 모음 내에서 "+복제"를 클릭합니다.
1. 클러스터의 모든 노드를 선택하고 [다중 VM 일관성 그룹](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)의 멤버로 만듭니다.
1. 애플리케이션 일관성이 해제*된 복제 정책을 선택합니다(크래시 일관성 지원만 사용 가능).
1. 복제를 사용하도록 설정합니다.

   ![storagespacesdirect 보호](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. 복제된 항목으로 이동합니다. 그러면 두 가상 머신 상태가 모두 표시됩니다. 
3. 두 가상 머신이 모두 보호되고 있으며 다중 VM 일관성 그룹의 멤버로도 표시됩니다.

   ![storagespacesdirect 보호](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>복구 계획 만들기
복구 계획은 장애 조치(failover) 시에 다중 계층 애플리케이션에서 여러 계층의 시퀀싱을 지원합니다. 시퀀싱은 애플리케이션의 일관성을 유지하는 데 도움이 됩니다. 다중 계층 웹 애플리케이션에 대한 복구 계획을 생성할 때는 [Site Recovery를 사용하여 복구 계획 만들기](site-recovery-create-recovery-plans.md)에서 설명하는 단계를 수행합니다.

### <a name="adding-virtual-machines-to-failover-groups"></a>장애 조치 그룹에 가상 머신 추가

1.  가상 머신을 추가하여 복구 계획을 만듭니다.
2.  ‘사용자 지정’을 클릭하여 VM을 그룹화합니다. 기본적으로 모든 VM은 ‘그룹 1’에 속합니다.


### <a name="add-scripts-to-the-recovery-plan"></a>복구 계획에 스크립트 추가
애플리케이션이 제대로 작동하려면 장애 조치(failover) 후에 또는 테스트 장애 조치(failover) 중에 Azure 가상 머신에서 일부 작업을 수행해야 할 수 있습니다. 일부 장애 조치(failover) 사후 작업은 자동화할 수 있습니다. 예를 들어 여기서는 부하 분산 장치를 연결하고 클러스터 IP를 변경합니다.


### <a name="failover-of-the-virtual-machines"></a>가상 머신의 장애 조치 
[ASR 복구 계획](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)을 사용하여 가상 머신의 두 노드를 모두 장애 조치해야 합니다. 

![storagespacesdirect 보호](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>테스트 장애 조치(failover) 실행
1.  Azure Portal에서 Recovery Services 자격 증명 모음을 선택합니다.
2.  만든 복구 계획을 선택합니다.
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

[장애 복구 실행](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback)에 대해 자세히 알아봅니다.
