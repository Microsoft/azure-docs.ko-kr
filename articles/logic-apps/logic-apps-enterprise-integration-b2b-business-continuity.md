---
title: B2B 통합 계정을 위한 재해 복구 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps에서 지역 간 재해 복구를 위한 준비
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/10/2017
ms.openlocfilehash: 8d024e0bc90724892bc53f8895b270716ad0cefc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001238"
---
# <a name="cross-region-disaster-recovery-for-b2b-integration-accounts-in-azure-logic-apps"></a>Azure Logic Apps에서 B2B 통합 계정을 위한 지역 간 재해 복구

B2B 워크로드에는 주문 및 청구서와 같은 금전 거래가 포함됩니다. 재해 이벤트 동안 기업은 파트너와 계약한 비즈니스 수준 SLA를 충족할 수 있도록 신속하게 복구하는 것이 매우 중요합니다. 이 문서에서는 B2B 워크로드에 대한 비즈니스 연속성 계획을 작성하는 방법을 보여 줍니다. 

* 재해 복구 준비 
* 재해 중 보조 지역으로 장애 조치 
* 재해 이벤트 후 주 지역으로 복귀

## <a name="disaster-recovery-readiness"></a>재해 복구 준비  

1. 보조 지역을 식별하고 보조 지역에 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)을 만듭니다.

2. 실행 상태를 보조 지역 통합 계정으로 복제해야 하는 필수 메시지 흐름에 대한 파트너, 스키마 및 계약을 추가합니다.

   > [!TIP]
   > 모든 지역에서 통합 계정 아티팩트 명명 규칙의 일관성을 유지합니다. 

3. 주 지역에서 실행 상태를 끌어오려면 보조 지역에 논리 앱을 만듭니다. 

   논리 앱에 *트리거* 및 *작업*이 있어야 합니다. 
   트리거는 주 지역 통합 계정에 연결해야 하고 작업은 보조 지역 통합 계정에 연결해야 합니다. 
   시간 간격에 따라 트리거는 주 지역 실행 상태 테이블을 폴링하고 새 레코드를 가져오며(있는 경우) 작업은 새 레코드를 보조 지역 통합 계정에 업데이트합니다. 
   따라서 주 지역에서 보조 지역으로 증분 런타임 상태를 가져올 수 있습니다.

4. Logic Apps 통합 계정의 비즈니스 연속성은 B2B 프로토콜 X12, AS2 및 EDIFACT에 따라 지원하도록 설계되었습니다. 자세한 단계를 알아보려면 해당 링크를 선택합니다.

5. 권장 지침은 보조 지역에도 모든 주 지역 리소스를 배포하는 것입니다. 

   주 지역 리소스에는 Azure SQL Database 또는 Azure Cosmos DB, Azure Service Bus 및 메시징에 사용되는 Azure Event Hubs, Azure API Management, Azure App Service의 Azure Logic Apps 기능이 포함됩니다.   

6. 주 지역에서 보조 지역으로의 연결을 설정합니다. 주 지역에서 실행 상태를 끌어오려면 보조 지역에 논리 앱을 만듭니다. 

   논리 앱에 트리거 및 작업이 있어야 합니다. 
   트리거는 주 지역 통합 계정에 연결해야 합니다. 
   작업은 보조 지역 통합 계정에 연결해야 합니다. 
   시간 간격에 따라 트리거는 주 지역 실행 상태 테이블을 폴링하고 새 레코드를 가져오며(있는 경우) 
   작업은 새 레코드를 보조 지역 통합 계정에 업데이트합니다. 
   이 프로세스는 주 지역에서 보조 지역으로 증분 런타임 상태를 가져오는 데 도움이 됩니다.

Logic Apps 통합 계정의 비즈니스 연속성은 B2B 프로토콜 X12, AS2 및 EDIFACT에 따라 지원을 제공합니다. X12 및 AS2 사용에 대한 자세한 단계는 이 문서의 [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) 및 [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2)를 참조하세요.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>재해 중 보조 지역으로 장애 조치(Failover)

재해 중 주 지역을 비즈니스 연속성에 사용할 수 없는 경우 트래픽을 보조 지역으로 전송합니다. 보조 지역은 기업이 파트너가 합의한 RPO/RTO를 충족시키도록 신속하게 기능을 복구하도록 도와줍니다. 또한 한 지역에서 다른 지역으로 장애 조치(Failover)하는 데 필요한 노력을 최소화합니다. 

주 지역에서 보조 지역으로 컨트롤 번호를 복사하는 동안 예상되는 대기 시간이 있습니다. 재해 이벤트 중에 중복되는 생성된 컨트롤 번호를 파트너에게 보내지 않도록 [PowerShell cmdlet](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery)을 사용하여 보조 지역 계약의 컨트롤 번호를 늘리는 것이 좋습니다.

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>재해 처리 후 주 지역으로 복귀

사용 가능해진 주 지역으로 대체하려면 다음 단계를 따릅니다.

1. 보조 지역의 파트너로부터 메시지 수신을 중지합니다.  

2. [PowerShell cmdlet](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery)을 사용하여 모든 주 지역 계약에 대해 생성된 컨트롤 번호를 늘립니다.  

3. 보조 지역에서 주 지역에 트래픽을 전송합니다.

4. 보조 지역에 생성된 논리 앱이 주 지역의 실행 상태를 가져오도록 설정되었는지 확인합니다.

## <a name="x12"></a>X12 

EDI X12 문서의 비즈니스 연속성은 컨트롤 번호를 기준으로 합니다.

> [!TIP]
> [X12 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/)을 사용하여 Logic Apps를 만들 수도 있습니다. 템플릿을 사용하려면 주 통합 계정 및 보조 통합 계정을 만들어야 합니다. 템플릿은 2개의 Logic Apps, 즉 받은 컨트롤 번호용과 생성된 컨트롤 번호용 Logic Apps를 만드는 데 도움이 됩니다. 각 트리거 및 작업은 Logic Apps에 생성되며, 트리거는 주 통합 계정에, 작업은 보조 통합 계정에 연결됩니다.

**필수 구성 요소**

인바운드 메시지에 대해 재해 복구를 사용하도록 설정하려면 X12 규약 수신 설정에서 중복 확인 설정을 선택합니다.

![중복된 검사 설정 선택](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. 보조 지역에 [논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 만듭니다.    

2. **X12**를 검색하고 **X12 - 컨트롤 번호가 수정되었을 때**를 선택합니다.   

   ![X12 검색](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   트리거는 통합 계정에 대한 연결을 설정하라는 메시지를 표시합니다. 
   트리거는 주 지역 통합 계정에 연결해야 합니다.

3. 연결 이름을 입력하고, 목록에서 *주 지역 통합 계정*을 선택하고, **만들기**를 선택합니다.   

   ![주 지역 통합 계정 이름](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. **컨트롤 번호 동기화를 시작하는 DateTime** 설정은 선택 사항입니다. **빈도** **일**, **시간**, **분** 또는 **초** 간격으로 설정할 수 있습니다.   

   ![날짜/시간 및 빈도](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. **새 단계** > **작업 추가**를 선택합니다.

   ![새 단계 후 작업 추가](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. **X12**를 검색하고 **X12 - 컨트롤 번호 추가 또는 업데이트**를 선택합니다.   

   ![제어 번호를 추가 또는 업데이트](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. 작업을 보조 지역 통합 계정에 연결하려면 사용할 수 있는 통합 계정 목록에 대해 **연결 변경** > **새 연결 추가**를 선택합니다. 연결 이름을 입력하고 목록에서 *보조 지역 통합 계정*을 선택한 후 **만들기**를 선택합니다. 

   ![보조 지역 통합 계정 이름](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. 오른쪽 위 모서리에 있는 아이콘을 클릭하여 원시 입력으로 전환합니다.

   ![원시 입력으로 전환](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. 동적 콘텐츠 선택기에서 본문을 선택하고 논리 앱을 저장합니다.

   ![동적 콘텐츠 필드](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   시간 간격에 따라 트리거는 주 지역 수신 컨트롤 번호 테이블을 폴링하고 새 레코드를 가져옵니다. 
   작업은 레코드를 보조 지역 통합 계정에 업데이트합니다. 
   업데이트가 없는 경우 트리거 상태가 **건너뜀**으로 표시됩니다.   

   ![컨트롤 번호 테이블](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

시간 간격에 따라 증분 런타임 상태가 주 지역에서 보조 지역으로 복제됩니다. 재해 중 주 지역을 사용할 수 없는 경우 비즈니스 연속성을 위해 트래픽을 보조 지역으로 전송합니다. 

## <a name="edifact"></a>EDIFACT 

EDI EDIFACT 문서의 비즈니스 연속성은 컨트롤 번호를 기준으로 합니다.

**필수 구성 요소**

인바운드 메시지에 대해 재해 복구를 사용하도록 설정하려면 EDIFACT 규약 수신 설정에서 중복 확인 설정을 선택합니다.

![중복된 검사 설정 선택](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. 보조 지역에 [논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 만듭니다.    

2. **EDIFACT**를 검색하고 **EDIFACT - 컨트롤 번호가 수정되는 경우**를 선택합니다.

   ![EDIFACT 검색](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   트리거는 통합 계정에 대한 연결을 설정하라는 메시지를 표시합니다. 
   트리거는 주 지역 통합 계정에 연결해야 합니다. 

3. 연결 이름을 입력하고, 목록에서 *주 지역 통합 계정*을 선택하고, **만들기**를 선택합니다.    

   ![주 지역 통합 계정 이름](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. **컨트롤 번호 동기화를 시작하는 DateTime** 설정은 선택 사항입니다. **빈도** **일**, **시간**, **분** 또는 **초** 간격으로 설정할 수 있습니다.    

   ![날짜/시간 및 빈도](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. **새 단계** > **작업 추가**를 선택합니다.    

   ![새 단계 후 작업 추가](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. **EDIFACT**를 검색하고 **EDIFACT - 컨트롤 번호 추가 또는 업데이트**를 선택합니다.   

   ![제어 번호를 추가 또는 업데이트](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. 작업을 보조 지역 통합 계정에 연결하려면 사용할 수 있는 통합 계정 목록에 대해 **연결 변경** > **새 연결 추가**를 선택합니다. 연결 이름을 입력하고 목록에서 *보조 지역 통합 계정*을 선택한 후 **만들기**를 선택합니다.

   ![보조 지역 통합 계정 이름](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. 오른쪽 위 모서리에 있는 아이콘을 클릭하여 원시 입력으로 전환합니다.

   ![원시 입력으로 전환](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. 동적 콘텐츠 선택기에서 본문을 선택하고 논리 앱을 저장합니다.   

   ![동적 콘텐츠 필드](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   시간 간격에 따라 트리거는 주 지역 수신 컨트롤 번호 테이블을 폴링하고 새 레코드를 가져옵니다.
   작업은 레코드를 보조 지역 통합 계정에 업데이트합니다. 
   업데이트가 없는 경우 트리거 상태가 **건너뜀**으로 표시됩니다.

   ![컨트롤 번호 테이블](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

시간 간격에 따라 증분 런타임 상태가 주 지역에서 보조 지역으로 복제됩니다. 재해 중 주 지역을 사용할 수 없는 경우 비즈니스 연속성을 위해 트래픽을 보조 지역으로 전송합니다. 

## <a name="as2"></a>AS2 

AS2 프로토콜을 사용하는 문서에 대한 비즈니스 연속성은 메시지 ID와 MIC 값을 기준으로 합니다.

> [!TIP]
> [AS2 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/pull/3302)을 사용하여 Logic Apps를 만들 수도 있습니다. 템플릿을 사용하려면 주 통합 계정 및 보조 통합 계정을 만들어야 합니다. 이 템플릿은 트리거와 작업이 있는 논리 앱을 만드는 데 도움이 됩니다. 논리 앱은 트리거에서 주 통합 계정으로, 그리고 작업에서 보조 통합 계정으로 연결을 만듭니다.

1. 보조 지역에 [논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 만듭니다.  

2. **AS2**를 검색하고 **AS2 - MIC 값이 만들어질 때**를 선택합니다.   

   ![AS2 검색](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   트리거는 통합 계정에 대한 연결을 설정하라는 메시지를 표시합니다. 
   트리거는 주 지역 통합 계정에 연결해야 합니다. 
   
3. 연결 이름을 입력하고, 목록에서 *주 지역 통합 계정*을 선택하고, **만들기**를 선택합니다.

   ![주 지역 통합 계정 이름](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. **MIC 값 동기화를 시작하는 DateTime** 설정은 선택 사항입니다. **빈도** **일**, **시간**, **분** 또는 **초** 간격으로 설정할 수 있습니다.   

   ![날짜/시간 및 빈도](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. **새 단계** > **작업 추가**를 선택합니다.  

   ![새 단계 후 작업 추가](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. **AS2**를 검색하고 **AS2 - MIC 콘텐츠 추가 또는 업데이트**를 선택합니다.  

   ![MIC 추가 또는 업데이트](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. 작업을 보조 통합 계정에 연결하려면 사용할 수 있는 통합 계정 목록에 대해 **연결 변경** > **새 연결 추가**를 선택합니다. 연결 이름을 입력하고 목록에서 *보조 지역 통합 계정*을 선택한 후 **만들기**를 선택합니다.

   ![보조 지역 통합 계정 이름](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. 오른쪽 위 모서리에 있는 아이콘을 클릭하여 원시 입력으로 전환합니다.

   ![원시 입력으로 전환](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. 동적 콘텐츠 선택기에서 본문을 선택하고 논리 앱을 저장합니다.   

   ![동적 콘텐츠](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   시간 간격에 따라 트리거는 주 지역 테이블을 폴링하고 새 레코드를 가져오며 작업은 새 레코드를 보조 지역 통합 계정에 업데이트합니다. 
   업데이트가 없는 경우 트리거 상태가 **건너뜀**으로 표시됩니다.  

   ![주 지역 테이블](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

시간 간격에 따라 증분 런타임 상태가 주 지역에서 보조 지역으로 복제됩니다. 재해 중 주 지역을 사용할 수 없는 경우 비즈니스 연속성을 위해 트래픽을 보조 지역으로 전송합니다. 

## <a name="next-steps"></a>다음 단계

[B2B 메시지 모니터링](logic-apps-monitor-b2b-message.md)

