---
title: StorSimple 디바이스 관리자 서비스에 대한 저장소 계정, 구독 마이그레이션 | Microsoft Docs
description: StorSimple 디바이스 관리자 service8000에 대한 저장소 계정, 구독을 마이그레이션하는 방법에 대해 알아봅니다.
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
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 3cce18fa1890fc9e518294e294cc43e0e55065aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631536"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>StorSimple 디바이스 관리자 서비스와 연결된 구독 및 저장소 계정 마이그레이션

새 등록이나 새 구독으로 StorSimple 서비스를 이동해야 합니다. 이러한 마이그레이션 시나리오는 계정 변경 또는 데이터 센터 변경입니다. 다음 표를 사용하여 이동하기 위한 자세한 단계를 포함하여 지원되는 시나리오를 이해합니다.

## <a name="account-changes"></a>계정 변경

| 이동할 수...| 지원됨| 가동 중지 시간| Azure 지원 프로세스| 접근 방식|
|-----|-----|-----|-----|-----|
| 다른 등록에 대한 전체 구독(StorSimple 서비스 및 저장소 계정 포함) | 예       | 아닙니다.       | **등록 이전**<br>다음 항목을 사용합니다.<li>새 규약에 따라 새 Azure 약정을 구입할 수 있습니다.</li><li>이전 등록에서 새 등록으로 모든 계정 및 구독을 마이그레이션하려고 합니다. 여기에는 이전 구독에서 사용한 Azure 서비스가 모두 포함됩니다.</li> | **1단계: Azure 엔터프라이즈 작업 지원 티켓을 여세요.**<li>[https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)로 이동합니다.</li><li> **등록 관리**를 선택한 후 **한 등록에서 새 등록으로 이전**을 선택합니다.<br>**2단계: 요청된 된 정보를 제공 합니다.**<br>다음 항목을 포함합니다.<li>원본 등록 번호</li><li> 대상 등록 번호</li><li>이전 개시 날짜|
| 기존 계정에서 새 등록으로 StorSimple 서비스 이동    | 예       | 아닙니다.       | **계정 이전**<br>사용:<li>전체 등록 이전을 원하지 않는 경우</li><li>특정 계정을 새 등록으로 이동하려고 합니다.</li>| **1단계: Azure 엔터프라이즈 작업 지원 티켓을 여세요.**<li>[https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)로 이동합니다.</li><li>**등록 관리**를 선택한 후 **EA 계정에서 새 등록으로 이전**을 선택합니다.<br>**2단계: 요청된 된 정보를 제공 합니다.**<br>다음 항목을 포함합니다.<li>원본 등록 번호</li><li> 대상 등록 번호</li><li>이전 개시 날짜|
| 한 구독에서 다른 구독으로 StorSimple 서비스 이동      | 아닙니다.        |    예         | 없음, 수동 프로세스|<li>StorSimple 디바이스에서 데이터를 마이그레이션합니다.</li><li>디바이스에서 디바이스 공장 재설정을 수행합니다. 그러면 모든 로컬 데이터가 삭제됩니다.</li><li>새 구독을 사용하여 StorSimple 디바이스 관리자 서비스로 디바이스를 등록합니다.</li><li>디바이스에 데이터를 다시 마이그레이션합니다.|
|Azure 구독의 소유권을 다른 디렉터리로 이전할 수 있나요? | 예       | 아닙니다.       | 기존 구독을 Azure AD 디렉터리에 연결 | [Azure AD 디렉터리에 기존 구독을 연결하려면](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) 문서를 참조하세요. 모든 항목이 제대로 표시되는 데 최대 10분이 걸릴 수 있습니다.|
| StorSimple 디바이스 관리자 서비스에서 다른 지역에 있는 다른 서비스로 StorSimple 디바이스 이동      | 아닙니다.        | 예            | 없음, 수동 프로세스 |위와 동일합니다.|
| 새 구독 또는 리소스 그룹의 저장소 계정     | 예        | 아닙니다.             |저장소 계정을 다른 구독 또는 리소스 그룹으로 이동 |이동 후 저장소 계정 액세스 키가 업데이트되면 사용자가 StorSimple 디바이스 관리자 서비스를 통해 마이그레이션된 저장소 계정에 대해 수동으로 액세스 키를 구성해야 합니다.|
| Azure Resource Manager 저장소 계정에 대한 클래식 저장소 계정      | 예        | 아닙니다.             |클래식에서 Azure Resource Manager로 마이그레이션 |<li>클래식에서 Azure Resource Manager로 저장소 계정을 마이그레이션하는 방법에 대한 자세한 지침은 [클래식 저장소 계정 마이그레이션](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-62-migrate-a-storage-account)을 참조하세요.</li><li> 마이그레이션 후 저장소 계정 액세스 키가 업데이트된 경우 사용자는 StorSimple 디바이스 관리자 서비스를 통해 마이그레이션된 저장소 계정에 대해 액세스 키를 동기화해야 합니다. 이것은 StorSimple 디바이스가 지속적으로 정상 작동하고 주/백업 데이터를 Azure로 계층화할 수 있게 하기 위한 것입니다. 액세스 키 동기화에 대한 자세한 지침은 [회전 워크플로](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)를 참조하세요.</li><li> StorSimple Cloud Appliance의 경우 클래식 저장소 계정이 마이그레이션되었으나 기본 가상 머신이 여전히 클래식 상태를 유지하면 해당 어플라이언스가 올바르게 작동해야 합니다. 클라우드 어플라이언스에 대한 기본 가상 컴퓨터가 마이그레이션된 경우 비활성화 및 삭제 기능이 작동하지 않습니다.</li><li> Azure Portal에 새 StorSimple Cloud Appliances를 만든 다음 기존 클라우드 어플라이언스에서 장애 조치(failo ver)해야 합니다. 클래식 저장소 계정을 사용하여 새 Azure Portal에서 StorSimple Cloud Appliance를 만들 수는 없습니다. Azure Resource Manager 저장소 계정이 필요합니다. 자세한 내용을 보려면 [StorSimple Cloud Appliance 배포 및 관리](storsimple-8000-cloud-appliance-u2.md)로 이동하세요.</li>|

## <a name="datacenter-changes"></a>데이터 센터 변경

| 이동할 수...| 지원됨|가동 중지 시간| Azure 지원 프로세스| 접근 방식|
|-----|-----|-----|-----|-----|
| Azure 데이터 센터 간에 StorSimple 서비스 이동 | 아닙니다. | 예 |없음, 수동 프로세스  |<li>StorSimple 디바이스에서 데이터를 마이그레이션합니다.</li><li>디바이스에서 디바이스 공장 재설정을 수행합니다. 그러면 모든 로컬 데이터가 삭제됩니다.</li><li>새 구독을 사용하여 새 StorSimple 디바이스 관리자 서비스로 디바이스를 등록합니다.</li><li>디바이스에 데이터를 다시 마이그레이션합니다.|
| Azure 데이터 센터 간에 저장소 계정 이동 | 아닙니다. |예  |없음, 수동 프로세스  | 위와 동일합니다.|

## <a name="next-steps"></a>다음 단계

* [StorSimple 디바이스 관리자 서비스 배포](storsimple-8000-manage-service.md)
* [Azure Portal에서 StorSimple 8000 시리즈 디바이스 배포](storsimple-8000-deployment-walkthrough-u2.md)
