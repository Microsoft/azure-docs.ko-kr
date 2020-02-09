---
title: Azure Analysis Services 모델에 대한 Logic Apps를 사용 하 여 새로 고침 | Microsoft Docs
description: 이 문서에서는 Azure Logic Apps를 사용 하 여 Azure Analysis Services에 대한 비동기 새로 고침을 코딩 하는 방법을 설명 합니다.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: a44aa5b355bea675f5d99761d97b8876a9b2a7d7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572334"
---
# <a name="refresh-with-logic-apps"></a>Logic Apps를 사용하여 새로 고침

Logic Apps 및 REST 호출을 사용 하 여 쿼리 확장에 대한 읽기 전용 복제본의 동기화를 포함 하 여 Azure Analysis 테이블 형식 모델에서 자동화 된 데이터 새로 고침 작업을 수행할 수 있습니다.

Azure Analysis Services에서 REST Api를 사용 하는 방법에 대한 자세한 내용은 REST API를 사용 하 여 [비동기 새로 고침](analysis-services-async-refresh.md)을 참조 하세요.

## <a name="authentication"></a>인증

모든 호출은 유효한 Azure Active Directory (OAuth 2) 토큰을 사용 하 여 인증 되어야 합니다.  이 문서의 예제에서는 SPN (서비스 주체)을 사용 하 여 Azure Analysis Services에 인증 합니다. 자세히 알아보려면 [Azure Portal를 사용 하 여 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)를 참조 하세요.

## <a name="design-the-logic-app"></a>논리 앱 디자인

> [!IMPORTANT]
> 다음 예에서는 Azure Analysis Services 방화벽을 사용 하지 않는 것으로 가정 합니다.  방화벽이 사용 되는 경우 요청 개시자의 공용 IP 주소를 Azure Analysis Services 방화벽에서 허용 목록 해야 합니다. 지역별 논리 앱 IP 범위에 대해 자세히 알아보려면 [Azure Logic Apps에 대한 제한 및 구성 정보](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses)를 참조 하세요.

### <a name="prerequisites"></a>필수 조건

#### <a name="create-a-service-principal-spn"></a>SPN (서비스 사용자) 만들기

서비스 주체를 만드는 방법에 대한 자세한 내용은 [Azure Portal를 사용 하 여 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)를 참조 하세요.

#### <a name="configure-permissions-in-azure-analysis-services"></a>Azure Analysis Services에서 사용 권한 구성
 
사용자가 만드는 서비스 주체에 서버에 대한 서버 관리자 권한이 있어야 합니다. 자세히 알아보려면 [서버 관리자 역할에 서비스 사용자 추가](analysis-services-addservprinc-admins.md)를 참조하세요.

### <a name="configure-the-logic-app"></a>논리 앱 구성

이 예제에서 논리 앱은 HTTP 요청을 받을 때 트리거되도록 설계 되었습니다. 이렇게 하면 Azure Data Factory와 같은 오케스트레이션 도구를 사용 하 여 Azure Analysis Services 모델 새로 고침을 트리거할 수 있습니다.

논리 앱을 만든 후에는 다음을 수행 합니다.

1. 논리 앱 디자이너에서 **HTTP 요청을 받을 때**의 첫 번째 작업을 선택 합니다.

   ![HTTP 수신 작업 추가](./media/analysis-services-async-refresh-logic-app/1.png)

이 단계는 논리 앱이 저장 된 후 HTTP POST URL로 채워집니다.

2. 새 단계를 추가 하 고 **HTTP**를 검색 합니다.  

   ![HTTP 작업 추가](./media/analysis-services-async-refresh-logic-app/9.png)

   ![HTTP 작업 추가](./media/analysis-services-async-refresh-logic-app/10.png)

3. **HTTP** 를 선택 하 여이 작업을 추가 합니다.

   ![HTTP 작업 추가](./media/analysis-services-async-refresh-logic-app/2.png)

HTTP 작업을 다음과 같이 구성 합니다.

|속성  |값  |
|---------|---------|
|**메서드**     |POST         |
|**URI**     | *서버 지역*/servers/https://*서버 이름*/models/*데이터베이스 이름*/새로 고침 <br /> <br /> 예: https: \//westus/서버/p s/모델/AdventureWorks/새로 고침|
|**헤더**     |   Content-type, application/json <br /> <br />  ![헤더](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**본문**     |   요청 본문을 형성 하는 방법에 대한 자세한 내용은 [REST API-사후 게시/새로 고침을 사용 하 여 비동기 새로 고침](analysis-services-async-refresh.md#post-refreshes)을 참조 하세요. |
|**인증**     |Active Directory OAuth         |
|**넌**     |Azure Active Directory TenantId를 입력 합니다.         |
|**대상**     |https://* .casoms. net         |
|**클라이언트 ID**     |서비스 사용자 이름 ClientID 입력         |
|**자격 증명 유형**     |Secret         |
|**비밀**     |서비스 사용자 이름 암호를 입력 하세요.         |

예:

![완료 된 HTTP 작업](./media/analysis-services-async-refresh-logic-app/7.png)

이제 논리 앱을 테스트 합니다.  논리 앱 디자이너에서 **실행**을 클릭 합니다.

![논리 앱 테스트](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Azure Data Factory에서 논리 앱 사용

논리 앱을 저장 한 후에는 **http 요청이 수신 되 면** 작업을 검토 한 다음 이제 생성 된 **http POST URL** 을 복사 합니다.  이 URL은 Azure Data Factory에서 논리 앱을 트리거하는 비동기 호출을 수행 하는 데 사용할 수 있는 URL입니다.

이 작업을 수행 하는 웹 활동 Azure Data Factory 예제는 다음과 같습니다.

![웹 작업 Data Factory](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>자체 포함 된 논리 앱 사용

Data Factory 등의 오케스트레이션 도구를 사용 하 여 모델 새로 고침을 트리거하는 것을 계획 하지 않은 경우 일정에 따라 새로 고침을 트리거하기 위해 논리 앱을 설정할 수 있습니다.

위의 예제를 사용 하 여 첫 번째 활동을 삭제 하 고 **일정** 활동으로 바꿉니다.

![작업 예약](./media/analysis-services-async-refresh-logic-app/12.png)

![작업 예약](./media/analysis-services-async-refresh-logic-app/13.png)

이 예에서는 **되풀이**를 사용 합니다.

활동이 추가 되 면 간격 및 빈도를 구성한 다음 새 매개 변수를 추가 하 고 **이러한 시간에**를 선택 합니다.

![작업 예약](./media/analysis-services-async-refresh-logic-app/16.png)

원하는 시간을 선택 합니다.

![작업 예약](./media/analysis-services-async-refresh-logic-app/15.png)

논리 앱을 저장 합니다.

## <a name="next-steps"></a>다음 단계

[샘플](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
