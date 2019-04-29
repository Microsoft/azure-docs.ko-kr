---
title: StorSimple Snapshot Manager 백업 카탈로그 | Microsoft Docs
description: StorSimple 스냅숏 관리자 MMC 스냅인을 사용하여 백업 카탈로그를 보고 관리하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: dc24ebd59fd977ef35766c304aec5824e2c7bb4c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127193"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>StorSimple 스냅숏 관리자를 사용하여 백업 카탈로그 관리

## <a name="overview"></a>개요
StorSimple Snapshot Manager의 기본 기능은 StorSimple 볼륨의 애플리케이션과 일치하는 백업 복사본을 스냅숏의 형태로 만들 수 있도록 하는 것입니다. 스냅숏은 *백업 카탈로그*라는 XML 파일에 나열됩니다. 백업 카탈로그는 볼륨 그룹별로 스냅숏을 구성한 다음 로컬 스냅숏 또는 클라우드 스냅숏별로 구성합니다.

이 자습서에서는 **백업 카탈로그** 노드를 사용하여 다음 작업을 완료하는 방법을 설명합니다.

* 볼륨 복원
* 볼륨 또는 볼륨 그룹 복제
* 백업 삭제
* 파일 복구
* StorSimple 스냅숏 관리자 데이터베이스 복원

**범위** 창에서 **Backup 카탈로그** 노드를 확장한 후 볼륨 그룹을 확장하면 백업 카탈로그를 볼 수 있습니다.

* 볼륨 그룹 이름을 클릭하면 **결과** 창에 해당 볼륨 그룹에서 사용할 수 있는 로컬 스냅숏 및 클라우드 스냅숏의 개수가 표시됩니다. 
* **로컬 스냅숏** 또는 **클라우드 스냅숏**을 클릭하면 **결과** 창에 각 백업 스냅숏에 대한 다음 정보가 표시됩니다(**보기** 설정에 따라 다름).
  
  * **이름** – 스냅숏을 만든 시간.
  * **유형** – 로컬 스냅숏인지 또는 클라우드 스냅숏인지.
  * **소유자** – 콘텐츠 소유자. 
  * **사용 가능** – 스냅숏을 현재 사용할 수 있는지 여부입니다. **True**이면 스냅숏을 사용할 수 있고 복원 가능합니다. **False**이면 스냅숏을 더 이상 사용할 수 없습니다. 
  * **가져옴** – 백업을 가져왔는지 여부입니다. **True**이면 StorSimple Snapshot Manager에서 디바이스를 구성했을 때 백업을 StorSimple 디바이스 관리자 서비스에서 가져왔습니다. **False**이면 백업을 가져오지 않았지만 StorSimple Snapshot Manager에서는 만들었습니다. (가져온 볼륨 그룹에는 해당 볼륨 그룹을 가져온 원본 디바이스를 식별하는 접미사가 추가되므로 쉽게 식별할 수 있습니다.)
    
    ![Backup 카탈로그](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* **로컬 스냅숏** 또는 **클라우드 스냅숏**을 확장한 다음 개별 스냅숏 이름을 클릭하면 **결과** 창에 선택한 스냅숏에 대해 다음 정보가 표시됩니다.
  
  * **이름** – 드라이브 문자로 식별되는 볼륨. 
  * **로컬 이름** – 드라이브의 로컬 이름(있는 경우). 
  * **디바이스** – 볼륨이 있는 디바이스의 이름. 
  * **사용 가능** – 스냅숏을 현재 사용할 수 있는지 여부입니다. **True**이면 스냅숏을 사용할 수 있고 복원 가능합니다. **False**이면 스냅숏을 더 이상 사용할 수 없습니다. 

## <a name="restore-a-volume"></a>볼륨 복원
다음 절차를 사용하여 백업에서 볼륨을 복원합니다.

#### <a name="prerequisites"></a>필수 조건
아직 하지 않은 경우, 볼륨 및 볼륨 그룹을 만든 다음 볼륨을 삭제합니다. 기본적으로 StorSimple 스냅숏 관리자는 삭제를 허용하기 전에 볼륨을 백업합니다. 이 예방 조치를 통해 볼륨을 실수로 삭제한 경우 또는 어떤 이유로든 데이터를 복구해야 하는 경우 데이터 손실을 방지할 수 있습니다. 

StorSimple 스냅숏 관리자는 예비 백업이 생성되는 동안 다음 메시지를 표시합니다.

![자동 스냅숏 메시지](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> 볼륨 그룹에 속해 있는 볼륨은 삭제할 수 없습니다. 삭제 옵션을 사용할 수 없습니다. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>볼륨을 복원하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple Snapshot Manager를 시작합니다. 
2. **범위** 창에서 **Backup 카탈로그** 노드를 확장하고 볼륨 그룹을 확장한 다음 **로컬 스냅숏** 또는 **클라우드 스냅숏**을 클릭합니다. 백업 스냅숏 목록이 **결과** 창에 표시됩니다.
3. 복원할 백업을 찾아 마우스 오른쪽 단추로 클릭한 다음 **복원**을 클릭합니다.
   
    ![백업 카탈로그 복원](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. 확인 페이지에서 세부 정보를 검토하고 **Confirm**을 입력한 다음 **확인**을 클릭합니다. StorSimple 스냅숏 관리자가 백업을 사용하여 볼륨을 복원합니다.
   
    ![확인 메시지 복원](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. 실행되는 복원 작업을 모니터링할 수 있습니다. **범위** 창에서 **작업** 노드를 확장한 다음 **실행 중**을 클릭합니다. 작업 세부 정보가 **결과** 창에 표시됩니다. 복원 작업이 완료되면 작업 세부 정보가 **최근 24시간** 목록으로 전송됩니다.

## <a name="clone-a-volume-or-volume-group"></a>볼륨 또는 볼륨 그룹 복제
다음 절차를 사용하여 볼륨 또는 볼륨 그룹의 클론을 만듭니다.

#### <a name="to-clone-a-volume-or-volume-group"></a>볼륨 또는 볼륨 그룹을 복제하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple Snapshot Manager를 시작합니다.
2. **범위** 창에서 **Backup 카탈로그** 노드를 확장하고 볼륨 그룹을 확장한 다음 **클라우드 스냅숏**을 클릭합니다. 백업 목록이 **결과** 창에 표시됩니다.
3. 복제할 볼륨 또는 볼륨 그룹을 찾아 해당 볼륨 또는 볼륨 그룹 이름을 마우스 오른쪽 단추로 클릭하고 **복제**를 클릭합니다. **클라우드 스냅숏 복제** 대화 상자가 나타납니다.
   
    ![클라우드 스냅숏 복제](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. **클라우드 스냅숏 복제** 대화 상자를 다음과 같이 완료합니다. 
   
   1. **이름** 텍스트 상자에 복제된 볼륨의 이름을 입력합니다. 이 이름이 **볼륨** 노드에 나타납니다. 
   2. (선택 사항) **드라이브**를 선택한 다음 드롭다운 목록에서 드라이브 문자를 선택합니다.
   3. (선택 사항) **폴더(NTFS)** 를 선택하고 폴더 경로를 입력하거나 찾아보기를 클릭하고 폴더 위치를 선택합니다. 
   4. **만들기**를 클릭합니다.
5. 복제 프로세스가 완료되면 복제된 볼륨을 초기화해야 합니다. 서버 관리자를 시작한 다음 디스크 관리를 시작합니다. 자세한 지침은 [볼륨 탑재](storsimple-snapshot-manager-manage-volumes.md#mount-volumes)를 참조하세요. 초기화 후 볼륨이 **범위** 창의 **볼륨** 노드 아래에 나열됩니다. 나열된 볼륨이 보이지 않으면 볼륨 목록을 새로 고칩니다(**볼륨** 노드를 마우스 오른쪽 단추로 클릭한 다음 **새로 고침** 클릭).

## <a name="delete-a-backup"></a>백업 삭제
다음 절차를 사용하여 백업 카탈로그에서 스냅숏을 삭제합니다. 

> [!NOTE]
> 스냅숏을 삭제하면 스냅숏에 연결된 백업된 데이터도 삭제됩니다. 그러나 클라우드에서 데이터를 정리하는 과정에는 시간이 좀 걸릴 수 있습니다.<br>


#### <a name="to-delete-a-backup"></a>백업을 삭제하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple Snapshot Manager를 시작합니다.
2. **범위** 창에서 **Backup 카탈로그** 노드를 확장하고 볼륨 그룹을 확장한 다음 **로컬 스냅숏** 또는 **클라우드 스냅숏**을 클릭합니다. 스냅숏 목록이 **결과** 창에 표시됩니다.
3. 삭제할 스냅숏을 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.
4. 확인 메시지가 나타나면 **확인**을 클릭합니다.

## <a name="recover-a-file"></a>파일 복구
볼륨에서 파일을 실수로 삭제하는 경우, 삭제 이전 날짜의 스냅숏을 검색하고 해당 스냅숏을 사용하여 볼륨의 클론을 만든 다음 복제된 볼륨에서 원래 볼륨으로 파일을 복사하면 파일을 복구할 수 있습니다.

#### <a name="prerequisites"></a>필수 조건
시작하기 전에 볼륨 그룹의 최신 백업이 있는지 확인합니다. 그런 다음 해당 볼륨 그룹의 볼륨 중 하나에 저장된 파일을 삭제합니다. 마지막으로, 다음 단계를 사용하여 삭제된 파일을 백업에서 복원합니다. 

#### <a name="to-recover-a-deleted-file"></a>삭제된 파일을 복구하려면
1. 바탕 화면에서 StorSimple 스냅숏 관리자 아이콘을 클릭합니다. StorSimple 스냅숏 관리자 콘솔 창이 나타납니다. 
2. **범위** 창에서 **Backup 카탈로그** 노드를 확장한 후 삭제된 파일이 포함된 스냅숏을 찾습니다. 일반적으로 삭제되기 바로 전에 만든 스냅숏을 선택해야 합니다.
3. 복제할 볼륨을 찾아서 마우스 오른쪽 단추로 클릭한 후 **복제**를 클릭합니다. **클라우드 스냅숏 복제** 대화 상자가 나타납니다.
   
    ![클라우드 스냅숏 복제](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. **클라우드 스냅숏 복제** 대화 상자를 다음과 같이 완료합니다. 
   
   1. **이름** 텍스트 상자에 복제된 볼륨의 이름을 입력합니다. 이 이름이 **볼륨** 노드에 나타납니다. 
   2. (선택 사항) **드라이브**를 선택한 다음 드롭다운 목록에서 드라이브 문자를 선택합니다. 
   3. (선택 사항) **폴더(NTFS)** 를 선택하고 폴더 경로를 입력하거나 **찾아보기**를 클릭하고 폴더 위치를 선택합니다. 
   4. **만들기**를 클릭합니다. 
5. 복제 프로세스가 완료되면 복제된 볼륨을 초기화해야 합니다. 서버 관리자를 시작한 다음 디스크 관리를 시작합니다. 자세한 지침은 [볼륨 탑재](storsimple-snapshot-manager-manage-volumes.md#mount-volumes)를 참조하세요. 초기화 후 볼륨이 **범위** 창의 **볼륨** 노드 아래에 나열됩니다. 
   
    나열된 볼륨이 보이지 않으면 볼륨 목록을 새로 고칩니다(**볼륨** 노드를 마우스 오른쪽 단추로 클릭한 다음 **새로 고침** 클릭).
6. 복제된 볼륨이 있는 NTFS 폴더를 열고 **볼륨** 노드를 확장한 다음 해당 복제된 볼륨을 엽니다. 복구할 파일을 찾아서 기본 볼륨에 복사합니다.
7. 파일을 복원한 후에는 복제된 볼륨이 포함되어 있는 NTFS 폴더를 삭제할 수 있습니다.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>StorSimple 스냅숏 관리자 데이터베이스 복원
호스트 컴퓨터에서 정기적으로 StorSimple 스냅숏 관리자 데이터베이스를 백업해야 합니다. 재해가 발생하거나 어떤 이유로든 호스트 컴퓨터에서 오류가 발생하면 백업에서 복원할 수 있습니다. 데이터베이스 백업 만들기는 수동 프로세스입니다.

#### <a name="to-back-up-and-restore-the-database"></a>데이터베이스를 백업 및 복원하려면
1. Microsoft StorSimple 관리 서비스를 중지합니다.
   
   1. 서버 관리자를 시작합니다.
   2. 서버 관리자 대시보드의 **도구** 메뉴에서 **서비스**를 선택합니다.
   3. **서비스** 창에서 **Microsoft StorSimple 관리 서비스**를 선택합니다.
   4. 오른쪽 창의 **Microsoft StorSimple 관리 서비스** 아래에서 **서비스 중지**를 클릭합니다.
2. 호스트 컴퓨터에서 C:\ProgramData\Microsoft\StorSimple\BACatalog로 이동합니다. 
   
   > [!NOTE]
   > ProgramData는 숨겨진 폴더입니다.
   > 
   > 
3. 카탈로그 XML 파일을 찾아 파일을 복사하고 안전한 위치 또는 클라우드에 복사본을 저장합니다. 호스트에서 오류가 발생하는 경우 이 백업 파일을 사용하여 StorSimple 스냅숏 관리자에서 만든 백업 정책을 복구할 수 있도록 도움을 줄 수 있습니다.
   
    ![Azure StorSimple 백업 카탈로그 파일](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Microsoft StorSimple 관리 서비스를 다시 시작합니다. 
   
   1. 서버 관리자 대시보드의 **도구** 메뉴에서 **서비스**를 선택합니다.
   2. **서비스** 창에서 **Microsoft StorSimple 관리 서비스**를 선택합니다.
   3. 오른쪽 창의 **Microsoft StorSimple 관리 서비스** 아래에서 **서비스 다시 시작**을 클릭합니다.
5. 호스트 컴퓨터에서 C:\ProgramData\Microsoft\StorSimple\BACatalog로 이동합니다. 
6. 카탈로그 XML 파일을 삭제하고 사용자가 만든 백업 버전으로 바꿉니다. 
7. 바탕 화면의 StorSimple 스냅숏 관리자 아이콘을 클릭하여 StorSimple 스냅숏 관리자를 시작합니다. 

## <a name="next-steps"></a>다음 단계
* [StorSimple 스냅숏 관리자를 사용하여 StorSimple 솔루션을 관리](storsimple-snapshot-manager-admin.md)하는 방법을 자세히 알아봅니다.
* [StorSimple 스냅숏 관리자 작업 및 워크플로](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows)에 대해 자세히 알아봅니다.

