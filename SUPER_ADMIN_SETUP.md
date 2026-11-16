# Fusion Gestão Pro — primeira etapa do Super Admin

## O que foi preparado

- Nova rota protegida `/super-admin`.
- Item Super Admin aparece no menu somente quando a função de autorização do banco retorna `true`.
- Painel inicial com indicadores de revendedores, contas ativas, testes, suspensões e pagamentos recebidos.
- Listagem de contas e ação de suspender/reativar contas ativas/suspensas.
- Migração SQL separada que cria a camada administrativa sem alterar `clients`, `servers`, `payments`, `settings` nem os dados legados.
- `.env` removido do pacote e regras de exclusão adicionadas ao `.gitignore`.

## Antes de usar

1. Faça backup do Supabase.
2. Revise e aplique `supabase/migrations/202611120001_platform_control_plane.sql` no ambiente de teste primeiro.
3. Abra `supabase/manual/GRANT_SUPER_ADMIN.sql`, substitua o marcador pelo e-mail exato da conta proprietária e execute manualmente no SQL Editor do Supabase.
4. Entre novamente no Fusion Gestão Pro. O menu Super Admin aparecerá para essa conta.
5. Cadastre tenants de teste diretamente no ambiente de homologação e valide que um usuário comum não consegue consultar `platform_tenants`.

A migração não concede Super Admin automaticamente e não cria preços comerciais. Os planos e valores precisam ser definidos pelo proprietário.

## Limites desta entrega

Esta é a primeira etapa do painel de controle. Cadastro/convite automático, checkout, webhooks de pagamento, assinaturas recorrentes, trial automático, WhatsApp por revendedor e a migração dos dados de `owner_id` para `tenant_id` ainda não foram implementados. A listagem só mostrará tenants criados após a aplicação da migração.

A migração antiga `drizzle/migrations/0001_isolate_account_data.sql` contém um UUID fixo e políticas de dados legadas. Não a execute novamente nem a altere em produção sem identificar o usuário dono dos dados e conferir o estado real do banco. Esta entrega não tenta adivinhar esse UUID.

## Segurança

- A autorização do Super Admin é conferida no banco por `is_platform_super_admin()` e pela tabela privada `platform_admins`; esconder um link no frontend não é considerado controle de segurança.
- A tabela `platform_admins` não possui política para usuários autenticados comuns. Somente `service_role` pode administrá-la; o Super Admin consulta a função booleana, não a lista de administradores.
- Não coloque `SUPABASE_SERVICE_ROLE_KEY`, tokens do WhatsApp ou outras credenciais no frontend, ZIP ou GitHub.
- Ainda é necessário adicionar testes automatizados de RLS com dois usuários e validar a migração no projeto Supabase real antes de produção.

## Configuração local

Copie `.env.example` para `.env` na máquina de desenvolvimento e preencha os valores a partir das configurações do Supabase. O arquivo `.env` real não acompanha este pacote.
