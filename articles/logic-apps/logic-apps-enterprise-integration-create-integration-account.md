---
title: B2B 솔루션에 대한 통합 계정 만들기 및 관리 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps를 사용하여 엔터프라이즈 통합 및 B2B 솔루션에 대한 통합 계정을 생성, 연결, 이동 및 삭제합니다.
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: 43ecdafac4f0a5cdc9e619537cdbe2a42ff7fe1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60999614"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>논리 앱을 사용하여 B2B 솔루션에 대한 통합 계정 만들기 및 관리

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용하여 [엔터프라이즈 통합 및 B2B 솔루션](../logic-apps/logic-apps-enterprise-integration-overview.md)을 구축하려면 거래 업체, 규약, 맵, 스키마, 인증서 등과 같은 B2B 아티팩트를 생성, 저장 및 관리하는 통합 계정이 있어야 합니다. 논리 앱이 통합 계정에서 아티팩트를 사용하고 XML 유효성 검사와 같은 Logic Apps B2B 커넥터를 사용하려면 먼저 논리 앱에 [통합 계정을 연결](#link-account)해야 합니다. 이렇게 연결하려면 통합 계정과 논리 앱 모두가 *동일한* Azure 위치 또는 지역이 있어야 합니다.

이 문서에서는 다음 작업을 수행하는 방법에 대해 설명합니다.

* 통합 계정을 만듭니다.
* 통합 계정을 논리 앱에 연결합니다.
* 통합 계정을 다른 Azure 리소스 그룹 또는 구독으로 이동합니다.
* 통합 계정을 삭제합니다.

Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

Azure 계정 자격 증명을 사용하여 <a href="https://portal.azure.com" target="_blank">Azure Portal</a>에 로그인합니다.

## <a name="create-integration-account"></a>통합 계정 만들기

1. Azure의 주 메뉴에서 **모든 서비스**를 선택합니다. 검색 상자에서 필터로 "통합 계정"을 입력하고 **통합 계정**을 선택합니다.

   ![통합 계정 찾기](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. **통합 계정** 아래에서 **추가**를 선택합니다.

   ![통합 계정을 만들기 위한 "추가" 선택](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. 통합 계정에 대한 정보를 제공합니다. 

   ![통합 계정의 세부 정보를 제공합니다.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | 자산 | 필수 | 예제 값 | 설명 | 
   |----------|----------|---------------|-------------|
   | Name | 예 | test-integration-account | 통합 계정의 이름입니다. 여기서는 지정된 이름을 사용합니다. | 
   | 구독 | 예 | <*Azure-subscription-name*> | 사용할 Azure 구독의 이름입니다. | 
   | 리소스 그룹 | 예 | test-integration-account-rg | 관련 리소스를 구성하는 데 사용되는 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)의 이름입니다. 여기서는 지정된 이름으로 새 리소스 그룹을 만듭니다. | 
   | 가격 책정 계층 | 예 | 무료 | 사용하려는 가격 책정 계층입니다. 여기서는 **체험**을 선택하지만, 자세한 내용은 [Logic Apps 제한 및 구성](../logic-apps/logic-apps-limits-and-config.md) 및 [Logic Apps 가격](https://azure.microsoft.com/pricing/details/logic-apps/)을 참조하세요. | 
   | 위치 | 예 | 미국 서부 | 통합 계정 정보를 저장할 지역입니다. 논리 앱과 동일한 위치를 선택하거나 통합 계정과 동일한 위치에 논리 앱을 만듭니다. | 
   | Log Analytics 작업 영역 | 아닙니다. | 꺼짐 | 진단 로깅에 대한 설정을 **끄기**로 유지합니다. | 
   ||||| 

4. 준비가 되면 **대시보드에 고정**을 선택하고 **만들기**를 선택합니다.

   Azure에서 통합 계정을 선택한 위치에 배포하면 일반적으로 1분 내에 완료되며 Azure에서 통합 계정이 열립니다.

   ![Azure에서 열린 통합 계정](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

이제 논리 앱에서 통합 계정을 사용하려면 먼저 통합 계정을 논리 앱에 연결해야 합니다.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>논리 앱에 연결

거래 업체, 규약, 맵 및 스키마와 같은 B2B 아티팩트가 포함된 통합 계정에 대한 액세스 권한을 논리 앱에 부여하려면 통합 계정을 논리 앱에 연결해야 합니다. 

> [!NOTE]
> 통합 계정과 논리 앱은 동일한 지역에 있어야 합니다.

1. Azure Portal에서 논리 앱을 찾아서 엽니다.

2. 논리 앱의 메뉴에 있는 **설정** 아래에서 **워크플로 설정**을 선택합니다. **통합 계정 선택** 목록에서 논리 앱에 연결하려는 통합 계정을 선택합니다.

   ![통합 계정 선택](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. 연결을 완료하려면 **저장**을 선택합니다.

   ![통합 계정 선택](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   통합 계정이 성공적으로 연결되면 Azure에서 확인 메시지가 표시됩니다. 

   ![Azure에서 성공적인 연결 확인](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

이제 논리 앱에서 통합 계정의 모든 아티팩트와 B2B 커넥터(예: XML 유효성 검사 및 플랫 파일 인코딩 또는 디코딩)를 사용할 수 있습니다.  

## <a name="unlink-from-logic-app"></a>논리 앱에서 연결 해제

논리 앱을 다른 통합 계정에 연결하거나 더 이상 논리 앱에서 통합 계정을 사용하지 않으려면 Azure Resource Explorer를 통해 링크를 삭제할 수 있습니다.

1. 브라우저에서 <a href="https://resources.azure.com" target="_blank">Azure Resource Explorer(https://resources.azure.com)</a>로 이동합니다. 동일한 Azure 자격 증명으로 로그인했는지 확인합니다.

   ![Azure 리소스 탐색기](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. 검색 상자에서 논리 앱 이름을 입력한 다음, 논리 앱을 찾아서 선택합니다.

   ![논리 앱 찾기 및 선택](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. 탐색기 제목 표시줄에서 **읽기/쓰기**를 선택합니다.

   !["읽기/쓰기" 모드 켜기](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. **데이터** 탭에서 **편집**을 선택합니다.

   !["데이터" 탭에서 "편집" 선택](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. 편집기에서 통합 계정에 대한 다음 형식의 `integrationAccount` 속성을 찾아서 삭제합니다.

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   예를 들면 다음과 같습니다.

   !["integrationAccount" 속성 정의 찾기](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. **데이터** 탭에서 **Put**(저장)을 선택하여 변경 내용을 저장합니다. 

   !["Put"(저장)을 선택하여 변경 내용 저장](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. 이제 Azure Portal의 논리 앱 **워크플로 설정** 아래에서 **통합 계정** 속성이 비어 있는지 확인합니다.

   ![통합 계정이 연결되어 있지 않은지 확인](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>통합 계정 이동

통합 계정을 다른 Azure 구독 또는 리소스 그룹으로 이동할 수 있습니다.

1. Azure 주 메뉴에서 **모든 서비스**를 선택합니다. 검색 상자에서 필터로 "통합 계정"을 입력하고 **통합 계정**을 선택합니다.

   ![통합 계정 찾기](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. **통합 계정** 아래에서 이동하려는 통합 계정을 선택합니다. 통합 계정 메뉴의 **설정** 아래에서 **속성**을 선택합니다.

   !["설정" 아래에서 "속성" 선택](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. 통합 계정에 대한 Azure 리소스 그룹 또는 구독을 변경합니다.

   !["리소스 그룹 변경" 또는 "구독 변경" 선택](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. 완료되면 모든 스크립트를 아티팩트에 대한 새 리소스 ID로 업데이트합니다.  

## <a name="delete-integration-account"></a>통합 계정 삭제

1. Azure 주 메뉴에서 **모든 서비스**를 선택합니다. 검색 상자에서 필터로 "통합 계정"을 입력하고 **통합 계정**을 선택합니다.

   ![통합 계정 찾기](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. **통합 계정** 아래에서 삭제하려는 통합 계정을 선택합니다. 통합 계정 메뉴에서 **개요**, **삭제**를 차례로 선택합니다. 

   ![통합 계정을 선택합니다. "개요" 페이지에서 "삭제" 선택](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. 통합 계정을 삭제하도록 승인하려면 **예**를 선택합니다.

   ![삭제를 승인하려면 "예" 선택](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>다음 단계

* [거래 업체 만들기](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [규약 만들기](../logic-apps/logic-apps-enterprise-integration-agreements.md)
