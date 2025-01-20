+++
title = "Software no preço e no prazo"
date = 2025-02-05T11:53:17-03:00
draft = false
type = "post"
summary = "Uma noção comum mas incorreta é de que as decisões sobre o problema a resolver e a solução a adotar já foram resolvidas, e o time de desenvolvimento é apenas o executor."
tags = ["produto", "desenvolvimento"]
+++

É verdade que o engenheiro de software é responsável por implementar a solução em um custo e prazo previsíveis, e que o papel de criar soluções e entender o mercado é de outro grupo, talvez do time de negócio, ou do gerente de produto ou de projetos. É um caminho perigoso, de separar as responsabilidades, porque a engenharia tem muitas alavancas para acelerar o desenvolvimento e a maior delas não está na fase da implementação.

Começamos cedo, na vida profissional, a lidar com comentários assim: "Engenheiro não sabe vender", ou "essa especificação já foi aprovada". Ou a clássica "essa é uma decisão da liderança", evidenciando o óbvio, que você está fora dela. Ou mais grosseiramente, como ouvi de um colega desenvolvedor, "o nosso trabalho é implementar, não entender". Nunca entendi bem essa posição, sempre quis entender mais a fundo a motivação para criar qualquer programa. E muitas vezes isso gerou atrito: pessoas que trabalhavam comigo ficavam cansadas de explicar o porquê das coisas. E algumas, mais acostumadas a racionalizar, abanar as mãos e ganhar discussões na carteirada, ficavam mesmo exaustas. Ao longo dos anos, fui criando mecanismos de anotação e escrita para desenroscar as soluções sem criar tanto atrito. Elas estavam certas sobre a minha baixa capacidade de vender, mas erradas sobre a necessidade de entender o cliente.

Entre outros desenvolvedores também nem sempre me encaixei: a vida dos sonhos, para alguns, era passar o dia escrevendo código. Eu nunca me encaixei nesse grupo. Para mim o software mais lindo que faz algo inútil era igualmente inútil.

### Menos é mais

Na minha tribo, o resultado final sempre veio em primeiro lugar, e as maneiras mais inteligentes de chegar lá eram as mais atraentes. O folclore da época era que "desenvolvedor bom é preguiçoso". Era uma brincadeira com o hábito de querer evitar trabalho manual, mesmo quando isso exige mais esforço intelectual. Prefiro gastar duas horas lendo a documentação uma hora escrevendo um script para evitar três horas de importação manual de dados. Com um tempo, criou-se uma [confusão](https://www.forbes.com/sites/forbestechcouncil/2018/01/08/developers-are-lazy-and-thats-usually-a-good-thing/) com o outro tipo de preguiça, de querer fazer o trabalho pela metade, por isso talvez seja melhor chamar de _malícia_. O principal benefício de investir no script é que o trabalho manual tende a aparecer de novo, e por isso o custo de criar uma solução reutilizável tende a se diluir.

Além disso, estudar as ferramentas com profundidade nos permite utilizá-las em todo seu potencial. Um exemplo concreto: antigamente o React-Admin tinha uma documentação fraca sobre autenticação (ver [v3.19](https://marmelab.com/react-admin/doc/3.19/Authentication.html)), e foi preciso insistir um pouco pra entender que um _authProvider_ era tudo o que precisávamos na nossa implementação. Um colega que tinha feito quase um fork do framework todo acabou removendo a maior parte do seu código pra usar a abordagem nova.

Por último, a prática de se aprofundar nos torna desenvolvedores melhores. A experiência me ensinou que se eu começo a me enroscar desenvolvendo uma solução própria, vale parar e procuar algo pronto. É muito possível que alguém tenha feito igual ou melhor do que eu faria e ainda colocado como software livre em algum lugar. A preguiça-malícia me fará encontrar e adotar uma biblioteca que pode estar sendo mantida por outras pessoas, alavancando minha capacidade de gerar valor.

### Zero é melhor ainda

Tudo o que eu falei até agora envolve _adicionar_ software, que é o que sobra de alavanca quando as decisões de produto já foram tomadas. O melhor momento de envolver a engenharia é ainda mais cedo, quando a solução está em formação, e ainda nos debruçamos muito sobre o problema. Lá existam muitas oportunidades de _excluir_ software do plano para o sucesso, em partes ou completamente.

Uma vez, quando eu trabalhava em um sistema Java para substituir um sistema legado, coisa muito comum de se ver no mundo da fábrica de software nos anos 2000, eu vivi na pela o poder de perguntar o que o cliente quer. Enormes roadmaps eram comuns, e no meio do primeiro milestone eu recebi um pedido para implementar uma calculadora automática de imposto de renda. Ela tinha que ficar dentro de uma página de cadastro com muitas outras entradas de dados sobre a renda da pessoa. Eu comecei a achar que ninguém ia preencher aquilo, ou que o RH já deveria ter aquelas informações. Putz, eu mesmo jamais iria preencher aquilo. Entendi que era uma ação rara no produto, que seria feito uma vez por ano. Descobri também que naquele ano apareceram várias calculadoras online para as pessoas usarem. Eu sugeri despriorizar, que é um _soft delete_ no roadmap.

Inicialmente foi negado o pedido, mas meu gerente de projetos, que era um cara safo, cutucou mais a fundo e descobriu que o sistema legado que estávamos substituindo não tinha essa funcionalidade, e ela entrou no escopo porque o SAP tem uma calculadora de imposto de renda. Sob a ótica de abandonar o sistema legado o mais rápido possível, que é o que o nosso cliente queria de nós. Mais do que isso, o cliente, após ser provocado, percebeu que o RH já tinha a maior parte dos dados que estavam sendo perguntados ali e ninguém iria usar aquela tela mesmo. Ficaram de voltar com uma integração diferente com o SAP, sem UI. Nunca aconteceu, porque projetos gigantes quase nunca terminam.

Visualize a economia de código, de testes manuais que nunca precisaram ser feitos, de angústia com a chamada ao SAP que retorna um erro obscuro, nada disso aconteceu, porque um dev levantou a mão e o responsável pelo projeto investigou a fundo.

Em alguns casos, o custo acontece em cascata. Por exemplo, se no seu formulário de cadastro você pergunta dados pessoais, é de se perguntar se ter essa informação realmente aumenta a capacidade de encantar o seu usuário. Guardar corretamente, num contexto de LGPD, pode encarecer bastante o sistema e arriscar a capacidade de terminar no prazo. É muito fácil deixar requisitos de valor duvidoso invadirem o escopo de uma solução, e muito difícil saber quais cortes economizam muito.

Embora entender as ferramentas, adotar bibliotecas e otimizar algoritmos gere ganhos palpáveis no desenvolvimento, nem se compara ao poder de entender o que o usuário quer. Você pode jogar funcionalidades inteiras fora, ou parte delas, ganhando com isso uma espécie de juros compostos de energia poupada:

- desafogar o prazo para o primeiro lançamento
- nunca ter que fazer nem a v2, nem a v3 da funcionalidade.

Se essa história não é familiar e seu time nunca viveu isso, pode ser o caso de experimentar, para alguns assuntos, envolver a engenharia mais cedo na discussão. Se houver oportunidade de entregar o mesmo valor com menos produto, é provável que este seja o grupo que vai descobrir. E se acontecer, a nossa responsabilidade de implementar a solução em custo e prazo previsíveis fica muito, muito mais fácil.

Bonus: assista ao vídeo do Dave Farley explicando como governo britânico [desperdiçou 500 milhões de libras](https://youtu.be/w-_nCA3RTYs?si=DjVmz7voOj9pWBmv) e não entregou nada.
