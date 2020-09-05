---
title: Python 3 사용 Linux Azure Virtual Machines 시스템에서 VM 확장을 사용 하는 문제
description: Python 3 사용 Linux 시스템에서 VM 확장을 사용 하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
ms.author: jparrel
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.assetid: 3cd520fd-eaf7-4ef9-b4d3-4827057e5028
ms.openlocfilehash: 15ece836e172b8316222ea606ca638650795d5d7
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852604"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>Python 3 사용 Linux Azure Virtual Machines 시스템에서 VM 확장을 사용 하는 문제

> [!NOTE]
> 워크 로드에 **python** 2.x가 지원 되어야 하는 경우가 아니면 사용자는 자신의 시스템에 **python** 3.x를 채택 하는 것이 좋습니다. 이러한 요구 사항의 예에는 레거시 관리 스크립트나 **Azure Disk Encryption** 및 **Azure Monitor**같은 확장이 포함 될 수 있습니다.
>
> 프로덕션 환경에 **python** 2.x를 설치 하기 전에 python 2.x의 장기 지원, 특히 보안 업데이트를 받을 수 있는 기능을 고려 합니다. 언급 된 확장 중 일부를 비롯 한 제품 ( **python 3.8** 지원으로 업데이트)은 python 2.x 사용을 중단 해야 합니다.

일부 Linux 배포판은 Python 3.8로 전환 되 고 `/usr/bin/python` python 용 레거시 entrypoint를 모두 제거 했습니다. 이러한 전환은 다음과 같은 두 가지 조건을 사용 하 여 특정 VM (가상 머신) 확장의 자동화 된 자동 배포에 영향을 줍니다.

- Python 3.x 지원으로 전환 중인 확장
- 레거시 entrypoint를 사용 하는 확장 `/usr/bin/python`

**Python** 3.x로 전환 된 Linux 배포 사용자는 `/usr/bin/python` 해당 vm에 확장을 배포 하기 전에 레거시 entrypoint가 존재 하는지 확인 해야 합니다. 그렇지 않으면 확장 배포가 실패할 수 있습니다. 

- 영향을 받는 보증 Linux 배포판에는 **Ubuntu Server 20.04 lts** 및 **ubuntu Pro 20.04 lts**가 포함 됩니다.

- 영향을 받는 VM 확장에는 **Azure Disk Encryption**, **Log Analytics**, **vm 액세스** (암호 재설정에 사용 됨) 및 **게스트 진단** (추가 성능 카운터에 사용 됨)이 포함 됩니다.

**Ubuntu 18.04 LTS** 에서 **ubuntu 20.04 lts**로 업그레이드 하는 것과 같은 현재 위치의 업그레이드는 `/usr/bin/python` symlink를 유지 하 고 영향을 받지 않은 상태로 유지 되어야 합니다.

## <a name="resolution"></a>해결 방법

앞의 요약에 설명 된 대로 알려진 영향을 받는 시나리오에 확장을 배포 하기 전에 다음과 같은 일반적인 권장 사항을 고려 합니다.

1. 확장을 배포 하기 전에 `/usr/bin/python` Linux 배포 공급 업체에서 제공 하는 방법을 사용 하 여 symlink를 복원 합니다.

   - 예를 들어 **Python 2.7**의 경우 다음을 사용 합니다. `sudo apt update && sudo apt install python-is-python2`

1. 이 권장 사항은 Azure 고객을 위한 것 이며 Azure Stack에서 지원 되지 않습니다.

   - 이 문제가 발생 하는 인스턴스를 이미 배포한 경우 VM 블레이드에서 명령 실행 기능을 사용 하 여 위에서 언급 한 명령을 실행 합니다. 실행 명령 확장 자체는 Python 3.8 전환의 영향을 받지 않습니다.

1. 새 인스턴스를 배포 하 고 프로 비전 시 확장을 설정 해야 하는 경우 **클라우드 초기화** 사용자 데이터를 사용 하 여 위에서 언급 한 패키지를 설치 합니다.

   예를 들어 Python 2.7:

   ```python
   # create cloud-init config
   cat > cloudinitConfig.json <<EOF
   #cloud-config
   package_update: true
    
   runcmd:
   - sudo apt update
   - sudo apt install python-is-python2 
   EOF

   # create VM
   az vm create \
       --resource-group <resourceGroupName> \
       --name <vmName> \
       --image <Ubuntu 20.04 Image URN> \
       --admin-username azadmin \
       --ssh-key-value "<sshPubKey>" \
       --custom-data ./cloudinitConfig.json
   ```

1. 조직의 정책 관리자가 Vm에 확장을 배포 하지 않아야 하는 경우 프로 비전 시간에 확장 지원을 사용 하지 않도록 설정할 수 있습니다.

   - REST API

     이 속성을 사용 하 여 VM을 배포할 수 있는 경우 확장을 사용 하지 않도록 설정 하 고 설정 하려면:

     ```python
       "osProfile": {
         "allowExtensionOperations": false
       },
     ```

## <a name="next-steps"></a>다음 단계

추가 정보는 [기본적으로 18.04 LTS-Python 3 이후의 다른 기본 시스템 변경 내용을](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default) 참조 하세요.
