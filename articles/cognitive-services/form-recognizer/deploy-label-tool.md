---
title: 양식 인식기 샘플 레이블 지정 도구를 배포 하는 방법
titleSuffix: Azure Cognitive Services
description: 감독 학습에 도움이 되도록 양식 인식기 샘플 레이블 지정 도구를 배포할 수 있는 다양 한 방법을 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: c7c4e1cc854fdd2fbf03d2274992bbc4a3bb93af
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717900"
---
# <a name="deploy-the-sample-labeling-tool"></a>샘플 레이블 지정 도구 배포

양식 인식기 샘플 레이블 지정 도구는 간단한 UI (사용자 인터페이스)를 제공 하는 응용 프로그램으로, 감독 학습을 목적으로 양식 (문서)에 레이블을 수동으로 지정 하는 데 사용할 수 있습니다. 이 문서에서는 다음을 수행 하는 방법을 설명 하는 링크 및 지침을 제공 합니다.

* [로컬에서 샘플 레이블 도구 실행](#run-the-sample-labeling-tool-locally)
* [샘플 레이블 지정 도구를 ACI (Azure Container Instance)에 배포](#deploy-with-azure-container-instances-aci)
* [을 사용 하 여 오픈 소스 OCR 양식 레이블 지정 도구에 참여](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>로컬에서 샘플 레이블 도구 실행

데이터 레이블을 지정 하는 가장 빠른 방법은 샘플 레이블 도구를 로컬로 실행 하는 것입니다. 다음 퀵 스타트에서는 폼 인식기 REST API와 샘플 레이블 도구를 사용 하 여 수동으로 레이블이 지정 된 데이터를 사용 하 여 사용자 지정 모델을 학습 합니다. 

* [빠른 시작: 샘플 레이블 도구를 사용 하 여 폼에 레이블을 지정 하 고, 모델을 학습 하 고, 폼을 분석](./quickstarts/label-tool.md)합니다.

## <a name="deploy-with-azure-container-instances-aci"></a>Azure Container Instances (ACI)를 사용 하 여 배포

시작 하기 전에 샘플 레이블 도구를 ACI (Azure Container Instance)에 배포 하는 방법에는 두 가지가 있습니다. 두 옵션은 ACI를 사용 하 여 샘플 레이블 지정 도구를 실행 하는 데 사용 됩니다. 

* [Azure Portal 사용](#azure-portal)
* [Azure CLI 사용](#azure-cli)

### <a name="azure-portal"></a>Azure portal

Azure Portal를 사용 하 여 새 리소스를 만들려면 다음 단계를 수행 합니다. 

1. [Azure Portal](https://portal.azure.com/signin/index/)에 로그인합니다.
2. **리소스 만들기**를 선택합니다. 
3. 그런 다음 **웹 앱**을 선택 합니다. 

   > [!div class="mx-imgBorder"]
   > ![웹 앱 선택](./media/quickstarts/formre-create-web-app.png)
   
4. 먼저 **기본 사항** 탭이 선택 되어 있는지 확인 합니다. 이제 몇 가지 정보를 제공 해야 합니다. 

   > [!div class="mx-imgBorder"]
   > ![기본 사항 선택](./media/quickstarts/formre-select-basics.png)
   * 구독-기존 Azure 구독을 선택 합니다.
   * 리소스 그룹-기존 리소스 그룹을 다시 사용 하거나이 프로젝트에 대 한 새 리소스 그룹을 만들 수 있습니다. 새 리소스 그룹을 만드는 것이 좋습니다.
   * 이름-웹 앱에 이름을 지정 합니다. 
   * 게시- **Docker 컨테이너** 선택
   * 운영 체제- **Linux** 선택
   * 지역-사용자에 게 적합 한 지역을 선택 합니다.
   * Linux 요금제-app service에 대 한 가격 책정 계층/계획을 선택 합니다. 

   > [!div class="mx-imgBorder"]
   > ![웹 앱 구성](./media/quickstarts/formre-select-docker-linux.png)

5. 다음으로, **Docker** 탭을 선택 합니다. 

   > [!div class="mx-imgBorder"]
   > ![Docker 선택](./media/quickstarts/formre-select-docker.png)

6. 이제 Docker 컨테이너를 구성 하겠습니다. 다른 설명이 없는 한 모든 필드는 필수입니다.

    # <a name="v20"></a>[v2.0](#tab/v2-0)  
   * 옵션- **단일 컨테이너** 선택
   * 이미지 원본- **개인 레지스트리** 선택 
   * 서버 URL-로 설정 합니다. `https://mcr.microsoft.com`
   * Username (선택 사항)-사용자 이름을 만듭니다. 
   * 암호 (선택 사항)-기억할 보안 암호를 만듭니다.
   * 이미지 및 태그-로 설정 합니다. `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
   * 연속 배포-개발 팀이 샘플 레이블 지정 도구를 변경할 때 자동 업데이트를 받으려면이를 **On** 으로 설정 합니다.
   * 시작 명령-이를로 설정 합니다. `./run.sh eula=accept`

    # <a name="v21-preview"></a>[v 2.1 preview](#tab/v2-1) 
   * 옵션- **단일 컨테이너** 선택
   * 이미지 원본- **개인 레지스트리** 선택 
   * 서버 URL-로 설정 합니다. `https://mcr.microsoft.com`
   * Username (선택 사항)-사용자 이름을 만듭니다. 
   * 암호 (선택 사항)-기억할 보안 암호를 만듭니다.
   * 이미지 및 태그-로 설정 합니다. `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:2.1.012970002-amd64-preview`
   * 연속 배포-개발 팀이 샘플 레이블 지정 도구를 변경할 때 자동 업데이트를 받으려면이를 **On** 으로 설정 합니다.
   * 시작 명령-이를로 설정 합니다. `./run.sh eula=accept`
    
    ---

   > [!div class="mx-imgBorder"]
   > ![Docker 구성](./media/quickstarts/formre-configure-docker.png)

7. 이제 끝났습니다! 그런 다음 **검토 + 만들기**, **만들기** 를 차례로 선택 하 여 웹 앱을 배포 합니다. 완료 되 면 리소스에 대 한 **개요** 에 제공 된 URL에서 웹 앱에 액세스할 수 있습니다.

> [!NOTE]
> 웹 앱을 만들 때 권한 부여/인증을 구성할 수도 있습니다. 시작 하는 데 필요 하지 않습니다. 

### <a name="azure-cli"></a>Azure CLI

Azure Portal를 사용 하는 대신 Azure CLI를 사용 하 여 리소스를 만들 수 있습니다. 계속 하기 전에 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)을 설치 해야 합니다. 이미 Azure CLI 작업 하 고 있는 경우이 단계를 건너뛸 수 있습니다. 

이 명령에 대해 알아야 할 몇 가지 사항이 있습니다.

* `DNS_NAME_LABEL=aci-demo-$RANDOM` 임의의 DNS 이름을 생성 합니다. 
* 이 샘플에서는 리소스를 만드는 데 사용할 수 있는 리소스 그룹이 있다고 가정 합니다. 를 `<resource_group_name>` 구독과 연결 된 유효한 리소스 그룹으로 바꿉니다. 
* 리소스를 만들 위치를 지정 해야 합니다. 을 `<region name>` 웹 앱의 원하는 지역으로 바꿉니다. 
* 이 명령은 자동으로 EULA에 동의 합니다.

Azure CLI에서이 명령을 실행 하 여 샘플 레이블 지정 도구에 대 한 웹 앱 리소스를 만듭니다. 


# <a name="v20"></a>[v2.0](#tab/v2-0)   
```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
``` 
# <a name="v21-preview"></a>[v 2.1 preview](#tab/v2-1)    
```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:2.1.012970002-amd64-preview \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
```

---

### <a name="connect-to-azure-ad-for-authorization"></a>권한 부여를 위해 Azure AD에 연결

Azure Active Directory에 웹 앱을 연결 하는 것이 좋습니다. 이렇게 하면 유효한 자격 증명이 있는 사용자만 로그인 하 여 웹 앱을 사용할 수 있습니다. [App Service 앱 구성](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) 의 지침에 따라 Azure Active Directory에 연결 합니다.

## <a name="open-source-on-github"></a>GitHub의 오픈 소스

OCR 폼 레이블 지정 도구는 GitHub에서 오픈 소스 프로젝트로도 사용할 수 있습니다. 도구는 React + Redux를 사용하여 빌드한 웹 애플리케이션으로, TypeScript로 작성되었습니다. 자세히 알아보거나 기여하려면 [OCR 양식 레이블 지정 도구](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

레이블을 사용 하 여 학습 빠른 시작을 사용 하 여 학습 데이터에 수동으로 레이블을 만들고 감독 [된](./quickstarts/label-tool.md) 학습을 수행 하는 방법을 알아봅니다.
