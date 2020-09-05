---
title: Windows에서 Azure 파일 공유 사용 | Microsoft Docs
description: Windows 및 Windows Server에서 Azure 파일 공유를 사용 하는 방법에 대해 알아봅니다. 온-프레미스 또는 Azure Vm에서 실행 되는 Windows 설치에서 SMB 3.0에 Azure 파일 공유를 사용 합니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 485a054a91bd3db9b116cf80a4e457d013c20ea1
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89071077"
---
# <a name="use-an-azure-file-share-with-windows"></a>Windows에서 Azure 파일 공유 사용
[Azure Files](storage-files-introduction.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 Windows 및 Windows Server에서 매끄럽게 사용할 수 있습니다. 이 문서에서는 Windows 및 Windows Server에서 Azure 파일 공유를 사용할 때의 고려 사항을 설명합니다.

온-프레미스 또는 다른 Azure 지역처럼 호스팅되는 Azure 지역 외부에서 Azure 파일 공유를 사용하려면 OS가 SMB 3.0을 지원해야 합니다. 

Azure VM 또는 온-프레미스에서 실행되는 Windows에서 Azure 파일 공유를 사용할 수 있습니다. 다음 표는 각 환경에서 파일 공유를 지원하는 OS 버전을 보여줍니다.

| Windows 버전        | SMB 버전 | Azure VM에 탑재 가능 | 온-프레미스에 탑재 가능 |
|------------------------|-------------|-----------------------|-----------------------|
| Windows Server 2019 | SMB 3.0 | 예 | 예 |
| Windows 10<sup>1</sup> | SMB 3.0 | 예 | 예 |
| Windows Server 반기 채널<sup>2</sup> | SMB 3.0 | 예 | 예 |
| Windows Server 2016 | SMB 3.0 | 예 | 예 |
| Windows 8.1 | SMB 3.0 | 예 | 예 |
| Windows Server 2012 R2 | SMB 3.0 | 예 | 예 |
| Windows Server 2012 | SMB 3.0 | 예 | 예 |
| Windows 7<sup>3</sup> | SMB 2.1 | 예 | 예 |
| Windows Server 2008 R2<sup>3</sup> | SMB 2.1 | 예 | 예 |

<sup>1</sup>Windows 10, 버전 1507, 1607, 1709, 1803, 1809, 1903 및 1909  
<sup>2</sup>Windows Server, 버전 1809, 1903 및 1909  
<sup>3</sup>Windows 7 및 Windows Server 2008 R2에 대한 정식 Microsoft 지원이 종료되었습니다. [ESU(연장 보안 업데이트) 프로그램](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates)을 통해서만 보안 업데이트에 대한 추가 지원을 구매할 수 있습니다. 이와 같은 운영 체제에서 마이그레이션하는 것이 좋습니다.

> [!Note]  
> 사용자의 Windows 버전에 대해 가장 최근의 KB를 선택하는 것이 좋습니다.

## <a name="prerequisites"></a>사전 요구 사항 

445 포트가 열려 있는지 확인: SMB 프로토콜은 TCP 포트 445가 열려 있어야 하며, 445 포트가 닫혀 있으면 연결이 실패합니다. 방화벽에서 cmdlet을 사용 하 여 포트 445을 차단 하 고 있는지 확인할 수 있습니다 `Test-NetConnection` . 차단 된 445 포트를 해결 하는 방법에 대 한 자세한 내용은 Windows 문제 해결 가이드의 [원인 1: 포트 445이 차단 되었습니다](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) . 섹션을 참조 하세요.

## <a name="using-an-azure-file-share-with-windows"></a>Windows에서 Azure 파일 공유 사용
Windows에서 Azure 파일 공유를 사용하려면 Azure 파일 공유를 탑재하거나(드라이브 문자 또는 탑재 지점 경로에 할당한다는 의미) [UNC 경로](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)를 통해 액세스해야 합니다. 

이 문서에서는 스토리지 계정 키를 사용하여 파일 공유에 액세스합니다. 스토리지 계정 키는 액세스하는 파일 공유 내 모든 파일 및 폴더에 대한 관리자 권한이 포함된 스토리지 계정의 관리 키이며, 스토리지 계정에 포함된 모든 파일 공유 및 다른 스토리지 리소스(Blob, 큐, 테이블 등)의 관리 키입니다. 스토리지 계정 키가 워크로드에 충분하지 않은 경우 [Azure 파일 동기화](storage-sync-files-planning.md)를 사용하거나 [SMB에 대한 ID 기반 인증](storage-files-active-directory-overview.md)을 사용할 수 있습니다.

SMB 파일 공유를 기대하는 LOB 애플리케이션을 Azure로 전환하는 일반적인 패턴은 Azure VM에서 전용 Windows 파일 서버를 실행하는 대신 Azure 파일 공유를 사용하는 것입니다. Azure 파일 공유를 사용하도록 기간 업무 앱을 마이그레이션할 때 고려해야 하는 중요한 사항 중 하나로, 많은 기간 업무 앱은 VM 관리 계정이 아니라 시스템 권한이 제한된 전용 서비스 계정 하에서 실행됩니다. 따라서 관리 계정이 아닌 서비스 계정의 Azure 파일 공유에 대한 자격 증명을 탑재/저장해야 합니다.

### <a name="mount-the-azure-file-share"></a>Azure 파일 공유 탑재

Azure Portal은 파일 공유를 호스트에 직접 탑재 하는 데 사용할 수 있는 스크립트를 제공 합니다. 제공 된 스크립트를 사용 하는 것이 좋습니다.

다음 스크립트를 가져오려면

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 탑재 하려는 파일 공유가 포함 된 저장소 계정으로 이동 합니다.
1. **파일 공유**를 선택합니다.
1. 탑재할 파일 공유를 선택 합니다.

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="예 들어":::

1. **연결**을 선택합니다.

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="파일 공유에 대 한 연결 아이콘의 스크린샷":::

1. 공유를 탑재할 드라이브 문자를 선택 합니다.
1. 제공 된 스크립트를 복사 합니다.

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="예제 텍스트":::

1. 파일 공유를 탑재 하려는 호스트의 셸에 스크립트를 붙여넣고 실행 합니다.

이제 Azure 파일 공유를 탑재 했습니다.

### <a name="mount-the-azure-file-share-with-file-explorer"></a>파일 탐색기를 통해 Azure 파일 공유 탑재
> [!Note]  
> 다음 지침은 Windows 10에서 보여 주는 것이며, 이전 릴리스와 다를 수 있습니다. 

1. 파일 탐색기를 엽니다. [시작] 메뉴에서 열거나 Win+E 바로 가기 키를 눌러서 열 수 있습니다.

1. 창의 왼쪽에 있는 **이 PC** 로 이동 합니다. 이렇게 하면 리본에서 사용할 수 있는 메뉴가 변경됩니다. [컴퓨터] 메뉴 아래에서 **네트워크 드라이브 연결**을 선택합니다.
    
    !["네트워크 드라이브 연결" 드롭다운 메뉴의 스크린샷](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. 드라이브 문자를 선택하고 UNC 경로를 입력합니다. UNC 경로 형식은 `\\<storageAccountName>.file.core.windows.net\<fileShareName>`입니다. 예: `\\anexampleaccountname.file.core.windows.net\example-share-name`
    
    !["네트워크 드라이브 연결" 대화 상자의 스크린샷](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. 사용자 이름으로 `AZURE\`이(가) 앞에 붙은 Storage 계정 이름을 사용하고, 암호로 Storage 계정 키를 사용합니다.
    
    ![네트워크 자격 증명 대화 상자의 스크린샷](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Azure 파일 공유를 원하는 대로 사용합니다.
    
    ![현재 탑재된 Azure 파일 공유](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Azure 파일 공유를 분리할 준비가 되면 파일 탐색기의 **네트워크 위치** 아래에서 공유 항목을 마우스 오른쪽 단추로 클릭하고 **연결 해제**를 선택하여 Azure 파일 공유를 탑재 해제할 수 있습니다.

### <a name="accessing-share-snapshots-from-windows"></a>Windows에서 공유 스냅샷에 액세스
Azure Backup 같은 스크립트 또는 서비스를 통해 수동으로 또는 자동으로 공유 스냅샷을 만든 경우 Windows의 파일 공유에서 이전 버전의 공유, 디렉터리 또는 특정 파일을 볼 수 있습니다. [Azure PowerShell](storage-how-to-use-files-powershell.md), [Azure CLI](storage-how-to-use-files-cli.md)또는 [Azure Portal](storage-how-to-use-files-portal.md)를 사용 하 여 공유 스냅숏을 만들 수 있습니다.

#### <a name="list-previous-versions"></a>이전 버전 나열
복원해야 하는 항목 또는 부모 항목을 찾습니다. 해당 항목을 두 번 클릭하여 원하는 디렉터리로 이동합니다. 마우스 오른쪽 단추로 클릭하고 메뉴에서 **속성**을 선택합니다.

![선택한 디렉터리를 마우스 오른쪽 단추로 클릭하여 표시된 메뉴](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

**이전 버전**을 선택하여 이 디렉터리에 대한 공유 스냅샷의 목록을 봅니다. 네트워크 속도 및 디렉터리의 공유 스냅샷 수에 따라 목록이 표시되는 데 수 초 정도 걸릴 수 있습니다.

![이전 버전 탭](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

**열기**를 선택하여 특정 스냅샷을 열 수 있습니다. 

![열린 스냅샷](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>이전 버전에서 복원
**복원**을 선택하여 공유 스냅샷을 만들 때의 전체 디렉터리의 내용을 원래 위치에 재귀적으로 복사합니다.

 ![경고 메시지의 복원 단추](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>Windows/Windows Server 보안
Windows에서 Azure 파일 공유를 탑재하려면 포트 445에 액세스할 수 있어야 합니다. 많은 조직에서 SMB 1에 내재된 보안 위험 때문에 포트 445를 차단합니다. CIFS(Common Internet File System)라고도 하는 SMB 1은 Windows 및 Windows Server에 포함된 레거시 파일 시스템 프로토콜입니다. SMB 1은 구식 프로토콜로 비효율적이며 무엇보다도 보안성이 떨어집니다. 좋은 소식은, Azure Files는 SMB 1을 지원하지 않습니다. 그리고 모든 지원되는 Windows 및 Windows Server 버전은 SMB 1을 제거하거나 사용하지 않도록 설정할 수 있습니다. 프로덕션 환경에서 Azure 파일 공유를 사용하기 전에 항상 SMB 1 클라이언트 및 서버를 Windows에서 제거하거나 사용하지 않도록 설정할 것을 [강력히 권장](https://aka.ms/stopusingsmb1)합니다.

다음 표에서는 각 Windows 버전의 SMB 1 상태에 대한 자세한 정보를 제공합니다.

| Windows 버전                           | SMB 1 기본 상태 | 해제/제거 방법       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019                       | 사용 안 함             | Windows 기능을 사용하여 제거 |
| Windows Server 버전 1709+            | 사용 안 함             | Windows 기능을 사용하여 제거 |
| Windows 10 버전 1709+                | 사용 안 함             | Windows 기능을 사용하여 제거 |
| Windows Server 2016                       | 사용              | Windows 기능을 사용하여 제거 |
| Windows 10 버전 1507, 1607, 1703 | 사용              | Windows 기능을 사용하여 제거 |
| Windows Server 2012 R2                    | 사용              | Windows 기능을 사용하여 제거 | 
| Windows 8.1                               | 사용              | Windows 기능을 사용하여 제거 | 
| Windows Server 2012                       | 사용              | 레지스트리를 사용하여 해제       | 
| Windows Server 2008 R2                    | 사용              | 레지스트리를 사용하여 해제       |
| Windows 7                                 | 사용              | 레지스트리를 사용하여 해제       | 

### <a name="auditing-smb-1-usage"></a>SMB 1 사용 감사
> Windows Server 2019, Windows Server 반기 채널(버전 1709 및 1803), Windows Server 2016, Windows 10(버전 1507, 1607, 1703, 1709 및 1803), Windows Server 2012 R2, Windows 8.1에 적용

환경에서 SMB 1을 제거하기 전에, SMB 1 사용을 감사하여 변경 때문에 손상되는 클라이언트가 있는지 확인할 수 있습니다. SMB 1을 사용하여 SMB 공유에 대한 요청이 만들어지는 경우 `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit` 아래의 이벤트 로그에 감사 이벤트가 기록됩니다. 

> [!Note]  
> Windows Server 2012 R2 및 Windows 8.1에서 감사를 지원하려면 [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720) 이상을 설치해야 합니다.

감사를 사용하려면 관리자 권한 PowerShell 세션에서 다음 cmdlet을 실행합니다.

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Windows Server에서 SMB 1 제거
> Windows Server 2019, Windows Server 반기 채널(버전 1709 및 1803), Windows Server 2016, Windows Server 2012 R2에 적용

Windows Server 인스턴스에서 SMB 1을 제거하려면 관리자 권한 PowerShell 세션에서 다음 cmdlet을 실행합니다.

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

제거 프로세스를 완료하려면 서버를 다시 시작합니다. 

> [!Note]  
> Windows 10 및 Windows Server 버전 1709부터는 SMB 1이 기본적으로 설치되지 않으며 SMB 1 클라이언트 및 SMB 1 서버에 대한 별도의 Windows 기능이 있습니다. 두 SMB 1 서버(`FS-SMB1-SERVER`) 및 SMB 1 클라이언트(`FS-SMB1-CLIENT`)를 설치하지 않는 것이 좋습니다.

### <a name="removing-smb-1-from-windows-client"></a>Windows 클라이언트에서 SMB 1 제거
> Windows 10(버전 1507, 1607, 1703, 1709, 1803) 및 Windows 8.1에 적용

Windows 클라이언트에서 SMB 1을 제거하려면 관리자 권한 PowerShell 세션에서 다음 cmdlet을 실행합니다.

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

제거 프로세스를 완료하려면 PC를 다시 시작합니다.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>레거시 버전의 Windows/Windows Server에서 SMB 1을 사용하지 않도록 설정
> Windows Server 2012, Windows Server 2008 R2, Windows 7에 적용

레거시 버전의 Windows/Windows Server에서 SMB 1을 완전히 제거할 수는 없지만 레지스트리를 통해 사용하지 않도록 설정할 수 있습니다. SMB 1을 사용하지 않도록 설정하려면 `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters` 아래에서 값이 `0`이고 유형이 `SMB1`인 새 레지스트리 키 `DWORD`을(를) 만듭니다.

다음 PowerShell cmdlet을 사용하면 쉽게 처리할 수 있습니다.

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

이 레지스트리 키를 만든 후 서버를 다시 시작해야만 SMB 1이 사용하지 않도록 설정됩니다.

### <a name="smb-resources"></a>SMB 리소스
- [SMB 1 사용 중지](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)
- [SMB 1 제품 클리어링 하우스](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)
- [DSCEA를 사용하여 환경의 SMB 1 검색](https://blogs.technet.microsoft.com/ralphkyttle/2017/04/07/discover-smb1-in-your-environment-with-dscea/)
- [그룹 정책을 통해 SMB 1을 사용하지 않도록 설정](https://blogs.technet.microsoft.com/secguide/2017/06/15/disabling-smbv1-through-group-policy/)

## <a name="next-steps"></a>다음 단계
Azure Files에 대한 자세한 내용은 다음 링크를 참조하세요.
- [Azure 파일 배포에 대한 계획](storage-files-planning.md)
- [FAQ](../storage-files-faq.md)
- [Windows에서 문제 해결](storage-troubleshoot-windows-file-connection-problems.md)      
