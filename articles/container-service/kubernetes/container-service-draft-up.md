---
title: (사용되지 않음) Azure Container Service 및 Azure Container Registry에서 Draft 사용
description: ACS Kubernetes 클러스터와 Azure Container Registry를 만들어 Azure에서 Draft로 첫 번째 애플리케이션을 만듭니다.
services: container-service
author: squillace
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: fb34be09ec08957621517c957b3570cdbcfc0468
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712673"
---
# <a name="deprecated-use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>(사용되지 않음) Azure Container Service 및 Azure Container Registry에서 Draft를 사용하여 애플리케이션 빌드 및 Kubernetes에 배포

> [!TIP]
> Azure Kubernetes Service를 사용하는 이 문서의 업데이트된 버전은 [AKS(Azure Kubernetes Service)에서 Draft 사용](../../aks/kubernetes-draft.md)을 참조하세요.

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Draft](https://aka.ms/draft)는 Docker와 Kubernetes에 대해 잘 알지 못하거나 패키지를 설치하지 않고도 손쉽게 컨테이너 기반 애플리케이션을 개발하여 Kubernetes 클러스터에 배포할 수 있는 새로운 오픈 소스 도구입니다. Draft와 같은 도구를 사용하면 사용자와 팀이 Kubernetes로 애플리케이션을 빌드하는 데 집중할 수 있는 한편 인프라에는 많은 주의를 기울이지 않습니다.

로컬을 포함하여 모든 Docker 이미지 레지스트리와 Kubernetes 클러스터에서 Draft를 사용할 수 있습니다. 이 자습서에서는 Kubernetes 및 ACR과 함께 ACS를 사용하여 Kubernetes에서 Draft를 사용하여 라이브지만 안전한 개발자 파이프라인을 만드는 방법과 Azure DNS를 사용하여 다른 사용자가 도메인에서 해당 개발자 파이프라인을 볼 수 있도록 노출하는 방법을 보여 줍니다.


## <a name="create-an-azure-container-registry"></a>Azure Container Registry 만들기
[새 Azure Container Registry를 쉽게 만들](../../container-registry/container-registry-get-started-azure-cli.md) 수 있으며, 그 단계는 다음과 같습니다.

1. ACR 레지스트리 및 ACS에서 Kubernetes 클러스터를 관리 하는 Azure 리소스 그룹을 만듭니다.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. [az acr create](/cli/azure/acr#az-acr-create)를 사용하여 ACR 이미지 레지스트리를 만들고 `--admin-enabled` 옵션이 `true`로 설정되어 있는지 확인합니다.
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Kubernetes로 Azure Container Service 만들기

이제 [az acs create](/cli/azure/acs#az-acs-create)를 통해 Kubernetes를 `--orchestrator-type` 값으로 사용하여 ACS 클러스터를 만들 준비가 되었습니다.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes --generate-ssh-keys
```

> [!NOTE]
> Kubernetes는 기본 조정자 유형이 아니므로 `--orchestrator-type kubernetes` 스위치를 사용해야 합니다.

성공했을 때의 출력은 다음과 비슷합니다.

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

이제 클러스터가 있으므로 [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes) 명령을 사용하여 자격 증명을 가져올 수 있습니다. 그리고 클러스터에 대한 로컬 구성 파일이 있습니다. 이는 Helm과 Draft가 작업을 완료하는 데 필요한 파일입니다.

## <a name="install-and-configure-draft"></a>Draft 설치 및 구성


1. https://github.com/Azure/draft/releases에서 환경에 맞는 Draft를 다운로드하고 명령을 사용할 수 있도록 PATH에 설치합니다.
2. https://github.com/kubernetes/helm/releases에서 환경에 맞는 Helm을 다운로드하고 [명령을 사용할 수 있도록 PATH에 설치합니다](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client).
3. Draft를 구성하여 레지스트리를 사용하고 만든 각 Helm 차트에 대한 하위 도메인을 만듭니다. Draft을 구성하려면 다음이 필요합니다.
   - Azure Container Registry 이름(이 예제에서는 `draftacsdemo`)
   - 레지스트리 키 또는 암호(`az acr credential show -n <registry name> --output tsv --query "passwords[0].value"` 명령 사용)

   `draft init`를 호출하면 구성 프로세스에서 위의 값을 묻는 메시지를 표시합니다. 레지스트리 URL의 URL 형식은 레지스트리 이름(이 예제에서는 `draftacsdemo`)과 `.azurecr.io`입니다. 사용자 이름은 그 자체로 레지스트리 이름입니다. 프로세스를 처음 실행하면 다음과 같은 화면이 표시됩니다.
   ```bash
    $ draft init
    Creating /home/ralph/.draft 
    Creating /home/ralph/.draft/plugins 
    Creating /home/ralph/.draft/packs 
    Creating pack go...
    Creating pack python...
    Creating pack ruby...
    Creating pack javascript...
    Creating pack gradle...
    Creating pack java...
    Creating pack php...
    Creating pack csharp...
    $DRAFT_HOME has been configured at /home/ralph/.draft.

    In order to configure Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): draftacsdemo.azurecr.io
    2. Enter your username: draftacsdemo
    3. Enter your password: 
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
   ```

이제 애플리케이션을 배포할 준비가 되었습니다.


## <a name="build-and-deploy-an-application"></a>애플리케이션 빌드 및 배포

Draft 리포지토리에는 [간단한 6개 예제 애플리케이션](https://github.com/Azure/draft/tree/master/examples)이 있습니다. 해당 리포지토리를 복제하고 [Java 예제](https://github.com/Azure/draft/tree/master/examples/example-java)를 사용해 보겠습니다. examples/java 디렉터리로 변경하고 `draft create`를 입력하여 애플리케이션을 빌드합니다. 다음 예제와 같아야 합니다.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

출력에는 Dockerfile과 Helm 차트가 포함됩니다. 빌드하고 배포하려면 `draft up`만 입력하면 됩니다. 출력은 광범위하지만 다음 예제와 같이 시작해야 합니다.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>애플리케이션 안전하게 보기

이제 컨테이너가 ACS에서 실행됩니다. 이를 보려면 `draft connect` 명령을 사용합니다. 이 명령을 사용하면 애플리케이션의 특정 포트를 사용하여 클러스터 IP에 안전하게 연결하여 로컬로 볼 수 있습니다. 성공하면 **SUCCESS** 표시 다음의 첫 번째 줄에서 앱에 연결할 URL을 찾습니다.

> [!NOTE]
> Pod가 준비되지 않았다는 메시지가 표시될 경우 잠시 후 다시 시도하거나 `kubectl get pods -w`를 사용하여 포드가 준비되는 것을 확인한 다음 준비되면 다시 시도합니다.

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

앞의 예제에서 `curl -s http://localhost:46143`을 입력하면 `Hello World, I'm Java!`라는 응답을 받을 수 있었습니다. OS 환경에 따라 CTRL+C 또는 CMD+C를 누르면 보안 터널이 삭제되고 계속 반복할 수 있습니다.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Azure DNS로 배포 도메인을 구성하여 애플리케이션 공유

이전 단계에서 이미 Draft가 만드는 개발자 반복 루프를 수행했습니다. 그러나 다음 작업을 수행하여 인터넷을 통해 애플리케이션을 공유할 수 있습니다.
1. ACS 클러스터에 수신 설치(앱을 표시할 공용 IP 주소 제공)
2. Azure DNS에 사용자 지정 도메인을 위임하고 수신 컨트롤러에 할당되는 IP 주소 ACS에 도메인 매핑

### <a name="use-helm-to-install-the-ingress-controller"></a>helm을 사용하여 수신 컨트롤러 설치
**helm**을 사용하여 `stable/traefik`를 검색하여 설치하고, 수신 컨트롤러에서 빌드에 대한 인바운드 요청을 사용하도록 설정합니다.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
이제 `ingress` 컨트롤러에 대한 조사식을 설정하여 해당 컨트롤러를 배포할 때 외부 IP 값을 캡처합니다. 이 IP 주소는 다음 섹션에서 배포 도메인에 매핑됩니다.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

이 경우 배포 도메인의 외부 IP는 `13.64.108.240`입니다. 이제 도메인을 해당 IP에 매핑할 수 있습니다.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>사용자 지정 하위 도메인에 수신 IP 매핑

Draft는 자체에서 만든 각 Helm 차트, 즉 작업 중인 각 애플리케이션에 대한 릴리스를 만듭니다. 각각은 **draft**에서 사용자가 제어하는 루트 _배포 도메인_의 최상위에 있는 _하위 도메인_으로 사용하도록 생성된 이름을 가져옵니다. (이 예제에서는 `squillace.io`를 배포 도메인으로 사용합니다.) 이 하위 도메인 동작을 사용하도록 설정하려면 `'*.draft'`에 대한 A 레코드를 배포 도메인의 DNS 항목에 만들어 생성된 각 하위 도메인이 Kubernetes 클러스터의 수신 컨트롤러로 라우팅되도록 해야 합니다. 

사용자의 도메인 공급자에는 DNS 서버를 할당하는 자체의 고유한 방법이 있습니다. [도메인 이름 서버를 Azure DNS로 위임](../../dns/dns-delegate-domain-azure-dns.md)하려면 다음 단계를 수행합니다.

1. 사용자 영역에 대한 리소스 그룹을 만듭니다.
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. 도메인에 대한 DNS 영역을 만듭니다.
   [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) 명령을 사용하여 DNS 컨트롤을 도메인에 대한 Azure DNS에 위임할 이름 서버를 가져옵니다.
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. 제공된 DNS 서버를 배포 도메인의 도메인 공급자에 추가합니다. 이렇게 하면 Azure DNS를 사용하여 원하는 대로 도메인을 다시 지정할 수 있습니다. 이 작업을 수행하는 방법은 도메인 공급자에 따라 다릅니다. 알아야 할 몇 가지 세부 정보가 [도메인 이름 서버를 Azure DNS로 위임](../../dns/dns-delegate-domain-azure-dns.md)에 나와 있습니다. 
4. 도메인이 Azure DNS에 위임된 후 이전 섹션의 2단계에 있는 `ingress` IP에 매핑되는 배포 도메인에 대한 A 레코드 집합 항목을 만듭니다.
   ```azurecli
   az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*.draft' -g squillace.io -z squillace.io
   ```
   출력은 다음과 같습니다.
   ```json
   {
    "arecords": [
      {
        "ipv4Address": "13.64.108.240"
      }
    ],
    "etag": "<guid>",
    "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
    "metadata": null,
    "name": "*.draft",
    "resourceGroup": "squillace.io",
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/A"
   }
   ```
5. **draft**를 다시 설치합니다.

   1. `helm delete --purge draft`를 입력하여 클러스터에서 **draftd**를 제거합니다. 
   2. 동일한 `draft-init` 명령을 사용하되 `--ingress-enabled` 옵션을 포함하여 **draft**를 다시 설치합니다.
      ```bash
      draft init --ingress-enabled
      ```
      위에서 처음에 수행했던 것처럼 메시지에 응답합니다. 하지만 Azure DNS로 구성한 전체 도메인 경로 사용에 대한 질문에 추가로 응답해야 합니다.

6. 송신(예: draft.example.com)에 대한 최상위 도메인(draft.squillace.io)을 입력합니다.
7. 이번에 `draft up`을 호출하면 `<appname>.draft.<domain>.<top-level-domain>` 형식의 URL에서 애플리케이션(또는 `curl`)을 볼 수 있습니다. 이 예제의 경우 `http://handy-labradoodle.draft.squillace.io`입니다. 
   ```bash
   curl -s http://handy-labradoodle.draft.squillace.io
   Hello World, I'm Java!
   ```


## <a name="next-steps"></a>다음 단계

이제 ACS Kubernetes 클러스터가 있으므로 [Azure Container Registry](../../container-registry/container-registry-intro.md)에서 조사하여 이 시나리오의 다양한 배포를 만들 수 있습니다. 예를 들어 특정 ACS 배포에 대해 더 깊은 하위 도메인의 작업을 제어하는 draft._basedomain.toplevel_ 도메인 DNS 레코드 집합을 만들 수 있습니다.






