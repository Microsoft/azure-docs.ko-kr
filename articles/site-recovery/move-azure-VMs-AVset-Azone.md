---
title: Azure Site Recovery를 사용하여 가용성 영역이 있는 Azure 지역으로 VM 이동
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 7d92311dfa699247995c7ded3e3930e19a9a537a
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86135473"
---
# <a name="move-azure-vms-into-availability-zones"></a>가용성 영역으로 Azure VM 이동
Azure 프로젝트의 가용성 영역은 데이터 센터 오류로부터 애플리케이션과 데이터를 보호할 수 있도록 지원합니다. 각 가용성 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 복원력을 보장하려면 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다. 지역 내에서 가용성 영역의 물리적 구분은 애플리케이션 및 데이터를 데이터 센터 오류로부터 보호할 수 있도록 합니다. 가용성 영역을 사용하여 Azure는 이제 VM(가상 머신) 작동 시간에 대해 99.99% SLA(서비스 수준 약정)를 제공합니다. 가용성 영역은 [가용성 영역을 지원하는 지역](../availability-zones/az-region.md)에 언급된 대로 선택된 지역에서 지원됩니다.

VM을 특정 지역에 *단일 인스턴스*로 배포했으며 이러한 VM을 가용성 영역으로 이동하여 가용성을 높이려는 경우 Azure Site Recovery를 사용하면 됩니다. 이 작업을 다음과 같이 좀 더 자세히 분석할 수 있습니다.

- 단일 인스턴스 VM을 대상 Azure 지역의 가용성 영역으로 이동
- 가용성 집합의 VM을 대상 Azure 지역의 가용성 영역으로 이동

> [!IMPORTANT]
> 현재 Azure Site Recovery는 한 지역에서 다른 지역으로 VM 이동을 지원합니다. 일부 지역에서는 지역 내의 영역 간 이동만 지원합니다. [자세히 알아보기](./azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="check-prerequisites"></a>필수 구성 요소 확인

- 대상 지역에서 [가용성 영역을 지원](../availability-zones/az-region.md)하는지 여부를 확인합니다. 선택한 [원본 지역/대상 지역 조합이 지원되는지](./azure-to-azure-support-matrix.md#region-support) 확인합니다. 대상 지역에서 합리적인 의사 결정을 진행합니다.
- [시나리오 아키텍처 및 구성 요소](azure-to-azure-architecture.md)를 이해해야 합니다.
- [제한 사항 및 요구 사항 지원](azure-to-azure-support-matrix.md)을 검토합니다.
- 계정 권한을 확인합니다. Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 관리자입니다. 구독 관리자가 아닌 경우에는 필요한 권한을 할당해 줄 것을 관리자에게 요청합니다. VM 복제를 사용하도록 설정하고 최종적으로 Azure Site Recovery를 사용하여 대상에 데이터를 복사하려면 다음 권한이 필요합니다.

    1. Azure 리소스에서 VM을 만들 수 있는 권한. 기본 제공되는 *Virtual Machine 참가자* 역할에는 다음을 포함하여 이러한 권한이 있습니다.
        - 선택한 리소스 그룹에서 VM을 만들 수 있는 권한
        - 선택한 가상 네트워크에서 VM을 만들 수 있는 권한
        - 선택한 스토리지 계정에 대한 쓰기 권한

    2. Azure Site Recovery 작업을 관리하기 위한 권한. *Site Recovery 참가자* 역할에는 Recovery Services 자격 증명 모음에서 Site Recovery 작업을 관리하는 데 필요한 모든 사용 권한이 있습니다.

## <a name="prepare-the-source-vms"></a>원본 VM 준비

1. Site Recovery를 사용하여 가용성 영역으로 VM을 이동하려면 VM에서 관리 디스크를 사용해야 합니다. 비관리형 디스크를 사용하는 기존 Windows VM을 Managed Disks를 사용하도록 변환할 수 있습니다. [비관리형 디스크에서 Managed Disks로 Windows 가상 머신 변환](../virtual-machines/windows/convert-unmanaged-to-managed-disks.md)의 단계를 따릅니다. 가용성 집합을 *관리형*으로 구성해야 합니다.
2. 이동할 Azure VM에 모든 최신 루트 인증서가 있는지 확인합니다. 최신 루트 인증서가 없는 경우 보안 제약 조건으로 인해 대상 Azure 지역에 데이터를 복사하도록 설정할 수 없습니다.

3. Windows VM의 경우 VM에 최신 Windows 업데이트를 모두 설치하여 신뢰할 수 있는 모든 루트 인증서가 컴퓨터에 있도록 합니다. 연결이 끊어진 환경에서 조직의 표준 Windows 업데이트 및 인증서 업데이트 프로세스를 따릅니다.

4. Linux VM의 경우 Linux 배포자가 제공한 지침에 따라 신뢰할 수 있는 최신 루트 인증서 및 인증서 해지 목록을 VM에 가져옵니다.
5. 이동할 VM의 네트워크 연결을 제어하기 위해 인증 프록시를 사용하지 않도록 합니다.

6. 이동하려는 VM이 인터넷에 액세스할 수 없고 방화벽 프록시를 사용하여 아웃바운드 액세스를 제어하는 경우 [아웃바운드 네트워크 연결 구성](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms)에서 요구 사항을 확인합니다.

7. 원본 네트워킹 레이아웃 및 현재 확인에 사용하는 모든 리소스(부하 분산 장치, NSG, 공용 IP 등)를 식별합니다.

## <a name="prepare-the-target-region"></a>대상 Azure 지역 준비

1. Azure 구독에서 재해 복구에 사용되는 대상 지역에 VM을 만들 수 있도록 허용하는지 확인합니다. 필요한 경우 지원 팀에 문의하여 필요한 할당량을 사용하도록 설정합니다.

2. 구독에 원본 VM과 동일한 크기의 VM을 지원할 수 있을 만큼 충분한 리소스가 있는지 확인합니다. Site Recovery를 사용하여 대상에 데이터를 복사하는 경우 Site Recovery는 동일한 크기 또는 가장 비슷한 크기를 대상 VM에 선택합니다.

3. 원본 네트워킹 레이아웃에서 식별된 모든 구성 요소의 대상 리소스를 만듭니다. 이 작업을 수행하면 대상 Azure 지역으로 전환한 후 원본에서 사용하던 모든 기능을 VM에서 그대로 사용할 수 있습니다.

    > [!NOTE]
    > 사용자가 원본 VM에 대해 복제를 사용하도록 설정하면 Azure Site Recovery는 가상 네트워크 및 스토리지 계정을 자동으로 검색하고 만듭니다. 이러한 리소스를 미리 만든 후 복제 사용 설정 단계의 일환으로 VM에 할당할 수도 있습니다. 하지만 그 외의 리소스는 뒷부분에 설명된 것처럼 대상 Azure 지역에 수동으로 만들어야 합니다.

     다음 문서에서 원본 VM 구성에 따라 가장 일반적으로 사용되는 네트워크 리소스를 만드는 방법을 참조하세요.

    - [네트워크 보안 그룹](../virtual-network/manage-network-security-group.md)
    - [부하 분산 장치](../load-balancer/index.yml)
    - [공용 IP](../virtual-network/virtual-network-public-ip-address.md)
    
   그 외의 네트워킹 구성 요소는 네트워킹 [설명서](../index.yml?pivot=products&panel=network)를 참조하세요.

    > [!IMPORTANT]
    > 대상에서 영역 중복 부하 분산 장치를 사용해야 합니다. [Standard Load Balancer 및 가용성 영역](../load-balancer/load-balancer-standard-availability-zones.md)에 대해 자세히 알아볼 수 있습니다.

4. 대상 Azure 지역으로 최종 전환하기 전에 구성을 테스트하려면 수동으로 대상 Azure 지역에 [비프로덕션 네트워크를 만듭니다](../virtual-network/quick-create-portal.md). 이 방법은 프로덕션 환경을 최소로만 중단하므로 권장됩니다.

## <a name="enable-replication"></a>복제 사용
다음 단계는 Azure Site Recovery를 사용하여 대상 Azure 지역으로 데이터를 복제하도록 설정한 후 최종적으로 가용성 영역으로 이동하는 방법을 안내합니다.

> [!NOTE]
> 이러한 단계는 단일 VM에 대한 단계입니다. 동일한 단계를 여러 VM으로 확장할 수 있습니다. Recovery Services 자격 증명 모음으로 이동한 후 **+ 복제**를 선택하고 관련 VM을 함께 선택합니다.

1. Azure Portal에서 **가상 머신**을 선택하고, 가용성 영역으로 이동할 VM을 선택합니다.
2. **작업**에서 **재해 복구**를 선택합니다.
3. **재해 복구 구성** > **대상 지역**에서 복제할 대상 지역을 선택합니다. 이 Azure 지역에서 [가용성 영역](../availability-zones/az-region.md)을 지원하는지 확인합니다.

    ![대상 지역 선택](media/azure-vms-to-zones/enable-rep-1.PNG)

4. 완료되면 **다음: 고급 설정**을 선택합니다.
5. 대상 구독, 대상 VM 리소스 그룹 및 가상 네트워크에 적합한 값을 선택합니다.
6. **가용성** 섹션에서 VM을 이동하려는 가용성 영역을 선택합니다. 
   > [!NOTE]
   > 가용성 집합 또는 가용성 영역에 대한 옵션이 보이지 않으면 [필수 구성 요소](#prepare-the-source-vms)를 충족하고 원본 VM [준비](#prepare-the-source-vms)가 완료되었는지 확인합니다.
  
    ![가용성 영역을 선택하기 위한 선택 항목](media/azure-vms-to-zones/enable-rep-2.PNG)

7. **복제 사용**을 선택합니다. 이 작업을 수행하면 VM에 대해 복제를 활성화하는 작업이 시작됩니다.

## <a name="check-settings"></a>설정 확인

복제 작업이 완료되면 복제 상태를 확인하고, 복제 설정을 수정하고, 배포를 테스트할 수 있습니다.

1. VM 메뉴에서 **재해 복구**를 선택합니다.
2. 복제 상태, 생성된 복구 지점, 원본 및 대상 지역을 지도에서 확인할 수 있습니다.

   ![복제 상태](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>구성 테스트

1. 가상 머신 메뉴에서 **재해 복구**를 선택합니다.
2. **테스트 장애 조치(failover)** 아이콘을 선택합니다.
3. **테스트 장애 조치(Failover)** 에서 장애 조치에 사용할 복구 지점을 선택합니다.

   - **가장 최근에 처리됨**: VM을 Site Recovery 서비스에서 처리된 최신 복구 지점으로 장애 조치합니다. 타임스탬프가 표시됩니다. 이 옵션을 사용하면 데이터를 처리하는 데 시간을 소비하지 않으므로 낮은 RTO(복구 목표 시간)가 제공됩니다.
   - **최신 앱 일치**: 모든 VM을 최신 앱 일치 복구 시점으로 장애 조치합니다. 타임스탬프가 표시됩니다.
   - **Custom**: 복구 시점을 선택합니다.

3. 구성을 테스트하기 위해 Azure VM을 이동할 테스트 대상 Azure 가상 네트워크를 선택합니다. 

    > [!IMPORTANT]
    > VM을 이동할 대상 Azure 지역의 프로덕션 네트워크가 아닌 별도의 Azure VM 네트워크를 오류 테스트에 사용하는 것이 좋습니다.

4. 이동 테스트를 시작하려면 **확인**을 선택합니다. 진행률을 추적하려면 VM을 선택하여 해당 속성을 엽니다. 또는 자격 증명 모음 이름 > **설정** > **작업** > **Site Recovery 작업**에서 **테스트 장애 조치** 작업을 선택하면 됩니다.
5. 장애 조치가 완료되면 Azure Portal > **Virtual Machines**에서 Azure VM 복제본이 표시됩니다. VM이 실행 중이고, 규모가 적절히 조정되었으며, 적절한 네트워크에 연결되어 있는지 확인하세요.
6. 이동 테스트 과정에서 만들어진 VM을 삭제하려면 복제된 항목에서 **테스트 장애 조치(failover) 정리**를 선택합니다. 테스트와 연관된 모든 관측 내용을 **참고**에 기록하고 저장합니다.

## <a name="move-to-the-target-region-and-confirm"></a>대상 Azure 지역으로 이동하고 확인

1.  가상 머신 메뉴에서 **재해 복구**를 선택합니다.
2. **장애 조치(failover)** 아이콘을 선택합니다.
3. **장애 조치(failover)** 에서 **최신**을 선택합니다. 
4. **장애 조치(failover)를 시작하기 전에 컴퓨터를 종료합니다.** 를 선택합니다. Site Recovery는 장애 조치(failover)를 트리거하기 전에 원본 VM을 종료하려고 합니다. 종료가 실패하더라도 장애 조치는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다. 
5. 작업이 완료되면 VM이 예상대로 대상 Azure 지역에 표시되는지 확인합니다.
6. **복제된 항목**에서 VM을 마우스 오른쪽 단추로 클릭하고 **커밋**을 클릭합니다. 대상 Azure 지역으로 이동하는 프로세스가 끝났습니다. 커밋 작업이 완료될 때까지 기다립니다.

## <a name="discard-the-resource-in-the-source-region"></a>원본 Azure 지역의 리소스 삭제

VM으로 이동합니다. **복제 사용 안 함**을 선택합니다. 이 작업을 수행하면 VM에 대한 데이터 복사 프로세스가 중지됩니다.  

> [!IMPORTANT]
> 이전 단계를 완료하여 이동 후에 Site Recovery 복제에 대해 요금이 부과되지 않도록 합니다. 원본 복제 설정이 자동으로 정리됩니다. 복제의 일부로 설치되는 Site Recovery 확장은 제거되지 않았으며 수동으로 제거해야 합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 가용성 집합 또는 가용성 영역으로 Azure VM을 이동하여 가용성을 높였습니다. 이제 이동한 VM에 대해 재해 복구를 설정할 수 있습니다.

> [!div class="nextstepaction"]
> [마이그레이션 후 재해 복구 설정](azure-to-azure-quickstart.md)
