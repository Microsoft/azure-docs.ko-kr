---
title: 삭제된 앱 복원
description: Azure App Service에서 삭제된 앱을 복원하는 방법을 알아봅니다. 실수로 삭제된 앱 때문에 신경 쓰이는 일을 방지합니다.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e894e0a8bd20d6a1c3c833a4c0a3656c0dcd0f05
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833169"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>PowerShell을 사용하여 삭제된 App Service 앱 복원

Azure App Service에서 앱을 실수로 삭제한 경우 [Az PowerShell 모듈](/powershell/azure/)의 명령을 사용하여 앱을 복원할 수 있습니다.

> [!NOTE]
> - 삭제된 앱은 초기 삭제 후 30일이 지나면 시스템에서 제거됩니다. 제거된 앱은 복구할 수 없습니다.
> - 사용량 플랜에서는 삭제 취소 기능을 사용할 수 없습니다.
> - App Service Environment에서 실행되는 Apps Service 앱은 스냅샷을 지원하지 않습니다. 따라서 App Service Environment에서 실행되는 App Service 앱에는 삭제 취소 기능과 복제 기능을 사용할 수 없습니다.
>

## <a name="re-register-app-service-resource-provider"></a>App Service 리소스 공급자 다시 등록

일부 고객은 삭제된 앱 목록을 검색하지 못하는 문제가 발생할 수 있습니다. 이 문제를 해결하려면 다음 명령을 실행합니다.

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>삭제된 앱 나열

삭제된 앱의 컬렉션을 가져오려면 `Get-AzDeletedWebApp`을 사용하면 됩니다.

삭제된 특정 앱을 자세히 알아보려면 다음을 사용합니다.

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

자세한 정보에는 다음이 포함됩니다.

- **DeletedSiteId**: 이름의 동일한 여러 앱이 삭제된 시나리오에 사용되는 앱의 고유 식별자
- **SubscriptionID**: 삭제된 리소스를 포함하는 구독
- **위치**: 원래 앱의 위치
- **ResourceGroupName**: 원래 리소스 그룹의 이름
- **Name**: 원래 앱의 이름
- **Slot**: 슬롯의 이름
- **Deletion Time**: 앱이 삭제된 시기  

## <a name="restore-deleted-app"></a>삭제된 앱 복원

>[!NOTE]
> `Restore-AzDeletedWebApp`은 함수 앱에서 지원되지 않습니다.

복원하려는 앱이 식별되면 `Restore-AzDeletedWebApp`을 사용하여 복원할 수 있습니다.

```powershell
Restore-AzDeletedWebApp -TargetResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> 배포 슬롯은 앱의 일부로 복원되지 않습니다. 스테이징 슬롯을 복원해야 하는 경우 `-Slot <slot-name>` 플래그를 사용합니다.
>

명령에 대한 입력은 다음과 같습니다.

- **대상 리소스 그룹**: 앱이 복원될 대상 리소스 그룹
- **Name**: 앱의 이름은 전역적으로 고유해야 합니다.
- **TargetAppServicePlanName**: 앱에 연결된 App Service 플랜

기본값으로 `Restore-AzDeletedWebApp`은 앱 구성과 콘텐츠를 모두 복원합니다. 콘텐츠만 복원하려면 이 commandlet과 함께 `-RestoreContentOnly` 플래그를 사용합니다.

> [!NOTE]
> 앱이 호스트된 후 App Service Environment에서 삭제된 경우에는 해당 App Service Environment가 계속 존재할 경우에만 복원할 수 있습니다.
>

전체 commandlet 참조는 여기에서 찾을 수 있습니다. [Restore-AzDeletedWebApp](/powershell/module/az.websites/restore-azdeletedwebapp).
