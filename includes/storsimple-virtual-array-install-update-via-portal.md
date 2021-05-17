---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: df572b88cf8a67163b28ec87154dcea01646b9a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95554338"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Azure Portal을 통해 업데이트를 설치하려면

1. StorSimple 디바이스 관리자를 이동하고 **디바이스** 를 선택합니다. 서비스에 연결된 디바이스 목록에서 업데이트하려는 디바이스를 선택하고 클릭합니다. 

    ![최근 디바이스 관리자 MySSDevManager가 강조 표시되고 선택되었습니다. 디바이스가 강조 표시되고 선택되며, 디바이스 MYSSIS1103이 강조 표시되고 선택됩니다.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate1m.png) 

2. **설정** 블레이드에서 **디바이스 업데이트** 를 클릭합니다. 

    ![MYSSIS1103에 대한 정보가 표시됩니다. 설정에서 디바이스 업데이트가 강조 표시됩니다.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate2m.png)  

3. 소프트웨어 업데이트를 사용할 수 있는 경우 메시지가 나타납니다. 업데이트를 확인하려면 **스캔** 을 클릭할 수도 있습니다.

    ![디바이스 업데이트 창에서 스캔 단추가 강조 표시됩니다. 최종 검사 / 검사되지 않음 / 소프트웨어 버전 / 10.0.10280.0의 네 줄 메시지가 있습니다.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate3m.png)

    스캔이 시작되고 성공적으로 완료되면 알림이 표시됩니다.

    ![알림에는 "디바이스 MySSIS1103.2:12 PM에 대한 업데이트 검색 / 작업을 성공적으로 완료했습니다."라고 표시됩니다.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate5m.png)

4. 업데이트가 스캔되면 **업데이트 다운로드** 를 클릭합니다. 

    ![디바이스 업데이트 창에는 "새 업데이트를 사용할 수 있음" 및 "최종 검사 / 2016/11/3 2:12 PM / 소프트웨어 버전 / 10.0.10280.0"이 표시됩니다. 버전이 강조 표시됩니다.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate6m.png)

5. **새 업데이트** 블레이드에서 업데이트를 다운로드한 후에 정보를 검토하고 설치를 확인해야 합니다. **확인** 을 클릭합니다.

    ![새 업데이트 창에는 "업데이트를 다운로드한 후에는 설치를 확인해야 합니다."라고 표시됩니다. 확인 단추가 강조 표시됩니다.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate7m.png)

6. 업로드가 시작되고 성공적으로 완료되면 알림이 표시됩니다.

     ![알림에는 "디바이스 MySSIS 업데이트를 다운로드하는 중… 오후 2:13"이라고 표시됩니다.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate8m.png)

5. **디바이스 업데이트** 블레이드에서 **설정** 을 클릭합니다.

     ![디바이스 업데이트 창에 "업데이트 설치 확인" 메시지가 표시됩니다. 설치 단추가 강조 표시됩니다.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate11m.png)   

6. **새 업데이트** 블레이드에서 업데이트가 중단되었다는 경고가 나타납니다. 가상 배열이 단일 노드 디바이스인 경우 디바이스가 업데이트된 후에 다시 시작됩니다. 따라서 진행 중인 모든 IO가 중단됩니다. **확인** 을 클릭하여 업데이트를 설치합니다. 

    ![새 업데이트 창의 메시지는 "이러한 업데이트가 설치될 때 디바이스에서 가동 중지 시간이 발생합니다."입니다. 확인 단추가 강조 표시됩니다.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate12m.png) 

7. 설치 작업이 시작될 때 알림을 받습니다. 

    ![알림에는 "디바이스 MYSSIS1103에 대한 업데이트 설치"라고 표시됩니다. 오후 2:15.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate13m.png)

8.  설치 작업을 성공적으로 완료한 후에 **디바이스 업데이트** 블레이드에서 **작업 보기** 링크를 클릭하여 설치를 모니터링합니다. 

    ![디바이스 업데이트 창에서 "업데이트 설치. 작업 보기"라는 레이블이 지정된 링크가 강조 표시됩니다.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate15m.png)

    **업데이트 설치** 블레이드로 이동합니다. 이 작업에 대한 자세한 정보가 표시됩니다.

    ![업데이트 설치 창에는 디바이스, 상태, 기간, 시작 시간 및 중지 시간을 포함한 설치 정보가 있습니다.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate16m.png)

9. 업데이트가 성공적으로 설치된 후에 디바이스 업데이트 블레이드에서 이 효과에 대한 메시지가 표시됩니다. 소프트웨어 버전도 **10.0.10288.0** 으로 변경됩니다. 

    ![디바이스 업데이트 창에는 "디바이스가 최신 상태입니다."가 표시되고 소프트웨어 버전(10.0.10288.0)이 제공됩니다.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate17m.png)
