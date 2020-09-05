---
title: 비밀의 Key Vault 상태가 변경될 때 이메일 보내기
description: Logic Apps를 사용하여 Key Vault 비밀 변경에 응답하는 가이드
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: c0121c42c5d498aa79109c874981b9de0f8f4b7c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588877"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Logic Apps를 사용하여 키 자격 증명 모음 비밀의 상태 변경에 대한 이메일 수신

이 가이드에서는 [Azure Logic Apps](../../logic-apps/index.yml)를 사용하여 [Azure Event Grid](../../event-grid/index.yml)를 통해 받은 Azure Key Vault 이벤트에 응답하는 방법을 알아봅니다. 결과적으로 Azure Key Vault에서 비밀이 만들어질 때마다 Azure 논리 앱이 알림 이메일을 보내도록 설정합니다.

Azure Key Vault/Azure Event Grid 통합에 대한 개요는 [Azure Event Grid를 사용하여 Key Vault 모니터링(미리 보기)](event-grid-overview.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Logic Apps에서 지원하는 이메일 공급자(예: Office 365 Outlook)가 제공한 이메일 계정 이 이메일 계정은 이벤트 알림을 보내는 데 사용됩니다. 지원되는 Logic App 커넥터의 전체 목록은 [커넥터 개요](/connectors)를 참조하세요.
- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- Azure 구독의 키 자격 증명 모음. [Azure CLI를 사용하여 Azure Key Vault에서 비밀을 설정하고 검색](../secrets/quick-create-cli.md)의 단계에 따라 새로운 키 자격 증명 모음을 빠르게 만들 수 있습니다.
- 리소스 공급자로 등록된 Event Grid는 [리소스 공급자 등록](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)을 참조하세요.

## <a name="create-a-logic-app-via-event-grid"></a>Event Grid를 통해 논리 앱 만들기

먼저 이벤트 그리드 처리기를 사용하여 논리 앱을 만들고 Azure Key Vault "SecretNewVersionCreated" 이벤트를 구독합니다.

Azure Event Grid 구독을 만들려면 다음 단계를 수행합니다.

1. Azure Portal에서 키 자격 증명 모음으로 이동하여 **이벤트 > 시작**을 선택하고 **Logic Apps**를 클릭합니다.

    
    ![Key Vault - 이벤트 페이지](../media/eventgrid-logicapps-kvsubs.png)

1. **Logic Apps 디자이너**에서 연결의 유효성을 검사하고 **계속**을 클릭합니다. 
 
    ![논리 앱 디자이너 - 연결](../media/eventgrid-logicappdesigner1.png)

1. **리소스 이벤트가 발생하는 경우** 화면에서 다음을 수행합니다.
    - **구독** 및 **리소스 이름**을 기본값으로 둡니다.
    - **리소스 종류**에 대해 **Microsoft.KeyVault.vaults**를 선택합니다.
    - **이벤트 유형 항목 - 1**에 대해 **Microsoft.KeyVault.SecretNewVersionCreated**를 선택합니다.

    ![논리 앱 디자이너 - 이벤트 처리기](../media/eventgrid-logicappdesigner2.png)

1. **+ 새 단계**를 선택합니다. 그러면 작업 선택 창이 열립니다.
1. **이메일** 검색. 전자 메일 공급자에 따라 일치하는 커넥터를 찾아 선택합니다. 이 자습서에서는 **Office 365 Outlook**이 사용됩니다. 다른 이메일 공급자를 위한 단계들도 비슷합니다.
1. **이메일 보내기(V2)** 작업을 선택합니다.

   ![논리 앱 디자이너 - 이메일 추가](../media/eventgrid-logicappdesigner3.png)

1. 이메일 템플릿을 작성합니다.
    - **받는 사람:** 알림 이메일을 받을 이메일 주소를 입력합니다. 이 자습서에서는 테스트를 위해 액세스할 수 있는 메일 계정을 사용합니다.
    - **제목** 및 **본문**: 이메일에 텍스트를 작성합니다. 선택기 도구에서 이벤트 데이터를 기반으로 동적 콘텐츠를 포함할 JSON 속성을 선택합니다. `@{triggerBody()?['Data']}`를 사용하여 이벤트 데이터를 검색할 수 있습니다.

    이메일 템플릿은 다음 예와 같습니다.

    ![논리 앱 디자이너 - 이메일 추가](../media/eventgrid-logicappdesigner4.png)

8. **다른 이름으로 저장**을 클릭합니다.
9. 새 논리 앱에 대한 **이름**을 입력하고 **만들기**를 클릭합니다.
    
    ![논리 앱 디자이너 - 이메일 추가](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>테스트 및 확인

1.  Azure Portal에서 키 자격 증명 모음으로 이동하여 **이벤트 > 이벤트 구독**을 선택합니다.  새 구독을 만들었는지 확인합니다.
    
    ![논리 앱 디자이너 - 이메일 추가](../media/eventgrid-logicapps-kvnewsubs.png)

1.  키 자격 증명 모음으로 이동하여 **비밀**을 선택하고, **+ 생성/가져오기**를 선택합니다. 테스트를 위해 새 비밀을 만들고 키의 이름을 지정하고 나머지 매개 변수를 기본 설정으로 유지합니다.

    ![Key Vault - 비밀 만들기](../media/eventgrid-logicapps-kv-create-secret.png)

1. **비밀 만들기** 화면에서 모든 이름, 값을 제공하고, **만들기**를 선택합니다.

비밀을 만들면 구성된 주소에서 이메일이 수신됩니다.

## <a name="next-steps"></a>다음 단계

- 개요: [Azure Event Grid를 사용하여 Key Vault 모니터링(미리 보기)](event-grid-overview.md)
- 방법: [키 자격 증명 모음 알림을 Azure Automation으로 라우팅](event-grid-tutorial.md)
- [Azure Key Vault에 대한 Azure Event Grid 이벤트 스키마(미리 보기)](../../event-grid/event-schema-key-vault.md)
- [Azure Event Grid](../../event-grid/index.yml)에 대해 자세히 알아봅니다.
- [Azure App Service의 Logic Apps 기능](../../logic-apps/index.yml)에 대해 자세히 알아봅니다.
