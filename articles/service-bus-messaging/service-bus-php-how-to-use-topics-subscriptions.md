---
title: PHP에서 Azure Service Bus 항목을 사용하는 방법
description: 이 자습서에서는 PHP 애플리케이션에서 Azure Service Bus 항목 및 구독을 사용하는 방법에 대해 알아봅니다.
ms.devlang: PHP
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 706f523fdfb3c710bb16b048cfc68ce98875adb1
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88066205"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-php"></a>빠른 시작: PHP에서 Service Bus 토픽 및 구독을 사용하는 방법

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

이 문서에서는 Service Bus 토픽과 구독을 사용하는 방법을 보여 줍니다. 샘플은 PHP로 작성되었으며 [PHP용 Azure SDK](https://github.com/Azure/azure-sdk-for-php)를 사용합니다. 다루는 시나리오는 다음과 같습니다.

- 토픽 및 구독 만들기 
- 구독 만들기 필터 
- 토픽에 메시지 보내기 
- 구독에서 메시지 받기
- 토픽 및 구독 삭제

## <a name="prerequisites"></a>사전 요구 사항
1. Azure 구독 이 자습서를 완료하려면 Azure 계정이 필요합니다. [Visual Studio 또는 MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)을 활성화해도 되고, 또는 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)에 가입해도 됩니다.
2. [빠른 시작: Azure Portal을 사용하여 Service Bus 토픽 및 해당 토픽에 대한 구독 만들기](service-bus-quickstart-topics-subscriptions-portal.md)의 단계에 따라 Service Bus **네임스페이스**를 만들고 **연결 문자열**을 가져옵니다.

    > [!NOTE]
    > 이 빠른 시작에서는 **PHP**를 사용하여 **토픽** 및 해당 토픽에 대한 **구독**을 만들 것입니다. 

## <a name="create-a-php-application"></a>PHP 애플리케이션 만들기
Azure Blob service에 액세스하는 PHP 애플리케이션을 만드는 데 유일한 요구 사항은 코드 내에서 [PHP용 Azure SDK](https://github.com/Azure/azure-sdk-for-php)의 클래스를 참조하는 것입니다. 어떠한 개발 도구를 사용해도 애플리케이션 또는 메모장을 만들 수 있습니다.

> [!NOTE]
> PHP를 설치하려면 [OpenSSL 확장](https://php.net/openssl)도 설치되어 있고 사용하도록 설정되어 있어야 합니다.
> 
> 

이 항목에서는 PHP 애플리케이션 내에서 로컬로 또는 Azure 웹 역할, 작업자 역할 또는 웹 사이트 내에서 실행되는 코드에서 호출할 수 있는 서비스 기능을 사용합니다.

## <a name="get-the-azure-client-libraries"></a>Azure 클라이언트 라이브러리 가져오기

### <a name="install-via-composer"></a>작성기를 통해 설치
1. 프로젝트 루트에 **composer.json** 이라는 파일을 만들고 다음 코드를 추가합니다.
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. 프로젝트 루트에 **[composer.phar][composer-phar]** 을 다운로드합니다.
3. 명령 프롬프트를 열고 프로젝트 루트에서 다음 명령을 실행합니다.
   
    ```
    php composer.phar install
    ```

## <a name="configure-your-application-to-use-service-bus"></a>Service Bus를 사용하도록 애플리케이션 구성
Service Bus API를 사용하려면 다음을 수행합니다.

1. [require_once][require-once] 문을 사용하여 자동 로더 파일을 참조합니다.
2. 사용할 수 있는 모든 클래스 참조

다음 예제에서는 자동 로더 파일을 포함하고 **ServiceBusService** 클래스를 참조하는 방법을 보여 줍니다.

> [!NOTE]
> 이 예제 및 이 문서의 다른 예제에서는 작성기를 통해 Azure용 PHP 클라이언트 라이브러리를 설치했다고 가정합니다. 라이브러리를 수동으로 또는 PEAR 패키지로 설치한 경우 **WindowsAzure.php** 자동 로더 파일을 참조해야 합니다.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

다음 예제에서 `require_once` 문은 항상 표시되지만 예제를 실행하는 데 필요한 클래스만 참조됩니다.

## <a name="set-up-a-service-bus-connection"></a>Service Bus 연결 설정
Service Bus 클라이언트를 인스턴스화하려면 먼저 다음 형식의 유효한 연결 문자열이 있어야 합니다.

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

여기서 `Endpoint`는 일반적으로 `https://[yourNamespace].servicebus.windows.net` 형식입니다.

Azure 서비스 클라이언트를 만들려면 `ServicesBuilder` 클래스를 사용해야 합니다. 다음을 수행할 수 있습니다.

* 연결 문자열을 직접 전달합니다.
* **CCM(CloudConfigurationManager)** 을 사용하여 여러 외부 소스에서 연결 문자열을 확인할 수 있습니다.
  * 기본적으로 하나의 외부 소스, 환경 변수에 대한 지원이 제공됩니다.
  * `ConnectionStringSource` 클래스를 확장하여 새 소스를 추가할 수 있습니다.

여기에 설명된 예제의 경우 연결 문자열이 직접 전달됩니다.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>토픽 만들기
`ServiceBusRestProxy` 클래스를 통해 Service Bus 토픽에 대한 관리 작업을 수행할 수 있습니다. `ServiceBusRestProxy` 개체는 관리에 필요한 토큰 사용 권한을 캡슐화하는 적절한 연결 문자열을 사용한 `ServicesBuilder::createServiceBusService` 팩터리 메서드를 통해 구성됩니다.

다음 예제에서는 `ServiceBusRestProxy`를 인스턴스화하고 `ServiceBusRestProxy->createTopic`을 호출하여 `MySBNamespace` 네임스페이스 내에 이름이 `mytopic`인 토픽을 만드는 방법을 보여 줍니다.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> `ServiceBusRestProxy` 개체의 `listTopics` 메서드를 사용하여 서비스 네임스페이스 내에 지정된 이름의 토픽이 이미 있는지 확인할 수 있습니다.
> 
> 

## <a name="create-a-subscription"></a>구독 만들기
토픽 구독은 `ServiceBusRestProxy->createSubscription` 메서드로도 만들 수 있습니다. 구독에는 이름이 지정되며, 구독의 가상 큐에 전달되는 메시지 집합을 제한하는 선택적 필터가 있을 수 있습니다.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>기본(MatchAll) 필터를 사용하여 구독 만들기
새 구독을 만들 때 필터를 지정하지 않은 경우 **MatchAll** 필터(기본값)가 사용됩니다. **MatchAll** 필터를 사용하면 토픽에 게시된 모든 메시지가 구독의 가상 큐에 배치됩니다. 다음 예제에서는 `mysubscription`라는 구독을 만들고 기본 **MatchAll** 필터를 사용합니다.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

### <a name="create-subscriptions-with-filters"></a>필터를 사용하여 구독 만들기
토픽에 전송된 메시지 중 특정 토픽 구독 내에 표시되어야 하는 메시지를 지정하는 필터를 설정할 수도 있습니다. 구독에서 지원하는 가장 유연한 유형의 필터는 SQL92 하위 집합을 구현하는 [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)입니다. SQL 필터는 토픽에 게시된 메시지의 속성에 적용됩니다. SqlFilter에 대한 자세한 내용은 [SqlFilter.SqlExpression 속성][sqlfilter]을 참조하세요.

> [!NOTE]
> 구독에 대한 각 규칙은 들어오는 메시지를 독립적으로 처리하여 해당 결과 메시지를 구독에 추가합니다. 또한 각 새 구독에는 토픽의 모든 메시지를 구독에 추가하는 필터가 포함된 기본 **규칙** 개체가 있습니다. 필터와 일치하는 메시지만 받으려면 기본 규칙을 제거해야 합니다. `ServiceBusRestProxy->deleteRule` 메서드를 사용하여 기본 규칙을 제거할 수 있습니다.
> 
> 

다음 예제에서는 사용자 지정 `MessageNumber` 속성이 3보다 큰 메시지만 선택하는 **SqlFilter**를 사용하여 이름이 `HighMessages`인 구독을 만듭니다. 사용자 지정 속성을 메시지에 추가하는 것과 관련한 정보는 [토픽에 메시지 보내기](#send-messages-to-a-topic)를 참조하세요.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

이 코드에서는 추가 네임스페이스(`WindowsAzure\ServiceBus\Models\SubscriptionInfo`)를 사용해야 합니다.

마찬가지로, 다음 예제에서는 `MessageNumber` 속성이 3 이하인 메시지만 선택하는 `SqlFilter`가 있는 이름이 `LowMessages`인 구독을 만듭니다.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

이제 `mytopic` 토픽으로 메시지를 보내는 경우 `mysubscription` 구독을 구독하는 수신자에게는 항상 배달되며, `HighMessages` 및 `LowMessages` 구독을 구독하는 수신자에게는 메시지 내용에 따라 선택적으로 배달됩니다.

## <a name="send-messages-to-a-topic"></a>토픽에 메시지 보내기
Service Bus 토픽에 메시지를 보내기 위해 애플리케이션은 `ServiceBusRestProxy->sendTopicMessage` 메서드를 호출합니다. 다음 코드는 위에서 `MySBNamespace` 서비스 네임스페이스 내에서 이전에 만든 `mytopic` 토픽에 메시지를 보내는 방법을 보여 줍니다.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Service Bus 토픽으로 전송된 메시지는 [BrokeredMessage][BrokeredMessage] 클래스의 인스턴스입니다. [BrokeredMessage][BrokeredMessage] 개체는 표준 속성 및 메서드 세트뿐 아니라 애플리케이션별 사용자 지정 속성을 저장하는 데 사용할 수 있는 속성을 갖고 있습니다. 다음 예제에서는 이전에 만든 `mytopic` 토픽에 테스트 메시지 5개를 보내는 방법을 보여 줍니다. `setProperty` 메서드는 각 메시지에 사용자 지정 속성(`MessageNumber`)을 추가하는 데 사용됩니다. `MessageNumber` 속성 값은 각 메시지에 따라 달라집니다. 이 값을 사용하여 [구독 만들기](#create-a-subscription) 섹션에 나와 있는 것처럼 메시지를 수신하는 구독을 결정할 수 있습니다.

```php
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);

    // Set custom property.
    $message->setProperty("MessageNumber", $i);

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Service Bus 토픽은 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 표준 및 사용자 지정 애플리케이션 속성이 포함된 헤더의 최대 크기는 64KB입니다. 한 토픽에 저장되는 메시지 수에는 제한이 없지만 한 토픽에 저장되는 총 메시지 크기는 제한됩니다. 토픽 크기의 상한은 5GB입니다. 할당량에 대한 자세한 내용은 [Service Bus 할당량][Service Bus quotas]을 참조하세요.

## <a name="receive-messages-from-a-subscription"></a>구독에서 메시지 받기
구독에서 메시지를 받는 가장 좋은 방법은 `ServiceBusRestProxy->receiveSubscriptionMessage` 메서드를 사용하는 것입니다. 메시지는 [*ReceiveAndDelete* 및 *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode)의 두 가지 모드로 받을 수 있습니다. **PeekLock**이 기본값입니다.

[ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) 모드를 사용하는 경우 수신은 1단계 작업입니다. 즉, Service Bus가 구독에서 메시지에 대한 읽기 요청을 받으면 메시지를 사용하는 것으로 표시하고 애플리케이션에 반환합니다. [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) * 모드는 가장 단순한 모델이며, 오류 발생 시 애플리케이션이 메시지를 처리하지 않아도 되는 시나리오에서 효과적입니다. 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요. Service Bus가 메시지를 사용되는 것으로 표시했기 때문에 애플리케이션이 다시 시작되고 메시지를 다시 사용하기 시작할 때 충돌 전에 사용한 메시지는 누락됩니다.

기본값인 [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) 모드에서는 메시지 수신이 2단계 작업이므로 메시지 누락이 허용되지 않는 애플리케이션을 지원할 수 있습니다. Service Bus는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 애플리케이션에 반환합니다. 애플리케이션은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후에 받은 메시지를 `ServiceBusRestProxy->deleteMessage`에 전달하여 수신 프로세스의 두 번째 단계를 완료합니다. Service Bus는 `deleteMessage` 호출을 확인한 후 메시지를 이용되는 것으로 표시하고 큐에서 제거합니다.

다음 예제에서는 [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) 모드(기본 모드)를 사용하여 메시지를 받고 처리하는 방법을 보여 줍니다. 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>애플리케이션 작동이 중단되어 메시지를 읽을 수 없는 문제를 처리하는 방법
Service Bus는 애플리케이션 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신자 애플리케이션이 메시지를 처리할 수 없는 경우 받은 메시지에 대해 `deleteMessage` 메서드 대신 `unlockMessage` 메서드를 호출할 수 있습니다. 그러면 Service Bus에서 큐 메시지의 잠금을 해제하므로 동일한 소비 애플리케이션이나 다른 소비 애플리케이션에서 메시지를 다시 받을 수 있습니다.

큐 내에서 잠긴 메시지와 연결된 시간 제한도 있으며, 애플리케이션에서 잠금 시간 제한이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 애플리케이션이 크래시되는 경우) Service Bus가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

애플리케이션이 메시지를 처리한 후 `deleteMessage` 요청이 실행되기 전에 크래시되는 경우 다시 시작될 때 메시지가 애플리케이션에 다시 배달됩니다. 이러한 유형의 처리를 *최소 한 번 이상 처리*라고 합니다. 즉, 각 메시지가 최소 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 애플리케이션 개발자가 중복 메시지 배달을 처리하는 논리를 애플리케이션에 추가해야 합니다. 이 작업은 배달을 여러 번 시도해도 일정하게 유지되는 메시지의 `getMessageId` 메서드를 사용하여 수행하는 경우가 많습니다.

## <a name="delete-topics-and-subscriptions"></a>토픽 및 구독 삭제
토픽이나 구독을 삭제하려면 각각 `ServiceBusRestProxy->deleteTopic` 또는 `ServiceBusRestProxy->deleteSubscripton` 메서드를 사용합니다. 토픽을 삭제하면 토픽에 등록된 모든 구독도 삭제됩니다.

다음 예제에서는 이름이 `mytopic`인 토픽 및 등록된 해당 구독을 삭제하는 방법을 보여 줍니다.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

`deleteSubscription` 메서드를 사용하여 구독을 독립적으로 삭제할 수 있습니다.

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

> [!NOTE]
> [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)로 Service Bus 리소스를 관리할 수 있습니다. Service Bus Explorer를 사용하면 Service Bus 네임스페이스에 연결하고 쉬운 방식으로 메시징 엔터티를 관리할 수 있습니다. 이 도구는 가져오기/내보내기 기능 또는 항목, 큐, 구독, 릴레이 서비스, Notification Hubs 및 이벤트 허브를 테스트하는 기능과 같은 고급 기능을 제공합니다. 

## <a name="next-steps"></a>다음 단계
자세한 내용은 [큐, 토픽 및 구독][Queues, topics, and subscriptions]을 참조하세요.

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md