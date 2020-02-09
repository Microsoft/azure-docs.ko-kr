---
title: Azure 방호 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure 방호 문제를 해결 하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: cherylmc
ms.openlocfilehash: de112ff441bb53a0b3bc7f4ffa4456f1c241682c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512952"
---
# <a name="troubleshoot-azure-bastion"></a>Azure 방호 문제 해결

이 문서에서는 Azure 방호 문제를 해결 하는 방법을 보여 줍니다.

## <a name="nsg"></a>AzureBastionSubnet에서 NSG를 만들 수 없음

**Q:** Azure 방호 서브넷에 NSG를 만들려고 하면 다음과 같은 오류가 발생 합니다. *' 네트워크 보안 그룹 <NSG name>에는 Azure 방호 서브넷 AzureBastionSubnet에 대한 필수 규칙이 없습니다. "라는*오류가 표시 됩니다.

**A:** *AzureBastionSubnet*에 nsg를 만들고 적용 하는 경우 nsg에서 다음 규칙을 추가 했는지 확인 합니다. 이러한 규칙을 추가 하지 않으면 NSG 생성/업데이트가 실패 합니다.

1. 제어 평면 연결-Gmanager의 443에 대한 인바운드
2. 진단 로깅 및 기타 – AzureCloud에서 443에 대한 아웃 바운드 (이 서비스 태그 내의 지역 태그는 아직 지원 되지 않음)
3. 대상 VM – 아웃 바운드 3389 및 22 to VirtualNetwork

NSG 규칙의 예제는 [빠른 시작 템플릿에서](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion)참조할 수 있습니다.
자세한 내용은 [Azure 방호에 대한 Nsg 지침](bastion-nsg.md)을 참조 하세요.

## <a name="sshkey"></a>Azure 방호에서 내 SSH 키를 사용할 수 없음

**Q:** 내 SSH 키 파일을 검색 하려고 하면 다음과 같은 오류가 발생 합니다. *' Ssh 개인 키는-----시작 RSA 개인 키-----으로 시작 해야 하 고-----END RSA 개인 키-----'로 끝납니다*.

**A:** Azure 방호는 현재 시점에서 RSA SSH 키만 지원 합니다. 대상 VM에서 프로 비전 된 공개 키를 사용 하 여 SSH의 RSA 개인 키인 키 파일을 탐색 해야 합니다. 

예를 들어 다음 명령을 사용 하 여 새 RSA SSH 키를 만들 수 있습니다.

**ssh-keygen-t rsa-b 4096-C "email@domain.com"**

출력:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="domain"></a>Windows 도메인에 가입 된 가상 머신에 로그인 할 수 없습니다.

**Q:** 도메인에 가입 된 Windows 가상 머신에 연결할 수 없습니다.

**A:** Azure 방호는 사용자 이름-암호 기반 도메인 로그인에 대해서만 도메인에 가입 된 VM 로그인을 지원 합니다. Azure Portal에서 도메인 자격 증명을 지정 하는 경우 *domain\username* 형식 대신 UPN (username@domain) 형식을 사용 하 여 로그인 합니다. 도메인에 가입 되거나 하이브리드 조인 된 (도메인 가입 및 Azure AD 조인) 가상 컴퓨터에 대해 지원 됩니다. Azure AD에 가입 된 가상 컴퓨터에 대해서는 지원 되지 않습니다.

## <a name="filetransfer"></a>파일 전송 문제

**Q:** Azure 방호에서 파일 전송이 지원 되나요?

**A:** 지금은 파일 전송이 지원 되지 않습니다. 지원을 추가 하기 위해 노력 하 고 있습니다.

## <a name="blackscreen"></a>Azure Portal의 검정 화면

**Q:** Azure 방호를 사용 하 여 연결 하려고 하면 Azure Portal에 검은색 화면이 표시 됩니다.

**A:** 이는 웹 브라우저와 Azure 방호 사이에 네트워크 연결 문제가 있거나 (클라이언트 인터넷 방화벽에서 Websocket 트래픽 또는 유사한 현상이 발생할 수 있음) Azure 방호와 대상 VM 간에 네트워크 연결 문제가 있는 경우에 발생 합니다. 대부분의 경우에는 AzureBastionSubnet에 적용 되는 NSG가 있거나 가상 네트워크에서 RDP/SSH 트래픽을 차단 하는 대상 VM 서브넷에 포함 됩니다. 클라이언트 인터넷 방화벽에서 Websocket 트래픽을 허용 하 고 대상 VM 서브넷의 NSGs를 확인 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [방호 FAQ](bastion-faq.md)를 참조 하세요.