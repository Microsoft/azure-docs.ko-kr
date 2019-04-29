---
title: 스키마를 사용한 XML 유효성 검사 - Azure Logic Apps | Microsoft Docs
description: 엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 XML 문서 유효성을 검사하는 스키마 추가
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.date: 02/06/2019
ms.openlocfilehash: 3cca995b353b88cc481cbda68df4211a724f7f09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60846374"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 스키마로 XML 유효성 검사

문서가 유효한 XML을 사용하고 있으며 예상된 데이터가 Azure Logic Apps에서 엔터프라이즈 통합 시나리오에 맞는 미리 정의된 형식으로 존재하는지 확인하기 위해 논리 앱은 스키마를 사용할 수 있습니다. 또한 스키마는 논리 앱이 B2B(기업 간) 시나리오에서 교환하는 메시지가 유효한지도 확인할 수 있습니다.

통합 계정 및 아티팩트(예: 스키마)와 관련된 제한에 대해서는 [Azure Logic Apps에 대한 제한 및 구성 정보](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">Azure 체험 계정에 등록</a>합니다.

* [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md): 엔터프라이즈 통합 및 B2B(기업 간) 솔루션을 위한 스키마 및 기타 아티팩트를 저장합니다. 

  스키마가 [2MB 이하](#smaller-schema)이면 Azure Portal에서 직접 통합 계정에 스키마를 추가할 수 있습니다. 그러나 스키마가 2MB보다 크고 [스키마 크기 제한](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)보다 작으면 Azure Storage 계정에 스키마를 업로드할 수 있습니다. 
  통합 계정에 해당 스키마를 추가하려면 통합 계정에서 스토리지 계정으로 연결할 수 있습니다. 
  이 작업을 위해 필요한 항목은 다음과 같습니다. 

  * [Azure Storage 계정](../storage/common/storage-account-overview.md): 스키마에 대한 blob 컨테이너를 만듭니다. [스토리지 계정을 만드는](../storage/common/storage-quickstart-create-account.md) 방법을 알아봅니다. 

  * 스키마를 저장하기 위한 blob 컨테이너. [blob 컨테이너를 만드는](../storage/blobs/storage-quickstart-blobs-portal.md) 방법을 알아봅니다. 
  나중에 통합 계정에 스키마를 추가할 때 컨테이너의 콘텐츠 URI가 필요합니다.

  * [Azure Storage 탐색기](../vs-azure-tools-storage-manage-with-storage-explorer.md): 스토리지 계정 및 blob 컨테이너를 관리하는 데 사용할 수 있습니다. 
  Storage 탐색기를 사용하려면 다음 옵션 중 하나를 선택합니다.
  
    * Azure Portal에서 스토리지 계정을 선택합니다. 
    스토리지 계정 메뉴에서 **Storage 탐색기**를 선택합니다.

    * 데스크톱 버전의 경우 [Azure Storage 탐색기를 다운로드 및 설치](https://www.storageexplorer.com/)합니다. 
    그런 다음, [Storage 탐색기 시작](../vs-azure-tools-storage-manage-with-storage-explorer.md)의 단계에 따라 Storage 탐색기를 스토리지 계정에 연결합니다. 
    자세한 내용은 [빠른 시작: Azure Storage 탐색기를 사용하여 개체 스토리지에 Blob 만들기](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

스키마를 만들고 추가할 때 논리 앱은 필요하지 않습니다. 그러나 스키마를 사용하려면 논리 앱에서 해당 스키마를 저장하는 통합 계정에 연결해야 합니다. [논리 앱을 통합 계정에 연결하는 방법](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)을 알아봅니다. 논리 앱이 아직 없는 경우 [논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 알아봅니다.

## <a name="add-schemas"></a>스키마 추가

1. Azure 계정 자격 증명을 사용하여 <a href="https://portal.azure.com" target="_blank">Azure Portal</a>에 로그인합니다.

1. 통합 계정을 찾아서 열려면 Azure 주 메뉴에서 **모든 서비스**를 선택합니다. 검색 상자에 "통합 계정"을 입력합니다. **통합 계정**을 선택합니다.

   ![통합 계정 찾기](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. 스키마를 추가할 통합 계정을 선택합니다. 예를 들면 다음과 같습니다.

   ![통합 계정 선택](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. 통합 계정의 **개요** 페이지에 있는 **구성 요소**에서 **스키마** 타일을 선택합니다.

   !["스키마" 선택](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. **스키마** 페이지가 열리면 **추가**를 선택합니다.

   !["추가" 선택](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

스키마(.xsd) 파일의 크기에 따라 [2MB보다 작거나](#smaller-schema) [2MB보다 크고](#larger-schema) 8MB보다 작은 스키마를 업로드하는 단계를 따릅니다.

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>2MB보다 작은 스키마 추가

1. **스키마 추가** 아래에 스키마의 이름을 입력합니다. 
   **작은 파일**을 선택한 상태로 둡니다. **스키마** 상자 옆에서 폴더 아이콘을 선택합니다. 업로드하려는 스키마를 찾아 선택합니다. 예를 들면 다음과 같습니다.

   ![더 작은 스키마 업로드](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. 준비가 되면 **확인**을 선택합니다.

   스키마 업로드가 완료되면 **스키마** 목록에 스키마가 나타납니다.

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>2MB보다 큰 스키마 추가

더 큰 스키마를 추가하려면 Azure Storage 계정의 Azure blob 컨테이너에 스키마를 업로드할 수 있습니다. Blob 컨테이너에 공용 읽기 액세스 권한이 있는지 여부를 스키마를 추가 하기 위한 단계에 따라 달라 집니다. 먼저 다음 단계를 수행하여 blob 컨테이너에 공용 읽기 액세스 권한이 있는지 여부를 확인합니다. [Blob 컨테이너에 대한 공용 액세스 수준 설정](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>컨테이너 액세스 수준 확인

1. Azure Storage 탐색기를 엽니다. 탐색기 창에서 Azure 구독을 아직 확장하지 않은 경우 확장합니다.

1. **Storage 계정** > {*your-storage-account*} > **Blob 컨테이너**를 확장합니다. Blob 컨테이너를 선택합니다.

1. Blob 컨테이너의 바로 가기 메뉴에서 **공용 액세스 수준 설정**을 선택합니다.

   * Blob 컨테이너에 최소한 공용 액세스 권한이 있는 경우 **취소**를 선택하고 이 페이지 뒷부분에 나오는 다음 단계를 따릅니다. [공용 액세스 권한이 있는 컨테이너에 업로드](#public-access)

     ![공용 액세스](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Blob 컨테이너에 공용 액세스 권한이 없는 경우 **취소**를 선택하고 이 페이지 뒷부분에 나오는 다음 단계를 따릅니다. [공용 액세스 권한이 없는 컨테이너에 업로드](#public-access)

     ![공용 액세스 권한 없음](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>공용 액세스 권한이 있는 컨테이너에 업로드

1. 저장소 계정에 스키마를 업로드합니다. 
   오른쪽 창에서 **업로드**를 선택합니다.

1. 업로드가 완료되면 업로드된 스키마를 선택합니다. 도구 모음에서 스키마 URL을 복사할 수 있도록 **URL 복사**를 선택합니다.

1. **스키마 추가** 창이 열려 있는 Azure Portal로 돌아갑니다. 
   어셈블리의 이름을 입력합니다. 
   **대용량 파일(2MB 초과)** 을 선택합니다. 

   이제 **스키마** 상자가 아닌 **콘텐츠 URI** 상자가 나타납니다.

1. **콘텐츠 URI** 상자에서 스키마의 URL을 붙여 넣습니다. 
   스키마 추가를 완료합니다.

스키마 업로드가 완료되면 **스키마** 목록에 스키마가 나타납니다. 통합 계정의 **개요** 페이지에 있는 **구성 요소**에서 **스키마** 타일에는 이제 업로드된 스키마의 수가 표시됩니다.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>공용 액세스 권한이 없는 컨테이너에 업로드

1. 저장소 계정에 스키마를 업로드합니다. 
   오른쪽 창에서 **업로드**를 선택합니다.

1. 업로드를 마친 후에 스키마의 SAS(공유 액세스 서명)을 생성합니다. 
   스키마의 바로 가기 메뉴에서 **공유 액세스 서명 가져오기**를 선택합니다.

1. **공유 액세스 서명** 창에서 **컨테이너 수준 공유 액세스 서명 URI 생성** > **만들기**를 선택합니다. 
   SAS URL이 생성되면 **URL** 상자 옆에서 **복사**를 선택합니다.

1. **스키마 추가** 창이 열려 있는 Azure Portal로 돌아갑니다. **대용량 파일**을 선택합니다.

   이제 **스키마** 상자가 아닌 **콘텐츠 URI** 상자가 나타납니다.

1. **콘텐츠 URI** 상자에서 이전에 생성한 SAS URI를 붙여 넣습니다. 스키마 추가를 완료합니다.

스키마 업로드가 완료되면 **스키마** 목록에 스키마가 나타납니다. 통합 계정의 **개요** 페이지에 있는 **구성 요소**에서 **스키마** 타일에는 이제 업로드된 스키마의 수가 표시됩니다.

## <a name="edit-schemas"></a>스키마 편집

기존 스키마를 업데이트하려면 원하는 변경 내용이 있는 새 스키마 파일을 업로드해야 합니다. 그러나 편집을 위해 기존 스키마를 먼저 다운로드할 수 있습니다.

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a>에서 통합 계정이 아직 열려 있지 않으면 찾아서 엽니다.

1. Azure 주 메뉴에서 **모든 서비스**를 선택합니다. 
   검색 상자에 "통합 계정"을 입력합니다. 
   **통합 계정**을 선택합니다.

1. 스키마를 업데이트할 통합 계정을 선택합니다.

1. 통합 계정의 **개요** 페이지에 있는 **구성 요소**에서 **스키마** 타일을 선택합니다.

1. **스키마** 페이지가 열리면 스키마를 선택합니다. 
   스키마를 먼저 다운로드한 후 편집하려면 **다운로드**를 선택하고 스키마를 저장합니다.

1. 업데이트된 스키마를 업로드할 준비가 되면 **스키마** 페이지에서 업데이트하려는 스키마를 선택하고 **업데이트**를 선택합니다.

1. 업로드하려는 업데이트된 스키마를 찾아 선택합니다. 
   스키마 파일의 업로드가 끝나면 업데이트된 스키마가 **스키마** 목록에 나타납니다.

## <a name="delete-schemas"></a>스키마 삭제

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a>에서 통합 계정이 아직 열려 있지 않으면 찾아서 엽니다.

1. Azure 주 메뉴에서 **모든 서비스**를 선택합니다. 
   검색 상자에 "통합 계정"을 입력합니다. 
   **통합 계정**을 선택합니다.

1. 스키마를 삭제할 통합 계정을 선택합니다.

1. 통합 계정의 **개요** 페이지에 있는 **구성 요소**에서 **스키마** 타일을 선택합니다.

1. **스키마** 페이지가 열리면 스키마를 선택하고 **삭제**를 선택합니다.

1. 스키마를 삭제할지 확인하려면 **예**를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [엔터프라이즈 통합 팩에 대해 자세히 알아보기](logic-apps-enterprise-integration-overview.md)
* [맵에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [변환에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-transform.md)
