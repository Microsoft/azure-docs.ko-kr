---
title: Azure에서 데이터 과학 환경 설정 - Team Data Science Process
description: 팀 데이터 과학 프로세스에서 사용되는 Azure의 데이터 과학 환경을 설정합니다.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8d8244384abe42819fed61329409e150d334c8d8
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111814310"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>팀 데이터 과학 프로세스에서 사용되는 데이터 과학 환경 설정
팀 데이터 과학 프로세스는 데이터의 스토리지, 처리 및 분석을 위해 다양한 데이터 과학 환경을 사용합니다. 여기에는 Azure Blob Storage, 여러 유형의 Azure 가상 머신, HDInsight(Hadoop) 클러스터 및 Azure Machine Learning 작업 영역이 포함됩니다. 사용할 환경에 대한 결정은 모델링할 데이터의 유형 및 양과 클라우드에서 해당 데이터의 대상에 따라 달라집니다. 

* 이러한 결정을 내릴 때 고려할 질문에 대한 지침은 [Azure Machine Learning 데이터 과학 환경 계획](plan-your-environment.md)을 참조하세요. 
* 고급 분석을 수행할 때 발생할 수 있는 일부 시나리오의 카탈로그는 [팀 데이터 과학 프로세스 시나리오](plan-sample-scenarios.md)

다음 문서에서는 팀 데이터 과학 프로세스에 사용되는 다양한 데이터 과학 환경을 설정하는 방법에 대해 설명합니다.

* [Azure storage-account](../../storage/common/storage-account-create.md)
* HDInsight 클러스터의 R 및 Python
  * [HDInsight의 Spark 소개](../../hdinsight/spark/apache-spark-overview.md)
  * [HDInsight(Hadoop) 클러스터](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)
  * [Jupyter Notebook의 PySpark 커널](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)
  * [HDInsight의 R Server](../../hdinsight/r-server/r-server-overview.md)
  * [HDInsight에서 R 서버 사용 시작](../../hdinsight/r-server/r-server-overview.md)
* [AzureMachine Learning Studio(클래식) 작업 영역](../classic/create-workspace.md)

**Microsoft DSVM(데이터 과학 Virtual Machine)** 은 Azure VM(가상 머신) 이미지로 사용할 수 있습니다. 이 VM은 데이터 분석 및 Machine Learning에 흔히 사용되는 몇 가지 인기 있는 도구로 사전 설치되고 구성됩니다. Windows 및 Linux에는 DSVM를 사용할 수 있습니다. 자세한 내용은 [Linux 및 Windows용 클라우드 기반 데이터 과학 Virtual Machine에 대한 소개](../data-science-virtual-machine/overview.md)를 참조하세요.

다음을 만드는 방법을 알아봅니다.

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/release-notes.md)