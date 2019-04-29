---
title: '.NET SDK: Azure Data Lake Storage Gen1에서의 파일 시스템 작업 | Microsoft Docs'
description: Azure Data Lake Storage Gen1 .NET SDK를 사용하여 Data Lake Storage Gen1에서 폴더 만들기 등의 파일 시스템 작업을 수행할 수 있습니다.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 02091f1b650e3e9932f9924bf36a5841861d3b1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878854"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>.NET SDK를 사용한 Azure Data Lake Storage Gen1에서의 파일 시스템 작업
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

이 문서에서는 .NET SDK를 사용하여 Data Lake Storage Gen1에서 파일 시스템 작업을 수행하는 방법을 알아봅니다. 파일 시스템 작업에는 Data Lake Storage Gen1 계정에 폴더 만들기, 파일 업로드, 파일 다운로드 등이 포함됩니다.

.NET SDK를 사용하여 Data Lake Storage Gen1에서 계정 관리 작업을 수행하는 방법에 대한 지침은 [.NET SDK를 사용한 Data Lake Storage Gen1에서의 계정 관리 작업](data-lake-store-get-started-net-sdk.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
* **Visual Studio 2013, 2015 또는 2017**. 아래 지침에서는 Visual Studio 2017을 사용합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure Data Lake Storage Gen1 계정**. 계정을 만드는 방법에 대한 지침은 [Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)을 참조하세요.

## <a name="create-a-net-application"></a>.NET 애플리케이션 만들기
[GitHub에서](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) 사용 가능한 코드 샘플은 저장소에서 파일 만들기, 파일 연결, 파일 다운로드 및 저장소에서 파일 삭제와 같은 프로세스를 설명합니다. 문서의 이 섹션은 코드의 주요 부분을 설명합니다.

1. Visual Studio를 열고 콘솔 애플리케이션을 만듭니다.
2. **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.
3. **새 프로젝트**에서 다음 값을 입력하거나 선택합니다.

   | 자산 | 값 |
   | --- | --- |
   | Category |Templates/Visual C#/Windows |
   | Template |콘솔 애플리케이션 |
   | 이름 |CreateADLApplication |

4. **확인**을 클릭하여 프로젝트를 만듭니다.

5. NuGet 패키지를 프로젝트에 추가합니다.

   1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.
   2. **NuGet 패키지 관리자** 탭에서 **패키지 원본**이 **nuget.org**로 설정되어 있고 **시험판 포함** 확인란이 선택되어 있는지 확인합니다.
   3. 다음 NuGet 패키지를 검색하고 설치합니다.

      * `Microsoft.Azure.DataLake.Store` - 이 자습서는 v1.0.0을 사용합니다.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - 이 자습서는 v2.3.1을 사용합니다.
    
      **NuGet 패키지 관리자**를 닫습니다.

6. **Program.cs**를 열고 기존 코드를 삭제한 후 다음 문을 포함시켜서 네임스페이스에 대한 참조를 추가합니다.

        using System;
        using System.IO;using System.Threading;
        using System.Linq;
        using System.Text;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.DataLake.Store;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. 아래 표시된 것처럼 변수를 선언하고 자리 표시자에 대한 값을 제공합니다. 또한, 여기에 제공하는 로컬 경로와 파일 이름이 컴퓨터에 존재해야 합니다.

        namespace SdkSample
        {
            class Program
            {
                private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";        
            }
        }

문서의 나머지 섹션에서는 제공되는 .NET 메서드를 사용하여 인증, 파일 업로드 등의 작업을 수행하는 방법을 볼 수 있습니다.

## <a name="authentication"></a>Authentication

* 응용 프로그램에 대한 최종 사용자 인증의 경우 [.NET SDK를 사용한 Data Lake Storage Gen1의 최종 사용자 인증](data-lake-store-end-user-authenticate-net-sdk.md)을 참조하세요.
* 응용 프로그램에 대한 서비스 간 인증의 경우 [.NET SDK를 사용한 Data Lake Storage Gen1의 서비스 간 인증](data-lake-store-service-to-service-authenticate-net-sdk.md)을 참조하세요.


## <a name="create-client-object"></a>클라이언트 개체 만들기
다음 코드 조각은 이는 서비스에 요청을 발급하는 데 사용되는 Data Lake Storage Gen1 파일 시스템 클라이언트 개체를 만듭니다.

    // Create client objects
    AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);

## <a name="create-a-file-and-directory"></a>파일 및 디렉터리 만들기
애플리케이션에 다음 코드 조각을 추가합니다. 이 코드 조각은 파일을 추가할 뿐만 아니라 존재하지 않는 모든 부모 디렉터리도 추가합니다.

    // Create a file - automatically creates any parent directories that don't exist
    // The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store
    using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);

        textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="append-to-a-file"></a>파일에 추가
다음 코드 조각은 Data Lake Storage Gen1 계정의 기존 파일에 데이터를 추가합니다.

    // Append to existing file
    using (var stream = client.GetAppendStream(fileName))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="read-a-file"></a>파일 읽기
다음 코드 조각은 Data Lake Storage Gen1에서 파일 내용을 읽습니다.

    //Read file contents
    using (var readStream = new StreamReader(client.GetReadStream(fileName)))
    {
        string line;
        while ((line = readStream.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }

## <a name="get-file-properties"></a>파일 속성 가져오기
다음 코드 조각은 파일 또는 디렉터리와 연결된 속성을 반환합니다.

    // Get file properties
    var directoryEntry = client.GetDirectoryEntry(fileName);
    PrintDirectoryEntry(directoryEntry);

`PrintDirectoryEntry` 메서드의 정의는 [GitHub에서](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) 샘플의 일부로 사용할 수 있습니다. 

## <a name="rename-a-file"></a>파일 이름 바꾸기
다음 코드 조각은 Data Lake Storage Gen1 계정의 기존 파일 이름을 바꿉니다.

    // Rename a file
    string destFilePath = "/Test/testRenameDest3.txt";
    client.Rename(fileName, destFilePath, true);

## <a name="enumerate-a-directory"></a>디렉터리 열거
다음 코드 조각은 Data Lake Storage Gen1 계정의 디렉터리를 열거합니다.

    // Enumerate directory
    foreach (var entry in client.EnumerateDirectory("/Test"))
    {
        PrintDirectoryEntry(entry);
    }

`PrintDirectoryEntry` 메서드의 정의는 [GitHub에서](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) 샘플의 일부로 사용할 수 있습니다.

## <a name="delete-directories-recursively"></a>디렉터리를 재귀적으로 삭제
다음 코드 조각은 디렉터리 및 모든 해당 하위 디렉터리를 재귀적으로 삭제합니다.

    // Delete a directory and all its subdirectories and files
    client.DeleteRecursive("/Test");

## <a name="samples"></a>샘플
Data Lake Storage Gen1 파일 시스템 SDK를 사용하는 방법에 대한 몇 가지 샘플은 다음과 같습니다.
* [GitHub의 기본 샘플](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [GitHub의 고급 샘플](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>참고 항목
* [.NET SDK를 사용한 Data Lake Storage Gen1의 계정 관리 작업](data-lake-store-get-started-net-sdk.md)
* [Data Lake Storage Gen1 .NET SDK 참조](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>다음 단계
* [Data Lake Storage Gen1의 데이터 보호](data-lake-store-secure-data.md)
