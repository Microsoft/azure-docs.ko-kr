---
title: Azure AD를 사용한 페더레이션이란? | Microsoft Docs
description: Azure AD를 사용한 페더레이션에 대해 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb1c3bc9f89db3f4b694803a63293a5537d4b98b
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89278755"
---
# <a name="what-is-federation-with-azure-ad"></a>Azure AD를 사용한 페더레이션이란?

페더레이션은 신뢰가 설정된 도메인의 컬렉션입니다. 신뢰 수준은 다른 경우도 있지만 일반적으로 인증 및 권한 부여가 포함됩니다. 일반적인 페더레이션은 리소스 집합에 대한 공유 액세스에서 신뢰가 설정된 여러 조직을 포함할 수 있습니다.

Azure AD에서 온-프레미스 환경을 페더레이션하고 인증 및 권한 부여에 이 페더레이션을 사용할 수 있습니다.  이 로그인 메서드를 통해 모든 사용자 인증이 온-프레미스에서 발생하도록 합니다.  이 메서드를 사용하면 관리자가 좀 더 엄격한 수준의 액세스 제어를 구현할 수 있습니다. AD FS와 PingFederate에서 페더레이션이 지원됩니다.

![페더레이션 ID](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> AD FS(Active Directory Federation Services)와 페더레이션을 사용하도록 선택하는 경우, 필요에 따라 AD FS 인프라에 장애가 발생할 경우 백업으로 암호 해시 동기화를 설정할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- [하이브리드 ID란?](./whatis-hybrid-identity.md)
- [Azure AD Connect 및 Connect Health란?](whatis-azure-ad-connect.md)
- [암호 해시 동기화란?](whatis-phs.md)
- [페더레이션이란?](whatis-fed.md)
- [Single Sign-On이란?](how-to-connect-sso.md)
- [페더레이션 작동 방식](how-to-connect-fed-whatis.md)
- [PingFederate을 사용한 페더레이션](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)