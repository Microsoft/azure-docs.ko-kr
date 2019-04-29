---
title: 인증 - Microsoft 위협 모델링 도구 - Azure | Microsoft Docs
description: 위협 모델링 도구에 노출되는 위협 완화
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 56620dc1d3e315caa3e259715ed84a539b91356d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610872"
---
# <a name="security-frame-authentication--mitigations"></a>보안 프레임: 인증 | 완화 

| 제품/서비스 | 문서 |
| --------------- | ------- |
| **웹 애플리케이션**    | <ul><li>[표준 인증 메커니즘을 사용하여 웹 애플리케이션 인증 고려](#standard-authn-web-app)</li><li>[애플리케이션에서 실패한 인증 시나리오를 안전하게 처리해야 함](#handle-failed-authn)</li><li>[버전 업그레이드 또는 적응 인증을 사용하도록 설정](#step-up-adaptive-authn)</li><li>[관리 인터페이스가 적절하게 잠겨 있는지 확인](#admin-interface-lockdown)</li><li>[암호 찾기 기능을 안전하게 구현](#forgot-pword-fxn)</li><li>[암호 및 계정 정책이 구현되었는지 확인](#pword-account-policy)</li><li>[사용자 이름 열거를 방지하기 위한 컨트롤 구현](#controls-username-enum)</li></ul> |
| **데이터베이스** | <ul><li>[가능한 경우 SQL Server에 연결하는 데 Windows 인증 사용](#win-authn-sql)</li><li>[가능한 경우 SQL Database에 연결하는 데 Azure Active Directory 인증 사용](#aad-authn-sql)</li><li>[SQL 인증 모드가 사용되는 경우 계정 및 암호 정책이 SQL Server에 적용되어야 함](#authn-account-pword)</li><li>[포함된 데이터베이스에 SQL 인증을 사용하지 마세요](#autn-contained-db)</li></ul> |
| **Azure 이벤트 허브** | <ul><li>[SaS 토큰을 사용하여 디바이스당 인증 자격 증명 사용](#authn-sas-tokens)</li></ul> |
| **Azure 신뢰 경계** | <ul><li>[Azure 관리자에 Azure Multi-Factor Authentication을 사용하도록 설정](#multi-factor-azure-admin)</li></ul> |
| **Service Fabric 신뢰 경계** | <ul><li>[Service Fabric 클러스터에 익명 액세스 제한](#anon-access-cluster)</li><li>[Service Fabric 클라이언트-노드 인증서가 노드-노드 인증서와 다른지 확인](#fabric-cn-nn)</li><li>[AAD를 사용하여 Service Fabric 클러스터에 대해 클라이언트 인증](#aad-client-fabric)</li><li>[Service Fabric 인증서가 승인된 인증 기관(CA)에서 가져온 것인지 확인](#fabric-cert-ca)</li></ul> |
| **Identity Server** | <ul><li>[Identity Server에서 지원하는 표준 인증 시나리오 사용](#standard-authn-id)</li><li>[기본 Identity Server 토큰 캐시를 확장성 있는 대안으로 재정의](#override-token)</li></ul> |
| **컴퓨터 신뢰 경계** | <ul><li>[배포된 애플리케이션의 이진 파일이 디지털로 서명되었는지 확인](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[WCF에서 MSMQ 큐에 연결할 때 인증을 사용하도록 설정](#msmq-queues)</li><li>[WCF-Message clientCredentialType을 none으로 설정하지 마세요](#message-none)</li><li>[WCF-Transport clientCredentialType을 none으로 설정하지 마세요](#transport-none)</li></ul> |
| **앱 API** | <ul><li>[Web API를 보호하는 데 표준 인증 기술이 사용되는지 확인](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Azure Active Directory에서 지원하는 표준 인증 시나리오 사용](#authn-aad)</li><li>[기본 ADAL 토큰 캐시를 확장성 있는 대안으로 재정의](#adal-scalable)</li><li>[ADAL 인증 토큰의 재생을 방지하는 데 TokenReplayCache가 사용되는지 확인](#tokenreplaycache-adal)</li><li>[ADAL 라이브러리를 사용하여 OAuth2 클라이언트에서 AAD(또는 온-프레미스 AD)로 토큰 요청 관리](#adal-oauth2)</li></ul> |
| **IoT 필드 게이트웨이** | <ul><li>[필드 게이트웨이에 연결되는 디바이스 인증](#authn-devices-field)</li></ul> |
| **IoT 클라우드 게이트웨이** | <ul><li>[클라우드 게이트웨이에 연결되는 디바이스가 인증되는지 확인](#authn-devices-cloud)</li><li>[장치당 인증 자격 증명 사용](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[필요한 컨테이너 및 Blob에만 익명 읽기 권한이 지정되었는지 확인](#req-containers-anon)</li><li>[SAS 또는 SAP를 사용하여 Azure Storage에서 개체에 대한 제한된 액세스 부여](#limited-access-sas)</li></ul> |

## <a id="standard-authn-web-app"></a>표준 인증 메커니즘을 사용하여 웹 애플리케이션 인증 고려

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| 세부 정보 | <p>인증은 엔터티가 일반적으로 사용자 이름 및 암호와 같은 자격 증명을 통해 해당 식별 정보를 증명하는 과정입니다. 제공되는 여러 인증 프로토콜을 고려할 수 있습니다. 그 중 일부는 다음과 같습니다.</p><ul><li>클라이언트 인증서</li><li>Windows 기반</li><li>양식 기반</li><li>페더레이션 - ADFS</li><li>페더레이션 - Azure AD</li><li>Federation - Identity Server</li></ul><p>원본 프로세스를 식별하는 데 표준 인증 메커니즘 사용 고려</p>|

## <a id="handle-failed-authn"></a>애플리케이션에서 실패한 인증 시나리오를 안전하게 처리해야 함

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| 세부 정보 | <p>명시적으로 사용자를 인증하는 애플리케이션은 실패한 인증 시나리오를 안전하게 처리해야 합니다. 해당 인증 메커니즘은 다음을 수행해야 합니다.</p><ul><li>인증에 실패할 경우 권한 있는 리소스에 대한 액세스 거부</li><li>인증 실패 및 액세스 거부가 발생한 후 일반 오류 메시지 표시</li></ul><p>다음을 테스트합니다.</p><ul><li>로그인 실패 후 권한이 있는 리소스 보호</li><li>인증 실패 및 액세스 거부 이벤트 발생 시 일반 오류 메시지가 표시됨</li><li>과도하게 실패한 경우 계정을 사용할 수 없음</li><ul>|

## <a id="step-up-adaptive-authn"></a>버전 업그레이드 또는 적응 인증을 사용하도록 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| 세부 정보 | <p>애플리케이션에 추가 권한 부여(예: SMS, 전자 메일 등으로 OTP 보내기와 같은 Multi-Factor Authentication을 통해 버전 업그레이드 또는 적응 인증 또는 재인증을 요청하는 메시지 표시)가 있는지 확인하여 사용자에게 중요한 정보에 대한 권한을 부여하기 전에 확인하는 과정을 거치도록 합니다. 이 규칙은 계정 또는 작업에 대한 중요한 변경에도 적용됩니다.</p><p>또한, 애플리케이션에서 상황에 맞는 권한 부여를 적절히 적용하는 방식으로 인증 도입을 구현하여 매개 변수 변조와 같은 예를 통한 무단 조작을 허용하지 않도록 해야 합니다.</p>|

## <a id="admin-interface-lockdown"></a>관리 인터페이스가 적절하게 잠겨 있는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| 세부 정보 | 첫 번째 솔루션은 특정 원본 IP 범위에서 관리 인터페이스로만 액세스를 부여하는 것입니다. 이 솔루션이 가능하지 않은 경우 관리 인터페이스에 로그인을 위해 항상 버전 업그레이드 또는 적응 인증을 적용하는 것이 좋습니다. |

## <a id="forgot-pword-fxn"></a>암호 찾기 기능을 안전하게 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| 세부 정보 | <p>첫째, 암호 자체보다는 시간이 제한된 활성화 토큰을 포함한 링크를 보내는 기타 복구 경로와 분실한 암호를 확인합니다. 링크가 전송되기 전에 소프트 토큰(예: SMS 토큰, 네이티브 모바일 애플리케이션 등)을 기반으로 한 추가 인증이 필요할 수 있습니다. 둘째, 새 암호 가져오기 프로세스를 진행 중인 동안은 사용자 계정이 잠기지 않도록 해야 합니다.</p><p>이 경우 공격자가 자동화된 공격을 통해 내부적으로 사용자를 잠그려고 할 때마다 서비스 거부 공격이 발생할 수 있습니다. 셋째, 새 암호 요청 설정이 진행될 때마다 사용자 이름이 열거되는 것을 방지하기 위해 표시되는 메시지를 일반화해야 합니다. 넷째, 항상 이전 암호 사용을 허용하지 말고 강력한 암호 정책을 적용해야 합니다.</p> |

## <a id="pword-account-policy"></a>암호 및 계정 정책이 구현되었는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| 세부 정보 | <p>조직의 정책 및 모범 사례에 따라 암호 및 계정 정책이 구현되어야 합니다.</p><p>무차별 암호 대입 및 사전 기반 추측을 방지하려면: 강력한 암호 정책을 구현하여 사용자가 복잡한 암호를 만들도록 해야 합니다(예: 최소 12자, 영숫자 및 특수 문자).</p><p>다음과 같이 계정 잠금 정책을 구현할 수 있습니다.</p><ul><li>**소프트 잠금:** 무차별 암호 대입 공격(brute force attack)으로부터 사용자를 보호하기 적합한 옵션일 수 있습니다. 예를 들어 사용자가 잘못된 암호를 세 번 입력할 때마다 애플리케이션은 1분 동안 계정을 잠그고 무차별 암호 대입 과정의 속도를 저하시켜 공격자의 진행을 방해할 수 있습니다. 이 예제의 경우 하드 잠금 대응 방법을 구현했다면 계정을 영구적으로 잠금으로써 "Dos"를 달성합니다. 또는 애플리케이션에서 OTP(일회성 암호)를 생성하고 대역외(전자 메일, sms 등을 통해) 사용자에게 보냅니다. 다른 방법은 실패한 시도 횟수 임계값에 도달한 후 CAPTCHA를 구현하는 것입니다.</li><li>**하드 잠금:** 이 유형의 잠금은 애플리케이션을 공격하는 사용자를 감지할 때마다 적용해야 하며 대응 팀이 과학 수사를 수행할 시간을 확보할 때까지 계정을 영구적으로 잠그는 방법으로 대응합니다. 이 과정을 거친 후 사용자에게 계정을 다시 제공하거나 추가 법적 조치를 취하도록 결정할 수 있습니다. 이러한 접근 방식은 공격자가 애플리케이션 및 인프라를 더 이상 통과하지 못하도록 합니다.</li></ul><p>기본 및 예측 가능한 계정에 대한 공격을 방어하려면 모든 키 및 암호를 대체할 수 있는, 설치 시간 후 생성 또는 대체되는지 확인합니다.</p><p>애플리케이션에서 암호를 자동으로 생성해야 하는 경우 생성된 암호가 임의적이며 높은 엔트로피를 포함하는지 확인합니다.</p>|

## <a id="controls-username-enum"></a>사용자 이름 열거를 방지하기 위한 컨트롤 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 사용자 이름이 열거되지 않도록 모든 오류 메시지를 일반화해야 합니다. 또한 등록 페이지처럼 기능에 따라 정보 누출이 불가피한 경우도 있습니다. 여기서는 CAPTCHA와 같은 속도 제한 메서드를 사용하여 공격자의 자동화된 공격을 방지해야 합니다. |

## <a id="win-authn-sql"></a>가능한 경우 SQL Server에 연결하는 데 Windows 인증 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | OnPrem |
| **특성**              | SQL 버전 - 모두 |
| **참조**              | [SQL Server - 인증 모드 선택](https://msdn.microsoft.com/library/ms144284.aspx) |
| **단계** | Windows 인증은 Kerberos 보안 프로토콜을 사용하고 강력한 암호를 위한 복잡한 검증을 거치는 암호 정책 적용을 제공하며 계정 잠금, 암호 만료를 지원합니다.|

## <a id="aad-authn-sql"></a>가능한 경우 SQL Database에 연결하는 데 Azure Active Directory 인증 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | SQL Azure |
| **특성**              | SQL 버전 - V12 |
| **참조**              | [Azure Active Directory 인증을 사용하여 SQL Database에 연결](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **단계** | **최소 버전:** Azure SQL Database에서 Microsoft Directory에 대해 AAD 인증을 사용하려면 Azure SQL Database V12가 필요합니다. |

## <a id="authn-account-pword"></a>SQL 인증 모드가 사용되는 경우 계정 및 암호 정책이 SQL Server에 적용되어야 함

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [SQL Server 암호 정책](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **단계** | SQL Server 인증을 사용하는 경우 Windows 사용자 계정을 기반으로 하지 않는 로그인이 SQL Server에 만들어집니다. SQL Server를 사용하여 사용자 이름 및 암호가 생성되어 SQL Server에 저장됩니다. SQL Server는 Windows 암호 정책 메커니즘을 사용할 수 있습니다. SQL Server 내부에 사용된 암호에 대해 Windows에 사용된 것과 동일한 복잡성 및 만료 정책을 적용할 수 있습니다. |

## <a id="autn-contained-db"></a>포함된 데이터베이스에 SQL 인증을 사용하지 마세요

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | OnPrem, SQL Azure |
| **특성**              | SQL 버전 - MSSQL2012, SQL 버전 - V12 |
| **참조**              | [포함된 데이터베이스의 보안 모범 사례](https://msdn.microsoft.com/library/ff929055.aspx) |
| **단계** | 적용된 암호 정책이 없는 경우 포함된 데이터베이스에 취약한 자격 증명이 설정될 가능성이 높아질 수 있습니다. Windows 인증을 활용합니다. |

## <a id="authn-sas-tokens"></a>SaS 토큰을 사용하여 디바이스당 인증 자격 증명 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Event Hub | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [Event Hubs 인증 및 보안 모델 개요](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **단계** | <p>Event Hubs 보안 모델은 공유 액세스 서명(SAS) 토큰 및 이벤트 게시자의 조합을 기반으로 합니다. 게시자 이름은 토큰을 받는 DeviceID를 나타냅니다. 해당 디바이스를 사용하여 생성된 토큰을 연결하는 데 도움이 됩니다.</p><p>모든 메시지에는 서비스 쪽 송신자로 태그가 지정되어 페이로드 내 원본 스푸핑 시도를 검색할 수 있습니다. 디바이스를 인증할 경우 고유 게시자로 범위가 지정된 디바이스당 SaS 토큰을 생성합니다.</p>|

## <a id="multi-factor-azure-admin"></a>Azure 관리자에 Azure Multi-Factor Authentication을 사용하도록 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure 신뢰 경계 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [Azure Multi-Factor Authentication 정의](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **단계** | <p>Multi-Factor Authentication(MFA)에는 두 개 이상의 검증 방법이 필요하며 사용자 로그인 및 트랜잭션에 중요한 두 번째 계층을 추가하는 인증 방법입니다. 이러한 인증에서는 다음 중 두 가지 이상의 검증 방법을 요구합니다.</p><ul><li>사용자가 알고 있는 정보(일반적으로 암호)</li><li>사용자가 보유한 디바이스(예: 휴대폰과 같이 쉽게 복제되지 않는 신뢰할 수 있는 디바이스)</li><li>사용자의 신원 정보(생체 인식)</li><ul>|

## <a id="anon-access-cluster"></a>Service Fabric 클러스터에 익명 액세스 제한

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Service Fabric 트러스트 경계 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | 환경 - Azure  |
| **참조**              | [Service Fabric 클러스터 보안 시나리오](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **단계** | <p>항상 실행 중인 프로덕션 작업이 있을 때 권한 없는 사용자가 클러스터에 연결하지 못하도록 하여 클러스터의 보안을 유지합니다.</p><p>Service Fabric 클러스터를 만드는 동안에는 보안 모드가 "secure"로 설정되고 필요한 X.509 서버 인증서를 구성해야 합니다. "insecure" 클러스터를 만들면 공용 인터넷에 관리 엔드포인트가 노출될 경우 익명 사용자가 클러스터에 연결할 수 있게 됩니다.</p>|

## <a id="fabric-cn-nn"></a>Service Fabric 클라이언트-노드 인증서가 노드-노드 인증서와 다른지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Service Fabric 트러스트 경계 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | 환경 - Azure, 환경 - 독립 실행형 |
| **참조**              | [Service Fabric 클라이언트-노드 인증서 보안](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [클라이언트 인증서를 사용하여 보안 클러스터에 연결](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **단계** | <p>클라이언트-노드 인증서 보안은 관리 클라이언트 인증서 및/또는 사용자 클라이언트 인증서를 지정하여 Azure 포털, Resource Manager 템플릿 또는 독립 실행형 JSON 템플릿을 통해 클러스터를 만드는 동안 구성됩니다.</p><p>지정한 관리 클라이언트 인증서 및 사용자 클라이언트 인증서는 노드 간 보안에 대해 지정한 기본 및 보조 인증서와 다릅니다.</p>|

## <a id="aad-client-fabric"></a>AAD를 사용하여 Service Fabric 클러스터에 대해 클라이언트 인증

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Service Fabric 트러스트 경계 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | 환경 - Azure |
| **참조**              | [클러스터 보안 시나리오 - 보안 권장 사항](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **단계** | 또한 Azure에서 실행되는 클라이언트는 클라이언트 인증서와 별도로, AAD(Azure Active Directory)를 사용하여 관리 엔드포인트에 안전하게 액세스할 수 있습니다. Azure 클러스터의 경우 AAD 보안을 사용하여 노드 간 보안에 대해 클라이언트 및 인증서를 인증하는 것이 좋습니다.|

## <a id="fabric-cert-ca"></a>Service Fabric 인증서가 승인된 인증 기관(CA)에서 가져온 것인지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Service Fabric 트러스트 경계 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | 환경 - Azure |
| **참조**              | [X.509 인증서 및 Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **단계** | <p>Service Fabric은 노드 및 클라이언트를 인증하는 데 X.509 서버 인증서를 사용합니다.</p><p>Service Fabric에서 인증서를 사용하는 동안 고려해야 할 몇 가지 중요한 사항은 다음과 같습니다.</p><ul><li>프로덕션 워크로드를 실행하는 클러스터에 사용되는 인증서는 올바르게 구성된 Windows Server 인증서 서비스를 사용하여 만들어지거나 승인된 CA(인증 기관)에서 획득해야 합니다. CA는 승인된 외부 CA 또는 적절히 관리되는 내부 PKI(공개 키 인프라)일 수 있습니다.</li><li>프로덕션 환경에서 MakeCert.exe와 같은 도구를 사용하여 만든 임시 또는 테스트 인증서를 사용하지 마세요.</li><li>자체 서명된 인증서를 사용할 수 있지만 프로덕션이 아닌 테스트 클러스터에 대해서만 이러한 인증서를 사용해야 합니다.</li></ul>|

## <a id="standard-authn-id"></a>Identity Server에서 지원하는 표준 인증 시나리오 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | ID 서버 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [IdentityServer3 - 큰 그림](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **단계** | <p>Identity Server에서 지원하는 일반적인 상호 작용은 다음과 같습니다.</p><ul><li>브라우저는 웹 애플리케이션과 통신</li><li>웹 애플리케이션은 Web API와 통신(때로는 자체적으로, 때로는 사용자 대신)</li><li>브라우저 기반 애플리케이션은 Web API와 통신</li><li>네이티브 애플리케이션은 Web API와 통신</li><li>서버 기반 애플리케이션은 Web API와 통신</li><li>Web API가 Web API와 통신(때로는 자체적으로, 때로는 사용자 대신)</li></ul>|

## <a id="override-token"></a>기본 Identity Server 토큰 캐시를 확장성 있는 대안으로 재정의

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | ID 서버 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [Identity Server 배포 - 캐싱](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html)(영문) |
| **단계** | <p>IdentityServer는 간단한 기본 메모리 내 캐시를 포함합니다. 작은 규모의 네이티브 앱에는 적절하지만 중간 계층 및 백 엔드 애플리케이션에 대해서는 다음과 같은 이유로 규모가 적절하지 않습니다.</p><ul><li>이러한 애플리케이션은 한 번에 많은 사용자가 액세스합니다. 모든 액세스 토큰을 동일한 저장소에 저장하면 격리 문제가 생성되고 규모로 작동 시 문제가 나타납니다. 많은 사용자가 있고 각각 앱에서 대신 액세스하는 리소스만큼 많은 토큰을 보유한다면 상당한 수의 비용이 많이 드는 조회 작업을 의미할 수 있습니다.</li><li>이러한 애플리케이션은 일반적으로 분산된 토폴로지에 배포되며 여기서 여러 노드가 동일한 캐시에 액세스할 수 있어야 합니다.</li><li>캐시된 토큰은 프로세스 재활용 및 비활성화에도 유지되어야 함</li><li>위의 모든 이유로, 웹앱을 구현하는 동안 기본 Identity Server 토큰 캐시를 Azure Cache for Redis처럼 확장성 있는 대안으로 재정의하는 것이 좋습니다.</li></ul>|

## <a id="binaries-signed"></a>배포된 애플리케이션의 이진 파일이 디지털로 서명되었는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 컴퓨터 신뢰 경계 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 배포된 애플리케이션의 이진 파일이 디지털로 서명되었는지 확인하여 이진 파일의 무결성을 검증합니다.|

## <a id="msmq-queues"></a>WCF에서 MSMQ 큐에 연결할 때 인증을 사용하도록 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, .NET Framework 3 |
| **특성**              | N/A |
| **참조**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **단계** | MSMQ 큐에 연결할 때 프로그램이 인증을 사용하는 데 실패하면 공격자는 처리를 위해 큐에 메시지를 익명으로 제출할 수 있습니다. 다른 프로그램에 메시지를 전달하는 데 사용되는 MSMQ 큐에 연결하는 데 인증이 사용되지 않으면 공격자가 악성인 익명의 메시지를 제출할 수 있습니다.|

### <a name="example"></a>예
아래에 보이는 WCF 구성 파일의 `<netMsmqBinding/>` 요소는 메시지 배달을 위한 MSMQ 큐에 연결할 때 WCF가 인증을 사용하지 않도록 지시합니다.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
모든 들어오거나 나가는 메시지에 대해 항상 Windows 도메인 또는 인증서 인증을 요구하도록 MSMQ를 구성합니다.

### <a name="example"></a>예
아래 WCF 구성 파일의 `<netMsmqBinding/>` 요소는 MSMQ 큐에 연결할 때 WCF가 인증서 인증을 사용하도록 지시합니다. 클라이언트는 X.509 인증서를 사용하여 인증됩니다. 클라이언트 인증서는 서버의 클라이언트 저장소에 있어야 합니다.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a id="message-none"></a>WCF-Message clientCredentialType을 none으로 설정하지 마세요

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | .NET Framework 3 |
| **특성**              | 클라이언트 자격 증명 유형 - 없음 |
| **참조**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_message_client) |
| **단계** | 인증이 없다는 것은 모든 사람이 이 서비스에 액세스할 수 있음을 의미합니다. 해당 클라이언트를 인증하지 않는 서비스는 모든 사용자가 액세스할 수 있습니다. 클라이언트 자격 증명에 대해 인증하도록 애플리케이션을 구성합니다. message clientCredentialType을 Windows 또는 Certificate로 설정하여 이 작업을 수행할 수 있습니다. |

### <a name="example"></a>예
```
<message clientCredentialType=""Certificate""/>
```

## <a id="transport-none"></a>WCF-Transport clientCredentialType을 none으로 설정하지 마세요

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, .NET Framework 3 |
| **특성**              | 클라이언트 자격 증명 유형 - 없음 |
| **참조**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_transport_client) |
| **단계** | 인증이 없다는 것은 모든 사람이 이 서비스에 액세스할 수 있음을 의미합니다. 해당 클라이언트를 인증하지 않는 서비스는 모든 사용자가 해당 기능에 액세스할 수 있습니다. 클라이언트 자격 증명에 대해 인증하도록 애플리케이션을 구성합니다. transport clientCredentialType을 Windows 또는 Certificate로 설정하여 이 작업을 수행할 수 있습니다. |

### <a name="example"></a>예
```
<transport clientCredentialType=""Certificate""/>
```

## <a id="authn-secure-api"></a>Web API를 보호하는 데 표준 인증 기술이 사용되는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Web API | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [ASP.NET Web API의 인증 및 권한 부여](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api)(영문), [ASP.NET Web API의 외부 인증 서비스(C#)](https://www.asp.net/web-api/overview/security/external-authentication-services)(영문) |
| **단계** | <p>인증은 엔터티가 일반적으로 사용자 이름 및 암호와 같은 자격 증명을 통해 해당 식별 정보를 증명하는 과정입니다. 제공되는 여러 인증 프로토콜을 고려할 수 있습니다. 그 중 일부는 다음과 같습니다.</p><ul><li>클라이언트 인증서</li><li>Windows 기반</li><li>양식 기반</li><li>페더레이션 - ADFS</li><li>페더레이션 - Azure AD</li><li>Federation - Identity Server</li></ul><p>참조 섹션의 링크를 통해 Web API를 보호하기 위해 각 인증 체계가 어떻게 구현되는지 자세히 살펴볼 수 있습니다.</p>|

## <a id="authn-aad"></a>Azure Active Directory에서 지원하는 표준 인증 시나리오 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure AD | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [Azure AD의 인증 시나리오](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [Azure Active Directory 코드 샘플](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [Azure Active Directory 개발자 가이드](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **단계** | <p>Azure AD(Azure Active Directory)는 IDaaS(Identity as a Service)를 제공하며 OAuth 2.0 및 OpenID Connect 등의 업계 표준 프로토콜을 지원하여 개발자의 인증 작업을 간소화합니다. 다음은 Azure AD에서 지원되는 다섯 가지 기본 애플리케이션 시나리오입니다.</p><ul><li>웹 브라우저-웹 애플리케이션: 사용자가 Azure AD로 보호되는 웹 애플리케이션에 로그인해야 합니다.</li><li>SPA(단일 페이지 애플리케이션) 사용자가 Azure AD로 보호되는 단일 페이지 애플리케이션에 로그인해야 합니다.</li><li>네이티브 애플리케이션-웹 API: 전화, 태블릿 또는 PC에서 실행되는 네이티브 애플리케이션이 사용자를 인증해야 Azure AD로 보호되는 웹 API에서 리소스를 가져올 수 있습니다.</li><li>웹 애플리케이션-웹 API 웹 애플리케이션이 Azure AD로 보호되는 웹 API에서 리소스를 가져와야 합니다.</li><li>디먼 또는 서버 애플리케이션-웹 API: 웹 사용자 인터페이스가 없는 서버 애플리케이션 또는 디먼 애플리케이션이 Azure AD로 보호되는 웹 API에서 리소스를 가져와야 합니다.</li></ul><p>자세한 구현 세부 정보는 참조 섹션의 링크를 참조하세요.</p>|

## <a id="adal-scalable"></a>기본 ADAL 토큰 캐시를 확장성 있는 대안으로 재정의

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure AD | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [웹 애플리케이션에 대한 Azure Active Directory의 최신 인증](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/)(영문), [ADAL 토큰 캐시로 Redis 사용](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)(영문)  |
| **단계** | <p>ADAL(Active Directory 인증 라이브러리)이 사용하는 기본 캐시는 정적인 저장소에 의존하는 메모리 내 캐시이며 프로세스 범위에서 사용 가능합니다. 네이티브 앱에는 작동하지만 중간 계층 및 백 엔드 애플리케이션에 대해서는 다음과 같은 이유로 규모가 적절하지 않습니다.</p><ul><li>이러한 애플리케이션은 한 번에 많은 사용자가 액세스합니다. 모든 액세스 토큰을 동일한 저장소에 저장하면 격리 문제가 생성되고 규모로 작동 시 문제가 나타납니다. 많은 사용자가 있고 각각 앱에서 대신 액세스하는 리소스만큼 많은 토큰을 보유한다면 상당한 수의 비용이 많이 드는 조회 작업을 의미할 수 있습니다.</li><li>이러한 애플리케이션은 일반적으로 분산된 토폴로지에 배포되며 여기서 여러 노드가 동일한 캐시에 액세스할 수 있어야 합니다.</li><li>캐시된 토큰은 프로세스 재활용 및 비활성화에도 유지되어야 함</li></ul><p>위의 모든 이유 때문에, 웹앱을 구현하는 동안 기본 ADAL 토큰 캐시를 Azure Cache for Redis처럼 확장성 있는 대안으로 재정의하는 것이 좋습니다.</p>|

## <a id="tokenreplaycache-adal"></a>ADAL 인증 토큰의 재생을 방지하는 데 TokenReplayCache가 사용되는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure AD | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [웹 애플리케이션에 대한 Azure Active Directory의 최신 인증](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/)(영문) |
| **단계** | <p>TokenReplayCache 속성을 통해 개발자는 토큰 재생 캐시를 정의할 수 있으며 두 번 이상 사용할 수 있는 토큰이 없음을 확인할 용도로 토큰을 저장하는 데 사용할 수 있는 저장소를 정의할 수 있습니다.</p><p>일반적인 공격 즉, 토큰 재생 공격에 대한 조치입니다. 로그인 시 전송된 토큰을 가로채는 공격자는 새로운 세션을 설정하기 위해 토큰을 다시 앱에 전송("재생")하려고 할 수 있습니다. 예를 들어, OIDC 코드 부여 흐름에서 사용자가 성공적으로 인증된 후 신뢰 당사자의 "/signin-oidc" 엔드포인트에 대한 요청은 "id_token", "code" 및 "state" 매개 변수로 구성됩니다.</p><p>신뢰 당사자는 이 요청을 확인하고 새 세션을 설정합니다. 이 요청을 악의적으로 캡처하고 재생한 공격자는 성공적인 세션을 설정하여 사용자를 스푸핑할 수 있습니다. OpenID Connect에서 nonce의 존재를 제한할 수는 있지만 공격을 성공적으로 적용할 수 있는 환경이 완전히 사라지지는 않습니다. 이러한 애플리케이션을 보호하려면 개발자는 ITokenReplayCache의 구현을 제공하고 인스턴스를 TokenReplayCache에 할당할 수 있습니다.</p>|

### <a name="example"></a>예
```csharp
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>예
ITokenReplayCache 인터페이스의 예제 구현은 다음과 같습니다. (프로젝트별 캐싱 프레임워크를 사용자 지정 및 구현하세요)
```csharp
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
구현된 캐시는 다음과 같이 "TokenValidationParameters" 속성을 통해 OIDC 옵션에서 참조되어야 합니다.
```csharp
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

이 구성의 효율성을 테스트하려면 OIDC로 보호되는 로컬 애플리케이션에 로그인하고 fiddler에서 `"/signin-oidc"` 엔드포인트에 대한 요청을 캡처합니다. 보호 조치가 없는 경우 fiddler에서 이 요청을 재생하면 새로운 세션 쿠키가 설정됩니다. TokenReplayCache 보호를 추가한 후 요청이 재생되면 다음과 같이 애플리케이션에서 예외가 throw됩니다. `SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a id="adal-oauth2"></a>ADAL 라이브러리를 사용하여 OAuth2 클라이언트에서 AAD(또는 온-프레미스 AD)로 토큰 요청 관리

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure AD | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **단계** | <p>ADAL(Azure AD 인증 라이브러리)은 클라이언트 애플리케이션 개발자들이 클라우드 또는 온-프레미스 AD(Active Directory)에 쉽게 사용자를 인증하고 API 호출 보안을 위한 액세스 토큰을 가져올 수 있게 합니다.</p><p>ADAL에는 비동기 지원, 액세스 토큰 및 새로고침 토큰을 저장하는 구성 가능한 토큰 캐시, 액세스 토큰이 만료되고 새로고침 토큰을 사용할 수 있을 때 자동 토큰 새로고침 등, 개발자들의 인증을 쉽게 지원하는 많은 기능이 있습니다.</p><p>대부분의 복잡성을 처리함으로써 ADAL은 개발자가 애플리케이션의 비즈니스 논리에 초점을 맞추고 보안 전문가가 아니더라도 리소스를 쉽게 보호할 수 있게 도와줍니다. .NET, JavaScript(클라이언트 및 Node.js), iOS, Android 및 Java에 대해 별도의 라이브러리를 사용할 수 있습니다.</p>|

## <a id="authn-devices-field"></a>필드 게이트웨이에 연결되는 디바이스 인증

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 필드 게이트웨이 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 각 디바이스가 데이터를 받기 전에 클라우드 게이트웨이와 업스트림 통신을 용이하게 하려면 필드 게이트웨이로 인증되었는지 확인합니다. 또한 디바이스가 디바이스별 자격 증명에 연결되었는지 확인하여 개별 디바이스를 고유하게 식별할 수 있도록 합니다.|

## <a id="authn-devices-cloud"></a>클라우드 게이트웨이에 연결되는 디바이스가 인증되는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 클라우드 게이트웨이 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, C#, Node.JS,  |
| **특성**              | 해당 없음, 게이트웨이 선택 - Azure IoT Hub |
| **참조**              | 해당 없음 [.NET을 사용 하 여 Azure IoT hub](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), [IoT hub 및 Node JS 시작](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted)합니다 [SAS 및 인증서로 IoT 보호](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [Git 리포지토리](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **단계** | <ul><li>**일반:** TLS(전송 계층 보안) 또는 IPSec을 사용하여 디바이스를 인증합니다. 전체 비대칭 암호화를 처리할 수 없는 PSK(미리 공유한 키)를 해당 디바이스에서 사용할 수 있도록 인프라가 지원해야 합니다. Azure AD, Oauth를 활용하세요.</li><li>**C#:** DeviceClient 인스턴스를 만들 때, 기본적으로 Create 메서드는 AMQP 프로토콜을 사용하여 IoT Hub와 통신하는 DeviceClient 인스턴스를 만듭니다. HTTPS 프로토콜을 사용하려면 프로토콜을 지정할 수 있도록 해주는 Create 메서드의 재정의를 사용합니다. HTTPS 프로토콜을 사용하려면 `Microsoft.AspNet.WebApi.Client` NuGet 패키지를 프로젝트에 추가하여 `System.Net.Http.Formatting` 네임스페이스를 포함해야 합니다.</li></ul>|

### <a name="example"></a>예
```csharp
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>예
**Node.JS: 인증**
#### <a name="symmetric-key"></a>대칭 키
* Azure에서 IoT hub 만들기
* 디바이스 ID 레지스트리에서 항목 만들기
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* 시뮬레이션된 디바이스 만들기
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
  #### <a name="sas-token"></a>SAS 토큰
* 대칭 키를 사용할 때 내부적으로 생성된 것을 가져오지만 명시적으로도 생성 및 사용할 수 있음
* 프로토콜 정의 : `var Http = require('azure-iot-device-http').Http;`
* sas 토큰 만들기:
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
  + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* sas 토큰을 사용하여 연결: 
    ```javascript
    Client.fromSharedAccessSignature(sas, Http); 
    ```
  #### <a name="certificates"></a>인증서
* OpenSSL과 같은 도구를 사용하여 자체 서명된 X509 인증서를 생성하고 인증서 및 키를 저장할 .cert 및 .key 파일을 각각 생성합니다.
* 인증서를 사용하여 보안 연결을 허용하는 디바이스 프로비전
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* 인증서를 사용하여 디바이스 연결
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    }; 
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a id="authn-cred"></a>장치당 인증 자격 증명 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 클라우드 게이트웨이  | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | 게이트웨이 선택 - Azure IoT Hub |
| **참조**              | [Azure IoT Hub 보안 토큰](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **단계** | IoT Hub 수준 공유 액세스 정책 대신, 디바이스 키 또는 클라이언트 인증서를 기반으로 SaS 토큰을 사용하여 디바이스당 인증 자격 증명을 사용합니다. 이렇게 하면 디바이스 또는 필드 게이트웨이의 인증 토큰을 재사용하는 것을 방지합니다. |

## <a id="req-containers-anon"></a>필요한 컨테이너 및 Blob에만 익명 읽기 권한이 지정되었는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Storage | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | StorageType - Blob |
| **참조**              | [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [공유 액세스 서명, 1부: SAS 모델 이해](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **단계** | <p>기본적으로는 저장소 계정 소유자만 컨테이너 및 해당 컨테이너 내의 모든 Blob에 액세스할 수 있습니다. 익명 사용자에게 컨테이너와 해당 Blob에 대한 읽기 권한을 제공하려면 공용 액세스를 허용하도록 컨테이너 권한을 설정할 수 있습니다. 그러면 익명 사용자가 요청을 인증하지 않고도 공개적으로 액세스 가능한 컨테이너 내의 Blob을 읽을 수 있습니다.</p><p>컨테이너는 컨테이너 액세스를 관리하기 위한 다음 옵션을 제공합니다.</p><ul><li>전체 공용 읽기 액세스: 익명 요청을 통해 컨테이너와 Blob 데이터를 읽을 수 있습니다. 클라이언트는 익명 요청을 통해 컨테이너 내에서 Blob을 열거할 수 있지만 저장소 계정 내에서 컨테이너를 열거할 수는 없습니다.</li><li>Blob 전용 공용 읽기 액세스: 이 컨테이너 내의 Blob 데이터는 익명 요청을 통해 읽을 수 있으나 컨테이너 데이터는 읽을 수 없습니다. 클라이언트는 익명 요청을 통해 컨테이너 내의 Blob을 열거할 수 없습니다.</li><li>공용 읽기 액세스 없음: 계정 소유자만 컨테이너 및 Blob 데이터를 읽을 수 있습니다.</li></ul><p>익명 액세스는 특정 Blob을 익명 읽기 권한에 사용해야 하는 경우에 가장 적합합니다. 더 세밀하게 제어하려면 공유 액세스 서명을 만들어 다른 권한 및 지정된 시간 간격을 통해 제한된 액세스를 위임할 수 있습니다. 중요한 데이터를 포함할 가능성이 있는 컨테이너 및 Blob를 실수로 익명 액세스하지 않는지 확인합니다.</p>|

## <a id="limited-access-sas"></a>SAS 또는 SAP를 사용하여 Azure Storage에서 개체에 대한 제한된 액세스 부여

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Storage | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A |
| **참조**              | [공유 액세스 서명, 1부: SAS 모델 이해](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) [공유 액세스 서명, 2부: Blob Storage로 SAS 생성 및 사용](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [공유 액세스 서명공유 액세스 서명 및 저장된 액세스 정책을 사용하여 계정의 개체에 대한 액세스를 위임하는 방법](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **단계** | <p>SAS(공유 액세스 서명)를 사용하면 계정 액세스 키를 노출하지 않고 다른 클라이언트에게 저장소 계정의 개체에 대한 제한된 액세스 권한을 확실히 부여할 수 있습니다. SAS는 저장소 리소스에 인증된 액세스를 수행하는 데 필요한 모든 정보가 쿼리 매개 변수에 있는 URI입니다. SAS를 사용하여 저장소 리소스에 액세스하려는 클라이언트는 SAS를 적절한 생성자 또는 메서드에 전달하면 됩니다.</p><p>계정 키로 신뢰할 수 없는 클라이언트에게 저장소 계정의 리소스에 대한 액세스 권한을 제공하려는 경우에 SAS를 사용할 수 있습니다. 저장소 계정 키는 기본 키와 보조 키를 모두 포함하여, 계정과 계정에 있는 모든 리소스에 대한 관리 권한을 부여합니다. 이러한 계정 키가 노출되면 계정이 악의적이거나 부주의하게 사용될 수 있습니다. 공유 액세스 서명은 다른 클라이언트가 계정 키를 사용하지 않고 부여된 권한에 따라 저장소 계정에서 데이터를 읽고, 쓰고, 삭제하도록 허용하는 안전한 대체 방법입니다.</p><p>매번 비슷한 매개 변수의 논리적 집합을 사용한다면 저장된 액세스 정책(SAP)을 사용하는 것이 더 좋습니다. 저장된 액세스 정책에서 파생된 SAS를 사용하면 해당 SAS를 즉시 해지할 수 있으므로 가능한 경우 항상 저장된 액세스 정책을 사용하는 것이 바람직합니다.</p>|
