---
title: カスタムの Azure Active Directory パスワード保護一覧を構成する
description: このチュートリアルでは、カスタムの Azure Active Directory 禁止パスワード保護一覧を構成して、環境内でよく使用される単語を制限する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89abdcf4c76c92c2ed967594527d323b2cd8be32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96741186"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>チュートリアル:Azure Active Directory のパスワードを保護するためのカスタムの禁止パスワードを構成する

ユーザーは多くの場合、学校、スポーツ チーム、有名人などのありふれたローカル単語を使用してパスワードを作成します。 これらのパスワードは簡単に推測できるため、辞書ベースの攻撃に対しては脆弱です。 自分の組織に強力なパスワードを適用するために、カスタムの Azure Active Directory (Azure AD) 禁止パスワード一覧を使用して、評価およびブロックする特定の文字列を追加できます。 カスタムの禁止パスワードの一覧に一致するものがある場合、パスワードの変更要求はエラーとなります。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * カスタムの禁止パスワードを有効にする
> * カスタムの禁止パスワードの一覧にエントリを追加する
> * 禁止パスワードを使用してパスワードの変更をテストする

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースと特権が必要です。

* 少なくとも Azure AD Premium P1 または試用版ライセンスが有効になっている、動作している Azure AD テナント。
    * 必要に応じて、[無料で作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* "*グローバル管理者*" 特権を持つアカウント。
* パスワードがわかっている管理者以外のユーザー (*testuser* など)。 このチュートリアルでは、このアカウントを使用してパスワード変更イベントをテストします。
    * ユーザーを作成する必要がある場合は、「[クイックスタート: Azure Active Directory に新しいユーザーを追加する](../fundamentals/add-users-azure-active-directory.md)」を参照してください。
    * 禁止パスワードを使用してパスワードの変更操作をテストするには、Azure AD テナントを[セルフサービス パスワード リセット用に構成](tutorial-enable-sspr.md)しておく必要があります。

## <a name="what-are-banned-password-lists"></a>禁止パスワードの一覧とは

Azure AD には、グローバル禁止パスワードの一覧が用意されています。 グローバル禁止パスワードの一覧の内容は、どの外部データ ソースにも基づいていません。 代わりに、グローバル禁止パスワードの一覧は、Azure AD のセキュリティ テレメトリと分析の継続的な結果に基づいています。 ユーザーまたは管理者が自分の資格情報を変更またはリセットしようとすると、希望するパスワードが禁止パスワードの一覧に照らしてチェックされます。 グローバル禁止パスワードの一覧に一致するものがある場合、パスワードの変更要求はエラーとなります。 この既定のグローバル禁止パスワード リストを編集することはできません。

許可されるパスワードの柔軟性を高めるために、カスタムの禁止パスワードの一覧を定義することもできます。 カスタムの禁止パスワードの一覧とグローバル禁止パスワードの一覧を組み合わせて使用することで、自分の組織内に強力なパスワードを適用できます。 カスタムの禁止パスワードの一覧には、次の例のような組織固有の用語を追加できます。

* ブランド名
* 製品名
* 場所 (本社など)
* 会社固有の内部用語
* 会社固有の意味を持つ略語

ユーザーが、パスワードをグローバルまたはカスタムの禁止パスワードの一覧に記載されているものにリセットしようとすると、次のエラー メッセージのいずれかが表示されます。

* "*残念ながら、パスワードには簡単に推測できる単語、語句、パターンが含まれています。別のパスワードでもう一度お試しください。* "
* "*残念ながら、管理者によってブロックされている単語または文字が含まれているためにそのパスワードを使用できません。別のパスワードでもう一度お試しください。* "

カスタムの禁止パスワードの一覧は、最大 1,000 個の用語に制限されています。 多数のパスワードをブロックできるようには設計されていません。 カスタムの禁止パスワードの一覧の利点を最大限に活用するには、[カスタムの禁止パスワードの一覧の概念](concept-password-ban-bad.md#custom-banned-password-list)と[パスワード評価アルゴリズムの概要](concept-password-ban-bad.md#how-are-passwords-evaluated)を確認してください。

## <a name="configure-custom-banned-passwords"></a>カスタムの禁止パスワードを構成する

カスタムの禁止パスワードの一覧を有効にし、いくつかのエントリを追加してみましょう。 カスタムの禁止パスワードの一覧には、いつでも新しいエントリを追加できます。

カスタムの禁止パスワードの一覧を有効にし、エントリを追加するには、次の手順を実行します。

1. "*グローバル管理者*" のアクセス許可を持つアカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. **Azure Active Directory** を検索して選択し、左側のメニューから **[セキュリティ]** を選択します。
1. **[管理]** メニュー ヘッダーで、 **[認証方法]** を選択し、 **[パスワード保護]** を選択します。
1. **[カスタム リストの適用]** オプションを *[はい]* に設定します。
1. **[カスタムの禁止パスワードの一覧]** に文字列 (1 行に 1 文字列) を追加します。 カスタムの禁止パスワードの一覧には、次の考慮事項と制限事項が適用されます。

    * カスタムの禁止パスワードの一覧には、最大 1,000 個の用語を含めることができます。
    * カスタム禁止パスワード リストでは、大文字と小文字は区別されません。
    * カスタムの禁止パスワードの一覧では、一般的な文字の置き換え ("o" と "0" や "a" と "\@" など) が考慮されています。
    * 最小文字数は 4 文字で、最大文字数は 16 文字です。

    次の例に示すように、禁止する独自のカスタム パスワードを指定します

    [ ![Azure portal の [認証方法] でカスタムの禁止パスワード リストを変更する](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png) ](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. **[Windows Server Active Directory のパスワード保護を有効にする]** オプションは、 *[いいえ]* のままにします。
1. カスタムの禁止パスワードとエントリを有効にするには、 **[保存]** を選択します。

カスタム禁止パスワード リストの更新が適用されるまでに数時間かかることがあります。

ハイブリッド環境では、[オンプレミスの環境に Azure AD パスワード保護をデプロイする](howto-password-ban-bad-on-premises-deploy.md)こともできます。 クラウドとオンプレミスの両方のパスワード変更要求に対して、同一のグローバルおよびカスタムの禁止パスワードの一覧が使用されます。

## <a name="test-custom-banned-password-list"></a>カスタムの禁止パスワードの一覧をテストする

カスタムの禁止パスワードの一覧が機能していることを確認するには、パスワードを、前のセクションで追加したパスワードとわずかに異なるものに変更してみます。 Azure AD でパスワードの変更が処理される際に、そのパスワードがカスタムの禁止パスワードの一覧にあるエントリと照合されます。 すると、エラーがユーザーに表示されます。

> [!NOTE]
> ユーザーが Web ベースのポータルで自分のパスワードをリセットできるように、あらかじめ Azure AD テナントを[セルフサービス パスワード リセット用に構成](tutorial-enable-sspr.md)しておく必要があります。 ユーザーは、必要に応じて [https://aka.ms/ssprsetup で SSPR に登録する](https://aka.ms/ssprsetup)ことができます。

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) の **[マイ アプリ]** ページに移動します。
1. 右上隅にあるご自身の名前を選択し、ドロップダウン メニューから **[プロファイル]** を選択します。

    ![プロファイルの選択](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. **[プロファイル]** ページの **[パスワードの変更]** を選択します。
1. **[パスワードの変更]** ページで、既存の (古い) パスワードを入力します。 前のセクションで定義したカスタムの禁止パスワードの一覧にある新しいパスワードを入力して確認し、 **[送信]** を選択します。
1. 次の例に示すように、管理者によってパスワードがブロックされたことを示すエラー メッセージが返されます。

    ![カスタムの禁止パスワードの一覧に含まれるパスワードを使用しようとしたときに表示されるエラー メッセージ](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルの一環として構成したカスタムの禁止パスワードの一覧をもう使用しない場合は、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **Azure Active Directory** を検索して選択し、左側のメニューから **[セキュリティ]** を選択します。
1. **[管理]** メニュー ヘッダーで、 **[認証方法]** を選択し、 **[パスワード保護]** を選択します。
1. **[カスタム リストの適用]** オプションを *[いいえ]* に設定します。
1. カスタムの禁止パスワード構成を更新するには、 **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure AD のカスタムのパスワード保護一覧を有効にし、構成しました。 以下の方法を学習しました。

> [!div class="checklist"]
> * カスタムの禁止パスワードを有効にする
> * カスタムの禁止パスワードの一覧にエントリを追加する
> * 禁止パスワードを使用してパスワードの変更をテストする

> [!div class="nextstepaction"]
> [リスクベースの Azure AD Multi-Factor Authentication を有効にする](./tutorial-enable-azure-mfa.md)