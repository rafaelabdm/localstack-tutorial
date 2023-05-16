<h1 align="center">
	✏️ Localstack Tutorial Hello World
</h1>

<p align="center"><br>
Nesse tutorial vamos subir uma aplicação com localstack. O Localstack é um emulador de serviço cloud que nos permite testar
nossas aplicações AWS ou Lambdas localmente na nossa máquina sem ter que conectar com um provedor de nuvem. Isso economiza tempo
na hora de testar ou alterar algo no nosso app.<br><br>
</p>

<p align="center">
Você pode descobrir mais no link: https://docs.localstack.cloud/getting-started/
</p>

---

<h2>✔️ Requisitos:</h2>
<ol>
  Docker: https://www.docker.com/products/docker-desktop/ <br>
  Python (Precisa ser uma versão de 3.10 pra baixo): https://www.python.org/downloads/release/python-3100/ <br>
  AWS CLI: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html <br>
  AWS SAM CLI for LocalStack: <TT>pip install aws-sam-cli-local</TT> <br>
</ol>

<h2>🔨 Passo a Passo:</h2>

<h3>DOCKER RUN</h3>
<p>
  Depois de tudo instalado, precisamos subir o container do localstack para receber nossas requisições. <br>
  Inicializamos o Docker Descktop, para ele rodar nosso Linux Kernel <br>
  Em seguida rodamos o seguinte comando: <br>
</p>

```
docker run --name localstack -d -p 4566:4566 localstack/localstack
```
<p>
  Entretanto, se você estiver no Windows, assim como eu... Você deve subir seu container através do docker-compose. 
  Isso porque precisamos compor nosso container com mais uma configuração para conseguir rodar em ambiente Windows. <br>
  Então escolha uma pasta de sua preferência, crie um arquivo docker-compose.yml e cole o texto aseguir:
</p>

```c
version: "3.8"

services:
  localstack:
    container_name: "${LOCALSTACK_DOCKER_NAME-localstack_main}"
    image: localstack/localstack
    ports:
      - "127.0.0.1:4566:4566"            # LocalStack Gateway
      - "127.0.0.1:4510-4559:4510-4559"  # external services port range
    environment:
      - DEBUG=${DEBUG-}
      - DOCKER_HOST=unix:///var/run/docker.sock
    volumes:
      - "${LOCALSTACK_VOLUME_DIR:-./volume}:/var/lib/localstack"
      - "/var/run/docker.sock:/var/run/docker.sock" # Esse carinha aqui faz toda a diferença em ambiente Windows
```

<p>
  Se optou pelo caminho do docker-compose, troque o comando do <TT>docker run [...]</TT> por <TT>docker-compose up</TT> 
  e pronto!<br>
  Depois disso, abra outro terminal e execute <TT>docker ps</TT>, devemos obter a lista de containers em execução:<br>
</p>

```
C:\Users\user>docker ps
CONTAINER ID   IMAGE                   COMMAND                  CREATED             STATUS                    PORTS                                                                    NAMES
10197c3caa8a   localstack/localstack   "docker-entrypoint.sh"   About an hour ago   Up 20 seconds (healthy)   127.0.0.1:4510-4559->4510-4559/tcp, 127.0.0.1:4566->4566/tcp, 5678/tcp   localstack_main
```


<h3>INIT</h3>
<p>
  Após subirmos nosso container com sucesso, vamos para nosso app. Agora entra o SAM CLI para Localstack.<br>
  O SAMLOCAL CLI é igual ao SAM CLI, mas ele redireciona nossas requisições para o localstack que acabamos de subir. <br>
  Para começar, escolha uma pasta de sua preferência e digite o comando: <TT>samlocal init</TT> <br>
  Esse comando deve rodar uma série de perguntas para configuração do seu projeto, escolha de acordo com as setas: <br>
</p>

```
C:\Users\user\Desktop>samlocal init

You can preselect a particular runtime or package type when using the `sam init` experience.
Call `sam init --help` to learn more.

Which template source would you like to use?
        1 - AWS Quick Start Templates <----------- esse
        2 - Custom Template Location
Choice: 1

Choose an AWS Quick Start application template
        1 - Hello World Example       <----------- esse
        2 - Data processing
        3 - Hello World Example With Powertools
        4 - Multi-step workflow
        5 - Scheduled task
        6 - Standalone function
        7 - Serverless API
        8 - Infrastructure event management
        9 - Lambda Response Streaming
        10 - Serverless Connector Hello World Example
        11 - Multi-step workflow with Connectors
        12 - Lambda EFS example
        13 - DynamoDB Example
        14 - Machine Learning
Template: 1

Use the most popular runtime and package type? (Python and zip) [y/N]: <----------- ENTER

Which runtime would you like to use?
        1 - aot.dotnet7 (provided.al2)
        2 - dotnet6
        3 - go1.x
        4 - go (provided.al2)
        5 - graalvm.java11 (provided.al2)
        6 - graalvm.java17 (provided.al2)
        7 - java17
        8 - java11
        9 - java8.al2
        10 - java8
        11 - nodejs18.x
        12 - nodejs16.x
        13 - nodejs14.x
        14 - nodejs12.x
        15 - python3.9
        16 - python3.8
        17 - python3.7
        18 - python3.10 <----------- se você baixou o que eu deixei no link em Requisitos escolha esse,
        19 - ruby2.7                   caso contrário escolha a versão do python que você tem instalada.
        20 - rust (provided.al2)
Runtime: 18

What package type would you like to use?
        1 - Zip  <----------- esse
        2 - Image
Package type: 1

Based on your selections, the only dependency manager available is pip.
We will proceed copying the template using pip.

Would you like to enable X-Ray tracing on the function(s) in your application?  [y/N]: <----------- ENTER

Would you like to enable monitoring using CloudWatch Application Insights?
For more info, please view https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch-application-insights.html [y/N]:
<----------- ENTER

Project name [sam-app]: tutorial-sam <----------- o nome do seu projeto, pode escolher :)

    -----------------------
    Generating application:
    -----------------------
    Name: tutorial-sam
    Runtime: python3.10
    Architectures: x86_64
    Dependency Manager: pip
    Application Template: hello-world
    Output Directory: .
    Configuration file: tutorial-sam\samconfig.toml

    Next steps can be found in the README file at tutorial-sam\README.md


Commands you can use next
=========================
[*] Create pipeline: cd tutorial-sam && sam pipeline init --bootstrap
[*] Validate SAM template: cd tutorial-sam && sam validate
[*] Test Function in the Cloud: cd tutorial-sam && sam sync --stack-name {stack-name} --watch
```

<p><br>
  Isso deve ter gerado uma pasta com o nome do projeto. Entre na pasta para executar os próximos passos.<br>
  <TT>cd tutorial-sam</TT>, no meu caso<br>
</p>

<h3>BUILD</h3>
<p>
  Já dentro da pasta faremos uma edição no arquivo <TT>template.yaml</TT> <br>
  Dentro de Outputs:, vamos alterar a variável Value: da nossa HelloWorldApi:<br><br>
  
  ANTES:
</p>

```
Outputs:
  HelloWorldApi:
    Description: "API Gateway endpoint URL for Prod stage for Hello World function"
    Value: !Sub "https://${ServerlessRestApi}.execute-api.${AWS::Region}.amazonaws.com/Prod/hello/"
```

<p><br>
  Depois:
</p>

```
Outputs:
  HelloWorldApi:
    Description: "API Gateway endpoint URL for Prod stage for Hello World function"
    Value: !Sub "https://${ServerlessRestApi}.execute-api.${AWS::Region}.localhost.localstack.cloud/Prod/hello/"
```

<p>
  Após esse passo, nossa aplicação está pronta para ser buildada. Ainda na pasta da app, execute <TT>samlocal build</TT> 
  e espere terminar de rodar. Se sua versão do pyhton estiver compátivel com a que você selecionou anteriormente 
  deve funcionar normalmente e aparecer a seguinte mensagem:<br>
</p>

```
C:\Users\user\Desktop\tutorial-sam>samlocal build
Starting Build use cache
Manifest file is changed (new hash: 3298f13049d19cffaa37ca931dd4d421) or dependency folder (.aws-sam\deps\c777bf72-7276-4754-8364-c67eac61c348) is
missing for (HelloWorldFunction), downloading dependencies and copying/building source
Building codeuri: C:\Users\user\Desktop\tutorial-sam\hello_world runtime: python3.10 metadata: {} architecture: x86_64 functions:
HelloWorldFunction
Running PythonPipBuilder:CleanUp
Running PythonPipBuilder:ResolveDependencies
Running PythonPipBuilder:CopySource
Running PythonPipBuilder:CopySource

Build Succeeded <------ sucessoooo!

Built Artifacts  : .aws-sam\build
Built Template   : .aws-sam\build\template.yaml

Commands you can use next
=========================
[*] Validate SAM template: sam validate
[*] Invoke Function: sam local invoke
[*] Test Function in the Cloud: sam sync --stack-name {{stack-name}} --watch
[*] Deploy: sam deploy --guided
```

<h3>DEPLOY</h3>
<p>
  Agora é a hora do deploy! É só digitar <TT>samlocal deploy --guided</TT> e seguir os passos:<br>
</p>

```
C:\Users\user\Desktop\tutorial-sam>samlocal deploy --guided

Configuring SAM deploy
======================

        Looking for config file [samconfig.toml] :  Found
        Reading default arguments  :  Success

        Setting default arguments for 'sam deploy'
        =========================================
        Stack Name [tutorial-sam]: <--------- ENTER
        AWS Region [us-east-1]:    <--------- ENTER
        #Shows you resources changes to be deployed and require a 'Y' to initiate deploy
        Confirm changes before deploy [Y/n]: n <--------- N
        #SAM needs permission to be able to create roles to connect to the resources in your template
        Allow SAM CLI IAM role creation [Y/n]: <--------- ENTER
        #Preserves the state of previously provisioned resources when an operation fails
        Disable rollback [y/N]: <--------- ENTER
        HelloWorldFunction may not have authorization defined, Is this okay? [y/N]: y <--------- Y
        Save arguments to configuration file [Y/n]: <--------- ENTER
        SAM configuration file [samconfig.toml]: <--------- ENTER
        SAM configuration environment [default]: <--------- ENTER

        Looking for resources needed for deployment:
```

<p>
<SUB>🛑 Caso apareça um ERRO falando algo sobre região: apenas rode o comando <TT>aws confiure</TT>, preencha o campo region 
com <TT>us-east-1</TT> e o campo seguinte com <TT>json</TT>. Os dois campos anteriores podem ser preenchidos com 
qualquer nome. </SUB>
	
  Se tudo der certo o deploy vai acontecer com sucesso, e ele apresentará os caminhos para acessar sua HelloWorldApi 
  no campo OUTPUTS:<br>  
</p>

```
CloudFormation outputs from deployed stack
-------------------------------------------------------------------------------------------------------------
Outputs
-------------------------------------------------------------------------------------------------------------
Key                 HelloWorldApi
Description         API Gateway endpoint URL for Prod stage for Hello World function
Value               https://6i3s7f0acz.execute-api.localhost.localstack.cloud:4566/Prod/hello/ <----- sua URL

[...]
-------------------------------------------------------------------------------------------------------------
```

<p>
  Agora é só acessar a URL fornecida a cima e tá lá! A mensagem mais amada de todos os programadores:<br>
</p>

![Sem título](https://github.com/rafaelabdm/localstack-tutorial/assets/102771790/b8252b44-a798-401f-bfa9-18d29f8ddd0d)
