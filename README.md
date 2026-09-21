# expense-assistant-open-webui-spring-ai

Open WebUI とローカル LLM、Spring AI で実装した MCP Server を組み合わせて動かす、経費アシスタントのサンプル環境です。

経費処理そのものを実装する MCP Server は別リポジトリとして管理し、このリポジトリから Git submodule として参照します。

- 親リポジトリ: `expense-assistant-open-webui-spring-ai`
- MCP Server: [`expense-assistant-mcp-server-spring-ai-example`](https://github.com/tanakari/expense-assistant-mcp-server-spring-ai-example)

## リポジトリの取得

このリポジトリは MCP Server を Git submodule として利用しているため、初回 clone 時は `--recurse-submodules` を付けてください。

```bash
git clone --recurse-submodules https://github.com/tanakari/expense-assistant-open-webui-spring-ai.git
cd expense-assistant-open-webui-spring-ai
```

通常の `git clone` を行った場合は、clone 後に以下を実行します。

```bash
git submodule update --init --recursive
```

## 更新

親リポジトリを更新する場合:

```bash
git pull --recurse-submodules
```

MCP Server 側の新しいコミットへ参照を更新する場合:

```bash
cd expense-assistant-mcp-server-spring-ai-example
git fetch
git switch main
git pull
cd ..
git add expense-assistant-mcp-server-spring-ai-example
```

Git submodule は特定コミットを参照するため、MCP Server 側が更新されても、このリポジトリが参照するバージョンは自動では変わりません。

## ディレクトリ構成

```text
expense-assistant-open-webui-spring-ai/
├── expense-assistant-mcp-server-spring-ai-example/  # Git submodule
├── .gitmodules
├── compose.yaml
├── LICENSE
└── README.md
```

今後、Open WebUI、OVMS、MCP Server をまとめて起動する Docker Compose 設定をこの親リポジトリ側で管理します。

## 利用方法

### 環境ファイルの作成

以下のコマンドを実行して `.env` ファイルを作成します。

```bash
echo "LOCAL_UID=$(id -u)" > .env
echo "LOCAL_GID=$(id -g)" >> .env
```

### Modelの格納ディレクトリの作成

モデルファイルを格納するディレクトリを作成し、適切な権限を設定します。

```bash
mkdir -p models
sudo chown -R "$(id -u):$(id -g)" ./models
chmod -R u+rwX ./models
```

### サブモジュールのビルド

PowerShell の場合は以下を実行してください。
```powershell
# サブモジュールのビルド
cd expense-assistant-mcp-server-spring-ai-example
.\mvnw.cmd clean package
cd ..
```

Bash の場合は以下を実行してください。
```bash
# サブモジュールのビルド
cd expense-assistant-mcp-server-spring-ai-example
./mvnw clean package
cd ..
```

### Docker Compose での起動

```bash
docker compose up --build -d
```

## 停止方法

```bash
docker compose down
```

### データの削除

```bash
docker volume rm expense-assistant-open-webui-spring-ai_open-webui-data
```

## License

Apache License 2.0
