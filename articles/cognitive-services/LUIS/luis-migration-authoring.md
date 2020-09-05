---
title: Azure 리소스 제작 키로 마이그레이션
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 전자 메일 계정에서 Azure 리소스로 Language Understanding (LUIS) 제작 인증을 마이그레이션하는 방법을 설명 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/13/2020
ms.author: diberry
ms.openlocfilehash: 4c9dc04770e5fadd72c5460a4b44c05ffda47cb7
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245454"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Azure 리소스 제작 키로 마이그레이션

LUIS () 제작 인증을 전자 메일 계정에서 Azure 리소스로 변경 했습니다. Language Understanding 현재 필수는 아니지만 Azure 리소스로 전환 하는 것은 나중에 적용 됩니다.


## <a name="what-is-migration"></a>마이그레이션 이란?

마이그레이션은 전자 메일 계정에서 Azure 리소스로의 제작 인증을 변경 하는 프로세스입니다. 마이그레이션한 후 계정이 Azure 구독 및 Azure 제작 리소스에 연결 됩니다. *모든 LUIS 사용자 (소유자 또는 협력자)는 결국 마이그레이션해야 합니다.*

마이그레이션은 LUIS 포털에서 수행 해야 합니다. 예를 들어 LUIS CLI를 사용 하 여 제작 키를 만드는 경우 LUIS 포털에서 마이그레이션 프로세스를 완료 해야 합니다. 마이그레이션 후 응용 프로그램에 계속 작성 수 있지만 응용 프로그램 수준 대신 Azure 리소스 수준에 추가 됩니다.

> [!Note]
> 마이그레이션하기 전에 작성를 LUIS app 수준에서 _협력자_ 라고 합니다. 마이그레이션 후 azure 리소스 수준에서 동일한 기능에 대해 _참가자_ 의 azure 역할을 사용 합니다.

## <a name="note-before-you-migrate"></a>마이그레이션 전 주의 사항

* **2020 년 11 월 2**일부 터 제작 환경을 마이그레이션해야 합니다. 
* 마이그레이션은 단방향 프로세스입니다. 마이그레이션한 후에는 뒤로 이동할 수 없습니다.
* 응용 프로그램의 소유자 인 경우 응용 프로그램은 자동으로 마이그레이션됩니다.
* 소유자는 마이그레이션할 앱의 하위 집합을 선택할 수 없으며 프로세스를 되돌릴 수 없습니다.
* 소유자가 마이그레이션한 후에는 협력자 측에서 응용 프로그램이 사라집니다.
* 소유자는 사용자에 게 마이그레이션을 알리기 위해 협력자에 게 전자 메일을 보낼지 묻는 메시지를 표시 합니다.
* 응용 프로그램의 협력자 인 경우 응용 프로그램은 사용자와 함께 마이그레이션하지 않습니다.
* 소유자가 공동 작업자의 마이그레이션에 대해 알 수 있는 방법은 없습니다.
* 마이그레이션은 자동으로 협력자를 수집 하 여 Azure authoring resource로 이동 하거나 추가 하지 않습니다. 앱 소유자는 마이그레이션 후이 단계를 완료 해야 하는 소유자입니다. 이 단계에서는 [Azure 제작 리소스에 대 한 권한이](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate)필요 합니다.
* 공동 작업자를 Azure 리소스에 할당 한 후에는 액세스 응용 프로그램으로 마이그레이션해야 합니다. 그렇지 않으면 응용 프로그램을 작성 하는 데 사용할 수 있는 권한이 없습니다.
* 마이그레이션된 사용자는 응용 프로그램의 협력자로 추가할 수 없습니다.
* 다른 사용자가 소유한 응용 프로그램에 할당 된 예측 키를 소유 하는 경우 소유자와 협력자 모두에 대 한 마이그레이션이 차단 됩니다. 이 문서의 뒷부분에 나오는 권장 사항을 참조 하세요.

> [!Note]
> 예측 런타임 리소스를 만들어야 하는 경우에 [는 별도의 프로세스](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) 를 수행 해야 합니다.

## <a name="migration-prerequisites"></a>마이그레이션 필수 조건

* 유효한 Azure 구독과 연결 해야 합니다. 테 넌 트 관리자에 게 구독에 대 한 추가를 요청 하거나 [무료로 등록할](https://azure.microsoft.com/free/cognitive-services)수 있습니다.
* LUIS 포털에서 또는 Azure Portal에서 LUIS Azure 제작 리소스를 만들어야 합니다. LUIS 포털에서 제작 리소스를 만드는 과정은 다음 섹션에서 설명 하는 마이그레이션 흐름의 일부입니다.
* 응용 프로그램의 협력자 인 경우 응용 프로그램이 자동으로 마이그레이션되지 않습니다. 이러한 응용 프로그램을 내보내거나 [내보내기 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)를 사용 하 여 백업 하는 것이 좋습니다. 마이그레이션 후 앱을 다시 LUIS으로 가져올 수 있습니다. 가져오기 프로세스에서는 사용자가 소유자 인 새 앱 ID를 사용 하 여 새 앱을 만듭니다.
* 응용 프로그램의 소유자 인 경우 자동으로 마이그레이션될 앱을 내보낼 필요가 없습니다. 각 앱의 협력자 목록을 저장 하는 것이 좋습니다. 이 목록을 포함 하는 전자 메일 템플릿은 선택적으로 마이그레이션 프로세스의 일부로 제공 됩니다.


|포털|용도|
|--|--|
|[Azure](https://azure.microsoft.com/free/cognitive-services)| 예측 및 제작 리소스를 만듭니다.<br> 리소스에 참가자를 할당 합니다.|
|[LUIS](https://www.luis.ai)| 새 제작 리소스로 마이그레이션합니다.<br> 마이그레이션 흐름에서 새 제작 리소스를 만듭니다.<br> Azure 리소스 **관리**페이지에서 예측 및 리소스 제작 리소스를 앱에 할당 또는 할당 해제  >  **Azure resources** 합니다. <br> 응용 프로그램을 한 제작 리소스에서 다른 제작 리소스로 이동 합니다.  |

> [!Note]
> F0 계층에 표시 된 대로 LUIS 앱을 제작 하는 것은 무료입니다. [가격 책정 계층에](luis-limits.md#key-limits)대해 자세히 알아보세요.


## <a name="migration-steps"></a>마이그레이션 단계

1. 작업 중인 LUIS 포털의 상단 도구 모음에 있는 **Azure** 아이콘에서 마이그레이션 프로세스를 시작할 수 있습니다.

   > [!div class="mx-imgBorder"]
   > ![마이그레이션 아이콘](./media/migrate-authoring-key/migration-button.png)

2. 마이그레이션 팝업 창에서 마이그레이션을 계속 하거나 나중에 마이그레이션할 수 있습니다. **지금 마이그레이션**을 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![마이그레이션 프로세스의 첫 번째 팝업 창에서 지금 마이그레이션을 선택 합니다.](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. 공동 작업자를 사용 하는 앱이 있는 경우 마이그레이션에 대 한 정보를 알려 주는 전자 메일을 전송 하 라는 메시지가 표시 됩니다. 선택적 단계입니다.

   각 협력자 및 앱에 대해 기본 전자 메일 응용 프로그램은 약간의 서식이 지정 된 전자 메일을 사용 하 여 열립니다. 전자 메일을 보내기 전에 편집할 수 있습니다. 전자 메일 템플릿에는 정확한 앱 ID 및 앱 이름이 포함 됩니다.

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```

   > [!Note]
   > 계정을 Azure로 마이그레이션한 후에는 공동 작업자에 게 더 이상 앱을 사용할 수 없습니다.

4. 응용 프로그램에 대 한 협력자 인 경우 마이그레이션 흐름 중에이 옵션을 선택 하 여 앱의 복사본을 내보내야 한다는 메시지가 표시 됩니다. 선택적 단계입니다.

   옵션을 선택 하면 다음 페이지가 나타납니다. 왼쪽에서 다운로드 단추를 선택 하 여 원하는 앱을 내보냅니다. 이러한 앱은 자동으로 마이그레이션되지 않으므로 마이그레이션한 후에 다시 가져올 수 있습니다.

   > [!div class="mx-imgBorder"]
   > ![응용 프로그램을 내보낼지 묻는 메시지를 표시 합니다.](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. 새 LUIS authoring 리소스를 만들도록 선택 하거나 Azure에서 이미 만든 경우 기존 제작 리소스로 마이그레이션할 수 있습니다. 다음 단추 중 하나를 선택 하 여 원하는 옵션을 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![새 제작 리소스를 만들고 기존 제작 리소스를 사용 하는 단추](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>LUIS에서 마이그레이션할 새 제작 리소스 만들기

새 제작 리소스를 만들려는 경우 **새 제작 리소스 만들기** 를 선택 하 고 다음 창에서 다음 정보를 제공 합니다. 그런 후 **완료**를 선택합니다.

> [!div class="mx-imgBorder"]
> ![제작 리소스를 만들기 위한 창](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **테 넌 트 이름**: Azure 구독과 연결 된 테 넌 트입니다. 이는 기본적으로 현재 사용 중인 테 넌 트로 설정 됩니다. 이니셜을 포함 하는 가장 오른쪽 아바타를 선택 하 여 테 넌 트를 전환할 수 있습니다.
* **리소스 이름**: 선택한 사용자 지정 이름입니다. 작성 및 예측 끝점 쿼리에 대 한 URL의 일부로 사용 됩니다.
* **구독 이름**: 리소스와 연결 되는 구독입니다. 테 넌 트에 속하는 구독이 둘 이상 있는 경우 드롭다운 목록에서 원하는 하나를 선택 합니다.
* **Azure 리소스 그룹 이름**: 드롭다운 목록에서 선택한 사용자 지정 리소스 그룹 이름입니다. 리소스 그룹을 사용하면 액세스 및 관리를 위해 Azure 리소스를 그룹화할 수 있습니다.

구독 당 지역 당 10 개의 무료 제작 리소스를 사용할 수 있습니다. 구독에 동일한 지역에 10 개 이상의 제작 리소스가 있는 경우 새 리소스를 만들 수 없습니다.

제작 리소스를 만들면 성공 메시지가 표시 됩니다. **닫기** 를 선택 하 여 팝업 창을 닫습니다.

  > [!div class="mx-imgBorder"]
  > ![작성 리소스가 성공적으로 생성 되었음을 나타내는 메시지입니다.](./media/migrate-authoring-key/migration-success-2.png)


### <a name="use-existing-authoring-resource-to-migrate"></a>기존 제작 리소스를 사용 하 여 마이그레이션

구독이 이미 LUIS authoring Azure 리소스와 연결 되어 있거나, Azure Portal에서 리소스를 만들고 새 리소스를 만드는 대신 해당 리소스를 마이그레이션하려면 **기존 제작 리소스 사용**을 선택 합니다. 다음 창에서 다음 정보를 입력 하 고 **완료**를 선택 합니다.

> [!div class="mx-imgBorder"]
>![기존 제작 리소스를 변경 하는 창](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **테 넌 트 이름**: Azure 구독과 연결 된 테 넌 트입니다. 이는 기본적으로 현재 사용 중인 테 넌 트로 설정 됩니다.
* **구독 이름**: 리소스와 연결 되는 구독입니다. 테 넌 트에 속하는 구독이 둘 이상 있는 경우 드롭다운 목록에서 원하는 하나를 선택 합니다.
* **리소스 이름**: 마이그레이션할 제작 리소스입니다.

> [!Note]
> 드롭다운 목록에서 제작 리소스를 볼 수 없는 경우에는 로그인 한 LUIS 포털에 따라 적절 한 위치에 해당 리소스를 만들었는지 확인 합니다. 또한 만든 내용이 예측 리소스가 아니라 제작 리소스 인지 확인 합니다.


제작 리소스 이름의 유효성을 검사 하 고 **마이그레이션** 단추를 선택 합니다.

> [!div class="mx-imgBorder"]
> ![마이그레이션 단추를 사용 하 여 제작 리소스를 선택 하는 창](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

성공 메시지가 나타납니다. **닫기** 를 선택 하 여 팝업 창을 닫습니다.

> [!div class="mx-imgBorder"]
> ![제작 리소스가 성공적으로 마이그레이션 되었다는 메시지](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>마이그레이션 후 앱 사용

마이그레이션 프로세스가 완료 되 면 소유자 인 모든 LUIS apps가 단일 LUIS authoring resource에 할당 됩니다.

**내 앱** 목록에는 새 제작 리소스로 마이그레이션된 앱이 표시 됩니다. 앱에 액세스 하기 전에 구독 및 LUIS authoring 리소스를 선택 하 여 작성할 수 있는 앱을 확인 합니다.

 > [!div class="mx-imgBorder"]
 > ![구독 및 제작 리소스에 대 한 상자](./media/create-app-in-portal-select-subscription-luis-resource.png)

LUIS 포털에서 앱을 계속 편집 하기 위해 제작 리소스의 키를 알 필요가 없습니다.

앱을 프로그래밍 방식으로 편집 하려는 경우 제작 키 값이 필요 합니다. 이러한 값은 **Manage**  >  LUIS 포털의**Azure 리소스** 관리 페이지에 표시 됩니다. 리소스 **키** 페이지의 Azure Portal 에서도 사용할 수 있습니다. 또한 더 많은 제작 리소스를 만들어 동일한 페이지에서 할당할 수 있습니다.

 > [!div class="mx-imgBorder"]
 > ![제작 리소스 관리 페이지](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="adding-contributors-to-authoring-resources"></a>제작 리소스에 참가자 추가

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

제작 리소스에 [참가자를 추가 하는 방법을](luis-how-to-collaborate.md) 알아봅니다. 기여자는 해당 리소스의 모든 응용 프로그램에 액세스할 수 있습니다.

Azure Portal, 해당 리소스에 대 한 **Access Control (IAM)** 페이지에서 제작 리소스에 참가자를 추가할 수 있습니다. 자세한 내용은 [참가자 액세스 추가](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)를 참조 하세요.

> [!Note]
> LUIS 앱의 소유자가 Azure 리소스에서 협력자로 협력자를 마이그레이션하고 추가 하는 경우에도 해당 협력자는 마이그레이션하지 않는 한 여전히 앱에 액세스할 수 없습니다.

## <a name="luis-portal-migration-reminders"></a>LUIS 포털 마이그레이션 미리 알림

[LUIS 포털](https://www.luis.ai) 은 마이그레이션 프로세스를 제공 합니다.

이러한 두 조건에 모두 해당 하는 경우 마이그레이션해야 한다는 메시지가 표시 됩니다.
* 제작을 위해 전자 메일 인증 시스템에 앱이 있습니다.
* 사용자는 앱 소유자입니다.

매주 앱을 마이그레이션할지 묻는 메시지가 표시 됩니다. 마이그레이션 없이이 창을 닫을 수 있습니다. 다음 예약 된 기간 전에 마이그레이션하려면 LUIS 포털의 상단 도구 모음에 있는 **Azure** 아이콘에서 마이그레이션 프로세스를 시작할 수 있습니다.

## <a name="prediction-resources-blocking-migration"></a>마이그레이션 차단 예측 리소스
마이그레이션은 응용 프로그램의 모든 런타임에 부정적인 영향을 줍니다. 마이그레이션하면 모든 협력자가 앱에서 제거 되 고 다른 앱의 협력자로 제거 됩니다. 이 프로세스는 협력자가 할당 하는 키도 제거 하 여 프로덕션 환경에 있는 경우 응용 프로그램을 중단 시킬 수 있음을 의미 합니다. 이는 자신에 게 할당 된 협력자 또는 키를 수동으로 제거할 때까지 마이그레이션을 차단 하는 이유입니다.

다음 조건 중 하나에 해당 하는 경우 마이그레이션이 차단 됩니다.

* 소유 하지 않은 앱에서 예측/런타임 리소스를 할당 했습니다.
* 사용자가 소유한 앱에 다른 사용자가 예측/런타임 리소스를 할당 하 고 있습니다.

### <a name="recommended-steps-if-youre-the-owner-of-the-app"></a>앱 소유자 인 경우 권장 되는 단계
일부 응용 프로그램의 소유자이 고 협력자가 이러한 응용 프로그램에 예측/런타임 키를 할당 한 경우를 마이그레이션하면 오류가 표시 됩니다. 이 오류는 다른 사용자가 소유한 예측 키가 할당 된 응용 프로그램 Id를 나열 합니다.

다음을 수행하는 것이 좋습니다.
* 공동 작업자에 게 마이그레이션에 대해 알립니다.
* 오류에 표시 된 응용 프로그램에서 모든 협력자를 제거 합니다.
* 공동 작업자를 수동으로 제거 하는 경우 성공 해야 하는 마이그레이션 프로세스를 진행 합니다.
* 새 제작 리소스에 협력자로 협력자를 할당 합니다. 공동 작업자는 예측 리소스를 마이그레이션하고 응용 프로그램에 다시 할당 합니다. 이로 인해 예측 리소스가 다시 할당 될 때까지 응용 프로그램이 일시적으로 중단 됩니다.

여기에 다른 잠재적인 솔루션이 있습니다. 소유자 마이그레이션 전에 협력자는 Azure Portal에서 Azure 구독의 참가자로 앱 소유자를 추가할 수 있습니다. 이 단계에서는 런타임 예측 리소스에 대 한 소유자 액세스 권한을 부여 합니다. 소유자가 추가 된 새 구독 (새 테 넌 트 아래에 있음)을 사용 하 여 마이그레이션하는 경우,이 단계는 협력자 및 app owner 모두에 대 한 마이그레이션 프로세스의 차단을 해제 하는 것이 아닙니다. 또한 앱을 중단 하지 않고 예측 키가 아직 할당 된 상태에서 앱을 원활 하 게 마이그레이션할 수 있습니다.


### <a name="recommended-steps-if-youre-a-collaborator-on-an-app"></a>앱의 협력자 인 경우 권장 되는 단계
응용 프로그램에 대 한 공동 작업을 하 고 이러한 응용 프로그램에 예측/런타임 키를 할당 한 경우를 마이그레이션하면 오류가 표시 됩니다. 오류는 마이그레이션을 차단 하는 응용 프로그램 Id 및 키 경로를 나열 합니다.

다음을 수행하는 것이 좋습니다.
* 응용 프로그램을 백업으로 내보냅니다. 이는 마이그레이션 프로세스의 선택적 단계입니다.
* Azure 리소스 **관리**페이지에서 예측 리소스의 할당을 취소  >  **Azure resources** 합니다.
* 마이그레이션 프로세스를 진행 합니다.
* 마이그레이션 후 응용 프로그램을 다시 가져옵니다.
* Azure 리소스 **관리**페이지에서 응용 프로그램에 예측 키를 다시 할당  >  **Azure resources** 합니다.

> [!Note]
> 마이그레이션한 후 응용 프로그램을 다시 가져올 때 앱 Id가 서로 다를 수 있습니다. 또한 프로덕션에서 적중 하는 것과는 다릅니다. 이제 이러한 응용 프로그램의 소유자가 됩니다.

## <a name="troubleshooting-the-migration-process"></a>마이그레이션 프로세스 문제 해결

마이그레이션을 시도 하지만 드롭다운 목록에서 Azure 구독을 찾을 수 없는 경우:
* Cognitive Services 리소스를 만들 수 있는 유효한 Azure 구독이 있는지 확인 합니다. [Azure Portal](https://ms.portal.azure.com) 로 이동 하 여 구독의 상태를 확인 합니다. 계정이 없는 경우 [무료 Azure 계정을 만듭니다](https://azure.microsoft.com/free/cognitive-services/).
* 유효한 구독과 연결 된 적절 한 테 넌 트에 있는지 확인 합니다. 이 도구 모음에서 사용자의 이니셜 왼쪽에 있는 테 넌 트를 전환할 수 있습니다. ![ 테 넌 트를 전환할 수 있는 도구 모음:](./media/migrate-authoring-key/switch-user-tenant-2.png)

기존 제작 리소스가 있지만 **기존 제작 리소스 사용** 옵션을 선택 하는 경우이 리소스를 찾을 수 없는 경우:
* 사용자의 리소스는 사용자가 로그인 하는 포털과 다른 위치에서 만들어졌을 것입니다. [LUIS 제작 지역 및 포털](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions)을 확인 합니다.
* 대신 LUIS 포털에서 새 리소스를 만듭니다.

**새 제작 리소스 만들기** 옵션을 선택 하면 "사용자의 Azure 정보를 검색 하지 못했습니다. 나중에 다시 시도 하십시오." 라는 오류 메시지와 함께 마이그레이션이 실패 합니다.
* 구독에는 구독 당 지역 별로 10 개 이상의 제작 리소스가 있을 수 있습니다. 이 경우 새 제작 리소스를 만들 수 없습니다.
* **기존 제작 리소스 사용** 옵션을 선택 하 고 구독에서 기존 리소스 중 하나를 선택 하 여 마이그레이션합니다.

다음 오류가 표시 [되 면 앱 소유자 인 경우 권장 단계](#recommended-steps-if-youre-the-owner-of-the-app)를 확인 합니다.
![소유자에 대 한 마이그레이션이 실패 한 것으로 표시 되는 오류](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

다음 오류가 표시 [되 면 앱의 협력자 인 경우 권장 단계](#recommended-steps-if-youre-a-collaborator-on-an-app)를 확인 합니다.
![협력자의 마이그레이션이 실패 한 것으로 표시 되는 오류](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>다음 단계

* [제작 및 런타임 키에 대 한 개념을](luis-how-to-azure-subscription.md)검토 합니다.
* [키를 할당](luis-how-to-azure-subscription.md) 하 고 [참가자를 추가](luis-how-to-collaborate.md)하는 방법을 검토 합니다.
