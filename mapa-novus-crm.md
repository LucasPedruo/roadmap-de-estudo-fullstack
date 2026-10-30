# Mapa do Novus CRM

Roteiro de estudo para quem vai trabalhar no repositório do Novus CRM e está
começando na programação. Cada etapa diz o que aprender, qual arquivo abrir
dentro do projeto e como saber que você entendeu.

| Número | O que é |
|---|---|
| 8 | aplicações em `apps/` |
| 19 | projetos .NET em `backend/` |
| 174 | controllers na Web.Api |
| 64 | componentes base em `packages/ui` |
| 2 | linguagens principais, C# e TypeScript |

## Sumário

1. [Como usar este mapa](#0-como-usar-este-mapa)
2. [O território](#1-o-território)
3. [As 14 etapas](#2-as-14-etapas)
4. [Backend por dentro](#3-backend-por-dentro)
5. [Frontend por dentro](#4-frontend-por-dentro)
6. [Infraestrutura e entrega](#5-infraestrutura-e-entrega)
7. [As sete armadilhas](#6-as-sete-armadilhas)
8. [Glossário](#7-glossário)
9. [Seus primeiros 30 dias](#8-seus-primeiros-30-dias)

---

## 0. Como usar este mapa

Se você já sabe Angular, TypeScript e lógica, isso cobre boa parte das etapas 3
e 4. O que falta é o resto do território. O repositório junta duas stacks
diferentes no mesmo lugar, e cada uma tem suas próprias regras.

Leia de cima para baixo na primeira vez. Depois volte pelo sumário ou pelo
glossário. Toda palavra estranha que aparecer aqui está explicada no fim.

Em cada etapa você encontra quatro blocos:

- **Estude** é o conceito solto, o que você procuraria num curso.
- **Abra aqui** é o arquivo real do repositório que mostra o conceito funcionando.
- **Exercício** é o que fazer com as mãos.
- **Pronto quando** é o teste que diz se você pode seguir adiante.

### As cinco trilhas

Cada etapa pertence a um território. A mesma marcação volta no glossário.

| Trilha | Assunto |
|---|---|
| Fundamentos | Terminal, Git, monorepo, conceitos de programação |
| Frontend | React, Next.js, design system, as telas |
| Backend | C#, .NET, camadas, API |
| Dados | Banco, filas, cache, tempo real, infraestrutura |
| Processo | Como o time trabalha |

---

## 1. O território

O Novus CRM é uma plataforma de atendimento e vendas. Uma empresa cliente usa o
sistema para conversar com os clientes dela por WhatsApp, e-mail, Instagram,
telefone e webchat. Em volta dessas conversas existem tickets de suporte, funil
de vendas, campanhas de disparo, chatbots, relatórios e uma loja virtual.

Tudo isso mora num repositório só, o que se chama **monorepo**. Em vez de um
repositório por aplicação, existe um repositório com pastas. A vantagem é mudar
o backend e a tela na mesma alteração. O custo é que você precisa saber em qual
pasta está pisando, porque as regras mudam de pasta para pasta.

### As pastas da raiz

| Pasta | O que é | Tecnologia |
|---|---|---|
| `backend/` | Toda a lógica, o banco e as APIs | .NET 10, C# |
| `apps/` | As telas, oito aplicações diferentes | Next.js, Vite, Expo |
| `packages/` | Código compartilhado entre as telas | TypeScript |
| `openspec/` | Especificações escritas antes do código | Markdown |
| `.claude/` | Configuração dos agentes de IA e da memória do time | Markdown, TypeScript |
| `.azure-pipelines/` | Modelos usados pela esteira de build | YAML |

### As oito aplicações de `apps/`

Elas não são variações da mesma coisa. Cada uma tem público, tecnologia e
restrição próprios. Confundir as convenções de uma com as de outra é o erro
mais comum de quem chega.

**admin** (`apps/admin`, localhost:3000)
O produto que o cliente usa. Chat, tickets, CRM, fluxos, loja, painéis. É a
aplicação maior e mais complexa do repositório. Next.js 16 com renderização no
servidor e sessão em cookie.

**backoffice** (`apps/backoffice`, localhost:3003)
Painel interno do time e dos donos de white-label. Enxerga a base inteira de
clientes, então trava mais a segurança. Controle de acesso por papel, não por
permissão como no admin.

**landing-page** (`apps/landing-page`, localhost:3001)
Site de marketing, novuscrm.com.br. Gera arquivos estáticos no build e não tem
servidor em produção. Nada de sessão, cookie ou chamada ao backend por aqui.

**status** (`apps/status`, localhost:5173)
Página pública de status dos serviços. Precisa ser a última coisa a cair, então
é um arquivo estático servido por nginx. Vite e React, sem Next.

**widget** (`apps/widget`)
O chat que roda dentro de um iframe no site do cliente. Sem autenticação, com o
tamanho do arquivo final tratado como requisito. Convive com a versão Angular
antiga em produção.

**mobile** (`apps/mobile`)
Aplicativo React Native com Expo Router. Não usa CSS nem nada do navegador.
Compartilha só os tokens e as regras puras com as telas web.

**docs** (`apps/docs`, localhost:3002)
Documentação pública em Mintlify. É a única pasta de `apps/` fora do Bun e do
Turborepo, porque não tem `package.json`.

**plugins-borda** (`apps/plugins-borda`)
Worker da Cloudflare que serve os plugins hospedados perto do usuário. Deploy
por `wrangler`, sem Docker.

### Os pacotes de `packages/`

| Pacote | Para que serve |
|---|---|
| `@novuscrm/ui` | O design system das telas web. 64 componentes base, de botão a tabela. |
| `@novuscrm/ui-core` | Tokens, temas e funções puras. Não conhece React nem navegador, por isso o mobile consegue usar. |
| `@novuscrm/analytics` | Medição de uso, GA4 e atribuição. |
| `@novuscrm/wlclient` | Protocolo de conversa entre o admin e um plugin dentro do iframe. |
| `plugin-cli`, `plugin-corpus`, `plugin-validador` | Ferramentas para quem escreve plugins para a plataforma. |
| `proposta-comercial` | Geração da proposta comercial compartilhada entre telas. |

### Os 19 projetos de `backend/`

Em C#, um projeto é uma pasta com um arquivo `.csproj` que vira uma biblioteca
ou um programa. Eles se dividem em camadas, que são bibliotecas, e serviços, que
são programas que rodam sozinhos.

**Camadas**

- `Dominio` guarda as regras do negócio e não depende de ninguém.
- `Aplicacao` e `Aplicacao.DTO` orquestram os casos de uso.
- `Infraestrutura` e `Infraestrutura.Data` falam com banco, fila, cache e serviços externos.
- `Infraestrutura.Ioc` liga tudo. `Abstracao` guarda contratos comuns.

**Serviços que rodam**

- `Web.Api` é a API principal, com 174 controllers.
- `CPlusChat.Workers` roda as tarefas de fundo.
- `CPlusChat.ApiPublica` é a API que os clientes integram.
- `IA.Web.Api` cuida das chamadas de inteligência artificial.
- `StatusMonitor` vigia os outros e alimenta a página de status.
- `AppHost` sobe tudo junto na sua máquina.

**Integrações e testes**

- `Plugin.MetaMessenger` fala com a API da Meta.
- `Vvs.Pix.K8Bank` cuida de pagamento por Pix.
- `ServiceDefaults` concentra métrica, log e health check.
- `Aplicacao.Tests` e `IA.Web.Api.Tests` guardam os testes automatizados.

---

## 2. As 14 etapas

A ordem importa. Cada etapa assume a anterior. O tempo sugerido conta dias de
trabalho normal, com tarefas reais no meio, não estudo em tempo integral.

### Etapa 1. Terminal, Git e o repositório na sua máquina

`Fundamentos` · cerca de 3 dias

Antes de qualquer código, você precisa se mover pelo repositório sem medo. Git é
o programa que guarda o histórico de tudo que o time escreveu. Cada alteração
vira um **commit**, que é uma foto do código com uma mensagem explicando o que
mudou.

**Estude**

- Navegar no terminal. `cd`, `ls`, `cat`, `grep`.
- Git do dia a dia. `status`, `diff`, `add`, `commit`, `pull`, `push`.
- Branch, merge e conflito. Um branch é uma linha de trabalho paralela.
- O que é um Pull Request e por que ninguém sobe direto para a linha principal.

**Abra aqui**

Rode `git log --oneline -20` e leia as mensagens. O padrão é
`tipo(escopo): descrição`, em português e no imperativo. Depois rode
`git log -- apps/admin`. Aparecem 2326 commits antigos, porque o admin veio de
outro repositório e o histórico foi preservado na mudança.

**Exercício**

Crie um branch, mude uma palavra num arquivo `.md`, veja o `git diff`, faça o
commit e desfaça tudo com `git reset --hard`. Repita até não ter mais dúvida
sobre o que cada comando fez.

**Pronto quando**

Você explica para outra pessoa a diferença entre o que está salvo no arquivo, o
que está na área de stage e o que já virou commit.

### Etapa 2. O monorepo, o Bun e o Turborepo

`Fundamentos` · cerca de 2 dias

Vindo do Angular você conhece o `npm install`. Aqui o gerenciador de pacotes é o
**Bun**, e `npm`, `npx`, `yarn` e `pnpm` estão proibidos. Não é preferência
estética. Cada gerenciador escreve um arquivo de trava diferente, e dois
arquivos de trava no mesmo repositório quebram o build de todo mundo.

O **workspace** é o truque que faz oito aplicações dividirem uma pasta
`node_modules` só. Por isso `bun install` roda apenas na raiz. Rodar dentro de
`apps/admin` cria um segundo arquivo de trava e quebra o workspace.

O **Turborepo** é o orquestrador. Ele sabe que o admin depende do `packages/ui`,
então constrói o pacote antes da aplicação. E guarda o resultado em cache. Um
segundo build sem mudanças deve terminar na hora com a mensagem `FULL TURBO`.

**Estude**

- O que é `package.json`, dependência e arquivo de trava.
- Workspace de monorepo e por que o `node_modules` fica na raiz.
- Cache de build e grafo de dependências.

**Abra aqui**

`package.json` da raiz, a lista de scripts. Depois `turbo.json`, onde a tarefa
`build` declara `dependsOn: ["^build"]`. O acento circunflexo significa "o build
das minhas dependências primeiro". Leia também `.claude/MONOREPO.md`, que é a
referência operacional de tudo que é transversal.

**Exercício**

Rode `bun install` na raiz. Depois `bun run dev:admin` e abra
`localhost:3000`. Rode `bun run build:landing` duas vezes seguidas e veja a
segunda vir do cache.

**Pronto quando**

Você sobe qualquer uma das aplicações sem consultar a documentação e sabe dizer
qual comando roda da raiz e qual roda de dentro da pasta.

### Etapa 3. TypeScript como se usa aqui

`Frontend` · cerca de 3 dias

Você já escreve TypeScript no Angular. O que muda é o rigor. O modo **strict**
está ligado, e o Biome, que é o programa que confere estilo e erros, barra
coisas que o Angular costuma deixar passar.

**O que o Biome recusa**

- `any`. Se você não sabe o tipo, descreva o que sabe.
- `enum`. Use união de strings ou objeto constante.
- Exportação padrão, fora dos arquivos especiais do Next.
- A exclamação de "confie em mim, não é nulo", chamada non-null assertion.
- `@ts-ignore`.

**Estude**

- Tipo união, tipo literal e união discriminada.
- Tipo genérico. É o `<T>` que aparece em toda função reaproveitável.
- `unknown` contra `any`. O primeiro obriga você a verificar antes de usar.
- Zod, a biblioteca que valida dados que chegam de fora e gera o tipo a partir da regra.

**Abra aqui**

`apps/admin/src/types/` tem os contratos por domínio. Note o par de nomes.
`<Tipo>Dto` é o espelho exato do que a API devolve. `<Tipo>View` é a forma que a
tela quer. A conversão entre os dois fica no arquivo `mappers.ts` de cada
domínio.

**Exercício**

Escolha um domínio pequeno, como `etiqueta`. Leia o tipo, o schema do Zod e o
mapper. Desenhe no papel o caminho do dado desde o JSON da API até o campo do
formulário.

**Pronto quando**

Você lê uma união discriminada como `{ ok: true, dados } | { ok: false, error }`
e sabe por que o TypeScript obriga você a testar o `ok` antes de acessar
`dados`.

### Etapa 4. React e Next.js, o salto maior vindo do Angular

`Frontend` · cerca de 1 semana

Esta é a etapa que mais custa para quem vem do Angular. No Angular você tem
componente com classe, template separado, módulos, injeção de dependência e
RxJS. Em React quase nada disso existe. Um componente é uma função que devolve a
tela. O estado vive em **hooks**, que são funções começadas com `use`.

Em cima do React vem o **Next.js 16**, que é o framework de aplicação. A
novidade que mais confunde é a divisão entre componente de servidor e componente
de cliente. Por padrão, tudo roda no servidor e chega ao navegador como HTML
pronto. Só vira código do navegador o arquivo que declara `"use client"` na
primeira linha.

**Tradução rápida do Angular**

| No Angular | Aqui |
|---|---|
| Componente com `@Component` | Função que devolve JSX |
| `*ngIf`, `*ngFor` | `{cond && ...}` e `.map()` |
| Serviço injetado | Módulo importado direto |
| RxJS e Observable | `async`/`await` e hooks |
| `ngOnInit` | Busca no servidor, ou `useEffect` no cliente |
| Reactive Forms | React Hook Form com Zod |
| Módulo e rota declarada | Pasta dentro de `src/app/` |

**Estude**

- JSX, props e estado. `useState`, `useEffect`, `useMemo`.
- Componente de servidor contra componente de cliente, e por que um de servidor não pode ser importado por um de cliente.
- App Router. A pasta vira a URL. `page.tsx`, `layout.tsx`, `loading.tsx`, `error.tsx`.
- Route group, a pasta entre parênteses que organiza sem entrar na URL.

**Abra aqui**

`apps/admin/src/app/` e os grupos `(app)`, `(auth)` e `(painelcliente)`. Abra
qualquer `page.tsx` e repare na regra da casa. A página só busca dado e passa
adiante. A tela de verdade vive em
`src/components/<domínio>/<x>-page/component.tsx`.

**Exercício**

Crie uma rota nova de teste em `apps/admin`, com `page.tsx` buscando dado e um
componente filho marcado como `"use client"` com um botão que conta cliques.
Depois apague a rota.

**Pronto quando**

Você olha um arquivo e responde na hora se ele roda no servidor ou no navegador,
e explica por que `page.tsx` nunca leva `"use client"`.

### Etapa 5. O caminho do dado no admin

`Frontend` · cerca de 4 dias

O navegador nunca fala com o backend .NET direto. Toda chamada passa pelo
servidor do Next. Por isso, quando você abre a aba Network do navegador, só
aparece `localhost:3000`. O token de acesso fica no servidor e nunca chega ao
navegador.

```
page.tsx (componente de servidor)
  └─ chama a server action de src/lib/<domínio>/actions.ts ("use server")
       └─ chama o cliente de API de src/lib/api/<recurso>.ts
            └─ server-fetch.ts (chamada autenticada) → Web.Api
```

**A regra do contrato**

Toda server action devolve `{ ok: true, ...dados }` ou `{ ok: false, error }`. A
página lança erro quando `ok` é falso, e o `error.tsx` do grupo mostra a tela de
falha com botão de tentar de novo. Falha de carregamento nunca vira lista vazia.
Lista vazia mente para o usuário.

**Abra aqui**

`apps/admin/src/lib/etiqueta/actions.ts` é um exemplo curto.
`apps/admin/src/lib/api/server-fetch.ts` mostra o resto, incluindo o detalhe de
que boa parte das consultas do backend usa POST em vez de GET.

**Exercício**

Siga uma tela inteira de listagem, do `page.tsx` até o endpoint do backend.
Anote o nome de cada arquivo no caminho.

**Pronto quando**

Você consegue adicionar um campo novo numa tela existente, passando pelo tipo,
pelo mapper, pela action e pelo componente.

### Etapa 6. O design system

`Frontend` · cerca de 2 dias

`packages/ui` guarda os 64 componentes base. Botão, campo, seletor, gaveta,
diálogo, tabela. Antes de criar qualquer componente visual, procure lá. O
`Button` sozinho tem mais de 300 chamadas só no admin, então mudar uma
propriedade dele mexe em cinco aplicações ao mesmo tempo.

`packages/ui-core` é a camada que não conhece React. Ela guarda os **tokens**,
que são os nomes das cores, espaçamentos e tamanhos. O mobile usa esse pacote
porque React Native não entende CSS.

**Regras que não se negociam**

- Importe pelo caminho público, `@novuscrm/ui/button/component`. Não existe barrel, que é aquele arquivo índice que reexporta tudo.
- Cor só por token semântico. Nada de hexadecimal escrito na classe.
- Nada de `next/*` dentro do pacote, porque status, widget e mobile não têm Next.
- Componente preso a regra de negócio fica na aplicação, não no pacote.

**Abra aqui**

`packages/ui/CLAUDE.md`, que é a fonte única dessas regras. Depois
`packages/ui/src/button/component.tsx` para ver como um componente é escrito.

**Pronto quando**

Diante de um pedido de tela nova, você sabe dizer o que já existe no pacote e o
que precisa ser escrito na aplicação.

### Etapa 7. As outras telas e por que elas divergem

`Frontend` · cerca de 2 dias

Cada aplicação tem uma restrição que explica suas escolhas. Copiar a convenção
do admin para as outras é o erro clássico, e os guias de cada pasta avisam isso
em letras maiúsculas.

| Aplicação | Restrição | Consequência |
|---|---|---|
| landing-page | Nenhum servidor em produção | Nada de server action, cookie ou revalidação. Só arquivo estático. |
| status | Tem que ser a última coisa a cair | Vite, dado buscado no navegador, token só na memória, CSP apertada. |
| widget | Roda num iframe em site de terceiro | Sem autenticação, cookie pode estar bloqueado, tamanho do bundle é requisito. |
| backoffice | Enxerga a base inteira de clientes | Acesso por papel, cabeçalhos de segurança fechados, auditoria no backend. |
| mobile | React Native, sem navegador | Sem CSS, sem DOM. Componentes nativos e `StyleSheet`. |

**Pronto quando**

Você lê o `CLAUDE.md` da pasta antes de editar, por reflexo, e consegue explicar
por que a página de status não usa Next.

### Etapa 8. C# e orientação a objetos

`Backend` · cerca de 1 semana

C# é uma linguagem compilada e de tipagem forte. Diferente do JavaScript, o
código vira um arquivo binário antes de rodar, e o compilador recusa o programa
inteiro se um tipo não bater. O erro aparece na sua máquina, não na do usuário.

O vocabulário de orientação a objetos aparece o tempo todo aqui. Uma **classe**
é a planta. Um **objeto** é a casa construída a partir dela. A classe `Ticket`
descreve o que um chamado tem. Cada chamado aberto no sistema é um objeto dessa
classe.

**O vocabulário mínimo**

- **Propriedade** é um dado guardado no objeto, como `Assunto`.
- **Método** é uma ação que o objeto sabe fazer, como `SetContaDeEmail`.
- **Construtor** é o método que roda quando o objeto nasce.
- **Interface** é um contrato. Diz o que precisa existir e não diz como.
- **Herança** é uma classe aproveitar o que outra já tem. `Ticket` herda de `EntidadeDeUmaConta`, que herda de `EntityBase`.
- **Namespace** é o endereço da classe, parecido com a pasta.
- **Nulo** é a ausência de valor. O ponto de interrogação em `DateTime?` marca que o campo aceita ficar vazio.

**Estude**

- Classe, objeto, propriedade, método, construtor e interface.
- `async` e `await` com `Task`. O conceito é o mesmo do JavaScript.
- LINQ, a forma de consultar listas. Parece muito com `map` e `filter`.
- Injeção de dependência. Você pede a interface no construtor e o sistema entrega a implementação.

**Abra aqui**

`backend/Dominio/Tickets/Ticket.cs`. Leia só as primeiras 80 linhas. Você vai
ver propriedades em português, datas que aceitam nulo e o par de chave
estrangeira com navegação, como `IdCriadoPor` ao lado de `CriadoPor`. Depois
`backend/Dominio/EntidadeDeUmaConta.cs`. São sete linhas e elas carregam a regra
mais importante do sistema.

**Nomes que o repositório exige**

Data sempre no formato `Data` mais preposição mais substantivo.
`DataDaCriacao`, `DataDoInicio`, `DataDaConclusao`. Nunca invente `CriadaEm` ou
`AtualizadoEm`. Exclusão lógica é `Excluido`. Marca de ativo é `Ativo` ou
`Ativa`.

**Pronto quando**

Você lê uma classe do domínio e sabe dizer quais propriedades viram colunas no
banco e quais existem só na memória.

### Etapa 9. A arquitetura em camadas

`Backend` · cerca de 4 dias

O backend segue **DDD**, sigla de Domain-Driven Design, que em português vira
projeto orientado a domínio. A ideia central é simples. As regras do negócio
ficam separadas da tecnologia. Trocar o banco de dados não deveria mexer na
regra de quando um ticket vence.

A separação vira direção de dependência. As camadas de baixo não conhecem as de
cima.

```
Dominio                        regras e entidades, não depende de nada
  ↓
Aplicacao, Aplicacao.DTO       casos de uso, conhece o domínio
  ↓
Infraestrutura, .Data          banco, fila, cache, serviço externo
  ↓
Infraestrutura.Ioc             liga interface com implementação
  ↓
Web.Api, Workers, ApiPublica   os programas que rodam de verdade
```

**Caminho de uma requisição**

O navegador chama o Next, o Next chama a Web.Api. Lá dentro o **controller**
recebe a chamada e não decide nada sozinho. Ele chama um **AppService** da
camada de aplicação. O AppService usa um **repositório** para ler e gravar,
aplica a regra do domínio e devolve um **DTO**. O controller converte em JSON e
responde.

**Abra aqui**

Escolha um controller pequeno em `backend/Web.Api/Controllers/` e siga a chamada
até o AppService correspondente em `backend/Aplicacao/`. Não tente ler os 174 de
uma vez.

**Pronto quando**

Diante de um bug você sabe em qual camada procurar, em vez de procurar em todas.

### Etapa 10. Banco de dados, EF Core e migrações

`Dados` · cerca de 1 semana

O banco é **PostgreSQL**, um banco relacional. Relacional quer dizer que os
dados moram em tabelas com colunas fixas, e as tabelas se ligam por chaves. A
tabela de ticket guarda o identificador do contato, e o contato mora na tabela
de contatos.

Ninguém escreve SQL na mão no dia a dia. Entre o C# e o banco existe o **Entity
Framework Core**, um ORM. Mapeamento objeto-relacional significa que a classe
`Ticket` vira a tabela de tickets e você escreve consultas em C#.

**Estude**

- Tabela, coluna, linha, chave primária e chave estrangeira.
- Índice. É o que faz a consulta parar de varrer a tabela inteira.
- Restrição de unicidade e de não nulo.
- Transação. Ou tudo grava, ou nada grava.
- SQL básico. `SELECT`, `WHERE`, `JOIN`, `GROUP BY`.

**Os três passos de toda mudança de modelo**

1. Mexer na entidade e no mapeamento dela.
2. Criar a **migração**, que é o arquivo com o comando que altera a estrutura do banco.
3. Regerar o **modelo compilado** com `dotnet ef dbcontext optimize`.

O terceiro passo é o que todo mundo esquece. O sistema usa uma cópia
pré-compilada do modelo para subir mais rápido. Sem regerar essa cópia, o EF não
enxerga a tabela nova e o serviço quebra em produção, mesmo com a migração
aplicada.

> **Cuidado. A conexão padrão aponta para produção.**
> A Web.Api aplica migrações pendentes ao subir. A conexão escrita no
> `appsettings.json` aponta para o banco de produção. Subir a API com uma
> migração que ainda não está em `master` altera a estrutura do banco dos
> clientes. O jeito certo é passar a conexão de desenvolvimento por variável de
> ambiente, como em `ConnectionStrings__Ticket='<dev>' bun run dev:webapi`.

**Abra aqui**

`backend/Infraestrutura.Data/Migrations/`, um arquivo de migração recente e
pequeno. Ignore os arquivos terminados em `.Designer.cs` e o
`SessionModelSnapshot.cs`. Eles são gerados e enormes.

**Pronto quando**

Você cria uma coluna nova do começo ao fim na sua máquina, com migração e modelo
compilado regerado, sem encostar em produção.

### Etapa 11. Multi-tenant, login e permissões

`Dados` · cerca de 4 dias

A plataforma é **multi-tenant**. Muitas empresas usam o mesmo sistema e o mesmo
banco, e cada uma só pode ver os próprios dados. Esse é o requisito mais sério
do projeto. Um vazamento aqui mostra a conversa de um cliente para outro.

O mecanismo começa na classe `EntidadeDeUmaConta`, que acrescenta a coluna
`NumeroConta` em toda entidade. Sobre ela existe um filtro automático que
acrescenta a conta atual em toda consulta. Quando você escreve uma consulta que
desliga esse filtro, você vira o responsável por filtrar na mão.

**Estude**

- Autenticação contra autorização. Quem você é, e o que você pode.
- **Keycloak**, o servidor que cuida do login. O sistema não guarda senha.
- **JWT**, o token assinado que prova quem você é em cada chamada.
- **OIDC** e **PKCE**, o protocolo e a proteção usados pela página de status.
- Papel contra permissão. O backoffice usa papel, o admin usa permissão por rota.

**Abra aqui**

`backend/Infraestrutura/Seguranca/ICurrentConta.cs` e `CurrentConta.cs` mostram
como a conta atual chega até a consulta. No frontend,
`apps/admin/src/lib/auth/route-access.ts` decide quem vê qual rota, e tem
testes.

**A regra de ouro**

Esconder um item do menu não é controle de acesso. A tela confere no servidor, a
action confere de novo e a API confere mais uma vez. Três camadas, de propósito.

**Pronto quando**

Você olha uma consulta nova e identifica se ela está filtrada por conta ou não.

### Etapa 12. O que roda fora da requisição

`Dados` · cerca de 1 semana

Nem tudo acontece enquanto o usuário espera. Disparar uma campanha para dez mil
contatos, sincronizar caixa de e-mail, conferir prazo de ticket. Esse trabalho
vai para outro lugar, e esse lugar tem quatro peças.

**Worker**
Um programa sem tela que fica rodando e trabalhando sozinho. Em
`CPlusChat.Workers` existem mais de 30, um para cada assunto. `WorkerSlaTicket`
confere prazo, `CampanhaWorker` dispara campanha, `EmailSyncWorker` sincroniza
e-mail.

**Fila, o RabbitMQ**
Um correio entre programas. Um lado publica a mensagem e segue a vida. O outro
lado consome quando puder. Se o consumidor estiver fora do ar, a mensagem espera
em vez de se perder.

**Cache, o Redis**
Memória rápida compartilhada. Guarda o que custa caro buscar e muda pouco. O
Redis também serve de ponte para o tempo real funcionar com várias cópias da API
rodando.

**Tempo real, o SignalR**
Conexão aberta entre navegador e servidor. É como a mensagem nova aparece na
tela sem recarregar. O ponto de encontro se chama **hub**.

**O padrão outbox**

Gravar no banco e publicar na fila são duas coisas. Se a gravação dá certo e a
publicação falha, o sistema fica inconsistente. A solução é gravar a mensagem
numa tabela, na mesma transação do dado, e deixar um worker publicar depois.
Isso se chama **outbox** e está em `Dominio/Outbox` e `OutboxWorker.cs`.

> **Cuidado. Evento de tempo real mexe em quatro arquivos.**
> Criar um evento novo exige registro em quatro lugares diferentes. Esquecer um
> deles não quebra só o evento novo. O worker do hub morre ao subir e todo o
> tempo real para. Existe uma skill própria, `novo-evento-realtime`, que confere
> os quatro arquivos.

**Pronto quando**

Diante de um pedido, você decide sozinho se aquilo responde na hora ou vira
trabalho de fundo.

### Etapa 13. Como isso tudo vira produção

`Infra` · cerca de 3 dias

**Docker** empacota o programa junto com tudo que ele precisa para rodar. O
resultado se chama **imagem**. A imagem em execução se chama **contêiner**. Isso
resolve o velho "na minha máquina funciona".

A **pipeline** é a esteira automática. Quando o código chega em `master`, o
Azure DevOps constrói as imagens e envia para o registro. Cada artefato tem a
sua pipeline, com filtro de pasta, para que uma mudança de tela não dispare o
build do backend.

**Detalhe que confunde**

O contexto de build muda por aplicação, de propósito. O admin precisa do arquivo
de trava da raiz, então o contexto é a raiz. O backend precisa do `.sln` e dos
projetos vizinhos, então o contexto é `backend/`.

**O Aspire**

`bun run dev:backend` sobe o `AppHost`, que orquestra Web.Api, workers, serviço
de IA, monitor de status e um Redis em contêiner. Ele mostra um painel com as
portas de cada serviço. Abra `backend/AppHost/Program.cs` para ver a lista
inteira em umas 70 linhas.

**Pronto quando**

Você acompanha uma pipeline da sua alteração até a imagem publicada e entende
cada passo do arquivo YAML.

### Etapa 14. O jeito de trabalhar do time

`Processo` · contínuo

Esta parte não aparece em curso nenhum. É a camada que o time montou em volta do
código, e ignorar ela gera retrabalho.

| Peça | O que faz |
|---|---|
| `CLAUDE.md` | O guia de cada área. Existe na raiz, no backend e em cada aplicação. Leia antes de editar a pasta. |
| `openspec/` | Especificação escrita antes do código. A mudança nasce como proposta, vira tarefas e depois vira código. |
| `.claude/skills/` | Procedimentos prontos. `verificar` roda os testes certos, `revisar` faz a revisão, `humanizer-br` revisa texto. |
| `.claude/memoria/` | Decisões e incidentes registrados. É onde está escrito por que uma regra existe. |
| `.claude/hooks/` | Travas automáticas. Bloqueiam comando perigoso, como subir a API com migração pendente. |

**Commit**

O padrão é `tipo(escopo): descrição no imperativo`, em português, minúscula e
sem ponto final. Os tipos são feat, fix, refactor, chore, style, docs, test,
perf, ci e build. O escopo é a área, como `admin` ou `backend`.

**Texto que uma pessoa lê**

Rótulo de tela, mensagem de erro, e-mail e documentação passam pela skill
`humanizer-br` antes de a tarefa acabar. As regras principais: frase curta, sem
travessão, sem ponto e vírgula, sem adjetivo vago. Em vez de "busca mais
robusta", escreva o que a busca passou a achar.

**Pronto quando**

Antes de abrir qualquer arquivo numa pasta nova, você já abriu o `CLAUDE.md`
dela.

---

## 3. Backend por dentro

### Os domínios do negócio

A pasta `backend/Dominio/` tem mais de 100 subpastas. Cada uma é um assunto do
produto. Vale conhecer os principais antes de abrir qualquer código, porque o
nome da pasta é a sua bússola.

| Domínio | O que guarda |
|---|---|
| `Chat` e `AtendimentoDeChat` | A conversa em si, as mensagens e a fila de atendimento. |
| `Tickets` | Chamado de suporte, com prazo, urgência, categoria e histórico. |
| `Contatos` e `Pessoas` | Quem fala com a empresa e quem trabalha nela. |
| `Funis` e `Vendas` | O CRM, as etapas do negócio e o valor em cada uma. |
| `Fluxos` e `Automacoes` | O chatbot e as regras que agem sozinhas. |
| `Campanhas` e `Transmissoes` | Disparo em massa por canal. |
| `Canais` | WhatsApp, Instagram, e-mail, webchat e telefone. |
| `Telefonia` | Chamada de voz, com LiveKit e FreeSWITCH. |
| `Produtos` e `Lojas` | Catálogo, pedido e carrinho. |
| `BaseConhecimento` e `DadosVetoriais` | Artigos e a busca por significado que a IA usa. |
| `AtendenteIa`, `Luma`, `TriagemIa` | Os recursos de inteligência artificial. |
| `Contas` e `Assinaturas` | A empresa cliente, o plano e a cobrança. |
| `PerfisDeAcesso` e `Seguranca` | Quem pode o quê. |
| `Plugins` | Extensões que rodam dentro do produto. |
| `Outbox` | A tabela que garante a entrega das mensagens para a fila. |

### Os trabalhadores de fundo

Vale abrir `backend/CPlusChat.Workers/` e ler só os nomes dos arquivos. Eles
contam o que o sistema faz quando ninguém está olhando.

| Worker | Trabalho |
|---|---|
| `OutboxWorker` | Lê a tabela de saída e publica as mensagens na fila. |
| `WorkerEventos` | Processa os eventos que os outros serviços publicaram. |
| `WorkerSlaTicket` | Confere prazo de ticket e avisa antes de estourar. |
| `CampanhaWorker` | Dispara campanha respeitando o ritmo de cada canal. |
| `EmailSyncWorker` e `EmailEnvioWorker` | Sincronizam e enviam e-mail. |
| `WorkerEncerraAtendimento` | Fecha conversa parada conforme a regra da conta. |
| `WorkerTransbordoAtendimento` | Passa a conversa adiante quando ninguém responde. |
| `WorkerVerificaFluxo` | Retoma fluxo de chatbot que ficou esperando. |
| `WorkerRecuperacaoCarrinho` | Chama de volta quem abandonou o carrinho. |
| `WorkerAssistenteAi` | Executa as tarefas do assistente de IA. |
| `WorkerExpurgoDeGravacaoDeVoz` | Apaga gravação antiga conforme a política de retenção. |

### Telefonia, em duas palavras

A voz tem dois caminhos. No modo direto, a operadora entrega a ligação ao
LiveKit, que leva o áudio até o navegador. No modo PBX, a ligação passa antes por
um FreeSWITCH, que cuida de horário, menu de atendimento e fila. O PBX mora em
outro repositório e o backend conversa com ele por HTTP e por fila. Quase tudo
disso está desligado por padrão.

---

## 4. Frontend por dentro

### Onde cada coisa mora no admin

| Pasta | Conteúdo |
|---|---|
| `src/app/` | As rotas. A pasta vira a URL. |
| `src/components/<domínio>/` | As telas de verdade, uma pasta por componente. |
| `src/components/ui/` | Componentes que não puderam subir para o pacote, por dependerem de action, DTO ou do Next. |
| `src/components/data-table/` | A tabela com busca, rolagem infinita e virtualização. Só o admin usa. |
| `src/lib/<domínio>/` | `actions.ts`, `mappers.ts` e `schema.ts` de cada assunto. |
| `src/lib/api/` | Um arquivo por recurso do backend, mais o `server-fetch`. |
| `src/lib/auth/` | Sessão, Keycloak, escolha de conta e controle de acesso por rota. |
| `src/hooks/` | Hooks compartilhados. |
| `src/types/` | Os contratos de tipo, os pares Dto e View. |
| `src/proxy.ts` | A proteção de rota. No Next 16 este arquivo substitui o antigo middleware. |

### Convenções visuais que pegam todo mundo

- Busca em tabela é automática, com atraso de 400 milissegundos. Sem botão de pesquisar.
- Paginação é rolagem infinita. Nunca números de página.
- Formulário em gaveta ou diálogo avisa antes de descartar alteração não salva.
- Telefone, documento, CEP e dinheiro aparecem com máscara. O backend guarda só os dígitos.
- Tudo que é clicável leva `cursor-pointer`, porque o reset do Tailwind não coloca.
- Botão de voltar é sempre o componente `BackButton`, nunca um botão montado na hora.

### Densidade da interface

O usuário escolhe uma densidade entre 85% e 115%. O valor vai para um cookie e
já sai no HTML do servidor, então não existe piscada ao carregar. Espaço e texto
crescem em ritmos diferentes, de propósito. Ao escrever componente novo, prefira
altura mínima a altura fixa em qualquer caixa que embale texto.

---

## 5. Infraestrutura e entrega

### As peças de fora do código

| Peça | Papel |
|---|---|
| PostgreSQL | O banco de dados principal. Uma base para produção e outra para desenvolvimento. |
| Redis | Cache distribuído e ponte do tempo real entre as cópias da API. |
| RabbitMQ | As filas. O servidor de desenvolvimento é compartilhado, por isso existe o modo isolado. |
| Keycloak | O servidor de identidade. Guarda usuário e senha e emite os tokens. |
| Azure Container Registry | Onde as imagens Docker construídas pela pipeline ficam guardadas. |
| nginx | Serve os arquivos estáticos da landing, da status e do widget, com as regras de segurança próprias. |

### Observabilidade

O backend usa OpenTelemetry com Prometheus. Em português simples, o sistema
conta o que acontece. **Log** é o registro escrito de um evento. **Métrica** é um
número contado ao longo do tempo. **Trace** é o caminho completo de uma
requisição entre os serviços. O `StatusMonitor` usa essas informações e o próprio
batimento dos workers para montar a página de status.

---

## 6. As sete armadilhas

Cada uma destas regras existe porque algo quebrou em produção. Elas estão
registradas em `.claude/memoria/` com data e motivo. Decore as sete antes de
encostar no código.

**1. A conexão padrão aponta para produção.**
Subir a Web.Api ou o AppHost com migração pendente aplica a mudança no banco dos
clientes. Use a conexão de desenvolvimento por variável de ambiente.

**2. Entidade nova sem identificador.**
A classe base não gera o identificador sozinha. Duas entidades novas nascem com
o mesmo valor vazio. A primeira grava e a segunda estoura chave duplicada. Chame
`GenerateNewIdentity()` na primeira linha do construtor.

**3. Modelo compilado desatualizado.**
Mudou entidade ou mapeamento, regere o modelo compilado. Sem isso o EF não
enxerga a mudança em produção, mesmo com a migração aplicada.

**4. Comando pesado dentro de migração.**
Migração roda em 30 segundos, em todas as cópias ao mesmo tempo, e segura o
sistema até terminar. Alteração em tabela grande vai para um script numerado em
`Infraestrutura.Data/Scripts/`.

**5. Evento de tempo real registrado pela metade.**
Faltou um dos quatro registros e o worker do hub morre ao subir. O tempo real
inteiro para, não só o evento novo.

**6. Arquivo gerado editado na mão.**
`CompiledModels/`, `SessionModelSnapshot.cs` e `public/libs/wlclient.js` são
gerados. Edição manual some na próxima geração. Mexa na origem.

**7. Convenção de uma aplicação aplicada em outra.**
O que vale no admin não vale na landing, na status nem no widget. A divergência
é escolha registrada, não dívida a corrigir.

---

## 7. Glossário

Toda palavra que aparece nas conversas do time, com a explicação de quem está
começando e o lugar onde ela aparece no repositório.

### Como o código se organiza

| Termo | O que é | Onde aparece |
|---|---|---|
| Repositório | A pasta do projeto com todo o histórico guardado pelo Git. Repositório também é o nome de um padrão do backend, o que confunde no começo. O contexto resolve. | |
| Monorepo | Um repositório só com vários projetos dentro. Permite mudar backend e tela na mesma alteração, e obriga a saber em qual pasta você está. | a raiz do repositório |
| Workspace | O arranjo que faz várias aplicações dividirem uma instalação de dependências só. Declarado na raiz com `apps/*` e `packages/*`. | `package.json` da raiz |
| Pacote | Um pedaço de código publicado com nome e versão, para ser usado por outros projetos. Os pacotes internos usam o prefixo `@novuscrm/`. | `packages/` |
| Dependência | Código de terceiros que o projeto usa. Fica listado no `package.json` e instalado em `node_modules`. | |
| Lockfile | Arquivo de trava. Anota a versão exata de cada dependência para que todo mundo instale a mesma coisa. | `bun.lock`, na raiz |
| Bun | O gerenciador de pacotes e executor de JavaScript do projeto. Faz o papel de `npm` e de `node`. Os outros gerenciadores estão proibidos. | |
| Node | O programa que roda JavaScript fora do navegador. O Bun é uma alternativa mais rápida a ele. | |
| Turborepo | O orquestrador de tarefas do monorepo. Conhece a ordem das dependências e guarda cache do que já foi construído. | `turbo.json` |
| Cache | Resultado guardado para não refazer o trabalho. Aparece no build do Turborepo e também em memória, com o Redis. | |
| Script | Atalho de comando declarado no `package.json`. `bun run dev:admin` executa o que está escrito ali. | |
| Build | O processo que transforma o código-fonte no que vai rodar em produção. Em TypeScript gera JavaScript e HTML. Em C# gera arquivos binários. | |
| Branch | Linha de trabalho paralela. Você cria uma, altera o código e depois junta na principal. Aqui a principal é `master`. | |
| Commit | Uma alteração salva no histórico, com mensagem. O padrão da casa é `tipo(escopo): descrição`. | |
| Pull Request | O pedido de juntar o seu branch na linha principal. Serve de espaço para revisão antes de o código entrar. | |
| Merge e rebase | Duas formas de juntar trabalho. O merge cria um ponto de encontro. O rebase reescreve os seus commits em cima dos novos. | |
| Conflito | Quando duas pessoas mudam a mesma linha. O Git para e pede que alguém decida qual versão fica. | |
| CLAUDE.md | O guia de uma área do repositório. Existe na raiz, no backend e em cada aplicação. As regras de áreas diferentes não se misturam. | `CLAUDE.md` em cada pasta |

### Programação e orientação a objetos

| Termo | O que é | Onde aparece |
|---|---|---|
| Classe | A planta de uma coisa. Descreve quais dados ela guarda e o que sabe fazer. | `backend/Dominio/Tickets/Ticket.cs` |
| Objeto e instância | A coisa construída a partir da classe. Cada ticket aberto é uma instância da classe `Ticket`. | |
| Propriedade | Um dado guardado no objeto. Em C# costuma virar coluna no banco. | |
| Método | Uma função que pertence ao objeto. É o que ele sabe fazer. | |
| Construtor | O método que roda quando o objeto nasce. Aqui é o lugar certo para gerar o identificador da entidade. | |
| Interface | Um contrato. Lista o que precisa existir sem dizer como funciona. Aqui começam com a letra I, como `ITicketRepository`. | |
| Herança | Uma classe aproveitar o que outra já tem. `Ticket` herda de `EntidadeDeUmaConta`, que herda de `EntityBase`. | |
| Abstrato | Classe que existe só para ser herdada. Você não cria um objeto dela direto. | |
| Namespace | O endereço da classe dentro do projeto. Aqui ele espelha a estrutura de pastas. | |
| Nulo | Ausência de valor. O ponto de interrogação em `DateTime?` marca que o campo pode ficar vazio. Esquecer dessa possibilidade é a origem de metade dos erros em tempo de execução. | |
| Assíncrono | Trabalho que demora e não trava o resto do programa enquanto acontece. Escrito com `async` e `await` nas duas linguagens. | |
| Genérico | Código que funciona com vários tipos. É o `<T>` que aparece em funções e classes reaproveitáveis. | |
| LINQ | A forma de consultar listas em C#. Parece com `map` e `filter` do JavaScript, e no EF Core vira SQL. | |
| Injeção de dependência | Você pede a interface no construtor e o sistema entrega a implementação pronta. Evita que cada classe monte as próprias ferramentas. | `backend/Infraestrutura.Ioc/` |
| IoC | Inversão de controle. O nome do princípio por trás da injeção de dependência. Dá nome à pasta `Infraestrutura.Ioc`. | |
| Exceção | Erro lançado no meio da execução. Se ninguém tratar, sobe até derrubar a operação. | |
| Teste automatizado | Código que confere se o outro código funciona. Roda sozinho antes de o trabalho acabar. | `backend/Aplicacao.Tests/` e os `*.test.ts` do admin |
| Mock | Peça falsa usada no teste no lugar da real. Aqui os testes do backend usam repositório falso, então nenhum teste grava no banco. É um ponto cego conhecido. | |

### Frontend

| Termo | O que é | Onde aparece |
|---|---|---|
| React | A biblioteca que desenha a tela. Um componente é uma função que devolve o que aparece. | |
| JSX | A sintaxe que parece HTML dentro do código JavaScript. É assim que o React descreve a tela. | |
| Props | Os dados que um componente recebe de quem o usou. O equivalente ao `@Input` do Angular. | |
| Estado | Dado que o componente guarda e que, ao mudar, redesenha a tela. | |
| Hook | Função que começa com `use` e dá superpoderes ao componente, como guardar estado ou reagir a mudanças. | `apps/admin/src/hooks/` |
| Next.js | O framework em cima do React. Cuida de rota, renderização no servidor e build. Aqui na versão 16, com App Router. | |
| App Router | O sistema de rotas do Next em que a pasta vira a URL. Arquivos com nome reservado, como `page.tsx` e `layout.tsx`, definem o que cada rota faz. | |
| Route group | Pasta entre parênteses, como `(app)`. Organiza as rotas e agrupa layout e tratamento de erro sem entrar na URL. | |
| SSR | Renderização no servidor. O HTML chega pronto ao navegador, o que ajuda no tempo de carregamento e na indexação. | |
| SPA | Aplicação de página única. Todo o desenho acontece no navegador. É o caso da status e do widget. | |
| Componente de servidor | Componente que roda só no servidor. Pode buscar dado direto e nunca vai para o navegador. É o padrão no admin. | |
| `"use client"` | A linha que marca o arquivo como código do navegador. Necessária para hook, evento de clique e API do browser. | |
| Server action | Função marcada com `"use server"` que roda no servidor e pode ser chamada pela tela. É por onde o admin fala com o backend. | `apps/admin/src/lib/<domínio>/actions.ts` |
| Hidratação | O momento em que o HTML vindo do servidor ganha comportamento no navegador. Diferença entre os dois lados gera o erro de hydration. | |
| Proxy | No admin é o arquivo que intercepta a requisição antes da rota, para proteger página e redirecionar quem não está logado. No Next 16 ele substitui o antigo middleware. | `apps/admin/src/proxy.ts` |
| Vite | A ferramenta de build das aplicações sem Next, a status e o widget. Rápida no desenvolvimento e gera arquivos estáticos. | |
| Expo e React Native | A base do aplicativo mobile. React Native desenha com componentes nativos do celular, sem HTML nem CSS. Expo é o conjunto de ferramentas em volta. | |
| Tailwind | A forma de escrever estilo por classes utilitárias, como `flex` e `gap-4`. Aqui na versão 4, sem arquivo de configuração. | |
| Token de design | O nome dado a uma cor, espaçamento ou tamanho. Você usa o nome, não o valor. Assim o tema troca sem mexer em componente. | `packages/ui-core`, `styles/` |
| Design system | O conjunto de componentes base e tokens que todas as telas usam. Aqui são os 64 componentes de `packages/ui`. | |
| Primitive | Componente base do design system, como botão ou campo. Mudar um deles mexe em cinco aplicações de uma vez. | |
| Barrel | Arquivo índice que reexporta tudo de uma pasta. Proibido aqui, porque engorda o resultado final do build. | |
| Biome | O programa que confere estilo e erros no código TypeScript, e também formata. Roda com `bun run lint` e `bun run format`. | |
| Zod | Biblioteca que valida dados em tempo de execução e gera o tipo a partir da regra. Usada nos formulários. | `src/lib/<domínio>/schema.ts` |
| DTO e View | O par de tipos da borda. `Dto` é o espelho exato da API. `View` é a forma que a tela quer. O mapper converte entre os dois. | |
| Mapper | A função que traduz de um formato para outro. Existe no frontend, entre DTO e View, e no backend, entre entidade e DTO. | |
| Debounce | Esperar um instante depois da última tecla antes de agir. É o que evita uma busca por letra digitada. Aqui o padrão é 400 milissegundos. | |
| Rolagem infinita | Carregar mais itens conforme a pessoa desce a lista. É o padrão do projeto, no lugar de números de página. | |
| Virtualização | Desenhar só as linhas visíveis de uma lista grande. É o que mantém a tabela rápida com milhares de registros. | |
| Bundle | O arquivo final de JavaScript entregue ao navegador. No widget o tamanho dele é tratado como requisito, porque roda em site de terceiro. | |

### Backend e .NET

| Termo | O que é | Onde aparece |
|---|---|---|
| .NET | A plataforma da Microsoft onde o C# roda. Aqui na versão 10. | |
| C# | A linguagem do backend. Compilada e de tipagem forte, o que faz o erro aparecer antes de rodar. | |
| Solução e projeto | Projeto é uma pasta com um `.csproj` que vira biblioteca ou programa. Solução é o arquivo `.sln` que junta todos. | `backend/CPlusChat.BackEnd.sln` |
| NuGet | O gerenciador de pacotes do .NET. O equivalente ao Bun do outro lado. | |
| API | A porta de entrada do backend. Recebe chamadas HTTP e devolve dados, em geral em JSON. | |
| REST | O estilo de API usado aqui. Cada recurso tem uma URL e os verbos HTTP dizem a ação. | |
| Endpoint | Um endereço específico da API, como `/atendimentos`. Uma URL mais um verbo. | |
| Verbo HTTP | GET lê, POST cria, PUT e PATCH alteram, DELETE apaga. Aqui boa parte das consultas usa POST, porque o filtro é grande demais para caber na URL. | |
| Status HTTP | O número da resposta. 200 deu certo, 400 o pedido estava errado, 401 não autenticado, 403 sem permissão, 404 não existe, 500 o servidor quebrou. | |
| Controller | A classe que recebe a chamada HTTP e repassa para a camada de aplicação. Não decide regra de negócio. | `backend/Web.Api/Controllers/`, 174 arquivos |
| AppService | A classe que executa o caso de uso. Lê, aplica a regra, grava e devolve DTO. | `backend/Aplicacao/<domínio>/` |
| Repositório | A classe que sabe ler e gravar uma entidade. Isola o resto do código do banco de dados. | |
| Entidade | A classe do domínio que vira tabela. Tem identidade própria, o `Id`. | `backend/Dominio/` |
| DDD | Projeto orientado a domínio. A ideia de separar a regra do negócio da tecnologia que a sustenta. Dá nome às camadas deste backend. | |
| Camada | Um nível da arquitetura com responsabilidade própria. Domínio, aplicação, infraestrutura. As de baixo não conhecem as de cima. | |
| CQRS | Separar o código que lê do código que escreve. Aqui aparece em estilo, não como regra absoluta. | |
| Unit of Work | O padrão que junta várias gravações numa transação só. No EF Core quem faz esse papel é o `DbContext`. | |
| Swagger | A página que documenta a API e deixa testar os endpoints no navegador. O texto dela é lido por gente de fora. | |
| OData | Um jeito padronizado de filtrar e ordenar pela própria URL. Usado em algumas consultas deste backend. | |
| Worker | Programa sem tela que trabalha sozinho, em laço ou ouvindo fila. Todo worker novo vai para `CPlusChat.Workers`. | `backend/CPlusChat.Workers/` |
| Hosted service | O nome técnico de um worker no .NET. Serviço que o programa sobe junto e mantém rodando. | |
| Aspire | A ferramenta que sobe todos os serviços do backend de uma vez na sua máquina, com painel e portas organizadas. | `backend/AppHost/Program.cs` |
| appsettings.json | O arquivo de configuração do serviço. Conexões, chaves e interruptores de funcionalidade. Segredo não entra aqui. | |
| User Secrets | O lugar onde segredos de desenvolvimento ficam, fora do repositório. Configurado por `dotnet user-secrets set`. | |
| Feature flag | Interruptor que liga ou desliga um recurso sem novo deploy. Aqui a maior parte nasce desligada. | |

### Banco de dados

| Termo | O que é | Onde aparece |
|---|---|---|
| PostgreSQL | O banco relacional do projeto. Guarda tudo, de mensagem de chat a configuração de conta. | |
| Tabela, coluna, linha | Tabela é o conjunto, como uma planilha. Coluna é o campo. Linha é um registro. | |
| Chave primária | A coluna que identifica a linha de forma única. Aqui quase sempre é o `Id`, um identificador aleatório. | |
| Chave estrangeira | A coluna que aponta para a linha de outra tabela. Nomeada como `Id` mais o nome da entidade, como `IdContaDeEmail`. | |
| Índice | Estrutura que acelera a busca. Sem índice, o banco varre a tabela inteira. | |
| Constraint | Restrição que o banco cobra sozinho, como não aceitar valor vazio ou repetido. | |
| Transação | Um bloco de operações que acontece por inteiro ou não acontece. Evita gravar metade da coisa. | |
| ORM | A camada que traduz classe em tabela e C# em SQL. Aqui é o Entity Framework Core. | |
| DbContext | A classe do EF que representa a sessão com o banco. Aqui ela se chama `Session`. | |
| Migração | O arquivo que descreve a mudança na estrutura do banco. Criado com `dotnet ef migrations add` e aplicado ao subir a API. | `backend/Infraestrutura.Data/Migrations/` |
| Modelo compilado | Uma cópia pré-gerada do mapeamento, para o serviço subir mais rápido. Precisa ser regerada a cada mudança de entidade, senão o EF ignora a novidade. | `backend/Infraestrutura.Data/CompiledModels/` |
| DDL | Os comandos que mudam a estrutura do banco, como criar tabela ou coluna. DDL pesado em tabela grande não entra em migração. | |
| Exclusão lógica | Marcar a linha como apagada em vez de apagar de verdade. Aqui a coluna se chama `Excluido`. | |
| Multi-tenant | Muitas empresas no mesmo sistema e no mesmo banco, cada uma vendo só o que é seu. A coluna `NumeroConta` e o filtro automático sustentam isso. | `backend/Dominio/EntidadeDeUmaConta.cs` |
| Busca vetorial | Busca por significado em vez de palavra exata. É o que permite a IA achar o artigo certo mesmo com outras palavras. | `backend/Dominio/DadosVetoriais/` |

### Mensageria, cache e tempo real

| Termo | O que é | Onde aparece |
|---|---|---|
| RabbitMQ | O servidor de filas. Um serviço publica a mensagem, outro consome quando puder. | `backend/Infraestrutura/Mensageria/` |
| Fila | A linha de espera das mensagens. Se o consumidor cair, a mensagem continua lá. | |
| Produtor e consumidor | Quem publica e quem lê. O mesmo serviço pode ser os dois, em filas diferentes. | |
| Outbox | A tabela que guarda a mensagem junto com o dado, na mesma transação. Um worker publica depois. Resolve o caso de gravar e falhar ao avisar. | `backend/Dominio/Outbox/` |
| Idempotência | Repetir a mesma operação sem efeito extra. Importante em fila, porque a mesma mensagem pode chegar duas vezes. | |
| Redis | Banco em memória usado como cache e como ponte entre as cópias da API. | |
| FusionCache | A biblioteca que organiza o cache no backend, combinando memória local e Redis. | `backend/Infraestrutura/Cache/` |
| Backplane | O canal que mantém várias cópias do serviço em sincronia. Sem ele, o usuário conectado na cópia 1 não recebe o aviso gerado na cópia 2. | |
| SignalR | A tecnologia de tempo real do .NET. Mantém uma conexão aberta e empurra atualização para a tela. | |
| Hub | O ponto de encontro do SignalR. Aqui existem o `ChatHub`, para o produto, e o `WebChatHub`, para o widget. | `backend/Aplicacao/ChatHub/` |
| WebSocket | O protocolo de conexão aberta nos dois sentidos que o SignalR usa quando está disponível. | |
| Webhook | O contrário de uma chamada de API. Um serviço de fora chama a sua URL quando algo acontece lá. | |

### Infraestrutura, rede e segurança

| Termo | O que é | Onde aparece |
|---|---|---|
| Docker | A ferramenta que empacota o programa com tudo que ele precisa. Resolve a diferença entre as máquinas. | |
| Imagem e contêiner | Imagem é o pacote parado. Contêiner é a imagem rodando. | |
| Dockerfile | A receita da imagem. Cada aplicação tem o seu, e o contexto de build muda de propósito entre eles. | |
| Contexto de build | A pasta que o Docker enxerga ao construir. Nas telas é a raiz, por causa do arquivo de trava. No backend é `backend/`, por causa da solução. | |
| Registry | O lugar onde as imagens ficam guardadas depois do build. Aqui é o Azure Container Registry. | |
| Pipeline | A esteira automática que constrói e publica a cada mudança em `master`. Uma por artefato, com filtro de pasta. | `azure-pipelines.yml` em cada app |
| CI e CD | Integração contínua e entrega contínua. O hábito de juntar o trabalho cedo e publicar por processo automático. | |
| nginx | O servidor que entrega arquivo estático. Serve a landing, a status e o widget, cada um com suas regras de segurança. | |
| Kubernetes | O orquestrador que roda os contêineres em produção, cuida de réplica e reinício. Os segredos do sistema vivem nele. | |
| Health check | Endereço que responde se o serviço está de pé. É o que o orquestrador e o monitor consultam. | |
| OpenTelemetry | O padrão para gerar log, métrica e trace de forma uniforme entre os serviços. | |
| Autenticação | Provar quem você é. | |
| Autorização | Decidir o que você pode fazer depois de provar quem é. São coisas diferentes e se confundem o tempo todo. | |
| Keycloak | O servidor de identidade. Guarda usuário e senha e emite os tokens que o backend confere. | |
| JWT | Token assinado que vai em cada chamada e prova quem você é. Tem prazo de validade. | |
| OAuth e OIDC | Os protocolos de login. OAuth trata de permissão de acesso, OIDC acrescenta a identidade por cima. | |
| PKCE | A proteção extra do login feito direto no navegador, sem segredo guardado. Usada pela página de status. | |
| Cookie | Pedaço de dado que o navegador guarda e reenvia ao servidor. No admin ele carrega a sessão, e dentro de um iframe pode estar bloqueado. | |
| CSP | Política de segurança de conteúdo. Diz ao navegador o que a página pode carregar e executar. Na status e no widget é o controle principal contra script injetado. | |
| CORS | A regra que decide se um site pode chamar a API de outro domínio. | |
| XSS | Ataque em que um texto do usuário vira código executado na página. É por isso que mensagem e nome sempre renderizam como texto puro. | |
| Iframe | Uma página dentro de outra. É como o widget roda no site do cliente, e é a origem de quase toda regra especial dele. | |

### Palavras do produto

| Termo | O que é |
|---|---|
| Conta | A empresa cliente dentro da plataforma. É a unidade de isolamento dos dados. |
| Atendimento | Uma conversa com um contato, do início ao fim. Na tela o módulo passou a se chamar Chat, mas o código mantém `atendimento`, porque é contrato com a API. |
| Ticket | Um chamado de suporte, com prazo, urgência, categoria e histórico. Diferente do atendimento, que é a conversa. |
| SLA | O prazo acordado para responder e resolver. Existe um worker só para vigiar isso. |
| Canal | Por onde a conversa chega. WhatsApp, Instagram, e-mail, webchat, telefone. |
| Fluxo | O chatbot montado em blocos. Existem dois motores de execução, e o estado precisa ser gravado nos dois. |
| Campanha | Disparo de mensagem para uma lista de contatos, com ritmo controlado por canal. |
| Funil e negócio | A parte de CRM. Negócio é a oportunidade de venda, e o funil são as etapas por onde ela passa. |
| White-label | Revenda com marca própria. Por isso nenhum texto escreve "Novus" na mão. O nome vem da configuração da conta. |
| Plugin | Extensão que roda dentro do produto, em iframe. Pode ser hospedada por nós ou pelo próprio cliente. |
| Luma | A assistente de inteligência artificial da plataforma. |
| Backoffice | O painel interno que enxerga todas as contas. Público diferente do admin, regras de segurança diferentes. |

### Processo e ferramentas do time

| Termo | O que é | Onde aparece |
|---|---|---|
| OpenSpec | O jeito de escrever a especificação antes do código. A mudança vira proposta, desenho e lista de tarefas, e no fim é arquivada. | `openspec/changes/` e `openspec/specs/` |
| Skill | Procedimento pronto que um agente de IA segue. `verificar` roda os testes certos, `revisar` faz a revisão, `humanizer-br` acerta o texto. | `.claude/skills/` |
| Hook | Aqui tem dois sentidos. No React é a função que começa com `use`. No harness é a trava automática que barra comando perigoso. | `.claude/hooks/` |
| Harness | O conjunto de agentes, skills, hooks e memória que apoia o trabalho no repositório. | |
| Memória do time | Os registros de decisão e de incidente. Antes de mudar uma regra estranha, procure ali o motivo. | `.claude/memoria/` |
| humanizer-br | A revisão de estilo obrigatória para texto que uma pessoa lê. Frase curta, fato concreto, sem travessão e sem adjetivo vago. | |
| Work item | O cartão de tarefa no Azure DevOps. O número dele entra no nome do branch e no rodapé do commit. | |

---

## 8. Seus primeiros 30 dias

O roteiro acima é o conteúdo. Este aqui é o calendário. Risque conforme for
fazendo.

### Semana 1, ambiente de pé

- [ ] Clonar o repositório e rodar `bun install` na raiz.
- [ ] Instalar o SDK do .NET 10 e o Docker.
- [ ] Copiar o `.env.example` do admin para `.env.local`.
- [ ] Subir `bun run dev:admin` e entrar na tela com autenticação simulada.
- [ ] Compilar o backend com `dotnet build backend/CPlusChat.BackEnd.sln`.
- [ ] Ler o `CLAUDE.md` da raiz inteiro, sem pressa.

### Semana 2, ler código de verdade

- [ ] Seguir uma tela de listagem do admin até o endpoint do backend.
- [ ] Ler `Ticket.cs` e `EntidadeDeUmaConta.cs`.
- [ ] Abrir `backend/AppHost/Program.cs` e listar os serviços que sobem juntos.
- [ ] Ler os nomes dos arquivos em `CPlusChat.Workers/`.
- [ ] Ler o `CLAUDE.md` do backend e o do admin.

### Semana 3, primeira alteração

- [ ] Pegar uma tarefa pequena de tela, de preferência com texto e campo existentes.
- [ ] Rodar a verificação da área antes de dizer que acabou.
- [ ] Passar o texto novo pela revisão de estilo.
- [ ] Abrir o Pull Request e acompanhar a revisão até o fim.

### Semana 4, atravessar as duas pontas

- [ ] Pegar uma tarefa que mexa no backend e na tela.
- [ ] Criar uma coluna nova com migração e modelo compilado regerado, apontando para o banco de desenvolvimento.
- [ ] Acompanhar a pipeline da sua alteração até a imagem publicada.
- [ ] Reler as sete armadilhas e conferir que nenhuma pegou você.

### Como pedir ajuda sem travar

Antes de chamar alguém:

1. Diga o que você queria fazer.
2. Diga o que você tentou e o que aconteceu, com a mensagem de erro inteira.
3. Diga onde você já procurou, em qual `CLAUDE.md` e em qual arquivo.

Esses três itens transformam uma pergunta de 20 minutos numa resposta de dois.
E, na metade das vezes, escrever o terceiro item já resolve.

---

Este mapa foi montado a partir dos guias do próprio repositório, de `CLAUDE.md`,
`.claude/MONOREPO.md` e dos guias de cada aplicação. Quando algo aqui divergir do
guia da pasta, o guia da pasta vence. Ele é a fonte, este documento é a porta de
entrada.
