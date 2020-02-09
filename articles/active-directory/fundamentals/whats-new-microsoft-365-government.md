---
title: Microsoft 365 정부에서 제공 되는 Azure AD의 새로운 기능 | Microsoft Docs
description: 사용자에 게 영향을 줄 수 있는 Microsoft 365 정부 클라우드 인스턴스에서 Azure Active Directory (Azure AD)에 대 한 몇 가지 변경 내용에 대해 알아봅니다.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75e7c6809810a78c81348bd4bbeb808e103f453f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805276"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Microsoft 365 정부에서 Azure Active Directory의 새로운 기능

다음 서비스를 사용 하는 고객에 게 적용 되는 Microsoft 365 정부 클라우드 인스턴스에서 Azure Active Directory (Azure AD)를 변경 했습니다.

- Microsoft Azure Government

- Microsoft 365 정부 – GCC High

- Microsoft 365 정부 – DoD

이 문서는 Microsoft 365 정부 – GCC 고객에 게는 적용 되지 않습니다.

## <a name="changes-to-the-initial-domain-name"></a>초기 도메인 이름에 대 한 변경 내용

Microsoft 365 정부 온라인 서비스에 대 한 조직의 초기 등록 중에 조직의 도메인 이름 `<your-domain-name>.onmicrosoft.com`을 선택 하 라는 메시지가 표시 됩니다. .Com 접미사가 있는 도메인 이름이 이미 있는 경우 아무 것도 변경 되지 않습니다.

그러나 새 Microsoft 365 정부 서비스에 등록 하는 경우 `.us` 접미사를 사용 하 여 도메인 이름을 선택 하 라는 메시지가 표시 됩니다. 따라서 `<your-domain-name>.onmicrosoft.us`됩니다.

>[!Note]
>이 변경 내용은 Csp (클라우드 서비스 공급자)로 관리 되는 고객에 게는 적용 되지 않습니다.

## <a name="changes-to-portal-access"></a>포털 액세스에 대 한 변경 내용

[엔드포인트 매핑 테이블](#endpoint-mapping)에 표시 된 것 처럼 Microsoft Azure Government, Microsoft 365 정부 – GCC High 및 Microsoft 365 정부 – DoD에 대 한 포털 엔드포인트을 업데이트 했습니다.

이전 고객은 전 세계 Azure (portal.azure.com) 및 Office 365 (portal.office.com) 포털을 사용 하 여 로그인 할 수 있었습니다. 이 업데이트를 사용 하 여 고객은 이제 특정 Microsoft Azure Government, Microsoft 365 정부 GCC High 및 Microsoft 365 정부-DoD 포털을 사용 하 여 로그인 해야 합니다.

## <a name="endpoint-mapping"></a>엔드포인트 매핑

다음 표에서는 모든 고객에 대 한 엔드포인트을 보여 줍니다.

| 이름 | 엔드포인트 세부 정보 |
|------|------------------|
| 포털 |Microsoft Azure Government: https://portal.azure.us<p>Microsoft 365 정부 – GCC High: https://portal.office365.us<p>Microsoft 365 정부 – DoD: https://portal.apps.mil |
| Azure Active Directory 기관 엔드포인트 | https://login.microsoftonline.us |
| Azure Active Directory Graph API | https://graph.windows.net |
| Microsoft 365 정부에 대 한 Microsoft Graph API-GCC High | https://graph.microsoft.us |
| Microsoft 365 정부용 Microsoft Graph API-DoD | https://dod-graph.microsoft.us |
| Azure Government services 엔드포인트 | 자세한 내용은 [Azure Government 개발자 가이드](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) 를 참조 하세요. |
| 정부 GCC 고급 엔드포인트 Microsoft 365 | 자세한 내용은 [Office 365 미국 정부 GCC 높은 엔드포인트](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) 을 참조 하세요. |
| Microsoft 365 Government - DoD | 자세한 내용은 [Office 365 미국 정부 DoD 엔드포인트](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 을 참조 하세요. |

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure Government란?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Azure Government AAD 기관 엔드포인트 업데이트](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [미국 정부 클라우드의 Microsoft Graph 엔드포인트](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 미국 정부 GCC High 및 DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)