---
title: Azure에서 부하 분산 장치 TCP 유휴 시간 제한 구성
titleSuffix: Azure Load Balancer
description: 이 문서에서는 Azure Load Balancer TCP 유휴 시간 제한을 구성하는 방법에 대해 알아봅니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 317f6a73812b0e4284564ca9b5593e09e22edf12
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048723"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Azure Load Balancer에 대한 TCP 유휴 시간 제한 설정 구성

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="tcp-idle-timeout"></a>TCP 유휴 시간 제한
Azure Load Balancer의 유휴 시간 제한 설정은 4~30분입니다. 기본적으로 4분으로 설정되어 있습니다. 비활성 기간이 시간 제한 값보다 긴 경우 클라이언트와 클라우드 서비스 간의 TCP 또는 HTTP 세션이 유지되지 않을 수 있습니다.

연결이 닫혀 있는 경우 클라이언트 애플리케이션이 다음 오류 메시지를 수신할 수 있습니다. “기본 연결이 닫혔습니다. 활성 상태로 유지될 것으로 예상된 연결이 서버에서 닫혔습니다.”

일반적인 방법은 TCP 연결 유지를 사용하는 것입니다. 이 방법은 더 오랜 기간 동안 연결을 활성 상태로 유지합니다. 자세한 내용은 이러한 [.NET 예제](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)를 참조하세요. 연결 유지를 사용하면 연결 비활성화 기간 동안 패킷이 전송됩니다. 연결 유지 패킷은 유휴 시간 제한 값에 도달하지 않고 연결이 장기간 유지되도록 합니다.

이 설정은 인바운드 연결에서만 작동합니다. 연결 끊김을 방지하려면 유휴 시간 제한 설정보다 낮은 간격으로 TCP 연결 유지를 구성하거나 유휴 시간 제한 값을 늘립니다. 이러한 시나리오를 지원하기 위해 구성 가능한 유휴 시간 제한에 대한 지원이 추가되었습니다.

TCP 연결 유지는 배터리 수명이 제한되지 않는 시나리오에서 작동합니다. 모바일 애플리케이션에는 권장되지 않습니다. 모바일 애플리케이션에서 TCP 연결 유지를 사용하면 디바이스 배터리가 더 빨리 방전될 수 있습니다.

![TCP 시간 제한](./media/load-balancer-tcp-idle-timeout/image1.png)

다음 섹션에서는 공용 IP 및 부하 분산 장치 리소스의 유휴 시간 제한 설정을 변경하는 방법을 설명합니다.

>[!NOTE]
> TCP 유휴 시간 제한은 UDP 프로토콜의 부하 분산 규칙에 영향을 주지 않습니다.


## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>인스턴스 수준 공용 IP의 TCP 시간 제한을 15분으로 구성

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes`는 선택 사항입니다. 설정하지 않으면 기본 시간 제한은 4분입니다. 허용되는 시간 제한 범위는 4분에서 30분 사이입니다.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>부하 분산된 규칙의 TCP 시간 제한을 15분으로 설정

부하 분산 장치의 유휴 시간 제한을 설정하기 위해 부하 분산 규칙에 'IdleTimeoutInMinutes'가 설정되어 있습니다. 다음은 그 예입니다.

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>다음 단계

[내부 부하 분산 장치 개요](load-balancer-internal-overview.md)

[인터넷 연결 부하 분산 장치 구성 시작](quickstart-load-balancer-standard-public-powershell.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)
