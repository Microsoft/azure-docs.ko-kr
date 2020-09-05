---
title: 미디어 청사진 샘플 제어
description: 미디어 청사진 샘플의 제어 매핑입니다. 각 컨트롤은 평가를 지원하는 하나 이상의 Azure 정책에 매핑됩니다.
ms.date: 08/13/2020
ms.topic: sample
ms.openlocfilehash: 4101b9030425c22b8806870d467581521870d639
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208469"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>미디어 청사진 샘플의 제어 매핑

다음 문서에서는 Azure Blueprints 미디어 청사진 샘플이 미디어 제어에 매핑되는 방법에 대해 자세히 설명합니다. 제어에 대한 자세한 내용은 [미디어](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)를 참조하세요.

다음은 **미디어** 제어에 대한 매핑입니다. 특정 컨트롤 매핑으로 바로 점프하려면 오른쪽의 탐색 기능을 사용합니다. 많은 매핑된 컨트롤은 [Azure Policy](../../../policy/overview.md) 이니셔티브를 사용하여 구현됩니다. 전체 이니셔티브를 검토하려면 Azure Portal에서 **정책**을 열고 **정의** 페이지를 선택합니다. 그런 다음, **\[미리 보기\]: 미디어 감사 제어** 기본 제공 정책 이니셔티브를 찾아서 선택합니다.

> [!IMPORTANT]
> 아래의 각 컨트롤은 하나 이상의 [Azure Policy](../../../policy/overview.md) 정의와 연결되어 있습니다. 이러한 정책은 컨트롤을 사용한 [규정 준수 평가](../../../policy/how-to/get-compliance-data.md)에 도움이 될 수 있지만, 컨트롤과 하나 이상의 정책 간에 일대일 또는 완벽한 일치 관계가 없는 경우도 많습니다. 따라서 Azure Policy의 **규정 준수**는 정책 자체만 가리킬 뿐, 컨트롤의 모든 요구 사항을 완벽하게 준수한다는 것은 아닙니다. 또한 규정 준수 표준에는 현재 Azure Policy 정의에서 처리되지 않은 컨트롤이 포함되어 있습니다. 따라서 Azure Policy의 규정 준수는 전반적인 규정 준수 상태를 부분적으로 표시할 뿐입니다. 이 규정 준수 청사진 샘플에 대한 컨트롤과 Azure Policy 정의 간의 연결은 시간이 지남에 따라 변경될 수 있습니다. 변경 기록을 보려면 [GitHub 커밋 기록](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md)을 참조하세요.

## <a name="access-control"></a>Access Control

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1.1 - 루트 액세스 키가 없는지 확인

- \[미리 보기\]: 필수 조건을 배포하여 신뢰할 수 있는 루트에 지정한 인증서가 포함되지 않은 Windows VM 감사

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1.2 - 암호, PIN 및 토큰을 보호해야 함

- \[미리 보기\]: 암호 최소 길이가 14자로 제한되지 않는 Windows VM을 감사하기 위한 필수 조건 배포

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1.8 - 공유 계정 액세스가 금지됨

- Service Bus 네임스페이스에서 RootManageSharedAccessKey를 제외한 모든 인증 규칙을 제거해야 함

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1.9 - 시스템에서 권한 있는 사용자에 대한 액세스를 제한해야 함

- 스토리지 계정에 대한 무제한 네트워크 액세스 감사

### <a name="ac--114--system-must-enforce-access-rights"></a>AC-1.14 - 시스템에서 액세스 권한을 적용해야 함

- \[미리 보기\]: 필수 조건을 배포하여 '사용자 권한 할당'에서 Windows VM 구성 감사

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC-1.15 - 보안 관련 정보 또는 기능에 대한 무단 액세스 방지

- \[미리 보기\]: '보안 옵션 - 시스템 설정'에서 Windows VM 구성의 감사 결과 표시

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 - 의무 분리는 적절한 역할 할당을 통해 적용해야 함

- [미리 보기\]: Kubernetes 서비스에서 RBAC(역할 기반 액세스 제어)를 사용해야 합니다.

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1.40 - 시스템에서 신뢰할 수 있는 네트워크와 신뢰할 수 없는 네트워크를 동시에 연결하지 않는지 확인

- \[미리 보기\]: 필수 조건을 배포하여 '보안 옵션 - 네트워크 액세스'에서 Windows VM 구성 감사

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1.42 및 AC-1.43 - 비직원의 원격 액세스는 특별히 승인된 정보 시스템에만 액세스할 수 있도록 제한해야 함

- \[미리 보기\]: 암호 없이 계정에서 원격으로 연결할 수 있는 Linux VM의 감사 결과를 표시합니다.

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1.50 - 모든 정보 시스템 구성 요소에 대한 보안 관련 이벤트 기록

- Logic Apps의 진단 로그를 사용하도록 설정해야 함

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1.54 - 모든 클라우드 콘솔 사용자가 MFA(다단계 인증)를 사용하도록 설정되었는지 확인

- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 계정 또는 리소스 위반을 방지하려면 쓰기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다.

## <a name="auditing--logging"></a>감사 및 로깅

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2.1 - 성공 및 실패 이벤트를 기록해야 함

- Search Service의 진단 로그를 사용하도록 설정해야 함

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL-2.16 - 네트워크 디바이스/인스턴스(ELB, 웹 애플리케이션 방화벽 등)에서 해당 네트워크 디바이스/인스턴스별로 중요한 보안 이벤트로 분류된 이벤트를 기록해야 함

- \[미리 보기\]: '보안 옵션 - 계정'에서 Windows VM 구성의 감사 결과 표시

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2.17 - 서버/인스턴스에서 해당 서버/인스턴스별로 중요한 보안 이벤트로 분류된 이벤트를 기록해야 함

- \[미리 보기\]: '보안 옵션 - 계정'에서 Windows VM 구성의 감사 결과 표시

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2.19 - 도메인 이벤트에서 도메인 관리 소프트웨어별로 중요하거나 높은 수준의 보안 이벤트로 분류된 이벤트를 기록해야 함

- \[미리 보기\]: '보안 옵션 - 계정'에서 Windows VM 구성의 감사 결과 표시
- \[미리 보기\]: 필수 조건을 배포하여 '보안 옵션 - Microsoft 네트워크 클라이언트'에서 Windows VM 구성 감사

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2.20 - 도메인 이벤트에서 도메인 보안 제어별로 중요한 보안 이벤트로 분류된 이벤트를 기록해야 함

- \[미리 보기\]: '보안 옵션 - 계정'에서 Windows VM 구성의 감사 결과 표시

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2.21 - 도메인 이벤트에서 도메인 로그에 대한 액세스 또는 변경 내용을 기록해야 함

- \[미리 보기\]: '보안 옵션 - 복구 콘솔'에서 Windows VM 구성의 감사 결과 표시

## <a name="cryptographic-controls"></a>암호화 제어

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4.2 - 애플리케이션 및 시스템에서 데이터 보호를 위해 현재 암호화 솔루션을 사용해야 함

- SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.
- 저장 데이터를 보호하고 규정 준수 요구 사항을 충족하려면 투명한 데이터 암호화를 사용하도록 설정해야 합니다.

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4.5 - 디지털 인증서는 승인된 인증 기관에서 서명해야 함

- \[미리 보기\]: 지정한 기간(일) 내에 만료되는 인증서가 포함된 Windows VM의 감사 결과 표시

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4.6 - 디지털 인증서는 사용자 또는 디바이스에 고유하게 할당해야 함

- \[미리 보기\]: 필수 조건을 배포하여 지정한 기간(일) 내에 만료되는 인증서가 포함된 Windows VM 감사

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4.7 - 레코드를 유지하는 기간 동안 레코드를 암호 해독할 수 있도록 암호화 자료를 저장해야 함

- 가상 머신에서 디스크 암호화를 적용해야 합니다.
- 디스크 암호화를 사용하도록 설정하지 않은 VM은 Azure Security Center에서 권장 사항으로 모니터링됩니다.

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4.8 - 비밀 및 프라이빗 키는 안전하게 저장해야 함

- SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.
- 저장 데이터를 보호하고 규정 준수 요구 사항을 충족하려면 투명한 데이터 암호화를 사용하도록 설정해야 합니다.

## <a name="change--config-management"></a>변경 및 구성 관리

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5.2 - 권한 있는 사용자만 시스템에서 승인된 변경 내용을 구현할 수 있음

- 시스템 업데이트를 머신에 설치해야 합니다.
- 서버의 누락된 보안 시스템 업데이트는 Azure Security Center에서 권장 사항으로 모니터링합니다.

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5.12 - 정보 시스템의 완전하고 정확하며 쉽게 사용할 수 있는 최신 기준 구성 유지

- 시스템 업데이트를 머신에 설치해야 합니다.
- 서버의 누락된 보안 시스템 업데이트는 Azure Security Center에서 권장 사항으로 모니터링합니다.

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5.13 - 자동화된 도구를 사용하여 정보 시스템의 기본 구성 유지

- 시스템 업데이트를 머신에 설치해야 합니다.
- 서버의 누락된 보안 시스템 업데이트는 Azure Security Center에서 권장 사항으로 모니터링합니다.

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5.14 - 불필요하거나 안전하지 않은 함수, 포트, 프로토콜 및 서비스 식별 및 사용 안 함

- 네트워크 인터페이스가 IP 전달을 사용하지 않아야 함
- \[미리 보기\]: 가상 머신에서 IP 전달을 사용하지 않도록 설정해야 함

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5.19 - 보안 구성 설정에 대한 변경 모니터링

- 네트워크 보안 그룹에 대한 진단 설정 배포

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5.22 - 승인된 소프트웨어 및 업데이트만 회사 시스템에 설치되어 있는지 확인

- 시스템 업데이트를 머신에 설치해야 합니다.
- 서버의 누락된 보안 시스템 업데이트는 Azure Security Center에서 권장 사항으로 모니터링합니다.

## <a name="identity--authentication"></a>ID 및 인증

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7.1 - 퍼블릭으로 분류되지 않은 정보에 액세스하려면 사용자 계정을 개인에게 고유하게 할당해야 합니다. 계정 ID는 표준화된 논리 형식을 사용하여 구성해야 합니다.

- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.
- 모니터링되지 않는 액세스를 방지하려면 소유자 권한이 있는 외부 계정을 구독에서 제거해야 합니다.

## <a name="network-security"></a>네트워크 보안

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9.2 - 네트워크 디바이스 관리 기능에 대한 액세스는 권한 있는 사용자로 제한됨

- \[미리 보기\]: 필수 조건을 배포하여 '보안 옵션 - 네트워크 액세스'에서 Windows VM 구성 감사

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9.3 - 모든 네트워크 디바이스는 가장 안전한 구성을 사용하여 구성해야 함

- \[미리 보기\]: 필수 조건을 배포하여 '보안 옵션 - 네트워크 액세스'에서 Windows VM 구성 감사

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9.5 - 방화벽을 통한 시스템의 모든 네트워크 연결은 정기적으로 승인 및 감사해야 함

- \[미리 보기\]: 'Windows 방화벽 속성'에서 Windows VM 구성의 감사 결과 표시

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9.7 - 신뢰할 수 있는 네트워크와 신뢰할 수 없거나 공용 네트워크 간의 경계에는 적절한 제어가 있어야 함

- \[미리 보기\]: 필수 조건을 배포하여 'Windows 방화벽 속성'에서 Windows VM 구성 감사

## <a name="security-planning"></a>보안 계획

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11.3 - 발생 확률과 함께 회사 정보 및 콘텐츠의 기밀성, 무결성 또는 가용성에 부정적인 영향을 줄 수 있는 위협을 식별해야 함

- SQL 관리형 인스턴스의 Advanced Data Security 설정에서 Advanced Threat Protection 유형을 '모두'로 설정해야 합니다.

### <a name="security-continuity"></a>보안 연속성

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12.5 - 장기 스토리지의 데이터는 보존 기간 동안 액세스할 수 있어야 하며 미디어 성능 저하 및 기술 변경으로부터 보호해야 합니다.

- 감사 보존 기간(일)을 90일보다 큰 값으로 설정하여 SQL 서버를 구성해야 합니다.
- 감사 보존 기간이 90일 미만으로 구성된 SQL 서버를 감사합니다.

## <a name="system-integrity"></a>시스템 무결성

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14.3 - 권한 있는 직원만 네트워크 및 사용자 활동을 모니터링할 수 있습니다.

- SQL 데이터베이스의 취약성을 수정해야 합니다.
- 취약성 평가 검사 결과 및 데이터베이스 취약성을 해결하는 방법에 대한 권장 사항을 모니터링합니다.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14.4 - 인터넷 연결 시스템에는 침입 탐지가 있어야 함

- SQL 서버에 위협 탐지 배포

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14.13 - 중앙 집중식으로 관리되는 맬웨어 방지 소프트웨어는 회사 전체에서 구현해야 함

- Windows Server용 기본 Microsoft IaaSAntimalware 확장 배포

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14.14 - 맬웨어 방지 소프트웨어는 컴퓨터와 미디어를 최소한 매주 검색해야 함

- Windows Server용 기본 Microsoft IaaSAntimalware 확장 배포

## <a name="vulnerability-management"></a>취약성 관리

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15.4 - 애플리케이션에서 매월 취약성을 검사하는지 확인합니다.

- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- 가상 머신 확장 집합의 OS 취약성을 감사하여 공격으로부터 보호합니다.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15.5 - 취약성을 식별하고, 위협에 페어링하며, 위험을 평가하는지 확인하는지 확인합니다.

- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- 가상 머신 확장 집합의 OS 취약성을 감사하여 공격으로부터 보호합니다.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15.6 - 식별된 취약성이 시간 표시 막대에 따라 상호 합의된 타임라인 내에서 수정되었는지 확인합니다.

- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- 가상 머신 확장 집합의 OS 취약성을 감사하여 공격으로부터 보호합니다.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15.7 - 취약성 관리 시스템의 액세스 및 사용은 권한 있는 직원으로 제한해야 합니다.

- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- 가상 머신 확장 집합의 OS 취약성을 감사하여 공격으로부터 보호합니다.

> [!NOTE]
> 특정 Azure Policy 정의의 가용성은 Azure Government 및 기타 국가 클라우드에 따라 다를 수 있습니다. 

## <a name="next-steps"></a>다음 단계

미디어 청사진 샘플의 제어 매핑을 검토했습니다. 이번에는 다음 문서를 참조하여 이 샘플의 개요 및 배포 방법을 알아보세요.

> [!div class="next step action"]
> [Media 청사진 - 컨트롤 매핑](./control-mapping.md)
> [Media 청사진 - 배포 단계](./deploy.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기
