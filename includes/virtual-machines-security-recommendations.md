---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/13/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: d0ede337f2ae17f28cd02df7ed35919bd9955e7a
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048554"
---
이 문서에는 Azure Virtual Machines에 대 한 보안 권장 사항이 포함 되어 있습니다. 이러한 권장 사항을 따라 공유 책임에 대 한 모델에 설명 된 보안 의무를 달성 합니다. 또한 권장 사항은 웹 앱 솔루션에 대 한 전반적인 보안을 개선 하는 데 도움이 됩니다. Microsoft에서 서비스 공급자 역할을 수행 하는 데 필요한 사항에 대 한 자세한 내용은 [클라우드 컴퓨팅을 위한 공유 책임](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)을 참조 하세요.

이 문서의 권장 사항 중 일부는 Azure Security Center 하 여 자동으로 해결할 수 있습니다. Azure에서 리소스에 대 한 첫 번째 방어선은 Azure Security Center입니다. Azure 리소스의 보안 상태를 주기적으로 분석 하 여 잠재적인 보안 취약성을 식별 합니다. 그런 다음 취약점을 해결 하는 방법을 권장 합니다. 자세한 내용은 [Azure Security Center의 보안 권장 사항](../articles/security-center/security-center-recommendations.md)을 참조 하세요.

Azure Security Center에 대 한 일반 정보 [는 Azure Security Center 무엇입니까?](../articles/security-center/security-center-intro.md)를 참조 하세요.

## <a name="general"></a>일반

| 권장 사항 | 설명 | 보안 센터 |
|-|----|--|
| 사용자 지정 VM 이미지를 빌드할 때 최신 업데이트를 적용 합니다. | 이미지를 만들기 전에 이미지에 포함 될 모든 응용 프로그램 및 운영 체제에 대 한 최신 업데이트를 설치 합니다.  | - |
| 최신 상태로 VM 유지 | Azure Automation에서 [업데이트 관리](../articles/automation/automation-update-management.md) 솔루션을 사용 하 여 Azure에서 Windows 및 Linux 컴퓨터에 대 한 운영 체제 업데이트를 관리할 수 있습니다. | [예](../articles/security-center/security-center-apply-system-updates.md) |
| Vm을 백업 합니다. | [Azure Backup](../articles/backup/backup-overview.md) 는 응용 프로그램 데이터를 보호 하 고 운영 비용을 최소화 합니다. 애플리케이션 오류로 인해 데이터가 손상되고 사용자 오류로 인해 애플리케이션에 버그가 발생할 수 있습니다. Azure Backup는 Windows 및 Linux를 실행 하는 Vm을 보호 합니다. | - |
| 복원 력 및 가용성 향상을 위해 여러 Vm을 사용 합니다. | VM에서 항상 사용 가능 해야 하는 응용 프로그램을 실행 하는 경우 여러 Vm 또는 [가용성 집합](../articles/virtual-machines/windows/manage-availability.md)을 사용 합니다. | - |
| BCDR (비즈니스 연속성 및 재해 복구) 전략을 채택 합니다. | Azure Site Recovery를 사용 하면 비즈니스 연속성을 지원 하도록 설계 된 다양 한 옵션을 선택할 수 있습니다. 다른 복제 및 장애 조치 (failover) 시나리오를 지원 합니다. 자세한 내용은 [Site Recovery 정보](../articles/site-recovery/site-recovery-overview.md)를 참조 하세요. | - |

## <a name="data-security"></a>데이터 보안

| 권장 사항 | 설명 | 보안 센터 |
|-|----|--|
| 운영 체제 디스크를 암호화 합니다. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) 는 Windows 및 LINUX IaaS VM 디스크를 암호화 하는 데 도움이 됩니다. 필요한 키가 없으면 암호화 된 디스크의 내용을 읽을 수 없습니다. 디스크 암호화는 디스크가 복사 된 경우에는 무단으로 액세스할 수 없는 방식으로 저장 된 데이터를 보호 합니다.| [예](../articles/security-center/security-center-apply-disk-encryption.md) |
| 데이터 디스크를 암호화 합니다. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) 는 Windows 및 LINUX IaaS VM 디스크를 암호화 하는 데 도움이 됩니다. 필요한 키가 없으면 암호화 된 디스크의 내용을 읽을 수 없습니다. 디스크 암호화는 디스크가 복사 된 경우에는 무단으로 액세스할 수 없는 방식으로 저장 된 데이터를 보호 합니다.| -  |
| 설치 된 소프트웨어를 제한 합니다. | 설치 된 소프트웨어를 솔루션을 성공적으로 적용 하는 데 필요한 것으로 제한 합니다. 이 지침을 통해 솔루션의 공격 노출 영역을 줄일 수 있습니다. | - |
| 바이러스 백신 또는 맬웨어 방지 프로그램을 사용 합니다. | Azure에서 Microsoft, Symantec, Trend 마이크로 및 Kaspersky와 같은 보안 공급 업체의 맬웨어 방지 소프트웨어를 사용할 수 있습니다. 이 소프트웨어는 악성 파일, 애드웨어 및 기타 위협 으로부터 Vm을 보호 하는 데 도움이 됩니다. 응용 프로그램 워크 로드에 따라 Microsoft 맬웨어 방지 프로그램을 배포할 수 있습니다. 기본 보안 기본 설정 또는 고급 사용자 지정 구성을 사용 합니다. 자세한 내용은 [Azure Cloud Services 및 Virtual Machines에 대 한 Microsoft 맬웨어 방지](../articles/security/azure-security-antimalware.md)를 참조 하세요. | - |
| 키와 비밀을 안전 하 게 저장 합니다. | 응용 프로그램 소유자에 게 중앙에서 관리 되는 보안 옵션을 제공 하 여 비밀 및 키 관리를 간소화 합니다. 이 관리는 실수로 인 한 손상 또는 누출의 위험을 줄여줍니다. Azure Key Vault는 FIPS 140-2 수준 2로 인증 된 Hsm (하드웨어 보안 모듈)에 키를 안전 하 게 저장할 수 있습니다. FIPs 140.2 수준 3을 사용 하 여 키와 비밀을 저장 해야 하는 경우 [Azure 전용 HSM](../articles/dedicated-hsm/overview.md)을 사용할 수 있습니다. | - |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리 

| 권장 사항 | 설명 | 보안 센터 |
|-|----|--|
| VM 인증을 중앙 집중화 합니다. | [Azure Active Directory 인증](../articles/active-directory/develop/authentication-scenarios.md)을 사용 하 여 Windows 및 Linux vm의 인증을 중앙 집중화할 수 있습니다. | - |

## <a name="monitoring"></a>모니터링

| 권장 사항 | 설명 | 보안 센터 |
|-|----|--|
| Vm을 모니터링 합니다. | [VM용 Azure Monitor](../articles/azure-monitor/insights/vminsights-overview.md) 를 사용 하 여 Azure vm 및 가상 머신 확장 집합의 상태를 모니터링할 수 있습니다. VM의 성능 문제로 인해 가용성의 보안 원칙을 위반하는 서비스 중단이 발생할 수 있습니다. | - |

## <a name="networking"></a>네트워킹

| 권장 사항 | 설명 | 보안 센터 |
|-|----|--|
| 관리 포트에 대 한 액세스를 제한 합니다. | 공격자는 개방형 관리 포트의 공용 클라우드 IP 범위를 검색 하 고 일반 암호 및 알려진 패치가 적용 되지 않은 취약점과 같은 "쉬운" 공격을 시도 합니다. [JIT (just-in-time) vm 액세스](../articles/security-center/security-center-just-in-time.md) 를 사용 하 여 Azure vm에 대 한 인바운드 트래픽을 잠글 수 있으며, 필요할 때 vm에 대 한 쉬운 연결을 제공 하는 동시에 공격에 대 한 노출을 줄일 수 있습니다. | - |
| 네트워크 액세스를 제한 합니다. | 네트워크 보안 그룹을 사용 하면 네트워크 액세스를 제한 하 고 노출 된 엔드포인트의 수를 제어할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹 만들기, 변경 또는 삭제](../articles/virtual-network/manage-network-security-group.md)를 참조 하세요. | - |

## <a name="next-steps"></a>다음 단계

응용 프로그램 공급자에 게 문의 하 여 추가 보안 요구 사항에 대해 알아보세요. 보안 응용 프로그램을 개발 하는 방법에 대 한 자세한 내용은 [보안 개발 설명서](../articles/security/fundamentals/abstract-develop-secure-apps.md)를 참조 하세요.
