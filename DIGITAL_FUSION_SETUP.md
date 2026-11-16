# Digital Fusion — ativação do aplicativo do cliente

Esta etapa adiciona a primeira versão do portal PWA do cliente e o acesso por código individual emitido pelo revendedor.

## O que entrou nesta versão

- Rota pública `/digital-fusion` com identidade visual Digital Fusion.
- Tela de instalação PWA; em produção, o acesso à conta fica bloqueado até o app estar instalado (modo standalone).
- Cada revendedor pode gerar um código na lista de Clientes pelo botão com ícone de chave.
- O código tem validade de 7 dias, é de uso único e é armazenado no banco somente como hash.
- O código vincula uma sessão anônima Supabase ao cadastro correto do cliente.
- O app mostra nome, plano, preço, vencimento e situação. Não expõe usuário/senha de acesso ao serviço do cliente.
- A autenticação do app usa uma chave de armazenamento separada para não sobrescrever a sessão do painel do revendedor no mesmo navegador.

## Antes de testar: Supabase

1. Confirme que este é o projeto Supabase correto antes de executar qualquer SQL. Não execute a migração em um banco diferente nem sobreponha dados existentes.
2. No SQL Editor do Supabase, revise e execute `drizzle/migrations/0005_customer_app_access.sql`.
3. Em Authentication → Sign In / Providers (ou configuração de Auth), habilite **Anonymous Sign-Ins**. O cliente não cria senha: a sessão anônima persistente do app fica vinculada ao código de ativação.
4. Confirme que a aplicação está publicada com `VITE_SUPABASE_URL` e `VITE_SUPABASE_PUBLISHABLE_KEY` corretas.

## Teste manual

1. Entre no Fusion Gestão Pro como revendedor.
2. Abra Clientes e use o botão de chave na linha de um cliente de teste.
3. Copie o código e o link `/digital-fusion` exibidos.
4. Abra o link em um celular compatível, instale o PWA e abra pelo ícone.
5. Digite o código. O cliente deve ver apenas o próprio nome, plano, preço e vencimento.
6. Gere outro código para o mesmo cadastro: isso revoga o vínculo anterior e permite ativar outro dispositivo. O código anterior deixa de ser válido.

## Limites desta etapa

- Ainda não há push notifications implementadas nem campanhas de desconto automáticas.
- Ainda não há checkout/pagamento no app; o botão de renovação aparece como “em breve”.
- O fluxo depende de conexão com a internet para validar o código e carregar o cadastro.
- O PWA não é um aplicativo nativo publicado na Play Store/App Store. No iPhone, a instalação é feita por Compartilhar → Adicionar à Tela de Início.
- O navegador precisa oferecer suporte à instalação PWA e o usuário precisa concluir a instalação. Em desenvolvimento (`npm run dev`), o bloqueio de instalação é ignorado para facilitar os testes locais.
- Se o cliente limpar os dados do navegador ou trocar de aparelho, o revendedor deve gerar um novo código. Isso invalida o vínculo anterior.

## Publicação

Este ZIP não executa a migração no Supabase nem publica o site automaticamente. Depois de aplicar a migração e habilitar Anonymous Sign-Ins, publique o frontend normalmente no ambiente do Fusion.
