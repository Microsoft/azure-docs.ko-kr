---
title: Azure에서 OpenShift 배포 문제 해결 | Microsoft Docs
description: Azure에서 OpenShift 배포 문제를 해결합니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: af6746e7246b8783e5bdbef34cf1b57427aa7ebb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771280"
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Azure에서 OpenShift 배포 문제 해결

OpenShift 클러스터가 성공적으로 배포되지 않으면 Azure Portal에서 오류가 출력됩니다. 출력이 읽기 어려워 문제를 파악하기가 어려울 수도 있습니다. 이 출력에서 종료 코드 3, 4 또는 5를 빠르게 검사합니다. 다음은 이러한 세 개의 종료 코드에 대한 정보를 제공합니다.

- 종료 코드 3: Red Hat 구독 사용자 이름 / 암호 또는 조직 ID / 활성화 키가 올바르지 않습니다
- 종료 코드 4: Red Hat 풀 ID에 잘못 되었거나 사용 가능한 자격이
- 종료 코드 5: Docker 씬 풀 볼륨을 프로 비전 할 수 없습니다.

다른 모든 종료 코드의 경우, ssh를 통해 호스트에 연결하여 로그 파일을 봅니다.

**OpenShift Container Platform**

ansible 플레이북 호스트에 대해 SSH를 수행합니다. 템플릿 또는 Marketplace 제품의 경우 bastion 호스트를 사용합니다. bastion에서 클러스터의 다른 모든 노드(마스터, 인프라, CNS, 컴퓨팅)에 대해 SSH를 수행할 수 있습니다. 로그 파일을 보려면 루트여야 합니다. 기본적으로 SSH 액세스에는 루트를 사용할 수 없으므로, 다른 노드에 대해 SSH를 수행하는 데 루트를 사용하지 마세요.

**OKD**

ansible 플레이북 호스트에 대해 SSH를 수행합니다. OKD 템플릿(버전 3.9 이하)의 경우 master-0 호스트를 사용합니다. OKD 템플릿(버전 3.10 이상)의 경우 bastion 호스트를 사용합니다. ansible 플레이북 호스트에서 클러스터의 다른 모든 노드(마스터, 인프라, CNS, 컴퓨팅)에 대해 SSH를 수행할 수 있습니다. 로그 파일을 보려면 루트(sudo su -)여야 합니다. 기본적으로 SSH 액세스에는 루트를 사용할 수 없으므로, 다른 노드에 대해 SSH를 수행하는 데 루트를 사용하지 마세요.

## <a name="log-files"></a>로그 파일

호스트 준비 스크립트에 대 한 로그 파일 (stderr 및 stdout)에 위치한 `/var/lib/waagent/custom-script/download/0` 모든 호스트에 있습니다. 호스트를 준비하는 동안 오류가 발생한 경우 이 로그 파일을 보고 오류를 확인합니다.

준비 스크립트를 성공적으로 실행 하는 경우 다음 로그 파일에 `/var/lib/waagent/custom-script/download/1` ansible 플레이 북 호스트의 디렉터리를 검사 해야 합니다. OpenShift를 실제로 설치하는 동안 오류가 발생한 경우 stdout 파일에 오류가 표시됩니다. 추가 지원을 받으려면 이 정보를 사용하여 고객 지원팀에 문의하세요.

예제 출력

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

설치 중에 발생하는 가장 일반적인 오류는 다음과 같습니다.

1. 개인 키에 암호가 있음
2. 개인 키를 사용한 키 자격 증명 모음 비밀이 올바르게 생성되지 않음
3. 서비스 주체 자격 증명이 잘못 입력됨
4. 서비스 주체에는 리소스 그룹에 대한 contributor 권한이 없음

### <a name="private-key-has-a-passphrase"></a>개인 키에 암호가 있음

권한이 거부 된에 대 한 ssh 오류가 표시 됩니다. ssh 개인 키에 대 한 암호를 확인 하 고 ansible 플레이 북 호스트로 합니다.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>개인 키를 사용한 키 자격 증명 모음 비밀이 올바르게 생성되지 않음

개인 키 ansible 플레이 북 호스트-~/.ssh/id_rsa에 복사 됩니다. 이 파일이 올바른지 확인합니다. ansible 플레이북 호스트에서 클러스터 노드 중 하나에 대한 SSH 세션을 열어 테스트합니다.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>서비스 주체 자격 증명이 잘못 입력됨

템플릿 또는 Marketplace 제품에 입력을 제공할 때 잘못된 정보가 제공되었습니다. 서비스 주체에 대한 올바른 appId(clientId) 및 암호(clientSecret)를 사용합니다. 다음 azure cli 명령을 실행하여 확인합니다.

```bash
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>서비스 주체에는 리소스 그룹에 대한 contributor 권한이 없음

Azure 클라우드 공급자를 사용하는 경우 사용되는 서비스 주체에 리소스 그룹에 대한 contributor 권한이 있어야 합니다. 다음 azure cli 명령을 실행하여 확인합니다.

```bash
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>추가 도구

일부 오류의 경우 다음 명령을 사용하여 자세한 정보를 가져올 수도 있습니다.

1. systemctl 상태 \<서비스 >
2. journalctl -xe
