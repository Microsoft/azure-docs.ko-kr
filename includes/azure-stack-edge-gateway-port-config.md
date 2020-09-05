---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/16/2019
ms.author: alkohli
ms.openlocfilehash: baf18ae0263215e6ff83570557255d06c3117fd4
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89085278"
---
| 포트 번호| 인 또는 아웃 | 포트 범위| 필수|   참고 |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80(HTTP)|아웃|WAN |아니요|업데이트 복구를 위한 인터넷 액세스에는 아웃바운드 포트가 사용됩니다. <br>아웃바운드 웹 프록시는 사용자가 구성할 수 있습니다. |
| TCP 443(HTTPS)|아웃|WAN|예|아웃바운드 포트는 클라우드의 데이터에 액세스하는 데 사용됩니다.<br>아웃바운드 웹 프록시는 사용자가 구성할 수 있습니다.|
| UDP 123(NTP)|아웃|WAN|일부 경우<br>참고 사항 보기|이 포트는 인터넷 기반 NTP 서버로 사용하는 경우에만 필요합니다.  |   
| UDP 53(DNS)|아웃|WAN|일부 경우<br>참고 사항 보기|이 포트는 인터넷 기반 DNS 서버로 사용하는 경우에만 필요합니다.<br>로컬 DNS 서버를 사용하는 것이 좋습니다. |
| TCP 5985(WinRM)|아웃/인|LAN|일부 경우<br>참고 사항 보기|이 포트는 HTTP에서 원격 PowerShell을 통해 디바이스에 연결해야 합니다.  |
| UDP 67(DHCP)|아웃|LAN|일부 경우<br>참고 사항 보기|이 포트는 로컬 DHCP 서버를 사용 중인 경우에만 필요합니다.  |
| TCP 80(HTTP)|아웃/인|LAN|예|이 포트는 로컬 관리용 디바이스의 로컬 UI에 사용되는 인바운드 포트입니다. <br>HTTP를 통해 로컬 UI에 액세스할 경우 HTTPS로 자동으로 리디렉션됩니다.  |
| TCP 443(HTTPS)|아웃/인|LAN|예|이 포트는 로컬 관리용 디바이스의 로컬 UI에 사용되는 인바운드 포트입니다. 이 포트는 장치 로컬 Api에 Azure Resource Manager 연결 하 고, REST Api를 통해 Blob 저장소를 연결 하 고, 액세스 및 새로 고침 토큰을 통해 인증 하기 위해 STS (보안 토큰 서비스)에 연결 하는 데도 사용 됩니다.|
| TCP 445(SMB)|In(다음 안에)|LAN|일부 경우<br>참고 사항 보기|이 포트는 SMB를 통해 연결하는경우에만 필요합니다. |
| TCP 2049(NFS)|In(다음 안에)|LAN|일부 경우<br>참고 사항 보기|이 포트는 NFS를 통해 연결하는경우에만 필요합니다. |


