---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754272"
---
장치를 대상으로 Azure에서 데이터에 사용 되는 저장소 계정과 연결 됩니다. 저장소 계정에 대 한 액세스는 구독 및 두 개의 512 비트 저장소에 의해 제어 됩니다 해당 저장소 계정과 연결 된 키에 액세스 합니다.

키 중 하나는 데이터 상자 Edge 장치를 저장소 계정에 액세스할 때 인증에 사용 됩니다. 다른 키를 정기적으로 키를 회전할 수 있도록 예약 된 상태로 유지 됩니다.

보안상의 이유로 많은 데이터 센터 키 회전이 필요 합니다. 키 회전에 대해 이 모범 사례를 따르는 것이 좋습니다.

- 저장소 계정 키는 저장소 계정의 루트 암호와 비슷합니다. 계정 키를 신중 하 게 보호 합니다. 없는 다른 사용자에 게 암호를 배포, 하드, 코드 또는 다른 사용자에 게 액세스할 수 있는 일반 텍스트로 아무 곳 이나 저장
- [계정 키를 다시 생성](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) Azure를 통해 포털 생각 하는 경우를 손상 될 수 있습니다.
- Azure 관리자에 게 주기적으로 변경 하거나 저장소 계정에 직접 액세스 하려면 Azure portal의 [저장소] 섹션을 사용 하 여 기본 또는 보조 키 다시 생성 해야 합니다.