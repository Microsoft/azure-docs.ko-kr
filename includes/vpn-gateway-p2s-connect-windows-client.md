---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: 15c29648e42ba190991d51188489883e29bee165
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041531"
---
>[!NOTE]
>연결하는 Windows 클라이언트 컴퓨터에서 관리자 권한이 있어야 합니다.
>

1. VNet에 연결하려면 클라이언트 컴퓨터에서 VPN 설정으로 이동하여 사용자가 만든 VPN 연결을 찾습니다. 가상 네트워크와 동일한 이름이 지정됩니다. **연결** 을 선택합니다. 인증서 사용을 안내하는 팝업 메시지가 나타날 수 있습니다. **계속** 을 선택하여 상승된 권한을 사용합니다.

1. **연결** 상태 페이지에서 **연결** 을 선택하여 연결을 시작합니다. **인증서 선택** 화면에서 표시되는 클라이언트 인증서가 연결하는 데 사용할 인증서인지 확인합니다. 그렇지 않은 경우 드롭다운 화살표를 사용하여 올바른 인증서를 선택한 다음, **확인** 을 선택합니다.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connection-status.png" alt-text="Windows 컴퓨터에서 연결":::

1. 연결이 설정되었습니다.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connected.png" alt-text="컴퓨터에서 Azure VNet에 연결 - 지점 및 사이트 간 연결 다이어그램":::
