---
title: Azure Static Web Apps란?
description: Azure Static Web Apps의 주요 특징 및 기능입니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 04/01/2021
ms.author: cshoe
ms.openlocfilehash: c0a4156fb0015c6d319700c82f6285dbf0ed1443
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107947882"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Azure Static Web Apps 미리 보기란?

Azure Static Web Apps는 코드 리포지토리에서 전체 스택 웹앱을 Azure에 자동으로 빌드하고 배포하는 서비스입니다.

:::image type="content" source="media/overview/azure-static-web-apps-overview.png" alt-text="Azure Static Web Apps 개요 다이어그램":::

Azure Static Web Apps의 워크플로는 개발자의 일상 워크플로에 맞게 조정됩니다. 앱은 코드 변경 내용을 기반으로 빌드되고 배포됩니다.

Azure Static Web Apps 리소스를 만들 때 Azure는 GitHub 또는 Azure DevOps와 직접 상호 작용하여 선택한 분기를 모니터링합니다. 커밋을 푸시하거나 감시된 분기로 끌어오기 요청을 수락할 때마다 빌드가 자동으로 실행되고 앱 및 API를 Azure에 배포합니다.

정적 웹앱은 일반적으로 서버쪽 렌더링이 필요하지 않은 Angular, React, Svelte, Vue 또는 Blazor 같은 라이브러리와 프레임워크를 사용하여 빌드됩니다. 이러한 앱에는 애플리케이션을 구성하는 HTML, CSS, JavaScript 및 이미지 자산이 포함됩니다. 기존 웹 서버를 사용하는 경우 이러한 자산은 필수 API 엔드포인트와 함께 단일 서버에서 제공됩니다.

Static Web Apps를 사용하면 정적 자산은 기존 웹 서버와 분리되고 대신 전 세계에 지리적으로 분산된 지점에서 제공됩니다. 이 배포는 파일이 물리적으로 최종 사용자에게 더 가까이 있으므로 파일을 훨씬 더 빠르게 제공합니다. 또한 API 엔드포인트는 [서버리스 아키텍처](../azure-functions/functions-overview.md)를 사용하여 호스팅되므로 전체 백 엔드 서버를 함께 사용하지 않아도 됩니다.

## <a name="key-features"></a>주요 기능

- HTML, CSS, JavaScript 및 이미지와 같은 정적 콘텐츠에 대한 **웹 호스팅** 입니다.
- Azure Functions에서 제공하는 **통합 API**.
- 리포지토리 변경 시 빌드 및 배포가 트리거되는 **최상급 GitHub 및 Azure DevOps 통합**
- **전역적으로 분산된** 정적 콘텐츠로, 사용자에게 더 가까운 콘텐츠를 제공합니다.
- 자동으로 갱신되는 **무료 SSL 인증서**.
- 앱에 브랜드 사용자 지정을 제공하는 **사용자 지정 도메인**.
- CORS 구성이 필요 없는 API를 호출할 때 역방향 프록시를 사용하는 **원활한 보안 모델**.
- Azure Active Directory, Facebook, Google, GitHub 및 Twitter와 **인증 공급자 통합**.
- **사용자 지정 가능한 권한 부여 역할 정의** 및 할당.
- **백 엔드 라우팅 규칙** 을 사용하여 사용자가 제공하는 콘텐츠와 경로를 완전히 제어할 수 있습니다.
- 게시하기 전에 사이트의 미리 보기 버전을 사용하도록 설정하는 끌어오기 요청으로 구동되는 **생성된 스테이징 버전**.

## <a name="what-you-can-do-with-static-web-apps"></a>Static Web Apps로 수행할 수 있는 작업

- [Azure Functions](apis.md) 백 엔드를 통해WebAssembly 애플리케이션을 만드는 [Angular](getting-started.md?tabs=angular), [React](getting-started.md?tabs=react), [Svelte](/learn/modules/publish-app-service-static-web-app-api/), [Vue](getting-started.md?tabs=vue), [Blazor](./deploy-blazor.md) 같은 JavaScript 프레임워크와 라이브러리를 사용하여 **최신 웹 애플리케이션을 빌드** 합니다.
- [Gatsby](publish-gatsby.md), [Hugo](publish-hugo.md), [VuePress](publish-vuepress.md)와 같은 프레임워크를 사용하여 **정적 사이트를 게시** 합니다.
- [Next.js](deploy-nextjs.md) 및 [Nuxt.js](deploy-nuxtjs.md)와 같은 프레임워크를 사용하여 **웹 애플리케이션** 을 배포합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [첫 번째 정적 앱 빌드](getting-started.md)
