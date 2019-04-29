---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 467af776af95cf035121250fdcadd2fee65d9805
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60724719"
---
#### <a name="to-create-a-volume-container"></a>볼륨 컨테이너를 만들려면
1. StorSimple 디바이스 관리자 서비스로 이동하고 **디바이스**를 클릭합니다. 테이블 형식 디바이스 목록에서 디바이스를 선택하여 클릭합니다. 

    ![볼륨 컨테이너 블레이드](./media/storsimple-8000-create-volume-container/createvolumecontainer1.png)

2. 디바이스 대시보드에서 **+ 볼륨 컨테이너 추가**를 클릭합니다.

    ![볼륨 컨테이너 블레이드](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

3. **볼륨 컨테이너 추가** 블레이드에서:
   
   1. 디바이스가 자동으로 선택됩니다.
   2. 볼륨 컨테이너의 **이름** 을 지정합니다. 이름은 3~32자 길이여야 합니다. 볼륨 컨테이너를 만든 후에는 이름을 바꿀 수 없습니다.
   3. **클라우드 저장소 암호화 사용** 을 선택하여 디바이스에서 클라우드로 보낸 데이터의 암호화를 사용합니다.
   4. 8~32자 길이의 **클라우드 저장소 암호화 키** 를 제공 및 확인합니다. 이 키는 암호화된 데이터에 액세스하는 디바이스가 사용합니다.
   5. **Storage 계정** 을 선택하여 이 볼륨 컨테이너와 연결합니다. 서비스를 만들 때 생성되는 기본 계정 또는 기존 저장소 계정을 선택할 수 있습니다. **새로 추가** 옵션을 사용하여 이 서비스 구독에 연결되지 않은 저장소 계정을 지정할 수도 있습니다.
   6. 사용 가능한 모든 대역폭을 사용하려는 경우 **대역폭 지정** 드롭다운 목록에서 **제한 없음**을 선택합니다. 이 옵션을 **사용자 지정** 으로 설정하여 대역폭 제어를 사용하고 1Mbps~1,000Mbps 사이의 값을 지정할 수도 있습니다.
      사용 가능한 대역폭 사용 정보가 있는 경우 **대역폭 템플릿 선택**을 지정하여 일정에 따라 대역폭을 할당할 수 있습니다. 단계별 절차의 경우 [대역폭 템플릿 추가](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template)로 이동합니다.

      ![볼륨 컨테이너 블레이드](./media/storsimple-8000-create-volume-container/createvolumecontainer6b.png)
   7. **만들기**를 클릭합니다.

        ![볼륨 컨테이너 블레이드](./media/storsimple-8000-create-volume-container/createvolumecontainer6.png)
   
       볼륨 컨테이너가 성공적으로 만들어지면 알림이 표시됩니다.

       ![볼륨 컨테이너 만들기 알림](./media/storsimple-8000-create-volume-container/createvolumecontainer8.png)

   새로 만들어진 볼륨 컨테이너가 디바이스에 대한 볼륨 컨테이너 목록에 나열됩니다.

   ![볼륨 컨테이너 추가 블레이드](./media/storsimple-8000-create-volume-container/createvolumecontainer9.png)


