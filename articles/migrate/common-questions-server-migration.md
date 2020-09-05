---
title: Azure Migrate Server 마이그레이션에 대 한 일반적인 질문
description: Azure Migrate Server 마이그레이션을 사용 하 여 컴퓨터를 마이그레이션하는 방법에 대 한 일반적인 질문에 대 한 답변을 받으세요.
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: b0ae28fc387125b198bed202d857c3b9ecdd44bb
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050661"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate 서버 마이그레이션: 일반적인 질문

이 문서에서는 Azure Migrate에 대 한 일반적인 질문에 답변 합니다. 서버 마이그레이션 도구. 다른 질문이 있는 경우 다음 리소스를 확인 하세요.

- Azure Migrate에 대 한 [일반적인 질문](resources-faq.md)
- [Azure Migrate 어플라이언스](common-questions-appliance.md) 에 대 한 질문
- [검색, 평가 및 종속성 시각화](common-questions-discovery-assessment.md) 에 대 한 질문
- [Azure Migrate 포럼](https://aka.ms/AzureMigrateForum) 에서 질문에 대 한 답변 받기

## <a name="where-should-i-install-the-replication-appliance-for-agent-based-migrations"></a>에이전트 기반 마이그레이션의 복제 어플라이언스를 설치 해야 하는 위치는 어디 인가요?

복제 어플라이언스는 전용 컴퓨터에 설치 해야 합니다. 복제하려는 원본 머신이나 이전에 설치한 Azure Migrate 검색 및 평가 어플라이언스에 복제 어플라이언스를 설치하면 안 됩니다. 자세한 내용은 [자습서](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines) 를 참조 하세요.

## <a name="how-can-i-migrate-my-aws-ec2-instances-to-azure"></a>AWS EC2 인스턴스를 Azure로 마이그레이션하려면 어떻게 하나요?

AWS EC2 인스턴스를 검색 하 고, 평가 하 고, Azure로 마이그레이션하려면이 [문서](https://docs.microsoft.com/azure/migrate/tutorial-migrate-aws-virtual-machines) 를 검토 하세요.

## <a name="can-i-migrate-aws-vms-running-amazon-linux-operating-system"></a>Amazon Linux 운영 체제를 실행 하는 AWS Vm을 마이그레이션할 수 있나요?

Amazon linux OS는 AWS 에서만 지원 되므로 Amazon Linux를 실행 하는 Vm은 있는 그대로 마이그레이션할 수 없습니다.
Amazon Linux에서 실행 되는 워크 로드를 마이그레이션하려면 Azure에서 CentOS/RHEL VM을 실행 하 고 관련 워크 로드 마이그레이션 방법을 사용 하 여 AWS Linux 컴퓨터에서 실행 되는 워크 로드를 마이그레이션할 수 있습니다. 예를 들어 워크 로드에 따라 웹 서버에 대 한 데이터베이스 또는 배포 도구 등의 마이그레이션에 도움이 되는 워크 로드 별 도구가 있을 수 있습니다.

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Azure Migrate로 마이그레이션에 대해 지원 되는 지역은 무엇 인가요?

[퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)에 대해 지원되는 지역을 검토합니다.

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-regions"></a>동일한 Azure Migrate 프로젝트를 사용 하 여 여러 지역으로 마이그레이션할 수 있나요?

Azure Migrate 프로젝트에서 여러 영역에 대 한 평가를 만들 수 있지만 하나의 Azure Migrate 프로젝트를 사용 하 여 하나의 Azure 지역에만 서버를 마이그레이션할 수 있습니다. 로 마이그레이션해야 하는 각 지역에 대 한 추가 Azure Migrate 프로젝트를 만들 수 있습니다.

- 에이전트 없는 VMware 마이그레이션의 경우 첫 번째 복제를 사용 하도록 설정 하면 대상 지역이 잠깁니다.
- 에이전트 기반 마이그레이션 (VMware, 물리적 서버 및 다른 클라우드의 서버)의 경우 복제 어플라이언스를 설정 하는 동안 포털에서 "리소스 만들기" 단추를 클릭 하면 대상 지역이 잠깁니다.
- 에이전트 없는 Hyper-v 마이그레이션의 경우 Hyper-v 복제 공급자를 설정 하는 동안 포털에서 "리소스 만들기" 단추를 클릭 하면 대상 지역이 잠깁니다.

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-subscriptions"></a>동일한 Azure Migrate 프로젝트를 사용 하 여 여러 구독으로 마이그레이션할 수 있나요? 

예, Azure Migrate 프로젝트에 대해 동일한 대상 지역의 여러 구독으로 마이그레이션할 수 있습니다. 컴퓨터 또는 컴퓨터 집합에 대 한 복제를 사용 하도록 설정 하는 동안 대상 구독을 선택할 수 있습니다. 대상 지역은 에이전트 없는 VMware 마이그레이션에 대 한 첫 번째 복제 후 및 에이전트 기반 마이그레이션 및 에이전트 없는 Hyper-v 마이그레이션에 대 한 복제 어플라이언스 및 Hyper-v 공급자 설치 중에 각각 잠겨 있습니다.

## <a name="what-are-the-migration-options-in-azure-migrate-server-migration"></a>Azure Migrate의 마이그레이션 옵션은 다음과 같습니다. 서버 마이그레이션?

Azure Migrate: 서버 마이그레이션 도구는 Azure에 대 한 원본 서버/v m의 마이그레이션을 수행 하는 두 가지 옵션, 즉 에이전트 없는 마이그레이션 및 에이전트 기반 마이그레이션을 제공 합니다.

선택한 마이그레이션 옵션에 관계 없이 Azure 마이그레이션을 사용 하 여 서버를 마이그레이션하는 첫 번째 단계는 서버에 대 한 복제를 사용 하도록 설정 하는 것입니다. 이렇게 하면 Azure에 대 한 v m/서버 데이터의 초기 복제가 수행 됩니다. 초기 복제가 완료 된 후에는 증분 데이터를 Azure로 마이그레이션하기 위해 지속적인 복제 (지속적인 델타 동기화)가 설정 됩니다. 작업이 델타 동기화 단계에 도달 하면 언제 든 지 Azure로 마이그레이션하도록 선택할 수 있습니다.  

마이그레이션 옵션을 결정 하는 동안 염두에 두어야 할 몇 가지 고려 사항은 다음과 같습니다.

**에이전트 없는 마이그레이션은** 마이그레이션하는 원본 v m/서버에 소프트웨어 (에이전트)를 배포할 필요가 없습니다. 에이전트 없는 옵션은 가상화 공급자가 제공 하는 기능과 통합 하 여 복제를 오케스트레이션 합니다.
[VMware vm](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware) 및 [hyper-v Vm](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v)에는 에이전트 없는 복제 옵션을 사용할 수 있습니다.

**에이전트 기반 마이그레이션을 사용** 하려면 마이그레이션할 원본 v m/컴퓨터에 Azure Migrate 소프트웨어 (에이전트)를 설치 해야 합니다. 에이전트 기반 옵션은 복제 기능에 대 한 가상화 플랫폼을 사용 하지 않으므로 x86/x64 아키텍처를 실행 하는 서버와 에이전트 기반 복제 방법에서 지 원하는 운영 체제 버전에 사용 될 수 있습니다.

에이전트 기반 마이그레이션 옵션은 [VMware vm](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware-agent), [hyper-v vm](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines), [물리적 서버](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines), [AWS에서 실행 되는 vm](https://docs.microsoft.com/azure/migrate/tutorial-migrate-aws-virtual-machines), gcp에서 실행 중인 vm 또는 다른 가상화 공급자에서 실행 되는 vm에 사용할 수 있습니다. 에이전트 기반 마이그레이션은 마이그레이션을 위해 컴퓨터를 물리적 서버로 처리 합니다.

에이전트 없는 마이그레이션은 지원 되는 시나리오 (VMWare 및 Hyper-v)의 에이전트 기반 복제 옵션에 대 한 추가 편의성과 단순성을 제공 하지만 다음과 같은 사용 사례에 에이전트 기반 시나리오를 사용 하는 것을 고려할 수 있습니다.

- IOPS 제한 환경: 에이전트 없는 복제는 스냅숏을 사용 하 고 저장소 IOPS/대역폭을 소비 합니다. 환경에서 저장소/IOPS에 대 한 제약 조건이 있는 경우 에이전트 기반 마이그레이션 방법을 사용 하는 것이 좋습니다.
- VCenter Server 없는 경우 VMware Vm을 실제 서버로 처리 하 고 에이전트 기반 마이그레이션 워크플로를 사용할 수 있습니다.

자세히 알아보려면이 [문서](https://docs.microsoft.com/azure/migrate/server-migrate-overview) 를 검토 하 여 VMware 마이그레이션의 마이그레이션 옵션을 비교 하세요.

## <a name="how-does-agentless-migration-work"></a>에이전트 없는 마이그레이션은 어떻게 작동 하나요?

Azure Migrate: 서버 마이그레이션은 VMware 가상 컴퓨터 및 Windows 또는 Linux를 실행 하는 Hyper-v 가상 컴퓨터의 마이그레이션에 대 한 에이전트 없는 복제 옵션을 제공 합니다. 또한이 도구는 물리적 서버를 마이그레이션하는 데 사용할 수 있는 Windows 및 Linux 서버 뿐만 아니라 VMware, Hyper-v, AWS, GCP 등의 x86/x64 가상 컴퓨터에 대 한 추가 에이전트 기반 복제 옵션도 제공 합니다. 에이전트 기반 복제 옵션을 사용 하려면 마이그레이션해야 하는 서버/가상 컴퓨터에 에이전트 소프트웨어를 설치 해야 합니다. 반면 에이전트 없는 옵션에서는 가상 컴퓨터 자체에 소프트웨어를 설치 하지 않아도 되므로 에이전트 기반 복제 옵션에 대 한 추가 편의성과 단순성을 제공 합니다.

에이전트 없는 복제 옵션은 가상화 공급자 (VMware, Hyper-v)에서 제공 하는 메커니즘을 사용 하 여 작동 합니다. VMware 가상 컴퓨터의 경우 에이전트 없는 복제 메커니즘은 VMware 스냅숏과 VMware 변경 된 블록 추적 기술을 사용 하 여 가상 컴퓨터 디스크에서 데이터를 복제 합니다. 이 메커니즘은 여러 백업 제품에서 사용 하는 메커니즘과 비슷합니다. Hyper-v 가상 컴퓨터의 경우 에이전트 없는 복제 메커니즘은 VM 스냅숏과 Hyper-v 복제본의 변경 내용 추적 기능을 사용 하 여 가상 컴퓨터 디스크에서 데이터를 복제 합니다.

가상 컴퓨터에 대해 복제를 구성 하는 경우 먼저 초기 복제 단계를 거칩니다. 초기 복제 중에 VM 스냅숏이 생성 되 고 스냅숏 디스크의 전체 데이터 사본이 구독의 관리 디스크에 복제 됩니다. VM에 대 한 초기 복제가 완료 되 면 복제 프로세스가 증분 복제 (델타 복제) 단계로 전환 됩니다. 증분 복제 단계에서 마지막으로 완료 된 복제 주기 이후에 발생 한 데이터 변경 내용이 정기적으로 복제 되어 복제본 관리 디스크에 적용 되므로 복제가 VM에서 발생 하는 변경 내용과 동기화 된 상태로 유지 됩니다. VMware 가상 컴퓨터의 경우 VMware 변경 된 블록 추적 기술은 복제 주기 간의 변경 내용을 추적 하는 데 사용 됩니다. 복제 주기를 시작할 때 VM 스냅숏이 생성 되 고 변경 된 블록 추적이 현재 스냅숏과 마지막으로 복제 된 스냅숏 간의 변경 내용을 가져오는 데 사용 됩니다. 이렇게 하면 VM에 대 한 복제를 동기화 된 상태로 유지 하기 위해 마지막으로 완료 된 복제 주기 이후에 변경 된 데이터만 복제 해야 합니다. 각 복제 주기가 끝나면 스냅숏이 해제 되 고 가상 머신에 대 한 스냅숏 통합이 수행 됩니다. 마찬가지로 hyper-v 가상 컴퓨터의 경우 Hyper-v 복제본 변경 내용 추적 엔진은 연속 복제 주기 간의 변경 내용을 추적 하는 데 사용 됩니다.
복제 하는 가상 머신에서 마이그레이션 작업을 수행 하는 경우 온-프레미스 가상 머신을 종료 하 고, 데이터 손실을 방지 하기 위해 최종 증분 복제를 한 번 수행할 수 있습니다. 마이그레이션 옵션을 수행할 때 가상 머신에 해당 하는 복제본 관리 디스크는 Azure에서 가상 머신을 만드는 데 사용 됩니다.

시작 하려면 [VMware 에이전트 없는 마이그레이션](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware) 및 [hyper-v 에이전트 없는 마이그레이션](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) 자습서를 참조 하세요.

## <a name="how-does-agent-based-migration-work"></a>에이전트 기반 마이그레이션은 어떻게 작동 하나요?

VMware 가상 컴퓨터 및 Hyper-v 가상 컴퓨터에 대 한 에이전트 없는 마이그레이션 옵션 외에도 서버 마이그레이션 도구는 물리적 서버에서 실행 되는 Windows 및 Linux 서버를 마이그레이션하거나 VMware, Hyper-v, AWS, Google Cloud Platform 등에서 x86/x64 가상 컴퓨터로 실행 되는 에이전트 기반 마이그레이션 옵션을 제공 합니다.

에이전트 기반 마이그레이션 방법은 마이그레이션되는 서버에 설치 된 에이전트 소프트웨어를 사용 하 여 서버 데이터를 Azure에 복제 합니다. 복제 프로세스는 복제 데이터를 복제 어플라이언스 또는 구성 서버 (또는 스케일 아웃 프로세스 서버) 라고 하는 전용 복제 서버로 릴레이 하는 오프 로드 아키텍처를 사용 합니다. 에이전트 기반 마이그레이션 옵션의 작동 방식에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/migrate/agent-based-migration-architecture) . 

참고: 복제 어플라이언스는 Azure Migrate 검색 기기와 다르며 별도/전용 컴퓨터에 설치 해야 합니다.

## <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>내 마이그레이션의 대역폭 요구 사항을 어떻게 할까요? 계기 인가요?

Azure로의 데이터 복제에 대 한 대역폭은 다양 한 요인에 따라 달라 지 며 온-프레미스 Azure Migrate 어플라이언스에서 Azure로 데이터를 읽고 복제할 수 있는 속도의 기능입니다. 복제는 초기 복제 및 델타 복제의 두 단계로 이루어집니다.

VM에 대 한 복제가 시작 되 면 디스크의 전체 복사본이 복제 되는 초기 복제 주기가 발생 합니다. 초기 복제를 완료 한 후에는 증분 복제 주기 (델타 주기)가 주기적으로 예약 되어 이전 복제 주기 이후에 발생 한 변경 내용을 전송 합니다.

### <a name="agentless-vmware-vm-migration"></a>에이전트 없는 VMware VM 마이그레이션

초기 복제를 완료 하는 데 필요한 데이터 볼륨을 기반으로 대역폭 요구 사항을 해결할 수 있습니다. 실제 창 이전에는 테스트 마이그레이션을 수행 하 고 기간 동안 가동 중지 시간을 최소화할 수 있도록 실제 마이그레이션 기간 이전에 초기 복제를 3-4 완료 하는 데 충분 한 시간을 제공 하는 것이 좋습니다.

다음 수식을 사용 하 여 에이전트 없는 VMware VM 마이그레이션에 필요한 대역폭 또는 시간을 예측할 수 있습니다.

초기 복제를 완료 하는 데 소요 되는 시간 = {디스크 크기 (또는 사용 가능한 경우 사용 된 크기) * 0.7 (30% 압축 평균 – 보수적인 예상치)}/>

### <a name="agent-based-vmware-vm-migration"></a>에이전트 기반 VMware VM 마이그레이션

에이전트 기반 복제 방법의 경우 Deployment planner는 데이터 변동에 대 한 환경을 프로 파일링 하 고 필요한 대역폭 요구 사항을 예측 하는 데 도움이 될 수 있습니다. 자세히 알아보려면이 [문서](https://docs.microsoft.com/azure/migrate/agent-based-migration-architecture#plan-vmware-deployment)를 참조 하세요. 

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>에이전트 없는 VMware 복제에 Azure Migrate 어플라이언스를 사용 하 여에서 복제를 어떻게 할까요? 제한 하 시겠습니까?  

NetQosPolicy를 사용 하 여 제한할 수 있습니다. 예를 들면 다음과 같습니다.

NetQosPolicy에서 사용할 AppNamePrefix는 "GatewayWindowsService.exe"입니다. Azure Migrate 어플라이언스에서 정책을 만들어 다음과 같은 정책을 만들어 어플라이언스에서 복제 트래픽을 제한할 수 있습니다.

New-NetQosPolicy-Name "ThrottleReplication"-AppPathNameMatchCondition "GatewayWindowsService.exe"-ThrottleRateActionBitsPerSecond 1mb

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>온-프레미스 환경에서 Azure로 데이터를 전송 하는 방법은 무엇 인가요? 전송 전에 암호화 되나요?

에이전트 없는 복제 사례의 Azure Migrate 어플라이언스는 데이터를 압축 하 고 업로드 하기 전에 암호화 합니다. 데이터는 https를 통해 보안 통신 채널을 통해 전송 되며 TLS 1.2 이상을 사용 합니다. 또한 Azure Storage는 데이터를 클라우드 (미사용 암호화)에 보관 하는 경우 자동으로 암호화 합니다.  

## <a name="how-does-churn-rate-affect-agentless-replication"></a>변동 률은 에이전트 없는 복제에 어떤 영향을 미칩니까?

에이전트 없는 복제는 데이터를 접기 하므로 변동 률 *패턴* 은 *변동 률*보다 더 중요 합니다. 다시 파일을 다시 쓰면 속도가 큰 영향을 주지 않습니다. 그러나 다른 모든 섹터가 기록 되는 패턴은 다음 주기에 높은 변동 (code churn)을 발생 시킵니다. 전송 되는 데이터의 양을 최소화 하기 때문에 다음 주기를 예약 하기 전에 데이터를 최대한 활용할 수 있습니다.

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>복제 주기는 얼마나 자주 예약 됩니까?

다음 복제 주기를 예약 하는 수식은 (이전 주기 시간/2) 또는 1 시간 중에서 더 큰 쪽입니다.

예를 들어 VM에서 델타 주기를 4 시간으로 사용 하는 경우 다음 주기가 2 시간 내에 예약 되며 다음 시간에는 예약 되지 않습니다. 첫 번째 델타 주기가 즉시 예약 되는 경우 프로세스는 초기 복제 직후에 다릅니다.

## <a name="how-do-i-migrate-windows-server-2003-running-on-vmwarehyper-v-to-azure"></a>VMware/Hyper-v에서 실행 되는 Windows Server 2003를 Azure로 마이그레이션할 어떻게 할까요? 있나요?

[Windows Server 2003 연장 지원은](https://go.microsoft.com/fwlink/?linkid=2140400) 2015 년 7 월 14 일에 종료 되었습니다.  Azure 지원 팀은 Azure에서 Windows Server 2003를 실행 하는 문제를 해결 하는 데 도움이 됩니다. 그러나이 지원은 OS 수준 문제 해결 또는 패치가 필요 하지 않은 문제로 제한 됩니다.
최신 버전의 Windows Server를 실행 하는 Azure 인스턴스로 응용 프로그램을 마이그레이션하는 것이 Azure 클라우드의 유연성과 안정성을 효과적으로 활용 하는 데 권장 되는 방법입니다.

그러나 Windows Server 2003를 Azure로 마이그레이션하도록 선택 하는 경우에는 Azure Migrate: 서버 마이그레이션 도구를 사용할 수 있습니다. Windows Server가 VMware 또는 Hyper-v에서 실행 되는 VM 인 경우이 문서를 검토 하 여 [Windows server 2003 컴퓨터에서 마이그레이션을 준비](https://go.microsoft.com/fwlink/?linkid=2140302)합니다.

## <a name="what-is-the-difference-between-the-test-migration-and-migrate-operations"></a>테스트 마이그레이션과 마이그레이션 작업의 차이점은 무엇 인가요?

테스트 마이그레이션은 실제 마이그레이션 전에 마이그레이션을 테스트 하 고 유효성을 검사 하는 방법을 제공 합니다. 테스트 마이그레이션은 Azure의 샌드박스 환경에서 Vm을 복제 하는 테스트 복사본을 만들 수 있도록 하 여 작동 합니다. 샌드박스 환경은 사용자가 지정 하는 테스트 가상 네트워크에 의해 경계선 됩니다. 테스트 마이그레이션 작업은 중단 되지 않으며, 응용 프로그램이 계속 해 서 소스에서 실행 되며, 격리 된 샌드박스 환경에서 복제 된 복사본에 대해 테스트를 수행할 수 있습니다. 마이그레이션 유효성을 검사 하 고, 앱 테스트를 수행 하 고, 실제 마이그레이션 전에 모든 문제를 해결 하기 위해 필요에 따라 여러 테스트를 수행할 수 있습니다.

## <a name="will-windows-server-2008-and-2008-r2-be-supported-in-azure-after-migration"></a>마이그레이션 후 Windows Server 2008 및 2008 r 2가 Azure에서 지원 되나요?

온-프레미스 Windows Server 2008 및 2008 R2 서버를 Azure virtual machines로 마이그레이션하고, 지원 날짜가 끝난 후 3 년 동안 연장 된 보안 업데이트를 사용할 수 있습니다. Azure Migrate: 서버 마이그레이션 도구를 사용 하 여 Windows Server 2008 및 2008 R2 워크 로드를 마이그레이션할 수 있습니다.

## <a name="is-there-a-rollback-option-for-azure-migrate"></a>Azure Migrate에 대 한 Rollback 옵션이 있나요?

테스트 마이그레이션 옵션을 사용 하 여 Azure에서 응용 프로그램 기능 및 성능에 대 한 유효성을 검사할 수 있습니다. 원하는 수의 테스트 마이그레이션을 수행 하 고 테스트 마이그레이션 작업을 통해 확신을 설정한 후 최종 마이그레이션을 실행할 수 있습니다. 테스트 마이그레이션은 온-프레미스 컴퓨터에 영향을 주지 않습니다 .이 컴퓨터는 작동 상태를 유지 하 고 실제 마이그레이션을 수행할 때까지 복제를 계속 합니다. 테스트 마이그레이션 UAT 동안 오류가 발생 한 경우 최종 마이그레이션을 연기 하 고 원본 v m/서버를 계속 실행 하 고 Azure에 복제 하도록 선택할 수 있습니다. 오류를 해결 한 후 최종 마이그레이션을 다시 시도할 수 있습니다.  
참고: Azure에 대 한 최종 마이그레이션을 수행 하 고 온-프레미스 원본 컴퓨터가 종료 된 후에는 Azure에서 온-프레미스 환경으로의 롤백을 수행할 수 없습니다.

## <a name="can-i-select-the-virtual-network-and-subnet-to-use-for-test-migrations"></a>테스트 마이그레이션에 사용할 Virtual Network 및 서브넷을 선택할 수 있나요?

테스트 마이그레이션의 Virtual Network를 선택할 수 있습니다. 서브넷은 다음 논리에 따라 자동으로 선택 됩니다.

- 복제를 사용 하도록 설정 하는 동안 기본이 아닌 대상 서브넷을 입력으로 지정 하는 경우 테스트 마이그레이션에 대해 선택한 Virtual Network에서 이름이 같은 서브넷을 사용 하 여 우선 순위를 Azure Migrate 합니다.
- 동일한 이름을 가진 서브넷을 찾을 수 없는 경우 Azure Migrate는 사용 Application Gateway 가능한 첫 번째 서브넷을 선택 합니다.

## <a name="why-is-the-test-migration-button-disabled-for-my-server"></a>내 서버에 대해 테스트 마이그레이션 단추가 사용 되지 않는 이유는 무엇 인가요?

테스트 마이그레이션 단추는 다음과 같은 시나리오에서 사용할 수 없는 상태일 수 있습니다.

- VM에 대 한 초기 복제 (IR)가 완료 될 때까지 테스트 마이그레이션을 시작할 수 없습니다. IR 프로세스가 완료 될 때까지 테스트 마이그레이션 단추를 사용할 수 없습니다. VM이 델타 동기화 단계에 있으면 테스트 마이그레이션을 수행할 수 있습니다.
- 테스트 마이그레이션이 이미 완료 되었지만 해당 VM에 대 한 테스트 마이그레이션 정리가 수행 되지 않은 경우 단추를 사용 하지 않도록 설정할 수 있습니다. 테스트 마이그레이션 정리를 수행 하 고 작업을 다시 시도 하세요.

## <a name="what-happens-if-i-dont-clean-up-my-test-migration"></a>내 테스트 마이그레이션을 정리 하지 않으면 어떻게 되나요?

테스트 마이그레이션은 복제 된 데이터를 사용 하 여 테스트 Azure VM을 만들어 실제 마이그레이션을 시뮬레이트합니다. 서버는 "-test" 접미사를 사용 하 여 복제 된 데이터의 지정 시간 복사본을 대상 리소스 그룹에 배포 합니다 (복제를 사용 하도록 설정 하는 동안 선택 됨). 테스트 마이그레이션은 서버 기능의 유효성을 검사 하 여 마이그레이션 후 문제가 최소화 되도록 하기 위한 것입니다. 테스트 마이그레이션이 사후 테스트를 정리 하지 않은 경우 테스트 가상 머신은 계속 해 서 Azure에서 실행 되며 요금이 부과 됩니다. 테스트 마이그레이션 후 정리 하려면 서버 마이그레이션 도구의 컴퓨터 복제 뷰로 이동 하 여 컴퓨터에서 ' 테스트 마이그레이션 정리 ' 작업을 사용 합니다.

## <a name="can-i-migrate-active-directory-domain-controllers-using-azure-migrate"></a>Azure Migrate를 사용 하 여 Active Directory 도메인 컨트롤러를 마이그레이션할 수 있나요?

서버 마이그레이션 도구는 응용 프로그램에 독립적 이며 대부분의 응용 프로그램에서 작동 합니다. 서버 마이그레이션 도구를 사용 하 여 서버를 마이그레이션하면 서버에 설치 된 모든 응용 프로그램이 함께 마이그레이션됩니다. 그러나 일부 응용 프로그램의 경우 서버 마이그레이션 이외의 다른 마이그레이션 방법이 마이그레이션에 더 적합할 수 있습니다.  Active Directory azure 환경에 온-프레미스 사이트가 연결 된 하이브리드 환경의 경우 Azure에서 추가 도메인 컨트롤러를 추가 하 고 Active Directory 복제를 설정 하 여 디렉터리를 Azure로 확장할 수 있습니다. 자체 도메인 컨트롤러를 요구 하거나 샌드박스 환경에서 응용 프로그램을 테스트 하는 Azure의 격리 된 환경으로 마이그레이션하는 경우 서버 마이그레이션 도구를 사용 하 여 서버를 마이그레이션할 수 있습니다.

## <a name="what-happens-if-i-dont-stop-replication-after-migration"></a>마이그레이션 후 복제를 중지 하지 않으면 어떻게 되나요?

복제를 중지 하는 경우 Azure Migrate: 서버 마이그레이션 도구는 복제용으로 만들어진 구독에서 관리 디스크를 정리 합니다. 마이그레이션 후 복제를 중지 하지 않으면 이러한 디스크에 대 한 요금이 계속 청구 됩니다. 복제 중지는 이미 마이그레이션된 컴퓨터에 연결 된 디스크에 영향을 주지 않습니다.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>VMware Vm을 마이그레이션하려면 VMware vCenter가 필요 한가요?

VMware 에이전트 기반 또는 에이전트 없는 마이그레이션을 사용 하 여 [Vmware vm을 마이그레이션하려면](server-migrate-overview.md) vm이 있는 ESXi 호스트를 vCenter Server으로 관리 해야 합니다. VCenter Server 없는 경우 실제 서버로 마이그레이션하여 VMware Vm을 마이그레이션할 수 있습니다. [자세히 알아보기](migrate-support-matrix-physical-migration.md).

## <a name="can-i-upgrade-my-os-while-migrating"></a>마이그레이션하는 동안 OS를 업그레이드할 수 있나요?

Azure Migrate: 서버 마이그레이션 도구는 현재와 유사한 마이그레이션만 지원 합니다. 이 도구는 마이그레이션 중에 OS 버전을 업그레이드 하는 것을 지원 하지 않습니다. 마이그레이션된 컴퓨터는 원본 컴퓨터와 동일한 OS를 갖게 됩니다.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliances"></a>내 vCenter Server에서 Vm을 검색 하기 위해 두 개 이상의 어플라이언스를 배포 했습니다. 그러나 Vm을 마이그레이션하도록 시도할 때 어플라이언스 중 하나에 해당 하는 Vm만 표시 됩니다.

여러 어플라이언스를 설정하는 경우 제공된 vCenter 계정에 있는 VM 간에 겹치는 부분이 없어야 합니다. 이러한 겹치는 검색은 지원되지 않는 시나리오입니다.

## <a name="how-do-i-know-if-my-vm-was-successfully-migrated"></a>VM이 성공적으로 마이그레이션 되었는지 확인 하는 어떻게 할까요?

V m/서버를 성공적으로 마이그레이션한 후에 Virtual Machines 페이지에서 VM을 보고 관리할 수 있습니다. 마이그레이션된 VM에 연결하여 유효성을 검사합니다.
또는 작업에 대 한 ' 작업 상태 '를 검토 하 여 마이그레이션이 성공적으로 완료 되었는지 확인할 수 있습니다. 오류가 표시 되 면 문제를 해결 하 고 마이그레이션 작업을 다시 시도 합니다.

## <a name="can-i-consolidate-multiple-source-vms-into-one-vm-while-migrating"></a>마이그레이션하는 동안 여러 원본 Vm을 하나의 VM으로 통합할 수 있나요?

Azure Migrate 서버 마이그레이션 기능은 현재와 같은 마이그레이션 기능을 지원 합니다. 마이그레이션의 일부로 서버를 통합 하거나 운영 체제를 업그레이드 하는 것은 지원 되지 않습니다. 

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>에이전트 없는 복제는 VMware 서버에 어떤 영향을 미칩니까?

에이전트 없는 복제는 VMware vCenter Server 및 VMware ESXi 호스트에 몇 가지 성능 영향을 줍니다. 에이전트 없는 복제는 스냅숏을 사용 하므로 저장소에서 IOPS를 사용 하므로 일부 IOPS 저장소 대역폭이 필요 합니다. 사용자 환경에서 저장소 또는 IOPs에 대 한 제약 조건이 있는 경우 에이전트 없는 복제를 사용 하지 않는 것이 좋습니다.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Azure Gen 2로 UEFI Vm의 에이전트 없는 마이그레이션을 수행할 수 있나요?

아니요. [VMware 에이전트 기반 마이그레이션](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware-agent), [hyper-v 마이그레이션](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines)또는 [물리적 서버 마이그레이션](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines) 옵션을 사용 하 여 이러한 vm을 Gen 2 Azure vm으로 마이그레이션할 수 있습니다.

***참고:*** Azure에서 2 세대 UEFI를 지 원하는 적절 한 VM 크기를 선택 했는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Migrate 개요](migrate-services-overview.md)를 참조 하세요.
