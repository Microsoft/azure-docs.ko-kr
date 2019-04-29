---
title: Azure IoT Hub의 X.509 보안을 위한 자습서 | Microsoft Docs
description: 시뮬레이션된 환경에서 Azure IoT Hub의 X.509 기반 보안을 시작합니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.openlocfilehash: 5795cde35d53a64620c4fdb6c3af99a7f56b12d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440818"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Azure IoT Hub의 X.509 보안 설정

이 자습서는 *X.509 인증서 인증*을 사용하여 Azure IoT Hub를 보호하는 데 필요한 단계를 시뮬레이트합니다. 예시를 위 오픈 소스 도구인 OpenSSL을 사용하여 Windows 시스템에서 로컬 인증서를 만드는 방법을 보여줍니다. 이 자습서는 테스트용으로만 사용하는 것이 좋습니다. 프로덕션 환경의 경우 *루트 CA(인증 기관)* 에서 인증서를 구입해야 합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 사용하려면 다음과 같은 리소스를 준비해야 합니다.

* Azure 구독으로 IoT Hub를 만들었습니다. 자세한 단계는 [포털을 통해 IoT Hub 만들기](iot-hub-create-through-portal.md)를 참조하세.

* 있다고 [Visual Studio 2017 또는 Visual Studio 2019](https://www.visualstudio.com/vs/) 컴퓨터에 설치 합니다.

## <a name="get-x509-ca-certificates"></a>X.509 CA 인증서 얻기

IoT Hub에서 X.509 인증서 기반 보안을 사용하려면 루트 인증서는 물론 리프 인증서까지 중간 인증서를 포함하는 [X.509 인증서 체인](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)을 시작해야 합니다.

다음 중 한 가지 방법으로 인증서를 얻을 수 있습니다.

* *루트 CA(인증 기관)* 에서 X.509 인증서를 구입합니다. 이 방법은 프로덕션 환경에 권장됩니다.

* [OpenSSL](https://www.openssl.org/)과 같은 타사 도구를 사용하여 자체적인 X.509 인증서를 만듭니다. 이 방법은 테스트 및 개발 목적으로 사용할 수 있습니다. PowerShell 또는 Bash를 사용하여 테스트 CA 인증서를 생성하는 방법에 대한 정보는 [샘플 및 자습서에 대한 테스트 CA 인증서 관리](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)를 참조합니다. 이 자습서의 나머지 부분에서는 [샘플 및 자습서에 대한 테스트 CA 인증서 관리](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) 지침에 따라 생성된 테스트 CA 인증서를 사용합니다.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>IoT Hub에 X.509 CA 인증서 등록

이 단계는 포털을 통해 IoT Hub에 새 인증 기관을 추가하는 방법을 보여줍니다.

1. Azure Portal에서 IoT Hub로 이동하여 **설정** > **인증서** 메뉴를 엽니다.

2. **추가**를 클릭하여 새 인증서를 추가합니다.

3. 인증서의 표시 이름을 입력합니다. 이전 섹션에서 만든 *RootCA.cer*이라는 루트 인증서 파일을 컴퓨터에서 선택합니다. **업로드**를 클릭합니다.

4. 인증서가 업로드가 완료되었다는 알림을 받으면 **저장**을 클릭합니다.

    ![인증서 업로드](./media/iot-hub-security-x509-get-started/add-new-cert.png)  

   인증서가 **인증서 탐색기** 목록에 표시됩니다. 이 인증서의 **상태**는 *확인되지 않음*입니다.

5. 이전 단계에서 추가한 인증서를 클릭합니다.

6. **인증서 세부 정보** 블레이드에서 **확인 코드 생성**을 클릭합니다.

7. 인증서 소유권을 확인할 **확인 코드**가 만들어집니다. 코드를 클립보드에 복사합니다.

   ![인증서 확인](./media/iot-hub-security-x509-get-started/verify-cert.png)  

8. 이제 서명을 생성하는 X.509 CA 인증서와 연결된 개인 키로 이 *확인 코드*에 서명해야 합니다. 이 서명 프로세스를 수행하는 데 사용할 수 있는 도구(예: OpenSSL)가 있습니다. 이것을 [소유 증명](https://tools.ietf.org/html/rfc5280#section-3.1)이라고 합니다. [샘플 및 자습서에 대한 테스트 CA 인증서 관리](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)의 3단계는 확인 코드를 생성합니다.

9. 위의 8단계에서 생성된 서명을 포털의 IoT Hub에 업로드합니다. Azure Portal의 **인증서 세부 정보** 블레이드에서 **확인 인증서 .pem 또는 .cer 파일입니다.** 로 이동하여 서명을 선택합니다. 예를 들어 옆에 있는 _파일 탐색기_ 아이콘을 사용하여 샘플 PowerShell 명령으로 생성된 *VerifyCert4.cer*을 선택합니다.

10. 인증서 업로드가 완료되면 **확인**을 클릭합니다. **인증서** 블레이드에서 인증서의 **상태**가 **_확인됨_** 으로 변경됩니다. 자동으로 업데이트되지 않으면 **새로 고침**을 클릭하십시오.

    ![인증서 업로드 확인](./media/iot-hub-security-x509-get-started/upload-cert-verification.png)  

## <a name="create-an-x509-device-for-your-iot-hub"></a>IoT Hub용 X.509 디바이스 만들기

1. Azure Portal에서 IoT Hub의 **탐색기 > IoT 장치**로 이동합니다.

2. **+ 추가**를 클릭하여 새 장치를 추가합니다.

3. **디바이스 ID**에 대한 표시 이름을 입력하고 **인증 유형**으로 **_X.509 CA Signed_** 를 선택합니다. **저장**을 클릭합니다.

   ![포털에서 X.509 디바이스 만들기](./media/iot-hub-security-x509-get-started/create-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>X.509 인증서로 X.509 디바이스 인증

X.509 디바이스를 인증하려면 먼저 CA 인증서로 디바이스에 서명해야 합니다. 리프 디바이스의 서명은 일반적으로 제조 공장에서 수행되며 제조 도구가 이에 따라 활성화됩니다. 디바이스가 한 제조업체에서 다른 제조업체로 이동함에 따라 각 제조업체의 서명 작업은 체인 내에서 중간 인증서로 캡처됩니다. 최종 결과는 CA 인증서에서 디바이스의 리프 인증서까지의 인증서 체인입니다. [샘플 및 자습서에 대한 테스트 CA 인증서 관리](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)의 4단계는 디바이스 인증서를 생성합니다.

다음으로 IoT Hub에 등록된 X.509 장치를 시뮬레이트하는 C# 애플리케이션을 만드는 방법을 보여줍니다. 시뮬레이트된 디바이스에서 허브로 온도 및 습도 값을 전송합니다. 이 자습서에서는 장치 애플리케이션만 만듭니다. 이 시뮬레이트된 장치에서 보낸 이벤트에 응답을 보낼 IoT Hub 서비스 애플리케이션을 작성하는 것은 독자가 연습하도록 남겨두었습니다. C# 애플리케이션은 [샘플 및 자습서에 대한 테스트 CA 인증서 관리](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)의 단계를 수행했다고 가정합니다.

1. Visual Studio에서 콘솔 애플리케이션 프로젝트 템플릿을 사용하여 새 Visual C# Windows 클래식 데스크톱 프로젝트를 만듭니다. 프로젝트의 이름을 **SimulateX509Device**로 지정합니다.

   ![Visual Studio에서 X.509 장치 프로젝트 만들기](./media/iot-hub-security-x509-get-started/create-device-project.png)

2. 솔루션 탐색기에서 **SimulateX509Device** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리...** 를 클릭합니다. NuGet 패키지 관리자 창에서 **찾아보기**를 선택하고 **microsoft.azure.devices.client**를 검색합니다. **설치**를 선택하여 **Microsoft.Azure.Devices.Client** 패키지를 설치한 후 사용 약관에 동의합니다. 이 프로시저에서는 Azure IoT 디바이스 SDK NuGet 패키지 및 해당 종속성에 대한 참조를 다운로드, 설치 및 추가합니다.

   ![Visual Studio에서 장치 SDK NuGet 패키지 추가](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

3. *Program.cs* 파일의 맨 위에 다음 코드 줄을 추가합니다.

    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

4. **Program** 클래스 내부에 다음 코드 줄 을 추가합니다.

    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

     _&lt;your_device_id&gt;_ 자리 표시자 대신 앞 섹션에서 사용한 디바이스 이름을 사용하십시오.

5. 다음 함수를 추가하여 온도 및 습도에 대한 임의의 숫자를 만들고 이 값을 허브에 보냅니다.

    ```CSharp
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

6. 마지막으로 다음 코드 줄을 **Main** 함수에 추가하여 설정에서 필요에 따라 _device-id_, _your-iot-hub-name_ 및 _absolute-path-to-your-device-pfx-file_ 자리 표시자를 바꿉니다.

    ```CSharp
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
7. 이 애플리케이션은 *.pfx* 파일에 액세스하므로 *관리자* 모드에서 실행해야 할 수 있습니다. Visual Studio 솔루션을 빌드합니다. **관리자**로 새 명령 창을 열고 이 솔루션이 포함된 폴더로 이동합니다. 솔루션 폴더 내의 *bin/Debug* 경로로 이동합니다. _관리자_ 명령 창에서 **SimulateX509Device.exe** 애플리케이션을 실행합니다. 디바이스가 허브에 성공적으로 연결되어 이벤트를 보내는 것이 표시되어야 합니다. 

   ![장치 앱 실행](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>다음 단계

IoT 솔루션 보안 유지에 대한 자세한 내용은 다음을 참조하세요.

* [IoT 보안 모범 사례](../iot-fundamentals/iot-security-best-practices.md)

* [IoT 보안 아키텍처](../iot-fundamentals/iot-security-architecture.md)

* [IoT 배포 보안 유지](../iot-fundamentals/iot-security-deployment.md)

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure IoT Edge를 사용하여 에지 디바이스에 AI 배포](../iot-edge/tutorial-simulate-device-linux.md)
