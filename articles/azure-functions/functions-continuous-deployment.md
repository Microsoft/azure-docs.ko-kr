---
title: Azure Functions에 대한 연속 배포 | Microsoft 문서
description: Azure App Service의 연속 배포 기능을 사용하여 Azure Functions를 게시합니다.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: fd8fa690c508b8bf748490668c1e9aaa811ac247
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731303"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions에 대한 연속 배포
Azure Functions를 사용하면 App Service 연속 통합을 사용하여 함수 앱을 쉽게 배포할 수 있습니다. Functions는 BitBucket, Dropbox, GitHub 및 Azure DevOps와 통합됩니다. 따라서 Azure에사 이러한 통합된 서비스 트리거 배포 중 하나를 사용하여 함수 코드가 업데이트된 워크플로를 구현할 수 있습니다. Azure Functions를 처음 접하는 경우 [Azure 함수 개요](functions-overview.md)로 시작합니다.

연속 배포는 여러 개의 빈번한 작성자가 통합되는 프로젝트에 적합한 옵션입니다. 또한 함수 코드에서 소스 제어를 유지 관리할 수 있습니다. 현재 지원되는 배포 원본은 다음과 같습니다.

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* 외부 리포지토리(Git 또는 Mercurial)
* [Git 로컬 리포지토리](../app-service/deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Azure DevOps](https://azure.microsoft.com/services/devops/)

배포는 함수 앱별로 구성됩니다. 연속 배포가 활성화된 후 포털에서 함수 코드에 대한 액세스는 *읽기 전용*으로 설정됩니다.

## <a name="continuous-deployment-requirements"></a>연속 배포 요구 사항

연속 배포를 설정하기 전에 구성된 배포 원본 및 배포 원본에 함수 코드가 있어야 합니다. 지정된 함수 앱 배포에서 각 함수는 명명된 하위 디렉터리에 있으며, 여기서 디렉터리 이름은 함수의 이름입니다.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Azure DevOps에서 배포할 수 있으려면 먼저 Azure 구독과 Azure DevOps 조직을 연결해야 합니다. 자세한 내용은 [Set up billing for your Azure DevOps organization](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal)(Azure DevOps 조직에 대한 청구 설정)을 참조하세요.

## <a name="set-up-continuous-deployment"></a>연속 배포 설정
다음 절차를 사용하여 기존 함수 앱에 대한 연속 배포를 구성합니다. 다음 단계에서는 GitHub 리포지토리와의 통합을 보여 주지만 Azure DevOps 또는 기타 배포 서비스에도 유사한 단계가 적용됩니다.

1. [Azure Portal](https://portal.azure.com)의 함수 앱에서 **플랫폼 기능** 및 **배포 옵션**을 클릭합니다. 
   
    ![연속 배포 설정](./media/functions-continuous-deployment/setup-deployment.png)
 
2. 그런 다음 **배포** 블레이드에서 **설정**을 클릭합니다.
 
    ![연속 배포 설정](./media/functions-continuous-deployment/setup-deployment-1.png)
   
3. **배포 원본** 블레이드에서 **원본 선택**을 클릭한 다음 선택한 배포 원본에 대한 정보를 입력하고 **확인**을 클릭합니다.
   
    ![배포 원본 선택](./media/functions-continuous-deployment/choose-deployment-source.png)

연속 배포가 구성된 후 배포 원본의 모든 파일 변경 내용이 함수 앱으로 복사되고 전체 사이트 배포가 트리거됩니다. 원본의 파일이 업데이트될 때 사이트가 다시 배포됩니다.

## <a name="deployment-options"></a>배포 옵션

다음은 몇 가지 일반적인 배포 시나리오입니다.

- [스테이징 배포 만들기](#staging)
- [기존 함수를 연속 배포로 이동](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>스테이징 배포 만들기

Function App은 배포 슬롯을 지원하지 않습니다. 그러나 연속 통합을 사용하여 별도 스테이징 및 프로덕션 배포를 계속 관리할 수 있습니다.

구성할 프로세스 및 스테이징 배포 작업은 일반적으로 다음과 같습니다.

1. 구독에서 프로덕션 코드와 스테이징에 대한 두 개의 함수 앱을 만듭니다. 

2. 아직 없는 경우 배포 원본을 만듭니다. 이 예제에서는 [GitHub]를 사용합니다.

3. 프로덕션 함수 앱의 경우 **연속 배포 설정**에서 앞의 단계를 완료하고 GitHub 리포지토리의 마스터 분기에 배포 분기를 설정합니다.
   
    ![배포 분기 선택](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. 스테이징 함수 앱에 대해 이 단계를 반복하지만 GitHub 리포지토리 대신 스테이징 분기를 선택합니다. 배포 원본이 분기를 지원하지 않는 경우 다른 폴더를 사용합니다.
    
5. 스테이징 분기 또는 폴더에서 코드를 업데이트한 후 스테이징 배포에 이러한 변경 내용이 반영되었는지 확인합니다.

6. 테스트 후 스테이징 분기의 변경 내용을 마스터 분기로 병합합니다. 이렇게 병합하면 프로덕션 함수 앱으로 트리거 배포가 트리거됩니다. 배포 원본이 분기를 지원하지 않는 경우 스테이징 폴더의 파일로 프로덕션 폴더의 파일을 덮어씁니다.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>기존 함수를 연속 배포로 이동
포털에서 만들고 유지 관리하는 기존 함수가 있는 경우 위에서 설명한 대로 연속 배포를 설정하려면 먼저 FTP 또는 로컬 Git 리포지토리를 사용하여 기존 함수 코드 파일을 다운로드해야 합니다. 함수 앱에 대한 App Service 설정에서 이 작업을 수행할 수 있습니다. 파일을 다운로드한 후 선택한 연속 배포 원본에 업로드할 수 있습니다.

> [!NOTE]
> 연속 통합을 구성한 후 함수 포털에서 원본 파일을 더 이상 편집할 수 없습니다.

- [방법: 배포 자격 증명 구성](#credentials)
- [방법: FTP를 사용하여 파일 다운로드](#downftp)
- [방법: 로컬 Git 리포지토리를 사용하여 파일 다운로드](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>방법: 배포 자격 증명 구성
FTP 또는 로컬 Git 리포지토리가 있는 함수 앱에서 파일을 다운로드하려면 먼저 사이트에 액세스할 수 있도록 자격 증명을 구성해야 합니다. 자격 증명은 함수 앱 수준에서 설정됩니다. 다음 단계를 사용하여 Azure Portal에서 배포 자격 증명을 설정합니다.

1. [Azure Portal](https://portal.azure.com)의 함수 앱에서 **플랫폼 기능** 및 **배포 자격 증명**을 클릭합니다.
   
    ![로컬 배포 자격 증명 설정](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. 사용자 이름 및 암호를 입력한 다음 **저장**을 클릭합니다. 이제 해당 자격 증명을 사용하여 FTP 또는 기본 제공 Git 리포지토리에서 함수 앱에 액세스할 수 있습니다.

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>방법: FTP를 사용하여 파일 다운로드

1. [Azure Portal](https://portal.azure.com)의 함수 앱에서 **플랫폼 기능**을 클릭하고 **속성**을 클릭한 다음 **FTP/배포 사용자**, **FTP 호스트 이름** 및 **FTPS 호스트 이름** 값을 복사합니다.  

    FTP 서버에 적절한 컨텍스트를 제공하기 위해 앱 이름을 포함하여 포털에서 표시한 대로 **FTP/배포 사용자**를 입력해야 합니다.
   
    ![배포 정보 가져오기](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. FTP 클라이언트에서 수집한 연결 정보를 사용하여 앱에 연결하고 함수에 대한 원본 파일을 다운로드합니다.

<a name="downgit"></a>
#### <a name="how-to-download-files-using-a-local-git-repository"></a>방법: 로컬 Git 리포지토리를 사용하여 파일 다운로드

1. [Azure Portal](https://portal.azure.com)의 함수 앱에서 **플랫폼 기능** 및 **배포 옵션**을 클릭합니다. 
   
    ![연속 배포 설정](./media/functions-continuous-deployment/setup-deployment.png)
 
2. 그런 다음 **배포** 블레이드에서 **설정**을 클릭합니다.
 
    ![연속 배포 설정](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. **배포 원본** 블레이드에서 **로컬 Git 리포지토리**를 클릭한 다음 **확인**을 클릭합니다.

3. **플랫폼 기능**에서 **속성**을 클릭하고 Git URL의 값을 기록해 둡니다. 
   
    ![연속 배포 설정](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Git 인식 명령 프롬프트 또는 즐겨 찾는 Git 도구를 사용하여 로컬 컴퓨터에서 리포지토리를 복제합니다. Git 복제 명령은 다음과 같이 표시됩니다.
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. 다음 예제와 같이 함수 앱의 파일을 로컬 컴퓨터의 복제로 가져옵니다.
   
        git pull origin master
   
    요청된 경우 [구성된 배포 자격 증명](#credentials)을 제공합니다.  

[GitHub]: https://github.com/

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions에 대한 모범 사례](functions-best-practices.md)
