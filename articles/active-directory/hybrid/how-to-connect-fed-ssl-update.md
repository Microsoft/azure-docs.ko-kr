---
title: Azure AD 연결 - ADFS 팜에 대한 TLS/SSL 인증서 업데이트 | Microsoft Docs
description: 이 문서에서는 Azure AD Connect를 사용하여 AD FS 팜에 대한 TLS/SSL 인증서를 업데이트하는 단계를 자세히 설명합니다.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 451b50e70b98849dfc4654566d09a5a961abe451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279910"
---
# <a name="update-the-tlsssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Active Directory Federation Services 팜에 대한 TLS/SSL 인증서 업데이트

## <a name="overview"></a>개요
이 문서에서는 Azure AD Connect를 사용하여 Active Directory Federation Services 팜에 대한 TLS/SSL 인증서를 업데이트하는 방법에 대해 설명합니다. 선택한 사용자 로그인 방법이 AD FS가 아닌 경우에도 Azure AD Connect 도구를 사용하여 AD FS 팜에 대한 TLS/SSL 인증서를 쉽게 업데이트할 수 있습니다.

간단한 세 단계로 모든 페더레이션 및 WAP(Web Application Proxy) 서버에서 AD FS 팜에 대한 TLS/SSL 인증서를 업데이트하는 전체 작업을 수행할 수 있습니다.

![3단계](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>AD FS에서 사용하는 인증서에 대한 자세한 내용은 [AD FS에서 사용하는 인증서 이해](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc730660(v=ws.11))를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* **AD FS 팜**: AD FS 팜이 Windows Server 2012 R2 이상을 기반으로 하는지 확인합니다.
* **Azure AD Connect**: Azure AD Connect 버전이 1.1.553.0 이상인지 확인합니다. **AD FS SSL 인증서 업데이트** 작업을 사용할 것입니다.

![TLS 업데이트 작업](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>1단계: AD FS 팜 정보 제공

Azure AD Connect는 다음과 같은 방법으로 AD FS 팜에 대한 정보를 자동으로 가져오려고 시도합니다.
1. AD FS(Windows Server 2016 이상)에서 팜 정보 쿼리.
2. Azure AD Connect를 통해 로컬에 저장된 이전 실행의 정보 참조.

표시된 서버 목록은 서버를 추가하거나 제거하여 AD FS 팜의 현재 구성을 반영하도록 수정할 수 있습니다. 서버 정보가 제공되면 Azure AD Connect가 연결 및 현재 TLS/SSL 인증서 상태를 표시합니다.

![AD FS 서버 정보](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

더 이상 AD FS 팜에 포함되지 않는 서버가 목록에 들어 있으면 **제거** 를 클릭하여 AD FS 팜의 서버 목록에서 서버를 삭제합니다.

![목록의 오프라인 서버](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Azure AD Connect에서 AD FS 팜에 대한 서버 목록에서 서버를 제거하는 것은 로컬 작업이며 이 작업을 수행하면 Azure AD Connect가 로컬에서 유지 관리하는 AD FS 팜에 대한 정보가 업데이트됩니다. Azure AD Connect는 변경 내용을 반영하기 위해 AD FS의 구성을 수정하지 않습니다.    

## <a name="step-2-provide-a-new-tlsssl-certificate"></a>2단계: 새 TLS/SSL 인증서 제공

AD FS 팜 서버에 대한 정보가 확인된 후 Azure AD Connect에서 새 TLS/SSL 인증서를 요청합니다. 설치를 계속하려면 암호로 보호된 PFX 인증서를 제공합니다.

![TLS/SSL 인증서](./media/how-to-connect-fed-ssl-update/certificate.png)

인증서를 제공하면 Azure AD Connect가 일련의 필수 조건을 진행합니다. 인증서에서 다음 사항을 확인하여 AD FS 팜의 인증서가 올바른지 확인합니다.

-   인증서의 주체 이름/주체 대체 이름은 페더레이션 서비스 이름과 같거나 와일드카드 인증서입니다.
-   인증서가 30일 이상 유효합니다.
-   인증서 신뢰 체인이 유효합니다.
-   인증서가 암호로 보호됩니다.

## <a name="step-3-select-servers-for-the-update"></a>3단계: 업데이트할 서버 선택

다음 단계에서 TLS/SSL 인증서를 업데이트해야 하는 서버를 선택합니다. 오프라인 상태인 서버는 업데이트 대상으로 선택할 수 없습니다.

![업데이트할 서버 선택](./media/how-to-connect-fed-ssl-update/selectservers.png)

구성이 완료되면 Azure AD Connect는 업데이트 상태를 나타내는 메시지를 표시하고 AD FS 로그인을 확인하는 옵션을 제공합니다.

![구성 완료](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>FAQ

* **새 AD FS TLS/SSL 인증서의 주체 이름은 무엇인가요?**

    Azure AD Connect는 인증서의 주체 이름/대체 주체 이름에 페더레이션 서비스 이름이 포함되어 있는지 확인합니다. 예를 들어 페더레이션 서비스 이름이 fs.contoso.com이면 주체 이름/대체 주체 이름은 fs.contoso.com이어야 합니다.  와일드 카드 인증서도 허용됩니다.

* **WAP 서버 페이지에서 자격 증명이 다시 요청되는 이유는 무엇인가요?**

    AD FS 서버에 연결하기 위해 제공한 자격 증명에 WAP 서버를 관리할 권한이 없으면 Azure AD Connect는 WAP 서버에 대한 관리자 권한이 포함된 자격 증명을 요청합니다.

* **서버가 오프라인으로 표시됩니다. 어떻게 해야 하나요?**

    서버가 오프라인 상태이면 Azure AD Connect가 작업을 수행할 수 없습니다. 서버가 AD FS 팜의 일부이면 서버 연결을 확인하세요. 이 문제를 해결한 후 마법사에서 새로 고침 아이콘을 눌러 상태를 업데이트하세요. 서버가 이전에 팜에 포함되었지만 더 이상 존재하지 않으면 **제거** 를 클릭하여 Azure AD Connect가 유지 관리하는 서버 목록에서 서버를 삭제합니다. Azure AD Connect의 목록에서 서버를 제거해도 AD FS 구성 자체는 변경되지 않습니다. Windows Server 2016 이상에서 AD FS를 사용하는 경우 서버는 구성 설정에 남아 있게 되며 다음에 작업이 실행될 때 표시됩니다.

* **새 TLS/SSL 인증서를 사용하여 팜 서버의 하위 집합을 업데이트할 수 있나요?**

    예. 언제든지 **SSL 인증서 업데이트** 작업을 다시 실행하여 나머지 서버를 업데이트할 수 있습니다. **SSL 인증서 업데이트용 서버 선택** 페이지에서 **SSL 만료 날짜** 를 기준으로 서버 목록을 정렬하면 아직 업데이트되지 않은 서버에 쉽게 액세스할 수 있습니다.

* **이전 실행에서 서버를 제거했지만 여전히 오프라인으로 표시되고 있으며 AD FS 서버 페이지에 표시됩니다. 오프라인 서버가 제거한 후에도 남아 있는 이유는 무엇인가요?**

    Azure AD Connect의 목록에서 서버를 제거해도 AD FS 구성에서는 서버가 제거되지 않습니다. Azure AD Connect는 팜에 대한 정보를 얻기 위해 AD FS(Windows Server 2016 이상)를 참조합니다. 서버가 AD FS 구성에 남아 있으면 해당 서버는 목록에 다시 표시됩니다.  

## <a name="next-steps"></a>다음 단계

- [Azure AD Connect 및 페더레이션](how-to-connect-fed-whatis.md)
- [Azure AD Connect를 사용하여 Active Directory Federation Services 관리 및 사용자 지정](how-to-connect-fed-management.md)