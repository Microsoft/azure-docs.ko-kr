---
title: 포함 파일
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 05/26/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: cefa6fab5bbd140f8540ebb78f66706beeffd530
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578301"
---
1. **VPN 사이트 연결** 을 선택하여 **사이트 연결** 페이지를 엽니다.

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="스크린샷은 미리 공유한 키 및 관련 설정에 대해 준비된 Virtual HUB의 연결된 사이트 창을 보여줍니다." border="false":::

   다음 필드를 작성합니다.

   * 미리 공유한 키를 입력합니다. 키를 입력하지 않으면 Azure에서 자동으로 키를 생성합니다.
   * 프로토콜 및 IPsec 설정을 선택합니다. 자세한 내용은 [기본/사용자 지정 IPsec](../articles/virtual-wan/virtual-wan-ipsec.md)을 참조하세요.
   * **기본 경로 전파** 에 대한 적절한 옵션을 선택합니다. **사용** 옵션을 설정하면 가상 허브가 학습한 기본 경로를 이 연결에 전파합니다. 허브에 방화벽을 배포하여 Virtual WAN 허브에서 기본 경로를 이미 학습한 경우나 연결된 다른 사이트에서 강제로 터널링을 사용한 경우에만 이 플래그가 연결에 기본 경로 전파를 사용하도록 설정합니다. 기본 경로는 Virtual WAN 허브에서 시작되지 않습니다.

2. **연결** 을 선택합니다.
3. 몇 분 후에 사이트에 연결 및 연결 상태가 표시됩니다.

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="스크린샷은 사이트 간 연결 및 연결 상태를 보여줍니다." lightbox="./media/virtual-wan-tutorial-connect-vpn-site-include/status.png"border="false":::

   **연결 상태:** VPN 사이트를 Azure 허브의 VPN 게이트웨이에 연결하는 연결에 대한 Azure 리소스의 상태입니다. 이 제어 영역 작업이 성공적이면 Azure VPN 게이트웨이와 온-프레미스 VPN 디바이스가 연결 설정을 진행합니다.

   **연결 상태:** 허브에 있는 Azure의 VPN 게이트웨이와 VPN 사이트 사이의 실제 연결(데이터 경로) 상태입니다. 다음과 같은 상태가 표시될 수 있습니다.

    * **알 수 없음**: 이 상태는 대개 백엔드 시스템이 다른 상태로 전환 작업 중인 경우 표시됩니다.
    * **연결 중**: Azure VPN 게이트웨이가 실제 온-프레미스 VPN 사이트에 연결하는 중입니다.
    * **연결됨**: Azure VPN 게이트웨이와 온-프레미스 VPN 사이트 사이에 연결이 설정되었습니다.
    * **연결 끊김**: 이 상태는 어떤 이유로(온-프레미스 또는 Azure에서) 연결이 끊어진 경우 표시됩니다.
4. 허브 VPN 사이트 내에서 다음 작업을 추가로 수행할 수 있습니다. 

   * VPN 연결을 편집하거나 삭제할 수 있습니다.
   * Azure Portal에서 사이트를 삭제할 수 있습니다.
   * 사이트 옆의 상황에 맞는 메뉴(…)로 분기별 구성을 다운로드하여 Azure 측에 대한 자세한 정보를 볼 수 있습니다. 허브의 모든 연결된 사이트에 대한 구성을 다운로드하려면 최상위 메뉴에서 **VPN 구성 다운로드** 를 선택합니다.
