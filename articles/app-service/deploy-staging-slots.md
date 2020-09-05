---
title: 스테이징 환경 설정
description: 프로덕션이 아닌 슬롯에 앱을 배포 하 고 프로덕션으로 자동 교환 하는 방법에 대해 알아봅니다. 안정성을 높이고 배포에서 앱 가동 중지 시간을 제거 합니다.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 04/30/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: b12b85a2248d7709066ba3218327e0a5d52a0192
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962165"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Azure App Service에서 스테이징 환경 설정
<a name="Overview"></a>

웹 앱, Linux, 모바일 백 엔드 또는 API 앱의 웹 앱을 [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)에 배포 하는 경우 **표준**, **프리미엄**또는 **격리** 된 App Service 계획 계층에서 실행 하는 경우 기본 프로덕션 슬롯 대신 별도의 배포 슬롯을 사용할 수 있습니다. 배포 슬롯은 고유한 호스트 이름이 있는 라이브 앱입니다. 앱 콘텐츠 및 구성 요소는 프로덕션 슬롯을 포함하여 두 배포 슬롯 간에 교환될 수 있습니다. 

애플리케이션을 비프로덕션 슬롯에 배포하면 다음과 같은 이점이 있습니다.

* 프로덕션 슬롯과 교환하기 전에 준비 배포 슬롯에서 앱 변경 사항의 유효성을 검사할 수 있습니다.
* 먼저 슬롯으로 앱을 배포하고 프로덕션으로 교환하기 때문에 프로덕션으로 교환되기 전에 슬롯에 있는 모든 인스턴스가 준비되어 있는 상태입니다. 따라서 앱을 배포할 때 가동 중지가 발생하지 않습니다. 트래픽 리디렉션은 중단 없이 원활하게 수행되며 교환 작업으로 인해 삭제되는 요청은 없습니다. 사전 교환 유효성 검사가 필요 하지 않은 경우 [자동 교환](#Auto-Swap) 을 구성 하 여이 전체 워크플로를 자동화할 수 있습니다.
* 교환 후에는 이전의 준비된 앱이 들어 있던 슬롯 안에 이전의 프로덕션 앱이 들어갑니다. 프로덕션 슬롯과 교환한 변경 내용이 예상과 다른 경우 같은 교환 작업을 즉시 수행하여 "마지막 양호 상태"로 돌아갈 수 있습니다.

각 App Service 계획 계층은 다양한 수의 배포 슬롯을 지원합니다. 배포 슬롯 사용에 대 한 추가 비용은 없습니다. 앱 계층이 지 원하는 슬롯 수를 확인 하려면 [App Service 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)을 참조 하세요. 

앱을 다른 계층으로 확장 하려면 대상 계층에서 앱이 이미 사용 하는 슬롯 수를 지원 하는지 확인 합니다. 예를 들어 앱의 슬롯이 5 개를 초과 하는 **경우 표준 계층은 5** 개의 배포 슬롯만 지원 하기 때문에 **표준** 계층으로 확장할 수 없습니다. 

<a name="Add"></a>

## <a name="add-a-slot"></a>슬롯 추가
여러 배포 슬롯을 사용 하도록 설정 하려면 앱이 **표준**, **프리미엄**또는 **격리** 계층에서 실행 되어야 합니다.


1. [Azure Portal](https://portal.azure.com/)에서 **App Services** 를 검색 하 여 선택 하 고 앱을 선택 합니다. 
   
    ![App Services 검색](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. 왼쪽 창에서 **배포 슬롯**  >  **슬롯 추가**를 선택 합니다.
   
    ![새 배포 슬롯 추가](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > 앱이 아직 **Standard**, **Premium**또는 **Isolated** 계층에 있지 않은 경우 준비 된 게시를 사용 하도록 지원 되는 계층을 나타내는 메시지가 표시 됩니다. 이 시점에서 **업그레이드** 를 선택 하 고 계속 하기 전에 앱의 **크기 조정** 탭으로 이동할 수 있는 옵션이 있습니다.
   > 

3. **슬롯 추가** 대화 상자에서 슬롯에 이름을 지정 하 고 다른 배포 슬롯에서 앱 구성을 복제할 것인지 여부를 선택 합니다. **추가** 를 선택 하 여 계속 합니다.
   
    ![구성 원본](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    기존 슬롯에서 구성을 복제할 수 있습니다. 복제할 수 있는 설정에는 앱 설정, 연결 문자열, 언어 프레임워크 버전, 웹 소켓, HTTP 버전 및 플랫폼 비트 수가 포함됩니다.

4. 슬롯이 추가 되 면 **닫기** 를 선택 하 여 대화 상자를 닫습니다. 이제 새 슬롯이 **배포 슬롯** 페이지에 표시 됩니다. 기본적으로 **traffic%** 는 새 슬롯에 대해 0으로 설정 되 고 모든 고객 트래픽은 프로덕션 슬롯으로 라우팅됩니다.

5. 새 배포 슬롯을 선택 하 여 해당 슬롯의 리소스 페이지를 엽니다.
   
    ![배포 슬롯 제목](./media/web-sites-staged-publishing/StagingTitle.png)

    스테이징 슬롯에는 다른 App Service 앱과 마찬가지로 관리 페이지가 있습니다. 슬롯의 구성을 변경할 수 있습니다. 배포 슬롯을 보고 있음을 알리기 위해 앱 이름이로 표시 되 **\<app-name>/\<slot-name>** 고 앱 유형이 **App Service (슬롯)** 입니다. 동일한 명칭을 사용 하 여 리소스 그룹에서 별도의 앱으로 슬롯을 볼 수도 있습니다.

6. 슬롯의 리소스 페이지에서 앱 URL을 선택 합니다. 배포 슬롯에는 고유한 호스트 이름이 있으며 라이브 앱 이기도 합니다. 배포 슬롯에 대 한 공용 액세스를 제한 하려면 [AZURE APP SERVICE IP 제한](app-service-ip-restrictions.md)을 참조 하세요.

다른 슬롯에서 설정을 복제하더라도 새 배포 슬롯에는 내용이 없습니다. 예를 들어 Git를 [사용 하 여이 슬롯에 게시할](./deploy-local-git.md)수 있습니다. 다른 리포지토리 분기 또는 다른 리포지토리로부터 슬롯에 배포할 수 있습니다.

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>교환 하는 동안 수행 되는 작업

### <a name="swap-operation-steps"></a>스왑 작업 단계

두 슬롯을 교환 하는 경우 (일반적으로 스테이징 슬롯에서 프로덕션 슬롯으로 App Service) 다음을 수행 하 여 대상 슬롯에 가동 중지 시간이 발생 하지 않도록 합니다.

1. 대상 슬롯 (예: 프로덕션 슬롯)에서 다음 설정을 원본 슬롯의 모든 인스턴스에 적용 합니다. 
    - [슬롯 특정](#which-settings-are-swapped) 앱 설정 및 연결 문자열 (해당 하는 경우).
    - [연속 배포](deploy-continuous-deployment.md) 설정 (사용 하도록 설정 된 경우)
    - 활성화 된 경우 [인증 설정을 App Service](overview-authentication-authorization.md) 합니다.
    
    이러한 경우 모두 원본 슬롯의 모든 인스턴스가 다시 시작 되도록 트리거합니다. [Preview를 사용](#Multi-Phase)하 여 교환 하는 동안 첫 번째 단계의 끝을 표시 합니다. 교환 작업이 일시 중지 되 고 소스 슬롯이 대상 슬롯의 설정으로 올바르게 작동 하는지 확인할 수 있습니다.

1. 소스 슬롯의 모든 인스턴스가 다시 시작을 완료할 때까지 기다립니다. 인스턴스를 다시 시작 하지 못하는 경우 교환 작업은 원본 슬롯의 모든 변경 내용을 되돌리고 작업을 중지 합니다.

1. [로컬 캐시](overview-local-cache.md) 를 사용 하는 경우 소스 슬롯의 각 인스턴스에서 응용 프로그램 루트 ("/")에 대 한 HTTP 요청을 수행 하 여 로컬 캐시 초기화를 트리거합니다. 각 인스턴스가 HTTP 응답을 반환할 때까지 기다립니다. 로컬 캐시 초기화를 수행 하면 각 인스턴스에서 다른 다시 시작이 발생 합니다.

1. [사용자 지정 준비](#Warm-up)상태에서 [자동 교환이](#Auto-Swap) 설정 된 경우 원본 슬롯의 각 인스턴스에서 응용 프로그램 루트 ("/")에 대 한 HTTP 요청을 만들어 [응용 프로그램 시작](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) 을 트리거합니다.

    `applicationInitialization`을 지정 하지 않으면 각 인스턴스에서 원본 슬롯의 응용 프로그램 루트에 대 한 HTTP 요청을 트리거합니다. 
    
    인스턴스가 HTTP 응답을 반환 하는 경우 준비 된 것으로 간주 됩니다.

1. 원본 슬롯의 모든 인스턴스가 성공적으로 준비 두 슬롯에 대 한 라우팅 규칙을 전환 하 여 두 슬롯을 교환 합니다. 이 단계를 수행한 후 대상 슬롯 (예: 프로덕션 슬롯)에는 이전에 원본 슬롯에서 준비 된 앱이 있습니다.

1. 이제 원본 슬롯의 이전에는 대상 슬롯에 사전 교환 앱이 있으므로 모든 설정을 적용 하 고 인스턴스를 다시 시작 하 여 동일한 작업을 수행 합니다.

교환 작업의 모든 지점에서 스왑 된 앱을 초기화 하는 모든 작업은 원본 슬롯에서 발생 합니다. 대상 슬롯은 교환이 성공 하거나 실패 하는 위치와 관계 없이 원본 슬롯을 준비 하 고 준비는 동안 온라인 상태를 유지 합니다. 스테이징 슬롯을 프로덕션 슬롯과 교환 하려면 프로덕션 슬롯이 항상 대상 슬롯 인지 확인 합니다. 이러한 방식으로 교환 작업은 프로덕션 앱에 영향을 주지 않습니다.

### <a name="which-settings-are-swapped"></a>어떤 설정이 교환되나요?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

특정 슬롯 (교환 되지 않음)에 맞게 앱 설정 또는 연결 문자열을 구성 하려면 해당 슬롯에 대 한 **구성** 페이지로 이동 합니다. 설정을 추가 하거나 편집한 후 **배포 슬롯 설정**을 선택 합니다. 이 확인란을 선택 하면 설정이 스왑할 수 없음을 App Service에 알려 줍니다. 

![슬롯 설정](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>두 슬롯 교환 
앱의 **배포 슬롯** 페이지 및 **개요** 페이지에서 배포 슬롯을 교환할 수 있습니다. 슬롯 교환에 대 한 기술 세부 정보는 [교환 하는 동안 수행](#AboutConfiguration)되는 작업을 참조 하세요.

> [!IMPORTANT]
> 앱을 배포 슬롯에서 프로덕션으로 교환 하기 전에 프로덕션이 대상 슬롯 인지 확인 하 고 원본 슬롯의 모든 설정이 프로덕션 환경에서 원하는 대로 정확 하 게 구성 되었는지 확인 합니다.
> 
> 

배포 슬롯을 교환 하려면:

1. 앱의 **배포 슬롯** 페이지로 이동 하 고 **교환**을 선택 합니다.
   
    ![교환 단추](./media/web-sites-staged-publishing/SwapButtonBar.png)

    **교환** 대화 상자에는 선택한 원본 및 대상 슬롯에서 변경 될 설정이 표시 됩니다.

2. 원하는 **원본** 및 **대상** 슬롯을 선택합니다. 일반적으로 대상은 프로덕션 슬롯입니다. 또한 **원본 변경** 내용 및 **대상 변경** 탭을 선택 하 고 구성 변경이 예상 되는지 확인 합니다. 완료 되 면 **교환**을 선택 하 여 슬롯을 즉시 교환할 수 있습니다.

    ![전체 교환](./media/web-sites-staged-publishing/SwapImmediately.png)

    교환이 실제로 수행 되기 전에 대상 슬롯이 새 설정으로 실행 되는 방식을 확인 하려면 **바꾸기**를 선택 하지 말고 [preview와 바꾸기](#Multi-Phase)의 지침을 따르세요.

3. 완료 되 면 **닫기**를 선택 하 여 대화 상자를 닫습니다.

문제가 있는 경우 [교체 문제 해결](#troubleshoot-swaps)을 참조 하세요.

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>미리 보기가 있는 교환(다단계 교환)

대상 슬롯으로 프로덕션으로 전환 하기 전에 교체 된 설정으로 앱이 실행 되는지 확인 합니다. 또한 원본 슬롯은 바꾸기 완료 전에 준비 중요 한 응용 프로그램에 적합 합니다.

Preview를 사용 하 여 교환을 수행 하는 경우 App Service는 동일한 [교환 작업](#AboutConfiguration) 을 수행 하지만 첫 번째 단계 후에는 일시 중지 됩니다. 그런 다음 교환을 완료 하기 전에 스테이징 슬롯에 대 한 결과를 확인할 수 있습니다. 

교환을 취소 하면 구성 요소가 원본 슬롯에 다시 적용 App Service.

Preview를 사용 하 여 교환 하려면:

1. [배포 슬롯 교환](#Swap) 의 단계를 수행 하 되 **미리 보기로 전환 수행을**선택 합니다.

    ![미리 보기가 있는 교환](./media/web-sites-staged-publishing/SwapWithPreview.png)

    이 대화 상자에는 1 단계에서 원본 슬롯의 구성과 원본 및 대상 슬롯이 2 단계에서 변경 되는 방식이 표시 됩니다.

2. 교환을 시작할 준비가 되 면 **교환 시작**을 선택 합니다.

    1 단계가 완료 되 면 대화 상자에 알림이 표시 됩니다. 로 이동 하 여 원본 슬롯에서 교환을 미리 봅니다 `https://<app_name>-<source-slot-name>.azurewebsites.net` . 

3. 보류 중인 교체를 완료할 준비가 되 면 **바꾸기 작업** 에서 **전체 바꾸기** 를 선택 하 고 **전체 교체**를 선택 합니다.

    보류 중인 교환을 취소 하려면 대신 **바꾸기 취소** 를 선택 합니다.

4. 완료 되 면 **닫기**를 선택 하 여 대화 상자를 닫습니다.

문제가 있는 경우 [교체 문제 해결](#troubleshoot-swaps)을 참조 하세요.

다단계 교환을 자동화 하려면 [PowerShell을 사용 하 여 자동화](#automate-with-powershell)를 참조 하세요.

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>교환 롤백
슬롯 교환 후 대상 슬롯(예: 프로덕션 슬롯)에서 오류가 발생하면 2개의 동일한 슬롯을 즉시 교환하여 슬롯을 교환 전 상태로 복원합니다.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>자동 전환 구성

> [!NOTE]
> 자동 교환은 Linux의 웹 앱에서 지원 되지 않습니다.

자동 교체는 앱을 실행 하는 동안 콜드 시작이 0이 고 가동 중지 시간이 0 인 앱을 지속적으로 배포 하려는 Azure DevOps 시나리오를 간소화 합니다. 자동 교환이 슬롯에서 프로덕션으로 사용 하도록 설정 된 경우 코드 변경 내용을 해당 슬롯에 푸시할 때마다 준비이 소스 슬롯에서 된 후 자동으로 [앱을 프로덕션으로 교체](#swap-operation-steps) App Service.

   > [!NOTE]
   > 프로덕션 슬롯에 대 한 자동 교환을 구성 하기 전에 비프로덕션 대상 슬롯에서 자동 교환 테스트를 고려 합니다.
   > 

자동 교환을 구성 하려면:

1. 앱의 리소스 페이지로 이동 합니다. **배포 슬롯**  >  *\<desired source slot>*  >  **구성**  >  **일반 설정**을 선택 합니다.
   
2. **자동 교환 사용**에서 **켜기**를 선택 합니다. 그런 다음 **배포 슬롯 자동 교환**에 필요한 대상 슬롯을 선택 하 고 명령 모음에서 **저장** 을 선택 합니다. 
   
    ![자동 교환 구성을 위한 선택 항목](./media/web-sites-staged-publishing/AutoSwap02.png)

3. 소스 슬롯에 대해 코드 푸시를 실행합니다. 자동 교환은 짧은 시간 후에 발생 하며 업데이트는 대상 슬롯의 URL에 반영 됩니다.

문제가 있는 경우 [교체 문제 해결](#troubleshoot-swaps)을 참조 하세요.

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>사용자 지정 준비 지정

일부 앱의 경우 교환 전에 사용자 지정 준비 작업이 필요할 수 있습니다. `applicationInitialization`web.config의 구성 요소를 사용 하 여 사용자 지정 초기화 작업을 지정할 수 있습니다. [교환 작업](#AboutConfiguration) 은이 사용자 지정 준비가 완료 될 때까지 대기한 후 대상 슬롯과 교환 합니다. 샘플 web.config 조각은 다음과 같습니다.

```xml
<system.webServer>
    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostName="[app hostname]" />
    </applicationInitialization>
</system.webServer>
```

요소를 사용자 지정 하는 방법에 대 한 자세한 내용은 `applicationInitialization` [가장 일반적인 배포 슬롯 교환 실패 및 수정 방법](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)을 참조 하세요.

또한 다음 [앱 설정](configure-common.md)중 하나 또는 둘 모두를 사용 하 여 준비 동작을 사용자 지정할 수 있습니다.

- `WEBSITE_SWAP_WARMUP_PING_PATH`: 사이트를 준비 하기 위해 ping 할 경로입니다. 슬래시로 시작하는 사용자 지정 경로를 값으로 지정하여 이 앱 설정을 추가합니다. 예제는 `/statuscheck`입니다. 기본값은 `/`입니다. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: 준비 작업에 대 한 유효한 HTTP 응답 코드입니다. HTTP 코드의 쉼표로 구분된 목록을 사용하여 이 앱 설정을 추가합니다. 예를 들면 `200,202` 입니다. 반환 된 상태 코드가 목록에 없으면 준비 및 교환 작업이 중지 됩니다. 기본적으로 모든 응답 코드는 유효합니다.

> [!NOTE]
> `<applicationInitialization>`구성 요소는 각 앱 시작의 일부 이지만 두 개의 준비 동작 앱 설정은 슬롯 교환에만 적용 됩니다.

문제가 있는 경우 [교체 문제 해결](#troubleshoot-swaps)을 참조 하세요.

## <a name="monitor-a-swap"></a>교환 모니터링

[교환 작업](#AboutConfiguration) 을 완료 하는 데 시간이 오래 걸리면 [활동 로그](../azure-monitor/platform/platform-logs-overview.md)에서 교환 작업에 대 한 정보를 가져올 수 있습니다.

포털의 앱 리소스 페이지에 있는 왼쪽 창에서 **활동 로그**를 선택 합니다.

교환 작업은 `Swap Web App Slots`으로 로그 쿼리에 표시됩니다. 이를 확장하고 하위 작업 또는 오류 중 하나를 선택하여 세부 정보를 볼 수 있습니다.

## <a name="route-traffic"></a>트래픽 라우팅

기본적으로 앱의 프로덕션 URL에 대한 모든 클라이언트 요청(`http://<app_name>.azurewebsites.net`)은 프로덕션 슬롯으로 라우팅됩니다. 트래픽의 일부를 다른 슬롯으로 라우팅할 수 있습니다. 이 기능은 새 업데이트에 대한 사용자 피드백이 필요하지만 프로덕션 환경으로 릴리스할 준비가 되지 않은 경우에 유용합니다.

### <a name="route-production-traffic-automatically"></a>자동으로 프로덕션 트래픽 라우팅

프로덕션 트래픽을 자동으로 라우팅하려면:

1. 앱의 리소스 페이지로 이동 하 고 **배포 슬롯**을 선택 합니다.

2. 라우팅하려는 슬롯의 **트래픽 %** 열에서 라우팅할 트래픽의 총량을 나타내는 백분율(0~100)을 지정합니다. **저장**을 선택합니다.

    ![트래픽 비율 설정](./media/web-sites-staged-publishing/RouteTraffic.png)

설정이 저장 된 후 지정 된 클라이언트 비율 (%)은 비프로덕션 슬롯으로 임의로 라우팅됩니다. 

클라이언트는 특정 슬롯에 자동으로 라우팅되고 나면 해당 클라이언트 세션의 수명 동안 해당 슬롯에 "고정" 됩니다. 클라이언트 브라우저에서 HTTP 헤더의 `x-ms-routing-name` 쿠키를 확인하여 세션이 고정된 슬롯을 볼 수 있습니다. "스테이징" 슬롯에 라우팅되는 요청에는 쿠키 `x-ms-routing-name=staging`이 있습니다. 프로덕션 슬롯으로 라우팅되는 요청에는 쿠키 `x-ms-routing-name=self`가 있습니다.

   > [!NOTE]
   > Azure Portal 옆에 [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) Azure CLI의 명령을 사용 하 여 DevOps 파이프라인이 나 기타 자동화 시스템과 같은 CI/CD 도구에서 라우팅 비율을 설정할 수도 있습니다.
   > 

### <a name="route-production-traffic-manually"></a>수동으로 프로덕션 트래픽 라우팅

자동 트래픽 라우팅 외에도 App Service는 특정 슬롯에 요청을 라우팅할 수 있습니다. 사용자가 베타 앱에 참여 하거나 옵트인 (opt out) 할 수 있도록 하려는 경우에 유용 합니다. 수동으로 프로덕션 트래픽을 라우팅하려면 `x-ms-routing-name` 쿼리 매개 변수를 사용합니다.

예를 들어 사용자가 베타 앱을 옵트아웃 (opt out) 할 수 있도록 웹 페이지에 다음 링크를 넣을 수 있습니다.

```html
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

문자열 `x-ms-routing-name=self` 는 프로덕션 슬롯을 지정합니다. 클라이언트 브라우저는 링크에 액세스 한 후 프로덕션 슬롯으로 리디렉션됩니다. 모든 후속 요청에는 `x-ms-routing-name=self` 프로덕션 슬롯에 세션을 고정 하는 쿠키가 있습니다.

사용자가 베타 앱에 옵트인 (opt in) 할 수 있도록 하려면 비프로덕션 슬롯의 이름에 동일한 쿼리 매개 변수를 설정 합니다. 예를 들면 다음과 같습니다.

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

기본적으로 새 슬롯에는 회색으로 표시 된의 라우팅 규칙이 제공 됩니다 `0%` . 이 값을 명시적으로 `0%` (검정 텍스트로 표시)로 설정 하면 사용자가 `x-ms-routing-name` 쿼리 매개 변수를 사용 하 여 스테이징 슬롯에 수동으로 액세스할 수 있습니다. 그러나 라우팅 백분율이 0으로 설정 되어 있으므로 자동으로 슬롯에 라우팅되지 않습니다. 이는 내부 팀에서 슬롯에 대 한 변경 내용을 테스트할 수 있도록 하는 동시에 공용에서 스테이징 슬롯을 "숨길" 수 있는 고급 시나리오입니다.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>슬롯 삭제

앱을 검색 하 고 선택 합니다. **배포 슬롯**  >  *\<slot to delete>*  >  **개요**를 선택 합니다. 앱 유형은 배포 슬롯을 보고 있음을 알리기 위해 **App Service (슬롯)** 로 표시 됩니다. 명령 모음에서 **삭제** 를 선택 합니다.  

![배포 슬롯 삭제](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>PowerShell을 사용하여 자동화

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell은 Windows PowerShell을 통해 Azure를 관리하기 위한 cmdlet을 제공하는 모듈로, Azure App Service에서 배포 슬롯을 관리하는 기능도 지원합니다.

Azure PowerShell을 설치 및 구성하는 방법과 Azure 구독에 Azure PowerShell을 인증하는 방법에 대한 자세한 내용은 [Microsoft Azure PowerShell 설치 및 구성 방법](/powershell/azure/)을 참조하세요.  

---
### <a name="create-a-web-app"></a>웹앱 만들기
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>슬롯 만들기
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Preview (다단계 교환)를 사용 하 여 교환 시작 및 대상 슬롯 구성을 원본 슬롯에 적용
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>보류 중인 교환 (검토와 교환)을 취소 하 고 원본 슬롯 구성을 복원 합니다.
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>배포 슬롯 교환
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>활동 로그에서 교환 이벤트 모니터링
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>슬롯 삭제
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>리소스 관리자 템플릿으로 자동화

[Azure Resource Manager 템플릿은](../azure-resource-manager/templates/overview.md) Azure 리소스의 배포 및 구성을 자동화 하는 데 사용 되는 선언적 JSON 파일입니다. 리소스 관리자 템플릿을 사용 하 여 슬롯을 교환 하려면 *Microsoft 웹/사이트/슬롯* 및 *microsoft 웹/사이트* 리소스에서 두 개의 속성을 설정 합니다.

- `buildVersion`: 슬롯에 배포 된 앱의 현재 버전을 나타내는 문자열 속성입니다. 예를 들면 "v1", "1.0.0.1" 또는 "2019-09-20T11:53:25.2887393-07:00"입니다.
- `targetBuildVersion`: 슬롯에 포함 해야 하는 항목을 지정 하는 문자열 속성입니다 `buildVersion` . TargetBuildVersion가 현재와 같지 않으면 지정 된 `buildVersion` 이 있는 슬롯을 검색 하 여 교환 작업을 트리거합니다 `buildVersion` .

### <a name="example-resource-manager-template"></a>리소스 관리자 템플릿 예제

다음 리소스 관리자 템플릿은 `buildVersion` 스테이징 슬롯의을 업데이트 하 고 `targetBuildVersion` 프로덕션 슬롯에서을 설정 합니다. 그러면 두 슬롯이 교환 됩니다. 템플릿은 "준비" 라는 슬롯을 사용 하 여 만든 webapp 이미 있다고 가정 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

이 리소스 관리자 템플릿은 idempotent 됩니다. 즉, 반복적으로 실행 하 여 슬롯의 동일한 상태를 생성할 수 있습니다. 첫 번째 실행 후는 `targetBuildVersion` 현재와 일치 `buildVersion` 하므로 교환이 트리거되지 않습니다.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>CLI를 사용 하 여 자동화

배포 슬롯에 대한 [Azure CLI](https://github.com/Azure/azure-cli) 명령은 [az webapp deployment slot](/cli/azure/webapp/deployment/slot)을 참조하세요.

## <a name="troubleshoot-swaps"></a>교체 문제 해결

[슬롯 교환](#AboutConfiguration)중에 오류가 발생 하면 *D:\home\LogFiles\eventlog.xml*에 기록 됩니다. 응용 프로그램 관련 오류 로그에도 기록 됩니다.

몇 가지 일반적인 교환 오류는 다음과 같습니다.

- 응용 프로그램 루트에 대 한 HTTP 요청 시간이 초과 되었습니다. 교환 작업은 각 HTTP 요청에 대해 90 초 동안 대기한 후 최대 5 번 다시 시도 합니다. 모든 다시 시도 시간이 초과 되 면 교환 작업이 중지 됩니다.

- 앱 콘텐츠가 로컬 캐시에 지정 된 로컬 디스크 할당량을 초과 하면 로컬 캐시 초기화가 실패할 수 있습니다. 자세한 내용은 [로컬 캐시 개요](overview-local-cache.md)를 참조 하세요.

- [사용자 지정 준비](#Warm-up)과정에서 HTTP 요청은 외부 URL을 거치지 않고 내부적으로 수행 됩니다. *Web.config*에서 특정 URL 재작성 규칙을 사용 하 여 실패할 수 있습니다. 예를 들어 도메인 이름을 리디렉션하는 규칙이 나 HTTPS를 적용 하면 준비 요청이 앱 코드에 도달 하지 못할 수 있습니다. 이 문제를 해결 하려면 다음 두 가지 조건을 추가 하 여 재작성 규칙을 수정 합니다.

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- 사용자 지정 준비를 사용 하지 않으면 URL 재작성 규칙은 여전히 HTTP 요청을 차단할 수 있습니다. 이 문제를 해결 하려면 다음 조건을 추가 하 여 재작성 규칙을 수정 합니다.

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```

- 슬롯 교체 후에는 앱에서 예기치 않은 다시 시작이 발생할 수 있습니다. 이는 교환 후 호스트 이름 바인딩 구성이 동기화 되지 않아 자체적으로 다시 시작 되지 않기 때문입니다. 그러나 특정 기본 저장소 이벤트 (예: 저장소 볼륨 장애 조치 (failover))는 이러한 불일치를 감지 하 여 모든 작업자 프로세스를 강제로 다시 시작할 수 있습니다. 이러한 유형의 다시 시작을 최소화 하려면 *모든 슬롯*에서 [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` 앱 설정을](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) 설정 합니다. 그러나이 앱 설정은 WCF (Windows Communication Foundation) 앱에서 작동 *하지* 않습니다.

## <a name="next-steps"></a>다음 단계
[비프로덕션 슬롯에 대한 액세스 차단](app-service-ip-restrictions.md)