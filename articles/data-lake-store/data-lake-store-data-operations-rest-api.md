---
title: 'REST API: Azure Data Lake Storage Gen1에서의 파일 시스템 작업 | Microsoft Docs'
description: WebHDFS REST API를 사용하여 Azure Data Lake Storage Gen1에서 파일 시스템 작업 수행
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
ms.openlocfilehash: 351c92f1e1a698893f61004d523ba79ebca253e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878786"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>REST API를 사용한 Azure Data Lake Storage Gen1에서의 파일 시스템 작업
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

이 문서에서는 WebHDFS REST API 및 Data Lake Storage Gen1 REST API를 사용하여 Azure Data Lake Storage Gen1에서 파일 시스템 작업을 수행하는 방법을 알아봅니다. REST API를 사용하여 Data Lake Storage Gen1에서 계정 관리 작업을 수행하는 방법에 대한 지침은 [REST API를 사용한 Data Lake Storage Gen1에서의 계정 관리 작업](data-lake-store-get-started-rest-api.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure Data Lake Storage Gen1 계정**. [Azure Portal을 사용하여 Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)에 있는 지침을 따릅니다.

* **[cURL](https://curl.haxx.se/)**. 이 문서에서는 cURL을 사용하여 Data Lake Storage Gen1 계정에 대해 REST API 호출을 수행하는 방법을 설명합니다.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Azure Active Directory를 사용하여 인증하려면 어떻게 해야 하나요?
Azure Active Directory를 사용한 인증에는 두 가지 접근 방식이 있습니다.

* 애플리케이션에 대한 최종 사용자 인증의 경우(대화형) [.NET SDK를 사용한 Data Lake Storage Gen1에서의 최종 사용자 인증](data-lake-store-end-user-authenticate-rest-api.md)을 참조하세요.
* 애플리케이션에 대한 서비스 간 인증의 경우(비대화형) [.NET SDK를 사용한 Data Lake Storage Gen1에서의 서비스 간 인증](data-lake-store-service-to-service-authenticate-rest-api.md)을 참조하세요.


## <a name="create-folders"></a>폴더 만들기
이 작업은 [여기](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory)에 정의된 WebHDFS REST API 호출을 기반으로 합니다.

다음 cURL 명령을 사용합니다. **\<yourstorename>** 을 Data Lake Storage Gen1 계정 이름으로 바꿉니다.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

이전 명령에서 \<`REDACTED`\>를 이전에 검색한 권한 부여 토큰으로 바꿉니다. 이 명령은 Data Lake Storage Gen1 계정의 루트 폴더에 **mytempdir**이라는 디렉터리를 만듭니다.

작업이 성공적으로 완료되면 다음 코드 조각과 같은 응답이 표시됩니다.

    {"boolean":true}

## <a name="list-folders"></a>폴더 나열
이 작업은 [여기](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory)에 정의된 WebHDFS REST API 호출을 기반으로 합니다.

다음 cURL 명령을 사용합니다. **\<yourstorename>** 을 Data Lake Storage Gen1 계정 이름으로 바꿉니다.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'

이전 명령에서 \<`REDACTED`\>를 이전에 검색한 권한 부여 토큰으로 바꿉니다.

작업이 성공적으로 완료되면 다음 코드 조각과 같은 응답이 표시됩니다.

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "<GUID>",
            "group": "<GUID>"
        }]
    }
    }

## <a name="upload-data"></a>데이터 업로드
이 작업은 [여기](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File)에 정의된 WebHDFS REST API 호출을 기반으로 합니다.

다음 cURL 명령을 사용합니다. **\<yourstorename>** 을 Data Lake Storage Gen1 계정 이름으로 바꿉니다.

    curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'

이전 구문에서 **-T** 매개 변수는 업로드 중인 파일의 위치 입니다.

다음 코드 조각과 유사하게 출력됩니다.
   
    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
    ...
    Content-Length: 0

    HTTP/1.1 100 Continue

    HTTP/1.1 201 Created
    ...

## <a name="read-data"></a>데이터 읽기
이 작업은 [여기](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File)에 정의된 WebHDFS REST API 호출을 기반으로 합니다.

Data Lake Storage Gen1 계정에서 데이터를 읽는 작업은 2단계 프로세스입니다.

* 먼저 엔드포인트 `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`에 대해 GET 요청을 제출합니다. 이 호출은 다음 GET 요청을 제출할 위치를 반환합니다.
* 그러면 엔드포인트 `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`에 대해 GET 요청을 제출합니다. 이 호출은 파일 콘텐츠를 표시합니다.

그러나 첫 번째 단계와 두 번째 단계 간에 입력 매개 변수의 차이가 없으므로 `-L` 매개 변수를 사용하여 첫 번째 요청을 제출할 수 있습니다. `-L` 옵션은 기본적으로 두 요청을 하나로 결합하며 cURL이 새 위치에서 요청을 다시 실행하도록 만듭니다. 마지막으로, 모든 요청 호출의 출력이 다음 코드 조각에 나온 것처럼 표시됩니다. **\<yourstorename>** 을 Data Lake Storage Gen1 계정 이름으로 바꿉니다.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'

다음 코드 조각과 유사한 결과가 표시됩니다.

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file"></a>파일 이름 바꾸기
이 작업은 [여기](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory)에 정의된 WebHDFS REST API 호출을 기반으로 합니다.

파일의 이름을 바꾸려면 다음 cURL 명령을 사용합니다. **\<yourstorename>** 을 Data Lake Storage Gen1 계정 이름으로 바꿉니다.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

다음 코드 조각과 유사한 결과가 표시됩니다.

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>파일 삭제
이 작업은 [여기](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory)에 정의된 WebHDFS REST API 호출을 기반으로 합니다.

파일을 삭제하려면 다음 cURL 명령을 사용합니다. **\<yourstorename>** 을 Data Lake Storage Gen1 계정 이름으로 바꿉니다.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

다음과 유사한 출력이 표시됩니다.

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>다음 단계
* [REST API를 사용한 Data Lake Storage Gen1에서의 계정 관리 작업](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>참고 항목
* [Azure Data Lake Storage Gen1 REST API 참조](https://docs.microsoft.com/rest/api/datalakestore/)
* [Azure Data Lake Storage Gen1과 호환되는 오픈 소스 빅 데이터 애플리케이션](data-lake-store-compatible-oss-other-applications.md)

