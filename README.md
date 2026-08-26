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