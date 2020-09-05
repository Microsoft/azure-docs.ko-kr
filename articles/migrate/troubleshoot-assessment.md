---
title: Azure Migrate의 평가 및 종속성 시각화 문제 해결
description: Azure Migrate에서 평가 및 종속성 시각화 문제 해결에 대 한 도움말을 확인 하세요.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: a6a185c61c32636dd0189bc5835f850348b196cd
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020357"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>평가/종속성 시각화 문제 해결

이 문서는 [Azure Migrate: 서버 평가](migrate-services-overview.md#azure-migrate-server-assessment-tool)를 사용 하 여 평가 및 종속성 시각화 문제를 해결 하는 데 도움이 됩니다.


## <a name="assessment-readiness-issues"></a>평가 준비 문제

평가 준비 문제를 다음과 같이 수정 합니다.

**문제점** | **해결**
--- | ---
지원되지 않는 부팅 유형 | Azure는 EFI 부팅 유형을 사용 하는 Vm을 지원 하지 않습니다. 마이그레이션을 실행 하기 전에 부팅 유형을 BIOS로 변환 하는 것이 좋습니다. <br/><br/>Azure Migrate Server Migration을 사용 하 여 이러한 Vm의 마이그레이션을 처리할 수 있습니다. 마이그레이션하는 동안 VM의 부팅 유형을 BIOS로 변환 합니다.
조건부로 지원 되는 Windows 운영 체제 | 운영 체제에서 지원 종료 날짜를 통과 했으며 [Azure에서 지원](https://aka.ms/WSosstatement)하기 위해 CSA (사용자 지정 지원 계약)가 필요 합니다. Azure로 마이그레이션하기 전에 업그레이드 하는 것이 좋습니다.
지원 되지 않는 Windows 운영 체제 | Azure는 [선택한 WINDOWS OS 버전만](https://aka.ms/WSosstatement)지원 합니다. Azure로 마이그레이션하기 전에 컴퓨터를 업그레이드 하는 것이 좋습니다.
조건부로 보증되는 Linux OS | Azure 보증에는 [선택한 LINUX OS 버전만](../virtual-machines/linux/endorsed-distros.md)있습니다. Azure로 마이그레이션하기 전에 컴퓨터를 업그레이드 하는 것이 좋습니다. 자세한 내용은 [여기](https://docs.microsoft.com/azure/migrate/troubleshoot-assessment#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment) 를 참조 하세요.
보증되지 않는 Linux OS | 컴퓨터가 Azure에서 시작 될 수 있지만 Azure는 운영 체제를 지원 하지 않습니다. Azure로 마이그레이션하기 전에 [보증 Linux 버전](../virtual-machines/linux/endorsed-distros.md) 으로 업그레이드 하는 것이 좋습니다.
알 수 없는 운영 체제 | VCenter Server에서 VM의 운영 체제가 "기타"로 지정 되었습니다. 이 동작은 VM의 Azure 준비 상태를 확인 하는 Azure Migrate 차단 합니다. 컴퓨터를 마이그레이션하기 전에 Azure에서 운영 체제가 [지원](https://aka.ms/azureoslist) 되는지 확인 합니다.
지원 되지 않는 비트 버전 | 32 비트 운영 체제를 사용 하는 Vm은 Azure에서 부팅 될 수 있지만, Azure로 마이그레이션하기 전에 64 비트로 업그레이드 하는 것이 좋습니다.
Microsoft Visual Studio 구독이 필요 합니다. | 컴퓨터에서 Visual Studio 구독을 통해서만 지원 되는 Windows 클라이언트 운영 체제를 실행 하 고 있습니다.
필요한 저장소 성능에 대 한 VM을 찾을 수 없습니다. | 컴퓨터에 필요한 저장소 성능 (초당 입/출력 작업 수 [IOPS] 및 처리량)이 Azure VM 지원을 초과 합니다. 마이그레이션을 시작하기 전에 컴퓨터의 스토리지 요구 사항을 낮춰봅니다.
필요한 네트워크 성능에 대 한 VM을 찾을 수 없습니다. | 컴퓨터에 필요한 네트워크 성능(입력/출력)이 Azure VM 지원을 초과합니다. 컴퓨터의 네트워킹 요구 사항을 낮춰봅니다.
지정 된 위치에서 VM을 찾을 수 없습니다. | 다른 대상 위치를 사용하여 마이그레이션을 실행해 봅니다.
하나 이상의 적합 하지 않은 디스크 | VM에 연결 된 하나 이상의 디스크가 Azure 요구 사항을 충족 하지 않습니다. 은<br/><br/> Azure Migrate: 서버 평가는 현재 울트라 SSD 디스크를 지원 하지 않으며 프리미엄 관리 디스크에 대 한 디스크 제한 (32 TB)에 따라 디스크를 평가 합니다.<br/><br/> VM에 연결 된 각 디스크에 대해 디스크 크기가 64 TB < (울트라 SSD 디스크에서 지원 됨) 확인 합니다.<br/><br/> 그렇지 않은 경우 Azure로 마이그레이션하기 전에 디스크 크기를 줄이거나, Azure에서 여러 디스크를 사용 하 고 여러 디스크를 [함께 스트라이프](../virtual-machines/premium-storage-performance.md#disk-striping) 하 여 저장소 제한을 더 늘려야 합니다. 각 디스크에 필요한 성능 (IOPS 및 처리량)이 Azure [관리 가상 머신 디스크](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)에서 지원 되는지 확인 합니다.
부적합한 네트워크 어댑터가 하나 이상 있습니다. | 마이그레이션을 시작하기 전에 컴퓨터에서 사용하지 않는 네트워크 어댑터를 제거합니다.
디스크 수 한도 초과 | 마이그레이션을 시작하기 전에 컴퓨터에서 사용하지 않는 디스크를 제거합니다.
디스크 크기 한도 초과 | Azure Migrate: 서버 평가는 현재 울트라 SSD 디스크를 지원 하지 않으며 프리미엄 디스크 제한 (32 TB)에 따라 디스크를 평가 합니다.<br/><br/> 그러나 Azure는 최대 64-TB 크기의 디스크를 지원 합니다 (울트라 SSD 디스크에서 지원 됨). 마이그레이션을 수행 하기 전에 디스크를 64 TB 미만으로 축소 하거나 Azure에서 여러 디스크를 사용 하 고 [함께 스트라이프](../virtual-machines/premium-storage-performance.md#disk-striping) 하 여 더 높은 저장소 제한을 얻습니다.
지정된 위치의 디스크를 사용할 수 없음 | 마이그레이션하기 전에 디스크가 대상 위치에 있는지 확인합니다.
지정된 이중화에 디스크를 사용할 수 없음 | 디스크가 평가 설정에 정의된 중복 스토리지 형식(기본적으로 LRS)을 사용해야 합니다.
내부 오류로 인해 디스크 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
필요한 코어 및 메모리가 포함된 VM을 찾을 수 없음 | Azure에서 적합 한 VM 유형을 찾을 수 없습니다. 마이그레이션하기 전에 온-프레미스 컴퓨터의 메모리 및 코어 수를 줄여봅니다.
내부 오류로 인해 VM 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 디스크에 대 한 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 네트워크 어댑터에 대 한 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
제안 통화 예약 인스턴스에 대 한 VM 크기를 찾을 수 없습니다. | 선택 된 RI, 제안 및 통화 조합에 대 한 VM 크기를 찾을 수 없기 때문에 컴퓨터가 적절 하지 않음으로 표시 되었습니다. 평가 속성을 편집 하 여 유효한 조합을 선택 하 고 평가를 다시 계산 합니다. 
조건적으로 준비 된 인터넷 프로토콜 | Azure VMware 솔루션 (AVS) 평가에만 적용 됩니다. AVS는 IPv6 인터넷 주소 요인을 지원 하지 않습니다.컴퓨터가 i p v 6을 통해 검색 되는 경우에는 업데이트를 위해 AVS 팀에 문의 하세요.

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>가져오기 기반 AVS 평가에서 알 수 없음으로 표시 된 제안 된 마이그레이션 도구

CSV 파일을 통해 가져온 컴퓨터의 경우 및 AVS 평가의 기본 마이그레이션 도구를 알 수 없습니다. 그러나 VMware 컴퓨터의 경우에는 VMware 하이브리드 클라우드 확장 (HCX) 솔루션을 사용 하는 것이 좋습니다. [자세한 정보](../azure-vmware/hybrid-cloud-extension-installation.md).

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Linux Vm은 Azure VM 평가에서 "조건부로 준비" 됩니다.

VMware 및 Hyper-v Vm의 경우 서버 평가는 서버 평가의 알려진 간격으로 인해 Linux Vm을 "조건적으로 준비 된" 것으로 표시 합니다. 

- 이 차이는 온-프레미스 Vm에 설치 된 Linux OS의 부 버전을 검색 하는 것을 방지 합니다.
- 예를 들어 RHEL 6.10의 경우 현재 서버 평가는 OS 버전으로 RHEL 6만 검색 합니다. 이는 Hyper-v 호스트 vCenter Server ar가 Linux VM 운영 체제의 커널 버전을 제공 하지 않기 때문입니다.
-  Azure는 특정 버전의 Linux만 보증 때문에 Linux Vm은 현재 서버 평가에서 조건부로 준비 된 것으로 표시 되어 있습니다.
- [Azure linux 지원](https://aka.ms/migrate/selfhost/azureendorseddistros)을 검토 하 여 온-프레미스 VM에서 실행 되는 Linux OS가 azure에서 보증 여부를 확인할 수 있습니다.
-  보증 배포를 확인 한 후에는이 경고를 무시 해도 됩니다.

이 간격은 VMware Vm에서 [응용 프로그램 검색](./how-to-discover-applications.md) 을 사용 하도록 설정 하 여 해결할 수 있습니다. 서버 평가는 제공된 게스트 자격 증명을 사용하여 VM에서 검색된 운영 체제를 사용합니다. 이 운영 체제 데이터는 Windows 및 Linux Vm의 경우 적절 한 OS 정보를 식별 합니다.

## <a name="operating-system-version-not-available"></a>운영 체제 버전을 사용할 수 없음

물리적 서버의 경우 운영 체제 부 버전 정보를 사용할 수 있습니다. 사용할 수 없는 경우 Microsoft 지원에 문의 하세요. VMware 컴퓨터의 경우 서버 평가는 vCenter Server VM에 지정 된 운영 체제 정보를 사용 합니다. 그러나 vCenter Server는 운영 체제의 부 버전을 제공 하지 않습니다. 부 버전을 검색 하려면 [응용 프로그램 검색](./how-to-discover-applications.md)을 설정 해야 합니다. Hyper-v Vm의 경우 운영 체제 부 버전 검색은 지원 되지 않습니다. 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure VM 평가에서 온-프레미스 보다 큰 azure Sku

Azure Migrate 서버 평가는 평가 유형에 따라 현재 온-프레미스 할당 보다 더 많은 코어 및 메모리를 사용 하는 Azure VM Sku를 권장할 수 있습니다.

- VM SKU 권장 사항은 평가 속성에 따라 달라 집니다.
- 이는 서버 평가에서 수행 하는 평가 유형 ( *성능 기반*또는 *온-프레미스)* 의 영향을 받습니다.
- 성능 기반 평가의 경우 서버 평가는 온-프레미스 vm (CPU, 메모리, 디스크 및 네트워크 사용률)의 사용률 데이터를 고려 하 여 온-프레미스 Vm의 올바른 대상 VM SKU를 확인 합니다. 또한 효과적인 사용률을 결정할 때 쾌적 인자를 추가합니다.
- 온-프레미스 크기 조정의 경우 성능 데이터를 고려 하지 않으며 대상 SKU는 온-프레미스 할당을 기반으로 하는 것이 좋습니다.

권장 구성에 영향을 줄 수 있는 방법을 보여 주기 위해 예를 살펴보겠습니다.

50% CPU 사용률과 50%의 메모리 사용률과 지정 된 1.3 편안 하 게 코어와 8 GB의 메모리가 있는 온-프레미스 VM이 있습니다.

-  평가를 **온-프레미스로**사용 하는 경우 4 개의 코어와 8gb의 메모리를 사용 하는 AZURE VM SKU를 사용 하는 것이 좋습니다.
- 평가가 성능 기반 인 경우 유효한 CPU 및 메모리 사용률 (4 개 코어의 50% * 1.3 = 2.6 코어 및 50% 8gb memory * 1.3 = 5.3 GB memory)을 기반으로 하 여 4 개 코어 (지원 되는 가장 가까운 코어 수)와 8GB의 메모리 (지원 되는 가장 가까운 메모리 크기)의 가장 저렴 한 VM SKU를 사용 하는 것이 좋습니다.
- 평가 크기 조정에 대해 [자세히 알아보세요](concepts-assessment-calculation.md#types-of-assessments) .

## <a name="why-is-the-recommended-azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure VM 평가에서 권장 되는 Azure disk Sku가 온-프레미스 보다 큰 이유는 무엇 인가요?

Azure Migrate Server 평가는 평가 유형에 따라 더 큰 디스크를 권장할 수 있습니다.
- 서버 평가의 디스크 크기 조정은 두 가지 평가 속성인 크기 조정 조건 및 저장소 유형에 따라 달라 집니다.
- 크기 조정 기준이 성능에 **따라 결정**되 고 저장소 유형이 **자동**으로 설정 된 경우 대상 디스크 유형 (표준 HDD, 표준 SSD 또는 프리미엄)을 식별 하는 경우 디스크의 IOPS 및 처리량 값이 고려 됩니다. 그런 다음 디스크 유형의 디스크 SKU를 권장 하며 권장 사항은 온-프레미스 디스크의 크기 요구 사항을 고려 합니다.
- 크기 조정 기준이 **성능 기반**이며 저장소 유형이 **프리미엄**인 경우 온-프레미스 디스크의 IOPS, 처리량 및 크기 요구 사항에 따라 AZURE의 premium disk SKU를 사용 하는 것이 좋습니다. 크기 조정 기준이 **온-프레미스** 이 고 저장소 유형이 **표준 HDD**, **표준 SSD**또는 **Premium**인 경우 동일한 논리를 사용 하 여 디스크 크기를 조정 합니다.

예를 들어 32 GB의 메모리를 포함 하는 온-프레미스 디스크가 있지만 디스크에 대해 집계 된 읽기/쓰기 IOPS가 800 IOPS 인 경우 서버 평가는 프리미엄 디스크 (IOPS 요구 사항)를 권장 하 고 필요한 IOPS 및 크기를 지원할 수 있는 디스크 SKU를 권장 합니다. 이 예제에서 가장 가까운 일치 항목은 P15(256GB, 1100IOPS)입니다. 온-프레미스 디스크에 필요한 크기가 32 GB 이더라도 서버 평가는 온-프레미스 디스크의 높은 IOPS 요구 사항으로 인해 더 큰 디스크를 권장 합니다.

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>평가 보고서에서 일부 또는 모든 VM의 성능 데이터가 누락된 이유는 무엇인가요?

"성능 기반" 평가의 경우 Azure Migrate 어플라이언스에서 온-프레미스 VM에 대한 성능 데이터를 수집할 수 없는 경우 평가 보고서 내보내기에 'PercentageOfCoresUtilizedMissing' 또는 'PercentageOfMemoryUtilizedMissing'이 표시됩니다. 다음을 확인하세요.

- 평가를 만드는 동안 VM의 전원이 켜져 있는지 확인합니다.
- 메모리 카운터가 누락되었지만 Hyper-V VM을 평가하려는 경우 이러한 VM에서 동적 메모리를 사용하도록 설정했는지 확인합니다. 현재 Azure Migrate 어플라이언스가 이러한 VM의 메모리 사용률을 수집할 수 없기 때문에 알려진 문제가 있습니다.
- 모든 성능 카운터가 누락 된 경우 평가에 대 한 포트 액세스 요구 사항이 충족 되는지 확인 합니다. [VMware](./migrate-support-matrix-vmware.md#port-access-requirements), [hyper-v](./migrate-support-matrix-hyper-v.md#port-access) 및 [물리적](./migrate-support-matrix-physical.md#port-access) 서버 평가를 위한 포트 액세스 요구 사항에 대해 자세히 알아보세요.
참고 - 임의의 성능 카운터가 누락된 경우 Azure Migrate: 서버 평가는 할당된 코어/메모리 온-프레미스로 대체하고 해당하는 VM 크기를 권장합니다.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>내 평가의 신뢰 등급이 낮은 이유는 무엇인가요?

신뢰 등급은 평가를 계산하는 데 필요한 [사용 가능한 데이터 요소](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#ratings)의 백분율을 기준으로 "성능 기반" 평가에 대해 계산됩니다. 아래에는 평가의 신뢰 등급이 낮아질 수 있는 이유가 나와 있습니다.

- 평가를 작성하는 기간 동안 환경을 프로파일링하지 않았습니다. 예를 들어 성능 기간을 일주일로 설정하여 평가를 만드는 경우 검색 시작 후 일주일 이상 기다려야 데이터 요소가 수집됩니다. 이 기간 동안 기다릴 수 없으면 성능 기간을 더 짧은 기간으로 변경하고 평가를 '다시 계산'하세요.
 
- 서버 평가는 평가 기간에 일부 또는 모든 VM에 대한 성능 데이터를 수집할 수 없습니다. 평가 기간 동안 VM의 전원이 켜져 있는지, 포트 443에 대한 아웃바운드 연결이 허용되는지 확인하세요. Hyper-V VM의 경우 동적 메모리를 사용하도록 설정하면 메모리 카운터가 누락되어 신뢰 등급이 낮아집니다. 신뢰 등급의 최신 변경 내용을 반영하려면 평가를 '다시 계산'하세요. 

- Server Assessment에서 검색이 시작된 후 VM 몇 개가 생성되었습니다. 예를 들어 마지막 1달의 성능 기록에 대한 평가를 만들려고 하는데, 일부 VM이 불과 일주일 전에 환경에서 생성되었습니다. 이 경우 새 VM의 성능 데이터를 전체 기간에 사용할 수 없으며 신뢰 등급이 낮아집니다.

신뢰 등급에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#confidence-ratings-performance-based).

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>Azure VM 평가에 운영 체제 라이선스가 포함 되어 있나요?

Azure Migrate Server 평가는 현재 Windows 컴퓨터에 대해서만 운영 체제 라이선스 비용을 고려 합니다. Linux 컴퓨터에 대 한 라이선스 비용은 현재 고려 되지 않습니다.

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>Azure VM 평가에서 성능 기반 크기 조정은 어떻게 작동 하나요?

Server Assessment는 온-프레미스 머신의 성능 데이터를 지속적으로 수집하여 Azure에서 VM SKU 및 디스크 SKU를 추천하는 데 사용합니다. 성능 기반 데이터를 수집 [하는 방법을 알아봅니다](concepts-assessment-calculation.md#calculate-sizing-performance-based) .

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>예약 인스턴스, VM 작동 시간 및 할인율 (%)의 잘못 된 조합을 사용 하 여 생성 되었다는 경고가 표시 되는 이유는 무엇 인가요?
' 예약 인스턴스 '를 선택 하면 ' 할인율 ' (' 할인율 ')이 및 ' VM 작동 시간 ' 속성은 적용 되지 않습니다. 이러한 속성의 잘못 된 조합을 사용 하 여 평가를 만든 후에는 편집 및 다시 계산 단추가 사용 하지 않도록 설정 됩니다. 새 평가를 만드세요. [자세히 알아보기](https://go.microsoft.com/fwlink/?linkid=2131554).

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>실제 서버의 일부 네트워크 어댑터에 대 한 성능 데이터가 표시 되지 않습니다.

물리적 서버에서 Hyper-v 가상화를 사용 하는 경우이 문제가 발생할 수 있습니다. 이러한 서버에서 제품 격차 때문에 현재 Azure Migrate 실제 및 가상 네트워크 어댑터를 모두 검색 합니다. 네트워크 처리량은 검색 된 가상 네트워크 어댑터에만 캡처됩니다.

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>실제 서버에 권장 되는 Azure VM SKU가 너무 큰 경우

물리적 서버에서 Hyper-v 가상화를 사용 하는 경우이 문제가 발생할 수 있습니다. 이러한 서버에서 현재 Azure Migrate 실제 및 가상 네트워크 어댑터를 모두 검색 합니다. 따라서 아니요입니다. 검색 된 네트워크 어댑터 수가 실제 보다 높습니다. 서버 평가가 필요한 수의 네트워크 어댑터를 지원할 수 있는 Azure VM을 선택 하는 경우이로 인해 큰 VM이 발생할 수 있습니다. 아니요의 영향에 [대해 자세히 알아보세요](./concepts-assessment-calculation.md#calculating-sizing) . 네트워크 어댑터의 크기를 조정 합니다. 이는 앞으로 처리 될 제품 격차입니다.

## <a name="readiness-category-not-ready-for-my-physical-server"></a>실제 서버에 대 한 준비 범주 "준비 안 됨"

Hyper-v 가상화를 사용 하는 물리적 서버의 경우 준비 범주가 "준비 되지 않음"으로 잘못 표시 될 수 있습니다. 이러한 서버에서 제품 격차 때문에 현재 실제 어댑터와 가상 어댑터를 모두 검색 Azure Migrate 합니다. 따라서 아니요입니다. 검색 된 네트워크 어댑터 수가 실제 보다 높습니다. 온-프레미스 및 성능 기반 평가에서 서버 평가는 필요한 수의 네트워크 어댑터를 지원할 수 있는 Azure VM을 선택 합니다. 네트워크 어댑터 수가 32 보다 더 높은 것으로 검색 되는 경우 최대값은 아닙니다. Azure Vm에서 지원 되는 Nic의 경우 컴퓨터가 "준비 되지 않음"으로 표시 됩니다.  아니요의 영향에 [대해 자세히 알아보세요](./concepts-assessment-calculation.md#calculating-sizing) . 크기 조정 시의 Nic.


## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>실제 서버에 대해 실제 보다 높은 검색 된 Nic 수

물리적 서버에서 Hyper-v 가상화를 사용 하는 경우이 문제가 발생할 수 있습니다. 이러한 서버에서 현재 Azure Migrate 실제 어댑터와 가상 어댑터를 모두 검색 합니다. 따라서 아니요입니다. 검색 된 Nic가 실제 보다 높습니다.

## <a name="dependency-visualization-in-azure-government"></a>Azure Government에서 종속성 시각화

Azure Migrate는 종속성 시각화 기능의 서비스 맵에 따라 달라 집니다. 서비스 맵 현재 Azure Government에서 사용할 수 없기 때문에 Azure Government에서이 기능을 사용할 수 없습니다.

## <a name="dependencies-dont-show-after-agent-install"></a>에이전트 설치 후 종속성이 표시 되지 않음

온-프레미스 Vm에서 종속성 시각화 에이전트를 설치 하 고 나면 Azure Migrate는 일반적으로 포털에서 종속성을 표시 하는 데 15-30 분이 소요 됩니다. 30 분 넘게 대기한 경우 Microsoft Monitoring Agent (MMA)가 Log Analytics 작업 영역에 연결할 수 있는지 확인 합니다.

Windows VM의 경우
1. 제어판에서 MMA를 시작 합니다.
2. **Microsoft Monitoring Agent 속성**  >  **Azure Log Analytics (OMS)** 에서 작업 영역의 **상태가** 녹색 인지 확인 합니다.
3. 상태가 녹색이 아닌 경우 작업 영역을 제거 하 고 다시 MMA에 추가 하십시오.

    ![MMA 상태](./media/troubleshoot-assessment/mma-properties.png)

Linux Vm의 경우 MMA 및 종속성 에이전트의 설치 명령이 성공 했는지 확인 합니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

- **MMS 에이전트**: 지원 되는 [Windows](../azure-monitor/platform/agents-overview.md#supported-operating-systems)및 [Linux](../azure-monitor/platform/agents-overview.md#supported-operating-systems) 운영 체제를 검토 합니다.
- **종속성 에이전트**: 지원 되는 [Windows 및 Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) 운영 체제입니다.

## <a name="visualize-dependencies-for--hour"></a>> 시간에 대 한 종속성 시각화

에이전트 없는 종속성 분석을 사용 하면 최대 30 일 동안 종속성을 시각화 하거나 지도에서 내보낼 수 있습니다.

에이전트 기반 종속성 분석을 사용 하면 지난 달의 특정 날짜로 다시 이동할 수 Azure Migrate 있지만 종속성을 시각화할 수 있는 최대 기간은 1 시간입니다. 예를 들어, 종속성 맵의 기간 기능을 사용 하 여 어제 종속성을 볼 수 있지만 1 시간 동안만 볼 수 있습니다. 그러나 Azure Monitor 로그를 사용 하 여 더 긴 기간 동안 [종속성 데이터를 쿼리할](./how-to-create-group-machine-dependencies.md) 수 있습니다.

## <a name="visualized-dependencies-for--10-machines"></a>> 10 대의 컴퓨터에 대 한 종속성 시각화

에이전트 기반 종속성 분석을 통해 Azure Migrate 서버 평가에서 Vm이 최대 10 개 포함 된 [그룹에 대 한 종속성을 시각화할](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) 수 있습니다. 규모가 큰 그룹의 경우 종속성을 시각화 하기 위해 Vm을 더 작은 그룹으로 분할 하는 것이 좋습니다.


## <a name="machines-show-install-agent"></a>컴퓨터에 "에이전트 설치"가 표시 됩니다.

종속성 시각화를 사용 하 여 Azure에 컴퓨터를 마이그레이션한 후에는 다음과 같은 동작으로 인해 컴퓨터에서 "종속성 보기" 대신 "에이전트 설치" 작업을 표시할 수 있습니다.


- Azure로 마이그레이션한 후 온-프레미스 컴퓨터는 꺼지고 동일한 Vm이 Azure에서 분리 됩니다. 이러한 컴퓨터는 다른 MAC 주소를 갖습니다.
- 컴퓨터에는 온-프레미스 IP 주소를 보유 했는지 여부에 따라 다른 IP 주소를 사용할 수도 있습니다.
- MAC 주소와 IP 주소가 온-프레미스와 다른 경우 Azure Migrate 온-프레미스 컴퓨터를 서비스 맵 종속성 데이터와 연결 하지 않습니다. 이 경우 종속성을 보는 대신 에이전트를 설치 하는 옵션이 표시 됩니다.
- Azure로 테스트를 마이그레이션한 후 온-프레미스 컴퓨터는 정상적으로 켜 집니다. Azure에서 분리 하는 동일한 컴퓨터는 다른 MAC 주소를 획득 하 고 다른 IP 주소를 획득할 수 있습니다. 이러한 컴퓨터에서 나가는 Azure Monitor 로그 트래픽을 차단 하지 않는 한 Azure Migrate 온-프레미스 컴퓨터를 서비스 맵 종속성 데이터와 연결 하지 않으므로 종속성을 볼 수 있는 대신 에이전트를 설치 하는 옵션이 표시 됩니다.

## <a name="dependencies-export-csv-shows-unknown-process"></a>종속성 내보내기 CSV는 "알 수 없는 프로세스"를 표시 합니다.
에이전트 없는 종속성 분석에서 프로세스 이름은 최상의 노력으로 캡처됩니다. 특정 시나리오에서는 원본 및 대상 서버 이름과 대상 포트가 캡처되고 종속성의 양쪽 end에서 프로세스 이름을 결정 하는 것은 불가능 합니다. 이러한 경우 프로세스는 "알 수 없는 프로세스"로 표시 됩니다.

## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-server-assessment"></a>서버 평가에서 작업 영역을 구성 하려고 할 때 내 Log Analytics 작업 영역이 나열 되지 않음
Azure Migrate는 현재 미국 동부, 동남 아시아 및 서유럽 지역에서 OMS 작업 영역을 만들 수 있도록 지원합니다. 다른 지역의 Azure Migrate 외부에서 작업 영역을 만든 경우 현재는 Azure Migrate 프로젝트에 연결할 수 없습니다.


## <a name="capture-network-traffic"></a>네트워크 트래픽 캡처

다음과 같이 네트워크 트래픽 로그를 수집 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. F12 키를 눌러 개발자 도구를 시작 합니다. 필요한 경우  **탐색에서 항목 지우기** 설정을 선택 취소 합니다.
3. **네트워크** 탭을 선택 하 고 네트워크 트래픽 캡처를 시작 합니다.
   - 크롬에서 **Preserve log**를 클릭합니다. 자동으로 기록이 시작됩니다. 빨간색 원은 트래픽이 캡처되고 있음을 나타냅니다. 빨간색 원이 나타나지 않으면 시작할 검정색 원을 선택 합니다.
   - Microsoft Edge 및 Internet Explorer에서 기록이 자동으로 시작 됩니다. 그렇지 않으면 녹색 재생 단추를 선택 합니다.
4. 오류를 재현해 봅니다.
5. 기록하는 동안 오류가 발생하면 기록을 중지하고 기록된 활동의 복사본을 저장합니다.
   - Chrome에서 마우스 오른쪽 단추를 클릭 하 고 **콘텐츠를 사용 하 여 HAR로 저장**을 선택 합니다. 이 작업은 로그를 압축 하 고 har 파일로 내보냅니다.
   - Microsoft Edge 또는 Internet Explorer에서 **캡처된 트래픽 내보내기** 옵션을 선택 합니다. 이 작업은 로그를 압축 하 고 내보냅니다.
6. **콘솔** 탭을 선택 하 여 경고 또는 오류를 확인 합니다. 콘솔 로그를 저장하려면:
   - 크롬의 경우 콘솔 로그에서 아무 위치를 마우스 오른쪽 단추로 클릭합니다. 다른 **이름으로 저장**을 선택 하 고 로그를 내보내고 압축 합니다.
   - Microsoft Edge 또는 Internet Explorer에서 오류를 마우스 오른쪽 단추로 클릭 하 고 **모두 복사**를 선택 합니다.
7. 개발자 도구를 닫습니다.


## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>내 평가에서 운영 체제 데이터는 어디에서 검색 되나요?

- VMware Vm의 경우 기본적으로 vCenter에서 제공 하는 운영 체제 데이터입니다. 
   - VMware linux Vm의 경우 응용 프로그램 검색을 사용 하는 경우 게스트 VM에서 OS 세부 정보를 인출 합니다. 평가에서 OS 세부 정보를 확인 하려면 검색 된 서버 보기로 이동 하 여 "운영 체제" 열의 값 위에 마우스를 올려 놓으면 됩니다. 표시 되는 텍스트에서 표시 되는 OS 데이터가 vCenter server 또는 VM 자격 증명을 사용 하 여 게스트 VM에서 수집 되었는지 확인할 수 있습니다. 
   - Windows Vm의 경우 운영 체제 세부 정보는 항상 vCenter Server에서 인출 됩니다.
- Hyper-v Vm의 경우 운영 체제 데이터가 Hyper-v 호스트에서 수집 됩니다.
- 물리적 서버의 경우 서버에서 인출 됩니다.

## <a name="next-steps"></a>다음 단계

평가를 [만들거나](how-to-create-assessment.md) [사용자 지정](how-to-modify-assessment.md) 합니다.
