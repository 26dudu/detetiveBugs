# LAUDO – Operação Resgate: Detetives de Bugs

Projeto: Central de Achados (Flutter + Firebase)

---

### Bug 1
- **Arquivo:** `lib/main.dart`
- **Sintoma:** o app não compilava (erro no import `firebase_options.dart`).
- **Causa:** o arquivo `firebase_options.dart` não vem no projeto, ele precisa ser gerado.
- **Correção:** rodei `flutterfire configure` e escolhi o projeto Firebase. Também deixei o import como `import 'firebase_options.dart';`.
- **Teste:** o erro sumiu e o app abriu no Chrome com `flutter run -d chrome`.

### Bug 2
- **Arquivo:** `lib/pages/central_page.dart`
- **Sintoma:** erro de sintaxe no texto do subtítulo.
- **Causa:** a string estava quebrada em duas linhas.
- **Correção:** coloquei `\n` dentro da string.
- **Teste:** o analisador não mostrou mais erro e a lista mostra o local e o nome do responsável em duas linhas.

### Bug 3
- **Arquivo:** `lib/pages/central_page.dart`
- **Sintoma:** erro ao abrir os detalhes (faltava parâmetro).
- **Causa:** `DetalhePage()` era chamada sem passar a ocorrência.
- **Correção:** `DetalhePage(ocorrencia: o)`.
- **Teste:** cliquei em uma ocorrência e os dados dela apareceram na tela de detalhes.

### Bug 4
- **Arquivo:** `lib/models/ocorrencia.dart`
- **Sintoma:** o botão de marcar como resolvido não aparecia para o dono.
- **Causa:** o `autorId` estava lendo o campo `autorNome`.
- **Correção:** `autorId: d['autorId']`.
- **Teste:** na conta que criou a ocorrência o botão apareceu.

### Bug 5
- **Arquivo:** `lib/models/ocorrencia.dart`
- **Sintoma:** itens em aberto apareciam como resolvidos e o contrário.
- **Causa:** tinha um `!` invertendo o valor de `resolvido`.
- **Correção:** tirei o `!`.
- **Teste:** um item novo aparece "Em aberto" e, depois de resolver, aparece "Resolvido".

### Bug 6
- **Arquivo:** `lib/services/central_service.dart` (`registrar`)
- **Sintoma:** o dono da ocorrência ficava errado e ninguém conseguia alterar o status.
- **Causa:** salvava o e-mail em `autorId`, mas a conferência usa o `uid`.
- **Correção:** `'autorId': u.uid`.
- **Teste:** no Firestore o campo `autorId` é o uid do usuário e o dono consegue alterar.

### Bug 7
- **Arquivo:** `lib/services/central_service.dart` (`alterarStatus`)
- **Sintoma:** clicar em "Marcar como resolvido" não mudava nada.
- **Causa:** gravava o mesmo valor que já estava.
- **Correção:** `{'resolvido': !item.resolvido}`.
- **Teste:** cliquei no botão e o status mudou. Clicando em "Reabrir" volta ao normal.

### Bug 8
- **Arquivo:** `lib/services/central_service.dart` (`enviarPista`)
- **Sintoma:** as pistas não apareciam na ocorrência certa.
- **Causa:** o caminho usava `.doc('pistas')` fixo em vez do id da ocorrência.
- **Correção:** `.doc(ocorrenciaId).collection('pistas')`.
- **Teste:** enviei uma pista em uma ocorrência e ela só apareceu nela.

### Bug 9
- **Arquivo:** `lib/pages/detalhe_page.dart`
- **Sintoma:** ao digitar uma pista e enviar, nada acontecia.
- **Causa:** a condição estava ao contrário (`isNotEmpty` com `return`).
- **Correção:** `if (pista.text.trim().isEmpty) return;`
- **Teste:** com texto a pista é enviada e com o campo vazio não.

### Bug 10
- **Arquivo:** `lib/pages/detalhe_page.dart`
- **Sintoma:** as pistas apareciam sem mensagem.
- **Causa:** enviava uma string vazia `''`.
- **Correção:** passei o texto digitado (`pista.text`).
- **Teste:** a pista digitada aparece na lista com o nome de quem enviou.

### Bug 11
- **Arquivo:** `lib/pages/central_page.dart` (`cadastrar`)
- **Sintoma:** dava para registrar com título ou local vazio.
- **Causa:** a validação usava `&&` em vez de `||`.
- **Correção:** troquei por `||`.
- **Teste:** deixei o local vazio e apareceu "Informe título e local."

### Bug 12
- **Arquivo:** `lib/pages/central_page.dart` (switch)
- **Sintoma:** o switch "Somente não resolvidos" funcionava ao contrário.
- **Causa:** o `onChanged` salvava `!value`.
- **Correção:** `somenteAbertos = value`.
- **Teste:** ligar o switch liga o filtro e desligar mostra todos.

### Bug 13
- **Arquivo:** `lib/pages/central_page.dart` (filtro)
- **Sintoma:** com o filtro ligado só apareciam os resolvidos.
- **Causa:** o filtro era `where((o) => o.resolvido)`.
- **Correção:** `where((o) => !o.resolvido)`.
- **Teste:** com o filtro ligado só aparecem itens "Em aberto".

### Bug 14
- **Arquivo:** `lib/services/central_service.dart` (`acompanhar`)
- **Sintoma:** os registros mais novos ficavam no final.
- **Causa:** `descending: false`.
- **Correção:** `descending: true`.
- **Teste:** registrei dois objetos e o último aparece no topo.

### Bug 15
- **Arquivo:** `test/widget_test.dart`
- **Sintoma:** erro no teste (`MyApp` não existe).
- **Causa:** era o teste padrão do Flutter e o app se chama `CentralApp`.
- **Correção:** troquei por um teste simples que compila.
- **Teste:** `flutter test` passou.

---

## Regras de segurança (Firestore)
- **Arquivo:** `firestore.rules`
- **Problema:** sem regras, qualquer pessoa poderia mudar o status de uma ocorrência de outra.
- **Correção:** só usuário logado lê e cria; só o dono altera. As regras não foram desativadas.
- **Teste:** na conta B não aparece o botão para resolver a ocorrência da conta A.

## Teste com duas contas
1. Conta A (janela normal) e conta B (janela anônima) logadas com contas Google diferentes.
2. A conta A registrou uma ocorrência e ela apareceu sozinha na conta B.
3. A conta B enviou uma pista e ela apareceu na ocorrência certa nas duas contas.
4. Só a conta A viu o botão para marcar como resolvido.


