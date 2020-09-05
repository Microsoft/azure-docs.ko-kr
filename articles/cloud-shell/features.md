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
ms.openlocfilehash: ea11dddd83651e233f008197ffa23a3becd978de
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642353"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Azure Cloud Shell에 대한 기능 및 도구

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell은 `Ubuntu 16.04 LTS`에서 실행됩니다.

## <a name="features"></a>기능

### <a name="secure-automatic-authentication"></a>안전한 자동 인증

Cloud Shell은 Azure CLI 및 Azure PowerShell에 대한 계정 액세스를 안전하게 자동으로 인증합니다.

### <a name="home-persistence-across-sessions"></a>여러 세션에 걸친 $HOME 지속성

세션 간에 파일을 유지하기 위해 Cloud Shell에서는 처음 시작할 때 Azure 파일 공유 연결을 안내합니다.
작업이 완료되면 Azure Cloud Shell은 이후의 모든 세션에 대해 스토리지(`$HOME\clouddrive`로 장착됨)를 자동으로 연결합니다.
또한 `$HOME` 디렉터리는 Azure 파일 공유에서 .img로 유지됩니다.
`$HOME` 외부에 있는 파일 및 컴퓨터 상태는 세션 간에 지속 되지 않습니다. SSH 키와 같은 비밀을 저장하는 경우 모범 사례를 사용합니다. [Azure Key Vault와 같은 서비스에는 설치에 대한 자습서가 있습니다](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Cloud Shell에서 파일을 유지하는 방법에 대해 자세히 알아보기](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Azure 드라이브(Azure:)

Cloud Shell PowerShell은 Azure drive ()를 제공 합니다 `Azure:` . 을 사용 하 여 Azure 드라이브로 전환 하 `cd Azure:` 고를 사용 하 여 홈 디렉터리로 돌아갈 수 있습니다 `cd  ~` .
Azure 드라이브는 파일 시스템 탐색과 비슷하게 Compute, 네트워크, Storage 등과 같은 Azure 리소스를 쉽게 검색 및 탐색할 수 있습니다.
사용하는 드라이브에 관계 없이 이러한 리소스를 관리하기 위해 친숙한 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure)을 계속 사용할 수 있습니다.
Azure Portal에서 또는 Azure PowerShell cmdlet을 통해 직접 만들어진 Azure 리소스에 대한 변경 내용은 Azure 드라이브에 반영됩니다.  `dir -Force`를 실행하여 리소스를 새로 고칠 수 있습니다.

![초기화 되는 Azure Cloud Shell의 스크린샷 및 디렉터리 리소스의 목록입니다.](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Exchange Online 관리

Cloud Shell PowerShell에는 Exchange Online 모듈의 개인 빌드가 포함 되어 있습니다.  `Connect-EXOPSSession`을 실행 하 여 Exchange cmdlet을 가져옵니다.

![Connect-EXOPSSession 및 Get 사용자 명령을 실행 하는 Azure Cloud Shell의 스크린샷](media/features-powershell/exchangeonline.png)

 `Get-Command -Module tmp_*`을 실행합니다.
> [!NOTE]
> 모듈 이름은로 시작 해야 하며 `tmp_` , 동일한 접두사를 사용 하 여 모듈을 설치한 경우 해당 cmdlet도 표시 됩니다. 

![명령을 실행 하는 Azure Cloud Shell의 스크린샷 tmp_ *.](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>오픈 소스 도구와 완벽한 통합

Cloud Shell은 Terraform, Ansible, Chef InSpec 같은 오픈 소스 도구를 위해 사전 구성된 인증을 포함하고 있습니다. 예제 연습에서 사용해 보십시오.

## <a name="tools"></a>도구

|범주   |속성   |
|---|---|
|Linux 도구            |bash<br> zsh<br> sh<br> tmux<br> dig<br>               |
|Azure 도구            |[Azure CLI](https://github.com/Azure/azure-cli) 및 [Azure 클래식 CLI](https://github.com/Azure/azure-xplat-cli)<br> [AZCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)<br> [Azure Functions CLI](https://github.com/Azure/azure-functions-core-tools)<br> [Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|텍스트 편집기           |코드(Cloud Shell 편집기)<br> vim<br> nano<br> emacs    |
|원본 제어         |git                    |
|빌드 도구            |make<br> maven<br> npm<br> pip         |
|컨테이너             |[Docker 컴퓨터](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|데이터베이스              |MySQL 클라이언트<br> PostgreSql 클라이언트<br> [sqlcmd 유틸리티](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|기타                  |iPython 클라이언트<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)<br> [퍼핏 볼트](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp 팩](https://www.packer.io/)<br> [Office 365 CLI](https://pnp.github.io/office365-cli/)|

## <a name="language-support"></a>언어 지원

|언어   |버전   |
|---|---|
|.NET Core  |[3.1.302](https://github.com/dotnet/core/blob/master/release-notes/3.1/3.1.6/3.1.302-download.md)       |
|Go         |1.9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 및 3.5(기본값)|

## <a name="next-steps"></a>다음 단계
[Cloud Shell 빠른 시작의 Bash](quickstart.md) <br>
[Cloud Shell 빠른 시작의 PowerShell](quickstart-powershell.md) <br>
[Azure CLI에 대한 자세한 정보](https://docs.microsoft.com/cli/azure/) <br>
[Microsoft Azure PowerShell에 관해 알아보기](https://docs.microsoft.com/powershell/azure/) <br>
