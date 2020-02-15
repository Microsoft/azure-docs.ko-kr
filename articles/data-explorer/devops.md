---
title: Azure 데이터 탐색기에 대 한 azure DevOps 작업
description: 이 항목에서는 릴리스 파이프라인을 만들고 배포 하는 방법에 대해 알아봅니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 6394d7149bd4e80f0a17a59a6259eedf4c806fd4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188171"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure 데이터 탐색기에 대 한 azure DevOps 작업

[Azure DevOps Services](https://azure.microsoft.com/services/devops/) 는 고성능 파이프라인, 무료 개인 Git 리포지토리, 구성 가능한 간판 보드, 광범위 한 자동화 된 연속 테스트 기능 등 개발 공동 작업 도구를 제공 합니다. [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) 은 모든 언어, 플랫폼 및 클라우드에서 작동 하는 고성능 파이프라인을 사용 하 여 코드를 배포 하기 위해 CI/CD를 관리할 수 있는 Azure devops 기능입니다.
[Azure 데이터 탐색기-관리자 명령은](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) 릴리스 파이프라인을 만들고 azure 데이터 탐색기 데이터베이스에 데이터베이스 변경 내용을 배포 하는 데 사용할 수 있는 Azure Pipelines 작업입니다. [Visual Studio Marketplace](https://marketplace.visualstudio.com/)에서 무료로 사용할 수 있습니다.

이 문서에서는 **Azure 데이터 탐색기 – Admin Commands** 작업을 사용 하 여 스키마 변경 내용을 데이터베이스에 배포 하는 방법에 대 한 간단한 예를 설명 합니다. 전체 CI/CD 파이프라인은 [Azure DevOps 설명서](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Azure 데이터 탐색기 클러스터 설정:
    * [Azure 데이터 탐색기 클러스터 및 데이터베이스](/azure/data-explorer/create-cluster-database-portal)
    * [AZURE ad 응용 프로그램을 프로 비전](/azure/kusto/management/access-control/how-to-provision-aad-app)하 여 azure ad (Azure Active Directory) 앱을 만듭니다.
    * Azure [데이터 탐색기 데이터베이스 사용 권한을 관리](/azure/data-explorer/manage-database-permissions)하 여 azure 데이터 탐색기 데이터베이스에서 Azure AD 앱에 대 한 액세스 권한을 부여 합니다.
* Azure DevOps 설치:
    * [무료 조직 등록](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [조직 만들기](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Azure DevOps에서 프로젝트 만들기](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Git를 사용 하는 코드](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>폴더 만들기

Git 리포지토리에서 다음 샘플 폴더 (*함수*, *정책*, *테이블*)를 만듭니다. 아래 표시 된 대로 [여기](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) 에서 해당 폴더에 파일을 복사 하 고 변경 내용을 커밋합니다. 다음 워크플로를 실행 하기 위한 샘플 파일이 제공 됩니다.

![폴더 만들기](media/devops/create-folders.png)

> [!TIP]
> 사용자 고유의 워크플로를 만들 때 코드를 idempotent 하는 것이 좋습니다. 예를 들어 create [table](/azure/kusto/management/create-table-command)대신 [. create-merge 테이블](/azure/kusto/management/create-table-command#create-merge-table) 을 사용 하 [고. create 함수 대신](/azure/kusto/management/functions#create-function) [create-또는-alter](/azure/kusto/management/functions#create-or-alter-function) 함수를 사용 합니다.

## <a name="create-a-release-pipeline"></a>릴리스 파이프라인 만들기

1. [Azure DevOps 조직](https://dev.azure.com/)에 로그인 합니다.
1. 왼쪽 메뉴에서 **파이프라인** > **릴리스** 를 선택 하 고 **새 파이프라인**을 선택 합니다.

    ![새 파이프라인](media/devops/new-pipeline.png)

1. **새 릴리스 파이프라인** 창이 열립니다. **파이프라인** 탭의 **템플릿 선택** 창에서 **빈 작업**을 선택 합니다.

     ![템플릿 선택](media/devops/select-template.png)

1. **스테이지** 단추를 선택 합니다. **스테이지** 창에서 **스테이지 이름을**추가 합니다. **저장** 을 선택 하 여 파이프라인을 저장 합니다.

    ![단계 이름](media/devops/stage-name.png)

1. **아티팩트 추가** 단추를 선택 합니다. **아티팩트 추가** 창에서 코드가 있는 리포지토리를 선택 하 고 관련 정보를 입력 한 다음 **추가**를 클릭 합니다. **저장** 을 선택 하 여 파이프라인을 저장 합니다.

    ![아티팩트 추가](media/devops/add-artifact.png)

1. **변수** 탭에서 **+ 추가** 를 선택 하 여 작업에 사용 될 **끝점 URL** 에 대 한 변수를 만듭니다. 끝점의 **이름과** **값** 을 씁니다. **저장** 을 선택 하 여 파이프라인을 저장 합니다. 

    ![변수 만들기](media/devops/create-variable.png)

    Endpoint_URL를 찾기 위해 Azure Portal에서 **azure 데이터 탐색기 클러스터** 의 개요 페이지에는 azure 데이터 탐색기 클러스터 URI가 포함 되어 있습니다. 다음 형식으로 URI를 생성 `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`합니다.  예: https:\//kustodocs.westus.kusto.windows.net? DatabaseName = SampleDB

    ![Azure 데이터 탐색기 클러스터 URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>배포할 작업 만들기

1. **파이프라인** 탭에서 **1 작업, 0 작업** 을 클릭 하 여 작업을 추가 합니다. 

    ![작업 추가](media/devops/add-task.png)

1. **테이블**, **함수**및 **정책을**이 순서로 배포 하는 세 가지 작업을 만듭니다. 

1. **작업** 탭에서 **에이전트 작업**에 의해 **+** 를 선택 합니다. **Azure 데이터 탐색기**를 검색합니다. **Marketplace**에서 **Azure 데이터 탐색기 – Admin 명령** 확장을 설치 합니다. 그런 다음 **Azure 데이터 탐색기 실행 명령**에서 **추가** 를 선택 합니다.

     ![관리자 명령 추가](media/devops/add-admin-commands.png)

1. 왼쪽에서 **Kusto 명령을** 클릭 하 고 다음 정보를 사용 하 여 작업을 업데이트 합니다.
    * **표시 이름**: 작업의 이름입니다.
    * **파일 경로**: 테이블 태스크에서 테이블 생성 파일이 *테이블* 폴더에 있기 때문 **에 테이블 태스크** 에서 */proable/* . csl을 지정 합니다.
    * **끝점 URL**: 이전 단계에서 만든 `EndPoint URL`변수를 입력 합니다.
    * **서비스 끝점 사용** 을 선택 하 고 **+ 새로 만들기**를 선택 합니다.

    ![Kusto 명령 작업 업데이트](media/devops/kusto-command-task.png)

1. **Azure 데이터 탐색기 서비스 연결 추가** 창에서 다음 정보를 완료 합니다.

    |설정  |제안 값  |
    |---------|---------|
    |**연결 이름**     |    이 서비스 끝점을 식별 하는 이름 입력     |
    |**클러스터 Url**    |    값은 Azure Portal의 Azure 데이터 탐색기 클러스터의 개요 섹션에서 찾을 수 있습니다. | 
    |**서비스 사용자 Id**    |    AAD 앱 ID (필수 구성 요소로 만들어짐)를 입력 합니다.     |
    |**서비스 사용자 앱 키**     |    AAD 앱 키 (필수 구성 요소로 만들어짐)를 입력 합니다.    |
    |**AAD 테 넌 트 Id**    |      AAD 테 넌 트 (예: microsoft.com, contoso.com)를 입력 합니다.    |

    **모든 파이프라인에서이 연결을 사용 하도록 허용 확인란을** 선택 합니다. **확인**을 선택합니다.

    ![서비스 연결 추가](media/devops/add-service-connection.png)

1. 1-5 단계를 두 번 반복 하 여 *함수* 및 *정책* 폴더에서 파일을 배포 합니다. **저장**을 선택합니다. **작업** 탭에서 만든 세 가지 태스크 인 **테이블 배포**, **함수 배포**및 **정책 배포**를 참조 하세요.

    ![모든 폴더 배포](media/devops/deploy-all-folders.png)

1. **+ 릴리스** > **릴리스** 만들기를 선택 합니다.

    ![릴리스 만들기](media/devops/create-release.png)

1. **로그** 탭에서 배포 상태가 성공 인지 확인 합니다.

    ![배포 성공](media/devops/deployment-successful.png)

이제 사전 프로덕션에 세 개의 작업을 배포 하기 위한 릴리스 파이프라인을 만들었습니다.