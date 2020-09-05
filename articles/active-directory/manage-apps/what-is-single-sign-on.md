---
title: Azure SSO(Single Sign-On)란?
description: Azure Active Directory에서 SSO(Single Sign-On)가 작동하는 방법에 대해 알아봅니다. 사용자가 모든 애플리케이션에 대한 암호를 기억할 필요가 없도록 SSO를 사용합니다. 또한 SSO를 사용하여 계정 관리를 간소화합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.custom: contperfq1
ms.openlocfilehash: 6c7ac840dfba665c4691fc4d389d66f44b077bf7
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069472"
---
# <a name="what-is-single-sign-on-sso"></a>SSO(Single Sign-On)란?

Single Sign-On은 사용자가 사용하는 모든 애플리케이션에 로그인할 필요가 없음을 의미합니다. 사용자가 한 번 로그인하면 다른 앱에도 해당 자격 증명이 사용됩니다.

최종 사용자인 경우 SSO 세부 정보에 대해 크게 신경 쓰지 않을 수 있습니다. 암호를 입력하지 않고도 생산적으로 만드는 앱을 사용하려고 합니다. https://myapps.microsoft.com 에서 앱을 찾을 수 있습니다.
 
관리자 또는 IT 전문가인 경우 SSO가 Azure에서 구현되는 방법에 대해 자세히 알아보세요.

## <a name="single-sign-on-basics"></a>Single Sign-On 기본 사항
Single Sign-On은 사용자가 로그인하고 애플리케이션을 사용하는 방법에 큰 도약을 제공합니다. Single Sign-On 기반 인증 시스템을 종종 "최신 인증"이라고 합니다. 최신 인증 및 Single Sign-On은 IAM(ID 및 액세스 관리)이라는 컴퓨팅 범주에 속합니다. Single Sign-On의 가능 여부를 이해하려면 이 비디오를 확인하세요.

인증 기본 사항: 기본 사항 | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="single-sign-on-with-web-applications"></a>웹 애플리케이션을 사용하는 Single Sign-On
웹 애플리케이션은 매우 인기가 있습니다. 웹앱은 다양한 회사에서 호스팅되며 서비스로 제공됩니다. 웹앱의 몇 가지 인기 있는 예제에는 Microsoft 365, GitHub 및 Salesforce가 있으며, 그 외에도 수천 개가 있습니다. 사용자는 자신의 컴퓨터에서 웹 브라우저를 사용하여 웹앱에 액세스합니다. Single Sign-On을 사용하면 사용자가 여러 번 로그인하지 않고도 다양한 웹앱을 탐색할 수 있습니다.

웹앱에서 Single Sign-On이 작동하는 방법에 대해 알아보려면 이 두 비디오를 확인하세요.

인증 기본 사항: 웹 애플리케이션 | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/tCNcG1lcCHY]

인증 기본 사항: 웹 Single Sign-On | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/51B-jSOBF8U]

## <a name="cloud-versus-on-premises-hosted-apps"></a>클라우드 및 온-프레미스에서 호스트되는 앱
Single Sign-On을 구현하는 방법은 앱이 호스트되는 위치에 따라 달라집니다. 앱에 액세스하기 위해 네트워크 트래픽이 라우팅되는 방식 때문에 호스팅에 문제가 있습니다. 앱이 호스트되고 온-프레미스 앱이라고 하는 로컬 네트워크를 통해 액세스되는 경우 사용자가 인터넷에 액세스하여 앱을 사용할 필요가 없습니다. 앱이 클라우드 호스팅 앱이라고 하는 다른 곳에서 호스트되는 경우 사용자는 앱을 사용하기 위해 인터넷에 액세스해야 합니다.

> [!TIP]
> 클라우드 호스팅 앱은 SaaS(Software as a Service) 앱이라고도 합니다. 

클라우드 호스팅 앱에 대한 Single Sign-On은 간단합니다. ID 공급자에게 앱에 사용되고 있음을 알립니다. 그런 다음, ID 공급자를 신뢰하도록 앱을 구성합니다. Azure AD를 앱의 ID 공급자로 사용하는 방법을 알아보려면 [애플리케이션 관리에 대한 빠른 시작 시리즈](view-applications-portal.md)를 참조하세요.

> [!TIP]
> 클라우드 및 인터넷이라는 용어는 종종 교환 가능한 방식으로 사용됩니다. 그 이유는 네트워크 다이어그램과 관련이 있습니다. 모든 구성 요소를 그리는 것은 불가능하기 때문에 다이어그램에서 클라우드 도형이 포함된 대형 컴퓨터 네트워크를 나타내는 것이 일반적입니다. 인터넷은 가장 잘 알려진 네트워크이므로 상호 교환이 쉽습니다. 그러나 모든 컴퓨터 네트워크를 클라우드로 연결할 수 있습니다.

온-프레미스 기반 앱에 Single Sign-On을 사용할 수도 있습니다. 온-프레미스 SSO를 수행하는 기술을 애플리케이션 프록시라고 합니다. 자세히 알아보려면 [Single Sign-On 옵션](sso-options.md)을 참조하세요.

## <a name="multiple-identity-providers"></a>여러 ID 공급자
여러 ID 공급자 간에 작업하도록 Single Sign-On을 설정하는 경우 이를 페더레이션이라고 합니다. 페더레이션이 작동하는 방법을 알아보려면 이 비디오를 확인하세요.

인증 기본 사항: 페더레이션 | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]


## <a name="next-steps"></a>다음 단계
* [애플리케이션 관리에 대한 빠른 시작 시리즈](view-applications-portal.md)
* [Single Sign-On 옵션](sso-options.md)
