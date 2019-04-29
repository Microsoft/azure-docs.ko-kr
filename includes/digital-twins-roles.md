---
title: 포함 파일
description: 포함 파일
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/20/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 7e4760990229433b2ea40fadd0d17de0b52fcb36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60534112"
---
다음 표에서는 Azure Digital Twins에서 사용할 수 있는 역할에 대해 설명합니다.

| **역할** | **설명** | **식별자** |
| --- | --- | --- |
| 공간 관리자 | 지정된 공간 및 아래의 모든 노드에 대한 *CREATE*, *READ*, *UPDATE* 및 *DELETE* 권한. 글로벌 권한입니다. | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| 사용자 관리자| 사용자 및 사용자 관련 개체에 대한 *CREATE*, *READ*, *UPDATE* 및 *DELETE* 권한. 공간에 대한 *READ* 권한. | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| 디바이스 관리자 | 디바이스 및 디바이스 관련 개체에 대한 *CREATE*, *READ*, *UPDATE* 및 *DELETE* 권한. 공간에 대한 *READ* 권한. | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| 키 관리자 | 액세스 키에 대한 *CREATE*, *READ*, *UPDATE* 및 *DELETE* 권한. 공간에 대한 *READ* 권한. | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| 토큰 관리자 |  액세스 키에 대한 *READ* 및 *UPDATE* 권한. 공간에 대한 *READ* 권한. | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| 사용자 |  해당 관련 개체를 포함하여 공간, 센서 및 사용자에 대한 *READ* 권한. | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| 지원 전문가 |  액세스 키를 제외한 모든 항목에 대한 *READ* 권한. | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| 디바이스 설치 관리자 | 해당 관련 개체를 포함하여 디바이스 및 센서에 대한 *READ* 및 *UPDATE* 권한. 공간에 대한 *READ* 권한. | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| 게이트웨이 디바이스 | 센서에 대한 *CREATE* 권한. 해당 관련 개체를 포함하여 디바이스 및 센서에 대한 *READ* 권한. | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |