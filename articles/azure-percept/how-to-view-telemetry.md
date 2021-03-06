---
title: Azure Percept 진한 모델 유추 원격 분석 보기
description: Azure IoT 탐색기에서 Azure Percept 진한의 비전 모델 유추 원격 분석을 보는 방법에 대해 알아봅니다.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 07abbc5f5e85c75b73774d11b6b81dd2085735b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095326"
---
# <a name="view-your-azure-percept-dks-model-inference-telemetry"></a>Azure Percept 진한 모델 유추 원격 분석 보기

이 가이드에 따라 azure [IoT 탐색기](https://github.com/Azure/azure-iot-explorer/releases)에서 AZURE Percept 진한의 비전 모델 유추 원격 분석을 볼 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Percept DK(Devkit)
- [Azure 구독](https://azure.microsoft.com/free/)
- [Azure Percept DK 설정 환경](./quickstart-percept-dk-set-up.md): devkit를 Wi-Fi 네트워크에 연결하고, IoT Hub를 만들고, devkit를 IoT Hub에 연결
- [사용자의 Azure Percept에 배포 된 비전 AI 모델](./how-to-deploy-model.md)

## <a name="view-telemetry"></a>원격 분석 보기

1. Devkit의 전원을 켭니다.

1. [Azure IoT 탐색기](https://github.com/Azure/azure-iot-explorer/releases)를 다운로드 하 여 설치 합니다. Windows 사용자 인 경우 .msi 파일을 선택 합니다.

    :::image type="content" source="./media/how-to-view-telemetry/azure-iot-explorer-download.png" alt-text="Azure IoT 탐색기의 다운로드 화면.":::

1. Azure IoT 탐색기에 IoT Hub 연결 합니다.

    1. [Azure 포털](https://portal.azure.com)로 이동합니다.

    1. **모든 리소스** 를 선택합니다.

        :::image type="content" source="./media/how-to-view-telemetry/azure-portal.png" alt-text="Azure Portal 홈페이지":::

    1. Azure Percept 진한가 연결 된 IoT Hub를 선택 합니다.

        :::image type="content" source="./media/how-to-view-telemetry/iot-hub.png" alt-text="Azure Portal IoT Hub 목록입니다.":::

    1. IoT Hub 페이지의 왼쪽에서 **공유 액세스 정책** 을 선택 합니다.

        :::image type="content" source="./media/how-to-view-telemetry/shared-access-policies.png" alt-text="공유 액세스 정책을 보여 주는 IoT Hub 페이지":::

    1. **Iothubowner** 을 클릭 합니다.

        :::image type="content" source="./media/how-to-view-telemetry/iothubowner.png" alt-text="Iothubowner가 강조 표시 된 공유 액세스 정책 화면":::

    1. **연결 문자열-기본 키** 옆에 있는 파란색 복사 아이콘을 클릭 합니다.

        :::image type="content" source="./media/how-to-view-telemetry/connection-string.png" alt-text="연결 문자열 복사 단추가 강조 표시 된 iothubowner 창":::

    1. Azure IoT 탐색기를 열고 **+ 연결 추가** 를 클릭 합니다.

    1. 연결 문자열 **추가** **창의 연결 문자열 상자에** 연결 문자열을 붙여넣고 **저장** 을 클릭 합니다.

        :::image type="content" source="./media/how-to-view-telemetry/add-connection-string.png" alt-text="연결 문자열을에 붙여 넣을 상자가 있는 Azure Iot 탐색기 창":::

    1. 모델 추론 개체에서 비전 SoM을 가리킵니다.

    1. **원격 분석** 을 선택 합니다.

    1. **시작** 을 클릭 하 여 장치에서 원격 분석 이벤트를 봅니다.

## <a name="next-steps"></a>다음 단계
[Azure PERCEPT 진한 비디오 스트림을](./how-to-view-video-stream.md)보는 방법에 대해 알아봅니다.