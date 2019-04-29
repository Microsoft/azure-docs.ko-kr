---
title: Azure Site Recovery의 Hyper-V와 Azure 간 재해 복구 아키텍처 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery 서비스를 사용한 온-프레미스 Hyper-V VM(VMM 없음)과 Azure 간 재해 복구를 배포할 때 사용되는 구성 요소 및 아키텍처를 간략하게 설명합니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: f77069592fb34caf409b387f5c8452159f55e296
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553337"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Hyper-V와 Azure 간 재해 복구 아키텍처


이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 Hyper-V와 Azure 간에 Hyper-V 가상 머신을 복제, 장애 조치 및 복구할 때 사용되는 아키텍처와 프로세스에 대해 설명합니다.

Hyper-V호스트는 선택적으로 System Center VMM(Virtual Machine Manager) 사설 클라우드에서 관리할 수 있습니다.



## <a name="architectural-components---hyper-v-without-vmm"></a>아키텍처 구성 요소 - VMM 없는 Hyper-v

다음 표와 그림은 Hyper-V 호스트를 VMM에서 관리하지 않을 때 Hyper-V를 Azure에 복제하는 데 사용되는 구성 요소를 개략적으로 보여 줍니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
**Azure** | Azure 구독, Azure Storage 계정 및 Azure 네트워크  | 온-프레미스 VM 워크로드에서 복제된 데이터는 저장소 계정에 저장됩니다. 온-프레미스 사이트에서 장애 조치가 발생한 경우 복제된 워크로드 데이터를 사용하여 Azure VM을 만듭니다.<br/><br/> Azure VM을 만들 때 Azure 가상 네트워크에 연결합니다.
**Hyper-V** | Site Recovery 배포 중에 Hyper-V 호스트와 클러스터를 Hyper-V 사이트에 모읍니다. Azure Site Recovery 공급자와 Recovery Services 에이전트를 각 독립 실행형 Hyper-V 호스트 또는 각 Hyper-V 클러스터 노드에 설치합니다. | 공급자는 인터넷을 통한 사이트 복구로 복제를 오케스트레이션합니다. Recovery Services 에이전트는 데이터 복제를 처리합니다.<br/><br/> 공급자 및 에이전트로부터의 통신은 모두 보호 및 암호화됩니다. Azure 저장소에 복제된 데이터도 암호화됩니다.
**Hyper-V VM** | Hyper-V에서 실행 중인 하나 이상의 VM. | 명시적으로 VM에 설치해야 하는 것은 없습니다.


**Hyper-V에서 Azure로 아키텍처(VMM 없음)**

![아키텍처](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)



## <a name="architectural-components---hyper-v-with-vmm"></a>아키텍처 구성 요소 - VMM 있는 Hyper-V

다음 표와 그림은 Hyper-V 호스트를 VMM 클라우드에서 관리할 때 Hyper-V를 Azure에 복제하는 데 사용되는 구성 요소를 개략적으로 보여 줍니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
**Azure** | Azure 구독, Azure Storage 계정 및 Azure 네트워크  | 온-프레미스 VM 워크로드에서 복제된 데이터는 저장소 계정에 저장됩니다. 온-프레미스 사이트에서 장애 조치가 발생한 경우 복제된 데이터를 사용하여 Azure VM을 만듭니다.<br/><br/> Azure VM을 만들 때 Azure 가상 네트워크에 연결합니다.
**VMM 서버** | VMM 서버에는 Hyper-V 호스트를 포함하는 하나 이상의 클라우드가 있습니다. | VMM 서버에 복제를 Site Recovery와 오케스트레이션하는 Site Recovery 공급자를 설치하고 Recovery Services 자격 증명 모음에 서버를 등록합니다.
**Hyper-V 호스트** | VMM에서 관리되는 하나 이상의 Hyper-V 호스트/클러스터. |  각 Hyper-V 호스트 또는 클러스터 노드에서 Recovery Services 에이전트를 설치합니다.
**Hyper-V VM** | Hyper-V 호스트 서버에서 실행되는 하나 이상의 VM. | 명시적으로 VM에 설치해야 하는 것은 없습니다.
**네트워킹** | VMM 서버에 설정된 논리 및 VM 네트워크. VM 네트워크는 클라우드와 연결된 논리 네트워크에 연결되어야 합니다. | VM 네트워크는 Azure 가상 네트워크에 매핑됩니다. 장애 조치 후 Azure VM이 만들어지면 VM 네트워크에 매핑되지 않은 Azure 네트워크에 추가됩니다.

**Hyper-V에서 Azure로 아키텍처(VMM 있음)**

![구성 요소](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>복제 프로세스

![Azure로 Hyper-V 복제](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**복제 및 복구 프로세스**


### <a name="enable-protection"></a>보호 사용

1. Azure Portal 또는 온-프레미스에서 Hyper-V VM에 대한 보호를 사용하도록 설정하면 **보호 활성화**가 시작됩니다.
2. 이 작업은 사용자가 구성한 설정으로 Azure에 대한 복제를 설정하기 위해 [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) 메서드를 호출하기 전에 해당 컴퓨터가 전제 조건에 부합하는지 확인합니다.
3. 작업은 [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) 메서드를 호출하여 초기 복제를 시작하여 전체 VM 복제를 초기화하고 Azure로 VM의 가상 디스크를 전송합니다.
4. **작업** 탭에서 작업을 모니터링할 수 있습니다.      ![작업 목록](media/hyper-v-azure-architecture/image1.png) ![보호 드릴 다운 사용](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>초기 데이터 복제

1. 초기 복제가 트리거되면 [Hyper-V VM 스냅숏](https://technet.microsoft.com/library/dd560637.aspx)이 생성됩니다.
2. VM의 가상 하드 디스크는 모두 Azure에 복사될 때까지 하나씩 복제됩니다. 이 작업은 VM 크기 및 네트워크 대역폭에 따라 시간이 오래 걸릴 수 있습니다. 네트워크 대역폭을 높이는 [한 방법을 알아봅니다](https://support.microsoft.com/kb/3056159).
3. 초기 복제 진행 중에 디스크가 변경될 경우, Hyper-V 복제 로그(.hrl)로 Hyper-V 복제본 복제 추적자가 이러한 변경 내용을 추적합니다. 이러한 로그 파일은 디스크와 동일한 폴더에 있습니다. 각 디스크에는 보조 저장소로 전송되는 .hrl 파일이 연결되어 있습니다. 초기 복제 진행 중에는 스냅숏과 로그 파일이 디스크 리소스를 사용합니다.
4. 초기 복제가 완료되면 VM 스냅숏은 삭제됩니다.
5. 로그의 델타 디스크 변경 내용이 동기화되고 부모 디스크로 병합합니다.


### <a name="finalize-protection-process"></a>보호 프로세스 완료

1. 초기 복제를 마친 후에는 **가상 머신에 대한 보호 완료** 작업이 실행됩니다. 네트워크 및 기타 사후 설정을 구성하므로 VM이 보호됩니다.
2. 이 단계에서 VM 설정이 장애 조치에 대비하고 있는지 확인할 수 있습니다. VM에 대한 재해 복구 훈련(장애 조치 테스트)을 실행하여 장애 조치가 기대한 대로 작동하는지 확인할 수 있습니다. 


## <a name="delta-replication"></a>델타 복제

1. 초기 복제 후에는 복제 정책에 따라 델타 복제가 시작됩니다.
2. Hyper-V 복제본 복제 추적자가 .hrl 파일로 가상 하드 디스크에 변경 내용을 추적합니다. 복제를 위해 구성된 각 디스크에는 연결된 .hrl 파일이 있습니다.
3. 로그는 고객의 저장소 계정으로 전송됩니다. 로그가 Azure로 전송 중인 경우 주 디스크의 변경 내용은 같은 폴더의 다른 로그 파일에 추적됩니다.
4. 초기 및 델타 복제가 진행되는 동안 Azure Portal에서 VM을 모니터링할 수 있습니다.

### <a name="resynchronization-process"></a>다시 동기화 프로세스

1. 델타 복제에 실패했고 전체 복제에는 대역폭이나 시간이 많이 소모될 경우 VM에서 다시 동기화가 발생합니다.
    - 예를 들어 .hrl 파일이 디스크 크기에 50%에 달한다면 VM이 다시 동기화되도록 표시됩니다.
    -  기본적으로 다시 동기화는 업무 시간 이외에 실행되도록 예약됩니다.
1.  다시 동기화는 델타 데이터만 보냅니다.
    - 원본 및 대상 VM의 체크섬을 계산하여 보내는 데이터 크기를 최소화합니다.
    - 고정 블록 청크 알고리즘을 사용하며 이 경우 원본 및 대상 파일이 고정 청크로 나누어집니다.
    - 각 청크에 대한 체크섬이 생성됩니다. 이를 비교하여 원본의 어떤 블록을 대상에 적용해야 하는지 결정합니다.
2. 다시 동기화를 마치면 일반 델타 복제가 다시 시작됩니다.
3. 일정대로 기본 다시 동기화 시간까지 기다릴 수 없으면 수동으로 VM을 다시 동기화할 수 있습니다. 시스템이 중단이 발생한 경우를 예로 들 수 있습니다. 이렇게 하려면 Azure Portal에서 **VM > 다시 동기화**를 선택합니다.

    ![수동 다시 동기화](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>다시 시도 프로세스

복제 오류가 발생한 경우 기본 제공 재시도가 있습니다. 다시 시도는 다음 표의 설명대로 분류됩니다.

**범주** | **세부 정보**
--- | ---
**복구할 수 없는 오류** | 재시도가 수행되지 않습니다. VM 상태가 **위험**으로 표시되고 관리자 개입이 필요합니다.<br/><br/> 이러한 오류의 예로는 VHD 체인 손상, VM 복제본에 대한 잘못된 상태, 네트워크 인증 오류, 권한 부여 오류, VM 찾을 수 없음 오류(독립 실행형 Hyper-V 서버의 경우)가 포함됩니다.
**복구할 수 있는 오류** | 첫 번째 시도부터 재시도 간격을 늘리는 기하급수적인 백오프(1, 2, 4, 8, 10분)를 사용하여 복제 간격마다 재시도가 발생합니다. 오류가 계속되면 30분마다 다시 시도하십시오. 이러한 예로는 네트워크 오류, 디스크 공간 부족, 메모리 부족 상태가 있습니다.



## <a name="failover-and-failback-process"></a>장애 조치 및 장애 복구 프로세스

1. 온-프레미스 Hyper-V VM에서 Azure로 계획된 또는 계획되지 않은 장애 조치를 실행할 수 있습니다. 계획된 장애 조치를 실행할 경우 데이터 손실을 방지하기 위해 원본 VM이 종료됩니다. 주 사이트에 액세스할 수 없는 경우 계획되지 않은 장애 조치를 실행합니다.
2. 단일 컴퓨터에 장애 조치를 수행하거나 복구 계획을 만들어서 여러 컴퓨터의 장애 조치를 오케스트레이션할 수 있습니다.
3. 장애 조치를 실행합니다. 첫 번째 장애 조치를 실행한 후에 Azure에서 만들어진 복제본 VM이 표시되어야 합니다. 필요한 경우 VM에 공용 IP 주소를 할당할 수 있습니다.
4. 그런 다음 복제본 Azure VM에서 워크로드에 액세스하려면 장애 조치를 커밋합니다.

온-프레미스 인프라를 다시 실행한 후 장애 복구를 수행할 수 있습니다. 장애 복구는 다음 3단계로 수행됩니다.

1. Azure에서 온-프레미스 사이트로 계획된 장애 조치를 시작합니다.
    - **가동 중단 시간 최소화**: 이 옵션을 사용하면 Site Recovery가 장애 조치(failover) 전에 데이터를 동기화합니다. 변경된 데이터 블록을 확인하고 온-프레미스 사이트에 다운로드하며 Azure VM은 그 동안에도 계속 실행되어 가동 중지 시간을 최소화합니다. 장애 조치 완료를 수동으로 지정해야 하는 경우 Azure VM이 종료되고 최종 델타 변경 내용이 복사되며 장애 조치가 시작됩니다.
    - **전체 다운로드**: 이 옵션에서는 장애 조치(failover) 도중에 데이터가 동기화됩니다. 이 옵션은 전체 디스크를 다운로드합니다. 체크섬을 계산하지 않아 더 빠르지만 가동 중지 시간은 더 늘어납니다. 상당 시간 동안 복제본 Azure VM을 실행하였거나 온-프레미스 VM이 삭제된 경우에 이 옵션을 사용합니다.
    - **VM 만들기**: 동일한 VM으로 또는 대체 VM으로 장애 복구(Failback)하도록 선택할 수 있습니다. VM이 없는 경우 Site Recovery가 이를 만들도록 지정할 수 있습니다.

2. 초기 동기화가 완료되면 장애 조치를 완료하도록 선택합니다. 완료되면 온-프레미스 VM에 로그온하여 모든 것이 기대한 대로 작동하는지 확인할 수 있습니다. Azure Portal에서 Azure VM이 중지된 것을 확인할 수 있습니다.
3.  그런 다음 장애 조치를 커밋하여 완료하고 다시 온-프레미스 VM에서 워크로드에 액세스합니다.
4. 워크로드가 장애 복구되면 역방향 복제를 활성화하여 온-프레미스 VM이 다시 Azure에 복제되게 합니다.



## <a name="next-steps"></a>다음 단계


[이 자습서](tutorial-prepare-azure.md)를 따라 Hyper-V와 Azure 간의 복제를 시작합니다.


