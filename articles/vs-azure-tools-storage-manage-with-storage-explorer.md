---
title: Storage 탐색기 시작 | Microsoft Docs
description: Storage 탐색기로 Azure 스토리지 리소스 관리
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: 3db1d638aa1e0d8488c310593a593465c2b9b336
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128282"
---
# <a name="get-started-with-storage-explorer"></a>Storage 탐색기 시작

## <a name="overview"></a>개요

Azure Storage 탐색기는 Windows, macOS 및 Linux에서 Azure Storage 데이터를 손쉽게 사용할 수 있는 독립 실행형 앱입니다. 이 문서에서는 Azure Storage 계정에 연결하고 관리하는 몇 가지 방법을 알아봅니다.

![Microsoft Azure Storage 탐색기][0]

## <a name="prerequisites"></a>필수 조건

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Azure Storage 탐색기는 다음 버전의 Windows에서 지원됩니다.

* Windows 10(권장)
* Windows 8
* Windows 7

모든 버전의 Windows에서는 .NET Framework 4.6.2 이상 버전이 필요합니다.

[Storage 탐색기 다운로드 및 설치](https://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Azure Storage 탐색기는 다음 버전의 macOS에서 지원됩니다.

* macOS 10.12 "Sierra" 이상 버전

[Storage 탐색기 다운로드 및 설치](https://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Azure Storage 탐색기는 다음 Linux 배포에서 지원됩니다.

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Azure Storage 탐색기는 다른 배포에서 작동할 수도 있지만 위에 나열된 항목만 공식적으로 지원됩니다.

Linux에서 Storage 탐색기를 설치 하는 자세한 도움말을 참조 하세요. 합니다 [문제 해결 가이드](https://docs.microsoft.com/en-us/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies)합니다.

Azure Storage 탐색기 [릴리스 정보](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409)는 일부 배포에 대한 특정 단계를 포함합니다.

[Storage 탐색기 다운로드 및 설치](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>저장소 계정 또는 서비스에 연결

Storage 탐색기는 Storage 계정에 연결하는 여러 방법을 제공합니다. 예를 들어 다음을 수행할 수 있습니다.

* Azure 구독과 연결된 저장소 계정에 연결합니다.
* 다른 Azure 구독에서 공유되는 저장소 계정 및 서비스에 연결합니다.
* Azure Storage 에뮬레이터를 사용하여 로컬 스토리지에 연결하고 관리합니다.

또한 글로벌 및 국가별 Azure에서 저장소 계정으로 작업할 수 있습니다.

* [Azure 구독에 연결](#connect-to-an-azure-subscription): Azure 구독에 속한 스토리지 리소스를 관리합니다.
* [로컬 개발 스토리지로 작업](#work-with-local-development-storage): Azure Storage 에뮬레이터를 사용하여 로컬 스토리지를 관리합니다.
* [외부 스토리지에 연결](#attach-or-detach-an-external-storage-account): 스토리지 계정의 계정 이름, 키 및 엔드포인트를 사용하여 다른 Azure 구독에 속하거나 국가별 Azure 클라우드 아래에 있는 스토리지 리소스를 관리합니다.
* [SAS를 사용하여 스토리지 계정 연결](#attach-a-storage-account-by-using-a-shared-access-signature-sas): SAS(공유 액세스 서명)를 사용하여 다른 Azure 구독에 속한 스토리지 리소스를 관리합니다.
* [SAS를 사용하여 서비스 연결](#attach-a-service-by-using-a-shared-access-signature-sas): SAS를 사용하여 다른 Azure 구독에 속한 특정 스토리지 서비스(Blob 컨테이너, 큐 또는 테이블)를 관리합니다.
* [연결 문자열을 사용하여 Azure Cosmos DB 계정에 연결](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): 연결 문자열을 사용하여 Cosmos DB 계정을 관리합니다.

## <a name="connect-to-an-azure-subscription"></a>Azure 구독에 연결

> [!NOTE]
> Azure 계정이 없는 경우 [무료 평가판을 등록](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)하거나 [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있습니다.
>
>

1. Storage 탐색기에서 **계정 관리**를 선택하여 **계정 관리 패널**로 이동합니다.

    ![계정 관리][1]

2. 이제 왼쪽 창에는 사용자가 로그인한 모든 Azure 계정이 표시됩니다. 다른 계정에 연결하려면 **계정 추가**를 선택합니다.

3. 국내 클라우드 또는 Azure Stack에 로그인하려는 경우 **Azure 환경** 드롭다운을 클릭하여 사용하려는 Azure 클라우드를 선택합니다. 환경을 선택했으면 **로그인...** 단추를 클릭합니다. Azure Stack에 로그인하는 경우 자세한 내용은 [Azure Stack 구독에 저장소 탐색기 연결](/azure-stack/user/azure-stack-storage-connect-se)을 참조하세요.

    ![로그인 옵션][2]

4. Azure Stack 계정으로 성공적으로 로그인하면 계정과 계정에 연결된 Azure Stack 구독이 왼쪽 창에 추가됩니다. 작업하려는 Azure 구독을 선택한 후 **적용**(**모든 구독:** 을 선택하면 나열된 Azure 구독의 전체 선택 및 선택 취소가 전환됨)을 선택합니다.

    ![Azure 구독 선택][3]

    왼쪽 창은 선택한 Azure 구독과 연결된 저장소 계정을 표시합니다.

    ![선택한 Azure 구독][4]

## <a name="work-with-local-development-storage"></a>로컬 개발 저장소로 작업

Storage 탐색기로 에뮬레이터를 사용하여 로컬 스토리지에서 작업할 수 있습니다. 이 방식에서는 Azure에 스토리지 계정을 반드시 배포하지 않고도, Azure Storage 작업을 시뮬레이션할 수 있습니다.

버전 1.1.0 이상에서는 Storage 탐색기는 로컬 저장소 에뮬레이터를 사용 하 여 모든 플랫폼에서 지원 합니다. Storage 탐색기는 해당 기본 로컬 스토리지 엔드포인트를 수신 대기하는 모든 에뮬레이트된 서비스에 연결할 수 있습니다.

합니다 [Azure storage 에뮬레이터](storage/common/storage-use-emulator.md) 현재 Windows 에서만 실행 됩니다. Linux용 저장소 에뮬레이터를 고려 중인 경우 커뮤니티에서 유지 관리하는 오픈 소스 저장소 에뮬레이터 [Azurite](https://github.com/azure/azurite)가 하나의 옵션이 될 수 있습니다.

> [!NOTE]
> Storage 서비스 및 기능에 대한 지원은 에뮬레이터의 선택에 따라 광범위하게 달라질 수 있습니다. 에뮬레이터가 함께 작업하려는 서비스 및 기능을 지원하는지 확인합니다.

1. 선택한 에뮬레이터의 서비스를 사용하지 않은 포트로 수신하도록 구성합니다.

   에뮬레이트된 서비스 | 기본 엔드포인트
   -----------------|-------------------------
   Blob            | `http://127.0.0.1:10000`
   큐           | `http://127.0.0.1:10001`
   테이블           | `http://127.0.0.1:10002`

2. 에뮬레이터를 시작합니다.
   > [!IMPORTANT]
   > Storage 탐색기는 자동으로 에뮬레이터를 시작하지 않습니다. 사용자가 직접 시작해야 합니다.

3. Storage 탐색기에서 **계정 추가** 단추를 클릭합니다. **로컬 에뮬레이터에 연결**을 선택하고 **다음**을 클릭합니다.

4. 위에서 구성한 서비스에 대한 포트 번호를 입력합니다. (해당 서비스를 사용하지 않으려면 공백으로 두십시오.) **다음**을 클릭한 다음, **연결**을 클릭하여 연결을 만듭니다.

5. **로컬 및 연결됨** > **저장소 계정** > 노드를 확장한 다음, 에뮬레이터 연결에 해당하는 노드 아래의 서비스 노드를 확장합니다.

   이 노드를 사용하여 로컬 Blob, 큐 및 테이블을 만들고 작업할 수 있습니다. 각 저장소 계정 유형을 작업하는 방법을 알아보려면 다음 가이드를 참조하세요.

   * [Azure Blob Storage 리소스 관리](vs-azure-tools-storage-explorer-blobs.md)
   * [Azure File 스토리지 리소스 관리](vs-azure-tools-storage-explorer-files.md)

## <a name="attach-or-detach-an-external-storage-account"></a>외부 저장소 계정 연결 또는 분리

Storage 탐색기를 사용하면 외부 스토리지 계정에 연결하여 스토리지 계정을 쉽게 공유할 수 있습니다. 이 섹션은 외부 저장소 계정에 연결(및 분리)하는 방법을 설명합니다.

### <a name="get-the-storage-account-credentials"></a>저장소 계정 자격 증명 가져오기

외부 저장소 계정을 공유하려면 해당 계정의 소유자는 먼저 계정에 대한 자격 증명(계정 이름 및 키)을 가져온 다음 해당 정보를 해당 계정에 연결하려는 사용자와 공유해야 합니다. 다음 단계를 수행하여 Azure Portal을 통해 저장소 계정 자격 증명을 얻을 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **찾아보기**를 선택합니다.

3. **Storage 계정**을 선택합니다.

4. **저장소 계정** 목록에서 원하는 저장소 계정을 선택합니다.

5. **설정** 아래에서 **액세스 키**를 선택합니다.

    ![액세스 키 옵션][7]

6. **저장소 계정 이름** 및 **key1**을 복사합니다.

    ![액세스 키][8]

### <a name="attach-to-an-external-storage-account"></a>외부 저장소 계정에 연결

외부 저장소 계정에 연결하려면 계정의 이름 및 키가 필요합니다. "저장소 계정 자격 증명 가져오기" 섹션에서는 Azure Portal에서 이러한 값을 가져오는 방법을 설명합니다. 그러나 포털에서는 계정 키를 **key1**이라고 부릅니다. 따라서 Storage 탐색기가 계정 키를 요구하는 위치에 **key1**을 입력합니다.

1. Storage 탐색기에서 **연결 대화 상자**를 엽니다.

    ![Azure Storage 옵션에 연결][9]

2. **연결 대화 상자**에서 **저장소 계정 이름 및 키 사용**을 선택합니다.

    ![이름 및 키 옵션으로 추가][10]

3. 계정 이름을 **계정 이름** 텍스트 상자에 붙여 넣고 계정 키(Azure Portal의 **key1**)를 **계정 키** 입력란에 붙여 넣은 후 **다음**을 선택합니다.

    ![이름 및 키 페이지][11]

    > [!NOTE]
    > 국내 클라우드의 이름 및 키를 사용하려면 **저장소 엔드포인트 도메인:** 드롭다운을 사용하여 해당 엔드포인트 도메인을 선택합니다.
    >
    >

4. **연결 요약** 대화 상자에서 정보를 확인합니다. 변경하려는 경우 **뒤로**를 선택하고 원하는 설정을 다시 입력합니다.

5. **연결**을 선택합니다.

6. 저장소 계정에 연결되면 저장소 계정 이름에 **(외부)** 가 표시됩니다.

    ![외부 저장소 계정 연결 결과][12]

### <a name="detach-from-an-external-storage-account"></a>외부 저장소 계정에서 분리

1. 분리할 외부 저장소 계정을 마우스 오른쪽 단추로 클릭한 다음 **분리**를 선택합니다.

    ![저장소 옵션에서 분리][13]

2. 확인 메시지에서 **예**를 선택하여 외부 저장소 계정에서 분리를 확인합니다.

## <a name="attach-a-storage-account-by-using-a-shared-access-signature-sas"></a>SAS(공유 액세스 서명)를 사용하여 저장소 계정 연결

공유 액세스 서명 또는 [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md)를 사용하면 Azure 구독 관리자는 Azure 구독 자격 증명을 제공하지 않고도 저장소 계정에 대한 임시 액세스 권한을 부여할 수 있습니다.

이 시나리오를 설명하기 위해 다음과 같은 상황을 가정하겠습니다. Azure 구독의 관리자인 사용자 A는 사용자 B에게 특정 권한으로 제한된 시간 동안 저장소 계정에 액세스할 수 있는 권한을 부여하려 합니다.

1. 사용자 A는 특정 기간 동안 원하는 사용 권한으로 SAS 연결 문자열을 생성합니다.

2. 사용자 A는 저장소 계정에 액세스하려는 사람(이 예제에서는 사용자 B)과 SAS를 공유합니다.

3. 사용자 B는 Storage 탐색기를 통해 제공된 SAS를 사용하여 사용자 A에 속한 계정에 연결합니다.

### <a name="generate-a-sas-query-string-for-the-account-you-want-to-share"></a>공유하려는 계정에 대한 SAS 쿼리 문자열 생성

1. Storage 탐색기에서 공유하려는 스토리지 계정을 마우스 오른쪽 단추로 클릭하고 **공유 액세스 서명 가져오기...** 를 선택합니다.

    ![SAS 상황에 맞는 메뉴 옵션 가져오기][14]

2. **공유 액세스 서명 생성** 대화 상자에서 계정에 대해 원하는 시간 프레임 및 권한을 지정하고 **만들기** 단추를 클릭합니다.

    ![SAS 가져오기 대화 상자][15]

3. **쿼리 문자열** 텍스트 상자 옆에 있는 **복사**를 선택하여 클립보드에 복사한 다음, **닫기**를 클릭합니다.

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>SAS 연결 문자열을 사용하여 저장소 계정에 연결

1. Storage 탐색기에서 **연결 대화 상자**를 엽니다.

    ![Azure Storage 옵션에 연결][9]

2. **연결 대화 상자** 대화 상자에서 **연결 문자열 또는 공유 액세스 서명 URI 사용**을 선택하고 **다음**을 클릭합니다.

    ![Azure Storage에 연결 대화 상자][16]

3. **연결 문자열 사용**을 선택하고 연결 문자열을 **연결 문자열:** 필드로 붙여 넣습니다. **다음** 단추를 클릭합니다.

    ![Azure Storage에 연결 대화 상자][17]

4. **연결 요약** 대화 상자에서 정보를 확인합니다. 변경 작업을 수행하려면 **뒤로**를 선택한 다음 원하는 설정을 입력합니다.

5. **연결**을 선택합니다.

6. 저장소 계정에 연결되면 저장소 계정 이름에 **(SAS)** 가 표시됩니다.

    ![SAS를 사용하여 계정에 연결한 결과][18]

## <a name="attach-a-service-by-using-a-shared-access-signature-sas"></a>SAS(공유 액세스 서명)를 사용하여 서비스 연결

"SAS를 사용하여 저장소 계정 연결" 섹션에서는 Azure 구독 관리자가 저장소 계정에 대한 SAS를 생성하고 공유하여 저장소 계정에 대한 임시 액세스 권한을 부여하는 방법을 보여 줍니다. 마찬가지로, SAS는 저장소 계정 내에서 특정 서비스(Blob 컨테이너, 큐, 테이블 또는 파일 공유)에 대해 생성될 수 있습니다.

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>공유할 서비스에 대한 SAS 생성

이 컨텍스트에서 서비스는 Blob 컨테이너, 큐, 테이블 또는 파일 공유일 수 있습니다. 나열된 서비스에 대한 SAS를 생성하려면 다음 항목을 참조하세요.

* [Blob 컨테이너에 대한 SAS 가져오기](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>SAS URI를 사용하여 공유 계정 서비스에 연결

1. Storage 탐색기에서 **연결 대화 상자**를 엽니다.

    ![Azure Storage 옵션에 연결][9]

2. **연결** 대화 상자에서 **연결 문자열 또는 공유 액세스 서명 URI 사용**을 선택하고 **다음**을 클릭합니다.

    ![Azure Storage에 연결 대화 상자][16]

3. **SAS URI 사용**을 선택하고 URI를 **URI:** 필드에 붙여 넣습니다. **다음** 단추를 클릭합니다.

    ![Azure Storage에 연결 대화 상자][19]

4. **연결 요약** 대화 상자에서 정보를 확인합니다. 변경 작업을 수행하려면 **뒤로**를 선택한 다음 원하는 설정을 입력합니다.

5. **연결**을 선택합니다.

6. 연결되면 새로 연결된 서비스가 **(SAS 연결 서비스)** 노드 아래에 표시됩니다.

    ![SAS를 사용하여 공유 서비스에 연결한 결과][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>연결 문자열을 사용하여 Azure Cosmos DB 계정에 연결

Azure 구독을 통해 Azure Cosmos DB 계정을 관리하는 것 외에, Azure Cosmos DB에 연결하는 또 다른 방법은 연결 문자열을 사용하는 것입니다. 연결 문자열을 사용하여 연결하려면 다음 단계를 따르세요.

1. 왼쪽 트리에서 **Local and Attached**(로컬 및 연결)를 찾아 **Azure Cosmos DB 계정**을 마우스 오른쪽 단추로 클릭한 후 **Connect to Azure Cosmos DB...**(Azure Cosmos DB에 연결...)를 선택합니다.

    ![연결 문자열을 사용하여 Azure Cosmos DB에 연결][21]

2. Azure Cosmos DB API를 선택하고 **연결 문자열**을 붙여 넣은 후 **확인**을 클릭하여 Azure Cosmos DB 계정을 연결합니다. 연결 문자열 검색에 대한 자세한 내용은 [Get the connection string](https://docs.microsoft.com/azure/cosmos-db/manage-account)(연결 문자열 가져오기)을 참조하세요.

    ![connection-string][22]

## <a name="connect-to-azure-data-lake-store-by-uri"></a>URI로 Azure Data Lake Store에 연결

구독에 없는 리소스에 액세스하려고 합니다. 하지만 리소스에 대한 URI를 가져올 수 있도록 다른 사용자가 권한을 부여합니다. 이 경우 로그인한 후 URI를 사용하여 Data Lake Store에 연결할 수 있습니다. 다음 단계를 참조하세요.

1. 저장소 탐색기를 엽니다.
2. 왼쪽 창에서 **로컬 및 연결됨**을 펼칩니다.
3. **Data Lake Store**를 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 **Data Lake Store에 연결...** 을 선택합니다.

    ![Data Lake Store 바로 가기 메뉴에 연결](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. URI를 입력하면 도구가 방금 입력한 URL의 위치로 이동합니다.

    ![Data Lake Store에 연결 팝업 대화 상자](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![Data Lake Store에 연결 결과](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="search-for-storage-accounts"></a>저장소 계정 검색

저장소 리소스를 찾아야 하며 위치를 잘 모를 경우, 왼쪽 창 위쪽에 있는 검색 상자를 사용하여 리소스를 검색할 수 있습니다.

검색 상자에 내용을 입력하면 왼쪽 창에 해당 시점까지 사용자가 입력한 검색 값과 일치하는 모든 리소스가 표시됩니다. 예를 들어 **엔드포인트** 검색 결과는 다음 스크린샷에 표시됩니다.

![Storage 계정 검색][23]

> [!NOTE]
> 검색 실행 시간 개선을 위해, **계정 관리 패널**을 사용하여 검색하는 항목이 포함되지 않은 모든 구독은 선택 취소합니다. 노드를 마우스 오른쪽 단추로 클릭하고 **여기에서 검색**을 선택하여 특정 노드에서 검색을 시작할 수도 있습니다.
>
>

## <a name="next-steps"></a>다음 단계

* [Storage 탐색기를 사용하여 Azure Blob Storage 리소스 관리](vs-azure-tools-storage-explorer-blobs.md)
* [Azure Storage 탐색기에서 Azure Cosmos DB 관리(미리 보기)](./cosmos-db/storage-explorer.md)
* [Storage 탐색기를 사용하여 Azure Data Lake Store 리소스 관리](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png
