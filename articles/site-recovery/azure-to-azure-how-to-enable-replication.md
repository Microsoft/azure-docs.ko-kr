---
title: Azure Site Recovery의 Azure VM에 대한 복제 구성 | Microsoft Docs
description: 이 문서에서는 Site Recovery를 사용하여 한 Azure 지역에서 다른 Azure 지역으로 Azure VM에 대한 복제를 구성하는 방법을 설명합니다.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 0dec8179cc9b69eeea679f517693e4debb9fb56e
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340695"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>다른 Azure 지역에 Azure Vm 복제


이 문서에서는 한 Azure 지역에서 다른 Azure 지역으로 Azure VM 복제를 사용하는 방법을 설명합니다.

## <a name="before-you-start"></a>시작하기 전에

이 문서에서는 Site Recovery 배포 준비 되어 있는지에 설명 된 대로 가정 합니다 [에서 Azure로 재해 복구 자습서](azure-to-azure-tutorial-enable-replication.md)합니다.

필수 구성 요소 위치에 있어야 하 고 Recovery Services 자격 증명 모음을 만들어야 합니다.


## <a name="enable-replication"></a>복제 사용

복제를 활성화합니다. 이 절차에서는 주 Azure 지역을 동아시아, 보조 지역을 동남 아시아라고 가정합니다.

1. 자격 증명 모음에서 **+복제**를 클릭합니다.
2. 다음 필드를 확인합니다.
   - **원본**: VM의 원점이며 이 경우 **Azure**입니다.
   - **원본 위치**: Vm을 보호 하려는 Azure 지역입니다. 이 그림에서는 원본 위치가 '동아시아'입니다.
   - **배포 모델**: 원본 머신의 Azure 배포 모델입니다.
   - **원본 구독**: 원본 Vm에 속한 구독입니다. 복구 서비스 자격 증명 모음이 있는 동일한 Azure Active Directory 테넌트 내에 있는 구독일 수 있습니다.
   - **리소스 그룹**: 원본 가상 머신이 속한 리소스 그룹입니다. 선택한 리소스 그룹 아래의 모든 VM은 다음 단계에서 보호를 위해 나열됩니다.

     ![복제 사용](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. **Virtual Machines > 가상 머신 선택**에서 복제하려는 각 VM을 클릭하여 선택합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 그런 후 **OK**를 클릭합니다.
    ![복제 활성화](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. **설정**에서 대상 사이트 설정을 선택적으로 구성할 수 있습니다.

   - **대상 위치**: 원본 가상 머신 데이터가 복제될 위치입니다. 선택한 컴퓨터 위치에 따라 Site Recovery에서 적합한 대상 지역 목록을 제공합니다. 대상 위치를 Recovery Services 자격 증명 모음 위치와 동일하게 유지하는 것이 좋습니다.
   - **대상 구독**: 재해 복구에 사용되는 대상 구독입니다. 기본적으로 대상 구독은 원본 구독과 동일합니다.
   - **대상 리소스 그룹**: 모든 복제된 가상 머신이 속하는 리소스 그룹입니다.
       - 기본적으로 Site Recovery는 이름에는 "asr" 접미사를 사용 하 여 대상 지역에 새 리소스 그룹을 만듭니다.
       - 이미 Site Recovery에서 만든 리소스 그룹이 있는 경우 다시 사용 됩니다.
       - 리소스 그룹 설정을 사용자 지정할 수 있습니다.
       - 대상 리소스 그룹의 위치는 원본 Vm 호스트 되는 지역 제외한 모든 Azure 지역 수 있습니다.
   - **대상 가상 네트워크**: 기본적으로 Site Recovery는 이름에는 "asr" 접미사를 사용 하 여 대상 지역에 새 가상 네트워크를 만듭니다. 이 가상 네트워크는 원본 네트워크에 매핑되고 이후의 모든 보호를 위해 사용됩니다. [자세히 알아봅니다](site-recovery-network-mapping-azure-to-azure.md) 를 확인해 보세요.
   - **대상 저장소 계정 (원본 VM managed disks를 사용 하지 않습니다)** : 기본적으로 Site Recovery는 원본 VM 스토리지 구성을 모방하는 새 대상 스토리지 계정을 만듭니다. 저장소 계정이 이미 있는 경우 다시 사용됩니다.
   - **복제본 관리 디스크 (원본 VM managed disks를 사용 하는 데 사용)** : Site Recovery는 원본 VM의 디스크 관리 되는 동일한 저장소 유형 (표준 또는 프리미엄)을 사용 하 여 원본 VM의 관리 디스크를 미러링합니다 대상 지역에 새 복제본 관리 디스크를 만듭니다.
   - **캐시 스토리지 계정**: Site Recovery는 원본 지역에 캐시 스토리지로 불리는 추가 스토리지 계정이 필요합니다. 원본 VM에서 발생하는 모든 변경 내용이 대상 위치로 복제되기 전에 추적되고 캐시 저장소 계정으로 전송됩니다.
   - **대상 가용성 세트**: 기본적으로 Site Recovery는 원본 지역에서 가용성 집합을 포함 된 Vm에 대 한 이름에 "asr" 접미사를 사용 하 여 대상 지역에 설정 하는 새 가용성을 만듭니다. 이미 Site Recovery에서 만든 가용성 집합이 있는 경우 다시 사용 됩니다.
   - **대상 가용성 영역**: 기본적으로 Site Recovery는 대상 지역이 가용성 영역을 지원하는 경우 대상 지역의 원본 지역과 동일한 영역 번호를 할당합니다.

     대상 지역이 가용성 영역을 지원하지 않는 경우 대상 VM은 기본적으로 단일 인스턴스로 구성됩니다. 필요한 경우 '사용자 지정'을 클릭하여 이러한 VM이 대상 지역에서 가용성 집합의 일부가 되도록 구성할 수 있습니다.

     >[!NOTE]
     >복제를 사용하도록 설정한 후에는 가용성 유형(단일 인스턴스, 가용성 집합 또는 가용성 영역)을 변경할 수 없습니다. 가용성 유형을 변경하려면 복제를 사용하지 않도록 설정했다가 다시 사용하도록 설정해야 합니다.
     >
    
   - **복제 정책**: 복구 지점 보존 기록 및 앱 일치 스냅샷 빈도에 대한 설정을 정의합니다. 기본적으로 Azure Site Recovery ' 24 시간 복구 지점 보존의 기본 설정을 사용 하 여 새 복제 정책을 만듭니다 및 '4 시간' 응용 프로그램 일치 스냅숏 빈도입니다.

     ![복제 사용](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>추가 된 디스크에 대 한 복제를 사용 하도록 설정

복제를 사용할 수 있는 Azure VM에 디스크를 추가 하는 경우 결과 다음과 같습니다.
-   VM에 대 한 복제 상태 경고 및 메모 알립니다을 알리는 표시 하거나 더 많은 디스크 보호를 위해 사용할 수 있습니다.
-   추가 된 디스크에 대 한 보호를 사용 하는 경우 경고는 디스크의 초기 복제 후 사라집니다.
-   디스크에 대 한 복제를 사용 하도록 설정 하지 않으려는 경우 경고를 해제할를 선택할 수 있습니다.

    
    ![새 디스크 추가](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

추가 디스크는 복제를 사용 하도록 설정 하려면 다음을 수행 합니다.

1.  자격 증명 모음에서 > **복제 된 항목**, 디스크를 추가 하는 VM을 클릭 합니다.
2.  클릭 **디스크**를 선택한 다음 복제를 사용 하도록 설정 하려는 데이터 디스크 (이러한 디스크는 **보호 되지 않는** 상태).
3.  **디스크 세부 정보**, 클릭 **복제를 사용 하도록 설정**합니다.

    ![추가 된 디스크에 대 한 복제를 사용 하도록 설정](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

사용 복제 작업이 실행 하 고 초기 복제가 완료 된 후 디스크 문제에 대 한 복제 상태 경고가 제거 됩니다.


  
## <a name="customize-target-resources"></a>대상 리소스 사용자 지정

Site Recovery에서 사용되는 기본 대상 설정을 수정할 수 있습니다.

1. ‘대상 구독’ 옆에 있는 **사용자 지정:** 을 클릭하여 기본 대상 구독을 수정합니다. 동일한 AAD(Azure Active Directory) 테넌트에서 사용할 수 있는 모든 구독 목록에서 구독을 선택합니다.

2. **사용자 지정**을 클릭하여 기본 설정을 수정합니다.
    - **대상 리소스 그룹**에서 ,구독 내 대상 위치에 있는 모든 리소스 그룹의 목록에서 리소스 그룹을 선택할 수 있습니다.
    - **대상 가상 네트워크**에서 대상 위치의 모든 가상 네트워크 목록에서 네트워크를 선택합니다.
    - **가용성 집합**에서 가용성 집합 설정이 원본 지역 가용성 집합의 일부인 경우 VM에 가용성 집합 설정을 추가할 수 있습니다.
    - **대상 저장소 계정**에서 사용할 계정을 선택합니다.

        ![복제 사용](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. **사용자 지정**을 클릭하여 복제 설정을 수정합니다.
4. **다중 VM 일관성**, 함께 복제 하려는 Vm을 선택 합니다.
    - 복제 그룹의 모든 컴퓨터는 장애 조치(failover) 시에 충돌 일치/앱 일치 복구 지점을 공유합니다.
    - 다중 VM 일관성을 사용 하면 영향을 줄 수 워크 로드 성능 (CPU를 많이 사용 됨). 컴퓨터가 동일한 워크 로드를 실행 하 고 여러 컴퓨터에서 일관성이 필요한 경우만 설정 해야 합니다.
    - 예를 들어, 응용 프로그램 2 SQL Server 가상 머신 및 두 명의 웹 서버에 있으면 추가 해야 SQL Server Vm만 복제 그룹에 있습니다.
    - 복제 그룹에는 최대 16 대의 Vm 포함할 수 있습니다.
    - 다중 VM 일관성을 사용하도록 설정하면 복제 그룹의 컴퓨터는 20004 포트를 통해 서로 통신하게 됩니다.
    - 20004 포트를 통한 Vm 간의 내부 통신을 차단 방화벽 어플라이언스가 없는지 확인 합니다.
    - Linux Vm이 복제 그룹에 속해야 하기를 원하는 경우에 특정 Linux 버전에 대 한 지침에 따라 20004 포트의 아웃 바운드 트래픽을 수동으로 열어야를 확인 합니다.
![복제 활성화](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)
    
5. **대상 리소스 만들기** > **복제 사용**을 클릭합니다.
6. VM이 복제에 대해 사용하도록 설정된 후 **복제된 항목**에서 VM 상태를 확인할 수 있습니다.

>[!NOTE]
>초기 복제 중 상태가 새로 고쳐질 때까지 시간이 걸릴 수 있습니다(진행률 없음). 최신 작업 상태를 가져오려면 **새로 고침** 단추를 클릭합니다.
>

# <a name="next-steps"></a>다음 단계

테스트 장애 조치(failover) 실행에 대해 [자세히 알아보세요](site-recovery-test-failover-to-azure.md).
