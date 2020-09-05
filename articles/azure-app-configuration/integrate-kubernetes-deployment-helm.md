---
title: Helm을 사용하여 Kubernetes 배포와 Azure App Configuration 통합
description: Helm을 사용하여 Kubernetes 배포에서 동적 구성을 사용하는 방법을 알아봅니다.
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: aac42e6f782ac1e939ff955c5811238f99e703eb
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "83725672"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>Helm을 사용하여 Kubernetes 배포와 통합

Helm은 Kubernetes에서 실행되는 애플리케이션을 정의, 설치 및 업그레이드할 수 있는 방법을 제공합니다. Helm 차트에는 Kubernetes 애플리케이션의 인스턴스를 만드는 데 필요한 정보가 포함되어 있습니다. 구성은 차트 자체 외부의 *values.yaml*이라는 파일에 저장됩니다. 

릴리스 프로세스 중에 Helm은 애플리케이션을 실행하기 위해 차트를 적절한 구성과 병합합니다. 예를 들어 *values.yaml*에 정의된 변수는 실행 중인 컨테이너 내에서 환경 변수로 참조할 수 있습니다. 또한 Helm은 데이터 볼륨으로 탑재되거나 환경 변수로 공개될 수 있는 Kubernetes 비밀 만들기를 지원합니다.

Helm을 실행할 때 추가 YAML 기반 구성 파일을 명령줄에 제공하여 *values.yaml*에 저장된 값을 재정의할 수 있습니다. Azure App Configuration은 구성 값을 YAML 파일로 내보내기를 지원합니다. 이 내보내기 기능을 배포에 통합하면 Kubernetes 애플리케이션에서 App Configuration에 저장된 구성 값을 활용할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * Helm을 사용하여 애플리케이션을 Kubernetes에 배포할 때 App Configuration의 값을 사용합니다.
> * App Configuration에서 Key Vault 참조를 기반으로 하는 Kubernetes 비밀을 만듭니다.

이 자습서에서는 Helm을 사용하여 Kubernetes를 관리하는 방법에 대한 기본적인 이해를 가정합니다. [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/kubernetes-helm)에서 Helm을 사용하여 애플리케이션을 설치하는 방법에 대해 자세히 알아보세요.

## <a name="prerequisites"></a>사전 요구 사항

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)(버전 2.4.0 이상) 설치
- [Helm](https://helm.sh/docs/intro/install/)(버전 2.14.0 이상) 설치
- Kubernetes 클러스터

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. **구성 탐색기** > **만들기**를 선택하여 다음 키-값 쌍을 추가합니다.

    | 키 | 값 |
    |---|---|
    | settings.color | 흰색 |
    | settings.message | Azure App Configuration의 정보 |

    지금은 **레이블**과 **콘텐츠 형식**을 비워 두세요.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>App Configuration에 Key Vault 참조 추가
1. [Azure Portal](https://portal.azure.com)에 로그인하고, 이름이 **Password**이고 값이 **myPassword**인 비밀을 [Key Vault](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)에 추가합니다. 
2. 이전 섹션에서 만든 App Configuration 저장소 인스턴스를 선택합니다.

3. **구성 탐색기**를 선택합니다.

4. **+ 만들기** > **Key Vault 참조**를 선택하고 다음 값을 지정합니다.
    - **키**: **secrets.password**를 선택합니다.
    - **레이블**: 이 값은 빈 상태로 둡니다.
    - **구독**, **리소스 그룹** 및 **Key Vault**: 이전 단계에서 만든 키 자격 증명 모음의 값에 해당하는 값을 입력합니다.
    - **비밀**: 이전 섹션에서 만든 **Password**라는 비밀을 선택합니다.

## <a name="create-helm-chart"></a>Helm 차트 만들기 ##
먼저, 다음 명령을 사용하여 Helm 차트 샘플을 만듭니다.
```console
helm create mychart
```

Helm은 아래와 같은 구조를 사용하여 mychart라는 새 디렉터리를 만듭니다. 

> [!TIP]
> 자세히 알아보려면 이 [차트 가이드](https://helm.sh/docs/chart_template_guide/getting_started/)를 따르세요.

```
mychart
|-- Chart.yaml
|-- charts
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```

다음으로, *deployment.yaml* 파일의 **spec:template:spec:containers** 섹션을 업데이트합니다. 다음 코드 조각에서는 두 개의 환경 변수를 컨테이너에 추가합니다. 그러면 배포 시 해당 값이 동적으로 설정됩니다.

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

업데이트 후의 전체 *deployment.yaml* 파일은 다음과 같습니다.

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    app.kubernetes.io/name: {{ include "mychart.name" . }}
    helm.sh/chart: {{ include "mychart.chart" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "mychart.name" . }}
      app.kubernetes.io/instance: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mychart.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            - name: Color
              value: {{ .Values.settings.color }}
            - name: Message
              value: {{ .Values.settings.message }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
```

중요한 데이터를 Kubernetes 비밀로 저장하려면 *secrets.yaml* 파일을 templates 폴더 아래에 추가합니다.

> [!TIP]
> [Kubernetes 비밀](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets)을 사용하는 방법에 대해 자세히 알아보세요.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password | b64enc }}
```

마지막으로, *deployment.yaml* 및 *secrets.yaml* 파일에서 참조한 구성 설정 및 비밀의 기본값을 선택적으로 제공하도록 *values.yaml* 파일을 다음 콘텐츠로 업데이트합니다. App Configuration에서 가져온 구성으로 실제 값을 덮어씁니다.

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>Helm 설치의 App Configuration에서 구성 전달 ##
먼저, 구성을 App Configuration에서 *myConfig.yaml* 파일로 다운로드합니다. 키 필터를 사용하여 **settings.** 로 시작하는 키만 다운로드합니다. 키 필터가 키 자격 증명 모음 참조의 키를 제외하는 데 충분하지 않은 경우 **--skip-keyvault** 인수를 사용하여 제외할 수 있습니다. 

> [!TIP]
> [export 명령](https://docs.microsoft.com/cli/azure/appconfig/kv?view=azure-cli-latest#az-appconfig-kv-export)에 대해 자세히 알아보세요. 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

다음으로, 비밀을 *mySecrets.yaml*이라는 파일로 다운로드합니다. **--resolve-keyvault** 명령줄 인수는 Key Vault에서 실제 값을 검색하여 Key Vault 참조를 확인합니다. 이 명령은 해당 Key Vault에 대한 액세스 권한이 있는 자격 증명을 사용하여 실행해야 합니다.

> [!WARNING]
> 이 파일에는 중요한 정보가 포함되어 있으므로 파일을 신중하게 유지하고 더 이상 필요하지 않은 경우 정리하세요.

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

Helm 업그레이드의 **-f** 인수를 사용하여 사용자가 만든 두 개의 구성 파일을 전달합니다. *values.yaml*에 정의된 구성 값을 App Configuration에서 내보낸 값으로 재정의합니다.

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

또한  **--set** 인수를 Helm 업그레이드에 사용하여 리터럴 키 값을 전달할 수 있습니다. 중요한 데이터를 디스크에 유지하지 않도록 하려면 **--set** 인수를 사용하는 것이 좋습니다. 

```powershell
$secrets = az appconfig kv list -n myAppConfiguration --key "secrets.*" --resolve-keyvault --query "[*].{name:key, value:value}" | ConvertFrom-Json

foreach ($secret in $secrets) {
  $keyvalues += $secret.name + "=" + $secret.value + ","
}

if ($keyvalues){
  $keyvalues = $keyvalues.TrimEnd(',')
  helm upgrade --install --set $keyvalues "example" ./mychart 
}
else{
  helm upgrade --install "example" ./mychart 
}

```

[Kubernetes 대시보드](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)에 액세스하여 구성 및 비밀이 성공적으로 설정되었는지 확인합니다. App Configuration의 **color** 및 **message** 값이 컨테이너의 환경 변수에 채워져 있음을 알 수 있습니다.

![로컬로 빠른 시작 앱 시작](./media/kubernetes-dashboard-env-variables.png)

App Configuration에서 Key Vault 참조로 저장되는 하나의 비밀인 **password**도 Kubernetes 비밀에 추가되었습니다. 

![로컬로 빠른 시작 앱 시작](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Helm을 사용하여 Kubernetes 배포에 사용할 Azure App Configuration 데이터를 내보냈습니다. App Configuration을 사용하는 방법에 대해 자세히 알아보려면 Azure CLI 샘플로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure CLI](https://docs.microsoft.com/cli/azure/appconfig?view=azure-cli-latest)
