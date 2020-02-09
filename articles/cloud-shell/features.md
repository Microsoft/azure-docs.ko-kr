---
title: Azure Cloud Shell 기능 | Microsoft Docs
description: Azure Cloud Shell의 기능 개요
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: damaerte
ms.openlocfilehash: 60832f9438a602945c63910a436d7638f15a201d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969437"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Azure Cloud Shell 용 기능 & 도구

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

`Ubuntu 16.04 LTS`에서 Azure Cloud Shell 실행 됩니다.

## <a name="features"></a>Jellemzők

### <a name="secure-automatic-authentication"></a>보안 자동 인증

Cloud Shell는 Azure CLI 및 Azure PowerShell에 대한 계정 액세스를 안전 하 고 자동으로 인증 합니다.

### <a name="home-persistence-across-sessions"></a>세션 간에 지 속성 $HOME

세션 간에 파일을 유지 하기 위해 처음 시작할 때 Azure 파일 공유를 연결 하는 과정을 안내 Cloud Shell 합니다.
완료 되 면 Cloud Shell는 이후 모든 세션에 대한 저장소 (`$HOME\clouddrive`으로 탑재 됨)를 자동으로 연결 합니다.
또한 `$HOME` 디렉터리는 Azure 파일 공유에. i m g로 유지 됩니다.
`$HOME` 이외의 파일 및 컴퓨터 상태는 세션 간에 지속 되지 않습니다. SSH 키와 같은 암호를 저장할 때 모범 사례를 사용 합니다. Azure Key Vault와 같은 서비스 [에는 설치 프로그램에 대한 자습서가 있습니다](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Cloud Shell에서 파일 유지에 대해 자세히 알아보세요.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Azure 드라이브 (Azure:)

Cloud Shell PowerShell은 Azure drive (`Azure:`)에서 시작 합니다.
Azure 드라이브를 사용 하면 파일 시스템 탐색과 유사 하 게 계산, 네트워크, 저장소 등의 Azure 리소스를 쉽게 검색 하 고 탐색할 수 있습니다.
사용자의 드라이브에 관계 없이 친숙 한 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure) 을 사용 하 여 이러한 리소스를 관리할 수 있습니다.
Azure Portal에서 직접 또는 Azure PowerShell cmdlet을 통해 Azure 리소스에 대한 모든 변경 내용은 Azure 드라이브에 반영 됩니다.  `dir -Force`를 실행 하 여 리소스를 새로 고칠 수 있습니다.

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Exchange Online 관리

Cloud Shell PowerShell에는 Exchange Online 모듈의 개인 빌드가 포함 되어 있습니다.  `Connect-EXOPSSession`를 실행 하 여 Exchange cmdlet을 가져옵니다.

![](media/features-powershell/exchangeonline.png)

 Futtassa a `Get-Command -Module tmp_*` parancsot.
> [!NOTE]
> 모듈 이름은 `tmp_`시작 해야 하며, 동일한 접두사를 사용 하 여 모듈을 설치한 경우 해당 cmdlet도 표시 됩니다. 

![](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>오픈 소스 도구와 긴밀 하 게 통합

Cloud Shell는 Terraform, Ansible 및 Chef InSpec와 같은 오픈 소스 도구에 대해 미리 구성 된 인증을 포함 합니다. 예제 연습에서 사용해 보세요.

## <a name="tools"></a>Eszközök

|Kategória   |Név   |
|---|---|
|Linux 도구            |bash<br> zsh<br> Sh<br> tmux<br> 살펴보고<br>               |
|Azure 도구            |[Azure CLI](https://github.com/Azure/azure-cli) 및 [Azure 클래식 CLI](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#writing-your-first-azcopy-command)<br> [CLI Azure Functions](https://github.com/Azure/azure-functions-core-tools)<br> [Service Fabric parancssori felület](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|텍스트 편집기           |코드 (Cloud Shell 편집기)<br> vim<br> nano<br> emacs    |
|Verziókövetés         |git                    |
|빌드 도구            |make<br> maven<br> npm<br> 주사위         |
|Containers             |[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [름](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|Adatbázis-kezelés              |MySQL 클라이언트<br> PostgreSql 클라이언트<br> [sqlcmd 유틸리티](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-스크립터](https://github.com/Microsoft/sql-xplat-cli) |
|Egyéb                  |iPython 클라이언트<br> [CLI Cloud Foundry](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)<br> [퍼핏 볼트](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp 팩](https://www.packer.io/)|

## <a name="language-support"></a>Nyelvi támogatás

|Nyelv   |Verzió   |
|---|---|
|.NET Core  |2.2.402       |
|Indítás         |1.9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[6.2.3](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 및 3.5 (기본값)|

## <a name="next-steps"></a>Következő lépések
[Cloud Shell 빠른 시작의 Bash](quickstart.md) <br>
[Cloud Shell 빠른 시작의 PowerShell](quickstart-powershell.md) <br>
[Azure CLI에 대해 알아보기](https://docs.microsoft.com/cli/azure/) <br>
[Azure PowerShell에 대해 알아보기](https://docs.microsoft.com/powershell/azure/) <br>
