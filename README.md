# desligar-ligar-vms-aws
Agendamento de funcionamento da VMs

Services -> Lambda > Funções > Criar Função

Entre as "opções a seguir para criar a função" será selecionada "Criar do zero", informará um "nome da função" (um nome claro, porém curto) e o "tempo de execução" (para a utilização desta função: Python 3.9). Ao clicar em "Criar função" será redirecionado para:

Services -> Lambda > Funções > "Nome da função"

Descendo a tela poderá ser visualizado o campo "Código". Normalmente neste campo já está definida uma função básica, que deverá ser apagada. No espaço em branco será difinido o seguinte:

import boto3
region = boto3.session.Session().region_name
ec2_client = boto3.client('ec2', region_name=region)

def lambda_handler(event, context):
    action = event.get('action')
    InstanceIds = event.get('InstanceIds')
    if action == 'start':
        ec2_client.start_instances(
            InstanceIds=InstanceIds
            )
        print(f'Instancia {InstanceIds} foi iniciada.')
    elif action == 'stop':
        ec2_client.stop_instances(
            InstanceIds=InstanceIds
            )
        print(f'Instancia {InstanceIds} foi desligada.')   
       
(Ainda na tela) Services -> Lambda > Funções > "Nome da função" 

Poderá ser visualizado o campo "Configuração" e então clicar em "Permissões". Assim poderá visualizar o campo "Papel de execução", clique onde terá o seguinte: "Nome da função"-role-(########), você será direcionado para:

Services -> IAM > Funções > "Nome da função"-role-(########)

Descendo a tela poderá ser visualizado o botão "Adicionar permissões" e então clicar em "Criar política em linha". 
Ao abrir a tela "Criar política" abaixo campo "Editor visual" e no campo "Escolha um serviço" busque  EC2e selecione-o.
Aparecerá o Nivel de acesso" que será selecionado os campos "Lista" e "Leitura", após isso, logo abaixo clique em "recursos", desça a página e no campo "recursos" marque a opção "Todos os recursos".

Suba a tela e clique na aba "JSON", neste campo apague o que estiver definido e coloque, no espaçõ em branco o seguinte:

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "ec2:Start*",
                "ec2:Stop*"
            ],
            "Resource": "arn:aws:ec2:sa-east-1:853307573551:instance/i-0b09c67043c58adec"
        }
    ]
}

E clique em "Revisar política" logo abaixo.

Aparecerá "Revisar política" onde deverá informar o "Nome" e clique em "Clicar política"

Acesse a página:

Services -> Lambda > Funções > "Nome da função" 

Visualize o campo "Configuração" e então clicar em "Permissões". No campo "Resumo de recursos" e clique em "Amazon EC2" como na imagem abaixo:

<img width="676" alt="image" src="https://user-images.githubusercontent.com/112088274/186745245-4d65a472-7620-4406-a273-d603be2b13a1.png">



