# Registro de Prompts Utilizados — Laboratório 02

Este arquivo documenta os prompts utilizados com assistentes de IA generativa durante a construção dos diagramas e explicações textuais deste estudo de caso, conforme exigido pela Política de Uso de IA do laboratório.

---

## 1.1 — Verificação de lógica atômica em componente/classe isolada

### Prompt inicial

**Ferramenta**: Claude (Anthropic)
**Objetivo**: Gerar o diagrama de classes inicial para o teste de unidade da classe `ValidadorMetragem`.

```text
Atue como um engenheiro de software e engenheiro de testes sênior.
Estou desenvolvendo um estudo de caso sobre estratégias e níveis de teste para um Sistema Inteligente de Validação de Evidências Fotográficas para serviços de telecomunicações.
Para o nível Teste de Unidade, especificamente a abordagem Verificação de lógica atômica em componente/classe isolada, gere um diagrama de classes em sintaxe PlantUML válido.
O cenário é o seguinte:
O sistema possui uma classe `ValidadorMetragem`, responsável por analisar o texto já extraído de uma fotografia e verificar se existe uma marcação de metragem válida do cabo, como `500 m`, `850 m`, `1493 m` ou `2000 m`.
O teste deve avaliar somente a lógica da classe `ValidadorMetragem`, de forma isolada, sem envolver o aplicativo, OCR, banco de dados ou outros componentes externos.
O diagrama deve representar:
- A classe `ValidadorMetragem`;
- O método responsável por validar o texto recebido;
- O método responsável por extrair a metragem identificada;
- O resultado retornado pela validação;
- Os atributos necessários para representar o resultado, como status de aprovação, metragem identificada e mensagem.
Não inclua componentes externos, banco de dados, OCR ou interfaces que não sejam necessários para representar o teste unitário.
Utilize nomes de classes, métodos e atributos em português e mantenha o diagrama simples e coerente com um teste de unidade.
Formate a resposta somente como um bloco de código PlantUML, sem explicações adicionais.
```

**Refinamentos aplicados após avaliação crítica**:
- Correção de tipos: `metragemIdentificada` alterado de `Double` para `Integer`, já que as metragens do domínio (500, 850, 1493, 2000) são sempre valores inteiros.
- Decisão de **não incluir** a classe.

---

## 2.1 — Integração Não Incremental (Big Bang)
*(a preencher)*