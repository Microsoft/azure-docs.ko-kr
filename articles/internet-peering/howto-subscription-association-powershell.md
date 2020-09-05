---
title: PowerShell을 사용 하 여 피어 ASN을 Azure 구독에 연결
titleSuffix: Azure
description: PowerShell을 사용 하 여 피어 ASN을 Azure 구독에 연결
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aed079c467139ac5819951c5895ba753ee38ae2d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89067742"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>PowerShell을 사용 하 여 피어 ASN을 Azure 구독에 연결

피어링 요청을 제출하기 전에 먼저 아래 단계를 사용하여 ASN을 Azure 구독과 연결해야 합니다.

원한다 면 [포털](howto-subscription-association-portal.md)을 사용 하 여이 가이드를 완료할 수 있습니다.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>PeerASN을 만들어 Azure 구독과 ASN 연결

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure 계정에 로그인하고 구독을 선택합니다.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>피어링 리소스 공급자 등록
다음 명령을 사용 하 여 구독에서 피어 링 리소스 공급자를 등록 합니다. 이 작업을 실행하지 않으면 피어링을 설정하는 데 필요한 Azure 리소스에 액세스할 수 없습니다.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

다음 명령을 사용 하 여 등록 상태를 확인할 수 있습니다.
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> 계속 하기 전에 *Registrationstate* 가 "등록 됨"으로 전환 될 때까지 기다립니다. 명령을 실행 한 후에는 5 ~ 30 분 정도 걸릴 수 있습니다.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>이 구독과 연결 된 피어 정보 업데이트

다음은 피어 정보를 업데이트 하는 예제입니다.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -Name은 리소스 이름에 해당 하며 선택 하는 모든 이름일 수 있습니다. 그러나-peerName은 회사의 이름에 해당 하며, PeeringDB 프로필에 최대한 가까이 있어야 합니다. -PeerName의 값은 문자 a-z, a-z 및 공백만 지원 합니다.

구독에는 여러 개의 ASNs가 있을 수 있습니다. 각 ASN에 대 한 피어 링 정보를 업데이트 합니다. "이름"이 각 ASN에 대해 고유한 지 확인 합니다.

피어는 [Peeringdb](https://www.peeringdb.com)에 완전 하 고 최신 프로필이 있어야 합니다. 등록 하는 동안이 정보를 사용 하 여 NOC 정보, 기술 연락처 정보 및 피어 링 기능에서의 현재 상태와 같은 피어의 세부 정보에 대 한 유효성을 검사 합니다.

위의 출력에서 **{subscriptionId}** 대신 실제 구독 ID가 표시 됩니다.

## <a name="view-status-of-a-peerasn"></a>PeerASN의 상태 보기

다음 명령을 사용 하 여 ASN 유효성 검사 상태를 확인 합니다.

```powershell
Get-AzPeerAsn
```

다음은 응답 예제입니다.
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> 피어링 요청을 제출하기 전에 ValidationState가 "승인됨"으로 전환될 때까지 기다립니다. 이 승인에는 최대 12시간이 걸릴 수 있습니다.

## <a name="modify-peerasn"></a>PeerAsn 수정
언제 든 지 NOC 연락처 정보를 수정할 수 있습니다.

예제는 아래와 같습니다.

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>PeerAsn 삭제
PeerASN 삭제는 현재 지원 되지 않습니다. PeerASN을 삭제 해야 하는 경우 [Microsoft 피어 링](mailto:peering@microsoft.com)에 문의 하세요.

## <a name="next-steps"></a>다음 단계

* [직접 피어링 만들기 또는 수정](howto-direct-powershell.md)
* [레거시 직접 피어링을 Azure 리소스로 변환](howto-legacy-direct-powershell.md)
* [Exchange 피어 링 만들기 또는 수정](howto-exchange-powershell.md)
* [레거시 Exchange 피어링을 Azure 리소스로 변환](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>추가 리소스

자세한 내용은 [인터넷 피어링 FAQ](faqs.md)를 방문하세요.
