---
title: 패키지에서 Azure Functions 실행
description: 함수 앱 프로젝트 파일을 포함하는 배포 패키지 파일을 탑재하여 Azure Functions 런타임이 함수를 실행하게 합니다.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: b2d90cf78263b30b4315199cf1c543186a435f17
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88639888"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>패키지에서 Azure Functions 실행

Azure의 함수 앱의 배포 패키지 파일에서 직접 함수를 실행할 수 있습니다. 다른 옵션은 함수 앱의 `d:\home\site\wwwroot` 디렉터리에 파일을 배포하는 것입니다.

이 문서에서는 패키지에서 함수를 실행하는 이점에 대해 설명합니다. 또한 함수 앱에서 이 기능을 사용하도록 설정하는 방법을 보여줍니다.

## <a name="benefits-of-running-from-a-package-file"></a>패키지 파일에서 실행하는 이점
  
패키지 파일에서 실행하는 것에 대한 여러 이점은 다음과 같습니다.

+ 파일 복사 잠금 문제의 위험을 줄여줍니다.
+ (다시 시작으로) 프로덕션 앱에 배포될 수 있습니다.
+ 앱에서 실행되는 파일을 확인할 수 있습니다.
+ [Azure Resource Manager 배포](functions-infrastructure-as-code.md)의 성능을 향상시킵니다.
+ 특히 대규모 npm 패키지 트리가 있는 JavaScript 함수의 경우 콜드 부팅 시간을 줄일 수 있습니다.

자세한 내용은 [이 공지](https://github.com/Azure/app-service-announcements/issues/84)를 참조 하세요.

## <a name="enabling-functions-to-run-from-a-package"></a>패키지에서 함수를 실행하도록 설정

패키지에서 함수 앱을 실행하도록 설정하려면 함수 앱 설정에 `WEBSITE_RUN_FROM_PACKAGE` 설정을 추가하면 됩니다. `WEBSITE_RUN_FROM_PACKAGE` 설정에는 다음 값 중 하나가 있어야 합니다.

| 값  | Description  |
|---------|---------|
| **`1`**  | Windows에서 실행 되는 함수 앱에 권장 됩니다. 함수 앱의 `d:\home\data\SitePackages` 폴더의 패키지 파일에서 실행합니다. [Zip 배포를 사용 하 여 배포](#integration-with-zip-deployment)하지 않는 경우이 옵션을 사용 하려면 폴더에 라는 파일이 있어야 합니다 `packagename.txt` . 이 파일에는 공백 없이 폴더에 패키지 파일 이름만 포함됩니다. |
|**`<URL>`**  | 실행하려는 특정 패키지 파일의 위치입니다. Blob Storage를 사용하는 경우 [SAS(공유 액세스 서명)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer)가 포함된 프라이빗 컨테이너를 사용하여 Functions 런타임이 패키지에 액세스할 수 있게 해야 합니다. [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)를 사용하여 Blob 스토리지 계정에 패키지 파일을 업로드할 수 있습니다. URL을 지정 하는 경우 업데이트 된 패키지를 게시 한 후에도 [트리거를 동기화](functions-deployment-technologies.md#trigger-syncing) 해야 합니다. |

> [!CAUTION]
> Windows에서 함수 앱을 실행 하는 경우 외부 URL 옵션의 콜드 시작 성능이 저하 됩니다. Windows에 함수 앱을 배포 하는 경우 `WEBSITE_RUN_FROM_PACKAGE` 를로 설정 하 `1` 고 zip 배포를 사용 하 여 게시 해야 합니다.

다음은 Azure Blob Storage에 호스트된 .zip 파일에서 실행되도록 구성된 함수 앱을 보여줍니다.

![WEBSITE_RUN_FROM_ZIP 앱 설정](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> 현재는 .zip 패키지 파일만 지원됩니다.

## <a name="integration-with-zip-deployment"></a>Zip 배포와 통합

[Zip 배포][Zip deployment for Azure Functions]는 Azure App Service의 기능으로서 함수 앱 프로젝트를 `wwwroot` 디렉터리에 배포할 수 있습니다. 프로젝트는 .zip 배포 파일로 패키지됩니다. 패키지를 `d:\home\data\SitePackages` 폴더에 배포하는 데 동일한 API를 사용할 수 있습니다. `1`의 `WEBSITE_RUN_FROM_PACKAGE` 앱 설정 값을 사용하여 Zip 배포 API는 `d:\home\site\wwwroot`에 파일을 추출하지 않고 `d:\home\data\SitePackages` 폴더에 패키지를 복사합니다. 또한 `packagename.txt` 파일도 만듭니다. 다시 시작 후 패키지가 `wwwroot` 읽기 전용 파일 시스템으로에 탑재 됩니다. Zip 배포에 대한 자세한 내용은 [Azure Functions에 대한 Zip 배포](deployment-zip-push.md)를 참조하세요.

> [!NOTE]
> 배포가 발생 하면 함수 앱의 다시 시작이 트리거됩니다. 다시 시작 하기 전에 모든 기존 함수 실행이 완료 되거나 제한 시간이 초과 될 수 있습니다. 자세한 내용은 [배포 동작](functions-deployment-technologies.md#deployment-behaviors)을 참조 하세요.

## <a name="adding-the-website_run_from_package-setting"></a>WEBSITE_RUN_FROM_PACKAGE 설정 추가

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>문제 해결

- 패키지에서 실행은 `wwwroot` 읽기 전용 이므로이 디렉터리에 파일을 쓸 때 오류가 발생 합니다.
- Tar 및 gzip 형식은 지원 되지 않습니다.
- 이 기능은 로컬 캐시로 구성 되지 않습니다.
- 콜드 부팅 성능이 개선 되도록 하려면 로컬 Zip 옵션 ( `WEBSITE_RUN_FROM_PACKAGE` = 1)을 사용 합니다.
- 패키지에서 실행은 배포 사용자 지정 옵션 ()과 호환 되지 않으므로 `SCM_DO_BUILD_DURING_DEPLOYMENT=true` 배포 중에 빌드 단계가 무시 됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
