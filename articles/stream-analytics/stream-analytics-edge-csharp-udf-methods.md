---
title: Azure Stream Analytics Edge 작업에 대한 .NET Standard 함수 개발(미리 보기)
description: Stream Analytics Edge 작업에 대한 C# 사용자 정의 함수를 작성하는 방법을 알아봅니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 5df4c9dfe18b02ade3a37717da9c68acbfcf1853
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61480986"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-edge-jobs-preview"></a>Azure Stream Analytics Edge 작업에 대한 .NET Standard 사용자 정의 함수 개발(미리 보기)

Azure Stream Analytics는 이벤트 데이터 스트림에서 변환과 계산을 수행하기 위해 SQL 방식 쿼리 언어를 제공합니다. 많은 기본 제공 함수가 있지만 일부 복잡한 시나리오에는 추가적인 유연성이 필요합니다. .NET Standard UDF(사용자 정의 함수)를 사용하면 .NET 표준 언어(C#, F# 등)로 작성된 사용자 고유의 함수를 호출하여 Stream Analytics 쿼리 언어를 확장할 수 있습니다. UDF를 사용하면 복잡한 수학 계산을 수행하고, ML.NET을 사용하여 사용자 지정 ML 모델을 가져오며, 누락된 데이터에 사용자 지정 대체 논리를 사용할 수 있습니다. Stream Analytics Edge 작업용 UDF 함수는 현재 미리 보기로 제공되며 프로덕션 워크로드에는 사용해서는 안 됩니다.

## <a name="overview"></a>개요
Azure Stream Analytics용 Visual Studio 도구를 사용하면 UDF를 쉽게 작성하고, 작업을 로컬로(심지어 오프라인으로) 테스트하고, Stream Analytics 작업을 Azure에 게시할 수 있습니다. Azure에 게시되면 IoT Hub를 사용하여 작업을 IoT 디바이스에 배포할 수 있습니다.

UDF를 구현하는 다음 세 가지 방법이 있습니다.

* ASA 프로젝트의 CodeBehind 파일
* 로컬 프로젝트의 UDF
* Azure 저장소 계정의 기존 패키지

## <a name="package-path"></a>패키지 경로

모든 UDF 패키지의 형식에는 `/UserCustomCode/CLR/*` 경로가 있습니다. DLL(동적 연결 라이브러리) 및 리소스는 `/UserCustomCode/CLR/*` 폴더 아래에 복사되어 시스템 및 Azure Stream Analytics DLL에서 사용자 DLL을 격리할 수 있습니다. 이 패키지 경로는 사용되는 방법에 관계없이 모든 함수에 사용됩니다.

## <a name="supported-types-and-mapping"></a>지원되는 형식 및 매핑

|**UDF 형식(C#)**  |**Azure Stream Analytics 형식**  |
|---------|---------|
|long  |  bigint   |
|double  |  double   |
|문자열  |  nvarchar(max)   |
|datetime  |  datetime   |
|struct  |  IRecord   |
|object  |  IRecord   |
|Array<object>  |  IArray   |
|dictionary<string, object>  |  IRecord   |

## <a name="codebehind"></a>CodeBehind
사용자 정의 함수는 **Script.asql** CodeBehind에 작성할 수 있습니다. Visual Studio 도구에서 CodeBehind 파일을 어셈블리 파일로 자동으로 컴파일합니다. 어셈블리는 Zip 파일로 패키지되고, Azure에 작업을 제출할 때 저장소 계정에 업로드됩니다. [Stream Analytics Edge 작업에 대한 C# UDF](stream-analytics-edge-csharp-udf.md) 자습서에 따라 CodeBehind를 사용하여 C# UDF를 작성하는 방법을 알아볼 수 있습니다. 

## <a name="local-project"></a>로컬 프로젝트
사용자 정의 함수는 Azure Stream Analytics 쿼리에서 나중에 참조되는 어셈블리에 작성할 수 있습니다. 이는 절차적 논리 또는 재귀와 같은 식 언어 이외의 .NET Standard 언어의 모든 기능이 필요한 복잡한 함수에 권장되는 옵션입니다. 로컬 프로젝트의 UDF는 여러 Azure Stream Analytics 쿼리에서 함수 논리를 공유해야 하는 경우에도 사용할 수 있습니다. UDF를 로컬 프로젝트에 추가하면 Visual Studio에서 함수를 로컬로 디버그하고 테스트할 수 있습니다.

로컬 프로젝트를 참조하려면 다음을 수행합니다.

1. 새 클래스 라이브러리를 솔루션에 만듭니다.
2. 코드를 클래스에 작성합니다. 클래스는 *public*으로, 개체는 *static public*으로 정의해야 합니다. 
3. 프로젝트를 빌드합니다. 도구에서 bin 폴더의 모든 아티팩트를 Zip 파일로 패키지하고 이 Zip 파일을 저장소 계정에 업로드합니다. 외부 참조의 경우 NuGet 패키지 대신 어셈블리 참조를 사용합니다.
4. Azure Stream Analytics 프로젝트에서 새 클래스를 참조합니다.
5. 새 함수를 Azure Stream Analytics 프로젝트에 추가합니다.
6. 어셈블리 경로를 `JobConfig.json` 작업 구성 파일에 구성합니다. [어셈블리 경로]를 **로컬 프로젝트 참조 또는 CodeBehind**로 설정합니다.
7. 함수 프로젝트와 Azure Stream Analytics 프로젝트를 모두 다시 빌드합니다.  

### <a name="example"></a>예

이 예에서 **UDFTest**는 C# 클래스 라이브러리 프로젝트이고, **ASAEdgeUDFDemo**는 Azure Stream Analytics Edge 프로젝트이며 **UDFTest**를 참조합니다.

![Visual Studio의 Azure Stream Analytics IoT Edge 프로젝트](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. C# 프로젝트를 빌드합니다. 이 프로젝트를 통해 Azure Stream Analytics 쿼리에서 C# UDF에 대한 참조를 추가할 수 있습니다.
    
   ![Visual Studio에서 Azure Stream Analytics IoT Edge 프로젝트 빌드](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. C# 프로젝트에 대한 참조를 ASA Edge 프로젝트에 추가합니다. [참조] 노드를 마우스 오른쪽 단추로 클릭하고, [참조 추가]를 선택합니다.

   ![Visual Studio에서 C# 프로젝트에 대한 참조 추가](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. 목록에서 C# 프로젝트 이름을 선택합니다. 
    
   ![참조 목록에서 C# 프로젝트 이름 선택](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. **솔루션 탐색기**의 **참조** 아래에 **UDFTest**가 나열되는지 확인합니다.

   ![솔루션 탐색기에서 사용자 정의 함수 참조 보기](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. **함수** 폴더를 마우스 오른쪽 단추로 클릭하고, **새 항목**을 선택합니다.

   ![Azure Stream Analytics Edge 솔루션의 함수에 새 항목 추가](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. **SquareFunction.json** C# 함수를 Azure Stream Analytics 프로젝트에 추가합니다.

   ![Visual Studio의 Stream Analytics Edge 항목에서 C# 함수 선택](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. **솔루션 탐색기**에서 함수를 두 번 클릭하여 구성 대화 상자를 엽니다.

   ![Visual Studio의 C# 함수 구성](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. C# 함수 구성에서 **ASA 프로젝트 참조에서 로드**를 선택하고, 드롭다운 목록에서 관련 어셈블리, 클래스 및 메서드 이름을 선택합니다. Stream Analytics Edge 쿼리에서 메서드, 형식 및 함수를 참조하려면 클래스를 *public*으로 정의하고 개체를 *static public*으로 정의해야 합니다.

   ![Stream Analytics C# 함수 구성](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>기존 패키지

선택한 IDE에서 .NET Standard UDF를 작성하고 Azure Stream Analytics 쿼리에서 호출할 수 있습니다. 먼저 코드를 컴파일하고 모든 DLL을 패키지합니다. 패키지의 형식은 `/UserCustomCode/CLR/*` 경로입니다. 그런 다음, Azure 저장소 계정의 컨테이너 루트에 `UserCustomCode.zip`을 업로드합니다.

어셈블리 zip 패키지가 Azure 저장소 계정에 업로드되면 Azure Stream Analytics 쿼리에서 함수를 사용할 수 있습니다. Stream Analytics Edge 작업 구성에 저장소 정보를 포함하기만 하면 됩니다. Visual Studio 도구에서 패키지를 다운로드하지 않으므로 이 옵션을 사용하여 함수를 로컬로 테스트할 수 없습니다. 패키지 경로는 서비스에 직접 구문 분석됩니다. 

`JobConfig.json` 작업 구성 파일에서 어셈블리 경로를 구성하려면 다음을 수행합니다.

**사용자 정의 코드 구성** 섹션을 확장하고 다음 제안 값으로 구성을 입력합니다.

 |**설정**  |**제안 값**  |
 |---------|---------|
 |어셈블리 원본  | 클라우드의 기존 어셈블리 패키지    |
 |리소스  |  현재 계정의 데이터 선택   |
 |구독  |  구독을 선택합니다.   |
 |Storage 계정  |  저장소 계정 선택   |
 |컨테이너  |  저장소 계정에서 만든 컨테이너 선택   |

![Visual Studio의 Azure Stream Analytics Edge 작업 구성](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-job-config.png)

## <a name="limitations"></a>제한 사항
UDF 미리 보기에는 현재 다음과 같은 제한 사항이 있습니다.

* .NET Standard 언어는 IoT Edge의 Azure Stream Analytics에만 사용할 수 있습니다. 클라우드 작업의 경우 JavaScript 사용자 정의 함수를 작성할 수 있습니다. 자세한 내용은 [Azure Stream Analytics JavaScript UDF](stream-analytics-javascript-user-defined-functions.md) 자습서를 참조하세요.

* .NET Standard UDF는 Visual Studio에서만 작성하여 Azure에 게시할 수 있습니다. Azure Portal의 **함수** 아래에서 읽기 전용 버전의 .NET Standard UDF를 볼 수 있습니다. Azure Portal에서는 .NET Standard 함수 작성을 지원하지 않습니다.

* 포털에서 .NET Standard UDF를 사용하는 경우 Azure Portal 쿼리 편집기에서 오류를 표시합니다. 

* 사용자 지정 코드는 Azure Stream Analytics 엔진과 컨텍스트를 공유하므로 사용자 지정 코드에서 Azure Stream Analytics 코드와 충돌하는 네임스페이스/dll_name이 있는 항목을 참조할 수 없습니다. 예를 들어 *Newtonsoft Json*은 참조할 수 없습니다.

## <a name="next-steps"></a>다음 단계

* [자습서: Azure Stream Analytics Edge 작업용 C# 사용자 정의 함수 작성(미리 보기)](stream-analytics-edge-csharp-udf.md)
* [자습서: Azure Stream Analytics JavaScript 사용자 정의 함수](stream-analytics-javascript-user-defined-functions.md)
* [Visual Studio를 사용하여 Azure Stream Analytics 작업 보기](stream-analytics-vs-tools.md)
