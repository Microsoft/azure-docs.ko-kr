---
title: Python에서 Queue Storage를 사용하는 방법 | Microsoft Docs
description: Azure Queue Storage를 사용하여 큐를 작성 및 삭제하고 메시지를 삽입하고 가져오고 삭제하는 방법을 알아봅니다. 샘플은 PHP로 작성되었습니다.
services: storage
author: roygara
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 01/11/2018
ms.author: rogarana
ms.subservice: queues
ms.openlocfilehash: 0f9cc11e64e2517ef7aff0cd51705c39bf212b37
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62109872"
---
# <a name="how-to-use-queue-storage-from-php"></a>PHP에서 Queue Storage를 사용하는 방법
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>개요
이 가이드에서는 Azure Queue Storage 서비스를 사용하여 일반 시나리오를 수행하는 방법을 설명합니다. 샘플은 [PHP용 Azure Storage Client Library][download]의 클래스를 통해 작성되었습니다. 여기서 다루는 시나리오에는 큐 메시지 삽입, 보기, 가져오기 및 삭제와 큐 만들기 및 삭제가 포함됩니다.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>PHP 애플리케이션 만들기
Azure Queue Storage에 액세스하는 PHP 애플리케이션을 만들기 위한 유일한 요구 사항은 코드 내에서 [PHP용 Azure Storage Client Library][download]의 클래스를 참조하는 것입니다. 애플리케이션을 만드는 데는 메모장을 포함한 어떠한 개발 도구도 사용할 수 있습니다.

이 가이드에서는 PHP 응용 프로그램 내에서 로컬로 또는 Azure 웹 역할, 작업자 역할 또는 웹 사이트 내에서 실행되는 코드에서 호출할 수 있는 Queue Storage 서비스 기능을 사용합니다.

## <a name="get-the-azure-client-libraries"></a>Azure 클라이언트 라이브러리 가져오기
### <a name="install-via-composer"></a>작성기를 통해 설치
1. 프로젝트 루트에 **composer.json** 이라는 파일을 만들고 다음 코드를 추가합니다.
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```
2. 프로젝트 루트에 **[composer.phar][composer-phar]** 을 다운로드합니다.
3. 명령 프롬프트를 열고 프로젝트 루트에서 다음 명령을 실행합니다.
   
    ```
    php composer.phar install
    ```

또는 GitHub에서 [Azure Storage PHP 클라이언트 라이브러리][download]로 이동하여 소스 코드를 복제합니다.

## <a name="configure-your-application-to-access-queue-storage"></a>Queue Storage에 액세스하도록 응용 프로그램 구성
Azure Queue Storage에 대한 API를 사용하려면 다음을 수행해야 합니다.

1. [require_once] 문을 사용하여 자동 로더 파일을 참조합니다.
2. 사용할 수 있는 모든 클래스를 참조합니다.

다음 예제에서는 자동 로더 파일을 포함하고 **QueueRestProxy** 클래스를 참조하는 방법을 보여 줍니다.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

다음 예제에서 `require_once` 문은 항상 표시되지만 예제를 실행하는 데 필요한 클래스만 참조됩니다.

## <a name="set-up-an-azure-storage-connection"></a>Azure 저장소 연결 설정
Azure Queue Storage 클라이언트를 인스턴스화하려면 먼저 유효한 연결 문자열이 있어야 합니다. 큐 저장소 연결 문자열 형식은 다음과 같습니다.

Live 서비스에 액세스하는 경우:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

에뮬레이터 저장소에 액세스하는 경우:

```php
UseDevelopmentStorage=true
```

Azure Queue 서비스 클라이언트를 만들려면 **QueueRestProxy** 클래스를 사용해야 합니다. 다음 기술 중 하나를 사용할 수 있습니다.

* 연결 문자열을 직접 전달합니다.
* 웹앱의 환경 변수를 사용하여 연결 문자열을 저장합니다. 연결 문자열 구성에 관한 [Azure 웹앱 구성 설정](../../app-service/web-sites-configure.md) 문서를 참조하세요.
여기에 설명된 예제의 경우 연결 문자열이 직접 전달됩니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>큐 만들기
**QueueRestProxy** 개체를 통해 **createQueue** 메서드를 사용하여 큐를 만들 수 있습니다. 큐를 만드는 경우 큐에 대한 옵션을 설정할 수 있으나 반드시 옵션을 설정해야 하는 것은 아닙니다. 아래 예제에서는 큐에 대해 메타데이터를 설정하는 방법을 보여 줍니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> 메타데이터 키에서 대/소문자를 구분하려고 하면 안 됩니다. 모든 키는 서비스에서 소문자로 읽혀집니다.
> 
> 

## <a name="add-a-message-to-a-queue"></a>큐에 메시지 추가
큐에 메시지를 추가하려면 **QueueRestProxy->createMessage**를 사용합니다. 이 메서드는 큐 이름, 메시지 텍스트 및 메시지 옵션(선택적)을 인수로 받아들입니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello World!");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>다음 메시지 보기
큐에서 메시지를 제거하지 않고도 **QueueRestProxy->peekMessages**를 호출하여 큐의 맨 앞에서 단일 메시지 또는 여러 메시지를 볼 수 있습니다. 기본적으로, **peekMessage** 메서드는 단일 메시지를 반환하지만 **PeekMessagesOptions->setNumberOfMessages** 메서드를 사용하면 이 값을 변경할 수 있습니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>큐에서 다음 메시지 제거
다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. 먼저 **QueueRestProxy->listMessages**를 호출하여 큐에서 읽어들이는 메시지가 다른 코드에 표시되지 않도록 합니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. (이 기간 동안 메시지를 삭제하지 않으면 큐에서 해당 메시지가 다시 표시됩니다.) 큐에서 메시지 제거를 완료하려면 **QueueRestProxy->deleteMessage**를 호출해야 합니다. 메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드는 메시지가 처리된 직후에 **deleteMessage** 를 호출합니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>대기 중인 메시지의 콘텐츠 변경
**QueueRestProxy->updateMessage**를 호출하여 큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 메시지가 작업을 나타내는 경우 이 기능을 사용하여 작업의 상태를 업데이트할 수 있습니다. 다음 코드는 큐 메시지를 새로운 콘텐츠로 업데이트하고 표시 제한 시간이 60초 더 늘어나도록 설정합니다. 그러면 메시지와 연결된 작업의 상태가 저장되고 클라이언트에서 메시지에 대한 작업을 계속할 수 있는 시간이 1분 더 허용됩니다. 이 기술을 사용하여 처리 단계가 하드웨어 또는 소프트웨어 오류로 인해 실패하는 경우 처음부터 시작하지 않고도 큐 메시지에 대한 여러 단계의 워크플로를 추적할 수 있습니다. 일반적으로, 다시 시도 수도 유지하므로, 메시지가 *n* 번 넘게 다시 시도된 경우 메시지를 지울 수도 있습니다. 이 기능은 처리될 때마다 애플리케이션 오류를 트리거하는 메시지를 차단하여 보호해 줍니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueClient->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-de-queuing-messages"></a>큐에서 메시지를 제거하기 위한 추가적인 옵션
큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다. 먼저, 메시지의 배치(최대 32개)를 가져올 수 있습니다. 두 번째로, 표시 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다. 다음 코드 예제는 **getMessages** 메서드를 사용하여 한 번 호출에 16개의 메시지를 가져옵니다. 그런 다음에 **for** 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분으로 설정합니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>큐 길이 가져오기
큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. **QueueRestProxy->getQueueMetadata** 메서드는 큐에 대한 메타데이터를 반환하도록 큐 서비스에 요청합니다. 반환된 개체에 대해 **getApproximateMessageCount** 메서드를 호출하면 큐에 얼마나 많은 메시지가 있는지 나타내는 개수를 얻게 됩니다. 큐 서비스가 요청에 응답한 후 메시지가 추가되거나 제거될 수 있으므로 이 메시지 수는 근사치일 뿐입니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>큐 삭제
큐 및 해당 큐의 모든 메시지를 삭제하려면 **QueueRestProxy->deleteQueue** 메서드를 호출합니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>다음 단계
이제 Azure Queue Storage의 기본 사항을 배웠으므로 다음 링크를 따라 좀더 복잡한 스토리지 작업에 대해 알아보세요.

* [Azure Storage PHP Client Library에 대한 API 참조](https://azure.github.io/azure-storage-php/)를 방문해 보세요.
* [고급 큐 예제](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php)를 참조하세요.

자세한 내용은 [PHP 개발자 센터](https://azure.microsoft.com/develop/php/)를 참조하세요.

[download]: https://github.com/Azure/azure-storage-php
[require_once]: http://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: https://getcomposer.org/composer.phar

