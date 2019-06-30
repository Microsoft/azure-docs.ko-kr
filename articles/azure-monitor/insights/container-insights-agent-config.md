---
title: Azure Monitor 컨테이너 에이전트 데이터 수집 구성 | Microsoft Docs
description: 이 문서에서는 컨테이너 에이전트가 stdout/stderr을 제어 하는 데 Azure Monitor를 구성할 수 있습니다 및 환경 변수 컬렉션을 로그 하는 방법을 설명 합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2019
ms.author: magoedte
ms.openlocfilehash: 1e7506e311c38d87371dd1b65440b6fb41a7ce78
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341656"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor에 대 한 데이터 수집 에이전트 구성

컨테이너에 대 한 azure Monitor 관리 되는 컨테이너 화 된 에이전트에서 Azure Kubernetes Service (AKS)에서 호스트 된 Kubernetes 클러스터에 배포 하는 컨테이너 워크 로드에서 stdout, stderr 및 환경 변수를 수집 합니다. 사용자 지정이이 환경을 제어 하기 위해 Kubernetes ConfigMaps 만들어 에이전트 데이터 수집 설정을 구성할 수 있습니다. 이 문서에서는 ConfigMap을 만들고 요구 사항에 따라 데이터 컬렉션을 구성 하는 방법을 보여 줍니다.

## <a name="configure-your-cluster-with-custom-data-collection-settings"></a>사용자 지정 데이터 수집 설정을 사용 하 여 클러스터 구성

템플릿 ConfigMap 파일에는 처음부터 새로 만들 필요 없이 사용자 지정을 사용 하 여 쉽게 편집할 수 있도록 제공 됩니다. 를 시작 하기 전에 대 한 Kubernetes 설명서를 검토 해야 [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) 만들기, 구성 및 ConfigMaps를 배포 하는 방법을 잘 알고 있어야 합니다. 이렇게 하면 네임 스페이스 당 또는 전체 클러스터 및 클러스터의 모든 pod/노드에 실행 중인 모든 컨테이너에 대 한 환경 변수에서 stdout 및 stderr을 필터링 할 수 있습니다.

>[!IMPORTANT]
>이 기능을 지 원하는 최소 에이전트 버전은 microsoft / oms:ciprod06142019 이상. 

### <a name="overview-of-configurable-data-collection-settings"></a>구성 가능한 데이터 컬렉션 설정 개요

데이터 수집을 제어 하도록 구성할 수 있는 설정은 다음과 같습니다.

|키 |데이터 형식 |값 |설명 |
|----|----------|------|------------|
|`schema-version` |문자열 (대/소문자 구분) |v1 |이 ConfigMap을 구문 분석할 때 에이전트가 사용 하는 스키마 버전입니다. 현재 지원 되는 스키마 버전 v1 되었습니다. 이 값을 수정 하는 지원 되지 않습니다 및 ConfigMap을 계산 하는 경우 거부 됩니다.|
|`config-version` |String | | 소스 제어 시스템/리포지토리에서이 구성 파일의이 버전을 추적 하는 기능을 지원 합니다. 허용 되는 최대 문자 10 되며 다른 모든 문자는 잘립니다. |
|`[log_collection_settings.stdout] enabled =` |Boolean | true 또는 false | Stdout 컨테이너 로그 수집을 사용 하는 경우를 제어 합니다. 로 설정 하면 `true` stdout 로그 컬렉션에 대 한 네임 스페이스 없음 제외 됩니다 (`log_collection_settings.stdout.exclude_namespaces` 아래에 있는 설정), 노드에서 모든 pod/클러스터의 모든 컨테이너에서 stdout 로그를 수집할 수 됩니다. 기본값은 ConfigMaps를 지정 하지 않으면 `enabled = true`합니다. |
|`[log_collection_settings.stdout] exclude_namespaces =`|문자열 | 쉼표로 구분 된 배열 |stdout 로그 수집 되지 것입니다. Kubernetes 네임 스페이스의 배열입니다. 이 설정은 유효 경우에만 `log_collection_settings.stdout.enabled` 로 설정 된 `true`합니다. ConfigMap를 지정 하지 않으면 기본값은 `exclude_namespaces = ["kube-system"]`합니다.|
|`[log_collection_settings.stderr] enabled =` |Boolean | true 또는 false |Stderr 컨테이너 로그 수집을 사용 하는 경우를 제어 합니다. 로 설정 하면 `true` stdout 로그 컬렉션에 대 한 네임 스페이스 없음 제외 됩니다 (`log_collection_settings.stderr.exclude_namespaces` 설정)를 stderr 로그 모든 컨테이너에서 클러스터의 모든 pod/노드에 수집 됩니다. 기본값은 ConfigMaps를 지정 하지 않으면 `enabled = true`합니다. |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |쉼표로 구분 된 배열 |stderr에 대 한 로그 수집 되지 것입니다. Kubernetes 네임 스페이스의 배열입니다. 이 설정은 유효 경우에만 `log_collection_settings.stdout.enabled` 로 설정 된 `true`합니다. ConfigMap를 지정 하지 않으면 기본값은 `exclude_namespaces = ["kube-system"]`합니다. |
| `[log_collection_settings.env_var] enabled =` |Boolean | true 또는 false | 환경 변수 컬렉션을 사용 하는 경우를 제어 합니다. 로 설정 하면 `false`, 환경 변수는 클러스터의 모든 pod/노드 간에 실행 중인 모든 컨테이너에 대 한 수집 됩니다. ConfigMap를 지정 하지 않으면 기본값은 `enabled = true`합니다. |

### <a name="configure-and-deploy-configmaps"></a>구성 및 ConfigMaps 배포

구성 하 고 ConfigMap 구성 파일을 클러스터에 배포 하려면 다음 단계를 수행 합니다.

1. [다운로드](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) 템플릿 ConfigMap yaml 파일을 컨테이너 azm-ms agentconfig.yaml로 저장 합니다.  
1. 사용자 지정을 사용 하 여 ConfigMap yaml 파일을 편집 합니다. 

    - 다음 예제를 사용 하 여 키/값 stdout 로그 컬렉션에 대 한 특정 네임 스페이스를 제외 하려면 구성한: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`합니다.
    - 특정 컨테이너에 대 한 환경 변수 컬렉션을 사용 하지 않으려면 키/값을 설정 `[log_collection_settings.env_var] enabled = true` 변수 컬렉션을 전역으로 사용 하 여 다음 단계를 따릅니다 [여기](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) 특정 컨테이너에 대 한 구성을 완료 하려면.
    - 다음 예제를 사용 하 여 키/값을 구성한 클러스터 전체의 stderr 로그 수집을 사용 하지 않으려면: `[log_collection_settings.stderr] enabled = false`합니다.

1. Kubectl 명령을 실행 하 여 ConfigMap을 만듭니다: `kubectl apply -f <configmap_yaml_file.yaml>`합니다.
    
    예: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    구성 변경이 적용을 완료 하려면 몇 분 정도 걸릴 수 있습니다 하 고 클러스터의 모든 omsagent pod를 다시 시작 합니다. 다시 시작 되는 모든 omsagent pod에 대 한 롤링 다시 시작, 모든 동시에 다시 시작 합니다. 메시지가 다음과 유사한 결과 포함 하는 표시를 다시 시작이 완료 되 면: `configmap "container-azm-ms-agentconfig" created`합니다.

구성을 성공적으로 적용을 확인 하려면 에이전트 포드를에서 로그를 검토 하려면 다음 명령을 사용 합니다. `kubectl logs omsagent-fdf58 -n=kube-system`합니다. Osmagent pod에서 구성 오류가 있는 경우 출력 오류가 표시 됩니다는 다음과 유사 합니다.

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

다시 시작 하 고 기본 구성을 사용 하 여 파일을 구문 분석에서 omsagent를 방지 하는 오류입니다. ConfigMap에서 오류를 수정한 후 yaml 파일을 저장 하 고 명령을 실행 하 여 업데이트 된 ConfigMaps 적용: `kubectl apply -f <configmap_yaml_file.yaml`합니다.

## <a name="applying-updated-configmap"></a>ConfigMap 업데이트 적용

이전에 사용한 하 고 이전과 동일한 명령을 사용 하 여 적용 ConfigMap 파일을 간단히 편집할 수 이미 배포한 경우 ConfigMap을 클러스터에 대 한 최신 구성을 사용 하 여 업데이트 하려는 경우 `kubectl apply -f <configmap_yaml_file.yaml`합니다.

구성 변경이 적용을 완료 하려면 몇 분 정도 걸릴 수 있습니다 하 고 클러스터의 모든 omsagent pod를 다시 시작 합니다. 다시 시작 되는 모든 omsagent pod에 대 한 롤링 다시 시작, 모든 동시에 다시 시작 합니다. 메시지가 다음과 유사한 결과 포함 하는 표시를 다시 시작이 완료 되 면: `configmap "container-azm-ms-agentconfig" updated`합니다.

## <a name="verifying-schema-version"></a>스키마 버전 확인

지원 되는 구성 스키마 버전 omsagent pod에 pod 주석 (스키마 버전)으로 사용할 수 있습니다. Kubectl 명령을 사용 하 여 볼 수 있습니다. `kubectl describe pod omsagent-fdf58 -n=kube-system`

출력 주석 스키마 버전을 사용 하 여 다음과 비슷하게 표시 됩니다.

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>다음 단계

- Azure Monitor를 사용하고 AKS 클러스터의 다른 측면을 모니터링하는 방법을 계속 알아보려면 [Azure Kubernetes 서비스 상태](container-insights-analyze.md)를 참조하세요.
- 뷰 [쿼리 예제 로그](container-insights-log-search.md#search-logs-to-analyze-data) 에 미리 정의 된 쿼리 및 평가 하거나 경고를 시각화, 분석 하 여 클러스터에 대 한 사용자 지정 하는 예제를 참조 하세요.