---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 98f9e0377e560fa0bba2fd470ff01431b2ed21d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754684"
---
| 포트 번호| 인 또는 아웃 | 포트 범위| 필수|   메모 |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80(HTTP)|아웃|WAN |아닙니다.|업데이트 복구를 위한 인터넷 액세스에는 아웃바운드 포트가 사용됩니다. <br>아웃바운드 웹 프록시는 사용자가 구성할 수 있습니다. |
| TCP 443(HTTPS)|아웃|WAN|yes|아웃바운드 포트는 클라우드의 데이터에 액세스하는 데 사용됩니다.<br>아웃바운드 웹 프록시는 사용자가 구성할 수 있습니다.|
| UDP 123(NTP)|아웃|WAN|일부 경우<br>참고 사항 보기|이 포트는 인터넷 기반 NTP 서버로 사용하는 경우에만 필요합니다.  |   
| UDP 53(DNS)|아웃|WAN|일부 경우<br>참고 사항 보기|이 포트는 인터넷 기반 DNS 서버로 사용하는 경우에만 필요합니다.<br>로컬 DNS 서버를 사용하는 것이 좋습니다. |
| TCP 5985(WinRM)|아웃/인|LAN|일부 경우<br>참고 사항 보기|이 포트는 HTTP에서 원격 PowerShell을 통해 디바이스에 연결해야 합니다.  |
| UDP 67(DHCP)|아웃|LAN|일부 경우<br>참고 사항 보기|이 포트는 로컬 DHCP 서버를 사용 중인 경우에만 필요합니다.  |
| TCP 80(HTTP)|아웃/인|LAN|예|이 포트는 로컬 관리용 디바이스의 로컬 UI에 사용되는 인바운드 포트입니다. <br>HTTP를 통해 로컬 UI에 액세스할 경우 HTTPS로 자동으로 리디렉션됩니다.  |
| TCP 443(HTTPS)|아웃/인|LAN|예|이 포트는 로컬 관리용 디바이스의 로컬 UI에 사용되는 인바운드 포트입니다. |
| TCP 445(SMB)|그런 다음|LAN|일부 경우<br>참고 사항 보기|이 포트는 SMB를 통해 연결하는경우에만 필요합니다. |
| TCP 2049(NFS)|그런 다음|LAN|일부 경우<br>참고 사항 보기|이 포트는 NFS를 통해 연결하는경우에만 필요합니다. |
