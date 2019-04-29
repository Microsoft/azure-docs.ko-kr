---
title: Azure API Management 가져오기에서 제한 사항 및 알려진 문제 | Microsoft Docs
description: Open API, WSDL 또는 WADL 형식을 사용하여 Azure API Management로 가져오기 시 알려진 문제 및 제한 사항입니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: 251caa840446e75ff13d9b4dcebcbae3a36473c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657512"
---
# <a name="api-import-restrictions-and-known-issues"></a>API 가져오기 제한 사항 및 알려진 문제
## <a name="about-this-list"></a>다음 목록 정보
API를 가져올 때 성공하기 위해 먼저 해결해야 하는 몇 가지 제한 사항이나 문제가 있을 수 있습니다. 이 문서에서는 이러한 내용을 API의 가져오기 형식별로 구성하여 설명합니다.

## <a name="open-api"> </a>OpenAPI/Swagger

OpenAPI 문서를 가져올 때 오류가 표시되면 해당 문서의 유효성을 미리 검사했는지 확인하세요. 유효성 검사를 수행할 때는 Azure Portal에서 디자인 - 프런트 엔드 - OpenAPI 사양 편집기를 선택하여 디자이너를 사용할 수도 있고, <a href="https://editor.swagger.io">Swagger Editor</a> 등의 타사 도구를 사용할 수도 있습니다.

### <a name="open-api-general"> </a>일반

* 경로와 쿼리 간에 필수 매개 변수 이름은 고유해야 합니다. OpenAPI에서는 매개 변수 이름이 경로, 쿼리, 헤더 등의 특정 위치 내에서만 고유하면 됩니다. 하지만 API Management에서는 경로 및 쿼리 매개 변수를 기준으로 작업을 구분할 수 있습니다. OpenAPI에서는 이러한 구분이 지원되지 않습니다. 따라서 전체 URL 템플릿 내에서 매개 변수 이름이 고유해야 합니다.
* **$ref** 포인터는 외부 파일을 참조할 수 없습니다.
* **x-ms-paths** 및 **x-servers**는 지원되는 유일한 확장명입니다.
* 사용자 지정 확장명은 가져오기 시 무시되며 내보내기용으로 저장되거나 보존되지 않습니다.
* **재귀** - 자기 자신을 참조하는 스키마 등 재귀적으로 정의된 정의는 API Management에서 지원되지 않습니다.
* 이 경우에는 원본 파일 URL(사용 가능한 경우)이 상대 서버 URL에 적용됩니다.

### <a name="open-api-v2"> </a>OpenAPI 버전 2

* JSON 형식만 지원됩니다.

### <a name="open-api-v3"> </a>OpenAPI 버전 3

* 여러 **서버**를 지정하면 API Management에서는 첫 번째 HTTPs URL 선택을 시도합니다. HTTPs URL이 없으면 첫 번째 HTTP URL이 선택됩니다. HTTP URL도 없으면 서버 URL은 비어 있게 됩니다.
* **Examples**는 지원되지 않지만 **example**은 지원됩니다.
* **여러 부분/형식으로 된 데이터**는 지원되지 않습니다.

> [!IMPORTANT]
> OpenAPI 가져오기와 관련된 중요한 정보 및 팁은 이 [문서](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/)를 참조하세요.

## <a name="wsdl"> </a>WSDL
SOAP 통과 API를 생성하는 데 WSDL 파일이 사용되며 SOAP-to-REST API의 백 엔드로 사용됩니다.
* **SOAP 바인딩** - 스타일 "document" 및 "literal" 인코딩의 SOAP 바인딩만 사용할 수 있습니다. "rpc" 스타일 또는 SOAP 인코딩은 지원되지 않습니다.
* **WSDL:Import** - 이 특성은 지원되지 않습니다. 고객은 가져오기를 문서 하나로 병합해야 합니다.
* **여러 부분으로 된 메시지** - 이러한 유형의 메시지는 지원되지 않습니다.
* **WCF wsHttpBinding** - Windows Communication Foundation을 사용하여 만든 SOAP 서비스는 basicHttpBinding을 사용해야 합니다. wsHttpBinding은 지원되지 않습니다.
* **MTOM** - MTOM을 사용한 서비스는 <em>작동할 수 있습니다</em>. 현재는 공식적으로 지원되지 않습니다.
* **재귀** - 재귀적으로 정의된(예: 자체의 배열을 참조) 형식은 APIM에서 지원되지 않습니다.
* **여러 네임스페이스** - 하나의 스키마에서 여러 네임스페이스를 사용할 수 있지만 메시지 파트를 정의하는 데는 대상 네임스페이스만 사용할 수 있습니다. 다른 입력 또는 출력 요소를 정의하는 데 사용되는 대상 이외의 네임스페이스는 유지되지 않습니다. 이러한 WSDL 문서를 가져올 수 있지만 내보내기 시 모든 메시지 파트의 대상 네임스페이스는 WSDL입니다.

## <a name="wadl"> </a>WADL
현재, 알려진 WADL 가져오기 문제가 없습니다.
