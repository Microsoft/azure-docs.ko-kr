---
title: Azure Backup 에이전트 문제 해결
description: 이 문서에서는 Azure Backup 에이전트의 설치 및 등록 문제를 해결 하는 방법에 대해 알아봅니다.
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: 86f9dfd8554e2560ea16a85c3551dba3f7dc01f1
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019626"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>MARS (Microsoft Azure Recovery Services) 에이전트 문제 해결

이 문서에서는 구성, 등록, 백업 및 복원 중에 표시 될 수 있는 오류를 해결 하는 방법을 설명 합니다.

## <a name="basic-troubleshooting"></a>기본 문제 해결

Microsoft Azure Recovery Services (MARS) 에이전트 문제 해결을 시작 하기 전에 다음 사항을 확인 하는 것이 좋습니다.

- [MARS 에이전트가 최신 상태 인지 확인](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)합니다.
- [MARS 에이전트와 Azure 간에 네트워크 연결이 있는지 확인](#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)합니다.
- MARS가 실행 중인지 확인 합니다 (서비스 콘솔). 필요한 경우 작업을 다시 시작 하 고 작업을 다시 시도 하세요.
- [스크래치 폴더 위치에서 5% ~ 10%의 사용 가능한 볼륨 공간을 사용할 수 있는지 확인](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)하십시오.
- [다른 프로세스 또는 바이러스 백신 소프트웨어가 Azure Backup 방해 하는지 확인](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)합니다.
- 백업 작업이 완료 되었지만 경고가 표시 되 면 [백업 작업이 완료 되었으나 경고가](#backup-jobs-completed-with-warning) 발생 합니다.
- 예약 된 백업이 실패 했지만 수동 백업이 작동 하는 경우에는 [예약에 따라 백업이 실행 되지 않습니다](#backups-dont-run-according-to-schedule).를 참조 하세요.
- OS에 최신 업데이트가 있는지 확인 합니다.
- 지원 되지 않는 [특성이 있는 지원 되지 않는 드라이브 및 파일이 백업에서 제외 되는지 확인](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)합니다.
- 보호 된 시스템의 시계가 올바른 표준 시간대로 구성 되어 있는지 확인 합니다.
- [4.5.2 이상 버전이 서버에 설치 되어 있는지 .NET Framework 확인](https://www.microsoft.com/download/details.aspx?id=30653)합니다.
- 자격 증명 모음에 서버를 등록 하려는 경우:
  - 에이전트가 서버에서 제거 되 고 포털에서 삭제 되었는지 확인 합니다.
  - 처음에 서버를 등록 하는 데 사용한 것과 동일한 암호를 사용 합니다.
- 오프 라인 백업의 경우 백업을 시작 하기 전에 원본 컴퓨터와 복사 컴퓨터 모두에 3.7.0이 설치 되어 있는지 Azure PowerShell 확인 합니다.
- 백업 에이전트가 Azure 가상 머신에서 실행 되는 경우 [이 문서](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)를 참조 하세요.

## <a name="invalid-vault-credentials-provided"></a>잘못된 자격 증명 모음이 제공되었습니다.

**오류 메시지**: 잘못 된 자격 증명 모음 자격 증명을 제공 했습니다. 파일이 손상되었거나 복구 서비스와 연결된 최신 자격 증명이 없습니다. (ID: 34513)

| 원인 | 권장 작업 |
| ---     | ---    |
| **자격 증명 모음 자격 증명이 유효 하지 않음** <br/> <br/> 자격 증명 모음 파일이 손상 되었거나 만료 되었을 수 있습니다. (예를 들어 등록 하는 동안 48 시간 이상 다운로드 되었을 수 있습니다.)| Azure Portal의 Recovery Services 자격 증명 모음에서 [새 자격 증명을 다운로드](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file) 합니다. 그런 후에 다음 단계를 적절 하 게 수행 합니다. <ul><li> MARS를 이미 설치 하 고 등록 한 경우 Microsoft Azure Backup 에이전트 MMC 콘솔을 엽니다. 그런 다음 **작업** 창에서 **서버 등록** 을 선택 하 여 새 자격 증명을 사용 하 여 등록을 완료 합니다. <br/> <li> 새 설치에 실패 하는 경우 새 자격 증명을 사용 하 여 다시 설치 해 봅니다.</ul> **참고**: 여러 자격 증명 모음 파일을 다운로드 한 경우 최신 파일만 다음 48 시간 동안 유효 합니다. 새 자격 증명 모음 자격 증명 파일을 다운로드 하는 것이 좋습니다.
| **프록시 서버/방화벽이 등록을 차단 하 고 있습니다.** <br/>또는 <br/>**인터넷 연결 없음** <br/><br/> 컴퓨터 또는 프록시 서버가 인터넷 연결을 제한 하 고 필요한 Url에 대 한 액세스를 보장 하지 않는 경우 등록에 실패 합니다.| 다음 단계를 수행하세요.<br/> <ul><li> IT 팀과 협력 하 여 시스템이 인터넷에 연결 되어 있는지 확인 합니다.<li> 프록시 서버가 없는 경우 에이전트를 등록할 때 프록시 옵션이 선택 되어 있지 않은지 확인 합니다. [프록시 설정을 확인](#verifying-proxy-settings-for-windows)하세요.<li> 방화벽/프록시 서버를 사용 하는 경우에는 네트워킹 팀과 협력 하 여 이러한 Url 및 IP 주소에 액세스할 수 있는지 확인 합니다.<br/> <br> **URL**<br> `www.msftncsi.com` <br> . Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP 주소**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>위의 문제 해결 단계를 완료 한 후 다시 등록 해 보세요.<br></br> Azure Express 경로를 통해 연결 하는 경우에는 [Azure express 경로 지원](backup-support-matrix-mars-agent.md#azure-expressroute-support)에 설명 된 대로 설정이 구성 되어 있는지 확인 합니다.
| **바이러스 백신 소프트웨어가 등록을 차단 하 고 있습니다.** | 서버에 바이러스 백신 소프트웨어가 설치 되어 있는 경우 다음과 같은 파일 및 폴더에 대 한 바이러스 백신 검사에 필요한 제외 규칙을 추가 합니다. <br/><ul> <li> CBengine.exe <li> CSC.exe<li> 스크래치 폴더입니다. 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> C:\Program Files\Microsoft Azure Recovery Services의 bin 폴더.

### <a name="additional-recommendations"></a>추가 권장 사항

- C:/Windows/Temp로 이동하여 확장명이 .tmp인 파일이 60,000 또는 65,000개 넘게 있는지 확인합니다. 있는 경우 해당 파일을 삭제합니다.
- 컴퓨터의 날짜와 시간이 현지 표준 시간대와 일치 하는지 확인 합니다.
- [이러한 사이트가](install-mars-agent.md#verify-internet-access) Internet Explorer의 신뢰할 수 있는 사이트에 추가 되었는지 확인 합니다.

### <a name="verifying-proxy-settings-for-windows"></a>Windows에 대 한 프록시 설정 확인

1. [Sysinternals](/sysinternals/downloads/psexec) 페이지에서 PsExec를 다운로드 합니다.
1. `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"`관리자 권한 명령 프롬프트에서를 실행 합니다.

   이 명령은 Internet Explorer를 엽니다.
1. **도구**  >  **인터넷 옵션**  >  **연결**  >  **LAN 설정**으로 이동 합니다.
1. 시스템 계정에 대 한 프록시 설정을 확인 합니다.
1. 프록시를 구성 하지 않고 프록시 정보를 제공 하는 경우 세부 정보를 제거 합니다.
1. 프록시가 구성 되어 있고 프록시 정보가 잘못 된 경우 **프록시 IP** 및 **포트** 세부 정보가 올바른지 확인 합니다.
1. Internet Explorer를 닫습니다.

## <a name="unable-to-download-vault-credential-file"></a>자격 증명 모음 파일을 다운로드할 수 없음

| 오류   | 권장 작업 |
| ---     | ---    |
|자격 증명 모음 파일을 다운로드하지 못했습니다. (ID: 403) | <ul><li> 다른 브라우저를 사용 하 여 자격 증명 모음을 다운로드 하거나 다음 단계를 수행 하세요. <ul><li> Internet Explorer를 시작합니다. F12를 선택 합니다. </li><li> **네트워크** 탭으로 이동 하 여 캐시 및 쿠키를 지웁니다. </li> <li> 페이지를 새로 고칩니다.<br></li></ul> <li> 구독이 사용 하지 않도록 설정 되었는지 여부를 확인 합니다.<br></li> <li> 방화벽 규칙에서 다운로드를 차단 하 고 있는지 확인 합니다. <br></li> <li> 자격 증명 모음 (자격 증명 모음 당 50 대)에 대 한 제한을 모두 사용 하지 않았는지 확인 합니다.<br></li>  <li> 사용자에 게 자격 증명 모음 자격 증명을 다운로드 하 고 자격 증명 모음에 서버를 등록 하는 데 필요한 Azure Backup 권한이 있는지 확인 합니다. [역할 기반 Access Control를 사용 하 여 Azure Backup 복구 지점의 관리를](backup-rbac-rs-vault.md)참조 하세요.</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure Recovery Services 에이전트에서 Microsoft Azure Backup에 연결할 수 없습니다.

| 오류  | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |
| <br /><ul><li>Microsoft Azure Recovery Service 에이전트에서 Microsoft Azure Backup에 연결할 수 없습니다. (ID: 100050) 네트워크 설정을 확인 하 고 인터넷에 연결할 수 있는지 확인 하세요.<li>(407) 프록시 인증 필요. |프록시가 연결을 차단 하 고 있습니다. |  <ul><li>Internet Explorer에서 **도구**  >  **인터넷 옵션**  >  **보안**  >  **인터넷**으로 이동 합니다. **사용자 지정 수준** 을 선택 하 고 **파일 다운로드** 섹션까지 아래로 스크롤합니다. **사용**을 선택합니다.<p>Internet Explorer에서 신뢰할 수 있는 사이트에 [url 및 IP 주소](install-mars-agent.md#verify-internet-access) 를 추가 해야 할 수도 있습니다.<li>프록시 서버를 사용하도록 설정을 변경합니다. 그런 다음 프록시 서버 세부 정보를 제공합니다.<li> 컴퓨터의 인터넷 액세스가 제한 된 경우 컴퓨터 또는 프록시의 방화벽 설정에서 이러한 [url 및 IP 주소](install-mars-agent.md#verify-internet-access)를 허용 하는지 확인 합니다. <li>서버에 바이러스 백신 소프트웨어가 설치 되어 있는 경우 다음 파일을 바이러스 백신 검사에서 제외 합니다. <ul><li>CBEngine.exe(dpmra.exe 대신)<li>CSC.exe(.NET Framework와 관련됨) 서버에 설치 된 모든 .NET Framework 버전에 대 한 CSC.exe 있습니다. 영향을 받는 서버에 있는 모든 버전의 .NET Framework에 대 한 CSC.exe 파일을 제외 합니다. <li>스크래치 폴더 또는 캐시 위치입니다. <br>스크래치 폴더 또는 캐시 경로에 대 한 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>C:\Program Files\Microsoft Azure Recovery Services의 bin 폴더.

## <a name="the-specified-vault-credential-file-cannot-be-used-as-it-is-not-downloaded-from-the-vault-associated-with-this-server"></a>지정 된 자격 증명 모음 자격 증명 파일은이 서버와 연결 된 자격 증명 모음에서 다운로드 되지 않으므로 사용할 수 없습니다.

| 오류  | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |
| 지정 된 자격 증명 모음 자격 증명 파일은이 서버와 연결 된 자격 증명 모음에서 다운로드 되지 않으므로 사용할 수 없습니다. (ID: 100110) 적절 한 자격 증명 모음 자격 증명을 제공 하세요. | 자격 증명 모음 자격 증명 파일이이 서버가 이미 등록 된 자격 증명 모음과 다른 자격 증명 모음에 있습니다. | 대상 머신 및 원본 머신이 동일한 Recovery Services 자격 증명 모음에 등록되었는지 확인합니다. 대상 서버가 다른 자격 증명 모음에 이미 등록 된 경우 **서버 등록** 옵션을 사용 하 여 올바른 자격 증명 모음에 등록 합니다.  

## <a name="backup-jobs-completed-with-warning"></a>백업 작업이 완료 되었지만 경고가 발생 했습니다.

- 파일 및 폴더를 백업 하는 동안 MARS 에이전트가 반복 되 면 백업이 완료 된 것으로 표시 될 수 있는 다양 한 조건이 발생할 수 있습니다. 이러한 상황에서 작업은 경고와 함께 완료로 표시 됩니다. 이는 문제가 없지만 하나 이상의 파일을 백업할 수 없음을 의미 합니다. 따라서 작업은 해당 파일을 건너 뛰 었으 나 데이터 원본에서 문제의 다른 모든 파일을 백업 했습니다.

  ![백업 작업이 완료 되었지만 경고가 발생 했습니다.](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- 백업에서 파일을 건너뛸 수 있는 조건은 다음과 같습니다.
  - 지원 되지 않는 파일 특성 (예: OneDrive 폴더, 압축 된 스트림, 재분석 지점의 경우) 전체 목록은 [지원 매트릭스](./backup-support-matrix-mars-agent.md#supported-file-types-for-backup)를 참조 하세요.
  - 파일 시스템 문제
  - 다른 프로세스가 방해 합니다. 예를 들어 파일에 대 한 핸들을 보유 하는 바이러스 백신 소프트웨어는 MARS 에이전트가 파일에 액세스 하지 못하도록 차단할 수 있습니다.
  - 응용 프로그램에 의해 잠긴 파일  

- 백업 서비스는 다음 명명 규칙을 사용 하 여 로그 파일에서 이러한 파일을 실패 한 것으로 표시 합니다. *C:\Program Files\Microsoft Azure Recovery service Agent\temp* 폴더 아래에 *LastBackupFailedFilesxxxx.txt* 합니다.
- 문제를 해결 하려면 로그 파일을 검토 하 여 문제의 특성을 이해 합니다.

  | 오류 코드             | 이유                                             | 권장 사항                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | 파일 또는 디렉터리가 손상 되어 읽을 수 없습니다. | 원본 볼륨에서 **chkdsk** 를 실행 합니다.                             |
  | 0x80070002, 0x80070003 | 시스템에서 지정 된 파일을 찾을 수 없습니다.         | [스크래치 폴더가 꽉 차지 않았는지 확인 합니다.](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)  <br><br>  스크래치 공간이 구성 된 볼륨이 있는지 확인 합니다 (삭제 되지 않음).  <br><br>   [MARS 에이전트가 컴퓨터에 설치 된 바이러스 백신에서 제외 되는지 확인 합니다.](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)  |
  | 0x80070005             | 액세스가 거부 되었습니다.                                    | [바이러스 백신 또는 기타 타사 소프트웨어가 액세스를 차단 하는지 확인](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)     |
  | 0x8007018b             | 클라우드 파일에 대 한 액세스가 거부 되었습니다.                | OneDrive 파일, Git 파일 또는 컴퓨터에서 오프 라인 상태일 수 있는 기타 파일 |

- [기존 정책에 제외 규칙 추가](./backup-azure-manage-mars.md#add-exclusion-rules-to-existing-policy) 를 사용 하 여 백업 정책에서 지원 되지 않거나 누락 되거나 삭제 된 파일을 제외 하 고 백업이 성공적으로 수행 되도록 할 수 있습니다.

- 최상위 폴더에 있는 동일한 이름의 보호 된 폴더를 삭제 하 고 다시 만들지 마십시오. 이렇게 하면 중요 한 불일치가 감지 되어 오류로 인해 백업이 완료 될 수 *있으므로 변경 내용을 복제할 수 없습니다.*  폴더를 삭제 하 고 다시 만들어야 하는 경우 보호 된 최상위 폴더의 하위 폴더에서이 작업을 수행 하는 것이 좋습니다.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>안전한 백업을 위한 암호화 키를 설정하지 못했습니다.

| 오류 | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |
| <br />보안 백업에 대 한 암호화 키를 설정 하지 못했습니다. 활성화가 완전히 성공한 것은 아니지만 암호화 암호가 다음 파일에 저장되었습니다. |<li>서버가 다른 자격 증명 모음에 이미 등록되어 있습니다.<li>구성하는 동안 암호가 손상되었습니다.| 자격 증명 모음에서 서버 등록을 취소 하 고 새 암호를 사용 하 여 다시 등록 합니다.

## <a name="the-activation-did-not-complete-successfully"></a>정품 인증이 완료되지 않았습니다.

| 오류  | 가능한 원인 | 권장 작업 |
|---------|---------|---------|
|<br />정품 인증이 완료되지 않았습니다. 내부 서비스 오류 [0x1FC07]로 인해 현재 작업이 실패했습니다. 잠시 후 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요.     | <li> 스크래치 폴더가 공간이 부족 한 볼륨에 있습니다. <li> 스크래치 폴더가 잘못 이동 되었습니다. <li> OnlineBackup.KEK 파일이 없습니다.         | <li>[최신 버전](https://aka.ms/azurebackup_agent) 의 MARS 에이전트로 업그레이드 합니다.<li>스크래치 폴더 또는 캐시 위치를 여유 공간이 있는 볼륨 (전체 백업 데이터 크기의 5% ~ 10%)으로 이동 합니다. 캐시 위치를 올바르게 이동 하려면 [파일 및 폴더 백업에 대 한 일반적인 질문](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)의 단계를 참조 하세요.<li> OnlineBackup.KEK 파일이 있는지 확인합니다. <br>*스크래치 폴더 또는 캐시 경로에 대 한 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*입니다.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>암호화 암호가 올바르게 구성되지 않았습니다.

| 오류  | 가능한 원인 | 권장 작업 |
|---------|---------|---------|
| <br />34506 오류입니다. 이 컴퓨터에 저장된 암호화의 암호가 제대로 구성되어 있지 않습니다.    | <li> 스크래치 폴더가 공간이 부족 한 볼륨에 있습니다. <li> 스크래치 폴더가 잘못 이동 되었습니다. <li> OnlineBackup.KEK 파일이 없습니다.        | <li>[최신 버전](https://aka.ms/azurebackup_agent)의 MARS 에이전트로 업그레이드합니다.<li>스크래치 폴더 또는 캐시 위치를 여유 공간이 있는 볼륨 (전체 백업 데이터 크기의 5% ~ 10%)으로 이동 합니다. 캐시 위치를 올바르게 이동 하려면 [파일 및 폴더 백업에 대 한 일반적인 질문](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)의 단계를 참조 하세요.<li> OnlineBackup.KEK 파일이 있는지 확인합니다. <br>*스크래치 폴더 또는 캐시 경로에 대 한 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*입니다.         |

## <a name="backups-dont-run-according-to-schedule"></a>백업이 일정에 따라 실행 되지 않음

예약 된 백업이 자동으로 트리거되지 않지만 수동 백업이 제대로 작동 하는 경우 다음 작업을 시도 합니다.

- Windows Server 백업 일정이 Azure files and folders 백업 일정과 충돌 하지 않는지 확인 합니다.

- 온라인 백업 상태가 **사용**으로 설정 되어 있는지 확인 합니다. 상태를 확인 하려면 다음 단계를 수행 합니다.

  1. 작업 스케줄러에서 **Microsoft** 를 확장 하 고 **온라인 백업**을 선택 합니다.
  1. **Microsoft-onlinebackup.kek 파일이** 을 두 번 클릭 하 고 **트리거** 탭으로 이동 합니다.
  1. 상태가 **사용**으로 설정 되어 있는지 확인 합니다. 그렇지 않은 경우 **편집**을 선택 하 고 **사용**을 선택한 다음 **확인**을 선택 합니다.

- 작업을 실행 하기 위해 선택한 사용자 계정이 서버에서 **시스템** 또는 **로컬 관리자 그룹** 인지 확인 합니다. 사용자 계정을 확인 하려면 **일반** 탭으로 이동 하 고 **보안** 옵션을 선택 합니다.

- 서버에 PowerShell 3.0 이상이 설치 되어 있는지 확인 합니다. PowerShell 버전을 확인 하려면 다음 명령을 실행 하 여 `Major` 버전 번호가 3 이상 인지 확인 합니다.

  `$PSVersionTable.PSVersion`

- 이 경로가 환경 변수의 일부 인지 확인 합니다 `PSMODULEPATH` .

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- 에 대 한 PowerShell 실행 정책이 `LocalMachine` 로 설정 된 경우 `restricted` 백업 작업을 트리거하는 powershell cmdlet이 실패할 수 있습니다. 관리자 모드에서 다음 명령을 실행 하 여 실행 정책을 확인 하 고 또는로 설정 합니다 `Unrestricted` `RemoteSigned` .

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
 ```

- PowerShell 모듈 MSOnlineBackup 파일이 없거나 손상 되지 않았는지 확인 하십시오. 파일이 없거나 손상 된 경우 다음 단계를 수행 합니다.

  1. 제대로 작동 하는 MARS 에이전트가 있는 컴퓨터에서 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.의 MSOnlineBackup 폴더를 복사 합니다.
  1. 문제가 있는 컴퓨터에서 복사한 파일을 동일한 폴더 위치 (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)에 붙여넣습니다.

     컴퓨터에 MSOnlineBackup 폴더가 이미 있는 경우 파일을 붙여넣거나 기존 파일을 바꿉니다.

> [!TIP]
> 변경 내용이 일관 되 게 적용 되도록 하려면 이전 단계를 수행한 후 서버를 다시 시작 합니다.

## <a name="resource-not-provisioned-in-service-stamp"></a>리소스가 서비스 스탬프에 프로 비전 되지 않음

오류 | 가능한 원인 | 권장 작업
--- | --- | ---
내부 서비스 오류 "리소스가 서비스 스탬프에서 프로 비전 되지 않았습니다."로 인해 현재 작업이 실패 했습니다. 잠시 후 작업을 다시 시도하세요. (ID: 230006) | 보호 된 서버의 이름이 바뀌었습니다. | <li> 자격 증명 모음에 등록 된 원래 이름으로 다시 서버 이름을 바꿉니다. <br> <li> 새 이름을 사용 하 여 자격 증명 모음에 서버를 다시 등록 합니다.

## <a name="job-could-not-be-started-as-another-job-was-in-progress"></a>다른 작업이 진행 중 이므로 작업을 시작할 수 없습니다.

**MARS 콘솔**  >  **작업 기록**에 "다른 작업이 진행 중 이므로 작업을 시작할 수 없습니다." 라는 경고 메시지가 표시 되는 경우이는 작업 스케줄러에 의해 트리거된 작업의 중복 인스턴스 때문일 수 있습니다.

![다른 작업이 진행 중 이므로 작업을 시작할 수 없습니다.](./media/backup-azure-mars-troubleshoot/job-could-not-be-started.png)

이 문제를 해결하려면:

1. 실행 창에 *taskschd* 를 입력 하 여 작업 스케줄러 스냅인을 시작 합니다.
1. 왼쪽 창에서 **작업 스케줄러 Library**  ->  **Microsoft**  ->  **onlinebackup.kek 파일이**로 이동 합니다.
1. 이 라이브러리의 각 태스크에 대해 작업을 두 번 클릭 하 여 속성을 열고 다음 단계를 수행 합니다.
    1. **설정** 탭으로 전환합니다.

         ![설정 탭](./media/backup-azure-mars-troubleshoot/settings-tab.png)

    1. **태스크가 이미 실행 중인 경우**에 대 한 옵션을 변경 하면 다음 규칙이 적용 됩니다. **새 인스턴스 시작 안 함을**선택 합니다.

         ![규칙을 변경 하 여 새 인스턴스 시작 안 함](./media/backup-azure-mars-troubleshoot/change-rule.png)

## <a name="troubleshoot-restore-problems"></a>복원 문제 해결

몇 분 후에도 Azure Backup이 복구 볼륨을 성공적으로 탑재하지 않을 수 있습니다. 그리고 프로세스 중에 오류 메시지가 표시 될 수도 있습니다. 일반적으로 복구를 시작 하려면 다음 단계를 수행 합니다.

1. 몇 분 동안 실행 되 고 있는 경우 탑재 프로세스를 취소 합니다.

2. 최신 버전의 백업 에이전트가 있는지 확인 합니다. 버전을 확인 하려면 MARS 콘솔의 **작업** 창에서 **정보 Microsoft Azure Recovery Services 에이전트 정보**를 선택 합니다. **버전** 번호가 [이 문서](https://go.microsoft.com/fwlink/?linkid=229525)에 언급된 버전보다 높거나 같은지 확인합니다. [최신 버전을 다운로드](https://go.microsoft.com/fwLink/?LinkID=288905)하려면이 링크를 선택 합니다.

3. **Device Manager**  >  **저장소 컨트롤러** 로 이동 하 여 **Microsoft iSCSI 초기자**를 찾습니다. 찾은 경우 7 단계로 직접 이동 합니다.

4. Microsoft iSCSI 초기자 서비스를 찾을 수 없는 경우 **Device Manager**  >  하드웨어 ID **ROOT\ISCSIPRT**를 사용 하 여 **알 수 없는 장치** 라는 Device Manager**저장소 컨트롤러** 에서 항목을 찾으려고 시도 합니다.

5. **알 수 없는 장치** 를 마우스 오른쪽 단추로 클릭 하 고 **드라이버 소프트웨어 업데이트**를 선택 합니다.

6. **업데이트된 드라이버 소프트웨어를 자동으로 검색**하는 옵션을 선택하여 드라이버를 업데이트합니다. 이 업데이트는 **알 수 없는 장치** 를 **Microsoft iSCSI 시작자**로 변경 해야 합니다.

    ![스토리지 컨트롤러가 강조 표시된 Azure Backup 디바이스 관리자의 스크린샷](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. **작업 관리자**  >  **서비스 (로컬)**  >  **Microsoft iSCSI 초기자 서비스**로 이동 합니다.

    ![서비스(로컬)가 강조 표시된 Azure Backup 작업 관리자의 스크린샷](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Microsoft iSCSI 초기자 서비스를 다시 시작합니다. 이렇게 하려면 해당 서비스를 마우스 오른쪽 단추로 클릭 하 고 **중지**를 선택 합니다. 그런 다음 다시 마우스 오른쪽 단추를 클릭 하 고 **시작**을 선택 합니다.

9. [인스턴트 복원](backup-instant-restore-capability.md)을 사용하여 복구를 다시 시도합니다.

그래도 복구가 실패 하면 서버 또는 클라이언트를 다시 시작 합니다. 다시 시작 하지 않으려는 경우 또는 서버를 다시 시작한 후에도 복구가 계속 실패 하는 경우 [다른 컴퓨터에서 복구](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)를 시도 합니다.

## <a name="troubleshoot-cache-problems"></a>캐시 문제 해결

캐시 폴더 (스크래치 폴더 라고도 함)가 잘못 구성 되었거나, 필수 구성 요소가 없거나, 액세스가 제한 된 경우 백업 작업이 실패할 수 있습니다.

### <a name="prerequisites"></a>필수 구성 요소

MARS 에이전트 작업을 성공적으로 수행 하려면 캐시 폴더에서 다음 요구 사항을 준수 해야 합니다.

- [스크래치 폴더 위치에서 5% ~ 10%의 사용 가능한 볼륨 공간을 사용할 수 있는지 확인 합니다.](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [스크래치 폴더 위치가 유효 하 고 액세스 가능한 지 확인](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [캐시 폴더의 파일 특성이 지원 되는지 확인 합니다.](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [할당 된 섀도 복사본 저장소 공간이 백업 프로세스에 충분 한지 확인](#increase-shadow-copy-storage)
- [캐시 폴더에 대 한 액세스를 제한 하는 다른 프로세스 (예: 바이러스 백신 소프트웨어)가 없는지 확인](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>섀도 복사본 저장소 늘리기

데이터 원본을 보호 하는 데 필요한 섀도 복사본 저장소 공간이 충분 하지 않은 경우 백업 작업이 실패할 수 있습니다. 이 문제를 해결 하려면 아래와 같이 **vssadmin** 를 사용 하 여 보호 된 볼륨의 섀도 복사본 저장소 공간을 늘리십시오.

- 관리자 권한 명령 프롬프트에서 현재 섀도 저장소 공간을 확인 합니다.<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- 다음 명령을 사용 하 여 섀도 저장소 공간을 늘립니다.<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>캐시 폴더에 대 한 액세스를 차단 하는 다른 프로세스 또는 바이러스 백신 소프트웨어

서버에 바이러스 백신 소프트웨어가 설치 되어 있는 경우 다음과 같은 파일 및 폴더에 대 한 바이러스 백신 검사에 필요한 제외 규칙을 추가 합니다.  

- 스크래치 폴더입니다. 기본 위치는 `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
- Bin 폴더 위치 `C:\Program Files\Microsoft Azure Recovery Services Agent\Bin`
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>일반적인 문제

이 섹션에서는 MARS 에이전트를 사용 하는 동안 발생 하는 일반적인 오류에 대해 설명 합니다.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

오류 메시지 | 권장 작업
--|--
Microsoft Azure Recovery Services 에이전트가 스크래치 위치에 저장된 백업 체크섬에 액세스할 수 없습니다. | 이 문제를 해결 하려면 다음 단계를 수행 하 고 서버를 다시 시작 합니다. <br/> - [바이러스 백신 또는 다른 프로세스가 스크래치 위치 파일을 잠그지 않았는지 확인 합니다.](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [스크래치 위치가 유효 하 고 MARS 에이전트에서 액세스할 수 있는지 확인 하십시오.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

오류 메시지 | 권장 작업
--|--
Microsoft Azure Recovery Services 에이전트가 VHD를 초기화하기 위해 스크래치 위치에 액세스할 수 없음 | 이 문제를 해결 하려면 다음 단계를 수행 하 고 서버를 다시 시작 합니다. <br/> - [바이러스 백신 또는 다른 프로세스가 스크래치 위치 파일을 잠그지 않았는지 확인 합니다.](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [스크래치 위치가 유효 하 고 MARS 에이전트에서 액세스할 수 있는지 확인 하십시오.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

오류 메시지 | 권장 작업
--|--
스크래치 폴더가 있는 볼륨의 저장소 공간이 부족 하 여 백업에 실패 했습니다. | 이 문제를 해결 하려면 다음 단계를 확인 하 고 작업을 다시 시도 하세요.<br/>- [MARS 에이전트가 최신 상태 인지 확인](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [백업 스크래치 공간에 영향을 주는 저장소 문제 확인 및 해결](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

오류 메시지 | 권장 작업
--|--
파일에 변경 내용을 찾을 수 없습니다. 이는 다양한 이유 때문일 수 있습니다. 작업을 다시 시도하세요. | 이 문제를 해결 하려면 다음 단계를 확인 하 고 작업을 다시 시도 하세요.<br/> - [MARS 에이전트가 최신 상태 인지 확인](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [백업 스크래치 공간에 영향을 주는 저장소 문제 확인 및 해결](#prerequisites)

## <a name="next-steps"></a>다음 단계

- [Azure Backup 에이전트를 사용 하 여 Windows Server를 백업 하는 방법](tutorial-backup-windows-server-to-azure.md)에 대 한 자세한 내용을 확인 하세요.
- 백업을 복원 해야 하는 경우 [Windows 컴퓨터에 파일 복원](backup-azure-restore-windows-server.md)을 참조 하세요.
