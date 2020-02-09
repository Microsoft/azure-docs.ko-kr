---
title: 제한 및 할당량 IoT 플러그 앤 플레이 미리 보기 | Microsoft Docs
description: IoT 플러그 앤 플레이 미리 보기를 사용 하는 경우 적용 되는 제한, 할당량 및 제한에 대해 알아봅니다.
author: miagdp
ms.author: miag
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 48ecaaba6d956efd9da75d0582fa06d231cb3f80
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531380"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>IoT 플러그 앤 플레이 미리 보기 제한, 할당량 및 제한

이 문서에서는 공개 미리 보기에 적용 되는 IoT 플러그 앤 플레이 별 제한, 할당량 및 제한에 대해 설명 합니다. 기존 [IoT Hub 할당량과 제한이](../iot-hub/iot-hub-devguide-quotas-throttling.md) 적용 됩니다.

## <a name="iot-hub"></a>IoT Hub

공개 미리 보기의 경우 다음과 같은 제한과 할당량이 IoT hub에 적용 됩니다.

| 제한, 제한 사항 및 제한 | 값 | 메모 |
|-----|-----|-----|
| 허브 당 등록할 수 있는 DCMs (장치 기능 모델) 또는 인터페이스 수 | 1500 ||
| 장치당 등록할 수 있는 최대 인터페이스 수 | 40 ||
| 장치당 등록할 수 있는 최대 DCMs 수 | 1 ||
| 인터페이스/DCM 파일의 최대 크기 | 512 문자 ||
| 인터페이스 이름의 최대 크기 | 256 문자 ||
| 속성 이름의 최대 크기  | 64 바이트, 심층 7 수준 (및 첫 번째 수준 `$iotin`에 예약 됨) | 허용 되는 문자: a-z, a-z, 0-9 (첫 번째 문자 아님) 및 밑줄 |
| 속성 값의 최대 크기 | 512바이트 ||
| 명령 이름의 최대 크기 | 100바이트 ||
| 디바이스 쌍 크기 | [IoT Hub 제한과](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) 동일 ||
| SKU에 대 한 확인 API 호출 (단위에 관계 없음) | 100 요청/초 ||

## <a name="model-repository"></a>모델 리포지토리

공개 미리 보기의 경우 모델 리포지토리에 적용 되는 제한 및 할당량은 다음과 같습니다.

| 제한, 제한 사항 및 제한| 값 |
|-----|-----|
| Azure Active Directory 테넌트 당 회사 모델 리포지토리의 수 | 1 |
| 모델 리포지토리 당 권한 부여 키 수 | 10  |
| 회사 모델 리포지토리의 모델 수 (DCMs 또는 인터페이스)| 1500  |
| Azure Active Directory 테넌트 당 공용 모델 리포지토리의 모델 수 (DCMs 또는 인터페이스)| 1500  |
| 회사 모델 리포지토리에서 삭제 되는 DCMs 또는 인터페이스 수 | 초당 10 개의 쿼리 (QPS)|
| 테넌트에서 생성/업데이트 되는 모델 리포지토리의 수| QPS 1 |
| 모델 리포지토리에서 생성/업데이트/삭제 된 권한 부여 키의 수 | QPS 1|
| 회사 모델 리포지토리에서 생성 되는 DCMs의 수 | 10 QPS |
| 회사 모델 리포지토리에서 생성 되는 인터페이스의 수 | 10 QPS|
| 공용 모델 리포지토리에서 생성 되는 DCMs의 수 | 10 QPS|
| 공용 모델 리포지토리에서 생성 되는 인터페이스의 수 | 10 QPS|

## <a name="parser-library"></a>파서 라이브러리

파서 라이브러리는 [디지털 쌍 정의 언어](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)에 적용 되는 제한을 따릅니다.

## <a name="next-steps"></a>다음 단계

제안 된 다음 단계는 [IoT 플러그 앤 플레이 장치에 연결 하 고 상호 작용](./howto-develop-solution.md)하는 방법을 배우는 것입니다.
