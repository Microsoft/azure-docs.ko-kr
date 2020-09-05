---
title: 자습서`:` 관리 ID를 사용하여 Azure Data Lake Store에 액세스 - Windows - Azure AD
description: Windows VM 시스템 할당 관리 ID를 사용하여 Azure Data Lake Store에 액세스하는 방법을 보여주는 자습서입니다.
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
ms.date: 11/14/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d29edec6145ebc03218264532cae07b6afc9654c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254205"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>자습서: Windows VM 시스템 할당 관리 ID를 사용하여 Azure Data Lake Store에 액세스

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Windows VM(가상 머신)에 대한 시스템 할당 관리 ID를 사용하여 Azure Data Lake Store에 액세스하는 방법을 보여줍니다. Azure에서 자동으로 관리되는 관리 서비스 ID를 사용하면 Azure AD 인증을 지원하는 서비스에 인증할 수 있으므로 코드에 자격 증명을 삽입할 필요가 없습니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * VM에 Azure Data Lake Store에 대한 액세스 권한 부여
> * VM ID를 사용하여 액세스 토큰 가져오기 및 Azure Data Lake Store에 액세스하는 데 사용하기

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]



## <a name="enable"></a>사용

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>액세스 권한 부여

이제 Azure Data Lake Store에 있는 파일 및 폴더에 대한 VM 액세스 권한을 부여할 수 있습니다.  이 단계에서는 기존 Data Lake Store를 사용하거나 새로 만들 수 있습니다.  Azure Portal을 사용하여 새 Data Lake Store를 만들려면 이 [Azure Data Lake Store 빠른 시작](../../data-lake-store/data-lake-store-get-started-portal.md)에 따르세요. [Azure Data Lake Store 설명서](../../data-lake-store/data-lake-store-overview.md)에서 Azure CLI 및 Azure PowerShell을 사용하는 빠른 시작도 있습니다.

Data Lake Store에서 새 폴더를 만들고, 해당 폴더에서 파일을 읽고, 쓰고, 실행할 수 있는 VM의 시스템 할당 ID 권한을 부여합니다.

1. Azure Portal의 왼쪽 탐색 창에서 **Data Lake Store**를 클릭합니다.
2. 이 자습서에 사용하려는 Data Lake Store를 클릭합니다.
3. 명령 모음에서 **데이터 탐색기**를 클릭합니다.
4. Data Lake Store의 루트 폴더를 선택합니다.  명령 모음에서 **액세스**를 클릭합니다.
5. **추가**를 클릭합니다.  **선택** 필드에서 VM의 이름을 입력합니다(예: **DevTestVM**).  검색 결과에서 VM을 선택하도록 클릭한 다음 **선택**을 클릭합니다.
6. **사용 권한 선택**을 클릭합니다.  **읽기** 및 **실행**을 선택하고, **이 폴더**에 추가하고, **액세스 권한 전용**으로 추가합니다.  **Ok**를 클릭합니다.  사용 권한은 성공적으로 추가되어야 합니다.
7. **액세스** 블레이드를 닫습니다.
8. 이 자습서에서는 새 폴더를 만듭니다.  명령 모음에서 **새 폴더**를 클릭하고 새 폴더에 이름을 입력합니다(예: **TestFolder**).  **Ok**를 클릭합니다.
9. 사용자가 만든 폴더를 클릭한 다음 명령 모음에서 **액세스**를 클릭합니다.
10. 5단계와 유사하게 **선택** 필드에서 **추가**를 클릭하고, VM의 이름을 입력하고, 선택하고, **선택**을 클릭합니다.
11. 6단계와 유사하게 **사용 권한 선택**을 클릭하고, **읽기**, **쓰기** 및 **실행**을 선택하고, **이 폴더**에 추가하고, **액세스 권한 항목 및 기본 권한 항목**으로 추가합니다.  **Ok**를 클릭합니다.  사용 권한은 성공적으로 추가되어야 합니다.

VM의 시스템 할당 관리 ID는 사용자가 만든 폴더에 있는 파일에서 모든 작업을 수행할 수 있습니다.  Data Lake Store에 대한 액세스 권한을 관리하는 방법에 대한 자세한 내용은 [Data Lake Store의 액세스 제어](../../data-lake-store/data-lake-store-access-control.md)에서 이 문서를 참고하세요.

## <a name="access-data"></a>데이터 액세스

Azure Data Lake Store는 기본적으로 Azure AD 인증을 지원하므로 Azure 리소스에 대한 관리 ID를 사용하여 획득한 액세스 토큰을 직접 수락할 수 있습니다.  Data Lake Store 파일 시스템에 인증하려면 "Bearer &lt;ACCESS_TOKEN_VALUE&gt;" 형식의 권한 부여 헤더에 있는 Data Lake Store 파일 시스템 엔드포인트에 Azure AD에서 발급한 액세스 토큰을 보냅니다.  Azure AD 인증을 위한 Data Lake Store 지원에 대한 자세한 내용을 보려면 [Azure Active Directory를 사용하여 Data Lake Store 인증](../../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)을 참고하세요.

> [!NOTE]
> Data Lake Store 파일 시스템 클라이언트 SDK는 Azure 리소스에 대한 관리 ID를 아직 지원하지 않습니다.  이 자습서는 지원이 SDK에 추가될 때 업데이트됩니다.

이 자습서에서는 REST를 요청하는 PowerShell을 사용하여 Data Lake Store 파일 시스템 REST API에 인증합니다. 인증을 위해 VM의 시스템 할당 관리 ID를 사용하려면 VM에서 요청해야 합니다.

1. Portal에서 **Virtual Machines** -> Windows VM으로 이동한 다음 **개요**에서 **연결**을 클릭합니다.
2. Windows VM을 만들 때 추가한 **사용자 이름**과 **암호**를 입력합니다. 
3. 이제 가상 머신에 대한 **원격 데스크톱 연결**을 만들었으므로 원격 세션에서 **PowerShell**을 엽니다. 
4. PowerShell의 `Invoke-WebRequest`를 사용하여 Azure Data Lake Store에 대한 액세스 토큰을 가져오도록 Azure 리소스 엔드포인트의 로컬 관리 ID에 요청합니다.  Data Lake Store의 리소스 식별자는 `https://datalake.azure.net/`입니다.  Data Lake가 리소스 식별자와 정확히 일치하고 후행 슬래시가 중요합니다.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -Method GET -Headers @{Metadata="true"}
   ```
    
   JSON 개체에서 PowerShell 개체로 응답을 변환합니다. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   응답에서 액세스 토큰을 추출합니다.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. PowerShell의 'Invoke-WebRequest'를 사용하여 Data Lake Store의 REST 엔드포인트에 요청하여 루트 폴더에서 폴더를 나열합니다.  모든 항목이 올바르게 구성되었는지 확인하는 간단한 방법입니다.  인증 헤더의 "Bearer" 문자열에 대문자 "B"가 있어야 합니다.  Azure Portal에 있는 Data Lake Store 블레이드의 **개요** 섹션에서 Data Lake Store의 이름을 찾을 수 있습니다.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   성공적인 응답은 다음과 같습니다.

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. 이제 Data Lake Store에 파일을 업로드해 볼 수 있습니다.  먼저 업로드할 파일을 만듭니다.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. PowerShell의 `Invoke-WebRequest`를 사용하여 이전에 만든 폴더에 파일을 업로드하도록 Data Lake Store의 REST 엔드포인트에 요청합니다.  이 요청은 두 단계로 이루어집니다.  첫 번째 단계에서는 요청하고 파일을 업로드해야 하는 리디렉션을 가져옵니다.  두 번째 단계에서는 실제로 파일을 업로드합니다.  이 자습서와 다른 값을 사용하는 경우 폴더 및 파일의 이름을 적절하게 설정해야 합니다. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   `$HdfsRedirectResponse` 값을 삽입하는 경우 다음과 같은 응답이 표시됩니다.

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   리디렉션 엔드포인트에 요청을 보내어 업로드를 완료합니다.

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   성공적인 응답은 다음과 같습니다.

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

다른 Data Lake Store 파일 시스템 API를 사용하여 파일을 추가하고, 다운로드할 수 있습니다.


## <a name="disable"></a>사용 안 함

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Windows 가상 머신에 대해 시스템 할당 관리 ID를 사용하여 Azure Data Lake Store에 액세스하는 방법을 알아보았습니다. Azure Data Lake Store에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
>[Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md)