# Guia — publicar o app e conectar ao Google Drive

Tempo total: cerca de 30 minutos, uma única vez. Tudo é gratuito.
Os nomes dos menus estão em português; entre parênteses, o nome em inglês, caso sua tela esteja em inglês.

> **Por que publicar?** O login do Google só funciona com o app num endereço https. Por isso esta etapa
> também publica o app no GitHub Pages, que é o que permite instalá-lo no celular.
> No GitHub fica **só o código** do app. **Seus dados ficam apenas no seu Google Drive.**

---

## Parte A — Publicar o app no GitHub Pages (10 min)

1. Crie uma conta em **github.com** (se ainda não tiver). Anote seu nome de usuário: ele vira o endereço do app.
2. Clique em **+ → Novo repositório** (*New repository*).
   - Nome: `minha-rotina`
   - Visibilidade: **Público** (*Public*). O GitHub Pages gratuito exige repositório público.
   - Clique em **Criar repositório** (*Create repository*).
3. Na página do repositório, clique em **enviar um arquivo existente** (*uploading an existing file*).
   Descompacte o zip e arraste **todo o conteúdo** da pasta: `index.html`, `config.js`, `manifest.webmanifest`, `sw.js`, a pasta `icons` e os arquivos `.md`.
   Clique em **Confirmar alterações** (*Commit changes*).
4. Vá em **Configurações → Pages** (*Settings → Pages*).
   Em **Origem** (*Source*), escolha **Implantar de um branch** (*Deploy from a branch*), branch **main**, pasta **/ (root)**, e salve.
5. Espere 1 ou 2 minutos. O app estará em:
   **`https://SEU-USUARIO.github.io/minha-rotina/`**
   Abra esse endereço para conferir. O Google ainda não vai funcionar, o que é esperado.

---

## Parte B — Configurar o Google Cloud (15 min)

6. Acesse **console.cloud.google.com/projectcreate** e crie o projeto **Minha Rotina**.
   Na página inicial do projeto, anote o **Número do projeto** (*Project number*, só dígitos). Esse é o **googleAppId**.

7. Ative duas APIs em **APIs e serviços → Biblioteca** (*APIs & Services → Library*):
   - **Google Drive API** → Ativar
   - **Google Picker API** → Ativar

8. Abra **Google Auth Platform** (console.cloud.google.com/auth/overview) e clique em **Primeiros passos** (*Get started*):
   - Nome do app: `Minha Rotina Diária`; e-mail de suporte: o seu
   - Público (*Audience*): **Externo** (*External*)
   - Informações de contato: o seu e-mail → aceite os termos → **Criar**

9. Ainda na Google Auth Platform:
   - **Acesso a dados** (*Data Access*) → **Adicionar ou remover escopos** → procure e marque
     `.../auth/drive.file` ("ver, editar, criar e excluir apenas os arquivos do Google Drive que você usa com este app") → **Atualizar** → **Salvar**.
     Esse é o único acesso que o app pede: ele **não enxerga** nenhum outro arquivo do seu Drive.
   - **Público-alvo** (*Audience*) → **Publicar app** (*Publish app*) → confirmar.
     Como o app usa só esse escopo restrito, não precisa de verificação do Google.
     *Alternativa:* deixar em **Teste** e adicionar você e cada leitor em **Usuários de teste** (*Test users*). Fora dessa lista, ninguém consegue entrar.

10. **Clientes** (*Clients*) → **Criar cliente** (*Create client*):
    - Tipo de aplicativo: **Aplicativo da Web** (*Web application*)
    - Nome: `Rotina web`
    - **Origens JavaScript autorizadas** (*Authorized JavaScript origins*) → Adicionar URI:
      `https://SEU-USUARIO.github.io` ← **sem** `/minha-rotina` e **sem** barra no final
    - URIs de redirecionamento: deixe em branco
    - **Criar** → copie o **ID do cliente** (termina em `.apps.googleusercontent.com`). Esse é o **googleClientId**.
      A "chave secreta do cliente" **não** é usada. Nunca a coloque no app.

11. **Chave de API**, usada pelo modo consulta: **APIs e serviços → Credenciais → Criar credenciais → Chave de API**.
    Em seguida, clique na chave para editar:
    - Restrições de aplicativo: **Referenciadores HTTP** → adicione `https://SEU-USUARIO.github.io/*`
    - Restrições de API: **Restringir chave** → marque **Google Picker API**
    - **Salvar** → copie a chave (começa com `AIza`). Essa é a **googleApiKey**.

---

## Parte C — Ligar o app ao Google (5 min)

12. No GitHub, abra o arquivo **config.js** → ícone de lápis (*Edit*) → preencha:
    ```js
    window.ROTINA_CONFIG = {
      googleClientId: "123456789012-xxxx.apps.googleusercontent.com",
      googleApiKey:   "AIzaSy....",
      googleAppId:    "123456789012"
    };
    ```
    **Confirmar alterações** (*Commit changes*). Espere 1 minuto.

13. **Traga seus dados atuais.** O app publicado é um endereço novo e começa vazio.
    - No `index.html` que você usava no computador: **Dados → Salvar backup (.json)**.
    - No app publicado: **Dados → Importar backup**.
      Esse caminho também aceita o backup do HTML antigo.

14. No app publicado: **Dados → Conectar ao Google Drive** → escolha sua conta → **Permitir**.
    Pronto: o selo **☁ Salvo no Drive** aparece no topo. No seu Drive surge a pasta **Minha Rotina** com:
    - `rotina.json`: os dados, atualizados segundos depois de cada marcação;
    - `Backups/`: uma cópia por dia (mantém os últimos 30 dias e a primeira cópia de cada mês).

---

## Parte D — Instalar no celular e no tablet

- **Android (Chrome):** abra o endereço → menu ⋮ → **Instalar app** (ou "Adicionar à tela inicial").
- **iPhone/iPad (Safari):** abra o endereço → **Compartilhar** → **Adicionar à Tela de Início**.
- Em cada aparelho: **Dados → Conectar ao Google Drive** com a **mesma conta**. As marcações já feitas aparecem sozinhas.

---

## Parte E — Compartilhar para consulta

15. No app: **Dados → Compartilhar para consulta** → digite o e-mail Google da pessoa → **Adicionar leitor**.
    O Google envia um e-mail para ela com o link de consulta. Você também pode copiar o link e mandar pelo WhatsApp.
16. A pessoa abre o link → **Entrar com Google** → na primeira vez, o Google mostra o arquivo `rotina.json` já selecionado → **Selecionar**.
    Ela passa a ver tudo (Hoje, Mês, Painel) em **somente leitura**. O botão **↻ Atualizar** traz as marcações mais recentes.
17. Para tirar o acesso: **Remover** ao lado do nome da pessoa.

---

## Problemas comuns

| O que aparece | O que fazer |
|---|---|
| "Erro 400: origin_mismatch" | Confira o passo 10: a origem precisa ser exatamente `https://SEU-USUARIO.github.io` |
| A janela do Google não abre | Permita pop-ups para o endereço do app |
| Selo **☁ Reconectar** | O login do Google dura cerca de 1 hora. Toque em Reconectar (1 toque). Nada se perde enquanto isso: as marcações ficam no aparelho e sobem depois |
| Leitor vê "ainda não tem acesso" | Confira se o e-mail dele está na lista de leitores. Se o app estiver em **Teste** (passo 9), adicione-o em Usuários de teste |
| Leitor vê "falta a chave de API" | Preencha `googleApiKey` e `googleAppId` no config.js (passos 6 e 11) |

**Atualizar o app no futuro:** substitua só o `index.html` (e o `sw.js`, quando vier novo) no GitHub. O `config.js` continua o mesmo.
