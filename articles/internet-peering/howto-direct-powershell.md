---
title: PowerShell을 사용 하 여 직접 피어 링 만들기 또는 수정
titleSuffix: Azure
description: PowerShell을 사용 하 여 직접 피어 링 만들기 또는 수정
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: be4bab10d369fcefe626bbe890acfff7fc7004b0
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079067"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>PowerShell을 사용 하 여 직접 피어 링 만들기 또는 수정

이 문서에서는 PowerShell cmdlet 및 Azure Resource Manager 배포 모델을 사용 하 여 Microsoft 직접 피어 링을 만드는 방법을 설명 합니다. 또한이 문서에서는 리소스의 상태를 확인 하거나, 업데이트 하거나, 삭제 하 고 프로 비전을 해제 하는 방법을 보여 줍니다.

선호 하는 경우 Azure [portal](howto-direct-portal.md)을 사용 하 여이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작하기 전에 [필수 구성 요소](prerequisites.md) 및 [직접 피어링 연습](walkthrough-direct-all.md)을 검토합니다.
* Azure 리소스로 변환 되지 않은 Microsoft와 직접 피어 링 연결이 이미 있는 경우 [PowerShell을 사용 하 여 레거시 직접 피어 링을 azure 리소스로 변환](howto-legacy-direct-powershell.md)을 참조 하세요.

### <a name="work-with-azure-powershell"></a>Azure PowerShell 작업
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>직접 피어링 만들기 및 프로비저닝

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure 계정에 로그인하고 구독을 선택합니다.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>직접 피어 링에 대해 지원 되는 피어 링 위치 목록 가져오기
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>직접 피어링 만들기
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>직접 피어링 확인
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>직접 피어링 수정
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>직접 피어링 프로비저닝 해제
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>다음 단계

* [PowerShell을 사용 하 여 Exchange 피어 링 만들기 또는 수정](howto-exchange-powershell.md)
* [PowerShell을 사용 하 여 레거시 Exchange 피어 링을 Azure 리소스로 변환](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>추가 리소스
다음 명령을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

```powershell
Get-Help Get-AzPeering -detailed
```

자세한 내용은 [인터넷 피어링 FAQ](faqs.md)를 참조하세요.
