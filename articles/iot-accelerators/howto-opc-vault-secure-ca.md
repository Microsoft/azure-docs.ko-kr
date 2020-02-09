---
title: OPC 자격 증명 모음 인증서 관리 서비스를 안전 하 게 실행 하는 방법-Azure | Microsoft Docs
description: Azure에서 OPC 자격 증명 모음 인증서 관리 서비스를 안전 하 게 실행 하 고 고려할 다른 보안 지침을 검토 하는 방법을 설명 합니다.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 88f8188779c5fb6b3cd07c67e9f35a6b8f9ad97d
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200091"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>OPC 자격 증명 모음 인증서 관리 서비스를 안전 하 게 실행

이 문서에서는 Azure에서 OPC 자격 증명 모음 인증서 관리 서비스를 안전 하 게 실행 하는 방법과 고려할 다른 보안 지침을 검토 하는 방법을 설명 합니다.

## <a name="roles"></a>역할

### <a name="trusted-and-authorized-roles"></a>트러스트 된 역할 및 권한이 부여 된 역할

OPC 자격 증명 모음 마이크로 서비스를 사용 하면 고유한 역할이 서비스의 다양 한 부분에 액세스할 수 있습니다.

> [!IMPORTANT]
> 배포 하는 동안 스크립트는 배포 스크립트를 실행 하는 사용자만 모든 역할에 대 한 사용자로 추가 합니다. 프로덕션 배포의 경우이 역할 할당을 검토 하 고 아래 지침에 따라 적절 하 게 다시 구성 해야 합니다. 이 작업을 수행 하려면 Azure Active Directory (Azure AD) 엔터프라이즈 응용 프로그램 포털에서 역할 및 서비스를 수동으로 할당 해야 합니다.

### <a name="certificate-management-service-roles"></a>인증서 관리 서비스 역할

OPC 자격 증명 모음 마이크로 서비스는 다음 역할을 정의 합니다.

- **읽기 권한자**: 기본적으로 테넌트의 모든 인증 된 사용자에 게는 읽기 권한이 있습니다. 
  - 응용 프로그램 및 인증서 요청에 대 한 읽기 액세스입니다. 응용 프로그램 및 인증서 요청을 나열 하 고 쿼리할 수 있습니다. 또한 장치 검색 정보 및 공용 인증서에는 읽기 액세스 권한으로 액세스할 수 있습니다.
- **기록기**: 사용자에 게 작성자 역할을 할당 하 여 특정 작업에 대 한 쓰기 권한을 추가 합니다. 
  - 응용 프로그램 및 인증서 요청에 대 한 읽기/쓰기 액세스입니다. 응용 프로그램을 등록, 업데이트 및 등록 취소할 수 있습니다. 인증서 요청을 만들고 승인 된 개인 키 및 인증서를 가져올 수 있습니다. 개인 키를 삭제할 수도 있습니다.
- **승인자**: 승인자 역할은 사용자에 게 할당 되어 인증서 요청을 승인 하거나 거부 합니다. 역할에는 다른 역할이 포함 되지 않습니다.
  - OPC 자격 증명 모음 마이크로 서비스 API에 액세스 하는 승인자 역할 외에도 사용자는 인증서에 서명할 수 있도록 Azure Key Vault의 키 서명 권한이 있어야 합니다.
  - 기록기 및 승인자 역할을 다른 사용자에 게 할당 해야 합니다.
  - 승인자의 주 역할은 인증서 요청의 생성 및 거부에 대 한 승인입니다.
- **관리자**: 관리자 역할은 인증서 그룹을 관리 하기 위해 사용자에 게 할당 됩니다. 역할은 승인자 역할을 지원 하지 않지만 기록기 역할은 포함 합니다.
  - 관리자는 새 CRL (인증서 해지 목록)을 발급 하 여 인증서 그룹을 관리 하 고, 구성을 변경 하 고, 응용 프로그램 인증서를 해지할 수 있습니다.
  - 작성자, 승인자 및 관리자 역할은 다른 사용자에 게 할당 하는 것이 가장 좋습니다. 추가 보안을 위해 승인자 또는 관리자 역할을 가진 사용자는 인증서를 발급 하거나 발급자 CA 인증서를 갱신 하기 위해 Key Vault에서 키 서명 권한이 필요 합니다.
  - 마이크로 서비스 관리 역할 외에도 역할에는 다음이 포함 되지만이에 국한 되지 않습니다.
    - CA 보안 방법의 구현 관리를 담당 합니다.
    - 인증서 생성, 해지 및 일시 중단 관리 
    - 암호화 키 수명 주기 관리 (예: 발급자 CA 키 갱신)
    - CA를 운영 하는 서비스의 설치, 구성 및 유지 관리
    - 서비스의 일상적인 운영. 
    - CA 및 데이터베이스 백업 및 복구.

### <a name="other-role-assignments"></a>기타 역할 할당

또한 서비스를 실행 하는 경우 다음 역할을 고려 하십시오.

- 외부 루트 인증 기관 (예: 소유자가 외부 ca에서 인증서를 구입 하거나 외부 CA에 종속 된 CA를 운영 하는 경우)을 사용 하는 인증서 조달 계약의 비즈니스 소유자입니다.
- 인증 기관의 개발 및 유효성 검사
- 감사 레코드를 검토 합니다.
- CA를 지원 하거나 물리적 및 클라우드 시설을 관리 하는 데 도움이 되지만 CA 작업을 수행 하기 위해 직접 신뢰 하지 않는 직원은 *권한* 있는 역할에 있습니다. 권한 있는 역할의 담당자를 수행할 수 있는 작업 집합도 문서화 되어야 합니다.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>분기별 신뢰할 수 있고 권한이 부여 된 역할의 구성원 자격 검토

적어도 분기별로 신뢰할 수 있고 권한이 부여 된 역할의 멤버 자격을 검토 합니다. 각 역할의 사용자 집합 (수동 프로세스의 경우) 또는 서비스 id (자동화 된 프로세스의 경우)가 최소한으로 유지 되는지 확인 합니다.

### <a name="role-separation-between-certificate-requester-and-approver"></a>인증서 요청자와 승인자 간의 역할 구분

인증서 발급 프로세스는 인증서 요청자와 인증서 승인자 역할 (사람 또는 자동화 된 시스템) 간의 역할 분리를 적용 해야 합니다. 인증서 발급 자가 인증서를 얻을 수 있는 권한이 있는지 확인 하는 인증서 승인자 역할의 인증을 받아야 합니다. 인증서 승인자 역할을 보유 하는 사람은 공식적으로 권한 있는 사용자 여야 합니다.

### <a name="restrict-assignment-of-privileged-roles"></a>권한 있는 역할의 할당 제한

권한 있는 역할 할당 (예: Administrators 및 승인자 그룹의 멤버 자격 권한 부여)을 제한 된 권한 있는 직원 집합으로 제한 해야 합니다. 모든 권한 있는 역할 변경에는 24 시간 내에 취소 된 액세스 권한이 있어야 합니다. 마지막으로 분기별로 권한 있는 역할 할당을 검토 하 고 불필요 하거나 만료 된 할당을 제거 합니다.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>권한 있는 역할은 2 단계 인증을 사용 해야 합니다.

서비스에 대 한 승인자 및 관리자의 대화형 로그인을 위해 multi-factor authentication (2 단계 인증이 라고도 함)을 사용 합니다.

## <a name="certificate-service-operation-guidelines"></a>인증서 서비스 작업 지침

### <a name="operational-contacts"></a>작업 연락처

인증서 서비스에는 자세한 운영 인시던트 응답 연락처가 포함 된 파일에 최신 보안 응답 계획이 있어야 합니다.

### <a name="security-updates"></a>보안 업데이트

최신 보안 업데이트를 사용 하 여 모든 시스템을 지속적으로 모니터링 하 고 업데이트 해야 합니다.

> [!IMPORTANT]
> OPC 자격 증명 모음 서비스의 GitHub 리포지토리는 보안 패치로 지속적으로 업데이트 됩니다. 이러한 업데이트를 모니터링 하 여 정기적인 간격으로 서비스에 적용 합니다.

### <a name="security-monitoring"></a>보안 모니터링

적절 한 보안 모니터링을 구독 하거나 구현 합니다. 예를 들어 Azure Security Center 또는 Office 365 모니터링 솔루션과 같은 중앙 모니터링 솔루션을 구독 하 고, 보안 이벤트가 모니터링 솔루션에 전송 되도록 적절 하 게 구성 합니다.

> [!IMPORTANT]
> 기본적으로 OPC 자격 증명 모음 서비스는 [Azure 애플리케이션 정보](https://docs.microsoft.com/azure/azure-monitor/app/devops) 를 모니터링 솔루션으로 사용 하 여 배포 됩니다. [Azure Security Center](https://azure.microsoft.com/services/security-center/) 와 같은 보안 솔루션을 추가 하는 것이 좋습니다.

### <a name="assess-the-security-of-open-source-software-components"></a>오픈 소스 소프트웨어 구성 요소의 보안 평가

제품 또는 서비스 내에서 사용 되는 모든 오픈 소스 구성 요소는 보통 또는 그 이상의 보안 취약성을 가져야 합니다.

> [!IMPORTANT]
> 연속 통합 빌드 중에 OPC 자격 증명 모음 서비스의 GitHub 리포지토리는 취약성에 대 한 모든 구성 요소를 검색 합니다. GitHub에서 이러한 업데이트를 모니터링 하 여 정기적인 간격으로 서비스에 적용 합니다.

### <a name="maintain-an-inventory"></a>인벤토리 유지 관리

모든 프로덕션 호스트 (영구 가상 컴퓨터 포함), 장치, 모든 내부 IP 주소 범위, Vip 및 공용 DNS 도메인 이름에 대 한 자산 인벤토리를 유지 관리 합니다. 시스템, 장치 IP 주소, VIP 또는 공용 DNS 도메인을 추가 하거나 제거할 때마다 30 일 이내에 재고를 업데이트 해야 합니다.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>기본 Azure OPC 자격 증명 모음 마이크로 서비스 프로덕션 배포의 인벤토리 

Azure에서:
- **App Service 계획**: 서비스 호스트에 대 한 App service 계획입니다. 기본값은 S1입니다.
- 마이크로 서비스에 대 한 **App Service** : OPC 자격 증명 모음 서비스 호스트입니다.
- 예제 응용 프로그램에 대 한 **App Service** : OPC 자격 증명 모음 샘플 응용 프로그램 호스트입니다.
- **표준 Key Vault**: 웹 서비스에 대 한 암호 및 Azure Cosmos DB 키를 저장 합니다.
- **Key Vault 프리미엄**: 발급자 CA 키, 서명 서비스, 자격 증명 모음 구성 및 응용 프로그램 개인 키의 저장소를 호스트 합니다.
- **Azure Cosmos DB**: 응용 프로그램 및 인증서 요청에 대 한 데이터베이스입니다. 
- **Application Insights**: (선택 사항) 웹 서비스 및 응용 프로그램에 대 한 모니터링 솔루션입니다.
- **AZURE AD 응용 프로그램 등록**: 예제 응용 프로그램, 서비스 및 edge 모듈에 대 한 등록

클라우드 서비스의 경우 서비스를 배포 하는 데 사용 되는 모든 호스트 이름, 리소스 그룹, 리소스 이름, 구독 Id 및 테넌트 Id를 문서화 해야 합니다. 

Azure IoT Edge 또는 로컬 IoT Edge 서버에서 다음을 수행 합니다.
- **OPC 자격 증명 모음 IoT Edge 모듈**: 공장 네트워크 OPC UA 전역 검색 서버를 지원 합니다. 

IoT Edge 장치의 경우 호스트 이름 및 IP 주소를 문서화 해야 합니다. 

### <a name="document-the-certification-authorities-cas"></a>Ca (인증 기관) 문서화

CA 계층 구조 설명서는 모든 운영 Ca를 포함 해야 합니다. 여기에는 서비스에서 관리 하지 않는 경우에도 관련 된 모든 하위 Ca, 부모 Ca 및 루트 Ca가 포함 됩니다. 공식적인 설명서 대신 만료 되지 않은 모든 CA 인증서의 완전 한 집합을 제공할 수 있습니다.

> [!NOTE]
> OPC 자격 증명 모음 샘플 응용 프로그램은 설명서 용 서비스에서 사용 및 생성 된 모든 인증서의 다운로드를 지원 합니다.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>모든 Ca (인증 기관)에 의해 발급 된 인증서 문서화

지난 12 개월 동안 발급 된 모든 인증서의 완전 한 집합을 제공 합니다.

> [!NOTE]
> OPC 자격 증명 모음 샘플 응용 프로그램은 설명서 용 서비스에서 사용 및 생성 된 모든 인증서의 다운로드를 지원 합니다.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>암호화 키를 안전 하 게 삭제 하는 표준 운영 절차 문서화

CA 수명이 지속 되는 동안에는 키 삭제가 드물게 발생할 수 있습니다. 이는 사용자에 게 Key Vault 인증서 삭제 권한이 할당 되지 않은 이유와 발급자 CA 인증서를 삭제 하기 위해 제공 된 Api가 없는 이유입니다. 인증 기관 암호화 키를 안전 하 게 삭제 하는 수동 표준 운영 절차는 Azure Portal Key Vault에 직접 액세스 하는 경우에만 사용할 수 있습니다. Key Vault에서 인증서 그룹을 삭제할 수도 있습니다. 즉시 삭제를 보장 하려면 [Key Vault 일시 삭제](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 기능을 사용 하지 않도록 설정 합니다.

## <a name="certificates"></a>인증서

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>인증서는 최소 인증서 프로필을 준수 해야 합니다.

OPC 자격 증명 모음 서비스는 구독자에 게 최종 엔터티 인증서를 발급 하는 온라인 CA입니다. OPC 자격 증명 모음 마이크로 서비스는 기본 구현에서 다음 지침을 따릅니다.

- 모든 인증서에는 아래에 지정 된 대로 다음 x.509 필드가 포함 되어야 합니다.
  - 버전 필드의 내용은 v3 이어야 합니다. 
  - 일련 번호 필드의 내용에는 FIPS (연방 정보 처리 표준) 140 승인 된 난수 생성기에서 가져온 엔트로피를 8 바이트 이상 포함 해야 합니다.<br>
    > [!IMPORTANT]
    > OPC 자격 증명 모음 일련 번호는 기본적으로 20 바이트 이며 운영 체제의 암호화 난수 생성기에서 가져옵니다. 난수 생성기는 Windows 장치에서 FIPS 140 승인 되지만 Linux에서는 승인 되지 않습니다. 기본 기술 OpenSSL FIPS 140 승인 되지 않은 Linux Vm 또는 Linux docker 컨테이너를 사용 하는 서비스 배포를 선택할 때이 점을 고려해 야 합니다.
  - IssuerUniqueID 및 # Uniqueid 필드는 없어야 합니다.
  - 최종 엔터티 인증서는 IETF RFC 5280에 따라 기본 제약 조건 확장으로 식별 해야 합니다.
  - 발급 CA 인증서에 대해 pathLenConstraint 필드를 0으로 설정 해야 합니다. 
  - 확장 키 사용 확장이 있어야 하 고 확장 키 사용 Oid (개체 식별자)의 최소 집합을 포함 해야 합니다. AnyExtendedKeyUsage OID (2.5.29.37.0)를 지정 하지 않아야 합니다. 
  - CDP (CRL 배포 지점) 확장이 발급자 CA 인증서에 있어야 합니다.<br>
    > [!IMPORTANT]
    > CDP 확장은 OPC 자격 증명 모음 CA 인증서에 있습니다. 그럼에도 불구 하 고 OPC UA 장치는 사용자 지정 메서드를 사용 하 여 Crl을 배포 합니다.
  - 구독자 인증서에는 기관 정보 액세스 확장이 있어야 합니다.<br>
    > [!IMPORTANT]
    > 권한 정보 액세스 확장은 OPC 자격 증명 모음 구독자 인증서에 표시 됩니다. 그럼에도 불구 하 고 OPC UA 장치는 사용자 지정 메서드를 사용 하 여 발급자 CA 정보를 배포 합니다.
- 승인 된 비대칭 알고리즘, 키 길이, 해시 함수 및 패딩 모드를 사용 해야 합니다.
  - RSA 및 s h a-2는 유일 하 게 지원 되는 알고리즘입니다.
  - RSA는 암호화, 키 교환 및 서명에 사용할 수 있습니다.
  - RSA 암호화는 OAEP, RSA-KEM 또는 RSA-PSS 패딩 모드만 사용 해야 합니다.
  - 2048 비트 보다 크거나 같은 키 길이가 필요 합니다.
  - S h a-2의 해시 알고리즘 패밀리 (SHA256, SHA384 및 SHA512)를 사용 합니다.
  - 일반 수명이 20 년 보다 크거나 같은 RSA 루트 CA 키는 4096 비트 이상 이어야 합니다.
  - RSA 발급자 CA 키는 2048 비트 이상 이어야 합니다. CA 인증서 만료 날짜가 2030 이후 이면 CA 키는 4096 비트 이상 이어야 합니다.
- 인증서 수명
  - 루트 CA 인증서: 루트 Ca에 대 한 최대 인증서 유효 기간은 25 년을 초과 해서는 안 됩니다.
  - 하위 CA 또는 온라인 발급자 CA 인증서: 온라인 상태이 고 구독자 인증서만 발급 하는 Ca에 대 한 최대 인증서 유효 기간은 6 년을 초과 해서는 안 됩니다. 이러한 Ca의 경우 새 인증서를 발급 하는 데 3 년 이내에 관련 개인 서명 키를 사용 해서는 안 됩니다.<br>
    > [!IMPORTANT]
    > 발급자 인증서는 외부 루트 CA를 제외 하 고 기본 OPC 자격 증명 모음 마이크로 서비스에서 생성 되는 것과 같이 각 요구 사항과 수명으로 온라인 하위 CA 처럼 처리 됩니다. 기본 수명은 5 년으로 설정 되며 키 길이는 2048 보다 크거나 같습니다.
  - 모든 비대칭 키는 최대 5 년 수명 및 권장 1 년 수명을 가져야 합니다.<br>
    > [!IMPORTANT]
    > 기본적으로 OPC 자격 증명 모음과 함께 발급 된 응용 프로그램 인증서의 수명은 수명이 2 년 이며 매년 바꾸어야 합니다. 
  - 인증서가 갱신 될 때마다 새 키로 갱신 됩니다.
- 응용 프로그램 인스턴스 인증서의 OPC UA 관련 확장
  - SubjectAltName 확장에는 응용 프로그램 Uri와 호스트 이름이 포함 됩니다. FQDN, IPv4 및 IPv6 주소를 포함할 수도 있습니다.
  - KeyUsage에는 digitalSignature, 부인 방지, keyEncipherment 및 dataEncipherment가 포함 됩니다.
  - ExtendedKeyUsage는 serverAuth 및 clientAuth를 포함 합니다.
  - AuthorityKeyIdentifier는 서명 된 인증서에 지정 됩니다.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>CA 키 및 인증서는 최소 요구 사항을 충족 해야 합니다.

- **개인 키**: RSA 키는 2048 비트 이상 이어야 합니다. CA 인증서 만료 날짜가 2030 이후 이면 CA 키는 4096 비트 이상 이어야 합니다.
- **수명**: 온라인 상태이 고 구독자 인증서만 발급 하는 Ca에 대 한 최대 인증서 유효 기간은 6 년을 초과 해서는 안 됩니다. 이러한 Ca의 경우 새 인증서를 발급 하는 데 3 년 이내에 관련 개인 서명 키를 사용 해서는 안 됩니다.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>CA 키는 하드웨어 보안 모듈을 사용 하 여 보호 됩니다.

OpcVault는 Azure Key Vault 프리미엄을 사용 하 고 키는 FIPS 140-2 수준 2 HSM (하드웨어 보안 모듈)에 의해 보호 됩니다. 

HSM 또는 소프트웨어와 관계 없이 Key Vault 사용 하는 암호화 모듈은 FIPS의 유효성을 검사 합니다. HSM으로 보호 되는 키를 만들거나 가져온 키는 HSM 내에서 처리 되며 FIPS 140-2 수준 2로 유효성이 검사 됩니다. 소프트웨어 보호로 만들어지거나 가져온 키는 FIPS 140-2 수준 1의 유효성을 검사 한 암호화 모듈 내에서 처리 됩니다.

## <a name="operational-practices"></a>운영 관행

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>인증서 등록을 위한 표준 운영 PKI 방법 문서화 및 유지 관리

다음을 포함 하 여 Ca에서 인증서를 발급 하는 방법에 대 한 표준 운영 절차 (Sop) 문서화 및 유지 관리 
- 구독자를 식별 하 고 인증 하는 방법입니다. 
- 인증서 요청을 처리 하 고 유효성을 검사 하는 방법 (해당 하는 경우 인증서 갱신 및 키 다시 생성 요청을 처리 하는 방법도 포함). 
- 발급 된 인증서를 구독자에 게 배포 하는 방법입니다. 

Opc 자격 증명 모음 마이크로 서비스 SOP는 opc 자격 증명 [모음 아키텍처](overview-opc-vault-architecture.md) 및 [Opc 자격 증명 모음 인증서 서비스 관리](howto-opc-vault-manage.md)에 설명 되어 있습니다. 이 방법은 "OPC 통합 아키텍처 사양 파트 12: 검색 및 글로벌 서비스 "


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>인증서 해지를 위한 표준 운영 PKI 방법 문서화 및 유지 관리

인증서 해지 프로세스는 [Opc 자격 증명 모음 아키텍처](overview-opc-vault-architecture.md) 에 설명 되어 있고 [Opc 자격 증명 모음 인증서 서비스를 관리](howto-opc-vault-manage.md)합니다.
    
### <a name="document-ca-key-generation-ceremony"></a>문서 CA 키 생성 공식 절차 

Azure Key Vault의 보안 저장소로 인해 OPC 자격 증명 모음 마이크로 서비스의 발급자 CA 키 생성이 간소화 되었습니다. 자세한 내용은 [OPC 자격 증명 모음 인증서 서비스 관리](howto-opc-vault-manage.md)를 참조 하세요.

그러나 외부 루트 인증 기관을 사용 하는 경우 CA 키 생성 공식 절차는 다음 요구 사항을 준수 해야 합니다.

CA 키 생성 공식 절차는 최소한 다음 항목을 포함 하는 문서화 된 스크립트에 대해 수행 되어야 합니다. 
- 역할 및 참가자 책임의 정의입니다.
- CA 키 생성 공식 절차의 수행에 대 한 승인입니다.
- 공식 절차에 필요한 암호화 하드웨어 및 정품 인증 자료입니다.
- 하드웨어 준비 (자산/구성 정보 업데이트 및 로그 오프 포함).
- 운영 체제 설치.
- CA 키 생성 공식 절차 중에 수행 되는 특정 단계는 다음과 같습니다. 
  - CA 응용 프로그램 설치 및 구성.
  - CA 키 생성.
  - CA 키 백업.
  - CA 인증서 서명.
  - 서비스의 보호 된 HSM에서 서명 된 키를 가져옵니다.
  - CA 시스템 종료입니다.
  - 저장소 자료 준비.


## <a name="next-steps"></a>다음 단계

OPC 자격 증명 모음을 안전 하 게 관리 하는 방법을 배웠으므로 이제 다음을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [OPC 자격 증명 모음을 사용 하 여 OPC UA 장치 보호](howto-opc-vault-secure.md)
