---
title: 원격 모니터링 내의 이메일 작업 - Azure | Microsoft Docs
description: 이 방법 가이드는 새 또는 기존 규칙에 이메일 작업을 추가하는 방법을 보여 줍니다.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: 693da06ce14a4854d5db49f588fa29d791060166
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61445668"
---
# <a name="add-an-email-action"></a>이메일 작업 추가

이메일 작업은 경고를 놓치는 일이 없도록 합니다. 기존 규칙에 이메일 작업을 추가하거나 새 규칙을 만들 때 이메일 작업을 추가할 수 있습니다.

이 방법 가이드의 단계를 완료하려면 Azure 구독에서 원격 모니터링 솔루션 가속기의 배포된 인스턴스가 필요합니다.

규칙을 만들거나 수정하려면 [**관리자**이거나 올바른 사용 권한이 있어야 합니다](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>기존 규칙 편집

다음 단계에 따라 기존 규칙에 이메일 작업을 추가합니다.

1. 원격 모니터링 솔루션으로 이동합니다.

1. **대시보드**에서 **규칙** 페이지로 이동합니다.

    ![규칙 페이지](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. 수정할 기존 규칙 옆에 있는 확인란을 클릭하고 맨 위에 있는 **편집**을 클릭합니다. 편집 가능한 **규칙** 패널이 나타납니다.

1. **작업** 섹션에서 **이메일 사용**을 **켜짐**으로 전환합니다.

1. 솔루션 가속기에서 이메일 작업을 사용하도록 처음으로 설정하는 경우 먼저 [Outlook에 로그인](#outlook)해야 합니다.

1. 받는 사람 상자에 이메일 주소를 입력하고 **Enter** 키를 눌러 각 이메일을 추가합니다.

    ![주소 항목](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. 이메일의 제목을 입력합니다.

1. 이메일 받는 사람에 대한 모든 추가 메모를 일반 텍스트로 입력합니다. [이메일 템플릿을 편집](#htmledit)하는 경우 HTML 서식을 사용할 수 있습니다.

1. **규칙 상태**가 **사용**으로 설정되어 있는지 확인합니다.

1. **적용**을 클릭합니다.

## <a name="create-a-new-rule"></a>새 규칙 만들기

다음 단계에 따라 새 규칙을 만들 때 이메일 작업을 추가합니다.

1. 원격 모니터링 솔루션으로 이동합니다.

1. **대시보드**에서 **규칙** 페이지로 이동합니다.

    ![규칙 페이지](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. [규칙 섹션 만들기](iot-accelerators-remote-monitoring-automate.md#create-a-rule)의 단계를 따릅니다. [고급 규칙 만들기](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) 섹션에서 **심각도 수준**을 설정하는 단계까지 진행합니다. 아직 **적용**을 클릭하지 마세요.

1. **작업** 섹션에서 **이메일 사용**을 **켜짐**으로 전환합니다.

1. 솔루션 가속기에서 이메일 작업을 사용하도록 처음으로 설정하는 경우 먼저 [Outlook에 로그인](#outlook)해야 합니다.

1. 받는 사람 상자에 이메일 주소를 입력하고 **Enter** 키를 눌러 각 이메일을 추가합니다.

    ![주소 항목](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. 이메일의 제목을 입력합니다.

1. 이메일 받는 사람에 대한 모든 추가 메모를 일반 텍스트로 입력합니다. [이메일 템플릿을 편집](#htmledit)하는 경우 HTML 서식을 사용할 수 있습니다.

1. **규칙 상태**가 **사용**으로 설정되어 있는지 확인합니다.

1. **적용**을 클릭합니다.

이제 이메일 작업이 적용된 규칙이 사용되도록 설정됩니다. 작업이 트리거될 때마다 새 이메일이 받는 사람에게 전송됩니다.

## Outlook에 로그인 <a name="outlook"></a>

솔루션 가속기에서 이메일 작업을 처음 사용하도록 설정할 경우 Outlook에 로그인해야 합니다. 이 작업은 이메일 알림을 보내는 이메일 계정을 설정합니다.

> [!NOTE]
> 솔루션 가속기 알림용으로만 특정 Outlook 계정을 생성해야 하며, 이메일 작업을 사용하도록 처음 설정할 때 해당 계정을 사용해야 합니다.

### <a name="contributor-role-outlook-setup"></a>참가자 역할 Outlook 설정

구독에서 **참가자** 역할을 갖는 사용자가 솔루션 가속기를 배포한 경우, 애플리케이션에는 웹 UI를 통해 이메일 작업을 설정하고 확인하기 위한 충분한 권한이 없습니다.

시작하기 전에 솔루션 가속기에서 이메일 알림을 보내는 데 사용할 Outlook 계정을 만듭니다.

다음 단계에서는 이메일 작업을 수동으로 설정하고 확인하는 방법을 보여 줍니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. 솔루션 가속기에 대한 리소스 그룹으로 이동합니다.

1. **office365-connector**를 클릭합니다.

    ![API 연결](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector.png)

1. 권한 부여 프로세스를 시작할 배너를 클릭합니다.

    ![권한 부여](./media/iot-accelerators-remote-monitoring-email-actions/connector.png)

1. **권한 부여**를 클릭합니다. 로그인하라는 메시지가 나타납니다. 로그인하는 데 사용하는 계정은 애플리케이션이 이메일 알림을 보내는 데 사용하는 이메일 주소여야 합니다.

    ![권한 부여 단추](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. 아래쪽에서 **저장**을 클릭합니다. 배너가 사라지면 사용자 권한 부여가 성공적으로 수행됩니다.

1. 알림이 전송되는 원본 이메일 주소를 변경하려면 **API 연결 편집**을 클릭합니다.

    ![이메일 변경](./media/iot-accelerators-remote-monitoring-email-actions/editemail.png)

### <a name="owner-role-outlook-setup"></a>소유자 역할 Outlook 설정

구독에서 **소유자** 역할을 갖는 사용자가 솔루션 가속기를 배포한 경우, 애플리케이션은 웹 UI를 통해 이메일 작업이 올바르게 설정되었는지 확인할 수 있습니다.

시작하기 전에 솔루션 가속기에서 이메일 알림을 보내는 데 사용할 Outlook 계정을 만듭니다.

다음 단계는 로그인한 후 이메일 작업을 설정하는 데 도움이 됩니다.

1. 클릭하여 Outlook에 로그인합니다. 로그인하면 Azure Portal로 페이지가 이동합니다.

   ![Outlook에 로그인](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. **권한 부여**를 클릭합니다. 로그인하라는 메시지가 나타납니다. 로그인하는 데 사용하는 계정은 애플리케이션이 이메일 알림을 보내는 데 사용하는 이메일 주소여야 합니다.

1. **저장**을 클릭합니다. 솔루션 가속기로 돌아가서 페이지를 새로 고칩니다.

1. 이메일 알림을 성공적으로 구성한 경우 다음 메시지가 표시됩니다.

   ![Outlook 로그인 성공](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## 이메일 HTML 사용자 지정 <a name="htmledit"></a>

기본 제공된 원격 모니터링 솔루션 가속기는 작업 이메일에 대한 기본 HTML 템플릿을 제공합니다. 이메일 템플릿은 이메일 작업 설정의 값을 사용합니다. 예를 들면 다음과 같습니다.

![이메일 예제](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate.png)

다음 단계에서는 HTML 이메일 템플릿을 편집하는 방법을 보여 줍니다. 예를 들어 자세한 정보를 포함하거나 사용자 지정 이미지를 추가할 수 있습니다.

1. Java 또는 .NET 원격 모니터링 GitHub 리포지토리를 복제합니다.

1. 이메일 템플릿 위치로 이동합니다.
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. 이 템플릿에서 매개 변수를 추가하거나 제거하여 메시지를 사용자 지정할 수 있습니다. 필요에 따라 호출을 추가, 제거 또는 대체할 수도 있습니다.

    .NET 코드 예제:  `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Java 코드 예제:  `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. 템플릿의 매개 변수는 `${...}` 형태를 갖습니다. 매개 변수를 삭제하려면 필요한 줄을 삭제합니다. 매개 변수를 추가하려면 삽입할 값이 있는 줄을 추가합니다.

1. 이미지 또는 사용자 지정 텍스트를 추가하려면 EmailTemplate.HTML 파일을 직접 업데이트합니다.

## <a name="throttling"></a>제한

원격 모니터링 솔루션 가속기는 Outlook을 사용하여 이메일 알림을 보냅니다. Outlook에서 전송되는 이메일 수가 [분당 30개 이메일](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits)로 제한됩니다. 이메일을 수신하는 이메일 클라이언트에서 분당 수신되는 이메일의 수를 제한할 수도 있습니다. 제한 사항은 특정 이메일 클라이언트에 문의하세요. 규칙에 대해 이메일 알림을 설정할 때 규칙은 적어도 1분 동안의 평균 값을 계산하고 아주 짧은 기간의 값은 사용하지 않아야 합니다.

![평균 계산](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>다음 단계

이 가이드에서는 원격 모니터링 솔루션 내의 새 또는 기존 규칙에 이메일 작업을 추가하는 방법을 보여 주었습니다. 또한 메시지 형식을 정의하는 HTML 편집 방법도 제시했습니다.

제안되는 다음 단계는 [경고를 사용하고 디바이스 문제를 해결하는 방법](iot-accelerators-remote-monitoring-maintain.md)을 살펴보는 것입니다.
