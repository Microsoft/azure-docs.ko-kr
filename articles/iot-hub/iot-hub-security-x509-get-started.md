---
title: Azure IoT Hub의 X.509 보안을 위한 자습서 | Microsoft Docs
description: 시뮬레이션된 환경에서 Azure IoT Hub의 X.509 기반 보안을 시작합니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-csharp
ms.openlocfilehash: 1c1e29d43fefa6be1586aefc28249fe2f0f9e9fb
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001028"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Azure IoT Hub의 X.509 보안 설정

이 자습서에서는 *X.509 인증서 인증*을 사용 하 여 Azure IoT hub를 보호 하는 데 필요한 단계를 보여 줍니다. 이에 대 한 설명은 오픈 소스 도구인 OpenSSL를 사용 하 여 Windows 컴퓨터에 로컬로 인증서를 만듭니다. 이 자습서는 테스트용으로만 사용하는 것이 좋습니다. 프로덕션 환경의 경우 *루트 CA(인증 기관)* 에서 인증서를 구입해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 사용하려면 다음과 같은 리소스를 준비해야 합니다.

* Azure 구독으로 IoT Hub를 만들었습니다. 자세한 단계는 [포털을 통해 IoT Hub 만들기](iot-hub-create-through-portal.md)를 참조하세.

* [Visual studio 2017 또는 Visual studio 2019](https://www.visualstudio.com/vs/) 이 설치 되어 있습니다.

## <a name="get-x509-ca-certificates"></a>X.509 CA 인증서 얻기

Azure IoT Hub에서 X.509 인증서 기반 보안을 사용하려면 루트 인증서는 물론 리프 인증서까지 중간 인증서를 포함하는 [X.509 인증서 체인](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)을 시작해야 합니다.

다음 방법 중 하나를 선택 하 여 인증서를 가져올 수 있습니다.

* *루트 CA(인증 기관)* 에서 X.509 인증서를 구입합니다. 이 방법은 프로덕션 환경에 권장 됩니다.

* [OpenSSL](https://www.openssl.org/)와 같은 타사 도구를 사용 하 여 고유한 X. x.509 인증서를 만듭니다. 이 기법은 테스트 및 개발 목적으로 충분 합니다. PowerShell 또는 Bash를 사용하여 테스트 CA 인증서를 생성하는 방법에 대한 정보는 [샘플 및 자습서에 대한 테스트 CA 인증서 관리](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)를 참조합니다. 이 자습서의 나머지 부분에서는 [샘플 및 자습서에 대한 테스트 CA 인증서 관리](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) 지침에 따라 생성된 테스트 CA 인증서를 사용합니다.

* 기존 루트 CA 인증서로 서명 된 [x.509 중간 ca 인증서](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust) 를 생성 하 고 허브에 업로드 합니다. 중간 인증서를 업로드 하 고 확인 한 후 아래에 설명 된 대로 루트 CA 인증서 대신 아래에서 사용할 수 있습니다. OpenSSL ([OpenSSL 요청](https://www.openssl.org/docs/man1.1.0/man1/req.html) 및 [OpenSSL ca](https://www.openssl.org/docs/man1.1.0/man1/ca.html))와 같은 도구를 사용 하 여 중간 ca 인증서를 생성 하 고 서명할 수 있습니다.

> [!NOTE]
> 타사 루트는 다른 사용자가 자신의 장치를 사용자의 IoT Hub에 연결할 수 있으므로 사용자가 고유 하지 않은 경우 업로드 하지 마세요.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>IoT Hub에 X.509 CA 인증서 등록

이 단계는 포털을 통해 IoT Hub에 새 인증 기관을 추가하는 방법을 보여줍니다.

> [!NOTE]
> IoT hub에 등록할 수 있는 x.509 CA 인증서의 최대 수는 25 개입니다. 자세한 내용은 [Azure IoT Hub 할당량 및 제한](iot-hub-devguide-quotas-throttling.md)을 참조 하세요.

1. Azure Portal에서 IoT hub로 이동 하 고 **Settings**  >  허브에 대 한 설정**인증서** 를 선택 합니다.

1. **추가** 를 선택 하 여 새 인증서를 추가 합니다.

1. **인증서 이름**에 표시 이름을 입력 하 고 컴퓨터에서 이전 섹션에서 만든 인증서 파일을 선택 합니다.

1. 인증서가 성공적으로 업로드 되었음을 알리는 메시지가 표시 되 면 **저장**을 선택 합니다.

    ![인증서 업로드](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   인증서가 확인 되지 않음 상태로 인증서 목록에 표시 **됩니다.**

1. **인증서 세부 정보**를 표시 하기 위해 방금 추가한 인증서를 선택 하 고 **확인 코드 생성**을 선택 합니다.

   ![인증서 확인](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. **확인 코드** 를 클립보드에 복사 합니다. 인증서 소유권의 유효성을 검사 하는 데 사용 합니다.

1. [예제 및 자습서에 대 한 테스트 CA 인증서 관리](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)의 3 단계를 수행 합니다.  이 프로세스는 서명을 생성 하는 x.509 CA 인증서와 연결 된 개인 키를 사용 하 여 확인 코드에 서명 합니다. 이 서명 프로세스를 수행하는 데 사용할 수 있는 도구(예: OpenSSL)가 있습니다. 이 프로세스를 [소유 증명](https://tools.ietf.org/html/rfc5280#section-3.1)이라고 합니다.

1. **인증서 세부 정보**의 **확인 인증서. pem 또는 .cer 파일**에서 서명 파일을 찾아 엽니다. 그런 다음 **확인**을 선택 합니다.

   인증서의 상태가 **확인**됨으로 변경 됩니다. 인증서가 자동으로 업데이트 되지 않으면 **새로 고침** 을 선택 합니다.

## <a name="create-an-x509-device-for-your-iot-hub"></a>IoT Hub용 X.509 디바이스 만들기

1. Azure Portal에서 IoT hub로 이동한 다음 **탐색기**  >  **iot 장치**를 선택 합니다.

1. 새로 **만들기를 선택 하** 여 새 장치를 추가 합니다.

1. **장치 ID**에 표시 이름을 입력 합니다. **인증 유형**에 대해 **x.509 CA 서명**을 선택 하 고 **저장**을 선택 합니다.

   ![포털에서 X.509 디바이스 만들기](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>X.509 인증서로 X.509 디바이스 인증

X.509 디바이스를 인증하려면 먼저 CA 인증서로 디바이스에 서명해야 합니다. 리프 디바이스의 서명은 일반적으로 제조 공장에서 수행되며 제조 도구가 이에 따라 활성화됩니다. 장치가 한 제조업체에서 다른 제조업체에 들어가면 각 제조업체의 서명 작업은 체인 내에서 중간 인증서로 캡처됩니다. 그 결과 CA 인증서에서 장치의 리프 인증서로 인증서 체인이 생성 됩니다. [샘플 및 자습서에 대한 테스트 CA 인증서 관리](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)의 4단계는 디바이스 인증서를 생성합니다.

다음으로 IoT Hub에 등록된 X.509 디바이스를 시뮬레이트하는 C# 애플리케이션을 만드는 방법을 보여줍니다. 시뮬레이트된 디바이스에서 허브로 온도 및 습도 값을 전송합니다. 이 자습서에서는 장치 응용 프로그램만 만듭니다. 이 시뮬레이트된 디바이스에서 보낸 이벤트에 응답을 보낼 IoT Hub 서비스 애플리케이션을 작성하는 것은 독자가 연습하도록 남겨두었습니다. C# 애플리케이션은 [샘플 및 자습서에 대한 테스트 CA 인증서 관리](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)의 단계를 수행했다고 가정합니다.

1. Visual Studio를 열고 **새 프로젝트 만들기**를 선택한 다음 **콘솔 앱 (.NET Framework)** 프로젝트 템플릿을 선택 합니다. **다음**을 선택합니다.

1. **새 프로젝트 구성**에서 프로젝트 이름을 *SimulateX509Device*로 지정한 다음, **만들기**를 선택 합니다.

   ![Visual Studio에서 X.509 디바이스 프로젝트 만들기](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. 솔루션 탐색기에서 **SimulateX509Device** 프로젝트를 마우스 오른쪽 단추로 클릭 한 다음 **NuGet 패키지 관리**를 선택 합니다.

1. **NuGet 패키지 관리자**에서 **찾아보기** 를 선택 하 고을 검색 한 후을 (를) 검색 하 **고 선택 합니다.** **설치**를 선택합니다.

   ![Visual Studio에서 디바이스 SDK NuGet 패키지 추가](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    이 단계에서는 Azure IoT 디바이스 SDK NuGet 패키지 및 해당 종속 항목에 참조를 다운로드, 설치 및 추가합니다.

1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

    ```csharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. **Program** 클래스에 다음 필드를 추가 합니다.

    ```csharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    _<your_device_id>_ 대신 이전 섹션에서 사용한 친숙 한 장치 이름을 사용 합니다.

1. 다음 함수를 추가하여 온도 및 습도에 대한 임의의 숫자를 만들고 이 값을 허브에 보냅니다.

    ```csharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

1. 마지막으로 **Main** 함수에 다음 코드 줄을 추가 하 여 설치 프로그램에서 요구 하는 대로 자리 표시자 _장치 id_, _-iot 허브 이름_및 _절대 경로-.pfx-파일_ 을 바꿉니다.

    ```csharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

   이 코드는 X.509 디바이스에 대한 연결 문자열을 만들어서 IoT Hub에 연결합니다. 연결이 완료되면 온도 및 습도 이벤트를 허브에 보내고 응답을 기다립니다.

1. 앱을 실행합니다. 이 응용 프로그램은 *.pfx* 파일에 액세스 하므로이 앱을 관리자 권한으로 실행 해야 할 수 있습니다.

   1. Visual Studio 솔루션을 빌드합니다.

   1. **관리자 권한으로 실행**을 사용 하 여 새 명령 프롬프트 창을 엽니다.  

   1. 솔루션이 포함 된 폴더로 이동한 다음 솔루션 폴더 내에서 *bin/Debug* 경로로 이동 합니다.

   1. 명령 프롬프트에서 응용 프로그램 **SimulateX509Device.exe** 를 실행 합니다.

   디바이스가 허브에 성공적으로 연결되어 이벤트를 보내는 것이 표시되어야 합니다.

   ![디바이스 앱 실](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>다음 단계

IoT 솔루션 보안 유지에 대한 자세한 내용은 다음을 참조하세요.

* [IoT 보안 모범 사례](../iot-fundamentals/iot-security-best-practices.md)

* [IoT 보안 아키텍처](../iot-fundamentals/iot-security-architecture.md)

* [IoT 배포 보안 유지](../iot-fundamentals/iot-security-deployment.md)

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure IoT Edge를 사용하여 에지 디바이스에 AI 배포](../iot-edge/tutorial-simulate-device-linux.md)
