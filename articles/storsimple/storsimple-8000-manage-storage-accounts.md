---
title: 저장소 계정 자격 증명, StorSimple 8000 시리즈 장치 관리
description: StorSimple 디바이스 관리자 구성 페이지를 사용하여 스토리지 계정에 대한 보안 키를 추가, 편집, 삭제 또는 회전하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 11bc97be7ff8d924f7ccd0b4672a8f4cb0a178ff
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116330"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>StorSimple 디바이스 관리자 서비스를 사용하여 스토리지 계정 자격 증명 관리

## <a name="overview"></a>개요

StorSimple 디바이스 관리자 서비스 블레이드의 **구성** 섹션에서는 StorSimple 디바이스 관리자 서비스에서 만들 수 있는 모든 글로벌 서비스 매개 변수를 표시합니다. 이러한 매개 변수는 서비스에 연결된 모든 디바이스에 적용할 수 있으며 다음을 포함합니다.

* Storage 계정 자격 증명
* 대역폭 템플릿 
* 액세스 제어 레코드 

이 자습서에서는 스토리지 계정 자격 증명을 추가, 편집 또는 삭제하거나, 스토리지 계정에 대한 보안 키를 회전하는 방법에 대해 설명합니다.

 ![스토리지 계정 자격 증명 목록](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Storage 계정에는 StorSimple 디바이스가 클라우드 서비스 공급자를 통해 Storage 계정에 액세스하는 데 사용하는 자격 증명이 포함되어 있습니다. Microsoft Azure Storage 계정의 경우 계정 이름 및 기본 액세스 키와 같은 자격 증명이 있습니다. 

**Storage 계정 자격 증명** 블레이드에는 청구 구독에 대해 만들어진 모든 Storage 계정이 다음 정보를 포함하여 테이블 형식으로 표시됩니다.

* **이름** – 만들어졌을 때 계정에 할당된 고유 이름입니다.
* **SSL 사용** – SSL 사용 및 디바이스와 클라우드 사이의 통신이 보안 채널을 통해 이루어지는지 여부입니다.
* **사용 볼륨** – 스토리지 계정을 사용하는 볼륨의 수입니다.

스토리지 계정과 관련하여 수행할 수 있는 가장 일반적인 작업은 다음과 같습니다.

* 스토리지 계정 추가 
* 스토리지 계정 편집 
* 스토리지 계정 삭제 
* 스토리지 계정의 키 회전 

## <a name="types-of-storage-accounts"></a>스토리지 계정 유형

StorSimple 디바이스에서 사용할 수 있는 스토리지 계정에는 다음과 같은 세 종류가 있습니다.

* **자동 생성된 스토리지 계정** – 이름 제안 시, 서비스를 처음 만들 때 이 스토리지 계정 유형이 자동으로 생성됩니다. 이 스토리지 계정을 만드는 방법에 대해 자세히 알아보려면 [온-프레미스 StorSimple 디바이스 배포](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service)에서 [1단계: 새 서비스 만들기](storsimple-8000-deployment-walkthrough-u2.md)를 참조하세요. 
* **서비스 구독의 Storage 계정** – 이러한 계정은 서비스와 동일한 구독과 연결된 Azure Storage 계정입니다. 이러한 Storage 계정을 만드는 방법에 대해 더 알아보려면 [Azure Storage 계정 정보](../storage/common/storage-create-storage-account.md)를 참조하세요. 
* **서비스 구독 외의 Storage 계정** - 이러한 계정은 서비스와 연결되지 않았고 서비스가 만들어지기 전에 존재했던 Azure Storage 계정입니다.

## <a name="add-a-storage-account"></a>스토리지 계정 추가

스토리지 계정(지정된 클라우드 서비스 공급자를 통해)에 연결된 액세스 자격 증명 및 친숙한 고유 이름을 제공하여 스토리지 계정을 추가할 수 있습니다. 디바이스와 클라우드 사이에서 네트워크 통신을 위한 보안 채널을 만들기 위해 SSL(Secure Sockets Layer) 모드를 사용하는 옵션도 있습니다.

특정 클라우드 서비스 공급자에 대해 여러 계정을 만들 수 있습니다. 하지만 스토리지 계정을 만든 후에는 클라우드 서비스 공급자를 변경할 수 없습니다.

스토리지 계정을 저장하는 동안 해당 서비스는 클라우드 서비스 공급자와 통신을 시도합니다. 사용자가 지정한 자격 증명 및 액세스 자료가 이 때 인증됩니다. 인증에 성공하는 경우에만 스토리지 계정이 만들어집니다. 인증에 실패하는 경우 그에 따른 오류 메시지가 표시됩니다.

Azure Storage 계정 자격 증명을 추가하려면 다음 절차를 사용합니다.

* 디바이스 관리자 서비스와 동일한 Azure 구독에 있는 스토리지 계정 자격 증명을 추가하려면
* Device Manager 서비스 구독 외부에 있는 Azure 스토리지 계정 자격 증명을 추가하려면

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>StorSimple 디바이스 관리자 서비스 구독 외부에 있는 Azure Storage 계정 자격 증명을 추가하려면

1. StorSimple 디바이스 관리자 서비스를 찾아 선택하고 두 번 클릭합니다. 그러면 **개요** 블레이드가 열립니다.
2. **구성** 섹션 내의 **Storage 계정 자격 증명**을 선택합니다. 그러면 StorSimple Device Manager 서비스와 연결된 모든 기존 스토리지 계정 자격 증명을 나열합니다.
3. **추가**를 클릭합니다.
4. **스토리지 계정 자격 증명 추가** 블레이드에서 다음을 수행합니다.
   
    1. **구독**에 **기타**를 선택합니다.
   
    2. Azure Storage 계정 자격 증명의 이름을 제공합니다.
   
    3. **스토리지 계정 선택키** 텍스트 상자에서 Azure Storage 계정 자격 증명의 기본 선택키를 지정합니다. 이 키를 가져오려면 Azure Storage 서비스로 이동하고 스토리지 계정 자격 증명을 선택한 다음 **계정 키 관리**를 클릭합니다. 이제 기본 선택키를 복사할 수 있습니다.
   
    4. SSL을 사용하도록 설정하려면 **사용** 단추를 클릭하여 StorSimple 디바이스 관리자 서비스와 클라우드 간의 네트워크 통신을 위한 보안 채널을 만듭니다. 프라이빗 클라우드 내에서 작동하는 경우에만 **사용 안 함** 단추를 클릭합니다.
   
    5. **추가**를 클릭합니다. 스토리지 계정 자격 증명이 성공적으로 만들어진 후 알림이 표시됩니다.

5. 새로 만든 스토리지 계정 자격 증명은 **Storage 계정 자격 증명**의 StorSimple Configure Device Manager 서비스 블레이드에 표시됩니다.
   


## <a name="edit-a-storage-account"></a>스토리지 계정 편집

볼륨 컨테이너에서 사용되는 스토리지 계정을 편집할 수 있습니다. 현재 사용 중인 스토리지 계정을 편집할 경우 수정할 수 있는 유일한 필드는 스토리지 계정에 대한 액세스 키입니다. 새 스토리지 액세스 키를 제공할 수 있으며 업데이트된 설정을 저장할 수 있습니다.

#### <a name="to-edit-a-storage-account"></a>스토리지 계정을 편집하려면

1. StorSimple 디바이스 관리자 서비스로 이동합니다. **구성** 섹션에서 **Storage 계정 자격 증명**을 클릭합니다.

    ![Storage 계정 자격 증명](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. **Storage 계정 자격 증명** 블레이드의 Storage 계정 자격 증명 목록에서 편집하려는 항목을 선택하고 클릭합니다. 

3. **SSL 사용** 섹션을 수정할 수 있습니다. **자세히...** 를 클릭하여 스토리지 계정 액세스 키를 회전하도록 **Sync access key to rotate**(회전할 액세스 키 동기화)를 선택할 수도 있습니다. 키 회전을 수행하는 방법에 대한 자세한 내용을 보려면 [스토리지 계정의 키 회전](#key-rotation-of-storage-accounts)으로 이동합니다. 설정을 수정한 후 **저장**을 클릭합니다. 

    ![편집된 스토리지 계정 자격 증명 저장](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. 확인하라는 메시지가 표시되면 **예**를 클릭합니다. 

    ![수정 확인](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

스토리지 계정에 대한 설정이 업데이트되고 저장됩니다. 

## <a name="delete-a-storage-account"></a>스토리지 계정 삭제

> [!IMPORTANT]
> 볼륨 컨테이너에서 사용하지 않는 경우에만 스토리지 계정을 삭제할 수 있습니다. 스토리지 계정을 볼륨 컨테이너에서 사용 중인 경우 먼저 볼륨 컨테이너를 삭제하고 연결된 스토리지 계정을 삭제합니다.

#### <a name="to-delete-a-storage-account"></a>스토리지 계정을 삭제하려면

1. StorSimple 디바이스 관리자 서비스로 이동합니다. **구성** 섹션에서 **Storage 계정 자격 증명**을 클릭합니다.

2. 스토리지 계정의 테이블 형식 목록에서 삭제하려는 계정을 마우스로 가리킵니다. 마우스 오른쪽 단추를 클릭하여 상황에 맞는 메뉴를 호출하고 **삭제**를 클릭합니다.

    ![스토리지 계정 자격 증명 삭제](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. 확인 메시지가 나타나면 **예** 를 클릭하여 삭제를 계속합니다. 테이블 형식 목록이 변경 내용을 반영하도록 업데이트됩니다.

    ![삭제 확인](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>스토리지 계정의 키 회전

보안상의 이유로 키 회전이 데이터 센터에서 요구되기도 합니다. 각 Microsoft Azure 구독에 하나 이상의 연결된 스토리지 계정을 만들 수 있습니다. 이러한 계정에 대한 액세스는 해당 스토리지 계정에 대한 구독 및 액세스 키를 통해 제어됩니다. 

스토리지 계정을 만들면 Microsoft Azure에서 두 개의 512비트 스토리지 액세스 키를 생성합니다. 이 키는 스토리지 계정에 액세스하는 경우 인증에 사용됩니다. 두 개의 스토리지 액세스 키가 있으면 스토리지 서비스나 해당 서비스에 대한 액세스에 중단 없이 키를 다시 생성할 수 있습니다. 현재 사용 중인 키는 *기본* 키이며 백업 키는 *보조* 키라고 합니다. Microsoft Azure StorSimple 디바이스가 클라우드 스토리지 서비스 공급자에 액세스할 때 이러한 두 키 중 하나를 제공해야 합니다.

## <a name="what-is-key-rotation"></a>키 회전 정의

일반적으로 애플리케이션은 데이터 액세스에 키 중 하나만 사용합니다. 특정 시간이 지나면 애플리케이션이 두 번째 키를 사용하도록 전환할 수 있습니다. 두 번째 키로 애플리케이션을 전환한 후 첫 번째 키를 사용 중지한 다음 새 키를 생성합니다. 이러한 방식으로 두 키를 사용하면 가동 중지 시간 없이 애플리케이션이 데이터에 액세스할 수 있습니다.

스토리지 계정 키는 항상 암호화된 형태로 서비스에 저장됩니다. 하지만 StorSimple 디바이스 관리자 서비스를 통해 다시 설정할 수 있습니다. 서비스는 StorSimple 디바이스 관리자 서비스를 처음 만들 때 생성한 기본 스토리지 계정뿐 아니라 Storage 서비스에서 만든 계정을 비롯하여 동일한 구독의 모든 스토리지 계정에 대해 기본 키 및 보조 키를 가져올 수 있습니다. StorSimple 디바이스 관리자 서비스는 Azure 클래식 포털에서 항상 이러한 키를 가져와 암호화된 방법으로 저장합니다.

## <a name="rotation-workflow"></a>회전 워크플로

Microsoft Azure 관리자가 Storage 계정에 직접 액세스하여(Microsoft Azure Storage 서비스를 통해) 기본 키 또는 보조 키를 다시 생성하거나 변경할 수 있습니다. StorSimple 디바이스 관리자 서비스는 이 변경 사항을 자동으로 표시하지 않습니다.

StorSimple 디바이스 관리자 서비스에 변경을 알리려면 StorSimple 디바이스 관리자 서비스에 액세스하고 스토리지 계정에 액세스한 다음 기본 또는 보조 키(변경된 키에 따라 다름)를 동기화해야 합니다. 그러면 서비스는 최신 키를 가져오고 해당 키를 암호화하여 디바이스에 암호화된 키를 보냅니다.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>서비스와 동일한 구독에서 스토리지 계정에 대한 키를 동기화하려면 
1. StorSimple 디바이스 관리자 서비스로 이동합니다. **구성** 섹션에서 **Storage 계정 자격 증명**을 클릭합니다.
2. 스토리지 계정의 테이블 형식 목록에서 수정하려는 항목을 클릭합니다. 

    ![키 동기화](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. **...자세히**를 클릭하고 **Sync access key to rotate**(회전할 액세스 키 동기화)를 선택합니다.   

    ![키 동기화](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. StorSimple Device Manager 서비스에서 이전에 Microsoft Azure Storage 서비스에서 변경된 키를 업데이트해야 합니다. 기본 액세스 키가 변경(다시 생성)된 경우 **기본** 키를 선택합니다. 보조 키가 변경된 경우 **보조** 키를 선택합니다. **키 동기화**를 클릭합니다.
      
      ![키 동기화](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

키가 성공적으로 동기화되면 알림이 표시됩니다.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>서비스 구독 외의 스토리지 계정에 대한 키를 동기화하려면
1. **서비스** 페이지에서 **구성** 탭을 클릭합니다.
2. **Storage 계정 추가/편집**을 클릭합니다.
3. 대화 상자에서 다음을 수행합니다.
   
   1. 업데이트하려는 액세스 키가 있는 스토리지 계정을 선택합니다.
   2. StorSimple 디바이스 관리자 서비스에서 스토리지 액세스 키를 업데이트해야 합니다. 이 경우 스토리지 액세스 키를 볼 수 있습니다. **Storage 계정 액세스 키** 상자에 새 키를 입력합니다. 
   3. 변경 내용을 저장합니다. 이제 스토리지 계정 액세스 키가 업데이트됩니다.

## <a name="next-steps"></a>다음 단계
* [StorSimple 보안](storsimple-8000-security.md)에 대해 자세히 알아봅니다.
* [StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스를 관리하는 방법](storsimple-8000-manager-service-administration.md)에 대해 자세히 알아봅니다.

