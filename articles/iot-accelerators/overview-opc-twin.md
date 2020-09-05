---
title: OPC 쌍이란? - Azure | Microsoft Docs
description: 이 문서는 OPC 쌍에 대한 개요를 제공합니다. OPC 쌍은 REST API를 통한 산업용 디바이스의 검색, 등록 및 원격 제어를 제공합니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 169a3149a2d8d02d486bc126071b17e9a4f7ed72
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167473"
---
# <a name="what-is-opc-twin"></a>OPC 쌍이란?

OPC 쌍은 Azure IoT Edge 및 IoT Hub를 사용하여 클라우드와 팩터리 네트워크를 연결하는 마이크로서비스로 구성되어 있습니다. OPC 쌍은 REST API를 통한 산업용 디바이스의 검색, 등록 및 원격 제어를 제공합니다. OPC 쌍은 OPC UA(OPC 통합 아키텍처) SDK가 필요하지 않고, 프로그래밍 언어 중립적이며, 서버리스 워크플로에 포함될 수 있습니다. 이 문서에서는 몇 가지 OPC 쌍 사용 사례를 설명합니다.

## <a name="discovery-and-control"></a>검색 및 제어
간단한 검색 및 등록에 OPC 쌍을 사용할 수 있습니다.

### <a name="simple-discovery-and-registration"></a>간단한 검색 및 등록
OPC 쌍을 사용하면 팩터리 운영자는 팩터리 네트워크를 검사할 수 있으므로 OPC UA 서버를 검색하고 등록할 수 있습니다. 또는 팩터리 운영자가 알려진 검색 URL을 사용하여 직접 OPC UA 디바이스를 등록할 수도 있습니다. 예를 들어, 작업 현장에 OPC 쌍 모듈이 있는 IoT Edge 게이트웨이를 설치한 후 모든 OPC UA 디바이스에 연결하기 위해 팩터리 운영자가 네트워크 검사를 원격으로 트리거하고 모든 OPC UA 서버를 시각적으로 볼 수 있습니다. 

### <a name="simple-control"></a>간단한 제어
OPC 쌍을 사용하면 팩터리 운영자는 이벤트에 반응하고 클라우드에서 작업 머신을 자동 또는 수동으로 즉시 재구성할 수 있습니다. OPC 쌍은 OPC UA 서버에서 서비스를 호출하고, 변수의 읽기/쓰기뿐만 아니라 해당 주소 공간을 찾아보고, 메서드를 실행하는 REST API를 제공합니다. 예를 들어, 보일러는 온도 KPI를 사용하여 생산 라인을 제어합니다. 온도 센서는 OPC 게시자를 사용하여 데이터의 변경 내용을 게시합니다. 팩터리 운영자는 온도가 임계값에 도달했다는 경고를 받습니다. 생산 라인은 자동으로 OPC 쌍을 통해 온도를 내립니다. 팩터리 운영자는 온도가 내려갔다는 알림을 받습니다.

## <a name="authentication"></a>인증
간단한 인증 및 간단한 개발자 환경에 OPC 쌍을 사용할 수 있습니다.

### <a name="simple-authentication"></a>간단한 인증 
OPC 쌍은 AAD(Azure Active Directory) 기반 인증 및 엔드투엔드의 감사를 사용합니다. 예를 들어, OPC 쌍은 운영자가 머신에서 수행한 작업을 파악하도록 하기 위해 애플리케이션을 OPC 쌍 위에 빌드할 수 있습니다. 머신 쪽에서 OPC UA 감사를 통해 이루어집니다. 클라우드 쪽에서는 REST API에 불변성 클라이언트 감사 로그 및 AAD 인증을 저장해서 수행합니다.

### <a name="simple-developer-experience"></a>간단한 개발자 환경 
OPC 쌍은 REST API를 통해 모든 프로그래밍 언어로 작성한 애플리케이션에 사용할 수 있습니다. 개발자가 솔루션에 OPC UA 클라이언트를 통합하는 경우 OPC UA SDK에 대한 지식이 필요하지 않습니다. OPC 쌍은 상태 비저장, 서버리스 아키텍처로 원활하게 통합할 수 있습니다. 예를 들어 경보 및 이벤트 대시보드에 대한 애플리케이션을 개발하는 전체 스택 웹 개발자는 C, C# 또는 전체 OPC UA 스택 구현에 대한 지식 없이도 OPC 쌍을 사용하여 JavaScript 또는 TypeScript에서 이벤트에 응답하는 논리를 작성할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이제 OPC 쌍과 그 용도에 대해 파악했으므로, 권장되는 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [OPC 자격 증명 모음이란?](overview-opc-vault.md)
