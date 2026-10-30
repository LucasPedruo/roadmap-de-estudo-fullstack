# Dicionário de termos da programação

O problema que este documento resolve: metade do vocabulário técnico é inglês
mal traduzido, e a tradução literal quase sempre leva para o lugar errado.
Outbox não é caixa de saída. Cache não é esconderijo. Commit não é compromisso.

Aqui cada termo tem a mesma estrutura:

- **Fala-se** a pronúncia aproximada, para você não travar na reunião.
- **Ao pé da letra** a tradução literal.
- **A armadilha** por que essa tradução engana.
- **O que é** a explicação de verdade.
- **Analogia** algo do mundo real que funciona igual.
- **A troca** o que você ganha e o que você perde ao usar.
- **Na conversa** como a palavra aparece numa frase do time.

Nem todo termo precisa dos sete campos. Quando não houver armadilha, o campo
não aparece.

---

## Índice

1. [O que é um trade-off](#1-o-que-é-um-trade-off)
2. [Os trade-offs clássicos](#2-os-trade-offs-clássicos)
3. [Falsos amigos](#3-falsos-amigos)
4. [O dia a dia do time](#4-o-dia-a-dia-do-time)
5. [Código e qualidade](#5-código-e-qualidade)
6. [Arquitetura e padrões](#6-arquitetura-e-padrões)
7. [Dados e banco](#7-dados-e-banco)
8. [Frontend e navegador](#8-frontend-e-navegador)
9. [Segurança](#9-segurança)
10. [Infraestrutura e operação](#10-infraestrutura-e-operação)
11. [Processo e time](#11-processo-e-time)
12. [Expressões e gírias](#12-expressões-e-gírias)

---

## 1. O que é um trade-off

**Fala-se** trêidóf.
**Ao pé da letra** troca, permuta.
**A armadilha** parece que é só uma escolha entre duas opções. É mais do que
isso. Trade-off é uma escolha em que **toda opção tem perda**. Não existe lado
sem custo.

**O que é.** Em engenharia de software quase nada é "melhor". Tudo é "melhor
para quê". Quando alguém pergunta "qual é o trade-off disso?", a pergunta real
é: o que a gente perde ao ganhar isso?

Exemplo concreto. Você guarda em cache o resultado de uma consulta pesada. Ganha
velocidade. Perde atualidade, porque o dado guardado pode estar velho. Não dá
para ter os dois ao mesmo tempo. Você escolhe qual dói menos naquele caso.

**Por que isso importa tanto.** A diferença entre quem está começando e quem é
experiente raramente é conhecer mais tecnologia. É conseguir dizer, para cada
escolha, o que está sendo pago. Quem responde "vamos usar X porque é melhor"
ainda não enxerga o custo. Quem responde "vamos usar X, e o preço é Y, que a
gente aceita porque Z" está fazendo engenharia.

**Analogia.** Comprar carro. Esportivo é rápido e gasta mais, não cabe a
família e o seguro é caro. Não existe o carro que é tudo. Existe o carro certo
para o seu uso.

**Na conversa.** "Qual o trade-off de colocar isso numa fila?" Resposta honesta:
"a tela para de travar, mas o usuário deixa de ver o resultado na hora, e agora
a gente precisa tratar o caso de falhar depois."

**A frase que vale decorar.** Não pergunte se uma solução é boa. Pergunte o que
ela custa e se você aceita pagar.

---

## 2. Os trade-offs clássicos

Estes aparecem repetidamente, em qualquer projeto. Reconhecer o padrão poupa
discussão.

### Velocidade contra atualidade

Guardar resultado em cache deixa rápido e deixa o dado velho. Quanto mais tempo
você guarda, mais rápido fica e mais desatualizado pode estar.

Pergunta que resolve: quanto tempo de defasagem o usuário tolera nesse dado?
Saldo bancário tolera zero. Lista de categorias tolera uma hora.

### Simplicidade contra flexibilidade

Toda camada de abstração que você cria para "ficar flexível no futuro" é código
a mais para entender hoje. Às vezes o futuro nunca chega e só sobra o custo.

Pergunta que resolve: você já tem o segundo caso de uso na mão, ou está
imaginando ele?

### Consistência contra disponibilidade

Em sistema distribuído, quando a rede falha entre duas partes, você escolhe:
recusar a operação para não ficar com dado errado, ou aceitar a operação e
acertar os detalhes depois.

Banco escolhe consistência. Rede social escolhe disponibilidade. É por isso que
sua curtida aparece instantaneamente e a contagem de curtidas demora a bater.

### Normalizar contra desnormalizar

Dado guardado uma vez só, com referências, evita contradição e exige juntar
tabelas para ler. Dado repetido fica rápido de ler e pode ficar inconsistente.

Pergunta que resolve: esse dado é lido muito mais do que escrito?

### Acoplamento contra duplicação

Reaproveitar o mesmo código em dois lugares liga os dois. Quando um precisar
mudar, o outro muda junto, mesmo sem querer. Duplicar separa e cria o risco de
corrigir num lugar só.

Regra prática que o mercado usa: duplicação é mais barata que abstração errada.
Espere o terceiro caso antes de unificar.

### Agora contra depois

Fazer rápido e torto entrega hoje e cobra juros amanhã. Isso tem nome, dívida
técnica. Não é sempre errado. É errado fazer sem registrar e sem intenção de
pagar.

### Construir contra comprar

Escrever a própria solução dá controle total e consome o seu tempo para sempre.
Usar algo pronto é rápido e te amarra às decisões de outra pessoa.

Pergunta que resolve: isso é o nosso diferencial ou é encanamento?

### Monolito contra serviços separados

Um sistema só é simples de rodar, de depurar e de alterar inteiro. Vários
serviços escalam separados e trazem rede, versionamento e falha parcial para
dentro da sua vida.

A recomendação madura hoje é começar monolito bem organizado e separar quando a
dor justificar.

### Otimizar contra entregar

Deixar rápido antes de saber o que está lento é perda de tempo. Tem uma frase
famosa sobre isso, de Donald Knuth: otimização prematura é a raiz de todo mal.
A versão completa dela diz algo mais útil: meça primeiro.

### Tipar tudo contra mover rápido

Tipagem rígida pega erro cedo e custa tempo para escrever. Tipagem frouxa escreve
rápido e descobre o erro em produção.

---

## 3. Falsos amigos

Palavras cuja tradução literal leva para o lugar errado. Esta tabela sozinha
resolve boa parte da confusão do começo.

| Termo | Você pensa | É na verdade |
|---|---|---|
| **Outbox** | Caixa de saída do e-mail | Tabela onde a mensagem fica esperando ser publicada, gravada junto com o dado |
| **Cache** | Esconderijo | Cópia guardada para não refazer trabalho caro |
| **Commit** | Compromisso | Gravar uma alteração no histórico |
| **Branch** | Filial, galho | Linha de trabalho paralela |
| **Merge** | Fusão de empresas | Juntar duas linhas de trabalho |
| **Build** | Construção civil | Transformar código-fonte no que roda |
| **Deploy** | Desdobrar, implantar tropa | Publicar a nova versão |
| **Release** | Lançamento de filme | Versão publicada do software |
| **Issue** | Questão, assunto | Tarefa ou defeito registrado |
| **Bug** | Inseto | Defeito no código |
| **Patch** | Remendo de roupa | Correção pequena |
| **Hotfix** | Conserto quente | Correção urgente direto em produção |
| **Rollback** | Rolar para trás | Voltar para a versão anterior |
| **Legacy** | Herança, legado afetivo | Código antigo que ninguém quer tocar |
| **Stack** | Pilha | O conjunto de tecnologias usadas |
| **Framework** | Estrutura metálica | Base pronta que define como você escreve |
| **Library** | Biblioteca | Conjunto de funções que você chama quando quer |
| **Runtime** | Tempo de corrida | O ambiente que executa o programa |
| **Thread** | Linha de costura | Linha de execução dentro do programa |
| **Pool** | Piscina | Conjunto de recursos prontos para reuso |
| **Lock** | Fechadura | Trava que impede dois processos de mexerem ao mesmo tempo |
| **Hook** | Gancho | Ponto onde você pendura seu código para rodar em certo momento |
| **Mock** | Zombar, imitar | Peça falsa usada no teste |
| **Stub** | Toco, canhoto | Resposta fixa que simula um serviço |
| **Seed** | Semente | Dados iniciais inseridos no banco |
| **Migration** | Migração de aves | Arquivo que altera a estrutura do banco |
| **Schema** | Esquema, plano | A estrutura das tabelas, ou o formato de um dado |
| **Payload** | Carga útil, carga de míssil | O conteúdo da mensagem ou requisição |
| **Endpoint** | Ponto final | Um endereço específico da API |
| **Middleware** | Software do meio | Código que roda entre o pedido e a resposta |
| **Pipeline** | Oleoduto | Sequência automática de etapas |
| **Container** | Contêiner de navio | Programa empacotado com tudo que precisa |
| **Image** | Imagem, foto | O pacote parado, antes de virar contêiner |
| **Registry** | Cartório | Onde as imagens ficam guardadas |
| **Cluster** | Aglomerado | Conjunto de máquinas trabalhando como uma |
| **Node** | Nó | Uma máquina dentro do cluster, ou um ponto numa estrutura |
| **Pod** | Vagem, casulo | A menor unidade que roda no Kubernetes |
| **Token** | Ficha de metrô | Credencial que prova quem você é |
| **Claim** | Reivindicação | Uma informação dentro do token, como o seu papel |
| **Salt** | Sal | Valor aleatório acrescentado à senha antes de embaralhar |
| **Hash** | Picadinho | Transformação de mão única, que não volta |
| **Race condition** | Condição de corrida | Dois processos disputando a mesma coisa, com resultado imprevisível |
| **Deadlock** | Tranca morta | Dois processos esperando um pelo outro, para sempre |
| **Throttling** | Estrangulamento | Limitar o ritmo de chamadas de propósito |
| **Fallback** | Recuo | Plano B automático quando o principal falha |
| **Graceful** | Gracioso | Encerrar com elegância, terminando o que estava em andamento |
| **Dirty** | Sujo | Alterado e ainda não salvo |
| **Stale** | Velho, embolorado | Dado desatualizado |
| **Flaky** | Escamoso, instável | Teste que ora passa, ora falha, sem mudar nada |
| **Smoke test** | Teste de fumaça | Verificação rápida de que o básico não quebrou |
| **Canary** | Canário | Liberar para poucos usuários antes de liberar para todos |
| **Blast radius** | Raio da explosão | O tamanho do estrago se aquilo falhar |
| **Bus factor** | Fator ônibus | Quantas pessoas precisam sumir para o projeto parar |
| **Yak shaving** | Tosar iaque | Sequência de tarefas sem relação que você precisa fazer antes da real |
| **Bikeshedding** | Discutir o galpão de bicicletas | Gastar a reunião no detalhe irrelevante |

---

## 4. O dia a dia do time

### build

*Fala-se bíld. Ao pé da letra: construção.*

**O que é.** O processo que transforma o código que você escreveu no formato
que roda de verdade. Em TypeScript, junta e reduz arquivos. Em C#, compila para
um formato binário.

**Analogia.** Você escreve a receita. O build é cozinhar. O que o cliente come
não é a receita, é o prato.

**Na conversa.** "O build quebrou" significa que o código não conseguiu virar
programa. Nada chegou ao usuário.

### deploy

*Fala-se deplói. Ao pé da letra: implantar, posicionar tropas.*

**O que é.** Colocar a versão nova para rodar no ambiente onde os usuários
estão. Build é cozinhar, deploy é servir.

**A troca.** Deploy frequente e pequeno dá menos medo e descobre problema cedo.
Deploy raro e grande acumula risco, e quando dá errado ninguém sabe qual das
cinquenta mudanças causou.

**Na conversa.** "Subiu em produção?" e "foi pro ar?" significam a mesma coisa.

### release

*Fala-se rilís. Ao pé da letra: liberação.*

**O que é.** A versão publicada, com um número ou nome. Deploy é o ato,
release é o pacote.

**Na conversa.** "Isso entra na release de sexta."

### rollback

*Fala-se róubéque. Ao pé da letra: rolar para trás.*

**O que é.** Voltar para a versão anterior quando a nova deu problema.

**A armadilha.** Parece sempre possível. Não é. Se a versão nova alterou a
estrutura do banco, voltar o código não volta o banco. Por isso existe a regra
de alterar banco de forma que as duas versões funcionem durante a troca.

**Na conversa.** "Dá rollback que a gente investiga com calma."

### hotfix

*Fala-se rótfiks. Ao pé da letra: conserto quente.*

**O que é.** Correção urgente que fura o processo normal e vai direto para
produção, porque algo está quebrado agora.

**A troca.** Você resolve o incidente rápido e pula a revisão e o teste que
existiriam. Hotfix costuma gerar o próximo incidente.

### bug

*Fala-se bãg. Ao pé da letra: inseto.*

**O que é.** Defeito. O programa faz algo diferente do esperado.

**De onde vem o nome.** Da década de 1940, quando um inseto preso num
computador causou falha de verdade. A história é real e virou vocabulário.

**Na conversa.** "Isso é bug ou é comportamento esperado?" é uma pergunta séria,
não ironia. Metade dos defeitos relatados é regra que ninguém explicou ao
usuário.

### feature

*Fala-se fítcher. Ao pé da letra: característica.*

**O que é.** Funcionalidade. Algo novo que o produto passa a fazer.

**Na conversa.** A piada "não é bug, é feature" existe porque de fato acontece
de um comportamento estranho ter sido decidido de propósito.

### refactor

*Fala-se refáctor. Em português, refatorar.*

**O que é.** Mudar como o código está escrito sem mudar o que ele faz. O
usuário não vê diferença nenhuma.

**A troca.** Deixa o código mais fácil de alterar depois e consome tempo que
não vira entrega visível. Refatorar sem teste automatizado é apostar.

**Na conversa.** "Isso é refactor, não muda comportamento" é uma informação
importante para quem vai revisar e para quem vai testar.

### legacy

*Fala-se légaci. Ao pé da letra: legado, herança.*

**A armadilha.** A palavra soa nobre em português. No trabalho, carrega um tom
de peso.

**O que é.** Código antigo, em produção, que ninguém quer tocar. Em geral sem
teste, sem documentação e sem quem o escreveu.

**O ponto que vale guardar.** Código legado é código que está funcionando e
dando dinheiro. O desprezo por ele costuma durar até você precisar reescrever e
descobrir as trinta regras de negócio escondidas lá dentro.

### breaking change

*Fala-se bréiquin tchêndge. Ao pé da letra: mudança que quebra.*

**O que é.** Alteração que faz quem usava do jeito antigo parar de funcionar.
Trocar o nome de um campo da API é breaking change. Acrescentar um campo novo,
não.

**A troca.** Quebrar permite limpar o desenho. Não quebrar acumula puxadinho.
A saída comum é versionar, manter o antigo por um tempo e avisar.

### workaround

*Fala-se uérc-aráund. Ao pé da letra: contornar.*

**O que é.** Solução provisória que evita o problema sem resolver a causa.

**A troca.** Destrava agora e esconde o problema. Workaround sem registro vira
comportamento oficial em seis meses, e ninguém lembra por que aquilo está ali.

### boilerplate

*Fala-se bóilerpleit. Ao pé da letra: chapa de caldeira.*

**A armadilha.** A origem é de impressão de jornal, onde textos prontos eram
distribuídos em chapas de metal. Nada a ver com código, mas o nome pegou.

**O que é.** Código repetitivo e obrigatório que você escreve sempre igual, sem
pensar. A estrutura básica de um arquivo, por exemplo.

**A troca.** Dá para eliminar com abstração ou geração automática. O custo é que
a mágica esconde o que está acontecendo, e quando quebra ninguém sabe onde
olhar.

### edge case

*Fala-se édge quêis. Ao pé da letra: caso de borda.*

**O que é.** A situação rara que quase nunca acontece e quebra tudo quando
acontece. Lista vazia, nome com acento, cliente com dois telefones iguais,
virada do ano, fuso horário diferente.

**Na conversa.** "Testou o edge case?" quer dizer: você tentou com o valor
estranho, ou só com o bonito?

### happy path

*Fala-se rápi péth. Ao pé da letra: caminho feliz.*

**O que é.** O fluxo em que tudo dá certo. O usuário preenche corretamente, a
rede responde, o pagamento aprova.

**O ponto.** Programar o caminho feliz leva 20% do tempo. Os outros 80% são os
caminhos infelizes, e é neles que o produto é julgado.

### regression

*Fala-se regréchon. Em português, regressão.*

**O que é.** Algo que funcionava e parou de funcionar depois de uma alteração.

**Por que importa.** É o tipo de defeito mais irritante para o usuário, porque
ele já contava com aquilo. Teste automatizado existe principalmente para pegar
regressão.

### overhead

*Fala-se ôverréd. Ao pé da letra: acima da cabeça, custo indireto.*

**O que é.** O custo extra que uma solução traz além do trabalho útil. Uma fila
tem overhead de serializar a mensagem, enviar e confirmar. Uma camada de
abstração tem overhead de leitura para quem vai entender o código.

**Na conversa.** "Não compensa, o overhead é maior que o ganho."

### latência e throughput

*Fala-se latência e thrúput.*

**O que são.** Latência é quanto tempo uma operação leva. Throughput, ou vazão,
é quantas operações cabem por segundo.

**A armadilha.** Parecem a mesma coisa e são independentes. Um sistema pode ser
lento por operação e atender muita gente ao mesmo tempo. Outro pode responder
rápido e atender pouca gente.

**Analogia.** Latência é quanto tempo um carro leva de São Paulo ao Rio.
Throughput é quantos carros a estrada comporta por hora. Alargar a estrada não
faz o carro chegar mais cedo.

---

## 5. Código e qualidade

### abstração

**O que é.** Esconder o detalhe e mostrar só o que interessa. Quando você usa
uma função chamada `enviarEmail`, não precisa saber de protocolo nem de
autenticação.

**A troca.** Toda abstração esconde poder e acrescenta uma camada para
atravessar na hora de depurar. Abstração boa economiza pensamento. Abstração
errada obriga você a entender duas coisas em vez de uma.

**A frase clássica.** Toda abstração vaza. Em algum momento o detalhe escondido
volta a importar, em geral quando algo quebra.

### acoplamento e coesão

**O que são.** Acoplamento é o quanto duas partes dependem uma da outra. Coesão
é o quanto uma parte trata de um assunto só.

O objetivo é acoplamento baixo e coesão alta. Partes que mexem no próprio
assunto e dependem pouco das outras.

**Como reconhecer o problema.** Se você muda uma coisa e precisa mexer em cinco
arquivos sem relação aparente, o acoplamento está alto. Se um arquivo trata de
e-mail, de imposto e de relatório, a coesão está baixa.

### encapsulamento

**O que é.** Guardar os dados dentro do objeto e expor só as operações
permitidas. Em vez de deixar qualquer um alterar o saldo direto, você oferece
depositar e sacar, que validam.

**A troca.** Protege a regra e escreve mais código. Em objeto que é só um saco
de dados, encapsular não paga.

### dívida técnica

*Em inglês, technical debt.*

**O que é.** O custo futuro de uma decisão tomada para ganhar tempo hoje.
Trabalho feito pela metade, teste não escrito, nome ruim que ficou.

**A analogia é exata.** Como dívida de dinheiro, ela cobra juros. Cada nova
funcionalidade construída em cima do atalho fica mais cara.

**O que separa dívida de bagunça.** Dívida é consciente e registrada. Bagunça é
desleixo. A primeira é ferramenta legítima, a segunda não.

### code smell

*Fala-se côud smél. Ao pé da letra: cheiro de código.*

**O que é.** Sinal de que algo provavelmente está errado, sem ser erro. Função
de trezentas linhas, nome genérico, parâmetro booleano que muda tudo, código
repetido em três lugares.

**Na conversa.** "Isso tem cheiro" quer dizer: não está quebrado, mas vai dar
problema.

### DRY, KISS e YAGNI

**DRY**, don't repeat yourself, não se repita. Cada conhecimento deve existir em
um lugar só.

**KISS**, keep it simple, mantenha simples. Entre duas soluções que funcionam,
escolha a que a próxima pessoa entende.

**YAGNI**, you aren't gonna need it, você não vai precisar disso. Não construa
para o futuro imaginado.

**A tensão entre eles.** DRY levado ao extremo cria abstração prematura, que o
YAGNI condena. A regra de bolso do mercado: duplique duas vezes, unifique na
terceira.

### SOLID

**O que é.** Cinco princípios de desenho orientado a objetos. O mais útil deles
para quem está começando é o primeiro, responsabilidade única: cada classe deve
ter um motivo só para mudar.

**Honestidade sobre o assunto.** SOLID é citado mais do que entendido. Vale
aprender, e vale desconfiar de quem usa a sigla para vencer discussão sem
explicar qual princípio está sendo violado e por quê.

### over-engineering

*Fala-se ôver-endjiniêring. Em português, engenharia em excesso.*

**O que é.** Construir uma solução mais complexa do que o problema pede.
Costuma nascer de boa intenção, preparar para um futuro que não chega.

**Como reconhecer.** Três camadas de interface para uma implementação só.
Configuração para algo que nunca vai mudar. Sistema de plugins para dois casos.

**O oposto também existe.** Under-engineering, a solução que não aguenta o
primeiro crescimento. O erro mais comum em time iniciante é o primeiro.

### hardcoded

*Fala-se rárdcôuded. Ao pé da letra: codificado duro.*

**O que é.** Valor escrito direto no código em vez de vir de configuração. Um
endereço de servidor cravado no meio da função, por exemplo.

**A troca.** Rápido de escrever e caro de mudar, porque exige nova publicação.
Nem tudo precisa ser configurável, mas endereço, chave e limite quase sempre
precisam.

### magic number

*Fala-se mégic nâmber. Ao pé da letra: número mágico.*

**O que é.** Um número solto no código, sem explicação. Alguém escreveu `86400`
e ninguém sabe que são os segundos de um dia.

**A solução.** Dar nome ao valor. O nome é a documentação.

### spaghetti code

**O que é.** Código tão emaranhado que seguir o fluxo é impossível. Uma coisa
chama a outra, que volta para a primeira, com condições em todo canto.

### god object

*Ao pé da letra: objeto deus.*

**O que é.** Uma classe que sabe tudo e faz tudo. Costuma ter milhares de linhas
e um nome genérico como `Manager`, `Helper` ou `Service`.

**Por que acontece.** Cada pessoa acrescenta "só mais um método" no lugar que já
existe, porque é mais rápido do que criar um arquivo novo.

### lint

*Fala-se lint. Ao pé da letra: fiapo de tecido.*

**A armadilha.** O nome vem de remover fiapos de roupa. A ferramenta remove
fiapos do código.

**O que é.** Programa que lê o código procurando problema e desvio de padrão,
sem executar nada.

### DX e UX

**UX** é a experiência de quem usa o produto. **DX** é a experiência de quem
desenvolve nele: o quanto é fácil rodar o projeto, entender o erro e fazer uma
alteração.

**Por que DX importa.** Projeto com DX ruim atrasa tudo em silêncio. Cada
pessoa perde vinte minutos por dia e ninguém mede isso.

### pair programming

*Fala-se pêir prôgraming. Em português, programação em dupla.*

**O que é.** Duas pessoas, um código. Uma escreve e a outra pensa à frente, e os
papéis se alternam.

**A troca.** Custa duas pessoas no mesmo problema e entrega revisão contínua,
menos defeito e troca de conhecimento. Funciona muito bem para tarefa difícil e
mal para tarefa repetitiva.

---

## 6. Arquitetura e padrões

### outbox

*Fala-se áutbóks. Ao pé da letra: caixa de saída.*

**A armadilha.** Você pensa na caixa de saída do e-mail, aquela onde a mensagem
fica até ser enviada. A intuição está quase certa, e é por isso que confunde. O
ponto não é a espera. É **onde** ela espera.

**O problema que existe antes do padrão.** Imagine que o sistema precisa fazer
duas coisas quando uma campanha é criada: gravar a campanha no banco e avisar
outro serviço, publicando uma mensagem numa fila.

São dois sistemas diferentes. Não existe uma transação que cubra os dois. Então
você tem duas ordens possíveis, e as duas falham:

- Grava no banco, depois publica. Se a publicação falhar, a campanha existe e
  ninguém foi avisado. O disparo nunca acontece.
- Publica, depois grava. Se a gravação falhar, o outro serviço vai processar
  uma campanha que não existe.

Isso tem nome, problema da escrita dupla. Não dá para resolver com try e catch,
porque o processo pode simplesmente morrer no meio.

**O que é o outbox.** Em vez de publicar na fila, você grava a mensagem numa
tabela do próprio banco, dentro da mesma transação do dado. Ou as duas gravações
acontecem, ou nenhuma acontece. O banco garante isso.

Depois, um processo separado lê essa tabela, publica na fila de verdade e marca
a linha como enviada. Se ele cair no meio, ao voltar encontra a linha ainda não
enviada e tenta de novo.

**Analogia.** Você precisa pagar uma conta e avisar seu sócio. Em vez de pagar e
depois ligar, correndo o risco de o telefone não funcionar, você escreve no
caderno da empresa, na mesma linha: "paguei e preciso avisar o sócio". O caderno
é a fonte da verdade. Alguém vai lá depois, lê o que falta avisar e avisa.

**A troca.** Você ganha a garantia de que nada se perde. Paga três coisas: uma
tabela a mais para manter, um processo a mais rodando, e o aviso deixa de ser
instantâneo, passa a ter um atraso de segundos.

Paga uma quarta, menos óbvia. A entrega passa a ser "pelo menos uma vez", nunca
"exatamente uma vez". A mesma mensagem pode ser publicada duas vezes se o
processo cair entre publicar e marcar como enviada. Por isso outbox anda sempre
de braço dado com idempotência.

**Na conversa.** "Isso tem que ir pelo outbox, senão a gente perde evento quando
o Rabbit estiver fora."

### inbox

*Ao pé da letra: caixa de entrada.*

**O que é.** O espelho do outbox, do lado de quem recebe. Antes de processar,
você grava o identificador da mensagem numa tabela. Se a mesma mensagem chegar
de novo, você vê que já está lá e ignora.

É assim que se resolve na prática a entrega repetida.

### idempotência

*Fala-se idempotência mesmo. A palavra vem da matemática.*

**O que é.** A propriedade de uma operação que pode ser repetida sem efeito
extra. Apagar um arquivo é idempotente: apagar de novo não muda nada. Somar 10
ao saldo não é, porque repetir soma de novo.

**Por que vira assunto o tempo todo.** Em sistema distribuído tudo pode chegar
duas vezes: o usuário clicou duas vezes, a rede repetiu, a fila reentregou.

**Como se faz na prática.** O cliente manda uma chave única junto com o pedido.
O servidor guarda essa chave com o resultado. Se o mesmo pedido chegar outra
vez, devolve o resultado guardado em vez de executar de novo.

**Na conversa.** "Esse endpoint é idempotente?" quer dizer: se eu chamar duas
vezes, cria dois registros?

### retry e backoff

*Fala-se ritrái e béquióf. Ao pé da letra: tentar de novo e recuar.*

**O que é.** Falha de rede costuma ser temporária. Tentar de novo resolve boa
parte. Backoff é esperar cada vez mais entre as tentativas, em vez de bater na
porta sem parar.

**A armadilha perigosa.** Retry sem cuidado transforma problema pequeno em
desastre. Se um serviço fica lento e mil clientes começam a repetir a chamada, o
serviço que ia se recuperar morre de vez. Isso se chama tempestade de
tentativas.

**Os detalhes que evitam isso.** Espera que dobra a cada tentativa, limite de
tentativas, e um valor aleatório somado à espera, para os clientes não voltarem
todos no mesmo instante. Esse valor aleatório se chama jitter.

**A troca.** Mais tentativas aumentam a chance de sucesso e aumentam a carga
sobre quem já está sofrendo. E só repita o que for idempotente.

### circuit breaker

*Fala-se cirquit brêiquer. Ao pé da letra: disjuntor.*

**A analogia é literal.** É o disjuntor da sua casa. Quando a corrente passa do
limite, ele desarma e corta tudo, para não queimar a instalação.

**O que é.** Um componente que conta as falhas ao chamar um serviço. Passou do
limite, ele para de tentar e passa a devolver erro na hora, sem nem chamar.
Depois de um tempo, deixa passar uma chamada de teste. Se funcionar, volta ao
normal.

**Por que é útil.** Chamar um serviço que está fora é pior do que não chamar.
Cada chamada fica esperando o tempo limite, e as suas threads ficam presas
esperando. Em pouco tempo o seu sistema cai junto por causa do vizinho.

**A troca.** Você falha rápido e protege o sistema. Em compensação, falha mesmo
quando o serviço já voltou, até o teste seguinte.

### fallback

*Fala-se fólbéque. Ao pé da letra: recuo, plano B.*

**O que é.** O que fazer quando o caminho principal falha. Mostrar dado em cache
mesmo velho, usar um valor padrão, ou exibir uma mensagem honesta.

**A pergunta que decide o desenho.** Falhar fechado ou falhar aberto? Se o
serviço de permissão não responde, você bloqueia todo mundo ou libera todo
mundo? Em segurança, a resposta padrão é bloquear. Em disponibilidade, liberar.
A escolha precisa ser consciente e escrita.

### rate limit e throttling

*Fala-se rêit límit e thrótling. Ao pé da letra: limite de taxa e estrangulamento.*

**O que é.** Limitar quantas chamadas alguém pode fazer num período. Protege o
sistema de abuso e de cliente mal programado.

**Na prática.** Quando você estoura, recebe o código 429 e um cabeçalho dizendo
quando pode tentar de novo. Respeitar esse cabeçalho é obrigação de quem
consome.

**No produto.** As APIs de WhatsApp e de IA têm limite. Por isso o disparo de
campanha tem ritmo controlado em vez de mandar tudo de uma vez.

### fila

*Em inglês, queue. Fala-se quiú.*

**O que é.** Um lugar onde mensagens esperam para serem processadas. Quem produz
deposita e segue. Quem consome pega quando pode.

**A troca.** Ganha resiliência, porque o consumidor pode estar fora e nada se
perde. Ganha absorção de pico. Perde a resposta imediata, perde a ordem
garantida e ganha um sistema novo para operar e monitorar.

**O sinal de alerta.** Fila crescendo sem parar significa que o consumidor não
dá conta. É a primeira métrica que se olha quando algo está estranho.

### publicar e assinar

*Em inglês, publish and subscribe, abreviado pub/sub.*

**O que é.** Em vez de o remetente escolher o destinatário, ele publica um
evento e quem tiver interesse se inscreve. Quem publica não sabe quem escuta.

**A troca.** Desacopla muito, e é exatamente por isso que fica difícil
responder "quem é afetado se eu mudar esse evento?". O acoplamento não sumiu,
ficou invisível.

### evento

**O que é.** O registro de que algo aconteceu, no passado. "Ticket foi criado",
"pagamento foi aprovado". Diferente de um comando, que pede algo.

**Por que a diferença importa.** Comando pode ser recusado. Evento não, já
aconteceu. Nomear evento no passado é convenção que evita confusão de desenho.

### consistência eventual

*Em inglês, eventual consistency.*

**A armadilha.** "Eventual" em inglês quer dizer "no final", não "talvez". A
tradução correta é consistência que se alcança com o tempo, não consistência
que talvez aconteça.

**O que é.** O sistema aceita ficar temporariamente desalinhado e garante que
vai acertar depois. Você curte uma publicação, a curtida aparece para você na
hora e o contador global demora alguns segundos.

**A troca.** Ganha velocidade e disponibilidade. Perde a certeza instantânea, e
precisa desenhar a interface para isso, em vez de mentir dizendo que está
pronto.

### teorema CAP

**O que é.** Num sistema distribuído, quando a rede entre as partes falha, você
só pode escolher dois entre três: consistência, disponibilidade e tolerância a
partição. Como a rede sempre falha, na prática a escolha é entre consistência e
disponibilidade.

**O que ele não diz.** Que você precisa escolher de uma vez para o sistema
inteiro. A escolha é por operação. Cobrança escolhe consistência. Listagem de
produtos escolhe disponibilidade.

### com estado e sem estado

*Em inglês, stateful e stateless.*

**O que é.** Um serviço sem estado não guarda nada entre uma requisição e outra.
Toda informação necessária vem no pedido. Um serviço com estado lembra.

**Por que importa.** Serviço sem estado é fácil de multiplicar. Basta subir mais
cópias atrás de um distribuidor de carga. Com estado, cada cópia tem a própria
memória, e o usuário que cair na cópia errada não encontra os próprios dados.

**Na prática.** É por isso que sessão vai para cookie, token ou Redis, e não
para a memória do processo.

### balanceador de carga

*Em inglês, load balancer.*

**O que é.** O componente que recebe as requisições e distribui entre as várias
cópias do serviço. Também percebe quando uma cópia está doente e para de mandar
tráfego para ela.

### proxy e proxy reverso

*Fala-se próxi.*

**O que é.** Um intermediário. O proxy comum fica na frente do cliente, falando
com a internet por ele. O proxy reverso fica na frente do servidor, recebendo as
chamadas e repassando.

**Por que existe o reverso.** Ele concentra o que não deve estar espalhado:
certificado de segurança, cabeçalhos, compressão, limite de taxa, cache e
distribuição de carga.

### gateway

*Fala-se guêit-uêi. Ao pé da letra: portão.*

**O que é.** A porta única de entrada de um conjunto de serviços. Quem está fora
fala com o gateway, que cuida de autenticação, limite de taxa e roteamento.

### monolito e microsserviços

**O que são.** Monolito é um sistema único, publicado de uma vez. Microsserviços
são vários serviços pequenos, publicados separados, que conversam pela rede.

**A troca, sem romantismo.** Microsserviços resolvem um problema de organização
de time, não um problema técnico. Eles permitem times publicarem sem esperar uns
aos outros. Em troca, trazem rede, latência, falha parcial, versionamento de
contrato, rastreamento distribuído e um custo de operação muito maior.

**O conselho que o mercado consolidou.** Comece monolito bem separado por dentro.
Divida quando a dor de publicar junto for maior que a dor de operar separado.

### padrões de projeto

*Em inglês, design patterns.*

**O que são.** Soluções conhecidas para problemas que se repetem. Não são
receitas obrigatórias, são vocabulário. Dizer "aqui eu usei um adapter" economiza
cinco minutos de explicação.

Os que mais aparecem no dia a dia:

- **Factory**, uma função que decide qual objeto criar.
- **Adapter**, uma casca que faz uma coisa parecer outra, para encaixar numa interface existente.
- **Strategy**, trocar o algoritmo em tempo de execução, escolhendo uma implementação entre várias.
- **Observer**, um objeto avisa vários interessados quando algo muda.
- **Facade**, uma porta simples na frente de um subsistema complicado.
- **Singleton**, garantir que só existe uma instância. É o mais usado e o mais criticado, porque vira estado global disfarçado.

**A troca.** Padrão aplicado onde o problema existe economiza trabalho. Padrão
aplicado por hábito vira over-engineering, e é fácil reconhecer o código de quem
acabou de ler o livro.

---

## 7. Dados e banco

### schema

*Fala-se squíma. Ao pé da letra: esquema.*

**A armadilha.** Em português, esquema sugere desenho ou plano. Aqui é mais
rígido: é a estrutura declarada, com as regras que o sistema cobra.

**O que é.** Duas coisas, conforme o contexto. No banco, é o conjunto de tabelas
e colunas com seus tipos. Numa validação de dados, é a descrição do formato
esperado, como a que o Zod cria.

### migration

*Fala-se maigrêichon. Ao pé da letra: migração.*

**A armadilha.** Não tem nada a ver com migrar dados de um lugar para outro, que
é o sentido comum da palavra.

**O que é.** Um arquivo que descreve uma alteração na estrutura do banco. Criar
tabela, acrescentar coluna, criar índice. Elas ficam em ordem e formam o
histórico da estrutura.

**A troca.** Ganha reprodutibilidade, porque qualquer ambiente chega ao mesmo
formato. Perde liberdade, porque migração já aplicada em produção não se edita.
Correção vira migração nova.

**O cuidado que derruba sistema.** Uma alteração que trava a tabela por minutos
derruba o produto enquanto roda. Alteração pesada em tabela grande não entra em
migração automática.

### seed

*Fala-se síd. Ao pé da letra: semente.*

**O que é.** Dados iniciais inseridos no banco para o sistema funcionar ou para
você testar. Lista de estados, tipos de atividade, usuário administrador.

### índice

*Em inglês, index.*

**O que é.** Estrutura auxiliar que acelera a busca. Sem ele, o banco percorre a
tabela inteira.

**Analogia.** O índice remissivo no fim de um livro. Sem ele, você folheia o
livro todo procurando a palavra.

**A troca.** Leitura rápida, escrita mais lenta e espaço em disco. Cada gravação
atualiza todos os índices daquela tabela. Por isso não se indexa tudo.

### transação

**O que é.** Um bloco de operações que acontece por inteiro ou não acontece.

**Analogia.** Transferência bancária. Tirar de uma conta e colocar na outra
precisa ser indivisível. Metade da operação é pior do que nenhuma.

### ACID

**O que é.** As quatro garantias de uma transação: atomicidade, consistência,
isolamento e durabilidade. Em resumo: tudo ou nada, regras respeitadas,
transações não se atrapalham, e o que foi confirmado não some.

### deadlock e race condition

*Fala-se dédlóque e rêis condíchon.*

**Deadlock** é quando dois processos esperam um pelo outro e nenhum anda. O
banco detecta e mata um dos dois.

**Race condition** é quando dois processos mexem na mesma coisa ao mesmo tempo e
o resultado depende de quem chegou primeiro. É o tipo de defeito que não
reproduz, só acontece em produção, no horário de pico.

**Analogia da race condition.** Duas pessoas olham o estoque, veem uma unidade e
vendem. O estoque fica negativo.

### N mais 1

*Em inglês, N+1 problem.*

**O que é.** O código busca uma lista de cem registros, e depois faz uma
consulta por registro para completar algum dado. Cento e uma idas ao banco onde
caberia uma.

**Por que é tão comum.** Porque não aparece em desenvolvimento, onde a lista tem
três linhas. Aparece em produção, com lista de mil.

**Na conversa.** "Isso aqui está dando N mais 1" é o diagnóstico mais frequente
de tela lenta.

### carregamento tardio e antecipado

*Em inglês, lazy loading e eager loading.*

**O que são.** Tardio busca a informação relacionada só quando alguém pedir.
Antecipado traz tudo de uma vez, junto com a consulta principal.

**A troca.** Tardio economiza quando você não precisa do relacionado, e é a
causa número um do problema N mais 1. Antecipado faz uma consulta só e traz
dados que talvez não sejam usados.

### upsert

*Fala-se ápsert. É a junção de update e insert.*

**O que é.** Grava se não existe, atualiza se existe, numa operação só.

### soft delete

*Ao pé da letra: exclusão macia.*

**O que é.** Marcar a linha como apagada em vez de apagar de verdade.

**A troca.** Ganha histórico, auditoria e a possibilidade de desfazer. Paga com
uma condição a mais em toda consulta, para sempre. Quem esquece essa condição
mostra dado apagado para o usuário.

### normalizar e desnormalizar

**O que são.** Normalizar é guardar cada informação em um lugar só, com
referências. Desnormalizar é repetir de propósito, para ler rápido.

**A troca.** Normalizado evita contradição e exige juntar tabelas. Desnormalizado
lê rápido e pode ficar inconsistente quando alguém atualiza só uma cópia.

### paginação por página e por cursor

**O que são.** Por página, você pede a página 3 com 20 itens, e o banco pula 40.
Por cursor, você pede os 20 seguintes a partir do último que recebeu.

**A troca.** Por página é simples e fica lento conforme a página aumenta, porque
o banco precisa percorrer tudo até o ponto. Também repete e pula itens quando a
lista muda no meio. Por cursor é estável e rápido, e não permite ir direto para a
página 50.

### snapshot

*Fala-se snépchót. Ao pé da letra: foto instantânea.*

**O que é.** O estado de algo congelado num momento. Aparece em backup, em
versionamento e em estruturas geradas que guardam o formato atual do modelo.

---

## 8. Frontend e navegador

### render

*Fala-se rénder. Em português, renderizar ou desenhar.*

**O que é.** Transformar dados e código no que aparece na tela.

**Na conversa.** "Esse componente está renderizando demais" quer dizer que ele
está sendo redesenhado mais vezes do que precisa, o que deixa a tela lenta.

### DOM

*Fala-se dom, sigla de Document Object Model.*

**O que é.** A árvore de elementos da página, como o navegador a enxerga por
dentro. Quando o JavaScript muda a tela, ele está mexendo no DOM.

### hidratação

*Em inglês, hydration.*

**A armadilha.** Nada a ver com água. O nome vem da ideia de "dar vida" a algo
seco.

**O que é.** O HTML chega pronto do servidor, mas sem comportamento. Hidratação
é o momento em que o JavaScript assume aquele HTML e os botões passam a
funcionar.

**O erro clássico.** Se o servidor desenhou uma coisa e o navegador desenharia
outra, o framework reclama. Costuma acontecer com data, hora e valor aleatório,
que mudam entre os dois momentos.

### bundle

*Fala-se bândol. Ao pé da letra: pacote, fardo.*

**O que é.** O arquivo final de JavaScript entregue ao navegador, com o seu
código e as bibliotecas juntos.

**Por que importa.** Cada kilobyte é tempo de carregamento na mão de quem tem
internet ruim. Em produto embutido em site de terceiro, o tamanho vira requisito
contratual, não detalhe.

### tree shaking

*Fala-se trí chêiquin. Ao pé da letra: sacudir a árvore.*

**A analogia é literal.** Você sacode a árvore e o que está morto cai.

**O que é.** A ferramenta de build remove do pacote final o código que ninguém
usa.

**O que atrapalha.** Arquivos que reexportam tudo de uma pasta, chamados barrel,
confundem a análise e fazem entrar código que não precisava. É por isso que
alguns projetos os proíbem.

### code splitting e lazy loading

*Ao pé da letra: dividir o código e carregar com preguiça.*

**O que é.** Em vez de entregar tudo de uma vez, o pacote é quebrado em pedaços
e cada um é baixado quando necessário. A tela de relatório só baixa quando
alguém abre relatório.

**A troca.** Primeira tela carrega mais rápido, e surge uma pequena espera no
momento da navegação. Por isso se faz um pré-carregamento quando o usuário passa
o mouse sobre o link.

### transpilar e minificar

**Transpilar** é converter código de uma versão da linguagem para outra, para
rodar em navegador antigo. **Minificar** é remover espaços, quebras de linha e
encurtar nomes para o arquivo ficar menor.

**Source map** é o arquivo que faz o caminho de volta, para você conseguir
depurar o código minificado e ver o original.

### polyfill

*Fala-se pólifil. Ao pé da letra: preenchimento.*

**O que é.** Um pedaço de código que implementa um recurso moderno em navegador
que não o tem. Preenche o buraco.

### debounce e throttle

*Fala-se dibáuns e thrótol.*

**O que são.** Duas formas de controlar função que dispara demais.

**Debounce** espera a pessoa parar. Se ela digita dez letras, a busca roda uma
vez, depois da última. Ao pé da letra, debounce vem de "tirar o repique" de um
botão físico.

**Throttle** deixa passar no máximo uma vez a cada intervalo. Se a pessoa rola a
página sem parar, a função roda a cada 200 milissegundos.

**Qual usar.** Debounce para busca enquanto digita. Throttle para rolagem e
redimensionamento.

### memoização

*Em inglês, memoization. Não é memorização.*

**O que é.** Guardar o resultado de um cálculo para não refazer com a mesma
entrada.

**A troca.** Economiza processamento e gasta memória. Em componente pequeno, o
custo de comparar as entradas pode ser maior que o cálculo que você evitou.

### prop drilling

*Fala-se próp dríling. Ao pé da letra: perfuração de propriedade.*

**O que é.** Passar um dado por cinco componentes só para chegar no sexto, sem
que os intermediários usem.

**As saídas.** Contexto compartilhado, composição com `children`, ou uma
biblioteca de estado. Cada uma com o próprio custo.

### skeleton

*Fala-se squéliton. Ao pé da letra: esqueleto.*

**O que é.** A silhueta cinza no formato do conteúdo, mostrada enquanto os dados
carregam.

**Por que é melhor que o símbolo girando.** Porque dá ao olho a forma do que vem,
e a tela não pula quando o conteúdo chega.

### viewport e breakpoint

**Viewport** é a área visível da página, que muda de tamanho conforme o
dispositivo. **Breakpoint** é a largura em que o layout muda de arranjo.

### i18n e l10n

**O que são.** Abreviações estranhas que confundem todo mundo no começo. O
número é a quantidade de letras entre a primeira e a última. I18n é
internationalization, com 18 letras no meio. L10n é localization.

**A diferença.** Internacionalizar é preparar o sistema para funcionar em
qualquer idioma, tirando texto cravado no código. Localizar é adaptar para um
lugar específico, com idioma, moeda, formato de data e regras locais.

### a11y

**O que é.** Mesma brincadeira, accessibility com 11 letras no meio.
Acessibilidade.

### toast, modal, dialog e drawer

**O que são.** Quatro formas de mostrar algo por cima da tela, e o uso é
diferente.

- **Toast** é o aviso curto que aparece num canto e some sozinho. Para confirmação, não para erro que exige ação.
- **Dialog** é a caixa que pede uma decisão e bloqueia o resto.
- **Modal** é o adjetivo, significa que bloqueia o que está atrás. Um dialog pode ser modal ou não.
- **Drawer** é o painel que desliza da lateral, bom para formulário sem tirar o contexto da tela.

---

## 9. Segurança

### autenticação e autorização

*Em inglês, authentication e authorization, abreviados authn e authz.*

**A diferença.** Autenticação responde quem é você. Autorização responde o que
você pode fazer. Confundir as duas é a origem de falha real.

**Na prática.** 401 é "não sei quem você é". 403 é "sei quem você é e você não
pode".

### token

*Fala-se tôuquen. Ao pé da letra: ficha.*

**A analogia é exata.** É a ficha do metrô. Você provou sua identidade uma vez,
recebeu uma ficha e agora usa a ficha. A catraca não precisa te reconhecer, só
precisa validar a ficha.

**A consequência.** Quem roubar a ficha entra no seu lugar. Por isso ficha tem
validade curta e nunca vai em lugar que o JavaScript de terceiro consegue ler.

### hash

*Fala-se réxi. Ao pé da letra: picadinho.*

**O que é.** Uma transformação de mão única. O mesmo texto sempre gera o mesmo
resultado, e do resultado não se volta ao texto.

**Onde se usa.** Senha. O sistema não guarda a sua senha, guarda o hash. Quando
você entra, ele aplica a mesma transformação e compara.

**A diferença para criptografia.** Criptografia volta, com a chave certa. Hash
não volta. Dizer "senha criptografada" é quase sempre erro de vocabulário.

### salt

*Ao pé da letra: sal.*

**O que é.** Um valor aleatório acrescentado à senha antes de gerar o hash, e
guardado junto.

**Por que existe.** Sem ele, duas pessoas com a mesma senha teriam o mesmo hash,
e um atacante com uma tabela pronta descobriria as duas de uma vez. Com sal,
cada hash é único.

### sanitizar e escapar

**O que são.** Duas defesas contra conteúdo malicioso.

**Escapar** é transformar caracteres especiais para que sejam lidos como texto, e
não como código. **Sanitizar** é remover ou limpar o que é perigoso antes de
guardar ou exibir.

**A regra que resolve a maioria dos casos.** Trate conteúdo de usuário sempre
como texto. Só renderize como HTML quando for mesmo necessário, e com uma
biblioteca de limpeza no caminho.

### princípio do menor privilégio

*Em inglês, least privilege.*

**O que é.** Cada pessoa e cada serviço recebe o mínimo de permissão necessária
para fazer o trabalho, e nada além.

**Na prática.** O serviço que só lê não deve ter permissão de escrever. O usuário
de suporte não deve enxergar dado financeiro.

### superfície de ataque

*Em inglês, attack surface.*

**O que é.** Tudo que está exposto e pode ser alvo. Cada endpoint público, cada
campo de formulário, cada dependência de terceiro.

**A consequência prática.** Toda funcionalidade nova aumenta a superfície. Por
isso remover código morto é ato de segurança, não só de limpeza.

### defesa em profundidade

*Em inglês, defense in depth.*

**O que é.** Não depender de uma proteção só. Se uma falhar, a próxima segura.

**Exemplo concreto.** Esconder o botão no menu, conferir a permissão no servidor
e conferir de novo na API. Parece redundante e é de propósito. Esconder o botão
não protege nada sozinho, porque a API pode ser chamada direto.

### falhar fechado e falhar aberto

*Em inglês, fail closed e fail open.*

**O que são.** A decisão de o que fazer quando a verificação não consegue
responder.

Falhar fechado nega por padrão. É o certo para permissão e pagamento. Falhar
aberto libera por padrão. É escolhido quando indisponibilidade da verificação
não pode derrubar o produto inteiro.

**O ponto.** Qualquer um dos dois está certo, desde que seja escolha explícita e
registrada. O errado é descobrir qual dos dois você tem por acidente, durante o
incidente.

---

## 10. Infraestrutura e operação

### contêiner e imagem

*Em inglês, container e image.*

**A armadilha.** Contêiner lembra o do navio, o que até ajuda. Imagem não lembra
nada de útil, porque não é foto.

**O que são.** A imagem é o pacote parado, com o programa e tudo que ele precisa.
O contêiner é a imagem rodando. Uma imagem gera muitos contêineres.

**Analogia.** A imagem é a receita com os ingredientes já separados. O contêiner
é o prato feito. Da mesma receita saem dez pratos iguais.

### orquestrador

*Em inglês, orchestrator. Na prática, Kubernetes.*

**O que é.** O programa que cuida dos contêineres em produção. Mantém o número
certo de cópias, reinicia o que morreu, distribui a carga e troca a versão sem
derrubar.

**A troca.** Ganha resiliência e escala automática. Paga com uma camada inteira
de complexidade, vocabulário novo e a necessidade de alguém que saiba operar.
Para sistema pequeno, não compensa.

### escalar na vertical e na horizontal

**O que são.** Vertical é dar mais memória e processador para a mesma máquina.
Horizontal é colocar mais máquinas.

**A troca.** Vertical é simples e tem teto, porque existe um limite de máquina
disponível. Horizontal não tem teto prático e exige que o serviço seja sem
estado, mais um distribuidor de carga na frente.

### cold start

*Fala-se côuld stárt. Ao pé da letra: partida a frio.*

**O que é.** A primeira requisição depois que um processo sobe é mais lenta,
porque ainda falta carregar e aquecer coisas.

**Onde dói.** Em ambiente que desliga o serviço quando ninguém usa, o primeiro
usuário depois da pausa paga a conta.

### graceful shutdown

*Ao pé da letra: desligamento gracioso.*

**O que é.** Quando o sistema recebe ordem de desligar, em vez de morrer na
hora, ele para de aceitar novos pedidos, termina o que está em andamento e só
então encerra.

**Por que importa.** Sem isso, toda publicação de versão nova corta requisições
no meio. O usuário vê erro sem que nada esteja quebrado.

### health check

*Fala-se rélth tchéque. Ao pé da letra: exame de saúde.*

**O que é.** Um endereço que responde se o serviço está de pé. O orquestrador
pergunta de tempos em tempos.

**O detalhe que separa bom de ruim.** Existem dois tipos. Um responde "estou
vivo", e se falhar o processo é reiniciado. Outro responde "estou pronto para
receber tráfego", e se falhar o serviço só é tirado da fila. Confundir os dois
faz o sistema reiniciar quando deveria apenas esperar.

### observabilidade

**O que é.** A capacidade de entender o que está acontecendo por dentro
olhando só o que sai.

**A diferença para monitoramento.** Monitoramento responde perguntas que você
preparou antes, como "está no ar?". Observabilidade permite investigar uma
pergunta nova, que você não previu, durante o incidente.

**Os três pilares.** Log é o registro de um evento. Métrica é um número ao longo
do tempo. Trace é o caminho completo de uma requisição entre os serviços, com o
tempo gasto em cada parada.

### SLA, SLO e SLI

**O que são.** Três siglas parecidas que confundem.

**SLI** é o indicador medido, como a porcentagem de requisições abaixo de 300
milissegundos. **SLO** é o objetivo interno para esse indicador, como 99,9%.
**SLA** é o contrato com o cliente, com multa se não cumprir.

**A regra prática.** O objetivo interno é sempre mais rígido que o contrato.
Assim você descobre o problema antes do cliente.

### orçamento de erro

*Em inglês, error budget.*

**O que é.** Se o objetivo é 99,9%, sobram 0,1% de falhas permitidas no período.
Isso é orçamento, e serve para decidir.

**Como se usa.** Sobrou orçamento, o time pode arriscar e lançar novidade.
Acabou o orçamento, o time para de lançar e volta a estabilizar. Tira a
discussão do achismo.

### incidente e postmortem

**Incidente** é quando algo quebra para o usuário. **Postmortem** é o documento
escrito depois, explicando o que houve, por quê e o que muda.

**A regra que faz funcionar.** Postmortem sem culpado. O objetivo é achar a
falha do processo, não a pessoa. Quando se procura culpado, as pessoas escondem
informação e a mesma falha volta.

### blast radius

*Ao pé da letra: raio da explosão.*

**O que é.** O tamanho do estrago se aquilo falhar. Uma alteração que afeta uma
conta tem raio pequeno. Uma que afeta todas, enorme.

**Como se usa.** É o que justifica liberar aos poucos, por conta ou por
percentual de usuários, em vez de ligar para todo mundo de uma vez.

### blue-green e canário

**O que são.** Duas formas de trocar versão sem derrubar.

**Blue-green** mantém duas instalações completas e vira a chave de uma para
outra. Voltar atrás é virar a chave de novo.

**Canário** manda uma fração pequena do tráfego para a versão nova e observa.
Se os números piorarem, cancela.

**A origem do nome canário.** Mineiros levavam canários para a mina. Se o pássaro
passasse mal, era hora de sair. O pássaro detectava o problema antes das pessoas.

### feature flag

*Ao pé da letra: bandeira de funcionalidade.*

**O que é.** Um interruptor que liga ou desliga um recurso sem publicar código
novo.

**Por que muda tudo.** Separa publicar de liberar. O código pode estar em
produção há duas semanas, desligado, e ser ligado para um cliente só.

**A troca.** Ganha controle e reversão instantânea. Paga com código que precisa
funcionar nos dois estados, e com o risco de acumular interruptores antigos que
ninguém remove.

### alert fatigue

*Ao pé da letra: fadiga de alerta.*

**O que é.** Quando o sistema avisa demais, as pessoas param de prestar atenção.
Aí o alerta que importa passa despercebido no meio dos cem que não importam.

**A regra.** Alerta que não exige ação humana imediata não deveria acordar
ninguém. Isso é painel, não alerta.

---

## 11. Processo e time

### sprint

*Fala-se sprint. Ao pé da letra: corrida curta.*

**O que é.** Um período fixo, normalmente de duas semanas, com um conjunto de
trabalho combinado no começo.

**A crítica honesta.** A palavra sugere correr, e esse é o mal-entendido mais
comum. O propósito é ritmo previsível, não velocidade máxima. Time que
corre todo sprint acumula dívida técnica e cansaço.

### backlog

*Fala-se béclóg. Ao pé da letra: acúmulo, atraso.*

**O que é.** A lista de tudo que ainda não foi feito, em ordem de prioridade.

### refinamento

*Em inglês, refinement ou grooming.*

**O que é.** A reunião em que o time lê os itens futuros, tira dúvidas e estima.
O objetivo é que ninguém comece uma tarefa descobrindo na hora que falta
informação.

### daily

*Fala-se déili. Ao pé da letra: diária.*

**O que é.** Reunião curta diária. A versão útil responde uma pergunta: alguém
está travado?

**A versão inútil.** Cada pessoa narra o que fez, ninguém escuta e a reunião
vira relatório para o gestor.

### retrospectiva

**O que é.** Reunião periódica sobre o processo, não sobre o produto. O que
funcionou, o que atrapalhou, o que muda.

**O que a faz funcionar.** Sair com no máximo duas ações concretas, com
responsável. Retrospectiva que vira lista de reclamação sem ação some do
calendário em dois meses.

### kanban e WIP

**Kanban** é o quadro com colunas, onde cada cartão anda da esquerda para a
direita.

**WIP** é work in progress, trabalho em andamento. O limite de WIP é a regra
mais importante e a mais ignorada: cada coluna aceita no máximo N cartões.

**Por que o limite existe.** Começar muita coisa ao mesmo tempo não entrega mais
rápido, entrega mais tarde, tudo junto, no fim. Trabalho pela metade não vale
nada.

### lead time e cycle time

**O que são.** Lead time é o tempo desde o pedido até a entrega, do ponto de
vista de quem pediu. Cycle time é o tempo desde o início do trabalho até a
entrega.

**A diferença importa.** Um time pode ter cycle time ótimo e lead time péssimo,
porque os pedidos ficam meses parados na fila antes de alguém começar.

### story point e estimativa

**O que é.** Um número que representa o tamanho relativo de uma tarefa, em vez
de horas.

**A intenção original.** Tirar a conversa de prazo e colocar em complexidade,
porque pessoas estimam tamanho relativo melhor do que tempo absoluto.

**O que acontece na prática.** Vira moeda de produtividade e volta a ser hora
disfarçada. Vale saber que isso é crítica comum, não heresia.

### MVP, POC e spike

**MVP** é produto mínimo viável. A menor versão que entrega valor real e permite
aprender com usuário de verdade. Não é versão porca, é versão pequena e completa.

**POC** é prova de conceito. Código feito para responder "isso é possível?" e
jogado fora depois. O erro clássico é a prova de conceito virar produção.

**Spike** é uma investigação com tempo fechado. Dois dias para descobrir como
algo funciona, e no fim uma resposta, não um código.

### blocker e dependência

**Blocker** é o que impede o trabalho de andar. A regra é avisar no mesmo dia,
não no fim da semana.

**Dependência** é quando a sua entrega depende de outra pessoa ou time. Toda
dependência é risco de prazo e precisa de combinado explícito.

### scope creep

*Fala-se scôup crip. Ao pé da letra: rastejamento de escopo.*

**O que é.** A tarefa que cresce aos poucos, sem ninguém decidir. Começou com
"só acrescentar um campo" e virou uma tela nova.

**Como se defende.** Não dizendo não para tudo, e sim tornando visível: "isso é
novo, entra como item separado".

### definition of done

*Ao pé da letra: definição de pronto.*

**O que é.** O acordo do time sobre o que significa terminar. Em geral inclui
código revisado, teste passando, texto revisado e documentação atualizada.

**Por que existe.** Porque "pronto" sem acordo significa coisas diferentes para
cada pessoa, e metade do retrabalho nasce daí.

### handoff

*Fala-se rénd-óf. Ao pé da letra: passagem de bastão.*

**O que é.** Passar um trabalho de uma pessoa ou time para outro.

**O custo escondido.** Todo handoff perde contexto. Por isso times tentam
reduzir o número de passagens entre especialidades.

### bus factor

*Ao pé da letra: fator ônibus.*

**O que é.** Quantas pessoas precisariam sumir para o projeto travar. Se só uma
pessoa entende o módulo de pagamento, o fator é um.

**A origem macabra do nome.** A pergunta original é quantas pessoas do time
precisariam ser atropeladas por um ônibus.

**Como se melhora.** Revisão de código, documentação e rodízio de quem mexe em
cada parte.

---

## 12. Expressões e gírias

### rubber duck debugging

*Ao pé da letra: depuração com pato de borracha.*

**O que é.** Explicar o problema em voz alta, para um colega ou para um objeto
na mesa. Uma parte das soluções aparece no meio da explicação, antes de qualquer
resposta.

**Por que funciona.** Explicar obriga você a organizar o raciocínio e a
verbalizar a suposição errada que estava escondida.

### yak shaving

*Ao pé da letra: tosar o iaque.*

**O que é.** A sequência absurda de tarefas sem relação que você precisa fazer
antes da tarefa real. Para corrigir um bug você precisa subir o projeto, para
isso precisa atualizar uma ferramenta, que exige atualizar outra, e três horas
depois você está configurando certificado.

**Na conversa.** "Passei a manhã no yak shaving" quer dizer que nada do
planejado andou, por motivos legítimos.

### bikeshedding

*Ao pé da letra: discutir o galpão de bicicletas.*

**O que é.** Gastar a reunião no detalhe irrelevante e aprovar o assunto difícil
em dois minutos.

**A origem.** Um comitê aprova uma usina nuclear rapidamente, porque ninguém
entende, e passa horas discutindo a cor do galpão de bicicletas, porque todo
mundo tem opinião.

### foot gun

*Ao pé da letra: arma apontada para o pé.*

**O que é.** Um recurso que funciona e facilita demais o erro grave. A
ferramenta não está quebrada, só é fácil se machucar com ela.

### happy path, sad path

Já vimos o caminho feliz. O caminho triste é o resto: rede caiu, campo veio
vazio, o usuário clicou duas vezes, o arquivo tem 400 megabytes.

### flaky test

*Ao pé da letra: teste escamoso, instável.*

**O que é.** O teste que passa e falha alternadamente, sem ninguém mudar nada.
Costuma envolver tempo, ordem de execução ou dependência externa.

**Por que é perigoso.** Ele ensina o time a ignorar teste vermelho. Aí o dia em
que um teste real falhar, ninguém vai olhar.

### works on my machine

*Ao pé da letra: funciona na minha máquina.*

**O que é.** A frase que virou piada porque é verdadeira e inútil ao mesmo
tempo. A diferença está sempre no ambiente, e é exatamente o problema que o
Docker nasceu para resolver.

### greenfield e brownfield

**O que são.** Campo verde é projeto do zero, sem restrição herdada. Campo
marrom é trabalhar em cima do que já existe.

**A realidade da carreira.** Quase todo trabalho é campo marrom. Saber se mover
em código que você não escreveu vale mais do que saber começar do zero.

### silver bullet

*Ao pé da letra: bala de prata.*

**O que é.** A solução mágica que resolve tudo. Não existe, e o ensaio clássico
que diz isso tem quarenta anos e continua certo.

**Na conversa.** "Não existe bala de prata" é a resposta madura para quem
propõe trocar a tecnologia inteira para resolver um problema de processo.

### premature optimization

*Ao pé da letra: otimização prematura.*

**O que é.** Deixar rápido o que ninguém mediu, e no caminho tornar o código
difícil de entender.

**A frase completa, que quase sempre é citada pela metade.** Donald Knuth disse
que a otimização prematura é a raiz de todo mal, e disse também que o programador
deve se preocupar com o trecho crítico depois de identificá-lo. A conclusão não é
"nunca otimize". É "meça antes".

### cargo cult

*Ao pé da letra: culto à carga.*

**O que é.** Copiar a forma sem entender o motivo. Usar uma arquitetura porque
uma empresa grande usa, sem ter o problema que ela tinha.

**A origem.** Comunidades do Pacífico que, depois da Segunda Guerra, construíram
pistas e torres de madeira esperando que os aviões de carga voltassem.

---

## Como continuar

Três hábitos que resolvem o vocabulário no médio prazo:

**Pergunte na hora.** "O que é outbox?" numa reunião custa dez segundos. Fingir
que entendeu custa a tarefa inteira. Ninguém no time se incomoda com a primeira
pergunta, só com a terceira vez sem anotar.

**Anote o termo e a frase em que ele apareceu.** A palavra solta não gruda. A
palavra no contexto, sim. "Falaram de circuit breaker quando a API da Meta
começou a demorar" vale mais que a definição.

**Procure sempre pelo trade-off.** Ao aprender um conceito novo, a pergunta
seguinte é o que ele custa. Quem só sabe para que serve repete o que leu. Quem
sabe o custo consegue decidir.

