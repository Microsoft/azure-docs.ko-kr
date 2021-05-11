---
title: Azure Resource Manager 템플릿 샘플
description: Azure Resource Manager 템플릿 샘플을 찾아서 다양한 구성으로 Azure Container Instances를 배포합니다.
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: 6c487087b39244178643fd81364150ceb3ac4a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169649"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure Container Instances용 Azure Resource Manager 템플릿

다음 예제 템플릿은 다양한 구성에서 컨테이너 인스턴스를 배포합니다.

배포 옵션은 [배포](#deployment) 섹션을 참조하세요. 사용자 고유의 템플릿을 만들려는 경우 Azure Container Instances [Resource Manager 템플릿 참조][ref]는 템플릿 형식 및 사용 가능한 속성을 자세히 설명합니다.

## <a name="sample-templates"></a>샘플 템플릿

| 템플릿 | Description |
|-|-|
| **애플리케이션** ||
| [WordPress][app-wp] | 컨테이너 그룹에 WordPress 웹 사이트 및 해당 MySQL 데이터베이스를 만듭니다. WordPress 사이트 콘텐츠 및 MySQL 데이터베이스는 Azure 파일 공유로 지속됩니다. 또한 애플리케이션 게이트웨이를 만들어 WordPress에 대한 공용 네트워크 액세스를 공개합니다. |
| [SQL Server와 IIS를 사용한 MS NAV][app-nav] | 전체 기능이 자체 포함된 Dynamics NAV / Dynamics 365 Business Central 환경를 사용하여 단일 Windows 컨테이너를 배포합니다. |
| **볼륨** ||
| [emptyDir][vol-emptydir] | emptyDir 볼륨을 공유하는 두 개의 Linux 컨테이너를 배포합니다. |
| [gitRepo][vol-gitrepo] | GitHub 리포지토리를 복제하고 볼륨으로 탑재하는 Linux 컨테이너를 배포합니다. |
| [암호][vol-secret] | 비밀 볼륨으로 탑재된 PFX 인증서를 사용하여 Linux 컨테이너를 배포합니다. |
| **네트워킹** ||
| [UDP-공개 컨테이너][net-udp] | UDP 포트를 공개하는 Windows 또는 Linux 컨테이너를 배포합니다. |
| [공용 IP를 사용한 Linux 컨테이너][net-publicip] | 공용 IP를 통해 액세스할 수 있는 단일 Linux 컨테이너를 배포합니다. |
| [가상 네트워크를 사용하는 컨테이너 그룹 배포][net-vnet] | 새 가상 네트워크, 서브넷, 네트워크 프로필 및 컨테이너 그룹을 배포합니다. |
| **Azure 리소스** ||
| [Azure Storage 계정 및 파일 공유 만들기][az-files] | 컨테이너 인스턴스에서 Azure CLI를 사용하여 스토리지 계정 및 Azure 파일 공유를 만듭니다.

## <a name="deployment"></a>배포

Resource Manager 템플릿을 사용하여 리소스를 배포하기 위한 몇 가지 옵션이 있습니다.

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure Portal][deploy-portal]

[REST API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[net-vnet]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
[deploy-portal]: ../azure-resource-manager/templates/deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-rest]: ../azure-resource-manager/templates/deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
