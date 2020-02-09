---
title: FSLogix 프로필 컨테이너 NetApp Windows 가상 데스크톱-Azure
description: Windows 가상 데스크톱에서 Azure NetApp Files를 사용 하 여 FSLogix 프로필 컨테이너를 만드는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: helohr
ms.openlocfilehash: 1e26d61e0b1ec50e7a3831970af1fd8fad7fed99
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483649"
---
# <a name="create-an-fslogix-profile-container-for-a-host-pool-using-azure-netapp-files"></a>Azure NetApp Files를 사용 하 여 호스트 풀의 FSLogix 프로필 컨테이너 만들기

[Windows 가상 데스크톱 서비스](overview.md)의 사용자 프로필 솔루션으로 FSLogix 프로필 컨테이너를 사용 하는 것이 좋습니다. FSLogix 프로필 컨테이너는 단일 컨테이너에 전체 사용자 프로필을 저장 하 고 Windows 가상 데스크톱과 같은 비영구 원격 컴퓨팅 환경에서 프로필을 로밍할 수 있도록 설계 되었습니다. 로그인 할 때 컨테이너는 로컬로 지원 되는 VHD (가상 하드 디스크) 및 Hyper-v VHDX (가상 하드 디스크)를 사용 하 여 컴퓨팅 환경에 동적으로 연결 됩니다. 이러한 고급 필터 드라이버 기술을 통해 사용자 프로필을 즉시 사용할 수 있으며 시스템에 로컬 사용자 프로필과 똑같이 표시 됩니다. FSLogix 프로필 컨테이너에 대해 자세히 알아보려면 [Fslogix 프로필 컨테이너 및 Azure files](fslogix-containers-azure-files.md)를 참조 하세요.

Windows 가상 데스크톱 환경에 대해 엔터프라이즈급 SMB 볼륨을 빠르고 안정적으로 프로 비전 하는 데 도움이 되는 사용 하기 쉬운 Azure native platform 서비스인 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)를 사용 하 여 FSLogix 프로필 컨테이너를 만들 수 있습니다. Azure NetApp Files에 대 한 자세한 내용은 [Azure NetApp Files 항목](../azure-netapp-files/azure-netapp-files-introduction.md) 을 참조 하세요.

이 가이드에서는 Azure NetApp Files 계정을 설정 하 고 Windows 가상 데스크톱에서 FSLogix 프로필 컨테이너를 만드는 방법을 보여 줍니다.

이 문서에서는 이미 [호스트 풀이](create-host-pools-azure-marketplace.md) 설정 되어 있고 Windows 가상 데스크톱 환경에서 하나 이상의 테넌트로 그룹화 되어 있다고 가정 합니다. 테넌트를 설정 하는 방법을 알아보려면 [Windows 가상 데스크톱에서 테넌트 만들기](tenant-setup-azure-active-directory.md) 및 [기술 커뮤니티 블로그 게시물](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054)을 참조 하세요.

이 가이드의 지침은 Windows 가상 데스크톱 사용자 전용입니다. Windows 가상 데스크톱 외부에서 Azure NetApp Files를 설정 하 고 FSLogix 프로필 컨테이너를 만드는 방법에 대 한 일반적인 지침을 찾고 있는 경우 [Azure NetApp Files 설정 및 NFS 볼륨 만들기 빠른](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)시작을 참조 하세요.

>[!NOTE]
>이 문서에서는 Azure NetApp Files 공유에 대 한 액세스를 보호 하기 위한 모범 사례에 대해서는 설명 하지 않습니다.

>[!NOTE]
>Azure의 여러 FSLogix 프로필 컨테이너 저장소 옵션에 대 한 비교 자료를 찾고 있는 경우 [FSLogix 프로필 컨테이너에 대 한 저장소 옵션](store-fslogix-profile.md)을 참조 하세요.

## <a name="prerequisites"></a>선행 조건

호스트 풀에 대해 FSLogix 프로필 컨테이너를 만들려면 먼저 다음을 수행 해야 합니다.

- Windows 가상 데스크톱 설정 및 구성
- Windows 가상 데스크톱 호스트 풀 프로 비전
- [Azure NetApp Files 구독 사용](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)

## <a name="set-up-your-azure-netapp-files-account"></a>Azure NetApp Files 계정 설정

시작 하려면 Azure NetApp Files 계정을 설정 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 참가자 또는 관리자 권한이 있는지 확인 합니다.

2. 검색 표시줄의 오른쪽에 있는 **Azure Cloud Shell 아이콘** 을 선택 하 여 Azure Cloud Shell를 엽니다.

3. Azure Cloud Shell 열리면 **PowerShell**을 선택 합니다.

4. Azure Cloud Shell를 처음 사용 하는 경우 동일한 구독에서 저장소 계정을 만들어 Azure NetApp Files와 Windows 가상 데스크톱을 유지 합니다.

   ![창 아래쪽의 저장소 만들기 단추가 빨간색으로 강조 표시 된 저장소 계정 창이 표시 됩니다.](media/create-storage-button.png)

5. Azure Cloud Shell 로드 되 면 다음 두 cmdlet을 실행 합니다.

   ```powershell
   az account set --subscription <subscriptionID>
   ```

   ```powershell
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. 창 왼쪽에서 **모든 서비스**를 선택 합니다. 메뉴 맨 위에 표시 되는 검색 상자에 **Azure NetApp Files** 을 입력 합니다.

   ![모든 서비스 검색 상자에 "Azure NetApp Files"를 입력 하는 사용자의 스크린샷 검색 결과는 Azure NetApp Files 리소스를 표시 합니다.](media/azure-netapp-files-search-box.png)


7. 검색 결과에서 **Azure NetApp Files** 를 선택한 다음 **만들기**를 선택 합니다.

8. **추가** 단추를 선택합니다.
9. **새 NetApp 계정** 블레이드가 열리면 다음 값을 입력 합니다.

    - **이름**에 netapp 계정 이름을 입력 합니다.
    - **구독**의 경우 드롭다운 메뉴에서 4 단계에서 설정한 저장소 계정에 대 한 구독을 선택 합니다.
    - **리소스 그룹**의 경우 드롭다운 메뉴에서 기존 리소스 그룹을 선택 하거나 **새로 만들기**를 선택 하 여 새 리소스 그룹을 만듭니다.
    - **위치**의 경우 드롭다운 메뉴에서 netapp 계정의 지역을 선택 합니다. 이 지역은 세션 호스트 Vm과 동일한 지역 이어야 합니다.

   >[!NOTE]
   >현재 Azure NetApp Files는 지역에 걸쳐 볼륨의 탑재를 지원 하지 않습니다.

10. 완료 되 면 **만들기** 를 선택 하 여 netapp 계정을 만듭니다.

## <a name="create-a-capacity-pool"></a>용량 풀 만들기

다음으로 새 용량 풀을 만듭니다. 

1. Azure NetApp Files 메뉴로 이동 하 여 새 계정을 선택 합니다.
2. 계정 메뉴의 저장소 서비스에서 **용량 풀** 을 선택 합니다.
3. **풀 추가**를 선택 합니다.
4. **새 용량 풀** 블레이드가 열리면 다음 값을 입력 합니다.

    - **이름**에 새 용량 풀의 이름을 입력 합니다.
    - **서비스 수준**의 경우 드롭다운 메뉴에서 원하는 값을 선택 합니다. 대부분의 환경에서 **프리미엄** 을 권장 합니다.
       >[!NOTE]
       >Premium 설정은 프리미엄 서비스 수준에 사용할 수 있는 최소 처리량 (256 MBps)을 제공 합니다. 프로덕션 환경에 대 한 처리량을 조정 해야 할 수 있습니다. 최종 처리량은 [처리량 한도](../azure-netapp-files/azure-netapp-files-service-levels.md)에 설명 된 관계를 기반으로 합니다.
    - **크기 (TiB)** 에는 사용자의 요구에 가장 적합 한 용량 풀 크기를 입력 합니다. 최소 크기는 4 TiB입니다.

5. 작업이 완료 되 면 **확인**을 선택 합니다.

## <a name="join-an-active-directory-connection"></a>Active Directory 연결 조인

그런 다음 Active Directory 연결에 조인 해야 합니다.

1. 페이지의 왼쪽에 있는 메뉴에서 **Active Directory 연결** 을 선택 하 고 **조인** 단추를 선택 하 여 **조인 Active Directory** 페이지를 엽니다.

   ![조인 Active Directory 연결 메뉴의 스크린샷](media/active-directory-connections-menu.png)

2. **조인 Active Directory** 페이지에서 다음 값을 입력 하 여 연결을 조인 합니다.

    - **주 dns**의 경우 도메인 이름을 확인할 수 있는 사용자 환경에 DNS 서버의 IP 주소를 입력 합니다.
    - **도메인**에 대해 FQDN (정규화 된 도메인 이름)을 입력 합니다.
    - **SMB 서버 (컴퓨터 계정) 접두사**에 컴퓨터 계정 이름에 추가 하려는 문자열을 입력 합니다.
    - **사용자 이름**에 도메인 가입을 수행할 수 있는 권한이 있는 계정의 이름을 입력 합니다.
    - **암호**에 계정의 암호를 입력 합니다.

  >[!NOTE]
  >[Active Directory 연결](create-fslogix-profile-container.md#join-an-active-directory-connection) 에서 만든 컴퓨터 계정이 **컴퓨터** 또는 **기업의 관련 OU**아래 도메인 컨트롤러에 표시 되는지 확인 하는 것이 가장 좋습니다.

## <a name="create-a-new-volume"></a>새 볼륨 만들기

다음으로 새 볼륨을 만들어야 합니다.

1. **볼륨**을 선택 하 고 **볼륨 추가**를 선택 합니다.

2. **볼륨 만들기** 블레이드가 열리면 다음 값을 입력 합니다.

    - **볼륨 이름**에 새 볼륨의 이름을 입력 합니다.
    - **용량 풀**의 경우 드롭다운 메뉴에서 방금 만든 용량 풀을 선택 합니다.
    - **할당량 (GiB)** 에는 사용자 환경에 적합 한 볼륨 크기를 입력 합니다.
    - **가상 네트워크**의 경우 드롭다운 메뉴에서 도메인 컨트롤러에 연결 되어 있는 기존 가상 네트워크를 선택 합니다.
    - **서브넷**에서 **새로 만들기**를 선택 합니다. 이 서브넷은 Azure NetApp Files에 위임 된다는 점에 유의 하세요.

3.  **다음: 프로토콜 \>\>** 를 선택 하 여 프로토콜 탭을 열고 볼륨 액세스 매개 변수를 구성 합니다.

## <a name="configure-volume-access-parameters"></a>볼륨 액세스 매개 변수 구성

볼륨을 만든 후에는 볼륨 액세스 매개 변수를 구성 합니다.

1.  프로토콜 유형으로 **SMB** 를 선택 합니다.
2.  **Active Directory** 드롭다운 메뉴의 구성에서 원래 [Active Directory 연결](create-fslogix-profile-container.md#join-an-active-directory-connection)에 연결 된 것과 동일한 디렉터리를 선택 합니다. 구독 당 Active Directory 하나의 제한이 있다는 점에 유의 하세요.
3.  **공유 이름** 텍스트 상자에 세션 호스트 풀 및 해당 사용자가 사용 하는 공유의 이름을 입력 합니다.

4.  페이지 맨 아래에서 **검토 + 만들기** 를 선택 합니다. 그러면 유효성 검사 페이지가 열립니다. 볼륨의 유효성을 검사 한 후 **만들기**를 선택 합니다.

5.  이때 새 볼륨이 배포 되기 시작 합니다. 배포가 완료 되 면 Azure NetApp Files 공유를 사용할 수 있습니다.

6.  탑재 경로를 확인 하려면 **리소스로 이동** 을 선택 하 고 개요 탭에서 찾습니다.

    ![탑재 경로를 가리키는 빨간색 화살표가 있는 개요 화면의 스크린샷](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>세션 호스트 Vm (가상 머신)에서 FSLogix 구성

이 섹션은 [파일 공유를 사용 하 여 호스트 풀에 대 한 프로필 컨테이너 만들기](create-host-pools-user-profile.md)를 기반으로 합니다.

1. 세션 호스트 VM에서 여전히 원격으로 로그인 하는 동안 [FSLogix 에이전트 .zip 파일을 다운로드](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) 합니다.

2. 다운로드 한 파일의 압축을 풉니다.

3. 파일에서 **x64** > **릴리스** 로 이동 하 여 **FSLogixAppsSetup**를 실행 합니다. 설치 메뉴가 열립니다.

4.  제품 키가 있는 경우 제품 키 텍스트 상자에 입력 합니다.

5. **사용 조건에 동의 함**옆의 확인란을 선택 합니다.

6. **설치**를 선택합니다.

7. **C:\\Program Files\\FSLogix\\앱** 으로 이동 하 여 에이전트를 설치 했는지 확인 합니다.

8. 시작 메뉴에서 관리자 권한으로 **RegEdit** 를 실행 합니다.

9. **컴퓨터\\HKEY_LOCAL_MACHINE\\소프트웨어\\FSLogix**로 이동 합니다.

10. **프로필**이라는 키를 만듭니다.

11.  **REG_DWORD** 형식을 데이터 값 **1**로 설정 하 여 **Enabled** 라는 값을 만듭니다.

12. **다중 문자열** 형식을 사용 하 여 **VHDLocations** 라는 값을 만들고 해당 데이터 값을 Azure NetApp Files 공유에 대 한 URI로 설정 합니다.

13. 로그인 하기 전에 기존 로컬 프로필에 대 한 문제를 방지 하기 위해 DWORD 값이 1 인 **DeleteLocalProfileWhenVHDShouldApply** 라는 값을 만듭니다.

     >[!WARNING]
     >DeleteLocalProfileWhenVHDShouldApply 값을 만들 때는 주의 해야 합니다. FSLogix 프로필 시스템에서 사용자에 게 FSLogix 프로필이 있어야 하지만 로컬 프로필이 이미 존재 하는 것으로 확인 되 면 프로필 컨테이너는 로컬 프로필을 영구적으로 삭제 합니다. 그러면 사용자에 게 새 FSLogix 프로필을 사용 하 여 로그인 됩니다.

## <a name="assign-users-to-session-host"></a>세션 호스트에 사용자 할당

1. 관리자 권한으로 **POWERSHELL ISE** 를 열고 Windows 가상 데스크톱에 로그인 합니다.

2. 다음 cmdlet을 실행합니다.

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. 자격 증명을 입력 하 라는 메시지가 표시 되 면 Windows 가상 데스크톱 테넌트에 테넌트 작성자 또는 RDS 소유자/RDS 참가자 역할이 있는 사용자의 자격 증명을 입력 합니다.

4. 다음 cmdlet을 실행 하 여 원격 데스크톱 그룹에 사용자를 할당 합니다.

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>사용자가 Azure NetApp 파일 공유에 액세스할 수 있는지 확인

1. 인터넷 브라우저를 열고 <https://rdweb.wvd.microsoft.com/webclient/index.html>로 이동 합니다.

2. 원격 데스크톱 그룹에 할당 된 사용자의 자격 증명을 사용 하 여 로그인 합니다.

3. 사용자 세션을 설정한 후에는 관리자 계정으로 Azure Portal에 로그인 합니다.

4. **Azure NetApp Files**를 열고 Azure NetApp Files 계정을 선택한 다음, **볼륨**을 선택 합니다. 볼륨 메뉴가 열리면 해당 볼륨을 선택 합니다.

   ![볼륨 단추를 선택 하 여 Azure Portal 이전에 설정한 NetApp 계정의 스크린샷](media/netapp-account.png)

5. **개요** 탭으로 이동 하 여 FSLogix 프로필 컨테이너가 공간을 사용 하 고 있는지 확인 합니다.

6. 원격 데스크톱을 사용 하 여 호스트 풀의 모든 VM 부분에 직접 연결 하 고 **파일 탐색기** 를 엽니다. 그런 다음 **탑재 경로로** 이동 합니다. 다음 예제에서는 탑재 경로가 \\\\anf-SMB-3863.gt1107.onmicrosoft.com\\anf-볼륨)으로 이동 합니다.

   이 폴더에는 다음 예제에 나와 있는 것과 같은 프로필 VHD (또는 VHDX)가 있어야 합니다.

   ![탑재 경로에 있는 폴더 내용의 스크린샷 내부는 "Profile_ssbb" 이라는 단일 VHD 파일입니다.](media/mount-path-folder.png)

## <a name="next-steps"></a>다음 단계

FSLogix 프로필 컨테이너를 사용 하 여 사용자 프로필 공유를 설정할 수 있습니다. 새 컨테이너를 사용 하 여 사용자 프로필 공유를 만드는 방법을 알아보려면 [파일 공유를 사용 하 여 호스트 풀에 대 한 프로필 컨테이너 만들기](create-host-pools-user-profile.md)를 참조 하세요.
