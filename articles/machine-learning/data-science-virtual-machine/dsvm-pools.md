---
title: 공유 풀
titleSuffix: Azure Data Science Virtual Machine
description: DSVMs (데이터 과학 Virtual Machines)의 공유 풀을 만들어 팀에 대 한 공유 리소스로 배포 & 하는 방법에 대해 알아봅니다.
keywords: 딥 러닝, AI, 데이터 과학 도구, 데이터 과학 가상 머신, 지리 공간적 분석, 팀 데이터 과학 프로세스
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 46572853cf882df4d6909297dd058d5ccaf782e1
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144773"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Data Science Virtual Machines의 공유 풀 만들기

이 문서에서는 팀에 대 한 DSVMs (데이터 과학 Virtual Machines)의 공유 풀을 만드는 방법에 대해 알아봅니다. 공유 풀을 사용할 경우의 이점에는 더 나은 리소스 사용률, 더 쉬워진 공유 및 공동 작업, DSVM 리소스에 대 한 보다 효과적인 관리 등이 있습니다.

다양한 메서드 및 기술을 사용하여 DSVM의 풀을 만들 수 있습니다. 이 문서는 대화형 Vm (가상 머신)에 대 한 풀을 중심으로 설명 합니다. 대체 관리형 컴퓨팅 인프라는 Azure Machine Learning 컴퓨팅입니다. 자세한 내용은 [PYTHON SDK를 사용 하 여 계산 대상 만들기](../how-to-create-attach-compute-sdk.md)를 참조 하세요.

## <a name="interactive-vm-pool"></a>대화형 VM 풀

전체 AI/데이터 과학 팀에서 공유되는 대화형 VM의 풀을 사용하면 각 사용자 집합에 대해 전용 인스턴스를 가질 필요 없이 사용자가 DSVM의 사용 가능한 인스턴스에 로그인할 수 있습니다. 이 설정은 리소스의 가용성과 효율성을 향상 시킬 수 있습니다.

[Azure 가상 머신 확장 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) 기술을 사용 하 여 대화형 VM 풀을 만듭니다. 확장 집합을 사용하여 동일한 그룹의 부하 분산된 자동 크기 조정 VM을 만들고 관리할 수 있습니다.

사용자는 주 풀의 IP 또는 DNS 주소에 로그인합니다. 확장 집합은 확장 집합에서 사용 가능한 DSVM으로 세션을 자동으로 라우팅합니다. 사용자가 로그인 하는 VM에 관계 없이 일관 되 고 친숙 한 환경을 원합니다. 확장 집합의 모든 VM 인스턴스는 Azure Files 공유 또는 NFS (네트워크 파일 시스템) 공유와 같은 공유 네트워크 드라이브를 탑재 합니다. 사용자의 공유 작업 영역은 일반적으로 각 인스턴스에 탑재된 공유 파일 저장소에 유지됩니다.

Ubuntu DSVM 인스턴스를 사용하여 확장 집합을 만드는 샘플 Azure Resource Manager 템플릿은 [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json)에서 찾을 수 있습니다. 동일한 위치에서 Azure Resource Manager 템플릿에 대 한 [매개 변수 파일](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) 의 샘플을 찾을 수 있습니다.

Azure CLI에서 매개 변수 파일에 대 한 값을 지정 하 여 Azure Resource Manager 템플릿에서 확장 집합을 만들 수 있습니다.

```azurecli-interactive
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```

이전 명령에서는 다음 항목이 있다고 가정합니다.

* 확장 집합의 인스턴스에 지정된 값을 포함한 매개 변수 파일의 복사본
* VM 인스턴스 수
* Azure Files 공유에 대한 포인터
* 각 VM에 탑재될 스토리지 계정의 자격 증명

매개 변수 파일은 명령에서 로컬로 참조됩니다. 매개 변수를 인라인으로 전달하거나 스크립트에서 그에 대한 프롬프트를 표시할 수도 있습니다.  

이전 템플릿은 프런트 엔드 확장 집합부터 Ubuntu DSVM의 백 엔드 풀까지의 SSH 및 JupyterHub 포트를 사용할 수 있습니다. 사용자는 일반적인 방식으로 Secure Shell (SSH) 또는 JupyterHub에서 VM에 로그인 합니다. VM 인스턴스는 동적으로 확장 또는 축소할 수 있으므로 모든 상태를 탑재 된 Azure Files 공유에 저장 해야 합니다. 같은 방식을 사용하여 Windows DSVM의 풀을 만들 수 있습니다.

[Azure Files 공유를 탑재하는 스크립트](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh)는 Azure Data Science VM 리포지토리에서도 GitHub로 사용할 수 있습니다. 스크립트는 매개 변수 파일의 지정된 탑재 지점에서 Azure Files 공유를 탑재합니다. 또한 스크립트는 초기 사용자의 홈 디렉터리에서 탑재된 드라이브에 대한 소프트 링크를 만듭니다. `$HOME/notebooks/remote`사용자가 Jupyter 노트북에 액세스 하 고 실행 하 고 저장할 수 있도록 Azure Files 공유의 사용자 특정 노트북 디렉터리는 디렉터리에 소프트 링크 됩니다. VM에 추가 사용자를 만들 때 동일한 규칙을 사용하여 Azure Files 공유에 대한 각 사용자의 Jupyter 작업 영역을 가리킬 수 있습니다.

가상 머신 확장 집합은 자동 크기 조정 기능을 지원합니다. 추가 인스턴스를 만들 시기 및 인스턴스를 확장 하는 시기에 대 한 규칙을 설정할 수 있습니다. 예를 들어 VM을 전혀 사용하지 않는 경우 클라우드 하드웨어 사용 비용을 절약하기 위해 0개의 인스턴스로 축소할 수 있습니다. 가상 머신 확장 집합의 설명서 페이지는 [자동 크기 조정](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)에 대한 자세한 단계를 제공합니다.

## <a name="next-steps"></a>다음 단계

* [일반적인 ID 설정](dsvm-common-identity.md)
* [클라우드 리소스에 액세스하기 위한 자격 증명을 안전하게 저장](dsvm-secure-access-keys.md)
