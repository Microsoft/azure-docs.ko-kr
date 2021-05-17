---
title: AKS(Azure Kubernetes Service)에서 OpenFaaS 사용
description: AKS(Azure Kubernetes Service) 클러스터에서 OpenFaaS를 배포한 후 사용하여 컨테이너로 서버리스 Functions를 빌드하는 방법에 대해 알아봅니다
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 319107127b79383fc3b49f0eeb856a0e6c5b09f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92747774"
---
# <a name="using-openfaas-on-aks"></a>AKS에서 OpenFaaS 사용

[OpenFaaS][open-faas]는 컨테이너 사용을 통해 서버리스 Functions를 빌드하기 위한 프레임워크입니다. 오픈 소스 프로젝트로써 커뮤니티 내에서 대규모로 채택되었습니다. 이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에서 OpenFaas를 설치하고 사용하는 방법을 자세히 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 아티클 내의 단계를 완료하기 위해 다음 항목이 필요합니다.

* Kubernetes에 대한 기본적인 이해
* AKS(Azure Kubernetes Service) 클러스터 및 개발 시스템에 구성된 AKS 자격 증명
* 개발 시스템에 설치된 Azure CLI.
* 시스템에 설치된 Git 명령줄 도구

## <a name="add-the-openfaas-helm-chart-repo"></a>OpenFaaS helm 차트 리포지토리 추가

브라우저에서 [https://shell.azure.com](https://shell.azure.com)으로 이동하여 Azure Cloud Shell을 엽니다.

OpenFaaS는 모든 최신 변경 내용으로 최신 상태를 유지하기 위해 자체 helm 차트를 유지 관리합니다.

```console
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>OpenFaaS 배포

좋은 방법은 OpenFaaS 및 OpenFaaS 함수를 고유한 Kubernetes 네임스페이스에 저장하는 것입니다.

OpenFaaS 시스템과 함수에 대한 네임스페이스를 만듭니다.

```console
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

OpenFaaS UI 포털과 REST API에 대한 암호를 생성합니다.

```console
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

`echo $PASSWORD`를 사용하여 비밀의 값을 가져올 수 있습니다.

여기에서 만든 암호는 클라우드 부하 분산 장치를 통해 인터넷에 노출되는 OpenFaaS Gateway에서 기본 인증을 사용하도록 설정하기 위해 helm 차트에서 사용됩니다.

OpenFaaS에 대한 Helm 차트는 복제된 리포지토리에 포함됩니다. 이 차트를 사용하여 OpenFaaS를 AKS 클러스터에 배포합니다.

```console
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
```

출력:

```output
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

```console
kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

OpenFaaS 게이트웨이에 액세스하기 위해 공용 IP 주소를 생성합니다. 이 IP 주소를 검색하려면 [kubectl get service][kubectl-get] 명령을 사용합니다. 서비스에 IP 주소가 할당될 때까지 잠깐 시간이 걸릴 수 있습니다.

```console
kubectl get service -l component=gateway --namespace openfaas
```

출력

```output
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

OpenFaaS 시스템을 테스트하려면 포트 8080의 외부 IP 주소로 이동합니다(이 예제에서 `http://52.186.64.52:8080`). 로그인하라는 메시지가 표시됩니다. 암호를 가져오려면 `echo $PASSWORD`를 입력합니다.

![OpenFaaS UI](media/container-service-serverless/openfaas.png)

마지막으로 OpenFaaS CLI를 설치합니다. 이 예제에서는 brew를 사용합니다, 추가 옵션은 [OpenFaaS CLI 설명서][open-faas-cli]를 참조하세요.

```console
brew install faas-cli
```

`$OPENFAAS_URL`을 위에서 찾은 공용 IP로 설정합니다.

Azure CLI로 로그인합니다.

```console
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>첫 번째 함수 만들기

이제 OpenFaaS가 작동하므로 OpenFaas 포털을 사용하여 함수를 만듭니다.

**새 함수 배포** 를 클릭하고 **Figlet** 을 검색합니다. Figlet 함수를 선택하고 **배포** 를 클릭합니다.

![검색 줄에 텍스트 figlet이 입력된 새 기능 배포 대화 상자를 보여 주는 스크린샷](media/container-service-serverless/figlet.png)

Curl을 사용하여 함수를 호출합니다. 다음 예제의 IP 주소를 OpenFaas 게이트웨이의 IP 주소로 바꿉니다.

```console
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

출력:

```output
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>두 번째 함수 만들기

이제 두 번째 함수를 만듭니다. 이 예제는 OpenFaaS CLI를 사용하여 배포되고 사용자 지정 컨테이너 이미지 및 Cosmos DB의 검색 데이터를 포함합니다. 함수를 만들기 전에 몇 가지 항목을 구성해야 합니다.

먼저 Cosmos DB에 대한 새 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

`MongoDB` 종류의 CosmosDB 인스턴스를 배포합니다. 인스턴스는 고유한 이름을 입력하고, `openfaas-cosmos`를 환경에 고유한 이름으로 업데이트합니다.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Cosmos 데이터베이스 연결 문자열을 가져오고 변수에 저장합니다.

`--resource-group` 인수의 값을 리소스 그룹의 이름으로 업데이트하고 `--name` 인수를 Cosmos DB 이름으로 업데이트합니다.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

이제 테스트 데이터로 Cosmos DB를 채웁니다. 파일 `plans.json`을 만들고 다음 JSON에 복사합니다.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

*mongoimport* 도구를 사용하여 데이터가 포함된 CosmosDB 인스턴스를 로드합니다.

필요한 경우 MongoDB 도구를 설치합니다. 다음 예제에서는 brew를 사용하여 이러한 도구를 설치합니다. 기타 옵션은 [MongoDB 설명서][install-mongo]를 참조하세요.

```console
brew install mongodb
```

데이터베이스에 데이터를 로드합니다.

```console
mongoimport --uri=$COSMOS -c plans < plans.json
```

출력:

```output
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

다음 명령을 실행하여 함수를 만듭니다. `-g` 인수 값을 OpenFaaS 게이트웨이 주소로 업데이트합니다.

```console
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

해당 기능이 배포되면 함수에 새로 만든 OpenFaaS 엔드포인트가 표시됩니다.

```output
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

curl을 사용하여 함수를 테스트합니다. OpenFaaS 게이트웨이 주소로 IP 주소를 업데이트합니다.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

출력:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

OpenFaaS UI 내에서 함수를 테스트할 수도 있습니다.

![대체 텍스트](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>다음 단계

고유한 GitHub 봇 만들기, 비밀 사용, 메트릭 보기, 자동 스케일링 방법과 같은 주제를 다루는 실습 랩 집합을 통해 OpenFaaS 워크샵으로 계속 배울 수 있습니다.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
