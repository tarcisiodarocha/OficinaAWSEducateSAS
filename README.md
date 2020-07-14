# OficinaAWSEducateSAS
Oficina a cerca de recursos do programa AWS Educate para o SAS do PROCC - Visão do Aluno

Links úteis:

* https://docs.aws.amazon.com/


## Objetivos

* Introduzir os recursos oferecidos pelo AWS Educate 
* Experimentar a condução de prática de laboratório no estilo videoconcerência através dos seguintes recursos:
  * EC2 e Cloud9 (AWS)
  * GitHub Classroom  

## (A) Preparar abas

### Tarefa A1. Entrar em sua conta do GitHub 

* Abrir: https://github.com/login 
* Fazer login na sua conta do GitHub
* Procurar e acessar repositório "tarcisiodarocha/OficinaAWSEducateSAS"

### Tarefa A2. Abrir Painel de Progresso 

* Abrir: https://docs.google.com/spreadsheets/d/1vbQuiA4edvL4aKPGspwJb1nVaINSddnJWwYQ5cq4OLw/edit?usp=sharing
* Preencher seu nome e email

### Tarefa A3. Entrar no AWS Educate

* Abrir e fazer login: https://www.awseducate.com/signin/SiteLogin 
* (Exibição da visão do aluno: AWS Educate )

### Tarefa A4. Entrar no AWS Console 

* Seguir passos via conta da AWS Educate
* Abrir aba com EC2
* Abrir aba com Cloud9 



## (B) Criar instância no EC2

### Tarefa B1. Criar nova instância 

* Clicar em "Instances" > "Launch instance"
* Selecionar a imagem "Ubuntu Server 18.04 LTS (HVM), SSD Volume Type"
* Selecionar o tipo da instância "t2.micro"
* Clicar em "Review and launch" e depois "Launch"

### Tarefa B2. Criar, baixar chave privada, e lançar instância

* Selecionar "Create a new key pair"
* Dar nome à chave: "chave-rabbitmq"
* Clicar em "Launch Instances" e depois em "View Instances"
* Renomear instância para "RabbitMQ1"

### Tarefa B3. Tentar pingar na instância

* Copiar IP público da instância
* Abrir nova aba: https://ping.eu/ping/
* Colar o ip da instância em "IP address", entrar código e clicar em "Go"

### Tarefa B4. Incluir ICMP, e portas 5672 e 15672 no grupo de segurança da instância

* Clicar no Security Group da instância. Ex: "launch-wizard1" 
* Selecionar "Inbound rules" e "Edit inbloud rules"
* Adicione nova regra "Add rule"
* Na nova regra troque "Custom TCP" por "All ICMP- IPv4" e "Custom" por "Anywhere"
* Adicione nova regra "Add rule"
* Coloque 5672 em "port range" e troque "Custom" por "Anywhere"
* Adicione nova regra "Add rule"
* Coloque 15672 em "port range" e troque "Custom" por "Anywhere"
* Clique em "Save rules"

### Tarefa B5. Pingar novamente na instância

* Via https://ping.eu/ping/


## (C) Criar novo ambiente no Cloud9 e acessar instância  

### Tarefa C1. Criar novo ambiente

* No Cloud9 clique em "Create environment"
* Dê nome ao ambiente (ex:  "SAS") e clique "Next step"
* Dentre as opções default mude a plataforma de "Amazon Linux" para "Ubuntu Server" 
* Clique "Next step" e "Create environment"

### Tarefa C2. Fazer ssh na instância "RabbitMQ1" 

* No Cloud9, clique em "File" > "Upload Local Files"
* Clique em "Select files" e na pasta "Downloads" da sua máquina, escolha a chave "chave-rabbit.pem" que foi baixada anteriormente
* No console do Cloud9, mude as permissões da chave com:
    ```
       chmod 400 chave-rabbit.pem
    ``` 
* Com o IP da sua instância, faça ssh. Exemplo:
    ``` 
       ssh -i chave-rabbit.pem ubuntu@100.26.162.202
    ``` 

## (D) Instalar RabbitMQ na instância via SSH

### Tarefa D1 – Atualizar Ubuntu e Instalar o Erlang

Via conexão ssh estabeleciada com a instância "RabbitMQ1" execute:

``` 
sudo apt update
sudo apt upgrade
``` 

``` 
wget https://packages.erlang-solutions.com/erlang/debian/pool/esl-erlang_23.0.2-2~ubuntu~bionic_amd64.deb
sudo dpkg -i esl-erlang_23.0.2-2~ubuntu~bionic_amd64.deb
``` 

Pode acontecer um erro de dependência do comando anterior. Instale as dependências com o comando:

``` 
sudo apt install -f
``` 

E reexecute o comando para instalar o Erlang:

``` 
sudo dpkg -i esl-erlang_23.0.2-2~ubuntu~bionic_amd64.deb
``` 

### Tarefa D2 – Instalar e Iniciar o RabbitMQ Server

Execute:
``` 
echo "deb https://dl.bintray.com/rabbitmq/debian bionic main" | sudo tee /etc/apt/sources.list.d/bintray.rabbitmq.list
wget -O- https://www.rabbitmq.com/rabbitmq-release-signing-key.asc | sudo apt-key add -
``` 
``` 
sudo apt update
sudo apt install rabbitmq-server
``` 
``` 
sudo systemctl start rabbitmq-server.service
sudo systemctl enable rabbitmq-server.service
``` 

### Tarefa D3 – Criar Admin User no RabbitMQ

Obs: Trocar "admin" por seu nome de usuário e "password" por uma senha.
``` 
sudo rabbitmqctl add_user admin password 
sudo rabbitmqctl set_user_tags admin administrator
sudo rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"
``` 

### Tarefa D4 – Ativar RabbitMQ Web Management Console
``` 
sudo rabbitmq-plugins enable rabbitmq_management
``` 

### Tarefa D5. Acesse o painel de gerenciamento

* No EC2, vá em "Instances" e selecione a instância "RabbitMQ1" 
* Copie o "Public DNS" e cole em uma aba no browser
* Acrescente ":15672" no final da URL e clique "enter"


## (E) Exemplo simples de uso do servidor RabbitMQ

### Tarefa E1. Clonando o repositório público "OficinaAWSEducateSAS"

* Logado na sua conta do GitHub, faça "fork" no repositório "tarcisiodarocha/OficinaAWSEducateSAS"
* No branch criado, clique em "Code" e copie a URL do repositório para clonagem
* No console do Cloud9 (diretório ~/environment/ ) clone o repositório com o seguinte comando (forneça a url do repositório)
``` 
   git clone <url-do-repositorio>
``` 

### Tarefa E2. Clonando um repositório de assignment do GitHub Classroom 

* Siga os passos a partir dessa URL de assignment: https://classroom.github.com/a/2JnkiXja
* No repositório criado, clique em "Code" e copie a URL do repositório para clonagem
* No console do Cloud9 (diretório ~/environment/ ) clone o repositório com o seguinte comando (forneça a url do repositório)
``` 
   git clone <url-do-repositorio>
``` 

### Tarefa E3. Preparando as dependências do exemplo

* No console do Cloud9, instale a dependência do emissor Python
``` 
   go get github.com/streadway/amqp
``` 
* Instale a dependência do receptor Go
``` 
   python -m pip install pika --upgrade
``` 


### Tarefa E4. Enviando mensagem com emissor Python

* No console do Cloud9, entre no diretório do repositório do assignment
* Na árvore de diretórios à esquerda, abra o arquivo "Emissor.py" 
* Altere "usuário", "senha" e "hostname" com os dados do seu servidor RabbitMQ e salve o arquivo com "CRTL+S"
* No console, execute o emissor com
``` 
   python Emissor.py
``` 

### Tarefa E5. Recebando mensagem com receptor Go

* Abra o arquivo "Receptor.go" 
* Altere "usuário", "senha" e "hostname" com os dados do seu servidor RabbitMQ e salve o arquivo com "CRTL+S"
* No console, execute o emissor com
``` 
   go run Receptor.go
``` 

### Tarefa E6. Submeta um commit da tarefa ao professor do assignment

* No diretório do assignment, execute os comandos git
``` 
   git add .
   git commit -m "submissão da tarefa"
   git push
``` 


 
 
