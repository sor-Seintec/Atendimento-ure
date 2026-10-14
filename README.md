# Atendimentos SEINTEC — URE

Sistema web para cadastro, consulta, edição, exclusão e acompanhamento de chamados da URE.

## Funcionalidades

- Dashboard com total de chamados, chamados abertos, concluídos e registros do mês.
- Cadastro de chamados.
- Edição e exclusão de chamados.
- Alteração de status com duplo clique sobre o status exibido na tabela.
- Filtros por texto, status e período.
- Relatórios por dia, mês e ano.
- Lista de atividades em ordem alfabética.
- Exportação dos chamados para Excel.
- Login por e-mail e senha usando Firebase Authentication.
- Alias de login `admin` para o usuário Firebase configurado.
- Dados armazenados no Cloud Firestore.

## Tecnologias

- HTML, CSS e JavaScript.
- Firebase Authentication.
- Cloud Firestore.
- Firebase Hosting ou GitHub Pages.
- SheetJS para exportação Excel.

## Arquivos principais

| Arquivo | Finalidade |
|---|---|
| `index.html` | Aplicação web completa, interface e integração com Firebase |
| `assets/` | Imagens e arquivos visuais da aplicação |
| `migrate-firestore.mjs` | Migração dos registros do SQLite para o Firestore |
| `firebase-service-account.json` | Chave administrativa local; não deve ser publicada |
| `chamados.sqlite` | Banco local original, mantido apenas como backup |
| `server.mjs` | Servidor local legado; não é usado pela versão online |

## Firebase

O projeto Firebase utilizado é:

```text
atendimentos-ure
```

Os chamados ficam na coleção:

```text
calls
```

Cada documento possui esta estrutura:

```json
{
  "id": "base-862",
  "date": "2026-10-01",
  "school": "Nome da escola",
  "ure": "SEINTEC",
  "activity": "Atividade realizada",
  "quantity": 1,
  "request": "Descrição da solicitação",
  "action": "Providências tomadas",
  "status": "Concluído"
}
```

## Autenticação

O Firebase Authentication utiliza o provedor **E-mail/senha**.

O usuário pode entrar usando:

```text
admin
```

ou o e-mail cadastrado no Firebase. O alias `admin` é convertido internamente para o e-mail configurado no código e não cria um segundo usuário.

## Regras recomendadas do Firestore

No Firebase Console, em **Firestore Database > Rules**, utilize regras que exijam autenticação:

```javascript
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {
    match /calls/{callId} {
      allow read, write: if request.auth != null;
    }
  }
}
```

## Migração do SQLite

A migração original foi feita do arquivo `chamados.sqlite` para a coleção `calls` do Firestore.

Para executar novamente a migração em ambiente autorizado:

```powershell
pnpm install
node migrate-firestore.mjs
```

O arquivo `firebase-service-account.json` precisa estar na raiz do projeto. Esse arquivo é secreto e não deve ser enviado ao GitHub ou compartilhado.

## Publicação no GitHub Pages

O sistema é uma aplicação estática e pode ser publicado no GitHub Pages.

Publique:

```text
index.html
assets/
```

Não publique:

```text
firebase-service-account.json
chamados.sqlite
server.mjs
node_modules/
```

No GitHub:

1. Crie um repositório.
2. Envie `index.html` e `assets/` para a branch `main`.
3. Acesse **Settings > Pages**.
4. Selecione **Deploy from a branch**.
5. Escolha a branch `main` e a pasta `/root`.
6. Salve e aguarde a publicação.

Depois, no Firebase Console, adicione o domínio do GitHub Pages em:

```text
Authentication > Settings > Authorized domains
```

Exemplo:

```text
seu-usuario.github.io
```

## Desenvolvimento local

Como a aplicação usa Firebase, ela deve ser aberta por um servidor HTTP local ou pelo GitHub Pages. Não é recomendado abrir o arquivo diretamente com `file://`.

Uma opção simples é usar um servidor estático local, como o Live Server do Visual Studio Code.

O `server.mjs` e o SQLite permanecem no projeto somente como referência e backup da versão anterior.

## Segurança

- Nunca publique `firebase-service-account.json`.
- Não coloque senhas diretamente no código.
- Mantenha as regras do Firestore exigindo `request.auth != null`.
- Crie usuários pelo Firebase Authentication.
- Faça backup periódico dos dados do Firestore.
