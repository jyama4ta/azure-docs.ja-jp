---
title: 会話の文字起こし (プレビュー) - 音声サービス
titleSuffix: Azure Cognitive Services
description: 会話の文字起こしは、会議のためのソリューションであり、認識、スピーカー ID、ダイアライゼーションを組み合わせて、会話の文字起こしが提供されます。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: c27493748381a3aced92aab41745f352f228c50e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95024995"
---
# <a name="what-is-conversation-transcription-in-meetings-preview"></a>会議での会話の文字起こしとは (プレビュー)

会話の文字起こしとは、音声認識、話者識別、および各話者の文の属性 (_ダイアライゼーション_ とも呼ばれる) を組み合わせて、任意の会話のリアルタイムまたは非同期 (あるいはその両方) の文字起こしを提供する [音声テキスト変換](speech-to-text.md) ソリューションです。 会話の文字起こしでは、会話の話者を区別して、誰がいつ何を発言したのかを特定します。また、開発者はアプリケーションに音声テキスト変換を簡単に追加して、複数話者のダイアライゼーションを実行できます。

## <a name="key-features"></a>主要な機能

- **タイムスタンプ** - 各話者の発話にはタイムスタンプがあるため、語句が話された時間が簡単に分かります。
- **読み取り可能なトランスクリプト** - トランスクリプトには、話された内容にテキストがほぼ一致するように、書式設定や句読点が自動的に追加されます。
- **ユーザー プロファイル** - ユーザー プロファイルは、ユーザーの音声サンプルを収集して署名生成のために送信することによって生成されます。
- **話者識別** - 話者はユーザー プロファイルを使用して識別され、_話者 ID_ がそれぞれに割り当てられます。
- **複数話者のダイアライゼーション** - 各話者 ID でオーディオ ストリームを合成することによって、誰が何を言ったのかを判別します。
- **リアルタイムの文字起こし** - 会話の最中に誰がいつ何を言ったのかをライブで文字起こしします。
- **非同期の文字起こし** - マルチチャンネル オーディオ ストリームを使用して、より精度の高い文字起こしを提供します。

> [!NOTE]
> 会話の文字起こしでは、会話の話者数に制限を設けていませんが、セッションあたり 2 から 10 人の話者に最適化されています。

## <a name="get-started"></a>はじめに

作業を開始するには、リアルタイムでの会話の文字起こしの[クイックスタート](how-to-use-conversation-transcription.md)をご覧ください。

## <a name="use-cases"></a>ユース ケース

聴覚障碍により耳が聞こえない参加者など、すべての人に対して会議をインクルーシブなものにするには、リアルタイムでの文字起こしを行うことが重要です。 リアルタイム モードの会話の文字起こしは、会議の音声を取得し、誰が何を言っているのかを判別します。これにより、会議のすべての参加者がトランスクリプトを読み、遅れをとらずに会議に参加できるようになります。

### <a name="improved-efficiency"></a>効率性の向上

会議の参加者は、会話の文字起こしにメモを取るのを任せて、会議に集中できます。 参加者は、メモを取ることで会議中に何かを聞き逃したりすることなく、積極的に会議に参加し、次のステップにすばやく進むことができます。

## <a name="how-it-works"></a>しくみ

ここに、会話の文字起こしのしくみの概要を示します。

![会話の文字起こしの入力の図](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>想定される入力

- **マルチチャンネル オーディオ ストリーム** - 仕様と設計の詳細については、[Microsoft Speech Device SDK マイク](./speech-devices-sdk-microphone.md)に関するページを参照してください。 開発キットの詳細および購入については、[Microsoft Speech Device SDK の入手](./get-speech-devices-sdk.md)に関するページを参照してください。
- **ユーザーの音声サンプル** - 会話の文字起こしには、会話の前にユーザー プロファイルが必要です。 各ユーザーからオーディオ録音を収集し、[署名生成サービス](https://aka.ms/cts/signaturegenservice)にその録音を送信して、音声を検証し、ユーザー プロファイルを生成する必要があります。

> [!NOTE]
> ユーザーの音声サンプルは省略可能です。 この入力がない場合、文字起こしでは複数の異なる話者が示されますが、事前登録されている特定の話者名を認識する代わりに、「Speaker1」、「Speaker2」などと表示されます。


## <a name="real-time-vs-asynchronous"></a>リアルタイムと非同期

会話の文字起こしには、3 つの文字起こしモードがあります。

### <a name="real-time"></a>リアルタイム

オーディオ データがライブで処理され、話者 ID とトランスクリプトが返されます。 文字起こしソリューションで、進行中の会話のトランスクリプトをライブで表示する必要がある場合は、このモードを選択します。 たとえば、聴覚障害により耳が聞こえない参加者に対して、会議をより参加しやすいものにするためにアプリケーションを構築する場合などが、リアルタイムでの文字起こしの最適なユースケースです。

### <a name="asynchronous"></a>非同期

オーディオ データがバッチ処理されて、話者 ID とトランスクリプトが返されます。 文字起こしソリューションで、トランスクリプトのライブ表示ではなく、より高い精度が求められる場合にこのモードを選択します。 たとえば、会議の参加者が欠席した会議の内容を簡単に把握できるようにするためにアプリケーションを構築する場合は、非同期の文字起こしモードを使用して、精度の高い文字起こしの結果を得られるようにします。

### <a name="real-time-plus-asynchronous"></a>リアルタイム + 非同期

オーディオ データはライブで処理され、話者 ID とトランスクリプトが返されます。さらに、非同期処理による精度の高いトランスクリプトも取得するための要求が作成されます。 アプリケーションで、リアルタイムの文字起こしを必要とし、さらに、会話や会議が行われた後で使用するためにより精度の高いトランスクリプトが求められる場合は、このモードを選択します。

## <a name="language-support"></a>言語のサポート

現在、会話の文字起こしでは、次のリージョンの[すべての音声テキスト変換言語](language-support.md#speech-to-text)がサポートされています:  `centralus`、`eastasia`、`eastus`、`westeurope`。 その他のロケールのサポートが必要な場合は、[会話の文字起こし機能の担当者](mailto:CTSFeatureCrew@microsoft.com)にお問い合わせください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [リアルタイムで会話を文字起こしする](how-to-use-conversation-transcription.md)