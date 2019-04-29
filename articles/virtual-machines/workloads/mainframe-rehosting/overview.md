---
title: Azure virtual machines의 재호스팅 메인프레임
description: Microsoft Azure에서 virtual machines (Vm)를 사용 하는 IBM Z 기반 시스템과 같은 메인프레임 워크 로드를 다시 호스트 합니다.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: c1d7b52bdce77ca108781a999a8a85b3e3fca0b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61487482"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Azure virtual machines의 재호스팅 메인프레임

워크 로드 마이그레이션 메인프레임에서 환경을 클라우드로 인프라 현대화 하 고 종종 비용을 절감할 수 있습니다. 대부분의 워크로드는 데이터베이스 이름을 업데이트하는 등 코드를 약간만 변경하면 Azure로 전송할 수 있습니다.

일반적으로, 용어 *메인프레임* 대규모 컴퓨터 시스템을 의미 합니다. 특히에서 현재 사용 중인 대부분은 IBM 시스템 Z 서버 또는 MVS, DOS, VSE, os/390 또는 z/OS를 실행 하는 IBM plug-compatible 시스템입니다.

Azure 가상 머신 (VM)를 분리 하 여 단일 인스턴스에서 특정 응용 프로그램에 대 한 리소스 관리에 사용 됩니다. 메인프레임 IBM z/OS 등이 목적을 위해 논리 파티션 (LPARS)를 사용합니다. 메인프레임을 IBM Db2 데이터베이스에 대 한 연결 된 COBOL 프로그램과 CICS 지역에 대 한 하나의 LPAR 및 별도 LPAR를 사용할 수 있습니다. 일반적인 [Azure에서 n 계층 응용 프로그램](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) 각 계층에 대 한 서브넷으로 분할할 수 있습니다 가상 네트워크에 Azure Vm을 배포 합니다.

Azure Vm 메인프레임에 에뮬레이션 환경 및 리프트 앤 시프트 시나리오를 지 원하는 컴파일러를 실행할 수 있습니다. 개발 및 테스트 Azure 개발/테스트 환경에는 메인프레임 마이그레이션할 첫 번째 워크 로드 간에 자주 않습니다. 에뮬레이트할 수 있는 일반적인 서버 구성 요소는 다음 그림과 같이 온라인 트랜잭션 처리 (OLTP), batch 및 데이터 수집 시스템을 포함 합니다.

![Azure 에뮬레이션 환경 z/OS 기반 시스템을 실행할 수 있습니다.](media/01-overview.png)

파트너 솔루션을 사용 하 여 Azure에서 다시 호스트 된 다른 동안 일부 메인프레임 작업을 비교적 쉽게 Azure로 마이그레이션할 수 있습니다. 파트너 솔루션을 선택 하는 방법에 대 한 자세한 지침은 합니다 [Azure 메인프레임 마이그레이션 센터](https://azure.microsoft.com/migration/mainframe/) 수 있습니다.

## <a name="mainframe-migration"></a>메인프레임 마이그레이션

Rehost, 다시 작성, 대체 또는 사용 중지? IaaS 또는 PaaS? 메인프레임 응용 프로그램에 대 한 적합 한 마이그레이션 전략을 확인 하려면 참조는 [메인프레임 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) Azure 아키텍처 센터 가이드입니다.

## <a name="micro-focus-rehosting-platform"></a>마이크로 포커스 재호스팅 플랫폼

Micro Focus Enterprise Server에 사용할 수 있는 플랫폼을 다시 호스팅할 가장 큰 메인프레임 중 하나입니다. 사용 하 여 저렴 x86 z/OS 워크 로드를 실행할 Azure 플랫폼입니다.

시작하기:

- [Azure에서 엔터프라이즈 서버 및 엔터프라이즈 개발자 설치](./microfocus/set-up-micro-focus-azure.md)
- [Azure에서 엔터프라이즈 개발자에 대 한 CICS BankDemo 설정](./microfocus/demo.md)
- [엔터프라이즈 서버를 Azure에서 Docker 컨테이너에서 실행](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>Azure에서 TmaxSoft OpenFrame

TmaxSoft OpenFrame에 리프트 앤 시프트 시나리오에 사용 되는 인기 있는 메인프레임 재호스팅 솔루션입니다. Azure에서 OpenFrame 환경을 개발, 데모, 테스트 또는 프로덕션 워크 로드에 적합 합니다.

시작하기:

- [TmaxSoft OpenFrame 시작](./tmaxsoft/get-started.md)
- [전자책 다운로드](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

IBM Z 개발 및 테스트 환경 (IBM zD & T) 개발, 테스트 및 데모 z/OS 기반 응용 프로그램에 사용할 수 있는 Azure에서 비-프로덕션 환경을 설정 합니다.

Azure 에뮬레이션 환경 다양 한 종류의 응용 프로그램 개발자가 제어 배포 (ADCDs)를 통해 Z 인스턴스를 호스팅할 수 있습니다. Azure 및 Azure Stack에서 zD T Personal Edition, zD T 병렬 Sysplex, 및 zD & T Enterprise Edition을 실행할 수 있습니다.

시작하기:

- [IBM zD Azure에서 T 12.0 설정](./ibm/install-ibm-z-environment.md)
- [ZD (& T)에서 ADCD 설정](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>Azure의 IBM DB2 pureScale

IBM DB2 pureScale 환경을 Azure에 대 한 데이터베이스 클러스터를 제공합니다. 원본 환경에 일치 하지 않습니다 하지만 병렬 Sysplex 설치에서 실행 되는 z/OS 용 IBM DB2와 유사한 가용성과 확장성을 제공 합니다.

시작 하려면 참조 [Azure에서 IBM DB2 pureScale](/azure/virtual-machines/linux/ibm-db2-purescale-azure)합니다.

## <a name="considerations"></a>고려 사항

서비스 (IaaS)로 Azure 인프라에 메인프레임 워크 로드를 마이그레이션하는 경우에 Azure Vm을 포함 하 여 주문형, 확장 가능한 컴퓨팅 리소스의 여러 형식에서 선택할 수 있습니다. Azure의 범위를 제공 [Linux](/azure/virtual-machines/linux/overview) 하 고 [Windows](/azure/virtual-machines/windows/overview) Vm.

### <a name="compute"></a>컴퓨팅

Azure 계산 능력을 메인프레임 용량 특성과 비교합니다. 메인프레임 워크 로드를 Azure로 이동 하려는 경우에 가상 Cpu (MIPS) 초당 1 백만 지침의 메인프레임 메트릭을 비교 합니다. 

설명 하는 방법 [메인프레임 계산을 Azure로 이동](./concepts/mainframe-compute-azure.md)합니다.

### <a name="high-availability-and-failover"></a>고가용성 및 장애 조치(failover)

Azure 약정 기반 서비스 수준 계약 (Sla)을 제공합니다. 여러 9 가진 가용성은 기본값인 및 서비스의 로컬 또는 지역 기반 복제를 사용 하 여 Sla를 최적화할 수 있습니다. 전체 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)는 Azure의 보장된 가용성에 대해 전반적으로 설명합니다.

VM과 같은 Azure IaaS를 사용 하 여 특정 시스템 함수는 장애 조치 지원을 제공할-예를 들어 장애 조치 클러스터링 인스턴스 및 [가용성 집합](/azure/virtual-machines/windows/regions-and-availability#availability-sets)합니다. 서비스 (PaaS) 리소스로 Azure 플랫폼을 사용 하는 경우 플랫폼에서는 장애 조치를 자동으로 처리 합니다. 예를 들면 [Azure SQL Database](/azure/sql-database/sql-database-technical-overview) 하 고 [Azure Cosmos DB](/azure/cosmos-db/introduction)합니다.

### <a name="scalability"></a>확장성

메인프레임은 일반적으로 강화되는 반면 클라우드 환경은 스케일 아웃됩니다. Azure의 범위를 제공 [Linux](/azure/virtual-machines/linux/sizes) 하 고 [Windows](/azure/virtual-machines/windows/sizes) 요구 사항에 맞게 크기입니다. 클라우드를 위나 아래로 일치 정확한 사용자 사양에도 확장 됩니다. 전원, 저장소 및 서비스 계산 [확장](/azure/architecture/best-practices/auto-scaling) 사용량 기반 청구 모델에서 주문형입니다.

### <a name="storage"></a>Storage

클라우드에서 유연 하 고 확장 가능한 저장소 옵션의 범위 있고 필요한 것에 대해서만 비용을 지불 합니다. [Azure Storage](/azure/storage/common/storage-introduction)는 데이터 개체용으로 대폭 확장 가능한 개체 저장소, 클라우드용 파일 시스템 서비스, 안정적인 메시징 저장소 및 NoSQL 저장소를 제공합니다. VM의 경우에는 관리형 디스크와 비관리형 디스크에서 안전한 영구 디스크 스토리지가 제공됩니다.

설명 하는 방법 [메인프레임 저장소를 Azure로 이동](./concepts/mainframe-storage-azure.md)합니다.

### <a name="backup-and-recovery"></a>Backup 및 복구

고유한 재해 복구 사이트 유지 관리 하는 비용이 많이 드는 제안일 수 있습니다. Azure에 구현 하기 쉽게 하 고 비용 효율적인 옵션에 대 한 [백업](/azure/backup/backup-introduction-to-azure-backup)를 [복구](/azure/site-recovery/site-recovery-overview), 및 [중복성](/azure/storage/common/storage-redundancy) 또는 지역 수준에서 또는 지리적 중복을 통해.

## <a name="azure-government-for-mainframe-migrations"></a>메인프레임 마이그레이션에 대 한 azure Government

공공 부문 엔터티 보다 현대적이 고 유연한 플랫폼을 메인프레임 응용 프로그램을 이동할 환영 합니다. Microsoft Azure Government는 글로벌 Microsoft Azure 플랫폼의 인스턴스를 물리적으로 분리 된-연방, 주 및 지방 정부 시스템에 대 한 패키지 있습니다. 세계적 수준의 보안, 보호 및 규정 준수 서비스 미국 정부 기관 및 파트너를 위한 구체적으로 제공합니다.

Azure Government 이러한 유형의 환경 해야 하는 시스템에 대 한 FedRAMP 영향에 대 한는 Provisional Authority to Operate (P-ATO)를 획득 합니다.

시작 하려면 다운로드 [메인프레임 응용 프로그램에 대 한 Microsoft Azure Government 클라우드](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)합니다.

## <a name="next-steps"></a>다음 단계

요청 우리의 [파트너](partner-workloads.md) 마이그레이션하거나 메인프레임 응용 프로그램을 다시 호스트 하는 데 있습니다. 파트너 솔루션을 선택 하는 방법에 대 한 자세한 지침에 대 한 참조를 [플랫폼 현대화 Alliance](https://www.platformmodernization.org/pages/mainframe.aspx) 웹 사이트입니다.

참고 항목:

- [메인프레임 항목에 대 한 백서](mainframe-white-papers.md)
- [메인프레임 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [문제 해결](/azure/virtual-machines/troubleshooting/)
- [Azure로 마이그레이션 메인프레임 익히기](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
