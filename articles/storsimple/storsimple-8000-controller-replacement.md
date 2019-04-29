---
title: StorSimple 8000 시리즈 디바이스 컨트롤러 교체 | Microsoft Docs
description: StorSimple 8000 시리즈 디바이스에서 하나 또는 두 개의 컨트롤러 모듈을 모두 꺼내고 교체하는 방법을 설명합니다.
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
ms.openlocfilehash: dd2f6fcc9b2f5d716566e91e89487969613d1005
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61482873"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>StorSimple 디바이스의 컨트롤러 모듈 교체
## <a name="overview"></a>개요
이 자습서에서는 StorSimple 디바이스에서 하나 또는 두 개의 컨트롤러 모듈을 모두 꺼내고 교체하는 방법을 설명합니다. 단일 및 이중 컨트롤러 교체 시나리오에 대한 기본 논리에 대해서도 설명합니다.

> [!NOTE]
> 컨트롤러 교체를 수행하기 전에 항상 컨트롤러 펌웨어를 최신 버전으로 업데이트하는 것이 좋습니다.
> 
> StorSimple 디바이스의 손상을 방지하려면 LED가 다음 중 하나로 표시될 때까지 컨트롤러를 꺼내지 마세요.
> 
> * 모든 표시등이 OFF입니다.
> * LED 3, ![녹색 확인 표시 아이콘](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), ![빨간색 십자 아이콘](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) 이 깜박이고 LED 0 및 LED 7이 **켜짐**입니다.


다음 표에서는 지원되는 컨트롤러 교체 시나리오를 보여 줍니다.

| 사례 | 교체 시나리오 | 해당 절차 |
|:--- |:--- |:--- |
| 1 |한 컨트롤러는 실패 상태이고 다른 컨트롤러는 정상 활성 상태입니다. |[단일 컨트롤러 교체를 지원하는 논리](#single-controller-replacement-logic) 및 [교체 단계](#single-controller-replacement-steps)를 설명하는 [단일 컨트롤러 교체](#replace-a-single-controller)입니다. |
| 2 |두 컨트롤러에서 모두 오류가 발생했으며 교체해야 합니다. 섀시, 디스크 및 디스크 엔클로저는 정상입니다. |[이중 컨트롤러 교체를 지원하는 논리](#dual-controller-replacement-logic) 및 [교체 단계](#dual-controller-replacement-steps)를 설명하는 [이중 컨트롤러 교체](#replace-both-controllers)입니다. |
| 3 |동일한 디바이스 또는 서로 다른 디바이스의 컨트롤러를 교환합니다. 섀시, 디스크 및 디스크 엔클로저는 정상입니다. |슬롯 불일치 경고 메시지가 표시됩니다. |
| 4 |한 컨트롤러는 없고 다른 컨트롤러에서는 오류가 발생합니다. |[이중 컨트롤러 교체를 지원하는 논리](#dual-controller-replacement-logic) 및 [교체 단계](#dual-controller-replacement-steps)를 설명하는 [이중 컨트롤러 교체](#replace-both-controllers)입니다. |
| 5 |하나 또는 두 개의 컨트롤러에서 모두 오류가 발생했습니다. 직렬 콘솔 또는 Windows PowerShell 원격을 통해 디바이스에 액세스할 수 없습니다. |수동 컨트롤러 교체 절차는 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하세요. |
| 6 |컨트롤러의 빌드 버전이 서로 다르며, 다음과 같은 이유 때문일 수 있습니다.<ul><li>컨트롤러에는 다양한 소프트웨어 버전이 있습니다.</li><li>컨트롤러에는 다양한 펌웨어 버전이 있습니다.</li></ul> |컨트롤러 소프트웨어 버전이 서로 다른 경우 교체 논리에서 이를 감지하고 교체 컨트롤러의 소프트웨어 버전을 업데이트합니다.<br><br>컨트롤러 펌웨어 버전이 서로 다르고 이전 펌웨어 버전을 자동으로 업그레이드할 수 **없는** 경우 Azure Portal에 경고 메시지가 표시됩니다. 업데이트를 검색하고 펌웨어 업데이트를 설치해야 합니다.</br></br>컨트롤러 펌웨어 버전이 서로 다르고 이전 펌웨어 버전을 자동으로 업그레이드할 수 있는 경우 컨트롤러 교체 논리에서 이를 감지하고 컨트롤러가 시작된 후 펌웨어가 자동으로 업데이트됩니다. |

오류가 발생한 경우 컨트롤러 모듈을 꺼내야 합니다. 하나 또는 두 개의 컨트롤러 모듈에서 모두 오류가 발생할 수 있으며, 단일 컨트롤러 교체 또는 이중 컨트롤러 교체가 요구될 수 있습니다. 교체 절차 및 지원 논리는 다음을 참조하세요.

* [단일 컨트롤러 교체](#replace-a-single-controller)
* [두 컨트롤러 모두 교체](#replace-both-controllers)
* [컨트롤러 꺼내기](#remove-a-controller)
* [컨트롤러 삽입](#insert-a-controller)
* [디바이스의 활성 컨트롤러 식별](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> 컨트롤러를 꺼내고 교체하기 전에 [StorSimple 하드웨어 구성 요소 교체](storsimple-8000-hardware-component-replacement.md)에서 안전 정보를 검토하세요.
> 
> 

## <a name="replace-a-single-controller"></a>단일 컨트롤러 교체
Microsoft Azure StorSimple 디바이스의 두 컨트롤러 중 하나에서 오류가 발생했거나 오작동하거나 없는 경우 단일 컨트롤러를 교체해야 합니다.

### <a name="single-controller-replacement-logic"></a>단일 컨트롤러 교체 논리
단일 컨트롤러 교체에서는 오류가 발생한 컨트롤러를 먼저 제거해야 합니다. (디바이스의 나머지 컨트롤러가 활성 컨트롤러가 됩니다.) 교체 컨트롤러를 삽입하면 다음 동작이 발생합니다.

1. 교체 컨트롤러가 즉시 StorSimple 디바이스와 통신을 시작합니다.
2. 활성 컨트롤러에 대한 VHD(가상 하드 디스크)의 스냅숏이 교체 컨트롤러에 복사됩니다.
3. 교체 컨트롤러가 이 VHD에서 시작될 때 대기 컨트롤러로 인식되도록 스냅숏이 수정됩니다.
4. 수정이 완료되면 교체 컨트롤러가 대기 컨트롤러로 시작됩니다.
5. 두 컨트롤러가 모두 실행되고 있으면 클러스터가 온라인 상태가 됩니다.

### <a name="single-controller-replacement-steps"></a>단일 컨트롤러 교체 단계
Microsoft Azure StorSimple 디바이스의 컨트롤러 중 하나에서 오류가 발생하는 경우 다음 단계를 따르세요. (다른 컨트롤러는 활성화되고 실행 중이어야 합니다. 두 컨트롤러에서 모두 오류가 발생하거나 오작동하는 경우 [이중 컨트롤러 교체 단계](#dual-controller-replacement-steps)로 이동합니다.)

> [!NOTE]
> 컨트롤러가 다시 시작되고 단일 컨트롤러 교체 절차에서 완전히 복구되는 데 30-45분이 걸릴 수 있습니다. 케이블 연결을 포함하여 전체 절차의 총 시간은 약 2시간입니다.


#### <a name="to-remove-a-single-failed-controller-module"></a>오류가 발생한 단일 컨트롤러 모듈을 꺼내려면
1. Azure Portal에서 StorSimple 디바이스 관리자 서비스로 이동하고 **디바이스**를 클릭한 다음 모니터링하려는 디바이스의 이름을 클릭합니다.
2. **모니터 > 하드웨어 상태**로 이동합니다. 컨트롤러 0 또는 컨트롤러 1의 상태가 오류를 나타내는 빨강이어야 합니다.
   
   > [!NOTE]
   > 단일 컨트롤러 교체에서 오류가 발생한 컨트롤러는 항상 대기 컨트롤러입니다.
   
3. 그림 1과 다음 표를 사용하여 오류가 발생한 컨트롤러 모듈을 찾습니다.
   
    ![디바이스 기본 엔클로저 모듈의 백플레인](./media/storsimple-controller-replacement/IC740994.png)
   
    **그림 1** StorSimple 디바이스 뒷면
   
   | 레이블 | 설명 |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |컨트롤러 0 |
   | 4 |컨트롤러 1 |
4. 오류가 발생한 컨트롤러의 데이터 포트에서 연결된 네트워크 케이블을 모두 제거합니다. 8600 모델을 사용하는 경우 컨트롤러를 EBOD 컨트롤러에 연결하는 SAS 케이블를 제거합니다.
5. [컨트롤러 제거](#remove-a-controller)의 단계에 따라 오류가 발생한 컨트롤러를 제거합니다.
6. 오류가 발생한 컨트롤러를 제거한 슬롯과 동일한 슬롯에 팩터리 교체를 설치합니다. 이렇게 하면 단일 컨트롤러 교체 논리가 트리거됩니다. 자세한 내용은 [단일 컨트롤러 교체 논리](#single-controller-replacement-logic)를 참조하세요.
7. 단일 컨트롤러 교체 논리가 백그라운드에서 진행되는 동안 케이블을 다시 연결합니다. 교체 전에 연결된 것과 동일한 방식으로 모든 케이블을 연결해야 합니다.
8. 컨트롤러가 다시 시작된 후 Azure Portal에서 **컨트롤러 상태** 및 **클러스터 상태**를 검사하여 컨트롤러가 다시 정상 상태로 돌아갔으며 대기 모드에 있는지 확인합니다.

> [!NOTE]
> 직렬 콘솔을 통해 디바이스를 모니터링하는 경우 컨트롤러가 교체 절차에서 복구되는 동안 여러 번 다시 시작되는 것을 확인할 수 있습니다. 직렬 콘솔 메뉴가 표시되면 교체가 완료된 것입니다. 컨트롤러 교체를 시작한 후 2시간 내에 메뉴가 표시되지 않는 경우 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하세요.
>
> 업데이트 4부터는 디바이스의 Windows PowerShell 인터페이스에서 cmdlet `Get-HCSControllerReplacementStatus`를 사용하여 컨트롤러 대체 프로세스의 상태를 모니터링할 수도 있습니다.
> 

## <a name="replace-both-controllers"></a>두 컨트롤러 모두 교체
Microsoft Azure StorSimple 디바이스의 두 컨트롤러에서 모두 오류가 발생했거나 오작동하거나 없는 경우 두 컨트롤러를 모두 교체해야 합니다. 

### <a name="dual-controller-replacement-logic"></a>이중 컨트롤러 교체 논리
이중 컨트롤러 교체에서는 먼저 오류가 발생한 두 컨트롤러를 모두 제거하고 교체를 삽입합니다. 두 교체 컨트롤러를 삽입하면 다음 동작이 발생합니다.

1. 슬롯 0의 교체 컨트롤러가 다음을 확인합니다.
   
   1. 최신 버전의 펌웨어 및 소프트웨어를 사용하고 있나요?
   2. 클러스터의 일부인가요?
   3. 피어 컨트롤러가 실행 중이며 클러스터되었나요?
      
      이러한 조건이 모두 true가 아니면 컨트롤러가 최근 매일 백업을 찾습니다(S 드라이브의 **nonDOMstorage**에 있음). 컨트롤러가 백업에서 VHD의 최신 스냅숏을 복사합니다.
2. 슬롯 0의 컨트롤러가 스냅숏을 사용하여 자체 이미지를 작성합니다.
3. 한편, 슬롯 1의 컨트롤러는 컨트롤러 0이 이미징을 완료하고 시작될 때까지 기다립니다.
4. 컨트롤러 0이 시작된 후 컨트롤러 1이 컨트롤러 0에서 만든 클러스터를 검색하며, 이로 인해 단일 컨트롤러 교체 논리가 트리거됩니다. 자세한 내용은 [단일 컨트롤러 교체 논리](#single-controller-replacement-logic)를 참조하세요.
5. 그 후에는 두 컨트롤러가 모두 실행되고 클러스터가 온라인 상태가 됩니다.

> [!IMPORTANT]
> 이중 컨트롤러 교체 다음에는 StorSimple 디바이스가 구성된 후 반드시 디바이스를 수동으로 백업해야 합니다. 24시간이 경과할 때까지 일별 디바이스 구성 백업이 트리거되지 않습니다. [Microsoft 지원](storsimple-8000-contact-microsoft-support.md)의 도움을 받아 디바이스를 수동으로 백업하세요.


### <a name="dual-controller-replacement-steps"></a>이중 컨트롤러 교체 단계
이 워크플로는 Microsoft Azure StorSimple 디바이스의 두 컨트롤러에서 모두 오류가 발생한 경우에 필요합니다. 이러한 상황은 냉각 시스템 작동이 중지되어 짧은 기간 내에 두 컨트롤러에서 모두 오류가 발생하는 데이터 센터에서 발생할 수 있습니다. StorSimple 디바이스가 켜져 있는지 여부 및 8600 또는 8100 모델을 사용하는지에 따라 다른 일련의 단계가 필요합니다.

> [!IMPORTANT]
> 컨트롤러가 다시 시작되고 이중 컨트롤러 교체 절차에서 완전히 복구되는 데 45분-1시간이 걸릴 수 있습니다. 케이블 연결을 포함하여 전체 절차의 총 시간은 약 2.5시간입니다.

#### <a name="to-replace-both-controller-modules"></a>두 컨트롤러 모듈을 모두 교체하려면
1. 디바이스가 꺼져 있는 경우 이 단계를 건너뛰고 다음 단계로 진행합니다. 디바이스가 켜져 있는 경우 디바이스를 끕니다.
   
   1. 8600 모델을 사용하는 경우 기본 엔클로저를 먼저 끈 다음 EBOD 엔클로저를 끕니다.
   2. 디바이스가 완전히 종료될 때까지 기다립니다. 디바이스 뒷면의 모든 LED가 꺼집니다.
2. 데이터 포트에 연결된 모든 네트워크 케이블을 제거합니다. 8600 모델을 사용하는 경우 기본 엔클로저를 EBOD 엔클로저에 연결하는 SAS 케이블도 제거합니다.
3. StorSimple 디바이스에서 두 컨트롤러를 모두 제거합니다. 자세한 내용은 [컨트롤러 제거](#remove-a-controller)를 참조하세요.
4. 컨트롤러 0에 대한 팩터리 교체를 먼저 삽입하고 컨트롤러 1을 삽입합니다. 자세한 내용은 [컨트롤러 삽입](#insert-a-controller)을 참조하세요. 이렇게 하면 이중 컨트롤러 교체 논리가 트리거됩니다. 자세한 내용은 [이중 컨트롤러 교체 논리](#dual-controller-replacement-logic)를 참조하세요.
5. 컨트롤러 교체 논리가 백그라운드에서 진행되는 동안 케이블을 다시 연결합니다. 교체 전에 연결된 것과 동일한 방식으로 모든 케이블을 연결해야 합니다. [StorSimple 8100 디바이스 설치](storsimple-8100-hardware-installation.md) 또는 [StorSimple 8600 디바이스 설치](storsimple-8600-hardware-installation.md)의 디바이스를 케이블로 연결 섹션에서 모델에 대한 자세한 지침을 참조하세요.
6. StorSimple 디바이스를 켭니다. 8600 모델을 사용하는 경우:
   
   1. EBOD 엔클로저가 먼저 켜지는지 확인합니다.
   2. EBOD 엔클로저가 실행될 때까지 기다립니다.
   3. 기본 엔클로저를 켭니다.
   4. 첫 번째 컨트롤러가 다시 시작되고 정상 상태이면 시스템이 실행됩니다.
      
      > [!NOTE]
      > 직렬 콘솔을 통해 디바이스를 모니터링하는 경우 컨트롤러가 교체 절차에서 복구되는 동안 여러 번 다시 시작되는 것을 확인할 수 있습니다. 직렬 콘솔 메뉴가 표시되면 교체가 완료된 것입니다. 컨트롤러 교체를 시작한 후 2.5시간 내에 메뉴가 표시되지 않는 경우 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하세요.
     
## <a name="remove-a-controller"></a>컨트롤러 꺼내기
StorSimple 디바이스에서 결함이 있는 컨트롤러 모듈을 꺼내려면 다음 절차를 따르세요.

> [!NOTE]
> 다음 그림은 컨트롤러 0에 해당합니다. 컨트롤러 1의 경우 반대가 됩니다.


#### <a name="to-remove-a-controller-module"></a>컨트롤러 모듈을 꺼내려면
1. 엄지와 집게 손가락으로 모듈 래치를 잡습니다.
2. 엄지와 집게 손가락을 부드럽게 눌러 컨트롤러 래치를 해제합니다.
   
    ![컨트롤러 래치 해제](./media/storsimple-controller-replacement/IC741047.png)
   
    **그림 2** 컨트롤러 래치 해제
3. 래치를 핸들로 사용하여 컨트롤러를 섀시 밖으로 밉니다.
   
    ![섀시 밖으로 컨트롤러 밀기](./media/storsimple-controller-replacement/IC741048.png)
   
    **그림 3** 섀시 밖으로 컨트롤러 밀기

## <a name="insert-a-controller"></a>컨트롤러 삽입
StorSimple 디바이스에서 결함이 있는 모듈을 꺼낸 후 팩터리 제공 컨트롤러 모듈을 설치하려면 다음 절차를 따르세요.

#### <a name="to-install-a-controller-module"></a>컨트롤러 모듈을 설치하려면
1. 인터페이스 커넥터에 손상된 부분이 있는지 확인합니다. 커넥터 핀이 손상되었거나 구부러진 경우 모듈을 설치하지 마세요.
2. 래치가 완전히 해제된 동안 컨트롤러 모듈을 섀시에 밀어넣습니다.
   
    ![섀시 안으로 컨트롤러 밀기](./media/storsimple-controller-replacement/IC741053.png)
   
    **그림 4** 섀시에 컨트롤러 밀어넣기
3. 컨트롤러 모듈을 삽입한 후 컨트롤러 모듈을 섀시 안으로 계속 누르면서 래치를 닫습니다. 래치가 걸려 컨트롤러를 제자리로 안내합니다.
   
    ![컨트롤러 래치 닫기](./media/storsimple-controller-replacement/IC741054.png)
   
    **그림 5** 컨트롤러 래치 닫기
4. 래치가 제자리에 놓이면 작업을 완료했습니다. 이제 **OK** LED가 켜집니다.
   
   > [!NOTE]
   > 컨트롤러 및 LED가 활성화되는 데 최대 5분 정도 걸릴 수 있습니다.
  
5. 교체에 성공했는지 확인하려면 Azure Portal에서 **모니터** > **하드웨어 상태**로 이동한 다음 컨트롤러 0과 컨트롤러 1이 모두 정상(녹색 상태)인지 확인합니다.

## <a name="identify-the-active-controller-on-your-device"></a>디바이스의 활성 컨트롤러 식별
처음 디바이스를 등록하거나 컨트롤러를 교체하는 경우와 같이 StorSimple 디바이스에서 활성 컨트롤러를 찾아야 하는 경우가 많습니다. 활성 컨트롤러는 모든 디스크 펌웨어 및 네트워킹 작업을 처리합니다. 다음 방법 중 하나를 사용하여 활성 컨트롤러를 식별할 수 있습니다.

* [Azure Portal을 사용하여 활성 컨트롤러 식별](#use-the-azure-portal-to-identify-the-active-controller)
* [StorSimple용 Windows PowerShell을 사용하여 활성 컨트롤러 식별](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [물리적 디바이스를 검사하여 활성 컨트롤러 식별](#check-the-physical-device-to-identify-the-active-controller)

아래에서는 이러한 각 절차에 대해 설명합니다.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Azure Portal을 사용하여 활성 컨트롤러 식별
Azure Portal에서 디바이스로 이동하고 **모니터** > **하드웨어 상태**로 이동한 후 **컨트롤러** 섹션으로 스크롤합니다. 여기서 활성 컨트롤러를 확인할 수 있습니다.

![Azure Portal에서 활성 컨트롤러 식별](./media/storsimple-controller-replacement/IC752072.png)

**그림 6** 활성 컨트롤러를 보여 주는 Azure Portal

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>StorSimple용 Windows PowerShell을 사용하여 활성 컨트롤러 식별
직렬 콘솔을 통해 디바이스에 액세스하는 경우 배너 메시지가 표시됩니다. 배너 메시지에는 모델, 이름, 설치된 소프트웨어 버전 및 액세스 중인 컨트롤러의 상태와 같은 기본 디바이스 정보가 포함되어 있습니다. 다음 그림은 배너 메시지의 예를 보여 줍니다.

![직렬 배너 메시지](./media/storsimple-controller-replacement/IC741098.png)

**그림 7** 컨트롤러 0을 활성으로 표시하는 배너 메시지

배너 메시지를 사용하여 연결된 컨트롤러가 활성 또는 수동인지 확인할 수 있습니다.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>물리적 디바이스를 검사하여 활성 컨트롤러 식별
디바이스의 활성 컨트롤러를 식별하려면 기본 엔클로저 뒷면에서 DATA 5 포트 위의 파란색 LED를 찾습니다.

이 LED가 깜박이면 컨트롤러가 활성 상태이며 다른 컨트롤러는 대기 모드에 있습니다. 다음 다이어그램과 표를 보조 도구로 사용합니다.

![데이터 포트가 있는 디바이스 기본 인클로저 백플레인](./media/storsimple-controller-replacement/IC741055.png)

**그림 8** 데이터 포트와 모니터링 LED가 있는 기본 엔클로저 뒷면

| 레이블 | 설명 |
|:--- |:--- |
| 1-6 |DATA 0 - 5 네트워크 포트 |
| 7 |파란색 LED |

## <a name="next-steps"></a>다음 단계
[StorSimple 하드웨어 구성 요소 교체](storsimple-8000-hardware-component-replacement.md)에 대해 자세히 알아봅니다.

