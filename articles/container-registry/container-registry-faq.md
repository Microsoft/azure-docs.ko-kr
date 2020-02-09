---
title: FAQ(질문과 대답)
description: Azure Container Registry 서비스와 관련 된 faq (질문과 대답)
author: sajayantony
ms.topic: article
ms.date: 07/02/2019
ms.author: sajaya
ms.openlocfilehash: 74863823f3e8ef32565e01981d3a742d696a8165
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75708311"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Azure Container Registry에 대해 자주 묻는 질문

이 문서에서는 Azure Container Registry에 대한 질문과 대답을 다룹니다.

## <a name="resource-management"></a>리소스 관리

- [리소스 관리자 템플릿을 사용 하 여 Azure container registry를 만들 수 있나요?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [ACR에서 이미지를 검색 하는 보안 취약성이 있나요?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Azure Container Registry를 사용 하 여 Kubernetes를 구성 어떻게 할까요??](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [컨테이너 레지스트리에 대한 관리자 자격 증명을 가져올 어떻게 할까요? 있나요?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [리소스 관리자 템플릿에서 관리자 자격 증명을 가져올 어떻게 할까요? 있나요?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Azure CLI 또는 Azure PowerShell를 사용 하 여 복제를 삭제 하더라도 복제 삭제가 거부 된 상태로 실패 합니다.](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [방화벽 규칙이 성공적으로 업데이트 되었지만 적용 되지 않습니다.](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용 하 여 Azure Container Registry를 만들 수 있나요?

예. 다음은 레지스트리를 만드는 데 사용할 수 있는 [템플릿입니다](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) .

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>ACR에서 이미지를 검색 하는 보안 취약성이 있나요?

예. [Azure Security Center](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration), [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) 및 [바다색](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)의 설명서를 참조 하세요.

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Azure Container Registry를 사용 하 여 Kubernetes를 구성 어떻게 할까요??

[Azure Kubernetes Service](../aks/cluster-container-registry-integration.md)의 [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) 및 단계에 대한 설명서를 참조 하세요.

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>컨테이너 레지스트리에 대한 관리자 자격 증명을 가져올 어떻게 할까요? 있나요?

> [!IMPORTANT]
> 관리자 사용자 계정은 단일 사용자가 레지스트리에 액세스 하도록 설계 되었습니다. 주로 테스트 목적으로 사용 됩니다. 관리자 계정 자격 증명을 여러 사용자와 공유하지 않는 것이 좋습니다. 헤드리스 시나리오의 경우 사용자 및 서비스 주체는 개별 ID를 사용하는 것이 좋습니다. [인증 개요](container-registry-authentication.md)를 참조 하세요.

관리자 자격 증명을 가져오기 전에 레지스트리의 관리 사용자를 사용 하도록 설정 했는지 확인 합니다.

Azure CLI를 사용 하 여 자격 증명을 가져오려면:

```azurecli
az acr credential show -n myRegistry
```

Azure Powershell 사용:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>리소스 관리자 템플릿에서 관리자 자격 증명을 가져올 어떻게 할까요? 있나요?

> [!IMPORTANT]
> 관리자 사용자 계정은 단일 사용자가 레지스트리에 액세스 하도록 설계 되었습니다. 주로 테스트 목적으로 사용 됩니다. 관리자 계정 자격 증명을 여러 사용자와 공유하지 않는 것이 좋습니다. 헤드리스 시나리오의 경우 사용자 및 서비스 주체는 개별 ID를 사용하는 것이 좋습니다. [인증 개요](container-registry-authentication.md)를 참조 하세요.

관리자 자격 증명을 가져오기 전에 레지스트리의 관리 사용자를 사용 하도록 설정 했는지 확인 합니다.

첫 번째 암호를 가져오려면:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

두 번째 암호를 가져오려면:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Azure CLI 또는 Azure PowerShell를 사용 하 여 복제를 삭제 하더라도 복제 삭제가 거부 된 상태로 실패 합니다.

사용자에 게 레지스트리에 대한 사용 권한이 있지만 구독에 대한 읽기 권한자 수준 권한이 없는 경우 오류가 표시 됩니다. 이 문제를 해결 하려면 사용자에 게 구독에 대한 구독자 권한을 할당 합니다.


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>방화벽 규칙이 성공적으로 업데이트 되었지만 적용 되지 않습니다.

방화벽 규칙 변경 내용을 전파 하는 데 다소 시간이 걸립니다. 방화벽 설정을 변경한 후 몇 분 정도 기다렸다가이 변경 내용을 확인 하세요.


## <a name="registry-operations"></a>레지스트리 작업

- [Docker 레지스트리 HTTP API v 2에 액세스 어떻게 할까요??](#how-do-i-access-docker-registry-http-api-v2)
- [리포지토리의 태그에서 참조 하지 않는 모든 매니페스트를 삭제 어떻게 할까요?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [이미지를 삭제 한 후에도 레지스트리 할당량 사용량이 감소 하지 않는 이유는 무엇 인가요?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [저장소 할당량 변경 내용의 유효성을 검사 어떻게 할까요??](#how-do-i-validate-storage-quota-changes)
- [컨테이너에서 CLI를 실행할 때 내 레지스트리를 사용 하 여 인증 어떻게 할까요??](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [TLS 1.2을 사용 하도록 설정 하는 방법](#how-to-enable-tls-12)
- [Azure Container Registry 콘텐츠 신뢰를 지원 하나요?](#does-azure-container-registry-support-content-trust)
- [레지스트리 리소스를 관리할 수 있는 권한 없이 풀 또는 푸시 이미지에 대한 액세스 권한을 부여 어떻게 할까요??](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [레지스트리에 대해 자동 이미지 격리를 사용 하도록 설정 어떻게 할까요?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Docker 레지스트리 HTTP API v 2에 액세스 어떻게 할까요??

ACR은 Docker 레지스트리 HTTP API v 2를 지원 합니다. Api는 `https://<your registry login server>/v2/`에서 액세스할 수 있습니다. 예: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>리포지토리의 태그에서 참조 하지 않는 모든 매니페스트를 삭제 어떻게 할까요?

Bash에 있는 경우:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Powershell의 경우:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

참고: 삭제 명령에 `-y`을 추가 하 여 확인을 건너뛸 수 있습니다.

자세한 내용은 [Azure Container Registry에서 컨테이너 이미지 삭제](container-registry-delete.md)를 참조 하세요.

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>이미지를 삭제 한 후에도 레지스트리 할당량 사용량이 감소 하지 않는 이유는 무엇 인가요?

이 상황은 기본 계층이 다른 컨테이너 이미지에서 여전히 참조 되는 경우에 발생할 수 있습니다. 참조가 없는 이미지를 삭제 하면 몇 분 안에 레지스트리 사용이 업데이트 됩니다.

### <a name="how-do-i-validate-storage-quota-changes"></a>저장소 할당량 변경 내용의 유효성을 검사 어떻게 할까요??

다음 docker 파일을 사용 하 여 1GB 계층으로 이미지를 만듭니다. 이렇게 하면 이미지에 레지스트리의 다른 이미지에서 공유 하지 않는 계층이 있습니다.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Docker CLI를 사용 하 여 이미지를 빌드하고 레지스트리에 푸시합니다.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Azure Portal에서 저장소 사용량이 증가 하거나 CLI를 사용 하 여 사용을 쿼리할 수 있습니다.

```bash
az acr show-usage -n myregistry
```

Azure CLI 또는 포털을 사용 하 여 이미지를 삭제 하 고 몇 분 후에 업데이트 된 사용 현황을 확인 합니다.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>컨테이너에서 CLI를 실행할 때 내 레지스트리를 사용 하 여 인증 어떻게 할까요??

Docker 소켓을 탑재 하 여 Azure CLI 컨테이너를 실행 해야 합니다.

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

컨테이너에서 `docker`를 설치 합니다.

```bash
apk --update add docker
```

그런 다음 레지스트리를 사용 하 여 인증 합니다.

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>TLS 1.2을 사용 하도록 설정 하는 방법

최신 docker 클라이언트 (버전 18.03.0 이상)를 사용 하 여 TLS 1.2을 사용 하도록 설정 합니다. 

> [!IMPORTANT]
> 2020 년 1 월 13 일부 터 Azure Container Registry TLS 1.2을 사용 하려면 서버 및 응용 프로그램에서 모든 보안 연결이 필요 합니다. TLS 1.0 및 1.1에 대한 지원은 사용이 중지 됩니다.

### <a name="does-azure-container-registry-support-content-trust"></a>Azure Container Registry는 콘텐츠 신뢰를 지원하나요?

예, [Docker Notary](https://docs.docker.com/notary/getting_started/) 이 통합 되었으며 사용 하도록 설정할 수 있으므로 Azure Container Registry에서 신뢰할 수 있는 이미지를 사용할 수 있습니다. 자세한 내용은 [Azure Container Registry의 콘텐츠 신뢰](container-registry-content-trust.md)를 참조 하세요.


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>지문 파일은 어디에 있나요?

`~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`에서 다음을 수행 합니다.

* 모든 역할의 공개 키와 인증서 (위임 역할 제외)는 `root.json`에 저장 됩니다.
* 위임 역할의 공개 키 및 인증서는 부모 역할의 JSON 파일에 저장 됩니다 (예: `targets/releases` 역할에 대한 `targets.json`).

Docker 및 Notary 클라이언트에서 수행 하는 전체 전체를 확인 한 후 해당 공개 키와 인증서를 확인 하는 것이 좋습니다.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>레지스트리 리소스를 관리할 수 있는 권한 없이 풀 또는 푸시 이미지에 대한 액세스 권한을 부여 어떻게 할까요??

ACR은 다양 한 수준의 사용 권한을 제공 하는 [사용자 지정 역할](container-registry-roles.md) 을 지원 합니다. 특히 `AcrPull` 및 `AcrPush` 역할을 통해 사용자는 Azure에서 레지스트리 리소스를 관리할 수 있는 권한 없이 이미지를 끌어오거나 푸시할 수 있습니다.

* Azure Portal: 레지스트리-> Access Control (IAM) > 추가 (역할에 대해 `AcrPull` 또는 `AcrPush` 선택).
* Azure CLI: 다음 명령을 실행 하 여 레지스트리의 리소스 ID를 찾습니다.

  ```azurecli
  az acr show -n myRegistry
  ```
  
  그런 다음 사용자에 게 `AcrPull` 또는 `AcrPush` 역할을 할당할 수 있습니다 (다음 예제에서는 `AcrPull`사용).

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  또는 해당 응용 프로그램 ID로 식별 되는 서비스 사용자에 게 역할을 할당 합니다.

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

그러면 담당자는 레지스트리의 이미지를 인증 하 고 액세스할 수 있습니다.

* 레지스트리에 인증 하려면:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* 리포지토리를 나열 하려면:

  ```azurecli
  az acr repository list -n myRegistry
  ```

 이미지를 꺼내려면:
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

`AcrPull` 또는 `AcrPush` 역할만 사용 하는 경우에는 담당자에 게 Azure에서 레지스트리 리소스를 관리할 수 있는 권한이 없습니다. 예를 들어 `az acr list` 또는 `az acr show -n myRegistry`는 레지스트리를 표시 하지 않습니다.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>레지스트리에 대해 자동 이미지 격리를 사용 하도록 설정 어떻게 할까요??

이미지 격리는 현재 ACR의 미리 보기 기능입니다. 보안 검사를 성공적으로 통과 한 이미지만 일반 사용자에 게 표시 되도록 레지스트리의 격리 모드를 사용 하도록 설정할 수 있습니다. 자세한 내용은 [ACR GitHub 리포지토리](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)를 참조 하세요.

## <a name="diagnostics-and-health-checks"></a>진단 및 상태 검사

- [`az acr check-health` 상태 확인](#check-health-with-az-acr-check-health)
- [다음 오류가 발생 하 여 docker pull 실패: net/http: 연결을 대기 하는 동안 요청이 취소 되었습니다. 헤더를 대기 하는 동안 시간이 초과 되었습니다.](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker push는 성공 하지만 오류가 발생 하 docker pull 실패 합니다. 권한이 없음: 인증 필요](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login` 성공 하지만 docker 명령이 실패 합니다. 권한 없음: 인증 필요](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Docker 디먼의 디버그 로그를 사용 하도록 설정 하 고 가져옵니다.](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [업데이트 후 즉시 새 사용자 권한이 적용 되지 않을 수 있습니다.](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [직접 REST API 호출에서 인증 정보는 올바른 형식으로 제공 되지 않습니다.](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Azure Portal에서 내 리포지토리 또는 태그를 모두 나열 하지 않는 이유는 무엇 인가요?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Azure Portal에서 리포지토리 또는 태그를 가져오는 데 실패 하는 이유는 무엇 인가요?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [끌어오기 또는 푸시 요청이 허용 되지 않는 작업으로 인해 실패 하는 이유는 무엇 인가요?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Windows에서 http 추적을 수집 어떻게 할까요?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>`az acr check-health` 상태 확인

일반적인 환경 및 레지스트리 문제를 해결 하려면 [Azure container registry의 상태 확인](container-registry-check-health.md)을 참조 하세요.

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>다음 오류가 발생 하 여 docker pull 실패: net/http: 연결을 대기 하는 동안 요청이 취소 되었습니다. 헤더를 대기 하는 동안 시간이 초과 되었습니다.

 - 이 오류가 일시적인 문제인 경우 다시 시도는 성공 합니다.
 - `docker pull` 지속적으로 실패 하면 Docker 디먼에 문제가 있을 수 있습니다. 일반적으로 Docker 디먼을 다시 시작 하 여 문제를 완화할 수 있습니다. 
 - Docker 디먼을 다시 시작한 후에도이 문제가 계속 되 면이 문제는 컴퓨터의 네트워크 연결에 문제가 있을 수 있습니다. 컴퓨터의 일반 네트워크가 정상 상태 인지 확인 하려면 다음 명령을 실행 하 여 엔드포인트 연결을 테스트 합니다. 이 연결 확인 명령을 포함 하는 최소 `az acr` 버전은 2.2.9입니다. 이전 버전을 사용 하는 경우 Azure CLI를 업그레이드 합니다.
 
   ```azurecli
    az acr check-health -n myRegistry
    ```
 - 모든 Docker 클라이언트 작업에는 항상 다시 시도 메커니즘이 있어야 합니다.

### <a name="docker-pull-is-slow"></a>Docker 풀 속도가 느림
[이](http://www.azurespeed.com/Azure/Download) 도구를 사용 하 여 컴퓨터 네트워크 다운로드 속도를 테스트 합니다. 컴퓨터 네트워크가 저속 인 경우 레지스트리와 동일한 지역에서 Azure VM을 사용 하는 것이 좋습니다. 일반적으로 네트워크 속도를 더 빠르게 제공 합니다.

### <a name="docker-push-is-slow"></a>Docker 푸시 속도가 느림
[이](http://www.azurespeed.com/Azure/Upload) 도구를 사용 하 여 컴퓨터 네트워크 업로드 속도를 테스트 합니다. 컴퓨터 네트워크가 저속 인 경우 레지스트리와 동일한 지역에서 Azure VM을 사용 하는 것이 좋습니다. 일반적으로 네트워크 속도를 더 빠르게 제공 합니다.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker push는 성공 하지만 오류가 발생 하 docker pull 실패 합니다. 권한이 없음: 인증 필요

이 오류는 Docker 디먼의 Red Hat 버전에서 발생할 수 있습니다. 여기서 `--signature-verification`는 기본적으로 사용 하도록 설정 되어 있습니다. 다음 명령을 실행 하 여 Red Hat Enterprise Linux (RHEL) 또는 Fedora에 대한 Docker 디먼 옵션을 확인할 수 있습니다.

```bash
grep OPTIONS /etc/sysconfig/docker
```

예를 들어 Fedora 28 서버에는 다음과 같은 docker 데몬 옵션이 있습니다.

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

`--signature-verification=false` 누락 되 면 다음과 유사한 오류가 발생 하 여 `docker pull` 실패 합니다.

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

오류를 해결하려면:
1. `--signature-verification=false` 옵션을 Docker 디먼 구성 파일 `/etc/sysconfig/docker`에 추가 합니다. 예:

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. 다음 명령을 실행 하 여 Docker 디먼 서비스를 다시 시작 합니다.

  ```bash
  sudo systemctl restart docker.service
  ```

`man dockerd`를 실행 하 여 `--signature-verification`에 대한 세부 정보를 찾을 수 있습니다.

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr login은 성공 하지만 docker는 다음 오류로 인해 실패: 권한 없음: 인증 필요

모든 소문자 서버 URL (예:)을 사용 해야 합니다. 예를 들어 레지스트리 리소스 이름이 대문자 또는 대/소문자 혼합 (`myRegistry`) 인 경우에도 `docker push myregistry.azurecr.io/myimage:latest`합니다.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Docker 디먼의 디버그 로그를 사용 하도록 설정 하 고 가져옵니다.  

`debug` 옵션을 사용 하 여 `dockerd`를 시작 합니다. 먼저 Docker 디먼 구성 파일 (`/etc/docker/daemon.json`)을 만들고 (없는 경우) `debug` 옵션을 추가 합니다.

```json
{   
    "debug": true   
}
```

그런 다음 디먼을 다시 시작 합니다. 예를 들어 Ubuntu 14.04를 사용 합니다.

```bash
sudo service docker restart
```

세부 정보는 [Docker 설명서](https://docs.docker.com/engine/admin/#enable-debugging)에서 찾을 수 있습니다. 

 * 로그는 시스템에 따라 다른 위치에서 생성 될 수 있습니다. 예를 들어 Ubuntu 14.04의 경우 `/var/log/upstart/docker.log`됩니다.   
자세한 내용은 [Docker 설명서](https://docs.docker.com/engine/admin/#read-the-logs) 를 참조 하세요.    

 * Windows용 Docker에 대한 로그 는% LOCALAPPDATA%/docker/. 아래에 생성 됩니다. 그러나 일부 디버그 정보는 아직 포함 되지 않을 수 있습니다.   

   전체 데몬 로그에 액세스 하기 위해 몇 가지 추가 단계가 필요할 수 있습니다.

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    이제 `dockerd`를 실행 하는 VM의 모든 파일에 액세스할 수 있습니다. 로그는 `/var/log/docker.log`에 있습니다.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>업데이트 후 즉시 새 사용자 권한이 적용 되지 않을 수 있습니다.

서비스 사용자에 게 새 권한 (새 역할)을 부여 하면 변경 내용이 즉시 적용 되지 않을 수 있습니다. 가능한 두 가지 이유는 다음과 같습니다.

* Azure Active Directory 역할 할당 지연입니다. 일반적으로 속도가 빠르며 전파 지연으로 인해 몇 분 정도 걸릴 수 있습니다.
* ACR 토큰 서버에 대한 사용 권한 지연입니다. 최대 10분이 걸릴 수 있습니다. 이를 완화 하려면 1 분 후에 동일한 사용자를 사용 하 여 다시 `docker logout` 다음 인증을 수행 하면 됩니다.

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

현재 ACR은 사용자의 홈 복제 삭제를 지원 하지 않습니다. 이 문제를 해결 하려면 템플릿에서 홈 복제 만들기를 포함 하지만 아래와 같이 `"condition": false`를 추가 하 여 만들기를 건너뜁니다.

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>직접 REST API 호출에서 인증 정보는 올바른 형식으로 제공 되지 않습니다.

`InvalidAuthenticationInfo` 오류가 발생할 수 있습니다. 특히 `curl` 도구를 사용 하 여 `-L`, `--location`를 사용 하 여 리디렉션을 수행 합니다.
예를 들어 `-L` 옵션과 기본 인증을 사용 하 여 `curl`를 사용 하 여 blob을 인출 합니다.

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

다음 응답이 발생할 수 있습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

근본 원인은 일부 `curl` 구현이 원래 요청의 헤더를 사용 하 여 리디렉션을 따르는 것입니다.

이 문제를 해결 하려면 헤더 없이 리디렉션을 수동으로 수행 해야 합니다. `curl`의 `-D -` 옵션을 사용 하 여 응답 헤더를 인쇄 한 다음 `Location` 헤더를 추출 합니다.

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Azure Portal에서 내 리포지토리 또는 태그를 모두 나열 하지 않는 이유는 무엇 인가요? 

Microsoft Edge/IE 브라우저를 사용 하는 경우 최대 100 개의 리포지토리 또는 태그를 볼 수 있습니다. 레지스트리에 100 개 이상의 리포지토리가 있는 경우 Firefox 또는 Chrome 브라우저를 사용 하 여 모두 나열 하는 것이 좋습니다.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Azure Portal에서 리포지토리 또는 태그를 가져오는 데 실패 하는 이유는 무엇 인가요?

브라우저에서 리포지토리 또는 태그를 가져오기 위한 요청을 서버로 보내지 못할 수 있습니다. 다음과 같은 여러 가지 이유가 있을 수 있습니다.

* 네트워크 연결 부족
* 방화벽
* 광고 차단
* DNS 오류

네트워크 관리자에 게 문의 하거나 네트워크 구성 및 연결을 확인 하십시오. Azure CLI를 사용 하 여 `az acr check-health -n yourRegistry`를 실행 하 여 환경에서 Container Registry에 연결할 수 있는지 확인 합니다. 또한 브라우저에서 incognito 또는 private 세션을 시도 하 여 오래 된 브라우저 캐시 또는 쿠키를 방지할 수도 있습니다.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>끌어오기 또는 푸시 요청이 허용 되지 않는 작업으로 인해 실패 하는 이유는 무엇 인가요?

다음은 작업이 허용 되지 않을 수 있는 몇 가지 시나리오입니다.
* 클래식 레지스트리는 더 이상 지원 되지 않습니다. [Az acr update](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-update) 또는 Azure Portal를 사용 하 여 지원 되는 [sku](https://aka.ms/acr/skus) 로 업그레이드 하세요.
* 이미지나 리포지토리가 삭제 되거나 업데이트 되지 않도록 잠글 수 있습니다. [Az acr show repository](https://docs.microsoft.com/azure/container-registry/container-registry-image-lock) 명령을 사용 하 여 현재 특성을 볼 수 있습니다.
* 일부 작업은 이미지가 격리 된 경우에만 허용 됩니다. [격리](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)에 대해 자세히 알아보세요.

### <a name="how-do-i-collect-http-traces-on-windows"></a>Windows에서 http 추적을 수집 어떻게 할까요?

#### <a name="prerequisites"></a>필수 조건

- Fiddler에서 https 암호 해독 사용: <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Docker ui를 통해 프록시를 사용 하도록 Docker를 사용 하도록 설정: <https://docs.docker.com/docker-for-windows/#proxies>
- 완료 되 면 되돌려야 합니다.  Docker는이 사용 하도록 설정 되어 있고 실행 되 고 있지 fiddler 작동 하지 않습니다.

#### <a name="windows-containers"></a>Windows 컨테이너

Docker 프록시를 127.0.0.1로 구성: 8888

#### <a name="linux-containers"></a>Linux 컨테이너

Docker vm 가상 스위치의 ip를 찾습니다.

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

이전 명령의 출력 및 포트 8888 (예: 10.0.75.1:8888)에 대한 Docker 프록시를 구성 합니다.

## <a name="tasks"></a>작업

- [일괄 처리 취소가 실행 어떻게 할까요??](#how-do-i-batch-cancel-runs)
- [Az acr build 명령에. a g 폴더를 포함 어떻게 할까요??](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [태스크가 원본 트리거에 대해 GitLab을 지원 하나요?](#does-tasks-support-gitlab-for-source-triggers)
- [작업에서 지원 되는 git 리포지토리 관리 서비스는 무엇 인가요?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>일괄 처리 취소가 실행 어떻게 할까요??

다음 명령은 지정 된 레지스트리에서 실행 중인 모든 작업을 취소 합니다.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Az acr build 명령에. a g 폴더를 포함 어떻게 할까요??

`az acr build` 명령에 로컬 원본 폴더를 전달 하는 경우 기본적으로 업로드 된 패키지에서 `.git` 폴더가 제외 됩니다. 다음 설정을 사용 하 여 `.dockerignore` 파일을 만들 수 있습니다. 업로드 된 패키지에서 `.git` 아래의 모든 파일을 복원 하는 명령을 지시 합니다. 

```sh
!.git/**
```

이 설정은 `az acr run` 명령에도 적용 됩니다.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>태스크가 원본 트리거에 대해 GitLab을 지원 하나요?

현재 원본 트리거에 대해 GitLab을 지원 하지 않습니다.

### <a name="what-git-repository-management-service-does-tasks-support"></a>작업에서 지원 되는 git 리포지토리 관리 서비스는 무엇 인가요?

| Git 서비스 | 원본 컨텍스트 | 수동 빌드 | Commit 트리거를 통한 자동 빌드 |
|---|---|---|---|
| GitHub | https://github.com/user/myapp-repo.git#mybranch:myfolder | 예 | 예 |
| Azure Repos | https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder | 예 | 예 |
| GitLab | https://gitlab.com/user/myapp-repo.git#mybranch:myfolder | 예 | 아닙니다. |
| BitBucket | https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder | 예 | 아닙니다. |

## <a name="run-error-message-troubleshooting"></a>오류 메시지 실행 문제 해결

| 오류 메시지 | 문제 해결 가이드 |
|---|---|
|VM에 대한 액세스가 구성 되지 않았으므로 구독을 찾을 수 없습니다.|ACR 작업에서 `az login --identity`를 사용 하는 경우 발생할 수 있습니다. 이 오류는 일시적인 오류 이며 관리 되는 Id의 역할 할당이 전파 되지 않은 경우에 발생 합니다. 몇 초 후에 작업을 다시 시도 합니다.|

## <a name="cicd-integration"></a>CI/CD 통합

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub 작업](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>다음 단계

* Azure Container Registry에 [대해 자세히 알아보세요](container-registry-intro.md) .
