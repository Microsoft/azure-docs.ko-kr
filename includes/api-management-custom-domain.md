---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: f98f750c14666400f41a249b3d8379b423e6dee2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60693598"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>TLS 핸드셰이크에서 APIM 프록시 서버가 SSL 인증서로 응답하는 방법

### <a name="clients-calling-with-sni-header"></a>SNI 헤더로 호출하는 클라이언트
고객이 프록시에 대해 구성된 하나 또는 여러 개의 사용자 지정 도메인을 갖는 경우 APIM은 사용자 지정 도메인(예: contoso.com)과 기본 도메인(예: apim-service-name.azure-api.net)에서 HTTPS 요청에 응답할 수 있습니다. SNI(서버 이름 표시) 헤더의 정보에 따라 APIM은 적절한 서버 인증서로 응답합니다.

### <a name="clients-calling-without-sni-header"></a>SNI 헤더 없이 호출하는 클라이언트
고객이 [SNI](https://tools.ietf.org/html/rfc6066#section-3) 헤더를 보내지 않는 클라이언트를 사용하는 경우 APIM은 다음 논리에 따라 응답을 만듭니다.

* 서비스에 프록시에 대해 구성된 하나의 사용자 지정 도메인이 있는 경우 기본 인증서는 프록시 사용자 지정 도메인에 발급된 인증서입니다.
* 서비스가 프록시에 대해 여러 사용자 지정 도메인을 구성한 경우(**프리미엄** 계층에서만 지원됨) 고객은 기본 인증서이어야 하는 인증서를 지정할 수 있습니다. 기본 인증서를 설정하려면 [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/createorupdate#hostnameconfiguration) 속성은 true로 설정되어야 합니다("defaultSslBinding":"true"). 고객이 속성을 설정하지 않는 경우 기본 인증서는 *.azure api.net에서 호스팅되는 기본 프록시 도메인에 발급된 인증서입니다.

## <a name="support-for-putpost-request-with-large-payload"></a>대용량 페이로드를 사용하여 PUT/POST 요청에 대한 지원

APIM 프록시 서버는 HTTPS에서 클라이언트 쪽 인증서를 사용하는 경우 대용량 페이로드를 사용하는 요청을 지원합니다(예: 페이로드 > 40KB). 서버의 요청을 중단되지 않도록 하기 위해 고객은 프록시 호스트 이름에서 속성 ["negotiateClientCertificate": "true"](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/CreateOrUpdate#hostnameconfiguration)를 설정할 수 있습니다. 속성이 true로 설정된 경우 모든 HTTP 요청 교환 전에 클라이언트 인증서가 SSL/TLS 연결 시 요청됩니다. 설정은 **프록시 호스트 이름** 수준에서 적용되므로 모든 연결 요청은 클라이언트 인증서를 요청합니다. 고객은 프록시에 대해 최대 20개의 사용자 지정 도메인을 구성하고(**프리미엄** 계층에서만 지원됨) 이 제한을 해결할 수 있습니다.

