---
title: Azure Relay를 사용하여 온-프레미스 WCF REST 서비스를 클라이언트에 노출
description: 이 자습서에서는 Azure WCF Relay를 사용하여 온-프레미스 WCF REST 서비스를 외부 클라이언트에 노출하는 방법을 설명합니다.
ms.topic: tutorial
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: bb2b9b5ed7c263762cc24b8eb2e6d66215147c4c
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935707"
---
# <a name="tutorial-expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>자습서: Azure WCF Relay를 사용하여 외부 클라이언트에 온-프레미스 WCF REST 서비스 노출

이 자습서에서는 Azure Relay를 사용하여 WCF Relay 클라이언트 애플리케이션 및 서비스를 빌드하는 방법을 설명합니다. [Service Bus 메시징](../service-bus-messaging/service-bus-messaging-overview.md)을 사용하는 유사한 자습서는 [Service Bus 큐 시작](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)을 참조하세요.

이 자습서를 통해 WCF Relay 클라이언트 및 서비스 애플리케이션을 만드는 단계를 이해할 수 있습니다. 원래 WCF와 마찬가지로, 서비스는 하나 이상의 엔드포인트를 노출하는 구문입니다. 각 엔드포인트는 하나 이상의 서비스 작업을 노출합니다. 서비스 엔드포인트는 서비스를 찾을 수 있는 주소, 클라이언트가 서비스와 통신해야 하는 정보가 포함된 바인딩, 서비스가 클라이언트에 제공하는 기능을 정의하는 계약을 지정합니다. WCF와 WCF 릴레이 간의 가장 큰 차이는 엔드포인트가 컴퓨터의 로컬이 아닌 클라우드에서 노출된다는 점입니다.

이 자습서의 섹션을 순서대로 완료하면 실행 중인 서비스가 하나 생깁니다. 서비스의 작업을 호출할 수 있는 클라이언트도 하나 생깁니다. 

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
>
> * 이 자습서를 수행하기 위한 필수 조건 설치
> * Relay 네임스페이스 만들기
> * WCF 서비스 계약 만들기
> * WCF 계약 구현
> * 릴레이 서비스에 등록할 WCF 서비스 호스트 및 실행
> * 서비스 계약에 대한 WCF 클라이언트 만들기
> * WCF 클라이언트 구성
> * WCF 클라이언트 구현
> * 애플리케이션 실행

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* Azure 구독 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
* [Visual Studio 2015 이상](https://www.visualstudio.com) - 이 자습서의 예제에서는 Visual Studio 2019를 사용합니다.
* Azure SDK for .NET. [SDK 다운로드 페이지](https://azure.microsoft.com/downloads/)에서 설치합니다.

## <a name="create-a-relay-namespace"></a>Relay 네임스페이스 만들기

첫 단계는 네임스페이스를 만들고 [SAS(공유 액세스 서명)](../service-bus-messaging/service-bus-sas.md) 키를 확보합니다. 네임스페이스는 릴레이 서비스를 통해 노출되는 각 애플리케이션에 대한 애플리케이션 경계를 제공합니다. SAS 키는 서비스 네임스페이스가 만들어질 때 시스템에 의해 자동으로 생성됩니다. 서비스 네임스페이스 및 SAS 키 조합은 Azure에 자격 증명을 제공하여 애플리케이션에 대한 액세스를 인증합니다.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>WCF 서비스 계약 정의

서비스 계약은 서비스에서 지원하는 작업을 지정합니다. 작업은 웹 서비스 메서드 또는 함수입니다. 계약은 C++, C#, 또는 Visual Basic 인터페이스를 정의하여 만듭니다. 인터페이스의 각 메서드는 특정 서비스 작업에 해당합니다. 각 인터페이스에는 [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) 특성이 적용되어야 하고 각 작업에는 [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) 특성이 적용되어야 합니다. [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) 특성이 적용된 인터페이스의 메서드에 [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) 특성이 지정되지 않은 경우 해당 메서드가 노출되지 않습니다. 이 작업을 위한 코드는 과정을 수행하면서 예제에 제공됩니다. 대규모 계약 및 서비스에 대한 자세한 내용은 [서비스 디자인 및 구현](/dotnet/framework/wcf/designing-and-implementing-services)을 참조하세요.

### <a name="create-a-relay-contract-with-an-interface"></a>인터페이스와 함께 릴레이 계약 만들기

1. 관리자 권한으로 Microsoft Visual Studio를 시작합니다. 이렇게 하려면 Visual Studio 프로그램 아이콘을 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 선택합니다.
1. Visual Studio에서 **새 프로젝트 만들기**를 선택합니다.
1. **새 프로젝트 만들기**에서 C#용 **콘솔 앱(.NET Framework)** 을 선택하고, **다음**을 선택합니다.
1. 프로젝트 이름을 *EchoService*로 지정하고 **만들기**를 선택합니다.

   ![콘솔 앱 만들기][2]

1. **솔루션 탐색기**에서 프로젝트의 이름을 마우스 오른쪽 단추를 클릭하고 **NuGet 패키지 관리**를 선택합니다. **NuGet 패키지 관리자**에서 **찾아보기**를 선택한 다음, **WindowsAzure.ServiceBus**를 검색하여 선택합니다. **설치**를 선택하고 사용 약관에 동의합니다.

    ![Service Bus 패키지][3]

   이 패키지는 Service Bus 라이브러리 및 WCF `System.ServiceModel`에 대한 참조를 자동으로 추가합니다. [System.ServiceModel](/dotnet/api/system.servicemodel)은 WCF의 기본 기능에 프로그래밍 방식으로 액세스할 수 있도록 하는 네임스페이스입니다. Service Bus는 WCF의 많은 개체와 특성을 사용하여 서비스 계약을 정의합니다.

1. *Program.cs*의 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. 네임스페이스 이름을 기본 이름인 `EchoService`에서 `Microsoft.ServiceBus.Samples`로 변경합니다.

   > [!IMPORTANT]
   > 이 자습서에서는 [WCF 클라이언트 구성](#configure-the-wcf-client) 섹션에서 구성 파일에 사용되는 계약 기반 관리형 유형의 네임스페이스인 C# 네임스페이스 `Microsoft.ServiceBus.Samples`를 사용합니다. 이 샘플을 빌드할 때 원하는 네임스페이스를 지정할 수 있습니다. 애플리케이션 구성 파일에서 계약과 서비스의 네임스페이스를 그에 맞게 수정하지 않으면 자습서가 작동하지 않습니다. *App.config* 파일에서 지정한 네임스페이스는 C# 파일에서 지정한 네임스페이스와 동일합니다.
   >

1. `Microsoft.ServiceBus.Samples` 네임스페이스 선언 직후 네임스페이스 안에서 이름이 `IEchoContract`인 새 인터페이스를 정의하고 네임스페이스 값이 `https://samples.microsoft.com/ServiceModel/Relay/`인 `ServiceContractAttribute` 특성을 해당 인터페이스에 적용합니다. 네임스페이스 선언 후 다음 코드를 붙여넣습니다.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    네임스페이스 값은 코드 전반에 사용하는 네임스페이스에 따라 다릅니다. 대신 네임스페이스 값은 이 계약에 대한 고유 식별자로 사용됩니다. 네임스페이스를 명시적으로 지정하면 기본 네임스페이스 값이 계약 이름에 추가되는 경우를 방지합니다.

   > [!NOTE]
   > 일반적으로 서비스 계약 네임스페이스는 버전 정보가 들어있는 명명 체계를 포함합니다. 서비스 계약 네임 스페이스에 버전 정보를 포함하면 서비스에서 새로운 네임스페이스로 새 서비스 계약을 정의하고 새 엔드포인트에 노출함으로써 주요 변경 내용을 분리할 수 있습니다. 이렇게 하면 클라이언트가 업데이트 없이도 기존의 서비스 계약을 지속적으로 사용할 수 있습니다. 버전 정보는 날짜 또는 빌드 번호로 구성될 수 있습니다. 자세한 내용은 [서비스 버전 관리](/dotnet/framework/wcf/service-versioning)를 참조하세요. 이 자습서에 나오는 서비스 계약 네임스페이스의 명명 체계에는 버전 정보가 없습니다.
   >

1. `IEchoContract` 인터페이스 내에서 다음과 같이 `IEchoContract` 계약이 인터페이스에 노출하는 단일 작업에 대한 메서드를 선언하고 공개 WCF 릴레이 계약의 일부로 노출하려는 메서드에 `OperationContractAttribute` 특성을 적용합니다.

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. 다음과 같이 `IEchoContract` 인터페이스 정의 바로 다음에 `IEchoContract` 및 `IClientChannel` 인터페이스에서 모두 상속되는 채널을 선언합니다.

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    채널은 호스트 및 클라이언트가 서로 정보를 전달하는 WCF 개체입니다. 나중에 두 애플리케이션 간에 정보를 에코할 채널에 대한 코드를 작성합니다.

1. **빌드** > **솔루션 빌드** 를 클릭하거나 Ctrl+Shift+B를 눌러 지금까지 수행한 작업이 정확한지 확인합니다.

### <a name="example-of-a-wcf-contract"></a>WCF 계약의 예

다음 코드는 WCF 릴레이 계약을 정의하는 기본 인터페이스를 보여 줍니다.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

이제 인터페이스를 만들었으므로 해당 인터페이스를 구현할 수 있습니다.

## <a name="implement-the-wcf-contract"></a>WCF 계약 구현

Azure 릴레이를 만들려면 먼저 인터페이스를 사용하여 계약을 만들어야 합니다. 인터페이스를 만드는 방법에 자세한 내용은 이전 섹션을 참조하세요. 그 다음 절차는 인터페이스 구현입니다. 여기에는 사용자 정의 `IEchoContract` 인터페이스를 구현하는 이름이 `EchoService`인 클래스를 만드는 작업이 포함됩니다. 인터페이스를 구현한 후에는 *App.config* 구성 파일을 사용하여 인터페이스를 구성합니다. 구성 파일에는 애플리케이션에 필요한 정보가 포함되어 있습니다. 이 정보에는 서비스 이름, 계약 이름, 릴레이 서비스와 통신하는 데 사용되는 프로토콜 유형이 있습니다. 이 작업에 사용되는 코드는 절차를 수행하는 예제에 제공됩니다. 서비스 계약을 구현하는 방법에 대한 보다 일반적인 내용은 [서비스 계약 구현](/dotnet/framework/wcf/implementing-service-contracts)을 참조하세요.

1. `IEchoContract` 인터페이스 정의 바로 뒤에 이름이 `EchoService`인 클래스를 새로 만듭니다. `EchoService` 클래스가 `IEchoContract` 인터페이스를 구현합니다.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    다른 인터페이스 구현과 유사하게, 다른 파일에 정의를 구현할 수 있습니다. 하지만, 이 자습서에서는 구현이 인터페이스 정의 및 `Main()` 메서드와 같은 파일에 위치합니다.

1. [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) 특성을 `IEchoContract` 인터페이스에 적용합니다. 특성은 서비스 이름 및 네임스페이스를 지정합니다. 그러면 `EchoService` 클래스는 다음과 같이 표시됩니다.

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. `EchoService` 클래스의 `IEchoContract` 인터페이스에서 정의된 `Echo` 메서드를 구현합니다.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. **빌드** > **솔루션 빌드** 또는 Ctrl+Shift+B를 선택합니다.

### <a name="define-the-configuration-for-the-service-host"></a>서비스 호스트에 대한 구성 정의

구성 파일은 WCF 구성 파일과 유사합니다. 구성 파일에는 서비스 이름, 엔드포인트 및 바인딩이 들어 있습니다. 엔드포인트는 클라이언트와 호스트가 서로 통신할 수 있도록 Azure Relay가 노출하는 위치입니다. 바인딩은 통신에 사용되는 프로토콜 유형입니다. 주요 차이점은 구성된 이 서비스 엔드포인트가 .NET Framework에 속하지 않는 [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) 바인딩을 참조한다는 것입니다. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding)은 서비스에서 정의한 바인딩 중 하나입니다.

1. **솔루션 탐색기**에서 **App.config** 파일을 두 번 클릭하여 Visual Studio 편집기에서 엽니다.
1. `<appSettings>` 요소에서 자리 표시자를 이전 단계에서 복사한 서비스 네임스페이스 및 SAS 키의 이름으로 바꿉니다.
1. `<system.serviceModel>` 태그 안에 `<services>` 요소를 추가합니다. 단일 구성 파일 내에 여러 릴레이 애플리케이션을 정의할 수 있습니다. 그러나 이 자습서에서는 하나만 정의합니다.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. `<services>` 요소 안에서 `<service>` 요소를 추가하여 서비스의 이름을 정의합니다.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. `<service>` 요소 안에서 엔드포인트 계약의 위치와 해당 엔드포인트의 바인딩 형식을 정의합니다.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    엔드포인트는 클라이언트가 호스트 애플리케이션을 검색하는 위치를 정의합니다. 나중에 이 자습서에서는 이 단계를 사용하여 Azure Relay를 통해 호스트를 완전히 공개하는 URI를 만듭니다. 바인딩은 릴레이 서비스와의 통신에 TCP를 프로토콜로 사용한다고 선언합니다.

1. **빌드** > **솔루션 빌드** 를 클릭하거나 Ctrl+Shift+B를 눌러 지금까지 수행한 작업이 정확한지 확인합니다.

### <a name="example-of-implementation-of-a-service-contract"></a>서비스 계약 구현 예제

다음 코드에서는 서비스 계약의 구현을 보여줍니다.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

다음 코드는 서비스 호스트와 연결된 *App.config* 파일의 기본 형식을 보여줍니다.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>릴레이 서비스에 등록할 WCF 서비스 호스트 및 실행

이 단계에서는 Azure Relay 서비스를 실행하는 방법을 설명합니다.

### <a name="create-the-relay-credentials"></a>릴레이 자격 증명 만들기

1. `Main()`에서 콘솔 창으로부터 읽은 네임스페이스와 SAS 키를 저장할 두 변수를 만듭니다.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    SAS 키는 나중에 프로젝트에 액세스하는 데 사용됩니다. 네임스페이스는 서비스 URI를 만들기 위해 매개 변수 형태로 `CreateServiceUri`에 전달됩니다.

1. [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) 개체를 사용하여 SAS 키를 자격 증명 유형으로 사용할 것을 선언합니다. 마지막 단계에서 추가한 코드 바로 뒤에 다음 코드를 추가합니다.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>서비스에 대한 기준 주소 만들기

이전 섹션에서 추가한 코드 뒤에 서비스의 기본 주소에 대한 `Uri` 인스턴스를 만듭니다. 이 URI는 Service Bus 체계, 네임스페이스 및 서비스 인터페이스의 경로를 지정합니다.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

값 "sb"는 Service Bus 체계의 약어이며 TCP를 프로토콜로 사용하고 있음을 나타냅니다. 이 체계는 [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding)이 바인딩으로 지정된 이전 구성 파일에서도 지정되었습니다.

이 자습서에 대한 URI는 `sb://putServiceNamespaceHere.windows.net/EchoService`입니다.

### <a name="create-and-configure-the-service-host"></a>서비스 호스트 만들기 및 구성

1. 아직 `Main()`에서 작업 중인 경우 연결 모드를 `AutoDetect`로 설정합니다.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    연결 모드는 HTTP 또는 TCP 등, 서비스가 릴레이 서비스와의 통신에 사용하는 프로토콜을 설명합니다. 기본 설정 `AutoDetect`를 사용하면 서비스에서는 TCP를 사용할 수 있으면 TCP를 통해, 사용할 수 없으면 HTTP를 통해 Azure Relay에 대한 연결을 시도합니다. 이 결과는 서비스가 클라이언트 통신을 위해 지정하는 프로토콜과는 다릅니다. 해당 프로토콜은 사용한 바인딩에 따라 결정됩니다. 예를 들어, 서비스에서 엔드포인트가 HTTP를 통해 클라이언트와 통신하도록 지정하는 [BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) 바인딩을 사용할 수 있습니다. 서비스가 TCP를 통해 Azure Relay와 통신하도록 동일한 서비스에서 `ConnectivityMode.AutoDetect`를 지정할 수 있습니다.

1. 이 섹션 앞부분에서 만든 URI를 사용하여 서비스 호스트를 만듭니다.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    서비스 호스트는 서비스를 인스턴스화하는 WCF 개체입니다. 만들려는 서비스 형식(`EchoService` 형식)을 이 개체에 전달하고, 서비스를 노출하려는 주소에도 전달합니다.

1. *Program.cs* 파일 맨 위에 [System.ServiceModel.Description](/dotnet/api/system.servicemodel.description) 및 [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description)에 대한 참조를 추가합니다.

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. `Main()`으로 돌아가 공용 액세스가 가능하도록 엔드포인트를 구성합니다.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    이 단계에서는 애플리케이션이 프로젝트에 대한 Atom 피드를 조사하여 공개적으로 찾을 수 있는 릴레이 서비스를 알립니다. `DiscoveryType`을 `private`으로 설정하면 클라이언트가 서비스에 계속 액세스할 수 있습니다. 그러나 `Relay` 네임스페이스를 검색할 때는 서비스가 표시되지 않습니다. 대신 클라이언트 엔드포인트 경로를 미리 알고 있어야 합니다.

1. 서비스 자격 증명을 *App.config* 파일에 정의된 서비스 엔드포인트에 적용합니다.

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    앞에서 설명했듯이, 구성 파일에서 여러 서비스와 엔드포인트를 선언할 수도 있습니다. 그렇게 한 경우 이 코드는 구성 파일을 트래버스하여 자격 증명을 적용해야 하는 모든 엔드포인트를 검색합니다. 이 자습서에서는 구성 파일에 엔드포인트가 하나만 있습니다.

### <a name="open-the-service-host"></a>서비스 호스트 열기

1. 아직 `Main()`에 있는 경우 다음 줄을 추가하여 서비스를 엽니다.

    ```csharp
    host.Open();
    ```

1. 서비스가 실행 중임을 사용자에게 알리고 서비스 종료 방법을 설명합니다.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. 완료되면 서비스 호스트를 닫습니다.

    ```csharp
    host.Close();
    ```

1. Ctrl+Shift+B를 선택하여 프로젝트를 빌드합니다.

### <a name="example-that-hosts-a-service-in-a-console-application"></a>콘솔 애플리케이션에서 서비스를 호스트하는 예제

완료된 서비스 코드는 다음과 같이 표시됩니다. 코드는 자습서에 포함된 이전 단계의 구현 및 서비스 계약을 포함하고 콘솔 애플리케이션에 서비스를 호스트합니다.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>서비스 계약에 대한 WCF 클라이언트 만들기

다음 작업은 클라이언트 애플리케이션을 만들고 나중에 구현할 서비스 계약을 정의하는 것입니다. 이 단계는 서비스를 만드는 데 사용되는 단계(계약을 정의하고, *App.config* 파일을 편집하고, 자격 증명을 사용하여 릴레이 서비스에 연결)와 유사합니다. 이 작업에 사용되는 코드는 과정을 수행하면서 예제에 제공됩니다.

1. 다음과 같이 클라이언트에 대한 현재 Visual Studio 솔루션에서 새 프로젝트를 만듭니다.

   1. **솔루션 탐색기**에서 현재 솔루션(프로젝트 아님)을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 프로젝트**를 선택합니다.
   1. **새 프로젝트 추가**에서 C#용 **콘솔 앱(.NET Framework)** 을 선택하고, **다음**을 선택합니다.
   1. 프로젝트 이름을 *EchoClient*로 지정하고 **만들기**를 선택합니다.

1. Program.cs 파일을 아직 열지 않은 경우 **솔루션 탐색기**의 **EchoClient** 프로젝트에서 **Program.cs** 파일을 두 번 클릭하여 편집기에서 엽니다.
1. 네임스페이스 이름을 기본 이름인 `EchoClient`에서 `Microsoft.ServiceBus.Samples`로 변경합니다.
1. [Service Bus NuGet 패키지](https://www.nuget.org/packages/WindowsAzure.ServiceBus)를 설치합니다.

   1. **솔루션 탐색기**에서 **EchoClient**를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 선택합니다.
   1. **찾아보기**를 선택한 다음, **WindowsAzure.ServiceBus**를 검색하여 선택합니다. **설치**를 선택하고 사용 약관에 동의합니다.

      ![Service Bus 패키지 설치][4]

1. *Program.cs* 파일에 [System.ServiceModel](/dotnet/api/system.servicemodel) 네임스페이스에 대한 `using` 문을 추가합니다.

    ```csharp
    using System.ServiceModel;
    ```

1. 다음 예제와 같이 서비스 계약 정의를 네임스페이스에 추가합니다. 이 정의는 **Service** 프로젝트에 사용되는 정의와 동일합니다. 이 코드를 `Microsoft.ServiceBus.Samples` 네임스페이스 위에 추가합니다.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Ctrl+Shift+B를 선택하여 클라이언트를 빌드합니다.

### <a name="example-of-the-echoclient-project"></a>EchoClient 프로젝트의 예

다음 코드는 **EchoClient** 프로젝트에서 *Program.cs* 파일의 현재 상태를 보여줍니다.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>WCF 클라이언트 구성

이 단계에서는 이 자습서의 앞부분에서 만든 서비스에 액세스하는 기본 클라이언트 애플리케이션의 *App.config* 파일을 만듭니다. 이 *App.config* 파일은 계약, 바인딩 및 엔드포인트 이름을 정의합니다. 이 작업에 사용되는 코드는 과정을 수행하면서 예제에 제공됩니다.

1. **솔루션 탐색기**의 **EchoClient** 프로젝트에서 **App.config**를 두 번 클릭하여 Visual Studio 편집기에서 엽니다.
1. `<appSettings>` 요소에서 자리 표시자를 이전 단계에서 복사한 서비스 네임스페이스 및 SAS 키의 이름으로 바꿉니다.
1. `system.serviceModel` 요소 내에 `<client>` 요소를 추가합니다.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    이 코드는 WCF 스타일 클라이언트 애플리케이션을 정의할 것을 선언합니다.

1. `client` 요소 내에서 엔드포인트의 이름, 계약, 바인딩 형식을 정의합니다.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    이 코드는 엔드포인트 이름을 정의합니다. 또한 서비스에서 정의한 계약, 그리고 클라이언트 애플리케이션이 TCP를 사용하여 Azure Relay와 통신한다는 사실을 정의합니다. 엔드포인트 이름은 이 엔드포인트 구성을 서비스 URI에 연결하는 다음 절차에서 사용됩니다.

1. **파일** > **모두 저장**을 선택합니다.

### <a name="example-of-the-appconfig-file"></a>App.config 파일 예제

다음 코드는 Echo 클라이언트에 대한 *App.config* 파일을 보여줍니다.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>WCF 클라이언트 구현

이 섹션에서는 이 자습서의 앞부분에서 만든 서비스에 액세스하는 기본 클라이언트 애플리케이션을 구현합니다. 서비스와 마찬가지로, 클라이언트는 Azure Relay에 액세스하기 위해 다음과 같은 여러 동일한 작업을 수행합니다.

* 연결 모드를 설정합니다.
* 호스트 서비스를 찾는 URI를 만듭니다.
* 보안 자격 증명을 정의합니다.
* 연결에 자격 증명을 적용합니다.
* 연결을 엽니다.
* 애플리케이션 특정 작업을 수행합니다.
* 연결을 닫습니다.

그러나 주요 차이 중 하나는 클라이언트 애플리케이션이 채널을 사용하여 릴레이 서비스에 연결한다는 것입니다. 반면 서비스는 **ServiceHost**에 대한 호출을 사용합니다. 이 작업에 사용되는 코드는 과정을 수행하면서 예제에 제공됩니다.

### <a name="implement-a-client-application"></a>클라이언트 애플리케이션 구현

1. 연결 모드를 `AutoDetect`로 설정합니다. **EchoClient** 애플리케이션의 `Main()` 메서드 내에 다음 코드를 추가합니다.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. 서비스 네임스페이스에 대한 값과, 콘솔에서 읽은 SAS 키를 담을 변수를 정의합니다.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. 릴레이 프로젝트에서 호스트의 위치를 정의하는 URI를 만듭니다.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. 서비스 네임스페이스 엔드포인트에 대한 자격 증명 개체를 만듭니다.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. *App.config* 파일에 설명된 구성을 로드하는 채널 팩터리를 만듭니다.

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    채널 팩터리는 서비스 및 클라이언트 애플리케이션이 통신하기 위해 경유하는 채널을 만드는 WCF 개체입니다.

1. 자격 증명을 적용합니다.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. 서비스에 대한 채널을 만들어 엽니다.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. 에코에 대해 기본 사용자 인터페이스 및 기능을 작성합니다.

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    이 코드에서는 서비스에 대한 프록시로 채널 개체의 인스턴스를 사용합니다.

1. 채널을 닫고, 팩터리를 닫습니다.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>이 자습서의 예제 코드

완성된 코드는 다음과 비슷합니다. 이 코드는 클라이언트 애플리케이션을 만드는 방법, 서비스 작업 호출 방법 및 작업 호출 완료 후 클라이언트를 닫는 방법을 보여줍니다.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>애플리케이션 실행

1. Ctrl+Shift+B를 선택하여 솔루션을 빌드합니다. 이 작업은 이전 단계에서 만든 클라이언트 프로젝트와 서비스 프로젝트를 빌드합니다.
1. 클라이언트 애플리케이션을 실행하기 전에 서비스 애플리케이션이 실행 중인지 확인해야 합니다. **솔루션 탐색기**에서 **EchoService** 솔루션을 마우스 오른쪽 단추로 클릭한 다음, **속성**을 선택합니다.
1. **속성 페이지**의 **공용 속성** > **시작 프로젝트**에서 **여러 개의 시작 프로젝트**를 선택합니다. **EchoService**가 목록 맨 처음에 나오는지 확인합니다.
1. **EchoService** 및 **EchoClient** 프로젝트의 **작업** 상자를 모두 **시작**으로 설정합니다.

    ![프로젝트 속성 페이지][5]

1. **프로젝트 종속성**을 선택합니다. **프로젝트**에서 **EchoClient**를 선택합니다. **다음에 종속**에서 **EchoService**가 선택되어 있는지 확인합니다.

    ![프로젝트 종속성][6]

1. **확인**을 선택하여 **속성 페이지**를 닫습니다.
1. F5를 선택하여 두 프로젝트를 실행합니다.
1. 두 콘솔 창을 열고 네임스페이스 이름에 대한 메시지를 표시합니다. 서비스가 먼저 실행되어야 하므로, **EchoService** 콘솔 창에서 네임스페이스를 입력하고 Enter를 선택합니다.
1. 그러면 콘솔에서 SAS 키를 요청합니다. SAS 키를 입력하고 Enter를 선택합니다.

    콘솔 창의 출력 예는 다음과 같습니다. 여기에 나와 있는 값은 예제일 뿐입니다.

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    서비스 애플리케이션은 다음 예제에서처럼 콘솔 창에 수신 중인 주소를 출력합니다.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. **EchoClient** 콘솔 창에서 이전에 서비스 애플리케이션에 입력한 동일한 정보를 입력합니다. 클라이언트 애플리케이션에 동일한 서비스 네임스페이스 및 SAS 키 값을 입력합니다.
1. 이 값을 입력한 후 클라이언트가 서비스에 대한 채널을 열고 다음 콘솔 출력 예제에서 보이는 것처럼 여러 텍스트를 입력하라는 메시지가 표시됩니다.

    `Enter text to echo (or [Enter] to exit):`

    서비스 애플리케이션에 보낼 텍스트를 입력하고 Enter를 선택합니다. 이 텍스트는 에코 서비스 작업을 통해 서비스에 전달되며 다음 예제 출력에서처럼 서비스 콘솔 창에 표시됩니다.

    `Echoing: My sample text`

    클라이언트 애플리케이션은 원본 텍스트인 `Echo` 작업의 반환 값을 받아 콘솔 창에 출력합니다. 다음 텍스트는 클라이언트 콘솔 창의 예제 출력입니다.

    `Server echoed: My sample text`

1. 이런 방식으로 클라이언트에서 서비스로 문자 메시지를 지속적으로 보낼 수 있습니다. 모두 마쳤으면 클라이언트와 서비스 콘솔 창에서 Enter를 선택하여 두 애플리케이션을 모두 종료합니다.

## <a name="next-steps"></a>다음 단계

다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
>[네트워크 외부의 클라이언트에 온-프레미스 WCF REST 서비스 노출](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
