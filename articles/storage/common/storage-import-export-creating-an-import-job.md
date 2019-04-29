---
title: Azure Import/Export에 대한 내보내기 작업 만들기 | Microsoft Docs
description: Microsoft Azure Import/Export 서비스에 대해 가져오기 작업을 만드는 방법을 알아봅니다.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: fa76f4fb5d4da5fd00bb9fa4ed862c6977a47e90
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483080"
---
# <a name="creating-an-import-job-for-the-azure-importexport-service"></a>Azure Import/Export 서비스에 대한 가져오기 작업 만들기

REST API를 사용하여 Microsoft Azure Import/Export 서비스에 대해 가져오기 작업을 만드는 단계는 다음과 같습니다.

- Azure Import/Export 도구로 드라이브 준비

- 드라이브를 배송할 위치 가져오기

- 가져오기 작업 만들기

- 지원되는 운송업체 서비스를 통해 Microsoft에 드라이브 배송

- 배송 정보로 가져오기 작업 업데이트

  Import/Export 서비스에 대한 개요 및 [Azure Portal](https://portal.azure.com/)을 사용하여 가져오기 및 내보내기 작업을 만들고 관리하는 방법을 보여 주는 자습서는 [Microsoft Azure Import/Export 서비스를 사용하여 데이터를 Blob Storage로 전송](storage-import-export-service.md)을 참조하세요.

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>Azure Import/Export 도구로 드라이브 준비

가져오기 작업을 위해 드라이브를 준비 하는 단계는 포털 또는 REST API를 통해 작업을 만들 수 있는지 여부를 동일 합니다.

다음은 드라이브 준비에 대한 간단한 설명입니다. 자세한 지침은 [Azure Import-Export 도구 참조](storage-import-export-tool-how-to-v1.md)를 참조하세요. [여기](https://go.microsoft.com/fwlink/?LinkID=301900)에서 Azure Import/Export 도구를 다운로드할 수 있습니다.

드라이브를 준비하는 과정은 다음과 같습니다.

- 가져올 데이터 식별

- Microsoft Azure Storage에서 대상 blob 식별

- Azure Import/Export 도구를 사용하여 하나 이상의 하드 드라이브로 데이터 복사

  또한 Azure Import/Export 도구는 드라이브가 준비되면 각 드라이브에 대해 매니페스트 파일도 생성합니다. 매니페스트 파일에는 다음이 포함됩니다.

- 업로드한 모든 파일의 열거형과 이러한 파일에서 blob으로의 매핑

- 각 파일의 세그먼트 체크섬

- 각 blob에 연결될 속성 및 메타데이터에 대한 정보

- 업로드될 blob이 컨테이너의 기존 blob과 이름이 같은 경우 수행할 작업 목록 가능한 옵션: a) blob을 파일로 덮어쓰기 b) 기존 blob을 유지하고 파일 업로드 건너뛰기 c) 다른 파일과 충돌하지 않게 이름에 접미사 추가

## <a name="obtaining-your-shipping-location"></a>배송 위치 가져오기

가져오기 작업을 만들기 전에 [위치 나열](https://docs.microsoft.com/rest/api/storageimportexport/locations/list) 연산을 호출하여 배송 위치 이름 및 주소를 가져와야 합니다. `List Locations`는 위치 목록과 해당 우편 발송 주소 목록을 반환합니다. 반환된 목록에서 위치를 선택하고 해당 주소로 하드 드라이브 배송할 수 있습니다. `Get Location` 연산을 사용하여 특정 위치에 대한 배송 주소를 직접 얻을 수도 있습니다.

 배송 위치를 가져오려면 다음 단계를 수행합니다.

-   저장소 계정의 위치 이름을 식별합니다. 이 값은 Azure Portal에서 Storage 계정의 **대시보드**에 있는 **위치** 필드에서 찾거나 Service Management API 연산 [Storage 계정 속성 가져오기](/rest/api/storagerp/storageaccounts)를 사용하여 쿼리할 수 있습니다.

-   `Get Location` 연산을 호출하여 이 저장소 계정을 처리하는 데 사용할 수 있는 위치를 검색합니다.

-   위치의 `AlternateLocations` 속성에 해당 위치 자체가 포함될 경우 이 위치를 사용해도 됩니다. 그렇지 않으면 대체 위치 중 하나를 사용하여 `Get Location` 연산을 다시 호출합니다. 유지 관리를 위해 원래 위치가 일시적으로 닫혀 있을 수 있습니다.

## <a name="creating-the-import-job"></a>가져오기 작업 만들기
가져오기 작업을 만들려면 [작업 배치](/rest/api/storageimportexport/jobs) 연산을 호출합니다. 다음 정보를 제공해야 합니다.

-   작업의 이름입니다.

-   저장소 계정 이름입니다.

-   이전 단계에서 얻은 배송 위치 이름입니다.

-   작업 유형(가져오기)입니다.

-   가져오기 작업이 완료된 후 드라이브를 보낼 반환 주소입니다.

-   작업의 드라이브 목록입니다. 각 드라이브에 대해 드라이브 준비 단계 중에 획득한 다음 정보를 포함해야 합니다.

    -   드라이브 ID

    -   BitLocker 키

    -   하드 드라이브의 매니페스트 파일 상대 경로

    -   Base16 인코딩 매니페스트 파일 MD5 해시

## <a name="shipping-your-drives"></a>드라이브 배송
이전 단계에서 획득한 주소로 드라이브를 배송해야 하고, Import/Export 서비스에 패키지의 추적 번호를 제공해야 합니다.

> [!NOTE]
>  패키지의 추적 번호를 제공하는 지원 운송업체 서비스를 통해 드라이브를 배송해야 합니다.

## <a name="updating-the-import-job-with-your-shipping-information"></a>배송 정보로 가져오기 작업 업데이트
추적 번호가 있으면 [작업 속성 업데이트](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Update) 연산을 호출하여 배송업체 이름, 작업의 추적 번호 및 반송을 위한 배송업체 계정 번호를 업데이트합니다. 경우에 따라 드라이브 개수 및 배송 날짜를 지정할 수도 있습니다.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>다음 단계

* [Import/Export 서비스 REST API 사용](storage-import-export-using-the-rest-api.md)
