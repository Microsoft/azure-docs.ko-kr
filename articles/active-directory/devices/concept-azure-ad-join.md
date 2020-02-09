---
title: Azure AD 조인 장치 란?
description: 디바이스 ID 관리가 사용자 환경의 리소스에 액세스하는 디바이스를 관리하는 데 어떻게 도움이 되는지 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24ec4373bceb3cc3c9e5be2c7a0dab1f62197b3c
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512201"
---
# <a name="azure-ad-joined-devices"></a>Azure AD 조인 디바이스

Azure AD 조인은 클라우드 우선 또는 클라우드 전용으로 사용 하려는 조직을 위해 작성 되었습니다. 모든 조직은 크기 또는 업계에 관계 없이 Azure AD 조인 장치를 배포할 수 있습니다. Azure AD 조인은 클라우드 및 온-프레미스 앱과 리소스에 모두 액세스할 수 있도록 하는 하이브리드 환경 에서도 작동 합니다.

|   | Azure AD 조인 |
| --- | --- |
| **정의** | 조직 계정이 장치에 로그인 하는 데 필요한 Azure AD에만 가입 됨 |
| **기본 대상** | 클라우드 전용 및 하이브리드 조직 모두에 적합 합니다. |
|   | 조직의 모든 사용자에 게 적용 가능 |
| **장치 소유권** | 조직 |
| **운영 체제** | 모든 Windows 10 장치 |
| **프로비전** | 셀프 서비스: Windows OOBE 또는 설정 |
|   | 대량 등록 |
|   | Windows Autopilot |
| **장치 로그인 옵션** | 다음을 사용 하는 조직 계정: |
|   | 암호 |
|   | 비즈니스용 Windows Hello |
|   | FIDO 2.0 보안 키 (미리 보기) |
| **디바이스 관리** | 모바일 장치 관리 (예: Microsoft Intune) |
|   | Microsoft Intune 및 Microsoft 엔드포인트을 사용 하 여 공동 관리 Configuration Manager |
| **주요 기능** | 클라우드 및 온-프레미스 리소스 모두에 SSO |
|   | MDM 등록 및 MDM 준수 평가를 통한 조건부 액세스 |
|   | 잠금 화면에서 셀프 서비스 암호 재설정 및 Windows Hello PIN 다시 설정 |
|   | 장치에서 Enterprise State Roaming |

Azure AD 조인 장치는 조직의 Azure AD 계정을 사용 하 여 로그인 됩니다. 조직의 리소스에 대 한 액세스는 해당 Azure AD 계정 및 장치 id에 적용 되는 [조건부 액세스 정책](../conditional-access/overview.md) 에 따라 추가로 제한할 수 있습니다.

관리자는 Microsoft Intune와 같은 MDM (모바일 장치 관리) 도구를 사용 하 여 Azure AD 조인 장치를 안전 하 게 제어 하거나 Microsoft 엔드포인트 Configuration Manager를 사용 하는 공동 관리 시나리오를 수행할 수 있습니다. 이러한 도구는 저장소 암호화, 암호 복잡성, 소프트웨어 설치 및 소프트웨어 업데이트와 같은 조직에서 필요한 구성을 적용 하는 수단을 제공 합니다. 관리자는 Configuration Manager를 사용 하 여 Azure AD 조인 장치에서 조직 응용 프로그램을 사용할 수 있도록 하 여 [비즈니스 및 교육용 Microsoft Store에서 앱을 관리할](https://docs.microsoft.com/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business)수 있습니다.

OOBE (기본 제공 경험), 대량 등록 또는 [Windows Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot)와 같은 셀프 서비스 옵션을 사용 하 여 Azure AD 조인을 수행할 수 있습니다.

Azure AD 가입 장치는 조직의 네트워크에 있는 경우 온-프레미스 리소스에 대 한 Single Sign-On 액세스를 계속 유지할 수 있습니다. Azure AD에 가입 된 장치는 여전히 파일, 인쇄 및 기타 응용 프로그램과 같은 온-프레미스 서버에 인증할 수 있습니다.

## <a name="scenarios"></a>시나리오

Azure AD 가입은 기본적으로 온-프레미스 Windows Server Active Directory 인프라가 없는 조직을 위해 고안되었으나, 다음과 같은 시나리오에서 확실히 사용할 수 있습니다.

- Azure AD 및 Intune 같은 MDM을 사용하여 클라우드 기반 인프라로 전환하려 합니다.
- 온-프레미스 도메인 가입을 사용할 수 없습니다. 예를 들어 제어되는 태블릿 및 휴대폰과 같은 모바일 디바이스가 필요한 경우입니다.
- 사용자가 기본적으로 Office 365 또는 Azure AD와 통합된 다른 SaaS 앱에 액세스해야 합니다.
- Active Directory 대신 Azure AD에서 사용자 그룹을 관리하고자 합니다. 이 시나리오는 예를 들어 계절 작업자, 계약자 또는 학생에 게 적용할 수 있습니다.
- 제한된 온-프레미스 인프라를 사용하는 원격 지사에서 작업자에게 조인 기능을 제공하려고 합니다.

Windows 10 디바이스에 대한 Azure AD 가입 디바이스를 구성할 수 있습니다.

Azure AD 가입 디바이스의 목표는 단순화하는 것입니다.

- 회사 소유 디바이스의 Windows 배포
- 모든 Windows 디바이스에서 조직의 앱 및 리소스에 액세스
- 회사 소유 디바이스의 클라우드 기반 관리
- 사용자가 Azure AD를 사용 하 여 장치에 로그인 하거나 회사 또는 학교 계정 Active Directory 동기화 할 수 있습니다.

![Azure AD 조인 디바이스](./media/concept-azure-ad-join/azure-ad-joined-device.png)

다음 방법 중 하나를 사용하여 Azure AD Join을 배포할 수 있습니다.

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
- [대량 배포](https://docs.microsoft.com/intune/windows-bulk-enroll)
- [셀프 서비스 환경](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>다음 단계

- [Azure AD 조인 구현 계획](azureadjoin-plan.md)
- [Azure AD 조인 장치에서 로컬 관리자 그룹을 관리 하는 방법](assign-local-admin.md)
- [Azure Portal를 사용 하 여 장치 id 관리](device-management-azure-portal.md)
- [Azure AD에서 오래 된 장치 관리](manage-stale-devices.md)
