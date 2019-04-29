---
title: PowerShell을 사용하여 Notification Hubs 배포 및 관리
description: Automation을 위한 PowerShell을 사용하여 Notification Hubs를 만들고 관리하는 방법
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 4dbbaeea736dd46478ad9992201ea28bd7bfc2ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457841"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>PowerShell을 사용하여 Notification Hubs 배포 및 관리

## <a name="overview"></a>개요

이 문서는 PowerShell을 사용하여 Azure Notification Hubs를 만들기 및 관리 방법을 보여줍니다. 다음과 같은 일반적인 자동화 작업은 이 문서에 표시됩니다.

- 알림 허브 만들기
- 자격 증명 설정

알림 허브용 새 서비스 버스 네임스페이스를 만드는 경우 [PowerShell로 Service Bus 관리](../service-bus-messaging/service-bus-powershell-how-to-provision.md)를 참조하세요.

알림 허브 관리는 Azure PowerShell에 포함된 cmdlet에서 직접 지원하지 않습니다. PowerShell에서 Microsoft.Azure.NotificationHubs.dll 어셈블리를 참조하는 것이 최선의 방법입니다. 어셈블리는 [Microsoft Azure Notification Hubs NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)와 함께 배포됩니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독. Azure는 구독 기반 플랫폼입니다. 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션], [구성원 제안] 또는 [평가판]을 참조하세요.
- Azure PowerShell이 설치된 컴퓨터 자세한 내용은 [Azure PowerShell 설치 및 구성]을 참조하세요.
- PowerShell 스크립트, NuGet 패키지 및 .NET Framework 전반에 대한 지식

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>.NET 어셈블리에 Service Bus에 대한 참조 포함

Azure Notification Hubs 관리는 아직 Azure PowerShell에서 PowerShell cmdlet에 포함되지 않습니다. Notification Hubs를 프로비전하려면 [Microsoft Azure Notification Hubs NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)에서 제공된 .NET 클라이언트를 사용할 수 있습니다.

먼저 스크립트가 Visual Studio 프로젝트에서 NuGet 패키지로 설치되는 **Microsoft.Azure.NotificationHubs.dll** 어셈블리를 찾을 수 있는지 확인합니다. 유연한 작업을 위해 스크립트는 다음 단계를 수행합니다.

1. 호출된 경로를 파악합니다.
2. 이름이 `packages`인 폴더를 찾을 때까지 경로를 탐색합니다. 이 폴더는 Visual Studio 프로젝트용 NuGet 패키지를 설치할 때 생성됩니다.
3. `Microsoft.Azure.NotificationHubs.dll` 어셈블리에 대한 `packages` 폴더를 재귀적으로 검색합니다.
4. 해당 형식을 나중에 사용할 수 있도록 어셈블리를 참조합니다.

PowerShell 스크립트에서 이러한 단계는 다음과 같이 구현됩니다.

``` powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>`NamespaceManager` 클래스 만들기

Notification Hubs를 프로비전하려면 SDK에서 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) 클래스의 인스턴스를 만듭니다.

Azure PowerShell을 포함하는 [Get-AzureSBAuthorizationRule] cmdlet을 사용하면 연결 문자열을 제공하는 데 사용되는 권한 부여 규칙을 검색할 수 있습니다. `NamespaceManager` 인스턴스에 대한 참조는 `$NamespaceManager` 변수에 저장됩니다. `$NamespaceManager`를 사용하여 알림 허브를 프로비전합니다.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>새 알림 허브 프로비전

새 Notification Hubs를 프로비전하려면 [Notification Hubs용 .NET API]를 사용합니다.

이 스크립트 부분에서는 4개의 로컬 변수를 설정합니다.

1. `$Namespace`: 알림 허브를 만들려는 네임스페이스 이름으로 설정합니다.
2. `$Path`: 새 알림 허브의 이름으로 설정합니다.  예를 들면 "MyHub"와 같습니다.
3. `$WnsPackageSid`: [Windows 개발자 센터](https://developer.microsoft.com/en-us/windows)의 Windows 앱용 패키지 SID로 설정합니다.
4. `$WnsSecretkey`: [Windows 개발자 센터](https://developer.microsoft.com/en-us/windows)의 Windows 앱 비밀 키로 설정합니다.

이러한 변수를 네임스페이스에 연결하고 새 알림 허브를 만들어서 Windows 앱용 WNS 자격 증명으로 WNS(Windows Notification Services) 알림을 처리합니다. 패키지SID 및 보안 키를 얻는 방법에 대한 정보는 [Notification Hubs 시작](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) 자습서를 참조하세요.

- 스크립트 코드 조각은 `NamespaceManager` 개체를 사용하여 `$Path`로 식별되는 알림 허브가 존재하는지 확인합니다.
- 존재하지 않는 경우 스크립트가 WNS 자격 증명으로 `NotificationHubDescription`을 만들고 `NamespaceManager` 클래스 `CreateNotificationHub` 메서드로 전달합니다.

``` powershell
$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```

## <a name="additional-resources"></a>추가 리소스

- [PowerShell을 사용하여 Service Bus 관리](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [PowerShell 스크립트를 사용하여 Service Bus 큐, 토픽 및 구독을 만드는 방법](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [PowerShell 스크립트를 사용하여 Service Bus 네임스페이스 및 Event Hub를 만드는 방법](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

즉시 사용 가능한 스크립트도 다운로드 가능합니다.

- [Service Bus PowerShell 스크립트](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[구매 옵션]: https://azure.microsoft.com/pricing/purchase-options/
[구성원 제안]: https://azure.microsoft.com/pricing/member-offers/
[평가판]: https://azure.microsoft.com/pricing/free-trial/
[Azure PowerShell 설치 및 구성]: /powershell/azureps-cmdlets-docs
[Notification Hubs용 .NET API]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule
