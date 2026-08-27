# Laboratório 02 — Estratégias e Níveis de Teste na Prática

## Sistema escolhido: Sistema Inteligente de Validação de Evidências Fotográficas

Para a realização da atividade foi escolhido como domínio um **sistema inteligente de validação de evidências fotográficas para serviços de telecomunicações**. O sistema tem como objetivo analisar fotografias utilizadas para comprovar a execução de serviços técnicos, verificando principalmente se a **metragem do cabo está visível e legível**.

A arquitetura considerada utiliza um **aplicativo para envio das fotografias**, um módulo de **visão computacional e OCR** para identificação dos textos presentes na imagem e um **módulo de validação**, responsável por diferenciar a metragem de outras informações, como coordenadas, data, horário e número da O.S.

Ao final da análise, a fotografia é classificada como **aprovada**, quando a metragem é identificada corretamente, ou **reprovada**, quando não é possível confirmar a metragem da evidência.

Os cenários apresentados neste laboratório representam um **sistema conceitual em desenvolvimento**, utilizado exclusivamente para modelar e demonstrar as diferentes estratégias e níveis de teste solicitados na atividade.

---

## 1. Teste de Unidade

### 1.1 Verificação de lógica atômica em componente/classe isolada
![Diagrama ValidadorMetragem](diagramas/1.1-teste-unidade-validacao-metragem.svg)

#### Explicação Textual

Nesta abordagem, o objetivo é verificar isoladamente a lógica interna da classe `ValidadorMetragem`, responsável por analisar um texto já extraído de uma fotografia (simulando a saída do OCR) e determinar se esse texto contém uma marcação de metragem de cabo válida, como `500 m`, `850 m`, `1493 m` ou `2000 m`.

O teste unitário concentra-se exclusivamente no método público `validarTexto(texto: String)`, que recebe uma string simulando o resultado do processo de OCR e retorna um objeto `ResultadoValidacao`. Internamente, esse método utiliza a lógica encapsulada em `extrairMetragem(texto: String): Integer`, responsável por identificar, dentro do texto recebido, um valor numérico inteiro compatível com o padrão de metragem esperado (armazenado no atributo `padraoMetragem`). O tipo `Integer` foi adotado porque as metragens de cabo trabalhadas neste cenário são sempre valores inteiros (500, 850, 1493, 2000), sem casas decimais. Essa separação entre extração e validação permite testar a classe de forma granular: primeiro verificando se a metragem é corretamente reconhecida no texto, e em seguida se essa metragem é corretamente classificada como aprovada ou reprovada.

O resultado da validação é representado pelo objeto `ResultadoValidacao`, que contém três atributos: `aprovado` (indicando se a metragem identificada é válida), `metragemIdentificada` (o valor inteiro extraído do texto, quando existente) e `mensagem` (uma descrição do motivo da aprovação ou reprovação, útil para rastreabilidade do resultado).

Por se tratar de um teste de unidade, nenhuma dependência externa é envolvida — não há chamadas a serviços de OCR, banco de dados, APIs ou qualquer componente do aplicativo. O texto de entrada é fornecido diretamente pelo testador, simulando diferentes cenários de saída do OCR, como textos com metragem válida, metragem em formato inválido, ausência de metragem ou ruído textual (ex.: caracteres especiais, unidades diferentes de metro).

O principal defeito que este teste busca identificar é a falha na lógica de reconhecimento e validação da metragem — por exemplo, a classe `ValidadorMetragem` aprovar incorretamente um texto sem metragem válida (falso positivo), reprovar um texto com metragem correta (falso negativo), extrair um valor incorreto em `extrairMetragem()` (ex.: capturar apenas parte do número, ou converter incorretamente um texto ruidoso), ou gerar uma `mensagem` inconsistente com o resultado de `aprovado`. Esses são erros de lógica pura, que devem ser detectados nesta camada, antes mesmo de qualquer integração com os demais componentes do sistema.

### 2.1 Integração Não Incremental (Big Bang)

![Diagrama Big Bang](diagramas/2.1-teste-integracao-big-bang.svg)

#### Explicação Textual

Nesta abordagem, o objetivo é testar a integração completa do fluxo de validação de evidências fotográficas, integrando de uma só vez os módulos `InterfaceTecnico`, `ServicoOCR`, `ValidadorMetragem` e `RepositorioEvidencias`, sem o uso de stubs ou drivers. Essa integração simultânea caracteriza a estratégia Big Bang.

O fluxo começa no `InterfaceTecnico`, por onde o técnico envia a fotografia. O `ServicoOCR` recebe a imagem e extrai o texto identificado. Em seguida, o texto é encaminhado ao `ValidadorMetragem`, responsável por verificar se existe uma metragem válida e gerar o resultado da validação. Por fim, esse resultado é enviado ao `RepositorioEvidencias`, responsável por armazenar o registro.

O principal objetivo do teste é identificar falhas de integração e comunicação entre os componentes, como formatos de dados incompatíveis, contratos incorretos ou informações que não sejam corretamente repassadas entre os módulos.

Uma característica dessa abordagem é que, caso ocorra uma falha, pode ser mais difícil identificar sua origem, pois todos os componentes são integrados e testados simultaneamente. Diferentemente das estratégias incrementais, não existem etapas intermediárias que permitam isolar progressivamente os problemas de integração.

### 2.2 Integração Incremental Top-Down (Descendente) com uso de Stubs

![Diagrama Top-Down com Stub](diagramas/2.2-teste-integracao-top-down-stub.svg)

#### Explicação Textual

Nesta abordagem, o objetivo é testar o módulo de alto nível `ServicoProcessamentoEvidencia`, responsável por orquestrar o fluxo de validação de evidências fotográficas, realizando a integração de forma incremental e descendente (Top-Down). Diferentemente da abordagem 2.1 (Big Bang), os componentes são integrados progressivamente, partindo do módulo de alto nível em direção aos componentes de baixo nível.

No cenário representado, `ServicoProcessamentoEvidencia` está integrado com `ServicoOCR` e `ValidadorMetragem`, responsáveis respectivamente por extrair o texto da fotografia e validar a metragem identificada. O `RepositorioEvidencias` real ainda não está disponível, sendo substituído pela implementação `RepositorioEvidenciasStub` da interface `IRepositorioEvidencias`.

O `RepositorioEvidenciasStub` simula a persistência em memória e permite controlar o comportamento do teste por meio de `configurarFalhaPersistencia()`. Dessa forma, é possível testar tanto o fluxo de sucesso quanto o comportamento do sistema diante de uma falha de persistência, sem depender de um banco de dados real.

Essa etapa representa parte de uma integração Top-Down maior. Conforme os componentes de baixo nível ficam disponíveis e são validados, os respectivos Stubs podem ser substituídos pelas implementações reais até que toda a cadeia esteja integrada.

O principal objetivo é verificar se `ServicoProcessamentoEvidencia` coordena corretamente as chamadas entre os componentes e trata adequadamente seus retornos. O teste pode revelar problemas como chamadas fora de ordem, tratamento incorreto de falhas de persistência ou dependências indevidas em detalhes do repositório.



### 2.3 Integração Incremental Bottom-Up (Ascendente) com uso de Drivers

![Diagrama Bottom-Up com Driver](diagramas/2.3-teste-integracao-bottom-up-driver.svg)

#### Explicação Textual

Nesta abordagem, o objetivo é testar a integração entre os componentes de baixo nível `ServicoOCR` e `ValidadorMetragem`, que já estão implementados e testados, antes que o módulo de alto nível `ServicoProcessamentoEvidencia` esteja disponível. Diferentemente da abordagem 2.2 (Top-Down), a integração ocorre de baixo para cima, partindo dos componentes inferiores em direção ao módulo controlador.

Como o orquestrador ainda não está disponível, utiliza-se o `ValidadorMetragemDriver`, um artefato de teste que assume temporariamente o papel do módulo de alto nível. Por meio do método `executarCenarioDeTeste(foto: String)`, o Driver aciona o `ServicoOCR`, que extrai o texto da fotografia, e posteriormente o `ValidadorMetragem`, que valida o texto extraído. Ao final, o Driver avalia o `ResultadoValidacao` retornado.

O `ServicoProcessamentoEvidencia` não aparece no diagrama propositalmente, pois ainda não está disponível nessa etapa. O Driver permite testar a integração entre os componentes reais sem depender da implementação do módulo superior.

A principal diferença em relação ao 2.2 é que, no Top-Down, utiliza-se um Stub para representar uma dependência inferior ainda não disponível. No Bottom-Up, utiliza-se um Driver para representar temporariamente o componente superior que ainda não foi implementado.

O principal objetivo do teste é verificar se o fluxo entre `ServicoOCR` e `ValidadorMetragem` funciona corretamente, identificando possíveis falhas de interface, como incompatibilidade no formato do texto extraído, tratamento incorreto de resultados vazios ou problemas na comunicação entre os componentes.

### 2.4 Teste de Fumaça (Smoke Testing)

![Diagrama Smoke Test](diagramas/2.4-teste-integracao-smoke-test.svg)

#### Explicação Textual

Nesta abordagem, o objetivo é verificar rapidamente, logo após um novo deploy, se o caminho crítico do fluxo de validação de evidências fotográficas está operacional, sem explorar múltiplos cenários, casos de borda ou situações de erro. Essa verificação superficial caracteriza o Teste de Fumaça (Smoke Test), cujo objetivo é determinar se o sistema está minimamente estável para prosseguir com testes mais aprofundados.

O cenário representa um `Tecnico` enviando, por meio da `InterfaceTecnico`, uma única fotografia de teste contendo a metragem válida `"1493 m"`. A interface encaminha a fotografia ao `ServicoOCR`, que extrai o texto e o retorna. Em seguida, o texto é enviado ao `ValidadorMetragem`, que retorna o resultado da validação. Por fim, o resultado é encaminhado ao `RepositorioEvidencias`, que confirma o armazenamento, permitindo que a interface exiba ao técnico a mensagem `"Foto aprovada"`.

O diagrama representa apenas o caminho principal (happy path), sem cenários de erro ou casos de borda. Essa escolha é proposital, pois o Smoke Test busca apenas confirmar que as principais etapas — captura, extração, validação e persistência — estão funcionando após o deploy.

O principal objetivo é identificar rapidamente falhas graves no fluxo principal, como serviços indisponíveis, problemas de configuração ou quebras de comunicação entre módulos. Caso o teste falhe, o sistema pode ser considerado instável e os testes mais detalhados não devem prosseguir até que o problema seja corrigido.

### 2.5 Teste de Regressão


![Diagrama Teste de Regressão](diagramas/2.5-teste-regressao.svg)

#### Explicação Textual

Nesta abordagem, o objetivo é garantir que uma alteração recente na classe `ValidadorMetragem` não tenha comprometido comportamentos que já funcionavam corretamente. Esse é o princípio central do Teste de Regressão: reexecutar testes existentes após uma alteração para verificar se as funcionalidades anteriores continuam operando como esperado.

A alteração realizada consiste em ampliar o método `validarTexto(texto: String): ResultadoValidacao` para reconhecer também metragens no formato decimal, como `"1.493 m"`, além dos formatos inteiros já suportados, como `"1493 m"` e `"850 m"`.

Após essa mudança, a `ValidadorMetragemRegressaoSuite <<Test>>` executa novamente os casos existentes e também o novo cenário. Os métodos `testarMetragemInteira()` e `testarOutraMetragemInteira()` representam casos que já eram suportados e devem continuar passando. `testarTextoSemMetragem()` verifica que textos sem uma metragem válida continuam sendo reprovados. Já `testarMetragemDecimal()` verifica a nova funcionalidade adicionada.

O relacionamento `ValidadorMetragemRegressaoSuite ..> ValidadorMetragem : testa` representa a execução desses cenários sobre a classe modificada, enquanto `ValidadorMetragem ..> ResultadoValidacao : retorna` representa o resultado produzido pela validação.

O principal objetivo do teste é identificar regressões, ou seja, comportamentos que funcionavam antes da alteração e passaram a falhar depois dela. Por exemplo, uma mudança na lógica de reconhecimento do formato decimal poderia fazer com que uma metragem inteira anteriormente válida deixasse de ser reconhecida. A suíte de regressão permite detectar esse tipo de efeito colateral antes que a alteração avance para as próximas etapas de teste.