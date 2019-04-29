---
title: Azure Stack VM에서 파일 백업
description: Azure Backup을 사용하여 Azure Stack 환경에 Azure Stack 파일과 애플리케이션을 백업하고 복구합니다.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/5/2018
ms.author: adigan
ms.openlocfilehash: 67d79f2aa41bab8a14d693098538d22ffeb05a4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848763"
---
# <a name="back-up-files-on-azure-stack"></a>Azure Stack에서 파일 백업
Azure Backup을 사용하여 Azure Stack의 파일 및 애플리케이션을 보호(또는 백업)합니다. 파일 및 애플리케이션을 백업하려면 Microsoft Azure Backup Server를 Azure Stack에서 실행되는 가상 머신으로 설치합니다. 동일한 가상 네트워크의 Azure Stack 서버에서 파일을 보호할 수 있습니다. Azure Backup Server를 설치하면 Azure 디스크를 추가하여 단기 백업 데이터에 사용할 수 있는 로컬 저장소를 증가시킵니다. Azure Backup Server는 장기 보존을 위해 Azure Storage를 사용합니다.

> [!NOTE]
> Azure Backup Server 및 System Center DPM(Data Protection Manager)은 유사하지만 DPM은 Azure Stack에서 사용하도록 지원되지 않습니다.
>

이 문서에서는 Azure Stack 환경에 Azure Backup Server 서버를 설치하는 방법을 다루지 않습니다. Azure Stack에 Azure Backup Server를 설치하려면 [Azure Backup Server 설치](backup-mabs-install-azure-stack.md) 문서를 참조하세요.


## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Azure Stack VM의 파일 및 폴더를 Azure에 백업

Azure Stack 가상 머신에서 파일을 보호하도록 Azure Backup Server를 구성하려면 Azure Backup Server 콘솔을 엽니다. 콘솔을 사용하여 보호 그룹을 구성하고 가상 머신의 데이터를 보호할 것입니다.

1. Azure Backup Server 콘솔에서 **보호**를 클릭하고 도구 모음에서 **새로 만들기**를 클릭하여 **새 보호 그룹 만들기** 마법사를 엽니다.

   ![Azure Backup Server에서 보호 구성](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    마법사가 열릴 때까지 몇 초 정도 걸릴 수 있습니다. 마법사가 열리면 **다음**을 클릭하여 **보호 그룹 형식 선택** 화면으로 넘어갑니다.

   ![새 보호 그룹 마법사가 열립니다.](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. **보호 그룹 형식 선택** 화면에서 **서버**를 선택하고 **다음**을 선택합니다.

    ![새 보호 그룹 마법사가 열립니다.](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    **그룹 구성원 선택** 화면이 열립니다. 

    ![새 보호 그룹 마법사가 열립니다.](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. **그룹 구성원 선택** 화면에서 **+** 기호를 클릭하여 하위 항목 목록을 확장합니다. 보호할 항목의 확인란을 모두 선택합니다. 항목을 선택했으면 **다음**을 클릭합니다.

    ![새 보호 그룹 마법사가 열립니다.](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    보호 정책을 공유할 모든 데이터를 한 보호 그룹에 배치하는 것이 좋습니다. 보호 그룹 계획 및 배포에 대한 자세한 내용은 System Center DPM 문서 [배포 보호 그룹](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801)을 참조하세요.

4. **데이터 보호 방법 선택** 화면에서 보호 그룹에 사용할 이름을 입력합니다. **다음 방법을 사용하여 단기 보호:** 및 **온라인 보호를 사용하려고 합니다**의 확인란을 선택합니다. **다음**을 클릭합니다.

    ![새 보호 그룹 마법사가 열립니다.](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    **온라인 보호 사용**을 선택하려면 먼저 **다음을 사용한 단기 보호 사용:** 디스크를 선택해야 합니다. Azure Backup Server가 테이프를 보호하지 못하므로 단기 보호에 선택할 수 있는 방법은 디스크밖에 없습니다.

5. **단기 목표 지정** 화면에서, 디스크에 저장된 복구 지점을 얼마나 오래 유지할 것인지, 언제 증분 백업을 저장할 것인지 지정합니다. **다음**을 클릭합니다.

    > [!IMPORTANT]
    > 5일 넘게 Azure Backup Server에 연결된 디스크에서 작업 복구(백업) 데이터를 유지**하지** 않아야 합니다.
    >

    ![새 보호 그룹 마법사가 열립니다.](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png) 

    증분 백업의 간격을 선택하는 대신, 예약된 각 복구 지점 직전에 고속 전체 백업을 실행하려면 **복구 지점 직전**을 클릭합니다. 애플리케이션 워크로드를 보호하는 경우 Azure Backup Server는 동기화 빈도 일정에 따라(애플리케이션에서 증분 백업을 지원하는 경우) 복구 지점을 만듭니다. 애플리케이션에서 증분 백업을 지원하지 않는 경우 Azure Backup Server가 고속 전체 백업을 실행합니다.

    **파일 복구 지점**에 대해 복구 지점을 만들 시기를 지정합니다. 복구 지점을 만드는 시간 및 요일을 설정하려면 **수정**을 클릭합니다.

6. **디스크 할당 검토** 화면에서 보호 그룹에 할당된 저장소 풀 디스크 공간을 검토합니다.

    **총 데이터 크기**는 백업할 데이터 크기이고 Azure Backup Server에 **프로비전할 디스크 공간**은 보호 그룹에 권장되는 공간입니다. Azure Backup Server는 설정에 따라 가장 적합한 백업 볼륨을 선택합니다. 하지만 사용자가 디스크 할당 세부 정보에서 백업 볼륨 선택을 편집할 수 있습니다. 워크로드의 경우 드롭다운 메뉴에서 원하는 저장소를 선택합니다. 편집을 통해 [사용 가능한 디스크 저장소] 창에서 [총 저장소] 및 [사용 가능한 저장소]의 값을 변경합니다. 미달 프로비전된 공간은 향후 원활한 백업이 가능하도록 Azure Backup Server가 볼륨에 추가하도록 제안하는 저장소 용량입니다.

7. **복제본 만들기 방법 선택**에서 초기 전체 데이터 복제를 처리하는 방법을 선택합니다. 네트워크를 통해 복제하기로 선택할 경우 Azure는 사용량이 적은 시간을 선택할 것을 권장합니다. 대용량 데이터이거나 네트워크 상태가 최적화되지 않은 경우 이동식 미디어를 사용하여 데이터 복제를 고려하세요.

8. **일관성 확인 옵션 선택**에서 일관성 확인을 자동화할 방법을 선택합니다. 데이터 복제가 일관적이지 않은 경우에만 또는 일정에 따라서만 일관성 확인을 실행하도록 설정해야 합니다. 자동 일관성 확인을 원하지 않는 경우 언제든지 다음과 같은 방법으로 수동 확인을 실행할 수 있습니다.
    * Azure Backup Server 콘솔의 **보호** 영역에서 보호 그룹을 마우스 오른쪽 단추로 클릭하고 **일관성 확인 수행**을 선택합니다.

9. Azure에 백업하도록 선택하는 경우 **온라인 보호 데이터 지정** 페이지에서 Azure에 백업할 워크로드를 선택해야 합니다.

10. **온라인 백업 일정 지정** 페이지에서 Azure에 증분 백업을 수행할 시기를 지정합니다. 

    매일, 매주, 매월 및 매년 백업을 실행하도록 예약할 수 있습니다. Backup은 하루 두 번까지 수행할 수 있습니다. 백업 작업 실행될 때마다 Azure에는 Azure Bckup Server 디스크에 저장된 백업 데이터 복사본에서 데이터 복구 지점이 만들어집니다.

11. **온라인 보존 정책 지정**에서 매일, 매주, 매월 및 매년 백업에서 만들어진 복구 지점을 Azure에 보존하는 방법을 선택합니다.

12. **온라인 복제 선택**에서 데이터의 초기 전체 복제를 수행하는 방법을 지정합니다. 

13. **요약**에서 설정을 검토합니다. **그룹 만들기**를 클릭하면 초기 데이터 복제가 발생합니다. 데이터 복제가 완료되면 **상태** 페이지에서 보호 그룹 상태가 **정상**으로 표시됩니다. 초기 백업 작업은 보호 그룹 설정에 따라 수행됩니다.

## <a name="recover-file-data"></a>파일 데이터 복구

Azure Backup Server 콘솔을 사용하여 가상 머신에 데이터를 복구합니다.

1. Azure Backup Server의 탐색 모음에서 **복구**를 클릭하고 복구할 데이터를 탐색합니다. 결과 창에서 데이터를 선택합니다.

2. 복구 지점 선택의 달력에서 굵게 표시된 날짜는 사용 가능한 복구 지점을 나타냅니다. 복구할 날짜를 선택합니다.

3. **복구 가능한 항목** 창에서 복구하려는 항목을 선택합니다.

4. **작업 창**에서 **복구**를 클릭하여 복구 마법사를 엽니다.

5. 다음과 같이 데이터를 복구할 수 있습니다.

    * **원래 위치로 복구** - 클라이언트 컴퓨터가 VPN을 통해 연결된 경우 이 옵션은 작동하지 않습니다. 다른 위치를 사용하고, 해당 위치에서 데이터를 복사해야 합니다.
    * **대체 위치로 복구**

6. 복구 옵션을 지정합니다.

    * **기존 버전 복구 동작**의 경우 **복사본 만들기**, **건너뛰기** 또는 **덮어쓰기**를 선택합니다. 덮어쓰기는 원래 위치로 복구하는 경우에만 사용할 수 있습니다.
    * **보안 복원**의 경우 **대상 컴퓨터의 설정 적용** 또는 **복구 지점 버전의 보안 설정 적용**을 선택합니다.
    * **네트워크 대역폭 사용 제한**의 경우 **수정**을 클릭하여 네트워크 대역폭 사용 제한을 사용하도록 설정합니다.
    * **알림** **복구가 완료되면 이메일 보내기**를 클릭하고, 알림을 받을 사람을 지정합니다. 이메일 주소를 쉼표로 구분합니다.
    * 모두 선택한 후에는 **다음**을 클릭합니다.

7. 복구 설정을 검토하고 **복구**를 클릭합니다. 

    > [!Note] 
    > 복구 작업이 진행 중인 동안에는 선택한 복구 항목에 대한 모든 동기화 작업이 취소됩니다.
    >

MBS(Modern Backup Storage)를 사용하는 경우 파일 서버 EUR(최종 사용자 복구)이 지원되지 않습니다. 파일 서버 EUR은 Modern Backup Storage에서 사용하지 않는 VSS(볼륨 섀도 복사본)에 종속되어 있습니다. EUR을 사용하는 경우 다음 단계에 따라 데이터를 복구합니다.

1. 보호되는 파일로 이동하여 파일 이름을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

2. **속성** 메뉴에서 **이전 버전**을 클릭하고 복구할 버전을 선택합니다.

## <a name="view-azure-backup-server-with-a-vault"></a>자격 증명 모음으로 Azure Backup Server 보기
Azure Portal에서 Azure Backup Server 엔터티를 보려면 다음 단계를 따르면 됩니다.
1. Recovery Services 자격 증명 모음 열기
2. 백업 인프라 클릭
3. 백업 관리 서버 보기

## <a name="see-also"></a>참고 항목
Azure Backup Server를 사용하여 다른 워크로드를 보호하는 방법에 대한 내용은 다음 아티클 중 하나를 참조하세요.
- [SharePoint 팜 백업](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [SQL Server 백업](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
