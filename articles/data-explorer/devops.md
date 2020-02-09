---
title: Azure 데이터 탐색기에 대한 azure DevOps 작업
description: 이 항목에서는 릴리스 파이프라인을 만들고 배포 설명
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 0628d5c07d7258cc4d68727c364e65bd81c78e8e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388988"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure 데이터 탐색기에 대한 azure DevOps 작업

[Azure DevOps 서비스](https://azure.microsoft.com/services/devops/) 개발 고성능 파이프라인, 무료 비공개 Git 리포지토리, 구성 가능한 Kanban 보드 및 광범위 한 자동화 되 고 연속 테스트 기능 같은 공동 작업 도구를 제공 합니다. [Azure 파이프라인](https://azure.microsoft.com/services/devops/pipelines/) 모든 언어, 플랫폼 및 클라우드 사용 되는 고성능 파이프라인을 사용 하 여 코드를 배포 하는 CI/CD를 관리할 수 있도록 Azure DevOps 기능입니다.
[Azure 데이터 탐색기-관리 명령을](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) 릴리스 파이프라인을 만들고 데이터베이스를 배포할 수 있도록 Azure 파이프라인 작업에 Azure 데이터 탐색기 데이터베이스로 변경 됩니다. 무료로 사용 가능 합니다 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)합니다.

이 문서에서는 사용 하는 간단한 예제를 설명 합니다 **Azure 데이터 탐색기-관리자 명령** 데이터베이스 스키마를 배포 하는 작업을 변경 합니다. 전체 CI/CD 파이프라인에 대한 참조 [Azure DevOps 설명서](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts)합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Azure의 데이터 탐색기 클러스터 설정:
    * [Azure 데이터 탐색기 클러스터 및 데이터베이스](/azure/data-explorer/create-cluster-database-portal)
    * Azure Active Directory (Azure AD) 앱을 만듭니다 [Azure AD 응용 프로그램을 프로 비전](/azure/kusto/management/access-control/how-to-provision-aad-app)합니다.
    * 액세스 하 여 Azure 데이터 탐색기 데이터베이스에서 Azure AD 앱에 권한을 부여할 [Azure 데이터 탐색기 데이터베이스 사용 권한 관리](/azure/data-explorer/manage-database-permissions)합니다.
* Azure DevOps 설정:
    * [무료 조직에 등록](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [조직 만들기](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Azure devops에서 프로젝트 만들기](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Git 사용 하 여 코드](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>폴더 만들기

다음 샘플 폴더를 만듭니다 (*함수*를 *정책을*를 *테이블*) Git 리포지토리에 있습니다. 파일을 복사 [여기](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) 해당 폴더에 표시 된 아래에 변경 내용을 커밋합니다. 다음 워크플로 실행 하는 샘플 파일이 제공 됩니다.

![폴더 만들기](media/devops/create-folders.png)

> [!TIP]
> 사용자 고유의 워크플로 만들 때에 사용자 코드 멱 등 원이 되도록 하는 것이 좋습니다. 사용 예를 들어 [속하지만 병합 테이블](/azure/kusto/management/tables#create-merge-tables) of [속하지만 테이블](/azure/kusto/management/tables#create-table)를 사용 하 여 [속하지만 또는 alter](/azure/kusto/management/functions#create-or-alter-function) 함수 대신 [속하지만](/azure/kusto/management/functions#create-function) 함수입니다.

## <a name="create-a-release-pipeline"></a>릴리스 파이프라인을 만들려면

1. 에 로그인 하 여 [Azure DevOps 조직](https://dev.azure.com/)합니다.
1. 선택 **파이프라인** > **릴리스** 왼쪽 메뉴에서 선택 **새 파이프라인**합니다.

    ![새 파이프라인](media/devops/new-pipeline.png)

1. 합니다 **새로 만들기 릴리스 파이프라인** 창이 열립니다. 에 **파이프라인** 탭의 **템플릿을 선택** 선택 창 **빈 작업**.

     ![템플릿 선택](media/devops/select-template.png)

1. 선택 **스테이지** 단추입니다. **단계** 창에 추가 합니다 **단계 이름**합니다. 선택 **저장할** 파이프라인을 저장 합니다.

    ![단계 이름](media/devops/stage-name.png)

1. 선택 **아티팩트 추가** 단추입니다. 에 **아티팩트 추가** 창 코드 있는 리포지토리를 선택 합니다. 관련 정보를 입력 하 고 클릭 **추가**합니다. 선택 **저장할** 파이프라인을 저장 합니다.

    ![아티팩트 추가](media/devops/add-artifact.png)

1. 에 **변수** 탭을 선택 **+ 추가** 에 대한 변수를 만들려면 **엔드포인트 URL** 태스크에서 사용할 합니다. 쓰기를 **이름을** 하며 **값** 엔드포인트의 합니다. 선택 **저장할** 파이프라인을 저장 합니다. 

    ![변수 만들기](media/devops/create-variable.png)

    Endpoint_URL의 개요 페이지에 찾으려는 사용자 **Azure 데이터 탐색기 클러스터** azure에서 포털 Azure 데이터 탐색기 클러스터 URI를 포함 합니다. 다음 형식의 URI를 생성 `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`합니다.  예를 들어, https:\//kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![Azure 데이터 탐색기 클러스터 URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>배포 하는 작업 만들기

1. 에 **파이프라인** 탭을 클릭 **1 작업, 0 작업** 태스크를 추가 합니다. 

    ![작업 추가](media/devops/add-task.png)

1. 배포 하려면 세 가지 작업을 만듭니다 **테이블**를 **함수**, 및 **정책**를이 순서 대로 합니다. 

1. 에 **태스크** 탭을 선택 **+** 하 여 **에이전트 작업**합니다. **Azure 데이터 탐색기**를 검색합니다. **Marketplace**를 설치 합니다 **Azure 데이터 탐색기-관리 명령을** 확장 합니다. 그런 다음 선택 **추가** 에 **Azure 데이터 탐색기 명령 실행**합니다.

     ![관리자 명령 추가](media/devops/add-admin-commands.png)

1. 클릭할 **Kusto 명령** 왼쪽에 다음 정보를 사용 하 여 작업 업데이트:
    * **표시 이름**: 태스크의 이름
    * **파일 경로**: 에 **테이블** 작업을 지정 */Tables/* .csl 테이블 만들기 파일이 있으므로 합니다 *테이블* 폴더.
    * **엔드포인트 URL**: 입력 된 `EndPoint URL`이전 단계에서 만든 변수입니다.
    * 선택 **사용 하 여 서비스 엔드포인트** 선택한 **+ 새로 만들기**합니다.

    ![Kusto 명령 작업 업데이트](media/devops/kusto-command-task.png)

1. 다음 정보를 완료 합니다 **서비스 연결 추가 Azure 데이터 탐색기** 창:

    |설정  |제안 값  |
    |---------|---------|
    |**연결 이름**     |    이 서비스 엔드포인트을 식별 하는 이름을 입력 합니다.     |
    |**클러스터 Url**    |    Azure portal에서 Azure 데이터 탐색기 클러스터의 개요 섹션에서 값을 찾을 수 있습니다. | 
    |**서비스 주체 Id**    |    AAD 앱 ID (필수 조건으로 생성 됨)를 입력 합니다.     |
    |**서비스 주체 앱 키**     |    (필수 조건으로 생성 됨) 하 여 AAD 앱 키를 입력 합니다.    |
    |**AAD 테넌트 Id**    |      AAD 테넌트 (예: microsoft.com contoso.com...)를 입력 합니다.    |

    선택 **이 연결을 사용 하는 모든 파이프라인 허용** 확인란을 선택 합니다. **확인**을 선택합니다.

    ![서비스 연결 추가](media/devops/add-service-connection.png)

1. 단계를 반복 1-5 다른 두 번의 파일을 배포 하는 *함수* 하 고 *정책을* 폴더. **저장**을 선택합니다. 에 **작업** 탭에서 만든 세 가지 작업을 참조 하세요. **테이블 배포**, **Functions 배포**, 및 **정책을 배포할**합니다.

    ![모든 폴더를 배포 합니다.](media/devops/deploy-all-folders.png)

1. 선택 **+ 릴리스** > **릴리스 만들기** 릴리스를 만드는 데 있습니다.

    ![릴리스를 만듭니다.](media/devops/create-release.png)

1. 에 **로그** 탭에서 성공적으로 배포 상태를 확인 합니다.

    ![배포가 되었습니다.](media/devops/deployment-successful.png)

이제 세 개의 태스크를 사전 프로덕션 배포에 대한 릴리스 파이프라인을 만들기를 완료 했습니다.