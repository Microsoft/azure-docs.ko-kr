---
title: Azure AD Connect 클라우드 프로비저닝 FAQ
description: 이 문서에서는 클라우드 프로비저닝에 대한 질문과 대답을 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2067bea25dae05c496c81929ae65d00565bf4f1
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020763"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect FAQ

Azure AD(Azure Active Directory) Connect 클라우드 프로비저닝에 대한 질문과 대답을 참조하세요.

## <a name="general-installation"></a>일반 설치

**Q: 클라우드 프로비저닝은 얼마나 자주 실행되나요?**

클라우드 프로비저닝은 2분마다 실행되도록 예약되어 있습니다. 2분마다 모든 사용자, 그룹 및 암호 해시 변경 내용이 Azure AD에 프로비저닝됩니다.

**Q: 첫 번째 실행에서 암호 해시 동기화 실패가 표시됩니다. 그 이유는 무엇일까요?**

예상된 동작입니다. 이 실패는 Azure AD에 없는 사용자 개체로 인해 발생한 것입니다. 사용자가 Azure AD에 프로비저닝되면 이후 실행에서 암호 해시를 프로비저닝해야 합니다. 몇 번의 실행을 기다린 후에 암호 해시 동기화에 오류가 더 이상 없는지 확인합니다.

**Q: Active Directory 인스턴스에 클라우드 프로비저닝에서 지원하지 않는 특성(예: 디렉터리 확장)이 있는 경우 어떻게 되나요?**

클라우드 프로비저닝은 지원되는 특성을 실행하고 프로비저닝합니다. 지원되지 않는 특성은 Azure AD에 프로비저닝되지 않습니다. Active Directory에서 디렉터리 확장을 검토하고 이러한 특성이 Azure AD로 이동할 필요가 없는지 확인합니다. 하나 이상의 특성이 필요한 경우 Azure AD Connect 동기화를 사용하거나 필요한 정보를 지원하는 특성 중 하나(예: 확장 특성 1-15)로 이동합니다.

**Q: Azure AD Connect 동기화와 클라우드 프로비저닝의 차이점은 무엇인가요?**

Azure AD Connect 동기화를 사용하면 온-프레미스 동기화 서버에서 프로비저닝이 실행됩니다. 구성은 온-프레미스 동기화 서버에 저장됩니다. Azure AD Connect 클라우드 프로비저닝을 사용하면 프로비저닝 구성이 클라우드에 저장되고 Azure AD 프로비저닝 서비스의 일부로 클라우드에서 실행됩니다. 

**Q: 클라우드 프로비저닝을 사용하여 여러 Active Directory 포리스트에서 동기화할 수 있나요?**

예. 클라우드 프로비저닝을 사용하여 여러 Active Directory 포리스트에서 동기화할 수 있습니다. 다중 포리스트 환경에서는 모든 참조(예: 관리자)가 도메인 내에 있어야 합니다.  

**Q: 에이전트를 업데이트하려면 어떻게 하나요?**

에이전트는 Microsoft에서 자동으로 업그레이드합니다. IT 팀의 경우 이를 통해 새 에이전트 버전을 테스트하고 유효성을 검사해야 하는 부담을 줄일 수 있습니다. 

**Q: 자동 업그레이드를 사용하지 않도록 설정할 수 있나요?**

자동 업그레이드를 사용하지 않도록 설정하는 방법은 지원되지 않습니다.

**Q: 클라우드 프로비저닝에 맞게 원본 앵커를 변경할 수 있나요?**

클라우드 프로비저닝은 기본적으로 ObjectGUID를 원본 앵커로 대체한 ms-ds-consistency-GUID를 사용합니다. 원본 앵커를 변경하는 방법은 지원되지 않습니다.

**Q: 클라우드 프로비저닝을 사용하는 경우 AD 도메인 이름이 있는 새 서비스 주체가 표시됩니다. 필요한가요?**

예, 클라우드 프로비저닝은 도메인 이름을 서비스 주체 이름으로 사용하여 프로비저닝 구성에 대한 서비스 주체를 만듭니다. 서비스 주체 구성은 변경하지 마세요.

**Q: 다음 번에 로그온 할 때 동기화된 사용자가 암호를 변경해야 하는 경우 어떻게 되나요?**

클라우드 프로비저닝에서 암호 해시 동기화를 사용하도록 설정하고 동기화된 사용자가 다음 번에 온-프레미스 AD에서 로그온할 때 암호를 변경해야 하는 경우 클라우드 프로비저닝은 암호 해시를 Azure AD로 변경하도록 프로비저닝하지 않습니다. 사용자가 암호를 변경하면 사용자 암호 해시가 AD에서 Azure AD로 프로비저닝됩니다.

**Q: 클라우드 프로비저닝에서 모든 개체에 대해 ms-ds-consistencyGUID의 쓰기 저장을 지원하나요?**

아니요, 클라우드 프로비저닝은 모든 개체(사용자 개체 포함)에 대해 ms-ds-consistencyGUID의 쓰기 저장을 지원하지 않습니다. 

**Q: 클라우드 프로비저닝을 사용하여 사용자를 프로비저닝하지만, 구성을 삭제했습니다. Azure AD에서 이전에 동기화된 개체가 계속 표시되는 이유는 무엇인가요?** 

구성을 삭제하는 경우 클라우드 프로비저닝은 Azure AD에서 동기화된 개체를 정리하지 않습니다. 이전 개체가 없는지 확인하려면 구성 범위를 빈 그룹 또는 조직 구성 단위로 변경합니다. 프로비저닝이 실행되고 개체가 정리되면 구성을 사용하지 않도록 설정하고 삭제합니다. 

## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)
