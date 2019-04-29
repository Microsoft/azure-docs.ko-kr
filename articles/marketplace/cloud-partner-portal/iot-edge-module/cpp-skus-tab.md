---
title: Azure IoT Edge 모듈 SKU | Microsoft Docs
description: IoT Edge 모듈에 대한 SKU를 만듭니다.
services: Azure, Marketplace, Cloud Partner Portal,
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
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 370d8160661c1f73124151a3a49d0bb3170dfb77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60910935"
---
# <a name="iot-edge-module-skus-tab"></a>IoT Edge 모듈 SKU 탭

**새 제품** 페이지의 **SKU** 탭을 사용하면 하나 이상의 SKU를 만들어 새 제품에 연결할 수 있습니다.  다양한 SKU를 사용하여 기능 집합, 청구 모델 또는 일부 다른 특성에 따라 솔루션을 구별할 수 있습니다.

## <a name="sku-settings"></a>SKU 설정

새 제품 만들기를 시작할 때 제품과 연결된 SKU가 없습니다. 새 SKU를 만들려면 다음 단계를 수행합니다.

- **IoT Edge 모듈 > 새 제품** 페이지에서 **SKU** 탭을 선택합니다.
- SKU 아래에서 **+ 새 SKU**를 선택하여 대화 상자를 엽니다.

  ![IoT Edge 모듈에 대한 새 제품 탭의 새 SKU 단추](./media/iot-edge-module-skus-tab-new-sku.png)

- **새 SKU** 대화 상자에서 SKU에 대한 식별자를 입력한 다음, **확인**을 선택합니다.
(다음 표는 식별자 명명 규칙을 제공합니다.)

**SKU** 탭이 새로 고쳐지고 SKU를 구성하도록 편집하는 필드가 표시됩니다. 필드 이름에 추가된 별표(*)는 필수를 나타냅니다.

|  **필드**       |     **설명**                                                          |
|  ---------       |     ---------------                                                          |
| **SKU ID**       | 이 SKU에 대한 식별자입니다. 이 이름은 소문자 영숫자 또는 대시(-)로 구성되며, 최대 50자이지만 대시로 끝날 수 없습니다. **참고:** 제품의 게시 된 후에이 이름을 변경할 수 없습니다. 이름은 제품 URL에 공개적으로 표시됩니다. |

## <a name="sku-details"></a>SKU 세부 정보

SKU가 Azure Marketplace 및 Azure Portal 웹 사이트에 표시될 방법을 정의하도록 **SKU 세부 정보**를 구성합니다.

![IoT Edge 모듈 SKU 메타데이터](media/iot-edge-module-skus-tab-metadata.png)

다음 표에서는 **SKU 세부 정보** 아래의 필드에 대한 용도, 내용 및 형식을 설명합니다.

|  **필드**       |     **설명**                                                          |
|  ---------       |     ---------------                                                          |
| **제목**        | 이 SKU의 제목입니다. 최대 길이는 50자입니다. <br/> Azure Portal에 표시되고 배포될 때 기본 모듈 이름으로 사용됩니다(공백 및 특수 문자 없이). 이 필드가 표시되는 위치를 정확하게 확인하려면 아래 그림을 참조하세요.|
| **요약**      | 이 SKU의 간단한 요약입니다. 최대 길이는 100자입니다. 제품을 요약하지 **마십시오**. SKU만 요약합니다.  이 요약은 Azure Marketplace에 표시됩니다. 이 필드가 표시되는 위치를 정확하게 확인하려면 아래 그림을 참조하세요.|
| **설명**  | 이 SKU의 짧은 설명입니다. 최대 길이는 3,000자입니다. 제품을 설명하지 마십시오. 이 SKU만 설명합니다. Azure Marketplace 및 Azure Portal에 표시됩니다. Azure Portal에서 Marketplace 탭에 정의된 제품을 설명하는 Marketplace 설명에 추가됩니다.  SKU 요약과 같을 수 있습니다. 이 필드가 표시되는 위치를 정확하게 확인하려면 아래 그림을 참조하세요.|
| **이 SKU 숨기기** | 기본 설정을 유지합니다. **아니요**입니다. |

### <a name="sku-example"></a>SKU 예제

 다음 예제에서는 SKU **제목**, **요약** 및 **설명** 필드가 서로 다른 보기에 표시되는 방식을 보여줍니다.
 
#### <a name="on-the-azure-marketplace-website"></a>Azure Marketplace 웹 사이트에서:

- SKU 세부 정보를 보는 경우:

    ![Azure Marketplace 웹 사이트에서 SKU가 표시되는 방법](media/iot-edge-module-ampdotcom-pdp-plans.png)

#### <a name="on-the-azure-portal-website"></a>Azure Portal 웹 사이트에서:

- SKU를 검색하는 경우:

    ![Azure Portal #1을 검색하는 경우 IoT Edge 모듈이 표시되는 방식](media/iot-edge-module-portal-browse.png)

    ![Azure Portal #2를 검색하는 경우 IoT Edge 모듈이 표시되는 방식](media/iot-edge-module-portal-product-picker.png)

- SKU를 검색하는 경우:

    ![Azure Portal을 검색하는 경우 IoT Edge 모듈이 표시되는 방식](media/iot-edge-module-portal-search.png)

- SKU 세부 정보를 보는 경우:

    ![포털에서 제품 세부 정보를 볼 때 IoT Edge 모듈이 표시되는 방식](./media/iot-edge-module-portal-pdp.png)

- 모듈을 배포하는 경우:
    
    ![배포될 때 IoT Edge 모듈이 표시되는 방식](./media/iot-edge-module-deployment.png)

## <a name="sku-content"></a>SKU 콘텐츠

**Edge 모듈 이미지**에서 IoT Edge 모듈을 업로드하는 데 필요한 정보를 제공합니다.

업로드하고 인증할 수 있도록 IoT Edge 모듈 이미지를 포함하는 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)(ACR)에 대한 액세스를 제공합니다. 게시된 후 Azure Marketplace에서 호스트된 공용 컨테이너 레지스트리를 사용하여 IoT Edge 모듈이 복사되고 배포됩니다.

여러 플랫폼을 대상으로 하고 태그를 통해 여러 버전을 제공할 수 있습니다. ["IoT Edge 모듈 기술 자산 준비"의 태그 및 버전 관리](./cpp-create-technical-assets.md)에 대해 자세히 알아봅니다.

![IoT Edge 모듈 이미지](./media/iot-edge-module-skus-tab-acr.png)

다음 표에서는 다음에 대한 필드의 용도, 내용 및 형식을 설명합니다.

- **이미지 리포지토리 세부 정보**
- **이미지 버전**

|  **필드**       |     **설명**                                                          |
|  ---------       |     ---------------                                                          |
|  ***이미지 리포지토리 세부 정보***   |  |
| **구독 ID**        | ACR의 Azure 구독 ID입니다.|
| **리소스 그룹 이름**      | ACR의 리소스 그룹 이름입니다.|
| **레지스트리 이름**  | ACR 레지스트리 이름입니다. 로그인 서버 이름이 아닌 레지스트리 이름만을 복사합니다(예: `azurecr.io` 없이). |
| **리포지토리 이름**  | IoT Edge 모듈을 포함하는 ACR의 리포지토리 이름입니다. **참고:** 이름을 설정 된 후 나중에 변경할 수 없습니다. 계정에 같은 이름의 다른 제품이 없도록 고유한 이름을 사용합니다. |
| **사용자 이름** | ACR(관리자 사용자 이름)과 연결된 사용자 이름입니다. |
| **암호** | ACR과 연결된 암호입니다. |
|  ***이미지 버전***   |  |
| **이미지 태그 또는 다이제스트** | 하나 이상의 `latest` 태그 및 버전 태그를 포함해야 합니다(예: `xx.xx.xx-`로 시작, 여기서 xx는 숫자). 여러 플랫폼을 대상으로 하는 [매니페스트 태그](https://github.com/estesp/manifest-tool)여야 합니다. 매니페스트 태그에서 참조하는 모든 태그는 업로드할 수 있도록 추가되어야 합니다. 태그를 사용하여 IoT Edge 모듈의 여러 버전을 추가할 수 있습니다. 모든 매니페스트 태그(`latest` 제외)는 `X.Y-` 또는 `X.Y.Z-`로 시작해야 합니다. 여기서 X, Y, Z는 정수입니다. ["IoT Edge 모듈 기술 자산 준비"의 태그 및 버전 관리](./cpp-create-technical-assets.md)에 대해 자세히 알아봅니다. <br/> 예를 들어 `latest` 태그가 `1.0.1-linux-x64`, `1.0.1-linux-arm32` 및 `1.0.1-windows-arm32`를 가리키는 경우 이러한 6개의 태그는 여기에 추가되어야 합니다. |

### <a name="help-your-customers-launch-your-iot-edge-module-by-using-default-settings"></a>기본 설정을 사용하여 고객이 IoT Edge 모듈을 시작하도록 돕기

IoT Edge 모듈을 배포하는 가장 일반적인 설정을 정의합니다. 이러한 기본값으로 IoT Edge 모듈 기본 제공을 시작하도록 하여 고객 배포를 최적화합니다.

![배포에서 IoT Edge 모듈 기본 설정](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

다음 표에서는 **기본 경로**, **기본 쌍 desired 속성**, **기본 환경 변수** 및 **기본 CreateOptions**에 대한 필드의 용도, 내용 및 형식을 설명합니다.

|  **필드**       |     **설명**                                                          |
|  ---------       |     ---------------                                                          |
| **기본 경로**        | 각 기본 경로 이름 및 값은 512자 미만이어야 합니다. 최대 5개의 기본 경로를 정의할 수 있습니다. 경로 값에 올바른 [경로 구문](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)을 사용해야 합니다. 모듈을 참조하려면 공백 및 특수 문자 없이 **SKU 제목**이 될 해당 기본 모듈 이름을 사용합니다. 아직 알려지지 않은 다른 모듈을 참조하려면 `<FROM_MODULE_NAME>` 규칙을 사용하여 고객이 이 정보를 업데이트해야 한다는 것을 알도록 합니다. [IoT Edge 경로](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)에 대해 자세히 알아봅니다. <br/> 예를 들어 모듈 `ContosoModule`이 `ContosoInput`의 입력 및 `ContosoOutput`에서 출력 데이터를 수신 대기하는 경우 다음 2개의 기본 경로를 정의하는 것이 좋습니다.<br/>- 이름 #1: `ToContosoModule`<br/>- 값 #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>- 이름 #2: `FromContosoModuleToCloud`<br/>- 값 #2: `FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **기본 쌍 desired 속성**      | 각 기본 쌍 desired 속성 이름 및 값은 512자 미만이어야 합니다. 최대 5개의 이름/값 쌍 desired 속성을 정의할 수 있습니다. 쌍 desired 속성의 값은 최대 4개의 중첩된 계층 구조의 배열이 없는 이스케이프되지 않은 유효한 JSON이어야 합니다. [쌍 desired 속성](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)에 대해 자세히 알아봅니다. <br/> 예를 들어 모듈이 쌍 desired 속성을 통해 동적으로 구성할 수 있는 새로 고침 빈도를 지원하는 경우 다음 기본 쌍 desired 속성을 정의하는 것이 좋습니다.<br/> - 이름 #1: `RefreshRate`<br/>- 값 #1: `60`|
| **기본 환경 변수**  | 각 기본 환경 변수 이름 및 값은 512자 미만이어야 합니다. 최대 5개의 이름/값 환경 변수를 정의할 수 있습니다. <br/>예를 들어 모듈을 시작하기 전에 사용 약관에 동의해야 하는 경우 다음 환경 변수를 정의할 수 있습니다.<br/> - 이름 #1: `ACCEPT_EULA`<br/>- 값 #1: `Y`|
| **기본 createOptions**  | createOptions는 512자 미만이어야 합니다. 이스케이프되지 않은 유효한 JSON이어야 합니다. [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules)에 대해 자세히 알아봅니다. <br/> 예를 들어 모듈에서 포트를 바인딩해야 하는 경우 다음 createOptions를 정의할 수 있습니다.<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|

<br/> **저장**을 선택하여 SKU 설정을 저장합니다. 

## <a name="next-steps"></a>다음 단계

[Marketplace 탭](./cpp-marketplace-tab.md)을 사용하여 제품에 대한 마켓플레이스 설명을 만듭니다.
