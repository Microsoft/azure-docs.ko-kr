---
title: 등록 관리
description: 이 항목에서는 푸시 알림을 받기 위해 알림 허브에 디바이스를 등록하는 방법에 대해 설명합니다.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.author: jowargo
ms.date: 04/08/2019
ms.openlocfilehash: 64c2cd0ed1572fdaaa42f4731519ba6d5c320f1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457739"
---
# <a name="registration-management"></a>등록 관리

## <a name="overview"></a>개요

이 항목에서는 푸시 알림을 받기 위해 알림 허브에 디바이스를 등록하는 방법에 대해 설명합니다. 먼저 높은 수준에서 등록을 설명한 다음 장치를 등록하는 두 가지 주 패턴, 즉 장치에서 알림 허브에 직접 등록 및 애플리케이션 백 엔드를 통해 등록을 소개합니다.

## <a name="what-is-device-registration"></a>디바이스 등록이란?

Notification Hub에 디바이스 등록은 **등록** 또는 **설치**를 사용하여 수행됩니다.

### <a name="registrations"></a>등록

등록은 태그 및 아마도 템플릿을 가진 디바이스에 대해 플랫폼 알림 서비스(PNS) 핸들을 연결합니다. PNS 핸들은 ChannelURI, 디바이스 토큰 또는 FCM 등록 ID일 수 있습니다. 태그는 디바이스 핸들의 정확한 집합에 알림을 올바른 디바이스 핸들 집합에 라우팅하기 위해 사용됩니다. 자세한 내용은 [라우팅 및 태그 식](notification-hubs-tags-segment-push-message.md)을 참조하세요. 템플릿은 등록당 변환을 구현하는 데 사용됩니다. 자세한 내용은 [템플릿](notification-hubs-templates-cross-platform-push-messages.md)을 사용하세요.

> [!NOTE]
> Azure Notification Hubs는 최대 장치 당 60 태그를 지원합니다.

### <a name="installations"></a>설치

설치는 푸시 모음 관련 속성을 포함하고 있는 향상된 등록입니다. 이는 디바이스 등록에 대한 최근의 가장 우수한 방식입니다. 그러나 아직 클라이언트 쪽 .NET SDK([백 엔드 작업을 위한 알림 허브 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/))에서는 지원되지 않습니다.  즉, 클라이언트 디바이스 자체에서 등록하는 경우 설치를 지원하기 위해 [Notification Hubs REST API](https://docs.microsoft.com/en-us/rest/api/notificationhubs/create-overwrite-installation) 접근 방식을 사용해야 합니다. 백 엔드 서비스를 사용하는 경우 [백 엔드 작업을 위한 알림 허브 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)를 사용할 수 있습니다.

설치 사용의 몇 가지 주요 장점은 다음과 같습니다.

- 설치 만들기 또는 업데이트가 완전히 멱등 상태입니다. 따라서 중복 등록을 걱정할 필요 없이 재시도할 수 있습니다.
- 설치 모델 특수 태그 형식을 지원 (`$InstallationId:{INSTALLATION_ID}`) 알림을 특정 장치에 직접 보낼 수 있도록 합니다. 예를 들어, 앱의 코드의 설치 ID를 설정 하는 경우 `joe93developer` 이 특정 장치에 대 한 개발자에 대 한 알림을 보낼 때이 장치를 대상 수를 `$InstallationId:{joe93developer}` 태그입니다. 따라서 추가 코딩 하지 않고도 특정 장치를 선택할 수 있습니다.
- 또한 설치를 사용하여 부분적인 등록 업데이트를 수행할 수도 있습니다. 설치의 부분 업데이트는 [JSON 패치 표준](https://tools.ietf.org/html/rfc6902)을 사용하여 PATCH 메서드에서 요청됩니다. 이는 등록 시 태그를 업데이트하려고 할 때 유용합니다. 전체 등록을 풀다운한 다음 모든 이전 태그를 다시 보낼 필요가 없습니다.

설치는 다음과 같은 속성을 포함할 수 있습니다. 설치 속성의 전체 목록은 [REST API를 사용하여 설치 만들기 또는 덮어쓰기](https://docs.microsoft.com/en-us/rest/api/notificationhubs/create-overwrite-installation) 또는 [설치 속성](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx)을 참조하세요.

```json
// Example installation format to show some supported properties
{
    installationId: "",
    expirationTime: "",
    tags: [],
    platform: "",
    pushChannel: "",
    ………
    templates: {
        "templateName1" : {
            body: "",
            tags: [] },
        "templateName2" : {
            body: "",
            // Headers are for Windows Store only
            headers: {
                "X-WNS-Type": "wns/tile" }
            tags: [] }
    },
    secondaryTiles: {
        "tileId1": {
            pushChannel: "",
            tags: [],
            templates: {
                "otherTemplate": {
                    bodyTemplate: "",
                    headers: {
                        ... }
                    tags: [] }
            }
        }
    }
}
```

> [!NOTE]
> 기본적으로 등록 및 설치는 만료되지 않습니다.

등록 및 설치는 각 디바이스/채널에 대한 유효한 PNS 핸들을 포함해야 합니다. PNS 핸들은 디바이스의 클라이언트 앱에서만 획득할 수 있으므로 한 가지 패턴은 클라이언트 앱을 사용하여 해당 디바이스에서 직접 등록하는 것입니다. 한편 태그와 관련된 보안 고려 사항 및 비즈니스 논리 때문에 앱 백 엔드에서 디바이스 등록을 관리해야 할 수 있습니다.

### <a name="templates"></a>템플릿

[템플릿](notification-hubs-templates-cross-platform-push-messages.md)을 사용하려면 장치 설치에서 해당 장치와 연결된 모든 템플릿을 JSON 형식으로 유지합니다(위 샘플 참조). 템플릿 이름은는 동일한 디바이스에 대한 서로 다른 템플릿을 대상 도움이 됩니다.

각 템플릿 이름은 템플릿 본문 및 선택적 태그 집합에 매핑됩니다. 또한 각 플랫폼이 추가 템플릿 속성을 가질 수 있습니다. Windows 스토어(WNS 사용) 및 Windows Phone 8(MPNS 사용)의 경우 추가 헤더 집합이 템플릿의 일부일 수 있습니다. APN의 경우 만료 속성을 상수 또는 템플릿 식으로 설정할 수 있습니다. 설치 속성의 전체 목록은 [REST를 사용하여 설치 만들기 또는 덮어쓰기](https://msdn.microsoft.com/library/azure/mt621153.aspx) 항목을 참조하세요.

### <a name="secondary-tiles-for-windows-store-apps"></a>Windows 스토어 앱용 보조 타일

Windows 스토어 클라이언트 애플리케이션의 경우 보조 타일에 알림을 보내는 것은 기본 타일에 보내는 것과 같습니다. 이 기능은 설치에서도 지원됩니다. 보조 타일에는 클라이언트 앱의 SDK가 투명하게 처리하는 다른 ChannelUri가 있습니다.

SecondaryTiles 사전은 Windows 스토어 앱에서 SecondaryTiles 개체를 만드는 데 사용하는 것과 같은 TileId를 사용합니다. 기본 ChannelUri와 마찬가지로 보조 타일의 ChannelUris도 언제든지 변경할 수 있습니다. 알림 허브의 설치를 계속 업데이트되게 하려면 디바이스가 설치를 보조 타일의 현재 ChannelUris로 새로 고쳐야 합니다.

## <a name="registration-management-from-the-device"></a>디바이스에서 등록 관리

클라이언트 앱에서 디바이스 등록을 관리하는 경우 백 엔드만는 알림을 보내기만 합니다. 클라이언트 앱은 PNS 핸들을 최신 상태로 유지하고 태그를 등록합니다. 다음 그림은 이 패턴을 보여 줍니다.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

디바이스는 먼저 PNS에서 PNS 핸들을 검색한 다음 알림 허브에 직접 등록합니다. 등록이 성공한 후 앱 백 엔드는 해당 등록을 대상으로 지정하는 알림을 보낼 수 있습니다. 알림을 보내는 방법에 대한 자세한 내용은 [라우팅 및 태그 식](notification-hubs-tags-segment-push-message.md)을 참조하세요.

이 경우 수신 권한만 사용하여 장치에서 사용자의 알림 허브에 액세스합니다. 자세한 내용은 [보안](notification-hubs-push-notification-security.md)을 참조하세요.

디바이스에서 등록은 가장 간단한 방법이지만 몇 가지 단점이 있습니다.

- 클라이언트 앱은 활성 상태일 때에만 태그를 업데이트할 수 있습니다. 예를 들어 사용자가 스포츠 팀과 관련된 태그를 등록하는 디바이스 두 개를 가지고 있는 경우 첫 번째 디바이스가 추가 태그(예: Seahawks)에 대해 등록하면 두 번째 디바이스는 두 번째 디바이스의 앱이 두 번째로 실행될 때까지 Seahawks에 관한 알림을 받지 않습니다. 더 일반적으로 태그가 여러 디바이스의 영향을 받는 경우 백 엔드에서 태그 관리가 바람직한 옵션입니다.
- 앱이 해킹을 당할 수 있으므로 “태그 수준 보안” 섹션에서 설명했듯이 특정 태그에 대한 등록을 보호하려면 좀 더 주의해야 합니다.

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>설치를 사용하여 디바이스에서 알림 허브에 등록하는 예제 코드

현재 이 기능은 [Notification Hubs REST API](https://msdn.microsoft.com/library/mt621153.aspx)를 사용해서만 지원됩니다.

설치를 업데이트하는 경우 [JSON 패치 표준](https://tools.ietf.org/html/rfc6902) 을 사용하여 PATCH 메서드를 사용할 수도 있습니다.

```
class DeviceInstallation
{
    public string installationId { get; set; }
    public string platform { get; set; }
    public string pushChannel { get; set; }
    public string[] tags { get; set; }
}

private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
        string hubName, string listenConnectionString)
{
    if (deviceInstallation.installationId == null)
        return HttpStatusCode.BadRequest;

    // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
    ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
    string hubResource = "installations/" + deviceInstallation.installationId + "?";
    string apiVersion = "api-version=2015-04";

    // Determine the targetUri that we will sign
    string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

    //=== Generate SaS Security Token for Authorization header ===
    // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
    string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

    using (var httpClient = new HttpClient())
    {
        string json = JsonConvert.SerializeObject(deviceInstallation);

        httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

        var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
        return response.StatusCode;
    }
}

var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

string installationId = null;
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored an installation id in application data, create and store as application data.
if (!settings.ContainsKey("__NHInstallationId"))
{
    installationId = Guid.NewGuid().ToString();
    settings.Add("__NHInstallationId", installationId);
}

installationId = (string)settings["__NHInstallationId"];

var deviceInstallation = new DeviceInstallation
{
    installationId = installationId,
    platform = "wns",
    pushChannel = channel.Uri,
    //tags = tags.ToArray<string>()
};

var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                    "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

if (statusCode != HttpStatusCode.Accepted)
{
    var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
else
{
    var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>등록을 사용하여 디바이스에서 알림 허브에 등록하는 예제 코드

이러한 메서드는 호출되는 디바이스에 대한 등록을 만들거나 업데이트합니다. 즉, 핸들이나 태그를 업데이트하려면 전체 등록을 덮어써야 합니다. 참고로 등록은 일시적이므로 언제나 특정 디바이스에 필요한 현재 태그와 함께 신뢰할 수 있는 저장소가 있어야 합니다.

```
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device id from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration id in device
// storage. Then when the app starts, you can check if a registration id already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration id in application data, store in application data.
if (!settings.ContainsKey("__NHRegistrationId"))
{
    // make sure there are no existing registrations for this push handle (used for iOS and Android)    
    string newRegistrationId = null;
    var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
    foreach (RegistrationDescription registration in registrations)
    {
        if (newRegistrationId == null)
        {
            newRegistrationId = registration.RegistrationId;
        }
        else
        {
            await hub.DeleteRegistrationAsync(registration);
        }
    }

    newRegistrationId = await hub.CreateRegistrationIdAsync();

    settings.Add("__NHRegistrationId", newRegistrationId);
}

string regId = (string)settings["__NHRegistrationId"];

RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
registration.RegistrationId = regId;
registration.Tags = new HashSet<string>(YourTags);

try
{
    await hub.CreateOrUpdateRegistrationAsync(registration);
}
catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
{
    settings.Remove("__NHRegistrationId");
}
```

## <a name="registration-management-from-a-backend"></a>백 엔드에서 등록 관리

백 엔드에서 등록을 관리하려면 추가 코드를 작성해야 합니다. 디바이스의 앱은 앱이 시작될 때마다 업데이트된 PNS 핸들을 백 엔드에 제공해야 하며(태그 및 템플릿과 함께), 백 엔드는 알림 허브에 대해 이 핸들을 업데이트해야 합니다. 다음 그림은 이 디자인을 보여 줍니다.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

백 엔드에서 등록을 관리할 경우의 이점은 디바이스의 해당 앱이 비활성화되었을 때에도 등록에 대한 태그를 수정할 수 있고 태그를 등록에 추가하기 전에 클라이언트 앱을 인증할 수 있다는 것입니다.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>설치를 사용하여 백 엔드에서 알림 허브에 등록하는 예제 코드

클라이언트 디바이스는 여전히 해당 PNS 핸들 및 관련 설치 속성을 전과 같이 가져오며 등록을 수행하고 태그 등에 권한을 부여할 수 있는 백 엔드의 사용자 지정 API를 호출합니다. 백 엔드는 [백 엔드 작업용 Notification Hub SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)를 활용할 수 있습니다.

설치를 업데이트하는 경우 [JSON 패치 표준](https://tools.ietf.org/html/rfc6902) 을 사용하여 PATCH 메서드를 사용할 수도 있습니다.

```
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// Custom API on the backend
public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
{

    Installation installation = new Installation();
    installation.InstallationId = deviceUpdate.InstallationId;
    installation.PushChannel = deviceUpdate.Handle;
    installation.Tags = deviceUpdate.Tags;

    switch (deviceUpdate.Platform)
    {
        case "mpns":
            installation.Platform = NotificationPlatform.Mpns;
            break;
        case "wns":
            installation.Platform = NotificationPlatform.Wns;
            break;
        case "apns":
            installation.Platform = NotificationPlatform.Apns;
            break;
        case "fcm":
            installation.Platform = NotificationPlatform.Fcm;
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }


    // In the backend we can control if a user is allowed to add tags
    //installation.Tags = new List<string>(deviceUpdate.Tags);
    //installation.Tags.Add("username:" + username);

    await hub.CreateOrUpdateInstallationAsync(installation);

    return Request.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>등록 ID를 사용하여 장치에서 알림 허브에 등록하는 예제 코드

앱 백 엔드에서 등록에 대해 기본 CRUDS 작업을 수행할 수 있습니다. 예를 들면 다음과 같습니다.

```
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by id
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

백 엔드에서 등록 업데이트 간의 동시성을 처리해야 합니다. Service Bus는 등록 관리를 위해 낙관적 동시성 제어를 제공합니다. HTTP 수준에서 이 기능은 등록 관리 작업에 ETag를 사용하여 구현 됩니다. 이 기능은 동시성 문제로 인해 업데이트가 거부될 경우 예외를 throw하는 Microsoft SDK에 의해 투명하게 사용됩니다. 앱 백 엔드는 이러한 예외를 처리하고 필요한 경우 업데이트를 다시 시도하는 일을 담당합니다.
