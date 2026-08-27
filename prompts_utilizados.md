# Registro de Prompts Utilizados — Laboratório 02

Este arquivo documenta os prompts utilizados com assistentes de IA generativa durante a construção dos diagramas deste estudo de caso, conforme exigido pela Política de Uso de IA do laboratório.

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

### Prompt inicial

**Ferramenta**: Gemini (Google)
**Objetivo**: Gerar o diagrama de componentes para a integração Big Bang do fluxo de validação de evidências fotográficas.

```text
Atue como um engenheiro de software e engenheiro de testes sênior.
Estou desenvolvendo um estudo de caso sobre estratégias e níveis de teste para um Sistema Inteligente de Validação de Evidências Fotográficas para serviços de telecomunicações.
Para o nível Teste de Integração, especificamente a abordagem Integração Não Incremental (Big Bang), gere um diagrama de componentes em sintaxe PlantUML válido.
O cenário é o seguinte:
O sistema é composto pelos módulos AplicativoCaptura (responsável por capturar e enviar a foto), ServicoOCR (responsável por extrair o texto da imagem), ValidadorMetragem (responsável por validar a metragem extraída) e RepositorioEvidencias (responsável por persistir o resultado da validação).
Na abordagem Big Bang, todos esses módulos são integrados de uma só vez, sem uso de stubs ou drivers intermediários, e testados em conjunto como um fluxo completo.
O diagrama deve representar:
- Os quatro módulos citados, como componentes (component);
- As dependências/fluxo de chamada entre eles (quem aciona quem), usando setas de dependência simples;
- Não deve haver nenhum componente do tipo stub, driver, mock ou simulador — todos os módulos representados devem ser reais, evidenciando que não há isolamento nem substituição de componentes na integração Big Bang.
Não detalhe atributos ou métodos internos de cada componente; o foco é a estrutura de integração entre os módulos, não a lógica interna de cada um.
Utilize nomes de componentes em português e mantenha o diagrama simples e coerente com a proposta de integração não incremental.
Formate a resposta somente como um bloco de código PlantUML, sem explicações adicionais.
```

**Refinamentos aplicados após avaliação crítica**:
- Ajuste do rótulo da última seta, de `"persiste resultado da validação"` para `"envia resultado da validação"`, reforçando que a responsabilidade de persistência é exclusiva do `RepositorioEvidencias`, e não do `ValidadorMetragem`.
- Renomeação do componente `AplicativoCaptura` para `InterfaceTecnico`, evitando pressupor uma decisão de implementação (aplicativo mobile) ainda não definida no projeto, e mantendo o padrão de nomenclatura por **responsabilidade funcional** já usado nos demais componentes (`ServicoOCR`, `ValidadorMetragem`, `RepositorioEvidencias`).

---

## 2.2 — Integração Incremental Top-Down (Descendente) com uso de Stubs

### Prompt inicial

**Ferramenta**: Gemini (Google)
**Objetivo**: Gerar o diagrama de classes representando a integração Top-Down de `ServicoProcessamentoEvidencia`, com `ServicoOCR` e `ValidadorMetragem` já reais e `RepositorioEvidencias` substituído por um Stub.

```text
Atue como um engenheiro de software e engenheiro de testes sênior.
Estou desenvolvendo um estudo de caso sobre estratégias e níveis de teste para um Sistema Inteligente de Validação de Evidências Fotográficas para serviços de telecomunicações.
Para o nível Teste de Integração, especificamente a abordagem Integração Incremental Top-Down (Descendente) com uso de Stubs, gere um diagrama de classes em sintaxe PlantUML válido.
O cenário é o seguinte:
O sistema possui um módulo de alto nível chamado ServicoProcessamentoEvidencia, responsável por orquestrar todo o fluxo: recebe a fotografia, aciona o ServicoOCR (já integrado e real) para extrair o texto, envia o texto para o ValidadorMetragem (já integrado e real) para validação, e por fim envia o resultado para persistência.
O módulo de persistência real (RepositorioEvidencias) ainda não está disponível nesta etapa da integração. Para permitir o avanço progressivo da integração Top-Down sem bloquear os testes do módulo de alto nível, utiliza-se uma interface IRepositorioEvidencias e uma classe RepositorioEvidenciasStub, que simula em memória o armazenamento do resultado da validação.
O diagrama deve representar esta etapa específica da integração:
- A classe ServicoProcessamentoEvidencia (módulo real de alto nível sob teste), com um método principal processarEvidencia(foto: String): ResultadoValidacao;
- A classe ServicoOCR (componente real já integrado), com um método simples de extração;
- A classe ValidadorMetragem (componente real já integrado, o mesmo já modelado anteriormente);
- A interface IRepositorioEvidencias, com um método salvar(resultado: ResultadoValidacao): Boolean;
- A classe RepositorioEvidenciasStub <<Stub (Simulador)>>, implementando essa interface, com um atributo simples simulando o armazenamento em memória;
- Os relacionamentos: ServicoProcessamentoEvidencia utiliza ServicoOCR, ValidadorMetragem e a interface IRepositorioEvidencias; RepositorioEvidenciasStub implementa IRepositorioEvidencias.
Utilize nomes de classes, métodos e atributos em português e mantenha o diagrama coerente com o padrão de integração Top-Down com Stub, seguindo o mesmo estilo do exemplo de referência do laboratório (TransferenciaPixService com GatewayBacenStub).
Formate a resposta somente como um bloco de código PlantUML, sem explicações adicionais.
```
**Refinamentos aplicados após avaliação crítica**:
- ...

---

## 2.3 — Integração Incremental Bottom-Up (Ascendente) com uso de Drivers

### Prompt inicial

**Ferramenta**: Gemini (Google)
**Objetivo**: Gerar o diagrama de classes representando a integração Bottom-Up entre `ServicoOCR` e `ValidadorMetragem`, com o módulo controlador `ServicoProcessamentoEvidencia` ainda inexistente e substituído por um Driver.

```text
Atue como um engenheiro de software e engenheiro de testes sênior.
Estou desenvolvendo um estudo de caso sobre estratégias e níveis de teste para um Sistema Inteligente de Validação de Evidências Fotográficas para serviços de telecomunicações.
Para o nível Teste de Integração, especificamente a abordagem Integração Incremental Bottom-Up (Ascendente) com uso de Drivers, gere um diagrama de classes em sintaxe PlantUML válido.
O cenário é o seguinte:
Os módulos de baixo nível ServicoOCR (responsável por extrair texto de uma fotografia) e ValidadorMetragem (responsável por validar a metragem extraída) já estão implementados e testados. O módulo de alto nível ServicoProcessamentoEvidencia, que futuramente orquestraria esses componentes, ainda não foi implementado.
Para testar a integração entre ServicoOCR e ValidadorMetragem antes que o orquestrador real exista, utiliza-se a classe ValidadorMetragemDriver, que assume temporariamente o papel do componente superior: aciona o ServicoOCR com uma fotografia de teste, recebe o texto extraído, encaminha esse texto para o ValidadorMetragem e verifica o ResultadoValidacao retornado.
O diagrama deve representar:
- A classe ServicoOCR (componente real de baixo nível), com o método extrairTexto(foto: String): String;
- A classe ValidadorMetragem (componente real de baixo nível), com o método validarTexto(textoExtraido: String): ResultadoValidacao;
- A classe ValidadorMetragemDriver <<Driver (Simulador)>>, com um método principal como executarCenarioDeTeste(foto: String): void, que aciona diretamente ServicoOCR e ValidadorMetragem (o Driver orquestra as chamadas a ambos, e não há chamada direta entre ServicoOCR e ValidadorMetragem);
- Os relacionamentos de uso: ValidadorMetragemDriver utiliza ServicoOCR; ValidadorMetragemDriver utiliza ValidadorMetragem.
Deixe claro, por meio de comentário ou nota no próprio diagrama, que o Driver é um artefato de teste e não uma dependência real do sistema.
Utilize nomes de classes, métodos e atributos em português e mantenha o diagrama coerente com o padrão de integração Bottom-Up com Driver.
Formate a resposta somente como um bloco de código PlantUML, sem explicações adicionais.
```

**Refinamentos aplicados após avaliação crítica**:
- Nota (`note top of ValidadorMetragemDriver`) reduzida para um texto mais objetivo, evitando poluição visual no diagrama.

---

## 2.4 — Teste de Fumaça (Smoke Testing)

### Prompt inicial

**Ferramenta**: Claude (Anthropic)
**Objetivo**: Gerar o diagrama de sequência representando o fluxo crítico do sistema sendo verificado logo após um deploy.

```text
Atue como um engenheiro de software e engenheiro de testes sênior.
Estou desenvolvendo um estudo de caso sobre estratégias e níveis de teste para um Sistema Inteligente de Validação de Evidências Fotográficas para serviços de telecomunicações.
Para o nível Teste de Integração, especificamente a abordagem Teste de Fumaça (Smoke Testing), gere um diagrama de sequência em sintaxe PlantUML válido.
O cenário é o seguinte:
Logo após um novo deploy do sistema, um Técnico envia uma única fotografia de teste conhecida, com metragem válida "1493 m", por meio da InterfaceTecnico. A foto é repassada ao ServicoOCR, que extrai o texto "1493 m". Esse texto é enviado ao ValidadorMetragem, que retorna um resultado de aprovação. O resultado é então enviado ao RepositorioEvidencias, que confirma o armazenamento. Por fim, a InterfaceTecnico exibe ao Técnico a mensagem "Foto aprovada", confirmando que o fluxo crítico está operacional.
O diagrama deve representar:
- Um ator Tecnico, que inicia o fluxo enviando a fotografia;
- Os participantes InterfaceTecnico, ServicoOCR, ValidadorMetragem e RepositorioEvidencias;
- A sequência completa de chamadas e retornos: envio da foto, extração do texto, validação da metragem, persistência do resultado, e a confirmação final exibida ao técnico;
- Não representar casos de erro, exceções ou fluxos alternativos — apenas o caminho principal (happy path);
- Uma nota indicando que este é um teste superficial e rápido, cobrindo apenas o caminho crítico do sistema após o deploy.
Utilize nomes em português e mantenha o diagrama simples, coerente com a proposta de um Smoke Test.
Formate a resposta somente como um bloco de código PlantUML, sem explicações adicionais.
```
**Refinamentos aplicados após avaliação crítica**: ...

---

## 2.5 — Teste de Regressão

### Prompt inicial

**Ferramenta**: Gemini (Google)
**Objetivo**: Gerar o diagrama de classes representando a suíte de regressão reexecutando casos existentes e novos sobre `ValidadorMetragem` após uma alteração.

```text
Atue como um engenheiro de software e engenheiro de testes sênior.
Estou desenvolvendo um estudo de caso sobre estratégias e níveis de teste para um Sistema Inteligente de Validação de Evidências Fotográficas para serviços de telecomunicações.
Para o nível Teste de Integração, especificamente a abordagem Teste de Regressão, gere um diagrama de classes em sintaxe PlantUML válido.
O cenário é o seguinte:
A classe ValidadorMetragem passou por uma alteração: além dos formatos inteiros já suportados (ex: "1493 m", "850 m"), agora ela também deve aceitar metragens com separador decimal (ex: "1.493 m"). Após essa mudança, uma suíte de testes de regressão é executada para garantir que os casos já existentes continuam passando, junto com os novos casos motivados pela alteração.
O diagrama deve representar:
- A classe ValidadorMetragem (já alterada), com o método validarTexto(texto: String): ResultadoValidacao;
- A classe ValidadorMetragemRegressaoSuite <<Test>>, contendo os métodos testarMetragemInteira(): void e testarOutraMetragemInteira(): void (casos de regressão, que já existiam antes da mudança), testarMetragemDecimal(): void (novo caso, motivado pela alteração) e testarTextoSemMetragem(): void (caso de regressão que garante que textos inválidos continuam sendo reprovados);
- O relacionamento de teste entre a suíte de regressão e ValidadorMetragem;
- Não incluir nenhum outro módulo do sistema (OCR, InterfaceTecnico, RepositorioEvidencias) — o foco é exclusivamente a reexecução dos testes de ValidadorMetragem após a alteração.
Utilize nomes de classes e métodos em português e mantenha o diagrama simples e coerente com a proposta de teste de regressão.
Formate a resposta somente como um bloco de código PlantUML, sem explicações adicionais.
```

**Refinamentos aplicados após avaliação crítica**:
- ...

---
