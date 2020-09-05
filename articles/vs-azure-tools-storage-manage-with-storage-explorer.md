---
title: Storage Explorer 시작 | Microsoft Docs
description: Storage 탐색기를 사용 하 여 Azure storage 리소스 관리를 시작 합니다. Azure Storage 탐색기를 다운로드 하 여 설치 하 고, 저장소 계정 또는 서비스에 연결 합니다.
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 9922601d67393d103b95da7286d8db71d913434a
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235248"
---
# <a name="get-started-with-storage-explorer"></a>Storage Explorer 시작

## <a name="overview"></a>개요

Microsoft Azure Storage Explorer는 Windows, macOS 및 Linux에서 Azure Storage 데이터를 쉽게 사용할 수 있게 하는 독립 실행형 앱입니다. 이 문서에서는 Azure storage 계정에 연결 하 고 관리 하는 몇 가지 방법을 알아봅니다.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>필수 구성 요소

# <a name="windows"></a>[Windows](#tab/windows)

다음 버전의 Windows에서는 Storage 탐색기을 지원 합니다.

* Windows 10(권장)
* Windows 8
* Windows 7

모든 버전의 Windows에서는 Storage 탐색기에 최소한 .NET Framework 4.7.2 필요 합니다.

# <a name="macos"></a>[macOS](#tab/macos)

다음 버전의 macOS 지원 Storage 탐색기:

* macOS 10.12 시에라리온 이상 버전

# <a name="linux"></a>[Linux](#tab/linux)

Storage 탐색기은 Linux의 가장 일반적인 배포를 위해 [Snap 저장소](https://snapcraft.io/storage-explorer) 에서 사용할 수 있습니다. 이 설치에 대해 Snap Store를 권장 합니다. Storage 탐색기 snap는 새 버전이 Snap 저장소에 게시 될 때 모든 종속성 및 업데이트를 설치 합니다.

지원 되는 배포에 대해서는 [snapd 설치 페이지](https://snapcraft.io/docs/installing-snapd)를 참조 하세요.

Storage 탐색기 암호 관리자를 사용 해야 합니다. 수동으로 암호 관리자에 연결 해야 할 수도 있습니다. 다음 명령을 실행 하 여 시스템의 암호 관리자에 Storage 탐색기를 연결할 수 있습니다.

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage 탐색기는 *release.tar.gz* 다운로드로도 사용할 수 있습니다. 종속성을 수동으로 설치 해야 합니다. Release.tar.gz 설치는 다음과 같은 Linux의 배포를 지원 *합니다* .

* Ubuntu 20.04 x64
* Ubuntu 18.04 x64
* Ubuntu 16.04 x64

*Release.tar.gz* 설치는 다른 배포판에서 작동할 수 있지만 나열 된 배포만 공식적으로 지원 됩니다.

Linux에 Storage 탐색기를 설치 하는 데 대 한 자세한 내용은 Azure Storage 탐색기 문제 해결 가이드에서 [linux 종속성](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies) 을 참조 하세요.

---

## <a name="download-and-install"></a>다운로드 및 설치

Storage 탐색기를 다운로드 하 고 설치 하려면 [Azure Storage 탐색기](https://www.storageexplorer.com)를 참조 하세요.

## <a name="connect-to-a-storage-account-or-service"></a>스토리지 계정 또는 서비스에 연결

Storage Explorer는 Storage 계정에 연결하는 여러 방법을 제공합니다. 일반적으로 다음 중 하나를 수행할 수 있습니다.

* [Azure에 로그인 하 여 구독 및 해당 리소스에 액세스](#sign-in-to-azure)
* [특정 저장소 또는 CosmosDB 리소스 연결](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Azure에 로그인

> [!NOTE]
> 로그인 한 후 리소스에 완전히 액세스 하려면 Storage 탐색기 관리 (Azure Resource Manager)와 데이터 계층 권한이 모두 필요 합니다. 즉, 저장소 계정에 대 한 액세스, 계정의 컨테이너 및 컨테이너의 데이터를 제공 하는 Azure Active Directory (Azure AD) 권한이 필요 합니다. 데이터 계층 에서만 사용 권한이 있는 경우 [AZURE AD를 통해 리소스를 추가](#add-a-resource-via-azure-ad)하는 것이 좋습니다. Storage 탐색기 필요한 특정 사용 권한에 대 한 자세한 내용은 [Azure Storage 탐색기 문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues)를 참조 하세요.

1. Storage 탐색기에서 **View**  >  **계정 관리** 보기를 선택 하거나 **계정 관리** 단추를 선택 합니다.

    ![계정 관리][1]

1. 이제 **계정 관리** 에서 로그인 한 모든 Azure 계정을 표시 합니다. 다른 계정에 연결 하려면 **계정 추가**를 선택 합니다.

1. **Azure Storage에 연결**에서 **Azure 환경** 에서 azure 클라우드를 선택 하 여 국가별 클라우드 또는 Azure Stack에 로그인 합니다. 환경을 선택한 후 **다음**을 선택 합니다.

    ![로그인 옵션][2]

    Storage 탐색기 로그인 할 페이지가 열립니다. 자세한 내용은 [Azure Stack 구독 또는 저장소 계정에 저장소 탐색기 연결을](/azure-stack/user/azure-stack-storage-connect-se)참조 하세요.

1. Azure 계정으로 성공적으로 로그인 하면 해당 계정과 연결 된 계정 및 Azure 구독이 **계정 관리**아래에 나타납니다. **모든 구독** 을 선택 하 여 나열 된 Azure 구독을 모두 선택 하거나 선택 취소 합니다. 작업하려는 Azure 구독을 선택한 후 **적용**을 선택합니다.

    ![Azure 구독 선택][3]

    **탐색기** 선택한 Azure 구독과 연결 된 저장소 계정을 표시 합니다.

    ![선택한 Azure 구독][4]

### <a name="attach-a-specific-resource"></a>특정 리소스 연결

Storage 탐색기에서 리소스에 연결 하는 방법에는 여러 가지가 있습니다.

* [AZURE AD를 통해 리소스를 추가](#add-a-resource-via-azure-ad)합니다. 데이터 계층 에서만 사용 권한이 있는 경우이 옵션을 사용 하 여 blob 컨테이너 또는 Azure Data Lake Storage Gen2 Blob 저장소 컨테이너를 추가 합니다.
* [연결 문자열을 사용](#use-a-connection-string)합니다. 저장소 계정에 대 한 연결 문자열이 있는 경우이 옵션을 사용 합니다. Storage 탐색기는 키 및 [공유 액세스 서명](storage/common/storage-dotnet-shared-access-signature-part-1.md) 연결 문자열을 모두 지원 합니다.
* [공유 액세스 서명 URI를 사용](#use-a-shared-access-signature-uri)합니다. Blob 컨테이너, 파일 공유, 큐 또는 테이블에 대 한 [공유 액세스 서명 URI](storage/common/storage-dotnet-shared-access-signature-part-1.md) 가 있는 경우이를 사용 하 여 리소스에 연결 합니다. 공유 액세스 서명 URI를 가져오기 위해 [Storage 탐색기](#generate-a-sas-in-storage-explorer) 또는 [Azure Portal](https://portal.azure.com)를 사용할 수 있습니다.
* [이름 및 키를 사용](#use-a-name-and-key)합니다. 저장소 계정에 대 한 계정 키 중 하나를 알고 있는 경우이 옵션을 사용 하 여 신속 하 게 연결할 수 있습니다. Azure Portal에서 **설정**선택  >  **키** 를 선택 하 여 저장소 계정 페이지에서 키를 [Azure portal](https://portal.azure.com)찾습니다.
* [로컬 에뮬레이터에 연결](#attach-to-a-local-emulator)합니다. 사용 가능한 Azure Storage 에뮬레이터 중 하나를 사용 하는 경우 에뮬레이터에 쉽게 연결 하려면이 옵션을 사용 합니다.
* [연결 문자열을 사용 하 여 Azure Cosmos DB 계정에 연결](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string)합니다. CosmosDB 인스턴스에 대 한 연결 문자열이 있는 경우이 옵션을 사용 합니다.
* [URI로 Azure Data Lake Store에 연결](#connect-to-azure-data-lake-store-by-uri)합니다. Azure Data Lake Store URI가 있는 경우이 옵션을 사용 합니다.

#### <a name="add-a-resource-via-azure-ad"></a>Azure AD를 통해 리소스 추가

1. **연결** 기호를 선택 하 여 **Azure Storage에 대 한 연결을**엽니다.

    ![Azure Storage 옵션에 연결][9]

1. 아직 수행 하지 않은 경우 **Azure 계정 추가** 옵션을 사용 하 여 리소스에 대 한 액세스 권한이 있는 azure 계정에 로그인 합니다. 로그인 한 후를 반환 하 여 **Azure Storage에 연결**합니다.

1. **Azure Active Directory (AZURE AD)를 통해 리소스 추가**를 선택 하 고 **다음**을 선택 합니다.

1. Azure 계정 및 테 넌 트를 선택 합니다. 이러한 값에는 연결 하려는 저장소 리소스에 대 한 액세스 권한이 있어야 합니다. **다음**을 선택합니다.

1. 연결 하려는 리소스 종류를 선택 합니다. 연결에 필요한 정보를 입력 합니다. 

   이 페이지에 입력 하는 정보는 추가 하는 리소스의 유형에 따라 달라 집니다. 올바른 리소스 형식을 선택 해야 합니다. 필요한 정보를 입력 한 후 **다음**을 선택 합니다.

1. **연결 요약** 을 검토 하 여 모든 정보가 올바른지 확인 합니다. 있는 경우 **연결**을 선택 합니다. 그렇지 않은 경우 **뒤로** 를 선택 하 여 이전 페이지로 돌아가 잘못 된 정보를 수정 합니다.

연결이 성공적으로 추가 되 면 리소스 트리는 연결을 나타내는 노드로 이동 합니다. 리소스는 **로컬 & 연결**된  >  **저장소 계정**  >  **(연결 된 컨테이너)**  >  **Blob 컨테이너**아래에 나타납니다. Storage 탐색기 연결을 추가할 수 없거나 연결을 성공적으로 추가한 후 데이터에 액세스할 수 없는 경우 [Azure Storage 탐색기 문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)를 참조 하세요.

#### <a name="use-a-connection-string"></a>연결 문자열 사용

1. **연결** 기호를 선택 하 여 **Azure Storage에 대 한 연결을**엽니다.

    ![Azure Storage 옵션에 연결][9]

1. **연결 문자열 사용**을 선택 하 고 **다음**을 선택 합니다.

1. 연결에 대 한 표시 이름을 선택 하 고 연결 문자열을 입력 합니다. 그다음에 **다음**을 선택합니다.

1. **연결 요약** 을 검토 하 여 모든 정보가 올바른지 확인 합니다. 있는 경우 **연결**을 선택 합니다. 그렇지 않은 경우 **뒤로** 를 선택 하 여 이전 페이지로 돌아가 잘못 된 정보를 수정 합니다.

연결이 성공적으로 추가 되 면 리소스 트리는 연결을 나타내는 노드로 이동 합니다. 리소스는 **로컬 & 연결 된**  >  **저장소 계정**아래에 나타납니다. Storage 탐색기 연결을 추가할 수 없거나 연결을 성공적으로 추가한 후 데이터에 액세스할 수 없는 경우 [Azure Storage 탐색기 문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)를 참조 하세요.

#### <a name="use-a-shared-access-signature-uri"></a>SAS(공유 액세스 서명) URI 사용

1. **연결** 기호를 선택 하 여 **Azure Storage에 대 한 연결을**엽니다.

    ![Azure Storage 옵션에 연결][9]

1. **SAS (공유 액세스 서명) URI 사용**을 선택 하 고 **다음**을 선택 합니다.

1. 연결에 대 한 표시 이름을 선택 하 고 공유 액세스 서명 URI를 입력 합니다. 연결 중인 리소스의 형식에 대 한 서비스 끝점은 자동으로 자동 채우기 되어야 합니다. 사용자 지정 끝점을 사용 하는 경우에는 그렇지 않을 수 있습니다. **다음**을 선택합니다.

1. **연결 요약** 을 검토 하 여 모든 정보가 올바른지 확인 합니다. 있는 경우 **연결**을 선택 합니다. 그렇지 않은 경우 **뒤로** 를 선택 하 여 이전 페이지로 돌아가 잘못 된 정보를 수정 합니다.

연결이 성공적으로 추가 되 면 리소스 트리는 연결을 나타내는 노드로 이동 합니다. 리소스는 연결 된 **Local & Attached**  >  **Storage Accounts**  >  컨테이너 형식에 대 한 서비스 노드인 로컬 & 연결 된 저장소 계정 **(연결 된 컨테이너)** 아래  >  *에*나타납니다. Storage 탐색기에서 연결을 추가할 수 없는 경우 [Azure Storage 탐색기 문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)를 참조 하세요. 연결을 성공적으로 추가한 후 데이터에 액세스할 수 없는 경우 문제 해결 가이드를 참조 하세요.

#### <a name="use-a-name-and-key"></a>이름 및 키 사용

1. **연결** 기호를 선택 하 여 **Azure Storage에 대 한 연결을**엽니다.

    ![Azure Storage 옵션에 연결][9]

1. **스토리지 계정 이름 및 키 사용**, **다음**을 차례로 선택합니다.

1. 연결에 대 한 표시 이름을 선택 합니다.

1. 저장소 계정 이름 및 해당 액세스 키 중 하나를 입력 합니다.

1. 사용할 **저장소 도메인** 을 선택 하 고 **다음**을 선택 합니다.

1. **연결 요약** 을 검토 하 여 모든 정보가 올바른지 확인 합니다. 있는 경우 **연결**을 선택 합니다. 그렇지 않은 경우 **뒤로** 를 선택 하 여 이전 페이지로 돌아가 잘못 된 정보를 수정 합니다.

연결이 성공적으로 추가 되 면 리소스 트리는 연결을 나타내는 노드로 이동 합니다. 리소스는 **로컬 & 연결 된**  >  **저장소 계정**아래에 나타납니다. Storage 탐색기 연결을 추가할 수 없거나 연결을 성공적으로 추가한 후 데이터에 액세스할 수 없는 경우 [Azure Storage 탐색기 문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)를 참조 하세요.

#### <a name="attach-to-a-local-emulator"></a>로컬 에뮬레이터에 연결

Storage 탐색기는 현재 두 가지 공식 저장소 에뮬레이터를 지원 합니다.

* [Azure Storage 에뮬레이터](storage/common/storage-use-emulator.md) (Windows에만 해당)
* [Azurite](https://github.com/azure/azurite) (Windows, Macos 또는 Linux)

에뮬레이터가 기본 포트에서 수신 대기 하는 경우 에뮬레이터 **-기본 포트** 노드를 사용 하 여 에뮬레이터에 액세스할 수 있습니다. **로컬 & 연결 된**저장소 계정에서 **에뮬레이터-기본 포트** 를 찾습니다  >  **Storage Accounts**.

연결에 다른 이름을 사용 하거나 에뮬레이터가 기본 포트에서 실행 되지 않는 경우 다음 단계를 수행 합니다.

1. 에뮬레이터를 시작 합니다. 명령을 입력 `AzureStorageEmulator.exe status` 하 여 각 서비스 유형에 대 한 포트를 표시 합니다.

   > [!IMPORTANT]
   > Storage 탐색기 에뮬레이터를 자동으로 시작 하지 않습니다. 수동으로 시작 해야 합니다.

1. **연결** 기호를 선택 하 여 **Azure Storage에 대 한 연결을**엽니다.

    ![Azure Storage 옵션에 연결][9]

1. **로컬 에뮬레이터에 연결**을 선택 하 고 **다음**을 선택 합니다.

1. 연결에 대 한 표시 이름을 선택 하 고 에뮬레이터에서 각 서비스 유형에 대해 수신 대기 하는 포트를 입력 합니다. **로컬 에뮬레이터에 연결** 대부분의 에뮬레이터에 대 한 기본 포트 값을 제안 합니다. 현재 공식 에뮬레이터는 모두 파일 서비스를 지원 하지 않으므로 **파일 포트** 는 비어 있습니다. 사용 중인 에뮬레이터가 파일을 지원 하면 사용할 포트를 입력할 수 있습니다. 그다음에 **다음**을 선택합니다.

1. **연결 요약** 을 검토 하 고 모든 정보가 올바른지 확인 합니다. 있는 경우 **연결**을 선택 합니다. 그렇지 않은 경우 **뒤로** 를 선택 하 여 이전 페이지로 돌아가 잘못 된 정보를 수정 합니다.

연결이 성공적으로 추가 되 면 리소스 트리는 연결을 나타내는 노드로 이동 합니다. 노드가 **로컬 & 연결 된**  >  **저장소 계정**아래에 표시 되어야 합니다. Storage 탐색기 연결을 추가할 수 없거나 연결을 성공적으로 추가한 후 데이터에 액세스할 수 없는 경우 [Azure Storage 탐색기 문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)를 참조 하세요.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>연결 문자열을 사용하여 Azure Cosmos DB 계정에 연결

Azure 구독을 통해 Azure Cosmos DB 계정을 관리 하는 대신 연결 문자열을 사용 하 여 Azure Cosmos DB에 연결할 수 있습니다. 연결하려면 다음 단계를 따릅니다.

1. **탐색기**에서 **로컬 & 연결**됨을 확장 하 고 **Cosmos DB 계정**을 마우스 오른쪽 단추로 클릭 한 다음 **Azure Cosmos DB에 연결**을 선택 합니다.

    ![연결 문자열을 사용하여 Azure Cosmos DB에 연결][21]

1. Azure Cosmos DB API를 선택 하 고 **연결 문자열** 데이터를 입력 한 다음 **확인** 을 선택 하 여 Azure Cosmos DB 계정을 연결 합니다. 연결 문자열을 검색 하는 방법에 대 한 자세한 내용은 [Azure Cosmos 계정 관리](https://docs.microsoft.com/azure/cosmos-db/manage-account)를 참조 하세요.

    ![연결 문자열][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>URI로 Azure Data Lake Store에 연결

구독에 없는 리소스에 액세스할 수 있습니다. 리소스 URI를 제공 하기 위해 해당 리소스에 대 한 액세스 권한이 있는 사용자가 필요 합니다. 로그인 한 후 URI를 사용 하 여 Data Lake Store에 연결 합니다. 연결하려면 다음 단계를 따릅니다.

1. **탐색기**에서 **로컬 & 연결**됨을 확장 합니다.

1. **Data Lake Storage Gen1**를 마우스 오른쪽 단추로 클릭 하 고 **Data Lake Storage Gen1에 연결을**선택 합니다.

    ![Data Lake Store 상황에 맞는 메뉴에 연결](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. URI를 입력 한 다음 **확인**을 선택 합니다. Data Lake Store **Data Lake Storage**아래에 나타납니다.

    ![Data Lake Store 결과에 연결](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

이 예제에서는 Data Lake Storage Gen1를 사용 합니다. 이제 Azure Data Lake Storage Gen2를 사용할 수 있습니다. 자세한 내용은 [Azure Data Lake Storage Gen1 정의](./data-lake-store/data-lake-store-overview.md)를 참조 하세요.

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Storage 탐색기에서 공유 액세스 서명 생성<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>계정 수준 공유 액세스 서명

1. 공유 하려는 저장소 계정을 마우스 오른쪽 단추로 클릭 하 고 **공유 액세스 서명 가져오기**를 선택 합니다.

    ![공유 액세스 서명 상황에 맞는 메뉴 옵션 가져오기][14]

1. **공유 액세스 서명**에서 계정에 대해 원하는 시간 프레임 및 사용 권한을 지정 하 고 **만들기**를 선택 합니다.

    ![공유 액세스 서명 가져오기][15]

1. **연결 문자열** 또는 원시 **쿼리 문자열** 을 클립보드에 복사 합니다.

### <a name="service-level-shared-access-signature"></a>서비스 수준 공유 액세스 서명

서비스 수준에서 공유 액세스 서명을 가져올 수 있습니다. 자세한 내용은 [blob 컨테이너에 대 한 SAS 가져오기](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)를 참조 하세요.

## <a name="search-for-storage-accounts"></a>스토리지 계정 검색

저장소 리소스를 찾으려면 **탐색기** 창에서 검색할 수 있습니다.

검색 상자에 텍스트를 입력 하면 Storage 탐색기는 해당 시점까지 입력 한 검색 값과 일치 하는 모든 리소스를 표시 합니다. 다음 예제에서는 **끝점**에 대 한 검색을 보여 줍니다.

![Storage 계정 검색][23]

> [!NOTE]
> 검색 속도를 높이려면 **계정 관리** 를 사용 하 여 검색 중인 항목이 포함 되지 않은 구독을 선택 취소 합니다. 노드를 마우스 오른쪽 단추로 클릭 하 고 **여기에서 검색** 을 선택 하 여 특정 노드에서 검색을 시작할 수도 있습니다.
>
>

## <a name="next-steps"></a>다음 단계

* [Storage 탐색기를 사용 하 여 Azure Blob storage 리소스 관리](vs-azure-tools-storage-explorer-blobs.md)
* [Azure Storage Explorer 를 사용하여 데이터 작업](./cosmos-db/storage-explorer.md)
* [Storage Explorer를 사용하여 Azure Data Lake Store 리소스 관리](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
