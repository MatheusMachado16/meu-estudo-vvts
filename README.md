# Laboratório 02 — Estratégias e Níveis de Teste na Prática

## Sistema escolhido: Sistema Inteligente de Validação de Evidências Fotográficas

Para a realização da atividade foi escolhido como domínio um **sistema inteligente de validação de evidências fotográficas para serviços de telecomunicações**. O sistema tem como objetivo analisar fotografias utilizadas para comprovar a execução de serviços técnicos, verificando principalmente se a **metragem do cabo está visível e legível**.

A arquitetura considerada utiliza um **aplicativo para envio das fotografias**, um módulo de **visão computacional e OCR** para identificação dos textos presentes na imagem e um **módulo de validação**, responsável por diferenciar a metragem de outras informações, como coordenadas, data, horário e número da O.S.

Ao final da análise, a fotografia é classificada como **aprovada**, quando a metragem é identificada corretamente, ou **reprovada**, quando não é possível confirmar a metragem da evidência.

Os cenários apresentados neste laboratório representam um **sistema conceitual em desenvolvimento**, utilizado exclusivamente para modelar e demonstrar as diferentes estratégias e níveis de teste solicitados na atividade.

---

## 1. Teste de Unidade

### 1.1 Verificação de lógica atômica em componente/classe isolada
