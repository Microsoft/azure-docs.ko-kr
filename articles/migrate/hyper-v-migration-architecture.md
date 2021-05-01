---
title: Azure Migrate에서 Hyper-V 마이그레이션이 어떻게 작동하나요?
description: Azure Migrate를 통한 Hyper-V 마이그레이션에 대해 알아보기
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 239918cc19eefbef9e3c3f12d5ddd3bb5434b490
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96751022"
---
# <a name="how-does-hyper-v-replication-work"></a>Hyper-V 복제 작동 방식

이 문서에서는 Azure Migrate Server Migration 도구를 사용하여 Hyper-V VM을 마이그레이션할 경우에 사용되는 아키텍처와 프로세스에 대한 개요를 제공합니다.

[Azure Migrate](migrate-services-overview.md)는 온-프레미스 앱과 워크로드 및 프라이빗/퍼블릭 클라우드 VM의 검색, 평가 및 Azure로의 마이그레이션을 추적할 수 있는 중앙 허브를 제공합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure Migrate 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다.

## <a name="agentless-migration"></a>에이전트 없는 마이그레이션

Azure Migrate Server Migration 도구는 Hyper-V에 최적화된 마이그레이션 워크플로를 사용하여 온-프레미스 Hyper-V VM에 에이전트 없는 복제를 제공합니다. 소프트웨어 에이전트는 Hyper-V 호스트나 클러스터 노드에만 설치하며, Hyper-V VM에는 아무 것도 설치할 필요가 없습니다.

## <a name="server-migration-and-azure-site-recovery"></a>서버 마이그레이션 및 Azure Site Recovery

Azure Migrate Server Migration은 온-프레미스 워크로드 및 클라우드 기반 VM을 Azure로 마이그레이션하기 위한 도구입니다. Site Recovery는 재해 복구 도구입니다. 이 도구는 데이터 복제에 사용되는 몇 가지 일반적인 기술 구성 요소를 공유하지만 다른 용도로 사용됩니다. 


## <a name="architectural-components"></a>아키텍처 구성 요소

![다이어그램은 Microsoft Azure에 연결하는 H T T P S 데이터 채널이 있는 원본 Hyper-V 네트워크를 보여 줍니다. 자세한 내용은 표에서 설명합니다.](./media/hyper-v-replication-architecture/architecture.png)



**구성 요소** | **배포** | 
--- | --- 
**복제본 공급자** | Microsoft Azure Site Recovery 공급자는 Hyper-V 호스트에 설치되고 Azure Migration Server Migration에 등록됩니다.<br/> 공급자는 Hyper-V VM에 대해 복제를 오케스트레이션합니다.
**Recovery Services 에이전트** | Microsoft Azure Recovery Service 에이전트는 데이터 복제를 처리합니다. 이는 공급자와 함께 작동하여 Hyper-V VM에서 Azure로 데이터를 복제합니다.<br/> 복제된 데이터는 Azure 구독의 스토리지 계정에 업로드됩니다. 서버 마이그레이션 도구는 복제된 데이터를 처리하고 구독의 복제본 디스크에 적용합니다. 복제본 디스크는 마이그레이션할 때 Azure VM을 만드는 데 사용됩니다.

- 구성 요소는 포털의 Azure Migrate Server Migration에서 다운로드되는 단일 설치 파일로 설치됩니다.
- 공급자 및 어플라이언스는 아웃바운드 HTTPS 포트 443 연결을 사용하여 Azure Migrate Server Migration과 통신합니다.
- 공급자 및 에이전트로부터의 통신은 모두 보안이 유지되며 암호화됩니다.


## <a name="replication-process"></a>복제 프로세스

1. Hyper-V VM에 복제를 사용하도록 설정하면 초기 복제가 시작됩니다.
2. Hyper-V VM 스냅샷이 생성됩니다.
3. VM의 VHD는 모두 Azure에 복사될 때까지 하나씩 복제됩니다. 초기 복제 시간은 VM 크기 및 네트워크 대역폭에 따라 달라집니다.
4. 초기 복제 중에 발생하는 디스크 변경 내용은 Hyper-V 복제본을 사용하여 추적되고 로그 파일(hrl 파일)에 저장됩니다.
    - 로그 파일은 디스크와 동일한 폴더에 있습니다.
    - 각 디스크에는 보조 스토리지로 전송되는 hrl 파일이 연결되어 있습니다.
    - 초기 복제 진행 중에는 스냅샷과 로그 파일이 디스크 리소스를 사용합니다.
4. 초기 복제가 완료되면 VM 스냅샷은 삭제되고 델타 복제가 시작됩니다.
5. 증분 디스크 변경 내용은 hrl 파일에서 추적됩니다. 복제 로그는 Recovery Services 에이전트가 Azure Storage 계정에 주기적으로 업로드합니다.


## <a name="performance-and-scaling"></a>성능 및 크기 조정

Hyper-V에 대한 복제 성능은 VM 크기, VM의 데이터 변경률(이탈), 로그 파일 스토리지에 대한 Hyper-V 호스트의 사용 가능한 공간, 복제 데이터에 대한 업로드 대역폭 및 Azure의 대상 스토리지를 비롯한 여러 요인의 영향을 받습니다.

- 동시에 여러 컴퓨터를 복제하는 경우 Hyper-V에 대한 [Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md)를 사용하여 복제를 최적화합니다.
- Hyper-V 복제를 계획하고 용량에 따라 Azure Storage 계정을 통해 복제를 분산합니다.

### <a name="control-upload-throughput"></a>업로드 처리량 제어

각 Hyper-V 호스트에서 Azure로 데이터를 업로드하는 데 사용되는 대역폭의 양을 제한할 수 있습니다. 이 경우 주의가 필요합니다. 값을 너무 낮게 설정하면 복제에 부정적인 영향을 주고 마이그레이션을 지연시킵니다.


1. Hyper-V 호스트 또는 클러스터 노드에 로그인합니다.
2. **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc** 를 실행하여 Windows Azure Backup MMC 스냅인을 엽니다.
3. 스냅인에서 **속성 변경** 을 선택합니다.
4. **대역폭 제한** 에서 **백업 작업에 인터넷 대역폭 사용량 제한 사용** 을 선택합니다. 작업 시간 및 비 작업 시간의 제한을 설정합니다. 유효 범위는 512Kbps~1,023Mbps입니다.
I

### <a name="influence-upload-efficiency"></a>업로드 효율성에 영향을 미침

복제에 대한 여분의 대역폭이 있고 업로드를 늘리려면 업로드 작업에 할당된 스레드 수를 다음과 같이 늘릴 수 있습니다.

1. Regedit를 사용하여 레지스트리를 엽니다.
2. HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM 키로 이동합니다.
3. 복제하는 각 VM에 대한 데이터 업로드에 사용되는 스레드 수의 값을 늘립니다. 기본값은 4이고 최대값은 32입니다. 




## <a name="next-steps"></a>다음 단계

Azure Migrate Server Migration을 사용하여 [Hyper-V 마이그레이션](tutorial-migrate-hyper-v.md)을 체험해 보세요.
