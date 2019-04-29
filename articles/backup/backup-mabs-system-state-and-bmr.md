---
title: Azure Backup Server로 시스템 상태 보호 및 완전 복구로 복원
description: Azure Backup Server를 사용하여 시스템 상태를 백업하고 BMR(완전 복구) 보호를 제공할 수 있습니다.
services: backup
author: rayne-wiselman
manager: carmonm
keywords: ''
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: raynew
ms.openlocfilehash: 35ab150670cdc27efcedca233928e0c2184aeca6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116178"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Azure Backup Server로 시스템 상태 백업 및 완전 복구로 복원

Azure Backup Server는 시스템 상태를 백업하고 BMR(완전 복구) 보호를 제공합니다.

*   **시스템 상태 백업**: 운영 체제 파일을 백업하므로 컴퓨터가 시작될 때 복구할 수 있지만, 시스템 파일과 레지스트리가 손실됩니다. 시스템 상태 백업에는 다음이 포함됩니다.
    * 도메인 구성원: 부팅 파일, COM+ 클래스 등록 데이터베이스, 레지스트리
    * 도메인 컨트롤러: Windows Server Active Directory(NTDS), 부팅 파일, COM+ 클래스 등록 데이터베이스, 레지스트리, 시스템 볼륨(SYSVOL)
    * 클러스터 서비스를 실행하는 컴퓨터: 클러스터 서버 메타데이터
    * 인증서 서비스를 실행하는 컴퓨터: 인증서 데이터
* **완전 복구 백업**: 운영 체제 파일 및 중요 볼륨의 모든 데이터를 백업합니다(사용자 데이터 제외). 기본적으로 BMR 백업에는 시스템 상태 백업이 포함됩니다. BMR 백업은 컴퓨터가 시작되지 않고 모든 것을 복구해야 할 경우 보호 기능을 제공합니다.

다음 표에는 백업 및 복구할 수 있는 항목이 요약되어 있습니다. 시스템 상태 및 BMR로 보호할 수 있는 앱 버전에 대한 자세한 내용은 [Azure Backup Server로 백업할 수 있는 항목](backup-mabs-protection-matrix.md)을 참조하세요.

|Backup|문제|Azure Backup Server 백업에서 복구|시스템 상태 백업에서 복구|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**파일 데이터**<br /><br />정기적인 데이터 백업<br /><br />BMR/시스템 상태 백업|손실된 파일 데이터|Y|N|N|
|**파일 데이터**<br /><br />파일 데이터의 Azure Backup Server 백업<br /><br />BMR/시스템 상태 백업|손실되거나 손상된 운영 체제|N|Y|Y|
|**파일 데이터**<br /><br />파일 데이터의 Azure Backup Server 백업<br /><br />BMR/시스템 상태 백업|손실된 서버(데이터 볼륨 그대로 유지)|N|N|Y|
|**파일 데이터**<br /><br />파일 데이터의 Azure Backup Server 백업<br /><br />BMR/시스템 상태 백업|손실된 서버(데이터 볼륨 손실)|Y|아닙니다.|예(BMR, 이후 백업된 파일 데이터의 정기적인 복구 수행)|
|**SharePoint 데이터**:<br /><br />팜 데이터의 Azure Backup Server 백업<br /><br />BMR/시스템 상태 백업|손실된 사이트, 목록, 목록 항목, 문서|Y|N|N|
|**SharePoint 데이터**:<br /><br />팜 데이터의 Azure Backup Server 백업<br /><br />BMR/시스템 상태 백업|손실되거나 손상된 운영 체제|N|Y|Y|
|**SharePoint 데이터**:<br /><br />팜 데이터의 Azure Backup Server 백업<br /><br />BMR/시스템 상태 백업|재해 복구|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Hyper-V 호스트 또는 게스트의 Azure Backup Server 백업<br /><br />호스트의 BMR/시스템 상태 백업|손실된 VM|Y|N|N|
|Hyper-V<br /><br />Hyper-V 호스트 또는 게스트의 Azure Backup Server 백업<br /><br />호스트의 BMR/시스템 상태 백업|손실되거나 손상된 운영 체제|N|Y|Y|
|Hyper-V<br /><br />Hyper-V 호스트 또는 게스트의 Azure Backup Server 백업<br /><br />호스트의 BMR/시스템 상태 백업|손실된 Hyper-V 호스트(VM 그대로 유지)|N|N|Y|
|Hyper-V<br /><br />Hyper-V 호스트 또는 게스트의 Azure Backup Server 백업<br /><br />호스트의 BMR/시스템 상태 백업|손실된 Hyper-V 호스트(VM 손실)|N|N|Y<br /><br />BMR, 이후 정기적인 Azure Backup Server 복구 수행|
|SQL Server/Exchange<br /><br />Azure Backup Server 앱 백업<br /><br />BMR/시스템 상태 백업|손실된 앱 데이터|Y|N|N|
|SQL Server/Exchange<br /><br />Azure Backup Server 앱 백업<br /><br />BMR/시스템 상태 백업|손실되거나 손상된 운영 체제|N|y|Y|
|SQL Server/Exchange<br /><br />Azure Backup Server 앱 백업<br /><br />BMR/시스템 상태 백업|손실된 서버(데이터베이스/트랜잭션 로그 그대로 유지)|N|N|Y|
|SQL Server/Exchange<br /><br />Azure Backup Server 앱 백업<br /><br />BMR/시스템 상태 백업|손실된 서버(데이터베이스/트랜잭션 로그 손실)|N|N|Y<br /><br />BMR 복구, 이후 정기적인 Azure Backup Server 복구 수행|

## <a name="how-system-state-backup-works"></a>시스템 상태 백업의 작동 방식

시스템 상태 백업이 실행되면 Backup Server에서는 Windows Server 백업과 통신하여 서버의 시스템 상태 백업을 요청합니다. 기본적으로 Backup Server 및 Windows Server 백업에서는 최대 사용 가능한 공간이 있는 드라이브를 사용합니다. 이 드라이브에 대한 정보는 PSDataSourceConfig.xml 파일에 저장됩니다. Windows Server Backup에서는 백업에 이 드라이브를 사용합니다.

Backup Server에서 상태 백업에 사용하는 드라이브를 사용자 지정할 수 있습니다. 보호된 서버에서 C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources로 이동합니다. 편집을 위해 PSDataSourceConfig.xml 파일을 엽니다. 드라이브 문자의 \<FilesToProtect\> 값을 변경합니다. 파일을 저장하고 닫습니다. 컴퓨터의 시스템 상태를 보호하도록 설정된 보호 그룹이 있는 경우 일관성 확인을 실행합니다. 경고가 생성되면 경고에서 **보호 그룹 수정**을 선택하고 마법사를 완료합니다. 그다음에 추가적인 일관성 확인을 실행합니다.

보호 서버가 클러스터에 있는 경우에는 클러스터 드라이브를 최대 사용 가능한 공간이 있는 드라이브로 선택할 수 있습니다. 해당 드라이브 소유권이 다른 노드로 전환되고 시스템 상태 백업이 실행되면 해당 드라이브를 사용할 수 없고 백업에 실패합니다. 이 시나리오에서는 로컬 드라이브를 가리키도록 PSDataSourceConfig.xml을 수정합니다.

그러면 Windows Server 백업에서는 복원 폴더의 루트에 WindowsImageBackup 폴더를 만듭니다. Windows Server Backup에서 백업을 만들 경우 모든 데이터가 이 폴더에 저장됩니다. 백업이 완료되면 파일이 Backup Server 컴퓨터에 전송됩니다. 다음 정보에 유의하세요.

* 이 폴더와 해당 콘텐츠는 백업이나 전송이 완료될 때 정리되지 않습니다. 이 상황을 고려하는 가장 좋은 방법은 다음에 백업이 완료될 경우에 사용하도록 공간이 예약되어 있는 것입니다.
* 백업이 수행될 때마다 폴더가 만들어집니다. 시간 및 날짜 스탬프에는 마지막 시스템 상태 백업의 시간이 반영됩니다.

## <a name="bmr-backup"></a>BMR 백업

BMR(시스템 상태 백업 포함)의 경우 백업 작업은 Backup Server 컴퓨터의 공유에 직접 저장됩니다. 보호된 서버의 폴더에는 저장되지 않습니다.

Backup Server에서는 Windows Server 백업을 호출하고 해당 BMR 백업에 대한 복제본 볼륨을 공유합니다. 이 경우 Windows Server Backup에는 최대 사용 가능한 공간이 있는 드라이브를 사용하도록 알리지 않습니다. 대신에 작업에 대해 생성된 공유를 사용합니다.

백업이 완료되면 파일이 Backup Server 컴퓨터에 전송됩니다. 로그는 C:\Windows\Logs\WindowsServerBackup에 저장됩니다.

## <a name="prerequisites-and-limitations"></a>필수 구성 요소 및 제한 사항

-   Windows Server 2003을 실행하는 컴퓨터나 클라이언트 운영 체제를 실행하는 컴퓨터에서는 BMR이 지원되지 않습니다.

-   서로 다른 보호 그룹에 있는 동일한 컴퓨터에 대한 BMR 및 시스템 상태를 보호할 수는 없습니다.

-   Backup Server 컴퓨터에서는 BMR을 위해 자체적으로 보호할 수 없습니다.

-   BMR의 경우 테이프에 대한 단기 보호(디스크-테이프 또는 D2T)가 지원되지 않습니다. 테이프에 대한 장기 보호(디스크-디스크-테이프 또는 D2D2T)가 지원되지 않습니다.

-   BMR 보호를 위해 Windows Server 백업은 보호된 컴퓨터에 설치되어야 합니다.

-   BMR 보호의 경우 시스템 상태 보호와 달리 Backup Server에는 보호된 컴퓨터에 대한 공간 요구 사항이 없습니다. Windows Server 백업은 백업을 직접 Backup Server 컴퓨터로 전송합니다. 백업 전송 작업은 Backup Server **작업** 보기에 나타나지 않습니다.

-   Backup Server에서는 BMR을 위해 복제본 볼륨의 30GB 공간을 예약합니다. [보호 그룹 수정] 마법사의 **디스크 할당** 페이지에서 또는 Get-DatasourceDiskAllocation 및 Set-DatasourceDiskAllocation PowerShell cmdlet을 사용하여 이 설정을 변경할 수 있습니다. 복구 지점 볼륨의 BMR 보호에는 5일 간 보존을 위해 6GB 정도가 필요합니다.
    * 복제본 볼륨 크기를 15GB 미만으로 줄일 수는 없습니다.
    * Backup Server에서는 BMR 데이터 원본의 크기를 계산하지 않습니다. 모든 서버에 대해 30GB를 가정합니다. 사용자 환경에서 예상하는 BMR 백업 크기에 따라 값을 변경하세요. BMR 백업 크기는 모든 중요한 볼륨에서 사용된 공간의 합계로 대략 계산할 수 있습니다. 중요한 볼륨 = 부팅 볼륨 + 시스템 볼륨 + 시스템 상태 데이터 호스트 볼륨(예: Active Directory).

-   시스템 상태 보호에서 BMR 보호로 변경할 경우 BMR 보호를 적용하려면 *복구 지점 볼륨*에 더 적은 공간이 필요합니다. 하지만 볼륨의 추가 공간은 회수되지 않습니다. [보호 그룹 수정] 마법사의 **디스크 할당 수정** 페이지에서 또는 Get-DatasourceDiskAllocation 및 Set-DatasourceDiskAllocation PowerShell cmdlet을 사용하여 볼륨 크기를 수동으로 축소할 수 있습니다.

    시스템 상태 보호에서 BMR 보호로 변경할 경우 BMR 보호를 적용하려면 *복제본 볼륨*에 더 많은 공간이 필요합니다. 볼륨이 자동으로 확장됩니다. 기본 공간 할당을 변경하려면 Modify-DiskAllocation PowerShell cmdlet을 사용합니다.

-   BMR 보호에서 시스템 상태 보호로 변경할 경우 복구 지점 볼륨에 더 많은 공간이 필요합니다. Backup Server에서는 자동으로 볼륨을 늘리려고 시도합니다. 저장소 풀에 공간이 부족하면 오류가 발생합니다.

    BMR 보호에서 시스템 상태 보호로 변경할 경우 보호된 컴퓨터에 공간이 필요합니다. 이는 시스템 상태 보호는 먼저 복제본을 로컬 컴퓨터에 쓰고 나서 Backup Server 컴퓨터로 전송하기 때문입니다.

## <a name="before-you-begin"></a>시작하기 전에

1.  **Azure Backup Server 배포**. Backup Server가 제대로 배포되어 있는지 확인합니다. 자세한 내용은 다음을 참조하세요.
    * [System requirements for Azure Backup Server](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)(Azure Backup Server 시스템 요구 사항)
    * [Backup Server 보호 매트릭스](backup-mabs-protection-matrix.md)

2.  **저장소 설정**. 백업 데이터를 디스크 및 테이프에 저장하고 Azure를 통해 클라우드에 저장할 수 있습니다. 자세한 내용은 [Prepare data storage](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage)(데이터 스토리지 준비)를 참조하세요.

3.  **보호 에이전트 설치**. 백업할 컴퓨터에 보호 에이전트를 설치합니다. 자세한 내용은 [Deploy the DPM protection agent](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent)(DPM 보호 에이전트 배포)를 참조하세요.

## <a name="back-up-system-state-and-bare-metal"></a>시스템 상태 백업 및 완전 복구 백업
[Deploy protection groups](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups)(보호 그룹 배포)의 설명대로 보호 그룹을 설정합니다. 서로 다른 그룹에 있는 동일한 컴퓨터에 대한 BMR 및 시스템 상태를 보호할 수는 없습니다. 또한 BMR을 선택하면 시스템 상태가 자동으로 사용하도록 설정됩니다.


1.  Backup Server 관리자 콘솔에서 [새 보호 그룹 만들기] 마법사를 열려면 **보호** > **작업** > **보호 그룹 만들기**를 선택합니다.

2.  **보호 그룹 형식 선택** 페이지에서 **서버**를 선택하고 **다음**을 선택합니다.

3.  **그룹 구성원 선택** 페이지에서 컴퓨터를 확장하고 **BMR** 또는 **시스템 상태**를 선택합니다.

    서로 다른 그룹에 있는 동일한 컴퓨터에 대한 BMR 및 시스템 상태를 둘 다 보호할 수는 없다는 것을 기억하세요. 또한 BMR을 선택하면 시스템 상태가 자동으로 사용하도록 설정됩니다. 자세한 내용은 [Deploy protection groups](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups)(보호 그룹 배포)를 참조하세요.

4.  **데이터 보호 방법 선택** 페이지에서 단기 및 장기 백업을 처리할 방법을 선택합니다. 단기 백업은 항상 디스크에 먼저 기록되고 Azure Backup을 사용하여 디스크에서 Azure 클라우드로 백업하는 옵션이 있습니다(단기 또는 장기). 클라우드에 대한 장기 백업 대신 Backup Server에 연결되지 않은 독립 실행형 테이프 디바이스 또는 테이프 라이브러리에 대한 장기 백업을 설정할 수도 있습니다.

5.  **단기 목표 선택** 페이지에서 디스크의 단기 저장소에 백업할 방법을 선택합니다.
    1. **보존 범위**의 경우 데이터를 디스크에서 유지할 기간을 선택합니다. 
    2. **동기화 빈도**의 경우 디스크에 대한 증분 백업을 실행할 빈도를 선택합니다. 백업 간격을 설정하지 않으려면 **복구 지점 직전** 옵션을 선택하면 됩니다. Backup Server에서는 각 복구 지점이 예약되기 직전에 빠른 전체 백업을 실행합니다.

6.  장기간 저장을 위해 데이터를 테이프에 저장하려면 **장치 목표 지정** 페이지에서 테이프 데이터를 유지할 기간을 선택합니다(1~99년). 
    1. **백업 빈도**의 경우 테이프에 대한 백업을 실행할 빈도를 선택합니다. 빈도는 선택한 보존 범위에 따라 달라집니다.
        * 보존 범위가 1~99년이면 매일, 매주, 격주, 매월, 분기마다, 반년마다 또는 매년 백업을 실행하도록 선택할 수 있습니다.
        * 보존 범위가 1~11개월이면 매일, 매주, 격주 또는 매월 백업을 실행하도록 선택할 수 있습니다.
        * 보존 범위가 1~4주이면 매일 또는 매주 백업을 실행하도록 선택할 수 있습니다.

    2. **테이프 및 라이브러리 세부 정보 선택** 페이지에서 사용할 테이프 및 라이브러리와 데이터를 압축 또는 암호화할지 여부를 선택합니다.

7.  **디스크 할당 검토** 페이지에서 보호 그룹에 할당된 저장소 풀 디스크 공간을 검토합니다.

    1. **총 데이터 크기**는 백업할 데이터의 크기입니다.
    2. **Disk space to be provisioned on Azure Backup Server**(Azure Backup Server에 프로비전할 디스크 공간)는 Backup Server에서 보호 그룹용으로 권장하는 공간입니다. Backup Server에서는 설정에 따라 가장 적합한 백업 볼륨을 선택합니다. 하지만 **디스크 할당 세부 정보**에서 백업 볼륨 선택을 편집할 수 있습니다. 
    3. 워크로드의 경우 드롭다운 메뉴에서 원하는 저장소를 선택합니다. 편집을 통해 **사용 가능한 디스크 저장소** 창에서 **총 저장소** 및 **사용 가능한 저장소**의 값을 변경합니다. 미달 프로비전된 공간은 원활한 백업을 위해 Backup Server가 볼륨에 추가하도록 제안하는 저장소 용량입니다.

8.  **복제본 만들기 방법 선택** 페이지에서 초기 전체 데이터 복제를 처리하는 방법을 선택합니다. 네트워크를 통해 복제할 경우 사용량이 적은 시간을 선택하는 것이 좋습니다. 대용량 데이터이거나 네트워크 상태가 최적화되지 않은 경우 이동식 미디어를 사용하여 데이터를 오프라인으로 복제하는 것을 고려하세요.

9. **일관성 확인 옵션 선택** 페이지에서 일관성 확인을 자동화할 방법을 선택합니다. 복제 데이터가 일관성을 잃은 경우에만 또는 일정에 따라 확인을 실행하도록 선택할 수 있습니다. 자동 일관성 확인을 구성하지 않으려면 언제든지 수동 확인을 실행할 수 있습니다. 수동 확인을 실행하려면 Backup Server 관리자 콘솔의 **보호** 영역에서 보호 그룹을 마우스 오른쪽 단추로 클릭하고 **일관성 확인 수행**을 선택합니다.

10. Azure Backup을 사용하여 클라우드에 백업하도록 선택한 경우 **온라인 보호 데이터 지정** 페이지에서 Azure에 백업할 워크로드를 선택해야 합니다.

11. **온라인 Backup 일정 지정** 페이지에서 Azure에 대한 증분 백업을 수행할 빈도를 선택합니다. 매일, 매주, 매월 및 매년 백업을 실행하도록 예약하고 백업을 실행할 시간 및 날짜를 선택할 수 있습니다. Backup은 하루 두 번까지 수행할 수 있습니다. 백업이 실행될 때마다 Azure에는 Backup Server 디스크에 저장된 백업 데이터 복사본에서 데이터 복구 지점이 만들어집니다.

12. **온라인 보존 정책 지정** 페이지에서 매일, 매주, 매월 및 매년 백업에서 만들어진 복구 지점을 Azure에 보존하는 방법을 선택합니다.

13. **온라인 복제 선택** 페이지에서 데이터의 초기 전체 복제를 수행하는 방법을 선택합니다. 네트워크를 통해 복제하거나 오프라인 백업(오프라인 시딩)을 수행할 수 있습니다. 오프라인 백업에는 Azure Import 기능이 사용됩니다. 자세한 내용은 [Azure Backup의 오프라인 백업 워크플로](backup-azure-backup-import-export.md)를 참조하세요.

14. **요약** 페이지에서 설정을 검토합니다. **그룹 만들기**를 선택한 후 데이터의 초기 복제가 수행됩니다. 데이터 복제가 완료되면 **상태** 페이지에서 보호 그룹 상태는 **정상**입니다. 이후 보호 그룹 설정에 따라 백업이 수행됩니다.

## <a name="recover-system-state-or-bmr"></a>시스템 상태 또는 BMR 복구
네트워크 위치에서 BMR 또는 시스템 상태를 복구할 수 있습니다. BMR을 백업한 경우 WinRE(Windows Recovery Environment)를 사용하여 시스템을 시작하고 네트워크에 연결합니다. 그다음에 Windows Server Backup을 사용하여 네트워크 위치에서 복구합니다. 시스템 상태를 백업한 경우 Windows Server Backup을 사용하여 네트워크 위치에서 복구하면 됩니다.

### <a name="restore-bmr"></a>BMR 복원
Backup Server 컴퓨터에서 복구를 실행합니다.

1.  **복구** 창에서 복구할 컴퓨터를 찾고 **완전 복구**를 선택합니다.

2.  사용 가능한 복구 지점은 달력에 굵게 표시됩니다. 사용할 복구 지점의 날짜 및 시간을 선택합니다.

3.  **복구 유형 선택** 페이지에서 **네트워크 폴더에 복사**를 선택합니다.

4.  **대상 지정** 페이지에서 데이터를 복사할 위치를 선택합니다. 선택된 대상에는 충분한 공간이 있어야 합니다. 새 폴더를 만드는 것이 좋습니다.

5.  **복구 옵션 지정** 페이지에서 적용할 보안 설정을 선택합니다. 그다음에 더 신속한 복구를 위해 SAN(저장 영역 네트워크) 기반 하드웨어 스냅숏을 사용할지 선택합니다. 이 옵션은 이 기능이 제공되는 SAN이 있고 클론을 만들어 쓰기 가능하도록 분할하는 기능이 있는 경우에만 사용할 수 있습니다. 또한 보호된 컴퓨터 및 Backup Server 컴퓨터는 같은 네트워크에 연결되어야 합니다.

6.  알림 옵션을 설정합니다. **확인** 페이지에서 **복구**를 선택합니다.

공유 위치를 설정합니다.

1.  복원 위치에서 백업이 있는 폴더로 이동합니다.

2.  공유 폴더의 루트가 WindowsImageBackup 폴더가 되도록 WindowsImageBackup의 한 수준 위 폴더를 공유합니다. 이렇게 하지 않으면 복원 시 백업을 찾을 수 없습니다. WinRE(Windows Recovery Environment)를 사용하여 연결하려면 올바른 IP 주소와 자격 증명을 사용하여 WinRE에서 액세스할 수 있는 공유가 필요합니다.

시스템을 복원합니다.

1.  복원할 시스템의 Windows DVD를 사용하여 이미지를 복원할 컴퓨터를 시작합니다.

2.  첫 번째 페이지에서 언어 및 로캘 설정을 확인합니다. **설치** 페이지에서 **컴퓨터 복구**를 선택합니다.

3.  **시스템 복구 옵션** 페이지에서 **이전에 만든 시스템 이미지를 사용하여 컴퓨터를 복구합니다.** 를 선택합니다.

4.  **시스템 이미지 백업 선택** 페이지에서 **시스템 이미지 선택** > **고급** > **네트워크에서 시스템 이미지 검색**을 선택합니다. 경고가 나타나면 **예**를 선택합니다. 공유 경로로 이동하여 자격 증명을 입력하고 나서 복구 지점을 선택합니다. 이렇게 하면 해당 복구 지점에서 사용할 수 있는 특정 백업이 검색됩니다. 사용할 복구 지점을 선택합니다.

5.  **백업 복원 방법을 선택하십시오.** 페이지에서 **디스크 포맷 및 다시 분할**을 선택합니다. 다음 페이지에서 설정을 확인합니다. 

6.  복원을 시작하려면 **마침**을 선택합니다. 다시 시작해야 합니다.

### <a name="restore-system-state"></a>시스템 상태 복원

Backup Server에서 복구를 실행합니다.

1.  **복구** 창에서 복구할 컴퓨터를 찾고 **완전 복구**를 선택합니다.

2.  사용 가능한 복구 지점은 달력에 굵게 표시됩니다. 사용할 복구 지점의 날짜 및 시간을 선택합니다.

3.  **복구 유형 선택** 페이지에서 **네트워크 폴더에 복사**를 선택합니다.

4.  **대상 지정** 페이지에서 데이터를 복사할 위치를 선택합니다. 선택된 대상에는 충분한 공간이 있어야 합니다. 새 폴더를 만드는 것이 좋습니다.

5.  **복구 옵션 지정** 페이지에서 적용할 보안 설정을 선택합니다. 그다음에 더 신속한 복구를 위해 SAN 기반 하드웨어 스냅숏을 사용할지 선택합니다. 이 옵션은 이 기능이 포함된 SAN이 있고 클론을 만들어 쓰기 가능하도록 분할하는 기능이 있는 경우에만 사용할 수 있습니다. 또한 보호된 컴퓨터 및 Backup Server 서버는 같은 네트워크에 연결되어야 합니다.

6.  알림 옵션을 설정합니다. **확인** 페이지에서 **복구**를 선택합니다.

Windows Server Backup을 실행합니다.

1.  **작업** > **복구** > **이 서버** > **다음**을 선택합니다.

2.  **다른 서버**를 선택하고, **위치 유형 지정** 페이지를 선택하고 나서, **원격 공유 폴더**를 선택합니다. 복구 지점이 포함된 폴더의 경로를 입력합니다.

3.  **복구 유형 선택** 페이지에서 **시스템 상태**를 선택합니다. 

4. **복구 위치 선택** 페이지에서 **원래 위치**를 선택합니다.

5.  **확인** 페이지에서 **복구**를 선택합니다. 복원한 후 서버를 다시 시작합니다.

6.  명령 프롬프트에서 시스템 상태 복원을 실행할 수도 있습니다. 이렇게 하려면 복구할 컴퓨터에서 Windows Server Backup을 시작합니다. 버전 식별자를 확인하려면 명령 프롬프트에 다음을 입력합니다. ```wbadmin get versions -backuptarget \<servername\sharename\>```

    버전 식별자를 사용하여 시스템 상태 복원을 시작합니다. 명령 프롬프트에 다음을 입력합니다. ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    복구를 시작할지 확인합니다. 명령 프롬프트 창에서 프로세스를 확인할 수 있습니다. 복원 로그가 생성됩니다. 복원한 후 서버를 다시 시작합니다.

