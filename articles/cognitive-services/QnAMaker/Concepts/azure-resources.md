---
title: Azure 리소스-QnA Maker
description: QnA Maker는 각각 다른 용도로 사용 되는 여러 Azure 소스를 사용 합니다. 이를 개별적으로 사용 하는 방법을 이해 하면 올바른 가격 책정 계층을 계획 하 고 선택 하거나 가격 책정 계층을 변경 하는 시기를 알 수 있습니다. 이러한 기능을 조합 하 여 사용 하는 방법을 이해 하면 문제 발생 시 문제를 찾아 해결할 수 있습니다.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 06731c97217e3b6a5c498743b83f1d16bab791eb
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548432"
---
# <a name="azure-resources-for-qna-maker"></a>QnA Maker에 대 한 Azure 리소스

QnA Maker는 각각 다른 용도로 사용 되는 여러 Azure 소스를 사용 합니다. 이를 개별적으로 사용 하는 방법을 이해 하면 올바른 가격 책정 계층을 계획 하 고 선택 하거나 가격 책정 계층을 변경 하는 시기를 알 수 있습니다. 이러한 기능을 _조합_ 하 여 사용 하는 방법을 이해 하면 문제 발생 시 문제를 찾아 해결할 수 있습니다.

## <a name="resource-planning"></a>리소스 계획

QnA Maker 기술 자료를 처음으로 개발 하는 경우에는 프로토타입 단계에서 테스트와 프로덕션 모두에 단일 QnA Maker 리소스를 보유 하는 것이 일반적입니다.

프로젝트의 개발 단계로 이동 하는 경우 다음을 고려해 야 합니다.

* 기술 자료 시스템에서 보유할 언어 수
* 지원 되는/에서 기술 자료가 필요한 지역 수
* 시스템이 보유 하는 각 도메인의 문서 수

단일 QnA Maker 리소스에 동일한 언어, 동일한 지역 및 동일한 주체 도메인 조합을 가진 모든 기술 자료를 보유 하도록 계획 합니다.

## <a name="pricing-tier-considerations"></a>가격 책정 계층 고려 사항

일반적으로 고려해야 하는 다음 세 가지 매개 변수가 있습니다.

* **서비스에서 필요한 처리량**:
    * 필요에 따라 App Service에 적합한 [앱 플랜](https://azure.microsoft.com/pricing/details/app-service/plans/)을 선택합니다. 앱을 [확장](https://docs.microsoft.com/azure/app-service/manage-scale-up)하거나 축소할 수 있습니다.
    * 이는 Azure **Cognitive Search** SKU 선택에도 영향을 줍니다. 자세한 내용은 [여기](https://docs.microsoft.com/azure/search/search-sku-tier)를 참조 하세요. 또한 복제본을 사용 하 여 Cognitive Search [용량](../../../search/search-capacity-planning.md) 을 조정 해야 할 수도 있습니다.

* **기술 자료의 크기 및 개수**: 시나리오에 적합한 [Azure Search SKU](https://azure.microsoft.com/pricing/details/search/)를 선택합니다. 일반적으로 다양 한 주체 도메인의 수에 따라 필요한 기술 자료의 수를 결정 합니다. 단일 언어에 대 한 주체 도메인은 하나의 기술 자료에 있어야 합니다.

    특정 계층에 N-1개의 기술 자료를 게시할 수 있습니다. 여기서 N은 계층에서 허용되는 최대 인덱스 개수입니다. 또한 계층당 허용되는 문서의 최대 크기 및 개수를 확인합니다.

    예를 들어 계층에 허용되는 인덱스가 15개 있으면 기술 자료 14개를 게시할 수 있습니다(게시되는 기술 자료당 인덱스 1개). 15번째 인덱스는 모든 기술 자료에서 작성 및 테스트용으로 사용됩니다.

* **원본 문서 수**: QnA Maker 관리 서비스의 체험 SKU는 포털 및 API를 통해 관리할 수 있는 문서 수를 3개(각각 1MB 크기)로 제한합니다. 표준 SKU는 관리할 수 있는 문서 수에 대한 제한이 없습니다. 자세한 내용은 [여기](https://aka.ms/qnamaker-pricing)를 참조하세요.

다음 표에서는 몇 가지 전반적인 지침을 제공합니다.

|                            | QnA Maker 관리 | App Service | Azure Cognitive Search | 제한 사항                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **실험**        | 체험 SKU             | 체험 계층   | 체험 계층    | KB 최대 2개, 50MB 크기까지 게시  |
| **개발/테스트 환경**   | 표준 SKU         | 공유됨      | Basic        | 최대 14KB, 2GB 크기까지 게시    |
| **프로덕션 환경** | 표준 SKU         | Basic       | Standard     | KB 최대 49개, 25GB 크기까지 게시 |

## <a name="recommended-settings"></a>권장 설정

|대상 QPS | App Service | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1, 인스턴스 1 개   | S1, 인스턴스 1 개    |
| 50         | S3, 10 개 인스턴스       | S1, 12 개 인스턴스         |
| 80         | S3, 10 개 인스턴스      |  S3, 12 개 인스턴스  |
| 100         | P3V2가, 10 개 인스턴스  | S3, 12 개 인스턴스, 3 개 파티션   |
| 200 ~ 250         | P3V2가, 20 개 인스턴스 | S3, 12 개 인스턴스, 3 개 파티션    |

## <a name="when-to-change-a-pricing-tier"></a>가격 책정 계층을 변경 하는 경우

|업그레이드|이유|
|--|--|
|[업그레이드](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) QnA Maker 관리 SKU|기술 자료에 더 많은 QnA 또는 문서 원본을 포함 하려고 합니다.|
|[업그레이드](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) SKU를 App Service 하 고 Cognitive Search 계층을 확인 하 고 [Cognitive Search 복제본을 만듭니다](../../../search/search-capacity-planning.md) .|기술 자료가 채팅 봇과 같은 클라이언트 앱에서 더 많은 요청을 처리 해야 합니다.|
|[업그레이드](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure Cognitive Search 서비스|많은 기술 자료를 사용할 예정입니다.|

[Azure Portal에서 App Service를 업데이트](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)하 여 최신 런타임 업데이트를 가져옵니다.

## <a name="resource-naming-considerations"></a>리소스 명명 고려 사항

과 같은 QnA Maker 리소스의 리소스 이름도 `qna-westus-f0-b` 다른 리소스의 이름을 사용 하는 데 사용 됩니다.

Azure Portal 만들기 창에서 QnA Maker 리소스를 만들고 다른 리소스에 대 한 가격 책정 계층을 선택할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![QnA Maker 리소스를 만들기 위한 Azure Portal의 스크린샷](../media/concept-azure-resource/create-blade.png)

리소스를 만든 후에는 이름에 문자를 postpends 하는 선택적 Application Insights 리소스를 제외 하 고 동일한 이름을 갖습니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal 리소스 목록 스크린샷](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> QnA Maker 리소스를 만들 때 새 리소스 그룹을 만듭니다. 리소스 그룹별 검색할 때 QnA Maker 리소스와 연결 된 모든 리소스를 볼 수 있습니다.

> [!TIP]
> 명명 규칙을 사용 하 여 리소스 이름 또는 리소스 그룹 내의 가격 책정 계층을 지정 합니다. 새 기술 자료를 만들거나 새 문서를 추가 하는 것에서 오류가 발생 하는 경우 Cognitive Search 가격 책정 계층 한도가 일반적인 문제입니다.

## <a name="resource-purposes"></a>리소스 목적

QnA Maker를 사용 하 여 만든 각 Azure 리소스는 특정 목적이 있습니다.

* QnA Maker 리소스
* Cognitive Search 리소스
* App Service
* 앱 계획 서비스
* Application Insights 서비스


### <a name="cognitive-search-resource"></a>Cognitive Search 리소스

[Cognitive Search](../../../search/index.yml) 리소스는 다음 작업에 사용 됩니다.

* QnA 쌍 저장
* 런타임에 QnA 쌍의 초기 순위 (ranker #1)를 제공 합니다.

#### <a name="index-usage"></a>인덱스 사용

리소스는 테스트 인덱스 역할을 하는 하나의 인덱스를 유지 하 고 나머지 인덱스는 게시 된 기술 자료 하나에 상관 관계가 있습니다.

리소스는 15 개의 인덱스를 보유 하 고, 14 개의 게시 된 기술 자료를 보유 하며, 한 개의 인덱스가 모든 기술 자료를 테스트 하는 데 사용 됩니다. 이 테스트 인덱스는 대화형 테스트 창을 사용 하는 쿼리가 테스트 인덱스를 사용 하지만 특정 기술 자료와 연결 된 특정 파티션의 결과만 반환 되도록 기술 자료에 의해 분할 됩니다.

#### <a name="language-usage"></a>언어 사용

QnA Maker 리소스에서 만든 첫 번째 기술 자료는 Cognitive Search 리소스 및 모든 인덱스에 대 한 _단일_ 언어 집합을 결정 하는 데 사용 됩니다. QnA Maker 서비스에는 _언어를 하나만 설정할_ 수 있습니다.

### <a name="qna-maker-resource"></a>QnA Maker 리소스

QnA Maker 리소스는 작성 및 게시 Api 뿐만 아니라 런타임에 QnA 쌍의 NLP (자연어 처리) 기반 #2 ranker ()를 기반으로 하는 Api에 대 한 액세스를 제공 합니다.

두 번째 순위는 메타 데이터 및 추가 작업 프롬프트를 포함할 수 있는 지능형 필터를 적용 합니다.

#### <a name="qna-maker-resource-configuration-settings"></a>QnA Maker 리소스 구성 설정

[QnA Maker 포털](https://qnamaker.ai)에서 새 기술 자료를 만들 때 **언어** 설정은 리소스 수준에서 적용 되는 유일한 설정입니다. 리소스의 첫 번째 기술 자료를 만들 때 언어를 선택 합니다.

### <a name="app-service-and-app-service-plan"></a>App service 및 App service 계획

[앱 서비스](../../../app-service/index.yml) 는 클라이언트 응용 프로그램에서 런타임 끝점을 통해 게시 된 기술 자료에 액세스 하는 데 사용 됩니다.

게시 된 기술 자료를 쿼리하려면 게시 된 모든 기술 자료가 동일한 URL 끝점을 사용 하지만 경로 내에서 **기술 자료 ID** 를 지정 합니다.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) 은 채팅 로그 및 원격 분석을 수집 하는 데 사용 됩니다. 서비스에 대 한 자세한 내용은 common [Kusto 쿼리](../how-to/get-analytics-knowledge-base.md) 를 참조 하세요.

## <a name="share-services-with-qna-maker"></a>QnA Maker와 서비스 공유

QnA Maker는 여러 Azure 리소스를 만듭니다. 관리를 줄이고 비용 공유를 활용 하려면 다음 표를 사용 하 여 공유할 수 있는 항목과 공유할 수 없는 항목을 이해 하십시오.

|서비스|공유|이유|
|--|--|--|
|Cognitive Services|X|디자인에서 불가능|
|App Service 계획|✔|App Service 계획에 할당 된 디스크 공간을 고정 했습니다. 동일한 App Service 계획을 공유 하는 다른 앱에서 상당한 디스크 공간을 사용 하는 경우 QnAMaker App Service 인스턴스에 문제가 발생 합니다.|
|App Service|X|디자인에서 불가능|
|Application Insights|✔|공유할 수 있습니다.|
|Search 서비스|✔|1. `testkb` 은 QnAMaker 서비스에 예약 된 이름이 고 다른 서비스에서는 사용할 수 없습니다.<br>2. 이름이 인 동의어 맵이 `synonym-map` QnAMaker 서비스에 예약 되어 있습니다.<br>3. 게시 된 기술 자료의 수는 검색 서비스 계층에 의해 제한 됩니다. 사용 가능한 인덱스가 있는 경우 다른 서비스에서 사용할 수 있습니다.|

### <a name="using-a-single-cognitive-search-service"></a>단일 Cognitive Search 서비스 사용

포털을 통해 QnA 서비스 및 해당 종속성 (예: 검색)을 만드는 경우 검색 서비스가 만들어지고 QnA Maker 서비스에 연결 됩니다. 이러한 리소스를 만든 후 이전에 기존 검색 서비스를 사용 하도록 App Service 설정을 업데이트 하 고 방금 만든 검색 서비스를 제거할 수 있습니다.

QnA Maker 리소스 생성 프로세스의 일부로 생성 된 것과 다른 인지 서비스 리소스를 사용 하도록 QnA Maker를 [구성 하는 방법](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) 에 대해 알아봅니다.

## <a name="management-service-region"></a>관리 서비스 지역

QnA Maker 관리 서비스는 QnA Maker 포털 및 초기 데이터 처리에만 사용 됩니다. 이 서비스는 **미국 서 부** 지역 에서만 사용할 수 있습니다. 이 미국 서 부 서비스에는 고객 데이터가 저장 되지 않습니다.

## <a name="keys-in-qna-maker"></a>QnA Maker의 키

QnA Maker 서비스는 App service에서 호스팅되는 런타임과 함께 사용 되는 키와 **쿼리 끝점 키** 를 **작성** 하는 두 가지 종류의 키를 처리 합니다.

**구독 키**를 찾고 있는 경우 [용어가 변경 되었습니다](#subscription-keys).

Api를 통해 서비스에 대 한 요청을 만들 때 이러한 키를 사용 합니다.

![키 관리](../media/qnamaker-how-to-key-management/key-management.png)

|이름|위치|용도|
|--|--|--|
|작성 키|[Azure Portal](https://azure.microsoft.com/free/cognitive-services/)|이러한 키는 [QnA Maker 관리 서비스 API](https://go.microsoft.com/fwlink/?linkid=2092179)에 액세스하는 데 사용됩니다. 이러한 Api를 사용 하면 기술 자료의 질문과 대답을 편집 하 고 기술 자료를 게시할 수 있습니다. 이러한 키는 새 QnA Maker 서비스를 만들 때 생성 됩니다.<br><br>**키** 페이지의 **Cognitive Services** 리소스에서 이러한 키를 찾습니다.|
|쿼리 끝점 키|[QnA Maker 포털](https://www.qnamaker.ai)|이러한 키는 게시 된 기술 자료 끝점을 쿼리하여 사용자 질문에 대 한 응답을 가져오는 데 사용 됩니다. 일반적으로이 쿼리 끝점은 채팅 봇 또는 QnA Maker 서비스에 연결 하는 클라이언트 응용 프로그램 코드에서 사용 합니다. 이러한 키는 QnA Maker 기술 자료를 게시할 때 생성 됩니다.<br><br>**서비스 설정** 페이지에서 이러한 키를 찾습니다. 드롭다운 메뉴의 페이지 오른쪽 위에 있는 사용자 메뉴에서이 페이지를 찾습니다.|

### <a name="subscription-keys"></a>구독 키

용어 작성 및 쿼리 끝점 키는 정정 조건입니다. 이전 용어는 **구독 키**였습니다. 구독 키를 참조 하는 다른 설명서가 표시 되는 경우이는 런타임에 사용 되는 끝점 키 작성 및 쿼리와 동일 합니다.

어떤 키를 찾아야 하는지 파악 하기 위해 키에 액세스 하는 기술 자료 관리 또는 기술 자료 쿼리를 알고 있어야 합니다.

## <a name="recommended-settings-for-network-isolation"></a>네트워크 격리에 대 한 권장 설정

* [가상 네트워크를 구성](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal)하 여 공용 액세스에서 인지 서비스 리소스를 보호 합니다.
* 공용 액세스에서 App Service (QnA Runtime) 보호:
    * 인지 서비스 Ip의 트래픽만 허용 합니다. 이러한 설정은 서비스 태그 "CognitiveServicesManagement"에 이미 포함 되어 있습니다. 이는 app service를 호출 하 고 Azure Search 서비스를 업데이트 하는 Api (만들기/업데이트 KB)를 작성 하는 데 필요 합니다.
    * 또한 Bot service, QnA Maker portal (corpnet 일 수 있음) 등의 다른 진입점을 허용 하 고, 예측 "GenerateAnswer" API 액세스를 위한 것입니다.
    * [서비스 태그에 대 한 자세한 정보를](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) 확인 하세요.

## <a name="next-steps"></a>다음 단계

* QnA Maker [기술 자료](knowledge-base.md) 에 대 한 자세한 정보
* [기술 자료 수명 주기](development-lifecycle-knowledge-base.md) 이해
* 서비스 및 기술 자료 [제한](../limits.md) 검토

