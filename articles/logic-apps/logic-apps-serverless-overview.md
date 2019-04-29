---
title: Azure Serverless 개요 | Microsoft Docs
description: 인프라에 대한 걱정 없이 클라우드에서 강력한 솔루션을 만드는 방법 알아보기
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 9cd1946d8fa670764bcc95b82298d07cc70417bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684111"
---
# <a name="overview-azure-serverless-with-azure-logic-apps-and-azure-functions"></a>개요: Azure Logic Apps 및 Azure Functions를 사용 하 여 서버 리스 azure

[서버리스](https://azure.microsoft.com/solutions/serverless/) 애플리케이션은 개발 속도 향상, 필요한 코드 감소 및 규모 간소화의 이점을 제공합니다.  이 문서는 서버를 사용하지 않는 솔루션과 Azure 서버를 사용하지 않는 제품의 다른 특성을 살펴봅니다.

## <a name="what-is-serverless"></a>서버를 사용하지 않음은 무엇입니까?

서버를 사용하지 않음은 서버가 없음을 의미하지 않습니다. 이는 개발자가 서버에 대한 걱정을 할 필요가 없음을 의미합니다.  기존 애플리케이션 개발의 많은 부분에서 애플리케이션의 요구에 맞는 솔루션의 크기 조정, 호스팅 및 모니터링에 대한 질문에 대답합니다.  서버를 사용하지 않으면 이러한 질문은 솔루션의 일부로 취급됩니다.  또한 서버를 사용하지 않는 애플리케이션은 사용량 기반 계획에 대해 요금이 청구됩니다.  애플리케이션이 사용되지 않는 경우 비용이 발생되지 않습니다.  이 기능을 통해 개발자는 솔루션의 비즈니스 논리에만 온전히 집중할 수 있습니다.

서버를 사용하지 않는 Azure의 핵심 서비스는 [Azure Functions](https://azure.microsoft.com/services/functions/) 및 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)입니다.  이러한 두 솔루션은 위의 원칙을 따르고 개발자가 최소한의 코드로 강력한 클라우드 애플리케이션을 빌드할 수 있도록 합니다.

## <a name="what-are-azure-functions"></a>Azure Functions란?

Azure Functions는 클라우드에서 작은 코드 또는 "함수"를 실행하기 위한 솔루션입니다. 실행할 전체 애플리케이션 또는 인프라를 염려하지 않고 당면한 문제에 필요한 코드를 작성할 수 있습니다. Functions는 개발 생산성을 높일 수 있으며 C#, F#, Node.js, Python, PHP 등의 원하는 개발 언어를 사용할 수 있습니다. 코드를 실행한 시간에 따라 지불하고 Azure는 필요에 따라 크기를 조정합니다.

Azure Functions를 바로 시작하려면 [첫 번째 Azure 함수 만들기](../azure-functions/functions-create-first-azure-function.md)를 시작합니다. Functions에 대한 자세한 기술 정보는 [개발자 참조](../azure-functions/functions-reference.md)를 참조하세요.

## <a name="what-are-azure-logic-apps"></a>Azure Logic Apps란?

Azure Logic Apps는 클라우드에서 확장 가능한 통합 및 워크플로를 단순화하고 구현하는 방법을 제공합니다. 모델에 비주얼 디자이너를 제공하고 프로세스를 워크플로라는 일련의 단계로 자동화합니다.  클라우스 및 온-프레미스 서비스에는 서버를 사용하지 않는 앱을 다른 API에 신속하게 연결하는 [많은 커넥터](../connectors/apis-list.md)가 있습니다.  논리 앱은 ('Dynamics CRM에 계정을 추가하는 경우'와 같이) 트리거로 시작하고 실행 후에 여러 조합 작업, 변환 및 조건 논리 실행을 시작할 수 있습니다.  Logic Apps는 프로세스에서 다른 Azure Functions를 오케스트레이션하는 경우(특히 프로세스가 외부 시스템 또는 API와 상호 작용해야 하는 경우) 탁월한 선택입니다.

Logic Apps를 시작하려면 [첫 번째 논리 앱 만들기](quickstart-create-first-logic-app-workflow.md)로 시작합니다.  Logic Apps에 대한 자세한 기술 정보는 [개발자 참조](logic-apps-workflow-actions-triggers.md)를 참조하세요.

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>Azure에서 서버를 사용하지 않는 애플리케이션을 빌드 및 배포하려면 어떻게 해야 합니까?

Azure는 서버를 사용하지 않는 앱의 개발, 배포 및 관리에 대한 풍부한 도구 집합을 제공합니다.  앱은 Azure Portal에서 직접 빌드하거나 [Visual Studio의 도구](logic-apps-serverless-get-started-vs.md)를 사용하여 빌드할 수 있습니다.  애플리케이션이 개발되면 [즉시 배포](logic-apps-create-deploy-template.md)될 수 있습니다.  Azure는 또한 서버를 사용하지 않는 앱에 대한 모니터링을 제공합니다.  이 모니터링에서 액세스할 수 있습니다 Azure portal, API 또는 Sdk를 통해 또는 통합 된 도구를 사용 하 여 Azure Monitor 로그를 Application Insights.

## <a name="next-steps"></a>다음 단계

* [Visual Studio에서 서버를 사용하지 않는 앱 빌드 시작](logic-apps-serverless-get-started-vs.md)
* [서버를 사용하지 않는 Customer Insights 대시보드 만들기](logic-apps-scenario-social-serverless.md)
* [논리 앱에 대한 배포 템플릿 빌드](logic-apps-create-deploy-template.md)
