---
title: HPC에 대 한 메시지 전달 인터페이스 설정-Azure Virtual Machines | Microsoft Docs
description: Azure에서 HPC에 대 한 MPI를 설정 하는 방법에 대해 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 469e926932ffa11ef9f2a262b78a587ba435549e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023993"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>HPC에 대 한 메시지 전달 인터페이스 설정

MPI (메시지 전달 인터페이스) 작업은 기존 HPC 워크 로드의 중요 한 부분입니다. Azure에서 SR-IOV 사용 VM 크기를 사용 하면 MPI의 거의 모든 버전을 사용할 수 있습니다. 

Vm에서 MPI 작업을 실행 하려면 테넌트 전체에서 파티션 키 (p-키)를 설정 해야 합니다. P 키 값을 확인 하는 방법에 대 한 자세한 내용은 [파티션 키 검색](#discover-partition-keys) 섹션의 단계를 따르세요.

## <a name="ucx"></a>함께 x

작업 [x](https://github.com/openucx/ucx) 는 IB에서 최상의 성능을 제공 하 고 MPICH 및 openmpi와 함께 작동 합니다.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

앞에서 설명한 대로를 설치 합니다.

```bash
sudo yum install –y openmpi
```

OpenMPI를 빌드합니다.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

OpenMPI를 실행 합니다.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

위에서 언급 한 대로 파티션 키를 확인 합니다.

## <a name="mpich"></a>MPICH

앞에서 설명한 대로를 설치 합니다.

MPICH를 빌드합니다.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

MPICH를 실행 합니다.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

위에서 언급 한 대로 파티션 키를 확인 합니다.

## <a name="mvapich2"></a>MVAPICH2

MVAPICH2를 빌드합니다.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

MVAPICH2를 실행 합니다.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>플랫폼 MPI Community Edition

플랫폼 MPI에 필요한 패키지를 설치 합니다.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

설치 프로세스를 따릅니다.

## <a name="intel-mpi"></a>Intel MPI

[INTEL MPI를 다운로드](https://software.intel.com/mpi-library/choose-download)합니다.

버전에 따라 I_MPI_FABRICS 환경 변수를 변경 합니다. Intel MPI 2018의 경우 `I_MPI_FABRICS=shm:ofa`를 사용 하 고 2019의 경우 `I_MPI_FABRICS=shm:ofi`를 사용 합니다.

프로세스 고정은 기본적으로 15, 30 및 60 PPN에 대해 제대로 작동 합니다.

## <a name="osu-mpi-benchmarks"></a>OSU MPI 벤치 마크

[OSU MPI 벤치 마크](http://mvapich.cse.ohio-state.edu/benchmarks/) 및 Untar를 다운로드 합니다.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

특정 MPI 라이브러리를 사용 하 여 벤치 마크 빌드:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI 벤치 마크는 `mpi/` 폴더에 있습니다.


## <a name="discover-partition-keys"></a>파티션 키 검색

동일한 테넌트 내의 다른 Vm (가용성 집합 또는 VM 확장 집합)과 통신 하기 위한 파티션 키 (p 키)를 검색 합니다.

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

둘 중 더 큰 값은 MPI에서 사용 해야 하는 테넌트 키입니다. 예: 다음은 p 키입니다. 0x800b는 MPI와 함께 사용 해야 합니다.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

기본 (0x7fff) 파티션 키가 아닌 다른 파티션을 사용 합니다. 키를 사용 하려면 MSB의 키를 지워야 합니다. 예를 들어 0x800b에 대해 UCX_IB_PKEY를 0x000b로 설정 합니다.

또한 테넌트 (AVSet 또는 VMSS)가 있는 한 PKEYs는 동일 하 게 유지 됩니다. 노드가 추가 되거나 삭제 되는 경우에도 마찬가지입니다. 새 테넌트가 서로 다른 PKEYs를 가져옵니다.


## <a name="set-up-user-limits-for-mpi"></a>MPI에 대 한 사용자 제한 설정

MPI에 대 한 사용자 제한을 설정 합니다.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>MPI에 대 한 SSH 키 설정

필요한 MPI 유형에 대 한 SSH 키를 설정 합니다.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

위의 구문은 공유 홈 디렉터리를 가정 합니다. ssh 디렉터리를 각 노드에 복사 해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure의 [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 에 대해 자세히 알아보세요.
