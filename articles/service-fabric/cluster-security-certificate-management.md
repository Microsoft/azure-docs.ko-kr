---
title: Service Fabric 클러스터의 인증서 관리
description: X.509 인증서를 사용 하 여 보호 되는 Service Fabric 클러스터에서 인증서를 관리 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev
ms.openlocfilehash: aba681157d71f94914462b8d9fc13b90d4d6b153
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653667"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Service Fabric 클러스터의 인증서 관리

이 문서에서는 Azure Service Fabric 클러스터의 통신을 보호 하는 데 사용 되는 인증서의 관리 측면에 대해 설명 하 고 [Service Fabric의 x.509 인증서 기반 인증](cluster-security-certificates.md)에 대 한 설명 [Service Fabric 클러스터 보안](service-fabric-cluster-security.md) 소개를 보완 합니다. 독자가 기본적인 보안 개념 뿐만 아니라 클러스터의 보안을 구성 하기 위해 Service Fabric 노출 하는 컨트롤에 대해 잘 알고 있다고 가정 합니다.  

이 제목에서 다루는 요소:

* ' 인증서 관리 '는 정확히 무엇 인가요?
* 인증서 관리에 포함 된 역할 및 엔터티
* 인증서의 여행
* 예제에 대 한 심층 살펴보기
* 문제 해결 및 질문과 대답

하지만 첫 번째 부인 정보:이 문서는 서비스, 기술 등을 비롯 한 실습 예제를 통해 이론상 측의 쌍을 연결 하려고 시도 합니다. 대상 그룹의 많은 부분이 Microsoft 내부에 있기 때문에 Microsoft Azure 관련 된 서비스, 기술 및 제품을 참조 합니다. 사용자의 경우에는 Microsoft 관련 세부 정보가 적용 되지 않는 설명 또는 지침에 대 한 의견 섹션을 자유롭게 확인 하세요.

## <a name="defining-certificate-management"></a>인증서 관리 정의
[도우미 문서](cluster-security-certificates.md)에서 보았듯이 인증서는 기본적으로 비대칭 키 쌍을 나타내는 엔터티를 설명 하는 특성을 사용 하 여 비대칭 키 쌍을 바인딩하는 암호화 개체입니다. 그러나이는 ' perishable ' 개체 이기도 합니다. 즉, 수명이 유한 하 고 공격에 취약 하거나 공격에 취약 한 경우 보안 관점에서 쓸모 없는 인증서를 렌더링할 수 있습니다. 이는 정기적으로 또는 보안 인시던트에 대 한 응답으로 인증서를 변경 해야 하는 경우를 의미 합니다. 관리의 다른 측면 (및 자체의 전체 토픽)은 인증서 개인 키의 보호 또는 확보 및 프로 비전 인증서와 관련 된 엔터티의 id를 보호 하는 비밀입니다. 인증서를 가져오는 데 사용 되는 프로세스와 절차를 참조 하 고 ' 인증서 관리 '로 필요한 곳에 안전 하 게 전송 합니다. 등록, 정책 설정 및 권한 부여 컨트롤과 같은 일부 관리 작업은이 문서의 범위를 벗어났습니다. 프로 비전, 갱신, 다시 키 지정 또는 해지와 같은 다른 사용자는 Service Fabric와 관련 된 것입니다. 그럼에도 불구 하 고 이러한 작업을 통해 한 클러스터를 적절 하 게 보호 하는 데 도움이 될 수 있으므로 여기에서 일정 수준으로 해결할 수 있습니다. 

목표는 프로세스를 사용자에 게 무료로 제공 하는 경우 클러스터의 중단 된 가용성을 보장 하 고 보안 보증을 제공 하기 위해 가능한 한 많은 인증서 관리를 자동화 하는 것입니다. 이 목표는 현재 Azure Service Fabric 클러스터에 realistic 됩니다. 이 문서의 나머지 부분에서는 인증서 관리를 먼저 분해할 나중에 autorollover를 사용 하도록 설정 하는 방법을 집중적으로 설명 합니다.

특히 범위의 항목은 다음과 같습니다.
  - 인증서 관리의 컨텍스트에서 소유자와 플랫폼 간 attributions의 분리와 관련 된 가정
  - 발급에서 사용에 이르기까지 긴 인증서 파이프라인
  - 인증서 순환-이유, 방법 및 시기
  - 어떤 문제가 발생 했을 수 있나요?

도메인 이름 보호/관리, 인증서에 등록 또는 인증서 발급을 적용 하도록 권한 부여 컨트롤 설정 등의 측면은이 문서의 범위를 벗어나는 것입니다. 선호 하는 PKI (공개 키 인프라) 서비스의 RA (등록 기관)를 참조 하세요. Microsoft 내부 소비자: Azure 보안에 연결 하세요.

## <a name="roles-and-entities-involved-in-certificate-management"></a>인증서 관리에 포함 된 역할 및 엔터티
Service Fabric 클러스터의 보안 방식은 "클러스터 소유자가이를 선언 하 고 Service Fabric 런타임은이를 적용" 하는 경우입니다. 이를 통해 클러스터의 기능에 참여 하는 id의 인증서, 키 또는 기타 자격 증명은 서비스 자체에서 제공 되지 않습니다. 모두 클러스터 소유자에 의해 선언 됩니다. 또한 클러스터 소유자는 클러스터에 인증서를 프로 비전 하 고, 필요에 따라 갱신 하 고, 항상 인증서의 보안을 보장 합니다. 구체적으로 말하면 클러스터 소유자는 다음을 확인 해야 합니다.
  - 클러스터 매니페스트의 NodeType 섹션에 선언 된 인증서는 [프레젠테이션 규칙](cluster-security-certificates.md#presentation-rules) 에 따라 해당 유형의 각 노드에서 찾을 수 있습니다.
  - 위에 선언 된 인증서는 해당 개인 키를 포함 하 여 설치 됩니다.
  - 프레젠테이션 규칙에 선언 된 인증서는 [유효성 검사 규칙](cluster-security-certificates.md#validation-rules) 을 통과 해야 합니다. 

해당 부분에 대 한 Service Fabric은의 책임이 있다고 가정 합니다.
  - 클러스터 정의의 선언과 일치 하는 인증서 찾기/찾기  
  - ' 필요 '로 Service Fabric 제어 되는 엔터티를 위해 해당 개인 키에 대 한 액세스 권한 부여
  - 설정 된 보안 모범 사례 및 클러스터 정의에 따라 엄격한 인증서 유효성 검사
  - 임박한 인증서 만료 시 경고 발생 또는 인증서 유효성 검사의 기본 단계를 수행 하는 데 실패
  - 호스트의 기본 구성에서 클러스터 정의의 인증서 관련 측면을 충족 하는지 확인 하는 중 (어느 정도) 

인증서 관리 부담 (활성 작업)은 클러스터 소유자 에게만 속합니다. 다음 섹션에서는 사용 가능한 메커니즘과 클러스터에 미치는 영향에 대 한 각 관리 작업에 대해 자세히 살펴보겠습니다.

## <a name="the-journey-of-a-certificate"></a>인증서의 여행
발급에서 Service Fabric 클러스터의 컨텍스트에서 사용에 대 한 인증서의 진행 상황을 신속 하 게 다시 확인할 수 있습니다.

  1. 도메인 소유자는 PKI의 RA에 결과 certificate와 연결할 도메인 또는 주체를 등록 합니다. 그러면 인증서가 도메인 또는 주체의 소유권 증명을 구성 합니다.
  2. 또한 도메인 소유자는 인증 된 요청자의 id 인 RA에서 지정 된 도메인 또는 주체를 사용 하 여 인증서 등록을 요청할 수 있는 엔터티를 지정 합니다. Microsoft Azure에서 기본 id 공급자는 Azure Active Directory 되며 권한 있는 요청자는 해당 AAD id (또는 보안 그룹을 통해)로 지정 됩니다.
  3. 그런 다음 인증 된 요청자는 비밀 관리 서비스를 통해 인증서에 등록 합니다. Microsoft Azure에서 선택한 SMS는 안전 하 게 저장 되 고 권한 있는 엔터티에의 한 암호/인증서 검색을 허용 하는 Azure Key Vault (AKV)입니다. 또한 AKV는 연결 된 인증서 정책에 구성 된 대로 인증서를 갱신/다시 키 합니다. (AKV는 id 공급자로 AAD를 사용 합니다.)
  4. ' 프로 비전 에이전트 ' 라고 하는 권한 있는 검색기-자격 증명 모음에서 개인 키를 포함 하 여 인증서를 검색 하 고 클러스터를 호스트 하는 컴퓨터에 설치 합니다.
  5. 각 노드에서 높은 권한으로 실행 되는 Service Fabric 서비스는 허용 된 Service Fabric 엔터티에 대 한 액세스 권한을 부여 합니다. 로컬 그룹에 의해 지정 되 고 ServiceFabricAdministrators와 ServiceFabricAllowedUsers 간에 분할 됩니다.
  6. Service Fabric 런타임은 인증서를 액세스 하 고 사용 하 여 페더레이션을 설정 하거나 인증 된 클라이언트에서 인바운드 요청을 인증 합니다.
  7. 프로 비전 에이전트는 자격 증명 모음 인증서를 모니터링 하 고 갱신 검색 시 프로 비전 흐름을 트리거합니다. 그런 다음 클러스터 소유자는 필요한 경우 클러스터 정의를 업데이트 하 여 인증서를 롤아웃할 의도를 표시 합니다.
  8. 프로 비전 에이전트 또는 클러스터 소유자는 사용 하지 않는 인증서를 정리/삭제 해야 합니다.
  
이를 위해 위의 시퀀스에서 처음 두 단계는 거의 관련이 없습니다. 유일한 연결은 인증서의 주체 일반 이름이 클러스터 정의에 선언 된 DNS 이름입니다.

이러한 단계는 아래에 나와 있습니다. 지문 및 일반 이름으로 선언 된 인증서 간 프로 비전의 차이점을 확인 합니다.

*그림 1.* 지문에 의해 선언 된 인증서의 발급 및 프로비저닝 흐름입니다.
![지 문으로 선언 된 인증서 프로 비전][Image1]

*그림 2.* 주체 일반 이름으로 선언 된 인증서의 발급 및 프로비저닝 흐름입니다.
![주체 일반 이름으로 선언 된 인증서 프로 비전][Image2]

### <a name="certificate-enrollment"></a> 인증서 등록
이 항목은 Key Vault [설명서](../key-vault/certificates/create-certificate.md)에 자세히 설명 되어 있습니다. 연속성 및 간편한 참조를 위해 여기에 개요를 포함 하 고 있습니다. Azure를 컨텍스트로 계속 사용 하 고, Azure Key Vault을 비밀 관리 서비스로 사용 하 여, 권한 있는 인증서 요청자는 자격 증명 모음 소유자가 부여 하는 자격 증명 모음에 대해 적어도 인증서 관리 권한을 가져야 합니다. 그러면 요청자는 다음과 같이 인증서에 등록 합니다.
    - 인증서의 도메인/주체, 원하는 발급자, 키 유형과 길이, 원하는 키 사용 등을 지정 하는 Azure Key Vault (AKV)에서 인증서 정책을 만듭니다. 자세한 내용은 [Azure Key Vault의 인증서를](../key-vault/certificates/certificate-scenarios.md) 참조 하세요. 
    - 위에 지정 된 정책을 사용 하 여 동일한 자격 증명 모음에 인증서를 만듭니다. 이를 통해 자격 증명 모음 개체로 키 쌍을 생성 하 고, 개인 키로 서명 된 인증서 서명 요청을 생성 하 고, 서명 하기 위해 지정 된 발급자에 게 전달 됩니다.
    - 발급자 (인증 기관)가 서명 된 인증서를 사용 하 여 회신 하면 결과가 자격 증명 모음에 병합 되 고 인증서를 다음 작업에 사용할 수 있습니다.
      - {vaultUri}/certificates/{name}: 공개 키와 메타 데이터를 포함 하는 인증서
      - {vaultUri}/keys/{name}: 인증서의 개인 키를 암호화 작업에 사용할 수 있습니다 (래핑/래핑 해제, 서명/확인).
      - {vaultUri}/secrets/{name}: 개인 키가 포함 된 인증서를 보호 되지 않는 pfx 또는 pem 파일로 다운로드 하는 데 사용할 수 있습니다.  
    자격 증명 모음 인증서는 실제로 정책을 공유 하는 시간순으로 인증서 인스턴스입니다. 인증서 버전은 정책의 수명 및 갱신 특성에 따라 만들어집니다. 자격 증명 모음 인증서는 주체 또는 도메인/DNS 이름을 공유 하지 않는 것이 좋습니다. 클러스터에서 서로 다른 자격 증명 모음 인증서의 인증서 인스턴스를 프로 비전 하는 데 방해가 될 수 있습니다. 단, 발급자, 키 사용 등과 같은 다른 특성은 동일 합니다.

이 시점에서 인증서가 자격 증명 모음에 있으므로 사용할 준비가 됩니다. 이후:

### <a name="certificate-provisioning"></a>인증서 프로 비전
자격 증명 모음에서 개인 키가 포함 된 인증서를 검색 하 여 클러스터의 각 호스트에 설치 하는 엔터티인 ' 프로 비전 에이전트 '를 언급 했습니다. (Service Fabric는 인증서를 프로 비전 하지 않습니다.) 이 컨텍스트에서 클러스터는 Azure Vm 및/또는 가상 머신 확장 집합의 컬렉션에서 호스팅됩니다. Azure에서 다음과 같은 메커니즘을 사용 하 여 자격 증명 모음에서 v m/VMSS로 인증서를 프로 비전 할 수 있습니다 .이는 프로 비전 에이전트가 자격 증명 모음 소유자에 의해 자격 증명 모음에 대 한 ' get ' 권한을 이전에 부여 한 것으로 가정 합니다. 
  - 임시: 운영자가 자격 증명 모음에서 인증서를 검색 하 고 (pfx/PKCS #12 또는 pem) 각 노드에 설치 합니다.
  - 배포 하는 동안 가상 머신 확장 집합 ' 암호 '로: 계산 서비스는 운영자 대신 자사 id를 사용 하 여 템플릿 배포에 사용 되는 자격 증명 모음의 인증서를 검색 하 여 가상 머신 확장 집합의 각 노드에 설치 합니다 ([예](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates):). 참고로, 버전이 지정 된 암호를 프로 비전 할 수 있습니다.
  - [KEY VAULT VM 확장](../virtual-machines/extensions/key-vault-windows.md)사용 이를 통해 관찰 된 인증서를 정기적으로 새로 고쳐 버전이 없는 선언을 사용 하 여 인증서를 프로 비전 할 수 있습니다. 이 경우 VM/VMSS에는 관찰 된 인증서를 포함 하는 자격 증명 모음에 대 한 액세스 권한이 부여 된 id 인 [관리 id](../virtual-machines/security-policy.md#managed-identities-for-azure-resources)가 있어야 합니다.

보안에서 가용성에 이르는 여러 가지 이유로 애드혹 메커니즘을 사용 하지 않는 것이 좋습니다 .이에 대해서는 여기서 설명 하지 않습니다. 자세한 내용은 [가상 머신 확장 집합의 인증서](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates)를 참조 하세요.

VMSS-/Compute-based 프로 비전은 보안 및 가용성 이점을 제공 하지만 제한도 제공 합니다. 인증서를 버전이 지정 된 암호로 사용 해야 합니다. 그러면 인증서가 지 문으로 선언 된 인증서로 보호 된 클러스터에만 적합 합니다. 이와 대조적으로 Key Vault VM 확장 기반 프로 비전은 항상 관찰 된 각 인증서의 최신 버전을 설치 합니다. 그러면 주체 일반 이름으로 선언 된 인증서로 보호 된 클러스터에만 적합 합니다. 예를 들어, 인스턴스에 의해 선언 된 인증서 (즉, 지문)에는 autorefresh 프로 비전 메커니즘 (예: KVVM 확장)을 사용 하지 마세요. 가용성이 손실 될 위험이 있습니다.

다른 프로 비전 메커니즘이 있을 수 있습니다. 위의 사항은 현재 Azure Service Fabric 클러스터에 대해 허용 됩니다.

### <a name="certificate-consumption-and-monitoring"></a>인증서 소비 및 모니터링
앞서 설명한 것 처럼 Service Fabric 런타임은 클러스터 정의에 선언 된 인증서를 찾고 사용 하는 일을 담당 합니다. [인증서 기반 인증](cluster-security-certificates.md) 에 대 한 문서는 각각 프레젠테이션 및 유효성 검사 규칙을 구현 하는 방법에 대해 자세히 설명 하 고 Service Fabric. 액세스 및 권한 부여 및 모니터링을 살펴보겠습니다.

Service Fabric의 인증서는 페더레이션 계층의 상호 인증에서 관리 끝점에 대 한 TLS 인증까지 다양 한 용도로 사용 됩니다. 이렇게 하려면 다양 한 구성 요소 또는 시스템 서비스에서 인증서의 개인 키에 액세스할 수 있어야 합니다. Service Fabric 런타임은 인증서 저장소를 정기적으로 검색 하 여 알려진 각 표시 규칙에 대해 일치 하는 항목을 찾습니다. 일치 하는 각 인증서에 대해 해당 하는 개인 키가 있으며, 임의 액세스 제어 목록이 사용 권한을 포함 하도록 업데이트 됩니다. 일반적으로이를 필요로 하는 각 id에 대 한 읽기 및 실행 권한이 부여 됩니다. 이 프로세스는 비공식적으로 ' ACLing '로 참조 됩니다. 프로세스는 1 분 주기로 실행 되며 설정 또는 끝점 인증서로 암호화 하는 데 사용 되는 인증서와 같은 ' 응용 프로그램 ' 인증서도 포함 합니다. ACLing는 프레젠테이션 규칙을 따르며, 지문에 의해 선언 된 인증서와 결과 클러스터 구성 업데이트가 없는 autorefreshed에는 액세스할 수 없습니다.    

### <a name="certificate-rotation"></a>인증서 회전
참고로, IETF [RFC 3647](https://tools.ietf.org/html/rfc3647) 은 대체 되는 인증서와 같은 특성을 가진 인증서 발급으로 [갱신](https://tools.ietf.org/html/rfc3647#section-4.4.6) 을 정의 합니다. 즉, 발급자, 주체의 공개 키 및 정보가 유지 되 고, 새 키 쌍을 사용 하 여 인증서 발급으로 키를 [다시](https://tools.ietf.org/html/rfc3647#section-4.4.7) 입력 하 고, 발급자가 변경 될 수 있는지 여부에 대 한 제한은 없습니다. 이러한 구분이 중요할 수 있는 경우 (발급자 고정을 사용 하 여 주체 일반 이름으로 선언 된 인증서의 경우) 두 시나리오를 모두 포함 하기 위해 중립 용어 ' 회전 '을 선택할 수 있습니다. 비공식적으로 사용 하는 경우 ' 갱신 '을 통해 다시 키를 다시 지정 하는 것을 염두에 두어야 합니다. 

이전에는 자동 인증서 회전을 지 원하는 Azure Key Vault를 살펴보았습니다. 인증서 연결 정책은 자격 증명 모음에서 인증서를 회전할 때 만료 전 일 수 또는 총 수명의 백분율에 따라 지정 시간을 정의 합니다. 프로 비전 에이전트는이 시점 이후에 호출 되어야 하며, 현재-이전 인증서가 만료 되기 전에이 새 인증서를 클러스터의 모든 노드에 배포 해야 합니다. Service Fabric는 인증서의 만료 날짜 (현재 클러스터에서 사용 중)가 미리 결정 된 간격 보다 빨리 발생 하는 경우 상태 경고를 발생 시킬 수 있습니다. 자격 증명 모음 인증서를 관찰 하도록 구성 된 자동 프로 비전 에이전트 (즉, KeyVault VM 확장)는 주기적으로 자격 증명 모음을 폴링하여 순환을 검색 하 고 새 인증서를 검색 및 설치 합니다. VM/VMSS ' 비밀 ' 기능을 통해 수행 된 프로 비전에는 새 인증서에 해당 하는 버전 지정 된 KeyVault URI를 사용 하 여 v m/VMSS를 업데이트 하는 권한 있는 운영자가 필요 합니다.

두 경우 모두, 회전 된 인증서가 모든 노드에 프로 비전 되 고 회전을 검색 하는 데 사용할 Service Fabric 메커니즘에 대해 설명 했습니다. 다음에 발생 하는 상황을 살펴보겠습니다 .이는 주체 일반 이름으로 선언 된 클러스터 인증서에 회전이 적용 되는 것으로 가정 합니다 (이 문서를 작성할 때 모두 적용 가능 및 Service Fabric 런타임 버전 7.1.409).
  - 클러스터 뿐만 아니라 내에서의 새 연결에 대 한 Service Fabric 런타임은 가장 오래 된 만료 날짜가 있는 일치 하는 인증서를 찾고 선택 합니다 (인증서의 ' NotAfter ' 속성, 종종 ' na '로 축약 됨).
  - 기존 연결은 활성 상태로 유지 되 고, 자연스럽 게 만료 되거나 종료 될 수 있습니다. 내부 처리기에 새 일치 항목이 있다는 알림이 표시 됩니다.

이는 다음과 같은 중요 한 관찰으로 변환 됩니다.
  - 만료 날짜가 현재 사용 중인 인증서 보다 빠른 경우 갱신 인증서를 무시할 수 있습니다.
  - 클러스터 또는 호스팅된 응용 프로그램의 가용성은 지시문 보다 우선 하 여 인증서를 회전 합니다. 클러스터는 결국 새로운 인증서를 수렴 하지만 타이밍을 보장 하지는 않습니다. 라디안은 다음과 같이 표시됩니다.
  - 순환 되는 인증서가 해당 선행 작업을 완전히 대체 했음을 관찰자에 게 명확 하 게 알 수는 없습니다. 가 (클러스터 인증서의 경우)를 확인 하 여 호스트 컴퓨터를 다시 부팅 하는 유일한 방법입니다. 클러스터의 임대 연결을 구성 하는 커널 모드 구성 요소는 영향을 받지 않으므로 Service Fabric 노드를 다시 시작 하는 것 만으로는 충분 하지 않습니다. 또한 VM/VMSS를 다시 시작 하면 일시적으로 가용성이 손실 될 수 있습니다. 응용 프로그램 인증서의 경우 해당 응용 프로그램 인스턴스만 다시 시작 해도 됩니다.
  - 유효성 검사 규칙을 충족 하지 않는 키 입력 인증서를 도입 하면 효과적으로 클러스터를 중단할 수 있습니다. 이에 대 한 가장 일반적인 예는 예기치 않은 발급자의 경우입니다. 클러스터 인증서는 발급자 고정을 사용 하 여 주체 일반 이름으로 선언 되지만, 회전 된 인증서는 새/선언 되지 않은 발급자가 발급 한 것입니다.     

### <a name="certificate-cleanup"></a>인증서 정리
이번에는 인증서를 명시적으로 제거 하기 위해 Azure에 프로 비전이 없습니다. 지정 된 시간에 지정 된 인증서가 사용 되 고 있는지 여부를 확인 하는 것은 일반적으로 중요 하지 않은 작업입니다. 이는 클러스터 인증서 보다 응용 프로그램 인증서에 대해 더 어렵습니다. Service Fabric 자체는 프로 비전 에이전트가 아니라 어떤 경우에도 사용자가 선언한 인증서를 삭제 하지 않습니다. 표준 프로 비전 메커니즘의 경우:
  - Vm/vmss 비밀으로 선언 된 인증서는 VM/VMSS 정의에서 참조 되는 한 프로 비전 되 고 자격 증명 모음에서 검색할 수 있습니다. 즉, 자격 증명 모음 비밀/인증서를 삭제 하면 후속 VM/VMSS 배포가 실패 합니다. 마찬가지로 자격 증명 모음에서 암호 버전을 사용 하지 않도록 설정 하면 해당 비밀 버전을 참조 하는 v m/VMSS 배포도 실패
  - KeyVault VM 확장을 통해 프로 비전 된 인증서의 이전 버전은 v m/VMSS 노드에 있을 수도 있고 없을 수도 있습니다. 에이전트는 현재 버전만 검색 하 고 설치 하며 인증서를 제거 하지 않습니다. 이미지로 다시 설치 노드 (일반적으로 매월 발생)는 인증서 저장소를 OS 이미지의 콘텐츠로 다시 설정 하므로 이전 버전이 암시적으로 제거 됩니다. 그러나 가상 머신 확장 집합을 확장 하면 현재 버전의 관찰 된 인증서만 설치 되는 것을 고려할 수 있습니다. 설치 된 인증서와 관련 하 여 노드 균질 가정 하지 마십시오.   

## <a name="simplifying-management---an-autorollover-example"></a>관리 간소화-autorollover 예제
이에 대 한 메커니즘, 제한 사항, 복잡 한 규칙 및 정의에 대해 설명 하 고 중단 디렉터리 예측 했습니다. 이는 이러한 모든 문제를 방지 하기 위해 자동 인증서 관리를 설정 하는 방법을 보여 주는 것일 수 있습니다. PaaSv2 가상 머신 확장 집합에서 실행 되는 Azure Service Fabric 클러스터의 컨텍스트에서 다음과 같이 비밀 관리를 위해 Azure Key Vault를 사용 하 고 관리 되는 id를 활용 하는 것입니다.
  - 인증서 유효성 검사는 지문 고정에서 제목 + 발급자 고정으로 변경 됩니다. 지정 된 발급자의 지정 된 주체를 사용 하는 인증서는 동일 하 게 신뢰할 수 있습니다.
    - 인증서는 신뢰할 수 있는 저장소 (Key Vault)에서 등록 되 고 에이전트로 새로 고쳐집니다 .이 경우에는 KeyVault VM 확장입니다.
    - 인증서 프로 비전은 배포 시간 및 버전 기반 (ComputeRP에서 수행 됨)에서 배포 후 및 버전 감소 키 자격 증명 모음 Uri를 사용 하 여 변경 됩니다.
    - 사용자 할당 관리 id를 통해 KeyVault에 대 한 액세스 권한이 부여 됩니다. UA id는 배포 중에 생성 되 고 가상 머신 확장 집합에 할당 됩니다.
    - 배포 후 에이전트 (KV VM 확장)는 가상 머신 확장 집합의 각 노드에서 관찰 된 인증서를 폴링하고 새로 고칩니다. 따라서 SF에서 가장 멀리 떨어진 올바른 인증서를 자동으로 선택 하기 때문에 인증서 회전이 완전히 자동화 됩니다.

이 시퀀스는 완전히 스크립트 가능 하 고 자동으로 제공 되며, 인증서 autorollover으로 구성 된 클러스터의 사용자 터치 무료 초기 배포를 허용 합니다. 자세한 단계는 아래에 나와 있습니다. PowerShell cmdlet과 json 템플릿 조각을 함께 사용 합니다. Azure와 상호 작용 하는 모든 지원 되는 방법을 사용 하 여 동일한 기능을 달성할 수 있습니다.

[!NOTE] 이 예에서는 인증서가 자격 증명 모음에 이미 있는 것으로 가정 합니다. 키 자격 증명 모음 관리 인증서를 등록 하 고 갱신 하려면이 문서의 앞부분에 설명 된 필수 구성 요소 수동 단계가 필요 합니다. 프로덕션 환경의 경우에는 KeyVault 관리 인증서를 사용 합니다. Microsoft 내부 PKI와 관련 된 샘플 스크립트는 아래에 포함 되어 있습니다.
인증서 autorollover은 CA에서 발급 한 인증서에만 적합 합니다. Azure Portal에서 Service Fabric 클러스터를 배포할 때 생성 된 인증서를 포함 하 여 자체 서명 된 인증서를 사용 하는 것은 무의미 이지만, 발급자 지문을 리프 인증서와 동일 하 게 선언 하 여 로컬/개발자 호스트 배포에 대해서도 가능 합니다.

### <a name="starting-point"></a>시작점
간단 하 게 하기 위해 다음 시작 상태를 가정 합니다.
  - Service Fabric 클러스터가 있고, 지 문으로 선언 된 CA 발급 인증서로 보안이 유지 됩니다.
  - 인증서는 자격 증명 모음에 저장 되 고 가상 머신 확장 집합 암호로 프로 비전 됩니다.
  - 동일한 인증서를 사용 하 여 클러스터를 일반 이름 기반 인증서 선언으로 변환 하 고 그에 따라 주체와 발급자의 유효성을 검사할 수 있습니다. 그렇지 않은 경우 [여기](service-fabric-cluster-security-update-certs-azure.md) 에 설명 된 대로이 목적을 위해 발급 된 CA 발급 인증서를 획득 하 고 지문을 사용 하 여 클러스터 정의에 추가 합니다.

다음은 이러한 상태에 해당 하는 템플릿 으로부터 발췌 한 json입니다. 참고이는 많은 필수 설정이 생략 되었으며 인증서 관련 측면도 보여 줍니다.
```json
  "resources": [
    {   ## VMSS definition
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeTypeName')]",
      "location": "[variables('computeLocation')]",
      "properties": {
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
            {
                "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeTypeName')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "certificate": {
                        "thumbprint": "[parameters('primaryClusterCertificateTP')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.1"
                }
            },}},
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
                },
                "vaultCertificates": [
                {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
                },
            ]}]
        },
    },
    {   ## cluster definition
        "apiVersion": "[variables('sfrpApiVersion')]",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "certificate": {
            "thumbprint": "[parameters('primaryClusterCertificateTP')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
    }
  ]
```   

위의 예에서는 지문이 있는 인증서 ```json [parameters('primaryClusterCertificateTP')] ``` 와 KeyVault URI에 있는 인증서 ```json [parameters('clusterCertificateUrlValue')] ``` 가 손도장에 의해 클러스터의 유일한 인증서로 선언 됨을 언급 했습니다. 다음으로 인증서의 autorollover을 확인 하는 데 필요한 추가 리소스를 설정 합니다.

### <a name="setting-up-prerequisite-resources"></a>필수 구성 요소 리소스 설정
앞서 설명한 것 처럼 가상 머신 확장 집합 비밀으로 프로 비전 된 인증서는 자사 자사 id를 사용 하 고 배포 운영자를 대신 하 여 Microsoft Compute 리소스 공급자 서비스에 의해 자격 증명 모음에서 검색 됩니다. Autorollover의 경우 변경 됩니다. 관리 되는 id를 사용 하 여 가상 머신 확장 집합에 할당 되 고 자격 증명 모음 비밀에 대 한 사용 권한이 부여 되는 것으로 전환 됩니다.

모든 후속 인용은 concomitantly를 배포 해야 합니다. 즉, 재생 후 분석 및 설명을 위해 개별적으로 나열 됩니다.

먼저 사용자 할당 id를 정의 합니다 (기본값은 예제로 포함 됨). 최신 정보는 [공식 설명서](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity) 를 참조 하세요.
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "userAssignedIdentityName": {
      "type": "string",
      "defaultValue": "sftstuaicus",
      "metadata": {
        "description": "User-assigned managed identity name"
      }
    },
  },
  "variables": {
      "vmssApiVersion": "2018-06-01",
      "sfrpApiVersion": "2018-02-01",
      "miApiVersion": "2018-11-30",
      "kvApiVersion": "2018-02-14",
      "userAssignedIdentityResourceId": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
  },    
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('userAssignedIdentityName')]",
      "apiVersion": "[variables('miApiVersion')]",
      "location": "[resourceGroup().location]"
    },
  ]}
```

그런 다음 자격 증명 모음 비밀에 대 한이 id 액세스를 부여 합니다. 현재 정보는 [공식 설명서](/rest/api/keyvault/vaults/updateaccesspolicy) 를 참조 하세요.
```json
  "resources":
  [{
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "[variables('kvApiVersion')]",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).tenantId]",
            "objectId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).principalId]",
            "dependsOn": [
              "[variables('userAssignedIdentityResourceId')]"
            ],
            "permissions": {
              "secrets": [
                "get",
                "list"
              ]}}]}}]
```

다음 단계에서는 다음 작업을 수행 합니다.
  - 사용자 할당 id를 가상 머신 확장 집합에 할당 합니다.
  - 관리 id를 만들 때와 자격 증명 모음에 대 한 액세스 권한을 부여 하는 결과에 대 한 가상 머신 확장 집합 종속성을 선언 합니다.
  - 시작 시 관찰 된 인증서를 검색 하도록 요구 하는 KeyVault VM 확장을 선언 합니다 ([공식 설명서](../virtual-machines/extensions/key-vault-windows.md)).
  - KVVM 확장에 따라 Service Fabric VM 확장의 정의를 업데이트 하 고 클러스터 인증서를 일반 이름으로 변환 합니다. 즉, 동일한 리소스의 범위에 포함 되기 때문에 단일 단계에서 이러한 변경을 수행 하 게 됩니다.

```json
  "parameters": {
    "kvvmextPollingInterval": {
      "type": "string",
      "defaultValue": "3600",
      "metadata": {
        "description": "kv vm extension polling interval in seconds"
      }
    },
    "kvvmextLocalStoreName": {
      "type": "string",
      "defaultValue": "MY",
      "metadata": {
        "description": "kv vm extension local store name"
      }
    },
    "kvvmextLocalStoreLocation": {
      "type": "string",
      "defaultValue": "LocalMachine",
      "metadata": {
        "description": "kv vm extension local store location"
      }
    },
    "kvvmextObservedCertificates": {
      "type": "array",
      "defaultValue": [
                "https://sftestcus.vault.azure.net/secrets/sftstcncluster",
                "https://sftestcus.vault.azure.net/secrets/sftstcnserver"
            ],
      "metadata": {
        "description": "kv vm extension observed certificates versionless uri"
      }
    },
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
  },
  "resources": [
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[variables('vmNodeTypeName')]",
    "location": "[variables('computeLocation')]",
    "dependsOn": [
      "[variables('userAssignedIdentityResourceId')]",
      "[concat('Microsoft.KeyVault/vaults/', concat(parameters('keyVaultName'), '/accessPolicies/add'))]"
    ],
    "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "[variables('userAssignedIdentityResourceId')]": {}
      }
    },
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
        {
          "name": "KVVMExtension",
          "properties": {
            "publisher": "Microsoft.Azure.KeyVault",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": "[parameters('kvvmextPollingInterval')]",
                    "linkOnRenewal": false,
                    "observedCertificates": "[parameters('kvvmextObservedCertificates')]",
                    "requireInitialSync": true
                }
            }
          }
        },
        {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
        "properties": {
          "type": "ServiceFabricNode",
          "provisionAfterExtensions" : [ "KVVMExtension" ],
          "publisher": "Microsoft.Azure.ServiceFabric",
          "settings": {
            "certificate": {
                "commonNames": [
                    "[parameters('certificateCommonName')]"
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            }
            },
            "typeHandlerVersion": "1.0"
          }
        },
  ] } ## extension profile
  },  ## VM profile
  "osProfile": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "[parameters('adminUsername')]",
  } 
  }
  ]
```
위에 명시 된 것은 아니지만 가상 머신 확장 집합의 ' OsProfile ' 섹션에서 자격 증명 모음 인증서 URL이 제거 되었습니다.
마지막 단계는 인증서 선언을 지문에서 일반 이름으로 변경 하도록 클러스터 정의를 업데이트 하는 것입니다. 여기에는 발급자 지문이 고정 되어 있습니다.

```json
  "parameters": {
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "defaultValue": "1b45ec255e0668375043ed5fe78a09ff1655844d,d7fe717b5ff3593764f4d90654d86e8362ec26c8,3ac7c3cac8de0dd392c02789c8be97474f456960,96ea05926e2e42cc207e358668be2c316857fb5e",
      "metadata": {
        "description": "Certificate issuer thumbprints separated by comma"
      }
    },
  },
  "resources": [
    {
      "apiVersion": "[variables('sfrpApiVersion')]",
      "type": "Microsoft.ServiceFabric/clusters",
      "name": "[parameters('clusterName')]",
      "location": "[parameters('clusterLocation')]",
      "properties": {
        "certificateCommonNames": {
          "commonNames": [{
              "certificateCommonName": "[parameters('certificateCommonName')]",
              "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
          }],
          "x509StoreName": "[parameters('certificateStoreValue')]"
        },
  ]
```

이 시점에서 위에서 언급 한 업데이트를 단일 배포에 실행할 수 있습니다. 해당 부분에서 Service Fabric 리소스 공급자 서비스는 [클러스터 인증서를 지문에서 일반 이름으로 변환 하](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations)는 세그먼트에 설명 된 대로 여러 단계로 클러스터 업그레이드를 분할 합니다.

### <a name="analysis-and-observations"></a>분석 및 관찰
이 섹션은 위에서 자세히 설명 하는 단계를 설명 하 고 중요 한 측면에 주목 하는 방법을 설명 하기 위한 것입니다.

#### <a name="certificate-provisioning-explained"></a>인증서 프로 비전, 설명
프로 비전 에이전트로 KVVM 확장은 미리 결정 된 빈도로 지속적으로 실행 됩니다. 관찰 된 인증서를 검색 하지 못할 경우 다음 줄에서 계속 진행 하 고 다음 주기까지 최대 절전 모드로 전환 합니다. 클러스터 부트스트랩 에이전트로 사용할 SFVM 확장에는 클러스터를 구성 하기 전에 선언 된 인증서가 필요 합니다. 즉,이는 ```json "provisionAfterExtensions" : [ "KVVMExtension" ]"``` KeyVaultVM 확장의 설정에 따라 여기에 표시 되는 클러스터 인증서를 성공적으로 검색 한 후에도 SFVM 확장이 실행 될 수 있음을 의미 합니다 ```json "requireInitialSync": true``` . 이는 첫 번째 실행 (배포 후 또는 다시 부팅 후)에 대 한 KVVM 확장을 나타내며 모두 성공적으로 다운로드 될 때까지 관찰 된 인증서를 순환 해야 합니다. 이 매개 변수를 false로 설정 하면 클러스터 인증서를 검색 하는 데 실패 하는 것과 함께 클러스터 배포가 실패 합니다. 반대로 관찰 된 인증서의 잘못 된/잘못 된 목록과 초기 동기화를 요구 하면 KVVM 확장 오류가 발생 하 여 클러스터를 배포 하는 데 실패 하 게 됩니다.  

#### <a name="certificate-linking-explained"></a>인증서 연결, 설명
KVVM 확장의 ' linkOnRenewal ' 플래그를 발견 했을 수 있으며 false로 설정 되어 있습니다. 여기서는이 플래그에 의해 제어 되는 동작과 클러스터 기능에 미치는 영향을 자세히 설명 합니다. 참고이 동작은 Windows에만 해당 됩니다.

[정의](../virtual-machines/extensions/key-vault-windows.md#extension-schema)에 따라:
```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

TLS 연결을 설정 하는 데 사용 되는 인증서는 일반적으로 S-채널 보안 지원 공급자를 통해 [핸들로 획득](/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea) 됩니다. 즉, 클라이언트는 인증서 자체의 개인 키에 직접 액세스 하지 않습니다. S-채널은 인증서 확장 ([CERT_RENEWAL_PROP_ID](/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)) 형식으로 자격 증명의 리디렉션 (연결)을 지원 합니다 .이 속성이 설정 된 경우 해당 값은 ' 갱신 ' 인증서의 지문을 나타내며 S-채널은 연결 된 인증서를 대신 로드 하려고 시도 합니다. 실제로는 ' 최종 ' 인증서 (갱신 표시 안 함)를 사용 하 여 종료 될 때까지이 링크 된 (및 비순환) 목록을 트래버스 합니다. 이 기능은 신중 하 게 사용 되는 경우 만료 된 인증서 (예를 들어)로 인해 발생 하는 가용성 손실에 대해 크게 완화 됩니다. 다른 경우에는 진단 및 완화 하기 어려운 중단의 원인일 수 있습니다. S 채널은 주체, 발급자 또는 클라이언트에서 결과 인증서의 유효성 검사에 참여 하는 기타 특정 특성과 관계 없이 갱신 속성의 인증서 트래버스를 실행 합니다. 실제로 결과 인증서에 연결 된 개인 키가 없거나 키가 해당 소비자로 작동 하지 않을 수 있습니다. 
 
연결을 사용 하는 경우 자격 증명 모음에서 관찰 된 인증서를 검색할 때 KeyVault VM 확장에서 일치 하는 기존 인증서를 찾아 갱신 확장 속성을 통해 연결 합니다. 일치는 SAN (주체 대체 이름)을 기반으로 하는 (독점적) 이며 아래 예시로 작동 합니다.
다음과 같이 기존의 두 인증서를 가정 합니다. A: CN = "Alice의 액세서리", SAN = {"alice.universalexports.com"}, 갱신 = ' ' B: CN = "Bob의 bits", SAN = {"bob.universalexports.com", "bob.universalexports.net"}, 갱신 = ' '
 
인증서 C가 KVVM ext: CN = "Mallory 's malware", SAN = {"alice.universalexports.com", "bob.universalexports.com", "mallory.universalexports.com"}에 의해 검색 된다고 가정 합니다.
 
의 SAN 목록은 C의에 완벽 하 게 포함 되어 있으므로 A. 갱신 = C. 지문; B의 SAN 목록은 C와 공통 된 교차를 갖지만,이는 완전히 포함 되지 않으므로 B. 갱신은 비어 있습니다.
 
인증서 A에 대해이 상태에서 AcquireCredentialsHandle (S-channel)을 호출 하려고 하면 실제로 원격 파티에 C를 전송 하 게 됩니다. Service Fabric의 경우 클러스터의 [전송 하위 시스템](service-fabric-architecture.md#transport-subsystem) 은 상호 인증을 위해 S 채널을 사용 하므로 위에 설명 된 동작은 클러스터의 기본 통신에 직접 영향을 줍니다. 위의 예제를 계속 진행 하 고가 클러스터 인증서 라고 가정 하면 다음 작업은 다음에 따라 달라 집니다.
  - 패브릭이 실행 되는 계정에 대 한 C의 개인 키가 없는 경우 개인 키를 가져오는 동안 오류가 표시 됩니다 (SEC_E_UNKNOWN_CREDENTIALS 또는 유사한).
  - C의 개인 키에 액세스할 수 있는 경우 다른 노드에서 반환 된 권한 부여 오류 (CertificateNotMatched, 권한 없음 등)가 표시 됩니다. 
 
두 경우 모두 전송에 실패 하 고 클러스터의 작동이 중단 될 수 있습니다. 증상은 다양 합니다. 이러한 문제를 방지 하기 위해 링크는 갱신 순서에 따라 달라 집니다 .이는 KVVM 확장의 관찰 된 인증서 목록, 자격 증명 모음에서 갱신 일정 또는 검색 순서를 변경 하는 일시적인 오류 목록 순서에 따라 결정 됩니다.

이러한 인시던트를 완화 하기 위해 다음을 수행 하는 것이 좋습니다.
  - 여러 자격 증명 모음 인증서의 San을 혼합 하지 마세요. 각 자격 증명 모음 인증서는 고유한 용도를 제공 해야 하며, 주체와 SAN은 특이성와 함께이를 반영 해야 합니다.
  - SAN 목록에 주체 일반 이름 (as, as, "CN =")을 포함 합니다. <subject common name>  
  - 확실 하지 않은 경우 KVVM 확장을 사용 하 여 프로 비전 된 인증서에 대해 갱신 시 링크 사용 안 함 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>사용자 할당 관리 id를 사용 하는 이유 사용의 의미는 무엇 인가요?
위의 json 코드 조각에서와 같이 작업/업데이트의 특정 시퀀싱은 변환의 성공 여부를 보장 하 고 클러스터의 가용성을 유지 하는 데 필요 합니다. 특히 가상 머신 확장 집합 리소스는 해당 id를 선언 하 고 사용 하 여 단일 (사용자 관점에서) 업데이트에서 비밀을 검색 합니다. 클러스터를 부트스트랩 하는 Service Fabric VM 확장은 관찰 된 인증서의 성공적인 검색에 따라 달라 지는 KeyVault VM 확장이 완료 되는지에 따라 달라 집니다. KVVM 확장은 가상 머신 확장 집합의 id를 사용 하 여 자격 증명 모음에 액세스 합니다. 즉, 가상 머신 확장 집합을 배포 하기 전에 자격 증명 모음에 대 한 액세스 정책이 이미 업데이트 된 것입니다. 

관리 id 생성을 삭제 하거나 다른 리소스에 할당 하려면 배포 운영자에 게 템플릿에서 참조 되는 다른 리소스를 관리 하는 데 필요한 역할 외에도 구독 또는 리소스 그룹에 필요한 역할 (ManagedIdentityOperator)이 있어야 합니다. 

보안 관점에서 볼 때 가상 머신 (확장 집합)는 Azure id와 관련 하 여 보안 경계로 간주 됩니다. 즉, 사용자는 VM에서 호스트 되는 모든 응용 프로그램에서 VM 관리 id 액세스 토큰을 나타내는 액세스 토큰을 가져올 수 있습니다 .이 토큰은 인증 되지 않은 IMDS 끝점에서 가져옵니다. VM을 공유 또는 다중 테 넌 트 환경으로 간주 하는 경우 클러스터 인증서를 검색 하는 방법이 표시 되지 않을 수도 있습니다. 그러나 인증서 autorollover에 적합 한 유일한 프로 비전 메커니즘입니다.

## <a name="troubleshooting-and-frequently-asked-questions"></a>문제 해결 및 질문과 대답

*Q*: 키 자격 증명 모음 관리 인증서를 프로그래밍 방식으로 등록 하는 방법
*A*: 키 자격 증명 모음 설명서에서 발급자의 이름을 찾은 다음 아래 스크립트에서 바꿉니다.  
```PowerShell
  $issuerName=<depends on your PKI of choice>
    $clusterVault="sftestcus"
    $clusterCertVaultName="sftstcncluster"
    $clusterCertCN="cus.cluster.sftstcn.system.servicefabric.azure-int"
    Set-AzKeyVaultCertificateIssuer -VaultName $clusterVault -Name $issuerName -IssuerProvider $issuerName
    $distinguishedName="CN=" + $clusterCertCN
    $policy = New-AzKeyVaultCertificatePolicy `
        -IssuerName $issuerName `
        -SubjectName $distinguishedName `
        -SecretContentType 'application/x-pkcs12' `
        -Ekus "1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2" `
        -ValidityInMonths 4 `
        -KeyType 'RSA' `
        -RenewAtPercentageLifetime 75        
    Add-AzKeyVaultCertificate -VaultName $clusterVault -Name $clusterCertVaultName -CertificatePolicy $policy
    
    # poll the result of the enrollment
    Get-AzKeyVaultCertificateOperation -VaultName $clusterVault -Name $clusterCertVaultName 
```

*Q*: 선언 되지 않은/지정 되지 않은 발급자가 인증서를 발급 하면 어떻게 되나요? 지정 된 PKI에 대 한 활성 발급자의 완전 한 목록을 어디에서 얻을 수 있나요?
*A*: 인증서 선언이 발급자 지문을 지정 하 고 인증서의 직접 발급자가 고정 발급자 목록에 포함 되지 않은 경우 해당 루트를 클라이언트에서 신뢰 하는지 여부에 관계 없이 인증서가 잘못 된 것으로 간주 됩니다. 따라서 발급자 목록이 최신 인지 확인 하는 것이 중요 합니다. 새 발급자를 도입 하는 것은 드물게 발생 하며 인증서를 발급 하기 전에 광범위 하 게 공개 되어야 합니다. 

일반적으로 PKI는 IETF [RFC 7382](https://tools.ietf.org/html/rfc7382)에 따라 인증 사용 약관을 게시 하 고 유지 관리 합니다. 다른 정보 중에 모든 활성 발급자가 포함 됩니다. 이 목록을 프로그래밍 방식으로 검색 하는 것은 PKI와는 다를 수 있습니다.   

Microsoft 내부 Pki의 경우 공인 발급자를 검색 하는 데 사용 되는 끝점/a p i에 대 한 내부 설명서를 참조 하세요. 이 목록을 주기적으로 검색 하 고 해당 클러스터 정의에 예상 되는 *모든* 발급자가 포함 되어 있는지 확인 하는 것은 클러스터 소유자의 책임입니다.

*Q*: 여러 pki가 지원 되나요? 
*A*: 예, 동일한 값을 사용 하 여 클러스터 매니페스트에서 여러 CN 항목을 선언할 수 없지만 동일한 CN에 해당 하는 여러 Pki의 발급자를 나열할 수 있습니다. 권장 되는 방법이 아니라 인증서 투명성 방법으로 인해 이러한 인증서를 발급 하지 못할 수 있습니다. 그러나 한 PKI에서 다른 PKI로 마이그레이션하는 것 처럼이는 허용 가능한 메커니즘입니다.

*Q*: 현재 클러스터 인증서가 CA에서 발급 되지 않았거나 의도 된 주체가 없는 경우는 어떻게 되나요? 
*A*: 원하는 주체를 사용 하 여 인증서를 획득 하 고 지문을 사용 하 여 클러스터 정의에 보조로 추가 합니다. 업그레이드가 성공적으로 완료 되 면 다른 클러스터 구성 업그레이드를 시작 하 여 인증서 선언을 일반 이름으로 변환 합니다. 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png
