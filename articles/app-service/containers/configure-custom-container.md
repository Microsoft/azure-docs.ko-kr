---
title: 고객 컨테이너-Azure App Service 구성 | Microsoft Docs
description: Azure App Service에서 작업 하도록 Node.js 앱을 구성 하는 방법 알아보기
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 1e5faa8d356b891d825586414c0a1a1b9fa47090
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60853323"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Azure App Service에 대 한 사용자 지정 Linux 컨테이너 구성

이 문서에서는 Azure App Service에서 실행 하는 사용자 지정 Linux 컨테이너를 구성 하는 방법을 보여 줍니다.

이 가이드는 주요 개념 및 App Service에서 Linux 앱의 컨테이너 화에 대 한 지침을 제공합니다. Azure App Service를 사용한 경험이 없는 경우에 따라 합니다 [사용자 지정 컨테이너 빠른 시작](quickstart-docker-go.md) 하 고 [자습서](tutorial-custom-docker-image.md) 첫 번째입니다. 이기도 한 [다중 컨테이너 앱 빠른 시작](quickstart-multi-container.md) 하 고 [자습서](tutorial-multi-container-app.md)합니다.

## <a name="configure-port-number"></a>포트 번호를 구성 합니다.

사용자 지정 이미지에서 웹 서버 80 이외의 포트를 사용할 수 있습니다. Azure를 사용 하 여 사용자 지정 하는 포트 정보를 `WEBSITES_PORT` 앱 설정 합니다. [이 자습서의 Python 샘플](https://github.com/Azure-Samples/docker-django-webapp-linux)에 대한 GitHub 페이지에서 `WEBSITES_PORT`를 _8000_으로 설정해야 함을 보여 줍니다. 실행 하 여 설정할 수 있습니다 [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) Cloud Shell에서 명령을 합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>환경 변수 구성

사용자 지정 컨테이너 외부에서 제공 해야 하는 환경 변수를 사용할 수 있습니다. 실행 하 여 전달할 수 있습니다 [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) Cloud Shell에서 명령을 합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

이 메서드는 모두 단일 컨테이너 응용 프로그램 또는 환경 변수에서 지정 되어 있는 다중 컨테이너 앱에 대 한 작동 합니다 *docker compose.yml* 파일입니다.

## <a name="use-persistent-shared-storage"></a>영구 공유 저장소를 사용 합니다.

사용할 수는 *홈/* 다시 시작 후 파일을 유지 하 고 인스턴스 간에 공유할 앱의 파일 시스템 디렉터리입니다. `/home` 앱 컨테이너 앱을 영구 저장소에 액세스할 수 있도록 제공 됩니다.

영구 저장소 사용 하지 않으면 다음에 쓸 때 여 `/home` 디렉터리 앱 다시 시작 또는 여러 인스턴스에서 유지 되지 않습니다. 유일한 예외는는 `/home/LogFiles` Docker 및 컨테이너 로그를 저장 하는 데 사용 되는 디렉터리입니다. 영구 저장을 사용 하는 경우,에 대 한 모든 쓰기는 `/home` 디렉터리 유지 되 고 스케일 아웃 응용 프로그램의 모든 인스턴스에서 액세스할 수 있습니다.

영구 저장소는 기본적으로 *비활성화*합니다. 를 사용 하도록 설정 하거나 사용 하지 않도록 설정 합니다 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 실행 하 여 앱 설정 [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) Cloud Shell에서 명령을 합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

> [!NOTE]
> 할 수도 있습니다 [고유한 영구 저장소를 구성](how-to-serve-content-from-azure-storage.md)합니다.

## <a name="enable-ssh"></a>SSH 사용

SSH를 사용하면 컨테이너와 클라이언트 간의 보안 통신을 설정할 수 있습니다. SSH를 지원 하기 위해 사용자 지정 컨테이너에 대 한 순서로 자체 Dockerfile에 추가 해야 합니다.

> [!TIP]
> 모든 기본 제공 Linux 컨테이너 이미지 리포지토리에 해당 SSH 지침을 추가 했습니다. 사용 하 여 다음 지침을 살펴볼 수 있습니다 합니다 [Node.js 10.14 리포지토리](https://github.com/Azure-App-Service/node/blob/master/10.14) 어떻게 것을 사용할 수 있습니다.

- 사용 된 [실행](https://docs.docker.com/engine/reference/builder/#run) SSH 서버를 설치 하 고 루트 계정의 암호를 설정 하는 명령 `"Docker!"`입니다. 기반으로 이미지에 대 한 예를 들어 [Alpine Linux](https://hub.docker.com/_/alpine), 다음 명령을 사용 해야 합니다.

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    이 구성은 컨테이너에 대 한 외부 연결을 허용 하지 않습니다. SSH를 사용할 때만 사용할 수는 `https://<app-name>.scm.azurewebsites.net` 게시 자격 증명을 사용 하 여 인증 합니다.

- 추가 [이 sshd_config 파일](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) 이미지 리포지토리를 사용 하 여 합니다 [복사](https://docs.docker.com/engine/reference/builder/#copy) 명령 파일을 복사 하는 */etc/ssh/* 디렉터리입니다. 에 대 한 자세한 내용은 *sshd_config* 파일을 참조 하십시오 [OpenBSD 설명서](https://man.openbsd.org/sshd_config)합니다.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > *sshd_config* 파일에 다음이 항목되어야 합니다.
    > - `Ciphers`는 이 목록에 적어도 하나의 항목이 있어야 합니다.`aes128-cbc,3des-cbc,aes256-cbc`
    > - `MACs`는 이 목록에 적어도 하나의 항목이 있어야 합니다.`hmac-sha1,hmac-sha1-96`

- 사용 된 [노출](https://docs.docker.com/engine/reference/builder/#expose) 명령 컨테이너에서 2222 포트를 열어야 합니다. 루트 암호는 알려져 있지만, 포트 2222를 인터넷에서 액세스할 수 없습니다. 것은 개인 가상 네트워크의 브리지 네트워크 내에서 컨테이너에 의해서만 액세스할 수 있습니다.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- 컨테이너에 대 한 시작 스크립트에서 SSH 서버를 시작 합니다.

    ```bash
    /usr/sbin/sshd
    ```

    예제를 보려면 어떻게 기본값 [Node.js 10.14 컨테이너](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) SSH 서버를 시작 합니다.

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>다중 컨테이너 앱 구성

- [Docker Compose를 사용 하 여 영구 저장소](#use-persistent-storage-in-docker-compose)
- [미리 보기 제한 사항](#preview-limitations)
- [Docker Compose 옵션](#docker-compose-options)
- [Kubernetes 구성 옵션](#kubernetes-configuration-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Docker Compose를 사용 하 여 영구 저장소

WordPress와 같은 다중 컨테이너 앱에는 제대로 작동 하려면 영구 저장소가 필요 합니다. 사용 하려면 Docker Compose 구성 저장소 위치를 가리켜야 *외부* 컨테이너입니다. 컨테이너 내에서 저장소 위치에는 앱을 다시 시작 외 변경 내용을 유지 하지 않습니다.

설정 하 여 영구 저장소를 사용 하도록 설정 합니다 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 앱 설정, 사용 하 여는 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) Cloud Shell에서 명령을 합니다.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

사용자 *docker compose.yml* 파일을 매핑하는 `volumes` 옵션을 `${WEBAPP_STORAGE_HOME}`. 

`WEBAPP_STORAGE_HOME`은 앱의 영구 저장소에 매핑되는 App Service의 환경 변수입니다. 예를 들면 다음과 같습니다.

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="use-custom-storage-in-docker-compose"></a>Docker Compose에 사용자 지정 저장소를 사용 합니다.

사용자 지정 id를 사용 하 여 다중 컨테이너 앱을 사용 하 여 azure Storage (Azure Files 또는 Azure Blob)을 탑재할 수 있습니다. 사용자 지정 id 이름을 보려면 실행 [ `az webapp config storage-account list --name <app_name> --resource-group <resource_group>` ](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)합니다.

사용자 *docker compose.yml* 파일을 매핑하는 `volumes` 옵션을 `custom-id`. 예를 들면 다음과 같습니다.

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

### <a name="preview-limitations"></a>미리 보기 제한 사항

다중 컨테이너는 현재 미리 보기 중입니다. 다음 App Service 플랫폼 기능은 지원 되지 않습니다.

- 인증/권한 부여
- 관리 ID

### <a name="docker-compose-options"></a>Docker Compose 옵션

다음 목록은 지원 되거나 지원 되지 않는 Docker Compose 구성 옵션을 보여 줍니다.

#### <a name="supported-options"></a>지원되는 옵션

- command
- entrypoint
- 환경
- Image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>지원되지 않는 옵션

- build(허용 안 함)
- depends_on(무시됨)
- networks(무시됨)
- secrets(무시됨)
- 포트 80 및 8080 이외의 포트(무시됨)

> [!NOTE]
> 명시적으로 호출 하는 다른 모든 옵션은 공개 미리 보기에서 무시 됩니다.

### <a name="kubernetes-configuration-options"></a>Kubernetes 구성 옵션

다음 구성 옵션은 Kubernetes에 대 한 지원 됩니다.

- args
- command
- 컨테이너
- Image
- 이름
- ports
- spec

> [!NOTE]
> 명시적으로 호출 하는 다른 모든 옵션은 공개 미리 보기에서 지원 되지 않습니다.
>

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 개인 컨테이너 리포지토리에서 배포](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [자습서: 다중 컨테이너 WordPress 앱](tutorial-multi-container-app.md)
