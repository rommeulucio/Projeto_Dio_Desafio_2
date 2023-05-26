# Desafio de Projeto: Adicionando Segurança em APIs na AWS com Amazon Cognito

## Sobre:

Este projeto consiste em uma solução para gerenciamento de usuários e autenticação em aplicações web e mobile utilizando o serviço da AWS Amazon Cognito. Com ele é possível implementar diversos mecanismos de segurança, além de integrar com terceiros como Facebook, Google e Apple.
A solução é composta pelos seguintes serviços da AWS: Amazon Cognito, DynamoDB, API Gateway e AWS Lambda. Será criada uma API REST no Amazon API Gateway, em seguida uma tabela NoSQL será criada no DynamoDB e posteriormente integrada à API Gateway com o Lambda. Com o Amazon Cognito vamos criar um ‘User Pool’ para gerenciar e autorizar novos usuários com a devida autenticação, esses usuários terão acesso para inserir novos registros no DynamoDB. Ao final vamos utilizar o Postman para interagir com a API, vamos criar um novo usuário, realizar a autenticação e inserir novos registros no DynamoDB.


## Pré-requisitos

* Conta na AWS
* Conhecimentos básicos em AWS Lambda, Amazon API Gateway e DynamoDB
* Postman instaladoa

## Tecnologias utilizadas

* [Amazon Cognito](https://docs.aws.amazon.com/pt_br/cognito/index.html)
* [DynamoDB](https://docs.aws.amazon.com/pt_br/amazondynamodb/latest/developerguide/Introduction.html)
* [AWS Lambda](https://docs.aws.amazon.com/pt_br/lambda/latest/dg/welcome.html)
* [Amazon API Gateway](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/welcome.html)

## Etapas do desenvolvimento:

### Criando uma API REST no Amazon API Gateway

- API Gateway Dashboard -> Create API -> REST API -> Build
- Protocol - REST -> Create new API -> API name [nome a api] -> Endpoint Type - Regional -> Create API



- Resources -> Actions -> Create Resource -> Resource Name [Items] -> Create Resource




### No Amazon DynamoDB

- DynamoDB Dashboard -> Tables -> Create table -> Table name [Items] -> Partition key [id] -> Create table




### No AWS Lambda

- Lambda Dashboard -> Create function -> Name [put_item_function] -> Create function



- Inserir código da função ```put_item_function.js``` disponível na pasta ```/src``` -> Deploy



- Configuration -> Permissions -> Execution role -> Abrir a Role no console do IAM



- IAM -> Permissions -> Add permissions -> create inline policy




- Service - DynamoDB -> Manual actions -> add actions -> putItem




- Resources -> Add arn -> Selecionar o arn da tabela criada no DynamoDB -> Add




- Review policy -> Name [crie o nome da policy] -> Create policy





### Integrando o API Gateway com o Lambda backend

- API Gateway Dashboard -> Selecionar a API criada -> Resources -> Selecionar o resource criado -> Action -> Create method - POST

- Integration type -> Lambda function -> Use Lambda Proxy Integration -> Lambda function -> Selecionar a função Lambda criada -> Save




- Actions -> Deploy API -> Deployment Stage -> New Stage [nome para o deploy] -> Deploy




### No POSTMAN

- Add Request -> Method POST -> Copiar o endpoint gerado no API Gateway
- Body -> Raw -> JSON -> Adicionar o seguinte body
- Send




### No Amazon Cognito
- Step 1:
  - Cognito Dashboard -> User Pools -> Create a User Pool -> Select: Cognito User Pool and Email -> Next



- step 2:

  - Select: Cognito defaults, No MFA, Enable Self-service account recovery and Email only
  - Next



- Step 3:

  - Only default, click in Next.



- Step 4:

  - Select: Send email with Cognito.
  - Next



- Step 5:

  - [name user pool]
  - select: use the Cognito Hosted UI
  - select: use a Cognito domain
  - [name for prefix domain]



  - Select: public client
  - [app client name]
  - select: Generate a client secret
  - in URL: https://example.com



  - in Advanced app cliente Settings:
    - Select: ALLOW_CUSTOM_AUTH, ALLOW_USER_SRP_AUTH, ALLOW_REFRESH_TOKEN_AUTH
    - Select: Authorization code grant and implicit grant
    - Select: OpenID and Email
    - Click in Next.


  - Create user pool

### Criando um autorizador do Amazon Cognito para uma API REST no Amazon API Gateway

- API Gateway Dashboard -> Selecionar a API criada -> Authorizers -> Create New Authorizer
- Name [CognitoAuth] -> Type - Cognito -> Cognito User Pool [pool criada anteriormente] -> Token Source [Authorization]



- Resources -> selecionar o resource criado -> selecionar o método criado -> Method Request -> Authorization - Selecionar o autorizador criado



- Caso não apareça o authorization criado faça um refresh na página.
- Salve não esqueça de realizar o deploy API novamente.



### No POSTMAN

- Add request -> Authorization
- Type - OAuth 2.0
- Callback URL [https://example.com/]
- Auth URL [a url criada no cognito]

- Client ID - obter o Client ID do Cognito em App clients


- Scope [email - openid] "deve ser igual ao cognito"
- Client Authentication [Send client credentials in body]
- Get New Acces Token



- Se tudo der certo irá abrir um popup para criar um usuário  e senha conforme a imagem abaixo:



- Crie um usuário e senha e sign up

- Abra o email e acesse o link para confirmar.



- Clique em confirme no popup e digite o email e senha criado para logar.



- Copiar o token gerado



- Agora vamos realizar um teste tentando inserir um item no dynamoDB com as devidas credenciais.

- Em Post Item -> Body -> Altere o id e price.



- Selecionar -> Authorization -> Type - Bearer Token -> Inserir o token copiado
- Send



* **Observe que o item foi inserido com sucesso!**

- Agora vamos realizar um teste sem estar logado no cognito.

- Selecionar -> Authorization -> Type -> No Auth



- Em Body altere o JSON e clique em Send.



* **Observer que o item não foi inserido com sucesso!**

## Conclusão

O serviço Amazon Cognito permitiu a criação de uma API REST segura e com autorização, possibilitando a inserção de novos registros no DynamoDB apenas por usuários autenticados. Essa solução pode ser aplicada em diversos cenários de aplicações web e mobile, possibilitando um controle de acesso seguro e escalável para as aplicações.


