# Desafio de Projeto: Adicionando Segurança em APIs na AWS com Amazon Cognito

![img](images/arquitetura.png)

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

![img](images/api-gateway-create.png)

- Resources -> Actions -> Create Resource -> Resource Name [Items] -> Create Resource

![img](images/api-gateway-create-resources.png)


### No Amazon DynamoDB

- DynamoDB Dashboard -> Tables -> Create table -> Table name [Items] -> Partition key [id] -> Create table

![img](images/dynamoDB-create-table.png)


### No AWS Lambda

- Lambda Dashboard -> Create function -> Name [put_item_function] -> Create function

![img](images/lambda-create-function.png)

- Inserir código da função ```put_item_function.js``` disponível na pasta ```/src``` -> Deploy

![img](images/lambda-deploy-function.png)

- Configuration -> Permissions -> Execution role -> Abrir a Role no console do IAM

![img](images/lambda-configuration-permissions.png)

- IAM -> Permissions -> Add permissions -> create inline policy

![img](images/IAM-create-policy.png)


- Service - DynamoDB -> Manual actions -> add actions -> putItem

![img](images/IAM-create-policy-dynamodb.png)


- Resources -> Add arn -> Selecionar o arn da tabela criada no DynamoDB -> Add

![img](images/IAM-create-policy-dynamodb-arn.png)


- Review policy -> Name [crie o nome da policy] -> Create policy

![img](images/IAM-review-create-policy.png)



### Integrando o API Gateway com o Lambda backend

- API Gateway Dashboard -> Selecionar a API criada -> Resources -> Selecionar o resource criado -> Action -> Create method - POST

- Integration type -> Lambda function -> Use Lambda Proxy Integration -> Lambda function -> Selecionar a função Lambda criada -> Save

![img](images/api-gateway-create-post.png)


- Actions -> Deploy API -> Deployment Stage -> New Stage [nome para o deploy] -> Deploy

![img](images/api-gateway-deploy-request.png)


### No POSTMAN

- Add Request -> Method POST -> Copiar o endpoint gerado no API Gateway
- Body -> Raw -> JSON -> Adicionar o seguinte body
- Send

![img](images/insert-item-with-postman.png)

![img](images/insert-item-with-postman-ok.png)


### No Amazon Cognito
- Step 1:
  - Cognito Dashboard -> User Pools -> Create a User Pool -> Select: Cognito User Pool and Email -> Next

![img](images/cognito-userpool.png)

- step 2:

  - Select: Cognito defaults, No MFA, Enable Self-service account recovery and Email only
  - Next

![img](images/cognito-step2.1.png)

![img](images/cognito-step2.2.png)

- Step 3:

  - Only default, click in Next.

![img](images/cognito-step3.png)

- Step 4:

  - Select: Send email with Cognito.
  - Next

![img](images/cognito-step4.png)

- Step 5:

  - [name user pool]
  - select: use the Cognito Hosted UI
  - select: use a Cognito domain
  - [name for prefix domain]

![img](images/cognito-step5.1.png)

![img](images/cognito-step5.2.png)

  - Select: public client
  - [app client name]
  - select: Generate a client secret
  - in URL: https://example.com

![img](images/cognito-step5.3.png)

  - in Advanced app cliente Settings:
    - Select: ALLOW_CUSTOM_AUTH, ALLOW_USER_SRP_AUTH, ALLOW_REFRESH_TOKEN_AUTH
    - Select: Authorization code grant and implicit grant
    - Select: OpenID and Email
    - Click in Next.
![img](images/cognito-step5.4.png)

![img](images/cognito-step5.5.png)

  - Create user pool

### Criando um autorizador do Amazon Cognito para uma API REST no Amazon API Gateway

- API Gateway Dashboard -> Selecionar a API criada -> Authorizers -> Create New Authorizer
- Name [CognitoAuth] -> Type - Cognito -> Cognito User Pool [pool criada anteriormente] -> Token Source [Authorization]

![img](images/api-gateway-authorizer-cognito.png)

- Resources -> selecionar o resource criado -> selecionar o método criado -> Method Request -> Authorization - Selecionar o autorizador criado

![img](images/api-gateway-endpoint-authorization.png)

- Caso não apareça o authorization criado faça um refresh na página.
- Salve não esqueça de realizar o deploy API novamente.

![img](images/api-gateway-deploy-cognito-athorization.png)

### No POSTMAN

- Add request -> Authorization
- Type - OAuth 2.0
- Callback URL [https://example.com/]
- Auth URL [a url criada no cognito]
![img](images/cognito-url.png)
- Client ID - obter o Client ID do Cognito em App clients
![img](images/cognito-clientId.png)

- Scope [email - openid] "deve ser igual ao cognito"
- Client Authentication [Send client credentials in body]
- Get New Acces Token

![img](images/postman-config1.png)

![img](images/postman-config2.png)

- Se tudo der certo irá abrir um popup para criar um usuário  e senha conforme a imagem abaixo:

![img](images/postman-signup.png)

- Crie um usuário e senha e sign up

- Abra o email e acesse o link para confirmar.

![img](images/postman-signup-confirm-email.png)

- Clique em confirme no popup e digite o email e senha criado para logar.

![img](images/postman-signup-confirm.png)

![img](images/postman-signin-ok.png)

- Copiar o token gerado

![img](images/postman-token-generated.png)

- Agora vamos realizar um teste tentando inserir um item no dynamoDB com as devidas credenciais.

- Em Post Item -> Body -> Altere o id e price.

![img](images/postman-test-insert.png)

- Selecionar -> Authorization -> Type - Bearer Token -> Inserir o token copiado
- Send

![img](images/postman-authorization-withtoken.png)

* **Observe que o item foi inserido com sucesso!**

- Agora vamos realizar um teste sem estar logado no cognito.

- Selecionar -> Authorization -> Type -> No Auth

![img](images/postman-no-auth.png)

- Em Body altere o JSON e clique em Send.

![img](images/postman-no-authorization.png)

* **Observer que o item não foi inserido com sucesso!**

## Conclusão

O serviço Amazon Cognito permitiu a criação de uma API REST segura e com autorização, possibilitando a inserção de novos registros no DynamoDB apenas por usuários autenticados. Essa solução pode ser aplicada em diversos cenários de aplicações web e mobile, possibilitando um controle de acesso seguro e escalável para as aplicações.


