---
title: StorSimple Snapshot Manager 및 볼륨 | Microsoft Docs
description: StorSimple 스냅숏 관리자 MMC 스냅인을 사용하여 볼륨을 보고 관리하고 백업을 구성하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: v-sharos
ms.openlocfilehash: 260dfdd4b8fe7c277358fa5773029ea9a532740a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61078303"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>StorSimple 스냅숏 관리자를 사용하여 볼륨 보기 및 관리
## <a name="overview"></a>개요
**범위** 창에서 StorSimple Snapshot Manager **볼륨** 노드를 사용하여 볼륨을 선택하고 관련 정보를 볼 수 있습니다. 볼륨은 호스트에 의해 탑재된 볼륨에 해당하는 드라이브로 표시됩니다. **볼륨** 노드는 iSCSI와 디바이스를 사용하여 검색한 볼륨을 포함하여 StorSimple에서 지원하는 로컬 볼륨 및 볼륨 유형을 표시합니다. 

지원되는 볼륨에 대한 자세한 내용은 [여러 볼륨 유형에 대한 지원](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types)을 참조하세요.

![결과 창의 볼륨 목록](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

또한 **볼륨** 노드를 사용하면 StorSimple 스냅숏 관리자에서 검색한 후에도 볼륨을 다시 검사하거나 삭제할 수 있습니다. 

이 자습서에서는 볼륨을 탑재, 초기화 및 포맷한 다음 StorSimple Snapshot Manager를 사용하여 다음 작업을 수행하는 방법에 대해 설명합니다.

* 볼륨에 대한 정보 보기 
* 볼륨 삭제
* 볼륨 다시 검사 
* 기본 볼륨 구성 및 백업
* 동적 미러 볼륨 구성 및 백업

> [!NOTE]
> 모든 **볼륨** 노드 작업은 **작업** 창에서도 사용할 수 있습니다.
> 
> 

## <a name="mount-volumes"></a>볼륨 탑재
다음 절차에 따라 StorSimple 볼륨을 탑재, 초기화 및 포맷할 수 있습니다. 이 절차는 하드 디스크 및 해당 볼륨 또는 파티션을 관리하기 위해 디스크 관리, 시스템 유틸리티를 사용합니다. 디스크 관리에 대한 자세한 내용은 Microsoft TechNet 웹 사이트에서 [디스크 관리](https://technet.microsoft.com/library/cc770943.aspx) 로 이동하세요.

#### <a name="to-mount-volumes"></a>볼륨을 탑재하려면
1. 호스트 컴퓨터에서 Microsoft iSCSI 초기자를 시작합니다.
2. 대상 포털로 인터페이스 IP 주소 중 하나를 제공하거나 IP 주소를 검색한 후 디바이스에 연결합니다. 디바이스가 연결된 후 Windows 시스템에서 볼륨에 액세스할 수 있습니다. Microsoft iSCSI 초기자 사용에 대한 자세한 내용은 [Microsoft iSCSI 초기자 설치 및 구성][1]에서 “iSCSI 대상 디바이스에 연결” 섹션을 참조하세요.
3. 다음 옵션 중 하나를 사용하여 디스크 관리를 시작합니다.
   
   * **실행** 상자에 Diskmgmt.msc를 입력합니다.
   * 서버 관리자를 시작하고 **저장소** 노드를 확장한 다음 **디스크 관리**를 선택합니다.
   * **관리 도구**를 시작하고 **컴퓨터 관리** 노드를 확장한 다음 **디스크 관리**를 선택합니다. 
     
     > [!NOTE]
     > 관리자 권한을 사용하여 디스크 관리를 실행해야 합니다.
     > 
     > 
4. 볼륨을 온라인 상태로 만듭니다.
   
   1. 디스크 관리에서 **오프라인**으로 표시된 볼륨을 마우스 오른쪽 단추로 클릭합니다.
   2. **디스크 다시 활성화**를 클릭합니다. 디스크를 다시 활성화한 후에는 해당 디스크가 **온라인**으로 표시되어야 합니다.
5. 볼륨 초기화:
   
   1. 검색된 볼륨을 마우스 오른쪽 단추로 클릭합니다.
   2. 메뉴에서 **디스크 초기화**를 선택합니다.
   3. **디스크 초기화** 대화 상자에서 초기화하려는 디스크를 선택한 다음 **확인**을 클릭합니다.
6. 단순 볼륨을 포맷합니다.
   
   1. 포맷할 볼륨을 마우스 오른쪽 단추로 클릭합니다.
   2. 메뉴에서 **새 단순 볼륨**을 선택합니다.
   3. 새 단순 볼륨 마법사를 사용하여 볼륨을 포맷합니다.
      
      * 볼륨 크기를 지정합니다.
      * 드라이브 문자를 지정합니다.
      * NTFS 파일 시스템을 선택합니다.
      * 64KB 할당 단위 크기를 지정합니다.
      * 빠른 포맷을 수행합니다.
7. 다중 파티션 볼륨을 포맷합니다. 자세한 지침은 [디스크 관리 구현](https://msdn.microsoft.com/library/dd163556.aspx)에서 "파티션 및 볼륨" 섹션을 참조하세요.

## <a name="view-information-about-your-volumes"></a>볼륨에 대한 정보 보기
다음 절차에 따라 로컬 및 Azure StorSimple 볼륨에 대한 정보를 볼 수 있습니다.

#### <a name="to-view-volume-information"></a>볼륨 정보를 보려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple Snapshot Manager를 시작합니다. 
2. **범위** 창에서 **볼륨** 노드를 클릭합니다. 모든 Azure StorSimple 볼륨을 포함하여 로컬 볼륨 및 탑재된 볼륨 목록이 **결과** 창에 표시됩니다. **결과** 창의 열은 구성할 수 있습니다. (**볼륨** 노드를 마우스 오른쪽 단추로 클릭하고 **보기**를 선택한 다음 **열 추가/제거**를 선택합니다.)
   
    ![열 구성](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | 결과 열 | 설명 |
   |:--- |:--- |
   |  이름 |**이름** 열에는 검색된 각 볼륨에 할당된 드라이브 문자가 포함되어 있습니다. |
   |  디바이스 |**디바이스** 열은 호스트 컴퓨터에 연결된 디바이스의 IP 주소를 포함합니다. |
   |  디바이스 볼륨 이름 |**디바이스 볼륨 이름** 열은 선택한 볼륨이 속한 디바이스 볼륨의 이름을 포함합니다. 이 이름은 해당 특정 볼륨에 대해 Azure Portal에서 정의한 볼륨 이름입니다. |
   |  액세스 경로 |**액세스 경로** 열은 볼륨에 대한 액세스 경로를 표시합니다. 호스트 컴퓨터에서 볼륨에 액세스할 수 있는 드라이브 문자 또는 탑재 지점입니다. |

## <a name="delete-a-volume"></a>볼륨 삭제
다음 절차에 따라 StorSimple Snapshot Manager에서 볼륨을 삭제할 수 있습니다.

> [!NOTE]
> 볼륨 그룹에 속해 있는 볼륨은 삭제할 수 없습니다. (볼륨 그룹에 속한 볼륨에 대해서는 삭제 옵션을 사용할 수 없습니다.) 볼륨을 삭제하려면 전체 볼륨 그룹을 삭제해야 합니다.

#### <a name="to-delete-a-volume"></a>볼륨을 삭제하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple Snapshot Manager를 시작합니다.
2. **범위** 창에서 **볼륨** 노드를 클릭합니다. 
3. **결과** 창에서 삭제하려는 볼륨을 마우스 오른쪽 단추로 클릭합니다.
4. 메뉴에서 **삭제**를 클릭합니다. 
   
    ![볼륨 삭제](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. **볼륨 삭제** 대화 상자가 나타납니다. 텍스트 상자에 **확인**을 입력한 다음 **확인**을 클릭합니다.
6. 기본적으로 StorSimple Snapshot Manager는 삭제하기 전에 볼륨을 백업합니다. 이 예방 조치를 통해 실수로 삭제하는 경우의 데이터 손실을 방지할 수 있습니다. StorSimple Snapshot Manager는 볼륨을 백업하는 동안 **자동 스냅숏** 진행률 메시지를 표시합니다. 
   
    ![자동 스냅숏 메시지](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>볼륨 다시 검사
다음 절차에 따라 StorSimple 스냅숏 관리자에 연결된 볼륨을 다시 검사할 수 있습니다.

#### <a name="to-rescan-the-volumes"></a>볼륨을 다시 검사하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple Snapshot Manager를 시작합니다.
2. **범위** 창에서 **볼륨**을 마우스 오른쪽 단추로 클릭한 다음 **볼륨 다시 검사**를 클릭합니다.
   
    ![볼륨 다시 검사](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    이 절차는 볼륨 목록을 StorSimple Snapshot Manager와 동기화합니다. 새로운 볼륨이나 삭제된 볼륨 등의 모든 변경 내용이 결과에 반영됩니다.

## <a name="configure-and-back-up-a-basic-volume"></a>기본 볼륨 구성 및 백업
다음 절차에 따라 기본 볼륨의 백업을 구성한 다음 백업을 즉시 시작하거나 예약된 백업에 대한 정책을 만들 수 있습니다.

### <a name="prerequisites"></a>필수 조건
시작하기 전에

* StorSimple 디바이스 및 호스트 컴퓨터가 올바르게 구성되었는지 확인합니다. 자세한 내용은 [온-프레미스 StorSimple 디바이스 배포](storsimple-deployment-walkthrough-u2.md)를 참조하세요.
* StorSimple Snapshot Manager 설치 및 구성 자세한 내용은 [StorSimple Snapshot Manager 배포](storsimple-snapshot-manager-deployment.md)를 참조하세요.

#### <a name="to-configure-backup-of-a-basic-volume"></a>기본 볼륨의 백업을 구성하려면
1. StorSimple 디바이스에서 기본 볼륨을 만듭니다.
2. [볼륨 탑재](#mount-volumes)에 설명된 대로 볼륨을 탑재, 초기화 및 포맷합니다. 
3. 바탕 화면에서 StorSimple Snapshot Manager 아이콘을 클릭합니다. StorSimple Snapshot Manager 창이 나타납니다. 
4. **범위** 창에서 **볼륨** 노드를 마우스 오른쪽 단추로 클릭한 다음 **볼륨 다시 검사**를 선택합니다. 검사가 끝나면 볼륨 목록이 **결과** 창에 나타납니다. 
5. **결과** 창에서 볼륨을 마우스 오른쪽 단추로 클릭한 다음 **볼륨 그룹 만들기**를 선택합니다. 
   
    ![볼륨 그룹 만들기](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. **볼륨 그룹 만들기** 대화 상자에서 볼륨 그룹의 이름을 입력하고 이 그룹에 볼륨을 할당한 다음 **확인**을 클릭합니다.
7. **범위** 창에서 **볼륨 그룹** 노드를 확장합니다. 새 볼륨 그룹이 **볼륨 그룹** 노드 아래에 나타납니다. 
8. 볼륨 그룹 이름을 마우스 오른쪽 단추로 클릭합니다.
   
   * 대화형(주문형) 백업 작업을 시작하려면 **Backup 수행**을 클릭합니다. 
   * 자동 백업을 예약하려면 **Backup 정책 만들기**를 클릭합니다. **일반** 페이지의 목록에서 볼륨 그룹을 선택합니다. **일정** 페이지에서 일정 정보를 입력합니다. 작업을 마쳤으면 **확인**을 클릭합니다. 
9. 백업 작업이 시작되었는지 확인하려면 **범위** 창에서 **작업** 노드를 확장한 다음 **실행 중** 노드를 클릭합니다. 현재 실행 중인 작업의 목록이 **결과** 창에 표시됩니다. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>동적 미러 볼륨 구성 및 백업
다음 단계를 완료하여 동적 미러 볼륨에 대한 백업을 구성할 수 있습니다.

* 1단계: 디스크 관리를 사용 하 여 동적 미러 볼륨을 만듭니다. 
* 2단계: StorSimple 스냅숏 관리자를 사용 하 여 백업을 구성 하려면입니다.

### <a name="prerequisites"></a>필수 조건
시작하기 전에

* StorSimple 디바이스 및 호스트 컴퓨터가 올바르게 구성되었는지 확인합니다. 자세한 내용은 [온-프레미스 StorSimple 디바이스 배포](storsimple-8000-deployment-walkthrough-u2.md)를 참조하세요.
* StorSimple Snapshot Manager 설치 및 구성 자세한 내용은 [StorSimple Snapshot Manager 배포](storsimple-snapshot-manager-deployment.md)를 참조하세요.
* StorSimple 디바이스에 두 볼륨을 구성합니다. (예제에서 사용할 수 있는 볼륨은 **디스크 1** 및 **디스크 2**입니다.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>1단계: 디스크 관리를 사용 하 여 동적 미러 볼륨을 만들려면
디스크 관리는 하드 디스크 및 볼륨 또는 하드 디스크와 볼륨이 포함된 파티션을 관리하기 위한 시스템 유틸리티입니다. 디스크 관리에 대한 자세한 내용은 Microsoft TechNet 웹 사이트에서 [디스크 관리](https://technet.microsoft.com/library/cc770943.aspx)로 이동하세요.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>동적 미러 볼륨을 만들려면
1. 다음 옵션 중 하나를 사용하여 디스크 관리를 시작합니다. 
   
   * **실행** 상자를 열고 **Diskmgmt.msc**를 입력한 다음 Enter 키를 누릅니다.
   * 서버 관리자를 시작하고 **저장소** 노드를 확장한 다음 **디스크 관리**를 선택합니다. 
   * **관리 도구**를 시작하고 **컴퓨터 관리** 노드를 확장한 다음 **디스크 관리**를 선택합니다. 
2. StorSimple 디바이스에서 사용할 수 있는 두 볼륨이 있는지 확인합니다. (예제에서 사용할 수 있는 볼륨은 **디스크 1** 및 **디스크 2**입니다.) 
3. 디스크 관리 창에서 아래쪽 창의 오른쪽 열에 있는 **디스크 1**을 마우스 오른쪽 단추로 클릭하고 **새 미러 볼륨**을 선택합니다. 
   
    ![새 미러 볼륨](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. **새 미러 볼륨** 마법사 페이지에서 **다음**을 클릭합니다.
5. **디스크 선택** 페이지의 **선택된** 창에서 **디스크 2**를 선택하고 **추가**를 클릭한 후 **다음**을 클릭합니다. 
6. **드라이브 문자 또는 경로 할당** 페이지에서 기본값을 적용하고 **다음**을 클릭합니다. 
7. **볼륨 포맷** 페이지의 **할당 단위 크기** 상자에서 **64K**를 선택합니다. **빠른 포맷 실행** 확인란을 선택하고 **다음**을 클릭합니다. 
8. **새 미러 볼륨 완료** 페이지에서 설정을 검토하고 **마침**을 클릭합니다. 
9. 기본 디스크가 동적 디스크로 변환된다는 메시지가 나타납니다. **예**를 클릭합니다.
   
    ![동적 디스크 변환 메시지](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. 디스크 관리에서 디스크 1과 디스크 2가 동적 미러 볼륨으로 표시되는지 확인합니다. (**동적**이 상태 열에 표시되고 용량 막대의 색은 미러 볼륨을 나타내는 빨강으로 변경되어야 합니다.) 
    
    ![디스크 관리에서 미러링한 동적 디스크](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>2단계: 백업을 구성 하려면 StorSimple 스냅숏 관리자를 사용 하 여
다음 절차에 따라 동적 미러 볼륨을 구성한 다음 백업을 즉시 시작하거나 예약된 백업에 대한 정책을 만듭니다.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>동적 미러 볼륨의 백업을 구성하려면
1. 바탕 화면에서 StorSimple Snapshot Manager 아이콘을 클릭합니다. StorSimple Snapshot Manager 창이 나타납니다. 
2. **범위** 창에서 **볼륨** 노드를 마우스 오른쪽 단추로 클릭하고 **볼륨 다시 검사**를 선택합니다. 검사가 끝나면 볼륨 목록이 **결과** 창에 나타납니다. 동적 미러 볼륨은 단일 볼륨으로 나열됩니다. 
3. **결과** 창에서 동적 미러 볼륨을 마우스 오른쪽 단추로 클릭한 다음 **볼륨 그룹 만들기**를 클릭합니다. 
4. **볼륨 그룹 만들기** 대화 상자에서 볼륨 그룹의 이름을 입력하고 이 그룹에 동적 미러 볼륨을 할당한 다음 **확인**을 클릭합니다. 
5. **범위** 창에서 **볼륨 그룹** 노드를 확장합니다. 새 볼륨 그룹이 **볼륨 그룹** 노드 아래에 나타나야 합니다. 
6. 볼륨 그룹 이름을 마우스 오른쪽 단추로 클릭합니다. 
   
   * 대화형(주문형) 백업 작업을 시작하려면 **Backup 수행**을 클릭합니다. 
   * 자동 백업을 예약하려면 **Backup 정책 만들기**를 클릭합니다. **일반** 페이지의 목록에서 볼륨 그룹을 선택합니다. **일정** 페이지에서 일정 정보를 입력합니다. 작업을 마쳤으면 **확인**을 클릭합니다. 
7. 실행되는 백업 작업을 모니터링할 수 있습니다. **범위** 창에서 **작업** 노드를 확장한 다음 **실행 중**을 클릭합니다. 작업 세부 정보가 **결과** 창에 표시됩니다. 백업 작업이 완료되면 세부 정보가 **최근 24**시간 작업 목록으로 전송됩니다. 

## <a name="next-steps"></a>다음 단계
* [StorSimple Snapshot Manager를 사용하여 StorSimple 솔루션을 관리](storsimple-snapshot-manager-admin.md)하는 방법을 알아봅니다.
* [StorSimple Snapshot Manager를 사용하여 볼륨 그룹을 만들고 관리](storsimple-snapshot-manager-manage-volume-groups.md)하는 방법을 알아봅니다.

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
