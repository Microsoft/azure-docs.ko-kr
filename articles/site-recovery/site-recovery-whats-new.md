---
title: Azure Site Recovery의 새로운 기능 | Microsoft Docs
description: Azure Site Recovery에 도입된 새 기능에 대한 요약 정보를 제공합니다.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: raynew
ms.openlocfilehash: 5ee1328dddb6ae1e1c878384097b0e10aa32feeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776291"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery의 새로운 기능

[Azure Site Recovery](site-recovery-overview.md) 서비스는 지속적으로 업데이트되고 개선됩니다. 서비스를 최신 상태로 유지할 수 있도록 이 문서에서는 최신 릴리스, 새 기능 및 새 콘텐츠에 대한 정보를 제공합니다. 이 페이지는 정기적으로 업데이트됩니다.

Site Recovery 기능에 대한 [여러분의 의견](https://feedback.azure.com/forums/256299-site-recovery)은 언제든지 환영합니다.

## <a name="q1-2019"></a>2019년 1분기 

### <a name="update-rollup-34-february-2019"></a>업데이트 롤업 34 (2019 년 2 월)

[업데이트 롤업 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) 다음 업데이트를 제공 합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 (롤업에 자세 함)에 대 한 업데이트
**문제 수정** | 다양 한 수정 및 개선 사항 (단계에서 설명한 대로 롤업)



### <a name="update-rollup-33-february-2019"></a>업데이트 롤업 33 (2019 년 2 월)

[업데이트 롤업 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) 다음 업데이트를 제공 합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 (롤업에 자세 함)에 대 한 업데이트
**문제 수정** | 다양 한 수정 및 개선 사항 (단계에서 설명한 대로 롤업)
**네트워크 매핑** | Azure VM 재해 복구를 위해 복제를 사용 하도록 설정 하면 모든 사용 가능한 대상 네트워크를 이제 사용할 수 있습니다. 
**표준 SSD** | 이제 다음을 사용 하 여 Azure Vm에 대 한 재해 복구를 설정할 수 있습니다 [표준 SSD 디스크](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)합니다.
**저장소 공간 다이렉트** | 사용 하 여 Azure VM 앱에서 실행 중인 앱에 대 한 재해 복구를 설정할 수 있습니다 [저장소 공간 다이렉트](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) 고가용성에 대 한 합니다.
**BRTFS 파일 시스템** | VMware vm의 경우 Azure Vm 외에도 지원 합니다.<br/><br/> 하는 경우 지원 되지 않습니다. BTRFS 파일 시스템 하위 볼륨은 복제를 사용 하도록 설정한 후 변경, 파일 시스템은 여러 디스크에 걸쳐 또는 파일 시스템의 BTRFS 경우 RAID를 지원 합니다.



### <a name="update-rollup-32-january-2019"></a>업데이트 롤업 32 (2019 년 1 월)

[업데이트 롤업 31](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) 다음 업데이트를 제공 합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 (롤업에 자세 함)에 대 한 업데이트
**문제 수정** | 다양 한 수정 및 개선 사항 (단계에서 설명한 대로 롤업)
**Linux에 대 한 재해 복구** | **Azure VM**: RedHat 워크스테이션 6/7 Ubuntu, Debian, SUSE에 대 한 새 커널 버전에 대 한 지원.<br/><br/> **VMware v m/물리적 서버**: Redhat Enterprise Linux 7.6; RedHat 워크스테이션 6/7 Oracle Linux 6.10/7.6; Ubuntu, Debian, SUSE에 대 한 새 커널 버전에 대 한 지원 하기 위해.


### <a name="update-rollup-31-january-2019"></a>업데이트 롤업 31 (2019 년 1 월)

[업데이트 롤업 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) 다음 업데이트를 제공 합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 (롤업에 자세 함)에 대 한 업데이트
**문제 수정** | 다양 한 수정 및 개선 사항 (단계에서 설명한 대로 롤업)
**Linux에 대 한 재해 복구** | **Azure VM**: Oracle Linux 6.8 및 6.9/7.0; UEK5 커널 지원 합니다.<br/><br/> **VMware v m/물리적 서버**: Oracle Linux 6.8 및 6.9/7.0; UEK5 커널에 대 한 지원입니다.
**BRTFS 파일 시스템** | Azure Vm에 대 한 지원.
**LVM** | LVM 및 LVM2 볼륨에 대 한 추가 지원 합니다.<br/><br/> LVM 볼륨에는 디스크 파티션에 /boot 디렉터리 지원 됩니다.
**Directories** | 별도 파티션 또는 동일한 시스템 디스크에 없는 파일 시스템으로 이러한 디렉터리 seet 추가로 지원 합니다. / (root), /boot, /usr, /usr/local, /var, /etc 합니다.
**Windows Server 2008** | 동적 디스크에 대 한 추가 지원 합니다.
**VMware VM 장애 조치** | Storvsc 및 vsbus 부팅 드라이버가 아닌 VMware Vm에 대 한 장애 조치 시간을 개선 합니다.
**UEFI 지원** | Azure Vm 부팅 유형이 UEFI를 지원 하지 않습니다. 이제 azure Site Recovery를 사용 하 여 UEFI 사용 하 여 온-프레미스 물리적 서버를 마이그레이션할 수 있습니다. Site Recovery는 마이그레이션을 시작 하기 전에 부팅 유형을 BIOS로 변환 하 여 서버를 마이그레이션합니다. Site Recovery 이전에이 변환을 vm만 지원 합니다. 지원은 이상 Windows Server 2012를 실행 중인 물리적 서버에서 사용할 수 있습니다.
**가용성 영역에서 azure Vm** | 가용성 영역에서 배포 된 Azure Vm에 대 한 다른 지역으로 복제를 사용할 수 있습니다. 이제 ou Azure VM에 대 한 복제를 사용 하도록 설정 하 고 단일 VM 인스턴스, 가용성 집합의 VM 또는 가용성 영역에서 VM을 장애 조치에 대 한 대상을 설정할 수 있습니다. 이 설정은 복제에 영향을 주지 않습니다. 공지를 [읽어보세요](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/).


## <a name="q4-2018"></a>2018년 4분기

### <a name="update-rollup-30-october-2018"></a>업데이트 롤업 30 (2018 년 10 월)

[업데이트 롤업 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) 다음 업데이트를 제공 합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 (롤업에 자세 함)에 대 한 업데이트
**문제 수정** | 다양 한 수정 및 개선 사항 (단계에서 설명한 대로 롤업)
**지역 지원** | Site Recovery 지원 오스트레일리아 중부 1 및 오스트레일리아 중부 2에 대 한 추가 합니다.
**디스크 암호화에 대 한 지원** | Azure AD 앱을 사용 하 여 Azure 디스크 암호화 (ADE)를 사용 하 여 암호화 하는 Azure Vm의 재해 복구에 대 한 추가 지원 합니다. [자세히 알아보기](azure-to-azure-how-to-enable-replication-ade-vms.md).
**디스크 제외** | 이제 Azure VM을 복제 하는 동안 초기화 되지 않은 디스크 자동으로 제외 됩니다.
**저장소 방화벽 사용** | 에 대 한 추가 지원이 [방화벽을 사용 하도록 설정 하는 저장소 계정](https://docs.microsoft.com/azure/storage/common/storage-network-security)합니다.<br/><br/> 재해 복구에 대 한 다른 Azure 지역에 방화벽을 사용 하도록 설정 하는 저장소 계정에서 관리 되지 않는 디스크를 사용 하 여 Azure Vm을 복제할 수 있습니다.<br/><br/> 관리 되지 않는 디스크에 대 한 방화벽을 사용 하도록 설정 하는 저장소 계정을 대상 저장소 계정으로 사용할 수 있습니다.<br/><br/> PowerShell를 사용 하 여만 지원 합니다.


### <a name="update-rollup-29-october-2018"></a>업데이트 롤업 29 (2018 년 10 월)

[업데이트 롤업 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) 다음 업데이트를 제공 합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 (롤업에 자세 함)에 대 한 업데이트
**문제 수정** | 다양 한 수정 및 개선 사항 (단계에서 설명한 대로 롤업)

### <a name="automatic-updates-for-the-mobility-service-extension"></a>모바일 서비스 확장의 자동 업데이트

Site Recovery는 모바일 서비스 확장의 자동 업데이트 옵션을 추가했습니다. 모바일 서비스 확장은 Site Recovery에서 복제한 각 Azure VM에 설치됩니다. 복제를 사용하도록 설정할 때 Site Recovery에서 확장 업데이트를 관리하도록 허용할 것인지 여부를 선택합니다. 업데이트 후 VM을 다시 시작할 필요가 없으며 복제에 영향을 주지 않습니다. [자세히 알아보기](azure-to-azure-autoupdate.md).

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>가속화된 네트워킹을 사용하여 VM 재해 복구

가속화된 네트워킹을 사용하면 VM에 대한 SR-IOV(단일 루트 I/O 가상화)를 구현하여 네트워킹 성능을 개선할 수 있습니다. Azure VM에 복제를 사용하도록 설정하면 Site Recovery는 가속화된 네트워킹의 사용 여부를 감지합니다. 사용되는 경우 장애 조치(failover) 후 Site Recovery는 자동으로 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) 및 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms)의 대상 복제본 Azure VM에서 가속화된 네트워킹을 구성합니다. [자세히 알아보기](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Azure VM 재해 복구의 새 가격 책정 계산기

Azure VM 재해 복구에는 VM 라이선스 비용과 네트워크 및 스토리지 비용이 발생합니다. Azure는 이러한 비용을 계산할 수 있는 [가격 책정 계산기](https://aka.ms/a2a-cost-estimator)를 제공합니다. 이제 Site Recovery는 표준 HDD 디스크 12개와 프리미엄 SSD 디스크 6개가 포함된 VM을 사용하는 3계층 앱을 기반으로 샘플 배포의 가격을 정하는 [가격 견적 예제](https://aka.ms/a2a-cost-estimator)를 제공합니다. 샘플은 표준은 하루 10GB, 프리미엄은 20GB의 데이터가 변경되는 것으로 가정합니다. 특정 배포에 맞게 변수를 예상 비용으로 변경할 수 있습니다. VM 수, 관리 디스크의 수 및 유형, VM에서 예상되는 총 데이터 변경률을 지정할 수 있습니다. 또한 압축 비율을 적용하여 대역폭 비용을 예측할 수 있습니다. 공지를 [읽어보세요](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).



## <a name="q3-2018"></a>2018년 3분기 


### <a name="update-rollup-28-august-2018"></a>업데이트 롤업 28 (2018 년 8 월)

[업데이트 롤업 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) 다음 업데이트를 제공 합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 (롤업에 자세 함)에 대 한 업데이트
**Linux에 대 한 재해 복구** | **Azure VM**: RedHat Enterprise Linux 6.10;에 대 한 지원 추가 CentOS 6.10 합니다.<br/><br/> **VMware Vm**: RedHat Enterprise Linux 6.10; CentOS 6.10.<br/><br/> Linux 기반 Vm GUID 파티션 테이블 (GPT) 파티션 스타일 레거시 BIOS 호환 모드에서 사용 하 여 이제 지원 됩니다. 참조 [Azure IaaS VM 디스크에 대 한 질문과 대답](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/faq-for-disks) 자세한 내용은 합니다. 
**클라우드 지원** | Azure 독일 클라우드의 Vm에 대 한 재해 복구를 지원 합니다.
**구독 간 재해 복구** | 한 지역의 Azure Vm을 동일한 Azure Active Directory 테 넌 트 내에서 다른 구독에서 다른 지역으로 복제를 지원 합니다. [자세히 알아보기](https://aka.ms/cross-sub-blog).
**Windows Server 2008** | 마이그레이션에 대 한 지원에는 실행 중인 Windows Server 2008 R2 2008 64 비트 및 32 비트 컴퓨터입니다.<br/><br/> 마이그레이션에만 (복제 및 장애 조치)입니다. 장애 복구는 지원 되지 않습니다.

### <a name="update-rollup-27-july-2018"></a>업데이트 롤업 27 (2018 년 7 월)

[업데이트 롤업 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) 다음 업데이트를 제공 합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 (롤업에 자세 함)에 대 한 업데이트
**Linux에 대 한 재해 복구** | **Azure VM**: Red Hat Enterprise Linux 7.5<br/><br/> **VMware v m/물리적 서버**: Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12




## <a name="next-steps"></a>다음 단계

[Azure 업데이트](https://azure.microsoft.com/updates/?product=site-recovery) 페이지에서 업데이트를 항상 최신 상태로 유지하세요.




 









