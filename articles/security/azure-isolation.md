---
title: Azure 공용 클라우드에서 격리 | Microsoft Docs
description: 애플리케이션 또는 엔터프라이즈의 요구 사항을 충족하기 위해 자동으로 확장하거나 축소할 수 있는 다양한 계산 인스턴스와 서비스를 포함하는 클라우드 기반 컴퓨팅 서비스에 대해 알아봅니다.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: f5d1c66cb049ab9ec52db619d55a4bb3e485e4b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60588539"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Azure 공용 클라우드에서 격리
##  <a name="introduction"></a>소개
### <a name="overview"></a>개요
현재 및 미래의 Azure 고객이 Azure 플랫폼에서 사용할 수 있는 다양한 보안 관련 기능을 이해하고 활용할 수 있도록 Microsoft는 일련의 백서, 보안 개요, 모범 사례 및 검사 목록을 개발했습니다.
주제는 폭과 깊이에 따라 다양하게 다루어지며 정기적으로 업데이트됩니다. 이 문서는 다음의 요약 섹션에서 설명하는 시리즈의 일부입니다.

### <a name="azure-platform"></a>Azure 플랫폼
Azure는 매우 다양한 운영 체제, 프로그래밍 언어, 프레임워크, 도구, 데이터베이스 및 디바이스를 지원하는 개방적이고 유연한 클라우드 서비스 플랫폼입니다. 예를 들어 다음을 수행할 수 있습니다.
- Docker 통합으로 Linux 컨테이너를 실행합니다.
- JavaScript, Python, .NET, PHP, Java 및 Node.js를 사용하여 앱을 빌드합니다.
- iOS, Android 및 Windows 디바이스용 백 엔드를 빌드합니다.

Microsoft Azure는 수백만의 개발자와 IT 전문가가 이미 믿고 사용하고 있는 동일한 수준의 기술을 지원합니다.

IT 자산을 만들거나 공용 클라우드 서비스 공급자로 마이그레이션하는 경우 조직에서 제공하는 서비스와 제어를 통해 애플리케이션과 데이터를 보호할 수 있는 해당 조직의 능력에 의존하여 클라우드 기반 자산의 보안을 관리합니다.

Azure의 인프라는 수백만 명의 고객을 동시에 호스팅하기 위한 시설에서 애플리케이션에 이르는 인프라를 설계하며 비즈니스의 보안 요구 사항을 충족하는 신뢰할 수 있는 기반을 제공합니다. 또한 Azure는 다양하게 구성 가능한 보안 옵션 및 제어 능력을 제공하므로 배포에 대한 고유한 요구 사항에 맞게 보안을 사용자 지정할 수 있습니다. 이 문서는 이러한 요구 사항을 충족하는 데 도움이 됩니다.

### <a name="abstract"></a>요약

Microsoft Azure를 사용하면 공유된 실제 인프라에서 애플리케이션과 VM(가상 머신)을 실행할 수 있습니다. 클라우드 환경에서 애플리케이션을 실행하는 데 있어 가장 중요한 경제적 동기 중 하나는 여러 고객에게 공유 리소스에 대한 비용을 분산할 수 있는 능력입니다. 이러한 다중 테넌트 방식은 저렴한 비용으로 서로 다른 고객 간에 리소스를 다중화함으로써 효율성을 향상시킵니다. 아쉽게도 물리적 서버 및 기타 인프라 리소스를 공유하여 임의의 잠재적 악성 사용자에게 속할 수 있는 중요한 애플리케이션과 VM을 실행할 위험이 있습니다.

이 문서에서는 Microsoft Azure에서 악의적인 사용자와 악의적이지 않은 사용자를 모두 격리하는 방법을 설명하고, 설계자에게 다양한 격리 옵션을 제공하여 클라우드 솔루션을 설계하는 데 유용한 정보를 제공합니다. 이 백서에서는 Azure 플랫폼 및 고객 관련 보안 제어 기술에 중점을 두는 반면, SLA, 가격 책정 모델 및 DevOps 구현 방법에 대한 고려 사항은 다루지 않습니다.

## <a name="tenant-level-isolation"></a>테넌트 수준 격리
클라우드 컴퓨팅의 주요 이점 중 하나는 수많은 고객들에게 동시에 공유되는 일반적인 인프라 개념에 있으며 규모의 경제를 실현합니다. 이 개념을 다중 테넌트라고 합니다. Microsoft는 Microsoft Cloud Azure의 다중 테넌트 아키텍처에서 보안, 기밀성, 개인 정보 보호, 무결성 및 가용성 표준을 지원할 수 있도록 지속적으로 노력하고 있습니다.

클라우드 기반 작업 공간에서 테넌트는 해당 클라우드 서비스의 특정 인스턴스를 소유하고 관리하는 클라이언트나 조직으로 정의할 수 있습니다. 테넌트는 Microsoft Azure에서 제공하는 ID 플랫폼을 사용하여 조직에서 Microsoft 클라우드 서비스에 등록할 때 받고 소유하는 Azure AD(Azure Active Directory)의 전용 인스턴스일 뿐입니다.

각 Azure AD 디렉터리는 고유하며 다른 Azure AD 디렉터리와 구분됩니다. 회사 사무실 건물이 해당 조직에게만 속하는 안전한 자산인 것처럼 Azure AD 디렉터리도 조직에서만 사용하기 위해 자산을 보호하도록 설계되었습니다. Azure AD 아키텍처는 고객 데이터 및 ID 정보가 함께 혼합되지 않도록 격리합니다. 즉, 한 Azure AD 디렉터리의 사용자 및 관리자가 실수로 또는 악의적으로 다른 디렉터리의 데이터에 액세스할 수 없습니다.

### <a name="azure-tenancy"></a>Azure 테넌트
Azure 테넌트(Azure 구독)는 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)에서 "고객/청구" 관계 및 고유한 [테넌트](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant)를 나타냅니다. Microsoft Azure의 테넌트 수준 격리는 Azure Active Directory 및 이 서비스에서 제공하는 [역할 기반 제어](https://docs.microsoft.com/azure/role-based-access-control/overview)를 사용하여 이루어집니다. 각각의 Azure 구독은 하나의 Azure AD(Active Directory) 디렉터리와 연결됩니다.

사용자, 그룹 및 해당 디렉터리에서 애플리케이션은 Azure 구독에서 리소스를 관리할 수 있습니다. Azure Portal, Azure 명령줄 도구 또는 Azure 관리 API를 사용하여 이러한 액세스 권한을 할당할 수 있습니다. Azure AD 테넌트는 보안 경계를 사용하여 논리적으로 격리되므로 어떤 고객도 악의적으로 또는 실수로 공동 테넌트에 액세스하거나 손상시킬 수 없습니다. Azure AD는 분리된 네트워크 세그먼트에서 격리된 "운영 체제 미설치(bare metal)" 서버에서 실행되며, 여기서 호스트 수준 패킷 필터링과 Windows 방화벽은 원하지 않는 연결과 트래픽을 차단합니다.

- Azure AD의 데이터에 대한 액세스는 보안 토큰 서비스(STS)를 통해 사용자 인증이 필요합니다. 사용자의 존재, 활성화된 상태 및 역할에 대한 정보는 권한 부여 시스템에서 사용되어 대상 테넌트에 대해 요청된 액세스가 이 세션에서 사용자에 대한 권한이 있는지 확인합니다.

![Azure 테넌트](./media/azure-isolation/azure-isolation-fig1.png)


- 테넌트는 각각 별개의 컨테이너이며, 이들 사이에 아무 관계가 없습니다.

- 테넌트 관리자가 페더레이션을 통해 권한을 부여하거나 다른 테넌트의 사용자 계정을 프로비전하지 않는 한 테넌트 간에 액세스할 수 없습니다.

- Azure AD 서비스를 구성하고 Azure AD의 백 엔드 시스템에 직접 액세스하는 서버에 대한 물리적 액세스가 제한됩니다.

- Azure AD 사용자는 물리적 자산 또는 위치에 액세스할 수 없으므로 다음과 같은 논리적 RBAC 정책 검사를 무시할 수 없습니다.

진단 및 유지 관리 요구 사항에 따라 Just-In-Time 권한 상승 시스템을 사용하는 작업 모델이 필요하고 사용됩니다. Azure AD PIM(Privileged Identity Management)은 적격 관리자 개념을 도입하고 있습니다. [적격 관리자](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)는 때때로 권한 있는 액세스 권한이 필요지만 매일 액세스할 필요가 없는 사용자여야 합니다. 역할은 사용자가 액세스가 필요할 때까지 비활성으로 있다가, 활성화 프로세스를 완료하고 미리 정해진 시간 동안 활성 관리자가 됩니다.

![Azure AD Privileged Identity Management](./media/azure-isolation/azure-isolation-fig2.png)

Azure Active Directory는 테넌트에서만 소유하고 관리하는 컨테이너에 대한 정책과 권한으로 보호되는 자체 컨테이너에서 각 테넌트를 호스팅합니다.

테넌트 컨테이너의 개념은 포털부터 영구 저장소에 이르기까지 모든 계층의 디렉터리 서비스에 깊숙이 뿌리내리고 있습니다.

여러 Azure Active Directory 테넌트의 메타데이터가 동일한 물리적 디스크에 저장되어있는 경우에도 디렉터리 서비스에서 정의된 것 이외의 컨테이너 간에는 아무 관계가 없으므로 디렉터리 서비스에서 테넌트를 결정합니다.

### <a name="azure-role-based-access-control-rbac"></a>RBAC(역할 기반 Access Control)
[RBAC(역할 기반 Access Control)](https://docs.microsoft.com/azure/role-based-access-control/overview)는 Azure에 세분화된 액세스 관리를 제공하여 Azure 구독 내에서 사용할 수 있는 다양한 구성 요소를 공유할 수 있게 합니다. Azure RBAC를 사용하면 조직 내에서 직무를 분리하고, 사용자가 자신의 작업 수행에 필요한 항목에 따라 액세스 권한을 부여할 수 있습니다. Azure 구독 또는 리소스에서 모든 사람에게 무제한 권한을 제공하는 대신 특정 작업만 허용할 수 있습니다.

Azure RBAC에는 모든 리소스 유형에 적용되는 3가지 기본 역할이 있습니다.

- **소유자** 는 액세스 권한을 다른 사용자에게 위임할 수 있는 권한을 포함하여 모든 리소스에 대한 전체 액세스 권한을 보유합니다.

- **참여자** 는 모든 유형의 Azure 리소스를 만들고 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수 없습니다.

- **읽기 권한자** 는 기존 Azure 리소스를 볼 수 있습니다.

![Azure 역할 기반 Access Control](./media/azure-isolation/azure-isolation-fig3.png)

Azure의 나머지 RBAC 역할은 특정 Azure 리소스의 관리를 허용합니다. 예를 들어 Virtual Machine 참여자 역할을 사용하면 사용자가 가상 머신을 만들고 관리할 수 있습니다. 가상 머신이 연결되는 Azure Virtual Network 또는 서브넷에 대한 액세스 권한을 부여하지 않습니다.

[RBAC 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)은 Azure에서 사용할 수 있는 역할을 나열합니다. 각 기본 제공 역할이 사용자에게 부여하는 작업 및 범위를 지정합니다. 더 많은 제어를 위해 사용자 고유의 역할을 정의하려는 경우 [Azure RBAC에서 사용자 지정 역할](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)을 빌드하는 방법을 참조하세요.

Azure Active Directory의 몇 가지 다른 기능은 다음과 같습니다.
- Azure AD는 호스팅되는 위치에 관계 없이 SaaS 애플리케이션에 SSO를 사용할 수 있게 합니다. 애플리케이션 일부는 Azure AD를 사용하여 페더레이션되고 나머지는 암호 SSO를 사용합니다. 또한 페더레이션된 애플리케이션은 사용자 프로비전 및 [암호 보관](https://www.techopedia.com/definition/31415/password-vault)을 지원할 수도 있습니다.

- [Azure Storage](https://azure.microsoft.com/services/storage/)의 데이터 액세스는 인증을 통해 제어됩니다. 각 저장소 계정에는 기본 키([저장소 계정 키](https://docs.microsoft.com/azure/storage/storage-create-storage-account) 또는 SAK) 및 보조 비밀 키(공유 액세스 서명 또는 SAS)가 있습니다.

- Azure AD는 온-프레미스 디렉터리와 함께 [Active Directory Federation Services](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs), 동기화 및 복제를 사용하여 페더레이션을 통해 IaaS(Identity as a Service)를 제공합니다.

- [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)은 사용자에게 모바일 앱, 전화 통화 또는 문자 메시지를 사용하여 로그인을 검증하도록 요구하는 다단계 인증 서비스입니다. Azure AD와 함께 사용할 수 있으며 Azure Multi-Factor Authentication 서버를 통해 온-프레미스 리소스의 보안을 유지하도록 도와주며, SDK를 사용하여 사용자 지정 애플리케이션 및 디렉터리와도 사용될 수 있습니다.

- [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/)를 사용하면 도메인 컨트롤러를 배포하지 않고도 Azure 가상 머신이 도메인에 가입될 수 있습니다. 이러한 가상 머신에 회사 Active Directory 자격 증명을 사용하여 로그인하고 모든 Azure Virtual Machines에 보안 기준을 적용하도록 그룹 정책을 사용하여 도메인에 가입된 가상 머신을 관리할 수 있습니다.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)는 수억 개의 ID로 확장하는 소비자 지향 애플리케이션에 항상 사용 가능한 전역적인 ID 관리 서비스를 제공합니다. 이 서비스는 모바일 및 웹 플랫폼에 통합될 수 있습니다. 소비자는 기존 소셜 계정을 사용하거나 자격 증명을 만들어 사용자 지정할 수 있는 환경을 통해 모든 애플리케이션에 로그인할 수 있습니다.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Microsoft 관리자로부터 격리 및 데이터 삭제
권한이 없는 사람이 부적절하게 액세스하거나 사용하지 않도록 데이터를 보호하기 위해 Microsoft에서 강력한 조치를 취합니다. 이러한 운영 프로세스 및 제어는 사용자의 데이터에 대한 액세스를 제어하는 계약상의 약정을 제공하는 [Online Services 약관](https://aka.ms/Online-Services-Terms)으로 지지됩니다.

-   Microsoft 엔지니어는 클라우드에서 데이터에 대한 기본 액세스 권한을 갖지 없습니다. 대신 필요할 때만 관리 감독하에 액세스 권한이 부여됩니다. 이러한 액세스 권한은 신중하게 제어되고 로깅되며, 더 이상 필요하지 않게 되면 해지됩니다.

-   Microsoft는 타사에서 제한된 서비스를 대신 제공하도록 할 수 있습니다. 하도급업자는 고객 데이터에만 액세스하여 고객과 계약한 서비스를 제공할 수 있으며 다른 용도로는 사용하지 못합니다. 또한 계약상 고객의 정보를 기밀로 유지할 의무가 있습니다.

ISO/IEC 27001과 같은 감사된 인증을 갖춘 비즈니스 서비스는 합법적인 비즈니스 목적으로만 해당 액세스를 입증하기 위해 샘플 감사를 수행하는 Microsoft 및 공인 감사 기관에서 정기적으로 확인됩니다. 언제든지 어떤 이유로든 자신의 고객 데이터에 액세스할 수 있습니다.

데이터를 삭제하면 Microsoft Azure는 캐시된 복사본 또는 백업 복사본을 모두 포함하여 데이터를 삭제합니다. 범위 내 서비스의 경우 삭제는 보존 기간 종료 후 90일 이내에 발생합니다. (범위 내 서비스는 [Online Services 약관](https://aka.ms/Online-Services-Terms)의 데이터 처리 조항 섹션에 정의되어 있습니다.)

저장소로 사용되는 디스크 드라이브에 하드웨어 장애가 발생하면 Microsoft에서 교체하거나 복구하기 위해 제조업체에 반환하기 전에 안전하게 [지워지거나 제거됩니다](https://microsoft.com/trustcenter/privacy/you-own-your-data). 어떤 방법으로도 데이터를 복구할 수 없도록 드라이브의 데이터를 덮어씁니다.

## <a name="compute-isolation"></a>Compute 격리
Microsoft Azure는 애플리케이션 또는 엔터프라이즈의 요구 사항을 충족하도록 자동으로 확장하거나 축소할 수 있는 다양한 계산 인스턴스와 서비스를 포함하는 클라우드 기반 컴퓨팅 서비스를 제공합니다. 이러한 컴퓨팅 인스턴스와 서비스는 여러 수준에서 격리를 제공하여 고객이 요구하는 구성에서 유연성을 유지하면서 데이터를 보호합니다.

### <a name="isolated-virtual-machine-sizes"></a>격리 가상 머신 크기
Azure Compute는 특정 하드웨어 유형에서 격리되고 단일 고객 전용인 가상 머신 크기를 제공합니다.  이러한 가상 머신 크기는 규정 준수 및 규정 요구 사항과 같은 요소를 포함하는 작업에 대해 다른 고객으로부터 높은 수준의 격리가 필요한 작업에 가장 적합합니다.  고객은 [중첩된 가상 머신에 대한 Azure 지원](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)을 사용하여 이러한 격리된 가상 머신의 리소스를 보다 세분화할 수도 있습니다.

격리 크기를 사용하면 가상 머신이 특정 서버 인스턴스에서 하나만 실행되도록 보장합니다.  현재 격리 가상 머신 제품에는 다음이 포함됩니다.
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2

[여기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)에서 사용 가능한 격리 크기 각각에 대해 자세히 알아볼 수 있습니다.

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>루트 VM과 게스트 VM 간 Hyper-V 및 루트 OS 격리
Azure의 계산 플랫폼은 Hyper-V 가상 머신에서 모든 고객 코드를 실행한다는 것을 의미하는 머신 가상화를 기반으로 합니다. 각 Azure 노드(또는 네트워크 엔드포인트)에는 하드웨어를 통해 직접 실행되는 하이퍼바이저가 있으며, 노드는 여러 게스트 VM(Virtual Machines)으로 나누어집니다.


![루트 VM과 게스트 VM 간 Hyper-V 및 루트 OS 격리](./media/azure-isolation/azure-isolation-fig4.jpg)


또한 각 노드에는 호스트 OS를 실행하는 특별한 하나의 루트 VM도 있습니다. 중요한 경계는 하이퍼바이저와 루트 OS에서 관리하는 게스트 VM/루트 VM 간 및 게스트 VM 간을 격리하는 것입니다. 하이퍼바이저/루트 OS 페어링은 수십 년간의 Microsoft 운영 체제 보안 경험과 Microsoft Hyper-V의 최신 학습을 활용하여 강력한 게스트 VM 격리를 제공합니다.

Azure 플랫폼은 가상화된 환경을 사용합니다. 사용자 인스턴스는 물리적 호스트 서버에 대한 액세스 권한이 없는 독립 실행형 가상 머신으로 작동합니다.

Azure 하이퍼바이저는 마이크로 커널처럼 작동하고 VMBus라는 공유 메모리 인터페이스를 사용하여 게스트 가상 머신에서 호스트에 모든 하드웨어 액세스 요청을 처리하도록 전달합니다. 이렇게 하면 사용자가 시스템에 원시 읽기/쓰기/실행 액세스를 가져오는 것을 방지하고 시스템 리소스를 공유할 위험을 완화합니다.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>고급 VM 배치 알고리즘 및 측면 채널 공격으로부터 보호
VM 간 공격에는 두 가지 단계가 포함됩니다. 먼저 교착 상태가 발생한 VM 중 하나와 동일한 호스트에 악의적 사용자가 제어하는 VM을 배치하고, 다음으로 격리 경계를 위반하여 중요한 공격 대상 정보를 훔치거나 탐욕이나 파괴 행위로 성능에 영향을 미치는 것입니다. Microsoft Azure는 고급 VM 배치 알고리즘을 사용하고 시끄러운 이웃 VM을 포함하여 알려진 모든 측면 채널 공격으로부터 보호함으로써 두 단계를 모두 보호합니다.

### <a name="the-azure-fabric-controller"></a>Azure 패브릭 컨트롤러
Azure 패브릭 컨트롤러는 테넌트 워크로드에 대한 인프라 리소스를 할당하는 일을 담당하고 호스트에서 가상 머신에 단방향 통신을 관리합니다. Azure 패브릭 컨트롤러의 VM 배치 알고리즘은 매우 정교하며 물리적 호스트 수준으로 예측하는 것이 거의 불가능합니다.

![Azure 패브릭 컨트롤러](./media/azure-isolation/azure-isolation-fig5.png)

Azure 하이퍼바이저는 가상 머신 간의 메모리 및 프로세스 분리를 적용하고 네트워크 트래픽을 게스트 OS 테넌트로 안전하게 라우팅합니다. 이렇게 하면 VM 수준에서 측면 채널 공격의 가능성과 해당 공격을 제거합니다.

Azure에서 루트 VM은 FA(패브릭 에이전트)를 호스팅하는 루트 OS라고 하는 확정된 운영 체제를 실행하기 때문에 특별합니다. 이에 따라 FA는 고객 VM의 게스트 OS 내에서 GA(게스트 에이전트)를 관리합니다. FA는 저장소 노드도 관리합니다.

Azure 하이퍼바이저, 루트 OS/FA 및 고객 VM/GA의 모음은 계산 노드를 구성합니다. FA는 계산 및 스토리지 노드 외부에 있는 FC(패브릭 컨트롤러)로 관리됩니다(컴퓨팅 및 스토리지 클러스터는 별도의 FC로 관리됨). 고객이 실행 중인 애플리케이션의 구성 파일을 업데이트하는 경우 FC는 FA와 통신한 다음, GA에 접속하여 애플리케이션에 구성 변경 내용을 알립니다. 하드웨어 장애가 발생하면 FC에서 자동으로 사용 가능한 하드웨어를 찾아 이 하드웨어에서 VM을 다시 시작합니다.

![Azure 패브릭 컨트롤러](./media/azure-isolation/azure-isolation-fig6.jpg)

패브릭 컨트롤러에서 에이전트로의 통신은 단방향입니다. 에이전트는 컨트롤러의 요청에만 응답하는 SSL로 보호되는 서비스를 구현합니다. 컨트롤러 또는 다른 권한이 부여된 내부 노드에 대한 연결은 시작할 수 없습니다. FC는 모든 응답을 신뢰할 수 있는 것으로 처리합니다.


![패브릭 컨트롤러](./media/azure-isolation/azure-isolation-fig7.png)

격리는 게스트 VM/루트 VM 간과 게스트 VM 간으로 확장됩니다. 또한 Compute 노드는 보호를 강화하기 위해 스토리지 노드로부터 격리됩니다.


하이퍼바이저 및 호스트 OS는 네트워크 패킷 필터를 제공하여 신뢰할 수 없는 가상 머신에서 스푸핑된 트래픽을 생성하거나, 해당 주소로 지정되지 않은 트래픽을 받거나, 보호된 인프라 엔드포인트로 트래픽을 보내거나, 부적절한 브로드캐스트 트래픽을 보내거나 받을 수 없도록 합니다.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>패브릭 컨트롤러 에이전트에서 VM을 격리하도록 구성되는 추가 규칙
기본적으로 가상 머신을 만들 때 모든 트래픽을 차단한 다음, 패브릭 컨트롤러 에이전트에서 규칙과 예외를 추가하여 권한이 부여된 트래픽을 허용하도록 패킷 필터를 구성합니다.

프로그래밍되는 규칙에는 다음 두 가지 범주의 규칙이 있습니다.

-   **머신 구성 또는 인프라 규칙:** 기본적으로 모든 통신이 차단됩니다. 가상 머신이 DHCP 및 DNS 트래픽을 보내고 받을 수 있는 데 예외 사항이 있습니다. 또한 가상 머신에서 "공용" 인터넷에 트래픽을 보내고, 동일한 Azure Virtual Network 및 OS 정품 인증 서버 내에서 다른 가상 머신으로 트래픽을 보낼 수도 있습니다. 가상 머신의 허용된 송신 대상 목록에는 Azure 라우터 서브넷, Azure 관리 및 기타 Microsoft 속성이 포함되어 있지 않습니다.

-   **역할 구성 파일:** 테넌트의 서비스 모델에 따라 인바운드 ACL(액세스 제어 목록)을 정의합니다.

### <a name="vlan-isolation"></a>VLAN 격리
각 클러스터에는 다음과 같이 3개의 VLAN이 있습니다.

![VLAN 격리](./media/azure-isolation/azure-isolation-fig8.jpg)


-   기본 VLAN - 신뢰할 수 없는 고객 노드를 상호 연결합니다.

-   FC VLAN - 신뢰할 수 있는 FC 및 지원 시스템을 포함합니다.

-   디바이스 VLAN - 신뢰할 수 있는 네트워크 및 기타 인프라 디바이스를 포함합니다.

FC VLAN에서 기본 VLAN으로의 통신은 허용되지만, 기본 VLAN에서 FC VLAN으로 초기화할 수는 없습니다. 또한 기본 VLAN에서 디바이스 VLAN으로의 통신은 차단됩니다. 이렇게 하면 고객 코드를 실행하는 노드가 손상되더라도 FC VLAN 또는 디바이스 VLAN의 노드는 공격할 수 없습니다.

## <a name="storage-isolation"></a>저장소 격리
### <a name="logical-isolation-between-compute-and-storage"></a>Compute 및 스토리지 간의 논리적 격리
기본 설계의 일부로 Microsoft Azure는 VM 기반 계산을 저장소로부터 분리합니다. 이러한 분리를 통해 계산 및 저장소를 독립적으로 확장할 수 있으므로 다중 테넌트 및 격리를 더 쉽게 제공할 수 있습니다.

따라서 Azure Storage는 논리적 격리를 제외하고는 Azure Compute에 대한 네트워크 연결이 없는 별도의 하드웨어에서 실행됩니다. [이](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)는 가상 디스크를 만들 때 디스크 공간이 전체 용량으로 할당되지 않음을 의미합니다. 대신 가상 디스크의 주소를 실제 디스크의 영역에 매핑하는 테이블이 만들어지며, 이 테이블은 처음에는 비어 있습니다. **고객이 처음으로 가상 디스크에 데이터를 쓰면 실제 디스크의 공간이 할당되고 해당 공간에 대한 포인터가 테이블에 배치됩니다.**
### <a name="isolation-using-storage-access-control"></a>저장소 액세스 제어를 사용한 격리
**Azure Storage의 Access Control**에는 간단한 액세스 제어 모델이 있습니다. Azure 구독마다 하나 이상의 Storage 계정을 만들 수 있습니다. 각 Storage 계정에는 해당 Storage 계정의 모든 데이터에 대한 액세스를 제어하는 데 사용되는 단일 비밀 키가 있습니다.

![저장소 액세스 제어를 사용한 격리](./media/azure-isolation/azure-isolation-fig9.png)

**Azure Storage 데이터(테이블 포함)** 는 범위가 지정된 액세스 권한을 부여하는 [SAS(공유 액세스 서명)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) 토큰을 통해 제어할 수 있습니다. SAS는 [SAK(Storage 계정 키)](https://msdn.microsoft.com/library/azure/ee460785.aspx)로 서명된 쿼리 템플릿(URL)을 통해 만들어집니다. [서명된 URL](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1)은 다른 프로세스(즉, 위임된 프로세스)에 제공할 수 있으며, 이 경우 쿼리의 세부 정보를 채우고 저장소 서비스를 요청할 수 있습니다. SAS를 사용하면 저장소 계정의 비밀 키를 공개하지 않고 클라이언트에 시간 기반 액세스 권한을 부여할 수 있습니다.

SAS는 지정된 권한 집합을 사용하여 지정된 기간 동안 저장소 계정의 개체로 제한된 권한을 클라이언트에 부여할 수 있다는 것입니다. 계정 선택키를 공유하지 않고도 이러한 제한된 권한을 부여할 수 있습니다.

### <a name="ip-level-storage-isolation"></a>IP 수준 저장소 격리
방화벽을 설정하고 신뢰할 수 있는 클라이언트에 대한 IP 주소 범위를 정의할 수 있습니다. IP 주소 범위를 사용하면 정의된 범위 내의 IP 주소를 가진 클라이언트만 [Azure Storage](https://docs.microsoft.com/azure/storage/storage-security-guide)에 연결할 수 있습니다.

IP 저장소 데이터는 IP 저장소에 전용 트래픽 또는 전용 트래픽 터널을 할당하는 데 사용되는 네트워킹 메커니즘을 통해 권한이 없는 사용자로부터 보호할 수 있습니다.

### <a name="encryption"></a>암호화
Azure는 다음과 같은 유형의 암호화를 제공하여 데이터를 보호합니다.
-   전송 중 암호화

-   휴지 상태의 암호화

#### <a name="encryption-in-transit"></a>전송 중 암호화
전송 중 암호화는 네트워크를 통해 전송되는 경우 데이터 보호의 메커니즘입니다. Azure Storage를 사용하면 다음을 사용하여 데이터를 보호할 수 있습니다.

-   [전송 수준 암호화](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit)(예: Azure Storage 안팎으로 데이터를 전송하는 경우 HTTPS)

-   [실시간 암호화](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares)(예: Azure 파일 공유에 대한 SMB 3.0 암호화)

-   저장소로 데이터가 전송되기 전에 암호화하고 저장소 외부로 전송된 후에 암호를 해독할 수 있도록 하는 [클라이언트 쪽 암호화](https://docs.microsoft.com/azure/storage/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage)

#### <a name="encryption-at-rest"></a>휴지 상태의 암호화
여러 조직에서 [미사용 데이터 암호화](https://docs.microsoft.com/azure/security/azure-isolation) 는 데이터 프라이버시, 규정 준수 및 데이터 주권을 위한 필수 단계입니다. “휴지 상태”의 데이터 암호화를 제공하는 세 가지 Azure 기능이 있습니다.

-   [Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-at-rest) 를 사용하면 Storage 서비스가 Azure Storage에 데이터를 쓸 때 데이터를 자동으로 암호화하도록 요청할 수 있습니다.

-   [Client-side Encryption](https://docs.microsoft.com/azure/storage/storage-security-guide#client-side-encryption) 는 휴지 상태의 암호화 기능을 제공합니다.

-   [Azure 디스크 암호화](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) 를 사용하면 IaaS 가상 머신에서 사용되는 OS 디스크 및 데이터 디스크를 암호화할 수 있습니다.

#### <a name="azure-disk-encryption"></a>Azure 디스크 암호화
VM(가상 머신)에 대해 [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)를 사용하면 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)에서 제어하는 키와 정책으로 VM 디스크(부팅 및 데이터 디스크 포함)를 암호화하여 조직의 보안 및 규정 준수 요구 사항을 처리할 수 있습니다.

Windows용 디스크 암호화 솔루션은 [Microsoft BitLocker 드라이브 암호화](https://technet.microsoft.com/library/cc732774.aspx)를 기반으로 하고, Linux 솔루션은 [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt)를 기반으로 합니다.

이 솔루션은 Microsoft Azure에서 사용되도록 설정될 경우 IaaS VM에 대해 다음 시나리오를 지원합니다.
-   Azure Key Vault와 통합

-   표준 계층 VM: A, D, DS, G, GS 등 계열의 IaaS VM

-   Windows 및 Linux IaaS VM에서 암호화 사용

-   Windows IaaS VM에 대한 OS 및 데이터 드라이브에서 암호화 사용 안 함

-   Linux IaaS VM에 대한 데이터 드라이브에서 암호화 사용 안 함

-   Windows 클라이언트 OS를 실행하는 IaaS VM에서 암호화 사용

-   탑재 경로가 있는 볼륨에서 암호화 사용

-   [mdadm](https://en.wikipedia.org/wiki/Mdadm)을 사용하여 디스크 스트라이프(RAID)로 구성된 Linux VM에서 암호화 사용

-   데이터 디스크에 대해 [LVM(논리 볼륨 관리자)](https://msdn.microsoft.com/library/windows/desktop/bb540532)을 사용하여 Linux VM에서 암호화 사용

-   저장소 공간을 사용하여 구성된 Windows VM에서 암호화 사용

-   모든 Azure 공용 지역 지원됨

이 솔루션은 릴리스에서 다음 시나리오, 기능 및 기술을 지원하지 않습니다.

-   기본 계층 IaaS VM

-   Linux IaaS VM에 대한 OS 드라이브에서 암호화 사용 안 함

-   클래식 VM 만들기 방법을 사용하여 만든 IaaS VM

-   온-프레미스 키 관리 서비스와의 통합

-   Azure 파일(공유 파일 시스템), NFS(네트워크 파일 시스템), 동적 볼륨, 소프트웨어 기반 RAID 시스템으로 구성된 Windows VM

## <a name="sql-azure-database-isolation"></a>SQL Azure Database 격리
SQL Database는 시장을 선도하는 Microsoft SQL Server 엔진을 기반으로 하고 중요한 업무용 워크로드를 처리할 수 있는 Microsoft 클라우드의 관계형 데이터베이스 서비스입니다. SQL Database는 계정 수준, 지리/지역 기반 및 네트워킹 기반의 예측 가능한 데이터 격리를 제공하며, 이러한 격리는 모두 거의 관리할 필요가 없습니다.

### <a name="sql-azure-application-model"></a>SQL Azure 애플리케이션 모델

[Microsoft SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started) 데이터베이스는 SQL Server 기술로 구축된 클라우드 기반 관계형 데이터베이스 서비스입니다. Microsoft에서 호스팅하는 확장성 있는 고가용성 다중 테넌트 데이터베이스 서비스를 클라우드에 제공합니다.

애플리케이션 관점에서 SQL Azure는 다음과 같은 계층 구조를 제공합니다. 각 수준은 아래 수준과 일대다 포함 관계입니다.

![SQL Azure 애플리케이션 모델](./media/azure-isolation/azure-isolation-fig10.png)

계정과 구독은 청구와 관리를 연결하는 Microsoft Azure 플랫폼 개념입니다.

논리 서버와 데이터베이스는 SQL Azure 관련 개념이며, SQL Azure, 제공되는 OData 및 TSQL 인터페이스를 사용하거나 Azure Portal에 통합된 SQL Azure 포털을 통해 관리됩니다.

SQL Azure 서버는 물리적 또는 VM 인스턴스가 아니라 소위 "논리 마스터" 데이터베이스에 저장되는 관리 및 보안 정책을 공유하는 데이터베이스 모음입니다.

![SQL Azure](./media/azure-isolation/azure-isolation-fig11.png)

논리 마스터 데이터베이스에는 다음이 포함됩니다.

-   서버에 연결하는 데 사용되는 SQL 로그인

-   방화벽 규칙

동일한 논리 서버에서 SQL Azure 데이터베이스에 대한 청구 및 사용량 관련 정보가 SQL Azure 클러스터의 동일한 물리적 인스턴스에 있다고 보장하지는 않지만 애플리케이션에서는 연결할 때 대상 데이터베이스 이름을 제공해야 합니다.

고객 관점에서 논리 서버는 지리적 그래픽 지역에 만들지만 서버는 실제로 해당 지역의 클러스터 중 하나에서 만들어집니다.

### <a name="isolation-through-network-topology"></a>네트워크 토폴로지를 통한 격리

논리 서버가 만들어지고 DNS 이름이 등록되면 DNS 이름은 서버를 배치한 특정 데이터 센터의 소위 "게이트웨이 VIP" 주소를 가리킵니다.

VIP(가상 IP 주소) 뒤에는 상태 비저장 게이트웨이 서비스의 모음이 있습니다. 일반적으로 여러 데이터 원본(마스터 데이터베이스, 사용자 데이터베이스 등) 간에 조정이 필요한 경우 게이트웨이가 관련됩니다. 게이트웨이 서비스에서는 다음을 구현합니다.
-   **TDS 연결 프록시 사용** - 여기서는 백 엔드 클러스터에 사용자 데이터베이스를 배치하고, 로그인 시퀀스를 구현한 다음, TDS 패킷을 백 엔드로 전달하고 다시 받습니다.

-   **데이터베이스 관리** - 여기서는 CREATE/ALTER/DROP 데이터베이스 작업을 수행하기 위한 워크플로 모음을 구현합니다. 데이터베이스 작업은 TDS 패킷이나 명시적 OData API를 검색(sniffing)하여 호출할 수 있습니다.

-   CREATE/ALTER/DROP 로그인/사용자 작업

-   OData API를 통한 논리 서버 관리 작업

![네트워크 토폴로지를 통한 격리](./media/azure-isolation/azure-isolation-fig12.png)

게이트웨이 뒤에 있는 계층을 "백 엔드"라고 합니다. 이는 모든 데이터를 고가용성 방식으로 저장하는 곳입니다. 각 데이터 조각은 "파티션" 또는 "장애 조치(failover) 단위"에 속하며, 각 조각에는 3개 이상의 복제본이 있습니다. 복제본은 SQL Server 엔진을 통해 저장되고 복제되며, 종종 "패브릭"이라고 하는 장애 조치 시스템에서 관리됩니다.

일반적으로 백 엔드 시스템은 보안상의 이유로 다른 시스템으로 아웃바운드 통신하지 않습니다. 이는 프런트 엔드(게이트웨이) 계층의 시스템에 예약되어 있습니다. 게이트웨이 계층 컴퓨터는 백 엔드 컴퓨터로 제한된 권한을 가지므로 심층 방어 메커니즘으로 공격 노출 영역을 최소화합니다.

### <a name="isolation-by-machine-function-and-access"></a>시스템 함수 및 액세스 권한으로 격리
SQL Azure는 다른 시스템 함수에서 실행되는 서비스로 구성됩니다. SQL Azure는 "백 엔드" 클라우드 데이터베이스와 "프런트 엔드"(게이트웨이/관리) 환경으로 구분되며, 여기서는 트래픽의 일반적인 원칙이 백 엔드로만 전달되며 백 엔드로부터 받을 수는 없습니다. 프론트 엔드 환경은 다른 서비스의 외부 세계와 통신할 수 있으며, 일반적으로 백 엔드에서 제한된 권한(호출해야 하는 진입점을 호출하는 데 충분한 권한)만 가집니다.

## <a name="networking-isolation"></a>네트워킹 격리
Azure 배포에는 여러 계층의 네트워크 격리가 있습니다. 다음 다이어그램에서는 Azure에서 고객에게 제공하는 다양한 계층의 네트워크 격리를 보여 줍니다. 이러한 계층은 Azure 플랫폼 자체 기능 및 고객 정의 기능에서 모두 고유합니다. Azure DDoS는 인바운드 인터넷을 통해 Azure에 대한 대규모 공격에 대한 격리를 제공합니다. 다음 격리 계층은 클라우드 서비스를 통해 가상 네트워크로 전달할 수 있는 트래픽을 결정하는 데 사용하도록 고객이 정의한 공용 IP 주소(엔드포인트)입니다. 기본 Azure Virtual Network 격리를 통해 모든 타 네트워크를 격리하고 트래픽이 사용자가 구성한 경로 및 방법을 통해서만 전달되도록 할 수 있습니다. 이러한 경로와 방법은 NSG, UDR 및 네트워크 가상 어플라이언스를 사용하여 보호된 네트워크의 애플리케이션 배포를 보호하기 위해 격리 경계를 만드는 데 사용할 수 있는 다음 계층입니다.

![네트워킹 격리](./media/azure-isolation/azure-isolation-fig13.png)

**트래픽 격리:** [가상 네트워크](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)는 Azure 플랫폼의 트래픽 격리 경계입니다. 두 Virtual Network를 같은 고객이 만들었다 하더라도, 한 Virtual Network의 VM(가상 머신)은 다른 가상 네트워크의 VM과 직접 통신할 수 없습니다. 격리는 고객 VM과 통신이 가상 네트워크 안에서 비공개 상태를 유지하는 데 있어 중요한 속성입니다.

[서브넷](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)은 IP 범위를 기반으로 하는 가상 네트워크에서 추가 격리 계층을 제공합니다. 가상 네트워크의 IP 주소를 사용하면 조직과 보안을 위해 가상 네트워크를 여러 서브넷으로 분할할 수 있습니다. VNet 내부의 서브넷(같은 또는 다른)에 배포된 VM 및 PaaS 역할 인스턴스는 추가 구성 없이 서로 통신할 수 있습니다. 또한 NSG의 ACL(액세스 제어 목록)에 구성된 규칙을 기반으로 하여 VM 인스턴스에 대한 네트워크 트래픽을 허용하거나 거부하도록 [NSG(네트워크 보안 그룹)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)를 구성할 수도 있습니다. Nsg는 서브넷 또는 서브넷 내의 개별 VM 인스턴스 중 하나와 연결될 수 있습니다. NSG를 서브넷과 연결한 경우 ACL 규칙은 해당 서브넷에 있는 모든 VM 인스턴스에 적용됩니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft Azure Virtual Network의 컴퓨터에 대한 네트워크 격리 옵션](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)

여기에는 특정 클라이언트 또는 다른 컴퓨터가 IP 주소의 허용 목록을 기반으로 하여 특정 엔드포인트에 연결할 수 있도록 특정 백 엔드 네트워크 또는 하위 네트워크의 컴퓨터에서 허용하는 클래식 프런트 엔드 및 백 엔드 시나리오가 포함됩니다.

- [Compute 격리](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure는 애플리케이션 또는 엔터프라이즈의 요구 사항을 충족하도록 자동으로 확장하거나 축소할 수 있는 다양한 계산 인스턴스와 서비스를 포함하는 클라우드 기반 컴퓨팅 서비스를 제공합니다.

- [저장소 격리](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure는 고객 VM 기반 계산을 저장소와 분리합니다. 이러한 분리를 통해 계산 및 저장소를 독립적으로 확장할 수 있으므로 다중 테넌트 및 격리를 더 쉽게 제공할 수 있습니다. 따라서 Azure Storage는 논리적 격리를 제외하고는 Azure Compute에 대한 네트워크 연결이 없는 별도의 하드웨어에서 실행됩니다. 모든 요청은 고객의 선택에 따라 HTTP 또는 HTTPS를 통해 실행됩니다.

