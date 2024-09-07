<h1 align="center">WhaTicket Versão Saas com Módulo Kanban, Modo Noturno e as seguintes integrações: | Versão 4.8.5</h1>

🗣️ DialogFlow
🔄 N8N
🌐 WebHooks
🤖 TypeBot
💬 ChatGPT

##Índice

01. Informações Importantes
02. Link do GitHub
03. Dados do Servidor e da Instalação
04. Atualizar o Servidor e Reiniciar - Passo 01
05. Instalador do Whaticket - Passo 02
06. Processo de instalação no terminal ssh - Passo 03
07. Fix nginx.conf
08. Fix Baileys, reinicio programado das aplicações nodes
09. Comandos node / pm2
10. Personalização e Teste Redis
11. Instalação com Docker / Ubuntu 22

=======================================================================================================================================================================

## 01. Informações Importantes

Utilizar Ubuntu 20.04
Mínimo requerido: 4 vcpu e 6gb RAM
Recomendado: 4 vcpu e 8gb RAM

**** Somente instale quando os endereços de frontend e backend estiverem pingando para o ip do servidor.

Confira em https://dnschecker.org/ se está propagado em todas as regiões.
Ou através do prompt de comando do Windows * Tecla Windows + R = CMD e comandos
ping seudominio.com.br

**** Não utilize caracteres especiais e letras maiúsculas no processo de instalação, não utilize numeros no nome de app.

**** Confira se seu provedor de serviço de vps tem algum firewall e a necessidade de liberar portas nele.
As portas em uso são 22, 80, 443, 3000, 4000, 5000, 5432 e 6379.

**** Se o seu servidor não acessa direto com usuário root habilite o privilegio com o comando sudo su, ou sudo passwd root (defina uma senha root), sudo su e faça o login com a senha que cadastrou.

*** Nunca delete o usuário admin, id 1 e a empresa criada na instalação, ao editar os dados do usuário superadmin não se esqueça de preencher a senha atual ou uma nova senha, se não ele salva a senha em branco no banco de dados.

**** Vá em configurações e empresas para habilitar o envio de campanhas. Após ativar faça logout e login para o menu aparecer.

**** O Plano 1 pode ser excluído, ele não aceita troca de nome, mas ao excluir vincule um novo plano a empresa 1, se não, não vai conseguir adicionar conexões, filas e novos usuários.

**** Ao mudar gerenciamento de horários, esvazie os campos preenchidos da opção atual, exemplo ao mudar de um gerenciamento de horário por fila para por empresa, vá as horários das filas e deixe os horários em branco (para que as informações dos horários sejam apagadas do banco de dados), faça o mesmo se for trocar um gerenciamento de horário de empresa para fila, limpando os campos de horários da empresa ou se desativar o horário limpando os campos atualmente ativos.


=======================================================================================================================================================================

## 02 . Link do GitHub

* Github - Instalador
https://github.com/alelopesnet/instalador.git

* Código
https://github.com/alelopesnet/atendezap.git

=======================================================================================================================================================================
## 03. Dados da Instalação

Substitua [[]] por seus dados personalizados.

Servidor:
IPV4: [[1.1.1.1]]
IPV6:
Senha: [[senha]]

Frontend = endereço principal do chatbot: [[app.dominio.com]]
Backend/API = endereço da api interna: [[api.dominio.com]]
App: [[nomedoapp]]
Senha deploy: [[senha-alfanumérica]]

Usuário super-admin: admin@admin.com
Senha: 123456

=======================================================================================================================================================================

## 04. Passo 01 - Atualizar o Servidor

*** Atualizar os pacotes do servidor Ubuntu - utilizando privilegio root:
sudo apt -y update && apt -y upgrade

Reinicie o servidor para concluir as atualizações:
reboot

=======================================================================================================================================================================

## 05. Passo 02 - Baixar o instalador do github

** Execute esses comandos no seu cliente ssh (recomendado bitvise)

cd /home
ls
sudo apt install -y git && git clone https://github.com/alelopesnet/instalador.git instalador && sudo chmod -R 777 instalador  && cd instalador  && sudo ./install_primaria

=======================================================================================================================================================================

## 06. Passo 03 - Executar a instalação

Siga o passo-a passo do terminal exibido - Tenha atenção ao copiar e colar para não ter espaços no final da expressão:

[0] Instalar WhatsPainel/Whaticket

Insira senha para o usuário Deploy e Banco de Dados (Não utilizar caracteres especiais ou letras maiúsculas)
[[senhadeploy]]

Insira o link do GITHUB do seu Whaticket que deseja instalar:
https://github.com/alelopesnet/atendezap.git

Informe um nome para a Instancia/Empresa que será instalada (Não utilizar espaços, caracteres especiais ou letras maiúsculas, utilize somente letras minúsculas;):
[[nomedoapp]]

Informe a Qt. de de Conexões/Whats que a poderá cadastrar: 1 a 9999
9999

Informe a Qt. de Usuários/Atendentes que a poderá cadastrar: de 1 a 9999
9999

Digite o domínio do FRONTEND/PAINEL; Ex: app.appbot.cloud
[[app.dominio.com]]

Digite o domínio do BACKEND/API; Ex: api.appbot.cloud
[[api.dominio.com]]

Digite a porta do FRONTEND para a appbot; Ex: 3000 A 3999
3000

Digite a porta do BACKEND para esta instancia; Ex: 4000 A 4999
4000

Digite a porta do REDIS/AGENDAMENTO MSG para a appbot; Ex: 5000 A 5999 = padrão dos redis é 6379 / na instalação com docker usar 5000
5000

=======================================================================================================================================================================

## 07. Fix nginx.conf

Corrigir erros de atualização da página.

Edite o arquivo de configurações do nginx: 
nano /etc/nginx/nginx.conf

adicione o header abaixo: (imediatamente acima de # server_tokens off;)

underscores_in_headers on;

+ Ctrl e X para fechar o terminal, e Y para salvar as alterações.

Teste as alterações do nginx:

nginx -t

Reinicie o serviço:

service nginx reload

=======================================================================================================================================================================

## 08. Fix Baileys, reinicio programado das aplicações node.

Utilizamos as tarefas agendadas, cron jobs, para programar o comando pm2 restart all:

crontab -e

Se você tiver vários editores de texto instalados, o sistema solicitará que você selecione um editor para atualizar a lista de tarefas cron. Use o número entre parênteses para escolher sua opção preferida. Estaremos usando a opção padrão, nano. (Escolha numéricanúmerica).
No modelo abaixo a cada 12 horas teremos um reinicializaçãoreniciaalização do frontend e do backend.

0 */6 * * * /usr/bin/node /usr/bin/pm2 restart all

Para outras opções de modelos cron, acesse um dos sites abaixo:

https://crontab.guru/
https://crontab-generator.org/
https://crontab.cronhub.io/

=======================================================================================================================================================================

## 09. Comandos node / pm2

======================================================================================================================================================== 
Comando para build/re-build:
sudo npm run build
======================================================================================================================================================== 
Comando para atualizar as bibliotecas;
npm update --force
======================================================================================================================================================== 
Comando para reiniciar pm2:
pm2 restart all
======================================================================================================================================================== 
Log:
pm2 log

Limpeza de log:
pm2 flush "id"
========================================================================================================================================================

## 10. Personalização e Teste Redis

##  Alterar Nome, Cor Primária, Logotipo e Favicon:

** Alterar Cor Primária: (#2DDD7F)
/frontend/src/App.js
/frontend/src/layout/index.js

** Cores do Chat Interno: (validar)
/frontend\src\pages\Chat\ChatMessages.js
frontend\src\pages\Chat\ChatList.js

** Cores da Lista de Tarefas (validar)
/frontend/src/pages/ToDoList/index.js

** Logo e LogoLogin:
/frontend/src/assets

** Icone e Favicon:
/frontend/public

** Comando para rebuild, caminho absoluto /home/deploy/"nome"/

cd /frontend npm run build

===================================================================================================================================================== 

##  Acesso ao PostgreSQL:

Utilize o PGAdmin4, com as credencias do banco de dados salvas no arquivo .env do backend e os dados de acesso root, IP e senha como túnel.

======================================================================================================================================================== 
Redis - Banco de Dados de Agendamento
A seguir encontra-se uma sequência de comandos usada para testar se a senha do Redis funciona. O primeiro comando tenta definir uma chave para um valor antes da autenticação:

Teste (Com Docker)

docker ps
docker exec -it CONTAINER ID bash
redis-cli
auth sua_senha

(Sem Docker)
redis-cli 
set key1 10
Isso não funcionará porque você não se autenticou, então o Redis retorna um erro:

(error) NOAUTH Authentication required.
O próximo comando autentica-se com a senha especificada no arquivo de configuração do Redis:

auth sua_senha
O Redis reconhece:

ping
reposta: pong
 
Após confirmar que você é capaz de executar comandos no cliente Redis depois da autenticação, você poderá sair do redis-cli:

quit
======================================================================================================================================================== 


## 11. Instalação no Ubuntu 22
Incapaz de criar o usuário deploy, criar manualmente antes da instalação.

Acesse o terminal SSH e atualize seu servidor:

sudo apt -y update && apt -y upgrade

Adicione manualmente o usuário deploy:

adduser deploy

* Insira a senha
* Repita a senha
* Enter para os campos de dados pessoais, não preencher.

usermod -aG sudo deploy
ou
adduser deploy sudo

Baixe o instalador:

cd /home
ls
sudo apt install -y git && git clone https://github.com/alelopesnet/instalador.git instalador && sudo chmod -R 777 instalador  && cd instalador  && sudo ./install_primaria

|||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||

Teste do redis/docker:
docker ps
docker exec -it redis-[[nome do app]] bash
redis-cli
auth [[senhadeploy]]
ping
reposta: pong

* fix, caso pm2 status não liste os apps depois de um reboot:

sudo su - deploy
cd /home/deploy/[[nome do app]]/frontend
sudo pm2 start server.js --name [[nome do app]]-frontend
sudo pm2 save --force

sudo su - deploy
cd /home/deploy/[[nome do app]]/backend
sudo pm2 start dist/server.js --name [[nome do app]]-backend
sudo pm2 save --force

* caso salvo acima, e não aparece após o novo reboot, repetir e incluir estes comandos:
sudo su - root
pm2 startup
sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u deploy --hp /home/deploy
