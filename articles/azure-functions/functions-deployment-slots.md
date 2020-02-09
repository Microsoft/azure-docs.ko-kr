---
title: Azure Functions 배포 슬롯
description: Azure Functions를 사용 하 여 배포 슬롯을 만들고 사용 하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: 0e8c93ea6d5c2b525ccbea2af900f100afcc3d93
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769220"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions 배포 슬롯

Azure Functions 배포 슬롯을 사용 하면 함수 앱에서 "슬롯" 이라는 다른 인스턴스를 실행할 수 있습니다. 슬롯은 공개적으로 사용할 수 있는 엔드포인트을 통해 노출 되는 다양 한 환경입니다. 앱 인스턴스 하나는 항상 프로덕션 슬롯에 매핑되고 요청 시 슬롯에 할당 된 인스턴스를 교환할 수 있습니다. 앱 서비스 계획에서 실행 되는 함수 앱에는 여러 슬롯이 있을 수 있으며 소비 계획에는 하나의 슬롯만 허용 됩니다.

다음은 슬롯을 교환 하 여 함수에 영향을 주는 방법을 나타냅니다.

- 트래픽 리디렉션이 원활 하 게 진행 됩니다. 교환 때문에 요청이 삭제 되지 않습니다.
- 교환 중에 함수를 실행 하는 경우 실행이 계속 되 고 후속 트리거가 스왑 된 앱 인스턴스로 라우팅됩니다.

> [!NOTE]
> 슬롯은 현재 Linux 소비 계획에 사용할 수 없습니다.

## <a name="why-use-slots"></a>슬롯을 사용 하는 이유

배포 슬롯을 사용 하면 여러 가지 이점이 있습니다. 다음 시나리오에서는 슬롯의 일반적인 용도를 설명 합니다.

- **용도에**따라 다른 환경: 다른 슬롯을 사용 하면 프로덕션 또는 스테이징 슬롯으로 교환 하기 전에 앱 인스턴스를 구분할 수 있습니다.
- **사전 준비**: 프로덕션에 직접 배포 하는 대신 슬롯에 배포 하면 앱이 라이브 전환 전에 준비 될 수 있습니다. 또한 슬롯을 사용 하면 HTTP로 트리거되는 워크 로드에 대한 대기 시간이 줄어듭니다. 인스턴스는 배포 전에 준비 새로 배포 된 함수에 대한 콜드 시작을 줄입니다.
- **간편한 대체**: 프로덕션을 사용한 교환 후 이전에 준비 된 앱이 있는 슬롯에는 이제 이전 프로덕션 앱이 있습니다. 프로덕션 슬롯으로 교환 되는 변경 내용이 원하는 대로 변경 되지 않은 경우 교체를 즉시 취소 하 여 "마지막으로 성공한 인스턴스"를 다시 가져올 수 있습니다.

## <a name="swap-operations"></a>교환 작업

교환 하는 동안 한 슬롯은 원본 및 대상으로 간주 됩니다. 소스 슬롯에는 대상 슬롯에 적용 되는 응용 프로그램의 인스턴스가 있습니다. 다음 단계는 교환 하는 동안 대상 슬롯의 가동 중지 시간이 발생 하지 않도록 합니다.

1. **설정 적용:** 대상 슬롯의 설정은 원본 슬롯의 모든 인스턴스에 적용 됩니다. 예를 들어 프로덕션 설정은 준비 인스턴스에 적용 됩니다. 적용 되는 설정에는 다음 범주가 포함 됩니다.
    - [슬롯 특정](#manage-settings) 앱 설정 및 연결 문자열 (해당 하는 경우)
    - [연속 배포](../app-service/deploy-continuous-deployment.md) 설정 (설정 된 경우)
    - [인증 설정 App Service](../app-service/overview-authentication-authorization.md) (사용 하도록 설정 된 경우)

1. **다시 시작 및 가용성 대기:** Swap은 원본 슬롯의 모든 인스턴스가 다시 시작을 완료 하 고 요청에 사용할 수 있도록 대기 합니다. 인스턴스를 다시 시작 하지 못하는 경우 교환 작업은 원본 슬롯의 모든 변경 내용을 되돌리고 작업을 중지 합니다.

1. **업데이트 라우팅:** 원본 슬롯의 모든 인스턴스가 성공적으로 준비 두 슬롯은 라우팅 규칙을 전환 하 여 교환을 완료 합니다. 이 단계를 수행한 후 대상 슬롯 (예: 프로덕션 슬롯)에는 이전에 원본 슬롯에서 준비 된 앱이 있습니다.

1. **작업 반복:** 이제 원본 슬롯의 이전에는 대상 슬롯에 사전 교환 앱이 있으므로 모든 설정을 적용 하 고 원본 슬롯에 대한 인스턴스를 다시 시작 하 여 동일한 작업을 수행 합니다.

다음 사항을 주의하세요.

- 교환 작업의 어떤 지점에서 든 원본 슬롯에서 스왑 된 앱의 초기화가 수행 됩니다. 대상 슬롯은 원본 슬롯이 준비 되는 동안 온라인 상태를 유지 하 고 교환에 성공 또는 실패 하는지 여부를 지정 합니다.

- 스테이징 슬롯을 프로덕션 슬롯과 교환 하려면 프로덕션 슬롯이 *항상* 대상 슬롯 인지 확인 합니다. 이러한 방식으로 교환 작업은 프로덕션 앱에 영향을 주지 않습니다.

- *교환을 시작 하기 전에*이벤트 원본 및 바인딩과 관련 된 설정을 [배포 슬롯 설정](#manage-settings) 으로 구성 해야 합니다. 앞에 "고정"으로 표시 하면 이벤트와 출력이 적절 한 인스턴스로 전달 됩니다.

## <a name="manage-settings"></a>설정 관리

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>배포 설정 만들기

설정을 배포 설정으로 표시 하 여 "고정" 할 수 있습니다. 고정 설정은 앱 인스턴스와 교환 하지 않습니다.

한 슬롯에서 배포 설정을 만드는 경우 교환에 포함 된 다른 슬롯에서 고유한 값을 사용 하 여 동일한 설정을 만들어야 합니다. 이러한 방식으로 설정의 값이 변경 되지 않는 반면, 설정 이름은 슬롯 간에 일관 되 게 유지 됩니다. 이 이름 일관성을 사용 하면 코드가 한 슬롯에 정의 된 설정에는 액세스를 시도 하지 않습니다.

배포 설정을 만들려면 다음 단계를 사용 합니다.

- 함수 앱의 *슬롯* 으로 이동 합니다.
- 슬롯 이름을 클릭 합니다.
- *플랫폼 기능 > 일반 설정*에서 **구성** 을 클릭 합니다.
- 현재 슬롯과 함께 사용할 설정 이름을 클릭 합니다.
- **배포 슬롯 설정** 확인란을 클릭 합니다.
- **확인**을 클릭합니다.
- 설정 블레이드가 사라지면 **저장** 을 클릭 하 여 변경 내용을 유지 합니다.

![배포 슬롯 설정](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>배포

슬롯은 슬롯을 만들 때 비어 있습니다. [지원 되는 배포 기술](./functions-deployment-technologies.md) 중 하나를 사용 하 여 응용 프로그램을 슬롯에 배포할 수 있습니다.

## <a name="scaling"></a>크기 조정

모든 슬롯은 프로덕션 슬롯과 동일한 수의 작업자로 확장 됩니다.

- 소비 계획의 경우 함수 앱이 확장 될 때 슬롯의 크기가 조정 됩니다.
- App Service 계획의 경우 앱은 고정 된 작업자 수로 확장 됩니다. 슬롯은 앱 계획과 동일한 수의 작업자에서 실행 됩니다.

## <a name="add-a-slot"></a>슬롯 추가

[CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) 또는 포털을 통해 슬롯을 추가할 수 있습니다. 다음 단계에서는 포털에서 새 슬롯을 만드는 방법을 보여 줍니다.

1. 함수 앱으로 이동 하 여 *슬롯*옆에 있는 **더하기 기호** 를 클릭 합니다.

    ![배포 슬롯 Azure Functions 추가](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. 텍스트 상자에 이름을 입력 하 고 **만들기** 단추를 누릅니다.

    ![이름 Azure Functions 배포 슬롯](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>슬롯 교환

[CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) 또는 포털을 통해 슬롯을 교환할 수 있습니다. 다음 단계에서는 포털에서 슬롯을 교환 하는 방법을 보여 줍니다.

1. 함수 앱으로 이동 합니다.
1. 교환 하려는 원본 슬롯 이름을 클릭 합니다.
1. *개요* 탭에서 **교환** 단추 ![클릭 하 Azure Functions 배포 슬롯을 교체](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. 교환에 대한 구성 설정을 확인 하 고 **교환** ![교체를 클릭 하 Azure Functions 배포 슬롯](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

교환 작업을 실행 하는 동안 작업이 잠시 소요 될 수 있습니다.

## <a name="roll-back-a-swap"></a>교환 롤백

교환 결과로 오류가 발생 하거나 단순히 교체를 "실행 취소" 하려는 경우 초기 상태로 롤백할 수 있습니다. 사전 전환 된 상태로 돌아가려면 다른 교환을 수행 하 여 교체를 반대로 합니다.

## <a name="remove-a-slot"></a>슬롯 제거

[CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) 또는 포털을 통해 슬롯을 제거할 수 있습니다. 다음 단계에서는 포털에서 슬롯을 제거 하는 방법을 보여 줍니다.

1. 함수 앱 개요로 이동 합니다.

1. **삭제** 단추를 클릭 합니다.

    ![배포 슬롯 Azure Functions 추가](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>슬롯 관리 자동화

[Azure CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)를 사용 하 여 슬롯에 대해 다음 작업을 자동화할 수 있습니다.

- [create](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [delete](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [list](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [swap](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [자동 교환](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>App Service 계획 변경

App Service 계획으로 실행 되는 함수 앱을 사용 하 여 슬롯에 대한 기본 App Service 요금제를 변경할 수 있습니다.

> [!NOTE]
> 소비 계획에서 슬롯의 App Service 요금제를 변경할 수 없습니다.

슬롯의 App Service 계획을 변경 하려면 다음 단계를 사용 합니다.

1. 슬롯으로 이동

1. *플랫폼 기능*에서 **모든 설정** 을 클릭 합니다.

    ![App service 계획 변경](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. **App Service 계획** 을 클릭 합니다.

1. 새 App Service 계획을 선택 하거나 새 계획을 만듭니다.

1. **확인**을 클릭합니다.

    ![App service 계획 변경](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>제한 사항

Azure Functions 배포 슬롯에는 다음과 같은 제한 사항이 있습니다.

- 앱에 사용할 수 있는 슬롯 수는 계획에 따라 다릅니다. 소비 계획은 배포 슬롯을 하나만 허용 합니다. App Service 계획에서 실행 되는 앱에 대한 추가 슬롯을 사용할 수 있습니다.
- 슬롯을 교환 하면 `AzureWebJobsSecretStorageType` 앱 설정이 `files`같은 앱에 대한 키가 다시 설정 됩니다.
- Linux 소비 계획에는 슬롯을 사용할 수 없습니다.

## <a name="support-levels"></a>지원 수준

배포 슬롯에 대해 지원 되는 두 가지 수준은 다음과 같습니다.

- **GA (일반 공급)** : 프로덕션 사용을 위해 완전히 지원 되 고 승인 됩니다.
- **미리 보기**: 아직 지원 되지 않지만 나중에 GA 상태에 도달할 것으로 예상 됩니다.

| OS/호스팅 계획           | 지원 수준     |
| ------------------------- | -------------------- |
| Windows 사용량       | 일반 공급 |
| Windows Premium           | 일반 공급  |
| Windows 전용         | 일반 공급 |
| Linux 소비         | 지원되지 않음          |
| Linux 프리미엄             | 일반 공급  |
| Linux 전용           | 일반 공급 |

## <a name="next-steps"></a>다음 단계

- [Azure Functions의 배포 기술](./functions-deployment-technologies.md)
