---
title: '자습서: 미리 보기 환경 및 공간 그래프 배포 - Azure Digital Twins | Microsoft Docs'
description: 이 자습서의 단계에 따라 Azure Digital Twins 인스턴스를 배포하고 공간 리소스를 구성하는 방법을 알아봅니다.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 16e4a7e2f06d2630c970f8daa4428e7a184a79df
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163044"
---
# <a name="tutorial-deploy-azure-digital-twins-preview-and-configure-a-spatial-graph"></a>자습서: Azure Digital Twins 미리 보기 배포 및 공간 그래프 구성

Azure Digital Twins 미리 보기를 사용하여 사람, 장소 및 디바이스를 일관된 공간 시스템에 결합할 수 있습니다. 이 자습서 시리즈는 Azure Digital Twins를 사용하여 온도 및 공기질이 최적 조건인 방 점유를 검색하는 방법을 보여줍니다. 

이러한 자습서에서는 사무실 건물의 시나리오를 빌드하는 .NET 콘솔 애플리케이션을 설명합니다. 건물에는 여러 층이 있고 각 층에는 방이 있습니다. 방에는 동작, 주변 온도 및 공기질을 감지하는 센서가 부착된 디바이스가 있습니다. 

Azure Digital Twins 서비스를 사용하여 건물의 물리적 영역 및 엔터티를 디지털 개체로 복제하는 방법을 알아봅니다. 또 다른 콘솔 애플리케이션을 사용하여 디바이스 이벤트를 시뮬레이션합니다. 그런 다음, 이러한 물리적 영역과 엔터티에서 들어오는 이벤트를 근실시간으로 모니터링하는 방법을 알아봅니다. 

사무실 관리자는 이 정보를 사용하여 이 건물에서 근무하는 직원이 최적 상태의 회의실을 예약하도록 도와줄 수 있습니다. 사무실 시설 관리자는 설정을 사용하여 공간 사용량 추세를 살펴보고 유지 관리 목적으로 작업 조건을 모니터링할 수 있습니다.

이 시리즈의 첫 번째 자습서에서는 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * Digital Twins 배포
> * 앱에 사용 권한 부여
> * Digital Twins 샘플 앱 수정
> * 건물 프로비전

이러한 자습서에서는 개념을 보다 구체적으로 설명하기 위해 [빠른 시작: 사용 가능한 회의실 찾기](quickstart-view-occupancy-dotnet.md)에 사용되는 것과 동일한 샘플을 수정하여 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 Azure 계정이 없으면 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- .NET Core SDK 이러한 자습서에 사용되는 Azure Digital Twins 샘플은 C#으로 작성되었습니다. 샘플을 빌드하고 실행할 수 있도록 개발 머신에 [.NET Core SDK 버전 2.1.403 이상](https://www.microsoft.com/net/download)을 설치합니다. 명령 창에서 `dotnet --version` 명령을 실행하여 머신에 올바른 버전이 설치되어 있는지 확인합니다.

- 샘플 코드를 탐색할 [Visual Studio Code](https://code.visualstudio.com/). 

## <a name="deploy-digital-twins"></a>Deploy Digital Twins

이 섹션의 단계에 따라 Azure Digital Twins 서비스의 새 인스턴스를 만듭니다. 구독당 하나의 인스턴스만 만들 수 있습니다. 이미 인스턴스를 실행 중인 경우 다음 섹션으로 건너뜁니다. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="grant-permissions-to-your-app"></a>앱에 권한 부여

Digital Twins는 Azure AD([Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md))를 사용하여 서비스에 대한 [읽기/쓰기 액세스](../active-directory/develop/v2-permissions-and-consent.md)를 제어합니다. Digital Twins 인스턴스와 연결해야 하는 모든 애플리케이션은 Azure AD에 등록되어야 합니다. 이 섹션의 단계에서는 샘플 앱을 등록하는 방법을 보여줍니다.

이미 앱 등록이 있는 경우 해당 앱 등록을 샘플에 다시 사용할 수 있습니다. 하지만 이 섹션을 끝까지 읽고 앱 등록이 올바르게 구성되었는지 확인해야 합니다.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>Digital Twins 샘플 구성

이 섹션에서는 [Digital Twins REST API](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index)와 통신하는 Azure Digital Twins 애플리케이션을 살펴봅니다. 

### <a name="download-the-sample"></a>샘플 다운로드

[빠른 시작: 사용 가능한 회의실 찾기](quickstart-view-occupancy-dotnet.md)에 필요한 샘플을 이미 다운로드한 경우 이 단계를 건너뛸 수 있습니다.

1. [Digital Twins .NET 샘플](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip)을 다운로드합니다.
2. 머신에서 ZIP 폴더의 콘텐츠를 추출합니다.

### <a name="explore-the-sample"></a>샘플 탐색

추출된 샘플 폴더의 **digital-twins-samples-csharp\digital-twins-samples.code-workspace** 파일을 Visual Studio Code에서 엽니다. 이 파일에는 다음 두 프로젝트가 포함되어 있습니다.

* **occupancy-quickstart** 프로비전 샘플을 사용하여 [공간 인텔리전스 그래프](concepts-objectmodel-spatialgraph.md#digital-twins-object-models)를 구성하고 프로비전할 수 있습니다. 이 그래프는 물리적 공간 및 포함된 리소스의 디지털화된 이미지입니다. 이 샘플은 스마트 건물에 대한 개체를 정의하는 [개체 모델](concepts-objectmodel-spatialgraph.md#digital-twins-object-models)을 사용합니다. Digital Twins 개체 및 REST API의 전체 목록을 보려면 [인스턴스](#deploy-digital-twins)에 대해 작성된 [이 REST API 설명서](https://docs.westcentralus.azuresmartspaces.net/management/swagger) 또는 관리 API URL를 방문하세요.

   이 샘플을 탐색하여 Digital Twins 인스턴스와 통신하는 방법을 이해하려면 **src\actions** 폴더를 시작하세요. 이 폴더의 파일은 이 자습서에서 사용할 명령을 구현합니다.
    - **provisionSample.cs** 파일은 공간 그래프를 프로비전하는 방법을 보여줍니다.
    - **getSpaces.cs** 파일은 프로비전된 공간에 대한 정보를 가져옵니다.
    - **getAvailableAndFreshSpaces.cs** 파일은 사용자 정의 함수라고 하는 사용자 지정 함수의 결과를 가져옵니다.
    - **createEndpoints.cs** 파일은 다른 서비스와 상호 작용하는 엔드포인트를 만듭니다.

* 시뮬레이션 샘플 **device-connectivity**는 센서 데이터를 시뮬레이션하여 Digital Twins 인스턴스에 대해 프로비전된 IoT 허브로 보냅니다. 이 샘플은 [공간 그래프를 프로비전한 이후 다음 자습서](tutorial-facilities-udf.md#simulate-sensor-data)에서 사용합니다. 이 샘플을 구성하는 데 사용한 센서 및 디바이스 식별자는 그래프를 프로비전하는 데 사용할 식별자와 동일해야 합니다.

### <a name="configure-the-provisioning-sample"></a>프로비전 샘플 구성

1. 명령 창을 열고, 다운로드한 샘플로 이동합니다. 다음 명령 실행:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. 이 명령을 실행하여 샘플 프로젝트에 대한 종속성을 복원합니다.

    ```cmd/sh
    dotnet restore
    ```

1. Visual Studio Code의 **occupancy-quickstart** 프로젝트에서 [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) 파일을 엽니다. 다음 값을 업데이트합니다.
   * **ClientId**: Azure AD 앱 등록의 애플리케이션 ID를 입력합니다. [앱 사용 권한을 설정](#grant-permissions-to-your-app)하는 섹션에서 이 ID를 기록했습니다.
   * **테넌트**: [Azure AD 테넌트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)의 디렉터리 ID를 입력합니다. [앱 사용 권한을 설정](#grant-permissions-to-your-app)하는 섹션에서 이 ID를 기록했습니다.
   * **BaseUrl**: Digital Twins 인스턴스의 URL을 입력합니다. 이 URL을 가져오려면 이 URL의 자리 표시자를 인스턴스에 대한 값(`https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`)으로 바꿉니다. [배포 섹션](#deploy-digital-twins)에서 관리 API URL을 수정하여 이 URL을 가져올 수도 있습니다. **swagger/** 를 **api/v1.0/** 로 바꿉니다.

1. 샘플을 사용하여 탐색할 수 있는 Digital Twins 기능 목록을 검토합니다. 다음 명령 실행:

    ```cmd/sh
    dotnet run
    ```

## <a name="understand-the-provisioning-process"></a>프로비전 프로세스 이해

이 섹션에서는 샘플이 건물의 공간 그래프를 프로비전하는 방법을 보여줍니다.

Visual Studio Code에서 **occupancy-quickstart\src\actions** 폴더로 이동하여 **provisionSample.cs** 파일을 엽니다. 다음 함수를 봅니다.

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}
```

이 함수는 같은 폴더에 있는 [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) 파일을 사용합니다. 이 파일을 열고, 사무실 건물의 계층 구조인 *구역*, *층*, *영역* 및 *방*을 살펴봅니다. 이러한 물리적 공간은 *디바이스* 및 *센서*를 포함할 수 있습니다. 각 항목에는 미리 정의된 `type`&mdash;이 있습니다(예: 층, 방).

샘플 **yaml** 파일은 `Default` Digital Twins 개체 모델을 사용하는 공간 그래프를 보여줍니다. 이 모델에서는 대부분의 형식에 대해 일반 이름을 제공합니다. 건물에 일반 이름을 사용하면 충분합니다. 예제는 SensorDataType에 대한 온도 및 SpaceBlobType에 대한 매핑입니다. 예제 공간 형식은 FocusRoom, ConferenceRoom 등의 하위 형식을 사용하는 방입니다. 

공장 같은 다른 유형의 장소에 대한 공간 그래프를 만들어야 하는 경우 다른 개체 모델이 필요합니다. 프로비전 샘플에 대한 명령줄에서 `dotnet run GetOntologies` 명령을 실행하여 사용 가능한 모델을 확인할 수 있습니다. 

공간 그래프 및 개체 모델에 대한 자세한 내용은 [Digital Twins 개체 모델 및 공간 인텔리전스 그래프의 이해](concepts-objectmodel-spatialgraph.md)를 참조하세요.

### <a name="modify-the-sample-spatial-graph"></a>샘플 공간 그래프 수정

**provisionSample.yaml** 파일은 다음 노드를 포함합니다.

- **resources**: `resources` 노드는 설치된 디바이스와 통신하는 Azure IoT Hub 리소스를 만듭니다. 그래프의 루트 노드에 있는 IoT 허브는 그래프의 모든 디바이스 및 센서와 통신할 수 있습니다.  

- **spaces**: Digital Twins 개체 모델에서 `spaces`는 물리적 위치를 나타냅니다. 각 공간에는 `Type`&mdash;(예: 영역, 구역 또는 고객)&mdash; 및 친숙한 `Name`이 있습니다. 공간은 계층적 구조를 만드는 다른 공간에 속할 수 있습니다. provisionSample.yaml 파일에는 가상 건물의 공간 그래프가 있습니다. `Venue`의 `Floor`, 층의 `Area`, 영역의 `Room` 노드 형식인 공간의 논리 중첩을 적어둡니다. 

- **devices**: spaces에는 여러 센서를 관리하는 물리적 디바이스 또는 가상 엔터티인 `devices`가 포함될 수 있습니다. 예를 들어 디바이스는 사용자의 휴대폰, Raspberry Pi 센서 Pod 또는 게이트웨이일 수 있습니다. 샘플의 가상 건물에서 **Focus Room**이라는 이름의 방에 **Raspberry Pi 3 A1** 디바이스가 어떻게 포함되는지 살펴보세요. 각 디바이스 노드는 고유한 `hardwareId`를 통해 식별되며, 이 샘플에서는 이러한 ID가 하드코딩되었습니다. 실제 프로덕션 환경에 사용할 수 있도록 이 샘플을 구성하려면 이러한 ID를 해당 설정의 값으로 바꿔야 합니다.  

- **sensors**: 디바이스에는 여러 `sensors`가 포함될 수 있습니다. 온도, 동작 및 배터리 수준과 같은 물리적 변경 내용을 감지하고 기록할 수 있습니다. 각 센서 노드는 `hardwareId`를 통해 식별되며, 여기서는 이 ID가 하드코딩되었습니다. 실제 애플리케이션의 경우 이러한 ID를 해당 설정에 사용되는 센서의 고유 식별자로 바꿔야 합니다. provisionSample.yaml 파일에는 *동작* 및 *이산화탄소*를 기록하는 두 개의 센서가 있습니다. 이산화탄소 센서에 대한 줄 아래에 다음 줄을 추가하여 *온도*를 기록할 또 다른 센서를 추가합니다. 이러한 항목은 provisionSample.yaml에서 주석 처리된 줄로 제공됩니다. 각 줄의 앞에서 `#` 문자를 제거하여 주석 처리를 제거할 수 있습니다. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > 이 코드 조각의 명령문과 `dataType` 및 `hardwareId` 키를 정렬합니다. 또한 편집기가 공간을 탭으로 바꾸지 않도록 주의해야 합니다. 

provisionSample.yaml 파일을 저장하고 닫습니다. 다음 자습서에서는 이 파일에 정보를 추가한 다음, Azure Digital Twins 샘플 건물을 프로비전합니다.

> [!TIP]
> [Azure Digital Twins 그래프 뷰어](https://github.com/Azure/azure-digital-twins-graph-viewer)를 사용하여 공간 그래프를 보고 수정할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 Azure Digital Twins를 탐색할 계획이 없는 경우 이 자습서에서 만든 리소스를 자유롭게 삭제할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **모든 리소스**를 선택하고, Digital Twins 리소스 그룹을 선택하고, **삭제**를 선택하면 됩니다.

    > [!TIP]
    > Digital Twins 인스턴스를 삭제하는 데 문제가 있을 경우 픽스가 포함된 서비스 업데이트가 배포된 것입니다. 인스턴스 삭제를 다시 시도해 보세요.

1. 필요한 경우 작업 머신에서 샘플 애플리케이션을 삭제합니다.

## <a name="next-steps"></a>다음 단계

샘플 건물의 조건을 모니터링하는 사용자 지정 논리를 구현하는 방법을 알아보려면 시리즈의 다음 자습서로 이동합니다. 
> [!div class="nextstepaction"]
> [자습서: 건물을 프로비전하고 작업 조건 모니터링](tutorial-facilities-udf.md)