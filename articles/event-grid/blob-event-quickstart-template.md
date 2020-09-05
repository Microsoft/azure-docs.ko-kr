---
title: Blob 스토리지 이벤트를 웹 엔드포인트에 전송 - 템플릿
description: Azure Event Grid 및 Azure Resource Manager 템플릿을 사용하여 Blob 스토리지 계정을 만들고 해당 이벤트를 구독합니다. 이벤트를 Webhook에 보냅니다.'
ms.date: 07/07/2020
ms.topic: quickstart
ms.openlocfilehash: 32fc17d1af552afa5a81fbc4a5973d69006742b9
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661268"
---
# <a name="route-blob-storage-events-to-web-endpoint-by-using-an-arm-template"></a>ARM 템플릿을 사용하여 Blob 스토리지 이벤트를 웹 엔드포인트로 라우팅

Azure Event Grid는 클라우드에 대한 이벤트 서비스입니다. 이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Blob 스토리지 계정을 만들고, 해당 Blob 스토리지에 대한 이벤트를 구독하고, 결과를 보기 위한 이벤트를 트리거합니다. 일반적으로 이벤트 데이터를 처리하고 작업을 수행하는 엔드포인트에 이벤트를 보냅니다. 그러나 이 문서를 간소화하기 위해 메시지를 수집하고 표시하는 웹앱에 이벤트를 보냅니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

### <a name="create-a-message-endpoint"></a>메시지 엔드포인트 만들기

Blob Storage 계정에 대한 이벤트를 구독하기 전에 이벤트 메시지에 대한 엔드포인트를 만들어 보겠습니다. 일반적으로 엔드포인트는 이벤트 데이터를 기반으로 작업을 수행합니다. 이 빠른 시작을 간소화하기 위해 이벤트 메시지를 표시하는 [미리 작성된 웹앱](https://github.com/Azure-Samples/azure-event-grid-viewer)을 배포합니다. 배포된 솔루션은 App Service 계획, App Service 웹앱 및 GitHub의 소스 코드를 포함합니다.

1. **Azure에 배포**를 선택하여 구독에 솔루션을 배포합니다. Azure Portal에서 매개 변수에 대한 값을 제공합니다.

    [Azure에 배포](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)
1. 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 배포가 성공된 후 실행하는지 확인하려면 웹앱을 봅니다. 웹 브라우저에서 `https://<your-site-name>.azurewebsites.net`으로 이동합니다.

1. 참조하는 사이트에 이벤트가 아직 게시되지 않았습니다.

   ![새 사이트 보기](./media/blob-event-quickstart-portal/view-site.png)

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-event-grid-subscription-and-storage/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-event-grid-subscription-and-storage/azuredeploy.json":::

템플릿에는 두 개의 Azure 리소스가 정의되어 있습니다.

* [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): Azure Storage 계정을 만듭니다.
* [**Microsoft EventGrid/systemTopics**](/azure/templates/microsoft.eventgrid/systemtopics): 저장소 계정에 지정된 이름을 사용하여 시스템 항목을 만듭니다.
* [**Microsoft EventGrid/systemTopics/Eventgrid**](/azure/templates/microsoft.eventgrid/systemtopics/eventsubscriptions): 시스템 항목에 대한 Azure Event Grid 구독을 만듭니다.

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 링크를 선택하여 Azure에 로그인하고 템플릿을 엽니다. 템플릿에서 키 자격 증명 모음 및 비밀이 생성됩니다.

    [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json)

2. **엔드포인트** 지정: 웹앱의 URL을 제공하고 `api/updates`를 홈 페이지 URL에 추가합니다.
3. **구매**를 선택하여 템플릿을 배포합니다.

  여기에서 Azure Portal은 템플릿을 배포하는데 사용됩니다. Azure PowerShell, Azure CLI 및 REST API를 사용할 수도 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

> [!NOTE]
> [여기에서](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid&pageNumber=1&sort=Popular) 추가 Azure Event Grid 템플릿 샘플을 찾을 수 있습니다.

## <a name="validate-the-deployment"></a>배포 유효성 검사

웹앱을 다시 확인하고, 구독 유효성 검사 이벤트를 보냈음을 확인합니다. 눈 모양 아이콘을 선택하여 이벤트 데이터를 확장합니다. Event Grid는 유효성 검사 이벤트를 보내므로 엔드포인트는 이벤트 데이터를 수신하려는 것을 확인할 수 있습니다. 웹앱은 구독의 유효성을 검사하는 코드를 포함합니다.

![구독 이벤트 보기](./media/blob-event-quickstart-portal/view-subscription-event.png)

이제 이벤트를 트리거하여 Event Grid가 메시지를 사용자 엔드포인트에 어떻게 배포하는지 살펴 보겠습니다.

파일을 업로드하여 Blob Storage에 대한 이벤트를 트리거합니다. 파일에는 특정 콘텐츠가 필요하지 않습니다. 문서에서는 testfile.txt라는 파일이 있다고 해도 모든 파일을 사용할 수 있다고 가정합니다.

파일을 Azure Blob 스토리지에 업로드하면 Event Grid는 구독 시 구성한 엔드포인트로 메시지를 보냅니다. 메시지는 JSON 형식이며 하나 이상의 이벤트가 있는 배열을 포함합니다. 다음 예제에서 JSON 메시지는 이벤트가 1개 있는 배열을 포함합니다. 웹앱을 본 다음, Blob 이벤트를 수신했다고 알립니다.

![결과 보기](./media/blob-event-quickstart-portal/view-results.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [리소스 그룹을 삭제](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group
)합니다.

## <a name="next-steps"></a>다음 단계

Azure Resource Manager 템플릿에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Resource Manager 설명서](../azure-resource-manager/index.yml)
* [Azure Resource Manager 템플릿에서 리소스 정의](/azure/templates/)
* [Azure Quickstart 템플릿](https://azure.microsoft.com/resources/templates/)
* [Azure Event Grid 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid).
