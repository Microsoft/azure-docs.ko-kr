---
title: 배포 자격 증명 구성
description: Azure App Service에 있는 배포 자격 증명의 유형과이를 구성 하 고 사용 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: a9d875e2c3899fa91b9cc41c0ee3b5a93ec5b8c8
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843789"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Azure App Service의 배포 자격 증명 구성
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)는 [로컬 Git 배포](deploy-local-git.md) 및 [FTP/S 배포](deploy-ftp.md)를 위해 두 가지 유형의 자격 증명을 지원합니다. 이러한 자격 증명은 Azure 구독 자격 증명과 동일 하지 않습니다.

* **사용자 수준 자격 증명** - 전체 Azure 계정에 대한 자격 증명 집합입니다. 모든 구독에서 Azure 계정에 액세스할 수 있는 권한이 있는 모든 앱의 App Service에 배포하는 데 사용할 수 있습니다. 포털 GUI(예: 앱 [리소스 페이지](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)의 **개요** 및 **속성**)에 표시되는 기본 집합입니다. 사용자에 게 RBAC (역할 기반 Access Control) 또는 공동 관리자 권한을 통해 앱에 대한 액세스 권한이 부여 되 면 해당 사용자는 액세스 권한이 취소 될 때까지 자신의 사용자 수준 자격 증명을 사용할 수 있습니다. 이러한 자격 증명을 다른 Azure 사용자와 공유하지 마세요.

* **앱 수준 자격 증명** - 각 앱마다 하나씩의 자격 증명 집합입니다. 해당 앱에만 배포하는 데 사용할 수 있습니다. 각 앱의 자격 증명은 앱 생성 시 자동으로 생성됩니다. 수동으로 구성할 수 없지만, 언제든지 다시 설정할 수 있습니다. 사용자에 게 (RBAC)를 통해 앱 수준 자격 증명에 대한 액세스 권한을 부여 하려면 해당 사용자가 앱의 기여자 이상 이어야 합니다 (웹 사이트 참가자 기본 제공 역할 포함). 읽기 권한자는 게시할 수 없으므로 자격 증명에 액세스할 수 없습니다.

## <a name="userscope"></a>사용자 수준 자격 증명 구성

모든 앱의 [리소스 페이지](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)에서 사용자 수준 자격 증명을 구성할 수 있습니다. 이러한 자격 증명을 구성하는 앱에 관계 없이 Azure 계정의 모든 앱과 모든 구독에 적용됩니다. 

### <a name="in-the-cloud-shell"></a>Cloud Shell

[Cloud Shell](https://shell.azure.com)에서 배포 사용자를 구성 하려면 [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) 명령을 실행 합니다. \<username> 및 \<password>를 배포 사용자 이름 및 암호로 바꿉니다. 

- 사용자 이름은 Azure 내에서 고유해야 하고, 로컬 Git 푸시의경우 ' @' 기호를 포함하면 안 됩니다. 
- 암호는 글자, 숫자, 기호의 세 가지 요소 중 두 가지를 사용하고 8자 이상이어야 합니다. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON 출력에는 암호가 `null`로 나옵니다. `'Conflict'. Details: 409` 오류가 발생하면 사용자 이름을 변경합니다. `'Bad Request'. Details: 400` 오류가 발생하면 더 강력한 암호를 사용합니다. 

### <a name="in-the-portal"></a>포털에서

Azure Portal에서 배포 자격 증명 페이지에 액세스 하려면 앱이 하나 이상 있어야 합니다. 사용자 수준 자격 증명을 구성하려면

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **App Services** >  **\<Any_app** > **Deployment center** > **FTP** > **대시보드**를 선택 합니다.

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    또는 Git 배포를 이미 구성한 경우 **App Services** >  **&lt;Any_app >**  > **배포 센터** > **FTP/자격 증명**을 선택 합니다.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. **사용자 자격 증명**을 선택 하 고 사용자 이름 및 암호를 구성한 다음 **자격 증명 저장**을 선택 합니다.

배포 자격 증명을 설정한 후에는 앱의 **개요** 페이지에서 *Git* 배포 사용자 이름을 찾을 수 있습니다.

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Git 배포가 구성 된 경우 페이지는 **git/배포 사용자 이름을**표시 합니다. 그렇지 않으면 **FTP/배포 사용자 이름**입니다.

> [!NOTE]
> Azure에서는 사용자 수준 배포 암호가 표시되지 않습니다. 암호를 잊은 경우 이 섹션의 단계에 따라 자격 증명을 다시 설정할 수 있습니다.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>FTP/FTPS와 함께 사용자 수준 자격 증명 사용

사용자 수준 자격 증명을 사용 하 여 FTP/FTPS 엔드포인트에 인증 requirers 다음과 같은 형식으로 사용자 이름을 지정 합니다. `<app-name>\<user-name>`

사용자 수준 자격 증명은 특정 리소스가 아니라 사용자에 게 연결 되기 때문에 로그인 작업을 올바른 앱 엔드포인트으로 보내기 위해 사용자 이름이이 형식 이어야 합니다.

## <a name="appscope"></a>앱 수준 자격 증명 정보 가져오기 및 다시 설정
앱 수준 자격 증명을 가져오려면

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **App Services** >  **&lt;Any_app** > **배포 센터** > **FTP/자격 증명**을 선택 합니다.

2. **앱 자격 증명**을 선택 하 고 **복사** 링크를 선택 하 여 사용자 이름 또는 암호를 복사 합니다.

앱 수준 자격 증명을 다시 설정 하려면 동일한 대화 상자에서 **자격 증명 재설정** 을 선택 합니다.

## <a name="next-steps"></a>다음 단계

이러한 자격 증명을 사용하여 [로컬 Git](deploy-local-git.md)에서 또는 [FTP/S](deploy-ftp.md)를 통해 앱을 배포하는 방법을 알아봅니다.
