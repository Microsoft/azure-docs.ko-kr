---
title: Azure Import/Export에 대한 내보내기 작업 만들기 | Microsoft Docs
description: Microsoft Azure Import/Export 서비스에 대해 내보내기 작업을 만드는 방법을 알아봅니다.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: e0513bc18f1cf14beb4c1becfc1835235a5ddc96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483097"
---
# <a name="creating-an-export-job-for-the-azure-importexport-service"></a>Azure Import/Export 서비스에 대한 내보내기 작업 만들기
REST API를 사용하여 Microsoft Azure Import/Export 서비스에 대해 내보내기 작업을 만드는 단계는 다음과 같습니다.

- 내보낼 blob 선택

- 배송 위치 가져오기

- 내보내기 작업 만들기

- 지원되는 운송업체 서비스를 통해 Microsoft에 빈 드라이브 배송

- 패키지 정보를 사용하여 내보내기 작업 업데이트

- Microsoft에서 드라이브 다시 받기

  Import/Export 서비스에 대한 개요 및 [Azure Portal](https://portal.azure.com/)을 사용하여 가져오기 및 내보내기 작업을 만들고 관리하는 방법을 보여 주는 자습서는 [Microsoft Azure Import/Export 서비스를 사용하여 데이터를 Blob Storage로 전송](storage-import-export-service.md)을 참조하세요.

## <a name="selecting-blobs-to-export"></a>내보낼 blob 선택
 내보내기 작업을 만들려면 저장소 계정에서 내보낼 blob 목록을 제공해야 합니다. 다음과 같은 방법으로 내보낼 blob을 선택할 수 있습니다.

- 상대 blob 경로를 사용하여 단일 blob 및 해당 스냅숏을 모두 선택할 수 있습니다.

- 상대 blob 경로를 사용하여 단일 blob을 선택하고 해당 스냅숏은 제외할 수 있습니다.

- 상대 blob 경로 및 스냅숏 시간을 사용하여 단일 스냅숏을 선택할 수 있습니다.

- blob 접두사를 사용하여 지정된 접두사를 갖는 모든 blob 및 스냅숏을 선택할 수 있습니다.

- 저장소 계정의 모든 blob 및 스냅숏을 내보낼 수 있습니다.

  내보낼 blob을 지정하는 방법에 대한 자세한 내용은 [작업 배치](/rest/api/storageimportexport/jobs) 연산을 참조하세요.

## <a name="obtaining-your-shipping-location"></a>배송 위치 가져오기
내보내기 작업을 만들기 전에 [위치 가져오기](https://portal.azure.com) 또는 [위치 나열](https://docs.microsoft.com/rest/api/storageimportexport/locations/list) 연산을 호출하여 배송 위치 이름 및 주소를 가져와야 합니다. `List Locations`는 위치 목록과 해당 우편 발송 주소 목록을 반환합니다. 반환된 목록에서 위치를 선택하고 해당 주소로 하드 드라이브 배송할 수 있습니다. `Get Location` 연산을 사용하여 특정 위치에 대한 배송 주소를 직접 얻을 수도 있습니다.

배송 위치를 가져오려면 다음 단계를 수행합니다.

-   저장소 계정의 위치 이름을 식별합니다. 이 값은 Azure Portal에서 Storage 계정의 **대시보드**에 있는 **위치** 필드에서 찾거나 Service Management API 연산 [Storage 계정 속성 가져오기](/rest/api/storagerp/storageaccounts)를 사용하여 쿼리할 수 있습니다.

-   `Get Location` 연산을 호출하여 이 저장소 계정을 처리하는 데 사용할 수 있는 위치를 검색합니다.

-   위치의 `AlternateLocations` 속성에 해당 위치 자체가 포함될 경우 이 위치를 사용해도 됩니다. 그렇지 않으면 대체 위치 중 하나를 사용하여 `Get Location` 연산을 다시 호출합니다. 유지 관리를 위해 원래 위치가 일시적으로 닫혀 있을 수 있습니다.

## <a name="creating-the-export-job"></a>내보내기 작업 만들기
 내보내기 작업을 만들려면 [작업 배치](/rest/api/storageimportexport/jobs) 연산을 호출합니다. 다음 정보를 제공해야 합니다.

-   작업의 이름입니다.

-   저장소 계정 이름입니다.

-   이전 단계에서 얻은 배송 위치 이름입니다.

-   작업 유형(내보내기)입니다.

-   내보내기 작업이 완료된 후 드라이브를 보낼 반환 주소입니다.

-   내보낼 blob(또는 blob 접두사) 목록입니다.

## <a name="shipping-your-drives"></a>드라이브 배송
 다음으로 Azure Import/Export 도구를 사용하여 내보내려고 선택한 blob 및 드라이브 크기에 따라 전송하려는 드라이브 수를 결정합니다. 자세한 내용은 [Azure Import/Export 도구 참조](storage-import-export-tool-how-to-v1.md)를 참조하세요.

 드라이브를 단일 패키지로 포장한 다음 이전 단계에서 얻은 주소로 배송합니다. 다음 단계를 위해 패키지의 추적 번호를 적어둡니다.

> [!NOTE]
>  패키지의 추적 번호를 제공하는 지원 운송업체 서비스를 통해 드라이브를 배송해야 합니다.

## <a name="updating-the-export-job-with-your-package-information"></a>패키지 정보를 사용하여 내보내기 작업 업데이트
 추적 번호가 있으면 [작업 속성 업데이트](/rest/api/storageimportexport/jobs) 연산을 호출하여 배송업체 이름과 작업의 추적 번호를 업데이트합니다. 경우에 따라 드라이브 개수, 반송 주소 및 배송 날짜를 지정할 수도 있습니다.

## <a name="receiving-the-package"></a>패키지 받기
 내보내기 작업이 처리된 후 암호화된 데이터와 함께 드라이브가 반환됩니다. [작업 가져오기](/rest/api/storageimportexport/jobs) 연산을 호출하여 각 드라이브의 BitLocker 키를 검색할 수 있습니다. 그런 다음 이 키를 사용하여 드라이브 잠금을 해제할 수 있습니다. 각 드라이브에 있는 드라이브 매니페스트 파일에는 드라이브의 파일 목록과 각 파일의 원래 blob 주소도 포함됩니다.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>다음 단계

* [Import/Export 서비스 REST API 사용](storage-import-export-using-the-rest-api.md)
