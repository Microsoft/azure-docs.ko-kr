---
title: Azure CLI를 사용하여 Azure AD 앱 만들기 및 Azure Media Services API에 액세스하도록 앱 구성 | Microsoft Docs
description: 이 항목에서는 Azure CLI를 사용하여 Azure AD 앱을 만들고 Azure Media Services API에 액세스하도록 앱을 구성하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 93a77c64e82c39f8f967a3549fafecfae59b194b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465750"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Azure CLI를 사용하여 Azure AD 앱을 만들고 Media Services API에 액세스하도록 구성 

이 항목에서는 Azure CLI를 사용하여 Azure Media Services 리소스에 액세스하기 위한 Azure AD(Active Directory) 애플리케이션과 서비스 사용자를 만드는 방법을 설명합니다. 

## <a name="prerequisites"></a>필수 조건

- Azure 계정. 자세한 내용은 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 
- Media Services 계정. 자세한 내용은 [Azure Portal을 사용하여 Azure Media Services 계정 만들기](media-services-portal-create-account.md)를 참조하세요.

## <a name="use-the-azure-cloud-shell"></a>Azure Cloud Shell 사용

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 포털의 위쪽 탐색 창에서 Cloud Shell을 시작합니다.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

자세한 내용은 [Azure Cloud Shell 개요](../../cloud-shell/overview.md)를 참조하세요.

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Azure CLI를 사용하여 Azure AD 앱을 만들고 미디어 계정 액세스 구성
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

예를 들면 다음과 같습니다.

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

이 예제에서 **scope**는 미디어 서비스 계정에 대한 전체 리소스 경로입니다. 하지만 **scope**는 모든 수준에 있을 수 있습니다.

예를 들어 다음 수준 중 하나일 수 있습니다.
 
* **구독** 수준.
* **리소스 그룹** 수준.
* **리소스** 수준(예: 미디어 계정).

자세한 내용은 [Azure CLI를 사용하여 Azure 서비스 사용자 만들기](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)를 참조하세요.

또한 [Azure 명령줄 인터페이스를 사용하여 역할 기반 Access Control 관리](../../role-based-access-control/role-assignments-cli.md)도 참조하세요. 

## <a name="next-steps"></a>다음 단계

[계정에 파일 업로드](media-services-portal-upload-files.md) 시작
