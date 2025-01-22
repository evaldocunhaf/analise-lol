ANÁLISE DE DADOS DO DATASET DOS CAMPEÕES DO LOL
DO PATCH 14.22

Contextualização do jogo
Nosso projeto de estatística foi sobre os campeões do jogo de MMO RPG: League of Legends, esse jogo consiste em 2 equipes de 5 pessoas, onde cada uma delas defende uma estrutura chamada de nexus que fica na base deles, o objetivo de cada equipe é destruir o nexus da equipe adversária, para destruir o nexus se faz necessário destruir todas as 3 torres de uma das 3 linhas do mapa, destruir o inibidor da linha, destruir as 2 torres que ficam na frente do nexus e por fim o nexus.

![1-ab4fb7f2](https://github.com/user-attachments/assets/1cb59f28-7a54-4718-bf97-6ea53b25778a)

mapa do jogo.

Para jogar esse jogo escolhemos algum dos campeões para entrar no mapa, e os campeões vão ser o foco deste trabalho, para fim de contexto, ao entrar em uma partida, primeiro vamos banir um campeão para a partida, onde nem a sua equipe nem a equipe adversária podem escolher mais esse campeão, como cada pessoa bane um campeão, 10 campeões podem ser banidos por partida(ja que temos 5 jogadores em cada time), e depois cada jogador seleciona um campeão para jogar.

Atualmente no League of Legends, temos 169 campeões, e cada mês(em média) o jogo solta um patch onde altera alguns atributos e habilidades dos campeões e dos itens(que não vai ser abordado aqui), o patch que vamos falar é o 14.22.1
Aquisição dos dados
Banco de dados
para adquirir os dados dos campeões, achamos um repositório do github com o banco de dados atualizado do patch 14.22.1 disponível em: https://github.com/bryanpimenta/league-of-legends-database, nesse banco temos todos os atributos iniciais dos campeões, quantidade de skins, habilidades, descricoes, etc…
no github temos o squema.sql, que nos copiamos e colocamos na nossa IDE de sql, para colocar no nosso próprio servidor de banco de dados, o SGBD era o mysql
porém precisavamos de dados sobre o desempenho de cada campeão no jogo, esse desempenho é medido em 3 parâmetros: winrate(taxa de vitórias do campeão de todas as partidas que ele joga), pickrate(taxa de vezes que ele é selecionado em relação a todas as partidas), banrate(taxa de vezes que o campeão é banido em relação a todas as partidas), para adquirir esses dados usamos o webscrapping
WebScrapping
Existe um site chamado OP.GG que tem várias informações estatísticas do jogo, como esses dados que queríamos: winrate, pickrate e banrate. Pensamos em adicionar esses dados ao banco de dados, para isso precisavamos extrair do OP.GG esses dados, fizemos um código em python que pela url, acessava a página do campeão no OP.GG e pegava o winrate, pickrate e banrate e registrava em um arquivo em formato de tupla, para ser inserido no banco, código:





```python
from selenium import webdriver
from selenium.webdriver.common.by import By


chrome_options = webdriver.ChromeOptions()


driver = webdriver.Chrome(options=chrome_options)

campeoes = [
    "aatrox", "ahri", "akali", "akshan", "alistar", "ambessa", "amumu", "anivia", "annie",
    "aphelios", "ashe", "aurelionsol", "aurora", "azir", "bard", "belveth", "blitzcrank",
    "brand", "braum", "briar", "caitlyn", "camille", "cassiopeia", "chogath", "corki", "darius",
    "diana", "drmundo", "draven", "ekko", "elise", "evelynn", "ezreal", "fiddlesticks", "fiora",
    "fizz", "galio", "gangplank", "garen", "gnar", "gragas", "graves", "gwen", "hecarim",
    "heimerdinger", "hwei", "illaoi", "irelia", "ivern", "janna", "jarvaniv", "jax", "jayce",
    "jhin", "jinx", "ksante", "kaisa", "kalista", "karma", "karthus", "kassadin", "katarina",
    "kayle", "kayn", "kennen", "khazix", "kindred", "kled", "kogmaw", "leblanc", "leesin",
    "leona", "lillia", "lissandra", "lucian", "lulu", "lux", "malphite", "malzahar", "maokai",
    "masteryi", "milio", "missfortune", "mordekaiser", "morgana", "naafiri", "nami", "nasus",
    "nautilus", "neeko", "nidalee", "nilah", "nocturne", "nunu", "olaf", "orianna",
    "ornn", "pantheon", "poppy", "pyke", "qiyana", "quinn", "rakan", "rammus", "reksai", "rell",
    "renata", "renekton", "rengar", "riven", "rumble", "ryze", "samira", "sejuani", "senna",
    "seraphine", "sett", "shaco", "shen", "shyvana", "singed", "sion", "sivir", "skarner",
    "smolder", "sona", "soraka", "swain", "sylas", "syndra", "tahmkench", "taliyah", "talon",
    "taric", "teemo", "thresh", "tristana", "trundle", "tryndamere", "twistedfate", "twitch",
    "udyr", "urgot", "varus", "vayne", "veigar", "velkoz", "vex", "vi", "viego", "viktor",
    "vladimir", "volibear", "warwick", "monkeyking", "xayah", "xerath", "xinzhao", "yasuo", "yone",
    "yorick", "yuumi", "zac", "zed", "zeri", "ziggs", "zilean", "zoe", "zyra"
]

chaves = [
    266, 103, 84, 166, 12, 799, 32, 34, 1, 523, 22, 136, 893, 268, 432, 200, 53,
    63, 201, 233, 51, 164, 69, 31, 42, 122, 131, 36, 119, 245, 60, 28, 81, 9, 114,
    105, 3, 41, 86, 150, 79, 104, 887, 120, 74, 910, 420, 39, 427, 40, 59, 24, 126,
    202, 222, 897, 145, 429, 43, 30, 38, 55, 10, 141, 85, 121, 203, 240, 96, 7, 64,
    89, 876, 127, 236, 117, 99, 54, 90, 57, 11, 902, 21, 82, 25, 950, 267, 75, 111,
    518, 76, 895, 56, 20, 2, 61, 516, 80, 78, 555, 246, 133, 497, 33, 421, 526, 888,
    58, 107, 92, 68, 13, 360, 113, 235, 147, 875, 35, 98, 102, 27, 14, 15, 72, 901,
    37, 16, 50, 517, 134, 223, 163, 91, 44, 17, 412, 18, 48, 23, 4, 29, 77, 6, 110,
    67, 45, 161, 711, 254, 234, 112, 8, 106, 19, 62, 498, 101, 5, 157, 777, 83, 350,
    154, 238, 221, 115, 26, 142, 143
]
with open("winrate.txt", "w") as arquivo:
    for indice, campeao in enumerate(campeoes):
        url = f"https://www.op.gg/champions/{campeao}/build?tier=all&type=ranked"

        driver.get(url)

        winrate = driver.find_element(By.XPATH, "/html/body/div[1]/div[2]/div[1]/div[3]/div[1]/div[1]/div[2]/div[2]/div/div/div[1]/div/strong").get_attribute("innerHTML").replace("%", "")
        pickrate = driver.find_element(By.XPATH, "/html/body/div[1]/div[2]/div[1]/div[3]/div[1]/div[1]/div[2]/div[2]/div/div/div[2]/div/strong").get_attribute("innerHTML").replace("%", "")
        banrate = driver.find_element(By.XPATH, "/html/body/div[1]/div[2]/div[1]/div[3]/div[1]/div[1]/div[2]/div[2]/div/div/div[3]/div/strong").get_attribute("innerHTML").replace("%", "")
        arquivo.write(f"('{chaves[indice]}',{winrate},{pickrate},{banrate})\n")

driver.quit()
```




Com esse código, tinhamos um arquivo com 169 tuplas dos dados de desempenho de cada campeão, e criamos uma tabela no banco de dados para esses dados e inserimos la
Extração dos dados
Para extrair os dados do sql fizemos uma potente query, onde buscamos colunas que desejamos a priori para poder ir para o colab começar o projeto, para isso usamos a IDE datagrip para executar essa query, e uma função que ele tem para transformar o resultado da query em um arquivo .csv, assim pegamos esse arquivo e criamos o notebook para ir as analises, segue a query abaixo:
```sql
SELECT C.name, C.tags, C.partype, CI.attack, CI.defense, CI.magic, CI.difficulty, CSKins.skins, CS.id, hp, hpperlevel, mp, mpperlevel, movespeed, armor, armorperlevel, spellblock, spellblockperlevel, attackrange, hpregen, hpregenperlevel, mpregen, mpregenperlevel, crit, critperlevel, attackdamage, attackdamageperlevel, attackspeedperlevel, attackspeed, CP.winrate, CP.pickrate, CP.banrate
FROM Champion C
JOIN ChampionInfo CI on CI.champion_id = C.`key`
JOIN ChampionPerformance CP on CP.champion_ID = C.`key`
JOIN ChampionStats CS on C.`key` = CS.champion_id
JOIN (SELECT CSk.champion_id, count(*) as skins
    FROM ChampionSkin CSk
    GROUP BY CSk.champion_id) as CSkins ON CSkins.champion_id = C.`key`
```

Explicação dos dados
Descricao das colunas
- name: nome do campeão
- tags: classes em que ele se encaixa
- partype: o que ele consome para conjurar habilidades
- attack, defense, magic: escala de 0 a 10 do quanto o campeão se encaixa nesses estilos de jogo
- dificulty: escala de 0 a 10 da dificuldade de uso do campeão
- skins: quantidade de 'skins' que o campeão tem no jogo
- hp: vida inicial do campeão
- hpperlevel: o quanto de vida o campeão ganha a cada nível que sobre
- mp: pontos de mana
- mpperlevel: o quanto de mana o campeão ganha a cada nível que sobre
- movespeed: o quão rápido o campeão se move no nivel inicial(por bloco de jogo)
- armor: resistencia a dano físico inicial do campeão
- armorperlevel: o quanto de resistencia a dano físico o campeão ganha a cada nível que sobre
- spellblock: resistencia a magia inicial do campeão
- spellblockperlevel: o quanto de resistencia a magia o campeão ganha a cada nível que sobre
- attackrange: a distância em que o campeão consegue executar ataques básicos
- hpregen: regeneração de vida inicial do campeão
- hpregenperlevel: o quanto de regeneração de vida o campeão ganha a cada nível que sobre
- mpregen: regeneração de mana inicial do campeão
- mpregenperlevel: o quanto de regeneração de mana o campeão ganha a cada nível que sobre
- crit: chance de critico inicial do campeão
- critperlevel: o quanto de chance critico o campeão ganha a cada nível que sobre
- attackdamage: dano de ataque inicial do campeão
- attackdamageperlevel: o quanto de dano de ataque o campeão ganha a cada nível que sobre
- attackspeed: o quanto de velocidade de ataque básico o campeão tem inicialmente
- attackspeedperlevel: o quanto de velocidade de ataque básico o campeão ganha a cada nível que sobe
- winrate: taxa de vitória do campeão
- pickrate: taxa de escolha do campeão
- banrate: taxa de banimento do campeão


Limpeza dos dados
depois de feita o arquivo.csv com os dados, importamos ele para o nosso código, depois de visualizar o head, percebemos algumas colunas que não iam nos ajudar na análise, como: id (não ajudaria nada sobre a análise por ser uma chave primária), crit e critperlevel (após visualizar essa coluna percebemos que nenhum dos campeões começam com chance de crítico, nem recebem chance de crítico de acordo com o seu nível então todos os valores dessas 2 colunas eram 0), attackspeed e attackspeedperlevel (esses dados estavam inseridos erroneamente na base de dados, não condizem com o jogo, então removemos ele) e por fim as tags, pois os dados dela não eram atômicos.
Já na análise dos valores nulos, percebemos que um dos campeões tinham o valor de partype como nulo, esse campeão era a Bel`veth, esse campeão não gasta nada para usar suas habilidades, então eu alterei o valor nulo para nenhum.
durante a análise exploratória dos dados, identifiquei um campeão que tem 10.000 de mana inicial, o que é impossível, visto que campeões magos (que tendem a ter mais itens que aumentam a mana) chegam a ter 6000 de mana, então ao ver quem era esse campeão, vimos que ele é o Viego, e ele não usa mana para usar suas habilidades, então coloquei esse valor de 10.000 como 0
Análise dos dados
Boxplots
para começar as análises, vamos ver os boxplots, o objetivo dos boxplots é nos mostrar a distribuição dos números das variaveis, focando nos quartis dela e mostrando os outliers.

![newplot (16)](https://github.com/user-attachments/assets/b29341e7-4a4d-4b81-af37-5f5f46d58ec2)

para começar vamos ver os gráficos das variáveis de desempenho de campeões, podemos a priori perceber que o winrate tem outliers apenas para baixo, e o valor máximo chega a pouco mais do que 53%, no histórico do jogo já tivemos campeões que chegaram a ter 60% de winrate, atualmente os campeões estão com um desempenho balanceado até, visto que a maioria dos dados se concentram ali por perto de 50%, ainda assim temos campeões que tem cerca de 45% de taxa de vitória que estão desempenhando muito mal.
Já nos gráficos de taxa de banimento e de escolha temos um efeito parecido, esse efeito acontece na taxa de banimento, porque alguns campeões mudam a dinâmica do jogo de maneira que alguns jogadores não gostam, e por isso esses campeões são banidos mais frequentemente. Para a taxa de escolha vemos que tanto ela quanto a de banimentos tem um outlier estupidamente maior do que o resto dos dados, isso ocorre pois no patch 14.22 houve o lançamento de um campeão novo, o que chama bastante atenção dos jogadores para tanto banir ele quanto pegar ele pois quando lança um campeão novo, ele tem uma grande disparidade de desempenho em relação aos outros campeões (esse dado não reflete muito na taxa de vitórias porque mesmo que o campeão seja muito bom, as pessoas não sabem jogar com ele)

![newplot (1)](https://github.com/user-attachments/assets/77cccefe-9d7b-4513-8983-6e9a77675751)


Observando o boxplot da quantidade de skins, vemos que não temos outliers mas temos uma grande disparidade, pois temos campeões com apenas 2 variações de aparência, e temos campeões com 23 skins, mas por nesse meio termos campeões com quantidade de skins bem variados, esse número não chega a ser um outlier. Por fim o box plot das notas de dificuldade nos mostra que a dificuldade vai de 0 a 10, e que temos mais campeões com mais de 5 de dificuldade (o que já seriam mais complicados de jogar), mas o jogo ainda é bem abrangente para você ir explorando as diferentes dificuldades de campeões



Histogramas
Agora analisando os histogramas, onde nosso objetivo era verificar se a frequência de valores de algumas colunas, apresentavam uma distribuição normal. Começamos analisando a coluna de hp, observa-se que ele apresenta uma distribuição tendendo para a direita, devido a 2 campeões que tem uma vida inicial muito fora do padrão, um tem pouco menos de 420 de vida e o outro tem pouco mais de 500, isso enviesa o histograma para que os dados fiquem mais à direita

![newplot (2)](https://github.com/user-attachments/assets/aa86d444-db2a-43cc-883e-00cda31d5553)



para o segundo histograma, foi analisada a armadura, a armadura não representa uma distribuição normal, pois as  pontas não estão bem definidas e existem 2 valores que competem para ser os maiores, de forma que estão bem distantes

![newplot (3)](https://github.com/user-attachments/assets/26db2f92-e3e4-4353-96e0-1cd5f7294d87)

para o terceiro histograma, foi analisado o spellblock que é a resistência do campeão a dano mágico, como dano mágico tem pouca aparição no jogo, a variação do valor não é tão grande, o que também não se fez uma distribuição normal.

![newplot (4)](https://github.com/user-attachments/assets/60726545-5354-42b9-a78c-415e5841ed4b)


para o próximo histograma, foi avaliado o mana inicial dos campeões, os valores de mana ficaram bem dispersos, devido a campeões que usam mana, outros usam energia, e ainda tem os que não usam nada, mas ainda assim, pode-se perceber que quase há uma distribuição normal a direita

![newplot (5)](https://github.com/user-attachments/assets/ee3ecc62-7248-460e-9662-832d38f25a2f)


por fim temos o histograma sobre dano de ataque, as vezes o dano de ataque independem do uso do campeão, às vezes campeões magos tem mais dano de ataque inicial do que alguns que são lutadores, pois o dano de ataque é usado no ataque básico, que é o ataque básico que não gasta mana e não tem cooldown(diferente das habilidades) e por campeões magos não obterem mais dano de ataque com o andar da partida, é bom que eles tenham bastante para poder terem o ataque básico eficiente

![newplot (6)](https://github.com/user-attachments/assets/caece7e7-edf0-433b-9212-e399f059d5ed)


ScatterPlots
Para análise dos scatterplots, começamos a relacionar os atributos dos campeões para gerar os gráficos e as linhas de tendências
O primeiro scatter é a relação entre entre a vida inicial e o dano de ataque, ela apresentou uma tendência a uma linha de função de primeiro grau, os pontos se concentram mais no canto direito do gráfico, já que a maioria dos campeões tem mais do que 550 de vida inicial

![newplot (7)](https://github.com/user-attachments/assets/ad6a1838-1b09-4db5-867a-97375ec83e01)


O segundo foi a relação entre armadura e dano de ataque, analisar essa relação nos mostrará que existem os bonecos feitos para serem resistentes no lol, que são os tanks, que eles não vão ter um ataque tão alto, diferente dos campeões que tem uma armadura na média, esses campeões são os assassinos e lutadores que devem sim ter mais ataque, ja os que tem ainda menos armadura geralmente são magos, que não precisam de tanto dano de ataque

![newplot (8)](https://github.com/user-attachments/assets/b5f4374f-4eae-4967-99e1-f9089920428f)


O terceiro scatter foi a relação entre spellblock e armadura, para avaliar se os campeões que são mais tanks tem uma boa defesa entre dano físico(dano de ataque) e dano mágico, os pontos que aparecem mais próximos a linha horizontal no meio do gráfico são os que te esses atributos balanceados, podemos perceber que a linha de tendencia tem um indice de angulo (a) menor, ja que os campeões tendem a ter a armadura e spellblock balanceados

![newplot (9)](https://github.com/user-attachments/assets/9a588ffd-bb5d-4c04-a650-1a8301978931)


O quarto gráfico foi sobre a relação entre a vida e a armadura, aqui buscamos saber se campeões que têm mais vida conseguem se proteger mais, nesse jogo não adianta você focar só em vida ou em armadura, já que existem itens que tem penetração de armadura (alguns campeões tem isso nas suas habilidades também) e existem também itens e habilidades de campeões que causam dano em porcentagem da vida (causando dano em 1% da sua vida, se você tem 500 de vida você recebe 5 de dano (antes de aplicar as reduções de armadura) mas se você tiver 1000, você recebe 10 de dano(antes de aplicar as reduções de armadura)).
</br>
Esse gráfico nos mostra que os campeões que tem muita vida também tem muita armadura

![newplot (10)](https://github.com/user-attachments/assets/dbc5c9ff-4328-4ef8-b84a-b02e13b6cd8c)

Por fim temos o gráfico que relaciona a quantidade de skins que um campeão tem, com a taxa de seleção dele, esse foi o gráfico mais incomum, percebe-se que os dados geralmente ficam mais à esquerda, o que significa que os campeões que não são tão escolhidos podem ter muitas ou poucas skins, porém com taxas maiores do que 12% vão ter mais do que 10 skins
skins são as roupas que você consegue colocar no seu campeão, mantendo os atributos e habilidades dele, mas alterando o visual dele e dependendo das skins, os efeitos visuais das habilidades

![newplot (11)](https://github.com/user-attachments/assets/2f2f9fd6-d6d1-489e-99c2-77b9a810f8c7)


Mapa de calor
Para o mapa de calor, tentamos estudar as correlações entre as variáveis: hp,mp, skins, movespeed, armor, spellblock, hpregen, attackdamage, winrate, pickrate, banrate, após gerar o gráfico, como as variáveis mais importantes são as 3 de desempenho, vamos avaliar como as outras variáveis se relacionam a elas:

![newplot (12)](https://github.com/user-attachments/assets/8973d5a7-7160-4c9c-a7cc-be927aeb36a7)


no banrate temos uma forte correlação entre ele e o pickrate, o que faz sentido visto que apenas campeões que são mais selecionados são os que valem a pena banir.
para o pickrate não temos nenhuma correlação tão forte, a mais forte é a coluna de skins, que como visto no scatterplot campeões mais selecionados tendem a ter mais skins.
por fim para o winrate temos que nenhuma das colunas realmente se relacionam bem com ela, sendo a mais forte a de movespeed, isso ocorre pois (em minha experiência de jogo) os campeões mais rápidos são os mais fortes

Normalização das colunas
A normalização das colunas se fez necessária devida a escala dos atributos dos campeões serem bem variadas, e ainda temos as métricas de desempenho que também possui escalas bem distintas, para isso se fez necessário a normalização do dataset
<img width="1278" alt="Captura de Tela 2025-01-22 às 13 33 08" src="https://github.com/user-attachments/assets/bddb8c5f-5d1f-414e-8899-88921f2824a7" />

Distribuição amostral
para a distribuição de amostras e análise das médias amostrais, fiz essas amostras de maneira aleatória sem nenhuma consideração(por tipo de campeão ou etc…) e com reposição, usando a biblioteca random, eu criava uma lista com n valores aleatórios não repetidos, sendo n valores de 10%, 20% e 30% do total da população.
Para gerar essas médias dos dados de amostra, usei esse código:
<img width="669" alt="Captura de Tela 2025-01-22 às 13 34 10" src="https://github.com/user-attachments/assets/43e7178a-6651-4c8c-9969-8be3d7fb3c2c" />

com esse código eu salvo as médias dos valores de dano de ataque, armadura e taxa de vitórias, e logo após gerei os gráficos mostrando as linhas de tendencia de cada média amostral
Para gerar as linhas de tendencias eu usei o código abaixo:
<img width="746" alt="Captura de Tela 2025-01-22 às 13 35 28" src="https://github.com/user-attachments/assets/1212fcfa-25e0-4643-b095-2ab1d96d53f5" />

o x_vals definido pelo np.linspace, é uma lista de números igualmente espaçados entre o valor mínimo e máximo da minha variável aleatória, ja a densidade é feito pelo usando a biblioteca scipy.stats que estima a função de densidade de probabilidade da variavel aleatoria, por fim a variavel_y da linha é criada, aplicando a função de densidade na variavel X, então calculamos a largura de cada barra do histograma para regular a densidade estimada para corresponder ao numero de observações, e por fim fazemos o calculo para o Y com a largura das barras para ajustar a linha e usamos x_vals e y_vals para criar o gráfico de linhas e colocar ele em cima do histograma
para as médias temos:

![newplot (13)](https://github.com/user-attachments/assets/9de1e11d-0570-4d3c-830b-d4047d3ce687)
![newplot (14)](https://github.com/user-attachments/assets/32cfbf3d-84f9-472f-becd-2d3869ce32cf)
![newplot (15)](https://github.com/user-attachments/assets/1f20756b-3b17-49f4-934d-7c91cd6227ce)


os 9 histogramas apresentam uma tendência a uma curva normal, na amostra de tamanho 32 percebe-se que a curva é mais normal do que a de 16, e bem parecida com a de 48, mostrando que com o aumento do tamanho da amostra, aumenta a tendência da curva a ser normal (exceto nos gráficos de taxa de vitórias, onde acontece o comportamento oposto)
COLOCAR EQUAÇ˜AO DAS CURVAS
Intervalos de confiança

DADO
LEGENDA
621.80
média da amostra do hp
42.34
desvio padrão da amostra do hp
613.09 - 630.5
intervalo de confiança 90%
611.42 - 632.17
intervalo de confiança 95%



DADO
LEGENDA
4.3
média da amostra da taxa de banimento
5.87
desvio padrão da amostra da taxa de banimento
3.1 - 5.51
intervalo de confiança 90%
2.86 - 5.74
intervalo de confiança 95%


Observando os dados sobre a vida inicial percebe-se que grande parte da vida está agrupada entre os 611 e 632, o que indica atualmente que o jogo está começando com muita vida no geral, e o jogo tende a ser mais demorado visto que os campeões demoraram mais para morrer
Já vendo os dados de banrate, vemos que ele se mantem ali entre os 5.3%, e o intervalo de confiança ali entre 2.84 e 5.74, indica que não há muitos indícios de campeões que estão sendo banidos em vários jogos, os banimentos estão bem distribuídos
