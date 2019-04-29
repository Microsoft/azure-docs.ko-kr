---
title: Key Vault 인증서 시작
description: 다음과 같은 시나리오는 키 자격 증명 모음에서 첫 번째 인증서를 만드는 데 필요한 추가 단계를 포함하여 몇 가지 Key Vault의 인증서 관리 서비스의 기본 사용을 간략하게 설명합니다.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 20c05bddddce4c7748e29551fe78d3e5609b2fa5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60641044"
---
# <a name="get-started-with-key-vault-certificates"></a>Key Vault 인증서 시작
다음과 같은 시나리오는 키 자격 증명 모음에서 첫 번째 인증서를 만드는 데 필요한 추가 단계를 포함하여 몇 가지 Key Vault의 인증서 관리 서비스의 기본 사용을 간략하게 설명합니다.

다음이 설명되어 있습니다.
- 첫 번째 Key Vault 인증서 만들기
- Key Vault와 협력하는 인증 기관으로 인증서 만들기
- Key Vault와 협력하지 않는 인증 기관으로 인증서 만들기
- 인증서 가져오기

## <a name="certificates-are-complex-objects"></a>인증서는 복잡한 개체입니다.
인증서는 Key Vault 인증서로 함께 연결된 세 가지 서로 관련된 리소스인, 인증서 메타데이터, 키 및 비밀로 구성됩니다.


![인증서는 복잡합니다.](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>첫 번째 Key Vault 인증서 만들기  
 KV(Key Vault)에서 인증서를 만들려면 필수 구성 요소 1단계 및 2단계를 성공적으로 수행하고 이 사용자/조직에 대해 키 자격 증명 모음이 존재해야 합니다.  

**1단계** - CA(인증 기관) 공급자  
-   지정된 회사(예: Contoso)에 대해 IT 관리자, PKI 관리자로 등록하거나 CA로 계정을 관리하는 것은 Key Vault 인증서를 사용하는 필수 구성 요소입니다.  
    다음 CA는 Key Vault와 현재 협력하는 공급자입니다.  
    -   DigiCert - Key Vault는 DigiCert를 통해 OV SSL 인증서를 제공합니다.  
    -   GlobalSign - Key Vault는 GlobalSign을 통해 OV SSL 인증서를 제공합니다.  
    -   WoSign - Key Vault는 WoSign 포털의 해당 WoSign 계정에서 고객에 의해 구성된 설정을 기반으로 WoSign을 통해 OV SSL 또는 EV SSL 인증서를 제공합니다.  

**2단계** - CA 공급자에 대한 계정 관리자는 Key Vault를 통해 SSL 인증서를 등록, 갱신 및 사용하도록 Key Vault에서 사용할 자격 증명을 만듭니다.

**3단계** - CA에 따라 인증서를 소유하는 Contoso 직원(Key Vault 사용자)과 함께 Contoso 관리자는 관리자로부터 또는 CA로 계정에서 직접 인증서를 가져올 수 있습니다.  

- [인증서 발급자 리소스를 설정](/rest/api/keyvault/setcertificateissuer/setcertificateissuer)하여 Key Vault에 자격 증명 작업 추가를 시작합니다. 인증서 발급자는 Azure KV(Key Vault)에 CertificateIssuer 리소스로 표시되는 엔터티입니다. KV 인증서의 원본에 대한 정보(발급자 이름, 공급자, 자격 증명 및 기타 관리 세부 정보)를 제공하는 데 사용됩니다.
  - 예: MyDigiCertIssuer  
    -   공급자  
    -   자격 증명 - CA 계정 자격 증명입니다. 각 CA에는 자체 특정 데이터가 있습니다.  

    CA 공급자로 계정을 만드는 방법에 대한 자세한 내용은 [Key Vault 블로그](https://aka.ms/kvcertsblog)에서 관련된 게시물을 참조하세요.  

**3.1단계** - 알림에 대한 [인증서 연락처](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts)를 설정합니다. Key Vault 사용자에 대한 연락처입니다. Key Vault는 이 단계를 적용하지 않습니다.  

참고 - 3.1단계를 통한 이 프로세스의 경우 일회성 작업입니다.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Key Vault와 협력하는 CA를 통해 인증서 만들기

![Key Vault 파트너 인증 기관을 통해 인증서 만들기](media/certificate-authority-2.png)

**4단계** - 다음 설명은 위의 다이어그램에서 녹색 숫자로 된 단계에 해당합니다.  
  (1) - 위의 다이어그램에서 애플리케이션은 내부적으로 키 자격 증명 모음에서 키를 만드는 작업으로 시작하는 인증서를 만듭니다.  
  (2) - Key Vault에서 SSL 인증서 요청을 CA에 보냅니다.  
  (3) - 애플리케이션이 인증서 완료를 위해 Key Vault에 대해 반복 및 대기 프로세스에서 폴링합니다. Key Vault가 x509 인증서를 통해 CA의 응답을 수신하는 경우 인증서 만들기가 완료됩니다.  
  (4) - CA가 X509 SSL 인증서를 사용하여 Key Vault의 SSL 인증서 요청에 응답합니다.  
  (5) - 새 인증서 만들기는 CA에 대한 X509 인증서를 병합하여 완료합니다.  

  Key Vault 사용자 - 정책을 지정하여 인증서를 만듭니다.

  -   필요에 따라 반복  
  -   정책 제약 조건  
      -   X509 속성  
      -   키 속성  
      -   공급자 참조 -> 예 MyDigiCertIssure  
      -   갱신 정보 -> 예 만료 90일 전  

  - 인증서 만들기 프로세스는 일반적으로 비동기 프로세스이며 인증서 만들기 작업의 상태에 대한 키 자격 증명 모음 폴링을 포함합니다.  
[인증서 작업 가져오기](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   상태: 완료, 오류 정보와 함께 실패 또는 취소  
      -   만들기에 대한 지연으로 인해 취소 작업을 시작할 수 있습니다. 취소가 적용되거나 적용되지 않을 수 있습니다.  

## <a name="import-a-certificate"></a>인증서 가져오기  
 또는 - Key Vault로 인증서를 가져올 수 있습니다(PFX 또는 PEM).  

 PEM 형식에 대한 자세한 내용은 [키, 비밀 및 인증서 정보](about-keys-secrets-and-certificates.md)의 인증서 섹션을 참조하세요.  

 인증서 가져오기 – 디스크에 PEM 또는 PFX가 있어야 하며 개인 키가 필요합니다. 
-   자격 증명 모음 이름 및 인증서 이름을 지정해야 합니다(정책은 선택 사항).

-   PEM / PFX 파일은 KV에서 구문 분석하고 인증서 정책을 채우는 데 사용할 수 있는 특성을 포함합니다. 인증서 정책이 이미 지정된 경우 KV는 PFX / PEM 파일에서 데이터를 일치시키려고 합니다.  

-   가져오기가 완료되면 후속 작업에서 새 정책을 사용합니다(새 버전).  

-   추가 작업이 없는 경우 Key Vault에서 수행하는 첫 번째 작업은 만료 경고를 보내는 것입니다. 

-   또한 사용자는 가져오기 시 사용할 수 있지만 가져오기 시 정보가 지정되지 않은 기본값을 포함하는 정책을 편집할 수 있습니다. 예: 발급자 정보 없음  

### <a name="formats-of-import-we-support"></a>지원 되는 가져오기 형식
PEM 파일 형식에 대 한 다음과 같은 유형의 가져오기 지원합니다. PKCS # 8로 인코딩된, 다음에는 암호화 되지 않은 키와 함께 단일 PEM 인코딩 인증서

-----BEGIN CERTIFICATE----- -----END CERTIFICATE-----

---BEGIN PRIVATE KEY------END PRIVATE KEY--

인증서 병합 2 기반 PEM 형식을 지원합니다. 단일 PKCS # 8로 인코딩된 인증서를 하거나 병합할 수 있습니다 또는 base64로 인코딩된 P7B 파일입니다. -----BEGIN CERTIFICATE----- -----END CERTIFICATE-----

현재 PEM 형식으로 EC 키를 지원 하지 않습니다.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Key Vault와 협력하지 않는 CA를 통해 인증서 만들기  
 이 방법을 통해 Key Vault의 파트너 공급자가 아닌 다른 CA와 작업할 수 있습니다. 즉, 조직은 선택한 CA와 작업할 수 있습니다.  

![사용자 고유의 인증 기관을 통해 인증서 만들기](media/certificate-authority-1.png)  

 다음 단계는 위의 다이어그램에서 녹색 글자로 된 단계에 해당합니다.  

  (1) - 위의 다이어그램에서 애플리케이션은 내부적으로 키 자격 증명 모음에서 키를 만드는 작업으로 시작하는 인증서를 만듭니다.  

  (2) - Key Vault는 애플리케이션에 CSR(Certificate Signing Request)을 반환합니다.  

  (3) - 애플리케이션에서 CSR을 선택한 CA에 전달합니다.  

  (4) - 선택한 CA가 X509 인증서로 응답합니다.  

  (5) - 애플리케이션이 CA에서 X509 인증서를 병합해 새로운 인증서 만들기를 완료합니다.

## <a name="see-also"></a>관련 항목

- [키, 비밀 및 인증서에 대한 정보](about-keys-secrets-and-certificates.md)
