---
title: ZIP 패키지에서 앱 실행
description: 원자성을 사용 하 여 앱의 ZIP 패키지를 배포 합니다. ZIP 배포 프로세스 중에 앱의 동작에 대한 예측 가능성과 안정성을 향상 시킵니다.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 5cc909d79b3f5ea2b4c6a3da12bc7250addbe00c
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945839"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>ZIP 패키지에서 직접 Azure App Service에서 앱 실행

[Azure App Service](overview.md)배포 ZIP 패키지 파일에서 직접 앱을 실행할 수 있습니다. 이 문서에서는 앱에서이 기능을 사용 하도록 설정 하는 방법을 보여 줍니다.

App Service의 다른 모든 배포 방법에는 다음과 같은 내용이 있습니다. 파일이 앱 (또는 Linux 앱 용 */home/site/wwwroot* )의 *D:\home\site\wwwroot* 에 배포 됩니다. 런타임에 응용 프로그램에서 동일한 디렉터리를 사용 하므로 파일 잠금 충돌로 인해 배포가 실패할 수 있으며 일부 파일이 아직 업데이트 되지 않았기 때문에 앱이 예상과 다르게 동작 합니다.

반면 패키지에서 직접 실행 하는 경우 패키지의 파일은 *wwwroot* 디렉터리에 복사 되지 않습니다. 대신, ZIP 패키지 자체는 읽기 전용 *wwwroot* 디렉터리로 직접 탑재 됩니다. 패키지에서 직접 실행 하는 경우 다음과 같은 몇 가지 이점이 있습니다.

- 배포와 런타임 간의 파일 잠금 충돌을 제거 합니다.
- 언제 든 지 전체 배포 된 앱만 실행 되도록 합니다.
- (다시 시작으로) 프로덕션 앱에 배포될 수 있습니다.
- Azure Resource Manager 배포의 성능을 향상 시킵니다.
- 특히 대규모 npm 패키지 트리가 있는 JavaScript 함수의 경우 콜드 부팅 시간을 줄일 수 있습니다.

> [!NOTE]
> 현재 ZIP 패키지 파일만 지원 됩니다.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>패키지에서 실행 가능

`WEBSITE_RUN_FROM_PACKAGE` 앱 설정은 패키지에서 실행 될 수 있도록 합니다. 설정 하려면 Azure CLI를 사용 하 여 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"`를 사용 하면 앱의 로컬 패키지에서 앱을 실행할 수 있습니다. [원격 패키지에서 실행할](#run-from-external-url-instead)수도 있습니다.

## <a name="run-the-package"></a>패키지 실행

App Service에서 패키지를 실행 하는 가장 쉬운 방법은 Azure CLI [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) command를 사용 하는 것입니다. 예:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

`WEBSITE_RUN_FROM_PACKAGE` 앱 설정이 설정 되어 있으므로이 명령은 패키지 콘텐츠를 앱의 *D:\home\site\wwwroot* 디렉터리에 추출 하지 않습니다. 대신 ZIP 파일을 있는 그대로 *D:\home\data\SitePackages*에 업로드 하 고, 런타임에 로드할 zip 패키지의 이름을 포함 하는 packagename을 동일한 디렉터리에 만듭니다 *.* [FTP](deploy-ftp.md)와 같은 다른 방식으로 ZIP 패키지를 업로드 하는 경우에는 *D:\home\data\SitePackages* 디렉터리와 *packagename* 파일을 수동으로 만들어야 합니다.

또한이 명령은 앱을 다시 시작 합니다. `WEBSITE_RUN_FROM_PACKAGE` 설정 되었으므로는 업로드 된 패키지를 읽기 전용 *wwwroot* 디렉터리로 탑재 하 고 탑재 된 해당 디렉터리에서 직접 앱을 실행 App Service.

## <a name="run-from-external-url-instead"></a>대신 외부 URL에서 실행

Azure Blob Storage와 같은 외부 URL에서 패키지를 실행할 수도 있습니다. [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)를 사용하여 Blob 스토리지 계정에 패키지 파일을 업로드할 수 있습니다. App Service 런타임이 패키지에 안전 하 게 액세스할 수 있도록 하려면 [SAS (공유 액세스 서명)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) 와 함께 개인 저장소 컨테이너를 사용 해야 합니다. 

Blob 저장소에 파일을 업로드 하 고 해당 파일에 대한 SAS URL이 있는 경우 `WEBSITE_RUN_FROM_PACKAGE` 앱 설정을 URL로 설정 합니다. 다음 예에서는 Azure CLI를 사용 하 여이 작업을 수행 합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Blob storage에 동일한 이름의 업데이트 된 패키지를 게시 하는 경우 업데이트 된 패키지가 App Service에 로드 되도록 앱을 다시 시작 해야 합니다.

## <a name="troubleshooting"></a>문제 해결

- 패키지에서 직접 실행 하면 `wwwroot` 읽기 전용이 됩니다. 응용 프로그램에서이 디렉터리에 파일을 쓰려고 하면 오류가 표시 됩니다.
- TAR 및 GZIP 형식은 지원 되지 않습니다.
- 이 기능은 [로컬 캐시](overview-local-cache.md)와 호환 되지 않습니다.
- 콜드 부팅 성능을 향상 시키려면 로컬 Zip 옵션 (`WEBSITE_RUN_FROM_PACKAGE`= 1)을 사용 합니다.

## <a name="more-resources"></a>추가 리소스

- [Azure App Service에 대한 연속 배포](deploy-continuous-deployment.md)
- [ZIP 또는 WAR 파일을 사용 하 여 코드 배포](deploy-zip.md)
