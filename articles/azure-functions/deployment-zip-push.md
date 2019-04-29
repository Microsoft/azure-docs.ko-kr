---
title: Azure Functions에 대한 Zip 푸시 배포 | Microsoft Docs
description: Kudu 배포 서비스의 .zip 파일 배포 기능을 사용하여 Azure Functions를 게시합니다.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 08/12/2018
ms.author: glenga
ms.openlocfilehash: 2762e5c4f2b67415a0e42e80a34ae5b34c57adc9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111202"
---
# <a name="zip-deployment-for-azure-functions"></a>Azure Functions에 대한 Zip 배포

이 문서에서는 .zip(압축) 파일에서 Azure에 함수 앱 프로젝트 파일을 배포하는 방법을 설명합니다. Azure CLI를 사용하고 REST API를 사용하여 푸시 배포를 수행하는 방법을 알아봅니다. [Azure Functions Core Tools](functions-run-local.md)도 Azure에 로컬 프로젝트를 게시할 때 이러한 배치 API를 사용합니다.

Azure Functions에는 Azure App Service에서 제공하는 전체 범위의 지속적인 배포 및 통합 옵션이 포함됩니다. 자세한 내용은 [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)를 참조하세요.

개발 속도를 높이기 위해 .zip 파일에서 함수 앱 프로젝트 파일을 직접 손쉽게 배포할 수 있습니다. .zip 배포 API는 .zip 파일의 내용을 가져와 함수 앱의 `wwwroot` 폴더로 추출합니다. 이 .zip 파일 배포는 다음을 포함하여 지속적인 통합 기반 배포를 지원하는 동일한 Kudu 서비스를 사용합니다.

+ 이전 배포의 남은 파일 삭제.
+ 배포 스크립트 실행을 포함한 배포 사용자 지정.
+ 배포 로그.
+ [소비 계획](functions-scale.md) 함수 앱에서 함수 트리거 동기화.

자세한 내용은 [.zip 배포 참조](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)를 참조하세요.

## <a name="deployment-zip-file-requirements"></a>배포 .zip 파일 요구 사항

푸시 배포에 사용할 .zip 파일에는 함수를 실행하는 데 필요한 모든 파일이 포함되어야 합니다.

>[!IMPORTANT]
> .zip 배포를 사용할 경우 .zip 파일에 없는 기존 배포의 모든 파일이 함수 앱에서 삭제됩니다.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

함수 앱에는 `wwwroot` 디렉터리의 파일 및 폴더가 모두 포함됩니다. .zip 파일 배포에는 디렉터리 자체가 아닌 `wwwroot` 디렉터리의 콘텐츠가 포함됩니다. C# 클래스 라이브러리 프로젝트를 배포하는 경우 .zip 패키지의 `bin` 하위 폴더에 컴파일된 라이브러리 파일 및 종속성을 포함시켜야 합니다.

## <a name="download-your-function-app-files"></a>함수 앱 파일 다운로드

로컬 컴퓨터에서 개발하는 경우 개발 컴퓨터에서 함수 앱 프로젝트 폴더의 .zip 파일을 쉽게 만들 수 있습니다.

그러나 Azure Portal의 편집기를 사용하여 함수를 만들었을 수 있습니다. 다음과 같은 방법 중 하나로 기존 함수 앱 프로젝트를 다운로드할 수 있습니다.

+ **Azure Portal에서:**

  1. [Azure Portal](https://portal.azure.com)에 로그인하고 함수 앱으로 이동합니다.

  2. **개요** 탭에서 **앱 콘텐츠 다운로드**를 선택합니다. 다운로드 옵션을 선택한 다음, **다운로드**를 선택합니다.

      ![함수 앱 프로젝트 다운로드](./media/deployment-zip-push/download-project.png)

     .zip 푸시 배포를 사용하여 함수 앱에 다시 게시하려면 다운로드한 .zip 파일 형식이 필요합니다. 포털 다운로드는 Visual Studio에서 직접 함수 앱을 여는 데 필요한 파일을 추가할 수도 있습니다.

+ **REST API 사용:**

    다음과 같은 배포 GET API를 사용하여 `<function_app>` 프로젝트에서 파일 다운로드: 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    `/site/wwwroot/`를 포함하면 zip 파일에 전체 사이트가 아닌 함수 앱 프로젝트 파일만이 포함되도록 합니다. Azure에 로그인하지 않은 경우 이를 묻는 메시지가 나타납니다.  

GitHub 리포지토리에서도 .zip 파일을 다운로드할 수 있습니다. GitHub 리포지토리를 .zip 파일로 다운로드할 경우 GitHub에서는 분기에 대한 추가 폴더 수준을 추가합니다. 이 추가 폴더 수준은 GitHub에서 다운로드한 대로 직접 .zip 파일을 배포할 수 없음을 의미합니다. GitHub 리포지토리를 사용하여 함수 앱을 유지 관리하려는 경우 [지속적인 통합](functions-continuous-deployment.md)을 사용하여 앱을 배포해야 합니다.  

## <a name="cli"></a>Azure CLI를 사용하여 배포

Azure CLI를 사용하여 푸시 배포를 트리거할 수 있습니다. [az functionapp deployment source config-zip](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) 명령을 사용하여 함수 앱에 .zip 파일을 푸시 배포합니다. 이 명령을 사용하려면 Azure CLI 버전 2.0.21 이상을 사용해야 합니다. 사용 중인 Azure CLI 버전을 확인하려면 `az --version` 명령을 사용합니다.

다음 명령에서 `<zip_file_path>` 자리 표시자를 .zip 파일 위치의 경로로 바꿉니다. 또한 `<app_name>`을 함수 앱의 고유 이름으로 바꿉니다. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```

이 명령은 다운로드한 .zip 파일의 프로젝트 파일을 Azure의 함수 앱에 배포합니다. 그런 다음, 앱을 다시 시작합니다. 이 함수 앱에 대한 배포 목록을 보려면 REST API를 사용해야 합니다.

로컬 컴퓨터에서 Azure CLI를 사용하는 경우 `<zip_file_path>`는 컴퓨터에 있는 .zip 파일의 경로입니다. [Azure Cloud Shell](../cloud-shell/overview.md)에서 Azure CLI를 실행할 수도 있습니다. Cloud Shell을 사용할 경우 먼저 Cloud Shell과 연결된 Azure Files 계정에 배포 .zip 파일을 업로드해야 합니다. 이 경우 `<zip_file_path>`는 Cloud Shell 계정에서 사용하는 저장소 위치입니다. 자세한 내용은 [Azure Cloud Shell에서 파일 유지](../cloud-shell/persisting-shell-storage.md)를 참조하세요.

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>배포 패키지에서 함수 실행

배포 패키지 파일에서 직접 함수를 실행하도록 선택할 수도 있습니다. 이 방법은 패키지에서 함수 앱의 `wwwroot` 디렉터리로 파일을 복사하는 배포 단계를 건너뜁니다. 대신, 패키지 파일은 Functions 런타임에 의해 탑재되고 `wwwroot` 디렉터리의 내용은 읽기 전용이 됩니다.  

Zip 배치는 이 기능과 통합되어 있으며 함수 앱 설정 `WEBSITE_RUN_FROM_PACKAGE`을 값 `1`로 설정하여 활성화할 수 있습니다. 자세한 내용은 [배포 패키지 파일에서 함수 실행](run-functions-from-deployment-package.md)을 참조하세요.

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
