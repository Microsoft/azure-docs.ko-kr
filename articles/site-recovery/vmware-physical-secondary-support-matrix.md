---
title: Azure Site Recovery를 사용하여 VMware VM 또는 물리적 서버에서 보조 VMware 사이트로 재해 복구하기 위한 지원 매트릭스 | Microsoft Docs
description: Azure Site Recovery를 사용한 VMware VM 및 물리적 서버와 보조 사이트 간의 재해 복구 지원이 요약되어 있습니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: raynew
ms.openlocfilehash: b316d6a8293d9f23eb89e8b6fffedac316759df4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564853"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>VMware VM 또는 물리적 서버에서 보조 사이트로 재해 복구하기 위한 지원 매트릭스

이 문서에는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 VMware VM 또는 Windows/Linux 물리적 서버에서 보조 VMware 사이트로 재해 복구하는 경우 지원되는 사항이 요약되어 있습니다.

- VMware VM 또는 물리적 서버를 Azure에 복제하려면 [이 지원 매트릭스](vmware-physical-azure-support-matrix.md)를 검토하세요.
- Hyper-V VM을 보조 사이트에 복제하려면 [이 지원 매트릭스](hyper-v-azure-support-matrix.md)를 검토하세요.

> [!NOTE]
> 온-프레미스 VMware VM 및 물리적 서버 복제는 InMage Scout에서 제공합니다. InMage Scout는 Azure Site Recovery 서비스 구독에 포함되어 있습니다.


## <a name="host-servers"></a>호스트 서버

**운영 체제** | **세부 정보**
--- | ---
vCenter Server | vCenter 5.5, 6.0 및 6.5<br/><br/> 6.0 또는 6.5를 실행하는 경우 5.5 기능만 지원됩니다.


## <a name="replicated-vm-support"></a>복제된 VM 지원

다음 표에서는 Site Recovery를 사용하여 복제된 컴퓨터에 대한 운영 체제 지원을 요약합니다. 워크로드를 지원되는 운영 체제에서 실행할 수 있습니다.

**운영 체제** | **세부 정보**
--- | ---
Windows Server | 64비트 Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1 이상.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4, 6.5, 6.8 <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


## <a name="linux-machine-storage"></a>Linux 컴퓨터 저장소

다음 저장소를 포함한 Linux 컴퓨터만을 복제할 수 있습니다.

- 파일 시스템(EXT3, ETX4, ReiserFS, XFS)
- 다중 경로 소프트웨어 장치 매퍼
- 볼륨 관리자(LVM2)
- HP CCISS 컨트롤러 저장소가 있는 물리적 서버는 지원되지 않습니다.
- ReiserFS 파일 시스템은 SUSE Linux Enterprise Server 11 SP3에서만 지원됩니다.

## <a name="network-configuration---hostguest-vm"></a>네트워크 구성 - 호스트/게스트 VM

**구성** | **지원됨**  
--- | --- 
호스트 - NIC 팀 | 예 
호스트 - VLAN | 예 
호스트 - IPv4 | 예 
호스트 - IPv6 | 아닙니다. 
게스트 VM - NIC 팀 | 아닙니다.
게스트 VM - IPv4 | 예
게스트 VM - IPv6 | 아닙니다.
게스트 VM - Windows/Linux - 고정 IP 주소 | 예
게스트 VM - 다중 NIC | 예


## <a name="storage"></a>Storage

### <a name="host-storage"></a>호스트 저장소

**Storage(호스트)** | **지원됨** 
--- | --- 
NFS | 예 
SMB 3.0 | N/A 
SAN(ISCSI) | 예 
다중 경로(MPIO) | 예 

### <a name="guest-or-physical-server-storage"></a>게스트 또는 물리적 서버 저장소

**구성** | **지원됨** 
--- | --- 
VMDK | 예 
VHD/VHDX | N/A 
2세대 VM | N/A 
공유 클러스터 디스크 | 예 
암호화된 디스크 | 아닙니다. 
UEFI| 예 
NFS | 아닙니다. 
SMB 3.0 | 아닙니다. 
RDM | 예 
디스크 > 1TB | 예 
스트라이프 디스크 포함 볼륨 > 1TB<br/><br/> LVM | 예 
저장소 공간 | 아닙니다. 
디스크 핫 추가/제거 | 예 
디스크 제외 | 예 
다중 경로(MPIO) | N/A 

## <a name="vaults"></a>자격 증명 모음

**작업** | **지원됨** 
--- | --- 
리소스 그룹 간에 자격 증명 모음 이동(동일 구독 내 또는 구독 간에) | 아닙니다. 
리소스 그룹 간에 저장소, 네트워크, Azure VM 이동(동일 구독 내 또는 구독 간에) | 아닙니다. 

## <a name="mobility-service-and-updates"></a>Mobility 서비스 및 업데이트

Mobility 서비스는 온-프레미스 VMware 서버 또는 물리적 서버와 보조 사이트 간 복제를 조정합니다. 복제를 설정할 때 Mobility 서비스 및 기타 구성 요소의 최신 버전이 필요합니다.

| **업데이트** | **세부 정보** |
| --- | --- |
|Scout 업데이트 | Scout 업데이트는 누적입니다. <br/><br/> 최신 Scout 업데이트에 대해 [알아보고 다운로드](vmware-physical-secondary-disaster-recovery.md#updates)  |
|구성 요소 업데이트 | Scout 업데이트에는 RX 서버, 구성 서버, 프로세스 및 마스터 대상 서버, vContinuum 서버 및 보호하려면 원본 서버를 포함하여 모든 구성 요소에 대한 업데이트가 포함되어 있습니다.<br/><br/> [자세히 알아보기](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>다음 단계

[InMage Scout 사용자 가이드](https://aka.ms/asr-scout-user-guide)를 다운로드합니다.

- [VMM 클라우드의 Hyper-V VM에서 보조 사이트로 복제](tutorial-vmm-to-vmm.md)
- [VMware VM 및 물리적 서버를 보조 사이트에 복제](tutorial-vmware-to-vmware.md)
