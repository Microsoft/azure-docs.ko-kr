---
title: 게스트 OS 제품군 1 사용 중지 알림 | Microsoft Docs
description: Azure 게스트 OS 제품군 1 사용 중지가 발생한 시기 및 적용되는 지를 확인하는 방법에 대한 정보를 제공합니다.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
ms.service: cloud-services
ms.topic: article
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: ae2df6f47d99fc5d452a6d3ea70f2dd2e4e7416b
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359588"
---
# <a name="guest-os-family-1-retirement-notice"></a>게스트 OS 제품군 1 사용 중지 확인
OS 제품군 1의 사용 중지가 2013 년 6월 1일에 처음 발표되었습니다.

**2014 년 9월 2일** Windows Server 2008 운영 체제에 기반을 둔 Azure 게스트 운영 체제(게스트 OS) 제품군 1.x가 공식적으로 사용 중지되었습니다. 게스트 OS 제품군 1이 사용 중지된 것을 알리는 오류 메시지와 함께 새 서비스를 배포하거나 제품군 1을 사용하여 기존 서비스를 업그레이드 하려는 모든 시도가 실패했습니다.

**2014년 11월 3일** 게스트 OS 제품군 1에 대한 연장 지원이 종료되어 완전히 사용 중지됩니다. 제품군 1의 모든 서비스에 적용됩니다. 언제든지 이러한 서비스를 중지할 수 있습니다. 수동으로 직접 업그레이드하지 않으면 서비스가 계속된다는 보장이 없습니다.

추가 질문이 있으면 [Cloud Services 포럼](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc)을 방문하거나 [Azure 지원에 문의하세요](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>영향을 받나요?
다음 중 하나에 적용되는 경우 Cloud Services에 영향을 받습니다.

1. 클라우드 서비스에 대한 ServiceConfiguration.cscfg 파일에 명시적으로 지정된 "osFamily ="1"의 값이 있습니다.
2. 클라우드 서비스에 대한 ServiceConfiguration.cscfg 파일에 명시적으로 지정된 osFamily의 값이 없습니다. 현재, 이 경우 시스템은 "1"의 기본값을 사용합니다.
3. Azure Portal은 게스트 운영 체제 제품군 값을 "Windows Server 2008"로 표시합니다.

어떤 클라우드 서비스가 어떤 OS 제품군을 실행 중인지 알기 위해, Azure PowerShell에서 다음 스크립트를 실행할 수 있지만 먼저 [Azure PowerShell을 설정해야](/powershell/azureps-cmdlets-docs) 합니다. 스크립트에 대한 자세한 내용은 [Azure 게스트 OS 제품군 1의 수명 종료: 6 월](https://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx)2014.

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

스크립트 출력의 osFamily 열이 비어 있거나 "1"을 포함하는 경우 클라우드 서비스는 OS 제품군 1 사용 중지의 영향을 받습니다.

## <a name="recommendations-if-you-are-affected"></a>영향을 받는 경우 권장 사항
지원되는 게스트 OS 제품군 중 하나에 클라우드 서비스 역할을 마이그레이션하는 것이 좋습니다.

**게스트 OS 제품군 4.x** -Windows Server 2012 R2 *(권장)*

1. 애플리케이션이.NET framework 4.0, 4.5 또는 4.5.1과 함께 SDK 2.1 이상을 사용 중이어야 합니다.
2. ServiceConfiguration.cscfg 파일에서 osFamily 특성을 "4"으로 설정하고 클라우드 서비스를 다시 배포합니다.

**게스트 OS 제품군 3.x** -Windows Server 2012

1. 애플리케이션이.NET framework 4.0 또는 4.5와 함께 SDK 1.8 이상을 사용 중이어야 합니다.
2. ServiceConfiguration.cscfg 파일에서 osFamily 특성을 "3"으로 설정하고 클라우드 서비스를 다시 배포합니다.

**게스트 OS 제품군 2.x** -Windows Server 2008 R2

1. 애플리케이션이.NET framework 3.5 또는 4.0과 함께 SDK 1.3 이상을 사용 중이어야 합니다.
2. ServiceConfiguration.cscfg 파일에서 osFamily 특성을 "2"로 설정하고 클라우드 서비스를 다시 배포합니다.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>게스트 OS 제품군 1에 대한 연장된 지원이 2014년 11월 3일에 종료되었습니다.
게스트 OS 제품군 1에서 클라우드 서비스는 더 이상 지원 되지 않습니다. 서비스 중단을 방지하려면 가능한 빨리 제품군 1을 마이그레이션 해제하세요.  

## <a name="next-steps"></a>다음 단계
최신 [게스트 OS 릴리스](cloud-services-guestos-update-matrix.md)를 검토합니다.
