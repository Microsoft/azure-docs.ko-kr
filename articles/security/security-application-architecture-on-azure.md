---
title: Azure 아키텍처 디자인으로 보안 통합 | Microsoft Docs
description: " 이 문서는 Azure에서 애플리케이션 및 서비스 아키텍처를 이해하고 디자인 및 구현으로 보안을 손쉽게 통합하는 데 도움이 됩니다. "
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: 4f2d9386-bda3-4ec8-8b1a-cd0c11242ffc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: terrylan
ms.openlocfilehash: 76f5627a28c2c0ab326197893a9b9739628af0fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597835"
---
# <a name="application-architecture-on-azure"></a>Azure 상의 애플리케이션 아키텍처
Microsoft Azure에서 클라우드 기반 솔루션의 보안을 유지하려면 견고한 아키텍처 기초가 중요합니다. 설계자, 디자이너 및 구현자는 모두 애플리케이션 및 서비스 아키텍처의 풍부한 지식을 활용합니다. 이러한 기본 지식은 모든 클라우드 기반 솔루션의 구성 요소를 이해하고 디자인 및 구현의 모든 측면으로 보안을 손쉽게 통합하는 데 도움이 됩니다.

다음을 통해 아키텍처 조사 및 디자인을 돕습니다.

* 아키텍처 청사진
* 클라우드 및 엔터프라이즈 기호 집합
* 3D 청사진 Visio 템플릿

## <a name="architectural-blueprints"></a>아키텍처 청사진
Microsoft는 Microsoft 제품을 사용하여 특정 유형의 시스템을 빌드하는 방법을 보여 주는 높은 수준의 일련의 [아키텍처 청사진](https://aka.ms/azblueprints) 을 게시하고 있습니다.
각 청사진에는 다음이 포함되어 있습니다.

* 사용자가 다운로드하여 수정할 수 있는 플랫 2D Visio 2003 기반 파일
* 비전문가에게 청사진을 소개하기 위한 컬러 3D 큐브 뷰 PDF 파일
* 3D 버전을 소개하는 비디오

## <a name="cloud-and-enterprise-symbol-set"></a>클라우드 및 엔터프라이즈 기호 집합
[Visio 및 기호 교육 비디오를 시청](https://aka.ms/CnESymbolsVideo)한 후 Azure, Windows Server, SQL Server 등을 설명하는 기술 자료를 작성하는 데 도움이 되는 [클라우드 및 엔터프라이즈 기호 집합을 다운로드](https://aka.ms/CnESymbols)하십시오. Microsoft 제품의 사용 방법을 설명하는 교재인 경우 이 기호를 아키텍처 다이어그램, 교육 자료, 프레젠테이션, 데이터시트, 인포그래픽, 백서 및 타사 책에도 사용할 수 있습니다. 하지만 사용자 인터페이스에는 사용할 수 없습니다.

## <a name="3d-blueprint-visio-template"></a>3D 청사진 Visio 템플릿
[Microsoft 아키텍처 청사진](https://aka.ms/azblueprints) 의 3D 버전은 타사 도구에서 처음 만들었습니다. 새로운 Visio 2013(및 이상) 템플릿은 [EDX.ORG에 분산된 Microsoft 아키텍처 인증 과정](https://docs.microsoft.com/azure/architecture/)의 일부로 2015년 8월 5일 제공되었습니다.

이 템플릿을 과정 외부에서도 사용할 있습니다.

* [교육 비디오를 시청](https://aka.ms/3dBlueprintTemplateVideo) 하여 이 템플릿으로 무엇을 할 수 있는지 알아보십시오.
*  [Microsoft 3D 청사진 Visio 템플릿](https://aka.ms/3DBlueprintTemplate)
* [클라우드 및 엔터프라이즈 기호](https://docs.microsoft.com/azure/architecture/) 를 다운로드하여 3D 템플릿과 함께 사용
