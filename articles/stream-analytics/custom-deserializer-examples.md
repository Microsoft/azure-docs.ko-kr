---
title: Azure Stream Analytics에서 .NET 사용자 지정 역직렬 변환기를 사용하여 모든 형식의 입력 읽기
description: 이 문서에서는 Azure Stream Analytics 클라우드 및 에지 작업에 대한 사용자 지정 .NET 역직렬 변환기를 정의하는 인터페이스 및 직렬화 형식에 대해 설명합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4616f6c567b0bba13fe04aed56fd5e4ddc293f90
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008389"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>.NET 사용자 지정 역직렬 변환기를 사용하여 모든 형식의 입력 읽기

.NET 사용자 지정 역직렬 변환기를 사용하면 Azure Stream Analytics 작업에서 세 가지 [기본 제공 데이터 형식](stream-analytics-parsing-json.md)의 외부 형식으로 데이터를 읽을 수 있습니다. 이 문서에서는 Azure Stream Analytics 클라우드 및 에지 작업에 대한 .NET 사용자 지정 역직렬 변환기를 정의하는 인터페이스 및 직렬화 형식에 대해 설명합니다. 프로토콜 버퍼 및 CSV 형식에 대한 역직렬 변환기 예도 있습니다.

## <a name="net-custom-deserializer"></a>.NET 사용자 지정 역직렬 변환기

다음 코드 샘플은 사용자 지정 역직렬 변환기를 정의하고 `StreamDeserializer<T>`를 구현하는 인터페이스입니다.

`UserDefinedOperator`는 모든 사용자 지정 스트리밍 연산자의 기본 클래스입니다. `StreamingContext`를 초기화합니다. 이는 역직렬 변환기와 관련된 문제를 디버깅하는 데 필요한 진단을 게시하기 위해 메커니즘이 포함된 컨텍스트를 제공합니다.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

다음 코드 조각은 스트리밍 데이터에 대한 역직렬화입니다. 

건너뛸 수 있는 오류는 `UserDefinedOperator`의 Initialize 메서드를 통해 전달된 `IStreamingDiagnostics`를 사용하여 내보내야 합니다. 모든 예외가 오류로 처리되며 역직렬 변환기가 다시 만들어집니다. 특정 수의 오류가 발생하면 작업이 실패 상태가 됩니다.

`StreamDeserializer<T>`는 스트림을 `T` 형식의 개체로 역직렬화합니다. 다음 조건이 충족되어야 합니다.

1. T는 클래스 또는 구조체입니다.
1. T의 모든 공용 필드는
    1. [Sbyte, byte, short, ushort, int, uint, long, DateTime, string, float, double] 중 하나이거나 NULL을 허용하는 해당 항목입니다.
    1. 동일한 규칙을 수행하는 다른 구조체 또는 클래스입니다.
    1. 동일한 규칙을 따르는 `T2` 형식의 배열입니다.
    1. T2가 동일한 규칙을 따르는 IList`T2`입니다.
    1. 재귀 형식이 없습니다.

`stream` 매개 변수는 직렬화된 개체를 포함하는 스트림입니다. `Deserialize`는 `T` 인스턴스의 컬렉션을 반환합니다.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext`는 사용자 연산자에 대한 진단을 게시하기 위해 메커니즘이 포함된 컨텍스트를 제공합니다.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics`는 직렬 변환기, 역직렬 변환기 및 사용자 정의 함수를 포함하는 사용자 정의 연산자에 대한 진단입니다.

`WriteError`는 리소스 로그에 오류 메시지를 기록하고 오류를 진단으로 보냅니다.

`briefMessage`는 간단한 오류 메시지입니다. 이 메시지는 진단에 나타나며 제품 팀에서 디버깅 목적으로 사용됩니다. 중요한 정보를 포함하지 않고 메시지를 200자 미만으로 유지합니다.

`detailedMessage`는 스토리지의 리소스 로그에만 추가되는 자세한 오류 메시지입니다. 이 메시지는 2000자 미만이어야 합니다.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>역직렬 변환기 예

이 섹션에서는 Protobuf 및 CSV에 대한 사용자 지정 역직렬 변환기를 작성하는 방법을 보여 줍니다. 이벤트 허브 캡처의 AVRO 형식 등의 추가 예를 보려면 [GitHub의 Azure Stream Analytics](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)를 방문하세요.

### <a name="protocol-buffer-protobuf-format"></a>프로토콜 버퍼(Protobuf) 형식

프로토콜 버퍼 형식의 사용 예입니다.

다음 프로토콜 버퍼 정의를 가정합니다.

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

**Google.Protobuf.Tools** NuGet에서 `protoc.exe`를 실행하면 정의에 의해 .cs 파일이 생성됩니다. 생성된 파일은 여기에 표시되지 않습니다. Stream Analytics 프로젝트에서 사용하는 Protobuf Nuget 버전이 입력을 생성하는 데 사용된 Protobuf 버전과 일치하는지 확인해야 합니다. 

다음 코드 조각은 생성된 파일이 프로젝트에 포함된 것으로 가정하는 역직렬 변환기를 구현한 것입니다. 이 구현은 생성된 파일에 대한 씬 래퍼입니다.

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

다음 코드 조각은 전파 오류도 보여 주는 간단한 CSV 역직렬 변환기입니다.

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>REST API에 대한 직렬화 형식

모든 Stream Analytics 입력에는 **직렬화 형식**이 있습니다. 입력 옵션에 대한 자세한 내용은 [REST API 입력](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) 설명서를 참조하세요.

다음 Javascript 코드는 REST API를 사용할 때의 .NET 역직렬 변환기 직렬화 형식 예입니다.

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName`은 `StreamDeserializer<T>`을 구현하는 클래스입니다. 여기에 대해서는 다음 섹션에서 설명합니다.

## <a name="region-support"></a>지역 지원

이 기능은 다음 지역에서 제공됩니다.

* 미국 중서부
* 북유럽
* 미국 동부
* 미국 서부
* 미국 동부 2
* 서유럽

추가 지역에 대해 [지원 요청](https://aka.ms/ccodereqregion)할 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>이 기능은 언제 모든 Azure 지역에서 사용할 수 있나요?

이 기능은 [6개의 지역](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support)에서 사용할 수 있습니다. 다른 지역에서 이 기능을 사용하는 데 관심이 있는 경우 [요청을 제출](https://aka.ms/ccodereqregion)할 수 있습니다. 모든 Azure 지역에 대한 지원은 로드맵에 있습니다.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>GetMetadataPropertyValue 함수와 유사한 내 입력의 MetadataPropertyValue에 액세스할 수 있나요?

이제는 이 기능이 지원되지 않습니다. 이 기능이 필요한 경우 [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese)에서 이 요청에 대해 응답할 수 있습니다.

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>다른 사용자가 혜택을 받을 수 있도록 내 역직렬 변환기 구현을 커뮤니티와 공유할 수 있나요?

역직렬 변환기를 구현한 후 커뮤니티와 공유하여 다른 사용자를 도울 수 있습니다. [Azure Stream Analytics GitHub 리포지토리](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)에 코드를 제출합니다.

### <a name="what-are-the-other-limitation-of-using-custom-deserializers-in-stream-analytics"></a>Stream Analytics에서 사용자 지정 역직렬 변환기를 사용하는 다른 제한은 무엇인가요?

입력이 MapField 유형을 포함하는 스키마를 사용하는 Protobuf 형식인 경우 사용자 지정 역직렬 변환기를 구현할 수 없습니다. 앞으로 이 유형을 지원하기 위해 노력하고 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 클라우드 작업을 위한 .NET 사용자 지정 역직렬 변환기](custom-deserializer.md)
