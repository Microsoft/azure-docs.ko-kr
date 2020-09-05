---
title: 응용 프로그램에 대 한 최종 사용자 환경-Azure Active Directory
description: Azure AD(Azure Active Directory)는 조직의 최종 사용자에게 애플리케이션을 배포하는 몇 가지 사용자 지정 가능한 방법을 제공합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.reviewer: arvindh
ms.openlocfilehash: d3b7dbbb9aab29e083795025ad5bf7381a4fa0ee
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231217"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Azure Active Directory에서 애플리케이션에 대한 최종 사용자 환경

Azure AD(Azure Active Directory)는 조직의 최종 사용자에게 애플리케이션을 배포하는 몇 가지 사용자 지정 가능한 방법을 제공합니다.

* Azure AD 내 앱
* Office 365 애플리케이션 실행 프로그램
* 페더레이션된 앱에 직접 로그온
* 페더레이션된 앱, 암호로 보호된 앱 또는 기존 앱에 대한 딥 링크

조직에 배포할 방법을 선택하는 것은 여러분의 몫입니다.

## <a name="azure-ad-my-apps"></a>Azure AD 내 앱

에서 내 앱 https://myapps.microsoft.com 은 최종 사용자가 Azure Active Directory의 조직 계정을 사용 하 여 AZURE AD 관리자가 액세스 권한을 부여 받은 응용 프로그램을 보고 시작할 수 있도록 하는 웹 기반 포털입니다. [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/)를 사용 하는 최종 사용자 인 경우 내 앱을 통해 셀프 서비스 그룹 관리 기능을 활용할 수도 있습니다.

기본적으로 모든 응용 프로그램은 단일 페이지에 함께 나열 됩니다. 그러나 컬렉션을 사용 하 여 관련 응용 프로그램을 함께 그룹화 하 고 별도의 탭에 표시 하 여 쉽게 찾을 수 있습니다. 예를 들어 컬렉션을 사용 하 여 특정 작업 역할, 작업, 프로젝트 등에 대해 응용 프로그램의 논리적 그룹을 만들 수 있습니다. 자세한 내용은 [My Apps 포털에서 컬렉션 만들기](access-panel-collections.md)를 참조 하세요. 

내 앱은 Azure Portal와 별개 이며, 사용자에 게 Azure 구독 또는 Office 365 구독이 필요 하지 않습니다.

Azure AD 내 앱에 대 한 자세한 내용은 [내 앱 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조 하세요.

## <a name="office-365-application-launcher"></a>Office 365 애플리케이션 실행 프로그램

Office 365을 배포한 조직의 경우 Azure AD를 통해 사용자에 게 할당 된 응용 프로그램은 Office 365 포털 ()에도 표시 됩니다 [https://portal.office.com/myapps](https://portal.office.com/myapps) . 이렇게 하면 조직의 사용자가 두 번째 포털을 사용하지 않고도 해당 앱을 쉽고 편리하게 시작할 수 있으므로 Office 365를 사용하는 조직에 권장되는 앱 시작 솔루션입니다.

Office 365 애플리케이션 실행 프로그램에 대한 자세한 내용은 [Office 365 앱 실행 프로그램에 해당 앱 표시](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher)(영문)를 참조하세요.

## <a name="direct-sign-on-to-federated-apps"></a>페더레이션된 앱에 직접 로그온

SAML 2.0, WS-Federation 또는 OpenID Connect를 지원하는 대부분의 페더레이션된 애플리케이션은 사용자가 애플리케이션에서 시작한 다음, 자동 리디렉션을 통하거나 로그인 링크를 클릭하여 Azure AD로 로그인하는 기능을 지원합니다. 이를 서비스 공급자가 시작한 로그온 이라고 하며, Azure AD 응용 프로그램 갤러리에서 대부분의 페더레이션 응용 프로그램은이를 지원 합니다 (자세한 내용은 Azure Portal의 앱 Single Sign-On 구성 마법사에서 연결 된 설명서 참조).

## <a name="direct-sign-on-links"></a>직접 로그온 링크

Azure AD는 암호 기반 Single Sign-On, 연결된 Single Sign-On 및 모든 형태의 페더레이션된 Single Sign-On을 지원하는 개별 애플리케이션에 대한 직접 Single Sign-On 링크도 지원합니다.

이러한 링크는 사용자가 Azure AD 내 앱 또는 Office 365에서 시작 하지 않고도 특정 응용 프로그램에 대 한 Azure AD 로그인 프로세스를 통해 사용자를 전송 하는 특별히 제작 된 Url입니다. 이러한 **사용자 액세스 url** 은 사용 가능한 엔터프라이즈 응용 프로그램의 속성에서 찾을 수 있습니다. Azure Portal에서 **Azure Active Directory** > **엔터프라이즈 애플리케이션**을 차례로 선택합니다. 응용 프로그램을 선택한 다음 **속성**을 선택 합니다.

![Twitter 속성의 사용자 액세스 URL 예](media/end-user-experiences/direct-sign-on-link.png)

이러한 링크는 복사하여 선택한 애플리케이션에 대한 로그인 링크를 제공할 위치에 붙여 넣을 수 있습니다. 이 링크는 전자 메일 또는 사용자 애플리케이션 액세스에 대해 설정한 사용자 지정 웹 기반 포털에 있을 수 있습니다. 다음은 Twitter에 대한 Azure AD Single Sign-On URL의 예제입니다.

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

내 앱에 대 한 조직 특정 Url과 마찬가지로 *myapps.microsoft.com* 도메인 뒤에 디렉터리에 대 한 활성 또는 확인 된 도메인 중 하나를 추가 하 여이 url을 추가로 사용자 지정할 수 있습니다. 이렇게 하면 사용자가 자신의 ID를 먼저 입력하지 않아도 조직 브랜딩이 로그인 페이지에 즉시 로드됩니다.

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

권한 있는 사용자가 이러한 응용 프로그램 관련 링크 중 하나를 클릭 하면 먼저 조직 로그인 페이지 (아직 로그인 하지 않은 것으로 가정)가 표시 되 고 로그인이 앱에서 먼저 중지 하지 않고 해당 앱으로 리디렉션됩니다. 사용자에게 암호 기반 Single Sign-On 브라우저 확장과 같은, 애플리케이션에 액세스하기 위한 필수 구성 요소가 없는 경우 확장을 설치할 것인지 묻는 링크가 표시됩니다. 애플리케이션에 대한 Single Sign-On 구성이 변경되는 경우에도 링크 URL은 그대로 유지됩니다.

이러한 링크는 내 앱 및 Office 365와 동일한 액세스 제어 메커니즘을 사용 하며, Azure Portal 응용 프로그램에 할당 된 사용자 또는 그룹만 성공적으로 인증할 수 있습니다. 그러나 권한이 없는 사용자에 게는 액세스 권한이 부여 되지 않은 것을 나타내는 메시지가 표시 되 고, 앱을 로드 하 여 액세스 권한이 있는 사용 가능한 응용 프로그램을 볼 수 있는 링크가 제공 됩니다.

## <a name="next-steps"></a>다음 단계

* [애플리케이션 관리에 대한 빠른 시작 시리즈](view-applications-portal.md)
* [Single Sign-On 이란?](what-is-single-sign-on.md)
* [애플리케이션과 Azure Active Directory 통합 시작 가이드](plan-an-application-integration.md)
