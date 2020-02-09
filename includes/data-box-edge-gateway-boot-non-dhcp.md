---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 880b630ae48eda086f6454f0d7108d27d3403b77
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182871"
---
비 DHCP 환경에서 부팅 하는 경우 데이터 상자 게이트웨이에 대한 가상 컴퓨터를 배포 하려면 다음이 단계를 따릅니다.

1. [장치의 Windows PowerShell 인터페이스를 연결할](#connect-to-the-powershell-interface)합니다.
2. 사용 된 `Get-HcsIpAddress` 가상 장치에서 사용 하도록 설정 하는 네트워크 인터페이스를 나열 하는 cmdlet입니다. 디바이스에 사용하도록 설정된 네트워크 인터페이스가 하나인 경우에는 `Ethernet`이라는 기본 이름이 인터페이스에 할당됩니다.

    다음 예제에서는이 cmdlet의 사용법을 보여 줍니다.

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. `Set-HcsIpAddress` cmdlet을 사용하여 네트워크를 구성합니다. 다음 예제를 참조하세요.

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

