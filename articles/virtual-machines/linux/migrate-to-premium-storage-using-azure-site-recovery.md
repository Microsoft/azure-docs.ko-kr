---
title: Azure Site Recovery를 사용하여 Linux VM을 Azure Premium Storage로 마이그레이션 | Microsoft Docs
description: Site Recovery를 사용하여 Azure Premium Storage에 기존 가상 머신을 마이그레이션합니다. Premium Storage는 Azure Virtual Machines에서 실행되는 I/O 사용량이 많은 작업에 대해 대기 시간이 짧은 고성능 디스크 지원을 제공합니다.
services: virtual-machines-linux,storage
cloud: Azure
author: luywang
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: ffcc2f46a30569979879ff302cde1e3b146d3b50
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543686"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Azure Site Recovery를 사용하여 Premium Storage로 마이그레이션

[Azure 프리미엄 SSD](disks-types.md)는 I/O 사용량이 많은 워크로드를 실행하는 VM(가상 머신)에서 대기 시간이 짧은 고성능 디스크 지원을 제공합니다. 이 가이드에서는 사용자가 [Azure Site Recovery](../../site-recovery/site-recovery-overview.md)를 사용하여 표준 스토리지 계정의 VM 디스크를 Premium Storage 계정으로 마이그레이션할 수 있도록 합니다.

Site Recovery는 온-프레미스 물리적 서버와 VM을 클라우드(Azure) 또는 보조 데이터센터에 복제하는 것을 오케스트레이션하여 비즈니스 연속성 및 재해 복구에 대한 전략에 기여하는 Azure 서비스입니다. 기본 위치에서 중단이 발생하면 보조 위치로 장애 조치(failover)하여 애플리케이션과 워크로드를 가용 상태로 유지합니다. 기본 위치가 정상 작업 상태로 돌아오면 다시 기본 위치로 돌아갑니다. 

Site Recovery는 프로덕션 환경에 영향을 주지 않고 재해 복구 훈련을 지원하는 테스트 장애 조치(failover)를 제공합니다. 데이터 손상을 최소화하면서(복제 빈도에 따라) 예기치 않은 재해에 대해 장애 조치(failover)를 실행할 수 있습니다. Premium Storage로 마이그레이션하는 시나리오에서 [Site Recovery에서 장애 조치(Failover)](../../site-recovery/site-recovery-failover.md)를 사용하여 대상 디스크를 프리미엄 스토리지 계정으로 마이그레이션할 수 있습니다.

이 옵션은 최소한의 가동 중지 시간을 제공하기 때문에 Site Recovery를 사용하여 Premium Storage로 마이그레이션하는 것이 좋습니다. 또한 이 옵션은 디스크를 복사하고 새 VM을 만드는 수동 실행을 방지합니다. Site Recovery는 체계적으로 디스크를 복사하고 장애 조치(failover) 중 새 VM을 만듭니다. 

Site Recovery는 최소한의 가동 중지 시간 또는 가동 중지 시간 없이 다양한 유형의 장애 조치(failover)를 지원합니다. 가동 중지 시간을 계획하고 데이터 손실을 예상하려면 Site Recovery에서 [장애 조치(failover) 유형](../../site-recovery/site-recovery-failover.md)을 참조하세요. [장애 조치(failover) 후 Azure VM에 연결을 준비](../../site-recovery/vmware-walkthrough-overview.md)하는 경우 장애 조치(failover) 후 RDP를 사용하여 Azure VM에 연결할 수 있어야 합니다.

![재해 복구 다이어그램][1]

## <a name="azure-site-recovery-components"></a>Azure Site Recovery 구성 요소

이 마이그레이션 시나리오와 관련된 Site Recovery 구성 요소는 다음과 같습니다.

* **구성 서버**는 통신을 조정하고 데이터 복제 및 복구 프로세스를 관리하는 Azure VM입니다. 이 VM에서 단일 설치 파일을 실행하여 구성 서버 및 프로세스 서버라는 복제 게이트웨이로 추가 구성 요소를 설치합니다. [구성 서버 필수 조건](../../site-recovery/vmware-walkthrough-overview.md)을 읽어보세요. 구성 서버를 한 번만 구성하면 동일한 지역에 대한 모든 마이그레이션에 사용할 수 있습니다.

* **프로세스 서버**는 복제 게이트웨이로써 

  1. 원본 VM에서 복제 데이터를 수신합니다.
  2. 캐싱, 압축 및 암호화를 사용하여 데이터를 최적화합니다.
  3. 데이터를 저장소 계정에 전송합니다. 

  또한 원본 VM에 대한 모바일 서비스의 푸시 설치를 처리하며 원본 VM의 자동 복구를 수행합니다. 기본 프로세스 서버가 구성 서버에 설치됩니다. 추가 독립 실행형 프로세스 서버를 배포하여 배포를 확장할 수 있습니다. [프로세스 서버 배포에 대한 모범 사례](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) 및 [추가 프로세스 서버 배포](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers)를 읽어보세요. 프로세스 서버를 한 번만 구성하면 동일한 지역에 대한 모든 마이그레이션에 사용할 수 있습니다.

* **모바일 서비스**는 복제하려는 모든 표준 VM에 배포되는 구성 요소입니다. 표준 VM에 기록된 데이터를 캡처하고 프로세스 서버에 전달합니다. [복제된 컴퓨터 필수 조건](../../site-recovery/vmware-walkthrough-overview.md)을 읽어보세요.

이 그래픽은 이러한 구성 요소가 어떻게 상호 작용하는지 보여줍니다.

![Site Recovery 구성 요소의 상호 작용][15]

> [!NOTE]
> Site Recovery는 저장소 공간 디스크의 마이그레이션을 지원하지 않습니다.

다른 시나리오에 대한 추가 구성 요소는 [시나리오 아키텍처](../../site-recovery/vmware-walkthrough-overview.md)를 참조하세요.

## <a name="azure-essentials"></a>Azure Essentials

다음은 이 마이그레이션 시나리오에 대한 Azure 요구 사항입니다.

* Azure 구독.
* 복제된 데이터를 저장할 Azure Premium Storage 계정
* 장애 조치(failover) 시 만든 경우 VM에서 연결할 Azure 가상 네트워크 Azure 가상 네트워크는 Site Recovery가 실행되는 동일한 지역에 있어야 합니다.
* 복제 로그를 저장할 Azure 표준 스토리지 계정 마이그레이션되는 VM 디스크와 동일한 저장소 계정일 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* 이전 섹션의 관련 마이그레이션 시나리오 구성 요소 이해
* [Site Recovery에서 장애 조치(Failover)](../../site-recovery/site-recovery-failover.md)에 대해 학습하여 작동 중단 시간 계획

## <a name="setup-and-migration-steps"></a>설정 및 마이그레이션 단계

Site Recovery를 사용하여 지역 간 또는 동일한 지역 내에서 Azure IaaS VM을 마이그레이션할 수 있습니다. 다음 지침은 [VMware VM 또는 물리적 서버를 Azure로 복제](../../site-recovery/vmware-walkthrough-overview.md) 문서에서 이 마이그레이션 시나리오에 맞게 만들어졌습니다. 이 문서의 지침 외에도 자세한 단계에 대한 링크를 따라가세요.

### <a name="step-1-create-a-recovery-services-vault"></a>1단계: Recovery Services 자격 증명 모음 만들기

1. [Azure Portal](https://portal.azure.com)을 엽니다.
2. **리소스 만들기** > **관리** > **Backup** 및 **Site Recovery(OMS)** 를 선택합니다. 또는 **찾아보기** > **Recovery Services 자격 증명 모음** > **추가**를 선택하면 됩니다. 
3. VM이 복제될 지역을 지정합니다. 동일한 지역에 마이그레이션하려는 경우 원본 VM 및 원본 저장소 계정이 있는 영역을 선택합니다. 

### <a name="step-2-choose-your-protection-goals"></a>2단계: 보호 목표 선택 

1. 구성 서버를 설치하려는 VM에서 [Azure Portal](https://portal.azure.com)을 엽니다.
2. **Recovery Services 자격 증명 모음** > **설정** > **Site Recovery** > **1단계: 인프라 준비** > **보호 목표**를 클릭합니다.

   ![보호 목표 창으로 이동][2]

3. **보호 목표**의 첫 번째 드롭다운 목록에서 **Azure로**를 선택합니다. 두 번째 드롭다운 목록에서 **가상화되지 않음/기타**를 선택한 다음 **확인**을 선택합니다.

   ![채워진 상자가 있는 보호 목표 창][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>3단계: 원본 환경(구성 서버) 설정

1. **인프라 준비** > **원본 준비** > **서버 추가** 창으로 이동하여 **Azure Site Recovery 통합 설치** 및 자격 증명 모음 등록 키를 다운로드합니다. 
 
   통합 설치 프로그램을 실행하려면 자격 증명 모음 등록 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

   ![서버 추가 창으로 이동][4]

2. **서버 추가** 창에 구성 서버를 추가합니다.

   ![구성 서버를 선택한 서버 창 추가][5]

3. 구성 서버로 사용 중인 VM에서 통합 설치 프로그램을 실행하여 구성 서버 및 프로세스 서버를 설치합니다. [스크린샷을 따라](../../site-recovery/vmware-walkthrough-overview.md) 설치를 완료할 수 있습니다. 이 마이그레이션 시나리오에 지정된 단계에 다음 스크린샷을 참조할 수 있습니다.

   1. **시작하기 전에**에서 **구성 서버 및 프로세스 서버 설치**를 선택합니다.

      ![시작하기 전에 페이지][6]

   2. **등록**에서 자격 증명 모음에서 다운로드한 등록 키를 찾아 선택합니다.

      ![등록 페이지][7]

   3. **환경 세부 정보**에서 VMware VM을 복제 여부를 선택합니다. 이 마이그레이션 시나리오의 경우 **아니오**를 선택합니다.

      ![환경 세부 정보 페이지][8]

4. 설치가 완료되면 **Microsoft Azure Site Recovery 구성 서버** 창에서 다음을 수행합니다.
 
   1. **계정 관리** 탭을 사용하여 Site Recovery에서 자동 검색에 사용할 수 있는 계정을 만듭니다. (물리적 컴퓨터 보호에 대한 시나리오에서 계정 설정은 관련이 없지만 다음 단계 중 하나를 활성화하려면 하나 이상의 계정이 필요합니다. 이 경우 계정 및 암호의 이름을 지정할 수 있습니다.) 
   2. **자격 증명 모음 등록** 탭을 사용하여 자격 증명 모음 자격 증명 파일을 업로드합니다.

      ![자격 증명 모음 등록 탭][9]

### <a name="step-4-set-up-the-target-environment"></a>4단계: 대상 환경 설정

**인프라 준비** > **대상**을 선택하고 장애 조치(failover) 후 VM에 사용하려는 배포 모델을 지정합니다. 시나리오에 따라 **클래식** 또는 **리소스 관리자**를 선택할 수 있습니다.

![대상 창][10]

Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다. 

> [!NOTE]
> 복제된 데이터에 Premium Storage 계정을 사용하는 경우 복제 로그를 저장할 추가 표준 스토리지 계정을 설정해야 합니다.

### <a name="step-5-set-up-replication-settings"></a>5단계: 복제 설정 지정

구성 서버가 사용자가 만든 복제 정책에 성공적으로 연결되었는지 확인하려면 [복제 설정 지정](../../site-recovery/vmware-walkthrough-overview.md)을 따릅니다.

### <a name="step-6-plan-capacity"></a>6단계: 용량 계획

1. [Capacity Planner](../../site-recovery/site-recovery-capacity-planner.md)를 사용하여 복제 요구 사항에 맞도록 네트워크 대역폭, 저장소 및 기타 요구 사항을 정확하게 예측합니다. 
2. 작업을 마쳤으면 **용량 계획을 완료하셨나요?** 에서 **예, 그렇습니다**를 선택합니다.

   ![용량 계획을 완료했는지 확인하는 상자][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>7단계: 모바일 서비스 설치 및 복제를 사용하도록 설정

1. 원본 VM에 [푸시 설치](../../site-recovery/vmware-walkthrough-overview.md)하거나 또는 원본 VM에 [모바일 서비스를 수동으로 설치](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md)하도록 선택할 수 있습니다. 제공된 링크에서 강제 설치의 요구 사항 및 수동 설치 관리자의 경로를 확인할 수 있습니다. 수동 설치를 수행하는 경우 구성 서버를 찾기 위해 내부 IP 주소를 사용해야 할 수 있습니다.

   ![구성 서버 세부 정보 페이지][12]

   실패한 VM에는 주 VM의 임시 디스크 및 복구 영역에서 VM을 프로비전하는 동안 만들어진 두 개의 임시 디스크가 있습니다. 복제하기 전에 임시 디스크를 제외하려면 복제를 활성화하기 전에 모바일 서비스를 설치합니다. 임시 디스크를 제외하는 방법에 대한 자세한 내용은 [복제에서 디스크 제외](../../site-recovery/vmware-walkthrough-overview.md)를 참조하세요.

2. 다음과 같이 복제를 사용합니다.
   1. **애플리케이션 복제** > **원본**을 선택합니다. 처음으로 복제를 활성화한 후 자격 증명 모음에서 **+복제**를 선택하여 추가 컴퓨터에 대해 복제를 활성화합니다.
   2. 1단계에서 프로세스 서버로 **원본**을 설정합니다.
   3. 2단계에서 장애 조치(failover) 후 배포 모델, 마이그레이션하는 Premium Storage 계정, 로그를 저장하는 표준 스토리지 계정 및 실패하는 가상 네트워크를 지정합니다.
   4. 3단계에서 IP 주소별로 보호되는 VM을 추가합니다. (해당 VM을 찾으려면 내부 IP 주소가 필요할 수 있습니다.)
   5. 4단계에서 프로세스 서버에서 이전에 설정한 계정을 선택하여 속성을 구성합니다.
   6. 5단계에서는 “5단계: 복제 설정 지정”에서 이전에 만든 복제 정책을 선택합니다.
   7. **확인**을 선택합니다.

   > [!NOTE]
   > Azure VM 할당이 취소되었다가 다시 시작되는 경우 동일한 IP 주소를 받는다는 보장이 없습니다. 구성 서버/프로세스 서버의 IP 주소 또는 보호되는 Azure VM이 변경되는 경우 이 시나리오에서 복제가 제대로 작동하지 않을 수도 있습니다.

   ![선택한 원본과 복제 창 사용][13]

Azure Storage 환경을 디자인할 때 가용성 집합의 각 VM에 대해 별도의 스토리지 계정을 사용하는 것이 좋습니다. [각 가용성 집합에 여러 저장소 계정 사용](../linux/manage-availability.md)을 위해서는 저장소 계층의 모범 사례를 따는 것이 좋습니다. 여러 저장소 계정에 VM 디스크를 배포하면 저장소의 가용성이 향상되고 Azure 저장소 인프라 전반에 걸쳐 I/O가 배포됩니다.

VM이 모든 VM의 디스크를 하나의 저장소 계정으로 복제하는 대신 가용성 집합에 있는 경우 여러 VM을 여러 번 마이그레이션하는 것이 좋습니다. 그러면 동일한 가용성 집합에 있는 VM이 단일 저장소 계정을 공유하지 않습니다. **복제 사용** 창을 사용하여 한 번에 하나씩 각 VM에 대해 대상 저장소 계정을 설정합니다.
 
필요에 따라 장애 조치(failover) 후 배포 모델을 선택할 수 있습니다. 장애 조치(failover) 후 배포 모델로 Azure Resource Manager를 선택하면 VM(Resource Manager)을 VM(Resource Manager)에 장애 조치하거나 VM(클래식)을 VM(Resource Manager)에 장애 조치할 수 있습니다.

### <a name="step-8-run-a-test-failover"></a>8단계: 테스트 장애 조치(failover) 실행

복제가 완료되었는지 여부를 확인하려면 해당 Site Recovery 인스턴스를 선택한 다음 **설정** > **복제된 항목**을 선택합니다. 복제 프로세스의 상태 및 백분율이 표시됩니다. 

초기 복제가 완료된 후 테스트 장애 조치(Failover)를 실행하여 복제 전략의 유효성을 검사합니다. 테스트 장애 조치(failover)의 자세한 단계는 [Site Recovery에서 테스트 장애 조치(failover) 실행](../../site-recovery/vmware-walkthrough-overview.md)을 참조하세요. 

> [!NOTE]
> 장애 조치(failover)를 실행하기 전에 VM 및 복제 전략이 요구 사항을 충족해야 합니다. 테스트 장애 조치(failover)를 실행하는 방법에 대한 자세한 내용은 [Site Recovery에서 Azure에 테스트 장애 조치(failover)](../../site-recovery/site-recovery-test-failover-to-azure.md)를 참조하세요.

**설정** > **작업** > *YOUR_FAILOVER_PLAN_NAME*에서 테스트 장애 조치(failover)의 상태를 볼 수 있습니다. 창에서는 단계의 분석 및 성공/실패 결과가 표시됩니다. 어느 단계에서 테스트 장애 조치(failover)가 실패하는 경우 단계를 선택하여 오류 메시지를 확인합니다. 

### <a name="step-9-run-a-failover"></a>9단계: 장애 조치(Failover) 실행

테스트 장애 조치(failover)가 완료되면 장애 조치(failover)를 실행하여 Premium Storage로 디스크를 마이그레이션하고 VM 인스턴스를 복제합니다. [장애 조치(Failover) 실행](../../site-recovery/site-recovery-failover.md#run-a-failover)에 나와 있는 자세한 단계를 따르세요. 

**VM 종료 및 최신 데이터 동기화**를 선택해야 합니다. 이 옵션은 Site Recovery가 보호된 VM을 종료하고 데이터를 동기화하여 최신 버전의 데이터가 장애 조치되도록 지정합니다. 이 옵션을 선택하지 않거나 시도가 성공하지 못하면 장애 조치(failover)가 VM에 대해 사용 가능한 최신 복구 지점에서 시작됩니다. 

Site Recovery는 Premium Storage 사용 가능 VM에 형식이 동일하거나 유사한 VM 인스턴스를 만듭니다. [Windows Virtual Machines 가격](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 또는 [Linux Virtual Machines 가격](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)으로 이동하여 다양한 VM 인스턴스의 성능 및 가격을 확인할 수 있습니다.

## <a name="post-migration-steps"></a>마이그레이션 후 단계

1. **해당하는 경우 복제된 VM을 가용성 집합에 구성합니다**. Site Recovery는 가용성 집합과 함께 VM 마이그레이션을 지원하지 않습니다. 복제된 VM의 배포에 따라 다음 중 하나를 수행합니다.
   * 클래식 배포 모델을 통해 만든 VM: Azure Portal의 가용성 집합에 VM을 추가합니다. 자세한 단계는 [기존 가상 머신을 가용성 집합에 추가](../linux/classic/configure-availability-classic.md)로 이동하세요.
   * Resource Manager 배포 모델을 통해 만든 VM: VM 구성을 저장하고 VM을 삭제한 후에 가용성 집합에 다시 만듭니다. 이렇게 하려면 [Azure Resource Manager VM 가용성 집합 설정](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)에서 스크립트를 사용합니다. 이 스크립트를 실행하기 전에 제한 사항을 확인하고 작동 중단 시간을 계획합니다.

2. **이전 VM 및 디스크를 삭제합니다**. 프리미엄 디스크가 원본 디스크와 일치하고 새 VM이 원본 VM과 동일한 기능을 수행해야 합니다. VM을 삭제하고 Azure Portal의 원본 저장소 계정에서 디스크를 삭제합니다. VM을 삭제했는데도 디스크가 삭제되지 않는 문제가 발생하는 경우 [저장소 리소스 삭제 오류 해결](storage-resource-deletion-errors.md)을 참조하세요.

3. **Azure Site Recovery 인프라를 정리합니다**. Site Recovery가 더 이상 필요하지 않은 경우 인프라를 정리할 수 있습니다. 복제된 항목, 구성 서버 및 복구 정책을 삭제한 후 Azure Site Recovery 자격 증명 모음을 삭제합니다.

## <a name="troubleshooting"></a>문제 해결

* [가상 머신 및 물리적 서버를 위한 보호 모니터링 및 문제 해결](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Microsoft Azure Site Recovery 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>다음 단계

가상 머신 마이그레이션에 대한 특정 시나리오에 대한 다음 리소스를 참조하세요.

* [Storage 계정 간에 Azure Virtual Machines 마이그레이션](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Linux 가상 하드 디스크 업로드](upload-vhd.md)
* [Amazon AWS에서 Microsoft Azure로 Virtual Machines 마이그레이션](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Azure Storage 및 Azure Virtual Machines에 대한 자세한 내용을 보려면 다음 리소스도 확인하세요.

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [IaaS VM의 디스크 유형 선택](disks-types.md)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png
