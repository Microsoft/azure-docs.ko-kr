---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157224"
---
저장소 계정에 대 한 중복성 옵션은 다음과 같습니다.

* LRS (로컬 중복 저장소): 간단 하 고 저렴 한 중복성 전략입니다. 데이터는 주 지역 내에서 동기적으로 세 번 복사 됩니다.
* ZRS (영역 중복 저장소): 고가용성이 필요한 시나리오에 대 한 중복성 데이터는 주 지역에 있는 세 개의 Azure 가용성 영역에서 동기적으로 복사 됩니다.
* GRS (지역 중복 저장소): 지역 가동 중단 으로부터 보호 하는 지역 간 중복성 데이터는 주 지역에서 동기적으로 세 번 복사 된 다음, 보조 지역에 비동기적으로 복사 됩니다. 보조 지역의 데이터에 대 한 읽기 액세스의 경우 읽기 액세스 지역 중복 저장소 (RA-GRS)를 사용 하도록 설정 합니다.
* GZRS (지역 중복 저장소) (미리 보기): 고가용성 및 최대 내구성이 모두 필요한 시나리오에 대 한 중복성입니다. 데이터는 주 지역에 있는 세 개의 Azure 가용성 영역에서 동기적으로 복사 된 다음, 보조 지역에 비동기적으로 복사 됩니다. 보조 지역의 데이터에 대 한 읽기 액세스의 경우 읽기 액세스 지리적 영역 중복 저장소 (RA-GZRS)를 사용 하도록 설정 합니다.

Azure Storage의 중복성 옵션에 대 한 자세한 내용은 [Azure Storage 중복성](../articles/storage/common/storage-redundancy.md)(영문)을 참조 하세요.
