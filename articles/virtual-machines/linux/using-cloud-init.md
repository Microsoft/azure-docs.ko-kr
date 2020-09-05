---
title: Azure의 Linux VM에 대한 cloud-init 지원 개요
description: Azure에서 프로비저닝 시간에 VM을 구성하는 cloud-init 기능의 개요입니다.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/15/2020
ms.author: danis
ms.openlocfilehash: 7ddbb48f3598780988feb25a11729a5086d31fde
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869272"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Azure의 가상머신에 대한 cloud-init 지원
이 문서에서는 Azure에서 프로비저닝 시간에 VM(가상 머신) 또는 가상 머신 확장 집합을 구성하는 [cloud-init](https://cloudinit.readthedocs.io)에 대한 지원을 설명합니다. Azure에서 리소스가 프로비저닝된 후 처음 부팅할 때 이러한 cloud-init 구성이 실행됩니다.  

VM 프로비저닝은 Azure가 호스트 이름, 사용자 이름, 암호 등의 VM Create 매개 변수 값을 전달하여 부팅 시 VM이 사용할 수 있게 하는 프로세스입니다. ‘프로비저닝 에이전트’는 이런 값을 사용하고, VM을 구성하며, 완료되면 다시 보고합니다. 

Azure는 [cloud-init](https://cloudinit.readthedocs.io) 및 [WALA(Azure Linux Agent)](../extensions/agent-linux.md) 등의 두 프로비저닝 에이전트를 지원합니다.

## <a name="cloud-init-overview"></a>cloud-init 개요
[cloud-init](https://cloudinit.readthedocs.io)는 처음 부팅 시 Linux VM을 사용자 지정하는 데 널리 사용되는 방법입니다. Cloud-init를 사용하여 패키지를 설치하고 파일을 쓰거나, 사용자 및 보안을 구성할 수 있습니다. 초기 부팅 프로세스 중에 cloud-init가 호출되므로 구성을 적용하기 위한 추가 단계나 필요한 에이전트가 없습니다.  `#cloud-config` 파일 또는 다른 입력의 형식을 올바르게 지정하는 방법에 대한 자세한 내용은 [cloud-init 설명서 사이트](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)를 참조하세요.  `#cloud-config` 파일은 base64로 인코딩된 텍스트 파일입니다.

cloud-init는 배포 간에도 작동합니다. 예를 들어, 패키지를 설치하는 데 **apt-get install** 또는 **yum install**은 사용하지 않습니다. 대신 설치할 패키지 목록을 정의할 수 있습니다. cloud-init는 선택한 배포판에 대해 기본 패키지 관리 도구를 자동으로 사용합니다.

Azure Marketplace에서 클라우드 초기화 사용 이미지를 사용할 수 있도록 보증 Linux 배포판 파트너와 적극적으로 작업 하 고 있습니다. 이러한 이미지를 사용하면 VM 및 가상 머신 확장 집합에서 cloud-init 배포 및 구성 작업을 원활하게 진행할 수 있습니다. 처음에는 보증된 Linux 배포판 파트너 및 공급업체와의 협력을 통해 cloud-init가 Azure의 해당 OS에서 작동하게 하였고, 이후 패키지를 업데이트하고 배포판 패키지 리포지토리에서 공개 제공했습니다. 

Azure에서 보증된 Linux 배포판 OS에 cloud-init를 제공하는 것은 패키지 지원을 제공한 다음, 이미지 지원을 제공하는 두 단계로 이루어져 있습니다.
* 'Azure에서의 cloud-init 패키지 지원’은 이제 어떤 cloud-init 패키지가 지원되거나 미리 보기 상태인지 설명하므로 이 패키지를 사용자 지정 이미지의 OS에 사용할 수 있습니다.
* '이미지 cloud-init 준비 여부'는 이미지가 이미 cloud-init를 사용하도록 구성되었는지 설명합니다.


### <a name="canonical"></a>Canonical
| 게시자/버전| 제안 | SKU | 버전 | 이미지 cloud-init 준비 여부 | Azure에서의 cloud-init 패키지 지원|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical 20.04 |UbuntuServer |18.04-LTS |최신 |예 | 예 |
|Canonical 18.04 |UbuntuServer |18.04-LTS |최신 |예 | 예 |
|Canonical 16.04|UbuntuServer |16.04-LTS |최신 |예 | 예 |
|Canonical 14.04|UbuntuServer |14.04.5-LTS |최신 |예 | 예 |

### <a name="rhel"></a>RHEL
| 게시자/버전 | 제안 | SKU | 버전 | 이미지 cloud-init 준비 여부 | Azure에서의 cloud-init 패키지 지원|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |예 | 예 - 지원 시작 패키지 버전: *18.2-1.el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | 예 (참고: 미리 보기 이미지 이며 더 이상 사용 하지 **않아야 합니다** . 1 월 2020 일에 제거 됩니다.) | N/A |
|RedHat 7.7(Gen1)|RHEL |7.7 | 7.7.2020051912 | 예 | 예 - 지원 시작 패키지 버전: *18.5-6.el7*|
|RedHat 7.7(Gen2)|RHEL | 77-gen2 | 7.7.2020051913 | 예 | 예 - 지원 시작 패키지 버전: *18.5-6.el7*|
|RedHat 7.7(Gen1)|RHEL |7-LVM | 7.7.2020051921 | 예 | 예 - 지원 시작 패키지 버전: *18.5-6.el7*|
|RedHat 7.7(Gen2)|RHEL | 7lvm-gen2 | 7.7.2020051922  | 예 | 예 - 지원 시작 패키지 버전: *18.5-6.el7*|
|RedHat 7.7(Gen1) |rhel-byos | rhel-lvm77 | 7.7.20200416 | 예  | 예 - 지원 시작 패키지 버전: *18.5-6.el7*|
|RedHat 8.1(Gen1) |RHEL |8.1-ci |8.1.2020042511 | 예 (참고: 미리 보기 이미지 이며, 모든 RHEL 8.1 이미지가 클라우드 init를 지 원하는 경우 1 2020 월 1 일에 제거 됨) | 아니요, 전체 지원 ETA는 2020년 6월|
|RedHat 8.1(Gen2) |RHEL |81-ci-gen2 |8.1.2020042524 | 예 (참고: 미리 보기 이미지 이며, 모든 RHEL 8.1 이미지가 클라우드 init를 지 원하는 경우 1 2020 월 1 일에 제거 됨) | 아니요, 전체 지원 ETA는 2020년 6월 |

* 모든 RedHat: RHEL 7.8 및 8.2 (Gen1 및 Gen2) 이미지는 클라우드 초기화를 사용 하 여 프로 비전 됩니다.

### <a name="centos"></a>CentOS

| 게시자/버전 | 제안 | SKU | 버전 | 이미지 cloud-init 준비 여부 | Azure에서의 cloud-init 패키지 지원|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |예 (참고: 미리 보기 이미지 이며 더 이상 사용 하지 **않아야 합니다** . 1 월 2020 일에 제거 됩니다.) | N/A |
|OpenLogic 7.7 |CentOS | 7.7 |7.7.2020062400 |예 | 예-패키지 버전에 대 한 지원: `18.5-6.el7.centos.5`|
|OpenLogic 7.7 (Gen2) |CentOS | 7_7-gen2 |7.7.2020062401 |예 | 예-패키지 버전에 대 한 지원: `18.5-6.el7.centos.5`|
|OpenLogic 7.7 |CentOS-HPC | 7.7 |7.6.2020062600 |예 | 예-패키지 버전에 대 한 지원: `18.5-6.el7.centos.5`|
|OpenLogic 7.7 (Gen2) |CentOS-HPC | 7_7-gen2 |7.6.2020062601 |예 | 예-패키지 버전에 대 한 지원: `18.5-6.el7.centos.5`|
|OpenLogic 8.1 |CentOS | 8_1 |8.1.2020062400 |예 | 예-패키지 버전에 대 한 지원: `18.5-7.el8_1.1`|
|OpenLogic 8.1 (Gen2) |CentOS | 8_1-gen2 |8.1.2020062401 |예 | 예-패키지 버전에 대 한 지원: `18.5-7.el8_1.1`|
|OpenLogic 8.1 |CentOS-HPC | 8_1 |8.1.2020062400 |예 | 예-패키지 버전에 대 한 지원: `18.5-7.el8_1.1`|
|OpenLogic 8.1 (Gen2) |CentOS: 8_1-gen2 | 8_1-gen2 |8.1.2020062401 |예 | 예-패키지 버전에 대 한 지원: `18.5-7.el8_1.1`|

* 모든 OpenLogic: CentOS 7.8 및 8.2 (Gen1 및 Gen2) 이미지는 클라우드 초기화를 사용 하 여 프로 비전 됩니다.

### <a name="oracle"></a>Oracle

| 게시자/버전 | 제안 | SKU | 버전 | 이미지 cloud-init 준비 여부 | Azure에서의 cloud-init 패키지 지원|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7.7 |Oracle-Linux |77-ci |7.7.01| 미리 보기 이미지 (참고: 미리 보기 이미지 이며, 모든 Oracle 7.7 이미지가 클라우드 init를 지 원하는 경우이를 2020 제거 합니다. | 아니요, 미리 보기의 패키지는 다음과 같습니다. *18.5-3.0.1.el7*

### <a name="suse-sles"></a>SUSE SLES
이러한 SLES 이미지는 클라우드 초기화를 사용 하 여 프로 비전 되도록 업데이트 되었으며, Gen2 이미지 변종이 업데이트 되었습니다.
* suse: sles-15-sp1-{basic/byos/hpc/hpc-byos/chost-byos}: gen1:2020.06.10
* suse: sles: gen1:2020.06.10
* suse: sles-15-sp1-byos: gen1:2020.06.10
* suse: 2020.06.10: gen1:
* suse: 관리자-서버-4-byos: gen1:2020.06.10
* suse: sles-{byos/sap/sap-byos}: 15:2020.06.10
* suse: sles-12-sp5: gen1:2020.06.10
* suse: sles-12-sp5 {-byos/basic/hpc-byos/hpc}: gen1:2020.06.10
* suse: sles-{byos/sap/sap-byos}: 12-sp4:2020.06.10
* suse: sles-{byos/sap/sap-byos}: 12-sp3:2020.06.10
* suse: sles-{byos/sap/sap-byos}: 12-sp2:2020.06.10


### <a name="debian"></a>Debian
| 게시자/버전 | 제안 | SKU | 버전 | 이미지 cloud-init 준비 여부 | Azure에서의 cloud-init 패키지 지원|
|:--- |:--- |:--- |:--- |:--- |:--- |
| debian (Gen1) |debian-10 | 10-cloudinit |cloud-init-preview| 예 (미리 보기 전용) | 아니요, 미리 보기 상태임 |
| debian (Gen2) |debian-10 | 10-cloudinit-gen2 |cloud-init-preview| 예 (미리 보기 전용) | 아니요, 미리 보기 상태임 |




현재 Azure Stack은 cloud-init 사용이 가능한 이미지의 프로비저닝을 지원합니다.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>cloud-init와 Linux 에이전트(WALA)의 차이는 무엇입니까?
WALA는 VM을 프로비저닝 및 구성하고 [Azure 확장](../extensions/features-linux.md)을 처리하는 데 사용되는 Azure 플랫폼 관련 에이전트입니다. 

기존 cloud-init 고객이 현재 cloud-init 스크립트를 사용하거나 새 고객이 상세한 cloud-init 구성 기능을 활용할 수 있게, Linux 에이전트 대신 cloud-init를 사용하도록 VM을 구성하는 작업을 개선하고 있습니다. Linux 시스템을 구성하기 위해 cloud-init 스크립트에 이미 투자한 경우 cloud-init 프로세스를 사용하기 위한 **추가 설정이 필요하지 않습니다**. 

cloud-init는 Azure 확장을 처리하지 않으므로 확장 처리를 위해 이미지에 여전히 WALA가 필요하나, cloud-init에서 프로비저닝을 위해 변환 중인 보증된 Linux 배포판 이미지에 대해 프로비저닝 코드를 사용하지 않도록 설정해야 WALA가 설치되어 올바르게 설정됩니다.

VM을 만들 때 프로비저닝 시간에 Azure CLI `--custom-data` 스위치를 포함하지 않은 경우 cloud-init 또는 WALA는 VM을 프로비저닝하는 데 필요한 최소한의 VM 프로비저닝 매개 변수를 사용하여 기본 설정으로 배포를 완료합니다.  `--custom-data` 스위치로 cloud-init 구성을 참조할 경우 VM 부팅 시점에 사용자 지정 데이터에 포함된 모든 항목이 cloud-init에 제공됩니다.

VM에 적용된 cloud-init 구성은 시간 제약 조건이 없으므로 제한 시간 초과로 인한 배포 실패가 없습니다. WALA에는 해당하지 않습니다. WALA 기본값을 변경하여 사용자 지정 데이터를 처리할 경우 전체 VM 프로비저닝 시간 허용 한도인 40분을 초과할 수 없으므로 VM Create에 실패합니다.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>cloud-init를 사용하는 가상 머신 배포
cloud-init를 사용하는 가상 머신 배포 방법은 배포하는 동안 cloud-init 지원 배포를 참조하는 것만큼 간단합니다.  Linux 배포 유지 관리자는 cloud-init를 사용하도록 설정하고 기본 Azure 게시 이미지와 통합하도록 선택해야 합니다. 배포하려는 이미지에서 cloud-init가 설정되었는지 확인한 후 AzureCLI를 사용하여 이미지를 배포할 수 있습니다. 

이미지 배포의 첫 번째 단계로 [az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만들어야 합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

그 다음 단계로 현재 셸에서 *cloud-init.txt*라는 파일을 만들고 다음 구성을 붙여넣습니다. 이 예제에서는 로컬 컴퓨터에 없는 Cloud Shell에서 파일을 만듭니다. 원하는 모든 편집기를 사용할 수 있습니다. `sensible-editor cloud-init.txt`를 입력하여 파일을 만들고 사용할 수 있는 편집기의 목록을 봅니다. #1을 선택하여 **nano** 편집기를 사용합니다. 전체 cloud-init 파일, 특히 첫 줄이 올바르게 복사되었는지 확인합니다.

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
`ctrl-X` 키를 눌러 파일을 종료하고, `y`를 입력하여 파일을 저장하고 `enter` 키를 눌러 종료 시 파일 이름을 확인합니다.

마지막 단계로 [az vm create](/cli/azure/vm) 명령을 사용하여 VM을 만듭니다. 

다음 예제에서는 *centos74*라는 VM을 만들고 기본 키 위치에 SSH 키가 없는 경우 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.  `--custom-data` 매개 변수를 사용하여 cloud-init 구성 파일을 전달합니다. 현재 작업 디렉터리 외부에 파일을 저장한 경우 *cloud-init.txt* 구성의 전체 경로를 제공합니다. 

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

VM이 생성되면 Azure CLI가 배포에 대한 정보를 표시합니다. `publicIpAddress`을 기록해 둡니다. 이 주소는 VM에 액세스하는 데 사용됩니다.  VM을 만들고 패키지를 설치하고 앱을 시작하는 데 시간이 약간 걸립니다. Azure CLI에서 프롬프트로 반환한 후 실행을 계속하는 백그라운드 작업이 있습니다. VM에 SSH한 후 문제 해결 섹션에 설명된 단계를 사용하여 cloud-init 로그를 볼 수 있습니다. 

[ARM 템플릿에서 매개 변수](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli#inline-parameters)를 전달 하 여 클라우드 초기화를 사용 하도록 설정 된 VM을 배포할 수도 있습니다.

## <a name="troubleshooting-cloud-init"></a>cloud-init 문제 해결
VM이 프로비전되면 cloud-init는 `--custom-data`에 정의된 모든 모듈과 스크립트를 실행하여 VM을 구성합니다.  구성 오류 또는 누락을 해결해야 하는 경우 **/var/log/cloud-init.log**에 있는 cloud-init 로그에서 모듈 이름(예: `disk_setup` 또는 `runcmd`)을 검색해야 합니다.

> [!NOTE]
> 모든 모듈 실패가 심각한 cloud-init 전체 구성 실패로 이어지는 것은 아닙니다. 예를 들어 `runcmd` 모듈을 사용하는 경우 스크립트가 실패해도 runcmd 모듈이 실행되었기 때문에 cloud-init는 성공한 프로비전을 계속 보고합니다.

cloud-init 로깅에 대한 자세한 내용은 [cloud-init 설명서](https://cloudinit.readthedocs.io/en/latest/topics/logging.html)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

[클라우드 초기화 문제를 해결](cloud-init-troubleshooting.md)합니다.


구성 변경에 대한 cloud-init 예제를 보려면 다음 문서를 참조하세요.
 
- [VM에 추가 Linux 사용자 추가](cloudinit-add-user.md)
- [패키지 관리자를 실행하여 첫 번째 부팅 시 기존 패키지 업데이트](cloudinit-update-vm.md)
- [VM 로컬 호스트 이름 변경](cloudinit-update-vm-hostname.md) 
- [애플리케이션 패키지 설치, 구성 파일 업데이트 및 키 삽입](tutorial-automate-vm-deployment.md)
 
