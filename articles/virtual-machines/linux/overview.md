---
title: Azure의 Linux VM 개요
description: Azure의 Linux 가상 머신 개요.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b205665a0e5fc06fdc784efa91036f26da5d3cde
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654347"
---
# <a name="linux-virtual-machines-in-azure"></a>Azure의 Linux 가상 머신

Azure Virtual Machines(VM)는 Azure에서 제공하는 여러 유형의 [확장성 있는 주문형 컴퓨팅 리소스](/azure/architecture/guide/technology-choices/compute-decision-tree) 중 하나입니다. 일반적으로 컴퓨팅 환경에서 다른 선택 옵션에서 제공하는 것보다 더 많이 제어해야 하는 경우에 VM을 선택합니다. 이 문서에서는 VM을 만들기 전에 고려해야 하는 요구 사항, 만드는 방법 및 관리하는 방법에 대해 설명합니다.

Azure VM은 가상 컴퓨터를 실행하는 실제 하드웨어를 구입 및 유지 관리하지 않고도 가상화의 유연성을 제공합니다. 하지만 가상 컴퓨터에서 실행하는 소프트웨어의 구성, 패치 및 설치와 같은 작업을 수행하여 VM을 계속 유지 관리할 필요가 있습니다.

Azure 가상 머신은 다양한 방식으로 사용할 수 있습니다. 예는 다음과 같습니다.

* **개발 및 테스트** – Azure VM은 애플리케이션의 코딩과 테스트에 필요한 특정 구성을 갖춘 컴퓨터를 만드는 쉽고 빠른 방법을 제공합니다.
* **클라우드의 애플리케이션** – 애플리케이션에 대한 수요가 변동할 수 있으므로 Azure의 VM에서 애플리케이션을 실행하는 것이 경제적입니다. 필요할 경우 여분의 VM에 대해 비용을 지불하고, 그렇지 않은 경우에는 해당 VM을 종료합니다.
* **확장된 데이터 센터** – Azure 가상 네트워크의 가상 머신은 조직의 네트워크에 쉽게 연결할 수 있습니다.

애플리케이션에서 사용하는 VM의 수는 요구 사항을 충족하는 데 필요한 만큼 늘리거나 줄일 수 있습니다.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>VM을 만들기 전의 고려 사항
Azure에서 애플리케이션 인프라를 구축하는 경우에는 언제나 다양한 [디자인 고려 사항](/azure/architecture/reference-architectures/n-tier/windows-vm)이 있습니다. VM의 이러한 양상으로 인해 시작하기 전에 다음 항목을 중요하게 고려해야 합니다.

* 애플리케이션 리소스 이름
* 리소스가 저장되어 있는 위치
* VM 크기
* 만들 수 있는 VM의 최대 수
* VM에서 실행하는 운영 체제
* 시작 이후의 VM 구성
* VM에 필요한 관련 리소스

### <a name="locations"></a>위치
Azure에서 만든 리소스는 모두 전 세계의 여러 [지리적 지역](https://azure.microsoft.com/regions/)에 걸쳐 배포됩니다. 일반적으로 VM을 만들 때 지역을 **위치**라고 합니다. VM의 경우 가상 하드 디스크가 저장되는 위치가 지정됩니다.

아래 표에서는 사용할 수 있는 위치 목록을 가져올 수 있는 몇 가지 방법을 보여 줍니다.

| 메서드 | 설명 |
| --- | --- |
| Azure portal |VM을 만들 때 목록에서 위치를 선택합니다. |
| Azure PowerShell |[Get-AzLocation](/powershell/module/az.resources/get-azlocation) 명령을 사용합니다. |
| REST API |[위치 나열](/rest/api/resources/subscriptions) 작업을 사용합니다. |
| Azure CLI |[az account list-locations](/cli/azure/account?view=azure-cli-latest) 작업을 사용합니다. |

### <a name="singapore-data-residency"></a>싱가포르 데이터 상주

Azure에서 단일 지역에 고객 데이터를 저장할 수 있는 기능은 현재 아시아 태평양 지역의 동남 아시아 지역(싱가포르)에서만 사용할 수 있습니다. 다른 모든 지역의 경우 고객 데이터는 지역에 저장됩니다. 자세한 내용은 [보안 센터](https://azuredatacentermap.azurewebsites.net/)를 참조하세요.

## <a name="availability"></a>가용성
Azure는 모든 디스크에 프리미엄 스토리지를 사용하여 VM을 배포하는 경우 업계 최고의 99.9% 단일 인스턴스 가상 머신 Service Level Agreement(서비스 수준 약정)를 발표했습니다.  배포에서 표준 99.95% VM 서비스 수준 약정을 충족하려면 가용성 집합 내부에서 워크로드를 실행하는 VM을 둘 이상 계속 배포해야 합니다. 가용성 집합을 사용하면 VM이 Azure 데이터 센터에서 여러 오류 도메인 간에 분산될 뿐만 아니라 다양한 유지 관리 창이 있는 호스트에 배포됩니다. 전체 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)는 Azure의 보장된 가용성에 대해 전반적으로 설명합니다.

## <a name="vm-size"></a>VM 크기
사용할 VM의 [크기](../sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)는 실행하려는 워크로드를 기준으로 결정됩니다. 그런 다음 선택하는 크기는 처리 성능, 메모리 및 스토리지 용량 등의 요소를 결정합니다. Azure는 다양한 크기를 제공하여 다양한 유형의 사용을 지원합니다.

Azure는 VM의 크기와 운영 체제에 기반한 [시간당 가격](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)을 청구합니다. 부분 시간의 경우 Azure는 사용 시간(분)에 대해서만 청구합니다. 스토리지는 가격이 책정되며 개별적으로 청구됩니다.

## <a name="vm-limits"></a>VM 제한
구독에는 프로젝트에 대해 많은 수의 VM을 배포하는 데 영향을 줄 수 있는 기본 [할당량 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md)이 있습니다. 구독별 기준으로 현재 제한은 지역당 20대의 VM입니다. [증가를 요구하는 지원 티켓을 제출](../../azure-portal/supportability/resource-manager-core-quotas-request.md)하면 한도가 늘어날 수 있습니다.

## <a name="managed-disks"></a>Managed Disks

Managed Disks는 백그라운드에서 Azure Storage 계정 만들기 및 관리 작업을 처리하기 때문에 스토리지 계정의 확장성 제한에 걱정할 필요가 없습니다. 디스크 크기와 성능 계층(표준 또는 프리미엄)을 지정하면, Azure가 디스크를 만들고 관리합니다. 디스크를 추가하거나 VM을 확장하고 축소하면 사용 중인 스토리지에 대해 걱정할 필요가 없습니다. 새 VM을 만드는 경우 [Azure CLI를 사용](quick-create-cli.md)하거나 Azure Portal을 사용하여 관리되는 OS 및 데이터 디스크로 VM을 만듭니다. 관리되지 않는 디스크가 있는 VM이 있는 경우 [Managed Disks로 지원되도록 VM을 변환](convert-unmanaged-to-managed-disks.md)할 수 있습니다.

또한 Azure 지역당 하나의 스토리지 계정에서 사용자 지정 이미지를 관리하고 동일한 구독에서 수백 개의 VM을 만드는 데 사용할 수도 있습니다. 관리 디스크에 대한 자세한 내용은 [Managed Disks 개요](../managed-disks-overview.md)를 참조하세요.

## <a name="distributions"></a>배포 
Microsoft Azure는 많은 파트너가 제공하고 유지 관리하는 다양하고 인기 있는 Linux 배포를 지원합니다.  Azure Marketplace에서 Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD 등과 같은 배포를 찾을 수 있습니다. Microsoft에서는 다양한 Linux 커뮤니티와 적극적으로 작업하여 [Azure 인증 Linux 배포판](endorsed-distros.md) 목록에 다양한 옵션을 추가합니다.

선택한 기본 Linux 배포가 현재 갤러리에 없는 경우 [Azure에서 Linux VHD 만들기 및 업로드](create-upload-generic.md)에 따라 "직접 Linux" VM을 가져올 수 있습니다.

Microsoft는 파트너와 긴밀히 협력하여 사용 가능한 이미지가 업데이트되고 Azure 런타임에 대해 최적화되도록 합니다.  Azure 파트너에 대한 자세한 내용은 다음 링크를 참조하세요.

* Azure의 Linux - [보증 배포판](endorsed-distros.md)
* SUSE- [Azure Marketplace - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps?search=suse%20sles&page=1)
* Red Hat - [Azure Marketplace - Red Hat Enterprise Linux 8.1](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux81-ARM)
* Canonical - [Azure Marketplace - Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer)
* Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian)
* FreeBSD - [Azure Marketplace - FreeBSD 10.4](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
* CoreOS - [Azure Marketplace - CoreOS별 Container Linux](https://azuremarketplace.microsoft.com/marketplace/apps/CoreOS.CoreOS)
* RancherOS - [Azure Marketplace - RancherOS](https://azuremarketplace.microsoft.com/marketplace/apps/rancher.rancheros)
* Bitnami - [Bitnami Library for Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Mesosphere DC/OS on Azure](https://azure.microsoft.com/services/kubernetes-service/mesosphere/)
* Docker - [Azure Marketplace - Docker 이미지](https://azuremarketplace.microsoft.com/marketplace/apps?search=docker&page=1&filters=virtual-machine-images)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudbees.cloudbees-core-contact)


## <a name="cloud-init"></a>Cloud-init 

적절한 DevOps 문화권을 얻으려면 모든 인프라가 코드여야 합니다.  모든 인프라가 코드로 되어 있으면 쉽게 다시 만들 수 있습니다.  Azure는 Ansible, Chef, SaltStack 및 Puppet과 같은 모든 주요 자동화 도구와 함께 작동합니다.  또한 Azure는 자체 자동화 도구도 제공합니다.

* [Azure 템플릿](create-ssh-secured-vm-from-template.md)
* [Azure VMAccess](../extensions/vmaccess.md)

Azure는 Azure를 지원하는 대부분의 Linux 배포판에서 [cloud-init](https://cloud-init.io/)를 지원합니다.  Azure Marketplace에서 cloud-init 활성화 이미지를 사용할 수 있도록 하기 위해 승인된 Linux 배포판 파트너와 적극적으로 공조하고 있습니다. 이러한 이미지를 사용하면 VM 및 가상 머신 확장 집합에서 cloud-init 배포 및 구성 작업을 원활하게 진행할 수 있습니다.

* [Azure Linux VM에서 cloud-init 사용](using-cloud-init.md)

## <a name="storage"></a>스토리지
* [Microsoft Azure Storage 소개](../../storage/common/storage-introduction.md)
* [azure-cli를 사용하여 Linux VM에 디스크 추가](add-disk.md)
* [Azure Portal에서 Linux VM에 데이터 디스크를 연결하는 방법](attach-disk-portal.md)

## <a name="networking"></a>네트워킹
* [Virtual Network 개요](../../virtual-network/virtual-networks-overview.md)
* [Azure의 IP 주소](../../virtual-network/public-ip-addresses.md)
* [Azure에서 Linux VM에 포트 열기](nsg-quickstart.md)
* [Azure 포털에서 정규화된 도메인 이름 만들기](portal-create-fqdn.md)


## <a name="next-steps"></a>다음 단계

첫 번째 VM을 만듭니다!

- [포털](quick-create-portal.md)
- [Azure CLI](quick-create-cli.md)
- [PowerShell](quick-create-powershell.md)
