---
title: StorSimple 8000 시리즈 디바이스에서 StorSimple 볼륨 컨테이너 관리 | Microsoft Docs
description: StorSimple 디바이스 관리자 서비스 볼륨 컨테이너 페이지를 사용하여 볼륨 컨테이너를 추가, 수정 또는 삭제하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/19/2017
ms.author: alkohli
ms.openlocfilehash: 7e1a5ac2c2b734c77fc3dbe788206f8c75044953
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60724742"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 볼륨 컨테이너 관리

## <a name="overview"></a>개요
이 자습서는 StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 볼륨 컨테이너를 만들고 관리하는 방법에 대해 설명합니다.

Microsoft Azure StorSimple 디바이스의 볼륨 컨테이너는 저장소 계정, 암호화 및 대역폭 소비 설정을 공유하는 하나 이상의 볼륨을 포함합니다. 디바이스는 모든 볼륨에 대해 여러 볼륨 컨테이너가 있을 수 있습니다. 

볼륨 컨테이너에는 다음 특성이 있습니다.

* **볼륨** – 볼륨 컨테이너 내에 포함된 계층화되거나 로컬로 고정된 StorSimple 볼륨입니다. 
* **암호화** -각 볼륨 컨테이너에 대해 암호화 키를 정의할 수 있습니다. 이 키는 StorSimple 디바이스에서 클라우드로 전송되는 데이터를 암호화하는데 사용됩니다. 군사 등급 AES 256 비트 키는 사용자가 입력한 키로 사용됩니다. 데이터를 보호하려면 항상 클라우드 저장소 암호화를 사용하는 것이 좋습니다.
* **스토리지 계정** – 데이터를 저장하는 데 사용되는 Azure Storage 계정입니다. 볼륨 컨테이너에 있는 모든 볼륨은 이 저장소 계정을 공유합니다. 기존 목록에서 저장소 계정을 선택하거나 볼륨 컨테이너를 만들고 해당 계정에 대한 액세스 자격 증명을 지정하는 경우 새 계정을 만들 수 있습니다.
* **클라우드 대역폭** – 데이터가 디바이스에서 클라우드로 전송될 때 디바이스에서 소비되는 대역폭입니다. 이 컨테이너를 만드는 경우 1Mbps ~ 1000Mbps 사이의 값을 지정하여 대역폭 제어를 적용할 수 있습니다. 디바이스에서 사용 가능한 모든 대역폭 사용을 원하는 경우 이 필드를 **무제한**으로 설정합니다. 일정에 따라 대역폭을 할당하도록 대역폭 서식 파일을 만들어 적용할 수도 있습니다.

다음 이 절차는 StorSimple **볼륨 컨테이너** 블레이드를 사용하여 다음 일반적인 작업을 완료하는 방법을 설명합니다.

* 볼륨 컨테이너 추가
* 볼륨 컨테이너 수정
* 볼륨 컨테이너 삭제

## <a name="add-a-volume-container"></a>볼륨 컨테이너 추가
볼륨 컨테이너를 추가하려면 다음 단계를 수행합니다.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>볼륨 컨테이너 수정
볼륨 컨테이너를 수정하려면 다음 단계를 수행합니다.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>볼륨 컨테이너 삭제
볼륨 컨테이너 내에 볼륨이 있습니다. 안에 포함된 모든 볼륨이 먼저 삭제되는 경우에만 삭제될 수 있습니다. 볼륨 컨테이너를 삭제하려면 다음 단계를 수행합니다.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>다음 단계
* [StorSimple 볼륨 관리](storsimple-8000-manage-volumes-u2.md)에 대해 자세히 알아봅니다. 
* [StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스를 관리하는 방법](storsimple-8000-manager-service-administration.md)에 대해 자세히 알아봅니다.

