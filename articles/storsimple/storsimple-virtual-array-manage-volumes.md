---
title: StorSimple Virtual Array에서 볼륨 관리 | Microsoft Docs
description: StorSimple Device Manager 및 이 기능을 사용하여 StorSimple Virtual Array에서 볼륨을 관리하는 방법을 설명합니다.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: a233a9deb58a7c1abc87a622a4f1f2581ee2e477
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125800"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>StorSimple Device Manager 서비스를 사용하여 StorSimple Virtual Array에서 볼륨 관리

## <a name="overview"></a>개요

이 자습서는 StorSimple Device Manager 서비스를 사용하여 StorSimple Virtual Array에서 볼륨을 만들고 관리하는 방법에 대해 설명합니다.

StorSimple Device Manager 서비스는 단일 웹 인터페이스에서 StorSimple 솔루션을 관리하는 Azure Portal의 확장입니다. 공유 및 볼륨 관리 외에도 StorSimple Device Manager 서비스를 사용하여 디바이스를 보고 관리하며, 경고를 보고, 백업 정책 및 백업 카탈로그를 보고 관리할 수 있습니다.

## <a name="volume-types"></a>볼륨 유형

StorSimple 볼륨은 다음과 같을 수 있습니다.

* **로컬로 고정 된**: 이러한 볼륨의 데이터가 항상 배열에 유지 되 고 클라우드로 유출 되지 않습니다.
* **계층화 된**: 이러한 볼륨의 데이터를 클라우드로 분산할 수 있습니다. 계층화된 볼륨을 만들 때 공간의 약 10%는 로컬 계층에 프로비전되고 공간의 90%는 클라우드에 프로비전됩니다. 예를 들어, 1TB 볼륨을 프로비전하는 경우 100GB는 로컬 공간에 상주하고 900GB는 데이터가 계층화될 때 클라우드에서 사용됩니다. 따라서 디바이스의 로컬 공간이 부족하면 로컬 계층에 필요한 10%를 사용할 수 없기 때문에 계층화된 볼륨을 프로비전할 수 없다는 것을 의미합니다.

### <a name="provisioned-capacity"></a>프로비전된 용량
각 볼륨 유형에 대한 최대 프로비전된 용량에 대해 다음 표를 참조하세요.

| **제한 식별자**                                       | **제한**     |
|------------------------------------------------------------|---------------|
| 계층화 볼륨의 최소 크기                            | 500GB        |
| 계층화 볼륨의 최대 크기                            | 5TB          |
| 로컬로 고정된 볼륨의 최소 크기                    | 50GB         |
| 로컬로 고정된 볼륨의 최대 크기                    | 500GB        |

## <a name="the-volumes-blade"></a>볼륨 블레이드
StorSimple 서비스 요약 블레이드의 **볼륨** 메뉴에서는 지정된 StorSimple 배열의 저장소 볼륨의 목록을 표시하고 관리할 수 있습니다.

![볼륨 블레이드](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

볼륨은 다음과 같은 특성으로 구성됩니다.

* **볼륨 이름** – 설명이 포함된 이름은 고유해야 하며 볼륨을 식별하는 데 도움이 됩니다.
* **상태** – 온라인 또는 오프라인 상태가 될 수 있습니다. 오프라인인 경우 볼륨은 해당 볼륨을 사용하는 데 액세스가 허용된 초기자(서버)에 보이지 않습니다.
* **유형** – 볼륨이 **계층화됨**(기본값) 또는 **로컬로 고정**인지를 나타냅니다.
* **용량** - 용량은 초기자(서버)가 저장할 수 있는 데이터의 전체 크기에 비해 사용된 데이터의 양을 지정합니다.
* **Backup** – StorSimple 가상 배열의 경우에 모든 볼륨은 자동으로 백업에서 사용할 수 있습니다.
* **연결된 호스트** – 이 볼륨에 대한 액세스가 허용된 초기자(서버)를 지정합니다.

![볼륨 세부 정보](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

이 자습서의 지침을 사용하여 다음 작업을 수행합니다.

* 볼륨 추가
* 볼륨 수정
* 볼륨을 오프라인으로 전환
* 볼륨 삭제

## <a name="add-a-volume"></a>볼륨 추가

1. StorSimple 서비스 요약 블레이드의 명령 모음에서 **+볼륨 추가**를 클릭합니다. 그려면 **볼륨 추가** 블레이드가 열립니다.
   
    ![볼륨 추가](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. **볼륨 추가** 블레이드에서 다음을 수행합니다.
   
   * **볼륨 이름** 필드에서 볼륨의 고유 이름을 입력합니다. 이름은 3~127개의 문자를 포함하는 문자열이어야 합니다.
   * **형식** 드롭다운 목록에서 **계층** 또는 **로컬로 고정** 볼륨을 만들지 여부를 지정합니다. 로컬 보증, 낮은 대기 시간 및 높은 성능을 필요로 하는 워크로드의 경우 **로컬로 고정된 볼륨**을 선택합니다. 다른 모든 데이터에 대해서는 **계층화된** 볼륨을 선택합니다.
   * **용량** 필드에서 볼륨의 크기를 지정합니다. 계층화된 볼륨은 500GB에서 5TB 사이여야 하고 로컬로 고정된 볼륨은 50GB에서 500GB 사이여야 합니다.
   * * **호스트 연결**을 클릭하고 이 볼륨에 연결하려는 iSCSI 초기자에 해당하는 ACR(액세스 제어 레코드)를 선택한 다음 **선택**을 클릭합니다.
3. 새롭게 연결된 호스트를 추가하려면 **새로 추가**를 클릭하고 호스트의 이름 및 해당 IQN(iSCSI 정규화 이름)을 입력한 다음 **추가**를 클릭합니다.
   
    ![볼륨 추가](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. 볼륨 구성을 완료했다면 **만들기**를 클릭합니다. 볼륨이 지정된 설정으로 만들어지면 동일하게 성공적으로 만들었다는 알림이 표시됩니다. 기본적으로 볼륨에 대한 백업을 사용하도록 설정합니다.
5. 볼륨이 성공적으로 만들어졌는지 확인하려면 **볼륨** 블레이드로 이동합니다. 볼륨이 목록으로 표시되어야 합니다.
   
    ![볼륨 만들기 성공](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>볼륨 수정

볼륨에 액세스하는 호스트를 변경할 경우 볼륨을 수정합니다. 볼륨을 만든 후에 볼륨의 다른 특성을 수정할 수 없습니다.

#### <a name="to-modify-a-volume"></a>볼륨을 수정하려면

1. StorSimple 서비스 요약 블레이드의 **볼륨** 설정에서 수정하려는 볼륨이 상주하는 가상 배열을 선택합니다.
2. 볼륨을 **선택**하고 **연결된 호스트**를 클릭하여 현재 연결된 호스트를 보고 다른 서버로 수정합니다.
   
    ![볼륨 편집](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. **저장** 명령 모음을 클릭하여 변경 사항을 저장합니다. 지정된 설정이 적용되고 알림이 표시됩니다.

## <a name="take-a-volume-offline"></a>볼륨을 오프라인으로 전환

볼륨을 수정 또는 삭제하려는 경우 볼륨을 오프라인으로 전환해야 할 수 있습니다. 볼륨이 오프라인 상태인 경우 읽기 전용 액세스를 사용할 수 없습니다. 디바이스에서뿐 아니라 호스트에서도 볼륨을 오프라인으로 전환해야 합니다.

#### <a name="to-take-a-volume-offline"></a>볼륨을 오프라인으로 전환하려면

1. 오프라인으로 전환하기 전에 해당 볼륨을 사용 중이 아니어야 합니다.
2. 먼저 호스트에서 볼륨을 오프라인으로 전환합니다. 이렇게 하면 볼륨에서 데이터가 손상될 잠재적 위험이 없습니다. 특정 단계의 경우 호스트 운영 체제에 대한 지침을 참조하세요.
3. 호스트의 볼륨이 오프라인이 되면 다음 단계를 수행하여 배열의 볼륨을 오프라인으로 전환합니다.
   
   * StorSimple 서비스 요약 블레이드의 **볼륨** 설정에서 오프라인으로 전환하려는 볼륨이 상주하는 가상 배열을 선택합니다.
   * 볼륨을 **선택**하고 **...**(또는 이 행의 오른쪽)를 클릭하고 상황에 맞는 메뉴에서 **오프라인으로 전환**을 선택합니다.
     
        ![오프라인 볼륨](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * **오프라인** 블레이드에서 정보를 검토하고 작업에 대한 동의를 확인합니다. **오프라인으로 전환**을 클릭하여 볼륨을 오프라인으로 전환합니다. 진행 중인 작업의 알림이 표시됩니다.
   * 볼륨이 성공적으로 오프라인으로 전환되었는지 확인하려면 **볼륨** 블레이드로 이동합니다. 볼륨의 상태가 오프라인으로 표시됩니다.
     
       ![오프라인 볼륨 확인](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>볼륨 삭제

> [!IMPORTANT]
> 오프라인 상태인 경우에만 볼륨을 삭제할 수 있습니다.
> 
> 

볼륨을 삭제하려면 다음 단계를 완료합니다.

#### <a name="to-delete-a-volume"></a>볼륨을 삭제하려면

1. StorSimple 서비스 요약 블레이드의 **볼륨** 설정에서 삭제하려는 볼륨이 상주하는 가상 배열을 선택합니다.
2. 볼륨을 **선택**하고 **...**(또는 이 행의 오른쪽)를 클릭하고 상황에 맞는 메뉴에서 **삭제**를 선택합니다.
   
    ![볼륨 삭제](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. 삭제하려는 볼륨의 상태를 확인합니다. 삭제하려는 볼륨이 오프라인 상태가 아닌 경우 [볼륨을 오프라인으로 전환](#take-a-volume-offline)의 단계를 따라 먼저 오프라인 상태로 전환합니다.
4. **삭제** 블레이드에서 확인하라는 메시지가 나타나면 확인을 수락하고 **삭제**를 클릭합니다. 이제 볼륨이 삭제되고 **볼륨** 블레이드가 가상 배열 내의 업데이트된 볼륨 목록을 보여줍니다.

## <a name="next-steps"></a>다음 단계

[StorSimple 볼륨 복제](storsimple-virtual-array-clone.md)방법에 대해 배웁니다.

