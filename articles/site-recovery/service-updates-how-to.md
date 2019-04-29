---
title: Azure Site Recovery 업데이트 | Microsoft Docs
description: 서비스 업데이트의 개요와 Azure Site Recovery에서 사용되는 구성 요소를 업그레이드하는 방법을 대략적으로 설명합니다.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/05/2019
ms.author: rajanaki
ms.openlocfilehash: e27dee213baf8365c3ad4efc69602f66e2081abe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036055"
---
# <a name="service-updates-in-azure-site-recovery"></a>Azure Site Recovery의 서비스 업데이트
조직으로서 계획되거나 계획되지 않은 정전이 발생한 경우 데이터 그리고 실행 중인 앱/워크로드를 안전하게 유지하는 방법을 알아야 합니다. Azure Site Recovery는 사이트의 작동이 중단된 경우에도 VM 및 물리적 서버에서 실행 중인 앱을 계속 사용할 수 있도록 하여 BCDR 전략에 기여합니다. Site Recovery는 VM 및 물리적 서버에서 실행 중인 워크로드를 복제하여 기본 사이트를 사용할 수 없게 된 경우 보조 위치에서 계속 사용할 수 있도록 합니다. 사이트가 복구되어 다시 실행되면 워크로드를 기본 사이트로 복구합니다.

Site Recovery는 다음을 위해 복제를 관리할 수 있습니다.

- [Azure 지역 간에 Azure VM 복제](azure-to-azure-tutorial-dr-drill.md).
- 온-프레미스 가상 머신 및 물리적 서버를 Azure 또는 보조 사이트에 복제
자세한 내용은 [여기](https://docs.microsoft.com/azure/site-recovery)에서 설명서를 참조하세요.

Azure Site Recovery는 새 기능 추가, 지원 매트릭스 개선 및 버그 수정 프로그램(있는 경우)을 비롯한 서비스 업데이트를 정기적으로 게시합니다. 모든 최신 기능, 향상된 기능 및 버그 수정 프로그램(있는 경우)을 최신 상태로 유지하려면 항상 최신 버전의 Azure SIte Recovery 구성 요소로 업데이트하는 것이 좋습니다. 
 
## <a name="support-statement-for-azure-site-recovery"></a>Azure Site Recovery에 대한 지원 정책 

> [!IMPORTANT]
> **릴리스된 Azure Site Recovery 구성 요소의 모든 새 버전 ‘N’을 사용하면 ‘N-4’ 이전의 모든 버전은 지원되지 않는 것으로 간주됩니다**. 따라서 항상 사용 가능한 최신 버전으로 업그레이드하는 것이 좋습니다.

> [!IMPORTANT]
> 업그레이드에 대한 공식 지원은 N-4 미만부터 N 버전(N이 최신 버전임)까지입니다. N-6인 경우 먼저 N-4로 업그레이드한 후 N으로 업그레이드해야 합니다.

### <a name="upgrading-when-the-difference-between-current-version-and-latest-released-version-is-greater-than-4"></a>현재 버전과 최신 릴리스 버전 간의 차이가 4보다 큰 경우 업그레이드

1. 먼저, 현재 설치된 구성 요소를 N 버전에서 N+4로 업그레이드한 후 호환 가능한 다음 버전으로 전환합니다. 현재 버전이 9.24이고 사용자가 9.16을 사용하고 있는 경우 먼저 9.20으로 업그레이드한 후 9.24로 업그레이드합니다.
2. 시나리오에 따라 모든 구성 요소에 대해 동일한 프로세스를 수행합니다.

### <a name="support-for-latest-oskernel-versions"></a>최신 OS/커널 버전에 대한 지원

> [!NOTE]
> 유지 관리 기간이 예약되어 있고 이 기간 안에 다시 부팅도 포함될 경우 먼저 Site Recovery 구성 요소를 업그레이드한 후 예약된 나머지 작업을 계속 진행하는 것이 좋습니다.

1. 커널/OS 버전을 업그레이드하기 전에 먼저 Azure Site Recovery에서 대상 버전을 지원하는지 확인합니다. 설명서에서 Azure VM, [VMware VM](vmware-physical-azure-support-matrix.md) 및 Hyper-V VM에 대한 정보를 찾을 수 있습니다.
2. [서비스 업데이트](https://azure.microsoft.com/updates/?product=site-recovery)를 참조하여 업그레이드하려는 특정 버전을 지원하는 Site Recovery 구성 요소 버전을 찾으세요.
3. 먼저 최신 Site Recovery 버전으로 업그레이드합니다.
4. 이제 OS/커널을 원하는 버전으로 업그레이드합니다.
5. 다시 부팅합니다.
6. 이렇게 하면 컴퓨터의 OS/커널 버전이 최신 버전으로 업그레이드되고, 새 버전을 지원하는 데 필요한 최신 Site Recovery 변경 내용이 원본 컴퓨터에 로드됩니다.



## <a name="azure-vm-disaster-recovery-to-azure"></a>Azure로 Azure VM 재해 복구
이 시나리오에서는 자동 업데이트를 [사용](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-autoupdate)할 것을 강력히 권장합니다. 다음과 같은 방법으로 Site Recovery가 업데이트를 관리하도록 선택할 수 있습니다.

- 복제 설정 단계의 일부로
- 자격 증명 모음 내에서 확장 업데이트 설정 전환

업데이트를 수동으로 관리하기로 선택한 경우 다음 단계를 따르세요.

1. Azure Portal로 이동한 후 "Recovery Services 자격 증명 모음"으로 이동합니다.
2. "Recovery Services 자격 증명 모음"에 대해 Azure Portal의 "복제된 항목" 창으로 이동합니다.
3. 화면 맨 위에 있는 다음 알림을 클릭합니다.
    
    *새 Site Recovery 복제 에이전트 업데이트를 사용할 수 있습니다.*
    
    *설치하려면 클릭하세요. ->*

4. 업데이트를 적용하려는 VM을 선택하고 **확인**을 클릭합니다.

## <a name="between-two-on-premises-vmm-sites"></a>두 개의 온-프레미스 VMM 사이트 간
1. Microsoft Azure Site Recovery 공급자용 최신 업데이트 롤업을 다운로드합니다.
2. 먼저 복구 사이트를 관리하는 온-프레미스 VMM 서버에서 업데이트 롤업을 설치합니다.
3. 복구 사이트를 업데이트한 후 기본 사이트를 관리하는 VMM 서버에서 업데이트 롤업을 설치합니다.

> [!NOTE]
> VMM이 고가용성 VMM(클러스터형 VMM)이면 VMM 서비스를 설치한 클러스터의 모든 노드에서 업그레이드를 설치해야 합니다.

## <a name="between-an-on-premises-vmm-site-and-azure"></a>온-프레미스 VMM 사이트 및 Azure 간
1. Microsoft Azure Site Recovery 공급자용 업데이트 롤업을 다운로드합니다.
2. 온-프레미스 VMM 서버에서 업데이트 롤업을 설치합니다.
3. 모든 Hyper-V 호스트에서 최신 에이전트인 MARS 에이전트를 설치합니다.

> [!NOTE]
> VMM이 고가용성 VMM(클러스터형 VMM)이면 VMM 서비스를 설치한 클러스터의 모든 노드에서 업그레이드를 설치해야 합니다.

## <a name="between-an-on-premises-hyper-v-site-and-azure"></a>온-프레미스 Hyper-V 사이트 및 Azure 간

1. Microsoft Azure Site Recovery 공급자용 업데이트 롤업을 다운로드합니다.
2. Azure Site Recovery에 등록한 Hyper-V 서버의 각 노드에 공급자를 설치합니다.

> [!NOTE]
> Hyper-V가 호스트 클러스터형 Hyper-V 서버인 경우 클러스터의 모든 노드에 업그레이드를 설치해야 합니다.

## <a name="between-an-on-premises-vmware-or-physical-site-to-azure"></a>온-프레미스 VMware 또는 실제 Azure 사이트 간

업데이트를 진행하기 전에 [Site Recovery 지원 명세서](#support-statement-for-azure-site-recovery)를 참조하여 업그레이드 경로를 알아 두세요.

1. 현재 버전 및 위에 제공한 지원 명세서를 기반으로 [여기](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server)에 나오는 지침에 따라 온-프레미스 관리 서버에서 먼저 업데이트를 설치합니다. 이 서버는 구성 서버 및 프로세스 서버 역할이 있는 서버입니다.
2. 스케일 아웃 프로세스 서버가 있는 경우 [여기](vmware-azure-manage-process-server.md#upgrade-a-process-server)에 나오는 지침에 따라 해당 서버를 다음으로 업데이트합니다.
3. 그런 다음, 각 보호된 항목에서 모바일 에이전트를 업데이트하려면 Azure Portal로 이동한 후 **보호된 항목** > **복제된 항목** 페이지로 이동합니다. 이 페이지에서 VM을 선택합니다. 각 VM의 페이지 하단에 나타나는 **업데이트 에이전트** 단추를 선택합니다. 그러면 보호된 모든 VM에서 모바일 서비스 에이전트가 업데이트됩니다.

### <a name="reboot-of-source-machine-after-mobility-agent-upgrade"></a>모바일 에이전트를 업그레이드한 후 원본 머신 다시 부팅

모든 최신 변경 내용이 원본 컴퓨터에 로드되도록 모바일 에이전트를 업그레이드할 때마다 다시 부팅하는 것이 좋습니다. 그러나 이 작업은 **필수는 아닙니다**. 마지막 다시 부팅 동안의 에이전트 버전과 현재 버전 간의 차이가 4보다 큰 경우 반드시 다시 부팅해야 합니다. 자세한 설명을 보려면 다음 표를 참조하세요.

|**마지막 다시 부팅하는 동안의 에이전트 버전** | **업그레이드 대상 버전** | **다시 부팅 필수 여부**|
|---------|---------|---------|
|9.16 |  9.18 | 필수 아님|
|9.16 | 9.19 | 필수 아님|
| 9.16 | 9.20 | 필수 아님
 | 9.16 | 9.21 | 예. 버전 차이(마지막 다시 부팅이 수행된 버전은 9.16이고 대상 버전은 9.21임)가 4보다 크므로 먼저 9.20으로 업그레이드하고, 다시 부팅한 후 9.21로 업그레이드합니다.

## <a name="links-to-currently-supported-update-rollups"></a>현재 지원되는 업데이트 롤업에 대한 링크

|업데이트 롤업  |공급자  |통합 설치| OVF  |MARS|
|---------|---------|---------|---------|--------|
|[업데이트 롤업 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery)     |   5.1.4000.0  |  9.23.5163.1   |  5.1.4000.0  | 2.0.9156.0
|[ 34](https://support.microsoft.com/en-us/help/4490016/update-rollup-34-for-azure-site-recovery) - 핫픽스     |   5.1.3950.0  |  9.22.5142.1   |  5.1.3950.0  | 2.0.9155.0
|[업데이트 롤업 33](https://support.microsoft.com/en-us/help/4489582/update-rollup-33-for-azure-site-recovery)     |   5.1.3900.0  |  9.22.5109.1   |  5.1.3900.0  | 2.0.9155.0
|[업데이트 롤업 32](https://support.microsoft.com/en-us/help/4485985/update-rollup-32-for-azure-site-recovery)     |   5.1.3800.0  |  9.21.5091.1   |  5.1.3800.0  |2.0.9144.0
|[업데이트 롤업 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)     |     5.1.3700.0      |   9.20.5051.1      |     5.1.3700.0    |2.0.9144.0
|[업데이트 롤업 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30)     |    5.1.3650.0   |   9.19.5007.1    |     5.1.3650.0    |2.0.9139.0

## <a name="previous-update-rollups"></a>이전 업데이트 롤업

- [업데이트 롤업 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery)
- [업데이트 롤업 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)
- [업데이트 롤업 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)
- [업데이트 롤업 26](https://support.microsoft.com/help/4344054/update-rollup-26-for-azure-site-recovery)  
- [업데이트 롤업 25](https://support.microsoft.com/help/4278275/update-rollup-25-for-azure-site-recovery) 
- [업데이트 롤업 23](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 
- [업데이트 롤업 22](https://support.microsoft.com/help/4072852/update-rollup-22-for-azure-site-recovery) 
- [업데이트 롤업 21](https://support.microsoft.com/help/4051380/update-rollup-21-for-azure-site-recovery) 
- [업데이트 롤업 20](https://support.microsoft.com/help/4041105/update-rollup-20-for-azure-site-recovery) 
- [업데이트 롤업 19](https://support.microsoft.com/help/4034599/update-rollup-19-for-azure-site-recovery) 
