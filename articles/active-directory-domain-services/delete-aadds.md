---
title: Azure Active Directory Domain Services 삭제 | Microsoft Docs
description: 을 사용 하 여 관리 되는 Azure Active Directory Domain Services 도메인을 사용 하지 않도록 설정 하거나 삭제 하는 방법을 알아봅니다 Azure Portal
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 993a73a42887b3869c097d14b6945a1093ac6889
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723064"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Active Directory Domain Services 관리 되는 도메인 삭제

Azure Active Directory Domain Services (Azure AD DS) 관리 도메인이 더 이상 필요 하지 않은 경우 삭제할 수 있습니다. Azure AD DS 관리 되는 도메인을 끄거나 일시적으로 사용 하지 않도록 설정할 수 있는 옵션은 없습니다. 관리 되는 도메인을 삭제 해도 삭제 되거나 Azure AD 테 넌 트에 부정적인 영향을 줍니다.

이 문서에서는 Azure Portal를 사용 하 여 관리 되는 도메인을 삭제 하는 방법을 보여 줍니다.

> [!WARNING]
> **삭제는 영구적 이며 되돌릴 수 없습니다.**
> 
> 관리 되는 도메인을 삭제 하면 다음 단계가 수행 됩니다.
>   * 관리되는 도메인의 도메인 컨트롤러가 가상 네트워크에서 프로비전 해제되고 제거됩니다.
>   * 관리되는 도메인의 데이터가 영구적으로 삭제됩니다. 이 데이터에는 사용자가 만든 사용자 지정 Ou, Gpo, 사용자 지정 DNS 레코드, 서비스 사용자, GMSAs 등이 포함 됩니다.
>   * 관리되는 도메인에 가입된 컴퓨터는 도메인과의 트러스트 관계를 해제하고 도메인 가입을 해제해야 합니다.
>       * 회사 AD 자격 증명을 사용 하 여 이러한 컴퓨터에 로그인 할 수 없습니다. 대신, 컴퓨터에 대 한 로컬 관리자 자격 증명을 사용 해야 합니다.

## <a name="delete-the-managed-domain"></a>관리 되는 도메인 삭제

관리 되는 도메인을 삭제 하려면 다음 단계를 완료 합니다.

1. Azure Portal에서 **Azure AD Domain Services**를 검색하여 선택합니다.
1. 관리 되는 도메인의 이름 (예: *aaddscontoso.com*)을 선택 합니다.
1. **개요** 페이지에서 **삭제**를 선택합니다. 삭제를 확인 하려면 관리 되는 도메인의 도메인 이름을 다시 입력 하 고 **삭제**를 선택 합니다.

관리 되는 도메인을 삭제 하는 데 15-20 분 이상 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure AD DS에 표시 하려는 기능에 대 한 [피드백을 공유][feedback] 해 보세요.

Azure AD DS을 다시 시작 하려면 [관리 되는 Azure Active Directory Domain Services 도메인 만들기 및 구성][create-instance]을 참조 하세요.

<!-- INTERNAL LINKS -->
[feedback]: https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160593%3fcategory_id%3d160593
[create-instance]: tutorial-create-instance.md