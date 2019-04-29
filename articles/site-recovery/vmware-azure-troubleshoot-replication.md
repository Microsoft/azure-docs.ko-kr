---
title: Azure Site Recovery를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구에 대한 복제 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구 중에 일반적인 복제 문제를 해결하는 방법을 제공합니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/14/2019
ms.author: mayg
ms.openlocfilehash: 1aaf13f01c7e7197001f3099fabd4b8be8545f0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565061"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware VM 및 실제 서버에 대한 복제 문제 해결

Azure Site Recovery를 사용하여 VMware 가상 머신 또는 물리적 서버를 보호하는 경우 특정 오류 메시지가 나타날 수 있습니다. 이 문서에서는 [Site Recovery](site-recovery-overview.md)를 사용하여 온-프레미스 VMware VM 및 실제 서버를 Azure에 복제하는 경우 발생할 수 있는 몇 가지 일반적인 문제를 설명합니다.

## <a name="monitor-process-server-health-to-avoid-replication-issues"></a>복제 문제 방지를 위해 프로세스 서버 상태 모니터링

포털에서 PS(프로세스 서버) 상태를 모니터링하여 연결된 원본 머신에 대한 복제가 진행 중인지 확인하는 것이 좋습니다. 자격 증명 모음에서 관리 > Site Recovery 인프라 > 구성 서버로 이동합니다. 구성 서버 블레이드에서 연결된 서버 아래의 프로세스 서버를 클릭합니다. 상태 통계가 포함된 프로세스 서버 블레이드가 열립니다. 복제, 인증서 만료 날짜 및 사용 가능한 여유 공간에 필요한 PS 서비스의 CPU 활용률, 메모리 사용량, 상태를 추적할 수 있습니다. 모든 통계의 상태가 녹색이어야 합니다. 

**메모리 및 CPU 사용량을 70% 미만으로, 사용 가능한 공간을 25% 이상으로 유지하는 것이 좋습니다**. 사용 가능한 공간은 프로세스 서버의 캐시 디스크 공간을 가리키며, Azure로 업로드하기 전에 원본 머신의 복제 데이터를 저장하는 데 사용됩니다. 20% 미만으로 감소하면 모든 연결된 원본 머신에 대한 복제가 제한됩니다. [용량 지침](./site-recovery-plan-capacity-vmware.md#capacity-considerations)에 따라 원본 머신을 복제하는 데 필요한 구성을 파악합니다.

PS 머신에서 다음 서비스가 실행 중인지 확인합니다. 실행되지 않고 있는 모든 서비스를 시작하거나 다시 시작합니다.

**기본 제공 프로세스 서버**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* Log Upload 서비스(LogUpload)
* InMage Scout Application 서비스
* Microsoft Azure Recovery Services 에이전트(obengine)
* InMage Scout VX Agent - Sentinel/Outpost(svagents)
* tmansvc
* World Wide Web Publishing 서비스(W3SVC)
* MySQL
* Microsoft Azure Site Recovery 서비스(dra)

**스케일 아웃 프로세스 서버**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* Log Upload 서비스(LogUpload)
* InMage Scout Application 서비스
* Microsoft Azure Recovery Services 에이전트(obengine)
* InMage Scout VX Agent - Sentinel/Outpost(svagents)
* tmansvc

**장애 복구(failback)를 위한 Azure의 프로세스 서버**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* Log Upload 서비스(LogUpload)

모든 서비스의 StartType이 **자동 또는 자동(지연된 시작)** 으로 설정되었는지 확인합니다. Microsoft Azure Recovery Services 에이전트(obengine) 서비스에는 위와 같은 StartType 설정이 필요하지 않습니다.

## <a name="replication-issues"></a>복제 문제

종종 초기 및 지속적인 복제 실패 원본 서버와 프로세스 서버 간 또는 프로세스 서버와 Azure 간의 연결 문제로 인해 발생 합니다. 대부분의 경우 다음 섹션의 단계를 완료하여 이러한 문제를 해결할 수 있습니다.

>[!Note]
>다음 사항을 확인합니다.
>1. 시스템 보호 된 항목에 대 한 날짜 시간이 동기화 되어 있습니다.
>2. 바이러스 백신 소프트웨어 없음은 Azure Site Recovery를 차단 합니다. 에 대해 알아봅니다 [자세한](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) Azure Site Recovery에 필요한 폴더 제외 합니다.

### <a name="check-the-source-machine-for-connectivity-issues"></a>연결 문제에 대 한 원본 컴퓨터를 확인 합니다.

다음 목록 표시 방법으로 원본 컴퓨터를 확인할 수 있습니다.

*  원본 서버의 명령줄에서 아래의 명령을 실행하여 텔넷으로 HTTPS 포트를 통해 프로세스 서버를 ping합니다. 프로세스 서버는 HTTPS 포트 9443을 기본적으로 사용하여 복제 트래픽을 주고받습니다. 등록 시 이 포트를 수정할 수 있습니다. 다음 명령은 네트워크 연결 문제와 방화벽 포트 차단 문제가 있는지 확인합니다.


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > Telnet을 사용하여 연결을 테스트합니다. `ping`은 사용하지 않도록 합니다. 텔넷을 설치하지 않은 경우 [텔넷 클라이언트 설치](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)에 나열된 단계를 완료합니다.

   텔넷이 PS 포트에 성공적으로 연결할 수 있는 경우 빈 화면이 표시됩니다.

   프로세스 서버에 연결할 수 없는 경우 프로세스 서버에서 인바운드 포트 9443을 허용합니다. 예를 들어, 네트워크에 경계 네트워크 또는 스크린된 서브넷이 있는 경우 프로세스 서버에서 인바운드 포트 9443을 허용해야 할 수 있습니다. 그런 다음, 문제가 여전히 발생하는지 확인합니다.

*  텔넷이 연결되고 원본 머신이 PS에 연결할 수 없다고 보고하는 경우 원본 머신에서 웹 브라우저를 열고 https://<PS_IP>: <PS_Data_Port>/ 주소에 연결할 수 있는지 확인합니다.

    이 주소에 도달하면 HTTPS 인증서 오류가 발생합니다. 인증서 오류를 무시하고 계속하면 400 - 잘못된 요청이 발생합니다. 이는 서버가 브라우저의 요청을 처리할 수 없으며 서버에 대한 표준 HTTPS 연결이 제대로 작동하고 정상 상태임을 의미합니다.

    실패하면 브라우저의 오류 메시지에 대한 세부 정보가 지침을 제공합니다. 예를 들어 프록시 인증이 잘못된 경우 프록시 서버에서 오류 메시지에 필요한 작업과 함께 407 - 프록시 인증을 반환합니다. 

*  네트워크 업로드 실패와 관련된 오류는 원본 VM에서 다음 로그를 참조하세요.

       C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

### <a name="check-the-process-server-for-connectivity-issues"></a>연결 문제에 대 한 프로세스 서버를 확인 합니다.

다음 목록은 프로세스 서버를 확인하는 방법입니다.

> [!NOTE]
> 프로세스 서버에 고정 IPv4 주소가 있어야 하며 NAT IP가 구성되어 있으면 안 됩니다.

* **원본 머신과 프로세스 서버 간의 연결 확인**
* 원본 머신에서 텔넷에 연결할 수 있지만 원본에서 PS에 연결할 수 없는 경우 원본 VM에서 cxpsclient 도구를 실행하여 원본 VM의 cxprocessserver와 엔드투엔드 연결을 확인합니다.

      <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>

   해당 오류에 대한 자세한 내용은 다음 디렉터리에서 PS에 대해 생성된 로그를 참조하세요.

      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err
      and
      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer
* PS.에서 하트 비트가 없습니다 경우 PS에서 다음 로그를 확인 합니다. 으로 식별 되는이 **오류 코드 806** 포털에 있습니다.

      C:\ProgramData\ASR\home\svsystems\eventmanager*.log
      and
      C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

* **프로세스 서버가 데이터를 Azure로 적극적으로 밀어넣는지 확인합니다**.

  1. 프로세스 서버에서 작업 관리자를 엽니다(Ctrl+Shift+Esc 누르기).
  2. **성능** 탭을 선택하고 **리소스 모니터 열기** 링크를 선택합니다. 
  3. **리소스 모니터** 페이지에서 **네트워크** 탭을 선택합니다. **네트워크 활동이 있는 프로세스**에서 **cbengine.exe**가 현재 대용량 데이터를 전송하고 있는지 확인합니다.

       ![네트워크 활동이 있는 프로세스 아래에 볼륨을 표시하는 스크린샷](./media/vmware-azure-troubleshoot-replication/cbengine.png)

  cbengine.exe가 대용량 데이터를 전송하고 있지 않으면 다음 섹션의 단계를 완료합니다.

* **프로세스 서버가 Azure Blob 스토리지에 연결할 수 있는지 여부를 확인합니다**.

  **cbengine.exe**를 선택합니다. **TCP 연결** 아래에서 프로세스 서버로부터 Azure Blob 스토리지 URL로의 연결이 있는지 확인합니다.

  ![cbengine.exe와 Azure Blob 스토리지 URL 간의 연결을 보여 주는 스크린샷](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

  프로세스 서버로부터 Azure Blob 스토리지 URL로의 연결이 없으면 제어판에서 **서비스**를 선택합니다. 다음 서비스가 실행 중인지 여부를 확인합니다.

  *  cxprocessserver
  *  InMage Scout VX Agent – Sentinel/Outpost
  *  Microsoft Azure Recovery Services 에이전트
  *  Microsoft Azure Site Recovery 서비스
  *  tmansvc

  실행되지 않고 있는 모든 서비스를 시작하거나 다시 시작합니다. 문제가 여전히 발생하는지 여부를 확인합니다.

* **프로세스 서버가 포트 443을 사용하여 Azure 공용 IP 주소에 연결할 수 있는지 여부를 확인합니다**.

  %programfiles%\Microsoft Azure Recovery Services Agent\Temp에서 최신 CBEngineCurr.errlog 파일을 엽니다. 파일에서 **443** 또는 문자열 **connection attempt failed**를 검색합니다.

  ![Temp 폴더의 로그 오류를 표시하는 스크린샷](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

  문제가 표시되면 프로세스 서버의 명령줄에서 Telnet을 사용하여 Azure 공용 IP 주소(이전 이미지에서는 IP 주소가 마스킹됨)를 ping합니다. 포트 443을 사용하여 CBEngineCurr.currLog 파일에서 Azure 공용 IP 주소를 찾을 수 있습니다.

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

  연결할 수 없으면 다음 단계에 설명된 대로 액세스 문제가 방화벽 또는 프록시 설정으로 인한 것인지 확인합니다.

* **프로세스 서버의 IP 주소 기반 방화벽이 액세스를 차단하는지 여부를 확인합니다**.

  서버에서 IP 주소 기반 방화벽 규칙을 사용하는 경우 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)의 전체 목록을 다운로드합니다. IP 주소 범위를 방화벽 구성에 추가하여 방화벽이 Azure(및 기본 HTTPS 포트 443)와의 통신을 허용하도록 합니다. 구독하는 Azure 지역과 Azure 미국 서부 지역에 해당하는 IP 주소 범위를 허용합니다(액세스 제어 및 ID 관리에 사용됨).

* **프로세스 서버의 URL 기반 방화벽이 액세스를 차단하는지 여부를 확인합니다**.

  서버에서 URL 기반 방화벽 규칙을 사용하는 경우 다음 표에 나열된 URL을 방화벽 구성에 추가합니다.

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **프로세스 서버의 프록시 설정이 액세스를 차단할지 여부를 확인합니다**.

   프록시 서버를 사용하는 경우 DNS 서버에서 프록시 서버 이름을 확인하는지 확인합니다. 구성 서버를 설정할 때 제공한 값을 확인하려면 레지스트리 키 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**로 이동합니다.

   다음으로, Azure Site Recovery에서 데이터를 보내는 데 동일한 설정을 사용하는지 확인합니다. 
      
   1. **Microsoft Azure Backup**을 검색합니다. 
   2. **Microsoft Azure Backup**을 열고 **작업** > **속성 변경**을 선택합니다. 
   3. **프록시 구성** 탭에 프록시 주소가 표시되어야 합니다. 프록시 주소는 레지스트리 설정에 표시된 프록시 주소와 동일해야 합니다. 그렇지 않은 경우 동일한 주소로 변경하세요.

*  **프로세스 서버에서 스로틀 대역폭이 제한되는지 여부를 확인합니다**.

   대역폭을 늘리고 문제가 여전히 발생하는지 확인합니다.


## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>원본 컴퓨터가 Azure Portal에 나열되지 않음

Site Recovery를 사용하여 복제를 사용하도록 설정할 원본 컴퓨터를 선택하려고 하면 다음과 같은 이유 중 하나로 컴퓨터를 계속 사용하지 못할 수 있습니다.

* **인스턴스 UUID가 같은 두 개의 가상 머신**: vCenter에 인스턴스 UUID가 같은 두 개의 가상 머신이 있는 경우 구성 서버에서 첫 번째로 검색된 가상 머신이 Azure Portal에 표시됩니다. 이 문제를 해결하려면 두 개의 가상 머신에 동일한 인스턴스 UUID가 없는지 확인합니다. 이 시나리오는 백업 VM이 활성화되고 검색 레코드에 기록되는 경우에 일반적으로 발생합니다. [Azure Site Recovery VMware-Azure: 중복되거나 오래된 항목을 정리하는 방법](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)을 참조하세요.
* **잘못된 vCenter 사용자 자격 증명**: OVF 템플릿 또는 통합 설정을 사용하여 구성 서버를 설정하는 동안 올바른 vCenter 자격 증명을 추가했는지 확인합니다. 설치 중에 추가한 자격 증명을 확인하려면 [자동 검색에 대한 자격 증명 수정](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)을 참조하세요.
* **vCenter 권한 부족**: vCenter에 액세스하기 위해 제공된 권한에 필요한 사용 권한이 없으면 가상 머신을 검색하지 못할 수 있습니다. [자동 검색용 계정 준비](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)에 설명된 사용 권한을 vCenter 사용자 계정에 추가해야 합니다.
* **Azure Site Recovery 관리 서버**: 구성 서버/스케일 아웃 프로세스 서버/마스터 대상 서버 역할 중 하나 이상에서 가상 머신이 관리 서버로 사용되는 경우 포털에서 가상 머신을 선택할 수 없습니다. 관리 서버는 복제할 수 없습니다.
* **Azure Site Recovery 서비스를 통해 이미 보호/장애 조치(Failover)됨**: Site Recovery를 통해 가상 머신을 이미 보호 또는 장애 조치하는 경우 포털에서 보호하기 위해 가상 머신을 선택할 수 없습니다. 포털에서 찾고 있는 가상 머신이 다른 사용자 또는 다른 구독을 통해 아직 보호되고 있지 않은지 확인합니다.
* **연결되지 않은 vCenter** VCenter가 연결된 상태인지 확인합니다. 확인하려면 [Recovery Services 가격 증명 모음] > [Site Recovery 인프라] > [구성 서버]로 이동하고 > 해당 구성 서버를 클릭하면 > 연결된 서버의 세부 정보와 함께 블레이드가 오른쪽에 열립니다. VCenter가 연결되었는지 확인합니다. “연결되지 않음” 상태인 경우 문제를 해결한 다음, 포털에서 [구성 서버를 새로 고칩니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.
* **ESXi 전원 꺼짐**: 가상 머신이 상주하는 ESXi 호스트의 전원이 꺼진 상태이면 가상 머신이 Azure Portal에 나열되지 않거나 선택할 수 없습니다. ESXi 호스트 전원을 켜고 포털에서 [구성 서버를 새로 고칩니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.
* **보류 중인 재부팅**: 가상 머신에 보류 중인 재부팅이 있는 경우 Azure Portal에서 머신을 선택할 수 없습니다. 보류 중인 재부팅 활동을 완료하고 [구성 서버를 새로 고쳐야 합니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.
* **IP 찾을 수 없음**: 가상 머신에 연결된 유효한 IP 주소가 없는 경우 Azure Portal에서 머신을 선택할 수 없습니다. 가상 머신에 유효한 IP 주소를 할당하고 [구성 서버를 새로 고칩니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>보호된 가상 머신은 포털에 회색으로 표시됩니다.

Site Recovery에서 복제된 가상 머신은 시스템에 중복된 항목이 있으면 Azure Portal에서 사용할 수 없습니다. 오래된 항목을 삭제하고 이 문제를 해결하는 방법에 대한 자세한 내용은 [Azure Site Recovery VMware-Azure: 중복되거나 오래된 항목을 정리하는 방법](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)을 참조하세요.

## <a name="common-errors-and-recommended-steps-for-resolution"></a>일반적인 오류 및 권장 해결 단계

### <a name="initial-replication-issues-error-78169"></a>초기 복제 문제 [Error 78169]

있는지는 위 확인을 통해 없음 연결, 대역폭이 나 시간이 동기화 관련된 문제, 되어 있는지 확인:

- 바이러스 백신 소프트웨어 없음은 Azure Site Recovery를 차단 합니다. 에 대해 알아봅니다 [자세한](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) Azure Site Recovery에 필요한 폴더 제외 합니다.

### <a name="application-consistency-recovery-point-missing-error-78144"></a>응용 프로그램 일관성 복구 지점이 없습니다. [오류 78144]

 볼륨 섀도 복사본 서비스 (VSS)를 사용 하 여 문제로 인해 발생합니다. 이 문제를 해결하려면: 
 
- Azure Site Recovery 에이전트의 설치 된 버전과 9.22.2 이상 인지 확인 합니다. 
- Windows 서비스에서 서비스로 서 VSS 공급자가 설치 되어 있는지 확인 하 고 또한 Azure Site Recovery VSS 공급자가 표시 되는지 확인 하려면 구성 요소 서비스 MMC를 확인 합니다.
- VSS 공급자 설치 되어 있지 않으면 참조를 [설치 오류 문제 해결 문서](vmware-azure-troubleshoot-push-install.md#vss-installation-failures)합니다.

- VSS를 사용 하지 않도록 설정 하는 경우
    - VSS 공급자 서비스의 시작 유형이 설정 되어 있는지 확인 **자동**합니다.
    - 다음 서비스를 다시 시작 합니다.
        - VSS 서비스
        - Azure Site Recovery VSS 공급자
        - VDS 서비스

### <a name="high-churn-on-source-machine-error-78188"></a>[오류 78188] 원본 컴퓨터에서 높은 변동

가능한 원인:
- 나열 된 디스크가 가상 컴퓨터의 데이터 변경 률 (쓰기 바이트/초)은 보다 [Azure Site Recovery 지원 제한](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) 복제 대상 저장소 계정 유형에 대 한 합니다.
- 변동률에 급증 하는 부분는 많은 양의 데이터 인해 보류 중인 업로드 합니다.

이 문제를 해결하려면
- 소스에서 이탈 비율 요구 사항에 따라 대상 저장소 계정 유형 (Standard 또는 Premium)은 프로 비전 되었는지 확인 합니다.
- 관찰 된 변동을 임시 인 경우 보류 중인 데이터 업로드 캐치 업 하 고 복구 지점을 만들 수는 몇 시간까지 기다립니다.
- 문제가 계속 지속 되는 경우 ASR을 사용 하 여 [배포 플래너](site-recovery-deployment-planner.md#overview) 복제를 계획할 수 있습니다.

### <a name="no-heartbeat-from-source-machine-error-78174"></a>원본 컴퓨터 [Error 78174]에서 하트 비트 없음

원본 컴퓨터에 Azure Site Recovery 모바일 에이전트를 구성 서버 (CS)와 통신 하지 하는 경우 발생 합니다.

문제를 해결 하려면 구성 서버에 원본 VM에서에서 네트워크 연결을 확인 하려면 다음 단계를 사용 합니다.

1. 원본 컴퓨터가 실행 되 고 있는지 확인 합니다.
2. 관리자 권한이 있는 계정을 사용 하 여 원본 컴퓨터에 로그인 합니다.
3. 다음 서비스가 실행 되 고 있는지 확인 하지 서비스를 다시 시작 하는 경우:
   - Svagents (InMage Scout VX Agent)
   - InMage Scout Application 서비스
4. 원본 컴퓨터의 오류 세부 정보에 대 한 위치에 로그를 검사 합니다.

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="no-heartbeat-from-process-server-error-806"></a>[오류 806] 프로세스 서버의 하트 비트 없음
프로세스 서버 (PS)에서 하트 비트가 없습니다 인 경우 다음을 확인 합니다.
1. PS VM이 작동 및 실행
2. 오류 세부 정보에 대 한 PS에서 다음 로그를 확인 합니다.

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="no-heartbeat-from-master-target-error-78022"></a>마스터 대상 [Error 78022]에서 하트 비트 없음

마스터 대상에 Azure Site Recovery 모바일 에이전트 구성 서버와 통신 하지 않는 경우 발생 합니다.

문제를 해결 하려면 서비스 상태를 확인 하려면 다음 단계를 사용 합니다.

1. 마스터 대상 VM 실행 되 고 있는지 확인 합니다.
2. 관리자 권한이 있는 계정을 사용 하 여 마스터 대상 VM에 로그인 합니다.
    - Svagents 서비스가 실행 되 고 있는지 확인 합니다. 실행 중인 서비스를 다시 시작
    - 오류 세부 정보에 대 한 위치에 로그를 확인 합니다.
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

### <a name="process-server-is-not-reachable-from-the-source-machine-error-78186"></a>프로세스 서버가 원본 컴퓨터 [Error 78186]에서 연결 되지 않습니다.

이 오류는 보고 되지 않은 경우 생성 되지 않는 앱 및 크래시 일치 지점에 연결 됩니다. 이 문제를 해결 하려면는 아래 링크를 문제 해결:
1. 되도록 [PS 서비스가 실행 되 고](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [원본 컴퓨터 연결 문제 확인](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [프로세스 서버 간의 연결 문제 확인](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues) 에 대해 제공 된 지침을 따릅니다.
    - 원본 연결 확인 중
    - 방화벽 및 프록시 문제

### <a name="data-upload-blocked-from-source-machine-to-process-server-error-78028"></a>[오류 78028] 프로세스 서버로 원본 컴퓨터에서 차단 되는 데이터 업로드

이 오류는 보고 되지 않은 경우 생성 되지 않는 앱 및 크래시 일치 지점에 연결 됩니다. 이 문제를 해결 하려면는 아래 링크를 문제 해결:

1. 되도록 [PS 서비스가 실행 되 고](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [원본 컴퓨터 연결 문제 확인](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [프로세스 서버 간의 연결 문제 확인](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues) 에 대해 제공 된 지침을 따릅니다.
    - 원본 연결 확인 중
    - 방화벽 및 프록시 문제

## <a name="next-steps"></a>다음 단계

도움이 필요한 경우 [Azure Site Recovery 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 질문을 게시하세요. 활발히 유지되는 커뮤니티가 있으며 엔지니어 중 하나가 도움을 줄 수 있습니다.
