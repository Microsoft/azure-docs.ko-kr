---
title: 삭제 된 앱 복원
description: Azure App Service에서 삭제 된 앱을 복원 하는 방법을 알아봅니다. 실수로 삭제 된 앱의 골칫거리을 방지 합니다.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: c7d778a0afca4b3552976526d58a2cb2efe12161
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689611"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>PowerShell을 사용 하 여 삭제 된 App Service 앱 복원

Azure App Service에서 앱을 실수로 삭제 한 경우 [Az PowerShell 모듈](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)의 명령을 사용 하 여 복원할 수 있습니다.

> [!NOTE]
> 삭제 된 앱은 초기 삭제 후 30 일 후 시스템에서 제거 됩니다. 제거 된 앱은 복구할 수 없습니다.
>

## <a name="re-register-app-service-resource-provider"></a>App Service 리소스 공급자를 다시 등록 합니다.
일부 고객은 삭제 된 앱 목록을 검색 하는 데 실패 하는 문제가 발생할 수 있습니다. 이 문제를 해결 하려면 다음 명령을 실행 합니다.

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>삭제된 앱 나열

삭제 된 앱의 컬렉션을 가져오기 위해 `Get-AzDeletedWebApp`를 사용할 수 있습니다.

삭제 된 특정 앱에 대한 자세한 내용은 다음을 사용할 수 있습니다.

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

세부 정보에는 다음이 포함 됩니다.

- **DeletedSiteId**: 동일한 이름의 여러 앱이 삭제 된 시나리오에 사용 되는 앱의 고유 식별자입니다.
- **SubscriptionID**: 삭제 된 리소스를 포함 하는 구독
- **위치**: 원래 앱의 위치
- **ResourceGroupName**: 원래 리소스 그룹의 이름
- **이름**: 원래 앱의 이름입니다.
- **슬롯**: 슬롯의 이름입니다.
- **삭제 시간**: 앱이 삭제 된 시간  

## <a name="restore-deleted-app"></a>삭제 된 앱 복원

복원 하려는 앱이 식별 되 면 `Restore-AzDeletedWebApp`를 사용 하 여 복원할 수 있습니다.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

명령에 대한 입력은 다음과 같습니다.

- **리소스 그룹**: 앱이 복원 되는 대상 리소스 그룹
- **이름**: 앱의 이름은 전역적으로 고유 해야 합니다.
- **Targetappserviceplan 이름**: 앱에 연결 된 App Service 계획

기본적으로 `Restore-AzDeletedWebApp`는 앱 구성과 콘텐츠를 모두 복원 합니다. 콘텐츠만 복원 하려면이 이상에서 `-RestoreContentOnly` 플래그를 사용 합니다.

> [!NOTE]
> 앱이에서 호스트 된 후 App Service Environment에서 삭제 된 경우에는 해당 App Service Environment 여전히 있는 경우에만 복원할 수 있습니다.
>

여기에서 전체 기능 참조를 찾을 수 있습니다. [AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
