---
title: Azure SQL Data Warehouse를 이용한 Striim 빠른 시작 | Microsoft Docs
description: Striim 및 Azure SQL Data Warehouse로 빨리 시작합니다.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: a835fbd427408afae9ea6ae1b1bc2a84c18b0c34
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475250"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Striim Azure SQL DW Marketplace 제품 설치 안내서

이 빠른 SQL Data Warehouse의 기존 인스턴스가 이미 있다고 가정합니다.

Azure Marketplace에서 Striim을 검색하고 Striim for Data Integration to SQL Data Warehouse (Staged)(SQL Data Warehouse로의 데이터 통합용 Striim(미리 구성)) 옵션을 선택합니다. 

![Striim 설치][install]

지정된 속성으로 Striim VM을 구성하고 Striim 클러스터 이름, 암호 및 관리자 암호를 기록해 둡니다.

![Striim 구성][configure]

배포 되 면 클릭 \<VM 이름 >-Connect를 클릭 하 고 VM 로컬 계정을 사용 하 여 로그인을 복사 하는 Azure portal에서 masternode 

![SQL Data Warehouse에 Striim 연결][connect]

<https://www.microsoft.com/en-us/download/details.aspx?id=54671>의 sqljdbc42.jar을 로컬 머신에 다운로드합니다. 

명령줄 창을 열고 JDBC jar를 다운로드한 위치로 디렉터리를 변경합니다. SCP를 통해 jar 파일을 Striim VM으로 전송하고 Azure Portal에서 주소 및 암호 가져오기

![VM에 jar 파일 복사][copy-jar]

다른 명령줄 창을 열거나 ssh 유틸리티를 사용하여 Striim 클러스터에 대해 ssh를 수행합니다.

![클러스터에 대해 SSH 수행][ssh]

다음 명령을 실행하여 JDBC jar 파일을 Striim의 lib 디렉터리로 이동하고 서버를 시작했다가 중지합니다.

   1. sudo su
   2. cd /tmp
   3. mv sqljdbc42.jar /opt/striim/lib
   4. systemctl stop striim-node
   5. systemctl stop striim-dbms
   6. systemctl start striim-dbms
   7. systemctl start striim-node

![Striim 클러스터 시작][start-striim]

이제 선호 하는 브라우저를 열고 이동할 \<DNS 이름 >: 9080

![로그인 화면으로 이동][navigate]

Azure Portal에서 설정한 사용자 이름과 암호로 로그인하고 원하는 마법사를 선택하여 시작하거나 앱 페이지로 끌어서 놓기 UI를 사용합니다.

![서버 자격 증명으로 로그인][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
