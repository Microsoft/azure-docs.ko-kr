---
title: QnA Maker 서비스 설정-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 기술 자료를 만들려면 먼저 Azure에서 QnA Maker 서비스를 설정해야 합니다. 구독에 새 리소스를 만들 수 있는 권한이 있으면 누구든지 QnA Maker 서비스를 설정할 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/28/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 00b8e6d44ed8449aa4ddf8716039c8c85c558b8f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901722"
---
# <a name="manage-qna-maker-resources"></a>QnA Maker 리소스 관리

QnA Maker 기술 자료를 만들려면 먼저 Azure에서 QnA Maker 서비스를 설정해야 합니다. 구독에 새 리소스를 만들 수 있는 권한이 있으면 누구든지 QnA Maker 서비스를 설정할 수 있습니다.

## <a name="types-of-keys-in-qna-maker"></a>QnA Maker의 키 유형

QnA Maker 서비스는 두 가지 종류의 키, 즉 **구독 키** 와 **엔드포인트 키**를 처리 합니다.

![키 관리](../media/qnamaker-how-to-key-management/key-management.png)

|이름|위치|용도|
|--|--|--|
|구독 키|[Azure Portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|이러한 키는 [QnA Maker management Service api](https://go.microsoft.com/fwlink/?linkid=2092179)에 액세스 하는 데 사용 됩니다. 이러한 Api를 사용 하면 기술 자료의 질문과 대답을 편집 하 고 기술 자료를 게시할 수 있습니다. 이러한 키는 새 QnA Maker 서비스를 만들 때 생성 됩니다.<br><br>**키** 페이지의 **Cognitive Services** 리소스에서 이러한 키를 찾습니다.|
|엔드포인트 키|[QnA Maker 포털](https://www.qnamaker.ai)|이러한 키는 게시 된 기술 자료 엔드포인트에 액세스 하 여 사용자 질문에 대 한 응답을 가져오는 데 사용 됩니다. 일반적으로이 엔드포인트은 채팅 봇 또는 QnA Maker 서비스에 연결 하는 클라이언트 응용 프로그램 코드에서 사용 합니다. 이러한 키는 QnA Maker 기술 자료를 게시할 때 생성 됩니다.<br><br>**서비스 설정** 페이지에서 이러한 키를 찾습니다. 드롭다운 메뉴의 페이지 오른쪽 위에 있는 사용자 메뉴에서이 페이지를 찾습니다.|

## <a name="create-a-new-qna-maker-service"></a>새 QnA Maker 서비스 만들기

이 절차는 기술 자료 콘텐츠를 관리 하는 데 필요한 Azure 리소스를 만듭니다. 이러한 단계를 완료 한 후에는 Azure Portal 리소스의 **키** 페이지에서 _구독_ 키를 찾을 수 있습니다.

1. Azure Portal에 로그인 하 여 QnA Maker 리소스를 [만듭니다](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. 사용 약관을 읽은 후 **만들기** 를 선택 합니다.

    ![새 QnA Maker 서비스 만들기](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. **QnA Maker**에서 적절 한 계층 및 지역을 선택 합니다.

    ![새 QnA Maker 서비스 만들기 - 가격 책정 계층 및 지역](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * **이름** 필드에이 QnA Maker 서비스를 식별 하는 고유한 이름을 입력 합니다. 또한이 이름은 기술 자료가 연결 될 QnA Maker 엔드포인트을 식별 합니다.
    * QnA Maker 리소스를 배포할 **구독** 을 선택 합니다.
    * QnA Maker 관리 서비스 (포털 및 관리 Api)에 대 한 **가격 책정 계층** 을 선택 합니다. [SKU 가격 책정에 대 한 자세한 내용을](https://aka.ms/qnamaker-pricing)참조 하세요.
    * 새 리소스 그룹을 만들거나 (권장)이 QnA Maker 리소스를 배포할 기존 **리소스 그룹** 을 사용 합니다. QnA Maker는 여러 Azure 리소스를 만듭니다. 이러한 리소스를 보유 하는 리소스 그룹을 만들 때 리소스 그룹 이름으로 이러한 리소스를 쉽게 찾고 관리 하 고 삭제할 수 있습니다.
    * **리소스 그룹 위치**를 선택 합니다.
    * Azure Cognitive Search 서비스의 **검색 가격 책정 계층** 을 선택 합니다. 무료 계층 옵션을 사용할 수 없는 경우 (흐리게 표시 됨) 이미 구독을 통해 배포 된 무료 서비스가 있음을 의미 합니다. 이 경우 기본 계층으로 시작 해야 합니다. [Azure Cognitive Search 가격 정보](https://azure.microsoft.com/pricing/details/search/)를 참조 하세요.
    * Azure Cognitive Search 인덱스를 배포할 **검색 위치** 를 선택 합니다. 고객 데이터를 저장 해야 하는 위치에 대 한 제한 사항은 Azure Cognitive Search에 대해 선택한 위치를 결정 하는 데 도움이 됩니다.
    * **앱 이름** 필드에 Azure App Service 인스턴스의 이름을 입력 합니다.
    * 기본적으로 App Service 기본값은 표준 (S1) 계층입니다. 계획을 만든 후에 변경할 수 있습니다. [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/)에 대해 자세히 알아보세요.
    * App Service를 배포할 **웹 사이트 위치** 를 선택 합니다.

        > [!NOTE]
        > **검색 위치** 는 **웹 사이트 위치**와 다를 수 있습니다.

    * **Application Insights**사용 하도록 설정할지 여부를 선택 합니다. **Application Insights**를 사용하도록 설정하면 QnA Maker는 트래픽, 채팅 로그 및 오류에 대한 원격 분석을 수집합니다.
    * Application Insights 리소스를 배포할 **앱 정보 위치** 를 선택 합니다.
    * 비용 절감 측정값의 경우, QnA Maker용으로 생성된 일부(전체는 아님) Azure 리소스를 [공유](#share-existing-services-with-qna-maker)할 수 있습니다.

1. 모든 필드의 유효성을 검사 한 후 **만들기**를 선택 합니다. 프로세스를 완료 하는 데 몇 분 정도 걸릴 수 있습니다.

1. 배포가 완료 되 면 구독에 다음과 같은 리소스가 생성 됩니다.

   ![QnA Maker 서비스를 새로 만든 리소스](../media/qnamaker-how-to-setup-service/resources-created.png)

    _Cognitive Services_ 형식의 리소스에는 _구독_ 키가 있습니다.

## <a name="find-subscription-keys-in-the-azure-portal"></a>Azure Portal에서 구독 키 찾기

QnA Maker 리소스를 만든 Azure Portal에서 구독 키를 확인 하 고 다시 설정할 수 있습니다.

1. Azure Portal의 QnA Maker 리소스로 이동 하 고 _Cognitive Services_ 형식의 리소스를 선택 합니다.

    ![QnA Maker 리소스 목록](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. **키**로 이동:

    ![구독 키](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>QnA Maker 포털에서 엔드포인트 키 찾기

엔드포인트은 기술 자료에 대 한 호출을 수행 하는 데 사용 되므로 엔드포인트은 리소스와 동일한 영역에 있습니다.

엔드포인트 키는 [QnA Maker 포털](https://qnamaker.ai)에서 관리할 수 있습니다.

1. [QnA Maker 포털](https://qnamaker.ai)에 로그인 하 고, 프로필로 이동한 다음, **서비스 설정**을 선택 합니다.

    ![엔드포인트 키](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 키 보기 또는 다시 설정:

    > [!div class="mx-imgBorder"]
    > [서비스 설정에서 엔드포인트 키를 표시, 복사 또는 설정 ![](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)](../media/qnamaker-how-to-key-management/Endpoint-keys1.png#lightbox)

    >[!NOTE]
    >키가 손상 되었다고 생각 되 면 키를 새로 고칩니다. 클라이언트 애플리케이션 또는 봇 코드에 해당 변경 내용을 적용해야 할 수도 있습니다.

## <a name="share-existing-services-with-qna-maker"></a>QnA Maker와 기존 서비스 공유

QnA Maker는 여러 Azure 리소스를 만듭니다. 관리를 줄이고 비용 공유를 활용 하려면 다음 표를 사용 하 여 공유할 수 있는 항목과 공유할 수 없는 항목을 이해 하십시오.

|서비스|공유|이유|
|--|--|--|
|Cognitive Services|X|디자인에서 불가능|
|App Service 계획|✔|App Service 계획에 할당 된 디스크 공간을 고정 했습니다. 동일한 App Service 계획을 공유 하는 다른 앱에서 상당한 디스크 공간을 사용 하는 경우 QnAMaker App Service 인스턴스에 문제가 발생 합니다.|
|App Service|X|디자인에서 불가능|
|Application Insights|✔|공유할 수 있습니다.|
|Search 서비스|✔|1. `testkb`는 QnAMaker 서비스에 예약 된 이름입니다. 다른 사용자는 사용할 수 없습니다.<br>2. 이름 `synonym-map` 별 동의어 맵이 QnAMaker 서비스에 예약 되어 있습니다.<br>3. 게시 된 기술 자료의 수는 검색 서비스 계층에 의해 제한 됩니다. 사용 가능한 인덱스가 있는 경우 다른 서비스에서 사용할 수 있습니다.|

[App service](../../../app-service/index.yml) 및 [Search 서비스](../../../search/index.yml)에 대해 자세히 알아보세요.

## <a name="using-a-single-search-service"></a>단일 검색 서비스 사용

포털을 통해 QnA 서비스 및 해당 종속성 (예: 검색)을 만드는 경우 검색 서비스가 만들어지고 QnA Maker 서비스에 연결 됩니다. 이러한 리소스를 만든 후 이전에 기존 검색 서비스를 사용 하도록 App Service 설정을 업데이트 하 고 방금 만든 검색 서비스를 제거할 수 있습니다.

Azure Resource Manager 템플릿을 통해 QnA 서비스를 만드는 경우 모든 리소스를 만들고 기존 검색 서비스를 사용 하는 App Service 만들기를 제어할 수 있습니다.


## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>다른 Cognitive Search 리소스를 사용 하도록 QnA Maker 구성

포털을 통해 QnA 서비스 및 해당 종속성 (예: 검색)을 만드는 경우 검색 서비스가 만들어지고 QnA Maker 서비스에 연결 됩니다. 이러한 리소스를 만든 후 이전에 기존 검색 서비스를 사용 하도록 App Service 설정을 업데이트 하 고 방금 만든 검색 서비스를 제거할 수 있습니다.

QnA Maker의 **App Service** 리소스는 Cognitive Search 리소스를 사용 합니다. QnA Maker에서 사용 하는 Cognitive Search 리소스를 변경 하려면 Azure Portal에서 설정을 변경 해야 합니다.

1. 사용할 QnA Maker Cognitive Search 리소스의 **관리자 키** 와 **이름을** 가져옵니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 QnA Maker 리소스와 연결 된 **App Service** 를 찾습니다. 둘 다 동일한 이름을 갖습니다.

1. **설정**, **구성**을 차례로 선택 합니다. 그러면 QnA Maker의 App Service에 대 한 모든 기존 설정이 표시 됩니다.

    > [!div class="mx-imgBorder"]
    > [App Service 구성 설정을 보여 주는 Azure Portal의 ![스크린샷](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png#lightbox)

1. 다음 키에 대 한 값을 변경 합니다.

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. 새 설정을 사용 하려면 App service를 다시 시작 해야 합니다. **개요**를 선택한 다음 **다시 시작**을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > [구성 설정이 변경 된 후 App Service 다시 시작 하 Azure Portal ![스크린샷](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Azure Resource Manager 템플릿을 통해 QnA 서비스를 만드는 경우 모든 리소스를 만들고 기존 검색 서비스를 사용 하는 App Service 만들기를 제어할 수 있습니다.

## <a name="upgrade-qna-maker"></a>업그레이드 QnA Maker

|업그레이드|이유|
|--|--|
|[업그레이드](#upgrade-qna-maker-sku) QnA Maker 관리 SKU|기술 자료에서 더 많은 질문과 대답을 원합니다.|
|[업그레이드](#upgrade-app-service) App Service SKU|기술 자료가 채팅 봇과 같은 클라이언트 앱에서 더 많은 요청을 처리 해야 합니다.|
|[업그레이드](#upgrade-the-azure-cognitive-search-service) Azure Cognitive Search 서비스|많은 기술 자료를 사용할 예정입니다.|


### <a name="upgrade-qna-maker-sku"></a>QnA Maker SKU 업그레이드

현재 계층을 벗어나 기술 자료에서 더 많은 질문과 대답을 원하는 경우 QnA Maker 서비스 가격 책정 계층을 업그레이드 하세요.

QnA Maker 관리 SKU를 업그레이드하려면

1. Azure Portal의 QnA Maker 리소스로 이동한 다음, **가격 책정 계층**을 선택합니다.

    ![QnA Maker 리소스](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. 적절한 SKU를 선택하고 **선택**을 누릅니다.

    ![QnA Maker 가격 책정](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>업그레이드 App Service

 기술 자료가 클라이언트 앱에서 더 많은 요청을 처리 해야 하는 경우 App Service 가격 책정 계층을 업그레이드 합니다.

App Service를 [강화 하거나 규모](https://docs.microsoft.com/azure/app-service/manage-scale-up) 를 확장할 수 있습니다.

Azure Portal에서 App Service 리소스로 이동 하 고 필요에 따라 **수직 확장** 또는 **수평 확장** 옵션을 선택 합니다.

![QnA Maker App Service 크기 조정](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Azure Cognitive Search 서비스 업그레이드

많은 기술 자료를 사용할 계획인 경우 Azure Cognitive Search 서비스 가격 책정 계층을 업그레이드 하세요.

현재 Azure search SKU의 전체 업그레이드를 수행할 수 없습니다. 그러나 원하는 SKU로 새 Azure Search 리소스를 만들고, 데이터를 새 리소스로 복원한 다음, QnA Maker 스택에 연결할 수 있습니다. 이렇게 하려면 다음 단계를 수행하세요.

1. Azure Portal에서 새 Azure search 리소스를 만들고 원하는 SKU를 선택 합니다.

    ![QnA Maker Azure Search 리소스](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. 원래 Azure Search 리소스의 인덱스를 새 리소스로 복원합니다. [백업 복원 샘플 코드](https://github.com/pchoudhari/QnAMakerBackupRestore)를 참조 하세요.

1. 데이터가 복원 된 후에는 새 Azure search 리소스로 이동 하 고, **키**를 선택 하 고, **이름** 및 **관리자 키**를 적어둡니다.

    ![QnA Maker Azure Search 키](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. 새 Azure search 리소스를 QnA Maker 스택에 연결 하려면 QnA Maker App Service 인스턴스로 이동 합니다.

    ![QnA Maker App Service 인스턴스](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. **응용 프로그램 설정** 을 선택 하 고 3 단계에서 **azuresearchname** 및 **azuresearchadminkey** 필드의 설정을 수정 합니다.

    ![QnA Maker App Service 설정](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. App Service 인스턴스를 다시 시작 합니다.

    ![QnA Maker App Service 인스턴스 다시 시작](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>최신 런타임 업데이트 가져오기

QnAMaker 런타임은 Azure Portal에서 [QnAMaker 서비스를 만들](./set-up-qnamaker-service-azure.md) 때 배포 되는 Azure App Service 인스턴스의 일부입니다. 런타임은 주기적으로 업데이트됩니다. QnA Maker App Service 인스턴스는 4 월 2019 사이트 확장 릴리스 (버전 5 이상) 이후 자동 업데이트 모드에 있습니다. 이 업데이트는 업그레이드 하는 동안 가동 중지 시간을 0으로 처리 하도록 설계 되었습니다.

https://www.qnamaker.ai/UserSettings 에서 현재 버전을 확인할 수 있습니다. 버전이 버전 5.x 보다 이전 버전인 경우 App Service를 다시 시작 하 여 최신 업데이트를 적용 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 QnAMaker 서비스 (리소스 그룹)로 이동 합니다.

    ![QnAMaker Azure 리소스 그룹](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. App Service 인스턴스를 선택 하 고 **개요** 섹션을 엽니다.

    ![QnAMaker App Service 인스턴스](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. App Service를 다시 시작 합니다. 업데이트 프로세스는 몇 초 내에 완료 되어야 합니다. 이 QnAMaker 서비스를 사용 하는 모든 종속 응용 프로그램 또는 봇은이 재시작 기간 동안 최종 사용자가 사용할 수 없습니다.

    ![QnAMaker App Service 인스턴스 다시 시작](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>관리 서비스 지역

QnA Maker 관리 서비스는 QnA Maker 포털 및 초기 데이터 처리에만 사용 됩니다. 이 서비스는 미국 서 부 지역 에서만 사용할 수 있습니다. 이 미국 서 부 서비스에는 고객 데이터가 저장 되지 않습니다.

## <a name="next-steps"></a>다음 단계

[App service](../../../app-service/index.yml) 및 [Search 서비스](../../../search/index.yml)에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [기술 자료 만들기 및 게시](../Quickstarts/create-publish-knowledge-base.md)
