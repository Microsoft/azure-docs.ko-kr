---
title: Azure Red Hat OpenShift에 대 한 Azure AD 테넌트 만들기
description: Microsoft Azure Red Hat OpenShift 클러스터를 호스트 하는 Azure Active Directory (Azure AD) 테넌트를 만드는 방법은 다음과 같습니다.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76270461"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift에 대 한 Azure AD 테넌트 만들기

Red Hat OpenShift를 Microsoft Azure 클러스터를 만들 [Azure Active Directory (AZURE AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 테넌트가 필요 합니다. *테넌트는* azure, Microsoft Intune 또는 Microsoft 365에 등록 하 여 Microsoft와의 관계를 만들 때 조직 또는 앱 개발자가 받는 azure AD의 전용 인스턴스입니다. 각 Azure AD 테넌트는 서로 다르며 다른 Azure AD 테넌트와 별개 이며 고유한 회사 및 학교 id와 앱 등록을 포함 합니다.

Azure AD 테넌트가 아직 없는 경우 다음 지침에 따라 새로 만듭니다.

## <a name="create-a-new-azure-ad-tenant"></a>새 Azure AD 테넌트 만들기

테넌트를 만들려면:

1. Azure Red Hat OpenShift 클러스터와 연결 하려는 계정을 사용 하 여 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.
2. [Azure Active Directory 블레이드](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) 를 열어 새 테넌트 (새 *Azure Active Directory*라고도 함)를 만듭니다.
3. **조직 이름을**제공 합니다.
4. **초기 도메인 이름을**제공 하십시오. 그러면 *onmicrosoft.com* 이 추가 됩니다. 여기에서 *조직 이름* 에 대 한 값을 다시 사용할 수 있습니다.
5. 테넌트가 생성 되는 국가 또는 지역을 선택 합니다.
6. **만들기**를 클릭합니다.
7. Azure AD 테넌트를 만든 후 **새 디렉터리를 관리 하려면 여기를 클릭** 하세요. 링크를 선택 합니다. Azure Portal의 오른쪽 위에 새 테넌트 이름이 표시 됩니다.  

    ![오른쪽 위에서 테넌트 이름을 보여 주는 포털의 스크린샷][tenantcallout]  

8. 나중에 Azure Red Hat OpenShift 클러스터를 만들 위치를 지정할 수 있도록 *테넌트 ID* 를 기록해 둡니다. 이제 포털에 새 테넌트에 대 한 Azure Active Directory 개요 블레이드가 표시 됩니다. **속성** 을 선택 하 고 **디렉터리 ID**의 값을 복사 합니다. [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md) 자습서에서 `TENANT`으로이 값을 참조 합니다.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>리소스

[AZURE AD 테넌트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)에 대 한 자세한 내용은 [Azure Active Directory 설명서](https://docs.microsoft.com/azure/active-directory/) 를 확인 하세요.

## <a name="next-steps"></a>다음 단계

서비스 주체를 만들고, 클라이언트 암호 및 인증 콜백 URL을 생성 하 고, Azure Red Hat OpenShift 클러스터에서 앱을 테스트 하기 위한 새 Active Directory 사용자를 만드는 방법에 대해 알아봅니다.

[Azure AD 앱 개체 및 사용자 만들기](howto-aad-app-configuration.md)
