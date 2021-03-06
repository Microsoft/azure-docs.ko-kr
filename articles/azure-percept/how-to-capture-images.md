---
title: Azure Percept Studio에서 코드 없는 비전 솔루션에 대 한 이미지 캡처
description: 코드를 사용 하지 않는 비전 솔루션을 위해 azure Percept Studio에서 Azure Percept 진한 이미지를 캡처하는 방법을 알아봅니다.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: d6cece6ee3079ba9f400f40026ca26ea36668710
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024645"
---
# <a name="capture-images-for-a-vision-project-in-azure-percept-studio"></a>Azure Percept Studio에서 비전 프로젝트용 이미지 캡처

이 가이드에 따라 azure Percept Studio에서 기존 비전 프로젝트에 대 한 Azure Percept의 비전 SoM을 사용 하 여 이미지를 캡처합니다. 비전 프로젝트를 아직 만들지 않은 경우 [코드 없는 비전 자습서](./tutorial-nocode-vision.md)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Percept DK(Devkit)
- [Azure 구독](https://azure.microsoft.com/free/)
- [Azure Percept DK 설정 환경](./quickstart-percept-dk-set-up.md): devkit를 Wi-Fi 네트워크에 연결하고, IoT Hub를 만들고, devkit를 IoT Hub에 연결
- [코드 없는 비전 프로젝트](./tutorial-nocode-vision.md)

## <a name="capture-images"></a>이미지 캡처

1. Devkit의 전원을 켭니다.

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)로 이동합니다.

1. 개요 페이지의 왼쪽에서 **장치** 를 클릭 합니다.

    :::image type="content" source="./media/how-to-capture-images/overview-devices-inline.png" alt-text="Azure Percept Studio 개요 화면" lightbox="./media/how-to-capture-images/overview-devices.png":::

1. 목록에서 devkit를 선택 합니다.

    :::image type="content" source="./media/how-to-capture-images/select-device.png" alt-text="Percept devices 목록.":::

1. 장치 페이지에서 **프로젝트에 대 한 이미지 캡처** 를 클릭 합니다.

    :::image type="content" source="./media/how-to-capture-images/capture-images.png" alt-text="사용 가능한 작업이 나열 된 Percept devices 페이지":::

1. **이미지 캡처** 창에서 다음을 수행 합니다.

    1. **프로젝트** 드롭다운 메뉴에서 이미지를 수집 하려는 비전 프로젝트를 선택 합니다.

    1. **장치 스트림 보기** 를 클릭 하 여 비전 SoM의 카메라가 올바르게 배치 되었는지 확인 합니다.

    1. **사진 촬영** 을 클릭 하 여 이미지를 캡처합니다.

    1. 또는 **자동 이미지 캡처** 옆의 확인란을 선택 하 여 이미지 캡처에 대 한 타이머를 설정 합니다.

        1. **캡처 요금** 에서 선호 하는 이미징 요금을 선택 합니다.
        1. **대상** 에서 수집할 이미지의 총 수를 선택 합니다.

    :::image type="content" source="./media/how-to-capture-images/take-photo.png" alt-text="이미지 캡처 화면":::

모든 이미지는 [Custom Vision](https://www.customvision.ai/)에서 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[코드 없는 비전 모델을 테스트 하 고 다시 학습](../cognitive-services/custom-vision-service/test-your-model.md)합니다.