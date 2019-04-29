---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: db2104020e9478b1fedf68e1c9467f75e16044e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60482740"
---
#### <a name="to-cable-for-power"></a>전원을 케이블로 연결하려면
1. 각 PCM(전원 및 냉각 모듈)의 전원 스위치가 OFF 위치에 있는지 확인합니다.
2. 기본 엔클로저에서 각 PCM에 전원 코드를 연결합니다.
3. 다음 그림과 같이 전원 코드를 랙 PDU(전원 분배 장치)에 연결합니다. 두 개의 PCM이 개별 전원을 사용하는지 확인합니다.
   
   > [!IMPORTANT]
   > 시스템의 고가용성을 보장하려면 다음 다이어그램에 표시된 것처럼 전원 케이블 연결 방식을 엄격히 준수하는 것이 좋습니다. 
   > 
   > 
   
    ![전원에 2U 디바이스를 케이블로 연결](./media/storsimple-cable-8100-for-power/HCSCableYour2UDeviceforPower.png)
   
    **8100 디바이스에서 전원 케이블 연결**
   
   | 레이블 | 설명 |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |컨트롤러 1 |
   | 3 |컨트롤러 0 |
   | 4 |PCM 1 |
   | 5 |PDU |
4. 시스템의 전원을 켜려면 양쪽 PCM의 전원 스위치를 ON 위치로 누릅니다.

