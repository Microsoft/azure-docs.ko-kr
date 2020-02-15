---
title: 음성 장치 SDK 마이크 배열 권장 사항
titleSuffix: Azure Cognitive Services
description: 음성 장치 SDK 마이크 배열 권장 사항. 이러한 배열 기 하 도형은 Microsoft 오디오 스택과 함께 사용 하는 것이 좋습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: a87bdd7a55036e8b70f0bc5816d2b587c1569202
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168131"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>음성 장치 SDK 마이크 배열 권장 사항

이 문서에서는 음성 장치 SDK에 대 한 마이크 배열을 디자인 하는 방법에 대해 알아봅니다.

음성 장치 SDK는 마이크 기 하 도형 및 구성 요소 선택을 포함 하 여 다음 지침에 따라 디자인 된 마이크 배열에서 가장 잘 작동 합니다. 또한 통합 및 전기적 고려 사항에 대 한 지침도 제공 됩니다.

## <a name="microphone-geometry"></a>마이크 기 하 도형

다음 배열 기 하 도형은 Microsoft 오디오 스택과 함께 사용 하는 것이 좋습니다. 특정 응용 프로그램, 사용자 시나리오 및 장치 폼 팩터 종속성이 포함 된 많은 수의 마이크를 사용 하 여 사운드 소스 위치 및 주변 소음 거부 기능이 향상 되었습니다.

|     | 순환 배열 |     | 선형 배열 |     |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \# Mics | 7 | 4 | 4 | 2 |
| geometry | 6 개 외부, 1 중심, 반경 = 42.5 mm, 고르게 간격 | 3 외부, 1 중심, 반경 = 42.5 mm, 고르게 간격 | 길이 = 120 mm, 간격 = 40 mm | 간격 = 40 mm |

마이크 채널은 위의 각 배열에 대해 표시 되는 번호 매기기에 따라 정렬 되며 0에서 늘립니다. Microsoft 오디오 스택은 반향 취소를 수행 하기 위해 오디오 재생의 추가 참조 스트림이 필요 합니다.

## <a name="component-selection"></a>구성 요소 선택

노이즈 및 왜곡을 사용 하지 않는 신호를 정확 하 게 재현 하려면 마이크 구성 요소를 선택 해야 합니다.

마이크 선택 시 권장 되는 속성은 다음과 같습니다.

| 매개 변수 | 권장 |
| --------- | ----------- |
| SNR | \>= 65 dB (1Khz 신호 94 dBSPL, 가중치가 적용 된 노이즈) |
| 진폭 일치 | ± 1 dB @ 1 kHz |
| 단계 일치 | ± 2 ° @ 1 kHz |
| 어쿠스틱 오버 로드 지점 (AOP) | \>= 120 dBSPL (THD = 10%) |
| 비트 전송률 | 최소 24 비트 |
| 샘플링 레이트 | 최소 16Khz\* |
| 빈도 응답 | ± 3 dB, 200-8000 Hz 부동 마스크\* |
| 안정성 | 저장소 온도 범위-40 ° C ~ 70 ° C<br />운영 온도 범위-20 ° C ~ 55 ° C |

고품질 _통신 (VoIP) 응용 프로그램에는 더 높은 샘플링 속도 또는 "더 광범위 한" 빈도 범위가 필요 합니다_ . \*

사용 되는 구성 요소의 성능을 하므로 성능이 저하 방지 하기 위해 좋은 구성 요소 선택은 좋은 electroacoustic 통합과 쌍을 이루어야 합니다. 고유한 사용 사례에는 추가 요구 사항 (예: 운영 온도 범위)이 필요할 수도 있습니다.

## <a name="microphone-array-integration"></a>마이크 배열 통합

장치에 통합 되는 마이크 배열의 성능은 구성 요소 사양과 다릅니다. 통합 후 마이크가 잘 일치 하는지 확인 하는 것이 중요 합니다. 따라서 고정 이득 또는 EQ 이후에 측정 된 장치 성능은 다음 권장 사항을 충족 해야 합니다.

| 매개 변수          | 권장                                        |
| ------------------ | -------------------------------------------------- |
| SNR                | \> 63 dB (1Khz 신호 94 dBSPL, 가중치가 적용 된 노이즈) |
| 출력 민감도 | -26 dBFS/Pa @ 1 kHz (권장)                  |
| 진폭 일치 | ± 2 dB, 200-8000 Hz                                |
| THD%\*             | ≤ 1%, 200-8000 Hz, 94 dBSPL, 다섯 번째 순서             |
| 빈도 응답 | ± 6 dB, 200-8000 Hz 부동 마스크\*\*              |

_THD (예: NEUMANN KH120)를 측정 하려면 낮은 왜곡 스피커가 \*\*합니다._

_VoIP (고품질 통신) 응용 프로그램에는 \*\*"더 광범위 한" 빈도 범위가 필요할 수 있습니다_ .

## <a name="speaker-integration-recommendations"></a>스피커 통합 권장 사항

스피커가 포함 된 음성 인식 장치에 대해 반향 취소가 필요 하므로 스피커 선택 및 통합을 위한 추가 권장 사항이 제공 됩니다.

| 매개 변수 | 권장 |
| --------- | ----------- |
| 선형 고려 사항 | 스피커가 참조 된 후에는 비선형 처리가 수행 되지 않습니다. 그렇지 않으면 하드웨어 기반 루프백 참조 스트림이 필요 합니다. |
| 스피커 루프백 | WASAPI, 개인 Api, 사용자 지정 ALSA 플러그 인 (Linux)을 통해 제공 되거나 펌웨어 채널을 통해 제공 됩니다. |
| THD% | 세 번째 되며 밴드 최소 5 순서, 70 dBA 재생 @ 0.8 m ≤ 6.3%, 315-500 Hz ≤ 5%, 630-5000 Hz |
| 마이크와 마이크 결합 | ITU-T G. 122 부록 b. 4 메서드를 사용 하는 \>-10 dB-mic 수준으로 정규화<br />TCLw = Tclw\+ (측정 된 수준-대상 출력 민감도)<br />TCLw = Tclw측정 \+ (측정 된 수준-(-26)) |

## <a name="integration-design-architecture"></a>통합 디자인 아키텍처

장치에 마이크를 통합 하는 경우 다음과 같은 아키텍처 지침이 필요 합니다.

| 매개 변수 | 권장 |
| --------- | -------------- |
| Mic 포트 유사성 | 모든 마이크 포트는 배열의 길이와 동일 합니다. |
| Mic 포트 차원 | 포트 크기 Ø 0.8-1.0 mm. 포트 길이/포트 직경 \< 2 |
| Mic 밀봉         | 스택에서 균일 하 게 구현 된 gaskets를 봉인 합니다. \> 70% 압축 비율의 폼 gaskets 권장 |
| Mic 안정성     | 메시는 먼지가 나 수신을 방지 하는 데 사용 해야 합니다 (하위 이식 마이크의 경우 PCB와 gasket/상단 덮개 사이). |
| Mic 격리       | 특히 통합 스피커로 인 한 진동 경로를 격리 하기 위해 구조를 통해 고무 gaskets 및 진동 분리 |
| 샘플링 클록      | 장치 오디오는 지터를 사용 하지 않아야 하 고, 낮은 드리프트로 드롭 아웃 해야 합니다. |
| 레코드 기능   | 장치는 개별 채널 원시 스트림을 동시에 기록할 수 있어야 합니다. |
| USB                 | 모든 USB 오디오 입력 장치는 [Usb 오디오 장치 Rev3 사양](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) 에 따라 설명자를 설정 해야 합니다. |
| 마이크 기 하 도형 | 드라이버에서 [마이크 배열 Geometry 설명자](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) 를 올바르게 구현 해야 합니다. |
| 검색 기능     | 장치에는 장치에 대 한 undiscoverable 또는 제어할 수 없는 하드웨어, 펌웨어 또는 타사 소프트웨어 기반의 비선형 오디오 처리 알고리즘이 없어야 합니다. |
| 캡처 형식      | 캡처 형식은 최소 샘플링 율 16Khz 및 권장 24 비트 깊이를 사용 해야 합니다. |

## <a name="electrical-architecture-considerations"></a>전기 아키텍처 고려 사항

해당 하는 경우, 배열은 USB 호스트 (예: Microsoft 오디오 스택을 실행 하는 SoC)에 연결 되 고 음성 서비스 또는 기타 응용 프로그램에 대 한 인터페이스를 사용할 수 있습니다.

TDM 변환과 같은 하드웨어 구성 요소는 마이크의 동적 범위와 SNR이 다시 샘플러 내에서 유지 되는지 확인 해야 합니다.

고속 USB 오디오 클래스 2.0는 더 높은 샘플 속도 및 비트 깊이에서 최대 7 개의 채널에 필요한 대역폭을 제공 하기 위해 모든 오디오 Mcu 내에서 지원 되어야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 장치 SDK에 대 한 자세한 정보](speech-devices-sdk.md)
