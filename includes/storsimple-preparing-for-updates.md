---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4e262c9e5bb88e77bc9c09853c06f4cdb41eedaa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860398"
---
## <a name="preparing-for-updates"></a>업데이트 준비
업데이트를 스캔하고 적용하기 전에 다음 단계를 수행해야 합니다.

1. 디바이스 데이터의 클라우드 스냅숏을 만듭니다.
2. 컨트롤러 고정 IP가 라우팅할 수 있으며 인터넷에 연결할 수 있는지 확인합니다. 이러한 고정된 IP는 디바이스를 서비스 업데이트하는 데 사용됩니다. 디바이스의 Windows PowerShell 인터페이스에서 다음 cmdlet을 실행하여 이를 테스트할 수 있습니다.
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **고정된 IP가 인터넷에 연결할 수 있는 경우 테스트 연결에 대한 샘플 출력**

        Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

        Source      Destination     IPV4Address      IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200

        Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

        Source      Destination       IPV4Address    IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200

이러한 수동 사전 검사를 성공적으로 완료한 후에 업데이트 검색 및 설치를 진행할 수 있습니다.

