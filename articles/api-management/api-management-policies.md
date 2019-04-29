---
title: Azure API Management 정책 | Microsoft Docs
description: Azure API Management에 사용할 수 있는 정책에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 711f6395959b3718ca0a094a03c07c4381892104
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63762848"
---
# <a name="api-management-policies"></a>API Management 정책
이 섹션에서는 다음 API Management 정책에 대한 참조를 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책](api-management-howto-policies.md)을 참조하세요.  
  
 정책은 게시자가 구성을 통해 API 동작을 변경할 수 있도록 하는 시스템의 강력한 기능입니다. 정책은 API의 요청이나 응답에 따라 순차적으로 실행되는 명령문의 컬렉션입니다. 많이 사용되는 명령문에는 XML에서 JSON으로 형식 변환, 개발자로부터 들어오는 호출의 양을 제한하는 호출 비율 제한 등이 포함됩니다. 다양한 다른 정책도 바로 사용할 수 있습니다.  
  
 정책이 다르게 지정하지 않는 한 정책 식은 어떤 API Management 정책에서든 특성 값 또는 텍스트 값으로 사용될 수 있습니다. [제어 흐름](api-management-advanced-policies.md#choose) 및 [변수 설정](api-management-advanced-policies.md#set-variable) 정책 등의 일부 정책은 정책 식을 기반으로 합니다. 자세한 내용은 [고급 정책](api-management-advanced-policies.md#AdvancedPolicies) 및 [정책 식](api-management-policy-expressions.md)을 참조하세요.  
  
##  <a name="ProxyPolicies"></a> 정책  
  
-   [액세스 제한 정책](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [HTTP 헤더 확인](api-management-access-restriction-policies.md#CheckHTTPHeader) - HTTP 헤더의 존재 및/또는 값을 적용합니다.  
    -   [구독으로 호출 속도 제한](api-management-access-restriction-policies.md#LimitCallRate) - 구독을 기준으로 호출 속도를 제한하여 API 사용량 급증을 방지합니다.  
    -   [키로 호출 속도 제한](api-management-access-restriction-policies.md#LimitCallRateByKey) - 키를 기준으로 호출 속도를 제한하여 API 사용량 급증을 방지합니다.  
    -   [호출자 IP 제한](api-management-access-restriction-policies.md#RestrictCallerIPs) - 특정 IP 주소 및/또는 주소 범위의 호출을 필터링(허용/거부)합니다.  
    -   [구독으로 사용 할당량 설정](api-management-access-restriction-policies.md#SetUsageQuota) - 구독을 기준으로 갱신 가능 또는 수명 호출 볼륨 및/또는 대역폭 할당량을 적용할 수 있습니다.  
    -   [키로 사용 할당량 설정](api-management-access-restriction-policies.md#SetUsageQuotaByKey) - 키를 기준으로 갱신 가능 또는 수명 호출 볼륨 및/또는 대역폭 할당량을 적용할 수 있습니다.  
    -   [JWT 유효성 검사](api-management-access-restriction-policies.md#ValidateJWT) - 지정된 HTTP 헤더 또는 지정된 쿼리 매개 변수에서 추출된 JWT의 존재 및 유효성을 적용합니다.  
-   [고급 정책](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [제어 흐름](api-management-advanced-policies.md#choose) - 부울 식의 평가 결과에 따라 정책 문을 조건부로 적용합니다.  
    -   [요청 전달](api-management-advanced-policies.md#ForwardRequest) - 백 엔드 서비스에 요청을 전달합니다.  
    -   [이벤트 허브에 기록](api-management-advanced-policies.md#log-to-eventhub) - 로거 엔터티가 정의한 메시지 대상에 지정된 형식으로 메시지를 보냅니다.  
    -   [다시 시도](api-management-advanced-policies.md#Retry) - 조건이 충족될 때까지 포함된 정책 문을 실행하도록 다시 시도합니다. 실행은 지정된 시간 간격으로 최대 지정된 재시도 횟수까지 반복됩니다.  
    -   [응답 반환](api-management-advanced-policies.md#ReturnResponse) - 파이프라인 실행을 중단하고 호출자에게 직접 지정된 응답을 반환합니다.  
    -   [단방향 요청 전송](api-management-advanced-policies.md#SendOneWayRequest) - 지정된 URL에 대한 응답을 기다리지 않고 요청을 보냅니다.  
    -   [요청 전송](api-management-advanced-policies.md#SendRequest) - 지정된 URL로 요청을 보냅니다.  
    -   [변수 설정](api-management-advanced-policies.md#set-variable) - 나중에 액세스할 수 있도록 명명된 context 변수의 값을 유지합니다.  
    -   [요청 메서드 설정](api-management-advanced-policies.md#SetRequestMethod) - 요청에 대한 HTTP 메서드를 변경할 수 있습니다.  
    -   [상태 코드 설정](api-management-advanced-policies.md#SetStatus) - 지정된 값으로 HTTP 상태 코드를 변경합니다.  
    -   [추적](api-management-advanced-policies.md#Trace) - [API 검사기](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) 출력에 문자열을 추가합니다.  
    -   [대기](api-management-advanced-policies.md#Wait) - 계속하기 전에 완료할 포함된 [요청 전송](api-management-advanced-policies.md#SendRequest), [캐시에서 값 가져오기](api-management-caching-policies.md#GetFromCacheByKey) 또는 [흐름 제어](api-management-advanced-policies.md#choose) 정책 등을 기다립니다.  
-   [인증 정책](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [기본 사용 인증](api-management-authentication-policies.md#Basic) - 기본 인증을 사용하여 백 엔드 서비스를 인증합니다.  
    -   [클라이언트 인증서 사용 인증](api-management-authentication-policies.md#ClientCertificate) - 클라이언트 인증서를 사용하여 백 엔드 서비스를 인증합니다.  
    -   [관리 되는 id를 사용 하 여 인증](api-management-authentication-policies.md#ManagedIdentity) -사용 하 여 백 엔드 서비스를 사용 하 여 인증을 [관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)합니다.  
-   [캐싱 정책](api-management-caching-policies.md#CachingPolicies)  
    -   [캐시에서 가져오기](api-management-caching-policies.md#GetFromCache) - 캐시 조회를 수행하여 사용 가능한 경우 올바르게 캐시된 응답을 반환합니다.  
    -   [캐시에 저장](api-management-caching-policies.md#StoreToCache) - 지정된 캐시 제어 구성에 따라 응답을 캐시합니다.  
    -   [캐시에서 값 가져오기](api-management-caching-policies.md#GetFromCacheByKey) - 키로 캐시된 항목을 검색합니다.  
    -   [값을 캐시에 저장](api-management-caching-policies.md#StoreToCacheByKey) - 키로 캐시에 항목을 저장합니다.  
    -   [캐시에서 값을 제거](api-management-caching-policies.md#RemoveCacheByKey) - 키로 캐시에서 항목을 제거합니다.  
-   [도메인 간 정책](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [도메인 간 호출 허용](api-management-cross-domain-policies.md#AllowCrossDomainCalls) - API를 Adobe Flash 및 Microsoft Silverlight 브라우저 기반 클라이언트에서 액세스할 수 있도록 합니다.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) - CORS(Cross-Origin Resource Sharing) 지원을 작업 또는 API에 추가하여 브라우저 기반 클라이언트의 도메인 간 호출을 허용합니다.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) - 패딩이 있는 JSON(JSONP) 지원을 작업 또는 API에 추가하여 JavaScript 브라우저 기반 클라이언트의 도메인 간 호출을 허용합니다.  
-   [변환 정책](api-management-transformation-policies.md#TransformationPolicies)  
    -   [XML로 JSON 변환](api-management-transformation-policies.md#ConvertJSONtoXML) - 요청 또는 응답 본문을 JSON에서 XML로 변환합니다.  
    -   [JSON으로 XML 변환](api-management-transformation-policies.md#ConvertXMLtoJSON) - 요청 또는 응답 본문을 XML에서 JSON으로 변환합니다.  
    -   [본문 문자열 찾기 및 바꾸기](api-management-transformation-policies.md#Findandreplacestringinbody) - 요청 또는 응답 하위 문자열을 찾아 다른 하위 문자열로 바꿉니다.  
    -   [콘텐츠의 URL 마스킹](api-management-transformation-policies.md#MaskURLSContent) - 응답 본문의 링크를 다시 써서(마스킹) 게이트웨이를 통해 동일한 링크를 가리키도록 합니다.  
    -   [백 엔드 서비스 설정](api-management-transformation-policies.md#SetBackendService) - 들어오는 요청의 백 엔드 서비스를 변경합니다.  
    -   [본문 설정](api-management-transformation-policies.md#SetBody) -들어오고 나가는 요청의 메시지 본문을 설정합니다.  
    -   [HTTP 헤더 설정](api-management-transformation-policies.md#SetHTTPheader) - 기존 응답 및/또는 요청 헤더에 값을 할당하거나 새로운 응답 및/또는 요청 헤더를 추가합니다.  
    -   [쿼리 문자열 매개 변수 설정](api-management-transformation-policies.md#SetQueryStringParameter) - 요청 쿼리 문자열 매개 변수를 추가하거나 그 값을 바꾸거나 삭제합니다.  
    -   [URL 다시 쓰기](api-management-transformation-policies.md#RewriteURL) - 요청 URL을 공용 양식에서 웹 서비스에 필요한 양식으로 변환합니다.  
    -   [XSLT를 사용하여 XML 변환](api-management-transformation-policies.md#XSLTransform) - 요청 또는 응답 본문의 XML에 XSL 변환을 적용합니다.  



## <a name="next-steps"></a>다음 단계
정책으로 작업하는 방법에 대한 자세한 내용은 다음을 참조하세요.

+ [API Management의 정책](api-management-howto-policies.md)
+ [API 변환](transform-api.md)
+ [정책 참조](api-management-policy-reference.md)(정책 문 및 해당 설정에 대한 전체 목록)
+ [정책 샘플](policy-samples.md)   
