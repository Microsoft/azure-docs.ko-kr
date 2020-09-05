---
title: Azure Storage 추가 (컨테이너)
description: Azure App Service에서 컨테이너 화 된 앱에 사용자 지정 네트워크 공유를 연결 하는 방법에 대해 알아봅니다. 앱 간에 파일을 공유 하 고, 원격으로 정적 콘텐츠를 관리 하 고, 로컬에서 액세스 합니다.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 8ca3688ad11bc7a694327990be22b8eccd8f8161
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962862"
---
# <a name="access-azure-storage-as-a-network-share-from-a-container-in-app-service"></a>App Service의 컨테이너에서 네트워크 공유로 Azure Storage 액세스

::: zone pivot="container-windows"

이 가이드에서는 App Service의 windows 컨테이너에 Azure Storage 파일을 네트워크 공유로 연결 하는 방법을 보여 줍니다. [Azure Files 공유](../storage/files/storage-how-to-use-files-cli.md) 및 [프리미엄 파일 공유](../storage/files/storage-how-to-create-premium-fileshare.md) 만 지원 됩니다. 혜택에는 보안된 콘텐츠, 콘텐츠 이식성, 여러 앱에 대한 액세스 및 여러 가지 전송 메서드가 포함됩니다.

::: zone-end

::: zone pivot="container-linux"

이 가이드에서는 Azure Storage Linux 컨테이너 App Service에 연결 하는 방법을 보여 줍니다. 보안 콘텐츠, 콘텐츠 이식성, 영구 저장소, 여러 앱에 대 한 액세스 및 여러 가지 전송 방법 등의 이점이 있습니다.

::: zone-end

## <a name="prerequisites"></a>필수 구성 요소

::: zone pivot="container-windows"

- [Azure App Service의 기존 Windows 컨테이너 앱](quickstart-custom-container.md)
- [Azure 파일 공유 만들기](../storage/files/storage-how-to-use-files-cli.md)
- [Azure 파일 공유에 파일 업로드](../storage/files/storage-files-deployment-guide.md)

::: zone-end

::: zone pivot="container-linux"

- [Linux 앱에서 기존 App Service](index.yml)
- [Azure Storage 계정](../storage/common/storage-account-create.md?tabs=azure-cli)
- [Azure 파일 공유 및 디렉터리](../storage/files/storage-how-to-use-files-cli.md)입니다.

::: zone-end

> [!NOTE]
> Azure Files은 기본이 아닌 저장소 이며 별도로 청구 되며 웹 앱에 포함 되지 않습니다. 인프라 제한으로 인 한 방화벽 구성 사용은 지원 하지 않습니다.
>

## <a name="limitations"></a>제한 사항

::: zone pivot="container-windows"

- App Service Azure Storage는 **미리 보기** 상태 이며 **프로덕션 시나리오**에서 **지원 되지 않습니다** .
- App Service Azure Storage는 현재 고유한 코드 시나리오 (비 컨테이너 화 된 Windows 앱)를 사용할 **수 없습니다** .
- App Service Azure Storage는 인프라 제한으로 인해 **저장소 방화벽** 구성 사용을 **지원 하지 않습니다** .
- App Service Azure Storage를 사용 하면 앱 당 **최대 5 개의** 탑재 지점이 지정 됩니다.
- App Service FTP/FTPs 끝점을 통해 앱에 탑재 된 Azure Storage에 액세스할 수 없습니다. [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 사용 합니다.

::: zone-end

::: zone pivot="container-linux"

- App Service의 Azure Storage는 Linux 및 Web App for Containers App Service에 대 한 **미리 보기** 상태입니다. **프로덕션 시나리오**에는 **지원 되지 않습니다** .
- App Service에서 Azure Storage는 컨테이너 (읽기/쓰기) 및 **Azure Blob 컨테이너** (읽기 전용)를 **Azure Files** 탑재 하는 것을 지원 합니다.
- App Service Azure Storage는 인프라 제한으로 인해 **저장소 방화벽** 구성 사용을 **지원 하지 않습니다** .
- App Service에서 Azure Storage를 사용 하 여 앱 당 **최대 5 개의** 탑재 시점을 지정할 수 있습니다.
- App Service FTP/FTPs 끝점을 통해 앱에 탑재 된 Azure Storage에 액세스할 수 없습니다. [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 사용 합니다.

::: zone-end

## <a name="link-storage-to-your-app"></a>앱에 저장소 연결

::: zone pivot="container-windows"

[Azure Storage 계정, 파일 공유 및 디렉터리](#prerequisites)를 만들었으면 이제 Azure Storage를 사용 하 여 앱을 구성할 수 있습니다.

App Service 앱의 디렉터리에 Azure Files 공유를 탑재 하려면 [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) 명령을 사용 합니다. 저장소 유형은 AzureFiles 여야 합니다.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

Azure Files 공유에 연결 하려는 다른 모든 디렉터리에 대해이 작업을 수행 해야 합니다.

::: zone-end

::: zone pivot="container-linux"

[Azure Storage 계정, 파일 공유 및 디렉터리](#prerequisites)를 만들었으면 이제 Azure Storage를 사용 하 여 앱을 구성할 수 있습니다.

App Service 앱의 디렉터리에 저장소 계정을 탑재 하려면 [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) 명령을 사용 합니다. 스토리지 형식은 AzureBlob 또는 AzureFiles일 수 있습니다. AzureFiles는이 예제에서 사용 됩니다. 탑재 경로 설정은 Azure Storage에서 탑재 하려는 폴더에 해당 합니다. '/'로 설정 하면 전체 Azure Storage가 탑재 됩니다.


> [!CAUTION]
> 웹 앱에서 탑재 경로로 지정 된 디렉터리는 비어 있어야 합니다. 이 디렉터리에 저장 된 모든 콘텐츠는 외부 마운트가 추가 될 때 삭제 됩니다. 기존 앱에 대한 파일을 마이그레이션하는 경우 시작하기 전에 앱 및 해당 콘텐츠의 백업을 만듭니다.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

스토리지 계정에 연결하려는 다른 디렉터리에 대해 이 작업을 수행해야 합니다.

::: zone-end

## <a name="verify-linked-storage"></a>연결 된 저장소 확인

공유가 앱에 연결 되 면 다음 명령을 실행 하 여이를 확인할 수 있습니다.

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>다음 단계

::: zone pivot="container-windows"

- 사용자 지정 [컨테이너를 사용 하 여 사용자 지정 소프트웨어를 Azure App Service로 마이그레이션합니다](tutorial-custom-container.md?pivots=container-windows).

::: zone-end

::: zone pivot="container-linux"

- [사용자 지정 컨테이너를 구성](configure-custom-container.md?pivots=platform-linux)합니다.

::: zone-end