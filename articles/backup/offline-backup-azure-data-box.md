---
title: Azure Data Box를 사용 하 여 오프 라인 백업
description: Azure Data Box를 사용 하 여 MARS 에이전트에서 Recovery Services 자격 증명 모음으로 대량 초기 백업 데이터를 오프 라인으로 설정 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 5a4aeebeddcca4adcac511c7c225c8809dd29c93
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180935"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Azure Data Box를 사용한 Azure Backup 오프라인 백업

[Azure Data Box](../databox/data-box-overview.md) 를 사용 하 여 네트워크를 사용 하지 않고 Recovery Services 자격 증명 모음에 대 한 MARS (large 초기 Microsoft Azure Recovery Services) 백업을 오프 라인으로 초기값으로 지정할 수 있습니다. 이 프로세스는 대기 시간이 긴 네트워크를 통해 대량 백업 데이터를 온라인으로 이동하는 데 사용되었을 시간과 네트워크 대역폭을 절약합니다. 이 기능은 현재 미리 보기 상태입니다. Azure Data Box 기반의 오프라인 백업은 [Azure Import/Export 서비스 기반의 오프라인 백업](./backup-azure-backup-import-export.md)에 비해 다음 두 가지 고유한 이점이 있습니다.

- 사용자 고유의 Azure 호환 디스크 및 커넥터를 조달 하지 않아도 됩니다. Azure Data Box에서 선택한 [Data Box SKU](https://azure.microsoft.com/services/databox/data/)와 관련된 디스크를 제공합니다.
- Azure Backup(MARS 에이전트)에서 Azure Data Box의 지원되는 SKU에 백업 데이터를 직접 쓸 수 있습니다. 이 기능을 사용하면 초기 백업 데이터에 사용할 스테이징 위치를 프로비저닝하지 않아도 됩니다. 디스크를 포맷하고 해당 데이터를 디스크에 복사하는 유틸리티도 필요하지 않습니다.

## <a name="azure-data-box-with-the-mars-agent"></a>MARS 에이전트를 사용 하 여 Azure Data Box

이 문서에서는 Azure Data Box를 사용 하 여 MARS 에이전트에서 Recovery Services 자격 증명 모음으로 대량 초기 백업 데이터를 오프 라인으로 설정 하는 방법을 설명 합니다.

## <a name="supported-platforms"></a>지원되는 플랫폼

Azure Data Box를 사용 하 여 MARS 에이전트에서 데이터를 시드 하는 프로세스는 다음 Windows Sku에서 지원 됩니다.

| **OS**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **워크스테이션**                        |                                                              |
| Windows 10 64비트                     | Enterprise, Pro, Home                                       |
| Windows 8.1 64비트                    | Enterprise, Pro                                             |
| Windows 8 64비트                      | Enterprise, Pro                                             |
| Windows 7 64비트                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Server**                             |                                                              |
| Windows Server 2019 64비트            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 64비트            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 64비트         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 64비트            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016 64비트    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2 64비트 | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 64비트    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1 64비트     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64 비트        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>백업 데이터 크기 및 지원되는 Data Box SKU

| 서버 당 백업 데이터 크기 (MARS의 압축 후) * | 지원되는 Azure Data Box SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <= 7.2 TB                                                    | [디스크 Azure Data Box](../databox/data-box-disk-overview.md) |
| >7.2 TB 및 <= 80 TB * *                                      | [Azure Data Box(100TB)](../databox/data-box-overview.md) |

* 일반적인 압축 요금은 10%에서 20% 사이입니다. <br>
* * 단일 MARS 서버에 대해 80 TB 이상의 초기 백업 데이터를 필요로 하는 경우에는에 문의 하세요 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

>[!IMPORTANT]
>단일 서버에서 초기 백업 데이터는 단일 Azure Data Box 인스턴스 또는 Azure Data Box 디스크에 포함 되어야 하며, 동일한 또는 다른 Sku의 여러 장치 간에 공유할 수 없습니다. 그러나 Azure Data Box 장치는 여러 서버의 초기 백업을 포함할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

### <a name="azure-subscription-and-required-permissions"></a>Azure 구독 및 필요한 권한

- 프로세스에는 Azure 구독이 필요 합니다.
- 이 프로세스를 수행 하려면 오프 라인 백업 정책을 수행 하도록 지정 된 사용자가 Azure 구독의 소유자 여야 합니다.
- Data Box 작업 및 Recovery Services 자격 증명 (데이터를 시드 해야 함)은 같은 구독에 있어야 합니다.
- Azure Data Box 작업 및 Recovery Services 자격 증명 모음과 연결 된 대상 저장소 계정이 동일한 지역에 있는 것이 좋습니다. 그러나이는 필요 하지 않습니다.

### <a name="get-azure-powershell-370"></a>Azure PowerShell 3.7.0 가져오기

*이는 프로세스의 가장 중요 한 필수 구성 요소입니다*. Azure PowerShell 버전 3.7.0를 설치 하기 전에 다음 검사를 수행 합니다.

#### <a name="step-1-check-the-powershell-version"></a>1 단계: PowerShell 버전 확인

1. Windows PowerShell을 열고 다음 명령을 실행 합니다.

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. 출력에 3.7.0 보다 높은 버전이 표시 되 면 "2 단계"를 수행 합니다. 그렇지 않으면 "3 단계"로 건너뜁니다.

#### <a name="step-2-uninstall-the-powershell-version"></a>2 단계: PowerShell 버전 제거

PowerShell의 현재 버전을 제거 합니다.

1. PowerShell에서 다음 명령을 실행 하 여 종속 모듈을 제거 합니다.

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. 모든 종속 모듈이 성공적으로 삭제 되었는지 확인 하려면 다음 명령을 실행 합니다.

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>3 단계: PowerShell 버전 3.7.0 설치

AzureRM 모듈이 없음을 확인 한 후에는 다음 방법 중 하나를 사용 하 여 버전 3.7.0을 설치 합니다.

- GitHub에서 [이 링크](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)를 사용 합니다.

또는

- PowerShell 창에서 다음 명령을 실행 합니다.

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Msi 파일을 사용 하 여 Azure PowerShell 설치할 수도 있습니다. 제거 하려면 제어판의 **프로그램 제거** 옵션을 사용 하 여 제거 합니다.

### <a name="order-and-receive-the-data-box-device"></a>Data Box 디바이스 주문 및 수령

Mars 및 Azure Data Box를 사용 하는 오프 라인 백업 프로세스를 수행 하려면 MARS 에이전트를 사용 하 여 오프 라인 백업을 트리거하기 전에 Data Box 장치가 배달 된 상태 여야 합니다. 요구 사항에 가장 적합 한 SKU를 주문 하려면 [백업 데이터 크기 및 지원 되는 Data Box sku](#backup-data-size-and-supported-data-box-skus)를 참조 하세요. [자습서: Azure Data Box 디스크 순서](../databox/data-box-disk-deploy-ordered.md) 를 정렬 하 고 Data Box 장치를 수신 하는 단계를 수행 합니다.

> [!IMPORTANT]
> **계정 종류**에 대해 *blobstorage* 를 선택 하지 마세요. MARS 에이전트에는 *Blobstorage* 를 선택할 때 지원 되지 않는 페이지 blob을 지 원하는 계정이 필요 합니다. Azure Data Box 작업에 대 한 대상 저장소 계정을 만들 때 **계정 종류** 로 **저장소 v2 (범용 v2)** 를 선택 합니다.

![인스턴스 세부 정보에서 계정 종류를 선택 합니다.](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>MARS 에이전트 설치 및 설정

1. MARS 에이전트의 이전 설치를 모두 제거 해야 합니다.
1. [이 웹 사이트](https://aka.ms/azurebackup_agent)에서 최신 MARS 에이전트를 다운로드 합니다.
1. *MARSAgentInstaller.exe*를 실행 하 고 백업을 저장 하려는 Recovery Services 자격 증명 모음에 [에이전트를 설치 하 고 등록](./install-mars-agent.md#install-and-register-the-agent) *하는 단계만 수행 합니다* .

   > [!NOTE]
   > Recovery Services 자격 증명 모음은 Azure Data Box 작업과 동일한 구독에 있어야 합니다.

   에이전트가 Recovery Services 자격 증명 모음에 등록 되 면 다음 섹션의 단계를 수행 합니다.

## <a name="set-up-azure-data-box-devices"></a>Azure Data Box 장치 설정

주문한 Azure Data Box SKU에 따라 다음에 나오는 해당 섹션에서 설명 하는 단계를 수행 합니다. 이 단계에서는 MARS 에이전트가 초기 백업 데이터를 식별 하 고 전송 하기 위해 Data Box 장치를 설정 하 고 준비 하는 방법을 보여 줍니다.

### <a name="set-up-azure-data-box-disks"></a>Azure Data Box 디스크 설정

하나 이상의 Azure Data Box 디스크 (최대 8TB)를 주문한 경우 여기에 설명 된 단계에 따라 [Data Box 디스크의 압축을 풀고 연결 하 고 잠금을 해제](../databox/data-box-disk-deploy-set-up.md)합니다.

>[!NOTE]
>MARS 에이전트를 포함 하는 서버에는 USB 포트가 없을 수 있습니다. 이 경우 Azure Data Box 디스크를 다른 서버 또는 클라이언트에 연결 하 고 장치의 루트를 네트워크 공유로 표시할 수 있습니다.

### <a name="set-up-azure-data-box"></a>Azure Data Box 설정

Azure Data Box 인스턴스를 주문한 경우 (최대 100 TB) 여기에 나오는 단계에 따라 [Data Box 인스턴스를 설정](../databox/data-box-deploy-set-up.md)합니다.

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Azure Data Box 인스턴스를 로컬 시스템으로 탑재

MARS 에이전트는 로컬 시스템 컨텍스트에서 작동 하므로 Azure Data Box 인스턴스가 연결 된 탑재 경로에 동일한 수준의 권한을 제공 해야 합니다.

NFS 프로토콜을 사용 하 여 Data Box 장치를 로컬 시스템으로 탑재할 수 있도록 하려면 다음을 수행 합니다.

1. MARS 에이전트가 설치 된 Windows server에서 NFS 기능에 클라이언트를 사용 하도록 설정 합니다. 대체 원본 *WIM: D: \Source\install\install.wim: 4*를 지정 합니다.
1. PsExec을 [Sysinternals](/sysinternals/downloads/psexec) 페이지에서 MARS 에이전트가 설치 된 서버로 다운로드 합니다.
1. 관리자 권한 명령 프롬프트를 열고 *PSExec.exe* 를 현재 디렉터리로 포함 하는 디렉터리를 사용 하 여 다음 명령을 실행 합니다.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   이전 명령으로 인해 열리는 명령 창이 로컬 시스템 컨텍스트에 있습니다. 이 명령 창을 사용 하 여 Azure 페이지 blob 공유를 Windows server의 네트워크 드라이브로 탑재 하는 단계를 실행 합니다.
1. [Data Box에 연결](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) 의 단계를 수행 하 여 MARS 에이전트가 있는 서버를 NFS를 통해 Data Box 장치에 연결 합니다. 로컬 시스템 명령 프롬프트에서 다음 명령을 실행 하 여 Azure 페이지 blob 공유를 탑재 합니다.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   공유가 탑재 된 후 서버에서 X:에 액세스할 수 있는지 확인 합니다. 가능 하면이 문서의 다음 섹션을 계속 진행 합니다.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Azure Data Box 장치에 초기 백업 데이터 전송

1. 서버에서 **Microsoft Azure Backup** 응용 프로그램을 엽니다.
1. **작업** 창에서 **백업 예약**을 선택 합니다.

    ![백업 일정 선택](./media/offline-backup-azure-data-box/schedule-backup.png)

1. **백업 예약 마법사**의 단계를 따릅니다.

1. **항목 추가** 단추를 선택 하 여 항목을 추가 합니다. 주문 하 고 받은 [AZURE DATA BOX SKU에서 지 원하는 크기 제한](#backup-data-size-and-supported-data-box-skus) 내에서 항목의 총 크기를 유지 합니다.

    ![백업할 항목 추가](./media/offline-backup-azure-data-box/add-items.png)

1. **파일 및 폴더** 및 **시스템 상태**에 대해 적절 한 백업 일정 및 보존 정책을 선택 합니다. 시스템 상태는 windows 클라이언트에만 적용 되 고 windows 서버에는 적용 되지 않습니다.
1. 마법사의 **초기 백업 유형 선택 (파일 및 폴더)** 페이지에서 **Microsoft Azure Data Box 디스크를 사용 하 여 전송** 옵션을 선택 하 고 **다음**을 선택 합니다.

    ![초기 백업 유형 선택](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Azure 구독에 대 한 소유자 액세스 권한이 있는 사용자 자격 증명을 사용 하 여 메시지가 표시 되 면 Azure에 로그인 합니다. 이 작업을 완료 한 후에는 다음과 유사한 페이지가 표시 됩니다.

    ![리소스 만들기 및 필요한 권한 적용](./media/offline-backup-azure-data-box/creating-resources.png)

   그러면 MARS 에이전트는 구독에서 배달 된 상태에 있는 Data Box 작업을 페치합니다.

    ![구독 ID에 대 한 Data Box 작업 페치](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Data Box 디스크의 압축을 풀고 연결 하 고 잠금을 해제 한 올바른 Data Box 순서를 선택 합니다. **다음**을 선택합니다.

    ![Data Box 주문 선택](./media/offline-backup-azure-data-box/select-databox-order.png)

1. **장치 검색 Data Box** 페이지에서 **장치 검색** 을 선택 합니다. 이 작업을 수행 하면 MARS 에이전트가 로컬로 연결 된 Azure Data Box 디스크를 검색 하 여 검색 합니다.

    ![장치 검색 Data Box](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Azure Data Box 인스턴스를 네트워크 공유로 연결 하는 경우 (USB 포트를 사용할 수 없거나 100-TB Data Box 장치를 정렬 하 여 탑재 했기 때문에) 먼저 검색에 실패 합니다. Data Box 장치의 네트워크 경로를 입력 하는 옵션이 제공 됩니다.

    ![네트워크 경로를 입력 하십시오.](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Azure Data Box 디스크의 루트 디렉터리에 대한 네트워크 경로를 제공합니다. 이 디렉터리는 *Pageblob*이름으로 디렉터리를 포함 해야 합니다.
    >
    >![Azure Data Box 디스크의 루트 디렉터리](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >예를 들어 디스크의 경로가이 `\\mydomain\myserver\disk1\` 고 *Disk1* 에 *pageblob*이라는 디렉터리가 포함 되어 있는 경우 MARS 에이전트 마법사 페이지에서 입력 하는 경로는 `\\mydomain\myserver\disk1\` 입니다.
    >
    >[Azure Data Box 100-TB 장치를 설정한](#set-up-azure-data-box-devices)경우 `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` 장치에 대 한 네트워크 경로로를 입력 합니다.

1. **다음**을 선택 하 고 다음 페이지에서 **마침** 을 선택 하 Azure Data Box를 사용 하 여 오프 라인 백업 구성으로 백업 및 보존 정책을 저장 합니다.

   다음 페이지에서는 정책이 성공적으로 저장 되었음을 확인 합니다.

    ![정책을 저장 했습니다.](./media/offline-backup-azure-data-box/policy-saved.png)

1. 이전 페이지에서 **닫기** 를 선택 합니다.

1. MARS 에이전트 콘솔의 **작업** 창에서 **지금 백업** 을 선택 합니다. 마법사 페이지에서 **백업을** 선택 합니다.

    ![지금 백업 마법사](./media/offline-backup-azure-data-box/backup-now.png)

MARS 에이전트는 선택한 데이터를 Azure Data Box 장치에 백업 하기 시작 합니다. 이 프로세스는 몇 시간에서 며칠까지 걸릴 수 있습니다. 시간은 MARS 에이전트가 있는 서버와 Azure Data Box 디스크 간의 파일 수 및 연결 속도에 따라 달라 집니다.

데이터 백업이 완료 되 면 MARS 에이전트에이 페이지와 유사한 페이지가 표시 됩니다.

![표시 된 백업 진행률](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>백업 후 단계

이 섹션에서는 Azure Data Box Disk에 대 한 데이터 백업이 성공한 후 수행 해야 하는 단계를 설명 합니다.

- 이 문서의 단계에 따라 [Azure에 Azure Data Box 디스크를 배송](../databox/data-box-disk-deploy-picked-up.md)합니다. Azure Data Box 100-TB 장치를 사용 하는 경우 다음 단계에 따라 [Azure Data Box 장치를 Azure에 제공](../databox/data-box-deploy-picked-up.md)합니다.

- Azure Portal에서 [Data Box 작업을 모니터링](../databox/data-box-disk-deploy-upload-verify.md)합니다. Azure Data Box 작업이 완료 되 면 MARS 에이전트는 다음 예약 된 백업 시 저장소 계정에서 Recovery Services 자격 증명 모음으로 데이터를 자동으로 이동 합니다. 그런 다음 복구 지점을 성공적으로 만든 경우 백업 작업을 *작업 완료* 로 표시 합니다.

    >[!NOTE]
    >MARS 에이전트는 정책을 만드는 동안 예약 된 시간에 백업을 트리거합니다. 이러한 작업은 작업이 완료 될 때까지 "Azure Data Box 작업이 완료 될 때까지 기다리는 중" 플래그를 지정 합니다.

- MARS 에이전트가 초기 백업에 해당 하는 복구 지점을 만든 후에는 Azure Data Box 작업과 연결 된 특정 콘텐츠 또는 저장소 계정을 삭제할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

MARS (Microsoft Azure Recovery Services) 에이전트가 테 넌 트에서 사용자를 위해 Azure Active Directory (Azure AD) 응용 프로그램을 만듭니다. 이 응용 프로그램에는 오프 라인 시드 정책을 구성할 때 생성 및 업로드 된 인증용 인증서가 필요 합니다. Azure PowerShell를 사용 하 여 Azure AD 응용 프로그램에 인증서를 만들고 업로드 합니다.

### <a name="problem"></a>문제

오프 라인 백업을 구성할 때 Azure PowerShell cmdlet의 버그로 인해 문제가 발생할 수 있습니다. MAB 에이전트가 만든 동일한 Azure AD 응용 프로그램에 여러 인증서를 추가 하지 못할 수 있습니다. 이 문제는 동일한 서버 또는 다른 서버에 대 한 오프 라인 시드 정책을 구성한 경우에 영향을 줍니다.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>문제가이 특정 근본 원인으로 인해 발생 했는지 확인 합니다.

문제가 앞에서 설명한 것과 동일한 지 여부를 확인 하려면 다음 단계 중 하나를 수행 합니다.

#### <a name="step-1-of-verification"></a>1 단계 확인

오프 라인 백업을 구성할 때 MAB 콘솔에 다음 오류 메시지가 표시 되는지 확인 합니다.

![현재 Azure 계정에 대 한 오프 라인 백업 정책을 만들 수 없습니다.](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>2 단계 확인

1. 설치 경로에서 **임시** 폴더를 엽니다. 기본 임시 폴더 경로는 *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*입니다. *Cbuicurr* 파일을 찾고 파일을 엽니다.

1. *Cis Icurr* 파일에서 마지막 줄로 스크롤하고 문제가이 오류 메시지와 동일한 지 확인 `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` 합니다.

### <a name="workaround"></a>해결 방법

이 문제를 해결 하기 위한 해결 방법으로 다음 단계를 수행 하 고 정책 구성을 다시 시도 합니다.

#### <a name="step-1-of-workaround"></a>해결 방법 1 단계

가져오기 또는 내보내기 작업을 만들 구독에 대 한 관리자 액세스 권한이 있는 다른 계정을 사용 하 여 MAB UI에 표시 되는 PowerShell에 로그인 합니다.

#### <a name="step-2-of-workaround"></a>해결 방법 2 단계

다른 서버에 오프 라인 시드가 구성 되어 있지 않고 다른 서버가 응용 프로그램에 종속 되어 있지 않은 경우 `AzureOfflineBackup_<Azure User Id>` 이 응용 프로그램을 삭제 합니다. **Azure Portal**  >  **Azure Active Directory**  >  **앱 등록**를 선택 합니다.

>[!NOTE]
> `AzureOfflineBackup_<Azure User Id>`응용 프로그램에 다른 오프 라인 시드가 구성 되어 있지 않은지 확인 하 고 다른 서버가이 응용 프로그램에 종속 되어 있지 않은지 확인 하십시오. **Settings**  >  **공개 키** 섹션 아래의 설정**키** 로 이동 합니다. 다른 공개 키를 추가 하지 않아야 합니다. 참조는 다음 스크린샷을 참조 하세요.
>
>![퍼블릭 키](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>3단계

오프 라인 백업에 대해 구성 하려는 서버에서 다음 작업을 수행 합니다.

1. **컴퓨터 인증서 응용 프로그램 관리**  >  **개인** 탭으로 이동 하 여 이름이 인 인증서를 찾습니다 `CB_AzureADCertforOfflineSeeding_<ResourceId>` .

2. 인증서를 선택 하 고, **모든 작업**을 마우스 오른쪽 단추로 클릭 하 고, 개인 키 없이 .cer 형식으로 **내보내기** 를 선택 합니다.

3. 2 단계에서 언급 한 Azure offline backup 응용 프로그램으로 이동 합니다. **설정**  >  **키**  >  **공개 키 업로드**를 선택 합니다. 이전 단계에서 내보낸 인증서를 업로드 합니다.

    ![공개 키 업로드](./media/offline-backup-azure-data-box/upload-public-key.png)

4. 서버에서 실행 창에 **regedit** 를 입력 하 여 레지스트리를 엽니다.

5. 레지스트리 *컴퓨터 \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider.로 이동 합니다.* **Cloudbackupprovider**를 마우스 오른쪽 단추로 클릭 하 고 이름이 인 새 문자열 값을 추가 `AzureADAppCertThumbprint_<Azure User Id>` 합니다.

    >[!NOTE]
    > Azure 사용자 ID를 가져오려면 다음 작업 중 하나를 수행합니다.
    >
    >- Azure에 연결된 PowerShell에서 `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` 명령을 실행합니다.
    > - `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup`이름이 *currentuserid*인 레지스트리 경로로 이동 합니다.

6. 이전 단계에서 추가 된 문자열을 마우스 오른쪽 단추로 클릭 하 고 **수정**을 선택 합니다. 값에서 2 단계에서 내보낸 인증서의 지문을 제공 합니다. **확인**을 선택합니다.

7. 지문 값을 가져오려면 인증서를 두 번 클릭 합니다. **세부 정보** 탭을 선택 하 고 지문 필드가 표시 될 때까지 아래로 스크롤합니다. **손 도장 (Thumbprint**)을 선택 하 고 값을 복사 합니다.

    ![인증서의 지문 필드](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>질문

직면 한 문제에 대 한 질문이 나 설명은를 문의 하세요 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .
