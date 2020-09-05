---
title: 클래식에서 Azure Resource Manager 마이그레이션 기술 심층 살펴보기
description: 클래식 배포 모델에서 Azure Resource Manager에 대 한 리소스의 플랫폼 지원 마이그레이션에 대 한 기술 심층 소개.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: da75e1d6208db5adf5f0f63d2a5525fc651513b0
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855912"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보

> [!IMPORTANT]
> 현재 IaaS Vm의 90%가 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)를 사용 하 고 있습니다. 2020 년 2 월 28 일부 터 클래식 Vm은 더 이상 사용 되지 않으며 2023 년 3 월 1 일에 완전히 사용 중지 됩니다. 이 사용 중단 및 [영향](./classic-vm-deprecation.md#how-does-this-affect-me)에 대 한 [자세한 내용을 알아보세요]( https://aka.ms/classicvmretirement) .

Azure 클래식 배포 모델에서 Azure Resource Manager 배포 모델로 마이그레이션하는 방법을 자세하게 살펴보겠습니다. Azure 플랫폼에서 두 가지 배포 모델 간에 리소스를 마이그레이션하는 방법을 더욱 잘 이해할 수 있도록 리소스 및 기능 수준에서 리소스를 검토합니다. 자세한 내용은 서비스 공지 문서를 참조 하세요.

* Linux: [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](./linux/migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Windows:  [플랫폼에서 지원 되는 IaaS 리소스를 클래식에서 Azure Resource Manager로 마이그레이션할 수](./windows/migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)있습니다.

## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>IaaS 리소스를 클래식 배포 모델에서 Azure 리소스 관리자로 마이그레이션
먼저 서비스(IaaS) 리소스로서 인프라의 데이터 평면과 관리 평면 간의 차이점을 이해해야 합니다.

* *관리/제어 평면*은 리소스를 수정하기 위해 관리/제어 평면 또는 API로 들어오는 호출을 설명합니다. 예를 들어 VM을 만들고, VM을 다시 시작하고, 새 서브넷을 사용하여 가상 네트워크를 업데이트하는 등의 작업은 실행 중인 리소스를 관리합니다. VM을 연결하는 데 직접적으로 영향을 주지 않습니다.
* *데이터 평면*(애플리케이션)은 애플리케이션 자체의 런타임을 설명하고, Azure API를 통과하지 않는 인스턴스와의 상호 작용을 포함합니다. 예를 들어 웹 사이트에 액세스하거나 실행 중인 SQL Server 인스턴스 또는 MongoDB 서버에서 데이터를 가져오는 것은 데이터 평면 또는 애플리케이션 상호 작용입니다. 다른 예로 스토리지 계정에서 Blob을 복사하고 가상 머신에 RDP(원격 데스크톱 프로토콜) 또는 SSH(Secure Shell)를 사용하기 위해 공용 IP 주소에 액세스하는 경우가 있습니다. 이러한 작업은 컴퓨팅, 네트워킹, 스토리지에서 애플리케이션을 계속 실행하는 상태에서 수행합니다.

데이터 평면은 클래식 배포 모델 및 리소스 관리자 스택 간에 동일합니다. 차이점은 마이그레이션 프로세스 중에 Microsoft에서 클래식 배포 모델의 리소스 표현을 리소스 관리자 스택의 표현으로 변환한다는 것입니다. 결과적으로 새 도구, API 및 SDK를 사용하여 리소스 관리자 스택에서 리소스를 관리해야 합니다.

![관리/제어 평면과 데이터 평면 간의 차이를 보여 주는 스크린샷](media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> 일부 마이그레이션 시나리오에서 Azure 플랫폼은 가상 머신을 중지하고, 할당 취소하고, 다시 시작합니다. 이렇게 하면 간단한 데이터 평면 가동 중지 시간이 발생합니다.
>

## <a name="the-migration-experience"></a>마이그레이션 환경
마이그레이션 시작 이전의 고려 사항

* 마이그레이션하려는 리소스가 지원되지 않는 기능이나 구성을 사용하지 않도록 확인합니다. 일반적으로 플랫폼이 이러한 문제를 감지하고 오류를 생성합니다.
* 가상 네트워크에 없는 VM이 있는 경우 해당 VM은 준비 작업의 일부로 중지 및 할당 취소됩니다. 공용 IP 주소를 손실하지 않으려면 준비 작업을 트리거하기 전에 IP 주소 예약을 고려합니다. VM이 가상 네트워크에 있는 경우에는 중지 및 할당 취소되지 않습니다.
* 업무 외 시간 동안 마이그레이션을 계획하여 마이그레이션 중 발생할 수 있는 예기치 않은 오류를 해결할 수 있도록 계획하십시오.
* 준비 단계가 완료된 후 쉽게 검증할 수 있도록 PowerShell, CLI(명령줄 인터페이스) 명령 또는 REST API를 사용하여 VM의 현재 구성을 다운로드합니다.
* 마이그레이션을 시작하기 전에 자동화/운영 스크립트를 업데이트하여 리소스 관리자 배포 모델을 처리합니다. 경우에 따라 리소스가 준비됨 상태일 때 GET 작업을 실행할 수도 있습니다.
* 클래식 배포 모델의 IaaS 리소스에 구성된 RBAC(역할 기반 Access Control) 정책을 평가하고, 마이그레이션이 완료된 후에 계획합니다.

마이그레이션 워크플로는 다음과 같습니다.

![마이그레이션 워크플로를 보여 주는 스크린샷](windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> 다음 섹션에서 설명하는 작업은 모두 멱등원(idempotent) 작업입니다. 지원되지 않는 기능 또는 구성 오류 이외의 문제가 있는 경우 준비, 중단 또는 커밋 작업을 다시 시도합니다. Azure에서 작업을 다시 시도해 보세요.
>
>

### <a name="validate"></a>유효성 검사
유효성 검사 작업은 마이그레이션 프로세스의 첫 번째 단계입니다. 이 단계의 목표는 클래식 배포 모델에서 마이그레이션하려는 리소스의 상태를 분석하는 것입니다. 즉 리소스에서 마이그레이션할 수 있는지(성공 또는 실패)를 평가하는 작업입니다.

마이그레이션을 유효성 검사하려는 가상 네트워크 또는 클라우드 서비스(가상 네트워크가 아닌 경우)를 선택합니다. 리소스가 마이그레이션할 수 없는 경우 Azure에서 이유를 나열합니다.

#### <a name="checks-not-done-in-the-validate-operation"></a>유효성 검사 작업에서 수행되지 않는 확인

유효성 검사 작업에서는 클래식 배포 모델에 있는 리소스의 상태만 분석합니다. 클래식 배포 모델에서 다양한 구성으로 인해 발생하는 모든 오류 및 지원되지 않는 시나리오에 대해 검사할 수 있습니다. Azure 리소스 관리자 스택에서 마이그레이션하는 동안 리소스에 적용할 수 있는 모든 문제를 확인하는 것은 불가능합니다. 이러한 문제는 마이그레이션의 다음 단계(준비 작업)에서 리소스를 변환하는 경우에만 확인됩니다. 다음 표에는 유효성 검사 작업에서 확인되지 않는 문제가 모두 나열되어 있습니다.


|유효성 검사 작업에 없는 네트워킹 확인|
|-|
|ER 및 VPN 게이트웨이가 모두 있는 가상 네트워크|
|연결이 끊겨 있는 가상 네트워크 게이트웨이 연결|
|Azure Resource Manager 스택으로 미리 마이그레이션되는 모든 ER 회로|
|네트워킹 리소스에 대한 Azure Resource Manager 할당량 확인 - 예: 고정 공용 IP, 동적 공용 IP, 부하 분산 장치, 네트워크 보안 그룹, 경로 테이블 및 네트워크 인터페이스 |
| 배포 및 가상 네트워크에서 유효한 모든 부하 분산 장치 규칙 |
| 동일한 가상 네트워크에서 중지-할당 취소된 VM 간에 충돌하는 프라이빗 IP. |

### <a name="prepare"></a>준비
준비 작업은 마이그레이션 프로세스의 두 번째 단계입니다. 이 단계의 목표는 클래식 배포 모델에서 리소스 관리자 리소스로의 IaaS 리소스 전환을 시뮬레이션하는 것입니다. 또한 준비 작업에서는 이 시뮬레이션을 시각화할 수 있도록 나란히 표시합니다.

> [!NOTE] 
> 클래식 배포 모델의 리소스는 이 단계에서 수정되지 않습니다. 이 단계는 마이그레이션하려는 경우에 실행하는 안전한 단계입니다. 

마이그레이션을 준비하려는 가상 네트워크 또는 클라우드 서비스(가상 네트워크가 아닌 경우)를 선택합니다.

* 리소스에서 마이그레이션할 수 없는 경우 Azure에서 마이그레이션 프로세스를 중지하고 준비 작업이 실패한 이유를 나열합니다.
* 리소스에서 마이그레이션할 수 있는 경우 Azure에서 마이그레이션 중인 리소스에 대한 관리 평면 작업을 잠급니다. 예: 마이그레이션 중인 VM에 데이터 디스크를 추가할 수 없습니다.

그런 다음 Azure에서 리소스를 마이그레션하기 위해 클래식 배포 모델에서 리소스 관리자로 메타데이터를 마이그레이션하기 시작합니다.

준비 작업이 완료되면 클래식 배포 모델과 리소스 관리자 모두에서 리소스를 시각화하는 옵션이 제공됩니다. 클래식 배포 모델의 모든 클라우드 서비스에 대해 Azure Platform에서 `cloud-service-name>-Migrated`패턴의 리소스 그룹 이름을 만듭니다.

> [!NOTE]
> 마이그레이션된 리소스에 대해 만든 리소스 그룹의 이름 (즉 "-Migrated")은 선택할 수 없습니다. 그러나 마이그레이션이 완료되면 Azure 리소스 관리자의 이동 기능을 사용하여 원하는 리소스 그룹으로 리소스를 이동할 수 있습니다. 자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요.

다음 두 스크린샷에서는 성공적인 준비 작업 후의 결과를 보여 줍니다. 첫 번째는 원본 클라우드 서비스가 포함된 리소스 그룹을 보여 주며, 두 번째는 동일한 Azure Resource Manager 리소스가 포함된 새로운 "-Migrated" 리소스 그룹을 보여 줍니다.

![원본 클라우드 서비스를 보여 주는 스크린샷](windows/media/migration-classic-resource-manager/portal-classic.png)

![준비 작업에서 Azure Resource Manager 리소스를 보여 주는 스크린샷](windows/media/migration-classic-resource-manager/portal-arm.png)

준비 단계가 완료된 후 화면 뒤에 숨어 있는 리소스의 모양은 다음과 같습니다. 데이터 평면의 리소스는 동일합니다. 관리 평면(클래식 배포 모델) 및 제어 평면(리소스 관리자)에서 모두 표시됩니다.

![준비 단계 다이어그램](windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> 클래식 배포 모델의 가상 네트워크에 없는 VM은 이 마이그레이션 단계에서 중지 또는 할당 취소됩니다.
>

### <a name="check-manual-or-scripted"></a>검사(수동 또는 스크립트)
이 검사 단계에는 이전에 다운로드한 구성을 사용하여 마이그레이션이 정상적으로 진행되는지 확인하는 옵션이 있습니다. 또는 포털에 로그인하고 속성과 리소스에 대해 주요 점검을 수행하여 메타데이터 마이그레이션이 정상적으로 진행되는지 확인할 수 있습니다.

가상 네트워크를 마이그레이션하는 경우 가상 머신의 구성이 대부분 다시 시작됩니다. 이러한 VM의 애플리케이션에 대해 애플리케이션이 아직 실행 중인지 확인할 수 있습니다.

모니터링 및 작업 스크립트를 테스트하여 VM이 예상대로 작동하며 업데이트된 스크립트가 올바르게 작동하는지 확인할 수 있습니다. 리소스가 준비됨 상태인 경우에는 GET 작업만 지원됩니다.

마이그레이션을 커밋해야 하는 시간에 대해 설정된 시간은 없습니다. 이 상태를 원하는 시간 동안 유지할 수 있습니다. 하지만 중단 또는 커밋할 때까지 이러한 리소스에 대한 관리 평면이 잠깁니다.

문제가 발생할 경우 언제든지 마이그레이션을 중단하고 클래식 배포 모델로 돌아갈 수 있습니다. 다시 돌아가면 Azure에서 리소스에 대한 관리 평면 작업을 열어 클래식 배포 모델에서 해당 VM에 대한 정상 작업을 재개할 수 있습니다.

### <a name="abort"></a>중단
변경 내용을 클래식 배포 모델로 되돌리고 마이그레이션을 중지하려는 경우의 옵션 단계입니다. 이 작업에서는 준비 단계에서 해당 리소스에 대해 만든 리소스 관리자 메타데이터를 삭제합니다. 

![중단 단계 다이어그램](windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> 커밋 작업을 트리거한 후에는 이 작업을 수행할 수 없습니다.     
>

### <a name="commit"></a>Commit
유효성 검사를 마친 후 마이그레이션을 커밋할 수 있습니다. 리소스는 더 이상 클래식 배포 모델에서 표시되지 않으며 리소스 관리자 배포 모델에서만 사용할 수 있습니다. 새 포털에서는 마이그레이션된 리소스만 관리할 수 있습니다.

> [!NOTE]
> 이 작업은 멱등원 작업입니다. 실패하면 작업을 다시 시도합니다. 계속 실패하는 경우 지원 티켓을 만들거나 [Microsoft Q&A](https://docs.microsoft.com/answers/index.html)에서 포럼을 만듭니다.
>
>

![커밋 단계 다이어그램](windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>마이그레이션 순서도

다음은 마이그레이션을 진행하는 방법을 보여 주는 순서도입니다.

![Screenshot that shows the migration steps](windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>클래식 배포 모델을 리소스 관리자 리소스로 변환
다음 표에서 클래식 배포 모델 및 리소스 관리자에서 리소스를 표현하는 방식을 확인할 수 있습니다. 다른 기능 및 리소스는 현재 지원되지 않습니다.

| 클래식 표현 | Resource Manager 표현 | 메모 |
| --- | --- | --- |
| 클라우드 서비스 이름 |DNS 이름 |마이그레이션하는 동안 명명 패턴 `<cloudservicename>-migrated`를 사용하여 모든 클라우드 서비스에 대한 새 리소스 그룹을 만듭니다. 이 리소스 그룹에는 모든 리소스가 포함됩니다. 클라우드 서비스 이름은 공용 IP 주소와 연결된 DNS 이름이 됩니다. |
| 가상 머신 |가상 머신 |VM 관련 속성은 변경되지 않고 마이그레이션됩니다. 컴퓨터 이름과 같은 특정 osProfile 정보는 클래식 배포 모델에 저장되지 않으며, 마이그레이션 후에도 비어 있습니다. |
| VM에 연결된 디스크 리소스 |VM에 연결된 암시적 디스크 |Resource Manager 배포 모델에서는 디스크가 최상위 리소스로 모델링되지 않습니다. VM에서 암시적 디스크로 마이그레이션됩니다. 현재 VM에 연결되어 있는 디스크만 지원됩니다. 리소스 관리자 VM은 이제 클래식 배포 모델에서 스토리지 계정을 사용할 수 있으므로 디스크를 업데이트하지 않고도 쉽게 마이그레이션할 수 있습니다. |
| VM 확장 |VM 확장 |클래식 배포 모델에서는 XML 확장을 제외한 모든 리소스 확장이 마이그레이션됩니다. |
| 가상 컴퓨터 인증서 |Azure Key Vault의 인증서 |클라우드 서비스에 서비스 인증서가 있는 경우 마이그레이션에서는 클라우드 서비스당 새 Azure 키 자격 증명 모음을 만들고 인증서를 이 키 자격 증명 모음으로 이동합니다. VM이 업데이트되면서 주요 자격 증명 모음의 인증서를 참조합니다. <br><br> 키 자격 증명 모음은 삭제하지 마세요. 이로 인해 VM이 실패한 상태가 될 수 있습니다. |
| WinRM 구성 |osProfile 하의 WinRM 구성 |Windows 원격 관리 구성은 마이그레이션의 일부로 변경되지 않은 상태로 이동됩니다. |
| 가용성 집합 속성 |가용성 집합 리소스 | 가용성 집합 사양은 클래식 배포 모델의 VM에 대한 속성입니다. 마이그레이션에서는 가용성 집합이 최상위 리소스가 됩니다. 클라우드 서비스당 둘 이상의 가용성 집합 또는 클라우드 서비스의 가용성 집합에 없는 하나 이상의 가용성 집합 및 VM 구성은 지원되지 않습니다. |
| VM의 네트워크 구성 |기본 네트워크 인터페이스 |VM의 네트워크 구성은 마이그레이션 후 기본 네트워크 인터페이스 리소스로 표현됩니다. 가상 네트워크에 없는 VM의 경우 마이그레이션 중 내부 IP 주소가 변경됩니다. |
| VM의 여러 네트워크 인터페이스 |네트워크 인터페이스 |VM에 여러 네트워크 인터페이스가 연결된 경우 각 네트워크 인터페이스는 모든 속성과 함께 마이그레이션의 일부로 최상위 리소스가 됩니다. |
| 부하 분산된 엔드포인트 집합 |부하 분산 장치 |클래식 배포 모델에서 플랫폼은 모든 클라우드 서비스에 대한 암시적 부하 분산 장치를 할당받습니다. 마이그레이션 중에는 새로운 부하 분산 장치 리소스를 만들며 부하 분산 엔드포인트 집합이 부하 분산 장치 규칙이 됩니다. |
| 인바운드 NAT 규칙 |인바운드 NAT 규칙 |VM에 정의된 입력 엔드포인트는 마이그레이션 중에 부하 분산 장치의 인바운드 NAT(Network Address Translation) 규칙으로 변환됩니다. |
| VIP 주소 |DNS 이름이 포함된 공용 IP 주소 |가상 IP 주소는 공용 IP 주소가 되며, 부하 분산 장치와 연결됩니다. 할당된 입력 엔드포인트가 있는 경우 가상 IP만 마이그레이션할 수 있습니다. |
| 가상 네트워크 |가상 네트워크 |가상 네트워크는 모든 속성과 함께 Resource Manager 배포 모델로 마이그레이션됩니다. `-migrated`이름을 사용하여 새 리소스 그룹이 생성됩니다. |
| 예약된 IP |정적 할당 방법의 공용 IP 주소 |부하 분산 장치와 연결되어 있고 예약된 IP는 클라우드 서비스 또는 가상 머신의 마이그레이션과 함께 마이그레이션됩니다. [Move-AzureReservedIP](/powershell/module/servicemanagement/azure.service/move-azurereservedip?view=azuresmps-4.0.0)를 사용하여 연결되지 않은 예약 IP를 마이그레이션할 수 있습니다.  |
| VM당 공용 IP 주소 |동적 할당 방법의 공용 IP 주소 |VM에 연결된 공용 IP 주소는 할당 방법이 정적으로 설정된 공용 IP 주소 리소스로 변환됩니다. |
| NSG |NSG |서브넷과 연결된 네트워크 보안 그룹은 마이그레이션 중 Resource Manager 배포 모델로 복제됩니다. 마이그레이션 중 클래식 배포 모델의 NSG는 제거되지 않습니다. 하지만 마이그레이션이 진행 중인 동안에는 NSG의 관리 평면 작업이 차단됩니다. [Move-AzureNetworkSecurityGroup](/powershell/module/servicemanagement/azure.service/move-azurenetworksecuritygroup?view=azuresmps-4.0.0)을 사용하여 연결되지 않은 NSG를 마이그레이션할 수 있습니다.|
| DNS 서버 |DNS 서버 |가상 네트워크 또는 VM과 연결된 DNS 서버는 해당 리소스 마이그레이션 중 모든 속성과 함께 마이그레이션됩니다. |
| UDR |UDR |서브넷과 연결된 사용자 정의 경로는 마이그레이션 중 Resource Manager 배포 모델로 복제됩니다. 마이그레이션 중 클래식 배포 모델의 UDR는 제거되지 않습니다. 마이그레이션이 진행 중인 동안에는 UDR의 관리 평면 작업이 차단됩니다. [Move-AzureRouteTable](/powershell/module/servicemanagement/azure.service/Move-AzureRouteTable?view=azuresmps-4.0.0)을 사용하여 연결되지 않은 UDR을 마이그레이션할 수 있습니다. |
| VM 네트워크 구성의 IP 전달 속성 |NIC의 IP 전달 속성 |마이그레이션 중 VM의 IP 전달 속성이 네트워크 인터페이스의 속성으로 변환됩니다. |
| 여러 IP가 포함된 부하 분산 장치 |여러 공용 IP 리소스가 포함된 부하 분산 장치 |부하 분산 장치와 연결된 모든 공용 IP는 공용 IP 리소스로 변환되며, 마이그레이션 후에 부하 분산 장치와 연결됩니다. |
| VM의 내부 DNS 이름 |NIC의 내부 DNS 이름 |마이그레이션하는 동안 VM의 내부 DNS 접미사는 NIC의 "InternalDomainNameSuffix"라는 읽기 전용 속성으로 마이그레이션됩니다. 마이그레이션 후에 접미사는 변경되지 않고 그대로 유지되며 VM 확인은 이전처럼 계속 작동해야 합니다. |
| 가상 네트워크 게이트웨이 |가상 네트워크 게이트웨이 |가상 네트워크 게이트웨이 속성은 변경되지 않은 채 그대로 마이그레이션됩니다. 게이트웨이와 연결된 VIP는 변경하지 않습니다. |
| 로컬 네트워크 사이트 |로컬 네트워크 게이트웨이 |로컬 네트워크 사이트 속성은 로컬 네트워크 게이트웨이라는 새 리소스로 변경되지 않은 채 그대로 마이그레이션됩니다. 이는 온-프레미스 주소 접두사 및 원격 게이트웨이 IP를 나타냅니다. |
| 연결 참조 |연결 |네트워크 구성에서 게이트웨이와 로컬 네트워크 사이트 간의 연결 참조는 Connection이라는 새 리소스로 표시됩니다. 네트워크 구성 파일의 모든 연결 참조 속성은 변경되지 않은 채 그대로 Connection 리소스에 복사됩니다. 클래식 배포 모델에서 가상 네트워크 간의 연결은 가상 네트워크를 나타내는 로컬 네트워크 사이트에 두 개의 IPsec 터널을 만들어 수행합니다. 로컬 네트워크 게이트웨이가 필요하지 않은 리소스 관리자 모델에서 가상 네트워크-가상 네트워크 연결 형식으로 변환됩니다. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>마이그레이션 후 자동화 및 도구 변경
클래식 배포 모델에서 리소스 관리자 배포 모델로 리소스를 마이그레이션하는 과정의 일부로 기존 자동화 또는 도구를 업데이트하여 마이그레이션 후에도 계속 작동하도록 해야 합니다.


## <a name="next-steps"></a>다음 단계

Linux의 경우:

* [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션 개요](./linux/migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 마이그레이션 계획](./linux/migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](./windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](./linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션을 지원하기 위한 커뮤니티 도구](./windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [가장 일반적인 마이그레이션 오류 검토](./linux/migration-classic-resource-manager-errors.md?toc=/azure/virtual-machines/linux/toc.json)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션과 관련된 가장 자주 묻는 질문 검토](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Windows의 경우:

* [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션 개요](./windows/migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 마이그레이션 계획](./windows/migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](./windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](./linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Resource Manager 마이그레이션에 대한 VPN Gateway 클래식](../vpn-gateway/vpn-gateway-classic-resource-manager-migration.md)
* [클래식에서 Resource Manager 배포 모델로 ExpressRoute 회로 및 연결된 가상 네트워크 마이그레이션](../expressroute/expressroute-migration-classic-resource-manager.md)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션을 지원하기 위한 커뮤니티 도구](./windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [가장 일반적인 마이그레이션 오류 검토](./windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션과 관련된 가장 자주 묻는 질문 검토](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
