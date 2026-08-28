# Registro de Prompts Utilizados — Laboratório 02

Este arquivo documenta os prompts utilizados com assistentes de IA generativa durante a construção dos diagramas deste estudo de caso, conforme exigido pela Política de Uso de IA do laboratório.

---

## 1.1 — Verificação de lógica atômica em componente/classe isolada

**Ferramenta**: Claude (Anthropic)
**Objetivo**: Gerar o diagrama de classes inicial para o teste de unidade da classe `ValidadorMetragem`.
### Prompt
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

---

## 2.1 — Integração Não Incremental (Big Bang)

**Ferramenta**: Gemini (Google)
**Objetivo**: Gerar o diagrama de componentes para a integração Big Bang do fluxo de validação de evidências fotográficas.
### Prompt
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

---

## 2.2 — Integração Incremental Top-Down (Descendente) com uso de Stubs

**Ferramenta**: Gemini (Google)
**Objetivo**: Gerar o diagrama de classes representando a integração Top-Down de `ServicoProcessamentoEvidencia`, com `ServicoOCR` e `ValidadorMetragem` já reais e `RepositorioEvidencias` substituído por um Stub.
### Prompt
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

---

## 2.3 — Integração Incremental Bottom-Up (Ascendente) com uso de Drivers

**Ferramenta**: Gemini (Google)
**Objetivo**: Gerar o diagrama de classes representando a integração Bottom-Up entre `ServicoOCR` e `ValidadorMetragem`, com o módulo controlador `ServicoProcessamentoEvidencia` ainda inexistente e substituído por um Driver.
### Prompt
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

---

## 2.4 — Teste de Fumaça (Smoke Testing)

**Ferramenta**: Claude (Anthropic)
**Objetivo**: Gerar o diagrama de sequência representando o fluxo crítico do sistema sendo verificado logo após um deploy.
### Prompt
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
---

## 2.5 — Teste de Regressão

**Ferramenta**: Gemini (Google)
**Objetivo**: Gerar o diagrama de classes representando a suíte de regressão reexecutando casos existentes e novos sobre `ValidadorMetragem` após uma alteração.
### Prompt
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

---

## 3.1 — Critérios de Aceitação (User Acceptance Testing)

**Ferramenta**: Gemini (Google)
**Objetivo**: Gerar o diagrama de sequência representando o Supervisor de Campo validando o critério de aceitação da evidência fotográfica, sob a perspectiva do usuário de negócio.
### Prompt
```text
Atue como um engenheiro de software e engenheiro de testes sênior.
Estou desenvolvendo um estudo de caso sobre estratégias e níveis de teste para um Sistema Inteligente de Validação de Evidências Fotográficas para serviços de telecomunicações.
Para o nível Teste de Validação, especificamente a abordagem Critérios de Aceitação (User Acceptance Testing), gere um diagrama de sequência em sintaxe PlantUML válido.
O cenário é o seguinte:
Um critério de aceitação definido junto ao negócio estabelece: "Dado que existe uma fotografia legível de um cabo contendo a metragem 1493 m, quando o Supervisor de Campo solicita a validação da evidência, então o sistema deve identificar 1493 m e apresentar a evidência como aprovada".
O teste é executado pelo SupervisorDeCampo, representando o usuário de negócio que aprova a evidência para fins de medição, sem se preocupar com os detalhes técnicos internos do sistema.
O diagrama deve representar:
- Um ator SupervisorDeCampo, que solicita a validação e recebe o resultado;
- O sistema representado de forma simplificada como um único participante SistemaValidacaoEvidencia (sem detalhar OCR, validador ou repositório internamente);
- A interação: o Supervisor solicita a validação da evidência, o sistema processa a fotografia e retorna o resultado (metragem identificada e status de aprovação), e o Supervisor aprova a evidência;
- Uma nota com o critério de aceitação no formato Dado/Quando/Então (Gherkin);
- Uma segunda nota reforçando que este teste valida a experiência e a necessidade do usuário de negócio, não os detalhes técnicos internos do sistema.
Não utilize o ator Tecnico neste diagrama — o foco é a perspectiva do Supervisor de Campo como responsável pela aceitação da evidência.
Utilize nomes em português e mantenha o diagrama simples, coerente com a proposta de um teste de aceitação do usuário.
Formate a resposta somente como um bloco de código PlantUML, sem explicações adicionais.
```

---

## 3.2 — Teste Alfa (Alpha Testing)

**Ferramenta**: Gemini (Google)
**Objetivo**: Gerar o diagrama de sequência representando a equipe interna de QA testando múltiplos cenários sobre o sistema, de forma exploratória, em ambiente controlado.
### Prompt
```text
Atue como um engenheiro de software e engenheiro de testes sênior.
Estou desenvolvendo um estudo de caso sobre estratégias e níveis de teste para um Sistema Inteligente de Validação de Evidências Fotográficas para serviços de telecomunicações.
Para o nível Teste de Validação, especificamente a abordagem Teste Alfa (Alpha Testing), gere um diagrama de sequência em sintaxe PlantUML válido.
O cenário é o seguinte:
Antes do lançamento para os usuários finais, uma equipe interna de QA (representada pelo ator AnalistaQA) realiza testes exploratórios no sistema completo, em ambiente controlado, simulando o uso real do técnico em campo. Diferente do Teste de Aceitação (UAT), que valida um único critério formal já combinado com o negócio, o Teste Alfa explora múltiplos cenários de uso, incluindo casos de sucesso e de falha, para identificar defeitos antes da liberação externa.
O diagrama deve representar:
- Um ator AnalistaQA, representando a equipe interna que realiza o teste;
- O sistema representado de forma simplificada como um participante único SistemaValidacaoEvidencia (sem detalhar OCR, validador ou repositório internamente, mantendo o foco no comportamento observável);
- Ao menos dois cenários explorados pelo AnalistaQA usando blocos alt/else do PlantUML: um cenário de sucesso (fotografia legível, com metragem válida, sistema aprova a evidência) e um cenário de falha (fotografia ilegível ou sem metragem identificável, sistema reprova a evidência);
- Uma nota explicando que o Teste Alfa é realizado por uma equipe interna, em ambiente controlado, de forma exploratória, antes da liberação para usuários externos.
Utilize nomes em português e mantenha o diagrama coerente com a proposta de um teste alfa interno e exploratório.
Formate a resposta somente como um bloco de código PlantUML, sem explicações adicionais.
```

---

## 3.3 — Teste Beta (Beta Testing)

**Ferramenta**: Claude (Anthropic)
**Objetivo**: Gerar o diagrama de sequência representando um técnico real usando o sistema em campo e reportando feedback, sem cenários pré-definidos.
### Prompt
```text
Atue como um engenheiro de software e engenheiro de testes sênior.
Estou desenvolvendo um estudo de caso sobre estratégias e níveis de teste para um Sistema Inteligente de Validação de Evidências Fotográficas para serviços de telecomunicações.
Para o nível Teste de Validação, especificamente a abordagem Teste Beta (Beta Testing), gere um diagrama de sequência em sintaxe PlantUML válido.
O cenário é o seguinte:
Após a conclusão do Teste Alfa (interno e controlado), o sistema é liberado para um grupo piloto de técnicos reais (ator TecnicoReal), que passam a utilizá-lo em condições reais de campo durante suas atividades normais de instalação de cabos, sem cenários pré-definidos por uma equipe de QA. Durante o uso real, o técnico pode encontrar um problema (ex: o sistema demora para responder, ou identifica incorretamente uma metragem em uma condição real de iluminação) e reporta esse problema por meio de um canal de feedback.
O diagrama deve representar:
- Um ator TecnicoReal, representando um usuário real do grupo piloto;
- O sistema representado de forma simplificada como um participante único SistemaValidacaoEvidencia;
- Um participante CanalFeedback, representando o mecanismo pelo qual o técnico reporta problemas ou sugestões percebidos durante o uso real;
- O fluxo: o técnico usa o sistema normalmente em campo (envia uma fotografia real), o sistema retorna um resultado, e o técnico, ao perceber um problema ou ponto de melhoria durante esse uso real, reporta o feedback ao CanalFeedback;
- Uma nota explicando que o Teste Beta ocorre em ambiente real, com usuários reais (não simulados), após a conclusão do Teste Alfa, e que o foco está na coleta de feedback genuíno de uso, não em cenários pré-roteirizados.
Utilize nomes em português e mantenha o diagrama coerente com a proposta de um teste beta com usuários reais em ambiente de produção.
Formate a resposta somente como um bloco de código PlantUML, sem explicações adicionais.
```

---

## 4.1 — Teste de Recuperação (Recovery Testing)

**Ferramenta**: Claude (Anthropic)
**Objetivo**: Gerar o diagrama de sequência representando a falha temporária do `RepositorioEvidencias` e a recuperação do sistema por meio de reenvio, sem perda do resultado.
### Prompt
```text
Atue como um engenheiro de software e engenheiro de testes sênior.
Estou desenvolvendo um estudo de caso sobre estratégias e níveis de teste para um Sistema Inteligente de Validação de Evidências Fotográficas para serviços de telecomunicações.
Para o nível Teste de Sistema, especificamente a abordagem Teste de Recuperação (Recovery Testing), gere um diagrama de sequência em sintaxe PlantUML válido.
O cenário é o seguinte:
Durante o processamento de uma evidência fotográfica, o ServicoProcessamentoEvidencia envia o resultado da validação ao RepositorioEvidencias para persistência. Nesse momento, o RepositorioEvidencias sofre uma queda abrupta (indisponibilidade do banco de dados) e não consegue confirmar o armazenamento. O sistema detecta essa falha, evita a perda do resultado (por exemplo, mantendo-o em uma fila de reenvio) e, quando o RepositorioEvidencias volta a ficar disponível, a persistência é concluída com sucesso automaticamente, sem exigir que o técnico refaça o processo.
O diagrama deve representar:
- O participante ServicoProcessamentoEvidencia, enviando o resultado da validação para persistência;
- O participante RepositorioEvidencias, que inicialmente falha (queda abrupta) e depois se recupera;
- Uma indicação visual da falha (ex: usando alt/else ou uma nota destacando o momento da queda);
- O mecanismo de recuperação: nova tentativa de envio (retry) após o RepositorioEvidencias voltar a responder, concluindo a persistência com sucesso;
- Uma nota explicando que o objetivo do Teste de Recuperação é verificar se o sistema volta a operar corretamente após uma falha, sem perda de dados.
Utilize nomes em português e mantenha o diagrama coerente com a proposta de um teste de recuperação após falha de um componente.
Formate a resposta somente como um bloco de código PlantUML, sem explicações adicionais.
```

---

## 4.2 — Teste de Segurança (Security Testing)

**Ferramenta**: Claude (Anthropic)
**Objetivo**: Gerar o diagrama de sequência representando a tentativa de acesso não autorizado sendo bloqueada pelo `ServicoAutenticacao`, antes de alcançar os dados protegidos.
### Prompt
```text
Atue como um engenheiro de software e engenheiro de testes sênior.
Estou desenvolvendo um estudo de caso sobre estratégias e níveis de teste para um Sistema Inteligente de Validação de Evidências Fotográficas para serviços de telecomunicações.
Para o nível Teste de Sistema, especificamente a abordagem Teste de Segurança (Security Testing), gere um diagrama de sequência em sintaxe PlantUML válido.
O cenário é o seguinte:
Um UsuarioNaoAutorizado tenta acessar as evidências fotográficas armazenadas no SistemaValidacaoEvidencia sem possuir credenciais válidas. A requisição passa primeiro pelo ServicoAutenticacao, responsável por verificar o token/credencial do usuário. Como o usuário não está autenticado (ou não possui permissão suficiente), o ServicoAutenticacao rejeita a requisição antes que ela alcance os dados protegidos, retornando um erro de acesso negado, sem expor nenhuma informação sensível do sistema.
O diagrama deve representar:
- Um ator UsuarioNaoAutorizado, tentando acessar o sistema;
- O participante ServicoAutenticacao, responsável por validar credenciais;
- O participante SistemaValidacaoEvidencia, protegido pela autenticação;
- O fluxo: a tentativa de acesso, a verificação de credenciais falhando, e o retorno de um erro de acesso negado — sem que a requisição chegue a acessar dados do SistemaValidacaoEvidencia;
- Uma nota explicando que o objetivo do Teste de Segurança é verificar se o sistema protege corretamente seus dados contra acessos não autorizados.
Utilize nomes em português e mantenha o diagrama coerente com a proposta de um teste de segurança contra acesso indevido.
Formate a resposta somente como um bloco de código PlantUML, sem explicações adicionais.
```

---

## 4.3 — Teste de Estresse (Stress Testing)

**Ferramenta**: Gemini (Google)
**Objetivo**: Gerar o diagrama de sequência representando um pico de requisições realista (fechamento diário de medições/LPU) sobrecarregando o `SistemaValidacaoEvidencia`.
### Prompt
```text
Atue como um engenheiro de software e engenheiro de testes sênior.
Estou desenvolvendo um estudo de caso sobre estratégias e níveis de teste para um Sistema Inteligente de Validação de Evidências Fotográficas para serviços de telecomunicações.
Para o nível Teste de Sistema, especificamente a abordagem Teste de Estresse (Stress Testing), gere um diagrama de sequência em sintaxe PlantUML válido.
O cenário é o seguinte:
Durante o fechamento diário das atividades de medição/LPU, diversos técnicos enviam várias fotografias de evidências em um curto período de tempo (por exemplo, 50 técnicos enviando cerca de 10 fotografias cada, totalizando um pico de aproximadamente 500 requisições em um curto intervalo). Esse volume fica muito acima da carga normal esperada pelo SistemaValidacaoEvidencia, gerando um pico de processamento. O objetivo do teste é observar como o sistema se comporta sob essa sobrecarga: se degrada de forma controlada (enfileirando requisições e respondendo com maior latência) ou se apresenta falhas graves (indisponibilidade total).
O diagrama deve representar:
- Um participante GeradorDeCarga, representando a ferramenta de teste que simula esse pico de requisições (utilize um bloco loop do PlantUML para representar o envio de múltiplas requisições em um curto intervalo);
- O participante SistemaValidacaoEvidencia, recebendo esse volume de requisições;
- A resposta do sistema sob a sobrecarga: uma nota ou mensagem indicando enfileiramento de requisições e aumento de latência, representando degradação controlada, em vez de falha total;
- Uma nota explicando que o pico simulado representa o volume acumulado de vários técnicos enviando evidências durante o fechamento diário, e não um número irreal de usuários conectados simultaneamente;
- Outra nota explicando que o objetivo do Teste de Estresse é verificar os limites de capacidade do sistema e como ele se comporta além da carga esperada.
Utilize nomes em português e mantenha o diagrama coerente com a proposta de um teste de estresse baseado em um pico realista de requisições.
Formate a resposta somente como um bloco de código PlantUML, sem explicações adicionais.
```

---

## 4.4 — Teste de Desempenho (Performance Testing)

**Ferramenta**: Gemini (Google)
**Objetivo**: Gerar o diagrama de sequência representando a medição do tempo de resposta do sistema sob condições normais de uso, comparando com um requisito de desempenho definido.
### Prompt
```text
Atue como um engenheiro de software e engenheiro de testes sênior.
Estou desenvolvendo um estudo de caso sobre estratégias e níveis de teste para um Sistema Inteligente de Validação de Evidências Fotográficas para serviços de telecomunicações.
Para o nível Teste de Sistema, especificamente a abordagem Teste de Desempenho (Performance Testing), gere um diagrama de sequência em sintaxe PlantUML válido.
O cenário é o seguinte:
Sob condições normais de uso (carga esperada, não sobrecarregada), um Tecnico envia uma fotografia ao SistemaValidacaoEvidencia, e o sistema deve processá-la e retornar o resultado dentro de um requisito de desempenho previamente definido: no máximo 3 segundos, do envio da fotografia até a exibição do resultado. O teste mede o tempo de resposta do sistema e verifica se ele atende a esse requisito.
O diagrama deve representar:
- Um ator Tecnico, enviando uma fotografia sob condições normais de uso (uma única requisição, sem sobrecarga);
- O participante SistemaValidacaoEvidencia, processando a fotografia e retornando o resultado;
- Uma indicação de medição de tempo (ex: notas indicando o início e o fim da medição, e o tempo total decorrido, comparado ao limite de 3 segundos);
- Uma nota explicando que o objetivo do Teste de Desempenho é verificar se o sistema atende aos requisitos de tempo de resposta sob condições normais de uso, diferenciando-se do Teste de Estresse (4.3), que avalia o comportamento sob carga excessiva e propositalmente além do normal.
Utilize nomes em português e mantenha o diagrama coerente com a proposta de um teste de desempenho medindo tempo de resposta sob carga normal.
Formate a resposta somente como um bloco de código PlantUML, sem explicações adicionais.
```
