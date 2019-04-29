---
title: 연결된 팩터리 토폴로지 구성 - Azure | Microsoft Docs
description: 연결된 팩터리 솔루션 가속기의 토폴로지를 구성하는 방법입니다.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 3fd160fbccfb5298cefed6a731797ca6962b997c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450026"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>연결된 팩터리 솔루션 가속기 구성

연결된 팩터리 솔루션 가속기는 가상 회사의 Contoso에 대한 시뮬레이트된 대시보드를 보여 줍니다. 이 회사는 전 세계 여러 위치에 팩터리를 보유합니다.

이 문서에서는 Contoso를 예로 사용하여 연결된 팩터리 솔루션의 토폴로지 구성 방법을 설명합니다.

## <a name="simulated-factories-configuration"></a>시뮬레이트된 팩터리 구성

각 Contoso 팩터리에는 각각이 3개의 스테이션으로 구성된 생산 라인이 있습니다. 각 스테이션은 특정 역할을 갖는 실제 OPC UA 서버입니다.

* 조립 스테이션
* 테스트 스테이션
* 포장 스테이션

이러한 OPC UA 서버에는 OPC UA 노드가 있으며 [OPC 게시자](https://github.com/Azure/iot-edge-opc-publisher)는 연결된 팩터리를 이러한 노드 값을 보냅니다. 다음 내용이 포함됩니다.

* 현재 전력 소비와 같은 현재 작동 상태
* 생산된 제품의 수와 같은 프로덕션 정보

대시보드를 사용하여 전역 보기에서 스테이션 수준 보기로 Contoso 팩터리 토폴로지 드릴할 수 있습니다. 연결된 팩터리 대시보드를 사용하여 다음을 수행할 수 있습니다.

* 토폴로지의 각 계층에 대한 OEE 및 KPI 수치 시각화
* 스테이션의 OPC UA 노드 현재 값의 시각화
* 스테이션 수준에서 전역 수준까지 OEE 및 KPI 수치 집계
* 값이 특정 임계값에 도달하는 경우 수행할 작업 및 작업 시각화

## <a name="connected-factory-topology"></a>연결된 팩터리 토폴로지

팩터리, 생산 라인 및 스테이션의 토폴로지는 계층 구조로 이루어져 있습니다.

* 전역 수준에서 팩터리 노드는 자식 항목입니다.
* 팩터리에서 생산 라인 노드는 자식 항목입니다.
* 생산 라인에서 스테이션 노드는 자식 항목입니다.
* 스테이션(OPC UA 서버)에서 OPC UA 노드는 자식 항목입니다.

토폴로지의 모든 노드에는 다음을 정의하는 속성의 공통 집합이 있습니다.

* 토폴로지 노드에 대한 고유 식별자
* 이름
* 설명
* 이미지
* 토폴로지 노드의 자식
* OEE 및 KPI 수치의 최소, 대상 및 최대값과 실행할 경고 작업

## <a name="topology-configuration-file"></a>토폴로지 구성 파일

이전 섹션에 나열된 속성을 구성하기 위해 연결된 팩터리 솔루션은 [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json)이라는 구성 파일을 사용합니다.

`WebApp/Contoso/Topology` 폴더의 솔루션 소스 코드에서 이 파일을 찾을 수 있습니다.

다음 코드 조각은 `ContosoTopologyDescription.json` 구성 파일을 간략히 보여 줍니다.

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

 `<global_configuration>`, `<factory_configuration>`, `<production_line_configuration>` 및 `<station_configuration>`의 공통 속성은 다음과 같습니다.

* **Name**(문자열 형식)

  대시보드에 표시할 토폴로지 노드를 설명하는 하나의 단어로만 이루어진 이름을 정의합니다.

* **Description**(문자열 형식)

  토폴로지 노드를 좀 더 자세히 설명합니다.

* **Image**(문자열 형식)

  토폴로지 노드에 대한 정보가 대시보드에 표시될 때 표시할 WebApp 솔루션에 대한 이미지의 경로입니다.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2**(`<performance_definition>` 형식)

  이러한 정의는 경고를 생성하는 데 사용되는 운영 수치의 최소값, 대상 및 최대값을 정의합니다. 이러한 속성은 경고가 검색되는 경우에 실행한 작업도 정의합니다.

`<factory_configuration>` 및 `<production_line_configuration>` 항목은 다음과 같은 속성을 갖습니다.

* **Guid**(문자열 형식)

  토폴로지 노드를 고유하게 식별합니다.

`<factory_configuration>`은 다음과 같은 속성을 갖습니다.

* **Location**(`<location_definition>` 형식)

  팩터리의 위치를 지정합니다.

`<station_configuration>`은 다음과 같은 속성을 갖습니다.

* **OpcUri**(문자열 형식)

  이 속성은 OPC UA 서버의 OPC UA 애플리케이션 URI로 설정되어야 합니다.
  OPC UA 사양에 따라 전역적으로 고유해야 하므로 이 속성은 스테이션 토폴로지에 노드를 식별하는 데 사용됩니다.

* OPC UA 노드의 배열인 **OpcNodes**(`<opc_node_description>` 형식)

`<location_definition>`은 다음과 같은 속성을 갖습니다.

* **City**(문자열 형식)

  위치와 가장 가까운 도시의 이름입니다.

* **Country**(문자열 형식)

  위치의 국가입니다.

* **Latitude**(double 형식)

  위치의 위도입니다.

* **Longitude**(double 형식)

  위치의 경도입니다.

`<performance_definition>`은 다음과 같은 속성을 갖습니다.

* **Minimum**(double 형식)

  값이 도달할 수 있는 하한 임계값입니다. 현재 값이 이 임계값 아래로 떨어지면 경고가 생성됩니다.

* **Target**(double 형식)

  이상적인 대상 값입니다.

* **Maximum**(double 형식)

  값이 도달할 수 있는 상한 임계값입니다. 현재 값이 이 임계값 위로 올라가면 경고가 생성됩니다.

* **MinimumAlertActions**(`<alert_action>` 형식)

  최소 경고에 대한 응답으로 수행할 수 있는 작업 집합을 정의합니다.

* **MaximumAlertActions**(`<alert_action>` 형식)

  최대 경고에 대한 응답으로 수행할 수 있는 작업 집합을 정의합니다.

`<alert_action`>은 다음과 같은 속성을 갖습니다.

* **Type**(문자열 형식)

  경고 작업의 형식입니다. 다음과 같은 형식이 알려져 있습니다.

  * **AcknowledgeAlert**: 경고의 상태가 승인됨으로 변경되어야 합니다.
  * **CloseAlert**: 동일한 유형의 모든 이전 경고는 대시보드에 더 이상 표시되지 않아야 합니다.
  * **CallOpcMethod**: OPC UA 메서드가 호출되어야 합니다.
  * **OpenWebPage**: 추가 컨텍스트 정보를 보여 주는 브라우저 창이 열려야 합니다.

* **Description**(문자열 형식)

  대시보드에 표시되는 작업에 대한 설명입니다.

* **Parameter**(문자열 형식)

  작업을 실행하는 데 필요한 매개 변수입니다. 값은 작업 형식에 따라 다릅니다.

  * **AcknowledgeAlert**: 매개 변수가 필요하지 않습니다.
  * **CloseAlert**: 매개 변수가 필요하지 않습니다.
  * **CallOpcMethod**: “부모 노드 NodeId” 형식으로 호출할 OPC UA 메서드의 노드 정보 및 매개 변수, 호출할 메서드의 NodeId, OPC UA 서버의 URI매개 변수입니다.
  * **OpenWebPage**: 브라우저 창에 표시할 URL입니다.

`<opc_node_description>`에는 스테이션(OPC UA 서버)의 OPC UA 노드에 대한 정보를 포함합니다. 기존 OPC UA 노드를 나타내지 않지만 연결된 팩터리의 계산 논리에서 저장소로 사용되는 노드도 유효합니다. 해당 속성은 다음과 같습니다.

* **NodeId**(문자열 형식)

  스테이션(OPC UA 서버의)의 주소 공간에서 OPC UA 노드의 주소입니다. 구문은 NodeId는에 대한 OPC UA 사양에 지정되어야 합니다.

* **SymbolicName**(문자열 형식)

  이 OPC UA 노드의 값이 표시될 때 대시보드에 표시할 이름입니다.

* **Relevance**(문자열 형식의 배열)

  OEE 또는 KPI OPC UA 노드 값의 계산이 관련되어 있음을 나타냅니다. 각 배열 요소는 다음 값 중 하나일 수 있습니다.

  * **OeeAvailability_Running**: 이 값은 OEE 가용성 계산과 관련되어 있습니다.
  * **OeeAvailability_Fault**: 이 값은 OEE 가용성 계산과 관련되어 있습니다.
  * **OeePerformance_Ideal**: 값은 OEE 성능의 계산과 관련이 있으며, 일반적으로 상수 값입니다.
  * **OeePerformance_Actual**: 값은 OEE 성능의 계산과 관련이 있습니다.
  * **OeeQuality_Good**: 이 값은 OEE 품질의 계산과 관련이 있습니다.
  * **OeeQuality_Bad**: 이 값은 OEE 품질의 계산과 관련이 있습니다.
  * **Kpi1**: 이 값은 KPI1의 계산과 관련이 있습니다.
  * **Kpi2**: 이 값은 KPI2의 계산과 관련이 있습니다.

* **OpCode**(문자열 형식)

  OPC UA 노드의 값이 OPC UA 노드의 값이 Time Series Insigh 쿼리 및 OEE/KPI 계산에서 처리되는 방식을 나타냅니다. 각 Time Series Insight 쿼리는 쿼리의 매개 변수에 해당하고 결과를 전달하는 특정 시간 범위를 대상으로 합니다. OpCode는 결과 계산 방식을 제어하고, 다음 값 중 하나일 수 있습니다.

  * **Diff**: 시간 범위에서 마지막 값과 첫 번째 값 간의 차이입니다.
  * **Avg**: 시간 범위에 있는 모든 값의 평균입니다.
  * **Sum**: 시간 범위에 있는 모든 값의 합계입니다.
  * **Last**: 현재 사용되지 않습니다.
  * **Count**: 시간 범위에 있는 값의 수입니다.
  * **Max**: 시간 범위에 있는 최대값입니다.
  * **Min**: 시간 범위에 있는 최소값입니다.
  * **Const**: 결과는 ConstValue 속성으로 지정된 값입니다.
  * **SubMaxMin**: 최대값과 최소값 간의 차이입니다.
  * **Timespan**: 시간 범위입니다.

* **Units**(문자열 형식)

  대시보드에 표시할 값의 단위를 정의합니다.

* **Visible**(부울 형식)

  값이 대시보드에 표시되어야 하는지 여부를 제어합니다.

* **ConstValue**(double 형식)

  **OpCode**가 **Const**이면 이 속성은 노드의 값입니다.

* **Minimum**(double 형식)

  현재 값이 이 값보다 작으면 최소 경고가 생성됩니다.

* **Maximum**(double 형식)

  현재 값이 이 값보다 크면 최대 경고가 생성됩니다.

* **MinimumAlertActions**(`<alert_action>` 형식)

  최소 경고에 대한 응답으로 수행할 수 있는 작업 집합을 정의합니다.

* **MaximumAlertActions**(`<alert_action>` 형식)

  최대 경고에 대한 응답으로 수행할 수 있는 작업 집합을 정의합니다.

스테이션 수준에서 **시뮬레이션** 개체도 표시됩니다. 이러한 개체는 연결된 팩터리 시뮬레이션을 구성하는 데만 사용되며, 실제 토폴로지를 구성하는 데 사용하면 안 됩니다.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>런타임 시 구성 데이터를 사용하는 방법

구성 파일에 사용되는 모든 속성은 사용 방법에 따라 여러 범주로 그룹화할 수 있습니다. 해당 범주는 다음과 같습니다.

### <a name="visual-appearance"></a>시각적 모양

이 범주의 속성이 연결된 팩터리 대시보드의 시각적 모양을 정의합니다. 다음은 이러한 템플릿의 예입니다.

* 이름
* 설명
* Image
* Location
* Units
* Visible

### <a name="internal-topology-tree-addressing"></a>내부 토폴로지 트리 주소 지정

WebApp은 모든 토폴로지 노드의 정보를 포함하는 내부 데이터 사전을 유지 관리합니다. 속성 **Guid** 및 **OpcUri**는 이 사전에 액세스하기 위한 키로 사용되며, 고유해야 합니다.

### <a name="oeekpi-computation"></a>OEE/KPI 계산

연결된 팩터리 시뮬레이션에 대한 OEE/KPI 수치는 다음에 의해 매개 변수화됩니다.

* OPC UA 노드 값은 계산에 포함되어야 합니다.
* 수치가 원격 분석 값에서 계산되는 방식입니다.

연결된 팩터리는 https://www.oeefoundation.org에서 게시하는 대로 OEE 수식을 사용합니다.

스테이션의 OPC UA 노드 개체를 사용하여 OEE/KPI 계산용으로 태그를 지정할 수 있습니다. **Relevance** 속성은 OPC UA 노드 값을 사용해야 하는 OEE/KPI 수치를 나타냅니다. **OpCode** 속성은 값을 계산에 사용할 방식을 정의합니다.

### <a name="alert-handling"></a>경고 처리

연결된 팩터리는 간단한 최소/최대 임계값 기반 경고 생성 메커니즘을 지원합니다. 해당 경고에 대한 응답으로 구성할 수 있는 미리 정의된 작업에는 여러 가지가 있습니다. 다음 속성은 이 메커니즘을 제어합니다.

* 최대
* 최소
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>원격 분석 데이터와의 상관 관계 지정

마지막 값 시각화 또는 Time Series Insight 쿼리 생성과 같은 특정 작업의 경우, WebApp은 수집된 원격 분석 데이터에 대한 주소 지정 체계가 필요합니다. 연결된 팩터리로 전송된 원격 분석을 내부 데이터 구조에도 저장해야 합니다. 이러한 작업을 사용하도록 설정하는 두 개의 속성 중 하나는 스테이션(OPC UA 서버)에 있고, 다른 하나는 OPC UA 노드 수준에 있습니다.

* **OpcUri**

  원격 분석을 제공하는 OPC UA 서버를 식별(전역 고유)합니다. 수집된 메시지에서 이 속성은 **ApplicationUri**로 전송됩니다.

* **NodeId**

  OPC UA 서버에서 노드 값을 식별합니다. 속성의 형식은 UA OPC 사양에 지정되어야 합니다. 수집된 메시지에서 이 속성은 **NodeId**로 전송됩니다.

원격 분석 데이터가 OPC 게시자를 사용하여 연결된 팩터리로 수집되는 방식에 대한 자세한 내용은 [이](https://github.com/Azure/iot-edge-opc-publisher) GitHub 페이지를 확인하세요.

## <a name="example-how-kpi1-is-calculated"></a>예제: KPI1 계산 방법

`ContosoTopologyDescription.json` 파일의 구성은 OEE/KPI 수치가 계산되는 방식을 제어합니다. 다음 예제에서는 이 파일의 속성이 KPI1의 계산을 제어하는 방식을 보여 줍니다.

연결된 팩터리 KPI1은 지난 시간에 성공적으로 제조된 제품의 수를 측정하는 데 사용됩니다. 연결된 팩터리 시뮬레이션의 각 스테이션(OPC UA 서버)은 이 KPI를 계산하기 위한 원격 분석을 제공하는 OPC UA 노드(`NodeId: "ns=2;i=385"`)를 제공합니다.

이 OPC UA 노드에 대한 구성은 다음 코드 조각과 같습니다.

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

이 구성을 통해 Time Series Insights를 사용하여 이 노드의 원격 분석 값을 쿼리할 수 있습니다. Time Series Insights 쿼리는 다음을 검색합니다.

* 값의 수
* 최소값
* 최대값
* 모든 값의 평균
* 지정된 시간 범위의 모든 고유한 **OpcUri**(**ApplicationUri**), **NodeId** 쌍의 모든 값 합계.

**NumberOfManufactureredProducts** 노드 값의 특징 중 하나는 증가하지만 한다는 것입니다. 시간 범위 내에 제조된 제품 수를 계산하기 위해 연결된 팩터리는 **OpCode** **SubMaxMin**을 사용합니다. 이러한 계산에서 시간 범위 시작의 최소값과 시간 범위 끝의 최대값을 검색합니다.

구성의 **OpCode**는 최대값과 최소값의 차이 결과를 계산하도록 계산 논리를 구성합니다. 해당 결과는 루트(전역) 수준으로 상향식으로 계산된 후 대시보드에 표시됩니다.

## <a name="next-steps"></a>다음 단계

다음으로 [연결된 팩터리 솔루션 가속기를 위해 Windows 또는 Linux에 게이트웨이 배포](iot-accelerators-connected-factory-gateway-deployment.md)를 학습하는 것이 좋습니다.
