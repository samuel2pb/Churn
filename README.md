# PETLOVE CASE: CHURN

## Preparo do Ambiente

### Importando bibliotecas

Foram utilizadas as bibliotecas pandas, matplotlib e seaborn. 

### Primeira Leitura dos Dados

Com a finalidade de observar possíveis ajustes imediatos que podem ser realizados para facilitar a análise.

### Datas Ambíguas

Para evitar ambiguidade ao lidar com análises que envolvam o tempo, será necessário tratar as datas de todas as colunas. 
Isso porque essas se encontram com o formato dd/mm/aa , onde anos com apenas 2 dígitos.

Foi realizada uma tentativa de conversão direta utilizando to_datetime() do pandas pode-se observar alguns erros aparentes principalmente na coluna ***birth_date***. Contudo, podemos descartar a presença desse erro nas colunas relacionadas diretamente ao e-commerce, visto que ***Marcio Waldman fundou sua primeira versão em 1999***, a data de criação máxima, após a conversão, é de ***2021-02-18 05:04:00*** o que indica que não há valores de 1999 transformados em 2099 e a biblioteca (***Pandas***) interpretou todos os valores acima do ano 2000 corretamente. Se pode inferir então, que as datas reformatadas nas colunas ***created_at, updated_at, deleted_at, last_date_purchase*** estão corretas e não precisam de correções. 
As correções serão aplicadas a todos os casos considerados lógicamente impossíveis, ou seja, ***birth_dates*** superiores a data atual.

O código para a correção, utiliza uma comparação entre o dataframe ***birth_date*** e a data atual para guardar na variável ***futuro*** os valores que estão acima do limiar definido. 
Esse vetor é então utilizado para localizar dentro do dataframe ***birth_date*** essas idades equivocadas e então substitui-las por suas correspondentes 1 século antes, denotado na função ***timedelta***. 

### Conferência de Valores Nulos

A incidência de valores nulos é encontrada na coluna *deleted_at* referentes a datas de cancelamento de clientes ainda ativos.

### A feature *deleted_at* é redundante se comparada a *updated_at* ?

Datas Conferidas Entre updated_at e deleted_at:505

Após se comparar ***updated_at*** ae ***deleted_at*** constatou-se que o universo de clientes que cancelaram suas assinaturas é de **505** e que a variável ***deleted_at*** é redundante para a análise.

## Adequação das variáveis para a análise

### Pode-se perceber dados que não serão relevantes, assim como dados que podem ser extraidos para facilitar a visualização.

Adicionando uma coluna ***duracao*** possbilita descrever linearmente a permanência do cliente o que facilitará as análises.

Além disso podemos substituir a coluna de ***birth_date*** pela ***idade*** do cliente.

Features como ***id***, ***name_hash*** , ***email_hash*** e ***address_hash*** terão pouco ou nenhum impacto na decisão do cliente poderão ser descartados da análise. 

Pode-se utilizar somente a coluna ***updated_at*** já que a ***deleted_at*** é atualizada uma ultima vez sendo que os valores de atulização são fieis aos valores da data de cancelamento. 

Pode-se utilizar ainda uma coluna ***churn*** para seguimentar clientes que cancelaram ou não pelo churn. Neste caso, será atribuído **1** para os que cancelaram, **0** para os ativos e **-1** para clientes pausados.

### Conferindo Intervalos Negativos

Existiam **177 intevalos negativos*** na variável ***duracao***, esses valores foram tratados antes de realização das análises.

Os valores de ***created_at*** que forem menores que ***updated_at*** foram substituidos pela data da ultima atulização (***updated_at**) subtraída da ***recency*** que é variável que diz a quanto tempo o cliente realizou sua última operação na plataforma. Pois logicamente, essas assinaturas existem no minímo a partir da última compra. 

O código utilizado criou uma coluna ***negative*** para segregar valores de ***duracao*** negativos e assim facilitar seu tratamento. Os valores segregados em ***created_at*** foram substituídos pela subtração entre a ***updated_at*** e ***recency***.
Após isso se atualizou novamente a coluna ***duracao*** e pode observar na contagem de valores negativos que já não havia mais intervalos errados.

## Análise

### Verificar a distribuição das classes relacionadas ao *status*

### Descrevendo as relações estatísticas evidentes

### Diagrama de Correlações para perceber alguma correlação já aparente entre as features.

### Análise da *recency*

Média de dias desde o último pedido (Churn = 1): 680.9702970297029
Média de dias desde o último pedido (Churn = 0):34.53214453308306
Média de dias desde o último pedido (Churn = -1):34.69309989701339

### Análise dos Grupos Etários

Para essa análise foram dividas as populações em cinco grupos, estes sendo:

**Grupo 1 : 26 até 37.2**    
**Grupo 2: 37.2 até 48.4**    
**Grupo 3: 48.4 até 59.6**    
**Grupo 4:59.6 até 70.8**    
**Grupo 5: 70.8 até 82**

Após segmentar os grupos etários e promover uma visualização gráfica dos mesmos,  percebe-se uma **baixa correlação** entre a idade e a tendência ao Churn, isso se deve pela distribuição regular entre as populações. 
Contudo pode-se levantar a hipótese que pessoas no **grupo 3 (Entre 48,4 e 59,6 anos)** são as mais tendenciosas ao Churn

### Análise referente a Duração da Assinatura

Fica evidente o período onde ocorrem as evasões, sendo entre **0 e 442 dias**, isso é indicado pelo período do **1° Quartil (0 até 442)** com  **57,2%** de todas as evasões ocorrendo neste período.
Essa hipótese também é corroborada ao se observar o **alto desvio padrão**, o que indica uma **distribuição desbalanceda** das populações.

### Análise da Receita Total Acumulada

A medida com que os gastos sobem, diminui a tendência ao Churn, isso porque **39,4%** dos clientes desistentes o fazem antes de acumular aproximadamente **R$1.060,00**. Isso indica que os clientes que a maioria dos clientes que desistem da assinatura o fazem nos **primeiros meses** ou seja enquanto seu gasto acumulado ainda não é significativo.
Isso é também corroborado pela curva de clientes não desistentes que seguem num ritmo de consumo aproximadamente constante

### Análise do Ticket Médio

O gráfico gerado mostrou que o ticket médio dos 3 perfis análisados são bastante semelhantes o que corrobora para a hipótese que esse fator **não seja determinante** no Churn.
Contudo este gráfico adiciona peso a tese que o estimulo ao consumo serve como premissa para a fidelização do cliente, visto que no estudo acima (***all_revenue***) percebe-se a tendência de um cliente interromper a sua assinatura antes dos **R$ 1060,00**

Numa análise inicial, partindo da hipótese que o ticket médio ocorre **mensalmente**, como a média da ***recency (34 dias)*** sugere. Se o ticket médio de um cliente é de cerca de **215,00** e o início das desistências se dá quando o cliente atinge um gasto de próximo a **1060,00** , se pode inferir que isso ocorre num período inferior a **5 meses** (1060/215) aproximadamente **168 dias**, o que corrobora a média de duração dos contratos desistentes que ocorrem entre **0 e 442 dias**.

### Análise dos Canais de Marketing

***Tráfego Orgânico*** é evidentemente o principal motivo de churn representando **38,8%** dos casos, isso é evidenciado não apenas pelos percentuais menores nas outras categorias mas pelo gráfico de densidade dos clientes ativos.

Contatos via ***Direct*** e ***Tráfego Pago*** também representam parcelas significativas e junto ao ***Tráfego Orgânico*** somam **71,6%** de cancelamentos.

Existe uma relação direta entre o contato direto com o cliente e sua permanência, isso é corroborado tanto pelos menores ídices de churn nessas categorias(Marketing Ativo) quanto no por porcentagens significativas no gráfico de permanência.

### Análise da Versão de Assinatura

Para essa análise foram dividas as populações em oito grupos referentes aos tipos de assinatura, estes sendo:

**Grupo 0: Formato 0.X.X**

**Grupo 1: Formato 1.X.X**

**Grupo 2: Formato 2.X.X**

**Grupo 3: Formato 3.X.X**

**Grupo 4: Formato 4.X.X**

**Grupo 5: Formato 5.X.X**

**Grupo 6: Formato 6.X.X**

**Grupo 7: Formato 7.X.X**

Assinaturas do tipo **3.X.X** e **4.X.X** são as mais volumosas e consequentemente as que produzem mais churn. Juntas representam  **65,2%** de todas as assinaturas perdidas, o que pode sinalizar que pessoas nesses planos podem ser o alvo de novas campanhas e promoções. Ao adicionar os clientes do plano tipo **2.X.X** soma-se **83%** de Churn, os grupos anteriores devem ser priorizados contudo o grupo 2.X.X também deve ser alvo de ações de marketing.

### Análise Quantidade de Itens

Quantidade média de itens dos clientes que cancelam assinatura: 8.647524752475247

### Análise Número de Pedidos

Ao se analisar o primeiro quartil, percebe-se que aproximadamente **5** pedidos acontecem antes do cancelamento. Contudo é apresentada uma nova perspectiva, **22,7%** dos cancelamentos já acontecem no primeiro pedido, e **13,26%** ocorrem sem pedido algum.

**49,1 %** dos cancelamentos ocorrem nessa faixa de **0 a 5 pedidos**  o que é ressalta a importantância de se analisar esse período em específico. Ou seja, a janela de tempo de evasão é menor ainda do que o imaginado.

### Análise de Regiões

Para essa análise foram dividas as populações em cinco grupos referentes as regiões do brasil, estes sendo:

**Grupo 1 : Norte**    
**Grupo 2: Nordeste**    
**Grupo 3: Centro-Oeste**    
**Grupo 4: Sudeste**    
**Grupo 5: Sul**

Sozinhas as regiões Norte e Nordeste somam **60,9%** de todos os casos de Churn, o que pode sugerir que futuras ações de marketing devem priorizar essas regiões.

Se se somar as incidências também no centro-oeste, juntos as 3 regiões equivalem a **76,7%** de todos os cancelamentos.

## As seguintes informações retiradas da análise exploratória foram consideradas relevantes.

A Média de recency entre os clientes ativos: **34**.

A idade das populações **não é** um fator correlacionado ao Churn.

O período com maior incidência ocorre no primeiro Quartil (**0 a 442 dias**) dos valores de *duracao* e corresponde a **57%** dos Churns.

A Média de Gastos Acumulados por clientes antes do cancelamento é de **R$ 1.060,00** e corresponde a **40%** dos Churns.

O ticket médio dos clientes é de **217,00**.

Clientes com planos tipo **3.X.X** e **4.X.X** possuem os maiores indices de churn somados aos do tipo **2.X.X** equivalem a **83%** dos cancelamentos.

Há em média **4,857** pedidos antes do churn que correspondem a **39,4%** dos casos.

Clientes que vieram a plataforma por ***Tráfego Orgânico*** correspondem sozinhos a **38,8%** dos casos de churn, contudo se somados ao contato por ***direct** e ***Trafego pago***  equivalem a **70%** dos casos de churn.

Clientes das **regiões norte, nordeste e centro** oeste somam **76,5%** de todo o churn apresentado nos estados.

Se Clientes enquanto estão ativos realizam pedidos a cada **34 dias** em média, possuem um ticket médio de **217 reais**  e acumulam gastos de até **1060 reais** antes de cancelarem sua assinatura, podemos testar nossa Hipótese da duração média até o churn. Para isso dividimos o gasto total pelo ticket médio e multiplicamos pelos dias **[(1060/217).34]** resultando em **166 dias** dentro do período (**0 até 442**) descrito pela análise.

## Teste da hipóteses:

Para trazer uma maior confiabilidade para a hipótese da duração do cliente na assinatura, pode-se agora testa-lá relacionada a outras variáveis cuja a importância agora é sabida alguns exemplos são:

**duracao = 0:450**

**average_ticket <=220** 

**all_revenue <= 1100**

Para ser uma hipótese válida são esperados resultados semelhantes a estes dentro dos casos de Churn.

Para se testar essa hipóteses serão utilizados os parâmetros considerados relevantes: 

Categorias de Marketing: **mkt_src != '1' & '6', (crm, telegram/Whatsapp)***. Visto que essas se mostraram a forma de marketing mais efetiva.

Categorias de Contrato: **simple_version = ['2','3','4'], (2.X.X, 3.X.X, 4.X.X)**

Regiões: **region = ['1','2','3'], (Norte, Nordeste, Centro-Oeste)**


A hipótese acima **cumpriu com o esperado, as médias de ticket médio, número de vendas e receita total estão suficientemente próximas das médias que se esperava encontrar**. Entretanto, existe um grande desvio padrão apresentado na receita total, isso pode ser um indicativo do cancelamento precoce ainda nos primeiros meses, esse alto desvio padrão se repete na duração, outro fator somador na hipótese das desistências precoces.  

Como se suspeita que as evasões ocorrem ainda nos primeiros meses observou-se o intervalo já discutido nas análises anteriores para confirmar se ele se mostra significativo dentro da hipótese. Esse intervalo vai de 0 a 450 dias.

Os resultados mostraram que a suspeita é verdadeira, **59,7%** dos cancelamentos da hipótese ocorreram  em até 450 dias e **22,3%** ocorreram nos primeiros 3 meses. Sozinhos essa população que atende a hipótese representa **48,7%** do universo dos cancelamentos. Sendo que destes, **29,1%** cancelam nos primeiros **450 dias** e **10,8%** nos primeiros **3 meses**. 

A hipótese **é satifatória**, pois existe correlação entre multiplas variáveis ao mesmo tempo, o passo seguinte foi analisar os dados referentes aos cadastros ativos e pausados para avaliar se uma solução embasada na hipótese surtiria o efeito esperado para o time de assinaturas.


### Aplicando a hipótese as Populações Active e Paused

**47,73%** de todos os clientes que não cancelaram se encontram dentro dos parâmetros selecionados, contudo podemos perceber que o período de permanência está fora da hipótese levantada, eles são indicados com o uso de 4 parâmetros simultâneos o que indica uma forte correlação entre eles, **eles precisam receber novas ações do time de assinaturas por meio de ***crm ou Telegram/Whatsapp*** para evitar a sua evasão**.

Contudo Pode-se perceber que existem **12,10%** dos clientes ativos ou pausados que se encontram em parâmetros muito próximos aos apresentados pelas análises, inclusive na janela de tempo que gera a princiapal evasão (**0:442**).Levando em consideração que eles atendem a **5 parâmetros simultâneamente**. **Eles devem ser o foco de novas ações do time de assinaturas por meio de ***crm ou Telegram/Whatsapp*** para evitar a sua evasão**.

## Conclusão: Aprendizado & Próximos Passos 

### Aprendizados

Como apontado pelas várias análises que foram feitas, além de existirem fatores que estão diretamente ligados a decisão pelo cancelamento. Existem ainda correlações entre esses fatores, o que fornece um direcionamento mais claro para uma atuação imediata do time de negócios.

1.  Esses fatores que aparentam estar ligados a taxa de Churn são apresentados a seguir: 

2.  O período com maior incidência ocorre no primeiro Quartil (**0 a 442 dias**) dos valores de *duracao* e corresponde a **57%** dos   Churns.

3.  A Média de Gastos Acumulados por clientes antes do cancelamento é de **R$ 1.060,00** e corresponde a **40%** dos Churns.

4.  Clientes com planos tipo **3.X.X** e **4.X.X** possuem os maiores indices de churn somados aos do tipo **2.X.X** equivalem a    **83%** dos cancelamentos.

5.  Há em média de **4,857** pedidos antes do churn que, estes correspondem a **39,4%** dos casos.

6.  Clientes que vieram a plataforma por ***Tráfego Orgânico*** correspondem sozinhos a **38,8%** dos casos de churn, contudo se somados ao contato por ***direct*** e ***Trafego pago***  equivalem a **70%** dos casos de churn.

7.  Clientes das **regiões norte, nordeste e centro oeste** somam **76,5%** de todo o churn apresentado nos estados.

Ao associar os fatores, **canal de marketing, versão da assinatura e região**. Confirmaram-se valores próximos levantados pela hipótese estes sendo, o **ticket médio de 220 reais**, a **duração média entre 0 e 450 dias** e uma **receita total média próxima a 1100 reais**. O que indica uma forte correlação entre esses fatores. Essa população que atende a hipótese representa **56,03%** do universo dos cancelamentos, o que é bastante significativo.

### Próximos Passos 

Com os aprendizados da análise, existem dois passos imediatos que podem ser tomados. Ambos serão baseados na mesma estratégia de atuação, contudo terão como foco populações ligeiramente distintas. 

Utilizar o CRM para identificar padrões de consumo e então estimular esse consumo por meio de cupons, créditos e descontos personalizados assim como frete grátis para populações que atendam aos seguintes parâmetros, priorizando os que apresentam mais de um parâmetro ao mesmo tempo:

a.	Possuem uma Assinatura do Tipo 3.X.X, 4.X.X ou 2.X.X.

b.	Estão Localizados em estados da Região, Norte, Nordeste e Centro-Oeste.

c.	Clientes que não foram alvo de estratégias baseadas em CRM e Telegram/Whatsapp

d. Clientes que tenham assinatura de 0 a 450 dias. 



