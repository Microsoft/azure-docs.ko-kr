---
title: OPC 쌍 모듈을 기존 Azure 프로젝트에 배포 하는 방법 | Microsoft Docs
description: 이 문서에서는 OPC 쌍을 기존 프로젝트에 배포 하는 방법을 설명 합니다. 배포 오류 문제를 해결 하는 방법에 대해서도 알아볼 수 있습니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b971ec13c71ccfd7d28ae6987593d09201b9b764
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824114"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>OPC 쌍을 기존 프로젝트에 배포

OPC 쌍 모듈은 IoT Edge에서 실행 되며 OPC 쌍 및 레지스트리 서비스에 대 한 몇 가지 Edge 서비스를 제공 합니다.

OPC 쌍 마이크로 서비스는 OPC 쌍 IoT Edge 모듈을 통해 팩터리에서 팩터리 연산자와 OPC UA 서버 장치 간의 통신을 용이 하 게 합니다. 마이크로 서비스은 REST API를 통해 OPC UA 서비스 (찾아보기, 읽기, 쓰기 및 실행)를 노출 합니다. 

OPC UA 장치 레지스트리 마이크로 서비스는 등록 된 OPC UA 응용 프로그램 및 해당 엔드포인트에 대 한 액세스를 제공 합니다. 운영자와 관리자는 새 OPC UA 응용 프로그램을 등록 하 고 등록을 취소 하 고 해당 엔드포인트을 포함 한 기존 응용 프로그램을 찾아볼 수 있습니다. 응용 프로그램 및 엔드포인트 관리 외에도 레지스트리 서비스는 등록 된 OPC 쌍 IoT Edge 모듈에 카탈로그 합니다. 서비스 API를 사용 하면 서버 검색을 시작 또는 중지 (서비스 검색) 하거나 OPC 쌍 마이크로 서비스를 사용 하 여 액세스할 수 있는 새 엔드포인트 쌍을 활성화 하는 등의 edge 모듈 기능을 제어할 수 있습니다.

모듈의 핵심은 감독자 id입니다. 감독자는 해당 OPC UA 레지스트리 API를 사용 하 여 활성화 된 OPC UA 서버 엔드포인트에 해당 하는 엔드포인트 쌍을 관리 합니다. 이 엔드포인트은 클라우드에서 실행 되는 OPC 쌍 마이크로 서비스에서 받은 OPC UA JSON을 OPC UA 이진 메시지로 변환 합니다 .이 메시지는 상태 저장 보안 채널을 통해 관리 되는 엔드포인트으로 전송 됩니다. 또한 감독자는 처리를 위해 장치 검색 이벤트를 OPC UA 장치 등록 서비스로 보내는 검색 서비스를 제공 하며, 이러한 이벤트는 OPC UA 레지스트리를 업데이트 합니다.  이 문서에서는 OPC 쌍 모듈을 기존 프로젝트에 배포 하는 방법을 보여 줍니다.

> [!NOTE]
> 배포 세부 정보 및 지침에 대 한 자세한 내용은 GitHub [리포지토리](https://github.com/Azure/azure-iiot-opc-twin-module)를 참조 하세요.

## <a name="prerequisites"></a>필수 조건

PowerShell 및 [AzureRM powershell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) 확장이 설치 되어 있는지 확인 합니다. 아직 수행 하지 않은 경우이 GitHub 리포지토리를 복제 합니다. PowerShell에서 다음 명령을 실행합니다.

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Azure에 산업용 IoT 서비스 배포

1. PowerShell 세션에서 다음을 실행 합니다.

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. 프롬프트에 따라 배포의 리소스 그룹에 이름을 지정 하 고 웹 사이트에 이름을 지정 합니다.   이 스크립트는 마이크로 서비스와 해당 Azure platform 종속성을 Azure 구독의 리소스 그룹에 배포 합니다.  또한이 스크립트는 OAUTH 기반 인증을 지원 하기 위해 AAD (Azure Active Directory) 테넌트에 응용 프로그램을 등록 합니다.  배포에는 몇 분 정도 걸릴 수 있습니다.  솔루션이 성공적으로 배포 되 면 표시 되는 항목의 예:

   ![산업용 IoT OPC 쌍 기존 프로젝트에 배포](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   출력에는 공용 엔드포인트의 URL이 포함 됩니다. 

3. 스크립트가 성공적으로 완료 되 면 `.env` 파일을 저장할지 여부를 선택 합니다.  콘솔, 개발 및 디버깅을 위한 모듈 배포 등의 도구를 사용 하 여 클라우드 엔드포인트에 연결 하려면 `.env` 환경 파일이 필요 합니다.

## <a name="troubleshooting-deployment-failures"></a>배포 오류 문제 해결

### <a name="resource-group-name"></a>리소스 그룹 이름

짧고 간단한 리소스 그룹 이름을 사용 해야 합니다.  이름은 리소스 명명 요구 사항을 준수 해야 하는 경우에도 리소스의 이름을 지정 하는 데 사용 됩니다.  

### <a name="website-name-already-in-use"></a>웹 사이트 이름이 이미 사용 중입니다.

웹 사이트의 이름을 이미 사용 하 고 있을 수 있습니다.  이 오류가 발생 하는 경우 다른 응용 프로그램 이름을 사용 해야 합니다.

### <a name="azure-active-directory-aad-registration"></a>AAD (Azure Active Directory) 등록

배포 스크립트는 Azure Active Directory에서 두 AAD 응용 프로그램을 등록 하려고 시도 합니다.  선택한 AAD 테넌트에 대 한 권한에 따라 배포가 실패할 수 있습니다. 두 가지 옵션이 있습니다.

1. 테넌트 목록에서 AAD 테넌트를 선택한 경우 스크립트를 다시 시작 하 고 목록에서 다른 항목을 선택 합니다.
2. 또는 다른 구독에 개인 AAD 테넌트를 배포한 후 스크립트를 다시 시작 하 고 사용 하도록 선택 합니다.

> [!WARNING]
> 인증 없이 계속 해 서는 안 됩니다.  이 작업을 수행 하도록 선택 하는 경우 누구나 인증 되지 않은 인터넷에서 OPC 쌍 엔드포인트에 액세스할 수 있습니다.   언제 든 지 ["로컬" 배포 옵션](howto-opc-twin-deploy-dependencies.md) 을 선택 하 여 타이어를 시작할 수 있습니다.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>일대일 산업용 IoT 서비스 데모 배포

서비스 및 종속성 뿐만 아니라 전체 데모를 배포할 수도 있습니다.  한 가지 데모에는 opc UA 서버, OPC 쌍 모듈, 모든 마이크로 서비스 및 샘플 웹 응용 프로그램이 포함 됩니다.  데모용입니다.

1. 리포지토리의 복제본이 있는지 확인 합니다 (위 참조). 리포지토리의 루트에서 PowerShell 프롬프트를 열고 다음을 실행 합니다.

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. 프롬프트에 따라 리소스 그룹에 새 이름을 할당 하 고 웹 사이트에 이름을 지정 합니다.  성공적으로 배포 되 면 스크립트는 웹 응용 프로그램 엔드포인트의 URL을 표시 합니다.

## <a name="deployment-script-options"></a>배포 스크립트 옵션

스크립트는 다음 매개 변수를 사용 합니다.

```powershell
-type
```

배포 유형 (vm, 로컬, 데모)

```powershell
-resourceGroupName
```

은 기존 또는 새 리소스 그룹의 이름일 수 있습니다.

```powershell
-subscriptionId
```

리소스를 배포할 구독 ID (선택 사항)입니다.

```powershell
-subscriptionName
```

또는 구독 이름입니다.

```powershell
-resourceGroupLocation
```

선택 사항으로, 리소스 그룹 위치입니다. 지정 된 경우는이 위치에 새 리소스 그룹을 만들려고 합니다.

```powershell
-aadApplicationName
```

등록할 AAD 응용 프로그램의 이름입니다.

```powershell
-tenantId
```

사용할 AAD 테넌트입니다.

```powershell
-credentials
```

## <a name="next-steps"></a>다음 단계

이제 OPC 쌍을 기존 프로젝트에 배포 하는 방법을 배웠으므로 다음 단계를 제안 합니다.

> [!div class="nextstepaction"]
> [OPC UA 클라이언트 및 OPC UA PLC의 보안 통신](howto-opc-vault-secure.md)
