---
title: 포함 파일
description: 포함 파일
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 482241deb1081ac8a5265a076eabbdc3fb6d659e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743394"
---
시스템 경로를 사용하면 자동으로 트래픽을 쉽게 배포할 수 있지만 가상 어플라이언스를 통해 패킷 라우팅을 제어하고자 하는 경우도 있습니다. 이러한 경우, 특정 서브넷으로 흐르는 패킷이 가상 어플라이언스로 가도록 한 다음 홉을 지정하는 사용자 정의 경로를 생성하고 가상 어플라이언스로 실행되는 VM에서 IP를 전달하도록 설정하면 됩니다.

일부 가상 어플라이언스를 사용할 수 있는 경우는 다음과 같습니다.

* 침입 감지 시스템(IDS)을 사용하여 트래픽 모니터링
* 방화벽을 사용하여 트래픽 제어

UDR 및 IP 전달에 대한 자세한 내용은 [사용자 정의 경로 및 IP 전달](../articles/virtual-network/virtual-networks-udr-overview.md)을 참조하세요.