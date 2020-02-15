---
title: Azure Portal에서 IoT Central 관리 | Microsoft Docs
description: 이 문서에서는 Azure Portal에서 IoT Central 응용 프로그램을 만들고 관리 하는 방법을 설명 합니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 27517c375265b552d2e1dec4d8c167d1bc86549d
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137688"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Azure Portal에서 IoT Central 관리

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure IoT Central application manager](https://aka.ms/iotcentral) 웹 사이트에서 IoT Central 응용 프로그램을 만들고 관리 하는 대신 [Azure Portal](https://portal.azure.com) 를 사용 하 여 응용 프로그램을 관리할 수 있습니다.

## <a name="create-iot-central-applications"></a>IoT Central 애플리케이션 만들기

응용 프로그램을 만들려면 [Azure Portal](https://ms.portal.azure.com) 로 이동 하 여 **리소스 만들기**를 선택 합니다.

**Marketplace에서 검색** 표시줄에 *IoT Central*을 입력 합니다.

![관리 포털: 검색](media/howto-manage-iot-central-from-portal/image0a1.png)

검색 결과에서 **IoT Central 응용 프로그램** 타일을 선택 합니다.

![관리 포털: 검색 결과](media/howto-manage-iot-central-from-portal/image0b1.png)

이제 **만들기**를 선택 합니다.

![관리 포털: IoT Central 리소스](media/howto-manage-iot-central-from-portal/image0c1.png)

양식에 있는 모든 필드를 작성합니다. 이 양식은 [Azure IoT Central application manager](https://aka.ms/iotcentral) 웹 사이트에서 응용 프로그램을 만들기 위해 작성 하는 양식과 비슷합니다. 자세한 내용은 [IoT Central 애플리케이션 만들기](quick-deploy-iot-central.md) 빠른 시작을 참조하세요.

![IoT Central 양식 만들기](media/howto-manage-iot-central-from-portal/image6a.png)

**Location** 은 응용 프로그램을 만들려는 [지리](https://azure.microsoft.com/global-infrastructure/geographies/) 입니다. 일반적으로 최적의 성능을 얻으려면 디바이스와 물리적으로 가장 가까운 위치를 선택해야 합니다. Azure IoT Central 공개 미리 보기는 현재 **미국**, **오스트레일리아**, **아시아 태평양** 또는 **유럽**에서 사용할 수 있습니다.  위치를 선택하면 나중에 다른 위치로 애플리케이션을 이동할 수 없습니다.


모든 필드를 채운 후 **만들기**를 선택 합니다.

## <a name="manage-existing-iot-central-applications"></a>기존 IoT Central 애플리케이션 관리

Azure IoT Central 애플리케이션이 이미 있으면 삭제하거나 Azure Portal의 다른 구독 또는 리소스 그룹으로 이동할 수 있습니다.

> [!NOTE]
> 무료 가격 책정 요금제에서 만든 응용 프로그램은 구독과 연결 되어 있지 않기 때문에 Azure Portal에서 볼 수 없습니다.

시작 하려면 포털에서 **모든 리소스** 를 선택 합니다. **숨겨진 형식 표시** 를 선택 하 고 **이름으로 필터** 에 응용 프로그램 이름을 입력 하 여 찾습니다. 그런 다음 관리 하려는 IoT Central 응용 프로그램을 선택 합니다.

응용 프로그램으로 이동 하려면 **IoT Central 응용 프로그램 URL**을 선택 합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image3.png)

응용 프로그램을 다른 리소스 그룹으로 이동 하려면 리소스 그룹 옆에서 **변경** 을 선택 합니다. **리소스 이동** 페이지에서이 응용 프로그램을 이동 하려는 리소스 그룹을 선택 합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image4a.png)

응용 프로그램을 다른 구독으로 이동 하려면 구독 옆에서 **변경** 을 선택 합니다. **리소스 이동** 페이지에서이 응용 프로그램을 이동 하려는 구독을 선택 합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>다음 단계

Azure Portal에서 Azure IoT Central 애플리케이션을 관리하는 방법을 알아보았으므로 다음 단계를 진행하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)