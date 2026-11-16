# Configuração do WhatsApp Web no Fusion Gestão Pro

## O que esta versão implementa
- Serviço separado para manter sessões de WhatsApp Web por usuário/revendedor.
- QR Code individual na Central WhatsApp.
- Persistência das credenciais em disco persistente do Render.
- Reconexão automática quando a sessão cai (exceto quando o usuário desconecta/logout).
- Envio de texto para números não salvos na agenda, após verificar se o número possui WhatsApp.
- Lembretes automáticos integrados ao fluxo existente: 3 dias antes, 1 dia antes e no dia do vencimento.
- O backend continua exigindo consentimento WhatsApp do cliente e ignora clientes suspensos.

## Deploy do serviço separado no Render
1. Envie o projeto ao seu GitHub.
2. No Render, crie um Web Service apontando para o mesmo repositório.
3. Configure `Root Directory` como `whatsapp-web-service`.
4. Build Command: `npm install`.
5. Start Command: `npm start`.
6. Use Node 20 ou superior.
7. Adicione um Persistent Disk montado em `/var/data` (2 GB ou mais). Sem disco persistente, as sessões podem ser perdidas em reinicializações.
8. Gere um segredo forte para `FUSION_WA_SERVICE_TOKEN` no serviço. Copie exatamente o mesmo valor para as variáveis do backend principal.
9. Configure `WA_AUTH_DIR=/var/data/whatsapp-sessions`.
10. Copie a URL pública do serviço para `FUSION_WA_SERVICE_URL` no backend principal do Fusion.

O arquivo `whatsapp-web-service/render.yaml` serve como referência, mas confirme o plano e o disco no painel do Render. Disco persistente e serviço sempre ativo podem gerar cobrança.

## Agendamento
A aplicação já possui a rota protegida `POST /api/public/jobs/lembretes`. Configure um Cron Job diário para chamar essa rota no horário desejado, com o segredo de cron já usado pelo backend. Não exponha esse segredo no navegador. Se já existe um cron configurado para essa rota, mantenha-o; não crie um segundo cron duplicado.

## Variáveis no backend principal
- `FUSION_WA_SERVICE_URL`: URL HTTPS do Web Service no Render.
- `FUSION_WA_SERVICE_TOKEN`: o mesmo segredo configurado no serviço de WhatsApp.

Não coloque o token em variáveis `VITE_` nem em código frontend.

## Primeiro teste
1. Abra a Central WhatsApp logado como revendedor.
2. Clique em `Conectar meu WhatsApp`.
3. No celular: WhatsApp > Dispositivos conectados > Conectar dispositivo.
4. Leia o QR Code.
5. Aguarde aparecer `WhatsApp conectado`.
6. Ative os lembretes nas Configurações e faça um teste com um cliente de teste que autorizou receber mensagens.

## Avisos e limites
Esta é uma integração não oficial baseada no protocolo do WhatsApp Web, não uma API oficial. O WhatsApp pode alterar o protocolo, derrubar sessões, limitar ou bloquear contas. Não há garantia de funcionamento contínuo. Use apenas para mensagens esperadas por clientes que deram consentimento, respeite pedidos de interrupção e evite disparos em massa. O QR Code e as credenciais de sessão são sensíveis: nunca compartilhe logs ou arquivos da pasta de sessões.

A aplicação web e o serviço de WhatsApp precisam estar publicados e com as variáveis acima configuradas para funcionar em produção. O ZIP contém o código e as instruções; ele não cria sozinho os serviços no Render nem configura variáveis ou banco de dados remoto.
