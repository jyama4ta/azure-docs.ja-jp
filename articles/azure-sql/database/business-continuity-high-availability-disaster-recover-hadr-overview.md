---
title: クラウド ビジネス継続性 - データベース復旧
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL Database および SQL Managed Instance でクラウド ビジネス継続性とデータベース復旧がどのようにサポートされ、ミッション クリティカルなクラウド アプリケーションの実行を維持できるようになるかについて説明します。
keywords: ビジネス継続性, クラウド ビジネス継続性, データベースの障害復旧, データベース復旧
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/25/2019
ms.openlocfilehash: 7bd991bd709bb4be69325afe967d7e5600a9e1a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222566"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Azure SQL Database によるビジネス継続性の概要
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database および SQL Managed Instance での **ビジネス継続性** とは、中断 (特にそのコンピューティング インフラストラクチャに対する) が発生した場合でもビジネス活動を続けることができるようにするメカニズム、ポリシー、手順を指します。 ほとんどの場合、SQL Database および SQL Managed Instance では、クラウド環境で発生する可能性がある破壊的なイベントが処理され、アプリケーションとビジネス プロセスの実行が維持されます。 ただし、次のような SQL Database では自動的に対処できない破壊的なイベントがあります。

- ユーザーが誤ってテーブルの行を削除または更新した。
- 悪意のある攻撃者がデータの削除やデータベースの削除に成功した。
- 地震により停電が発生し、データセンターが一時的に利用不可になった。

この概要では、SQL Database および SQL Managed Instance に用意されている事業継続とディザスター リカバリーの機能について説明します。 データ損失につながる、またはデータベースやアプリケーションを使用不能状態に追い込む破壊的なイベントから復旧するためのオプション、推奨事項、およびチュートリアルについて説明します。 ユーザーまたはアプリケーション エラーがデータ整合性に影響を及ぼすとき、Azure リージョンでシステム停止が発生したとき、あるいはアプリケーションにメンテナンスが必要なときの対処方法について説明します。

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>ビジネス継続性を提供するときに使用できる SQL Database の機能

データベースの観点からは、発生する可能性のある 4 つの主要な障害シナリオがあります。

- ディスク ドライブの障害など、データベース ノードに影響するローカルなハードウェアまたはソフトウェアの障害。
- 通常はアプリケーションのバグや人的ミスによって発生するデータの破損または削除。 このような障害はアプリケーション固有であり、通常、データベース サービスでは検出できません。
- 自然災害による可能性がある、データ センターの停止。 このシナリオでは、代替データ センターへのアプリケーションのフェールオーバーを含む何らかのレベルの geo 冗長性が必要です。
- アップグレードまたはメンテナンスのエラー、インフラストラクチャの計画的なメンテナンスまたはアップグレードの間に予期しない問題が発生したときは、以前のデータベースの状態に迅速にロールバックすることが必要な場合があります。

ローカルのハードウェアとソフトウェアの障害を軽減するために、SQL Database には[高可用性アーキテクチャ](high-availability-sla.md)が組み込まれています。これにより、最大で 99.995% の可用性 SLA でこれらの障害からの自動復旧が保証されます。  

SQL Database および SQL Managed Instance では、データ損失からビジネスを守るために、データベースの完全バックアップ (毎週)、データベースの差分バックアップ (12 時間ごと)、およびトランザクション ログのバックアップ (5 分から 10 分ごと) が自動的に作成されます。 バックアップは、すべてのサービス レベルで少なくとも 7 日間 RA-GRS ストレージに保存されます。 Basic を除くすべてのサービス レベルで、ポイントインタイム リストアのために、構成可能なバックアップ保有期間 (最大 35 日間) がサポートされます。

また SQL Database および SQL Managed Instance には、予期しないさまざまなシナリオを軽減するために使用できるビジネス継続性機能がいくつか用意されています。

- [テンポラル テーブル](../temporal-tables.md)では、任意の時点から行バージョンを復元することができます。
- [組み込み自動バックアップ](automated-backups-overview.md)と[ポイントインタイム リストア](recovery-using-backups.md#point-in-time-restore)を使用すると、構成された保有期間 (最大 35 日間) 内の特定の時点にデータベース全体を復元できます。
- **サーバーが削除されていない** 場合は、[削除されたデータベースを削除された時点に復元する](recovery-using-backups.md#deleted-database-restore)ことができます。
- [長期的なバックアップ保有期間](long-term-retention-overview.md)では、バックアップを 10 年間保持することができます。 これは SQL Managed Instance の制限付きパブリック プレビュー段階にあります。
- データ センターの停止またはアプリケーションのアップグレードが発生した場合に、[アクティブ geo レプリケーション](active-geo-replication-overview.md)を使って、読み取り可能レプリカを作成し、手動で任意のレプリカにフェールオーバーできます。
- [自動フェールオーバー グループ](auto-failover-group-overview.md#terminology-and-capabilities)を使用すると、データセンターの機能停止が発生した場合に、アプリケーションを自動的に復旧することができます。

## <a name="recover-a-database-within-the-same-azure-region"></a>同じ Azure リージョン内でデータベースを復旧する

自動データベース バックアップを使用すると、過去の特定の時点にデータベースを復元できます。 この方法で、人的ミスによるデータ破損から復旧できます。 ポイントインタイム リストアにより、破損イベントが発生する前のデータの状態を表す新しいデータベースを同じサーバーに作成できます。 ほとんどのデータベースで、復元操作にかかる時間は 12 時間未満です。 非常に大規模なデータベースや非常にアクティブなデータベースの場合、復旧にはこれよりも長い時間がかかることがあります。 復旧時間について詳しくは、[データベースの復旧時間](recovery-using-backups.md#recovery-time)に関するページをご覧ください。

ポイントインタイム リストア (PITR) のサポートされている最大バックアップ保有期間がアプリケーションにとって十分でない場合は、データベースの長期保有期間 (LTR) ポリシーを構成することで、保有期間を延長できます。 詳細については、「[Long-term backup retention](long-term-retention-overview.md)」(長期バックアップ リテンション) をご覧ください。

## <a name="compare-geo-replication-with-failover-groups"></a>geo レプリケーションとフェールオーバー グループを比較する

[自動フェールオーバー グループ](auto-failover-group-overview.md#terminology-and-capabilities)により、[geo レプリケーション](active-geo-replication-overview.md)のデプロイと使用が簡略化され、次の表に示す追加機能が追加されます。

|                                              | geo レプリケーション | フェールオーバー グループ  |
|:---------------------------------------------| :-------------- | :----------------|
| **Automatic failover**                          |     いいえ          |      はい         |
| **複数のデータベースを同時にフェールオーバーする**  |     いいえ          |      はい         |
| **ユーザーがフェールオーバー後に接続文字列を更新する必要がある**      |     はい         |      いいえ          |
| **SQL Managed Instance のサポート**                   |     いいえ          |      はい         |
| **プライマリと同じリージョンに存在できる**             |     はい         |      いいえ          |
| **複数のレプリカ**                            |     はい         |      いいえ          |
| **読み取りスケールをサポートする**                          |     はい         |      はい         |


## <a name="recover-a-database-to-the-existing-server"></a>既存のサーバーにデータベースを復旧する

まれではありますが、Azure データセンターが停止することもあります。 停止が発生すると、ビジネスが中断します。この中断はわずか数分で解消されることもありますが、数時間に及ぶ場合もあります。

- オプションの 1 つは、データセンターの停止が終了し、データベースがオンラインに戻るのを待つことです。 このオプションは、オフラインのデータベースが許容されるアプリケーションで有効です。 たとえば、常時作業する必要のない開発プロジェクトや無料試用版がこれに該当します。 データセンターが停止したときは、復旧までの時間を予測できないため、このオプションはしばらくデータベースが必要ない場合にのみ有効です。
- もう 1 つのオプションは、[geo 冗長データベース バックアップ](recovery-using-backups.md#geo-restore) (geo リストア) を使用して、任意の Azure リージョン内の任意のサーバーにデータベースを復元する方法です。 geo リストアではソースとして geo 冗長バックアップが使用され、障害によってデータベースまたはデータセンターにアクセスできない場合でも、geo リストアを使用してデータベースを復旧できます。
- 最後に、[アクティブ geo レプリケーション](active-geo-replication-overview.md)を使用した geo セカンダリか、または 1 つまたは複数のデータベースの[自動フェールオーバー グループ](auto-failover-group-overview.md)のどちらかを構成している場合は、障害からすばやく復旧できます。 選択するテクノロジに応じて、手動または自動フェールオーバーを使用できます。 フェールオーバーそのものにかかる時間はほんの数秒ですが、サービスをアクティブにするために少なくとも 1 時間はかかります。 これは、機能停止の規模に見合ったフェールオーバーを確実に行ううえで必要な時間です。 また、非同期レプリケーションの特性上、フェールオーバーによって少量のデータが失われることがあります。

ビジネス継続性計画を開発するときは、破壊的なイベントが発生してから、アプリケーションが完全に復旧するまでの最大許容時間について理解する必要があります。 アプリケーションを完全に復旧するために必要な時間は、目標復旧時間 (RTO) と呼ばれます。 さらに、予期しない破壊的なイベントからの復旧中にアプリケーションが損失を許容できる最大データ更新 (期間) 量についても理解しなければなりません。 潜在的なデータ損失は、目標復旧時点 (RPO) と呼ばれます。

復旧方法によって、提供される RPO と RTO のレベルは異なります。 完全なアプリケーション復旧を達成するために、特定の復旧方法を選択することも、複数の方法を組み合わせて使用することもできます。 次の表で、各復旧オプションの RPO と RTO の比較を示します。 自動フェールオーバー グループにより、geo レプリケーションのデプロイと使用が簡略化され、次の表に示す追加機能が追加されます。

| 復旧方法 | RTO | RPO |
| --- | --- | --- |
| Geo レプリケーション バックアップからの geo リストア | 12 時間 | 1 時間 |
| 自動フェールオーバー グループ | 1 時間 | 5 秒 |
| 手動のデータベース フェールオーバー | 30 秒 | 5 秒 |

> [!NOTE]
> *手動のデータベース フェールオーバー* は、[計画外モード](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities)を使用して、単一データベースをその geo レプリケートされたセカンダリにフェールオーバーすることを指します。
自動フェールオーバーの RTO と RPO について詳しくは、この記事の前出の表を参照してください。

自動フェールオーバー グループは、アプリケーションが次のいずれかの条件を満たす場合に使用します。

- ミッション クリティカルである。
- サービス レベル アグリーメント (SLA) で 12 時間以上のダウンタイムが許可されていない。
- ダウンタイムによって財務責任が発生する。
- データの変更頻度が高く、1 時間分のデータが失われることも許されない。
- アクティブ geo レプリケーションの追加コストが、潜在的な財務責任と関連するビジネス損失を下回る。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

アプリケーションの要件に応じて、データベース バックアップとアクティブ geo レプリケーションを組み合わせて使用できます。 これらのビジネス継続性機能を使用したスタンドアロン データベースおよびエラスティック プールの設計に関する考慮事項については、[クラウド ディザスター リカバリー用のアプリケーション設計](designing-cloud-solutions-for-disaster-recovery.md)に関するページと[エラスティック プールのディザスター リカバリー戦略](disaster-recovery-strategies-for-applications-with-elastic-pool.md)に関するページをご覧ください。

以下のセクションでは、データベース バックアップまたは アクティブ geo レプリケーションのいずれかを使用して復旧する手順の概要について説明します。 要件の計画、復旧後の手順、障害をシミュレートしてディザスター リカバリー訓練を実施する方法など、詳細な手順については、[SQL Database 内のデータベースの障害からの復旧](disaster-recovery-guidance.md)に関するページをご覧ください。

### <a name="prepare-for-an-outage"></a>障害に備える

使用するビジネス継続性機能に関係なく、次の操作を行う必要があります。

- サーバー レベルの IP ファイアウォール規則、ログイン、マスター データベース レベルのアクセス許可など、ターゲット サーバーを特定して準備します。
- クライアントとクライアント アプリケーションを、新しいサーバーにリダイレクトする方法を決めます
- 監査の設定、アラートなど、他の依存関係を文書化します

準備が不十分な状態で、フェールオーバーまたはデータベースの復旧後にアプリケーションをオンラインにすると、余計な時間がかかり、負荷がかかったときにトラブルシューティングが必要になる場合があります。良くない組み合わせです。

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>geo レプリケートされたセカンダリ データベースにフェールオーバーする

復旧メカニズムとしてアクティブ geo レプリケーションまたは自動フェールオーバー グループを使用している場合は、自動フェールオーバー ポリシーを構成するか、または[手動の計画外フェールオーバー](active-geo-replication-configure-portal.md#initiate-a-failover)を使用できます。 いったん開始すると、フェールオーバーによってセカンダリは新しいプライマリになり、新しいトランザクションを記録してクエリに応答できるようになります。失われるのは、レプリケートされなかった最小限のデータだけです。 フェールオーバー プロセスの設計については、[クラウド障害復旧用のアプリケーション設計](designing-cloud-solutions-for-disaster-recovery.md)に関するページをご覧ください。

> [!NOTE]
> データセンターがオンラインに戻ると、古いプライマリは自動的に新しいプライマリに再接続し、セカンダリ データベースになります。 プライマリを元のリージョンに再配置する場合は、計画されたフェールオーバーを手動で開始することができます (フェールバック)。

### <a name="perform-a-geo-restore"></a>geo リストアを実行する

geo 冗長ストレージ (既定で有効) で自動バックアップを使用している場合、データベースは [geo リストア](disaster-recovery-guidance.md#recover-using-geo-restore)を使用して復旧できます。 通常、復旧は 12 時間以内に実行され、最大 1 時間分のデータ損失が発生します。これは、最後のログ バックアップが実行およびレプリケートされたタイミングによって決まります。 復旧処理が完了するまで、データベースは、トランザクションを記録したり、クエリに応答したりすることはできません。 なお、geo リストアでは最後の使用可能な一時点までしかデータベースを復元できません。

> [!NOTE]
> 復旧されたデータベースにアプリケーションを切り替える前にデータセンターがオンラインに戻った場合は、復旧をキャンセルすることができます。

### <a name="perform-post-failover--recovery-tasks"></a>フェールオーバー後のタスク/復旧タスクを実行する

復旧にどちらのメカニズムを使ったとしても、ユーザーおよびアプリケーションの動作を元に戻す前に、次の追加タスクを実行する必要があります。

- クライアントとクライアント アプリケーションを、新しいサーバーおよび復元されたデータベースにリダイレクトする。
- ユーザーが接続できるように、適切なサーバー レベルの IP ファイアウォール規則が適用されていることを確認する。または、[データベース レベルのファイアウォール](firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)を使用して、適切な規則を有効にする。
- 適切なログインとマスター データベース レベルのアクセス許可が適切に指定されていることを確保する (または [包含ユーザー](/sql/relational-databases/security/contained-database-users-making-your-database-portable)を使用する)。
- 必要に応じて、監査を構成する。
- 必要に応じて、アラートを構成する。

> [!NOTE]
> フェールオーバー グループを使用している場合で、かつ読み取り/書き込みリスナーを使用してデータベースに接続している場合、フェールオーバー後のリダイレクトは、自動的かつアプリケーションに対して透過的に実行されます。

## <a name="upgrade-an-application-with-minimal-downtime"></a>最小のダウンタイムでアプリケーションをアップグレードする

アプリケーションのアップグレードなど、計画されたメンテナンスのために、アプリケーションをオフラインにしなければならないことがあります。 [アプリケーション アップグレードの管理](manage-application-rolling-upgrade.md) に関するページでは、アクティブ geo レプリケーションを使用してクラウド アプリケーションのローリング アップグレードを有効化し、アップグレード中のダウンタイムを最小限に抑え、問題が発生した場合の復旧パスを提供する方法について説明します。

## <a name="next-steps"></a>次のステップ

単一データベースおよびエラスティック プール用アプリケーション設計に関する考慮事項については、[クラウド ディザスター リカバリー用のアプリケーション設計](designing-cloud-solutions-for-disaster-recovery.md)に関するページと、[エラスティック プールのディザスター リカバリー戦略](disaster-recovery-strategies-for-applications-with-elastic-pool.md)に関するページをご覧ください。
