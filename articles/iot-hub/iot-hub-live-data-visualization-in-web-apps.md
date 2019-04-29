---
title: Azure IoT Hub에서 센서 데이터의 실시간 데이터 시각화 – Web Apps | Microsoft Docs
description: Microsoft Azure App Service의 Web Apps 기능을 사용하여 센서에서 수집하여 IoT Hub로 보낸 온도 및 습도 데이터를 시각화할 수 있습니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: 070f37a969411cfc4caf5f2d2b089ccfae759ca2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125715"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-by-using-the-web-apps-feature-of-azure-app-service"></a>Azure App Service의 Web Apps 기능을 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화

![종단 간 다이어그램](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>학습 내용

이 자습서에서는 웹앱에서 호스팅되는 웹 애플리케이션을 실행하여 IoT Hub에서 받는 실시간 센서 데이터를 시각화하는 방법에 대해 알아봅니다. Power BI를 사용하여 IoT Hub의 데이터를 시각화하려면 [Power BI를 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화](iot-hub-live-data-visualization-in-power-bi.md)를 참조하세요.

## <a name="what-you-do"></a>수행할 작업

* Azure Portal에서 웹앱 만들기
* 소비자 그룹을 추가하여 IoT Hub에서 데이터 액세스 준비
* IoT Hub에서 센서 데이터를 읽도록 웹앱 구성
* 웹앱에서 호스팅할 웹 애플리케이션 업로드
* 웹앱을 열어 IoT Hub에서 실시간 온도 및 습도 데이터 확인

## <a name="what-you-need"></a>필요한 항목

* 완료 합니다 [Raspberry Pi 온라인 시뮬레이터](iot-hub-raspberry-pi-web-simulator-get-started.md) 자습서 또는 장치 자습서; 중 하나 예를 들어 [node.js 사용 하 여 Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md)합니다. 이러한 다음 요구 사항을 다룹니다.

  * 활성 Azure 구독
  * 구독 중인 IoT Hub
  * 메시지를 IoT Hub로 보내는 클라이언트 애플리케이션

* [Git 다운로드](https://www.git-scm.com/downloads)

## <a name="create-a-web-app"></a>웹앱 만들기

1. [Azure Portal](https://portal.azure.com/)에서 **리소스 만들기** > **웹 + 모바일** > **웹앱**을 클릭합니다.

2. 고유한 작업 이름을 입력하고, 구독을 확인하며, 리소스 그룹 및 위치를 지정하고, **대시보드에 고정**을 선택한 다음, **만들기**를 클릭합니다.

   리소스 그룹과 동일한 위치를 선택하는 것이 좋습니다. 
   
   ![웹앱 만들기](./media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>IoT Hub에서 데이터를 읽도록 웹앱 구성

1. 방금 프로비전한 웹앱을 엽니다.

2. **애플리케이션 설정**을 클릭한 다음 **앱 설정** 아래에서 다음 키/값 쌍을 추가합니다.

   | 키                                   | 값                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Azure CLI에서 획득                                      |
   | Azure.IoT.IoTHub.ConsumerGroup        | IoT Hub에 추가하는 소비자 그룹의 이름  |
   | WEBSITE_NODE_DEFAULT_VERSION          | 8.9.4                                                        |

   ![키/값 쌍을 사용하여 Azure 웹앱에 설정 추가](./media/iot-hub-live-data-visualization-in-web-apps/3_web-app-settings-key-value-azure.png)

3. **애플리케이션 설정**을 클릭하고 **일반 설정**에서 **웹 소켓** 옵션을 설정/해제한 후 **저장**을 클릭합니다.

   ![웹 소켓 옵션 설정/해제](./media/iot-hub-live-data-visualization-in-web-apps/4_toggle_web_sockets.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>웹앱에서 호스팅할 웹 애플리케이션 업로드

GitHub에서 IoT Hub의 실시간 센서 데이터를 표시하는 웹 애플리케이션을 사용할 수 있었습니다. Git 리포지토리에서 작동하도록 웹앱을 구성하고, GitHub에서 웹 애플리케이션을 다운로드하고, Azure에 업로드하여 웹앱에서 호스트하기만 하면 됩니다.

1. 웹앱에서 **배포 옵션** > **원본 선택** > **로컬 Git 리포지토리**를 차례로 클릭한 다음 **확인**을 클릭합니다.

   ![로컬 Git 리포지토리를 사용하도록 웹앱 배포 구성](./media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

2. **배포 자격 증명**을 클릭하고 Azure에서 Git 리포지토리에 연결하는 데 사용할 사용자 이름과 암호를 만든 다음 **저장**을 클릭합니다.

3. **개요**를 클릭하고 **Git 복제 URL** 값을 기록해 둡니다.

   ![웹앱의 Git Clone URL 가져오기](./media/iot-hub-live-data-visualization-in-web-apps/6_web-app-git-clone-url-azure.png)

4. 로컬 컴퓨터에서 명령 창 또는 터미널 창을 엽니다.

5. GitHub에서 웹앱을 다운로드하고, Azure를 업로드하여 웹앱을 호스팅합니다. 이렇게 하려면 다음 명령을 실행합니다.

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   cd web-apps-node-iot-hub-data-visualization
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!NOTE]
   > \<Git 복제 URL\>은 웹앱의 **개요** 페이지에서 있는 Git 리포지토리의 URL입니다.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>웹앱을 열어 IoT Hub에서 실시간 온도 및 습도 데이터 확인

웹앱의 **개요** 페이지에서 URL을 클릭하여 웹앱을 엽니다.

![웹앱의 URL 가져오기](./media/iot-hub-live-data-visualization-in-web-apps/7_web-app-url-azure.png)

IoT Hub에서 실시간 온도 및 습도 데이터를 볼 수 있습니다.

![실시간 온도 및 습도를 보여 주는 웹앱 페이지](./media/iot-hub-live-data-visualization-in-web-apps/8_web-app-page-show-real-time-temperature-humidity-azure.png)

> [!NOTE]
> 애플리케이션 예제가 사용자 장치에서 실행 중인지 확인합니다. 그렇지 않은 경우 빈 차트를 받습니다. [사용자 디바이스 설정](iot-hub-raspberry-pi-kit-node-get-started.md)에 있는 자습서를 참조할 수 있습니다.

## <a name="next-steps"></a>다음 단계

웹앱을 사용하여 IoT Hub에서 실시간 센서 데이터를 시각화했습니다.

Azure IoT Hub에서 데이터를 시각화하는 다른 방법은 [Power BI를 사용하여 IoT Hub에서 실시간 센서 데이터 시각화](iot-hub-live-data-visualization-in-power-bi.md)를 참조하세요.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]