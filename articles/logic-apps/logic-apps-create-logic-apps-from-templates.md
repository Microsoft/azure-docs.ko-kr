---
title: 템플릿으로 워크플로 만들기 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps에서 논리 앱 템플릿을 사용하여 신속하게 워크플로 빌드
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.date: 10/15/2017
ms.openlocfilehash: 134a8f9625b45a8196ebd47f10286093f6ba0d46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459698"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>미리 빌드된 템플릿에서 논리 앱 워크플로 만들기

워크플로를 보다 신속하게 만들 수 있도록 하기 위해 Logic Apps는 일반적으로 사용되는 패턴에 따라 미리 만들어진 논리 앱인 템플릿을 제공합니다. 제공된 이러한 템플릿을 사용하거나 사용자 시나리오에 맞게 편집합니다.

몇 가지 템플릿 범주는 다음과 같습니다.

| 템플릿 형식 | 설명 | 
| ------------- | ----------- | 
| 엔터프라이즈 클라우드 템플릿 | Azure Blob, Dynamics CRM, Salesforce, Box 및 엔터프라이즈 클라우드 요구 사항에 대한 다른 커넥터를 포함하여 통합하려는 경우입니다. 예를 들어 이러한 템플릿을 사용하여 비즈니스 잠재 고객을 구성하거나 회사 파일 데이터를 백업할 수 있습니다. | 
| 개인 생산성 템플릿 | 매일 미리 알림을 설정하고, 중요한 작업 항목을 할 일 목록으로 설정하고, 단일 사용자 승인 단계까지 시간이 걸리는 작업을 자동화하여 개인 생산성을 향상시킵니다. | 
| 소비자 클라우드 템플릿 | Twitter 같은 소셜 미디어 서비스, Slack 및 메일을 통합하려는 경우입니다. 소셜 미디어 마케팅 이니셔티브를 강화하는 데 유용합니다. 이러한 템플릿은 클라우드 복사와 같은 작업을 포함하며 일반적으로 반복 작업에 소요된 시간을 절약하여 생산성을 높일 수 있습니다. | 
| 엔터프라이즈 통합 팩 템플릿 | VETER(유효성 검사, 추출, 변환, 보강, 라우팅) 파이프라인을 구성하고, AS2를 통한 X12 EDI 문서 수신과 XML로 변환하고, X12, EDIFACT와 AS2 메시지를 처리하는 경우입니다. | 
| 프로토콜 패턴 템플릿 | HTTP에서 요청-응답 및 FTP와 SFTP에서 통합과 같은 프로토콜 패턴을 구현하는 경우입니다. 제공된 대로 이러한 템플릿을 사용하거나 복잡한 프로토콜 패턴에 대해 빌드합니다. | 
||| 

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정에 등록합니다](https://azure.microsoft.com/free/). 논리 앱을 빌드하는 방법에 대한 자세한 내용은 [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="create-logic-apps-from-templates"></a>템플릿에서 논리 앱 만들기

1. 아직 로그인하지 않은 경우 [Azure Portal](https://portal.azure.com "Azure Portal")에 로그인합니다.

2. Azure 주 메뉴에서 **리소스 만들기** > **엔터프라이즈 통합** > **논리 앱**을 선택합니다.

   ![Azure Portal, New, 엔터프라이즈 통합, Logic App](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. 이 이미지의 테이블에서 설정을 사용하여 논리 앱을 만듭니다.

   ![논리 앱 세부 정보 제공](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | 설정 | 값 | 설명 | 
   | ------- | ----- | ----------- | 
   | **Name** | *your-logic-app-name* | 고유한 논리 앱 이름을 제공합니다. | 
   | **구독** | *your-Azure-subscription-name* | 사용할 Azure 구독을 선택합니다. | 
   | **리소스 그룹** | *your-Azure-resource-group-name* | 이 논리 앱에 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만들거나 선택하고 이 앱과 연결된 모든 리소스를 구성합니다. | 
   | **위치**: | *your-Azure-datacenter-region* | 논리 앱을 배포하기 위한 데이터 센터 지역을 선택합니다(예: 미국 서부). | 
   | **Log Analytics** | **끄기**(기본값) 또는 **켜기** | 켜기 [진단 로깅](../logic-apps/logic-apps-monitor-your-logic-apps.md#turn-on-diagnostics-logging-for-your-logic-app) 를 통해 논리 앱에 대 한 [Azure Monitor 로그](../log-analytics/log-analytics-overview.md)합니다. Log Analytics 작업 영역이 이미 있어야 합니다. | 
   |||| 

4. 준비가 되면 **대시보드에 고정**을 선택합니다. 이런 방식으로 논리 앱은 자동으로 Azure 대시보드에 표시되고 배포 후에 열립니다. **만들기**를 선택합니다.

   > [!NOTE]
   > 논리 앱을 고정하지 않으려면 계속할 수 있도록 배포 후에 논리 앱을 수동으로 찾고 열어야 합니다.

   Azure가 논리 앱을 배포한 후에 Logic Apps 디자이너가 열리고 소개 비디오가 포함된 페이지를 보여줍니다. 
   비디오 아래에서 공통 논리 앱 패턴의 템플릿을 찾을 수 있습니다. 

5. **템플릿**에 대한 소개 비디오 및 공통 트리거는 넘어갑니다. 미리 빌드된 템플릿을 선택합니다. 예를 들면 다음과 같습니다.

   ![논리 앱 템플릿을 선택합니다.](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > 처음부터 논리 앱을 빌드하려면 **빈 Logic App**을 선택합니다.

   미리 빌드된 템플릿을 선택하면 해당 템플릿에 대한 자세한 정보를 볼 수 있습니다. 
   예를 들면 다음과 같습니다.

   ![미리 빌드된 템플릿 선택](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. 선택한 템플릿을 계속하려면 **이 템플릿 사용**을 선택합니다. 

7. 템플릿에 있는 커넥터에 따라 다음 단계 중 하나를 수행하라는 메시지가 표시됩니다.

   * 자격 증명을 사용하여 템플릿에서 참조하는 시스템 또는 서비스에 로그인합니다.

   * 템플릿에서 참조하는 서비스 또는 시스템에 대한 연결을 만듭니다. 연결하려면 연결에 이름을 지정하고 필요한 경우 사용하려는 리소스를 선택합니다. 

   * 이러한 연결을 이미 설정한 경우 **계속**을 선택합니다.

   예를 들면 다음과 같습니다.

   ![연결 만들기](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   작업이 완료되면 논리 앱이 열리고 Logic Apps 디자이너에 표시됩니다.

   > [!TIP]
   > 템플릿 뷰어로 돌아가려면 디자이너 도구 모음에서 **템플릿**을 선택합니다. 이 작업은 저장되지 않은 변경 내용을 삭제하므로 요청을 확인하라는 경고 메시지가 표시됩니다.

8. 논리 앱을 계속 빌드합니다.

   > [!NOTE] 
   > 많은 템플릿에는 필수 속성을 미리 채울 수 있는 커넥터가 포함됩니다. 그러나 일부 템플릿에서는 여전히 논리 앱을 올바르게 배포하기 전에 값을 제공해야 합니다. 누락된 속성 필드를 완료하지 않고 배포하려고 하면 오류 메시지가 발생합니다. 

## <a name="update-logic-apps-with-templates"></a>템플릿으로 논리 앱 업데이트

1. [Azure Portal](https://portal.azure.com "Azure Portal")의 Logic App 디자이너에서 논리 앱을 찾아서 엽니다.

2. 디자이너 도구 모음에서 **템플릿**을 선택합니다. 이 작업은 저장되지 않은 변경 내용을 삭제하므로 계속하려는지 확인할 수 있도록 경고 메시지가 표시됩니다. 확인하려면 **확인**을 선택합니다. 예를 들면 다음과 같습니다.

   !["템플릿" 선택](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. **템플릿**에 대한 소개 비디오 및 공통 트리거는 넘어갑니다. 미리 빌드된 템플릿을 선택합니다. 예를 들면 다음과 같습니다.

   ![논리 앱 템플릿을 선택합니다.](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   미리 빌드된 템플릿을 선택하면 해당 템플릿에 대한 자세한 정보를 볼 수 있습니다. 
   예를 들면 다음과 같습니다.

   ![미리 빌드된 템플릿 선택](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. 선택한 템플릿을 계속하려면 **이 템플릿 사용**을 선택합니다. 

5. 템플릿에 있는 커넥터에 따라 다음 단계 중 하나를 수행하라는 메시지가 표시됩니다.

   * 자격 증명을 사용하여 템플릿에서 참조하는 시스템 또는 서비스에 로그인합니다.

   * 템플릿에서 참조하는 서비스 또는 시스템에 대한 연결을 만듭니다. 연결하려면 연결에 이름을 지정하고 필요한 경우 사용하려는 리소스를 선택합니다. 

   * 이러한 연결을 이미 설정한 경우 **계속**을 선택합니다.

   ![연결 만들기](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   이제 논리 앱이 열리고 Logic Apps 디자이너에 표시됩니다.

8. 논리 앱을 계속 빌드합니다. 

   > [!TIP]
   > 변경 내용을 저장하지 않은 경우 작업을 삭제하고 이전 논리 앱에 반환할 수 있습니다. 디자이너 도구 모음에서 **삭제**를 선택합니다.

> [!NOTE] 
> 많은 템플릿에는 필수 속성을 미리 채울 수 있는 커넥터가 포함됩니다. 그러나 일부 템플릿에서는 여전히 논리 앱을 올바르게 배포하기 전에 값을 제공해야 합니다. 누락된 속성 필드를 완료하지 않고 배포하려고 하면 오류 메시지가 발생합니다.

## <a name="deploy-logic-apps-built-from-templates"></a>템플릿에서 빌드한 논리 앱 배포

템플릿을 변경한 후에 변경 내용을 저장할 수 있습니다. 또한 이 작업은 논리 앱을 자동으로 게시합니다.

디자이너 도구 모음에서 **저장**을 선택합니다.

![논리 앱 저장 및 게시](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

예제, 시나리오, 고객 사례 및 연습을 통해 논리 앱을 빌드하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [논리 앱 예제, 시나리오 및 연습 검토](../logic-apps/logic-apps-examples-and-scenarios.md)