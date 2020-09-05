---
title: Privileged Identity Management의 Azure AD 역할 기능 | Microsoft Docs
description: PIM (할당 Privileged Identity Management)에 대 한 Azure AD 역할을 관리 하는 방법
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 07/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c039842a04923bc02aa288576570d51c39156c40
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88784014"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할에 대 한 관리 기능

Privileged Identity Management에서 Azure AD 역할에 대 한 관리 환경은 Azure AD 역할 및 Azure 리소스 역할을 관리 하는 방법을 통합 하도록 업데이트 되었습니다. 이전에는 Azure 리소스 역할에 대 한 Privileged Identity Management Azure AD 역할에 사용할 수 없는 몇 가지 주요 기능을가지고 있습니다.

현재 출시 되는 업데이트의 경우 두 가지를 단일 관리 환경으로 병합 하 고 azure 리소스 역할과 마찬가지로 Azure AD 역할에 대해 동일한 기능을 제공 합니다. 이 문서에서는 업데이트 된 기능 및 요구 사항을 알려 줍니다.

## <a name="time-bound-assignments"></a>시간 범위 할당

이전에는 역할 할당에 대 한 두 가지 가능한 상태 ( *적격* 및 *영구*)가 있었습니다. 이제 각 할당 형식에 대 한 시작 및 종료 시간을 설정할 수도 있습니다. 이러한 추가 기능을 통해 할당을 저장할 수 있는 네 가지 상태를 확인할 수 있습니다.

- 영구적으로 사용할 수 있음
- 영구적으로 활성
- 지정 된 시작 및 종료 날짜가 할당 된 적격
- 지정 된 시작 및 종료 날짜가 할당 된 활성

대부분의 경우에는 사용자에 게 적격 할당을 설정 하 고 매번 역할을 활성화 하는 것을 원하지 않는 경우에도 할당에 대 한 만료 시간을 설정 하 여 Azure AD 조직을 보호할 수 있습니다. 예를 들어 적격 한 임시 사용자가 있는 경우 작업이 완료 될 때 역할 할당에서 자동으로 제거 되도록 만료를 설정 하는 것이 좋습니다.

## <a name="new-role-settings"></a>새 역할 설정

또한 Azure AD 역할에 대 한 새로운 설정을 추가 합니다.

- **이전**에는 역할 별로 활성화 설정만 구성할 수 있었습니다. 즉, 다단계 인증 요구 사항 및 인시던트/요청 티켓 요구 사항과 같은 정품 인증 설정이 지정 된 역할에 적합 한 모든 사용자에 게 적용 되었습니다.
- **이제**개별 사용자가 multi-factor authentication을 수행 해야 역할을 활성화할 수 있는지 여부를 구성할 수 있습니다. 또한 특정 역할과 관련 된 Privileged Identity Management 전자 메일을 고급 제어할 수 있습니다.

## <a name="extend-and-renew-assignments"></a>할당 확장 및 갱신

시간 제한 할당을 확인 하는 즉시, 역할이 만료 된 경우에는 무엇을 해야 하나요? 이 새 버전에서는 이 시나리오에 대한 다음 두 가지 옵션이 제공됩니다.

- **확장**: 역할 할당이 만료 된 경우 사용자가 Privileged Identity Management를 사용 하 여 해당 역할 할당에 대 한 확장을 요청할 수 있습니다.
- **갱신**: 역할 할당이 만료 된 경우 사용자가 Privileged Identity Management를 사용 하 여 해당 역할 할당에 대 한 갱신을 요청할 수 있습니다.

사용자가 시작하는 두 작업은 글로벌 관리자 또는 권한 있는 역할 관리자의 승인이 필요합니다. 관리자는 더 이상 만료를 관리하는 업무를 수행할 필요가 없습니다. 확장 또는 갱신 요청을 기다리고 요청이 유효한 경우 해당 요청을 승인 하기만 하면 됩니다.

## <a name="api-changes"></a>API 변경 내용

고객이 Azure AD 조직에 업데이트 된 버전을 롤아웃할 때 기존 graph API의 작동이 중지 됩니다. [Azure 리소스 역할에 대 한 Graph API](/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta)를 사용 하려면로 전환 해야 합니다. 해당 API를 사용 하 여 Azure AD 역할을 관리 하려면 `/azureResources` `/aadroles` 서명에서로 바꾸고의 디렉터리 ID를 사용 합니다 `resourceId` .

이전 API를 사용 하는 모든 고객에 게 연락 하 여 이러한 변경 사항을 미리 파악 하는 것이 가장 좋습니다. Azure AD 조직이 새 버전으로 이동 했 고 여전히 이전 API에 의존 하는 경우의 팀에 문의 pim_preview@microsoft.com 하세요.

## <a name="powershell-change"></a>PowerShell 변경

Azure AD 역할에 Privileged Identity Management PowerShell 모듈을 사용 하는 고객의 경우 PowerShell에서 업데이트 작업을 중지 합니다. 이전 cmdlet 대신 Azure AD Preview PowerShell 모듈 내에서 Privileged Identity Management cmdlet을 사용 해야 합니다. [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)에서 Azure AD PowerShell 모듈을 설치 합니다. 이제 [이 PowerShell 모듈에서 PIM 작업에 대 한 설명서와 샘플을 읽을](powershell-for-azure-ad-roles.md)수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 사용자 지정 역할 할당](azure-ad-custom-roles-assign.md)
- [Azure AD 사용자 지정 역할 할당 제거 또는 업데이트](azure-ad-custom-roles-update-remove.md)
- [Azure AD 사용자 지정 역할 할당 구성](azure-ad-custom-roles-configure.md)
- [Azure AD의 역할 정의](../users-groups-roles/directory-assign-admin-roles.md)