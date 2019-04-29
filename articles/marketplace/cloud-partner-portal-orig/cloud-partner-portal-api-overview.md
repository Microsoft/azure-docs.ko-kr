---
title: Cloud 파트너 포털 API 참조 | Microsoft Docs
description: 사용할 필수 조건에 대한 설명과 Marketplace API 작업 목록입니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 116eb48330381c7560c55ea9535b3c1b7c6a6a70
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094401"
---
<a name="cloud-partner-portal-api-reference"></a>Cloud 파트너 포털 API 참조
==================================

Cloud 파트너 포털 REST API를 사용하면 프로그래밍 방식으로 워크로드, 제안 및 게시자 프로필을 검색 및 조작할 수 있습니다. 이 API는 RBAC(역할 기반 액세스 제어)를 사용하여 처리 시에 올바른 권한을 적용합니다.

이 참조는 Cloud 파트너 포털 REST API에 대한 기술 세부 정보를 제공합니다. 이 문서의 페이로드 샘플은 참조용일 뿐이며 새 기능이 추가되면 변경될 수 있습니다.


<a name="prerequisites-and-considerations"></a>필수 구성 요소 및 고려 사항
-------------------------------

이 API를 사용하기 전에 다음을 검토해야 합니다.

- 계정에 서비스 주체를 추가하고 인증을 위한 Azure AD(Azure Active Directory) 액세스 토큰을 가져오는 방법을 알아보기 위한 [필수 구성 요소](./cloud-partner-portal-api-prerequisites.md) 문서 
- 이러한 API를 호출하는 데 사용할 수 있는 두 가지 [동시성 제어](./cloud-partner-portal-api-concurrency-control.md)
전략
- 버전 관리 및 오류 처리와 같은 추가 API [고려 사항](./cloud-partner-portal-api-considerations.md)


<a name="common-tasks"></a>일반 작업
------------
이 참조는 다음과 같은 일반적인 작업을 수행하기 위한 API를 자세히 설명합니다.


### <a name="offers"></a>제품

-   [모든 제안 검색](./cloud-partner-portal-api-retrieve-offers.md)
-   [특정 제안 검색](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [제품 상태 검색](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [제안 만들기](./cloud-partner-portal-api-creating-offer.md)
-   [제품 게시](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>작업

-   [작업 검색](./cloud-partner-portal-api-retrieve-operations.md)
-   [작업 취소](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>앱 게시

-   [라이브로 전환](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>다른 작업

-   [가상 머신 제안에 대한 가격 책정 설정](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>문제 해결

-   [인증 오류 문제 해결](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
