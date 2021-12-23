# spring-boot-weblogic-example
Um exemplo de aplicativo de inicialização rápida pronto para ser implantado no weblogic (testado em 12c).

Este é um aplicativo bastante minimalista, mas já possui alguns princípios básicos:
- xsd file to define contract
- serviço simulado que fornece endpoint de soap, utilizando classes geradas de xsd
- spring-boot and maven based build

**How to use:**

1) Execute **mvn clean package**
2) Implantar resultando **.war** arquivo no weblogic
3) Estás pronto! Você já pode enviar um **POST** request.xml para **http://localhost:7001/mockservice** (desde que você tenha uma instância do weblogic em execução localmente)

![alt text](https://github.com/isaccanedo/spring-boot-weblogic/blob/master/weblogic.png "Deployed into weblogic")

**POST-ing da linha de comando (requires curl)**

```
[gyuszi@gyuszidev spring-boot-weblogic-example]$ curl -X POST -H "Content-Type: text/xml" --data-binary @request.xml http://localhost:7001/mockservice
<env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/">
    <env:Header/>
    <env:Body>
        <ResponseStructure xmlns="http://isaccanedo.br">
            <rsuid>errer2323sdfsdf</rsuid>
            <message>Hello from example app!</message>
        </ResponseStructure>
    </env:Body>
</env:Envelope>
[gyuszi@gyuszidev spring-boot-weblogic-example]$
```

The heart of our mini appliction is this soap endpoint.

```
    @PayloadRoot(namespace = "http://isaccanedo.br", localPart = "RequestStructure")
    @ResponsePayload
    public ResponseStructure responseStructure(@RequestPayload RequestStructure request) {
        return new ResponseStructure() {{
            setRsuid(request.getRquid());
            setMessage("Hello from example app!");
        }};
    }
```

Observe que este aplicativo não usa nenhum Java EE/EJB API. Não usa JAX-WS, mas ao invés Spring-WS.
Weblogic will not list anything if you open the detail of your deployment in the web console (REST/WEB services tab will be empty)
