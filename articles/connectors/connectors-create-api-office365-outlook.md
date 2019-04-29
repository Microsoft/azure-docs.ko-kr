---
title: Office 365 Outlook에 연결 - Azure Logic Apps | Microsoft Docs
description: Office 365 REST API 및 Azure Logic Apps를 사용하여 이메일, 연락처 및 달력 관리
author: ecfan
manager: jeconnoc
ms.author: v-yiso
origin.date: 10/18/2016
ms.date: 09/03/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 52abf17e869216e65780129a7b48df79bd79f67a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105057"
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Office 365 Outlook 커넥터 시작
Office 365 Outlook 커넥터를 통해 Office 365에서 Outlook과 상호 작용할 수 있습니다. 이 커넥터를 사용하여 연락처 및 일정 항목을 만들기, 편집 및 업데이트하고 전자 메일을 가져오고 보내며 회신할 수도 있습니다.

Office 365 Outlook을 사용하여 다음을 수행합니다.

* Office 365 내에서 전자 메일 및 일정 기능을 사용하여 워크플로를 빌드합니다. 
* 새 전자 메일이 있거나 일정 항목이 업데이트될 때 트리거를 사용하여 워크플로를 시작합니다.
* 전자 메일을 보내고 새 일정 이벤트를 만드는 등의 작업을 사용합니다. 예를 들어 Salesforce에 새 개체(트리거)가 있는 경우 Office 365 Outlook으로 전자 메일을 보냅니다(작업). 

이 아티클에서는 논리 앱에서 Office 365 Outlook 커넥터를 사용하는 방법을 보여주고, 트리거 및 작업을 나열합니다.

> [!NOTE]
> 이 버전의 문서는 Logic Apps GA(일반 공급)에 적용됩니다.
> 
> 

Logic Apps에 대해 자세히 알아보려면 [논리 앱이란 무엇인가요?](../logic-apps/logic-apps-overview.md) 및 [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="connect-to-office-365"></a>Office 365에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. 연결은 논리 앱과 다른 서비스 간의 연결을 제공합니다. 예를 들어 Office 365 Outlook에 연결하려면 먼저 Office 365 *연결*이 필요합니다. 연결을 만들려면 연결하려는 서비스에 액세스할 때 일반적으로 사용하는 자격 증명을 입력합니다. 따라서 Office 365 Outlook을 사용하는 경우 Office 365 계정에 대한 자격 증명을 입력하여 연결을 만듭니다.

## <a name="create-the-connection"></a>연결 만들기
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>트리거 사용
트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. 원하는 간격 및 빈도로 서비스의 "폴링"을 트리거합니다. [트리거에 대해 자세히 알아보세요.](../logic-apps/logic-apps-overview.md#logic-app-concepts)

1. 논리 앱에서 트리거 목록을 가져오려면 "office 365"를 입력합니다.  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. **Office 365 Outlook - 예정된 이벤트가 곧 시작될 때**를 선택합니다. 연결이 이미 있는 경우 드롭다운 목록에서 일정을 선택합니다.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    로그인하라는 메시지가 표시되면 로그인 세부 정보를 입력하여 연결을 만듭니다. 이 항목의 [연결 만들기](connectors-create-api-office365-outlook.md#create-the-connection)에서 단계가 나열됩니다. 
   
   > [!NOTE]
   > 이 예제에서는 일정 이벤트가 업데이트되면 논리 앱이 실행됩니다. 이 트리거의 결과를 보려면 텍스트 메시지를 보내는 다른 작업을 추가합니다. 예를 들어 일정 이벤트가 15분 내에 시작되는 경우 텍스트를 보내는 Twilio *메시지 보내기* 작업을 추가합니다. 
   > 
   > 
3. **편집** 단추를 선택하고 **빈도** 및 **간격** 값을 설정합니다. 예를 들어 15분마다 폴링을 트리거하려면 **빈도**를 **분**으로, **간격**을 **15**로 설정합니다. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. 변경 내용을 **저장**합니다(도구 모음 왼쪽 위 모서리). 논리 앱이 저장되며 이 논리 앱이 사용 상태로 자동 설정될 수 있습니다.

## <a name="use-an-action"></a>작업 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](../logic-apps/logic-apps-overview.md#logic-app-concepts)

1. 더하기 기호를 선택합니다. 여러 선택 항목 표시 됩니다. **작업 추가**, **조건 추가**, 또는 중 하나는 **자세한** 옵션입니다.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. **작업 추가**를 선택합니다.
3. 사용 가능한 모든 작업의 목록을 표시하려면 텍스트 상자에 "office 365"를 입력합니다.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. 이 예제에서는 **Office 365 Outlook - 연락처 만들기**를 선택했습니다. 연결이 이미 존재하는 경우 **폴더 ID**, **지정된 이름** 및 기타 속성을 선택합니다.  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    연결 정보를 묻는 메시지가 표시되면 연결을 만들기 위한 세부 정보를 입력합니다. 이 항목의 [연결 만들기](connectors-create-api-office365-outlook.md#create-the-connection)에서는 이러한 속성에 대해 설명합니다. 
   
   > [!NOTE]
   > 이 예제에서는 Office 365 Outlook에 새 연락처를 만듭니다. 다른 트리거의 출력을 사용하여 연락처를 만들 수 있습니다. 예를 들어 SalesForce *개체를 만들 때* 트리거를 추가합니다. 그런 다음 SalesForce 필드를 사용하여 Office 365에서 연락처를 만드는 Office 365 Outlook *연락처 만들기* 작업을 추가합니다. 
   > 
   > 
5. 변경 내용을 **저장**합니다(도구 모음 왼쪽 위 모서리). 논리 앱이 저장되며 이 논리 앱이 사용 상태로 자동 설정될 수 있습니다.

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/office365connector/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md) [API 목록](apis-list.md)에서 Logic Apps의 사용 가능한 다른 커넥터를 확인하세요.

