---
title: NFS Ubuntu Linux 서버 볼륨 만들기
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Service)에서 Pod에 사용할 NFS Ubuntu Linux 서버 볼륨을 수동으로 만드는 방법에 대해 알아봅니다.
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 4e817d572a98ffb8135adf58d13f50ccacbc8746
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86251997"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)를 사용하여 NFS(네트워크 파일 시스템) Linux 서버 볼륨을 수동으로 만들고 사용합니다.
컨테이너 간에 데이터를 공유하는 것은 종종 컨테이너 기반 서비스 및 애플리케이션의 필수 구성 요소입니다. 일반적으로 외부 영구적 볼륨에서 동일한 정보에 액세스해야 하는 다양한 Pod가 있습니다.    
Azure 파일은 옵션이지만 Azure VM에서 NFS 서버를 만드는 것은 또 다른 형태의 영구 공유 스토리지입니다. 

이 문서에서는 Ubuntu 가상 머신에서 NFS 서버를 만드는 방법을 보여 줍니다. 또한 AKS 컨테이너에 이 공유 파일 시스템에 대한 액세스 권한을 부여합니다.

## <a name="before-you-begin"></a>시작하기 전에
이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

AKS 클러스터는 NFS 서버와 동일한 또는 피어링된 가상 네트워크에 있어야 합니다. 클러스터는 VM과 동일한 VNET일 수 있는 기존 VNET에서 만들어야 합니다.

기존 VNET을 사용하여 구성하는 단계는 [기존 VNET에서 AKS 클러스터 만들기][aks-virtual-network] 및 [VNET 피어링을 사용하여 가상 네트워크 연결][peer-virtual-networks] 문서에 설명되어 있습니다.

또한 Ubuntu Linux 가상 머신(예: 18.04 LTS)을 만들었다고 가정합니다. 설정 및 크기는 원하는 대로 할 수 있으며 Azure를 통해 배포할 수 있습니다. Linux 빠른 시작은 [LINUX VM 관리][linux-create]를 참조하세요.

AKS 클러스터를 먼저 배포하는 경우 Azure는 Ubuntu 컴퓨터를 배포할 때 가상 네트워크 필드를 자동으로 채워 동일한 VNET 내에 적용합니다. 그러나 대신 피어링된 네트워크를 사용하려는 경우 위의 설명서를 참조하세요.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>가상 머신에 NFS 서버 배포
Ubuntu 가상 머신 내에서 NFS 서버를 설정하는 스크립트는 다음과 같습니다.
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
스크립트의 영향으로 서버가 다시 시작되며 AKS에 NFS 서버를 탑재할 수 있습니다.

>[!IMPORTANT]  
>**AKS_SUBNET** 을 클러스터의 올바른 항목으로 바꿔야 합니다. 그러지 않으면 "*"를 사용하여 모든 포트 및 연결에 NFS 서버를 열어야 합니다.

VM을 만든 후에는 위의 스크립트를 파일에 복사합니다. 그리고 다음을 사용하여 로컬 컴퓨터 또는 스크립트가 있는 곳에서 스크립트를 VM으로 이동할 수 있습니다. 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
스크립트가 VM에 있으면 VM에서 ssh를 사용하여 다음 명령을 통해 실행할 수 있습니다.
```console
sudo ./nfs-server-setup.sh
```
권한 거부 오류로 인해 실행이 실패하면 다음 명령을 통해 실행 권한을 설정합니다.
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>NFS 서버에 AKS 클러스터 연결
볼륨에 액세스하는 방법을 지정하는 영구적 볼륨 및 영구적 볼륨 클레임을 프로비저닝하여 NFS 서버를 클러스터에 연결할 수 있습니다.

동일한 가상 네트워크 또는 피어링된 가상 네트워크에서 두 서비스를 연결해야 합니다. 동일한 VNET에서 클러스터를 설정하는 방법에 대한 지침은 [기존 VNET에서 AKS 클러스터 만들기][aks-virtual-network]를 참조하세요.

동일한 가상 네트워크(또는 피어링된 가상 네트워크)에 있으면 AKS 클러스터에서 영구적 볼륨 및 영구적 볼륨 클레임을 프로비저닝해야 합니다. 그런 다음 컨테이너는 NFS 드라이브를 로컬 디렉터리에 탑재할 수 있습니다.

다음은 영구적 볼륨에 대한 Kubernetes 정의 예입니다(이 정의는 클러스터와 VM이 동일한 VNET에 있다고 가정함).

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
**NFS_INTERNAL_IP**, **NFS_NAME** 및 **NFS_EXPORT_FILE_PATH** 를 NFS 서버 정보로 바꿉니다.

영구적 볼륨 클레임 파일도 필요합니다. 다음은 포함해야 하는 항목의 예입니다.

>[!IMPORTANT]  
>**"storageClassName"** 은 빈 문자열로 유지되어야 합니다. 그렇지 않으면 클레임이 작동하지 않습니다.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>문제 해결
클러스터에서 서버에 연결할 수 없는 경우 내보낸 디렉터리 또는 해당 부모 디렉터리에 서버에 액세스할 수 있는 권한이 없는 것일 수 있습니다.

내보내기 디렉터리와 부모 디렉터리 모두에 777 권한이 있는지 확인합니다.

아래 명령을 실행하여 사용 권한을 확인할 수 있으며, 디렉터리에는 *'drwxrwxrwx'* 권한이 있어야 합니다.
```console
ls -l
```

## <a name="more-information"></a>자세한 정보
다음은 전체 연습을 얻거나 NFS 서버 설치 프로그램을 디버그하는 데 도움이 되는 자세한 자습서입니다.
  - [NFS 자습서][nfs-tutorial]

## <a name="next-steps"></a>다음 단계

관련 모범 사례는 [AKS에서 스토리지 및 백업에 대한 모범 사례][operator-best-practices-storage]를 참조하세요.

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: ./configure-kubenet.md#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: ../virtual-network/tutorial-connect-virtual-networks-portal.md

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
