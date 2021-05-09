---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 11/28/2019
ms.author: raynew
ms.openlocfilehash: de15e3028cf22cdd03ce29385278fc5e2babaa9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008487"
---
이 문서에서는 다음을 가정합니다.

1. 온-프레미스 네트워크와 Azure Virtual Network 간의 **사이트 간 VPN** 또는 **Express 경로** 가 이미 설정되었다고 가정합니다.
2. 사용자 계정은 가상 머신을 장애 조치할 Azure 구독에서 새 가상 머신을 만들 수 있는 권한을 보유합니다.
3. 구독에는 새로운 프로세스 서버 가상 머신을 스핀업하는 데 사용할 수 있는 최소 8개의 코어가 있습니다.
4. **구성 서버 암호** 를 사용할 수 있습니다.

> [!TIP]
> 가상 머신을 장애 조치할 Azure Virtual Network에서 구성 서버(온-프레미스 실행)의 포트 443에 연결할 수 있는지 확인합니다.
