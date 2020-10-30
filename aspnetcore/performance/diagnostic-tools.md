---
title: パフォーマンス診断ツール
author: mjrousos
description: ASP.NET Core アプリのパフォーマンスの問題を診断するための便利なツール。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/diagnostic-tools
ms.openlocfilehash: 71386de232265840f9b825bd33d23bb1d218efc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060860"
---
# <a name="performance-diagnostic-tools"></a>パフォーマンス診断ツール

作成者: [Mike Rousos](https://github.com/mjrousos)

この記事では、ASP.NET Core のパフォーマンスの問題を診断するためのツールの一覧を示します。

## <a name="visual-studio-diagnostic-tools"></a>Visual Studio 診断ツール

Visual Studio に組み込まれている [プロファイリングツールと診断ツール](/visualstudio/profiling) は、パフォーマンスの問題の調査を開始するのに適した場所です。 これらのツールは、Visual Studio 開発環境から使用するのに強力で便利です。 ツールを使用すると、ASP.NET Core アプリの CPU 使用率、メモリ使用量、パフォーマンスイベントを分析できます。 組み込まれているので、開発時にプロファイリングを簡単に行うことができます。

詳細については、 [Visual Studio のドキュメント](/visualstudio/profiling/profiling-overview)を参照してください。

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) は、アプリの詳細なパフォーマンスデータを提供します。 Application Insights は、応答率、エラー率、依存関係の応答時間などのデータを自動的に収集します。 Application Insights は、アプリに固有のカスタムイベントとメトリックのログ記録をサポートしています。

Azure アプリケーション Insights には、監視対象のアプリに関する洞察を提供する複数の方法が用意されています。

- [アプリケーションマップ](/azure/application-insights/app-insights-app-map) –分散アプリのすべてのコンポーネントで、パフォーマンスのボトルネックや障害のホットスポットを見つけるのに役立ちます。
- [Azure メトリックスエクスプローラー](/azure/azure-monitor/platform/metrics-getting-started) は、グラフのプロット、傾向の視覚的な関連付け、およびメトリックの値におけるスパイクと dip の調査を可能にする、Microsoft Azure portal のコンポーネントです。
- [Application Insights ポータルのパフォーマンスブレード](/azure/application-insights/app-insights-tutorial-performance):

  - 監視対象のアプリのさまざまな操作のパフォーマンスの詳細を表示します。
  - 1つの操作をドリルダウンして、長期間に寄与するすべてのパーツ/依存関係を確認できます。
  - Profiler をここから呼び出して、必要に応じてパフォーマンストレースを収集できます。

- [Azure アプリケーション Insights Profiler](/azure/azure-monitor/app/profiler) を使用すると、.net アプリの通常のオンデマンドプロファイリングを行うことができます。  Azure portal は、呼び出し履歴とホットパスを使用してキャプチャされたパフォーマンストレースを表示します。 また、PerfView を使用して詳細な分析を行うために、トレースファイルをダウンロードすることもできます。

Application Insights は、さまざまな環境で使用できます。

- Azure で動作するように最適化されています。
- 運用、開発、およびステージングで機能します。
- [Visual Studio](/azure/application-insights/app-insights-visual-studio)または他のホスト環境からローカルで動作します。

詳細については、「[Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)」を参照してください。

## <a name="perfview"></a>PerfView

[Perfview](https://github.com/Microsoft/perfview) は .net チームによって作成されたパフォーマンス分析ツールで、.net パフォーマンスの問題を診断するためのものです。 PerfView を使用すると、CPU の使用率、メモリと GC の動作、パフォーマンスイベント、およびウォールクロック時間を分析できます。

PerfView の詳細と、 [Perfview のビデオチュートリアル](https://channel9.msdn.com/Series/PerfView-Tutorial) を開始する方法、またはツールまたは [GitHub](https://github.com/Microsoft/perfview)で利用可能なユーザーガイドを参照してください。

## <a name="windows-performance-toolkit"></a>Windows Performance Toolkit

[Windows パフォーマンスツールキット](/windows-hardware/test/wpt/) (WPT) は、Windows パフォーマンスレコーダー (wpr) と Windows performance ANALYZER (WPA) の2つのコンポーネントで構成されています。 これらのツールは、Windows オペレーティングシステムとアプリのパフォーマンスプロファイルを詳細に生成します。 WPT には、データを視覚化するための豊富な方法が用意されていますが、そのデータの収集は PerfView のより強力ではありません。

## <a name="perfcollect"></a>PerfCollect

PerfView は .NET シナリオ用の便利なパフォーマンス分析ツールですが、Windows 上でのみ実行されるため、Linux 環境で実行されている ASP.NET Core アプリからトレースを収集するために使用することはできません。

[Perfcollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) は、ネイティブ linux プロファイリングツール ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) および [LTTng](https://lttng.org/)) を使用して、perfcollect で分析できる linux 上のトレースを収集する bash スクリプトです。 PerfCollect は、Perfcollect を直接使用できない Linux 環境でパフォーマンスの問題が発生した場合に役立ちます。 代わりに、PerfCollect で .NET Core アプリからトレースを収集し、Perfcollect を使用して Windows コンピューターで分析することができます。

PerfCollect をインストールして開始する方法の詳細については、 [GitHub を](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)参照してください。

## <a name="other-third-party-performance-tools"></a>その他のサードパーティ製のパフォーマンスツール

次に、.NET Core アプリケーションのパフォーマンス調査に役立つサードパーティ製のパフォーマンスツールをいくつか示します。

- [MiniProfiler](https://miniprofiler.com/)
- [JetBrains](https://www.jetbrains.com/)からの[Dottrace](https://www.jetbrains.com/profiler/)と[dottrace](https://www.jetbrains.com/dotmemory/)
- Intel からの[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html)
