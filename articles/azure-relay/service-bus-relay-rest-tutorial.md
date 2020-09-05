---
title: '자습서: Azure Relay를 사용하는 REST 자습서'
description: '자습서: REST 기반 인터페이스를 표시하는 Azure Relay 호스트 애플리케이션을 빌드합니다.'
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 0620f55650d0e4da0cd7a616649df952f3017455
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88922330"
---
# <a name="tutorial-azure-wcf-relay-rest-tutorial"></a>자습서: Azure WCF 릴레이 REST 자습서

이 자습서에서는 REST 기반 인터페이스를 표시하는 Azure Relay 호스트 애플리케이션을 빌드하는 방법을 설명합니다. REST는 웹 브라우저와 같은 웹 클라이언트가 HTTP 요청을 통해 Service Bus API에 액세스할 수 있도록 합니다.

자습서에서는 WCF(Windows Communication Foundation) REST 프로그래밍 모델을 사용하여 Azure Relay에 REST 서비스를 구축합니다. 자세한 내용은 [WCF REST 프로그래밍 모델](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) 및 [서비스 디자인 및 구현](/dotnet/framework/wcf/designing-and-implementing-services)을 참조하세요.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
>
> * 이 자습서를 수행하기 위한 필수 조건 설치
> * Relay 네임스페이스 만들기
> * REST 기반 WCF 서비스 계약 정의
> * REST 기반 WCF 계약 구현
> * REST 기반 WCF 서비스 호스트 및 실행
> * 서비스 실행 및 테스트

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* Azure 구독 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
* [Visual Studio 2015 이상](https://www.visualstudio.com) - 이 자습서의 예제에서는 Visual Studio 2019를 사용합니다.
* Azure SDK for .NET. [SDK 다운로드 페이지](https://azure.microsoft.com/downloads/)에서 설치합니다.

## <a name="create-a-relay-namespace"></a>Relay 네임스페이스 만들기

Azure에서 릴레이 기능 사용을 시작하려면 먼저 서비스 네임스페이스를 만들어야 합니다. 네임스페이스는 애플리케이션 내에서 Azure 리소스의 주소를 지정하기 위한 범위 컨테이너를 제공합니다. [여기의 지침](relay-create-namespace-portal.md)을 따라 릴레이 네임스페이스를 만듭니다.

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Azure Relay와 사용할 REST 기반 WCF 서비스 계약 정의

WCF REST 스타일 서비스를 만들 때 계약을 정의해야 합니다. 계약은 호스트가 지원하는 작업을 지정합니다. 서비스 작업은 웹 서비스 메서드와 비슷합니다. C++, C# 또는 Visual Basic 인터페이스를 사용하여 계약을 정의합니다. 인터페이스의 각 메서드는 특정 서비스 작업에 해당합니다. 각 인터페이스에 [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) 특성을 적용하고, 각 작업에 [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) 특성을 적용합니다. 

> [!TIP]
> [ServiceContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) 특성이 있는 인터페이스의 메서드에 [OperationContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) 특성이 없으면 해당 메서드가 표시되지 않습니다. 이러한 작업에 사용되는 코드는 절차를 수행하면서 예제에 표시됩니다.

WCF 계약과 REST 스타일 계약의 주요 차이는 [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute)에 대한 속성의 추가합니다. 이 속성을 사용하면 인터페이스의 메서드를 인터페이스 반대편의 메서드로 매핑할 수 있습니다. 이 예제에서는 [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) 특성을 사용하여 메서드를 `HTTP GET`에 연결합니다. 이 접근 방식을 사용하면 Service Bus가 인터페이스로 전송된 명령을 정확하게 검색하고 해석할 수 있습니다.

### <a name="to-create-a-contract-with-an-interface"></a>인터페이스와 함께 계약을 만들려면

1. 관리자 권한으로 Microsoft Visual Studio를 시작합니다. 이렇게 하려면 Visual Studio 프로그램 아이콘을 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 선택합니다.
1. Visual Studio에서 **새 프로젝트 만들기**를 선택합니다.
1. **새 프로젝트 만들기**에서 C#용 **콘솔 앱(.NET Framework)** 을 선택하고, **다음**을 선택합니다.
1. 프로젝트 이름을 *ImageListener*로 지정합니다. 기본 **위치**를 사용하고, **만들기**를 선택합니다.

   C# 프로젝트의 경우 Visual Studio에서 *Program.cs* 파일을 만듭니다. 이 클래스는 콘솔 애플리케이션 프로젝트를 제대로 구축하는데 필요한 빈 `Main()` 메서드를 포함합니다.

1. **솔루션 탐색기**에서 **ImageListener** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 선택합니다.
1. **찾아보기**를 선택한 다음, **WindowsAzure.ServiceBus**를 검색하여 선택합니다. **설치**를 선택하고 사용 약관에 동의합니다.

    이 단계에서는 Service Bus 및 *System.ServiceModel.dll*에 대한 참조를 추가합니다. 이 패키지는 Service Bus 라이브러리 및 WCF `System.ServiceModel`에 대한 참조를 자동으로 추가합니다.

1. `System.ServiceModel.Web.dll`에 대한 참조를 프로젝트에 명시적으로 추가합니다. **솔루션 탐색기**에서 프로젝트 폴더 아래의 **참조** 폴더를 마우스 오른쪽 단추로 클릭하고, **참조 추가**를 선택합니다.
1. **참조 추가**에서 **Framework**를 선택하고, **검색**에 *System.ServiceModel.Web*을 입력합니다. **System.ServiceModel.Web** 확인란을 선택한 다음 **확인**을 클릭합니다.

그리고 프로젝트의 코드를 다음과 같이 변경합니다.

1. *Program.cs* 파일 맨 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * [System.ServiceModel](/dotnet/api/system.servicemodel)은 WCF의 기본 기능에 프로그래밍 방식의 액세스를 가능하게 하는 네임스페이스입니다. WCF 릴레이는 WCF의 많은 개체와 특성을 사용하여 서비스 계약을 정의합니다. 이 네임스페이스는 대부분의 릴레이 애플리케이션에서 사용됩니다.
    * [System.ServiceModel.Channels](/dotnet/api/system.servicemodel.channels)는 채널을 정의하는 데 도움이 되며, 이 개체를 통해 Azure Relay 및 클라이언트 웹 브라우저와 통신하게 됩니다.
    * [System.ServiceModel.Web](/dotnet/api/system.servicemodel.web)에는 웹 기반 애플리케이션을 만들 수 있는 형식이 포함되어 있습니다.

1. `ImageListener` 네임스페이스 이름을 `Microsoft.ServiceBus.Samples`로 변경합니다.

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. 네임스페이스 선언의 중괄호를 연 바로 다음에 `IImageContract`라는 새 인터페이스를 정의하고 이 인터페이스에 `ServiceContractAttribute` 특성의 값을 `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1`로 적용합니다. 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    네임스페이스 값은 코드 전반에 사용하는 네임스페이스에 따라 다릅니다. 네임스페이스 값은 이 계약의 고유 식별자이며 버전 정보가 있어야 합니다. 자세한 내용은 [서비스 버전 관리](/dotnet/framework/wcf/service-versioning)를 참조하세요. 네임스페이스를 명시적으로 지정하면 기본 네임스페이스 값이 계약 이름에 추가되는 경우를 방지합니다.

1. `IImageContract` 인터페이스 내에서 `IImageContract` 계약이 인터페이스에 노출하는 단일 작업에 대한 메서드를 선언하고, 공개 Service Bus 계약의 일부로 노출하려는 메서드에 `OperationContract` 특성을 적용합니다.

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. `OperationContract` 특성에서 `WebGet` 값을 추가합니다.

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   `WebGet` 값을 추가하면 릴레이 서비스가 HTTP GET 요청을 `GetImage`로 라우팅하고 `GetImage`의 반환 값을 `HTTP GETRESPONSE` 회신으로 해석할 수 있습니다. 이 자습서의 뒷부분에서는 웹 브라우저를 사용하여 이 메서드에 액세스하고, 브라우저에 이미지를 표시할 것입니다.

1. `IImageContract` 정의 바로 다음에 `IImageContract` 및 `IClientChannel` 인터페이스로부터 상속되는 채널을 선언합니다.

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   채널은 서비스 및 클라이언트가 서로 정보를 전달하는 WCF 개체입니다. 나중에 호스트 애플리케이션에서 채널을 만듭니다. 그러면 Azure Relay는 이 채널을 사용하여 브라우저의 HTTP GET 요청을 `GetImage` 구현으로 전달합니다. 릴레이는 이 채널을 사용하여 `GetImage` 반환 값을 가져와서 클라이언트 브라우저에 대한 `HTTP GETRESPONSE`로 해석하기도 합니다.

1. **빌드** > **솔루션 빌드**를 선택하여 지금까지 수행한 작업이 정확한지 확인합니다.

### <a name="example-that-defines-a-wcf-relay-contract"></a>WCF Relay 계약을 정의하는 예제

다음 코드는 WCF 릴레이 계약을 정의하는 기본 인터페이스를 보여 줍니다.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>REST 기반 WCF 서비스 계약 구현

REST 스타일 WCF Relay 서비스를 만들려면 먼저 인터페이스를 사용하여 계약을 만듭니다. 다음 단계는 인터페이스를 구현합니다. 이 절차에는 사용자 정의 `IImageContract` 인터페이스를 구현하는 이름이 `ImageService`인 클래스를 만드는 단계가 포함됩니다. 계약을 구현한 후에는 *App.config* 파일을 사용하여 인터페이스를 구현합니다. 구성 파일에는 애플리케이션에 필요한 정보가 포함되어 있습니다. 이 정보에는 서비스 이름, 계약 이름, 릴레이 서비스와 통신하는 데 사용되는 프로토콜 유형이 있습니다. 이러한 작업에 사용되는 코드는 절차를 수행하면서 예제에 표시됩니다.

이전 단계와 마찬가지로, REST 스타일 계약과 WCF Relay 계약의 구현 간에는 거의 차이가 없습니다.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>REST 스타일 Service Bus 계약을 구현하려면

1. `IImageContract` 인터페이스 정의 바로 뒤에 이름이 `ImageService`인 클래스를 새로 만듭니다. `ImageService` 클래스가 `IImageContract` 인터페이스를 구현합니다.

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    다른 인터페이스 구현과 유사하게, 다른 파일에 정의를 구현할 수 있습니다. 하지만 이 자습서의 경우 구현은 인터페이스 정의 및 `Main()` 메서드와 동일한 파일에서 나타납니다.

1. [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) 특성을 `IImageService` 클래스에 적용하여 클래스가 WCF 계약의 구현이라는 것을 나타냅니다.

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    앞서 설명했듯이, 이 네임스페이스는 기존의 네임스페이스가 아니라 계약을 식별하는 WCF 아키텍처의 일부입니다. 자세한 내용은 [데이터 계약 이름](/dotnet/framework/wcf/feature-details/data-contract-names/)을 참조하세요.

1. *.jpg* 이미지를 프로젝트에 추가합니다. 이 파일은 서비스가 수신 브라우저에 표시하는 그림입니다.

   1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**를 선택합니다.
   1. 그리고 **기존 항목**을 선택합니다.
   1. **기존 항목 추가**를 사용하여 적절한 .jpg를 찾은 다음, **추가**를 선택합니다. 파일을 추가할 때 **파일 이름** 옆에 있는 드롭다운 목록에서 **모든 파일**을 선택합니다.

   이 자습서의 나머지 부분에서는 이미지의 이름을 *image.jpg*로 가정합니다. 다른 파일이 있으면 이미지 이름을 변경하거나 보완하도록 코드를 변경해야 합니다.

1. 실행 중인 서비스가 이미지 파일을 반드시 찾게 하려면 **솔루션 탐색기**에서 이미지 파일을 마우스 오른쪽 단추로 클릭한 다음, **속성**을 선택합니다. **속성**에서 **출력 디렉터리로 복사**를 **변경된 내용만 복사**로 설정합니다.

1. [인터페이스와 함께 계약을 만들려면](#to-create-a-contract-with-an-interface)의 절차를 사용하여 *System.Drawing.dll* 어셈블리에 대한 참조를 프로젝트에 추가합니다.

1. 관련된 다음 `using` 문을 추가합니다.

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. `ImageService` 클래스에서 비트맵을 로드하는 다음 생성자를 추가하고, 이 생성자를 클라이언트 브라우저로 보낼 준비를 합니다.

    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

1. 이전 코드 바로 뒤에서, 이미지를 포함하는 HTTP 메시지를 반환하는 다음 `GetImage` 메서드를 `ImageService` 클래스에 추가합니다.

    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```

    이 구현은 `MemoryStream`을 사용하여 이미지를 가져온 후 브라우저로 스트리밍할 준비를 합니다. 또한 0에서 스트림 위치를 시작하고, 스트림 콘텐츠를 *.jpg*로 선언하고, 정보를 스트리밍합니다.

1. **빌드** > **솔루션 빌드**를 선택합니다.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Service Bus에서 웹 서비스를 실행하기 위한 구성을 정의하려면

1. **솔루션 탐색기**에서 **App.config** 파일을 두 번 클릭하여 Visual Studio 편집기에서 엽니다.

    *App.config* 파일에는 서비스 이름, 엔드포인트 및 바인딩이 들어 있습니다. 엔드포인트는 클라이언트와 호스트가 서로 통신할 수 있도록 Azure Relay가 노출하는 위치입니다. 바인딩은 통신에 사용되는 프로토콜 유형입니다. 여기서 주요 차이점은 구성된 서비스 엔드포인트가 [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) 바인딩을 참조한다는 것입니다.

1. `<system.serviceModel>` XML 요소는 하나 이상의 서비스를 정의하는 WCF 요소입니다. 여기서는 서비스 이름 및 엔드포인트를 정의하는데 사용됩니다. `<system.serviceModel>` 요소의 맨 아래에(하지만 여전히 `<system.serviceModel>` 내에서) 다음 콘텐츠를 포함하는 `<bindings>` 요소를 추가합니다.

    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    이 콘텐츠는 애플리케이션에서 사용되는 바인딩을 정의합니다. 여러 바인딩을 정의할 수 있지만, 이 자습서에서는 하나만 정의합니다.

    이전 코드는 `relayClientAuthenticationType`이 `None`으로 설정된 WCF Relay [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) 바인딩을 정의합니다. 이 설정은 이 바인딩을 사용하는 엔드포인트에 클라이언트 자격 증명이 필요 없다는 것을 나타냅니다.

1. `<bindings>` 요소 다음에 `<services>` 요소를 추가합니다. 바인딩과 유사하게 단일 구성 파일 내에 여러 서비스를 정의할 수 있습니다. 하지만 이 자습서에서는 하나만 정의합니다.

    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    이 콘텐츠는 이전에 정의된 기본 `webHttpRelayBinding`을 사용하는 서비스를 구성합니다. 기본 `sbTokenProvider`도 사용하며, 이것은 다음 단계에서 정의됩니다.

1. `<services>` 요소 후에는 다음 콘텐츠를 사용하여 `<behaviors>` 요소를 만들고, `SAS_KEY`를 SAS(공유 액세스 서명) 키로 바꿉니다. [Azure Portal][Azure portal]에서 SAS 키를 얻으려면 [관리 자격 증명 가져오기](service-bus-relay-tutorial.md#get-management-credentials)를 참조하세요.

    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

1. 계속 *App.config*의 `<appSettings>` 요소에서 연결 문자열 값 전체를 이전에 포털에서 얻은 연결 문자열로 바꿉니다.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. **빌드** > **솔루션 빌드**를 선택하여 전체 솔루션을 빌드합니다.

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>REST 기반 WCF 서비스 계약을 구현하는 예제

다음 코드는 `WebHttpRelayBinding` 바인딩을 사용하여 Service Bus에서 실행되는 REST 기반 서비스에 대한 계약 및 서비스 구현을 보여줍니다.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

다음 예제에서는 서비스와 관련된 *App.config* 파일을 보여줍니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>Azure Relay를 사용하기 위해 REST 기반 WCF 서비스 호스팅

이 섹션에서는 WCF Relay와 함께 콘솔 애플리케이션을 사용하여 웹 서비스를 실행하는 방법을 설명합니다. 이 섹션에서 작성하는 전체 코드 목록은 절차를 수행하면서 예제에 제공됩니다.

### <a name="to-create-a-base-address-for-the-service"></a>서비스에 대한 기본 주소를 만들려면

1. `Main()` 함수 선언에서 프로젝트의 네임스페이스를 저장할 변수를 만듭니다. `yourNamespace`를 이전에 만든 릴레이 네임스페이스의 이름으로 바꿔야 합니다.

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    Service Bus는 네임스페이스 이름을 사용하여 고유 URI를 만듭니다.

1. 네임스페이스를 기반으로 하는 서비스 기본 주소에 대한 `Uri` 인스턴스를 만듭니다.

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>웹 서비스 호스트를 만들고 구성하려면

계속 `Main()`에서, 이 섹션의 앞부분에서 만든 URI 주소를 사용하여 웹 서비스 호스트를 만듭니다.
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

서비스 호스트는 호스트 애플리케이션을 인스턴스화하는 WCF 개체입니다. 이 예제는 만들려는 호스트 형식(`ImageService`)과 호스트 애플리케이션을 노출하려는 주소를 전달합니다.

### <a name="to-run-the-web-service-host"></a>웹 서비스 호스트를 실행하려면

1. 아직 `Main()`에 있는 경우 다음 줄을 추가하여 서비스를 엽니다.

    ```csharp
    host.Open();
    ```

    이제 서비스가 실행 중입니다.

1. 서비스가 실행 중임을 나타내고 서비스를 중지하는 방법을 알리는 메시지를 표시합니다.

    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. 완료되면 서비스 호스트를 닫습니다.

    ```csharp
    host.Close();
    ```

### <a name="example-of-the-service-contract-and-implementation"></a>서비스 계약 및 구현 예제

다음 예제는 자습서에 포함된 이전 단계의 구현 및 서비스 계약을 포함하고 콘솔 애플리케이션에 서비스를 호스트합니다. 다음 코드를 이름이 *ImageListener.exe*인 실행 파일로 컴파일합니다.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>서비스 실행 및 테스트

솔루션을 빌드한 후 다음을 수행하여 애플리케이션을 실행합니다.

1. F5 키를 선택하거나 실행 파일 위치 *ImageListener\bin\Debug\ImageListener.exe*로 이동하여 서비스를 실행합니다. 다음 단계를 수행하는 데 필요하므로 앱을 실행 상태로 둡니다.
1. 이미지를 보려면 명령 프롬프트에서 주소를 복사하여 브라우저로 붙여 넣습니다.
1. 완료되면 명령 프롬프트 창에서 Enter를 선택하여 앱을 닫습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure Relay 서비스를 사용하는 애플리케이션을 빌드했습니다. 자세한 정보는 다음 문서를 참조하세요.

* [Azure 릴레이란?](relay-what-is-it.md)
* [Azure WCF Relay를 사용하여 외부 클라이언트에 온-프레미스 WCF REST 서비스 노출](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
