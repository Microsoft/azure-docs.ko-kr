---
title: ISO 27001 Shared Services 청사진 샘플 컨트롤
description: ISO 27001 Shared Services 청사진 샘플에 대한 컨트롤 매핑. 각 컨트롤은 평가를 지원하는 하나 이상의 Azure 정책에 매핑됩니다.
ms.date: 07/13/2020
ms.topic: sample
ms.openlocfilehash: 7655ae2828972a9b99479c7a94f02b02005a9284
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927133"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>ISO 27001 Shared Services 청사진 샘플에 대한 컨트롤 매핑

다음 문서에서는 Azure Blueprints ISO 27001 Shared Services 청사진 샘플이 ISO 27001 컨트롤에 매핑되는 방법을 자세히 설명합니다. 컨트롤에 대한 자세한 내용은 [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html)을 참조하세요.

다음은 **ISO 27001:2013** 컨트롤에 대한 매핑입니다. 특정 컨트롤 매핑으로 바로 점프하려면 오른쪽의 탐색 기능을 사용합니다. 많은 매핑된 컨트롤은 [Azure Policy](../../../policy/overview.md) 이니셔티브를 사용하여 구현됩니다. 전체 이니셔티브를 검토하려면 Azure Portal에서 **정책**을 열고 **정의** 페이지를 선택합니다. 그런 다음, ISO 27001:2013 컨트롤 **감사 \[미리 보기\]를 찾아서 선택하고 감사 요구 사항**이 담긴 정책 이니셔티브를 지원하는 특정 VM 확장을 배포합니다.

> [!IMPORTANT]
> 아래의 각 컨트롤은 하나 이상의 [Azure Policy](../../../policy/overview.md) 정의와 연결되어 있습니다. 이러한 정책은 컨트롤을 사용한 [규정 준수 평가](../../../policy/how-to/get-compliance-data.md)에 도움이 될 수 있지만, 컨트롤과 하나 이상의 정책 간에 일대일 또는 완벽한 일치 관계가 없는 경우도 많습니다. 따라서 Azure Policy의 **규정 준수**는 정책 자체만 가리킬 뿐, 컨트롤의 모든 요구 사항을 완벽하게 준수한다는 것은 아닙니다. 또한 규정 준수 표준에는 현재 Azure Policy 정의에서 처리되지 않은 컨트롤이 포함되어 있습니다. 따라서 Azure Policy의 규정 준수는 전반적인 규정 준수 상태를 부분적으로 표시할 뿐입니다. 이 규정 준수 청사진 샘플에 대한 컨트롤과 Azure Policy 정의 간의 연결은 시간이 지남에 따라 변경될 수 있습니다. 변경 기록을 보려면 [GitHub 커밋 기록](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-shared/control-mapping.md)을 참조하세요.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 의무 분리

Azure 구독 소유자가 한 명만 있으면 관리 중복이 허용되지 않습니다. 반대로, Azure 구독 소유자가 너무 많으면 손상된 소유자 계정을 통한 위반 가능성이 증가할 수 있습니다. 이 청사진을 사용하면 Azure 구독에 대한 소유자 수를 감사하는 [Azure Policy](../../../policy/overview.md) 정의 2개를 할당하여 Azure 구독 소유자 수를 적절하게 유지할 수 있습니다. 구독 소유자 권한을 관리하면 적절한 임무 분리를 구현하는 데 도움이 될 수 있습니다.

- 구독에 최대 3명의 소유자를 지정해야 합니다.
- 구독에 둘 이상의 소유자를 할당해야 합니다.

## <a name="a821-classification-of-information"></a>A.8.2.1 정보의 분류

Azure의 [SQL Vulnerability Assessment 서비스](../../../../azure-sql/database/sql-vulnerability-assessment.md)는 데이터베이스에 저장된 중요한 데이터를 검색하는 데 도움이 되며 해당 데이터를 분류하기 위한 권장 사항을 포함합니다. 이 청사진은 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 SQL Vulnerability Assessment 검사 중에 식별된 취약성이 해결되었는지 감사합니다.

- SQL 데이터베이스의 취약성을 수정해야 합니다.

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 네트워크 및 네트워크 서비스 액세스

Azure는 Azure 리소스에 대한 액세스 권한이 있는 사용자를 관리하기 위해 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../../../role-based-access-control/overview.md)를 구현합니다. 이 청사진은 [Azure Policy](../../../policy/overview.md) 정의 7개를 할당하여 Azure 리소스에 대한 액세스 권한을 제어하는 데 도움이 됩니다. 이 정책은 리소스에 더 많은 권한의 액세스를 허용할 수 있는 리소스 유형의 사용을 감사합니다.
이 정책을 위반하는 리소스를 이해하면 Azure 리소스 액세스가 권한 있는 사용자로 제한되도록 정정 작업을 수행하는 데 도움이 될 수 있습니다.

- 암호 없는 계정이 있는 Linux VM을 감사하기 위한 필수 조건 배포
- 암호 없이 계정에서 원격으로 연결할 수 있는 Linux VM을 감사하기 위한 필수 조건을 배포합니다.
- 암호 없는 계정이 있는 Linux VM의 감사 결과 표시
- 암호 없이 계정에서 원격으로 연결할 수 있는 Linux VM의 감사 결과를 표시합니다.
- 스토리지 계정을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.
- 가상 머신을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.
- 관리 디스크를 사용하지 않는 VM 감사

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 권한 있는 액세스 권한 관리

이 청사진은 소유자 및/또는 쓰기 권한이 있는 외부 계정 및 다단계 인증을 사용하지 않는 소유자 및/또는 쓰기 권한을 가진 계정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 4개를 할당하여, 권한 있는 액세스 권한을 제한 및 제어하는 데 도움이 됩니다. Azure는 Azure 리소스에 대한 액세스 권한이 있는 사용자를 관리하기 위해 RBAC(역할 기반 액세스 제어)를 구현합니다. 또한 이 청사진은 SQL Servers 및 Service Fabric에 대한 Azure Active Directory 인증 사용을 감사하는 Azure Policy 정의 3개를 할당합니다. Azure Active Directory 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다. 이 청사진은 사용자 지정 RBAC 규칙의 사용을 감사하는 Azure Policy 정의도 할당합니다. 사용자 지정 RBAC 규칙은 오류가 발생할 가능성이 높기 때문에 사용자 지정 RBAC 규칙이 구현되는 경우를 이해하면 어떤 구현이 필요하고 적절한지 확인하는 데 도움이 될 수 있습니다.

- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.
- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.
- SQL Server에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.
- Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 합니다.
- 사용자 지정 RBAC 규칙의 사용 감사

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 사용자의 비밀 인증 정보 관리

이 청사진은 다단계 인증을 사용하지 않는 계정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 3개를 할당합니다. 다단계 인증은 인증 정보 중 한 부분이 손상되더라도 계정을 안전하게 유지하는 데 도움이 됩니다. 다단계 인증을 사용하지 않고 계정을 모니터링하면 손상될 가능성이 더 높은 계정을 식별할 수 있습니다. 또한 이 청사진은 Linux VM 암호 파일 권한을 감사하여 잘못 설정된 경우 경고하는 Azure Policy 정의 2개도 할당합니다. 이 설정을 사용하여 인증이 손상되지 않도록 정정 작업을 수행할 수 있습니다.

- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- passwd 파일 권한이 0644로 설정되지 않은 Linux VM의 감사 결과 표시
- 필수 구성 요소를 배포하여 암호 파일 권한이 0644로 설정되지 않은 Linux VM 감사

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 사용자 액세스 권한 검토

Azure는 Azure의 리소스에 액세스할 수 있는 사용자를 관리하는 데 도움이 되는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../../../role-based-access-control/overview.md)를 구현합니다. Azure Portal을 사용하여 Azure 리소스에 대한 액세스 권한이 있는 사용자 및 해당 사용자의 권한을 검토할 수 있습니다. 이 청사진은 사용 중단된 계정 및 상승된 권한이 있는 외부 계정을 포함하여 우선적으로 검토해야 하는 계정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 4개를 할당합니다.

- 더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.
- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 액세스 권한 제거 또는 조정

Azure는 Azure의 리소스에 액세스할 수 있는 사용자를 관리하는 데 도움이 되는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../../../role-based-access-control/overview.md)를 구현합니다. [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) 및 RBAC를 사용하여 사용자 역할을 조직 변경이 반영되도록 업데이트할 수 있습니다. 필요한 경우 계정을 로그인하지 못하게 차단하거나 제거하여 Azure 리소스에 대한 액세스 권한을 즉시 제거할 수 있습니다. 이 청사진은 제거 대상으로 고려해야 하는 사용 중단 계정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 2개를 할당합니다.

- 더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 보안 로그온 프로시저

이 청사진은 다단계 인증을 사용하지 않는 계정을 감사하는 Azure Policy 정의 세 개를 할당합니다. Azure Multi-Factor Authentication은 두 번째 형식의 인증을 요구하여 추가 보안을 제공하고 강력한 인증을 제공합니다. 다단계 인증을 사용하지 않고 계정을 모니터링하면 손상될 가능성이 더 높은 계정을 식별할 수 있습니다.

- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

## <a name="a943-password-management-system"></a>A.9.4.3 암호 관리 시스템

이 청사진은 최소 강도 및 기타 암호 요구 사항을 적용하지 않는 Windows VM을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 10개를 할당하여 강력한 암호를 적용하는 데 도움이 됩니다. 암호 강도 정책을 위반하는 VM을 인식하면 모든 VM 사용자 계정의 암호가 정책을 준수하도록 정정 작업을 수행하는 데 도움이 됩니다.

- 암호 복잡성 설정이 사용되지 않는 Windows VM의 감사 결과 표시
- 암호 최대 사용 기간이 70일로 설정되지 않은 Windows VM의 감사 결과 표시
- 암호 최소 사용 기간이 1일로 설정되지 않은 Windows VM의 감사 결과 표시
- 암호 최소 길이가 14자로 제한되지 않는 Windows VM의 감사 결과 표시
- 이전 24개 암호의 재사용을 허용하는 Windows VM의 감사 결과 표시
- 암호 복잡성 설정이 사용되지 않는 Windows VM을 감사하기 위한 필수 조건 배포
- 암호 최대 사용 기간이 70일로 설정되지 않은 Windows VM을 감사하기 위한 필수 조건 배포
- 암호 최소 사용 기간이 1일로 설정되지 않은 Windows VM을 감사하기 위한 필수 조건 배포
- 암호 최소 길이가 14자로 제한되지 않는 Windows VM을 감사하기 위한 필수 조건 배포
- 이전 24개 암호의 재사용을 허용하는 Windows VM을 감사하기 위한 요구 사항 배포

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 암호화 컨트롤의 사용에 대한 정책

이 청사진은 특정 암호화 컨트롤을 적용하고 취약한 암호 설정의 사용을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 13개를 할당하여 암호화 컨트롤 사용에 대한 정책을 적용하는 데 도움이 됩니다.
Azure 리소스의 암호화 구성이 최적이 아닐 수 있는 경우를 이해하면 리소스가 정보 보안 정책에 따라 구성되도록 정정 작업을 수행하는 데 도움이 될 수 있습니다. 구체적으로, 이 청사진에서 할당하는 정책을 적용하려면 Blob 스토리지 계정 및 Data Lake 스토리지 계정에 대한 암호화가 필요하며 SQL 데이터베이스에 대해 투명한 암호화가 필요합니다.또한 스토리지 계정, SQL 데이터베이스, 가상 머신 디스크 및 자동화 계정 변수에 대한 암호화 누락을 감사하고, 스토리지 계정, 함수 앱, 웹앱, API 앱 및 Redis Cache에 대한 안전하지 않은 연결을 감사하고, 취약한 가상 머신 암호 암호화를 감사하고, 암호화되지 않은 Service Fabric 통신을 감사해야 합니다.

- 함수 앱에 HTTPS를 통해서만 액세스 가능
- 웹 애플리케이션에 HTTPS를 통해서만 액세스 가능
- API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.
- 해독 가능한 암호화를 사용하여 암호를 저장하지 않는 Windows VM을 감사하기 위한 필수 조건 배포
- 해독 가능한 암호화를 사용하여 암호를 저장하지 않는 Windows VM의 감사 결과 표시
- 가상 머신에서 디스크 암호화를 적용해야 합니다.
- 자동화 계정 변수를 암호화해야 합니다.
- Azure Cache for Redis에 보안 연결만 사용하도록 설정해야 함
- Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.
- Service Fabric 클러스터의 ClusterProtectionLevel 속성을 EncryptAndSign으로 설정해야 합니다.
- SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.

## <a name="a1241-event-logging"></a>A.12.4.1 이벤트 로깅

이 청사진은 Azure 리소스에 대한 로그 설정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 7개를 할당하여 시스템 이벤트가 기록되도록 하는 데 도움이 됩니다.
진단 로그는 Azure 리소스 내에서 수행된 작업에 대한 인사이트를 제공합니다.

- 종속성 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨
- 가상 머신 확장 집합에서 종속성 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨
- [미리 보기]: Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- 가상 머신 확장 집합에서 Log Analytics 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨
- 진단 설정 감사
- SQL Server에 대한 감사가 사용되도록 설정되어야 함

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 관리자 및 운영자 로그

이 청사진은 Azure 리소스에 대한 로그 설정을 감사하는 Azure Policy 정의 7개를 할당하여 시스템 이벤트가 기록되도록 보장합니다. 진단 로그는 Azure 리소스 내에서 수행된 작업에 대한 인사이트를 제공합니다.

- 종속성 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨
- 가상 머신 확장 집합에서 종속성 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨
- [미리 보기]: Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- 가상 머신 확장 집합에서 Log Analytics 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨
- 진단 설정 감사
- SQL Server에 대한 감사가 사용되도록 설정되어야 함

## <a name="a1244-clock-synchronization"></a>A.12.4.4 클럭 동기화

이 청사진은 Azure 리소스에 대한 로그 설정을 감사하는 Azure Policy 정의 7개를 할당하여 시스템 이벤트가 기록되도록 보장합니다. Azure 로그는 동기화된 내부 클록을 사용하여 리소스 간 이벤트의 시간 관련 레코드를 만듭니다.

- 종속성 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨
- 가상 머신 확장 집합에서 종속성 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨
- [미리 보기]: Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- 가상 머신 확장 집합에서 Log Analytics 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨
- 진단 설정 감사
- SQL Server에 대한 감사가 사용되도록 설정되어야 함

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 운영 체제에 소프트웨어 설치

적응형 애플리케이션 제어는 Azure에 있는 VM에서 실행할 수 있는 애플리케이션을 제어하는 데 도움이 되는 Azure Security Center의 솔루션입니다. 이 청사진은 허용된 애플리케이션 세트에 대한 변경 사항을 모니터링하는 Azure Policy 정의를 할당합니다. 이 기능을 사용하면 Azure VM에서 소프트웨어 및 애플리케이션 설치를 제어할 수 있습니다.

- 머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 합니다.

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 기술 취약성 관리

이 청사진은 Azure Security Center에서 누락된 시스템 업데이트, 운영 체제 취약성, SQL 취약성 및 가상 머신 취약성을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의 5개를 할당하여 정보 시스템 취약성 관리를 도와줍니다. Azure Security Center는 배포된 Azure 리소스의 보안 상태에 대한 실시간 인사이트를 가질 수 있도록 하는 보고 기능을 제공합니다.

- Azure Security Center에서 누락된 Endpoint Protection 모니터링
- 시스템 업데이트를 머신에 설치해야 합니다.
- 머신 보안 구성의 취약성을 수정해야 합니다.
- SQL 데이터베이스의 취약성을 수정해야 합니다.
- 취약성 평가 솔루션으로 취약성을 수정해야 합니다.

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 소프트웨어 설치에 대한 제한 사항

적응형 애플리케이션 제어는 Azure에 있는 VM에서 실행할 수 있는 애플리케이션을 제어하는 데 도움이 되는 Azure Security Center의 솔루션입니다. 이 청사진은 허용된 애플리케이션 세트에 대한 변경 사항을 모니터링하는 Azure Policy 정의를 할당합니다. 소프트웨어 설치에 대한 제한 사항은 소프트웨어 취약성이 도입될 가능성을 줄이는 데 도움이 될 수 있습니다.

- 머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 합니다.

## <a name="a1311-network-controls"></a>A.13.1.1 네트워크 제어

이 청사진은 관대한 규칙을 사용하여 네트워크 보안 그룹을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 네트워크를 관리하고 제어하는 데 도움이 됩니다. 너무 관대한 규칙은 원치 않는 네트워크 액세스를 허용할 수 있으며, 검토를 거쳐야 합니다. 또한 이 청사진은 보호되지 않는 엔드포인트, 애플리케이션 및 스토리지 계정을 모니터링하는 Azure Policy 정의 3개를 할당합니다. 방화벽으로 보호되지 않는 엔드포인트 및 애플리케이션과 액세스가 제한되지 않는 스토리지 계정은 정보 시스템 내에 포함된 정보에 대한 원치 않는 액세스를 허용할 수 있습니다.

- 인터넷 연결 엔드포인트를 통한 액세스를 제한해야 합니다.
- 스토리지 계정은 네트워크 액세스를 제한해야 함

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 정보 전송 정책 및 절차

이 청사진은 스토리지 계정 및 Redis Cache에 대한 보안되지 않은 연결을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 2개를 할당하여 Azure 서비스를 사용한 정보 전송이 안전하게 이루어지도록 하는 데 도움이 됩니다.

- Azure Cache for Redis에 보안 연결만 사용하도록 설정해야 함
- Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

지금까지 ISO 27001 Shared Services 청사진을 살펴보았으므로 이제 다음 문서를 참조하여 아키텍처 및 이 샘플을 배포하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [ISO 27001 Shared Services 청사진 - 개요](./index.md)
> [ISO 27001 Shared Services 청사진 - 배포 단계](./deploy.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기
