---
title: 기존 Azure 프로젝트에는 OPC 쌍 모듈을 배포 하는 방법 | Microsoft Docs
description: OPC 쌍 기존 프로젝트를 배포 하는 방법입니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6bdfeefc366734aa10dbaccec69bac8e0b41103f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61451315"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>기존 프로젝트에 OPC 쌍 배포

OPC 쌍 모듈이 IoT Edge에서 실행 되 고 OPC 쌍 및 레지스트리 서비스에 여러 지 서비스를 제공 합니다. 

OPC 쌍 마이크로 서비스 팩토리 연산자와 OPC 쌍 IoT Edge 모듈을 통해 공장 현장에서 OPC UA 서버 장치 간의 통신을 용이 하 게 합니다. 마이크로 서비스는 해당 REST API를 통해 OPC UA 서비스 (찾아보기, 읽기, 쓰기 및 실행)을 노출 합니다. 

OPC UA 장치 레지스트리 마이크로 서비스를 등록 된 OPC UA 응용 프로그램 및 해당 끝점에 대 한 액세스를 제공합니다. 운영자 및 관리자 수 등록 및 새 OPC UA 응용 프로그램의 등록을 취소 하 고 해당 끝점을 포함 한 기존 찾아보기. 응용 프로그램 및 끝점 관리 하는 것 외에도 레지스트리 서비스는 또한 등록 된 OPC 쌍 IoT Edge 모듈 카탈로그입니다. 서비스 API를 사용 하면 컨트롤 가장자리 모듈 기능을 예를 들어, 시작, 서버 검색 (검색 서비스), 중지 또는 OPC 쌍 마이크로 서비스를 사용 하 여 액세스할 수 있는 새로운 끝점 쌍을 활성화 합니다.

모듈의 핵심은 감독자 id입니다. 감독자는 해당 OPC UA 레지스트리 API를 사용 하 여 활성화 되는 OPC UA 서버의 끝점에 해당 하는 끝점 쌍을 관리 합니다. 이 끝점 쌍 상태 저장 보안 채널을 통해 관리 되는 끝점으로 전송 되는 OPC UA 이진 메시지를 클라우드에서 실행 되는 OPC 쌍 마이크로 서비스에서 받은 OPC UA JSON을 변환 합니다. 또한 감독자는 이러한 이벤트 발생 OPC UA 레지스트리 업데이트를 처리 하는 것에 대 한 OPC UA 장치 온 보 딩 서비스 장치 검색 이벤트를 전송 하는 검색 서비스를 제공 합니다.  이 문서에서는 OPC 쌍 모듈 기존 프로젝트를 배포 하는 방법을 보여 줍니다. 

> [!NOTE]
> 배포 세부 정보 및 지침에 대 한 자세한 내용은 참조는 GitHub [리포지토리](https://github.com/Azure/azure-iiot-opc-twin-module)합니다.

## <a name="prerequisites"></a>필수 조건

PowerShell이 있는지 확인 하 고 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) 확장을 설치 합니다.   하지 않았으면 지금 아직, 경우에이 GitHub 리포지토리를 복제 합니다.  명령 프롬프트 또는 터미널을 열고 실행 합니다.

```bash
git clone --recursive https://github.com/Azure/azure-iiot-components 
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>산업 IoT 서비스를 Azure에 배포

1. 열린 명령 프롬프트 또는 터미널에서 다음을 실행 합니다.

   ```bash
   deploy
   ```

2. 지시에 따라 웹 사이트에 배포의 리소스 그룹에 이름과 이름을 할당 합니다.   스크립트는 마이크로 서비스 및 Azure 구독에서 리소스 그룹에 Azure 플랫폼 종속성을 배포합니다.  스크립트는 또한 OAUTH 기반 인증을 지원 하도록 Azure Active Directory (AAD) 테 넌 트에서 응용 프로그램을 등록 합니다.  배포에는 몇 분 정도 걸립니다.  예제 솔루션을 성공적으로 배포한 후에 참조입니다.

   ![기존 프로젝트에 산업 IoT OPC 쌍 배포](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   출력에는 공용 끝점의 URL을 포함합니다. 

3. 스크립트를 성공적으로 완료 되 면.env 파일을 저장할 것인지를 선택 합니다.  콘솔과 같은 도구를 사용 하 여 클라우드 끝점에 연결 하거나 개발 및 디버깅에 대 한 모듈을 배포 하려는 경우.env 환경 파일이 필요 합니다.

## <a name="troubleshooting-deployment-failures"></a>배포 오류 문제 해결

### <a name="resource-group-name"></a>리소스 그룹 이름

짧고 간단한 리소스 그룹 이름을 사용 해야 합니다.  이름으로 리소스에 같이 준수 해야 리소스 명명 요구 사항 이름도 사용 됩니다.  

### <a name="website-name-already-in-use"></a>이미 사용 중인 웹 사이트 이름

웹 사이트의 이름을 이미 사용 중인 것 같습니다.  이 오류를 실행 하는 경우 다른 응용 프로그램 이름을 사용 해야 합니다.

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (AAD) 등록

배포 스크립트를 Azure Active Directory에 두 개의 AAD 응용 프로그램을 등록 하려고 시도 합니다.  선택한 AAD 테 넌 트 사용자 권한에 따라 배포가 실패할 수 있습니다. 두 가지 옵션이 있습니다.

1. AAD 테 넌 트를 테 넌 트의 목록에서 선택한 경우 스크립트를 다시 시작 하 고 목록에서 다른 이름을 선택 합니다.
2. 또는 개인 AAD 테 넌 트를 다른 구독에 배포 스크립트를 다시 시작 하 고 사용 하려면 선택 합니다.

> [!WARNING]
> 인증 하지 않고 계속 되지 않습니다.  이렇게 하면 인증 되지 않은 인터넷에서 OPC 쌍 끝점 누구나 액세스할 수 있습니다.   선택할 수도 있습니다는 ["local" 배포 옵션](howto-opc-twin-deploy-dependencies.md) 바퀴를 시작 합니다.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>배포에 올인원 산업 IoT 서비스 데모

서비스와 종속성만 대신에 올인원 데모를 배포할 수도 있습니다.  하나의 데모에서는 모든 OPC UA 서버 세, OPC 쌍 모듈, 모든 마이크로 서비스 및 샘플 웹 응용 프로그램을 포함합니다.  데모용 것입니다.

1. (위 참조) 리포지토리를 복제 했는지 확인 합니다. 리포지토리 및 실행의 루트에서 명령 프롬프트 또는 터미널을 엽니다.

   ```bash
   deploy -type demo
   ```

2. 지시에 따라 웹 사이트에 이름과 리소스 그룹에 새 이름을 할당 합니다.  성공적으로 배포 되 면 스크립트를 웹 응용 프로그램 끝점의 URL이 표시 됩니다.

## <a name="deployment-script-options"></a>배포 스크립트 옵션

스크립트는 다음 매개 변수를 사용합니다.

```bash
-type
```

배포 (vm 로컬 데모)의 형식

```bash
-resourceGroupName
```

기존 또는 새 리소스 그룹의 이름일 수 있습니다.

```bash
-subscriptionId
```

선택적 리소스를 배포할 구독 ID입니다.

```bash
-subscriptionName
```

또는 구독 이름입니다.

```bash
-resourceGroupLocation
```

선택 사항, 리소스 그룹 위치입니다. 를 지정 하는 경우이 위치에 새 리소스 그룹을 만들 하려고 합니다.

```bash
-aadApplicationName
```

이름에 아래 등록 하려면 AAD 응용 프로그램입니다. 

```bash
-tenantId
```

AAD 테 넌 트를 사용 합니다.

```bash
-credentials
```

## <a name="next-steps"></a>다음 단계

이제 OPC 쌍 기존 프로젝트를 배포 하는 방법을 배웠으므로 제안 된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [OPC 클라이언트 및 OPC PLC 보안 통신](howto-opc-vault-deploy-existing-client-plc-communication.md)
