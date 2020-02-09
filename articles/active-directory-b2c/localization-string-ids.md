---
title: 지역화 문자열 ID - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C의 사용자 지정 정책에서 api.signuporsignin의 ID를 사용하여 콘텐츠 정의의 ID를 지정하는 방법을 설명합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: cd5e543a6d5dc2b59847a78bd925be68f8cdde25
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982860"
---
# <a name="localization-string-ids"></a>지역화 문자열 ID

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Localization** 요소를 사용하면 사용자 경험용 정책에서 여러 로캘이나 언어를 지원할 수 있습니다. 이 문서에서는 정책에서 사용할 수 있는 지역화 ID 목록을 제공합니다. UI 지역화 개념을 숙지하려면 [지역화](localization.md)를 참조하세요.

## <a name="sign-up-or-sign-in-page-elements"></a>등록 또는 로그인 페이지 요소

ID가 `api.signuporsignin`인 콘텐츠 정의에 사용되는 ID는 다음과 같습니다.

| ID | 기본값 |
| -- | ------------- |
| **local_intro_email** | 기존 계정으로 로그인 |
| **logonIdentifier_email** | 메일 주소 |
| **requiredField_email** | 이메일을 입력하세요. |
| **invalid_email** | 올바른 이메일 주소를 입력하세요. |
| **email_pattern** | ^ [A-za-z0-9.! # $% & ' ' *+/=? ^ _\`{\|} ~-] + @ [a-za-z0-9-] + (?:\\. [ A-za-z0-9-] +)* $ |
| **local_intro_username** | 사용자 이름으로 로그인 |
| **logonIdentifier_username** | 사용자 이름 |
| **requiredField_username** | 사용자 이름을 입력하세요. |
| **password** | 암호 |
| **requiredField_password** | 암호를 입력하세요. |
| **invalid_password** | 입력한 암호의 형식이 잘못되었습니다. |
| **forgotpassword_link** | 암호를 잊으셨습니까? |
| **createaccount_intro** | 계정이 없으세요? |
| **createaccount_link** | 지금 등록하세요. |
| **divider_title** | 또는 |
| **cancel_message** | 사용자가 암호를 잊음 |
| **button_signin** | 로그인 |
| **social_intro** | 소셜 계정으로 로그인 |
  **remember_me** |로그인 유지|
| **unknown_error** | 로그인하는 동안 문제가 발생했습니다. 나중에 다시 시도하세요. |

다음 예제에서는 등록 또는 로그인 페이지의 몇 가지 사용자 인터페이스 요소 사용 방법을 보여 줍니다.

![등록 또는 로그인 페이지 UX 요소](./media/localization-string-ids/localization-susi.png)

ID 공급자의 ID는 사용자 경험 **ClaimsExchange** 요소에서 구성됩니다. ID 공급자의 제목을 지역화하려는 경우 **ElementType**은 `ClaimsProvider`로 설정하고 **StringId**는 `ClaimsExchange`의 ID로 설정합니다.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

다음 예제에서는 Facebook ID 공급자를 아랍어로 지역화합니다.

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>등록 또는 로그인 오류 메시지

| ID | 기본값 |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | 암호가 올바르지 않습니다. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | 계정을 찾을 수 없는 것 같습니다. |
| **UserMessageIfOldPasswordUsed** | 이전 암호를 사용한 것 같습니다. |
| **DefaultMessage** | 잘못된 사용자 이름 또는 암호 |
| **UserMessageIfUserAccountDisabled** | 계정이 잠겨 있습니다. 잠금 해제하려면 고객 지원 담당자에게 문의한 다음 다시 시도하세요. |
| **UserMessageIfUserAccountLocked** | 권한 없는 사용을 방지하기 위해 계정이 일시적으로 잠겨 있습니다. 나중에 다시 시도하십시오. |
| **AADRequestsThrottled** | 현재 요청이 너무 많습니다. 잠시 기다린 후에 다시 시도하세요. |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>등록 및 자체 어설션된 페이지 사용자 인터페이스 요소

아래에는 ID가 `api.localaccountsignup`인 콘텐츠 정의 또는 `api.selfasserted`로 시작하는 모든 콘텐츠 정의(예: `api.selfasserted.profileupdate` 및 `api.localaccountpasswordreset`)의 ID가 나와 있습니다.

| ID | 기본값 |
| -- | ------------- |
| **ver_sent** | 확인 코드를 보낸 대상: |
| **ver_but_default** | 기본값 |
| **cancel_message** | 사용자가 자체 어설션된 정보의 입력을 취소함 |
| **preloader_alt** | 기다려 주세요. |
| **ver_but_send** | 확인 코드 보내기 |
| **alert_yes** | 예 |
| **error_fieldIncorrect** | 하나 이상의 필드가 잘못 채워졌습니다. 항목을 확인하고 다시 시도하세요. |
| **year** | 년 |
| **verifying_blurb** | 정보를 처리하는 동안 잠시 기다려 주세요. |
| **button_cancel** | 취소 |
| **ver_fail_no_retry** | 여러 번 틀렸습니다. 나중에 다시 시도하세요. |
| **month** | 월 |
| **ver_success_msg** | 이메일 주소를 확인했습니다. 이제 계속할 수 있습니다. |
| **months** | 1월, 2월, 3월, 4월, 5월, 6월, 7월, 8월, 9월, 10월, 11월, 12월 |
| **ver_fail_server** | 이메일 주소를 확인하는 동안 문제가 발생했습니다. 유효한 이메일 주소를 입력하고 다시 시도하세요. |
| **error_requiredFieldMissing** | 필수 필드가 누락되어 있습니다. 모든 필수 필드를 작성하고 다시 시도하세요. |
| **initial_intro** | 다음 세부 정보를 제공하세요. |
| **ver_but_resend** | 새 코드 전송 |
| **button_continue** | Create |
| **error_passwordEntryMismatch** | 암호 입력 필드가 일치하지 않습니다. 두 필드에 같은 암호를 입력하고 다시 시도하세요. |
| **ver_incorrect_format** | 잘못된 형식 |
| **ver_but_edit** | 이메일 변경 |
| **ver_but_verify** | 코드 확인 |
| **alert_no** | 아닙니다. |
| **ver_info_msg** | 받은 편지함으로 확인 코드를 보냈습니다. 확인 코드를 아래 입력란에 복사하세요. |
| **day** | 일 |
| **ver_fail_throttled** | 이 이메일 주소를 확인해 달라는 요청이 너무 많습니다. 잠시 기다렸다가 다시 시도하세요. |
| **helplink_text** | 이것은 무엇인가요? |
| **ver_fail_retry** | 해당 코드가 잘못되었습니다. 다시 시도하세요. |
| **alert_title** | 세부 정보 입력 취소 |
| **required_field** | 이 정보는 필수 항목입니다. |
| **alert_message** | 세부 정보 입력을 취소하시겠습니까? |
| **ver_intro_msg** | 확인이 필요합니다. [보내기] 단추를 클릭하세요. |
| **ver_input** | 확인 코드 |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>등록 및 자체 어설션된 페이지 오류 메시지

| ID | 기본값 |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | 지정한 ID를 사용하는 사용자가 이미 있습니다. 다른 ID를 선택하세요. |
| **UserMessageIfClaimNotVerified** | 확인되지 않은 클레임: {0} |
| **UserMessageIfIncorrectPattern** | {0}에 대한 잘못된 패턴 |
| **UserMessageIfMissingRequiredElement** | 필요한 {0} 요소가 없습니다. |
| **UserMessageIfValidationError** | {0}의 유효성 검사 오류 |
| **UserMessageIfInvalidInput** | {0}에 잘못된 입력이 있습니다. |
| **ServiceThrottled** | 현재 요청이 너무 많습니다. 잠시 기다린 후에 다시 시도하세요. |

다음 예제에서는 등록 페이지의 몇 가지 사용자 인터페이스 요소 사용 방법을 보여 줍니다.

![레이블이 지정 된 UI 요소 이름이 있는 등록 페이지](./media/localization-string-ids/localization-sign-up.png)

다음 예제에서는 사용자가 확인 코드 보내기 단추를 클릭한 후 등록 페이지의 몇 가지 사용자 인터페이스 요소 사용 방법을 보여 줍니다.

![등록 페이지 이메일 확인 UX 요소](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>전화 인증 페이지 사용자 인터페이스 요소

ID가 `api.phonefactor`인 콘텐츠 정의용 ID는 다음과 같습니다.

| ID | 기본값 |
| -- | ------------- |
| **button_verify** | 내게 전화 걸기 |
| **country_code_label** | 국가 코드 |
| **cancel_message** | 사용자가 다단계 인증을 취소함 |
| **text_button_send_second_code** | 새 코드 보내기 |
| **code_pattern** | \\d{6} |
| **intro_mixed** | 다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 SMS 또는 전화로 코드를 보내겠습니다. |
| **intro_mixed_p** | 다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 전화를 받거나 SMS로 코드를 받을 번호를 선택하세요. |
| **button_verify_code** | 코드 확인 |
| **requiredField_code** | 받은 확인 코드를 입력하세요. |
| **invalid_code** | 받은 6자리 코드를 입력하세요. |
| **button_cancel** | 취소 |
| **local_number_input_placeholder_text** | 전화 번호 |
| **button_retry** | 다시 시도 |
| **alternative_text** | 내 휴대폰이 없음 |
| **intro_phone_p** | 다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 전화를 받을 번호를 선택하세요. |
| **intro_phone** | 다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 전화를 걸겠습니다. |
| **enter_code_text_intro** | 아래에 인증 코드를 입력하거나  |
| **intro_entry_phone** | 전화를 걸어 인증할 수 있도록 아래에 숫자를 입력하세요. |
| **intro_entry_sms** | SMS를 통해 인증 코드를 보낼 수 있도록 아래에 숫자를 입력하세요. |
| **button_send_code** | 코드 보내기 |
| **invalid_number** | 유효한 전화 번호를 입력하세요. |
| **intro_sms** | 다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 SMS로 코드를 보내겠습니다. |
| **intro_entry_mixed** | SMS 또는 전화를 통해 인증 코드를 보낼 수 있도록 아래에 숫자를 입력하세요. |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 SMS로 코드를 받을 번호를 선택하세요. |
| **requiredField_countryCode** | 국가 번호를 선택하세요. |
| **requiredField_number** | 전화 번호를 입력하세요. |
| **country_code_input_placeholder_text** |국가 또는 지역 |
| **number_label** | 전화 번호 |
| **error_tryagain** | 제공한 전화 번호가 통화 중이거나 사용될 수 없습니다. 번호를 확인 후 다시 시도하세요. |
| **error_incorrect_code** | 입력한 확인 코드가 레코드와 일치하지 않습니다. 다시 시도하거나 새 코드를 요청하세요. |
| **countryList** | {\"DEFAULT\":\"Country/Region\",\"AF\":\"Afghanistan\",\"AX\":\"Åland Islands\",\"AL\":\"Albania\",\"DZ\":\"Algeria\",\"AS\":\"American Samoa\",\"AD\":\"Andorra\",\"AO\":\"Angola\",\"AI\":\"Anguilla\",\"AQ\":\"Antarctica\",\"AG\":\"Antigua and Barbuda\",\"AR\":\"Argentina\",\"AM\":\"Armenia\",\"AW\":\"Aruba\",\"AU\":\"Australia\",\"AT\":\"Austria\",\"AZ\":\"Azerbaijan\",\"BS\":\"Bahamas\",\"BH\":\"Bahrain\",\"BD\":\"Bangladesh\",\"BB\":\"Barbados\",\"BY\":\"Belarus\",\"BE\":\"Belgium\",\"BZ\":\"Belize\",\"BJ\":\"Benin\",\"BM\":\"Bermuda\",\"BT\":\"Bhutan\",\"BO\":\"Bolivia\",\"BQ\":\"Bonaire\",\"BA\":\"Bosnia and Herzegovina\",\"BW\":\"Botswana\",\"BV\":\"Bouvet Island\",\"BR\":\"Brazil\",\"IO\":\"British Indian Ocean Territory\",\"VG\":\"British Virgin Islands\",\"BN\":\"Brunei\",\"BG\":\"Bulgaria\",\"BF\":\"Burkina Faso\",\"BI\":\"Burundi\",\"CV\":\"Cabo Verde\",\"KH\":\"Cambodia\",\"CM\":\"Cameroon\",\"CA\":\"Canada\",\"KY\":\"Cayman Islands\",\"CF\":\"Central African Republic\",\"TD\":\"Chad\",\"CL\":\"Chile\",\"CN\":\"China\",\"CX\":\"Christmas Island\",\"CC\":\"Cocos (Keeling) Islands\",\"CO\":\"Colombia\",\"KM\":\"Comoros\",\"CG\":\"Congo\",\"CD\":\"Congo (DRC)\",\"CK\":\"Cook Islands\",\"CR\":\"Costa Rica\",\"CI\":\"Côte d’Ivoire\",\"HR\":\"Croatia\",\"CU\":\"Cuba\",\"CW\":\"Curaçao\",\"CY\":\"Cyprus\",\"CZ\":\"Czech Republic\",\"DK\":\"Denmark\",\"DJ\":\"Djibouti\",\"DM\":\"Dominica\",\"DO\":\"Dominican Republic\",\"EC\":\"Ecuador\",\"EG\":\"Egypt\",\"SV\":\"El Salvador\",\"GQ\":\"Equatorial Guinea\",\"ER\":\"Eritrea\",\"EE\":\"Estonia\",\"ET\":\"Ethiopia\",\"FK\":\"Falkland Islands\",\"FO\":\"Faroe Islands\",\"FJ\":\"Fiji\",\"FI\":\"Finland\",\"FR\":\"France\",\"GF\":\"French Guiana\",\"PF\":\"French Polynesia\",\"TF\":\"French Southern Territories\",\"GA\":\"Gabon\",\"GM\":\"Gambia\",\"GE\":\"Georgia\",\"DE\":\"Germany\",\"GH\":\"Ghana\",\"GI\":\"Gibraltar\",\"GR\":\"Greece\",\"GL\":\"Greenland\",\"GD\":\"Grenada\",\"GP\":\"Guadeloupe\",\"GU\":\"Guam\",\"GT\":\"Guatemala\",\"GG\":\"Guernsey\",\"GN\":\"Guinea\",\"GW\":\"Guinea-Bissau\",\"GY\":\"Guyana\",\"HT\":\"Haiti\",\"HM\":\"Heard Island and McDonald Islands\",\"HN\":\"Honduras\",\"HK\":\"Hong Kong SAR\",\"HU\":\"Hungary\",\"IS\":\"Iceland\",\"IN\":\"India\",\"ID\":\"Indonesia\",\"IR\":\"Iran\",\"IQ\":\"Iraq\",\"IE\":\"Ireland\",\"IM\":\"Isle of Man\",\"IL\":\"Israel\",\"IT\":\"Italy\",\"JM\":\"Jamaica\",\"JP\":\"Japan\",\"JE\":\"Jersey\",\"JO\":\"Jordan\",\"KZ\":\"Kazakhstan\",\"KE\":\"Kenya\",\"KI\":\"Kiribati\",\"KR\":\"Korea\",\"KW\":\"Kuwait\",\"KG\":\"Kyrgyzstan\",\"LA\":\"Laos\",\"LV\":\"Latvia\",\"LB\":\"Lebanon\",\"LS\":\"Lesotho\",\"LR\":\"Liberia\",\"LY\":\"Libya\",\"LI\":\"Liechtenstein\",\"LT\":\"Lithuania\",\"LU\":\"Luxembourg\",\"MO\":\"Macao SAR\",\"MK\":\"North Macedonia\",\"MG\":\"Madagascar\",\"MW\":\"Malawi\",\"MY\":\"Malaysia\",\"MV\":\"Maldives\",\"ML\":\"Mali\",\"MT\":\"Malta\",\"MH\":\"Marshall Islands\",\"MQ\":\"Martinique\",\"MR\":\"Mauritania\",\"MU\":\"Mauritius\",\"YT\":\"Mayotte\",\"MX\":\"Mexico\",\"FM\":\"Micronesia\",\"MD\":\"Moldova\",\"MC\":\"Monaco\",\"MN\":\"Mongolia\",\"ME\":\"Montenegro\",\"MS\":\"Montserrat\",\"MA\":\"Morocco\",\"MZ\":\"Mozambique\",\"MM\":\"Myanmar\",\"NA\":\"Namibia\",\"NR\":\"Nauru\",\"NP\":\"Nepal\",\"NL\":\"Netherlands\",\"NC\":\"New Caledonia\",\"NZ\":\"New Zealand\",\"NI\":\"Nicaragua\",\"NE\":\"Niger\",\"NG\":\"Nigeria\",\"NU\":\"Niue\",\"NF\":\"Norfolk Island\",\"KP\":\"North Korea\",\"MP\":\"Northern Mariana Islands\",\"NO\":\"Norway\",\"OM\":\"Oman\",\"PK\":\"Pakistan\",\"PW\":\"Palau\",\"PS\":\"Palestinian Authority\",\"PA\":\"Panama\",\"PG\":\"Papua New Guinea\",\"PY\":\"Paraguay\",\"PE\":\"Peru\",\"PH\":\"Philippines\",\"PN\":\"Pitcairn Islands\",\"PL\":\"Poland\",\"PT\":\"Portugal\",\"PR\":\"Puerto Rico\",\"QA\":\"Qatar\",\"RE\":\"Réunion\",\"RO\":\"Romania\",\"RU\":\"Russia\",\"RW\":\"Rwanda\",\"BL\":\"Saint Barthélemy\",\"KN\":\"Saint Kitts and Nevis\",\"LC\":\"Saint Lucia\",\"MF\":\"Saint Martin\",\"PM\":\"Saint Pierre and Miquelon\",\"VC\":\"Saint Vincent and the Grenadines\",\"WS\":\"Samoa\",\"SM\":\"San Marino\",\"ST\":\"São Tomé and Príncipe\",\"SA\":\"Saudi Arabia\",\"SN\":\"Senegal\",\"RS\":\"Serbia\",\"SC\":\"Seychelles\",\"SL\":\"Sierra Leone\",\"SG\":\"Singapore\",\"SX\":\"Sint Maarten\",\"SK\":\"Slovakia\",\"SI\":\"Slovenia\",\"SB\":\"Solomon Islands\",\"SO\":\"Somalia\",\"ZA\":\"South Africa\",\"GS\":\"South Georgia and South Sandwich Islands\",\"SS\":\"South Sudan\",\"ES\":\"Spain\",\"LK\":\"Sri Lanka\",\"SH\":\"St Helena, Ascension, Tristan da Cunha\",\"SD\":\"Sudan\",\"SR\":\"Suriname\",\"SJ\":\"Svalbard\",\"SZ\":\"Swaziland\",\"SE\":\"Sweden\",\"CH\":\"Switzerland\",\"SY\":\"Syria\",\"TW\":\"Taiwan\",\"TJ\":\"Tajikistan\",\"TZ\":\"Tanzania\",\"TH\":\"Thailand\",\"TL\":\"Timor-Leste\",\"TG\":\"Togo\",\"TK\":\"Tokelau\",\"TO\":\"Tonga\",\"TT\":\"Trinidad and Tobago\",\"TN\":\"Tunisia\",\"TR\":\"Turkey\",\"TM\":\"Turkmenistan\",\"TC\":\"Turks and Caicos Islands\",\"TV\":\"Tuvalu\",\"UM\":\"U.S. Outlying Islands\",\"VI\":\"U.S. Virgin Islands\",\"UG\":\"Uganda\",\"UA\":\"Ukraine\",\"AE\":\"United Arab Emirates\",\"GB\":\"United Kingdom\",\"US\":\"United States\",\"UY\":\"Uruguay\",\"UZ\":\"Uzbekistan\",\"VU\":\"Vanuatu\",\"VA\":\"Vatican City\",\"VE\":\"Venezuela\",\"VN\":\"Vietnam\",\"WF\":\"Wallis and Futuna\",\"YE\":\"Yemen\",\"ZM\":\"Zambia\",\"ZW\":\"Zimbabwe\"} |
| **error_448** | 제공한 전화 번호에 연결할 수 없습니다. |
| **error_449** | 사용자가 다시 시도 횟수를 초과했습니다. |
| **verification_code_input_placeholder_text** | 확인 코드 |

다음 예제에서는 MFA 등록 페이지의 몇 가지 사용자 인터페이스 요소 사용 방법을 보여 줍니다.

![등록 페이지 이메일 확인 UX 요소](./media/localization-string-ids/localization-mfa1.png)

다음 예제에서는 MFA 유효성 검사 페이지의 몇 가지 사용자 인터페이스 요소 사용 방법을 보여 줍니다.

![등록 페이지 이메일 확인 UX 요소](./media/localization-string-ids/localization-mfa2.png)

## <a name="verification-display-control-user-interface-elements"></a>확인 표시 컨트롤 사용자 인터페이스 요소

[확인 표시 컨트롤](display-control-verification.md) 에 대한 id는 다음과 같습니다.

| ID | 기본값 |
| -- | ------------- |
|verification_control_but_change_claims |변경 |
|verification_control_fail_send_code |코드를 보내지 못했습니다. 나중에 다시 시도 하세요. |
|verification_control_fail_verify_code |코드를 확인 하지 못했습니다. 나중에 다시 시도 하세요. |
|verification_control_but_send_code |코드 보내기 |
|verification_control_but_send_new_code |새 코드 보내기 |
|verification_control_but_verify_code |코드 확인 |

## <a name="one-time-password-error-messages"></a>일회용 암호 오류 메시지
다음은 [일회성 암호 기술 프로필](one-time-password-technical-profile.md) 오류 메시지의 id입니다.

| ID | 기본값 |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |한 번의 암호 확인 제공으로 최대 시도 횟수를 초과 했습니다. |
|UserMessageIfSessionDoesNotExist |한 번의 암호 확인 세션이 만료 되었습니다. |
|UserMessageIfSessionConflict |한 번의 암호 확인 세션 충돌 |
|Usermessageifin유효한 코드 |확인을 위해 제공한 일회용 암호가 올바르지 않습니다. |








