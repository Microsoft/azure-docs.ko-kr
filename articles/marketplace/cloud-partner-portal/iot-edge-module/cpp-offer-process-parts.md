---
title: Azure IoT Edge 모듈 제품 게시 개요 | Microsoft Docs
description: Azure Marketplace에서 IoT Edge 모듈 제품을 게시하는 프로세스에 대한 개요입니다.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 5b4d4471d9c77b5d13dfd5f8c2e9394b1c2d2a87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60910704"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>IoT Edge 모듈 제품 게시 개요

<table> <tr> <td>이 섹션에서는 Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>에 새로운 Azure IoT Edge 모듈 제안을 게시하는 방법을 설명합니다. IoT Edge 모듈은 IoT Edge 디바이스에서 실행하도록 만들어진 Docker 호환 컨테이너입니다. Azure IoT Edge 모듈은 IoT Edge가 관리하는 계산의 최소 단위이며 Azure 서비스 또는 사용자 지정 솔루션 코드를 포함할 수 있습니다. </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>게시 프로세스

IoT Edge 모듈 제품을 게시하기 위한 대략적인 단계는 다음과 같습니다.

1. 제품 만들기<br> 제품에 대한 자세한 정보를 제공합니다. 이 정보는 제품 설명, 마케팅 자료, 지원 정보 및 자산 사양을 포함합니다.

2. 비즈니스 및 기술 자산 만들기<br> 연결된 솔루션에 대한 비즈니스 자산(법률 문서 및 마케팅 자료) 및 기술 자산(Azure Container Registry에서 호스트되는 IoT Edge 모듈 이미지)을 만듭니다.

3. SKU 만들기<br> 제품과 연결된 SKU를 만듭니다. 게시하려는 각 이미지마다 고유한 SKU가 필요합니다.

4. 제품 인증 및 게시 <br>제품 및 기술 자산을 완료한 후에 제품을 제출할 수 있습니다. 이 제출은 게시 프로세스를 시작합니다. 이 프로세스 중 솔루션을 테스트하고, 유효성을 검사한 다음, Azure Marketplace에서 "라이브 상태가 됩니다".

## <a name="parts-of-an-offer"></a>제품의 부분

다음 문서에서는 IoT Edge 모듈 제품의 주요 부분을 설명합니다.

- [필수 구성 요소](./cpp-prerequisites.md) <br>이 문서에서는 IoT Edge 모듈 제품을 만들고 게시하기 전에 기술 및 비즈니스 요구 사항을 나열합니다.
- [IoT Edge 모듈 기술 자산 준비](./cpp-create-technical-assets.md) <br>이 문서에서는 IoT Edge 모듈에 대한 기술 자산을 준비하는 방법을 설명합니다. 이러한 자산은 IoT Edge 모듈을 Azure Marketplace에 게시하기 전에 모든 필수 기술 조건을 충족해야 합니다.
- [IoT Edge 모듈 제품 만들기](./cpp-create-offer.md) <br>이 문서에서는 [Cloud 파트너 포털](https://cloudpartner.azure.com)을 사용하여 새 IoT Edge 모듈 제품 항목을 만드는 데 필요한 단계를 나열합니다.
- [IoT Edge 모듈 제품 게시](./cpp-publish-offer.md)<br> 이 문서에서는 Azure Marketplace에 게시할 제품을 제출하는 방법을 설명합니다.

## <a name="next-steps"></a>다음 단계

IoT Edge 모듈을 Microsoft Azure Marketplace에 게시하기 위한 [기술 및 비즈니스 요구 사항](./cpp-prerequisites.md)을 검토합니다.
