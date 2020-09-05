---
title: 여러 Azure 서비스를 사용하여 작업 자동화
description: 자습서 - Azure Logic Apps, Azure Storage 및 Azure Functions를 사용하여 이메일을 처리하는 자동화된 워크플로 만들기
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 02/27/2020
ms.openlocfilehash: 79ce5125283a234530435891044ead3141665433
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89002779"
---
# <a name="tutorial-automate-tasks-to-process-emails-by-using-azure-logic-apps-azure-functions-and-azure-storage"></a>자습서: Azure Logic Apps, Azure Functions 및 Azure Storage를 사용하여 이메일을 처리하는 작업 자동화

Azure Logic Apps를 사용하면 워크플로를 자동화하고 Azure 서비스, Microsoft 서비스, 기타 SaaS(software-as-a-service) 앱 및 온-프레미스 시스템의 데이터를 통합할 수 있습니다. 이 자습서에서는 수신 이메일 및 첨부 파일을 처리하는 [논리 앱](../logic-apps/logic-apps-overview.md)을 빌드하는 방법을 보여줍니다. 이 논리 앱은 이메일 콘텐츠를 분석하고, Azure 스토리지에 콘텐츠를 저장하고, 해당 콘텐츠를 검토하기 위한 알림을 보냅니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 저장된 이메일 및 첨부 파일을 확인할 수 있도록 [Azure 스토리지](../storage/common/storage-introduction.md) 및 Storage Explorer를 설정합니다.
> * 이메일에서 HTML을 제거하는 [Azure 함수](../azure-functions/functions-overview.md)를 만듭니다. 이 자습서에는 이 함수에 사용할 수 있는 코드가 포함되어 있습니다.
> * 빈 논리 앱을 만듭니다.
> * 이메일의 첨부 파일을 모니터링하는 트리거를 추가합니다.
> * 이메일에 첨부 파일이 있는지 확인하는 조건을 추가합니다.
> * 이메일에 첨부 파일이 있으면 Azure 함수를 호출하는 작업을 추가합니다.
> * 이메일 및 첨부 파일에 대한 스토리지 BLOB을 만드는 작업을 추가합니다.
> * 이메일 알림을 보내는 작업을 추가합니다.

여기까지 모두 마치면 논리 앱이 이 워크플로와 비슷하게 보입니다.

![완료된 상위 수준 논리 앱](./media/tutorial-process-email-attachments-workflow/overview.png)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* Office 365 Outlook, Outlook.com, Gmail 등 Logic Apps에서 지원되는 이메일 공급자의 이메일 계정. 다른 공급자에 대한 내용은 [여기서 커넥터 목록을 검토하세요](/connectors/).

  이 논리 앱은 Office 365 Outlook 계정을 사용합니다. 다른 이메일 계정을 사용하는 경우 일반적인 단계는 동일하지만 UI가 약간 다르게 표시될 수 있습니다.

  > [!IMPORTANT]
  > Gmail 커넥터를 사용하려는 경우 G Suite 비즈니스 계정만 논리 앱에서 제한 없이 이 커넥터를 사용할 수 있습니다. Gmail 소비자 계정이 있는 경우 특정 Google 승인 서비스에서만 이 커넥터를 사용하거나 [Gmail 커넥터 인증에 사용할 Google 클라이언트 앱을 만들](/connectors/gmail/#authentication-and-bring-your-own-application) 수 있습니다. 자세한 내용은 [Azure Logic Apps의 Google 커넥터에 대한 데이터 보안 및 개인정보처리방침](../connectors/connectors-google-data-security-privacy-policy.md)을 참조하세요.

* [체험판 Microsoft Azure Storage Explorer](https://storageexplorer.com/)를 다운로드하여 설치합니다. 이 도구를 사용하여 스토리지 컨테이너가 올바르게 설정되었는지 확인할 수 있습니다.

## <a name="set-up-storage-to-save-attachments"></a>첨부 파일을 저장하도록 스토리지 설정

수신 이메일 및 첨부 파일을 [Azure Storage 컨테이너](../storage/common/storage-introduction.md)에 BLOB으로 저장할 수 있습니다.

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 스토리지 컨테이너를 만들려면 먼저 Azure Portal의 **기본** 탭에서 다음 설정을 사용하여 [스토리지 계정 만들기](../storage/common/storage-account-create.md)를 수행해야 합니다.

   | 설정 | 값 | 설명 |
   |---------|-------|-------------|
   | **구독** | <*Azure-subscription-name*> | Azure 구독의 이름 |  
   | **리소스 그룹** | <*Azure-resource-group*> | 관련 리소스를 구성하고 관리하는 데 사용되는 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)의 이름. 이 예제에서는 "LA-Tutorial-RG"를 사용합니다. <p>**참고:** 리소스 그룹은 특정 지역 내에 있습니다. 일부 지역에서 이 자습서의 항목을 사용할 수 없을 수도 있지만, 가능하면 동일한 지역을 사용해 보세요. |
   | **스토리지 계정 이름** | <*Azure-storage-account-name*> | 스토리지 계정 이름은 3-24자여야 하고 소문자와 숫자만 포함할 수 있습니다. 이 예제에서는 "attachmentstorageacct"를 사용합니다. |
   | **위치** | <*Azure-region*> | 스토리지 계정에 대한 정보를 저장할 지역입니다. 이 예제에서는 “미국 서부”를 사용합니다. |
   | **성능** | Standard | 이 설정은 지원되는 데이터 형식 및 데이터를 저장하기 위한 미디어를 지정합니다. [스토리지 계정 유형](../storage/common/storage-introduction.md#types-of-storage-accounts)을 참조하세요. |
   | **계정 종류** | 범용 가상 컴퓨터 | [스토리지 계정 유형](../storage/common/storage-introduction.md#types-of-storage-accounts) |
   | **복제** | LRS(로컬 중복 스토리지) | 이 설정은 데이터가 복사, 저장, 관리 및 동기화되는 방식을 지정합니다. [LRS(로컬 중복 스토리지): Azure Storage에 대한 저렴한 데이터 중복성](../storage/common/storage-redundancy.md)을 참조하세요. |
   | **액세스 계층(기본값)** | 현재 설정을 유지합니다. |
   ||||

   **고급** 탭에서 다음 설정을 선택합니다.

   | 설정 | 값 | 설명 |
   |---------|-------|-------------|
   | **보안 전송 필요** | 사용 안 함 | 이 설정은 연결의 요청에 필요한 보안을 지정합니다. [보안 전송 필요](../storage/common/storage-require-secure-transfer.md)를 참조하세요. |
   ||||

   스토리지 계정을 만들려면 [Azure PowerShell](../storage/common/storage-account-create.md?tabs=powershell) 또는 [Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli)를 사용할 수도 있습니다.

1. 완료되면 **검토 + 만들기**를 선택합니다.

1. Azure가 스토리지 계정을 배포한 후에는 스토리지 계정을 찾아 스토리지 계정의 액세스 키를 가져옵니다.

   1. 스토리지 계정 메뉴의 **설정** 아래에서 **액세스 키**를 선택합니다.

   1. 스토리지 계정 이름과 **key1**을 복사한 다음, 이러한 값을 안전한 곳에 저장합니다.

      ![스토리지 계정 이름과 키를 복사 및 저장](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   스토리지 계정의 액세스 키를 가져오려면 [Azure PowerShell](/powershell/module/az.storage/get-azstorageaccountkey) 또는 [Azure CLI](/cli/azure/storage/account/keys?view=azure-cli-latest.md#az-storage-account-keys-list)를 사용할 수도 있습니다.

1. 이메일 첨부 파일에 대한 Blob Storage 컨테이너를 만듭니다.

   1. 스토리지 계정 메뉴에서 **개요**를 선택합니다. 개요 창에서 **연결**을 선택합니다.

      ![Blob Storage 컨테이너 추가](./media/tutorial-process-email-attachments-workflow/create-storage-container.png)

   1. **컨테이너** 페이지가 열리면 도구 모음에서 **컨테이너**를 선택합니다.

   1. **새 컨테이너**에서 컨테이너 이름으로 `attachments`를 입력합니다. **퍼블릭 액세스 수준**에서 **컨테이너(컨테이너와 Blob에 대한 익명 읽기 권한)**  > **확인**을 선택합니다.

      여기까지 마쳤으면 Azure Portal에서 스토리지 계정의 스토리지 컨테이너를 찾을 수 있습니다.

      ![완료된 스토리지 컨테이너](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

   스토리지 컨테이너를 만들려면 [Azure PowerShell](/powershell/module/az.storage/new-azstoragecontainer) 또는 [Azure CLI](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create)를 사용할 수도 있습니다.

다음으로, Storage Explorer를 스토리지 계정에 연결합니다.

## <a name="set-up-storage-explorer"></a>Storage Explorer 설정

이제 Storage Explorer를 스토리지 계정에 연결하여 논리 앱에서 첨부 파일을 Blob으로 스토리지 컨테이너에 올바르게 저장하는지 확인할 수 있습니다.

1. Microsoft Azure Storage Explorer를 시작합니다.

   Storage Explorer에서 스토리지 계정에 대한 연결을 묻는 메시지를 표시합니다.

1. **Azure Storage에 연결** 창에서 **스토리지 계정 이름 및 키 사용** > **다음**을 선택합니다.

   ![Storage Explorer - 스토리지 계정에 연결](./media/tutorial-process-email-attachments-workflow/storage-explorer-choose-storage-account.png)

   > [!TIP]
   > 메시지가 표시되지 않으면 Storage Explorer 도구 모음에서 **계정 추가**를 선택합니다.

1. **표시 이름**에 연결에 사용할 친숙한 이름을 입력합니다. **계정 이름** 아래에서 스토리지 계정 이름을 제공합니다. **계정 키**에서 이전에 저장한 액세스 키를 입력하고 **다음**을 선택합니다.

1. 연결 정보를 확인한 다음, **연결**을 선택합니다.

   Storage Explorer에서 연결이 생성되고, 탐색기 창의 **로컬 및 첨부** > **스토리지 계정** 아래에 스토리지 계정이 표시됩니다.

1. **스토리지 계정**에서 Blob Storage 컨테이너를 찾으려면 스토리지 계정(여기서는 **attachmentstorageacct**), **Blob 컨테이너**(**attachments** 컨테이너가 있음)를 차례로 확장합니다. 예를 들면 다음과 같습니다.

   ![Storage Explorer - 스토리지 컨테이너 찾기](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

다음으로, 수신 이메일에서 HTML을 제거하는 [Azure 함수](../azure-functions/functions-overview.md)를 만듭니다.

## <a name="create-function-to-clean-html"></a>HTML을 정리하는 함수 만들기

이제 다음 단계에서 제공하는 코드 조각을 사용하여 수신 이메일에서 HTML을 제거하는 Azure 함수를 만듭니다. 이렇게 하면 이메일 콘텐츠를 좀 더 깨끗하게 정리하고 보다 쉽게 처리할 수 있습니다. 그런 후 논리 앱에서 이 함수를 호출할 수 있습니다.

1. 함수를 만들기 전에 다음 설정을 사용하여 [함수 앱을 만듭니다](../azure-functions/functions-create-function-app-portal.md).

   | 설정 | 값 | 설명 |
   | ------- | ----- | ----------- |
   | **앱 이름** | <*function-app-name*> | 함수 앱의 이름은 Azure에서 전역적으로 고유해야 합니다. 이 예에서는 “CleanTextFunctionApp”을 이미 사용하고 있으므로 다른 이름을 입력합니다(예: “MyCleanTextFunctionApp-<*사용자-이름*>”). |
   | **구독** | <*your-Azure-subscription-name*> | 이전에 사용한 동일한 Azure 구독 |
   | **리소스 그룹** | LA-Tutorial-RG | 이전에 사용한 동일한 Azure 리소스 그룹 |
   | **OS** | <*your-operating-system*> | 자주 사용하는 함수 프로그래밍 언어를 지원하는 운영 체제를 선택합니다. 이 예에서는 **Windows**를 선택합니다. |
   | **호스팅 계획** | 소비 계획 | 이 설정은 계산 성능처럼 함수 앱을 실행하기 위한 리소스를 할당하고 크기를 조정하는 방법을 결정합니다. [호스팅 계획 비교](../azure-functions/functions-scale.md)를 참조하세요. |
   | **위치** | 미국 서부 | 이전에 사용한 동일한 지역 |
   | **런타임 스택** | 기본 설정 언어 | 자주 사용하는 함수 프로그래밍 언어를 지원하는 런타임을 선택합니다. C# 및 F# 함수의 경우 **.NET**을 선택합니다. |
   | **스토리지** | cleantextfunctionstorageacct | 함수 앱에 대한 스토리지 계정을 만듭니다. 소문자와 숫자만 사용할 수 있습니다. <p>**참고:** 이 스토리지 계정은 함수 앱을 포함하며, 이메일 첨부 파일에 대해 이전에 만든 스토리지 계정과 다릅니다. |
   | **Application Insights** | 사용 안 함 | [Application Insights](../azure-monitor/app/app-insights-overview.md)를 사용한 애플리케이션 모니터링을 켭니다. 하지만 이 자습서에서는 **사용 안 함** > **적용**을 선택합니다. |
   ||||

   배포 후 함수 앱이 자동으로 열리지 않으면 [Azure Portal](https://portal.azure.com) 검색 상자에서 **함수 앱**을 찾아 선택합니다. **함수 앱**에서 함수 앱을 선택합니다.

   ![함수 앱 선택](./media/tutorial-process-email-attachments-workflow/select-function-app.png)

   그렇지 않으면 Azure에서 다음과 같이 함수 앱을 자동으로 엽니다.

   ![생성된 함수 앱](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   함수 앱을 만들려면 [Azure CLI](../azure-functions/functions-create-first-azure-function-azure-cli.md) 또는 [PowerShell 및 Resource Manager 템플릿](../azure-resource-manager/templates/deploy-powershell.md)을 사용할 수도 있습니다.

1. **함수 앱** 목록에서 함수 앱을 확장합니다(아직 확장되지 않은 경우). 함수 앱에서 **함수**를 선택합니다. 함수 도구 모음에서 **새 함수**를 선택합니다.

   ![새 함수 만들기](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

1. **아래 템플릿 선택 또는 빠른 시작으로 이동**에서 **HTTP 트리거** 템플릿을 선택합니다.

   ![HTTP 트리거 템플릿 선택](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

   Azure는 HTTP 트리거 함수에 대한 언어별 템플릿을 사용하여 함수를 만듭니다.

1. **새 함수** 창의 **이름** 아래에서 `RemoveHTMLFunction`를 입력합니다. **권한 부여 수준**을 **함수**로 유지하고 **만들기**를 선택합니다.

   ![함수 이름 지정](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

1. 편집기가 열리면 템플릿 코드를 이 샘플 코드로 바꿉니다. 이 코드는 HTML을 제거하고 호출자에 결과를 반환합니다.

   ```csharp
   #r "Newtonsoft.Json"

   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   using Newtonsoft.Json;
   using System.Text.RegularExpressions;

   public static async Task<IActionResult> Run(HttpRequest req, ILogger log) {

      log.LogInformation("HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await new StreamReader(req.Body).ReadToEndAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return (ActionResult)new OkObjectResult(new { updatedBody });
   }
   ```

1. 완료되면 **저장**을 선택합니다. 함수를 테스트하려면 편집기의 오른쪽 가장자리에 있는 화살표( **<** ) 아이콘 아래에서 **테스트**를 선택합니다.

   !["테스트" 창을 엽니다.](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

1. **테스트** 창의 **요청 본문** 아래에서 다음 줄을 입력하고 **실행**을 선택합니다.

   `{"name": "<p><p>Testing my function</br></p></p>"}`

   ![함수 테스트](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   **출력** 창에 함수의 결과가 표시됩니다.

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

함수가 작동하는 것을 확인했으면 논리 앱을 만듭니다. 이 자습서에서는 이메일에서 HTML을 제거하는 함수를 만드는 방법을 보여 주지만, Logic Apps는 **HTML-Text** 커넥터도 제공합니다.

## <a name="create-your-logic-app"></a>논리 앱 만들기

1. Azure 최상위 검색 상자에 `logic apps`를 입력하고, **Logic Apps**를 선택합니다.

   !["Logic Apps" 찾기 및 선택](./media/tutorial-process-email-attachments-workflow/find-select-logic-apps.png)

1. **Logic Apps** 창에서 **추가**를 선택합니다.

   ![새 논리 앱 추가](./media/tutorial-process-email-attachments-workflow/add-new-logic-app.png)

1. 여기에 표시된 것처럼 **논리 앱** 창에서 논리 앱에 대한 정보를 제공합니다. 완료되면 **검토 + 만들기**를 선택합니다.

   ![논리 앱 정보 제공](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | 설정 | 값 | 설명 |
   | ------- | ----- | ----------- |
   | **구독** | <*your-Azure-subscription-name*> | 이전에 사용한 동일한 Azure 구독 |
   | **리소스 그룹** | LA-Tutorial-RG | 이전에 사용한 동일한 Azure 리소스 그룹 |
   | **논리 앱 이름** | LA-ProcessAttachment | 논리 앱의 이름 |
   | **위치 선택** | 미국 서부 | 이전에 사용한 동일한 지역 |
   | **Log Analytics** | 꺼짐 | 이 자습서에서는 **해제** 설정을 선택합니다. |
   ||||

1. Azure가 앱을 배포한 후 Azure 도구 모음에서 알림 아이콘을 선택하고 **리소스로 이동**을 선택합니다.

   ![Azure 알림 목록에서 “리소스로 이동”을 선택합니다.](./media/tutorial-process-email-attachments-workflow/go-to-new-logic-app-resource.png)

1. Logic Apps 디자이너가 열리고 일반적인 논리 앱 패턴에 대한 소개 비디오 및 템플릿이 포함된 페이지가 표시됩니다. **템플릿** 아래에서 **빈 논리 앱**을 선택합니다.

   ![빈 논리 앱 템플릿 선택](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

다음으로, 첨부 파일이 있는 수신 이메일을 수신 대기하는 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 추가합니다. 모든 논리 앱은 특정 이벤트가 발생하거나 새 데이터가 특정 조건을 충족할 때 실행되는 트리거를 통해 시작되어야 합니다. 자세한 내용은 [첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="monitor-incoming-email"></a>수신 이메일 모니터링

1. 디자이너의 검색 상자에서 필터로 `when new email arrives`를 입력합니다. 이메일 공급자에 대해 **새 이메일이 도착하는 경우 - <*your-email-provider*>** 트리거를 선택합니다.

   예를 들면 다음과 같습니다.

   ![이메일 공급자에 대해 "새 이메일이 도착하는 경우" 트리거 선택](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * Azure 회사 또는 학교 계정에서 Office 365 Outlook을 선택합니다.

   * Microsoft 개인 계정에서 Outlook.com을 선택합니다.

1. 자격 증명을 요청하는 메시지가 표시되면 Logic Apps에서 이메일 계정에 연결할 수 있도록 이메일 계정에 로그인합니다.

1. 이제 트리거에서 새 이메일을 필터링하는 데 사용하는 조건을 입력합니다.

   1. 이메일 확인을 위해 아래에서 설명하는 설정을 지정합니다.

      ![이메일 확인에 사용할 폴더, 간격 및 빈도 지정](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | 설정 | 값 | 설명 |
      | ------- | ----- | ----------- |
      | **폴더** | 받은 편지함 | 확인할 이메일 폴더 |
      | **첨부 파일 있음** | 예 | 첨부 파일이 있는 이메일만 받습니다. <p>**참고:** 이 트리거는 계정에서 이메일을 제거하지 않으며, 새 메시지만 확인하고 제목 필터와 일치하는 이메일만 처리합니다. |
      | **첨부 파일 포함** | 예 | 첨부 파일을 확인하는 데서 그치지 않고 첨부 파일을 워크플로의 입력으로 가져옵니다. |
      | **간격** | 1 | 검사 간에 대기하는 간격의 수 |
      | **빈도** | Minute | 검사 간 간격의 시간 단위 |
      ||||

   1. **새 매개 변수 추가** 목록에서 **제목 필터**를 선택합니다.

   1. **제목 필터** 상자가 작업에 나타나면 여기에 나열된 제목을 지정합니다.

      | 설정 | 값 | 설명 |
      | ------- | ----- | ----------- |
      | **제목 필터** | `Business Analyst 2 #423501` | 이메일 제목에서 찾을 텍스트 |
      ||||

1. 지금은 트리거 세부 정보를 숨기려면 트리거의 제목 표시줄 내부를 클릭합니다.

   ![세부 정보를 숨기려면 셰이프 축소](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

   이제 논리 앱이 라이브 상태지만 이메일 확인 외에는 아무 것도 수행하지 않습니다. 다음으로, 워크플로를 계속하는 조건을 지정하는 조건을 추가합니다.

## <a name="check-for-attachments"></a>첨부 파일 확인

이제 첨부 파일이 있는 이메일만 선택하는 조건을 추가합니다.

1. 트리거 아래에서 **새 단계**를 선택합니다.

   !["새 단계"](./media/tutorial-process-email-attachments-workflow/add-condition-under-trigger.png)

1. **작업 선택** 아래의 검색 상자에 `condition`을 입력합니다. 현재 선택한 작업: **Condition**

   !["조건" 선택](./media/tutorial-process-email-attachments-workflow/select-condition.png)

   1. 보다 구체적인 설명이 포함되도록 조건 이름을 바꿉니다. 조건의 제목 표시줄에서 줄임표( **...** ) 단추 > **이름 바꾸기**를 선택합니다.

      ![조건 이름 바꾸기](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

   1. `If email has attachments and key subject phrase` 설명이 포함되도록 조건 이름을 바꿉니다.

1. 첨부 파일이 있는 이메일을 확인하는 조건을 만듭니다.

   1. **And** 아래의 첫 번째 행에서 왼쪽 상자 내부를 클릭합니다. 표시되는 동적 콘텐츠 목록에서 **첨부 파일 있음** 속성을 선택합니다.

      ![조건 작성](./media/tutorial-process-email-attachments-workflow/build-condition.png)

   1. 중간 상자에서 **이(가) 다음과 같은 경우** 연산자를 유지합니다.

   1. 오른쪽 상자에서 트리거의 **첨부 파일 있음** 속성 값과 비교할 값으로 **True**를 입력합니다.

      ![조건 작성](./media/tutorial-process-email-attachments-workflow/finished-condition.png)

      두 값이 같으면 이메일에 첨부 파일이 하나 이상 있는 것이며, 조건을 통과하고, 워크플로가 계속됩니다.

   코드 편집기 창에서 볼 수 있는 기본 논리 앱 정의에서 이 조건은 다음 예제와 같습니다.

   ```json
   "Condition": {
      "actions": { <actions-to-run-when-condition-passes> },
      "expression": {
         "and": [ {
            "equals": [
               "@triggerBody()?['HasAttachment']",
                 "true"
            ]
         } ]
      },
      "runAfter": {},
      "type": "If"
   }
   ```

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

### <a name="test-your-condition"></a>조건 테스트

이제 조건이 올바르게 작동하는지 테스트합니다.

1. 아직 논리 앱을 실행하고 있지 않으면 디자이너 도구 모음에서 **실행**을 선택합니다.

   이 단계에서는 지정된 간격이 경과할 때까지 기다릴 필요 없이 논리 앱을 수동으로 시작합니다. 하지만 받은 편지함에 테스트 이메일이 도착할 때까지는 아무 일도 발생하지 않습니다.

1. 다음 기준을 충족하는 이메일을 자신에게 보냅니다.

   * 트리거의 **제목 필터**에서 지정한 텍스트(`Business Analyst 2 #423501`)가 이메일의 제목에 포함되어 있습니다.

   * 이메일에 첨부 파일이 하나 있습니다. 이제 빈 텍스트 파일을 하나 만들고 해당 파일을 이메일에 첨부합니다.

   이메일이 도착하면 논리 앱에서는 첨부 파일 및 지정된 제목 텍스트를 확인합니다. 조건을 통과하면 트리거가 실행되고 Logic Apps 엔진이 논리 앱 인스턴스를 만들고 워크플로를 시작합니다.

1. 트리거가 실행되고 논리 앱이 성공적으로 실행되었는지 확인하려면 논리 앱 메뉴에서 **개요**를 선택합니다.

   ![트리거 및 실행 기록 확인](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   트리거가 성공적으로 실행되었지만 논리 앱이 트리거되지 않았거나 실행되지 않은 경우 [논리 앱 문제 해결](../logic-apps/logic-apps-diagnosing-failures.md)을 참조하세요.

다음으로, **true인 경우** 분기에 대해 수행할 작업을 정의합니다. 첨부 파일과 함께 이메일을 저장하려면 이메일 본문에서 HTML을 제거한 후 이메일 및 첨부 파일용 스토리지 컨테이너에 BLOB을 만듭니다.

> [!NOTE]
> 이메일에 첨부 파일이 없는 경우에는 논리 앱이 **false인 경우** 분기에 대해 아무 것도 할 필요가 없습니다.
> 이 자습서를 마친 후 추가 연습으로 **false인 경우** 분기에 대해 수행할 적절한 작업을 추가할 수 있습니다.

## <a name="call-removehtmlfunction"></a>RemoveHTMLFunction 호출

이 단계에서는 이전에 만든 Azure 함수를 논리 앱에 추가하고, 이메일 트리거의 이메일 본문 내용을 이 함수로 전달합니다.

1. 논리 앱 메뉴에서 **논리 앱 디자이너**를 선택합니다. **true인 경우** 분기에서 **작업 추가**를 선택합니다.

   !["True인 경우" 내에서 작업 추가](./media/tutorial-process-email-attachments-workflow/if-true-add-action.png)

1. 검색 상자에서 "azure 함수"를 찾아 **Azure 함수 선택 - Azure Functions** 작업을 선택합니다.

   !["Azure 함수 선택" 작업 선택](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

1. 이전에 만든 함수 앱(이 예에서는 `CleanTextFunctionApp`)을 선택합니다.

   ![Azure 함수 앱 선택](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

1. 이제 **RemoveHTMLFunction** 함수를 선택합니다.

   ![Azure 함수 선택](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

1. `Call RemoveHTMLFunction to clean email body` 설명을 사용하여 함수 셰이프 이름을 바꿉니다.

1. 이제 함수에서 처리할 입력을 지정합니다.

   1. **요청 본문** 아래에서 후행 공백이 있는 다음 텍스트를 입력합니다.

      `{ "emailBody":`

      다음 단계에서 이 입력을 처리하는 동안 입력이 JSON 형식으로 올바르게 지정될 때까지 잘못된 JSON에 대한 오류가 표시됩니다. 앞에서 이 함수를 테스트할 때 이 함수에 지정된 입력에서 JSON(JavaScript Object Notation)을 사용했습니다. 따라서 요청 본문에서도 동일한 형식을 사용해야 합니다.

      또한 커서가 **요청 본문** 상자 안에 있으면 이전 작업에서 사용할 수 있는 속성 값을 선택할 수 있도록 동적 콘텐츠 목록이 표시됩니다.

   1. 동적 콘텐츠 목록의 **새 이메일이 도착하는 경우** 아래에서 **본문** 속성을 선택합니다. 이 속성 뒤에는 닫는 중괄호(`}`)를 추가해야 합니다.

      ![함수로 전달할 요청 본문 지정](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   완료되면 함수에 대한 입력이 다음 예제와 같습니다.

   ![함수에 전달할 요청 본문을 완성했습니다.](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing-2.png)

1. 논리 앱을 저장합니다.

다음으로, 이메일 본문을 저장할 수 있도록 스토리지 컨테이너에 Blob을 만드는 작업을 추가합니다.

## <a name="create-blob-for-email-body"></a>이메일 본문용 BLOB 만들기

1. **true인 경우** 블록의 Azure 함수 아래에 **작업 추가**를 선택합니다.

1. 검색 상자에서 필터로 `create blob`을 입력하고 다음 작업을 선택합니다. **Blob 만들기**

   ![이메일 본문용 BLOB을 만드는 작업 추가](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

1. 아래에 나와 있는 대로 이러한 설정을 사용하여 스토리지 계정에 대한 연결을 만듭니다. 완료되면 **만들기**를 선택합니다.

   ![스토리지 계정에 연결](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | 설정 | 값 | Description |
   | ------- | ----- | ----------- |
   | **연결 이름** | AttachmentStorageConnection | 연결에 대해 설명하는 이름 |
   | **Storage 계정** | attachmentstorageacct | 앞에서 첨부 파일 저장용으로 만든 스토리지 계정의 이름 |
   ||||

1. **설명이 포함되도록**BLOB 만들기`Create blob for email body` 작업 이름을 바꿉니다.

1. **Blob 만들기** 작업에서 이 정보를 입력하고, Blob을 만들기 위해 아래에 나와 있는 대로 이러한 필드를 선택합니다.

   ![이메일 본문에 대한 BLOB 정보 제공](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | 설정 | 값 | 설명 |
   | ------- | ----- | ----------- |
   | **폴더 경로** | /attachments | 앞에서 만든 컨테이너의 경로 및 이름입니다. 이 예제에서는 폴더 아이콘을 클릭한 다음, "/attachments" 컨테이너를 선택합니다. |
   | **Blob 이름** | **보내는 사람** 필드 | 이 예제에서는 보낸 사람의 이름을 Blob의 이름으로 사용합니다. 이 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시한 다음, **새 이메일이 도착하는 경우** 작업 아래에서 **보내는 사람** 필드를 선택합니다. |
   | **BLOB 콘텐츠** | **콘텐츠** 필드 | 이 예제에서는 HTML이 없는 이메일 본문을 Blob 콘텐츠로 사용합니다. 이 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시한 다음, **RemoveHTMLFunction을 호출하여 이메일 본문 정리** 아래에서 **본문**을 선택합니다. |
   ||||

   완료되면 작업은 다음 예제와 같습니다.

   !["Blob 만들기" 작업 완료](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body-done.png)

1. 논리 앱을 저장합니다.

### <a name="check-attachment-handling"></a>첨부 파일 처리 확인

이제 논리 앱이 사용자가 지정한 방식으로 이메일을 처리하는지 테스트합니다.

1. 아직 논리 앱을 실행하고 있지 않으면 디자이너 도구 모음에서 **실행**을 선택합니다.

1. 다음 기준을 충족하는 이메일을 자신에게 보냅니다.

   * 트리거의 **제목 필터**에서 지정한 텍스트(`Business Analyst 2 #423501`)가 이메일의 제목에 포함되어 있습니다.

   * 이메일에 첨부 파일이 하나 이상 있습니다. 이제 빈 텍스트 파일을 하나 만들고 해당 파일을 이메일에 첨부합니다.

   * 이메일 본문에 테스트 콘텐츠가 포함되어 있으며 예를 들면 다음과 같습니다. `Testing my logic app`

   트리거가 성공적으로 실행되었지만 논리 앱이 트리거되지 않았거나 실행되지 않은 경우 [논리 앱 문제 해결](../logic-apps/logic-apps-diagnosing-failures.md)을 참조하세요.

1. 논리 앱이 이메일을 올바른 스토리지 컨테이너에 저장했는지 확인합니다.

   1. Storage Explorer에서 **로컬 및 첨부** > **스토리지 계정** > **attachmentstorageacct(키)**  > **Blob 컨테이너** > **첨부 파일**을 확장합니다.

   1. 이메일의 **첨부 파일** 컨테이너를 선택합니다.

      아직 논리 앱에서 처리한 첨부 파일이 없기 때문에 지금은 컨테이너에 이메일만 표시됩니다.

      ![저장된 이메일의 Storage Explorer 확인](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   1. 작업을 마쳤으면 Storage Explorer에서 이메일을 삭제합니다.

1. 필요에 따라**false인 경우** 분기(지금은 아무 작업도 수행하지 않음)를 테스트하려면 조건을 만족하지 않는 이메일을 보내면 됩니다.

다음으로, 모든 이메일 첨부 파일을 처리할 루프를 추가합니다.

## <a name="process-attachments"></a>첨부 파일 처리

이메일의 각 첨부 파일을 처리하려면 논리 앱의 워크플로에 **For each** 루프를 추가합니다.

1. **이메일 본문용 Blob 만들기** 셰이프 아래에서 **작업 추가**를 선택합니다.

   !["for each" 루프 추가](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

1. **작업 선택** 아래의 검색 상자에 `for each`를 필터로 입력하고, 다음 작업을 선택합니다. **For each**

   !["For each" 선택](./media/tutorial-process-email-attachments-workflow/select-for-each.png)

1. `For each email attachment` 설명이 포함되도록 루프 이름을 바꿉니다.

1. 이제 루프에서 처리할 데이터를 지정합니다. **이전 단계에서 출력 선택** 상자 내부를 클릭하여 동적 콘텐츠 목록을 연 다음, **첨부 파일**을 선택합니다.

   !["첨부 파일" 선택](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   **첨부 파일** 필드는 이메일에 포함된 모든 첨부 파일이 있는 배열을 전달합니다. **For each** 루프는 배열을 통해 전달되는 항목마다 작업을 반복합니다.

1. 논리 앱을 저장합니다.

다음으로, 각 첨부 파일을 **첨부 파일** 스토리지 컨테이너에 BLOB으로 저장하는 작업을 추가합니다.

## <a name="create-blob-for-each-attachment"></a>For each 첨부 파일용 Blob 만들기

1. 검색된 각 첨부 파일에서 수행할 작업을 지정할 수 있도록 **각 이메일 첨부 파일** 루프에서 **작업 추가**를 선택합니다.

   ![루프에 작업 추가](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

1. 검색 상자에 필터로 `create blob`을 입력한 다음, 다음 작업을 선택합니다. **Blob 만들기**

   ![BLOB을 만드는 작업 추가](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

1. **설명이 포함되도록**BLOB 2 만들기`Create blob for each email attachment` 작업 이름을 바꿉니다.

1. **For each 이메일 첨부파일용 Blob 만들기** 작업에서 이 정보를 제공하고, 아래에 나와 있는 대로 만들려는 각 Blob에 대한 속성을 선택합니다.

   ![Blob 정보 제공](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | 설정 | 값 | 설명 |
   | ------- | ----- | ----------- |
   | **폴더 경로** | /attachments | 앞에서 만든 컨테이너의 경로 및 이름입니다. 이 예제에서는 폴더 아이콘을 클릭한 다음, "/attachments" 컨테이너를 선택합니다. |
   | **Blob 이름** | **이름** 필드 | 이 예제에서는 첨부 파일의 이름을 Blob의 이름으로 사용합니다. 이 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시한 다음, **새 이메일이 도착하는 경우** 작업 아래에서 **이름** 필드를 선택합니다. |
   | **BLOB 콘텐츠** | **콘텐츠** 필드 | 이 예제에서는 **콘텐츠** 필드를 Blob 콘텐츠로 사용합니다. 이 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시한 다음, **새 이메일이 도착하는 경우** 작업 아래에서 **콘텐츠** 필드를 선택합니다. |
   ||||

   완료되면 작업은 다음 예제와 같습니다.

   !["Blob 만들기" 작업 완료](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment-done.png)

1. 논리 앱을 저장합니다.

### <a name="check-attachment-handling"></a>첨부 파일 처리 확인

다음으로, 논리 앱이 사용자가 지정한 방식으로 첨부 파일을 처리하는지 테스트합니다.

1. 아직 논리 앱을 실행하고 있지 않으면 디자이너 도구 모음에서 **실행**을 선택합니다.

1. 다음 기준을 충족하는 이메일을 자신에게 보냅니다.

   * 트리거의 **제목 필터** 속성에서 지정한 텍스트(`Business Analyst 2 #423501`)가 이메일 제목에 포함되어 있습니다.

   * 이메일에 첨부 파일이 두 개 이상 있습니다. 이제 빈 텍스트 파일을 두 개 만들고 해당 파일을 이메일에 첨부합니다.

   트리거가 성공적으로 실행되었지만 논리 앱이 트리거되지 않았거나 실행되지 않은 경우 [논리 앱 문제 해결](../logic-apps/logic-apps-diagnosing-failures.md)을 참조하세요.

1. 논리 앱이 이메일과 첨부 파일을 올바른 스토리지 컨테이너에 저장했는지 확인합니다.

   1. Storage Explorer에서 **로컬 및 첨부** > **스토리지 계정** > **attachmentstorageacct(키)**  > **Blob 컨테이너** > **첨부 파일**을 확장합니다.

   1. 이메일 및 첨부 파일용 **첨부 파일** 컨테이너를 확인합니다.

      ![저장된 이메일 및 첨부 파일 확인](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   1. 작업을 마쳤으면 Storage Explorer에서 이메일 및 첨부 파일을 삭제합니다.

다음으로, 논리 앱에서 첨부 파일을 검토하라는 이메일을 보낼 수 있도록 작업을 추가합니다.

## <a name="send-email-notifications"></a>이메일 알림 보내기

1. **true인 경우** 분기의 **For each 이메일 첨부 파일** 루프 아래에서 **작업 추가**를 선택합니다.

   !["for each" 루프에서 작업 추가](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

1. 검색 상자에서 필터로 `send email`을 입력한 다음, 이메일 공급자에 대해 “이메일 보내기” 작업을 선택합니다.

   작업 목록을 특정 서비스로 필터링하려면 먼저 커넥터를 선택할 수 있습니다.

   ![이메일 공급자에 대해 "이메일 보내기" 작업 선택](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * Azure 회사 또는 학교 계정에서 Office 365 Outlook을 선택합니다.

   * Microsoft 개인 계정에서 Outlook.com을 선택합니다.

1. 자격 증명을 입력하라는 메시지가 나타나면 Logic Apps가 이메일 계정에 대한 연결을 만들 수 있도록 이메일 계정에 로그인합니다.

1. `Send email for review` 설명이 포함되도록 **이메일 보내기** 작업 이름을 바꿉니다.

1. 이 작업에 대한 정보를 입력하고, 아래에 나와 있는 대로 이메일에 포함할 필드를 선택합니다. 편집 상자에서 빈 줄을 추가하려면 Shift + Enter 키를 누릅니다.

   ![이메일 알림 보내기](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   동적 콘텐츠 목록에서 필요한 필드를 찾을 수 없는 경우 **새 이메일이 도착하는 경우** 옆에 있는 **자세히 보기**를 선택합니다.

   | 설정 | 값 | 메모 |
   | ------- | ----- | ----- |
   | **수행할 작업** | <*recipient-email-address*> | 자신의 이메일 주소를 사용하여 테스트할 수 있습니다. |
   | **Subject**  | ```ASAP - Review applicant for position:``` **제목** | 포함하려는 이메일 제목입니다. 이 상자 내부를 클릭하고, 예제 텍스트를 입력한 다음, 동적 콘텐츠 목록의 **새 이메일이 도착하는 경우** 아래에서 **제목** 필드를 선택합니다. |
   | **본문** | ```Please review new applicant:``` <p>```Applicant name:``` **보낸 사람** <p>```Application file location:``` **경로** <p>```Application email content:``` **본문** | 이메일의 본문 콘텐츠입니다. 이 상자 내부를 클릭하고, 예제 텍스트를 입력한 다음, 동적 콘텐츠 목록에서 다음 필드를 선택합니다. <p>- **새 이메일 도착하는 경우** 아래의 **보내는 사람** 필드 </br>- **이메일 본문용 BLOB 만들기** 아래의 **경로** 필드 </br>- **RemoveHTMLFunction을 호출하여 이메일 본문 지우기** 아래의 **본문** 필드 |
   ||||

   > [!NOTE]
   > 첨부 파일이 포함된 배열인 **콘텐츠** 필드와 같은 배열이 있는 필드를 선택하면, 디자이너에서 해당 필드를 참조하는 작업 주위에 "For each" 루프를 자동으로 추가합니다.
   > 그렇게 하면 논리 앱이 각 배열 항목에서 해당 작업을 수행할 수 있습니다.
   > 루프를 제거하려면 배열에 대한 필드를 제거하고, 참조하는 작업을 루프 외부로 이동하고, 루프의 제목 표시줄에서 줄임표( **...** )를 선택한 후, **삭제**를 선택합니다.

1. 논리 앱을 저장합니다.

이제 다음 예제와 같이 논리 앱을 테스트합니다.

![완료된 논리 앱](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>논리 앱 실행

1. 다음 기준을 충족하는 이메일을 자신에게 보냅니다.

   * 트리거의 **제목 필터** 속성에서 지정한 텍스트(`Business Analyst 2 #423501`)가 이메일 제목에 포함되어 있습니다.

   * 이메일에 첨부 파일이 하나 이상 있습니다. 이전 단계에서 만든 빈 텍스트 파일을 다시 사용할 수 있습니다. 보다 현실적인 시나리오를 원한다면 이력서 파일을 첨부합니다.

   * 이메일 본문에 다음 텍스트가 있으며, 이 텍스트를 복사하여 붙여넣을 수 있습니다.

     ```text

     Name: Jamal Hartnett

     Street address: 12345 Anywhere Road

     City: Any Town

     State or Country: Any State

     Postal code: 00000

     Email address: jamhartnett@outlook.com

     Phone number: 000-000-0000

     Position: Business Analyst 2 #423501

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  

     Certifications: Six Sigma Green Belt, Lean Project Management

     Language skills: English, Mandarin, Spanish

     Education: Master of Business Administration
     ```

1. 논리 앱을 실행합니다. 성공적으로 실행되면 논리 앱에서 이 예제와 비슷한 이메일을 보냅니다.

   ![논리 앱에서 보낸 이메일 알림](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   이메일을 받지 못한 경우 이메일의 정크 폴더를 확인합니다. 이메일 정크 필터가 이러한 종류의 메일을 리디렉션할 수 있습니다. 그렇지 않으면 논리 앱이 올바르게 실행되는지 모르는 경우 [논리 앱 문제 해결](../logic-apps/logic-apps-diagnosing-failures.md)을 참조하세요.

축하드립니다. 여러 Azure 서비스에서 작업을 자동화하고 일부 사용자 지정 코드를 호출하는 논리 앱을 만들어서 실행하셨습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 샘플이 더 이상 필요 없으면 논리 앱 및 관련 리소스가 포함된 리소스 그룹을 삭제합니다.

1. 최상위 Azure 검색 상자에 `resources groups`를 입력하고, **리소스 그룹**을 선택합니다.

   !["리소스 그룹"을 찾아 선택합니다.](./media/tutorial-process-email-attachments-workflow/find-azure-resource-groups.png)

1. **리소스 그룹** 목록에서 이 자습서에 대한 리소스 그룹을 선택합니다. 

   ![자습서에 대한 리소스 그룹 찾기](./media/tutorial-process-email-attachments-workflow/find-select-tutorial-resource-group.png)

1. **개요** 창에서 **리소스 그룹 삭제**를 선택합니다.

   ![논리 앱 리소스 그룹 삭제](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

1. 확인 창이 표시되면 리소스 그룹 이름을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Storage 및 Azure Functions 같은 Azure 서비스를 통합하여 이메일 첨부 파일을 처리하고 저장하는 논리 앱을 만들었습니다. 지금부터는 논리 앱을 빌드하는 데 사용할 수 있는 다른 커넥터에 대해서 알아보세요.

> [!div class="nextstepaction"]
> [Logic Apps용 커넥터 자세히 알아보기](../connectors/apis-list.md)
