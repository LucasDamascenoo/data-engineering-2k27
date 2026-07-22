
# Pipelines de dados

Eh um fluxo "automatizado" que leva os dados para as etapas (etl), onde refinamos, e por fim disponibilizamos os dados prontos para uso:


## Etapas:

1. Ingestao dos dados (E): eh a etapa onde nos conectados com bancos de dados, apis, arquivos(csv,parquets), erps para trazer os dados como esta na origem.

2. Transformacoes (T): Nessa etapa, ja temos os dados brutos, cabe a nos engenheiro de dados transformar esses dados, seja tratando nulos, padronizando colunas, criando regras de negocios, indicadores.

3. Carga(L): Ja a carga eh a etapa final, a etapa que os dados ja estao pronto para uso, seja para o time ne analistas, cientistas de dados, pessoas do negocio ou   plugar em um bi .

Alem do fluxo "normal" que uma pipeline tem, temos que tomar outras acoes para garantir que todo esse processo garanta os dados corretamente, seja automatico(agendado), que os dados tenham qualidade, e se falhar(acontece) saibamos como identificar o que deu erro e corrigir.



![fluxo da pipeline](/img/pipeline_de_dados.svg)


## Orquestracao

Orquestração é o processo de gerenciar e coordenar automaticamente a execução das etapas de um pipeline — garantindo que cada etapa só rode quando a anterior terminar com sucesso, no momento certo, e na ordem correta.Pensa assim: sem orquestração, você teria que rodar cada etapa do pipeline manualmente, na hora certa, e torcer para não ter esquecido de nada. A orquestração automatiza essa coordenação — ela sabe que a transformação só pode começar depois que a ingestão terminou, e a carga só começa depois que a transformação passou.


![orquestracao dos dados](/img/orquestracao_pipeline.svg)

Os principais conceitos por trás da orquestração são:

1. Dependências — você declara que T depende de E, e L depende de T. O orquestrador garante a ordem automaticamente, sem você precisar ficar olhando.

2. Agendamento — você define quando o pipeline roda: todo dia às 6h, a cada hora, toda segunda-feira, etc. O orquestrador dispara sozinho.

3. Retry automático — se uma etapa falhar (ex: API fora do ar), o orquestrador pode tentar de novo N vezes antes de alertar alguém. Isso evita falsos alarmes por problemas temporários.

4. Logs e histórico — toda execução fica registrada: quando rodou, quanto tempo levou, se falhou e em qual etapa. Isso se conecta diretamente com a observabilidade que você mencionou nas suas anotações.

## Observabilidade:

Observabilidade é a capacidade de entender o que está acontecendo dentro do seu pipeline — não apenas saber se ele rodou ou falhou, mas conseguir responder perguntas como: onde falhou? por quê? quando começou a degradar? quais dados foram afetados?A ideia central é que erros vão acontecer. A questão não é evitar 100% dos problemas, mas detectá-los rápido e no lugar certo, antes que cheguem ao time de negócio ou ao BI.


![Observabilidade](/img/observabilidade_pipeline.svg)

Os três pilares na prática:

1. **logs** são o registro detalhado do que aconteceu — "a etapa E iniciou às 08:00, leu 50.000 linhas, terminou às 08:03, sem erros". É o que você consulta para investigar um problema depois que ele aconteceu.

2. **Métricas** são os números que você acompanha ao longo do tempo — tempo de execução, volume de dados processados, taxa de falha. Permitem perceber que algo está errado antes de quebrar de vez: por exemplo, se sua ingestão normalmente leva 5 min e hoje levou 40 min, isso é um sinal de alerta mesmo sem ter falhado.
3. **Alertas** são as notificações automáticas disparadas quando algo ultrapassa um limiar — pipeline falhou, SLA não foi cumprido (ex: o dado deveria estar pronto às 7h mas não está), tabela chegou com zero linhas. Chegam via Slack, e-mail, ou ferramentas como PagerDuty.
A distinção importante para um engenheiro de dados: observabilidade não é só sobre erros técnicos. Um dado errado que não quebra o pipeline mas entrega um número errado para o BI é muito mais perigoso — e só a qualidade de dados combinada com observabilidade te protege disso. Por isso as duas andaram juntas nas suas anotações.

## Qualidade de dados:

Qualidade de dados é a garantia de que os dados que chegam ao destino final são confiáveis, completos e corretos — não apenas que o pipeline rodou sem erro técnico, mas que o conteúdo dos dados faz sentido.
Um pipeline pode executar perfeitamente do ponto de vista técnico e ainda assim entregar dados errados. Qualidade de dados é a camada que detecta isso.

![Qualidade de dados](/img/qualidade_de_dados.svg)

Os quatro tipos principais de validação que você vai aplicar nos checkpoints:

1.**Schema** — as colunas que deveriam existir existem? Os tipos estão corretos? (ex: valor chegou como string em vez de float porque a origem mudou sem avisar — isso quebra silenciosamente toda regra de negócio downstream)
2.**Nulos** — campos obrigatórios estão preenchidos? Qual é a porcentagem aceitável de nulos nessa coluna? Se customer_id chegou 30% nulo, algo está errado na origem.

3.**Volume** — a tabela chegou com zero linhas? Veio com 100x mais registros que o normal? Ambos são sinais de problema. Volume zerado é um dos erros mais perigosos porque o pipeline tecnicamente "roda com sucesso".
4.**Regras de negócio** — valores dentro do esperado? Status de pedido só pode ser aberto, pago ou cancelado. Idade não pode ser negativa. Receita de uma filial não pode ser maior que a receita total da empresa. Essas são validações que só fazem sentido quando você conhece o domínio.

Quando uma validação falha, você tem basicamente duas decisões: colocar o dado em quarentena (isola para revisão sem parar tudo) ou bloquear o pipeline (para tudo e alerta o time — usado quando o dado ruim causaria dano grave ao BI ou às decisões de negócio).


## Governanca de dados:

Governança de dados é o conjunto de políticas, processos e responsabilidades que definem como os dados são gerenciados, acessados e usados dentro de uma organização — quem pode ver o quê, o que cada dado significa, de onde ele veio e quem é responsável por ele.Se qualidade garante que o dado está correto, governança garante que o dado está seguro, rastreável e bem documentado.


![Governanca de dados](/img/governanca_de_dados.svg)

Os quatro pilares na prática:

1. **Catálogo de dados** — é a "documentação viva" dos seus dados. Responde: o que é a coluna vlr_liq? É valor líquido com ou sem impostos? De qual sistema veio? Sem catálogo, cada analista inventa sua própria interpretação e o time vive em 
reuniões tentando alinhar definições. Ferramentas: DataHub, Amundsen, Atlan.

2. **Linhagem de dados** — rastreia o caminho completo de um dado: de qual tabela na origem ele veio, por quais transformações passou, e em quais dashboards ele aparece. Quando um número no BI está errado, linhagem te diz exatamente onde no pipeline o problema ocorreu — e quais outros relatórios foram afetados.

3. **Controle de acesso** — nem todo mundo deveria ver tudo. Dados de salário, CPF, dados médicos precisam de restrição. O modelo mais comum é RBAC (Role-Based Access Control): você acessa o que seu papel permite. Mascaramento é outra técnica — o analista vê ***-***-123 em vez do CPF completo.

4. **Conformidade (LGPD)** — a lei impõe obrigações: saber quais dados pessoais você armazena, por quanto tempo, com qual consentimento, e conseguir deletar tudo de um usuário se ele solicitar. Para o engenheiro de dados, isso significa identificar e catalogar dados sensíveis no pipeline desde a ingestão, não depois.

A governança fecha o ciclo com tudo que que estudamos: a observabilidade te diz o que aconteceu, a qualidade te diz se o dado está correto, e a governança te diz quem é responsável, o que o dado significa e quem pode acessá-lo. Juntas, as quatro garantem um pipeline maduro e confiável.