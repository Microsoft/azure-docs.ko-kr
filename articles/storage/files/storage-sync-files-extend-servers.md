---
title: 자습서 - Azure 파일 동기화를 사용하여 Windows 파일 서버 확장 | Microsoft Docs
description: Azure 파일 동기화를 통해 Windows 파일 서버를 확장하는 방법을 처음부터 끝까지 살펴봅니다.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e3154b9635da889ed7f0484fc04c565c27e9241b
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031512"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>자습서: Azure 파일 동기화를 사용하여 Windows 파일 서버 확장

이 문서에서는 Azure 파일 동기화를 사용하여 Windows 서버의 스토리지 용량을 확장하기 위한 기본 단계를 설명합니다. 자습서는 Azure VM(가상 머신)으로 Windows Server를 을 특징으로 하지만 일반적으로는 온-프레미스 서버에 이 프로세스를 수행하게 될 것입니다. [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md) 문서의 자체 환경에서 Azure 파일 동기화 배포를 위한 지침을 찾을 수 있습니다.

> [!div class="checklist"]
> * 스토리지 동기화 서비스 배포
> * Azure 파일 동기화에 사용할 Windows Server 준비
> * Azure 파일 동기화 에이전트 설치
> * 스토리지 동기화 서비스에 Windows Server 등록
> * 동기화 그룹 및 클라우드 엔드포인트 만들기
> * 서버 엔드포인트 만들기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="prepare-your-environment"></a>환경 준비

이 자습서의 경우 Azure 파일 동기화를 배포하려면 먼저 다음을 수행해야 합니다.

- Azure 스토리지 계정 및 파일 공유 만들기
- Windows Server 2016 Datacenter VM 설정
- Azure 파일 동기화에 대한 Windows Server VM 준비

### <a name="create-a-folder-and-txt-file"></a>폴더 및 .txt 파일 만들기

로컬 컴퓨터에서 이름이 _FilesToSync_인 새 폴더를 만들고 이름이 _mytestdoc.txt_인 텍스트 파일을 추가합니다. 나중에 이 자습서에서 이 파일 공유에 파일을 업로드할 것입니다.

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>파일 공유 만들기

Azure 스토리지 계정을 배포한 후 파일 공유를 만듭니다.

1. Azure Portal에서 **리소스로 이동**을 선택합니다.
1. 스토리지 계정 창에서 **파일**을 선택합니다.

    ![파일 선택](./media/storage-sync-files-extend-servers/click-files.png)

1. **+ 파일 공유**를 선택합니다.

    ![파일 공유 추가 단추 선택](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. 새 파일 공유의 이름을 _afsfileshare_로 지정합니다. **할당량**에 대해 "1"을 입력한 다음, **만들기**를 선택합니다. 할당량은 최대 5TiB까지 가능하지만 이 자습서에서는 1GB만 필요합니다.

    ![새 파일 공유에 대해 이름과 할당량 입력](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. 새 파일 공유를 선택합니다. 파일 공유 위치에서 **업로드**를 선택합니다.

    ![파일 업로드](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. .txt 파일을 만들었던 _FilesToSync_ 폴더로 이동하고, _mytestdoc.txt_를 선택한 다음, **업로드**를 선택합니다.

    ![파일 공유 찾아보기](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

지금까지 스토리지 계정과 파일이 하나 있는 파일 공유를 만들었습니다. 다음으로 이 자습서에서 온-프레미스 서버를 나타내는 Windows Server 2016 Datacenter가 있는 Azure VM을 배포합니다.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>VM 배포 및 데이터 디스크 연결

1. Azure Portal로 이동하고 왼쪽 메뉴를 확장합니다. 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.
1. **Azure Marketplace** 리소스 목록 위에 있는 검색 상자에서 **Windows Server 2016 Datacenter**를 검색하고 결과에서 선택합니다. **만들기**를 선택합니다.
1. **기본 사항** 탭으로 이동합니다. **프로젝트 세부 정보**에서 이 자습서용으로 만든 리소스 그룹을 선택합니다.

   ![포털 블레이드에서 VM에 대한 기본 정보 입력](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. **인스턴스 세부 정보**에서 VM 이름을 입력합니다. 예를 들어 _myVM_을 사용합니다.
1. **지역**, **가용성 옵션**, **이미지** 및 **크기**에 대한 기본 설정을 변경하지 마십시오.
1. **관리 계정**에서 VM의 **사용자 이름**과 **암호**를 입력합니다.
1. **인바운드 포트 규칙**에서 **선택한 포트 허용**을 선택한 다음, 드롭다운 메뉴에서 **RDP(3389)** 및 **HTTP**를 선택합니다.

1. VM을 만들기 전에 데이터 디스크를 만들어야 합니다.

   1. **다음: 디스크**를 선택합니다.

      ![데이터 디스크 추가](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. **디스크** 탭의 **디스크 옵션**에서 기본값을 그대로 둡니다.
   1. **데이터 디스크**에서 **새 디스크 만들기 및 연결**을 선택합니다.

   1. 이 자습서의 경우 **1GB**로 변경할 수 있는 **크기(GiB)** 를 제외하고 기본 설정을 사용합니다.

      ![데이터 디스크 세부 정보](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. **확인**을 선택합니다.
1. **검토 + 만들기**를 선택합니다.
1. **만들기**를 선택합니다.

   **알림** 아이콘을 선택하여 **배포 진행률**을 확인할 수 있습니다. 새 VM을 만드는 데 몇 분 정도 걸릴 수 있습니다.

1. VM 배포가 완료되면 **리소스로 이동**을 선택합니다.

   ![리소스로 이동](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

지금까지 새 가상 머신을 만들었고 데이터 디스크를 연결했습니다. 다음으로 VM에 연결합니다.

### <a name="connect-to-your-vm"></a>VM에 연결

1. Azure Portal의 가상 머신 속성 페이지에서 **연결**을 선택합니다.

   ![포털에서 Azure VM에 연결](./media/storage-sync-files-extend-servers/connect-vm.png)

1. **가상 머신에 연결** 페이지에서 포트 3389를 통해 **IP 주소**로 연결하는 기본 옵션을 유지합니다. **RDP 파일 다운로드**를 선택합니다.

   ![RDP 파일 다운로드](./media/storage-sync-files-extend-servers/download-rdp.png)

1. 다운로드한 RDP 파일을 열고 메시지가 표시되면 **연결**을 선택합니다.
1. **Windows 보안** 창에서 **추가 선택 사항** 및 **다른 계정 사용**을 차례로 선택합니다. 사용자 이름으로 *localhost\username*을 입력하고, 가상 머신에 대해 만든 암호를 입력한 다음, **확인**을 선택합니다.

   ![다른 옵션 선택](./media/storage-sync-files-extend-servers/local-host2.png)

1. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예** 또는 **계속**을 선택하여 연결을 만듭니다.

### <a name="prepare-the-windows-server"></a>Windows Server 준비

Windows Server 2016 Datacenter 서버에 대해 Internet Explorer 보안 강화 구성을 사용하지 않도록 설정합니다. 이 단계는 최초 서버 등록에만 필요합니다. 서버가 등록된 후에 사용하도록 다시 설정할 수 있습니다.

Windows Server 2016 Datacenter VM에서 서버 관리자가 자동으로 열립니다.  서버 관리자가 기본적으로 열리지 않으면 시작 메뉴에서 검색합니다.

1. **서버 관리자**에서 **로컬 서버**를 선택합니다.

   ![서버 관리자 UI 왼쪽에 있는 "로컬 서버"](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. **속성** 창에서 **IE 보안 강화 구성** 링크를 선택합니다.  

    ![서버 관리자 UI의 "IE 보안 강화 구성" 창](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. **Internet Explorer 보안 강화 구성** 대화 상자에서 **관리자**와 **사용자**에 대해 **사용 안 함**을 선택합니다.

    ![“해제"가 선택된 Internet Explorer 보안 강화 구성 팝업 창](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

이제 VM에 데이터 디스크를 추가할 수 있습니다.

### <a name="add-the-data-disk"></a>데이터 디스크를 추가합니다.

1. **Windows Server 2016 Datacenter** VM에 있는 상태에서 **파일 및 스토리지 서비스** > **볼륨** > **디스크**를 선택합니다.

    ![데이터 디스크](media/storage-sync-files-extend-servers/your-disk.png)

1. 이름이 **Msft 가상 디스크**인 1GB 디스크를 마우스 오른쪽 단추로 클릭하고 **새 볼륨**을 선택합니다.
1. 마법사를 완료합니다. 기본 설정을 사용하고 지정된 드라이브 문자를 기록합니다.
1. **만들기**를 선택합니다.
1. **닫기**를 선택합니다.

   이제 디스크를 온라인으로 전환했고 볼륨을 만들었습니다. Windows Server VM에서 파일 탐색기를 열어 최근에 추가된 데이터 디스크의 현재 상태를 확인합니다.

1. VM의 파일 탐색기에서 **이 PC**를 확장하고 새 드라이브를 엽니다. 이 예제에서는 F: 드라이브입니다.
1. 마우스 오른쪽 단추로 클릭하고 **새로 만들기** > **폴더**를 선택합니다. 폴더의 이름을 _FilesToSync_로 지정합니다.
1. **FilesToSync** 폴더를 엽니다.
1. 마우스 오른쪽 단추로 클릭하고 **새로 만들기** > **텍스트 문서**를 선택합니다. 텍스트 파일의 이름을 _MyTestFile_로 지정합니다.

    ![새 텍스트 파일 추가](media/storage-sync-files-extend-servers/new-file.png)

1. **파일 탐색기**와 **서버 관리자**를 닫습니다.

### <a name="download-the-azure-powershell-module"></a>Azure PowerShell 모듈 다운로드

다음으로 Windows Server 2016 Datacenter VM에서 서버에 Azure PowerShell 모듈을 설치합니다.

1. VM에서 관리자 권한 PowerShell 창을 엽니다.
1. 다음 명령 실행:

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > NuGet 2.8.5.201 이전 버전을 사용하는 경우 최신 버전의 NuGet을 다운로드하여 설치하라는 메시지가 표시됩니다.

   기본적으로 PowerShell 갤러리는 PowerShellGet에 대한 신뢰할 수 있는 리포지토리로 구성되지 않습니다. PSGallery를 처음 사용할 때는 다음과 같은 메시지가 표시됩니다.

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. 설치를 계속하려면 **예** 또는 **모두 예**로 답변합니다.

`Az` 모듈은 Azure PowerShell cmdlet의 롤업 모듈입니다. 설치하면 사용 가능한 모든 Azure Resource Manager 모듈이 다운로드되고 cmdlet을 사용할 수 있게 됩니다.

지금까지 자습서에 대한 환성을 설정했습니다. 스토리지 동기화 서비스를 배포할 준비가 되었습니다.

## <a name="deploy-the-service"></a>서비스 배포

Azure 파일 동기화를 배포하려면 먼저 선택한 구독의 리소스 그룹에 **스토리지 동기화 서비스** 리소스를 배치합니다. 스토리지 동기화 서비스는 해당 구독 및 리소스 그룹에서 액세스 권한을 상속합니다.

1. Azure Portal에서 **리소스 만들기**를 선택한 다음, **Azure 파일 동기화**를 검색합니다.
1. 검색 결과에서 **Azure 파일 동기화**를 선택합니다.
1. **만들기**를 선택하여 **스토리지 동기화 배포** 탭을 엽니다.

   ![스토리지 동기화 배포](media/storage-sync-files-extend-servers/afs-info.png)

   열리는 창에 다음 정보를 입력합니다.

   | 값 | Description |
   | ----- | ----- |
   | **이름** | 스토리지 동기화 서비스의 고유 이름(구독별)입니다.<br><br>이 자습서의 경우 _afssyncservice02_를 사용합니다. |
   | **구독** | 이 자습서에 대해 사용하는 Azure 구독입니다. |
   | **리소스 그룹** | 스토리지 동기화 서비스를 포함하는 리소스 그룹입니다.<br><br>이 자습서의 경우 _afsresgroup101918_을 사용합니다. |
   | **위치** | 미국 동부 |

1. 작업이 끝나면 **만들기**를 선택하여 **스토리지 동기화 서비스**를 배포합니다.
1. **알림** 탭 > **리소스로 이동**을 선택합니다.

## <a name="install-the-agent"></a>에이전트 설치

Azure 파일 동기화 에이전트는 Windows Server가 Azure 파일 공유와 동기화되도록 하는 다운로드 가능 패키지입니다.

1. **Windows Server 2016 Datacenter** VM에서 **Internet Explorer**를 엽니다.
1. [Microsoft 다운로드 센터](https://go.microsoft.com/fwlink/?linkid=858257)로 이동합니다. **Azure 파일 동기화 에이전트** 섹션으로 스크롤하여 **다운로드**를 선택합니다.

   ![동기화 에이전트 다운로드](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. **StorageSyncAgent_V3_WS2016.EXE**에 대한 확인란을 선택하고 **다음**을 선택합니다.

   ![에이전트 선택](media/storage-sync-files-extend-servers/select-agent.png)

1. **한 번 허용** > **실행** > **열기**를 선택합니다.
1. PowerShell 창을 닫지 않았다면 닫습니다.
1. **스토리지 동기화 에이전트 설치 마법사**에서 기본값을 수락합니다.
1. **설치**를 선택합니다.
1. **마침**을 선택합니다.

Azure 동기화 서비스를 배포하고 Windows Server 2016 Datacenter VM에 에이전트를 설치했습니다. 이제 스토리지 동기화 서비스에 VM을 등록해야 합니다.

## <a name="register-windows-server"></a>Windows Server 등록

스토리지 동기화 서비스에 Windows Server를 등록하면 서버(또는 클러스터)와 스토리지 동기화 서비스 간에 트러스트 관계가 설정됩니다. 서버는 하나의 스토리지 동기화 서비스에만 등록할 수 있습니다. 다른 서버 및 해당 스토리지 동기화 서비스와 연결된 Azure 파일 공유와 동기화할 수 있습니다.

Azure 파일 동기화 에이전트를 설치한 후 서버 등록 UI가 자동으로 열립니다. 그렇지 않으면 다음 파일 위치에서 수동으로 열 수 있습니다. `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. VM에 서버 등록 UI가 열리면 **확인**을 선택합니다.
1. **로그인**을 선택하여 시작합니다.
1. Azure 계정 자격 증명으로 로그인하고 **로그인**을 선택합니다.
1. 다음 정보를 지정합니다.

   ![서버 등록 UI 스크린샷](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | 값 | Description |
   | **Azure 구독** | 이 자습서에 대한 스토리지 동기화 서비스가 포함된 구독입니다. |
   | **리소스 그룹** | 스토리지 동기화 서비스를 포함하는 리소스 그룹입니다. 이 자습서의 경우 _afsresgroup101918_을 사용합니다. |
   | **스토리지 동기화 서비스** | 스토리지 동기화 서비스의 이름입니다. 이 자습서의 경우 _afssyncservice02_를 사용합니다. |

1. **등록**을 선택하여 서버 등록을 완료합니다.
1. 등록 프로세스의 일부로 추가 로그인을 요구하는 메시지가 표시됩니다. 로그인하고 **다음**을 선택합니다.
1. **확인**을 선택합니다.

## <a name="create-a-sync-group"></a>동기화 그룹 만들기

동기화 그룹은 파일 집합에 대한 동기화 토폴로지를 정의합니다. 동기화 그룹은 Azure 파일 공유를 나타내는 하나의 클라우드 엔드포인트를 포함해야 합니다. 또한 동기화 그룹은 하나 이상의 서버 엔드포인트를 포함해야 합니다. 서버 엔드포인트는 등록된 서버의 경로를 나타냅니다. 동기화 그룹을 만들려면:

1. [Azure Portal](https://portal.azure.com/)의 스토리지 동기화 서비스에서 **+ 동기화 그룹**을 선택합니다. 이 자습서의 경우 *afssyncservice02*를 사용합니다.

   ![Azure Portal에서 새 동기화 그룹 만들기](media/storage-sync-files-extend-servers/add-sync-group.png)

1. 다음 정보를 입력하여 클라우드 엔드포인트가 있는 동기화 그룹을 만듭니다.

   | 값 | Description |
   | ----- | ----- |
   | **동기화 그룹 이름** | 이 이름은 스토리지 동기화 서비스 내에서 고유해야 하지만 사용자에게 논리적인 어떤 이름도 될 수 있습니다. 이 자습서의 경우 *afssyncgroup*을 사용합니다.|
   | **구독** | 이 자습서에 대해 스토리지 동기화 서비스를 배포한 구독입니다. |
   | **스토리지 계정** | **스토리지 계정 선택**을 선택합니다. 표시되는 창에서 사용자가 만든 Azure 파일 공유가 있는 스토리지 계정을 선택합니다. 이 자습서의 경우 *afsstoracct101918*을 사용합니다. |
   | **Azure 파일 공유** | 사용자가 만든 Azure 파일 공유의 이름입니다. 이 자습서의 경우 *afsfileshare*를 사용합니다. |

1. **만들기**를 선택합니다.

동기화 그룹을 선택할 경우 이제 하나의 **클라우드 엔드포인트**가 표시될 것입니다.

## <a name="add-a-server-endpoint"></a>서버 엔드포인트 추가

서버 엔드포인트는 등록된 서버의 특정 위치를 나타냅니다. 예를 들어 서버 볼륨의 폴더입니다. 서버 엔드포인트를 추가하려면:

1. 새로 만든 동기화 그룹을 선택한 다음, **서버 엔드포인트 추가**를 선택합니다.

   ![동기화 그룹 창에서 새 서버 엔드포인트 추가](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. **서버 엔드포인트 추가** 창에 다음 정보를 입력하여 서버 엔드포인트를 만듭니다.

   | | |
   | ----- | ----- |
   | 값 | Description |
   | **등록된 서버** | 사용자가 만든 서버의 이름입니다. 이 자습서의 경우 *afsvm101918*을 사용합니다. |
   | **Path** | 사용자가 만든 드라이브에 대한 Windows Server 경로입니다. 이 자습서의 경우 *f:\filestosync*를 사용합니다. |
   | **클라우드 계층화** | 이 자습서에 대해 사용하지 않는 상태로 둡니다. |
   | **사용 가능한 볼륨 공간** | 이 자습서에 대해 비워 둡니다. |

1. **만들기**를 선택합니다.

이제 Azure 파일 공유 및 Windows Server에서 파일이 동기화 상태입니다.

![성공적으로 동기화된 Azure Storage](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 파일 동기화를 사용하여 Windows 서버의 스토리지 용량을 확장하기 위한 기본 단계를 학습했습니다. Azure 파일 동기화 배포를 위한 계획을 더 상세히 살펴보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Azure 파일 동기화 배포 계획](./storage-sync-files-planning.md)
