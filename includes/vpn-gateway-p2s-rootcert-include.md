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
ms.openlocfilehash: 4c8e7e5272f180c482ca7fdd44302f49eb888b25
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320130"
---
엔터프라이즈 솔루션을 사용하여 생성된 루트 인증서를 사용하거나(추천) 자체 서명된 인증서를 생성합니다. 루트 인증서가 만들어지면 공용 인증서 데이터(개인 키 아님)를 Base64 인코딩된 X.509 .cer 파일로 내보냅니다. 그런 다음, 공용 인증서 데이터를 Azure 서버에 업로드합니다.

* **엔터프라이즈 인증서:** 엔터프라이즈 솔루션을 사용하는 경우 기존 인증서 체인을 사용할 수 있습니다. 사용하려는 루트 인증서용 .cer 파일을 획득합니다.
* **자체 서명된 루트 인증서:** 엔터프라이즈 인증서 솔루션을 사용하지 않는 경우 자체 서명된 루트 인증서를 만듭니다. 그렇지 않으면 만든 인증서가 P2S 연결과 호환되지 않으며, 연결하려고 할 때 클라이언트에 연결 오류 메시지가 표시됩니다. Microsoft Azure PowerShell, MakeCert, 또는 OpenSSL을 사용할 수 있습니다. 다음 문서의 단계에서는 호환되는 자체 서명된 루트 인증서를 생성하는 방법을 설명합니다.

  * [Windows 10 PowerShell 지침](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): 이러한 지침에 따라 Windows 10 및 PowerShell에서 인증서를 생성해야 합니다. 루트 인증서에서 생성된 클라이언트 인증서는 지원되는 모든 P2S 클라이언트에 설치할 수 있습니다.
  * [MakeCert 지침](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): 인증서를 생성하는 데 사용할 Windows 10 컴퓨터에 대한 액세스 권한이 없는 경우 MakeCert를 사용합니다. MakeCert는 더 이상 사용되지 않지만, 인증서를 생성하는 데에는 여전히 사용할 수 있습니다. 루트 인증서에서 생성한 클라이언트 인증서는 지원되는 모든 P2S 클라이언트에 설치할 수 있습니다.
  * [Linux 지침](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)
