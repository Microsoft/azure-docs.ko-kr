---
title: Azure IoT Edge 모듈 필수 구성 요소 | Microsoft Docs
description: IoT Edge 모듈을 게시하기 위한 필수 구성 요소입니다.
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
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: a4f1023bdf8a49fccbbda1fd0dc537f83a3acee1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60910339"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge 모듈 게시 필수 구성 요소

이 문서에서는 IoT Edge 모듈 제품을 게시하기 위한 필수 구성 요소를 설명합니다.  아직 수행 하는 경우 검토 합니다 [가이드를 게시 하는 IoT Edge 모듈](../..//iot-edge-module.md)합니다.


## <a name="publishing-prerequisites"></a>게시 필수 구성 요소

IoT Edge 모듈을 Azure Marketplace에 게시하려면 다음 필수 구성 요소를 충족해야 합니다.

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 액세스합니다. 자세한 내용은 [Azure Marketplace 및 AppSource 게시 가이드](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)를 참조하세요.
- [Azure Marketplace 약관](https://azure.microsoft.com/support/legal/marketplace-terms/)에 대한 규약
- Azure Container Registry에서 IoT Edge 모듈 기술 자산을 호스트합니다.  자세한 내용은 [IoT Edge 모듈 기술 자산을 준비하는 방법](./cpp-create-technical-assets.md)을 참조하세요.
- IoT Edge 모듈 메타데이터를 사용할 수 있도록 준비합니다. 예를 들어, 다음 자산을 준비 합니다.
    - 제목
    - 설명(HTML 형식)
    - 로고 이미지(PNG 형식 및 40x40px, 90x90px, 115x115px, 255x115px를 포함한 고정 이미지 크기)
    - 사용 약관 및 개인정보처리방침
    - 경로, 쌍 desired 속성, createOptions, 환경 변수를 포함하는 기본 모듈 구성
    - 모듈 설명서
    - 지원 연락처


## <a name="next-steps"></a>다음 단계

했으면 [IoT Edge 모듈 기술 자산을 준비](./cpp-create-technical-assets.md), 준비가 됩니다 [IoT Edge 모듈 제품 만들기](./cpp-create-offer.md)합니다. 
