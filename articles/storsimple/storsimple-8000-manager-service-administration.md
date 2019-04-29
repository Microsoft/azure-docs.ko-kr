---
title: StorSimple 디바이스 관리자 서비스 관리 | Microsoft Docs
description: Azure Portal에서 StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스를 관리하는 방법을 알아봅니다.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: alkohli
ms.openlocfilehash: b5490c4e79ee1458b498f539c0db2cc189fce7f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723309"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스 관리

## <a name="overview"></a>개요

이 문서에서는 연결 방법, 사용 가능한 여러 옵션 및 이 UI를 통해 수행될 수 있는 특정 워크로드에 대한 링크를 포함한 StorSimple 디바이스 관리자 서비스 인터페이스를 설명합니다. 이 지침은 StorSimple 실제 디바이스 및 클라우드 어플라이언스, 두 작업 모두에 적용됩니다.

이 문서를 읽은 후 다음에 대해 알 수 있습니다.

* StorSimple 디바이스 관리자 서비스에 연결
* StorSimple 디바이스 관리자 서비스를 통한 StorSimple 디바이스 관리

## <a name="connect-to-storsimple-device-manager-service"></a>StorSimple 디바이스 관리자 서비스에 연결

StorSimple Device Manager 서비스는 Microsoft Azure에서 실행되며 여러 StorSimple 디바이스에 연결됩니다. 이러한 디바이스를 관리하는 브라우저에서 실행되는 중앙 Microsoft Azure Portal을 사용합니다. StorSimple 디바이스 관리자 서비스에 연결하려면 다음을 수행합니다.

#### <a name="to-connect-to-the-service"></a>해당 서비스에 연결하려면
1. [https://portal.azure.com/](https://portal.azure.com/)으로 이동합니다.
2. Microsoft 계정 자격 증명을 사용하여 Microsoft Azure Portal(해당 창의 상단 오른쪽에 있는)로 로그온합니다.
3. StorSimple 디바이스 관리자 서비스에 액세스하려면 왼쪽 탐색 창으로 아래로 스크롤합니다.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>StorSimple 디바이스 관리자 서비스를 사용한 StorSimple 디바이스 관리

다음 표에서 모든 일반 관리 작업 및 StorSimple 디바이스 관리자 서비스 UI 내에서 수행할 수 있는 복잡한 워크플로의 요약을 보여줍니다. 이러한 작업은 시작되는 UI 블레이드 페이지에 따라 구성됩니다.

각 워크플로에 대한 자세한 내용은 표에서 적절한 절차를 클릭합니다.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple 디바이스 관리자 워크플로

| 수행하려는 작업 ... | 이 절차를 사용합니다. |
| --- | --- |
| 서비스 만들기</br>서비스 삭제</br>서비스 등록 키 가져오기</br>서비스 등록 키 다시 생성 |[StorSimple 디바이스 관리자 서비스 배포](storsimple-8000-manage-service.md) |
| 작업 로그 보기 |[StorSimple 디바이스 관리자 서비스 요약 사용](storsimple-8000-service-dashboard.md) |
| 서비스 데이터 암호화 키 변경</br>작업 로그 보기 |[StorSimple 디바이스 관리자 서비스 대시보드 사용](storsimple-8000-service-dashboard.md) |
| 디바이스 비활성화</br>디바이스 삭제 |[디바이스 비활성화 또는 디바이스 삭제](storsimple-8000-deactivate-and-delete-device.md) |
| 재해 복구 및 디바이스 장애 조치(Failover)</br>실제 디바이스에 장애 조치(Failover)</br>가상 디바이스에 대한 장애 조치</br>비즈니스 연속성 재해 복구(BCDR) |[StorSimple 디바이스에 대한 장애 조치 및 재해 복구](storsimple-8000-device-failover-disaster-recovery.md) |
| 볼륨에 대한 백업 목록</br>백업 세트를 선택합니다.</br>백업 세트 삭제 |[백업 관리](storsimple-8000-manage-backup-catalog.md) |
| 볼륨 복제 |[볼륨 복제](storsimple-8000-clone-volume-u2.md) |
| 백업 세트 복원 |[백업 세트 복원](storsimple-8000-restore-from-backup-set-u2.md) |
| Storage 계정 정보</br>저장소 계정 추가</br>저장소 계정 편집</br>저장소 계정 삭제</br>저장소 계정의 키 회전 |[저장소 계정 관리](storsimple-8000-manage-storage-accounts.md) |
| 대역폭 템플릿 정보</br>대역폭 템플릿 추가</br>대역폭 템플릿 편집</br>대역폭 템플릿 삭제</br>기본 대역폭 템플릿 사용</br>지정된 시간에 시작되는 하루 종일 대역폭 템플릿 만들기 |[대역폭 템플릿 관리](storsimple-8000-manage-bandwidth-templates.md) |
| 액세스 제어 레코드 정보</br>액세스 제어 레코드 만들기</br>액세스 제어 레코드 편집</br>액세스 제어 레코드 삭제 |[액세스 제어 레코드 관리](storsimple-8000-manage-acrs.md) |
| 작업 세부 정보 보기</br>작업 취소 |[작업 관리](storsimple-8000-manage-jobs-u2.md) |
| 경고 알림 받기</br>경고 관리</br>경고 검토 |[StorSimple 경고 보기 및 관리](storsimple-8000-manage-alerts.md) |
| 모니터링 차트 만들기 |[StorSimple 디바이스 모니터링](storsimple-monitor-device.md) |
| 볼륨 컨테이너 추가</br>볼륨 컨테이너 수정</br>볼륨 컨테이너 삭제 |[볼륨 컨테이너 관리](storsimple-8000-manage-volume-containers.md) |
| 볼륨 추가</br>볼륨 수정</br>볼륨을 오프라인으로 전환</br>볼륨 삭제</br>볼륨 모니터링 |[볼륨 관리](storsimple-8000-manage-volumes-u2.md) |
| 디바이스 설정 수정</br>시간 설정 수정</br>DNS.md 설정 수정</br>네트워크 인터페이스 구성 |[StorSimple 디바이스에 대한 디바이스 구성 수정](storsimple-8000-modify-device-config.md) |
| 웹 프록시 설정 보기 |[디바이스에 대한 웹 프록시 구성](storsimple-8000-configure-web-proxy.md) |
| 디바이스 관리자 암호 수정</br>StorSimple 스냅숏 관리자 암호 수정 |[StorSimple 암호 변경](storsimple-8000-change-passwords.md) |
| 원격 관리 구성 |[StorSimple 디바이스에 원격으로 연결](storsimple-8000-remote-connect.md) |
| 경고 설정 구성 |[StorSimple 경고 보기 및 관리](storsimple-8000-manage-alerts.md) |
| StorSimple 디바이스에 대한 CHAP 구성 |[StorSimple 디바이스에 대한 CHAP 구성](storsimple-configure-chap.md) |
| 백업 정책 추가</br>일정 추가 또는 수정</br>백업 정책 삭제</br>수동 백업 수행</br>여러 볼륨과 일정의 사용자 지정 백업 정책 만들기 |[백업 정책 관리](storsimple-8000-manage-backup-policies-u2.md) |
| 디바이스 컨트롤러 중지</br>디바이스 컨트롤러 다시 시작</br>디바이스 컨트롤러 종료</br>디바이스를 공장 기본값으로 다시 설정</br>(위의 내용은 온-프레미스 디바이스만 해당) |[StorSimple 디바이스 컨트롤러 관리](storsimple-8000-manage-device-controller.md) |
| StorSimple 하드웨어 구성 요소</br>하드웨어 상태 모니터링</br>(위의 내용은 온-프레미스 디바이스만 해당) |[하드웨어 구성 요소 모니터링](storsimple-8000-monitor-hardware-status.md) |
| 지원 패키지 만들기 |[지원 패키지 만들기 및 관리](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| 소프트웨어 업데이트 설치 |[디바이스 업데이트](storsimple-update-device.md) |

## <a name="next-steps"></a>다음 단계

StorSimple 디바이스의 일상적인 작업 또는 해당 하드웨어 구성 요소 사용 시 발생하는 모든 문제는 다음을 참조하세요.

* [진단 도구를 사용하여 문제 해결](storsimple-8000-diagnostics.md)
* [StorSimple 모니터링 표시기 LED 사용](storsimple-monitoring-indicators.md)

문제를 해결할 수 없고 서비스 요청을 만드는 데 필요한 경우 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)를 참조합니다.

