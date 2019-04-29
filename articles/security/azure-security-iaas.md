---
title: Azure의 IaaS 작업에 대한 보안 모범 사례 | Microsoft Docs
description: " Azure IaaS로 작업을 마이그레이션하면 설계를 다시 평가할 기회를 얻게 됩니다. "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2018
ms.author: barclayn
ms.openlocfilehash: da165634f5323183b633ee3c8a59e0d2607e8ef1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586533"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Azure의 IaaS 작업에 대한 보안 모범 사례

대부분 IaaS(Infrastructure as a Service) 시나리오에서 [Azure VM(가상 머신)](https://docs.microsoft.com/azure/virtual-machines/)은 클라우드 컴퓨팅을 사용하는 조직에 주요 워크로드입니다. 이 사실은 조직이 워크로드를 클라우드에 천천히 마이그레이션하고 싶은 [하이브리드 시나리오](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx)에서 명백합니다. 이러한 시나리오에서 [IaaS에 대한 일반적인 보안 고려 사항](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)을 따르고 모든 VM에 보안 모범 사례를 적용합니다.

보안에 대한 사용자의 책임은 클라우드 서비스의 형식에 기반합니다. 다음 차트에는 Microsoft와 여러분 모두에 대한 책임의 균형이 요약되어 있습니다.

![책임 영역](./media/azure-security-iaas/sec-cloudstack-new.png)

보안 요구 사항은 다양한 워크로드 유형을 포함한 여러 요인에 따라 달라집니다. 이러한 모범 사례 중 어떤 것도 혼자서는 시스템 보안을 유지할 수는 없습니다. 보안의 다른 요소와 마찬가지로, 적절한 옵션을 선택하고 각 솔루션의 간극을 해소하면서 서로 어떻게 보완할 수 있는지 파악해야 합니다.

이 문서에서는 VM 및 운영 체제에 대한 보안 모범 사례를 설명합니다.

모범 사례는 의견의 일치를 기반으로 하며 현재 Azure 플랫폼 기능 및 기능 집합과 함께 작동합니다. 시간이 지남에 따라 의견 및 기술이 변경될 수 있으므로 이 문서는 해당 변경 내용을 반영하도록 업데이트됩니다.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>인증 및 액세스 제어를 사용하여 VM 보호
VM을 보호하는 첫 번째 단계는 승인된 사용자만 새 VM을 설정하고 VM에 액세스할 수 있게 하는 것입니다.

**모범 사례**: VM 액세스 제어   
**세부 정보**: [Azure 정책](../azure-policy/azure-policy-introduction.md)을 사용하여 조직의 리소스에 대한 규칙을 설정하고 사용자 지정된 정책을 만듭니다. [리소스 그룹](../azure-resource-manager/resource-group-overview.md)과 같은 리소스에 이러한 정책을 적용합니다. 리소스 그룹에 속한 VM에서 해당 정책을 상속합니다.

조직에 구독이 많으면 구독에 대한 액세스, 정책 및 준수를 효율적으로 관리하는 방법이 필요할 수 있습니다. [Azure 관리 그룹](../azure-resource-manager/management-groups-overview.md)은 구독 상위 수준의 범위를 제공합니다. 관리 그룹(컨테이너)에 구독을 구성하고 거버넌스 조건을 해당 그룹에 적용합니다. 관리 그룹에 속하는 모든 구독은 그룹에 적용되는 조건을 자동으로 상속합니다. 관리 그룹은 어떤 형식의 구독을 사용하든 관계 없이 대규모의 엔터프라이즈급 관리를 제공합니다.

**모범 사례**: VM 설정 및 배포에서 가변성 감소   
**세부 정보**: [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) 템플릿을 사용하여 배포 선택을 강화하고 사용자 환경에서 VM을 쉽게 이해하고 목록을 만듭니다.

**모범 사례**: 권한 있는 액세스 보호   
**세부 정보**: [최소 권한 접근 방식](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) 및 기본 제공 Azure 역할을 사용하여 사용자가 VM에 액세스하고 VM을 설정할 수 있도록 합니다.

- [가상 머신 Contributor](../role-based-access-control/built-in-roles.md#virtual-machine-contributor): VM을 관리할 수 있으나 해당 VM이 연결된 가상 네트워크 또는 스토리지 계정은 관리할 수 없습니다.
- [클래식 가상 머신 Contributor](../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): 클래식 배포 모델을 사용하여 만든 VM을 관리할 수 있으나 VM이 연결된 가상 네트워크 또는 스토리지 계정은 관리할 수 없습니다.
- [보안 관리자](../role-based-access-control/built-in-roles.md#security-admin): Security Center에서만: 보안 정책 보기, 보안 상태 보기, 보안 정책 편집, 경고 및 권장 사항 보기, 경고 및 권장 사항 해제를 수행합니다.
- [DevTest Lab 사용자](../role-based-access-control/built-in-roles.md#devtest-labs-user): 모든 항목을 볼 수 있으며 VM을 연결, 시작, 다시 시작 및 종료할 수 있습니다.

구독 관리자 및 공동 관리자는 이 설정을 변경하여 구독에서 모든 VM의 관리자로 만들 수 있습니다. 모든 구독 관리자 및 공동 관리자를 신뢰하여 모든 머신에 로그인하도록 합니다.

> [!NOTE]
> 동일한 수명 주기의 VM을 동일한 리소스 그룹으로 통합하는 것이 좋습니다. 리소스 그룹을 사용하여 리소스에 대한 비용 청구를 배포, 모니터링 및 롤업할 수 있습니다.
>
>

VM 액세스 및 설정을 제어하는 조직은 해당 VM의 전반적인 보안을 개선합니다.

## <a name="use-multiple-vms-for-better-availability"></a>가용성 향상을 위해 여러 VM 사용
VM에서 고가용성의 중요한 애플리케이션을 실행할 경우에는 여러 VM을 사용하는 것이 좋습니다. 가용성 향상을 위해 [가용성 집합](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)을 사용합니다.

가용성 집합은 해당 집합에 배치한 VM 리소스가 Azure 데이터 센터에 배포될 때 서로 간에 격리되도록 하기 위해 Azure에서 사용할 수 있는 논리적 그룹화입니다. Azure는 가용성 집합에 배치한 VM을 여러 물리적 서버, 계산 랙, 스토리지 단위 및 네트워크 스위치에서 실행되도록 합니다. 하드웨어 또는 Azure 소프트웨어 오류가 발생할 경우 VM의 하위 집합에만 영향을 주는 한편 전체 애플리케이션은 고객이 계속 사용할 수 있습니다. 가용성 집합은 안정적인 클라우드 솔루션을 빌드하려고 할 때 필수적인 기능입니다.

## <a name="protect-against-malware"></a>맬웨어로부터 보호
맬웨어 방지 보호를 설치하여 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별하고 제거해야 합니다. [Microsoft Antimalware](azure-security-antimalware.md) 또는 Microsoft 파트너의 엔드포인트 보호 솔루션([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/search/result.aspx?q=Windows+defender+endpoint+protection) 및 [System Center Endpoint Protection](https://www.microsoft.com/search/result.aspx?q=System+Center+endpoint+protection))을 설치할 수 있습니다.

Microsoft 맬웨어 방지 프로그램에는 실시간 보호, 예약된 검색, 맬웨어 치료, 서명 업데이트, 엔진 업데이트, 샘플 보고 및 제외 이벤트 컬렉션과 같은 기능이 포함됩니다. 프로덕션 환경과는 별도로 호스트되는 환경의 경우 VM 및 클라우드 서비스를 보호하는 데 맬웨어 방지 확장을 사용할 수 있습니다.

배포 및 기본 제공 검색(경고 및 인시던트)을 용이하게 하기 위해 [Azure Security Center](https://docs.microsoft.com/azure/security-center/)와 Microsoft 맬웨어 방지 프로그램 및 파트너 솔루션을 통합할 수 있습니다.

**모범 사례**: 맬웨어로부터 보호하는 맬웨어 방지 솔루션 설치   
**세부 정보**: [Microsoft 파트너 솔루션 또는 Microsoft 맬웨어 방지 프로그램 설치](../security-center/security-center-install-endpoint-protection.md)

**모범 사례**: 보호 상태를 모니터링하기 위해 Security Center와 맬웨어 방지 솔루션 통합   
**세부 정보**: [Security Center를 사용하여 엔드포인트 보호 문제 관리](../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>VM 업데이트 관리
모든 온-프레미스 VM과 같이 Azure VM은 사용자가 관리하도록 합니다. Azure에서는 Windows 업데이트를 푸시하지 않습니다. VM 업데이트를 관리해야 합니다.

**모범 사례**: 최신 상태로 VM 유지   
**세부 정보**: Azure Automation의 [업데이트 관리](../automation/automation-update-management.md) 솔루션을 사용하면 Azure, 온-프레미스 환경 또는 다른 클라우드 공급자에 배포된 Windows 및 Linux 컴퓨터에 대한 운영 체제 업데이트를 관리할 수 있습니다. 모든 에이전트 컴퓨터에서 사용 가능한 업데이트의 상태를 신속하게 평가하고 서버에 대한 필수 업데이트를 설치하는 프로세스를 관리할 수 있습니다.

업데이트 관리를 통해 관리되는 컴퓨터는 다음 구성을 사용하여 평가 및 업데이트 배포를 수행합니다.

- Windows 또는 Linux용 MMA(Microsoft Monitoring Agent)
- Linux용 PowerShell DSC(필요한 상태 구성)
- Automation Hybrid Runbook Worker
- Windows 컴퓨터용 Microsoft Update 또는 WSUS(Windows Server Update Services)

Windows 업데이트를 사용하면 해당 설정을 활성화 상태로 유지하는 것이 좋습니다.

**모범 사례**: 배포 시 빌드한 이미지에 최신 Windows 업데이트가 포함되는지 확인   
**세부 정보**: 모든 배포의 첫 번째 단계로 모든 Windows 업데이트를 확인 및 설치합니다. 이 측정값은 사용자 또는 사용자의 고유한 라이브러리에서 제공되는 이미지를 배포할 때 적용하는 것이 특히 중요합니다. Azure Marketplace의 이미지가 기본적으로 자동으로 업데이트되지만 공용 릴리스 이후 최대 몇 주 동안 지연 시간이 발생할 수 있습니다.

**모범 사례**: OS의 새 버전을 강제로 적용하기 위해 주기적으로 VM 다시 배포   
**세부 정보**: [Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-authoring-templates.md)을 사용하여 VM을 정의하므로 쉽게 배포할 수 있습니다. 템플릿을 사용하면 필요한 경우 패치된 보안 VM을 제공합니다.

**모범 사례**: 최신 보안 업데이트 설치   
**세부 정보**: 고객이 Azure로 이동하는 첫 번째 작업 일부는 실습 및 외부 연결 시스템입니다. Azure VM이 인터넷에 액세스할 수 있어야 하는 애플리케이션 또는 서비스를 호스트하는 경우 패치 적용에 유의해야 합니다. 운영 체제에 대해 패치를 설치합니다. 파트너 애플리케이션의 취약점을 패치하지 않으면 적절한 패치 관리가 진행되어도 우회할 수 있는 문제가 야기될 수도 있습니다.

**모범 사례**: 백업 솔루션 배포 및 테스트   
**세부 정보**: 다른 작업을 처리하는 것과 동일한 방식으로 백업을 처리해야 합니다. 클라우드로 확장되는 프로덕션 환경에 속하는 시스템의 경우도 마찬가지입니다.

테스트 및 개발 시스템은 온-프레미스 환경에 대한 경험을 토대로 사용자에게 친숙한 비슷한 복원 기능을 제공하는 백업 전략을 따라야 합니다. Azure로 이동되는 프로덕션 작업은 가능한 경우 기존 백업 솔루션과 통합되어야 합니다. 또는 [Azure Backup](../backup/backup-azure-vms-first-look-arm.md)을 사용하여 백업 요구를 보다 쉽게 처리할 수 있습니다.

소프트웨어 업데이트 정책을 적용하지 않는 조직은 알려진, 이전에 고정 취약점을 악용하는 위협에 더 많이 노출됩니다. 산업 규정을 준수하려면 회사는 클라우드에 있는 워크로드의 보안을 보장할 수 있도록 성실하게 올바른 보안 컨트롤을 사용하고 있음을 증명해야 합니다.

기존 데이터센터 및 Azure IaaS에 대한 소프트웨어 업데이트 모범 사례에는 많은 유사점이 있습니다. Azure에 위치한 VM을 포함하도록 현재 소프트웨어 업데이트 정책을 평가하는 것이 좋습니다.

## <a name="manage-your-vm-security-posture"></a>VM 보안 태세 관리
사이버 위협은 진화하고 있습니다. VM을 보호하려면 신속하게 위협을 감지하고 리소스에 대한 무단 액세스를 방지하고 경고를 트리거하며 거짓 오류를 줄일 수 있는 모니터링 기능이 필요합니다.

[Windows](../security-center/security-center-virtual-machine.md) 및 [Linux VM](../security-center/security-center-linux-virtual-machine.md)의 보안 태세를 모니터링하기 위해 [Azure Security Center](../security-center/security-center-intro.md)를 사용합니다. Security Center에서 다음 기능을 활용하여 VM을 보호합니다.

- 권장된 구성 규칙으로 OS 보안 설정 적용
- 누락될 수 있는 시스템 보안 및 중요 업데이트 식별 및 다운로드
- 엔드포인트 맬웨어 방지 보호에 대한 권장 사항 배포
- 디스크 암호화 유효성 검사
- 취약점 평가 및 수정
- 위협 감지

Security Center는 위협을 적극적으로 모니터링할 수 있으며 잠재적 위협은 보안 경고에 표시됩니다. 상호 관련된 위협은 보안 인시던트라고 하는 단일 보기로 집계됩니다.

Security Center에서 데이터를 저장 [Azure Monitor 로그](../log-analytics/log-analytics-overview.md)합니다. Azure Monitor 로그는 응용 프로그램 및 리소스의 작업에 대 한 정보를 제공 하는 쿼리 언어 및 분석 엔진을 제공 합니다. 또한 데이터는 온-프레미스의 가상 머신에 설치된 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md), 관리 솔루션 및 클라우드 또는 에이전트에서 수집됩니다. 이렇게 공유된 기능은 환경에 대한 전체적인 그림을 만드는 데 도움이 됩니다.

VM에 대해 강력한 보안을 적용하지 않는 조직은 권한이 없는 사용자가 보안 컨트롤을 억제하는 잠재적인 시도를 알지 못하는 상태가 됩니다.

## <a name="monitor-vm-performance"></a>VM 성능 모니터링
리소스 남용은 VM 프로세스가 소비해야 하는 것보다 더 많은 리소스를 소비하는 경우 문제가 될 수 있습니다. VM의 성능 문제로 인해 가용성의 보안 원칙을 위반하는 서비스 중단이 발생할 수 있습니다. CPU 또는 메모리 사용량이 높으면 DoS(서비스 거부) 공격을 나타낼 수 있기 때문에 IIS 또는 다른 웹 서버를 호스팅하는 VM에 특히 중요합니다. 문제가 발생하는 동안 VM 액세스를 적극적으로 모니터링할 뿐만 아니라 정상 작업 중에 측정된 기준 성능에 대해 사전 대처식으로 모니터링해야 합니다.

[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md)를 사용하여 리소스 상태에 대한 가시성을 얻는 것이 좋습니다. Azure Monitor 기능:

- [리소스 진단 로그 파일](../azure-monitor/platform/diagnostic-logs-overview.md): VM 리소스를 모니터링하고 성능 및 가용성을 손상시킬 수 있는 잠재적인 문제를 식별할 수 있습니다.
- [Azure 진단 확장](../azure-monitor/platform/diagnostics-extension-overview.md): Windows VM에 모니터링 및 진단 기능을 제공합니다. 확장을 [Azure Resource Manager 템플릿](../virtual-machines/windows/extensions-diagnostics-template.md)에 속하도록 포함시켜서 이러한 기능을 사용하도록 설정할 수 있습니다.

VM 성능을 모니터링하지 않는 조직은 성능 패턴의 특정 변경 내용이 정상 또는 비정상인지 확인할 수 없습니다. 정상보다 더 많은 리소스를 소비하는 VM은 외부 리소스의 잠재적인 공격 또는 VM에서 손상된 프로세스가 실행 중임을 나타낼 수 있습니다.

## <a name="encrypt-your-virtual-hard-disk-files"></a>가상 하드 디스크 파일 암호화
VHD(가상 하드 디스크)를 암호화하여 암호화 키 및 비밀과 함께 저장소의 미사용 부팅 볼륨 및 데이터 볼륨을 보호할 수 있는 것이 좋습니다.

[Azure Disk Encryption](azure-security-disk-encryption-overview.md)을 사용하면 Windows 및 Linux IaaS 가상 머신 디스크를 암호화할 수 있습니다. Azure Disk Encryption은 산업 표준인 Windows의 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 기능과 Linux의 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 기능을 사용하여 OS 및 데이터 디스크를 위한 볼륨 암호화를 제공합니다. 또한 고객이 Key Vault 구독에서 디스크 암호화 키 및 암호를 관리 및 제어할 수 있도록 [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/)에 통합되어 있습니다. 이 솔루션은 가상 머신 디스크에 있는 모든 데이터가 Azure Storage에서 미사용 시 암호화되도록 합니다.

Azure Disk Encryption을 사용하는 모범 사례는 다음과 같습니다.

**모범 사례**: VM에서 암호화 사용   
**세부 정보**: Azure Disk Encryption은 암호화 키를 생성하고 키 자격 증명 모음에 작성합니다. Key Vault에서 암호화 키를 관리하려면 Azure AD 인증이 필요합니다. 이를 위해 Azure AD 애플리케이션을 만듭니다. 인증을 위해 클라이언트 비밀 기반 인증 또는 [클라이언트 인증서 기반 Azure AD 인증](../active-directory/active-directory-certificate-based-authentication-get-started.md)을 사용할 수 있습니다.

**모범 사례**: 암호화 키에 대한 추가 보안 계층에 KEK(키 암호화 키) 사용 KEK를 키 자격 증명 모음에 추가합니다.   
**세부 정보**: 사용 된 [추가 AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey) key vault에 키 암호화 키를 만드는 cmdlet입니다. 또한 키를 관리하기 위해 온-프레미스 HSM(하드웨어 보안 모듈)에서 KEK를 가져올 수도 있습니다. 자세한 내용은 [Key Vault 설명서](../key-vault/key-vault-hsm-protected-keys.md)를 참조하세요. 키 암호화 키가 지정되면 Azure Disk Encryption에서 해당 키를 사용하여 Key Vault에 쓰기 전에 암호화 비밀을 래핑합니다. 온-프레미스 키 관리 HSM에서 이 키의 에스크로 복사본을 유지하면 키를 실수로 삭제하는 경우에 추가적인 보호를 제공합니다.

**모범 사례**: 디스크가 암호화되기 전에 먼저 [스냅숏](../virtual-machines/windows/snapshot-copy-managed-disk.md) 및/또는 백업 수행 백업은 암호화 중에 예상치 않은 오류가 발생하는 경우 복구 옵션을 제공합니다.   
**세부 정보**: 암호화가 수행되기 전에 관리 디스크가 있는 VM은 백업해야 합니다. 백업 설정 되 면 사용할 수는 **집합 AzVMDiskEncryptionExtension** 지정 하 여 관리 되는 디스크를 암호화 하는 cmdlet를 *-skipVmBackup* 매개 변수입니다. 암호화된 VM을 백업하고 복원하는 방법에 대한 자세한 내용은 [Azure Backup](../backup/backup-azure-vms-encryption.md) 문서를 참조하세요.

**모범 사례**: 암호화 비밀이 지역 경계를 넘지 않도록 하려면 Azure Disk Encryption에서는 키 자격 증명 모음과 VM을 동일한 지역에 배치해야 함   
**세부 정보**: 암호화할 VM과 동일한 지역에 있는 키 자격 증명 모음을 만들고 사용합니다.

Azure Disk Encryption을 적용할 때 다음 비즈니스 요구 사항을 충족할 수 있습니다.

- IaaS VM은 업계 표준 암호화 기술을 통해 미사용 시 보호되어 조직의 보안 및 규정 준수 요구 사항을 처리합니다.
- IaaS VM은 고객이 제어하는 키 및 정책에 따라 시작되고, 키 자격 증명 모음에서 이러한 사용을 감사할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure 보안 모범 사례 및 패턴](security-best-practices-and-patterns.md)에서 Azure를 사용하여 클라우드 솔루션을 디자인하고, 배포하고, 관리할 때 사용할 수 있는 더 많은 보안 모범 사례를 참조하세요.

Azure 보안 및 관련 Microsoft 서비스에 대한 보다 일반적인 정보를 제공하는 다음 리소스도 확인할 수 있습니다.
* [Azure 보안 팀 블로그](https://blogs.msdn.microsoft.com/azuresecurity/) – Azure Security 관련 최신 정보를 확인할 수 있습니다.
* [Microsoft 보안 응답 센터](https://technet.microsoft.com/library/dn440717.aspx) - Azure와 관련된 문제를 비롯한 Microsoft 보안 취약점을 보고하거나 secure@microsoft.com으로 이메일을 보낼 수 있습니다.
