---
title: 자습서`:` 관리 ID를 사용하여 Azure Data Lake Store에 액세스 - Linux - Azure AD
description: Linux VM 시스템 할당 관리 ID를 사용하여 Azure Data Lake Store에 액세스하는 방법을 보여주는 자습서입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d465419dfe36fd5dd67abdef22a6f54fba69a98e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267465"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>자습서: Linux VM 시스템 할당 관리 ID를 사용하여 Azure Data Lake Store에 액세스

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Linux VM(가상 머신)에 대한 시스템 할당 관리 ID를 사용하여 Azure Data Lake Store에 액세스하는 방법을 보여 줍니다. 다음 방법을 알아봅니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * VM에 Azure Data Lake Store에 대한 액세스 권한 부여
> * VM의 시스템 할당 관리 ID를 사용하여 Azure Data Lake Store에 액세스하도록 액세스 토큰을 가져옵니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-access"></a>액세스 권한 부여

이 섹션에서는 Azure Data Lake Store에 있는 파일 및 폴더에 VM 액세스 권한을 부여하는 방법을 보여줍니다. 이 단계에서는 기존 Data Lake Store 인스턴스를 사용하거나 새로 만들 수 있습니다. Azure Portal을 사용하여 Data Lake Store 인스턴스를 만들려면 이 [Azure Data Lake Store 빠른 시작](../../data-lake-store/data-lake-store-get-started-portal.md)을 참조하세요. [Azure Data Lake Store 설명서](../../data-lake-store/data-lake-store-overview.md)에는 Azure CLI 및 Azure PowerShell을 사용하는 빠른 시작도 있습니다.

Data Lake Store에서 새 폴더를 만들고, 해당 폴더에서 파일을 읽고, 쓰고, 실행할 수 있는 Linux VM 시스템 할당 관리 ID 권한을 부여합니다.

1. Azure Portal의 왼쪽 창에서 **Data Lake Store**를 선택합니다.
2. 사용할 Data Lake Store 인스턴스를 선택합니다.
3. 명령 모음에서 **데이터 탐색기**를 선택합니다.
4. Data Lake Store 인스턴스의 루트 폴더가 선택됩니다. 명령 모음에서 **액세스**를 선택합니다.
5. **추가**를 선택합니다.  **선택** 상자에서 VM의 이름(예: **DevTestVM**)을 입력합니다. 검색 결과에서 VM을 선택한 다음, **선택**을 클릭합니다.
6. **사용 권한 선택**을 클릭합니다.  **읽기** 및 **실행**을 선택하고, **이 폴더**에 추가하고, **액세스 권한 전용**으로 추가합니다. **확인**을 선택합니다.  사용 권한은 성공적으로 추가되어야 합니다.
7. **액세스** 창을 닫습니다.
8. 이 자습서에서는 새 폴더를 만듭니다. 명령 모음에서 **새 폴더**를 선택하고 새 폴더 이름(예: **TestFolder**)을 입력합니다.  **확인**을 선택합니다.
9. 만든 폴더를 선택한 다음, 명령 모음에서 **액세스**를 선택합니다.
10. 5단계와 마찬가지로 **추가**를 선택합니다. **선택** 상자에 VM의 이름을 입력합니다. 검색 결과에서 VM을 선택한 다음, **선택**을 클릭합니다.
11. 6단계와 마찬가지로 **권한 선택**을 선택합니다. **읽기**, **쓰기** 및 **실행**을 선택하고, **이 폴더**에 추가하고, **액세스 권한 항목 및 기본 권한 항목**으로 추가합니다. **확인**을 선택합니다.  사용 권한은 성공적으로 추가되어야 합니다.

이제 사용자가 만든 폴더의 파일에 Azure 리소스에 대한 관리 ID가 모든 작업을 수행할 수 있습니다. Data Lake Store에 대한 액세스 권한을 관리하는 방법에 대한 자세한 내용은 [Data Lake Store의 Access Control](../../data-lake-store/data-lake-store-access-control.md)을 참조하세요.

## <a name="get-an-access-token"></a>액세스 토큰 가져오기 

이 섹션에서는 액세스 토큰을 가져오고 Data Lake Store 파일 시스템을 호출하는 방법을 보여줍니다. Azure Data Lake Store는 기본적으로 Azure AD 인증을 지원하므로 Azure 리소스에 대한 관리 ID를 사용하여 획득한 액세스 토큰을 직접 수락할 수 있습니다. Data Lake Store 파일 시스템에 인증하려면 Azure AD에서 발급한 액세스 토큰을 Data Lake Store 파일 시스템 엔드포인트로 보냅니다. 액세스 토큰은 권한 부여 헤더에 "Bearer \<ACCESS_TOKEN_VALUE\>" 형식으로 있습니다.  Azure AD 인증을 위한 Data Lake Store 지원에 대해 자세히 알아보려면 [Azure Active Directory를 사용하여 Data Lake Store 인증](../../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)을 참조하세요.

이 자습서에서는 cURL을 사용하여 REST 요청을 생성하여 Data Lake Store 파일 시스템용 REST API를 인증합니다.

> [!NOTE]
> Data Lake Store 파일 시스템용 클라이언트 SDK는 Azure 리소스에 대한 관리 ID를 아직 지원하지 않습니다.

아래의 단계를 완료하려면 SSH 클라이언트가 필요합니다. Windows를 사용 중인 경우 [Linux용 Windows 하위 시스템](/windows/wsl/about)에서 SSH 클라이언트를 사용할 수 있습니다. SSH 클라이언트의 키 구성에 대한 도움이 필요하면 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](../../virtual-machines/linux/ssh-from-windows.md) 또는 [Azure에서 Linux VM용 SSH 공개 및 프라이빗 키 쌍을 만들고 사용하는 방법](../../virtual-machines/linux/mac-create-ssh-keys.md)을 참조하세요.

1. 포털에서 Linux VM으로 이동합니다. **개요**에서 **연결**을 선택합니다.  
2. 원하는 SSH 클라이언트를 사용하여 VM에 연결합니다. 
3. 터미널 창에서 cURL을 사용하여 Data Lake Store 파일 시스템에 액세스 토큰을 가져오도록 Azure 리소스에 대한 로컬 관리 ID 엔드포인트에 요청합니다. Data Lake Store의 리소스 식별자는 `https://datalake.azure.net/`입니다.  리소스 식별자에 후행 슬래시를 포함해야 합니다.
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -H Metadata:true   
   ```
    
   성공적인 응답에는 Data Lake Store에 인증하는 데 사용하는 액세스 토큰이 반환됩니다.

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. cURL을 사용하여 Data Lake Store 파일 시스템의 REST 엔드포인트에 요청하여 루트 폴더의 폴더를 나열합니다. 모든 항목이 올바르게 구성되었는지 확인하는 간단한 방법입니다. 이전 단계에서 액세스 토큰의 값을 복사합니다. 인증 헤더의 "Bearer" 문자열에 대문자 "B"가 있어야 합니다. Azure Portal에 있는 **Data Lake Store** 창의 **개요** 섹션에서 Data Lake Store 인스턴스의 이름을 찾을 수 있습니다.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   성공적인 응답은 다음과 같습니다.

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. 이제 Data Lake Store 인스턴스에 파일을 업로드해 볼 수 있습니다. 먼저 업로드할 파일을 만듭니다.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. cURL을 사용하여 Data Lake Store 파일 시스템 REST 엔드포인트에 요청하여 이전에 만든 폴더에 파일을 업로드합니다. 업로드에는 리디렉션이 포함되고 cURL은 리디렉션을 자동으로 따릅니다. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    성공적인 응답은 다음과 같습니다.

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

Data Lake Store 파일 시스템에 다른 API를 사용하여 파일에 추가, 파일 다운로드 등을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Linux VM 시스템 할당 관리 ID를 사용하여 Azure Data Lake Store에 액세스하는 방법을 알아보았습니다. Azure Data Lake Store에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
>[Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md)