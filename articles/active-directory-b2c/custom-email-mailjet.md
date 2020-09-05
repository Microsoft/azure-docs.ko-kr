---
title: Mailjet를 사용 하 여 사용자 지정 전자 메일 확인
titleSuffix: Azure AD B2C
description: Azure AD B2C 지원 응용 프로그램을 사용 하기 위해 등록할 때 고객에 게 전송 된 확인 전자 메일을 사용자 지정 하기 위해 Mailjet와 통합 하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 29e82a67b85356cfc15e806bb331330b3f272a04
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88584967"
---
# <a name="custom-email-verification-with-mailjet"></a>Mailjet를 사용 하 여 사용자 지정 전자 메일 확인

Azure Active Directory B2C (Azure AD B2C)의 사용자 지정 전자 메일을 사용 하 여 응용 프로그램을 사용 하기 위해 등록 하는 사용자에 게 사용자 지정 전자 메일을 보냅니다. [Displaycontrols](display-controls.md) (현재 미리 보기 상태) 및 타사 전자 메일 공급자 mailjet를 사용 하 여 사용자 고유의 전자 메일 템플릿 및 *의* 주소와 주체를 사용할 수 있을 뿐만 아니라 지역화 및 사용자 지정 OTP (일회용 암호) 설정을 지원할 수 있습니다.

사용자 지정 전자 메일을 확인 하려면 [Mailjet](https://Mailjet.com), [SendGrid](custom-email.md)또는 [SparkPost](https://sparkpost.com)와 같은 타사 전자 메일 공급자, 사용자 지정 REST API 또는 HTTP 기반 메일 공급자 (자체 포함)를 사용 해야 합니다. 이 문서에서는 Mailjet를 사용 하는 솔루션을 설정 하는 방법을 설명 합니다.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-mailjet-account"></a>Mailjet 계정 만들기

아직 없는 경우 Mailjet 계정을 설정 하 여 시작 합니다 (Azure 고객은 200 메일/일 제한으로 6000 메일의 잠금을 해제할 수 있음). 

1. [Mailjet 계정 만들기](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/)의 설치 지침을 따릅니다.
1. 전자 메일을 보내고, 발신자 전자 메일 주소나 도메인을 [등록 하 고 유효성을 검사할](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/#how-to-configure-mailjet-for-use) 수 있습니다.
2. [API 키 관리 페이지로](https://app.mailjet.com/account/api_keys)이동 합니다. 이후 단계에서 사용할 **API 키** 와 **비밀 키** 를 기록 합니다. 계정을 만들 때 두 키가 자동으로 생성 됩니다.  

## <a name="create-azure-ad-b2c-policy-key"></a>Azure AD B2C 정책 키 만들기

그런 다음 참조할 정책에 대 한 Azure AD B2C 정책 키에 Mailjet API 키를 저장 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **개요** 페이지에서 **Id 경험 프레임 워크**를 선택 합니다.
1. **정책 키**, **추가**를 차례로 선택합니다.
1. **옵션**에 대해 **수동**을 선택 합니다.
1. 정책 키의 **이름**을 입력합니다. 예들 들어 `MailjetApiKey`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. **비밀**에서 이전에 기록한 Mailjet **API 키** 를 입력 합니다.
1. **키 사용**으로는 **서명**을 선택합니다.
1. **만들기**를 선택합니다.
1. **정책 키**, **추가**를 차례로 선택합니다.
1. **옵션**에 대해 **수동**을 선택 합니다.
1. 정책 키의 **이름**을 입력합니다. 예들 들어 `MailjetSecretKey`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. **비밀**에서 이전에 기록한 Mailjet **비밀 키** 를 입력 합니다.
1. **키 사용**으로는 **서명**을 선택합니다.
1. **만들기**를 선택합니다.

## <a name="create-a-mailjet-template"></a>Mailjet 템플릿 만들기

Mailjet 계정이 만들어지고 Mailjet API 키가 Azure AD B2C 정책 키에 저장 된 상태에서 Mailjet [동적 트랜잭션 템플릿을](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)만듭니다.

1. Mailjet 사이트에서 [트랜잭션 템플릿](https://app.mailjet.com/templates/transactional) 페이지를 열고 **새 템플릿 만들기**를 선택 합니다.
1. **HTML로 코딩 하 여**선택한 다음, **처음부터 코드**를 선택 합니다.
1. 과 같은 고유한 템플릿 이름을 입력 한 `Verification email` 다음 **만들기**를 선택 합니다.
1. HTML 편집기에서 다음 HTML 템플릿을 붙여넣거나 사용자 고유의를 사용 합니다. `{{var:otp:""}}`및 `{{var:email:""}}` 매개 변수는 일회성 암호 값 및 사용자 전자 메일 주소를 사용 하 여 동적으로 교체 됩니다.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{var:email:""}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{var:otp:""}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. 왼쪽 맨 위에 있는 **제목 편집** 을 확장 합니다.
    1. **제목**에 대해 제목의 기본값을 입력 합니다. Mailjet는 API에 주체 매개 변수가 포함 되지 않은 경우이 값을 사용 합니다.
    1. **이름**에 회사 이름을 입력 합니다.
    1. **주소**에서 전자 메일 주소를 선택 합니다.
    1. **저장**을 선택합니다.
1. 오른쪽 맨 위에 있는 **저장 & 게시**를 선택 하 고 **예, 변경 내용 게시** 를 클릭 합니다.
1. 이후 단계에서 사용 하기 위해 만든 템플릿의 **템플릿 ID** 를 기록 합니다. [클레임 변환을 추가할](#add-the-claims-transformation)때이 ID를 지정 합니다.


## <a name="add-azure-ad-b2c-claim-types"></a>Azure AD B2C 클레임 유형 추가

정책에서 내의 요소에 다음 클레임 유형을 추가 합니다 `<ClaimsSchema>` `<BuildingBlocks>` .

이러한 클레임 유형은 OTP (일회용 암호) 코드를 사용 하 여 전자 메일 주소를 생성 및 확인 하는 데 필요 합니다.

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="emailRequestBody">
  <DisplayName>Mailjet request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>클레임 변환 추가

다음으로는 Mailjet로 전송 된 요청의 본문으로 사용할 JSON 문자열 클레임을 출력 하는 클레임 변환이 필요 합니다.

JSON 개체의 구조는 InputParameters의 점 표기법과 InputClaims의 TransformationClaimTypes Id로 정의 됩니다. 점 표기법의 숫자는 배열을 의미 합니다. 값은 InputClaims의 값과 InputParameters ' "Value" 속성에서 제공 됩니다. JSON 클레임 변환에 대 한 자세한 내용은 [json 클레임 변환](json-transformations.md)을 참조 하세요.

내의 요소에 다음 클레임 변환을 추가 합니다 `<ClaimsTransformations>` `<BuildingBlocks>` . 클레임 변환 XML에 대 한 다음 업데이트를 수행 합니다.

* `Messages.0.TemplateID`InputParameter 값을 앞에서 [Mailjet 템플릿 만들기](#create-a-mailjet-template)에서 만든 MAILJET 트랜잭션 템플릿의 ID로 업데이트 합니다.
* `Messages.0.From.Email`주소 값을 업데이트 합니다. 유효한 전자 메일 주소를 사용 하 여 확인 전자 메일이 스팸으로 표시 되는 것을 방지할 수 있습니다.
* `Messages.0.Subject`제목 줄 입력 매개 변수의 값을 조직에 적절 한 제목 줄로 업데이트 합니다.

```XML
<ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your Mailjet template. -->
    <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
    <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

    <!-- Update with an email appropriate for your organization. -->
    <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>

    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="Messages.0.Subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>DataUri 콘텐츠 정의 추가

내의 클레임 변환 아래 `<BuildingBlocks>` 에서 다음 [contentdefinition](contentdefinitions.md) 을 추가 하 여 버전 2.0.0 데이터 URI를 참조 합니다.

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>DisplayControl 만들기

확인 표시 컨트롤은 사용자에 게 전송 되는 확인 코드를 사용 하 여 전자 메일 주소를 확인 하는 데 사용 됩니다.

이 예제 표시 컨트롤은 다음으로 구성 됩니다.

1. `email`사용자의 주소 클레임 유형을 수집 합니다.
1. 사용자가 `verificationCode` 사용자에 게 보낸 코드를 사용 하 여 클레임 형식을 제공할 때까지 기다립니다.
1. `email`이 표시 컨트롤에 대 한 참조를 포함 하는 자체 어설션된 기술 프로필에를 반환 합니다.
1. 작업을 사용 하 여 `SendCode` otp 코드를 생성 하 고 otp 코드가 포함 된 전자 메일을 사용자에 게 보냅니다.

   ![확인 코드 전자 메일 보내기 작업](media/custom-email-mailjet/display-control-verification-email-action-01.png)

콘텐츠 정의에서 여전히 내에 `<BuildingBlocks>` [VerificationControl](display-control-verification.md) 형식의 다음 [DisplayControl](display-controls.md) 를 정책에 추가 합니다.

```XML
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendOtp" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>OTP 기술 프로필 추가

`GenerateOtp`기술 프로필은 전자 메일 주소에 대 한 코드를 생성 합니다. `VerifyOtp`기술 프로필은 전자 메일 주소와 연결 된 코드를 확인 합니다. 형식의 구성과 일회용 암호의 만료를 변경할 수 있습니다. OTP 기술 프로필에 대 한 자세한 내용은 [일회용 암호 기술 프로필 정의](one-time-password-technical-profile.md)를 참조 하세요.

요소에 다음 기술 프로필을 추가 합니다 `<ClaimsProviders>` .

```XML
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>REST API 기술 프로필 추가

이 REST API 기술 프로필은 Mailjet 형식을 사용 하 여 전자 메일 콘텐츠를 생성 합니다. RESTful 기술 프로필에 대 한 자세한 내용은 [RESTful 기술 프로필 정의](restful-technical-profile.md)를 참조 하세요.

OTP 기술 프로필과 마찬가지로 다음 기술 프로필을 요소에 추가 합니다 `<ClaimsProviders>` .

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="sendOtp">
      <DisplayName>Use email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.mailjet.com/v3.1/send</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_MailjetApiKey" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_MailjetSecretKey" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateEmailRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="emailRequestBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>DisplayControl에 대 한 참조 만들기

마지막 단계에서 사용자가 만든 DisplayControl에 대 한 참조를 추가 합니다. 기존 `LocalAccountSignUpWithLogonEmail` 및 `LocalAccountDiscoveryUsingEmailAddress` 자체 어설션된 기술 프로필을 다음으로 바꿉니다. 이전 버전의 Azure AD B2C 정책을 사용한 경우 이러한 기술 프로필 `DisplayClaims` 은 DisplayControl .에 대 한 참조와 함께 사용 됩니다.

자세한 내용은 [자체 어설션된 기술 프로필](restful-technical-profile.md) 및 [DisplayControl](display-controls.md)를 참조 하세요.

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="LocalAccountDiscoveryUsingEmailAddress">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
      </DisplayClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>필드 전자 메일 지역화

전자 메일을 지역화 하려면 Mailjet 또는 메일 공급자에 게 지역화 된 문자열을 보내야 합니다. 예를 들어 전자 메일의 제목, 본문, 코드 메시지 또는 서명을 지역화할 수 있습니다. 이렇게 하려면 [GetLocalizedStringsTransformation](string-transformations.md) 클레임 변환을 사용 하 여 지역화 된 문자열을 클레임 형식으로 복사할 수 있습니다. `GenerateEmailRequestBody`JSON 페이로드를 생성 하는 클레임 변환은 지역화 된 문자열을 포함 하는 입력 클레임을 사용 합니다.

1. 정책에서 다음 문자열 클레임을 정의 합니다. subject, message, codeIntro 및 signature
1. [GetLocalizedStringsTransformation](string-transformations.md) 클레임 변환을 정의 하 여 1 단계의 클레임으로 지역화 된 문자열 값을 대체 합니다.
1. `GenerateEmailRequestBody`다음 XML 코드 조각과 함께 입력 클레임을 사용 하도록 클레임 변환을 변경 합니다.
1. Azure AD B2C에 의해 지역화 될 모든 문자열 대신 동적 매개 변수를 사용 하도록 Mailjet 템플릿을 업데이트 합니다.

    ```XML
    <ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
        <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
        <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
        <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="Messages.0.Subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="Messages.0.Variables.otpmessage" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="Messages.0.Variables.otpcodeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="Messages.0.Variables.otpsignature" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your Mailjet template. -->
        <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
        <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

        <!-- Update with an email appropriate for your organization. -->
        <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. 다음 [지역화](localization.md) 요소를 추가 합니다.

    ```xml
    <Localization Enabled="true">
      <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
        <SupportedLanguage>en</SupportedLanguage>
        <SupportedLanguage>es</SupportedLanguage>
      </SupportedLanguages>
      <LocalizedResources Id="api.custom-email.en">
        <LocalizedStrings>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
        </LocalizedStrings>
        </LocalizedStrings>
      </LocalizedResources>
      <LocalizedResources Id="api.custom-email.es">
        <LocalizedStrings>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
        </LocalizedStrings>
      </LocalizedResources>
    </Localization>
    ```

1. [Contentdefinitions](contentdefinitions.md) 요소를 업데이트 하 여 LocalizedResources 요소에 대 한 참조를 추가 합니다.

    ```xml
    <ContentDefinitions>
      <ContentDefinition Id="api.localaccountsignup">
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
        <LocalizedResourcesReferences MergeBehavior="Prepend">
          <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
          <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
        </LocalizedResourcesReferences>
      </ContentDefinition>
      <ContentDefinition Id="api.localaccountpasswordreset">
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
        <LocalizedResourcesReferences MergeBehavior="Prepend">
          <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
          <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
        </LocalizedResourcesReferences>
      </ContentDefinition>
    </ContentDefinitions>
    ```

1. 마지막으로 및 기술 프로필에 다음 입력 클레임 변환을 추가 `LocalAccountSignUpWithLogonEmail` `LocalAccountDiscoveryUsingEmailAddress` 합니다.

    ```xml
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```

## <a name="next-steps"></a>다음 단계

GitHub에 대 한 사용자 지정 전자 메일 확인 정책의 예를 찾을 수 있습니다.

- [사용자 지정 전자 메일 확인-DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- 사용자 지정 REST API 또는 HTTP 기반 SMTP 전자 메일 공급자를 사용 하는 방법에 대 한 자세한 내용은 [Azure AD B2C 사용자 지정 정책에서 RESTful 기술 프로필 정의](restful-technical-profile.md)를 참조 하세요.