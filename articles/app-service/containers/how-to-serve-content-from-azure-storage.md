---
title: Azure Storage에서 Linux 컨테이너로 콘텐츠 제공
description: Azure App Service에서 Linux 컨테이너에 사용자 지정 네트워크 공유를 연결 하는 방법에 대해 알아봅니다. 앱 간에 파일을 공유 하 고, 원격으로 정적 콘텐츠를 관리 하 고, 로컬에서 액세스 합니다.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: b2be84625035bb368784f3f423d63121c29255ad
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121424"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Linux의 App Service에 있는 Azure Storage의 콘텐츠 제공

> [!NOTE]
> 이 문서는 Linux 컨테이너에 적용 됩니다. 사용자 지정 Windows 컨테이너에 배포 하려면 [App Service의 Windows 컨테이너에서 Azure Files 구성](../configure-connect-to-azure-storage.md)을 참조 하세요. Linux에서 App Service Azure Storage는 **미리 보기** 기능입니다. 이 기능은 **프로덕션 시나리오에서 지원 되지 않습니다**.
>

이 가이드에서는 Azure Storage를 Linux에서 App Service에 연결 하는 방법을 보여 줍니다. 보안 콘텐츠, 콘텐츠 이식성, 영구 저장소, 여러 앱에 대 한 액세스 및 여러 가지 전송 방법 등의 이점이 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Azure CLI](/cli/azure/install-azure-cli)(2.0.46 이상)
- [Linux 앱에서 기존 App Service](https://docs.microsoft.com/azure/app-service/containers/)
- [Azure Storage 계정](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- [Azure 파일 공유 및 디렉터리](https://docs.microsoft.com/azure/storage/common/storage-azure-cli#create-and-manage-file-shares)입니다.


## <a name="limitations-of-azure-storage-with-app-service"></a>App Service Azure Storage의 제한 사항

- App Service Azure Storage는 Linux 및 Web App for Containers App Service에 대 한 **미리 보기** 상태입니다. **프로덕션 시나리오**에는 **지원 되지 않습니다** .
- Azure Storage App Service를 사용 하 여 **컨테이너 Azure Files 컨테이너** (읽기/쓰기) 및 **Azure Blob 컨테이너** (읽기 전용)를 지원 합니다.
- App Service Azure Storage는 인프라 제한으로 인해 **저장소 방화벽** 구성 사용을 **지원 하지 않습니다** .
- App Service Azure Storage를 사용 하면 앱 당 **최대 5 개의** 탑재 지점이 지정 됩니다.
- App Service FTP/FTPs 끝점을 통해 앱에 탑재 된 Azure Storage에 액세스할 수 없습니다. [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 사용 합니다.
- Azure Storage는 웹 앱에 **포함 되지** 않으며 별도로 청구 됩니다. [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage)에 대해 자세히 알아보세요.

> [!WARNING]
> Azure Blob Storage를 사용 하는 App Service 구성은 2 월 2020 일에 읽기 전용이 됩니다. [자세히 알아보기](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Azure Storage를 사용 하 여 앱 구성

[Azure Storage 계정, 파일 공유 및 디렉터리](#prerequisites)를 만들었으면 이제 Azure Storage를 사용 하 여 앱을 구성할 수 있습니다.

스토리지 계정을 App Service 앱의 디렉터리에 탑재하려면 [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) 명령을 사용합니다. 스토리지 형식은 AzureBlob 또는 AzureFiles일 수 있습니다. AzureFiles는이 예제에서 사용 됩니다.


> [!CAUTION]
> 웹 앱에서 탑재 경로로 지정 된 디렉터리는 비어 있어야 합니다. 이 디렉터리에 저장 된 모든 콘텐츠는 외부 마운트가 추가 될 때 삭제 됩니다. 기존 앱에 대한 파일을 마이그레이션하는 경우 시작하기 전에 앱 및 해당 콘텐츠의 백업을 만듭니다.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

스토리지 계정에 연결하려는 다른 디렉터리에 대해 이 작업을 수행해야 합니다.

## <a name="verify-azure-storage-link-to-the-web-app"></a>웹 앱에 대 한 Azure Storage 링크를 확인 합니다.

스토리지 컨테이너가 웹앱에 연결되면 다음 명령을 실행하여 이를 확인할 수 있습니다.

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Docker Compose에서 Azure Storage 사용

Azure Storage는 사용자 지정 id를 사용 하 여 다중 컨테이너 앱으로 탑재할 수 있습니다. 사용자 지정 id 이름을 보려면 [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)를 실행 합니다.

*Docker-compose.ci.build.yml* 파일에서 `volumes` 옵션을 `custom-id`에 매핑합니다. 다음은 그 예입니다.

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>다음 단계

- [Azure App Service에서 웹앱 구성](../configure-common.md)

