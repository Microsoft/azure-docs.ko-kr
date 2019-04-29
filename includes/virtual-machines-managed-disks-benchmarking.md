---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9c59b98fb615266c193f997c01c83922c18d4408
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679784"
---
*캐시 준비 중*  
ReadOnly 호스트 캐싱을 사용한 디스크는 디스크 제한보다 더 높은 IOPS를 제공할 수 있습니다. 호스트 캐시에서 이 최대 읽기 성능을 얻으려면 먼저 이 디스크의 캐시를 준비해야 합니다. 이렇게 하면 벤치마킹 도구에서 CacheReads 볼륨을 구동하는 읽기 IO는 직접적인 디스크가 아니라 실제 캐시에 적중합니다. 캐시는 단일 캐시가 사용된 디스크에서 추가 IOPS 결과에 도달합니다.

> [!IMPORTANT]
>  VM을 다시 부팅할 때마다 벤치마킹을 실행하기 전에 캐시를 준비해야 합니다.

## <a name="tools"></a>도구

### <a name="iometer"></a>Iometer

[Iometer 도구를 다운로드](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) 합니다.

#### <a name="test-file"></a>테스트 파일

Iometer는 벤치마킹 테스트를 실행할 볼륨에 저장된 테스트 파일을 사용합니다. IOPS 디스크 및 처리량을 측정하도록 이 테스트 파일에 읽기 및 쓰기를 구동합니다. Iometer는 이 테스트 파일을 제공받지 않은 경우 만듭니다. CacheReads 및 NoCacheWrites 볼륨에 iobw.tst라는 200GB 테스트 파일을 만듭니다.

#### <a name="access-specifications"></a>액세스 사양

요청 IO 크기, % 읽기/쓰기, % 임의/순차 사양은 Iometer의 “액세스 사양” 탭을 사용하여 구성됩니다. 아래에 설명된 각 시나리오에 대한 액세스 사양을 만듭니다. 액세스 사양을 만들고 RandomWrites\_8K, RandomReads\_8K와 같은 적절한 이름으로 “저장”합니다. 테스트 시나리오를 실행할 때 해당 사양을 선택합니다.

최대 쓰기 IOPS 시나리오에 대한 액세스 사양의 예는 아래와 같습니다.  
    ![최대 쓰기 IOPS에 대한 액세스 사양의 예](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>최대 IOPS 테스트 사양

 최대 IOP를 보여주기 위해 작은 요청 크기를 사용합니다. 8K 요청 크기를 사용하고 임의 쓰기 및 읽기에 대한 사양을 만듭니다.

| 액세스 사양 | 요청 크기 | 임의 % | 읽기 % |
| --- | --- | --- | --- |
| RandomWrites\_8K |8K |100 |0 |
| RandomReads\_8K |8K |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>최대 처리량 테스트 사양

 최대 처리량을 보여주기 위해 더 큰 요청 크기를 사용합니다. 64K 요청 크기를 사용하고 임의 쓰기 및 읽기에 대한 사양을 만듭니다.

| 액세스 사양 | 요청 크기 | 임의 % | 읽기 % |
| --- | --- | --- | --- |
| RandomWrites\_64K |64K |100 |0 |
| RandomReads\_64K |64K |100 |100 |

#### <a name="run-the-iometer-test"></a>Iometer 테스트 실행

 아래 단계를 수행하여 캐시를 준비합니다.

1. 아래에 표시된 값으로 두 액세스 사양을 만듭니다.

   | 이름 | 요청 크기 | 임의 % | 읽기 % |
   | --- | --- | --- | --- |
   | RandomWrites\_1MB |1MB |100 |0 |
   | RandomReads\_1MB |1MB |100 |100 |
1. 다음 매개 변수로 캐시 디스크 초기화를 위한 Iometer 테스트를 실행합니다. 대상 볼륨에 대해 3개의 작업자 스레드 및 128의 큐 크기를 사용합니다. 테스트의 [실행 시간] 기간을 [테스트 설정] 탭에서 2시간으s로 설정합니다.

   | 시나리오 | 대상 볼륨 | 이름 | 기간 |
   | --- | --- | --- | --- |
   | 디스크 캐시 초기화 |CacheReads |RandomWrites\_1MB |2시간 |
1. 다음 매개 변수로 캐시 디스크 준비를 위한 Iometer 테스트를 실행합니다. 대상 볼륨에 대해 3개의 작업자 스레드 및 128의 큐 크기를 사용합니다. 테스트의 [실행 시간] 기간을 [테스트 설정] 탭에서 2시간으s로 설정합니다.

   | 시나리오 | 대상 볼륨 | 이름 | 기간 |
   | --- | --- | --- | --- |
   | 캐시 디스크 준비 |CacheReads |RandomReads\_1MB |2시간 |

캐시 디스크를 준비한 후 아래에 나열된 테스트 시나리오를 계속합니다. Iometer 테스트를 실행하려면 **각** 대상 볼륨에 대해 최소 세 개의 작업자 스레드를 사용합니다. 각 작업자 스레드의 경우 해당 테스트 시나리오를 실행하도록 아래 표에 표시된 것처럼 대상 볼륨을 선택하고 큐 크기를 설정하고 저장된 테스트 사양 중 하나를 선택합니다. 또한 표는 이러한 테스트를 실행할 때 IOPS 및 처리량에 대한 예상된 결과를 보여 줍니다. 모든 시나리오의 경우 8KB의 작은 IO 크기 및 128의 높은 큐 크기가 사용됩니다.

| 테스트 시나리오 | 대상 볼륨 | 이름 | 결과 |
| --- | --- | --- | --- |
| 최대 읽기 IOPS |CacheReads |RandomWrites\_8K |50,000 IOPS  |
| 최대 쓰기 IOPS |NoCacheWrites |RandomReads\_8K |64,000 IOPS |
| 최대 결합된 IOPS |CacheReads |RandomWrites\_8K |100,000 IOPS |
| NoCacheWrites |RandomReads\_8K | &nbsp; | &nbsp; |
| 최대 읽기 MB/초 |CacheReads |RandomWrites\_64K |524MB/초 |
| 최대 쓰기 MB/초 |NoCacheWrites |RandomReads\_64K |524MB/초 |
| 결합된 MB/초 |CacheReads |RandomWrites\_64K |1000MB/초 |
| NoCacheWrites |RandomReads\_64K | &nbsp; | &nbsp; |

아래는 결합된 IOPS 및 처리량 시나리오에 대한 Iometer 테스트 결과의 스크린샷입니다.

#### <a name="combined-reads-and-writes-maximum-iops"></a>읽기 및 쓰기 최대 IOPS를 결합

![읽기 및 쓰기 최대 IOPS를 결합](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>읽기 및 쓰기 최대 처리량을 결합

![읽기 및 쓰기 최대 처리량을 결합](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

FIO는 Linux VM의 벤치마크 저장소에 널리 사용되는 도구입니다. 다른 IO 크기, 순차 또는 임의 읽기 및 쓰기를 선택하는 유연성을 가집니다. 지정된 I/O 작업을 수행하는 작업자 스레드 또는 프로세스를 생성합니다. 각 작업자 스레드가 작업 파일을 사용하여 수행해야 하는 I/O 작업의 유형을 지정할 수 있습니다. 아래 예에 나와 있는 시나리오 당 하나의 작업 파일을 만들었습니다. Premium Storage에서 실행되는 다른 작업을 벤치마크하도록 이러한 작업 파일의 사양을 변경할 수 있습니다. 예제에서 **Ubuntu**를 실행하는 Standard DS 14 VM을 사용하고 있습니다. 벤치마킹 테스트를 실행 하기 전에 캐시 웜 및 벤치마킹 섹션의 시작 부분에서 설명한 동일한 설치를 사용 합니다.

시작하기 전에 [FIO를 다운로드](https://github.com/axboe/fio) 하고 가상 머신에 설치합니다.

Ubuntu에 대해 다음 명령을 실행합니다.

```
apt-get install fio
```

디스크에서 쓰기 작업 구동에 대해 4개의 작업자 스레드를, 읽기 작업 구동에 대해 4개의 작업자 스레드를 사용합니다. 쓰기 작업자는 “None”으로 설정된 캐시와 10개의 디스크가 있는 “nocache” 볼륨의 트래픽으로 구동됩니다. 읽기 작업자는 “ReadOnly”로 설정된 캐시와 1개의 디스크가 있는 “readcache” 볼륨의 트래픽으로 구동됩니다.

#### <a name="maximum-write-iops"></a>최대 쓰기 IOPS

 최대 쓰기 IOPS를 얻으려면 다음 사양을 가진 작업 파일을 만듭니다. “fiowrite.ini”로 이름을 지정합니다.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

이전 섹션에서 설명한 설계 지침을 따라 핵심 사항을 기록해 둡니다. 이러한 사양은 최대 IOPS 구동에 필수적입니다.  

* 256의 높은 큐 크기.  
* 8KB의 작은 블록 크기  
* 임의 쓰기를 수행하는 다중 스레드.

다음 명령을 실행하여 30초 동안 FIO 테스트를 시작합니다.  

```
sudo fio --runtime 30 fiowrite.ini
```

테스트가 실행되는 동안 VM 및 프리미엄 디스크가 제공하는 쓰기 IOPS 수를 볼 수 있습니다. 아래 예제처럼 DS14 VM은 50,000 IOPS의 해당 최대 쓰기 IOPS 제한을 제공합니다.  
    ![VM 및 프리미엄 디스크가 제공하는 쓰기 IOPS 수](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>최대 읽기 IOPS

 최대 읽기 IOPS를 얻으려면 다음 사양을 가진 작업 파일을 만듭니다. "fioread.ini"로 이름을 지정합니다.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

이전 섹션에서 설명한 설계 지침을 따라 핵심 사항을 기록해 둡니다. 이러한 사양은 최대 IOPS 구동에 필수적입니다.

* 256의 높은 큐 크기.  
* 8KB의 작은 블록 크기  
* 임의 쓰기를 수행하는 다중 스레드.

다음 명령을 실행하여 30초 동안 FIO 테스트를 시작합니다.

```
sudo fio --runtime 30 fioread.ini
```

테스트가 실행되는 동안 VM 및 프리미엄 디스크가 제공하는 읽기 IOPS 수를 볼 수 있습니다. 아래 예제처럼 DS14 VM은 64,000 읽기 IOPS보다 많이 제공합니다. 이는 디스크와 캐시 성능의 조합입니다.  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>최대 읽기 및 쓰기 IOPS

 결합된 최대 읽기 및 쓰기 IOPS를 얻으려면 다음과 같은 사양의 작업 파일을 만듭니다. "fioreadwrite.ini"로 이름을 지정합니다.

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

이전 섹션에서 설명한 설계 지침을 따라 핵심 사항을 기록해 둡니다. 이러한 사양은 최대 IOPS 구동에 필수적입니다.

* 128의 높은 큐 크기  
* 4KB의 작은 블록 크기  
* 임의 읽기 및 쓰기를 수행하는 다중 스레드

다음 명령을 실행하여 30초 동안 FIO 테스트를 시작합니다.

```
sudo fio --runtime 30 fioreadwrite.ini
```

테스트가 실행되는 동안 VM 및 프리미엄 디스크가 제공하는 결합된 읽기 및 쓰기 IOPS 수를 볼 수 있습니다. 아래 예제처럼 DS14 VM은 결합된 읽기 및 쓰기 IOPS를 100,000 보다 많이 제공합니다. 이는 디스크와 캐시 성능의 조합입니다.  
    ![결합된 읽기 및 쓰기 IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>결합된 최대 처리량

 결합된 최대 읽기 및 쓰기 처리량을 얻으려면 읽기 및 쓰기를 수행하는 다중 스레드로 더 큰 블록 크기 및 큰 큐 크기를 사용합니다. 64KB의 블록 크기와 128의 큐 깊이를 사용할 수 있습니다.
