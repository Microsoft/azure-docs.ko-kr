---
title: 'Java SDK: Azure Data Lake Storage Gen1에서의 파일 시스템 작업 | Microsoft Docs'
description: Azure Data Lake Storage Gen1 Java SDK를 사용하여 Data Lake Storage Gen1에서 폴더 만들기 등의 파일 시스템 작업을 수행하는 방법을 설명합니다.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bc6e0718cdc4ccb18480dc760279da9c177db4cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60877467"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-java-sdk"></a>Java SDK를 사용한 Azure Data Lake Storage Gen1에서의 파일 시스템 작업
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Azure Data Lake Storage Gen1 Java SDK를 사용하여 폴더 만들기, 데이터 파일 업로드 및 다운로드 등의 기본 작업을 수행하는 방법에 대해 알아봅니다. Data Lake Storage Gen1에 대한 자세한 내용은 [Azure Data Lake Storage Gen1](data-lake-store-overview.md)을 참조하세요.

[Azure Data Lake Storage Gen1 Java API 문서](https://azure.github.io/azure-data-lake-store-java/javadoc/)에서 Data Lake Storage Gen1용 Java SDK API 문서에 액세스할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* Java 개발 키트(JDK 7 이상, Java 버전 1.7 이상 사용)
* Data Lake Storage Gen1 계정 [Azure Portal을 사용하여 Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)에 있는 지침을 따릅니다.
* [Maven](https://maven.apache.org/install.html) 이 자습서에서는 빌드 및 프로젝트 종속성을 위해 Maven을 사용합니다. Maven 또는 Gradle과 같은 빌드 시스템을 사용하지 않고 빌드할 수 있지만 이러한 시스템 생성은 종속성을 훨씬 쉽게 관리할 수 있습니다.
* (선택 사항)[IntelliJ IDEA](https://www.jetbrains.com/idea/download/) 또는 [Eclipse](https://www.eclipse.org/downloads/)나 유사한 IDE

## <a name="create-a-java-application"></a>Java 애플리케이션 만들기
[GitHub에서](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) 사용 가능한 코드 샘플은 저장소에서 파일 만들기, 파일 연결, 파일 다운로드 및 저장소에서 파일 삭제와 같은 프로세스를 설명합니다. 문서의 이 섹션은 코드의 주요 부분을 설명합니다.

1. 명령줄에서 [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)을 사용하거나 IDE에서 Maven 프로젝트를 만듭니다. IntelliJ를 사용하여 Java 프로젝트를 만드는 방법에 대한 자세한 내용은 [여기](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html)를 참조하세요. Eclipse를 사용하여 프로젝트를 만드는 방법에 대한 자세한 내용은 [여기](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm)를 참조하세요. 

2. Maven **pom.xml** 파일에 다음 종속성을 추가합니다. **\</project>** 태그 앞에 다음 코드 조각을 추가합니다.
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.5</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    첫 번째 종속성은 Maven 리포지토리에서 Data Lake Storage Gen1 SDK(`azure-data-lake-store-sdk`)를 사용하는 것입니다. 두 번째 종속성은 이 애플리케이션에 사용하는 로깅 프레임워크(`slf4j-nop`)를 지정하는 것입니다. Data Lake Storage Gen1 SDK는 [slf4j](https://www.slf4j.org/) 로깅 외관을 사용하며 이로 인해 log4j, Java 로깅, logback 혹은 로깅 없음과 같이 널리 사용되는 여러 로깅 프레임워크 중에서 선택할 수 있습니다. 이 예제에서 로깅을 비활성화하므로 **slf4j-nop** 바인딩을 사용합니다. 앱에서 다른 로깅 옵션을 사용하려면 [여기](https://www.slf4j.org/manual.html#projectDep)를 참조하세요.

3. 애플리케이션에 다음 import 문을 추가합니다.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

        import java.io.*;
        import java.util.Arrays;
        import java.util.List;

## <a name="authentication"></a>Authentication

* 애플리케이션에 대한 최종 사용자 인증의 경우 [Java를 사용한 Data Lake Storage Gen1의 최종 사용자 인증](data-lake-store-end-user-authenticate-java-sdk.md)을 참조하세요.
* 애플리케이션에 대한 서비스 간 인증의 경우 [Java를 사용한 Data Lake Storage Gen1의 서비스 간 인증](data-lake-store-service-to-service-authenticate-java.md)을 참조하세요.

## <a name="create-a-data-lake-storage-gen1-client"></a>Data Lake Storage Gen1 클라이언트 만들기
[ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) 개체를 만들 때는 Data Lake Storage Gen1에 인증할 때 생성한 Data Lake Storage Gen1 계정 이름 및 토큰 공급자를 지정해야 합니다([인증](#authentication) 섹션 참조). Data Lake Storage Gen1 계정 이름은 정규화된 도메인 이름이어야 합니다. 예를 들어 **FILL-IN-HERE**는 **mydatalakestoragegen1.azuredatalakestore.net**과 같은 이름으로 바꿉니다.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

다음 섹션의 코드 조각에는 몇 가지 일반적인 파일 시스템 작업의 예가 포함되어 있습니다. 다른 작업을 확인하려면 **ADLStoreClient** 개체의 전체 [Data Lake Storage Gen1 Java SDK API 문서](https://azure.github.io/azure-data-lake-store-java/javadoc/) 개체를 살펴볼 수 있습니다.

## <a name="create-a-directory"></a>디렉터리 만들기

다음 코드 조각은 지정한 Data Lake Storage Gen1 계정의 루트에서 디렉터리 구조를 만듭니다.

    // create directory
    client.createDirectory("/a/b/w");
    System.out.println("Directory created.");

## <a name="create-a-file"></a>파일 만들기

다음 코드 조각은 디렉터리 구조에 파일(c.txt)을 만들고 파일에 일부 데이터를 씁니다.

    // create file and write some content
    String filename = "/a/b/c.txt";
    OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
    PrintStream out = new PrintStream(stream);
    for (int i = 1; i <= 10; i++) {
        out.println("This is line #" + i);
        out.format("This is the same line (%d), but using formatted output. %n", i);
    }
    out.close();
    System.out.println("File created.");

또한 바이트 배열을 사용하여 파일(d.txt)를 만들 수 있습니다.

    // create file using byte arrays
    stream = client.createFile("/a/b/d.txt", IfExists.OVERWRITE);
    byte[] buf = getSampleContent();
    stream.write(buf);
    stream.close();
    System.out.println("File created using byte array.");

위의 코드 조각에 사용된 `getSampleContent` 함수에 대한 정의는 [GitHub에서](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) 샘플의 일부로 사용할 수 있습니다. 

## <a name="append-to-a-file"></a>파일에 추가

다음 코드 조각은 기존 파일에 콘텐츠를 추가합니다.

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();
    System.out.println("File appended.");

위의 코드 조각에 사용된 `getSampleContent` 함수에 대한 정의는 [GitHub에서](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) 샘플의 일부로 사용할 수 있습니다.

## <a name="read-a-file"></a>파일 읽기

다음 코드 조각은 Data Lake Storage Gen1 계정의 파일에서 콘텐츠를 읽습니다.

    // Read File
    InputStream in = client.getReadStream(filename);
    BufferedReader reader = new BufferedReader(new InputStreamReader(in));
    String line;
    while ( (line = reader.readLine()) != null) {
        System.out.println(line);
    }
    reader.close();
    System.out.println();
    System.out.println("File contents read.");

## <a name="concatenate-files"></a>파일 연결

다음 코드 조각은 Data Lake Storage Gen1 계정의 두 파일을 연결합니다. 성공하면 연결된 파일은 두 개의 기존 파일을 대체합니다.

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);
    System.out.println("Two files concatenated into a new file.");

## <a name="rename-a-file"></a>파일 이름 바꾸기

다음 코드 조각은 Data Lake Storage Gen1 계정의 파일 이름을 바꿉니다.

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");
    System.out.println("New file renamed.");

## <a name="get-metadata-for-a-file"></a>파일에 대한 메타데이터 가져오기

다음 코드 조각은 Data Lake Storage Gen1 계정의 파일 메타데이터를 검색합니다.

    // get file metadata
    DirectoryEntry ent = client.getDirectoryEntry(filename);
    printDirectoryInfo(ent);
    System.out.println("File metadata retrieved.");

## <a name="set-permissions-on-a-file"></a>파일에 권한 설정

다음 코드 조각은 이전 섹션에서 만든 파일에 권한을 설정합니다.

    // set file permission
    client.setPermission(filename, "744");
    System.out.println("File permission set.");

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

다음 코드 조각은 재귀적으로 디렉터리의 콘텐츠를 나열합니다.

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }
    System.out.println("Directory contents listed.");

위의 코드 조각에 사용된 `printDirectoryInfo` 함수에 대한 정의는 [GitHub에서](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) 샘플의 일부로 사용할 수 있습니다.

## <a name="delete-files-and-folders"></a>파일 및 폴더 삭제

다음 코드 조각은 Data Lake Storage Gen1 계정에서 지정된 파일 및 폴더를 재귀적으로 삭제합니다.

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");
    System.out.println("All files and folders deleted recursively");
    promptEnterKey();

## <a name="build-and-run-the-application"></a>애플리케이션 빌드 및 실행
1. IDE 내에서 실행하려면 **실행** 단추를 찾아 누릅니다. Maven을 실행하려면 [exec:exec](https://www.mojohaus.org/exec-maven-plugin/exec-mojo.html)을 사용합니다.
2. 명령줄 빌드에서 실행할 수 있는 독립 실행형 jar을 생성하려면 [Maven 어셈블리 플러그 인](https://maven.apache.org/plugins/maven-assembly-plugin/usage.html)을 사용하여 포함된 모든 종속성을 가진 jar을 빌드합니다. [GitHub의 소스 코드 예제](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml)에서 pom.xml에 예제가 있습니다.

## <a name="next-steps"></a>다음 단계
* [Java SDK에 대한 JavaDoc 탐색](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Data Lake Storage Gen1의 데이터 보호](data-lake-store-secure-data.md)


