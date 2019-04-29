---
title: StorSimple 8000 시리즈 디바이스의 PCM 교체 | Microsoft Docs
description: StorSimple 디바이스의 PCM(전원 및 냉각 모듈)을 꺼내고 교체하는 방법을 설명합니다.
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
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 42561570e24aec5edd33248ef1738e53175e480e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632460"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>StorSimple 디바이스의 전원 및 냉각 모듈 교체
## <a name="overview"></a>개요
Microsoft Azure StorSimple 디바이스의 PCM(전원 및 냉각 모듈)은 기본 및 EBOD 엔클로저를 통해 제어되는 전원 공급 디바이스 및 냉각 팬으로 구성되어 있습니다. 각 엔클로저마다 인증된 PCM 모델 하나만 있습니다. 기본 엔클로저는 764W PCM에 대해 인증되고 EBOD 엔클로저는 580W PCM에 대해 인증됩니다. 기본 엔클로저와 EBOD 엔클로저의 PCM은 서로 다르지만 교체 절차는 동일합니다.

이 자습서에서는 다음을 수행하는 방법을 설명합니다.

* PCM 꺼내기
* 교체 PCM 설치

> [!IMPORTANT]
> PCM을 꺼내고 교체하기 전에 [StorSimple 하드웨어 구성 요소 교체](storsimple-8000-hardware-component-replacement.md)에서 안전 정보를 검토하세요.


## <a name="before-you-replace-a-pcm"></a>PCM을 교체하기 전에
PCM을 교체하기 전에 다음과 같은 중요한 문제에 주의하세요.

* PCM의 전원 공급 장치에서 오류가 발생할 경우 결함이 있는 모듈을 설치된 상태로 두고 전원 코드를 뺍니다. 팬이 엔클로저에서 계속 전원을 받고 적절한 냉각을 계속 공급합니다. 팬에서 오류가 발생할 경우 PCM을 즉시 교체해야 합니다.
* PCM을 꺼내기 전에 주 스위치(있는 경우)를 끄거나 전원 코드를 물리적으로 분리하여 PCM에서 전원을 끊습니다. 전원이 곧 종료된다는 경고가 시스템에 제공됩니다.
* 결함이 있는 PCM을 교체하기 전에 지속적인 시스템 작동을 위해 다른 PCM이 작동하는지 확인합니다. 가능한 한 빨리 결함이 있는 PCM을 완벽하게 작동하는 PCM으로 교체해야 합니다.
* PCM 모듈 교체를 완료하는 데 몇 분밖에 걸리지 않지만 과열을 방지하기 위해 오류가 발생한 PCM을 꺼내고 10분 내에 완료해야 합니다.
* 공장에서 출고된 교체 764 W PCM 모듈에는 백업 배터리 모듈이 없습니다. 교체하기 전에 결함이 있는 PCM에서 배터리를 제거하고 교체 모듈에 이를 삽입해야 합니다. 자세한 내용은 [백업 배터리 모듈 제거 및 삽입](storsimple-8000-battery-replacement.md)방법을 참조하세요.

## <a name="remove-a-pcm"></a>PCM 꺼내기
Microsoft Azure StorSimple 디바이스에서 PCM(전원 및 냉각 모듈)을 꺼낼 준비가 되면 다음 지침을 따르세요.

> [!NOTE]
> PCM을 꺼내기 전에 올바른 교체(기본 엔클로저의 경우 764W, EBOD 엔클로저의 경우 580W)가 있는지 확인합니다.

#### <a name="to-remove-a-pcm"></a>PCM을 꺼내려면
1. Azure 클래식 포털에서 **설정 > 모니터 > 하드웨어 상태**를 클릭합니다. **공유 구성 요소** 아래에서 PCM 구성 요소의 상태를 확인하여 오류가 발생한 PCM을 식별합니다.
   
   * PCM 0의 전원 공급 장치에서 오류가 발생한 경우 **PCM 0의 전원 공급 장치** 상태가 빨강으로 표시됩니다.
   * PCM 1의 전원 공급 장치에서 오류가 발생한 경우 **PCM 1의 전원 공급 장치** 상태가 빨강으로 표시됩니다.
   * PCM 1의 팬에서 오류가 발생한 경우 **PCM 0의 냉각 0** 또는 **PCM 0의 냉각 1** 상태가 빨강으로 표시됩니다.
2. 기본 엔클로저 뒷면에서 오류가 발생한 PCM을 찾습니다. 8600 모델을 실행하는 경우 전면 패널 LED 디스플레이에 표시된 시스템 장치 식별 번호를 확인하여 기본 엔클로저를 식별합니다. 기본 엔클로저에 표시되는 기본 장치 ID는 **00**인 반면, EBOD 엔클로저에 표시되는 기본 장치 ID는 **01**입니다. 다음 다이어그램과 표에서는 LED 디스플레이의 전면 패널에 대해 설명합니다.
   
    ![전면 OPS 패널의 시스템 ID](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **그림 1** 디바이스의 전면 패널  
   
   | 레이블 | 설명 |
   |:--- |:--- |
   | 1 |음소거 단추 |
   | 2 |시스템 전원 |
   | 3 |모듈 결함 |
   | 4 |논리적 결함 |
   | 5 |장치 ID 디스플레이 |
3. 기본 엔클로저 뒷면의 모니터링 표시기 LED를 사용하여 결함이 있는 PCM을 식별할 수도 있습니다. LED를 사용하여 결함이 있는 PCM을 찾는 방법을 이해하려면 다음 다이어그램과 표를 참조하세요. 예를 들어 **팬 오류** 에 해당하는 LED가 켜지면 팬에서 오류가 발생한 것입니다. 마찬가지로, **AC 오류** 에 해당하는 LED가 켜지면 전원 공급 장치에서 오류가 발생한 것입니다. 
   
    ![디바이스 PCM 모니터링 표시기 LED의 백플레인](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **그림 2** 표시기 LED가 있는 PCM 뒷면
   
   | 레이블 | 설명 |
   |:--- |:--- |
   | 1 |AC 전원 오류 |
   | 2 |팬 오류 |
   | 3 |배터리 결함 |
   | 4 |PCM 정상 |
   | 5 |DC 전원 오류 |
   | 6 |배터리 정상 |
4. StorSimple 디바이스 뒷면의 다음 다이어그램을 참조하여 오류가 발생한 PCM 모듈을 찾습니다. PCM 0은 왼쪽에 있고 PCM 1은 오른쪽에 있습니다. 뒤에 나오는 표에서는 모듈에 대해 설명합니다.
   
     ![디바이스 기본 엔클로저 모듈의 백플레인](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **그림 3** 플러그 인 모듈이 있는 디바이스 뒷면 
   
   | 레이블 | 설명 |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |컨트롤러 0 |
   | 4 |컨트롤러 1 |
5. 결함이 있는 PCM을 끄고 전원 공급 장치 코드를 뺍니다. 이제 PCM을 꺼낼 수 있습니다.
6. 엄지와 집게 손가락으로 PCM 핸들의 래치와 측면을 잡고 눌러서 핸들을 엽니다.
   
    ![PCM 핸들 열기](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **그림 4** PCM 핸들 열기
7. 핸들을 잡고 PCM을 꺼냅니다.
   
    ![디바이스 PCM 꺼내기](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **그림 5** PCM 꺼내기

## <a name="install-a-replacement-pcm"></a>교체 PCM 설치
StorSimple 디바이스에 PCM을 설치하려면 다음 지침을 따르세요. 교체 PCM을 설치하기 전에 백업 배터리 모듈을 삽입했는지 확인합니다(764 W PCM에만 적용). 자세한 내용은 [백업 배터리 모듈 제거 및 삽입](storsimple-8000-battery-replacement.md)방법을 참조하세요.

#### <a name="to-install-a-pcm"></a>PCM을 설치하려면
1. 이 엔클로저에 적합한 교체 PCM이 있는지 확인합니다. 기본 엔클로저에는 764W PCM이 필요하고 EBOD 엔클로저에는 580W PCM이 필요합니다. 기본 엔클로저에 580W PCM을 사용하거나 EBOD 엔클로저에 764W PCM을 사용해서는 안 됩니다. 다음 그림은 PCM에 부착된 레이블에서 이 정보를 식별할 수 있는 위치를 보여 줍니다.
   
    ![디바이스 PCM 레이블](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **그림 6** PCM 레이블
2. 커넥터에 특히 주의해서 엔클로저에 손상된 부분이 있는지 확인합니다. 
   
   > [!NOTE]
   > **커넥터 핀이 구부러진 경우 모듈을 설치하지 마세요.**
   > 
   > 
3. PCM 핸들을 열린 위치에 놓고 모듈을 엔클로저에 밀어넣습니다.
   
    ![디바이스 PCM 설치](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **그림 7** PCM 설치
4. 수동으로 PCM 핸들을 닫습니다. 핸들 래치가 걸리면 딸깍하는 소리가 들립니다.
   
   > [!NOTE]
   > 커넥터 핀이 걸리도록 래치를 해제하지 않고 핸들을 부드럽게 잡아당길 수 있습니다. PCM이 밖으로 밀리면 커넥터가 걸리기 전에 래치가 닫힌 것입니다.
   
5. 전원과 PCM에 전원 케이블을 연결합니다.
6. 변형 방지 묶음을 고정합니다.
7. PCM을 켭니다.
8. 교체에 성공했는지 확인합니다. StorSimple 디바이스 관리자 서비스의 Azure Portal에서 디바이스로 이동한 후 **설정 &gt; 모니터 &gt; 하드웨어 상태**로 이동합니다. **공유 구성 요소** 아래에서 PCM 상태가 녹색이어야 합니다.
   
   > [!NOTE]
   > 교체 PCM이 완전히 초기화되는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
[StorSimple 하드웨어 구성 요소 교체](storsimple-8000-hardware-component-replacement.md)에 대해 자세히 알아봅니다.

