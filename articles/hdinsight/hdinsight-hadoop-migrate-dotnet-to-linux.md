---
title: Linux 기반 HDInsight에서 Hadoop MapReduce와 함께 .NET 사용 - Azure
description: Linux 기반 HDInsight에서 MapReduce 스트리밍을 위해 .NET 애플리케이션을 사용하는 방법에 대해 알아보세요.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: a1d1488840ca2b17c83f380af4fa24105bb36202
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761161"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Windows 기반 HDInsight용 .NET 솔루션을 Linux 기반 HDInsight로 마이그레이션

Linux 기반 HDInsight 클러스터는 [Mono(https://mono-project.com)](https://mono-project.com)를 사용하여 .NET 애플리케이션을 실행합니다. Mono에서는 MapReduce 애플리케이션 등의 .NET 구성 요소와 Linux 기반 HDInsight를 함께 사용할 수 있습니다. 이 문서에서는 Linux 기반 HDInsight의 Mono와 함께 사용할 수 있도록 Windows 기반 HDInsight 클러스터용으로 만든 .NET 솔루션을 마이그레이션하는 방법에 대해 배웁니다.

## <a name="mono-compatibility-with-net"></a>Mono와 .NET의 호환성

Mono 버전 4.2.1은 HDInsight 버전 3.6에 포함되어 있습니다. HDInsight와 함께 제공되는 Mono 버전에 대한 자세한 내용은 [HDInsight 구성 요소 버전](hdinsight-component-versioning.md)을 참조하세요.

Mono와 .NET 간 호환성에 대한 자세한 내용은 [Mono 호환성(https://www.mono-project.com/docs/about-mono/compatibility/)](https://www.mono-project.com/docs/about-mono/compatibility/) 문서를 참조하세요.

> [!IMPORTANT]  
> SCP.NET 프레임워크는 Mono와 호환됩니다. Mono와 함께 SCP.NET을 사용하는 방법에 대한 자세한 내용은 [Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발](storm/apache-storm-develop-csharp-visual-studio-topology.md)을 참조하세요.

## <a name="automated-portability-analysis"></a>자동 이식성 분석

[.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)를 사용하여 애플리케이션과 Mono 간 비호환성 보고서를 생성할 수 있습니다. 분석기를 구성하여 애플리케이션의 Mono 이식성을 확인하려면 다음 단계를 사용하세요.

1. [.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)를 설치합니다. 설치 중 사용하려는 Visual Studio의 버전을 선택합니다.

2. Visual Studio 2015에서 __Analyze__ > __Portability Analyzer Settings__를 선택한 다음 __Mono__ 섹션에서 __4.5__가 선택되었는지 확인합니다.

    ![분석기 설정의 Mono 섹션에 4.5가 선택되어 있음](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    __확인__을 클릭하여 구성을 저장합니다.

3. __Analyze__ > __Analyze Assembly Portability__를 선택합니다. 솔루션이 포함된 어셈블리를 선택한 다음 __열기__를 선택하여 분석을 시작합니다.

4. 분석이 완료되면 __Analyze__ > __View analysis reports__를 선택합니다. __Portability Analysis Results__에서 __Open report__를 선택하여 보고서를 엽니다.

    ![이식성 분석기 결과 대화 상자](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> 분석기는 솔루션의 모든 문제를 탐지할 수 없습니다. 예를 들어 Mono가 Windows에서 실행 중이면 `c:\temp\file.txt`의 파일 경로가 정상으로 간주됩니다. 동일한 경로가 Linux 플랫폼에서는 유효하지 않습니다.

## <a name="manual-portability-analysis"></a>수동 이식성 분석

[애플리케이션 이식성(https://www.mono-project.com/docs/getting-started/application-portability/)](https://www.mono-project.com/docs/getting-started/application-portability/) 문서의 정보를 사용하여 코드의 수동 감사를 수행합니다.

## <a name="modify-and-build"></a>수정 및 빌드

계속해서 Visual Studio를 사용하여 HDInsight를 위한 .NET 솔루션을 빌드합니다. 하지만 프로젝트가 .NET Framework 4.5를 사용하도록 구성되었는지 확인해야 합니다.

## <a name="deploy-and-test"></a>배포 및 테스트

.NET Portability Analyzer 또는 수동 분석의 권장 사항을 사용하여 솔루션을 수정한 후에는 HDInsight로 테스트해야 합니다. Linux 기반 HDInsight 클러스터에서 솔루션을 테스트할 경우 해결해야 할 사소한 문제가 발견될 수 있습니다. 테스트하는 동안 애플리케이션에서 추가 기록을 활성화하는 것이 좋습니다.

로그에 액세스하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Linux 기반 HDInsight에서 Apache Hadoop YARN 애플리케이션 로그에 액세스](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 MapReduce와 함께 C# 사용](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Apache Hive 및 Apache Pig과 함께 C# 사용자 정의 함수 사용](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [HDInsight에서 Apache Storm용 C# 토폴로지 개발](storm/apache-storm-develop-csharp-visual-studio-topology.md)
