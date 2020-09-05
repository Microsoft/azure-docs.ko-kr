---
title: 라이선스 셀프 서비스 암호 재설정 - Azure Active Directory
description: 셀프 서비스 암호 재설정 라이선스 요구 사항 Azure Active Directory 차이점에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: c24698736d611604c39b6fa91aef97f73576c6c6
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716211"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Azure Active Directory 셀프 서비스 암호 재설정에 대 한 라이선스 요구 사항

사용자가 장치 또는 응용 프로그램에 로그인 할 수 없는 경우 지원 센터 호출 및 생산성 손실을 줄이기 위해 SSPR (셀프 서비스 암호 재설정)에 대해 Azure Active Directory (Azure AD)의 사용자 계정을 사용할 수 있습니다. SSPR을 구성 하는 기능에는 암호 변경, 다시 설정, 잠금 해제 및 온-프레미스 디렉터리에 쓰기 저장이 포함 됩니다. 기본 SSPR 기능은 Microsoft 365 Business Standard 이상 및 모든 Azure AD Premium Sku에서 무료로 제공 됩니다.

이 문서에서는 셀프 서비스 암호 재설정을 사용 하 고 사용 하는 다양 한 방법에 대해 자세히 설명 합니다. 가격 책정 및 청구에 대 한 자세한 내용은 [AZURE AD 가격 책정 페이지](https://azure.microsoft.com/pricing/details/active-directory/)를 참조 하세요.

## <a name="compare-editions-and-features"></a>버전 및 기능 비교

SSPR는 사용자별로 사용이 허가 됩니다. 규정 준수를 유지 하기 위해 조직에서는 사용자에 게 적절 한 라이선스를 할당 해야 합니다.

다음 표에서는 암호 변경, 다시 설정 또는 온-프레미스 쓰기 저장에 대 한 다양 한 SSPR 시나리오와 기능을 제공 하는 Sku를 간략하게 설명 합니다.

| 기능 | Azure AD Free | Microsoft 365 Business Standard | Microsoft 365 Business Premium | Azure AD Premium P1 또는 P2 |
| --- |:---:|:---:|:---:|:---:|
| **클라우드 전용 사용자 암호 변경**<br />Azure AD의 사용자가 암호를 알고 있으며 새 항목으로 변경 하려고 합니다. | ● | ● | ● | ● |
| **클라우드 전용 사용자 암호 재설정**<br />Azure AD의 사용자가 암호를 잊어버린 경우 다시 설정 해야 합니다. | | ● | ● | ● |
| **온-프레미스 쓰기 저장을 사용 하 여 하이브리드 사용자 암호 변경 또는 다시 설정**<br />Azure AD Connect를 사용 하 여 온-프레미스 디렉터리에서 동기화 되는 Azure AD 사용자가 암호를 변경 하거나 다시 설정 하 고 새 암호를 온-프레미스에 다시 쓸 수 있습니다. | | | ● | ● |

> [!WARNING]
> 독립 실행형 Microsoft 365 기본 및 표준 라이선스 계획은 온-프레미스 쓰기 저장을 사용한 SSPR 지원 하지 않습니다. 온-프레미스 쓰기 저장 기능을 사용 하려면 Azure AD Premium P1, Premium P2 또는 Microsoft 365 Business Premium이 필요 합니다.

비용을 비롯 한 추가 라이선스 정보는 다음 페이지를 참조 하세요.

* [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory 기능 및 특성](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>그룹 또는 사용자 기반 라이선스 사용

Azure AD는 그룹 기반 라이선스를 지원 합니다. 관리자는 라이선스를 한 번에 하나씩 할당하는 대신 사용자 그룹에 한꺼번에 라이선스를 할당할 수 있습니다. 자세한 내용은 [라이선스 할당, 확인 및 문제 해결](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)을 참조하세요.

일부 Microsoft 서비스는 일부 위치 에서만 사용할 수 있습니다. 사용자에 게 라이선스를 할당 하려면 관리자가 해당 사용자에 대해 **사용 위치** 속성을 지정 해야 합니다. 라이선스 할당은 Azure Portal의 **사용자**  >  **프로필**  >  **설정** 섹션에서 수행할 수 있습니다. *그룹 라이선스 할당을 사용할 때 사용 위치가 지정되지 않은 사용자는 디렉터리의 위치를 상속합니다.*

## <a name="next-steps"></a>다음 단계

SSPR을 시작 하려면 다음 자습서를 완료 하세요.

> [!div class="nextstepaction"]
> [자습서: 셀프 서비스 암호 재설정 사용 (SSPR)](tutorial-enable-sspr.md)