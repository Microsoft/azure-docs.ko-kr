---
title: Azure 스프링 클라우드의 서비스 계획 및 할당량
description: Azure 스프링 클라우드의 서비스 할당량 및 서비스 계획에 대해 알아보기
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 7d47311385848f96a12a50c1dcf8b8284869c71d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260359"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure 스프링 클라우드의 할당량 및 서비스 계획

모든 Azure 서비스는 리소스 및 기능에 대 한 기본 제한 및 할당량을 설정 합니다.   Azure 스프링 클라우드는 기본 및 표준 이라는 두 가지 가격 책정 계층을 제공 합니다. 이 문서의 두 계층에 대 한 제한이 자세히 설명 됩니다.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure 스프링 클라우드 서비스 계층 및 제한

| 리소스 | Basic | Standard
------- | ------- | -------
vCPU | 서비스 인스턴스당 1 개 | 서비스 인스턴스당 4 개
메모리 | 서비스 인스턴스당 2gb | 서비스 인스턴스당 8gb
구독당 지역별 Azure Spring Cloud 서비스 인스턴스 수 | 10 | 10
Azure Spring Cloud 서비스 인스턴스당 총 앱 인스턴스 수 | 25 | 500
영구적 볼륨 | 1GB/앱 x 10 앱 | 50 g b/앱 x 10 앱

## <a name="next-steps"></a>다음 단계

몇 가지 기본 제한을 늘릴 수 있습니다. 설정에 대 한 증가가 필요한 경우 [지원 요청을 만듭니다](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
