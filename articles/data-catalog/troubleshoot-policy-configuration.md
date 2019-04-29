---
title: Azure Data Catalog에 대 한 Azure Active Directory 정책을 구성 하는 방법
description: 하지만 데이터 원본 등록 도구에 로그인 하려고 할 때 Azure Data Catalog 포털에 로그인 할 수 있는 상황이 발생할 수 있습니다, 오류 메시지가 발생 합니다.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/06/2019
ms.openlocfilehash: e69a7e3bd104d0fb82b248b6560d4fd082c88426
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116604"
---
# <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory 정책 구성

Azure Data Catalog 포털에 로그인할 수 있는 상황이 발생할 수 있지만, 데이터 원본 등록 도구에 로그인을 시도할 때 로그인하지 않도록 하는 오류 메시지가 발생합니다. 이 오류는 회사 네트워크에 있는 경우 또는 회사 네트워크 외부에서 연결 하는 경우에 발생할 수 있습니다.

## <a name="registration-tool"></a>등록 도구

등록 도구가 *폼 인증* 을 사용하여 Azure Active Directory에 대한 사용자 로그인의 유효성 검사를 수행합니다. 로그인이 성공하려면 Azure Active Directory 관리자가 *전역 인증 정책*에서 폼 인증을 사용할 수 있도록 해야 합니다.

전역 인증 정책을 사용하면 다음 이미지에 나와 있는 것처럼 인트라넷 및 엑스트라넷 연결에 대해 개별 인증을 사용하도록 설정할 수 있습니다. 폼 인증 연결 되어 있는 네트워크에 사용할 수 없는 경우 로그인 오류가 발생할 수 있습니다.

 ![Azure Active Directory 전역 인증 정책](./media/troubleshoot-policy-configuration/global-auth-policy.png)

자세한 내용은 [인증 정책 구성](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11))을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 카탈로그를 만들려면](data-catalog-get-started.md)