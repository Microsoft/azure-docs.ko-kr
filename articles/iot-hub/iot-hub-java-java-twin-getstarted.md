---
title: Azure IoT Hub 디바이스 쌍 시작(Java) | Microsoft Docs
description: Azure IoT Hub 디바이스 쌍을 사용하여 태그를 추가한 다음, IoT Hub 쿼리를 사용하는 방법입니다. Java용 Azure IoT 디바이스 SDK를 사용하여 디바이스 앱을 구현하고 Java용 Azure IoT 서비스 SDK를 사용하여 태그를 추가하고 IoT Hub 쿼리를 실행하는 서비스 앱을 구현합니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e0114c37b2204a7ad1d7b0cf9c7f336dcd85883a
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110498"
---
# <a name="get-started-with-device-twins-java"></a>디바이스 쌍 시작(Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

이 자습서에서는 다음 두 개의 Java 콘솔 앱을 만듭니다.

* **add-tags-query**, 태그를 추가하고 디바이스 쌍을 쿼리하는 Java 백 엔드 앱입니다.
* **simulated-device**, IoT Hub에 연결하고 reported 속성을 사용하여 해당 연결 조건을 보고하는 Java 디바이스 앱입니다.

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) 문서는 디바이스 및 백 엔드 앱을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 대한 정보를 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Java SE Development Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)입니다. JDK 8용 다운로드를 가져오려면 **장기 지원**에서 **Java 8**을 선택해야 합니다.

* [Maven 3](https://maven.apache.org/download.cgi)

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

* 방화벽에서 포트 8883가 열려 있는지 확인 합니다. 이 문서의 device 샘플에서는 포트 8883을 통해 통신 하는 MQTT 프로토콜을 사용 합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단 될 수 있습니다. 이 문제를 해결 하는 방법 및 방법에 대 한 자세한 내용은 [IoT Hub에 연결 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조 하세요.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT hub 연결 문자열을 가져옵니다.

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>서비스 응용 프로그램 만들기

이 섹션에서는 **myDeviceId**와 연결된 IoT Hub의 디바이스 쌍에 태그로 위치 메타데이터를 추가하는 Java 앱을 만듭니다. 먼저 앱은 미국에 있는 디바이스에 대한 IoT 허브를 쿼리한 다음 셀룰러 네트워크 연결을 보고하는 디바이스에 대한 IoT 허브를 쿼리합니다.

1. 개발 컴퓨터에서 **iot-$-** m a t-t-t i m 시작 이라는 빈 폴더를 만듭니다.

2. Iot-Maven **-getstarted** 폴더에서 명령 프롬프트에 다음 명령을 사용 하 여 **add tags-query** 라는 이름의 프로젝트를 만듭니다.

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. 명령 프롬프트에서 **추가 태그-쿼리** 폴더로 이동 합니다.

4. 텍스트 편집기를 사용 하 여 **추가 태그-쿼리** 폴더에서 **pom .xml** 파일을 열고 **종속성** 노드에 다음 종속성을 추가 합니다. 이러한 종속성을 통해 IoT Hub와 통신하도록 앱에서 **iot-service-client** 패키지를 사용할 수 있습니다.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > **Maven 검색**을 사용하여 [iot-service-client](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)의 최신 버전을 확인할 수 있습니다.

5. **종속성** 노드 뒤에 다음 **빌드** 노드를 추가합니다. 이 구성은 Maven에서 Java 1.8를 사용 하 여 앱을 빌드하도록 지시 합니다.

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. **pom.xml** 파일을 저장하고 닫습니다.

7. 텍스트 편집기를 사용 하 여 **add-tags-query\src\main\java\com\mycompany\app\App.java** 파일을 엽니다.

8. 파일에 다음 **import** 문을 추가합니다.

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. `{youriothubconnectionstring}`를 [iot hub 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 iot hub 연결 문자열로 바꿉니다.

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. 다음 **절을 포함하도록**main`throws` 메서드 서명을 업데이트합니다.

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. **Devicetwin** 및 **Devicetwindevice** 개체를 만들려면 **main** 메서드의 코드를 다음 코드로 바꿉니다. **DeviceTwin** 개체는 IoT 허브와의 통신을 처리합니다. **DeviceTwinDevice** 개체는 해당 속성 및 태그로 디바이스 쌍을 나타냅니다.

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. 다음 `try/catch` 블록을 **main** 메서드에 추가합니다.

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. 디바이스 쌍에서 **region** 및 **plant** 디바이스 쌍 태그를 업데이트하려면 `try` 블록에 다음 코드를 추가합니다.

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

14. IoT 허브에서 디바이스 쌍을 쿼리하려면 이전 단계에서 추가한 코드 뒤의 `try` 블록에 다음 코드를 추가합니다. 코드는 두 개의 쿼리를 실행합니다. 각 쿼리는 최대 100의 장치를 반환 합니다.

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

15. **Add-tags-query\src\main\java\com\mycompany\app\App.java** 파일을 저장 하 고 닫습니다.

16. **add-tags-query** 앱을 빌드하고 오류를 수정합니다. 명령 프롬프트에서 **태그-쿼리** 폴더로 이동한 후 다음 명령을 실행 합니다.

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>디바이스 앱 만들기

이 섹션에서는 IoT Hub로 전송되는 reported 속성 값을 설정하는 Java 콘솔 앱을 만듭니다.

1. Iot-Maven **-getstarted** 폴더에서 명령 프롬프트에 다음 명령을 사용 하 여 **시뮬레이션 된 장치** 라는 프로젝트를 만듭니다.

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 명령 프롬프트에서 **시뮬레이션 된 장치** 폴더로 이동 합니다.

3. 텍스트 편집기를 사용 하 여 **시뮬레이션 된 장치** 폴더에서 **pom .xml** 파일을 열고 **종속성** 노드에 다음 종속성을 추가 합니다. 이러한 종속성을 통해 앱의 **iot-장치 클라이언트** 패키지를 사용 하 여 iot hub와 통신할 수 있습니다.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > **Maven 검색**을 사용하여 [iot-device-client](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)의 최신 버전을 확인할 수 있습니다.

4. **종속성** 노드에 다음 종속성을 추가 합니다. 이 종속성은 장치 클라이언트 SDK에서 로깅을 구현 하는 데 사용 되는 Apache [SLF4J](https://www.slf4j.org/) 로깅 외관에 대해 NOP를 구성 합니다. 이 구성은 선택 사항 이지만, 생략 하면 앱을 실행할 때 콘솔에 경고가 표시 될 수 있습니다. 장치 클라이언트 SDK의 로그인에 대 한 자세한 내용은 *Java 용 Azure IoT 장치 SDK* 추가 정보 파일에 대 한 샘플의 [로깅](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) 을 참조 하세요.

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. **종속성** 노드 뒤에 다음 **빌드** 노드를 추가합니다. 이 구성에서는 Maven에 Java 1.8을 사용하여 앱을 빌드하도록 지시합니다.

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. **pom.xml** 파일을 저장하고 닫습니다.

7. 텍스트 편집기를 사용 하 여 **simulated-device\src\main\java\com\mycompany\app\App.java** 파일을 엽니다.

8. 파일에 다음 **import** 문을 추가합니다.

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. `{yourdeviceconnectionstring}`를 [IoT hub에서 새 장치 등록](#register-a-new-device-in-the-iot-hub)에서 복사한 장치 연결 문자열로 바꿉니다.

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    이 샘플 앱은 **DeviceClient** 개체를 인스턴스화할 때 **프로토콜** 변수를 사용합니다.

10. 쌍 업데이트에 대한 정보를 인쇄하려면 **App** 클래스에 다음 메서드를 추가합니다.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

11. **Main** 메서드의 코드를 다음 코드로 바꿉니다.

    * IoT Hub와 통신하는 디바이스 클라이언트를 만듭니다.

    * **Device** 개체를 만들어 디바이스 쌍 속성을 저장합니다.

    ```java
    DeviceClient client = new DeviceClient(connString, protocol);

    // Create a Device object to store the device twin properties
    Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
    };
    ```

12. 다음 코드를 **main** 메서드에 추가하여 **connectivityType** reported 속성을 만들고 IoT Hub로 보냅니다.

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

13. **main** 메서드의 끝에 다음 코드를 추가합니다. **Enter** 키를 대기 하면 장치 쌍 작업의 상태를 보고 하 IoT Hub 시간을 사용할 수 있습니다.

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

14. **main** 메서드의 서명을 수정하여 다음과 같은 예외를 포함합니다.

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

15. **Simulated-device\src\main\java\com\mycompany\app\App.java** 파일을 저장 하 고 닫습니다.

16. **simulated-device** 앱을 빌드하고 오류를 수정합니다. 명령 프롬프트에서 **시뮬레이션 된 장치** 폴더로 이동 하 고 다음 명령을 실행 합니다.

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>앱 실행

이제 콘솔 앱을 실행할 준비가 되었습니다.

1. **추가 태그-쿼리** 폴더의 명령 프롬프트에서 다음 명령을 실행 하 여 **태그 추가 쿼리** 서비스 앱을 실행 합니다.

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![태그 값을 업데이트하고 디바이스 쿼리를 실행하는 Java IoT Hub 서비스 앱](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    디바이스 쌍에 추가된 **plant** 및 **region** 태그를 볼 수 있습니다. 첫 번째 쿼리에서 디바이스를 반환하지만 두 번째는 그렇지 않습니다.

2. **시뮬레이션 된 장치** 폴더의 명령 프롬프트에서 다음 명령을 실행 하 여 **connectivityType** 보고 된 속성을 장치 쌍에 추가 합니다.

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![디바이스 클라이언트는 **connectivityType** reported 속성을 추가합니다.](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. **추가 태그-쿼리** 폴더의 명령 프롬프트에서 다음 명령을 실행 하 여 두 번째로 **추가 태그 쿼리** 서비스 앱을 실행 합니다.

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![태그 값을 업데이트하고 디바이스 쿼리를 실행하는 Java IoT Hub 서비스 앱](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    이제 장치가 **connectivityType** 속성을 IoT Hub로 보냈지만 두 번째 쿼리는 장치를 반환 합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Portal에서 새 IoT Hub를 구성한 다음, IoT Hub의 ID 레지스트리에서 디바이스 ID를 만들었습니다. 백 엔드 앱에서 태그로 디바이스 메타데이터를 추가하고, 디바이스 쌍에서 디바이스 연결 정보를 보고하는 디바이스 앱을 작성했습니다. 또한 SQL과 유사한 IoT Hub 쿼리 언어를 사용하여 디바이스 쌍 정보를 쿼리하는 방법도 살펴보았습니다.

아래와 같이 실행할 방법을 알아보려면 다음 리소스를 참조하세요.

* [IoT Hub 시작](quickstart-send-telemetry-java.md) 자습서를 참조하여 디바이스에서 원격 분석을 보냅니다.

* [직접 메서드 사용](quickstart-control-device-java.md) 자습서를 참조하여 대화형으로(예: 사용자 제어 앱에서 팬 작동) 디바이스를 제어합니다.
