---
title: StorSimple 기술 사양 | Microsoft Docs
description: StorSimple 하드웨어 구성 요소에 대한 기술 사양 및 규제 표준 준수 정보를 설명합니다.
services: storsimple
documentationcenter: NA
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
ms.openlocfilehash: b36d721896bd7b4f95d831eded500a96969937c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631892"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>StorSimple 디바이스에 대한 기술 사양 및 규정 준수

## <a name="overview"></a>개요

Microsoft Azure StorSimple 디바이스의 하드웨어 구성 요소는 이 문서에 설명된 기술 사양 및 규제 표준을 준수합니다. 기술 사양은 PCM(전원 및 냉각 모듈), 디스크 드라이브, 저장소 용량, 엔클로저를 설명합니다. 호환성 정보는 국제 표준, 안전성 및 배출 및 케이블 연결 등의 작업을 다룹니다.

## <a name="power-and-cooling-module-specifications"></a>전원 및 냉각 모듈 사양

StorSimple 디바이스에는 두 개의 100-240V 듀얼 팬, SBB 규격 PCM(전원 냉각 모듈)이 있습니다. 중복 전원 구성을 제공합니다. PCM에 실패하면, 디바이스는 실패한 모듈이 교체될 때까지 다른 PCM에서 정상적으로 작동합니다.

EBOD 엔클로저는 580 W PCM을 사용하며 기본 엔클로저는 764 W PCM을 사용합니다. 다음 표에서 PCM과 관련된 기술 사양을 보여줍니다.

| 사양 | 580 W PCM(EBOD) | 764 W PCM(기본) |
| --- | --- | --- |
| 최대 출력 전원 |580W |764 |
| Frequency(빈도) |50/60Hz |50/60Hz |
| 전압 범위 선택 |자동 범위 지정: 90 – 264 V AC, 47/63 Hz |자동 범위 지정: 90- 264 V AC, 47/63 Hz |
| 최대 유입 전류 |20A |20A |
| 전원 요소 수정 |> 95% 정격 입력 전압 |> 95% 정격 입력 전압 |
| 고주파 |EN61000-3-2를 충족합니다. |EN61000-3-2를 충족합니다. |
| 출력 |5V 대기 전압 \@ 2.0 A |5V 대기 전압 \@ 2.7 A |
| +5V \@ 42 A |+5V \@ 40 A | |
| +12V \@ 38 A |+12V \@ 38 A | |
| 핫 플러그형 |예 |예 |
| 스위치 및 LED |AC ON/OFF 스위치와 4개의 상태 표시기 LED |AC ON/OFF 스위치와 6개의 상태 표시기 LED |
| 냉각 엔클로저 |가변 팬 속도 제어를 사용한 냉각팬 축 |가변 팬 속도 제어를 사용한 냉각팬 축 |

## <a name="power-consumption-statistics"></a>전원 소비 통계

다음 표에서 StorSimple 디바이스의 다양한 모델에 대한 일반적인 전원 소비 데이터(실제 값은 게시 값과 다를 수 있음)를 나열합니다.

| 조건 | 240V AC | 240V AC | 240V AC | 110V AC | 110V AC | 110V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  속도가 느린 팬 , 유휴 상태의 드라이브 |1.45 A |0.31 kW |1057.76 BTU/hr |3.19 A |0.34 kW |1160.13 BTU/hr |
|  속도가 느린 팬 , 드라이브 액세스 |1.54 A |0.33 kW |1126.01 BTU/hr |3.27 A |0.36 kW |1228.37 BTU/hr |
|  속도가 빠른 팬, 유휴 상태의 드라이브, 전원이 켜진 두 PSU |2.14 A |0.49 kW |1671.95 BTU/hr |4.99 A |0.54 kW |1842.56 BTU/hr |
|  속도가 빠른 팬, 유휴 상태의 드라이브, PSU 하나는 전원이 켜지고 하나는 유휴 상태 |2.05 A |0.48 kW |1637.83 BTU/hr |4.58 A |0.50 kW |1706.07 BTU/hr |
|  속도가 빠른 팬, 드라이브 액세스, 전원이 켜진 두 PSU |2.26 A |0.51 kW |1740.19 BTU/hr |4.95 A |0.54 kW |1842.56 BTU/hr |
|  속도가 빠른 팬, 드라이브 액세스, PSU 하나는 전원이 켜지고 하나는 유휴 상태 |2.14 A |0.49 kW |1671.95 BTU/hr |4.81 A |0.53 kW |1808.44 BTU/hr |

## <a name="disk-drive-specifications"></a>디스크 드라이브 사양

StorSimple 디바이스는 최대 12개의 3.5인치 폼 요소 SAS(Serial Attached SCSI) 디스크 드라이브를 지원합니다. 제품 구성에 따라 실제 드라이브는SSD(Solid-State Drives) 또는 HDD(하드 디스크 드라이브)의 혼합일 수 있습니다. 12개의 디스크 드라이브 슬롯이 3 x 4 구성으로 엔클로저 앞에 있습니다. EBOD 엔클로저는 12개의 다른 디스크 드라이브를 위한 추가 저장소를 허용합니다. 항상 HDD입니다.

## <a name="storage-specifications"></a>저장소 사양

StorSimple 디바이스에는 8100 및 8600에 대한 하드 디스크 드라이브 및 반도체 드라이브가 혼합되어 있습니다. 8100 및 8600에 대한 총 사용 가능한 용량은 각각 약 15TB 및 38TB입니다. 다음 표에서는 SSD, HDD 및 StorSimple 솔루션 용량의 컨텍스트에서 클라우드 용량에 대한 세부 정보를 설명합니다.

| 디바이스 모델 / 용량 | 8100 | 8600 |
| --- | --- | --- |
| HDD(하드 디스크 드라이브)의 수 |8 |19 |
| SSD(반도체 드라이브)의 수 |4 |5 |
| 단일 HDD 용량 |4 TB |4TB |
| 단일 SSD 용량 |400 GB |800GB |
| 예비 용량 |4 TB |4 TB |
| 사용 가능한 HDD 용량 |14TB |36TB |
| 사용 가능한 SSD 용량 |800GB |2TB |
| 총 사용 가능한 용량 * |최대 15TB |최대 38TB |
| 최대 솔루션 용량(클라우드 포함) |200TB |500TB |

<sup>* </sup>- *총 사용 가능한 용량에는 데이터, 메타데이터 및 버퍼에 사용할 수 있는 용량이 포함됩니다. 로컬로 고정된 볼륨을 8100 디바이스에서는 최대 8.5TB, 더 큰 8600 디바이스에서는 최대 22.5TB까지 프로비전할 수 있습니다. 자세한 내용은 [StorSimple 로컬로 고정된 볼륨](storsimple-8000-local-volume-faq.md)을 참조하세요.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>엔클로저 차원과 가중치 사양

다음 표에서 크기 및 무게에 대해 다양한 엔클로저 사양을 보여줍니다.

### <a name="enclosure-dimensions"></a>엔클로저 크기

다음 표에서 인치 및 밀리미터로 엔클로저의 크기를 나타냅니다.

| 인클로저 | 밀리미터 | 인치 |
| --- | --- | --- |
| 높이 |87.9 |3.46 |
| 장착 플랜지 너비 |483 |19.02 |
| 엔클로저 본체 너비 |443 |17.44 |
| 전면 장착 플랜지에서부터 엔클로저 본체끝까지의 깊이 |577 |22.72 |
| 작업 패널에서 엔클로저의 가장 끝까지의 깊이 |630.5 |24.82 |
| 장착 플랜지에서 엔클로저의 가장 끝까지의 깊이 |603 |23.74 |

### <a name="enclosure-weight"></a>엔클로저 무게

구성에 따라 완전히 로드된 기본 엔클로저는 21에서 33kg까지의 무게일 수 있으므로 다룰 때는 두 사람이 필요합니다.

| 인클로저 | 무게 |
| --- | --- |
| 최대 중량(구성에 따라 다름) |30kg – 33kg |
| 비어 있음(맞는 드라이브가 없음) |21-23kg |

## <a name="enclosure-environment-specifications"></a>엔클로저 환경 사양

이 섹션에는 엔클로저 환경과 관련된 사양을 나열합니다. 온도, 습도, 고도, 충격, 진동, 방향, 안전성 및 전자기 호환성(EMC)이 이 범주에 포함됩니다.

### <a name="temperature-and-humidity"></a>온도 및 습도

| 인클로저 | 주변 온도 범위 | 주변 상대 습도 | 최대 습구 |
| --- | --- | --- | --- |
| 작동 |5°C - 35°C(41°F - 95°F) |20%-80% 비압축 |28°C (82°F) |
| 작동 불가능 |-40°C - 70°C(40°F - 158°F) |5% - 100% 비압축 |29°C(84°F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>기류, 고도, 충격, 진동, 방향, 안전성 및 EMC

| 인클로저 | 운영 사양 |
| --- | --- |
| 기류 |시스템의 공기는 앞에서 뒤로 흐릅니다. 압력이 낮고, 후면 배기가 설치된 시스템을 작동할 수 있어야 합니다. 랙 문 및 장애물때문에 생성된 역 압력은 5 파스칼(0.5mm 수면계)을 초과해서는 안됩니다. |
| 작동 가능 고도 |최대 작동 온도가 7000 피트 이상에서 5°로 지정된 -30m에서 3045m까지(-100피트 ~ 10, 000피트)입니다. |
| 작동 불가능 고도 |-305m ~ 12,192m(-1,000피트 ~ 40,000피트) |
| 충격, 작동 |5g 10ms ½sine |
| 충격, 작동 불가능 |30g 10ms ½sine |
| 진동, 작동 |0.21g RMS 5-500Hz 임의 |
| 진동, 작동 불가능 |1.04g RMS 2-200Hz 임의 |
| 진동, 재배치 |3g 2-200Hz sine |
| 방향 및 탑재 |19"랙 탑재(2 EIA 단위) |
| 랙 레일 |IEC 297를 준수하는 최소 700mm(31.50인치) 깊이 랙 |
| 안전성 및 승인 |CE 및 UL EN 61000-3, IEC 61000-3, UL 61000 3 |
| EMC |EN55022(CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>국제 표준 준수

Microsoft Azure StorSimple 디바이스에서 다음 국제 표준을 준수합니다.  

* CE - EN 60950 - 1
* IEC 60950-1에 대한 CB 보고서
* UL 60950 - 1에 대한 UL 및 cUL 

## <a name="safety-compliance"></a>안전 규정 준수

Microsoft Azure StorSimple 디바이스는 다음 안전 등급을 만족합니다.

* 시스템 제품 형식 승인: UL, cUL, CE
* 안전 규정 준수: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>EMC 규정 준수

Microsoft Azure StorSimple 디바이스는 다음 EMC 등급을 만족합니다.

### <a name="emissions"></a>배출

디바이스는 전도성 및 복사성 배출 수준에 대한 EMC 규정을 준수합니다.

* 전도성 방출 제한 수준: CFR 47 파트 15B 클래스 A EN55022 클래스 A CISPR 클래스는
* 방출 제한 수준 복사 성: CFR 47 파트 15B 클래스 A EN55022 클래스 A CISPR 클래스는

### <a name="harmonics-and-flicker"></a>고주파 및 깜박임

디바이스는 EN61000-3-2/3을 준수합니다.

### <a name="immunity-limit-levels"></a>면역 제한 수준

디바이스는 EN55024를 준수합니다.

## <a name="ac-power-cord-compliance"></a>AC 전원 코드 규정 준수

플러그 및 전체 전원 코드 어셈블리는 디바이스가 사용 중인 국가에 맞는 표준을 충족해야하며 해당 국가에서 인정할 수 있는 안전 승인서가 있어야 합니다. 다음 표에서 미국과 유럽에 대한 표준을 보여줍니다.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>AC 전원 코드-미국(나열된 NRTL이어야 함)

| 구성 요소 | 사양 |
| --- | --- |
| 코드 형식 |SV 또는 SVT, 최소 18 AWG, 3선, 최대 2.0m 길이 |
| 플러그 |NEMA 5-15P 첨부 파일 형식 접지 플러그  등급 120V, 10A 또는 IEC 320 C14, 250V, 10A |
| 소켓 |IEC 320 C-13, 250V, 10A |

### <a name="ac-power-cords---europe"></a>AC 전원 코드-유럽

| 구성 요소 | 사양 |
| --- | --- |
| 코드 형식 |고주파, H05-VVF-3G1.0 |
| 소켓 |IEC 320 C-13, 250V, 10A |

## <a name="supported-network-cables"></a>지원되는 네트워크 케이블

10GbE 네트워크 인터페이스에 대한 데이터 2와 데이터 3은 [지원되는 네트워크 케이블 및 모듈의 목록](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 데이터 센터에 StorSimple 디바이스를 배포할 준비가 되었습니다. 자세한 내용은 [온-프레미스 디바이스 배포](storsimple-8000-deployment-walkthrough-u2.md)를 참조하세요.

