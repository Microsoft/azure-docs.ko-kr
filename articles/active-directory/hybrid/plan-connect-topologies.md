---
title: 'Azure AD Connect: 지원되는 토폴로지 | Microsoft Docs'
description: 이 항목은 Azure AD Connect에 대해 지원되고 지원되지 않는 토폴로지에 대해 자세히 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: conceptual
ms.date: 11/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47973a8444de64dc5c2bb75b5f0d65d1e6d35f6e
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509089"
---
# <a name="topologies-for-azure-ad-connect"></a>Azure AD Connect에 대한 토폴로지
이 문서에서는 주요 통합 솔루션으로 Azure AD Connect Sync를 사용하는 다양한 온-프레미스 및 Azure AD(Azure Active Directory) 토폴로지에 대해 설명합니다. 이 문서에는 지원되는 구성과 지원되지 않는 구성이 포함되어 있습니다.


다음은 문서의 그림에 대한 범례입니다.

| 설명 | 기호 |
| --- | --- |
| 온-프레미스 Active Directory 포리스트 |![온-프레미스 Active Directory 포리스트](./media/plan-connect-topologies/LegendAD1.png) |
| 필터링된 가져오기를 사용한 온-프레미스 Active Directory |![필터링된 가져오기를 사용한 Active Directory](./media/plan-connect-topologies/LegendAD2.png) |
| Azure AD Connect Sync 서버 |![Azure AD Connect Sync 서버](./media/plan-connect-topologies/LegendSync1.png) |
| Azure AD Connect Sync 서버 “스테이징 모드” |![Azure AD Connect Sync 서버 “스테이징 모드”](./media/plan-connect-topologies/LegendSync2.png) |
| FIM(Forefront Identity Manager) 2010 또는 MIM(Microsoft Identity Manager) 2016을 사용하는 GALSync |![FIM 2010 또는 MIM 2016을 사용하는 GALSync](./media/plan-connect-topologies/LegendSync3.png) |
| Azure AD Connect Sync 서버, 자세히 설명됨 |![Azure AD Connect Sync 서버, 자세히 설명됨](./media/plan-connect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/LegendAAD.png) |
| 지원되지 않는 시나리오 |![지원되지 않는 시나리오](./media/plan-connect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> Microsoft는 공식적으로 문서화된 구성 또는 작업 외의 Azure AD Connect 동기화에 대한 수정 또는 작업을 지원하지 않습니다. 이러한 구성 또는 작업으로 인해 Azure AD Connect 동기화 상태가 일치 하지 않거나 지원 되지 않을 수 있습니다. 따라서 Microsoft는 이러한 배포에 대 한 기술 지원을 제공할 수 없습니다.


## <a name="single-forest-single-azure-ad-tenant"></a>단일 포리스트, 단일 Azure AD 테넌트
![단일 포리스트 및 단일 테넌트에 대한 토폴로지](./media/plan-connect-topologies/SingleForestSingleDirectory.png)

가장 일반적인 토폴로지는 도메인 하나 또는 여러 개와 Azure AD 테넌트 하나를 사용하는 단일 온-프레미스 포리스트입니다. Azure AD 인증의 경우 암호 해시 동기화가 사용됩니다. Azure AD Connect의 빠른 설치는 이 토폴로지만 지원합니다.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>단일 포리스트, 여러 동기화 서버를 하나의 Azure AD 테넌트로
![단일 포리스트에 지원되지 않는 필터링된 토폴로지](./media/plan-connect-topologies/SingleForestFilteredUnsupported.png)

[준비 서버](#staging-server)를 제외하고 동일한 Azure AD 테넌트에 여러 Azure AD Connect Sync 서버를 연결하는 것은 지원되지 않습니다. 이러한 서버가 상호 배타적 개체 집합을 동기화하도록 구성된 경우에도 지원되지 않습니다. 단일 서버 포리스트의 모든 도메인에 연결할 수 없는 경우 또는 여러 서버에 부하를 분산하려는 경우에 이 토폴로지를 고려해 볼 수 있습니다.

## <a name="multiple-forests-single-azure-ad-tenant"></a>여러 포리스트, 단일 Azure AD 테넌트
![여러 포리스트 및 단일 테넌트에 대한 토폴로지](./media/plan-connect-topologies/MultiForestSingleDirectory.png)

많은 조직에는 다중 온-프레미스 Active Directory 포리스트가 있는 환경이 있습니다. 둘 이상의 온-프레미스 Active Directory 포리스트가 있는 이유는 여러 가지가 있습니다. 대표적인 예는 계정 리소스 포리스트를 사용하는 디자인과 합병 또는 인수의 결과입니다.

포리스트가 여러 개인 경우 단일 Azure AD Connect Sync 서버에서 모든 포리스트에 연결할 수 있어야 합니다. 서버는 도메인에 가입 되어 있어야 합니다. 모든 포리스트에 연결해야 하는 경우 서버를 경계 네트워크(DMZ, 완충 지역 또는 스크린된 서브넷이라고도 함)에 배치할 수 있습니다.

Azure AD Connect 설치 마법사는 여러 포리스트에 표시되는 사용자를 통합하는 몇 가지 옵션을 제공합니다. 이러한 옵션의 목표는 사용자를 Azure AD에서 한 번만 표시하는 것입니다. 설치 마법사에서 사용자 지정 설치 경로에 구성할 수 있는 몇 가지 일반적인 토폴로지가 있습니다. **사용자를 고유하게 식별** 페이지에서 해당 토폴로지를 나타내는 옵션을 선택합니다. 통합은 사용자에 대해서만 구성됩니다. 중복된 그룹은 기본 구성과 통합되지 않습니다.

일반적인 토폴로지는 별도의 토폴로지, [전체 메시](#multiple-forests-full-mesh-with-optional-galsync)및 [계정 리소스 토폴로지에](#multiple-forests-account-resource-forest)대 한 섹션에서 설명 합니다.

Azure AD Connect 동기화의 기본 구성에서 다음 사항을 가정합니다.

* 각 사용자는 하나의 계정만 사용할 수 있으며 이 계정이 위치한 포리스트는 사용자를 인증하는 데 사용됩니다. 이 가정은 암호 해시 동기화, 통과 인증 및 페더레이션에 대한 것입니다. UserPrincipalName 및 sourceAnchor/immutableID는 이 포리스트에서 제공됩니다.
* 사용자마다 사서함이 하나씩만 제공됩니다.
* 사용자의 사서함을 호스트하는 포리스트는 Exchange GAL(전체 주소 목록)에 표시되는 특성에 대해 최상의 데이터 품질을 가집니다. 사용자의 사서함이 없는 경우 아무 포리스트를 사용하여 이러한 특성 값을 부여할 수 있습니다.
* 연결된 사서함이 있으면 로그인에 사용되는 다른 포리스트에도 계정이 있습니다.

현재 환경이 이러한 가정과 일치하지 않은 경우 다음 상황이 발생합니다.

* 활성 계정 또는 사서함이 두 개 이상이면 동기화 엔진이 하나를 선택하고 다른 하나를 무시합니다.
* 다른 활성 계정이 없으면 연결된 사서함을 Azure AD로 내보내지 않습니다. 임의 그룹의 구성원으로 사용자 계정이 표시되지 않습니다. DirSync에 있는 연결된 사서함은 항상 일반 사서함으로 표시됩니다. 이 변경 사항은 다중 포리스트 시나리오를 보다 적절하게 지원하기 위해 의도된 동작입니다.

자세한 내용은 [기본 구성 이해](concept-azure-ad-connect-sync-default-configuration.md)에서 확인할 수 있습니다.

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>여러 포리스트, 여러 동기화 서버를 하나의 Azure AD 테넌트로
![다중 포리스트 및 다중 동기화 서버에 지원되지 않는 토폴로지](./media/plan-connect-topologies/MultiForestMultiSyncUnsupported.png)

단일 Azure AD 테넌트에 Azure AD Connect Sync 서버를 두 개 이상 연결하는 것은 지원되지 않습니다. [준비 서버](#staging-server)사용은 예외입니다.

단일 Azure AD 테넌트에 연결된 **여러 동기화 서버**와 다른 이 토폴로지는 지원되지 않습니다.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>여러 포리스트, 단일 동기화 서버, 사용자가 하나의 디렉터리에 표시됩니다
![모든 디렉터리에서 사용자를 한 번만 표시하는 옵션](./media/plan-connect-topologies/MultiForestUsersOnce.png)

![다중 포리스트 및 별도의 토폴로지에 대한 설명](./media/plan-connect-topologies/MultiForestSeparateTopologies.png)

이 환경에서는 모든 온-프레미스 포리스트가 별도의 엔터티로 처리됩니다. 사용자가 다른 포리스트에 표시되지 않습니다. 각 포리스트에는 자체 Exchange 조직이 있으며 포리스트 사이에 GALSync가 없습니다. 이 토폴로지는 합병/인수 후 또는 각 사업부가 서로 독립적으로 운영되는 조직 내의 상황일 수 있습니다. 이러한 포리스트가 Azure AD의 동일한 조직 내에 있으며 통합된 GAL과 함께 표시됩니다. 앞의 그림에서 모든 포리스트에 있는 각 개체는 메타버스에 한 번만 표시되고 대상 Azure AD 테넌트에서 집계됩니다.

### <a name="multiple-forests-match-users"></a>다중 포리스트, 사용자 일치
이 모든 시나리오의 공통점은 분산 및 보안 그룹이 다양한 사용자, 연락처 및 FSP(외부 보안 주체)를 포함할 수 있다는 것입니다. FSP는 AD DS(Active Directory Domain Services)에 사용되어 보안 그룹 내 다른 포리스트의 멤버를 나타냅니다. 모든 FSP는 Azure AD에서 실제 개체로 확인됩니다.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>다중 포리스트: GALSync 선택 사항이 제공되는 전체 메시
![여러 디렉터리에 사용자 ID가 있을 때 메일 특성을 사용하여 사용자를 일치시키는 옵션](./media/plan-connect-topologies/MultiForestUsersMail.png)

![다중 포리스트를 위한 전체 메시 토폴로지](./media/plan-connect-topologies/MultiForestFullMesh.png)

전체 메시 토폴로지는 사용자와 리소스를 원하는 포리스트에 배치할 수 있습니다. 일반적으로 포리스트 간에 양방향 트러스트가 있습니다.

둘 이상의 포리스트에 Exchange가 있으면 온-프레미스 GALSync 솔루션이 선택적으로 제공될 수 있습니다. 그러면 모든 사용자는 그 외의 모든 포리스트에 연락처로 표시됩니다. GALSync는 일반적으로 FIM 2010 또는 MIM 2016을 통해 구현됩니다. Azure AD Connect를 온-프레미스 GALSync에 사용할 수 없습니다.

이 시나리오에서 ID 개체는 메일 특성을 통해 조인됩니다. 한 포리스트에 사서함이 있는 사용자는 다른 포리스트의 연락처와 조인됩니다.

### <a name="multiple-forests-account-resource-forest"></a>다중 포리스트: 계정 리소스 포리스트
![여러 디렉터리에 ID가 있을 때 ObjectSID 및 msExchMasterAccountSID 특성을 사용하여 ID를 일치시키는 옵션](./media/plan-connect-topologies/MultiForestUsersObjectSID.png)

![다중 포리스트를 위한 계정 리소스 포리스트 토폴로지](./media/plan-connect-topologies/MultiForestAccountResource.png)

계정 리소스 포리스트 토폴로지에는 활성 사용자 계정이 있는 하나 이상의 *계정* 포리스트가 있습니다. 또한 비활성화 된 계정이 있는 하나 이상의 *리소스* 포리스트가 있습니다.

이 시나리오에서는 하나 이상의 리소스 포리스트가 모든 계정 포리스트를 신뢰합니다. 리소스 포리스트에는 일반적으로 Exchange 및 Lync와 확장된 Active Directory 스키마가 있습니다. 다른 공유 서비스는 물론이고 모든 Exchange 및 Lync 서비스도 이 포리스트에 배치됩니다. 사용자는 이 포리스트에 비활성화된 계정을 가지며 사서함이 계정 포리스트에 연결됩니다.

## <a name="office-365-and-topology-considerations"></a>Office 365 및 토폴로지 고려 사항
일부 Office 365 워크로드의 경우 지원되는 토폴로지에 약간의 제한이 있습니다.

| 워크로드 | 제한 |
| --------- | --------- |
| Exchange Online | Exchange Online에서 지원하는 하이브리드 토폴로지에 대한 자세한 내용은 [여러 Active Directory 포리스트를 사용한 하이브리드 배포](https://technet.microsoft.com/library/jj873754.aspx)를 참조하세요. |
| 비즈니스용 Skype | 다중 포리스트 온-프레미스를 사용하는 경우 계정 리소스 포리스트 토폴로지만 지원됩니다. 자세한 내용은 [Business Server 2015용 Skype에 대한 환경 요구 사항](https://technet.microsoft.com/library/dn933910.aspx)을 참조하세요. |

조직의 규모가 큰 경우에는 [Office 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) 기능을 사용하는 것이 좋습니다. 사용자의 리소스가 배치되는 데이터 센터 지역을 정의할 수 있습니다.

## <a name="staging-server"></a>스테이징 서버
![토폴로지의 준비 서버](./media/plan-connect-topologies/MultiForestStaging.png)

Azure AD Connect는 *준비 모드*에서 두 번째 서버의 설치를 지원합니다. 이 모드에서 서버는 모든 연결된 디렉터리에서 데이터를 읽지만 연결된 디렉터리에는 아무 것도 쓰지 않습니다. 일반 동기화 주기를 사용하므로 ID 데이터의 업데이트된 복사본을 갖게 됩니다.

주 서버가 실패하면 준비 서버로 장애 조치(failover)할 수 있습니다. Azure AD Connect 마법사에서 이 작업을 수행합니다. 기본 서버와 인프라를 공유하지 않으므로 두 번째 서버를 다른 데이터 센터에 배치할 수 있습니다. 주 서버에서 적용한 모든 구성 변경 사항은 두 번째 서버에 수동으로 복사해야 합니다.

준비 서버는 새로운 사용자 지정 구성 및 그 구성이 데이터에 미치는 효과를 테스트하는 데 사용할 수 있습니다. 변경 내용을 미리보고 구성을 조정할 수 있습니다. 새 구성에 만족한다면 준비 서버를 활성 서버로 만들고 이전 활성 서버를 준비 모드로 설정할 수 있습니다.

이 메서드는 활성 동기화 서버를 교체하는 데도 사용할 수 있습니다. 새 서버를 준비하고 준비 모드로 설정합니다. 정상 상태인지 확인하고, 활성 상태로 만들어 준비 모드를 해제하고, 현재 활성 서버를 종료합니다.

여러 데이터 센터에 여러 백업을 보유하려는 경우 준비 서버를 여러 개 둘 수도 있습니다.

## <a name="multiple-azure-ad-tenants"></a>여러 Azure AD 테넌트
조직의 Azure AD에 테넌트를 하나만 보유할 것을 권장합니다.
다중 Azure AD 테넌트를 사용하기 위한 계획을 세우기 전에 [Azure AD에서 관리 단위 관리](../users-groups-roles/directory-administrative-units.md)를 참조하세요. 단일 테넌트를 사용할 수 있는 일반적인 시나리오에 대해 설명되어 있습니다.

![다중 포리스트 및 다중 테넌트를 위한 토폴로지](./media/plan-connect-topologies/MultiForestMultiDirectory.png)

Azure AD Connect Sync 서버와 Azure AD 테넌트가 1:1 관계입니다. 각 Azure AD 테넌트에 하나의 Azure AD Connect 동기화 서버 설치가 필요합니다. Azure AD 테넌트 인스턴스는 서로 격리되도록 설계되었습니다. 즉, 한 테넌트의 사용자가 다른 테넌트의 사용자를 볼 수 없습니다. 이러한 격리를 원하는 경우 이 구성이 지원됩니다. 그렇지 않으면 단일 Azure AD 테넌트 모델을 사용해야 합니다.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Azure AD 테넌트에서 각 개체가 한 번만
![단일 포리스트에 대한 필터링된 토폴로지](./media/plan-connect-topologies/SingleForestFiltered.png)

이 토폴로지에서는 하나의 Azure AD Connect 동기화 서버가 각 Azure AD 테넌트에 연결됩니다. 각 서버가 상호 배타적인 개체 집합을 운영하도록 Azure AD Connect Sync 서버에 필터링을 구성해야 합니다. 예를 들어 각 서버의 범위를 특정 도메인 또는 조직 단위로 지정할 수 있습니다.

DNS 도메인은 단일 Azure AD 테넌트에만 등록할 수 있습니다. 온-프레미스 Active Directory 인스턴스의 사용자 UPN도 별도의 네임스페이스를 사용해야 합니다. 예를 들어 위 그림에서 별도의 UPN 접미사 세 개가 contoso.com, fabrikam.com 및 wingtiptoys.com의 온-프레미스 Active Directory 인스턴스에 등록됩니다. 각 온-프레미스 Active Directory 도메인의 사용자는 서로 다른 네임스페이스를 사용합니다.

>[!NOTE]
>GalSync(전체 주소 목록 동기화 차단)는 이 토폴로지에서 자동으로 수행되지 않으며, 각 테넌트가 Exchange Online 및 비즈니스용 Skype Online에서 완전한 GAL(전체 주소 목록)을 갖도록 보장하는 추가 사용자 지정 MIM을 구현해야 합니다.


이 토폴로지는 그 외의 지원되는 시나리오에 대해 다음과 같은 제한 사항이 있습니다.

* 하나의 Azure AD 테넌트만 온-프레미스 Active Directory 인스턴스를 사용하여 Exchange 하이브리드를 활성화할 수 있습니다.
* Windows 10 디바이스는 하나의 Azure AD 테넌트에만 연결할 수 있습니다.
* 암호 해시 동기화 및 통과 인증에 대한 SSO(Single Sign-On) 옵션은 하나의 Azure AD 테넌트에만 사용할 수 있습니다.

상호 배타적인 집합 개체에 대한 요구 사항은 쓰기 저장에도 적용됩니다. 이 토폴로지는 단일 온-프레미스 구성을 전제로 하기 때문에 일부 쓰기 저장 기능이 지원되지 않습니다. 이러한 기능으로는 다음이 포함됩니다.

* 기본 구성으로 쓰기 저장 그룹화.
* 디바이스 쓰기 저장.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Azure AD 테넌트에서 각 개체가 여러 번
![단일 포리스트 및 다중 테넌트를 지원하지 않는 토폴로지](./media/plan-connect-topologies/SingleForestMultiDirectoryUnsupported.png) ![단일 포리스트 및 다중 커넥터를 지원하지 않는 토폴로지](./media/plan-connect-topologies/SingleForestMultiConnectorsUnsupported.png)

다음 작업은 지원되지 않습니다.

* 동일한 사용자를 여러 Azure AD 테넌트와 동기화.
* 한 Azure AD 테넌트의 사용자가 다른 Azure AD 테넌트에 연락처로 표시되도록 구성 변경.
* 여러 Azure AD 테넌트에 연결되도록 Azure AD Connect Sync 수정.

### <a name="galsync-by-using-writeback"></a>쓰기 저장을 사용한 GALSync
![다중 포리스트 및 다중 디렉터리를 지원하지 않는 토폴로지, Azure AD에 집중하는 GALSync 사용](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![다중 포리스트 및 다중 디렉터리를 지원하지 않는 토폴로지, 온-프레미스 Active Directory에 집중하는 GALSync 사용](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD 테넌트는 서로 격리되도록 설계되었습니다. 다음 작업은 지원되지 않습니다.

* 다른 Azure AD 테넌트에서 데이터를 읽도록 Azure AD Connect Sync의 구성 변경.
* Azure AD Connect Sync를 사용하여 사용자를 다른 온-프레미스 Active Directory 인스턴스에 연락처로 내보내기.

### <a name="galsync-with-on-premises-sync-server"></a>온-프레미스 동기화 서버로 GALSync
![다중 포리스트 및 다중 디렉터리를 위한 토폴로지의 GALSync](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync.png)

FIM 2010 또는 MIM 2016 온-프레미스를 사용하여 두 Exchange 조직 간에 GALSync를 통해 사용자를 동기화할 수 있습니다. 한 조직의 사용자는 다른 조직에서 외부 사용자/연락처로 표시됩니다. 이러한 여러 온-프레미스 Active Directory 인스턴스를 각각 자체 Azure AD 테넌트와 동기화할 수 있습니다.

### <a name="using-unauthorized-clients-to-access-the-azure-ad-connect-backend"></a>권한 없는 클라이언트를 사용 하 여 Azure AD Connect 백 엔드에 액세스
![권한 없는 클라이언트를 사용 하 여 Azure AD Connect 백 엔드에 액세스](./media/plan-connect-topologies/other-client-unsupported.png)

Azure Active Directory Connect 서버는 Azure Active Directory Connect 백 엔드를 통해 Azure Active Directory와 통신 합니다. 이 백 엔드와 통신 하는 데 사용할 수 있는 유일한 소프트웨어는 Azure Active Directory Connect입니다. 다른 소프트웨어나 방법을 사용 하 여 Azure Active Directory Connect 백 엔드와 통신할 수 없습니다. 

## <a name="next-steps"></a>다음 단계
이러한 시나리오에 대해 Azure AD Connect를 설치하는 방법을 알아보려면 [Azure AD Connect의 사용자 지정 설치](how-to-connect-install-custom.md)를 참조하세요.

[Azure AD Connect 동기화](how-to-connect-sync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 id 통합](whatis-hybrid-identity.md)에 대해 자세히 알아보세요.
