---
title: StorSimple Virtual Array 공유 관리 | Microsoft Docs
description: StorSimple 디바이스 관리자 및 이 기능을 사용하여 StorSimple 가상 배열에서 공유를 관리하는 방법을 설명합니다.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 82a6cdb6c9a39a0d196049a7ba662681ea06b36a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116869"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>StorSimple Device Manager 서비스를 사용하여 StorSimple Virtual Array에서 공유 관리

## <a name="overview"></a>개요

이 자습서는 StorSimple Device Manager 서비스를 사용하여 StorSimple Virtual Array에서 공유를 만들고 관리하는 방법에 대해 설명합니다.

StorSimple Device Manager 서비스는 단일 웹 인터페이스에서 StorSimple 솔루션을 관리하는 Azure Portal의 확장입니다. 공유 및 볼륨 관리 외에도 StorSimple Device Manager 서비스를 사용하여 디바이스를 보고 관리하며, 경고를 보고, 백업 정책을 관리하고, 백업 카탈로그를 관리할 수 있습니다.

## <a name="share-types"></a>공유 유형

StorSimple 공유는 다음과 같을 수 있습니다.

* **로컬로 고정 된**: 이러한 공유의 데이터가 항상 배열에 유지 되 고 클라우드로 유출 되지 않습니다.
* **계층화 된**: 이러한 공유의 데이터를 클라우드로 분산할 수 있습니다. 계층화된 공유를 만들 때 공간의 약 10%는 로컬 계층에 프로비전되고 공간의 90%는 클라우드에 프로비전됩니다. 예를 들어, 1TB 공유를 프로비전하는 경우 100GB는 로컬 공간에 상주하고 900GB는 데이터가 계층화될 때 클라우드에서 사용됩니다. 따라서 디바이스의 로컬 공간이 부족하면 로컬 계층에 필요한 10%를 사용할 수 없기 때문에 계층화된 공유를 프로비전할 수 없다는 것을 의미합니다.

### <a name="provisioned-capacity"></a>프로비전된 용량

각 공유 유형에 대한 최대 프로비전된 용량에 대해 다음 표를 참조하세요.

| **제한 식별자** | **제한** |
| --- | --- |
| 계층화 공유의 최소 크기 |500GB |
| 계층화 공유의 최대 크기 |20TB |
| 로컬로 고정된 공유의 최소 크기 |50GB |
| 로컬로 고정된 공유의 최대 크기 |2TB |

## <a name="the-shares-blade"></a>공유 블레이드

StorSimple 서비스 요약 블레이드의 **공유** 메뉴에서는 지정된 StorSimple 배열의 저장소 공유의 목록을 표시하고 관리할 수 있습니다.

![공유 블레이드](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

공유는 다음과 같은 특성으로 구성됩니다.

* **공유 이름** – 설명이 포함된 이름은 고유해야 하며 공유를 식별하는 데 도움이 됩니다.
* **상태** – 온라인 또는 오프라인 상태가 될 수 있습니다. 공유가 오프라인 상태이면 공유 사용자는 공유에 액세스할 수 없습니다.
* **유형** – 공유가 **계층화됨**(기본값) 또는 **로컬로 고정**인지를 나타냅니다.
* **용량** - 용량은 공유에 저장할 수 있는 데이터의 전체 크기에 비해 사용된 데이터의 양을 지정합니다.
* **설명** – 공유를 설명하는 데 도움이 되는 옵션 설정입니다.
* **사용 권한** - Windows 탐색기를 통해 관리할 수 있는 공유에 대한 NTFS 사용 권한입니다.
* **Backup** – StorSimple 가상 배열의 경우에 모든 공유는 자동으로 백업에서 사용할 수 있습니다.

![공유 세부 정보](./media/storsimple-virtual-array-manage-shares/share-details.png)

이 자습서의 지침을 사용하여 다음 작업을 수행합니다.

* 공유 추가
* 공유 수정
* 오프라인으로 공유 전환
* 공유 삭제

## <a name="add-a-share"></a>공유 추가

1. StorSimple 서비스 요약 블레이드의 명령 모음에서 **+공유 추가**를 클릭합니다. 그려면 **공유 추가** 블레이드가 열립니다.

    ![공유 추가](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. **공유 추가** 블레이드에서 다음을 수행합니다.
   
   1. **공유 이름** 필드에서 공유의 고유 이름을 입력합니다. 이름은 3~127개의 문자를 포함하는 문자열이어야 합니다.

   2. 공유에 대한 선택적 **설명**입니다. 설명은 공유 소유자를 식별하는 데 도움이 됩니다.

   3. **형식** 드롭다운 목록에서 **계층** 또는 **로컬로 고정** 공유를 만들지 여부를 지정합니다. 로컬 보증, 낮은 대기 시간 및 높은 성능이 필요한 워크로드의 경우 **로컬로 고정된 공유**를 선택합니다. 다른 모든 데이터에 대해서는 **계층화된** 공유를 선택합니다.

   4. **용량** 필드에서 공유의 크기를 지정합니다. 계층화된 공유는 500GB에서 20TB 사이여야 하고 로컬로 고정된 공유는 50GB에서 2TB 사이여야 합니다.

   5. **전체 기본 사용 권한 설정** 필드에서 공유에 액세스할 사용자 또는 그룹에 권한을 할당합니다. 사용자 또는 사용자 그룹의 이름을 _john@contoso.com_ 형식으로 지정합니다. 이 공유에 액세스하는 관리자 권한은 사용자 그룹(단일 사용자 대신)을 사용하여 허용하는 것이 좋습니다. 여기에서 권한을 할당한 후에 파일 탐색기를 사용하여 해당 권한을 수정할 수 있습니다.
3. 공유 구성을 완료했다면 **만들기**를 클릭합니다. 지정한 설정으로 공유가 만들어지면 알림이 표시됩니다. 기본적으로 공유에 대한 백업을 사용하도록 설정합니다.
4. 공유가 성공적으로 만들어졌는지 확인하려면 **공유** 블레이드로 이동합니다. 공유가 목록으로 표시되어야 합니다.
   
    ![공유 만들기 성공](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>공유 수정

공유에 대한 설명을 변경해야 하는 경우 공유를 수정합니다. 공유가 만들어지면 다른 공유 속성을 수정할 수 없습니다.

#### <a name="to-modify-a-share"></a>공유를 수정하려면

1. StorSimple 서비스 요약 블레이드의 **공유** 설정에서 수정하려는 공유가 상주하는 가상 배열을 선택합니다.
2. 공유를 **선택**하여 공유에 대한 현재 설명을 보고 수정합니다.
3. **저장** 명령 모음을 클릭하여 변경 사항을 저장합니다. 지정된 설정이 적용되고 알림이 표시됩니다.
   
    ![ 공유 편집](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>오프라인으로 공유 전환

공유를 수정 또는 삭제하려는 경우 공유를 오프라인으로 전환해야 할 수 있습니다. 공유가 오프라인 상태인 경우 읽기 전용 액세스를 사용할 수 없습니다. 디바이스에서뿐 아니라 호스트에서도 공유를 오프라인으로 전환해야 합니다.

#### <a name="to-take-a-share-offline"></a>오프라인으로 공유를 전환하려면

1. 오프라인으로 전환하기 전에 해당 공유를 사용 중이 아니어야 합니다.
2. 다음 단계를 수행하여 오프라인으로 배열의 공유를 전환합니다.
   
    1. StorSimple 서비스 요약 블레이드의 **공유** 설정에서 오프라인으로 전환하려는 공유가 상주하는 가상 배열을 선택합니다.

    2. 공유를 **선택**하고 **...**(또는 이 행의 오른쪽)를 클릭하고 상황에 맞는 메뉴에서 **오프라인으로 전환**을 선택합니다.
     
        ![오프라인 공유](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. **오프라인** 블레이드에서 정보를 검토하고 작업에 대한 동의를 확인합니다. **오프라인으로 전환**을 클릭하여 공유를 오프라인으로 전환합니다. 진행 중인 작업의 알림이 표시됩니다.

    4. 공유가 성공적으로 오프라인으로 전환되었는지 확인하려면 **공유** 블레이드로 이동합니다. 공유의 상태가 오프라인으로 표시됩니다.

## <a name="delete-a-share"></a>공유 삭제

> [!IMPORTANT]
> 오프라인 상태인 경우에만 공유를 삭제할 수 있습니다.


공유를 삭제하려면 다음 단계를 완료합니다.

#### <a name="to-delete-a-share"></a>공유를 삭제하려면

1. StorSimple 서비스 요약 블레이드의 **공유** 설정에서 삭제하려는 공유가 상주하는 가상 배열을 선택합니다.
2. 공유를 **선택**하고 **...**(또는 이 행의 오른쪽)를 클릭하고 상황에 맞는 메뉴에서 **삭제**를 선택합니다.
   
    ![공유 삭제](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. 삭제하려는 공유의 상태를 확인합니다. 삭제하려는 공유가 오프라인 상태가 아니면 먼저 오프라인으로 전환합니다. [오프라인으로 공유 전환](#take-a-share-offline) 단계를 따릅니다.
4. **삭제** 블레이드에서 확인하라는 메시지가 나타나면 확인을 수락하고 **삭제**를 클릭합니다. 이제 공유가 삭제되고 **공유** 블레이드가 가상 배열 내의 업데이트된 공유 목록을 보여줍니다.

## <a name="next-steps"></a>다음 단계
[StorSimple 공유를 복제](storsimple-virtual-array-clone.md)하는 방법에 대해 알아봅니다.

