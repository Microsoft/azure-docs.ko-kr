---
title: 이전 버전의 Azure Migrate 사용
description: 이전 버전의 Azure Migrate를 사용하는 방법을 설명합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 71fbd56c2566f008a096482755abbcdb174a987e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001640"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>이전 버전의 Azure Migrate 사용

이 문서에서는 이전 버전의 Azure Migrate 사용에 대한 정보를 제공합니다. 


Azure Migrate 서비스에는 다음과 같은 두 가지 버전이 있습니다.

- **현재 버전**: 이 버전을 사용하여 Azure Migrate 프로젝트를 만들고, 온-프레미스 컴퓨터를 검색하고, 평가 및 마이그레이션을 오케스트레이션합니다. 이 버전의 새로운 기능에 대해 [자세히 알아보세요](whats-new.md).
- **이전 버전**: 이전 버전의 Azure Migrate를 사용하고 있었다면(온-프레미스 VMware VM의 평가만 지원됨) 이제 현재 버전을 사용해야 합니다. 이 문서에서는 이전 버전 프로젝트를 클래식 프로젝트라고 합니다. 이전 버전에서 만든 Azure Migrate 프로젝트를 계속 사용해야 하는 경우 수행할 수 있는 작업과 할 수 없는 작업은 다음과 같습니다.
    - 더 이상 마이그레이션 프로젝트를 만들 수 없습니다.
    - 새 검색은 수행하지 않는 것이 좋습니다.
    - 기존 프로젝트에 계속 액세스할 수 있습니다.
    - 평가를 계속 실행할 수 있습니다.
    

## <a name="upgrade-between-versions"></a>버전 간 업그레이드

이전 버전의 프로젝트나 구성 요소를 새 버전으로 업그레이드할 수 없습니다. [새 Azure Migrate 프로젝트를 생성](create-manage-projects.md)하고 [평가 및 마이그레이션 도구를 추가](how-to-add-tool-first-time.md)해야 합니다. 사용 가능한 평가 및 마이그레이션 도구를 사용하는 방법을 이해하려면 자습서를 사용하세요. 클래식 프로젝트에 연결된 Log Analytics 작업 영역이 있는 경우 클래식 프로젝트를 삭제한 후에 현재 버전의 프로젝트에 연결할 수 있습니다.

## <a name="find-projects-from-previous-version"></a>이전 버전에서 프로젝트 찾기

다음과 같이 이전 버전에서 프로젝트를 찾습니다.

1. Azure Portal > **모든 서비스**에서 **Azure Migrate**를 검색하여 선택합니다. 
2. Azure Migrate 대시보드에는 이전 Azure Migrate 프로젝트에 액세스할 수 있는 알림 및 링크가 있습니다.
3. 링크를 클릭하여 클래식 프로젝트를 엽니다.

## <a name="delete-projects-from-previous-version"></a>이전 버전에서 프로젝트 삭제

다음과 같이 이전 버전에서 프로젝트를 찾아 삭제합니다.

1. Azure Portal > **모든 서비스**에서 **Azure Migrate**를 검색하여 선택합니다. 
2. Azure Migrate 대시보드에는 이전 Azure Migrate 프로젝트에 액세스할 수 있는 알림 및 링크가 있습니다.
3. 링크를 클릭하여 클래식 프로젝트를 엽니다.
4. 삭제하려는 프로젝트를 선택하여 삭제합니다. 


## <a name="create-an-assessment"></a>평가 만들기

VM을 포털에서 검색한 후 그룹화하여 평가를 만듭니다.

- VM이 포털에서 검색되면 즉시 온-프레미스 평가로 만들 수 있습니다.
- 성능 기반 평가를 위해서는 신뢰할 수 있는 크기 권장 사항을 얻기 위해 성능 기반 평가를 만들기 전에 하루 이상 대기하는 것이 좋습니다.

다음과 같이 평가를 만듭니다.

1. 프로젝트 **개요** 페이지에서 **+평가 만들기**를 클릭합니다.
2. 평가 속성을 검토하려면 **모두 보기**를 클릭합니다.
3. 그룹을 만들고 그룹 이름을 지정합니다.
4. 그룹에 추가하려는 컴퓨터를 선택합니다.
5. **평가 만들기**를 클릭하고, 그룹 및 평가를 만듭니다.
6. 평가를 만든 후 **개요** > **대시보드**에서 봅니다.
7. **평가 내보내기**를 클릭하고, Excel 파일로 다운로드합니다.

최신 성능 데이터로 기존 평가를 업데이트하려는 경우 평가에서 **다시 계산** 명령을 사용하여 업데이트할 수 있습니다.

## <a name="review-an-assessment"></a>평가 검토 

평가에는 다음 세 가지 단계가 있습니다.

- 평가는 컴퓨터가 Azure에서 호환되는지 여부를 파악하기 위한 적합성 분석으로 시작합니다.
- 예상 크기
- 월별 비용 예측

컴퓨터는 이전 단계를 통과하는 경우에만 이후 단계로 이동합니다. 예를 들어 컴퓨터가 적합성 검사에 실패하면 Azure에 적합하지 않은 것으로 표시되고 크기 조정과 비용 예측이 수행되지 않습니다.


### <a name="review-azure-readiness"></a>Azure 준비 상태 검토

평가의 Azure 준비 상태 보기는 각 VM의 준비 상태를 보여줍니다.

**준비 상태** | **State** | **세부 정보**
--- | --- | ---
Azure 준비 완료 | 호환성 문제가 없습니다. 이 머신은 있는 그대로 Azure로 마이그레이션할 수 있으며, 전체 Azure 지원 방식으로 Azure에서 부팅됩니다. | 준비된 VM의 경우 Azure Migrate는 Azure에서의 VM 크기를 권장합니다.
조건부 Azure 준비 완료 | 컴퓨터가 Azure에서 부팅될 수 있지만 전체 Azure 지원이 제공되지 않을 수 있습니다. 예를 들어 이전 버전의 Windows Server가 설치된 컴퓨터는 Azure에서 지원되지 않습니다. | Azure Migrate는 준비 상태와 관련된 이슈를 설명하고 수정 단계를 제공합니다.
Azure를 사용할 준비 안 됨 |  VM이 Azure에서 부팅되지 않습니다. 예를 들어 VM에 4TB보다 큰 디스크가 있는 경우 Azure에서 호스트할 수 없습니다. | Azure Migrate는 준비 상태와 관련된 이슈를 설명하고 수정 단계를 제공합니다.
준비 상태 알 수 없음 | 일반적으로 데이터를 사용할 수 없기 때문에 Azure Migrate는 Azure 준비 상태를 확인할 수 없습니다.


#### <a name="azure-vm-properties"></a>Azure VM 속성
준비 상태를 확인할 때는 Azure에서 VM을 실행할 수 있는지 여부를 식별하기 위해 많은 VM 속성이 고려됩니다.


**속성** | **세부 정보** | **준비 상태**
--- | --- | ---
**부팅 유형** | BIOS는 지원됩니다. UEFI는 지원되지 않습니다. | 부팅 유형이 UEFI인 경우 조건부 준비 완료입니다.
**코어 수** | Azure VM에 대해 최대 코어 수(128)보다 작거나 같은 시스템 코어가 지원됩니다.<br/><br/> 성능 기록을 사용할 수 있으면 Azure Migrate는 사용된 코어 수를 고려합니다.<br/>쾌적 인자가 평가 설정에 지정되어 있는 경우 사용된 코어 수와 쾌적 인자를 곱합니다.<br/><br/> 성능 기록이 없으면 Azure Migrate는 쾌적 인자를 적용하지 않고 할당된 코어 수를 사용합니다. | 제한보다 작거나 같은 경우 준비 완료입니다.
**메모리** | Azure VM에 대해 최대 메모리 크기(Azure M 시리즈 Standard_M128m&nbsp;<sup>2</sup>에서 3892GB)보다 작거나 같은 컴퓨터 메모리 크기 [자세히 알아보기](../virtual-machines/sizes.md).<br/><br/> 성능 기록을 사용할 수 있으면 Azure Migrate는 사용된 메모리 크기를 고려합니다.<br/><br/>쾌적 인자가 지정되어 있는 경우 사용된 메모리 크기와 쾌적 인자를 곱합니다.<br/><br/> 기록이 없으면 쾌적 인자를 적용하지 않고 할당된 메모리 크기를 사용합니다.<br/><br/> | 제한 내에 있는 경우 준비 완료입니다.
**스토리지 디스크** | 할당된 디스크 크기는 4TB(4,096GB) 이하여야 합니다.<br/><br/> 컴퓨터에 연결되는 디스크의 수는 OS 디스크를 포함하여 65개 이하여야 합니다. | 제한 내에 있는 경우 준비 완료입니다.
**네트워킹** | 컴퓨터에는 32개 이하의 NIC가 연결되어야 합니다. | 제한 내에 있는 경우 준비 완료입니다.

#### <a name="guest-operating-system"></a>게스트 운영 체제

Azure Migrate는 VM 속성과 함께 온-프레미스 VM의 게스트 OS도 확인하여 Azure에서 VM을 실행할 수 있는지 여부를 식별합니다.

- Azure Migrate는 vCenter Server에 지정된 OS를 고려합니다.
- Azure Migrate에서 수행하는 검색은 어플라이언스 기반이므로 VM 내부에서 실행 중인 OS가 vCenter Server에 지정된 OS와 동일한지 여부를 확인할 수 있는 방법이 없습니다.

다음 논리가 사용됩니다.

**운영 체제** | **세부 정보** | **준비 상태**
--- | --- | ---
Windows Server 2016 및 모든 SP | Azure에서 완벽하게 지원합니다. | Azure 준비 완료
Windows Server 2012 R2 및 모든 SP | Azure에서 완벽하게 지원합니다. | Azure 준비 완료
Windows Server 2012 및 모든 SP | Azure에서 완벽하게 지원합니다. | Azure 준비 완료
Windows Server 2008 R2 및 모든 SP | Azure에서 완벽하게 지원합니다.| Azure 준비 완료
Windows Server 2008(32비트 및 64비트) | Azure에서 완벽하게 지원합니다. | Azure 준비 완료
Windows Server 2003, 2003 R2 | 지원되지 않으며, Azure에서 지원되려면 [CSA(사용자 지정 지원 계약)](https://aka.ms/WSosstatement)가 필요합니다. | 조건부 Azure 준비 완료의 경우 Azure로 마이그레이션하기 전에 OS를 업그레이드하는 것이 좋습니다.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | 지원되지 않습니다. Azure에서 컴퓨터가 부팅되더라도 Azure가 OS 지원을 제공하지 않습니다. | 조건부 Azure 준비 완료의 경우 Azure로 마이그레이션하기 전에 OS를 업그레이드하는 것이 좋습니다.
Windows 클라이언트 7, 8, 10 | Azure는 [Visual Studio 구독만 지원합니다.](../virtual-machines/windows/client-images.md) | 조건부 Azure 준비 완료
Windows 10 Pro Desktop | Azure는 [다중 테넌트 호스팅 권한을 지원합니다.](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) | 조건부 Azure 준비 완료
Windows Vista, XP Professional | 지원되지 않습니다. Azure에서 컴퓨터가 부팅되더라도 Azure가 OS 지원을 제공하지 않습니다. | 조건부 Azure 준비 완료의 경우 Azure로 마이그레이션하기 전에 OS를 업그레이드하는 것이 좋습니다.
Linux | Azure는 이러한 [Linux 운영 체제](../virtual-machines/linux/endorsed-distros.md)를 보증합니다. 다른 Linux 운영 체제도 Azure에서 부팅될 수 있지만, Azure로 마이그레이션하기 전에 OS를 보증된 버전으로 업그레이드하는 것이 좋습니다. | 버전이 보증되면 Azure 준비 완료입니다.<br/><br/>버전이 보증되지 않으면 조건부 준비 완료입니다.
기타 운영 체제<br/><br/> 예: Oracle Solaris, Apple macOS, FreeBSD 등 | Azure는 이러한 운영 체제를 보증하지 않습니다. Azure에서 컴퓨터가 부팅되더라도 Azure가 OS 지원을 제공하지 않습니다. | 조건부 Azure 준비 완료의 경우 Azure로 마이그레이션하기 전에 지원되는 OS를 설치하는 것이 좋습니다.  
vCenter Server에서 **기타**로 지정된 OS | 이 경우 Azure Migrate가 OS를 식별할 수 없습니다. | 준비 상태 알 수 없음입니다. VM 내부에서 실행 중인 OS가 Azure에서 지원되는지 확인합니다.
32비트 운영 체제 | Azure에서 컴퓨터가 부팅되더라도 Azure가 전체 지원을 제공하지 않을 수 있습니다. | 조건부 Azure 준비 완료의 경우 Azure로 마이그레이션하기 전에 컴퓨터의 OS를 32비트 OS에서 64비트 OS로 업그레이드하는 것이 좋습니다.


### <a name="review-sizing"></a>크기 조정 검토

 Azure Migrate 크기 권장 사항은 평가 속성에 지정된 크기 조정 기준에 따라 달라집니다.

- 크기가 성능을 기준으로 조정되는 경우 VM(CPU 및 메모리) 및 디스크(IOPS 및 처리량)의 성능 기록을 고려하여 권장 크기가 결정됩니다.
- 크기 조정 기준이 '온-프레미스로'이면 VM 온-프레미스 크기에 따라 Azure에서 크기가 권장됩니다. 디스크 크기는 평가 속성에 지정된 스토리지 유형을 기준으로 조정됩니다(기본값은 프리미엄 디스크). Azure Migrate는 VM 및 디스크의 성능 데이터를 고려하지 않습니다.

### <a name="review-cost-estimates"></a>예상 비용 검토

예상 비용은 각 머신의 세부 사항과 함께 Azure에서 VM을 실행하는 데 필요한 총 컴퓨팅 및 스토리지 비용을 보여 줍니다.

- 예상 비용은 VM 컴퓨터 및 해당 디스크에 대한 권장 크기 사항과 평가 속성을 사용하여 계산됩니다.
- 컴퓨팅 및 스토리지에 대한 월별 예상 비용은 그룹의 모든 VM에 대해 집계됩니다.
- 비용 예측은 온-프레미스 VM을 Azure IaaS(Infrastructure as a service) VM으로 실행하기 위한 것입니다. Azure Migrate는 PaaS(Platform as a Service) 또는 SaaS(Software as a Service) 비용을 고려하지 않습니다.

### <a name="review-confidence-rating-performance-based-assessment"></a>신뢰 등급 검토(성능 기반 평가)

각 성능 기반 평가는 신뢰 등급과 관련됩니다.

- 신뢰 등급의 범위는 별 1개부터 별 5개 사이입니다(별 1개가 등급이 가장 낮고 별 5개가 등급이 가장 높음).
- 신뢰 등급은 평가 컴퓨팅에 필요한 데이터 요소의 가용성에 따라 평가에 할당됩니다.
- 평가의 신뢰 등급은 Azure Migrate에서 제공하는 권장 크기의 신뢰성을 추정하는 데 도움이 됩니다.
- 신뢰 등급은 "있는 그대로" 온-프레미스 평가에 사용할 수 없습니다.

Azure Migrate에서는 성능 기반 크기 조정을 위해 다음이 필요합니다.
- CPU의 사용률 데이터
- VM 메모리 데이터
- VM에 연결된 각 디스크에는 디스크 IOPS 및 처리량 데이터가 필요합니다.
- VM에 연결된 각 네트워크 어댑터의 경우 Azure Migrate에는 네트워크 입/출력이 필요합니다.
- 위의 방법 중 어떤 것도 사용할 수 없는 경우 크기 권장 사항(및 신뢰 등급)이 안정적이지 않을 수 있습니다.


사용 가능한 데이터 요소의 백분율에 따라 가능한 신뢰 등급이 표에 요약되어 제공됩니다.

**데이터 요소 가용성** | **신뢰 등급**
--- | ---
0%-20% | 별 1개
21%-40% | 별 2개
41%-60% | 별 3개
61%-80% | 별 4개
81%-100% | 별 5개


#### <a name="assessment-issues-affecting-confidence-ratings"></a>신뢰 등급에 영향을 주는 평가 문제

다음과 같은 여러 이유로 인해 평가에 일부 데이터 요소가 없을 수도 있습니다.

- 평가 기간 동안 환경을 프로파일링하지 않았습니다. 예를 들어 성능 기간을 1일로 설정하여 평가를 작성하는 경우 검색 시작 후 1일 이상 기다려야 모든 데이터 요소가 수집됩니다.
- 평가 계산 기간 동안 일부 VM이 종료되었습니다. 평가 기간 중 일부 VM이 꺼지면 Azure Migrate는 해당 기간의 성능 데이터는 수집할 수 없습니다.
- 일부 VM은 평가 계산 기간 중간에 생성되었습니다. 예를 들어, 지난 달의 성능 기록을 사용하여 평가를 만들었지만, 환경에 포함된 많은 VM은 1주 전에 만들었으면 새 VM의 성능 기록은 전체 기간에 대한 것이 아닙니다.

> [!NOTE]
> 평가의 신뢰 등급이 5개 별인 경우 어플라이언스가 환경을 프로파일링하도록 하루 이상 기다린 다음, 평가를 다시 계산합니다. 성능 기반 크기 조정이 안정적이지 않을 수도 있습니다. 다시 계산하지 않으려면 평가 속성을 변경하여 온-프레미스 크기 조정으로 전환하는 것이 좋습니다.



## <a name="create-groups-using-dependency-visualization"></a>종속성 시각화를 사용하여 그룹 만들기

그룹을 수동으로 만들 뿐 아니라 종속성 시각화를 사용하여 그룹을 만들 수도 있습니다.
- 일반적으로 평가를 실행하기 전에 컴퓨터 종속성을 교차 확인하여 더 높은 수준의 신뢰도로 그룹을 평가하려는 경우에 이 방법을 사용합니다.
- 종속성을 시각화화면 Azure로의 마이그레이션을 효과적으로 계획할 수 있습니다. 따라서 Azure로 마이그레이션할 때 하나도 남겨두지 않고 전부 가져올 수 있으며 예기치 않은 중단이 발생하지 않습니다.
- 함께 마이그레이션해야 하는 모든 상호 종속적인 시스템을 검색하고, 실행 중인 시스템이 여전히 사용자에게 서비스 중인지 아니면 마이그레이션 대신 서비스 해제가 적합한지 여부를 식별할 수 있습니다.
- Azure Migrate는 Azure Monitor의 서비스 맵 솔루션을 사용하여 종속성 시각화를 사용하도록 설정합니다.

> [!NOTE]
> Azure Government에서는 종속성 시각화를 사용할 수 없습니다.

종속성 시각화를 설정하려면 Log Analytics 작업 영역을 Azure Migrate 프로젝트와 연결하고 종속성을 시각화할 컴퓨터에 에이전트를 설치한 다음, 종속성 정보를 사용하여 그룹을 만듭니다. 



### <a name="associate-a-log-analytics-workspace"></a>Log Analytics 작업 영역 연결

종속성 시각화를 사용하려면 Log Analytics 작업 영역을 마이그레이션 프로젝트와 연결합니다. 작업 영역은 마이그레이션 프로젝트를 만든 것과 같은 구독에서만 만들거나 연결할 수 있습니다.

1. 프로젝트에 Log Analytics 작업 영역을 연결하려면 **개요** > **기본 정보**에서 **구성 필요**를 클릭합니다.
2. 새 작업 영역을 만들거나 기존 작업 영역을 연결할 수 있습니다.
  - 새 작업 영역을 만들려면 이름을 지정합니다. 마이그레이션 프로젝트와 같은 [Azure 지리적 위치](https://azure.microsoft.com/global-infrastructure/geographies/)에 해당하는 지역에서 작업 영역이 작성됩니다.
  - 기존 작업 영역을 연결하는 경우에는 마이그레이션 프로젝트와 동일한 구독에서 사용 가능한 모든 작업 영역을 선택할 수 있습니다. [지원되는 서비스 맵 지역](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)에서 만든 해당 작업 영역만 나열됩니다. 작업 영역을 연결하려면 작업 영역에 대한 '읽기 권한자' 액세스 권한이 있어야 합니다.

> [!NOTE]
> 마이그레이션 프로젝트에 연결된 작업 영역은 변경할 수 없습니다.

### <a name="download-and-install-vm-agents"></a>VM 에이전트 다운로드 및 설치

작업 영역을 구성한 후에는 평가하려는 각 온-프레미스 컴퓨터에 에이전트를 다운로드하여 설치합니다. 또한 인터넷에 연결되지 않은 머신이 있으면 [Log Analytics 게이트웨이](../azure-monitor/platform/gateway.md)를 다운로드하여 설치해야 합니다.

1. **개요**에서 **관리** > **컴퓨터**를 차례로 클릭하고 필요한 컴퓨터를 선택합니다.
2. **종속성** 열에서 **에이전트 설치**를 클릭합니다.
3. **종속성** 페이지에서 평가하려는 각 VM에 MMA(Microsoft Monitoring Agent) 및 종속성 에이전트를 다운로드하여 설치합니다.
4. 작업 영역 ID와 키를 복사합니다. 이 옵션은 온-프레미스 컴퓨터에 MMA를 설치할 때 필요합니다.

> [!NOTE]
> 에이전트의 설치를 자동화하려면 Configuration Manager와 같은 배포 도구 또는 [Intigua](https://www.intigua.com/intigua-for-azure-migration)와 같이 Azure Migrate용 에이전트 배포 솔루션을 제공하는 파트너 도구를 사용할 수 있습니다.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>Windows 머신에 MMA 에이전트 설치

Windows 컴퓨터에 에이전트를 설치하려면

1. 다운로드한 에이전트를 두 번 클릭합니다.
2. **Welcome** 페이지에서 **다음**을 클릭합니다. **사용 조건** 페이지에서 **동의함**을 클릭하여 라이선스에 동의합니다.
3. **대상 폴더**에서 기본 설치 폴더를 유지하거나 수정하고 **다음**을 클릭합니다.
4. **에이전트 설치 옵션**에서 **Azure Log Analytics** > **다음**을 차례로 선택합니다.
5. **추가**를 클릭하여 새로운 Log Analytics 작업 영역을 추가합니다. 포털에서 복사한 작업 영역 ID와 키를 붙여넣습니다. **다음**을 클릭합니다.

명령줄에서 또는 Configuration Manager와 같은 자동화된 방법을 사용하여 에이전트를 설치할 수 있습니다. 이 방법을 사용하여 MMA 에이전트를 설치하는 방법을 [자세히 알아보세요](../azure-monitor/platform/log-analytics-agent.md#installation-options).

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Linux 머신에 MMA 에이전트 설치

Linux 컴퓨터에 에이전트를 설치하려면

1. scp/sftp를 사용하여 Linux 컴퓨터로 해당 번들(x86 또는 x64)을 전송합니다.
2. --install 인수를 사용하여 번들을 설치합니다.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

MMA에서 지원하는 Linux 운영 체제 목록을 [자세히 확인](../azure-monitor/platform/agents-overview.md#supported-operating-systems)해 보세요.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Operations Manager에서 모니터링하는 머신에 MMA 에이전트 설치

System Center Operations Manager 2012 R2 이상에서 모니터링하는 머신의 경우 MMA 에이전트를 설치할 필요가 없습니다. 서비스 맵은 Operations Manager MMA와 연결되어 필요한 종속성 데이터를 수집합니다. [자세히 알아보기](../azure-monitor/insights/service-map-scom.md#prerequisites). 종속성 에이전트를 설치해야 합니다.

### <a name="install-the-dependency-agent"></a>종속성 에이전트 설치

1. Windows 컴퓨터에 종속성 에이전트를 설치하려면 설치 파일을 두 x 누르고 마법사를 따릅니다.
2. Linux 컴퓨터에 종속성 에이전트를 설치하려면 다음 명령을 사용하여 루트로 설치합니다.

    ```sh InstallDependencyAgent-Linux64.bin```

- Windows 및 Linux 운영 체제에 대한 [종속성 에이전트 지원](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)에 대해 자세히 알아보세요.
- 스크립트를 사용하여 종속성 에이전트를 설치하는 방법에 대해 [자세히 알아봅니다](../azure-monitor/insights/vminsights-enable-hybrid.md#dependency-agent).

>[!NOTE]
> 시스템 필수 구성 요소 및 종속성 에이전트를 배포하는 방법에 대한 개요를 제공하기 위해 참조되는 VM용 Azure Monitor 문서는 서비스 맵 솔루션에도 적용됩니다.

### <a name="create-a-group-with-dependency-mapping"></a>종속성 매핑을 사용하여 그룹 만들기

1. 에이전트를 설치한 후에 포털로 이동하여 **관리** > **컴퓨터**를 차례로 클릭합니다.
2. 에이전트를 설치한 컴퓨터를 검색합니다.
3. 이제 컴퓨터의 **종속성** 열이 **종속성 보기**로 표시됩니다. 해당 열을 클릭하여 컴퓨터의 종속성을 봅니다.
4. 컴퓨터의 종속성 맵은 다음과 같은 세부 정보를 보여 줍니다.
    - 컴퓨터와 주고 받는 인바운드(클라이언트) 및 아웃바운드(서버) TCP 연결
        - MMA 및 종속성 에이전트가 설치되지 않은 종속 컴퓨터는 포트 번호별로 그룹화됩니다.
        - MMA 및 종속성 에이전트가 설치된 종속 컴퓨터는 별도의 상자로 표시됩니다.
    - 컴퓨터 내부에서 실행 중인 프로세스. 각 컴퓨터 상자를 확장하여 프로세스를 볼 수 있습니다.
    - FQDN, 운영 체제, MAC 주소를 포함하는 컴퓨터 속성이 표시됩니다. 각 컴퓨터 상자를 클릭하여 세부 정보를 볼 수 있습니다.

4. 시간 범위 레이블의 시간 범위를 클릭하여 여러 시간 범위의 종속성을 살펴볼 수 있습니다. 범위는 기본적으로 1시간입니다. 시간 범위를 수정하거나 시작 및 종료 날짜와 기간을 지정할 수 있습니다.

   > [!NOTE]
   >    최대 1시간의 시간 범위가 지원됩니다. 더 긴 기간에 대한 [종속성 데이터를 쿼리](./how-to-create-group-machine-dependencies.md)하려면 Azure Monitor 로그를 사용합니다.

5. 그룹화하려는 종속 컴퓨터를 식별한 후 Ctrl 키를 누른 상태로 맵에서 여러 컴퓨터를 클릭하여 선택하고 **컴퓨터 그룹화**를 클릭합니다.
6. 그룹 이름을 지정합니다. Azure Migrate에서 종속 컴퓨터가 검색되는지 확인합니다.

    > [!NOTE]
    > Azure Migrate에서 종속 컴퓨터가 검색되지 않으면 그룹에 추가할 수 없습니다. 종속 컴퓨터를 그룹을 추가하려면 vCenter Server에서 올바른 범위에 대해 검색 프로세스를 다시 실행하고 Azure Migrate에서 컴퓨터가 검색되는지 확인해야 합니다.  

7. 이 그룹에 대한 평가를 만들려면 그룹에 대한 새 평가를 만드는 확인란을 선택합니다.
8. **확인**을 클릭하여 그룹을 저장합니다.

그룹이 만들어지면 해당 그룹의 모든 컴퓨터에 에이전트를 설치하고 전체 그룹의 종속성을 시각화하여 그룹을 구체화하는 것이 좋습니다.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Azure Monitor 로그에서 종속성 데이터 쿼리

서비스 맵에서 캡처한 종속성 데이터는 Azure Migrate 프로젝트와 연결된 Log Analytics 작업 영역에서 쿼리하는 데 사용할 수 있습니다. Azure Monitor 로그에서 쿼리할 서비스 맵 데이터 테이블에 대해 [자세히 알아보세요](../azure-monitor/insights/service-map.md#log-analytics-records). 

Kusto 쿼리를 실행하려면 다음을 수행합니다.

1. 에이전트를 설치한 후 포털로 이동하고 **개요**를 클릭합니다.
2. **개요**에서 프로젝트의 **필수** 섹션으로 이동하고 **OMS 작업 영역** 옆에 제공된 작업 영역 이름을 클릭합니다.
3. Log Analytics 작업 영역 페이지에서 **일반** > **로그**를 클릭합니다.
4. Azure Monitor 로그를 사용하여 종속성 데이터를 수집하는 쿼리를 작성합니다. 다음 섹션에서 샘플 쿼리를 찾습니다.
5. [실행]을 클릭하여 쿼리를 실행합니다. 

Kusto 쿼리를 작성하는 방법을 [자세히 알아보세요](../azure-monitor/log-query/get-started-portal.md). 

### <a name="sample-azure-monitor-logs-queries"></a>예제 Azure Monitor 로그 쿼리

다음은 종속성 데이터를 추출하는 데 사용할 수 있는 샘플 쿼리입니다. 선호하는 데이터 요소를 추출하도록 쿼리를 수정할 수 있습니다. 종속성 데이터 레코드의 필드 전체 목록은 [여기](../azure-monitor/insights/service-map.md#log-analytics-records)에서 사용할 수 있습니다. 추가 샘플 쿼리는 [여기](../azure-monitor/insights/service-map.md#sample-log-searches)에서 찾을 수 있습니다.

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>컴퓨터 세트의 인바운드 연결 요약

연결 메트릭의 VMConnection 표에 포함된 레코드는 물리적 네트워크 연결을 나타내지 않습니다. 여러 물리적 네트워크 연결은 논리적 연결로 그룹화됩니다. 물리적 네트워크 연결 데이터를 VMConnection의 단일 논리적 레코드로 집계하는 방법에 대해 [자세히 알아봅니다](../azure-monitor/insights/service-map.md#connections). 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>컴퓨터 세트 간의 인바운드 연결에서 송수신된 데이터의 양을 요약합니다.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```


## <a name="next-steps"></a>다음 단계
최신 버전의 Azure Migrate에 대해 [알아봅니다](migrate-services-overview.md).