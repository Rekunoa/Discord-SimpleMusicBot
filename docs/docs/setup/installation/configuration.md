# ボットの設定について  
本ボットの設定ファイルは、`.env`と`config.json`の2ファイルです。これらに各種設定を記述します。  
各サンプルファイルを参考にして各自で作成してください。

## `.env`ファイル
- [サンプルファイル](https://github.com/mtripg6666tdr/Discord-SimpleMusicBot/blob/master/.env.sample)

こちらには主にトークンなどの認証情報を記述します。  
`.env.sample`がサンプルファイルとなっていますので、コピー＆リネームしてお使いください。  

:::info

なお、`.env`ファイルを使わず、環境変数でも代用することができます。都合の良い方をお使いください。

:::

:::info

replitなど、環境変数の設定方法が特殊な場合もあるため、replitのようなホスティングサービスを使用する際は、利用するサービスのヘルプページをご覧ください。

:::

### `TOKEN`  
  ボットのトークンです。[Discord Developer Portal](https://discord.com/developers/applications)から取得してください。

---
任意設定の設定項目は、以下を開いてください。

<details>
<summary>任意設定の設定項目</summary>

### `CSE_KEY`  
  歌詞検索に使用する[Google Custom Search](https://developers.google.com/custom-search/v1/introduction?hl=ja)のkeyです。(任意指定)

### `DB_URL`  
  「キューやループの有効無効等のデータのバックアップ」に使用するデータベースサーバーのURLです。(任意指定)  
  サーバーの仕様等については[バックアップ](../backup/overview.md)を参照してください。

### `DB_TOKEN`
  「キューやループの有効無効等のデータのバックアップ」に使用するデータベースサーバーのトークンです。(任意指定)  
  サーバーの仕様等については[バックアップ](../backup/overview.md)を参照してください。

### `PORT`
  `config.json`で`webserver`を`true`にした場合に作成されるWebサーバーのポートを指定できます。(任意指定)  
  **複数のボットを同時稼働しており、それぞれでWebサーバーが有効になっている場合、それぞれのボットで違う値を設定する必要があります。**  
  デフォルトでは`8081`が使用されます。

### `LOG_TRANSFER_PORT`
  内部的に、ロガーで使用されるTCPポートの番号を指定します。(任意指定)  
  **複数のボットを同時稼働する際には、それぞれのボットで違う値を設定する必要があります。**  
  デフォルトでは`5003`が使用されます。

### `PO_TOKEN`
  YouTubeの再生に使われるセッション情報を設定します。
### `VISITOR_DATA`
  YouTubeの再生に使われるセッション情報を設定します。

:::info

`PO_TOKEN`と`VISITOR_DATA`の取得方法は、[こちらのガイド（英語）](https://github.com/yt-dlp/yt-dlp/wiki/PO-Token-Guide#no-account)を参照してください。  
代わりに、後述する`TSG_URL`オプションを使用することもできます。

:::

### `TSG_URL`
  YouTubeの再生に使われるセッション情報を取得できるサーバーのURLを設定します。

:::info

  [このプロジェクト](https://github.com/iv-org/youtube-trusted-session-generator)のサーバーを想定した設定です。
  リフレッシュ間隔が長すぎるため、[フォークしたプロジェクト](https://github.com/mtripg6666tdr/youtube-trusted-session-generator)も公開しています。

  このURLに対してHTTP GETリクエストをした際に、以下の形式でセッション情報が返却されるようなサーバーのURLであれば、なんでも指定することが可能です。
```ts
{
  visitor_data: string,
  potoken: string,
}
```

  Docker Compose でボットを設定されている場合、 `compose.yml`にサービスを追加することで簡単にサーバーを設定できます。
  編集の際には、インデントに注意してください。

```yml title="compose.yml"
  # bot や mongo の並びに、 ytsg を追加してください。
  ytsg:
    image: ghcr.io/mtripg6666tdr/youtube-trusted-session-generator:latest
    retart: always
    ports:
      - 8080:8080

  # bot の environments に、以下の行を追加してください。
      - TSG_URL=http://ytsg:8080/token

  # bot の depends_on に、以下の行を追加してください。
      - ytsg
```

※なお、これらの設定をしても、再生が必ず改善する保証なく、変化がない場合もあります。

:::

</details>

## `config.json`ファイル
- [サンプルファイル](https://github.com/mtripg6666tdr/Discord-SimpleMusicBot/blob/master/config.json.sample)

こちらにはボットの設定情報などを記述します。  

:::warning

任意指定の設定に関しては、**値をnullにしてください("null"ではなくnull)**  
例：

```json title="config.json"
{
  "adminId": null
}
```

:::

`config.json.sample`がサンプルファイルとなっていますので、コピー＆リネームしてお使いください。  
(カッコ内は設定値の、TypeScript表記の"型"です。)

### `adminId` (string | string[] | null)  
  管理人のユーザーのIDを指定します。複数の管理人がいる場合は、配列で指定してください。設定しない場合は`null`
### `debug` (boolean)  
  デバッグ用の構成で起動するかを指定します。通常であれば`false`に設定してください。  
  デバッグ用構成を有効にすると、ボットの動作が以下のように変更されます。
  - 出力されるログの量が増えます。
  - 権限がある場合、ログが`logs`フォルダーにファイルとして出力されます。
  - 想定されていないエラーが発生した場合、ボットはクラッシュし、プログラムが終了します。

:::info
このオプションは、`Node.js`の`--inspect`オプションとはまったく別の設定なため、このオプションを使用しても、デバッガーを接続することはできません。
:::

### `maxLogFiles` (number | undefined)
  上の`debug`が`true`に設定されている際に出力されるログファイルの最大数を指定できます。
  設定しないとデフォルトで`100`となります。  
  このプロパティは、省略されても動作するようになっています。

:::note

`debug`プロパティが`false`の場合にはこのオプションに効果はありません。

:::

### `maintenance` (boolean)  
  メンテナンス用の構成で起動するか。メンテナンス用構成では、`adminId`で指定した管理者以外からのコマンドをすべて無視するようになります。

### `errorChannel` (string | null)  
  エラーレポートを送信するテキストチャンネルのID。設定しない場合は`null`

### `prefix` (string | null)  
  指定する場合は一文字でデフォルトプレフィックスを指定してください。  

### `webserver` (boolean)  
  ウェブサーバーを起動するか  

### `bgm` (object)  
  このプロパティを設定することで、自動的にBGMを再生するように構成できます。  
  詳細は、[BGM機能](../feature/bgm)を参照してください。

### `noMessageContent` (boolean)
  Message Content Intentが[Discord Developer Portal](https://discord.com/developers/applications)で無効になっている場合は、`true`に設定してください。  
  デフォルトは`false`です。  

### `twentyFourSeven` (string[])
  参加しているユーザーがボイスチャンネルから退出しても、再生を一時停止しないボイスチャンネルのIDを配列で指定します。  
  詳細は、[24/7再生機能](../feature/4-247.md)を参照してください。  

### `alwaysTwentyFourSeven` (boolean)
  参加しているユーザーがボイスチャンネルから退出しても、再生を一時停止しない場合には`true`に設定します。  
  このオプションが`true`の場合、上の`twentyFourSeven`は常に無視されます。  
  詳細は、[24/7再生機能](../feature/4-247.md)を参照してください。  

### `cacheLevel` ("memory" | "persistent" | "full")
  ボットによる[キャッシュ](../feature/5-cache.md)レベルを設定します。  
  メモリキャッシュ(`"memory"`)、恒常キャッシュ(`"persistent"`)、フルキャッシュ(`"full"`)の順にキャッシュレベルが高くなり、
  キャッシュレベルが高いほど、再生が高速化されます。  
  [`cacheLimit`](#cachelimit-number--undefined)の設定により、キャッシュの容量制限をすることも可能です。

### `cacheLimit` (number | undefined)
  `cacheLevel`が`persistent`の場合に、`./cache`に保存されるキャッシュの最大容量をMB(メガバイト)単位で指定します。  
  プロパティを省略すると、`500`MBとなります。

:::note

`cacheLevel`プロパティが`"memory"`の場合にはこのオプションに効果はありません。

:::

:::info

ボットのキャッシュ機能については、[キャッシュ](../feature/5-cache.md)をご覧ください。

:::

### `defaultLanguage` (string)
  ボットのデフォルトの言語を設定します。使用可能な言語はリポジトリの`locales`フォルダをご確認ください。日本語であれば`"ja"`です。

### `country` (string)
  ボットがメインとする国コードを設定します。日本であれば`"JP"`です。

### `botWhiteList` (string[] | null | undefined)
  本ボットを複数台のボットでホストしている場合、ここにお互いのボットのユーザーIDを指定することで、相互運用がしやすくなります。  
  現時点では、具体的には`インポート`および`再生`コマンドで、お互いのメッセージに反応できるようになります。  
  例えば、`ボットA`で`エクスポート`コマンドで出力したキューを`ボットB`でインポートする、などができるようになります。
  :::warning
  相互運用しているボットは、できるだけ同じバージョンを使用するようにしてください。バージョンが異なる場合、予期しない動作をする可能性があります。
  :::

### `djRoleNames` (string[] | null | undefined)
  本ボットでDJロールとして認識されるロールの名前を変更できます。  
  何も指定しないと`DJ`という名前のロールがDJロールとして認識されます。  
  DJロールがついているユーザーは、他のユーザーより操作できることが多くなります。詳しくは、[コマンド使用時の権限](../command-permissions.md)をご確認ください。

### `showGuildCountStatus` (boolean | undefined)
  ボットが参加しているサーバー数や、ボットが接続しているサーバー数をステータスとして表示するかどうかを指定します。  
  `true`にした場合、ボットのステータスに`2/3 Serversを再生中`（接続中サーバー数/参加サーバー数）などと表示されます。  
  `false`にした場合、ボットのステータスには`音楽を再生中`などと表示されます。  
  デフォルトの設定は`false`です。

<details>
<summary>そのほかの非推奨の設定項目</summary>

:::danger
これらの設定は現在非推奨です。将来のリリースで完全に削除される可能性があります。
:::

### `proxy` (string|null)  
(v4.1から非推奨)
  プロキシを使用する場合はそのURL。設定しない場合は`null`

:::note
一部の音楽ソースの取得に使用されます。
:::

</details>


---

設定はこれで以上となります。
