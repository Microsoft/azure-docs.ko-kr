---
title: 다수의 VM을 Azure로 마이그레이션 자동화 | Microsoft Docs
description: 스크립트를 사용하여 Azure Site Recovery를 통해 다수의 VM을 마이그레이션하는 방법을 설명합니다.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: f90140e9464ee72e9ceae8ca140bd060c51aade8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597105"
---
# <a name="scale-migration-of-vms-using-azure-site-recovery"></a>Azure Site Recovery를 사용하여 VM 마이그레이션의 크기 조정

이 문서는 스크립트를 사용하여 Azure Site Recovery를 통해 다수의 VM을 마이그레이션하는 프로세스를 파악하는 데 도움이 됩니다. 이러한 스크립트는 GitHub의 [Azure PowerShell 샘플](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) 리포지토리에서 다운로드할 수 있습니다. Managed disks로 Azure 지원과 마이그레이션 VMware, AWS, GCP Vm 및 물리적 서버를 마이그레이션하는 스크립트를 사용할 수 있습니다. VM을 물리적 서버로 마이그레이션하는 경우 이 스크립트를 사용하여 Hyper-V VM을 마이그레이션할 수도 있습니다. 스크립트는 [여기](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell)에 문서화된 Azure Site Recovery PowerShell을 활용합니다.

## <a name="current-limitations"></a>현재 제한 사항:
- 대상 VM의 기본 NIC에 대해서만 고정 IP 주소를 지정할 수 있습니다.
- 스크립트는 Azure 하이브리드 혜택 관련 입력을 허용하지 않으므로 포털에서 복제된 VM의 속성을 수동으로 업데이트해야 합니다.

## <a name="how-does-it-work"></a>어떻게 작동합니까?

### <a name="prerequisites"></a>필수 조건
시작하기 전에 다음 단계를 수행해야 합니다.
- Site Recovery 자격 증명 모음이 Azure 구독에 생성되었는지 확인
- 구성 서버 및 프로세스 서버가 원본 환경에 설치되었으며 자격 증명 모음이 환경을 검색할 수 있는지 확인
- 복제 정책이 생성되어 구성 서버와 연결되었는지 확인
- 구성 서버 (온-프레미스 Vm을 복제 하는 데 사용할)에 VM의 관리자 계정 추가 확인
- Azure의 대상 아티팩트 만들어졌는지 확인
    - 대상 리소스 그룹
    - 대상 저장소 계정 (및 해당 리소스 그룹)-프리미엄 managed disks로 마이그레이션하려는 경우 프리미엄 저장소 계정 만들기
    - 캐시 저장소 계정 (및 해당 리소스 그룹)-표준 저장소 계정 자격 증명 모음과 동일한 지역에서 만들기
    - 장애 조치(failover)를 위한 대상 가상 네트워크(및 해당 리소스 그룹)
    - 대상 서브넷
    - 테스트 장애 조치(failover)를 위한 대상 가상 네트워크(및 해당 리소스 그룹)
    - 가용성 집합(필요한 경우)
    - 대상 네트워크 보안 그룹 및 해당 리소스 그룹
- 대상 VM의 속성을 결정했는지 확인합니다.
    - 대상 VM 이름
    - Azure의 대상 VM 크기(Azure Migrate 평가를 사용하여 결정할 수 있음)
    - VM에 있는 기본 NIC의 개인 IP 주소
- GitHub의 [Azure PowerShell 샘플](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) 리포지토리에서 스크립트 다운로드

### <a name="csv-input-file"></a>CSV 입력 파일
모든 필수 완료 했으면 마이그레이션하려는 각 원본 컴퓨터에 대 한 데이터를 있는 CSV 파일을 만드는 해야 합니다. 입력 CSV에는 입력 정보가 포함된 헤더 줄과 마이그레이션해야 하는 각 머신에 대한 세부 정보가 포함된 행이 있어야 합니다. 모든 스크립트가 동일한 CSV 파일을 사용하도록 설계되었습니다. 참조용 샘플 CSV 템플릿이 스크립트 폴더에 제공됩니다.

### <a name="script-execution"></a>스크립트 실행
CSV가 준비되면 다음 단계를 실행하여 온-프레미스 VM을 마이그레이션할 수 있습니다.

**단계 #** | **스크립트 이름** | **설명**
--- | --- | ---
1 | asr_startmigration.ps1 | csv에 나열된 모든 VM에 대해 복제를 사용하도록 설정합니다. 이 스크립트는 각 VM에 대한 작업 정보가 포함된 CSV 출력을 만듭니다.
2 | asr_replicationstatus.ps1 | 복제 상태를 확인합니다. 이 스크립트는 각 VM의 상태가 포함된 csv를 만듭니다.
3 | asr_updateproperties.ps1 | VM이 복제/보호되고 나면, 이 스크립트를 사용하여 VM의 대상 속성(컴퓨팅 및 네트워크 속성)을 업데이트합니다.
4 | asr_propertiescheck.ps1 | 속성이 제대로 업데이트되었는지 확인합니다.
5 | asr_testmigration.ps1 |  csv에 나열된 VM의 테스트 장애 조치(failover)를 시작합니다. 이 스크립트는 각 VM에 대한 작업 정보가 포함된 CSV 출력을 만듭니다.
6 | asr_cleanuptestmigration.ps1 | 테스트 장애 조치(failover)된 VM의 유효성을 수동으로 검사한 후 이 스크립트를 사용하여 테스트 장애 조치(failover) VM을 정리할 수 있습니다.
7 | asr_migration.ps1 | csv에 나열된 VM에 대해 계획되지 않은 장애 조치(failover)를 수행합니다. 이 스크립트는 각 VM에 대한 작업 정보가 포함된 CSV 출력을 만듭니다. 스크립트 종료 되지 않는 Vm 온-프레미스 응용 프로그램 일관성을 위해 장애 조치를 트리거하기 전에 것 수동으로 종료 하는 Vm이 스크립트를 실행 하기 전에 합니다.
8 | asr_completemigration.ps1 | Vm에 대 한 커밋 작업을 수행 하 고 Azure Site Recovery 엔터티 삭제
9 | asr_postmigration.ps1 | 장애 조치(failover) 후 NIC에 네트워크 보안 그룹을 할당하려는 경우 이 스크립트를 사용하면 됩니다. 대상 VM의 NIC 중 하나에 NSG를 할당합니다.

## <a name="how-to-migrate-to-managed-disks"></a>Managed disks로 마이그레이션하려면 어떻게 하나요?
스크립트를 기본적으로 마이그레이션합니다 Vm에서 Azure managed disks로. 제공 된 대상 저장소 계정이 프리미엄 저장소 계정을 이면 마이그레이션 프리미엄 managed disks 사후 생성 됩니다. 캐시 저장소 계정이 표준 계정 수 수 있습니다. 대상 저장소 계정이 표준 저장소 계정을 경우 마이그레이션 후 표준 디스크에 생성 됩니다. 

## <a name="next-steps"></a>다음 단계

Azure Site Recovery를 사용하여 서버를 Azure로 마이그레이션하는 방법에 대한 [자세한 정보](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure)
