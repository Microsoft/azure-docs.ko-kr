---
title: Azure Disk Storage에서 애플리케이션 벤치마킹 - 관리형 디스크
description: Azure에서 애플리케이션을 벤치마킹하는 프로세스를 알아봅니다.
services: virtual-machines-windows,storage
author: rockboyfor
ms.author: v-yeche
origin.date: 01/11/2019
ms.date: 04/01/2019
ms.topic: article
ms.service: virtual-machines-windows
ms.tgt_pltfrm: windows
ms.subservice: disks
ms.openlocfilehash: 8db1fb3c9b3ed551cd668cf14105eb8bfb486251
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679781"
---
# <a name="benchmarking-a-disk"></a>디스크 벤치마킹

벤치마킹은 애플리케이션에서 다양한 작업을 시뮬레이션하고 각 작업에 대한 애플리케이션 성능을 측정하는 과정입니다. [고성능을 위한 디자인 문서](premium-storage-performance.md)에 설명된 단계를 사용하여 애플리케이션 성능 요구 사항을 수집했습니다. 애플리케이션을 호스팅하는 VM에서 벤치마킹 도구를 실행하여 Premium Storage를 통해 애플리케이션이 얻을 수 있는 성능 수준을 확인할 수 있습니다. 이 문서에서는 Azure Premium Storage 디스크로 프로비저닝된 표준 DS14 VM의 벤치마킹 예제를 제공합니다.

Windows 및 Linux용으로 각각 일반 벤치마킹 도구 Iometer 및 FIO를 사용했습니다. 이러한 도구는 작업과 같은 프로덕션을 시뮬레이션하는 여러 스레드를 생성하고 시스템 성능을 측정합니다. 도구를 사용하여 일반적으로 애플리케이션에 대해 변경할 수 없는 블록 크기 및 큐 크기와 같은 매개 변수를 구성할 수도 있습니다. 애플리케이션 작업의 다양한 유형에 대해 프리미엄 디스크로 프로비전된 높은 확장성의 VM에 최대 성능을 구동하도록 유연성을 제공합니다. 각 벤치마킹 도구에 대한 자세한 내용은 [Iometer](http://www.iometer.org/) 및 [FIO](http://freecode.com/projects/fio)를 방문하세요.

아래 예제를 수행하려면 Standard DS14 VM을 만들고 VM에 11개의 Premium Storage 디스크를 연결합니다. 11개의 디스크는 “None”으로 호스트 캐싱을 사용하여 10개의 디스크를 구성하고 NoCacheWrites라는 볼륨으로 스트라이프합니다. 나머지 디스크에 “ReadOnly”로 호스트 캐싱을 구성하고 이 디스크를 사용하여 CacheReads라는 볼륨을 만듭니다. 이 설정을 사용하면 표준 DS14 VM의 최대 읽기 및 쓰기 성능을 확인할 수 있습니다. 프리미엄 SSD를 사용하여 DS14 VM을 만드는 방법에 대한 자세한 단계를 보려면 [고성능을 위한 디자인](premium-storage-performance.md)으로 이동하세요.

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>다음 단계

고성능을 위한 디자인 문서를 진행합니다. 이 문서에서는 프로토타입을 위한 기존 애플리케이션과 비슷한 검사 목록을 만듭니다. 벤치마킹 도구를 사용하여 작업을 시뮬레이션하고 프로토타입 애플리케이션의 성능을 측정할 수 있습니다. 이렇게 하면 애플리케이션 성능 요구 사항과 일치하거나 능가할 수 있는 디스크 제품을 확인할 수 있습니다. 그런 다음, 프로덕션 애플리케이션에 대해 동일한 지침을 구현할 수 있습니다.

> [!div class="nextstepaction"]
> [고성능을 위한 디자인](premium-storage-performance.md) 문서를 참조하세요.