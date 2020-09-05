---
title: Azure Active Directory에 대한 하이브리드 ID란?
description: 하이브리드 ID는 온-프레미스와 클라우드 모두에서 인증 및 권한 부여에 사용되는 공통 사용자 ID입니다.
keywords: Azure AD Connect 소개, Azure AD Connect 개요, Azure AD Connect 정의, active directory 설치
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f74798eeed4764bf2f7b71cb5049fb1c6b3ae077
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89278652"
---
# <a name="what-is-hybrid-identity-with-azure-active-directory"></a>Azure Active Directory에 대한 하이브리드 ID란?

오늘날의 비즈니스와 기업에서는 온-프레미스와 클라우드 애플리케이션이 점점 더 많이 혼합되고 있습니다.  사용자는 온-프레미스 및 클라우드 모두에서 이러한 애플리케이션에 액세스해야 합니다. 온-프레미스와 클라우드의 사용자를 관리하는 일은 까다롭습니다. 

Microsoft의 ID 솔루션은 온-프레미스 및 클라우드 기반 기능에 걸쳐 있습니다.  이러한 솔루션은 위치에 관계없이 모든 리소스에 대한 인증 및 권한 부여에 사용할 일반적인 사용자 ID를 만듭니다. 이를 **하이브리드 ID**라고 합니다.

Azure AD에 대한 하이브리드 ID와 하이브리드 ID 관리를 사용하면 이러한 시나리오가 지원됩니다.

Azure AD에서 하이브리드 ID를 구현하려면 시나리오에 따라 세 가지 인증 방법 중 하나를 사용하면 됩니다.   세 가지 방법은 다음과 같습니다. 

- **[PHS(암호 해시 동기화)](whatis-phs.md)**  
- **[PTA(통과 인증)](how-to-connect-pta.md)**  
- **[페더레이션(AD FS)](whatis-fed.md)** 

또한 이러한 인증 방법은 [Single Sign-On](how-to-connect-sso.md) 기능도 제공합니다.  Single Sign-On은 회사 네트워크에 연결된 회사 디바이스에 있을 때 사용자를 자동으로 서명합니다.

자세한 내용은 [Azure Active Directory 하이브리드 ID 솔루션에 적합한 인증 방법 선택](./choose-ad-authn.md)을 참조하세요. 

## <a name="common-scenarios-and-recommendations"></a>일반적인 시나리오 및 권장 사항 

다음은 몇 가지 일반적인 하이브리드 ID 및 액세스 관리 시나리오와 각 시나리오에 적합한 하이브리드 ID 옵션(하나 또는 여러 개)에 대한 권장 사항입니다. 

|수행 작업:|PHS 및 SSO<sup>1</sup>| PTA 및 SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|온-프레미스 Active Directory에서 만든 새 사용자, 연락처 및 그룹 계정을 클라우드에 자동으로 동기화|![권장](./media/whatis-hybrid-identity/ic195031.png)| ![권장](./media/whatis-hybrid-identity/ic195031.png) |![권장](./media/whatis-hybrid-identity/ic195031.png)| 
|Office 365 하이브리드 시나리오에 대한 테넌트를 설정합니다.|![권장](./media/whatis-hybrid-identity/ic195031.png)| ![권장](./media/whatis-hybrid-identity/ic195031.png) |![권장](./media/whatis-hybrid-identity/ic195031.png)| 
|사용자가 자신의 온-프레미스 암호를 사용하여 로그인하고 클라우드 서비스에 액세스할 수 있도록 합니다.|![권장](./media/whatis-hybrid-identity/ic195031.png)| ![권장](./media/whatis-hybrid-identity/ic195031.png) |![권장](./media/whatis-hybrid-identity/ic195031.png)| 
|회사 자격 증명을 사용하여 Single Sign-On을 구현합니다.|![권장](./media/whatis-hybrid-identity/ic195031.png)| ![권장](./media/whatis-hybrid-identity/ic195031.png) |![권장](./media/whatis-hybrid-identity/ic195031.png)|  
|클라우드에 암호 해시가 저장되지 않도록 합니다.| |![권장](./media/whatis-hybrid-identity/ic195031.png)|![권장](./media/whatis-hybrid-identity/ic195031.png)| 
|클라우드 기반 다단계 인증 솔루션을 사용하도록 설정합니다.|![권장](./media/whatis-hybrid-identity/ic195031.png)|![권장](./media/whatis-hybrid-identity/ic195031.png)|![권장](./media/whatis-hybrid-identity/ic195031.png)| 
|온-프레미스 Multi-Factor Authentication 솔루션을 사용합니다.| | |![권장](./media/whatis-hybrid-identity/ic195031.png)| 
|사용자에 대한 스마트 카드 인증을 지원합니다.<sup>4</sup>| | |![권장](./media/whatis-hybrid-identity/ic195031.png)| 
|Office 포털 및 Windows 10 바탕 화면에 암호 만료 알림을 표시합니다.| | |![권장](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Single Sign-On을 사용하여 암호 해시 동기화 
> 
> <sup>2</sup> 통과 인증 및 Single Sign-On  
> 
> <sup>3</sup> AD FS를 통해 페더레이션된 Single Sign-On  
>  
> <sup>4</sup> AD FS는 엔터프라이즈 PKI와 통합되어 인증서를 사용한 로그인을 허용할 수 있습니다. 이러한 인증서는 MDM 또는 GPO와 같은 신뢰할 수 있는 프로비전 채널이나 스마트 카드 인증서(PIV/CAC 카드 포함) 또는 비즈니스용 Hello(cert-trust)를 통해 배포되는 소프트 인증서일 수 있습니다. 스마트 카드 인증 지원에 대한 자세한 내용은 [이 블로그](/archive/blogs/samueld/adfs-certauth-aad-o365)를 참조하세요. 
> 

## <a name="license-requirements-for-using-azure-ad-connect"></a>Azure AD Connect 사용을 위한 라이선스 요구 사항

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>다음 단계 

- [Azure AD Connect 및 Connect Health란?](whatis-azure-ad-connect.md) 
- [PHS(암호 해시 동기화)란?](whatis-phs.md) 
- [PTA(통과 인증)란?](how-to-connect-pta.md) 
- [페더레이션이란?](whatis-fed.md) 
- [Single Sign-On이란?](how-to-connect-sso.md)