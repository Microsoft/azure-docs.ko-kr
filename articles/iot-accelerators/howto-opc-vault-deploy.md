---
title: OPC 자격 증명 모음 인증서 관리 서비스를 배포 하는 방법-Azure | Microsoft Docs
description: OPC 자격 증명 모음 인증서 관리 서비스를 처음부터 배포 하는 방법입니다.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f577059e1ebf70e3a9dfe9e538a9d3d49d7c8e96
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200000"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>OPC 자격 증명 모음 인증서 관리 서비스 빌드 및 배포

이 문서에서는 Azure에서 OPC 자격 증명 모음 인증서 관리 서비스를 배포 하는 방법을 설명 합니다.

> [!NOTE]
> 자세한 내용은 GitHub [OPC 자격 증명 모음 리포지토리](https://github.com/Azure/azure-iiot-opc-vault-service)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="install-required-software"></a>필수 소프트웨어 설치

현재 빌드 및 배포 작업은 Windows로 제한 됩니다.
샘플은 모두 .NET Standard 용 C# 으로 작성 되었으며 배포를 위해 서비스와 샘플을 빌드해야 합니다.
.NET Standard 하는 데 필요한 모든 도구는 .NET Core 도구와 함께 제공 됩니다. [.Net Core 시작을](https://docs.microsoft.com/dotnet/articles/core/getting-started)참조 하세요.

1. [.Net Core 2.1 이상을 설치][dotnet-install]합니다.
2. [Docker 설치][docker-url] (선택 사항, 로컬 Docker 빌드를 필요로 하는 경우에만).
4. [PowerShell 용 Azure 명령줄 도구][powershell-install]를 설치 합니다.
5. [Azure 구독][azure-free]에 등록 합니다.

### <a name="clone-the-repository"></a>리포지토리 복제

아직 수행 하지 않은 경우이 GitHub 리포지토리를 복제 합니다. 명령 프롬프트 또는 터미널을 열고 다음을 실행 합니다.

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

또는 Visual Studio 2017에서 직접 리포지토리를 복제할 수 있습니다.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Windows에서 Azure 서비스 빌드 및 배포

PowerShell 스크립트는 OPC 자격 증명 모음 마이크로 서비스 및 응용 프로그램을 쉽게 배포 하는 방법을 제공 합니다.

1. 리포지토리 루트에서 PowerShell 창을 엽니다. 
3. 배포 폴더로 `cd deploy`이동 합니다.
3. 다른 배포 된 웹 `myResourceGroup` 페이지와 충돌 하지 않을 수 있는 이름을 선택 합니다. 이 문서의 뒷부분에 나오는 "이미 사용 중인 웹 사이트 이름" 섹션을 참조 하세요.
5. 대화형 설치를 사용 `.\deploy.ps1` 하 여 배포를 시작 하거나 전체 명령줄을 입력 합니다.  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. 이 배포를 사용 하 여 개발 하려는 경우를 `-development 1` 추가 하 여 Swagger UI를 사용 하도록 설정 하 고 디버그 빌드를 배포 합니다.
6. 스크립트의 지침에 따라 구독에 로그인 하 고 추가 정보를 제공 합니다.
9. 빌드 및 배포 작업에 성공 하면 다음과 같은 메시지가 표시 됩니다.
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

   > [!NOTE]
   > 문제가 발생 하는 경우이 문서의 뒷부분에 나오는 "배포 실패 문제 해결" 섹션을 참조 하세요.

8. 즐겨 찾는 브라우저를 열고 응용 프로그램 페이지를 엽니다.`https://myResourceGroup.azurewebsites.net`
8. 웹 앱과 OPC 자격 증명 모음 마이크로 서비스을 배포 후 준비 하는 데 몇 분 정도 제공 합니다. 첫 번째 응답을 받을 때까지 웹 홈 페이지를 처음 사용할 때 최대 1 분 동안 중단 될 수 있습니다.
11. Swagger API를 보려면 다음을 엽니다.`https://myResourceGroup-service.azurewebsites.net`
13. Dotnet을 사용 하 여 로컬 GDS 서버를 시작 `.\myResourceGroup-gds.cmd`하려면를 시작 합니다. Docker를 사용 하 `.\myResourceGroup-dockergds.cmd`여 시작 합니다.

정확히 동일한 설정을 사용 하 여 빌드를 다시 배포할 수 있습니다. 이러한 작업은 모든 응용 프로그램 암호를 갱신 하 고 Azure Active Directory (Azure AD) 응용 프로그램 등록의 일부 설정을 다시 설정할 수 있습니다.

웹 앱 이진 파일만 다시 배포할 수 있습니다. 매개 변수 `-onlyBuild 1`를 사용 하 여 서비스의 새 zip 패키지와 앱이 웹 응용 프로그램에 배포 됩니다.

성공적으로 배포 되 면 서비스 사용을 시작할 수 있습니다. [OPC 자격 증명 모음 인증서 관리 서비스 관리를](howto-opc-vault-manage.md)참조 하세요.

## <a name="delete-the-services-from-the-subscription"></a>구독에서 서비스 삭제

방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 서비스가 배포 된 리소스 그룹으로 이동 합니다.
3. **리소스 그룹 삭제**를 선택하고 확인합니다.
4. 잠시 후에는 배포 된 모든 서비스 구성 요소가 삭제 됩니다.
5. **Azure Active Directory** > **앱 등록**로 이동 합니다.
6. 배포 된 각 리소스 그룹에 대해 등록 된 세 가지 등록이 있습니다. 등록의 이름은, `resourcegroup-client` `resourcegroup-module`, `resourcegroup-service`입니다. 각 등록을 개별적으로 삭제 합니다.

이제 배포 된 모든 구성 요소가 제거 됩니다.

## <a name="troubleshooting-deployment-failures"></a>배포 오류 문제 해결

### <a name="resource-group-name"></a>리소스 그룹 이름

짧고 간단한 리소스 그룹 이름을 사용 합니다. 이름은 리소스 이름 및 서비스 URL 접두사에도 사용 됩니다. 따라서 리소스 명명 요구 사항을 준수 해야 합니다.  

### <a name="website-name-already-in-use"></a>웹 사이트 이름이 이미 사용 중입니다.

웹 사이트의 이름을 이미 사용 하 고 있을 수 있습니다. 다른 리소스 그룹 이름을 사용 해야 합니다. 배포 스크립트 https://resourcegroupname.azurewebsites.net 에서 사용 하는 호스트 이름은 및 https://resourgroupname-service.azurewebsites.net 입니다.
서비스의 다른 이름은 짧은 이름 해시를 조합 하 여 작성 되 고 다른 서비스와 충돌 하지 않을 가능성이 있습니다.

### <a name="azure-ad-registration"></a>Azure AD 등록 

배포 스크립트는 Azure AD에 세 개의 Azure AD 응용 프로그램을 등록 하려고 합니다. 선택한 Azure AD 테넌트의 사용 권한에 따라이 작업이 실패할 수 있습니다. 두 가지 옵션이 있습니다.

- 테넌트 목록에서 Azure AD 테넌트를 선택한 경우 스크립트를 다시 시작 하 고 목록에서 다른 항목을 선택 합니다.
- 또는 다른 구독에 개인 Azure AD 테넌트를 배포 합니다. 스크립트를 다시 시작 하 고 사용 하도록 선택 합니다.

## <a name="deployment-script-options"></a>배포 스크립트 옵션

스크립트는 다음 매개 변수를 사용 합니다.


```
-resourceGroupName
```

기존 또는 새 리소스 그룹의 이름일 수 있습니다.

```
-subscriptionId
```


리소스가 배포 되는 구독 ID입니다. 선택 사항입니다.

```
-subscriptionName
```


또는 구독 이름을 사용할 수 있습니다.

```
-resourceGroupLocation
```


리소스 그룹 위치입니다. 지정 된 경우이 매개 변수는이 위치에 새 리소스 그룹을 만들려고 합니다. 이 매개 변수는 선택 사항 이기도 합니다.


```
-tenantId
```


사용할 Azure AD 테넌트입니다. 

```
-development 0|1
```

이는 개발용으로 배포 하기 위한 것입니다. 디버그 빌드를 사용 하 고 ASP.NET 환경을 Development로 설정 합니다. 응용 `.publishsettings` 프로그램 및 서비스를 직접 배포 하는 데 사용할 수 있도록 Visual Studio 2017에서 가져오기에 대 한를 만듭니다. 이 매개 변수는 선택 사항 이기도 합니다.

```
-onlyBuild 0|1
```

이는 웹 앱을 다시 빌드하고 다시 배포 하 고 Docker 컨테이너를 다시 빌드하기 위한 것입니다. 이 매개 변수는 선택 사항 이기도 합니다.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>다음 단계

이제 OPC 자격 증명 모음을 처음부터 배포 하는 방법을 배웠으므로 다음과 같은 작업을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [OPC 자격 증명 모음 관리](howto-opc-vault-manage.md)
