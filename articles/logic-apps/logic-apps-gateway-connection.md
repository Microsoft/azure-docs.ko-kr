---
title: 온-프레미스 데이터 원본 액세스
description: Azure에서 데이터 게이트웨이 리소스를 만들어 Azure Logic Apps에서 온-프레미스 데이터 원본에 연결
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, divswa, logicappspm
ms.topic: article
ms.date: 08/18/2020
ms.openlocfilehash: 2dd086ccc45458299cf6b8a7ad83d023055c96ae
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661200"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Azure Logic Apps에서 온-프레미스 데이터 원본에 연결

[로컬 컴퓨터에 온 *-프레미스 데이터 게이트웨이* 를 설치](../logic-apps/logic-apps-gateway-install.md) 하 고 논리 앱에서 온-프레미스의 데이터 원본에 액세스 하려면 먼저 게이트웨이 설치를 위해 Azure에서 게이트웨이 리소스를 만들어야 합니다. 그런 다음 Azure Logic Apps에서 사용할 수 있는 [온-프레미스 커넥터](../connectors/apis-list.md#on-premises-connectors) 에 사용 하려는 트리거와 작업에서이 게이트웨이 리소스를 선택할 수 있습니다. Azure Logic Apps는 데이터 게이트웨이를 통해 읽기 및 쓰기 작업을 지원 합니다. 그러나 이러한 작업에는 [페이로드 크기 제한](/data-integration/gateway/service-gateway-onprem#considerations)이 있습니다.

이 문서에서는 [로컬 컴퓨터에](../logic-apps/logic-apps-gateway-install.md)이전에 설치 된 게이트웨이에 대 한 Azure 게이트웨이 리소스를 만드는 방법을 보여 줍니다. 게이트웨이에 대 한 자세한 내용은 [게이트웨이의 작동](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)원리를 참조 하세요.

> [!TIP]
> 게이트웨이를 사용 하지 않고 Azure 가상 네트워크의 온-프레미스 리소스에 직접 액세스 하려면 [*통합 서비스 환경을*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 대신 만드는 것이 좋습니다. 

다른 서비스에서 게이트웨이를 사용하는 방법에 대한 내용은 다음 문서를 참조하세요.

* [Microsoft Power Automate 온-프레미스 데이터 게이트웨이](/power-automate/gateway-reference)
* [Microsoft Power BI 온-프레미스 데이터 게이트웨이](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps 온-프레미스 데이터 게이트웨이](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services 온-프레미스 데이터 게이트웨이](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>지원되는 데이터 원본

Azure Logic Apps 온-프레미스 데이터 게이트웨이는 이러한 데이터 원본에 대 한 [온-프레미스 커넥터](../connectors/apis-list.md#on-premises-connectors) 를 지원 합니다.

* BizTalk Server 2016
* 파일 시스템
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle 데이터베이스
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

REST 또는 SOAP를 사용 하 여 HTTP 또는 HTTPS를 통해 데이터 원본에 연결 하는 [사용자 지정 커넥터](../logic-apps/custom-connector-overview.md) 를 만들 수도 있습니다. 게이트웨이 자체는 추가 비용이 발생 하지 않지만 [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md) 은 Azure Logic Apps의 이러한 커넥터 및 기타 작업에 적용 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

* 이미 [로컬 컴퓨터에 온-프레미스 데이터 게이트웨이를 설치](../logic-apps/logic-apps-gateway-install.md)했습니다. 이 게이트웨이 설치는이 설치에 연결 되는 게이트웨이 리소스를 만들기 전에 존재 해야 합니다.

* 게이트웨이 설치에 사용한 것 [과 동일한 Azure 계정 및 구독이](../logic-apps/logic-apps-gateway-install.md#requirements) 있습니다. 이 Azure 계정은 단일 [Azure Active Directory (AZURE AD) 테 넌 트 또는 디렉터리](../active-directory/fundamentals/active-directory-whatis.md#terminology)에만 속해야 합니다. 게이트웨이 관리자만 Azure에서 게이트웨이 리소스를 만들 수 있기 때문에 Azure에서 게이트웨이 리소스를 만들려면 동일한 Azure 계정 및 구독을 사용 해야 합니다. 서비스 사용자는 현재 지원 되지 않습니다.

  * Azure에서 게이트웨이 리소스를 만들 때 게이트웨이 설치를 선택 하 여 게이트웨이 리소스 및 해당 게이트웨이 리소스에만 연결 합니다. 각 게이트웨이 리소스는 하나의 게이트웨이 설치에만 연결할 수 있습니다. 다른 게이트웨이 리소스와 이미 연결 되어 있는 게이트웨이 설치는 선택할 수 없습니다.
  
  * 논리 앱 및 게이트웨이 리소스는 동일한 Azure 구독에 존재 하지 않아도 됩니다. 구독 액세스 권한이 있는 경우 온-프레미스 데이터 원본에 액세스할 수 있는 트리거 및 작업에서 게이트웨이 리소스가 있는 다른 Azure 구독을 선택할 수 있습니다.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Azure 게이트웨이 리소스 만들기

로컬 컴퓨터에 게이트웨이를 설치한 후 게이트웨이에 대 한 Azure 리소스를 만듭니다.

1. 게이트웨이를 설치 하는 데 사용 된 것과 동일한 Azure 계정으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.

1. Azure Portal 검색 상자에 "온-프레미스 데이터 게이트웨이"를 입력 하 고 **온-프레미스 데이터**게이트웨이를 선택 합니다.

   !["온-프레미스 데이터 게이트웨이" 찾기](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. **온-프레미스 데이터 게이트웨이**아래에서 **추가**를 선택 합니다.

   ![데이터 게이트웨이에 대 한 새 Azure 리소스 추가](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. **연결 게이트웨이 만들기**에서 게이트웨이 리소스에 대해이 정보를 제공 합니다. 완료되면 **만들기**를 선택합니다.

   | 속성 | Description |
   |----------|-------------|
   | **리소스 이름** | 문자, 숫자, 하이픈 ( `-` ), 밑줄 ( `_` ), 괄호 ( `(` , `)` ) 또는 마침표 ()만 포함 하는 게이트웨이 리소스의 이름을 제공 `.` 합니다. |
   | **구독** | 게이트웨이 설치에 사용 된 Azure 계정에 대 한 Azure 구독을 선택 합니다. 기본 구독은 로그인하는 데 사용한 Azure 계정을 기반으로 합니다. |
   | **리소스 그룹** | 사용 하려는 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md) |
   | **위치** | [게이트웨이를 설치](../logic-apps/logic-apps-gateway-install.md)하는 동안 게이트웨이 클라우드 서비스에 대해 선택한 것과 동일한 지역 또는 위치입니다. 그렇지 않으면 게이트웨이 설치가 **설치 이름** 목록에 표시 되지 않습니다. 논리 앱 위치는 게이트웨이 리소스 위치와 다를 수 있습니다. |
   | **설치 이름** | 이러한 조건이 충족 될 때만 목록에 표시 되는 게이트웨이 설치를 선택 합니다. <p><p>-게이트웨이 설치는 만들려는 게이트웨이 리소스와 동일한 지역을 사용 합니다. <br>-게이트웨이 설치는 다른 Azure 게이트웨이 리소스에 연결 되지 않습니다. <br>-게이트웨이 설치는 게이트웨이 리소스를 만드는 데 사용 하는 것과 동일한 Azure 계정에 연결 됩니다. <br>-Azure 계정은 단일 [Azure Active Directory (AZURE AD) 테 넌 트 또는 디렉터리](../active-directory/fundamentals/active-directory-whatis.md#terminology) 에 속하며 게이트웨이 설치에 사용한 것과 동일한 계정입니다. <p><p>자세한 내용은 질문과 [대답](#faq) 섹션을 참조 하세요. |
   |||

   다음은 게이트웨이 리소스와 동일한 지역에 있고 동일한 Azure 계정에 연결 된 게이트웨이 설치를 보여 주는 예제입니다.

   ![데이터 게이트웨이 리소스 만들기에 대 한 세부 정보 제공](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>온-프레미스 데이터 연결

게이트웨이 리소스를 만들고 이 리소스에 Azure 구독을 연결한 후에는 해당 게이트웨이 사용하여 논리 앱과 온-프레미스 데이터 원본 간에 연결을 만들 수 있습니다.

1. Azure Portal의 논리 앱 디자이너에서 논리 앱을 만들거나 엽니다.

1. 온-프레미스 연결을 지원하는 **SQL Server**와 같은 커넥터를 추가합니다.

1. **온-프레미스 데이터 게이트웨이를 통해 연결**을 선택합니다.

1. 게이트웨이 **의** **구독** 목록에서 원하는 게이트웨이 리소스가 있는 Azure 구독을 선택 합니다.

   구독 액세스 권한이 있는 경우 각각 다른 게이트웨이 리소스와 연결 된 다양 한 Azure 구독에서 선택할 수 있습니다. 논리 앱 및 게이트웨이 리소스는 동일한 Azure 구독에 존재 하지 않아도 됩니다.

1. 선택한 구독에서 사용 가능한 게이트웨이 리소스를 보여 주는 **연결 게이트웨이** 목록에서 원하는 게이트웨이 리소스를 선택 합니다. 각 게이트웨이 리소스는 단일 게이트웨이 설치에 연결 됩니다.

   > [!NOTE]
   > 논리 앱의 위치는 게이트웨이 리소스의 위치와 다를 수 있기 때문에 게이트웨이 목록에는 다른 지역의 게이트웨이 리소스가 포함 되어 있습니다. 

1. 만들려는 연결에 따라 고유한 연결 이름 및 기타 필요한 정보를 제공 합니다.

   고유한 연결 이름을 사용 하면 나중에 여러 연결을 만드는 경우 해당 연결을 쉽게 찾을 수 있습니다. 해당하는 경우 사용자 이름의 정규화된 도메인도 포함됩니다.

   예를 들면 다음과 같습니다.

   ![논리 앱과 데이터 게이트웨이 간에 연결 만들기](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. 완료되면 **만들기**를 선택합니다.

게이트웨이 연결에 논리 앱을 사용할 준비가 되었습니다.

## <a name="edit-connection"></a>연결 편집

게이트웨이 연결에 대 한 설정을 업데이트 하려면 연결을 편집할 수 있습니다.

1. 논리 앱에 대 한 모든 API 연결을 찾으려면 논리 앱의 메뉴에 있는 **개발 도구**아래에서 **api 연결**을 선택 합니다.

   ![논리 앱 메뉴에서 "API 연결"을 선택 합니다.](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. 원하는 게이트웨이 연결을 선택 하 고 **API 연결 편집**을 선택 합니다.

   > [!TIP]
   > 업데이트가 적용 되지 않는 경우 게이트웨이 설치를 위해 [게이트웨이 Windows 서비스 계정을 중지 하 고 다시 시작](../logic-apps/logic-apps-gateway-install.md#restart-gateway) 해 보세요.

Azure 구독에 연결된 모든 API 연결을 찾으려면:

* Azure Portal 메뉴에서 **모든 서비스**  >  **웹**  >  **API 연결**을 선택 합니다.
* 또는 Azure Portal 메뉴에서 **모든 리소스**를 선택 합니다. **유형** 필터를 **API 연결**로 설정 합니다.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>게이트웨이 리소스 삭제

다른 게이트웨이 리소스를 만들거나, 게이트웨이 설치를 다른 게이트웨이 리소스에 연결 하거나, 게이트웨이 리소스를 제거 하려면 게이트웨이 설치에 영향을 주지 않고 게이트웨이 리소스를 삭제할 수 있습니다.

1. Azure Portal 메뉴에서 **모든 리소스**를 선택 하거나 모든 페이지에서 **모든 리소스** 를 검색 하 고 선택 합니다. 게이트웨이 리소스를 찾아 선택합니다.

1. 게이트웨이 리소스 메뉴에서 아직 선택하지 않은 경우 **온-프레미스 데이터 게이트웨이**를 선택합니다. 게이트웨이 리소스 도구 모음에서 **삭제**를 선택 합니다.

   예를 들면 다음과 같습니다.

   ![Azure에서 게이트웨이 리소스 삭제](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>질문과 대답

**Q**: Azure에서 게이트웨이 리소스를 만들 때 게이트웨이 설치가 나타나지 않는 이유는 무엇 인가요? <br/>
**A**: 이 문제는 다음과 같은 이유 때문에 발생할 수 있습니다.

* Azure 계정은 로컬 컴퓨터의 게이트웨이 설치에 사용한 것과 동일한 계정이 아닙니다. 게이트웨이 설치에 사용한 것과 동일한 id를 사용 하 여 Azure Portal에 로그인 했는지 확인 합니다. 게이트웨이 관리자만 Azure에서 게이트웨이 리소스를 만들 수 있습니다. 서비스 사용자는 현재 지원 되지 않습니다.

* Azure 계정은 단일 [AZURE AD 테 넌 트 또는 디렉터리](../active-directory/fundamentals/active-directory-whatis.md#terminology)에만 속해 있지 않습니다. 게이트웨이 설치 중에 사용한 것과 동일한 Azure AD 테 넌 트 또는 디렉터리를 사용 하 고 있는지 확인 합니다.

* 게이트웨이 리소스와 게이트웨이 설치는 동일한 지역에 존재 하지 않습니다. 그러나 논리 앱의 위치는 게이트웨이 리소스 위치와 다를 수 있습니다.

* 게이트웨이 설치는 이미 다른 게이트웨이 리소스와 연결 되어 있습니다. 각 게이트웨이 리소스는 하나의 게이트웨이 설치에만 연결할 수 있으며 Azure 계정 및 구독 하나에만 연결할 수 있습니다. 따라서 다른 게이트웨이 리소스와 이미 연결 되어 있는 게이트웨이 설치를 선택할 수 없습니다. 이러한 설치는 **설치 이름** 목록에 표시 되지 않습니다.

  Azure Portal에서 게이트웨이 등록을 검토 하려면 *모든* azure 구독에서 **온-프레미스 데이터 게이트웨이** 리소스 종류를 포함 하는 모든 azure 리소스를 찾습니다. 다른 게이트웨이 리소스에서 게이트웨이 설치의 연결을 끊으려면 [게이트웨이 리소스 삭제](#change-delete-gateway-resource)를 참조 하세요.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>다음 단계

* [논리 앱 보안](./logic-apps-securing-a-logic-app.md)
* [논리 앱에 대한 일반적인 예제 및 시나리오](./logic-apps-examples-and-scenarios.md)
