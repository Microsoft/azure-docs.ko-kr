---
title: Azure API Management 정책 식 | Microsoft Docs
description: Azure API Management에서 정책 식에 대해 자세히 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: 90b2dfdbec0d6dc81a05b845832fda92fe36d98c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60656594"
---
# <a name="api-management-policy-expressions"></a>API Management 정책 식
이 문서에서는 C# 7 정책 식 구문에 대해 설명합니다. 각 식에서 암시적으로 제공된 [컨텍스트](api-management-policy-expressions.md#ContextVariables) 변수와 .NET Framework 형식의 허용된 [하위 집합](api-management-policy-expressions.md#CLRTypes)에 액세스합니다.

자세한 내용은 다음을 참조하세요.

- 백 엔드 서비스에 컨텍스트 정보를 제공하는 방법을 참조합니다. [쿼리 문자열 매개 변수 설정](api-management-transformation-policies.md#SetQueryStringParameter) 및 [HTTP 헤더 설정](api-management-transformation-policies.md#SetHTTPheader) 정책을 사용하여 이 정보를 제공합니다.
- [JWT 유효성 검사](api-management-access-restriction-policies.md#ValidateJWT) 정책을 사용하여 토큰 클레임에 따라 작업에 대한 액세스 권한을 미리 부여하는 방법을 참조합니다.
- [API 검사기](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) 추적을 사용하여 정책을 평가하는 방법 및 평가 결과를 확인하는 방법을 참조합니다.
- [캐시에서 가져오기](api-management-caching-policies.md#GetFromCache) 및 [캐시에 저장](api-management-caching-policies.md#StoreToCache) 정책이 있는 식을 사용하여 API Management 응답 캐싱을 구성하는 방법을 참조합니다. 백 엔드 서비스의 `Cache-Control` 지시문에 지정된 대로 백 엔드 서비스의 응답 캐싱과 일치하는 기간을 설정합니다.
- 콘텐츠 필터링을 수행하는 방법을 참조합니다. [흐름 제어](api-management-advanced-policies.md#choose) 및 [본문 설정](api-management-transformation-policies.md#SetBody) 정책을 사용하여 백 엔드에서 받은 응답의 데이터 요소를 제거합니다.
- 정책 명령문을 다운로드하려면 [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) GitHub 리포지토리를 참조하세요.


## <a name="Syntax"></a> 구문
단일 문 식은 `@(expression)`으로 묶으며 여기서 `expression`은 올바르게 구성된 C# 식입니다.

다중 문 식은 `@{expression}`으로 묶습니다. 다중 문 식 내에 모든 코드 경로는 `return` 문으로 끝나야 합니다.

## <a name="PolicyExpressionsExamples"></a> 예

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));
  }
  else
  {
    return null;
  }
}
```

## <a name="PolicyExpressionsUsage"></a>사용
정책 참조에서 다르게 지정하지 않는 한, 식은 어떤 API Management [정책](api-management-policies.md)에서든 특성 값 또는 텍스트 값으로 사용될 수 있습니다.

> [!IMPORTANT]
> 정책 식을 사용하는 경우 정책을 정의할 때 정책 식에 대해 제한된 검증만 이루어집니다. 식은 런타임 시 게이트웨이에 의해 실행됩니다. 정책 식에 의해 생성된 모든 예외는 런타임 오류가 발생합니다.

## <a name="CLRTypes"></a> 정책 식에 허용된 .NET Framework 형식
다음 표에서는 .NET Framework 형식과 정책 식에 허용된 멤버를 보여 줍니다.

|Type|지원되는 멤버|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|모두|
|Newtonsoft.Json.JsonConvert|SerializeObject, DeserializeObject|
|Newtonsoft.Json.Linq.Extensions|모두|
|Newtonsoft.Json.Linq.JArray|모두|
|Newtonsoft.Json.Linq.JConstructor|모두|
|Newtonsoft.Json.Linq.JContainer|모두|
|Newtonsoft.Json.Linq.JObject|모두|
|Newtonsoft.Json.Linq.JProperty|모두|
|Newtonsoft.Json.Linq.JRaw|모두|
|Newtonsoft.Json.Linq.JToken|모두|
|Newtonsoft.Json.Linq.JTokenType|모두|
|Newtonsoft.Json.Linq.JValue|모두|
|System.Array|모두|
|System.BitConverter|모두|
|System.Boolean|모두|
|System.Byte|모두|
|System.Char|모두|
|System.Collections.Generic.Dictionary<TKey, TValue>|모두|
|System.Collections.Generic.HashSet<T>|모두|
|System.Collections.Generic.ICollection<T>|모두|
|System.Collections.Generic.IDictionary<TKey, TValue>|모두|
|System.Collections.Generic.IEnumerable<T>|모두|
|System.Collections.Generic.IEnumerator<T>|모두|
|System.Collections.Generic.IList<T>|모두|
|System.Collections.Generic.IReadOnlyCollection<T>|모두|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|모두|
|System.Collections.Generic.ISet<T>|모두|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|모두|
|System.Collections.Generic.List<T>|모두|
|System.Collections.Generic.Queue<T>|모두|
|System.Collections.Generic.Stack<T>|모두|
|System.Convert|모두|
|System.DateTime|(생성자)에 AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, 날짜, Day, DayOfWeek, DayOfYear, DaysInMonth, 시간, IsDaylightSavingTime, IsLeapYear, MaxValue, 밀리초, 분, MinValue, 월, 지금 추가 를 구문 분석, 둘째, Subtract, 틱, 시간, 오늘, ToString, UtcNow, 연도|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|모두|
|System.Decimal|모두|
|System.Double|모두|
|System.Exception|모두|
|System.Guid|모두|
|System.Int16|모두|
|System.Int32|모두|
|System.Int64|모두|
|System.IO.StringReader|모두|
|System.IO.StringWriter|모두|
|System.Linq.Enumerable|모두|
|System.Math|모두|
|System.MidpointRounding|모두|
|System.Net.WebUtility|모두|
|System.Nullable|모두|
|System.Random|모두|
|System.SByte|모두|
|System.Security.Cryptography.AsymmetricAlgorithm|모두|
|System.Security.Cryptography.CipherMode|모두|
|System.Security.Cryptography.HashAlgorithm|모두|
|System.Security.Cryptography.HashAlgorithmName|모두|
|System.Security.Cryptography.HMAC|모두|
|System.Security.Cryptography.HMACMD5|모두|
|System.Security.Cryptography.HMACSHA1|모두|
|System.Security.Cryptography.HMACSHA256|모두|
|System.Security.Cryptography.HMACSHA384|모두|
|System.Security.Cryptography.HMACSHA512|모두|
|System.Security.Cryptography.KeyedHashAlgorithm|모두|
|System.Security.Cryptography.MD5|모두|
|System.Security.Cryptography.Oid|모두|
|System.Security.Cryptography.PaddingMode|모두|
|System.Security.Cryptography.RNGCryptoServiceProvider|모두|
|System.Security.Cryptography.RSA|모두|
|System.Security.Cryptography.RSAEncryptionPadding|모두|
|System.Security.Cryptography.RSASignaturePadding|모두|
|System.Security.Cryptography.SHA1|모두|
|System.Security.Cryptography.SHA1Managed|모두|
|System.Security.Cryptography.SHA256|모두|
|System.Security.Cryptography.SHA256Managed|모두|
|System.Security.Cryptography.SHA384|모두|
|System.Security.Cryptography.SHA384Managed|모두|
|System.Security.Cryptography.SHA512|모두|
|System.Security.Cryptography.SHA512Managed|모두|
|System.Security.Cryptography.SymmetricAlgorithm|모두|
|System.Security.Cryptography.X509Certificates.PublicKey|모두|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|모두|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|이름|
|System.Security.Cryptography.X509Certificates.X509Certificate|모두|
|System.Security.Cryptography.X509Certificates.X509Certificate2|모두|
|System.Security.Cryptography.X509Certificates.X509ContentType|모두|
|System.Security.Cryptography.X509Certificates.X509NameType|모두|
|System.Single|모두|
|System.String|모두|
|System.StringComparer|모두|
|System.StringComparison|모두|
|System.StringSplitOptions|모두|
|System.Text.Encoding|모두|
|System.Text.RegularExpressions.Capture|Index, Length, Value|
|System.Text.RegularExpressions.CaptureCollection|Count, Item|
|System.Text.RegularExpressions.Group|Captures, Success|
|System.Text.RegularExpressions.GroupCollection|Count, Item|
|System.Text.RegularExpressions.Match|Empty, Groups, Result|
|System.Text.RegularExpressions.Regex|(생성자) IsMatch, 일치, 일치 하는 항목이 대체 이스케이프 해제, 분할|
|System.Text.RegularExpressions.RegexOptions|모두|
|System.Text.StringBuilder|모두|
|System.TimeSpan|모두|
|System.TimeZone|모두|
|System.TimeZoneInfo.AdjustmentRule|모두|
|System.TimeZoneInfo.TransitionTime|모두|
|System.TimeZoneInfo|모두|
|System.Tuple|모두|
|System.UInt16|모두|
|System.UInt32|모두|
|System.UInt64|모두|
|System.Uri|모두|
|System.UriPartial|모두|
|System.Xml.Linq.Extensions|모두|
|System.Xml.Linq.XAttribute|모두|
|System.Xml.Linq.XCData|모두|
|System.Xml.Linq.XComment|모두|
|System.Xml.Linq.XContainer|모두|
|System.Xml.Linq.XDeclaration|모두|
|System.Xml.Linq.XDocument|제외한 모든,: 로드|
|System.Xml.Linq.XDocumentType|모두|
|System.Xml.Linq.XElement|모두|
|System.Xml.Linq.XName|모두|
|System.Xml.Linq.XNamespace|모두|
|System.Xml.Linq.XNode|모두|
|System.Xml.Linq.XNodeDocumentOrderComparer|모두|
|System.Xml.Linq.XNodeEqualityComparer|모두|
|System.Xml.Linq.XObject|모두|
|System.Xml.Linq.XProcessingInstruction|모두|
|System.Xml.Linq.XText|모두|
|System.Xml.XmlNodeType|모두|

## <a name="ContextVariables"></a> 컨텍스트 변수
`context`라는 변수는 모든 정책 [식](api-management-policy-expressions.md#Syntax)에서 암시적으로 사용할 수 있습니다. 해당 멤버는 `\request`와 관련된 정보를 제공합니다. 모든 `context` 멤버는 읽기 전용입니다.

|컨텍스트 변수|허용된 메서드, 속성 및 매개 변수 값|
|----------------------|-------------------------------------------------------|
|context|Api: IApi<br /><br /> 배포<br /><br /> Elapsed: TimeSpan - 타임스탬프 값과 현재 시간 사이의 시간 간격<br /><br /> lastError<br /><br /> 작업(Operation)<br /><br /> Product<br /><br /> 요청<br /><br /> RequestId: Guid - 고유한 요청 식별자<br /><br /> response<br /><br /> 구독<br /><br /> Timestamp: DateTime - 요청이 수신된 시점<br /><br /> Tracing: bool - 추적의 설정 여부를 나타냅니다. <br /><br /> 사용자<br /><br /> Variables: IReadOnlyDictionary<string, object><br /><br /> void Trace(message: string)|
|context.Api|Id: string<br /><br /> IsCurrentRevision: bool<br /><br />  Name: string<br /><br /> Path: string<br /><br /> Revision: string<br /><br /> ServiceUrl: IUrl<br /><br /> Version: string |
|context.Deployment|Region: string<br /><br /> ServiceName: string<br /><br /> Certificates: IReadOnlyDictionary<string, X509Certificate2>|
|context.LastError|Source: string<br /><br /> Reason: string<br /><br /> Message: string<br /><br /> Scope: string<br /><br /> Section: string<br /><br /> Path: string<br /><br /> PolicyId: string<br /><br /> context.LastError에 대한 자세한 내용은 [오류 처리](api-management-error-handling-policies.md)를 참조하세요.|
|context.Operation|Id: string<br /><br /> Method: string<br /><br /> Name: string<br /><br /> UrlTemplate: string|
|context.Product|Apis: IEnumerable<IApi\><br /><br /> ApprovalRequired: bool<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Name: string<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|context.Request|본문: IMessageBody<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: string<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: string<br /><br /> OriginalUrl:IUrl<br /><br /> Url: IUrl|
|string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> 쉼표로 구분된 요청 헤더 값 또는 `defaultValue`(헤더가 없는 경우)를 반환합니다.|
|context.Response|본문: IMessageBody<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|
|string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> 쉼표로 구분된 응답 헤더 값 또는 `defaultValue`(헤더가 없는 경우)를 반환합니다.|
|context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: string<br /><br /> Key: string<br /><br /> Name: string<br /><br /> PrimaryKey: string<br /><br /> SecondaryKey: string<br /><br /> StartDate: DateTime?|
|context.User|Email: string<br /><br /> FirstName: string<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Identities: IEnumerable<IUserIdentity\><br /><br /> LastName: string<br /><br /> Note: string<br /><br /> RegistrationDate: DateTime|
|IApi|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> Protocols: IEnumerable<string\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|
|IGroup|Id: string<br /><br /> Name: string|
|IMessageBody|As<T\>(preserveContent: bool = false): Where T: string, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> 지정된 형식 `T`에서 요청 또는 응답 메시지 본문을 읽는 데 `context.Request.Body.As<T>` 또는 `context.Response.Body.As<T>` 메서드가 사용됩니다. 기본적으로 이 메서드는 원래 메시지 본문 스트림을 사용하고 반환된 후에는 사용할 수 없게 렌더링합니다. 이 메서드가 본문 스트림의 복사본에 대해 작동하도록 하여 이러한 문제를 피하려면 `preserveContent` 매개 변수를 `true`로 설정합니다. 예제를 보려면 [여기](api-management-transformation-policies.md#SetBody)로 이동하세요.|
|IUrl|Host: string<br /><br /> Path: string<br /><br /> Port: int<br /><br /> 쿼리: IReadOnlyDictionary<string, string[]><br /><br /> QueryString: string<br /><br /> Scheme: string|
|IUserIdentity|Id: string<br /><br /> Provider: string|
|ISubscriptionKeyParameterNames|Header: string<br /><br /> Query: string|
|string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: string<br /><br /> defaultValue: string<br /><br /> 쉼표로 구분된 쿼리 매개 변수 값 또는 `defaultValue`(매개 변수가 없는 경우)를 반환합니다.|
|T context.Variables.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> `T` 형식으로 캐스팅되는 변수 값 또는 `defaultValue`(변수가 없는 경우)를 반환합니다.<br /><br /> 이 메서드는 지정된 형식이 반환된 변수의 실제 형식과 일치하지 않는 경우 예외를 발생시킵니다.|
|BasicAuthCredentials AsBasic(input: this string)|input: string<br /><br /> 입력 매개 변수에 유효한 HTTP 기본 인증 권한 부여 요청 헤더 값이 포함된 경우 메서드는 `BasicAuthCredentials` 형식의 개체를 반환하고 그렇지 않은 경우 null을 반환합니다.|
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: string<br /><br /> result: out BasicAuthCredentials<br /><br /> 입력 매개 변수가 요청 헤더에 유효한 HTTP 기본 인증 권한 부여 값을 포함하는 경우 메서드는 `true`를 반환하고 결과 매개 변수는 `BasicAuthCredentials` 형식의 값을 포함합니다. 그렇지 않은 경우 메서드는 `false`를 반환합니다.|
|BasicAuthCredentials|Password: string<br /><br /> UserId: string|
|Jwt AsJwt(input: this string)|input: string<br /><br /> 입력 매개 변수에 유효한 JWT 토큰 값이 포함된 경우 메서드는 `Jwt` 형식의 개체를 반환하고 그렇지 않은 경우 `null`을 반환합니다.|
|bool TryParseJwt(input: this string, result: out Jwt)|input: string<br /><br /> result: out Jwt<br /><br /> 입력 매개 변수에 유효한 JWT 토큰 값이 포함된 경우 메서드는 `true`를 반환하고 결과 매개 변수는 `Jwt` 형식의 값을 포함합니다. 그렇지 않은 경우 메서드는 `false`를 반환합니다.|
|Jwt|Algorithm: string<br /><br /> Audience: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: string<br /><br /> Issuer: string<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Subject: string<br /><br /> Type: string|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: string<br /><br /> defaultValue: string<br /><br /> 쉼표로 구분된 클레임 값 또는 `defaultValue`(헤더가 없는 경우)를 반환합니다.|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - 암호화할 일반 텍스트<br /><br />alg - 대칭 암호화 알고리즘의 이름<br /><br />key - 암호화 키<br /><br />iv - 초기화 벡터<br /><br />암호화된 일반 텍스트를 반환합니다.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - 암호화할 일반 텍스트<br /><br />alg - 암호화 알고리즘<br /><br />암호화된 일반 텍스트를 반환합니다.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - 암호화할 일반 텍스트<br /><br />alg - 암호화 알고리즘<br /><br />key - 암호화 키<br /><br />iv - 초기화 벡터<br /><br />암호화된 일반 텍스트를 반환합니다.|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - 해독할 암호 텍스트<br /><br />alg - 대칭 암호화 알고리즘의 이름<br /><br />key - 암호화 키<br /><br />iv - 초기화 벡터<br /><br />일반 텍스트를 반환합니다.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - 해독할 암호 텍스트<br /><br />alg - 암호화 알고리즘<br /><br />일반 텍스트를 반환합니다.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - input - 해독할 암호 텍스트<br /><br />alg - 암호화 알고리즘<br /><br />key - 암호화 키<br /><br />iv - 초기화 벡터<br /><br />일반 텍스트를 반환합니다.|


## <a name="next-steps"></a>다음 단계

정책으로 작업하는 방법에 대한 자세한 내용은 다음을 참조하세요.

+ [API Management의 정책](api-management-howto-policies.md)
+ [API 변환](transform-api.md)
+ [정책 참조](api-management-policy-reference.md)(정책 문 및 해당 설정에 대한 전체 목록)
+ [정책 샘플](policy-samples.md)
