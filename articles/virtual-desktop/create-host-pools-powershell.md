---
title: PowerShell-Azure 사용 하 여 Windows 가상 데스크톱 미리 보기 호스트 풀 만들기
description: PowerShell cmdlet을 사용 하 여 Windows 가상 데스크톱 미리 보기에서 호스트 풀을 만드는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: 2af9df4771d58f2288820dad8ef8d7ac84deb8ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870543"
---
# <a name="create-a-host-pool-with-powershell"></a>PowerShell을 사용한 호스트 풀 만들기

호스트 풀은 Windows Virtual Desktop 미리 보기 테넌트 환경 내에서 하나 이상의 동일한 가상 머신 컬렉션입니다. 각 호스트 풀은 사용자가 물리적 데스크톱에서처럼 상호 작용할 수 있는 앱 그룹을 포함할 수 있습니다.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>PowerShell 클라이언트를 사용 하 여 호스트 풀을 만들려면

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)(아직 다운로드하고 가져오지 않은 경우).

Windows 가상 데스크톱 환경에 로그인 하려면 다음 cmdlet을 실행 합니다.

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

그런 다음 테 넌 트 그룹에 컨텍스트를 설정 하려면 다음 cmdlet을 실행 합니다. 테 넌 트 그룹의 이름에 없는 경우 테 넌 트 이므로 "기본 테 넌 트 그룹의" 가장 가능성이 높은이 cmdlet을 건너뛸 수 있습니다.

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
```

다음으로 Windows Virtual Desktop 테 넌 트에 새 호스트 풀을 만들려면이 cmdlet을 실행 합니다.

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

호스트 풀에 조인 하 고 로컬 컴퓨터에 새 파일에 저장 된 세션 호스트에 권한을 부여 하는 등록 토큰을 만들려면 다음 cmdlet을 실행 합니다. 기간 등록 토큰이 유효-ExpirationHours 매개 변수를 사용 하 여 지정할 수 있습니다.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

그런 다음 호스트 풀에 대 한 기본 데스크톱 응용 프로그램 그룹에 Azure Active Directory 사용자를 추가 하려면이 cmdlet을 실행 합니다.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

합니다 **추가 RdsAppGroupUser** cmdlet 추가 보안 그룹을 지원 하지 않습니다 하 고만 앱 그룹에 한 번에 한 명의 사용자를 추가 합니다. 앱 그룹에 여러 사용자를 추가 하려는 경우에 적절 한 사용자 계정 이름을 사용 하 여 cmdlet을 다시 실행 하십시오.

등록 토큰을 나중에 사용 되는 변수를 내보내려면 다음 cmdlet을 실행 [호스트 Windows 가상 데스크톱 풀에 가상 컴퓨터 등록](#register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool)합니다.

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>호스트 풀에 대 한 가상 머신 만들기

이제 호스트 Windows 가상 데스크톱 풀에 조인할 수는 Azure 가상 컴퓨터를 만들 수 있습니다.

여러 가지 방법으로 가상 컴퓨터를 만들 수 있습니다.

- [Azure 갤러리 이미지에서 가상 머신 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [관리 되는 이미지에서 가상 머신 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [관리 되지 않는 이미지에서 가상 머신 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-preview-agent-installations"></a>Windows 가상 데스크톱 미리 보기 에이전트 설치에 대 한 가상 컴퓨터 준비

가상 데스크톱 Windows 에이전트를 설치 및 가상 데스크톱 Windows 호스트 풀에 가상 컴퓨터를 등록 하기 전에 가상 컴퓨터를 준비 하려면 다음을 수행 해야 합니다.

- 컴퓨터가 도메인에 가입 해야합니다. 따라서 들어오는 Windows 가상 데스크톱 사용자가 자신의 Azure Active Directory 계정에서 Active Directory 계정에 매핑할 수 하 여 가상 컴퓨터에 대 한 액세스를 허용 했습니다.
- 가상 컴퓨터는 Windows Server 운영 체제를 실행 하는 경우에 데스크톱 세션 호스트 RDSH (원격) 역할을 설치 해야 합니다. RDSH 역할 가상 데스크톱 Windows 에이전트를 제대로 설치를 허용 합니다.

성공적으로 도메인 가입 하려면 각 가상 머신에서 다음 작업을 수행 합니다.

1. [가상 머신에 연결](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) 가상 머신을 만들 때 제공한 자격 증명을 사용 합니다.
2. 가상 컴퓨터에서 시작 **Control Panel** 선택한 **시스템**입니다.
3. 선택 **컴퓨터 이름**를 선택 **설정을 변경**를 선택한 후 **변경 하는 중...**
4. 선택 **도메인** 가상 네트워크에 Active Directory 도메인을 입력 합니다.
5. 도메인 가입 컴퓨터에 대 한 권한이 있는 도메인 계정으로 인증 합니다.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool"></a>Windows 가상 데스크톱 미리 보기 호스트 풀에 가상 컴퓨터 등록

호스트 Windows 가상 데스크톱 풀에 가상 컴퓨터를 등록 하기만 가상 데스크톱 Windows 에이전트를 설치 하면 됩니다.

가상 데스크톱 Windows 에이전트를 등록 하려면 각 가상 컴퓨터에서 다음을 수행 합니다.

1. [가상 머신에 연결](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) 가상 머신을 만들 때 제공한 자격 증명을 사용 합니다.
2. 에이전트 다운로드 및 설치 된 Windows 가상 데스크톱.
   - 다운로드 합니다 [Windows 가상 데스크톱 에이전트](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)합니다.
   - 다운로드 한 설치 관리자를 마우스 오른쪽 단추로 클릭 한 다음를 선택 합니다 **속성**를 선택 **차단 해제**을 선택한 후 **확인**합니다. 이렇게 하면 설치 관리자를 신뢰 하도록 합니다.
   - 설치 관리자를 실행합니다. 설치 프로그램 등록 토큰을 요청에서 가져온 값을 입력 합니다 **내보내기 RdsRegistrationInfo** cmdlet.
3. 다운로드 하 여 Windows 가상 데스크톱 에이전트 부팅 로더를 설치 합니다.
   - 다운로드 합니다 [Windows 가상 데스크톱 에이전트 부트로더](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)합니다.
   - 다운로드 한 설치 관리자를 마우스 오른쪽 단추로 클릭 한 다음를 선택 합니다 **속성**를 선택 **차단 해제**을 선택한 후 **확인**합니다. 이렇게 하면 설치 관리자를 신뢰 하도록 합니다.
   - 설치 관리자를 실행합니다.
4. 설치 하거나 가상 데스크톱 Windows side-by-side-스택을 활성화 합니다. 단계는 다를 수는 OS 버전에 따라 가상 컴퓨터를 사용 합니다.
   - 가상 머신의 OS가 Windows Server 2016 합니다.
     - 다운로드 합니다 [가상 데스크톱 Windows side-by-side-스택](https://go.microsoft.com/fwlink/?linkid=2084270)합니다.
     - 다운로드 한 설치 관리자를 마우스 오른쪽 단추로 클릭 한 다음를 선택 합니다 **속성**를 선택 **차단 해제**을 선택한 후 **확인**합니다. 이렇게 하면 설치 관리자를 신뢰 하도록 합니다.
     - 설치 관리자를 실행합니다.
   - 가상 머신의 OS가 Windows 10 1809 이상 또는 Windows Server 2019 이상:
     - 다운로드 합니다 [스크립트](https://go.microsoft.com/fwlink/?linkid=2084268) side-by-side-스택을 활성화 합니다.
     - 다운로드 한 스크립트를 마우스 오른쪽 단추로 클릭 한 다음를 선택 합니다 **속성**를 선택 **차단 해제**을 선택한 후 **확인**합니다. 이렇게 하면 스크립트를 신뢰 하도록 합니다.
     - **시작** 메뉴에서 Windows PowerShell ISE에 대 한 검색을 마우스 오른쪽 단추로 클릭 한 다음 선택 **관리자 권한으로 실행**합니다.
     - 선택 **파일**, 다음 **열기...** , 한 다음 다운로드 한 파일에서 PowerShell 스크립트를 찾아서 엽니다.
     - 스크립트를 실행 하려면 녹색 재생 단추를 선택 합니다.

>[!IMPORTANT]
>Azure에서 Windows Virtual Desktop 환경의 보안을 유지하도록 돕기 위해 VM에서 인바운드 포트 3389를 열지 않는 것이 좋습니다. Windows Virtual Desktop에서는 사용자가 인바운드 포트 3389를 열지 않아도 호스트 풀의 VM에 액세스할 수 있습니다. 문제 해결을 위해 포트 3389를 열어야 하는 경우 [Just-In-Time VM 액세스](https://docs.microsoft.com/en-us/azure/security-center/security-center-just-in-time)를 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이제 호스트 풀 했으므로 Remoteapp을 사용 하 여 채울 수 있습니다. Windows Virtual Desktop에서 앱을 관리하는 방법에 대한 자세한 내용은 앱 그룹 관리 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [앱 그룹 관리 자습서](./manage-app-groups.md)
