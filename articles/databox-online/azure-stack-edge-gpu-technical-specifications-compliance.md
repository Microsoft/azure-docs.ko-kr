---
title: GPU 기술 사양 및 규정 준수를 사용 하는 Microsoft Azure Stack Edge | Microsoft Docs
description: GPU를 사용 하는 Azure Stack Edge 장치에 대 한 기술 사양 및 규정 준수에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: 3f354655a612d4085b0a0de45ae1a6e5ee097ade
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266666"
---
# <a name="technical-specifications-and-compliance-for-azure-stack-edge-with-gpu"></a>GPU를 사용 하는 Azure Stack에 지에 대 한 기술 사양 및 규정 준수 

온보드 GPU (그래픽 처리 장치)를 사용 하는 Azure Stack Edge의 하드웨어 구성 요소는이 문서에 설명 된 기술 사양 및 규제 표준을 준수 합니다. 기술 사양은 하드웨어, PSUs (전원 공급 장치), 저장소 용량, 엔클로저 및 환경 표준을 설명 합니다.

## <a name="compute-and-memory-specifications"></a>계산 및 메모리 사양

Azure Stack Edge 디바이스에는 컴퓨팅 및 메모리에 대한 다음과 같은 사양이 있습니다.

| 사양           | 값                  |
|-------------------------|----------------------------|
| CPU                     | 2 X Intel Xeon 실버 4214 (캐스케이드 Lake) CPU            |
| 메모리                  | 128 (8x16 GB) GB RAM                     |


## <a name="compute-acceleration-specifications"></a>계산 가속 사양

GPU (그래픽 처리 장치)는 Kubernetes, 심층 학습 및 기계 학습 시나리오를 지 원하는 모든 Azure Stack Edge 장치에 포함 되어 있습니다.

| 사양           | 값                  |
|-------------------------|----------------------------|
| GPU   | 하나 또는 두 개의 nVidia T4 Gpu <br> 자세한 내용은 [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)를 참조 하세요.| 


## <a name="power-supply-unit-specifications"></a>전원 공급 장치 사양

Azure Stack Edge 장치에는 고성능 팬이 있는 2 개의 100-240 V 전원 공급 장치 (PSUs)가 있습니다. 두 대의 PSU는 중복 전원 구성을 제공합니다. PSU가 실패하면, 디바이스는 실패한 모듈이 교체될 때까지 다른 PSU에서 정상적으로 작동합니다. 다음 표에는 PSU의 기술 사양이 나열되어 있습니다.

| 사양           | 750W PSU                  |
|-------------------------|----------------------------|
| 최대 출력 전원    | 750W                     |
| 빈도               | 50/60Hz                   |
| 전압 범위 선택 | 자동 범위 지정: 100-240V AC |
| 핫 플러그형           | 예                        |


## <a name="network-interface-specifications"></a>네트워크 인터페이스 사양

Azure Stack Edge 장치에는 PORT1-PORT6의 6 개 네트워크 인터페이스가 있습니다.

| 사양           | Description                 |
|-------------------------|----------------------------|
|  네트워크 인터페이스    | **2 X 1 GbE 인터페이스** – 1 관리 인터페이스 포트 1은 초기 설치에 사용 되며 기본적으로 정적입니다. 초기 설치가 완료 된 후에는 모든 IP 주소를 사용 하 여 데이터에 대 한 인터페이스를 사용할 수 있습니다. 그러나 다시 설정 시 인터페이스는 정적 IP로 돌아갑니다. <br>다른 인터페이스 포트 2는 사용자가 구성할 수 있으며, 데이터 전송에 사용할 수 있으며, 기본적으로 DHCP입니다. <br>**4 X 25 GbE 인터페이스** – 포트 6을 통해 포트 3의 이러한 데이터 인터페이스는 DHCP (기본값) 또는 정적으로 사용자에 의해 구성 될 수 있습니다. 이러한 인터페이스는 10 개의 GbE 인터페이스로 작동할 수도 있습니다.  | 

Azure Stack Edge 장치에는 다음과 같은 네트워크 하드웨어가 있습니다.

* **사용자 지정 Microsoft Qlogic Cavium 25G NDC 어댑터** -포트 1에서 포트 4까지
* **Mellanox 이중 포트 25G connectx-3-4 채널 네트워크 어댑터** -포트 5 및 포트 6.

다음은 Mellanox 카드에 대 한 세부 정보입니다.

| 매개 변수           | 설명                 |
|-------------------------|----------------------------|
| 모델    | Connectx-3®-4 Lx 네트워크 인터페이스 카드                      |
| 모델 설명               | 25GbE 이중 포트 SFP28; PCIe 3.0 x8; ROHS R6                    |
| 장치 부품 번호 (R640) | MCX4121A-ACAT  |
| PSID (R640)           | MT_2420110034                         |

이러한 네트워크 카드에 대해 지원 되는 케이블, 스위치 및 트랜시버의 전체 목록은 다음으로 이동 합니다.

- [Qlogic Cavium 25G NDC 어댑터 상호 운용성 매트릭스](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)입니다.
- [Mellanox 이중 포트 25G connectx-3-4 채널 네트워크 어댑터 호환 제품](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)입니다.  

## <a name="storage-specifications"></a>스토리지 사양

Azure Stack Edge 장치에는 각각 1.6 TB의 용량을 포함 하는 5 개의 2.5 "NVMe DC P4610 Ssd가 있습니다. 부팅 드라이브는 240 g b의 SATA SSD입니다. 장치에 사용할 수 있는 총 용량은 약 8.28 TB입니다. 다음 표에는 장치의 저장소 용량이 정리 되어 있습니다.

|     사양                          |     값             |
|--------------------------------------------|-----------------------|
|    부팅 SATA 반도체 드라이브 (SSD)      |    1                  |
|    NVMe Ssd 수                     |    5                  |
|    부팅 SSD 용량                       |    240GB             |
|    단일 NVMe SSD 용량                |    1.6TB             |
|    총 용량                          |    8.28 TB            |
|    총 사용 가능한 용량 *                  |    ~ 7.95 TB          |
|    SAS 컨트롤러                          |    HBA330 12 Gbps     |


**일부 공간은 내부용으로 예약되어 있습니다.*

<!--Remove based on feedback from Ravi
## Other hardware specifications

Your Azure Stack Edge device also contains the following hardware:

* iDRAC baseboard management
* Performance fans
* Custom ID module-->

## <a name="enclosure-dimensions-and-weight-specifications"></a>엔클로저 차원과 가중치 사양

다음 표에서 크기 및 무게에 대해 다양한 엔클로저 사양을 보여줍니다.

### <a name="enclosure-dimensions"></a>엔클로저 크기

다음 표에서는의 1U 장치 엔클로저 크기를 밀리미터 및 인치로 나열 합니다.

|     인클로저     |     밀리미터     |     인치     |
|-------------------|---------------------|----------------|
|    높이         |    44.45            |    1.75"       |
|    너비          |    434.1            |    17.09"      |
|    길이         |    740.4            |    29.15"      |

다음 표에서는 인치 및 밀리미터로 배송 패키지의 크기를 나타냅니다.

|     패키지     |     밀리미터     |     인치     |
|-------------------|---------------------|----------------|
|    높이         |    311.2            |    12.25인치          |
|    너비          |    642.8            |    25.31인치          |
|    길이         |    1,051.1          |    41.38인치          |

### <a name="enclosure-weight"></a>엔클로저 무게

장치 패키지 따져 66 들어 올리려면 두 사람이 필요합니다. 디바이스의 무게는 엔클로저 구성에 따라 달라집니다.

|     인클로저                                 |     무게          |
|-----------------------------------------------|---------------------|
|    포장을 포함한 총 무게       |    61lbs          |
|    디바이스의 무게                       |    35lbs          |

## <a name="enclosure-environment-specifications"></a>엔클로저 환경 사양

이 섹션에서는 온도, 습도 및 고도와 같은 엔클로저 환경과 관련된 사양을 나열합니다.

### <a name="temperature-and-humidity"></a>온도 및 습도

|     인클로저         |     주변 온도 범위     |     주변 상대 습도     |     최대 이슬점     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    작동        |    10°C - 35°C(50°F - 86°F)         |    10% - 80% 비응결         |    29°C(84°F)            |
|    작동 불가능    |    -40°C - 65°C(-40°F - 149°F)     |    5% - 95% 비응결          |    33°C(91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>기류, 고도, 충격, 진동, 방향, 안전성 및 EMC

|     인클로저                           |     운영 사양                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    기류                              |    시스템의 공기는 앞에서 뒤로 흐릅니다. 압력이 낮고, 후면 배기가 설치된 시스템을 작동할 수 있어야 합니다. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    작동 최대 고도        |    [작동 온도 출력 저하 사양](#operating-temperature-de-rating-specifications)에 따라 결정된 대로 최대 작동 온도 출력이 저하된 3048m(1만 피트)                                                                                |
|    작동 불가능 최대 고도    |    12,000m(39,370피트)                                                                                                                                                                                         |
|    충격, 작동                   |    11밀리초 동안 6G(6개 방향)                                                                                                                                                                         |
|    충격, 작동 불가능               |    2밀리초 동안 71G(6개 방향)                                                                                                                                                                           |
|    진동, 작동               |    0.26G<sub>RMS</sub> 5Hz-350Hz 임의                                                                                                                                                                                     |
|    진동, 작동 불가능           |    15분 동안 1.88G<sub>RMS</sub> 10Hz-500Hz(6개 측면으로 모두 테스트됨)                                                                                                                                                  |
|    방향 및 탑재             |    표준 19 "랙 탑재 (1U)                                                                                                                                                                                       |
|    안전성 및 승인                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014(Class D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    에너지             |    위원회 규정(EU) 번호 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>작동 온도 출력 저하 사양

|     작동 온도 출력 저하     |     주변 온도 범위                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    최대 35°C(95°F)                       |    최대 온도는 950m(3,117피트) 위부터 1°C/300m(1°F/547피트)씩 감소합니다.    |
|    35°C - 40°C(95°F - 104°F)            |    최대 온도는 950m(3,117피트) 위부터 1°C/175m(1°F/319피트)씩 감소합니다.    |
|    40°C - 45°C(104°F - 113°F)           |    최대 온도는 950m(3,117피트) 위부터 1°C/125m(1°F/228피트)씩 감소합니다.    |

## <a name="next-steps"></a>다음 단계

[Azure Stack Edge 배포](azure-stack-edge-gpu-deploy-prep.md)
