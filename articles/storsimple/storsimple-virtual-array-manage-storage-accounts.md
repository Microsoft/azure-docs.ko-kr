---
title: StorSimple Virtual Array의 저장소 계정 공유 자격 증명 관리 | Microsoft Docs
description: StorSimple Device Manager 구성 페이지를 사용하여 StorSimple Virtual Array와 연결된 스토리지 계정 자격 증명의 보안 키를 추가, 편집, 삭제 또는 회전하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: a992851deda0659509c0ee4ea5de76b19734f017
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128841"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>StorSimple Device Manager를 사용하여 StorSimple Virtual Array의 스토리지 계정 자격 증명 관리

## <a name="overview"></a>개요
StorSimple Virtual Array의 StorSimple Device Manager 서비스 블레이드 중 **구성** 섹션에서는 StorSimple Manager 서비스에서 만들 수 있는 글로벌 서비스 매개 변수를 표시합니다. 이러한 매개 변수는 서비스에 연결된 모든 디바이스에 적용할 수 있으며 다음을 포함합니다.

* Storage 계정 자격 증명
* 액세스 제어 레코드
  
  ![Device Manager 서비스 대시보드](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

이 자습서에서는 StorSimple 가상 배열에 대한 저장소 계정 자격 증명을 추가, 편집 또는 삭제하는 방법에 대해 설명합니다. 이 자습서의 정보는 StorSimple 가상 배열에만 적용됩니다. 8000 시리즈에서 저장소 계정을 관리하는 방법에 대한 자세한 내용은 [StorSimple Manager 서비스를 사용하여 저장소 계정 관리](storsimple-manage-storage-accounts.md)를 참조하세요.

Storage 계정 자격 증명은 클라우드 서비스 공급자와 Storage 계정에 액세스하기 위해 디바이스가 사용하는 자격 증명을 포함합니다. Microsoft Azure 저장소 계정의 경우 계정 이름 및 기본 액세스 키와 같은 자격 증명이 있습니다.

**Storage 계정 자격 증명** 블레이드에서 청구 구독에 대해 만들어진 모든 Storage 계정 자격 증명이 다음 정보를 포함하여 테이블 형식으로 표시됩니다.

* **이름** – 만들어졌을 때 계정에 할당된 고유 이름입니다.
* **SSL 사용** – SSL 사용 및 디바이스와 클라우드 사이의 통신이 보안 채널을 통해 이루어지는지 여부입니다.
  
  ![구성 섹션](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

**Storage 계정 자격 증명** 블레이드에서 수행할 수 있는 Storage 계정 자격 증명과 관련된 가장 일반적인 태스크는 다음과 같습니다.

* 저장소 계정 자격 증명 추가
* 저장소 계정 자격 증명 편집
* 저장소 계정 자격 증명 삭제

## <a name="types-of-storage-account-credentials"></a>저장소 계정 자격 증명 유형
StorSimple 디바이스에서 사용할 수 있는 스토리지 계정 자격 증명에는 다음과 같은 세 종류가 있습니다.

* **자동 생성된 저장소 계정 자격 증명** – 이름 제안 시, 서비스를 처음 만들 때 이 저장소 계정 자격 증명 유형이 자동으로 생성됩니다. 이 저장소 계정 자격 증명을 만드는 방법에 대해 더 알아보려면 [새 서비스 만들기](storsimple-virtual-array-manage-service.md#create-a-service)를 참조하세요.
* **서비스 구독의 스토리지 계정 자격 증명** – 이러한 계정은 서비스와 동일한 구독과 연결된 Azure Storage 계정 자격 증명입니다. 이러한 스토리지 계정 자격 증명을 만드는 방법에 대해 더 알아보려면 [Azure Storage 계정 정보](../storage/common/storage-create-storage-account.md)를 참조하세요.
* **서비스 구독 외부의 스토리지 계정 자격 증명** - 이러한 계정 자격 증명은 서비스와 연결되지 않았고 서비스가 만들어지기 전에 존재했던 Azure Storage 계정 자격 증명입니다.

## <a name="add-a-storage-account-credential"></a>저장소 계정 자격 증명 추가
스토리지 계정에 연결된 액세스 자격 증명 및 고유 이름을 제공하여 스토리지 계정 자격 증명을 StorSimple Device Manager 서비스 구성에 추가할 수 있습니다. 디바이스와 클라우드 사이에서 네트워크 통신을 위한 보안 채널을 만들기 위해 SSL(Secure Sockets Layer) 모드를 사용하는 옵션도 있습니다.

특정 클라우드 서비스 공급자에 대해 여러 계정을 만들 수 있습니다. 저장소 계정 자격 증명을 저장하는 동안 해당 서비스는 클라우드 서비스 공급자와 통신을 시도합니다. 사용자가 지정한 자격 증명 및 액세스 자료가 이 때 인증됩니다. 인증에 성공하는 경우에만 저장소 계정 자격 증명이 만들어집니다. 인증에 실패하는 경우 그에 따른 오류 메시지가 표시됩니다.

Azure Storage 계정 자격 증명을 추가하려면 다음 절차를 사용합니다.

* 디바이스 관리자 서비스와 동일한 Azure 구독에 있는 저장소 계정 자격 증명을 추가하려면
* Device Manager 서비스 구독 외부에 있는 Azure 스토리지 계정 자격 증명을 추가하려면

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>디바이스 관리자 서비스와 동일한 Azure 구독에 있는 저장소 계정 자격 증명을 추가하려면

1. Device Manager 서비스를 찾아 선택하고 두 번 클릭합니다. 그러면 **개요** 블레이드가 열립니다.
2. **구성** 섹션 내의 **Storage 계정 자격 증명**을 선택합니다.
3. **추가**를 클릭합니다.
4. **저장소 계정 추가** 블레이드에서 다음을 수행합니다.
   
    1. **구독**에 **현재**를 선택합니다.
    2. Azure 저장소 계정의 이름을 제공합니다.
    3. **사용**을 선택하여 StorSimple 디바이스와 클라우드 간의 네트워크 통신을 위한 보안 채널을 만듭니다. 사설 클라우드 내에서 작동하는 경우에만 **사용 안 함**을 선택합니다.
    4. **추가**를 클릭합니다. 저장소 계정이 성공적으로 만들어진 후 알림이 표시됩니다.<br></br>
   
        ![기존 저장소 계정 자격 증명 추가](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Device Manager 서비스 구독 외부에 있는 Azure 스토리지 계정 자격 증명을 추가하려면

1. Device Manager 서비스를 찾아 선택하고 두 번 클릭합니다. 그러면 **개요** 블레이드가 열립니다.
2. **구성** 섹션 내의 **Storage 계정 자격 증명**을 선택합니다. 그러면 StorSimple Device Manager 서비스와 연결된 모든 기존 스토리지 계정 자격 증명을 나열합니다.
3. **추가**를 클릭합니다.
4. **저장소 계정 추가** 블레이드에서 다음을 수행합니다.
   
    1. **구독**에 **기타**를 선택합니다.
   
    2. Azure Storage 계정 자격 증명의 이름을 제공합니다.
   
    3. **스토리지 계정 선택키** 텍스트 상자에서 Azure Storage 계정 자격 증명의 기본 선택키를 지정합니다. 이 키를 가져오려면 Azure Storage 서비스로 이동하고 스토리지 계정 자격 증명을 선택한 다음 **계정 키 관리**를 클릭합니다. 이제 기본 선택키를 복사할 수 있습니다.
   
    4. SSL을 사용하도록 설정하려면 **사용** 단추를 클릭하여 StorSimple 디바이스 관리자 서비스와 클라우드 간의 네트워크 통신을 위한 보안 채널을 만듭니다. 사설 클라우드 내에서 작동하는 경우에만 **사용 안 함** 단추를 클릭합니다.
   
    5. **추가**를 클릭합니다. 저장소 계정 자격 증명이 성공적으로 만들어진 후 알림이 표시됩니다.

5. 새로 만든 스토리지 계정 자격 증명은 **Storage 계정 자격 증명**의 StorSimple Configure Device Manager 서비스 블레이드에 표시됩니다.
   
    ![Device Manager 서비스 구독 외부의 스토리지 계정 자격 증명 추가](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>저장소 계정 자격 증명 편집
디바이스에서 사용하는 스토리지 계정 자격 증명을 편집할 수 있습니다. 현재 사용 중인 저장소 계정 자격 증명을 편집할 경우 수정할 수 있는 필드는 저장소 계정 자격 증명에 대한 선택키 및 SSL 모드입니다. 새 저장소 액세스 키를 제공하거나 **SSL 모드 사용** 선택을 수정하고 업데이트된 설정을 저장할 수 있습니다.

#### <a name="to-edit-a-storage-account-credential"></a>저장소 계정 자격 증명을 편집하려면
1. Device Manager 서비스를 찾아 선택하고 두 번 클릭합니다. 그러면 **개요** 블레이드가 열립니다.
2. **구성** 섹션 내의 **Storage 계정 자격 증명**을 선택합니다. 그러면 StorSimple Device Manager 서비스와 연결된 모든 기존 스토리지 계정 자격 증명을 나열합니다.
3. 저장소 계정 자격 증명의 테이블 형식 목록에서 수정하려는 계정을 선택하고 두 번 클릭합니다.
4. 저장소 계정 자격 증명 **속성** 블레이드에서 다음을 수행합니다.
   
   1. 필요에 따라 **SSL 사용** 모드 선택을 수정할 수 있습니다.
   2. 저장소 계정 자격 증명 선택키를 다시 생성하도록 선택할 수 있습니다. 자세한 내용은 [저장소 계정 키 다시 생성](../storage/common/storage-account-manage.md#access-keys)을 참조하세요. 새 저장소 계정 자격 증명 키를 제공합니다. Azure 저장소 계정의 경우 이 키가 기본 선택키입니다.
   3. **속성** 블레이드 맨 위에 있는 **저장**을 클릭하여 설정을 저장합니다. 설정은 **Storage 계정 자격 증명** 블레이드에서 업데이트됩니다.
      
      ![저장소 계정 자격 증명 편집](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>저장소 계정 자격 증명 삭제
> [!IMPORTANT]
> 사용하지 않는 경우에만 저장소 계정 자격 증명을 삭제할 수 있습니다. 저장소 계정 자격 증명을 사용하는 중이면 알림이 표시됩니다.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>저장소 계정 자격 증명을 삭제하려면
1. Device Manager 서비스를 찾아 선택하고 두 번 클릭합니다. 그러면 **개요** 블레이드가 열립니다.
2. **구성** 섹션 내의 **Storage 계정 자격 증명**을 선택합니다. 그러면 StorSimple Device Manager 서비스와 연결된 모든 기존 스토리지 계정 자격 증명을 나열합니다.
3. 저장소 계정 자격 증명의 테이블 형식 목록에서 삭제하려는 계정을 선택하고 두 번 클릭합니다.
4. 저장소 계정 자격 증명 **속성** 블레이드에서 다음을 수행합니다.
   
   1. **삭제**를 클릭하여 자격 증명을 삭제합니다.
   2. 확인 메시지가 나타나면 **예** 를 클릭하여 삭제를 계속합니다. 테이블 형식 목록이 변경을 반영하도록 업데이트됩니다.
      
      ![저장소 계정 자격 증명 삭제](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>저장소 계정 자격 증명 키 동기화
보안상의 이유로 키 회전이 데이터 센터에서 요구되기도 합니다. Microsoft Azure 관리자가 Microsoft Azure Storage 서비스를 통해 스토리지 계정 자격 증명에 직접 액세스하여 기본 키 또는 보조 키를 다시 생성하거나 변경할 수 있습니다. StorSimple 디바이스 관리자 서비스는 이 변경 사항을 자동으로 표시하지 않습니다.

StorSimple Device Manager 서비스에 변경을 알리려면 StorSimple Device Manager 서비스에 액세스하고 스토리지 계정 자격 증명에 액세스한 다음, 기본 또는 보조 키(변경된 키에 따라 다름)를 동기화해야 합니다. 그러면 서비스는 최신 키를 가져오고 해당 키를 암호화하여 디바이스에 암호화된 키를 보냅니다.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>서비스와 동일한 구독에서 저장소 계정 자격 증명에 대한 키를 동기화하려면(Azure에만 해당)
1. 서비스 방문 블레이드에서 서비스를 선택하고 서비스 이름을 두 번 클릭한 다음 **구성** 섹션에서 **Storage 계정 자격 증명**을 클릭합니다.
2. **Storage 계정 자격 증명** 블레이드의 Storage 계정 자격 증명 목록에서 동기화하려는 키의 Storage 계정 자격 증명을 선택합니다.
3. 선택한 저장소 계정 자격 증명의 **속성** 블레이드에서 다음을 수행합니다.
   
    1. **추가**를 클릭한 다음 **동기화 선택키**를 클릭합니다.
   
    2. 확인 메시지가 나타나면 **동기화 키**를 클릭하여 동기화를 완료합니다.
    
4. StorSimple Device Manager 서비스에서 이전에 Microsoft Azure Storage 서비스에서 변경된 키를 업데이트해야 합니다. **동기화 저장소 계정 키** 블레이드에서 기본 선택키가 변경(다시 생성)되는 경우 [기본]을 클릭한 다음 **동기화 키**를 클릭합니다. 보조 키가 변경된 경우 **보조**를 클릭한 다음 **동기화 키**를 클릭합니다.
   
    ![동기화 선택키](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>다음 단계
* [StorSimple 가상 배열을 관리](storsimple-ova-web-ui-admin.md)하는 방법을 알아봅니다.

