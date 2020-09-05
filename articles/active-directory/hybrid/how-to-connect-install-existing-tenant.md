---
title: 'Azure AD Connect: Azure AD가 이미 있는 경우 | Microsoft Docs'
description: 이 문서에서는 기존 Azure AD 테넌트가 있을 때 Connect를 사용하는 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 582ec01a7a843358bef749aec693a59f88a1d655
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640653"
---
# <a name="azure-ad-connect-when-you-have-an-existing-tenant"></a>Azure AD Connect: 기존 테 넌 트가 있는 경우
Azure AD Connect를 사용하는 방법에 대한 항목 중 대부분은 새 Azure AD 테넌트로 시작하고 여기에는 사용자 또는 다른 개체가 없다고 가정하고 있습니다. 그러나 이미 Azure AD 테넌트로 시작하여 사용자와 다른 개체를 제공한 후에 Connect를 사용하려는 경우 이 문서가 도움이 됩니다.

## <a name="the-basics"></a>기본 사항
Azure AD의 개체는 클라우드(Azure AD) 또는 온-프레미스에서 마스터됩니다. 단일 개체의 경우 온-프레미스의 일부 특성과 Azure AD의 다른 일부 특성은 관리할 수 없습니다. 각 개체에는 해당 개체를 관리하는 위치를 나타내는 플래그가 있습니다.

온-프레미스의 일부 사용자와 Azure AD의 다른 사용자는 관리할 수 있습니다. 이 구성의 일반적인 시나리오는 회계 직원과 판매 직원이 혼합된 조직입니다. 회계 직원은 온-프레미스 AD 계정을 갖는 반면 판매 직원은 그렇지 않습니다. 판매 직원은 Azure AD에서 계정을 갖습니다. 온-프레미스의 일부 사용자와 Azure AD의 일부 사용자를 관리합니다.

Azure AD와 온-프레미스에 있는 사용자를 관리하기 시작했으며 나중에 Connect를 사용하려는 경우 고려해야 할 몇 가지 추가 사항이 있습니다.

## <a name="sync-with-existing-users-in-azure-ad"></a>Azure AD의 기존 사용자와 동기화
Azure AD Connect를 설치 하 고 동기화를 시작 하면 azure ad의 Azure AD sync 서비스에서 모든 새 개체를 확인 하 고 일치 하는 기존 개체를 찾으려고 시도 합니다. 이 프로세스에는 **userPrincipalName**, **proxyAddresses** 및 **sourceAnchor**/**immutableID**의 세 가지 특성이 사용됩니다. **userPrincipalName** 및 **proxyAddresses**에 대한 일치를 **소프트 일치**라고 하며, **sourceAnchor**에 대한 일치는 **하드 일치**라고 합니다. 기본 전자 메일 주소인 **proxyAddresses** 특성의 경우 **SMTP:** 가 있는 값만 평가에 사용됩니다.

일치는 Connect에서 나오는 새 개체에 대해서만 평가됩니다. 이러한 특성 중 하나와 일치하도록 기존 개체를 변경하는 경우 오류가 대신 표시됩니다.

Azure AD가 Connect에서 나오는 개체의 특성 값과 동일하고 Azure AD에 이미 있는 개체를 찾으면 Connect에서 Azure AD의 개체를 받습니다. 이전에 클라우드에서 관리된 개체는 온-프레미스 관리 대상으로 플래그가 지정됩니다. 온 프레미스 AD의 값이 있는 Azure AD의 모든 특성은 온-프레미스 값으로 덮어씁니다. 예외는 특성에 **NULL** 온-프레미스 값이 있는 경우입니다. 이 경우 Azure AD의 값은 그대로 유지되지만 온-프레미스에서 다른 값으로만 변경할 수 있습니다.

> [!WARNING]
> Azure AD의 모든 특성은 온-프레미스 값으로 덮어쓰게 되므로 온-프레미스 데이터가 양호한지 확인해야 합니다. 예를 들어 Office 365에서만 전자 메일 주소를 관리하고 온-프레미스 AD DS에서 전자 메일 주소를 업데이트하지 않은 경우 AD DS에 없는 Azure AD/Office 365의 값을 잃게 됩니다.

> [!IMPORTANT]
> 항상 기본 설정으로 사용되는 암호 동기화를 사용하는 경우 Azure AD의 암호는 온-프레미스 AD의 암호로 덮어씁니다. 사용자가 다른 암호를 관리하는 데 사용되는 경우 Connect를 설치할 때 온-프레미스 암호를 사용해야 한다고 사용자에게 알려야 합니다.

이전 섹션과 경고는 계획에 고려되어야 합니다. 온-프레미스 AD DS에 반영되지 않은 변경을 Azure AD에서 많이 수행한 경우에는 개체를 Azure AD Connect와 동기화하기 전에 업데이트된 값으로 AD DS를 채우는 방법을 계획해야 합니다.

개체를 소프트 일치로 일치시키는 경우 Azure AD의 개체에 **sourceAnchor**가 추가되어 나중에 하드 일치를 사용할 수 있습니다.

>[!IMPORTANT]
> Azure Active Directory의 기존 관리 계정과 온-프레미스 계정을 동기화하지 않는 것이 좋습니다.

### <a name="hard-match-vs-soft-match"></a>하드 일치 및 소프트 일치
Connect를 새로 설치하는 경우 소프트 일치와 하드 일치 사이에 실질적인 차이가 없습니다. 차이점은 재해 복구 상황에 있습니다. Azure AD Connect를 사용하여 서버를 잃어버린 경우 데이터 손실 없이 새 인스턴스를 다시 설치할 수 있습니다. sourceAnchor가 있는 개체는 초기 설치 시 Connect로 보내집니다. 그런 다음 Azure AD에서 동일하게 수행하는 것보다 훨씬 빠른 클라이언트(Azure AD Connect)에서 일치를 평가할 수 있습니다. 하드 일치는 Connect와 Azure AD 모두에서 평가되지만, 소프트 일치는 Azure AD에서만 평가됩니다.

### <a name="other-objects-than-users"></a>사용자 이외의 다른 개체
메일이 설정된 그룹 및 연락처의 경우 proxyAddresses에 따라 소프트 매치를 수행할 수 있습니다. 하드 매치는 사용자 전용의 sourceAnchor/immutableID(PowerShell 사용)만을 업데이트할 수 있으므로 적용되지 않습니다. 메일이 사용되지 않는 그룹의 경우 현재 소프트 매치 또는 하드 매치가 지원되지 않습니다.

### <a name="admin-role-considerations"></a>관리자 역할 고려 사항
신뢰할 수 없는 온-프레미스 사용자가 관리자 역할이 있는 클라우드 사용자와 일치 하지 않도록 하기 위해 Azure AD Connect는 관리 역할이 있는 개체와 온-프레미스 사용자 개체를 일치 시 키 지 않습니다. 이 기능은 기본적으로 설정됩니다. 이 동작을 해결 하려면 다음을 수행할 수 있습니다.

1.  클라우드 전용 사용자 개체에서 디렉터리 역할을 제거 합니다.
2.  실패 한 사용자 동기화 시도가 있는 경우 클라우드에서 격리 된 개체를 하드 삭제 합니다.
3.  동기화를 트리거합니다.
4.  필요에 따라 일치가 발생 한 후 디렉터리 역할을 클라우드의 사용자 개체에 다시 추가 합니다.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Azure AD의 데이터에서 새로운 온-프레미스 Active Directory 만들기
일부 고객은 Azure AD를 사용하는 클라우드 전용 솔루션으로 시작하고 온-프레미스 AD를 사용하지 않습니다. 나중에 온-프레미스 리소스를 사용하지만 Azure AD 데이터를 기반으로 하는 온-프레미스 AD를 구축하려고 합니다. 이 시나리오에서는 Azure AD Connect가 도움이 되지 않습니다. 온-프레미스 사용자를 만들지 않으며 온-프레미스 암호를 Azure AD에서와 동일하게 설정할 수 있는 기능이 없습니다.

온-프레미스 AD를 추가하려는 유일한 이유가 LOB(기간 업무 앱)를 지원하는 것이라면 [Azure AD 도메인 서비스](../../active-directory-domain-services/index.yml)를 대신 사용해야 합니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
