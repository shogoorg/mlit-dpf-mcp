# MLIT DATA PLATFORM MCP Server

## 目次
- [MLIT DATA PLATFORM MCP Server](#mlit-data-platform-mcp-server)
  - [目次](#目次)
  - [1. 概要](#1-概要)
  - [2. 主な機能](#2-主な機能)
  - [3. 動作環境](#3-動作環境)
  - [4. インストールとセットアップ](#4-インストールとセットアップ)
    - [前提条件](#前提条件)
    - [手順](#手順)
  - [5. ディレクトリ構成](#5-ディレクトリ構成)
  - [6. ライセンス](#6-ライセンス)
  - [7. 注意事項](#7-注意事項)
  - [8. お問い合わせ](#8-お問い合わせ)


## 1. 概要
国土交通省が保有するデータと民間等のデータを連携し、一元的に検索・表示・ダウンロードを可能にする[国土交通データプラットフォーム](https://data-platform.mlit.go.jp/)が提供する利用者向けAPIと接続するMCP (Model Context Protocol) サーバー（α版）です。

本MCPサーバーを利用することで、大規模言語モデル（LLM）と直接連携し、対話形式で直感的にデータを検索・取得することが可能になります。APIに関する専門的な知識がなくても、誰でも簡単に国土交通データプラットフォームから曖昧な指示や複雑な条件設定でデータを検索・取得が可能な、新しいデータ活用のかたちを提供します。


## 2. 主な機能と代表プロンプト一覧

国土交通データプラットフォームの利用者向けAPIを活用し、以下の18の機能と対話型プロンプトを提供します：

### 1. Search系（複数対象・範囲検索）

1. **検索 (Search)**
   * 「**＜さいたま市役所、藤沢市役所、京都市役所、舞鶴市役所＞周辺**の**＜建築物、住所＞**」
   * *(English: "Buildings and addresses around <Saitama City Hall, Fujisawa City Hall, Kyoto City Hall, Maizuru City Hall>")*

2. **位置矩形による検索 (Search by Location Rectangle)**
   * ①「**＜さいたま市役所、藤沢市役所、京都市役所、舞鶴市役所＞から＜浦和駅、藤沢駅、京都駅、東舞鶴駅＞にかけてのエリア**の**＜建築物、住所＞**」
   * *(English: "Buildings and addresses in the area spanning from <Saitama City Hall, Fujisawa City Hall, Kyoto City Hall, Maizuru City Hall> to <Urawa Station, Fujisawa Station, Kyoto Station, Higashi-Maizuru Station>")*
   * ②（緯度経度指定時）「**＜さいたま市役所、藤沢市役所、京都市役所、舞鶴市役所＞周辺**の緯度経度範囲＜**北緯35.85〜35.87、東経139.64〜139.66**＞の**＜建築物、住所＞**」
   * *(English: "Buildings and addresses within coordinate bounding box <Lat 35.85-35.87, Lon 139.64-139.66> around <Saitama City Hall, Fujisawa City Hall, Kyoto City Hall, Maizuru City Hall>")*

3. **位置地点と距離による検索 (Search by Location Point Distance)**
   * 「**＜さいたま市役所、藤沢市役所、京都市役所、舞鶴市役所＞周辺**から半径＜**1km以内**＞の**＜建築物、住所＞**」
   * *(English: "Buildings and addresses within <1km radius> around <Saitama City Hall, Fujisawa City Hall, Kyoto City Hall, Maizuru City Hall>")*

4. **属性による検索 (Search by Attribute)**
   * ①「**＜さいたま市浦和区、藤沢市、京都市中京区、舞鶴市＞**の**＜建築物、住所＞**」
   * *(English: "Buildings and addresses in <Urawa Ward (Saitama), Fujisawa City, Nakagyo Ward (Kyoto), Maizuru City>")*
   * ②（施設種別指定時）「**＜さいたま市、藤沢市、京都市、舞鶴市＞**の＜**公共施設（庁舎、学校、避難施設）**＞の**建築物**」
   * *(English: "<Public facilities (city halls, schools, evacuation shelters)> in <Saitama City, Fujisawa City, Kyoto City, Maizuru City>")*

---

### 2. Get系（単一対象・詳細取得）

5. **データ取得 (Get Data)**
   * 「**＜さいたま市役所、藤沢市役所、京都市役所、舞鶴市役所＞**の**＜建築物、住所＞ さらに詳しく**」
   * *(English: "<buildings, addresses> of <Saitama City Hall, Fujisawa City Hall, Kyoto City Hall, Maizuru City Hall> Learn more")*

6. **データサマリー取得 (Get Data Summary)**
   * 「**＜さいたま市役所、藤沢市役所、京都市役所、舞鶴市役所＞**の**＜建築物、住所＞の基本情報 さらに詳しく**」
   * *(English: "Basic information on <buildings, addresses> of <Saitama City Hall, Fujisawa City Hall, Kyoto City Hall, Maizuru City Hall> Learn more")*

7. **データカタログ取得 (Get Data Catalog)**
   * 「**＜さいたま市、藤沢市、京都市、舞鶴市＞**の**データセット（カテゴリー） さらに詳しく**」
   * *(English: "Datasets (categories) of <Saitama City, Fujisawa City, Kyoto City, Maizuru City> Learn more")*

8. **データカタログサマリー取得 (Get Data Catalog Summary)**
   * 「**＜さいたま市、藤沢市、京都市、舞鶴市＞**の**データセット（カテゴリー）のサマリー さらに詳しく**」
   * *(English: "Summary of datasets (categories) of <Saitama City, Fujisawa City, Kyoto City, Maizuru City> Learn more")*

9. **ファイルダウンロードURL取得 (Get File Download URLs)**
   * 「**＜さいたま市役所、藤沢市役所、京都市役所、舞鶴市役所＞**の**＜建築物、住所＞のダウンロードURL さらに詳しく**」
   * *(English: "Download URLs for <buildings, addresses> of <Saitama City Hall, Fujisawa City Hall, Kyoto City Hall, Maizuru City Hall> Learn more")*

10. **ZIPファイルダウンロードURL取得 (Get Zipfile Download URL)**
    * 「**＜さいたま市役所、藤沢市役所、京都市役所、舞鶴市役所＞**の**＜建築物、住所＞のZIPダウンロードURL さらに詳しく**」
    * *(English: "ZIP download URL for <buildings, addresses> of <Saitama City Hall, Fujisawa City Hall, Kyoto City Hall, Maizuru City Hall> Learn more")*

11. **サムネイルURL取得 (Get Thumbnail URLs)**
    * 「**＜さいたま市役所、藤沢市役所、京都市役所、舞鶴市役所＞**の**建築物のサムネイルURL さらに詳しく**」
    * *(English: "Thumbnail URLs for buildings of <Saitama City Hall, Fujisawa City Hall, Kyoto City Hall, Maizuru City Hall> Learn more")*

12. **全データ取得 (Get All Data)**
    * 「**＜さいたま市、藤沢市、京都市、舞鶴市＞**の**＜建築物、住所＞の全件データ さらに詳しく**」
    * *(English: "All data records for <buildings, addresses> of <Saitama City, Fujisawa City, Kyoto City, Maizuru City> Learn more")*

13. **カウントデータ取得 (Get Count Data)**
    * 「**＜さいたま市、藤沢市、京都市、舞鶴市＞**の**＜建築物、住所＞の登録件数 さらに詳しく**」
    * *(English: "Record counts of <buildings, addresses> in <Saitama City, Fujisawa City, Kyoto City, Maizuru City> Learn more")*

14. **サジェスト取得 (Get Suggest)**
    * 「『**＜さいたま市役所、藤沢市役所、京都市役所、舞鶴市役所＞**』の**＜建築物、住所＞のサジェスト候補 さらに詳しく**」
    * *(English: "Search suggestions for <buildings, addresses> of '<Saitama City Hall, Fujisawa City Hall, Kyoto City Hall, Maizuru City Hall>' Learn more")*

15. **都道府県データ取得 (Get Prefecture Data)**
    * 「**＜埼玉県、神奈川県、京都府＞**の**都道府県情報 さらに詳しく**」
    * *(English: "Prefecture information for <Saitama, Kanagawa, Kyoto> Learn more")*

16. **市区町村データ取得 (Get Municipality Data)**
    * 「**＜さいたま市、藤沢市、京都市、舞鶴市＞**の**市区町村情報 さらに詳しく**」
    * *(English: "Municipality information for <Saitama City, Fujisawa City, Kyoto City, Maizuru City> Learn more")*

17. **メッシュ取得 (Get Mesh)**
    * ①「**＜さいたま市役所、藤沢市役所、京都市役所、舞鶴市役所＞がある＜1kmメッシュ（地域区画）＞**の**＜建築物、住所＞ さらに詳しく**」
    * *(English: "<buildings, addresses> in the <1km regional mesh grid> of <Saitama City Hall, Fujisawa City Hall, Kyoto City Hall, Maizuru City Hall> Learn more")*
    * ②（メッシュコード指定時）「地域メッシュコード＜**53394523**＞内の**＜建築物、住所＞ さらに詳しく**」
    * *(English: "<buildings, addresses> within regional mesh code <53394523> Learn more")*

18. **コード正規化 (Normalize Codes)**
    * 「**＜埼玉県さいたま市、神奈川県藤沢市、京都府京都市、京都府舞鶴市＞**の**都道府県名と市区町村名 正規化**」
    * *(English: "Normalize prefecture and municipality names of <Saitama City (Saitama), Fujisawa City (Kanagawa), Kyoto City (Kyoto), Maizuru City (Kyoto)>")*

---

### 3. 統合系（自律エージェント・探索＆深掘り）

* **抽象バージョン (Abstract Baseline)**:
  * 「**＜さいたま市役所、藤沢市役所、京都市役所、舞鶴市役所＞周辺**の**＜データセット、建築物、住所＞ さらに詳しく**」
  * *(English: "<datasets, buildings, addresses> around <Saitama City Hall, Fujisawa City Hall, Kyoto City Hall, Maizuru City Hall> Learn more")*

* **具体バージョン (Concrete Scenario: 洪水データ・避難所・住所)**:
  * 「**＜さいたま市役所、藤沢市役所、京都市役所、舞鶴市役所＞周辺**の**＜洪水データ、避難所、住所＞ さらに詳しく**」
  * *(English: "<flood data, evacuation shelters, addresses> around <Saitama City Hall, Fujisawa City Hall, Kyoto City Hall, Maizuru City Hall> Learn more")*


## 3. 動作環境

* OS：Windows 10 / 11 または macOS 13以降
* MCPホスト：Claude Desktopなど
* MCPサーバー実行環境：Python 3.10+
* メモリ：8GB以上推奨
* ストレージ：空き容量 1GB以上（キャッシュやログを含む）

## 4. インストールとセットアップ

### 前提条件
Claude DesktopなどのMCP対応AIアプリケーション および Python がインストールされていることを前提としています。以下は、Claude Desktopでの利用を想定した手順です。

### 手順

1. **国土交通データプラットフォームでアカウントを作成し、APIキーを取得**
   
   詳しい手順は、[こちら](https://data-platform.mlit.go.jp/api_docs/usage/introduction.html)をご覧ください。

2. **リポジトリをクローン**

   ```bash
   git clone https://github.com/MLIT-DATA-PLATFORM/mlit-dpf-mcp.git
   cd mlit-dpf-mcp
   ```

3. **仮想環境を作成 & 有効化**

   ```bash
   python -m venv .venv
   .venv\Scripts\activate      # Windows
   source .venv/bin/activate   # macOS/Linux
   ```

4. **依存ライブラリをインストール**

   ```bash
   pip install -e .
   pip install aiohttp pydantic tenacity python-json-logger mcp python-dotenv
   ```

5. **環境変数を設定**

   `.env.example`をコピーし、 `.env` ファイルを作成します：

   ```
   MLIT_API_KEY=your_api_key_here
   MLIT_BASE_URL=https://data-platform.mlit.go.jp/api/v1/
   ```

   あるいはコマンドラインから直接設定することも可能です：

   ```bash
   export MLIT_API_KEY=your_api_key_here
   export MLIT_BASE_URL=https://data-platform.mlit.go.jp/api/v1/
   ```

   `your_api_key_here`は必ず、手順1で取得したAPIキーに置き換えてください。

6. **MCP サーバーの起動**

   **SSE サーバー（HTTP/SSE）として起動する場合（デフォルト）:**
   ```bash
   python -m src.server --transport sse --port 8000
   # または
   uvicorn src.server:app --port 8000
   ```
   エンドポイント: `http://localhost:8000/sse`

   **stdio（標準入出力）モードで起動する場合:**
   ```bash
   python -m src.server --transport stdio
   ```

7. **Claude Desktopの設定ファイルを開く**

   * **Windows：** `C:\Users\<ユーザー名>\AppData\Roaming\Claude\claude_desktop_config.json`
   * **macOS：** `~/Library/Application Support/Claude/claude_desktop_config.json`
   * Claude Desktopアプリの設定画面にある「開発者」メニューの「設定を編集」ボタンをクリックして`claude_desktop_config.json`を開くことも可能です。

8. **MCPサーバーの構成を追加**

   ```json
   {
     "mcpServers": {
       "mlit-dpf-mcp": {
         "command": "......./mlit-dpf-mcp/.venv/Scripts/python.exe",
         "args": [
           "....../mlit-dpf-mcp/src/server.py"
         ],
         "env": {
           "MLIT_API_KEY": "your_api_key_here",
           "MLIT_BASE_URL": "https://data-platform.mlit.go.jp/api/v1/",
           "PYTHONUNBUFFERED": "1",
           "LOG_LEVEL": "WARNING"
         }
       }
     }
   }
   ```

   `command`と`args`は必ず、実際のパスに変更してください。  
   `your_api_key_here`は必ず、手順1で取得したAPIキーに置き換えてください。

9. **Claude Desktop を再起動**


## 5. ディレクトリ構成

```
mlit-dpf-mcp/
├─ src/
│  ├─ server.py   # MCP サーバー & ツール定義
│  ├─ client.py   # MLIT GraphQL API クライアント
│  ├─ schemas.py  # Pydantic モデル（入力バリデーション）
│  ├─ config.py   # 環境変数ロード & 設定検証
│  └─ utils.py    # ロギング、タイマー、レート制限
├─ pyproject.toml
├─ README.md
└─ LICENSE
```

## 6. ライセンス
* 本リポジトリはMITライセンスで提供されています。[ライセンス](./LICENSE)を参照してください。



## 7. 注意事項
* 本リポジトリで提供されるデータの利用に関しては、 [国土交通データプラットフォームの利用規約](https://data-platform.mlit.go.jp/assets/policy/%E5%9B%BD%E5%9C%9F%E4%BA%A4%E9%80%9A%E3%83%87%E3%83%BC%E3%82%BF%E3%83%97%E3%83%A9%E3%83%83%E3%83%88%E3%83%95%E3%82%A9%E3%83%BC%E3%83%A0%E5%88%A9%E7%94%A8%E8%A6%8F%E7%B4%84.pdf)に従う必要があります。ご使用前に国土交通データプラットフォームの利用規約を必ずご確認ください。
* 本リポジトリの個人情報の取り扱いは、[国土交通データプラットフォームのプライバシーポリシー](https://data-platform.mlit.go.jp/assets/policy/%E5%9B%BD%E5%9C%9F%E4%BA%A4%E9%80%9A%E3%83%87%E3%83%BC%E3%82%BF%E3%83%97%E3%83%A9%E3%83%83%E3%83%88%E3%83%95%E3%82%A9%E3%83%BC%E3%83%A0_%E3%83%97%E3%83%A9%E3%82%A4%E3%83%90%E3%82%B7%E3%83%BC%E3%83%9D%E3%83%AA%E3%82%B7%E3%83%BC.pdf)に準拠しております。
* 本リポジトリはα版として提供しているものです。動作保証は行っておりません。
* 本リポジトリの内容は予告なく変更・削除する可能性があります。
* 本リポジトリの利用により生じた損失及び障害等について、国土交通省及び国土交通データプラットフォームはいかなる責任も負わないものとします。

## 8. お問い合わせ
本リポジトリはα版です。お気づきの点があれば下記お問い合わせフォームまでご連絡下さい。
* [国土交通データプラットフォームお問い合わせフォーム](https://forms.cloud.microsoft/pages/responsepage.aspx?id=6dtgrapYuEqgn0vEnGaJBOrx9xG7EpJLvmRgrTAinyBUQlpHVUQ2UEM0TUkwVUdMVE5HNUM0OTlXVyQlQCN0PWcu&route=shorturl)
