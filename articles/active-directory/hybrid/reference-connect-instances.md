---
title: 'Azure AD Connect: 서비스 인스턴스 동기화 | Microsoft Docs'
description: 이 페이지에서는 Azure AD 인스턴스에 대한 특별한 고려 사항을 문서화합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca481d50efb99d6e36c66388192e9f27cd66bf45
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096099"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: 인스턴스에 대한 특별 고려 사항
Azure AD Connect는 Azure AD 및 Office 365의 전세계 인스턴스로 가장 많이 사용됩니다. 그러나 다른 인스턴스도 있고 URL 및 기타 특별한 고려 사항에 대한 다른 요구 사항을 포함합니다.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud 독일
[Microsoft Cloud 독일](https://www.microsoft.de/cloud-deutschland) 은 독일 데이터 트러스티에서 운영하는 독립 클라우드입니다.

| 프록시 서버에서 열린 URL |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +인증서 해지 목록 |

Azure AD 테넌트에 로그인할 경우 onmicrosoft.de 도메인의 계정을 사용해야 합니다.

Microsoft Cloud 독일에 현재 표시되지 않는 기능은 다음과 같습니다.

* **비밀번호 쓰기 저장**은 Azure AD Connect 버전 1.1.570.0 이상에서 미리 보기로 제공됩니다.
* 다른 Azure AD Premium 서비스를 사용할 수 없습니다.

## <a name="microsoft-azure-government-cloud"></a>Microsoft Azure Government 클라우드
[Microsoft Azure Government 클라우드](https://azure.microsoft.com/features/gov/) 는 미국 정부를 위한 클라우드입니다.

이 클라우드는 DirSync의 이전 버전에서 지원되어 왔습니다. Azure AD Connect의 1.1.180 빌드에서 차세대 클라우드가 지원됩니다. 이 차세대 클라우드는 미국 전용 기반 엔드포인트를 사용하고 프록시 서버에서 열린 URL의 다른 목록이 있습니다.

| 프록시 서버에서 열린 URL |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.windows.net(자동 Azure AD 정부 테넌트 검색에 필수) |
| \*.gov.us.microsoftonline.com |
| +인증서 해지 목록 |

> [!NOTE]
> AAD Connect 버전 1.1.647.0의 경우 *.windows.net이 프록시 서버에 열려 있으므로 더 이상 레지스트리에서 AzureInstance 값을 설정할 필요가 없습니다.

Microsoft Azure Government 클라우드에 현재 표시되지 않는 기능은 다음과 같습니다.

* **비밀번호 쓰기 저장**은 Azure AD Connect 버전 1.1.570.0 이상에서 미리 보기로 제공됩니다.
* 다른 Azure AD Premium 서비스를 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
