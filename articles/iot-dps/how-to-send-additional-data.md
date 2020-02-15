---
title: 장치와 Azure 장치 프로 비전 서비스 간에 페이로드를 전송 하는 방법
description: 이 문서에서는 장치 및 DPS (장치 프로 비전 서비스) 간에 페이로드를 전송 하는 방법을 설명 합니다.
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3eec39e975b1e782eafe16205623c625f462a865
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209412"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>장치와 DPS 간에 페이로드를 전송 하는 방법
경우에 따라 DPS는 장치에서 더 많은 데이터를 제공 하 여 올바른 IoT Hub에 적절히 프로 비전 하 고 해당 데이터를 장치에서 제공 해야 합니다. 그 반대의 경우 DPS는 클라이언트 쪽 logics를 용이 하 게 하기 위해 장치에 데이터를 반환할 수 있습니다. 

## <a name="when-to-use-it"></a>사용하는 시기
이 기능은 [사용자 지정 할당](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)에 대 한 향상 된 기능으로 사용할 수 있습니다. 예를 들어, 사용자 개입 없이 장치 모델을 기반으로 장치를 할당 하려고 합니다. 이 경우에는 [사용자 지정 할당](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)을 사용 합니다. 장치를 구성 하 여 [장치 등록 호출](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)의 일부로 모델 정보를 보고할 수 있습니다. DPS는 사용자 지정 할당 webhook에 장치의 페이로드를 전달 합니다. 그리고 함수는 장치 모델 정보를 받을 때이 장치가 이동 하는 IoT Hub를 결정할 수 있습니다. 마찬가지로 webhook에서 장치에 일부 데이터를 반환 하려는 경우 데이터를 다시 웹 후크 응답에서 문자열로 전달 합니다.  

## <a name="device-sends-data-payload-to-dps"></a>장치에서 DPS로 데이터 페이로드를 보냅니다.
장치에서 DPS에 [장치 등록 호출](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) 을 보내는 경우 본문의 다른 필드를 사용 하도록 등록 호출을 향상 시킬 수 있습니다. 본문은 다음과 같습니다. 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “interfaces”: “TODO: get how interfaces are reported by devices from PnP folks.”, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS가 장치에 데이터를 반환 합니다.
사용자 지정 할당 정책 webhook에서 일부 데이터를 장치로 반환 하려는 경우 데이터를 다시 웹 후크 응답의 문자열로 전달 합니다. 변경 내용은 아래 페이로드 섹션에 있습니다. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK 지원
이 기능은 C, C#, JAVA 및 Node.js [클라이언트 sdk](https://docs.microsoft.com/azure/iot-dps/)에서 사용할 수 있습니다.  

## <a name="next-steps"></a>다음 단계
* Azure IoT Hub 및 Azure IoT Hub Device Provisioning Service에서 [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks)를 사용하여 개발
