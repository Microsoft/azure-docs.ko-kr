---
title: 응용 프로그램에서 관리 id 사용
description: Azure Service Fabric 응용 프로그램 코드에서 관리 되는 id를 사용 하 여 Azure 서비스에 액세스 하는 방법입니다. 이 기능은 공개 미리 보기 상태입니다.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: 59680ec7911f55c3dc49d8834b410a039aa435dc
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610321"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services-preview"></a>Service Fabric 응용 프로그램의 관리 되는 id를 활용 하 여 Azure 서비스에 액세스 하는 방법 (미리 보기)

Service Fabric 응용 프로그램은 관리 되는 id를 활용 하 여 Azure Active Directory 기반 인증을 지 원하는 다른 Azure 리소스에 액세스할 수 있습니다. 응용 프로그램은 시스템 할당 또는 사용자 할당 일 수 있는 id를 나타내는 [액세스 토큰](../active-directory/develop/developer-glossary.md#access-token) 을 가져올 수 있으며,이 토큰을 ' 전달자 ' 토큰으로 사용 하 여 다른 서비스 ( [보호 된 리소스 서버](../active-directory/develop/developer-glossary.md#resource-server)라고도 함)에 인증할 수 있습니다. 토큰은 Service Fabric 응용 프로그램에 할당 된 id를 나타내며, 해당 id를 공유 하는 Azure 리소스 (SF 응용 프로그램 포함)에만 발급 됩니다. 관리 되는 id에 대 한 자세한 설명 및 시스템 할당 id와 사용자 할당 id를 구분 하는 방법은 [관리 id 개요](../active-directory/managed-identities-azure-resources/overview.md) 설명서를 참조 하세요. 이 문서 전체에서 관리 되는 id 사용 Service Fabric 응용 프로그램을 [클라이언트 응용 프로그램](../active-directory/develop/developer-glossary.md#client-application) 으로 참조 합니다.

> [!IMPORTANT]
> 관리 id는 리소스를 포함 하는 구독과 연결 된 해당 Azure AD 테넌트의 Azure 리소스와 서비스 주체 간의 연결을 나타냅니다. 따라서 Service Fabric 컨텍스트에서 관리 id는 Azure 리소스로 배포 된 응용 프로그램에 대해서만 지원 됩니다. 

> [!IMPORTANT]
> Service Fabric 응용 프로그램의 관리 되는 id를 사용 하기 전에 클라이언트 응용 프로그램에 보호 된 리소스에 대 한 액세스 권한을 부여 해야 합니다. [AZURE AD 인증을 지 원하는 azure 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) 목록을 참조 하 여 지원 여부를 확인 한 다음 특정 단계에 대 한 특정 단계에 대 한 자세한 내용을 확인 하 여 관심 있는 리소스에 대 한 id 액세스 권한을 부여 하세요. 

## <a name="acquiring-an-access-token-using-rest-api"></a>REST API를 사용 하 여 액세스 토큰 가져오기
관리 id에 대해 사용 하도록 설정 된 클러스터에서 Service Fabric 런타임은 응용 프로그램이 액세스 토큰을 가져오는 데 사용할 수 있는 localhost 엔드포인트을 노출 합니다. 엔드포인트은 클러스터의 모든 노드에서 사용할 수 있으며 해당 노드의 모든 엔터티에 액세스할 수 있습니다. 권한 있는 호출자는이 엔드포인트을 호출 하 고 인증 코드를 제공 하 여 액세스 토큰을 가져올 수 있습니다. 코드는 각각의 개별 서비스 코드 패키지 활성화에 대해 Service Fabric 런타임에 의해 생성 되며 해당 서비스 코드 패키지를 호스팅하는 프로세스의 수명에 바인딩됩니다.

특히 관리 되는 id 사용 Service Fabric 서비스 환경은 다음 변수로 시드 됩니다.
- ' MSI_ENDPOINT ': localhost 엔드포인트, 전체 경로, API 버전 및 해당 서비스의 관리 되는 id에 해당 하는 매개 변수
- ' MSI_SECRET ': 불투명 문자열 이며 현재 노드의 서비스를 고유 하 게 나타내는 인증 코드입니다.

> [!NOTE]
> ' MSI_ENDPOINT ' 및 ' MSI_SECRET ' 이름은 이전에 관리 되는 id ("관리 서비스 ID")를 지정 하며,이는 이제 사용 되지 않습니다. 이 이름은 관리 되는 id를 지 원하는 다른 Azure 서비스에서 사용 하는 동일한 환경 변수 이름과도 일치 합니다.

> [!IMPORTANT]
> 응용 프로그램 코드는 ' MSI_SECRET ' 환경 변수의 값을 중요 한 데이터로 간주 해야 합니다 .이는 로깅되지 않으며 그렇지 않은 경우에는 disseminated 합니다. 인증 코드에는 로컬 노드 외부의 값이 없지만 서비스를 호스팅하는 프로세스는 종료 된 후에는 Service Fabric 서비스의 id를 나타내므로 액세스 토큰 자체와 동일한 예방 조치를 사용 하 여 처리 해야 합니다.

클라이언트는 토큰을 가져오기 위해 다음 단계를 수행 합니다.
- 관리 되는 id 엔드포인트 (MSI_ENDPOINT 값)을 API 버전 및 토큰에 필요한 리소스 (대상 사용자)와 연결 하 여 URI를 형성 합니다.
- 지정 된 URI에 대 한 GET http 요청을 만듭니다.
- 요청에 인증 코드 (MSI_SECRET 값)를 헤더로 추가 합니다.
- 요청을 제출 합니다.

성공적인 응답에는 결과 액세스 토큰을 나타내는 JSON 페이로드 뿐만 아니라이를 설명 하는 메타 데이터가 포함 됩니다. 실패 한 응답에도 오류에 대 한 설명이 포함 됩니다. 오류 처리에 대 한 자세한 내용은 아래를 참조 하세요.

액세스 토큰은 다양 한 수준 (노드, 클러스터, 리소스 공급자 서비스)에서 Service Fabric에 의해 캐시 되므로 성공적인 응답은 토큰이 사용자 응용 프로그램의 요청에 대 한 응답으로 직접 발급 되었음을 암시 하는 것은 아닙니다. 토큰은 수명 미만으로 캐시 되므로 응용 프로그램은 유효한 토큰을 수신 하도록 보장 됩니다. 응용 프로그램 코드는 자신이 획득 한 액세스 토큰을 캐시 하는 것이 좋습니다. 캐싱 키에는 대상 그룹의 파생이 포함 되어야 합니다. 


샘플 요청:
```http
GET 'http://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://keyvault.azure.com/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
각 항목이 나타내는 의미는 다음과 같습니다.

| 요소 | Description |
| ------- | ----------- |
| `GET` | HTTP 동사는 엔드포인트에서 데이터를 검색한다는 것을 나타냅니다. 이 경우에는 OAuth 액세스 토큰입니다. | 
| `http://localhost:2377/metadata/identity/oauth2/token` | MSI_ENDPOINT 환경 변수를 통해 제공 되는 Service Fabric 응용 프로그램에 대 한 관리 id 엔드포인트입니다. |
| `api-version` | 관리 되는 Id 토큰 서비스의 API 버전을 지정 하는 쿼리 문자열 매개 변수 현재 유일 하 게 허용 되는 값은 `2019-07-01-preview`이며 변경 될 수 있습니다. |
| `resource` | 쿼리 문자열 매개 변수는 대상 리소스의 앱 ID URI를 나타냅니다. 발급 된 토큰의 `aud` (대상) 클레임으로 반영 됩니다. 이 예제에서는 앱 ID URI가 https:\//keyvault.azure.com/인 Azure Key Vault에 액세스 하기 위한 토큰을 요청 합니다. |
| `Secret` | Service Fabric 서비스가 호출자를 인증 하기 위해 관리 되는 Service Fabric Id 토큰 서비스에서 요구 하는 HTTP 요청 헤더 필드입니다. 이 값은 MSI_SECRET 환경 변수를 통해 SF 런타임에서 제공 됩니다. |


샘플 응답:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://keyvault.azure.com/"
}
```
각 항목이 나타내는 의미는 다음과 같습니다.

| 요소 | Description |
| ------- | ----------- |
| `token_type` | 토큰 형식입니다. 이 경우이 토큰의 프레 젠 터 (' 전달자 ')가 토큰의 의도 한 주체 임을 의미 하는 "전달자" 액세스 토큰입니다. |
| `access_token` | 요청된 액세스 토큰입니다. 보안이 설정된 REST API를 호출할 때 토큰은 호출자를 인증하는 API를 허용하는 "전달자" 토큰으로 `Authorization` 요청 헤더 필드에 포함됩니다. | 
| `expires_on` | 액세스 토큰이 만료 되는 타임 스탬프입니다. "1970-01-01T0:0: 0Z UTC"의 초 수로 표시 되며 토큰의 `exp` 클레임에 해당 합니다. 이 경우, 토큰은 2019-08-08T06:10:11 + 00:00 (RFC 3339)에 만료 됩니다.|
| `resource` | 요청에 대 한 `resource` 쿼리 문자열 매개 변수를 통해 지정 된 액세스 토큰을 발급 한 리소스입니다. 토큰의 ' aud ' 클레임에 해당 합니다. |


## <a name="acquiring-an-access-token-using-c"></a>을 사용 하 여 액세스 토큰 가져오기C#
위의은에서 C#가 됩니다.

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("MSI_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("MSI_SECRET");
            var managedIdentityAuthenticationHeader = "secret";
            var managedIdentityApiVersion = "2019-07-01-preview";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);

            try
            {
                var response = await new HttpClient().SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>관리 Id를 사용 하 여 Service Fabric 응용 프로그램에서 Key Vault 액세스
이 샘플은 위의를 기반으로 하 여 관리 id를 사용 하 여 Key Vault에 저장 된 비밀에 액세스 하는 방법을 보여 줍니다.

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>오류 처리
HTTP 응답 헤더의 ' 상태 코드 ' 필드는 요청의 성공 상태를 나타냅니다. ' 200 OK ' 상태는 성공을 나타내고 응답은 위에서 설명한 대로 액세스 토큰을 포함 합니다. 다음은 가능한 오류 응답을 간단히 열거 한 것입니다.

| 상태 코드 | 오류 원인 | 처리 방법 |
| ----------- | ------------ | ------------- |
| 404 찾을 수 없습니다. | 알 수 없는 인증 코드 이거나 응용 프로그램에 관리 되는 id가 할당 되지 않았습니다. | 응용 프로그램 설정 또는 토큰 취득 코드를 수정 합니다. |
| 429 요청이 너무 많음. |  제한에 도달 하 여 AAD 또는 SF에 의해 적용 됩니다. | 지수 백오프를 통해 다시 시도. 아래 지침을 참조하세요. |
| 요청의 4xx 오류입니다. | 요청 매개 변수 중 하나 이상이 올바르지 않습니다. | 다시 시도하지 마세요.  자세한 내용은 오류 세부 정보를 확인하세요.  4xx 오류는 디자인 타임 오류입니다.|
| 서비스에서 5xx 오류가 발생 했습니다. | 관리 id 하위 시스템 또는 Azure Active Directory에서 일시적인 오류를 반환 했습니다. | 잠시 후에 다시 시도 하는 것이 안전 합니다. 다시 시도 하면 조정 상태 (429)가 발생할 수 있습니다.|

오류가 발생 하는 경우 해당 HTTP 응답 본문에는 오류 세부 정보를 포함 하는 JSON 개체가 포함 됩니다.

| 요소 | Description |
| ------- | ----------- |
| 코드 | 오류 코드 |
| correlationId | 디버깅에 사용할 수 있는 상관 관계 ID입니다. |
| message | 오류의 자세한 설명입니다. **오류 설명은 언제 든 지 변경 될 수 있습니다. 오류 메시지 자체에 의존 하지 않습니다.**|

샘플 오류:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

다음은 관리 되는 id와 관련 된 일반적인 Service Fabric 오류 목록입니다.

| 코드 | 메시지 | Description | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | 요청 헤더에서 비밀을 찾을 수 없습니다. | 인증 코드를 요청과 함께 제공 하지 않았습니다. | 
| ManagedIdentityNotFound | 지정 된 응용 프로그램 호스트에 대 한 관리 id를 찾을 수 없습니다. | 응용 프로그램에 id가 없거나 인증 코드를 알 수 없습니다. |
| ArgumentNullOrEmpty | ' Resource ' 매개 변수는 null 또는 빈 문자열일 수 없습니다. | 요청에 리소스 (대상 그룹)가 제공 되지 않았습니다. |
| InvalidApiVersion | Api-version ' '은 지원 되지 않습니다. 지원 되는 버전은 ' 2019-07-01-preview '입니다. | 요청 URI에 지정 된 API 버전이 없거나 지원 되지 않습니다. |
| InternalServerError | 오류가 발생했습니다. | Service Fabric 스택의 외부에서 관리 되는 id 하위 시스템에 오류가 발생 했습니다. 리소스에 대해 잘못 된 값이 지정 된 것 같습니다 (후행 '/' 확인). | 

## <a name="retry-guidance"></a>다시 시도 지침 

일반적으로 다시 시도할 수 있는 오류 코드는 429 (너무 많은 요청)입니다. 내부 서버 오류/5xx 오류 코드는 다시 시도할 수 있지만 원인은 영구적 일 수 있습니다. 

제한 제한은 관리 id 하위 시스템에 대 한 호출 수 (특히 ' 업스트림 ' 종속성 (관리 Id Azure 서비스 또는 보안 토큰 서비스)에 적용 됩니다. Service Fabric는 파이프라인의 다양 한 수준에서 토큰을 캐시 하지만 관련 된 구성 요소의 분산 특성을 제공 하는 경우 호출자는 일관성 없는 제한 응답을 경험할 수 있습니다 (즉, 응용 프로그램의 한 노드/인스턴스는 제한 되지만 동일한 id에 대 한 토큰을 요청 하는 동안 다른 노드에서는 제한 됨). 조정 조건이 설정 되 면 조건을 지울 때까지 동일한 응용 프로그램의 후속 요청이 HTTP 상태 코드 429 (너무 많은 요청)로 실패할 수 있습니다.  

다음과 같이 지 수 백오프를 사용 하 여 제한으로 인해 실패 한 요청을 다시 시도 하는 것이 좋습니다. 

| 호출 인덱스 | 429 수신에 대 한 작업 | 
| --- | --- | 
| 1 | 1 초 대기 후 다시 시도 |
| 2 | 2 초 대기 후 다시 시도 |
| 3 | 4 초 후 다시 시도 |
| 4 | 8 초 대기 후 다시 시도 |
| 4 | 8 초 대기 후 다시 시도 |
| 5 | 16 초 대기 후 다시 시도 |

## <a name="resource-ids-for-azure-services"></a>Azure 서비스의 리소스 ID
Azure AD를 지 원하는 리소스 목록 및 해당 리소스 Id에 대해 [AZURE ad 인증을 지 원하는 azure 서비스](../active-directory/managed-identities-azure-resources/services-support-msi.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계
* [시스템 할당 관리 id를 사용 하 여 Azure Service Fabric 응용 프로그램 배포](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [사용자 할당 관리 id를 사용 하 여 Azure Service Fabric 응용 프로그램 배포](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Azure Service Fabric 응용 프로그램에 다른 Azure 리소스에 대 한 액세스 권한 부여](./how-to-grant-access-other-resources.md)