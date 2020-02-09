---
title: 표시 컨트롤을 사용 하 여 클레임 확인
titleSuffix: Azure AD B2C
description: Azure AD B2C 표시 하는 컨트롤을 사용 하 여 사용자 지정 정책에서 제공 하는 사용자 경험의 클레임을 확인 하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4d7deb02e28734eb2b4c42bad566fdfd0d380682
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75479086"
---
# <a name="verification-display-control"></a>확인 표시 컨트롤

확인 [표시 컨트롤](display-controls.md) 을 사용 하 여 전자 메일 주소 또는 전화 번호와 같이 사용자에 게 전송 된 인증 코드를 사용 하 여 클레임을 확인 합니다.

## <a name="verificationcontrol-actions"></a>VerificationControl 작업

확인 표시 컨트롤은 다음 두 단계로 구성 됩니다 (작업).

1. 확인 코드를 보내야 하는 전자 메일 주소 또는 전화 번호와 같은 사용자의 대상을 요청 합니다. 사용자가 **코드 보내기** 단추를 선택 하면 확인 표시 컨트롤의 **sendcode 동작이** 실행 됩니다. **Sendcode 작업** 은 코드를 생성 하 고, 전송할 콘텐츠를 생성 하 고, 사용자에 게 보냅니다. 주소 값은 미리 채워질 수 있으며 두 번째 단계 인증으로 사용할 수 있습니다.

    ![코드 보내기 작업에 대한 예제 페이지](media/display-control-verification/display-control-verification-email-action-01.png)

1. 코드를 보낸 후 사용자는 메시지를 읽고, 표시 컨트롤에서 제공 하는 컨트롤에 확인 코드를 입력 하 고, **코드 확인**을 선택 합니다. **코드 확인**을 선택 하면 해당 주소와 연결 된 코드를 확인 하기 위해 **verifycode 작업이** 실행 됩니다. 사용자가 **새 코드 보내기**를 선택 하면 첫 번째 작업이 다시 실행 됩니다.

    ![코드 확인 작업에 대한 예제 페이지](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>VerificationControl 필수 요소

**VerificationControl** 은 다음 요소를 포함 해야 합니다.

- `DisplayControl` 형식이 `VerificationControl`입니다.
- `DisplayClaims`
  - 로 **보내기** -인증 코드를 보낼 위치를 지정 하는 하나 이상의 클레임입니다. 예를 들어 *전자 메일* 또는 *국가 코드* 와 *전화 번호*를 사용할 수 있습니다.
  - **확인 코드** -코드를 보낸 후 사용자가 제공 하는 확인 코드 클레임입니다. 이 클레임은 필수로 설정 해야 하며 `ControlClaimType` `VerificationCode`로 설정 되어야 합니다.
- 사용자가 확인 프로세스를 완료 한 후 자체 어설션된 페이지로 반환 될 출력 클레임 (선택 사항)입니다. 예를 들어 *전자 메일* 또는 *국가 코드* 와 *전화 번호*를 사용할 수 있습니다. 자체 어설션된 기술 프로필은 클레임을 사용 하 여 데이터를 유지 하거나 출력 클레임을 다음 오케스트레이션 단계로 버블링 합니다.
- 다음 이름의 두 `Action`s:
  - **Sendcode** -사용자에 게 코드를 보냅니다. 이 작업에는 일반적으로 코드를 생성 하 고 보내는 두 가지 유효성 검사 기술 프로필이 포함 됩니다.
  - **Verifycode** -코드를 확인 합니다. 이 작업에는 일반적으로 단일 유효성 검사 기술 프로필이 포함 됩니다.

아래 예제에서는 페이지에 **전자 메일** 텍스트 상자가 표시 됩니다. 사용자가 전자 메일 주소를 입력 하 고 **sendcode**를 선택 하면 Azure AD B2C 백 엔드에서 **sendcode** 동작이 트리거됩니다.

그런 다음 사용자가 **verificationCode** 를 입력 하 고 **verifycode** 를 선택 하 여 백 엔드에서 **verifycode** 작업을 트리거합니다. 모든 유효성 검사가 통과 하면 **VerificationControl** 가 완료 된 것으로 간주 되 고 사용자는 다음 단계를 계속 진행할 수 있습니다.

```XML
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
