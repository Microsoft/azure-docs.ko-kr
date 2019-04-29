---
title: Jenkins 연속 통합 솔루션과 함께 Azure Storage 사용
description: 이 자습서에서는 Jenkins 연속 통합 솔루션을 사용하여 만든 아티팩트용 리포지토리로 Azure Blob 서비스를 사용하는 방법을 보여 줍니다.
ms.topic: article
ms.author: tarcher
author: tarcher
services: devops
ms.service: storage
custom: jenkins
ms.date: 07/31/2018
ms.subservice: common
ms.openlocfilehash: 8ea80d557185f4489a96384b77ddd2519e7bd049
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477470"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Jenkins 연속 통합 솔루션과 함께 Azure Storage 사용

이 문서에서는 Blob Storage를 Jenkins CI(지속적인 통합) 솔루션에서 만든 빌드 아티팩트의 리포지토리로 사용하거나 빌드 프로세스에 사용할 다운로드 가능 파일의 원본으로 사용하는 방법을 보여 줍니다. 이 솔루션은 민첩한 개발 환경에서 코딩하고(Java 또는 다른 언어 사용) 빌드가 연속 통합을 기반으로 실행 중이며, 다른 조직 구성원이나 고객과 빌드 아티팩트를 공유하거나 보관 파일을 유지 관리할 수 있도록 빌드 아티팩트의 리포지토리가 필요한 경우와 같은 시나리오에서 유용합니다. 다른 시나리오는 빌드 작업 자체에 종속성 등의 다른 파일이 빌드 입력의 다운로드로 필요한 경우입니다.

이 자습서에서는 Microsoft가 제공하는 Jenkins CI용 Azure Storage 플러그 인을 사용하게 됩니다.

## <a name="jenkins-overview"></a>Jenkins 개요
Jenkins는 개발자가 손쉽게 코드 변경 사항을 통합하고 빌드를 자동으로 자주 생성할 수 있게 하여 소프트웨어 프로젝트의 연속 통합을 가능케 함으로써 개발자의 생산성을 높여 줍니다. 빌드에는 버전이 지정되고 빌드 아티팩트는 다양한 리포지토리에 업로드될 수 있습니다. 이 문서에서는 Azure Blob Storage를 빌드 아티팩트의 리포지토리로 사용하는 방법을 보여 줍니다. Azure Blob Storage에서 종속성을 다운로드하는 방법도 보여 줍니다.

Jenkins에 대한 자세한 내용은 [Meet Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)에서 찾을 수 있습니다.

## <a name="benefits-of-using-the-blob-service"></a>Blob service를 사용할 경우의 이점
Blob service를 사용하여 Agile 개발 빌드 아티팩트를 호스트할 경우의 이점은 다음과 같습니다.

* 빌드 아티팩트 및/또는 다운로드 가능 종속성의 고가용성
* Jenkins CI 솔루션이 빌드 아티팩트를 업로드할 때의 성능
* 고객 및 파트너가 빌드 아티팩트를 다운로드할 때의 성능
* 익명 액세스, 만료 기반 공유 액세스 서명 액세스, 전용 액세스 중에서 선택하여 사용자 액세스 정책 제어

## <a name="prerequisites"></a>필수 조건
* Jenkins 연속 통합 솔루션
  
    Jenkins CI 솔루션이 아직 없으면 다음 방법을 통해 Jenkins CI 솔루션을 실행할 수 있습니다.
  
  1. Java를 사용할 수 있는 컴퓨터에서 <https://jenkins-ci.org>로부터 jenkins.war를 다운로드합니다.
  2. jenkins.war를 포함하는 폴더로 열려 있는 명령 프롬프트에서 다음을 실행합니다.
     
      `java -jar jenkins.war`

  3. 브라우저에서 `http://localhost:8080/`을 열면 Azure Storage 플러그 인을 설치 및 구성하게 될 Jenkins 대시보드가 열립니다.
     
      일반적인 Jenkins CI 솔루션은 서비스로 실행하도록 설정되지만, 이 자습서에서는 Jenkins war를 명령줄에서 실행하는 것으로 충분합니다.
* Azure 계정. <https://www.azure.com>에서 Azure 계정을 등록할 수 있습니다.
* Azure 저장소 계정. Storage 계정이 아직 없으면 [Storage 계정 만들기](../common/storage-quickstart-create-account.md)에 설명된 단계를 따라 계정을 만들 수 있습니다.
* Jenkins CI 솔루션에 익숙하면 좋지만 반드시 그러해야 하는 것은 아닙니다. 아래에서는 Jenkins CI 빌드 아티팩트를 위한 리포지토리로 Blob service를 사용할 때 필요한 단계를 보여 주기 위해 기본적인 예를 사용합니다.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Jenkins CI에서 Blob service를 사용하는 방법
Blob service를 Jenkins와 함께 사용하려면 Azure Storage 플러그인을 설치하고 Storage 계정을 사용하도록 플러그인을 구성한 후, 빌드 아티팩트를 Storage 계정에 업로드하는 빌드 후 작업을 만들어야 합니다. 이와 관련된 단계가 다음 섹션에 설명되어 있습니다.

## <a name="how-to-install-the-azure-storage-plugin"></a>Azure Storage 플러그인을 설치하는 방법
1. Jenkins 대시보드에서 **Jenkins 관리**를 선택합니다.
2. **Jenkins 관리** 페이지에서 **플러그 인 관리**를 선택합니다.
3. **가용성** 탭을 선택합니다.
4. **Artifact Uploaders** 섹션에서 **Microsoft Azure Storage plugin**을 확인합니다.
5. **Install without restart**(다시 시작 없이 설치) 또는 **Download now and install after restart**(지금 다운로드 및 다시 시작 후에 설치)를 클릭합니다.
6. Jenkins를 다시 시작합니다.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Storage 계정을 사용하도록 Azure Storage 플러그 인을 구성하는 방법
1. Jenkins 대시보드에서 **Jenkins 관리**를 선택합니다.
2. **Jenkins 관리** 페이지에서 **시스템 구성**을 선택합니다.
3. **Microsoft Azure Storage Account Configuration** 섹션에서:
   1. [Azure Portal](https://portal.azure.com)에서 가져올 수 있는 저장소 계정 이름을 입력합니다.
   2. [Azure Portal](https://portal.azure.com)에서 확인 가능한 저장소 계정 키도 입력합니다.
   3. 전역 Azure 클라우드를 사용 중이면 **Blob 서비스 끝점 URL**에 기본값을 사용합니다. 다른 Azure 클라우드를 사용 중이면 [Azure Portal](https://portal.azure.com) 에서 저장소 계정에 대해 지정된 엔드포인트를 사용합니다. 
   4. **저장소 자격 증명 유효성 검사**를 선택하여 저장소 계정의 유효성을 검사합니다. 
   5. [옵션] Jenkins CI에서 더 많은 스토리지 계정을 사용할 수 있게 하려면 **Add more Storage Accounts**(추가 스토리지 계정 추가)를 선택합니다.
   6. **저장**을 선택하여 설정을 저장합니다.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>빌드 아티팩트를 저장소 계정으로 업로드하는 빌드 후 작업을 만드는 방법
설명할 목적으로 우선 몇 개의 파일을 만들 작업을 만든 다음, 그 파일을 저장소 계정에 업로드할 빌드 후 작업을 추가해야 합니다.

1. Jenkins 대시보드에서 **새 항목**을 선택합니다.
2. 작업 이름을 **MyJob**으로 지정하고 **Build a free-style software project**(자유형 소프트웨어 프로젝트 빌드)를 선택한 후 **확인**를 선택합니다.
3. 작업 구성의 **빌드** 섹션에서 **빌드 단계 추가**를 선택하고 **Windows 배치 명령 실행**을 선택합니다.
4. **Command**에서 다음 명령을 사용합니다.

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. 작업 구성의 **Post-build Actions**(빌드 후 작업) 섹션에서 **Add post-build action**(빌드 후 작업 추가)을 선택하고 **Upload artifacts to Azure Blob storage**(Azure Blob 스토리지로 아티팩트 업로드)를 선택합니다.
6. **Storage account name**에서는 사용할 스토리지 계정을 선택합니다.
7. **Container name**에서 컨테이너 이름을 지정합니다. (컨테이너는 빌드 아티팩트가 업로드될 때 없으면 만들어집니다.) 환경 변수를 사용할 수 있으며, 이 예제에서는 컨테이너 이름으로 `${JOB_NAME}`을 입력합니다.
   
    **팁**
   
    **Execute Windows batch command**의 스크립트를 입력한 **명령** 섹션 아래에는 Jenkins에 의해 인식되는 환경 변수에 대한 링크가 있습니다. 환경 변수 이름과 설명을 알아보려면 이 링크를 선택합니다. **BUILD_URL** 환경 변수와 같은 특수 문자가 포함된 환경 변수는 컨테이너 이름이나 일반 가상 경로로 사용할 수 없습니다.
8. 이 예제에서는 **Make new container public by default**(기본적으로 새 컨테이너를 공용으로 지정)를 선택합니다. (개인 컨테이너를 사용하려는 경우 액세스를 허용하려면 공유 액세스 서명을 만들어야 합니다. 이 내용은 이 문서에서 다루지 않습니다. [SAS(공유 액세스 서명) 사용](../storage-dotnet-shared-access-signature-part-1.md)에서 공유 액세스 서명에 대한 자세한 내용을 알아볼 수 있습니다.)
9. [선택 사항] 빌드 아티팩트를 업로드하기 전에 컨테이너에서 내용을 지우려면 **업로드 전에 컨테이너 정리**를 선택합니다. 컨테이너의 내용을 지우지 않으려면 선택 취소한 상태로 둡니다.
10. **업로드할 아티팩트 목록**에 `text/*.txt`를 입력합니다.
11. **Common virtual path for uploaded artifacts**(업로드된 아티팩트의 일반 가상 경로)에는 이 자습서에서 사용할 `${BUILD\_ID}/${BUILD\_NUMBER}`를 입력합니다.
12. **저장**을 선택하여 설정을 저장합니다.
13. Jenkins 대시보드에서 **지금 빌드**를 선택하여 **MyJob**을 실행합니다. 콘솔 출력을 점검하여 상태를 확인합니다. 빌드 후 작업이 빌드 아티팩트를 업로드하기 시작하면 Azure 저장소 상태 메시지가 콘솔 출력에 포함됩니다.
14. 작업이 성공적으로 완료되었을 때 공용 Blob을 열어 빌드 아티팩트를 검사할 수 있습니다.
    1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
    2. **저장소**를 선택합니다.
    3. Jenkins에 사용한 저장소 계정 이름을 선택합니다.
    4. **컨테이너**를 선택합니다.
    5. **myjob**이라는 이름의 컨테이너를 선택합니다. 이 이름은 Jenkins 작업을 만들 때 할당한 작업 이름의 소문자 버전입니다. 컨테이너 이름 및 Blob 이름은 Azure 저장소에서 소문자(및 대/소문자 구분)입니다. **myjob**라는 컨테이너의 Blob 목록 내에서 **hello.txt** 및 **date.txt**가 표시됩니다. 이 항목 중 하나의 URL을 복사하여 브라우저에서 엽니다. 빌드 아티팩트로 업로드된 텍스트 파일이 표시됩니다.

아티팩트를 Azure Blob Storage에 업로드하는 빌드 후 작업은 작업당 하나만 만들 수 있습니다. 아티팩트를 Azure Blob Storage로 업로드하는 단일 빌드 후 작업에서는 세미콜론을 구분 기호로 사용하여 **업로드할 아티팩트 목록**에서 다른 파일(와일드카드 포함) 및 파일 경로를 지정할 수 있습니다. 예를 들어 Jenkins 빌드에서 작업 영역의 **build** 폴더에 JAR 파일과 TXT 파일을 생성한 상태에서 두 개를 모두 Azure Blob Storage에 업로드하려면 **업로드할 아티팩트 목록** 값에 `build/\*.jar;build/\*.txt`를 사용합니다. 이중 콜론 구문을 사용하여 Blob 이름 내에 사용할 경로를 지정할 수도 있습니다. 예를 들어 Blob 경로에 **이진 파일**을 사용하여 JAR을 업로드하고 Blob 경로에 **통지**를 사용하여 TXT 파일을 업로드하려면 **업로드할 아티팩트 목록** 값에 `build/\*.jar::binaries;build/\*.txt::notices`를 사용합니다.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Azure Blob Storage에서 다운로드하는 빌드 단계를 만드는 방법
다음 단계에서는 Azure Blob Storage에서 항목을 다운로드하는 빌드 단계를 구성하는 방법을 보여 줍니다. 이 작업은 빌드에 항목을 포함하려는 경우에 유용합니다. 이 패턴을 사용하는 예제는 Azure Blob Storage에 유지할 수 있는 JAR입니다.

1. 작업 구성의 **빌드** 섹션에서 **빌드 단계 추가**를 선택한 후 **Azure Blob Storage에서 다운로드**를 선택합니다.
2. **Storage account name**에서는 사용할 스토리지 계정을 선택합니다.
3. **Container name**에서 다운로드할 Blob이 있는 컨테이너의 이름을 지정합니다. 환경 변수를 사용할 수 있습니다.
4. **Blob name**에서 Blob 이름을 지정합니다. 환경 변수를 사용할 수 있습니다. Blob 이름의 첫 부분 글자를 지정한 후에 와일드카드로 별표를 사용할 수도 있습니다. 예를 들어 **프로젝트\\*** 이름의 시작 하는 모든 blob를 지정 하는 **프로젝트**합니다.
5. [옵션] **Download path**에서 Azure Blob Storage로부터 파일을 다운로드할 Jenkins 컴퓨터의 경로를 지정합니다. 환경 변수도 사용할 수 있습니다. ( **Download path**에 값을 제공하지 않으면 Azure Blob Storage의 파일이 작업에 해당하는 작업 영역으로 다운로드됩니다.)

Azure Blob Storage에서 다운로드할 추가 항목이 있는 경우에는 추가 빌드 단계를 만들 수 있습니다.

빌드를 실행하고 나면 빌드 기록 콘솔 출력을 보거나 다운로드 위치를 보고 원하는 Blob이 다운로드되었는지 확인할 수 있습니다.  

## <a name="components-used-by-the-blob-service"></a>Blob service에서 사용하는 구성 요소
이 섹션에서는 Blob Service 구성 요소의 개요를 제공합니다.

* **스토리지 계정**: Azure Storage에 대한 모든 액세스는 Storage 계정을 통해 수행됩니다. 저장소 계정은 Blob 액세스를 위한 가장 높은 수준의 네임스페이스입니다. 전체 크기가 100TB를 초과하지 않을 경우 한 계정에 포함될 수 있는 컨테이너 수는 제한이 없습니다.
* **컨테이너**: 컨테이너는 Blob 집합의 그룹화를 제공합니다. 모든 Blob은 컨테이너에 있어야 합니다. 한 계정에 포함될 수 있는 컨테이너 수에는 제한이 없습니다. 한 컨테이너에 저장될 수 있는 Blob 수에도 제한이 없습니다.
* **Blob**: 임의 형식 및 크기의 파일입니다. Azure Blob Storage 서비스에 저장할 수 있는 Blob 유형에는 블록과 페이지 Blob 두 가지가 있습니다. 대부분의 파일은 블록 Blob입니다. 단일 블록 Blob의 크기는 최대 200GB일 수 있습니다. 이 자습서에서는 블록 Blob을 사용합니다. 다른 Blob 유형인 페이지 Blob의 크기는 최대 1TB일 수 있으며, 파일의 바이트 범위가 자주 수정되는 경우 더 효율적입니다. Blob에 대한 자세한 내용은 [블록 Blob, 추가 Blob 및 페이지 Blob 이해](https://msdn.microsoft.com/library/azure/ee691964.aspx)를 참조하세요.
* **URL 형식**: 다음 URL 형식을 사용하여 Blob에 주소를 지정할 수 있습니다.
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    위의 형식은 전역 Azure 클라우드에 적용됩니다. 다른 Azure 클라우드를 사용 중이면 [Azure Portal](https://portal.azure.com) 내의 엔드포인트를 사용하여 URL 엔드포인트를 확인합니다.)
  
    위 형식에서 `storageaccount`은(는) 저장소 계정 이름을 나타내고, `container_name`은(는) 컨테이너 이름을 나타내고, `blob_name`은(는) Blob 이름을 각각 나타냅니다. 컨테이너 이름 내에 슬래시( **/**(영문)에서 찾을 수 있습니다. 이 자습서에서 사용되는 컨테이너 이름의 예는 **MyJob**이고 **${BUILD\_ID}/${BUILD\_NUMBER}** 는 일반 가상 경로에 사용되었으므로 Blob의 URL 형식은 다음과 같습니다.
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Jenkins 플러그 인 문제 해결

Jenkins 플러그 인에서 버그가 발생하면 [Jenkins JIRA](https://issues.jenkins-ci.org/)에서 특정 구성 요소에 대한 문제를 제출해 주세요.

## <a name="next-steps"></a>다음 단계
* [Meet Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
* [Java용 Azure Storage SDK](https://github.com/azure/azure-storage-java)
* [Azure Storage 클라이언트 SDK 참조](http://dl.windowsazure.com/storage/javadoc/)
* [Azure Storage 서비스 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Storage 팀 블로그](https://blogs.msdn.com/b/windowsazurestorage/)

자세한 내용은 [Java 개발자용 Azure](/java/azure)를 방문하세요.
