---
title: Java 및 Eclipse를 사용하여 Azure 함수 앱 만들기 | Microsoft Docs
description: Java 및 Eclipse를 통해 간단한 HTTP 트리거 서버리스 앱을 만들고 Azure Functions에 게시하는 방법 가이드입니다.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: Azure Functions, 함수, 이벤트 처리, 계산, 서버리스 아키텍처, Java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 123a24eb13de584d8e3b70d0d8b1173f583867c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341442"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Java 및 Eclipse를 사용 하 여 첫 번째 함수 만들기 

이 문서에서는 Eclipse IDE 및 Apache Maven을 통해 [서버리스](https://azure.microsoft.com/solutions/serverless/) 함수 프로젝트를 만들고, 테스트 및 디버그한 다음, Azure Functions에 배포하는 방법을 보여줍니다. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>개발 환경 설정

Java 및 Eclipse를 통해 함수 앱을 개발하려면 다음을 설치해야 합니다.

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/) 버전 8
-  [Apache Maven](https://maven.apache.org) 버전 3.0 이상
-  [Eclipse](https://www.eclipse.org/downloads/packages/)(Java 및 Maven 지원 포함)
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> 이 퀵 스타트를 완료하려면 JAVA_HOME 환경 변수를 JDK 설치 위치로 설정해야 합니다.

Azure Functions를 실행 및 디버그하기 위한 로컬 환경을 제공하는 [Azure Functions Core Tools 버전 2](functions-run-local.md#v2)를 설치하는 것이 좋습니다. 

## <a name="create-a-functions-project"></a>Functions 프로젝트 만들기

1. Eclipse에서 **File**(파일) 메뉴를 선택한 다음 **Project**(프로젝트)를 선택합니다. 
1. **New Project**(새 프로젝트) 창에서 **Java Project** 폴더를 열고 **Maven Project**를 선택한 다음, **Next**(다음)를 선택합니다.
1. **New Maven Project** 대화 상자의 기본값을 그대로 두고 **Next**(다음)를 선택합니다.
1. **Add Archetype**을 선택하고 [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)에 대한 항목을 추가합니다.
    - Archetype Group ID: com.microsoft.azure
    - Archetype Artifact ID: azure-functions-archetype
    - 버전: 사용 하 여 최신 버전 [중앙 리포지토리](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Eclipse Maven 만들기](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. **확인**을 클릭하고 현재 프로젝트에 대한 세부 정보를 입력한 후 **마침**을 클릭합니다.

Maven은 이름이 _artifactId_인 새 폴더에 프로젝트 파일을 만듭니다. 프로젝트에서 생성된 코드는 HTTP 트리거 요청의 본문을 에코하는 간단한 [HTTP 트리거](/azure/azure-functions/functions-bindings-http-webhook) 함수입니다.

## <a name="run-functions-locally-in-the-ide"></a>IDE에서 로컬로 함수 실행

> [!NOTE]
> 로컬에서 함수를 실행하고 디버그하려면 [Azure Functions Core Tools 버전 2](functions-run-local.md#v2)를 설치해야 합니다.

1. 생성된 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **Run As**(다음으로 실행)와 **Maven build**를 선택합니다.
1. **Edit Configuration**(구성 편집) 대화 상자에서 **Goals**(목표) 및 **Name**(이름) 필드에 `package`를 입력한 다음, **Run**(실행)을 선택합니다. 그러면 함수 코드가 빌드되고 패키지됩니다.
1. 빌드가 완료되면 `azure-functions:run`을 목표 및 이름으로 사용하여 위와 같이 다른 실행 구성을 만듭니다. **Run**(실행)을 선택하여 IDE에서 함수를 실행합니다.

함수 테스트가 끝나면 콘솔 창에서 런타임을 종료합니다. 한 번에 하나의 함수 호스트만 활성화되고 로컬로 실행될 수 있습니다.

### <a name="debug-the-function-in-eclipse"></a>Eclipse에서 함수 디버그

이전 단계의 **Run As**(다음으로 실행) 구성 설정에서 `azure-functions:run`을 `mvn azure-functions:run -DenableDebug`로 변경하고 업데이트된 구성을 실행하여 디버그 모드로 함수 앱을 시작합니다.

**Run**(실행) 메뉴를 선택하고 **Debug Configurations**(디버그 구성)을 엽니다. **Remote Java Application**(원격 Java 애플리케이션)을 선택하고 새 항목을 만듭니다. 구성에 이름을 지정하고 설정을 입력합니다. 포트는 함수 호스트에 의해 열린 디버그 포트(기본값: `5005`)와 일치해야 합니다. 설정 후 `Debug`를 클릭하여 디버그를 시작합니다.

![Eclipse에서 함수 디버깅](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

IDE를 사용하여 함수에서 중단점을 설정하고 개체를 검사합니다. 완료되면 디버거 및 실행 중인 함수 호스트를 중지합니다. 한 번에 하나의 함수 호스트만 활성화되고 로컬로 실행될 수 있습니다.

## <a name="deploy-the-function-to-azure"></a>Azure에 함수 배포

Azure Functions에 대한 배포 프로세스는 Azure CLI의 계정 자격 증명을 사용합니다. 컴퓨터의 명령 프롬프트를 계속 사용하기 전에 [Azure CLI를 통해 로그인](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)합니다.

```azurecli
az login
```

새 **Run As**(다음으로 실행) 구성에서 `azure-functions:deploy` Maven 목표를 사용하여 새 함수 앱에 코드를 배포합니다.

배포가 완료되면 Azure 함수 앱에 액세스하는 데 사용할 수 있는 URL이 표시됩니다.

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>다음 단계

- Java 함수 개발에 대한 자세한 내용은 [Java 함수 개발자 가이드](functions-reference-java.md)를 참조합니다.
- `azure-functions:add` Maven 대상을 사용하여 프로젝트에 다른 트리거가 있는 다른 함수를 추가합니다.
