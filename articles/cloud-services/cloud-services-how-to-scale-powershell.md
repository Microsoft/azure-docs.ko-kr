---
title: Windows PowerShell에서 Azure 클라우드 서비스 크기 조정 | Microsoft Docs
description: (클래식) PowerShell을 사용하여 Azure에서 웹 역할 또는 작업자 역할을 축소 또는 확장하는 방법을 알아봅니다.
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: ee37dd8c-6714-4c61-adb8-03d6bbf76c9a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: mmccrory
ms.openlocfilehash: 240b34abd2f9b937c8a7ea449ad7f148870a9d33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61433265"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>PowerShell에서 클라우드 서비스 크기를 조정하는 방법

Windows PowerShell을 사용하여 인스턴스를 추가하거나 제거함으로써 웹 역할 또는 작업자 역할을 축소하거나 확대할 수 있습니다.  

## <a name="log-in-to-azure"></a>Azure에 로그인

PowerShell을 통해 구독에 대한 작업을 수행하려면 먼저 로그인해야 합니다.

```powershell
Add-AzureAccount
```

계정에 연결된 구독이 여러 개인 경우 클라우드 서비스가 상주하는 위치에 따라 현재 구독을 변경해야 할 수 있습니다. 현재 구독을 확인하려면 다음을 실행합니다.

```powershell
Get-AzureSubscription -Current
```

현재 구독을 변경해야 할 경우 다음을 실행합니다.

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>역할에 대한 현재 인스턴스 수 확인

사용자 역할의 현재 상태를 확인하려면 다음을 실행합니다.

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

현재 해당 OS 버전 및 인스턴스 수를 포함하여 역할에 대한 정보를 다시 가져와야 합니다. 아래의 경우 역할은 단일 인스턴스를 갖습니다.

![역할에 대한 정보](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>인스턴스를 더 추가하여 역할 확장

역할을 확장하려면 **Set-AzureRole** cmdlet에 **Count** 매개 변수로 원하는 인스턴스 수를 전달합니다.

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

새 인스턴스가 프로비전되고 시작되는 동안 cmdlet은 일시적으로 차단됩니다. 이 시간 동안 새 PowerShell 창을 열고 앞에 표시된 대로 **Get-AzureRole**을 호출하면 새 대상 인스턴스의 수가 표시됩니다. 그리고 포털에서 역할 상태를 검사하면 새 인스턴스가 시작 중임이 표시되어야 합니다.

![포털에서 시작 중인 VM 인스턴스](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

새 인스턴스가 시작되면 cmdlet이 다음과 같이 성공적으로 반환됩니다.

![역할 인스턴스 증가 성공](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>인스턴스를 제거하여 역할 축소

같은 방식으로 인스턴스를 제거하여 역할을 축소할 수 있습니다. **Set-AzureRole**에서 **Count** 매개 변수를 축소 작업이 완료된 후의 원하는 인스턴스 수로 설정합니다.

## <a name="next-steps"></a>다음 단계

PowerShell에서 Cloud Services에 대한 자동 크기 조정을 구성하는 것은 불가능합니다. 자동 크기 조정은 [클라우드 서비스 크기를 자동으로 조정하는 방법](cloud-services-how-to-scale-portal.md)을 참조하세요.
