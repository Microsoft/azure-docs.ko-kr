---
title: 샘플 시나리오 살펴보기
titleSuffix: Azure Digital Twins
description: ADT 탐색기 샘플을 사용하여 미리 작성된 시나리오를 시각화하고 살펴봅니다.
author: baanders
ms.author: baanders
ms.date: 8/12/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: b199a50443445b983011d755c3ffacaa71131faf
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88893980"
---
# <a name="explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>ADT 탐색기를 사용하여 Azure Digital Twins 샘플 시나리오 살펴보기

Azure Digital Twins를 사용하면 실제 환경의 라이브 모델을 만들어 이와 상호 작용할 수 있습니다. 이는 개별 요소를 **디지털 쌍**으로 모델링한 다음, 라이브 이벤트에 응답하고 정보를 쿼리할 수 있는 지식 **그래프**에 연결하여 수행됩니다.

이 빠른 시작에서는 [**ADT(Azure Digital Twins) 탐색기**](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)라는 샘플 애플리케이션의 지원을 통해 미리 작성된 Azure Digital Twins 그래프를 살펴봅니다. ADT 탐색기를 사용하면 시나리오를 업로드하고, 쌍 및 그래프의 시각적 표현을 보고, 브라우저 기반 시각적 환경을 통해 다른 관리 작업을 수행할 수 있습니다.

이 빠른 시작에 포함된 주요 단계는 다음과 같습니다.

1. Azure Digital Twins 인스턴스 및 ADT 탐색기 설정
1. 미리 작성된 모델 및 그래프 데이터를 업로드하여 샘플 시나리오 생성
1. 만들어진 시나리오 그래프 살펴보기
1. 그래프 변경

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 Azure 구독이 필요합니다. 아직 없는 경우 지금 **[체험 구독을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** .

빠른 시작을 시작하려면 먼저 다음 두 가지 샘플도 다운로드해야 합니다.
* **ADT 탐색기** 샘플 애플리케이션. 이 샘플에는 빠른 시작에서 Azure Digital Twins 시나리오를 로드하고 살펴보는 데 사용하는 기본 앱이 포함되어 있습니다. 앱을 가져오려면 [ADT(Azure Digital Twins) 탐색기](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)로 이동합니다. *ZIP 다운로드* 단추를 눌러 이 샘플 코드의 *.ZIP* 파일을 _**ADT_Explorer.zip**_ 으로 컴퓨터에 다운로드합니다.
* **Azure Digital Twins 시나리오 예제**. 여기에는 사용할 ADT 탐색기에 로드할 미리 작성된 Azure Digital Twins 그래프가 포함되어 있습니다. 시나리오를 가져오려면 [Azure Digital Twins 샘플](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples)로 이동합니다. *ZIP 다운로드* 단추를 눌러 이 샘플 코드의 *.ZIP* 파일을 _**Azure_Digital_Twins_samples.zip**_ 으로 컴퓨터에 다운로드합니다.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Azure Digital Twins 및 ADT 탐색기 설정

Azure Digital Twins를 사용하는 첫 번째 단계는 **Azure Digital Twins 인스턴스**를 설정하는 것입니다. 서비스의 인스턴스가 만들어지면 나중에 빠른 시작에서 예제 데이터로 채울 수 있습니다.

또한 ADT 탐색기가 컴퓨터에서 실행되고 Azure Digital Twins 인스턴스에 액세스할 수 있는 권한을 설정합니다. 이렇게 하면 샘플 앱을 사용하여 인스턴스 및 해당 데이터를 검색할 수 있습니다.

### <a name="set-up-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 설정

인스턴스 및 필요한 인증을 설정하는 가장 간단한 방법은 자동화된 배포 스크립트 샘플을 실행하는 것입니다. [*방법: 인스턴스 및 인증 설정(스크립팅됨)* ](how-to-set-up-instance-scripted.md)의 지침을 따릅니다. 지침에는 각 단계를 성공적으로 완료했으며 새 인스턴스를 사용할 준비가 되었는지 확인하는 단계도 포함되어 있습니다.

이 빠른 시작에서는 인스턴스를 설정할 때 다음 값이 필요합니다. 이러한 값을 다시 수집해야 하는 경우 설정 문서의 해당 섹션에 대한 아래 링크를 사용하여 [Azure Portal](https://portal.azure.com)에서 찾을 수 있습니다.
* Azure Digital Twins 인스턴스 **_호스트 이름_**([포털에서 찾기](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure AD 앱 등록 **_애플리케이션(클라이언트) ID_**([포털에서 찾기](how-to-set-up-instance-portal.md#collect-important-values))
* Azure AD 앱 등록 **_디렉터리(테넌트) ID_**([포털에서 찾기](how-to-set-up-instance-portal.md#collect-important-values))

### <a name="set-adt-explorer-permissions"></a>ADT 탐색기 권한 설정

다음으로, 로컬로 호스팅되는 웹 애플리케이션인 ADT 탐색기를 사용하도록 만든 Azure Digital Twins 인스턴스를 준비합니다. Azure Portal의 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) 페이지를 방문하여 목록에서 앱 등록 이름을 선택합니다.

등록 메뉴에서 *인증*을 선택하고, *+ 플랫폼 추가*를 누릅니다.

:::image type="content" source="media/quickstart-adt-explorer/authentication-pre.png" alt-text="앱 등록에 대한 인증 세부 정보의 Azure Portal 페이지입니다. '플랫폼 추가' 단추를 중심으로 강조 표시되어 있습니다." lightbox="media/quickstart-adt-explorer/authentication-pre.png":::

이어지는 *플랫폼 구성* 페이지에서 *웹*을 선택합니다.
구성 세부 정보를 다음과 같이 입력합니다.
* **리디렉션 URI**: *http://localhost:3000* 이라는 리디렉션 URI를 추가합니다.
* **암시적 허용**: *액세스 토큰* 확인란을 선택합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/authentication-configure-web.png" alt-text="화면 위에서 설명한 정보가 강조 표시된 플랫폼 구성 페이지":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

*구성*을 눌러 완료합니다.

이제 ADT 탐색기에서 사용할 웹 구성이 있습니다. 이는 Azure Portal의 [인증] 탭에 반영됩니다.

:::image type="content" source="media/quickstart-adt-explorer/authentication-post.png" alt-text="앱 등록에 대한 인증 세부 정보의 Azure Portal 페이지입니다. 리디렉션 URI가 http://localhost:3000 이고 암시적 허용을 액세스 토큰에 사용하도록 설정된 웹 플랫폼 섹션을 중심으로 강조 표시되어 있습니다.":::

### <a name="run-and-configure-adt-explorer"></a>ADT 탐색기 실행 및 구성

다음으로, ADT 탐색기 애플리케이션을 실행하고 Azure Digital Twins 인스턴스에 맞게 구성합니다.

다운로드한 _**ADT_Explorer.zip**_ 폴더로 이동하여 압축을 풉니다. *ADT_explorer/client/src* 폴더 위치에서 명령 프롬프트를 엽니다.

`npm install`을 실행하여 필요한 종속성을 모두 다운로드합니다.

그런 다음, `npm run start`를 실행하여 앱을 시작합니다.

몇 초 후 브라우저 창이 열리고 앱이 브라우저에 표시됩니다.

:::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="localhost:3000에서 실행 중인 앱을 보여 주는 브라우저 창입니다. 이 앱은 ADT 탐색기라고 하며, 쿼리 탐색기, 모델 보기, 그래프 보기 및 속성 탐색기에 대한 상자를 포함하고 있습니다. 화면 데이터는 아직 없습니다." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

창 위쪽에서 *로그인* 단추를 눌러 설정한 인스턴스와 작동하도록 ADT 탐색기를 구성합니다. 

:::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="창 위쪽 근처에 있는 로그인 아이콘이 강조 표시된 ADT 탐색기입니다. 이 아이콘은 열쇠의 실루엣과 겹쳐진 사람의 간단한 실루엣을 보여 줍니다." lightbox="media/quickstart-adt-explorer/sign-in.png":::

이전에 [필수 조건](#prerequisites) 섹션에서 수집한 중요 정보를 입력합니다.
* 애플리케이션(클라이언트) ID
* 디렉터리(테넌트) ID
* ADT URL(*https://<instance host name>* 형식)

>[!NOTE]
> 언제든지 동일한 아이콘을 선택하여 로그인 상자를 다시 가져오면 이 정보를 다시 방문/편집할 수 있습니다. 전달한 값을 유지합니다.

Microsoft에서 *요청된 권한* 팝업 창을 표시하는 경우 이 애플리케이션에 대한 동의를 허용하고 수락하여 계속 진행합니다.

## <a name="add-the-sample-data"></a>샘플 데이터 추가

다음으로, 샘플 시나리오와 그래프를 ADT 탐색기로 가져옵니다.

샘플 시나리오는 다운로드한 _**Azure_Digital_Twins_samples.zip**_ 폴더에 있으므로 지금 이 폴더로 이동하여 압축을 풀어야 합니다.

### <a name="models"></a>모델

Azure Digital Twins 솔루션의 첫 번째 단계는 환경에 대한 어휘를 정의하는 것입니다. 이는 사용자 환경에 있는 엔터티 형식을 설명하는 사용자 지정 [**모델**](concepts-models.md)을 만들어 수행됩니다. 

각 모델은 **DTDL(디지털 쌍 정의 언어)** 이라는 JSON-LD와 유사한 언어로 작성되며, *속성*, *원격 분석*, *관계* 및 *구성 요소* 측면에서 단일 형식의 엔터티를 설명합니다. 나중에 이러한 형식의 특정 인스턴스를 나타내는 디지털 쌍의 기반으로 이러한 모델을 사용합니다.

일반적으로 모델을 만드는 경우 다음 세 가지 단계를 완료합니다.
1. 모델 정의를 작성합니다(빠른 시작에서 이미 샘플 솔루션의 일부로 수행되었음).
2. 유효성을 검사하여 구문이 정확한지 확인합니다(빠른 시작에서 이미 샘플 솔루션의 일부로 수행되었음).
3. Azure Digital Twins 인스턴스에 업로드합니다.
 
이 빠른 시작에서는 모델 파일이 이미 작성되고, 유효성이 검사되었으며, 다운로드한 솔루션에 포함되어 있습니다. 이 섹션에서는 미리 작성된 두 가지 모델을 인스턴스에 업로드하여 건물 환경의 다음 구성 요소를 정의합니다.
* Floor
* 공간

#### <a name="upload-models"></a>모델 업로드

*모델 보기* 상자에서 *모델 업로드* 아이콘을 누릅니다.

:::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="모델 보기 상자에서 가운데 아이콘이 강조 표시되어 있습니다. 이 아이콘은 클라우드를 가리키는 화살표를 보여 줍니다." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. 표시되는 파일 선택기 상자에서 다운로드한 리포지토리의 *Azure_Digital_Twins_samples/AdtSampleApp/SampleClientApp/models* 폴더로 이동합니다.
2. *Room.json* 및 *Floor.json*을 선택하고, [확인]을 누릅니다. (원하는 경우 다른 모델을 업로드할 수 있지만, 이 빠른 시작에서는 사용되지 않습니다.)
3. Azure 계정에 로그인하라는 팝업 대화 상자를 따릅니다.

>[!NOTE]
>다음 오류 메시지가 표시되는 경우 :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="'오류: 모델을 가져오는 동안 오류가 발생했습니다. ClientAuthError: 팝업 창을 여는 동안 오류가 발생했습니다. IE를 사용하거나 브라우저에서 팝업이 차단되면 이 문제가 발생할 수 있습니다.'를 보여 주고 아래쪽에 닫기 단추가 있는 팝업입니다." border="false"::: 
> 팝업 차단을 사용하지 않도록 설정하거나 다른 브라우저를 사용해 보세요.

이제 ADT 탐색기에서 이러한 모델 파일을 Azure Digital Twins 인스턴스에 업로드합니다. *모델 보기* 상자에 표시되며, 이름 및 전체 모델 ID를 보여 줍니다. *모델 보기* 정보 풍선을 클릭하여 숨겨져 있는 DTDL 코드를 볼 수 있습니다.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="내부에 층(dtmi:example:Floor;1) 및 방(dtmi:example:Room;1)의 두 가지 모델 정의가 나열된 '모델 보기' 상자의 보기입니다. 각 모델에 대해 원형 'i' 문자를 보여 주는 '모델 보기' 아이콘이 강조 표시되어 있습니다." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>쌍 및 쌍 그래프

이제 일부 모델이 Azure Digital Twins 인스턴스에 업로드되었으므로 모델 정의를 따르는 [**디지털 쌍**](concepts-twins-graph.md)을 추가할 수 있습니다. 

디지털 쌍은 농장의 센서, 자동차의 조명 또는 이 빠른 시작의 건물 층에 있는 방과 같은 비즈니스 환경 내의 실제 엔터티를 나타냅니다. 지정된 모델 유형의 여러 쌍(예: 모두 *방* 모델을 사용하는 여러 개의 방)을 만들고, 관계를 사용하여 전체 환경을 나타내는 **쌍 그래프**에 연결할 수 있습니다.

이 섹션에서는 미리 만들어진 그래프에 연결된 미리 만들어진 쌍을 업로드합니다. 그래프에는 다음 레이아웃으로 연결된 두 개의 층과 두 개의 방이 있습니다.
* *Floor0*
    - *Room0* 포함
* *Floor1*
    - *Room1* 포함

#### <a name="import-the-graph"></a>그래프 가져오기

*그래프 보기* 상자에서 *그래프 가져오기* 아이콘을 누릅니다.

:::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="그래프 보기 상자에서 아이콘이 강조 표시되어 있습니다. 이 아이콘은 클라우드를 가리키는 화살표를 보여 줍니다." lightbox="media/quickstart-adt-explorer/import-graph.png":::

파일 선택기 상자에서 *client/examples* 폴더로 다시 이동하고, _**buildingScenario.xlsx**_ 스프레드시트 파일을 선택합니다. 이 파일에는 샘플 그래프에 대한 설명이 포함되어 있습니다. [확인]을 누릅니다.

몇 초 후 ADT 탐색기에서 로드될 그래프의 미리 보기를 표시하는 *가져오기* 보기를 엽니다.

그래프 업로드를 확인하려면 *그래프 보기*의 오른쪽 위 모서리에서 *저장* 아이콘을 누릅니다.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="그래프 미리 보기 창에서 저장 아이콘이 강조 표시되어 있습니다." lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

이제 ADT 탐색기에서 업로드된 파일을 사용하여 요청된 쌍 및 이러한 쌍들 간의 관계를 만듭니다. 완료되었음을 나타내는 대화 상자가 표시됩니다. *닫기*를 누릅니다.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="그래프 가져오기 성공을 나타내는 대화 상자입니다. '가져오기를 완료했습니다. 49개 쌍을 가져왔습니다. 50개 관계를 가져왔습니다.'" lightbox="media/quickstart-adt-explorer/import-success.png":::라고 보여 줍니다.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

이제 그래프가 ADT 탐색기에 업로드되었습니다. 그래프를 보려면 ADT 탐색기 창 위쪽의 *그래프 탐색기* 상자에서 *쿼리 실행* 단추를 누릅니다. 

:::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="창 위쪽에서 '쿼리 실행'이라는 단추가 강조 표시되어 있습니다." lightbox="media/quickstart-adt-explorer/run-query.png":::

그러면 모든 디지털 쌍을 선택하고 표시하는 기본 쿼리가 실행됩니다. ADT 탐색기에서 서비스로부터 모든 쌍과 관계를 검색하고, 이를 통해 정의된 그래프를 *그래프 보기* 상자에 그립니다.

## <a name="explore-the-graph"></a>그래프 살펴보기

이제 샘플 시나리오의 업로드된 그래프가 표시됩니다.

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="내부에 쌍 그래프가 있는 '그래프 보기' 상자의 보기입니다. 'floor1'이라는 레이블이 지정된 원은 '포함'이라는 레이블이 지정된 화살표로 'room1'이라는 레이블이 지정된 원에 연결되고, 'floor0'이라는 레이블이 지정된 원은 '포함'이라는 레이블이 지정된 화살표로 'room0'이라는 레이블이 지정된 원에 연결되어 있습니다.":::

원(그래프 "노드")은 디지털 쌍을 나타내며, 선은 관계를 나타냅니다. *Floor0* 쌍에는 *Room0*이 포함되고 *Floor1* 쌍에는 *Room1*이 포함되어 있음을 알 수 있습니다.

마우스를 사용하는 경우 그래프의 조각을 클릭하고 끌어서 이동할 수 있습니다.

### <a name="view-twin-properties"></a>쌍 속성 보기 

쌍을 선택하여 *속성 탐색기* 상자에서 속성과 해당 값의 목록을 볼 수 있습니다. 

*Room0*의 속성은 다음과 같습니다.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="'속성 탐색기' 상자를 중심으로 강조 표시되어 있습니다. 다른 필드 중에서 'Room0'의 $dtId 필드, 70의 온도 필드 및 30의 습도 필드를 포함한 Room0의 속성을 보여 줍니다." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

*Room0*의 온도는 **70**입니다.

*Room1*의 속성은 다음과 같습니다.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="'속성 탐색기' 상자를 중심으로 강조 표시되어 있습니다. 다른 필드 중에서 'Room1'의 $dtId 필드, 80의 온도 필드 및 60의 습도 필드를 포함한 Room1의 속성을 보여 줍니다." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

*Room1*의 온도는 **80**입니다.

### <a name="query-the-graph"></a>그래프 쿼리

Azure Digital Twins의 주요 기능은 환경에 대한 질문에 답하도록 쉽고 효율적으로 트윈 그래프를 [쿼리](concepts-query-language.md)하는 기능입니다. 

그래프에서 쌍을 쿼리하는 한 가지 방법은 *속성*을 사용하는 것입니다. 속성을 기반으로 하여 쿼리하면 주의가 필요한 환경에서 이상값을 찾는 것을 포함하여 다양한 질문에 대답할 수 있습니다.

이 섹션에서는 쿼리를 실행하여 _**내 환경에서 온도가 75를 초과하는 모든 쌍은 무엇인가요?**_ 라는 질문에 대답합니다.

대답을 확인하려면 *쿼리 탐색기* 상자에서 다음 쿼리를 실행합니다.

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

이전의 쌍 속성을 살펴보면 *Room0*의 온도가 **70**이고 *Room1*의 온도가 **80**입니다. 따라서 _**Room1**_ 만 결과에 표시됩니다.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Room1만 보여 주는 속성 쿼리의 결과" lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> 다른 비교 연산자( *<* , *>* , *=* 또는 *!=* )도 위의 쿼리 내에서 지원됩니다. 이러한 값, 다른 값 또는 다른 쌍 속성을 쿼리에 연결하여 사용자 고유의 질문에 대답해 볼 수 있습니다.

## <a name="edit-data-in-the-graph"></a>그래프에서 데이터 편집

ADT 탐색기를 사용하여 그래프에 표시된 쌍의 속성을 편집할 수 있습니다. 이 섹션에서는 **_Room0의 온도_를 76으로 높이겠습니다**.

이렇게 하려면 **Room0*을 선택하여 속성 탐색기* 상자에서 해당 속성 목록을 표시합니다.

이 목록의 속성은 편집할 수 있습니다. 새 값을 입력하도록 설정하려면 온도 값으로 **70**을 선택합니다. **76**을 입력하고, *저장* 아이콘을 눌러 온도를 **76**으로 업데이트합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="Room0에 대한 속성을 보여 주는 '속성 탐색기' 상자입니다. 온도 값은 76을 표시하는 편집 가능한 상자이며 [저장] 아이콘을 중심으로 강조 표시되어 있습니다." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

성공적으로 저장되면 업데이트하기 위해 Azure Digital Twins [API](how-to-use-apis-sdks.md)를 사용하여 백그라운드에서 사용된 패치 코드를 표시하는 *패치 정보* 창이 표시됩니다. *닫기*를 누릅니다.

### <a name="query-to-see-the-result"></a>결과를 확인하는 쿼리

그래프가 *Room0*의 온도에 대한 업데이트를 성공적으로 등록했는지 확인하려면 이전 쿼리를 다시 실행하여 환경에서 **온도가 75를 초과하는 모든 쌍을 가져옵니다**.

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

이제 *Room0*의 온도가 **70**에서 **76**으로 변경되었으므로 두 쌍이 모두 결과에 표시됩니다.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Room0 및 Room1을 모두 보여 주는 속성 쿼리의 결과" lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>학습 검토 및 컨텍스트화

이 빠른 시작에서는 Azure Digital Twins 인스턴스를 만들고, ADT 탐색기에 연결하고, 샘플 시나리오로 채웠습니다. 

그런 다음, 다음을 수행하면서 그래프를 살펴보았습니다.
1. 쿼리를 사용하여 시나리오에 대한 질문에 대답합니다.
2. 디지털 쌍의 속성을 편집합니다.
    * 쿼리를 다시 실행하여 업데이트의 결과로 대답이 변경되는 상황을 확인합니다.

이 연습의 목적은 환경이 계속 변경되는 경우에도 Azure Digital Twins 그래프를 사용하여 환경에 대한 질문에 대답하는 방법을 보여 주기 위한 것입니다. 

이 빠른 시작에서는 온도를 수동으로 업데이트했지만, Azure Digital Twins에서는 일반적으로 디지털 쌍을 실제 IoT 디바이스에 연결하여 원격 분석 데이터를 기반으로 하여 업데이트를 자동으로 받습니다. 이렇게 하면 항상 환경의 실제 상태를 반영하는 라이브 그래프를 작성하고, 쿼리를 사용하여 환경에서 발생하는 상황에 대한 정보를 실시간으로 얻을 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작의 작업을 마치려면 먼저 실행 중인 콘솔 앱을 종료합니다. 그러면 브라우저에서 ADT 탐색기 앱에 대한 연결이 종료되고 브라우저에서 라이브 데이터를 더 이상 볼 수 없습니다. 브라우저 탭을 닫을 수 있습니다.

Azure Digital Twins 자습서로 계속 진행하려면 이 빠른 시작에 사용된 인스턴스를 해당 문서에 다시 사용할 수 있으므로 제거할 필요가 없습니다.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

마지막으로, 로컬 컴퓨터에 다운로드한 프로젝트 샘플 폴더(_**ADT_Explorer.zip**_ 및 _**Azure_Digital_Twins_samples.zip**_)를 삭제합니다.

## <a name="next-steps"></a>다음 단계 

다음으로, Azure Digital Twins 자습서로 계속 진행하여 사용자 고유의 Azure Digital Twins 시나리오와 상호 작용 도구를 빌드합니다.

> [!div class="nextstepaction"]
> [*자습서: 클라이언트 앱 코딩*](tutorial-code.md)