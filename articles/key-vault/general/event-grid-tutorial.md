---
title: Azure Event Grid를 사용하여 키 자격 증명 모음 알림 수신 및 응답
description: Azure Event Grid와 Key Vault를 통합하는 방법에 대해 알아봅니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 283c66eb3b49b60b87283c5d94cc4f110adceffe
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588750"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Azure Event Grid를 사용하여 키 자격 증명 모음 알림 수신 및 응답(미리보기)

현재 미리 보기로 제공되는 Azure Event Grid와 Azure Key Vault를 통합하면 키 자격 증명 모음에 저장된 비밀의 상태가 변경될 때 사용자에게 알릴 수 있습니다. 기능에 대한 개요는 [Event Grid를 사용하여 Key Vault 모니터링](event-grid-overview.md)을 참조하세요.

이 가이드는 Event Grid를 통해 Key Vault 알림을 받는 방법과 Azure Automation을 통해 상태 변경에 응답하는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- Azure 구독의 키 자격 증명 모음. [Azure CLI를 사용하여 Azure Key Vault에서 비밀을 설정하고 검색](../secrets/quick-create-cli.md)의 단계에 따라 새로운 키 자격 증명 모음을 빠르게 만들 수 있습니다.

## <a name="concepts"></a>개념

Event Grid는 클라우드용 이벤트 서비스입니다. 이 가이드에서는 Key Vault에 대한 이벤트를 구독하고 이벤트를 Automation으로 라우팅합니다. 키 자격 증명 모음의 비밀 중 하나가 곧 만료되면 상태 변경에 대해 Event Grid에 알리고 엔드포인트에 HTTP POST가 수행됩니다. 그런 다음, 웹후크가 PowerShell 스크립트의 Azure Automation 실행을 트리거합니다.

![HTTP POST 순서도](../media/image1.png)

## <a name="create-an-automation-account"></a>Automation 계정 만들기

[Azure Portal](https://portal.azure.com)을 통해 Automation 계정을 만듭니다.

1.  portal.azure.com으로 이동하여 구독에 로그인합니다.

1.  검색 상자에 **Automation 계정**을 입력합니다.

1.  검색 창에 있는 드롭다운 목록의 **서비스** 섹션에서 **Automation 계정**을 선택합니다.

1.  **추가**를 선택합니다.

    ![Automation 계정 창](../media/image2.png)

1.  **Automation 계정 추가** 창에 필요한 정보를 입력한 다음, **만들기**를 선택합니다.

## <a name="create-a-runbook"></a>Runbook 만들기

Automation 계정이 준비되면 Runbook을 만듭니다.

![Runbook UI 만들기](../media/image3.png)

1.  방금 만든 Automation 계정을 선택합니다.

1.  **프로세스 자동화** 아래에서 **Runbook**을 선택합니다.

1.  **Runbook 만들기**를 선택합니다.

1.  runbook의 이름을 지정하고 runbook 유형으로 **PowerShell**을 선택합니다.

1.  만든 Runbook을 선택한 다음, **편집** 단추를 선택합니다.

1.  테스트 목적으로 다음 코드를 입력하고 **게시** 단추를 선택합니다. 그러면 받은 POST 요청의 결과가 반환됩니다.

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![Runbook UI 게시](../media/image4.png)

## <a name="create-a-webhook"></a>웹후크 만들기

웹후크를 만들어서 새로 만든 Runbook을 트리거합니다.

1.  방금 게시한 Runbook의 **리소스** 섹션에서 **Webhook**를 선택합니다.

1.  **Webhook 추가**를 선택합니다.

    ![Webhook 추가 단추](../media/image5.png)

1.  **Webhook 만들기**를 선택합니다.

1. 웹후크의 이름을 지정하고 만료 날짜를 설정하고 URL을 복사합니다.

    > [!IMPORTANT] 
    > URL을 만든 후에는 볼 수 없습니다. 이 가이드의 나머지 부분에서 액세스할 수 있는 안전한 위치에 복사본을 저장해야 합니다.

1. **매개 변수 및 실행 설정**을 선택한 다음, **확인**을 선택합니다. 매개 변수를 입력하지 마십시오. 그러면 **만들기** 단추가 활성화됩니다.

1. **확인**을 선택한 다음, **만들기**를 선택합니다.

    ![새 Webhook UI 만들기](../media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Event Grid 구독 만들기

[Azure Portal](https://portal.azure.com)을 통해 Event Grid 구독을 만듭니다.

1.  키 자격 증명 모음으로 이동하여 **이벤트** 탭을 선택합니다. 표시되지 않는 경우 [포털의 미리 보기 버전](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true)을 사용하고 있는지 확인합니다.

    ![Azure Portal의 이벤트 탭](../media/image7.png)

1.  **이벤트 구독** 단추를 선택합니다.

1.  구독에 대한 설명이 포함된 이름을 만듭니다.

1.  **Event Grid 스키마**를 선택합니다.

1.  **항목 리소스**는 상태 변경을 모니터링하려는 키 자격 증명 모음이어야 합니다.

1.  **이벤트 형식 필터**의 경우 모든 옵션을 선택된 상태(**9개 선택됨**)로 둡니다.

1.  **엔드포인트 유형**으로 **Webhook**를 선택합니다.

1.  **엔드포인트 선택**을 선택합니다. 컨텍스트 창에서 [웹후크 만들기](#create-a-webhook) 단계의 웹후크 URL을 **구독자 엔드포인트** 필드에 붙여넣습니다.

1.  컨텍스트 창에서 **선택 확인**을 선택합니다.

1.  **만들기**를 선택합니다.

    ![이벤트 구독 만들기](../media/image8.png)

## <a name="test-and-verify"></a>테스트 및 확인

Event Grid 구독이 올바르게 구성되었는지 확인합니다. 이 테스트에서는 [Event Grid 구독 만들기](#create-an-event-grid-subscription)에서 “비밀 새 버전 생성됨” 알림을 구독했으며 키 자격 증명 모음에 새 버전의 비밀을 생성하는 데 필요한 권한이 있다고 가정합니다.

![Event Grid 구독의 구성 테스트](../media/image9.png)

![비밀 만들기 창](../media/image10.png)

1.  Azure Portal에서 키 자격 증명 모음으로 이동합니다.

1.  새 비밀을 만듭니다. 테스트 목적으로 만료 날짜를 다음 날짜로 설정합니다.

1.  키 자격 증명 모음의 **이벤트** 탭에서 생성된 Event Grid 구독을 선택합니다.

1.  **메트릭** 아래에서 이벤트가 캡처되었는지 확인합니다. 두 가지 이벤트가 필요합니다. SecretNewVersion 및 SecretNearExpiry 이벤트이며, 이러한 이벤트는 Event Grid가 키 자격 증명 모음에서 비밀의 상태 변경을 캡처했는지 확인합니다.

    ![메트릭 창: 캡처한 이벤트를 확인합니다.](../media/image11.png)

1.  Automation 계정으로 이동합니다.

1.  **Runbook** 탭을 선택한 다음, 생성한 Runbook을 선택합니다.

1.  **Webhook** 탭을 선택하고 “마지막으로 트리거한” 타임스탬프가 새 비밀을 생성한 후 60초 이내인지 확인합니다. 이것은 Event Grid가 키 자격 증명 모음의 상태 변경에 대한 이벤트 세부 정보로 웹후크에 POST를 수행했고 웹후크가 트리거되었다는 것을 확인합니다.

    ![Webhook 탭, 마지막으로 트리거된 타임스탬프](../media/image12.png)

1. Runbook으로 돌아가서 **개요** 탭을 선택합니다.

1. **최근 작업** 목록을 확인합니다. 작업이 생성되었고 상태가 완료된 것이 표시됩니다. 이것은 웹후크가 Runbook을 트리거하여 스크립트 실행을 시작했음을 확인합니다.

    ![Webhook 최근 작업 목록](../media/image13.png)

1. 최근 작업을 선택하고 Event Grid에서 웹후크로 보낸 POST 요청을 확인합니다. JSON을 검토하고 키 자격 증명 모음과 이벤트 형식에 대한 매개 변수가 올바른지 확인합니다. JSON 개체의 “event type” 매개 변수가 키 자격 증명 모음에서 발생한 이벤트(이 예제의 경우 Microsoft.KeyVault.SecretNearExpiry)와 일치하면, 테스트가 성공한 것입니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="you-cant-create-an-event-subscription"></a>이벤트 구독을 만들 수 없습니다.

Azure 구독 리소스 공급자에서 Event Grid 및 키 자격 증명 모음 공급자를 다시 등록합니다. [Azure 리소스 공급자 및 종류](../../azure-resource-manager/management/resource-providers-and-types.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

축하합니다! 위의 단계를 모두 올바르게 수행했다면 이제 키 자격 증명 모음에 저장된 비밀의 상태 변경에 프로그래밍 방식으로 응답할 준비가 된 것입니다.

폴링 기반 시스템을 사용하여 키 자격 증명 모음에서 비밀의 상태 변경을 검색했다면, 이 알림 기능을 사용하여 시작할 수 있습니다. 비밀이 만료될 때 프로그래밍 방식으로 비밀을 갱신하는 코드로 Runbook의 테스트 스크립트를 대체할 수도 있습니다.

자세한 정보:


- 개요: [Azure Event Grid를 사용하여 Key Vault 모니터링(미리 보기)](event-grid-overview.md)
- 방법: [키 자격 증명 모음 비밀 변경 시 이메일 받기](event-grid-logicapps.md)
- [Azure Key Vault에 대한 Azure Event Grid 이벤트 스키마(미리 보기)](../../event-grid/event-schema-key-vault.md)
- [Azure Key Vault 개요](overview.md))
- [Azure Event Grid 개요](../../event-grid/overview.md)
- [Azure Automation 개요](../../automation/index.yml)
