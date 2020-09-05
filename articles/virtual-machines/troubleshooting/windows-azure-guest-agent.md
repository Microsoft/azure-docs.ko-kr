---
title: Microsoft Azure 게스트 에이전트 문제 해결
description: Microsoft Azure 게스트 에이전트가 작동 하지 않는 문제 해결
services: virtual-machines-windows
ms.service: virtual-machines-windows
author: dkdiksha
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/07/2020
ms.author: genli
ms.openlocfilehash: 38bf652ccefe98c2bd0fb28e8018d41df21651dc
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88659469"
---
# <a name="troubleshooting-windows-azure-guest-agent"></a>Microsoft Azure 게스트 에이전트 문제 해결

Windows Azure 게스트 에이전트는 VM (가상 컴퓨터) 에이전트입니다. IP 주소 168.63.129.16에서 VM이 패브릭 컨트롤러 (VM이 호스트 되는 기본 실제 서버)와 통신할 수 있습니다. 이는 통신을 용이 하 게 하는 가상 공용 IP 주소입니다. 자세한 내용은 [IP 주소 168.63.129.16?](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16)을 참조 하세요.

 온-프레미스에서 Azure로 마이그레이션하거나 사용자 지정 이미지를 사용 하 여 만든 VM에는 Microsoft Azure 게스트 에이전트가 설치 되어 있지 않습니다. 이러한 시나리오에서는 VM 에이전트를 수동으로 설치 해야 합니다. VM 에이전트를 설치 하는 방법에 대 한 자세한 내용은 [Azure Virtual Machine 에이전트 개요](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)를 참조 하세요.

Windows Azure 게스트 에이전트를 성공적으로 설치한 후에는 VM의 services.msc에 나열 된 다음 서비스를 확인할 수 있습니다.
 
- Windows Azure 게스트 에이전트 서비스
- 원격 분석 서비스
- RD 에이전트 서비스

**Windows Azure 게스트 에이전트 서비스**:이 서비스는 WAppAgent의 모든 로깅을 담당 하는 서비스입니다. 이 서비스는 게스트에서 호스트로의 다양 한 확장 및 통신을 구성 하는 일을 담당 합니다. 

**원격 분석 서비스**:이 서비스는 VM의 원격 분석 데이터를 백 엔드 서버에 전송 하는 작업을 담당 합니다.

**RD 에이전트 서비스**:이 서비스는 게스트 에이전트의 설치를 담당 합니다. 또한 투명 설치 관리자는 다른 구성 요소와 게스트 에이전트의 서비스를 업그레이드 하는 데 도움이 되는 Rd 에이전트의 구성 요소입니다. RDAgent는 게스트 VM의 하트 비트를 물리적 서버의 호스트 에이전트로 보내는 작업도 담당 합니다.

> [!NOTE]
> VM 게스트 에이전트의 버전 2.7.41491.971부터 원격 분석 구성 요소가 RDAgent 서비스에 포함 되어 있으므로 새로 만든 Vm에이 원격 분석 서비스가 표시 되지 않을 수 있습니다.

## <a name="checking-agent-status-and-version"></a>에이전트 상태 및 버전 확인

Azure Portal에서 VM 속성 페이지로 이동 하 여 **에이전트 상태**를 확인 합니다. Windows Azure 게스트 에이전트가 제대로 작동 하는 경우 상태에 **준비**됨이 표시 됩니다. VM 에이전트가 **준비 되지 않음** 상태 이면 Azure Portal의 확장과 **실행 명령이** 작동 하지 않습니다.

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>준비 안 됨 상태의 VM 에이전트 문제 해결

### <a name="step-1-check-whether-the-windows-azure-guest-agent-service-is-installed"></a>1 단계 Windows Azure 게스트 에이전트 서비스가 설치 되어 있는지 확인

- 패키지 확인

    C:\WindowsAzure 폴더를 찾습니다. 버전 번호를 표시 하는 GuestAgent 폴더가 표시 되 면 Windows Azure 게스트 에이전트가 VM에 설치 된 것입니다. 설치 된 패키지를 찾을 수도 있습니다.  Windows Azure 게스트 에이전트가 VM에 설치 되어 있는 경우 패키지는 다음 위치에 저장 됩니다 `C:\windows\OEM\GuestAgent\VMAgentPackage.zip` .
    
    다음 PowerShell 명령을 실행 하 여 vm 에이전트가 VM에 배포 되었는지 여부를 확인할 수 있습니다.
    
    `Get-Az VM -ResourceGroup “RGNAME” – Name “VMNAME” -displayhint expand`
    
    출력에서 **ProvisionVMAgent** 속성을 찾고 값이 **True**로 설정 되어 있는지 확인 합니다. 이 경우 에이전트가 VM에 설치 되어 있음을 의미 합니다.
    
- 서비스 및 프로세스 확인

   서비스 콘솔 (services.msc)로 이동 하 여 Windows Azure 게스트 에이전트 서비스, RDAgent 서비스, Windows Azure 원격 분석 서비스, Windows Azure 네트워크 에이전트 서비스 등의 서비스 상태를 확인 합니다.
 
    작업 관리자에서 다음 프로세스를 검사 하 여 이러한 서비스가 실행 중인지 확인할 수도 있습니다.
       
    - WindowsAzureGuestAgent.exe: Windows Azure 게스트 에이전트 서비스
    - WaAppAgent.exe: RDAgent 서비스
    - WindowsAzureNetAgent.exe: Windows Azure 네트워크 에이전트 서비스
    - WindowsAzureTelemetryService.exe: Windows Azure 원격 분석 서비스

   이러한 프로세스 및 서비스를 찾을 수 없는 경우 Windows Azure 게스트 에이전트가 설치 되어 있지 않음을 나타냅니다.

- 프로그램 및 기능 확인

    제어판에서 **프로그램 및 기능** 으로 이동 하 여 Windows Azure 게스트 에이전트 서비스가 설치 되어 있는지 확인 합니다.

실행 중인 패키지, 서비스 및 프로세스를 찾을 수 없고 프로그램 및 기능에 설치 된 Windows Azure 게스트 에이전트도 표시 되지 않는 경우에는 [Microsoft Azure 게스트 에이전트 서비스를 설치](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)해 보십시오. 게스트 에이전트가 제대로 설치 되지 않으면 [VM 에이전트를 오프 라인으로 설치할](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)수 있습니다.

서비스를 볼 수 있고 실행 중인 경우에는 문제가 해결 되었는지 확인 하는 서비스를 다시 시작 합니다. 서비스가 중지 된 경우 서비스를 시작 하 고 몇 분 정도 기다립니다. 그런 다음 **에이전트 상태가** **준비**로 보고 되는지 확인 합니다. 이러한 서비스가 충돌 하는 경우 일부 타사 프로세스로 인해 이러한 서비스가 충돌 하는 것일 수 있습니다. 이러한 문제를 추가로 해결 하려면 [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에 문의 하세요.

### <a name="step-2-check-whether-auto-update-is-working"></a>2 단계 자동 업데이트가 작동 하는지 확인

Windows Azure 게스트 에이전트에는 자동 업데이트 기능이 있습니다. 그러면 자동으로 새 업데이트를 확인 하 고 설치 합니다. 자동 업데이트 기능이 제대로 작동 하지 않는 경우 다음 단계를 사용 하 여 Windows Azure 게스트 에이전트를 제거 하 고 설치 해 봅니다.

1. Windows Azure 게스트 에이전트가 **프로그램 및 기능**에 표시 되는 경우 Windows Azure 게스트 에이전트를 제거 합니다.

2. 관리자 권한이 있는 명령 프롬프트 창을 엽니다.

3. 게스트 에이전트 서비스를 중지 합니다. 서비스가 중지 되지 않는 경우 서비스를 **수동으로 시작** 으로 설정 하 고 VM을 다시 시작 해야 합니다.
    ```
    net stop rdagent
    net stop WindowsAzureGuestAgent
    net stop WindowsAzureTelemetryService
    ```
1. 게스트 에이전트 서비스를 삭제 합니다.
    ```
    sc delete rdagent
    sc delete WindowsAzureGuestAgent
    sc delete WindowsAzureTelemetryService
    ```
1. 아래에서 `C:\WindowsAzure` 이름이 OLD 인 폴더를 만듭니다.

1. 패키지 또는 GuestAgent로 이름이 지정 된 폴더를 이전 폴더로 이동 합니다.

1. [여기](https://go.microsoft.com/fwlink/?linkid=394789&clcid=0x409)에서 최신 버전의 에이전트 설치 패키지를 다운로드 하 여 설치 합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다.

1. 다음 명령을 사용 하 여 게스트 에이전트를 설치 합니다.

    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log
    ```
    그런 다음 게스트 에이전트 서비스가 올바르게 시작 되는지 확인 합니다.
 
    드문 경우 지만 게스트 에이전트가 올바르게 설치 되지 않으면 [VM 에이전트를 오프 라인으로 설치할](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)수 있습니다.
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>3 단계 VM이 패브릭 컨트롤러에 연결할 수 있는지 확인 합니다.

PsPing와 같은 도구를 사용 하 여 VM이 80, 32526 및 443 포트의 168.63.129.16에 연결할 수 있는지 여부를 테스트 합니다. VM이 정상적으로 연결 되지 않으면 VM의 로컬 방화벽에서 포트 80, 443 및 32526을 통한 아웃 바운드 통신이 열려 있는지 확인 합니다. 이 IP 주소가 차단 되는 경우 VM 에이전트는 다양 한 시나리오에서 예기치 않은 동작을 표시할 수 있습니다.

## <a name="advanced-troubleshooting"></a>고급 문제 해결

Windows Azure 게스트 에이전트 문제 해결에 대 한 이벤트는 다음 로그 파일에 기록 됩니다.

- C:\WindowsAzure\Logs\WaAppAgent.log
- C:\WindowsAzure\Logs\TransparentInstaller.log

Windows Azure 게스트 에이전트가 **준비 되지 않음** 상태를 입력 하거나 예상 대로 작동을 중지할 수 있는 몇 가지 일반적인 시나리오는 다음과 같습니다.

### <a name="agent-stuck-on-starting"></a>에이전트가 "시작 중"에 중단 되었습니다.

Waappagent.exe 로그에서 에이전트가 시작 프로세스에서 중단 된 것을 확인할 수 있으며이를 시작할 수 없습니다.

**로그 정보**
 
[00000007] [05/28/2019 12:58:50.90] [INFO] WindowsAzureGuestAgent를 시작 합니다. 버전 2.7.41491.901

**분석**
 
VM에서 계속 이전 버전의 Windows Azure 게스트 에이전트를 실행 하 고 있습니다. C:\WindowsAzure 폴더에 여러 개의 동일한 버전을 포함 하 여 여러 Windows Azure 게스트 에이전트 인스턴스가 설치 된 것을 확인할 수 있습니다. 에이전트 인스턴스가 여러 개 설치 되어 있기 때문에 VM은 최신 버전의 Windows Azure 게스트 에이전트를 시작 하지 않습니다.

**해결 방법**

Microsoft Azure 게스트 에이전트를 수동으로 제거한 후 다음 단계를 수행 하 여 다시 설치 합니다.

1. 프로그램 및 기능 > 제어판을 열고 Windows Azure 게스트 에이전트를 제거 합니다.
1. 작업 관리자를 열고 Windows Azure 게스트 에이전트 서비스, RDAgent 서비스, Windows Azure 원격 분석 서비스 및 Microsoft Azure 네트워크 에이전트 서비스와 같은 서비스를 중지 합니다.
1. C:\WindowsAzure 아래에서 이름이 OLD 인 폴더를 만듭니다.
1. 패키지 또는 GuestAgent로 이름이 지정 된 폴더를 이전 폴더로 이동 합니다. 또한 xxxxx GuestAgent_x로 시작 하는 C:\WindowsAzure\logs의 GuestAgent 폴더를 이전 폴더로 이동 합니다.
1. 최신 버전의 MSI 에이전트를 다운로드 하 여 설치 합니다. 설치를 완료 하려면 관리자 권한이 있어야 합니다.
1. 다음 MSI 명령을 사용 하 여 게스트 에이전트를 설치 합니다.
    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log`
    ```
1. RDAgent, Windows Azure 게스트 에이전트 및 Windows Azure 원격 분석 서비스가 현재 실행 중인지 확인 합니다.
 
1. Waappagent.exe에서 최신 버전의 Windows Azure 게스트 에이전트가 실행 되 고 있는지 확인 합니다.
 
1. C:\WindowsAzure.에서 이전 폴더를 삭제 합니다.
  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>WireServer IP (호스트 IP)에 연결할 수 없습니다. 

Waappagent.exe 및 원격 분석에서 다음 오류 항목을 확인할 수 있습니다.

**로그 정보**

```Log sample
[ERROR] GetVersions() failed with exception: Microsoft.ServiceModel.Web.WebProtocolException: Server Error: Service Unavailable (ServiceUnavailable) ---> 
System.Net.WebException: The remote server returned an error: (503) Server Unavailable.
```
```Log sample
[00000011] [12/11/2018 06:27:55.66] [WARN]  (Ignoring) Exception while fetching supported versions from HostGAPlugin: System.Net.WebException: Unable to connect to the remote server 
---> System.Net.Sockets.SocketException: An attempt was made to access a socket in a way forbidden by its access permissions 168.63.129.16:32526
at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
at System.Net.ServicePoint.ConnectSocketInternal(Boolean connectFailure, Socket s4, Socket s6, Socket& socket, IPAddress& address, ConnectSocketState status, IAsyncResult asyncResult, Exception& exception)
--- End of inner exception stack trace ---
at System.Net.WebClient.DownloadDataInternal(Uri address, WebRequest& request)
at System.Net.WebClient.DownloadString(Uri address)
at Microsoft.GuestAgentHostPlugin.Client.GuestInformationServiceClient.GetVersions()
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.HostGAPluginUtility.UpdateHostGAPluginAvailability()`
```
**분석**

VM이 wireserver 호스트 서버에 연결할 수 없습니다.

**해결 방법**

1. Wireserver에 연결할 수 없기 때문에 원격 데스크톱을 사용 하 여 VM에 연결한 다음 인터넷 브라우저에서 다음 URL에 액세스를 시도 합니다. http://168.63.129.16/?comp=versions 
1. 1 단계의 URL에 연결할 수 없는 경우 네트워크 인터페이스에서 DHCP를 사용 하도록 설정 되어 있고 DNS가 있는지 여부를 확인 합니다. 네트워크 인터페이스의 DHCP 상태를 확인 하려면 명령을 실행  `netsh interface ip show config` 합니다.
1. DHCP를 사용 하지 않도록 설정한 경우에는 다음을 실행 하 여 노란색의 값을 인터페이스 이름으로 변경 해야 `netsh interface ip set address name="Name of the interface" source=dhcp` 합니다.
1. 방화벽, 프록시 또는 IP 주소 168.63.129.16에 대 한 액세스를 차단할 수 있는 다른 원본으로 인해 발생할 수 있는 문제를 확인 합니다.
1. Windows 방화벽 또는 타사 방화벽에서 포트 80, 443 및 32526에 대 한 액세스를 차단 하는지 확인 합니다. 이 주소를 차단 하지 않아야 하는 이유에 대 한 자세한 내용은 [IP 주소 168.63.129.16?](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16)을 참조 하세요.

### <a name="guest-agent-is-stuck-stopping"></a>게스트 에이전트가 "중지 중" 상태로 중지 됨  

Waappagent.exe에서 다음 오류 항목을 확인할 수 있습니다.

**로그 정보** 

```Log sample
[00000007] [07/18/2019 14:46:28.87] [WARN] WindowsAzureGuestAgent stopping.
[00000007] [07/18/2019 14:46:28.89] [INFO] Uninitializing StateExecutor with WaitForTerminalStateReachedOnEnd : True
[00000004] [07/18/2019 14:46:28.89] [WARN] WindowsAzureGuestAgent could not be stopped. Exception: System.NullReferenceException: Object reference not set to an instance of an object.
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.WaitForExtensionWorkflowComplete(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorForCloud.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentCore.Stop(Boolean waitForTerminalState)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.DoStopService()
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.<>c__DisplayClass2.<OnStopProcessing>b__1()
```
**분석**

Windows Azure 게스트 에이전트가 중지 프로세스에서 중단 되었습니다.

**해결 방법**

1. VM에서 WaAppAgent.exe 실행 되 고 있는지 확인 합니다. 실행 되 고 있지 않으면 rdgagent 서비스를 다시 시작 하 고 5 분을 기다립니다. WaAppAgent.exe를 실행 하는 경우 WindowsAzureGuest.exe 프로세스를 종료 합니다.
2. 1 단계를 수행 해도 문제가 해결 되지 않으면 현재 설치 된 버전을 제거 하 고 에이전트의 최신 버전을 수동으로 설치 합니다.

### <a name="npcap-loopback-adapter"></a>Npcap 루프백 어댑터 

Waappagent.exe에서 다음 오류 항목을 확인할 수 있습니다.
 
```Log sample
[00000006] [06/20/2019 07:44:28.93] [INFO]  Attempting to discover fabric address on interface Npcap Loopback Adapter.
[00000024] [06/20/2019 07:44:28.93] [WARN]  Empty DHCP option data returned
[00000006] [06/20/2019 07:44:28.93] [ERROR] Did not discover fabric address on interface Npcap Loopback Adapter
```
**분석**

Npcap 루프백 어댑터는 VM에 Wireshark에 의해 설치 됩니다. Wireshark는 네트워크 트래픽을 프로 파일링 하 고 패킷을 분석 하기 위한 오픈 소스 도구입니다. 이러한 도구를 종종 네트워크 분석기, 네트워크 프로토콜 분석기 또는 탐지기 라고 합니다.

**해결 방법**

Npcap 루프백 어댑터는 WireShark에 의해 설치 될 수 있습니다. 사용 하지 않도록 설정 하 고 문제가 해결 되었는지 확인 하십시오.

## <a name="next-steps"></a>다음 단계

"Windows Azure 게스트 에이전트가 작동 하지 않습니다." 문제를 추가로 해결 하려면 [Microsoft 지원에 문의 하세요](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).