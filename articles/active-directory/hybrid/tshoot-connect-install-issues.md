---
title: Azure AD Connect 설치 문제 해결 | Microsoft Docs
description: 이 토픽에서는 Azure AD Connect 설치 문제를 해결하는 단계를 안내합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25314b4a306678dc877a95194907b3d73979e4f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89275864"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>문제 해결: Azure AD Connect 설치 이슈

## <a name="recommended-steps"></a>**권장되는 단계**
적합한 [Azure AD Connect 설치 유형](./how-to-connect-install-select-installation.md)을 확인합니다. 기본 설치 조건을 충족하는 경우 기본 설치를 선택할 것을 강력하게 권장합니다. 기본 설치는 설치를 완료하는 데 필요한 최소한의 옵션만 제공하므로 문제가 발생할 가능성이 낮습니다. 

그러나 기본 설치 기준을 충족하지 않아 사용자 지정 설치를 수행해야 하는 경우 여기서 제공하는 모범 사례를 따르면 일반적인 문제를 방지할 수 있습니다. 설명이 너무 길어지지 않도록 여기서는 몇 가지 옵션만 언급합니다.

* AAD Connect를 설치하려는 머신의 관리자인지 확인합니다. 동일한 관리자 자격 증명을 사용하여 머신에 로그인합니다.

* 다음 페이지에서 모든 옵션을 기본값으로 적용합니다. 단, 기존 SQL Server를 사용하려는 경우 "기존 SQL Server 사용"을 선택합니다. 사용자 지정 설치 옵션을 사용하는 방법에 대한 [자세한 내용](./how-to-connect-install-custom.md)을 확인하세요. 

    ![기존 SQL Server 사용](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* 다음 페이지에서 기존 계정의 권한 문제를 방지하기 위해 "새 AD 계정 만들기" 옵션을 선택합니다.

    ![AD 포리스트 계정](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**일반적인 문제**

* [온-프레미스 Active Directory 연결 이슈](./reference-connect-adconnectivitytools.md)

* [온라인 Azure Active Directory 연결 문제](./tshoot-connect-connectivity.md)

* [온-프레미스 Active Directory 권한 이슈](./how-to-connect-configure-ad-ds-connector-account.md)

## <a name="recommended-documents"></a>**권장되는 문서**
* [Azure AD Connect에 대한 필수 구성 요소](./how-to-connect-install-prerequisites.md)
* [Azure AD Connect에 사용할 설치 유형 선택](./how-to-connect-install-select-installation.md)
* [기본 설정을 사용하여 Azure AD Connect 시작](./how-to-connect-install-express.md)
* [Azure AD Connect의 사용자 지정 설치](./how-to-connect-install-custom.md)
* [Azure AD Connect: 이전 버전에서 최신 버전으로 업그레이드](./how-to-upgrade-previous-version.md)
* [Azure AD Connect: 스테이징 서버란?](./plan-connect-topologies.md#staging-server)
* [ADConnectivityTool PowerShell 모듈이란?](./how-to-connect-adconnectivitytools.md)

## <a name="next-steps"></a>다음 단계
- [Azure AD Connect 동기화](how-to-connect-sync-whatis.md)
- [하이브리드 ID란?](whatis-hybrid-identity.md)