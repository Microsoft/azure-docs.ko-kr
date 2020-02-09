---
title: Azure Data Box를 사용 하 여 오프 라인 백업
description: Azure Data Box를 사용 하 여 MARS 에이전트에서 Azure Recovery Services Vault로 대량 초기 백업 데이터를 오프 라인으로 설정 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 553939df8aa7a1b097b2cd6c7f29365302101324
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027031"
---
# <a name="azure-backup-offline-backup-using-azure-data-box"></a>Azure Data Box를 사용 하 여 오프 라인 백업 Azure Backup

[Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) 서비스를 사용 하 여 Azure Recovery Services 자격 증명 모음에 대한 대량 초기 MARS 백업 (네트워크를 사용 하지 않고)의 초기값을 지정할 수 있습니다.  이렇게 하면 시간 및 네트워크 대역폭이 절약 되므로 대기 시간이 긴 네트워크를 통해 많은 양의 백업 데이터를 온라인으로 이동 하는 경우에 사용 됩니다. 이 기능은 현재 미리 보기 상태입니다. Azure Data Box 기반 오프 라인 백업에서는 [Azure Import/Export 서비스 기반 오프 라인 백업](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)에 비해 두 가지 이점을 제공 합니다.

1. 사용자 고유의 Azure 호환 디스크 및 커넥터를 조달 하지 않아도 됩니다. Azure Data Box 서비스는 선택한 [DATA BOX SKU](https://azure.microsoft.com/services/databox/data/) 와 연결 된 디스크를 제공 합니다.

2. Azure Backup (MARS 에이전트)는 Azure Data Box의 지원 되는 Sku에 백업 데이터를 직접 쓸 수 있습니다. 이렇게 하면 초기 백업 데이터에 스테이징 위치를 프로 비전 할 필요가 없으며, 해당 데이터를 포맷 하 고 디스크에 복사 하는 유틸리티의 필요성이 없어집니다.

## <a name="azure-data-box-with-mars-agent"></a>MARS 에이전트를 사용 하 여 Azure Data Box

이 문서에서는 Azure Data Box를 사용 하 여 MARS 에이전트에서 Azure Recovery Services Vault로 대량 초기 백업 데이터를 오프 라인으로 설정 하는 방법을 설명 합니다. 이 문서는 다음 부분으로 나뉘어 있습니다.

* 지원되는 플랫폼
* 지원 되는 Data Box Sku
* 필수 조건
* MARS 에이전트 설정
* 설치 Azure Data Box
* Azure Data Box에 백업 데이터 전송
* 백업 후 단계

## <a name="supported-platforms"></a>지원되는 플랫폼

Azure Data Box를 사용 하 여 MARS 에이전트에서 데이터를 시드 하는 프로세스는 다음 Windows Sku에서 지원 됩니다.

| **OS**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **워크스테이션과**                        |                                                              |
| Windows 10 64 비트                     | Enterprise, Pro, Home                                       |
| Windows 8.1 64 비트                    | Enterprise, Pro                                             |
| Windows 8 64 비트                      | Enterprise, Pro                                             |
| Windows 7 64 비트                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Server**                             |                                                              |
| Windows Server 2019 64 비트            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 64 비트            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 64 비트         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 64 비트            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016 64 비트    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2 64 비트 | Standard, Workgroup, 필수                              |
| Windows Storage Server 2012 64 비트    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1 64 비트     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64 비트        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>백업 데이터 크기 및 지원 되는 Data Box Sku

| 서버 당 백업 데이터 크기 (MARS의 게시 압축) * | 지원 되는 Azure Data Box SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| < = 7.2 TB                                                    | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7.2 TB 및 < = 80 TB * *                                      | [Azure Data Box (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

\* 일반적인 압축 요금은 10-20% 마다 다릅니다. <br>
\* * 단일 MARS 서버에 대해 80 TB 이상의 초기 백업 데이터를 필요로 하는 경우 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) 에 연결 합니다.

>[!IMPORTANT]
>단일 서버에서 초기 백업 데이터는 단일 Azure Data Box 또는 Azure Data Box Disk에 포함 되어야 하며, 동일한 또는 다른 Sku의 여러 장치 간에 공유할 수 없습니다. 그러나 Azure Data Box 장치는 여러 서버의 초기 백업을 포함할 수 있습니다.

## <a name="pre-requisites"></a>필수 조건

### <a name="azure-subscription-and-required-permissions"></a>Azure 구독 및 필요한 권한

* 프로세스에 Azure 구독이 필요 합니다.
* 이 프로세스를 수행 하려면 오프 라인 백업 정책을 수행 하도록 지정 된 사용자가 Azure 구독의 "소유자" 여야 합니다.
* Data Box 작업 및 Recovery Services 자격 증명 (데이터를 시드 해야 함)은 같은 구독에 있어야 합니다.
* Azure Data Box 작업 및 Recovery Services 자격 증명 모음과 연결 된 대상 저장소 계정이 동일한 지역에 있는 것이 좋습니다. 그러나이 방법은 필요 하지 않습니다.

### <a name="get-azure-powershell-370"></a>Azure PowerShell 3.7.0 가져오기

**이는 프로세스의 가장 중요 한 필수**구성 요소입니다. Azure PowerShell (ver)를 설치 하기 전에 3.7.0)에서 다음 검사를 수행 합니다. * *

#### <a name="step-1-check-powershell-version"></a>1 단계: PowerShell 버전 확인

* Windows PowerShell을 열고 다음 명령을 실행합니다.

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

* 출력에 **3.7.0**보다 높은 버전이 표시 되는 경우 아래 2 단계를 수행 합니다. 그렇지 않은 경우, 3단계로 건너뜁니다.

#### <a name="step-2-uninstall-the-powershell-version"></a>2 단계: PowerShell 버전 제거

다음 작업을 수행 하 여 최신 버전의 PowerShell을 제거 합니다.

* PowerShell에서 다음 명령을 실행 하 여 종속 모듈을 제거 합니다.

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

* 다음 명령을 실행 하 여 모든 종속 모듈이 성공적으로 삭제 되었는지 확인 합니다.

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>3 단계: PowerShell 버전 3.7.0 설치

AzureRM 모듈이 없음을 확인 한 후에는 다음 방법 중 하나를 사용 하 여 버전 3.7.0을 설치 합니다.

* GitHub에서 [링크](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

또는

* PowerShell 창에서 다음 명령을 실행 합니다.

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Msi 파일을 사용 하 여 설치 된 Azure PowerShell 수도 있습니다. 제거 하려면 제어판의 프로그램 제거 옵션을 사용 하 여 제거 합니다.

### <a name="order-and-receive-the-data-box-device"></a>Data Box 장치 순서 및 수신

MARS 및 Azure Data Box를 사용 하는 오프 라인 백업 프로세스에서는 MARS 에이전트를 사용 하 여 오프 라인 백업을 트리거하기 전에 필요한 Data Box 장치가 "배달 됨" 상태 여야 합니다. 요구 사항에 가장 적합 한 SKU를 주문 하려면 [백업 데이터 크기 및 지원 되는 Data Box sku](#backup-data-size-and-supported-data-box-skus) 를 참조 하세요. [이 문서의](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) 단계에 따라 Data Box 장치를 주문 하 고 수신 합니다.

>[!IMPORTANT]
>계정 종류에 대해 BlobStorage를 선택 하지 마세요. MARS 에이전트에는 BlobStorage를 선택할 때 지원 되지 않는 페이지 Blob을 지 원하는 계정이 필요 합니다. Azure Data Box 작업에 대한 대상 저장소 계정을 만들 때 계정 종류로 *저장소 v2* (*범용 v2*)를 선택 하는 것이 좋습니다.

![인스턴스 세부 정보에서 계정 종류를 선택 합니다.](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-setup-the-mars-agent"></a>MARS 에이전트 설치 및 설정

* MARS 에이전트의 이전 설치를 모두 제거 해야 합니다.

* [여기](https://aka.ms/azurebackup_agent)에서 최신 MARS 에이전트를 다운로드 합니다.

* *Aagentsetup.exe* 를 실행 하 고, 백업을 저장 하려는 Recovery Services 자격 증명 모음에 [에이전트를 설치](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) 하 고 등록**하는 단계만** 수행 합니다.

  >[!NOTE]
  > Recovery Services 자격 증명 모음은 Azure Data Box 작업과 동일한 구독에 있어야 합니다.

* 에이전트가 Recovery Services 자격 증명 모음에 등록 되 면 아래 섹션의 단계를 따릅니다.

## <a name="setup-azure-data-box-devices"></a>Azure Data Box 장치 설치

주문한 Azure Data Box SKU에 따라 아래 해당 섹션에서 설명 하는 단계를 수행 하 여 MARS 에이전트가 초기 백업 데이터를 식별 하 고 전송할 Data Box 장치를 설정 하 고 준비 합니다.

### <a name="setup-azure-data-box-disk"></a>설치 Azure Data Box Disk

하나 이상의 Azure Data Box 디스크 (최대 8TB)를 주문한 경우 여기에 설명 된 단계에 따라 [Data Box Disk의 압축을 풀고 연결 하 고 잠금을 해제](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up)합니다.

>[!NOTE]
>MARS 에이전트를 포함 하는 서버에는 USB 포트가 없을 수 있습니다. 이 경우 Azure Data Box Disk을 다른 서버/클라이언트에 연결 하 고 장치의 루트를 네트워크 공유로 표시할 수 있습니다.

### <a name="setup-azure-data-box"></a>설치 Azure Data Box

Azure Data Box (최대 100 TB)를 주문한 경우 여기에 설명 된 단계에 따라 [Data Box를 설정](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)합니다.

#### <a name="mount-your-azure-data-box-as-local-system"></a>Azure Data Box를 로컬 시스템으로 탑재

MARS 에이전트는 로컬 시스템 컨텍스트에서 작동 하므로 Azure Data Box 연결 된 탑재 경로에 동일한 수준의 권한이 제공 되어야 합니다. NFS 프로토콜을 사용 하 여 Data Box 장치를 로컬 시스템으로 탑재할 수 있는지 확인 하려면 다음 단계를 수행 합니다.

* MARS 에이전트가 설치 된 Windows Server에서 NFS 용 클라이언트 기능을 사용 하도록 설정 합니다.<br>
  대체 원본 지정: *WIM: D: \Source\install\install.wim: 4*

* <https://download.sysinternals.com/files/PSTools.zip>에서 MARS 에이전트가 설치 된 서버로 PSExec를 다운로드 합니다.

* 관리자 권한 명령 프롬프트를 열고 PSExec가 포함 된 디렉터리를 현재 디렉터리로 사용 하 여 다음 명령을 실행 합니다.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

* 위의 명령 결과로 열리는 명령 창은 로컬 시스템 컨텍스트에 있습니다. 이 명령 창을 사용 하 여 Azure 페이지 Blob 공유를 Windows Server의 네트워크 드라이브로 탑재 하는 단계를 실행 합니다.

* NFS를 통해 MARS Data Box 에이전트와 서버를 연결 하 고 로컬 시스템 명령 프롬프트에서 다음 명령을 실행 하 여 Azure 페이지 Blob 공유를 탑재 하려면 [여기](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) 의 단계를 따르세요.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

* 탑재 되 면 서버에서 X:에 액세스할 수 있는지 확인 합니다. 그렇다면이 문서의 다음 섹션을 계속 진행 합니다.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Azure Data Box 장치에 초기 백업 데이터 전송

* 서버에서 **Microsoft Azure Backup** 응용 프로그램을 엽니다.

* **작업** 창에서 **백업 예약** 을 클릭 합니다.

    ![백업 예약을 클릭 합니다.](./media/offline-backup-azure-data-box/schedule-backup.png)

* **백업 예약 마법사** 의 단계를 따릅니다.

* 항목의 총 크기가 주문 하 고 받은 [AZURE DATA BOX SKU에서 지원 되는 크기 제한](#backup-data-size-and-supported-data-box-skus) 내에 있도록 **항목을 추가** 합니다.

    ![백업할 항목 추가](./media/offline-backup-azure-data-box/add-items.png)

* 파일 및 폴더 및 시스템 상태에 대해 적절 한 백업 일정 및 보존 정책 선택 (시스템 상태는 windows 서버에만 적용 되 고 Windows 클라이언트에는 적용 되지 않음)

* 마법사의 **초기 백업 유형 선택 (파일 및 폴더)** 화면에서 **Microsoft Azure Data Box 디스크를 사용 하 여 전송** 옵션을 선택 하 고 **다음** 을 클릭 합니다.

    ![초기 백업 유형 선택](./media/offline-backup-azure-data-box/initial-backup-type.png)

* Azure 구독에 대한 소유자 액세스 권한이 있는 사용자 자격 증명을 사용 하 여 메시지가 표시 되 면 Azure에 로그인 합니다. 작업을 완료 한 후 아래와 같은 화면이 표시 됩니다.

    ![리소스 만들기 및 필요한 권한 적용](./media/offline-backup-azure-data-box/creating-resources.png)

* 그러면 MARS 에이전트는 구독에서 "배달 됨" 상태인 Data Box 작업을 가져옵니다.

    ![구독 ID에 대한 databox 작업을 가져오는 중](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

* Data Box 디스크의 압축을 풀고 연결 하 고 잠금을 해제 한 올바른 데이터 상자 순서를 선택 합니다. **다음**을 클릭합니다.

    ![Data Box 순서 선택](./media/offline-backup-azure-data-box/select-databox-order.png)

* **Data Box 장치 검색** 화면에서 **장치 검색** 을 클릭 합니다. 이렇게 하면 MARS 에이전트가 로컬로 연결 된 Azure Data Box 디스크를 검색 하 고 아래와 같이 검색 합니다.

    ![장치 검색 Data Box](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Azure Data Box 네트워크 공유로 연결 된 경우 (USB 포트를 사용할 수 없거나 100 TB Data Box 장치를 정렬 하 여 탑재 했기 때문에) 검색은 처음에 실패 하지만 Data Box 장치의 네트워크 경로를으로 입력 하는 옵션을 제공 합니다. hown 아래에 있습니다.

    ![네트워크 경로를 입력 하십시오.](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Azure Data Box 디스크의 루트 디렉터리에 대한 네트워크 경로를 제공 합니다. 이 디렉터리에는 아래와 같이 이름이 *Pageblob* 인 디렉터리가 포함 되어야 합니다.
    >
    >![Azure Data Box 디스크의 루트 디렉터리](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >예를 들어 디스크 경로가 `\\mydomain\myserver\disk1\`이 고 *disk1* 에 *pageblob*이라는 디렉터리가 포함 된 경우 MARS 에이전트 마법사에서 제공 하는 경로는 `\\mydomain\myserver\disk1\`
    >
    >[Azure Data Box 100 TB 장치를 설치](#setup-azure-data-box)하는 경우 장치에 대한 네트워크 경로로 다음을 제공 `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`

* 다음 **을 클릭** 하 고 다음 화면에서 **마침** 을 클릭 하 Azure Data Box를 사용 하 여 오프 라인 백업 구성으로 백업 및 보존 정책을 저장 합니다.

* 다음 화면은 정책이 성공적으로 저장 되었음을 확인 합니다.

    ![정책을 저장 했습니다.](./media/offline-backup-azure-data-box/policy-saved.png)

* 위의 화면에서 **닫기** 를 클릭 합니다.

* MARS 에이전트 콘솔의 **작업** 창에서 ***지금** 백업을 클릭 하 고 아래와 같이 마법사 **화면에서 백업을 클릭** 합니다.

    ![지금 백업 마법사](./media/offline-backup-azure-data-box/backup-now.png)

* MARS 에이전트는 사용자가 선택한 데이터를 Azure Data Box 장치에 백업 하기 시작 합니다. 이 작업은 MARS 에이전트가 있는 서버와 Azure Data Box Disk 간의 연결 속도와 파일 수에 따라 몇 일에서 며칠까지 걸릴 수 있습니다.

* 데이터 백업이 완료 되 면 MARS 에이전트에 아래와 유사한 화면이 표시 됩니다.

    ![표시 된 백업 진행률](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>백업 후 단계

이 섹션에서는 Azure Data Box Disk에 대한 데이터 백업이 성공적으로 완료 되 면 수행 하는 단계를 설명 합니다.

* 이 문서의 단계에 따라 [Azure에 Azure Data Box 디스크를 배송](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up)합니다. Azure Data Box 100-TB 장치를 사용 하는 경우 다음 단계에 따라 [Azure에 Azure Data Box를 제공](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)합니다.

* Azure Portal에서 [Data Box 작업을 모니터링](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) 합니다. Azure Data Box 작업이 "완료" 되 면 MARS 에이전트는 예약 된 다음 백업 시 저장소 계정에서 Recovery Services 자격 증명 모음으로 데이터를 자동으로 이동 합니다. 그런 다음 복구 지점을 성공적으로 만든 경우 백업 작업을 "작업 완료 됨"으로 표시 합니다.

    >[!NOTE]
    >MARS 에이전트는 정책이 생성 되는 동안 예약 된 시간에 백업을 트리거합니다. 그러나 이러한 작업은 작업이 완료 될 때까지 "Azure Data Box 작업이 완료 되기를 기다리는 중입니다." 라는 플래그를 지정 합니다.

* MARS 에이전트가 초기 백업에 해당 하는 복구 지점을 만든 후에는 Azure Data Box 작업과 연결 된 저장소 계정 또는 특정 내용을 삭제할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

MAB (Microsoft Azure Backup) 에이전트가 테넌트에 Azure AD 응용 프로그램을 만듭니다. 이 응용 프로그램에는 오프 라인 시드 정책을 구성할 때 생성 및 업로드 되는 인증을 위한 인증서가 필요 합니다. Azure AD 응용 프로그램에 인증서를 만들고 업로드 하는 데 Azure PowerShell를 사용 합니다.

### <a name="issue"></a>문제

오프 라인 백업을 구성할 때 문제가 발생할 수 있습니다. Azure PowerShell cmdlet의 버그로 인해 MAB 에이전트가 만든 동일한 Azure AD 응용 프로그램에 여러 인증서를 추가할 수 없습니다. 이렇게 하면 동일한 서버 또는 다른 서버에 대한 오프 라인 시드 정책을 구성한 경우에 영향을 줍니다.

### <a name="how-to-verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>문제가이 특정 근본 원인으로 인 한 것인지 확인 하는 방법

위의 문제로 인해 실패가 발생 하는지 확인 하려면 다음 단계 중 하나를 수행 합니다.

#### <a name="step-1"></a>1단계

오프 라인 백업을 구성할 때 MAB 콘솔에 다음 오류 메시지가 표시 되는지 확인 합니다.

![현재 Azure 계정에 대한 오프 라인 백업 정책을 만들 수 없습니다.](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>2단계

* 설치 경로에서 **임시** 폴더를 엽니다 (기본 임시 폴더 경로는 *C:\Program Files\Microsoft Azure Recovery Services agent\temp*). **C를** 검색 하 고 파일을 엽니다.

* **Cis Icurr** 파일에서 마지막 줄로 스크롤하고 `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`로 인해 오류가 발생 했는지 확인 합니다.

### <a name="workaround"></a>해결 방법

이 문제를 해결 하기 위한 해결 방법으로 다음 단계를 수행 하 고 정책 구성을 다시 시도 합니다.

#### <a name="first-step"></a>첫 번째 단계

가져오기 내보내기 작업을 만들 구독에 대한 관리자 액세스 권한이 있는 다른 계정을 사용 하 여 MAB UI에 표시 되는 PowerShell에 로그인 합니다.

#### <a name="second-step"></a>두 번째 단계

다른 서버에 오프 라인 시드가 구성 되어 있지 않고 다른 서버가 `AzureOfflineBackup_<Azure User Id>` 응용 프로그램에 종속 되어 있지 않은 경우 **Azure Portal** > **Azure Active Directory** > 앱 등록에서이 응용 프로그램을 삭제 **합니다.**

>[!NOTE]
> 응용 프로그램 `AzureOfflineBackup_<Azure User Id>`에 다른 오프 라인 시드가 구성 되어 있지 않고 다른 서버가이 응용 프로그램에 종속 되어 있지 않은지 확인 합니다. **공개 키** 섹션 아래의 **설정** > **키** 로 이동 하 여 다른 공개 키를 추가 하지 않아야 합니다. 참조는 다음 스크린샷을 참조 하세요.
>
>![공개 키](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="third-step"></a>세 번째 단계

오프 라인 백업을 구성 하려는 서버에서 다음 작업을 수행 합니다.

1. **컴퓨터 인증서 응용 프로그램 관리** > **개인** 탭을 열고 이름이 인 인증서를 찾습니다 `CB_AzureADCertforOfflineSeeding_<ResourceId>`

2. 위의 인증서를 선택 하 고, **모든 작업** 을 마우스 오른쪽 단추로 클릭 하 고 개인 키를 사용 하지 않고를 .cer 형식으로 **내보냅니다** .

3. **지점 2**에서 언급 한 Azure Offline Backup 응용 프로그램으로 이동 합니다. **설정** > **키** > **공개 키 업로드** 에서 위의 단계에서 내보낸 인증서를 업로드 합니다.

    ![공개 키 업로드](./media/offline-backup-azure-data-box/upload-public-key.png)

4. 서버에서 실행 창에 **regedit** 를 입력 하 여 레지스트리를 엽니다.

5. 레지스트리 *컴퓨터 \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider.로 이동 합니다.* **Cloudbackupprovider** 를 마우스 오른쪽 단추로 클릭 하 고 이름이 인 새 문자열 값을 추가 `AzureADAppCertThumbprint_<Azure User Id>`

    >[!NOTE]
    > Azure 사용자 Id를 가져오려면 다음 작업 중 하나를 수행 합니다.
    >
    >1. Azure 연결 된 PowerShell에서 `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as defined in the portal”` 명령을 실행 합니다.
    > 2. 레지스트리 경로 ( *Computer \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\DbgSettings\OnlineBackup*;)로 이동 합니다. 이름: *Currentuserid*

6. 위의 단계에서 추가 된 문자열을 마우스 오른쪽 단추로 클릭 하 고 **수정**을 선택 합니다. 값에서 **지점 2** 에서 내보낸 인증서의 지문을 입력 하 고 **확인**을 클릭 합니다.

7. 지문 값을 가져오려면 인증서를 두 번 클릭 한 다음 **세부 정보** 탭을 선택 하 고 지문 필드가 표시 될 때까지 아래로 스크롤합니다. **손 도장 (Thumbprint** )을 클릭 하 고 값을 복사 합니다.

    ![인증서의 지문 필드](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>문의 사항

질문이 나 설명에 대해 직면 한 문제에 대한 자세한 내용은을 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)
