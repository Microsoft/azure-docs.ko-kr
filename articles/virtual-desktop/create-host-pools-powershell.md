---
title: Windows 가상 데스크톱 호스트 풀 만들기 PowerShell-Azure
description: PowerShell cmdlet을 사용 하 여 Windows 가상 데스크톱에서 호스트 풀을 만드는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: f510879e7df967944f5e7a3deac308a430d53d0c
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771311"
---
# <a name="create-a-host-pool-with-powershell"></a>PowerShell을 사용한 호스트 풀 만들기

호스트 풀은 Windows Virtual Desktop 테넌트 환경 내에서 하나 이상의 동일한 가상 머신 컬렉션입니다. 각 호스트 풀은 사용자가 물리적 데스크톱에서처럼 상호 작용할 수 있는 앱 그룹을 포함할 수 있습니다.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>PowerShell 클라이언트를 사용 하 여 호스트 풀 만들기

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)(아직 다운로드하고 가져오지 않은 경우).

다음 cmdlet을 실행 하 여 Windows 가상 데스크톱 환경에 로그인 합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

그런 다음이 cmdlet을 실행 하 여 Windows 가상 데스크톱 테넌트에 새 호스트 풀을 만듭니다.

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

다음 cmdlet을 실행 하 여 등록 토큰을 만들어 호스트 풀에 조인 하 고 로컬 컴퓨터의 새 파일에 저장 하도록 세션 호스트에 권한을 부여 합니다. -ExpirationHours 매개 변수를 사용 하 여 등록 토큰의 유효 기간을 지정할 수 있습니다.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

그런 다음이 cmdlet을 실행 하 여 호스트 풀의 기본 데스크톱 앱 그룹에 Azure Active Directory 사용자를 추가 합니다.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

**RdsAppGroupUser** cmdlet은 보안 그룹 추가를 지원 하지 않으며 앱 그룹에 한 번에 한 사용자만 추가 합니다. 앱 그룹에 여러 사용자를 추가 하려면 적절 한 사용자 계정 이름을 사용 하 여 cmdlet을 다시 실행 합니다.

다음 cmdlet을 실행 하 여 등록 토큰을 변수로 내보냅니다 .이 변수는 나중에 [가상 컴퓨터를 Windows 가상 데스크톱 호스트 풀에 등록](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)하는 데 사용 됩니다.

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>호스트 풀에 대 한 가상 컴퓨터 만들기

이제 Windows 가상 데스크톱 호스트 풀에 가입할 수 있는 Azure 가상 컴퓨터를 만들 수 있습니다.

여러 가지 방법으로 가상 컴퓨터를 만들 수 있습니다.

- [Azure 갤러리 이미지에서 가상 머신 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [관리 되는 이미지에서 가상 머신 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [관리 되지 않는 이미지에서 가상 머신 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

>[!NOTE]
>Windows 7을 사용 하 여 호스트 OS로 가상 컴퓨터를 배포 하는 경우 만들기 및 배포 프로세스는 약간 다릅니다. 자세한 내용은 [Windows 가상 데스크톱에 windows 7 가상 컴퓨터 배포](deploy-windows-7-virtual-machine.md)를 참조 하세요.

세션 호스트 가상 컴퓨터를 만든 후에는 다른 라이선스를 지불 하지 않고 Windows 또는 Windows Server 가상 컴퓨터를 실행 하기 위해 [세션 호스트 VM에 windows 라이선스를 적용](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) 합니다. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Windows 가상 데스크톱 에이전트 설치용 가상 컴퓨터 준비

가상 컴퓨터를 준비 하려면 먼저 다음 작업을 수행 해야 합니다. Windows 가상 데스크톱 에이전트를 설치 하 고 Windows 가상 데스크톱 호스트 풀에 가상 컴퓨터를 등록 하려면 다음을 수행 해야 합니다.

- 컴퓨터에 도메인에 가입 해야 합니다. 이렇게 하면 들어오는 Windows 가상 데스크톱 사용자를 Azure Active Directory 계정에서 Active Directory 계정으로 매핑할 수 있으며 가상 컴퓨터에 대 한 액세스를 성공적으로 수행할 수 있습니다.
- 가상 컴퓨터가 Windows Server OS를 실행 하는 경우에는 RDSH (원격 데스크톱 세션 호스트) 역할을 설치 해야 합니다. RDSH 역할을 사용 하면 Windows 가상 데스크톱 에이전트를 제대로 설치할 수 있습니다.

성공적으로 도메인에 가입 하려면 각 가상 머신에서 다음 작업을 수행 합니다.

1. 가상 컴퓨터를 만들 때 제공한 자격 증명을 사용 하 여 [가상 컴퓨터에 연결](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) 합니다.
2. 가상 컴퓨터에서 **제어판** 을 시작 하 고 **시스템**을 선택 합니다.
3. **컴퓨터 이름**을 선택 하 고 **설정 변경**을 선택한 다음 **변경 ...** 을 선택 합니다.
4. **도메인** 을 선택 하 고 가상 네트워크에 Active Directory 도메인을 입력 합니다.
5. 도메인 가입 컴퓨터에 대 한 권한이 있는 도메인 계정을 사용 하 여 인증 합니다.

    >[!NOTE]
    > Azure AD DS(Azure Active Directory Domain Services) 환경에 VM을 조인하는 경우 도메인 가입 사용자가 [AAD DC 관리자 그룹](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)의 멤버이기도 한지 확인합니다.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Windows 가상 데스크톱 호스트 풀에 가상 컴퓨터 등록

Windows 가상 데스크톱 호스트 풀에 가상 컴퓨터를 등록 하는 것은 Windows 가상 데스크톱 에이전트를 설치 하는 것 만큼 간단 합니다.

Windows 가상 데스크톱 에이전트를 등록 하려면 각 가상 컴퓨터에서 다음을 수행 합니다.

1. 가상 컴퓨터를 만들 때 제공한 자격 증명을 사용 하 여 [가상 컴퓨터에 연결](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) 합니다.
2. Windows 가상 데스크톱 에이전트를 다운로드 하 여 설치 합니다.
   - [Windows 가상 데스크톱 에이전트](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)를 다운로드 합니다.
   - 다운로드 한 설치 관리자를 마우스 오른쪽 단추로 클릭 하 고 **속성**을 선택한 다음 **차단 해제**를 선택 하 고 **확인**을 선택 합니다. 그러면 시스템에서 설치 관리자를 신뢰할 수 있습니다.
   - 설치 관리자를 실행합니다. 설치 관리자에서 등록 토큰을 묻는 메시지가 표시 되 면 **RdsRegistrationInfo** cmdlet에서 가져온 값을 입력 합니다.
3. Windows 가상 데스크톱 에이전트 부팅 로더를 다운로드 하 여 설치 합니다.
   - [Windows 가상 데스크톱 에이전트 부팅 로더](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)를 다운로드 합니다.
   - 다운로드 한 설치 관리자를 마우스 오른쪽 단추로 클릭 하 고 **속성**을 선택한 다음 **차단 해제**를 선택 하 고 **확인**을 선택 합니다. 그러면 시스템에서 설치 관리자를 신뢰할 수 있습니다.
   - 설치 관리자를 실행합니다.

>[!IMPORTANT]
>Azure에서 Windows Virtual Desktop 환경의 보안을 유지하도록 돕기 위해 VM에서 인바운드 포트 3389를 열지 않는 것이 좋습니다. Windows Virtual Desktop에서는 사용자가 인바운드 포트 3389를 열지 않아도 호스트 풀의 VM에 액세스할 수 있습니다. 문제 해결을 위해 포트 3389를 열어야 하는 경우 [Just-In-Time VM 액세스](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)를 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이제 호스트 풀을 만들었으므로 Remoteapp으로 채울 수 있습니다. Windows Virtual Desktop에서 앱을 관리하는 방법에 대한 자세한 내용은 앱 그룹 관리 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [앱 그룹 관리 자습서](./manage-app-groups.md)
