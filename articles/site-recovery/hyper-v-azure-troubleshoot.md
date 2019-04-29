---
title: Azure Site Recovery를 사용하여 Hyper-V와 Azure 간 재해 복구 문제 해결 | Microsoft Docs
description: Azure Site Recovery를 사용하여 Hyper-V와 Azure 간 복제의 재해 복구 문제를 해결하는 방법을 설명합니다.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: rajanaki
ms.openlocfilehash: 8bb790571e1499bd45fb8bee27f4f1896046cbc2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749109"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Hyper-V와 Azure 간 복제 및 장애 조치(Failover) 문제 해결

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 온-프레미스 Hyper-V VM과 Azure 간에 복제를 수행할 때 발생할 수 있는 몇 가지 일반적인 문제에 대해 설명합니다.

## <a name="enable-protection-issues"></a>보호 사용 문제

Hyper-V VM에 대해 보호를 사용하도록 설정할 경우 문제가 발생하면 다음 권장 사항을 확인하세요.

1. Hyper-V 호스트 및 VM이 모든 [요구 사항 및 필수 구성 요소](hyper-v-azure-support-matrix.md)를 충족하는지 확인합니다.
2. Hyper-V 서버가 System Center VMM(Virtual Machine Manager) 클라우드에 있는 경우 [VMM 서버](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional)가 준비되어 있는지 확인합니다.
3. Hyper-V 호스트에서 Hyper-V Virtual Machine Management 서비스가 실행되고 있는지 확인합니다.
4. VM의 Hyper-V-VMMS\Admin 로그인에 표시되는 문제를 확인합니다. 이 로그는 **Applications and Services Logs** > **Microsoft** > **Windows**에 있습니다.
5. 게스트 VM에서 WMI가 사용되도록 설정되어 있고 액세스 가능한지 확인합니다.
   - 기본 WMI 테스트에 대해 [자세히 알아봅니다](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/).
   - WMI [문제를 해결합니다](https://aka.ms/WMiTshooting).
   - [문제 해결](https://technet.microsoft.com/library/ff406382.aspx#H22) WMI 스크립트 및 서비스에 문제가 있습니다.
6. 게스트 VM에 최신 버전의 Integration Services가 실행되고 있는지 확인합니다.
    - 최신 버전이 있는지 [확인합니다](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Integration Services를 최신 상태로 [유지합니다](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date).
    
## <a name="replication-issues"></a>복제 문제

다음과 같이 초기 및 지속적인 복제 문제를 해결합니다.

1. [최신 버전](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)의 Site Recovery 서비스를 실행하고 있는지 확인합니다.
2. 복제가 일시 중지되었는지 여부를 확인합니다.
   - Hyper-V 관리자 콘솔에서 VM 상태를 확인합니다.
   - 상태가 심각한 경우 마우스 오른쪽 단추로 VM > **복제** > **복제 상태 보기**를 클릭합니다.
   - 복제가 일시 중지되면 **복제 다시 시작**을 클릭합니다.
3. 필요한 서비스가 실행되고 있는지 확인합니다. 그렇지 않은 경우 다시 시작합니다.
    - VMM을 사용하지 않고 Hyper-V를 복제하는 경우 다음 서비스가 Hyper-V 호스트에서 실행되고 있는지 확인합니다.
        - Virtual Machine Management 서비스
        - Microsoft Azure Recovery Services Agent 서비스
        - Microsoft Azure Site Recovery 서비스
        - WMI Provider Host 서비스
    - 작업 환경에서 VMM을 사용하여 복제하는 경우 다음 서비스가 실행되고 있는지 확인합니다.
        - Hyper-V 호스트에서 Virtual Machine Management 서비스, Microsoft Azure Recovery Services Agent 및 WMI Provider Host 서비스가 실행되고 있는지 확인합니다.
        - VMM 서버에서 System Center Virtual Machine Manager 서비스가 실행되고 있는지 확인합니다.
4. Hyper-V 서버와 Azure 간의 연결을 확인합니다. 연결을 확인하려면 Hyper-V 호스트에서 작업 관리자를 엽니다. **성능** 탭에서 **리소스 모니터 열기**를 클릭합니다. **네트워크** 탭 > **네트워크 활동이 있는 프로세스**에서 cbengine.exe가 대용량(Mbs)의 데이터를 전송하고 있는지를 확인합니다.
5. Hyper-V 호스트가 Azure Storage Blob URL에 연결할 수 있는지 확인합니다. 호스트를 연결할 수 있는지 확인하려면 **cbengine.exe**를 선택하고 확인합니다. **TCP 연결**에서 호스트-Azure Storage Blob 간 연결을 확인합니다.
6. 아래 설명된 대로 성능 문제를 확인합니다.
    
### <a name="performance-issues"></a>성능 문제

네트워크 대역폭 제한은 복제에 영향을 줄 수 있습니다. 이러한 문제는 다음과 같이 해결합니다.

1. 사용자 환경에 대역폭 제약 또는 제한 문제가 있는지 [확인](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage)합니다.
2. [Deployment Planner 프로파일러](hyper-v-deployment-planner-run.md)를 실행합니다.
3. 프로파일러를 실행한 후 [대역폭](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) 및 [저장소](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) 권장 사항을 따릅니다.
4. [데이터 변동 제한 사항](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)을 확인합니다. VM에서 높은 데이터 변동이 확인되면 다음을 수행합니다.
   - VM이 재동기화용으로 표시되어 있는지 여부를 확인합니다.
   - [이 단계](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/)에 따라 변동의 원본을 조사합니다.
   - HRL 로그 파일이 사용 가능한 디스크 공간의 50%를 초과하면 변동이 발생할 수 있습니다. 이것이 문제인 경우 문제가 발생하는 모든 VM에 대해 더 많은 저장소 공간을 프로비전합니다.
   - 복제가 일시 중지되지 않았는지 확인합니다. 일시 중지된 경우 HRL 파일에 변경 내용이 계속 써지므로 파일 크기가 커질 수 있습니다.
 

## <a name="critical-replication-state-issues"></a>심각한 복제 상태 문제

1. 복제 상태를 확인하려면 온-프레미스 Hyper-V 관리자 콘솔에 연결하고 VM을 선택한 후 상태를 확인합니다.

    ![복제 상태](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. **복제 상태 보기**를 클릭하여 세부 정보를 확인합니다.

    - 복제가 일시 중지되면 VM > **복제** > **복제 다시 시작**을 마우스 오른쪽 단추로 클릭합니다.
    - Site Recovery에 구성된 Hyper-V 호스트의 VM을 동일한 클러스터의 다른 Hyper-V 호스트 또는 독립 실행형 컴퓨터로 마이그레이션하면 해당 VM에 대한 복제는 영향을 받지 않습니다. 새 Hyper-V 호스트가 모든 필수 구성 요구를 충족하고 Site Recovery에 구성되었는지 확인하기만 하면 됩니다.

## <a name="app-consistent-snapshot-issues"></a>앱 일치 스냅숏 문제

앱 일치 스냅숏은 VM 내 애플리케이션 데이터의 지정 시간 스냅숏입니다. VSS(볼륨 섀도 복사본 서비스)는 스냅숏을 만들 때 VM의 앱이 일관된 상태가 되도록 합니다.  이 섹션에서는 발생할 수 있는 몇 가지 일반적인 문제에 대해 자세히 설명합니다.

### <a name="vss-failing-inside-the-vm"></a>VM 내부의 VSS 실패

1. 최신 버전의 Integration Services가 설치되고 실행 중인지 확인합니다.  Hyper-V 호스트의 관리자 권한 PowerShell 프롬프트에서 **get-vm  | select Name, State, IntegrationServicesState** 명령을 실행하여 업데이트를 사용할 수 있는지 확인합니다.
2. VSS 서비스가 실행되고 있으며 정상 상태인지 확인합니다.
   - 서비스를 확인하려면 게스트 VM에 로그인합니다. 그런 다음, 관리자 명령 프롬프트를 열고 다음 명령을 실행하여 모든 VSS 기록기가 정상 상태인지 확인합니다.
       - **Vssadmin list writers**
       - **Vssadmin list shadows**
       - **Vssadmin list providers**
   - 출력을 확인합니다. 기록기가 실패 상태인 경우 다음을 수행합니다.
       - VM의 애플리케이션 이벤트 로그에서 VSS 작업 오류를 확인합니다.
   - 실패한 기록기와 관련된 다음 서비스를 다시 시작합니다.
     - 볼륨 섀도 복사본
       - Azure Site Recovery VSS 공급자
   - 이렇게 한 후에 몇 시간 정도 기다렸다가 앱 일치 스냅숏이 제대로 생성되는지 확인합니다.
   - 최후의 수단으로 VM을 다시 부팅합니다. 이렇게 하면 응답하지 않는 상태의 서비스가 해결될 수 있습니다.
3. VM에 동적 디스크가 없는지 확인합니다. 이러한 디스크는 앱 일치 스냅숏에 지원되지 않습니다. 디스크 관리(diskmgmt.msc)에서 확인할 수 있습니다.

    ![동적 디스크](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. VM에 연결된 iSCSI 디스크가 없는지 확인합니다. 지원되지 않습니다.
5. Backup 서비스가 사용되도록 설정되어 있는지 확인합니다. **Hyper-V 설정** > **Integration Services**에서 사용하도록 설정되어 있는지 확인합니다.
6. VSS 스냅숏을 만드는 앱과 충돌하지 않는지 확인합니다. 여러 앱이 VSS 스냅숏을 동시에 만들려고 하면 충돌이 발생할 수 있습니다. 예를 들어, 복제 정책에 따라 Site Recovery가 스냅숏을 만들도록 예약되어 있을 때 Backup 앱이 VSS 스냅숏을 만드는 경우가 여기에 해당합니다.   
7. VM에서 높은 변동률이 발생하는지 확인합니다.
    - Hyper-V 호스트에서 성능 카운터를 사용하여 게스트 VM에 대한 일일 데이터 변경률을 측정할 수 있습니다. 데이터 변경률을 측정하려면 다음 카운터를 사용하도록 설정합니다. 5-15분 동안 전체 VM 디스크에서 이 값의 샘플을 집계하여 VM 변동 정보를 파악합니다.
        - 범주: “Hyper-V 가상 스토리지 디바이스”
        - 카운터: “쓰기 바이트/초”</br>
        - 이 데이터 변동률은 VM 및 해당 앱의 작업량에 따라 증가하거나 높은 수준을 유지합니다.
        - 평균 원본 디스크 데이터 변동은 Site Recovery용 표준 저장소에 대해 2MB/s입니다. [자세히 알아보기](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - 또한 [저장소 확장성 목표를 확인](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)할 수 있습니다.
8. [Deployment Planner](hyper-v-deployment-planner-run.md)를 실행합니다.
9. [네트워크](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) 및 [저장소](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input)에 대한 권장 사항을 검토합니다.


### <a name="vss-failing-inside-the-hyper-v-host"></a>Hyper-V 호스트 내부의 VSS 실패

1. 이벤트 로그에서 VSS 오류 및 권장 사항을 확인합니다.
    - Hyper-V 호스트 서버의 **이벤트 뷰어** > **Applications and Services Logs** > **Microsoft** > **Windows** > **Hyper-V** > **Admin**에서 Hyper-V Admin 이벤트 로그를 엽니다.
    - 앱 일치 스냅숏 실패를 나타내는 이벤트가 있는지 여부를 확인합니다.
    - 일반적인 오류는 다음과 같습니다. “Hyper-V가 가상 머신 ‘XYZ’에 대한 VSS 스냅숏 세트를 생성하지 못했습니다. 작성자에게 일시적인 오류가 발생했습니다. VSS 서비스가 응답하지 않을 때 서비스를 다시 시작하면 문제가 해결될 수 있습니다."

2. VM에 대한 VSS 스냅숏을 생성하려면 VM에서 Hyper-V Integration Services가 설치되어 있는지와 Backup(VSS) Integration Services가 사용되도록 설정되어 있는지 확인합니다.
    - Integration Services VSS 서비스/디먼이 게스트에서 실행되고 있는지와 **정상** 상태인지 확인합니다.
    - Hyper-V 호스트의 관리자 권한 PowerShell 세션에서 **et-VMIntegrationService -VMName<VMName>-Name VSS** 명령을 사용하여 이 내용을 확인할 수 있습니다. 게스트 VM에 로그인하여 이 정보를 가져올 수도 있습니다. [자세히 알아보기](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - VM에서 Backup/VSS Integration Services가 실행되고 있는지와 정상 상태인지 확인합니다. 그렇지 않은 경우, 이러한 서비스와 Hyper-V 호스트 서버의 Hyper-V 볼륨 섀도 복사본 요청자 서비스를 다시 시작합니다.

### <a name="common-errors"></a>일반 오류

**오류 코드** | **메시지** | **세부 정보**
--- | --- | ---
**0x800700EA** | “Hyper-V가 가상 머신에 대한 VSS 스냅숏 세트를 생성하지 못했습니다. 추가 데이터를 사용할 수 있습니다. (0x800700EA). 백업 작업이 진행 중인 경우 VSS 스냅숏 집합 생성이 실패할 수 있습니다.<br/><br/> 가상 머신에 대한 복제 작업이 실패했습니다. 추가 데이터를 사용할 수 있습니다.” | VM의 동적 디스크가 사용되도록 설정되어 있는지 확인합니다. 지원되지 않습니다.
**0x80070032** | Hyper-V 볼륨 섀도 복사본 요청자가 해당 버전이 Hyper-V에서 요구하는 버전과 일치하지 않으므로 가상 머신 <./VMname>에 연결하지 못했습니다. | 최신 Windows 업데이트가 설치되어 있는지 확인합니다.<br/><br/> 최신 버전의 Integration Services로 [업그레이드](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date)합니다.



## <a name="collect-replication-logs"></a>복제 로그 수집

모든 Hyper-V 복제 이벤트는 **Applications and Services Logs** > **Microsoft** > **Windows**에 있는 Hyper-V-VMMS\Admin 로그에 기록됩니다. 또한 다음과 같이 Hyper-V Virtual Machine 관리 서비스에 대한 분석 로그를 사용할 수 있습니다.

1. 이벤트 뷰어에서 분석 및 디버그 로그가 표시되도록 합니다. 로그를 사용할 수 있도록 하려면 이벤트 뷰어에서 **보기** > **분석 및 디버그 로그 표시**를 클릭합니다. 분석 로그가 **Hyper-V-VMMS** 아래에 표시됩니다.
2. **작업** 창에서 **로그 사용**을 클릭합니다. 

    ![로그 사용](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. 로그가 사용되도록 설정되면 **성능 모니터**에서 **데이터 수집기 집합** 아래에 **이벤트 추적 세션**으로 나타납니다. 
4. 수집된 정보를 보려면 로그를 사용하지 않도록 설정하여 추적 세션을 중지합니다. 그런 후 로그를 저장하고 다시 이벤트 뷰어에서 열거나 다른 도구를 사용하여 필요에 따라 변환합니다.


### <a name="event-log-locations"></a>이벤트 로그 위치

**이벤트 로그** | **세부 정보** |
--- | ---
**Applications and Service Logs/Microsoft/VirtualMachineManager/Server/Admin**(VMM 서버) | VMM 관련 문제를 해결하기 위한 로그입니다.
**애플리케이션 및 서비스 로그/MicrosoftAzureRecoveryServices/Replication**(Hyper-V 호스트) | Microsoft Azure Recovery Services 에이전트 관련 문제를 해결하기 위한 로그입니다. 
**애플리케이션 및 서비스 로그/Microsoft/Azure Site Recovery/Provider/Operational**(Hyper-V 호스트)| Microsoft Azure Site Recovery Services 관련 문제를 해결하기 위한 로그입니다.
**애플리케이션 및 서비스 로그/Microsoft/Windows/Hyper-V-VMMS/Admin**(Hyper-V 호스트) | Hyper-V VM 관리 문제를 해결하기 위한 로그입니다.

### <a name="log-collection-for-advanced-troubleshooting"></a>고급 문제 해결을 위해 로그 수집

다음 도구는 고급 문제 해결에 유용할 수 있습니다.

-   VMM의 경우 [SDP(Support Diagnostics Platform) 도구](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx)를 사용하여 Site Recovery 로그 수집을 수행합니다.
-   VMM이 없는 Hyper-V의 경우 [이 도구를 다운로드](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)하고 Hyper-V 호스트에서 실행하여 로그를 수집합니다.

