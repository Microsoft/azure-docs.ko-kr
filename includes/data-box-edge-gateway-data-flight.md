---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754287"
---
이동 중인 데이터의 경우

- 장치와 Azure 간에 전송 되는 데이터에 대 한 표준 TLS 1.2 사용 됩니다. TLS 1.1 및 이전 버전에 없는 대체 방법이 있습니다. TLS 1.2 지원 되지 않는 경우 에이전트 통신이 차단 됩니다. TLS 1.2 포털 및 SDK 관리도 필요 합니다.
- 장치의 로컬 웹 브라우저의 UI 통해 액세스 하는 클라이언트에 표준 TLS 1.2는 기본 보안 프로토콜로 사용 됩니다.

    - TLS 1.2를 사용 하도록 브라우저를 구성 하는 것이 좋습니다.
    - 브라우저에서 TLS 1.2를 지원 하지 않으면 TLS 1.1 또는 TLS 1.0을 사용할 수 있습니다.
- 데이터 서버 로부터 복사할 때 데이터를 보호 하기 위해 SMB 3.0 암호화를 사용 하 여 사용 하는 것이 좋습니다.
