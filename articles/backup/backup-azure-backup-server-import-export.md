---
title: DPM 및 Azure Backup Server를 위한 오프라인 백업
description: Azure Backup를 사용 하면 Azure Import/Export 서비스를 사용 하 여 네트워크에서 데이터를 보낼 수 있습니다. 이 문서에서는 DPM 및 Azure Backup Server에 대 한 오프 라인 백업 워크플로를 설명 합니다.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 909c7cc85590005afd3b6bd32a94020937f96c32
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89002014"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-mabs"></a>DPM 및 Azure Backup Server에 대 한 오프 라인 백업 워크플로 (MABS)

>[!IMPORTANT]
> 이러한 단계는 DPM 2019 UR1 이상 및 MABS v3 UR1 이상에 적용 됩니다.

MABS (System Center Data Protection Manager 및 Azure Backup Server)는 Azure Backup와 통합 되며 Azure에 데이터를 처음 전체 백업 하는 동안 네트워크 및 저장소 비용을 절약 하는 여러 가지 기본 제공 효율성을 사용 합니다. 초기 전체 백업은 일반적으로 많은 양의 데이터를 전송하고 델타/증분만 전송하는 후속 백업과 비교할 때 더 많은 네트워크 대역폭이 필요합니다. Azure Backup은 초기 백업을 압축합니다. 오프라인 시드 프로세스를 통해 Azure Backup은 디스크를 사용하여 압축된 초기 백업 데이터를 Azure에 오프라인으로 업로드할 수 있습니다.

Azure Backup의 오프 라인 시드 프로세스는 [Azure Import/Export 서비스](../storage/common/storage-import-export-service.md)와 긴밀 하 게 통합 됩니다. 디스크를 사용 하 여 Azure로 데이터를 전송 하는 데이 서비스를 사용할 수 있습니다. 대기 시간이 짧고 대역폭이 높은 네트워크를 통해 전송 해야 하는 테라바이트 (테라바이트)의 초기 백업 데이터가 있는 경우 오프 라인 시드 워크플로를 사용 하 여 하나 이상의 하드 드라이브에 초기 백업 복사본을 Azure 데이터 센터에 제공할 수 있습니다. 이 문서에서는 System Center Data Protection Manager (DPM) 및 Microsoft Azure Backup 서버 (MABS)에 대해이 워크플로를 완료 하는 개요와 추가 단계를 제공 합니다.

> [!NOTE]
> MARS (Microsoft Azure Recovery Services) 에이전트에 대 한 오프 라인 백업 프로세스는 DPM 및 MABS와는 다릅니다. MARS 에이전트를 사용 하 여 오프 라인 백업을 사용 하는 방법에 대 한 자세한 내용은 [Azure Backup의 오프 라인 백업 워크플로](backup-azure-backup-import-export.md)를 참조 하세요. Azure Backup 에이전트를 사용 하 여 수행 된 시스템 상태 백업에는 오프 라인 백업이 지원 되지 않습니다.
>
> MABS UR1 업데이트는 MABS의 Azure Data Box를 사용 하 여 오프 라인 백업에 대 한 미리 보기도 제공 합니다. [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com)자세한 내용은을 (를) 확인 하세요.

## <a name="overview"></a>개요

Azure Backup 및 Azure Import/Export 서비스의 오프 라인 시드 기능을 사용 하면 디스크를 사용 하 여 오프 라인으로 데이터를 Azure에 업로드 하는 것이 간단 합니다. 오프라인 백업 프로세스는 다음과 같은 단계를 포함합니다.

> [!div class="checklist"]
>
> * 백업 데이터는 네트워크를 통해 전송 되지 않고 스테이징 위치에 기록 됩니다.
> * 그런 다음, 준비 위치의 데이터는 *Azureofflinebackupdiskprep* 유틸리티를 사용 하 여 하나 이상의 SATA 디스크에 기록 됩니다.
> * Azure 가져오기 작업은 유틸리티에 의해 자동으로 생성 됩니다.
> * 그런 다음 SATA 드라이브가 가장 가까운 Azure 데이터 센터로 전송 됩니다.
> * Azure에 백업 데이터를 업로드하는 작업이 완료되면 Azure Backup에서 백업 데이터를 백업 자격 증명 모음에 복사하고 증분 백업이 예약됩니다.

## <a name="prerequisites"></a>전제 조건

오프 라인 백업 워크플로를 시작 하기 전에 다음 필수 구성 요소가 충족 되는지 확인 합니다.

* [Recovery Services 자격 증명 모음](backup-azure-recovery-services-vault-overview.md)이 생성되었습니다. 하나를 만들려면 [Recovery Services 자격 증명 모음 만들기](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)-백업-windows-azure #-복구-서비스 자격 증명 모음)의 단계를 수행 합니다.
* [최신 버전의 Microsoft Azure Recovery Services 에이전트가](https://aka.ms/azurebackup_agent) SC DPM 또는 mabs에 설치 되어 있고 Recovery Services 자격 증명 모음에 등록 되어 있는지 확인 합니다.
* 업데이트 롤업 1은 SC DPM 2019 또는 MABS v3에 설치 됩니다.

  > [!NOTE]
  > DPM 2019 UR1 및 MABS v3 UR1를 사용 하 여 오프 라인 시드는 Azure Active Directory를 사용 하 여 인증 합니다.

* DPM 또는 MABS 서버에서 Microsoft Edge 또는 Internet Explorer 11이 설치 되어 있고 JavaScript가 사용 하도록 설정 되어 있는지 확인 합니다.
* Recovery Services 자격 증명 모음과 동일한 구독에서 Azure Storage 계정을 만듭니다.
* Azure Active Directory 애플리케이션을 만드는 데 [필요한 권한](../active-directory/develop/howto-create-service-principal-portal.md)이 있는지 확인합니다. 오프라인 백업 워크플로는 Azure Storage 계정과 연결된 구독에 Azure Active Directory 애플리케이션을 만듭니다. 애플리케이션의 목표는 오프라인 백업 워크플로에 필요한 Azure Import 서비스에 대한 보안 및 범위 액세스를 Azure Backup에 제공하는 것입니다.
* Azure Storage 계정이 포함된 구독에 Microsoft.ImportExport 리소스 공급자를 등록합니다. 리소스 공급자를 등록하려면 다음 단계를 따릅니다.
    1. 주 메뉴에서 **구독**을 클릭합니다.
    2. 여러 구독을 구독 하는 경우 오프 라인 백업에 사용 중인 구독을 선택 합니다. 구독을 하나만 사용하는 경우 해당 구독이 표시됩니다.
    3. 구독 메뉴에서 **리소스 공급자**를 클릭하여 공급자 목록을 봅니다.
    4. 공급자 목록에서 Microsoft.ImportExport로 스크롤합니다. 상태가 NotRegistered인 경우 **등록**을 클릭합니다.

       ![리소스 공급자 등록](./media/backup-azure-backup-server-import-export/register-import-export.png)

* 초기 복사본을 저장하기 위한 충분한 디스크 공간의 컴퓨터 내부 또는 외부에 네트워크 공유 또는 추가 드라이브일 수 있는 준비 위치가 생성됩니다. 예를 들어 500-GB 파일 서버를 백업 하려는 경우 준비 영역이 500 GB 이상 인지 확인 합니다. (압축으로 인해 더 적은 양이 사용됩니다.)
* Azure로 전송 되는 디스크의 경우 2.5 인치 SSD 또는 2.5 인치 또는 3.5 인치 SATA II/III 내부 하드 드라이브만 사용 해야 합니다. 최대 10TB의 하드 드라이브를 사용할 수 있습니다. 서비스에서 지원하는 최신 드라이브 집합의 경우 [Azure Import/Export 서비스 설명서](../storage/common/storage-import-export-requirements.md#supported-hardware)를 확인하세요.
* SATA 드라이브는 준비 위치에서 SATA 드라이브로 백업 데이터의 복사가 수행되는 컴퓨터(*복사 컴퓨터*라고 함)에 연결되어야 합니다. 복사 컴퓨터에서 BitLocker가 사용 하도록 설정 되어 있는지 확인 합니다.

## <a name="workflow"></a>워크플로

이 섹션의 정보는 오프라인 백업 워크플로를 완료하여 데이터를 Azure 데이터 센터로 제공하고 Azure Storage에 업로드할 수 있는 데 도움이 됩니다. Import 서비스 또는 이 프로세스의 특정 요소에 대해 궁금한 점이 있으면 앞서 나온 [Import 서비스 개요](../storage/common/storage-import-export-service.md) 설명서를 참조하세요.

## <a name="initiate-offline-backup"></a>오프라인 백업 시작

1. 온라인 보호를 사용하여 새 보호 그룹을 생성하거나 기존 보호 그룹에 온라인 보호를 추가하는 경우 다음과 같은 화면이 표시됩니다. 초기 온라인 복제 방법을 선택하려면 **보유한 디스크를 사용하여 전송**을 선택하고 **다음**을 클릭합니다.

    ![Import 화면](./media/backup-azure-backup-server-import-export/create-new-protection-group.png)

2. Azure 로그인 페이지가 열립니다. Azure 구독에 대 한 *소유자* 역할 권한이 있는 azure 사용자 계정을 사용 하 여 로그인 합니다.

    ![Azure 로그인 페이지](./media/backup-azure-backup-server-import-export/choose-initial-online-replication.png)

3. **자체 디스크 사용** 페이지에 입력을 제공합니다.

   ![사용자 고유의 디스크 사용에 대 한 입력](./media/backup-azure-backup-server-import-export/use-your-own-disk.png)

   입력에 대한 설명은 다음과 같습니다.

   * **준비 위치**: 초기 백업 복사본을 쓸 임시 스토리지 위치입니다. 스테이징 위치는 네트워크 공유 또는 로컬 컴퓨터에 있을 수 있습니다. 복사 컴퓨터와 원본 컴퓨터가 다른 경우 스테이징 위치의 전체 네트워크 경로를 지정 합니다.
   * **Azure Resource Manager 스토리지 계정**: Azure 구독의 Resource Manager 유형 스토리지 계정(범용 v1 또는 범용 v2) 이름입니다.
   * **Azure Storage 컨테이너**: 백업 데이터를 가져올 Azure Storage 계정의 대상 BLOB Storage 컨테이너의 이름입니다.
   * **Azure 구독 ID**: Azure Storage 계정이 생성되는 Azure 구독의 ID입니다.
   * **Azure Import 작업 이름**: Azure Import 서비스 및 Azure Backup이 디스크에서 Azure로 전송된 데이터의 전송을 추적하는 고유한 이름입니다.

    제공 된 **준비 위치** 및 **Azure 가져오기 작업 이름** 정보를 저장 합니다. 디스크를 준비 하는 데 필요 합니다.

4. 보호를 만들거나 업데이트 하는 워크플로를 완료 합니다. 오프라인 백업 복사를 시작하려면 **보호 그룹**을 마우스 오른쪽 버튼으로 클릭한 다음 **복구 지점 만들기** 옵션을 선택합니다. 그런 다음 **온라인 보호** 옵션을 선택합니다.

   ![복구 지점 만들기](./media/backup-azure-backup-server-import-export/create-recovery-point.png)

5. 모니터링 창에서 온라인 복제본 만들기 작업을 모니터링합니다. *Azure Import 작업이 완료될 때까지 대기* 경고와 함께 작업이 성공적으로 완료됩니다.

   ![복구 지점 완료](./media/backup-azure-backup-server-import-export/complete-recovery-point.png)

6. 작업이 완료되면 준비 위치를 디스크 준비에 사용할 수 있습니다.

## <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA 드라이브 준비 및 Azure로 배송

*AzureOfflineBackupDiskPrep* 유틸리티는 가장 가까운 Azure 데이터 센터로 전송되는 SATA 드라이브를 준비합니다. 이 유틸리티는 Azure Backup 에이전트 설치 디렉터리 (다음 경로)에서 사용할 수 있습니다. `*\Microsoft Azure Recovery Services Agent\Utils\\*`

1. 디렉터리로 이동하여 **AzureOfflineBackupDiskPrep** 디렉터리를 SATA 드라이브가 연결된 다른 컴퓨터로 복사합니다. SATA 드라이브가 연결된 컴퓨터에서 다음을 확인합니다.

   * 복사 컴퓨터는 "오프 라인 백업 시작" 섹션의 워크플로에 제공 된 것과 동일한 네트워크 경로를 사용 하 여 오프 라인 시드 워크플로의 준비 위치에 액세스할 수 있습니다.
   * 복사 컴퓨터에서 BitLocker가 사용 설정되어 있습니다.
   * 3.7.0가 복사 컴퓨터에 설치 됩니다. DPM 또는 MABS 서버에서 Azure Backupdiskprep 유틸리티를 실행 하는 경우에는 필요 하지 않습니다. Azure PowerShell
   * 최신 호환 브라우저(Microsoft Edge 또는 Internet Explorer 11)가 설치되고 JavaScript가 사용 설정되어 있습니다.
   * 복사 컴퓨터는 Azure Portal에 액세스할 수 있습니다. 필요한 경우 복사 컴퓨터는 원본 컴퓨터와 동일할 수 있습니다.

     > [!IMPORTANT]
     > 원본 컴퓨터가 가상 컴퓨터인 경우에는 복사 컴퓨터와 다른 실제 서버 또는 클라이언트 컴퓨터를 사용 해야 합니다.

1. *Azureofflinebackupdiskprep* 유틸리티 디렉터리를 현재 디렉터리로 사용 하 여 복사 컴퓨터에서 관리자 권한 명령 프롬프트를 엽니다. 다음 명령을 실행합니다.

    ```console
    .\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>
    ```

    | 매개 변수 | 설명 |
    | --- | --- |
    | s:&lt;*Staging Location Path*&gt; |이 필수 입력은 "오프 라인 백업 시작" 섹션의 워크플로에 입력 한 스테이징 위치에 대 한 경로를 제공 하는 데 사용 됩니다. |
    | p:&lt;*Path to PublishSettingsFile*&gt; |이 선택적 입력은 Azure 게시 설정 파일에 대 한 경로를 제공 하는 데 사용 됩니다. |

    명령을 실행할 때 유틸리티는 준비해야 하는 드라이브에 해당하는 Azure Import 작업의 선택을 요청합니다. 제공된 준비 위치와 연결된 가져오기 작업이 하나만 있는 경우 다음과 같은 화면이 표시됩니다.

      ![디스크 준비 콘솔](./media/backup-azure-backup-server-import-export/disk-prep-console.png)

1. Azure로 전송하기 위해 준비하려는 탑재된 디스크에 대해 후행 콜론 없이 드라이브 문자를 입력합니다.
1. 메시지가 표시되면 드라이브 형식을 확인합니다.
1. Azure 구독에 로그인 하 라는 메시지가 표시 됩니다. 자격 증명을 제공합니다.

    ![Azure 로그인 화면](./media/backup-azure-backup-server-import-export/signin-disk-prep.png)

    그런 다음 도구는 디스크를 준비 하 고 백업 데이터를 복사 하기 시작 합니다. 제공 된 디스크에 백업 데이터에 대 한 충분 한 공간이 없는 경우 도구에서 메시지가 표시 되 면 추가 디스크를 연결 해야 할 수 있습니다. <br/>

    도구를 성공적으로 실행한 후 명령 프롬프트에서 다음 세 가지 정보를 제공합니다.
    * 제공한 하나 이상의 디스크가 Azure로 배송될 준비가 되었습니다.
    * 가져오기 작업이 생성되었다는 확인 메시지가 표시됩니다. 가져오기 작업은 사용자가 제공한 이름을 사용합니다.
    * 이 도구는 Azure 데이터 센터에 대한 배송 주소를 표시합니다.

     ![Azure 디스크 준비 완료](./media/backup-azure-backup-server-import-export/console.png)

1. 명령 실행이 끝날 때 배송 정보를 업데이트 하는 옵션도 표시 됩니다.

1. 도구가 제공하는 주소로 디스크를 배송하고 나중에 참조할 수 있도록 추적 번호를 유지합니다.

   > [!IMPORTANT]
   > 두 Azure 가져오기 작업은 동일한 추적 번호를 가질 수 없습니다. 단일 Azure 가져오기 작업에서 유틸리티에 의해 준비 된 드라이브가 단일 패키지로 함께 배송 되 고 패키지에 대 한 단일 고유 추적 번호가 있는지 확인 합니다. 여러 Azure 가져오기 작업의 일부로 준비 된 드라이브를 단일 패키지로 결합 하지 마세요.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Azure Import 작업의 배송 세부 정보 업데이트

다음 절차는 Azure Import 작업 배송 세부 정보를 업데이트합니다. 이 정보에는 다음에 대한 세부 정보가 포함됩니다.

* Azure에 디스크를 배달하는 운송업체 이름
* 디스크의 반송 세부 정보

   1. Azure 구독에 로그인합니다.
   2. 주 메뉴에서 **모든 서비스**를 클릭하고 모든 서비스 대화 상자에 Import를 입력합니다. **Import/Export 작업**이 표시되면 해당 작업을 클릭합니다.
       ![배송 정보 입력](./media/backup-azure-backup-server-import-export/search-import-job.png)

       **Import/export 작업** 메뉴가 열리고 선택한 구독의 모든 Import/export 작업 목록이 표시됩니다.

   3. 구독이 여러 개인 경우 백업 데이터를 가져오는 데 사용되는 구독을 선택해야 합니다. 그런 다음 새로 만든 Import 작업을 선택하여 세부 정보를 엽니다.

       ![배송 정보 검토](./media/backup-azure-backup-server-import-export/import-job-found.png)

   4. Import 작업에 대한 설정 메뉴에서 **배송 정보 관리**를 클릭하고 반송 세부 정보를 입력합니다.

       ![배송 정보 저장](./media/backup-azure-backup-server-import-export/shipping-info.png)

   5. 배송 운송업체의 추적 번호가 있으면 Azure Import 작업 개요 페이지에서 배너를 클릭하고 다음 세부 정보를 입력합니다.

      > [!IMPORTANT]
      > Azure Import 작업 생성 후 2주 이내에 운송업체 정보 및 추적 번호가 업데이트되었는지 확인합니다. 2주 이내에 이 정보를 확인하지 못하면 작업이 삭제되고 드라이브가 처리되지 않을 수 있습니다.

      ![작업 개요](./media/backup-azure-backup-server-import-export/job-overview.png)

      ![추적 정보](./media/backup-azure-backup-server-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>드라이브를 처리하는 시간

Azure 가져오기 작업을 처리 하는 데 걸리는 시간은 다양 합니다. 프로세스 시간은 배송 시간, 작업 유형, 복사 되는 데이터의 유형 및 크기, 제공 된 디스크 크기와 같은 요소에 따라 달라 집니다. Azure Import/Export 서비스에는 SLA가 없습니다. 디스크가 수신 되 면 서비스는 Azure storage 계정에 대해 7 ~ 10 일 내에 백업 데이터 복사를 완료 합니다. 다음 섹션에서는 Azure 가져오기 작업의 상태를 모니터링할 수 있는 방법을 설명 합니다.

### <a name="monitor-azure-import-job-status"></a>Azure 가져오기 작업 상태 모니터링

**Import/Export 작업** 페이지로 이동하고 작업을 선택하여 Azure Portal에서 Import 작업의 상태를 모니터링할 수 있습니다. Import 작업의 상태에 대한 자세한 내용은 [Storage Import/Export 서비스](../storage/common/storage-import-export-service.md) 문서를 참조하세요.

### <a name="complete-the-workflow"></a>워크플로 완료

가져오기 작업이 완료되면 스토리지 계정에서 초기 백업 데이터를 사용할 수 있습니다. 다음 예약 된 백업 시 Azure Backup는 저장소 계정에서 Recovery Services 자격 증명 모음으로 데이터의 내용을 복사 합니다.

다음 예약 온라인 복제본 만들기 작업 시 Data Protection Manager는 초기 백업 복사본에 대해 증분 백업을 수행합니다.

## <a name="next-steps"></a>다음 단계

* Azure Import/Export 서비스 워크플로에 대 한 질문은 [Microsoft Azure Import/Export 서비스를 사용 하 여 Blob storage로 데이터 전송](../storage/common/storage-import-export-service.md)을 참조 하세요.
