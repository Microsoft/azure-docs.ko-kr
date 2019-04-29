---
title: PHP용 Azure SDK 다운로드(영문)
description: Azure SDK for PHP를 다운로드하여 설치하는 방법에 대해 알아봅니다.
documentationcenter: php
services: app-service\web
author: allclark
manager: douge
editor: ''
ms.assetid: bac355ac-4c25-42f4-8273-c5112eafa8d4
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 06/01/2016
ms.author: allclark;yaqiyang
ms.openlocfilehash: 4f2c242e27d8a0ed6687eb4a3510bbce6e07694f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457637"
---
# <a name="download-the-azure-sdk-for-php"></a>PHP용 Azure SDK 다운로드

## <a name="overview"></a>개요

PHP용 Azure SDK에는 Azure용 PHP 애플리케이션을 개발, 배포 및 관리할 수 있는 구성 요소가 포함되어 있습니다. 구체적으로 말해서 PHP용 Azure SDK에는 다음이 포함되어 있습니다.

* **Azure용 PHP 클라이언트 라이브러리**. 이러한 클래스 라이브러리는 Azure 기능(예: 데이터 관리 서비스 및 클라우드 서비스)에 액세스하기 위한 인터페이스를 제공합니다.
* **Mac, Linux 및 Windows용 Azure 명령줄 인터페이스(Azure CLI)**. Azure Websites 및 Azure Virtual Machines와 같은 Azure 서비스를 배포 및 관리하기 위한 명령 집합입니다. Azure CLI는 Mac, Linux 및 Windows를 포함한 모든 플랫폼에서 작동합니다.
* **Azure PowerShell(Windows에만 해당)**. Cloud Services 및 Virtual Machines와 같은 Azure 서비스를 배포 및 관리하기 위한 PowerShell cmdlet 집합입니다.
* **Azure 에뮬레이터(Windows에만 해당)**. 컴퓨팅 및 스토리지 에뮬레이터는 애플리케이션을 로컬로 테스트할 수 있는 클라우드 서비스 및 데이터 관리 서비스의 로컬 에뮬레이터입니다. Azure 에뮬레이터는 Windows에서만 실행됩니다.

아래 섹션에서는 위에서 언급한 구성 요소를 다운로드하고 설치하는 방법에 대해 설명합니다.

이 항목의 설명에서는 [PHP][install-php]가 설치되어 있다고 가정합니다.

> [!NOTE]
> Azure용 PHP 클라이언트 라이브러리를 사용하려면 PHP 5.5 이상이 있어야 합니다.
>
>

## <a name="php-client-libraries-for-azure"></a>Azure용 PHP 클라이언트 라이브러리

Azure용 PHP 클라이언트 라이브러리는 운영 체제에서 Azure 기능(예: 데이터 관리 서비스 및 클라우드 서비스)에 액세스하기 위한 인터페이스를 제공합니다. 이러한 라이브러리는 작성기를 통해 설치할 수 있습니다.

Azure용 PHP 클라이언트 라이브러리를 사용하는 방법에 대한 내용은 [Blob Service 사용 방법][blob-service], [Table Service 사용 방법][table-service] 및 [큐 서비스 사용 방법][queue-service]을 참조하세요.

### <a name="install-via-composer"></a>작성기를 통해 설치

1. [Git를 설치합니다][install-git]. Windows에서는 PATH 환경 변수에도 Git 실행 파일을 추가해야 합니다.

2. 프로젝트 루트에 **composer.json** 이라는 파일을 만들고 다음 코드를 추가합니다.

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. 프로젝트 루트에 **[composer.phar][composer-phar]** 을 다운로드합니다.

4. 명령 프롬프트를 열고 프로젝트 루트에서 이 파일을 실행합니다.

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>Azure PowerShell 및 Azure 에뮬레이터

Azure PowerShell는 Azure 서비스(예: Cloud Services 및 Virtual Machines)를 배포 및 관리하기 위한 PowerShell cmdlet 집합입니다. Azure 에뮬레이터는 애플리케이션을 로컬로 테스트할 수 있는 클라우드 서비스 및 데이터 관리 서비스의 에뮬레이터입니다. 이러한 구성 요소는 Windows에서만 지원됩니다.

Azure PowerShell 및 Azure 에뮬레이터는 [Microsoft 웹 플랫폼 설치 관리자][download-wpi]를 사용하여 설치하는 것이 좋습니다. PHP, SQL Server, PHP용 Microsoft Drivers for SQL Server, WebMatrix와 같은 다른 개발 구성 요소를 설치하도록 선택할 수도 있습니다.

Azure PowerShell 사용 방법에 대한 내용은 [Azure PowerShell 사용 방법][powershell-tools]을 참조하세요.

## <a name="azure-cli"></a>Azure CLI

Azure CLI는 Azure Websites 및 Azure Virtual Machines와 같은 Azure 서비스를 배포 및 관리하기 위한 명령 집합입니다. Azure CLI를 설치하는 방법에 대한 자세한 내용은 [Azure CLI 설치](cli-install-nodejs.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [PHP 개발자 센터](https://azure.microsoft.com/develop/php/)를 참조하세요.

[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: https://getcomposer.org/composer.phar
[nodejs-org]: https://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: https://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: https://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: https://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: https://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: https://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: https://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: https://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: https://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
