---
title: Azure로 마이그레이션된 워크로드 보안 유지 및 관리 모범 사례 | Microsoft Docs
description: Azure로 마이그레이션한 워크로드를 운영, 관리 및 보호하기 위한 모범 사례를 확인합니다.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: afcd180146bc349bda9375f10eb56f85f67ccb52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60252995"
---
# <a name="best-practices-for-securing-and-managing-workloads-migrated-to-azure"></a>Azure로 마이그레이션된 워크로드 보안 유지 및 관리 모범 사례

마이그레이션을 계획 및 디자인할 때 마이그레이션 자체 외에도 마이그레이션 후에 사용되는 Azure의 보안 및 관리 모델을 고려해야 합니다. 이 문서에서는 마이그레이션 후에 Azure 배포의 보안을 유지하고 계속해서 최적 수준의 배포를 유지하기 위한 계획 및 모범 사례를 설명합니다. 

> [!IMPORTANT]
> 이 문서에서 설명하는 모범 사례와 의견은 작성 당시의 사용 가능한 Azure 플랫폼과 서비스 기능을 기반으로 합니다. 특징과 기능은 시간이 지남에 따라 변경됩니다.

## <a name="secure-migrated-workloads"></a>마이그레이션된 워크로드 보안 유지

마이그레이션 후 가장 중요한 작업은 내부 및 외부 위협으로부터 마이그레이션된 워크로드를 보호하는 것입니다. 다음 모범 사례는 이러한 작업을 수행하는 데 도움이 됩니다.

- [Azure Security Center 사용](#best-practice-follow-azure-security-center-recommendations): Azure Security Center에서 제공하는 모니터링, 평가 및 권장 사항을 사용하는 방법을 알아봅니다.
- [데이터 암호화](#best-practice-encrypt-data): Azure에서 데이터를 암호화하기 위한 모범 사례를 확인합니다.
- [맬웨어 방지 설정](#best-practice-protect-vms-with-antimalware): 맬웨어 및 악의적인 공격으로부터 VM을 보호합니다.
- [웹앱 보호](#best-practice-secure-web-apps): 마이그레이션된 웹앱에서 중요한 정보를 안전하게 유지합니다.
- [구독 검토](#best-practice-review-subscriptions-and-resource-permissions): 마이그레이션 후 Azure 구독 및 리소스에 액세스할 수 있는 사용자를 확인합니다.
- [로그 사용](#best-practice-review-audit-and-security-logs): Azure 감사 및 보안 로그를 정기적으로 검토합니다.
- [기타 보안 기능 검토](#best-practice-evaluate-other-security-features): Azure에서 제공하는 고급 보안 기능을 이해하고 평가합니다.

## <a name="best-practice-follow-azure-security-center-recommendations"></a>모범 사례: Azure Security Center 권장 사항 수행

Microsoft는 Azure 테넌트 관리자에게 공격으로부터 워크로드를 보호하는 보안 기능을 사용하는 데 필요한 정보를 제공하기 위해 많은 노력을 기울이고 있습니다.  Azure Security Center는 통합 보안 관리를 제공합니다. Security Center에서 워크로드에 보안 정책을 적용하고, 위협에 대한 노출을 제한하고, 공격을 검색 및 대응할 수 있습니다. Security Center는 Azure 테넌트에서 리소스 및 구성을 분석하고 다음을 포함하는 보안 권장 사항을 제공합니다.

- **중앙 집중식 정책 관리** – 모든 하이브리드 클라우드 작업에 걸쳐 보안 정책을 중앙에서 관리하여 회사 또는 규정 보안 요구 사항을 준수할 수 있습니다.
- **연속적인 보안 평가** – 머신, 네트워크, 저장소 및 데이터 서비스, 응용 프로그램의 보안 상태를 모니터링하여 잠재적인 보안 문제를 찾아낼 수 있습니다.
- **실행 가능한 권장 지침** – 우선 순위가 지정된 실행 가능한 보안 권장 지침을 사용하여 공격자들이 악용하기 전에 보안 취약성을 수정할 수 있습니다.
- **경고 및 인시던트 우선 순위 지정** - 우선 순위가 지정된 보안 경고와 인시던트를 통해 가장 중요한 위협부터 중점적으로 확인할 수 있습니다.

평가 및 권장 사항 외에도, Security Center는 특정 리소스에 대해 사용할 수 있는 다양한 기타 보안 기능도 제공합니다.

- **JIT(Just In Time) 액세스**: Azure VM의 관리 포트에 대한 액세스를 JIT(Just In Time) 방식으로 제어하여 네트워크 공격에 줄입니다.
    - 인터넷에서 VM RDP 포트 3389를 열어 두면 VM이 악의적인 행위자 활동에 지속적으로 노출됩니다. Azure IP 주소는 잘 알려져 있으므로 해커는 열린 3389 포트를 공격하기 위해 지속적으로 프로브합니다. 
    - JIT(Just In Time)는 NSG(네트워크 보안 그룹) 및 특정 포트가 열려 있는 기간을 제한하는 수신 규칙을 사용합니다.
    - JIT(Just In Time)를 사용하도록 설정하면 Security Center는 사용자에게 VM에 대한 RBAC(역할 기반 액세스 제어) 쓰기 액세스 권한이 있는지 확인합니다. 또한 사용자가 VM에 연결하는 방법에 대한 규칙도 지정합니다. 권한에 문제가 없는 경우 요청이 승인되고 Security Center가 지정된 시간 동안 선택된 포트로의 인바운드 트래픽을 허용하도록 NSG를 구성합니다. NSG는 해당 시간이 만료되면 이전 상태로 돌아갑니다.
- **적응형 애플리케이션 제어**: 동적 앱 허용 목록을 사용하여 실행되는 앱을 제어함으로써 소프트웨어 및 맬웨어를 VM에서 분리합니다.
    - 적응형 애플리케이션 제어를 사용하면 앱을 허용 목록에 추가할 수 있으며, 악의적인 사용자나 관리자가 사용자 VM에 승인되지 않았거나 심사 대상인 소프트웨어 앱을 설치하지 못하게 할 수 있습니다.
    - 악성 앱 실행하려는 시도를 차단하거나 경고하고, 원치 않거나 악의적인 앱을 피하고, 조직의 앱 보안 정책을 준수하도록 할 수 있습니다.
- **파일 무결성 모니터링**: VM에서 실행되는 파일의 무결성을 확인합니다.
    - VM 문제를 유발하는 소프트웨어를 설치할 필요는 없습니다.  시스템 파일을 변경하면 VM 오류 또는 성능 저하가 발생할 수도 있습니다.  파일 무결성 모니터링은 시스템 파일 및 레지스트리 설정이 변경되었는지 검사하고 업데이트된 내용이 있으면 알립니다.
    - Security Center는 모니터링해야 하는 파일을 권장합니다.


**자세한 정보:**

- Azure Security Center에 대한 [자세한 정보](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Just-In-Time VM 액세스에 대한 [자세한 정보](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
- 적응형 애플리케이션 제어 적용에 대한 [자세한 정보](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)
- 파일 무결성 모니터링 [시작](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)


## <a name="best-practice-encrypt-data"></a>모범 사례: 데이터 암호화 

암호화는 Azure 보안 방침의 중요한 부분입니다. 암호화를 모든 수준에서 사용하도록 설정하여 권한 없는 당사자가 전송 중 데이터 및 미사용 데이터를 포함하는 중요한 데이터에 액세스하지 못하게 할 수 있습니다. 

### <a name="encryption-for-iaas"></a>IaaS용 암호화

- **VM**: VM의 경우 Azure Disk Encryption을 사용하여 Windows 및 Linux IaaS VM 디스크를 암호화할 수 있습니다.
    - 디스크 암호화는 Windows용 BitLocker와 Linux용 DM-Crypt를 활용하여 OS 및 데이터 디스크에 볼륨 암호화를 제공합니다.
    - Azure에서 만든 암호화 키를 사용하거나 Azure Key Vault에서 보호되는 사용자 고유의 암호화 키를 제공할 수 있습니다. 
    - 디스크 암호화를 사용하면 미사용(디스크) 상태일 때 또는 VM 부팅 중에 IaaS VM 데이터가 보호됩니다. 
    - Azure Security Center는 암호화되지 않은 VM이 있는 경우 경고합니다.
- **저장소**: Azure Storage에 저장된 미사용 데이터를 보호합니다.
    - Azure Storage 계정에 저장된 데이터는 FIPS 140-2와 호환되는 Microsoft 생성 AES 키 또는 사용자 고유의 키를 사용하여 암호화할 수 있습니다.
    - 스토리지 서비스 암호화는 모든 신규 및 기존 스토리지 계정에 대해 사용하도록 설정되며 사용하지 않도록 설정할 수 없습니다.


### <a name="encryption-for-paas"></a>PaaS용 암호화

사용자 고유의 VM 및 인프라를 관리하는 IaaS와 달리, PaaS에서는 모델 플랫폼 및 인프라를 공급자가 관리하므로 사용자는 핵심 애플리케이션 논리 및 기능에 좀 더 집중할 수 있습니다. PaaS 서비스의 유형이 상당히 다양하므로 각 서비스는 보안을 위해 개별적으로 평가됩니다. 한 가지 예로 Azure SQL Database에 대한 암호화를 사용하도록 설정하는 방법을 알아보겠습니다.

- **Always Encrypted**: SQL Server Management Studio에서 Always Encrypted 마법사를 사용하여 미사용 데이터를 보호합니다.
    - 개별 열 데이터를 암호화하기 위해 Always Encrypted 키를 만듭니다.
    - Always Encrypted 키는 데이터베이스 메타데이터에 암호화된 상태로 저장되거나 Azure Key Vault와 같은 신뢰할 수 있는 키 저장소에 저장될 수 있습니다.
    - 이 기능을 사용하기 위해 앱을 변경해야 할 수도 있습니다.
- **TDE(투명한 데이터 암호화)**: 데이터베이스, 관련 백업 및 트랜잭션 로그 파일의 실시간 암호화 및 암호 해독을 수행하여 Azure SQL Database를 보호합니다.
    - TDE를 사용하면 앱 계층의 변경 없이도 암호화 작업을 수행할 수 있습니다.
    - TDE에서 Microsoft가 제공하는 암호화 키를 사용할 수도 있고, 사용자가 Bring Your Own Key 지원을 사용하여 고유한 키를 제공할 수도 있습니다.


**자세한 정보:**
- IaaS VM용 Azure Disk Encryption에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview).
- IaaS Windows VM용 암호화를 [사용하도록 설정](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-windows)합니다.
- 미사용 데이터의 Azure 스토리지 서비스 암호화에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
- Always Encrypted 개요를 [읽어봅니다](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Azure SQL Database용 TDE에 대해 [읽어봅니다](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-2017).
- Bring Your Own Key를 사용한 TDE에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql).

## <a name="best-practice-protect-vms-with-antimalware"></a>모범 사례: 맬웨어 방지를 사용하여 VM 보호

특히 이전의 Azure 마이그레이션 VM에는 적절한 수준의 맬웨어 방지 솔루션이 설치되어 있지 않을 수 있습니다.  Azure는 바이러스, 스파이웨어 및 기타 맬웨어에서 VM을 보호하는 데 유용한 무료 엔드포인트 솔루션을 제공합니다.
- Azure용 Microsoft 맬웨어 방지 프로그램은 알려진 악성 또는 원치 않는 소프트웨어가 스스로 설치하려고 할 때 경고를 생성합니다.
- 사용자의 개입 없이 백그라운드에서 실행되는 단일 에이전트 솔루션입니다.
- Azure Security Center에서 엔드포인트 보호가 실행되지 않는 VM을 쉽게 식별하고 필요한 경우 Microsoft 맬웨어 방지 프로그램을 설치할 수 있습니다.

![VM용 맬웨어 방지 프로그램](./media/migrate-best-practices-security-management/antimalware.png)
*VM용 맬웨어 방지 프로그램*

**자세한 정보:**

- Microsoft 맬웨어 방지 프로그램에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/security/azure-security-antimalware).

## <a name="best-practice-secure-web-apps"></a>모범 사례: 웹앱 보호

마이그레이션된 웹앱에는 다음과 같은 몇 가지 문제가 나타납니다.

- 대부분의 레거시 웹 애플리케이션은 구성 파일 내에 중요한 정보를 포함하는 경향이 있습니다.  이러한 정보를 포함하는 파일은 앱을 백업할 때 또는 앱 코드를 소스 제어에서 체크 인 또는 체크 아웃할 때 보안 문제를 나타낼 수 있습니다.
- 또한 VM에 있는 웹앱을 마이그레이션하는 경우 온-프레미스 네트워크 및 방화벽으로 보호되는 환경에서 인터넷 연결 환경으로 해당 머신을 이동할 가능성이 높습니다. 온-프레미스 보호 리소스와 동일한 작업을 수행하는 솔루션을 설정하는지 확인해야 합니다.


Azure는 다음과 같은 몇 가지 솔루션을 제공합니다.

- **Azure Key Vault**: 현재, 웹앱 개발자는 이러한 파일에서 중요한 정보가 유출되지 않도록 하기 위한 단계를 수행하고 있습니다. 정보를 보호하는 한 가지 방법은 파일에서 추출한 후 Azure Key Vault에 저장하는 것입니다.
    - Key Vault를 사용하여 앱 비밀을 중앙에서 보관하고 해당 배포를 제어할 수 있습니다. 이 경우 보안 정보를 앱 파일에 저장할 필요가 없습니다.
    - 앱은 사용자 지정 코드 없이도 URI를 사용하여 자격 증명 모음의 정보 액세스를 보호할 수 있습니다.
    - Azure Key Vault를 사용하면 Azure 보안 제어를 통해 액세스를 잠그고 '롤링 키'를 원활하게 구현할 수 있습니다. Microsoft는 사용자 데이터를 보거나 추출하지 않습니다.
- **App Service Environment**: 마이그레이션한 앱을 추가로 보호해야 할 경우 App Service Environment 및 웹 애플리케이션 방화벽을 추가하여 앱 리소스를 보호하는 것을 고려할 수 있습니다.
    - Azure App Service Environment는 Windows 및 Linux 웹앱, Docker 컨테이너, 모바일 앱 및 함수와 같은 App Service 앱을 실행하는 완전히 격리된 전용 환경을 제공합니다.
    - 이 기능은 규모가 크거나, 격리 및 보안된 네트워크 액세스가 필요하거나, 높은 메모리 사용률을 발생하는 앱에 유용합니다.
- **웹 애플리케이션 방화벽**: 웹앱에 대해 중앙 집중식 보호를 제공하는 Application Gateway의 기능입니다.
    - 백 엔드 코드를 수정하지 않고도 웹앱을 보호합니다.
    - 애플리케이션 게이트웨이 뒤에서 여러 웹앱을 동시에 보호합니다.
    - 웹 애플리케이션 방화벽은 Azure Monitor를 사용하여 모니터링할 수 있으며 Azure Security Center에 통합됩니다.



![웹앱 보호](./media/migrate-best-practices-security-management/web-apps.png)

*Azure Key Vault*

**자세한 정보:**

- Azure Key Vault [개요를 살펴봅니다](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
- 웹 애플리케이션 방화벽에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/application-gateway/waf-overview).
- App Service Environment를 [소개합니다](https://docs.microsoft.com/azure/app-service/environment/intro).
- Key Vault에서 비밀을 읽도록 웹앱을 구성하는 방법을 [자세히 알아봅니다](https://docs.microsoft.com/azure/key-vault/tutorial-web-application-keyvault).
- 웹 애플리케이션 방화벽에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/application-gateway/waf-overview).

## <a name="best-practice-review-subscriptions-and-resource-permissions"></a>모범 사례: 구독 및 리소스 사용 권한 검토

워크로드를 마이그레이션하고 Azure에서 실행할 경우 워크로드 액세스 권한이 있는 직원이 이동하게 됩니다. 보안 팀은 Azure 테넌트 및 리소스 그룹에 대한 액세스를 정기적으로 검토해야 합니다. Azure는 RBAC(역할 기반 액세스 제어)를 포함하는 다양한 ID 관리 및 액세스 제어 보안 기능을 제공하여 Azure 리소스에 액세스하기 위한 권한을 부여합니다.

- RBAC는 보안 주체를 위해 액세스 권한을 할당합니다. 보안 주체는 사용자, 그룹(사용자 세트), 서비스 주체(앱 및 서비스에서 사용하는 ID) 및 관리 ID(Azure에서 자동으로 관리하는 Azure Active Directory ID)를 나타냅니다.
- RBAC는 소유자, 참가자 및 읽기 권한자와 같은 보안 주체 및 역할이 수행할 수 있는 작업을 정의하는 역할 정의(권한 컬렉션)에 역할을 할당할 수 있습니다.
- 또한 RBAC는 역할의 경계를 설정하는 범위를 설정할 수도 있습니다. 범위는 관리 그룹, 구독, 리소스 그룹 또는 리소스를 비롯한 다양한 수준에서 설정할 수 있습니다.
- Azure 액세스 권한이 있는 관리자가 사용자가 허용하려는 리소스에만 액세스할 수 있는지 확인합니다.  Azure의 미리 정의된 역할이 충분히 세부적이지 않으면 사용자 지정 역할을 만들어 액세스 권한을 구분하고 제한할 수 있습니다.

![액세스 제어](./media/migrate-best-practices-security-management/subscription.png)

*액세스 제어 - IAM*

**자세한 정보:**

- RBAC에 대한 [정보](https://docs.microsoft.com/azure/role-based-access-control/overview)
- RBAC 및 Azure Portal을 사용하는 액세스 관리 방법을 [알아봅니다](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
- 사용자 지정 역할에 대해 [알아봅니다](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

## <a name="best-practice-review-audit-and-security-logs"></a>모범 사례: 감사 및 보안 로그 검토

Azure AD(Active Directory)는 Azure Monitor에서표시되는 활동 로그를 제공합니다. 이러한 로그는 Azure 테넌시에서 수행된 작업, 발생 시기, 수행한 사람을 캡처합니다. 

- 감사 로그에는 테넌트의 작업 기록이 표시됩니다. 로그인 활동 로그는 작업을 수행한 사람을 표시합니다. 
- 보안 보고서 액세스 권한은 Azure AD 라이선스에 따라 달라집니다. 무료 및 기본 권한의 경우 위험한 사용자 및 로그인 목록을 볼 수 있습니다. 프리미엄 1 및 프리미엄 2 버전에서는 기본 이벤트 정보를 볼 수 있습니다.
- 장기 보존 및 데이터 인사이트를 위해 활동 로그를 다양한 엔드포인트로 라우팅할 수 있습니다.
- 로그를 검토하거나 SIEM(보안 정보 및 이벤트 관리) 도구를 통합하여 정기적으로 비정상적인 상태를 자동으로 검토합니다.  프리미엄 1 또는 2를 사용하지 않는 경우 직접 또는 SIEM 시스템을 사용하여 많은 분석 작업을 수행해야 합니다.  분석 중에는 위험한 로그인 및 이벤트와 기타 사용자 공격 패턴이 검색됩니다.


![사용자 및 그룹](./media/migrate-best-practices-security-management/azure-ad.png)

*Azure AD 사용자 및 그룹*

**자세한 정보:**

- Azure Monitor의 Azure AD 활동 로그에 대해 [알아봅니다](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor).
- Azure AD Portal에서 활동 보고서를 감사하는 방법을 [알아봅니다](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs).

## <a name="best-practice-evaluate-other-security-features"></a>모범 사례: 기타 보안 기능 평가

Azure는 고급 보안 옵션을 제공하는 다양한 다른 보안 기능을 제공합니다. 이러한 모범 사례의 일부에는 추가 기능 라이선스 및 프리미엄 옵션이 필요합니다.

- **Azure AD AU(관리 단위) 구현**: 기본적인 Azure 액세스 제어 권한만으로 지원 담당자에 관리 임무를 위임하는 것은 매우 까다로운 일일 있습니다.  지원 담당자에게 Azure AD의 모든 그룹을 관리하기 위한 액세스 권한을 부여하는 방식은 조직의 보안 차원에서 적절하지 않을 수 있습니다.  AU를 사용하면 온-프레미스 OU(조직 구성 단위)와 비슷한 방식으로 Azure 리소스를 컨테이너로 구분할 수 있습니다.  AU를 사용하려면 AU 관리자에게 Azure AD 프리미엄 라이선스가 있어야 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).
- **MFA(Multi-Factor Authentication) 사용**: 프리미엄 Azure AD 라이선스가 있는 경우 관리자 계정에서 MFA를 사용하도록 설정하고 적용할 수 있습니다. 피싱은 계정 자격 증명이 손상되는 가장 흔한 경우입니다.  악의적인 행위자에게 관리자 계정 자격 증명이 있으면 모든 리소스 그룹을 삭제하는 것과 같은 광범위한 행위를 멈추기 어렵습니다. 이메일, Authenticator 앱 및 휴대폰 문자 메시지 등의 다양한 방법으로 MFA을 설정할 수 있습니다. 관리자는 파급력이 가장 낮은 옵션을 선택할 수 있습니다. MFA는 위협 분석 및 조건부 액세스 정책과 통합되어 무작위로 MFA 시도-응답을 요구합니다. [보안 지침](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices) 및 MFA [설정 방법](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices)에 대해 자세히 알아보세요.
- **조건부 액세스 구현**: 대부분의 중소규모 조직에서 Azure 관리자 및 지원 팀은 단일 지리적 위치에 있을 것입니다. 이 경우 대부분의 로그인은 동일한 영역에서 발생합니다. 이러한 위치의 IP 주소가 상당히 고정적인 경우 이러한 영역 외부에서 관리자 로그인이 발생하는 일이 드물 것입니다. 원격의 악의적 행위자가 관리자의 자격 증명을 손상하는 경우에도 원격 위치 또는 임의 IP 주소를 사용하는 스푸핑된 위치에서의 로그인을 방지하기 위해 MFA와 결합된 조건부 액세스와 같은 보안 기능을 구현할 수 있습니다. 조건부 액세스에 대해 [자세히 알아보고](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) Azure AD의 조건부 액세스를 위한 [모범 사례를 검토](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)하세요.
- **엔터프라이즈 애플리케이션 사용 권한 검토**: 시간이 지나면서 관리자는 조직에 미치는 영향을 알지 못한 상태로 Microsoft 및 타사 링크를 클릭하게 됩니다. 링크는 Azure 앱에 권한을 할당하고, Azure AD 데이터를 읽을 수 있는 액세스 권한 또는 전체 Azure 구독을 관리하기 위한 모든 권한을 허용할 수도 있는 동의 화면을 표시할 수 있습니다. 관리자와 사용자가 Azure 리소스에 대한 액세스를 허용한 앱을 정기적으로 검토해야 합니다. 이러한 앱에 필요한 사용 권한만 있는지 확인해야 합니다. 또한 분기별이나 연 2회 정도 앱 페이지에 대한 링크를 이메일로 사용자에게 전송하여 조직 데이터에 대한 액세스를 허용한 앱을 파악하도록 할 수 있습니다. 애플리케이션 유형에 대해 [자세히 알아보고](https://docs.microsoft.com/azure/active-directory/manage-apps/application-types) Azure AD에서 앱 할당을 [제어하는 방법](https://docs.microsoft.com/azure/active-directory/manage-apps/remove-user-or-group-access-portal)을 알아봅니다.



## <a name="managed-migrated-workloads"></a>관리되는 마이그레이션된 워크로드

이 섹션에서는 다음을 포함하여 Azure 관리를 위한 몇 가지 모범 사례를 권장합니다.

- [리소스 관리](#best-practice-name-resource-groups): 스마트 이름 지정, 우연한 삭제 방지, 리소스 사용 권한 관리 및 효과적인 리소스 태그 지정을 포함하는 Azure 리소스 그룹 및 리소스에 대한 모범 사례입니다.
- [청사진 사용](#best-practice-implement-blueprints): 배포 환경의 구축 및 관리에 청사진을 사용하는 방법에 대한 빠른 개요를 확인합니다.
- [아키텍처 검토](#best-practice-review-azure-reference-architectures): 마이그레이션 후 배포를 빌드할 때 유용한 정보를 얻을 수 있는 샘플 Azure 아키텍처를 검토합니다.
- [관리 그룹 설정](#best-practice-manage-resources-with-management-groups): 여러 구독이 있는 경우 구독을 관리 그룹으로 모은 후 해당 그룹에 거버넌스 설정을 적용할 수 있습니다.
- [액세스 정책 설정](#best-practice-deploy-azure-policy): Azure 리소스에 규정 준수 정책을 적용합니다.
- [BCDR 전략 구현](#best-practice-implement-a-bcdr-strategy): BCDR(비즈니스 연속성 및 재해 복구) 전략을 통합하여 중단이 발생할 경우 데이터를 안전하게 유지하고, 환경의 복원력을 유지하고, 리소스를 계속 작동합니다.
- [VM 관리](#best-practice-use-managed-disks-and-availability-sets): 복원력 및 고가용성을 위해 VM을 가용성 그룹으로 묶습니다. 손쉬운 VM 디스크 및 스토리지 관리를 위해 Managed Disks를 사용합니다.
- [리소스 사용 모니터링](#best-practice-monitor-resource-usage-and-performance): Azure 리소스에 대한 진단 로깅을 사용하도록 설정하고, 사전 문제 해결을 위해 경고 및 플레이북을 구축하고, 배포 상태의 통합 보기를 위해 Azure 대시보드를 사용합니다.
- [지원 및 업데이트 관리](#best-practice-manage-updates): Azure 지원 플랜과 구현 방법을 이해하고, VM을 최신 상태로 유지하기 위한 모범 사례를 확인하고, 변경 관리를 위한 프로세스를 진행합니다.


## <a name="best-practice-name-resource-groups"></a>모범 사례: 리소스 그룹 이름 지정

리소스 그룹에 관리자와 지원 팀이 쉽게 인식하고 탐색할 수 있는 의미 있는 이름을 부여하면 생산성과 효율성을 크게 향상됩니다.
- Azure 명명 규칙을 따르는 것이 좋습니다.
- AD Connect를 사용하여 온-프레미스 AD DS를 Azure AD와 동기화하는 경우 온-프레미스의 보안 그룹 이름과 Azure의 보안 그룹 이름이 일치하도록 하는 것이 좋습니다.

![이름 지정](./media/migrate-best-practices-security-management/naming.png)

*리소스 그룹 이름 지정*


**자세한 정보:**

- 명명 규칙에 대한 [자세한 정보](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)

## <a name="best-practice-implement-delete-locks-for-resource-groups"></a>모범 사례: 리소스 그룹에 대한 삭제 잠금 구현

마지막으로 필요한 작업은 리소스 그룹을 실수로 삭제하여 사라지는 일이 없도록 하는 것입니다. 이런 상황이 발생하지 않도록 삭제 잠금을 구현하는 것이 좋습니다.


![삭제 잠금](./media/migrate-best-practices-security-management/locks.png)

*삭제 잠금*

**자세한 정보:**

- 예기치 않은 변경을 방지하기 위한 리소스 잠그기에 대해 [알아봅니다](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).


## <a name="best-practice-understand-resource-access-permissions"></a>모범 사례: 리소스 액세스 권한 이해 

구독 소유자는 구독의 모든 리소스 그룹 및 리소스에 액세스할 수 있습니다.
- 이 할당 권한은 매우 중요하므로 꼭 필요한 경우에만 사용자를 추가합니다. 이러한 유형의 사용 권한이 미치는 영향을 이해하는 것은 환경을 안전하고 안정적으로 유지하는 데 상당히 중요합니다.
- 리소스를 적절한 리소스 그룹에 배치해야 합니다.
    - 리소스의 수명 주기를 비슷하게 일치시킵니다. 전체 리소스 그룹을 삭제해야 하는 경우에는 리소스를 이동할 필요가 없어야 합니다.
    - 관리 간소화를 위해 함수 또는 워크로드를 지원하는 리소스는 함께 배치해야 합니다.

**자세한 정보:**

- 구독 및 리소스 그룹 구성에 대해 [자세히 알아봅니다](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/).

## <a name="best-practice-tag-resources-effectively"></a>모범 사례: 리소스에 효과적으로 태그 지정

종종 리소스와 관련된 리소스 그룹 이름만 사용해서는 내부 요금 청구 또는 구독 내의 관리와 같은 메커니즘을 효과적으로 구현하기 위한 충분한 메타데이터를 얻을 수 없습니다.
- 모범 사례로, Azure 태그를 사용하여 쿼리 및 보고할 수 있는 유용한 메타데이터를 추가할 수 있습니다. 
- 태그는 사용자가 정의하는 속성을 사용하여 리소스를 논리적으로 구성하는 방법을 제공합니다.  태그는 리소스 그룹 또는 리소스에 직접 적용할 수 있습니다.
- 태그는 리소스 그룹 또는 개별 리소스에 적용할 수 있습니다. 리소스 그룹 태그는 그룹의 리소스에서 상속되지 않습니다.
- Powershell 또는 Azure Automation를 사용하여 태그 지정을 자동화하거나 개별 그룹 및 리소스에 태그를 지정할 수 있습니다. -태그 지정 방법 또는 셀프 서비스 태그  요청 및 변경 관리 시스템을 설정되면 요청의 정보를 쉽게 활용하여 회사별 리소스 태그를 채울 수 있습니다.


![태그 지정](./media/migrate-best-practices-security-management/tagging.png)
*태그 지정*

**자세한 정보:**

- 태그 지정 및 태그 제한 사항에 대해 [알아봅니다](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags).
- 태그 지정을 설정하고 리소스 그룹에서 해당 리소스에 태그를 적용하는 PowerShell 및 CLI 예제를 [검토합니다](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#powershell).
- Azure 태그 지정 모범 사례에 대해 [읽어봅니다](https://www.azurefieldnotes.com/2016/07/18/azure-resource-tagging-best-practices/).


## <a name="best-practice-implement-blueprints"></a>모범 사례: 청사진 구현

엔지니어나 설계자가 청사진을 통해 프로젝트의 설계 매개 변수를 스케치하는 것과 마찬가지로, 클라우드 설계자와 중앙 IT 그룹은 Azure Blueprints를 통해 조직의 표준, 패턴 및 요구 사항을 구현하고 준수하는 반복 가능한 Azure 리소스 집합을 정의할 수 있습니다. 개발 팀은 Azure Blueprints를 사용하여 조직의 규정 준수 요구 사항을 충족하고 네트워킹 등의 기본 제공 구성 요소 세트를 포함하는 새 환경을 빠르게 빌드하고 만들어 개발 및 제공 시간을 단축할 수 있습니다.

- 청사진을 사용하여 리소스 그룹, Azure Resource Manager 템플릿, 정책 및 역할 할당의 배포를 오케스트레이션합니다.
- Azure Blueprints는 전역적으로 분산된 Azure Cosmos DB에 저장됩니다. 청사진 개체는 여러 Azure 지역에 복제됩니다. 복제를 통해 Blueprints가 리소스를 배포하는 지역에 관계없이, 대기 시간이 짧아지고, 가용성이 향상되고, 청사진에 일관되게 액세스할 수 있습니다.

**자세한 정보:**

- 청사진에 대해 [읽어봅니다](https://docs.microsoft.com/azure/governance/blueprints/overview).
- 의료 분야의 AI를 가속화하는 데 사용하는 청사진 예제를 [검토합니다](https://azure.microsoft.com/blog/customizing-azure-blueprints-to-accelerate-ai-in-healthcare/).

## <a name="best-practice-review-azure-reference-architectures"></a>모범 사례: Azure 참조 아키텍처 검토

Azure에서 안전하고 확장 가능하고 관리가 용이한 워크로드를 구축하는 일은 어려울 수 있습니다.  지속적으로 변화하기 때문에 최적의 환경을 위해 다양한 기능을 사용하는 것은 어려울 수 있습니다. 학습할 참조가 있으면 워크로드를 디자인하고 마이그레이션하는 데 유용할 수 있습니다.  Azure 및 Azure 파트너는 다양한 유형의 환경을 위한 몇 가지 샘플 참조 아키텍처를 구축하고 있습니다. 이러한 샘플은 배우고 참조할 수 있는 아이디어를 제공하도록 디자인되었습니다. 

참조 아키텍처는 시나리오별로 정렬되어 있습니다. 여기에는 권장 방법, 관리, 가용성, 확장성 및 보안에 대한 권장 사항이 포함되어 있습니다.
Azure App Service Environment는 Windows 및 Linux 웹앱, Docker 컨테이너, 모바일 앱 및 함수를 포함하는 App Service 앱을 실행하는 완전히 격리된 전용 환경을 제공합니다. App Service는 보안, 부하 분산, 자동 크기 조정 및 자동화된 관리를 포함하는 Azure의 강력한 기능을 애플리케이션에 추가합니다. 또한 Azure DevOps 및 GitHub, 패키지 관리, 스테이징 환경, 사용자 지정 도메인 및 SSL 인증서의 지속적인 배포와 같은 DevOps 기능도 활용할 수 있습니다. App Service는 격리 및 보안된 네트워크 액세스가 필요한 앱과 많은 양의 메모리와 크기 조정이 필요한 기타 리소스를 사용하는 앱에 유용합니다.

**자세한 정보:**

- Azure 참조 아키텍처에 대해 [알아봅니다](https://docs.microsoft.com/azure/architecture/reference-architectures/).
- Azure 예제 시나리오를 [검토합니다](https://docs.microsoft.com/azure/architecture/example-scenario/).

## <a name="best-practice-manage-resources-with-management-groups"></a>모범 사례: 관리 그룹으로 리소스 관리

조직에 여러 구독이 있는 경우 액세스, 정책 및 규정 준수에 관리해야 합니다. Azure 관리 그룹은 구독 상위 수준의 범위를 제공합니다.

- 관리 그룹이라는 컨테이너에 구독을 구성하고 거버넌스 조건을 적용합니다.
- 관리 그룹의 모든 구독은 관리 그룹 조건을 자동으로 상속합니다.
- 관리 그룹은 어떤 유형의 구독을 보유하든 관계없이 대규모의 엔터프라이즈급 관리를 제공합니다.
- 예를 들어 VM을 만들 수 있는 지역을 제한하는 관리 그룹 정책을 적용할 수 있습니다. 이 정책은 모든 관리 그룹, 구독 및 해당 관리 그룹의 모든 리소스에 적용됩니다.
- 리소스를 통합 정책 및 액세스 관리를 위한 계층 구조로 구성하는 유연한 관리 그룹 및 구독 구조를 만들 수 있습니다.

다음 다이어그램은 관리 그룹을 사용하여 거버넌스용 계층을 만드는 예를 보여줍니다.

![관리 그룹](./media/migrate-best-practices-security-management/management-groups.png)
*관리 그룹*

**자세한 정보:**
- 리소스를 관리 그룹으로 구성하는 방법에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/governance/management-groups/index).

## <a name="best-practice-deploy-azure-policy"></a>모범 사례: Azure Policy 배포

Azure Policy는 정책을 만들고, 할당하고, 관리하는 데 사용하는 Azure의 서비스입니다.

- 정책은 리소스에 대해 다양한 규칙과 효과를 적용하여 리소스를 회사 표준 및 서비스 수준 계약을 준수하는 상태로 유지합니다.
- Azure Policy는 리소스를 평가하면서 정책을 준수하지 않는 리소스를 검색합니다.
- 예를 들어, 사용자 환경에서 특정 SKU 크기의 VM만 허용하는 정책을 만들 수 있습니다. Azure Policy는 리소스를 만들고 업데이트할 때와 기존 리소스를 검색할 때 이러한 설정을 평가합니다. 
- Azure에서 할당할 수 있는 일부 기본 제공 정책이 있으며, 사용자가 고유한 정책을 만들 수도 있습니다.


![Azure Policy](./media/migrate-best-practices-security-management/policy.png)
*Azure Policy*

**자세한 정보:**
- Azure Policy의 [개요를 확인합니다](https://docs.microsoft.com/azure/governance/policy/overview).
- 규정 준수를 적용하는 정책을 만들고 관리하는 방법을 [알아봅니다](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage).


## <a name="best-practice-implement-a-bcdr-strategy"></a>모범 사례: BCDR 전략 구현

BCDR(비즈니스 연속성 및 재해 복구) 계획은 Azure로 마이그레이션을 계획하는 경우에 완료해야 하는 중요한 작업입니다. 법률 조항에 따라, 계약서에는 허리케인이나 지진 같은 강력한 물리력으로 인해 의무를 이행하지 못하는 불가항력 절이 포함됩니다. 그러나 재해가 발생할 경우 서비스를 계속 실행하고 필요한 경우 복구하도록 할 의무도 있습니다. 이러한 능력은 회사의 미래를 만들 수도 있고 망칠 수도 있습니다.

광범위하게, BCDR 전략은 다음을 고려해야 합니다.
- **데이터 백업**: 중단이 발생할 경우 쉽게 복구할 수 있도록 데이터를 안전하게 유지하는 방법입니다.
- **재해 복구**: 중단이 발생할 경우 앱을 복원하고 사용 가능하게 유지하는 방법입니다. 

### <a name="azure-resiliency-features"></a>Azure 복원력 기능
Azure 플랫폼은 다양한 복원력 기능을 제공합니다.

- **지역 페어링**: Azure는 지역을 페어링하여 데이터 상주 경계 내에서 지역 보호를 제공합니다. Azure는 지역 쌍 간에 물리적 격리를 보장하고, 광범위한 중단이 발생한 경우 쌍 중에서 한 지역의 복구를 우선적으로 진행하고, 각 지역에서 별도로 시스템 업데이트를 배포하고, Azure 지역 중복 스토리지와 같은 기능을 통해 지역 쌍 간에 복제를 진행할 수 있도록 합니다.
- **가용성 영역**: 가용성 영역은 Azure 지역과는 별도로 물리적 영역을 설정하여 전체 Azure 데이터 센터 오류로부터 보호 합니다. 각 영역에는 고유한 전원, 네트워크 인프라 및 냉각 메커니즘이 유지됩니다.
- **가용성 세트**: 가용성 세트는 데이터 센터 내의 오류로부터 보호합니다. VM을 가용성 세트로 그룹화하여 고가용성을 유지합니다. 각 가용성 세트 내에서 Azure는 공통되는 전원 및 네트워크 스위치를 갖는 기본 하드웨어와 함께 그룹화되는 여러 장애 도메인과 동시에 유지 관리를 겪거나 다시 부팅될 수 있는 기본 하드웨어와 함께 그룹화되는 업데이트 도메인을 구현합니다. 예를 들어, 워크로드가 Azure VM 간에 분산되면 각 앱 계층에 대한 둘 이상의 VM을 하나의 세트에 넣을 수 있습니다. 예를 들어, 프런트 엔드 VM을 하나의 세트에 배치하고, 데이터 계층 VM을 다른 세트에 배치할 수 있습니다. 한 번에 한 세트에서 하나의 업데이트 도메인만 다시 부팅되고 Azure는 세트의 VM이 장애 도메인 간에 분산되도록 하므로 세트의 모든 VM에서 동시에 장애가 발생하는 일이 방지됩니다.

### <a name="set-up-bcdr"></a>BCDR 설정

Azure로 마이그레이션할 때 Azure 플랫폼에서 이러한 기본 제공 복원력 기능을 제공하지만 고가용성, 재해 복구 및 백업을 제공하는 Azure 기능 및 서비스를 활용하도록 Azure 배포를 디자인해야 한다는 사실을 이해하는 것이 중요합니다.

- BCDR 솔루션은 회사 목적에 따라 달라지며 Azure 배포 전략의 영향을 받습니다. IaaS(서비스 제공 인프라) 및 PaaS(Platform as a Service) 배포는 BCDR에 대해 다른 해결 과제를 발생합니다.
- 일단 설치되면 BCDR 솔루션을 정기적으로 테스트하여 사용자 전략이 실행 가능한지 확인해야 합니다.


## <a name="best-practice-back-up-your-data"></a>모범 사례: 데이터 백업

대부분의 경우 온-프레미스 워크로드는 마이그레이션 후에 더 이상 사용되지 않으며, 데이터 백업에 대한 온-프레미스 전략을 연장하거나 대체해야 합니다. 전체 데이터 센터를 Azure로 마이그레이션하는 경우 Azure 기술 또는 타사 통합 솔루션을 사용하여 전체 백업 솔루션을 디자인하고 구현해야 합니다. 


### <a name="back-up-an-iaas-deployment"></a>IaaS 배포 백업

Azure IaaS VM에서 실행되는 워크로드의 경우 다음과 같은 백업 솔루션을 고려합니다.

- **Azure Backup**: Azure Windows 및 Linux VM에 대해 애플리케이션 일치 백업을 제공합니다.
- **스토리지 스냅숏**: Blob 스토리지의 스냅숏을 만듭니다.

#### <a name="azure-backup"></a>Azure Backup

Azure Backup은 Azure Storage에 저장되는 데이터 복구 지점을 만듭니다. Azure Backup은 Azure VM 디스크 및 Azure Files(미리 보기)를 백업할 수 있습니다. Azure Files는 클라우드에 SMB를 통해 액세스할 수 있는 파일 공유를 제공합니다.
   
Azure Backup을 사용하여 여러 가지 방법으로 VM을 백업할 수 있습니다.

- **VM 설정에서 직접 백업**: Azure Portal의 VM 옵션에서 직접 Azure Backup을 사용하여 VM을 백업할 수 있습니다. 하루에 1번 VM을 백업하고 필요에 따라 VM 디스크를 복원할 수 있습니다. Azure Backup은 앱 인식 데이터 스냅숏(VSS)을 만들며 VM에 에이전트가 설치되지 않습니다.
- **Recovery Services 자격 증명 모음에 직접 백업**: Azure Backup Recovery Services 자격 증명 모음을 배포하여 IaaS VM을 백업할 수 있습니다. 이를 통해 백업을 추적 및 관리하기 위한 단일 위치와 세분화된 백업 및 복원 옵션이 제공됩니다. 백업은 파일/폴더 수준에서 하루에 최대 3회 진행됩니다. 이 백업은 앱을 인식하지 않고 Linux는 지원되지 않습니다. 백업하려는 각 VM에서 MARS(Microsoft Azure Recovery Services) 에이전트를 설치해야 합니다.
- **Azure Backup Server: Azure Backup Server로 VM 보호**: Azure Backup Server는 Azure Backup과 함께 무료로 제공됩니다. VM은 로컬 Azure Backup Server 스토리지로 백업됩니다. 그러면 Azure Backup Server를 Azure의 자격 증명 모음에 백업합니다. 이 백업은 앱을 인식하며 백업 빈도와 보존 방식도 완전히 세분화됩니다. 앱 수준에서 백업할 수 있습니다. 예를 들어 SQL Server 또는 SharePoint를 백업할 수 있습니다.

보안을 위해 Azure Backup은 AES 256을 사용하여 진행 중인 데이터를 암호화한 후 HTTPS를 통해 Azure로 전송합니다. Azure에서 백업한 미사용 데이터는 전송 및 보관을 위해 [SSE(스토리지 서비스 암호화)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)를 사용하여 암호화됩니다.


![Azure Backup](./media/migrate-best-practices-security-management/iaas-backup.png)
*Azure Backup*

**자세한 정보:**

- 여러 다른 유형의 백업에 대해 [알아봅니다](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup).
- Azure VM용 [백업 인프라를 계획합니다](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction).

#### <a name="storage-snapshots"></a>스토리지 스냅숏

Azure VM은 Azure Storage에 페이지 Blob으로 저장됩니다. 

- 스냅숏은 특정 시점의 Blob 상태를 캡처합니다.
- Azure VM 디스크에 대한 대체 백업 방법으로, Storage Blob의 스냅숏을 만든 후 다른 스토리지 계정으로 복사할 수 있습니다. 
- 전체 Blob을 복사하거나 증분 스냅숏 복사를 사용하여 델타 변경만 복사함으로써 스토리지 공간을 줄일 수 있습니다.
- 부가적인 조치로, Blob 스토리지 계정에 대해 일시 삭제를 사용하도록 설정할 수 있습니다. 이 기능을 사용하도록 설정하면 삭제한 Blob이 삭제용으로 표시되지만 즉시 제거되지는 않습니다. 이러한 중간 기간 동안 Blob을 복원할 수 있습니다.

**자세한 정보:**

- Azure Blob Storage에 대해 [알아봅니다](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).
- Blob 스냅숏을 만드는 방법을 [알아봅니다](https://docs.microsoft.com/azure/storage/blobs/storage-blob-snapshots).
- Blob 스토리지 백업에 대한 [샘플 시나리오를 검토합니다](https://azure.microsoft.com/blog/microsoft-azure-block-blob-storage-backup).
- 일시 삭제에 대해 [읽어봅니다](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).
- [Azure Storage에서 재해 복구 및 강제 장애 조치(Failover) (미리 보기)](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

#### <a name="third-party-backup"></a>타사 백업

또한 타사 솔루션을 사용하여 Azure VM 및 스토리지 컨테이너를 로컬 스토리지 또는 기타 클라우드 공급자로 백업할 수 있습니다. Azure Marketplace의 백업 솔루션에 대해 [자세히 알아봅니다](https://azuremarketplace.microsoft.com/marketplace/apps?search=backup&page=1). 


### <a name="back-up-a-paas-deployment"></a>PaaS 배포 백업


사용자 고유의 VM 및 인프라를 관리하는 IaaS와 달리, PaaS에서는 모델 플랫폼 및 인프라를 공급자가 관리하므로 사용자는 핵심 애플리케이션 논리 및 기능에 좀 더 집중할 수 있습니다. PaaS 서비스의 유형이 상당히 다양하므로 각 서비스는 백업을 위해 개별적으로 평가됩니다. 두 가지 일반적인 Azure PaaS 서비스인 Azure SQL Database와 Azure Functions에 대해 살펴보겠습니다.

#### <a name="back-up-azure-sql-database"></a>Azure SQL Database 백업

Azure SQL Database는 완전 관리형 PaaS 데이터베이스 엔진입니다. 백업 자동화를 비롯한 다양한 비즈니스 연속성 기능을 제공합니다.

- SQL Database는 자동으로 매주 전체 데이터베이스 백업을 수행하고, 12시간 간격으로 차등 백업을 수행합니다. 트랜잭션 로그 백업은 데이터 손실로부터 데이터베이스를 보호하기 위해 5~10분 간격으로 수행됩니다.
- 백업은 투명하며 추가 비용을 발생하지 않습니다.
- 백업은 지역 중복성을 위해 RA-GRS 스토리지에 저장되고 페어링된 지리적 지역으로 복제됩니다.
- 백업 보존은 구매 모델에 따라 달라집니다. DTU 기반 서비스 계층은 기본 계층의 경우 7일, 기타 계층의 경우 35일까지 다양합니다.
- 보존 기간 내 지정 시간으로 데이터베이스를 복원할 수 있습니다. 또한 삭제된 데이터베이스를 복원하거나, 서로 다른 지리적 지역으로 복원하거나, 데이터베이스에 LTR(장기 보존 정책)이 적용될 경우 장기 백업에서 복원할 수도 있습니다.


![Azure SQL 백업](./media/migrate-best-practices-security-management/sql-backup.png)
*Azure SQL 백업*

**자세한 정보:**
- SQL Database에 대한 [자동화된 백업](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)
- 자동화된 백업을 사용하여 [데이터베이스 복구](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups)

 
#### <a name="back-up-azure-functions"></a>Azure Functions 백업

Azure Functions는 거의 코드로 작동하므로 GitHub 또는 Azure DevOps 서비스의 소스 제어와 같이 코드를 보호하는 데 사용하는 것과 동일한 방법을 사용하여 백업하는 것이 좋습니다.

**자세한 정보:**

- Azure DevOps에 대한 [데이터 보호](/azure/devops/organizations/security/data-protection)

## <a name="best-practice-set-up-disaster-recovery"></a>모범 사례: 재해 복구 설정 

BCDR 계획은 데이터를 보호하는 것 외에, 재해가 발생하는 경우 앱과 워크로드를 사용 가능하게 유지하는 방법을 고려해야 합니다. 

### <a name="set-up-disaster-recovery-for-iaas-apps"></a>IaaS 앱에 대한 재해 복구 설정

Azure IaaS VM 및 Azure Storage에서 실행되는 워크로드의 경우 다음과 같은 솔루션을 고려합니다.

- **Azure Site Recovery**: 주 지역의 Azure VM에서 보조 지역으로의 복제를 오케스트레이션합니다. 중단이 발생할 경우 주 지역에서 보조 지역으로 장애 조치(failover)한 후 앱에 계속 액세스할 수 있습니다. 상황이 정상으로 돌아오면 주 지역으로 장애 복구(Failback)합니다.
- **Azure Storage**: Azure는 다양한 유형의 스토리지에 대해 기본 제공 복원력 및 고가용성을 제공합니다.

#### <a name="azure-site-recovery"></a>Azure Site Recovery 

Azure Site Recovery는 중단이 발생할 때 Azure VM이 온라인 상태가 되도록 하고 VM 앱을 사용 가능하게 만들기 위한 기본 Azure 서비스입니다. Site Recovery는 주 Azure 지역에서 보조 Azure 지역으로 VM을 복제합니다. 재해가 발생하는 경우 주 지역에서 VM을 장애 조치(Failover)하고, 계속해서 보조 지역에서 평소처럼 액세스하도록 합니다. 작동이 정상화되면 VM을 주 지역으로 장애 복구(Failback)할 수 있습니다.


![Azure Site Recovery](./media/migrate-best-practices-security-management/site-recovery.png)

*Site Recovery*

**자세한 정보:**
- Azure VM용 재해 복구 시나리오를 [검토합니다](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-disaster-recovery-guidance).
- 마이그레이션한 후 Azure VM에 대해 재해 복구를 설정하는 방법을 [알아봅니다](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-replicate-after-migration).

#### <a name="azure-storage"></a>Azure 저장소

Azure Storage는 기본 제공 복원력 및 고가용성을 위해 복제됩니다.

-   **GRS(지역 중복 스토리지)**: 지정된 1년 동안 최소 99.99999999999999%(16개의 9)의 개체 내구성으로 지역 전체의 가동 중단으로부터 보호합니다.
    - 스토리지 데이터는 주 지역이 페어링되는 보조 지역으로 복제됩니다.
    - 주 지역이 중단되고 Microsoft가 보조 지역으로의 장애 조치(Failover)를 시작하는 경우 데이터에 대한 읽기 액세스 권한을 갖게 됩니다.
- **RA-GRS(읽기 액세스 지역 중복 스토리지)**: 전체 지역 가동 중단으로부터 보호합니다.
    - 스토리지 데이터가 보조 지역으로 복제됩니다.
    - Microsoft가 장애 조치(Failover)를 시작하는지 여부에 관계없이, 보조 지역에서 복제된 데이터에 대해 읽기 액세스 권한이 보장됩니다. 동일한 지역에 있는 두 개 이상의 데이터 센터에 문제가 있을 수 있지만 지리적으로 분리된 지역에서 해당 데이터를 계속 사용할 수 있습니다.
-   **ZRS(영역 중복 스토리지)**:  데이터 센터 오류로부터 보호합니다.
    - ZRS는 단일 지역에 있는 3개의 스토리지 클러스터에서 데이터를 동기적으로 복제합니다. 클러스터는 물리적으로 구분되며 각각 자체의 가용성 영역에 있습니다.
    - 재해가 발생하는 경우에도 스토리지를 계속 사용할 수 있습니다. ZRS는 중요 업무용 워크로드의 최소 대상이어야 합니다.



**자세한 정보:**

- Azure Storage 복제에 대해 [알아봅니다](https://docs.microsoft.com/azure/storage/common/storage-redundancy).


### <a name="set-up-disaster-recovery-for-paas-workloads"></a>PaaS 워크로드에 대한 재해 복구 설정

PaaS 워크로드 예제에 대해 재해 복구 옵션을 고려해 보세요.

#### <a name="disaster-recovery-of-azure-sql-server"></a>Azure SQL Server의 재해 복구

각각이 데이터 손실, 복구 시간 및 비용에 영향을 미치는 여러 가지 다양한 옵션이 있습니다.

장애 조치(failover) 그룹 및 활성 지역 복제를 사용하여 지역 가동 중단 및 치명적인 장애에 대해 복원력을 제공할 수 있습니다.

- **활성 지역 복제**: 데이터 센터 중단이 발생하거나 주 데이터베이스에 연결할 수 없는 경우 빠른 재해 복구를 위해 활성 지역 복제를 배포합니다.
    - 지역 복제는 동일하거나 다른 지역에 있는 최대 4개의 보조 지역에 읽기 가능한 데이터베이스 복제본을 지속적으로 만듭니다.
    - 가동 중단이 발생할 경우 보조 지역 중 하나로 장애 조치(failover)하고 데이터베이스를 다시 온라인 상태로 전환합니다.
- **자동 장애 조치(failover) 그룹**: 자동 장애 조치(failover)은 여러 데이터베이스의 투명한 장애 조치(failover)를 사용하여 활성 지역 복제를 확장합니다.
    - 자동 장애 조치(failover) 그룹은 그룹 수준 데이터베이스 복제 및 자동 장애 조치(failover) 기능을 갖춘 활성 지역 복제의 강력한 추상화를 제공합니다.
    - 하나 이상의 주 데이터베이스를 호스트하는 주 서버, 주 데이터베이스의 읽기 전용 복제본을 호스트하는 보조 서버, 각 서버를 가리키는 수신기 및 자동 장애 조치(failover) 정책을 포함하는 장애 조치(failover) 그룹을 만듭니다.
    - 지정된 수신기 엔드포인트를 사용하면 장애 조치 후에 SQL 연결 문자열을 변경할 필요가 없어집니다.
- **지역 복원**: 
    -   지역 복원을 사용하여 데이터베이스를 다른 지역으로 복구할 수 있습니다. 모든 Azure 데이터베이스의 자동화된 백업은 백그라운드에서 보조 지역으로 복제됩니다. 항상 보조 지역에 저장된 백업 파일의 복사본에서 데이터베이스를 복원합니다.
-   **영역 중복 데이터베이스**는 Azure 가용성 영역에 대한 기본 제공 지원을 제공합니다.
    - 영역 중복 데이터베이스는 데이터 센터 장애가 발생할 경우 Azure SQL Server의 가용성을 향상시킵니다.
    - 영역 중복 기능을 사용할 경우 지역의 다른 가용성 영역 내에 중복된 데이터베이스 복제본을 배치할 수 있습니다. 



![지역에서 복제](./media/migrate-best-practices-security-management/geo-replication.png)

*지역에서 복제*

**자세한 정보:**
- Azure SQL Server의 고가용성에 대해 [알아봅니다](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability).
- 재해 복구를 위한 Azure SQL Database 101에 대해 [읽어봅니다](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/).
- 활성 지역 복제 및 장애 조치(failover) 그룹의 [개요를 확인합니다](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- 재해 복구용 디자인에 대해 [알아봅니다](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery).
- 장애 조치(failover) 그룹에 대한 [모범 사례를 확인합니다](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- 지역 복원 또는 장애 조치(failover) 이후의 보안에 대한 [모범 사례를 확인합니다](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-security-config).
- 영역 중복에 대해 [알아봅니다](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability#zone-redundant-configuration).
- SQL Database용 재해 복구 훈련을 수행하는 방법을 [알아봅니다](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-drills).

### <a name="disaster-recovery-for-azure-functions"></a>Azure Functions에 대한 재해 복구

Azure의 컴퓨팅 인프라가 실패하면 Azure 함수 앱을 사용하지 못할 수 있습니다.

- 이러한 작동 중단 가능성을 최소화하기 위해 다른 지역에 배포된 두 개의 함수 앱을 사용합니다.
- Azure Traffic Manager는 기본 함수 앱의 문제를 감지하고, 보조 지역의 함수 앱으로 트래픽을 자동으로 리디렉션하도록 구성할 수 있습니다.
- 지역 중복 스토리지가 있는 Traffic Manager를 사용하면 지역에 장애가 발생할 경우에 대비해서 여러 지역에서 동일한 함수를 유지할 수 있습니다.


![Traffic Manager](./media/migrate-best-practices-security-management/traffic-manager.png)

*Traffic Manager*

**자세한 정보:**

- Azure 앱용 재해 복구에 대해 [알아봅니다](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).
- 지속형 Azure 함수의 재해 복구 및 지역 복제에 대해 [알아봅니다](https://docs.microsoft.com/azure/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution).


## <a name="best-practice-use-managed-disks-and-availability-sets"></a>모범 사례: Managed Disks 및 가용성 세트 사용

Azure는 가용성 세트를 사용하여 VM을 논리적으로 함께 그룹화하고 다른 리소스의 세트에 VM을 격리합니다. 가용성 세트의 VM은 로컬 오류로부터 보호하기 위해 별도 하위 시스템을 사용하여 여러 장애 도메인 간에 분산되며, 세트의 모든 VM이 동시에 다시 부팅되는 일이 없도록 여러 업데이트 도메인 간에도 분산됩니다.

Azure Managed Disks는 VM 디스크와 연결된 스토리지 계정을 관리하여 Azure IaaS VM의 디스크 관리를 간소화합니다.

- 가능한 경우 Managed Disks를 사용하는 것이 좋습니다. 사용하려는 스토리지 유형과 필요한 디스크 크기만 지정하면 Azure에서 백그라운드에서 자동으로 디스크를 만들고 관리합니다.
- 기존 디스크를 Managed Disks로 변환할 수 있습니다.
- 높은 복원력 및 가용성을 위해서는 가용성 세트에 VM을 만들어야 합니다. 예정된 가동 중단 또는 갑작스러운 가동 중단이 발생할 경우 가용성 세트는 세트에 포함된 VM 중 하나 이상을 계속 사용할 수 있도록 합니다.


![Managed Disks](./media/migrate-best-practices-security-management/managed-disks.png)

*Managed Disks*

**자세한 정보:**
- Managed Disks의 [개요를 확인합니다](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).
- 디스크를 Managed Disks로 변환하는 방법을 [알아봅니다](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- Azure에서 Windows VM의 가용성을 관리하는 방법을 [알아봅니다](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).


## <a name="best-practice-monitor-resource-usage-and-performance"></a>모범 사례: 리소스 사용량 및 성능 모니터링 

뛰어난 크기 조정 기능을 활용하기 위해 워크로드를 Azure로 이동했을 수 있습니다. 그러나 워크로드를 이동한다고 해도 Azure에서 사용자 입력 없이 자동으로 크기 조정을 구현하는 것은 아닙니다. 예를 들어

- 마케팅 조직이 300% 더 많은 트래픽을 발생하는 새로운 TV 광고를 푸시하는 경우 이로 인해 사이트 가용성 문제가 발생할 수 있습니다. 새로 마이그레이션한 워크로드로 인해 할당된 제한에 도달하여 충돌이 일어날 수 있습니다.
- 또 다른 예제로 마이그레이션된 워크로드에 대한 DDoS(분산 서비스 거부) 공격이 일어날 수 있습니다. 이러한 경우 크기는 조정하지 않으면서 공격 출처가 리소스에 도달하지 않는 방식을 원할 수 있습니다.

두 가지 경우의 해결 방법이 다르지만, 사용량 및 성능 모니터링을 통해 상황을 정확히 이해하는 것이 필요합니다.

- Azure Monitor에서는 이러한 메트릭을 확인하고, 경고, 자동 크기 조정, 이벤트 허브, 논리 앱 등을 사용하여 대응할 수 있습니다.
- Azure 모니터링 외에도, 타사 SIEM 애플리케이션을 통합하여 Azure 로그에서 감사 및 성능 이벤트를 모니터링할 수 있습니다.


![Azure Monitor](./media/migrate-best-practices-security-management/monitor.png)
*Azure Monitor*

**자세한 정보:**

- Azure Monitor에 대해 [알아봅니다](https://docs.microsoft.com/azure/azure-monitor/overview).
- 통합 모니터링 및 진단을 위한 [모범 사례를 확인합니다](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
- 자동 크기 조정에 대해 [알아봅니다](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling).
- SIEM 도구에 Azure 데이터를 라우팅하는 방법을 [알아봅니다](https://docs.microsoft.com/azure/security-center/security-center-export-data-to-siem).

## <a name="best-practice-enable-diagnostic-logging"></a>모범 사례: 진단 로깅 사용

Azure 리소스는 상당히 많은 수의 로깅 메트릭 및 원격 분석 데이터를 생성합니다.

- 기본적으로 대부분의 리소스 유형에서는 진단 로깅을 사용하도록 설정하지 않습니다.
- 리소스에 대해 진단 로깅을 사용하도록 설정하여 로깅 데이터를 쿼리하고 해당 데이터를 기준으로 경고 및 플레이북을 빌드할 수 있습니다.
- 진단 로깅을 사용하도록 설정하면 각 리소스에는 특정 범주 세트가 구성됩니다. 하나 이상의 로깅 범주와 로그 데이터의 위치를 선택합니다. 저장소 계정에 이벤트 허브 또는 Azure Monitor 로그로 로그를 보낼 수 있습니다. 


![진단 로깅](./media/migrate-best-practices-security-management/diagnostics.png)
*진단 로깅*

**자세한 정보:**

- 로그 데이터 수집 및 사용에 대해 [알아봅니다](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).
- 진단 로깅에 대해 [지원되는 기능에 대해 알아봅니다](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema).


## <a name="best-practice-set-up-alerts-and-playbooks"></a>모범 사례: 경고 및 플레이북 설정

Azure 리소스에 대해 진단 로깅을 사용하도록 설정하면 로깅 데이터를 사용하여 사용자 지정 경고를 만들 수 있습니다.

- 경고는 모니터링 데이터에서 조건이 발견되면 사전에 알려줍니다. 그러면 시스템 사용자가 알아차리기 전에 문제를 해결할 수 있습니다. 메트릭 값, 로그 검색 쿼리, 활동 로그 이벤트, 플랫폼 상태 및 웹 사이트 가용성 등을 경고할 수 있습니다.
- 경고가 트리거되면 논리 앱 플레이북을 실행할 수 있습니다. 플레이북은 특정 경고에 대한 응답을 자동화하고 오케스트레이션할 수 있습니다. 플레이북은 Azure Logic Apps를 기준으로 합니다. 논리 앱 템플릿을 사용하여 플레이북을 만들거나 나만의 고유한 플레이북을 만들 수 있습니다.
- 간단한 예로, 네트워크 보안 그룹에 대해 포트 검색이 발생할 때 트리거되는 경고를 만들 수 있습니다.  실행되는 플레이북을 설정하고 검색 원본의 IP 주소를 잠글 수 있습니다.
- 또 다른 예로 메모리 누수가 있는 앱을 들 수 있습니다.  메모리 사용량이 특정 지점에 도달하면 플레이북은 프로세스를 재생할 수 있습니다.


![경고](./media/migrate-best-practices-security-management/alerts.png)
*경고*

**자세한 정보:**
- 경고에 대해 [알아봅니다](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts).
- Security Center 경고에 대응하는 보안 플레이북에 대해 [알아봅니다](https://docs.microsoft.com/azure/security-center/security-center-playbooks).

## <a name="best-practice-use-the-azure-dashboard"></a>모범 사례: Azure 대시보드 사용

Azure Portal은 웹 기반 통합 콘솔로, 간단한 웹앱에서 복잡한 클라우드 응용 프로그램까지 모든 것을 빌드, 관리 및 모니터링할 수 있습니다. 여기에는 사용자 지정 가능한 대시보드 및 접근성 옵션이 포함됩니다.
- 여러 개의 대시보드를 만들고 Azure 구독에 액세스할 수 있는 다른 사용자와 공유할 수 있습니다.
- 이 공유 모델을 사용하여 팀은 Azure 환경을 파악할 수 있으며 클라우드에서 시스템을 관리할 때 사전에 대처할 수 있습니다.


![Azure 대시보드](./media/migrate-best-practices-security-management/dashboard.png)
*Azure 대시보드*

**자세한 정보:**

- 대시보드를 만드는 방법을 [알아봅니다](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards).
- 대시보드 구조에 대해 [알아봅니다](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-structure).


## <a name="best-practice-understand-support-plans"></a>모범 사례: 지원 플랜 이해

경우에 따라 지원 담당자 또는 Microsoft 지원 담당자와 공동으로 작업해야 할 필요가 있을 수 있습니다. 재해 복구와 같은 시나리오 동안 지원을 위한 정책 및 절차를 설정하는 것은 중요한 작업입니다. 또한 관리자 및 지원 담당자는 이러한 정책을 구현하는 방법을 교육 받아야 합니다.

- 드물지만 Azure 서비스 문제가 워크로드에 영향을 미칠 경우, 관리자는 가장 적절하고 효율적인 방식으로 Microsoft 지원 티켓을 제출하는 방법을 알고 있어야 합니다.
- Azure에 제공하는 다양한 지원 플랜을 숙지해야 합니다. 개발자 인스턴스 전용 응답 시간부터 응답 시간이 15분 미만인 프리미어 지원까지 다양합니다.


![지원 플랜](./media/migrate-best-practices-security-management/support.png)
*지원 플랜*

**자세한 정보:**
- Azure 지원 플랜의 [개요를 확인합니다](https://azure.microsoft.com/support/options/).
- SLA(서비스 수준 계약)에 대해 [알아봅니다](https://azure.microsoft.com/support/legal/sla/).

## <a name="best-practice-manage-updates"></a>모범 사례: 업데이트 관리

최신 운영 체제 및 소프트웨어 업데이트로 Azure VM을 업데이트하는 일은 번거로운 작업입니다. 모든 VM을 노출하고, 필요한 업데이트를 파악하고, 해당 업데이트를 자동으로 푸시하는 일은 매우 중요합니다.

- Azure Automation의 업데이트 관리 기능을 사용하면 Azure, 온-프레미스 및 다른 클라우드 공급자에 배포된 Windows 및 Linux 머신의 운영 체제 업데이트를 관리할 수 있습니다. 
- 업데이트 관리를 사용하여 모든 에이전트 컴퓨터에서 사용 가능한 업데이트의 상태에 빠르게 액세스하고 업데이트 설치를 관리할 수 있습니다.
- Azure Automation 계정에서 직접 VM의 업데이트 관리를 사용하도록 설정할 수 있습니다. 또한 Azure Portal에서 VM 페이지의 단일 VM을 업데이트할 수도 있습니다.
- 또한 Azure VM을 System Center Configuration Manager에 등록할 수 있습니다. 그런 다음, Configuration Manager 워크로드를 Azure에 마이그레이션하고 단일 웹 인터페이스에서 보고 및 소프트웨어 업데이트를 수행할 수 있습니다.


![VM 업데이트](./media/migrate-best-practices-security-management/updates.png)
*업데이트*

**자세한 정보:**

- Azure의 업데이트 관리에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/automation/automation-update-management).
- 업데이트 관리와 Configuration Manager를 통합하는 방법을 [자세히 알아봅니다](https://docs.microsoft.com/azure/automation/oms-solution-updatemgmt-sccmintegration).
- Azure의 Configuration Manager에 대한 [질문과 대답](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure)


## <a name="implement-a-change-management-process"></a>변경 관리 프로세스 구현

모든 프로덕션 시스템과 마찬가지로, 모든 유형의 변경은 작업 환경에 영향을 줄 수 있습니다. 프로덕션 시스템 변경하기 위해 요청을 제출해야 하는 변경 관리 프로세스 또한 마이그레이션된 환경에서 유용합니다.

- 변경 관리를 위한 모범 사례 프레임워크를 구축하여 관리자 및 지원 직원 사이에서 인지도를 높일 수 있습니다.
- Azure Automation을 사용하여 마이그레이션된 워크플로에 대한 구성 관리 및 변경 내용 추적을 지원할 수 있습니다.
- 변경 관리 프로세스를 적용할 경우 감사 로그를 사용하여 Azure 변경 로그를 기존(또는 신규) 변경 요청에 연결할 수 있습니다. 해당 변경 요청 없이 수행된 변경이 있을 경우 프로세스에서 잘못된 부분조을 사할 수 있습니다.

Azure Automation에는 변경 내용 추적 솔루션에 있습니다.

- 이 솔루션은 Windows 및 Linux 소프트웨어 및 파일, Windows 레지스트리 키, Windows 서비스 및 Linux 디먼의 변경 내용을 추적합니다.
- 모니터링 되는 서버에 변경 내용 처리를 위해 클라우드의 Azure Monitor 서비스에 전송 됩니다.
- 논리는 수신된 데이터에 적용되며 클라우드 서비스는 데이터를 기록합니다.
- 변경 추적 대시보드에서 서버 인프라에서 수행한 변경 내용을 쉽게 확인할 수 있습니다.


![변경 관리](./media/migrate-best-practices-security-management/change.png)
*변경 관리*

**자세한 정보:**

- 변경 추적에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/automation/automation-change-tracking).
- Azure Automation 기능에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/automation/automation-intro).




## <a name="next-steps"></a>다음 단계 

기타 모범 사례를 검토합니다.


- 마이그레이션 이후 네트워킹에 대한 [모범 사례](migrate-best-practices-networking.md)
- 마이그레이션 이후 관리 및 보안에 대한 [모범 사례](migrate-best-practices-costs.md)








