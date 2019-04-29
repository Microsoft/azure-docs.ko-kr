---
title: Azure Site Recovery를 사용 하 여 Azure로 재해 복구에 대 한 VMware vm 복제를 사용 하도록 설정 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용 하 여 재해 복구를 위해 Azure로 복제에 대 한 VMware Vm을 사용 하도록 설정 하는 방법을 설명 합니다.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 4/18/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: ba55afbd62bbbc2290d1daaebf77becc249c1d8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60922817"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>VMware VM에 대해 Azure로의 복제를 사용하도록 설정

이 문서에서는 Azure로의 온-프레미스 VMware VM 복제를 사용하도록 설정하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 사용자가 다음 작업을 수행한 것으로 가정합니다.

- [온-프레미스 원본 환경 설정](vmware-azure-set-up-source.md)합니다.
- [Azure에서 대상 환경 설정](vmware-azure-set-up-target.md)합니다.

## <a name="before-you-start"></a>시작하기 전에
VMware 가상 머신을 복제 하는 경우이 정보 사항에 유의 해야 합니다.

* Azure 사용자 계정에 특정 [사용 권한](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)이 있어야 Azure에 새 가상 머신을 복제할 수 있습니다.
* VMware VM은 15분마다 검색됩니다. 15 분 정도 걸릴 수 이상 vm이 검색 후 Azure portal에 표시 합니다. 마찬가지로, 검색 15 분이 걸릴 수 있습니다 이상 vCenter 서버 또는 vSphere 호스트를 추가 합니다.
* 15 분 정도 걸릴 수 있습니다 (예: VMware 도구 설치) 가상 머신의 환경 변경 사항에 대 한 이상 포털에서 업데이트 합니다.
* VMware Vm에 대 한 마지막 검색 시간을 확인할 수 있습니다. 참조 된 **마지막 연락** 필드에 **구성 서버** vCenter server/vSphere 호스트에 대 한 페이지입니다.
* 예약된 된 검색까지 기다리지 않고 복제에 대 한 가상 컴퓨터를 추가 하려면 구성 서버를 강조 표시 (그러나 클릭 하지 않음), 선택한 **새로 고침**합니다.
* Virtual machine을 준비 하는 경우 복제를 설정한 경우 프로세스 서버에 Azure Site Recovery 모바일 서비스를 자동으로 설치 합니다.

## <a name="enable-replication"></a>복제 사용

이 섹션의 단계를 수행 하기 전에 다음 정보를 note:
* Azure Site Recovery는 이제 모든 새 복제에 대 한 관리 되는 디스크에 직접 복제합니다. 프로세스 서버는 대상 지역의 캐시 저장소 계정에 복제 로그를 기록합니다. 이러한 로그 복제본 관리 디스크의 복구 지점을 만드는 데 사용 됩니다.
* 장애 조치의 경우 선택한 복구 지점이 대상 관리 디스크를 만드는 데 사용 됩니다.
* 대상 저장소 계정에 복제 하도록 이전에 구성 된 Vm은 영향을 받지 않습니다.
* 새 가상 컴퓨터에 대 한 저장소 계정에 대 한 복제만 REST Representational State Transfer () API 및 Powershell을 통해 제공 됩니다. 저장소 계정에 복제 하기 위한 Azure REST API 버전 2016-08-10 또는 2018-01-10을 사용 합니다.

1. 로 **2 단계: 애플리케이션 복제** > **원본**을 클릭합니다. 처음에 대 한 복제를 활성화 하면 선택 **+ 복제** 추가 가상 컴퓨터에 대해 복제를 사용 하도록 자격 증명 모음에 있습니다.
1. **원본** 페이지 > **원본**에서 구성 서버를 선택합니다.
1. 에 대 한 **컴퓨터 종류**를 선택 **가상 머신** 또는 **물리적 컴퓨터**합니다.
1. **vCenter/vSphere 하이퍼바이저**에서 vSphere 호스트를 관리하는 vCenter Server를 선택하거나 해당 호스트를 선택합니다. 이 설정은 물리적 컴퓨터를 복제 하는 경우 관련이 없습니다.
1. 추가 프로세스 서버를 만들지 않은 경우 구성 서버로 사용할 프로세스 서버를 선택 합니다. 그런 다음 **확인**을 선택합니다.

    ![복제 소스 창 사용](./media/vmware-azure-enable-replication/enable-replication2.png)

1. 에 대 한 **대상**, 장애 조치 가상 머신을 만들려는 구독 및 리소스 그룹을 선택 합니다. 장애 조치 Vm에 대 한 Azure에서 사용 하려는 배포 모델을 선택 합니다.

1. Azure 네트워크 및 장애 조치 후 Azure Vm에서 연결할 서브넷을 선택 합니다. 네트워크는 사이트 복구 서비스 자격 증명 모음과 동일한 지역에 있어야 합니다.

   선택 **선택한 컴퓨터에 대해 지금 구성** 보호에 대해 선택한 모든 가상 컴퓨터에 네트워크 설정을 적용 합니다. 선택 **나중에 구성할** 가상 머신 당 Azure 네트워크를 선택 합니다. 네트워크가 없는 경우 만들어야 합니다. Azure Resource Manager를 사용 하 여 네트워크를 만들려는 선택 **새로 만들기**합니다. 해당 하는 경우 서브넷을 선택 하 고 선택한 **확인**합니다.
   
   ![복제 대상 창 사용](./media/vmware-azure-enable-replication/enable-rep3.png)

1. 에 대 한 **Virtual machines** > **가상 머신 선택**, 복제 하려는 각 가상 컴퓨터를 선택 합니다. 복제를 활성화 하는 가상 컴퓨터만 선택할 수 있습니다. 그런 다음 **확인**을 선택합니다. 참조 수 없거나 특정 가상 머신을 선택을 참조 하세요 [원본 컴퓨터는 Azure portal에 표시 되지 않으면](https://aka.ms/doc-plugin-VM-not-showing) 문제를 해결 합니다.

    ![복제 가상 머신 선택 창 사용](./media/vmware-azure-enable-replication/enable-replication5.png)

1. 에 대 한 **속성** > **속성을 구성할**, 프로세스 서버 가상 머신에서 Site Recovery 모바일 서비스를 자동으로 설치 하는 데 사용 하는 계정을 선택 합니다. 또한 대상 관리 되는 디스크 데이터 변동 패턴 기반 복제 유형을 선택 합니다.
10. 기본적으로 원본 가상 컴퓨터의 모든 디스크가 복제 됩니다. 복제에서 디스크를 제외 하려면 선택을 취소 합니다 **Include** 복제 하지 않으려는 모든 디스크에 대 한 확인란 합니다. 그런 다음 **확인**을 선택합니다. 나중에 추가 속성을 설정할 수 있습니다. 에 대해 자세히 알아보세요 [디스크를 제외](vmware-azure-exclude-disk.md)합니다.

    ![사용 하도록 설정 복제 구성 속성 창](./media/vmware-azure-enable-replication/enable-replication6.png)

1. 언제 **복제 설정을** > **복제 설정 구성**, 올바른 복제 정책이 선택 되어 있는지 확인 합니다. 복제 정책 설정을 수정할 수 있습니다 **설정을** > **복제 정책이** > ***정책 이름***  >  **설정 편집**합니다. 정책에 적용 되는 변경 내용을 적용할 복제 및 새 가상 머신.
1. 사용 하도록 설정 **다중 VM 일관성** 가상 컴퓨터를 복제 그룹으로 수집 하려는 경우. 그룹의 이름을 지정 하 고 선택한 **확인**합니다.

    > [!NOTE]
    >    * 복제 그룹에 가상 머신 함께 복제 되 고 장애 조치 시 공유 크래시 일관성 및 앱 일치 복구 지점을 갖습니다.
    >    * 워크로드를 미러링하도록 VM 및 물리적 서버를 함께 수집합니다. 다중 VM 일관성을 사용하면 워크로드 성능에 영향을 줄 수 있습니다. 가상 컴퓨터가 동일한 워크 로드를 실행 하 고 일관성이 필요한 경우에이 작업을 수행 합니다.

    ![복제 창 사용](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. **복제 사용**을 선택합니다. 진행률을 추적할 수는 **보호 사용** 에서 작업 **설정** > **작업** > **Site Recovery 작업**. **보호 완료** 작업이 실행된 후에는 가상 머신이 장애 조치(Failover)를 수행할 준비가 되어 있습니다.

## <a name="view-and-manage-vm-properties"></a>VM 속성 보기 및 관리

그런 다음 원본 가상 머신의 속성을 확인 합니다. Azure VM 이름이 [Azure 가상 머신 요구 사항](vmware-physical-azure-support-matrix.md#replicated-machines)을 준수해야 합니다.

1. 로 이동 **설정을** > **복제 된 항목**, 한 다음 가상 컴퓨터를 선택 합니다. 합니다 **Essentials** 페이지 VM의 설정 및 상태에 대 한 정보를 표시 합니다.
1. **속성**에서 해당 VM에 대한 복제 및 장애 조치(failover) 정보를 볼 수 있습니다.
1. **Compute 및 네트워크** > **Compute 속성**, 여러 VM 속성을 변경할 수 있습니다. 

    ![계산 및 네트워크 속성 창](./media/vmware-azure-enable-replication/vmproperties.png)

    * Azure VM 이름: 필요한 경우 Azure 요구 사항을 충족 하도록 이름을 수정 합니다.
    * 대상 VM 크기 또는 VM 유형: 기본 VM 크기를 기준으로 대상 Azure 지역에서에서 디스크 수, NIC 수, CPU 코어 수, 메모리 및 사용 가능한 VM 역할 크기를 포함 하는 몇 가지 매개 변수 선택 됩니다. Azure Site Recovery는 모든 조건을 충족 하는 첫 번째 사용 가능한 VM 크기를 선택 합니다. 장애 조치 전에 언제 든 지 요구 사항에 따라 다른 VM 크기를 선택할 수 있습니다. VM 디스크 크기는 또한 원본 디스크 크기를 기준으로 하 고 장애 조치 후에 변경할 수 있습니다 note 합니다. 디스크 크기 및 IOPS 속도에 대해 자세히 알아보려면 [Windows VM 디스크에 대 한 확장성 및 성능 목표](../virtual-machines/windows/disk-scalability-targets.md)합니다.

    *  리소스 그룹: 선택할 수 있습니다는 [리소스 그룹](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines)에서 가상 컴퓨터는 장애 조치 후의 일부가 됩니다. 장애 조치 전에 언제 든 지가이 설정을 변경할 수 있습니다. 장애 조치 후 가상 머신을 다른 리소스 그룹으로 마이그레이션하는 경우 해당 가상 컴퓨터에 대 한 보호 설정을 중단 합니다.
    * 가용성 집합: 선택할 수 있습니다는 [가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) 가상 컴퓨터가 장애 조치 후에 포함 되어야 하는 경우. 가용성 집합을 선택 하면 다음 정보를 염두에서에 둡니다.

        * 지정된 된 리소스 그룹에 속하는 가용성 집합만 나열 됩니다.  
        * 서로 다른 가상 네트워크에 있는 Vm 동일한 가용성 집합에 속할 수 없습니다.
        * 동일한 크기의 가상 머신만 가용성 집합의 일부가 될 수 있습니다.
1. 또한 대상 네트워크, 서브넷 및 Azure VM에 할당 된 IP 주소에 대 한 정보를 추가할 수 있습니다.
2. **디스크**에서 복제될 VM의 운영 체제 및 데이터 디스크를 볼 수 있습니다.

### <a name="configure-networks-and-ip-addresses"></a>네트워크 및 IP 주소 구성

대상 IP 주소를 설정할 수 있습니다. 주소를 제공 하지 않으면 장애 조치 가상 컴퓨터가 DHCP를 사용 합니다. 장애 조치(failover) 시 사용할 수 없는 주소를 설정하면 장애 조치(failover)가 작동하지 않습니다. 주소가 테스트 장애 조치에 사용할 수 있는 경우 테스트 장애 조치에 대 한 동일한 대상 IP 주소를 사용할 수 있습니다.

네트워크 어댑터 수가 지정 하는 대상 가상 머신에 대 한 다음과 같은 크기에 따라 결정 됩니다.

- 원본 가상 컴퓨터의 네트워크 어댑터 수가 대상 VM의 크기에 허용 되는 어댑터 수보다 작거나 인 경우 대상에 동일한 어댑터 수가 원본으로
- 원본 가상 머신의 어댑터의 수가 대상 VM의 크기에 허용 되는 수를 초과 하면 대상 크기 최대치가 사용 됩니다. 예를 들어, 원본 가상 컴퓨터에 두 네트워크 어댑터가 대상 VM의 크기가 4를 지 원하는 경우 대상 가상 컴퓨터에 두 어댑터가 있습니다. 원본 VM에 두 어댑터가 있지만 대상 크기가 하나만 지 원하는 경우 대상 VM에는 하나의 어댑터만 있습니다.
- 가상 머신에 네트워크 어댑터가 여러 개 있으면 모두 동일한 네트워크에 연결됩니다. 또한 첫 번째 목록에 표시 되는 어댑터가 합니다 *기본* Azure 가상 컴퓨터의 네트워크 어댑터입니다. 

### <a name="azure-hybrid-benefit"></a>Azure 하이브리드 혜택

Microsoft Software Assurance 고객은 Azure Hybrid Benefit을 사용는 Azure로 마이그레이션되는 Windows Server 컴퓨터에 대 한 라이선스 비용을 절감할 수 있습니다. 장점은 Azure 재해 복구에도 적용 됩니다. 자격이 있는 경우 Site Recovery는 장애 조치가 있는 경우 만드는 가상 컴퓨터에는 혜택을 할당할 수 있습니다. 이렇게 하려면 다음 단계를 수행하세요.
1. 로 이동 합니다 **컴퓨터 및 네트워크 속성** 복제 된 가상 머신의 합니다.
2. Azure 하이브리드 혜택을 받을 수 있는 Windows Server 라이선스가 있는지 묻는 메시지가 나타나면 답변 합니다.
3. 장애 조치 시 만들어지는 VM에 혜택을 적용 하는 데 사용할 수 있는 Software Assurance를 사용 하 여 적절 한 Windows Server 라이선스가 있는지 확인 합니다.
4. 복제 된 가상 컴퓨터에 대 한 설정을 저장 합니다.

[Azure 하이브리드 혜택](https://aka.ms/azure-hybrid-benefit-pricing)에 대해 자세히 알아봅니다.

## <a name="resolve-common-issues"></a>일반적인 문제 해결

* 각 디스크는 4TB 보다 작아야 합니다.
* OS 디스크는 동적 디스크가 아닌 기본 디스크를 해야 합니다.
* 세대 2/UEFI 사용 가상 컴퓨터 운영 체제 제품군, Windows이 고 부팅 디스크는 300GB 보다 작아야 합니다.

## <a name="next-steps"></a>다음 단계

가상 머신이 보호 되는 상태에 도달한 후 시도 [장애 조치](site-recovery-failover.md) Azure에 응용 프로그램 표시 여부를 확인 하려면.

* 복제를 사용하지 않도록 설정하려는 경우 [등록 및 보호 설정을 정리](site-recovery-manage-registration-and-protection.md)하는 방법을 알아봅니다.
* 설명 하는 방법 [Powershell을 사용 하 여 가상 머신에 대해 복제를 자동화](vmware-azure-disaster-recovery-powershell.md)합니다.
