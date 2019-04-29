---
title: StorSimple 디바이스 관리자 서비스 배포 | Microsoft Docs
description: Azure Portal에서 StorSimple 디바이스 관리자 서비스를 만들고 삭제하는 방법 및 서비스 등록 키를 관리하는 방법에 대해 설명합니다.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: alkohli
ms.openlocfilehash: 9f6e5b606caa661429a3c4d4a53e2021d57730aa
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116958"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>StorSimple 가상 배열에 StorSimple 디바이스 관리자 서비스 배포
## <a name="overview"></a>개요

StorSimple Device Manager 서비스는 Microsoft Azure에서 실행되며 여러 StorSimple 디바이스에 연결됩니다. 서비스를 만든 후에 브라우저에서 실행되는 Microsoft Azure Portal에서 이러한 디바이스를 관리하는 데 사용할 수 있습니다. 하나의 중앙 위치에서 StorSimple 디바이스 관리자 서비스에 연결된 모든 디바이스를 모니터링하여 관리 부담을 최소화할 수 있습니다.

StorSimple 디바이스 관리자 서비스와 관련된 일반적인 태스크는 다음과 같습니다.

* 서비스 만들기
* 서비스 삭제
* 서비스 등록 키 가져오기
* 서비스 등록 키 생성

이 자습서에서는 이러한 태스크를 수행하는 방법을 설명합니다. 이 문서에 포함된 정보는 StorSimple 가상 배열에만 적용됩니다. StorSimple 8000 시리즈에 대한 자세한 내용은 [StorSimple Manager 서비스 배포](storsimple-manage-service.md)를 참조하세요.

## <a name="create-a-service"></a>서비스 만들기

서비스를 만들려면:

* 엔터프라이즈 계약을 사용하여 구독
* 활성 Microsoft Azure 저장소 계정
* 액세스 관리에 사용되는 청구 정보

또한 서비스를 만들 때 저장소 계정을 생성하도록 선택할 수 있습니다.

하나의 서비스로 여러 디바이스를 관리할 수 있습니다. 하지만 하나의 디바이스는 여러 서비스로 확장할 수 없습니다. 대규모 엔터프라이즈는 서로 다른 구독, 조직 또는 배포 위치와 동작하는 여러 서비스 인스턴스를 가질 수 있습니다.

> [!NOTE]
> StorSimple 8000 시리즈 디바이스와 StorSimple 가상 배열을 관리하려면 별도의 StorSimple Device Manager 서비스 인스턴스가 필요합니다.


서비스를 만들려면 다음 단계를 수행합니다.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>서비스 삭제

서비스를 삭제하기 전에 사용 중인 연결 디바이스가 없는지 확인합니다. 서비스를 사용 중인 경우 연결된 디바이스를 비활성화합니다. 비활성화 작업은 디바이스와 서비스 간의 연결을 제공하지만 클라우드의 디바이스 데이터는 유지합니다.

> [!IMPORTANT]
> 서비스가 삭제된 후에는 작업을 되돌릴 수 없습니다. 서비스를 사용하던 모든 디바이스는 다른 서비스에 사용하기 전에 공장 기본 설정을 해야 합니다. 이 시나리오에서는 구성뿐 아니라 디바이스의 로컬 데이터도 손실됩니다.
 

서비스를 삭제하려면 다음 단계를 수행합니다.

#### <a name="to-delete-a-service"></a>서비스를 삭제하려면

1. **모든 리소스**로 이동합니다. StorSimple 디바이스 관리자 서비스를 검색합니다. 삭제하려는 서비스를 선택합니다.
   
    ![삭제할 서비스 선택](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. 서비스 대시보드로 이동하여 서비스에 연결된 디바이스가 없는지 확인합니다. 이 서비스에 등록된 디바이스가 없는 경우 효과에 대한 배너 메시지가 나타납니다. **삭제**를 클릭합니다.
   
    ![서비스 삭제](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. 확인 메시지가 나타나면 확인 알림에서 **예**를 클릭합니다. 
   
    ![서비스 삭제 확인](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. 서비스 삭제에는 몇 분 정도 걸릴 수 있습니다. 서비스가 성공적으로 삭제된 후에 알림이 표시됩니다.
   
    ![성공적인 서비스 삭제](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

서비스 목록이 새로 고쳐집니다.

 ![업데이트된 서비스 목록](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>서비스 등록 키 가져오기
서비스를 성공적으로 만든 후에 서비스에 StorSimple 디바이스를 등록해야 합니다. 처음으로 StorSimple 디바이스를 등록하려면 서비스 등록 키가 필요합니다. 기존 StorSimple 서비스에 추가 디바이스를 등록하려면 등록 키와 서비스 데이터 암호화 키(등록 시 첫 번째 디바이스에서 생성된 키)가 모두 필요합니다. 서비스 데이터 암호화 키에 대한 자세한 내용은 [StorSimple 보안](storsimple-security.md)을 참조하세요. 서비스에 대한 **키** 블레이드에 액세스하여 등록 키를 가져올 수 있습니다.

서비스 등록 키를 가져오려면 다음 단계를 수행합니다.

#### <a name="to-get-the-service-registration-key"></a>서비스 등록 키를 가져오려면
1. **StorSimple 디바이스 관리자** 블레이드에서 **관리 &gt;****키**로 이동합니다.
   
   ![키 블레이드](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. **키** 블레이드에서 서비스 등록 키가 표시됩니다. 복사 아이콘을 사용하여 등록 키를 복사합니다. 

서비스 등록 키를 안전한 장소에 보관합니다. 이 키와 서비스 데이터 암호화 키는 이 서비스에 추가 디바이스를 등록할 때 필요합니다. 서비스 등록 키를 가져온 후 StorSimple 인터페이스용 Windows PowerShell을 통해 디바이스를 구성해야 합니다.

## <a name="regenerate-the-service-registration-key"></a>서비스 등록 키 생성
키 회전을 수행해야 하거나 서비스 관리자 목록이 변경된 경우 서비스 등록 키를 다시 생성해야 합니다. 키를 다시 생성하면 후속 디바이스 등록을 위해서만 새 키가 사용됩니다. 이미 등록된 디바이스는 이 과정에 영향을 받지 않습니다.

서비스 등록 키를 다시 생성하려 다음 단계를 수행합니다.

#### <a name="to-regenerate-the-service-registration-key"></a>서비스 등록 키를 다시 생성하려면
1. **StorSimple 디바이스 관리자** 블레이드에서 **관리 &gt;****키**로 이동합니다.
   
   ![키 블레이드](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. **키** 블레이드에서 **다시 생성**을 클릭합니다.
   
   ![다시 생성 클릭](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. **서비스 등록 키 다시 생성** 블레이드에서 키를 다시 생성하는 경우에 필요한 작업을 검토합니다. 이 서비스에 등록된 모든 후속 디바이스는 새 등록 키를 사용합니다. **다시 생성**을 클릭하여 확인합니다. 등록이 완료된 후에 알림이 표시됩니다.
   
   ![다시 생성 키 확인](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. 새 서비스 등록 키가 나타납니다.
   
    ![다시 생성 키 확인](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   이 서비스에 새 디바이스 등록을 위해 이 키를 복사하고 저장합니다.

## <a name="next-steps"></a>다음 단계
* StorSimple 가상 배열 [시작](storsimple-virtual-array-deploy1-portal-prep.md) 방법에 대해 알아봅니다.
* [StorSimple 디바이스 관리](storsimple-ova-web-ui-admin.md)방법에 대해 알아봅니다.

