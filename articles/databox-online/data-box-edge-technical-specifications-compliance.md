---
title: Microsoft Azure Data Box Edge 기술 사양 및 규정 준수 | Microsoft Docs
description: Azure Data Box Edge에 대한 기술 사양 및 규정 준수에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/26/2019
ms.author: alkohli
ms.openlocfilehash: 9e1a7f7cd2643aae61e60d77ad74f4a08266a977
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863600"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Azure Data Box Edge 기술 사양

Microsoft Azure Data Box Edge 장치의 하드웨어 구성 요소는이 문서에 설명 된 기술 사양과 규정 표준을 준수 합니다. 기술 사양은 PSUs (전원 공급 장치), 저장소 용량, 엔클로저 및 환경 표준을 설명 합니다. 

## <a name="compute-memory-specifications"></a>계산, 메모리 사양

Data Box Edge 장치에는 계산 및 메모리에 대한 다음과 같은 사양이 있습니다.

| 사양           | 값                  |
|-------------------------|----------------------------|
| CPU    | 2 X 10 코어 CPU                     |
| 메모리              | 128GB RAM                  |


## <a name="fpga-specifications"></a>FPGA 사양

ML (Machine Learning) 시나리오를 가능 하 게 하는 FPGA (필드 프로그래밍 가능 게이트 배열)는 모든 Data Box Edge 장치에 포함 됩니다. 

| 사양           | 값                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> 사용 가능한 심층 신경망 (DNN) 모델은 [CLOUD FPGA 인스턴스에서 지 원하는](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure)것과 같습니다.| 


## <a name="power-supply-unit-specifications"></a>전원 공급 장치 사양

Data Box Edge 장치에는 고성능 팬이 있는 2 개의 100-240 V 전원 공급 장치 (PSUs)가 있습니다. 두 PSUs는 중복 전원 구성을 제공 합니다. PSU에 오류가 발생 하는 경우 장치는 오류가 발생 한 모듈이 교체 될 때까지 다른 PSU에서 계속 정상적으로 작동 합니다. 다음 표에는 PSUs의 기술 사양이 나열 되어 있습니다.

| 사양           | 750 W PSU                  |
|-------------------------|----------------------------|
| 최대 출력 전원    | 750 W                     |
| 빈도               | 50/60Hz                   |
| 전압 범위 선택 | 자동 범위: 100-240 V AC |
| 핫 플러그형           | 예                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>스토리지 사양

Data Box Edge 장치에는 각각 1.6 TB의 용량을 포함 하는 9 X 2.5 "NVMe Ssd가 있습니다. 이러한 Ssd 중 1 개는 운영 체제 디스크이 고 다른 8 개는 데이터 디스크입니다. 장치에 사용할 수 있는 총 용량은 약 12.5 TB입니다. 다음 표에는 장치의 저장소 용량에 대한 세부 정보가 나와 있습니다.

|     사양                          |     값             |
|--------------------------------------------|-----------------------|
|    SSD(반도체 드라이브)의 수     |    8                  |
|    단일 SSD 용량                     |    1.6TB             |
|    전체 용량                          |    12.8TB            |
|    총 사용 가능한 용량 *                  |    ~ 12.5 TB            |

**일부 공간은 내부용으로 예약 되어 있습니다.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>엔클로저 차원과 가중치 사양

다음 표에서 크기 및 무게에 대해 다양한 엔클로저 사양을 보여줍니다.

### <a name="enclosure-dimensions"></a>엔클로저 크기

다음 표에서 인치 및 밀리미터로 엔클로저의 크기를 나타냅니다.

|     인클로저     |     밀리미터     |     인치     |
|-------------------|---------------------|----------------|
|    높이         |    44.45            |    1.75 "          |
|    너비          |    434.1           |    17.09 "          |
|    길이          |    740.4           |    29.15"          |

다음 표에서는 출하 패키지의 크기를 밀리미터 및 인치로 나열 합니다.

|     패키지     |     밀리미터     |     인치     |
|-------------------|---------------------|----------------|
|    높이         |    311.2            |    12.25인치          |
|    너비          |    642.8          |    25.31인치          |
|    길이          |   1,051.1          |    41.38인치          |

### <a name="enclosure-weight"></a>엔클로저 무게

장치 패키지 따져 66 및는이를 처리 하는 두 사람이 필요 합니다. 장치의 가중치는 인클로저 구성에 따라 달라 집니다.

|     인클로저                                 |     무게          |
|-----------------------------------------------|---------------------|
|    패키징을 포함 한 총 무게       |    61.          |
|    장치의 가중치                       |    35.          |

## <a name="enclosure-environment-specifications"></a>엔클로저 환경 사양

이 섹션에서는 온도, 습도, 고도 등 인클로저 환경과 관련 된 사양을 나열 합니다.

### <a name="temperature-and-humidity"></a>온도 및 습도

|     인클로저         |     주변 온도 범위     |     주변 상대 습도     |     최대 이슬점     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    작동        |    10 ° C-35 ° C (50 ° F-86 ° F)         |    10%-80% 비 비응축.         |    29°C(84°F)            |
|    작동 불가능    |    -40 ° C ~ 65 ° C (-40 ° F-149 ° F)     |    5%-95% 비응축.          |    33°C(91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>기류, 고도, 충격, 진동, 방향, 안전성 및 EMC

|     인클로저                           |     운영 사양                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    기류                              |    시스템의 공기는 앞에서 뒤로 흐릅니다. 압력이 낮고, 후면 배기가 설치된 시스템을 작동할 수 있어야 합니다. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    최대 고도, 작동        |    3048 미터 (1만 피트), [운영 온도 비 등급 사양](#operating-temperature-de-rating-specifications)에 따라 결정 되는 최대 운영 온도 등급 지정                                                                                |
|    최대 고가, 비작동    |    12000 미터 (39370 피트)                                                                                                                                                                                         |
|    충격, 작동                   |    6 방향 12 밀리초에 대한 6 G                                                                                                                                                                         |
|    충격, 작동 불가능               |    6 방향 2 밀리초의 경우 71 G                                                                                                                                                                           |
|    진동, 작동               |    0.26G<sub>RMS</sub> 5Hz-350Hz 임의                                                                                                                                                                                     |
|    진동, 작동 불가능           |    15 분 동안 1.88 G<sub>RMS</sub> 10 hz ~ 500 Hz (테스트 된 6 개 면 모두)                                                                                                                                                  |
|    방향 및 탑재             |    19 "랙 탑재                                                                                                                                                                                        |
|    안전성 및 승인                 |    EN 60950-1:2006 + A1:2010 + A2:2013 + A11:2009 + A12:2011/IEC 60950-1:2005 ed2 + A1:2009 + A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014(Class D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    에너지             |    위원회 규정(EU) 번호 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>운영 온도 비 등급 사양

|     운영 온도 비 등급     |     주변 온도 범위                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    최대 35 ° C (95 ° F)                       |    최대 온도는 950 m (3117 ft) 위의 1 ° C/300 m (1 ° F/547 ft)으로 줄어듭니다.    |
|    35 ° C ~ 40 ° C (95 ° F ~ 104 ° F)            |    최대 온도는 950 m (3117 ft) 이상으로 1 ° C/175 m (1 ° F/319 ft)으로 줄어듭니다.    |
|    40 ° C ~ 45 ° C (104 ° F to 113 ° F)           |    최대 온도는 950 m (3117 ft) 이상으로 1 ° C/125 m (1 ° F/228 개입니다 ft)으로 줄어듭니다.    |


## <a name="next-steps"></a>다음 단계

- [Azure Data Box Edge 배포](data-box-edge-deploy-prep.md)
