# Como configurar a sincronização em nuvem

Esta versão do Livro-Caixa usa o **Firebase** (do Google) para login e para guardar
os lançamentos na nuvem, sincronizando entre celular, computador etc.
O plano gratuito (Spark) é mais do que suficiente para uso pessoal.

## Passo 1 — Criar o projeto
1. Acesse https://console.firebase.google.com
2. Clique em **Adicionar projeto**, dê um nome (ex: "livro-caixa") e finalize a criação.

## Passo 2 — Ativar o login por e-mail/senha
1. No menu lateral, vá em **Build > Authentication**.
2. Clique em **Get started**.
3. Na aba **Sign-in method**, ative o provedor **E-mail/senha**.

## Passo 3 — Criar o banco de dados
1. No menu lateral, vá em **Build > Firestore Database**.
2. Clique em **Criar banco de dados**.
3. Escolha **modo de produção** e a região mais próxima de você (ex: `southamerica-east1`).

## Passo 4 — Configurar as regras de segurança
Em **Firestore Database > Regras**, substitua o conteúdo por:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /usuarios/{userId}/transacoes/{transacaoId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

Isso garante que cada pessoa só acessa os próprios lançamentos. Clique em **Publicar**.

## Passo 5 — Pegar as chaves de configuração
1. Clique na engrenagem (⚙) ao lado de **Visão geral do projeto > Configurações do projeto**.
2. Em **Seus apps**, clique no ícone **</>** (Web) para criar um app web.
3. Dê um apelido (ex: "livro-caixa-web") e clique em **Registrar app**.
4. Copie o objeto `firebaseConfig` que aparece.

## Passo 6 — Colar no código
Abra o arquivo `index.html`, procure por `firebaseConfig` perto do início do
`<script>` e substitua pelos valores que você copiou:

```js
const firebaseConfig = {
  apiKey: "...",
  authDomain: "...",
  projectId: "...",
  storageBucket: "...",
  messagingSenderId: "...",
  appId: "..."
};
```

## Passo 7 — Publicar
Hospede a pasta em um serviço com HTTPS (necessário para login funcionar):
- **Netlify** (netlify.com) — arraste a pasta e pronto
- **Vercel** (vercel.com)
- **Firebase Hosting** (já que você já tem o projeto criado: `firebase deploy`)

Depois disso, abra o link no celular e use "Adicionar à tela inicial" para
instalar como app.

## Dúvidas comuns
- **"Firebase não configurado ainda"**: você esqueceu de colar o `firebaseConfig`.
- **Login funciona no computador mas não sincroniza no celular**: confirme que
  está usando o mesmo e-mail/senha nos dois aparelhos.
- **Quero migrar dados da versão local (sem login)**: não há migração automática;
  os lançamentos antigos ficaram salvos no navegador do aparelho antigo. Se
  precisar, você pode registrá-los manualmente de novo já logado.
