---
title: チュートリアル:NAT ゲートウェイの作成 - Azure portal
titlesuffix: Azure Virtual Network NAT
description: このチュートリアルでは、Azure portal を使用して NAT ゲートウェイを作成および検証する方法について説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 82b5892b027627871e5492e3c6cd3776a923632b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553444"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>チュートリアル:Azure portal を使用した NAT ゲートウェイの作成

このチュートリアルでは、Azure Virtual Network NAT サービスを使用する方法について説明します。 Azure 内の仮想マシンに送信接続を提供する NAT ゲートウェイを作成しましょう。 

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * 仮想ネットワークを作成します。
> * 仮想マシンを作成します。
> * NAT ゲートウェイを作成し、仮想ネットワークに関連付けます。
> * 仮想マシンに接続し、NAT IP アドレスを確認します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="virtual-network"></a>仮想ネットワーク

VM をデプロイして NAT ゲートウェイを使用する前に、リソース グループおよび仮想ネットワークを作成しておく必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 画面の左上で、 **[リソースの作成] > [ネットワーク] > [仮想ネットワーク]** の順に選択するか、検索ボックスで **Virtual network** を検索します。

3. **［作成］** を選択します

4. **[仮想ネットワークの作成]** の **[基本]** タブで次の情報を入力または選択します。

    | **設定**          | **Value**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **プロジェクトの詳細**  |                                                                 |
    | サブスクリプション     | Azure サブスクリプションを選択します。                                  |
    | リソース グループ   | **[新規作成]** を選択します。 </br> 「**myResourceGroupNAT**」と入力します。 </br> **[OK]** を選択します。 |
    | **インスタンスの詳細** |                                                                 |
    | 名前             | 「**myVNet**」と入力します                                    |
    | リージョン           | **[(ヨーロッパ) 西ヨーロッパ]** を選択します |

5. **[IP アドレス]** タブを選択するか、ページの下部にある **[Next: IP Addresses]\(次へ: IP アドレス\)** ボタンを選択します。

6. **[IP アドレス]** タブで、次の情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | IPv4 アドレス空間 | 「**10.1.0.0/16**」と入力します。 |

7. **[+ サブネットの追加]** を選択します。 

8. **[サブネットの編集]** に次の情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | サブネット名 | 「**mySubnet**」と入力します。 |
    | サブネットのアドレス範囲 | 「**10.1.0.0/24**」と入力します。 |

9. **[追加]** を選択します。

10. **[セキュリティ]** タブをクリックします。

11. **[BastionHost]** で **[有効にする]** を選択します。 この情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | 要塞名 | 「**myBastionHost**」と入力します |
    | AzureBastionSubnet のアドレス空間 | 「**10.1.1.0/24**」と入力します |
    | パブリック IP アドレス | **[新規作成]** を選択します。 </br> **[名前]** に「**myBastionIP**」と入力します。 </br> **[OK]** を選択します。 |

12. **[確認と作成]** タブを選択するか、 **[確認と作成]** ボタンを選択します。

13. **［作成］** を選択します

## <a name="nat-gateway"></a>NAT Gateway

1 つまたは複数のパブリック IP アドレス リソース、パブリック IP プレフィックス、またはその両方を使用できます。 パブリック IP リソースと NAT ゲートウェイ リソースを追加します。

1. 画面の左上で、 **[リソースの作成] > [ネットワーク] > [NAT ゲートウェイ]** を選択するか、検索ボックスで "**NAT ゲートウェイ**" を検索します。

2. **［作成］** を選択します 

3. **[ネットワーク アドレス変換 (NAT) ゲートウェイを作成します]** の **[基本]** タブにこの情報を入力または選択します。

    | **設定**          | **Value**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **プロジェクトの詳細**  |                                                                 |
    | サブスクリプション     | Azure サブスクリプションを選択します。                                  |
    | リソース グループ   | **[myResourceGroupNAT]** を選択します。 |
    | **インスタンスの詳細** |                                                                 |
    | 名前             | 「**myNATgateway**」と入力します                                    |
    | リージョン           | **[(ヨーロッパ) 西ヨーロッパ]** を選択します  |
    | 可用性ゾーン | **[なし]** を選択します。 |
    | アイドル タイムアウト (分) | 「**10**」と入力します。 |

4. **[Outbound IP]\(アウトバウンド IP\)** タブを選択するか、ページの下部にある **[Next: Outbound IP]\(次へ: アウトバウンド IP\)** を選択します。

5. **[Outbound IP]\(アウトバウンド IP\)** タブで、次の情報を入力または選択します。

    | **設定** | **Value** |
    | ----------- | --------- |
    | パブリック IP アドレス | **[Create a new public IP address]\(新しいパブリック IP アドレスを作成する\)** を選択します。 </br> **[名前]** に「**myPublicIP**」と入力します。 </br> **[OK]** を選択します。 |

6. **[サブネット]** タブを選択するか、ページの下部にある **[次へ: サブネット]** ボタンを選択します。

7. **[サブネット]** タブで、 **[仮想ネットワーク]** プルダウンから **[myVNet]** を選択します。

8. **[mySubnet]** の横にあるチェック ボックスをオンにします。

9. **[Review + create]\(確認と作成\)** タブを選択するか、ページの下部にある青色の **[Review + create]\(確認と作成\)** ボタンを選択します。

10. **［作成］** を選択します
    
## <a name="virtual-machine"></a>仮想マシン

このセクションでは、NAT ゲートウェイをテストする仮想マシンを作成し、アウトバウンド接続のパブリック IP アドレスを確認します。

1. ポータルの左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[仮想マシン]** を選択します。 

2. **[基本]** タブの **[仮想マシンの作成]** ページで、次の情報を入力または選択します。

    | **設定** | **Value** |
    | ----------- | --------- |
    | **プロジェクトの詳細** |   |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[myResourceGroupNAT]** を選択します。 |
    | **インスタンスの詳細** |   |
    | 仮想マシン名 | 「**myVM**」と入力します。 |
    | リージョン | **[(ヨーロッパ) 西ヨーロッパ]** を選択します。 |
    | 可用性のオプション | 既定値の [no redundancy required]\(冗長性は必要ありません\) のままにします。 |
    | Image | **[Windows Server 2019 Datacenter - Gen1]** を選択します。 |
    | サイズ | **[Standard_DS1_v2]** を選択します。 |
    | **管理者アカウント** |   |
    | ユーザー名 | 仮想マシンのユーザー名を入力します。 |
    | Password | パスワードを入力します。 |
    | パスワードの確認 | パスワードを確認します。 |
    | **受信ポートの規則** |    |
    | パブリック受信ポート | **[なし]** を選択します。 |

3. **[ディスク]** タブを選択するか、ページの下部にある **[次へ: ディスク]** ボタンを選択します。

4. **[ディスク]** タブでは、既定値のままにします。

5. **[ネットワーク]** タブを選択するか、ページの下部にある **[次へ: ネットワーク]** ボタンを選択します。

6. **[ネットワーク]** タブで、次の情報を入力または選択します。

    | **設定** | **Value** |
    | ----------- | --------- |
    | **ネットワーク インターフェイス** |   |
    | 仮想ネットワーク | **[myVNet]** を選択します。 |
    | Subnet | **[mySubnet]** を選択します。 |
    | パブリック IP | **[なし]** を選択します。 |
    | NIC ネットワーク セキュリティ グループ | **[Basic]** を選択します。 |
    | パブリック受信ポート | **[なし]** を選択します。 |

7. **[Review + create]\(確認と作成\)** タブを選択するか、ページの下部にある青色の **[Review + create]\(確認と作成\)** ボタンを選択します。

8. **［作成］** を選択します

## <a name="test-nat-gateway"></a>NAT ゲートウェイをテストする

このセクションでは、NAT ゲートウェイをテストします。 まず、NAT ゲートウェイのパブリック IP を検出します。 次に、テスト仮想マシンに接続し、NAT ゲートウェイ経由のアウトバウンド接続を確認します。
    
1. **[概要]** 画面で、NAT ゲートウェイのパブリック IP アドレスを見つけます。 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** 、 **[myPublicIP]** の順に選択します。

2. パブリック IP アドレスを書き留めておきます。

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="NAT ゲートウェイのパブリック IP アドレスを検出する" border="true":::

3. 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** の順に選択し、リソースの一覧で **[myResourceGroupNAT]** リソース グループにある **[myVM]** を選択します。

4. **[概要]** ページで **[接続]** 、 **[要塞]** の順に選択します。

5. 青色の **[Bastion を使用する]** ボタンを選択します。

6. VM 作成時に入力したユーザー名とパスワードを入力します。

7. **myTestVM** で **Internet Explorer** 開きます。

8. アドレス バーに「 **https://whatsmyip.com** 」と入力します。

9. 表示された IP アドレスが前の手順でメモしておいた NAT ゲートウェイのアドレスと一致していることを確認します。

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer に表示された外部アウトバウンド IP" border="true":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

今後このアプリケーションを使う予定がなければ、次の手順を使用して、仮想ネットワーク、仮想マシン、および NAT ゲートウェイを削除します。

1. 左側のメニューから、 **[リソース グループ]** を選択します。

2. **[myResourceGroupNAT]** リソース グループを選択します。

3. **[リソース グループの削除]** を選択します。

4. 「**myResourceGroupNAT**」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

Azure Virtual Network NAT の詳細については、以下を参照してください。
> [!div class="nextstepaction"]
> [Virtual Network NAT の概要](nat-overview.md)
