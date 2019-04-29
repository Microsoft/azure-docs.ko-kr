---
title: 원격 모니터링과 Time Series Insights 통합 - Azure | Microsoft Docs
description: 이 방법에서 아직 Time Series Insights를 포함하지 않은 기존 원격 모니터링 솔루션에 대해 Time Series Insights를 구성하는 방법에 대해 알아봅니다.
author: aditidugar
manager: timlt
ms.author: adugar
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 4cc9b0051eaa12eee07f067352126ad159107a83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61443031"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>원격 모니터링과 Azure Time Series Insights 통합

Azure Time Series Insights는 클라우드에서 IoT 규모 시계열 데이터를 관리하기 위한 완전 관리형 분석, 저장 및 가상화 서비스입니다. Time Series Insights를 사용하면 시계열 데이터를 저장 및 관리하고, 이벤트를 동시에 탐색 및 시각화하고, 근본 원인 분석을 수행하며, 여러 사이트 및 자산을 비교할 수 있습니다.

원격 모니터링 솔루션 가속기는 이제 자동 배포 및 Time Series Insights와의 통합을 제공합니다. 이 방법에서 아직 Time Series Insights를 포함하지 않은 기존 원격 모니터링 솔루션에 대해 Time Series Insights를 구성하는 방법에 대해 알아봅니다.

> [!NOTE]
> Azure 중국 클라우드에서는 현재 Time Series Insights를 사용할 수 없습니다. Azure China 클라우드에서 새 원격 모니터링 솔루션 가속기를 배포하는 경우 모든 저장소에 Cosmos DB를 사용합니다.

## <a name="prerequisites"></a>필수 조건

이 방법을 완료하려면 이미 원격 모니터링 솔루션이 배포되어 있어야 합니다.

* [원격 모니터링 솔루션 가속기 배포](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>소비자 그룹 만들기

Time Series Insights에 데이터를 스트리밍하는 데 사용될 IoT Hub에서 전용 소비자 그룹을 만듭니다.

> [!NOTE]
> 소비자 그룹은 애플리케이션에서 Azure IoT Hub의 데이터를 끌어오는 데 사용됩니다. 각 소비자 그룹은 최대 5개의 출력 소비자를 허용합니다. 각 5개의 출력 싱크에 대해 새로운 소비자 그룹을 만들어야 하며, 최대 32개의 소비자 그룹을 만들 수 있습니다.

1. Azure Portal에서 Cloud Shell 단추를 클릭합니다.

1. 다음 명령을 실행하여 새 소비자 그룹을 만듭니다. 원격 모니터링 배포에서 IoT 허브의 이름 및 원격 모니터링 배포의 이름을 리소스 그룹 이름으로 사용합니다.

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Time Series Insights 배포

다음으로, 원격 모니터링 솔루션에 Time Series Insights를 추가 리소스로 배포하고 IoT 허브에 연결합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **리소스 만들기** > **사물 인터넷** > **Time Series Insights**를 선택합니다.

    ![새로운 Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Time Series Insights 환경을 만들려면 다음 표에 있는 값을 사용합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 환경 이름 | 다음 스크린샷에서는 **contorosrmtsi**라는 이름을 사용합니다. 이 단계를 완료할 때는 자신만의 고유한 이름을 선택합니다. |
    | 구독 | 드롭다운에서 Azure 구독을 선택합니다. |
    | 리소스 그룹 | **기존 리소스를 사용합니다**. 기존 원격 모니터링 리소스 그룹의 이름을 선택합니다. |
    | Location | **미국 동부**를 사용합니다. 가능하면 원격 모니터링 솔루션으로 동일한 지역의 환경을 만듭니다. |
    | SKU |**S1** |
    | 용량 | **1** |

    ![Time Series Insights 만들기](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. **만들기**를 클릭합니다. 환경을 만드는 데 시간이 걸릴 수 있습니다.

## <a name="create-event-source"></a>이벤트 원본 만들기

IoT 허브에 연결할 새 이벤트 원본을 만듭니다. 이전 단계에서 만든 소비자 그룹을 사용하고 있는지 확인합니다. Time Series Insights는 각 서비스에 다른 서비스에서 사용하고 있지 않은 전용 소비자 그룹이 있어야 합니다.

1. 새 Time Series Insights 환경으로 이동합니다.

1. 왼쪽에서 **이벤트 원본**을 선택합니다.

    ![이벤트 원본 보기](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. **추가**를 클릭합니다.

    ![이벤트 원본 추가](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. IoT Hub를 새 이벤트 원본으로 구성하려면 다음 표에 있는 값을 사용합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 이벤트 원본 이름 | 다음 스크린샷에서는 **contosorm-iot-hub**라는 이름을 사용합니다. 이 단계를 완료할 때는 자신만의 고유한 이름을 사용합니다. |
    | 원본 | **IoT Hub** |
    | 가져오기 옵션 | **사용 가능한 구독에서 IoT Hub 사용** |
    | 구독 ID | 드롭다운에서 Azure 구독을 선택합니다. |
    | IoT Hub 이름 | **contosorma57a6**입니다. 원격 모니터링 솔루션에서 IoT Hub의 이름을 사용합니다. |
    | IoT Hub 정책 이름 | **iothubowner** 사용되는 정책이 소유자 정책인지 확인합니다. |
    | IoT Hub 정책 키 | 이 필드는 자동으로 채워집니다. |
    | IoT Hub 소비자 그룹 | **timeseriesinsights** |
    | 이벤트 직렬화 형식 | **JSON**     | 
    | 타임스탬프 속성 이름 | 비워 둠 |

    ![이벤트 원본 만들기](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. **만들기**를 클릭합니다.

## <a name="configure-the-data-access-policy"></a>데이터 액세스 정책 구성

원격 모니터링 솔루션에 대한 액세스 권한이 있는 모든 사용자가 Time Series Insights 탐색기에서 데이터를 탐색할 수 있는지 확인하려면 Azure Portal의 데이터 액세스 정책에서 애플리케이션 및 사용자를 추가합니다. 

1. 왼쪽 목록에서 **리소스 그룹**을 선택합니다.

1. **ContosoRM** 리소스 그룹을 선택합니다.

1. Azure 리소스의 목록에서 **contosormtsi**를 선택합니다.

1. **데이터 액세스 정책**을 선택하여 현재 역할 할당 목록을 확인합니다.

1. **추가**를 선택하여 **사용자 규칙 선택** 창을 엽니다.

   역할을 할당할 수 있는 권한이 없는 경우 **추가** 옵션이 표시되지 않습니다.

1. **역할** 드롭다운 목록에서 **읽기 권한자** 및 **기여자** 같은 역할을 선택합니다.

1. **선택** 목록에서 사용자, 그룹 또는 애플리케이션을 선택합니다. 목록에 보안 주체가 보이지 않으면 **선택** 상자에 직접 입력하여 표시 이름, 이메일 주소 및 개체 식별자에 대한 디렉터리를 검색할 수 있습니다.

1. **저장**을 선택하여 역할 할당을 만듭니다. 몇 분이 지나면 데이터 액세스 정책에서 보안 주체에 역할이 할당됩니다.

> [!NOTE]
> Time Series Insights 탐색기에 대한 액세스 권한을 추가 사용자에게 부여해야 하는 경우 다음 단계를 사용하여 [데이터 액세스 권한을 부여](../time-series-insights/time-series-insights-data-access.md#grant-data-access)할 수 있습니다.

## <a name="configure-azure-stream-analytics"></a>Azure Stream Analytics 구성 

다음 단계는 Cosmos DB에 메시지를 보내기를 중단하도록 Azure Stream Analytics 관리자 마이크로 서비스를 구성해서 Time Series Insights에만 저장하는 것입니다. Cosmos DB에서 메시지를 복제하려는 경우 이 단계를 건너뜁니다.

1. 왼쪽 목록에서 **리소스 그룹**을 선택합니다.

1. **ContosoRM** 리소스 그룹을 선택합니다.

1. 리소스 목록에서 ASA(Azure Stream Analytics) 스트리밍 작업을 찾습니다. 리소스 이름은 **streamingjobs-** 로 시작합니다.

1. 맨 위에 있는 단추를 클릭하여 ASA 스트리밍 작업을 중지합니다.

1. ASA 쿼리를 편집하고, Cosmos DB의 메시지 스트림를 가리키는 **SELECT**, **INTO** 및 **FROM** 절을 제거합니다. 이러한 절은 쿼리의 맨 아래에 있어야 하며 다음 예제와 같아야 합니다.

    ```sql
    SELECT
            CONCAT(T.IoTHub.ConnectionDeviceId, ';', CAST(DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) AS nvarchar(max))) as id,
            1 as [doc.schemaVersion],
            'd2cmessage' as [doc.schema],
            T.IoTHub.ConnectionDeviceId as [device.id],
            'device-sensors;v1' as [device.msg.schema],
            'StreamingJobs' as [data.schema],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', System.Timestamp) as [device.msg.created],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) as [device.msg.received],
            udf.removeUnusedProperties(T) as Data
    INTO
        Messages
    FROM
        DeviceTelemetry T PARTITION BY PartitionId TIMESTAMP BY T.EventEnqueuedUtcTime
    ```

6. Azure Stream Analytics 스트리밍 작업을 다시 시작합니다.

7. 명령 프롬프트에서 다음과 같은 명령을 입력하여 Azure Stream Analytics 관리자 마이크로서비스에 대한 최신 변경 내용을 끌어옵니다.

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>원격 분석 마이크로 서비스 구성

명령 프롬프트에 다음 명령을 입력하여 최신 원격 분석 마이크로서비스를 끌어옵니다.

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[선택 사항]* Time Series Insights 탐색기에 연결할 웹 UI 구성

Time Series Insights 탐색기에서 쉽게 데이터를 보려면 환경에 쉽게 연결할 UI를 사용자 지정하는 것이 좋습니다. 이렇게 하려면 다음 명령을 사용하여 Web UI에 대한 최신 변경 내용을 끌어옵니다.

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>환경 변수 구성

Time Series Insights 통합을 완료하려면 업데이트된 마이크로서비스에 대한 배포 환경을 구성해야 합니다.

### <a name="basic-deployments"></a>기본 배포

업데이트된 마이크로서비스에 대한 `basic` 배포 환경을 구성합니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 탭을 클릭합니다.

1. **앱 등록**을 클릭합니다.

1. **ContosoRM** 애플리케이션을 검색하여 클릭합니다.

1. **설정** > **키**로 이동한 다음, 애플리케이션에 대한 새 키를 만듭니다. 키 값을 안전한 위치에 복사해야 합니다.

1. 최신 태그를 사용하여 GitHub 리포지토리에서 [최신 Docker Compose yaml 파일](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm)을 끌어옵니다. 

1. [SSH 키 생성 및 사용 방법](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)에 설명된 단계를 따라 VM에 SSH합니다.

1. 연결되면 `cd /app`을 입력합니다.

1. Docker Compose yaml 파일의 각 마이크로서비스 및 VM의 `env-setup` 스크립트에 다음과 같은 환경 변수를 추가합니다.

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. **원격 분석 서비스**로 이동하고 위의 동일한 환경 변수를 추가하여 Docker Compose 파일도 편집합니다.

1. **ASA 관리자 서비스**로 이동하고 `PCS_TELEMETRY_STORAGE_TYPE`을 추가하여 Docker Compose 파일을 편집합니다.

1. VM에서 `sudo ./start.sh`를 사용하여 Docker 컨테이너를 다시 시작합니다.

> [!NOTE]
> 위의 구성은 환경 변수의 1.0.2 이전 버전의 원격 모니터링에 대 한 유효

### <a name="standard-deployments"></a>표준 배포

위의 업데이트된 마이크로서비스에 대한 `standard` 배포 환경을 구성합니다.

1. 명령줄에서 `kubectl proxy`를 실행합니다. 자세한 내용은 [Kubernetes API 액세스](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server)를 참조하세요.

1. Kubernetes 관리 콘솔을 엽니다.

1. 구성 지도를 찾아 TSI에 대한 다음의 새 환경 변수를 추가합니다.

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. 원격 분석 서비스 Pod에 대한 템플릿 yaml 파일을 편집합니다.

    ```yaml
    - name: PCS_AAD_TENANT
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.tenant
    - name: PCS_AAD_APPID
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.audience
    - name: PCS_AAD_APPSECRET
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.serviceprincipal.secret
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    - name: PCS_TSI_FQDN
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.tsi.fqdn
    ```

5. ASA 관리자 서비스 Pod에 대한 템플릿 yaml 파일을 편집합니다.

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>다음 단계

* Time Series Insights 탐색기에서 데이터를 탐색하고 경고를 진단하는 방법에 대한 자세한 내용은 [루트 원인 분석 수행](iot-accelerators-remote-monitoring-root-cause-analysis.md) 자습서를 참조하세요.

* Time Series Insights 탐색기에서 데이터를 탐색하고 쿼리하는 방법에 대해 알아보려면 [Azure Time Series Insights 탐색기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer) 설명서를 참조하세요.
