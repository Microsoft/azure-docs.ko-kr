---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8840e6649058837207c75dbdf7baa5c0b11ef387
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467656"
---
장치는 Azure에서 데이터의 대상으로 사용 되는 저장소 계정과 연결 됩니다. 저장소 계정에 대한 액세스는 해당 저장소 계정과 연결 된 구독 및 2 512 비트 저장소 액세스 키에 의해 제어 됩니다.

키 중 하나는 Data Box Edge 장치에서 저장소 계정에 액세스할 때 인증에 사용 됩니다. 다른 키는 예약에 보관 되므로 정기적으로 키를 회전할 수 있습니다.

보안상의 이유로 많은 데이터 센터에 키 회전이 필요 합니다. 키 회전에 대해 이 모범 사례를 따르는 것이 좋습니다.

- 스토리지 계정 키는 스토리지 계정의 루트 암호와 비슷합니다. 계정 키를 신중 하 게 보호 합니다. 암호를 다른 사용자에 게 배포 하거나 하드 코딩 하거나 다른 사용자가 액세스할 수 있는 일반 텍스트의 어디에 나 저장 하지 마세요.
- 손상 될 수 있다고 생각 되는 경우 Azure Portal를 통해 계정 키를 다시 생성 합니다. 자세한 내용은 [저장소 계정 액세스 키 관리](../articles/storage/common/storage-account-keys-manage.md)를 참조 하세요.
- Azure 관리자는 Azure Portal의 저장소 섹션을 사용 하 여 기본 또는 보조 키를 정기적으로 변경 하거나 다시 생성 하 여 저장소 계정에 직접 액세스 해야 합니다.