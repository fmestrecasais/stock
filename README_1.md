# Armazém FMC

Controlo de stock da Fundação Mestre Casais. Site estático (GitHub Pages) + Firebase (Authentication + Firestore). Sem passo de compilação: são ficheiros HTML que carregam o Firebase por CDN.

## Ficheiros

- `index.html` — a aplicação (inventário, prateleiras, pedidos, contagens, folhas A4, utilizadores).
- `firestore.rules` — regras de segurança. Colar no Firebase (Firestore → Regras) e publicar.
- `seed.html` — importação única dos dados iniciais. Apagar depois de usar.
- `data-export/` — cópia dos dados exportados do dashboard antigo (backup).

## Perfis

| Perfil | Pode |
|---|---|
| **owner** (dono) | Tudo o que o admin faz **+** registar contagens físicas. |
| **admin** | Produtos, prateleiras, entradas/saídas/transferências, aprovar pedidos, gerir utilizadores, folhas A4. |
| **requisitante** | Ver o stock e fazer pedidos. |
| **leitura** | Só ver o stock. Perfil por defeito de quem entra pela primeira vez. |

## Pôr a funcionar (uma vez)

1. **Publicar as regras.** No Firebase → Firestore Database → separador **Regras**: colar o conteúdo de `firestore.rules` e **Publicar**.

2. **Criar a tua conta.** Firebase → Authentication → Users → **Add user**, com o teu email e uma palavra-passe.

3. **Tornar-te dono.** Abre a aplicação (passo 5) e inicia sessão uma vez: isso cria o teu registo em `users/{o-teu-uid}` com perfil `leitura`. Depois, no Firestore → coleção `users` → o teu documento → muda o campo `role` de `leitura` para `owner`. Recarrega.

4. **Importar os dados.** Com sessão iniciada, abre `seed.html` e carrega em **Importar agora**. Confirma que diz tudo verde. Depois podes apagar o `seed.html`.

5. **Publicar no GitHub Pages.**
   - Cria um repositório e envia estes ficheiros (sem a pasta `data-export/` nem o `seed.html`, se já importaste).
   - No repositório → Settings → Pages → Source: branch `main`, pasta `/root` → Save.
   - Em poucos minutos fica em `https://<utilizador>.github.io/<repo>/`.

6. **Autorizar o domínio no Firebase.** Firebase → Authentication → Settings → **Authorized domains** → adiciona `<utilizador>.github.io`. Sem isto, o login não funciona no site publicado.

## Adicionar pessoas

1. A pessoa cria conta: ou tu crias em Authentication → Add user e dás-lhe a palavra-passe, ou (mais simples) ela abre o site, e na primeira entrada usa "Esqueci a palavra-passe" para definir a sua.
2. Assim que entra a primeira vez, aparece na aba **Utilizadores** como "Só leitura".
3. Nessa aba, um administrador atribui-lhe o perfil certo (Requisitante, Administrador…).

## Segurança — o que nunca vai para o Git

- A `firebaseConfig` que está no `index.html` (incl. `apiKey`) **é pública** por natureza. Não é segredo: quem protege os dados são as `firestore.rules`.
- **Nunca** commitar uma chave de *service account* (ficheiro JSON do Admin SDK). Aqui não é usada.
- Tudo exige login. Rever as regras sempre que mudar a estrutura de dados.
