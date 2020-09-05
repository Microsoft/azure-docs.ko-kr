---
title: SWIFT CSP-CSCF v2020 청사진 샘플 컨트롤
description: SWIFT CSP-CSCF v2020 청사진 샘플의 컨트롤 매핑. 각 컨트롤은 평가를 지원하는 하나 이상의 Azure 정책에 매핑됩니다.
ms.date: 08/18/2020
ms.topic: sample
ms.openlocfilehash: ee9ba86c41f37aac8eba3dbf973d2853a493547a
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612744"
---
# <a name="control-mapping-of-the-swift-csp-cscf-v2020-blueprint-sample"></a>SWIFT CSP-CSCF v2020 청사진 샘플의 컨트롤 매핑

다음 문서에서는 Azure Blueprints SWIFT CSP-CSCF v2020 청사진 샘플이 SWIFT CSP-CSCF v2020 컨트롤에 매핑되는 방법을 자세히 설명합니다. 컨트롤에 대한 자세한 내용은 [SWIFT CSP-CSCF v2020](https://www.swift.com/myswift/customer-security-programme-csp)을 참조하세요.

다음은 **SWIFT CSP-CSCF v2020** 컨트롤에 대한 매핑입니다. 특정 컨트롤 매핑으로 바로 점프하려면 오른쪽의 탐색 기능을 사용합니다. 많은 매핑된 컨트롤은 [Azure Policy](../../../policy/overview.md) 이니셔티브를 사용하여 구현됩니다. 전체 이니셔티브를 검토하려면 Azure Portal에서 **정책**을 열고 **정의** 페이지를 선택합니다. 그런 다음, **\[미리 보기\]: SWIFT CSP-CSCF v2020 컨트롤 감사 및 감사 요구 사항을 지원하기 위한 특정 VM 확장 배포** 기본 제공 정책 이니셔티브를 찾아서 선택합니다.

> [!IMPORTANT]
> 아래의 각 컨트롤은 하나 이상의 [Azure Policy](../../../policy/overview.md) 정의와 연결되어 있습니다. 이러한 정책은 컨트롤을 사용한 [규정 준수 평가](../../../policy/how-to/get-compliance-data.md)에 도움이 될 수 있지만, 컨트롤과 하나 이상의 정책 간에 일대일 또는 완벽한 일치 관계가 없는 경우도 많습니다. 따라서 Azure Policy의 **규정 준수**는 정책 자체만 가리킬 뿐, 컨트롤의 모든 요구 사항을 완벽하게 준수한다는 것은 아닙니다. 또한 규정 준수 표준에는 현재 Azure Policy 정의에서 처리되지 않은 컨트롤이 포함되어 있습니다. 따라서 Azure Policy의 규정 준수는 전반적인 규정 준수 상태를 부분적으로 표시할 뿐입니다. 이 규정 준수 청사진 샘플에 대한 컨트롤과 Azure Policy 정의 간의 연결은 시간이 지남에 따라 변경될 수 있습니다. 변경 기록을 보려면 [GitHub 커밋 기록](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/swift-2020/control-mapping.md)을 참조하세요.

## <a name="12-and-51-account-management"></a>1.2 및 5.1 계정 관리

이 청사진은 조직의 계정 관리 요구 사항을 준수하지 않는 계정을 검토하는 데 도움이 됩니다. 이 청사진은 구독 및 사용되지 않는 계정에 대한 읽기, 쓰기 및 소유자 권한으로 외부 계정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 이러한 정책에 의해 감사된 계정을 검토하면 계정 관리 요구 사항이 충족되도록 적절한 조치를 취할 수 있습니다.

- 더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.
- 읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.
- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.

## <a name="26-51-64-and-65a-account-management--role-based-schemes"></a>2.6, 5.1, 6.4 및 6.5A 계정 관리 | 역할 기반 체계

Azure는 Azure의 리소스에 대한 액세스 권한이 있는 사용자를 관리할 수 있도록 [RBAC(역할 기반 액세스 제어)](../../../../role-based-access-control/overview.md)를 구현합니다. Azure Portal을 사용하여 Azure 리소스에 대한 액세스 권한이 있는 사용자 및 해당 사용자의 권한을 검토할 수 있습니다. 또한 이 청사진은 SQL Server 및 Service Fabric에 대한 Azure Active Directory 인증의 사용을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. Azure Active Directory 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다. 또한 이 청사진은 사용자 지정 RBAC 규칙의 사용을 감사하는 Azure Policy 정의를 할당합니다. 사용자 지정 RBAC 규칙은 오류가 발생할 가능성이 높기 때문에 사용자 지정 RBAC 규칙이 구현되는 경우를 이해하면 어떤 구현이 필요하고 적절한지 확인하는 데 도움이 될 수 있습니다.

- SQL Server에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.
- 관리 디스크를 사용하지 않는 VM 감사
- Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 합니다.

## <a name="29a--account-management--account-monitoring--atypical-usage"></a>2.9A 계정 관리 | 계정 모니터링/비정상적 사용

JIT(Just-In-Time) 가상 머신 액세스를 사용하면 Azure 가상 머신으로의 인바운드 트래픽이 잠기기 때문에, 필요할 때 VM에 쉽게 연결할 수 있으면서도 공격 노출을 줄일 수 있습니다. JIT의 가상 머신 액세스 요청이 모두 활동 로그에 기록되므로 비정상적인 사용을 모니터링할 수 있습니다. 이 청사진은 JIT 액세스를 지원할 수 있지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함

## <a name="13-51-and-64-separation-of-duties"></a>1.3, 5.1 및 6.4 의무 분리

Azure 구독 소유자가 한 명만 있으면 관리 중복이 허용되지 않습니다. 반대로, Azure 구독 소유자가 너무 많으면 손상된 소유자 계정을 통한 위반 가능성이 증가할 수 있습니다. 이 청사진을 사용하면 Azure 구독의 소유자 수를 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 적절한 수의 Azure 구독 소유자를 유지 관리할 수 있습니다. 또한 이 청사진은 Windows 가상 머신에서 관리자 그룹의 멤버 자격을 제어할 수 있는 Azure Policy 정의를 할당합니다. 구독 소유자 및 가상 머신 관리자 권한을 관리하면 적절한 임무 분리를 구현하는 데 도움이 될 수 있습니다.

- 구독에 최대 3명의 소유자를 지정해야 합니다.
- 지정한 멤버 중 일부가 관리자 그룹에 없는 Windows VM의 감사 결과를 표시합니다.
- 지정한 멤버 중 일부가 관리자 그룹에 없는 Windows VM을 감사하기 위한 필수 조건을 배포합니다.
- 구독에 둘 이상의 소유자를 할당해야 합니다.

## <a name="13-51-and-64-least-privilege--review-of-user-privileges"></a>1.3, 5.1 및 6.4 최소 권한 | 사용자 권한 검토

Azure는 Azure의 리소스에 대한 액세스 권한이 있는 사용자를 관리할 수 있도록 [RBAC(역할 기반 액세스 제어)](../../../../role-based-access-control/overview.md)를 구현합니다. Azure Portal을 사용하여 Azure 리소스에 대한 액세스 권한이 있는 사용자 및 해당 사용자의 권한을 검토할 수 있습니다. 이 청사진은 검토를 위해 우선 순위를 지정해야 하는 감사 계정에 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 이러한 계정 지표는 최소 권한 컨트롤이 구현되었는지 확인하는 데 도움이 될 수 있습니다.

- 구독에 최대 3명의 소유자를 지정해야 합니다.
- 지정한 도메인에 조인되지 않은 Windows VM의 감사 결과 표시
- 지정한 도메인에 조인되지 않은 Windows VM을 감사하기 위한 필수 조건 배포
- 구독에 둘 이상의 소유자를 할당해야 합니다.

## <a name="22-and-27-security-attributes"></a>2.2 및 2.7 보안 특성

Azure SQL Database에 대한 고급 데이터 보안의 데이터 검색 및 분류 기능은 데이터베이스에서 중요한 데이터를 검색, 분류, 레이블 지정 및 보호하는 기능을 제공합니다. 데이터베이스 분류 상태에 대한 가시성을 제공하고, 데이터베이스 내 및 해당 경계 외부의 중요한 데이터에 대한 액세스를 추적하는 데 사용할 수 있습니다. 고급 데이터 보안을 사용하면 조직에 적합한 보안 특성과 관련된 정보를 확인할 수 있습니다. 이 청사진은 SQL 서버에서 고급 데이터 보안의 사용을 모니터링하고 적용하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 

- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.
- SQL 서버에 Advanced Data Security 배포

## <a name="22-27-41-and-61-remote-access--automated-monitoring--control"></a>2.2, 2.7, 4.1 및 6.1 원격 액세스 | 자동화된 모니터링/제어

이 청사진을 사용하면 Azure App Service 애플리케이션의 원격 디버깅이 해제되어 있는지 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의 및 암호 없이 계정에서 원격 연결을 허용하는 Linux 가상 머신을 감사하는 정책 정의를 할당하여 원격 액세스를 모니터링하고 제어할 수 있습니다. 또한 이 청사진은 스토리지 계정에 제한 없이 액세스할 수 있는 Azure Policy 정의를 할당합니다. 이러한 지표를 모니터링하면 원격 액세스 방법이 보안 정책을 준수하는 데 도움이 될 수 있습니다.

- 암호 없이 계정에서 원격으로 연결할 수 있는 Linux VM의 감사 결과를 표시합니다.
- 암호 없이 계정에서 원격으로 연결할 수 있는 Linux VM을 감사하기 위한 필수 조건을 배포합니다.
- 스토리지 계정은 네트워크 액세스를 제한해야 함
- API 앱에 대해 원격 디버깅을 해제해야 함
- 함수 앱에 대해 원격 디버깅을 해제해야 함
- 웹 애플리케이션에 대해 원격 디버깅을 해제해야 함

## <a name="13-and-64-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>1.3 및 6.4 감사 레코드의 콘텐츠 | 계획된 감사 레코드 콘텐츠의 중앙 집중식 관리

Azure Monitor를 통해 수집되는 로그 데이터는 Log Analytics 작업 영역에 저장되므로 중앙 집중식 구성 및 관리가 가능합니다. 이 청사진을 사용하면 Azure 가상 머신에서 Log Analytics 에이전트의 배포를 감사하고 적용하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 이벤트가 기록되도록 할 수 있습니다.

- \[미리 보기\]: Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- Linux VM용 Log Analytics 에이전트 배포
- Windows VM용 Log Analytics 에이전트 배포

## <a name="22-27-and-64-response-to-audit-processing-failures"></a>2.2, 2.7 및 6.4 감사 처리 실패에 대한 응답

이 청사진은 감사 및 이벤트 로깅 구성을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 이러한 구성을 모니터링하면 감사 시스템 오류 또는 구성 오류 지표를 제공하고 정정 작업을 수행하는 데 도움이 될 수 있습니다.

- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.
- 진단 설정 감사
- SQL Server에 대한 감사가 사용되도록 설정되어야 함

## <a name="13-and-64-audit-review-analysis-and-reporting--central-review-and-analysis"></a>1.3 및 6.4 감사 검토, 분석 및 보고 | 중앙 검토 및 분석

Azure Monitor를 통해 수집되는 로그 데이터는 Log Analytics 작업 영역에 저장되므로 중앙 집중식 보고 및 분석이 가능합니다. 이 청사진을 사용하면 Azure 가상 머신에서 Log Analytics 에이전트의 배포를 감사하고 적용하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 이벤트가 기록되도록 할 수 있습니다.

- \[미리 보기\]: Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- Linux VM용 Log Analytics 에이전트 배포
- Windows VM용 Log Analytics 에이전트 배포

## <a name="13-22-27-64-and-65a-audit-generation"></a>1.3, 2.2, 2.7, 6.4 및 6.5A 감사 생성

이 청사진을 사용하면 Azure 리소스에 대한 로그 설정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 시스템 이벤트가 기록되도록 할 수 있습니다. 이러한 정책 정의는 Azure 가상 머신에 대한 Log Analytics 에이전트의 배포와 다른 Azure 리소스 종류의 감사 설정 구성을 감사하고 적용합니다. 또한 이러한 정책은 진단 로그 구성을 감사하여 Azure 리소스 내에서 수행되는 작업에 대한 인사이트를 제공합니다. 뿐만 아니라 SQL 서버에 감사 및 Advanced Data Security가 구성됩니다.

- Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- Linux VMSS(VM Scale Sets)용 Log Analytics 에이전트 배포
- Linux VM용 Log Analytics 에이전트 배포
- Windows VMSS(VM Scale Sets)용 Log Analytics 에이전트 배포
- Windows VM용 Log Analytics 에이전트 배포
- 진단 설정 감사
- SQL 감사 서버 수준 감사 설정
- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.
- SQL 서버에 Advanced Data Security 배포
- SQL Server에 대한 감사가 사용되도록 설정되어야 함
- 네트워크 보안 그룹에 대한 진단 설정 배포

## <a name="11-least-functionality--prevent-program-execution"></a>1.1 최소한의 기능 | 프로그램 실행 금지

Azure Security Center의 적응형 애플리케이션 제어는 특정 소프트웨어가 가상 머신에서 실행되지 않도록 차단하는 자동화된 인텔리전트 엔드투엔드 애플리케이션 허용 목록 관리 솔루션입니다. 애플리케이션 제어는 승인되지 않은 애플리케이션의 실행을 금지하는 적용 모드에서 실행할 수 있습니다. 이 청사진은 애플리케이션 허용 목록을 사용하는 것이 좋지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 Azure Policy 정의를 할당합니다.

- 머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 합니다.

## <a name="11-least-functionality--authorized-software--whitelisting"></a>1.1 최소한의 기능 | 권한이 부여된 소프트웨어/허용 목록

Azure Security Center의 적응형 애플리케이션 제어는 특정 소프트웨어가 가상 머신에서 실행되지 않도록 차단하는 자동화된 인텔리전트 엔드투엔드 애플리케이션 허용 목록 관리 솔루션입니다. 애플리케이션 제어를 사용하면 가상 머신에 사용하도록 승인인 애플리케이션 목록을 만들 수 있습니다. 이 청사진은 애플리케이션 허용 목록을 사용하는 것이 좋지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 합니다.

## <a name="11-user-installed-software"></a>1.1 사용자가 설치한 소프트웨어

Azure Security Center의 적응형 애플리케이션 제어는 특정 소프트웨어가 가상 머신에서 실행되지 않도록 차단하는 자동화된 인텔리전트 엔드투엔드 애플리케이션 허용 목록 관리 솔루션입니다. 애플리케이션 제어를 사용하면 소프트웨어 제한 정책을 적용하고 규정 준수 여부를 모니터링할 수 있습니다. 이 청사진은 애플리케이션 허용 목록을 사용하는 것이 좋지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 합니다.
- 가상 머신을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.

## <a name="42-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>4.2 식별 및 인증(조직 사용자) | 네트워크를 통해 권한 있는 계정에 액세스

이 청사진을 사용하면 소유자 및/또는 쓰기 권한이 있지만 다단계 인증을 사용하도록 설정되지 않은 계정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 권한 있는 액세스를 제한하고 제어할 수 있습니다. 다단계 인증은 인증 정보 중 한 부분이 손상되더라도 계정을 안전하게 유지하는 데 도움이 됩니다. 다단계 인증을 사용하지 않고 계정을 모니터링하면 손상될 가능성이 더 높은 계정을 식별할 수 있습니다.

- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

## <a name="42-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>4.2 식별 및 인증(조직 사용자) | 권한 없는 계정에 대한 네트워크 액세스

이 청사진은 읽기 권한이 있지만 다단계 인증을 사용하지 않는 계정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 액세스 권한을 제한 및 제어할 수 있습니다. 다단계 인증은 인증 정보 중 한 부분이 손상되더라도 계정을 안전하게 유지하는 데 도움이 됩니다. 다단계 인증을 사용하지 않고 계정을 모니터링하면 손상될 가능성이 더 높은 계정을 식별할 수 있습니다.

- 구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

## <a name="23-and-41-authenticator-management"></a>2.3 및 4.1 Authenticator 관리

이 청사진은 암호 없이 계정으로부터의 원격 연결을 허용하거나 passwd 파일에 대한 권한이 잘못 설정된 Linux 가상 머신을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 또한 이 청사진은 Windows 가상 머신에 대한 암호 암호화 유형 구성을 감사하는 정책 정의를 할당합니다. 이러한 지표를 모니터링하면 시스템 인증자가 조직의 식별 및 인증 정책을 준수하는 데 도움이 됩니다.

- passwd 파일 권한이 0644로 설정되지 않은 Linux VM의 감사 결과 표시
- passwd 파일 권한이 0644로 설정되지 않은 Linux VM을 감사하기 위한 요구 사항 배포
- 암호 없는 계정이 있는 Linux VM의 감사 결과 표시
- 암호 없는 계정이 있는 Linux VM을 감사하기 위한 요구 사항 배포
- 해독 가능한 암호화를 사용하여 암호를 저장하지 않는 Windows VM의 감사 결과 표시
- 해독 가능한 암호화를 사용하여 암호를 저장하지 않는 Windows VM을 감사하기 위한 요구 사항 배포

## <a name="23-and-41-authenticator-management--password-based-authentication"></a>2.3 및 4.1 Authenticator 관리 | 암호 기반 인증

이 청사진을 사용하면 최소 암호 보안 수준 및 기타 암호 요구 사항을 적용하지 않는 Windows 가상 머신을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 강력한 암호를 적용할 수 있습니다. 암호 강도 정책을 위반하는 가상 머신을 파악할 수 있으면 모든 가상 머신 사용자 계정의 암호가 조직의 암호 정책을 준수하도록 정정 작업을 수행하는 데 도움이 됩니다.

- 이전 24개 암호의 재사용을 허용하는 Windows VM의 감사 결과 표시
- 암호 최대 사용 기간이 70일로 설정되지 않은 Windows VM의 감사 결과 표시
- 암호 최소 사용 기간이 1일로 설정되지 않은 Windows VM의 감사 결과 표시
- 암호 복잡성 설정이 사용되지 않는 Windows VM의 감사 결과 표시
- 암호 최소 길이가 14자로 제한되지 않는 Windows VM의 감사 결과 표시
- 해독 가능한 암호화를 사용하여 암호를 저장하지 않는 Windows VM의 감사 결과 표시
- 이전 24개 암호의 재사용을 허용하는 Windows VM을 감사하기 위한 요구 사항 배포
- 암호 최대 사용 기간이 70일로 설정되지 않은 Windows VM을 감사하기 위한 필수 조건 배포
- 암호 최소 사용 기간이 1일로 설정되지 않은 Windows VM을 감사하기 위한 필수 조건 배포
- 암호 복잡성 설정이 사용되지 않는 Windows VM을 감사하기 위한 필수 조건 배포
- 암호 최소 길이가 14자로 제한되지 않는 Windows VM을 감사하기 위한 필수 조건 배포
- 해독 가능한 암호화를 사용하여 암호를 저장하지 않는 Windows VM을 감사하기 위한 필수 조건 배포

## <a name="22-and-27-vulnerability-scanning"></a>2.2 및 2.7 취약성 검색

이 청사진을 사용하면 Azure Security Center에서 운영 체제 취약성, SQL 취약성 및 가상 머신 취약성을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 정보 시스템 취약성을 관리할 수 있습니다. Azure Security Center는 배포된 Azure 리소스의 보안 상태에 대한 실시간 인사이트를 가질 수 있도록 하는 보고 기능을 제공합니다. 또한 이 청사진은 SQL 서버에서 Advanced Data Security를 감사하고 적용하는 정책 정의를 할당합니다. Advanced Data Security는 배포된 리소스의 취약성을 이해할 수 있는 취약성 평가 및 고급 위협 보호 기능이 포함되어 있습니다.

- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.
- SQL Server에 대한 감사가 사용되도록 설정되어야 함
- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- SQL 데이터베이스의 취약성을 수정해야 합니다. 
- 머신 보안 구성의 취약성을 수정해야 합니다.

## <a name="13-denial-of-service-protection"></a>1.3 서비스 거부 방지

Azure의 DDoS(분선 서비스 거부) 표준 계층은 기본 서비스 계층보다 강력한 추가 기능 및 위험 완화 기능을 제공합니다. 이러한 추가 기능으로는 Azure Monitor 통합과 공격 후 완화 보고서를 검토하는 기능이 포함됩니다. 이 청사진은 DDoS 표준 계층을 사용하는지 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 서비스 계층 간의 기능 차이를 이해하면 해당 Azure 환경의 서비스 거부를 방지하는 최적의 솔루션을 선택할 수 있습니다.

- Azure DDoS Protection 표준을 사용하도록 설정해야 함

## <a name="11-and-61-boundary-protection"></a>1.1 및 6.1 경계 보호

이 청사진을 사용하면 Azure Security Center에서 네트워크 보안 그룹 강화 추천 사항을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 시스템 경계를 관리하고 제어할 수 있습니다. Azure Security Center는 인터넷 연결 가상 머신의 트래픽 패턴을 분석하여 공격 노출 영역을 줄일 수 있는 네트워크 보안 그룹 규칙 추천 사항을 제공합니다.
또한 이 청사진은 보호되지 않은 엔드포인트, 애플리케이션 및 스토리지 계정을 모니터링하는 정책 정의를 할당합니다. 방화벽으로 보호되지 않는 엔드포인트 및 애플리케이션과 액세스가 제한되지 않는 스토리지 계정은 정보 시스템 내에 포함된 정보에 대한 원치 않는 액세스를 허용할 수 있습니다.

- 적응형 네트워크 강화 추천 사항은 인터넷에 연결된 가상 머신에 적용해야 합니다.
- 인터넷 연결 엔드포인트를 통한 액세스를 제한해야 합니다.
- 스토리지 계정에 대한 무제한 네트워크 액세스 감사

## <a name="29a-boundary-protection--access-points"></a>2.9A 경계 보호 | 액세스 지점

JIT(Just-In-Time) 가상 머신 액세스를 사용하면 Azure 가상 머신으로의 인바운드 트래픽이 잠기기 때문에, 필요할 때 VM에 쉽게 연결할 수 있으면서도 공격 노출을 줄일 수 있습니다. JIT 가상 머신 액세스를 사용하면 Azure 리소스에 대한 외부 연결 수를 제한할 수 있습니다. 이 청사진은 JIT 액세스를 지원할 수 있지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함

## <a name="29a-boundary-protection--external-telecommunications-services"></a>2.9A 경계 보호 | 외부 원격 통신 서비스

JIT(Just-In-Time) 가상 머신 액세스를 사용하면 Azure 가상 머신으로의 인바운드 트래픽이 잠기기 때문에, 필요할 때 VM에 쉽게 연결할 수 있으면서도 공격 노출을 줄일 수 있습니다. JIT 가상 머신 액세스를 사용하면 액세스 요청 및 승인 프로세스를 촉진하여 트래픽 흐름 정책 예외를 관리할 수 있습니다. 이 청사진은 JIT 액세스를 지원할 수 있지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함

## <a name="21-24-24a-25a-and-26-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>2.1, 2.4, 2.4A, 2.5A 및 2.6 전송 기밀성 및 무결성 | 암호화 또는 대체 물리적 보호

이 청사진을 사용하면 통신 프로토콜에 구현된 암호화 메커니즘을 모니터링하는 데 도움이 되는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 전송된 정보의 기밀성과 무결성을 보호할 수 있습니다. 통신이 제대로 암호화되면 조직의 요구 사항을 충족시키거나 무단 공개 및 수정으로부터 정보를 보호하는 데 도움이 될 수 있습니다.

- API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.
- 보안 통신 프로토콜을 사용하지 않는 Windows 웹 서버의 감사 결과 표시
- 보안 통신 프로토콜을 사용하지 않는 Windows 웹 서버를 감사하기 위한 필수 조건 배포
- 함수 앱에 HTTPS를 통해서만 액세스 가능
- Redis Cache에 보안 연결만 사용하도록 설정해야 합니다.
- Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.
- 웹 애플리케이션에 HTTPS를 통해서만 액세스 가능

## <a name="22-23-25-41-and-27-protection-of-information-at-rest--cryptographic-protection"></a>2.2, 2.3, 2.5, 4.1 및 2.7 저장 정보 보호 | 암호화 보호

이 청사진을 사용하면 특정 암호화 컨트롤을 적용하고 취약한 암호 설정의 사용을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 암호화 컨트롤을 통해 저장 정보를 보호하는 정책을 적용할 수 있습니다. Azure 리소스의 암호화 구성이 최적이 아닐 수 있는 경우를 이해하면 리소스가 정보 보안 정책에 따라 구성되도록 정정 작업을 수행하는 데 도움이 될 수 있습니다. 특히 이 청사진이 할당하는 정책 정의는 데이터 레이크 스토리지 계정을 암호화하고, SQL 데이터베이스의 데이터를 투명하게 암호화하고, SQL 데이터베이스, 가상 머신 디스크 및 자동화 계정 변수에서 누락된 암호화를 감사할 것을 요구합니다.

- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.
- SQL 서버에 Advanced Data Security 배포
- SQL DB 투명한 데이터 암호화 배포
- SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.

## <a name="13-22-and-27-flaw-remediation"></a>1.3, 2.2 및 2.7 결함 수정

이 청사진을 사용하면 Azure Security Center에서 누락된 시스템 업데이트, 운영 체제 취약성, SQL 취약성 및 가상 머신 취약성을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 정보 시스템 결함을 관리할 수 있습니다. Azure Security Center는 배포된 Azure 리소스의 보안 상태에 대한 실시간 인사이트를 가질 수 있도록 하는 보고 기능을 제공합니다. 또한 이 청사진은 가상 머신 확장 집합용 운영 체제 패치를 보장하는 정책 정의를 할당합니다.

- Virtual Machine Scale Sets에 자동 OS 이미지 패치 필요
- 가상 머신 확장 집합에 대한 시스템 업데이트를 설치해야 합니다.
- 가상 머신에 시스템 업데이트를 설치해야 합니다.
- 가상 머신 확장 집합에서 종속성 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨
- 자동화 계정 변수를 암호화해야 합니다.
- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- 가상 머신에서 보안 구성의 취약성을 수정해야 합니다.
- SQL 데이터베이스의 취약성을 수정해야 합니다.

## <a name="61-malicious-code-protection"></a>6.1 악성 코드 방지

이 청사진을 사용하면 Azure Security Center에서 가상 머신의 누락된 엔드포인트 보호를 모니터링하고 Microsoft 맬웨어 방지 솔루션을 Windows 가상 머신에 적용하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 악성 코드 방지를 포함하여 엔드포인트 보호를 관리할 수 있습니다.

- Windows Server용 기본 Microsoft IaaSAntimalware 확장 배포
- 가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.
- Azure Security Center에서 누락된 Endpoint Protection 모니터링
- 스토리지 계정을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.

## <a name="61-malicious-code-protection--central-management"></a>6.1 악성 코드 방지 | 중앙 관리

이 청사진을 사용하면 Azure Security Center에서 가상 머신의 누락된 엔드포인트 보호를 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 악성 코드 방지를 포함하여 엔드포인트 보호를 관리할 수 있습니다. Azure Security Center는 배포된 Azure 리소스의 보안 상태에 대한 실시간 인사이트를 얻을 수 있는 중앙 집중식 관리 및 보고 기능을 제공합니다.

- 가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.
- Azure Security Center에서 누락된 Endpoint Protection 모니터링

## <a name="11-13-22-27-28-and-64-information-system-monitoring"></a>1.1, 1.3, 2.2, 2.7, 2.8 및 6.4 정보 시스템 모니터링

이 청사진은 Azure 리소스에 로깅 및 데이터 보안을 적용하고 감사하여 시스템 모니터링을 도와줍니다. 특히, 이 정책은 Log Analytics 에이전트 배포를 감사 및 적용하고 SQL 데이터베이스, 스토리지 계정 및 네트워크 리소스의 보안 설정을 강화합니다. 이러한 기능은 비정상적인 동작과 공격 징후를 감지하여 적절한 조치를 취하도록 도와줍니다.

- Log Analytics 에이전트가 예상대로 연결되지 않은 Windows VM의 감사 결과 표시
- Linux VMSS(VM Scale Sets)용 Log Analytics 에이전트 배포
- Linux VM용 Log Analytics 에이전트 배포
- Windows VMSS(VM Scale Sets)용 Log Analytics 에이전트 배포
- Windows VM용 Log Analytics 에이전트 배포
- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.
- SQL Server에 대한 Advanced Data Security 설정에는 보안 경고를 받을 이메일 주소가 포함되어야 합니다.
- Azure Stream Analytics에서 진단 로그를 사용하도록 설정
- SQL 서버에 Advanced Data Security 배포
- SQL Server에 감사 배포
- 가상 네트워크를 만들 때 Network Watcher 배포
- SQL 서버에 위협 탐지 배포

## <a name="22-and-28-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>2.2 및 2.8 정보 시스템 모니터링 | 트래픽 분석/비밀 반출

Azure Storage에 사용되는 Advanced Threat Protection은 스토리지 계정에 대한 비정상적이고 피해를 줄 수 있는 액세스 또는 악용 시도를 탐지합니다. 보호 경고 대상으로는 비정상적인 액세스 패턴, 비정상적인 추출/업로드 및 의심스러운 스토리지 작업이 포함됩니다. 이러한 지표는 정보를 몰래 반출하는 것을 탐지할 수 있습니다.

- SQL 서버에 위협 탐지 배포

> [!NOTE]
> 특정 Azure Policy 정의의 가용성은 Azure Government 및 기타 국가 클라우드에 따라 다를 수 있습니다.

## <a name="next-steps"></a>다음 단계

지금까지 SWIFT CSP-CSCF v2020 청사진의 컨트롤 매핑을 살펴보았습니다. 이제 다음 문서를 참조하여 청사진에 대해 알아보고 이 샘플을 배포하는 방법을 살펴보세요.

> [!div class="nextstepaction"]
> [SWIFT CSP-CSCF v2020 청사진 - 개요](./index.md)
> [SWIFT CSP-CSCF v2020 청사진 - 배포 단계](./deploy.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기
