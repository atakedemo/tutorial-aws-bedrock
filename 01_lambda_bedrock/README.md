# LambdaからBedrockの生成APIアプリを動かす

## 手順

### 0. MacでのPythonバージョン修正

Python3.9を使用するため調整する

```bash
pyenv install 3.9.16
pyenv local 3.9.16
```

### 1. Lambdaレイヤーの作成

```bash
pip install -t ./python langchain==0.2.0 langchain-aws==0.1.4 langchain-community==0.2.0 python-dateutil==2.8.2
```

## 参考
