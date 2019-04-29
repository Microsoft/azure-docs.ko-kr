---
title: StorSimple 모니터링 표시기 | Microsoft Docs
description: StorSimple 디바이스의 상태를 모니터링하는 데 사용되는 LED(발광 다이오드) 및 청각적 경보에 대해 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: ef8acf1c3c9211168ebacc8d62647f6789c745a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630612"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>StorSimple 모니터링 표시기를 사용하여 디바이스 관리


## <a name="overview"></a>개요
StorSimple 디바이스에는 모듈 및 StorSimple 디바이스의 모듈 및 전반적인 상태를 모니터링하는 데 사용할 수 있는 경보 및 LED(발광 다이오드)가 포함됩니다. 모니터링 표시기는 디바이스의 기본 인클로저 및 EBOD 인클로저의 하드웨어 구성 요소에서 찾을 수 있습니다. 모니터링 표시기는 LED 또는 청각적 경보가 될 수 있습니다.

모듈의 상태를 나타내는 데 사용되는 세 가지 LED 상태는 녹색, 깜박이는 녹색에서 빨간색-주황색으로 변함, 빨간색-주황색입니다.  

* 녹색 LED는 정상 작동 상태를 나타냅니다.  
* 깜박이는 녹색에서 빨간색-주황색으로 변하는 LED는 사용자 간섭이 필요할 수 있는 중요하지 않은 조건의 현재 상태를 나타냅니다.  
* 빨간색-주황색 LED는 모듈 내에 존재하는 중요한 오류임을 나타냅니다.  

이 문서의 나머지 부분에서는 다양한 모니터링 표시기 LED, StorSimple 디바이스에서의 해당 위치, LED 상태에 기반한 디바이스 상태 및 관련된 모든 청각적 경보를 설명합니다.

## <a name="front-panel-indicator-leds"></a>전면 패널 표시기 LED
*작업 패널* 또는 *ops 패널*이라고도 하는 전면 패널은 시스템에서 모든 모듈의 집계 상태를 표시합니다. 전면 패널은 StorSimple 기본 및 EBOD 인클로저에서 동일하며 아래에서 설명됩니다.  

   ![디바이스 전면 패널][1]

전면 패널은 다음과 같은 표시기를 포함합니다.  

1. 음소거 단추
2. 전원 표시기 LED(녹색/빨간색-주황색)
3. 모듈 결함 표시기 LED(켜기 빨간색-주황색/끄기)
4. 논리 결함 표시기 LED(켜기 빨간색-주황색/끄기)
5. 장치 ID 디스플레이  

디바이스 및 EBOD 인클로저의 전면 패널 LED 사이의 주요한 차이점은 LED 디스플레이에서 표시되는 **시스템 디바이스 ID 번호** 입니다. 디바이스에 표시되는 기본 디바이스 ID는 **00**이며, EBOD 엔클로저에 표시되는 기본 디바이스 ID는 **01**입니다. 이를 통해 디바이스가 켜졌을 때 디바이스와 EBOD 인클로저 사이를 빠르게 구분할 수 있습니다. 디바이스가 꺼져 있는 경우 [새 디바이스 켜기](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) 에 제공된 정보를 사용하여 EBOD 엔클로저에서 디바이스를 구분할 수 있습니다.  

## <a name="front-panel-led-status"></a>전면 패널 LED 상태
다음 표를 통해 디바이스 또는 EBOD 인클로저의 전면 패널 LED에 나타나는 상태를 식별할 수 있습니다.  

| 시스템 전원 | 모듈 결함 | 논리적 결함 | 경보 | 상태 |
| --- | --- | --- | --- | --- |
| 빨간색-주황색 |끄기 |끄기 |N/A |AC 전원 손실, 백업 전원으로 작동 또는 AC 전원 켜기 및 컨트롤러 모듈이 제거되었습니다. |
| 녹색 |켜기 |켜기 |N/A |ops 패널 전원 켜기(5초) 상태 테스트 |
| 녹색 |끄기 |끄기 |N/A |전원이 켜짐, 모든 기능이 정상 상태임 |
| 녹색 |켜기 |N/A |PCM 결함 LED, 팬 결함 LED |모든 PCM 결함, 팬 결함, 온도 초과 또는 미만 |
| 녹색 |켜기 |N/A |I/O 모듈 LED |모든 컨트롤러 모듈 결함 |
| 녹색 |켜기 |N/A |N/A |인클로저 논리적 결함 |
| 녹색 |깜박임 |N/A |컨트롤러 모듈의 모듈 상태 LED. PCM 결함 LED, 팬 결함 LED |알 수 없는 컨트롤러 모듈 유형이 설치됨, I2C 버스 오류, 컨트롤러 모듈 VPD(Vital Product Data) 구성 오류 |

## <a name="power-cooling-module-pcm-indicator-leds"></a>PCM(전원 냉각 모듈) 표시기 LED
PCM(전원 냉각 모듈) 표시기 LED는 각 PCM 모듈의 기본 인클로저 또는 EBOD 인클로저의 뒷면에서 찾을 수 있습니다. 이 항목에서는 StorSimple 디바이스의 상태를 모니터링하기 위해 다음 LED를 사용하는 방법에 대해 설명합니다.  

* 기본 인클로저의 PCM LED
* EBOD 인클로저의 PCM LED

## <a name="pcm-leds-for-the-primary-enclosure"></a>기본 인클로저의 PCM LED
StorSimple 디바이스에는 764W PCM 모듈과 추가 배터리가 있습니다. 다음 그림에서는 디바이스의 LED 패널을 보여줍니다.  

   ![기본 엔클로저의 PCM LED][2]

LED 범례

1. AC 전원 오류
2. 팬 오류
3. 배터리 결함
4. PCM 정상
5. DC 오류
6. 배터리 정상  

PCM 상태는 LED 패널에 표시됩니다. 디바이스 PCM LED 패널에는 6개의 LED가 있습니다. 이러한 LED 중 4개는 전원 공급 장치 및 팬의 상태를 표시합니다. 나머지 두 LED는 PCM의 백업 배터리 모듈 상태를 나타냅니다. 다음 테이블에서 PCM 상태를 확인할 수 있습니다.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>전원 공급 장치 및 팬의 PCM 표시기 LED
| 상태 | PCM 정상(녹색) | AC 오류(주황색) | 팬 오류(주황색) | DC 오류(주황색) |
| --- | --- | --- | --- | --- |
| AC 전원 없음(인클로저) |끄기 |OFF |OFF |끄기 |
| AC 전원 없음(이 PCM만 해당) |끄기 |켜기 |끄기 |켜기 |
| AC 있음 PCM 켜짐-정상 |켜기 |OFF |OFF |끄기 |
| PCM 오류(팬 오류) |끄기 |끄기 |켜기 |N/A |
| PCM 결함(과도 진폭, 과전압, 과전류) |끄기 |켜기 |ON |켜기 |
| PCM(팬이 허용 오차를 벗어남) |켜기 |OFF |끄기 |켜기 |
| 대기 모드 |깜박임 |끄기 |OFF |끄기 |
| PCM 펌웨어 다운로드 |끄기 |깜박임 |깜박임 |깜박임 |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>백업 배터리의 PCM 표시기 LED
| 상태 | 배터리 정상(녹색) | 배터리 결함(주황색) |
| --- | --- | --- |
| 배터리 없음 |끄기 |끄기 |
| 배터리가 있고 충전됨 |켜기 |끄기 |
| 배터리 충전 중 또는 유지 관리를 위한 방전 |깜박임 |끄기 |
| 배터리 "소프트" 결함(복구 가능) |끄기 |깜박임 |
| 배터리 "하드" 결함(복구 불가능) |끄기 |켜기 |
| 배터리 해제됨 |깜박임 |끄기 |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>EBOD 인클로저의 PCM LED
EBOD 인클로저에 580W PCM이 있으며 추가 배터리는 없습니다. EBOD 인클로저의 PCM 패널에 전원 공급 장치 및 팬을 위한 표시기 LED만 있습니다. 다음 그림에서는 이러한 LED를 보여줍니다.

   ![EBOD 엔클로저의 PCM LED][3] 

다음 표는 PCM 상태를 확인하는 데 사용할 수 있습니다.  

| 상태 | PCM 정상(녹색) | AC 오류(주황색) | 팬 오류(주황색) | DC 오류(주황색) |
| --- | --- | --- | --- | --- |
| AC 전원 없음(인클로저) |끄기 |OFF |OFF |끄기 |
| AC 전원 없음(이 PCM만 해당) |끄기 |켜기 |끄기 |켜기 |
| AC 있음 PCM 켜짐 - 정상 |켜기 |OFF |OFF |끄기 |
| PCM 오류(팬 오류) |끄기 |끄기 |켜기 |X |
| PCM 결함(과도 진폭, 과전압, 과전류) |끄기 |켜기 |ON |켜기 |
| PCM(팬이 허용 오차를 벗어남) |켜기 |OFF |끄기 |켜기 |
| 대기 모드 |깜박임 |끄기 |OFF |끄기 |
| PCM 펌웨어 다운로드 |끄기 |깜박임 |깜박임 |깜박임 |

## <a name="controller-module-indicator-leds"></a>컨트롤러 모듈 표시기 LED
StorSimple 디바이스에는 기본 컨트롤러 및 EBOD 컨트롤러 모듈에 대 한 LED가 포함됩니다.   

### <a name="monitoring-leds-for-the-primary-controller"></a>기본 컨트롤러에 대한 모니터링 LED
다음 그림을 통해 기본 컨트롤러에 있는 LED를 확인할 수 있습니다. (방향을 지원하기 위해 모든 구성 요소가 나열되었습니다.)  

   ![LED -기본 컨트롤러 모니터링][4]

다음 표를 사용하여 컨트롤러 모듈이 올바르게 작동하는지 확인할 수 있습니다.  

### <a name="controller-indicator-leds"></a>컨트롤러 표시기 LED
| LED | 설명 |
| --- | --- |
| ID LED(파란색) |모듈이 식별되는지 나타냅니다. 실행 중인 컨트롤러에 파란색 LED가 깜박이면 해당 컨트롤러는 활성 컨트롤러이며 다른 하나는 대기 컨트롤러입니다. 자세한 내용은 [디바이스의 활성 컨트롤러 식별](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)을 참조하세요. |
| 오류 LED(주황색) |컨트롤러의 오류를 나타냅니다. |
| 정상 LED(녹색) |녹색은 컨트롤러가 정상임을 나타냅니다. 깜박이는 녹색은 컨트롤러 VPD 구성 오류를 나타냅니다. |
| SAS 활동 LED(녹색) |녹색은 현재 활동이 없는 연결을 나타냅니다. 깜박이는 녹색은 활동 중인 연결을 나타냅니다. |
| 이더넷 상태 LED |링크/네트워크 활동을 나타내는 오른쪽: 링크 활동(녹색), 네트워크 활동(깜박이는 녹색) 네트워크 속도를 나타내는 왼쪽: 1000Mb/s(노란색), 100Mb/s(녹색) 및 10Mb/s(꺼짐) 구성 요소 모델에 따라 네트워크 인터페이스가 설정되지 않은 경우에도 이 빛이 깜박일 수 있습니다. |
| POST LED |컨트롤러가 켜져 있을 때 부팅 진행률을 나타냅니다. StorSimple 디바이스에서 부팅에 실패하는 경우 이 LED는 Microsoft 기술 지원 서비스에서 오류가 발생한 부팅 프로세스 지점을 식별하는 데 도움이 됩니다. |

> [!IMPORTANT]
> 오류 LED가 켜져 있으면 컨트롤러를 다시 시작하여 해결할 수 있는 컨트롤러 모듈에 문제가 있습니다. 컨트롤러를 다시 시작해도 이 문제가 해결되지 않으면 Microsoft 기술 지원 서비스에 문의하세요.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>EBOD의 모니터링 LED(EBOD 인클로저)
각 6Gb/s SAS EBOD 컨트롤러에 다음 그림과 같이 해당 상태를 나타내는 LED가 있습니다.  

  ![LED - EBOD 엔클로저 모니터링][5]

다음 표를 사용하여 EBOD 컨트롤러 모듈이 올바르게 작동하는지 확인할 수 있습니다.  

### <a name="ebod-controller-module-indicator-leds"></a>EBOD 컨트롤러 모듈 표시기 LED
| 상태 | I/O 모듈 정상(녹색) | I/O 모듈 결함(주황색) | 호스트 포트 활동(녹색) |
| --- | --- | --- | --- |
| 컨트롤러 모듈 정상 |켜기 |끄기 |- |
| 컨트롤러 모듈 결함 |끄기 |켜기 |- |
| 외부 호스트 포트 연결 없음 |- |- |끄기 |
| 외부 호스트 포트 연결 - 작업 없음 |- |- |켜기 |
| 외부 호스트 포트 연결 - 작업 |- |- |깜박임 |
| 컨트롤러 모듈 메타데이터 오류 |깜박임 |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>기본 인클로저 및 EBOD 인클로저의 디스크 드라이브 표시기 LED
StorSimple 디바이스에는 기본 인클로저 및 EBOD 인클로저에 둘 다 있는 디스크 드라이브가 있습니다. 이 섹션에서 설명한 대로 각 디스크 드라이브에는 모니터링 표시기 LED가 포함됩니다. 

디스크 드라이브의 경우 드라이브 상태는 각 드라이브 캐리어 모듈 전면에 장착된 녹색 LED 및 빨간색-주황색 LED를 통해 나타납니다. 다음 그림에서는 이러한 LED를 보여줍니다.

  ![디스크 드라이브 LED][6]

다음 표를 사용하여 전체적인 전면 패널 LED 상태에 차례로 영향을 주는 각 디스크 드라이브의 상태를 확인할 수 있습니다.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>EBOD 인클로저의 디스크 드라이브 표시기 LED
| 상태 | 활동 정상 LED(녹색) | 오류 LED(빨간색-주황색) | 연결된 ops 패널 LED |
| --- | --- | --- | --- |
| 설치된 드라이브 없음 |끄기 |끄기 |없음 |
| 드라이브 설치됨 및 작동 |활동 시 깜박임 켜기/끄기 |X |없음 |
| SES(SCSI 인클로저 서비스) 디바이스 ID 집합 |켜기 |깜박임 1초 켜기/1초 끄기 |없음 |
| SES 디바이스 결함 비트 집합 |켜기 |켜기 |논리적 결함(빨간색) |
| 전원 제어 회로 오류 |끄기 |켜기 |모듈 결함(빨간색) |

## <a name="audible-alarms"></a>청각적 경보
StorSimple 디바이스는 기본 인클로저 및 EBOD 인클로저 둘 다와 관련된 청각적 경보를 포함합니다. 청각적 경보는 두 인클로저의 전면 패널(ops 패널이라고도 함)에 있습니다. 청각적 경보는 결함 조건이 있는 경우를 나타냅니다. 다음 조건에서 경보가 울립니다.  

* 팬 결함 또는 오류
* 전압 범위 벗어남
* 과열 또는 저온 상태
* 열 오버런
* 시스템 결함
* 논리적 결함
* 전원 공급 결함
* PCM(전원 냉각 모듈) 제거  

다음 표에서는 다양한 경보 상태를 설명합니다.  

### <a name="alarm-states"></a>경보 상태
| 경보 상태 | 액션(Action) | 음소거 단추를 누르면 작업 |
| --- | --- | --- |
| S0 |정상 모드: 무음 |경고음 두 번 |
| S1 |결함 모드: 1 초 켜기/1 초 끄기 |S2 또는 S3으로 전환(설명 참조) |
| S2 |알림 모드: 간헐적 경고음 |없음 |
| S3 |음소거 모드: 무음 |없음 |
| S4 |심각한 결함 모드: 경보 지속 |사용할 수 없음: 음소거가 활성화 되어있지 않음 |

> [!NOTE]
> * 경보 상태 S1에서 2분 이내에 음소거를 누르지 않으면 상태가 S2 또는 S3으로 자동 전환됩니다.  
> * 경보 상태 S1에서 S4는 결함 조건이 지워진 후 S0으로 돌아갑니다.  
> * 다른 상태에서 심각한 결함 상태 S4를 입력할 수 있습니다.  


ops 패널에 있는 음소거 단추를 눌러 청각적 경보를 음소거할 수 있습니다. 음소거 스위치를 수동으로 조작하지 않으면 2분 후 자동 음소거가 발생합니다. 경보가 음소거 되면 문제가 여전히 있음을 나타내기 위해 짧은 간헐적 경고음이 계속 됩니다. 모든 문제가 해결되면 경보가 멈춥니다.

다음 표에서는 다양한 경보 조건을 설명합니다.

### <a name="alarm-conditions"></a>경보 조건
| 상태 | 심각도 | 경보 | Ops 패널 LED |
| --- | --- | --- | --- |
| PCM 경보 – 단일 PCM에서 DC 전원 손실 |결함 - 중복 손실 없음 |S1 |모듈 결함 |
| PCM 경보 – 단일 PCM에서 DC 전원 손실 |결함 - 중복 손실 |S1 |모듈 결함 |
| PCM 팬 오류 |결함 - 중복 손실 |S1 |모듈 결함 |
| SBB 모듈이 PCM 오류 발견 |오류 |S1 |모듈 결함 |
| PCM 제거됨 |구성 오류 |없음 |모듈 결함 |
| 인클로저 구성 오류 |결함 - 중요 |S1 |모듈 결함 |
| 저온 경보 |Warning |S1 |모듈 결함 |
| 고온 경보 |Warning |S1 |모듈 결함 |
| 과열 경보 |결함 - 중요 |S1 |모듈 결함 |
| I2C 버스 오류 |결함 - 중복 손실 |S1 |모듈 결함 |
| Ops 패널 통신 오류(I2C) |결함 - 중요 |S1 |모듈 결함 |
| 컨트롤러 오류 |결함 - 중요 |S1 |모듈 결함 |
| SBB 인터페이스 모듈 결함 |결함 - 중요 |S1 |모듈 결함 |
| SBB 인터페이스 모듈 결함 - 남은 기능 모듈 없음 |결함 - 중요 |S4 |모듈 결함 |
| SBB 인터페이스 모듈 제거됨 |Warning |없음 |모듈 결함 |
| 드라이브 전원 컨트롤 결함 |경고 - 드라이브 전원 손실 없음 |S1 |모듈 결함 |
| 드라이브 전원 컨트롤 결함 |결함 - 중요, 드라이브 전원 손실 |S1 |모듈 결함 |
| 드라이브 제거됨 |Warning |없음 |모듈 결함 |
| 전원 부족 |Warning |없음 |모듈 결함 |

## <a name="next-steps"></a>다음 단계
[StorSimple 하드웨어 구성 요소 및 상태](storsimple-8000-monitor-hardware-status.md)에 대해 자세히 알아봅니다.

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


