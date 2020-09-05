---
title: 모델 구문 분석 및 유효성 검사
titleSuffix: Azure Digital Twins
description: 파서 라이브러리를 사용 하 여 DTDL 모델을 구문 분석 하는 방법에 대해 알아봅니다.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 69b52be3a3eca2ab48ed09f6401780ea033f223c
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723982"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>DTDL 파서 라이브러리를 사용 하 여 모델 구문 분석 및 유효성 검사

Azure Digital Twins의 [모델](concepts-models.md) 은 JSON-LD 기반 디지털 Twins 정의 언어 (dtdl)를 사용 하 여 정의 됩니다. **Azure Digital Twins 인스턴스에 업로드 하기 전에 오프 라인으로 모델의 유효성을 검사 하는 것이 좋습니다.**

이 작업을 수행 하는 데 도움이 되도록 NuGet: [**DigitalTwins**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)에 .net 클라이언트 쪽 dtdl 구문 분석 라이브러리가 제공 됩니다. 

파서 라이브러리를 c # 코드에서 직접 사용 하거나, 파서 라이브러리: [**Dtdl Validator 샘플**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)에서 빌드된 언어 독립적인 코드 샘플 프로젝트를 사용할 수 있습니다.

## <a name="use-the-dtdl-validator-sample"></a>DTDL 유효성 검사기 샘플 사용

[**Dtdl 유효성 검사기**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator) 는 dtdl이 유효한 지 확인 하기 위해 모델 문서의 유효성을 검사할 수 있는 샘플 프로젝트입니다. 이 클래스는 .NET 파서 라이브러리를 기반으로 하며 언어를 독립적으로 제공 합니다. 샘플 링크의 *ZIP 다운로드* 단추를 사용 하 여 가져올 수 있습니다.

소스 코드는 파서 라이브러리를 사용 하는 방법에 대 한 예제를 보여 줍니다. Validator 샘플을 명령줄 유틸리티로 사용 하 여 DTDL 파일의 디렉터리 트리의 유효성을 검사할 수 있습니다. 또한 대화형 모드를 제공 합니다.

DTDL Validator 샘플 폴더에서 *readme.md* 파일을 참조 하 여 샘플을 자체 포함 된 실행 파일로 패키징하는 방법에 대 한 지침을 참조 하세요.

자체 포함 된 패키지를 빌드하여 경로에 실행 파일을 추가한 후에는 컴퓨터의 콘솔에서 다음 명령을 사용 하 여 유효성 검사기를 실행할 수 있습니다.

```cmd/sh
DTDLValidator
```

이 샘플에서는 기본 옵션을 사용 하 여 `*.json` 현재 디렉터리와 모든 하위 디렉터리에서 파일을 검색 합니다. 다음 옵션을 추가 하 여 지정 된 디렉터리 및 확장명이 *dtdl*인 파일의 모든 하위 디렉터리에서 샘플 검색을 수행할 수도 있습니다.

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

`-i`샘플에 대 한 옵션을 추가 하 여 대화형 모드를 시작할 수 있습니다.

```cmd/sh
DTDLValidator -i
```

이 샘플에 대 한 자세한 내용은 소스 코드 또는 실행을 참조 하세요 `DTDLValidator --help` .

## <a name="use-the-net-parser-library"></a>.NET 파서 라이브러리 사용 

[**DigitalTwins**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) 라이브러리는 dtdl 정의에 대 한 모델 액세스를 제공 하며, 기본적으로 dtdl에 대 한 c # 리플렉션과 동등한 역할을 합니다. 이 라이브러리는 특히 시각적 개체 또는 텍스트 편집기에서 DTDL 유효성 검사를 위해 [Azure Digital Twins SDK](how-to-use-apis-sdks.md)와 독립적으로 사용할 수 있습니다. 모델 정의 파일을 서비스에 업로드 하기 전에 해당 파일의 유효성을 확인 하는 데 유용 합니다.

파서 라이브러리를 사용 하려면 DTDL 문서 집합을 제공 합니다. 일반적으로 서비스에서 이러한 모델 문서를 검색 하지만 클라이언트가 처음으로 서비스에 업로드 해야 하는 경우 로컬에서 사용할 수 있습니다. 

다음은 파서 사용에 대 한 일반적인 워크플로입니다.
1. 서비스에서 일부 또는 모든 DTDL 문서를 검색 합니다.
2. 반환 된 메모리 내 DTDL 문서를 파서에 전달 합니다.
3. 파서는 전달 된 문서 집합의 유효성을 검사 하 고 자세한 오류 정보를 반환 합니다. 이 기능은 편집기 시나리오에서 유용 합니다.
4. 파서 Api를 사용 하 여 문서 집합에 포함 된 모델을 계속 분석할 수 있습니다. 

파서의 기능에는 다음이 포함 됩니다.
* 구현 된 모든 모델 인터페이스 (인터페이스 섹션의 내용)를 가져옵니다 `extends` .
* 모델에 선언 된 모든 속성, 원격 분석, 명령, 구성 요소 및 관계를 가져옵니다. 이 명령은 또한 이러한 정의에 포함 된 메타 데이터를 모두 가져오고 상속 ( `extends` 섹션)을 고려 합니다.
* 모든 복합 모델 정의를 가져옵니다.
* 모델을 다른 모델에서 할당할 수 있는지 여부를 확인 합니다.

> [!NOTE]
> [PnP (IoT 플러그 앤 플레이](../iot-pnp/overview-iot-plug-and-play.md) ) 장치는 작은 구문 변형을 사용 하 여 기능을 설명 합니다. 이 구문 변형은 Azure Digital Twins에서 사용 되는 DTDL의 의미상 호환 되는 하위 집합입니다. 파서 라이브러리를 사용 하는 경우 디지털 쌍에 대 한 DTDL을 만드는 데 사용 된 구문 변형을 알 필요가 없습니다. 파서는 항상 기본적으로 PnP와 Azure Digital Twins 구문 모두에 대해 동일한 모델을 반환 합니다.

### <a name="code-with-the-parser-library"></a>파서 라이브러리를 사용 하는 코드

사용자 응용 프로그램에서 모델의 유효성을 검사 하거나 동적 모델 기반 UI, 대시보드 및 보고서를 생성 하는 등의 작업을 수행 하기 위해 파서 라이브러리를 직접 사용할 수 있습니다.

아래의 파서 코드 예제를 지원 하려면 Azure Digital Twins 인스턴스에 정의 된 몇 가지 모델을 고려 하세요.

> [!TIP] 
> `dtmi:com:contoso:coffeeMaker`모델은 *기능 모델* 구문을 사용 하며,이는 해당 모델을 노출 하는 PnP 장치를 연결 하 여 서비스에 설치 되었음을 의미 합니다.

```json
{
  "@id": " dtmi:com:contoso:coffeeMaker",
  "@type": "CapabilityModel",
  "implements": [
        { "name": "coffeeMaker", "schema": " dtmi:com:contoso:coffeeMakerInterface" }
  ]    
}
{
  "@id": " dtmi:com:contoso:coffeeMakerInterface",
  "@type": "Interface",
  "contents": [
      { "@type": "Property", "name": "waterTemp", "schema": "double" }  
  ]
}
{
  "@id": " dtmi:com:contoso:coffeeBar",
  "@type": "Interface",
  "contents": [
        { "@type": "relationship", "contains": " dtmi:com:contoso:coffeeMaker" },
        { "@type": "property", "name": "capacity", "schema": "integer" }
  ]    
}
```

다음 코드에서는 파서 라이브러리를 사용 하 여 c #에서 이러한 정의를 반영 하는 방법의 예를 보여 줍니다.

```csharp
async void ParseDemo(DigitalTwinsClient client)
{
    try
    {
        AsyncPageable<ModelData> mdata = client.GetModelsAsync(null, true);
        List<string> models = new List<string>();
        await foreach (ModelData md in mdata)
            models.Add(md.Model);
        ModelParser parser = new ModelParser();
        IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM = await parser.ParseAsync(models);

        List<DTInterfaceInfo> interfaces = new List<DTInterfaceInfo>();
        IEnumerable<DTInterfaceInfo> ifenum = 
            from entity in dtdlOM.Values
            where entity.EntityKind == DTEntityKind.Interface
            select entity as DTInterfaceInfo;
        interfaces.AddRange(ifenum);
        foreach (DTInterfaceInfo dtif in interfaces)
        {
            PrintInterfaceContent(dtif, dtdlOM);
        }

    } catch (RequestFailedException rex)
    {

    }
}

void PrintInterfaceContent(DTInterfaceInfo dtif, IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM, int indent=0)
{
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < indent; i++) sb.Append("  ");
    Console.WriteLine($"{sb}Interface: {dtif.Id} | {dtif.DisplayName}");
    SortedDictionary<string, DTContentInfo> contents = dtif.Contents;
    foreach (DTContentInfo item in contents.Values)
    {
        switch (item.EntityKind)
        {
            case DTEntityKind.Property:
                DTPropertyInfo pi = item as DTPropertyInfo;
                Console.WriteLine($"{sb}--Property: {pi.Name} with schema {pi.Schema}");
                break;
            case DTEntityKind.Relationship:
                DTRelationshipInfo ri = item as DTRelationshipInfo;
                Console.WriteLine($"{sb}--Relationship: {ri.Name} with target {ri.Target}");
                break;
            case DTEntityKind.Telemetry:
                DTTelemetryInfo ti = item as DTTelemetryInfo;
                Console.WriteLine($"{sb}--Telemetry: {ti.Name} with schema {ti.Schema}");
                break;
            case DTEntityKind.Component:
                DTComponentInfo ci = item as DTComponentInfo;
                Console.WriteLine($"{sb}--Component: {ci.Id} | {ci.Name}");
                dtdlOM.TryGetValue(ci.Id, out DTEntityInfo value);
                DTInterfaceInfo component = value as DTInterfaceInfo;
                PrintInterfaceContent(component, dtdlOM, indent + 1);
                break;
            default:
                break;
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

모델 작성을 완료 한 후에는 DigitalTwinsModels Api를 사용 하 여 모델을 업로드 하는 방법 (및 다른 관리 작업을 수행 하는 방법)을 참조 하세요.
* [*방법: 사용자 지정 모델 관리*](how-to-manage-model.md)