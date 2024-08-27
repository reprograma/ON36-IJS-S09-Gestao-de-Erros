<h1 align="center">
  <img src="assets/reprograma-fundos-claros.png" alt="logo reprograma" width="500">
</h1>

# Gestão de erros

Turma Online XX - Imersão JavaScript | Semana XX | 20XX | Professora Rayane Pimentel

### Objetivo

- Breve apresentação do conteúdo e dos objetivos da aula.
- Importância da gestão de erros e segurança em APIs.

### Resumo

O que veremos na aula de hoje?

- [Gestão de erros em APIs](#gestão-de-erros-em-apis)

  - [Introdução](#introdução)

- [Conteúdo](#conteúdo)

  - [Tipos de Erros em APIs](#tipos-de-erros-em-apis)

    - [Erros do cliente]()
    - [Erros do servidor]()
    - [Erros de validação]()

  - [Estratégias de Gestão de Erros]()

    - [Padrões de Resposta]()
    - [Middleware de Tratamento de Erro]()
    - [Log de Erros]()
    - [Monitoramento e Alertas]()
    - [Segurança na Gestão de Erros]()

  - [Segurança]()

    - [Autenticação e autorização]()
    - [Testes de Segurança]()

  - [Exercícios](#exercícios)
  - [Material da aula](#material-da-aula)
  - [Links Úteis](#links-úteis)

# Gestão de erros em APIs

## Introdução

Nessa aula iremos ver importância da gestão de erros em APIs e como ela impacta a experiência do usuário, manutenção do código e a segurança da aplicação.

## Conteúdo

### 1. Tipos de Erros em APIs

#### 1.1 Erros do cliente:

Erros do lado do cliente ocorrem quando há problemas na requisição feita ao servidor, como requisições malformadas ou falta de autorização para acessar um recurso.

- **HTTP 400** (Bad Request): Indica que a requisição está malformada ou contém dados inválidos.
  - Exemplo: Um usuário envia uma requisição com uma URL contendo caracteres especiais que não são permitidos, ou um payload JSON inválido.

<img src="./assets/erro-cliente.png">

- **HTTP 404**: Ocorre quando o recurso solicitado não é encontrado no servidor.

  - "Erro 404: Pagina não encontrada" quando o usuário digita uma URL incorreta que não existe.

  ![Alt text](./assets/erro-cliente-404.png)

- **HTTP 403** (Forbidden): Indica que o usuário não tem permissão para acessar o recurso solicitado, mesmo que esteja autenticado.

  - Exemplo: O servidor entende a requisição, mas o usuário não tem autorização para acessá-la, como tentar acessar uma página restrita.

  ![Alt text](./assets/erro-cliente-403.png)

#### 1.2 Erros do Servidor:

Erros do lado do servidor acontecem quando há um problema ao processar a requisição do cliente. Geralmente, esses erros indicam falhas no código, no banco de dados ou no próprio servidor.

- **HTTP 500** (Internal Server Error): Indica que o servidor encontrou uma condição inesperada que o impediu de atender a requisição.
  - Exemplo: Ocorre quando há uma exceção não tratada no código, como uma falha ao conectar ao banco de dados.

![Alt text](./assets/erro-servidor-500.png)

- **HTTP 503** (Service Unavailable): Ocorre quando o servidor não está disponível para processar a requisição, geralmente por estar em manutenção ou sobrecarregado.

  - Exemplo: Um servidor de banco de dados está offline ou o serviço está temporariamente fora do ar.

- **HTTP 502** (Bad Gateway): Indica que um servidor, atuando como gateway ou proxy, recebeu uma resposta inválida de outro servidor ao tentar atender a requisição.
  - Exemplo: Um serviço intermediário como um load balancer não conseguiu se comunicar com o servidor de aplicação.

#### 1.3 Erros de Validação:

Erros de validação ocorrem quando os dados enviados pelo cliente não atendem aos requisitos ou regras definidas pela aplicação.

- **HTTP 422** (Unprocessable Entity): Indica que o servidor entendeu a requisição, mas não conseguiu processá-la porque os dados fornecidos são semanticamente incorretos, mesmo que estejam tecnicamente bem formatados.

  ![Alt text](./assets/erro-validacao-422.png)

### 2. Estratégias de Gestão de Erros

Vamos discutir e explorar diferentes abordagens para lidar com erros e problemas que podem ocorrer em uma API. O objetivo é fornecer estratégias e técnicas para detectar, tratar e prevenir erros, garantindo que os problemas sejam rapidamente identificados e resolvidos, minimizando o impacto nos usuários e no negócio.


#### 2.1 Padrões de Respostas

Definir padrões claros de resposta para diferentes tipos de erros, incluindo status HTTP, mensagens de erro e estrutura de dados.

##### 2.1.1 Uso de Códigos de Status HTTP Corretos

Os códigos de status de resposta HTTP indicam se uma solicitação HTTP específica foi concluída com êxito. As respostas são agrupadas em cinco classes:

- _1xx (Informacional)_: Indica que a solicitação foi recebida e está sendo processada.
- _2xx (Sucesso)_: A solicitação foi bem-sucedida.
- _3xx (Redirecionamento)_: A ação adicional é necessária para completar a solicitação.
- _4xx (Erro do Cliente)_: Problemas com a solicitação do cliente.
- _5xx (Erro do Servidor_): Problemas no processamento da solicitação pelo servidor.

Exemplos Comuns:

    200 OK: Solicitação bem-sucedida.
    404 Not Found: Recurso não encontrado.
    500 Internal Server Error: Erro interno no servidor.

```json
{
  "error": {
    "code": 400,
    "message": "Campo 'nome' é obrigatório"
  }
}
```

###### Boas Práticas

- Evite detalhes desnecessários nas mensagens de erro. Em vez disso, use mensagens de erro genéricas para evitar a exposição de informações sensíveis.

Exemplo do que não fazer:

![Alt text](./assets/msn.png)

- Utilize códigos de status de erro adequados. Nunca use um status de sucesso para indicar um erro.

Exemplo de erro não claro:

`Campo é obrigatório`, não indicando ao usuário qual é o campo obrigatório.

```json
{
  "error": {
    "code": 400,
    "message": "Campo é obrigatório"
  }
}
```

Exemplo melhorado:

```json
{
  "error": {
    "code": 400,
    "message": "O campo 'nome' é obrigatório."
  }
}
```
Aqui, a mensagem de erro foi refinada para ser mais clara e específica, indicando exatamente qual campo está faltando.

##### 2.1.2 Estruturação de Mensagens de Erro utilizando RFC 7807

O [RFC 7807](https://datatracker.ietf.org/doc/html/rfc7807) fornece uma maneira padronizada de estruturar mensagens de erro em APIs, promovendo consistência e clareza. Usando essa abordagem, você pode criar respostas de erro que são compreensíveis para clientes e fáceis de depurar.

Principais Requisitos do RFC 7807:

- Utilize códigos de status HTTP adequados, geralmente entre 400 e 500, para representar erros.
- O header `Content-Type` deve ser `application/problem+json` ou `application/problem+xml` se estiver usando XML.

```json
application/problem+json
application/problem+xml
```

###### Estrutura Básica de uma Mensagem de Erro

- **Title**: um breve resumo do tipo de problema.
- **Detail**: uma descrição detalhada do problema.
- **Type**: uma URL que descreve o tipo do problema.
- **Status**: o status HTTP gerado pelo servidor.
- **Instance**: (opcional) um URI exclusivo para o erro específico, apontando para um log ou outra fonte de informação relevante.

Exemplo:

```json
HTTP/1.1 404 Not Found
Content-Type: application/problem+json
Content-Language: en

{
  "type": "https://bookstore.example.com/problems/book-not-found",
  "title": "Book Not Found",
  "status": 404,
  "detail": "The book with ID 12345 could not be found in our database.",
  "instance": "/api/books/12345",
  "custom-field": "Additional information or context here"
}
```

Em alguns casos, uma única solicitação pode resultar em múltiplos erros. O RFC 7807 permite lidar com essas situações através de extensões personalizadas que podem ser adicionadas à mensagem de erro básica. Isso permite fornecer detalhes específicos sobre cada erro ocorrido.

Exemplo de Múltiplos Erros:

```json
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json

{
  "type": "https://example.com/problemas#validacao",
  "title": "Erro de Validação",
  "status": 400,
  "detail": "Um ou mais campos de entrada falharam na validação.",
  "instance": "/api/usuarios",
  "errors": [
    {
      "campo": "username",
      "erro": "Nome de usuário já existente."
    },
    {
      "campo": "email",
      "erro": "Endereço de email inválido."
    }
  ]
}
```
Neste exemplo, a propriedade `errors` é uma extensão personalizada que contém uma lista de objetos, cada um descrevendo um erro específico ocorrido durante o processo de validação. Isso demonstra como o RFC 7807 pode ser utilizado para fornecer respostas detalhadas e informativas em casos de múltiplos erros facilitando a depuração e melhorando a experiência do usuário final.



#### 2.2 Middleware de Tratamento de Erros

Implementar middleware para capturar e tratar erros de forma centralizada, convertendo-os em respostas HTTP apropriadas.

**Middleware**

Vocês já pararam para pensar o que acontece no meio do caminho entre uma requisição e a resposta que recebemos em uma aplicação?

Essa camada de controle, que processa as requisições antes de chegarem ao destino final, é o que chamamos de middleware.

- Middlewares são blocos de código que são executados durante o processamento de uma requisição HTTP, `antes` de chegar ao manipulador de rota (controller).
- Eles seguem o padrão de design `Chain of Responsibility`, permitindo que várias operações sejam executadas em sequência.


**O que são Middlewares?**

Os Middlewares são funções que são executadas antes do manipulador de rota e são utilizadas para executar tarefas específicas no ciclo de solicitação. Essas funções podem ser declaradas em uma classe, utilizando o decorador `@Injectable()` e implementadas com a interface `NestMiddleware()`.

A função `use`, que é obrigatória na interface `NestMiddleware()`, tem acesso ao objeto `Request` e `Response`, bem como à função `next()`, que chama a próxima função middleware ou, eventualmente, o manipulador de rota, seguindo o padrão de design `Chain Of Responsibility`.


Uma característica importante dos middlewares é que eles são projetados para lidar exclusivamente com solicitações HTTP. Por isso, têm acesso aos objetos request e response, mas não ao contexto completo da aplicação.

**Usando a Analogia para simplificar**

Imagine que você está em um restaurante:

1. O Pedido (Request): Um cliente faz um pedido ao garçom.
2. Middleware 1 - Garçom: O garçom verifica se o pedido está correto e se o cliente pode fazer aquele pedido (autenticação). Se o pedido estiver errado ou o cliente não tiver permissão, o garçom pode cancelar o pedido (interromper o fluxo). Caso contrário, ele encaminha o pedido para a cozinha.
3. Middleware 2 - Cozinheiro: O cozinheiro recebe o pedido e prepara o prato, verificando se todos os ingredientes estão corretos (validação dos dados). Se algo estiver errado, ele pode pedir uma correção (modificar o request) ou preparar o prato conforme solicitado e passar para a próxima etapa.
4. Middleware 3 - Entrega: Finalmente, o prato é entregue ao cliente. Aqui, o garçom verifica se está tudo certo antes de servir (última modificação antes da resposta final).

Cada etapa do processo representa um middleware. Se em algum ponto houver um problema, o fluxo pode ser interrompido ou corrigido antes de seguir adiante. Se tudo correr bem, o pedido chega ao cliente (manipulador de rota), exatamente como solicitado.


**O que podemos executar dentro dos Middlewares?**

- Modificar o Request ou Response: Alterar cabeçalhos, adicionar dados ou modificar informações antes de enviá-las ao próximo middleware.
- Interromper o fluxo: Finalizar a resposta diretamente, se uma condição for atendida, como retornar um erro de autenticação.
- Executar validações ou autenticações: Verificar tokens de segurança, permissões de usuário, ou validar os dados recebidos.



##### 2.2.1 Exceções padrão do NestJS


As exceções são uma forma de indicar que algo `deu errado`. No NestJS, temos exceções padrão que podem ser utilizadas para tratar erros comuns, como erros de solicitação HTTP, erros de validação de dados, etc.

O NestJS fornece várias exceções padrão que podem ser utilizadas em seus projetos. Aqui estão algumas das mais comuns:

* `HttpException`: uma exceção genérica para erros de solicitação HTTP.
* `BadRequestException`: uma exceção para erros de solicitação com parâmetros inválidos(400).
* `NotFoundException`: uma exceção para recursos não encontrados(404).
* `UnauthorizedException`: uma exceção para acesso não autorizado.
* Outras: `ForbiddenException`, `UnauthorizedException`, [etc](https://docs.nestjs.com/exception-filters#built-in-http-exceptions).

##### 2.2.2 Filter de Exceções

Às vezes, você precisará criar uma exceção personalizada para tratar um erro específico. Por exemplo, se você estiver trabalhando com uma API de pagamento e quiser tratar erros de pagamento, você pode criar uma exceção personalizada chamada `PaymentException`.

```js
typescript
// payment.exception.ts
import { Injectable } from '@nestjs/common';
import { HttpException, HttpStatus } from '@nestjs/common';

@Injectable()
export class PaymentException extends HttpException {
  constructor(message: string) {
     super(message, HttpStatus.PAYMENT_REQUIRED); // Usando o status HTTP 402 - Payment Required
  }
}
```

Utilizando em uma controller:

```js
import { Controller, Post, Body } from '@nestjs/common';
import { PaymentException } from './payment.exception';

@Controller('payments')
export class PaymentsController {
  @Post()
  processPayment(@Body() paymentDetails: any) {
    // Simulação de uma falha de pagamento
    const paymentSuccessful = false; // Suponha que a transação falhou

    if (!paymentSuccessful) {
      throw new PaymentException('Payment failed due to insufficient funds');
    }

    return { message: 'Payment processed successfully' };
  }
}

```

##### 2.2.3 Interceptors

Interceptors são objetos que podem ser utilizados para interceptar solicitações e respostas em tempo real, permitindo que você execute código personalizado antes ou após a execução de um controller ou middleware.

Eles podem ser utilizados para transformar dados de saída, gerenciar erros de forma centralizada, fazer logging, modificar headers de resposta, entre outras funcionalidades.



#### 2.3 Log de Erros


O log de erros é um registro detalhado dos erros e exceções que ocorrem na aplicação. Isso permite que você:

* Identifique e resolva problemas rapidamente
* Análise padrões de erros e melhorie a segurança do aplicativo
* Realize treinamento de dados para melhorar a previsibilidade e detecção de erros

Para logs de produção, é comum utilizar ferramentas especializadas, como Winston, Logstash, ou serviços na nuvem como Elastic Stack ou Datadog.
Essas ferramentas permitem armazenar logs de maneira centralizada, aplicar filtros, fazer buscas complexas e gerar relatórios detalhados.


#### 2.4 Monitoramento e Alertas

Monitoramento envolve a coleta contínua de métricas e logs da sua aplicação para identificar comportamentos anômalos e problemas de performance.

Alertas permitem que você seja notificado imediatamente quando erros críticos ocorrem, garantindo que problemas sejam resolvidos antes que causem grandes impactos.

Existem várias ferramentas de monitoramento que você pode utilizar, como:

* Prometheus e Grafana para monitorar métricas de desempenho
* ELK Stack (Elasticsearch, Logstash, Kibana) para analisar logs e monitorar o aplicativo
* New Relic para monitorar o desempenho do aplicativo e identificar problemas

---

### Exercícios

- [Exercicio para sala](/exercicios/para-sala/)
- [Exercicio para casa](/exercicios/para-casa/)

### Material da aula

- [Material](/material)

### Links Úteis

- [Códigos de status de respostas HTTP](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Status)
-

<p align="center">
Desenvolvido com :purple_heart:  
</p>
