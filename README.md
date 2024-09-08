# Faster Whisper for docker compose
Faster Whisper large-v3モデルを使用して音声ファイルまたは動画ファイルの文字起こしをすることができます。
docker composeを使用しているので環境構築が楽です。

基本的には、GPUを推奨しますが、GPUが搭載されていない場合でも、CPUを使って文字起こしをします
※低速なので夜間実行推奨

## 前提条件

- Dockerがインストールされていること

GPUを使用する場合：
- NVIDIA GPU（CUDA対応）
- NVIDIA Container Toolkitのインストール

補足：GPUが利用できない環境でも、アプリケーションは自動的にCPUを使用して動作します。

## セットアップ

1. このリポジトリをローカルマシンにクローンします。

2. GPUを使用する場合：NVIDIA Container Toolkitがインストールされ、設定されていることを確認してください。nvidia-smiコマンドが実行できればOKです。

3. 音声ファイルまたは動画ファイルを`./media`ディレクトリに配置します。
   - サポートされているファイル形式：[mp3 , mp4 , mpeg , mpga , m4a , wav , webm]

## アプリケーションのビルドと実行

アプリケーションをビルドして実行するには、以下のコマンドを使用します：

```
docker compose up --build
```

このコマンドはDockerイメージをビルドし、コンテナを起動します。文字起こしプロセスは自動的に開始されます。

## デバイス自動検出

このアプリケーションは、利用可能なハードウェアを自動的に検出し、適切なデバイスとコンピュートタイプを選択します：

- GPUが利用可能な場合：CUDA（"cuda"）デバイスと"float16"コンピュートタイプを使用
- GPUが利用できない場合：CPU（"cpu"）デバイスと"int8"コンピュートタイプを使用

この自動検出により、ユーザーは環境に応じた手動設定を行う必要がありません。

## プロジェクト構造

- `main.py`: 音声文字起こしを実行するメインのPythonスクリプト。
- `Dockerfile`: アプリケーションのDockerイメージを定義します。
- `compose.yml`: Docker Compose設定ファイル。
- `pyproject.toml`と`poetry.lock`: Python依存関係を管理するためのPoetry設定ファイル。
- `./media`: 入力する音声ファイルまたは動画ファイルを配置するディレクトリ。
- `./output`: 文字起こし結果が保存されるディレクトリ。

## Docker設定

`compose.yml`ファイルは以下の設定でDockerコンテナを構成します：

- 現在のディレクトリのDockerfileを使用してイメージをビルドします。
- `.env`ファイルから環境変数を読み込みます。
- GPUを使用する場合：コンテナ用にNVIDIA GPUを1つ予約します。
- `./media`ディレクトリをコンテナ内の`/app/media`にマウントして、入力ファイルにアクセスします。
- `./output`ディレクトリをコンテナ内の`/app/mount`にマウントして、文字起こし結果を保存します。

## 依存関係管理

このプロジェクトではPythonの依存関係管理にPoetryを使用しています。`pyproject.toml`ファイルでプロジェクトの依存関係を定義し、それらは`poetry.lock`ファイルにロックされます。

## 出力

文字起こし結果はホストマシンの`./output`ディレクトリに保存されます。

## カスタマイズ

文字起こしプロセスをカスタマイズしたり、入力ファイルの処理方法を変更したりするには、`main.py`スクリプトを適宜修正してください。

## トラブルシューティング

問題が発生した場合は、以下を確認してください：

1. Docker環境が正しく設定されていること。
2. 入力ファイルが`./media`ディレクトリに正しく配置されていること。
3. 入力ファイル形式がサポートされていること。
4. ファイルが破損していないこと。

GPUを使用する場合：
1. NVIDIAドライバーが最新であること。
2. NVIDIA Container Toolkitが正しくインストールされ、設定されていること。
3. お使いのGPUがDockerfileで指定されているCUDAバージョンと互換性があること。
