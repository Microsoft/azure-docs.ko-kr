---
title: Microsoft Azure StorSimple Manager 가상 배열 관리 | Microsoft Docs
description: Azure Portal에서 StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 온-프레미스 가상 배열을 관리하는 방법을 알아봅니다.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: bb6bb491ca71e5ced5aecc8137e9e1cbd950e80b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123808"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 가상 배열 관리
![설정 프로세스 흐름](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>개요
이 문서에서는 연결 방법, 사용 가능한 여러 옵션을 포함한 StorSimple 디바이스 관리자 서비스 인터페이스를 설명하고 이 UI를 통해 수행될 수 있는 특정 워크로드에 대한 링크를 제공합니다.

이 문서를 읽은 후 다음 방법에 대해 알 수 있습니다.

* StorSimple 디바이스 관리자 서비스에 연결
* StorSimple 디바이스 관리자 UI로 이동
* StorSimple 디바이스 관리자 서비스를 통한 StorSimple 가상 배열 관리

> [!NOTE]
> StorSimple 8000 시리즈 디바이스에 사용 가능한 관리 옵션을 보려면 [StorSimple 관리자 서비스를 사용하여 StorSimple 디바이스 관리](storsimple-manager-service-administration.md)로 이동하세요.
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>StorSimple 디바이스 관리자 서비스에 연결
StorSimple 디바이스 관리자 서비스는 Microsoft Azure에서 실행되며 여러 StorSimple 가상 배열에 연결됩니다. 이러한 디바이스를 관리하는 브라우저에서 실행되는 중앙 Microsoft Azure Portal을 사용합니다. StorSimple 디바이스 관리자 서비스에 연결하려면 다음을 수행합니다.

#### <a name="to-connect-to-the-service"></a>해당 서비스에 연결하려면
1. [https://ms.portal.azure.com](https://ms.portal.azure.com)로 이동합니다.
2. Microsoft 계정 자격 증명을 사용하여 Microsoft Azure Portal(해당 창의 상단 오른쪽에 있는)로 로그온합니다.
3. 지정된 구독에서 모든 디바이스 관리자를 보려면 StorSimple 디바이스 관리자에서 찾아보기 --&gt; '필터'로 이동합니다.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>StorSimple 디바이스 관리자 서비스를 사용하여 관리 작업 수행
다음 표에서 모든 일반 관리 작업 및 StorSimple 디바이스 관리자 서비스 요약 블레이드 내에서 수행할 수 있는 복잡한 워크플로의 요약을 보여줍니다. 이러한 작업은 시작되는 블레이드 페이지에 따라 구성됩니다.

각 워크플로에 대한 자세한 내용은 표에서 적절한 절차를 클릭합니다.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple 디바이스 관리자 워크플로
| 수행하려는 작업 ... | 이 절차 사용 |
| --- | --- |
| 서비스 만들기</br>서비스 삭제</br>서비스 등록 키 가져오기</br>서비스 등록 키 생성 |[StorSimple 디바이스 관리자 서비스 배포](storsimple-virtual-array-manage-service.md) |
| 작업 로그 보기 |[StorSimple 서비스 요약 사용](storsimple-virtual-array-service-summary.md) |
| 가상 배열 비활성화</br>가상 배열 삭제 |[가상 배열 비활성화 또는 삭제](storsimple-virtual-array-deactivate-and-delete-device.md) |
| 재해 복구 및 디바이스 장애 조치(Failover)</br>장애 조치 필수 구성 요소</br>비즈니스 연속성 재해 복구(BCDR)</br>재해 복구 중 오류 |[StorSimple 가상 배열에 대한 재해 복구 및 디바이스 장애 조치(failover)](storsimple-virtual-array-failover-dr.md) |
| 공유 및 볼륨 백업</br>수동 백업 수행</br>백업 일정 변경</br>기존 백업 확인 |[StorSimple 가상 배열 백업](storsimple-virtual-array-backup.md) |
| 백업 세트에서 공유 복제</br>백업 세트에서 볼륨 복제</br>항목 수준 복구(파일 서버에만 해당) |[StorSimple 가상 배열의 백업에서 복제](storsimple-virtual-array-clone.md) |
| Storage 계정 정보</br>저장소 계정 추가</br>저장소 계정 편집</br>저장소 계정 삭제 |[StorSimple 가상 배열에 대한 저장소 계정 관리](storsimple-virtual-array-manage-storage-accounts.md) |
| 액세스 제어 레코드 정보</br>액세스 제어 레코드 추가 또는 수정 </br>액세스 제어 레코드 삭제 |[StorSimple 가상 배열에 대한 액세스 제어 레코드 관리](storsimple-virtual-array-manage-acrs.md) |
| 작업 세부 정보 보기 |[StorSimple 가상 배열 작업 관리](storsimple-virtual-array-manage-jobs.md) |
| 경고 설정 구성</br>경고 알림 받기</br>경고 관리</br>경고 검토 |[StorSimple 가상 배열에 대한 경고 보기 및 관리](storsimple-virtual-array-manage-alerts.md) |
| 디바이스 관리자 암호 수정 |[StorSimple 가상 배열 디바이스 관리자 암호 변경](storsimple-virtual-array-change-device-admin-password.md) |
| 소프트웨어 업데이트 설치 |[가상 배열 업데이트](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> 다음과 같은 작업에 대해 [로컬 웹 UI](storsimple-ova-web-ui-admin.md) 를 사용해야 합니다.
> 
> * [서비스 데이터 암호화 키 검색](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [지원 패키지 만들기](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [가상 배열 중지 및 다시 시작](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>다음 단계
웹 UI 및 사용 방법에 대한 자세한 내용은 [StorSimple 웹 UI를 사용하여 StorSimple 가상 배열 관리](storsimple-ova-web-ui-admin.md)를 참조하세요.

