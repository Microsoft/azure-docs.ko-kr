---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 12/11/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: c0ce4e882f270f5e0c789a608aaada5c6c9cba92
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320105"
---
연결 문제가 있으면 다음 항목을 확인합니다.

- **인증서 내보내기 마법사**를 사용하여 클라이언트 인증서를 내보낸 경우, 이를 .pfx 파일로 내보내고 **가능한 경우 인증 경로에 있는 인증서 모두 포함**을 선택했는지 확인합니다. 이 값을 사용하여 인증서를 내보낼 때는 루트 인증서 정보도 내보냅니다. 인증서가 클라이언트 컴퓨터에 설치되면 .pfx 파일의 루트 인증서도 설치됩니다. 루트 인증서가 설치되어 있는지 확인하려면 **사용자 인증서 관리**를 열고 **신뢰할 수 있는 루트 인증 기관\인증서**를 선택합니다. 인증이 작동하는 데 필요한 루트 인증서가 나열되어 있는지 확인합니다.

- 엔터프라이즈 CA 솔루션에서 발급한 인증서를 사용했지만 인증할 수 없는 경우 클라이언트 인증서의 인증 순서를 확인합니다. 클라이언트 인증서를 두 번 클릭하고, **세부 정보** 탭을 선택한 다음, **확장된 키 사용**을 선택하여 인증 목록 순서를 확인합니다. *클라이언트 인증*이 목록의 첫 번째 항목인지 확인합니다. 그렇지 않으면 목록의 첫 번째 항목으로 *클라이언트 인증*이 있는 사용자 템플릿을 기반으로 하는 클라이언트 인증서를 발급합니다.

- 추가 P2S 문제 해결 정보는 [P2S 연결 문제 해결](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)을 참조하세요.
