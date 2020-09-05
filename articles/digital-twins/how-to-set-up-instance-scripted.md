---
title: 인스턴스 및 인증 설정(스크립팅됨)
titleSuffix: Azure Digital Twins
description: 자동화 된 배포 스크립트를 실행 하 여 Azure Digital Twins 서비스의 인스턴스를 설정 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5d41a9b638ab023d045060e14488e91dca07b10f
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181377"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Azure Digital Twins 인스턴스 및 인증 (스크립팅된) 설정

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

이 문서에서는 인스턴스를 만들고 인증을 설정 하는 등 **새 Azure Digital Twins 인스턴스를 설정**하는 단계에 대해 설명 합니다. 이 문서를 완료 한 후에는에 대 한 프로그래밍을 시작할 준비가 된 Azure Digital Twins 인스턴스를 사용할 수 있습니다.

이 버전의이 문서에서는 프로세스를 간소화 하는 [ **자동화 된 배포 스크립트** 샘플](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/) 을 실행 하 여 이러한 단계를 완료 합니다. 
* 백그라운드에서 스크립트를 실행 하는 수동 CLI 단계를 보려면이 문서의 CLI 버전 [*(방법: 인스턴스 및 인증 (CLI) 설정)*](how-to-set-up-instance-cli.md)을 참조 하세요.
* Azure Portal에 따라 수동 단계를 보려면이 문서의 포털 버전 [*(방법: 인스턴스 및 인증 설정) (포털)*](how-to-set-up-instance-portal.md)을 참조 하세요.

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>배포 스크립트 실행

이 문서에서는 azure digital Twins 코드 샘플을 사용 하 여 Azure Digital Twins 인스턴스 및 필요한 인증을 반 자동으로 배포 합니다. 또한 스크립팅된 상호 작용을 직접 작성 하기 위한 시작 지점으로 사용할 수 있습니다.

샘플 스크립트는 PowerShell로 작성 됩니다. 이 샘플은 샘플 링크로 이동 하 고 제목 아래에 있는 *ZIP 다운로드* 단추를 선택 하 여 컴퓨터에 다운로드할 수 있는 [Azure Digital twins 샘플](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/)의 일부입니다.

다운로드 한 샘플 폴더에서 배포 스크립트는 _Azure_Digital_Twins_samples.zip > 스크립트 > **deploy.ps1** _에 있습니다.

Cloud Shell에서 배포 스크립트를 실행 하는 단계는 다음과 같습니다.
1. 브라우저에서 [Azure Cloud Shell](https://shell.azure.com/) 창으로 이동 합니다. 다음 명령을 사용 하 여 로그인 합니다.
    ```azurecli-interactive
    az login
    ```
    CLI는 기본 브라우저를 열 수 있으면 기본 브라우저를 열고 Azure 로그인 페이지를 로드합니다. 그렇지 않으면 *https://aka.ms/devicelogin*에서 브라우저 페이지를 열고 터미널에 표시된 권한 부여 코드를 입력합니다.
 
2. 로그인 한 후 Cloud Shell 창 아이콘 표시줄을 찾습니다. "파일 업로드/다운로드" 아이콘을 선택 하 고 "업로드"를 선택 합니다.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="업로드 옵션 선택 항목을 보여 주는 Cloud Shell 창":::

    컴퓨터에서 _**deploy.ps1**_ 파일로 이동 하 고 "열기"를 누릅니다. 그러면 Cloud Shell 창에서 실행할 수 있도록 Cloud Shell에 파일이 업로드 됩니다.

3. Cloud Shell 창에서 명령을 전송 하 여 스크립트를 실행 `./deploy.ps1` 합니다. 스크립트가 자동화 된 설치 단계를 실행 하면 다음 값을 전달 하 라는 메시지가 표시 됩니다.
    * 인스턴스: 사용할 Azure 구독의 *구독 ID*
    * 인스턴스의 경우 인스턴스를 배포 하려는 *위치* 입니다. Azure Digital Twins를 지 원하는 지역을 확인 하려면 [*지역별 제공 되는 azure 제품*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)을 방문 하세요.
    * 인스턴스의 경우: *리소스 그룹* 이름입니다. 기존 리소스 그룹을 사용 하거나 새 이름을 입력 하 여 만들 수 있습니다.
    * 인스턴스의 경우: Azure Digital Twins 인스턴스의 *이름* 입니다. 새 인스턴스의 이름은 구독에 대 한 지역 내에서 고유 해야 합니다. 즉, 사용자가 선택한 이름을 이미 사용 하 고 있는 지역에 다른 Azure 디지털 Twins 인스턴스가 있는 경우 다른 이름을 선택 하 라는 메시지가 표시 됩니다.
    * 앱 등록: 등록에 연결할 *AZURE AD 응용 프로그램 표시 이름* 입니다. 이 앱 등록에서는 [Azure Digital Twins api](how-to-use-apis-sdks.md)에 대 한 액세스 권한을 구성 합니다. 나중에 클라이언트 앱이 앱 등록에 대해 인증 되 고, 그 결과 Api에 대해 구성 된 액세스 권한이 부여 됩니다.
    * 앱 등록의 경우: Azure AD 응용 프로그램에 대 한 *AZURE ad 응용 프로그램 회신 URL* 입니다. `http://localhost`을 사용합니다. 이 스크립트는 *공용 클라이언트/네이티브 (모바일 & 데스크톱)* URI를 설정 합니다.

이 스크립트는 azure Digital Twins 인스턴스를 만들고, azure 사용자에 게 인스턴스에 대 한 azure *Digital Twins 소유자 (미리 보기)* 역할을 할당 하 고, 사용할 클라이언트 앱에 대 한 azure AD 앱 등록을 설정 합니다.

다음은 스크립트에서 출력 로그를 발췌 한 것입니다.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="배포 스크립트를 실행 하 여 입력 및 출력 로그를 표시 하는 Cloud Shell 창" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

스크립트가 성공적으로 완료 되 면 최종 인쇄물은로 표시 됩니다 `Deployment completed successfully` . 그렇지 않으면 오류 메시지를 해결 하 고 스크립트를 다시 실행 합니다. 이미 완료 한 단계를 무시 하 고 종료 된 지점에서 입력 요청을 다시 시작 합니다.

스크립트가 완료 되 면 이제 Azure Digital Twins 인스턴스를 관리할 수 있는 권한으로 준비 하 고 클라이언트 앱에 액세스할 수 있는 권한을 설정할 수 있습니다.

> [!NOTE]
> 이 스크립트는 현재 Azure Digital Twins (*Azure Digital Twins 소유자 (미리 보기)*) 내에서 필요한 관리 역할을 Cloud Shell 스크립트를 실행 하는 동일한 사용자에 게 할당 합니다. 인스턴스를 관리 하는 다른 사람에 게이 역할을 할당 해야 하는 경우 Azure Portal ([지침](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) 또는 CLI ([지침](how-to-set-up-instance-cli.md#set-up-user-access-permissions))를 통해이 역할을 수행할 수 있습니다.

## <a name="collect-important-values"></a>중요 한 값 수집

스크립트에 의해 설정 되는 리소스의 몇 가지 중요 한 값은 Azure Digital Twins 인스턴스 작업을 계속할 때 필요할 수 있습니다. 이 섹션에서는 [Azure Portal](https://portal.azure.com) 를 사용 하 여 이러한 값을 수집 합니다. 이러한 파일을 안전한 장소에 저장 하거나 나중에 필요할 때 찾을 수 있도록이 섹션으로 돌아옵니다.

다른 사용자가 인스턴스에 대 한 프로그래밍을 수행 하는 경우에도 이러한 값을 공유 해야 합니다.

### <a name="collect-instance-values"></a>인스턴스 값 수집

[Azure Portal](https://portal.azure.com)포털 검색 표시줄에서 인스턴스의 이름을 검색 하 여 Azure Digital twins 인스턴스를 찾습니다.

이를 선택 하면 인스턴스의 *개요* 페이지가 열립니다. *이름*, *리소스 그룹*및 *호스트 이름을*확인 합니다. 나중에 인스턴스를 식별 하 고 연결 하는 데 필요할 수 있습니다.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="인스턴스의 개요 페이지에서 중요 한 값 강조 표시":::

### <a name="collect-app-registration-values"></a>앱 등록 값 수집 

앱 등록에서 두 가지 중요 한 값은 나중에 [Azure Digital Twins api에 대 한 클라이언트 앱 인증 코드를 작성](how-to-authenticate-client.md)하는 데 필요 합니다. 

이를 찾으려면 [이 링크](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) 를 따라 Azure Portal의 Azure AD 앱 등록 개요 페이지로 이동 합니다. 이 페이지에는 구독에 생성 된 모든 앱 등록이 표시 됩니다.

이 목록에서 방금 만든 앱 등록이 표시 됩니다. 이를 선택 하 여 세부 정보를 엽니다.

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="앱 등록에 대 한 중요 한 값의 포털 보기":::

**페이지에** 표시 된 *응용 프로그램 (클라이언트) id* 및 *디렉터리 (테 넌 트) id* 를 기록해 둡니다. 클라이언트 응용 프로그램에 대 한 코드를 작성 하는 사람이 아닌 경우이 값을 지정할 사용자와 공유 해야 합니다.

## <a name="verify-success"></a>성공 확인

스크립트에 의해 설정 된 리소스 및 사용 권한 생성을 확인 하려는 경우 [Azure Portal](https://portal.azure.com)에서 볼 수 있습니다.

### <a name="verify-instance"></a>인스턴스 확인

인스턴스가 만들어졌는지 확인 하려면 Azure Portal의 [Azure Digital Twins 페이지로](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) 이동 합니다. 이 페이지에는 모든 Azure Digital Twins 인스턴스가 나열 됩니다. 목록에서 새로 만든 인스턴스의 이름을 찾습니다.

### <a name="verify-user-role-assignment"></a>사용자 역할 할당 확인

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> 스크립트는 현재이 필수 역할을 Cloud Shell 스크립트를 실행 하는 사용자와 동일한 사용자에 게 할당 합니다. 인스턴스를 관리 하는 다른 사람에 게이 역할을 할당 해야 하는 경우 Azure Portal ([지침](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) 또는 CLI ([지침](how-to-set-up-instance-cli.md#set-up-user-access-permissions))를 통해이 역할을 수행할 수 있습니다.
>
> 스크립팅된 설정에 문제가 있는 경우 포털 또는 CLI를 사용 하 여 사용자 고유의 역할 할당을 다시 실행할 수도 있습니다.

### <a name="verify-app-registration"></a>앱 등록 확인

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

먼저, 등록에 Azure Digital Twins 권한 설정이 올바르게 설정 되었는지 확인 합니다. 이렇게 하려면 메뉴 모음에서 *매니페스트* 를 선택 하 여 앱 등록의 매니페스트 코드를 확인 합니다. 코드 창의 아래쪽으로 스크롤하고 아래에서 이러한 필드를 찾습니다 `requiredResourceAccess` . 값은 아래 스크린샷에 있는 값과 일치 해야 합니다.

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

## <a name="other-possible-steps-for-your-organization"></a>조직의 다른 가능한 단계

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>다음 단계

클라이언트 앱의 인증 코드를 작성 하 여 클라이언트 응용 프로그램을 인스턴스에 연결 하는 방법을 참조 하세요.
* [*방법: 앱 인증 코드 작성*](how-to-authenticate-client.md)
