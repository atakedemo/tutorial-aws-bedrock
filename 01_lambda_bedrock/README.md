# LambdaからBedrockの生成APIアプリを動かす

## 手順

### 0. 開発用のDocker環境を起動する

```bash
docker-compose up --build
docker-compose exec aws-demo-langchain-lambda bash
```

### 1. Lambdaレイヤーの作成

必要なライブラリを[./python](./python/)配下へインストールする

```bash
mkdir python
pip3 install -t ./python fastapi==0.99.0 langchain==0.2.0 langchain-aws==0.1.4 langchain-community==0.2.0 python-dateutil==2.8.2
```

Lambdaレイヤーを作成する

```bash
# Zipファイルを生成する（Lambdaレイヤーの容量制限に収まるよう、不要なbotoは削除）
rm -rf python/boto*
zip -r9 langchain-layer.zip python

# ZipファイルをLambdaレイヤーとしてアップデート
aws lambda publish-layer-version \
 --layer-name langchain-layer \
 --compatible-runtime python3.9 \
 --compatible-architectures x86_64 \
 --zip-file fileb://langchain-layer.zip --no-cli-pager
```

### 3. Lambda関数の作成

* AWSのコンソールから、手順2で作成したLambdaレイヤーを利用した関数を作成する
* Lambda関数に "AmazonBedrockFullAccess"のIAMポリシーを追加

※Pythonコード

```python
from langchain_aws import ChatBedrock
from langchain_core.messsages import HumanMessage, SystemMessage

def invoke_bedrock(prompt: str):
    chat = ChatBedrock(
        model_id="anthropic.claude-3-sonnet-20240229-v1:0",
        model_kwargs={"max_tokens": 1000},
    )

    messages = [
        SystemMessage(content="あなたのタスクはユーザーの質問に明確に答えること"),
        HumanMessage(content=prompt),
    ]

    response = chat.invoke(messsages)
    return response.content

def lambda_handler(event, context):
    result = invoke_bedrock("空が青いのはなぜですか？")
    return {"statusCode": 200, "body": result}
```

## 参考
