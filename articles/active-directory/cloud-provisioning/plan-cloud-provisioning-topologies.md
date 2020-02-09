---
title: 지원 되는 토폴로지 및 시나리오 Azure AD Connect 클라우드 프로 비전
description: 이 항목에서는 필수 구성 요소 및 하드웨어 요구 사항 클라우드 프로 비전에 대해 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 142974423816b07d754a5425017aedc3195e2f4e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793998"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>지원 되는 토폴로지 및 시나리오 Azure AD Connect 클라우드 프로 비전
이 문서에서는 Azure AD Connect 클라우드 프로 비전을 사용 하는 다양 한 온-프레미스 및 Azure Active Directory (Azure AD) 토폴로지에 대해 설명 합니다. 이 문서에는 지원 되는 구성 및 시나리오만 포함 되어 있습니다.

> [!IMPORTANT]
> Microsoft는 공식적으로 문서화 된 구성 또는 작업을 벗어나 클라우드 프로 비전을 수정 하거나 Azure AD Connect 하는 것을 지원 하지 않습니다. 이러한 구성 또는 작업으로 인해 Azure AD Connect 클라우드 프로 비전에 일관성이 없거나 지원 되지 않는 상태가 될 수 있습니다. 결과적으로, Microsoft는 해당 배포에 대해 기술 지원을 제공할 수 없습니다.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>모든 시나리오 및 토폴로지에 대해 기억할 사항
다음은 솔루션을 선택할 때 염두에 두어야 하는 정보 목록입니다.

- 모든 포리스트에서 사용자 및 그룹을 고유 하 게 식별 해야 함
- 클라우드 프로 비전에는 포리스트 간 일치가 발생 하지 않습니다.
- 사용자 또는 그룹은 모든 포리스트에서 한 번만 표시 되어야 합니다.
- 개체에 대 한 원본 앵커는 자동으로 선택 됩니다.  Msds-consistencyguid를 사용 합니다 (있는 경우). 그렇지 않으면 ObjectGUID가 사용 됩니다.
- 원본 앵커에 사용 되는 특성은 변경할 수 없습니다.



## <a name="multi-forest-single-azure-ad-tenant"></a>다중 포리스트, 단일 Azure AD 테넌트
![다중 포리스트 및 단일 테넌트에 대 한 토폴로지](media/plan-cloud-provisioning-topologies/multi-forest.png)

가장 일반적인 토폴로지는 하나 이상의 도메인 및 단일 Azure AD 테넌트를 포함 하는 여러 AD 포리스트입니다.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Azure AD Connect 있는 기존 포리스트, 클라우드 프로 비전을 사용 하는 새 포리스트
![단일 포리스트 및 단일 테넌트에 대한 토폴로지](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

이 시나리오는 토폴로지가 다중 포리스트 시나리오와 유사 하지만이는 기존 Azure AD Connect 환경을 포함 하지만 Azure AD Connect 클라우드 프로 비전을 사용 하 여 새 포리스트를 가져오는 것입니다.  이 시나리오의 예는 [자습서: 단일 AZURE AD 테넌트를 포함 하는 기존 포리스트](tutorial-existing-forest.md) 를 참조 하세요.

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>기존 하이브리드 AD 포리스트에서 클라우드 프로 비전 Azure AD Connect 파일럿
단일 포리스트 및 단일 테넌트](media/plan-cloud-provisioning-topologies/migrate.png)에 대 한 ![토폴로지는 동일한 포리스트에 Azure AD Connect 및 Azure AD Connect 클라우드 프로 비전을 모두 포함 하 고 그에 따라 사용자 및 그룹의 범위를 지정 합니다. 참고: 개체는 도구 중 하나 에서만 범위 내에 있어야 합니다. 

이 시나리오에 대 한 예는 [자습서: 기존 동기화 된 AD 포리스트에 Azure AD Connect 클라우드 프로 비전 파일럿](tutorial-pilot-aadc-aadccp.md) 을 참조 하세요.

## <a name="single-forest-single-azure-ad-tenant"></a>단일 포리스트, 단일 Azure AD 테넌트
![단일 포리스트 및 단일 테넌트에 대한 토폴로지](media/plan-cloud-provisioning-topologies/single-forest.png)

가장 간단한 토폴로지는 하나 이상의 도메인 및 단일 Azure AD 테넌트를 포함 하는 단일 온-프레미스 포리스트입니다.  이 시나리오의 예는 [자습서: 단일 AZURE AD 테넌트를 포함 하는 단일 포리스트](tutorial-single-forest.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계 

- [프로 비전 이란?](what-is-provisioning.md)
- [클라우드 프로 비전 Azure AD Connect 이란?](what-is-cloud-provisioning.md)

