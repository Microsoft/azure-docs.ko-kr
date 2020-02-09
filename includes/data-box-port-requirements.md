---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839757"
---
| 포트 번호| 인 또는 아웃 | 포트 범위| 필수| 메모 |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80(HTTP)|그런 다음|LAN|예|이 포트는 HTTP를 통한 데이터 상자 블로그 저장소 REST Api에 연결할 사용 됩니다. REST Api에 연결 하지,이 자동으로 리디렉션합니다 로컬 웹 UI에 8443 개. |
| TCP 443(HTTPS)|그런 다음|LAN|예|이 포트는 HTTPS를 통한 데이터 상자 블로그 저장소 REST Api에 연결할 사용 됩니다. REST Api에 연결 하지,이 자동으로 리디렉션합니다 로컬 웹 UI에 8443 개. |
| TCP 8443 (HTTPS Alt)|입력|LAN|예|HTTPS에 대해 대체 포트 이며 장치 관리에 대한 로컬 웹 UI에 연결할 때 사용 됩니다. |
| TCP 445(SMB)|아웃/인|LAN|일부 경우<br>메모를 참조하세요.|이 포트는 SMB를 통해 연결 하는 경우에 필요 합니다. |
| TCP 2049(NFS)|아웃/인|LAN|일부 경우<br>메모를 참조하세요.|이 포트는 NFS를 통해 연결 하는 경우에 필요 합니다. |
| TCP 111 (NFS)|아웃/인|LAN|일부 경우<br>메모를 참조하세요.|이 포트 rpcbind/포트 매핑에 사용 되 고 NFS를 통해 연결 하는 경우에 필요 합니다.  |
