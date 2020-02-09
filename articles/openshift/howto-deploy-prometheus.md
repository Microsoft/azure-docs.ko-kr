---
title: Azure Red Hat OpenShift 클러스터에 독립 실행형 프로메테우스 인스턴스 배포 | Microsoft Docs
description: 응용 프로그램의 메트릭을 모니터링 하려면 Azure Red Hat OpenShift 클러스터에 프로메테우스 인스턴스를 만듭니다.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: 프로메테우스, aro, openshift, 메트릭, red hat
ms.openlocfilehash: f81a993caa31578e689fb3a90108f3cf0ca81fc2
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875130"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift 클러스터에 독립 실행형 프로메테우스 인스턴스 배포

이 문서에서는 Azure Red Hat OpenShift 클러스터에서 서비스 검색을 사용 하는 독립 실행형 프로메테우스 인스턴스를 구성 하는 방법을 설명 합니다.

> [!NOTE]
> Azure Red Hat OpenShift 클러스터에 대 한 고객 관리 액세스는 필요 하지 않습니다.

대상 설정:

- 프로메테우스 및 Alertmanager를 포함 하는 하나의 프로젝트 (프로메테우스 프로젝트)
- 모니터링할 응용 프로그램을 포함 하는 두 개의 프로젝트 (project1 및 project2)

일부 프로메테우스 구성 파일을 로컬로 준비 합니다. 새 폴더를 만들어 저장 합니다. 암호 토큰은 나중에 클러스터에 추가 되는 경우 구성 파일은 클러스터에 비밀으로 저장 됩니다.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>OC 도구를 사용 하 여 클러스터에 로그인

1. 웹 브라우저를 열고 클러스터 https://openshift 의 웹 콘솔로 이동 합니다. *임의 id*입니다.azmosa.io).
2. Azure 자격 증명으로 로그인합니다.
3. 오른쪽 위 모서리에서 사용자 이름을 선택 하 고 **로그인 명령 복사**를 선택 합니다.
4. 사용할 터미널에 사용자 이름을 붙여 넣습니다.

> [!NOTE]
> 올바른 클러스터에 로그인 했는지 확인 하려면 `oc whoami -c` 명령을 실행 합니다.

## <a name="prepare-the-projects"></a>프로젝트 준비

프로젝트를 만들려면 다음 명령을 실행 합니다.
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> `-n` `oc project` 또는 매개변수를사용하거나명령을실행하여활성프로젝트를선택할수있습니다.`--namespace`

## <a name="prepare-the-prometheus-configuration-file"></a>프로메테우스 구성 파일 준비
다음 콘텐츠를 입력 하 여 프로메테우스. yml 파일을 만듭니다.
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
다음 구성을 입력 하 여 Prom 이라는 비밀을 만듭니다.
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

프로메테우스. yml 파일은 기본 프로메테우스 구성 파일입니다. 간격을 설정 하 고 자동 검색을 세 가지 프로젝트 (project1, project2)로 구성 합니다. 이전 구성 파일에서 자동 검색 된 엔드포인트은 인증 없이 HTTP를 통해 스크랩 됩니다.

스크랩 엔드포인트에 대 한 자세한 내용은 [프로메테우스 scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config)를 참조 하세요.


## <a name="prepare-the-alertmanager-config-file"></a>Alertmanager 구성 파일 준비
다음 콘텐츠를 입력 하 여 alertmanager .yml 파일을 만듭니다.
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
다음 구성을 입력 하 여 Prom 경고 라는 비밀을 만듭니다.
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager .yml은 경고 관리자 구성 파일입니다.

> [!NOTE]
> 위의 두 단계를 확인 하려면 `oc get secret -n prometheus-project` 명령을 실행 합니다.

## <a name="start-prometheus-and-alertmanager"></a>프로메테우스 및 Alertmanager 시작
[Openshift/원본 리포지토리로](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) 이동 하 여 [프로메테우스-독립형 .yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) 템플릿을 다운로드 합니다. 다음 구성을 입력 하 여 해당 템플릿을 프로메테우스 프로젝트에 적용 합니다.
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
프로메테우스-독립형 .yaml 파일은 OpenShift 템플릿입니다. 이 도구는 그 앞에 oauth 프록시를 사용 하 여 프로메테우스 인스턴스를 만들며,이 인스턴스는 oauth 프록시를 사용 하 여 보안이 유지 됩니다. 이 템플릿에서 oauth-proxy는 프로메테우스 프로젝트 네임 스페이스를 "가져올" 수 있는 모든 사용자를 허용 하도록 구성 됩니다 ( `-openshift-sar` 플래그 참조).

> [!NOTE]
> Prom StatefulSet의 DESIRED 및 CURRENT number 복제본이 같은지 확인 하려면 `oc get statefulset -n prometheus-project` 명령을 실행 합니다. 프로젝트의 모든 리소스를 확인 하려면 `oc get all -n prometheus-project` 명령을 실행 합니다.

## <a name="add-permissions-to-allow-service-discovery"></a>서비스 검색을 허용 하는 권한 추가

다음 콘텐츠를 입력 하 여 prometheus-sdrole 파일을 만듭니다.
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
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
서비스 검색을 허용 하려는 모든 프로젝트에 템플릿을 적용 하려면 다음 명령을 실행 합니다.
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Role 및 rolebinding이 올바르게 만들어졌는지 확인 하려면 `oc get role` 및 `oc get rolebinding` 명령을 실행 합니다.

## <a name="optional-deploy-example-application"></a>선택 사항: 예제 응용 프로그램 배포

모든 것이 작동 중이지만 메트릭 소스가 없습니다. Prometheus URL로 이동 (https://prom-prometheus-project.apps.*임의 id*.*지역*.azmosa.io/). 다음 명령을 사용 하 여 찾을 수 있습니다.

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> 호스트 이름 앞에 https://접두사를 추가 해야 합니다.

**상태 > 서비스 검색** 페이지에 0/0 활성 대상이 표시 됩니다.

/Whe 엔드포인트에서 기본 Python 메트릭을 노출 하는 예제 응용 프로그램을 배포 하려면 다음 명령을 실행 합니다.
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
새 응용 프로그램은 배포 후 30 초 내에 서비스 검색 페이지에 유효한 대상으로 나타나야 합니다.

자세한 내용은 **상태** > **대상**을 선택 합니다.

> [!NOTE]
> 모든 성공적인 스크랩 대상에 대해 프로메테우스는 위쪽 메트릭에 데이터 요소를 추가 합니다. 왼쪽 위 모서리에서 **프로메테우스** 를 선택 하 고 식 으로를 입력 한 다음 **실행**을 선택 합니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 프로메테우스 계측을 응용 프로그램에 추가할 수 있습니다. 프로메테우스 메트릭 준비를 간소화 하는 프로메테우스 클라이언트 라이브러리는 다양 한 프로그래밍 언어에 대해 준비 됩니다.

자세한 내용은 다음 GitHub 라이브러리를 참조 하십시오.

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
