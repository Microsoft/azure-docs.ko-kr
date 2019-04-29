---
title: Azure Site Recovery의 Azure VM에 대한 복제 구성 | Microsoft Docs
description: 이 문서에서는 Site Recovery를 사용하여 한 Azure 지역에서 다른 Azure 지역으로 Azure VM에 대한 복제를 구성하는 방법을 설명합니다.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: c8b5cf840b8cb5eec2a993cfe35c8a8a7ac904fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60791382"
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>다른 Azure 지역으로 Azure 가상 머신 복제



이 문서에서는 한 Azure 지역에서 다른 Azure 지역으로 Azure VM 복제를 사용하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 [Azure 간 자습서](azure-to-azure-tutorial-enable-replication.md)에 설명된 대로 이 시나리오에 대한 Site Recovery를 이미 설정했다고 가정합니다. 필수 조건을 갖추고 Recovery Services 자격 증명 모음을 만들었는지 확인합니다.



## <a name="enable-replication"></a>복제 사용

복제를 활성화합니다. 이 절차에서는 주 Azure 지역을 동아시아, 보조 지역을 동남 아시아라고 가정합니다.

1. 자격 증명 모음에서 **+복제**를 클릭합니다.
2. 다음 필드를 확인합니다.
   - **원본**: VM의 원점이며 이 경우 **Azure**입니다.
   - **원본 위치**: 가상 머신을 보호할 Azure 지역입니다. 이 그림에서는 원본 위치가 '동아시아'입니다.
   - **배포 모델**: 원본 머신의 Azure 배포 모델입니다.
   - **원본 구독**: 원본 가상 머신이 속한 구독입니다. 복구 서비스 자격 증명 모음이 있는 동일한 Azure Active Directory 테넌트 내에 있는 구독일 수 있습니다.
   - **리소스 그룹**: 원본 가상 머신이 속한 리소스 그룹입니다. 선택한 리소스 그룹 아래의 모든 VM은 다음 단계에서 보호를 위해 나열됩니다.

     ![복제 사용](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. **Virtual Machines > 가상 머신 선택**에서 복제하려는 각 VM을 클릭하여 선택합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 그런 다음 **확인**을 클릭합니다.
    ![복제 활성화](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. **설정**에서 대상 사이트 설정을 선택적으로 구성할 수 있습니다.

   - **대상 위치**: 원본 가상 머신 데이터가 복제될 위치입니다. 선택한 컴퓨터 위치에 따라 Site Recovery에서 적합한 대상 지역 목록을 제공합니다. 대상 위치를 Recovery Services 자격 증명 모음 위치와 동일하게 유지하는 것이 좋습니다.
   - **대상 구독**: 재해 복구에 사용되는 대상 구독입니다. 기본적으로 대상 구독은 원본 구독과 동일합니다.
   - **대상 리소스 그룹**: 모든 복제된 가상 머신이 속하는 리소스 그룹입니다. 기본적으로 Azure Site Recovery는 "asr" 접미사를 사용하여 대상 지역에 새 리소스 그룹을 만듭니다. Azure Site Recovery에서 만든 리소스 그룹이 이미 있는 경우 해당 리소스 그룹이 재사용 됩니다. 아래 섹션처럼 리소스 그룹을 사용자 지정할 수도 있습니다. 원본 가상 머신이 호스트되는 지역을 제외한 모든 Azure 지역이 대상 리소스 그룹의 위치가 될 수 있습니다.
   - **대상 가상 네트워크**: 기본적으로 Site Recovery는 "asr" 접미사를 사용하여 대상 지역에 새 가상 네트워크를 만듭니다. 이 가상 네트워크는 원본 네트워크에 매핑되고 이후의 모든 보호를 위해 사용됩니다. [자세히 알아봅니다](site-recovery-network-mapping-azure-to-azure.md) 를 확인해 보세요.
   - **대상 스토리지 계정(원본 VM이 관리 디스크를 사용하지 않는 경우)**: 기본적으로 Site Recovery는 원본 VM 스토리지 구성을 모방하는 새 대상 스토리지 계정을 만듭니다. 저장소 계정이 이미 있는 경우 다시 사용됩니다.
   - **복제본 관리 디스크(원본 VM이 관리 디스크를 사용하는 경우)**: Site Recovery는 대상 지역에 새로운 복제본 관리 디스크를 만들어서 원본 VM의 관리 디스크와 동일한 스토리지 유형(표준 또는 프리미엄)을 원본 VM의 관리 디스크로 미러링합니다.
   - **캐시 스토리지 계정**: Site Recovery는 원본 지역에 캐시 스토리지로 불리는 추가 스토리지 계정이 필요합니다. 원본 VM에서 발생하는 모든 변경 내용이 대상 위치로 복제되기 전에 추적되고 캐시 저장소 계정으로 전송됩니다.
   - **대상 가용성 세트**: 기본적으로 Azure Site Recovery는 원본 지역에서 가용성 세트에 있는 VM 부분의 이름에 “asr” 접미사가 있는 대상 지역에 새 가용성 세트를 만듭니다. Azure Site Recovery에서 만든 가용성 집합이 이미 있는 경우 해당 가용성 집합이 재사용 됩니다.
   - **대상 가용성 영역**: 기본적으로 Site Recovery는 대상 지역이 가용성 영역을 지원하는 경우 대상 지역의 원본 지역과 동일한 영역 번호를 할당합니다.

     대상 지역이 가용성 영역을 지원하지 않는 경우 대상 VM은 기본적으로 단일 인스턴스로 구성됩니다. 필요한 경우 '사용자 지정'을 클릭하여 이러한 VM이 대상 지역에서 가용성 집합의 일부가 되도록 구성할 수 있습니다.

     >[!NOTE]
     >복제를 사용하도록 설정한 후에는 가용성 유형(단일 인스턴스, 가용성 집합 또는 가용성 영역)을 변경할 수 없습니다. 가용성 유형을 변경하려면 복제를 사용하지 않도록 설정했다가 다시 사용하도록 설정해야 합니다.
     >
    
   - **복제 정책**: 복구 지점 보존 기록 및 앱 일치 스냅숏 빈도에 대한 설정을 정의합니다. 기본적으로 Azure Site Recovery는 복구 지점 보존의 경우 '24시간', 앱 일치 스냅숏 빈도의 경우 '60분'인 기본 설정으로 새 복제 정책을 만듭니다.

     ![복제 사용](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)
  
## <a name="customize-target-resources"></a>대상 리소스 사용자 지정

Site Recovery에서 사용되는 기본 대상 설정을 수정할 수 있습니다.

1. ‘대상 구독’ 옆에 있는 **사용자 지정:** 을 클릭하여 기본 대상 구독을 수정합니다. 동일한 AAD(Azure Active Directory) 테넌트에서 사용할 수 있는 모든 구독 목록에서 구독을 선택합니다.

2. **사용자 지정**을 클릭하여 기본 설정을 수정합니다.
    - **대상 리소스 그룹**에서 ,구독 내 대상 위치에 있는 모든 리소스 그룹의 목록에서 리소스 그룹을 선택할 수 있습니다.
    - **대상 가상 네트워크**에서 대상 위치의 모든 가상 네트워크 목록에서 네트워크를 선택합니다.
    - **가용성 집합**에서 가용성 집합 설정이 원본 지역 가용성 집합의 일부인 경우 VM에 가용성 집합 설정을 추가할 수 있습니다.
    - **대상 저장소 계정**에서 사용할 계정을 선택합니다.

        ![복제 사용](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
1. **사용자 지정**을 클릭하여 복제 설정을 수정합니다.
   - **다중 VM 일관성**에서 함께 복제하려는 VM을 선택합니다. 
   - 복제 그룹의 모든 컴퓨터는 장애 조치(failover) 시에 충돌 일치/앱 일치 복구 지점을 공유합니다. 다중 VM 일관성을 사용하도록 설정하면 워크로드 성능에 영향을 줄 수 있으며(CPU 집약적이므로), 머신에서 동일한 워크로드를 실행하며 여러 머신에서 일관성이 필요한 경우에만 사용해야 합니다. 예를 들어 애플리케이션에 두 개의 SQL 가상 머신과 두 개의 웹 서버가 있는 경우 SQL 가상 머신만 복제 그룹의 일부로 추가해야 합니다.
   - 복제 그룹에 최대 16개의 가상 머신이 포함되도록 선택할 수 있습니다.
   - 다중 VM 일관성을 사용하도록 설정하면 복제 그룹의 컴퓨터는 20004 포트를 통해 서로 통신하게 됩니다. 20004 포트를 통한 VM 간의 내부 통신을 차단하는 방화벽 어플라이언스가 없는지 확인합니다. Linux VM을 복제 그룹에 포함하고 싶다면 특정 Linux 버전의 지침에 따라 20004 포트의 아웃바운드 트래픽을 수동으로 열어야 합니다.
![복제 활성화](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)
    
2. **대상 리소스 만들기** > **복제 사용**을 클릭합니다.
3. VM이 복제에 대해 사용하도록 설정된 후 **복제된 항목**에서 VM 상태를 확인할 수 있습니다.

>[!NOTE]
>초기 복제 중 상태가 새로 고쳐질 때까지 시간이 걸릴 수 있습니다(진행률 없음). 최신 작업 상태를 가져오려면 **새로 고침** 단추를 클릭합니다.
>

# <a name="next-steps"></a>다음 단계

테스트 장애 조치(failover) 실행에 대해 [자세히 알아보세요](site-recovery-test-failover-to-azure.md).
