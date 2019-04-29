---
title: Microsoft® 부드러운 스트리밍 클라이언트 이식 키트 라이선스
description: Microsoft® 부드러운 스트리밍 클라이언트 이식 키트 라이선스를 얻는 방법에 대해 알아보세요.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: xpouyat
ms.openlocfilehash: 505def9cde7cddf2ddcc23408fa3159de886167a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472724"
---
# <a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Microsoft® 부드러운 스트리밍 클라이언트 이식 키트 라이선스 
## <a name="overview"></a>개요
Microsoft 부드러운 스트리밍 클라이언트 이식 키트(줄여서**SSPK**)는 포함된 장치 제조업체, 케이블 및 모바일 운영자, 콘텐츠 서비스 공급자, 송수화기 제조업체, ISV(독립 소프트웨어 공급업체) 및 솔루션 공급자가 적응 콘텐츠를 부드러운 스트리밍 형식으로 스트리밍하는 제품 및 서비스를 만들 수 있도록 최적화된 부드러운 스트리밍 클라이언트 구현입니다. SSPK는 정식 사용자가 어떠한 디바이스 및 플랫폼에도 이식할 수 있는 부드러운 스트리밍 클라이언트의 디바이스 및 플랫폼 독립적인 구현입니다. 

아래 내용은 상위 수준의 아키텍처로, IIS 부드러운 스트리밍 이식 키트 상자는 Microsoft에서 제공하는 부드러운 스트리밍 클라이언트 구현이며 부드러운 스트리밍 콘텐츠 재생을 위한 모든 핵심 논리를 포함합니다. 그런 다음 파트너가 적절한 인터페이스를 구현하여 특정 디바이스 또는 플랫폼에 맞게 이 콘텐츠를 이식합니다. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>설명
SSPK는 뛰어난 비즈니스 가치를 제공하는 조건으로 사용 허가됩니다. SSPK 라이선스는 업계에 다음을 제공합니다.

* C++의 부드러운 스트리밍 이식 키트 소스 
  * 부드러운 스트리밍 클라이언트 기능 구현
  * 형식 구문 분석, 추론, 버퍼링 논리 등 추가
* 플레이어 애플리케이션 API 
  * 미디어 플레이어 애플리케이션과 상호 작용을 위한 프로그래밍 인터페이스
* PAL(플랫폼 추상화 계층) 인터페이스 
  * 운영 체제(스레드, 소켓)와 상호 작용을 위한 프로그래밍 인터페이스
* HAL(하드웨어 추상화 계층) 인터페이스 
  * 하드웨어 A/V 디코더(디코딩, 렌더링)와 상호 작용을 위한 프로그래밍 인터페이스
* DRM(디지털 권한 관리) 인터페이스 
  * DAL(DRM 추상화 계층)을 통해 DRM을 처리하기 위한 프로그래밍 인터페이스
  * Microsoft PlayReady 이식 키트는 별도로 제공되지만 이 인터페이스를 통해 통합됩니다. Microsoft PlayReady 디바이스 라이선스에 대한 자세한 내용을 보려면 [여기](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl)를 클릭하세요.
* 구현 샘플 
  * Linux용 샘플 PAL 구현
  * GStreamer용 샘플 HAL 구현

## <a name="licensing-options"></a>라이선스 옵션
Microsoft 부드러운 스트리밍 클라이언트 이식 키트는 두 가지의 고유한 라이선스 계약에 따라 정식 사용자에게 제공됩니다. 하나는 부드러운 스트리밍 클라이언트 중간 제품 개발을 위한 라이선스에고 다른 하나는 부드러운 스트리밍 클라이언트 최종 제품을 최종 사용자에게 배포하기 위한 라이선스입니다.

* 칩셋 제조업체, 시스템 통합 업체 또는 중간 제품 개발을 위해 소스 코드 이식 키트가 필요한 ISV(독립 소프트웨어 공급업체)는 Microsoft 부드러운 스트리밍 클라이언트 이식 키트 **중간 제품 라이선스**를 실행해야 합니다.
* 부드러운 스트리밍 클라이언트 최종 제품을 최종 사용자에게 배포하는 권한이 필요한 디바이스 제조업체 또는 ISV는 Microsoft 부드러운 스트리밍 클라이언트 이식 키트 **최종 제품 라이선스**를 실행해야 합니다.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft 부드러운 스트리밍 클라이언트 이식 키트 중간 제품 라이선스
이 라이선스에 따라, Microsoft는 부드러운 스트리밍 클라이언트 이식 키트 및 필요한 지적 재산권을 제공하여 부드러운 스트리밍 클라이언트 중간 제품을 개발하고 다른 부드러운 스트리밍 클라이언트 이식 키트 디바이스 정식 사용자에게 배포하며 정식 사용자가 부드러운 스트리밍 클라이언트 최종 제품을 배포할 수 있도록 합니다.

#### <a name="fee-structure"></a>요금 구조
한 번의 미국 50,000달러 라이선스 비용으로 부드러운 스트리밍 클라이언트 이식 키트에 액세스할 수 있습니다. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft 부드러운 스트리밍 클라이언트 이식 키트 최종 제품 라이선스
이 라이선스에 따라, Microsoft는 다른 부드러운 스트리밍 클라이언트 이식 키트 정식 사용자로부터 부드러운 스트리밍 클라이언트 중간 제품을 받고 회사 브랜드 부드러운 스트리밍 클라이언트 최종 제품을 최종 사용자에게 배포하는 데 필요한 모든 지적 재산권을 제공합니다.

#### <a name="fee-structure"></a>요금 구조
부드러운 스트리밍 클라이언트 최종 제품은 아래 사용료 모델에 따라 제공됩니다.

* 제공된 디바이스 구현당 0.10달러
* 사용료는 각 연도에 50,000달러로 제한됨
* 각 연도의 최초 10,000대의 디바이스 구현에 대해서는 사용료 없음 

## <a name="licensing-procedure-and-sspk-access"></a>라이선스 절차 및 SSPK 액세스
라이선스와 관련해서 궁금한 점이 있으면 [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com)으로 메일을 보내 주세요.

[SSPK 배포 포털](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) 은 등록된 중간 정식 사용자가 액세스할 수 있습니다.

중간 및 최종 SSPK 정식 사용자는 [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com)를 클릭하세요.

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft 부드러운 스트리밍 클라이언트 중간 제품 계약 정식 사용자
* Adroit Business Solutions, Inc
* Advanced Digital Broadcast SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Ltd.
* Alticast Corporation
* Amazon Digital Services, Inc.
* Arion Technology, Inc.
* AVC Multimedia Software Co., Ltd.
* Cavium, Inc.
* EchoStar Purchasing Corporation
* Enseo, Inc.
* Fluendo S.A.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* iWEDIA S.A. 
* Liberty Global Services BV
* MediaTek Inc.
* MStar Co, Ltd
* Nintendo Co., Ltd.
* OpenTV, Inc.
* Saffron Digital Limited
* Sichuan Changhong Electric Co., Ltd
* SoftAtHome
* Sony Corporation
* Tatung Technology Inc.
* TCL Technology Electronics(Huizhou) Co., Ltd.
* Top Victory Investments, Ltd.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft 부드러운 스트리밍 클라이언트 최종 제품 계약 정식 사용자
* Advanced Digital Broadcast SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Ltd.
* Amazon Digital Services, Inc.
* AmTRAN Technology Co., Ltd.
* Arcadyan Technology Corporation
* Arion Technology, Inc.
* ATMACA ELEKTRONİK SAN. VE TİC. A.Ş
* British Sky Broadcasting Limited
* CastPal Technology Inc., Shenzhen
* Compal Electronics, Inc.
* Dongguan Digital AV Technology Corp., Ltd.
* EchoStar Purchasing Corporation
* Enseo, Inc.
* FilmFlex 영화 제한
* Fluendo S.A.
* FUNAI ELECTRIC CO., LTD
* Gibson Innovations Limited
* Haier Information Applicantion S.R.L
* HANDAN BroadInfoCom Co., Ltd.
* Hisense International Co., Ltd. 
* Homecast Co., Ltd
* Hon Hai Precision Industry Co., Ltd.
* Infomir GMBH
* Kaonmedia Co., Ltd.
* KDDI Corporation
* Nintendo Co., Ltd.
* Orange SA
* Saffron Digital Limited
* Sagemcom Broadband SAS
* Shenzhen Coship Electronics CO., LTD
* Shenzhen Jiuzhou Electric Co., Ltd
* Shenzhen Skyworth Digital Technology Co., Ltd
* Sichuan Changhong Electric Co., Ltd.
* Skardin Industrial Corp.
* Sky Deutschland Fernsehen GmbH & Co. KG
* SmarDTV S.A.
* SoftAtHome
* Sony Corporation
* TCL Overseas Marketing (Macao Commercial Offshore) Limited
* Technicolor Delivery Technologies, SAS
* Tongfang Global Ltd.
* Top Victory Investments, Ltd.
* Toshiba Lifestyle Products & Services Corporation
* Universal Media Corporation /Slovakia/ s.r.o.
* VIZIO, Inc.
* Wistron Corporation
* ZTE Corporation

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

