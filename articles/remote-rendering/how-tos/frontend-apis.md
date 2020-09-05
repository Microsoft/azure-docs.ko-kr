---
title: 인증용 Azure Frontend API
description: 인증용 C# 프런트 엔드 API를 사용하는 방법을 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 0488f467a036957bf2341aab63919a105f383bdf
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003527"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>인증을 위해 Azure Frontend API 사용

이 섹션에서는 인증 및 세션 관리를 위해 API를 사용 하는 방법을 설명 합니다.

> [!CAUTION]
이 장에서 설명 하는 함수는 내부적으로 서버에서 REST 호출을 실행 합니다. 모든 REST 호출의 경우와 마찬가지로, 이러한 명령을 너무 자주 보내면 서버가 제한을 초과 하 여 결국 오류가 반환 됩니다. `SessionGeneralContext.HttpResponseCode`이 경우 멤버의 값은 429 ("요청이 너무 많음")입니다. 이에 대 한 규칙에 따라 **이후 호출 사이에 5-10 초의**지연이 발생 합니다.


## <a name="azurefrontendaccountinfo"></a>AzureFrontendAccountInfo

AzureFrontendAccountInfo는 SDK의 ```AzureFrontend``` 인스턴스에 대한 인증 정보를 설정하는 데 사용됩니다.

중요한 필드는 다음과 같습니다.

```cs

public class AzureFrontendAccountInfo
{
    // Something akin to "<region>.mixedreality.azure.com"
    public string AccountDomain;

    // Can use one of:
    // 1) ID and Key.
    // 2) ID and AuthenticationToken.
    // 3) ID and AccessToken.
    public string AccountId = Guid.Empty.ToString();
    public string AccountKey = string.Empty;
    public string AuthenticationToken = string.Empty;
    public string AccessToken = string.Empty;
}
```

해당하는 C++는 다음과 같습니다.

```cpp
struct AzureFrontendAccountInfo
{
    std::string AccountDomain{};
    std::string AccountId{};
    std::string AccountKey{};
    std::string AuthenticationToken{};
    std::string AccessToken{};
};
```

도메인의 _지역_ 부분에 대해 [가까운 지역](../reference/regions.md)을 사용합니다.

계정 정보는 [계정 정보 검색](create-an-account.md#retrieve-the-account-information) 단락에 설명된 대로 포털에서 가져올 수 있습니다.

## <a name="azure-frontend"></a>Azure 프런트 엔드

관련 클래스는 ```AzureFrontend``` 및 ```AzureSession```입니다. ```AzureFrontend```는 계정 관리 및 계정 수준 기능에 사용됩니다. 여기에는 자산 변환 및 렌더링 세션 생성이 포함됩니다. ```AzureSession```은 세션 수준 기능에 사용되며 세션 업데이트, 쿼리, 갱신 및 서비스 해제가 포함됩니다.

열려 있거나 생성된 각 ```AzureSession```은 생성된 프런트 엔드에 대한 참조를 유지합니다. 완전히 종료하려면 프런트 엔드를 할당 취소하기 전에 모든 세션을 할당 취소해야 합니다.

세션의 할당을 취소 하면 Azure에서 서버를 중지 하지 않으므로를 `AzureSession.StopAsync` 명시적으로 호출 해야 합니다.

세션이 생성되고 해당 상태가 준비된 것으로 표시되면 `AzureSession.ConnectToRuntime`을 사용하여 원격 렌더링 런타임에 연결할 수 있습니다.

### <a name="threading"></a>스레딩

모든 AzureSession 및 AzureFrontend 비동기 호출은 주 애플리케이션 스레드가 아닌 백그라운드 스레드에서 완료됩니다.

### <a name="conversion-apis"></a>변환 API

변환 서비스에 대한 자세한 내용은 [모델 변환 REST API](conversion/conversion-rest-api.md)를 참조하세요.

#### <a name="start-asset-conversion"></a>자산 변환 시작

```cs
private StartConversionAsync _pendingAsync = null;

void StartAssetConversion(AzureFrontend frontend, string storageContainer, string blobinputpath, string bloboutpath, string modelName, string outputName)
{
    _pendingAsync = frontend.StartConversionAsync(
        new AssetConversionInputParams(storageContainer, blobinputpath, "", modelName),
        new AssetConversionOutputParams(storageContainer, bloboutpath, "", outputName)
        );
    _pendingAsync.Completed +=
        (StartConversionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to start asset conversion!");
            }
        };

        _pendingAsync = null;
}
```

```cpp
void StartAssetConversion(ApiHandle<AzureFrontend> frontend, std::string storageContainer, std::string blobinputpath, std::string bloboutpath, std::string modelName, std::string outputName)
{
    AssetConversionInputParams input;
    input.BlobContainerInformation.BlobContainerName = blobinputpath;
    input.BlobContainerInformation.StorageAccountName = storageContainer;
    input.BlobContainerInformation.FolderPath = "";
    input.InputAssetPath = modelName;

    AssetConversionOutputParams output;
    output.BlobContainerInformation.BlobContainerName = blobinputpath;
    output.BlobContainerInformation.StorageAccountName = storageContainer;
    output.BlobContainerInformation.FolderPath = "";
    output.OutputAssetPath = outputName;

    ApiHandle<StartAssetConversionAsync> conversionAsync = *frontend->StartAssetConversionAsync(input, output);
    conversionAsync->Completed([](ApiHandle<StartAssetConversionAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res.Result
        }
        else
        {
            printf("Failed to start asset conversion!");
        }
    }
    );
}
```


#### <a name="get-conversion-status"></a>변환 상태 가져오기

```cs
private ConversionStatusAsync _pendingAsync = null
void GetConversionStatus(AzureFrontend frontend, string assetId)
{
    _pendingAsync = frontend.GetAssetConversionStatusAsync(assetId);
    _pendingAsync.Completed +=
        (ConversionStatusAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get status of asset conversion!");
            }

            _pendingAsync = null;
        };
}
```

```cpp
void GetConversionStatus(ApiHandle<AzureFrontend> frontend, std::string assetId)
{
    ApiHandle<ConversionStatusAsync> pendingAsync = *frontend->GetAssetConversionStatusAsync(assetId);
    pendingAsync->Completed([](ApiHandle<ConversionStatusAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            // use res->Result
        }
        else
        {
            printf("Failed to get status of asset conversion!");
        }

    });
}
```


### <a name="rendering-apis"></a>렌더링 API

세션 관리에 대한 자세한 내용은 [세션 관리 REST API](session-rest-api.md)를 참조하세요.

서비스에서 렌더링 세션을 동적으로 만들거나 기존 세션 ID를 AzureSession 개체로 '열 수' 있습니다.

#### <a name="create-rendering-session"></a>렌더링 세션 만들기

```cs
private CreateSessionAsync _pendingAsync = null;
void CreateRenderingSession(AzureFrontend frontend, RenderingSessionVmSize vmSize, ARRTimeSpan maxLease)
{
    _pendingAsync = frontend.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationParams(vmSize, maxLease));

    _pendingAsync.Completed +=
        (CreateSessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to create session!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend, RenderingSessionVmSize vmSize, const ARRTimeSpan& maxLease)
{
    RenderingSessionCreationParams params;
    params.MaxLease = maxLease;
    params.Size = vmSize;
    ApiHandle<CreateSessionAsync> pendingAsync = *frontend->CreateNewRenderingSessionAsync(params);

    pendingAsync->Completed([] (ApiHandle<CreateSessionAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res->Result
        }
        else
        {
            printf("Failed to create session!");
        }
    });
}
```

#### <a name="open-an-existing-rendering-session"></a>기존 렌더링 세션 열기

기존 세션을 여는 것은 동기식 호출입니다.

```cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend, std::string sessionId)
{
    ApiHandle<AzureSession> session = *frontend->OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```


#### <a name="get-current-rendering-sessions"></a>현재 렌더링 세션 가져오기

```cs
private SessionPropertiesArrayAsync _pendingAsync = null;
void GetCurrentRenderingSessions(AzureFrontend frontend)
{
    _pendingAsync = frontend.GetCurrentRenderingSessionsAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesArrayAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get current rendering sessions!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void GetCurrentRenderingSessions(ApiHandle<AzureFrontend> frontend)
{
    ApiHandle<SessionPropertiesArrayAsync> pendingAsync = *frontend->GetCurrentRenderingSessionsAsync();
    pendingAsync->Completed([](ApiHandle<SessionPropertiesArrayAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            // use res.Result
        }
        else
        {
            printf("Failed to get current rendering sessions!");
        }
    });
}
```

### <a name="session-apis"></a>세션 API

#### <a name="get-rendering-session-properties"></a>렌더링 세션 속성 가져오기

```cs
private SessionPropertiesAsync _pendingAsync = null;
void GetRenderingSessionProperties(AzureSession session)
{
    _pendingAsync = session.GetPropertiesAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get properties of session!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void GetRenderingSessionProperties(ApiHandle<AzureSession> session)
{
    ApiHandle<SessionPropertiesAsync> pendingAsync = *session->GetPropertiesAsync();
    pendingAsync->Completed([](ApiHandle<SessionPropertiesAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res.Result
        }
        else
        {
            printf("Failed to get properties of session!");
        }
    });
}
```

#### <a name="update-rendering-session"></a>렌더링 세션 업데이트

```cs
private SessionAsync _pendingAsync;
void UpdateRenderingSession(AzureSession session, ARRTimeSpan updatedLease)
{
    _pendingAsync = session.RenewAsync(
        new RenderingSessionUpdateParams(updatedLease));
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session renewed succeeded!");
            }
            else
            {
                Console.WriteLine("Failed to renew rendering session!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void UpdateRenderingSession(ApiHandle<AzureSession> session, const ARRTimeSpan& updatedLease)
{
    RenderingSessionUpdateParams params;
    params.MaxLease = updatedLease;
    ApiHandle<SessionAsync> pendingAsync = *session->RenewAsync(params);
    pendingAsync->Completed([](ApiHandle<SessionAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            printf("Rendering session renewed succeeded!");
        }
        else
        {
            printf("Failed to renew rendering session!");
        }
    });
}
```

#### <a name="stop-rendering-session"></a>렌더링 세션 중지

```cs
private SessionAsync _pendingAsync;
void StopRenderingSession(AzureSession session)
{
    _pendingAsync = session.StopAsync();
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session stopped successfully!");
            }
            else
            {
                Console.WriteLine("Failed to stop rendering session!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void StopRenderingSession(ApiHandle<AzureSession> session)
{
    ApiHandle<SessionAsync> pendingAsync = *session->StopAsync();
    pendingAsync->Completed([](ApiHandle<SessionAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            printf("Rendering session stopped successfully!");
        }
        else
        {
            printf("Failed to stop rendering session!");
        }
    });
}
```

#### <a name="connect-to-arr-inspector"></a>ARR 검사자에 연결

```cs
private ArrInspectorAsync _pendingAsync = null;
void ConnectToArrInspector(AzureSession session, string hostname)
{
    _pendingAsync = session.ConnectToArrInspectorAsync(hostname);
    _pendingAsync.Completed +=
        (ArrInspectorAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                // Launch the html file with default browser
                string htmlPath = res.Result;
#if WINDOWS_UWP
                UnityEngine.WSA.Application.InvokeOnUIThread(async () =>
                {
                    var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(htmlPath);
                    await Windows.System.Launcher.LaunchFileAsync(file);
                }, true);
#else
                InvokeOnAppThreadAsync(() =>
                {
                    System.Diagnostics.Process.Start("file:///" + htmlPath);
                });
#endif
            }
            else
            {
                Console.WriteLine("Failed to connect to ARR inspector!");
            }
        };
}
```

```cpp
void ConnectToArrInspector(ApiHandle<AzureSession> session, std::string hostname)
{
    ApiHandle<ArrInspectorAsync> pendingAsync = *session->ConnectToArrInspectorAsync(hostname);
    pendingAsync->Completed([](ApiHandle<ArrInspectorAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            // Launch the html file with default browser
            std::string htmlPath = "file:///" + *res->Result();
            ShellExecuteA(NULL, "open", htmlPath.c_str(), NULL, NULL, SW_SHOWDEFAULT);
        }
        else
        {
            printf("Failed to connect to ARR inspector!");
        }
    });
}
```

## <a name="next-steps"></a>다음 단계

* [계정 만들기](create-an-account.md)
* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
