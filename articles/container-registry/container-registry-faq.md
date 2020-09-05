---
title: 질문과 대답
description: Azure Container Registry 서비스와 관련된 질문과 대답입니다.
author: sajayantony
ms.topic: article
ms.date: 03/18/2020
ms.author: sajaya
ms.openlocfilehash: 02facedda206a5621cabe62a07520303635dc3ff
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245369"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Azure Container Registry에 대한 질문과 대답

이 문서에서는 Azure Container Registry에 대한 질문과 대답 및 알려진 문제에 대해 설명합니다.

레지스트리 문제 해결에 대 한 지침은 다음을 참조 하세요.
* [레지스트리 로그인 문제 해결](container-registry-troubleshoot-login.md)
* [레지스트리의 네트워크 문제 해결](container-registry-troubleshoot-access.md)
* [레지스트리 성능 문제 해결](container-registry-troubleshoot-performance.md)

## <a name="resource-management"></a>리소스 관리

- [Resource Manager 템플릿을 사용하여 Azure 컨테이너 레지스트리를 만들 수 있나요?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [ACR에서 이미지를 검사하는 보안 취약성이 있나요?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Azure Container Registry를 사용하여 Kubernetes를 구성하려면 어떻게 하나요?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [컨테이너 레지스트리에 대한 관리자 자격 증명을 가져오려면 어떻게 하나요?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Resource Manager 템플릿에서 관리자 자격 증명을 가져오려면 어떻게 하나요?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Azure CLI 또는 Azure PowerShell을 사용하여 복제가 삭제되었지만 복제 삭제가 금지됨 상태로 실패함](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [방화벽 규칙이 성공적으로 업데이트되었지만 적용되지 않음](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 Azure 컨테이너 레지스트리를 만들 수 있나요?

예. 레지스트리를 만드는 데 사용할 수 있는 [템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry)이 있습니다.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>ACR에서 이미지를 검사하는 보안 취약성이 있나요?

예. [Azure Security Center](../security-center/azure-container-registry-integration.md), [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) 및 [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry) 설명서를 참조하세요.

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Azure Container Registry를 사용하여 Kubernetes를 구성하려면 어떻게 하나요?

[Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) 설명서 및 [Azure Kubernetes Service](../aks/cluster-container-registry-integration.md) 단계를 참조하세요.

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>컨테이너 레지스트리에 대한 관리자 자격 증명을 가져오려면 어떻게 하나요?

> [!IMPORTANT]
> 관리 사용자 계정은 주로 테스트를 위해 단일 사용자가 레지스트리에 액세스할 수 있도록 설계되었습니다. 관리자 계정 자격 증명을 여러 사용자와 공유하지 않는 것이 좋습니다. 헤드리스 시나리오의 경우 사용자 및 서비스 주체는 개별 ID를 사용하는 것이 좋습니다. [인증 개요](container-registry-authentication.md)를 참조하세요.

관리자 자격 증명을 가져오기 전에 레지스트리의 관리 사용자를 사용하도록 설정되어 있는지 확인합니다.

Azure CLI를 사용하여 자격 증명을 가져오려면 다음을 수행합니다.

```azurecli
az acr credential show -n myRegistry
```

Azure PowerShell 사용:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Resource Manager 템플릿에서 관리자 자격 증명을 가져오려면 어떻게 하나요?

> [!IMPORTANT]
> 관리 사용자 계정은 주로 테스트를 위해 단일 사용자가 레지스트리에 액세스할 수 있도록 설계되었습니다. 관리자 계정 자격 증명을 여러 사용자와 공유하지 않는 것이 좋습니다. 헤드리스 시나리오의 경우 사용자 및 서비스 주체는 개별 ID를 사용하는 것이 좋습니다. [인증 개요](container-registry-authentication.md)를 참조하세요.

관리자 자격 증명을 가져오기 전에 레지스트리의 관리 사용자를 사용하도록 설정되어 있는지 확인합니다.

첫 번째 암호를 가져오려면 다음을 수행합니다.

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

두 번째 암호를 가져오려면 다음을 수행합니다.

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Azure CLI 또는 Azure PowerShell을 사용하여 복제가 삭제되었지만 복제 삭제가 금지됨 상태로 실패함

사용자에게 레지스트리에 대한 권한이 있지만 구독에 대한 읽기 권한자 수준 권한이 없는 경우 이 오류가 표시됩니다. 이 문제를 해결하려면 사용자에게 구독에 대한 읽기 권한자 권한을 할당합니다.


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>방화벽 규칙이 성공적으로 업데이트되었지만 적용되지 않음

방화벽 규칙 변경 내용을 전파하는 데 다소 시간이 걸립니다. 방화벽 설정이 변경되면 몇 분 정도 기다렸다가 해당 변경 내용을 확인하세요.


## <a name="registry-operations"></a>레지스트리 작업

- [Docker 레지스트리 HTTP API V2에 액세스하려면 어떻게 하나요?](#how-do-i-access-docker-registry-http-api-v2)
- [리포지토리의 태그에서 참조하지 않는 매니페스트를 모두 삭제하려면 어떻게 하나요?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [이미지를 삭제한 후에도 레지스트리 할당량 사용량이 감소하지 않는 이유는 무엇인가요?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [스토리지 할당량 변경의 유효성을 검사하려면 어떻게 하나요?](#how-do-i-validate-storage-quota-changes)
- [컨테이너에서 CLI를 실행할 때 내 레지스트리를 사용하여 인증하려면 어떻게 하나요?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [TLS 1.2를 사용하도록 설정하려면 어떻게 하나요?](#how-to-enable-tls-12)
- [Azure Container Registry에서 콘텐츠 신뢰를 지원하나요?](#does-azure-container-registry-support-content-trust)
- [레지스트리 리소스를 관리할 수 있는 권한 없이 이미지를 풀하거나 푸시할 수 있는 액세스 권한을 부여하려면 어떻게 하나요?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [자동 이미지 격리를 레지스트리에 사용하도록 설정하려면 어떻게 하나요?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [익명 풀 액세스를 사용하도록 설정하려면 어떻게 하나요?](#how-do-i-enable-anonymous-pull-access)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Docker 레지스트리 HTTP API V2에 액세스하려면 어떻게 하나요?

ACR은 Docker Registry HTTP API V2를 지원합니다. API는 `https://<your registry login server>/v2/`에서 액세스할 수 있습니다. 예: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>리포지토리의 태그에서 참조하지 않는 매니페스트를 모두 삭제하려면 어떻게 하나요?

Bash의 경우:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

PowerShell의 경우:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

참고: `-y`를 delete 명령에 추가하여 확인을 건너뛸 수 있습니다.

자세한 내용은 [Azure Container Registry에서 컨테이너 이미지 삭제](container-registry-delete.md)를 참조하세요.

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>이미지를 삭제한 후에도 레지스트리 할당량 사용량이 감소하지 않는 이유는 무엇인가요?

다른 컨테이너 이미지에서 기본 계층을 여전히 참조하고 있는 경우 이 상황이 발생할 수 있습니다. 참조가 없는 이미지를 삭제하면 몇 분 안에 레지스트리 사용량이 업데이트됩니다.

### <a name="how-do-i-validate-storage-quota-changes"></a>스토리지 할당량 변경의 유효성을 검사하려면 어떻게 하나요?

다음 Docker 파일을 사용하여 이미지를 1GB 계층으로 만듭니다. 이렇게 하면 레지스트리의 다른 이미지와 공유하지 않는 계층이 이미지에 있습니다.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Docker CLI를 사용하여 이미지를 빌드하고 레지스트리에 푸시합니다.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Azure Portal에서 스토리지 사용량이 증가했음을 확인하거나 CLI를 사용하여 사용량을 쿼리할 수 있습니다.

```azurecli
az acr show-usage -n myregistry
```

Azure CLI 또는 포털을 사용하여 이미지를 삭제하고 몇 분 안에 업데이트된 사용량을 확인합니다.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>컨테이너에서 CLI를 실행할 때 내 레지스트리를 사용하여 인증하려면 어떻게 하나요?

Docker 소켓을 탑재하여 Azure CLI 컨테이너를 실행해야 합니다.

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

컨테이너에서 `docker`를 설치합니다.

```bash
apk --update add docker
```

그런 다음, 레지스트리를 사용하여 인증합니다.

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>TLS 1.2를 사용하도록 설정하려면 어떻게 하나요?

최신 Docker 클라이언트(버전 18.03.0 이상)를 사용하여 TLS 1.2를 사용하도록 설정합니다. 

> [!IMPORTANT]
> 2020년 1월 13일부터 Azure Container Registry는 TLS 1.2를 사용하기 위해 서버 및 애플리케이션의 모든 보안 연결이 필요합니다. TLS 1.0 및 1.1에 대한 지원이 중단됩니다.

### <a name="does-azure-container-registry-support-content-trust"></a>Azure Container Registry에서 콘텐츠 신뢰를 지원하나요?

예, [Docker Notary](https://docs.docker.com/notary/getting_started/)가 통합되어 사용하도록 설정될 수 있으므로 Azure Container Registry에서 신뢰할 수 있는 이미지를 사용할 수 있습니다. 자세한 내용은 [Azure Container Registry의 콘텐츠 신뢰](container-registry-content-trust.md)를 참조하세요.


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>지문 파일은 어디에 있나요?

`~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata` 아래에서:

* 위임 역할을 제외한 모든 역할의 공개 키 및 인증서는 `root.json`에 저장되어 있습니다.
* 위임 역할의 공개 키 및 인증서는 해당 부모 역할의 JSON 파일(예: `targets/releases` 역할에 대한 `targets.json`)에 저장되어 있습니다.

Docker 및 Notary 클라이언트에서 전체 TUF 확인을 수행한 후에 이러한 공개 키 및 인증서를 확인하는 것이 좋습니다.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>레지스트리 리소스를 관리할 수 있는 권한 없이 이미지를 풀하거나 푸시할 수 있는 액세스 권한을 부여하려면 어떻게 하나요?

ACR은 다양한 수준의 권한을 제공하는 [사용자 지정 역할](container-registry-roles.md)을 지원합니다. 특히 `AcrPull` 및 `AcrPush` 역할을 통해 사용자는 Azure에서 레지스트리 리소스를 관리할 수 있는 권한 없이 이미지를 풀하거나 푸시할 수 있습니다.

* Azure Portal: 레지스트리 -> 액세스 제어(IAM) -> 추가(역할에 대해 `AcrPull` 또는 `AcrPush` 선택)
* Azure CLI: 다음 명령을 실행하여 레지스트리의 리소스 ID를 찾습니다.

  ```azurecli
  az acr show -n myRegistry
  ```
  
  그런 다음, 사용자에게 `AcrPull` 또는 `AcrPush` 역할을 할당할 수 있습니다(다음 예제에서는 `AcrPull` 사용).

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  또는 해당 응용 프로그램 ID로 식별 되는 서비스 사용자에 게 역할을 할당 합니다.

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

그러면 담당자가 레지스트리의 이미지를 인증하고 액세스할 수 있습니다.

* 레지스트리에 인증하려면:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* 리포지토리를 나열하려면:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* 이미지를 끌어오려면:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

`AcrPull` 또는 `AcrPush` 역할만 사용하는 경우 담당자에게는 Azure에서 레지스트리 리소스를 관리할 수 있는 권한이 없습니다. 예를 들어 `az acr list` 또는 `az acr show -n myRegistry`에서 레지스트리가 표시되지 않습니다.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>자동 이미지 격리를 레지스트리에 사용하도록 설정하려면 어떻게 하나요?

이미지 격리는 현재 ACR의 미리 보기 기능입니다. 보안 검사를 성공적으로 통과한 이미지만 일반 사용자에게 표시되도록 레지스트리의 격리 모드를 사용하도록 설정할 수 있습니다. 자세한 내용은 [ACR GitHub 리포지토리](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)를 참조하세요.

### <a name="how-do-i-enable-anonymous-pull-access"></a>익명 풀 액세스를 사용하도록 설정하려면 어떻게 하나요?

익명(퍼블릭) 풀 액세스에 대한 Azure 컨테이너 레지스트리 설정은 현재 미리 보기 기능입니다. 레지스트리에 [범위 맵 (사용자) 또는 토큰 리소스가](https://aka.ms/acr/repo-permissions) 있는 경우 지원 티켓을 발생 시키기 전에 삭제 하십시오 (시스템 범위 맵은 무시 될 수 있음). 퍼블릭 액세스를 사용하도록 설정하려면 https://aka.ms/acr/support/create-ticket 에서 지원 티켓을 여세요. 자세한 내용은 [Azure 피드백 포럼](https://feedback.azure.com/forums/903958-azure-container-registry/suggestions/32517127-enable-anonymous-access-to-registries)을 참조하세요.




## <a name="diagnostics-and-health-checks"></a>진단 및 상태 검사

- [`az acr check-health`를 사용하여 상태 검사](#check-health-with-az-acr-check-health)
- ['net/http: 연결을 기다리는 동안 요청이 취소되었습니다(헤더를 기다리는 동안 Client.Timeout이 초과됨).' 오류로 인해 docker pull이 실패함](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker push가 성공했지만 '권한 없음: 인증 필요' 오류로 인해 docker pull이 실패함](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login`이 성공했지만 '권한 없음: 인증 필요' 오류로 인해 docker 명령이 실패함](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Docker 디먼의 디버그 로그를 사용하도록 설정하고 가져옴](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [업데이트 후 새 사용자 권한이 즉시 적용되지 않을 수 있음](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [직접 REST API 호출에서 인증 정보가 올바른 형식으로 제공되지 않음](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Azure Portal에서 내 리포지토리 또는 태그를 모두 나열하지 않는 이유는 무엇인가요?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Azure Portal에서 리포지토리 또는 태그를 가져오지 못하는 이유는 무엇인가요?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [허용되지 않은 작업으로 인해 풀 또는 푸시 요청이 실패하는 이유는 무엇인가요?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [리포지토리 형식이 잘못 되었거나 지원 되지 않습니다.](#repository-format-is-invalid-or-unsupported)
- [Windows에서 http 추적을 수집하려면 어떻게 하나요?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>`az acr check-health`를 사용하여 상태 검사

일반적인 환경 및 레지스트리 문제를 해결하려면 [Azure 컨테이너 레지스트리의 상태 검사](container-registry-check-health.md)를 참조하세요.

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>'net/http: 연결을 기다리는 동안 요청이 취소되었습니다(헤더를 기다리는 동안 Client.Timeout이 초과됨).' 오류로 인해 docker pull이 실패함

 - 이 오류가 일시적인 문제인 경우 다시 시도하면 성공합니다.
 - `docker pull`이 계속 실패하면 Docker 디먼에 문제가 있을 수 있습니다. 일반적으로 Docker 디먼을 다시 시작하여 문제를 완화할 수 있습니다. 
 - Docker 디먼을 다시 시작한 후에도 이 문제가 계속 표시되면 머신의 일부 네트워크 연결 문제일 수 있습니다. 머신의 일반 네트워크가 정상인지 확인하려면 다음 명령을 실행하여 엔드포인트 연결을 테스트합니다. 이 연결 검사 명령이 포함된 최소 `az acr` 버전은 2.2.9입니다. 이전 버전을 사용하는 경우 Azure CLI를 업그레이드하세요.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - 모든 Docker 클라이언트 작업에는 항상 다시 시도 메커니즘이 있어야 합니다.

### <a name="docker-pull-is-slow"></a>docker pull 속도가 느림
[이](http://www.azurespeed.com/Azure/Download) 도구를 사용하여 머신 네트워크 다운로드 속도를 테스트합니다. 머신 네트워크가 느릴 경우 레지스트리와 동일한 지역에서 Azure VM을 사용하는 것이 좋습니다. 이렇게 하면 일반적으로 네트워크 속도가 빨라집니다.

### <a name="docker-push-is-slow"></a>docker push 속도가 느림
[이](http://www.azurespeed.com/Azure/Upload) 도구를 사용하여 머신 네트워크 업로드 속도를 테스트합니다. 머신 네트워크가 느릴 경우 레지스트리와 동일한 지역에서 Azure VM을 사용하는 것이 좋습니다. 이렇게 하면 일반적으로 네트워크 속도가 빨라집니다.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>docker push가 성공했지만 '권한 없음: 인증 필요' 오류로 인해 docker pull이 실패함

이 오류는 기본적으로 `--signature-verification`을 사용하도록 설정되어 있는 Docker 디먼의 Red Hat 버전에서 발생할 수 있습니다. 다음 명령을 실행하여 RHEL(Red Hat Enterprise Linux) 또는 Fedora에 대한 Docker 디먼 옵션을 확인할 수 있습니다.

```bash
grep OPTIONS /etc/sysconfig/docker
```

예를 들어 Fedora 28 Server에 있는 Docker 디먼 옵션은 다음과 같습니다.

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

`--signature-verification=false`가 누락되면 다음과 비슷한 오류로 인해 `docker pull`이 실패합니다.

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

오류를 해결하려면:
1. `--signature-verification=false` 옵션을 Docker 디먼 구성 파일(`/etc/sysconfig/docker`)에 추가합니다. 다음은 그 예입니다.
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. 다음 명령을 실행하여 Docker 디먼 서비스를 다시 시작합니다.
   
   ```bash
   sudo systemctl restart docker.service
   ```

`man dockerd`를 실행하여 `--signature-verification`의 세부 정보를 확인할 수 있습니다.

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr login이 성공했지만 '권한 없음: 인증 필요' 오류로 인해 docker 명령이 실패함

레지스트리 리소스 이름이 대문자 또는 대/소문자(예: `myRegistry`)인 경우에도 모두 소문자인 서버 URL(예: `docker push myregistry.azurecr.io/myimage:latest`)을 사용해야 합니다.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Docker 디먼의 디버그 로그를 사용하도록 설정하고 가져옴    

`debug` 옵션을 사용하여 `dockerd`를 시작합니다. 먼저 Docker 디먼 구성 파일(`/etc/docker/daemon.json`)이 없는 경우 해당 파일을 만들고, `debug` 옵션을 추가합니다.

```json
{    
    "debug": true    
}
```

그런 다음, 디먼을 다시 시작합니다. 예를 들어 Ubuntu 14.04를 사용하는 경우 다음과 같습니다.

```bash
sudo service docker restart
```

자세한 내용은 [Docker 설명서](https://docs.docker.com/engine/admin/#enable-debugging)에서 확인할 수 있습니다.    

 * 로그는 시스템에 따라 다른 위치에서 생성될 수 있습니다. 예를 들어 Ubuntu 14.04의 경우 `/var/log/upstart/docker.log`입니다.    
자세한 내용은 [Docker 설명서](https://docs.docker.com/engine/admin/#read-the-logs)를 참조하세요.    

 * Windows용 Docker의 경우 로그는 %LOCALAPPDATA%/docker/ 아래에 생성됩니다. 그러나 일부 디버그 정보가 아직 포함되지 않았을 수 있습니다.    

   전체 디먼 로그에 액세스하려면 몇 가지 추가 단계가 필요할 수 있습니다.

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    이제 `dockerd`를 실행하는 VM의 모든 파일에 액세스할 수 있습니다. 로그는 `/var/log/docker.log`에 있습니다.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>업데이트 후 새 사용자 권한이 즉시 적용되지 않을 수 있음

새 권한(역할)을 서비스 주체에게 부여하면 변경 내용이 즉시 적용되지 않을 수 있습니다. 가능한 두 가지 이유가 있습니다.

* Azure Active Directory 역할 할당 지연. 일반적으로 속도가 빠르지만 전파 지연으로 인해 몇 분 정도 걸릴 수 있습니다.
* ACR 토큰 서버의 권한 지연. 이 작업에는 최대 10분 정도 걸릴 수 있습니다. 완화하려면 `docker logout`을 수행한 다음, 1분 후에 동일한 사용자로 다시 인증하면 됩니다.

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

ACR은 현재 사용자의 홈 복제 삭제를 지원하지 않습니다. 이 문제를 해결하려면 홈 복제 만들기를 템플릿에 포함하지만 아래와 같이 `"condition": false`를 추가하여 만들기를 건너뜁니다.

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>직접 REST API 호출에서 인증 정보가 올바른 형식으로 제공되지 않음

특히 리디렉션을 수행하기 위해 `-L`, `--location` 옵션이 포함된 `curl` 도구를 사용하는 경우 `InvalidAuthenticationInfo` 오류가 발생할 수 있습니다.
예를 들어 `-L` 옵션이 있는 `curl` 및 기본 인증을 사용하여 Blob을 가져오는 경우입니다.

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

다음과 같은 응답이 발생할 수 있습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

근본 원인은 일부 `curl` 구현에서 원래 요청의 헤더를 사용하여 리디렉션을 수행한다는 것입니다.

이 문제를 해결하려면 헤더 없이 수동으로 리디렉션을 수행해야 합니다. `curl`의 `-D -` 옵션을 사용하여 응답 헤더를 출력한 다음, `Location` 헤더를 추출합니다.

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Azure Portal에서 내 리포지토리 또는 태그를 모두 나열하지 않는 이유는 무엇인가요? 

Microsoft Edge/IE 브라우저를 사용하는 경우 최대 100개의 리포지토리 또는 태그가 표시될 수 있습니다. 100개 이상의 리포지토리 또는 태그가 레지스트리에 있는 경우 Firefox 또는 Chrome 브라우저를 사용하여 모두 나열하는 것이 좋습니다.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Azure Portal에서 리포지토리 또는 태그를 가져오지 못하는 이유는 무엇인가요?

브라우저에서 리포지토리 또는 태그를 가져오기 위한 요청을 서버에 보내지 못할 수 있습니다. 다음과 같은 여러 가지 이유가 있을 수 있습니다.

* 네트워크 연결 부족
* 방화벽
* 광고 차단
* DNS 오류

네트워크 관리자에게 문의하거나 네트워크 구성 및 연결을 확인하세요. 환경에서 컨테이너 레지스트리에 연결할 수 있는지 확인하려면 Azure CLI를 사용하여 `az acr check-health -n yourRegistry`를 실행해 봅니다. 또한 오래된 브라우저 캐시 또는 쿠키를 방지하기 위해 브라우저에서 incognito(시크릿 모드) 또는 프라이빗 세션을 시도할 수도 있습니다.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>허용되지 않은 작업으로 인해 풀 또는 푸시 요청이 실패하는 이유는 무엇인가요?

작업이 허용될 수 없는 몇 가지 시나리오는 다음과 같습니다.
* 클래식 레지스트리가 더 이상 지원되지 않습니다. [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) 또는 Azure Portal을 사용하여 지원되는 [서비스 계층](https://aka.ms/acr/skus)으로 업그레이드하세요.
* 이미지 또는 리포지토리가 잠겨 있어 삭제하거나 업데이트할 수 없습니다. [az acr show repository](./container-registry-image-lock.md) 명령을 사용하여 현재 특성을 볼 수 있습니다.
* 이미지가 격리 모드에 있는 경우 일부 작업이 허용되지 않습니다. [격리](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)에 대해 자세히 알아보세요.
* 레지스트리가 [저장소 용량 한도](container-registry-skus.md#service-tier-features-and-limits)에 도달 했을 수 있습니다.

### <a name="repository-format-is-invalid-or-unsupported"></a>리포지토리 형식이 잘못 되었거나 지원 되지 않습니다.

리포지토리 작업에서 리포지토리 이름을 지정할 때 "지원 되지 않는 리포지토리 형식", "잘못 된 형식" 또는 "요청 된 데이터가 없습니다."와 같은 오류가 표시 되는 경우 이름의 철자 및 대/소문자를 확인 합니다. 유효한 리포지토리 이름에는 소문자 영숫자, 마침표, 대시, 밑줄 및 슬래시만 포함할 수 있습니다. 

전체 리포지토리 명명 규칙은 [Open Container 이니셔티브 배포 사양](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview)을 참조 하세요.

### <a name="how-do-i-collect-http-traces-on-windows"></a>Windows에서 http 추적을 수집하려면 어떻게 하나요?

#### <a name="prerequisites"></a>사전 요구 사항

- Fiddler(<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>)에서 https 암호 해독을 사용하도록 설정합니다
- Docker UI(<https://docs.docker.com/docker-for-windows/#proxies>)에서 프록시를 통해 Docker를 사용하도록 설정합니다.
- 완료되면 되돌려야 합니다.  이 기능을 사용하도록 설정되어 있고 Fiddler가 실행되지 않으면 Docker가 작동하지 않습니다.

#### <a name="windows-containers"></a>Windows 컨테이너

Docker 프록시를 127.0.0.1:8888로 구성합니다.

#### <a name="linux-containers"></a>Linux 컨테이너

Docker vm 가상 스위치의 ip를 찾습니다.

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Docker 프록시를 이전 명령과 8888 포트(예: 10.0.75.1:8888)의 출력에 맞게 구성합니다.

## <a name="tasks"></a>작업

- [취소 실행을 일괄 처리하려면 어떻게 하나요?](#how-do-i-batch-cancel-runs)
- [.git 폴더를 az acr build 명령에 포함하려면 어떻게 하나요?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [작업에서 원본 트리거에 대해 GitLab을 지원하나요?](#does-tasks-support-gitlab-for-source-triggers)
- [작업에서 지원하는 git 리포지토리 관리 서비스는 무엇인가요?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>취소 실행을 일괄 처리하려면 어떻게 하나요?

다음 명령은 지정된 레지스트리에서 실행 중인 모든 작업을 취소합니다.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>.git 폴더를 az acr build 명령에 포함하려면 어떻게 하나요?

로컬 원본 폴더를 `az acr build` 명령에 전달하면 기본적으로 업로드된 패키지에서 `.git` 폴더가 제외됩니다. `.dockerignore` 파일은 다음 설정을 사용하여 만들 수 있습니다. 이 설정은 업로드된 패키지의 `.git` 아래에 있는 모든 파일을 복원하도록 명령에 지시합니다. 

`!.git/**`

이 설정은 `az acr run` 명령에도 적용됩니다.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>작업에서 원본 트리거에 대해 GitLab을 지원하나요?

현재 원본 트리거에 대해 GitLab을 지원하지 않습니다.

### <a name="what-git-repository-management-service-does-tasks-support"></a>작업에서 지원하는 git 리포지토리 관리 서비스는 무엇인가요?

| Git 서비스 | 원본 컨텍스트 | 수동 빌드 | 커밋 트리거를 통한 자동 빌드 |
|---|---|---|---|
| GitHub | `https://github.com/user/myapp-repo.git#mybranch:myfolder` | 예 | 예 |
| Azure Repos | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` | 예 | 예 |
| GitLab | `https://gitlab.com/user/myapp-repo.git#mybranch:myfolder` | 예 | 예 |
| BitBucket | `https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder` | 예 | 예 |

## <a name="run-error-message-troubleshooting"></a>실행 오류 메시지 문제 해결

| 오류 메시지 | 문제 해결 가이드 |
|---|---|
|VM에 대한 액세스가 구성되지 않았으므로 구독을 찾을 수 없음|ACR 작업에서 `az login --identity`를 사용하는 경우 이 오류가 발생할 수 있습니다. 이는 일시적인 오류이며, 관리 ID의 역할 할당이 전파되지 않은 경우에 발생합니다. 몇 초 동안 기다린 후에 다시 시도하세요.|

## <a name="cicd-integration"></a>CI/CD 통합

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub Actions](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>다음 단계

* Azure Container Registry에 대해 [자세히 알아봅니다](container-registry-intro.md).
