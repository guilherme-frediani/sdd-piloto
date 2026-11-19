# Brief: CTL DocAnswer

| Campo | Valor |
| --- | --- |
| Versão | 0.6 |
| Templates | v1.1 |
| Data | 2026-11-19 |
| Dono (PO) | Guilherme |
| Cliente / Sponsor | Lucas (cliente e Sponsor) |
| Tech Lead, Engenheiro de IA/ML, QA e Dev | Rafael (acúmulo de papéis: ver P26) |
| Segurança e Compliance | Guilherme e Rafael, em par (acúmulo de papéis: ver P26) |
| Status | rascunho |

## 1. Problema e objetivo

<!-- Gate: G0 · Preenche: PO -->

Hoje, para responder a perguntas sobre o conteúdo de um conjunto de documentos, o cliente precisa fazer uma análise manual: acessar repositórios, abrir pastas e documentos e consolidar a informação. Exemplo: para saber "Quantos projetos foram realizados em 2025?", é preciso abrir os documentos e contar os projetos à mão. Outro exemplo: para saber quais riscos um projeto levantou, é preciso localizar e ler os documentos desse projeto.

Para quem: o cliente, que é leigo em tecnologia e precisa de uma solução simples e direta. O desenvolvedor também é usuário: ele baixa as métricas de performance da solução.

**Objetivo:** permitir que o cliente envie documentos e obtenha, de forma simples, respostas às suas perguntas no mesmo idioma da pergunta (português ou inglês), baseadas por padrão apenas nos documentos presentes na conversa e acompanhadas da referência de origem, em texto ou como documento gerado no formato pedido, usando um modelo de IA executado localmente no hardware do usuário. A busca na internet só acontece quando o cliente pede explicitamente (caso 8).

**Entregável adicional (pedido na reunião de 2026-11-19, dentro do escopo):** pesquisa sobre as limitações e diferenças entre agentes de grandes empresas (ex.: Claude) e um modelo executado localmente. Pergunta-guia do cliente: "Por que teria um modelo rodando local em minha máquina ao invés de mandar tudo no Claude?". Responsável: Rafael. Formato e prazo em aberto (P32).

## 2. Usuários e casos de uso prioritários

<!-- Gate: G0 e G1 (alimenta o golden dataset) · Preenche: PO -->

| # | Usuário | Situação | Entrada de exemplo | Saída esperada |
| --- | --- | --- | --- | --- |
| 1 | Cliente (leigo em tecnologia) | Quer saber quantos projetos foram feitos em um ano, sem contar à mão | Envia a pasta com os documentos dos projetos e pergunta: "me diga quantos projetos foram feitos no ano de 2025" | Resposta em texto (linguagem natural) com a quantidade de projetos feitos em 2025, baseada apenas nos documentos enviados, com as referências usadas (ver regra de referência abaixo) |
| 2 | Cliente (leigo em tecnologia) | Quer detalhes sobre um projeto específico | "Quais foram os riscos levantados pelo projeto XYZ?" | Resposta em texto com os riscos do projeto XYZ que constam nos documentos enviados, com as referências usadas |
| 3 | Cliente (leigo em tecnologia) | Faz uma pergunta cuja resposta não está nos documentos enviados | Pergunta sobre informação ausente dos documentos | Resposta pré-cadastrada: "Desculpe, não consegui encontrar as informações para responder sua pergunta." O texto das respostas pré-cadastradas deve ser simples de editar, pois vai mudar durante o desenvolvimento. O sistema não busca na internet por conta própria: só quando o cliente pede (caso 8) |
| 4 | Cliente (leigo em tecnologia) | Quer o resultado como documento em vez de texto | "Gere um documento word a partir do que você me respondeu" ou "Apresente a resposta em powerpoint" | Documento no formato pedido pelo cliente (PDF, Word, Markdown, PowerPoint ou Excel) com o conteúdo da resposta, baseado apenas nos documentos enviados |
| 5 | Desenvolvedor | Quer comparar a performance desta solução (modelo local) com soluções que usam modelos em nuvem. A comparação é feita por ele, fora do sistema | Acessa os dados de performance direto pela interface, não pelo chat, e escolhe o formato do download (CSV ou JSON). Os dados ficam visíveis para qualquer usuário: não há autenticação, pois a aplicação roda apenas localmente | Arquivo CSV ou JSON com, no mínimo: tempo de processamento, tempo de resposta e acerto (a partir do feedback dado a cada resposta). A lista final de métricas ainda será acordada com o cliente |
| 6 | Cliente (leigo em tecnologia) | **Fora da primeira versão (desejável).** Quer perguntar sobre o conteúdo de uma imagem | Envia um screenshot de um slide de PowerPoint e faz uma pergunta sobre o que aparece nele | Resposta em texto baseada no conteúdo da imagem, com a referência usada. Detalhes adiados para quando a funcionalidade entrar (P34) |
| 7 | Cliente (leigo em tecnologia) | Pergunta em um idioma sobre documentos em outro | Pergunta em inglês sobre documentos em português (ou o inverso) | Resposta correta, com referências, sempre no idioma da pergunta, independentemente do idioma do documento |
| 8 | Cliente (leigo em tecnologia) | Quer que a resposta use informação da internet | Pede explicitamente, na pergunta, que o sistema pesquise na internet | Resposta baseada na pesquisa na internet, com a URL de cada site usado como referência. Nenhum conteúdo dos documentos sai da máquina na consulta. Sem esse pedido explícito, o sistema busca apenas nos documentos da conversa. Se a resposta pode combinar internet e documentos está em aberto (P31) |

**Regra de referência (casos 1, 2, 4 e 8):** toda resposta vem acompanhada da referência usada para respondê-la.

- Documento (PDF, Word): nome do arquivo e página, quando possível.
- Planilha: apenas o nome da planilha.
- Apresentação: nome do arquivo e página (slide).
- Markdown: apenas o nome do arquivo.
- Internet: apenas a URL do site usado para obter a informação.

**Feedback:** o usuário pode dar feedback sobre cada resposta recebida. Esse feedback alimenta a métrica de acerto. O formato do feedback será definido com o cliente (P22).

**Guardrails:** a solução deve ter guardrails. O PO definiu como mínimo as três categorias abaixo. Na reunião de 2026-11-19 o cliente classificou guardrails como prioridade baixa e disse que não tem definição do que é essencial; se G-1 a G-3 seguem obrigatórios na primeira entrega está em aberto (P35). Os demais guardrails serão definidos depois (P27). Em cada guardrail, o sistema responde com uma resposta pré-cadastrada e simples de editar, como no caso 3. O texto de cada resposta está em aberto (P29).

| # | Categoria | Entrada de exemplo | Saída esperada |
| --- | --- | --- | --- |
| G-1 | Pergunta imprópria | Pergunta contendo palavrão | Resposta pré-cadastrada e editável; texto em aberto (P29) |
| G-2 | Pergunta genérica, não ligada aos documentos | "Quem é o presidente do Brasil?" | Resposta pré-cadastrada e editável; texto em aberto (P29). Vale mesmo quando o cliente pede explicitamente uma busca na internet: a pergunta genérica não é respondida |
| G-3 | Pergunta sobre a própria IA | "Qual modelo você usa?", "Quem te criou?" | Resposta pré-cadastrada e editável; texto em aberto (P29) |

## 3. Critérios de sucesso

<!-- Gate: G0 · Preenche: PO com o cliente · QA confirma os critérios do sistema; Engenheiro de IA/ML confirma os critérios de modelo -->

Nenhum critério tem forma de medir nem limiar definidos ainda (P9). Na reunião de 2026-11-19 o cliente informou que não tem definição de critérios de sucesso nem de métricas que deseja acompanhar. Como o G0 exige que o cliente valide os critérios, eles precisam ser propostos ao cliente (P9).

| Critério | Como se mede | Limiar | Quem valida |
| --- | --- | --- | --- |
| Respostas baseadas apenas nos documentos enviados | Pergunta em aberto (P9) | Pergunta em aberto (P9) | Engenheiro de IA/ML |
| Respostas corretas quando a informação está nos documentos (ex.: contagem de projetos de 2025, riscos do projeto XYZ) | Pergunta em aberto (P9) | Pergunta em aberto (P9) | Engenheiro de IA/ML |
| Resposta pré-cadastrada quando a informação não está nos documentos | Pergunta em aberto (P9) | Pergunta em aberto (P9) | Engenheiro de IA/ML |
| Perguntas em português e em inglês entendidas e respondidas sempre no idioma da pergunta | Pergunta em aberto (P9) | Pergunta em aberto (P9) | Engenheiro de IA/ML |
| Busca na internet só quando o cliente pede explicitamente, com a URL como referência | Pergunta em aberto (P9) | Pergunta em aberto (P9) | QA |
| Nenhum conteúdo dos documentos sai da máquina, inclusive nas consultas de busca na internet | Pergunta em aberto (P9) | Pergunta em aberto (P9) | Segurança e Compliance |
| Guardrails G-1, G-2 e G-3 acionados nas perguntas impróprias, genéricas e sobre a IA (prioridade em aberto, P35) | Pergunta em aberto (P9) | Pergunta em aberto (P9) | Engenheiro de IA/ML |
| Toda resposta acompanhada da referência, conforme a regra da seção 2 | Pergunta em aberto (P9) | Pergunta em aberto (P9) | QA |
| Nenhuma chamada a modelo hospedado em nuvem | Pergunta em aberto (P9) | Pergunta em aberto (P9) | QA |
| Geração de documento no formato pedido (PDF, Word, Markdown, PowerPoint ou Excel) a partir da resposta | Pergunta em aberto (P9) | Pergunta em aberto (P9) | QA |
| Exportação das métricas em CSV e em JSON | Pergunta em aberto (P9) | Pergunta em aberto (P9) | QA |
| Uso simples e direto por um cliente leigo em tecnologia | Pergunta em aberto (P9) | Pergunta em aberto (P9) | PO com o cliente |

## 4. Fora de escopo

<!-- Gate: G0 · Preenche: PO -->

- Uso de qualquer modelo de IA hospedado em nuvem.
- Respostas baseadas em informação que não esteja nos documentos enviados, exceto quando o cliente pede explicitamente uma busca na internet (caso 8).
- Busca na internet por iniciativa do sistema.
- Comparação automática com soluções em nuvem. O sistema só exporta as métricas; a comparação é feita pelo desenvolvedor.
- Interpretação de imagens, na primeira versão. É funcionalidade desejável pelo cliente para versões futuras (caso 6).
- Processamento de vídeos.
- Perguntas por voz.
- Respostas da IA por voz.
- Integração com WhatsApp, Teams e Outlook.
- Autenticação. A aplicação roda apenas localmente, e as métricas de performance ficam visíveis para qualquer usuário.

## 5. Dados e data contracts

<!-- Gate: G0 · Preenche: PO (lista), Data Owner (contratos) -->

| Fonte | Data Owner | Contrato assinado? | Dados pessoais? | Link para o contrato |
| --- | --- | --- | --- | --- |
| Documentos enviados pelo cliente (pastas e arquivos; ex.: documentos de projetos) | Lucas (cliente) | não | Pergunta em aberto (P12) | Não existe ainda (`sdd/data-contracts/DC-001-<fonte>.md`) |

**Os dados do cliente são secretos:** o cliente não enviará os seus documentos à equipe (reunião de 2026-11-19). Eles só serão processados na máquina do cliente e não estarão disponíveis para desenvolvimento nem para avaliação (ver P24).

Formatos aceitos no envio, em ordem de prioridade de implementação (1 = primeiro a implementar):

1. PDF
2. Word
3. Markdown
4. PowerPoint
5. Excel

A ordem diz respeito apenas à ordem de implementação. Os documentos gerados pelo sistema saem no formato pedido pelo cliente, entre os mesmos cinco formatos, implementados na mesma ordem de prioridade. Imagens ficam fora da primeira versão. Limite de tamanho e de quantidade em aberto (P13).

Idiomas: documentos, perguntas e respostas em português e em inglês. A resposta sai sempre no idioma da pergunta, mesmo quando o documento está em outro idioma.

Há dados para montar o golden dataset? **Não.** Não existe hoje conjunto de documentos com perguntas e respostas corretas, e o cliente não enviará os seus documentos. O golden dataset terá de ser montado com documentos substitutos (P24).

**Insumo opcional:** nenhuma avaliação de prontidão dos dados foi informada.

## 6. Restrições e riscos

<!-- Gate: G0 · Preenche: PO · Segurança e Compliance sinaliza o que é seu -->

| Tema | Restrição ou risco | Responsável |
| --- | --- | --- |
| Privacidade e LGPD | Os documentos do cliente são secretos e não serão compartilhados com a equipe. Presença de dados pessoais em aberto (P12). Restrição: a solução deve garantir que os documentos não saiam da máquina, inclusive nas consultas da busca na internet pedida pelo cliente (caso 8) | Segurança e Compliance |
| Conformidade | A definir (P15) | Segurança e Compliance |
| Guardrails | O mínimo definido pelo PO está na seção 2 (G-1 a G-3). O cliente classificou guardrails como prioridade baixa e não tem definição do que é essencial (P27, P35). Guardrails são item de veto de Segurança e Compliance (Guilherme e Rafael) | Segurança e Compliance |
| Custo esperado | Pergunta em aberto (P16) | Tech Lead |
| Latência esperada | Pergunta em aberto (P16) | Tech Lead |
| Técnico | O modelo de IA deve ser executado localmente, no hardware do usuário; nenhum modelo hospedado em nuvem pode ser usado. A solução é uma aplicação web local. Especificação mínima do hardware em aberto (P17). Se imagens entrarem numa versão futura, será preciso modelo local com suporte a imagem, o que pesa no hardware | Tech Lead |
| Técnico (acesso à internet) | A busca na internet sob pedido explícito (caso 8) precisa de acesso à rede e de um provedor de busca, ainda não escolhido (P31). A consulta enviada ao provedor não pode conter conteúdo dos documentos. É permissão de agente sob veto de Segurança e Compliance | Tech Lead, Segurança e Compliance |
| Técnico (avaliação) | Não existe golden dataset, e os documentos reais do cliente não estarão disponíveis. Sem golden dataset, os critérios de modelo não podem ser medidos; com documentos substitutos, a performance medida pode não refletir a performance nos documentos reais (P24) | Engenheiro de IA/ML |
| Cliente (disponibilidade, escopo) | Cliente leigo em tecnologia: a interface precisa ser simples e direta. O cliente não tem critérios de sucesso nem métricas definidos, o que trava o G0 até que uma proposta seja validada por ele (P7, P9) | PO |
| Papéis | Rafael acumula Dev, Tech Lead, QA, Engenheiro de IA/ML e Segurança e Compliance (em par com Guilherme), o que conflita com a regra "quem implementa nunca é quem mede nem quem aceita". Guilherme acumula PO e Segurança e Compliance. Com isso, quem implementa também tem veto sobre guardrails e permissões dos agentes (P26) | PO |

## 7. Parecer de viabilidade

<!-- Gate: G0 · Preenche: Tech Lead, QA, Engenheiro de IA/ML, Segurança e Compliance -->

- **Tech Lead:** pendente.
- **QA:** pendente.
- **Engenheiro de IA/ML:** pendente.
- **Segurança e Compliance:** pendente.

## 8. Premissas e perguntas em aberto

<!-- Gate: G0 · Preenche: qualquer papel -->

| # | Premissa ou pergunta | Dono | Prazo |
| --- | --- | --- | --- |
| P7 | Lista final de métricas de performance, além das mínimas (tempo de processamento, tempo de resposta, acerto). O cliente não tem métricas definidas (2026-11-19): quem monta a proposta para o cliente validar? | PO com o cliente | |
| P9 | Para cada critério de sucesso: como se mede e qual o limiar aceito? O cliente não tem critérios definidos (2026-11-19): a proposta precisa ser montada e validada por ele no G0 | PO com o cliente | |
| P12 | Os documentos enviados podem conter dados pessoais? | Segurança e Compliance | |
| P13 | Há limite de tamanho ou de quantidade de documentos? | PO com o cliente | |
| P15 | Requisitos de conformidade aplicáveis: a definir. | Segurança e Compliance | |
| P16 | Quais são as expectativas de custo e de tempo de resposta? | Tech Lead | |
| P17 | Qual é o hardware disponível no computador do usuário (CPU, GPU, memória)? Qual a configuração mínima a suportar? | Tech Lead | |
| P22 | Formato do feedback de cada resposta (ex.: certo/errado, nota): a definir com o cliente. | PO com o cliente | |
| P24 | O golden dataset não existe e o cliente não enviará os seus documentos (secretos). Quem o monta, com quais documentos substitutos (públicos ou sintéticos, nos formatos e idiomas do escopo), e quem rotula as respostas corretas? O cliente pode validar se os substitutos representam os documentos reais? | PO, Engenheiro de IA/ML | |
| P25 | Além da mensagem de informação não encontrada, há outras respostas pré-cadastradas previstas? | PO | |
| P26 | Rafael acumula Dev, Tech Lead, QA, Engenheiro de IA/ML e Segurança e Compliance (em par com Guilherme, que também é PO). A metodologia exige que quem implementa não seja quem mede nem quem aceita, e que o Tech Lead audite o evidence pack do Dev. Quem mede e audita, ou como essa regra é adaptada neste projeto? Mudança na metodologia exige commit `framework:` aprovado pelo Tech Lead | PO, Tech Lead | |
| P27 | Quais são os demais guardrails, além de G-1 a G-3? O cliente não tem definição do que é essencial (2026-11-19) | PO, Segurança e Compliance | |
| P29 | Qual é o texto da resposta pré-cadastrada de cada guardrail (G-1, G-2, G-3)? | PO | |
| P31 | Busca na internet sob pedido explícito (caso 8): qual provedor de busca? Quando o cliente pede a busca, a resposta usa só a internet ou combina internet e documentos? | Provedor: Tech Lead, Segurança e Compliance (veto). Combinação: PO com o cliente | |
| P32 | Pesquisa sobre agentes de grandes empresas (ex.: Claude) versus modelo local: qual o formato (documento, apresentação) e qual o prazo? | PO com o cliente, Rafael | |
| P35 | Com guardrails em prioridade baixa para o cliente, G-1 a G-3 continuam obrigatórios na primeira entrega? | PO; Segurança e Compliance (veto) | |

### Respostas registradas

Respondidas pelo PO (Guilherme) em 2026-11-16.

| # | Pergunta | Resposta |
| --- | --- | --- |
| P1 | Nome do projeto | CTL DocAnswer |
| P2 | PO, cliente e Sponsor | PO: Guilherme. Desenvolvedor: Rafael. Cliente: Lucas. Não há Sponsor (consequências em P20) |
| P3 | Perfil do usuário | Cliente leigo em tecnologia; a solução deve ser simples e direta. O download das métricas é necessidade do desenvolvedor |
| P4 | Resposta quando a informação não está nos documentos | Resposta pré-cadastrada: "Desculpe, não consegui encontrar as informações para responder sua pergunta." Respostas pré-cadastradas devem ser simples de editar |
| P5 | Exemplos de pedido de documento | "Gere um documento word a partir do que você me respondeu"; "Apresente a resposta em powerpoint" |
| P6 | Referência nas respostas | Toda resposta traz a referência: nome do arquivo e página quando possível; planilha, apenas a planilha; apresentação, a página |
| P7 | Métricas e formato | Mínimas: tempo de processamento, tempo de resposta e acerto (feedback do usuário por resposta); lista final a acordar com o cliente. Exportação em CSV ou JSON, à escolha. A comparação com soluções em nuvem é feita pelo usuário, não automaticamente |
| P8 | Outros casos de uso | Detalhes de um projeto específico, ex.: "Quais foram os riscos levantados pelo projeto XYZ?" |
| P10 | Fora de escopo | Processamento de imagens e vídeos, perguntas por voz, integração com WhatsApp, Teams e Outlook, autenticação (imagens revistas em 2026-11-19) |
| P11 | Data Owner | O próprio cliente (Lucas) |
| P13 | Formatos aceitos | PDF, Word, Excel, PowerPoint e Markdown; outros formatos a validar com o cliente |
| P14 | Golden dataset | Não existe (desdobramento em P24) |
| P15 | Conformidade | A definir |
| P18 | Forma de acesso | Aplicação web local |
| P19 | Idiomas | Documentos em português e inglês; idioma de perguntas e respostas a validar com o cliente |
| P20 | Sponsor | Lucas é o Sponsor, além de cliente |
| P21 (parcial) | Papéis | Tech Lead, Engenheiro de IA/ML e QA: Rafael. Segurança e Compliance segue em aberto |
| P22 | Formato do feedback | A definir com o cliente |
| P23 | Tempo de processamento | Sim: é o tempo de processamento dos documentos enviados |
| P4 (complemento) | Guardrails | A solução deve ter guardrails, no mínimo para perguntas impróprias (ex.: com palavrão), genéricas (ex.: "quem é o presidente do Brasil") e sobre a IA (ex.: "qual modelo você usa", "quem te criou"). Os demais serão definidos depois |
| Correção do caso 5 | Acesso às métricas | O desenvolvedor encontra os dados de performance direto na interface; não os pede pelo chat |
| Complemento P10 | Fora de escopo | Respostas da IA por voz |
| P30 | Visibilidade das métricas | Os dados de performance ficam visíveis na interface para qualquer usuário. Não há autenticação porque a aplicação roda apenas localmente |
| P28 | Resposta dos guardrails | Resposta pré-cadastrada e editável em cada guardrail, como no caso 3 |

Registradas pelo PO (Guilherme) a partir da reunião com o cliente (Lucas) em 2026-11-19.

| # | Pergunta | Resposta |
| --- | --- | --- |
| P13 | Formatos aceitos e prioridade | Ordem de prioridade: PDF, Word, Markdown, PowerPoint, Excel (PDF é o mais prioritário). Nenhum formato de documento novo pedido. Limite de tamanho e quantidade segue em aberto |
| P19 | Idioma de perguntas e respostas | O sistema tem que entender e responder em português e em inglês (regra do idioma da resposta em P33) |
| P7, P9 | Critérios de sucesso e métricas | O cliente não tem definição de critérios de sucesso nem de métricas que deseja acompanhar |
| P27 | Guardrails | Prioridade baixa para o cliente; não há definição do que é essencial (desdobramento em P35) |
| P24 (complemento) | Dados do cliente | O cliente não enviará os seus dados: são secretos |
| P10 (revisão) | Imagens | O cliente quer que imagens sejam interpretadas pelo modelo (ex.: screenshot de slide de PowerPoint). Imagens saem do fora de escopo; vídeos continuam fora (desdobramento em P34) |
| Novo pedido | Acesso à internet | O cliente quer que o modelo acesse a internet caso seja necessário pesquisar (conflitos em P31) |
| Novo pedido | Pesquisa local versus nuvem | O cliente quer uma pesquisa sobre as limitações e diferenças entre agentes de grandes empresas (ex.: Claude) e modelo local: "Por que teria um modelo rodando local em minha máquina ao invés de mandar tudo no Claude?" (desdobramento em P32) |

Registradas pelo PO (Guilherme) em 2026-11-19, após retorno do cliente.

| # | Pergunta | Resposta |
| --- | --- | --- |
| P31 (parcial) | Quando buscar na internet | Apenas quando o cliente solicitar. Por padrão, o sistema busca os dados apenas nos documentos presentes na conversa |
| P32 (parcial) | Pesquisa local versus nuvem | Responsável: Rafael. Faz parte do escopo. Formato a definir |
| P33 | Idioma da resposta | Sempre no idioma da pergunta, mesmo quando o documento está em outro idioma |
| P34 (parcial) | Imagens | Funcionalidade desejável pelo cliente, mas fora da primeira versão |
| P36 (parcial) | Formato dos documentos gerados | Os documentos gerados saem no formato pedido pelo cliente. A ordem de prioridade dos formatos diz respeito apenas à ordem de implementação |
| P21 | Segurança e Compliance | Guilherme e Rafael, em par |
| P31 (complemento) | Busca na internet | A solução deve garantir que os documentos não saiam da máquina. A referência é apenas a URL do site usado para obter a informação. Pergunta genérica (G-2) não é respondida, mesmo com pedido explícito de busca. Provedor de busca segue em aberto |
| P34 | Referência de Markdown | Apenas o nome do arquivo |
| P36 | Formatos de saída | Os mesmos da leitura, na mesma ordem de prioridade de implementação: PDF, Word, Markdown, PowerPoint, Excel |

## 9. Aprovação (G0)

<!-- Gate: G0 · O cliente e o Sponsor validam; os demais assinam o que é seu -->

| Papel | Nome | Aprova | Data |
| --- | --- | --- | --- |
| Cliente | Lucas | critérios de sucesso | |
| Sponsor | Lucas | escopo e orçamento | |
| PO | Guilherme | brief | |
| Tech Lead | Rafael | viabilidade | |
| QA | Rafael | mensurabilidade dos critérios do sistema | |
| Engenheiro de IA/ML | Rafael | mensurabilidade dos critérios de modelo | |
| Data Owner | Lucas | data contracts | |
