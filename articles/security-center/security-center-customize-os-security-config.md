---
title: Azure Security Center에서 OS 보안 구성 사용자 지정(미리 보기) | Microsoft Docs
description: 이 문서에서는 보안 센터 평가를 사용자 지정하는 방법을 설명합니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: c0c37724e61490c8c33b5e2d37879549bbc6d7ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60705498"
---
# <a name="customize-os-security-configurations-in-azure-security-center-preview"></a>Azure Security Center에서 OS 보안 구성 사용자 지정(미리 보기)

이 연습에서는 Azure Security Center에서 OS 보안 구성 평가를 사용자 지정하는 방법을 보여줍니다.

## <a name="what-are-os-security-configurations"></a>OS 보안 구성이란?

Azure Security Center는 방화벽, 감사, 암호 정책 등과 관련된 규칙을 포함하여 OS를 강화하기 위한 [150여 개의 권장 규칙](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)을 적용하여 보안 구성을 모니터링합니다. 컴퓨터에 취약한 구성이 있는 것으로 확인되면 Security Center에서 보안 권장 사항이 생성됩니다.

규칙을 사용자 지정하면 조직의 환경에 보다 적합한 구성 옵션을 제어할 수 있습니다. 사용자가 사용자 지정 평가 정책을 설정한 다음 구독에서 적용 가능한 모든 시스템에 적용할 수 있습니다.

> [!NOTE]
> - 현재 OS 보안 구성 사용자 지정은 Windows Server 버전 2008, 2008 R2, 2012, 2012 R2 및 2016 운영 체제에서만 사용할 수 있습니다.
> - 구성은 선택한 구독 아래의 모든 작업 영역에 연결된 모든 VM과 컴퓨터에 적용됩니다.
> - OS 보안 구성 사용자 지정은 Security Center 표준 계층에서만 사용할 수 있습니다.
>
>

특정 규칙을 사용 및 사용 안 함으로 설정하고, 기존 규칙에 대한 설정을 원하는 대로 변경하고, 지원되는 규칙 유형(레지스트리, 감사 정책, 보안 정책)을 기준으로 새 규칙을 추가하여 OS 보안 구성 규칙을 사용자 지정할 수 있습니다. 현재 원하는 설정은 정확한 값이어야 합니다.

새 규칙은 동일한 유형의 다른 기존 규칙과 동일한 형식 및 구조여야 합니다.

> [!NOTE]
> OS 보안 구성을 사용자 지정하려면 *구독 소유자*, *구독 참가자* 또는 *보안 관리자*의 역할이 할당되어야 합니다.
>
>

## <a name="customize-the-default-os-security-configuration"></a>기본 OS 보안 구성 사용자 지정

Security Center에서 기본 OS 보안 구성을 사용자 지정하려면 다음을 수행합니다.

1.  **Security Center** 대시보드를 엽니다.

2.  왼쪽 창의 **보안 정책**을 선택합니다.      

    ![보안 정책 목록](media/security-center-customize-os-security-config/manual-provision.png)

3.  사용자 지정할 구독의 행에서 **설정 편집**을 클릭합니다.

4. **보안 구성 편집**을 선택합니다.  

    !["보안 구성 편집" 창](media/security-center-customize-os-security-config/blade.png)

5. 단계에 따라 파일을 다운로드 및 편집하고 수정된 파일을 업로드합니다.

   > [!NOTE]
   > 기본적으로 다운로드한 구성 파일은 *json* 형식입니다. 파일을 수정하는 방법에 대한 지침은 [구성 파일 사용자 지정](#customize-the-configuration-file)을 참조하세요.
   >

6. 변경 내용을 커밋하려면 **저장**을 선택합니다. 그렇지 않으면 정책이 저장되지 않습니다.

    ![저장 단추](media/security-center-customize-os-security-config/save-successfully.png)

   파일 저장이 완료되면 구독 아래 작업 영역에 연결된 컴퓨터와 모든 VM에 구성이 적용됩니다. 이 프로세스는 일반적으로 몇 분 정도 소요되지만 인프라 규모에 따라 더 오래 걸릴 수 있습니다.

언제든 현재 정책 구성을 기본 상태로 재설정할 수 있습니다. 이렇게 하려면 **OS 보안 구성 규칙 편집** 창에서 **재설정**을 선택합니다. 확인 팝업 창에서 **예**를 선택하여 이 옵션을 확인합니다.

![재설정 확인 창](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>구성 파일 사용자 지정

사용자 지정 파일에는 지원되는 각 OS 버전에 대한 일련의 규칙이나 규칙 집합이 있습니다. 다음 예제와 같이 각 규칙 집합에는 고유 이름과 고유 ID가 있습니다.

![규칙 집합 이름 및 ID](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> 이 예제 파일은 Visual Studio를 사용하여 편집되었지만, JSON 뷰어 플러그 인이 설치되어 있을 경우 메모장을 사용할 수도 있습니다.
>
>

사용자 지정 파일을 편집할 때 규칙을 하나 또는 모두 수정할 수 있습니다. 각 규칙 집합은 세 범주로 구분된 *규칙* 섹션을 포함합니다. 여기에 표시된 대로 레지스트리, 감사 정책 및 보안 정책

![세 가지 규칙 집합 범주](media/security-center-customize-os-security-config/rules-section.png)

각 범주에 자체 특성 집합이 있습니다. 다음과 같은 특성은 변경할 수 있습니다.

- **expectedValue**: 이 특성의 필드 데이터 형식은 *규칙 유형*마다 지원되는 값과 일치해야 합니다. 예를 들면 다음과 같습니다.

  - **baselineRegistryRules**: 이 값은 해당 규칙에 정의된 [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884)과 일치해야 합니다.

  - **baselineAuditPolicyRules**: 다음 문자열 값 중 하나를 사용합니다.

    - *Success and Failure*

    - *Success*

  - **baselineSecurityPolicyRules**: 다음 문자열 값 중 하나를 사용합니다.

    - *No one*

    - 허용된 사용자 그룹의 목록입니다. 예를 들면 다음과 같습니다. *관리자*, *백업 운영자*

-   **state**: 이 문자열에는 *Disabled*나 *Enabled* 옵션을 포함할 수 있습니다. 이 릴리스에 대 한 문자열은 대/소문자 구분.

구성할 수 있는 필드는 이뿐입니다. 파일 형식 또는 크기를 위반하면 변경 내용을 저장할 수 없습니다. 유효한 JSON 구성 파일을 업로드해야 함을 알려 주는 오류가 표시됩니다.

다른 잠재적 오류 목록은 [오류 코드](#error-codes)를 참조하세요.

다음 세 개 섹션에는 이전 규칙에 대한 예제가 있습니다. *expectedValue* 및 *state* 특성은 변경할 수 있습니다.

**baselineRegistryRules**
```json
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\LanManServer\\\\Parameters",
    "valueName": "restrictnullsessaccess",
    "ruleId": "f9020046-6340-451d-9548-3c45d765d06d",
    "originalId": "0f319931-aa36-4313-9320-86311c0fa623",
    "cceId": "CCE-10940-5",
    "ruleName": "Network access: Restrict anonymous access to Named Pipes and
    Shares",
    "ruleType": "Registry",
    "expectedValue": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Disabled"

    }
```

**baselineAuditPolicyRules**
```json
    {
    "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
    "ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
    "originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
    "cceId": "CCE-11001-5",
    "ruleName": "Audit Policy: Account Management: Other Account Management Events",
    "ruleType": "AuditPolicy",
    "expectedValue": "Success and Failure",
    "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

**baselineSecurityPolicyRules**
```json
    {
    "sectionName": "Privilege Rights",
    "settingName": "SeIncreaseWorkingSetPrivilege",
    "ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
    "originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
    "cceId": "CCE-10548-6",
    "ruleName": "Increase a process working set",
    "ruleType": "SecurityPolicy",
    "expectedValue": "Administrators, Local Service",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

몇 가지 규칙은 여러 OS 유형에 중복됩니다. 중복 규칙에는 동일한 *originalId* 특성이 있습니다.

## <a name="create-custom-rules"></a>사용자 지정 규칙 만들기

새 규칙을 만들 수도 있습니다. 새 규칙을 만들기 전에 다음과 같은 제한 사항에 유의하세요.

-   스키마 버전, *baselineId* 및 *baselineName*은 변경할 수 없습니다.

-   규칙 집합을 제거할 수 없습니다.

-   규칙 집합을 추가할 수 없습니다.

-   허용되는 최대 규칙 수(기본 규칙 포함)는 1000개입니다.

새 사용자 지정 규칙은 새 사용자 지정 소스(!= “Microsoft”)로 표시됩니다. *ruleId* 필드는 null이거나 비워 둘 수 있습니다. 비어 있으면 Microsoft에서 ID를 생성합니다. 비어 있지 않으면 모든 규칙(기본 및 사용자 지정)에 유효한 고유 GUID가 있어야 합니다. 핵심 필드에 대한 다음 제약 조건을 검토합니다.

-   **originalId**: null이거나 비워 둘 수 있습니다. *originalId*가 비어 있지 않으면 유효한 GUID여야 합니다.

-   **cceId**: null이거나 비워 둘 수 있습니다. *cceId*가 비어 있지 않으면 고유해야 합니다.

-   **ruleType**: Registry, AuditPolicy 또는 SecurityPolicy 중 하나를 선택합니다.

-   **Severity**: Unknown, Critical, Warning 또는 Informational 중 하나를 선택합니다.

-   **analyzeOperation**: *Equals*이어야 합니다.

-   **auditPolicyId**: 유효한 GUID이어야 합니다.

-   **regValueType**: Int, Long, String 또는 MultipleString 중 하나를 선택합니다.

> [!NOTE]
> Hive는 *LocalMachine*이어야 합니다.
>
>

새 사용자 지정 규칙의 예:

**레지스트리**:
```json
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule", "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
    }
```
**보안 정책**:
```json
   {
   "sectionName": "Privilege Rights",
   "settingName": "SeDenyBatchLogonRight",
   "originalId": "",
   "cceId": "",
   "ruleName": "My new security policy rule", "baselineRuleType":
   "SecurityPolicy",
   "expectedValue": "Guests",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```
**감사 정책**:
```json
   {
   "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
   "originalId": "",
   "cceId": "",
   "ruleName": " My new audit policy rule ", "baselineRuleType": "AuditPolicy",
   "expectedValue": " Failure",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```

## <a name="file-upload-failures"></a>파일 업로드 오류

제출된 구성 파일이 값 또는 형식 오류로 인해 잘못된 경우 오류가 표시됩니다(예: **저장 작업 실패**). 수정된 구성 파일을 다시 제출하기 전에 자세한 오류 CSV 보고서를 다운로드하여 오류를 수정할 수 있습니다.

오류 파일의 예:

![오류 파일 예제](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>오류 코드

잠재적 오류는 다음 표에 모두 나와 있습니다.

| **오류**                                | **설명**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfigurationSchemaVersionError  | *schemaVersion* 속성이 잘못되었거나 비어 있습니다. 값을 *{0}*(으)로 설정해야 합니다.                                                         |
| BaselineInvalidStringError               | *{0}* 속성에 *\\n*을 포함할 수 없습니다.                                                                                                         |
| BaselineNullRuleError                    | 기준 구성 규칙 목록에 값이 *null*인 규칙이 있습니다.                                                                         |
| BaselineRuleCceIdNotUniqueError          | CCE-ID *{0}* 이(가) 고유하지 않습니다.                                                                                                                  |
| BaselineRuleEmptyProperty                | *{0}* 속성이 없거나 잘못되었습니다.                                                                                                       |
| BaselineRuleIdNotInDefault               | 규칙에 소스 속성 *Microsoft*가 있지만 Microsoft 기본 규칙 집합에 없습니다.                                                   |
| BaselineRuleIdNotUniqueError             | 규칙 ID는 고유 하지 않습니다.                                                                                                                       |
| BaselineRuleInvalidGuid                  | *{0}* 속성이 잘못되었습니다. 값이 유효한 GUID가 아닙니다.                                                                             |
| BaselineRuleInvalidHive                  | Hive는 LocalMachine이어야 합니다.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | 규칙 이름이 고유하지 않습니다.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | 해당 규칙이 새 구성에 없습니다. 규칙을 삭제할 수 없습니다.                                                                     |
| BaselineRuleNotFoundError                | 해당 규칙이 기본 기준 규칙 집합에 없습니다.                                                                                        |
| BaselineRuleNotInPlace                   | 규칙이 {0} 유형의 기본 규칙과 일치하며 {1} 목록에 나열됩니다.                                                                       |
| BaselineRulePropertyTooLong              | *{0}* 속성이 너무 깁니다. 허용되는 최대 길이는 {1}입니다.                                                                                        |
| BaselineRuleRegTypeInvalidError          | 예상 값 *{0}* 이(가) 정의된 레지스트리 값 형식과 일치하지 않습니다.                                                              |
| BaselineRulesetAdded                     | 기본 구성에 ID가 *{0}* 인 규칙 집합이 없습니다. 규칙 집합을 추가할 수 없습니다.                                               |
| BaselineRulesetIdMustBeUnique            | 지정된 기준 규칙 집합 *{0}* 이(가) 고유해야 합니다.                                                                                           |
| BaselineRulesetNotFound                  | 지정된 구성에 ID가 *{0}* 이고 이름이 *{1}* 인 규칙 집합이 없습니다. 규칙 집합을 삭제할 수 없습니다.                                |
| BaselineRuleSourceNotMatch               | ID가 *{0}* 인 규칙이 이미 정의되어 있습니다.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | 기본 규칙 유형은 *{0}* 입니다.                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | 규칙의 실제 유형이 *{0}* 인데 *ruleType* 속성이 *{1}* 입니다.                                                                          |
| BaselineRuleUnpermittedChangesError      | *expectedValue* 및 *state* 속성만 변경할 수 있습니다.                                                                       |
| BaselineTooManyRules                     | 허용되는 최대 사용자 지정 규칙 수는 규칙 {0}개입니다. 지정된 구성에는 {1} 규칙, {2} 기본 규칙 및 {3} 사용자 지정 규칙이 있습니다. |
| ErrorNoConfigurationStatus               | 구성 상태가 없습니다. 원하는 구성 상태를 설정합니다. *Default* 또는 *Custom*                                    |
| ErrorNonEmptyRulesetOnDefault            | 구성 상태가 Default로 설정되어 있습니다. *BaselineRulesets* 목록은 null이거나 비어 있어야 합니다.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | 지정된 구성 상태가 *Custom*인데 *baselineRulesets* 속성이 null이거나 비어 있습니다.                                             |
| ErrorParsingBaselineConfig               | 지정된 구성이 잘못되었습니다. 정의된 하나 이상의 값이 null 값이거나 잘못된 형식입니다.                                  |
| ErrorParsingIsDefaultProperty            | 지정된 *configurationStatus* 값 *{0}* 이(가) 잘못되었습니다. 값에 *Default* 또는 *Custom*만 지정할 수 있습니다.                                         |
| InCompatibleViewVersion                  | 보기 버전 *{0}* 은(는) 이 작업 영역 유형에서 지원되지 *않습니다*.                                                                                   |
| InvalidBaselineConfigurationGeneralError | 지정된 기준 구성에 하나 이상의 형식 유효성 검사 오류가 있습니다.                                                          |
| ViewConversionError                      | 뷰가 작업 영역에서 지원하는 버전보다 오래된 버전입니다. 보기 변환 실패: {0}.                                                                 |

충분한 권한이 없을 경우 다음과 같은 일반 오류가 표시될 수 있습니다.

!["저장 작업 실패" 오류 메시지](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center에서 OS 보안 구성 평가를 사용자 지정하는 방법을 살펴보았습니다. 구성 규칙 및 수정에 대해 자세히 알아보려면 다음을 참조하세요.

- [Security Center 공통 구성 식별자 및 기준 규칙](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Security Center는 CCE(일반 구성 열거형)를 사용하여 구성 규칙에 고유 ID를 할당합니다. 자세한 내용은 [CCE](https://nvd.nist.gov/config/cce/index)를 참조하세요.
- OS 구성이 권장되는 보안 구성 규칙과 일치하지 않을 경우 취약점을 해결하려면 [보안 구성 수정](security-center-remediate-os-vulnerabilities.md)을 참조하세요.
