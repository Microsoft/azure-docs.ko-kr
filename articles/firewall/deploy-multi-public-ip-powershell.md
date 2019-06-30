---
title: Azure PowerShell을 사용 하 여 여러 공용 IP 주소를 사용 하 여 Azure 방화벽 배포
description: 이 문서에서는 Azure PowerShell을 사용 하 여 여러 공용 IP 주소를 사용 하 여 Azure 방화벽을 배포 하는 방법을 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/21/2019
ms.author: victorh
ms.openlocfilehash: 9ec37197376c815c4fb9072164520a707b02be2b
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312734"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Azure PowerShell을 사용 하 여 여러 공용 IP 주소를 사용 하 여 Azure 방화벽을 배포

> [!IMPORTANT]
> 여러 공용 IP 주소를 사용 하 여 azure 방화벽 현재 공개 미리 보기 중입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

최대 600 개의 공용 IP 주소를 사용 하 여 Azure 방화벽을 배포할 수 있습니다.

이 기능을 사용 하면 다음과 같은 경우:

- **DNAT** -백 엔드 서버에 여러 표준 포트 인스턴스를 변환할 수 있습니다. 예를 들어 두 개의 공용 IP 주소에 있는 경우 두 IP 주소에 대 한 TCP 포트 3389(rdp)를 번역할 수 있습니다.
- **SNAT** -추가 포트를 SNAT 포트 소모가 발생할 가능성이 줄어듭니다 아웃 바운드 SNAT 연결에 사용할 수 있습니다. 이때 Azure 방화벽 연결에 사용할 원본 공용 IP 주소를 임의로 선택 합니다. 네트워크에서 다운스트림 필터링에 있는 경우 방화벽을 사용 하 여 연결 된 모든 공용 IP 주소를 허용 해야 합니다.

다음 Azure PowerShell 예제를 추가, 제거 하는 방법 Azure 방화벽에 대 한 공용 IP 주소를 구성에 표시 합니다.

> [!NOTE]
> 공개 미리 보기에서 추가 하거나 실행 중인 방화벽에 공용 IP 주소를 제거 하면 DNAT 규칙을 사용 하 여 기존 인바운드 연결이 작동 하지 않을 40 120 초. 방화벽을 할당 취소 하거나 삭제 하는 경우가 아니면 방화벽에 할당 된 첫 번째 공용 IP 주소를 제거할 수 없습니다.

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>기존 방화벽에 공용 IP 주소 추가

이 예제에서는 공용 IP 주소 *azFwPublicIp1* 방화벽에 연결 합니다.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>기존 방화벽에서 공용 IP 주소를 제거 합니다.

이 예제에서는 공용 IP 주소 *azFwPublicIp1* 으로 방화벽에서 분리 합니다.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>두 개 이상의 공용 IP 주소를 사용 하 여 방화벽 만들기

이 예제에서는 가상 네트워크에 연결 된 방화벽을 만듭니다 *vnet* 두 개의 공용 IP 주소를 사용 하 여 합니다.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="next-steps"></a>다음 단계

* [자습서: Azure Firewall 로그 모니터링](./tutorial-diagnostics.md)
