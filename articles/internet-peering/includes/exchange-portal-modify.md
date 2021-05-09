---
title: 포함 파일
titleSuffix: Azure
description: 포함 파일
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "81680953"
---
이 섹션에서는 직접 피어링에 대해 다음과 같은 수정 작업을 수행하는 방법을 설명합니다.

### <a name="add-exchange-peering-connections"></a>Exchange 피어링 연결 추가

1. **+ 연결 추가** 단추를 선택하고 새 피어링 연결을 구성합니다.
    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 보기](../media/setup-exchange-modify-addconnection.png)
1. **Exchange 피어링 연결** 양식을 작성하고 **저장** 을 선택합니다. 피어링 연결을 구성하는 방법에 대한 도움말을 보려면 "직접 피어링 만들기 및 프로비전" 섹션의 단계를 검토하세요.
    > [!div class="mx-imgBorder"]
    > ![Exchange 피어링 연결 양식](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Exchange 피어링 연결 제거

1. 삭제하려는 피어링 연결을 선택한 다음, **...**  > **연결 삭제** 를 선택합니다.
    > [!div class="mx-imgBorder"]
    > ![연결 삭제 단추](../media/setup-exchange-modify-deleteconnection.png)
1. **삭제 확인** 상자에 리소스 ID를 입력하고 **삭제** 를 선택합니다.
    > [!div class="mx-imgBorder"]
    > ![삭제 확인](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>활성 연결에서 IPv4 또는 IPv6 세션 추가

1. 수정하려는 피어링 연결을 선택한 다음, **...**  > **연결 편집** 을 선택합니다.
    > [!div class="mx-imgBorder"]
    > ![연결 편집 단추](../media/setup-exchange-modify-editconnection.png)
1. **IPv4 주소** 또는 **IPv6 주소** 정보를 추가한 다음, **저장** 을 선택합니다.
    > [!div class="mx-imgBorder"]
    > ![피어링 연결 수정](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>활성 연결에서 IPv4 또는 IPv6 세션 제거

기존 연결에서 IPv4 또는 IPv6 세션을 제거하는 것은 현재 포털에서 지원되지 않습니다. 자세한 내용은 [Microsoft 피어링](mailto:peeringexperience@microsoft.com)에 문의하세요.