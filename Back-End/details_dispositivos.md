**Página : https://docs.stg.eugenio.io/information**

- Dispositivos Logicalis:

    Há duas coisas a serem tentadas antes de ligar o gateway:
    {
        *As antenas estão cada uma corretamente conectadas nos respectivos soquetes no gateway.*
        *A conexão do cubo com o gateway está correta.*
    }

- Antenas
    Existem três tipos de antenas que você pode conectar:
    {
        *A antena do modem (ou 3G / 4G), responsável pelo sinal para receber a conexão do celular, e depois se conectar à internet (consequentemente, ao Portal Eugenio).*
        *A antena WiFi, por meio da qual o gateway pode habilitar o host de conexão sem fio.*
        *A antena GPS, usada para análise de localização do gateway.*
    }

    # Você pode verificar se está registrado corretamente pelo Portal Eugenio, na seção Lista de Dispositivos (aba Gateways).
    # Em caso de sucesso, o Gateway será listado (mesmo se estiver desconectado).

- Instalação Física
    # Para permitir que seu dispositivo se conecte a Eugenio, você deve registrá-lo 
    # Seguindo as etapas abaixo:
    {
        *Faça login na GUI de Eugenio*
        *Crie o esquema de dados do dispositivo (mais informações em Definindo um Esquema)*
        *Crie o dispositivo(mais informações para dispositivos genéricos ou Eugenio são explicados abaixo em Registrar dispositivo)*
    }

- Registrar dispositivo
    # A próxima etapa no processo de envio de mensagens do aparelho é fazer o cadastro no corretor.

    # Para dispositivos genéricos, existem quatro etapas a serem seguidas para que o dispositivo seja criado e registrado:
    {
        *Na interface Eugenio, acesse o menu Dispositivos.*
        *Vá para a guia Coisas.*
        *Crie um dispositivo, definindo a impressão digital do seu "certificado de cliente" que você salvou anteriormente, nome e descrição.*
        *Após a criação, você precisa baixar o certificado CA e salvar o ID, host, porta e deviceToCloud. Eles serão usados ​​posteriormente para configurar o Cliente MQTT.*
    }
    # Para dispositivos Eugenio, os requisitos e comandos são especificados "API de gerenciamento de dispositivos".

    # Quando um dispositivo Eugenio é registrado com sucesso, o corpo da resposta JSON da solicitação POST é composto por uma seção mqtt que possui os seguintes componentes:
    {
        *"host": o endereço do corretor MQTT.*
        *"porta": a porta do MQTT.*
        *"deviceToCloud": tópico p/ publicar mensagens para o corretor.*
    }

    # Você precisará desses elementos para conectar o dispositivo à nuvem Eugenio.

- Dispositivo real : Conexão MQTT
    # Os exemplos a seguir mostram como configurar um cliente MQTT:

    [
        import paho.mqtt.client as mqtt
        import ssl

        ca_cert  ''
        device_cert = ''
        device_key = ''
        device_id = '<DEVICE ID>'
        username = 'EUGENIO_IOT_HUB/' + device_id + '/api-version=2017-06-30'
        password = None

        client = mqtt.Client(device_id, mqtt.MQTTv311)

        client.username_pw_set(username, password)

        client.tls_set(ca_certs=ca_cert, certfile=device_cert, keyfile=device_key, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)

        client.tls_insecure_set(False)
    ]

    # Eugenio suporta apenas autenticação X509 por meio de conexão MQTT segura (geralmente porta 8883 / tcp).

    # Para se conectar usando X509, você precisará dos seguintes arquivos:
    {
        *Arquivo CA (CA.cert por exemplo): obtido na resposta ao registrar o novo dispositivo.*
        *Certificado de cliente (device.cert por exemplo): deve ser gerado / provedor pelo desenvolvedor.*
        *Chave do cliente (device.key por exemplo): igual à anterior.*
    }

    # Existem três pontos importantes a saber:
    {
        *A username seguir o esquema "EUGENIO_IOT_HUB // api-version = 2017-06-30 ", onde <DEVICE_ID> era o dispositivo registrado na nuvem Eugenio*
        *O passworddeve estar vazio O SSL / TLS usa o protocolo "TLSv1".*
    }

# Na guia Publicar, no primeiro campo, insira o tópico deviceToCloud que você anotou anteriormente (por exemplo, devices / mqttfx_device / messages / events /). Em Payload, digite o seguinte JSON:

# Substitua o 'carimbo de data e hora' e o 'valor int' pelos seus dados
    [
        {
        "schema": "mqttfx_schema",
        "payload": {
            "event_date": <timestamp>,
            "event_value": <int value>
            }
        }
    ]

- Troca de Mensagens
    # Mensagens de dispositivo para nuvem
    # Existem dois tipos de eventos que um dispositivo precisa lidar:

    # events: que são mensagens ou qualquer tipo de dado de telemetria que os dispositivos enviam para a nuvem
    # commands: bruxa são mensagens recebidas da nuvem para o dispositivo. Normalmente são comandos que obrigam o dispositivo a fazer alguma tarefa.

    --Enviando eventos--

Os exemplos a seguir mostram como publicar um evento:
    [
        import json

        topic = '<deviceToCloud parameter>'

        message = {
            "schema": "my_device_door",
            "payload": {
                "ls": 30,
                "rs": 50
                }
            }
        client.publish(topic, json.dumps(message))
    ]

    {
        *Após o registro do dispositivo Eugenio usando a API HTTP, obtivemos do corpo da resposta* JSON um parâmetro chamado 'deviceToCloud'. Este parâmetro deve ser usado como tópico MQTT para publicar mensagens do dispositivo para a nuvem.*
        *Para publicar, você precisará do tópico (retornado no cadastro do dispositivo) e da mensagem (lembrando que deve seguir o esquema). Você pode ver um exemplo de publicação na nuvem.*
    }
