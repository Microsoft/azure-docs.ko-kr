---
title: Azure Site Recovery로 구성한 Windows Server 2012 R2 호스트 및 SCVMM을 Windows Server 2016으로 업그레이드
description: Azure Site Recovery 서비스를 사용하여 Azure에 Azure Stack VM에 대한 재해 복구를 설정하는 방법을 알아봅니다.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: b67290f72f762331a6d699fb79aef0c0d7f9fb65
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61275526"
---
# <a name="upgrade-windows-server-2012-r2-hosts-scvmm-2012-r2-configured-with-azure-site-recovery-to-windows-server-2016--scvmm-2016"></a>Azure Site Recovery로 구성한 Windows Server 2012 R2 호스트, SCVMM 2012 R2를 Windows Server 2016 및 SCVMM 2016으로 업그레이드

이 문서에서는 Azure Site Recovery로 구성한 Windows Server 2012 R2 호스트, SCVMM 2012 R2를 Windows Server 2016 및 SCVMM 2016으로 업그레이드하는 방법을 보여줍니다.

Site Recovery는 BCDR(비즈니스 연속성 및 재해 복구 개선) 전략에 기여하는 서비스입니다. 이 서비스는 예상된 및 예기치 않은 중단이 발생할 때 VM 워크로드를 계속 사용할 수 있도록 합니다.

> [!IMPORTANT]
> Azure Site Recovery를 사용한 복제용으로 이미 구성된 Windows Server 2012 R2 호스트를 업그레이드하는 경우 이 문서에 언급된 단계를 따라야 합니다. 업그레이드를 위해 다른 경로를 선택하면 지원되지 않는 상태가 발생할 수 있으며 복제가 중단되거나 장애 조치(Failover)를 수행하지 못할 수 있습니다.


이 문서에서는 환경에서 다음 구성을 업그레이드하는 방법을 설명합니다.

> [!div class="checklist"]
> * **SCVMM으로 관리하지 않는 Windows Server 2012 R2 호스트** 
> * **독립 실행형 SCVMM 2012 R2 서버로 관리하는 Windows Server 2012 R2 호스트** 
> * **고가용성 SCVMM 2012 R2 서버로 관리하는 Windows Server 2012 R2 호스트**


## <a name="prerequisites--factors-to-consider"></a>고려할 필수 구성 요소 및 요인

업그레이드하기 전에 다음 사항을 참조하세요.

- SCVMM으로 관리하지 않는 Windows Server 2012 R2 호스트가 있고 해당 독립 실행형 환경이 설정되어 있는 경우 업그레이드를 수행하려고 하면 복제가 중단됩니다.
- SCVMM 2012 R2를 처음 설치하는 동안 “*not store my Keys in Active Directory under Distributed Key Management*(Active Directory의 분산 키 관리에서 내 키를 저장하지 않음)”을 선택한 경우 업그레이드가 성공적으로 완료되지 않습니다.

- System Center 2012 R2 VMM을 사용하는 경우 

    - VMM에서 데이터베이스 정보를 확인합니다. **VMM 콘솔** -> **설정** -> **일반** -> **데이터베이스 연결**
    - System Center Virtual Machine Manager 에이전트 서비스에 사용되는 서비스 계정을 확인합니다.
    - VMM 데이터베이스의 백업이 있는지 확인합니다.
    - 관련된 SCVMM 서버의 데이터베이스 이름을 적어 둡니다. 이 작업은 **VMM 콘솔** -> **설정** -> **일반** -> **데이터베이스 연결**로 이동하여 수행할 수 있습니다.
    - 2012R2 기본 및 복구 VMM 서버 둘 다의 VMM ID를 적어 둡니다. VMM ID는 레지스트리 “HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup”에서 찾을 수 있습니다.
    - 클러스터에 추가한 새 SCVMM이 이전과 동일한 이름을 갖는지 확인합니다. 

- 양쪽에서 SCVMM으로 관리하는 두 사이트 간에 복제를 수행하는 경우 주 항목을 업그레이드하기 전에 먼저 복구 항목을 업그레이드해야 합니다.
  > [!WARNING]
  > SCVMM 2012 R2를 업그레이드하는 동안 분산 키 관리에서 **Active Directory에 암호화 키를 저장**하도록 선택합니다. 서비스 계정 및 분산 키 관리의 설정을 신중히 선택합니다. 사용자의 선택에 따라, 업그레이드 후에 템플릿의 암호와 같은 암호화된 데이터를 사용할 수 없게 되어 Azure Site Recovery를 통한 복제에 영향을 미칠 수 있습니다.

> [!IMPORTANT]
> 자세한 SCVMM 설명서에서 [필수 구성 요소](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)를 참조하세요.

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>SCVMM으로 관리하지 않는 Windows Server 2012 R2 호스트 
아래 언급된 단계 목록은 [이 자습서](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial)에 따라 [Hyper-V 호스트에서 Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture)로 수행한 사용자 구성에 적용됩니다.

> [!WARNING]
> 필수 구성 요소에 언급된 대로 이러한 단계는 독립 실행형 Hyper-V 호스트 구성이 아닌 클러스터 환경 시나리오에만 적용됩니다.

1. 롤링 클러스터 업그레이드 프로세스를 실행하려면 [롤링 클러스터 업그레이드](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process)를 수행하는 단계를 따릅니다.
2. 클러스터에 도입된 새로운 Windows Server 2016 호스트를 모두 사용하는 경우[여기]에 언급된 단계에 따라 Azure Site Recovery에서 Windows Server 2012 R2 호스트의 참조를 제거하세요. 이 호스트는 클러스터에서 드레이닝 및 방출하기 위해 선택한 호스트여야 합니다.
3. 모든 가상 머신에 대해 *Update-VMVersion* 명령이 실행되면 업그레이드가 완료된 것입니다. 
4. [여기](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment)에 언급된 단계를 사용하여 새 Windows Server 2016 호스트를 Azure Site Recovery에 등록하세요. Hyper-V 사이트가 이미 활성 상태이므로 클러스터에 새 호스트를 등록하기만 하면 됩니다. 
5.  Azure Portal로 이동한 후 Recovery Services 내에서 복제된 상태를 확인합니다.

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>독립 실행형 SCVMM 2012 R2 서버로 관리하는 Windows Server 2012 R2 호스트 업그레이드
Windows Server 2012 R2 호스트를 업그레이드하기 전에 SCVMM 2012 R2를 SCVMM 2016으로 업그레이드해야 합니다. 아래 단계를 수행하세요.

**독립 실행형 SCVMM 2012 R2에서 SCVMM 2016으로 업그레이드**

1.  제어판 -> 프로그램 -> 프로그램 및 기능 ->Microsoft Azure Site Recovery로 이동하여 ASR 공급자를 제거하고 제거를 클릭합니다.
2. [SCVMM 데이터베이스를 유지하고 운영 체제를 업그레이드합니다.](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. **프로그램 추가/제거**에서 **VMM** > **제거**를 선택합니다. b. **기능 제거**를 선택한 후 **VMM 관리 서버 및 VMM 콘솔**을 선택합니다. 다. **데이터베이스 옵션**에서 **데이터베이스 유지**를 선택합니다. d. 요약을 검토하고 **제거**를 클릭합니다.

4. [VMM 2016 설치합니다](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016).
5. SCVMM을 시작하고 **패브릭** 탭에서 각 호스트의 상태를 검사합니다. 최신 상태를 가져오려면 **새로 고침**을 클릭합니다. 상태는 “주의 필요”로 표시되어야 합니다. 
17. SCVMM에 최신 [Microsoft Azure Site Recovery Provider](https://aka.ms/downloaddra)를 설치합니다.
16. 클러스터의 각 호스트에 최신 [MARS(Microsoft Azure Recovery Service) 에이전트](https://aka.ms/latestmarsagent)를 설치합니다. 새로 고쳐 SCVMM이 호스트를 성공적으로 쿼리할 수 있는지 확인합니다.

**Windows Server 2012 R2 호스트를 Windows Server 2016으로 업그레이드**

1. [여기](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process)에 언급된 단계에 따라 롤링 클러스터 업그레이드 프로세스를 실행합니다. 
2. 클러스터에 새 호스트를 추가한 후 SCVMM 콘솔에서 호스트를 새로 고쳐 이 업데이트된 호스트에 VMM 에이전트를 설치합니다.
3. 가상 머신의 VM 버전을 업데이트하려면 *Update-VMVersion*을 실행합니다. 
4.  Azure Portal로 이동한 후 Recovery Services 자격 증명 모음에서 가상 머신의 복제된 상태를 확인합니다. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>고가용성 SCVMM 2012 R2 서버로 관리하는 Windows Server 2012 R2 호스트 업그레이드
Windows Server 2012 R2 호스트를 업그레이드하기 전에 SCVMM 2012 R2를 SCVMM 2016으로 업그레이드해야 합니다. Azure Site Recovery로 구성된 SCVMM 2012 R2 서버를 업그레이드하는 동안 추가 VMM 서버가 없는 혼합 모드 및 추가 VMM 서버가 있는 혼합 모드의 두 업그레이드 모드가 지원됩니다.

**SCVMM 2012 R2를 SCVMM 2016으로 업그레이드**

1.  제어판 -> 프로그램 -> 프로그램 및 기능 ->Microsoft Azure Site Recovery로 이동하여 ASR 공급자를 제거하고 제거를 클릭합니다.
2. 실행할 업그레이드 모드에 따라 [여기](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server)에 언급된 단계를 수행합니다.
3. SCVMM 콘솔을 시작하고 **패브릭** 탭에서 각 호스트의 상태를 검사합니다. 최신 상태를 가져오려면 **새로 고침**을 클릭합니다. 상태는 “주의 필요”로 표시되어야 합니다.
4. SCVMM에 최신 [Microsoft Azure Site Recovery Provider](https://aka.ms/downloaddra)를 설치합니다.
5. 클러스터의 각 호스트에 최신 [MARS(Microsoft Azure Recovery Service) 에이전트](https://aka.ms/latestmarsagent)를 업데이트합니다. 새로 고쳐 SC VMM이 호스트를 성공적으로 쿼리할 수 있는지 확인합니다.


**Windows Server 2012 R2 호스트를 Windows Server 2016으로 업그레이드**

1. [여기](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process)에 언급된 단계에 따라 롤링 클러스터 업그레이드 프로세스를 실행합니다.
2. 클러스터에 새 호스트를 추가한 후 SCVMM 콘솔에서 호스트를 새로 고쳐 이 업데이트된 호스트에 VMM 에이전트를 설치합니다.
3. 가상 머신의 VM 버전을 업데이트하려면 *Update-VMVersion*을 실행합니다. 
4.  Azure Portal로 이동한 후 Recovery Services 자격 증명 모음에서 가상 머신의 복제된 상태를 확인합니다. 

## <a name="next-steps"></a>다음 단계
호스트가 업그레이드되면 [테스트 장애 조치(Failover)](tutorial-dr-drill-azure.md)를 수행하여 복제 및 재해 복구 상태를 테스트할 수 있습니다.

