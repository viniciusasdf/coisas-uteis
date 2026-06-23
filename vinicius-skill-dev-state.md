---
name: dev-state
description: Continuidade de desenvolvimento entre sessões e máquinas. Use para retomar, consultar estado, registrar checkpoint, salvar estado, preparar handoff, sugerir mensagem de commit ou mostrar ajuda.
---

# Objetivo

Manter `docs/dev/state.md` como a fonte mínima de continuidade operacional do projeto, sem duplicar documentação técnica, regras de negócio, especificações formais ou histórico completo de conversas.

A skill deve funcionar de forma plug-and-play no repositório atual. Antes de executar comandos operacionais, deve verificar se a estrutura mínima do fluxo existe e inicializar automaticamente o que estiver faltando, desde que isso possa ser feito de forma segura, leve e idempotente.

O arquivo de estado deve registrar o estado técnico e produtivo da tarefa. Ele não deve registrar pendências genéricas de Git, como commit, push, `git add` ou revisão de diff, quando essas etapas fizerem parte do fluxo normal após `salvar estado`.

---

# Arquivos do fluxo

Arquivo de estado do projeto:

```text
docs/dev/state.md
```

Arquivo de instruções persistentes do projeto:

```text
AGENTS.md
```

---

# Comandos reconhecidos

Use esta skill quando o usuário disser:

- `retomar`
- `estado`
- `checkpoint`
- `salvar estado`
- `salvar estado: ...`
- `ajuda`
- `ajuda dev-state`
- `dev-state ajuda`


Também use esta skill quando o usuário pedir para:

- registrar progresso;
- preparar retomada;
- fazer handoff entre máquinas;
- atualizar o estado de desenvolvimento;
- continuar trabalho iniciado em outro computador ou workspace;
- usar o estado salvo como referência para a mensagem de commit.

---

# Ordem de execução

1. Se o comando for de ajuda, responder apenas com a ajuda rápida e encerrar.
2. Para qualquer outro comando reconhecido, executar bootstrap leve, se necessário.
3. Executar o comportamento específico do comando.
4. Nunca executar ações fora do escopo sem pedido explícito do usuário.

---

# Ajuda da skill

Quando o usuário pedir ajuda usando `ajuda`, `ajuda dev-state`, `dev-state ajuda`, `$dev-state ajuda`, `$dev-state -h` ou `$dev-state --help`, responder apenas com a ajuda rápida da skill.

Não executar bootstrap.

Não criar arquivos.

Não alterar `docs/dev/state.md`.

Não alterar `AGENTS.md`.

Não executar comandos.

Resposta sugerida:

```text
dev-state — continuidade de desenvolvimento

Comandos:
  retomar                Mostra de onde continuar.
  estado                 Mostra o estado atual sem atualizar progresso.
  checkpoint             Registra avanço intermediário.
  salvar estado          Salva o estado técnico e sugere mensagem de commit.
  salvar estado: ...     Salva o estado usando sua observação como prioridade.

Primeira execução em um projeto:
  $dev-state retomar

Arquivos usados:
  docs/dev/state.md      Estado mínimo do projeto.
  AGENTS.md              Comandos curtos do fluxo.

Uso comum:
  git pull
  retomar

  salvar estado
  git add <arquivos>
  git commit -m "<mensagem sugerida>"
  git push
```

---

# Bootstrap leve e idempotente

Antes de executar comandos operacionais, verificar apenas:

1. Se `docs/dev/state.md` existe.
2. Se `AGENTS.md` existe.
3. Se `AGENTS.md` contém os marcadores `<!-- dev-state:start -->` e `<!-- dev-state:end -->`.
4. Se `docs/dev/state.md` contém o marcador `<!-- dev-state:v1 -->`.

O bootstrap deve ser idempotente. Executar a skill várias vezes não deve duplicar seções, sobrescrever conteúdo útil ou gerar alterações desnecessárias.

Não fazer:

- varredura ampla do repositório;
- leitura automática de toda a documentação;
- análise automática de `git diff`;
- listagem automática de todos os arquivos modificados;
- inspeção de dependências;
- execução automática de testes;
- alteração de código-fonte da aplicação;
- alteração automática de `.gitignore`;
- alteração de `package.json`, scripts ou arquivos de configuração do projeto sem pedido explícito.

---

## Template padrão de `docs/dev/state.md`

Se `docs/dev/state.md` não existir, criar o arquivo com o seguinte conteúdo:

```md
<!-- dev-state:v1 -->

# Estado de desenvolvimento

## Tarefa ativa

Nenhuma tarefa ativa registrada.

## Branch atual

Não informado.

## Último estado conhecido

Não informado.

## Próximo passo

Não informado.

## Decisões recentes

- Nenhuma decisão recente registrada.

## Arquivos relevantes

- Nenhum arquivo relevante registrado.

## Comandos úteis

- Nenhum comando registrado.

## Observações para retomada

Não informado.
```

---

## Estado existente sem marcador

Se `docs/dev/state.md` existir, mas não contiver `<!-- dev-state:v1 -->`, avaliar se o arquivo é compatível com a estrutura esperada.

Se for compatível, adicionar no topo:

```md
<!-- dev-state:v1 -->
```

Se não for compatível, preservar o conteúdo existente e informar que revisão manual é recomendada.

Não sobrescrever conteúdo útil.

---

## Template padrão da seção `dev-state` no `AGENTS.md`

Se `AGENTS.md` não existir, criar o arquivo com a seção abaixo.

Se `AGENTS.md` existir, mas não tiver a seção `dev-state`, adicionar a seção ao final do arquivo.

Não duplicar a seção se os marcadores já existirem.

```md
<!-- dev-state:start -->
## Continuidade de desenvolvimento

Use `docs/dev/state.md` como fonte mínima de continuidade operacional entre sessões e máquinas.

Comandos curtos reconhecidos:

- `retomar`: ler `docs/dev/state.md` e indicar objetivamente de onde continuar.
- `estado`: consultar `docs/dev/state.md` sem alterar arquivos, salvo bootstrap inicial necessário.
- `checkpoint`: atualizar `docs/dev/state.md` com um registro intermediário curto.
- `salvar estado`: atualizar `docs/dev/state.md` ao encerrar ou pausar uma tarefa, sem registrar pendências genéricas de Git.
- `salvar estado: ...`: atualizar `docs/dev/state.md` usando a observação do usuário como orientação principal.
- `ajuda dev-state`: mostrar a ajuda rápida da skill sem alterar arquivos.

Ao atualizar o estado, seja minimalista. Não duplique documentação técnica, não registre histórico completo e não inclua detalhes triviais.
<!-- dev-state:end -->
```

---

# Comportamento dos comandos

## `retomar`

Use quando o usuário quiser continuar o trabalho em uma nova sessão, máquina ou workspace.

Comportamento:

1. Executar bootstrap leve, se necessário.
2. Ler `docs/dev/state.md`.
3. Responder objetivamente:
   - tarefa ativa;
   - branch atual;
   - último estado conhecido;
   - próximo passo recomendado;
   - arquivos relevantes.
4. Não atualizar progresso, salvo criação inicial dos arquivos ausentes.
5. Não inventar estado ausente.

---

## `estado`

Use quando o usuário quiser apenas consultar o estado atual.

Comportamento:

1. Executar bootstrap leve, se necessário.
2. Ler `docs/dev/state.md`.
3. Resumir o estado atual.
4. Não atualizar progresso, salvo criação inicial dos arquivos ausentes.

---

## `checkpoint`

Use para registrar um ponto intermediário durante uma tarefa em andamento.

Comportamento:

1. Executar bootstrap leve, se necessário.
2. Atualizar `docs/dev/state.md`.
3. Manter a tarefa ativa.
4. Registrar apenas progresso relevante desde a última atualização.
5. Atualizar o próximo passo, se necessário.
6. Remover informações obsoletas, se houver.
7. Não transformar o arquivo em changelog.
8. Não registrar pendências genéricas de Git, commit, push ou revisão de diff.

---

## `salvar estado`

Use quando o usuário for pausar, encerrar a sessão ou trocar de máquina.

Comportamento:

1. Executar bootstrap leve, se necessário.
2. Atualizar `docs/dev/state.md`.
3. Registrar:
   - tarefa ativa;
   - branch atual;
   - último estado técnico conhecido;
   - próximo passo técnico ou funcional;
   - decisões recentes relevantes;
   - arquivos relevantes para retomada;
   - comandos úteis de validação, se houver;
   - observações para retomada.
4. Remover informações obsoletas.
5. Manter o arquivo curto e operacional.
6. Não registrar pendências genéricas de Git, commit, push ou revisão de diff.
7. Não registrar que há commit pendente apenas porque o usuário ainda vai commitar após salvar o estado.
8. Não registrar que há revisão pendente apenas porque existem alterações ainda não commitadas.
9. Ao responder ao usuário, sugerir uma mensagem de commit baseada no estado salvo, quando houver informação suficiente.
10. Não escrever a sugestão de commit dentro de `docs/dev/state.md`.
11. Não executar `git add`, `git commit`, `git push`, merge, rebase ou alteração de branch sem pedido explícito.

Resposta recomendada após salvar:

```text
Estado salvo.

Sugestão de commit:
<mensagem sugerida>

Próximo passo técnico registrado:
<próximo passo>
```

---

## `salvar estado: ...`

Use quando o usuário informar uma orientação explícita junto ao comando.

Comportamento:

1. Executar bootstrap leve, se necessário.
2. Usar a observação do usuário como prioridade.
3. Atualizar `docs/dev/state.md` de acordo com a observação.
4. Preservar informações ainda relevantes.
5. Remover informações obsoletas.
6. Não registrar detalhes desnecessários.
7. Não registrar pendências genéricas de Git, commit, push ou revisão de diff.
8. Se a observação do usuário mencionar commit, tratar isso como contexto para a resposta, não como pendência a ser registrada no estado, salvo se o usuário pedir expressamente.
9. Ao responder ao usuário, sugerir uma mensagem de commit baseada no estado salvo e na observação explícita.
10. Não escrever a sugestão de commit dentro de `docs/dev/state.md`.

---

# Estado operacional versus fluxo Git

`docs/dev/state.md` deve registrar apenas o estado técnico e produtivo da tarefa.

Não registrar como pendência:

- commit pendente;
- revisão pendente genérica;
- `git add` pendente;
- `git commit` pendente;
- `git push` pendente;
- arquivos modificados apenas aguardando commit;
- necessidade genérica de revisar o diff antes do commit.

Essas etapas fazem parte do fluxo operacional normal após `salvar estado` e não devem ser tratadas como pendências de desenvolvimento.

O próximo passo registrado no `state.md` deve representar o próximo trabalho técnico ou funcional a ser feito depois da retomada, não o próximo comando Git.

Só registrar revisão pendente quando houver uma revisão técnica substantiva explicitamente indicada pelo usuário ou inerente à tarefa, por exemplo:

- revisar regra de negócio específica;
- validar comportamento com teste específico;
- revisar decisão arquitetural;
- conferir impacto em módulo crítico.

Não registrar revisão pendente apenas porque existem alterações ainda não commitadas.

---

# Estrutura obrigatória do `state.md`

Manter sempre esta estrutura:

```md
<!-- dev-state:v1 -->

# Estado de desenvolvimento

## Tarefa ativa

...

## Branch atual

...

## Último estado conhecido

...

## Próximo passo

...

## Decisões recentes

...

## Arquivos relevantes

...

## Comandos úteis

...

## Observações para retomada

...
```

---

# Regras de atualização do estado

Ao atualizar `docs/dev/state.md`:

1. Identificar a tarefa principal em andamento.
2. Identificar a branch atual com `git branch --show-current`, somente quando for necessário atualizar o estado.
3. Resumir o progresso real da sessão.
4. Registrar o próximo passo técnico ou funcional.
5. Registrar apenas decisões recentes que afetem implementação futura.
6. Listar apenas arquivos realmente relevantes para retomar o trabalho.
7. Incluir comandos úteis somente se forem necessários para validação, build, teste ou execução local.
8. Remover pendências já concluídas.
9. Preservar informações ainda úteis.
10. Manter o arquivo curto, objetivo e operacional.
11. Não registrar pendências genéricas de Git como parte do estado.

---

# Regras para sugestão de commit

Quando `salvar estado` ou `salvar estado: ...` for executado, sugerir uma mensagem de commit na resposta final, se houver informação suficiente.

A sugestão deve ser curta, objetiva e baseada no estado salvo.

Preferir o formato convencional quando possível:

```text
tipo: descrição curta
```

Exemplos:

```text
feat: implementa continuidade mínima com dev-state
```

```text
docs: atualiza estado de desenvolvimento
```

```text
refactor: ajusta fluxo de recuperação por similaridade
```

Não escrever a sugestão de commit dentro de `docs/dev/state.md`.

Não executar commit sem pedido explícito.

---

# Regras de otimização

A skill deve evitar trabalho desnecessário.

Não fazer:

- varredura geral do repositório;
- leitura automática de toda a documentação;
- análise automática de `git diff`;
- listagem automática de todos os arquivos modificados;
- inspeção de dependências;
- execução automática de testes;
- alteração de arquivos da aplicação;
- alteração automática de `.gitignore`;
- alteração de scripts ou configurações do projeto sem pedido explícito.

A skill só deve fazer ações adicionais quando o usuário pedir explicitamente.

---

# Regras de estilo

Use linguagem objetiva e operacional.

Prefira:

```md
- Implementada a agregação inicial por processo.
- Falta validar ordenação final do ranking.
- Próximo passo: criar testes para casos com múltiplos chunks do mesmo processo.
```

Evite:

```md
Hoje discutimos longamente sobre várias alternativas possíveis para implementar a fase, considerando diversos pontos de vista...
```

---

# Restrições

Não use `docs/dev/state.md` para explicar arquitetura em profundidade.

Não registre histórico completo de commits.

Não registre discussões longas.

Não invente progresso. Se algo não foi concluído, marque como pendente.

Não registre segredos, tokens, senhas, credenciais, dados pessoais desnecessários ou informações sensíveis.

Não sincronize nem versione diretórios internos do Codex.

Não altere código da aplicação como parte do fluxo de estado.

Não execute comandos destrutivos.

Não faça commit, push, merge, rebase ou alteração de branch sem pedido explícito.