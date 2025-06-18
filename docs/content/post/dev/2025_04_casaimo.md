+++
title = "Busca Casaimo - Melhorando a lembrança"
date = 2025-06-18T06:35:00-03:00
draft = false
type = "post"
summary = "Make it work, make it right, make it fast"
tags = ["desenvolvimento", "ciência de dados"]
+++

Como líder técnico, sempre estimulo meu time a fazer uma pequena pausa assim que definimos que precisamos de um pedaço de software. Antes de sair escrevendo código, é importante parar para escrever um parágrafo ou dois sobre o que você vai implementar e como essa implementação resolve o problema. Muitas vezes, o problema que temos na mão está mal definido e necessita de esclarecimentos. Algumas vezes acontece de o problema ser resolvido com outro software, muito mais simples. Pular esse passo significa, geralmente, desperdiçar energia. Como saber o que fazer não garante que será feito, vou contar como aconteceu comigo: como eu desperdicei dias no calendário por pular essa etapa.

No começo do ano, trabalhei em uma plataforma que usava IA para ajudar pessoas a encontrar apartamentos para morar em Portugal. Um componente crítico do sistema é um LLM que transforma texto livre em uma consulta SQL estruturada, com o tipo de imóvel, filtros de preço, localização, número de quartos, e outras características desejadas.

Segundo apurei com o time, o sistema estava bom, os resultados fazendo sentido. Mas pra algumas consultas voltavam poucos resultados, às vezes nenhum, o que pode ser uma pista de que existem resultados relevantes que foram filtrados. Ou seja, a impressão era de que existe um problema de **lembrança** (ou revocação, o termo brasileiro de quem fala difícil). Como fazer para melhorar essas buscas?

<aside>
💡

**Revocação (lembrança) **: a capacidade de um sistema de busca de trazer para o conjunto de respostas o máximo possível de resultados relevantes. Ela aumenta quando mais resultados relevantes são recuperados.

</aside>

Trouxe um pouco da minha experiência no Google Maps para a questão. Só existe um jeito confiável de evoluir em qualidade: medir a qualidade atual, descobrir as grandes oportunidades, e priorizar a pesquisa de melhorias de acordo com o tamanho das oportunidades. Normalmente não queremos fazer mudanças muito bruscas, queremos mudar só um pouquinho de cada vez. E isso significa que sempre iteramos em ciclos curtos: após mexer no código, precisamos saber o que mudou. Normalmente usa-se um conjunto de consultas representativo do universo, o que nesse estágio da startup significa mesmo **todas as consultas**. Muitas vezes, uma mudança pequena no algoritmo impacta apenas um subconjunto de consultas, então precisamos de um programa que roda todas as consultas com o algoritmo velho e com o novo, e compara os dois lados, escondendo o que ficou idêntico.

### O plano e o tropeço

Ou seja, meu plano de ataque era simples:

1. Encontrar um conjunto de consultas com problema
2. Diagnosticar o problema e inventar maneiras de melhorar a lembrança com pequenos ajustes
3. Avaliar os ajustes para descobrir se a mudança traz bons resultados.

Adivinha quem começou pelo #3? Olhando para trás, é fácil ver que o #3 é onde tinha mais código pra fazer, que é quase um torrão de açúcar para mim. Pequei um número de consultas que concordávamos que estavam ruins e parti para implementar a ferramenta de avalição.

Eu fiz uma ferramenta de avaliação lado-a-lado, inspirado no side-by-side que a gente usava no Google Maps, usando [Streamlit](http://streamlit.io). A ideia é usar uma UI e mostrar para um humano os resultados, tentando mascarar o que é o experimento e o que é o original, e usar feedback do humano para decidir qual lado ficou melhor. Você pode mostrar todos os resultados em uma lista só, e pedir para avaliar cada resultado em relação à consulta, ou mostrar duas listas e perguntar qual lado responde melhor.

Adotar o Streamlit foi bom porque trabalhos estatísticos como esse são penosos, com esperas longas. Esperar carregar o dado, esperar `.apply()` em milhares de linhas. Com uma linha eu adiciono um cache que responde bem a mudanças no código. Também é fácil mostrar uma barra de progresso. Além disso, UIs de avaliação costumam ter poucos controles: um ou outro checkbox, selecionar uma linha em um data frame, etc, que ele tira de letra. E eu sabia que, mais cedo ou mais tarde, viriam os gráficos e visualizações, e do Streamlit é fácil usar plotly e as outras libs fantásticas do Python.

Mas trabalhar na ferramenta para comparar duas variantes da busca logo de cara foi uma decisão ruim. Side-by-sides são ferramentas muito boas para melhorar a **precisão**, mas a minha missão era outra: melhorar a **lembrança.** Enquanto aumentar a precisão exige excluir resultados irrelevantes, aumentar a lembrança exige incluir resultados relevantes. Geralmente fica um cabo-de-guerra entre os dois objetivos e em muitos casos é meio fácil ficar preso em um máximo local em que pequenas melhorias em uma das métricas sempre pioram a outra, portanto eventualmente a ferramenta seria necessária, mas provou-se não ser a ferramenta ideal uma vez que eu tive um diagnóstico em mãos.

### O funil

Voltei ao primeiro passo: identificar as consultas com problemas e qual o problema com elas.

Conversando com o time, declaramos que qualquer busca com menos de 6 resultados indica uma falha de lembrança e consegui um conjunto de consultas. Deixei o número 6 parametrizado na barra lateral para poder analisar o comportamento em diferentes recortes. E então parti para o próximo passo: diagnosticar o que deu errado. É preciso decidir quais resultados são relevantes e teriam sido retornados se a busca estivesse nota 10 (se vamos melhorar a lembrança, o que é que está lá para lembrar?). Nossa base tinha cerca de 130 mil imóveis e 2 mil consultas, portanto é seguro dizer que alguns são relevantes para qualquer consulta e, se não foram retornados, é porque algum dos filtros o excluiu.

Eu nunca tinha parado pra pensar nisso, mas melhorar a lembrança de uma busca se parece muito com melhorar um funil de conversão. Então fiz uma pequena ferramenta para entender o funil de resultados.

Diferente de um funil normal, em que primeiro temos um evento de clique em um anúncio, depois uns visita ao site, depois um cadastro, e finalmente o pagamento,  a ordem dos filtros de uma busca é largamente independente. Isso nos obriga a usar um pouco de intuição e bom senso para ordenar artificialmente os filtros e lembrar que os números são um pouco ruídosos, como vou explicar a seguir.

Declarei que a Localização é o primeiro recorte, e depois aplico os filtros em uma ordem arbitrária. A imagem a seguir mostra os resultados para a busca `procuro um t3 em belem por quinentos mil` . Em Portugal é praxe falar do número de quartos com um T na frente, então T3 é um apartamento de 3 quartos. Obviamente que o mundo é criativo as incorporadoras criaram “T0+1” e outras expressões que vamos ignorar neste artigo.

A tabela a seguir mostra o comportamento de uma única consulta no funil. Começamos com os filtros básicos (imóvel disponível, com preço declarado). Aplicamos o filtro de localização em Belém , depois de preço, e assim por diante.

![tabela.png](image.png)

Na figura acima, podemos ver que a limitação de preço excluiu 64% dos apartamentos que chegaram até ali (pelo visto, morar em Belém é caro!)

A pergunta seguinte seria como expandir o conjunto de resultados. Será que tem um monte de apartamento logo ali depois da fronteira dos 500 mil? Fiz um plot com 4 colunas pra analisar:

![filtro.png](image1.png)

Dá pra ver que até 120% de aumento trariam mais 8 resultados, um aumento significativo para os 11. Talvez esse seja o jeito certo de relaxar a restrição de preço? O novo algoritmo poderia trazer alguns resultados a mais, depois consertar no ranking pra não perder precisão.

Nessas horas, dá uma coceira pra atacar o problema e tentar resolvê-lo com talento e alguma cafeína. Dessa vez eu não caí na armadilha, já caí nela vezes suficientes. O primeiro diagnóstico raramente é o mais relevante. Será que o preço importa tanto assim mas outras consultas?

### O agregado

Eu extrapolei essa ideia de funil para o conjunto completo de consultas com baixa lembrança. Para cada uma, posso coletar o percentual de candidatos excluidos a cada passo, ou descobrir qual filtro trouxe o conjunto de candidatos para menos de 6, e tentar responder a pergunta: “qual dos filtros pode ser relaxado para aumentar a lembrança sem piorar a precisão”? O gráfico a seguir dá pista de que mexer com o tipo de imóvel seja o mais benéfico. Uma consulta pode especificar casa, apartamento, ou nenhum filtro. E a hora que eu vi isso, me deu um estalo e percebi que caí em outra armadilha de desenvolvimento que já tinha esbarrado antes.

![agregado.png](image2.png)

### Lição antiga

Meus colegas cientistas de dados me ensinaram que **sempre** vale a pena fazer uma exploração de dados inicial para entender os objetos com que estamos lidando, encontrar outliers que atrapalham a análise, e de repente descobrir que o seu dado é um lixo e não vai te ensinar nada.

Eu, engenheiro, aprendi mal, e demorei duas semanas para encontrar um bug que um deles teria visto em horas: todas as nossas consultas restringem o tipo de imóvel para “apartamento”, não importa o que o usuário escreveu. Não é plausível que 100% das consultas pediram apartamento, e de fato tenho exemplos que explicitamente mencionam “casa” na lista. Se eu tivesse feito uma análise da distribuição dos valores de cada pedacinho da consulta, teria visto isso logo de cara. Quem aprende mal, aprende duas vezes, dizia meu avô.

Ao olhar algumas consultas com problemas e os filtros que as quebraram, encontrei bugs pequenos como requisições com “max_price == 0”, que excluem todo mundo e que já estavam sendo tratados no algoritmo original da busca, mas meu algoritmo caseiro que aplica um filtro de cada vez não pegou. Coisas assim aparecem logo olhando um punhado de histogramas e boxplots.

### O saldo

Em horas bunda-na-cadeira, a brincadeira toda durou umas 18h. Como o meu engajamento era intemitente, no entanto, duas semanas se passaram. Por um lado, a coisa mais comum do mundo, por outro lado, duas semanas em uma startup podem significar a vida ou a morte.

Conto essa história por dois motivos: primeiro, pra me ajudar a lembrar pra sempre da experiência e gravar melhor esse aprendizado. O segundo, para te ajudar a criar mecanismos para se auto-regular, como os que eu criei. Todo dia, pela manhã, eu fazia uma rápida avaliação de o quanto eu andei ontem e o quanto eu devo andar hoje. Se você nota que não está andando na direção certa, hora de repensar. E, apesar de não ter escrito o que eu ia fazer no dia 1, eu acabei escrevendo meio em paralelo. Nem sempre precisa ser um *****design doc* formal.

Quando esse texto estava quase pronto, Aman Khan [escreveu na newsletter do Lenny](https://www.lennysnewsletter.com/p/beyond-vibe-checks-a-pms-complete) que o mundo das IAs e dos LLMs só vai te trazer sucesso se você aprender a fazer boas avaliações.

### O futuro

Agora penso que vale questionar a existência do filtro, antes de tudo: será que o tipo de imóvel deveria mesmo ser um requisito rígido? Eu já morei em apartamento pequeno, médio, grande, casa com jardim, quitinete-retangular-de-copacabana. Se o preço for bom, os outros critérios de área e número de quartos estiverem dentro, mas o tipo for “errado”, eu deveria esconder dos resultados? Especialmente se houverem poucos resultados para mostrar, talvez seja melhor incluir tudo.

