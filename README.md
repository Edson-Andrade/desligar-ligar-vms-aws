# desligar-ligar-vms-aws
Progamação de Funcionamento de Instancias

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

Suba a tela e clique na aba "JSON", neste campo apague o que estiver definido e coloque, no espaço em branco o seguinte:

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

Aparecerá "Revisar política" onde deverá informar o "Nome" e clique em "Criar política"

Acesse a página:

Services -> Lambda > Funções > "Nome da função" 

Visualize o campo "Configuração" e então clicar em "Permissões". No campo "Resumo de recursos" e clique em "Amazon EC2" como na imagem abaixo:

<img width="676" alt="image" src="https://user-images.githubusercontent.com/112088274/186745245-4d65a472-7620-4406-a273-d603be2b13a1.png">

Onde deve ser vizualizado as permissões de "start" e "stop" na guia "Ações": 

Allow: ec2:Start*
Allow: ec2:Stop*

Confirmardo vá ao seguinte serviço:

Services -> Amazon EventBridge > Clique em "Criar Regra" (Aqui dois eventos deverão ser criados, cada um focado em sua função "Ligar" ou "Desligar")

Defina um "Nome", Defina "Barramento de Eventos" como "default" e no "Tipo de Regra" marque "Programação" e clique em "Próximo"

Em "Expressão cron" será agendado a execução para ligar ou desligar a instancia, como no exemplo a seguir:

cron (00 15 ? * 5 *) Ao

Em seguida aparecerá abaixo do "Cron" a programação das próxima execuções, clique em "Próximo"

Nas opções de "Tipos de destino" marque o "Serviço AWS", "Selecionar um destino" selecione "Função do Lambda" e selecione a função criada anteriormente com o nome escolhido, clique em configurações adicionais, após em "Configurar entrada de destino" selecione "Constante (texto JSON)" e em "Especificar a constante JSON" informe o seguinte: 

PARA LIGAR:

{
  "action": "start",
  "InstanceIds": [
    "ID-INSTÂNCIA"
  ]
}

PARA DESLIGAR:

{
  "action": "stop",
  "InstanceIds": [
    "ID-INSTÂNCIA"
  ]
}

Em "ID-INSTÂNCIA" será o ID da Instancia alvo.

Clique em "Próximo"

"Configura etiquetas" (tags) é opcional, mas sempre recomendado para melhor organização.

Clique em "Próximo"

Será vizualizado um resumo da configuração do evento e em seguida clique em "Criar Regra"

A Execução da função pode ser acompanhada pelos seus logs em: Services -> Lambda > Funções > "Nome da função" > Aba "Monitor" > Botão "Vizualizar logs no CloudWatch", onde em "Streams de log" haverá os eventos que ocorreram se foram ou não com sucesso no momento programado.

