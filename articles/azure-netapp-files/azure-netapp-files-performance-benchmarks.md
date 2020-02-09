---
title: Azure NetApp Files에 대한 성능 벤치 마크 테스트 결과 | Microsoft Docs
description: 볼륨 수준에서 Azure NetApp Files에 대한 성능 벤치 마크 테스트 결과를 설명 합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 1d6b43110046f26d8c8070b19587366588eee7b6
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881748"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>Azure NetApp Files에 대한 성능 벤치 마크 테스트 결과

이 문서에서는 볼륨 수준에서 Azure NetApp Files에 대한 성능 벤치 마크 테스트 결과를 설명 합니다. 

## <a name="sample-application-used-for-the-tests"></a>테스트에 사용 되는 샘플 응용 프로그램

Azure NetApp Files를 사용 하 여 샘플 응용 프로그램에서 성능 테스트를 실행 했습니다. 응용 프로그램에는 다음과 같은 특징이 있습니다. 

* 클라우드 용으로 빌드된 Linux 기반 응용 프로그램
* 추가 된 Vm (가상 머신)으로 선형 크기를 조정 하 여 필요에 따라 계산 능력을 높일 수 있습니다.
* Data lake의 신속한 액세스 가능성 필요
* 때때로 임의의 경우에 따라 임의의 i/o 패턴을 사용할 수 있습니다. 
    * 임의 패턴은 대량의 i/o에 대해 짧은 대기 시간이 필요 합니다. 
    * 순차 패턴에는 많은 양의 대역폭이 필요 합니다. 

## <a name="about-the-workload-generator"></a>워크 로드 생성기 정보

결과는 Vdbench summary 파일에서 가져온 것입니다. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) 은 저장소 성능의 유효성을 검사 하기 위해 디스크 i/o 작업을 생성 하는 명령줄 유틸리티입니다. 사용 되는 클라이언트 서버 구성은 확장 가능 합니다.  단일 혼합 마스터/클라이언트와 14 전용 클라이언트 Vm이 포함 됩니다.

## <a name="about-the-tests"></a>테스트 정보

테스트는 샘플 응용 프로그램에 포함 될 수 있는 한도를 식별 하는 데 사용할 수 있는 테스트와 제한을 초과 하는 응답 시간을 식별 하도록 설계 되었습니다.  

다음 테스트가 실행 되었습니다. 

* 100% 8-KiB 임의 읽기
* 100% 8-KiB 임의 쓰기
* 100% 64-KiB 순차 읽기
* 100% 64-KiB 순차 쓰기
* 50% 64-KiB 순차 읽기, 50% 64-KiB 순차 쓰기
* 50% 8-KiB 임의 읽기, 50% 8-KiB 임의 쓰기

## <a name="bandwidth"></a>대역폭

Azure NetApp Files는 여러 [서비스 수준을](azure-netapp-files-service-levels.md)제공 합니다. 각 서비스 수준은 프로 비전 된 용량 (볼륨 할당량)의 TiB에 따라 서로 다른 대역폭을 제공 합니다. 볼륨의 대역폭 제한은 서비스 수준 및 볼륨 할당량의 조합을 기반으로 프로 비전 됩니다. 대역폭 제한은 실현 되는 실제 처리량의 크기를 결정 하는 데 한 가지 요소입니다.  

현재 4500 MiB는 테스트 중인 단일 볼륨에 대해 작업에서 달성 한 최고 처리량입니다.  프리미엄 서비스 수준에서 70.31 TiB 볼륨 할당량은 아래 계산에 따라 처리량을 실현할 수 있도록 충분 한 대역폭을 프로 비전 합니다. 

![대역폭 수식](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![할당량 및 서비스 수준](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>처리량 집약적인 워크 로드

처리량 테스트에서는 Vdbench 및 12xD32s V3 저장소 Vm의 조합을 사용 했습니다. 테스트의 샘플 볼륨은 다음 처리량 번호를 달성 했습니다.

![처리량 테스트](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>I/o를 많이 사용 하는 워크 로드

I/o 테스트에서는 Vdbench 및 12xD32s V3 저장소 Vm의 조합을 사용 합니다. 테스트의 샘플 볼륨은 다음 i/o 번호를 달성 했습니다.

![I/o 테스트](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>대기 시간

테스트 Vm과 Azure NetApp Files 볼륨 간의 거리가 i/o 성능에 영향을 줍니다.  아래 차트에서는 두 개의 서로 다른 Vm 집합에 대한 IOPS 및 대기 시간 응답 곡선을 비교 합니다.  한 Vm 집합은 Azure NetApp Files 근처에 있고 다른 집합은 더 멀리 떨어져 있습니다.  추가 Vm 집합의 대기 시간이 증가 하면 지정 된 병렬 처리 수준에서 달성 된 IOPS의 양에 영향을 줍니다.  상관 없이 볼륨에 대한 읽기는 아래 그림과 같이 30만 IOPS를 초과할 수 있습니다. 

![대기 시간 연구](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>요약

대기 시간에 민감한 작업 (데이터베이스)에는 1 밀리초의 응답 시간이 있을 수 있습니다. 트랜잭션 성능은 단일 볼륨에 대해 제한은 30만 개의 IOPS를 초과할 수 있습니다.

스트리밍 및 이미징에 대한 처리량 관련 응용 프로그램은 4.5 GiB/s 처리량을 가질 수 있습니다.

## <a name="example-scripts"></a>예제 스크립트

다음 예제 스크립트는 데모용 으로만 사용할 수 있습니다.  프로덕션 용도로 사용 되지 않습니다.  

    #
    #This script makes the following assumptions about the environment
    #VM Naming Convention:
    #   VM naming convention: vdbench-vnet1-1 .. vdbench-vnet1-x
    #
    #VM Count:
    #   The script is written for 24 VM's, 
    #   If you wish to test with a different number of vm's replace {1..24} with {1..Some Number}
    #
    #Volume mount point:
    #    The volumes in this script are mounted at the following mount points on all virtual machines
    #    /mnt/vdb1 ... /mnt/vdb6
    #   
    #Virtual machines must have
    #   make sure that vdbench is present on all vms in the environment and that java has been installed on each vm as well as nfs-utils
    #
    #The following tunables were configured on all virtual machines in the environment
    #    No special tunables were used to extract the results identified in this paper.
    #    Even rsize and wsize were left at the default (64K)
    #
    #Special Notes (thread counts) 
    #   You can hone in on the amount of I/O you hope to achieve in your testing using littles law,
    #   Run each test with a thread count of 1 to determnine the best possible latency.
    #   Thread count equals latency in seconds * desired I/O rate.  
    #   If you find a minimum latency of 1.14ms for example, divide that by 1000 to convert 1.3ms to .0013 seconds.
    #   
    #   If your goal from a single machine is for example 3,500 IOPS, a thread count of 4.0 is required.
    
    #   thread count 4.0 == .0013s * 5,000ops
    #
    #    
    #Special Notes: Increased window size:
    #   As round-trip time increases; corresponding increases are required in TCP Window Size – think Littles law as shown above. 
    #   Check the values of the following and adjust as needed: net.core.rmem_max, net.core.rmem_default, net.ipv4.tcp_rmem
    #
    #Run the utility like this:
    #    vdbench -f vnet1-SeqMix-workload
    #
    #When the utility is finished its run, grep 'avg' from the summary file to see overall run performance.
     
    
    
    #LUN CONFIG FILES
    for vnet in 1; do
        echo "sd=default,size=693g" > vnet${vnet}-luns-nfs
        for vm in {1..24}; do
            for vol in {1..6}; do
                echo "sd=sd-${vm}-${vol},host=vm${vm},lun=/mnt/vdb${vol}/file-${vm}-${vol},openflags=o_direct" >> vnet${vnet}-luns-nfs
            done
        done
    done
    
    
    #HOST CONFIG FILES
    for vnet in 1; do
        echo "hd=default,jvms=1,shell=ssh" > vnet${vnet}-hosts-nfs
        for vm in {1..24}; do
            echo "hd=vm${vm},system=vdbench-vnet${vnet}-${vm},user=root" >> vnet${vnet}-hosts-nfs
        done
    done
    
    #VDBENCH WORK SCRIPTS
    for vnet in 1; do
        for pattern in FillWrite SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "include=vnet${vnet}-hosts-nfs" > vnet${vnet}-${pattern}-workload
            echo "include=vnet${vnet}-luns-nfs" >> vnet${vnet}-${pattern}-workload
        done 
        #Fill Write File
        echo "wd=FillWrite,sd=sd*,rdpct=0,seekpct=eof,xfersize=64k" >> vnet${vnet}-FillWrite-workload
        echo "* Run Defaults" >> vnet${vnet}-FillWrite-workload
        echo 'rd=Initialize-LUNs-Full,wd=FillWrite,sd=("sd*"),iorate=max,interval=1,forthreads=1,elapsed=259200' >> vnet${vnet}-FillWrite-workload
    
        #The actual workload files - feel free to add more or use less.
        #   The thread counts shown below were used to generate the performance collateral, 
        #   feel free to change to suite your needs.
    
        echo "wd=SeqWrite,sd=sd*,rdpct=0,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqWrite-workload
        echo "wd=SeqRead,sd=sd*,rdpct=100,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqRead-workload
        echo "wd=RndRead,sd=sd*,rdpct=100,seekpct=100,xfersize=8k" >> vnet${vnet}-RndRead-workload
        echo "wd=RndWrite,sd=sd*,rdpct=0,seekpct=100,xfersize=8k" >> vnet${vnet}-RndWrite-workload
        echo "wd=SeqMix,sd=sd*,rdpct=50,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqMix-workload
        echo "wd=RndMix,sd=sd*,rdpct=50,seekpct=100,xfersize=8k" >> vnet${vnet}-RndMix-workload
        for pattern in SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "* Run Defaults" >> vnet${vnet}-${pattern}-workload
            #echo "rd=default,curve=(50,100),iorate=curve,warmup=60,elapsed=600,iorate=curve" >> vnet${vnet}-${pattern}-workload
            echo "rd=default,warmup=60,elapsed=600,iorate=max" >> vnet${vnet}-${pattern}-workload
            echo "* Run Definitions" >> vnet${vnet}-${pattern}-workload
        done
        echo 'rd=RndRead,wd=RndRead,sd=("sd*"),threads=25' >> vnet${vnet}-RndRead-workload
        echo 'rd=RndWrite,wd=RndWrite,sd=("sd*"),threads=20' >> vnet${vnet}-RndWrite-workload
        echo 'rd=SeqRead-4,wd=SeqRead,sd=("sd*"),threads=20' >> vnet${vnet}-SeqRead-workload
        echo 'rd=SeqWrite-26,wd=SeqWrite,sd=("sd*"),threads=26' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-32,wd=SeqWrite,sd=("sd*"),threads=32' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-40,wd=SeqWrite,sd=("sd*"),threads=40' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=RndMix,wd=RndMix,sd=("sd*"),threads=25' >> vnet${vnet}-RndMix-workload
        echo 'rd=SeqMix-6,wd=SeqMix,sd=("sd*"),threads=6' >> vnet${vnet}-SeqMix-workload
        echo 'rd=SeqMix-7,wd=SeqMix,sd=("sd*"),threads=7' >> vnet${vnet}-SeqMix-workload
    done
