---
title: StorSimple 8000 시리즈 디바이스의 디스크 드라이브 교체 | Microsoft Docs
description: StorSimple 기본 인클로저 또는 EBOD 엔클로저의 디스크 드라이브를 교체하는 방법을 설명합니다.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 8/25/2017
ms.author: alkohli
ms.openlocfilehash: 3d6ef22e4df36996d68194589f43ea0f57def22c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576954"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>StorSimple 8000 시리즈 디바이스의 디스크 드라이브 교체

## <a name="overview"></a>개요
이 자습서에서는 Microsoft Azure StorSimple 디바이스에서 오작동하거나 오류가 발생한 하드 디스크 드라이브를 꺼내고 교체하는 방법을 설명합니다. 디스크 드라이브를 교체하려면 다음을 수행해야 합니다.

* 조작 방지 잠금 해제
* 디스크 드라이브 꺼내기
* 교체 디스크 드라이브 설치

> [!IMPORTANT]
> 디스크 드라이브를 꺼내고 교체하기 전에 [StorSimple 하드웨어 구성 요소 교체](storsimple-8000-hardware-component-replacement.md)에서 안전 정보를 검토하세요.
 

## <a name="disengage-the-antitamper-lock"></a>조작 방지 잠금 해제
이 절차에서는 디스크 드라이브를 교체할 때 StorSimple 디바이스의 조작 방지 잠금을 사용 또는 해제하는 방법을 설명합니다. 조작 방지 잠금은 드라이브 캐리어 핸들에 있으며 핸들의 래치 섹션에 있는 작은 입구를 통해 액세스합니다. 드라이브는 잠금이 잠긴 위치로 설정되어 제공됩니다.

#### <a name="to-unlock-the-antitamper-lock"></a>조작 방지 잠금을 해제하려면
1. 핸들의 입구와 해당 소켓에 잠금 키(Microsoft에서 제공한 "조작 방지" T10 드라이버)를 조심해서 삽입합니다. 
   
   조작 방지 잠금이 활성화되면 입구에 빨간색 표시기가 표시됩니다.
  
    ![잠긴 디스크 드라이브](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **그림 1** 조작 방지 잠금 사용
   
   | 레이블 | 설명 |
   |:--- |:--- |
   | 1 |표시기 입구 |
   | 2 |조작 방지 잠금 |
2. 키 위의 입구에 빨간색 표시기가 표시되지 않을 때까지 키를 시계 반대 방향으로 돌립니다.
3. 키를 꺼냅니다.
   
    ![ 잠금 해제된 디스크 드라이브](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **그림 2** 잠금 해제된 디스크 드라이브
4. 이제 디스크 드라이브를 꺼낼 수 있습니다.

잠금을 사용하려면 단계를 역순으로 따르세요.

## <a name="remove-the-disk-drive"></a>디스크 드라이브 꺼내기
StorSimple 디바이스는 RAID 10과 유사한 저장소 공간 구성을 지원합니다. 이는 오류가 발생한 하나의 디스크, SSD(반도체 드라이브) 또는 HDD(하드 디스크 드라이브)에서 정상적으로 작동할 수 있음을 의미합니다.

> [!IMPORTANT]
> * 시스템에 오류가 발생한 디스크가 둘 이상 있는 경우 항상 시스템에서 SSD 또는 HDD를 둘 이상 꺼내지 마세요. 이렇게 하면 데이터가 손실될 수 있습니다.
> * 이전에 SSD가 포함된 슬롯에는 교체 SSD를 넣어야 합니다. 마찬가지로, 이전에 HDD가 포함된 슬롯에는 교체 HDD를 넣습니다.
> * Azure Portal에서 슬롯에는 0 – 11 사이의 번호가 매겨져 있습니다. 따라서 포털에 슬롯 2의 디스크에서 오류가 발생했다고 표시되면 디바이스 왼쪽 위에서 세 번째 슬롯에서 오류가 발생한 디스크를 찾습니다.
> 
> 

시스템 작동하는 동안 드라이브를 꺼내고 교체할 수 있습니다.

#### <a name="to-remove-a-drive"></a>드라이브를 꺼내려면
1. 실패한 디스크를 식별하려면 Azure Portal에서 디바이스의 **설정 &gt; 하드웨어 상태**로 이동합니다. 기본 엔클로저 및/또는 EBOD 엔클로저에서 디스크 오류가 발생할 수 있으므로(8600 모델을 사용하는 경우) **공유 구성 요소** 및 **EBOD 공유 구성 요소** 아래에서 디스크 상태를 확인합니다. 엔클로저 중 하나에서 오류가 발생한 디스크는 빨간색 상태로 표시됩니다.
2. 기본 엔클로저 또는 EBOD 엔클로저 앞에 있는 드라이브를 찾습니다. 
3. 디스크가 잠금 해제된 경우 다음 단계로 진행합니다. 디스크가 잠긴 경우 [조작 방지 잠금 해제](#disengage-the-antitamper-lock)의 절차에 따라 잠금 해제합니다.
4. 드라이브 캐리어 모듈의 검은색 래치를 누르고 섀시 앞에서 드라이브 캐리어 핸들을 밖으로 잡아당깁니다.
   
    ![디스크 드라이브 핸들 해제](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **그림 3** 드라이브 핸들 해제
5. 드라이브 캐리어 핸들이 완전히 확장되면 드라이브 캐리어를 섀시 밖으로 밉니다. 
   
    ![디스크 드라이브 밖으로 디스크 밀기](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **그림 4** 캐리어 밖으로 디스크 드라이브 밀기

## <a name="install-the-replacement-disk-drive"></a>교체 디스크 드라이브 설치
StorSimple 디바이스에서 드라이브 오류가 발생하고 드라이브를 꺼낸 후에 새 드라이브로 교체하려면 이 절차를 따르세요.

#### <a name="to-insert-a-drive"></a>드라이브를 삽입하려면
1. 다음 그림과 같이 드라이브 캐리어 핸들이 완전히 확장되었는지 확인합니다.
   
    ![핸들이 확장된 디스크 드라이브](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **그림 5** 핸들이 확장된 드라이브
2. 드라이브 캐리어를 섀시에 밀어넣습니다.
   
    ![디스크 드라이브 캐리어 안으로 디스크 밀기](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **그림 6** 섀시에 드라이브 캐리어 밀어넣기
3. 드라이브 캐리어를 삽입한 후 드라이브 캐리어 핸들이 잠긴 위치에 올 때까지 드라이브 캐리어를 섀시 안으로 계속 누르면서 드라이브 캐리어 핸들을 닫습니다.
4. Microsoft에서 제공한 잠금 키(조작 방지 Torx 드라이버)로 잠금 나사를 시계 방향으로 1/4 돌려 캐리어 핸들을 제자리에 고정합니다.
5. 교체에 성공했고 드라이브가 작동하는지 확인합니다. Azure Portal에 액세스하고 **디바이스 설정** > **하드웨어 상태**로 이동합니다. **공유 구성 요소** 또는 **EBOD 공유 구성 요소** 아래에서 드라이브 상태가 정상임을 나타내는 녹색이어야 합니다.

   
   > [!NOTE]
   > 교체 후 디스크 상태가 녹색으로 바뀌는 데 몇 시간이 걸릴 수 있습니다.
  
## <a name="next-steps"></a>다음 단계
[StorSimple 하드웨어 구성 요소 교체](storsimple-8000-hardware-component-replacement.md)에 대해 자세히 알아봅니다.

