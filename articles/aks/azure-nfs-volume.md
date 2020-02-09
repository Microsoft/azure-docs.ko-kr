---
title: AKS (pod of Azure Kubernetes Service)에서 사용할 NFS (네트워크 파일 시스템) Ubuntu 서버를 만듭니다.
description: AKS (Azure Kubernetes Service)에서 pod와 함께 사용할 NFS Ubuntu Linux 서버 볼륨을 수동으로 만드는 방법에 대해 알아봅니다.
services: container-service
author: ozboms
ms.service: container-service
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 9b9c4b326596887774d9dfc0dd792052ec672be2
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063818"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>AKS (Azure Kubernetes Service)를 사용 하 여 NFS (네트워크 파일 시스템) Linux 서버 볼륨을 수동으로 만들고 사용 합니다.
컨테이너 간에 데이터를 공유 하는 것은 종종 컨테이너 기반 서비스 및 응용 프로그램의 필수 구성 요소입니다. 일반적으로 외부 영구 볼륨에서 동일한 정보에 액세스 해야 하는 다양 한 pod 있습니다.    
Azure files는 옵션 이지만 Azure VM에서 NFS 서버를 만드는 것은 또 다른 형태의 영구 공유 저장소입니다. 

이 문서에서는 Ubuntu 가상 머신에서 NFS 서버를 만드는 방법을 보여 줍니다. 또한 AKS 컨테이너에이 공유 파일 시스템에 대한 액세스 권한을 부여 합니다.

## <a name="before-you-begin"></a>시작하기 전에
이 문서에서는 기존 AKS 클러스터가 있다고 가정 합니다. AKS 클러스터가 필요한 경우 [Azure CLI를 사용][aks-quickstart-cli] 하거나 [Azure Portal를 사용][aks-quickstart-portal]하 여 AKS 빠른 시작을 참조 하세요.

AKS 클러스터는 NFS 서버와 동일한 또는 피어 링 가상 네트워크에 있어야 합니다. 클러스터는 VM과 동일한 VNET 일 수 있는 기존 VNET에서 만들어야 합니다.

기존 VNET을 사용 하 여를 구성 하는 단계는 설명서: [기존 vnet에서 AKS 클러스터 만들기][aks-virtual-network] 및 [VNET 피어 링을 사용 하 여 가상 네트워크 연결][peer-virtual-networks] 의 설명에 설명 되어 있습니다.

또한 Ubuntu Linux 가상 컴퓨터 (예: 18.04 LTS)를 만들었다고 가정 합니다. 설정 및 크기는 원하는 대로 할 수 있으며 Azure를 통해 배포할 수 있습니다. Linux 빠른 시작은 [LINUX VM 관리][linux-create]를 참조 하세요.

AKS 클러스터를 먼저 배포 하는 경우 Azure는 Ubuntu 컴퓨터를 배포할 때 가상 네트워크 필드를 자동으로 채워 동일한 VNET 내에 라이브 합니다. 그러나 피어 링 네트워크를 대신 사용 하려는 경우 위의 설명서를 참조 하세요.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>가상 컴퓨터에 NFS 서버 배포
Ubuntu 가상 머신 내에서 NFS 서버를 설정 하는 스크립트는 다음과 같습니다.
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
스크립트 때문에 서버가 다시 시작 되 고 AKS에 NFS 서버를 탑재할 수 있습니다.

>[!IMPORTANT]  
>**AKS_SUBNET** 를 클러스터의 올바른 항목으로 바꿔야 합니다. 그렇지 않으면 "*"를 사용 하 여 모든 포트 및 연결에 NFS 서버를 엽니다.

VM을 만든 후에는 위의 스크립트를 파일에 복사 합니다. 그런 다음을 사용 하 여 로컬 컴퓨터 또는 스크립트를 VM으로 이동할 수 있습니다. 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
스크립트가 VM에 있으면 VM에 ssh를 수행 하 고 명령을 통해 실행할 수 있습니다.
```console
sudo ./nfs-server-setup.sh
```
권한 거부 오류로 인해 실행이 실패 하면 명령을 통해 실행 권한을 설정 합니다.
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>NFS 서버에 AKS 클러스터 연결
볼륨에 액세스 하는 방법을 지정 하는 영구 볼륨 및 영구적 볼륨 클레임을 프로 비전 하 여 NFS 서버를 클러스터에 연결할 수 있습니다.

동일한 가상 네트워크 또는 피어 링 가상 네트워크에서 두 서비스를 연결 해야 합니다. 동일한 VNET에서 클러스터를 설정 하는 방법에 대한 지침은 [기존 vnet에서 AKS 클러스터 만들기][aks-virtual-network] 를 참조 하세요.

동일한 가상 네트워크 (또는 피어 링)에 있으면 AKS 클러스터에서 영구적 볼륨 및 영구 볼륨 클레임을 프로 비전 해야 합니다. 그런 다음 컨테이너는 NFS 드라이브를 로컬 디렉터리에 탑재할 수 있습니다.

다음은 영구 볼륨에 대한 예제 Kubernetes 정의입니다 (이 정의는 클러스터와 VM이 동일한 VNET에 있다고 가정).

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
NFS 서버 정보를 사용 하 여 **NFS_INTERNAL_IP**, **NFS_NAME** 및 **NFS_EXPORT_FILE_PATH** 를 바꿉니다.

영구적 볼륨 클레임 파일도 필요 합니다. 포함할 항목의 예는 다음과 같습니다.

>[!IMPORTANT]  
>**"storageClassName"** 은 빈 문자열을 유지 해야 합니다. 그렇지 않으면 클레임이 작동 하지 않습니다.

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
클러스터에서 서버에 연결할 수 없는 경우 내보낸 디렉터리 또는 해당 부모에 서버에 액세스할 수 있는 권한이 없는 것일 수 있습니다.

내보내기 디렉터리와 부모 디렉터리 모두에 777 권한이 있는지 확인 합니다.

아래 명령을 실행 하 여 사용 권한을 확인할 수 있습니다. 디렉터리에는 *' drwxrwxrwx '* 권한이 있어야 합니다.
```console
ls -l
```

## <a name="more-information"></a>자세한 정보
전체 연습을 얻거나 NFS 서버 설치 프로그램을 디버그 하는 데 도움이 되는 자세한 자습서는 다음과 같습니다.
  - [NFS 자습서][nfs-tutorial]

## <a name="next-steps"></a>다음 단계

관련 모범 사례는 [AKS의 저장소 및 백업에 대한 모범 사례][operator-best-practices-storage]를 참조 하세요.

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
