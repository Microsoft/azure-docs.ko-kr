---
title: TLS 1.2 적용-Azure Active Directory 등록 서비스
description: Azure AD 장치 등록 서비스에 대 한 TLS 1.0 및 1.1 지원 제거
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 07/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bb8c6c64e0a68f5176c4eb0c0177c5220394695
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268760"
---
# <a name="enforce-tls-12-for-the-azure-ad-registration-service"></a>Azure AD 등록 서비스에 대 한 TLS 1.2 적용

장치 id를 사용 하 여 장치를 클라우드에 연결 하는 데는 Azure AD (Azure Active Directory) 장치 등록 서비스가 사용 됩니다. Azure AD 장치 등록 서비스는 현재 Azure와의 통신에 TLS (Transport Layer Security) 1.2을 사용할 수 있도록 지원 합니다. 보안 및 최고의 암호화를 보장 하려면 TLS 1.0 및 1.1을 사용 하지 않도록 설정 하는 것이 좋습니다. 이 문서에서는 등록을 완료 하 고 Azure AD 장치 등록 서비스와 통신 하는 데 사용 되는 컴퓨터를 확인 하는 방법에 대 한 정보를 제공 TLS 1.2를 사용 합니다.

TLS 프로토콜 버전 1.2는 보안 통신을 제공 하도록 설계 된 암호화 프로토콜입니다. TLS 프로토콜은 주로 개인 정보 보호 및 데이터 무결성을 제공하는 것을 목표로 합니다. TLS는 [RFC 5246 (외부 링크)](https://tools.ietf.org/html/rfc5246)에서 버전 1.2이 정의 된 많은 반복을 통해 왔습니다.

현재 연결 분석에는 TLS 1.1 및 1.0 사용량이 거의 표시 되지 않지만 TLS 1.1 및 1.0에 대 한 지원이 종료 되기 전에 필요한 대로 영향을 받는 클라이언트 또는 서버를 업데이트할 수 있도록이 정보를 제공 하 고 있습니다. 하이브리드 시나리오 또는 Active Directory Federation Services (AD FS)에 대 한 온-프레미스 인프라를 사용 하는 경우 인프라에서 TLS 1.2를 사용 하는 인바운드 및 아웃 바운드 연결을 모두 지원할 수 있는지 확인 합니다.

## <a name="update-windows-servers"></a>Windows 서버 업데이트

Azure AD Device Registration Service를 사용 하는 Windows 서버 또는 프록시 역할을 하는 경우 다음 단계를 사용 하 여 TLS 1.2를 사용 하도록 설정 합니다.

> [!IMPORTANT]
> 레지스트리를 업데이트 한 후 변경 내용을 적용 하려면 Windows server를 다시 시작 해야 합니다.

### <a name="enable-tls-12"></a>TLS 1.2 사용

다음과 같이 레지스트리 문자열이 구성 되어 있는지 확인 합니다.

- HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ 클라이언트
  - "DisabledByDefault" = dword: 00000000
  - "Enabled" = dword: 00000001
- HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Server
  - "DisabledByDefault" = dword: 00000000
  - "Enabled" = dword: 00000001
- HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft \. NETFramework\v4.0.30319
  - "SchUseStrongCrypto"=dword:00000001

## <a name="update-non-windows-proxies"></a>비 Windows 프록시 업데이트

장치와 Azure AD 장치 등록 서비스 간의 프록시로 작동 하는 모든 컴퓨터는 TLS 1.2을 사용 하도록 설정 되어 있어야 합니다. 공급 업체의 지침에 따라 지원을 확인 합니다.

## <a name="update-ad-fs-servers"></a>AD FS 서버 업데이트

Azure AD 장치 등록 서비스와 통신 하는 데 사용 되는 모든 AD FS 서버에서 TLS 1.2를 사용 하도록 설정 해야 합니다. 이 구성을 사용/확인 하는 방법에 대 한 자세한 내용은 [SSL/TLS 프로토콜 및 암호 AD FS 그룹 관리](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) 를 참조 하세요.

## <a name="client-updates"></a>클라이언트 업데이트

모든 클라이언트 서버와 브라우저 서버 조합에서 TLS 1.2를 사용 하 여 Azure AD 장치 등록 서비스에 연결 해야 하므로 이러한 장치를 업데이트 해야 할 수 있습니다.

다음 클라이언트는 TLS 1.2를 지원할 수 없는 것으로 알려져 있습니다. 클라이언트를 업데이트 하 여 중단 없이 액세스할 수 있도록 합니다.

- Android 버전 4.3 및 이전 버전
- Firefox 버전 5.0 및 이전 버전
- Windows 7 및 이전 버전의 Internet Explorer 버전 8-10
- Windows Phone 8.0의 Internet Explorer 10
- OS X 10.8.4 및 이전 버전의 Safari 버전 6.0.4

## <a name="next-steps"></a>다음 단계

[TLS/SSL 개요 (Schannel SSP)](/windows-server/security/tls/tls-ssl-schannel-ssp-overview)