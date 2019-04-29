---
title: StorSimple 8000 시리즈 디바이스의 섀시 교체 | Microsoft Docs
description: StorSimple 기본 엔클로저 또는 EBOD 엔클로저에 섀시를 꺼내고 교체하는 방법을 설명합니다.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 7dfc39f4d08c8a49d1564a0a5bd7e3ef4156e3fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61312448"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>StorSimple 디바이스의 섀시 교체
## <a name="overview"></a>개요
이 자습서에서는 StorSimple 8000 시리즈 디바이스의 섀시를 꺼내고 교체하는 방법을 설명합니다. StorSimple 8100 모델은 단일 엔클로저 디바이스(섀시 1개)인 반면 8600은 이중 엔클로저 디바이스(섀시 2개)입니다. 8600 모델의 경우 디바이스에 오류가 발생할 수 있는 두 개의 섀시(기본 엔클로저용 섀시 또는 EBOD 엔클로저용 섀시)가 있습니다.

두 경우 모두 Microsoft에서 제공한 교체 섀시가 비게 됩니다. PCM(전원 및 냉각 모듈), 컨트롤러 모듈, SSD(반도체 디스크 드라이브), HDD(하드 디스크 드라이브) 또는 EBOD 모듈은 포함되지 않습니다.

> [!IMPORTANT]
> 섀시를 꺼내고 교체하기 전에 [StorSimple 하드웨어 구성 요소 교체](storsimple-8000-hardware-component-replacement.md)에서 안전 정보를 검토하세요.


## <a name="remove-the-chassis"></a>섀시 꺼내기
StorSimple 디바이스의 섀시를 꺼내려면 다음 단계를 따르세요.

#### <a name="to-remove-a-chassis"></a>섀시를 꺼내려면
1. StorSimple 디바이스가 종료되고 모든 전원에서 연결이 끊어졌는지 확인합니다.
2. 해당하는 경우 모든 네트워크 및 SAS 케이블을 뺍니다.
3. 랙에서 장치를 꺼냅니다.
4. 각 드라이브를 꺼내고 들어 있던 슬롯을 적어둡니다. 자세한 내용은 [디스크 드라이브 꺼내기](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive)를 참조하세요.
5. EBOD 엔클로저에서(이 섀시에서 오류가 발생한 경우) EBOD 컨트롤러 모듈을 꺼냅니다. 자세한 내용은 [EBOD 컨트롤러 꺼내기](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller)를 참조하세요.
   
    기본 엔클로저에서(이 섀시에서 오류가 발생한 경우) 컨트롤러를 꺼내고 들어 있던 슬롯을 적어둡니다. 자세한 내용은 [컨트롤러 꺼내기](storsimple-8000-controller-replacement.md#remove-a-controller)를 참조하세요.

## <a name="install-the-chassis"></a>섀시 설치
StorSimple 디바이스에 섀시를 설치하려면 다음 단계를 따르세요.

#### <a name="to-install-a-chassis"></a>섀시를 설치하려면
1. 섀시를 랙에 탑재합니다. 자세한 내용은 [StorSimple 8100 디바이스 랙 탑재](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) 또는 [StorSimple 8600 디바이스 랙 탑재](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device)를 참조하세요.
2. 섀시를 랙에 탑재한 후 이전에 설치된 곳과 동일한 위치에 컨트롤러 모듈을 설치합니다.
3. 이전에 설치된 곳과 동일한 위치 및 슬롯에 드라이브를 설치합니다.
   
   > [!NOTE]
   > SSD를 먼저 슬롯에 설치한 다음 HDD를 설치하는 것이 좋습니다.
  
4. 디바이스를 랙에 탑재하고 구성 요소를 설치한 후 디바이스를 해당 전원에 연결하고 디바이스를 켭니다. 자세한 내용은 [StorSimple 8100 디바이스 케이블 연결](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) 또는 [StorSimple 8600 디바이스 케이블 연결](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[StorSimple 하드웨어 구성 요소 교체](storsimple-8000-hardware-component-replacement.md)에 대해 자세히 알아봅니다.

