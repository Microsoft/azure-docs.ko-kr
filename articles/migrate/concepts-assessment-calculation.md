---
title: Azure Migrate 평가 계산 | Microsoft Docs
description: Azure Migrate 서비스의 평가 계산에 대한 개요를 제공합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 012a352b00de2e2d1bf64fd18125ddd10faba5cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679122"
---
# <a name="assessment-calculations"></a>평가 계산

[Azure Migrate](migrate-overview.md)는 Azure로 마이그레이션하는 온-프레미스 워크로드를 평가합니다. 이 문서에서는 평가를 계산하는 방법에 대한 정보를 제공합니다.


## <a name="overview"></a>개요

Azure Migrate 평가에는 세 가지 단계가 있습니다. 평가는 적합성 분석으로 시작되며 크기 조정, 월별 비용 예측이 차례로 진행됩니다. 컴퓨터는 이전 단계를 통과하는 경우에만 이후 단계로 이동합니다. 예를 들어 컴퓨터가 Azure 적합성 검사에 실패하면 Azure에 적합하지 않은 것으로 표시되고 크기 조정과 비용 예측이 수행되지 않습니다.

## <a name="azure-suitability-analysis"></a>Azure 적합성 분석

클라우드마다 고유한 제한 사항과 요구 사항이 있으므로 일부 시스템은 클라우드에서 실행하는 데 적합하지 않습니다. Azure Migrate는 Azure로 마이그레이션 적합성에 대해 각 온-프레미스 컴퓨터를 평가하고 컴퓨터를 다음 범주 중 하나로 분류합니다.
- **Azure 준비 완료** - 컴퓨터를 변경하지 않고 현재 상태대로 Azure로 마이그레이션할 수 있습니다. Azure를 완벽하게 지원하는 상태로 Azure에서 부팅됩니다.
- **조건부 Azure 준비 완료** - 컴퓨터가 Azure에서 부팅되지만 Azure를 완벽하게 지원하지 않을 수도 있습니다. 예를 들어 이전 버전의 Windows Server OS가 설치된 컴퓨터는 Azure에서 지원되지 않습니다. 이러한 컴퓨터를 Azure로 마이그레이션할 때는 주의해야 하며, 마이그레이션 전에 평가에 제안된 수정 지침에 따라 준비 상태 문제를 해결해야 합니다.
- **Azure를 사용할 준비 안 됨** - 컴퓨터가 Azure에서 부팅되지 않습니다. 예를 들어 온-프레미스 컴퓨터에 크기가 4TB를 초과하는 디스크가 연결되어 있을 경우 Azure에서 호스트할 수 없습니다. Azure로 마이그레이션하기 전에 평가에 제안된 수정 지침에 따라 준비 상태 문제를 해결해야 합니다. Azure를 사용할 준비 안 됨으로 표시된 컴퓨터에 대해서는 정적 크기 조정 및 비용 예측이 수행되지 않습니다.
- **준비 상태 알 수 없음** - vCenter Server에서 사용 가능한 데이터가 부족하여 Azure Migrate가 컴퓨터의 준비 상태를 찾을 수 없습니다.

Azure Migrate는 컴퓨터 속성과 게스트 운영 체제를 검토하여 온-프레미스 컴퓨터의 Azure 준비 상태를 식별합니다.

### <a name="machine-properties"></a>컴퓨터 속성
Azure Migrate는 온-프레미스 VM의 다음 속성을 검토하여 Azure에서 VM을 실행할 수 있는지 여부를 식별합니다.

**속성** | **세부 정보** | **Azure 준비 상태**
--- | --- | ---
**부팅 유형** | Azure는 부팅 유형이 BIOS인 VM을 지원하지만 UEFI인 VM은 지원하지 않습니다. | 부팅 유형이 UEFI인 경우 조건부 준비 완료입니다.
**코어 수** | 컴퓨터의 코어 수는 Azure VM에 대해 지원되는 최대 코어 수(128개 코어) 이하여야 합니다.<br/><br/> 성능 기록을 사용할 수 있으면 Azure Migrate는 사용된 코어 수와 비교합니다. 쾌적 인자가 평가 설정에 지정되어 있는 경우 사용된 코어 수와 쾌적 인자를 곱합니다.<br/><br/> 성능 기록이 없으면 Azure Migrate는 쾌적 인자를 적용하지 않고 할당된 코어 수를 사용합니다. | 제한보다 작거나 같은 경우 준비 완료입니다.
**메모리** | 컴퓨터 메모리 크기는 Azure VM에 허용되는 최대 메모리 크기(Azure M 시리즈 Standard_M128m&nbsp;<sup>2</sup>에서 3892GB)와 같거나 작아야 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> 성능 기록을 사용할 수 있으면 Azure Migrate는 사용된 메모리 크기와 비교합니다. 쾌적 인자가 지정되어 있는 경우 사용된 메모리 크기와 쾌적 인자를 곱합니다.<br/><br/> 기록이 없으면 쾌적 인자를 적용하지 않고 할당된 메모리 크기를 사용합니다.<br/><br/> | 제한 내에 있는 경우 준비 완료입니다.
**저장소 디스크** | 할당된 디스크 크기는 4TB(4,096GB) 이하여야 합니다.<br/><br/> 컴퓨터에 연결되는 디스크의 수는 OS 디스크를 포함하여 65개 이하여야 합니다. | 제한 내에 있는 경우 준비 완료입니다.
**네트워킹** | 컴퓨터에는 32개 이하의 NIC가 연결되어야 합니다. | 제한 내에 있는 경우 준비 완료입니다.

### <a name="guest-operating-system"></a>게스트 운영 체제
Azure Migrate는 VM 속성과 함께 온-프레미스 VM의 게스트 OS도 확인하여 Azure에서 VM을 실행할 수 있는지 여부를 식별합니다.

> [!NOTE]
> Azure Migrate는 vCenter Server에 지정된 OS에서 다음 분석을 수행한다고 간주합니다. Azure Migrate에서 수행하는 검색은 어플라이언스 기반이므로 VM 내부에서 실행 중인 OS가 vCenter Server에 지정된 OS와 동일한지 여부를 확인할 수 있는 방법이 없습니다.

다음 논리는 Azure Migrate에서 운영 체제를 기준으로 VM의 Azure 준비 상태를 식별하는 데 사용됩니다.

**운영 체제** | **세부 정보** | **Azure 준비 상태**
--- | --- | ---
Windows Server 2016 및 모든 SP | Azure에서 완벽하게 지원합니다. | Azure 준비 완료
Windows Server 2012 R2 및 모든 SP | Azure에서 완벽하게 지원합니다. | Azure 준비 완료
Windows Server 2012 및 모든 SP | Azure에서 완벽하게 지원합니다. | Azure 준비 완료
Windows Server 2008 R2 및 모든 SP | Azure에서 완벽하게 지원합니다.| Azure 준비 완료
Windows Server 2008(32비트 및 64비트) | Azure에서 완벽하게 지원합니다. | Azure 준비 완료
Windows Server 2003, 2003 R2 | 이러한 운영 체제는 지원 날짜가 종료되었으며, Azure에서 지원을 받기 위해 [CSA(사용자 지정 지원 계약)](https://aka.ms/WSosstatement)가 필요합니다. | 조건부 Azure 준비 완료의 경우 Azure로 마이그레이션하기 전에 OS를 업그레이드하는 것이 좋습니다.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | 이러한 운영 체제는 지원 날짜가 종료되었으며, Azure에서 컴퓨터가 부팅되더라도 Azure가 OS 지원을 제공하지 않습니다. | 조건부 Azure 준비 완료의 경우 Azure로 마이그레이션하기 전에 OS를 업그레이드하는 것이 좋습니다.
Windows 클라이언트 7, 8, 10 | Azure는 [Visual Studio 구독만 지원합니다.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | 조건부 Azure 준비 완료
Windows 10 Pro Desktop | Azure는 [다중 테넌트 호스팅 권한을 지원합니다.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | 조건부 Azure 준비 완료
Windows Vista, XP Professional | 이러한 운영 체제는 지원 날짜가 종료되었으며, Azure에서 컴퓨터가 부팅되더라도 Azure가 OS 지원을 제공하지 않습니다. | 조건부 Azure 준비 완료의 경우 Azure로 마이그레이션하기 전에 OS를 업그레이드하는 것이 좋습니다.
Linux | Azure는 이러한 [Linux 운영 체제](../virtual-machines/linux/endorsed-distros.md)를 보증합니다. 다른 Linux 운영 체제도 Azure에서 부팅될 수 있지만, Azure로 마이그레이션하기 전에 OS를 보증된 버전으로 업그레이드하는 것이 좋습니다. | 버전이 보증되면 Azure 준비 완료입니다.<br/><br/>버전이 보증되지 않으면 조건부 준비 완료입니다.
기타 운영 체제<br/><br/> 예: Oracle Solaris, Apple Mac OS, FreeBSD 등 | Azure는 이러한 운영 체제를 보증하지 않습니다. Azure에서 컴퓨터가 부팅되더라도 Azure가 OS 지원을 제공하지 않습니다. | 조건부 Azure 준비 완료의 경우 Azure로 마이그레이션하기 전에 지원되는 OS를 설치하는 것이 좋습니다.  
vCenter Server에서 **기타**로 지정된 OS | 이 경우 Azure Migrate가 OS를 식별할 수 없습니다. | 준비 상태 알 수 없음입니다. VM 내부에서 실행 중인 OS가 Azure에서 지원되는지 확인합니다.
32비트 운영 체제 | Azure에서 컴퓨터가 부팅되더라도 Azure가 전체 지원을 제공하지 않을 수 있습니다. | 조건부 Azure 준비 완료의 경우 Azure로 마이그레이션하기 전에 컴퓨터의 OS를 32비트 OS에서 64비트 OS로 업그레이드하는 것이 좋습니다.

## <a name="sizing"></a>크기 조정

컴퓨터가 Azure 준비 완료로 표시되면 Azure Migrate는 VM 및 해당 디스크의 크기를 Azure에 맞게 조정합니다. 평가 속성에 지정된 크기 조정 기준이 성능 기반 크기 조정인 경우 Azure Migrate는 컴퓨터의 성능 기록을 고려하여 Azure에서 VM 크기와 디스크 형식을 식별합니다. 이 방법은 온-프레미스 VM을 초과 할당했지만 사용률이 낮으며 비용을 절약하기 위해 Azure에서 VM을 적정 크기로 조정하려는 경우에 유용합니다.

VM 크기 조정의 성능 기록을 고려하지 않고 현재 상태대로 VM을 Azure로 이동하려는 경우 크기 조정 기준을 *온-프레미스로*로 지정할 수 있습니다. 그러면 Azure Migrate는 사용률 데이터를 고려하지 않고 온-프레미스 구성을 기준으로 VM의 크기를 조정합니다. 이 경우 디스크 크기 조정은 평가 속성에 지정한 저장소 유형(표준 디스크 또는 프리미엄 디스크)을 기반으로 수행됩니다.

### <a name="performance-based-sizing"></a>성능 기반 크기 조정

성능 기반 크기 조정을 위해 Azure Migrate는 VM에 연결된 디스크로 시작한 다음, 네트워크 어댑터를 처리하며, 온-프레미스 VM의 컴퓨팅 요구 사항을 기준으로 Azure VM을 매핑합니다.

- **저장소**: Azure Migrate는 머신에 연결된 모든 디스크를 Azure의 디스크에 매핑하려고 합니다.

    > [!NOTE]
    > Azure Migrate는 평가에 위해 관리 디스크만 지원합니다.

    - 유효 디스크 IOPS(초당 I/O) 및 처리량(MBps)을 얻기 위해 Azure Migrate는 디스크 IOPS와 처리량에 쾌적 인자를 곱합니다. Azure Migrate는 유효 IOPS 및 처리량 값에 따라 디스크를 Azure의 표준 또는 프리미엄 디스크에 매핑할지를 식별합니다.
    - Azure Migrate가 필요한 IOPS 및 처리량을 가진 디스크를 찾지 못하면 컴퓨터가 Azure에 적합하지 않은 것으로 표시됩니다. 디스크 및 VM당 Azure 제한에 대해 [자세히 알아보세요](../azure-subscription-service-limits.md#storage-limits).
    - 적합한 디스크 집합을 찾으면 Azure Migrate는 저장소 중복 방법 및 평가 설정에 지정된 위치를 지원하는 디스크를 선택합니다.
    - 적합한 디스크가 여러 개인 경우 비용이 가장 낮은 디스크를 선택합니다.
    - 디스크의 성능 데이터를 사용할 수 없는 경우 모든 디스크가 Azure의 표준 디스크에 매핑됩니다.

- **네트워크**: Azure Migrate는 온-프레미스 머신에 연결된 네트워크 어댑터 수와 이러한 네트워크 어댑터에 필요한 성능을 지원할 수 있는 Azure VM을 찾으려고 합니다.
    - 온-프레미스 VM의 유효 네트워크 성능을 얻기 위해 Azure Migrate는 모든 네트워크 어댑터를 통해 컴퓨터에서 초당 전송(네트워크 출력)되는 데이터(MBps)를 집계하고 쾌적 인자를 적용합니다. 이 숫자는 필요한 네트워크 성능을 지원할 수 있는 Azure VM을 찾는 데 사용됩니다.
    - 네트워크 성능과 더불어, Azure VM이 필요한 네트워크 어댑터 수를 지원할 수 있는지 여부도 고려합니다.
    - 네트워크 성능 데이터를 사용할 수 없는 경우 네트워크 어댑터 개수만 VM 크기 조정에 고려됩니다.

- **컴퓨팅**: 스토리지 및 네트워크 요구 사항이 계산된 후 Azure Migrate는 CPU 및 메모리 요구 사항을 고려하여 Azure의 적정 VM 크기를 찾습니다.
    - Azure Migrate는 사용한 코어 및 메모리를 확인하고 쾌적 인자를 적용하여 유효 코어 및 메모리를 얻습니다. 이 숫자에 따라 Azure에서 적합한 VM 크기를 찾습니다.
    - 적합한 크기가 없으면 컴퓨터가 Azure에 적합하지 않은 것으로 표시됩니다.
    - 적합한 크기가 있으면 Azure Migrate는 저장소 및 네트워킹 계산을 적용합니다. 그런 다음 최종 VM 크기 권장 사항에 대해 위치 및 가격 책정 계층 설정을 적용합니다.
    - 적합한 Azure VM 크기가 여러 개인 경우 비용이 가장 낮은 크기가 권장됩니다.

### <a name="as-on-premises-sizing"></a>온-프레미스로 크기 조정
크기 조정 기준이 *온-프레미스로 크기 조정*인 경우 Azure Migrate는 VM과 디스크의 성능 기록을 고려하지 않고 온-프레미스에 할당된 크기에 따라 Azure에서 VM SKU를 할당합니다. 디스크 크기 조정과 유사하게 평가 속성에 지정된 저장소 유형을 확인하고(표준/프리미엄) 그에 따라 디스크 유형을 권장합니다. 기본 저장소 유형은 프리미엄 디스크입니다.

### <a name="confidence-rating"></a>신뢰 등급
Azure Migrate의 각 성능 기준 평가는 별 1개~5개 사이의 신뢰 등급에 연결됩니다(별 1개가 가장 낮고 5개가 가장 높음). 신뢰 등급은 평가 계산에 필요한 데이터 요소의 가용성에 따라 평가에 할당됩니다. 평가의 신뢰 등급은 Azure Migrate에서 제공하는 권장 크기의 신뢰성을 추정하는 데 도움이 됩니다. 신뢰 등급은 온-프레미스 평가에 적용되지 않습니다.

성능 기반 크기 조정의 경우 Azure Migrate에는 VM의 CPU 및 메모리 사용률 데이터가 필요합니다. 또한 VM에 연결된 각 디스크에는 디스크 IOPS 및 처리량 데이터가 필요합니다. 마찬가지로 VM에 연결된 각 네트워크 어댑터의 경우 성능 기반 크기 조정을 수행하려면 Azure Migrate에는 네트워크 입/출력이 필요합니다. 위의 사용률 데이터를 vCenter Server에서 사용할 수 없는 경우 Azure Migrate가 권장하는 크기의 신뢰성이 떨어질 수 있습니다. 사용 가능한 데이터 요소의 백분율에 따라 아래와 같이 평가의 신뢰 등급이 제공됩니다.

   **데이터 요소 가용성** | **신뢰 등급**
   --- | ---
   0%-20% | 별 1개
   21%-40% | 별 2개
   41%-60% | 별 3개
   61%-80% | 별 4개
   81%-100% | 별 5개

   아래에는 평가의 신뢰 등급이 낮아질 수 있는 몇 가지 이유가 나와 있습니다.

- 평가를 작성하는 기간 동안 환경을 프로파일링하지 않았습니다. 예를 들어 성능 기간을 1일로 설정하여 평가를 작성하는 경우 검색 시작 후 1일 이상 기다려야 모든 데이터 요소가 수집됩니다.

- 평가 계산 기간에 일부 VM이 종료되었습니다. 평가 기간 중 일부 VM이 꺼지면 해당 기간의 성능 데이터는 수집할 수 없습니다.

- 평가 계산 기간에 일부 VM이 생성되었습니다. 예를 들어 마지막 1달의 성능 기록에 대한 평가를 만들려고 하는데, 일부 VM이 불과 일주일 전에 환경에서 생성되었습니다. 이 경우 전체 기간에 대한 새 VM의 성능 기록이 제공되지 않습니다.

  > [!NOTE]
  > 평가의 신뢰 등급이 별 5개 미만인 경우 어플라이언스가 환경을 프로파일링하도록 하루 이상 기다린 다음, 평가를 *다시 계산*하는 것이 좋습니다. 위의 단계를 수행할 수 없는 경우 성능 기반 크기 조정의 신뢰성이 떨어질 수 있으므로 평가 속성을 변경하여 *온-프레미스로 크기 조정*으로 전환하는 것이 좋습니다.

## <a name="monthly-cost-estimation"></a>월별 비용 예측

크기 조정 권장 사항이 완료되면 Azure Migrate는 마이그레이션 후 계산 및 스토리지 비용을 계산합니다.

- **컴퓨팅 비용**: Azure Migrate는 권장되는 Azure VM 크기를 사용하여 청구 API를 통해 VM에 대한 월별 비용을 계산합니다. 계산에는 운영 체제, 소프트웨어 보증, 예약 인스턴스, VM 작동 시간, 위치 및 통화 설정이 고려됩니다. 모든 머신에서 비용을 집계하여 월별 총 계산 비용을 계산합니다.
- **스토리지 비용**: 머신에 연결된 모든 디스크의 월별 비용을 집계하여 해당 머신의 월별 스토리지 비용이 계산됩니다. Azure Migrate는 모든 컴퓨터의 저장소 비용을 집계하여 월별 총 저장소 비용을 계산합니다. 현재 계산에서는 평가 설정에 지정된 제안을 고려하지 않습니다.

비용은 평가 설정에 지정된 통화로 표시됩니다.


## <a name="next-steps"></a>다음 단계

[온-프레미스 VMware VM에 대한 평가 만들기](tutorial-assessment-vmware.md)
