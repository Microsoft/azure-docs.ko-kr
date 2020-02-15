---
title: Azure의 통합 및 자동화 플랫폼 옵션
description: 다음 통합 작업에 최적화된 Microsoft 클라우드 서비스를 비교합니다. Microsoft Flow, Logic Apps, Functions 및 WebJobs.
ms.topic: overview
ms.date: 04/09/2018
ms.custom: mvc
ms.openlocfilehash: bd9f3bfe1578b632707382cfe422f19514e7ce48
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988110"
---
> [!NOTE]
> Microsoft Flow는 이제 Power Automate입니다. 자세한 내용은 [이 블로그](https://aka.ms/flow-now-pa)를 참조하세요.
> 
> 이 콘텐츠는 향후에 브랜딩 변경을 반영하도록 업데이트됩니다.
# <a name="choose-the-right-integration-and-automation-services-in-azure"></a>Azure에서 적절한 통합 및 자동화 서비스 선택

이 문서에서는 다음 Microsoft 클라우드 서비스를 비교합니다.

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/webjobs-create.md)

이 모든 서비스는 통합 문제를 해결하고 비즈니스 프로세스를 자동화할 수 있습니다. 입력, 동작, 조건 및 출력을 모두 정의할 수 있습니다. 각 서비스를 일정이나 트리거에 따라 실행할 수 있습니다. 서비스마다 고유의 장점이 있으며, 이 문서에서는 차이점을 설명합니다. 

Azure Functions와 다른 Azure 컴퓨팅 옵션 간에 보다 일반적인 비교를 찾고 있는 경우 [Azure 컴퓨팅 서비스를 선택하기 위한 조건](/azure/architecture/guide/technology-choices/compute-comparison) 및 [마이크로서비스에 대한 Azure 컴퓨팅 옵션 선택](/azure/architecture/microservices/design/compute-options)을 참조하세요.

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>Microsoft Flow 및 Azure Logic Apps 비교

Microsoft Flow 및 Logic Apps는 모두 워크플로를 만들 수 있는 *디자이너 중심* 통합 서비스입니다. 두 서비스는 다양한 SaaS 및 엔터프라이즈 애플리케이션과 통합합니다. 

Microsoft Flow는 Logic Apps를 기반으로 빌드됩니다. 두 서비스는 동일한 워크플로 디자이너 및 [커넥터](../connectors/apis-list.md)를 공유합니다. 

Microsoft Flow는 개발자나 IT를 통하지 않고 사무실 작업자가 간단히 통합할 수 있도록 해줍니다(예: SharePoint 문서 라이브러리에 대한 승인 프로세스). 또한 Logic Apps를 통해 엔터프라이즈 수준의 Azure DevOps 및 보안 사례가 요구되는 고급 통합(예: B2B 프로세스)을 사용할 수 있습니다. 비즈니스 워크플로의 복잡성이 점차 증가하는 것이 일반적입니다. 그에 따라 우선 Flow를 시작한 다음, 필요에 따라 논리 앱으로 변환할 수 있습니다.

다음 표를 통해 Microsoft Flow 또는 Logic Apps가 특정 통합에 가장 적합한지 확인할 수 있습니다.

|  | Microsoft Flow | Logic Apps |
| --- | --- | --- |
| **사용자** |사무실 직원, 비즈니스 사용자, SharePoint 관리자 |전문 통합 업체 및 개발자, IT 전문가 |
| **시나리오** |셀프서비스 |고급 통합 |
| **디자인 도구** |브라우저 및 모바일 앱에서 UI만 해당 |브라우저 내부 및 [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [코드 보기](../logic-apps/logic-apps-author-definitions.md) 사용 가능 |
| **ALM(애플리케이션 수명 주기 관리)** |비 프로덕션 환경에서 디자인 및 테스트하고, 준비되면 프로덕션 환경으로 승격하세요. |Azure DevOps: [Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)의 소스 제어, 테스트, 지원, 자동화 및 관리 효율성 |
| **관리자 환경** |Microsoft Flow 및 DLP(데이터 손실 방지) 정책 관리, 라이선스 추적: [Microsoft Flow 관리 센터](https://admin.flow.microsoft.com) |리소스 그룹, 연결, 액세스 및 로깅 관리: [Azure Portal](https://portal.azure.com) |
| **보안** |Office 365 보안 및 규정 준수 감사 로그, DLP, 중요한 데이터의 [저장 데이터 암호화](https://wikipedia.org/wiki/Data_at_rest#Encryption) 등 |Azure의 보안 보증: [Azure 보안](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Azure Security Center](https://azure.microsoft.com/services/security-center/), [감사 로그](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Azure Functions 및 Azure Logic Apps 비교

Functions 및 Logic Apps는 서버가 없는 워크로드를 활성화하는 Azure 서비스입니다. Azure Functions는 서버리스 컴퓨팅 서비스인 반면 Azure Logic Apps는 서버리스 워크플로를 제공합니다. 둘 다 복합 *오케스트레이션*을 만듭니다. 오케스트레이션은 Logic Apps에서 *작업*이라는 함수 또는 단계의 집합이며 복잡한 작업을 수행하기 위해 실행됩니다. 예를 들어 주문의 일괄 처리를 처리하기 위해 많은 함수의 인스턴스를 동시에 실행하고, 모든 인스턴스가 완료되기를 기다린 다음, 집계에 대한 결과를 컴퓨팅하는 함수를 실행할 수 있습니다.

Azure Functions의 경우 코드를 작성하고 [지속성 함수 확장](durable/durable-functions-overview.md)을 사용하여 오케스트레이션을 개발합니다. Logic Apps의 경우 GUI를 사용하거나 구성 파일을 편집하여 오케스트레이션을 만듭니다.

오케스트레이션을 빌드할 때 논리 앱에서 함수를 호출하고 함수에서 논리 앱을 호출하여 서비스를 짜 맞출 수 있습니다. 서비스의 기능 또는 개인 기본 설정에 따라 각 오케스트레이션을 빌드하는 방법을 선택합니다. 다음 표는 이들 간의 주요 차이점 중 일부를 나열합니다.

|  | 지속성 함수 | Logic Apps |
| --- | --- | --- |
| **개발** | 코드 중심(명령적) | 디자이너 중심(선언적) |
| **연결** | [12가지의 기본 제공 바인딩 형식 정보](functions-triggers-bindings.md#supported-bindings), 사용자 지정 바인딩에 대한 코드 작성 | [대규모의 커넥터 컬렉션](../connectors/apis-list.md), [B2B 시나리오용 엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md), [사용자 지정 커넥터 빌드](../logic-apps/custom-connector-overview.md) |
| **actions** | 각 작업은 Azure 함수입니다. 작업 함수에 대한 코드 작성 |[즉시 사용 가능한 작업의 대규모 컬렉션](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| **Monitoring** | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Azure Monitor 로그](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| **관리** | [REST API](durable/durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [REST API](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| **실행 컨텍스트** | [로컬로](functions-runtime-overview.md) 또는 클라우드에서 실행 가능 | 클라우드에서만 실행|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Functions 및 WebJobs 비교

Azure Functions와 마찬가지로, Azure App Service WebJobs with the WebJobs SDK는 개발자를 위해 설계된 *코드 중심* 통합 서비스입니다. 둘 다 [Azure App Service](../app-service/overview.md)를 기반으로 빌드되며 [소스 제어 통합](../app-service/deploy-continuous-deployment.md), [인증](../app-service/overview-authentication-authorization.md), [Application Insights를 사용한 모니터링](functions-monitoring.md) 등의 기능을 지원합니다.

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs 및 WebJobs SDK

App Service의 *WebJobs* 기능을 사용하여 App Service 웹앱의 컨텍스트에서 스크립트 또는 코드를 실행할 수 있습니다. *WebJobs SDK*는 WebJobs를 위해 설계된 프레임워크로, 개발자가 Azure 서비스의 이벤트에 응답하기 위해 작성하는 코드를 간소화합니다. 예를 들어 썸네일 이미지를 만들어서 Azure Storage에 이미지 Blob 만들기에 대응할 수 있습니다. WebJobs SDK는 WebJob을 배포할 수 있는 .NET 콘솔 애플리케이션으로 실행됩니다. 

WebJobs와 WebJobs SDK는 최고의 호환성을 자랑하지만, WebJobs SDK 없이 WebJobs만 또는 그 반대로 사용할 수도 있습니다. WebJob은 App Service 샌드박스에서 실행되는 모든 프로그램 또는 스크립트를 실행할 수 있습니다. WebJobs SDK 콘솔 애플리케이션은 온-프레미스 서버처럼 콘솔 애플리케이션을 실행하는 모든 위치에서 실행 가능합니다.

### <a name="comparison-table"></a>비교 표

Azure Functions는 WebJobs SDK 기반이므로 다른 Azure 서비스와 여러 동일한 이벤트 트리거 및 연결을 공유합니다. 다음은 Azure Functions와 WebJobs with the WebJobs SDK 중에서 하나를 선택할 때 고려할 요소입니다.

|  | Functions | WebJobs with WebJobs SDK |
| --- | --- | --- |
|[자동 크기 조정](functions-scale.md#how-the-consumption-and-premium-plans-work)이 가능한 [서버 없는 응용 프로그램 모델](https://azure.microsoft.com/solutions/serverless/)|✔||
|[브라우저에서 개발 및 테스트](functions-create-first-azure-function.md) |✔||
|[사용량 기준 과금 가격 책정](functions-scale.md#consumption-plan)|✔||
|[Logic Apps와 통합](functions-twitter-email.md)|✔||
| 트리거 이벤트 |[타이머](functions-bindings-timer.md)<br>[Azure Storage 큐 및 BLOB](functions-bindings-storage-blob.md)<br>[Azure Service Bus 큐 및 토픽](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/웹후크(GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[타이머](functions-bindings-timer.md)<br>[Azure Storage 큐 및 BLOB](functions-bindings-storage-blob.md)<br>[Azure Service Bus 큐 및 토픽](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[파일 시스템](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| 지원되는 언어  |C#<br>F#<br>JavaScript<br>Java<br>Python<br>PowerShell |C#<sup>1</sup>|
|패키지 관리자|NPM 및 NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs(WebJobs SDK 제외)는 C#, Java, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python 등을 지원합니다. 위의 목록은 전체 목록이 아닙니다. WebJob은 App Service 샌드박스에서 실행 가능한 모든 프로그램 또는 스크립트를 실행할 수 있습니다.

<sup>2</sup> WebJobs(WebJobs SDK 제외)는 NPM 및 NuGet을 지원합니다.

### <a name="summary"></a>요약

Azure Functions는 Azure App Service WebJobs보다 개발자 생산성이 우수합니다. 또한 더 많은 프로그래밍 언어, 개발 환경, Azure 서비스 통합 및 가격 책정 옵션을 제공합니다. 대부분의 시나리오에서 가장 좋은 선택입니다.

다음은 WebJobs가 최선의 선택이 될 수 있는 두 가지 시나리오입니다.

* 이벤트, `JobHost` 개체를 수신 대기하는 코드를 보다 정교하게 제어해야 합니다. Functions는 [host.json](functions-host-json.md) 파일의 `JobHost` 동작을 사용자 지정하는 제한된 수의 방법을 제공합니다. 경우에 따라 JSON 파일의 문자열로 지정할 수 없는 작업을 수행해야 합니다. 예를 들어 WebJobs SDK를 통해서만 Azure Storage에 대한 사용자 지정 다시 시도 정책을 구성할 수 있습니다.
* 코드 조각을 실행할 App Service 앱이 있고 동일한 Azure DevOps 환경에서 코드 조각을 함께 관리하려고 합니다.

Azure 또는 타사 서비스를 통합하기 위한 코드 조각을 실행하려는 다른 시나리오의 경우 WebJobs with the WebJobs SDK를 통해 Azure Functions를 선택합니다.

<a name="together"></a>

## <a name="microsoft-flow-logic-apps-functions-and-webjobs-together"></a>Microsoft Flow, Logic Apps, Functions 및 WebJobs를 함께 사용

이러한 서비스 중 하나만 선택할 필요가 없습니다. 이러한 서비스는 서로 통합될 뿐 아니라 외부 서비스와도 통합됩니다.

흐름은 논리 앱을 호출할 수 있습니다. 논리 앱은 함수를 호출할 수 있고, 함수는 논리 앱을 호출할 수 있습니다. 예를 보려면 [Azure Logic Apps와 통합되는 함수 만들기](functions-twitter-email.md)를 참조하세요.

Microsoft Flow, Logic Apps 및 Functions 간의 통합은 계속해서 점차 개선됩니다. 하나의 서비스에서 어떤 기능을 빌드하여 다른 서비스에서 사용할 수 있습니다.

다음 링크를 사용하여 통합 서비스에 대해 자세히 알아볼 수 있습니다.

* [Christopher Anderson의 통합 시나리오에 대한 Azure Functions 및 Azure App Service 활용](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integrations Made Simple by Charles Lamanna](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Logic Apps 라이브 웹캐스트](https://aka.ms/logicappslive)
* [Microsoft Flow 질문과 대답](/power-automate/frequently-asked-questions)

## <a name="next-steps"></a>다음 단계

첫 번째 흐름, 논리 앱 또는 함수 앱을 만들어 시작하세요. 다음 링크 중 하나를 선택합니다.

* [Microsoft Flow 시작](/power-automate/getting-started)
* [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [첫 번째 Azure Function 만들기](functions-create-first-azure-function.md)
