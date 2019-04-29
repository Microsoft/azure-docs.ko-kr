---
title: Azure AD Connect - userCertificate 특성으로 인한 LargeObject 오류 | Microsoft Docs
description: 이 항목에서는 userCertificate 특성으로 인한 LargeObject 오류에 대한 수정 단계를 제공합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: c851b5ef024e6584e6f8c93995208b08a91fbb60
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095492"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Azure AD Connect 동기화: userCertificate 특성으로 인한 LargeObject 오류 처리

Azure AD는 **userCertificate** 특성에서 인증서 값에 대해 최대 **15**의 제한을 적용합니다. Azure AD Connect에서 15개 이상의 값을 포함하는 개체를 Azure AD로 내보내면 Azure AD는 다음 메시지와 함께 **LargeObject** 오류를 반환합니다.

>*"프로비전된 개체가 너무 큽니다. 이 개체의 특성 값 개수를 자르십시오. 다음 동기화 주기에 작업이 다시 시도됩니다..."*

다른 AD 특성에 의해 LargeObject 오류가 발생할 수 있습니다. 실제로 userCertificate 특성으로 인한 것인지 확인하려면 온-프레미스 AD 또는 [Synchronization Service Manager Metaverse Search](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch)에서 개체에 대해 확인해야 합니다.

LargeObject 오류가 있는 테넌트에서 개체 목록을 얻으려면 다음 방법 중 하나를 사용합니다.

 * 테넌트가 Azure AD Connect Health에서 동기화되도록 설정되어 있는 경우 제공된 [동기화 오류 보고서](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync)를 참조할 수 있습니다.
 
 * 각 동기화 주기가 끝날 때 전송되는 디렉터리 동기화 오류에 대한 알림 메일에는 LargeObject 오류가 있는 개체 목록이 포함됩니다. 
 * Azure AD에 마지막으로 내보내기 작업을 클릭하면 [Synchronization Service Manager Operations 탭](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations)에 LargeObject 오류가 있는 개체 목록이 표시됩니다.
 
## <a name="mitigation-options"></a>해결 방법 옵션
LargeObject 오류가 해결될 때까지 동일한 개체에 대한 다른 특성 변경 사항을 Azure AD로 내보낼 수 없습니다. 오류를 해결하기 위해 다음 옵션을 고려할 수 있습니다.

 * Azure AD Connect를 빌드 1.1.524.0 이상으로 업그레이드합니다. Azure AD Connect 빌드 1.1.524.0에서는 15개 이상의 값이 특성에 있는 경우 기본 동기화 규칙에서 userCertificate 및 userSMIMECertificate 특성을 내보내지 않도록 업데이트되었습니다. Azure AD Connect를 업그레이드하는 방법에 대한 자세한 내용은 [Azure AD Connect: 이전 버전에서 최신 버전으로 업그레이드](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version) 문서를 참조하세요.

 * Azure AD Connect에서 **15개 이상의 인증서 값을 포함하는 개체의 실제 값 대신 null 값**을 내보내는 **아웃바운드 동기화 규칙**을 구현합니다. 이 옵션은 값이 15개 이상인 개체에 대해 Azure AD로 내보낼 인증서 값이 필요하지 않은 경우에 적합합니다. 이 동기화 규칙을 구현하는 방법에 대한 자세한 내용은 다음 섹션 [userCertificate 특성의 내보내기를 제한하는 동기화 규칙 구현](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute)을 참조하세요.

 * 조직에서 더 이상 사용하지 않는 값을 제거하여 온-프레미스 AD 개체의 인증서 값 수(15개 이하)를 줄입니다. 만료되거나 사용하지 않는 인증서로 특성 블로트가 발생한 경우 적합합니다. [여기에서 제공되는 PowerShell 스크립트](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f)를 사용하여 온-프레미스 AD에서 만료된 인증서를 찾고 백업하며 삭제할 수 있습니다. 인증서를 삭제하기 전에 조직의 공개 키 인프라(Public-Key-Infrastructure) 관리자와 확인하는 것이 좋습니다

 * Azure AD Connect를 구성하여 userCertificate 특성이 Azure AD로 내보내지지 않도록 제외하십시오. 일반적으로 특정 시나리오를 사용하기 위해 Microsoft Online Services에서 특성을 사용할 수 있으므로 이 옵션을 사용하지 않는 것이 좋습니다. 특히 다음과 같습니다.
    * User 개체의 userCertificate 특성은 Exchange Online 및 Outlook 클라이언트에서 메시지 서명 및 암호화에 사용됩니다. 이 기능에 대해 자세히 알아보려면 [메시지 서명 및 암호화를 위한 S/MIME](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx) 문서를 참조하세요.

    * Computer 개체의 userCertificate 특성은 Windows 10 온-프레미스 도메인에 조인된 디바이스가 Azure AD에 연결할 수 있도록 하기 위해 Azure AD에서 사용됩니다. 이 기능에 대한 자세한 내용은 [Windows 10 환경용 Azure AD에 도메인 조인된 디바이스 연결](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy)을 참조하세요.

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>userCertificate 특성의 내보내기를 제한하는 동기화 규칙 구현
userCertificate 특성으로 인해 발생한 LargeObject 오류를 해결하기 위해 Azure AD Connect에서 **15개 이상의 인증서 값을 포함하는 개체의 실제 값 대신 null 값**을 내보내는 아웃바운드 동기화 규칙을 구현할 수 있습니다. 이 섹션에서는 **User** 개체에 대한 동기화 규칙을 구현하는 데 필요한 단계를 설명합니다. 이 단계는 **Contact** 및 **Computer** 개체에 대해 적용할 수 있습니다.

> [!IMPORTANT]
> Null 값을 내보내면 Azure AD로 이전에 성공적으로 내보낸 인증서 값이 제거됩니다.

다음과 같이 단계를 요약할 수 있습니다.

1. 동기화 스케줄러를 비활성화하고 진행 중인 동기화가 없는지 확인합니다.
3. userCertificate 특성에 대한 기존 아웃바운드 동기화 규칙을 찾습니다.
4. 필요한 아웃바운드 동기화 규칙을 만듭니다.
5. LargeObject 오류가 있는 기존 개체에서 새 동기화 규칙을 확인합니다.
6. LargeObject 오류가 있는 나머지 개체에 새 동기화 규칙을 적용합니다.
7. Azure AD로 내보내기를 기다리고 있는 예기치 않은 변경 사항이 없는지 확인합니다.
8. 변경 사항을 Azure AD로 내보냅니다.
9. 동기화 스케줄러를 다시 활성화합니다.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1단계. 동기화 스케줄러를 비활성화하고 진행 중인 동기화가 없는지 확인합니다.
Azure AD에 의도하지 않은 변경 사항을 내보내지 않도록 새 동기화 규칙을 구현하는 중간에 동기화가 수행되지 않도록 합니다. 기본 제공 동기화 스케줄러를 비활성화하려면
1. Azure AD Connect 서버에서 PowerShell 세션을 시작합니다.

2. cmdlet을 실행하여 예약된 동기화 비활성화: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> 이전 단계는 기본 제공된 스케줄러가 있는 Azure AD Connect의 최신 버전(1.1.xxx.x)에만 적용됩니다. Windows Task Scheduler를 사용하는 이전 버전(1.0.xxx.x)의 Azure AD Connect를 사용하거나 정기적인 동기화를 트리거하기 위해 사용자 고유의 사용자 지정 스케줄러(공통이 아님)를 사용하는 경우 적절하게 비활성화해야 합니다.

1. 시작 → 동기화 서비스로 이동하여 **Synchronization Service Manager**를 시작합니다.

1. **작업** 탭으로 이동하고 상태가 *“진행 중”* 인 작업이 없는지 확인합니다.

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>2단계. userCertificate 특성에 대한 기존 아웃바운드 동기화 규칙을 찾습니다.
User 개체에 대한 userCertificate 특성을 Azure AD로 내보내기 위해 활성화 및 구성된 기존 동기화 규칙이 있어야 합니다. 이 동기화 규칙을 찾아 해당 **우선 순위**와 **범위 지정 필터** 구성을 확인합니다.

1. 시작 → 동기화 규칙 편집기로 이동하여 **동기화 규칙 편집기**를 시작합니다.

2. 다음 값을 사용하여 검색 필터를 구성합니다.

    | 특성 | 값 |
    | --- | --- |
    | Direction |**Outbound** |
    | MV 개체 유형 |**Person** |
    | 커넥터 |*Azure AD 커넥터의 이름* |
    | 커넥터 개체 유형 |**user** |
    | MV 특성 |**userCertificate** |

3. User 개체에 대한 userCertficiate 특성을 내보내기 위해 OOB(out-of-box) 동기화 규칙을 Azure AD 커넥터에 사용하는 경우 *“Out to AAD – User ExchangeOnline”* 규칙을 다시 가져와야 합니다.
4. 이 동기화 규칙의 **우선 순위** 값을 적어둡니다.
5. 동기화 규칙을 선택하고 **편집**을 클릭합니다.
6. *“Edit Reserved Rule Confirmation(예약된 규칙 편집 확인)”* 팝업 대화 상자에서 **아니요**를 클릭합니다. (이 동기화 규칙에 대해 어떠한 변경도 없으므로 안심하세요).
7. 편집 화면에서 **범위 지정 필터** 탭을 선택합니다.
8. 범위 지정 필터 구성을 적어둡니다. OOB 동기화 규칙을 사용하는 경우 다음을 포함하여 정확히 **두 개의 절을 포함하는 하나의 범위 지정 필터 그룹**이 있어야 합니다.

    | 특성 | 연산자 | 값 |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | 사용자 |
    | cloudMastered | NOTEQUAL | True  |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>3단계. 필요한 아웃바운드 동기화 규칙을 만듭니다.
새로운 동기화 규칙은 기존 동기화 규칙과 동일한 **범위 지정 필터**와 **높은 우선 순위**를 포함해야 합니다. 이렇게 하면 새 동기화 규칙이 기존 동기화 규칙과 동일한 개체 집합에 적용되고 userCertificate 특성에 대한 기존 동기화 규칙을 재정의합니다. 동기화 규칙을 만들려면
1. 동기화 규칙 편집기에서 **새 규칙 추가** 단추를 클릭합니다.
2. **설명 탭** 아래에서 다음 구성을 제공합니다.

    | 특성 | 값 | 세부 정보 |
    | --- | --- | --- |
    | 이름 | *이름 제공* | 예: *“Out to AAD – userCertificate에 대한 사용자 지정 재정의”* |
    | 설명 | *설명 제공* | 예: *“userCertificate 특성에 15개 이상의 값이 있는 경우 NULL을 내보냅니다.”* |
    | 연결된 시스템 | *Azure AD 커넥터에 선택* |
    | 연결된 시스템 개체 유형 | **user** | |
    | 메타버스 개체 유형 | **person** | |
    | 링크 형식 | **Join** | |
    | 우선 순위 | *1 ~ 99 사이의 숫자 선택* | 선택한 번호는 기존의 동기화 규칙에 사용되어서는 안되며 기존 동기화 규칙보다 낮은 값(따라서 높은 우선 순위)을 포함합니다. |

3. **범위 지정 필터** 탭으로 이동하고 기존 동기화 규칙이 사용 중인 동일한 범위 지정 필터를 구현합니다.
4. **조인 규칙** 탭을 건너뜁니다.
5. **변환** 탭으로 이동하고 다음 구성을 사용하여 새 변환을 추가합니다.

    | 특성 | 값 |
    | --- | --- |
    | 흐름 형식 |**Expression** |
    | 대상 특성 |**userCertificate** |
    | 원본 특성 |*다음 식 사용*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. **추가** 단추를 클릭하여 동기화 규칙을 만듭니다.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>4단계. LargeObject 오류가 있는 기존 개체에서 새 동기화 규칙을 확인합니다.
이 단계는 생성된 동기화 규칙이 다른 개체에 적용하기 전에 LargeObject 오류가 있는 기존 AD 개체에서 올바르게 작동하는지 확인합니다.
1. Synchronization Service Manager에 있는 **작업** 탭으로 이동합니다.
2. 가장 최근의 Azure AD로 내보내기 작업을 선택하고 LargeObject 오류가 있는 개체 중 하나를 클릭합니다.
3.  커넥터 공간 개체 속성 팝업 화면에서 **미리 보기** 단추를 클릭합니다.
4. 미리 보기 팝업 화면에서 **전체 동기화**를 선택하고 **커밋 미리 보기**를 클릭합니다.
5. 미리 보기 화면과 커넥터 공간 개체 속성 화면을 닫습니다.
6. Synchronization Service Manager에 있는 **커넥터** 탭으로 이동합니다.
7. **Azure AD** 커넥터를 마우스 오른쪽 단추로 클릭하고 **실행...** 을 선택합니다.
8. 커넥터 실행 팝업에서 **내보내기** 단계를 선택하고 **확인**을 클릭합니다.
9. Azure AD로 내보내기가 완료될 때까지 기다렸다가 이 특정 개체에 LargeObject 오류가 더 이상 없는지 확인합니다.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>5단계. LargeObject 오류가 있는 나머지 개체에 새 동기화 규칙을 적용합니다.
동기화 규칙이 추가되면 AD 커넥터에서 전체 동기화 단계를 실행해야 합니다.
1. Synchronization Service Manager에 있는 **커넥터** 탭으로 이동합니다.
2. **AD** 커넥터를 마우스 오른쪽 단추로 클릭하고 **실행...** 을 선택합니다.
3. 커넥터 실행 팝업에서 **전체 동기화** 단계를 선택하고 **확인**을 클릭합니다.
4. 전체 동기화 단계가 완료될 때까지 기다립니다.
5. AD 커넥터가 둘 이상이면 나머지 AD 커넥터에 대해 위의 단계를 반복합니다. 일반적으로 여러 온-프레미스 디렉터리가 있으면 여러 커넥터가 필요합니다.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>6단계. Azure AD로 내보내기를 기다리고 있는 예기치 않은 변경 사항이 없는지 확인합니다.
1. Synchronization Service Manager에 있는 **커넥터** 탭으로 이동합니다.
2. **Azure AD** 커넥터를 마우스 오른쪽 단추로 클릭하고 **커넥터 공간 검색**을 선택합니다.
3. 커넥터 공간 검색 팝업에서:
    1. 범위를 **보류 중인 내보내기**로 설정합니다.
    2. **추가**, **수정** 및 **삭제**를 포함하여 확인란 3개를 모두 선택합니다.
    3. **검색** 단추를 클릭하면 Azure AD로 내보내기를 기다리는 변경 사항이 있는 모든 개체가 반환됩니다.
    4. 예기치 않은 변경 사항이 없는지 확인합니다. 지정된 개체에 대해 변경 사항을 검사하려면 개체를 두 번 클릭합니다.

### <a name="step-7-export-the-changes-to-azure-ad"></a>7단계. 변경 사항을 Azure AD로 내보냅니다.
변경 사항을 Azure AD로 내보내려면
1. Synchronization Service Manager에 있는 **커넥터** 탭으로 이동합니다.
2. **Azure AD** 커넥터를 마우스 오른쪽 단추로 클릭하고 **실행...** 을 선택합니다.
4. 커넥터 실행 팝업에서 **내보내기** 단계를 선택하고 **확인**을 클릭합니다.
5. Azure AD로 내보내기가 완료될 때까지 기다렸다가 LargeObject 오류가 더 이상 없는지 확인합니다.

### <a name="step-8-re-enable-sync-scheduler"></a>8단계: 동기화 스케줄러를 다시 활성화합니다.
이제 문제가 해결되었고 기본 제공 동기화 스케줄러를 다시 활성화합니다.
1. PowerShell 세션을 시작합니다.
2. cmdlet을 실행하여 예약된 동기화 다시 활성화: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> 이전 단계는 기본 제공된 스케줄러가 있는 Azure AD Connect의 최신 버전(1.1.xxx.x)에만 적용됩니다. Windows Task Scheduler를 사용하는 이전 버전(1.0.xxx.x)의 Azure AD Connect를 사용하거나 정기적인 동기화를 트리거하기 위해 사용자 고유의 사용자 지정 스케줄러(공통이 아님)를 사용하는 경우 적절하게 비활성화해야 합니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.

