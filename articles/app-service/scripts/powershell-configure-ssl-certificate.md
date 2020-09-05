---
title: 'PowerShell: TLS/SSL 업로드 및 바인딩'
description: Azure PowerShell을 사용하여 App Service의 배포 및 관리를 자동화하는 방법을 알아봅니다. 이 샘플에서는 사용자 지정 TLS/SSL 인증서를 앱에 바인딩하는 방법을 보여줍니다.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.openlocfilehash: 74bec2e8793331019a6eca986446880c10565aeb
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079951"
---
# <a name="bind-a-custom-tlsssl-certificate-to-a-web-app-using-powershell"></a>PowerShell을 사용하여 웹앱에 사용자 지정 TLS/SSL 인증서 바인딩

이 샘플 스크립트는 해당 관련된 리소스를 사용하여 App Service에서 웹앱을 만든 다음, 사용자 지정 도메인 이름의 TLS/SSL 인증서를 바인딩합니다. 

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Connect-AzAccount`를 실행하여 Azure에 연결합니다. 또한 다음 사항을 확인합니다.

- `az login` 명령을 사용하여 Azure와 연결했습니다.
- 도메인 등록 기관의 DNS 구성 페이지에 액세스할 수 있습니다.
- 업로드하고 바인딩하려는 TLS/SSL 인증서에 사용할 .PFX 파일 및 해당 암호가 유효해야 합니다.

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, 웹앱 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | App Service 계획을 만듭니다. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 웹앱을 만듭니다. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | App Service 계획을 수정하여 해당 가격 책정 계층을 변경합니다. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | 웹앱의 구성을 수정합니다. |
| [New-AzWebAppSSLBinding](/powershell/module/az.websites/new-azwebappsslbinding) | 웹앱에 TLS/SSL 인증서 바인딩을 만듭니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

Azure App Service Web Apps에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../samples-powershell.md)에서 확인할 수 있습니다.
