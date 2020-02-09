---
title: API 형식 지원의 제한 사항 및 세부 정보
titleSuffix: Azure API Management
description: Azure API Management의 Open API, WSDL 및 WADL 형식 지원에 대한 알려진 문제 및 제한 사항에 대한 세부 정보입니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: 61d43addfdf9008cb7aa8a073dcf3bb702cb55f1
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513374"
---
# <a name="api-import-restrictions-and-known-issues"></a>API 가져오기 제한 사항 및 알려진 문제

## <a name="about-this-list"></a>다음 목록 정보

API를 가져올 때 몇 가지 제한 사항이 나 가져오기를 성공적으로 수행 하기 위해 수정 해야 하는 문제를 식별할 수 있습니다. 이 문서에서는 API의 가져오기 형식으로 구성 된 이러한 제한 사항을 설명 합니다. OpenAPI 내보내기가 작동 하는 방법에 대해서도 설명 합니다.

## <a name="open-api"> </a>Openapi/Swagger 가져오기 제한 사항

OpenAPI 문서를 가져올 때 오류가 표시되면 해당 문서의 유효성을 미리 검사했는지 확인하세요. 유효성 검사를 수행할 때는 Azure Portal에서 디자인 - 프런트 엔드 - OpenAPI 사양 편집기를 선택하여 디자이너를 사용할 수도 있고, <a href="https://editor.swagger.io">Swagger Editor</a> 등의 타사 도구를 사용할 수도 있습니다.

### <a name="open-api-general"> </a>일반

-   경로와 쿼리 간에 필수 매개 변수 이름은 고유해야 합니다. OpenAPI에서는 매개 변수 이름이 경로, 쿼리, 헤더 등의 특정 위치 내에서만 고유하면 됩니다. 하지만 API Management에서는 경로 및 쿼리 매개 변수를 기준으로 작업을 구분할 수 있습니다. OpenAPI에서는 이러한 구분이 지원되지 않습니다. 따라서 전체 URL 템플릿 내에서 매개 변수 이름이 고유해야 합니다.
-   `\$ref` 포인터는 외부 파일을 참조할 수 없습니다.
-   `x-ms-paths` 및 `x-servers` 유일 하 게 지원 되는 확장입니다.
-   사용자 지정 확장명은 가져오기 시 무시되며 내보내기용으로 저장되거나 보존되지 않습니다.
-   `Recursion`-API Management 자신을 참조 하는 스키마와 같이 재귀적으로 정의 된 정의를 지원 하지 않습니다.
-   이 경우에는 원본 파일 URL(사용 가능한 경우)이 상대 서버 URL에 적용됩니다.
-   보안 정의는 무시 됩니다.
-   API 작업에 대한 인라인 스키마 정의는 지원 되지 않습니다. 스키마 정의는 API 범위에서 정의 되며 API 작업 요청 또는 응답 범위에서 참조할 수 있습니다.
-   정의 된 URL 매개 변수는 URL 템플릿의 일부 여야 합니다.
-   API에서 반환 하는 MIME 형식을 설명 하는 `Produces` 키워드는 지원 되지 않습니다. 

### <a name="open-api-v2"> </a>Openapi 버전 2

-   JSON 형식만 지원됩니다.

### <a name="open-api-v3"> </a>Openapi 버전 3

-   많은 `servers` 지정 하는 경우 API Management 첫 번째 HTTPs URL을 선택 하려고 시도 합니다. HTTPs URL이 없으면 첫 번째 HTTP URL이 선택됩니다. HTTP URL도 없으면 서버 URL은 비어 있게 됩니다.
-   `Examples` 지원 되지 않지만 `example`는입니다.

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI 가져오기, 업데이트 및 내보내기 메커니즘

### <a name="add-new-api-via-openapi-import"></a>OpenAPI 가져오기를 통해 새 API 추가

OpenAPI 문서에 있는 각 작업에 대해 Azure 리소스 이름과 표시 이름이 각각 `operationId` 및 `summary`로 설정 된 새 작업이 생성 됩니다. `operationId` 값은 아래에 설명 된 규칙에 따라 정규화 됩니다. `summary` 값은 있는 그대로 가져오며 길이는 300 자로 제한 됩니다.

`operationId` 지정 되지 않은 경우 (즉, 존재 하지 않거나, `null`이거나, 비어 있는 경우) Azure 리소스 이름 값은 HTTP 메서드와 경로 템플릿 (예: `get-foo`)을 결합 하 여 생성 됩니다.

`summary` 지정 되지 않은 경우 (즉, 존재 하지 않거나 `null`이거나 비어 있는 경우) `display name` 값이 `operationId`로 설정 됩니다. `operationId` 지정 하지 않으면 HTTP 메서드와 경로 템플릿 (예: `Get - /foo`)을 결합 하 여 표시 이름 값이 생성 됩니다.

### <a name="update-an-existing-api-via-openapi-import"></a>OpenAPI 가져오기를 통해 기존 API 업데이트

가져오는 동안 기존 API는 OpenAPI 문서에 설명 된 API와 일치 하도록 변경 됩니다. OpenAPI 문서의 각 작업은 기존 작업의 Azure 리소스 이름과 `operationId` 값을 비교 하 여 기존 작업과 일치 합니다.

일치 하는 항목이 있으면 기존 작업의 속성이 "내부"로 업데이트 됩니다.

일치 항목을 찾을 수 없는 경우에는 위의 섹션에 설명 된 규칙을 사용 하 여 새 작업을 만듭니다. 새 작업 마다 가져오기 작업은 동일한 HTTP 메서드 및 경로 템플릿을 사용 하 여 기존 작업에서 정책을 복사 하려고 합니다.

기존의 일치 하지 않는 작업은 모두 삭제 됩니다.

가져오기를 보다 예측 가능 하 게 만들려면 다음 지침을 따르세요.

- 모든 작업에 대해 `operationId` 속성을 지정 해야 합니다.
- 초기 가져오기 후 `operationId`를 변경 하지 않습니다.
- `operationId` 및 HTTP 메서드 또는 경로 템플릿을 동시에 변경 하지 마십시오.

### <a name="export-api-as-openapi"></a>OpenAPI로 API 내보내기

각 작업에 대해 해당 Azure 리소스 이름을 `operationId`으로 내보내고 표시 이름을 `summary`으로 내보냅니다.
OperationId의 정규화 규칙

- 소문자로 변환합니다.
- 영숫자가 아닌 문자의 각 시퀀스를 단일 대시로 바꿉니다. 예를 들어 `GET-/foo/{bar}?buzz={quix}`은 `get-foo-bar-buzz-quix-`으로 변환 됩니다.
- 두 면에서 대시를 트리밍합니다. 예를 들어 `get-foo-bar-buzz-quix-` `get-foo-bar-buzz-quix` 됩니다.
- 크기를 조정 하면 76 문자, 4 자는 리소스 이름에 대한 최대한도 미만으로 잘립니다.
- 필요한 경우 중복 제거 접미사에 대해 나머지 네 문자를 `-1, -2, ..., -999`형식으로 사용 합니다.


## <a name="wsdl"> </a>WSDL

WSDL 파일은 SOAP 통과 및 SOAP-REST Api를 만드는 데 사용 됩니다.

-   **SOAP 바인딩** - 스타일 "document" 및 "literal" 인코딩의 SOAP 바인딩만 사용할 수 있습니다. "rpc" 스타일 또는 SOAP 인코딩은 지원되지 않습니다.
-   **WSDL:Import** - 이 특성은 지원되지 않습니다. 고객은 가져오기를 문서 하나로 병합해야 합니다.
-   **여러 부분으로 된 메시지** - 이러한 유형의 메시지는 지원되지 않습니다.
-   **WCF wsHttpBinding** - Windows Communication Foundation을 사용하여 만든 SOAP 서비스는 basicHttpBinding을 사용해야 합니다. wsHttpBinding은 지원되지 않습니다.
-   **MTOM** - MTOM을 사용한 서비스는 <em>작동할 수 있습니다</em>. 현재는 공식적으로 지원되지 않습니다.
-   **재귀** - 재귀적으로 정의된(예: 자체의 배열을 참조) 형식은 APIM에서 지원되지 않습니다.
-   **여러 네임스페이스** - 하나의 스키마에서 여러 네임스페이스를 사용할 수 있지만 메시지 파트를 정의하는 데는 대상 네임스페이스만 사용할 수 있습니다. 다른 입력 또는 출력 요소를 정의 하는 데 사용 되는 대상이 아닌 다른 네임 스페이스는 유지 되지 않습니다. 이러한 WSDL 문서를 가져올 수 있지만 내보내기 시 모든 메시지 파트의 대상 네임스페이스는 WSDL입니다.
-   **배열** -SOAP-REST 변환은 아래 예제에 표시 된 래핑된 배열만 지원 합니다.

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"> </a>WADL

현재, 알려진 WADL 가져오기 문제가 없습니다.
