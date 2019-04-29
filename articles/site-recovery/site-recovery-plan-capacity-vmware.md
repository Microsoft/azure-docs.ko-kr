---
title: Azure Site Recovery를 사용하여 Azure로 VMware 재해 복구를 수행하기 위한 용량 및 크기 조정 계획 | Microsoft Docs
description: 이 문서는 Azure Site Recovery를 사용하여 Azure로의 복제로 VMware VM의 재해 복구를 설정할 때 적용할 용량 및 크기 조정을 계획하는 데 참고할 수 있습니다.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 7b10db06ab4ade1b23985b1a259d82d4818941b1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124969"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Azure로 VMware 재해 복구를 위한 용량 및 크기 조정 계획

이 문서를 통해 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 온-프레미스 VMware VM 및 물리적 서버를 Azure에 복제할 때 적용할 용량 및 크기 조정을 계획할 수 있습니다.

## <a name="how-do-i-start-capacity-planning"></a>용량 계획을 시작하려면 어떻게 해야 하나요?

Azure Site Recovery 인프라 요구 사항을 알아보려면 VMware 복제를 위해 [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc)를 실행하여 자신의 복제 환경에 대한 정보를 수집하세요. 자세한 내용은 [VMware에서 Azure로의 Azure Site Recovery Deployment Planner 정보](site-recovery-deployment-planner.md)를 참조하세요. 

Site Recovery Deployment Planner는 호환 및 호환되지 않는 VM, VM당 디스크 및 디스크당 데이터 변동에 대한 전체 정보가 포함된 보고서를 제공합니다. 이 도구는 대상 RPO를 충족하기 위한 네트워크 대역폭 요구 사항과 성공적인 복제 및 테스트 장애 조치(failover)에 필요한 Azure 인프라에 대해서도 요약합니다.

## <a name="capacity-considerations"></a>용량 고려 사항

구성 요소 | 세부 정보
--- | ---
**복제** | **최대 일일 변동률:** 보호된 머신은 프로세스 서버를 하나만 사용할 수 있습니다. 단일 프로세스 서버는 최대 2TB의 일일 변동률을 처리할 수 있습니다. 따라서 2TB는 보호된 머신에 지원되는 최대 일일 데이터 변경률입니다.<br /><br /> **최대 처리량:** 복제된 컴퓨터는 Azure에서 하나의 스토리지 계정에 속할 수 있습니다. 표준 Azure Storage 계정은 초당 최대 20,000개의 요청을 처리할 수 있습니다. 따라서 원본 머신 전반에서 IOPS(초당 입출력 작업)를 20,000으로 제한하는 것이 좋습니다. 예를 들어 원본 머신의 디스크가 5개이고 각 디스크가 원본 머신에서 120 IOPS(8K 크기)를 생성할 경우, 원본 머신은 Azure 내에서 디스크당 IOPS 한도인 500을 초과하지 않습니다. (필요한 스토리지 계정 수는 총 원본 머신 IOPS를 20,000으로 나눈 값입니다.)
**구성 서버** | 구성 서버는 보호된 머신에서 실행되는 모든 워크로드에서 일일 변경률 용량을 처리할 수 있어야 합니다. 구성 머신에는 Azure Storage에 데이터를 지속적으로 복제하기에 충분한 대역폭이 있어야 합니다.<br /><br /> 가장 좋은 방법은 보호하려는 머신과 동일한 네트워크 및 LAN 세그먼트에 구성 서버를 배치하는 것입니다. 구성 서버를 다른 네트워크에 배치할 수도 있지만 보호하려는 머신에 레이어 3 네트워크 가시성이 있어야 합니다.<br /><br /> 구성 서버에 권장하는 크기는 다음 섹션의 테이블에 요약되어 있습니다.
**프로세스 서버** | 첫 번째 프로세스 서버는 기본적으로 구성 서버에 설치됩니다. 사용자 환경의 크기를 조정하는 추가 프로세스 서버를 배포할 수 있습니다. <br /><br /> 프로세스 서버는 보호된 머신에서 복제 데이터를 수신합니다. 또한 캐싱, 압축 및 암호화를 사용하여 데이터를 최적화합니다. 그런 다음, Azure에 데이터를 보냅니다. 프로세스 서버 머신에는 이러한 작업을 수행하기에 충분한 리소스가 있어야 합니다.<br /><br /> 프로세스 서버는 디스크 기반 캐시를 사용합니다. 네트워크 병목 현상 또는 중단이 발생하는 경우 저장된 데이터 변경을 처리할 수 있도록 600GB 이상의 별도의 캐시 디스크를 사용하세요.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>구성 서버 및 기본 제공 프로세스 서버에 대한 크기 권장 사항

기본 제공 프로세스 서버를 사용하여 워크로드를 보호하는 구성 서버는 다음 구성에 따라 최대 200개의 가상 머신을 처리할 수 있습니다.

CPU | 메모리 | 캐시 디스크 크기 | 데이터 변경률 | 보호된 머신
--- | --- | --- | --- | ---
8개 vCPU(2개 소켓 * 4코어 \@ 2.5GHz) | 16GB | 300GB | 500GB 이하 | 100대 미만의 머신을 복제하는 데 사용됩니다.
12개 vCPU(2개 소켓 * 6코어 \@ 2.5GHz) | 18GB | 600GB | 501GB~1TB | 100~150대의 머신을 복제하는 데 사용됩니다.
16개 vCPU(2개 소켓 * 8코어 \@ 2.5GHz) | 32GB | 1TB | >1TB~2TB | 151~200개의 머신을 복제하는 데 사용됩니다.
[OVF 템플릿](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template)을 사용하여 다른 구성 서버 배포 | | | | 200대가 넘는 머신을 복제하는 경우 새 구성 서버를 배포합니다.
다른 [프로세스 서버](vmware-azure-set-up-process-server-scale.md#download-installation-file) 배포 | | | >2TB| 전체 일별 데이터 변경률이 2TB를 초과하는 경우 새 스케일 아웃 프로세스 서버를 배포합니다.

이러한 구성에서 다음을 확인할 수 있습니다.

* 각 원본 머신마다 100GB 디스크가 3개 있습니다.
* 캐시 디스크 측정을 위해 벤치마킹 스토리지로 RAID 10을 이용한 10K RPM의 공유 액세스 서명 드라이브를 8개 사용했습니다.

## <a name="size-recommendations-for-the-process-server"></a>프로세스 서버에 대한 크기 권장 사항

프로세스 서버는 Azure Site Recovery에서 데이터 복제를 처리하는 구성 요소입니다. 일일 변경률이 2TB를 초과하는 경우 복제 로드를 처리할 스케일 아웃 프로세스 서버를 추가해야 합니다. 다음과 같이 규모를 확장할 수 있습니다.

* [OVF 템플릿](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template)을 사용 및 배포하여 구성 서버 수를 늘립니다. 예를 들어 구성 서버를 두 대 사용할 경우 최대 400개의 머신을 보호할 수 있습니다.
* [스케일 아웃 프로세스 서버](vmware-azure-set-up-process-server-scale.md#download-installation-file)를 추가합니다. 스케일 아웃 프로세스 서버를 사용하여 구성 서버 대신(또는 함께) 복제 트래픽을 처리합니다.

다음 표는 이 시나리오를 설명합니다.

* 스케일 아웃 프로세스 서버를 설정했습니다.
* 스케일 아웃 프로세스 서버를 사용하도록 보호된 가상 머신을 구성했습니다.
* 보호된 각 원본 머신마다 100GB 디스크가 3개씩 있습니다.

추가 프로세스 서버 | 캐시 디스크 크기 | 데이터 변경률 | 보호된 머신
--- | --- | --- | ---
4vCPU(소켓 2개 * 2코어 \@ 2.5GHz), 8GB 메모리 | 300GB | 250GB 이하 | 85개 이하의 머신을 복제하는 데 사용됩니다.
8vCPU(소켓 2개 * 4코어 \@ 2.5GHz), 12GB 메모리 | 600GB | 251GB~1TB | 86~150개의 머신을 복제하는 데 사용됩니다.
12vCPU(소켓 2개 * 6코어 \@ 2.5GHz), 24GB 메모리 | 1TB | >1TB~2TB | 151~225개의 머신을 복제하는 데 사용됩니다.

서버 크기를 조정하는 방법은 강화 모델 또는 규모 확장 모델에 대한 선호도에 따라 달라집니다. 강화하려면 몇 가지 고급 구성 서버와 프로세스 서버를 배포합니다. 확장하려면 리소스가 적은 서버를 더 많이 배포합니다. 예를 들어, 전반적인 일일 데이터 변경률이 1.5TB인 머신 200개를 보호하려면 다음 작업 중 하나를 수행하면 됩니다.

* 단일 프로세스 서버(16vCPU, 24GB RAM)를 설정합니다.
* 2개의 프로세스 서버(8vCPU 2개, 12GB RAM 2개)를 설정합니다.

## <a name="control-network-bandwidth"></a>네트워크 대역폭 제어

[Site Recovery Deployment Planner](site-recovery-deployment-planner.md)를 실행하여 복제(초기 복제 및 델타)에 필요한 대역폭을 계산한 다음, 복제에 사용되는 대역폭 양을 제어할 수 있는 옵션은 다음과 같이 두 가지입니다.

* **대역폭 제한**: Azure에 복제하는 VMware 트래픽이 특정 프로세스 서버를 통과합니다. 프로세스 서버로 실행되는 머신에서 대역폭을 제한할 수 있습니다.
* **대역폭 영향**: 몇 가지 레지스트리 키를 사용하면 복제에 사용되는 대역폭에 영향을 줄 수 있습니다.
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** 레지스트리 값은 디스크의 데이터 전송(초기 또는 델타 복제)에 사용되는 스레드 수를 지정합니다. 값이 높을수록 복제에 사용되는 네트워크 대역폭이 증가합니다.
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** 레지스트리 값은 장애 복구(failback) 중에 데이터 전송에 사용되는 스레드 수를 지정합니다.

### <a name="throttle-bandwidth"></a>대역폭 제한

1. 프로세스 서버 역할을 하는 머신에서 Azure Backup MMC 스냅인을 엽니다. 기본적으로 Backup 바로 가기는 바탕 화면이나 다음 폴더에서 사용할 수 있습니다. C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. 스냅인에서 **속성 변경**을 선택합니다.

    ![속성을 변경하는 Azure Backup MMC 스냅인 옵션의 스크린샷](./media/site-recovery-vmware-to-azure/throttle1.png)
3. **제한** 탭에서 **백업 작업에 인터넷 대역폭 사용 제한 사용**을 선택합니다. 작업 시간 및 비 작업 시간의 제한을 설정합니다. 유효 범위는 512Kbps~1,023Mbps입니다.

    ![Azure Backup 속성 대화 상자의 스크린샷](./media/site-recovery-vmware-to-azure/throttle2.png)

[Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) cmdlet를 사용하여 제한을 설정할 수도 있습니다. 예를 들면 다음과 같습니다.

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** 은 제한이 필요 없다는 뜻입니다.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>VM의 네트워크 대역폭 변경

1. VM의 레지스트리에서 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**으로 이동합니다.
   * 복제 디스크의 대역폭 트래픽을 변경하려면 **UploadThreadsPerVM** 값을 수정합니다. 키가 없는 경우 키를 만듭니다.
   * Azure에서 장애 복구(failback) 트래픽에 대한 대역폭을 변경하려면 **DownloadThreadsPerVM** 값을 수정합니다.
2. 각 키의 기본값은 **4**입니다. "과도하게 프로비전된" 네트워크에서는 이러한 레지스트리 키를 기본값에서 변경해야 합니다. 사용 가능한 최댓값은 **32**입니다. 트래픽을 모니터링하여 값을 최적화합니다.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>500개를 초과하는 VM을 보호하도록 Site Recovery 인프라 설정

Site Recovery 인프라를 설정하기 전에 환경에 액세스하여 호환되는 가상 머신, 일일 데이터 변경률, 달성해야 하는 RPO에 필요한 네트워크 대역폭, 필요한 Site Recovery 구성 요소 수, 초기 복제를 완료하는 데 소요되는 시간 등의 요소를 측정합니다. 다음 단계를 완료하여 필요한 정보를 수집합니다.

1. 이러한 매개 변수를 측정하려면 사용자 환경에서 Site Recovery Deployment Planner를 실행합니다. 유용한 지침은 [VMware에서 Azure로의 Azure Site Recovery Deployment Planner 정보](site-recovery-deployment-planner.md)를 참조하세요.
2. [구성 서버의 권장 크기](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server)에 맞는 구성 서버를 배포합니다. 프로덕션 워크로드가 650개 가상 머신을 초과하는 경우 다른 구성 서버를 배포합니다.
3. 측정된 일일 데이터 변경률을 기준으로 [크기 지침](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)에 따라 [스케일 아웃 프로세스 서버](vmware-azure-set-up-process-server-scale.md#download-installation-file)를 배포합니다.
4. 데이터 변경 률 2 MBps를 초과 하는 디스크 가상 머신에 대 한 예상 되는 경우 프리미엄 managed disks를 사용 하는 확인 합니다. Site Recovery Deployment Planner는 특정 기간 동안 실행됩니다. 다른 시간의 데이터 변경률 최고치는 보고서에 캡처되지 않을 수 있습니다.
5. 달성하려는 RPO에 따라 [네트워크 대역폭을 설정](site-recovery-plan-capacity-vmware.md#control-network-bandwidth)합니다.
6. 인프라가 설정되면 워크로드에 대한 재해 복구를 활성화합니다. 방법을 알아보려면 [Azure 복제에 대한 VMware의 원본 환경 설정](vmware-azure-set-up-source.md)을 참조하세요.

## <a name="deploy-additional-process-servers"></a>추가 프로세스 서버 배포

원본 머신이 200개를 초과하도록 배포를 스케일 아웃하거나 총 일일 변동률이 2TB를 초과하는 경우에는 트래픽 볼륨을 처리할 프로세스 서버를 추가해야 합니다. 제품 제공 9.24 버전에서 향상 되었습니다 [제품 설명서](vmware-azure-manage-process-server.md#process-server-selection-guidance) 스케일 아웃 프로세스 서버를 설정 하는 경우에 합니다. [프로세스 서버 설정](vmware-azure-set-up-process-server-scale.md) 새 원본 컴퓨터를 보호 하기 위해 또는 [부하 분산](vmware-azure-manage-process-server.md#balance-the-load-on-process-server)합니다.

### <a name="migrate-machines-to-use-the-new-process-server"></a>새 프로세스 서버를 사용하도록 컴퓨터 마이그레이션

1. **설정** > **Site Recovery 서버**를 선택합니다. 구성 서버를 선택한 다음, **프로세스 서버**를 확장합니다.

    ![프로세스 서버 대화 상자의 스크린샷](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. 현재 사용 중인 프로세스 서버를 마우스 오른쪽 단추로 클릭한 다음, **전환**을 클릭합니다.

    ![구성 서버 대화 상자의 스크린샷](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. **대상 프로세스 서버 선택**에서 사용할 새 프로세스 서버를 선택합니다. 그런 다음, 서버에서 처리할 가상 머신을 선택합니다. 서버에 대한 정보를 얻으려면 정보 아이콘을 선택합니다. 부하 결정에 도움이 되도록 선택된 각 가상 머신을 새 프로세스 서버로 복제하는 데 필요한 평균 공간이 표시됩니다. 확인 표시를 선택하여 새 프로세스 서버로 복제를 시작합니다.

## <a name="deploy-additional-master-target-servers"></a>추가 마스터 대상 서버 배포

다음 시나리오에서 둘 이상의 마스터 대상 서버가 필요합니다.

*   Linux 기반 가상 머신을 보호하려고 합니다.
*   구성 서버에서 사용할 수 있는 마스터 대상 서버가 VM의 데이터 저장소에 액세스할 수 없습니다.
*   마스터 대상 서버의 총 디스크 수(서버의 로컬 디스크 수와 보호할 디스크 수)가 60개를 초과합니다.

Linux 기반 가상 머신에 마스터 대상 서버를 추가하는 방법을 알아보려면 [장애 복구(failback)를 위한 Linux 마스터 대상 서버 설치](vmware-azure-install-linux-master-target.md)를 참조하세요.

Windows 기반 가상 머신에 대해 마스터 대상 서버를 추가하려면:

1. **Recovery Services 자격 증명 모음** > **Site Recovery 인프라** > **구성 서버**로 이동합니다.
2. 필요한 구성 서버를 선택한 다음, **마스터 대상 서버**를 선택합니다.

    ![마스터 대상 서버 추가 단추를 보여주는 스크린샷](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. 통합 설치 파일을 다운로드 한 다음, VM에서 파일을 실행하여 마스터 대상 서버를 설치합니다.
4. **마스터 대상 설치** > **다음**을 선택합니다.

    ![마스터 대상 설치 옵션 선택을 보여주는 스크린샷](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. 기본 설치 위치를 선택한 다음, **설치**를 선택합니다.

     ![기본 설치 위치를 보여주는 스크린샷](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. 마스터 대상을 구성 서버에 등록하려면 **Proceed To Configuration**(구성으로 계속 진행)을 선택합니다.

    ![Proceed To Configuration(구성으로 계속 진행) 단추를 보여주는 스크린샷](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. 구성 서버의 IP 주소를 입력한 다음, 암호를 입력합니다. 암호를 생성하는 방법을 알아보려면 [구성 서버 암호 생성](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)을 참조하세요. 

    ![구성 서버에 대한 IP 주소와 암호를 입력하는 곳을 보여주는 스크린샷](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. **등록**을 선택합니다. 등록을 마치면 **마침**을 선택합니다.

등록이 성공적으로 완료되면, Azure Portal의 **Recovery Services 자격 증명 모음** > **Site Recovery 인프라** > **구성 서버**에 구성 서버의 마스터 대상 서버 아래에 해당 서버가 나열됩니다.

 > [!NOTE]
 > [Windows용 마스터 대상 서버 통합 설치 파일](https://aka.ms/latestmobsvc)의 최신 버전을 다운로드하십시오.

## <a name="next-steps"></a>다음 단계

[Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner)를 다운로드 및 실행합니다.
