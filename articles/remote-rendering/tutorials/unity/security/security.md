---
title: Azure Remote Rendering 및 모델 스토리지 보안
description: 콘텐츠 보안을 위한 Remote Rendering 애플리케이션 강화
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 403a5b68e3320700e275c744210f480be2c88e84
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021326"
---
# <a name="tutorial-securing-azure-remote-rendering-and-model-storage"></a>자습서: Azure Remote Rendering 및 모델 스토리지 보안

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * Azure Remote Rendering 모델을 포함하는 Azure Blob Storage 보호
> * Azure Remote Rendering 인스턴스에 액세스하기 위해 Azure AD로 인증
> * Azure Remote Rendering 인증에 Azure 자격 증명 사용

## <a name="prerequisites"></a>필수 구성 요소

* 이 자습서는 [자습서: 재질, 조명 및 효과 구체화](..\materials-lighting-effects\materials-lighting-effects.md)를 기반으로 합니다.

## <a name="why-additional-security-is-needed"></a>추가 보안이 필요한 이유

애플리케이션의 현재 상태와 Azure 리소스에 대한 액세스는 다음과 같습니다.

![초기 보안](./media/security-one.png)

"AccountID + AccountKey" 및 "URL + SAS 토큰"은 모두 기본적으로 사용자 이름과 암호를 함께 저장합니다. 예를 들어 "AccountID + AccountKey"가 노출되면 공격자는 권한 없이 간단하게 ARR 리소스를 사용자의 비용 부담으로 사용할 수 있습니다.

## <a name="securing-your-content-in-azure-blob-storage"></a>Azure Blob Storage의 콘텐츠 보호

Azure Remote Rendering은 올바른 구성을 사용하여 Azure Blob Storage의 콘텐츠에 안전하게 액세스할 수 있습니다. [방법: 스토리지 계정 연결](../../../how-tos/create-an-account.md#link-storage-accounts)을 참조하여 BLOB 스토리지 계정으로 Azure Remote Rendering 인스턴스를 구성하세요.

연결된 BLOB 스토리지를 사용하는 경우 모델을 로드할 때 다음과 같이 약간 다른 방법을 사용하게 됩니다.

```csharp
var loadModelParams = new LoadModelFromSASParams(modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
```

위의 줄에서는 `FromSAS` 버전의 매개 변수 및 세션 작업을 사용합니다. 이러한 버전을 SAS가 아닌 버전으로 변환해야 합니다.

```csharp
var loadModelParams = new LoadModelParams(storageAccountPath, blobContainerName, modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
```

연결된 BLOB 스토리지 계정의 사용자 지정 모델을 로드하도록 **RemoteRenderingCoordinator**를 수정하겠습니다.

1. 아직 [방법: 스토리지 계정 연결](../../../how-tos/create-an-account.md#link-storage-accounts)을 완료하지 않았다면 지금 완료하여 Blob Storage 인스턴스에 액세스하기 위한 ARR 인스턴스 권한을 부여합니다.
1. 현재 **LoadModel** 메서드 바로 아래에서 수정된 다음 **LoadModel** 메서드를 **RemoteRenderingCoordinator**에 추가합니다.

    ```csharp
    /// <summary>
    /// Loads a model from blob storage that has been linked to the ARR instance
    /// </summary>
    /// <param name="storageAccountName">The storage account name, this contains the blob containers </param>
    /// <param name="blobContainerName">The blob container name, i.e. arroutput</param>
    /// <param name="modelPath">The relative path inside the container to the model, i.e. test/MyCustomModel.arrAsset</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns></returns>
    public async Task<Entity> LoadModel(string storageAccountName, string blobContainerName, string modelPath, Transform parent = null, ProgressHandler progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Actions.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelParams($"{storageAccountName}.blob.core.windows.net", blobContainerName, modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
        if (progress != null)
            loadModelAsync.ProgressUpdated += progress;
        var result = await loadModelAsync.AsTask();
        return modelEntity;
    }
    ```

    대부분의 경우 이 코드는 원래 `LoadModel` 메서드와 동일하지만, 여기서는 메서드 호출의 SAS 버전을 비 SAS 버전으로 바꿨습니다.

    추가 입력 `storageAccountName` 및 `blobContainerName`도 인수에 추가되었습니다. 첫 번째 자습서에서 만든 첫 번째 **LoadTestModel** 메서드와 비슷한 다른 메서드에서 이 새 **LoadModel** 메서드를 호출할 것입니다.

1. **LoadTestModel** 바로 뒤에서 다음 메서드를 **RemoteRenderingCoordinator**에 추가합니다.

    ```csharp
    private bool loadingLinkedCustomModel = false;

    [SerializeField]
    private string storageAccountName;
    public string StorageAccountName {
        get => storageAccountName.Trim();
        set => storageAccountName = value;
    }

    [SerializeField]
    private string blobContainerName;
    public string BlobContainerName {
        get => blobContainerName.Trim();
        set => blobContainerName = value;
    }

    [SerializeField]
    private string modelPath;
    public string ModelPath {
        get => modelPath.Trim();
        set => modelPath = value;
    }

    [ContextMenu("Load Linked Custom Model")]
    public async void LoadLinkedCustomModel()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if (loadingLinkedCustomModel)
        {
            Debug.Log("Linked Test model already loading or loaded!");
            return;
        }
        loadingLinkedCustomModel = true;

        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("LinkedCustomModel");
        testParent.transform.position = new Vector3(0f, 0f, 3f);

        await LoadModel(StorageAccountName, BlobContainerName, ModelPath, testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```

    이 코드는 **RemoteRenderingCoordinator** 구성 요소에 세 개의 추가 문자열 변수를 추가합니다.
    ![연결된 모델](./media/storage-account-linked-model.png)

1. **RemoteRenderingCoordinator** 구성 요소에 값을 추가합니다. [모델 변환을 위한 빠른 시작](../../../quickstarts/convert-model.md)을 완료하면 값이 다음과 같습니다.

    * **스토리지 계정 이름**: 스토리지 계정에 대해 선택하는 전역적으로 고유한 스토리지 계정 이름입니다. 이 빠른 시작에서는 *arrtutorialstorage*였으며, 이 값은 달라집니다.
    * **Blob 컨테이너 이름**: arroutput. Blob Storage 컨테이너입니다.
    * **모델 경로**: *arrconfig.json* 파일에 정의된 "outputFolderPath" 및 "outputAssetFileName"의 조합입니다. 이 빠른 시작에서는 "outputFolderPath":"converted/robot", "outputAssetFileName": "robot.arrAsset"였습니다. 모델 경로 값 "converted/robot/robot.arrAsset"이 되며, 이 값은 달라집니다.

    >[!TIP]
    > "-UseContainerSas" 인수 [없이 **Conversion.ps1**](../../../quickstarts/convert-model.md#run-the-conversion) 스크립트를 실행하면 이 스크립트는 SAS 토큰 대신 위의 모든 값을 출력합니다. ![연결된 모델](./media/converted-output.png)
1. 한동안 GameObject **TestModel**을 제거 또는 비활성화하여 사용자 지정 모델을 로드하는 데 필요한 공간을 확보합니다.
1. 장면을 재생하고 원격 세션에 연결합니다.
1. **RemoteRenderingCoordinator**를 마우스 오른쪽 단추로 클릭하고 **연결된 사용자 지정 모델 로드**를 선택합니다.
    ![연결된 모델 로드](./media/load-linked-model.png)

로컬 애플리케이션에서 SAS 토큰을 제거하여 애플리케이션의 보안을 강화하는 단계를 수행했습니다.

이제 애플리케이션의 현재 상태와 Azure 리소스에 대한 액세스는 다음과 같습니다.

![보안 강화](./media/security-two.png)

로컬 애플리케이션에서 "암호"(AccountKey)를 제거해야 합니다. 이 작업은 AAD(Azure Active Directory) 인증을 사용하여 수행할 수 있습니다.

## <a name="azure-active-directory-azure-ad-authentication"></a>Azure AD(Azure Active Directory) 인증

AAD 인증을 사용하면 보다 제어된 방식으로 ARR을 사용하는 개인 또는 그룹을 확인할 수 있습니다. ARR은 계정 키를 사용하는 대신 [액세스 토큰](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)을 허용하는 기능을 기본적으로 제공합니다. 액세스 토큰은 요청된 특정 리소스의 특정 부분만 잠금 해제하는 시간이 제한된 사용자 관련 키로 생각하시면 됩니다.

**RemoteRenderingCoordinator** 스크립트에는 원격 세션 관리를 구성하는 데 사용되는 **AzureFrontendAccountInfo** 개체를 반환하는 메서드가 포함된 **ARRCredentialGetter**라는 대리자가 있습니다. **ARRCredentialGetter**에 다른 메서드를 할당하여 Azure 로그인 흐름을 사용하도록 허용하고, Azure 액세스 토큰을 포함하는 **AzureFrontendAccountInfo** 개체를 생성할 수 있습니다. 이 액세스 토큰은 로그인하는 사용자에게 적용됩니다.

1. [방법: 인증 구성 - 배포된 애플리케이션에 대한 인증](../../../how-tos/authentication.md#authentication-for-deployed-applications)을 따르세요. 특히, Azure Spatial Anchors 설명서 [Azure AD 사용자 인증](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication)에 나열된 지침을 따릅니다. 여기에는 새 Azure Active Directory 애플리케이션을 등록하고 ARR 인스턴스에 대한 액세스를 구성하는 작업이 포함됩니다.
1. 새 AAD 애플리케이션을 구성한 후에는 AAD 애플리케이션이 다음 이미지와 비슷한지 확인합니다.

    **AAD 애플리케이션 -> 인증** ![앱 인증](./media/app-authentication-public.png)

    **AAD 애플리케이션 -> API 권한** ![App API](./media/request-api-permissions-step-five.png)

1. Remote Rendering 계정을 구성한 후에는 구성이 다음 이미지와 비슷한지 확인합니다.

    **AAR -> AccessControl(IAM)** ![ARR 역할](./media/azure-remote-rendering-role-assignment-complete.png)

    >[!NOTE]
    > *소유자* 역할은 클라이언트 애플리케이션을 통해 세션을 관리하기에 충분하지 않습니다. 세션 관리 기능을 부여하려는 모든 사용자에게 **Remote Rendering 클라이언트** 역할을 제공해야 합니다. 세션을 관리하고 모델을 변환하는 기능을 부여하려는 모든 사용자에게 **Remote Rendering 관리자** 역할을 제공해야 합니다.

Azure 쪽의 준비가 완료되었으므로, 이제 코드가 AAR 서비스에 연결하는 방법을 수정해야 합니다. 이를 위해 새 **AzureFrontendAccountInfo** 개체를 반환하는 **BaseARRAuthentication** 인스턴스를 구현합니다. 여기서는 Azure 액세스 토큰을 사용하여 계정 정보를 구성합니다.

1. **AADAuthentication**이라는 새 스크립트를 만들고 해당 코드를 다음으로 바꿉니다.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Identity.Client;
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using UnityEngine;

    public class AADAuthentication : BaseARRAuthentication
    {
        [SerializeField]
        private string accountDomain;
        public string AccountDomain
        {
            get => accountDomain.Trim();
            set => accountDomain = value;
        }

        [SerializeField]
        private string activeDirectoryApplicationClientID;
        public string ActiveDirectoryApplicationClientID
        {
            get => activeDirectoryApplicationClientID.Trim();
            set => activeDirectoryApplicationClientID = value;
        }

        [SerializeField]
        private string azureTenantID;
        public string AzureTenantID
        {
            get => azureTenantID.Trim();
            set => azureTenantID = value;
        }

        [SerializeField]
        private string azureRemoteRenderingAccountID;
        public string AzureRemoteRenderingAccountID
        {
            get => azureRemoteRenderingAccountID.Trim();
            set => azureRemoteRenderingAccountID = value;
        }

        public override event Action<string> AuthenticationInstructions;

        string authority => "https://login.microsoftonline.com/" + AzureTenantID;

        string redirect_uri = "https://login.microsoftonline.com/common/oauth2/nativeclient";

        string[] scopes => new string[] { "https://sts.mixedreality.azure.com/mixedreality.signin" };

        public void OnEnable()
        {
            RemoteRenderingCoordinator.ARRCredentialGetter = GetAARCredentials;
            this.gameObject.AddComponent<ExecuteOnUnityThread>();
        }

        public async override Task<AzureFrontendAccountInfo> GetAARCredentials()
        {
            var result = await TryLogin();
            if (result != null)
            {
                Debug.Log("Account signin successful " + result.Account.Username);

                var AD_Token = result.AccessToken;

                return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
            }
            else
            {
                Debug.LogError("Error logging in");
            }
            return default;
        }

        private Task DeviceCodeReturned(DeviceCodeResult deviceCodeDetails)
        {
            //Since everything in this task can happen on a different thread, invoke responses on the main Unity thread
            ExecuteOnUnityThread.Enqueue(() =>
            {
                // Display instructions to the user for how to authenticate in the browser
                Debug.Log(deviceCodeDetails.Message);
                AuthenticationInstructions?.Invoke(deviceCodeDetails.Message);
            });

            return Task.FromResult(0);
        }

        public override async Task<AuthenticationResult> TryLogin()
        {
            var clientApplication = PublicClientApplicationBuilder.Create(ActiveDirectoryApplicationClientID).WithAuthority(authority).WithRedirectUri(redirect_uri).Build();
            AuthenticationResult result = null;
            try
            {
                var accounts = await clientApplication.GetAccountsAsync();

                if (accounts.Any())
                {
                    result = await clientApplication.AcquireTokenSilent(scopes, accounts.First()).ExecuteAsync();

                    return result;
                }
                else
                {
                    try
                    {
                        result = await clientApplication.AcquireTokenWithDeviceCode(scopes, DeviceCodeReturned).ExecuteAsync(CancellationToken.None);
                        return result;
                    }
                    catch (MsalUiRequiredException ex)
                    {
                        Debug.LogError("MsalUiRequiredException");
                        Debug.LogException(ex);
                    }
                    catch (MsalServiceException ex)
                    {
                        Debug.LogError("MsalServiceException");
                        Debug.LogException(ex);
                    }
                    catch (MsalClientException ex)
                    {
                        Debug.LogError("MsalClientException");
                        Debug.LogException(ex);
                        // Mitigation: Use interactive authentication
                    }
                    catch (Exception ex)
                    {
                        Debug.LogError("Exception");
                        Debug.LogException(ex);
                    }
                }
            }
            catch (Exception ex)
            {
                Debug.LogError("GetAccountsAsync");
                Debug.LogException(ex);
            }

            return null;
        }
    }
    ```

>[!NOTE]
> 이 코드는 아직 완전하지 않으며 상업용 애플리케이션에 사용할 수 없습니다. 예를 들어 적어도 로그아웃 기능을 추가하는 것이 좋습니다. 이 작업은 클라이언트 애플리케이션에서 제공하는 `Task RemoveAsync(IAccount account)` 메서드를 사용하여 수행할 수 있습니다. 이 코드는 자습서에만 사용할 수 있도록 작성되었으며, 실제 구현은 애플리케이션에 따라 달라집니다.

이 코드는 먼저 **AquireTokenSilent**를 사용하여 토큰을 자동으로 가져오려고 시도합니다. 사용자가 이전에 이 애플리케이션을 인증했다면 이 작업이 성공합니다. 실패하면 사용자 개입이 더 많은 전략으로 넘어갑니다.

이 코드에서는 [디바이스 코드 흐름](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)을 사용하여 액세스 토큰을 가져옵니다. 이 흐름을 사용하면 사용자가 컴퓨터 또는 모바일 디바이스에서 자신의 Azure 계정에 로그인하여 결과 토큰을 HoloLens 애플리케이션으로 다시 보낼 수 있습니다.

ARR 관점에서 이 클래스의 가장 중요한 부분은 다음 줄입니다.

```csharp
return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
```

여기서는 계정 도메인, 계정 ID 및 액세스 토큰을 사용하여 새 **AzureFrontendAccountInfo** 개체를 만듭니다. 이전에 구성된 역할 기반 권한에 따라 사용자에게 권한이 부여되는 한, ARR 서비스에서 원격 렌더링 세션을 쿼리, 생성 및 조인할 때 이 토큰이 사용됩니다.

이렇게 변경한 후, 애플리케이션의 현재 상태와 Azure 리소스에 대한 액세스는 다음과 같습니다.

![훨씬 강력한 보안](./media/security-three.png)

사용자 자격 증명은 디바이스에 저장되지 않으므로(심지어 이 예에서는 디바이스에 들어오지도 않음) 노출 위험이 매우 낮습니다. 이제 디바이스에서는 시간이 제한된 사용자별 액세스 토큰을 사용하여 ARR에 액세스하고, ARR은 액세스 제어(IAM)를 사용하여 Blob Storage에 액세스합니다. 이러한 두 단계를 통해 소스 코드에서 "암호"가 완전히 제거되고 보안이 대폭 강화되었습니다. 그러나 가장 강력한 보안은 아니며, 모델 및 세션 관리를 웹 서비스로 이동하면 보안이 더욱 강화됩니다. 추가 보안 고려 사항은 [상업적 준비](../commercial-ready/commercial-ready.md) 챕터에서 설명합니다.

### <a name="testing-aad-auth"></a>AAD 인증 테스트

Unity 편집기에서 AAD 인증이 활성화되면 애플리케이션을 시작할 때마다 인증해야 합니다. 디바이스에서 인증 단계가 처음으로 수행되며, 토큰이 만료되거나 무효화될 때에만 다시 인증이 필요합니다.

1. **AADAuthentication** 구성 요소를 **RemoteRenderingCoordinator** GameObject에 추가합니다.

    ![AAD 인증 구성 요소](./media/azure-active-directory-auth-component.png)

1. 클라이언트 ID 및 테넌트 ID의 값을 입력합니다. 두 값은 앱 등록의 개요 페이지에서 찾을 수 있습니다.

    * **계정 도메인**은 **RemoteRenderingCoordinator**의 계정 도메인에서 사용하던 것과 동일한 도메인입니다.
    * **Active Directory 애플리케이션 클라이언트 ID**는 AAD 앱 등록에서 찾을 수 있는 *애플리케이션(클라이언트) ID*입니다(아래 이미지 참조).
    * **Azure 테넌트 ID**는 AAD 앱 등록에서 찾을 수 있는 *디렉터리(테넌트) ID*입니다(아래 이미지 참조).
    * **Azure Remote Rendering 계정 ID**는 **RemoteRenderingCoordinator**에 사용했던 것과 동일한 **계정 ID**입니다.

    ![AAD 인증 구성 요소](./media/app-overview-data.png)

1. Unity 편집기에서 재생을 누르고 세션 실행에 동의합니다.
    **AADAuthentication** 구성 요소에는 보기 컨트롤러가 있으므로, 세션 권한 부여 모달 패널 뒤에 프롬프트를 표시하도록 자동으로 연결됩니다.
1. **AppMenu**의 오른쪽 패널에 있는 지침을 따르세요.
    다음과 유사한 내용이 표시되어야 합니다. ![AAD 인증 구성 요소](./media/device-flow-instructions.png) 보조 디바이스(또는 동일한 디바이스의 브라우저)에서 제공된 코드를 입력하고 자격 증명을 사용하여 로그인하면 요청 애플리케이션(여기서는 Unity 편집기)에 액세스 토큰이 반환됩니다.
1. 이 시점 이후로 애플리케이션의 모든 것이 정상적으로 진행됩니다. 스테이지가 예상대로 진행되지 않으면 Unity 콘솔에서 오류를 확인합니다.

## <a name="build-to-device"></a>디바이스에 빌드

MSAL을 사용하여 디바이스에 애플리케이션을 빌드하는 경우 프로젝트의 **자산** 폴더에 파일을 포함해야 합니다. 이렇게 하면 컴파일러가 **자습서 자산**에 포함된 *Microsoft.Identity.Client.dll*을 사용하여 애플리케이션을 올바르게 빌드하는 데 도움이 됩니다.

1. **Assets** 폴더에 **link.xml**이라는 새 파일을 추가합니다.
1. 파일에 다음을 추가합니다.

    ```xml
    <linker>
        <assembly fullname="Microsoft.Identity.Client" preserve="all"/>
        <assembly fullname="System.Runtime.Serialization" preserve="all"/>
        <assembly fullname="System.Core">
            <type fullname="System.Linq.Expressions.Interpreter.LightLambda" preserve="all" />
        </assembly>
    </linker>
    ```

1. 변경 내용 저장

[빠른 시작: HoloLens에 Unity 샘플 배포 - 샘플 프로젝트 빌드](../../../quickstarts/deploy-to-hololens.md#build-the-sample-project)의 단계에 따라 HoloLens에 빌드합니다.

## <a name="next-steps"></a>다음 단계

이 자습서의 나머지 부분에는 Azure Remote Rendering을 사용하는 프로덕션 준비 완료 애플리케이션을 만들기 위한 개념 토픽이 포함되어 있습니다.

> [!div class="nextstepaction"]
> [다음: 상업적 준비](../commercial-ready/commercial-ready.md)
