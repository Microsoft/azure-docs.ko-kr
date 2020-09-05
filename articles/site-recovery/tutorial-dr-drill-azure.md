---
title: Azure Site Recovery를 사용하여 Azure로 재해 복구 훈련 실행
description: Azure Site Recovery를 사용하여 온-프레미스에서 Azure로 재해 복구 훈련을 실행하는 방법에 대해 알아봅니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5bd9f5316f8b8799633de8c0c84c61424c0e4f4a
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "84699620"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Azure로 재해 복구 훈련 실행

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 Azure에 온-프레미스 머신에 대한 재해 복구 훈련을 실행하는 방법을 설명합니다. 훈련은 데이터 손실 없이 복제 전략의 유효성을 검사합니다.


이 문서는 온-프레미스 머신에 대한 재해 복구를 Azure에 설정하는 방법을 보여 주는 자습서 시리즈 중 네 번째 자습서입니다.

이 자습서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 테스트 장애 조치(failover)를 위한 격리된 네트워크 설정
> * 장애 조치(failover) 후 Azure VM에 연결할 준비
> * 단일 머신에 대해 테스트 장애 조치(failover) 실행

> [!NOTE]
> 자습서는 시나리오에 맞는 가장 간단한 배포 경로를 보여줍니다. 가능한 경우 기본 옵션을 사용하고 가능한 모든 설정과 경로를 보여주지 않습니다. 재해 복구 훈련 단계에 대해 자세히 알아보려면 [이 문서를 검토하세요](site-recovery-test-failover-to-azure.md).

## <a name="before-you-start"></a>시작하기 전에

이전 자습서 완료:

1. VMware VM, Hyper-V VM 및 물리적 머신의 온-프레미스 재해 복구가 Azure에 수행되도록 [Azure를 설정](tutorial-prepare-azure.md)했는지 확인합니다.
2. 재해 복구용 온-프레미스 [VMware](vmware-azure-tutorial-prepare-on-premises.md) 또는 [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) 환경을 준비합니다. 물리적 서버에 대한 재해 복구를 설정하는 경우 [지원 매트릭스](vmware-physical-secondary-support-matrix.md)를 검토합니다.
3. [VMware VM](vmware-azure-tutorial.md), [Hyper-V VM](hyper-v-azure-tutorial.md) 또는 [물리적 머신](physical-azure-disaster-recovery.md)에 대한 재해 복구를 설정합니다.
 

## <a name="verify-vm-properties"></a>VM 속성 확인

테스트 장애 조치(failover)를 실행하기 전에 먼저 VM 속성을 확인하고 [Hyper-V VM](hyper-v-azure-support-matrix.md#replicated-vms) 또는 [VMware VM](vmware-physical-azure-support-matrix.md#replicated-machines)이 Azure 요구 사항을 준수하는지 확인합니다.

1. **보호된 항목**에서 **복제된 항목**을 클릭하고 VM을 클릭합니다.
2. **복제된 항목** 창에 VM 정보, 상태 및 최신 사용 가능한 복구 지점의 요약이 제공됩니다. 자세한 내용을 보려면 **속성**을 클릭합니다.
3. **컴퓨팅 및 네트워크**에서 Azure 이름, 리소스 그룹, 대상 크기, 가용성 집합 및 관리 디스크 설정을 수정할 수 있습니다.
4. 장애 조치(failover) 후 Azure VM이 배치될 네트워크/서브넷 및 할당되는 IP 주소를 포함한 네트워크 설정을 보고 수정할 수 있습니다.
5. **디스크**에서 VM의 운영 체제 및 데이터 디스크에 대한 정보를 볼 수 있습니다.

## <a name="create-a-network-for-test-failover"></a>테스트 장애 조치(Failover)를 위한 네트워크 만들기

테스트 장애 조치(Failover)를 위해 각 VM에 대한 **Compute 및 네트워크** 설정의 특정 프로덕션 복구 사이트 네트워크와는 격리된 네트워크를 선택합니다. 사용자가 만드는 Azure Virtual Network는 기본적으로 다른 네트워크와 격리됩니다. 테스트 네트워크는 프로덕션 네트워크와 비슷해야 합니다.

- 테스트 네트워크에는 프로덕션 네트워크와 같은 수의 서브넷이 있어야 합니다. 서브넷의 이름은 같아야 합니다.
- 테스트 네트워크에는 동일한 IP 주소 범위를 사용해야 합니다.
- 테스트 네트워크의 DNS를 **Compute 및 네트워크** 설정의 DNS VM에 대해 지정된 IP 주소로 업데이트합니다. 자세한 내용은 [Active Directory의 테스트 장애 조치(failover) 시 고려 사항](site-recovery-active-directory.md#test-failover-considerations)을 읽어보세요.

## <a name="run-a-test-failover-for-a-single-vm"></a>VM에 대해 테스트 장애 조치(failover) 실행

테스트 장애 조치(failover)를 실행하면 다음 상황이 발생합니다.

1. 필수 구성 요소 확인은 장애 조치(failover)에 필요한 모든 조건이 충족되었는지 확인하기 위해 실행합니다.
2. 장애 조치(failover)는 데이터를 처리하며 이 데이터에서 Azure VM이 만들어질 수 있습니다. 최신 복구 시점을 선택하는 경우 해당 데이터에서 복구 시점이 만들어집니다.
3. 이전 단계에서 처리한 데이터를 사용하여 Azure VM이 만들어집니다.

다음과 같이 테스트 장애 조치(Failover)를 실행합니다.

1. **설정** > **복제된 항목**에서 VM > **+테스트 장애 조치(Failover)** 를 클릭합니다.
2. 이 자습서에서는 **가장 최근에 처리된** 복구 지점을 선택합니다. 그러면 사용 가능한 최신 시점으로 VM을 장애 조치(failover)합니다. 타임스탬프가 표시됩니다. 이 옵션을 사용하면 데이터를 처리하는 데 시간을 소비하지 않으므로 낮은 RTO(복구 시간 목표)가 제공됩니다.
3. **테스트 장애 조치(Failover)** 에서 장애 조치(Failover)가 발생한 후에 Azure VM이 연결될 대상 Azure 네트워크를 선택합니다.
4. **확인**을 클릭하여 장애 조치(failover)를 시작합니다. VM을 클릭하여 해당 속성을 열어 진행률을 추적할 수 있습니다. 또는 자격 증명 모음 이름 > **설정** > **작업** >
   **Site Recovery 작업**에서 **테스트 장애 조치(Failover)** 작업을 클릭하면 됩니다.
5. 장애 조치가 완료되면 Azure Portal > **Virtual Machines**에서 Azure VM 복제본이 표시됩니다. VM의 크기가 적당하고, VM이 올바른 네트워크에 연결되어 있으며 실행 중인지 확인합니다.
6. 이제 Azure에서 복제된 VM에에 연결할 수 있습니다.
7. 테스트 장애 조치(failover) 중에 만든 Azure VM을 삭제하려면 VM에서 **테스트 장애 조치(failover) 정리**를 클릭합니다. **참고**에서 테스트 장애 조치와 관련된 모든 관측 내용을 기록하고 저장합니다.

일부 시나리오에서는 장애 조치(failover)를 위해서는 추가 처리가 필요하며 이러한 작업을 완료하는 데는 약 8~10분이 소요됩니다. VMware Linux 컴퓨터, DHCP 서비스가 사용되도록 설정되지 않은 VMware VM과 부팅 드라이버인 storvsc, vmbus, storflt, intelide, atapi가 없는 VMware VM의 경우 테스트 장애 조치(failover)가 더 오래 걸릴 수 있습니다.

## <a name="connect-after-failover"></a>장애 조치(Failover) 후 연결

장애 조치(failover) 후 RDP/SSH를 사용하여 Azure VM에 연결하려면 [연결을 준비](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)합니다. 장애 조치(failover) 후 연결 문제가 발생하는 경우 [문제 해결 가이드](site-recovery-failover-to-azure-troubleshoot.md)를 따릅니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [VMware VM의 장애 조치(failover) 및 장애 복구(failback) 실행](vmware-azure-tutorial-failover-failback.md)
> [Hyper-V VM의 장애 조치(failover) 및 장애 복구(failback) 실행](hyper-v-azure-failover-failback-tutorial.md)
> [물리적 머신의 장애 조치(failover) 및 장애 복구(failback) 실행](physical-to-azure-failover-failback.md)
