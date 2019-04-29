---
title: Azure에서 OPC 쌍 클라우드 종속성을 배포 하는 방법 | Microsoft Docs
description: OPC 쌍 Azure 종속성을 배포 하는 방법입니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ae9f2b05bfc6ea6315022d04c8d267d916cf282e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61451602"
---
# <a name="deploying-dependencies-for-local-development"></a>로컬 개발에 대 한 배포 종속성

이 문서에서는 로컬 개발 및 디버깅을 위해 Azure 플랫폼 서비스 필요만 배포 하는 방법에 설명 합니다.   끝으로 로컬 개발 및 디버깅에 필요한 모든 것을 포함 하는 리소스 그룹 배포 해야 합니다.

## <a name="deploy-azure-platform-services"></a>Azure 플랫폼 서비스를 배포 합니다.

1. PowerShell이 있는지 확인 하 고 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-1.1.0) 확장을 설치 합니다.  명령 프롬프트 또는 터미널을 열고 실행 합니다.

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. 지시에 따라 배포에 대 한 리소스 그룹에 이름을 할당 합니다.  스크립트는 Azure 구독에 있지만 마이크로 서비스가 아님이 리소스 그룹에 종속성만 배포합니다.  스크립트는 또한 Azure Active Directory에서 응용 프로그램을 등록합니다.  OAUTH 기반 인증을 지원 하려면 필요 합니다.  배포에는 몇 분 정도 걸릴 수 있습니다.

3. 스크립트가 완료 되 면.env 파일을 저장 하도록 선택할 수 있습니다.  .Env 환경 파일은 모든 서비스 및 도구의 개발 컴퓨터에서 실행 하려면 구성 파일.  

## <a name="troubleshooting-deployment-failures"></a>배포 오류 문제 해결

### <a name="resource-group-name"></a>리소스 그룹 이름

짧고 간단한 리소스 그룹 이름을 사용 해야 합니다.  이름으로 리소스에 같이 준수 해야 리소스 명명 요구 사항 이름도 사용 됩니다.  

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (AAD) 등록

배포 스크립트를 Azure Active Directory에서 AAD 응용 프로그램을 등록 하려고 시도 합니다.  선택한 AAD 테 넌 트 사용자 권한에 따라이 실패할 수 있습니다.   다음과 같은 3 가지가 있습니다.

1. AAD 테 넌 트를 테 넌 트의 목록에서 선택한 경우 스크립트를 다시 시작 하 고 목록에서 다른 이름을 선택 합니다.
2. 또는 개인 AAD 테 넌 트를 배포 스크립트를 다시 시작 하 고 사용 하려면 선택 합니다.
3. 인증 하지 않고 계속 합니다.  마이크로 서비스를 로컬로 실행 중인 때문이 허용 되지만 프로덕션 환경을 모방 하지 않습니다.  

## <a name="next-steps"></a>다음 단계

기존 프로젝트를 성공적으로 OPC 쌍 서비스를 배포 했으므로 다음 제안된 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [OPC 쌍 모듈을 배포 하는 방법 알아보기](howto-opc-twin-deploy-modules.md)
