---
title: Azure Blob Storage에 연결
description: Azure Logic Apps를 사용 하 여 Azure storage 계정에서 blob 만들기 및 관리
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 10/28/2019
tags: connectors
ms.openlocfilehash: 86e8415cf2076819e23226e5e7878a2c96343f69
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789926"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용 하 여 Azure Blob Storage에서 blob 만들기 및 관리

이 문서에서는 Azure Blob Storage 커넥터를 사용하여 논리 앱 내에서 Azure 스토리지 계정의 Blob으로 저장된 파일에 액세스하고 관리하는 방법을 보여줍니다. 이러한 방식으로 파일을 관리하는 작업 및 워크플로를 자동화하는 논리 앱을 만들 수 있습니다. 예를 들어 스토리지 계정에서 파일을 만들고, 가져오고, 업데이트하고, 삭제하는 논리 앱을 빌드할 수 있습니다.

Azure 웹 사이트에서 업데이트되는 도구가 있다고 가정해 보겠습니다. 이 도구는 논리 앱에 대한 트리거의 역할을 합니다. 이 이벤트가 발생하면 논리 앱에서 Blob Storage 컨테이너의 일부 파일을 업데이트하도록 할 수 있습니다. 이는 논리 앱의 작업입니다.

논리 앱을 처음 사용하는 경우 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토합니다. 커넥터 관련 기술 정보는 [Azure Blob Storage 커넥터 참조](https://docs.microsoft.com/connectors/azureblobconnector/)를 참조하세요.

> [!IMPORTANT]
> Azure Logic Apps에서 방화벽 뒤에 있는 저장소 계정에 액세스할 수 있도록 하려면이 항목의 뒷부분에 있는 [방화벽 뒤에 있는 저장소 계정 액세스](#storage-firewalls) 섹션을 참조 하세요.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Limits

* 기본적으로 Azure Blob Storage 작업은 *50 MB 미만의*파일을 읽거나 쓸 수 있습니다. 50 MB 보다 큰 파일을 처리 하기 위해 최대 1024 MB까지 Azure Blob Storage 작업은 [메시지 청크](../logic-apps/logic-apps-handle-large-messages.md)를 지원 합니다. **Blob 콘텐츠 가져오기** 작업은 청크를 암시적으로 사용 합니다.

* Azure Blob Storage 트리거는 청크를 지원 하지 않습니다. 파일 콘텐츠를 요청 하는 경우 트리거는 50 MB 미만의 파일만 선택 합니다. 50MB보다 큰 파일을 가져오려면 다음 패턴을 따릅니다.

  * **Blob이 추가 되거나 수정**되는 경우와 같이 파일 속성을 반환 하는 Azure Blob Storage 트리거를 사용 합니다 (속성만).

  * 전체 파일을 읽고 청크를 암시적으로 사용 하는 Azure Blob Storage **Blob 콘텐츠 가져오기** 작업을 사용 하 여 트리거를 수행 합니다.

## <a name="prerequisites"></a>전제 조건

* Azure 구독. Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [Azure Storage 계정 및 스토리지 컨테이너](../storage/blobs/storage-quickstart-blobs-portal.md)

* Azure Blob Storage 계정에 액세스해야 하는 논리 앱 Azure Blob Storage 트리거를 통해 논리 앱을 시작하려면 [빈 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)이 필요합니다.

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Blob Storage 트리거 추가

Azure Logic Apps에서 모든 논리 앱은 특정 이벤트가 발생하거나 특정 조건이 충족할 때 실행되는 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 통해 시작되어야 합니다. 트리거가 실행될 때마다 Logic Apps 엔진에서 논리 앱 인스턴스를 만들고 앱의 워크플로를 실행하기 시작합니다.

이 예제에서는 blob의 속성이 저장소 컨테이너에서 추가 되거나 업데이트 될 때 **blob이 추가 되거나 수정 될 때 (속성만)** 트리거를 사용 하 여 논리 앱 워크플로를 시작 하는 방법을 보여 줍니다.

1. [Azure Portal](https://portal.azure.com) 또는 Visual Studio에서 논리 앱 디자이너를 여는 빈 논리 앱을 만듭니다. 이 예에서는 Azure Portal을 사용합니다.

2. 검색 상자에서 "azure blob"을 필터로 입력합니다. 트리거 목록에서 원하는 트리거를 선택합니다.

   이 예제에서는 **blob이 추가 되거나 수정 될 때 (속성만)** 이 트리거를 사용 합니다.

   ![트리거 Azure Blob Storage 선택](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. 연결 세부 정보를 묻는 메시지가 표시되면 [이제 Blob Storage 연결을 만듭니다](#create-connection). 또는 연결이 이미 존재하는 경우 트리거에 대한 필요한 정보를 제공합니다.

   이 예에서는 모니터링하려는 컨테이너 및 폴더를 선택합니다.

   1. **컨테이너** 상자에서 폴더 아이콘을 선택합니다.

   2. 폴더 목록에서 오른쪽 괄호( **>** )를 선택한 다음, 원하는 폴더를 찾고 선택할 때까지 찾습니다.

      ![트리거와 함께 사용할 저장소 폴더 선택](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. 트리거에서 변경 내용에 대한 폴더를 확인하려는 간격 및 빈도를 선택합니다.

4. 완료되면 디자이너 도구 모음에서 **저장**을 선택합니다.

5. 이제 트리거 결과와 함께 수행하려는 작업에 대한 논리 앱에 하나 이상의 작업을 계속해서 추가합니다.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Blob Storage 작업 추가

Azure Logic Apps에서 [작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)은 트리거 또는 다른 작업을 수행하는 워크플로의 한 단계입니다. 이 예제의 경우 논리 앱은 [되풀이 트리거](../connectors/connectors-native-recurrence.md)로 시작합니다.

1. [Azure Portal](https://portal.azure.com) 또는 Visual Studio의 논리 앱 디자이너에서 논리 앱을 엽니다. 이 예에서는 Azure Portal을 사용합니다.

2. 논리 앱 디자이너의 트리거 또는 작업에서 **새 단계**를 선택 합니다.

   ![논리 앱 워크플로에 새 단계 추가](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   기존 단계 간에 작업을 추가하려면 연결 화살표 위로 마우스를 이동합니다. 표시 되는 더하기 기호 ( **+** )를 선택 하 고 **작업 추가**를 선택 합니다.

3. 검색 상자에서 "azure blob"을 필터로 입력합니다. 작업 목록에서 원하는 작업을 선택합니다.

   이 예제에서는 **blob 콘텐츠 가져오기** 작업을 사용 합니다.

   ![Azure Blob Storage 작업 선택](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. 연결 세부 정보를 묻는 메시지가 표시되면 [이제 Azure Blob Storage 연결을 만듭니다](#create-connection).
또는 연결이 이미 존재하는 경우 작업에 대한 필요한 정보를 제공합니다.

   이 예제의 경우 원하는 파일을 선택합니다.

   1. **Blob** 상자에서 폴더 아이콘을 선택합니다.
  
      ![작업에 사용할 저장소 폴더 선택](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Blob의 **ID** 번호를 기준으로 원하는 파일을 찾아서 선택 합니다. 이전에 설명한 blob storage 트리거에서 반환 하는 blob의 메타 데이터에서이 **ID** 번호를 찾을 수 있습니다.

5. 완료되면 디자이너 도구 모음에서 **저장**을 선택합니다.
논리 앱을 테스트하려면 선택한 폴더에 Blob이 포함되어 있는지 확인합니다.

이 예제에서는 Blob에 대한 콘텐츠만을 가져옵니다. 콘텐츠를 보려면 다른 커넥터를 사용하여 Blob으로 파일을 만드는 다른 작업을 추가합니다. 예를 들어 Blob 콘텐츠에 따라 파일을 만드는 OneDrive 작업을 추가합니다.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>스토리지 계정에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 연결을 만들지 묻는 메시지가 표시 되 면 다음 정보를 제공 합니다.

   | 자산 | 필수 | Value | 설명 |
   |----------|----------|-------|-------------|
   | **연결 이름** | yes | <*connection-name*> | 연결에 만들 이름 |
   | **Storage 계정** | yes | <*storage-account*> | 목록에서 스토리지 계정을 선택합니다. |
   ||||

   다음은 그 예입니다.

   ![Azure Blob storage 계정 연결 만들기](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png)  

1. 준비가 되 면 **만들기** 를 선택 합니다.

1. 연결을 만든 후 [blob 저장소 트리거 추가](#add-trigger) 또는 [blob 저장소 추가 작업](#add-action)을 계속 합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Open API (이전의 Swagger) 파일에 설명 된 대로 트리거, 작업 및 제한과 같은 기술 세부 정보는 [커넥터의 참조 페이지](https://docs.microsoft.com/connectors/azureblobconnector/)를 참조 하세요.

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>방화벽 뒤에 저장소 계정 액세스

[방화벽 및 방화벽 규칙](../storage/common/storage-network-security.md)을 사용 하 여 액세스를 제한 하 여 Azure storage 계정에 네트워크 보안을 추가할 수 있습니다. 그러나이 설치 프로그램은 저장소 계정에 액세스 해야 하는 Azure 및 기타 Microsoft 서비스에 대한 챌린지를 만듭니다. 데이터 센터의 로컬 통신은 내부 IP 주소를 추상화 하므로 IP 제한을 사용 하 여 방화벽 규칙을 설정할 수 없습니다. 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../storage/common/storage-network-security.md)을 참조하세요.

Azure Blob Storage 커넥터 또는 다른 솔루션을 사용 하 여 Azure Logic Apps에서 방화벽 뒤에 있는 저장소 계정에 액세스 하기 위한 다양 한 옵션은 다음과 같습니다.

* Azure Storage Blob 커넥터

  * [다른 지역의 저장소 계정에 액세스](#access-other-regions)
  * [신뢰할 수 있는 가상 네트워크를 통해 저장소 계정 액세스](#access-trusted-virtual-network)

* 기타 솔루션

  * [관리 id를 사용 하 여 신뢰할 수 있는 서비스로 저장소 계정 액세스](#access-trusted-service)
  * [Azure API Management를 통해 저장소 계정에 액세스](#access-api-management)

<a name="access-other-regions"></a>

### <a name="access-to-storage-accounts-in-other-regions"></a>다른 지역의 저장소 계정에 대한 액세스

논리 앱은 방화벽 규칙이 있고 동일한 지역에 있는 저장소 계정에 직접 액세스할 수 없습니다. 그러나 [지역에서 관리 되는 커넥터의 아웃 바운드 IP 주소](../logic-apps/logic-apps-limits-and-config.md#outbound)에 대한 액세스를 허용 하는 경우 azure Table Storage 커넥터 또는 azure Queue Storage 커넥터를 사용 하는 경우를 제외 하 고 논리 앱은 다른 지역의 저장소 계정에 액세스할 수 있습니다. Table Storage 또는 Queue Storage에 액세스 하려면 기본 제공 HTTP 트리거 및 작업을 계속 사용할 수 있습니다.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>신뢰할 수 있는 가상 네트워크를 통해 저장소 계정 액세스

관리 하는 Azure 가상 네트워크에 저장소 계정을 넣은 다음 해당 가상 네트워크를 신뢰할 수 있는 가상 네트워크 목록에 추가할 수 있습니다. 논리 앱이 신뢰할 수 있는 [가상 네트워크](../virtual-network/virtual-networks-overview.md)를 통해 저장소 계정에 액세스 하도록 하려면 가상 네트워크의 리소스에 연결할 수 있는 [ISE (integration service environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에 해당 논리 앱을 배포 해야 합니다. 그런 다음 해당 ISE의 서브넷을 신뢰할 수 있는 목록에 추가할 수 있습니다. Blob Storage 커넥터와 같은 Azure Storage 커넥터는 저장소 컨테이너에 직접 액세스할 수 있습니다. 이 설정은 ISE에서 서비스 엔드포인트을 사용 하는 것과 동일 합니다.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>관리 id를 사용 하 여 신뢰할 수 있는 서비스로 저장소 계정 액세스

Microsoft의 신뢰할 수 있는 서비스에 방화벽을 통해 저장소 계정에 대한 액세스 권한을 부여 하려면 해당 서비스에 대한 해당 저장소 계정에 예외를 설정 하면 됩니다. 이 솔루션은 [인증을 위해 관리 되는 id](../active-directory/managed-identities-azure-resources/overview.md) 를 지 원하는 Azure 서비스가 방화벽 뒤에 있는 저장소 계정에 신뢰할 수 있는 서비스로 액세스 하도록 허용 합니다. 특히 글로벌 다중 테넌트 Azure의 논리 앱에서 이러한 저장소 계정에 액세스 하려면 먼저 논리 앱에서 [관리 되는 id 지원을 사용 하도록 설정](../logic-apps/create-managed-service-identity.md) 합니다. 그런 다음 논리 앱에서 HTTP 작업 또는 트리거를 사용 하 고 [논리 앱의 관리 되는 id를 사용 하도록 해당 인증 유형을 설정](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)합니다. 이 시나리오에서는 HTTP 작업 또는 트리거 *만* 사용할 수 있습니다.

예외 및 관리 되는 id 지원을 설정 하려면 다음과 같은 일반적인 단계를 수행 합니다.

1. 저장소 계정의 **설정**에서 **방화벽 및 가상 네트워크**를 선택 합니다. 다음 **에서 액세스 허용에서** **선택한 네트워크** 옵션을 선택 하 여 관련 설정이 표시 되도록 합니다.

1. **예외**에서 **신뢰할 수 있는 Microsoft 서비스에서이 저장소 계정에 액세스 하도록 허용**을 선택 하 고 **저장**을 선택 합니다.

   ![Microsoft 신뢰할 수 있는 서비스를 허용 하는 예외 선택](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. 논리 앱의 설정에서 [관리 되는 id에 대한 지원을 사용 하도록 설정](../logic-apps/create-managed-service-identity.md)합니다.

1. 논리 앱의 워크플로에서 저장소 계정 또는 엔터티에 액세스 하는 HTTP 작업 또는 트리거를 추가 하 고 설정 합니다.

   > [!IMPORTANT]
   > 나가는 HTTP 작업 또는 Azure Storage 계정에 대한 트리거 호출의 경우 요청 헤더에는 저장소 계정에서 실행 하려는 작업의 `x-ms-version` 속성 및 API 버전이 포함 되어 있는지 확인 합니다. 자세한 내용은 [관리 되는 id를 사용 하 여 액세스 인증](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) 및 [Azure Storage services에 대한 버전](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests)관리를 참조 하세요.

1. 해당 작업에서 인증에 사용할 [관리 되는 id를 선택](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) 합니다.

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Azure API Management를 통해 저장소 계정에 액세스

[API Management](../api-management/api-management-key-concepts.md)에 전용 계층을 사용 하는 경우 API Management를 사용 하 고 방화벽을 통해 후자의 IP 주소를 허용 하 여 저장소 API를 앞으로 이동할 수 있습니다. 기본적으로 API Management에서 사용 하는 Azure 가상 네트워크를 저장소 계정의 방화벽 설정에 추가 합니다. 그런 다음 API Management 작업 또는 HTTP 작업을 사용 하 여 Azure Storage Api를 호출할 수 있습니다. 그러나이 옵션을 선택 하는 경우 인증 프로세스를 직접 처리 해야 합니다. 자세한 내용은 [간단한 엔터프라이즈 통합 아키텍처](https://aka.ms/aisarch)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
