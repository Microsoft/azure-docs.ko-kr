---
title: 배포 자격 증명 구성 - Azure App Service | Microsoft Docs
description: Azure App Service 배포 자격 증명 사용 방법을 알아봅니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnoc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/10/2019
ms.author: cephalin;byvinyal
ms.custom: seodec18
ms.openlocfilehash: df874ab77c88f05b048b1f9d10873943b7bebf36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765484"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Azure App Service의 배포 자격 증명 구성
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)는 [로컬 Git 배포](deploy-local-git.md) 및 [FTP/S 배포](deploy-ftp.md)를 위해 두 가지 유형의 자격 증명을 지원합니다. 이러한 자격 증명에 Azure Active Directory 자격 증명과 동일 않습니다.

* **사용자 수준 자격 증명** - 전체 Azure 계정에 대한 자격 증명 집합입니다. 모든 구독에서 Azure 계정에 액세스할 수 있는 권한이 있는 모든 앱의 App Service에 배포하는 데 사용할 수 있습니다. 포털 GUI(예: 앱 [리소스 페이지](../azure-resource-manager/manage-resources-portal.md#manage-resources)의 **개요** 및 **속성**)에 표시되는 기본 집합입니다. RBAC(역할 기반 액세스 제어) 또는 공동 관리자 권한을 통해 사용자에게 앱 액세스 권한이 부여되면 해당 사용자는 액세스가 취소될 때까지 자신의 사용자 수준 자격 증명을 사용할 수 있습니다. 이러한 자격 증명을 다른 Azure 사용자와 공유하지 마세요.

* **앱 수준 자격 증명** - 각 앱마다 하나씩의 자격 증명 집합입니다. 해당 앱에만 배포하는 데 사용할 수 있습니다. 각 앱의 자격 증명은 앱 생성 시 자동으로 생성됩니다. 수동으로 구성할 수 없지만, 언제든지 다시 설정할 수 있습니다. 사용자에게 (RBAC를 통해) 앱 수준 자격 증명에 대한 액세스 권한을 부여하려면 해당 사용자가 앱에서 기여자이거나 그보다 높아야 합니다. 읽기 권한자는 게시할 수 없으므로 자격 증명에 액세스할 수 없습니다.

## <a name="userscope"></a>사용자 수준 자격 증명 설정 및 다시 설정

모든 앱의 [리소스 페이지](../azure-resource-manager/manage-resources-portal.md#manage-resources)에서 사용자 수준 자격 증명을 구성할 수 있습니다. 이러한 자격 증명을 구성하는 앱에 관계 없이 Azure 계정의 모든 앱과 모든 구독에 적용됩니다. 

사용자 수준 자격 증명을 구성하려면

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **App Services** > **&lt;any_app >** > **배포 center** > **배포 자격 증명**을 클릭합니다.

    포털에서 배포 자격 증명 페이지에 액세스하려면 앱이 하나 이상 있어야 합니다. 그러나 [Azure CLI](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set)를 사용하면 기존 앱 없이 사용자 수준 자격 증명을 구성할 수 있습니다.

2. **사용자 자격 증명**을 클릭하고, 사용자 이름 및 암호를 구성하고, **자격 증명 저장**을 클릭합니다.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

배포 자격 증명을 설정하면 앱의 **개요**에서 *Git* 배포 사용자 이름을 찾을 수 있습니다.

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

그런 다음 앱의 **속성**에서 *FTP* 배포 사용자 이름을 찾을 수 있습니다.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure에서는 사용자 수준 배포 암호가 표시되지 않습니다. 암호를 잊은 경우 이 섹션의 단계에 따라 자격 증명을 다시 설정할 수 있습니다.
>
>  

## <a name="use-user-level-credentials-with-ftpftps"></a>FTP/FTPS를 사용 하 여 사용자 수준 자격 증명을 사용 합니다.

다음 형식으로 사용자 이름을 requirers 사용자 수준 자격 증명을 사용 하 여 FTP/FTPS 끝점 인증: `<app-name>\<user-name>`

사용자 수준 자격 증명은 사용자와 특정 리소스 하지에 연결 된, 이후 사용자 이름 적합 한 앱 끝점으로의 로그인 작업을이 형식 이어야 합니다.

## <a name="appscope"></a>앱 수준 자격 증명 정보 가져오기 및 다시 설정
앱 수준 자격 증명을 가져오려면

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **App Services** > **&lt;any_app >** > **배포 center** > **배포 자격 증명**을 클릭합니다.

2. **앱 자격 증명**을 클릭하고, **복사** 링크를 클릭하여 사용자 이름 또는 암호를 복사합니다.

    ![](./media/app-service-deployment-credentials/deployment_credentials_app_level.png)

앱 수준 자격 증명을 다시 설정하려면 동일한 대화 상자에서 **자격 증명 다시 설정**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

이러한 자격 증명을 사용하여 [로컬 Git](deploy-local-git.md)에서 또는 [FTP/S](deploy-ftp.md)를 통해 앱을 배포하는 방법을 알아봅니다.
