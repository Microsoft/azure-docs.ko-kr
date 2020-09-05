---
title: '자습서: 샘플 클라이언트 앱으로 기본 사항 살펴보기'
titleSuffix: Azure Digital Twins
description: 샘플 명령줄 애플리케이션을 사용하여 Azure Digital Twins SDK를 살펴보는 자습서
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: a1dc42815167da308fd87b541c0f21d02b47329b
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022516"
---
# <a name="tutorial-explore-azure-digital-twins-with-a-sample-client-app"></a>자습서: 샘플 클라이언트 앱으로 Azure Digital Twins 살펴보기

이 자습서에서는 Azure Digital Twins 인스턴스와 상호 작용을 위해 명령줄 클라이언트 애플리케이션을 구현하는 샘플 애플리케이션을 소개합니다. 클라이언트 앱은 [*자습서: 클라이언트 앱 코딩*](tutorial-code.md)에서 작성한 앱과 유사합니다.

이 샘플을 사용하여 모델 업로드, 트윈 생성 및 수정, 관계 생성과 같은 필수 Azure Digital Twins 작업을 수행할 수 있습니다. 또한 샘플 코드를 보고 Azure Digital Twins API에 대해 배우고 원하는 대로 샘플 프로젝트를 수정하여 명령을 직접 구현해 볼 수 있습니다.

이 자습서에서는 다음을 수행합니다.
> [!div class="checklist"]
> * Azure Digital Twins 인스턴스 설정
> * 인스턴스와 상호 작용하도록 샘플 명령줄 앱 구성
> * 명령줄 앱을 사용하여 **모델**, **디지털 트윈**, **관계** 및 **쿼리**를 포함한 Azure Digital Twins를 살펴봅니다.

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="explore-with-the-sample-solution"></a>샘플 솔루션으로 살펴보기

인스턴스와 샘플 앱이 구성되었으므로 샘플 프로젝트와 미리 작성된 예제 코드를 사용하여 기본 Azure Digital Twins 솔루션을 빌드하고 살펴보겠습니다. 주요 솔루션 구성 요소가 **모델**, **디지털 트윈** 및 **관계**이므로 환경의 쿼리 가능한 **트윈 그래프**가 생성됩니다.

### <a name="model-a-physical-environment-with-dtdl"></a>DTDL로 물리적 환경 모델링

Azure Digital Twins 솔루션을 만드는 첫 번째 단계는 사용자 환경에 대한 트윈 [**모델**](concepts-models.md)을 정의하는 것입니다. 

모델은 개체 지향 프로그래밍 언어의 클래스와 유사하며, 나중에 [디지털 트윈](concepts-twins-graph.md)이 따르고 인스턴스화할 수 있도록 사용자 정의 템플릿을 제공합니다. 모델은 **DTDL(Digital Twins 정의 언어)** 이라는 JSON과 같은 언어로 작성되며 트윈의 *속성*, *원격 분석*, *관계* 및 *구성 요소*를 정의할 수 있습니다.

> [!NOTE]
> 또한 DTDL은 디지털 트윈에 *명령* 정의를 허용합니다. 그러나 Azure Digital Twins 서비스에서는 현재 명령이 지원되지 않습니다.

_**AdtE2ESample**_ 프로젝트가 열려 있는 Visual Studio 창에서 *솔루션 탐색기* 창을 사용하여 *AdtSampleApp\SampleClientApp\Models* 폴더로 이동합니다. 이 폴더에는 샘플 모델이 있습니다.

*Room.json*을 선택하여 편집 창에서 열고 다음과 같은 방법으로 변경합니다.

* **버전 번호를 업데이트**하여 이 모델의 최신 버전을 제공하고 있음을 나타냅니다. 이렇게 하려면 `@id` 값의 끝에 있는 *1*을 *2*로 변경합니다. 현재 버전 번호보다 큰 번호도 사용할 수 있습니다.
* **속성을 편집**합니다. `Humidity` 속성의 이름을 *HumidityLevel*로 변경합니다. 원하는 경우 다른 이름을 사용할 수 있습니다. *HumidityLevel*이 아닌 다른 이름을 사용하는 경우 해당 이름을 기억하여 자습서 전체에서 *HumidityLevel* 대신 사용합니다.
* **속성을 추가**합니다. 줄 15에서 끝나는 `HumidityLevel` 속성 아래에 다음 코드를 붙여 넣어 `RoomName` 속성을 room에 추가합니다.

    ```json
    ,
    {
      "@type": "Property",
      "name": "RoomName",
      "schema": "string"
    }
    ```
* **관계를 추가**합니다. 방금 추가한 `RoomName` 속성 아래에 다음 코드를 붙여 넣어 이러한 유형의 트윈이 다른 트윈과 *포함* 관계를 형성하는 기능을 추가합니다.

    ```json
    ,
    {
      "@type": "Relationship",
      "name": "contains",
    }
    ```

완료 시 업데이트된 모델은 다음과 같습니다.

:::image type="content" source="media/tutorial-command-line-app/room-model.png" alt-text="업데이트된 버전 번호, HumidityLevel 및 RoomName 속성, 포함 관계로 편집된 Room.json" border="false":::

계속 진행하기 전에 파일을 저장합니다.

> [!TIP]
> 모델을 직접 만들려는 경우 *AdtSampleApp\SampleClientApp\Models* 폴더에 *.json* 확장자로 저장하는 새 파일에 *Room* 모델 코드를 붙여 넣을 수 있습니다. 그런 다음, 속성과 관계를 추가하여 원하는 대로 구성합니다. 이 폴더의 다른 샘플 모델을 보고 아이디어를 얻을 수도 있습니다.

> [!TIP] 
> DTDL이 유효한지 확인하기 위해 모델 문서를 검사하는 데 사용할 수 있는 언어 독립적 [DTDL 유효성 검사기 샘플](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)이 있습니다. 이 파일은 DTDL 파서 라이브러리를 기반으로 합니다. DTDL 파서 라이브러리에 대한 자세한 내용은 [*방법: 모델 구문 분석 및 유효성 검사*](how-to-parse-models.md)를 참조하세요.

### <a name="get-started-with-the-command-line-app"></a>명령줄 앱 시작

모델을 정의했으므로 나머지 단계에서는 샘플 앱을 사용하여 Azure Digital Twins 인스턴스와 상호 작용합니다. 도구 모음의 다음 단추로 프로젝트를 실행합니다.

:::image type="content" source="media/tutorial-command-line-app/start-button-sample.png" alt-text="Visual Studio 시작 단추(SampleClientApp 프로젝트)":::

콘솔 창이 열리고 인증을 수행하고 명령을 기다립니다. 
* 브라우저를 통해 인증이 처리됩니다. 인증 프롬프트와 함께 기본 웹 브라우저가 열립니다. 이 프롬프트를 사용하여 Azure 자격 증명에 로그인합니다. 그런 다음, 브라우저 탭이나 창을 닫을 수 있습니다.

프로젝트 콘솔은 다음 스크린샷과 같습니다.

:::image type="content" source="media/tutorial-command-line-app/command-line-app.png" alt-text="명령줄 앱의 시작 메시지":::

> [!TIP]
> 이 프로젝트에서 사용할 수 있는 모든 명령 목록을 보려면 프로젝트 콘솔에 `help`를 입력하고 Return 키를 누릅니다.
> :::image type="content" source="media/tutorial-command-line-app/command-line-app-help.png" alt-text="help 명령의 출력":::

이 자습서의 나머지 단계를 위해 프로젝트 콘솔을 계속 실행합니다.

#### <a name="upload-models-to-azure-digital-twins"></a>Azure Digital Twins에 모델 업로드

모델을 설계한 후에는 Azure Digital Twins 인스턴스에 업로드해야 합니다. 그러면 고유의 사용자 지정 도메인 어휘로 Azure Digital Twins 서비스 인스턴스가 구성됩니다. 모델을 업로드한 후에는 이를 사용하는 트윈 인스턴스를 만들 수 있습니다.

프로젝트 콘솔 창에서 다음 명령을 실행하여 업데이트된 *Room* 모델과 다음 섹션에서도 사용할 *Floor* 모델을 업로드하여 여러 유형의 트윈을 만듭니다.

```cmd/sh
CreateModels Room Floor
```

출력에 모델이 성공적으로 만들어졌다고 표시되어야 합니다.

> [!TIP]
> 이전에 모델을 직접 설계한 경우 위 명령의 `Room Floor` 목록에 해당 파일 이름(확장자 생략 가능)을 추가하여 여기에 모델을 업로드할 수도 있습니다.

명령 `GetModels true`를 실행하여 모델이 만들어졌는지 확인합니다. 이를 통해 업로드된 모든 모델에 대해 Azure Digital Twins 인스턴스가 쿼리되고 해당하는 전체 정보가 출력됩니다. 결과에서 편집된 *Room* 모델을 찾습니다.

:::image type="content" source="media/tutorial-command-line-app/output-get-models.png" alt-text="업데이트된 Room 모델을 표시하는 GetModels의 결과":::

#### <a name="errors"></a>오류

샘플 애플리케이션은 서비스의 오류도 처리합니다. 

`CreateModels` 명령을 다시 실행하여 방금 업로드한 동일한 모델 중 하나를 다시 업로드해 봅니다.

```cmd/sh
CreateModels Room
```

모델을 덮어쓸 수 없으므로 이제 서비스 오류가 반환됩니다.
기존 모델을 삭제하는 방법에 대한 자세한 내용은 [*방법: 사용자 지정 모델 관리*](how-to-manage-model.md)를 참조하세요.
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

### <a name="create-digital-twins"></a>디지털 트윈 만들기

일부 모델이 Azure Digital Twins 인스턴스에 업로드되었으므로 모델 정의를 기반으로 [**디지털 트윈**](concepts-twins-graph.md)을 만들 수 있습니다. 디지털 트윈은 농장의 센서, 건물의 방 또는 자동차의 조명과 같은 비즈니스 환경 내의 엔터티를 나타냅니다. 

디지털 트윈을 만들려면 `CreateDigitalTwin` 명령을 사용합니다. 트윈이 기반으로 하는 모델을 참조해야 하며, 필요에 따라 모델의 속성에 대한 초기 값을 정의할 수 있습니다. 이 단계에서는 관계 정보를 전달할 필요가 없습니다.

실행 중인 프로젝트 콘솔에서 이 코드를 실행하여 이전에 업데이트한 *Room* 모델과 또 다른 모델인 *Floor*를 기반으로 여러 개의 트윈을 만듭니다. *Room*에는 세 가지 속성이 있으므로 인수에 이들 속성에 대한 초기 값을 제공할 수 있습니다.

```cmd/sh
CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
CreateDigitalTwin dtmi:example:Floor;1 floor0
CreateDigitalTwin dtmi:example:Floor;1 floor1
```

> [!TIP]
> 이전에 사용자 고유의 모델을 업로드한 경우 위의 명령을 기반으로 `CreateDigitalTwin` 명령을 직접 만들어서 사용자의 모델 유형을 추가합니다.

이러한 명령의 출력에 트윈이 성공적으로 만들어졌다고 표시되어야 합니다. 

:::image type="content" source="media/tutorial-command-line-app/output-create-digital-twin.png" alt-text="floor0, floor1, room0 및 room1을 표시하는 CreateDigitalTwin 명령의 결과에서 발췌":::

`Query` 명령을 실행하여 트윈이 만들어졌는지 확인할 수도 있습니다. 이 명령은 Azure Digital Twins 인스턴스에 포함된 모든 디지털 트윈을 쿼리합니다. 결과에서 *floor0*, *floor1*, *room0* 및 *room1* 트윈을 찾습니다.

#### <a name="modify-a-digital-twin"></a>디지털 트윈 수정

만든 트윈의 속성을 수정할 수도 있습니다. 이 명령을 실행하여 *room0*의 RoomName을 *Room0*에서 *PresidentialSuite*로 변경해 봅니다.

```cmd/sh
UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
```

출력에 트윈이 성공적으로 업데이트되었다고 표시되어야 합니다.

이 명령을 실행하여 *room0*의 정보를 확인할 수도 있습니다.

```cmd/sh
GetDigitalTwin room0
```

출력에 업데이트된 이름이 반영되어야 합니다.

> [!NOTE]
> 기본 REST API는 JSON 패치를 사용하여 트윈에 대한 업데이트를 정의합니다. 명령줄 앱은 이 형식을 반영하므로 기본 API에 실제로 필요한 항목을 시험해 볼 수 있습니다.

### <a name="create-a-graph-by-adding-relationships"></a>관계를 추가하여 그래프 만들기

다음으로, 이러한 트윈 간의 몇 가지 **관계**를 만들어 [**트윈 그래프**](concepts-twins-graph.md)로 연결할 수 있습니다. 트윈 그래프는 전체 환경을 나타내는 데 사용됩니다. 

관계를 추가하려면 `CreateRelationship` 명령을 사용합니다. 관계가 시작되는 트윈, 추가할 관계 유형 및 관계가 연결되는 트윈을 지정합니다. 마지막으로, 관계에 대한 이름(ID)을 제공합니다.

다음 코드를 실행하여 앞에서 만든 각 *Floor* 트윈의 "포함" 관계를 해당 *Room* 트윈에 추가합니다. 이를 위해서는 *Floor* 모델에 *포함* 관계가 정의되어 있어야 합니다.

```cmd/sh
CreateRelationship floor0 contains room0 relationship0
CreateRelationship floor1 contains room1 relationship1
```

이러한 명령의 출력은 관계가 성공적으로 만들어졌는지 확인합니다.

:::image type="content" source="media/tutorial-command-line-app/output-create-relationship.png" alt-text="relationship0 및 relationship1을 표시하는 CreateRelationship 명령의 결과에서 발췌":::

Azure Digital Twins 인스턴스의 관계를 쿼리하는 다음 명령 중 하나를 사용하여 관계를 확인할 수도 있습니다.
* 각 floor에서 시작되는 모든 관계를 보려면 다음 명령을 실행합니다(한 쪽에서 관계 보기).
    ```cmd/sh
    GetRelationships floor0
    GetRelationships floor1
    ```
* 각 room에 도착하는 모든 관계를 보려면 다음 명령을 실행합니다("다른" 쪽에서 관계 보기).
    ```cmd/sh
    GetIncomingRelationships room0
    ```
* 이러한 관계를 개별적으로 쿼리하려면 다음 명령을 실행합니다. 
    ```cmd/sh
    GetRelationship floor0 contains relationship0
    GetRelationship floor1 contains relationship1
    ```

이 자습서에서 설정한 트윈과 관계는 다음과 같은 개념적 그래프를 만듭니다.

:::image type="content" source="media/tutorial-command-line-app/sample-graph.png" alt-text="relationship0을 통해 room0에 연결되는 floor0과 relationship1을 통해 room1에 연결되는 floor1을 보여주는 그래프" border="false":::

### <a name="query-the-twin-graph-to-answer-environment-questions"></a>트윈 그래프를 쿼리하여 환경 질문에 대답

Azure Digital Twins의 주요 기능은 환경에 대한 질문에 답하도록 쉽고 효율적으로 트윈 그래프를 [쿼리](concepts-query-language.md)하는 기능입니다. 실행 중인 프로젝트 콘솔에서 다음 명령을 실행하여 이에 대한 개념을 파악합니다.

* **Azure Digital Twins에 표시되는 내 환경의 모든 엔터티는 무엇인가요?** (모두 쿼리)

    ```cmd/sh
    Query
    ```

    이를 통해 환경을 한눈에 파악하고 모든 것이 Azure Digital Twins 내에 원하는 대로 표시되는지 확인할 수 있습니다. 이 쿼리의 결과는 각 디지털 트윈에 세부 정보가 포함된 출력입니다. 발췌 내용은 다음과 같습니다.

    :::image type="content" source="media/tutorial-command-line-app/output-query-all.png" alt-text="room0과 floor1을 표시하는 트윈 쿼리의 일부 결과":::

    >[!NOTE]
    >추가 인수를 제외한 명령 `Query`는 `Query SELECT * FROM DIGITALTWINS`와 같습니다.

* **내 환경의 모든 room은 무엇인가요?** (모델로 쿼리)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    특정 유형의 트윈으로 쿼리를 제한하여 표시되는 항목에 대한 보다 구체적인 정보를 얻을 수 있습니다. 이 쿼리의 결과는 *room0*과 *room1*을 표시하지만 *floor0*이나 *floor1*을 표시하지 **않습니다**(room이 아닌 floor이기 때문에).
    
    :::image type="content" source="media/tutorial-command-line-app/output-query-model.png" alt-text="room0과 room1만 표시하는 모델 쿼리의 결과":::

* ***floor0*의 모든 room은 무엇인가요?** (관계로 쿼리)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    그래프의 관계를 기반으로 쿼리하여 트윈 연결 방식에 대한 정보를 얻거나 쿼리를 특정 영역으로 제한할 수 있습니다. *room0*만 *floor0*에 있으므로 이것이 결과의 유일한 room입니다.

    :::image type="content" source="media/tutorial-command-line-app/output-query-relationship.png" alt-text="room0을 표시하는 관계 쿼리의 결과":::

* **온도가 75도가 넘는 내 환경의 모든 트윈은 무엇인가요?** (속성으로 쿼리)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    속성을 기반으로 그래프를 쿼리하여 환경에서 주의가 필요한 이상값을 찾는 등 다양한 질문에 답할 수 있습니다. 다른 비교 연산자( *<* , *>* , *=* 또는 *!=* )도 지원됩니다. *room1*이 온도가 80도이기 때문에 다음 결과에 표시됩니다.

    :::image type="content" source="media/tutorial-command-line-app/output-query-property.png" alt-text="room1만 표시하는 속성 쿼리의 결과":::

* **온도가 75도가 넘는 *floor0*의 모든 room은 무엇인가요?** (복합 쿼리)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    `AND`, `OR`, `NOT`과 같은 조합 연산자를 사용하여 SQL에서와 같이 이전 쿼리를 결합할 수도 있습니다. 이 쿼리는 `AND`를 사용하여 트윈 온도에 대한 이전 쿼리를 더 구체화합니다. 이제 결과에는 *floor0*에서 온도가 75도가 넘는 room만 포함됩니다(이 경우에는 없음). 결과 집합이 비어 있습니다.

    :::image type="content" source="media/tutorial-command-line-app/output-query-compound.png" alt-text="아무 결과도 표시하지 않는 복합 쿼리의 결과":::

## <a name="clean-up-resources"></a>리소스 정리

이 자습서의 프로젝트는 다음 자습서인 [*자습서: 엔드투엔드 솔루션 연결*](tutorial-end-to-end.md)의 기초를 형성합니다. 다음 자습서로 계속 진행할 계획인 경우 여기에서 설정한 리소스를 유지하여 이 Azure Digital Twins 인스턴스와 구성된 샘플 앱을 계속 사용할 수 있습니다.
* 이 경우 샘플 앱의 `DeleteAllTwins` 명령과 `DeleteAllModels` 명령을 사용하여 인스턴스의 트윈과 모델을 각각 지울 수 있습니다. 이를 통해 다음 자습서를 위한 깨끗한 슬레이트를 준비할 수 있습니다.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

마지막으로, 로컬 컴퓨터에 다운로드한 프로젝트 샘플 폴더를 삭제합니다.

## <a name="next-steps"></a>다음 단계 

이 자습서에서는 인스턴스와 상호 작용하도록 인스턴스와 클라이언트 애플리케이션을 설정하여 Azure Digital Twins를 시작했습니다. 클라이언트 앱을 사용하여 Azure Digital Twins를 살펴보고, 모델, 디지털 트윈 및 관계를 만들었습니다. 또한 솔루션에서 몇 가지 쿼리를 실행하여 Azure Digital Twins가 환경에 대해 어떤 종류의 질문에 답할 수 있는지 알아보았습니다.

다음 자습서에서 다른 Azure 서비스와 함께 샘플 명령줄 앱을 사용하여 데이터 기반의 엔드투엔드 시나리오를 완성하세요.
> [!div class="nextstepaction"]
> [*자습서: 엔드투엔드 솔루션 연결*](tutorial-end-to-end.md)