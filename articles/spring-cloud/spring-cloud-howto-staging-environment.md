---
title: Azure Spring Cloud에서 스테이징 환경 설정 | Microsoft Docs
description: Azure Spring Cloud에서 청록색 배포를 사용하는 방법 알아보기
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 5612a514ed89f73453f3751b34263b0beeea1c59
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138136"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Azure 스프링 클라우드에서 스테이징 환경 설정

이 문서에서는 Azure 스프링 클라우드의 파랑-녹색 배포 패턴을 사용 하 여 스테이징 배포를 설정 하는 방법을 설명 합니다. 파란색/녹색 배포는 새로운(녹색) 버전이 배포되는 동안 기존(파란색) 버전을 유지하도록 하는 Azure DevOps 지속적인 업데이트 패턴입니다. 이 문서에서는 프로덕션 배포를 직접 변경 하지 않고 스테이징 배포를 프로덕션 환경에 배치 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 [Azure 스프링 클라우드 응용 프로그램을 시작 하는 방법에 대 한 자습서](spring-cloud-quickstart-launch-app-portal.md)에서 PiggyMetrics 응용 프로그램을 이미 배포 했다고 가정 합니다. PiggyMetrics는 "게이트웨이", "계정-서비스" 및 "인증 서비스"의 세 가지 응용 프로그램으로 구성 됩니다.  

이 예제에 다른 응용 프로그램을 사용 하려는 경우 응용 프로그램의 공용 부분을 간단히 변경 해야 합니다.  이러한 변경을 통해 스테이징 배포와 프로덕션을 구분합니다.

>[!TIP]
> Azure Cloud Shell은이 문서의 지침을 실행 하는 데 사용할 수 있는 무료 대화형 셸입니다.  최신 버전의 Git, JDK, Maven 및 Azure CLI를 포함 하는 미리 설치 된 공통 Azure 도구를 포함 합니다. Azure 구독에 로그인 하는 경우 [Azure Cloud Shell](https://shell.azure.com)를 시작 합니다.  자세히 알아보려면 [Azure Cloud Shell 개요](../cloud-shell/overview.md)를 참조 하세요.

Azure 스프링 클라우드에서 스테이징 환경을 설정 하려면 다음 섹션의 지침을 따르세요.

## <a name="install-the-azure-cli-extension"></a>Azure CLI 확장 설치

다음 명령을 사용하여 Azure CLI용 Azure Spring Cloud 확장을 설치합니다.

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>모든 배포 보기

Azure Portal에서 서비스 인스턴스로 이동 하 고 **배포 관리** 를 선택 하 여 모든 배포를 확인 합니다. 세부 정보를 보려면 각 배포를 선택할 수 있습니다.

## <a name="create-a-staging-deployment"></a>스테이징 배포 만들기

1. 로컬 개발 환경에서 PiggyMetrics 게이트웨이 응용 프로그램을 약간만 수정 합니다. 예를 들어 *게이트웨이/src/main/resources/static/css/launch .css* 파일의 색을 변경 합니다. 이렇게 하면 두 배포를 쉽게 구분할 수 있습니다. Jar 패키지를 빌드하려면 다음 명령을 실행 합니다. 

    ```azurecli
    mvn clean package
    ```

1. Azure CLI에서 새 배포를 만들고 스테이징 배포 이름 "green"을 지정 합니다.

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. 배포가 성공적으로 완료 되 면 **응용 프로그램 대시보드에서**게이트웨이 페이지에 액세스 하 고 왼쪽의 **앱 인스턴스** 탭에서 모든 인스턴스를 확인 합니다.
  
> [!NOTE]
> 확인이 완료 되기 전에 트래픽이이 배포로 라우팅되지 않도록 검색 상태가 *OUT_OF_SERVICE* 됩니다.

## <a name="verify-the-staging-deployment"></a>스테이징 배포 확인

1. **배포 관리** 페이지로 돌아가서 새 배포를 선택 합니다. 배포 상태가 *실행 중*으로 표시되어야 합니다. 환경이 스테이징 환경 이므로 **도메인 할당/** 할당 해제 단추가 회색으로 표시 됩니다.

1. **개요** 창에 **테스트 끝점이**표시 됩니다. 새 브라우저 창에 복사 하 여 붙여넣고 새 PiggyMetrics 페이지가 표시 됩니다.

>[!TIP]
> * 테스트 끝점이 슬래시 (/)로 끝나는지 확인 하 여 CSS 파일이 올바르게 로드 되는지 확인 합니다.  
> * 브라우저에서 페이지를 보기 위해 로그인 자격 증명을 입력해야 하는 경우, [URL 디코드](https://www.urldecoder.org/)를 사용하여 테스트 엔드포인트를 디코드합니다. URL 디코드는 "https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green" 형식의 URL을 반환합니다.  이 형식을 사용 하 여 끝점에 액세스 합니다.

>[!NOTE]    
> 구성 서버 설정은 스테이징 환경과 프로덕션 모두에 적용 됩니다. 예를 들어 구성 서버에서 앱 게이트웨이의 컨텍스트 경로 (`server.servlet.context-path`)를 *somepath*로 설정 하는 경우 녹색 배포의 경로는 "https://\<username >:\<password > @\<cluster-name >. i n t e r/w o w s/somepath/..."로 변경 됩니다.
 
 이 시점에서 공용 앱 게이트웨이를 방문 하면 새 변경 없이 이전 페이지가 표시 됩니다.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>녹색 배포를 프로덕션 환경으로 설정

1. 스테이징 환경에서 변경 내용을 확인 한 후 프로덕션으로 푸시할 수 있습니다. **배포 관리**로 돌아가서 **게이트웨이** 응용 프로그램 확인란을 선택 합니다.

2. **배포 설정**을 선택 합니다.
3. **프로덕션 배포** 목록에서 **녹색**을 선택 하 고 **적용**을 선택 합니다.
4. 게이트웨이 애플리케이션 **개요** 페이지로 이동합니다. 게이트웨이 응용 프로그램에 대 한 도메인을 이미 할당 한 경우 URL은 **개요** 창에 표시 됩니다. 수정 된 PiggyMetrics 페이지를 보려면 URL을 선택 하 고 사이트로 이동 합니다.

>[!NOTE]
> 녹색 배포를 프로덕션 환경으로 설정한 후에는 이전 배포가 스테이징 배포가 됩니다.

## <a name="modify-the-staging-deployment"></a>스테이징 배포 수정

변경에 만족 하지 않는 경우 응용 프로그램 코드를 수정 하 고 새 jar 패키지를 작성 한 다음 Azure CLI를 사용 하 여 녹색 배포에 업로드할 수 있습니다.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>스테이징 배포 삭제

Azure 포트에서 스테이징 배포를 삭제 하려면 스테이징 배포 페이지로 이동한 후 **삭제** 단추를 선택 합니다.

또는 다음 명령을 실행 하 여 Azure CLI에서 스테이징 배포를 삭제 합니다.

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
