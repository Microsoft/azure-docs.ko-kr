---
title: B2B 엔터프라이즈 통합용 XML 유효성 검사 - Azure Logic Apps | Microsoft Docs
description: 엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 B2B 솔루션용 스키마로 XML 유효성 검사
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.date: 07/08/2016
ms.openlocfilehash: 8db0dbadd944007ff953f9ea69695bf988ffebb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996333"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 B2B 엔터프라이즈 통합용 XML 유효성 검사

종종 B2B 시나리오에서 규약을 맺은 파트너는 데이터 처리를 시작하기 위해 먼저 교환하는 메시지가 유효한지 확인해야 합니다. 엔터프라이즈 통합 팩의 XML 유효성 검사 커넥터를 사용하여 미리 정의된 스키마에 대해 문서의 유효성을 검사할 수 있습니다.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>XML 유효성 검사 커넥터로 문서 유효성 검사

1. 논리 앱을 만들고 XML 데이터의 유효성을 검사하는 데 사용할 스키마를 포함하는 [통합 계정에 앱을 연결](../logic-apps/logic-apps-enterprise-integration-accounts.md "논리 앱에 통합 계정을 연결하는 방법 알아보기")합니다.

2. **요청 - HTTP 요청을 받은 경우** 트리거를 논리 앱에 추가합니다.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1.png)

3. **XML 유효성 검사** 작업을 추가하려면 **작업 추가**를 선택합니다.

4. 사용하려는 논리 앱으로 모든 작업을 필터링하려면 검색 상자에 *xml*을 입력합니다. **XML 유효성 검사**를 선택합니다.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-2.png)

5. 유효성을 검사할 XML 콘텐츠를 지정하려면 **콘텐츠**를 선택합니다.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1-5.png)

6. 본문 태그를 유효성을 검사하려는 콘텐츠로 선택합니다.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-3.png)

7. 이전 *콘텐츠*의 유효성을 검사하는 데 사용할 스키마를 지정하려면 **스키마 이름**을 선택합니다.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-4.png)

8. 작업을 저장합니다.  

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-5.png)

이제 유효성 검사 커넥터 설정이 끝났습니다. 실제 애플리케이션에서는 유효성을 검사한 데이터를 SalesForce와 같은 LOB(기간 업무) 앱에 저장하려고 할 수도 있습니다. 유효성을 검사한 출력을 Salesforce에 보내려면 작업을 추가합니다.

유효성 검사 작업을 테스트하려면 HTTP 엔드포인트에 대해 요청을 수행합니다.

## <a name="next-steps"></a>다음 단계
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")   

