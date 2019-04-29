---
title: Azure Site Recovery의 VMware와 Azure 간 재해 복구 아키텍처 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 온-프레미스 VMware VM과 Azure 간 재해 복구를 설정할 때 사용되는 구성 요소 및 아키텍처를 간략하게 설명합니다.
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: raynew
ms.openlocfilehash: d9fdd6f42e1443c0515c2c38496e9d474c87715c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837426"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>VMware와 Azure 간 재해 복구 아키텍처

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 VMware 사이트와 Azure 간에 VMware VM(가상 머신)의 재해 복구 복제, 장애 조치(failover) 및 복구를 배포할 때 사용되는 아키텍처와 프로세스에 대해 설명합니다.


## <a name="architectural-components"></a>아키텍처 구성 요소

다음 표와 그림은 VMware와 Azure 간 재해 복구에 사용되는 구성 요소를 개략적으로 보여 줍니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
**Azure** | Azure 구독, 캐시, 관리 디스크와 Azure 네트워크에 대 한 Azure Storage 계정. | 온-프레미스 Vm에서 복제 된 데이터는 Azure storage에 저장 됩니다. 장애 조치를 온-프레미스에서 Azure로 실행할 때 복제된 데이터를 사용하여 Azure VM을 만듭니다. Azure VM을 만들 때 Azure 가상 네트워크에 연결합니다.
**구성 서버 컴퓨터** | 단일 온-프레미스 컴퓨터입니다. 다운로드한 OVF 템플릿에서 배포할 수 있는 VMware VM으로 실행하는 것이 좋습니다.<br/><br/> 이 컴퓨터는 구성 서버, 프로세스 서버 및 마스터 대상 서버를 포함하는 모든 온-프레미스 Site Recovery 구성 요소를 실행합니다. | **구성 서버**: 온-프레미스와 Azure 간의 통신을 조정하여 데이터 복제를 관리합니다.<br/><br/> **프로세스 서버**: 기본적으로 구성 서버에 설치합니다. 복제 데이터를 수신하고, 캐싱, 압축 및 암호화를 사용하여 최적화하며, Azure Storage로 보냅니다. 또한 프로세스 서버는 복제하려는 VM에 Azure Site Recovery 모바일 서비스를 설치하고, 온-프레미스 컴퓨터의 자동 검색을 수행합니다. 배포가 늘어나면 프로세스 서버로 실행하는 별도의 프로세스 서버를 추가하여 더 큰 복제 트래픽을 처리할 수 있습니다.<br/><br/> **마스터 대상 서버**: 기본적으로 구성 서버에 설치합니다. Azure에서 장애 복구(Failback) 중에 복제 데이터를 처리합니다. 대규모 배포의 경우 장애 복구를 위해 추가적인 별도의 마스터 대상 서버를 추가할 수 있습니다.
**VMware 서버** | VMware VM은 온-프레미스 vSphere ESXi 서버에서 호스트됩니다. 호스트를 관리하려면 vCenter 서버를 사용하는 것이 좋습니다. | Site Recovery 배포 중에 VMware 서버를e Recovery Services 자격 증명 모음에 추가합니다.
**복제된 컴퓨터** | 복제한 각 VMware VM에 모바일 서비스가 설치됩니다. | 프로세스 서버에서 자동 설치를 수행할 수 있도록 하는 것이 좋습니다. 또는 서비스를 수동으로 설치하거나 System Center Configuration Manager와 같은 자동화된 배포 방법을 사용할 수 있습니다.

**VMware에서 Azure로 아키텍처**

![구성 요소](./media/vmware-azure-architecture/arch-enhanced.png)



## <a name="replication-process"></a>복제 프로세스

1. VM에 대해 복제를 사용하도록 설정하면 지정된 복제 정책을 사용하여 Azure Storage에 초기 복제가 시작됩니다. 다음 사항에 유의하세요.
    - VMware VM의 경우 복제는 VM에서 실행 중인 모바일 서비스 에이전트를 사용하여 블록 수준에서 거의 지속적으로 이루어집니다.
    - 복제 정책 설정이 적용됩니다.
        - **RPO 임계값**: 이 설정은 복제에 영향을 주지 않습니다. 모니터링에 도움이 됩니다. 현재 RPO가 지정 임계값 한도를 초과하는 경우 이벤트가 발생하고 선택적으로 메일이 전송됩니다.
        - **복구 지점 보존**: 이 설정은 중단이 발생하는 경우 얼마나 오래전으로 되돌아갈지 지정합니다. Premium Storage의 최대 보존 기간은 24시간입니다. 표준 저장소의 경우는 72시간입니다. 
        - **앱 일치 스냅숏**: 앱의 요구 사항에 따라 1시간에서 12시간마다 앱 일치 스냅숏을 만들 수 있습니다. 스냅숏은 표준 Azure Blob 스냅숏입니다. VM에서 실행되는 모바일 에이전트는 이 설정에 따라 VSS 스냅숏을 요청하며 이 특정 시점을 복제 스트림의 애플리케이션 일치 지점으로 북마크합니다.

2. 트래픽은 인터넷을 통해 Azure Storage 공용 엔드포인트에 복제됩니다. Azure ExpressRoute과 [공용 피어링](../expressroute/expressroute-circuit-peerings.md#publicpeering)을 함께 사용할 수도 있습니다. 온-프레미스 사이트에서 Azure로의 사이트 간 VPN(가상 사설망)을 통한 트래픽 복제는 지원되지 않습니다.
3. 초기 복제가 완료되면 Azure에 대한 델타 변경 내용의 복제가 시작됩니다. 머신의 추적된 변경 내용이 프로세스 서버로 전송됩니다.
4. 다음과 같이 통신이 발생합니다.

    - VM은 복제 관리를 위해 HTTPS 443 인바운드 포트에 대한 온-프레미스 구성 서버와 통신합니다.
    - 구성 서버는 HTTPS 443 아웃바운드 포트를 통해 Azure를 사용하는 복제를 오케스트레이션합니다.
    - VM은 HTTPS 9443 인바운드 포트의 프로세스 서버(구성 서버 컴퓨터에서 실행)로 복제 데이터를 전송합니다. 이 포트는 수정할 수 있습니다.
    - 프로세스 서버는 복제 데이터를 수신하고, 이를 최적화 및 암호화하며, 443 아웃바운드 포트를 통해 Azure Storage로 보냅니다.




**VMware에서 Azure로 복제 프로세스**

![복제 프로세스](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>장애 조치 및 장애 복구 프로세스

복제가 설정되고 재해 복구 훈련(테스트 장애 조치)을 실행하여 모든 것이 예상대로 작동하는지 확인한 후 필요에 따라 장애 조치와 장애 복구를 실행할 수 있습니다.

1. 단일 컴퓨터에서 장애 조치(Failover)를 수행하거나 여러 VM을 동시에 장애 조치(Failover)할 복구 계획을 만들 수 있습니다. 단일 컴퓨터 장애 조치(Failover) 이외의 복구 계획을 사용할 때의 이점은 다음과 같습니다.
    - 단일 복구 계획에 앱의 모든 VM을 포함하여 앱 종속성을 모델링할 수 있습니다.
    - 스크립트, Azure Runbook 및 수동 작업에 대한 일시 중지를 추가할 수 있습니다.
2. 초기 장애 조치를 트리거한 후 이를 커밋하여 Azure VM에서 워크로드 액세스를 시작합니다.
3. 기본 온-프레미스 사이트를 다시 사용할 수 있는 경우 장애 복구(Failback)를 준비할 수 있습니다. 장애 복구(Failback)를 위해서는 다음을 포함한 장애 복구 인프라를 설정해야 합니다.

    * **Azure의 임시 프로세스 서버**: Azure에서 장애 복구하려면 Azure에서 복제를 처리하는 프로세스 서버로 작동하도록 Azure VM을 설정합니다. 장애 복구를 완료한 후 이 VM을 삭제할 수 있습니다.
    * **VPN 연결**: 장애 복구(failback)하려면 Azure 네트워크에서 온-프레미스 사이트로의 VPN 연결(또는 ExpressRoute)이 필요합니다.
    * **개별 마스터 대상 서버**: 기본적으로 구성 서버와 함께 온-프레미스 VMware VM에 설치된 마스터 대상 서버에서 장애 복구를 처리합니다. 대량 트래픽을 장애 복구해야 하는 경우 별도의 온-프레미스 마스터 대상 서버를 이 용도로 설정합니다.
    * **장애 복구 정책**: 온-프레미스 사이트에 다시 복제하려면 장애 복구 정책이 필요합니다. 이 정책은 온-프레미스에서 Azure로의 복제 정책을 만들 때 자동으로 만들어졌습니다.
4. 구성 요소를 배치한 후 다음 3가지 동작에 따라 장애 복구(Failback)가 발생합니다.

    - 1단계: Azure에서 다시 온-프레미스 VMware VM으로 복제하도록 Azure VM을 다시 보호합니다.
    -  2단계: 온-프레미스 사이트로 장애 조치를 실행합니다.
    - 3단계: 워크로드가 장애 복구되면 온-프레미스 VM에 대한 복제를 다시 사용하도록 설정합니다.
    
 
**Azure로부터 VMware 장애 복구**

![장애 복구](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>다음 단계

[이 자습서](vmware-azure-tutorial.md)를 따라 VMware와 Azure 간의 복제를 활성화합니다.