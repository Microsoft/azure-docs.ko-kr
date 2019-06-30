---
title: Azure Red Hat OpenShift 클러스터에서 독립 실행형 Prometheus 배포 | Microsoft Docs
description: 응용 프로그램의 메트릭을 모니터링 하는 Azure의 Red Hat OpenShift 클러스터에서 Prometheus 인스턴스를 만들려고 하는 방법은 다음과 같습니다.
author: Makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus aro openshift, 메트릭, red hat
ms.openlocfilehash: e66658151361edd43f61d398274c88c047928028
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342636"
---
# <a name="deploy-a-standalone-prometheus-in-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift 클러스터에서 독립 실행형 Prometheus 배포

이 가이드에서는 Azure의 Red Hat OpenShift 클러스터에서 서비스 검색을 사용 하 여 독립 실행형 Prometheus를 구성 하는 방법을 설명 합니다.  클러스터에 대 한 "고객 관리자" 필요 하지 않습니다.

대상 설치는 다음과 같습니다.

- Prometheus 및 Alertmanager를 포함 하는 한 프로젝트 (prometheus-프로젝트)
- 응용 프로그램 모니터링을 포함 하는 두 개의 프로젝트 project1 앱과 앱 project2

일부 Prometheus 구성 파일을 로컬로 준비할 수 있습니다. 저장할 새 폴더를 만듭니다.
비밀 토큰은 나중에 추가 되는 경우 이러한 구성 파일을 비밀로 클러스터에 저장 수 됩니다.

## <a name="step-1-sign-in-to-the-cluster-using-the-oc-tool"></a>1단계: 사용 하 여 클러스터에 로그인 합니다 `oc` 도구
웹 브라우저를 사용 하 여 클러스터의 웹 콘솔에 이동 (https://openshift. *임의 id*. *지역*. azmosa.io).
Azure 자격 증명으로 로그인합니다.
오른쪽 상단에서 사용자 이름을 클릭 하 고 "로그인 명령 복사"를 선택 합니다. 사용 하 여 터미널에 붙여 넣습니다.

사용 하 여 올바른 클러스터에 로그인 하는 경우를 확인할 수 있습니다는 `oc whoami -c` 명령입니다.

## <a name="step-2-prepare-the-projects"></a>2단계: 프로젝트를 준비 합니다.

프로젝트를 만듭니다.
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> 사용할 수 있습니다는 `-n` 나 `--namespace` 매개 변수 또는 활성 프로젝트를 선택 합니다 `oc project` 명령

## <a name="step-3-prepare-prometheus-config"></a>3단계: Prometheus 구성 준비
다음과 같은 콘텐츠로 prometheus.yml 라는 파일을 만듭니다.
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
구성 사용 하 여 "prom" 라는 비밀을 만듭니다.
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

위에 나열 된 파일이 기본 Prometheus 구성 파일입니다.
간격을 설정 하 고 세 개의 프로젝트 (prometheus 프로젝트, 앱 project1 project2 앱)에서 자동 검색을 구성 합니다.
이 예제에서는 자동 인증 없이 HTTP를 통해 끝점을 가져오고 수를 검색 합니다.
끝점을 스크랩 하는 방법은 참조 하세요. https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config 합니다.


## <a name="step-4-prepare-alertmanager-config"></a>4단계: Alertmanager 구성 준비
다음과 같은 콘텐츠로 alertmanager.yml 라는 파일을 만듭니다.
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
"Prom 경고" 구성 사용 하 여 명명 된 암호를 만듭니다.
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

위에 나열 된 파일은 경고 관리자 구성 파일.

> [!NOTE]
> 사용 하 여 이전 두 단계를 확인할 수 있습니다. `oc get secret -n prometheus-project`

## <a name="step-5-start-prometheus-and-alertmanager"></a>5단계: Prometheus 및 Alertmanager 시작
다운로드 합니다 [prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) 템플릿에서 합니다 [openshift/원본 리포지토리로](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) prometheus-프로젝트에 적용
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Prometheus standalone.yaml 파일이 고 oauth 프록시를 사용 하 여 보호도 Alertmanager 인스턴스를 앞에 oauth 프록시를 사용 하 여 Prometheus 인스턴스를 만드는 OpenShift 템플릿.  이 템플릿에서 oauth 프록시는 모든 사용자가 있습니다 "get" "prometheus 프로젝트" 네임 스페이스를 허용 하도록 구성 되어 (참조는 `-openshift-sar` 플래그).

> [!NOTE]
> "Prom" StatefulSet 같은 있는지 확인할 수 있습니다 *필요한* 하 고 *현재* 복제본 수는 `oc get statefulset -n prometheus-project` 명령입니다.
> 사용 하 여 프로젝트의 모든 리소스를 확인할 수도 있습니다 `oc get all -n prometheus-project`합니다.

## <a name="step-6-add-permissions-to-allow-service-discovery"></a>6단계: 서비스 검색을 허용 하도록 사용 권한을 추가 합니다.
다음과 같은 콘텐츠로 prometheus sdrole.yml를 만듭니다.
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus Service Discovery Role
    description: |
      A role and rolebinding adding permissions required to perform Service Discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
서비스 검색을 허용 하려는 모든 프로젝트에 템플릿을 적용 합니다.
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Prometheus 자체에서 메트릭을 수집할 수 있게 되기를 원할 경우 너무 prometheus 프로젝트에서 사용 권한을 적용 해야 합니다.

> [!NOTE]
> 역할 및 RoleBinding를 사용 하 여 올바르게 생성 된 경우를 확인할 수 있습니다 합니다 `oc get role` 고 `oc get rolebinding` 각각 명령

## <a name="optional-deploy-example-application"></a>선택 사항: 예제 응용 프로그램 배포
모든 것이 작동 하지만 메트릭 원본이 없습니다. Prometheus URL로 이동 (https://prom-prometheus-project.apps. *임의 id*. *지역*.azmosa.io/), 다음 명령을 사용 하 여 찾을 수 있습니다.
```
oc get route prom -n prometheus-project
```
Https:// 사용 하 여 호스트 이름을 접두사로 사용 해야 합니다.

합니다 **상태 > 서비스 검색** 0/0 active 대상 페이지에 표시 됩니다.

/Metrics 끝점에서 노출 기본 python 메트릭을 예제 응용 프로그램을 배포 하려면 다음 명령을 실행 합니다.
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
새 응용 프로그램 배포 후 30 초 내에서 서비스 검색 페이지에서 유효한 대상으로 표시 됩니다. 자세한 세부 정보를 얻을 수 있습니다 합니다 **상태 > 대상** 페이지입니다.

> [!NOTE]
> 성공적으로 직접 달았습니다 모든 대상에 대 한 Prometheus에서 "최대" 메트릭 데이터 포인트를 추가합니다. 클릭 **Prometheus** 왼쪽 위에서 모서리 및 식 및 클릭 "up" 입력 **Execute**합니다.

## <a name="next-steps"></a>다음 단계

Prometheus는 사용자 지정 계측 응용 프로그램에 추가할 수 있습니다.

준비 Prometheus 메트릭 간소화, Prometheus 클라이언트 라이브러리를 다양 한 프로그래밍 언어 준비가 되었습니다.

 - Java https://github.com/prometheus/client_java
 - Python https://github.com/prometheus/client_python
 - 이동 https://github.com/prometheus/client_golang
 - Ruby https://github.com/prometheus/client_ruby
