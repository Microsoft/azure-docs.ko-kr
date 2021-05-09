---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9f1c30b303bd1fe02e0685c7d848be92073ca2f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016875"
---
#### <a name="to-add-a-storage-account-credential-in-the-same-azure-subscription-as-the-storsimple-device-manager-service"></a>StorSimple Device Manager 서비스와 동일한 Azure 구독에 있는 스토리지 계정 자격 증명을 추가하려면

1. StorSimple 디바이스 관리자 서비스로 이동합니다. **구성** 섹션에서 **Storage 계정 자격 증명** 을 클릭합니다.

    ![스토리지 계정 자격 증명으로 이동](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct1.png)

2. **Storage 계정 자격 증명** 블레이드에서 **+ 추가** 를 클릭합니다.

    ![스토리지 계정 자격 증명 추가](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct2.png)

3. **스토리지 계정 자격 증명 추가** 블레이드에서 다음 단계를 수행합니다.

    1. 서비스와 동일한 Azure 구독에서 스토리지 계정 자격 증명을 추가하면 **현재** 가 선택되는지 확인합니다.

    2. **스토리지 계정** 드롭다운 목록에서 기존 스토리지 계정을 선택합니다.

    3. 선택한 스토리지 계정에 따라 **위치** 가 표시됩니다(회색으로 표시되며 여기서 변경할 수 없음).

    4. **SSL 모드 사용** 을 선택하여 디바이스와 클라우드 간의 네트워크 통신을 위한 보안 채널을 만듭니다. 프라이빗 클라우드 내에서 작동하는 경우에만 **SSL 사용** 을 비활성화합니다.

        ![스토리지 계정 자격 증명 추가 블레이드](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct3.png)

    5. **추가** 를 클릭하여 스토리지 계정 자격 증명에 대해 작업 만들기를 시작합니다. 스토리지 계정 자격 증명이 성공적으로 만들어지면 알림이 표시됩니다.

        ![스토리지 계정 자격 증명에 대한 성공 알림](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct5.png)

새로 만든 Storage 계정 자격 증명이 **Storage 계정 자격 증명** 목록 아래에 표시됩니다.

![스토리지 계정 자격 증명 나열](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct6.png)

