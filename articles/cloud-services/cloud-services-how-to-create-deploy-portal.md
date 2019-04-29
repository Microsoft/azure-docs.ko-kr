---
title: 클라우드 서비스를 만들고 배포하는 방법 | Microsoft Docs
description: Azure 포털을 사용하여 클라우드 서비스를 만들고 배포하는 방법을 알아봅니다.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 56ea2f14-34a2-4ed9-857c-82be4c9d0579
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeconnoc
ms.openlocfilehash: a6cf2276da463f71f008c4bfb6eee4c232b18308
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61433761"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>클라우드 서비스를 만들고 배포하는 방법
Azure portal에는 두 가지 방법으로 클라우드 서비스 만들기 및 배포를 제공 합니다. *빠른 생성* 하 고 *사용자 지정 만들기*합니다.

이 문서는 빠른 생성 방법을 사용하여 새 클라우드 서비스를 만든 다음 **업로드** 를 사용하여 Azure에서 클라우드 서비스 패키지를 업로드하고 배포하는 방법에 대해 설명합니다. 이 방법을 사용하는 경우 작업을 진행하면서 모든 요구 사항을 완료하는 데 사용할 수 있는 편리한 링크를 Azure 포털에서 제공합니다. 클라우드 서비스를 만들 때 배포할 준비가 되면 사용자 지정 만들기를 사용하여 동시에 둘 다를 수행할 수 있습니다.

> [!NOTE]
> Azure DevOps에서 클라우드 서비스를 게시하려는 경우 빠른 생성을 사용한 다음 Azure 빠른 시작 또는 대시보드에서 Azure DevOps 게시를 설정합니다. 자세한 내용은 [Continuous Delivery to Azure by Using Azure DevOps][TFSTutorialForCloudService](Azure DevOps를 사용하여 Azure에 지속적으로 전송)를 참조하거나 **빠른 시작** 페이지에 대한 도움말을 참조하세요.
>
>

## <a name="concepts"></a>개념
Azure에서 애플리케이션을 클라우드 서비스로 배포하려면 다음과 같은 세 가지 구성 요소가 필요합니다.

* **서비스 정의**  
   클라우드 서비스 정의 파일(.csdef)은 역할 수를 포함하여 서비스 모델을 정의합니다.
* **서비스 구성**  
   클라우드 서비스 구성 파일(.cscfg)은 역할 인스턴스 수를 포함하여 클라우드 서비스 및 개별 역할에 대한 구성 설정을 제공합니다.
* **서비스 패키지**  
   서비스 패키지(.cspkg)에는 애플리케이션 코드와 구성 및 서비스 정의 파일이 포함됩니다.

이러한 구성 요소에 대한 자세한 내용과 패키지를 만드는 방법은 [여기](cloud-services-model-and-package.md)에서 볼 수 있습니다.

## <a name="prepare-your-app"></a>앱 준비
클라우드 서비스를 배포하려면 먼저 애플리케이션 코드 및 클라우드 서비스 구성 파일(.cscfg)에서 클라우드 서비스 패키지(.cspkg)를 만들어야 합니다. Azure SDK는 필요한 배포 파일을 준비하는 도구를 제공합니다. [Azure 다운로드](https://azure.microsoft.com/downloads/) 페이지에서 애플리케이션 코드를 개발하려는 언어로 SDK를 설치할 수 있습니다.

세 가지 클라우드 서비스 기능은 서비스 패키지를 내보내기 전에 특별히 구성해야 합니다.

* 데이터 암호화에 SSL(Secure Sockets Layer)을 사용하는 클라우드 서비스를 배포하려는 경우 SSL에 맞게 [애플리케이션을 구성](cloud-services-configure-ssl-certificate-portal.md#modify)합니다.
* 역할 인스턴스에 대한 원격 데스크톱 연결을 구성하려면 원격 데스크톱에 대한 [역할을 구성](cloud-services-role-enable-remote-desktop-new-portal.md) 합니다.
* 클라우드 서비스에 대해 자세한 모니터링을 구성하려면 클라우드 서비스에 Azure Diagnostics를 사용하도록 설정합니다. *최소 모니터링* (기본 모니터링 수준)에서는 역할 인스턴스(가상 머신)에 대해 호스트 운영 체제에서 수집된 성능 카운터를 사용합니다. *세부 정보 표시 모니터링* 에서는 역할 인스턴스 내 성능 데이터를 기반으로 추가 메트릭을 수집하여 애플리케이션 처리 중 발생하는 문제를 보다 자세히 분석할 수 있습니다. Azure Diagnostics를 사용하도록 설정하는 방법에 대해 알아보려면 [Azure에서 진단 사용](cloud-services-dotnet-diagnostics.md)을 참조하세요.

웹 역할 또는 작업자 역할 배포를 통해 클라우드 서비스를 만들려면 [서비스 패키지를 만들어야](cloud-services-model-and-package.md#servicepackagecspkg)합니다.

## <a name="before-you-begin"></a>시작하기 전에
* Azure SDK를 설치하지 않은 경우 **Azure SDK 설치** 를 클릭하여 [Azure 다운로드 페이지](https://azure.microsoft.com/downloads/)를 열고 코드를 개발하려는 언어의 SDK를 다운로드합니다. 이 작업은 나중에 수행할 수 있습니다.
* 역할 인스턴스에 인증서가 필요한 경우 인증서를 만듭니다. 클라우드 서비스에는 개인 키가 포함된 .pfx 파일이 필요합니다. 클라우드 서비스를 만들고 배포할 때 Azure에 인증서를 업로드할 수 있습니다.

## <a name="create-and-deploy"></a>만들기 및 배포
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **리소스 만들기 > Compute**를 클릭한 다음, 아래로 스크롤하여 **클라우드 서비스**를 클릭합니다.

    ![클라우드 서비스 게시](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. 새 **클라우드 서비스** 창에서 **DNS 이름**의 값을 입력합니다.
4. 새 **리소스 그룹** 을 만들거나 기존 항목을 선택합니다.
5. **위치**를 선택합니다.
6. **패키지**를 클릭합니다. **패키지 업로드** 창에서 열립니다. 필수 필드를 입력합니다. 역할에 단일 인스턴스가 포함된 경우 **단일 인스턴스가 포함된 역할이 하나 이상 있는 경우에도 배포합니다.** 가 선택되어 있어야 합니다.
7. **배포 시작** 이 선택되어 있는지 확인합니다.
8. **확인**을 클릭하면 **패키지 업로드** 창이 닫힙니다.
9. 추가할 인증서가 없는 경우 **만들기**를 클릭합니다.

    ![클라우드 서비스 게시](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>인증서 업로드
배포 패키지가 [인증서를 사용하도록 구성되었으면](cloud-services-configure-ssl-certificate-portal.md#modify)이제 인증서를 업로드할 수 있습니다.

1. **인증서**를 선택하고 **인증서 추가** 창에서 SSL 인증서 .pfx 파일을 선택한 다음, 인증서에 대한 **암호**를 제공합니다.
2. **인증서 첨부**를 클릭한 다음, **인증서 추가** 창에서 **확인**을 클릭합니다.
3. **클라우드 서비스** 창에서 **만들기**를 클릭합니다. 배포가 **준비** 상태에 도달하면 다음 단계로 진행할 수 있습니다.

    ![클라우드 서비스 게시](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>배포가 완료되었는지 확인
1. 클라우드 서비스 인스턴스를 클릭합니다.

    서비스가 **실행 중**상태로 표시됩니다.
2. **Essentials**에서 **사이트 URL**을 클릭하여 웹 브라우저에서 클라우드 서비스를 엽니다.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: https://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>다음 단계
* [클라우드 서비스의 일반 구성](cloud-services-how-to-configure-portal.md)
* [사용자 지정 도메인 이름](cloud-services-custom-domain-name-portal.md)구성
* [클라우드 서비스를 관리합니다](cloud-services-how-to-manage-portal.md).
* [SSL 인증서](cloud-services-configure-ssl-certificate-portal.md)구성
