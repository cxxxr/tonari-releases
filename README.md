# tonari

部屋で一人だと集中できない人のための、デスクトップアプリです。画面の隅で AI の同僚（ペルソナ）が自分の作業を進めていて、ときどき独り言を言います。25 分ごとの区切りで「そっちどう？」と声をかけてきます。

このリポジトリは配布用で、インストーラだけを置いています。

## 用意するもの

- [Node.js](https://nodejs.org/) 22 以上。ペルソナが自分のプロジェクトで `node` と `npm` を使います
- Anthropic の API キー（環境変数 `ANTHROPIC_API_KEY`）。ペルソナが考えるのに使います（下の「設定」）
- 声を出すなら [AivisSpeech](https://aivis-project.com/)（下の「声を出す」）。パネルの案内から tonari に入れてもらえます。無ければ字幕だけになります

## 始めかた

[Releases](https://github.com/cxxxr/tonari-releases/releases) から自分の OS のインストーラ（macOS は Apple シリコン向けの `.dmg`、Windows は `.exe`、Linux は `.deb` か `.AppImage`）を落として入れ、tonari を起動します。
インストーラは署名していないので、初めて開くときに OS の警告が出ます（macOS は「システム設定 → プライバシーとセキュリティ」の「このまま開く」、Windows は「詳細情報」から「実行」）。Ubuntu 24.04 などで AppImage が起動しないときは `.deb` を使ってください。

1. 「なにやる？」に一言で答える
2. ペルソナの作業を選ぶ。自分のプロジェクトを進めてもらうか、あなたのリポジトリを「見守る」（別に回している Claude Code の作業を、ペルソナの作業として見せる）か「手伝う」（作業を一部任せる）
3. ペルソナが隣で作業を始める。あなたも自分の作業をする
4. 区切りで休憩。終わるときに「どこまでいった？」と聞かれる

## 声を出す（AivisSpeech）

声のエンジン（AivisSpeech）が動いていないと、パネルに案内が出ます。

- **いちばん簡単なやり方:** 案内の「tonari に入れてもらう（約 1.8GB）」を押します。tonari が声のエンジンと既定の声を落として入れ、声で話しはじめます。どの OS でも同じで、Linux でも Docker は要りません。次からは tonari の起動と一緒に動き、終わると止まります。初めてのときは、落とすのに数分かかります
- **自分で入れる場合（Windows・macOS）:** [AivisSpeech の公式サイト](https://aivis-project.com/)からアプリを入れて、起動しておきます。動いていれば、tonari はそちらを使います
- **自分で入れる場合（Linux）:** 音声合成エンジンを [Docker](https://docs.docker.com/engine/install/) で動かします

  ```bash
  mkdir -p ~/.local/share/AivisSpeech-Engine
  docker run -d --name aivisspeech --restart unless-stopped \
    -p 127.0.0.1:10101:10101 \
    -v ~/.local/share/AivisSpeech-Engine:/home/user/.local/share/AivisSpeech-Engine-Dev \
    ghcr.io/aivis-project/aivisspeech-engine:cpu-latest
  ```

  `-v` の右側（`/home/user/...`）はコンテナの中の場所なので、そのまま書きます。`user.yaml`（下の「設定」）に `voice_engine:` の `start_cmd: docker start aivisspeech` を書いておくと、tonari の起動のときに立ち上げます

声が出ないときは、ペルソナ画面の声の欄で、エンジンにある声を選んでください。

## 設定

パネル右上の人の形のボタンでペルソナ画面を開き、ペルソナを追加・切り替えたり、名前・見た目（VRM）・声を変えたりできます。作業と休憩の長さ・独り言の量・打鍵音・作業の音は歯車から変えられます。どちらも作業中は開けないので、休憩中か始める前に開いてください。

画面に無い設定は `~/.local/share/tonari/user/user.yaml`（Windows は `%APPDATA%\tonari\user\user.yaml`）に YAML で書きます。初めて起動したときに、書ける項目をコメントにして作ります。

API キーで使うには、環境変数 `ANTHROPIC_API_KEY` を渡して起動し、`user.yaml` に次を書きます。

```yaml
narrator:
  backend: api
```

## 見ないもの・しないこと

- 画面の内容やキー入力の中身は見ません。あなたの様子として使うのは「最後に入力してから何秒たったか」だけです
- 「見守る」を選んだリポジトリでは、Claude が書いたコミットの件名とブランチ名を独り言の材料として Claude に渡します。あなたが自分でしたコミットは拾いません。ファイルの中身は読みません
- ペルソナが書き込むのは自分の作業場所だけで、あなたのファイルには書かないようにしています。ただし、ペルソナが打つコマンドは中身から判断して止めているので、完全には防げません
- 「手伝う」を選ぶと、ペルソナはそのリポジトリの複製の中で作業します。元のリポジトリには書きません。ただし、そのリポジトリのテストやスクリプトを実行することがあるので、信頼できるリポジトリだけを選んでください

## クレジット

- 付属のアバターは pixiv Inc. の VRM 1.0 のサンプルモデル（VRM1_Constraint_Twist_Sample。(c) 2022 pixiv Inc.）です。[VRM Public License 1.0](https://vrm.dev/licenses/1.0/) で、再配布・改変が許されていて、クレジットの表記は要りません。ペルソナ画面から、好きな VRM に替えられます
- 既定の声は AivisSpeech の凛音エルです。声のモデルの利用規約は、AivisHub のモデルのページで確認してください
