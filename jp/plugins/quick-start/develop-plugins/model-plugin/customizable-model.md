はい、承知いたしました。以下に、ご提示いただいた中国語の原文を、より自然で分かりやすい日本語に再翻訳します。オリジナルの形式は維持し、翻訳の問題点を考慮して修正しました。

# カスタムモデルの組み込み

カスタムモデルとは、ユーザー自身でデプロイまたは設定する必要があるLLMのことです。この記事では、[Xinferenceモデル](https://inference.readthedocs.io/en/latest/)を例に、モデルプラグイン内でカスタムモデルを組み込む方法を解説します。

カスタムモデルには、デフォルトでモデルタイプとモデル名の2つのパラメータが含まれており、サプライヤのyamlファイルで定義する必要はありません。

サプライヤ設定ファイルで`validate_provider_credential`を実装する必要はありません。Runtimeは、ユーザーが選択したモデルタイプまたはモデル名に基づいて、対応するモデルレイヤの`validate_credentials`メソッドを自動的に呼び出して検証します。

### カスタムモデルプラグインの組み込み

カスタムモデルを組み込むには、以下の手順に従います。

1.  **モデルサプライヤファイルの作成**

    組み込むカスタムモデルのモデルタイプを明確にします。
2.  **モデルタイプに応じたコードファイルの作成**

    モデルのタイプ（`llm`や`text_embedding`など）に応じて、コードファイルを作成します。各モデルタイプが独立した論理構造を持つようにすることで、保守と拡張が容易になります。
3.  **モデルモジュールに基づいたモデル呼び出しコードの記述**

    対応するモデルタイプモジュールに、モデルタイプと同名のPythonファイル（例：llm.py）を作成します。ファイル内で、具体的なモデルロジックを実装するクラスを定義します。このクラスは、システムのモデルインターフェース仕様に準拠している必要があります。
4.  **プラグインのデバッグ**

    新たに追加されたサプライヤ機能について、ユニットテストと統合テストを作成し、すべての機能モジュールが期待どおりに動作することを確認します。

***

### 1. **モデルサプライヤファイルの作成**

プラグインプロジェクトの`/provider`パスに、`xinference.yaml`ファイルを作成します。

`Xinference`は、`LLM`、`Text Embedding`、`Rerank`のモデルタイプをサポートしているため、`xinference.yaml`ファイルにこれらのモデルタイプを含める必要があります。

サンプルコード：

<pre class="language-yaml"><code class="lang-yaml">provider: xinference # サプライヤIDを決定
label: # サプライヤの表示名。en_US（英語）とzh_Hans（中国語）の2つの言語を設定できます。zh_Hansを設定しない場合、デフォルトでen_USが使用されます。
  en_US: Xorbits Inference
icon_small: # 小さいアイコン。他のサプライヤのアイコンを参考に、対応するサプライヤ実装ディレクトリの_assetsディレクトリに保存してください。言語設定はlabelと同様です。
  en_US: icon_s_en.svg
icon_large: # 大きいアイコン
  en_US: icon_l_en.svg
help: # ヘルプ
  title:
    en_US: How to deploy Xinference
    zh_Hans: 如何部署 Xinference (Xinferenceのデプロイ方法)
  url:
    en_US: https://github.com/xorbitsai/inference
<strong>supported_model_types: # サポートされているモデルタイプ。XinferenceはLLM/Text Embedding/Rerankをサポートしています。
</strong><strong>- llm
</strong><strong>- text-embedding
</strong><strong>- rerank
</strong>configurate_methods: # Xinferenceはローカルにデプロイするサプライヤであり、事前定義されたモデルはありません。使用するモデルはXinferenceのドキュメントに従ってデプロイする必要があるため、ここではカスタムモデルを使用します。
- customizable-model
provider_credential_schema:
  credential_form_schemas:
</code></pre>

次に、`provider_credential_schema`フィールドを定義します。`Xinference`は、text-generation、embeddings、rerankingモデルをサポートしています。サンプルコードを以下に示します。

<pre class="language-yaml"><code class="lang-yaml">provider_credential_schema:
  credential_form_schemas:
  - variable: model_type
    type: select
    label:
      en_US: Model type
      zh_Hans: 模型类型 (モデルタイプ)
    required: true
    options:
<strong>    - value: text-generation
</strong>      label:
        en_US: Language Model
        zh_Hans: 语言模型 (言語モデル)
<strong>    - value: embeddings
</strong>      label:
        en_US: Text Embedding
<strong>    - value: reranking
</strong>      label:
        en_US: Rerank
</code></pre>

Xinferenceの各モデルでは、`model_name`という名前を定義する必要があります。

```yaml
  - variable: model_name
    type: text-input
    label:
      en_US: Model name
      zh_Hans: 模型名称 (モデル名)
    required: true
    placeholder:
      zh_Hans: モデル名を入力してください
      en_US: Input model name
```

Xinferenceモデルでは、ユーザーがモデルのローカルデプロイアドレスを入力する必要があります。プラグイン内では、Xinferenceモデルのローカルデプロイアドレス（server\_url）とモデルUIDを入力できる場所を提供する必要があります。サンプルコードを以下に示します。

<pre class="language-yaml"><code class="lang-yaml"><strong>  - variable: server_url
</strong>    label:
      zh_Hans: サーバーURL
      en_US: Server url
    type: text-input
    required: true
    placeholder:
      zh_Hans: Xinferenceのサーバーアドレスをここに入力してください（例：https://example.com/xxx）
      en_US: Enter the url of your Xinference, for example https://example.com/xxx
<strong>  - variable: model_uid
</strong>    label:
      zh_Hans: モデルUID
      en_US: Model uid
    type: text-input
    required: true
    placeholder:
      zh_Hans: モデルUIDを入力してください
      en_US: Enter the model uid
</code></pre>

すべてのパラメータを入力すると、カスタムモデルサプライヤのyaml設定ファイルの作成が完了します。次に、設定ファイルで定義されたモデルに具体的な機能コードファイルを追加する必要があります。

### 2. モデルコードの記述

Xinferenceモデルサプライヤのモデルタイプには、llm、rerank、speech2text、ttsタイプが含まれています。そのため、`/models`パスに各モデルタイプの独立したグループを作成し、対応する機能コードファイルを作成する必要があります。

以下では、llmタイプを例に、`llm.py`コードファイルの作成方法を説明します。コードを作成する際には、Xinference LLMクラスを作成する必要があります。名前は`XinferenceAILargeLanguageModel`とし、`__base.large_language_model.LargeLanguageModel`基底クラスを継承し、以下のメソッドを実装します。

* **LLMの呼び出し**

LLM呼び出しの中核となるメソッドです。ストリーミングと同期の両方の戻り値をサポートします。

```python
def _invoke(self, model: str, credentials: dict,
            prompt_messages: list[PromptMessage], model_parameters: dict,
            tools: Optional[list[PromptMessageTool]] = None, stop: Optional[list[str]] = None,
            stream: bool = True, user: Optional[str] = None) \
        -> Union[LLMResult, Generator]:
    """
    Invoke large language model

    :param model: model name
    :param credentials: model credentials
    :param prompt_messages: prompt messages
    :param model_parameters: model parameters
    :param tools: tools for tool calling
    :param stop: stop words
    :param stream: is stream response
    :param user: unique user id
    :return: full response or stream response chunk generator result
    """
```

コードを実装する際には、同期戻り値とストリーミング戻り値で異なる関数を使用する必要があります。

Pythonでは、関数に`yield`キーワードが含まれている場合、その関数はジェネレータ関数として認識され、戻り値の型は`Generator`に固定されます。したがって、同期戻り値とストリーミング戻り値をそれぞれ実装する必要があります。例えば、以下のサンプルコードを参照してください。

> この例では、パラメータが簡略化されています。実際のコードを記述する際には、上記のパラメータリストを参照してください。

```python
def _invoke(self, stream: bool, **kwargs) \
        -> Union[LLMResult, Generator]:
    if stream:
          return self._handle_stream_response(**kwargs)
    return self._handle_sync_response(**kwargs)

def _handle_stream_response(self, **kwargs) -> Generator:
    for chunk in response:
          yield chunk
def _handle_sync_response(self, **kwargs) -> LLMResult:
    return LLMResult(**response)
```

* **入力トークンの事前計算**

モデルがトークンの事前計算インターフェースを提供していない場合は、0を返すことができます。

```python
def get_num_tokens(self, model: str, credentials: dict, prompt_messages: list[PromptMessage],
                 tools: Optional[list[PromptMessageTool]] = None) -> int:
  """
  Get number of tokens for given prompt messages

  :param model: model name
  :param credentials: model credentials
  :param prompt_messages: prompt messages
  :param tools: tools for tool calling
  :return:
  """
```

直接0を返したくない場合は、`self._get_num_tokens_by_gpt2(text: str)`メソッドを使用してトークンを計算できます。このメソッドは`AIModel`基底クラスにあり、GPT-2のTokenizerを使用して計算を行います。ただし、あくまで代替手段であり、計算結果には誤差が生じる可能性があることに注意してください。

* **モデルの認証情報の検証**

サプライヤの認証情報の検証と同様に、ここでは個々のモデルを検証します。

```python
def validate_credentials(self, model: str, credentials: dict) -> None:
    """
    Validate model credentials

    :param model: model name
    :param credentials: model credentials
    :return:
    """
```

*   **モデルパラメータのスキーマ**

    [事前定義されたモデルタイプ](integrate-the-predefined-model.md)とは異なり、YAMLファイルにモデルがサポートするパラメータが事前に定義されていないため、モデルパラメータのスキーマを動的に生成する必要があります。

    例えば、Xinferenceは`max_tokens`、`temperature`、`top_p`の3つのモデルパラメータをサポートしています。ただし、サプライヤによっては、モデルごとに異なるパラメータをサポートする場合があります（例：OpenLLM）。

    例として、サプライヤ`OpenLLM`のモデルAは`top_k`パラメータをサポートしていますが、モデルBはサポートしていません。この場合、各モデルに対応するパラメータスキーマを動的に生成する必要があります。以下にサンプルコードを示します。

* ```python
  def get_customizable_model_schema(self, model: str, credentials: dict) -> AIModelEntity | None:
      """
          used to define customizable model schema
      """
      rules = [
          ParameterRule(
              name='temperature', type=ParameterType.FLOAT,
              use_template='temperature',
              label=I18nObject(
                  zh_Hans='温度', en_US='Temperature'
              )
          ),
          ParameterRule(
              name='top_p', type=ParameterType.FLOAT,
              use_template='top_p',
              label=I18nObject(
                  zh_Hans='Top P', en_US='Top P'
              )
          ),
          ParameterRule(
              name='max_tokens', type=ParameterType.INT,
              use_template='max_tokens',
              min=1,
              default=512,
              label=I18nObject(
                  zh_Hans='最大生成长度', en_US='Max Tokens'
              )
          )
      ]

      # if model is A, add top_k to rules
      if model == 'A':
          rules.append(
              ParameterRule(
                  name='top_k', type=ParameterType.INT,
                  use_template='top_k',
                  min=1,
                  default=50,
                  label=I18nObject(
                      zh_Hans='Top K', en_US='Top K'
                  )
              )
          )

      """
          some NOT IMPORTANT code here
      """

      entity = AIModelEntity(
          model=model,
          label=I18nObject(
              en_US=model
          ),
          fetch_from=FetchFrom.CUSTOMIZABLE_MODEL,
          model_type=model_type,
          model_properties={ 
              ModelPropertyKey.MODE:  ModelType.LLM,
          },
          parameter_rules=rules
      )

      return entity
  ```
* **呼び出し例外エラーのマッピング**

モデルの呼び出し時に例外が発生した場合、Runtimeで指定された`InvokeError`タイプにマッピングする必要があります。これは、Difyが異なるエラーに対して異なる後続処理を実行できるようにするためです。

Runtime Errors:

* `InvokeConnectionError`  呼び出し接続エラー
* `InvokeServerUnavailableError`  呼び出しサービスが利用不可
* `InvokeRateLimitError` 呼び出しが制限に達した
* `InvokeAuthorizationError` 呼び出し認証失敗
* `InvokeBadRequestError` 呼び出しパラメータエラー

```python
@property
def _invoke_error_mapping(self) -> dict[type[InvokeError], list[type[Exception]]]:
    """
    Map model invoke error to unified error
    The key is the error type thrown to the caller
    The value is the error type thrown by the model,
    which needs to be converted into a unified error type for the caller.

    :return: Invoke error mapping
    """
```

さらに詳しいインターフェースメソッドについては、[インターフェースドキュメント：Model](../../../schema-definition/model/)を参照してください。

この記事で取り上げた完全なコードファイルについては、[GitHubコードリポジトリ](https://github.com/langgenius/dify-official-plugins/tree/main/models/xinference)をご覧ください。

### 3. プラグインのデバッグ

プラグインの開発が完了したら、次にプラグインが正常に動作するかどうかをテストする必要があります。詳細については、以下を参照してください。

{% content-ref url="debug-plugin.md" %}
[debug-plugin.md](debug-plugin.md)
{% endcontent-ref %}

### 4.  プラグインの公開

プラグインをDify Marketplaceに公開する場合は、以下を参照してください。

{% content-ref url="../../../publish-plugins/publish-to-dify-marketplace.md" %}
[publish-to-dify-marketplace.md](../../../publish-plugins/publish-to-dify-marketplace.md)
{% endcontent-ref %}

### さらに詳しく

**クイックスタート:**

* [拡張タイププラグインの開発](../extension-plugin.md)
* [モデルタイププラグインの開発](README.md)
* [バンドルタイププラグイン：複数のプラグインをパッケージ化](../bundle.md)

**プラグインインターフェースドキュメント:**

* [マニフェスト](../../../schema-definition/manifest.md) 構造
* [エンドポイント](../../../schema-definition/endpoint.md) 詳細定義
* [Difyサービスの逆呼び出し](../../../schema-definition/reverse-invocation-of-the-dify-service/)
* [ツール](../../../schema-definition/tool.md)
* [モデル](../../../schema-definition/model/)
* [拡張エージェント戦略](../../../schema-definition/agent.md)