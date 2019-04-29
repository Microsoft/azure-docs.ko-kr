---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 853bd32cf3ea97571929d54fb7d3ba04bde16b81
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405847"
---
이 구성을 시작하기 전에 Azure 계정에 로그인해야 합니다. cmdlet에서 Azure 계정에 대한 로그인 자격 증명을 입력하라는 메시지를 표시합니다. 로그인한 다음, Azure PowerShell에 사용할 수 있도록 계정 설정을 다운로드합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](../articles/powershell-azure-resource-manager.md)을 참조하세요.

로그인하려면 상승된 권한으로 PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

```powershell
Connect-AzAccount
```

Azure 구독이 여러 개인 경우 계정의 구독을 확인합니다.

```powershell
Get-AzSubscription
```

사용할 구독을 지정합니다.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
