---
title: Azure NetApp Files에 대한 SMB 성능 Faq | Microsoft Docs
description: Azure NetApp Files의 SMB 성능에 대해 자주 묻는 질문과 대답입니다.
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
ms.date: 01/14/2020
ms.author: b-juche
ms.openlocfilehash: 6391a3eeead6a52371c11011a65f4b4de7260156
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046423"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Azure NetApp Files의 SMB 성능에 대한 Faq

이 문서에서는 Azure NetApp Files에 대한 SMB 성능 모범 사례에 대한 Faq (질문과 대답)에 답변 합니다.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Smb 공유에서 smb 다중 채널을 사용 하나요? 

예, SMB 다중 채널은 기본적으로 사용 하도록 설정 되며, 2020 년 1 월 1 일에 변경 내용이 배치 됩니다. 기존 SMB 볼륨을 미리 청구 하는 모든 SMB 공유에는 기능이 사용 하도록 설정 되어 있으며 새로 만든 모든 볼륨은 생성 시 기능을 사용할 수 있습니다. 

기능을 사용 하기 전에 설정 된 모든 SMB 연결을 다시 설정 하 여 SMB 다중 채널 기능을 활용 해야 합니다. 다시 설정 하려면 SMB 공유의 연결을 끊고 다시 연결할 수 있습니다.

## <a name="is-rss-supported"></a>RSS가 지원 되나요?

예, Azure NetApp Files는 RSS (수신측 배율)를 지원 합니다.

SMB 다중 채널을 사용 하도록 설정 하면 SMB3 client는 단일 RSS를 지 원하는 NIC (네트워크 인터페이스 카드)를 통해 Azure NetApp Files SMB 서버에 여러 TCP 연결을 설정 합니다. 

## <a name="which-windows-versions-support-smb-multichannel"></a>어떤 Windows 버전에서 SMB 다중 채널을 지원 하나요?

Windows는 최상의 성능을 사용할 수 있도록 Windows 2012 이후 SMB 다중 채널을 지원 합니다.  자세한 내용은 [Smb 다중 채널 배포](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) 및 [smb 다중 채널의 기본 사항](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/) 을 참조 하세요. 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Azure virtual machine에서 RSS를 지원 하나요?

Azure 가상 컴퓨터 Nic에서 RSS를 지원 하는지 확인 하려면 다음과 같이 명령 `Get-SmbClientNetworkInterface`를 실행 하 고 `RSS Capable`필드를 확인 합니다. 

![Azure 가상 컴퓨터에 대한 RSS 지원](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>SMB 다이렉트를 지원할 Azure NetApp Files 있나요?

아니요, Azure NetApp Files SMB 다이렉트를 지원 하지 않습니다. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>SMB 다중 채널의 혜택은 무엇 인가요? 

SMB 다중 채널 기능을 사용 하면 SMB3 클라이언트에서 단일 NIC (네트워크 인터페이스 카드) 또는 여러 Nic를 통해 연결 풀을 설정 하 고이를 사용 하 여 단일 SMB 세션에 대한 요청을 보낼 수 있습니다. 반면, SMB1 및 SMB2는 기본적으로 클라이언트에서 하나의 연결을 설정 하 고 해당 연결을 통해 지정 된 세션에 대한 모든 SMB 트래픽을 전송 해야 합니다. 이 단일 연결은 단일 클라이언트에서 달성할 수 있는 전체 프로토콜 성능을 제한 합니다.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>SMB 용 클라이언트에서 여러 Nic를 구성 해야 하나요?

아닙니다. Smb 클라이언트는 SMB 서버에서 반환 되는 NIC 수와 일치 합니다.  각 저장소 볼륨은 하나의 저장소 엔드포인트에서 액세스할 수 있습니다.  즉, 지정 된 SMB 관계에 하나의 NIC만 사용 됩니다.  

아래 `Get-SmbClientNetworkInterace`의 출력에서 볼 때 가상 머신에는 두 개의 네트워크 인터페이스인-15와 12가 있습니다.  아래 명령 `Get-SmbMultichannelConnection`아래에 표시 된 것 처럼 두 개의 RSS 지원 NIC가 있어도 SMB 공유와의 연결에는 인터페이스 12만 사용 됩니다. 인터페이스 15를 사용 하 고 있지 않습니다.

![RSS 지원 NIC](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Azure에서 NIC 팀이 지원 되나요?

NIC 팀은 Azure에서 지원 되지 않습니다. 여러 네트워크 인터페이스가 Azure virtual machines에서 지원 되기는 하지만 실제 구문이 아니라 논리를 나타냅니다. 따라서 내결함성을 제공 하지 않습니다.  또한 Azure 가상 컴퓨터에 사용할 수 있는 대역폭은 개별 네트워크 인터페이스가 아니라 컴퓨터 자체에 대해서만 계산 됩니다.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>SMB 다중 채널과 같은 성능은 어떻습니까?

다음 테스트 및 그래프는 단일 인스턴스 작업에서 SMB 다중 채널의 기능을 보여 줍니다.

### <a name="random-io"></a>임의 i/o  

클라이언트에서 SMB 다중 채널을 사용 하지 않도록 설정 하면 FIO 및 40 GiB 작업 집합을 사용 하 여 순수 8 KiB 읽기 및 쓰기 테스트를 수행 했습니다.  `1`,`4`,`8`,`16`, `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>`의 RSS 네트워크 인터페이스 설정 당 SMB 클라이언트 연결 수를 증가 시켜 각 테스트 간에 SMB 공유가 분리 되었습니다. 테스트는 `4`의 기본 설정이 i/o를 많이 사용 하는 워크 로드에 충분 함을 보여 줍니다. `8` 및 `16`에 대한 증가는 영향을 주지 않습니다. 

명령 `netstat -na | findstr 445` `1`에서 `4` `8` 및 `16`까지 추가 연결이 설정 되었음을 증명 했습니다.  Perfmon `Per Processor Network Activity Cycles` 통계 (이 문서에 포함 되지 않음)가 확인 하는 것 처럼 각 테스트 중에 4 개의 CPU 코어가 SMB에 대해 완전히 활용 되었습니다.

![임의 i/o 테스트](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Azure 가상 머신은 SMB (또는 NFS) 저장소 i/o 제한에 영향을 주지 않습니다.  아래 표시 된 것 처럼 D16 인스턴스 유형은 캐시 된 저장소 IOPS의 경우 32000, 캐시 되지 않은 저장소 IOPS의 경우 25600입니다.  그러나 위의 그래프는 SMB를 통해 훨씬 더 많은 i/o를 보여 줍니다.

![임의 i/o 비교](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>순차 IO 

위에서 설명한 임의 i/o 테스트와 유사한 테스트는 64-KiB 순차 i/o를 사용 하 여 수행 되었습니다. 4 개 이상의 RSS 네트워크 인터페이스로 클라이언트 연결 수를 늘리는 것은 임의 i/o에 뚜렷한 영향을 주지 않지만 순차적 i/o에는 동일 하 게 적용 되지 않습니다. 다음 그래프에서 볼 수 있듯이 각 증가는 읽기 처리량의 해당 증가와 연결 됩니다. 각 인스턴스 형식/크기에 대해 Azure에 의해 배치 된 네트워크 대역폭 제한으로 인해 쓰기 처리량이 플랫으로 유지 되었습니다. 

![순차 i/o 테스트](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure는 각 가상 머신 유형/크기에 대해 네트워크 요금 제한을 배치 합니다. 요금 제한은 아웃 바운드 트래픽에만 적용 됩니다. 가상 컴퓨터에 있는 Nic의 수는 컴퓨터에 사용할 수 있는 총 대역폭 크기와는 관련이 없습니다.  예를 들어 D16 인스턴스 유형은 네트워크 제한이 8000 Mbps (1000 MiB/s)로 부과 됩니다.  위의 순차 그래프에서 볼 수 있듯이 제한은 아웃 바운드 트래픽 (쓰기)에 영향을 주지만 다중 채널 읽기는 영향을 받지 않습니다.

![순차 i/o 비교](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-advanced-networking-recommended"></a>고급 네트워킹을 권장 하나요?

최대 성능을 위해 가능한 경우 고급 네트워킹을 구성 하는 것이 좋습니다. 다음 사항을 염두에 두어야 합니다.  

* Azure Portal이 기능을 지 원하는 가상 컴퓨터에 대해 기본적으로 고급 네트워킹을 사용 하도록 설정 합니다.  그러나 Ansible 및 유사한 구성 도구와 같은 다른 배포 방법은 그렇지 않을 수 있습니다.  고급 네트워킹을 사용 하지 않으면 컴퓨터의 성능이 hobble 수 있습니다.  
* 인스턴스 유형 또는 크기에 대한 지원이 부족 하 여 가상 머신의 네트워크 인터페이스에서 고급 네트워킹을 사용 하지 않는 경우 더 큰 인스턴스 유형을 사용 하 여 사용 하지 않도록 설정 된 상태로 유지 됩니다. 이러한 경우 수동 작업을 수행 해야 합니다.

## <a name="are-jumbo-frames-supported"></a>점보 프레임은 지원 되나요?

점보 프레임은 Azure virtual machines에서 지원 되지 않습니다.

## <a name="is-smb-signing-supported"></a>SMB 서명이 지원 되나요? 

SMB 프로토콜은 파일 및 인쇄 공유 및 원격 Windows 관리와 같은 기타 네트워킹 작업을 위한 기반을 제공 합니다. 전송 중인 SMB 패킷을 수정하는 중간자(man-in-the-middle) 공격을 방지하기 위해 SMB 프로토콜은 SMB 패킷의 디지털 서명을 지원합니다. 

SMB 서명은 Azure NetApp Files에서 지 원하는 모든 SMB 프로토콜 버전에 대해 지원 됩니다. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>SMB 서명의 성능 영향은 무엇 인가요?  

Smb 서명은 SMB 성능에 영향을 주지 않습니다. 성능 저하가 발생할 수 있는 다른 원인 중에서 각 패킷의 디지털 서명은 아래에 표시 된 perfmon 출력과 함께 추가 클라이언트 쪽 CPU를 사용 합니다. 이 경우 SMB 서명을 포함 하 여 SMB를 담당 하는 Core 0이 표시 됩니다.  이전 섹션에서 비 다중 채널 순차 읽기 처리량 번호와 비교 하는 경우 SMB 서명이 875MiB/s에서 약 250MiB/s로 전체 처리량을 줄이는 것을 보여 줍니다. 

![SMB 서명 성능 영향](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>다음 단계  

- [Azure NetApp Files에 대한 Faq](azure-netapp-files-faqs.md)
- Azure NetApp Files에서 SMB 파일 공유를 사용 하는 방법에 대한 [Smb 작업을 위한 관리 되는 Enterprise 파일 공유 Azure NetApp Files](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) 를 참조 하세요.