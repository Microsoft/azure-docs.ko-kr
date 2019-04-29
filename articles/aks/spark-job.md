---
title: AKS(Azure Kubernetes Service)로 Apache Spark 작업 실행
description: AKS(Azure Kubernetes Service)를 사용하여 Apache Spark 작업 실행
services: container-service
author: rockboyfor
manager: digimobile
ms.service: container-service
ms.topic: article
origin.date: 03/15/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: ddaff590fd493b430a72c30dd35cb1b891b80d84
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104955"
---
# <a name="running-apache-spark-jobs-on-aks"></a>AKS에서 Apache Spark 작업 실행

[Apache Spark][apache-spark]는 대규모 데이터 처리를 위한 고속 엔진입니다. [Spark 2.3.0 릴리스][spark-latest-release]부터 Apache Spark는 Kubernetes 클러스터와의 네이티브 통합을 지원합니다. AKS(Azure Kubernetes Service)는 Azure에서 실행되는 관리 Kubernetes 환경입니다. 이 문서에서는 Apache Spark 작업을 준비하고 AKS(Azure Kubernetes Service) 클러스터에서 실행하는 방법을 자세히 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 아티클 내의 단계를 완료하기 위해 다음 항목이 필요합니다.

* Kubernetes 및 [Apache Spark][spark-quickstart]에 대한 기본적인 이해.
* [Docker 허브][docker-hub] 계정 또는 [Azure Container Registry][acr-create].
* 개발 시스템에 [설치된][azure-cli] Azure CLI.
* 시스템에 설치된 [JDK 8][java-install].
* 시스템에 설치된 SBT([Scala 빌드 도구][sbt-install]).
* 시스템에 설치된 Git 명령줄 도구

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

Spark는 대규모 데이터 처리에 사용되며 Kubernetes 노드의 크기가 Spark 리소스 요구 사항을 충족해야 합니다. AKS(Azure Kubernetes Service) 노드의 최소 권장 크기는 `Standard_D3_v2`입니다.

이 최소 권장 사항을 충족하는 AKS 클러스터가 필요한 경우 다음 명령을 실행합니다.

클러스터용 리소스 그룹을 만듭니다.

```azurecli
az group create --name mySparkCluster --location chinaeast2
```

노드 크기가 `Standard_D3_v2`인 AKS 클러스터를 만듭니다.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2
```

AKS 클러스터에 연결합니다.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

ACR(Azure Container Registry)을 사용하여 컨테이너 이미지를 저장하는 경우 AKS와 ACR 간에 인증을 구성합니다. 구성 단계는 [ACR 인증 설명서][acr-aks]를 참조하세요.

## <a name="build-the-spark-source"></a>Spark 소스 빌드

AKS 클러스터에서 Spark 작업을 실행하기 전에 Spark 소스 코드를 빌드하고 컨테이너 이미지에 패키지해야 합니다. Spark 소스에는 이 프로세스를 완료하는 데 사용할 수 있는 스크립트가 포함되어 있습니다.

Spark 프로젝트 리포지토리를 개발 시스템에 복제합니다.

```bash
git clone -b branch-2.3 https://github.com/apache/spark
```

복제된 리포지토리의 디렉터리를 변경하고 Spark 소스의 경로를 변수에 저장합니다.

```bash
cd spark
sparkdir=$(pwd)
```

여러 가지 JDK 버전이 설치된 경우 현재 세션에 8 버전을 사용하도록 `JAVA_HOME`을 설정합니다.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

다음 명령을 실행하여 Kubernetes를 지원하는 Spark 소스 코드를 빌드합니다.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

다음 명령은 Spark 컨테이너 이미지를 만들어서 컨테이너 이미지 레지스트리에 푸시합니다. `registry.example.com`을 컨테이너 레지스트리의 이름으로 바꾸고 `v1`를 사용하려는 태그로 바꿉니다. Docker 허브를 사용하는 경우 이 값은 레지스트리 이름입니다. ACR(Azure Container Registry)을 사용하는 경우 이 값은 ACR 로그인 서버 이름입니다.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

컨테이너 이미지를 컨테이너 이미지 레지스트리에 푸시합니다.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Spark 작업 준비

다음으로, Spark 작업을 준비합니다. jar 파일은 Spark 작업을 보관하는 데 사용되며 `spark-submit` 명령을 실행할 때 필요합니다. jar는 공용 URL을 통해 액세스하게 할 수도 있고, 컨테이너 이미지 안에 사전 패키지로 제공할 수도 있습니다. 이 예제에서는 Pi 값을 계산하는 샘플 jar을 만듭니다. 그런 후 이 jar 파일을 Azure 저장소에 업로드합니다. 기존 jar 파일이 있는 경우 자유롭게 바꾸셔도 좋습니다.

Spark 작업에 대한 프로젝트를 만들 디렉터리를 만듭니다.

```bash
mkdir myprojects
cd myprojects
```

템플릿에서 새 Scala 프로젝트를 만듭니다.

```bash
sbt new sbt/scala-seed.g8
```

프로젝트 이름을 입력하라는 메시지가 표시되면 `SparkPi`를 입력합니다.

```bash
name [Scala Seed Project]: SparkPi
```

새로 만든 프로젝트 디렉터리로 이동합니다.

```bash
cd sparkpi
```

다음 명령을 실행하여 SBT 플러그 인을 추가하면 프로젝트를 jar 파일로 패키징할 수 있습니다.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.6")' >> project/assembly.sbt
```

다음 명령을 실행하여 샘플 코드를 새로 만든 프로젝트에 복사하고 필요한 모든 종속성을 추가합니다.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11",/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

프로젝트를 jar 파일에 패키징하려면 다음 명령을 실행합니다.

```bash
sbt assembly
```

패키징을 마치면 다음과 유사한 출력이 표시됩니다.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>저장소에 작업 복사

jar 파일을 보관할 Azure 저장소 계정 및 컨테이너를 만듭니다.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location chinaeast2
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

다음 명령을 사용하여 jar 파일을 Azure 저장소 계정에 업로드합니다.

```bash
CONTAINER_NAME=jars
BLOB_NAME=SparkPi-assembly-0.1.0-SNAPSHOT.jar
FILE_TO_UPLOAD=target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar

echo "Creating the container..."
az storage container create --name $CONTAINER_NAME
az storage container set-permission --name $CONTAINER_NAME --public-access blob

echo "Uploading the file..."
az storage blob upload --container-name $CONTAINER_NAME --file $FILE_TO_UPLOAD --name $BLOB_NAME

jarUrl=$(az storage blob url --container-name $CONTAINER_NAME --name $BLOB_NAME | tr -d '"')
```

이제 `jarUrl` 변수는 jar 파일에 공개적으로 액세스할 수 있는 경로를 포함하고 있습니다.

## <a name="submit-a-spark-job"></a>Spark 작업 제출

다음 코드를 사용하여 별도의 명령줄에서 kube-proxy를 시작합니다.

```bash
kubectl proxy
```

Spark 리포지토리의 루트로 돌아갑니다.

```bash
cd $sparkdir
```

`spark-submit` 명령을 사용하여 작업을 제출합니다.

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

이 연산은 Spark 작업을 시작하고, 작업 상태를 셸 세션에 스트리밍합니다. 작업이 실행되는 동안 kubectl get pods 명령을 사용하여 Spark 드라이버 Pod 및 실행기 Pod를 볼 수 있습니다. 두 번째 터미널 세션을 열고 다음 명령을 실행합니다.

```console
$ kubectl get pods

NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

작업이 실행되는 동안 Spark UI에 액세스할 수도 있습니다. 두 번째 터미널 세션에서 `kubectl port-forward` 명령을 사용하여 Spark UI에 대한 액세스를 제공합니다.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Spark UI에 액세스하려면 브라우저에서 `127.0.0.1:4040` 주소를 엽니다.

![Spark UI](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>작업 결과 및 로그 가져오기

작업이 완료되면 드라이버 Pod는 "완료됨" 상태가 됩니다. 다음 명령을 사용하여 Pod의 이름을 가져옵니다.

```bash
kubectl get pods --show-all
```

출력

```bash
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

`kubectl logs` 명령을 사용하여 spark 드라이버 Pod에서 로그를 가져옵니다. Pod 이름을 드라이버 Pod의 이름으로 바꿉니다.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

이 로그에서 Pi의 값이 되는 Spark 작업의 결과를 볼 수 있습니다.

```bash
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>컨테이너 이미지를 사용하여 jar 패키징

위의 예제에서는 Spark jar 파일을 Azure 저장소에 업로드했습니다. 또 다른 옵션은 jar 파일을 사용자 지정된 Docker 이미지로 패키징하는 것입니다.

이렇게 하려면 `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` 디렉터리에서 Spark 이미지에 대한 `dockerfile`을 찾습니다. `WORKDIR` 및 `ENTRYPOINT` 선언 사이에 Spark 작업 `jar`에 대한 `ADD` 명령문을 추가합니다.

jar 경로를 개발 시스템의 `SparkPi-assembly-0.1.0-SNAPSHOT.jar` 파일 위치로 업데이트합니다. 개발자 고유의 사용자 지정 jar 파일을 사용해도 됩니다.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

포함된 Spark 스크립트를 사용하여 이미지를 빌드하고 푸시합니다.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

작업을 실행할 때 원격 jar URL을 가리키는 대신, `local://` 체계를 Docker 이미지의 jar 파일 경로에 사용할 수 있습니다.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> Spark에서 [설명서][spark-docs]: "Kubernetes 스케줄러는 현재 실험적입니다. 이후 버전에서는 구성, 컨테이너 이미지 및 진입점 동작이 변경될 수 있습니다."

## <a name="next-steps"></a>다음 단계

자세한 내용은 Spark 설명서를 참조하세요.

> [!div class="nextstepaction"]
> [Spark 설명서][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: https://docs.azure.cn/zh-cn/java/java-supported-jdk-runtime?view=azure-java-stable
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html

<!-- LINKS - internal -->
[acr-aks]: /container-registry/container-registry-auth-aks
[acr-create]: /container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: /aks/
[azure-cli]: https://docs.azure.cn/zh-cn/cli/?view=azure-cli-latest?view=azure-cli-latest
[storage-account]: /storage/common/storage-azure-cli