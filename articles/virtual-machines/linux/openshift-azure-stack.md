---
title: Azure Stack에 OpenShift 배포 | Microsoft Docs
description: Azure Stack에서 OpenShift를 배포합니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: haroldw
ms.openlocfilehash: 91b37753ae80596612eda9d3ccd34858691e35ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771552"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Azure Stack에서 OKD 또는 OpenShift Container Platform 배포

Azure Stack에서 OpenShift를 배포할 수 있습니다. Azure와 Azure Stack 간에는 몇 가지 주요 차이점이 있으므로 배포가 약간 다르고 기능도 약간 다릅니다.

현재 Azure 클라우드 공급자는 Azure Stack에서 작동하지 않습니다. 이러한 이유로 Azure Stack에서 영구 저장소를 위해 디스크 연결을 사용할 수 없습니다. 대신, NFS, iSCSI, GlusterFS 등의 다른 저장소 옵션을 구성할 수 있습니다. 또한 CNS를 사용하도록 설정하고 GlusterFS를 영구 저장소로 사용할 수도 있습니다. CNS를 사용하도록 설정하면 GlusterFS 사용량에 대한 추가 저장소와 함께 3개의 노드가 추가로 배포됩니다.

Azure Stack에서 OpenShift Container Platform 또는 OKD를 배포하는 몇 가지 방법 중 하나를 사용할 수 있습니다.

- 필요한 Azure 인프라 구성 요소를 수동으로 배포한 다음 [OpenShift Container Platform 설명서](https://docs.openshift.com/container-platform) 또는 [OKD 설명서](https://docs.okd.io)를 따르는 방법이 있습니다.
- OpenShift Container Platform 클러스터의 배포를 간소화하는 기존 [Resource Manager 템플릿](https://github.com/Microsoft/openshift-container-platform/)을 사용할 수도 있습니다.
- 또한 기존 [Resource Manager 템플릿](https://github.com/Microsoft/openshift-origin)을 사용하여 OKD 클러스터의 배포를 간소화할 수도 있습니다.

Resource Manager 템플릿을 사용하는 경우 적절한 분기(azurestack-release-3.x)를 선택합니다. Azure용 템플릿은 API 버전이 Azure 및 Azure Stack 간에 서로 다르기 때문에 작동하지 않습니다. RHEL 이미지 참조는 현재 azuredeploy.json 파일에 하드 코딩되어 있으므로 이미지에 맞게 변경해야 합니다.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

모든 옵션에 Red Hat 구독이 필요합니다. 배포하는 동안 Red Hat Enterprise Linux 인스턴스는 Red Hat 구독에 등록되고 OpenShift Container Platform에 대한 자격이 포함된 풀 ID에 연결됩니다.
유효한 RHSM(Red Hat Subscription Manager) 사용자 이름, 암호 및 풀 ID가 있어야 합니다. 또는 정품 인증 키, 조직 ID 및 풀 ID를 사용할 수 있습니다.  이 정보는 https://access.redhat.com에 로그인하여 확인할 수 있습니다.

## <a name="azure-stack-prerequisites"></a>Azure Stack의 필수 구성 요소

OpenShift 클러스터를 배포하려면 RHEL 이미지(OpenShift Container Platform) 또는 CentOS 이미지(OKD)를 Azure Stack 환경에 추가해야 합니다. 이러한 이미지를 추가하려면 Azure Stack 관리자에게 문의하세요. 지침은 다음에서 찾을 수 있습니다.

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>OpenShift Container Platform 또는 OKD Resource Manager 템플릿을 사용하여 배포

Resource Manager 템플릿을 사용하여 배포하려면 매개 변수 파일을 사용하여 입력 매개 변수를 제공합니다. 배포를 추가로 사용자 지정하려면 GitHub 리포지토리를 포크하고 적절한 항목을 변경합니다.

일반적인 사용자 지정 옵션에는 다음이 포함됩니다(이에 국한되지 않음).

- 요새 VM 크기(azuredeploy.json의 변수)
- 명명 규칙(azuredeploy.json의 변수)
- OpenShift 클러스터 세부 정보, 호스트 파일을 통해 수정 됨(deployOpenShift.sh)
- RHEL 이미지 참조(azuredeploy.json의 변수)

Azure CLI를 사용하는 배포 단계에 대해서는 [OpenShift Container Platform](./openshift-container-platform.md) 섹션 또는 [OKD](./openshift-okd.md) 섹션 중에서 해당하는 섹션을 따르세요.

## <a name="next-steps"></a>다음 단계

- [배포 후 작업](./openshift-post-deployment.md)
- [Azure에서 OpenShift 배포 문제 해결](./openshift-troubleshooting.md)