---
title: Active Directory Domain Services Azure Files FSLogix 프로필 컨테이너 만들기-Azure
description: 이 문서에서는 Azure Files 및 Azure Active Directory Domain Services를 사용 하 여 FSLogix 프로필 컨테이너를 만드는 방법을 설명 합니다.
author: Heidilohr
ms.topic: how-to
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ea834ed874f3011d95f8b924df860576f72bc4ee
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88825616"
---
# <a name="create-a-profile-container-with-azure-files-and-azure-ad-ds"></a>Azure Files 및 Azure AD DS를 사용 하 여 프로필 컨테이너 만들기

이 문서에서는 Azure Files 및 Azure Active Directory Domain Services (AD DS)를 사용 하 여 FSLogix 프로필 컨테이너를 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 준비 사항

이 문서에서는 Azure AD DS 인스턴스를 이미 설정 했다고 가정 합니다. 아직 없는 경우 먼저 [기본 관리 되는 도메인 만들기](../active-directory-domain-services/tutorial-create-instance.md) 의 지침에 따라 다음을 반환 합니다.

## <a name="add-azure-ad-ds-admins"></a>Azure AD DS 관리자 추가

관리자를 추가 하려면 새 사용자를 만들고 해당 사용자에 게 권한을 부여 합니다.

관리자를 추가 하려면:

1. 사이드바에서 **Azure Active Directory** 를 선택 하 고 **모든 사용자**를 선택한 다음 **새 사용자**를 선택 합니다.

2.  사용자 세부 정보를 필드에 입력 합니다.

3. 화면 왼쪽의 Azure Active Directory 창에서 **그룹**을 선택 합니다.

4. **AAD DC 관리자** 그룹을 선택 합니다.

5. 왼쪽 창에서 **멤버**를 선택 하 고 기본 창에서 **멤버 추가** 를 선택 합니다. 그러면 Azure AD에서 사용할 수 있는 모든 사용자의 목록이 표시 됩니다. 방금 만든 사용자 프로필의 이름을 선택 합니다.

## <a name="set-up-an-azure-storage-account"></a>Azure Storage 계정 설정

이제 SMB (서버 메시지 블록)를 통해 Azure AD DS 인증을 사용 하도록 설정할 시간입니다.

인증을 사용 하도록 설정 하려면:

1. 아직 설치 하지 않은 경우 [Azure Storage 계정 만들기](../storage/common/storage-account-create.md)의 지침에 따라 범용 v2 Azure Storage 계정을 설정 하 고 배포 합니다.

2. 계정을 설정 했으면 **리소스로 이동**을 선택 합니다.

3. 화면 왼쪽에 있는 창에서 **구성** 을 선택한 다음 기본 창에서 **Azure Files에 대 한 Azure Active Directory 인증** 을 사용 하도록 설정 합니다. 완료되면 **저장**을 선택합니다.

4. 화면 왼쪽에 있는 창에서 **개요** 를 선택 하 고 기본 창에서 **파일** 을 선택 합니다.

5. **파일 공유** 를 선택 하 고 화면 오른쪽에 표시 되는 필드에 **이름** 및 **할당량** 을 입력 합니다.

## <a name="assign-access-permissions-to-an-identity"></a>ID에 액세스 권한 할당

다른 사용자는 파일 공유에 액세스 하기 위한 액세스 권한이 필요 합니다. 이렇게 하려면 각 사용자에 게 적절 한 액세스 권한으로 역할을 할당 해야 합니다.

사용자에 게 액세스 권한을 할당 하려면:

1. Azure Portal에서 [Azure Storage 계정 설정](#set-up-an-azure-storage-account)에서 만든 파일 공유를 엽니다.

2. **Access Control(IAM)** 을 선택합니다.

3. **역할 할당 추가를**선택 합니다.

4. **역할 할당 추가** 탭의 역할 목록에서 적절 한 기본 제공 역할을 선택 합니다. 적절 한 사용 권한을 얻으려면 적어도 **저장소 파일 데이터 SMB 공유 참가자** 를 선택 해야 합니다.

5. 에 대 한 **액세스 할당**에 **Azure Active Directory 사용자, 그룹 또는 서비스 주체**를 선택 합니다.

6. 대상 Azure Active Directory id의 이름 또는 전자 메일 주소를 선택 합니다.

7. **저장**을 선택합니다.

## <a name="get-the-storage-account-access-key"></a>저장소 계정 액세스 키 가져오기

다음에는 저장소 계정에 대 한 액세스 키를 가져와야 합니다.

저장소 계정 액세스 키를 가져오려면:

1. Azure Portal 사이드바에서 **저장소 계정**을 선택 합니다.

2. 저장소 계정 목록에서 Azure AD DS를 사용 하도록 설정 하 고 위의 단계에서 사용자 지정 역할을 만든 계정을 선택 합니다.

3. **설정**아래에서 **액세스 키** 를 선택 하 고 **key1**에서 키를 복사 합니다.

4. **Virtual Machines** 탭으로 이동 하 여 호스트 풀의 일부가 될 VM을 찾습니다.

5. **Virtual Machines (adVM)** 아래에서 VM (가상 머신)의 이름을 선택 하 고 **연결** 을 선택 합니다.

    그러면 자체 자격 증명을 사용 하 여 VM에 로그인 할 수 있는 RDP 파일이 다운로드 됩니다.

    > [!div class="mx-imgBorder"]
    > ![가상 컴퓨터에 연결 창의 RDP 탭 스크린샷](media/rdp-tab.png)

6. VM에 로그인 한 경우 관리자 권한으로 명령 프롬프트를 실행 합니다.

7. 다음 명령을 실행합니다.

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
     ```

    - `<desired-drive-letter>`원하는 드라이브 문자 (예:)로 대체 `y:` 합니다.
    - 의 모든 인스턴스를 `<storage-account-name>` 앞에서 지정한 저장소 계정 이름으로 바꿉니다.
    - `<share-name>`을 이전에 만든 공유의 이름으로 바꿉니다.
    - `<storage-account-key>`을 Azure의 저장소 계정 키로 바꿉니다.

    예를 들어:

     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. 다음 명령을 실행 하 여 Windows 가상 데스크톱 사용자가 다른 사용자의 프로필 컨테이너에 대 한 액세스를 차단 하는 동안 자신의 프로필 컨테이너를 만들 수 있도록 합니다.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

    - `<mounted-drive-letter>`드라이브를 매핑하는 데 사용한 드라이브의 문자로 대체 합니다.
    - `<user-email>`공유에 대 한 액세스를 필요로 하는 사용자를 포함 하는 사용자 또는 Active Directory 그룹의 UPN으로 대체 합니다.

    예를 들어:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="create-a-profile-container"></a>프로필 컨테이너 만들기

이제 프로필을 사용할 준비가 되었으므로 FSLogix 프로필 컨테이너를 만들어 보겠습니다.

FSLogix 프로필 컨테이너를 구성 하려면:

1. 이 문서의 시작 부분에서 구성한 세션 호스트 VM에 로그인 한 다음 [FSLogix 에이전트를 다운로드 하 여 설치](/fslogix/install-ht/)합니다.

2. 다운로드 한 fslogix 에이전트 파일의 압축을 풀고 **x64**  >  **릴리스**로 이동한 다음 **FSLogixAppsSetup.exe**을 엽니다.

3. 설치 관리자가 시작 되 면 **사용 조건에 동의 함을 선택 합니다.** 해당 하는 경우 새 키를 제공 합니다.

4. **설치**를 선택합니다.

5. **C 드라이브**를 연 다음 **Program Files**  >  **fslogix**  >  **앱** 으로 이동 하 여 fslogix 에이전트가 제대로 설치 되었는지 확인 합니다.

     >[!NOTE]
     > 호스트 풀에 여러 Vm이 있는 경우 각 VM에 대해 1 ~ 5 단계를 반복 해야 합니다.

6. 관리자 권한으로 **레지스트리 편집기** (RegEdit)를 실행 합니다.

7. **컴퓨터**  >  **HKEY_LOCAL_MACHINE**  >  **software**  >  **fslogix**로 이동 하 여 **fslogix**를 마우스 오른쪽 단추로 클릭 하 고 **새로 만들기**를 선택한 다음 **키**를 선택 합니다.

8. **프로필**이라는 새 키를 만듭니다.

9.  **프로필**을 마우스 오른쪽 단추로 클릭 하 고 **새로 만들기**를 선택한 다음 **DWORD (32 비트) 값을 선택 합니다.** 값의 이름을 **Enabled** 로 설정 하 고 **데이터** 값을 **1**로 설정 합니다.

    > [!div class="mx-imgBorder"]
    > ![프로필 키의 스크린샷 REG_DWORD 파일은 강조 표시 되 고 데이터 값은 1로 설정 됩니다.](media/dword-value.png)

10. **프로필**을 마우스 오른쪽 단추로 클릭 하 고 **새로 만들기**를 선택한 다음 **다중 문자열 값**을 선택 합니다. **VHDLocations** 값의 이름을로 설정 하 고 Azure Files 공유에 대 한 URI를 `\\fsprofile.file.core.windows.net\share` 데이터 값으로 설정 합니다.

    > [!div class="mx-imgBorder"]
    > ![VHDLocations 파일을 표시 하는 프로필 키의 스크린샷 해당 데이터 값은 Azure Files 공유에 대 한 URI를 표시 합니다.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>세션 호스트에 사용자 할당

이제 세션 호스트에 사용자를 할당 해야 합니다.

사용자를 할당 하려면:

1. 관리자 권한으로 Windows PowerShell을 실행 한 후 다음 cmdlet을 실행 하 여 PowerShell을 사용 하 여 Windows 가상 데스크톱에 로그인 합니다.

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   자격 증명을 입력 하 라는 메시지가 표시 되 면 Windows 가상 데스크톱 테 넌 트에서 TenantCreator, RDS Owner 또는 RDS 참가자 역할을 부여 받은 동일한 사용자를 입력 합니다.

2. 다음 cmdlet을 실행 하 여 원격 데스크톱 그룹에 사용자를 할당 합니다.

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    이전 cmdlet과 마찬가지로 `<your-wvd-tenant>` , `<wvd-pool>` 및를 `<user-principal>` 관련 값으로 바꾸어야 합니다.

    예를 들어:

     ```powershell
     $pool1 = "contoso"

     $tenant = "contoso"

     $appgroup = "Desktop Application Group"

     $user1 = "jane.doe@contoso.com"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>프로필이 작동 하는지 확인

이제 만든 프로필이 있고 의도 한 대로 작동 하는지 확인 해야 합니다.

프로필을 확인 하려면:

1. 브라우저를 열고 [Windows 가상 데스크톱 웹 클라이언트](https://rdweb.wvd.microsoft.com/arm/webclient)로 이동 합니다.

2. 원격 데스크톱 그룹에 할당 된 사용자 계정으로 로그인 합니다.

3. 사용자 세션이 설정 되 면 Azure Portal을 열고 관리 계정으로 로그인 합니다.

4. 사이드바에서 **저장소 계정**을 선택 합니다.

5. 세션 호스트 풀의 파일 공유로 구성한 저장소 계정을 선택 하 고 Azure AD DS에서 사용 하도록 설정 합니다.

6. **파일** 아이콘을 선택 하 고 공유를 확장 합니다.

    모든 항목이 올바르게 설정 되 면 다음과 같이 이름이 지정 된 **디렉터리가** 표시 됩니다 `<user SID>-<username>` .

## <a name="next-steps"></a>다음 단계

FSLogix 프로필 컨테이너를 만드는 다른 방법을 찾고 있는 경우 다음 문서를 확인 하세요.

- [파일 공유를 사용 하 여 호스트 풀에 대 한 프로필 컨테이너를 만듭니다](create-host-pools-user-profile.md).
- [Azure NetApp Files를 사용 하 여 호스트 풀의 FSLogix 프로필 컨테이너 만들기](create-fslogix-profile-container.md)

[Fslogix 프로필 컨테이너 및 azure files](fslogix-containers-azure-files.md)에서 Azure 파일용 fslogix 컨테이너와 관련 된 개념에 대 한 자세한 정보를 찾을 수 있습니다.
