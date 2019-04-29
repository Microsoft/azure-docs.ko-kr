---
title: StorSimple 디바이스에 대한 안정성 | Microsoft Docs
description: 안전성 규칙, 지침 및 고려 사항을 설명하고 StorSimple 디바이스를 안전하게 설치하고 작동하는 방법을 설명합니다.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: 66b881ab13e27ee457af4fa1bafb82ad14e9674d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631682"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>StorSimple 디바이스의 안전한 설치 및 작동
![경고 아이콘](./media/storsimple-safety/IC740879.png)
![안전성 고지 읽기 아이콘](./media/storsimple-safety/IC740885.png) **안전성 및 상태 정보 읽기**

이 문서에서 Microsoft Azure StorSimple 디바이스에 적용되는 모든 안전성 및 상태 정보를 읽으십시오 나중에 참조할 수 있도록 StorSimple 디바이스와 함께 제공되는 모든 인쇄된 가이드를 보관하십시오. 지침을 따르지 않고 이 제품을 적절하게 설정, 사용 및 관리하지 않으면 심각한 부상 또는 사망 위험이 증가하거나 디바이스가 손상될 수 있습니다. [이 가이드의 다운로드할 수 있는 버전](https://www.microsoft.com/download/details.aspx?id=44233)도 제공됩니다.

## <a name="safety-icon-conventions"></a>안전성 아이콘 표시 규칙
다음은 Microsoft Azure StorSimple 디바이스를 설정 및 실행할 때 관찰되는 안전 주의 사항을 검토할 때 표시되는 아이콘입니다.

| 아이콘 | 설명 |
|:--- |:--- |
| ![위험 아이콘](./media/storsimple-safety/IC740879.png) **위험!** |피하지 않을 경우 사망 또는 심각한 부상을 당하는 위험한 상황을 나타냅니다. 이 위험도 표시는 가장 극단적인 상황으로 제한됩니다. |
| ![경고 아이콘](./media/storsimple-safety/IC740879.png) **경고!** |피하지 않을 경우 사망 또는 심각한 부상을 당할 수 있는 위험한 상황을 나타냅니다. |
| ![경고 아이콘](./media/storsimple-safety/IC740879.png) **주의!** |피하지 않을 경우 최소 또는 보통 수준의 부상을 당할 수 있는 위험한 상황을 나타냅니다. |
| ![참고 아이콘](./media/storsimple-safety/IC740881.png) **고지:** |중요하지만 위험과 관련되지 않은 것으로 간주되는 정보를 나타냅니다. |
| ![감전 아이콘](./media/storsimple-safety/IC740882.png) **감전 위험** |높은 전압 |
| ![무거운 무게 아이콘](./media/storsimple-safety/IC740883.png) **무거운 무게** | |
| ![사용자 서비스 가능 부품 없음 아이콘](./media/storsimple-safety/IC740879.png) **사용자 서비스 가능 부품 없음** |제대로 교육을 받지 않은 경우 액세스하지 마세요. |
| ![안전성 고지 읽기 아이콘](./media/storsimple-safety/IC740885.png)**먼저 모든 지침 읽기** | |
| ![기울어짐 위험 아이콘](./media/storsimple-safety/IC740886.png) **기울어짐 위험** | |

## <a name="handling-precautions"></a>취급 주의 사항
![경고 아이콘](./media/storsimple-safety/IC740879.png) ![무거운 무게 아이콘](./media/storsimple-safety/IC740883.png) **경고!** 

부상 위험을 줄이려면:

* 완전히 구성된 인클로저의 최고 무게는 32kg(70lbs)입니다. 혼자 들지 마십시오.
* 인클로저를 이동하기 전에 항상 두 사람이 무게를 지탱하도록 해야 합니다. 한 사람이 이 무게를 들어 올릴 경우 부상을 당할 수 있습니다.
* 장치의 뒷면에 있는 전원 및 냉각 모듈(PCM)의 핸들로 인클로저를 들지 마십시오. 무게를 지탱하도록 설계되지 않았습니다.

## <a name="connection-precautions"></a>연결 주의 사항
![Warning Icon](./media/storsimple-safety/IC740879.png) ![Electrical Shock Icon](./media/storsimple-safety/IC740882.png) **경고!**

부상, 감전 또는 사망 가능성을 줄이려면:

* 여러 AC 전원에서 전기를 공급받는 경우 완전한 격리를 위해 모든 전원의 연결을 해제하십시오.
* 이동하기 전이나 어떤 방식으로든 손상될 것이라고 판단되는 장치 플러그를 영구적으로 뽑으십시오.
* 전원 공급 코드에 안전한 전기 접지 연결을 제공하십시오. 전원을 공급하기 전에 인클로저의 접지가 국가 및 지역 요구 사항을 충족하는지 확인하십시오.
* 인클로저에서 PCM을 제거하기 전에 항상 전원 연결을 해제해야 합니다.
* 전원 공급 디바이스 코드의 플러그가 기본 차단 디바이스라면, 소켓 콘센트가 장비 근처에 있고 쉽게 액세스할 수 있는지 확인하십시오.

![Warning Icon](./media/storsimple-safety/IC740879.png) ![Electrical Shock Icon](./media/storsimple-safety/IC740882.png) **경고!**

전기 연결에서 과열 또는 화재 가능성을 줄이려면:

* 기술 사양에 상세히 기술된 요구 사항을 충족하기 위해 전기 과부하 보호 기능과 함께 적합한 전원을 제공하십시오.
* 두 갈래의 전원 코드("Y" 리드)를 사용하지 마십시오.
* 해당 안전, 배기 가스 및 열 요구 사항을 준수하기 위해 커버를 제거하지 말고 모든 베이를 플러그인 모듈 또는 드라이브 블랭크로 채워야 합니다.
* 장비가 제조업체에서 지정한 방식으로 사용되어야 합니다. 제조업체에서 지정하지 않은 방식으로 이 장비를 사용할 경우 장비에서 제공하는 보호 기능이 작동하지 않을 수 있습니다.

![참고 아이콘](./media/storsimple-safety/IC740881.png) **고지:**

장비의 적절한 작동 및 제품 손상을 막으려면:

* 디바이스의 뒷면에 있는 RJ45 포트는 이더넷 연결 전용입니다. 원격 통신 네트워크에 연결해서는 안 됩니다.
* 앞-뒤 냉각 설계를 수용할 수 있는 랙에 디바이스를 설치해야 합니다.
* 모든 플러그인 모듈 및 블랭크 판은 시스템 인클로저의 일부입니다. 교체품을 즉시 추가할 수 있을 때만 제거해야 합니다. 시스템은 모든 모듈 또는 블랭크가 장착된 상태에서만 실행해야 합니다.

## <a name="rack-system-precautions"></a>랙 시스템 주의 사항
랙 캐비닛에 디바이스를 탑재할 때는 다음 안전 요구 사항을 고려해야 합니다.

![경고 아이콘](./media/storsimple-safety/IC740879.png) ![기울어짐 위험 아이콘](./media/storsimple-safety/IC740886.png) **경고!**

전복되어 발생하는 부상 가능성을 줄이려면:

* 랙 설계는 설치된 인클로저의 총 무게를 지탱해야 하며 설치 또는 정상적인 사용 중에 랙이 기울어 넘어지거나 밀어 넘어지지 않도록 안정화 기능을 포함해야 합니다.
* 랙을 로드할 때는 아래에서 위로 랙을 채우고 위에서 아래로 비우십시오.
* 두 개 이상의 인클로저를 랙 바깥쪽으로 동시에 밀지 마십시오. 랙이 넘어질 위험이 있습니다.

![Warning Icon](./media/storsimple-safety/IC740879.png) ![Electrical Shock Icon](./media/storsimple-safety/IC740882.png) **경고!**

부상, 감전 또는 사망 가능성을 줄이려면:

* 랙은 안전한 배전 시스템을 포함해야 합니다. 인클로저에 과전류 보호 기능을 제공해야 하며 설치된 인클로저 총 수에 의해 과부하되어서는 안 됩니다. 명판에 표시된 정격 소비 전력을 확인해야 합니다.
* 배전 시스템에서는 랙에 있는 각 인클로저에 신뢰할 수 있는 접지를 제공해야 합니다.
* 배전 시스템의 설계에서는 모든 인클로저의 모든 전원 공급 장치로부터 나오는 전체 접지 누설 전류를 고려해야 합니다. 각 인클로저의 각 전원 공급 장치에는 60Hz, 264볼트에서 최대 1.0mA의 접지 누설 전류를 포함합니다. 랙에 “누설 전류 높음. 전기를 연결하기 전에 접지(어스) 연결이 필요합니다."라는 레이블이 필요할 수 있습니다.
* 인클로저와 함께 구성된 랙은 UL 60950-1 및 IEC 60950-1/EN 60950-1의 안전 요구 사항을 충족해야 합니다.

![참고 아이콘](./media/storsimple-safety/IC740881.png) **고지:**

랙 시스템의 적절한 냉각을 위해:

* 랙 설계 시 섭씨 35도(화씨 95도)의 인클로저 작동을 위한 최대 주변 온도를 고려해야 합니다.
* 시스템은 저압의 후면 배기 설치에서 작동합니다(랙 도어에서 발생하는 배압 및 장애물이 5파스칼[0.5mm 수면계]을 초과하지 않음).

## <a name="power-cooling-module-pcm-precautions"></a>PCM(전원 냉각 모듈) 주의 사항
디바이스는 두 개의 PCM과 함께 작동하도록 설계되었습니다. 각 PCM에는 전원 공급 장치와 이중 축 팬이 있습니다. 임계 조건에서 시스템은 한 대의 전원 공급 장치에 오류가 발생해도 정상적인 작동을 계속할 수 있습니다. 항상 두 PCM(및 전원 공급 장치)을 설치해야 합니다. PCM 하나로는 예비 전력을 제공하지 않습니다. 따라서 하나의 PCM만 고장나도 가동 중지 시간 또는 데이터 손실이 발생할 수 있습니다.

![경고 아이콘](./media/storsimple-safety/IC740879.png) ![감전 아이콘](./media/storsimple-safety/IC740882.png) **경고!**

부상, 감전 또는 사망 가능성을 줄이려면:

* PCM에서 커버를 제거하지 마십시오. 내부 감전 위험이 있습니다. PCM을 반환하고 교체하려면 [Microsoft 지원에 문의](storsimple-contact-microsoft-support.md)하세요.

![참고 아이콘](./media/storsimple-safety/IC740881.png) **고지:**

장비의 적절한 작동 및 제품 손상을 막으려면:

* 실패한 PCM은 24시간 이내 교체해야 합니다. 교체할 PCM을 제거한 후 제거 시점부터 10분이내 교체를 완료해야 합니다.
* 교체품을 즉시 설치할 수 없는 경우 PCM을 제거하지 마십시오. 모든 모듈이 장착된 상태에서만 인클로저를 작동해야 합니다.

## <a name="electrostatic-discharge-esd-precautions"></a>정전기 방전(ESD) 주의 사항
![참고 아이콘](./media/storsimple-safety/IC740881.png) **고지:**

다음 ESD 관련 주의 상태를 확인하세요.

* 적절한 방전 손목 또는 발목 스트랩을 착용했는지 확인하십시오.
* 모듈 및 구성 요소를 취급할 때는 모든 기본적인 ESD 주의 사항을 확인하십시오.
* 백플레인 구성 요소 및 모듈 커넥터에 닿지 않도록 하십시오.
* ESD 손상 시 보증이 적용되지 않습니다.

## <a name="battery-disposal-precautions"></a>배터리 폐기 주의 사항
전원 공급 장치는 일시적인 단기 정전 중에 메모리 내용을 보호하는 특수 배터리를 사용합니다. 이 배터리는 PCM에 장착되어 있습니다. 배터리에 대한 다음 정보를 숙지하십시오.

![경고 아이콘](./media/storsimple-safety/IC740879.png) **경고!**

누전, 화재, 폭발, 부상 또는 사망 위험을 줄이려면:

* 국가/지역 규정에 따라 사용한 배터리를 폐기하십시오.
* 분해하거나 충격을 가하거나 섭씨 60도(화씨 140도)가 넘게 가열하거나 소각하지 마십시오. PCM 배터리는 제공된 배터리로만 교체하십시오. 다른 배터리를 사용할 경우 화재 또는 폭발 위험이 있습니다.
* 전원 공급 장치에서 제거하는 경우 배터리에 보호용 끝 캡을 사용하십시오.

![참고 아이콘](./media/storsimple-safety/IC740881.png) **고지:**

배터리를 선적하거나 항공편으로 운송하는 경우 [https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)의 IATA 리튬 배터리 지침 문서를 따르세요.

이 보안 공지를 검토한 후 다음 단계에서는 디바이스를 개봉하고 랙 및 케이블을 연결합니다.

## <a name="next-steps"></a>다음 단계
* 8100 디바이스의 경우 [StorSimple 8100 디바이스 설치](storsimple-8100-hardware-installation.md)로 이동합니다.
* 8600 디바이스의 경우 [StorSimple 8600 디바이스 설치](storsimple-8600-hardware-installation.md)로 이동합니다.

