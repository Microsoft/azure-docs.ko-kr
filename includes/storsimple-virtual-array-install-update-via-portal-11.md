---
title: 파일 포함
description: 파일 포함
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 8ef8b9d3350d0599ab00dfdbb018cf8dd900e316
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95558915"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Azure Portal을 통해 업데이트를 설치하려면

1. StorSimple 디바이스 관리자를 이동하고 **디바이스** 를 선택합니다. 서비스에 연결된 디바이스 목록에서 업데이트하려는 디바이스를 선택하고 클릭합니다.

2. **설정** 에서 **디바이스 업데이트** 를 클릭합니다.  

3. 소프트웨어 업데이트를 사용할 수 있는 경우 메시지가 나타납니다. 업데이트를 확인하려면 **스캔** 을 클릭할 수도 있습니다. 실행 중인 소프트웨어 버전을 기록해 둡니다. 

    ![장치 업데이트 창에는 "새 업데이트를 사용할 수 있습니다." (강조 표시 됨) 및 "마지막으로 검사 한/6/22/2018 2:46 PM/Software version/10.0.10296.0"가 표시 됩니다. 버전이 강조 표시 됩니다.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    스캔이 시작되고 성공적으로 완료되면 알림이 표시됩니다.
 
4. 업데이트가 스캔되면 **업데이트 다운로드** 를 클릭합니다. **새 업데이트** 에서 릴리스 정보를 검토합니다. 또한 업데이트가 다운로드된 후에는 설치를 확인해야 합니다. **확인** 을 클릭합니다.

    ![장치 업데이트에서 업데이트 다운로드 단추가 강조 표시 됩니다. 업데이트를 다운로드 한 후에는 새 업데이트에 릴리스 정보에 대 한 링크와 설치를 확인 하는 메시지가 있습니다. 확인 단추가 있습니다.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    업로드가 시작되고 성공적으로 완료되면 알림이 표시됩니다.

5. **디바이스 업데이트** 에서 **설치** 를 클릭합니다.

     ![장치 업데이트에는 "업데이트 설치 확인"이 표시 됩니다. 설치 단추와 소프트웨어 버전이 강조 표시 됩니다.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. **새 업데이트** 아래에 업데이트가 중단되었다는 경고가 나타납니다. 가상 배열이 단일 노드 디바이스인 경우 디바이스가 업데이트된 후에 다시 시작됩니다. 따라서 진행 중인 모든 IO가 중단됩니다. **확인** 을 클릭하여 업데이트를 설치합니다.

    ![새 업데이트는 "이러한 업데이트가 설치 될 때 장치에 가동 중지 시간이 발생 합니다." 라고 표시 됩니다. 릴리스 정보에 대 한 링크와 확인 단추가 있습니다.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    설치 작업이 시작될 때 알림을 받습니다.

7.  설치 작업이 완료되면 **작업 보기** 링크를 클릭합니다. 이 작업을 수행하면 **업데이트 설치** 블레이드로 이동합니다. 이 작업에 대한 자세한 정보가 표시됩니다. 

    ![장치 업데이트에는 "업데이트 설치" 라는 레이블이 붙은 링크가 있습니다. 작업 보기 "를 참조 하십시오.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. 소프트웨어 버전 업데이트 1(10.0.10296.0)을 실행하는 가상 배열로 시작한 경우 이제 업데이트 1.1을 실행하여 완료됩니다. 나머지 단계를 건너뛸 수 있습니다. 

    소프트웨어 버전 업데이트 0.6(10.0.10293.0)을 실행하는 가상 배열로 시작한 경우 이제 업데이트 1.0으로 업데이트됩니다. 업데이트를 사용할 수 있음을 나타내는 다른 메시지가 표시됩니다. 4-7단계를 반복하여 업데이트 1.1을 설치합니다.

    

