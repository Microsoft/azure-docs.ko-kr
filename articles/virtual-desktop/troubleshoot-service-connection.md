---
title: 서비스 연결 문제 해결 Windows 가상 데스크톱-Azure
description: Windows 가상 데스크톱 테넌트 환경에서 클라이언트 연결을 설정할 때 발생 하는 문제를 해결 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
ms.openlocfilehash: 81ba2b3b5834e06922f694d6eb5338465bfb0ab7
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975462"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows 가상 데스크톱 서비스 연결

이 문서를 사용 하 여 Windows 가상 데스크톱 클라이언트 연결 문제를 해결할 수 있습니다.

## <a name="provide-feedback"></a>피드백 제공하기

사용자 의견을 제공 하 고 windows 가상 [데스크톱 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서 제품 팀 및 기타 활성 커뮤니티 구성원에 게 Windows 가상 데스크톱 서비스에 대해 논의할 수 있습니다.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>사용자가 연결 되어 있지만 아무것도 표시 되지 않습니다 (피드 없음).

사용자가 원격 데스크톱 클라이언트를 시작 하 고 인증할 수 있지만 웹 검색 피드에 아이콘이 표시 되지 않습니다.

다음 명령줄을 사용 하 여 문제를 보고 하는 사용자가 응용 프로그램 그룹에 할당 되었는지 확인 합니다.

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

사용자가 올바른 자격 증명으로 로그인 하 고 있는지 확인 합니다.

웹 클라이언트를 사용 하는 경우 캐시 된 자격 증명 문제가 없는지 확인 합니다.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Windows 10 Enterprise 다중 세션 가상 컴퓨터가 응답 하지 않음

가상 컴퓨터가 응답 하지 않으며 RDP를 통해 액세스할 수 없는 경우 호스트 상태를 확인 하 여 진단 기능으로 문제를 해결 해야 합니다.

호스트 상태를 확인 하려면 다음 cmdlet을 실행 합니다.

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

호스트 상태가 `NoHeartBeat`이면 VM이 응답 하지 않으며 에이전트가 Windows 가상 데스크톱 서비스와 통신할 수 없음을 의미 합니다.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True  Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True  Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
```

NoHeartBeat 비트 상태를 수정 하기 위해 수행할 수 있는 몇 가지 작업이 있습니다.

### <a name="update-fslogix"></a>FSLogix 업데이트

FSLogix가 최신이 아닌 경우, 특히 frxdrvvt의 버전인 2.9.7205.27375 경우 교착 상태가 발생할 수 있습니다. [FSLogix를 최신 버전으로 업데이트](https://go.microsoft.com/fwlink/?linkid=2084562)해야 합니다.

### <a name="disable-bgtaskregistrationmaintenancetask"></a>BgTaskRegistrationMaintenanceTask 사용 안 함

FSLogix 업데이트가 작동 하지 않는 경우 BiSrv 구성 요소가 주간 유지 관리 작업 중에 시스템 리소스를 소모 하는 문제가 발생할 수 있습니다. 다음 두 가지 방법 중 하나로 BgTaskRegistrationMaintenanceTask를 사용 하지 않도록 설정 하 여 유지 관리 작업을 일시적으로 해제 합니다.

- 시작 메뉴로 이동 하 여 **작업 스케줄러**를 검색 합니다. **작업 스케줄러 Library** > **Microsoft** > **Windows** > **BrokerInfrastructure**로 이동 합니다. **BgTaskRegistrationMaintenanceTask**라는 작업을 찾습니다. 찾은 경우 마우스 오른쪽 단추로 클릭 하 고 드롭다운 메뉴에서 **사용 안 함** 을 선택 합니다.
- 관리자 권한으로 명령줄 메뉴를 열고 다음 명령을 실행 합니다.
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 및 에스컬레이션 트랙 문제 해결에 대 한 개요는 [문제 해결 개요, 사용자 의견 및 지원](troubleshoot-set-up-overview.md)을 참조 하세요.
- Windows 가상 데스크톱 환경에서 테넌트 및 호스트 풀을 만드는 동안 발생 하는 문제를 해결 하려면 [테넌트 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조 하세요.
- Windows 가상 데스크톱에서 VM (가상 컴퓨터)을 구성 하는 동안 발생 하는 문제를 해결 하려면 [세션 호스트 가상 컴퓨터 구성](troubleshoot-vm-configuration.md)을 참조 하세요.
- Windows 가상 데스크톱과 함께 PowerShell을 사용할 때 발생 하는 문제를 해결 하려면 [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)을 참조 하세요.
- 문제 해결 자습서를 진행 하려면 [자습서: 템플릿 배포 리소스 관리자 문제 해결](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조 하세요.
