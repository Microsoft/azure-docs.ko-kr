---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 393b29245141b2970e7c1a227d6e8b1b131c445c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93061637"
---
클라이언트 인증서를 생성하는 데 사용한 것 외의 클라이언트 컴퓨터에서 P2S 연결을 만들려는 경우 클라이언트 인증서를 설치해야 합니다. 클라이언트 인증서를 설치하는 경우 클라이언트 인증서를 내보낼 때 만든 암호가 필요합니다.

1. *.pfx* 파일을 찾아 클라이언트 컴퓨터에 복사합니다. 클라이언트 컴퓨터에서 *.pfx* 파일을 두 번 클릭하여 설치합니다. **저장소 위치** 를 **현재 사용자** 로 유지한 후, **다음** 을 선택합니다.
1. 가져올 **파일** 페이지에서 아무 것도 변경하지 않습니다. **다음** 을 선택합니다.
1. **프라이빗 키 보호** 페이지에서 인증서의 암호를 입력하거나 보안 주체가 올바른지 확인한 후, **다음** 을 선택합니다.
1. **인증서 저장소** 페이지에서 기본 위치를 유지한 후, **다음** 을 선택합니다.
1. **마침** 을 선택합니다. 인증서 설치에 대한 **보안 경고** 에서 **예** 를 선택합니다. 인증서를 생성했으므로 이 보안 경고에 대해 '예'를 편리하게 선택할 수 있습니다.
1. 이제 인증서를 성공적으로 가져왔습니다.
