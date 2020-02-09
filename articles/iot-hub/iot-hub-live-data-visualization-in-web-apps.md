---
title: 웹 앱에서 IoT hub 데이터의 실시간 데이터 시각화
description: 웹 응용 프로그램을 사용 하 여 센서에서 수집 하 여 Iot hub로 보낸 온도 및 습도 데이터를 시각화할 수 있습니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 6c7981d15acf2b2b71dfb4234f85b738efe62ce0
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76767958"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>웹 응용 프로그램에서 Azure IoT hub의 실시간 센서 데이터 시각화

![엔드투엔드 다이어그램](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>학습 내용

이 자습서에서는 로컬 컴퓨터에서 실행 되는 node.js 웹 앱을 사용 하 여 IoT hub에서 받는 실시간 센서 데이터를 시각화 하는 방법에 대해 알아봅니다. 웹 앱을 로컬로 실행 한 후에는 필요에 따라 Azure App Service에서 웹 앱을 호스트 하는 단계를 수행할 수 있습니다. Power BI를 사용하여 IoT Hub의 데이터를 시각화하려면 [Power BI를 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화](iot-hub-live-data-visualization-in-power-bi.md)를 참조하세요.

## <a name="what-you-do"></a>수행할 작업

* 웹 응용 프로그램에서 센서 데이터를 읽는 데 사용할 소비자 그룹을 IoT hub에 추가 합니다.
* GitHub에서 웹 앱 코드 다운로드
* 웹 앱 코드를 검사 합니다.
* 웹 앱에 필요한 IoT Hub 아티팩트를 보유 하도록 환경 변수 구성
* 개발 컴퓨터에서 웹 앱 실행
* 웹 페이지를 열어 IoT hub에서 실시간 온도 및 습도 데이터 확인
* 필드 Azure CLI를 사용 하 여 Azure App Service에서 웹 앱을 호스팅합니다.

## <a name="what-you-need"></a>필요한 항목

* [Raspberry Pi 온라인 시뮬레이터](iot-hub-raspberry-pi-web-simulator-get-started.md) 자습서 또는 장치 자습서 중 하나를 완료 합니다. 예를 들어 [node.js를 사용 하 여 Pi를 Raspberry](iot-hub-raspberry-pi-kit-node-get-started.md)합니다. 다음 요구 사항을 다룹니다.

  * 활성 Azure 구독
  * 구독 중인 IoT Hub
  * 메시지를 IoT Hub로 보내는 클라이언트 애플리케이션

* [Git 다운로드](https://www.git-scm.com/downloads)

* 이 문서의 단계에서는 Windows 개발 컴퓨터를 가정 합니다. 그러나 선호 하는 셸에서 Linux 시스템에서 이러한 단계를 쉽게 수행할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

다음 명령을 실행하여 Cloud Shell 인스턴스에 Azure CLI용 Microsoft Azure IoT 확장을 추가합니다. IOT 확장은 Azure CLI에 IoT Hub, IoT Edge 및 IoT DPS(Device Provisioning Service) 고유의 명령을 추가합니다.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT Hub에 소비자 그룹 추가

[소비자 그룹](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) 은 앱과 Azure 서비스가 동일한 이벤트 허브 엔드포인트의 데이터를 독립적으로 사용할 수 있도록 하는 이벤트 스트림에 독립적인 뷰를 제공 합니다. 이 섹션에서는 웹 앱에서 데이터를 읽는 데 사용 하는 IoT hub의 기본 제공 엔드포인트에 소비자 그룹을 추가 합니다.

다음 명령을 실행 하 여 IoT hub의 기본 제공 엔드포인트에 소비자 그룹을 추가 합니다.

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

선택한 이름을 적어 둡니다 .이 자습서의 뒷부분에서 필요 합니다.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>IoT hub에 대 한 서비스 연결 문자열 가져오기

IoT hub는 몇 가지 기본 액세스 정책을 사용 하 여 생성 됩니다. 이러한 정책 중 하나는 서비스에서 IoT hub의 엔드포인트을 읽고 쓸 수 있는 충분 한 권한을 제공 하는 **서비스** 정책입니다. 다음 명령을 실행 하 여 서비스 정책을 준수 하는 IoT hub에 대 한 연결 문자열을 가져옵니다.

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

연결 문자열은 다음과 같이 표시 됩니다.

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

서비스 연결 문자열을 적어 둡니다 .이 자습서의 뒷부분에서 필요 합니다.

## <a name="download-the-web-app-from-github"></a>GitHub에서 웹 앱 다운로드

명령 창을 열고 다음 명령을 입력 하 여 GitHub에서 샘플을 다운로드 하 고 샘플 디렉터리로 변경 합니다.

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>웹 앱 코드를 검사 합니다.

웹 앱-iot-허브-데이터 시각화 디렉터리에서 즐겨 사용 하는 편집기에서 웹 앱을 엽니다. 다음은 VS Code에서 볼 파일 구조를 보여 줍니다.

![웹 앱 파일 구조](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

잠시 후 다음 파일을 살펴보겠습니다.

* **Node.js** 는 웹 소켓과 이벤트 허브 래퍼 클래스를 초기화 하는 서비스 쪽 스크립트입니다. 클래스가 들어오는 메시지를 웹 소켓에 브로드캐스트하는 데 사용 하는 이벤트 허브 래퍼 클래스에 대 한 콜백을 제공 합니다.

* **Event-hub-reader** 는 지정 된 연결 문자열 및 소비자 그룹을 사용 하 여 IoT hub의 기본 제공 엔드포인트에 연결 하는 서비스 쪽 스크립트입니다. 들어오는 메시지에 대 한 메타 데이터에서 DeviceId 및 EnqueuedTimeUtc를 추출한 다음 node.js에서 등록 한 콜백 메서드를 사용 하 여 메시지를 릴레이 합니다.

* **Chart-device-data** 는 웹 소켓에서 수신 대기 하 고, 각 DeviceId를 추적 하 고, 각 장치에 대해 들어오는 데이터의 마지막 50 위치를 저장 하는 클라이언트 쪽 스크립트입니다. 그런 다음 선택한 장치 데이터를 차트 개체에 바인딩합니다.

* **Index. html** 은 웹 페이지에 대 한 UI 레이아웃을 처리 하 고 클라이언트 쪽 논리에 필요한 스크립트를 참조 합니다.

## <a name="configure-environment-variables-for-the-web-app"></a>웹 앱에 대 한 환경 변수 구성

IoT hub에서 데이터를 읽으려면 웹 앱은 IoT hub의 연결 문자열 및 읽을 소비자 그룹의 이름을 요구 합니다. Node.js의 다음 줄에 있는 프로세스 환경에서 이러한 문자열을 가져옵니다.

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

명령 창에서 다음 명령을 사용 하 여 환경 변수를 설정 합니다. 자리 표시자 값을 IoT hub의 서비스 연결 문자열 및 이전에 만든 소비자 그룹의 이름으로 바꿉니다. 문자열을 인용 하지 않습니다.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>웹앱 실행

1. 장치가 실행 중이 고 데이터를 보내고 있는지 확인 합니다.

2. 명령 창에서 다음 줄을 실행 하 여 참조 된 패키지를 다운로드 하 고 설치 하 고 웹 사이트를 시작 합니다.

   ```cmd
   npm install
   npm start
   ```

3. 웹 앱이 IoT hub에 성공적으로 연결 되 고 포트 3000에서 수신 대기 중임을 나타내는 출력이 콘솔에 표시 됩니다.

   ![콘솔에서 시작 되는 웹 앱](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>웹 페이지를 열어 IoT hub의 데이터 보기

`http://localhost:3000`브라우저를 엽니다.

장치 **선택** 목록에서 장치를 선택 하 여 장치에서 IoT hub로 보낸 최근 50 온도 및 습도 데이터 지점의 실행 플롯을 표시 합니다.

![실시간 온도 및 습도를 보여 주는 웹앱 페이지](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

또한 콘솔에 웹 앱이 브라우저 클라이언트에 브로드캐스팅하는 메시지를 표시 하는 출력이 표시 됩니다.  

![콘솔의 웹 앱 브로드캐스트 출력](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>App Service에서 웹 앱 호스트

[Azure App Service의 Web Apps 기능은](https://docs.microsoft.com/azure/app-service/overview) 웹 응용 프로그램 호스팅을 위한 PAAS (platform as a Service)를 제공 합니다. Azure App Service에서 호스트 되는 웹 응용 프로그램은 추가 보안, 부하 분산 및 확장성과 같은 강력한 Azure 기능과 연속 배포, 패키지 관리 등과 같은 Azure 및 파트너 DevOps 솔루션의 이점을 누릴 수 있습니다. Azure App Service는 널리 사용 되는 여러 언어로 개발 된 웹 응용 프로그램을 지원 하 고 Windows 또는 Linux 인프라에 배포 됩니다.

이 섹션에서는 App Service에서 웹 앱을 프로 비전 하 고 Azure CLI 명령을 사용 하 여 코드를 배포 합니다. [Az webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) 설명서에 사용 되는 명령에 대 한 세부 정보를 찾을 수 있습니다. 시작 하기 전에 [iot hub에 리소스 그룹을 추가](#add-a-consumer-group-to-your-iot-hub)하 고, [iot hub에 대 한 서비스 연결 문자열을 가져오고](#get-a-service-connection-string-for-your-iot-hub), [GitHub에서 웹 앱을 다운로드](#download-the-web-app-from-github)하는 단계를 완료 했는지 확인 합니다.

1. [App Service 계획](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) 은 실행 App Service에서 호스팅되는 앱에 대 한 계산 리소스 집합을 정의 합니다. 이 자습서에서는 개발자/무료 계층을 사용 하 여 웹 앱을 호스팅합니다. 무료 계층을 사용 하 여 웹 앱은 다른 고객의 앱을 포함 하 여 다른 App Service 앱과 공유 된 Windows 리소스에서 실행 됩니다. 또한 Azure는 Linux 계산 리소스에 웹 앱을 배포 하는 App Service 계획을 제공 합니다. 사용 하려는 App Service 계획이 이미 있는 경우이 단계를 건너뛸 수 있습니다.

   Windows 무료 계층을 사용 하 여 App Service 계획을 만들려면 다음 명령을 실행 합니다. IoT hub가 있는 동일한 리소스 그룹을 사용 합니다. 서비스 계획 이름에는 대 문자와 소문자, 숫자 및 하이픈을 포함할 수 있습니다.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. 이제 App Service 계획에서 웹 앱을 프로 비전 합니다. `--deployment-local-git` 매개 변수를 사용 하면 로컬 컴퓨터의 Git 리포지토리에서 웹 앱 코드를 업로드 하 고 배포할 수 있습니다. 웹 앱 이름은 전역적으로 고유 해야 하며 대/소문자, 숫자 및 하이픈을 포함할 수 있습니다. 사용 중인 node.js 런타임의 버전에 따라 `--runtime` 매개 변수에 대해 노드 버전 10.6 이상을 지정 해야 합니다. `az webapp list-runtimes` 명령을 사용 하 여 지원 되는 런타임 목록을 가져올 수 있습니다.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. 이제 IoT hub 연결 문자열 및 이벤트 허브 소비자 그룹을 지정 하는 환경 변수에 대 한 응용 프로그램 설정을 추가 합니다. 개별 설정은 `-settings` 매개 변수에서 구분 된 공간입니다. IoT hub에 대 한 서비스 연결 문자열과이 자습서의 앞부분에서 만든 소비자 그룹을 사용 합니다. 값을 따옴표로 묶으면 안 됩니다.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. 웹 앱에 웹 소켓 프로토콜을 사용 하도록 설정 하 고 HTTPS 요청만 받도록 웹 앱을 설정 합니다 (HTTP 요청은 HTTPS로 리디렉션 됨).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. App Service에 코드를 배포 하려면 [사용자 수준 배포 자격 증명](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)을 사용 합니다. 사용자 수준 배포 자격 증명은 Azure 자격 증명과 다르며 웹 앱에 대 한 Git 로컬 및 FTP 배포에 사용 됩니다. 설정 되 면 Azure 계정의 모든 구독에 있는 모든 App Service 앱에서 유효 합니다. 사용자 수준 배포 자격 증명을 이전에 설정한 경우 해당 자격 증명을 사용할 수 있습니다.

   이전에 사용자 수준 배포 자격 증명을 설정 하지 않았거나 암호를 기억할 수 없는 경우 다음 명령을 실행 합니다. 배포 사용자 이름은 Azure 내에서 고유 해야 하며 로컬 Git 푸시에 대 한 ' @ ' 기호를 포함 하지 않아야 합니다. 메시지가 표시 되 면를 입력 하 고 새 암호를 확인 합니다. 암호는 글자, 숫자, 기호의 세 가지 요소 중 두 가지를 사용하고 8자 이상이어야 합니다.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. 코드를 App Service까지 푸시하는 데 사용할 Git URL을 가져옵니다.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. App Service에서 웹 앱에 대 한 Git 리포지토리를 참조 하는 복제에 원격을 추가 합니다. \<Git 클론 URL\>의 경우 이전 단계에서 반환 된 URL을 사용 합니다. 명령 창에서 다음 명령을 실행 합니다.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. App Service에 코드를 배포 하려면 명령 창에 다음 명령을 입력 합니다. 자격 증명을 입력 하 라는 메시지가 표시 되 면 5 단계에서 만든 사용자 수준 배포 자격 증명을 입력 합니다. App Service 원격의 master 분기로 푸시 하는지 확인 합니다.

    ```cmd
    git push webapp master:master
    ```

9. 배포 진행률이 명령 창에 업데이트 됩니다. 성공적으로 배포 되 면 다음 출력과 유사한 줄로 끝납니다.

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. 다음 명령을 실행 하 여 웹 앱의 상태를 쿼리하고 실행 중인지 확인 합니다.

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. 브라우저에서 `https://<your web app name>.azurewebsites.net` 으로 이동합니다. 웹 앱을 로컬로 표시할 때 표시 되는 것과 비슷한 웹 페이지입니다. 장치가 실행 중이 고 데이터를 전송 하는 경우 장치에서 보낸 50 가장 최근 온도 및 습도 판독값의 실행 플롯이 표시 되어야 합니다.

## <a name="troubleshooting"></a>문제 해결

이 샘플에 대 한 모든 문제를 해결 하려면 다음 섹션의 단계를 수행 합니다. 문제가 계속 되 면이 항목의 맨 아래에 의견을 보내 주시기 바랍니다.

### <a name="client-issues"></a>클라이언트 문제

* 목록에 장치가 표시 되지 않거나 그래프가 그려지지 않는 경우 장치에서 장치 코드가 실행 되 고 있는지 확인 합니다.

* 브라우저에서 개발자 도구를 열고 (대부분의 브라우저에서 F12 키를 열고) 콘솔을 찾습니다. 표시 된 경고 또는 오류를 확인 합니다.

* /Js/chat-device-data.js.에서 클라이언트 쪽 스크립트를 디버그할 수 있습니다.

### <a name="local-website-issues"></a>로컬 웹 사이트 문제

* 콘솔 출력을 위해 노드를 시작한 창에서 출력을 봅니다.

* 서버 코드, 특히 node.js 및/scripts/event-hub-reader.js.를 디버깅 합니다.

### <a name="azure-app-service-issues"></a>Azure App Service 문제

* Azure Portal에서 웹 앱으로 이동 합니다. 왼쪽 창의 **모니터링** 아래에서 **App Service 로그**를 선택 합니다. **응용 프로그램 로깅 (파일 시스템)** 을 켜기로 설정 하 고 **수준** 을 오류로 설정한 다음 **저장**을 선택 합니다. 그런 다음 **모니터링**중인 **로그 스트림을** 엽니다.

* Azure Portal의 웹 앱에서 **개발 도구** 아래에 있는 **콘솔** 을 선택 하 고 `node -v` 및 `npm -v`를 사용 하 여 노드 및 npm 버전의 유효성을 검사 합니다.

* 패키지를 찾을 수 없다는 오류가 표시 되 면 순서 대로 단계를 실행 했을 수 있습니다. 사이트가 배포 될 때 (`git push`) app service는 구성 된 현재 노드 버전에 따라 실행 되는 `npm install`를 실행 합니다. 나중에 구성에서 변경 된 경우에는 코드에 대 한 의미 없는 변경을 수행 하 고 다시 푸시 해야 합니다.

## <a name="next-steps"></a>다음 단계

웹앱을 사용하여 IoT Hub에서 실시간 센서 데이터를 시각화했습니다.

Azure IoT Hub에서 데이터를 시각화 하는 다른 방법은 [Power BI를 사용 하 여 IoT Hub에서 실시간 센서 데이터 시각화](iot-hub-live-data-visualization-in-power-bi.md)를 참조 하세요.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
