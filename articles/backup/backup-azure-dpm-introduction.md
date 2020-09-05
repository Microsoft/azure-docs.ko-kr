---
title: 작업을 백업 하도록 DPM 서버 준비
description: 이 문서에서는 Azure Backup 서비스를 사용 하 여 Azure에 대 한 System Center Data Protection Manager (DPM) 백업을 준비 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: 4534b75018fb91cedda00b8c1167012858c5e562
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021037"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>System Center DPM을 사용하여 Azure에 워크로드를 백업하도록 준비

이 문서에서는 Azure Backup 서비스를 사용하여 Azure에 대한 System Center DPM(Data Protection Manager) 백업을 준비하는 방법을 설명합니다.

이 문서는 다음을 제공합니다.

- Azure Backup을 사용한 DPM 배포 개요
- DPM에서 Azure Backup을 사용할 때의 필수 구성 요소 및 제한 사항
- Recovery Services 백업 자격 증명 모음 설정과 자격 증명 모음에 대한 Azure Storage 유형의 선택적 수정을 비롯한 Azure 준비 단계
- 자격 증명 모음의 자격 증명 다운로드, Azure Backup 에이전트 설치 및 자격 증명 모음에 DPM 서버를 비롯한 DPM 서버 준비 단계
- 일반 오류에 대한 문제 해결 팁

## <a name="why-back-up-dpm-to-azure"></a>DPM을 Azure에 백업하는 이유?

[System CENTER DPM](/system-center/dpm/dpm-overview) 은 파일 및 응용 프로그램 데이터를 백업 합니다. DPM 은 Azure Backup과 다음과 같이 상호작용합니다.

- **물리적 서버 또는 온-프레미스 VM에서 실행 되는 DPM** -디스크 및 테이프 백업 외에도 Azure에서 백업 자격 증명 모음에 데이터를 백업할 수 있습니다.
- **AZURE vm에서 실행 되는 dpm** -System Center 2012 R2 업데이트 3 이상에서 Dpm을 azure vm에 배포할 수 있습니다. VM에 연결된 Azure 디스크에 데이터를 백업할 수도 있고 Azure Backup을 사용하여 데이터를 백업 자격 증명 모음에 백업할 수도 있습니다.

DPM 서버를 Azure에 백업하는 경우 업무상 이점은 다음과 같습니다.

- 온-프레미스 DPM의 경우, 테이프로 장기 배포하는 대신 Azure Backup을 사용할 수 있습니다.
- Azure VM에서 실행되는 DPM의 경우 Azure Backup를 사용하면 Azure 디스크에서 스토리지를 오프로드할 수 있습니다. 오래된 데이터를 백업 자격 증명 모음에 저장하면 새로운 데이터를 디스크에 저장하여 비즈니스를 강화할 수 있습니다.

## <a name="prerequisites-and-limitations"></a>필수 구성 요소 및 제한 사항

**설정** | **요구 사항**
--- | ---
Azure VM의 DPM | System Center 2012 R2 DPM 2012 R2 업데이트 롤업 3 이상
물리적 서버의 DPM | System Center 2012 SP1 이상, System Center 2012 R2
Hyper-V VM의 DPM | System Center 2012 SP1 이상, System Center 2012 R2
VMware VM의 DPM | System Center 2012 R2 업데이트 롤업 5 이상
구성 요소 | DPM 서버에는 Windows PowerShell 및 .NET Framework 4.5가 설치 되어 있어야 합니다.
지원되는 앱 | DPM으로 백업할 수 대상을 [알아봅니다](/system-center/dpm/dpm-protection-matrix).
지원되는 파일 형식 | Azure Backup으로 백업할 수 있는 파일 형식은<br> <li>암호화 (전체 백업만 해당)<li> 압축 (증분 백업 지원) <li> 스파스 (증분 백업 지원)<li> 압축 및 스파스 (스파스로 처리 됨)
지원되지 않는 파일 형식 | <li>대/소문자를 구분 하는 파일 시스템의 서버<li> 하드 링크 (건너뜀)<li> 재분석 지점의 (건너뜀)<li> 암호화 및 압축 (건너뜀)<li> 암호화 및 스파스 (건너뜀)<li> 압축된 스트림<li> 스트림 구문 분석
로컬 스토리지 | 백업 하려는 각 컴퓨터에는 백업 중인 데이터 크기의 5% 이상이 되는 로컬 사용 가능한 저장소가 있어야 합니다. 예를 들어 100GB 데이터를 백업하는 경우 스크래치 위치에 최소 5GB의 여유 공간이 필요합니다.
자격 증명 모음 스토리지 | Azure Backup 자격 증명 모음에 백업할 수 있는 데이터 양에는 제한이 없지만 데이터 원본 (예: 가상 머신 또는 데이터베이스)의 크기는 54400 GB를 초과할 수 없습니다.
Azure ExpressRoute | 공용 피어 링 (이전 회로에 사용 가능) 및 Microsoft 피어 링을 사용 하 여 Azure Express 경로를 통해 데이터를 백업할 수 있습니다. 개인 피어 링에 대 한 백업은 지원 되지 않습니다.<br/><br/> **공용 피어 링 사용**: 다음 도메인/주소에 대 한 액세스를 확인 합니다.<br/><br/>- `http://www.msftncsi.com/ncsi.txt` <br/><br/>- `microsoft.com` <br/><br/>-`.WindowsAzure.com`<br/><br/>-`.microsoftonline.com`<br/><br/>-`.windows.net`<br/><br/> **Microsoft 피어 링을 사용 하 여**다음 서비스/지역 및 관련 커뮤니티 값을 선택 합니다.<br/><br/>-Azure Active Directory (12076:5060)<br/><br/>-Microsoft Azure 지역 (Recovery Services 자격 증명 모음의 위치에 따라)<br/><br/>-Azure Storage (Recovery Services 자격 증명 모음의 위치에 따라)<br/><br/>자세한 내용은 [express 경로 라우팅 요구 사항](../expressroute/expressroute-routing.md)을 참조 하세요.<br/><br/>**참고**: 공용 피어 링은 새 회로에서 사용 되지 않습니다.
Azure Backup 에이전트 | DPM이 System Center 2012 SP1에서 실행 중인 경우 DPM SP1에 대한 롤업 2 이상을 설치합니다. 이 롤업은 에이전트 설치를 위해 반드시 필요합니다.<br/><br/> 이 문서에서는 MARS(icrosoft Azure Recovery Service) 에이전트라고도 하는 최신 버전의 Azure Backup 에이전트를 배포하는 방법을 설명합니다. 이전 버전을 배포한 경우 최신 버전으로 업데이트하여 백업이 예상대로 작동하도록 합니다.

시작하기 전에 Azure Backup 기능을 사용할 수 있는 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. [Azure Backup 가격 정책](https://azure.microsoft.com/pricing/details/backup/)을 읽어보십시오.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>스토리지 설정 수정

지역 중복 스토리지와 로컬 중복 스토리지 중에서 선택할 수 있습니다.

- 기본적으로 사용자 자격 증명 모음에는 지역 중복 스토리지가 있습니다.
- 자격 증명 모음이 기본 백업인 경우 지역 중복 스토리지 옵션이 설정된 상태로 둡니다. 오래 지속되지 않는 저렴한 옵션을 원하는 경우에는 다음 절차에 따라 로컬 중복 스토리지를 구성합니다.
- [Azure Storage](../storage/common/storage-redundancy.md)와 [지역 중복](../storage/common/storage-redundancy.md) 및 [로컬 중복](../storage/common/storage-redundancy.md) 스토리지 옵션에 대해 알아봅니다.
- 초기 백업 전에 스토리지 설정을 수정합니다. 항목을 이미 백업한 경우 스토리지 설정을 수정하기 전에 자격 증명 모음에 백업하는 작업을 중지합니다.

스토리지 복제 설정을 편집하려면

1. 자격 증명 모음 대시보드를 엽니다.

2. **관리**에서 **백업 인프라**를 선택 합니다.

3. **백업 구성** 메뉴에서 자격 증명 모음에 대한 스토리지 옵션을 선택합니다.

    ![백업 자격 증명 모음 목록](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>저장소 자격 증명 다운로드

Vault에서 DPM 서버를 등록하는 경우 자격 증명 모음의 자격 증명을 사용합니다.

- 자격 증명 모음 자격 증명 파일은 포털에서 각 백업 자격 증명 모음에 대해 생성하는 인증서입니다.
- 그런 다음 포털은 Access Control Service(ACS)에 공개 키를 업로드합니다.
- 머신 등록 워크플로 중에 머신을 인증하는 인증서의 프라이빗 키가 사용자에게 제공됩니다.
- 인증에 따라 Azure Backup 서비스는 식별된 자격 증명 모음으로 데이터를 전송합니다.

### <a name="best-practices-for-vault-credentials"></a>자격 증명 모음의 자격 증명에 대한 모범 사례

자격 증명을 얻으려면 Azure Portal에서 보안 채널을 통해 자격 증명 모음 자격 증명 파일을 다운로드 합니다.

- 자격 증명 모음의 자격 증명은 등록 워크플로 중에만 사용됩니다.
- 자격 증명 모음의 자격 증명 파일이 안전하고 손상되지 않도록 하는 것은 사용자의 책임입니다.
  - 자격 증명을 제어할 수 없으면 보관 자격 증명을 사용하여 다른 머신을 자격 증명 모음에 등록할 수 있습니다.
  - 그러나 백업 데이터는 사용자에 게 속한 암호를 사용 하 여 암호화 되므로 기존 백업 데이터를 손상 시킬 수 없습니다.
- 파일이 DPM 서버에서 액세스할 수 있는 위치에 저장 되어 있는지 확인 합니다. 파일 공유/s s s에 저장 된 경우 액세스 권한이 있는지 확인 합니다.
- 자격 증명 모음 자격 증명은 48시간이 지나면 만료됩니다. 필요에 따라 새로운 자격 증명 모음의 자격 증명을 여러 번 다운로드할 수 있습니다. 그러나 등록 워크플로 중에 최신 보관 자격 증명 파일만 사용할 수 있습니다.
- Azure Backup 서비스는 인증서의 프라이빗 키를 인식하지 않으며 프라이빗 키는 포털 또는 서비스에서 사용할 수 없습니다.

다음과 같이 자격 증명 모음의 자격 증명 파일을 로컬 컴퓨터에 다운로드합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. DPM 서버를 등록하려는 자격 증명 모음을 엽니다.
3. **설정**에서 **속성**을 선택 합니다.

    ![자격 증명 모음 메뉴 열기](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. **속성**  >  **백업 자격 증명**에서 **다운로드**를 선택 합니다. 포털은 자격 증명 모음 이름과 현재 날짜를 조합하여 자격 증명 모음의 자격 증명 파일을 생성하고 다운로드할 수 있도록 제공합니다.

    ![자격 증명 다운로드](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. **저장** 을 선택 하 여 자격 증명 모음 자격 증명을 폴더에 다운로드 하거나 다른 **이름으로 저장** 을 선택 하 여 위치를 지정 합니다. 파일이 생성되기 까지 최대 1분이 소요됩니다.

## <a name="install-the-backup-agent"></a>Backup 에이전트 설치

Azure Backup으로 백업되는 모든 머신에는 MARS(Microsoft Azure Recovery Service) 에이전트라고도 하는 Backup 에이전트가 설치되어 있어야 합니다. 다음과 같이 DPM 서버에 에이전트를 설치합니다.

1. DPM 서버를 등록하려는 자격 증명 모음을 엽니다.
2. **설정**에서 **속성**을 선택 합니다.

    ![자격 증명 모음 설정 열기](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. **속성** 페이지에서 Azure Backup 에이전트를 다운로드합니다.

    ![다운로드](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. 다운로드한 후 MARSAgentInstaller.exe를 실행하여 DPM 컴퓨터에 에이전트를 설치합니다.
5. 에이전트의 설치 폴더 및 캐시 폴더를 선택합니다. 캐시 위치의 사용 가능한 공간이 백업 데이터의 5% 이상이어야 합니다.
6. 프록시 서버를 사용하여 인터넷에 연결하는 경우 **프록시 구성** 화면에서 프록시 서버 세부 정보를 입력합니다. 인증된 프록시를 사용하는 경우 이 화면에 사용자 이름 및 암호 세부 정보를 입력합니다.
7. Azure Backup 에이전트가 .NET Framework 4.5 및 Windows PowerShell(설치되지 않은 경우)을 설치하여 설치를 완료합니다.
8. 에이전트가 설치되면 창을 **닫습니다**.

    ![닫기](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>자격 증명 모음에 VMM 서버 등록

1. DPM 관리자 콘솔 > **관리**에서 **온라인**을 선택 합니다. **등록**을 선택합니다. 서버 등록 마법사가 열립니다.
2. **프록시 구성**에서 필요에 따라 프록시 설정을 지정합니다.

    ![프록시 구성](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. **백업 자격 증명 모음**에서 다운로드한 자격 증명 모음의 자격 증명 파일을 찾아 선택합니다.

    ![자격 증명 모음 자격 증명](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. **제한 설정**에서 백업에 대한 대역폭 제한을 선택적으로 사용하도록 설정할 수 있습니다. 지정된 작업 기간(시간 및 일 수)에 대한 속도 제한을 설정할 수 있습니다.

    ![제한 설정](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. **복구 폴더 설정**에서 데이터 복구 동안 사용할 수 있는 위치를 지정합니다.

    - Azure Backup은 복구된 데이터에 대한 임시 보유 영역으로 이 위치를 사용합니다.
    - 데이터 복구가 완료된 후 Azure Backup은 이 영역의 데이터를 정리합니다.
    - 위치에는 동시에 복구 해야 하는 항목을 저장할 수 있는 충분 한 공간이 있어야 합니다.

    ![복구 폴더 설정](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. **암호화 설정**에서 암호를 생성 하거나 제공 합니다.

    - 암호는 클라우드에 대한 백업을 암호화하는 데 사용됩니다.
    - 최소 16자를 지정합니다.
    - 이 파일은 복구에 필요하므로 안전한 위치에 저장합니다.

    ![암호화](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > 암호화 암호를 소유 하 고 있으며 Microsoft는 이러한 암호를 볼 수 없습니다.
    > 암호를 분실 하거나 잊어버린 경우 Microsoft에서 백업 데이터를 복구 하는 데 도움을 줍니다.

7. **등록** 을 선택 하 여 DPM 서버를 자격 증명 모음에 등록 합니다.

서버를 자격 증명 모음에 성공적으로 등록 한 후에는 Microsoft Azure에 대 한 백업을 시작할 준비가 완료 된 것입니다. Azure에 작업을 백업 하도록 DPM 콘솔에서 보호 그룹을 구성 해야 합니다. 보호 그룹을 배포 하 [는 방법을 알아봅니다](/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-2019) .

## <a name="troubleshoot-vault-credentials"></a>자격 증명 모음의 자격 증명 문제 해결

### <a name="expiration-error"></a>만료 오류

자격 증명 모음 자격 증명 파일은 포털에서 다운로드 한 후 48 시간 동안만 유효 합니다. 이 화면에서 오류가 발생 하는 경우 (예: "제공 된 자격 증명 모음 자격 증명 파일이 만료 되었습니다") Azure Portal에 로그인 하 고 자격 증명 모음 자격 증명 파일을 다시 다운로드 합니다.

### <a name="access-error"></a>액세스 오류

설치 응용 프로그램에서 액세스할 수 있는 위치에서 자격 증명 모음 자격 증명 파일을 사용할 수 있는지 확인 합니다. 액세스 관련 오류가 발생할 경우 이 컴퓨터의 임시 위치에 자격 증명 모음 자격 증명 파일을 복사하고 작업을 다시 시도합니다.

### <a name="invalid-credentials-error"></a>잘못된 자격 증명 오류

잘못 된 자격 증명 모음 자격 증명 오류 (예: "잘못 된 자격 증명 모음 자격 증명을 제공 했습니다.")가 발생 한 경우 파일이 손상 되었거나 복구 서비스와 연결 된 최신 자격 증명이 없습니다.

- 포털에서 새 자격 증명 모음 자격 증명 파일을 다운로드한 후에 작업을 다시 시도합니다.
- 이 오류는 일반적으로 Azure Portal에서 자격 증명 **모음 다운로드** 옵션을 선택 하는 경우에 나타납니다. 이 경우 두 번째 자격 증명 모음 자격 증명 파일만 유효합니다.
