---
title: MABS를 사용하여 Azure에 SharePoint 팜 백업
description: Azure Backup Server를 사용하여 SharePoint 데이터를 백업 및 복원합니다. 이 문서에서는 원하는 데이터를 Azure에 저장할 수 있도록 SharePoint 팜을 구성하는 정보를 제공합니다. 디스크 또는 Azure에서 보호된 SharePoint 데이터를 복원할 수 있습니다.
ms.topic: conceptual
ms.date: 04/26/2020
ms.openlocfilehash: 00af51764d5a9454b002de6375b2b16d6e80c300
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017433"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-mabs"></a>MABS를 사용하여 Azure에 SharePoint 팜 백업

SharePoint 팜은 다른 데이터 원본을 백업하는 것과 같은 방법으로 Microsoft Azure Backup Server(MABS)를 사용하여 Microsoft Azure에 백업합니다. Azure Backup은 일간, 주간, 월간 혹은 연간 백업 지점을 생성하도록 백업 일정에 유연성을 제공하고 다양한 백업 지점에 관한 보존 정책 옵션을 제공합니다. MABS는 빠른 RTO(복구 시간 목표)를 위해 로컬 디스크 복사본을 저장하는 기능과 경제적인 장기 보존을 위해 Azure에 사본을 복사하는 기능을 제공합니다.

MABS를 사용하여 SharePoint를 Azure에 백업하는 것은 SharePoint를 DPM(Data Protection Manager)에 로컬로 백업하는 것과 비슷한 프로세스입니다. Azure에 대한 특정 고려 사항은 이 문서에 나와 있습니다.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint가 지원하는 버전 및 관련 보호 시나리오

지원되는 SharePoint 버전 및 백업에 필요한 MABS 버전 목록은 [MABS 보호 매트릭스](./backup-mabs-protection-matrix.md)를 참조하세요.

## <a name="before-you-start"></a>시작하기 전에

SharePoint 팜을 Azure에 백업하기 전에 몇 가지 확인이 필요합니다.

### <a name="whats-not-supported"></a>지원 되지않는 사항

* SharePoint 팜을 보호하는 MABS는 검색 인덱스 또는 애플리케이션 서비스 데이터베이스를 보호하지 않습니다. 이러한 데이터베이스들은 별도로 보호를 구성해야 합니다.

* MABS는 스케일 아웃 파일 서버(SOFS) 공유에 호스트되는 SharePoint SQL Server 데이터베이스의 백업을 제공하지 않습니다.

### <a name="prerequisites"></a>필수 구성 요소

계속하기 전에 워크로드를 보호하기 위하여 Microsoft Azure Backup 사용을 위한 [필수 구성 요소](backup-azure-dpm-introduction.md#prerequisites-and-limitations) 를 모두 충족하는지 확인합니다. 필수 조건을 위한 작업에는 백업 자격 증명 모음 만들기, 보관 자격 증명 모음 다운로드, Azure Backup 에이전트 설치, 자격 증명 모음에 Azure Backup 서버 등록 등이 포함됩니다.

추가 필수 조건 및 제한 사항은 다음과 같습니다.

* 기본적으로 SharePoint를 보호하는 경우 모든 콘텐츠 데이터베이스(및 SharePoint_Config 및 SharePoint_AdminContent* 데이터베이스)가 보호됩니다. 검색 인덱스, 템플릿 또는 애플리케이션 서비스 데이터베이스 또는 사용자 프로필 서비스와 같은 사용자 지정 항목을 추가하려는 경우 보호를 위해 따로 구성해야 합니다. 이러한 유형의 기능 또는 사용자 지정 파일을 포함하는 모든 폴더에 대해 보호를 사용하도록 설정해야 합니다.

* SharePoint 데이터베이스를 SQL Server 데이터 원본으로 보호할 수 없습니다. 팜 백업에서 개별 데이터베이스를 복구할 수 있습니다.

* MABS는 **로컬 시스템**으로 실행됩니다. SQL Server 데이터베이스를 백업하려면 SQL Server의 해당 계정에 대한 sysadmin 권한이 필요합니다. 백업하려는 SQL Server에서 NT AUTHORITY\SYSTEM을 **sysadmin**으로 설정합니다.

* 팜의 모든 항목이 천만 개에 이르기 때문에, MABS 폴더의 위치에는 최소 2GB의 공간이 필요합니다. 이 공간은 카탈로그를 생성하는 데 필요 합니다. MABS를 사용하여 특정 항목(사이트 모음, 사이트, 목록, 문서 라이브러리, 폴더, 개별 문서 및 목록 항목)을 복구하도록 하기 위해 카탈로그 생성 기능은 각 콘텐츠 데이터베이스에 포함된 URL의 목록을 만듭니다. MABS 관리자 콘솔 안의 복구 작업 영역에서 복구 가능한 항목 창의 URL 목록을 볼 수 있습니다.

* SharePoint 팜에서, SQL Server 데이터베이스가 SQL Server 별칭으로 구성된 경우 MABS에서 보호할 프런트 엔드 웹 서버에 SQL Server 클라이언트 구성 요소를 설치합니다.

* 애플리케이션 저장소 항목의 보호는 SharePoint 2013에서 지원되지 않습니다.

* MABS는 원격 FILESTREAM 보호를 지원하지 않습니다. FILESTREAM은 데이터베이스의 일부여야 합니다.

## <a name="configure-backup"></a>백업 구성

SharePoint 팜을 백업하려면 ConfigureSharePoint.exe를 사용하여 SharePoint에 대한 보호를 구성하고 MABS에서 보호 그룹을 만듭니다.

1. **ConfigureSharePoint.exe 실행** - 이 도구는 SharePoint VSS 기록기 서비스 \(WSS\)를 구성하고 보호 에이전트에 SharePoint 팜에 대한 자격 증명을 제공합니다. 보호 에이전트를 배포한 후에는 프런트 엔드 웹 서버의 `<MABS Installation Path\>\bin` 폴더\-에서 ConfigureSharePoint.exe 파일을 찾을 수 있습니다.  여러 WFE 서버가 있는 경우 그 중 하나에만 설치해야 합니다. 다음과 같이 실행합니다.

    * WFE 서버의 명령 프롬프트에서 `\<MABS installation location\>\\bin\\`으로 이동하여 `ConfigureSharePoint \[\-EnableSharePointProtection\] \[\-EnableSPSearchProtection\] \[\-ResolveAllSQLAliases\] \[\-SetTempPath <path>\]`를 실행합니다. 여기에서 다음이 적용됩니다.

        * **EnableSharePointProtection**을 사용하여 SharePoint 팜을 보호하도록 하고, VSS 기록기를 사용하도록 설정하고, 해당 자격 증명을 입력한 사용자 권한으로 실행할 DCOM 애플리케이션 WssCmdletsWrapper의 ID를 이 옵션에 등록합니다. 이 계정은 프런트 엔드 웹 서버에서 팜 관리자 및 로컬 관리자여야 합니다.

        * **EnableSPSearchProtection**은 프런트 엔드 웹 서버의 HKLM\\Software\\Microsoft\\ Microsoft Data Protection Manager\\Agent\\2.0\\에서 레지스트리 키를 사용하여 WSS 3.0 SP Search를 보호하도록 하고, 해당 자격 증명을 입력한 사용자 권한으로 실행할 DCOM 애플리케이션 WssCmdletsWrapper의 ID를 이 옵션에 등록합니다. 이 계정은 프런트 엔드 웹 서버에서 팜 관리자 및 로컬 관리자여야 합니다.

        * **ResolveAllSQLAliases**는 SharePoint VSS 기록기에서 보고하는 모든 별칭을 표시하고 해당하는 SQL Server에 확인해 줍니다. 또한 확인된 인스턴스 이름을 표시합니다. 서버가 미러되면 미러된 서버도 표시합니다. SQL Server에 확인되지 않는 모든 별칭을 보고합니다.

        * **SetTempPath**는 환경 변수 TEMP 및 TMP를 지정된 경로로 설정합니다. 대규모 사이트 모음, 사이트, 목록 또는 항목을 복구 중인데 팜 관리자 임시 폴더에 공간이 부족하면 항목 수준 복구가 실패합니다. 이 옵션을 사용하여 복구 중인 사이트 모음 또는 사이트를 저장하는 데 충분한 공간이 있는 볼륨으로 임시 파일의 폴더 경로를 변경할 수 있습니다.

    * 팜 관리자 자격 증명을 입력 합니다. 이 계정은 WFE 서버에서 로컬 관리자 그룹의 구성원 이어야 합니다. 팜 관리자가 로컬 관리자가 아닌 경우, WFE 서버에 다음 권한을 부여 합니다.

        * **WSS_Admin_WPG** 그룹에 MABS 폴더(`%Program Files%\Data Protection Manager\DPM\`)에 대한 모든 권한을 부여합니다.

        * **WSS_Admin_WPG** 그룹에 MABS 레지스트리 키(`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager`)에 대한 읽기 액세스 권한을 부여합니다.

        ConfigureSharePoint.exe를 실행한 후에 SharePoint 팜 관리자 자격 증명이 변경되면 이 파일을 다시 실행해야 합니다.

1. 보호 그룹을 만들려면 **보호** > **작업** > **보호 그룹 만들기**를 클릭하여 MABS 콘솔에서 **새 보호 그룹 만들기** 마법사를 엽니다.

1. **보호 그룹 형식 선택**에서 **서버**를 선택합니다.

1. **그룹 멤버 선택**에서 WFE 역할을 보유하는 서버를 확장합니다. WFE 서버가 두 개 이상 있는 경우 ConfigureSharePoint.exe를 설치한 서버를 선택합니다.

    SharePoint 서버 MABS 쿼리 VSS를 확장하여 MABS에서 보호할 수 있는 데이터를 확인합니다.  SharePoint 데이터베이스가 원격이면 MABS가 해당 데이터베이스에 연결합니다. SharePoint 데이터 원본이 표시되지 않는 경우에는 SharePoint 서버 및 원격 SQL Server에서 VSS 기록기가 실행되고 있는지 확인하고, MABS 에이전트가 SharePoint 서버와 원격 SQL Server 모두에 설치되어 있는지 확인합니다. 또한 SharePoint 데이터베이스가 SQL Server 데이터베이스처럼 다른 위치에서 보호되고 있지 않은지 확인합니다.

1. **데이터 보호 방법 선택**에서 단기 및 장기\- 백업을 처리하는 방법을 지정합니다. 단기 백업은 \- 항상 디스크에 먼저 사용 되며 \( 단기 또는 장기 Azure Backup를 사용 하 여 디스크에서 Azure 클라우드로 백업 하는 옵션을 사용 합니다 \- \) .

1. **단기\- 목표 선택**에서 디스크의 단기\- 스토리지에 백업할 방법을 선택합니다.   **보존 범위**에서 데이터를 디스크에서 유지할 기간을 지정합니다. **동기화 빈도**에서 디스크에 대한 증분 백업을 실행할 빈도를 지정합니다. 백업 간격을 설정하지 않으려는 경우 각 복구 지점이 예약되기 직전에 MABS가 빠른 전체 백업을 실행하도록, 복구 지점 직전을 선택할 수 있습니다.

1. 디스크 할당 검토 페이지에서 보호 그룹에 할당된 스토리지 풀 디스크 공간을 검토합니다.

    **총 데이터 크기**는 백업할 데이터의 크기이고 **MABS에서 프로비저닝할 디스크 공간**은 MABS에서 보호 그룹에 권장하는 공간입니다. MABS는 설정에 따라 가장 적합한 백업 볼륨을 선택합니다. 하지만 사용자가 **디스크 할당 세부 정보**에서 백업 볼륨 선택을 편집할 수 있습니다. 워크로드의 경우 드롭다운 메뉴에서 원하는 스토리지를 선택합니다. 편집을 통해 **사용 가능한 디스크 스토리지** 창에서 **총 스토리지** 및 **사용 가능한 스토리지**의 값을 변경합니다. 미달 프로비전된 공간은 향후 원활한 백업이 가능하도록 MABS가 볼륨에 추가하도록 제안하는 스토리지 용량입니다.

1. **복제본 만들기 방법 선택**에서 초기 전체 데이터 복제를 처리하는 방법을 선택합니다.  네트워크를 통해 복제하도록 선택하는 경우 사용량이 많지 않은 시간을 선택하는 것이 좋습니다. 대용량 데이터이거나 네트워크 상태가 최적화되지 않은 경우 이동식 미디어를 사용하여 데이터를 오프라인으로 복제하는 것을 고려하세요.

1. **일관성 확인 옵션 선택**에서 일관성 확인을 자동화할 방법을 선택합니다. 확인은 복제본 데이터가 일치하지 않을 때만 실행하거나 일정에 따라서만 실행하도록 설정할 수 있습니다. 자동 일관성 확인을 구성하지 않더라도 언제든지 MABS 콘솔의 **보호** 영역에서 보호 그룹을 마우스 오른쪽 단추로 클릭하고 **일관성 확인 수행**을 선택하여 수동으로 확인할 수 있습니다.

1. Azure Backup을 사용하여 클라우드에 백업하도록 선택한 경우 **온라인 보호 데이터 지정** 페이지에서 Azure에 백업할 워크로드를 선택해야 합니다.

1. **온라인 백업 일정 지정** 페이지에서 Azure에 증분 백업을 수행할 빈도를 지정합니다. 매일, 매주, 매월 및 매년 백업을 실행하도록 예약할 수 있습니다. Backup은 하루 두 번까지 수행할 수 있습니다. 백업이 실행될 때마다 Azure에는 MABS 디스크에 저장된 백업 데이터 복사본에서 데이터 복구 지점이 만들어집니다.

1. **온라인 보존 정책 지정**에서 매일, 매주, 매월 및 매년 백업에서 만들어진 복구 지점을 Azure에 보존하는 방법을 지정할 수 있습니다.

1. **온라인 복제 선택**에서 데이터의 초기 전체 복제를 수행하는 방법을 지정합니다. 네트워크를 통해 복제할 수도 있고 오프라인 백업(오프라인 시딩)을 수행할 수도 있습니다. 오프라인 백업에는 Azure Import 기능이 사용됩니다. [자세히 알아보기](./backup-azure-backup-import-export.md).

1. **요약** 페이지에서 설정을 검토합니다. **그룹 만들기**를 클릭하면 데이터의 초기 복제가 수행됩니다. 완료되면 **상태** 페이지에서 보호 그룹 상태가 **정상**으로 표시됩니다. 그런 후에는 보호 그룹 설정에 따라 백업이 수행됩니다.

## <a name="monitoring"></a>모니터링

보호 그룹을 생성한 후 초기 복제가 실행되며 MABS에서 SharePoint 데이터의 백업 및 동기화가 시작됩니다. MABS는 초기 동기화 및 후속 백업을 모니터링합니다.  다음 두 가지 방법으로 SharePoint 데이터를 모니터링할 수 있습니다.

* 기본 MABS 모니터링을 사용하여 경고를 게시하고 알림을 구성함으로써 사전 모니터링에 대한 알림을 설정할 수 있습니다. 중요, 경고, 정보 알림 및 인스턴스화된 복구 상태에 대한 알림을 메일로 보낼 수 있습니다.

* Operations Manager를 사용하는 경우 경고를 중앙에 게시할 수 있습니다.

### <a name="set-up-monitoring-notifications"></a>모니터링 알림 설정

1. MABS 관리자 콘솔에서 **모니터링** > **작업** > **옵션**을 클릭합니다.

2. **SMTP 서버**를 클릭하고 경고를 보낼 서버 이름, 포트, 메일 주소를 입력합니다. 주소가 유효해야 합니다.

3. **인증된 SMTP 서버**에 사용자 이름과 암호를 입력합니다. 사용자 이름과 암호는 이전 단계에서 기술한 “보낸 사람” 주소에 해당하는 사람의 도메인 계정 이름이어야 합니다. 그렇지 않으면 알림 배달이 실패합니다.

4. SMTP 서버 설정을 테스트하려면 **테스트 메일 보내기**를 클릭하고 MABS에서 테스트 메시지를 보낼 메일 주소를 입력한 다음, **확인**을 클릭합니다. **옵션** > **알림**을 클릭하고 받는 사람에게 알릴 경고 유형을 선택합니다. **받는 사람**에 MABS에서 알림의 사본을 보낼 각 받는 사람의 메일 주소를 입력합니다.

### <a name="publish-operations-manager-alerts"></a>Operations Manager 경고 게시

1. MABS 관리자 콘솔에서 **모니터링** > **작업** > **옵션** > **경고 게시** > **활성 경고 게시**를 클릭합니다.

2. **알림 게시**를 사용하도록 설정한 후에는 사용자 조치가 필요할 수 있는 모든 기존 MABS 경고가 **MABS 경고** 이벤트 로그에 게시됩니다. 그러면 MABS 서버에 설치 된 Operations Manager 에이전트가 이러한 경고를 Operations Manager에 게시 하 고 새 경고가 생성 될 때 계속 해 서 콘솔을 업데이트 합니다.

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>MABS를 사용하여 디스크에서 SharePoint 항목 복원

아래 예제에서는 실수로 삭제한 *SharePoint 복구 항목*을 복구해야 합니다.
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. **MABS 관리자 콘솔**을 엽니다. MABS에서 보호되는 모든 SharePoint 팜이 **보호** 탭에 표시됩니다.

    ![MABS SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. 항목 복구를 시작하려면 **복구** 탭을 선택합니다.

    ![MABS SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. 복구 지점 범위 내에서 와일드카드 기반 검색을 사용하여 *SharePoint 항목을 복구할* SharePoint를 검색합니다.

    ![MABS SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. 검색 결과에서 적절한 복구 지점을 선택하고, 마우스 오른쪽 단추로 클릭하여, **복구**를 선택합니다.
5. 다양한 복구 지점을 살펴보고 복구할 데이터베이스 또는 항목을 선택할 수 있습니다. **날짜 > 복구 시간**을 선택한 다음 올바른 **데이터베이스 > SharePoint 팜 > 복구 지점 > 항목**을 선택합니다.

    ![MABS SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. 항목을 마우스 오른쪽 단추로 클릭하고 **복구**를 선택하여 **복구 마법사**를 엽니다. **다음**을 클릭합니다.

    ![복구 선택 사항 확인](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. 수행할 복구 유형을 선택한 후 **다음**을 클릭합니다.

    ![복구 유형](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > 예제에서는 **원본 사이트에 복구** 를 선택하여, 원본 SharePoint 사이트에 항목이 복구됩니다.
   >
   >
8. 사용할 **복구 프로세스** 를 선택합니다.

   * SharePoint 팜이 변경 되지 않았고 복원 중인 복구 지점과 동일한 경우 **복구 팜을 사용 하지 않고** 복구를 선택 합니다.
   * SharePoint 팜 복구 지점이 생성 된 이후 변경 된 경우 **복구 팜을 사용 하 여 복구** 를 선택합니다.

     ![복구 프로세스](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. 임시로 데이터베이스를 복구할 스테이징 SQL Server 인스턴스 위치를 제공하고, 항목을 복구하기 위해 SharePoint를 실행하는 서버 및 MABS에 준비 파일 공유를 제공합니다.

    ![스테이징 위치 1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS는 SharePoint 항목을 호스트 하는 콘텐츠 데이터베이스를 임시 SQL Server 인스턴스에 연결 합니다. 콘텐츠 데이터베이스에서, MABS는 항목을 복구하여 MABS의 준비 파일 위치에 넣습니다. 이제 준비 위치에 있는 복구된 항목을 SharePoint 팜의 준비 위치로 내보내야 합니다.

    ![스테이징 Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. **복구 옵션 지정**을 선택하고, SharePoint 팜에 보안 설정을 적용하거나 복구 지점의 보안 설정을 적용합니다. **다음**을 클릭합니다.

    ![복구 옵션](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > 네트워크 대역폭 사용량을 제한 하도록 선택할 수 있습니다. 따라서 프로덕션 시간 동안 프로덕션 서버에 미치는 영향이 최소화 됩니다.
    >
    >
11. 요약 정보를 검토하고 **복구** 를 클릭하여 파일 복구를 시작합니다.

    ![복구 요약](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. 이제 **MABS 관리자 콘솔**의 **모니터링** 탭을 선택하여 복구의 **상태**를 봅니다.

    ![복구 상태](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > 이제 파일이 복원됩니다. 복원된 파일을 확인하려면 SharePoint 사이트를 새로 고침하세요.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-mabs"></a>MABS를 사용하여 Azure에서 SharePoint 데이터베이스 복원

1. SharePoint 콘텐츠 데이터베이스를 복구하려면, 다양한 복구 지점을 살펴보고(이전과 같이), 복원할 복구 지점을 선택합니다.

    ![MABS SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. SharePoint 복구 지점을 두번 클릭하면 사용할 수 있는 SharePoint 카탈로그 정보가 나타납니다.

   > [!NOTE]
   > SharePoint 팜은 Azure에서 장기 보존으로 보호되기 때문에 MABS 서버에서 사용할 수 있는 카탈로그 정보(메타데이터)가 없습니다. 따라서, 지정 시간 SharePoint 콘텐츠 데이터베이스 복구가 필요할 때마다, SharePoint 팜 카탈로그를 다시 만들어야 합니다.
   >
   >
3. **카탈로그 다시 만들기**를 클릭합니다.

    ![MABS SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    **클라우드 카탈로그 다시 만들기** 상태 창이 열립니다.

    ![MABS SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    카탈로그 만들기가 완료되면, 상태가 *성공*으로 변경됩니다. **닫기**를 클릭합니다.

    ![MABS SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. MABS **복구** 탭의 SharePoint 개체를 클릭하여 콘텐츠 데이터베이스 구조를 가져옵니다. 항목을 마우스 오른쪽 단추로 클릭한 다음 **복구**를 클릭합니다.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. 이제 이 문서 앞부분의 복구 단계를 수행하여 디스크로 SharePoint 콘텐츠 데이터베이스를 복구합니다.

## <a name="switching-the-front-end-web-server"></a>프런트 엔드 웹 서버 전환

프런트 엔드 웹 서버가 두 대 이상 있고 MABS에서 팜을 보호하는 데 사용하는 서버를 전환하려는 경우 다음 지침을 따르세요.

다음 절차에서는 두 개의 프런트 엔드 웹 서버 *Server1* 및 *Server2*를 사용하는 서버 팜 예제를 사용합니다. MABS는 *Server1*을 사용하여 팜을 보호합니다. 팜에서 *Server1*을 제거할 수 있도록 MABS에서 사용하는 프런트 엔드 웹 서버를 *Server2*로 변경합니다.

> [!NOTE]
> MABS에서 팜을 보호하는 데 사용하는 프런트 엔드 웹 서버를 사용할 수 없는 경우 다음 절차의 4단계부터 진행하여 프런트 엔드 웹 서버를 변경합니다.

### <a name="to-change-the-front-end-web-server-that-mabs-uses-to-protect-the-farm"></a>MABS가 팜을 보호하는 데 사용하는 프런트 엔드 웹 서버를 변경하려면

1. 명령 프롬프트에서 다음 명령을 실행하여 *Server1*에서 SharePoint VSS 기록기 서비스를 중지합니다.

    ```CMD
    stsadm -o unregisterwsswriter
    ```

1. *Server1*에서 레지스트리 편집기를 열고 다음 키로 이동합니다.

   **HKLM\System\CCS\Services\VSS\VssAccessControl**

1. VssAccessControl 하위 키에 나열된 모든 값을 확인합니다. 항목의 값 데이터가 0이고 다른 VSS 기록기가 연결된 계정 자격 증명으로 실행되고 있는 경우 값 데이터를 1로 변경합니다.

1. *Server2*에 보호 에이전트를 설치합니다.

   > [!WARNING]
   > 두 서버가 동일한 도메인에 있는 경우에만 웹 프런트 엔드 서버를 전환할 수 있습니다.

1. *Server2*의 명령 프롬프트에서 디렉터리를 `_MABS installation location_\bin\`으로 변경하고 **ConfigureSharepoint**를 실행합니다. ConfigureSharepoint에 대한 자세한 내용은 [백업 구성](#configure-backup)을 참조하세요.

1. 서버 팜이 속한 보호 그룹을 선택한 다음, **보호 그룹 수정**을 클릭합니다.

1. 그룹 수정 마법사의 **그룹 멤버 선택** 페이지에서 *Server2*를 확장하고 서버 팜을 선택한 후 마법사를 완료합니다.

   일관성 확인이 시작됩니다.

1. 6단계를 수행한 경우 이제 보호 그룹에서 볼륨을 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Exchange Server](backup-azure-exchange-mabs.md) 문서를 참조하세요.
[SQL Server 백업](backup-azure-sql-mabs.md) 문서를 참조하세요.
