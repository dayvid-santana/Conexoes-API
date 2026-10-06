**SOAP** (*Simple Object Access Protocol*) é um protocolo para comunicação entre sistemas por meio de **mensagens XML**, muito comum em integrações corporativas e sistemas legados.

Apesar do nome dizer “Simple”, na prática ele é mais estruturado e rígido que APIs REST.

### A ideia central

Imagine dois sistemas:

```text
Sistema Java
    │
    │  SOAP Request (XML)
    ▼
┌─────────────────┐
│ Web Service SOAP│
└─────────────────┘
    │
    │  SOAP Response (XML)
    ▼
Sistema Java
```

Por exemplo, você quer consultar um cliente. Em REST, poderia ser:

```http
GET /clientes/123
```

Em SOAP, normalmente você envia um `POST` contendo um documento XML:

```xml
<soap:Envelope
    xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">

    <soap:Body>
        <ConsultarCliente>
            <id>123</id>
        </ConsultarCliente>
    </soap:Body>

</soap:Envelope>
```

O servidor pode responder:

```xml
<soap:Envelope
    xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">

    <soap:Body>
        <ConsultarClienteResponse>
            <cliente>
                <id>123</id>
                <nome>João</nome>
                <ativo>true</ativo>
            </cliente>
        </ConsultarClienteResponse>
    </soap:Body>

</soap:Envelope>
```

A estrutura básica de uma mensagem SOAP é:

```text
Envelope
├── Header       ← opcional
│   ├── autenticação
│   ├── segurança
│   └── metadados
│
└── Body
    └── operação + dados
```

O **Envelope** identifica a mensagem como SOAP. O **Header** pode carregar autenticação, tokens, informações de segurança etc. O **Body** contém aquilo que você realmente quer executar.

Um conceito particularmente importante é o **WSDL** (*Web Services Description Language*). Pense nele como o **contrato formal da API SOAP**. Ele descreve quais operações existem, quais parâmetros cada operação recebe, quais tipos de dados são usados, o que será retornado e onde o serviço está localizado.

Por exemplo, conceitualmente:

```text
WSDL
│
├── consultarCliente(id)
│       └── retorna Cliente
│
├── cadastrarCliente(cliente)
│       └── retorna Resultado
│
└── excluirCliente(id)
        └── retorna Resultado
```

Isso permite que ferramentas gerem código automaticamente. Em Java, por exemplo, você pode receber um WSDL e gerar classes que permitem trabalhar aproximadamente assim:

```java
Cliente cliente = service.consultarCliente(123);
```

enquanto a biblioteca cuida da criação do XML, HTTP, parsing da resposta etc.

### SOAP × REST

A diferença conceitual é importante:

| SOAP | REST |
|---|---|
| Protocolo | Estilo arquitetural |
| Normalmente XML | Geralmente JSON |
| Forte uso de contratos/WSDL | Frequentemente OpenAPI |
| Operações | Recursos |
| Mais rígido | Mais flexível |
| Muito comum em sistemas corporativos antigos | Dominante em APIs web modernas |

Por exemplo:

**REST**

```http
GET /clientes/123
```

Você está dizendo:

> “Quero o recurso cliente 123.”

**SOAP**

```xml
<ConsultarCliente>
    <id>123</id>
</ConsultarCliente>
```

Você está dizendo:

> “Execute a operação ConsultarCliente com o argumento 123.”

Essa diferença entre **recursos** e **operações** ajuda bastante a entender as duas abordagens.

SOAP também possui um ecossistema de especificações chamado **WS-\***, como WS-Security, WS-Addressing e WS-ReliableMessaging. Por isso ele ainda aparece bastante em bancos, ERPs, governos, telecomunicações e integrações empresariais que precisam de contratos e requisitos de segurança mais rígidos.

Para desenvolvimento backend, eu estudaria SOAP nesta sequência:

```text
HTTP
 ↓
XML
 ↓
SOAP Envelope
 ↓
SOAP Header / Body / Fault
 ↓
WSDL
 ↓
XSD
 ↓
WS-Security
 ↓
Cliente SOAP em Java/Python
```

O próximo conceito que vale entender é **WSDL + XSD**, porque é aí que SOAP começa a ficar realmente diferente de simplesmente “mandar XML por HTTP”.
