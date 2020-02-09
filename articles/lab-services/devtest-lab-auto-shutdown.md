---
title: Azure DevTest Labs에서 자동 종료 정책 관리 Microsoft Docs
description: 사용 하지 않을 때 가상 컴퓨터가 자동으로 종료 되도록 랩에 대 한 자동 종료 정책을 설정 하는 방법에 대해 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2020
ms.author: spelluru
ms.openlocfilehash: a2d0b9bdfba1b96ad42e45d54faf106b2361e29d
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264793"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩 및 계산 가상 컴퓨터에 대 한 자동 종료 구성

이 문서에서는 DevTest Labs에서 랩 vm에 대 한 자동 종료 설정 및 계산 vm을 구성 하는 방법을 설명 합니다. 

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>랩 vm에 대 한 자동 종료 구성 (DevTest Labs)
Azure DevTest Labs를 통해 각 랩에 대한 정책(설정)을 관리하여 비용을 제어하고 랩에서의 낭비를 최소화할 수 있습니다. 이 문서에서는 랩 계정에 대 한 자동 종료 정책을 구성 하 고 랩 계정에서 랩에 대 한 자동 종료 설정을 구성 하는 방법을 보여 줍니다. 모든 랩 정책을 설정하는 방법을 보려면 [Azure DevTest Labs에서 랩 정책 정의](devtest-lab-set-lab-policy.md)를 참조하세요.  

### <a name="set-auto-shut-down-policy-for-a-lab"></a>랩에 대 한 자동 종료 정책 설정
랩 소유자로서 랩에서 모든 VM에 대한 종료 일정을 구성할 수 있습니다. 이 작업을 수행하면 사용되지 않는(유휴 상태) 머신의 실행 비용을 절약할 수 있습니다. 모든 랩 VM에서 중앙 집중식으로 종료 정책을 적용할 수 있을 뿐만 아니라 랩 사용자가 개별 머신에 대한 일정을 설정하는 수고를 줄일 수도 있습니다. 이 기능을 사용하면 랩 사용자에게 모든 권한에 대한 권한 없음 제공에서 시작하는 랩 일정에 대한 정책을 설정할 수 있습니다. 랩 소유자로서 다음 단계를 수행하여 이 정책을 구성할 수 있습니다.

1. 랩의 홈 페이지에서 **구성 및 정책**을 선택합니다.
2. 왼쪽 메뉴의 **일정** 섹션에서 **자동 종료 정책**을 선택합니다.
3. 옵션 중 하나를 선택합니다. 다음 섹션에서는 이러한 옵션에 대한 자세한 정보를 제공합니다. 설정 정책은 기존 VM이 아닌 랩에서 생성된 새 VM에만 적용됩니다. 

    ![자동 종료 정책 옵션](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="configure-auto-shutdown-settings"></a>자동 종료 설정 구성
자동 종료 정책은이 랩의 vm이 종료 되는 시간을 지정할 수 있도록 하 여 랩 낭비를 최소화 하는 데 도움이 됩니다.

랩에 대한 정책을 보거나 변경하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스**를 선택한 다음 목록에서 **DevTest Labs**를 선택합니다.
3. 랩 목록에서 원하는 랩을 탭합니다.   
4. **구성 및 정책**을 선택합니다.

    ![정책 설정 창](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. 랩의 **구성 및 정책** 창의 **일정**에서 **자동 종료** 를 선택 합니다.
   
    ![자동 종료](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. 이 정책을 사용하도록 설정하려면 **설정**을 선택하고 사용하지 않도록 설정하려면 **해제**를 선택합니다.
7. 이 정책을 사용하도록 설정한 경우 현재 랩의 모든 VM을 종료하는 시간(및 표준 시간대)을 지정합니다.
8. 지정 된 자동 종료 시간 이전에 30 분 전에 알림을 보내는 옵션에 대해 **예** 또는 **아니요** 를 지정 합니다. **예**를 선택할 경우 알림을 게시하거나 보낼 위치를 지정하는 이메일 주소 또는 웹후크 URL 엔드포인트를 입력합니다. 사용자에게 알림이 전송되고 종료를 지연할 수 있는 옵션이 제공됩니다. 자세한 내용은 [알림](#notifications) 섹션을 참조 하세요. 
9. **저장**을 선택합니다.

    기본적으로 이 정책을 사용하도록 설정하면 현재 랩의 모든 VM에 적용됩니다. 특정 VM에서이 설정을 제거 하려면 VM의 관리 창을 열고 **자동 종료** 설정을 변경 합니다.
    
> [!NOTE]
> 랩 또는 특정 랩 가상 컴퓨터의 자동 종료 일정을 현재 예약 된 시간의 30 분 이내에 업데이트 하는 경우 업데이트 된 종료 시간은 다음 날의 일정에 적용 됩니다. 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>사용자가 일정을 설정하고 옵트아웃할 수 있음
이 정책에 랩을 설정한 경우 랩 사용자는 랩 일정을 재정의하거나 옵트아웃할 수 있습니다. 이 옵션은 랩 사용자에게 해당 VM의 자동 종료 일정에 대한 모든 권한을 부여합니다. 랩 사용자는 해당 VM 자동 종료 일정 페이지에 변경 내용이 표시되지 않습니다.

![자동 종료 정책 옵션-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>사용자가 일정을 설정하지만 옵트아웃할 수 없음
이 정책에 랩을 설정한 경우 랩 사용자는 랩 일정을 재정의할 수 있습니다. 그러나 자동 종료 정책을 옵트아웃할 수 없습니다. 이 옵션을 통해 랩의 모든 머신이 자동 종료 일정에 포함됩니다. 랩 사용자는 해당 VM의 자동 종료 일정을 업데이트하고, 종료 알림을 설정할 수 있습니다.

![자동 종료 정책 옵션-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>랩 관리자가 설정한 일정을 사용자가 제어할 수 없음
이 정책에 랩을 설정한 경우 랩 사용자는 랩 일정을 재정의하거나 옵트아웃할 수 없습니다. 이 옵션은 랩 관리자에게 랩의 모든 머신에 대한 일정의 완전한 권한을 제공합니다. 랩 사용자는 해당 VM에 대한 자동 종료 알림만 설정할 수 있습니다.

![자동 종료 정책 옵션-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

### <a name="notifications"></a>알림
자동 종료가 랩 소유자에 의해 설정 되 면 Vm의 영향을 받는 경우 자동 종료가 트리거되는 30 분 전에 랩 사용자에 게 알림이 전송 됩니다. 이 옵션을 사용 하면 랩 사용자는 종료 되기 전에 작업을 저장할 수 있습니다. 또한 알림은 각 VM에 대 한 링크를 제공 하 여 다음 작업을 수행 합니다.

- 이 시간 동안 자동 종료 건너뛰기
- VM에서 계속 작업을 수행할 수 있도록 1 시간 또는 2 시간의 자동 종료를 다시 알림 합니다.

알림은 구성 된 웹 후크 엔드포인트 또는 자동 종료 설정의 랩 소유자가 지정한 전자 메일 주소를 통해 전송 됩니다. 웹 후크를 사용 하면 특정 이벤트를 구독 하는 통합을 빌드하거나 설정할 수 있습니다. 이러한 이벤트 중 하나가 트리거되면 DevTest Labs는 HTTP POST 페이로드를 webhook의 구성 된 URL에 보냅니다. 웹후크에 대한 자세한 내용은 [웹후크 또는 API Azure Function 만들기](../azure-functions/functions-create-a-web-hook-or-api-function.md)를 참조하세요. 

웹 후크는 다양 한 앱에서 광범위 하 게 지원 되므로 (예: 여유 시간, Azure Logic Apps 등) 웹 후크를 사용 하는 것이 좋습니다 .이를 통해 알림을 보내는 고유한 방법을 구현할 수 있습니다. 예를 들어이 문서에서는 Azure Logic Apps를 사용 하 여 전자 메일에서 자동 종료 알림을 가져오는 방법을 안내 합니다. 먼저 실습에서 자동 종료 알림을 사용 하도록 설정 하는 기본 단계를 빠르게 수행해 보겠습니다.   

### <a name="create-a-logic-app-that-receives-email-notifications"></a>전자 메일 알림을 수신 하는 논리 앱 만들기
[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 는 Office 365 및 twitter와 같은 다른 클라이언트와 서비스를 쉽게 통합할 수 있도록 해 주는 다양 한 기본 제공 커넥터를 제공 합니다. 개략적인 수준에서 전자 메일 알림에 대 한 논리 앱을 설정 하는 단계는 다음 네 단계로 나눌 수 있습니다. 

- 논리 앱을 만듭니다. 
- 기본 제공 템플릿을 구성 합니다.
- 전자 메일 클라이언트와 통합
- Webhook URL을 가져옵니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기
시작 하려면 다음 단계를 사용 하 여 Azure 구독에서 논리 앱을 만듭니다.

1. 왼쪽 메뉴에서 **+ 리소스 만들기** 를 선택 하 고 **통합**을 선택한 다음 **논리 앱**을 선택 합니다. 

    ![새 논리 앱 메뉴](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. **논리 앱-만들기** 페이지에서 다음 단계를 수행 합니다. 
    1. 논리 앱의 **이름을** 입력 합니다.
    2. Azure **구독**을 선택합니다.
    3. 새 **리소스 그룹**을 만들거나 기존 리소스 그룹을 선택합니다. 
    4. 논리 앱의 **위치** 를 선택 합니다. 

        ![새 논리 앱-설정](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. 알림에서 알림에서 **리소스로 이동** **을 선택**합니다. 

    ![리소스로 이동](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. **배포 도구** 범주에서 **논리 앱 디자이너** 를 선택 합니다.

    ![HTTP 요청/응답을 선택 합니다.](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. **HTTP 요청-응답** 페이지에서 **이 템플릿 사용**을 선택 합니다. 

    ![이 템플릿 사용 옵션을 선택 합니다.](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. 다음 JSON을 **요청 본문 Json 스키마** 섹션에 복사 합니다. 

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName"
        ],
        "type": "object"
    }
    ```
    
    ![요청 본문 JSON 스키마](./media/devtest-lab-auto-shutdown/request-json.png)
7. 디자이너에서 **+ 새 단계** 를 선택 하 고 다음 단계를 수행 합니다.
    1. **Office 365 Outlook**을 검색 합니다. 전자 메일을 보냅니다. 
    2. **작업**에서 **전자 메일 보내기** 를 선택 합니다. 
    
        ![전자 메일 보내기 옵션](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. **로그인** 을 선택 하 여 전자 메일 계정에 로그인 합니다. 
    4. **대상** 필드를 선택 하 고 소유자를 선택 합니다.
    5. **제목**을 선택 하 고 전자 메일 알림의 제목을 입력 합니다. 예: "machine vmName for Lab: 연구원 이름"
    6. **본문**을 선택 하 고 전자 메일 알림의 본문 콘텐츠를 정의 합니다. 예: "vmName은 15 분 내에 종료 되도록 예약 됩니다. URL을 클릭 하 여이 종료를 건너뜁니다. 한 시간 동안 종료 지연: delayUrl60. 2 시간 동안 종료 지연: delayUrl120. "

        ![요청 본문 JSON 스키마](./media/devtest-lab-auto-shutdown/email-options.png)
1. 도구 모음에서 **저장**을 선택합니다. 이제 **HTTP POST URL**을 복사할 수 있습니다. 복사 단추를 선택 하 여 URL을 클립보드에 복사 합니다. 

    ![WebHook URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>계산 vm에 대 한 자동 종료 구성

1. **가상 컴퓨터** 페이지의 왼쪽 메뉴에서 **자동 종료** 를 선택 합니다. 
2. **자동 종료** 페이지에서 설정 **을 선택 하 여이** 정책을 사용 하도록 설정 하 고 **해제** 를 선택 하 여 사용 하지 않도록 설정 합니다.
3. 이 정책을 사용 하도록 설정 하는 경우 VM을 종료 해야 하는 **시간** 및 표준 **시간대**를 지정 합니다.
4. 지정 된 자동 종료 시간 이전에 30 분 전에 알림을 보내는 옵션에 대해 **예** 또는 **아니요** 를 지정 합니다. **예**를 선택할 경우 알림을 게시하거나 보낼 위치를 지정하는 이메일 주소 또는 웹후크 URL 엔드포인트를 입력합니다. 사용자에게 알림이 전송되고 종료를 지연할 수 있는 옵션이 제공됩니다. 자세한 내용은 [알림](#notifications) 섹션을 참조 하세요. 
9. **저장**을 선택합니다.

    ![계산 VM에 대 한 자동 종료 구성](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

## <a name="next-steps"></a>다음 단계
모든 정책을 설정 하는 방법을 알아보려면 [Azure DevTest Labs에서 랩 정책 정의](devtest-lab-set-lab-policy.md)를 참조 하세요.

