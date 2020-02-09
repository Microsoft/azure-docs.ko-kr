---
title: MARS 에이전트를 사용 하 여 Windows 컴퓨터 백업
description: MARS (Azure Backup Microsoft Recovery Services) 에이전트를 사용 하 여 Windows 컴퓨터를 백업 합니다.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 990929cc95d1c25117873ca39415d33370456b91
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025540"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>Azure Backup MARS 에이전트를 사용하여 Windows 머신 백업

이 문서에서는 [Azure Backup](backup-overview.md) 서비스 및 MARS (Microsoft Azure Recovery Services) 에이전트를 사용 하 여 Windows 컴퓨터를 백업 하는 방법을 설명 합니다 .이 에이전트를 Azure Backup 에이전트 라고도 합니다.

이 문서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 필수 구성 요소를 확인 하 고 Recovery Services 자격 증명 모음을 만듭니다.
> * MARS 에이전트 다운로드 및 설정
> * 백업 정책 및 일정을 만듭니다.
> * 주문형 백업을 수행 합니다.

## <a name="about-the-mars-agent"></a>MARS 에이전트 정보

MARS 에이전트는 Azure Backup에서 온-프레미스 컴퓨터 및 Azure Vm에서 Azure의 백업 Recovery Services 자격 증명 모음으로 파일, 폴더 및 시스템 상태를 백업 하는 데 사용 됩니다. 다음과 같이 에이전트를 실행할 수 있습니다.

* 온-프레미스 Windows 컴퓨터에서 직접 에이전트를 실행 하 여 Azure에서 백업 Recovery Services 자격 증명 모음에 직접 백업할 수 있습니다.
* Windows를 실행 하는 Azure vm에서 에이전트를 실행 하 여 VM의 특정 파일 및 폴더를 백업 합니다.
* MABS(Microsoft Azure Backup Server) 또는 System Center DPM(Data Protection Manager) 서버에서 에이전트를 실행합니다. 이 시나리오에서 컴퓨터 및 워크 로드는 MABS/DPM에 백업 된 다음, MABS/DPM은 MARS 에이전트를 사용 하 여 Azure의 자격 증명 모음에 백업 합니다.
에이전트가 설치된 위치에 따라 백업할 수 있는 항목이 달라집니다.

> [!NOTE]
> Azure Vm을 백업 하는 기본 방법은 VM에서 Azure Backup 확장을 사용 하는 것입니다. 이 경우 전체 VM을 백업합니다. VM의 특정 파일 및 폴더를 백업 하려는 경우 확장을 함께 MARS 에이전트를 설치 하 고 사용할 수 있습니다. [자세히 알아보기](backup-architecture.md#architecture-built-in-azure-vm-backup).

![Backup 프로세스 단계](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>시작하기 전에

* [방법 알아보기](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) Azure Backup MARS 에이전트를 사용 하 여 Windows 컴퓨터를 백업 합니다.
* 보조 MABS 또는 DPM 서버에서 MARS 에이전트를 실행 하는 백업 아키텍처 [에 대해 알아봅니다](backup-architecture.md#architecture-back-up-to-dpmmabs) .
* 지원 되는 기능과 MARS 에이전트를 사용 하 여 백업할 수 있는 항목을 [검토](backup-support-matrix-mars-agent.md) 합니다.
* 백업 하려는 컴퓨터에서 인터넷 액세스를 확인 합니다.
* 서버 또는 클라이언트를 Azure에 백업하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/free/) 을 만들 수 있습니다.

### <a name="verify-internet-access"></a>인터넷 액세스 확인

컴퓨터의 인터넷 액세스가 제한 된 경우 컴퓨터 또는 프록시의 방화벽 설정에서 다음 Url 및 IP 주소를 허용 하는지 확인 합니다.

#### <a name="urls"></a>URL

* www\.msftncsi.com
* *.Microsoft.com
* *.WindowsAzure.com
* *.microsoftonline.com
* *.windows.net

#### <a name="ip-addresses"></a>IP 주소

* 20.190.128.0/18
* 40.126.0.0/18

위에 나열 된 모든 Url 및 IP 주소에 대한 액세스는 포트 443에서 HTTPS 프로토콜을 사용 합니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음은 시간이 지남에 따라 만든 모든 백업 및 복구 지점과 백업 된 컴퓨터에 적용 되는 백업 정책을 포함 합니다. 다음과 같이 자격 증명 모음을 만듭니다.

1. Azure 구독을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **Recovery Services 자격 증명 모음**을 검색하여 선택합니다.

    ![Recovery Services 자격 증명 모음 만들기 1단계](./media/backup-configure-vault/open-recovery-services-vaults.png)

3. **Recovery Services 자격 증명 모음** 메뉴에서 **+ 추가**를 클릭 합니다.

    ![Recovery Services 자격 증명 모음 만들기 2단계](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다.

   * 이름은 Azure 구독에 대해 고유해야 합니다.
   * 2~50자를 포함할 수 있습니다.
   * 문자로 시작해야 하며, 문자, 숫자, 하이픈만 사용할 수 있습니다.

5. 자격 증명 모음을 만들어야 하는 Azure 구독, 리소스 그룹 및 지역을 선택 합니다. 백업 데이터는 자격 증명 모음으로 전송 됩니다. 그런 다음, **만들기**를 클릭합니다.

    ![Recovery Services 자격 증명 모음 만들기 3단계](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   * 자격 증명 모음을 만드는 데 시간이 걸릴 수 있습니다.
   * 포털의 오른쪽 위 영역에 있는 상태 알림을 모니터링합니다. 몇 분 후에 자격 증명 모음이 표시 되지 않으면 **새로 고침**을 클릭 합니다.

     ![새로 고침 단추 클릭](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>스토리지 중복 설정

Azure Backup는 자격 증명 모음에 대한 저장소를 자동으로 처리 합니다. 저장소를 복제 하는 방법을 지정 해야 합니다.

1. **Recovery Services 자격 증명 모음** 블레이드에서 새 자격 증명 모음을 클릭합니다. **설정** 섹션에서 **속성**을 클릭 합니다.
2. **속성**의 **백업 구성**에서 **업데이트**를 클릭 합니다.

3. 저장소 복제 유형을 선택 하 고 **저장**을 클릭 합니다.

      ![새 자격 증명 모음의 스토리지 구성 설정](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

* Azure를 기본 백업 저장소 엔드포인트으로 사용 하는 경우 기본 **지역 중복** 설정을 계속 사용 하는 것이 좋습니다.
* Azure를 기본 백업 스토리지 엔드포인트로 사용하지 않는 경우 Azure Storage 비용이 감소되는 **로컬 중복**을 선택합니다.
* [지역](../storage/common/storage-redundancy-grs.md) 및 [로컬](../storage/common/storage-redundancy-lrs.md) 중복성에 대해 자세히 알아보세요.

## <a name="download-the-mars-agent"></a>MARS 에이전트 다운로드

백업 하려는 컴퓨터에 MARS 에이전트를 다운로드 하 여 설치 합니다.

* 컴퓨터에 에이전트를 이미 설치한 경우 최신 버전을 실행 하 고 있는지 확인 합니다.
* 포털에서 또는 [직접 다운로드](https://aka.ms/azurebackup_agent) 를 사용 하 여 최신 버전을 사용할 수 있습니다.

1. 자격 증명 모음의 **시작**에서 **백업**을 클릭 합니다.

    ![백업 목표 블레이드 열기](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. **워크 로드가 실행 되는 위치**에서 **온-프레미스**를 선택 합니다. Azure VM에 MARS 에이전트를 설치 하려는 경우에도이 옵션을 선택 해야 합니다.
3. **백업할 항목**에서 **파일 및 폴더** 및/또는 **시스템 상태**를 선택 합니다. 여러 가지 옵션을 사용할 수 있지만 보조 백업 서버를 실행 하는 경우에만 지원 됩니다. **인프라 준비**를 클릭 합니다.

      ![파일 및 폴더 구성](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. **준비 인프라**의 **Recovery Services 에이전트 설치**에서 MARS 에이전트를 다운로드 합니다.

    ![인프라 준비](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. 다운로드 팝업 메뉴에서 **저장**을 클릭합니다. 기본적으로 **MARSagentinstaller.exe** 파일이 다운로드 폴더에 저장됩니다.

6. 이제 **최신 Recovery Services Agent를 이미 다운로드 하거나 사용한**후 자격 증명 모음 자격 증명을 다운로드 합니다.

    ![자격 증명 모음 자격 증명 다운로드](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. **저장**을 클릭합니다. 파일이 다운로드 폴더에 다운로드 됩니다. 자격 증명 모음 자격 증명 파일을 열 수 없습니다.

## <a name="install-and-register-the-agent"></a>에이전트 설치 및 등록

1. 백업 하려는 컴퓨터에서 **Marsagentinstaller .exe** 파일을 실행 합니다.
2. MARS 에이전트 설치 마법사 > **설치 설정**에서 에이전트를 설치할 위치와 캐시에 사용할 위치를 지정 합니다. 그런 후 **Next** 를 클릭합니다.
   * Azure Backup은 캐시를 사용 하 여 데이터 스냅숏을 Azure로 보내기 전에 저장 합니다.
   * 캐시 위치에는 백업할 데이터 크기의 5% 이상에 해당 하는 여유 공간이 있어야 합니다.

    ![MARS 마법사 설치 설정](./media/backup-configure-vault/mars1.png)

3. **프록시 구성**에서 Windows 컴퓨터에서 실행 되는 에이전트가 인터넷에 연결 되는 방법을 지정 합니다. 그런 후 **Next** 를 클릭합니다.

   * 사용자 지정 프록시를 사용 하는 경우 프록시 설정 및 자격 증명 (필요한 경우)을 지정 합니다.
   * 에이전트는 [이러한 url](#verify-internet-access)에 액세스할 수 있어야 합니다.

     ![MARS 마법사 인터넷 액세스](./media/backup-configure-vault/mars2.png)

4. **설치** 에서 필수 구성 요소 확인을 검토 하 고 **설치**를 클릭 합니다.
5. 에이전트가 설치 되 면 **등록 진행을**클릭 합니다.
6. **서버 등록 마법사** > 자격 증명 **모음 id**에서 다운로드 한 자격 증명 파일을 찾아 선택 합니다. 그런 후 **Next** 를 클릭합니다.

    ![자격 증명 모음 자격 증명](./media/backup-configure-vault/register1.png)

7. **암호화 설정**에서 컴퓨터에 대한 백업을 암호화 하 고 암호를 해독 하는 데 사용할 암호를 지정 합니다.

    * 암호화 암호를 안전한 위치에 저장합니다.
    * 암호를 분실 하거나 잊은 경우 Microsoft는 백업 데이터를 복구 하는 데 도움을 줍니다. 파일을 안전한 위치에 저장하세요. 백업을 복원 하는 데 필요 합니다.

8. **Finish**를 클릭합니다. 이제 에이전트가 설치되었고 컴퓨터가 자격 증명 모음에 등록되었습니다. 백업을 구성하고 일정을 예약할 준비가 완료되었습니다.

## <a name="create-a-backup-policy"></a>백업 정책 만들기

백업 정책은 데이터의 스냅숏을 사용 하 여 복구 지점의 생성 시점 및 복구 지점의 보존 기간을 지정 합니다.

* MARS 에이전트를 사용 하 여 백업 정책을 구성 합니다.
* Azure Backup는 DST (일광 절약 시간제)를 자동으로 고려 하지 않습니다. 이로 인해 실제 시간과 예약 된 백업 시간 사이에 약간의 차이가 발생할 수 있습니다.

다음과 같이 정책을 만듭니다.
1. MARS 에이전트를 다운로드 하 고 등록 한 후 에이전트 콘솔을 시작 합니다. **Microsoft Azure Backup**에 대한 컴퓨터를 검색하여 찾을 수 있습니다.  
2. **작업**에서 **백업 예약**을 클릭 합니다.

    ![Windows Server 백업 예약](./media/backup-configure-vault/schedule-first-backup.png)
3. 백업 예약 마법사에서 **시작**> **다음**을 클릭 합니다.
4. **백업할 항목 선택**에서 **항목 추가**를 클릭 합니다.

    ![백업할 항목 선택](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. **항목 선택**에서 백업할 항목을 선택 하 고 **확인**을 클릭 합니다.

    ![백업할 항목 선택](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. **백업할 항목 선택** 페이지에서 **다음**을 클릭 합니다.
7. **백업 일정 지정** 페이지에서 매일 또는 매주 백업을 수행 하려는 경우를 지정 합니다. 그런 후 **Next** 를 클릭합니다.

    - 복구 지점은 백업을 수행할 때 생성 됩니다.
    - 사용자 환경에서 생성 되는 복구 지점의 수는 백업 일정에 따라 달라 집니다.


8. 하루에 최대 3 번까지 매일 백업을 예약할 수 있습니다. 예를 들어 스크린샷은 매일 자정에 하나씩, 오후 6:00 시에 하나씩 두 개의 일별 백업을 보여 줍니다.

    ![일별 일정](./media/backup-configure-vault/day-schedule.png)


9. 주별 백업만 실행할 수 있습니다. 예를 들어 스크린샷은 9:30 오전 시와 오전 1:00 시에 모든 대체 일요일 & 수요일에 수행 된 백업을 보여 줍니다.

    ![주별 일정](./media/backup-configure-vault/week-schedule.png)


10. **보존 정책 선택** 페이지에서 데이터의 기록 복사본을 저장 하는 방법을 지정 합니다. 그런 후 **Next** 를 클릭합니다.

    - 보존 설정은 저장 해야 하는 복구 지점과 저장 해야 하는 기간을 지정 합니다.
    - 예를 들어 일별 보존 설정을 설정 하는 경우 매일 보존에 지정 된 시간에 최신 복구 지점이 지정 된 일 수 동안 보존 됨을 표시 합니다. 또는 매월 30 일에 생성 된 복구 지점을 12 개월 동안 저장 하도록 표시 하는 월별 보존 정책을 지정할 수 있습니다.
    - 매일 및 매주 복구 지점 보존은 일반적으로 백업 일정과 일치 합니다. 백업이 일정에 따라 트리거될 경우 백업으로 만들어진 복구 지점은 매일 또는 매주 보존 정책에 지정 된 기간 동안 저장 됩니다.
    - 예를 들어 다음 스크린샷에서는 다음과 같습니다.

        -   자정 및 오후 6:00에 매일 백업은 7 일 동안 유지 됩니다.
        -   토요일 자정 및 오후 6:00에 수행 된 백업은 4 주 동안 유지 됩니다.
        -   월의 마지막 주에 수행 된 토요일 자정 및 오후 6:00 시의 백업은 12 개월 동안 유지 됩니다.
        -   3 월의 마지막 주 토요일에 수행 된 백업은 10 년 동안 유지 됩니다.

        ![보존 예](./media/backup-configure-vault/retention-example.png)


11. **초기 백업 유형 선택** 에서 네트워크를 통해 초기 백업을 수행 하거나 오프 라인 백업을 사용할지 결정 합니다. 오프 라인 백업에 대한 자세한 내용은이 [문서](offline-backup-azure-data-box.md)를 참조 하세요. 네트워크를 통해 초기 백업을 수행 하려면 **네트워크를 통해 자동으로** 를 선택 하 고 **다음**을 클릭 합니다.

    ![초기 백업 유형](./media/backup-azure-manage-mars/choose-initial-backup-type.png)


12. **확인**에서 정보를 검토 한 다음 **마침**을 클릭 합니다.

    ![백업 유형 확인](./media/backup-azure-manage-mars/confirm-backup-type.png)


13. 마법사가 백업 일정 생성을 완료하면 **닫기**를 클릭합니다.

    ![백업 프로세스 수정 확인](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

에이전트가 설치 된 각 컴퓨터에서 정책을 만들어야 합니다.

### <a name="perform-the-initial-backup-offline"></a>오프 라인으로 초기 백업 수행

네트워크를 통해 또는 오프 라인에서 초기 백업을 자동으로 실행할 수 있습니다. 초기 백업에 대한 오프 라인 시드는 전송에 많은 네트워크 대역폭을 필요로 하는 대량의 데이터가 있는 경우 유용 합니다. 다음과 같이 오프 라인 전송을 수행 합니다.

1. 준비 위치에 백업 데이터를 작성 합니다.
2. AzureOfflineBackupDiskPrep 도구를 사용 하 여 스테이징 위치에서 하나 이상의 SATA 디스크로 데이터를 복사 합니다.
3. 이 도구는 Azure 가져오기 작업을 만듭니다. Azure 가져오기 및 내보내기에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) .
4. Azure 데이터 센터에 SATA 디스크를 보냅니다.
5. 데이터 센터에서 디스크 데이터는 Azure storage 계정에 복사 됩니다.
6. Azure Backup 저장소 계정에서 자격 증명 모음으로 데이터를 복사 하 고 증분 백업이 예약 됩니다.

오프 라인 시드에 대해 [자세히 알아보세요](offline-backup-azure-data-box.md) .

### <a name="enable-network-throttling"></a>네트워크 제한 사용

네트워크 제한을 사용 하도록 설정 하 여 MARS 에이전트에서 네트워크 대역폭을 사용 하는 방법을 제어할 수 있습니다. 제한은 근무 시간에 데이터를 백업 해야 하지만 백업 및 복원 작업에 사용 되는 대역폭 크기를 제어 하려는 경우에 유용 합니다.

* Azure Backup 네트워크 제한은 로컬 운영 체제의 [QoS (서비스 품질)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) 를 사용 합니다.
* 백업에 대한 네트워크 제한은 Windows Server 2012 이상 및 Windows 8부터 사용할 수 있습니다. 운영 체제는 최신 서비스 팩을 실행 해야 합니다.

네트워크 제한을 다음과 같이 설정 합니다.

1. MARS 에이전트에서 **속성 변경**을 클릭 합니다.
2. **제한** 탭에서 **백업 작업에 인터넷 대역폭 사용 제한 사용**을 선택 합니다.

    ![네트워크 제한](./media/backup-configure-vault/throttling-dialog.png)
3. 근무 시간 및 근무 외 시간에 허용 되는 대역폭을 지정 합니다. 대역폭 값은 512 Kbps에서 시작 하 여 1023 MBps로 이동 합니다. 그런 후 **OK**를 클릭합니다.

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

1. MARS 에이전트에서 **지금**백업을 클릭 합니다.

    ![지금 Windows Server 백업](./media/backup-configure-vault/backup-now.png)

2. MARS 에이전트 버전이 2.0.9169.0 이상 이면 사용자 지정 보존을 설정할 수 있습니다. 다음 기간 **까지 백업 보존** 섹션에서 제시 된 달력의 날짜를 선택 합니다.

   ![백업 일정 보존](./media/backup-configure-vault/mars-ondemand.png)

3. **확인**에서 설정을 검토 하 고 **백업**을 클릭 합니다.
4. **닫기** 를 클릭하여 마법사를 닫습니다. 백업이 완료 되기 전에이 작업을 수행 하면 마법사가 백그라운드에서 계속 실행 됩니다.
5. 초기 백업 작업이 완료되면 Backup 콘솔에 **작업 완료** 상태가 표시됩니다.

## <a name="on-demand-backup-policy-retention-behavior"></a>주문형 백업 정책 보존 동작

>[!NOTE]
>2\.0.9169.0 보다 오래 된 MARS 에이전트 버전에만 적용 가능
>

* 자세한 내용은 [백업 정책 만들기](backup-configure-vault.md#create-a-backup-policy) 의 8 단계를 참조 하세요.

| 백업 일정 옵션 | 백업 된 데이터는 얼마나 오래 유지 되나요?
| -- | --
| 백업 예약 간격: * 일 | **기본 보존**: "일별 백업에 대한 보존 기간 (일)"에 해당 합니다. <br/><br/> **예외**: 장기 보존 (주, 월, 년)에 대해 예약 된 매일 예약 된 백업 집합이 실패 한 경우이 실패 한 예약 된 백업 직후에 트리거되는 주문형 백업은 장기 보존에 대한 것으로 간주 됩니다. 그렇지 않으면 다음 예약 된 백업이 장기 보존을 위해 고려 됩니다.<br/><br/> **예**: 목요일 8:00 am에 수행 된 예약 된 백업이 실패 하 고 동일한 백업이 주별/월별/매년 보존에 대해 고려 되어야 하는 경우 첫 번째 8:00 주문형 백업은 목요일 8:00 am 백업에 적용 되는 주별/월별/매년 보존에 대해 자동으로 태그가 지정 됩니다.
| 백업 예약 간격: * 매주 | **기본 보존**: 1 일 <br/> 주간 백업 정책을 사용 하는 데이터 원본에 대해 수행 되는 주문형 백업은 데이터 원본에 대한 가장 최근의 백업 이더라도 다음 날에 삭제 됩니다. <br/><br/> **예외**: 장기 보존 (주, 월, 년)에 대해 예약 된 매주 예약 된 백업 세트가 실패할 경우이 실패 한 예약 된 백업 직후에 트리거되는 주문형 백업은 장기 보존에 대한 것으로 간주 됩니다. 그렇지 않으면 다음 예약 된 백업이 장기 보존을 위해 고려 됩니다. <br/><br/> **예**: 목요일 8:00에 수행 된 예약 된 백업이 실패 하 고 매월/매년 보존에 대해 동일한 백업이 고려 되어야 하는 경우 첫 번째 8:00 주문형 백업은 목요일 8:00 am 백업에 적용 되는 월간/연도별 보존에 대해 자동으로 태그가 지정 됩니다.

## <a name="next-steps"></a>다음 단계

[파일을 복원 하는 방법을 알아봅니다](backup-azure-restore-windows-server.md).
