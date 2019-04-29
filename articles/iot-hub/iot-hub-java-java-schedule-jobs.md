---
title: Azure IoT Hub(Java)를 사용하여 작업 예약 | Microsoft 문서
description: 여러 디바이스에서 직접 메서드를 호출하여 Azure IoT Hub 작업을 예약하고 원하는 속성을 설정하는 방법을 설명합니다. Java용 Azure IoT 디바이스 SDK를 사용하여 시뮬레이션된 디바이스 앱을 구현하고 Java용 Azure IoT 서비스 SDK를 사용하여 작업을 실행하는 서비스 앱을 구현합니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/10/2017
ms.openlocfilehash: ce7c70eef2d030a956ca5cc1ea85aff008074edb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60863955"
---
# <a name="schedule-and-broadcast-jobs-java"></a>작업 예약 및 브로드캐스트(Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub를 사용하여 수백만 대의 디바이스를 업데이트하는 작업을 예약하고 추적합니다. 작업을 사용하여 다음을 수행합니다.

* desired 속성 업데이트
* tags 업데이트
* 직접 메서드 호출

작업(job)은 이러한 작업(action) 중 하나를 래핑하고 디바이스 집합에 대한 실행을 추적합니다. 디바이스 쌍 쿼리에서는 작업 실행 대상인 디바이스 집합을 정의합니다. 예를 들어 백 엔드 앱은 작업(job)을 사용하여 10,000대 디바이스에 대해 디바이스를 재부팅하는 직접 메서드를 호출할 수 있습니다. 디바이스 쌍 쿼리로 디바이스 집합을 지정하고 향후 실행될 작업(job)을 예약합니다. 작업(job)은 해당하는 각 디바이스에서 재부팅 직접 메서드를 수신 및 실행할 때 진행 상태를 추적합니다.

이러한 각 기능에 대한 자세한 내용은 다음을 참조하세요.

* 디바이스 쌍 및 속성: [쌍 디바이스 시작](iot-hub-java-java-twin-getstarted.md)

* 직접 메서드: [IoT Hub 개발자 가이드-직접 메서드](iot-hub-devguide-direct-methods.md) 고 [자습서: 직접 메서드 사용](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* **lockDoor**라는 직접 메서드를 구현하는 디바이스 앱을 만듭니다. 또한 이 디바이스 앱은 백 엔드 앱에서 원하는 속성 변경 내용을 수신합니다.

* 여러 디바이스에 대해 **lockDoor** 직접 메서드를 호출하는 작업을 만드는 백 엔드 앱을 만듭니다. 다른 작업이 여러 디바이스로 원하는 속성 업데이트를 보냅니다.

이 자습서를 마치면 다음과 같은 java 콘솔 디바이스 앱과 java 콘솔 백 엔드 앱이 생성됩니다.

**simulated-device**: IoT Hub에 연결되고, **lockDoor** 직접 메서드를 구현하고, 원하는 속성 변경 내용을 처리합니다.

**schedule-jobs** 작업을 사용하여 **lockDoor** 직접 메서드를 호출하고 여러 디바이스에서 디바이스 쌍의 원하는 속성을 업데이트합니다.

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) 문서는 디바이스 및 백 엔드 앱을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 대한 정보를 제공합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 최신 [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>IoT Hub에 대한 연결 문자열 검색

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[Azure CLI용 IoT 확장](https://github.com/Azure/azure-iot-cli-extension) 도구를 사용하여 IoT Hub에 디바이스를 추가할 수도 있습니다.

## <a name="create-the-service-app"></a>서비스 응용 프로그램 만들기

이 섹션에서는 작업을 통해 다음을 수행하는 Java 콘솔 앱을 만듭니다.

* 여러 디바이스에서 **lockDoor** 직접 메서드 호출

* 여러 디바이스로 원하는 속성 전송

앱을 만들려면 다음을 수행합니다.

1. 개발 컴퓨터에서 `iot-java-schedule-jobs`라는 빈 폴더를 만듭니다.

2. `iot-java-schedule-jobs` 폴더에서 명령 프롬프트를 통해 다음 명령을 사용하여 **schedule-jobs**라는 Maven 프로젝트를 만듭니다. 긴 단일 명령입니다.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

3. 명령 프롬프트에서 `schedule-jobs` 폴더로 이동합니다.

4. 텍스트 편집기를 사용하여 `schedule-jobs` 폴더에서 `pom.xml` 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이러한 종속성을 통해 IoT Hub와 통신하도록 앱에서 **iot-service-client** 패키지를 사용할 수 있습니다.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 검색](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)을 사용하여 **iot-service-client**의 최신 버전을 확인할 수 있습니다.

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

6. `pom.xml` 파일을 저장하고 닫습니다.

7. 텍스트 편집기를 사용하여 `schedule-jobs\src\main\java\com\mycompany\app\App.java` 파일을 엽니다.

8. 파일에 다음 **import** 문을 추가합니다.

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

9. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. `{youriothubconnectionstring}`은 *IoT Hub 만들기* 섹션에서 기록한 IoT Hub 연결 문자열로 바꿉니다.

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. **App** 클래스에 다음 메서드를 추가하여 디바이스 쌍에서 원하는 속성 **Building** 및 **Floor**를 업데이트하는 작업을 예약합니다.

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

11. **lockDoor** 메서드를 호출하는 작업을 예약하려면 **App** 클래스에 다음 메서드를 추가합니다.

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

12. 작업을 모니터링하려면 **App** 클래스에 다음 메서드를 추가합니다.

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

13. 실행한 작업의 세부 정보를 쿼리하려면 다음 메서드를 추가합니다.

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

14. 다음 `throws` 절을 포함하도록 **main** 메서드 서명을 업데이트합니다.

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. 두 작업을 순서대로 실행하고 모니터링하려면 **main** 메서드에 다음 코드를 추가합니다.

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

16. `schedule-jobs\src\main\java\com\mycompany\app\App.java` 파일을 저장하고 닫습니다.

17. **schedule-jobs** 앱을 빌드하고 오류를 수정합니다. 명령 프롬프트에서 `schedule-jobs` 폴더로 이동하고 다음 명령을 실행합니다.

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>디바이스 앱 만들기

이 섹션에서는 IoT Hub에서 전송한 원하는 속성을 처리하고 직접 메서드 호출을 구현하는 Java 콘솔 앱을 만듭니다.

1. 명령 프롬프트에서 다음 명령을 사용하여 `iot-java-schedule-jobs` 폴더에 **simulated-device**라는 Maven 프로젝트를 만듭니다. 긴 단일 명령입니다.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

2. 명령 프롬프트에서 `simulated-device` 폴더로 이동합니다.

3. 텍스트 편집기를 사용하여 `simulated-device` 폴더에서 `pom.xml` 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이러한 종속성을 통해 IoT 허브와 통신하도록 앱에서 **iot-device-client** 패키지를 사용할 수 있습니다.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 검색](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)을 사용하여 **iot-device-client**의 최신 버전을 확인할 수 있습니다.

4. **종속성** 노드 뒤에 다음 **빌드** 노드를 추가합니다. 이 구성에서는 Maven에 Java 1.8을 사용하여 앱을 빌드하도록 지시합니다.

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

5. `pom.xml` 파일을 저장하고 닫습니다.

6. 텍스트 편집기를 사용하여 `simulated-device\src\main\java\com\mycompany\app\App.java` 파일을 엽니다.

7. 파일에 다음 **import** 문을 추가합니다.

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

8. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. `{youriothubname}`을 IoT 허브 이름으로 바꾸고 `{yourdevicekey}`를 *디바이스 ID 만들기* 섹션에서 만든 디바이스 키 값으로 바꿉니다.

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    이 샘플 앱은 **DeviceClient** 개체를 인스턴스화할 때 **프로토콜** 변수를 사용합니다.

9. 콘솔에 디바이스 쌍 알림을 인쇄하려면 다음 중첩 클래스를 **App** 클래스에 추가합니다.

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

10. 콘솔에 직접 메서드 알림을 인쇄하려면 다음 중첩 클래스를 **App** 클래스에 추가합니다.

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

11. IoT Hub로부터의 직접 메서드 호출을 처리하려면 다음 중첩 클래스를 **App** 클래스에 추가합니다.

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

12. 다음 `throws` 절을 포함하도록 **main** 메서드 서명을 업데이트합니다.

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

13. **main** 메서드에 다음 코드를 추가합니다.
    * IoT Hub와 통신하는 디바이스 클라이언트를 만듭니다.
    * **Device** 개체를 만들어 디바이스 쌍 속성을 저장합니다.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

14. 디바이스 클라이언트 서비스를 시작하려면 다음 코드를 **main** 메서드에 추가합니다.

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

15. 종료하기 전에 사용자가 **Enter** 키를 누를 때까지 대기하려면 다음 코드를 **main** 메서드 끝에 추가합니다.

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

16. `simulated-device\src\main\java\com\mycompany\app\App.java` 파일을 저장하고 닫습니다.

17. **simulated-device** 앱을 빌드하고 오류를 수정합니다. 명령 프롬프트에서 `simulated-device` 폴더로 이동하고 다음 명령을 실행합니다.

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>앱 실행

이제 콘솔 앱을 실행할 준비가 되었습니다.

1. 명령 프롬프트의 `simulated-device` 폴더에서 다음 명령을 실행하여 원하는 속성 변경 내용과 직접 메서드 호출을 수신 대기하도록 디바이스 앱을 시작합니다.

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![디바이스 클라이언트 시작](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. 명령 프롬프트의 `schedule-jobs` 폴더에서 다음 명령을 실행하여 **schedule-jobs** 서비스 앱을 실행해 두 작업을 실행합니다. 첫 번째 작업에서는 원하는 속성 값을 설정하고 두 번째 작업에서는 직접 메서드를 호출합니다.

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT Hub 서비스 앱에서 두 개의 작업을 작성함](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. 디바이스 앱이 원하는 속성 변경 및 직접 메서드 호출을 처리합니다.

    ![디바이스 클라이언트에 변경 내용에 응답함](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Portal에서 새 IoT Hub를 구성한 다음, IoT Hub의 ID 레지스트리에서 디바이스 ID를 만들었습니다. 그리고 두 작업을 실행하는 백 엔드 앱을 만들었습니다. 첫 번째 작업은 원하는 속성 값을 설정했으며 두 번째 작업은 직접 메서드를 호출했습니다.

아래와 같이 실행할 방법을 알아보려면 다음 리소스를 참조하세요.

* [IoT Hub 시작](quickstart-send-telemetry-java.md) 자습서를 참조하여 디바이스에서 원격 분석을 보냅니다.

* [직접 메서드 사용](quickstart-control-device-java.md) 자습서를 참조하여 대화형으로(예: 사용자가 제어하는 앱에서 팬을 켬) 디바이스 제어