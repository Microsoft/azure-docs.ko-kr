---
title: Node.js에서 Azure Service Bus 토픽 및 구독을 사용하는 방법 | Microsoft Docs
description: Node.js app에서 Azure의 Service Bus 토픽 및 구독을 사용하는 방법에 대해 알아봅니다.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: d3f71382a3f2b15ec0f9764b9913a95c0d32b21d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60591818"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs"></a>Node.js에서 Service Bus 토픽 및 구독을 사용하는 방법

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

이 가이드에서는 Node.js 애플리케이션에서 Service Bus 토픽과 구독을 사용하는 방법을 설명합니다. 다루는 시나리오는 다음과 같습니다.

- 토픽 및 구독 만들기 
- 구독 만들기 필터 
- 토픽에 메시지 보내기 
- 구독에서 메시지 받기
- 토픽 및 구독 삭제 

토픽 및 구독에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하세요.

## <a name="prerequisites"></a>필수 조건
1. Azure 구독. 이 자습서를 완료하려면 Azure 계정이 필요합니다. 활성화할 수 있습니다 하 [Visual Studio 또는 MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) 에 등록 또는 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)합니다.
2. 단계를 수행 합니다 [빠른 시작: Service Bus 토픽 및 구독 항목을 만들려면 Azure portal을 사용 하 여](service-bus-quickstart-topics-subscriptions-portal.md) Service Bus를 만들려면 **네임 스페이스** 받고 합니다 **연결 문자열**합니다.

    > [!NOTE]
    > 만들려는 **항목** 및 **구독** 사용 하 여 토픽에 **Node.js** 이 빠른 시작 합니다. 

## <a name="create-a-nodejs-application"></a>Node.js 애플리케이션 만들기
빈 Node.js 애플리케이션을 만듭니다. Node.js 애플리케이션을 만드는 방법에 대한 지침은 [Node.js 애플리케이션을 만들어 Azure 웹 사이트에 배포], Windows PowerShell을 사용하는 [Node.js 클라우드 서비스][Node.js Cloud Service] 또는 WebMatrix를 사용하는 웹 사이트를 참조하세요.

## <a name="configure-your-application-to-use-service-bus"></a>Service Bus를 사용하도록 애플리케이션 구성
Service Bus를 사용하려면 Node.js Azure 패키지를 다운로드합니다. 이 패키지에는 Service Bus REST 서비스와 통신하는 라이브러리 집합이 포함되어 있습니다.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>NPM(Node Package Manager)을 사용하여 패키지 가져오기
1. **PowerShell**(Windows), **Terminal**(Mac) 또는 **Bash**(Unix)와 같은 명령줄 인터페이스를 엽니다.
2. 샘플 애플리케이션을 만든 폴더로 이동합니다.
3. 명령 창에 **npm install azure**를 입력합니다. 그러면 다음과 같이 출력됩니다.

   ```
       azure@0.7.5 node_modules\azure
   ├── dateformat@1.0.2-1.2.3
   ├── xmlbuilder@0.4.2
   ├── node-uuid@1.2.0
   ├── mime@1.2.9
   ├── underscore@1.4.4
   ├── validator@1.1.1
   ├── tunnel@0.0.2
   ├── wns@0.5.3
   ├── xml2js@0.2.7 (sax@0.5.2)
   └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
   ```
3. **ls** 명령을 수동으로 실행하여 **node\_modules** 폴더가 만들어졌는지 확인할 수 있습니다. 이 폴더에서 Service Bus 토픽에 액세스하는 데 필요한 라이브러리가 들어 있는 **Azure** 패키지를 찾습니다.

### <a name="import-the-module"></a>모듈 가져오기
메모장 또는 다른 텍스트 편집기를 사용하여 다음을 애플리케이션의 **server.js** 파일 맨 위에 추가합니다.

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Service Bus 연결 설정
Azure 모듈은 이전 단계인 "자격 증명 구하기"에서 가져온 연결 문자열에 대해 `AZURE_SERVICEBUS_CONNECTION_STRING` 환경 변수를 읽습니다. 이러한 환경 변수가 설정되어 있지 않은 경우 `createServiceBusService`를 호출할 때 계정 정보를 지정해야 합니다.

Azure 클라우드 서비스의 환경 변수 설정 예제는 [환경 변수 설정](../container-instances/container-instances-environment-variables.md#azure-cli-example)을 참조하세요.



## <a name="create-a-topic"></a>토픽 만들기
**ServiceBusService** 개체를 사용하면 토픽으로 작업할 수 있습니다. 다음 코드는 **ServiceBusService** 개체를 만듭니다. 이 코드를 **server.js** 파일의 위쪽, Azure 모듈을 가져오기 위한 문 뒤에 추가하십시오.

```javascript
var serviceBusService = azure.createServiceBusService();
```

**ServiceBusService** 개체에서 `createTopicIfNotExists`를 호출하면 지정된 토픽이 반환되거나(있는 경우) 지정한 이름의 새 토픽이 생성됩니다. 다음 코드는 `createTopicIfNotExists`를 사용하여 `MyTopic`이라는 토픽을 만들거나 이 토픽에 연결합니다.

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

`createTopicIfNotExists` 메서드는 추가 옵션도 지원합니다. 이러한 옵션을 통해 메시지 TTL(Time to Live)이나 최대 토픽 크기 등 기본 토픽 설정을 재정의할 수 있습니다. 

다음은 최대 토픽 크기를 5GB, TTL(Time to Live)을 1분으로 설정하는 예제입니다.

```javascript
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>필터
**ServiceBusService**를 사용하여 수행되는 작업에 선택적 필터링 작업을 적용할 수 있습니다. 필터링 작업은 로깅, 자동 재시도 등을 포함할 수 있습니다. 필터는 시그니쳐가 있는 메서드를 구현하는 개체입니다.

```javascript
function handle (requestOptions, next)
```

요청 옵션에 대한 전처리를 수행한 후에 메서드는 `next`를 호출하고 다음 서명을 사용하여 콜백을 전달합니다.

```javascript
function (returnObject, finalCallback, next)
```

이 콜백에서 `returnObject`(서버에 요청 응답 반환)를 처리한 후에 콜백은 다음(있는 경우)을 호출하여 다른 필터를 계속 처리하거나 `finalCallback`을 호출하여 서비스 호출을 종료해야 합니다.

Node.js용 Azure SDK에는 재시도 논리를 구현하는 두 필터 **ExponentialRetryPolicyFilter** 및 **LinearRetryPolicyFilter**가 포함되어 있습니다. 다음 코드는 **ExponentialRetryPolicyFilter**를 사용하는 **ServiceBusService** 개체를 만듭니다.

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>구독 만들기
토픽 구독은 **ServiceBusService** 개체로도 만들 수 있습니다. 구독에는 이름이 지정되며, 구독의 가상 큐에 전달되는 메시지 집합을 제한하는 선택적 필터가 있을 수 있습니다.

> [!NOTE]
> 구독은 구독 자체 또는 구독과 연결된 토픽이 삭제될 때까지 영구적입니다. 애플리케이션에 구독을 만들기 위한 논리가 포함된 경우, `getSubscription` 메서드를 사용하여 구독이 존재하는지를 먼저 확인해야 합니다.
>
>

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>기본(MatchAll) 필터를 사용하여 구독 만들기
**MatchAll** 필터는 구독을 만들 때 사용하는 기본 필터입니다. **MatchAll** 필터를 사용하면 토픽에 게시된 모든 메시지가 구독의 가상 큐에 배치됩니다. 다음 예제에서는 AllMessages라는 구독을 만들고 기본 **MatchAll** 필터를 사용합니다.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>필터를 사용하여 구독 만들기
토픽으로 전송된 메시지 중 특정 토픽 구독 내에 표시되어야 하는 메시지의 범위를 지정하는 필터를 만들 수도 있습니다.

구독에서 지원하는 가장 유연한 유형의 필터는 SQL92 하위 집합을 구현하는 **SqlFilter**입니다. SQL 필터는 토픽에 게시된 메시지의 속성에 적용됩니다. SQL 필터와 함께 사용할 수 있는 식에 대한 자세한 내용은 [SqlFilter.SqlExpression][SqlFilter.SqlExpression] 구문을 참조하세요.

**ServiceBusService** 개체의 `createRule` 메서드를 사용하여 구독에 필터를 추가할 수 있습니다. 이 메서드를 사용하면 기존 구독에 새 필터를 추가할 수 있습니다.

> [!NOTE]
> 기본 필터는 모든 새로운 구독에 자동으로 적용되므로 먼저 기본 필터를 제거해야 합니다. 그렇지 않으면 **MatchAll**이 사용자가 지정하는 기타 필터를 재정의합니다. **ServiceBusService** 개체의 `deleteRule` 메서드를 사용하여 기본 규칙을 제거할 수 있습니다.
>
>

다음 예제에서는 사용자 지정 `messagenumber` 속성이 3보다 큰 메시지만 선택하는 **SqlFilter**를 사용하여 이름이 `HighMessages`인 구독을 만듭니다.

```javascript
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'HighMessages',
            'HighMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

마찬가지로, 다음 예제에서는 `messagenumber` 속성이 3 이하인 메시지만 선택하는 **SqlFilter**가 있는 이름이 `LowMessages`인 구독을 만듭니다.

```javascript
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'LowMessages',
            'LowMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

이제 `MyTopic`으로 메시지를 보내는 경우 `AllMessages` 토픽 구독을 구독하는 수신자에게는 배달되고, 메시지 내용에 따라 `HighMessages` 및 `LowMessages` 토픽 구독을 구독하는 수신자에게 선택적으로 배달됩니다.

## <a name="how-to-send-messages-to-a-topic"></a>토픽에 메시지를 보내는 방법
Service Bus 토픽에 메시지를 보내려면 애플리케이션에서 **ServiceBusService** 개체의 `sendTopicMessage` 메서드를 사용해야 합니다.
Service Bus 토픽으로 보내는 메시지는 **BrokeredMessage** 개체입니다.
**BrokeredMessage** 개체에는 표준 속성 세트(예: `Label` 및 `TimeToLive`), 애플리케이션별 사용자 지정 속성을 저장하는 데 사용되는 사전 및 문자열 데이터의 본문이 있습니다. 애플리케이션은 문자열 값을 `sendTopicMessage`에 전달하여 메시지의 본문을 설정할 수 있습니다. 그러면 필수 표준 속성이 기본값으로 채워집니다.

다음 예제에서는 5개의 테스트 메시지를 `MyTopic`에 보내는 방법을 보여 줍니다. 루프가 반복될 때마다 각 메시지의 `messagenumber` 속성 값이 변경되며 이 속성 값에 따라 해당 메시지를 받는 구독이 결정됩니다.

```javascript
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Service Bus 토픽은 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 표준 및 사용자 지정 애플리케이션 속성이 포함된 헤더의 최대 크기는 64KB입니다. 한 토픽에 저장되는 메시지 수에는 제한이 없지만 한 토픽에 저장되는 총 메시지 크기는 제한됩니다. 이 토픽 크기는 생성 시 정의되며 상한이 5GB입니다.

## <a name="receive-messages-from-a-subscription"></a>구독에서 메시지 받기
**ServiceBusService** 개체의 `receiveSubscriptionMessage` 메서드를 사용하여 구독에서 메시지를 받습니다. 기본적으로 메시지를 읽으면 구독에서 해당 메시지가 삭제됩니다. 하지만 `isPeekLock` 선택적 매개 변수를 **true**로 설정하여 메시지를 구독에서 삭제하지 않고 읽고(보고) 잠글 수 있습니다.

받기 작업의 일부로 메시지를 읽고 삭제하는 기본 동작은 가장 단순한 모델이며, 오류 발생 시 응용 프로그램이 메시지를 처리하지 않아도 되는 시나리오에서 가장 효과적입니다. 이 동작에 대한 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요. Service Bus가 메시지를 사용되는 것으로 표시했기 때문에 애플리케이션이 다시 시작되고 메시지를 다시 사용하기 시작할 때 충돌 전에 사용한 메시지는 누락됩니다.

`isPeekLock` 매개 변수를 **true**로 설정하면 수신은 2단계 작업이 되므로, 누락된 메시지를 허용하지 않는 애플리케이션을 지원할 수 있습니다. Service Bus는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠그고, 애플리케이션에 반환합니다.
애플리케이션은 메시지를 처리하거나 추가 처리를 위해 안전하게 저장한 후에 **deleteMessage** 메서드를 호출하여 수신 프로세스의 두 번째 단계를 완료하고 삭제할 메시지를 매개 변수로 전달합니다. **deleteMessage** 메서드는 메시지를 사용됨으로 표시하고 구독에서 제거합니다.

다음 예제에서는 `receiveSubscriptionMessage`를 사용하여 메시지를 받고 처리하는 방법을 보여줍니다. 먼저 'LowMessages' 구독에서 메시지를 받고 삭제한 다음, true로 설정된 `isPeekLock`을 사용하여 'HighMessages' 구독에서 메시지를 받습니다. 그런 다음 `deleteMessage`를 사용하여 메시지를 삭제합니다.

```javascript
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        })
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>애플리케이션 작동 중단 및 읽을 수 없는 메시지를 처리하는 방법
Service Bus는 애플리케이션 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 애플리케이션이 메시지를 처리할 수 없는 경우 **ServiceBusService** 개체의 `unlockMessage` 메서드를 호출할 수 있습니다. 이 메서드를 사용하면 Service Bus가 구독 내 메시지의 잠금을 해제하고 메시지를 다시 받을 수 있습니다. 이 인스턴스에서는 동일하게 사용되는 애플리케이션 또는 다른 사용되는 애플리케이션입니다.

구독 내에는 잠긴 메시지와 관련된 제한 시간도 있습니다. 애플리케이션에서 잠금 시간 제한이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 애플리케이션이 크래시되는 경우) Service Bus가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

애플리케이션이 메시지를 처리한 후 `deleteMessage` 메서드가 호출되기 전에 충돌하는 경우 다시 시작될 때 메시지가 애플리케이션에 다시 배달됩니다. 이 동작은 일반적으로 *최소 한 번 이상 처리*라고 합니다. 즉, 각 메시지가 최소 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 중복 메시지 배달을 처리하는 논리를 애플리케이션에 추가해야 합니다. 배달 시도 간에 일정하게 유지하는 메시지의 **MessageId** 속성을 사용하면 됩니다.

## <a name="delete-topics-and-subscriptions"></a>토픽 및 구독 삭제
토픽과 구독은 영구적이므로, [Azure Portal][Azure portal] 또는 프로그래밍 방식을 통해 명시적으로 삭제해야 합니다.
다음 예제는 `MyTopic` 토픽을 삭제하는 방법을 보여 줍니다.

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

토픽을 삭제하면 토픽에 등록된 모든 구독도 삭제됩니다. 구독을 개별적으로 삭제할 수도 있습니다. 아래 예제는 `HighMessages` 구독을 `MyTopic` 토픽에서 삭제하는 방법을 보여 줍니다.

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

## <a name="next-steps"></a>다음 단계
이제 Service Bus 토픽의 기본 사항을 익혔으므로 다음 링크를 따라 이동하여 자세한 내용을 확인할 수 있습니다.

* [큐, 토픽 및 구독][Queues, topics, and subscriptions]을 참조하세요.
* [SqlFilter][SqlFilter]에 대한 API 참조
* GitHub에서 [Node용 Azure SDK][Azure SDK for Node] 리포지토리를 방문하세요.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Node.js 애플리케이션을 만들어 Azure 웹 사이트에 배포]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

