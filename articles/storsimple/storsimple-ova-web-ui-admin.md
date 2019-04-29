---
title: StorSimple 가상 배열 웹 UI 관리 | Microsoft Docs
description: StorSimple Virtual Array 웹 UI를 통해 기본적인 디바이스 관리 작업을 수행하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 92671206a4171ca838423f55b526191ef30e5c35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630465"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>웹 UI를 사용하여 StorSimple 가상 배열 관리
![설정 프로세스 흐름](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>개요
이 문서의 자습서는 2016년 3월 일반 GA(공급) 버전을 실행하는 Microsoft Azure StorSimple Virtual Array(StorSimple 온-프레미스 가상 디바이스라고도 함)에 적용됩니다. 이 문서는 StorSimple 가상 배열에서 수행할 수 있는 복잡한 워크플로 및 관리 작업 중 일부를 설명합니다. StorSimple Manager 서비스 UI(포털 UI라고 함) 및 디바이스에 대한 로컬 웹을 사용하여 StorSimple Virtual Array를 관리할 수 있습니다. 이 문서는 웹 UI를 사용하여 수행할 수 있는 작업에 중점을 둡니다.

이 문서에는 다음 자습서가 포함되어 있습니다.

* 서비스 데이터 암호화 키 가져오기
* 웹 UI 설정 오류 해결
* 로그 패키지 생성
* 디바이스 종료 또는 다시 시작

## <a name="get-the-service-data-encryption-key"></a>서비스 데이터 암호화 키 가져오기
서비스 데이터 암호화 키는 StorSimple Manager 서비스에 첫 번째 디바이스를 등록할 때 생성됩니다. 이 키는 StorSimple Manager 서비스에 추가 디바이스를 등록하기 위한 서비스 등록 키에 필요합니다.

서비스 데이터 암호화 키의 위치를 잊어버려서 키를 찾아야 하는 경우에는 서비스에 등록된 디바이스의 로컬 웹 UI에서 다음 단계를 수행합니다.

#### <a name="to-get-the-service-data-encryption-key"></a>서비스 데이터 암호화 키를 가져오려면
1. 로컬 웹 UI에 연결합니다. **구성** > **클라우드 설정**으로 이동합니다.
2. 페이지 아래쪽에서 **서비스 데이터 암호화 키 가져오기**를 클릭합니다. 키가 표시됩니다. 이 키를 복사하여 저장합니다.
   
    ![서비스 데이터 암호화 키 가져오기 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>웹 UI 설정 오류 해결
로컬 웹 UI를 통해 디바이스를 구성하는 경우에 오류가 발생할 수 있습니다. 오류를 진단하고 해결하기 위해 진단 테스트를 실행합니다.

#### <a name="to-run-the-diagnostic-tests"></a>진단 테스트를 실행하려면
1. 로컬 웹 UI에서 **문제 해결** > **진단 테스트**로 이동합니다.
   
    ![진단 실행 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. 페이지 아래쪽에서 **진단 테스트 실행**을 클릭합니다. 네트워크, 디바이스, 웹 프록시, 시간 또는 클라우드 설정에서 문제가 될만한 내용을 진단하는 테스트가 시작됩니다. 디바이스에서 테스트가 실행 중이라는 메시지가 표시됩니다.
3. 테스트가 완료된 후 결과가 표시됩니다. 다음 예제는 진단 테스트의 결과를 보여줍니다. 웹 프록시 설정이 디바이스에 구성되어 있지 않기 때문에 웹 프록시 테스트가 실행되지 않았습니다. 네트워크 설정, DNS 서버, 시간 설정에 대한 다른 모든 테스트는 성공적으로 수행되었습니다.
   
    ![진단 실행 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>로그 패키지 생성
로그 패키지는 Microsoft 지원에서 디바이스 문제를 해결하는 데 도움을 줄 수 있는 관련된 로그로 구성됩니다. 이 릴리스에서 로그 패키지는 로컬 웹 UI를 통해 생성될 수 있습니다.

#### <a name="to-generate-the-log-package"></a>로그 패키지를 생성하려면
1. 로컬 웹 UI에서 **문제 해결** > **시스템 로그**로 이동합니다.
   
    ![로그 패키지 생성 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. 페이지 아래쪽에서 **로그 패키지 만들기**를 클릭합니다. 시스템 로그 패키지가 만들어집니다. 이 작업에 몇 분 정도가 소요됩니다.
   
    ![로그 패키지 생성 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    패키지가 성공적으로 만들어지면 이를 알리는 메시지가 표시되고, 패키지가 만들어진 날짜와 시간을 나타내도록 페이지가 업데이트됩니다.
   
    ![로그 패키지 생성 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. **로그 패키지 다운로드**를 클릭합니다. 압축된 패키지가 시스템에 다운로드됩니다.
   
    ![로그 패키지 생성 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. 다운로드한 로그 패키지의 압축을 풀어서 시스템 로그 파일을 볼 수 있습니다.

## <a name="shut-down-and-restart-your-device"></a>디바이스 종료 및 다시 시작
로컬 웹 UI를 사용하여 가상 디바이스를 종료하거나 다시 시작할 수 있습니다. 다시 시작하기 전에 호스트에서 볼륨 또는 공유를 오프라인으로 전환한 후 디바이스를 다시 시작하는 것이 좋습니다. 이렇게 하면 데이터 손상 가능성이 최소화됩니다. 

#### <a name="to-shut-down-your-virtual-device"></a>가상 디바이스를 종료하려면
1. 로컬 웹 UI에서 **유지 관리** > **전원 설정**으로 이동합니다.
2. 페이지 아래쪽에서 **종료**를 클릭합니다.
   
    ![디바이스 종료 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. 디바이스를 종료하면 진행 중인 IO가(있다면) 중단되고 가동 중지 시간이 발생한다는 내용의 경고가 표시됩니다. 필터 설정 후에 확인 아이콘 ![확인 아이콘](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![디바이스 종료 경고](./media/storsimple-ova-web-ui-admin/image37.png)
   
    종료가 시작되었다는 메시지가 표시됩니다.
   
    ![디바이스 종료 시작됨](./media/storsimple-ova-web-ui-admin/image38.png)
   
    디바이스가 종료됩니다. 디바이스를 시작하려면 Hyper-V 관리자를 통해 시작해야 합니다.

#### <a name="to-restart-your-virtual-device"></a>가상 디바이스를 다시 시작하려면
1. 로컬 웹 UI에서 **유지 관리** > **전원 설정**으로 이동합니다.
2. 페이지 아래쪽에서 **다시 시작**을 클릭합니다.
   
    ![디바이스 다시 시작](./media/storsimple-ova-web-ui-admin/image36.png)
3. 디바이스를 다시 시작하면 진행 중인 IO가(있다면) 중단되고 가동 중지 시간이 발생한다는 내용의 경고가 표시됩니다. 필터 설정 후에 확인 아이콘 ![확인 아이콘](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![다시 시작 경고](./media/storsimple-ova-web-ui-admin/image37.png)
   
    다시 시작이 시작되었다는 메시지가 표시됩니다.
   
    ![다시 시작 시작됨](./media/storsimple-ova-web-ui-admin/image39.png)
   
    다시 시작을 진행하는 동안 UI에 대한 연결이 끊어집니다. 주기적으로 UI를 새로 고쳐서 다시 시작을 모니터링할 수 있습니다. 또는 Hyper-V 관리자를 통해 디바이스 다시 시작 상태를 모니터링할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[StorSimple Manager 서비스를 사용하여 디바이스를 관리](storsimple-virtual-array-manager-service-administration.md)하는 방법을 알아봅니다.

