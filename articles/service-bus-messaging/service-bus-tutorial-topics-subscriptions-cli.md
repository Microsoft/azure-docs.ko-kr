---
title: Azure CLI를 사용하여 Service Bus 토픽 및 구독 만들기
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Service Bus 토픽 및 구독을 만드는 방법에 대해 알아봅니다.
ms.date: 06/23/2020
ms.topic: quickstart
author: spelluru
ms.author: spelluru
ms.openlocfilehash: 3a6535a13ab00c4e22ac4cd8c2de5a5bbb02d0a8
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88189805"
---
# <a name="use-azure-cli-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Azure CLI를 사용하여 Service Bus 토픽 및 해당 토픽에 대한 구독 만들기
이 빠른 시작에서는 Azure CLI를 사용하여 Service Bus 토픽을 만든 다음, 해당 토픽에 대한 구독을 만듭니다. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Service Bus 토픽 및 구독 정의
Service Bus 토픽 및 구독은 *게시/구독* 메시징 통신 모델을 지원합니다. 토픽 및 구독을 사용하는 경우 분산 애플리케이션의 구성 요소가 서로 직접 통신하지 않고 중간자 역할을 하는 토픽을 통해 메시지를 교환합니다.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

각 메시지가 단일 소비자에 의해 처리되는 Service Bus 큐와 반대로, 토픽과 구독은 게시/구독 패턴을 사용하여 일대다 형태의 통신을 제공합니다. 하나의 토픽에 여러 구독을 등록할 수 있습니다. 토픽에 메시지를 전송하면 각 구독에서 독립적으로 처리할 수 있습니다. 토픽 구독은 토픽에 전송된 메시지의 복사본을 받는 가상 큐와 유사합니다. 선택적으로 구독별 토픽에 대한 필터 규칙을 등록할 수 있으며, 이렇게 하면 각 토픽 구독에서 받는 토픽 메시지를 필터링하거나 제한할 수 있습니다.

Service Bus 토픽 및 구독을 사용하면 다수의 사용자와 애플리케이션에 대해 다수의 메시지를 처리하도록 확장할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정][free account]을 만들 수 있습니다.

이 빠른 시작에서는 Azure Portal에 로그인한 후에 시작할 수 있는 Azure Cloud Shell을 사용합니다. Azure Cloud Shell에 대한 자세한 내용은 [Azure Cloud Shell 개요](../cloud-shell/overview.md)를 참조하세요. 머신에 Azure PowerShell을 [설치](/cli/azure/install-azure-cli)하고 사용할 수도 있습니다. 

## <a name="create-a-service-bus-topic-and-subscriptions"></a>Service Bus 항목과 구독 만들기
[토픽에 대한 각 구독](service-bus-messaging-overview.md#topics)은 각 메시지의 복사본을 받을 수 있습니다. 토픽은 완전히 프로토콜이며, 의미상 Service Bus 큐와 호환됩니다. Service Bus 토픽은 메시지 속성을 설정하거나 수정하는 선택적 동작과 함께 필터 조건이 포함된 다양한 선택 규칙을 지원합니다. 규칙이 일치할 때마다 메시지를 생성합니다. 규칙, 필터 및 작업에 대해 자세히 알아보려면 이 [링크](topic-filters.md)를 따르세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 다음 이미지에 표시된 아이콘을 선택하여 Azure Cloud Shell을 시작합니다. Cloud Shell이 **PowerShell** 모드에 있으면 **Bash** 모드로 전환합니다. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell 시작":::
3. 다음 명령을 실행하여 Azure 리소스 그룹을 만듭니다. 원하는 경우 리소스 그룹 이름 및 위치를 업데이트합니다. 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```
4. 다음 명령을 입력하여 Service Bus 메시징 네임스페이스를 만듭니다. 네임스페이스의 이름을 고유하게 업데이트합니다. 

    ```azurecli-interactive
    namespaceName=MyNameSpace$RANDOM
    az servicebus namespace create --resource-group MyResourceGroup --name $namespaceName --location eastus
    ```
5. 다음 명령을 실행하여 네임스페이스에 항목을 만듭니다. 

    ```azurecli-interactive
    az servicebus topic create --resource-group MyResourceGroup   --namespace-name $namespaceName --name MyTopic
    ```
6. 항목에 대한 첫 번째 구독 만들기
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S1    
    ```
6. 항목에 대한 두 번째 구독 만들기
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S2    
    ```
6. 항목에 대한 세 번째 구독 만들기
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S3    
    ```
7. 사용자 지정 속성을 통해 필터를 사용하여 첫 번째 구독에서 필터를 만듭니다(`StoreId`는 `Store1`, `Store2` 및 `Store3` 중 하나임).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"    
    ```
8. 사용자 지정 속성(`StoreId = Store4`)을 통해 필터를 사용하여 두 번째 구독에서 필터를 만듭니다.

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"    
    ```
9. 사용자 지정 속성을 통해 필터를 사용하여 세 번째 구독에서 필터를 만듭니다(`Store1`, `Store2`, `Store3` 또는 `Store4`에 없는 `StoreId`).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S3 --name MyThirdFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3', 'Store4')"     
    ```
10. 다음 명령을 실행하여 네임스페이스에 대한 기본 연결 문자열을 가져옵니다. 이 연결 문자열을 사용하여 큐에 연결하고 메시지를 보내고 받습니다. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group MyResourceGroup --namespace-name $namespaceName --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    연결 문자열과 항목 이름을 적어 둡니다. 메시지를 보내고 받는 데 사용합니다. 
    

## <a name="next-steps"></a>다음 단계
토픽에 메시지를 보내고 이러한 메시지를 구독을 통해 수신하는 방법을 알아보려면 TOC에서 프로그래밍 언어 선택 문서를 참조하세요. 

> [!div class="nextstepaction"]
> [메시지 게시 및 구독](service-bus-dotnet-how-to-use-topics-subscriptions.md)


[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create