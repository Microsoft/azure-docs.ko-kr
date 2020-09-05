---
title: Windows VM 시스템 할당 관리 ID를 사용하여 Azure Storage에 액세스 | Microsoft Docs
description: Windows VM 시스템 할당 관리 ID를 사용하여 Azure Storage에 액세스하는 프로세스를 단계별로 안내하는 자습서입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: de1cc69b3cfdac307edf6dfe999a5d538c2cb811
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89263181"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage"></a>자습서: Windows VM 시스템 할당 관리 ID를 사용하여 Azure Storage에 액세스

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Windows VM(가상 머신)에 대한 시스템 할당 관리 ID를 사용하여 Azure Storage에 액세스하는 방법을 보여줍니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 스토리지 계정에 Blob 컨테이너 만들기
> * 스토리지 계정에 Windows VM의 시스템 할당 관리 ID 액세스 부여
> * 액세스 가져오기 및 액세스를 사용하여 Azure Storage 호출

> [!NOTE]
> Azure Storage에 대한 Azure Active Directory 인증은 공개 미리 보기 상태입니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]



## <a name="enable"></a>사용

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>액세스 권한 부여


### <a name="create-storage-account"></a>스토리지 계정 만들기

이 섹션에서는 스토리지 계정을 만듭니다.

1. Azure Portal의 왼쪽 위에 있는 **+ 리소스 만들기** 단추를 클릭합니다.
2. **스토리지**를 클릭한 다음, **스토리지 계정 - Blob, 파일, 테이블, 큐**를 클릭합니다.
3. **이름** 아래에서 스토리지 계정의 이름을 입력합니다.
4. **배포 모델** 및 **계정 종류**는 **리소스 관리자** 및 **스토리지(범용 v1)** 로 설정해야 합니다.
5. **구독** 및 **리소스 그룹**은 이전 단계에서 VM을 만들 때 지정한 것과 일치합니다.
6. **만들기**를 클릭합니다.

    ![새 스토리지 계정 만들기](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

### <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Blob 컨테이너 만들기 및 스토리지 계정에 파일 업로드

파일에 Blob Storage가 필요하므로 파일을 저장할 Blob 컨테이너를 만들어야 합니다. 그런 다음, 새 스토리지 계정에서 Blob 컨테이너에 파일을 업로드합니다.

1. 새로 만든 스토리지 계정으로 다시 이동합니다.
2. **Blob Service**에서 **컨테이너**를 클릭합니다.
3. 페이지 맨 위에서 **+ 컨테이너**를 클릭합니다.
4. **새 컨테이너** 아래에서 컨테이너에 대한 이름을 입력하고 **공용 액세스 수준** 아래에서 기본값을 유지합니다.

    ![스토리지 컨테이너 만들기](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. 선택한 편집기를 사용하여 로컬 컴퓨터에서 *hello world.txt*라는 파일을 만듭니다. 파일을 열고 "Hello world! :)"라는 텍스트(따옴표 제외)를 추가한 다음, 저장합니다.
6. 컨테이너 이름을 클릭한 다음, **업로드**를 클릭하여 새로 만든 컨테이너에 파일을 추가합니다.
7. **Blob 업로드** 창의 **파일** 아래에서 폴더 아이콘을 클릭하고 로컬 컴퓨터에서 **hello_world.txt**라는 파일을 찾고, 파일을 선택한 다음, **업로드**를 클릭합니다.
    ![텍스트 파일 업로드](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

### <a name="grant-access"></a>액세스 권한 부여

이 섹션에서는 VM에 Azure Storage 컨테이너에 대한 액세스 권한을 부여하는 방법을 보여줍니다. VM의 시스템 할당 관리 ID를 사용하여 Azure Storage Blob에서 데이터를 검색할 수 있습니다.

1. 새로 만든 스토리지 계정으로 다시 이동합니다.
2. 왼쪽 패널의 **액세스 제어(IAM)** 링크를 클릭합니다.
3. 페이지의 위쪽에서 **+ 역할 할당 추가**를 클릭하여 VM에 대한 새 역할 할당을 추가합니다.
4. **역할**의 드롭다운에서 **Storage Blob 데이터 판독기**를 선택합니다.
5. 다음 드롭다운의 **다음에 대한 액세스 할당** 아래에서 **가상 머신**을 선택합니다.
6. 다음으로 적절한 구독이 **구독** 드롭다운에 나열되는지 확인한 다음, **리소스 그룹**을 **모든 리소스 그룹**으로 설정합니다.
7. **선택** 아래에서 VM을 선택한 다음, **저장**을 클릭합니다.

    ![권한 할당](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="access-data"></a>데이터 액세스 

Azure Storage는 기본적으로 Azure AD 인증을 지원하므로 관리 ID를 사용하여 획득한 액세스 토큰을 직접 수락할 수 있습니다. 이 방법은 Azure Storage를 Azure AD와 통합하는 작업의 일부로, 연결 문자열에서 자격 증명을 제공하는 것과는 다릅니다.

다음은 액세스 토큰을 사용한 다음, 이전에 만든 파일의 콘텐츠를 읽어 Azure Storage에 대한 연결을 여는 .NET 코드의 예제입니다. VM의 관리 ID 엔드포인트에 액세스하기 위해서는 VM에 대해 이 코드를 실행해야 합니다. 액세스 토큰 방법을 사용하려면 .NET Framework 4.6 이상이 필요합니다. `<URI to blob file>`의 값을 적절하게 바꿉니다. 만들고 Blob Storage로 업로드한 파일로 이동하고 **속성** 아래의 **URL**을 **개요** 페이지로 복사하여 이 값을 가져올 수 있습니다.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;
using System.Net;
using System.Web.Script.Serialization;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;

namespace StorageOAuthToken
{
    class Program
    {
        static void Main(string[] args)
        {
            //get token
            string accessToken = GetMSIToken("https://storage.azure.com/");

            //create token credential
            TokenCredential tokenCredential = new TokenCredential(accessToken);

            //create storage credentials
            StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

            Uri blobAddress = new Uri("<URI to blob file>");

            //create block blob using storage credentials
            CloudBlockBlob blob = new CloudBlockBlob(blobAddress, storageCredentials);

            //retrieve blob contents
            Console.WriteLine(blob.DownloadText());
            Console.ReadLine();
        }

        static string GetMSIToken(string resourceID)
        {
            string accessToken = string.Empty;
            // Build request to acquire MSI token
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=" + resourceID);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";

            try
            {
                // Call /token endpoint
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader, and extract access token
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                JavaScriptSerializer j = new JavaScriptSerializer();
                Dictionary<string, string> list = (Dictionary<string, string>)j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
                accessToken = list["access_token"];
                return accessToken;
            }
            catch (Exception e)
            {
                string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                return accessToken;
            }
        }
    }
}
```

응답에는 파일의 내용이 포함됩니다.

`Hello world! :)`


## <a name="disable"></a>사용 안 함

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>다음 단계

이 자습서에서는 Windows VM의 시스템 할당 ID를 활성화하여 Azure Storage에 액세스하는 방법을 알아보았습니다.  Azure Storage에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Azure Storage](../../storage/common/storage-introduction.md)