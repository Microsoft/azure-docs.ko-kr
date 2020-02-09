---
title: 컨테이너에 대 한 Azure Monitor를 사용 하 여 Azure Red Hat OpenShift 클러스터 구성 | Microsoft Docs
description: 이 문서에서는 Azure Red Hat OpenShift에서 호스트 되 Azure Monitor를 사용 하 여 Kubernetes 클러스터의 모니터링을 구성 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 0d5ed362d6eb76e2fa04b88e9e45c890118a53eb
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979782"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor를 사용 하 여 Azure Red Hat OpenShift 클러스터 구성

컨테이너에 대 한 Azure Monitor는 AKS (Azure Kubernetes Service) 및 AKS 엔진 클러스터에 대 한 풍부한 모니터링 환경을 제공 합니다. 이 문서에서는 [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 에서 호스트 되는 Kubernetes 클러스터의 모니터링을 사용 하도록 설정 하 여 비슷한 모니터링 환경을 구현 하는 방법을 설명 합니다.

>[!NOTE]
>Azure Red Hat OpenShift에 대 한 지원은 현재 공개 미리 보기의 기능입니다.
>

컨테이너에 대 한 Azure Monitor는 다음과 같은 지원 되는 메서드를 사용 하 여 새로운 또는 하나 이상의 기존 Azure Red Hat OpenShift 배포를 사용 하도록 설정할 수 있습니다.

- Azure Portal 또는 Azure Resource Manager 템플릿을 사용 하 여 기존 클러스터의 경우
- Azure Resource Manager 템플릿을 사용 하는 새 클러스터의 경우

## <a name="supported-and-unsupported-features"></a>지원 되거나 지원 되지 않는 기능

컨테이너에 대 한 Azure Monitor는 [개요](container-insights-overview.md) 문서에 설명 된 대로 Azure Red Hat openshift의 모니터링을 지원 합니다. 단, 다음 기능은 제외 됩니다.

- 라이브 데이터 (미리 보기)
- 클러스터 노드 및 pod에서 [메트릭을 수집](container-insights-update-metrics.md) 하 고 Azure Monitor 메트릭 데이터베이스에 저장 합니다.

## <a name="prerequisites"></a>필수 조건

- 컨테이너에 대 한 Azure Monitor의 기능을 사용 하도록 설정 하 고 액세스 하려면 최소한 Azure 구독에서 Azure *참가자* 역할의 멤버 여야 하 고 컨테이너에 대 한 Azure Monitor로 구성 된 Log Analytics 작업 영역의 구성원 인 [*Log Analytics 참가자*](../platform/manage-access.md#manage-access-using-azure-permissions) 역할의 구성원 이어야 합니다.

- 모니터링 데이터를 보려면 컨테이너에 대해 Azure Monitor 구성 된 Log Analytics 작업 영역에 대 한 [*Log Analytics 읽기 권한자*](../platform/manage-access.md#manage-access-using-azure-permissions) 역할 권한의 멤버입니다.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여 새 클러스터에서 사용

모니터링이 사용 하도록 설정 된 Azure Red Hat OpenShift 클러스터를 배포 하려면 다음 단계를 수행 합니다. 계속 하기 전에 [Azure Red Hat OpenShift 클러스터 만들기](../../openshift/tutorial-create-cluster.md#prerequisites) 자습서를 검토 하 여 환경이 올바르게 설정 되도록 구성 해야 하는 종속성을 이해 하세요.

이 메서드는 두 가지 JSON 템플릿을 포함합니다. 하나는 모니터링을 사용 하도록 설정 된 클러스터를 배포 하는 구성을 지정 하 고 다른 하나는 다음을 지정 하기 위해 구성 하는 매개 변수 값을 포함 합니다.

- Azure Red Hat OpenShift 클러스터 리소스 ID입니다.

- 클러스터가 배포 되는 리소스 그룹입니다.

- 이미 생성 된 테넌트를 만드는 단계를 수행한 후에 표시 된 [테넌트 ID를 Azure Active Directory](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) 합니다.

- 이미 만든 하나 이상의 단계를 만든 후 [클라이언트 응용 프로그램 ID를 Azure Active Directory](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) 합니다.

- 이미 생성 된 단계를 수행 하는 단계를 수행한 후에 [클라이언트 암호를 Azure Active Directory](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) 합니다.

- [AZURE AD 보안 그룹](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) 하나를 만드는 단계를 수행 하 고 나 서 이미 만든 그룹입니다.

- 기존 Log Analytics 작업 영역의 리소스 ID입니다.

- 클러스터에 만들 마스터 노드의 수입니다.

- 에이전트 풀 프로필의 계산 노드 수입니다.

- 에이전트 풀 프로필의 인프라 노드 수입니다.

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.

- [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/templates/deploy-powershell.md)

- [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.65 이상을 실행 해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

Azure PowerShell 또는 CLI를 사용 하 여 모니터링을 사용 하도록 설정 하기 전에 Log Analytics 작업 영역을 만들어야 합니다. 작업 영역을 만들려면 [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)나 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 또는 [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)에서 설정할 수 있습니다.

1. 다음 명령을 사용 하 여 모니터링 추가 기능을 사용 하 여 클러스터를 만들려면 Azure Resource Manager 템플릿 및 매개 변수 파일을 다운로드 하 여 로컬 폴더에 저장 합니다.

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Azure에 로그인

    ```azurecli
    az login    
    ```

    여러 구독에 액세스할 수 있으면 `az account set -s {subscription ID}`에서 `{subscription ID}`를 사용하려는 구독으로 바꿔서 실행합니다.

3. 클러스터에 대 한 리소스 그룹이 아직 없는 경우 새로 만듭니다. Azure에서 OpenShift를 지 원하는 Azure 지역 목록은 [지원 되는 지역](../../openshift/supported-resources.md#azure-regions)을 참조 하세요.

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. JSON 매개 변수 파일 **newClusterWithMonitoringParam** 를 편집 하 고 다음 값을 업데이트 합니다.

    - *location*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. 다음 단계에서는 Azure CLI를 사용 하 여 모니터링을 사용 하도록 설정 하 여 클러스터를 배포 합니다.

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    출력은 다음과 유사합니다.

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>기존 클러스터에 대해 사용

Azure에 배포 된 Azure Red Hat OpenShift 클러스터의 모니터링을 사용 하도록 설정 하려면 다음 단계를 수행 합니다. Azure Portal에서 또는 제공 된 템플릿을 사용 하 여이를 수행할 수 있습니다.

### <a name="from-the-azure-portal"></a>Azure Portal에서

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal 메뉴 또는 홈 페이지에서 **Azure Monitor**를 선택 합니다. **인사이트** 섹션에서 **컨테이너**를 선택합니다.

3. **모니터 - 컨테이너** 페이지에서 **모니터링되지 않는 클러스터**를 선택합니다.

4. 모니터링 되지 않는 클러스터 목록에서 목록에서 클러스터를 찾고 **사용**을 클릭 합니다. 열 **클러스터 유형**아래의 값 **ARO** 를 검색 하 여 목록에서 결과를 식별할 수 있습니다.

5. 클러스터와 동일한 구독에 기존 Log Analytics 작업 영역이 있는 경우 **컨테이너용 Azure Monitor에 온보딩** 페이지의 드롭다운 목록에서 해당 작업 영역을 선택합니다.  
    목록은 구독에서 클러스터가 배포 되는 기본 작업 영역 및 위치를 preselects 합니다.

    ![모니터링 되지 않는 클러스터에 대 한 모니터링 사용](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >클러스터의 모니터링 데이터를 저장하기 위해 새 Log Analytics 작업 영역을 만들려면 [Log Analytics 작업 영역 만들기](../../azure-monitor/learn/quick-create-workspace.md)를 참조하세요. RedHat OpenShift 클러스터가 배포 되는 것과 동일한 구독에서 작업 영역을 만들어야 합니다.

모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다.

### <a name="enable-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하도록 설정

이 메서드는 두 가지 JSON 템플릿을 포함합니다. 한 가지 템플릿은 모니터링을 사용하도록 구성을 지정하고, 다른 템플릿은 다음을 지정하도록 구성하는 매개 변수 값을 포함합니다.

- Azure RedHat OpenShift 클러스터 리소스 ID입니다.

- 클러스터가 배포 되는 리소스 그룹입니다.

- Log Analytics 작업 영역.

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.

- [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/templates/deploy-powershell.md)

- [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.65 이상을 실행 해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

Azure PowerShell 또는 CLI를 사용 하 여 모니터링을 사용 하도록 설정 하기 전에 Log Analytics 작업 영역을 만들어야 합니다. 작업 영역을 만들려면 [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)나 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 또는 [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)에서 설정할 수 있습니다.

1. 다음 명령을 사용 하 여 모니터링 추가 기능으로 클러스터를 업데이트 하려면 템플릿 및 매개 변수 파일을 다운로드 합니다.

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Azure에 로그인

    ```azurecli
    az login    
    ```

    여러 구독에 액세스할 수 있으면 `az account set -s {subscription ID}`에서 `{subscription ID}`를 사용하려는 구독으로 바꿔서 실행합니다.

3. Azure RedHat OpenShift 클러스터의 구독을 지정 합니다.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. 다음 명령을 실행 하 여 클러스터 위치 및 리소스 ID를 식별 합니다.

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. AraResoruceLocation JSON 매개 **변수 파일을** 편집 하 고 *Araresourceid* 및값을 업데이트 합니다. **workspaceResourceId** 값은 Log Analytics 작업 영역의 전체 리소스 ID 이며, 작업 영역 이름을 포함합니다.

6. Azure CLI를 사용 하 여 배포 하려면 다음 명령을 실행 합니다.

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    출력은 다음과 유사합니다.

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>다음 단계

- 모니터링을 사용 하 여 RedHat OpenShift 클러스터의 상태 및 리소스 사용률 및 해당 작업에서 실행 되는 작업을 수집할 수 있습니다. 컨테이너에 Azure Monitor [를 사용 하는 방법을](container-insights-analyze.md) 알아봅니다.

- 컨테이너에 대 한 Azure Monitor를 사용 하 여 클러스터 모니터링을 중지 하는 방법을 알아보려면 [Azure Red Hat OpenShift 클러스터 모니터링을 중지 하는 방법](container-insights-optout-openshift.md)을 참조 하세요.
