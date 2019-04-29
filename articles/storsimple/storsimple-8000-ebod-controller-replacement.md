---
title: StorSimple 8600 EBOD 컨트롤러 교체 | Microsoft Docs
description: StorSimple 8600 디바이스에서 하나 또는 두 개의 EBOD 컨트롤러를 모두 꺼내고 교체하는 방법을 설명합니다.
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
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: b05d1f36d1e74b3d915e216676859654fbcbacf3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578694"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>StorSimple 디바이스의 EBOD 컨트롤러 교체

## <a name="overview"></a>개요
이 자습서에서는 Microsoft Azure StorSimple 디바이스에서 결함이 있는 EBOD 컨트롤러 모듈을 교체하는 방법을 설명합니다. EBOD 컨트롤러 모듈을 교체하려면 다음을 수행해야 합니다.

* 결함이 있는 EBOD 컨트롤러 꺼내기
* 새 EBOD 컨트롤러 설치

시작하기 전에 다음 정보를 고려하세요.

* 사용하지 않은 모든 슬롯에 빈 EBOD 모듈을 삽입해야 합니다. 슬롯이 열려 있으면 엔클로저가 제대로 냉각되지 않습니다.
* EBOD 컨트롤러는 핫 스왑이 가능하며 꺼내거나 교체할 수 있습니다. 교체가 있을 때까지 오류가 발생한 모듈을 꺼내지 마세요. 교체 프로세스를 시작하는 경우 10분 내에 완료해야 합니다.

> [!IMPORTANT]
> StorSimple 구성 요소를 제거하거나 교체하기 전에 [안전성 아이콘 표시 규칙](storsimple-safety.md#safety-icon-conventions) 및 기타 [안전 주의 사항](storsimple-safety.md)을 검토해야 합니다.

## <a name="remove-an-ebod-controller"></a>EBOD 컨트롤러 꺼내기
StorSimple 디바이스에서 오류가 발생한 EBOD 컨트롤러 모듈을 교체하기 전에 다른 EBOD 컨트롤러 모듈이 활성화되어 실행되고 있는지 확인합니다. 다음 절차와 표에서는 EBOD 컨트롤러 모듈을 꺼내는 방법을 설명합니다.

#### <a name="to-remove-an-ebod-module"></a>EBOD 모듈을 꺼내려면
1. Azure Portal을 엽니다.
2. 디바이스로 이동한 후 **설정** > **하드웨어 상태**로 이동한 다음 활성 EBOD 컨트롤러 모듈의 LED 상태가 녹색이고 오류가 발생한 EBOD 컨트롤러 모듈의 LED가 빨간색인지 확인합니다.
3. 디바이스 뒷면에서 오류가 발생한 EBOD 컨트롤러 모듈을 찾습니다.
4. 시스템에서 EBOD 모듈을 꺼내기 전에 EBOD 컨트롤러 모듈을 컨트롤러에 연결하는 케이블을 뺍니다.
5. 컨트롤러에 연결된 EBOD 컨트롤러 모듈의 SAS 포트를 정확하게 적어둡니다. EBOD 모듈을 교체한 후 시스템을 이 구성으로 복원해야 합니다.
   
   > [!NOTE]
   > 일반적으로 포트 A이며, 다음 다이어그램에서는 **호스트 인** 으로 레이블이 붙어 있습니다.
   
    ![EBOD 컨트롤러의 백플레인](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **그림 1** EBOD 모듈 뒷면
   
   | 레이블 | 설명 |
   |:--- |:--- |
   | 1 |오류 LED |
   | 2 |전원 LED |
   | 3 |SAS 커넥터 |
   | 4 |SAS LED |
   | 5 |팩터리 전용 직렬 포트 |
   | 6 |포트 A(호스트 인) |
   | 7 |포트 B(호스트 아웃) |
   | 8 |포트 C(팩터리 전용) |

## <a name="install-a-new-ebod-controller"></a>새 EBOD 컨트롤러 설치
다음 절차와 표에서는 StorSimple 디바이스에 EBOD 컨트롤러 모듈을 설치하는 방법을 설명합니다.

#### <a name="to-install-an-ebod-controller"></a>EBOD 컨트롤러를 설치하려면
1. EBOD 디바이스에서 특히 인터페이스 커넥터에 손상된 부분이 있는지 확인합니다. 핀이 구부러진 경우 새 EBOD 컨트롤러를 설치하지 마세요.
2. 래치를 열린 위치에 놓고 래치가 걸릴 때까지 모듈을 엔클로저에 밀어넣습니다.
   
    ![EBOD 컨트롤러 설치](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **그림 2** EBOD 컨트롤러 모듈 설치
3. 래치를 닫습니다. 래치가 걸리면 딸깍하는 소리가 들립니다.
   
    ![EBOD 래치 해제](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **그림 3** EBOD 모듈 래치 닫기
4. 케이블을 다시 연결합니다. 교체 전에 있던 구성을 정확하게 사용합니다. 케이블을 연결하는 방법에 대한 자세한 내용은 다음 다이어그램과 표를 참조하세요.
   
    ![전원에 4U 디바이스를 케이블로 연결](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **그림 4**. 케이블 다시 연결
   
   | 레이블 | 설명 |
   |:--- |:--- |
   | 1 |기본 인클로저 |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |컨트롤러 0 |
   | 5 |컨트롤러 1 |
   | 6 |EBOD 컨트롤러 0 |
   | 7 |EBOD 컨트롤러 1 |
   | 8 |EBOD 인클로저 |
   | 9 |전력 분배 장치 |

## <a name="next-steps"></a>다음 단계
[StorSimple 하드웨어 구성 요소 교체](storsimple-8000-hardware-component-replacement.md)에 대해 자세히 알아봅니다.

