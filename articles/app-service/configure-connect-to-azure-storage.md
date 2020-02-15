---
title: 사용자 지정 저장소 추가 (Windows 컨테이너)
description: Azure App Service의 사용자 지정 Windows 컨테이너에서 사용자 지정 네트워크 공유를 연결 하는 방법에 대해 알아봅니다. 앱 간에 파일을 공유 하 고, 원격으로 정적 콘텐츠를 관리 하 고, 로컬에서 액세스 합니다.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120675"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>App Service의 Windows 컨테이너에서 Azure Files 구성

> [!NOTE]
> 이 문서는 사용자 지정 Windows 컨테이너에 적용 됩니다. _Linux_에서 App Service에 배포 하려면 [Azure Storage에서 콘텐츠 제공](./containers/how-to-serve-content-from-azure-storage.md)을 참조 하세요.
>

이 가이드에서는 Windows 컨테이너의 Azure Storage에 액세스 하는 방법을 보여 줍니다. [Azure Files 공유](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) 및 [프리미엄 파일 공유](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) 만 지원 됩니다. 이 방법의 Azure Files 공유를 사용 합니다. 혜택에는 보안된 콘텐츠, 콘텐츠 이식성, 여러 앱에 대한 액세스 및 여러 가지 전송 메서드가 포함됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Azure CLI](/cli/azure/install-azure-cli)(2.0.46 이상)
- [Azure App Service의 기존 Windows 컨테이너 앱](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Azure 파일 공유 만들기](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Azure 파일 공유에 파일 업로드](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure Files은 기본이 아닌 저장소 이며 별도로 청구 되며 웹 앱에 포함 되지 않습니다. 인프라 제한으로 인 한 방화벽 구성 사용은 지원 하지 않습니다.
>

## <a name="limitations"></a>제한 사항

- Windows 컨테이너의 Azure Storage는 **미리 보기** 상태 이며 **프로덕션 시나리오**에서 **지원 되지 않습니다** .
- Windows 컨테이너의 Azure Storage는 컨테이너 (읽기/쓰기)의 탑재 **Azure Files** 지원 합니다.
- Windows App Service 계획에 대 한 고유한 코드 시나리오를 가져오기 위해 Windows 컨테이너의 Azure Storage 현재 **지원 되지 않습니다** .
- Windows 컨테이너의 Azure Storage는 인프라 제한으로 인해 **저장소 방화벽** 구성 사용을 **지원 하지 않습니다** .
- Windows 컨테이너의 Azure Storage를 사용 하 여 앱 당 **최대 5 개의** 탑재 시점을 지정할 수 있습니다.
- App Service FTP/FTPs 끝점을 통해 앱에 탑재 된 Azure Storage에 액세스할 수 없습니다. [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 사용 합니다.
- Azure Storage은 독립적으로 청구 되며 웹 앱에 **포함 되지 않습니다** . [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage)에 대해 자세히 알아보세요.

## <a name="link-storage-to-your-web-app-preview"></a>웹앱에 스토리지 연결(미리 보기)

 App Service 앱의 디렉터리에 Azure Files 공유를 탑재 하려면 [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) 명령을 사용 합니다. 저장소 유형은 AzureFiles 여야 합니다.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Azure Files 공유에 연결 하려는 다른 모든 디렉터리에 대해이 작업을 수행 해야 합니다.

## <a name="verify"></a>확인

Azure Files 공유가 웹 앱에 연결 되 면 다음 명령을 실행 하 여이를 확인할 수 있습니다.

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>다음 단계

- [Windows 컨테이너 (미리 보기)를 사용 하 여 Azure App Service ASP.NET 앱을 마이그레이션합니다](app-service-web-tutorial-windows-containers-custom-fonts.md).
