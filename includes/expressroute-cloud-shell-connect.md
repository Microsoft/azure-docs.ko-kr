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
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60364767"
---
 Azure Cloud Shell을 사용 하는 경우 로그인 할 Azure 계정에 자동으로 '평가판 사용해 보기'를 클릭 한 후. 로컬 로그인 하려면 상승 된 권한으로 PowerShell 콘솔을 엽니다 및 연결할 cmdlet을 실행 합니다.

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