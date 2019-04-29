---
title: Azure의 Linux 가상 머신에 대한 cloud-init 지원 개요 | Microsoft Docs
description: Microsoft Azure의 cloud-init 기능 개요
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 6dd1dd0ce2395e2b06d80385ffd299835a280526
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614037"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Azure의 가상 머신에 대한 Cloud-init 지원
이 문서에서는 Azure에서 프로비전할 때 VM(가상 머신) 또는 VMSS(가상 머신 확장 집합)을 구성할 수 있도록 [cloud-init](https://cloudinit.readthedocs.io)를 위해 존재하는 지원에 대해 설명합니다. Azure에서 리소스가 프로비전되면 처음 부팅 시 이러한 cloud-init 스크립트가 실행됩니다.  

## <a name="cloud-init-overview"></a>Cloud-init 개요
[Cloud-init](https://cloudinit.readthedocs.io)는 처음 부팅 시 Linux VM을 사용자 지정하는 데 널리 사용되는 방법입니다. Cloud-init를 사용하여 패키지를 설치하고 파일을 쓰거나, 사용자 및 보안을 구성할 수 있습니다. 초기 부팅 프로세스 중에 cloud-init가 호출되므로 구성을 적용하기 위한 추가 단계나 필요한 에이전트가 없습니다.  `#cloud-config` 파일의 형식을 제대로 지정하는 방법에 대한 자세한 내용은 [cloud-init 설명서 사이트](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)를 참조하세요.  `#cloud-config` 파일은 base64로 인코딩된 텍스트 파일입니다.

Cloud-init는 배포에서도 작동합니다. 예를 들어, 패키지를 설치하는 데 **apt-get install** 또는 **yum install**은 사용하지 않습니다. 대신 설치할 패키지 목록을 정의할 수 있습니다. cloud-init에서 선택한 배포판의 기본 패키지 관리 도구를 자동으로 사용합니다.

 Azure Marketplace에서 cloud-init 활성화 이미지를 사용할 수 있도록 하기 위해 승인된 Linux 배포판 파트너와 적극적으로 공조하고 있습니다. 이러한 이미지를 사용하면 VM 및 VMSS(VM Scale Sets)에서 cloud-init 배포 및 구성 작업을 원활하게 진행할 수 있습니다. 다음 표에서는 Azure 플랫폼에서 현재 사용 가능한 cloud-init 지원 이미지를 보여 줍니다.

| 게시자 | 제안 | SKU | Version | cloud-init 준비 여부 |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04-LTS |최신 |예 | 
|Canonical |UbuntuServer |17.10 |최신 |예 | 
|Canonical |UbuntuServer |16.04-LTS |최신 |예 | 
|Canonical |UbuntuServer |14.04.5-LTS |최신 |예 |
|CoreOS |CoreOS |Stable |최신 |예 |
|OpenLogic |CentOS |7-CI |최신 |미리 보기 |
|RedHat |RHEL |7-RAW-CI |최신 |미리 보기 |

현재 Azure Stack은 cloud-init을 사용하는 RHEL 7.4 및 CentOS 7.4 프로비전을 지원하지 않습니다.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>cloud-init와 Linux 에이전트(WALA)의 차이는 무엇입니까?
WALA는 VM을 프로비전 및 구성하고 Azure 확장을 처리하는 데 사용되는 Azure 플랫폼 관련 에이전트입니다. 기존 cloud-init 고객이 현재 cloud-init 스크립트를 사용할 수 있도록, Linux 에이전트 대신 cloud-init를 사용하도록 VM을 구성하는 작업을 개선하고 있습니다.  Linux 시스템을 구성하기 위해 cloud-init 스크립트에 이미 투자한 경우 **추가 설정이 필요 없습니다**. 

프로비전할 때 AzureCLI `--custom-data` 스위치를 포함하지 않으면 WALA는 VM을 프로비전하는 데 필요한 최소한의 VM 프로비전 매개 변수를 사용하여 기본 설정으로 배포를 완료합니다.  cloud-init `--custom-data` 스위치를 참조하면 사용자 지정 데이터에 포함된 것이 무엇이든(개인 설정 또는 전체 스크립트) 관계없이 WALA 기본값을 재정의합니다. 

VM의 WALA 구성은 최대 VM 프로비전 시간 내에서 작업하도록 시간이 제한됩니다.  VM에 적용되는 Cloud-init 구성은 시간 제약 조건이 없으므로 제한 시간 초과로 인한 배포 실패가 없습니다. 

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

다음 예제에서는 *centos74*라는 VM을 만들고 기본 키 위치에 SSH 키가 없는 경우 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.  `--custom-data` 매개 변수를 사용하여 cloud-init 구성 파일을 전달합니다. 현재 작업 디렉터리 외부에 파일을 저장한 경우 *cloud-init.txt* 구성의 전체 경로를 제공합니다. 다음 예제에서는 *centos74*라는 VM을 만듭니다.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

VM이 생성되면 Azure CLI가 배포에 대한 정보를 표시합니다. `publicIpAddress`을 기록해 둡니다. 이 주소는 VM에 액세스하는 데 사용됩니다.  VM을 만들고 패키지를 설치하고 앱을 시작하는 데 시간이 약간 걸립니다. Azure CLI에서 프롬프트로 반환한 후 실행을 계속하는 백그라운드 작업이 있습니다. VM에 SSH한 후 문제 해결 섹션에 설명된 단계를 사용하여 cloud-init 로그를 볼 수 있습니다. 

## <a name="troubleshooting-cloud-init"></a>cloud-init 문제 해결
VM이 프로비전되면 cloud-init는 `--custom-data`에 정의된 모든 모듈과 스크립트를 실행하여 VM을 구성합니다.  구성 오류 또는 누락을 해결해야 하는 경우 **/var/log/cloud-init.log**에 있는 cloud-init 로그에서 모듈 이름(예: `disk_setup` 또는 `runcmd`)을 검색해야 합니다.

> [!NOTE]
> 모든 모듈 실패가 심각한 cloud-init 전체 구성 실패로 이어지는 것은 아닙니다. 예를 들어 `runcmd` 모듈을 사용하는 경우 스크립트가 실패해도 runcmd 모듈이 실행되었기 때문에 cloud-init는 성공한 프로비전을 계속 보고합니다.

cloud-init 로깅에 대한 자세한 내용은 [cloud-init 설명서](https://cloudinit.readthedocs.io/en/latest/topics/logging.html)를 참조하세요. 

## <a name="next-steps"></a>다음 단계
구성 변경에 대한 cloud-init 예제를 보려면 다음 문서를 참조하세요.
 
- [VM에 추가 Linux 사용자 추가](cloudinit-add-user.md)
- [패키지 관리자를 실행하여 첫 번째 부팅 시 기존 패키지 업데이트](cloudinit-update-vm.md)
- [VM 로컬 호스트 이름 변경](cloudinit-update-vm-hostname.md) 
- [애플리케이션 패키지 설치, 구성 파일 업데이트 및 키 삽입](tutorial-automate-vm-deployment.md)
