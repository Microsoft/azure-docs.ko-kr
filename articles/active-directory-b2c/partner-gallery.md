---
title: Azure AD B2C에 대 한 ISV 파트너 갤러리
titleSuffix: Azure AD B2C
description: ISV 파트너와 통합 하 여 사용자의 요구에 맞게 최종 사용자 환경을 조정 하는 방법을 알아봅니다. 파트너 네트워크는 솔루션 기능을 확장 합니다. MFA 사용, 보안 고객 인증, 역할 기반 Access Control Id 확인 교정을 통해 사기 행위를 방지 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ec1aa165b8fa5d3a249cb525887acf2990136bd3
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049872"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>ISV 파트너 Azure Active Directory B2C

ISV 파트너 네트워크는 원활한 최종 사용자 환경을 구축 하는 데 도움이 되는 솔루션 기능을 확장 합니다. Azure AD B2C를 사용 하면 ISV 파트너와 통합 하 여 multi-factor authentication 방법을 사용 하도록 설정 하 고, SCA (Secure Customer Authentication)를 사용 하도록 설정 하 고, 역할 기반 Access Control를 수행 하 고, id 확인 및 교정을 통해 사기 행위를 수행할 수 있습니다. 아래에 나열 된 ISV 파트너와 앱을 통합 하는 방법을 알아보려면 자세한 샘플 연습을 사용 하세요.

>[!NOTE]
>[GitHub의 Azure Active Directory B2C 커뮤니티 사이트](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/) 는 커뮤니티의 샘플 사용자 지정 정책을 제공 합니다.

## <a name="integration-isv-partners"></a>통합 ISV 파트너

| ISV 파트너 | 설명 및 통합 연습  |
| :--- | :--- |
| ![로고](./media/partner-gallery/arkose-logo.png) | [Arkose Labs](./partner-arkose-labs.md) 는 조직이 봇 공격, 계정 인수 공격 및 사기성 계정 입구를 방지 하는 데 도움이 되는 사기 행위 방지 솔루션 공급자입니다.
| ![로고](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) 는 사기 행위를 방지 하기 위해 사용자 특성을 기반으로 위험 평가를 수행 하는 id 확인 및 교정 공급자입니다.|
| ![로고](./media/partner-gallery/hypr-logo.png) | [HYPR](./partner-hypr.md) 는 암호를 공개 키 암호화로 바꿔서 사기 행위, 피싱 및 자격 증명 재사용을 제거 하는 진정한 암호 없는 인증 공급자입니다.|
| ![로고](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md) 는 id 확인 솔루션, 사기 행위 방지 솔루션, 규정 준수 솔루션 및 기타를 제공 하는 id 확인 및 교정 공급자입니다.|
| ![로고](./media/partner-gallery/itsme-logo.png) | [itsme](./partner-itsme.md) 는 전자 Id, 인증 및 신뢰 서비스 (eidas) 호환 디지털 ID 솔루션으로, 사용자가 카드 판독기, 암호, 2 단계 인증 및 여러 PIN 코드 없이 안전 하 게 로그인 할 수 있도록 합니다. |
| ![로고](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) 는 id 확인 서비스 이며,이 서비스를 사용 하면 실시간 자동화 id를 확인 하 고 고객 데이터를 보호 합니다. |
| ![로고](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) 은 사용자 id를 확인 하 고 사용자의 장치에 따라 포괄적인 위험 평가를 제공 하는 프로 파일링 및 id 유효성 검사 공급자입니다. |
| ![로고](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md) 는 회사에서 고객 및 id 요구 사항을 실시간으로 *파악* 하는 데 사용할 수 있는 문서 ID 및 얼굴 인식 확인 솔루션입니다.  |
| ![로고](./media/partner-gallery/trusona-logo.png) | [Trusona](./partner-trusona.md) 통합을 사용 하면 안전 하 게 로그인 하 고 암호 없는 인증, multi-factor authentication 및 디지털 라이선스 검색을 사용할 수 있습니다.|
| ![로고](./media/partner-gallery/twilio-logo.png) | [Twilio Verify 앱](./partner-twilio.md) 은 MFA (multi-factor authentication)를 사용 하 여 SMS OTP (일회성 암호), 시간 기반 일회용 암호 (totp) 및 푸시 알림을 사용 하도록 설정 하 고, 결제 서비스 지시어 2 (PSD2)에 대 한 SCA 요구 사항을 준수 하는 여러 솔루션을 제공 합니다.|
| ![로고](./media/partner-gallery/typingdna-logo.png) | [Typingdna](./partner-typingdna.md) 는 사용자 입력 패턴을 기반으로 하는 id 확인 및 교정 공급자 이며, id 확인 솔루션을 사용 하 여 multi-factor authentication을 강제 적용 하 고, 결제 서비스 지시어 2 (PSD2)에 대 한 SCA 요구 사항을 준수 하는 데 도움이 됩니다. |
| ![로고](./media/partner-gallery/whoiam-logo.png) | [WhoIAM](./partner-whoiam.md) 는 조직이 음성, SMS 및 전자 메일을 기준으로 사용자를 확인할 수 있도록 하는 BRIMS (브랜드 Identity Management System) 응용 프로그램입니다. 

## <a name="next-steps"></a>다음 단계

위의 표에서 파트너를 선택 하 여 솔루션을 Azure AD B2C와 통합 하는 방법을 알아보세요.

추가 정보:

- [GitHub의 B2C 커뮤니티 사이트](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/)
- [Azure AD B2C에 대 한 사용자 지정 정책](custom-policy-overview.md)
