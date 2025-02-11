---
title: 時間でパーティション分割されたファイル名に基づいて新しいファイルを増分コピーする
description: Azure データ ファクトリを作成してから、データのコピー ツールを使用して、時間でパーティション分割されたファイル名のみに基づく新しいファイルを増分的に読み込みます。
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: aa6c0d9035f172cc7fb520ff58f233a82fca228a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741074"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>データのコピー ツールを使用することにより、パーティション分割されたファイル名に基づく新しいファイルを増分コピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

このチュートリアルでは、Azure Portal を使用してデータ ファクトリを作成します。 次に、時間でパーティション分割されたファイル名に基づく新しいファイルを Azure Blob Storage から Azure Blob Storage に増分コピーするパイプラインを、データのコピー ツールを使用して作成します。

> [!NOTE]
> Azure Data Factory を初めて使用する場合は、「[Azure Data Factory の概要](introduction.md)」を参照してください。

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * データのコピー ツールを使用してパイプラインを作成します。
> * パイプラインとアクティビティの実行を監視します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* **Azure ストレージ アカウント**:Blob Storage を "_ソース_" および "_シンク_" データ ストアとして使用します。 Azure ストレージ アカウントがない場合は、「[ストレージ アカウントの作成](../storage/common/storage-account-create.md)」の手順をご覧ください。

### <a name="create-two-containers-in-blob-storage"></a>Blob ストレージに 2 つのコンテナーを作成する

次の手順を行って、チュートリアルで使用する Blob ストレージを準備します。

1. **source** という名前のコンテナーを作成します。  ご自分のコンテナー内に、フォルダー パスを **2020/03/17/03** として作成します。 空のテキスト ファイルを作成し、**file1.txt** という名前を付けます。 ご利用のストレージ アカウント内のフォルダー パス **source/2020/03/17/03** に file1.txt をアップロードします。  この作業は、[Azure Storage Explorer](https://storageexplorer.com/) をはじめとするさまざまなツールを使用して実行できます。

    ![[ファイルのアップロード]](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > フォルダー名は、UTC 時間に合わせて調整してください。  たとえば、現在の UTC 時間が 2020 年 3 月 17 日午前 3 時 38 分である場合は、**source/{Year}/{Month}/{Day}/{Hour}/** のルールに従って、**source/2020/03/17/03/** としてフォルダー パスを作成できます。

2. **destination** という名前のコンテナーを作成します。 この作業は、[Azure Storage Explorer](https://storageexplorer.com/) をはじめとするさまざまなツールを使用して実行できます。

## <a name="create-a-data-factory"></a>Data Factory の作成

1. 左側のメニューで、 **[リソースの作成]**  >  **[統合]**  >  **[Data Factory]** を選択します。

   ![[新規] ウィンドウでの [Data Factory] の選択](./media/doc-common-process/new-azure-data-factory-menu.png)

2. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。

    データ ファクトリの名前は "_グローバルに一意_" にする必要があります。 次のエラー メッセージが表示される場合があります。

   ![[新しいデータ ファクトリ] のエラー メッセージ](./media/doc-common-process/name-not-available-error.png)

   データ ファクトリの名前の値に関するエラー メッセージが表示された場合は、別の名前を入力してください。 たとえば、_**yourname**_**ADFTutorialDataFactory** という名前を使用します。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事をご覧ください。
3. 新しいデータ ファクトリの作成先となる Azure **サブスクリプション** を選択します。
4. **[リソース グループ]** で、次の手順のいずれかを行います。

    a. **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。

    b. **[新規作成]** を選択し、リソース グループの名前を入力します。 
         
    リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。

5. **[バージョン]** で、バージョンとして **[V2]** を選択します。
6. **[場所]** で、データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストア (Azure Storage、SQL Database など) やコンピューティング (Azure HDInsight など) は、他の場所やリージョンに存在していてもかまいません。
7. **［作成］** を選択します
8. 作成が完了すると、 **[Data Factory]** ホーム ページが表示されます。
9. 別のタブで Azure Data Factory ユーザー インターフェイス (UI) を起動するには、 **[Author & Monitor]\(作成と監視\)** タイルを選択します。

    ![データ ファクトリのホーム ページ](./media/doc-common-process/data-factory-home-page.png)


## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>データのコピー ツールを使用してパイプラインを作成する

1. **[Let's get started]\(始めましょう\)** ページで、 **[データのコピー]** タイルを選択してデータのコピー ツールを起動します。

   ![データのコピー ツールのタイル](./media/doc-common-process/get-started-page.png)

2. **[プロパティ]** ページで、次の手順を行います。

    a. **[タスク名]** に「**DeltaCopyFromBlobPipeline**」と入力します。

    b. **[Task cadence or Task schedule]\(タスクの周期またはスケジュール\)** で、 **[Run regularly on schedule]\(スケジュールに従って定期的に実行する\)** を選択します。

    c. **[Trigger type]\(トリガーの種類\)** で、 **[Tumbling Window]\(タンブリング ウィンドウ\)** を選択します。

    d. **[Recurrence]\(繰り返し\)** で、**1 時間** と指定します。

    e. **[次へ]** を選択します。

    指定したタスク名のパイプラインが Data Factory UI によって作成されます。

    ![[プロパティ] ページ](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. **[ソース データ ストア]** ページで、次の手順を実行します。

    a. **[+ 新しい接続の作成]** をクリックして、接続を追加します
    
    b. ギャラリーから [Azure Blob Storage] を選び、[続行] を選択します。
    
    c. **[新しいリンクされたサービス (Azure Blob Storage)]** ページで、リンクされたサービスの名前を入力します。 Azure サブスクリプションを選択し、 **[ストレージ アカウント名]** 一覧からご利用のストレージ アカウントを選びます。 接続をテストし、 **[作成]** を選択します。

    ![[ソース データ ストア] ページ](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. **[ソース データ ストア]** ページで新しく作成したリンクされたサービスを選択し、 **[次へ]** をクリックします。

4. **[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\)** ページで、次の手順を行います。

    a. **source** コンテナーを選択してから、 **[選択]** を選択します。

    ![スクリーンショットに、[入力ファイルまたはフォルダーの選択] ダイアログ ボックスが示されています。](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. **[File loading behavior]\(ファイル読み込み動作\)** で、 **[Incremental load: time-partitioned folder/file names]\(段階的に読み込み: 時間でパーティション分割されたフォルダー/ファイル名\)** を選択します。

    c. 動的フォルダー パスを **source/{year}/{month}/{day}/{hour}/** として書き込み、次のスクリーンショットに示すように形式を変更します。 **[Binary copy]\(バイナリ コピー\)** をオンにして、 **[次へ]** をクリックします。

    ![スクリーンショットに、フォルダーが選択されている [入力ファイルまたはフォルダーの選択] ダイアログ ボックスが示されています。](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. **[Destination data store]\(コピー先データ ストア\)** ページで、データ コピー元ストアと同じストレージ アカウントである **AzureBlobStorage** を選択し、 **[次へ]** をクリックします。

    ![[Destination data store]\(コピー先データ ストア\) ページ](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. **[Choose the output file or folder]\(出力ファイルまたはフォルダーの選択\)** ページで、次の手順を行います。

    a. **destination** フォルダーを参照して選択し、 **[選択]** をクリックします。

    b. 動的フォルダー パスを **destination/{year}/{month}/{day}/{hour}/** として書き込み、形式を次のように変更します。

    ![スクリーンショットに、[出力ファイルまたはフォルダーの選択] ダイアログボックスが示されています。](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    c. **[次へ]** をクリックします。

    ![スクリーンショットに、[次へ] が選択されている [出力ファイルまたはフォルダーの選択] ダイアログ ボックスが示されています。](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. **[設定]** ページで **[次へ]** を選択します。

8. **[サマリー]** ページで設定を確認し、 **[次へ]** を選択します。

    ![概要ページ](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. **[Deployment]\(デプロイ\)** ページで **[監視]** を選択してパイプライン (タスク) を監視します。
    ![[Deployment]\(デプロイ\) ページ](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. 左側の **[監視]** タブが自動的に選択されたことがわかります。  パイプラインの実行が自動的にトリガーされるまで待機する必要があります (約 1 時間後)。 実行時に、パイプライン名のリンク **DeltaCopyFromBlobPipeline** をクリックしてアクティビティの実行の詳細を表示するか、パイプラインを再実行します。 **[最新の情報に更新]** を選択して、一覧を更新します。

    ![スクリーンショットに、[パイプライン実行] ウィンドウが示されています。](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs-1.png)
11. パイプライン内のアクティビティ (コピー アクティビティ) は 1 つだけなので、エントリは 1 つのみです。 **[ソース]** および **[ターゲット]** 列の列幅を (必要に応じて) 調整し、詳細が表示されるようにすると、ソース ファイル (file1.txt) が *source/2020/03/17/03/* から、同じファイル名の *destination/2020/03/17/03/* にコピーされていることがわかります。 

    ![スクリーンショットに、パイプライン実行の詳細が示されています。](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Azure Storage Explorer (https://storageexplorer.com/) ) を使用してファイルをスキャンしても、同じことを確認できます。

    ![スクリーンショットに、宛先のパイプライン実行の詳細が示されています。](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. 別の空のテキスト ファイルを作成し、**file2.txt** という新しい名前を付けます。 ご利用のストレージ アカウント内のフォルダー パス **source/2020/03/17/04** に、file2.txt ファイルをアップロードします。 この作業は、[Azure Storage Explorer](https://storageexplorer.com/) をはじめとするさまざまなツールを使用して実行できます。

    > [!NOTE]
    > 新しいフォルダー パスを作成する必要がないか注意してください。 フォルダー名は、UTC 時間に合わせて調整してください。  たとえば、現在の UTC 時間が 2020 年 3 月 17 日午前 4 時 20 分である場合は、**source/{Year}/{Month}/{Day}/{Hour}/** のルールに従って、**source/2020/03/17/04/** としてフォルダー パスを作成できます。

13. **[パイプラインの実行]** ビューに戻るには、 **[All Pipelines Runs]\(すべてのパイプライン実行\)** を選択し、さらに 1 時間後に同じパイプラインが再び自動的にトリガーされるまで待ちます。  

    ![スクリーンショットに、そのページに戻るための [すべてのパイプライン実行] リンクが示されています。](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. 2 つ目のパイプライン実行については、新しい **DeltaCopyFromBlobPipeline** を選択し、同じことを行って詳細を確認します。 ソース ファイル (file2.txt) が **source/2020/03/17/04/** から、同じファイル名の **destination/2020/03/17/04/** にコピーされたことがわかります。 Azure Storage Explorer (https://storageexplorer.com/) ) を使用して **destination** コンテナー内でファイルをスキャンしても、同じことを確認できます。


## <a name="next-steps"></a>次のステップ
次のチュートリアルに進み、Azure 上の Spark クラスターを使ってデータを変換する方法について学習しましょう。

> [!div class="nextstepaction"]
>[クラウド内の Spark クラスターを使用してデータを変換する](tutorial-transform-data-spark-portal.md)
