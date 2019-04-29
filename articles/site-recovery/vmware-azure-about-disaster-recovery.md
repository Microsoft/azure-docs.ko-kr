---
title: Azure Site Recovery를 사용한 VMware VM에서 Azure로의 재해 복구 정보 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery 서비스를 사용하여 수행되는 VMware VM에서 Azure로의 재해 복구 개요를 제공합니다.
author: raynew
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 3/18/2019
ms.author: raynew
ms.openlocfilehash: 9d7b94500320315c1379ba3dfb8b6460bc105b49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61272696"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>VMware VM에서 Azure로의 재해 복구 정보

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 수행되는 온-프레미스 VMware VM에서 Azure로의 재해 복구 개요를 제공합니다.

## <a name="what-is-bcdr"></a>BCDR이란?

BCDR(비즈니스 연속성 및 재해 복구) 전략은 비즈니스가 계속 작동되고 실행되도록 도와줍니다. 계획된 가동 중단 및 예기치 않은 작동 중단 동안 BCDR은 데이터를 안전하고 사용 가능하게 유지하고, 앱이 계속 실행되도록 합니다. 지역 페어링 및 고가용성 저장소와 같은 플랫폼 BCDR 기능 외에도, Azure는 BCDR 솔루션의 통합된 일부로 Recovery Services를 제공합니다. Recovery Services에는 다음이 포함됩니다. 

- [Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup)은 온-프레미스 및 Azure VM 데이터를 백업합니다. 파일 및 폴더, 특정 워크로드 또는 전체 VM을 백업할 수 있습니다. 
- [Azure Site Recovery](site-recovery-overview.md)는 온-프레미스 컴퓨터 또는 Azure IaaS VM에서 실행되는 앱 및 워크로드대에 해 복원력 및 재해 복구를 제공합니다. Site Recovery는 복제를 오케스트레이션하고 중단이 발생하면 Azure로 장애 조치(Failover)합니다. 또한 Azure에서 기본 사이트로의 복구를 처리합니다. 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Site Recovery는 어떻게 재해 복구를 수행하나요?

1. Azure와 온-프레미스 사이트를 준비한 후 온-프레미스 컴퓨터에 대한 복제를 설정하고 사용하도록 지정합니다.
2. Site Recovery는 정책 설정에 따라 컴퓨터의 초기 복제를 오케스트레이션합니다.
3. 초기 복제 후에, Site Recovery는 델타 변경 내용을 Azure로 복제합니다. 
4. 모든 항목이 예상대로 복제되면 재해 복구 훈련을 실행합니다.
    - 이러한 훈련은 실제 요구가 있을 때 장애 조치(Failover)가 예상대로 진행되도록 하는 데 도움이 됩니다.
    - 또한 이러한 훈련 중에는 프로덕션 환경에 영향을 주지 않고 테스트 장애 조치(Failover)가 수행됩니다.
5. 중단이 발생하는 경우에 Azure로의 전체 장애 조치(Failover)를 실행합니다. 단일 컴퓨터를 장애 조치(Failover)하거나 여러 컴퓨터를 동시에 장애 조치(Failover)하는 복구 계획을 만들 수 있습니다.
6. 장애 조치 시 관리 디스크 또는 저장소 계정에 VM 데이터에서 Azure Vm은 만듭니다. 사용자는 Azure VM에서 앱 및 워크로드에 계속 액세스할 수 있습니다.
7. 온-프레미스 사이트를 다시 사용할 수 있게 되면 Azure에서 장애 복구(Failback)합니다.
8. 장애 복구(Failback)를 수행하고 기본 사이트에서 한 번 더 작업한 후에 온-프레미스 VM을 Azure로 다시 복제할 수 있습니다.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>내 작업 환경이 Azure로 재해 복구하기에 적합한지 어떻게 알 수 있나요?

Site Recovery는 지원되는 VMware VM 또는 실제 서버에서 실행 중인 모든 워크로드를 복제할 수 있습니다. 작업 환경에서 확인해야 하는 항목은 다음과 같습니다.

- VMware VM을 복제하는 경우 VMware 가상화 서버의 올바른 버전을 실행하고 있나요? [여기에서 확인하세요](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- 복제하려는 컴퓨터가 지원되는 운영 체제를 실행하고 있나요? [여기에서 확인하세요](vmware-physical-azure-support-matrix.md#replicated-machines).
- Linux 재해 복구의 경우 컴퓨터에서 지원되는 파일 시스템/게스트 저장소가 실행되고 있나요? [여기에서 확인하세요](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).
- 복제하려는 컴퓨터가 Azure 요구 사항을 충족하나요? [여기에서 확인하세요](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- 사용하는 네트워크 구성이 지원되나요? [여기에서 확인하세요](vmware-physical-azure-support-matrix.md#network).
- 사용하는 저장소 구성이 지원되나요? [여기에서 확인하세요](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>시작하기 전에 Azure에서 어떤 항목을 설정해야 하나요?

Azure에서 다음을 준비해야 합니다.

1. Azure 계정에 Azure에서 VM을 만들 수 있는 권한이 있는지 확인합니다.
2. 장애 조치 후 저장소 계정 또는 관리 되는 디스크에서 만든 경우 Azure Vm은 조인 하는 Azure 네트워크를 만듭니다.
3. Site Recovery에 대한 Azure Recovery Services 자격 증명 모음을 설정합니다. 이 자격 증명 모음은 Azure Portal에 있고, 배포, 구성, 오케스트레이션, 모니터링 작업과 Site Recovery 배포 문제를 해결하는 데 사용됩니다.

*도움이 더 필요하세요?*

Azure로 설정 하는 방법을 알아봅니다 [계정 확인](tutorial-prepare-azure.md#verify-account-permissions)을 만듭니다는 [네트워크](tutorial-prepare-azure.md#set-up-an-azure-network), 및 [자격 증명 모음 설정](tutorial-prepare-azure.md#create-a-recovery-services-vault)합니다.



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>시작하기 전에 온-프레미스에서 어떤 항목을 설정해야 하나요?

온-프레미스에서는 다음 작업을 수행해야 합니다.

1. 다음과 같이 몇 가지 계정을 설정해야 합니다.

    - VMware VM을 복제하는 경우 Site Recovery에서 vCenter Server 또는 vSphere ESXi 호스트에 액세스하여 VM을 자동으로 검색하기 위한 계정이 필요합니다.
    - 복제하려는 각 물리적 컴퓨터 또는 VM에 Site Recovery 모바일 서비스 에이전트를 설치하기 위한 계정이 필요합니다.

2. 아직 실행하지 않은 경우 VMware 인프라의 호환성을 확인해야 합니다.
3. 장애 조치(Failover) 후 Azure VM에 연결할 수 있는지 확인합니다. 온-프레미스 Windows 컴퓨터에 RDP를 설치하거나 Linux 컴퓨터에 SSH를 설치합니다.

*도움이 더 필요하세요?*
- [자동 검색](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) 및 [모바일 서비스 설치](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation)를 위한 계정을 준비합니다.
- VMware 설정이 호환되는지 [확인](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements)합니다.
- 장애 조치(Failover) 후에 Azure에 연결할 수 있도록 [준비](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover)합니다.
- 장애 조치(Failover) 후 Azure VM에 대한 IP 주소를 설정하는 방법에 대한 자세한 내용을 보려면 [이 문서를 읽어보세요](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>재해 복구 설정하려면 어떻게 하나요?

Azure 및 온-프레미스 인프라가 준비된 후에 재해 복구를 설정할 수 있습니다.

1. 배포해야 하는 구성 요소를 이해하려면 [VMware에서 Azure로의 아키텍처](vmware-azure-architecture.md), 및 [물리적 서버에서 Azure로의 아키텍처](physical-azure-architecture.md)를 검토합니다. 많은 구성 요소가 있으므로 이러한 구성 요소가 어떻게 잘 맞는지 이해하는 것이 중요합니다.
2. **원본 환경**: 배포의 첫 번째 단계로 복제 원본 환경 설정을 설정합니다. 복제할 대상과 복제할 위치를 지정합니다.
3. **구성 서버**: 온-프레미스 원본 환경에서 구성 서버를 설정해야 합니다.
    - 구성 서버는 단일 온-프레미스 컴퓨터입니다. VMware 재해 복구의 경우 다운로드 가능 OVF 템플릿에서 배포할 수 있는 VMware VM으로 배포하는 것이 좋습니다.
    - 구성 서버는 온-프레미스와 Azure 간의 통신을 조정합니다.
    - 다른 일부 구성 요소는 구성 서버 컴퓨터에서 실행됩니다.
        - 프로세스 서버, 최적화, 받고 Azure에서 캐시 저장소 계정에 복제 데이터를 보냅니다. 또한 복제하려는 컴퓨터에서 모바일 서비스의 자동 설치를 처리하고, VMware 서버에서 VM의 자동 검색을 수행합니다.
        - 마스터 대상 서버는 Azure에서 장애 복구 중 복제 데이터를 처리합니다.
    - 설정 중에 자격 증명 모음에 구성 서버가 등록되고, MySQL Server 및 VMware PowerCLI가 다운로드되고, 자동 검색 및 모바일 서비스 설치용으로 만든 계정이 지정됩니다.
4. **대상 환경**: 대상 Azure 환경 여 설정한 Azure 구독 및 네트워크 설정을 지정 합니다.
5. **복제 정책**: 복제 수행 방법을 지정합니다. 또한 복구 지점이 생성 및 저장되는 주기와 앱 일치 스냅숏을 만들지 여부가 설정됩니다.
6. **복제를 활성화합니다**. 온-프레미스 컴퓨터에 대해 복제를 사용하도록 설정합니다. 모바일 서비스를 설치하기 위한 계정을 만든 경우 컴퓨터에 대해 복제를 사용하도록 설정할 때 해당 계정이 설치됩니다. 

*도움이 더 필요하세요?*

- 이러한 단계를 빠르게 연습해보려면 [VMware 자습서](vmware-azure-tutorial.md)와 [실제 서버 연습](physical-azure-disaster-recovery.md)을 수행할 수 있습니다.
- 원본 환경 설정에 대해 [자세히 알아봅니다](vmware-azure-set-up-source.md).
- 구성 서버 요구 사항 및 VMware 복제를 위해 OVF 템플릿으로 구성 서버를 설정하는 방법을 [자세히 알아봅니다](vmware-azure-deploy-configuration-server.md). 어떤 이유로든 템플릿을 사용할 수 없거나 실제 서버를 복제하려는 경우 [이러한 지침을 사용](physical-azure-set-up-source.md#set-up-the-source-environment)합니다.
- 대상 설정에 대해 [자세히 알아봅니다](vmware-azure-set-up-target.md).
- 복제 정책 설정에 대해 [자세히 알아봅니다](vmware-azure-set-up-replication.md).
- 복제를 사용하도록 설정하고 복제에서 디스크를 [제외](vmware-azure-exclude-disk.md)하는 방법을 [알아봅니다](vmware-azure-enable-replication.md).


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>문제가 있는 경우 어떻게 해결하나요?

- 첫 번째 단계로, [배포를 모니터링](site-recovery-monitor-and-troubleshoot.md)하여 복제된 항목, 작업 및 인프라 문제를 확인하고 오류를 식별합니다.
- 초기 복제를 완료할 수 없거나 진행 중인 복제가 예상대로 작동하지 않을 경우 [이 문서를 검토](vmware-azure-troubleshoot-replication.md)에서 일반적인 오류 및 문제 해결 팁을 찾습니다.
- 복제하려는 컴퓨터의 모바일 서비스 자동 설치와 관련된 문제가 발생하는 경우 [이 문서](vmware-azure-troubleshoot-push-install.md)에서 일반적인 오류를 검토하세요.
- 장애 조치(Failover)가 예상대로 작동하지 않는 경우 [이 문서](site-recovery-failover-to-azure-troubleshoot.md)에서 일반적인 오류를 확인하세요.
- 장애 복구(Failback)가 작동하지 않는 경우 발생한 문제가 [이 문서](vmware-azure-troubleshoot-failback-reprotect.md)에 나오는지 확인하세요.



## <a name="next-steps"></a>다음 단계

이제 복제가 수행되었으므로 [재해 복구 훈련을 실행](tutorial-dr-drill-azure.md)하여 장애 조치(Failover)가 예상대로 작동하는지 확인해야 합니다. 
