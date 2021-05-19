---
title: 빠른 시작 - 샘플 시나리오 살펴보기
titleSuffix: Azure Digital Twins
description: 빠른 시작 - Azure Digital Twins Explorer 샘플을 사용하여 미리 작성된 시나리오를 시각화하고 살펴봅니다.
author: baanders
ms.author: baanders
ms.date: 4/27/2021
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: d56c4b8fc17dc29578366e3d84e9ba20ca95a9dd
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109789716"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-azure-digital-twins-explorer"></a>빠른 시작 - Azure Digital Twins Explorer를 사용하여 Azure Digital Twins 샘플 시나리오 살펴보기

Azure Digital Twins를 사용하면 실제 환경의 라이브 모델을 만들어 이와 상호 작용할 수 있습니다. 먼저 개별 요소를 **디지털 트윈** 으로 모델링합니다. 그런 다음, 라이브 이벤트에 응답하고 정보를 쿼리할 수 있는 기술 자료 **그래프** 에 연결합니다.

이 빠른 시작에서는 [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)라는 샘플 애플리케이션의 지원을 통해 미리 작성된 Azure Digital Twins 그래프를 살펴봅니다. Azure Digital Twins Explorer를 사용하여 다음을 수행합니다.

- 환경의 디지털 표현을 업로드합니다.
- Azure Digital Twins에서 환경을 나타내기 위해 만들어진 트윈 및 그래프의 시각적 이미지를 봅니다.
- 브라우저 기반의 시각적 환경을 통해 다른 관리 작업을 수행합니다.

이 빠른 시작에 포함된 주요 단계는 다음과 같습니다.

1. Azure Digital Twins 인스턴스 및 Azure Digital Twins Explorer 설정
1. 미리 작성된 모델 및 그래프 데이터를 업로드하여 샘플 시나리오 생성
1. 만들어진 시나리오 그래프 살펴보기
1. 그래프 변경

작업할 샘플 그래프는 두 개의 층과 두 개의 방이 있는 건물을 나타냅니다. 그래프는 다음 이미지와 비슷합니다.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="화살표로 연결된 4개의 원형 노드로 구성된 그래프 보기. 'Floor1'이라는 레이블이 지정된 원은 'contains'라는 레이블이 지정된 화살표를 통해 'Room1'이라는 레이블이 지정된 원에 연결됩니다. 'Floor0'이라는 레이블이 지정된 원은 'contains'라는 레이블이 지정된 화살표를 통해 'Room0'이라는 레이블이 지정된 원에 연결됩니다. 'Floor1'과 'Floor0'은 연결되지 않습니다.":::

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 Azure 구독이 필요합니다. 아직 없는 경우 지금 [체험 구독을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

또한 머신에 **Node.js** 가 필요합니다. 최신 버전을 받으려면 [Node.js](https://nodejs.org/)를 참조하세요.

마지막으로 빠른 시작에서 사용할 샘플을 다운로드해야 합니다. 샘플 애플리케이션은 **Azure Digital Twins Explorer** 입니다. 이 샘플에는 빠른 시작에서 Azure Digital Twins 시나리오를 로드하고 살펴보는 데 사용할 앱이 포함되어 있습니다. 샘플 시나리오 파일도 들어 있습니다. 샘플을 받으려면 [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)로 이동합니다. 제목 아래에 있는 **코드 찾아보기** 단추를 선택하면 샘플에 대한 GitHub 리포지토리로 이동합니다. **코드** 단추와 **ZIP 다운로드** 를 선택하여 샘플을 .zip 파일로 다운로드합니다. 

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/download-repo-zip.png" alt-text="GitHub의 digital-twins-explorer 리포지토리 스크린샷. 코드 단추를 선택하면 ZIP 다운로드 단추가 강조 표시된 작은 대화 상자가 생성됩니다." lightbox="media/quickstart-azure-digital-twins-explorer/download-repo-zip.png":::

**digital-twins-explorer-main.zip** 폴더의 압축을 풀고 파일 압축을 풉니다.

## <a name="set-up-azure-digital-twins-and-azure-digital-twins-explorer"></a>Azure Digital Twins 및 Azure Digital Twins Explorer 설정

Azure Digital Twins를 사용하는 첫 번째 단계는 Azure Digital Twins 인스턴스를 설정하는 것입니다. 서비스의 인스턴스를 만들고 Azure Digital Twins Explorer로 인증하도록 자격 증명을 설정한 후에는 Azure Digital Twins Explorer에서 인스턴스에 연결하고 빠른 시작의 뒷부분에 나오는 예제 데이터로 인스턴스를 채울 수 있습니다.

이 섹션의 나머지 부분에서는 이러한 단계를 안내합니다.

### <a name="set-up-an-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 설정

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>로컬 Azure 자격 증명 설정

Azure Digital Twins Explorer 애플리케이션은 로컬 컴퓨터에서 실행할 때 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)(`Azure.Identity` 라이브러리의 일부)을 사용하여 Azure Digital Twins 인스턴스로 사용자를 인증합니다. 클라이언트 앱에서 Azure Digital Twins를 사용하여 인증하는 여러 방법에 대한 자세한 내용은 [앱 인증 코드 작성](how-to-authenticate-client.md)을 참조하세요.

이 인증 형식을 사용하면 Azure Digital Twins Explorer는 로컬 [Azure CLI](/cli/azure/install-azure-cli) 또는 Visual Studio/Visual Studio Code의 Azure 로그인과 같은 로컬 환경 내에서 자격 증명을 검색합니다. 이러한 이유로 Azure Digital Twins Explorer 앱의 자격 증명을 설정하려면 이러한 메커니즘 중 하나를 통해 **Azure에 로컬로 로그인** 해야 합니다.

이러한 방법 중 하나를 통해 Azure에 이미 로그인한 경우 [다음 섹션](#run-and-configure-azure-digital-twins-explorer)으로 건너뛸 수 있습니다.

그렇지 않으면 다음 단계에 따라 로컬 Azure CLI를 설치할 수 있습니다.

1. [이 설치 링크](/cli/azure/install-azure-cli)의 프로세스에 따라 OS와 일치하는 설치를 완료합니다.
1. 머신에서 콘솔 창을 엽니다.
1. `az login`을 실행하고 인증 프롬프트에 따라 Azure 계정에 로그인합니다.
1. 마지막 단계: 이 계정에서 여러 Azure 구독을 사용하는 경우 `az account set --subscription "<your-subscription-name-or-ID>"`를 실행하여 Azure Digital Twins 인스턴스가 포함된 Azure 구독에 대한 인증 컨텍스트를 설정합니다(구독의 이름 또는 ID 값이 유효함).

로그인하고 다음 섹션에서 Azure Digital Twins Explorer를 실행하면 Azure Digital Twins Explorer가 자동으로 해당 Azure 자격 증명을 선택합니다.

원한다면 인증 콘솔 창을 닫아도 됩니다. 또는 다음 단계에서 사용할 때까지 계속 열어 두어도 됩니다.

### <a name="run-and-configure-azure-digital-twins-explorer"></a>Azure Digital Twins Explorer 실행 및 구성

다음으로, Azure Digital Twins Explorer 애플리케이션을 실행하고 Azure Digital Twins 인스턴스에 맞게 구성합니다.

1. 다운로드하여 압축을 푼 **digital-twins-explorer-main** 폴더로 이동합니다.
콘솔 창을 **digital-twins-explorer-main/client/src** 폴더 위치로 엽니다.

1. `npm install`을 실행하여 필요한 종속성을 모두 다운로드합니다.

1. `npm run start`를 실행하여 앱을 실행합니다.

   몇 초 후 브라우저 창이 열리고 앱이 브라우저에 표시됩니다.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/explorer-blank.png" alt-text="localhost:3000에서 실행 중인 앱을 보여 주는 브라우저 창입니다. 이 앱은 Azure Digital Twins Explorer라고 하며 쿼리 탐색기, 모델, 그래프 보기 및 속성에 대한 패널을 포함하고 있습니다. 화면 데이터는 아직 없습니다." lightbox="media/quickstart-azure-digital-twins-explorer/explorer-blank.png":::

1. 다음 이미지처럼 창의 오른쪽 위 모서리에서 **로그인** 단추를 선택하고, 앞에서 설정한 인스턴스와 함께 작동하도록 Azure Digital Twins Explorer를 구성합니다.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/sign-in.png" alt-text="창 위쪽 근처에 있는 로그인 아이콘이 강조 표시된 Azure Digital Twins Explorer. 이 아이콘은 열쇠의 실루엣과 겹쳐진 사람의 간단한 실루엣을 보여 줍니다." lightbox="media/quickstart-azure-digital-twins-explorer/sign-in.png":::

1. 이전에 [Azure Digital Twins 인스턴스 설정](#set-up-an-azure-digital-twins-instance) 섹션에서 수집한 Azure Digital Twins 인스턴스 URL을 *https://<instance-host-name>* 형식으로 입력합니다.

> [!TIP]
> 연결할 때 `SignalRService.subscribe` 오류 메시지가 표시되면 Azure Digital Twins URL이 *https://* 로 시작하는지 확인합니다.
>
> 인증 오류가 표시되면 **환경 변수** 를 확인하여 Azure Digital Twins에 포함된 자격 증명이 유효한지 확인할 수 있습니다. `DefaultAzureCredential`은 [특정 순서](/dotnet/api/overview/azure/identity-readme#defaultazurecredential)로 자격 증명 형식에 대해 인증을 시도하고 환경 변수를 먼저 평가합니다.

Microsoft에서 **요청된 권한** 팝업 창을 표시하는 경우 이 애플리케이션에 대한 동의를 허용하고 수락하여 계속 진행합니다.

>[!NOTE]
> 언제든지 동일한 아이콘을 선택하여 **로그인** 상자를 다시 열어서 이 정보를 다시 방문/편집할 수 있습니다. 전달한 값을 유지합니다.

## <a name="add-the-sample-data"></a>샘플 데이터 추가

다음으로, 샘플 시나리오와 그래프를 Azure Digital Twins Explorer로 가져옵니다. 샘플 시나리오는 이전에 다운로드한 **digital-twins-explorer-main** 폴더에도 있습니다.

### <a name="models"></a>모델

Azure Digital Twins 솔루션의 첫 번째 단계는 환경의 어휘를 정의하는 것입니다. 환경에 존재하는 엔터티 형식을 설명하는 사용자 지정 [모델](concepts-models.md)을 만들겠습니다.

각 모델은 DTDL(디지털 트윈 정의 언어)이라고 하는 JSON-LD와 같은 언어로 작성됩니다. 각 모델은 **속성**, **원격 분석**, **관계** 및  **구성 요소** 의 측면에서 단일 엔터티 형식을 설명합니다. 나중에 이러한 형식의 특정 인스턴스를 나타내는 디지털 트윈의 기반으로 이러한 모델을 사용합니다.

일반적으로 모델을 만들 때 다음 세 가지 단계를 완료하게 됩니다.

1. 모델 정의를 작성합니다. 이 단계는 빠른 시작에서 이미 샘플 솔루션의 일부로 수행되었습니다.
1. 유효성을 검사하여 구문이 정확한지 확인합니다. 이 단계는 빠른 시작에서 이미 샘플 솔루션의 일부로 수행되었습니다.
1. Azure Digital Twins 인스턴스에 업로드합니다.
 
이 빠른 시작에서는 모델 파일이 이미 작성되어 유효성 검사를 마쳤습니다. 여러분이 다운로드한 솔루션에 포함되어 있습니다. 이 섹션에서는 미리 작성된 두 가지 모델을 인스턴스에 업로드하여 건물 환경의 다음 구성 요소를 정의합니다.

* Floor
* 공간

#### <a name="upload-models"></a>모델 업로드

다음 단계에 따라 모델을 업로드합니다.

1. **모델** 패널에서 **모델 업로드** 아이콘을 선택합니다.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/upload-model.png" alt-text="모델 패널에서 가운데 아이콘이 강조 표시되어 있습니다. 이 아이콘은 클라우드를 가리키는 화살표를 보여 줍니다." lightbox="media/quickstart-azure-digital-twins-explorer/upload-model.png":::
 
1. 표시되는 파일 선택기 창에서 다운로드한 리포지토리의 **digital-twins-explorer-main/client/examples** 폴더로 이동합니다.
1. **Room.json** 및 **Floor.json** 을 선택하고, **확인** 을 선택합니다. 원한다면 추가 모델을 업로드할 수 있지만, 이 빠른 시작에서는 사용되지 않습니다.

이제 Azure Digital Twins Explorer에서 이러한 모델 파일을 Azure Digital Twins 인스턴스에 업로드합니다. **모델** 패널에 표시되며, 식별 이름과 전체 모델 ID를 보여줍니다. **모델 보기** 정보 아이콘을 선택하면 숨겨져 있는 DTDL 코드를 볼 수 있습니다.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/model-info.png" alt-text="내부에 층(dtmi:example:Floor;1) 및 방(dtmi:example:Room;1)의 두 가지 모델 정의가 나열된 모델 보기 패널의 보기입니다. 원 안에 'i'라는 문자를 보여주는 [모델 정보 보기]’ 아이콘이 각 모델에서 강조 표시되어 있습니다." lightbox="media/quickstart-azure-digital-twins-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>쌍 및 쌍 그래프

이제 일부 모델이 Azure Digital Twins 인스턴스에 업로드되었으므로 모델 정의를 따르는 [디지털 쌍](concepts-twins-graph.md)을 추가할 수 있습니다.

디지털 트윈은 비즈니스 환경 내의 실제 엔터티를 나타냅니다. 팜의 센서, 자동차의 조명, 이 빠른 시작에 나오는 건물의 방을 예로 들 수 있습니다. 모두 Room 모델을 사용하는 여러 채팅방처럼 지정된 모델 형식의 여러 트윈을 만들 수 있습니다. 전체 환경을 나타내는 **트윈 그래프** 에 관계를 연결합니다.

이 섹션에서는 미리 만들어진 그래프에 연결된 미리 만들어진 트윈을 업로드합니다. 그래프에는 다음 레이아웃으로 연결된 두 개의 층과 두 개의 방이 있습니다.

* Floor0
    - Room0 포함
* Floor1
    - Room1 포함

#### <a name="import-the-graph"></a>그래프 가져오기

다음 단계에 따라 그래프를 가져옵니다.

1. **트윈 그래프** 패널에서 **그래프 가져오기** 아이콘을 선택합니다.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-graph.png" alt-text="그래프 보기 패널에서 아이콘이 강조 표시되어 있습니다. 이 아이콘은 클라우드를 가리키는 화살표를 보여줍니다." lightbox="media/quickstart-azure-digital-twins-explorer/import-graph.png":::

2. 파일 선택기 창에서 **digital-twins-explorer-main/client/examples** 폴더로 이동하여 **buildingScenario.xlsx** 스프레드시트 파일을 선택합니다. 이 파일에는 샘플 그래프에 대한 설명이 포함되어 있습니다. **확인** 을 선택합니다.

   몇 초 후 Azure Digital Twins Explorer에서 **가져오기** 보기가 열리고 로드할 그래프의 미리 보기가 표시됩니다.

3. 그래프 업로드를 확인하려면 **트윈 그래프** 패널의 오른쪽 위 모서리에서 **저장** 아이콘을 선택합니다.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png" alt-text="[그래프 미리 보기] 창에서 [저장] 아이콘이 강조 표시되어 있습니다." lightbox="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. 이제 Azure Digital Twins Explorer에서 업로드된 파일을 사용하여 요청된 트윈 및 이러한 트윈 간의 관계를 만듭니다. 완료되면 대화 상자가 나타납니다. **닫기** 를 선택합니다.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-success.png" alt-text="그래프 가져오기가 성공했음을 나타내는 대화 상자이며 다음과 같은 메시지를 보여줍니다. '가져오기를 완료했습니다. 4개 쌍을 가져왔습니다. 2개 관계를 가져왔습니다.'" lightbox="media/quickstart-azure-digital-twins-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. 이제 그래프가 Azure Digital Twins Explorer에 업로드되었습니다. **트윈 그래프** 패널로 다시 전환합니다.
 
   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/twin-graph-tab.png" alt-text="트윈 그래프 탭이 강조 표시됩니다." lightbox="media/quickstart-azure-digital-twins-explorer/twin-graph-tab.png":::

6. 그래프를 보려면 Azure Digital Twins Explorer 창 상단 근처에 있는 **쿼리 탐색기** 패널에서 **쿼리 실행** 단추를 선택합니다.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/run-query.png" alt-text="창의 오른쪽 위 모서리에 있는 [쿼리 실행] 단추가 강조 표시됩니다." lightbox="media/quickstart-azure-digital-twins-explorer/run-query.png":::

이 작업은 모든 디지털 트윈을 선택하고 표시하는 기본 쿼리를 실행합니다. Azure Digital Twins Explorer는 서비스의 모든 트윈과 관계를 검색합니다. 그리고 그러한 관계를 통해 정의된 그래프를 **트윈 그래프** 패널에 그립니다.

## <a name="explore-the-graph"></a>그래프 살펴보기

이제 샘플 시나리오의 업로드된 그래프가 표시됩니다.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="내부에 트윈 그래프가 있는 [그래프 보기] 패널의 보기입니다. 'floor1'이라는 레이블이 지정된 원은 'contains'라는 레이블이 지정된 화살표를 통해 'room1'이라는 레이블이 지정된 원에 연결됩니다. 'floor0'이라는 레이블이 지정된 원은 'contains'라는 레이블이 지정된 화살표를 통해 'room0'이라는 레이블이 지정된 원에 연결됩니다.":::

원(그래프 "노드")은 디지털 트윈을 나타냅니다. 선은 관계를 나타냅니다. Floor0 트윈에는 Room0이 포함되고, Floor1 트윈에는 Room1이 포함됩니다.

마우스를 사용하는 경우 그래프 조각을 끌어서 이동할 수 있습니다.

### <a name="view-twin-properties"></a>쌍 속성 보기

트윈을 선택하여 **속성** 패널에서 속성과 해당 값의 목록을 볼 수 있습니다.

Room0의 속성은 다음과 같습니다.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room0.png" alt-text="Room0의 속성을 보여주는 속성 패널을 중심으로 강조 표시되어 있습니다. 여기에는 Room0의 $dtId 필드, 70을 나타내는 Temperature 필드, 30을 나타내는 Humidity 필드가 우선적으로 포함되어 있습니다." lightbox="media/quickstart-azure-digital-twins-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room0의 온도는 70입니다.

Room1의 속성은 다음과 같습니다.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room1.png" alt-text="Room1의 속성을 보여주는 속성 패널을 중심으로 강조 표시되어 있습니다. 여기에는 Room1의 $dtId 필드, 80을 나타내는 Temperature 필드, 60을 나타내는 Humidity 필드가 우선적으로 포함되어 있습니다." lightbox="media/quickstart-azure-digital-twins-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room1의 온도는 80입니다.

### <a name="query-the-graph"></a>그래프 쿼리

Azure Digital Twins의 주요 기능은 환경에 대한 질문에 답하도록 쉽고 효율적으로 트윈 그래프를 [쿼리](concepts-query-language.md)하는 기능입니다.

그래프에서 쌍을 쿼리하는 한 가지 방법은 **속성** 을 사용하는 것입니다. 속성 기반 쿼리를 통해 다양한 질문에 대답할 수 있습니다. 예를 들어 사용자 환경에서 주의가 필요할 수 있는 이상값을 찾을 수 있습니다.

이 섹션에서는 환경에서 온도가 75보다 높은 트윈이 몇 개인지 묻는 질문에 대답하는 쿼리를 실행합니다.

대답을 확인하려면 **쿼리 탐색기** 패널에서 다음 쿼리를 실행합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="TemperatureQuery":::

이전의 트윈 속성을 살펴보면 Room0의 온도가 70이고 Room1의 온도가 80입니다. 따라서 Room1만 결과에 표시됩니다.
    
:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png" alt-text="Room1만 보여주는 속성 쿼리의 결과" lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png":::

>[!TIP]
> 다른 비교 연산자(<,>, = 또는 !=)도 위의 쿼리 내에서 지원됩니다. 이러한 연산자, 다른 값 또는 다른 트윈 속성을 쿼리에 연결하여 사용자 고유의 질문에 대답해 볼 수 있습니다.

## <a name="edit-data-in-the-graph"></a>그래프에서 데이터 편집

Azure Digital Twins Explorer를 사용하여 그래프에 표시된 쌍의 속성을 편집할 수 있습니다. 이 섹션에서는 Room0의 온도를 76으로 높이겠습니다.

시작 하려면 다음 쿼리를 다시 실행하여 모든 디지털 트윈을 선택합니다. 그러면 **트윈 그래프** 패널에 전체 그래프가 한 번 더 표시됩니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="GetAllTwins":::

**Room0** 을 선택하여 **속성** 패널에 속성 목록을 표시합니다.

이 목록의 속성은 편집할 수 있습니다. 새 값을 입력하도록 설정하려면 온도 값으로 **70** 을 선택합니다. **76** 을 입력하고, **저장** 아이콘을 선택하여 온도를 **76** 으로 업데이트합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png" alt-text="Room0의 속성을 보여주는 속성 패널입니다. 온도 값은 76을 표시하는 편집 가능한 상자이며 [저장] 아이콘을 중심으로 강조 표시되어 있습니다." lightbox="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

이제 업데이트하기 위해 Azure Digital Twins [API](concepts-apis-sdks.md)를 사용하여 백그라운드에서 사용된 패치 코드를 표시하는 **패치 정보** 창이 표시됩니다. **닫기** 를 선택합니다.

### <a name="query-to-see-the-result"></a>결과를 확인하는 쿼리

그래프가 Room0의 온도 업데이트를 성공적으로 등록했는지 확인하려면 이전 쿼리를 다시 실행하여 환경에서 온도가 75를 초과하는 모든 트윈을 가져옵니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="TemperatureQuery":::

이제 Room0의 온도가 70에서 76으로 변경되었으므로 두 쌍이 모두 결과에 표시됩니다.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png" alt-text="Room0 및 Room1을 모두 보여주는 속성 쿼리의 결과" lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>학습 검토 및 컨텍스트화

이 빠른 시작에서는 Azure Digital Twins 인스턴스를 만들고, Azure Digital Twins Explorer에 연결하고, 샘플 시나리오로 채웠습니다.

그런 다음, 다음을 수행하여 그래프를 살펴보았습니다.

* 쿼리를 사용하여 시나리오에 대한 질문에 대답합니다.
* 디지털 쌍의 속성을 편집합니다.
* 쿼리를 다시 실행하여 업데이트의 결과로 대답이 변경되는 상황을 확인합니다.

이 연습의 목적은 환경이 계속 변경되는 경우에도 Azure Digital Twins 그래프를 사용하여 환경에 대한 질문에 대답하는 방법을 보여 주기 위한 것입니다.

이 빠른 시작에서는 온도를 수동으로 업데이트했습니다. Azure Digital Twins에서는 디지털 트윈을 실제 IoT 디바이스에 연결하여 원격 분석 데이터를 기반으로 업데이트를 자동으로 받는 것이 일반적입니다. 이 방법을 통해 항상 환경의 실제 상태를 반영하는 라이브 그래프를 작성할 수 있습니다. 쿼리를 사용하여 환경에서 발생하는 상황에 대한 정보를 실시간으로 가져올 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작의 작업을 마치려면 먼저 실행 중인 콘솔 앱을 종료합니다. 그러면 브라우저에서 Azure Digital Twins Explorer 앱 연결이 종료됩니다. 더 이상 브라우저에서 라이브 데이터를 볼 수 없습니다. 브라우저 탭을 닫을 수 있습니다.

그러고 나서 다음에 수행하려는 작업에 따라 제거할 리소스를 선택할 수 있습니다.

* **Azure Digital Twins 자습서를 계속 진행할 생각이라면** 이 빠른 시작의 인스턴스를 해당 문서에 다시 사용할 수 있으므로 제거할 필요가 없습니다.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

로컬 머신에서 프로젝트 폴더를 삭제해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

다음으로, Azure Digital Twins 자습서로 계속 진행하여 사용자 고유의 Azure Digital Twins 시나리오와 상호 작용 도구를 빌드합니다.

> [!div class="nextstepaction"]
> [자습서: 클라이언트 앱 코딩](tutorial-code.md)
