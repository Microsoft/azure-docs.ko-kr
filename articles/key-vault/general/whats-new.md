---
title: Azure Key Vault의 새 기능 | Microsoft Docs
description: Azure Key Vault 최신 업데이트
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.date: 07/27/2020
ms.author: mbaldwin
ms.openlocfilehash: b469ea0d0a91cc4ca7f0602dffbcc63b31c60855
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588367"
---
# <a name="whats-new-for-azure-key-vault"></a>Azure Key Vault의 새 기능

> [!WARNING]
> **2020년 7월**: 서비스 구현에 영향을 미칠 수 있는 키 자격 증명 모음에 대한 두 가지 업데이트가 있는데, 하나는 [기본적으로 키 자격 증명 모음 일시 삭제 사용](#soft-delete-on-by-default)이고 다른 하나는 [Azure TLS 인증서 변경](#azure-tls-certificate-changes)입니다. 자세한 내용은 다음을 참조하세요.

Azure Key Vault의 새 기능은 다음과 같습니다. [Azure 업데이트 Key Vault 채널](https://azure.microsoft.com/updates/?category=security&query=Key%20vault)에도 새 기능과 개선된 기능이 공지되어 있습니다.

## <a name="soft-delete-on-by-default"></a>기본적으로 일시 삭제 사용

2020년이 종료되면 새 키 자격 증명 모음과 기존 키 자격 증명 모음을 포함한 **모든 키 자격 증명 모음에서 기본적으로 소프트 삭제가 사용**됩니다. 호환성이 손상될 수 있는 이 변경 내용에 대한 자세한 내용과 영향을 받는 키 자격 증명 모음을 찾아서 미리 업데이트하는 단계에 대한 자세한 내용은 [모든 키 자격 증명 모음에 대해 사용하도록 설정되는 일시 삭제](soft-delete-change.md) 문서를 참조하세요. 

## <a name="azure-tls-certificate-changes"></a>Azure TLS 인증서 변경  

Microsoft는 다른 루트 CA(인증 기관)의 TLS 인증서를 사용하도록 Azure 서비스를 업데이트하 고 있습니다. 이렇게 변경하는 이유는 현재 CA 인증서가 CA/브라우저 포럼 기준 요구 사항 중 하나를 준수하지 않기 때문입니다.

### <a name="when-will-this-change-happen"></a>언제 변경되나요?

- Azure AD(Azure Active Directory) 서비스는 2020년 7월 7일에 이 전환을 시작했습니다.
- 새로 만든 모든 Azure TLS/SSL 엔드포인트에는 새로운 루트 CA에 연결하는 업데이트된 인증서가 포함되어 있습니다. 
- 기존 Azure 엔드포인트는 2020년 8월 13일부터 단계별 전환을 시작하여 2020년 10월 26일에 완료할 예정입니다.

> [!IMPORTANT]
> 고객은 Azure 서비스에 연결을 시도할 때 연결 오류를 방지하려면 이 변경 후에 애플리케이션을 업데이트해야 할 수도 있습니다. 

### <a name="what-is-changing"></a>변경되는 내용

현재 Azure 서비스에서 사용하는 대부분의 TLS 인증서는 다음과 같은 루트 CA에 연결됩니다.

| CA의 일반 이름 | 지문(SHA1) |
|--|--|
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

Azure 서비스에서 사용하는 TLS 인증서는 다음과 같은 루트 CA 중 하나에 연결됩니다.

| CA의 일반 이름 | 지문(SHA1) |
|--|--|
| [DigiCert Global Root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert Global Root CA](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D-TRUST Root Class 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft EV ECC Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20EV%20ECC%20Root%20Certificate%20Authority%202017.crt) | 6b1937abfd64e1e40daf2262a27857c015d6228d |

### <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>기존의 중간 지문을 언제 사용 중지할 수 있나요?

현재 CA 인증서는 2021년 2월 15일까지 해지되지 *않습니다*. 이 날짜가 지나면 코드에서 이전 지문을 제거할 수 있습니다.

이 날짜가 변경될 경우 새로운 해지 날짜에 대한 알림이 제공됩니다.

### <a name="will-this-affect-me"></a>내게 어떤 영향이 있나요?

**대부분의 Azure 고객에게는 영향을 주지 않을 것**으로 예상됩니다.  그러나 허용되는 CA 목록을 명시적으로 지정하는 경우 애플리케이션에 영향을 줄 수 있습니다. 이 방법을 인증서 고정이라고 합니다.

다음과 같은 방법으로 애플리케이션이 영향을 받는지 알아볼 수 있습니다.

- [여기](https://www.microsoft.com/pki/mscorp/cps/default.htm)에 있는 Microsoft IT TLS CA의 지문, 일반 이름 및 기타 인증서 속성을 소스코드에서 검색합니다. 일치하는 항목이 있으면 애플리케이션이 영향을 받습니다. 이 문제를 해결하려면 새 CA를 포함하도록 소스 코드를 업데이트합니다. 모범 사례에 따라 긴급하게 CA를 추가 또는 편집할 수 있어야 합니다. 업계 규정을 준수하려면 CA 인증서를 7일 이내에 바꿔야 하므로, 고정 방식을 사용하는 고객은 신속하게 대응해야 합니다.

- Azure API 또는 다른 Azure 서비스와 통합되는 애플리케이션이 있고 해당 애플리케이션이 인증서 고정을 사용하는지 확실하지 않은 경우 애플리케이션 공급업체에 확인하세요.

- Azure 서비스와 통신하는 여러 운영 체제 및 언어 런타임은 다음과 같은 새 루트를 사용하여 인증서 체인을 올바르게 빌드하는 추가 단계가 필요할 수 있습니다. 
    - **Linux**: 여러 배포판에서 위에 나열된 CA를 /etc/ssl/certs에 추가해야 합니다. 자세한 지침은 해당 배포판의 설명서를 참조하세요.
    - **Java**: 위에 나열된 CA가 Java 키 저장소에 들어 있어야 합니다.
    - **연결이 끊어진 환경에서 실행되는 Windows**: 연결이 끊어진 환경에서 실행되는 시스템은 위에 나열된 루트를 신뢰할 수 있는 루트 인증 기관 저장소에 추가하고, 중간 인증서를 중간 인증 기관 저장소에 추가해야 합니다.
    - **Android**: 사용하는 디바이스의 설명서와 Android 버전을 확인합니다.
    - **기타 하드웨어 디바이스, 특히 IoT**: 디바이스 제조업체에 문의하세요. 

- 특정 CRL(인증서 해지 목록) 다운로드 및/또는 OCSP(온라인 인증서 상태 프로토콜) 확인 위치에 대한 아웃바운드 호출만 허용하도록 방화벽 규칙이 설정된 환경의 경우 다음 CRL 및 OCSP URL을 허용해야 합니다.

    - http://crl3&#46;digicert&#46;com
    - http://crl4&#46;digicert&#46;com
    - http://ocsp&#46;digicert&#46;com
    - http://www&#46;d-trust&#46;net
    - http://root-c3-ca2-2009&#46;ocsp&#46;d-trust&#46;net
    - http://crl&#46;microsoft&#46;com
    - http://oneocsp&#46;microsoft&#46;com
    - http://ocsp&#46;msocsp&#46;com

## <a name="june-2020"></a>2020년 6월

Key Vault용 Azure Monitor는 현재 미리 보기로 제공됩니다.  Azure Monitor는 Key Vault 요청, 성능, 실패 및 대기 시간에 대한 통합 보기를 제공하여 키 자격 증명 모음의 종합적인 모니터링을 제공합니다. 자세한 내용은 [Key Vault용 Azure Monitor(미리 보기)](../../azure-monitor/insights/key-vault-insights-overview.md)를 참조하세요.

## <a name="may-2020"></a>2020년 5월

이제 Key Vault "BYOK(Bring Your Own Key)"를 일반적으로 사용할 수 있습니다. [Azure Key Vault BYOK 사양](../keys/byok-specification.md)을 살펴보고, [Key Vault로 HSM 보호 키 가져오기(BYOK)](../keys/hsm-protected-keys-byok.md) 방법에 대해 알아보세요.

## <a name="march-2020"></a>2020년 3월

이제 미리 보기에서 프라이빗 엔드포인트를 사용할 수 있습니다. Azure Private Link Service를 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure Key Vault 및 Azure 호스팅 고객/파트너 서비스에 액세스할 수 있습니다.  [Azure Private Link와 Key Vault를 통합하는 방법](private-link-service.md)을 알아보세요.

## <a name="2019"></a>2019

- 차세대 Azure Key Vault SDK가 출시되었습니다. 사용 방법은 [Python](../secrets/quick-create-python.md), [.NET](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md) 및 [Node.js](../secrets/quick-create-node.md)에 대한 Azure Key Vault 비밀 빠른 시작을 참조하세요.
- 키 자격 증명 모음 인증서를 관리하는 새로운 Azure 정책. [Key Vault에 대한 Azure Policy 기본 제공 정의](../policy-samples.md)를 참조하세요.
- Azure Key Vault 가상 머신 확장은 이제 일반 공급됩니다.  [Linux용 Key Vault 가상 머신 확장](../../virtual-machines/extensions/key-vault-linux.md) 및 [Windows용 Key Vault 가상 머신 확장](../../virtual-machines/extensions/key-vault-windows.md)을 참조하세요.
- Azure Key Vault에 대한 이벤트 기반 비밀 관리는 현재 Azure Event Grid에서 사용할 수 있습니다. [Azure Key Vault의 이벤트에 대한 Event Grid 스키마](../../event-grid/event-schema-key-vault.md]에서 자세한 내용을 확인하고, [Azure Event Grid를 사용하여 키 자격 증명 모음 알림 수신 및 응답](event-grid-tutorial.md) 방법을 알아보세요.

## <a name="2018"></a>2018

올해 출시된 새로운 기능 및 통합:

- Azure Functions와 통합됩니다. 키 자격 증명 모음 작업에 [Azure Functions](../../azure-functions/index.yml)를 활용하는 예제 시나리오는 [비밀 순환 자동화](../secrets/tutorial-rotation.md)를 참조하세요. 
- [Azure Databricks와 통합](/azure/databricks/scenarios/store-secrets-azure-key-vault). 이 통합을 통해 이제 Azure Databricks는 두 가지 유형의 비밀 범위 Azure Key Vault 지원 및 Databricks 지원을 제공합니다. 자세한 내용은 [Azure Key Vault에서 지원하는 비밀 범위 만들기](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)를 참조하세요.
- [Azure Key Vault의 가상 네트워크 서비스 엔드포인트](overview-vnet-service-endpoints.md)

## <a name="2016"></a>2016

올해 출시된 새로운 기능:

- 관리형 스토리지 계정 키. Azure Storage와 더 쉽게 통합되는 스토리지 계정 키 기능을 추가했습니다. 자세한 내용은 개요 토픽 [관리 스토리지 계정 키 개요](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys)를 참조하세요.
- 일시 삭제. 일시 삭제 기능은 키 자격 증명 모음 및 키 자격 증명 모음 개체의 데이터 보호를 개선합니다. 자세한 내용은 [일시 삭제 개요](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 토픽을 참조하세요.

## <a name="2015"></a>2015

올해 출시된 새로운 기능:
- 인증서 관리. 2016년 9월 26일에 GA 버전 2015-06-01에 추가된 기능입니다.

일반 공급(버전 2015-06-01)은 2015년 6월 24일에 발표되었습니다. 이 릴리스에서 다음과 같은 사항이 변경되었습니다. 
- 키 삭제 - "사용" 필드를 제거했습니다.
- 키에 대한 정보 가져오기 - "사용" 필드를 제거했습니다.
- 키 자격 증명 모음에 키 가져오기 - "사용" 필드를 제거했습니다.
- 키 복원 - "사용" 필드를 제거했습니다.     
- RSA 알고리즘에 대한 "RSA_OAEP"를 "RSA-OAEP"로 변경했습니다. [키, 비밀 및 인증서에 대한 정보](about-keys-secrets-certificates.md)를 참조하세요.    
 
두 번째 미리보기 버전(2015-02-01-preview)은 2015년 4월 20일에 발표되었습니다. 자세한 내용은 [REST API 업데이트](https://docs.microsoft.com/archive/blogs/kv/rest-api-update)를 참조하세요. 다음 작업을 업데이트했습니다.
 
- 자격 증명 모음에 키 나열 - 작업에 페이지 매김 지원을 추가했습니다.
- 키 버전 나열 - 키 버전을 나열하는 작업을 추가했습니다.  
- 자격 증명 모음에 비밀 나열 - 페이지 매김 지원을 추가했습니다.
- 비밀 버전 나열 - 비밀 버전을 나열하는 작업을 추가합니다.  
- 모든 작업 - 특성에 만든/업데이트한 타임스탬프를 추가했습니다.  
- 비밀 만들기 - 비밀에 콘텐츠 형식을 추가했습니다.
- 키 만들기 - 선택 사항 정보로 태그를 추가했습니다.
- 비밀 만들기 - 선택 사항 정보로 태그를 추가했습니다.
- 키 업데이트 - 선택 사항 정보로 태그를 추가했습니다.
- 비밀 업데이트 - 선택 사항 정보로 태그를 추가했습니다.
- 비밀의 최대 크기를 10K에서 25K 바이트로 변경했습니다. [키, 비밀 및 인증서에 대한 정보](about-keys-secrets-certificates.md)를 참조하세요.    

## <a name="2014"></a>2014
 
첫 번째 미리보기 버전(2014-12-08-preview)은 2015년 1월 8일에 발표되었습니다.  

## <a name="next-steps"></a>다음 단계

추가 질문이 있는 경우 [지원](https://azure.microsoft.com/support/options/)을 통해 문의하시기 바랍니다.  
