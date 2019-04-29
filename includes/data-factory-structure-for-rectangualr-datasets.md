---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309169"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>사각형 데이터 세트의 구조 정의 지정
데이터 세트 JSON의 구조 섹션은 사각형 테이블(행 및 열 포함)에 대한 **선택적** 섹션으로, 해당 테이블에 대한 열 모음을 포함합니다. 형식 변환에 필요한 형식 정보를 제공하거나 열 매핑을 수행하기 위해 구조 섹션을 사용합니다. 다음 섹션에서는 이러한 기능을 자세히 설명합니다. 

각 열에는 다음 속성이 포함됩니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| 이름 |열의 이름입니다. |예 |
| 형식 |열의 데이터 형식입니다. 형식 정보를 지정해야 할 시기에 대한 자세한 내용은 아래의 형식 변환 섹션을 참조하세요. |아닙니다. |
| culture |지정된 형식이 .NET 형식 Datetime 또는 Datetimeoffset일 때 사용할 .NET 기반 culture입니다. 기본값은 "en-us"입니다. |아닙니다. |
| format |지정된 형식이 .NET 형식 Datetime 또는 Datetimeoffset일 때 사용할 형식 문자열입니다. |아닙니다. |

다음 샘플에서는 3개의 열 즉, userid, name 및 lastlogindate가 있는 테이블에 대한 구조 섹션 JSON을 보여줍니다.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

"structure" 정보를 포함할 시기 및 **structure** 섹션에 포함할 항목에 대해서는 다음 지침을 따르세요.

* **구조화된 데이터 원본** 의 경우, 싱크의 특정 열에 대해 특정 원본 열의 열 매핑을 수행하려 하고 해당 이름이 동일하지는 않은 경우(아래 열 매핑 섹션의 세부 정보 참조)에만 "structure" 섹션을 지정해야 합니다. 
  
    위에서 설명했듯이 형식 정보는 "structure" 섹션에서 선택 사항입니다. 구조화된 원본의 경우는 데이터 저장소에서 데이터 세트 정의의 일부로 형식 정보를 이미 사용할 수 있으므로 "structure" 섹션을 포함할 때는 형식 정보를 포함시키지 않아야 합니다.
* **읽기 데이터 원본(특히 Azure Blob)의 스키마인 경우** 스키마 또는 형식 정보를 데이터와 함께 저장하지 않고도 데이터를 저장할 수 있습니다. 이러한 유형의 데이터 원본에서 다음 2가지 경우는 "structure"를 포함해야 합니다.
  * 열 매핑을 수행하려는 경우.
  * 데이터 세트가 복사 작업의 원본이면 "structure"에 형식 정보를 제공할 수 있으며 데이터 팩터리는 싱크에 대한 네이티브 형식으로 변환하기 위해 이 형식 정보를 사용합니다. 자세한 내용은 [Azure Blob 저장소의 데이터 이동](../articles/data-factory/v1/data-factory-azure-blob-connector.md) 을 참조하세요.

### <a name="supported-net-based-types"></a>지원되는 .NET 기반 형식
데이터 팩터리는 Azure Blob과 같은 읽기 데이터 원본의 스키마에 대해 "structure"에 형식 정보를 제공하기 위해 다음과 같은 CLS 규격 .NET 기반 형식 값을 지원합니다.

* Int16
* Int32 
* Int64
* Single
* Double
* Decimal
* Byte[]
* Bool
* String 
* Guid
* Datetime
* Datetimeoffset
* Timespan 

Datetime 및 Datetimeoffset의 경우 사용자 지정 Datetime 문자열의 구문 분석을 용이하게 하려면 "culture" 및 "format" 문자열을 지정할 수도 있습니다. 아래의 형식 변환 샘플을 참조하세요.

