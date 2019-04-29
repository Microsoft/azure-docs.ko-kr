---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: deabef0c2c3540e515fe72a161710c95a20fa86f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320139"
---
상승된 권한으로 PowerShell 콘솔을 엽니다.



Azure PowerShell을 로컬로 실행하는 경우 Azure 계정에 연결합니다. *Connect-AzAccount* cmdlet에서 자격 증명을 입력하라는 메시지를 표시합니다. 인증한 다음, Azure PowerShell에 사용할 수 있도록 계정 설정을 다운로드합니다. PowerShell을 로컬로 실행하지 않고, 대신 브라우저에서 Azure Cloud Shell '사용해 보세요.'를 사용하는 경우 이 첫 번째 단계를 건너뜁니다. 자동으로 Azure 계정에 연결됩니다.

```azurepowershell
Connect-AzAccount
```

둘 이상의 구독이 있는 경우 Azure 구독 목록을 가져옵니다.

```azurepowershell-interactive
Get-AzSubscription
```

사용할 구독을 지정합니다.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```