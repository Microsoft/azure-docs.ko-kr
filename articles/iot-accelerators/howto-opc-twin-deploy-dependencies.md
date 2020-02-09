---
title: Azure에서 OPC 쌍 클라우드 종속성을 배포 하는 방법 Microsoft Docs
description: 이 문서에서는 로컬 개발 및 디버깅을 수행 하는 데 필요한 OPC 쌍 Azure 종속성을 배포 하는 방법을 설명 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 231d1efa02ec80e8ad56a8895d4262d774480111
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824109"
---
# <a name="deploying-dependencies-for-local-development"></a>로컬 개발을 위한 종속성 배포

이 문서에서는 로컬 개발 및 디버깅을 수행 하는 데 필요한 Azure Platform 서비스만 배포 하는 방법을 설명 합니다.   마지막으로 로컬 개발 및 디버깅에 필요한 모든 것을 포함 하는 리소스 그룹이 배포 됩니다.

## <a name="deploy-azure-platform-services"></a>Azure platform services 배포

1. PowerShell 및 [AzureRM powershell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) 확장이 설치 되어 있는지 확인 합니다.  명령 프롬프트 또는 터미널을 열고 다음을 실행 합니다.

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. 프롬프트에 따라 배포에 대 한 리소스 그룹에 이름을 할당 합니다.  이 스크립트는 Azure 구독의이 리소스 그룹에 대 한 종속성만 배포 하지만 마이크로 서비스는 배포 하지 않습니다.  또한 스크립트는 Azure Active Directory에 응용 프로그램을 등록 합니다.  이는 OAUTH 기반 인증을 지 원하는 데 필요 합니다.  배포에는 몇 분 정도 걸릴 수 있습니다.

3. 스크립트가 완료 되 면를 선택 하 여 env 파일을 저장할 수 있습니다.  Env 환경 파일은 개발 컴퓨터에서 실행 하려는 모든 서비스 및 도구의 구성 파일입니다.  

## <a name="troubleshooting-deployment-failures"></a>배포 오류 문제 해결

### <a name="resource-group-name"></a>리소스 그룹 이름

짧고 간단한 리소스 그룹 이름을 사용 해야 합니다.  이름은 리소스 명명 요구 사항을 준수 해야 하는 경우에도 리소스의 이름을 지정 하는 데 사용 됩니다.  

### <a name="azure-active-directory-aad-registration"></a>AAD (Azure Active Directory) 등록

배포 스크립트는 Azure Active Directory에서 AAD 응용 프로그램을 등록 하려고 시도 합니다.  선택한 AAD 테넌트에 대 한 권한에 따라이 작업이 실패할 수 있습니다.   세 개의 옵션이 있습니다.

1. 테넌트 목록에서 AAD 테넌트를 선택한 경우 스크립트를 다시 시작 하 고 목록에서 다른 항목을 선택 합니다.
2. 또는 개인 AAD 테넌트를 배포 하 고 스크립트를 다시 시작 하 고 사용 하도록 선택 합니다.
3. 인증 없이 계속 합니다.  마이크로 서비스를 로컬로 실행 하 고 있으므로이는 허용 되지만 프로덕션 환경을 모방 하지는 않습니다.  

## <a name="next-steps"></a>다음 단계

이제 OPC 쌍 서비스를 기존 프로젝트에 성공적으로 배포 했으므로 제안 된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [OPC 쌍 모듈을 배포 하는 방법에 대해 알아봅니다.](howto-opc-twin-deploy-modules.md)
