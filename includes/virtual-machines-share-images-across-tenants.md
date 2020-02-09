---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903695"
---
그러나 Azure 테넌트 외부에서 이미지를 공유 하려는 경우에는 쉽게 공유할 수 있도록 앱 등록을 만들어야 합니다.  앱 등록을 사용 하면 다음과 같은 보다 복잡 한 공유 시나리오를 사용할 수 있습니다. 

* 한 회사가 다른 회사를 획득 하 고 Azure 인프라가 별도의 테넌트에 걸쳐 있는 경우 공유 이미지를 관리 합니다. 
* Azure 파트너는 고객을 대신 하 여 Azure 인프라를 관리 합니다. 이미지 사용자 지정은 파트너 테넌트 내에서 수행 되지만 인프라 배포는 고객의 테넌트에서 수행 됩니다. 


## <a name="create-the-app-registration"></a>앱 등록 만들기

두 테넌트가 모두 이미지 갤러리 리소스를 공유 하는 데 사용할 응용 프로그램 등록을 만듭니다.
1. [Azure Portal에서 앱 등록 (미리 보기)](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/)를 엽니다.    
1. 페이지 맨 위에 있는 메뉴에서 **새 등록** 을 선택 합니다.
1. **이름**에 *myGalleryApp*를 입력 합니다.
1. **지원 되는 계정 유형**에서 **조직 디렉터리와 개인 Microsoft 계정의 계정**을 선택 합니다.
1. **리디렉션 URI**에 *https://www.microsoft.com* 를 입력 한 다음 **등록**을 선택 합니다. 앱 등록을 만든 후 개요 페이지가 열립니다.
1. 개요 페이지에서 **응용 프로그램 (클라이언트) ID** 를 복사 하 고 나중에 사용 하기 위해 저장 합니다.   
1. **인증서 & 암호**를 선택 하 고 **새 클라이언트 암호**를 선택 합니다.
1. **설명**에 *공유 이미지 갤러리 교차 테넌트 앱 암호*를 입력 합니다.
1. **만료**됨에서 기본값인 **1 년** 을 그대로 두고 **추가**를 선택 합니다.
1. 비밀 값을 복사 하 여 안전한 장소에 저장 합니다. 페이지를 나간 후에는 검색할 수 없습니다.


앱 등록에 공유 이미지 갤러리를 사용할 수 있는 권한을 부여 합니다.
1. Azure Portal에서 다른 테넌트와 공유 하려는 공유 이미지 갤러리를 선택 합니다.
1. **액세스 제어 (IAM) 선택**을 선택 하 고 **역할 할당 추가** 에서 *추가*를 선택 합니다. 
1. **역할**아래에서 **판독기**를 선택 합니다.
1. **다음에 대 한 액세스 할당:** 에서이를 **Azure AD 사용자, 그룹 또는 서비스 주체로**그대로 둡니다.
1. **선택**에서 *myGalleryApp* 를 입력 하 고 목록에 표시 되 면 선택 합니다. 완료 되 면 **저장**을 선택 합니다.


## <a name="give-tenant-2-access"></a>테넌트 2 액세스 권한 부여

브라우저를 사용 하 여 로그인을 요청 하 여 테넌트 2에 응용 프로그램 액세스 권한을 부여 합니다. *\<TENANT2 ID >* 을 이미지 갤러리를 공유 하려는 테넌트의 테넌트 id로 바꿉니다. *\<응용 프로그램 (클라이언트) id >* 를 만든 앱 등록의 응용 프로그램 id로 바꿉니다. 대체를 수행한 후 URL을 브라우저에 붙여 넣고 로그인 프롬프트에 따라 테넌트 2에 로그인 합니다.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

[Azure Portal](https://portal.azure.com) 에서 테넌트 2로 로그인 하 고 VM을 만들려는 리소스 그룹에 앱 등록 액세스를 제공 합니다.

1. 리소스 그룹을 선택 하 고 **액세스 제어 (IAM)** 를 선택 합니다. **역할 할당 추가** 에서 **추가**를 선택 합니다. 
1. **역할**아래에서 **참가자**를 입력 합니다.
1. **다음에 대 한 액세스 할당:** 에서이를 **Azure AD 사용자, 그룹 또는 서비스 주체로**그대로 둡니다.
1. 유형 **선택** 에서 *myGalleryApp* 를 선택 하 고 목록에 표시 되 면 선택 합니다. 완료 되 면 **저장**을 선택 합니다.

> [!NOTE]
> 동일한 관리 되는 이미지를 사용 하 여 다른 이미지 버전을 만들려면 먼저 이미지 버전이 빌드되고 복제 될 때까지 기다려야 합니다.

