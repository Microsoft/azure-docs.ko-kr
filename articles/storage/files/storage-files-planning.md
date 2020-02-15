---
title: Azure Files 배포에 대한 계획 | Microsoft Docs
description: Azure Files 배포를 계획할 때 고려할 사항을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 203bf584711fbfcfd0baeee8f5e4c7f70d96823b
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157223"
---
# <a name="planning-for-an-azure-files-deployment"></a>Azure Files 배포에 대한 계획

[Azure Files](storage-files-introduction.md)는 산업 표준 SMB 프로토콜을 통해 액세스할 수 있는, 클라우드에서 완전히 관리되는 파일 공유를 제공합니다. Azure Files은 완벽하게 관리되기 때문에 프로덕션 시나리오에서 이를 배포하면 파일 서버 또는 NAS 디바이스를 훨씬 쉽게 배포하고 관리할 수 있습니다. 이 문서에서는 조직 내에서 프로덕션 용도로 Azure 파일 공유를 배포할 때 고려해야 할 항목을 다룹니다.

## <a name="management-concepts"></a>관리 개념

 다음 다이어그램에서는 Azure Files 관리 구조를 보여 줍니다.

![파일 구조](./media/storage-files-introduction/files-concepts.png)

* **Storage 계정**: Azure Storage에 대한 모든 액세스는 Storage 계정을 통해 수행됩니다. 저장소 계정 용량에 대 한 자세한 내용은 [standard storage 계정에 대 한 확장성 및 성능 목표](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) 를 참조 하세요.

* **공유**: File Storage 공유는 Azure의 SMB 파일 공유입니다. 모든 디렉터리 및 파일을 부모 공유에 만들어야 합니다. 계정에는 공유를 무제한 포함할 수 있으며, 공유는 파일 공유의 총 용량까지 파일 수에 제한 없이 저장할 수 있습니다. 프리미엄 및 표준 파일 공유에 대 한 총 용량은 100 TiB입니다.

* **디렉터리**: 선택적인 디렉터리 계층 구조입니다.

* **파일**: 공유에 있는 파일입니다. 파일의 크기는 최대 1TiB일 수 있습니다.

* **URL 형식**: 파일 REST 프로토콜을 사용하여 Azure 파일 공유에 적용된 요청의 경우, 다음 URL 형식을 사용하여 파일의 주소를 지정할 수 있습니다.

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>데이터 액세스 방법

Azure Files는 두 가지의 편리한 데이터 액세스 방법을 기본 제공하며, 이 두 가지 방법을 따로 또는 서로 조합하여 데이터에 액세스할 수 있습니다.

1. **직접 클라우드 액세스**: Azure 파일 공유는 업계 표준 서버 메시지 블록(SMB) 프로토콜 또는 파일 REST API를 통해 [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) 및/또는 [Linux](storage-how-to-use-files-linux.md)에 의해 탑재될 수 있습니다. SMB를 사용하면 공유되는 파일의 읽기 및 쓰기는 Azure에서 파일 공유에서 직접 수행됩니다. Azure에서 VM으로 탑재하려면 운영 체제의 SMB 클라이언트가 SMB 2.1 이상을 지원해야 합니다. 사용자의 워크스테이션과 같은 온-프레미스를 탑재하려면 워크스테이션이 지원하는 SMB 클라이언트는 SMB 3.0 이상(암호화 사용)을 지원해야 합니다. SMB 외에도 새로운 애플리케이션 또는 서비스는 소프트웨어 개발을 위해 쉽고 확장 가능한 애플리케이션 인터페이스를 제공하는 파일 REST를 통해 파일 공유에 직접 액세스할 수 있습니다.
2. **Azure 파일 동기화**: Azure 파일 동기화를 사용하면 공유를 Windows 서버 온-프레미스 또는 Azure에 복제할 수 있습니다. 사용자는 Windows Server를 통해(예: SMB 또는 NFS 공유를 통해) 파일 공유에 액세스하게 됩니다. 이는 지사와 같이 Azure 데이터 센터에서 멀리 떨어진 곳에서 데이터에 액세스하고 수정하는 시나리오에 유용합니다. 여러 Windows Server 엔드포인트 간(예: 여러 지사 간)에 데이터가 복제될 수 있습니다. 마지막으로, 모든 데이터를 여전히 서버를 통해 액세스할 수 있지만 서버에는 데이터의 전체 복사본이 없도록 데이터는 Azure Files에 계층화될 수 있습니다. 오히려 사용자가 열 때 데이터 원활하게 호출됩니다.

다음 표는 사용자와 애플리케이션이 Azure 파일 공유에 액세스할 수 있는 방법을 보여줍니다.

| | 직접 클라우드 액세스 | Azure 파일 동기화 |
|------------------------|------------|-----------------|
| 어떤 프로토콜을 사용해야 합니까? | Azure Files는 SMB 2.1, SMB 3.0 및 파일 REST API를 지원합니다. | Windows Server(SMB, NFS, FTPS, 등)에서 지원되는 프로토콜을 통해 Azure 파일 공유 액세스 |  
| 어디에서 워크로드를 실행합니까? | **Azure에서**: Azure Files는 데이터에 직접 액세스할 수 있습니다. | **느린 네트워크의 온-프레미스**: Windows, Linux 및 macOS 클라이언트는 Azure 파일 공유의 빠른 캐시로서 로컬 온-프레미스 Windows 파일 공유를 탑재할 수 있습니다. |
| ACL의 어떤 수준이 필요합니까? | 공유 및 파일 수준입니다. | 공유, 파일 및 사용자 수준입니다. |

## <a name="data-security"></a>데이터 보안

Azure Files에는 데이터 보안을 위한 몇 가지 기본 제공 옵션이 있습니다.

* 두 네트워크 상 프로토콜에서 암호화 지원: SMB 3.0 암호화 및 HTTPS를 통한 파일 REST입니다. 기본적으로: 
    * SMB 3.0 암호화를 지 원하는 클라이언트는 암호화 된 채널을 통해 데이터를 보내고 받습니다.
    * 암호화를 사용 하 여 SMB 3.0을 지원 하지 않는 클라이언트는 암호화 되지 않은 smb 2.1 또는 SMB 3.0를 통해 데이터 센터 내에서 통신할 수 있습니다. SMB 클라이언트는 암호화 기능이 없는 SMB 3.0 또는 SMB 2.1을 통해 데이터 센터 내에서 통신할 수 없습니다.
    * 클라이언트는 HTTP 또는 HTTPS를 사용하여 파일 REST를 통해 통신할 수 있습니다.
* 미사용 데이터 암호화([Azure Storage 서비스 암호화](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): SSE(스토리지 서비스 암호화)는 모든 스토리지 계정에 대해 사용되도록 설정됩니다. 미사용 데이터는 완전히 관리되는 키로 암호화됩니다. 미사용 암호화를 사용할 경우 스토리지 비용이 증가하거나 성능이 저하되지 않습니다. 
* 암호화 된 데이터 전송 시 선택적 요구 사항:이 옵션을 선택 하면 암호화 되지 않은 채널을 통해 데이터에 대 한 액세스를 거부 Azure Files. 구체적으로 말하면, 암호화 연결을 통한 HTTPS 및 SMB 3.0만 허용됩니다.

    > [!Important]  
    > 데이터의 보안 전송이 필요한 경우 암호화된 SMB 3.0과 통신할 수 없는 이전 버전의 SMB 클라이언트는 실패합니다. 자세한 내용은 [Windows에 탑재](storage-how-to-use-files-windows.md), [Linux에 탑재](storage-how-to-use-files-linux.md) 및 [macOS에 탑재](storage-how-to-use-files-mac.md)를 참조하세요.

최상의 보안을 위해 최신 클라이언트를 사용하여 데이터에 액세스할 때마다 미사용 시 암호화와 전송 시 데이터 암호화 모두를 항상 사용하는 것이 좋습니다. 예를 들어, SMB 2.1만 지원하는 Windows Server 2008 R2 VM에 공유를 탑재하는 경우 SMB 2.1이 암호화를 지원하지 않기 때문에 스토리지 계정에 암호화되지 않은 트래픽을 허용해야 합니다.

Azure 파일 공유에 액세스하기 위해 Azure 파일 동기화를 사용하는 경우, 미사용 시 데이터 암호화 필요 여부에 관계 없이 항상 암호화된 HTTPS 및 SMB 3.0을 사용하여 데이터를 Windows 서버에 동기화합니다.

## <a name="file-share-performance-tiers"></a>파일 공유 성능 계층

Azure Files는 standard 및 premium의 두 가지 성능 계층을 제공 합니다.

### <a name="standard-file-shares"></a>표준 파일 공유

표준 파일 공유는 Hdd (하드 디스크 드라이브)로 지원 됩니다. 표준 파일 공유는 일반적인 용도의 파일 공유 및 개발/테스트 환경과 같은 성능 변동에 대 한 낮은 수준의 IO 작업 부하에 대 한 안정적인 성능을 제공 합니다. 종량제 청구 모델에서만 제공됩니다.

> [!IMPORTANT]
> 5 TiB 보다 큰 파일 공유를 사용 하려면 온보드 단계와 지역 가용성 및 제한 사항에 대해 [더 큰 파일 공유에 등록 (표준 계층)](#onboard-to-larger-file-shares-standard-tier) 섹션을 참조 하세요.

### <a name="premium-file-shares"></a>프리미엄 파일 공유

프리미엄 파일 공유는 Ssd (반도체 드라이브)에서 지원 됩니다. 프리미엄 파일 공유는 IO 집약적 워크 로드에 대해 대부분의 IO 작업에 대해 단일 자릿수 밀리초 내에 일관 된 고성능 및 짧은 대기 시간을 제공 합니다. 이를 통해 데이터베이스, 웹 사이트 호스팅, 개발 환경 등 다양 한 워크 로드에 적합 합니다. 프리미엄 파일 공유는 프로비전된 청구 모델에서만 제공됩니다. 프리미엄 파일 공유는 표준 파일 공유와는 별도의 배포 모델을 사용 합니다.

Azure Backup는 프리미엄 파일 공유에 사용할 수 있으며 Azure Kubernetes Service는 1.13 이상 버전에서 프리미엄 파일 공유를 지원 합니다.

프리미엄 파일 공유를 만드는 방법을 알아보려면 [Azure premium file storage 계정을 만드는 방법](storage-how-to-create-premium-fileshare.md)에 대 한 문서를 참조 하세요.

현재 표준 파일 공유와 프리미엄 파일 공유 사이에서 직접 변환할 수 없습니다. 두 계층으로 전환 하려는 경우 해당 계층에서 새 파일 공유를 만들고 원래 공유에서 만든 새 공유로 데이터를 수동으로 복사 해야 합니다. Azure Files 지원 되는 복사 도구 (예: Robocopy 또는 AzCopy)를 사용 하 여이 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 프리미엄 파일 공유는 저장소 계정을 제공 하는 대부분의 지역에서 더 작은 하위 집합의 ZRS로 LRS와 함께 사용할 수 있습니다. 현재 지역에서 프리미엄 파일 공유를 사용할 수 있는지 확인 하려면 Azure에 대 한 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=storage) 페이지를 참조 하세요. ZRS를 지 원하는 지역에 대 한 자세한 내용은 [중복성 Azure Storage](../common/storage-redundancy.md)를 참조 하세요.
>
> 새 지역 및 프리미엄 계층 기능의 우선 순위를 지정 하는 데 도움이 되도록이 [설문 조사](https://aka.ms/pfsfeedback)를 작성해 주세요.

#### <a name="provisioned-shares"></a>프로비전된 공유

프리미엄 파일 공유는 고정 GiB/IOPS/처리량 비율을 기준으로 프로비전됩니다. 공유에 프로비전되는 각 GiB당 IOPS 1개, 초당 0.1MiB의 처리량이 공유당 최대 한도까지 지급됩니다. 허용되는 최소 프로비저닝 용량은 100GiB(최소 IOPS/처리량)입니다.

최상의 노력 원칙에 따라 모든 공유는 60분 이상의 기간 동안(공유 크기별로 다름) 프로비전된 스토리지 용량(GiB)당 IOPS를 3개까지 버스트할 수 있습니다. 새 공유에는 프로비전된 용량을 기준으로 전체 버스트 크레딧이 초기 제공됩니다.

공유는 1 GiB 증분 단위로 프로 비전 되어야 합니다. 최소 크기는 100 GiB, 다음 크기는 101 GiB 등입니다.

> [!TIP]
> 기준 IOPS = 1 * 프로 비전 된 GiB. (최대 10만 IOPS).
>
> 버스트 제한 = 3 * 기준선 IOPS. (최대 10만 IOPS).
>
> 송신 율 = 60 MiB/s + 0.06 * 프로 비전 된 GiB
>
> 수신 율 = 40 MiB/s + 0.04 * 프로 비전 된 GiB

프로 비전 된 공유 크기는 공유 할당량에 의해 지정 됩니다. 공유 할당량은 언제 든 지 늘릴 수 있지만 마지막 증가 이후 24 시간 후에만 감소할 수 있습니다. 할당량 증가 없이 24 시간 동안 기다린 후에는 다시 늘릴 때까지 공유 할당량을 원하는 횟수 만큼 줄일 수 있습니다. IOPS/처리량 크기 변경은 크기가 변경 된 후 몇 분 내에 적용 됩니다.

사용 된 GiB에서 프로 비전 된 공유의 크기를 줄일 수 있습니다. 이 작업을 수행 하는 경우 데이터가 손실 되지 않지만 사용 된 크기에 대 한 요금이 청구 되며, 사용 되는 크기가 아니라 프로 비전 된 공유의 성능 (기준선 IOPS, 처리량 및 버스트 IOPS)을 수신 하 게 됩니다.

다음 표에서는 프로 비전 된 공유 크기에 대 한 이러한 열의 공식을의 몇 가지 예를 보여 줍니다.

|용량 (GiB) | 기준 IOPS | 버스트 IOPS | 송신 (MiB/s) | 수신 (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | 최대 300     | 66   | 44   |
|500         | 500     | 최대 1500   | 90   | 60   |
|1,024       | 1,024   | 최대 3072   | 122   | 81   |
|5,120       | 5,120   | 최대 15360  | 368   | 245   |
|10,240      | 10,240  | 최대 30720  | 675 | 450   |
|33,792      | 33,792  | 최대 10만 | 2088 | 1,392   |
|51,200      | 51,200  | 최대 10만 | 3,132 | 2088   |
|102,400     | 100,000 | 최대 10만 | 6204 | 4136   |

> [!NOTE]
> 파일 공유 성능에는 컴퓨터 네트워크 제한, 사용 가능한 네트워크 대역폭, IO 크기, 병렬 처리 등 많은 요인이 적용 됩니다. 예를 들어, KiB 읽기/쓰기 IO 크기가 8 인 내부 테스트를 기반으로 SMB를 통한 프리미엄 파일 공유에 연결 된 단일 Windows 가상 머신, *표준 F16s_v2*는 20K 읽기 Iops 및 15K 쓰기 iops를 달성할 수 있습니다. 512 MiB 읽기/쓰기 IO 크기를 사용 하는 경우 동일한 VM이 1.1 GiB/s 송신 및 370 MiB/s 수신 처리량을 달성할 수 있습니다. 최대 성능 확장을 얻으려면 부하를 여러 Vm에 분산 합니다. 몇 가지 일반적인 성능 문제 및 해결 방법에 대해서는 [문제 해결 가이드](storage-troubleshooting-files-performance.md) 를 참조 하세요.

#### <a name="bursting"></a>화

프리미엄 파일 공유는 IOPS를 3 배까지 버스트 할 수 있습니다. 버스트는 자동화 되며 신용 시스템을 기반으로 작동 합니다. 버스트는 최상의 노력을 기반으로 작동 하며 버스트 제한은 보장 되지 않습니다. 파일 공유는 제한 *까지* 버스트 될 수 있습니다.

크레딧은 파일 공유에 대 한 트래픽이 기준선 IOPS 미만이 될 때마다 버스트 버킷에 누적 됩니다. 예를 들어 100 GiB 공유에는 100 기준선 IOPS가 있습니다. 공유의 실제 트래픽이 1 초 간격으로 40 IOPS 인 경우 60 사용 하지 않는 IOPS는 버스트 버킷으로 제공한 됩니다. 이러한 크레딧은 나중에 작업이 기준선 IOPs를 초과 하는 경우에 사용 됩니다.

> [!TIP]
> 버스트 버킷의 크기 = 기준선 IOPS * 2 * 3600.

공유가 기준선 IOPS를 초과 하 고 버스트 버킷에 크레딧을 가질 때마다 버스트 됩니다. 크레딧이 남아 있는 한 공유는 지속 될 수 있지만, 50 TiB 보다 작은 공유는 최대 1 시간 동안 버스트 제한에만 유지 됩니다. 50 보다 큰 공유는 기술적으로 1 시간 제한 (최대 2 시간)을 TiB 수 있지만이는 발생 하는 버스트 크레딧 수를 기준으로 합니다. 기준 IOPS를 초과 하는 각 IO는 하나의 크레딧을 사용 하며, 모든 크레딧이 사용 되 면 공유는 기준선 IOPS로 돌아옵니다.

공유 크레딧에는 세 가지 상태가 있습니다.

- 발생 파일 공유에서 기준선 IOPS 보다 작은 값을 사용 하는 경우입니다.
- 파일 공유가 버스트 인 경우 거절
- 남은 상수 (크레딧을 사용 하지 않거나 기준선 IOPS가 사용 중인 경우)

새 파일 공유는 버스트 버킷의 전체 크레딧 수로 시작 합니다. 서버 제한으로 인해 공유 IOPS가 기준선 IOPS 미만이 면 버스트 크레딧을 계산 하지 않습니다.

## <a name="file-share-redundancy"></a>파일 공유 중복성

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

읽기 액세스 지역 중복 저장소 (RA-GRS)를 옵트인 (opt in) 하는 경우 Azure 파일이 현재 모든 지역에서 읽기 액세스 지역 중복 저장소 (RA-GRS)를 지원 하지 않는다는 것을 알아야 합니다. RA-GRS 저장소 계정의 파일 공유는 GRS 계정에서와 같이 작동 하며 GRS 가격이 청구 됩니다.

> [!Warning]  
> Azure 파일 공유를 GRS 스토리지 계정의 클라우드 엔드포인트로 사용하는 경우 스토리지 계정 장애 조치(failover)를 시작하면 안 됩니다. 이러한 계정을 장애 조치(failover)하면 동기화가 더 이상 진행되지 않고, 새로 계층화된 파일의 경우 예기치 않은 데이터 손실이 발생할 수도 있습니다. Azure 지역이 손실되는 경우 Microsoft는 Azure 파일 동기화와 호환되는 방식으로 스토리지 계정의 장애 조치(failover)를 트리거합니다.

프리미엄 공유 Azure Files LRS와 ZRS를 모두 지원 하며, ZRS는 현재 더 작은 하위 집합에서 사용할 수 있습니다.

## <a name="onboard-to-larger-file-shares-standard-tier"></a>더 큰 파일 공유에 등록 (표준 계층)

이 섹션은 표준 파일 공유에만 적용 됩니다. 모든 프리미엄 파일 공유는 100 TiB 용량으로 제공 됩니다.

### <a name="restrictions"></a>제한

- LRS/ZRS to GRS/GZRS 계정 변환은 대량 파일 공유를 사용 하는 모든 저장소 계정에 사용할 수 없습니다.

### <a name="regional-availability"></a>국가별 가용성

모든 Azure 지역에서 100 TiB 용량 제한을 사용 하는 표준 파일 공유를 전역적으로 사용할 수 있습니다.

- LRS: 남부 아프리카 북부와 남아프리카 서 부를 제외한 모든 지역입니다.
   - 미국 동부 및 유럽 서부: 모든 새 계정이 지원 됩니다. 적은 수의 기존 계정이 업그레이드 프로세스를 완료 하지 않았습니다. 기존 저장소 계정이 [대량 파일 공유를 사용 하도록 설정](storage-files-how-to-create-large-file-share.md)하 여 업그레이드 프로세스를 완료 했는지 여부를 확인할 수 있습니다.

- ZRS: 일본 동부, 북부 유럽, 남아프리카 공화국 북부를 제외한 모든 지역입니다.
- GRS/GZRS: 지원 되지 않습니다.

### <a name="enable-and-create-larger-file-shares"></a>더 큰 파일 공유 사용 및 만들기

큰 파일 공유 사용을 시작 하려면 [큰 파일 공유를 사용 하도록 설정 하 고 만드는 방법](storage-files-how-to-create-large-file-share.md)문서를 참조 하세요.

## <a name="data-growth-pattern"></a>데이터 증가 패턴

현재 Azure 파일 공유의 최대 크기는 100 TiB입니다. 현재 이 제한으로 인해 Azure 파일 공유를 배포할 때 예상되는 데이터 증가를 고려해야 합니다.

Azure File Sync를 사용 하 여 여러 Azure 파일 공유를 단일 Windows 파일 서버에 동기화 할 수 있습니다. 이를 통해 온-프레미스에 있을 수 있는 오래 된 대량 파일 공유를 Azure File Sync으로 가져올 수 있습니다. 자세한 내용은 [Azure File Sync 배포 계획](storage-files-planning.md)을 참조 하세요.

## <a name="data-transfer-method"></a>데이터 전송 방법

온-프레미스 파일 공유와 같은 기존 파일 공유에서 Azure Files로 데이터를 대량으로 쉽게 전송할 수 있는 여러 옵션이 있습니다. 자주 사용되는 옵션 몇 가지는 다음과 같습니다(비 한정적 목록).

* **[Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)** : Azure 파일 공유 ("클라우드 끝점")와 Windows 디렉터리 네임 스페이스 ("서버 끝점") 간의 첫 번째 동기화의 일부로, Azure File Sync는 기존 파일 공유의 모든 데이터를 Azure Files에 복제 합니다.
* **[Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : Azure Import/Export 서비스를 사용하면 하드 디스크 드라이브를 Azure 데이터 센터에 발송하여 많은 양의 데이터를 안전하게 Azure 파일 공유로 전송할 수 있습니다. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** : Robocopy는 Windows 및 Windows Server와 함께 제공되는 잘 알려진 복사 도구입니다. 파일 공유를 로컬로 탑재하고 탑재된 위치를 Robocopy 명령의 대상으로 사용하는 방식으로 Robocopy를 사용하여 데이터를 Azure Files로 전송할 수 있습니다.
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : AzCopy는 간단한 명령과 최적의 성능으로 데이터를 Azure Files 및 Azure Blob Storage에서 복사하도록 디자인된 명령줄 유틸리티입니다.

## <a name="next-steps"></a>다음 단계
* [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)
* [Azure Files 배포](storage-files-deployment-guide.md)
* [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
