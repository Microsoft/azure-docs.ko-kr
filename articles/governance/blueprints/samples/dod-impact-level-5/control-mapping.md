---
title: DoD 영향 수준 5 청사진 샘플 컨트롤
description: DoD 영향 수준 5 청사진 샘플의 컨트롤 매핑. 각 컨트롤은 평가를 지원하는 하나 이상의 Azure 정책에 매핑됩니다.
ms.date: 06/30/2020
ms.topic: sample
ms.openlocfilehash: a7d1439a73cf387cee773d558ced50ce1c07f6dc
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87921319"
---
# <a name="control-mapping-of-the-dod-impact-level-5-blueprint-sample"></a>DoD 영향 수준 5 청사진 샘플의 컨트롤 매핑

다음 문서에서는 Azure Blueprints DoD IL5(국방부 영향 수준 5) 청사진 샘플이 DoD 영향 수준 5 컨트롤에 매핑되는 방법을 자세히 설명합니다. 컨트롤에 대한 자세한 내용은 [DoD 클라우드 컴퓨팅 SRG(보안 요구 사항 가이드)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/Cloud_Computing_SRG_v1r3.pdf)를 참조하세요. DISA(방어 정보 시스템 기관)는 DoD 클라우드 컴퓨팅 SRG(보안 요구 사항 가이드)를 개발하고 유지 관리하는 미국 DoD(국방부)의 기관입니다. SRG는 DoD 정보, 시스템 및 애플리케이션을 호스팅하는 CSP(클라우드 서비스 공급자)와 DoD의 클라우드 서비스 사용에 대한 기본 보안 요구 사항을 정의합니다.  

다음 매핑은 **DoD 영향 수준 5** 컨트롤에 대한 것입니다. 특정 컨트롤 매핑으로 바로 점프하려면 오른쪽의 탐색 기능을 사용합니다. 많은 매핑된 컨트롤은 [Azure Policy](../../../policy/overview.md) 이니셔티브를 사용하여 구현됩니다. 전체 이니셔티브를 검토하려면 Azure Portal에서 **정책**을 열고 **정의** 페이지를 선택합니다. 그런 다음, **\[미리 보기\]: DoD 영향 수준 5** 기본 제공 정책 이니셔티브를 찾아 선택합니다.

> [!IMPORTANT]
> 아래의 각 컨트롤은 하나 이상의 [Azure Policy](../../../policy/overview.md) 정의와 연결되어 있습니다. 이러한 정책은 컨트롤을 사용한 [규정 준수 평가](../../../policy/how-to/get-compliance-data.md)에 도움이 될 수 있지만, 컨트롤과 하나 이상의 정책 간에 일대일 또는 완벽한 일치 관계가 없는 경우도 많습니다. 따라서 Azure Policy의 **규정 준수**는 정책 자체만 가리킬 뿐, 컨트롤의 모든 요구 사항을 완벽하게 준수한다는 것은 아닙니다. 또한 규정 준수 표준에는 현재 Azure Policy 정의에서 처리되지 않은 컨트롤이 포함되어 있습니다. 따라서 Azure Policy의 규정 준수는 전반적인 규정 준수 상태를 부분적으로 표시할 뿐입니다. 이 규정 준수 청사진 샘플에 대한 컨트롤과 Azure Policy 정의 간의 연결은 시간이 지남에 따라 변경될 수 있습니다.
> 변경 기록을 보려면 [GitHub 커밋 기록](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/dod-impact-level-5/control-mapping.md)을 참조하세요.

## <a name="ac-2-account-management"></a>AC-2 계정 관리

이 청사진은 조직의 계정 관리 요구 사항을 준수하지 않는 계정을 검토하는 데 도움이 됩니다. 이 청사진은 구독 및 사용되지 않는 계정에 대한 읽기, 쓰기 및 소유자 권한으로 외부 계정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 이러한 정책에 의해 감사된 계정을 검토하면 계정 관리 요구 사항이 충족되도록 적절한 조치를 취할 수 있습니다.

- 더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.
- 읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.
- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) 계정 관리 | 역할 기반 체계

Azure는 Azure의 리소스에 액세스할 수 있는 사용자를 관리하는 데 도움이 되는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../../../role-based-access-control/overview.md)를 구현합니다. Azure Portal을 사용하여 Azure 리소스에 대한 액세스 권한이 있는 사용자 및 해당 사용자의 권한을 검토할 수 있습니다. 또한 이 청사진은 SQL Server 및 Service Fabric에 대한 Azure Active Directory 인증의 사용을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. Azure Active Directory 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다. 또한 이 청사진은 사용자 지정 RBAC 규칙의 사용을 감사하는 Azure Policy 정의를 할당합니다. 사용자 지정 RBAC 규칙은 오류가 발생할 가능성이 높기 때문에 사용자 지정 RBAC 규칙이 구현되는 경우를 이해하면 어떤 구현이 필요하고 적절한지 확인하는 데 도움이 될 수 있습니다.

- SQL Server에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.
- 사용자 지정 RBAC 규칙의 사용 감사
- Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 합니다.

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) 계정 관리 | 계정 모니터링/비정상적 사용

JIT(Just-In-Time) 가상 머신 액세스를 사용하면 Azure 가상 머신으로의 인바운드 트래픽이 잠기기 때문에, 필요할 때 VM에 쉽게 연결할 수 있으면서도 공격 노출을 줄일 수 있습니다. JIT의 가상 머신 액세스 요청이 모두 활동 로그에 기록되므로 비정상적인 사용을 모니터링할 수 있습니다. 이 청사진은 JIT 액세스를 지원할 수 있지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함

## <a name="ac-4-information-flow-enforcement"></a>AC-4 정보 흐름 적용

CORS(교차 원본 리소스 공유)를 사용하면 App Services 리소스를 외부 도메인에서 요청할 수 있습니다. 필요한 도메인만 API, 함수 및 웹 애플리케이션과 상호 작용할 수 있도록 허용하는 것이 좋습니다. 이 청사진은 Azure Security Center에서 CORS 리소스 액세스 제한을 모니터링할 수 있는 [Azure Policy](../../../policy/overview.md) 정책을 할당합니다. CORS 구현을 이해하면 정보 흐름 제어가 구현되었는지 확인하는 데 도움이 됩니다.

- CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용하지 않아야 합니다.

## <a name="ac-5-separation-of-duties"></a>AC-5 의무 분리

Azure 구독 소유자가 한 명만 있으면 관리 중복이 허용되지 않습니다. 반대로, Azure 구독 소유자가 너무 많으면 손상된 소유자 계정을 통한 위반 가능성이 증가할 수 있습니다. 이 청사진을 사용하면 Azure 구독의 소유자 수를 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 적절한 수의 Azure 구독 소유자를 유지 관리할 수 있습니다. 또한 이 청사진은 Windows 가상 머신에서 관리자 그룹의 멤버 자격을 제어할 수 있는 Azure Policy 정의를 할당합니다. 구독 소유자 및 가상 머신 관리자 권한을 관리하면 적절한 임무 분리를 구현하는 데 도움이 될 수 있습니다.

- 구독에 최대 3명의 소유자를 지정해야 합니다.
- 관리자 그룹에 지정한 구성원이 포함된 Windows VM의 감사 결과 표시
- 지정한 멤버 중 일부가 관리자 그룹에 없는 Windows VM의 감사 결과를 표시합니다.
- 관리자 그룹에 지정한 구성원이 포함된 Windows VM을 감사하기 위한 필수 구성 요소 배포
- 지정한 멤버 중 일부가 관리자 그룹에 없는 Windows VM을 감사하기 위한 필수 조건을 배포합니다.
- 구독에 둘 이상의 소유자를 할당해야 합니다.

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) 최소 권한 | 사용자 권한 검토

Azure는 Azure의 리소스에 액세스할 수 있는 사용자를 관리하는 데 도움이 되는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../../../role-based-access-control/overview.md)를 구현합니다. Azure Portal을 사용하여 Azure 리소스에 대한 액세스 권한이 있는 사용자 및 해당 사용자의 권한을 검토할 수 있습니다. 이 청사진은 검토를 위해 우선 순위를 지정해야 하는 감사 계정에 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 이러한 계정 지표는 최소 권한 컨트롤이 구현되었는지 확인하는 데 도움이 될 수 있습니다.

- 구독에 최대 3명의 소유자를 지정해야 합니다.
- 관리자 그룹에 지정한 구성원이 포함된 Windows VM의 감사 결과 표시
- 지정한 멤버 중 일부가 관리자 그룹에 없는 Windows VM의 감사 결과를 표시합니다.
- 관리자 그룹에 지정한 구성원이 포함된 Windows VM을 감사하기 위한 필수 구성 요소 배포
- 지정한 멤버 중 일부가 관리자 그룹에 없는 Windows VM을 감사하기 위한 필수 조건을 배포합니다.
- 구독에 둘 이상의 소유자를 할당해야 합니다.

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) 원격 액세스 | 자동화된 모니터링/제어

이 청사진을 사용하면 Azure App Service 애플리케이션의 원격 디버깅이 해제되어 있는지 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의 및 암호 없이 계정에서 원격 연결을 허용하는 Linux 가상 머신을 감사하는 정책 정의를 할당하여 원격 액세스를 모니터링하고 제어할 수 있습니다. 또한 이 청사진은 스토리지 계정에 제한 없이 액세스할 수 있는 Azure Policy 정의를 할당합니다. 이러한 지표를 모니터링하면 원격 액세스 방법이 보안 정책을 준수하는 데 도움이 될 수 있습니다.

- 암호 없이 계정에서 원격으로 연결할 수 있는 Linux VM의 감사 결과를 표시합니다.
- 암호 없이 계정에서 원격으로 연결할 수 있는 Linux VM을 감사하기 위한 필수 조건을 배포합니다.
- 스토리지 계정은 네트워크 액세스를 제한해야 함
- API Apps에 대해 원격 디버깅을 해제해야 합니다.
- 함수 앱에 대해 원격 디버깅을 해제해야 합니다.
- 웹 애플리케이션에 대해 원격 디버깅을 해제해야 합니다.

## <a name="ac-23-data-mining"></a>AC-23 데이터 마이닝

이 청사진은 SQL Server에 감사 및 Advanced Data Security가 구성되도록 합니다.

- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.
- SQL Managed Instance에서 Advanced Data Security를 사용하도록 설정해야 함
- SQL Server에 대한 감사가 사용되도록 설정되어야 함

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) 감사 레코드의 콘텐츠 | 계획된 감사 레코드 콘텐츠의 중앙 집중식 관리

Azure Monitor를 통해 수집되는 로그 데이터는 Log Analytics 작업 영역에 저장되므로 중앙 집중식 구성 및 관리가 가능합니다. 이 청사진을 사용하면 Azure 가상 머신에서 Log Analytics 에이전트의 배포를 감사하고 적용하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 이벤트가 기록되도록 할 수 있습니다.

- \[미리 보기\]: Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- 가상 머신 확장 집합에서 Log Analytics 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨
- VM에 대한 Log Analytics 작업 영역 감사 - 불일치 보고
- Virtual Machine Scale Sets에 Log Analytics 에이전트를 설치해야 합니다.
- 가상 머신에 Log Analytics 에이전트를 설치해야 합니다.

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 감사 처리 실패에 대한 응답

이 청사진은 감사 및 이벤트 로깅 구성을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 이러한 구성을 모니터링하면 감사 시스템 오류 또는 구성 오류 지표를 제공하고 정정 작업을 수행하는 데 도움이 될 수 있습니다.

- 진단 설정 감사
- SQL Server에 대한 감사가 사용되도록 설정되어야 함
- SQL Managed Instance에서 Advanced Data Security를 사용하도록 설정해야 함
- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) 감사 검토, 분석 및 보고 | 중앙 검토 및 분석

Azure Monitor를 통해 수집되는 로그 데이터는 Log Analytics 작업 영역에 저장되므로 중앙 집중식 보고 및 분석이 가능합니다. 이 청사진을 사용하면 Azure 가상 머신에서 Log Analytics 에이전트의 배포를 감사하고 적용하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 이벤트가 기록되도록 할 수 있습니다.

- \[미리 보기\]: Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- 가상 머신 확장 집합에서 Log Analytics 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨
- VM에 대한 Log Analytics 작업 영역 감사 - 불일치 보고

## <a name="au-6-5-audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>AU-6 (5) 감사 검토, 분석 및 보고 | 통합/검색 및 모니터링 기능

이 청사진은 가상 머신, 가상 머신 확장 집합, SQL Database 서버 및 SQL Managed Instance 서버에 대한 취약성 평가를 분석하여 레코드를 감사하는 정책 정의를 제공합니다. 또한 이러한 정책은 진단 로그 구성을 감사하여 Azure 리소스 내에서 수행되는 작업에 대한 인사이트를 제공합니다. 이러한 인사이트는 배포된 리소스의 보안 상태에 대한 실시간 정보를 제공하며, 업데이트 관리 작업의 우선 순위를 지정하는 데 도움이 될 수 있습니다. 자세한 취약성 검색 및 모니터링을 위해 Azure Sentinel 및 Azure Security Center를 활용하는 것이 좋습니다.

- 진단 설정 감사
- SQL Managed Instance에서 취약성 평가를 사용하도록 설정해야 함
- SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.
- 머신 보안 구성의 취약성을 수정해야 합니다.
- SQL 데이터베이스의 취약성을 수정해야 합니다.
- 취약성 평가 솔루션으로 취약성을 수정해야 합니다.
- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- \[미리 보기\]: Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- 가상 머신 확장 집합에서 Log Analytics 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨

## <a name="au-12-audit-generation"></a>AU-12 감사 생성

이 청사진은 Azure 가상 머신에 대한 Log Analytics 에이전트의 배포와 다른 Azure 리소스 종류의 감사 설정 구성을 감사하고 적용하는 정책 정의를 제공합니다.
또한 이러한 정책은 진단 로그 구성을 감사하여 Azure 리소스 내에서 수행되는 작업에 대한 인사이트를 제공합니다. 뿐만 아니라 SQL 서버에 감사 및 Advanced Data Security가 구성됩니다.

- \[미리 보기\]: Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- 가상 머신 확장 집합에서 Log Analytics 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨
- VM에 대한 Log Analytics 작업 영역 감사 - 불일치 보고
- 진단 설정 감사
- SQL Server에 대한 감사가 사용되도록 설정되어야 함
- SQL Managed Instance에서 Advanced Data Security를 사용하도록 설정해야 함
- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.

## <a name="au-12-01-audit-generation--system-wide--time-correlated-audit-trail"></a>AU-12 (01) 감사 생성 | 시스템 수준/시간 관련 감사 내역

이 청사진을 사용하면 Azure 리소스에 대한 로그 설정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 시스템 이벤트가 기록되도록 할 수 있습니다.
이 기본 제공 정책은 진단 설정이 사용되도록 설정되었는지 여부를 확인하기 위해 리소스 형식의 배열을 지정해야 합니다.

- 진단 설정 감사

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) 최소한의 기능 | 프로그램 실행 금지

Azure Security Center의 적응형 애플리케이션 제어는 특정 소프트웨어가 가상 머신에서 실행되지 않도록 차단하거나 방지할 수 있는 지능적이고 자동화된 엔드투엔드 애플리케이션 허용 목록 솔루션입니다. 애플리케이션 제어는 승인되지 않은 애플리케이션의 실행을 금지하는 적용 모드에서 실행할 수 있습니다. 이 청사진은 애플리케이션 허용 목록을 사용하는 것이 좋지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 Azure Policy 정의를 할당합니다.

- 머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 합니다.

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) 최소한의 기능 | 권한이 부여된 소프트웨어/허용 목록

Azure Security Center의 적응형 애플리케이션 제어는 특정 소프트웨어가 가상 머신에서 실행되지 않도록 차단하거나 방지할 수 있는 지능적이고 자동화된 엔드투엔드 애플리케이션 허용 목록 솔루션입니다. 애플리케이션 제어를 사용하면 가상 머신에 사용하도록 승인인 애플리케이션 목록을 만들 수 있습니다. 이 청사진은 애플리케이션 wallow 목록을 사용하는 것이 좋지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 합니다.

## <a name="cm-11-user-installed-software"></a>CM-11 사용자가 설치한 소프트웨어

Azure Security Center의 적응형 애플리케이션 제어는 특정 소프트웨어가 가상 머신에서 실행되지 않도록 차단하거나 방지할 수 있는 지능적이고 자동화된 엔드투엔드 애플리케이션 허용 목록 솔루션입니다. 애플리케이션 제어를 사용하면 소프트웨어 제한 정책을 적용하고 규정 준수 여부를 모니터링할 수 있습니다. 이 청사진은 애플리케이션 허용 목록을 사용하는 것이 좋지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 합니다.

## <a name="cp-7-alternate-processing-site"></a>CP-7 대체 처리 사이트

Azure Site Recovery는 가상 머신에서 실행되는 워크로드를 기본 위치에서 보조 위치로 복제합니다. 기본 사이트가 중단되는 경우 워크로드가 보조 위치로 장애 조치(failover)됩니다. 이 청사진은 재해 복구를 구성하지 않고 가상 머신을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 이 지표를 모니터링하면 필요한 비상 제어를 준비할 수 있습니다.

- 재해 복구를 구성하지 않고 가상 머신 감사

## <a name="cp-9-05--information-system-backup--transfer-to-alternate-storage-site"></a>CP-9 (05) 정보 시스템 백업 | 대체 스토리지 사이트로 전송

이 청사진은 조직의 시스템 백업 정보를 대체 스토리지 사이트로 전자적으로 감사하는 Azure Policy 정의를 할당합니다. 스토리지 메타데이터를 물리적으로 배송하려면 Azure Data Box를 사용하는 것이 좋습니다.

- Storage 계정에 대해 지역 중복 스토리지를 사용하도록 설정해야 함
- Azure Database for PostgreSQL에 대해 지역 중복 백업을 사용하도록 설정해야 함
- Azure Database for MySQL에 대해 지역 중복 백업을 사용하도록 설정해야 함
- Azure SQL Database에 대해 장기 지역 중복 백업을 사용하도록 설정해야 함

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) 식별 및 인증(조직 사용자) | 네트워크를 통해 권한 있는 계정에 액세스

이 청사진을 사용하면 소유자 및/또는 쓰기 권한이 있지만 다단계 인증을 사용하도록 설정되지 않은 계정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 권한 있는 액세스를 제한하고 제어할 수 있습니다. 다단계 인증은 인증 정보 중 한 부분이 손상되더라도 계정을 안전하게 유지하는 데 도움이 됩니다. 다단계 인증을 사용하지 않고 계정을 모니터링하면 손상될 가능성이 더 높은 계정을 식별할 수 있습니다.

- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) 식별 및 인증(조직 사용자) | 권한 없는 계정에 대한 네트워크 액세스

이 청사진은 읽기 권한이 있지만 다단계 인증을 사용하지 않는 계정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 액세스 권한을 제한 및 제어할 수 있습니다. 다단계 인증은 인증 정보 중 한 부분이 손상되더라도 계정을 안전하게 유지하는 데 도움이 됩니다. 다단계 인증을 사용하지 않고 계정을 모니터링하면 손상될 가능성이 더 높은 계정을 식별할 수 있습니다.

- 구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

## <a name="ia-5-authenticator-management"></a>IA-5 인증자 관리

이 청사진은 암호 없이 계정으로부터의 원격 연결을 허용하거나 passwd 파일에 대한 권한이 잘못 설정된 Linux 가상 머신을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 또한 이 청사진은 Windows 가상 머신에 대한 암호 암호화 유형 구성을 감사하는 정책 정의를 할당합니다. 이러한 지표를 모니터링하면 시스템 인증자가 조직의 식별 및 인증 정책을 준수하는 데 도움이 됩니다.

- passwd 파일 권한이 0644로 설정되지 않은 Linux VM의 감사 결과 표시
- 암호 없는 계정이 있는 Linux VM의 감사 결과 표시
- 해독 가능한 암호화를 사용하여 암호를 저장하지 않는 Windows VM의 감사 결과 표시
- 필수 구성 요소를 배포하여 암호 파일 권한이 0644로 설정되지 않은 Linux VM 감사
- 암호 없는 계정이 있는 Linux VM을 감사하기 위한 필수 조건 배포
- 해독 가능한 암호화를 사용하여 암호를 저장하지 않는 Windows VM을 감사하기 위한 필수 조건 배포

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) 인증자 관리 | 암호 기반 인증

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

## <a name="ir-6-2-incident-reporting--vulnerabilities-related-to-incidents"></a>IR-6 (2) 인시던트 보고 | 인시던트 관련 취약성

이 청사진은 가상 머신, 가상 머신 확장 집합 및 SQL 서버에 대한 취약성 평가를 분석하여 레코드를 감사하는 정책 정의를 제공합니다. 이러한 인사이트는 배포된 리소스의 보안 상태에 대한 실시간 정보를 제공하며, 업데이트 관리 작업의 우선 순위를 지정하는 데 도움이 될 수 있습니다.

- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- 취약성 평가 솔루션으로 취약성을 수정해야 합니다.
- 머신 보안 구성의 취약성을 수정해야 합니다.
- 컨테이너 보안 구성의 취약성을 수정해야 합니다.
- SQL 데이터베이스의 취약성을 수정해야 합니다.

## <a name="ra-5-vulnerability-scanning"></a>RA-5 취약성 검색

이 청사진을 사용하면 Azure Security Center에서 운영 체제 취약성, SQL 취약성 및 가상 머신 취약성을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 정보 시스템 취약성을 관리할 수 있습니다. Azure Security Center는 배포된 Azure 리소스의 보안 상태에 대한 실시간 인사이트를 가질 수 있도록 하는 보고 기능을 제공합니다. 또한 이 청사진은 SQL 서버에서 Advanced Data Security를 감사하고 적용하는 정책 정의를 할당합니다. Advanced Data Security는 배포된 리소스의 취약성을 이해할 수 있는 취약성 평가 및 고급 위협 보호 기능이 포함되어 있습니다.

- SQL Managed Instance에서 Advanced Data Security를 사용하도록 설정해야 함
- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.
- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- 머신 보안 구성의 취약성을 수정해야 합니다.
- SQL 데이터베이스의 취약성을 수정해야 합니다.
- 취약성 평가 솔루션으로 취약성을 수정해야 합니다.

## <a name="sc-5-denial-of-service-protection"></a>SC-5 서비스 거부 방지

Azure의 DDoS(분산 서비스 거부) 표준 계층은 기본 서비스 계층보다 강력한 추가 기능 및 위험 완화 기능을 제공합니다. 이러한 추가 기능으로는 Azure Monitor 통합과 공격 후 완화 보고서를 검토하는 기능이 포함됩니다. 이 청사진은 DDoS 표준 계층을 사용하는지 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 서비스 계층 간의 기능 차이를 이해하면 해당 Azure 환경의 서비스 거부를 방지하는 최적의 솔루션을 선택할 수 있습니다.

- Azure DDoS Protection 표준을 사용하도록 설정해야 함

## <a name="sc-7-boundary-protection"></a>SC-7 경계 보호

이 청사진을 사용하면 Azure Security Center에서 네트워크 보안 그룹 강화 추천 사항을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 시스템 경계를 관리하고 제어할 수 있습니다. Azure Security Center는 인터넷 연결 가상 머신의 트래픽 패턴을 분석하여 공격 노출 영역을 줄일 수 있는 네트워크 보안 그룹 규칙 추천 사항을 제공합니다.
또한 이 청사진은 보호되지 않은 엔드포인트, 애플리케이션 및 스토리지 계정을 모니터링하는 정책 정의를 할당합니다. 방화벽으로 보호되지 않는 엔드포인트 및 애플리케이션과 액세스가 제한되지 않는 스토리지 계정은 정보 시스템 내에 포함된 정보에 대한 원치 않는 액세스를 허용할 수 있습니다.

- 인터넷 연결 엔드포인트를 통한 액세스를 제한해야 합니다.
- 스토리지 계정은 네트워크 액세스를 제한해야 함

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) 경계 보호 | 액세스 지점

JIT(Just-In-Time) 가상 머신 액세스를 사용하면 Azure 가상 머신으로의 인바운드 트래픽이 잠기기 때문에, 필요할 때 VM에 쉽게 연결할 수 있으면서도 공격 노출을 줄일 수 있습니다. JIT 가상 머신 액세스를 사용하면 Azure 리소스에 대한 외부 연결 수를 제한할 수 있습니다. 이 청사진은 JIT 액세스를 지원할 수 있지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) 경계 보호 | 외부 원격 통신 서비스

JIT(Just-In-Time) 가상 머신 액세스를 사용하면 Azure 가상 머신으로의 인바운드 트래픽이 잠기기 때문에, 필요할 때 VM에 쉽게 연결할 수 있으면서도 공격 노출을 줄일 수 있습니다. JIT 가상 머신 액세스를 사용하면 액세스 요청 및 승인 프로세스를 촉진하여 트래픽 흐름 정책 예외를 관리할 수 있습니다. 이 청사진은 JIT 액세스를 지원할 수 있지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 가상 머신에서 Just-In-Time 네트워크 액세스 제어를 적용해야 합니다.

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) 전송 기밀성 및 무결성 | 암호화 또는 대체 물리적 보호

이 청사진을 사용하면 통신 프로토콜에 구현된 암호화 메커니즘을 모니터링하는 데 도움이 되는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 전송된 정보의 기밀성과 무결성을 보호할 수 있습니다. 통신이 제대로 암호화되면 조직의 요구 사항을 충족시키거나 무단 공개 및 수정으로부터 정보를 보호하는 데 도움이 될 수 있습니다.

- API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.
- 보안 통신 프로토콜을 사용하지 않는 Windows 웹 서버의 감사 결과 표시
- 보안 통신 프로토콜을 사용하지 않는 Windows 웹 서버를 감사하기 위한 필수 조건 배포
- 함수 앱에 HTTPS를 통해서만 액세스 가능
- Azure Cache for Redis에 보안 연결만 사용하도록 설정해야 함
- Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.
- 웹 애플리케이션에 HTTPS를 통해서만 액세스 가능

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) 저장 정보 보호 | 암호화 보호

이 청사진을 사용하면 특정 암호화 컨트롤을 적용하고 취약한 암호 설정의 사용을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 암호화 컨트롤을 통해 저장 정보를 보호하는 정책을 적용할 수 있습니다. Azure 리소스의 암호화 구성이 최적이 아닐 수 있는 경우를 이해하면 리소스가 정보 보안 정책에 따라 구성되도록 정정 작업을 수행하는 데 도움이 될 수 있습니다. 특히 이 청사진이 할당하는 정책 정의는 데이터 레이크 스토리지 계정을 암호화하고, SQL 데이터베이스의 데이터를 투명하게 암호화하고, SQL 데이터베이스, 가상 머신 디스크 및 자동화 계정 변수에서 누락된 암호화를 감사할 것을 요구합니다.

- SQL Managed Instance에서 Advanced Data Security를 사용하도록 설정해야 함
- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.
- 가상 머신에서 디스크 암호화를 적용해야 합니다.
- SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.

## <a name="si-2-flaw-remediation"></a>SI-2 결함 수정

이 청사진을 사용하면 Azure Security Center에서 누락된 시스템 업데이트, 운영 체제 취약성, SQL 취약성 및 가상 머신 취약성을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 정보 시스템 결함을 관리할 수 있습니다. Azure Security Center는 배포된 Azure 리소스의 보안 상태에 대한 실시간 인사이트를 가질 수 있도록 하는 보고 기능을 제공합니다. 또한 이 청사진은 가상 머신 확장 집합용 운영 체제 패치를 보장하는 정책 정의를 할당합니다.

- 가상 머신 확장 집합에 대한 시스템 업데이트를 설치해야 합니다.
- 시스템 업데이트를 머신에 설치해야 합니다.
- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- 머신 보안 구성의 취약성을 수정해야 합니다.
- SQL 데이터베이스의 취약성을 수정해야 합니다.
- 취약성 평가 솔루션으로 취약성을 수정해야 합니다.

## <a name="si-02-06-flaw-remediation--removal-of-previous-versions-of-software--firmware"></a>SI-02 (06) 결함 수정 | 이전 버전의 소프트웨어/펌웨어 제거

이 청사진은 애플리케이션에서 최신 버전의 .NET Framework, HTTP, Java, PHP, Python, TLS를 사용하도록 보장하는 데 유용한 정책 정의를 할당합니다. 또한 이 청사진은 Kubernetes Services가 취약하지 않은 버전으로 업그레이드되도록 하는 정책 정의를 할당합니다.

- API 앱의 일부로 사용되는 경우 '.NET Framework' 버전이 최신 상태인지 확인합니다.
- 함수 앱의 일부로 사용되는 경우 '.NET Framework' 버전이 최신 상태인지 확인합니다.
- 웹앱의 일부로 사용되는 경우 '.NET Framework' 버전이 최신 상태인지 확인합니다.
- API 앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인합니다.
- 함수 앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인합니다.
- 웹앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인합니다.
- API 앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.
- 함수 앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.
- 웹앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.
- API 앱의 일부로 사용되는 경우 최신의 'PHP 버전'인지 확인합니다.
- 함수 앱의 일부로 사용되는 경우 최신의 'PHP 버전'인지 확인합니다.
- 웹앱의 일부로 사용되는 경우 최신의 'PHP 버전'인지 확인합니다.
- API 앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.
- 함수 앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.
- 웹앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.
- API 앱에서 최신 TLS 버전을 사용해야 합니다.
- 함수 앱에서 최신 TLS 버전을 사용해야 합니다.
- 웹앱에서 최신 TLS 버전을 사용해야 합니다.
- Kubernetes Services를 취약하지 않은 Kubernetes 버전으로 업그레이드해야 함

## <a name="si-3-malicious-code-protection"></a>SI-3 악성 코드 방지

이 청사진을 사용하면 Azure Security Center에서 가상 머신의 누락된 엔드포인트 보호를 모니터링하고 Microsoft 맬웨어 방지 솔루션을 Windows 가상 머신에 적용하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 악성 코드 방지를 포함하여 엔드포인트 보호를 관리할 수 있습니다.

- 가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.
- Azure Security Center에서 누락된 Endpoint Protection 모니터링
- Microsoft IaaSAntimalware 확장을 Windows Server에 배포해야 합니다.

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) 악성 코드 방지 | 중앙 관리

이 청사진을 사용하면 Azure Security Center에서 가상 머신의 누락된 엔드포인트 보호를 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 악성 코드 방지를 포함하여 엔드포인트 보호를 관리할 수 있습니다. Azure Security Center는 배포된 Azure 리소스의 보안 상태에 대한 실시간 인사이트를 얻을 수 있는 중앙 집중식 관리 및 보고 기능을 제공합니다.

- 가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.
- Azure Security Center에서 누락된 Endpoint Protection 모니터링

## <a name="si-4-information-system-monitoring"></a>SI-4 정보 시스템 모니터링

이 청사진은 Azure 리소스에 로깅 및 데이터 보안을 적용하고 감사하여 시스템 모니터링을 도와줍니다. 특히, 이 정책은 Log Analytics 에이전트 배포를 감사 및 적용하고 SQL 데이터베이스, 스토리지 계정 및 네트워크 리소스의 보안 설정을 강화합니다. 이러한 기능은 비정상적인 동작과 공격 징후를 감지하여 적절한 조치를 취하도록 도와줍니다.

- \[미리 보기\]: Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- 가상 머신 확장 집합에서 Log Analytics 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨
- VM에 대한 Log Analytics 작업 영역 감사 - 불일치 보고
- SQL Managed Instance에서 Advanced Data Security를 사용하도록 설정해야 함
- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.
- Network Watcher를 사용하도록 설정해야 합니다.

## <a name="si-4-12-information-system-monitoring--automated-alerts"></a>SI-4 (12) 정보 시스템 모니터링 | 자동화된 경고

이 청사진은 데이터 보안 알림이 제대로 사용되도록 설정하는 데 유용한 정책 정의를 제공합니다. 또한 이 청사진은 Azure Security Center에 표준 가격 책정 계층이 사용되도록 합니다. 표준 가격 책정 계층은 Azure Security Center에서 네트워크 및 가상 머신에 대한 위협을 탐지하고 위협 인텔리전스, 변칙 검색 및 동작 분석을 제공합니다.

- 심각도가 높은 경고에 대해 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.
- 구독에 대한 보안 연락처 이메일 주소를 제공해야 합니다.
- 구독에 대한 보안 연락처 전화 번호를 제공해야 합니다.

> [!NOTE]
> 특정 Azure Policy 정의의 가용성은 Azure Government 및 기타 국가 클라우드에 따라 다를 수 있습니다. 

## <a name="next-steps"></a>다음 단계

지금까지 DoD 영향 수준 5 청사진의 컨트롤 매핑을 살펴보았으므로 이제 다음 문서를 참조하여 청사진 및 이 샘플을 배포하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [DoD 영향 수준 5 청사진 - 개요](./index.md)
> [DoD 영향 수준 5 청사진 - 배포 단계](./deploy.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기