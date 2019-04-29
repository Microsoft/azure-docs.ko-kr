---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: 81bde837cd78646f1fc59d921246c72978ecb840
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543196"
---
# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Azure의 가상 머신에서 인프라 자동화 도구 사용
Azure VM(가상 머신)을 대규모의 일관된 방식으로 만들고 관리하려면 일종의 자동화가 필요합니다. 완벽한 Azure 인프라 배포 및 관리 수명 주기를 자동화할 수 있는 다양한 도구와 솔루션이 있습니다. 이 문서에서는 Azure에서 사용할 수 있는 인프라 자동화 도구 중 일부를 소개합니다. 이러한 도구는 일반적으로 다음 방법 중 하나에 적합합니다.

- VM 구성 자동화
    - 도구에는 [Ansible](#ansible), [Chef](#chef) 및 [Puppet](#puppet)이 포함됩니다.
    - VM 사용자 지정과 관련된 도구에는 Linux VM용 [cloud-init](#cloud-init), [PowerShell DSC(Desired State Configuration)](#powershell-dsc) 및 모든 Azure VM을 위한 [Azure 사용자 지정 스크립트 확장](#azure-custom-script-extension)이 포함됩니다.
 
- 인프라 관리 자동화
    - 도구에는 사용자 지정 VM 이미지 빌드를 자동화하는 [Packer](#packer) 및 인프라 빌드 프로세스를 자동화하는 [Terraform](#terraform)이 포함됩니다.
    - [Azure Automation](#azure-automation)은 Azure와 온-프레미스 인프라에 걸쳐 작업을 수행할 수 있습니다.

- 애플리케이션 배포 및 전달 자동화
    - 예로는 [Azure DevOps Services](#azure-devops-services)와 [Jenkins](#jenkins)가 있습니다.

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/)은 구성 관리, VM 만들기 또는 애플리케이션 배포를 위한 자동화 엔진입니다. Ansible은 대개 SSH 키가 있는 에이전트 없는 모델을 사용하여 대상 컴퓨터를 인증하고 관리합니다. 구성 작업은 플레이북에서 정의되며, 특정 작업을 수행하는 데 사용할 수 있는 다양한 Ansible 모듈이 포함됩니다. 자세한 내용은 [Ansible 작동 방법(영문)](https://www.ansible.com/how-ansible-works)을 참조하세요.

방법 배우기:

- [Azure에서 사용하도록 Linux에 Ansible 설치 및 구성](../articles/virtual-machines/linux/ansible-install-configure.md)
- [Linux 가상 머신 만들기](../articles/virtual-machines/linux/ansible-create-vm.md)
- [Linux 가상 머신 관리](../articles/virtual-machines/linux/ansible-manage-linux-vm.md)


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/)는 인프라를 구성, 배포 및 관리하는 방법을 정의하는 데 도움이 되는 자동화 플랫폼입니다. 추가 구성 요소로는 인프라가 아닌 애플리케이션 수명 주기 자동화를 위한 Chef Habitat과, 보안 및 정책 요구 사항의 준수를 자동화하는 데 유용한 Chef InSpec이 있습니다. Chef Client는 대상 컴퓨터에 설치되고, 하나 이상의 중앙 Chef Server에서 구성을 저장 및 관리합니다. 자세한 내용은 [Chef 개요(영문)](https://docs.chef.io/chef_overview.html)를 참조하세요.

방법 배우기:

- [Azure Marketplace에서 Chef Automate 배포(영문)](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview)
- [Windows에 Chef 설치 및 Azure VM 만들기](../articles/virtual-machines/windows/chef-automation.md)


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com)은 애플리케이션 배달 및 배포 프로세스를 처리하는 엔터프라이즈 지원 자동화 플랫폼입니다. 에이전트가 대상 컴퓨터에 설치되어 Puppet Master에서 Azure 인프라 및 VM에 대해 원하는 구성을 정의하는 매니페스트를 실행할 수 있게 합니다. Puppet은 Jenkins 및 GitHub와 같은 다른 솔루션과 통합되어 향상된 DevOps 워크플로를 구현할 수 있습니다. 자세한 내용은 [Puppet 작동 방법(영문)](https://puppet.com/product/how-puppet-works)을 참조하세요.

방법 배우기:

- [Azure Marketplace에서 Puppet 배포(영문)](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2?tab=Overview)


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io)는 처음 부팅 시 Linux VM을 사용자 지정하는 데 널리 사용되는 방법입니다. Cloud-init를 사용하여 패키지를 설치하고 파일을 쓰거나, 사용자 및 보안을 구성할 수 있습니다. 초기 부팅 프로세스 중에 cloud-init가 호출되므로 구성을 적용하기 위한 추가 단계나 필요한 에이전트가 없습니다.  `#cloud-config` 파일의 형식을 제대로 지정하는 방법에 대한 자세한 내용은 [cloud-init 설명서 사이트](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)를 참조하세요.  `#cloud-config` 파일은 base64로 인코딩된 텍스트 파일입니다.

Cloud-init는 배포에서도 작동합니다. 예를 들어, 패키지를 설치하는 데 **apt-get install** 또는 **yum install**은 사용하지 않습니다. 대신 설치할 패키지 목록을 정의할 수 있습니다. cloud-init에서 선택한 배포판의 기본 패키지 관리 도구를 자동으로 사용합니다.

Azure Marketplace에서 cloud-init 활성화 이미지를 사용할 수 있도록 하기 위해 승인된 Linux 배포판 파트너와 적극적으로 공조하고 있습니다. 이러한 이미지를 사용하면 VM 및 가상 머신 확장 집합에서 cloud-init 배포 및 구성 작업을 원활하게 진행할 수 있습니다. Azure의 cloud-Init에 대한 자세한 정보

- [Azure에서 Linux 가상 컴퓨터에 cloud-init 지원](../articles/virtual-machines/linux/using-cloud-init.md)
- [cloud-init를 사용한 자동화된 VM 구성에 대한 자습서 진행](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md)


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell DSC(Desired State Configuration)](https://msdn.microsoft.com/powershell/dsc/overview)는 대상 컴퓨터의 구성을 정의하는 관리 플랫폼입니다. 또한 DSC는 [OMI(Open Management Infrastructure) 서버](https://collaboration.opengroup.org/omi/)를 통해 Linux에서 사용할 수 있습니다.

DSC 구성은 컴퓨터에 설치할 항목과 호스트를 구성하는 방법을 정의합니다. LCM(로컬 구성 관리자) 엔진은 푸시된 구성에 따라 요청된 작업을 처리하는 각 대상 노드에서 실행됩니다. 풀 서버는 중앙 호스트에서 실행되어 DSC 구성 및 관련 리소스를 저장하는 웹 서비스이며, 각 대상 호스트의 LCM 엔진과 통신하여 필요한 구성을 제공하고 준수 여부를 보고합니다.

방법 배우기:

- [기본 DSC 구성 만들기](https://msdn.microsoft.com/powershell/dsc/quickstarts/website-quickstart)
- [DSC 풀 서버 구성](https://msdn.microsoft.com/powershell/dsc/pullserver)
- [Linux용 DSC 사용](https://msdn.microsoft.com/powershell/dsc/lnxgettingstarted)


## <a name="azure-custom-script-extension"></a>Azure 사용자 지정 스크립트 확장
[Linux](../articles/virtual-machines/linux/extensions-customscript.md) 또는 [Windows](../articles/virtual-machines/windows/extensions-customscript.md)용 Azure 사용자 지정 스크립트 확장은 Azure VM에서 스크립트를 다운로드하고 실행합니다. 이 확장은 VM을 만들 때 또는 VM 사용 중 언제든지 사용할 수 있습니다. 

스크립트는 Azure Storage 또는 GitHub 리포지토리 등의 공개 위치에서 다운로드할 수 있습니다. 사용자 지정 스크립트 확장을 사용하면 원본 VM에서 실행되는 모든 언어로 스크립트를 작성할 수 있습니다. 이러한 스크립트는 애플리케이션을 설치하거나 필요에 따라 VM을 구성하는 데 사용할 수 있습니다. 자격 증명을 보호하기 위해 암호와 같은 중요한 정보를 보호된 구성에 저장할 수 있습니다. 이러한 자격 증명은 VM 내부에서만 암호 해독됩니다.

방법 배우기:

- [Azure CLI를 사용하여 Linux VM 만들기 및 사용자 지정 스크립트 확장 사용](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json)
- [Azure PowerShell을 사용하여 Windows VM 만들기 및 사용자 지정 스크립트 확장 사용](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json)


## <a name="packer"></a>Packer
[Packer](https://www.packer.io)는 Azure에서 사용자 지정 VM 이미지를 만들 때 빌드 프로세스를 자동화합니다. Packer를 사용하여 OS를 정의하고 특정 요구 사항에 맞게 VM을 사용자 지정하는 사후 구성 스크립트를 실행합니다. 일단 구성되면 VM이 관리 디스크 이미지로 캡처됩니다. Packer는 원본 VM, 네트워크 및 저장소 리소스를 만들고, 구성 스크립트를 실행한 다음, VM 이미지를 만드는 프로세스를 자동화합니다.

방법 배우기:

- [Packer를 사용하여 Azure에서 Linux VM 이미지 만들기](../articles/virtual-machines/linux/build-image-with-packer.md)
- [Packer를 사용하여 Azure에서 Windows VM 이미지 만들기](../articles/virtual-machines/windows/build-image-with-packer.md)


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io)은 단일 템플릿 형식 언어인 HCL(HashiCorp 구성 언어)을 사용하여 Azure 인프라 전체를 정의하고 만들 수 있는 자동화 도구입니다. Terraform을 사용하면 지정된 애플리케이션 솔루션에 대한 네트워크, 스토리지 및 VM 리소스를 만드는 프로세스를 자동화하는 템플릿을 정의할 수 있습니다. 다른 플랫폼의 기존 Terraform 템플릿을 Azure에서 사용하여 Azure Resource Manager 템플릿으로 변환할 필요 없이 일관성을 보장하고 인프라 배포를 간소화할 수 있습니다.

방법 배우기:

- [Azure를 사용하여 Terraform 설치 및 구성](../articles/virtual-machines/linux/terraform-install-configure.md)
- [Terraform을 사용하여 Azure 인프라 만들기](../articles/virtual-machines/linux/terraform-create-complete-vm.md)


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/)은 Runbook을 사용하여 대상 VM에서 일단의 작업을 처리합니다. Azure Automation은 인프라를 만드는 대신 기존 VM을 관리하는 데 사용됩니다. Azure Automation은 또한 Linux 및 Windows VM 외에도, Hybrid Runbook Worker를 사용하여 온-프레미스 가상 또는 물리적 컴퓨터에서 실행될 수 있습니다. Runbook은 GitHub와 같은 원본 제어 리포지토리에 저장할 수 있습니다. 그런 다음 이러한 Runbook은 수동으로 또는 정의된 일정에 따라 실행될 수 있습니다.

또한 Azure Automation은 지정된 VM 집합을 구성하는 방법에 대한 정의를 만들 수 있는 DSC(Desired State Configuration) 서비스도 제공합니다. 그러면 DSC에서 필요한 구성이 적용되고 VM이 일관성을 유지하고 있는지 확인합니다. Azure Automation DSC는 Windows 및 Linux 컴퓨터 모두에서 실행됩니다.

방법 배우기:

- [PowerShell Runbook 만들기](../articles/automation/automation-first-runbook-textual-powershell.md)
- [Hybrid Runbook Worker를 사용하여 온-프레미스 리소스 관리](../articles/automation/automation-hybrid-runbook-worker.md)
- [Azure Automation DSC 사용](../articles/automation/automation-dsc-getting-started.md)


## <a name="azure-devops-services"></a>Azure DevOps Services
[Azure DevOps Services](https://www.visualstudio.com/team-services/)는 코드 공유 및 추적, 자동화된 빌드 사용 및 완전한 CI/CD(연속 통합 및 연속 개발) 파이프라인 만들기를 지원하는 도구 모음입니다. Azure DevOps Services는 Visual Studio 및 다른 편집기와 통합되어 사용을 간소화합니다. 또한 Azure DevOps Services로 Azure VM을 만들고 구성한 다음, Azure VM에 코드를 배포할 수도 있습니다.

다음에 대해 자세히 알아봅니다.

- [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io)는 애플리케이션을 배포 및 테스트하고, 코드 전달을 위한 자동화된 파이프라인을 만드는 데 도움이 되는 연속 통합 서버입니다. Jenkins 핵심 플랫폼을 확장하는 플러그인은 수백 가지 있으며, webhook를 통해 다른 많은 제품 및 솔루션과도 통합할 수 있습니다. Azure VM에 Jenkins를 수동으로 설치하거나, Docker 컨테이너 내에서 Jenkins를 실행하거나, 미리 빌드된 Azure Marketplace 이미지를 사용할 수 있습니다.

방법 배우기:

- [Jenkins, GitHub 및 Docker를 사용하여 Azure에서 Linux VM의 개발 인프라 만들기](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md)


## <a name="next-steps"></a>다음 단계
Azure에서 인프라 자동화 도구를 사용하기 위한 옵션은 다양합니다. 필요한 요구 사항과 환경에 가장 적합한 솔루션을 자유롭게 사용할 수 있습니다. Azure에 기본 제공된 일부 도구를 시작 및 사용하려면 [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) 또는 [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) VM의 사용자 지정을 자동화하는 방법을 참조하세요.
