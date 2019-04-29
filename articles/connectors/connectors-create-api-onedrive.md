---
title: OneDrive에 연결 - Azure Logic Apps | Microsoft Docs
description: OneDrive REST API 및 Azure Logic Apps를 사용하여 파일 업로드 및 관리
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 10/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 840a010f8606387a250552d884621a96d0031f90
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106230"
---
# <a name="get-started-with-the-onedrive-connector"></a>OneDrive 커넥터 시작
OneDrive에 연결하여 파일 업로드, 가져오기, 삭제 등을 포함하여 파일을 관리합니다. 

OneDrive를 사용하여 다음과 같은 작업을 수행합니다. 

* OneDrive에서 파일을 저장하여 워크플로를 작성하거나 OneDrive의 기존 파일을 업데이트합니다. 
* 트리거를 사용하여 OneDrive 내에서 파일이 만들어지거나 업데이트될 때 워크플로를 시작합니다.
* 파일 만들기, 파일 삭제 등의 작업을 사용합니다. 예를 들어 새 Office 365 전자 메일이 첨부 파일과 함께 수신되면(트리거) OneDrive에 새 파일을 만듭니다(작업).

이 아티클에서는 논리 앱에서 OneDrive 커넥터를 사용하는 방법을 보여주고, 트리거 및 작업을 나열합니다.

Logic Apps에 대해 자세히 알아보려면 [논리 앱이란 무엇인가요?](../logic-apps/logic-apps-overview.md) 및 [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="connect-to-onedrive"></a>OneDrive에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. 연결은 논리 앱과 다른 서비스 간의 연결을 제공합니다. 예를 들어 OneDrive에 연결하려면 먼저 OneDrive *연결*이 필요합니다. 연결을 만들려면 연결하려는 서비스에 액세스할 때 일반적으로 사용하는 자격 증명을 입력합니다. 따라서 OneDrive를 사용하는 경우 OneDrive 계정에 대한 자격 증명을 입력하여 연결을 만듭니다.

### <a name="create-the-connection"></a>연결 만들기
> [!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## <a name="use-a-trigger"></a>트리거 사용
트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. 원하는 간격 및 빈도로 서비스의 "폴링"을 트리거합니다. [트리거에 대해 자세히 알아보세요.](../logic-apps/logic-apps-overview.md#logic-app-concepts)

1. 논리 앱에서 트리거 목록을 가져오려면 "onedrive"를 입력합니다.  
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. **파일을 수정할 때**를 선택합니다. 연결이 이미 있는 경우 선택 표시 단추를 선택하여 폴더를 선택합니다.
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    로그인하라는 메시지가 표시되면 로그인 세부 정보를 입력하여 연결을 만듭니다. 이 아티클의 [연결 만들기](connectors-create-api-onedrive.md#create-the-connection)에는 단계가 나열됩니다. 
   
   > [!NOTE]
   > 이 예제에서는 선택한 폴더의 파일이 업데이트되면 논리 앱이 실행됩니다. 이 트리거의 결과를 보려면 전자 메일을 보내는 다른 작업을 추가합니다. 예를 들어 파일이 업데이트될 때 전자 메일을 보내는 Office 365 Outlook *전자 메일 보내기* 작업을 추가합니다. 

3. **편집** 단추를 선택하고 **빈도** 및 **간격** 값을 설정합니다. 예를 들어 15분마다 폴링을 트리거하려면 **빈도**를 **분**으로, **간격**을 **15**로 설정합니다. 
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. 변경 내용을 **저장**합니다(도구 모음 왼쪽 위 모서리). 논리 앱이 저장되며 이 논리 앱이 사용 상태로 자동 설정될 수 있습니다.

## <a name="use-an-action"></a>작업 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](../logic-apps/logic-apps-overview.md#logic-app-concepts)

1. 더하기 기호를 선택합니다. 여러 선택 항목 표시 됩니다. **작업 추가**, **조건 추가**, 또는 중 하나는 **자세한** 옵션입니다.
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. **작업 추가**를 선택합니다.
3. 텍스트 상자에 "onedrive"를 입력하여 사용 가능한 모든 작업의 목록을 표시합니다.
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 
4. 이 예제에서는 **OneDrive - 파일 만들기**를 선택합니다. 연결이 이미 존재하는 경우 **폴더 경로**를 선택하여 파일을 입력하고 **파일 이름**을 입력한 후 원하는 **파일 콘텐츠**를 선택합니다.  
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    연결 정보를 묻는 메시지가 표시되면 연결을 만들기 위한 세부 정보를 입력합니다. 이 아티클의 [연결 만들기](connectors-create-api-onedrive.md#create-the-connection)에서는 이러한 속성에 대해 설명합니다. 
   
   > [!NOTE]
   > 이 예제에서는 OneDrive 폴더에 새 파일을 만듭니다. 다른 트리거의 출력을 사용하여 OneDrive 파일을 만들 수 있습니다. 예를 들어 Office 365 Outlook *새 전자 메일이 도착했을 때* 트리거를 추가합니다. 그런 후 ForEach 내에서 Attachments 및 Content-Type 필드를 사용하는 OneDrive *파일 만들기* 작업을 추가하여 OneDrive에서 새 파일을 만듭니다. 
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. 변경 내용을 **저장**합니다(도구 모음 왼쪽 위 모서리). 논리 앱이 저장되며 이 논리 앱이 사용 상태로 자동 설정될 수 있습니다.


## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/onedriveconnector/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다.

## <a name="more-connectors"></a>추가 커넥터
[API 목록](apis-list.md)으로 돌아갑니다.