# DNS TCPDUMP
Arthur A. Vianna(Autor)

Nilson L. Damasceno(Revisão)

10/05/2021

## Objetivo
Este documento tem como objetivo sintetizar as informações a respeito do padrão utilizado pelo TCPDump em requisições e respostas DNS.

## Padrão

### Requisição
``` src > dst: id op? flags qtype qclass name (len) ```

* src: ip de origem.
* dst: ip de destino.
* id: query id(um par requisição resposta possui o mesmo query id). Este campo pode ser acompanhado de "+" ou "%".
* * id+: DNS RECURSION DESIRED
* * id%: DNS CHECKING DISABLED
* op?: -
* flags: corresponde a anomalias encontradas pelo TCPdump, são colocadas entre colchetes, seus valores podem ser: [xau], [xa] e [xn], onde x é um contador.
* * [au]: additional records, indica que há uma anomalia na seção de registros adicionais da query.
* * [a]: answer, indica que há uma anomalia na seção de resposta da query.
* * [n]: nameserver, indica que há uma anomalia na seção de nameservers da query.
* qtype: A?, AAAA?, TXT?, ...
* qclass: IN, CHAOS, HS, ANY
* * IN: the arpa internet
* * CHAOS: for chaos net (MIT)
* * HS: for Hesiod name server (MIT) (XXX)
* * ANY: wildcard match
* name: hostname que se deseja descobrir o ip.
* (len): query length.

### Resposta
``` src > dst: id op rcode flags a/n/au type class data (len) ```

* id: query id(um par requisição resposta possui o mesmo query id). Este campo pode ser acompanhado de "*", "-", "|" e "$".
* * id*: AUTHORITATIVE ANSWER
* * id-: RECURSION AVAIABLE
* * id|: TRUNCATED MESSAGGE
* * id$: AUTHENTIC DATA FROM NAMED
* op: -
* rcode: response code, campo usado para exibir erros como, FormErr, NXDomain, Refused, NotAuth, ...
* flags a/n/au
* * a número de registros de resposta
* * u número de registros name server
* * au número de registros adicionais
* type: A, AAAA, TXT, ...

## Exemplos

Os exemplos a seguir foram gerados utilizando o comando:

```Shell
#!/bin/sh
FILE=`date +dns_web_%Y%m%d_%H%M%S.txt`
current_directory=`pwd`
INTERFACE="enp6s0f1"
N=10000
DNS="udp port 53"
WEB="tcp dst port 80 or tcp dst port 443"

sudo tcpdump -i "$INTERFACE" -l -U -vvv -n -tttt -c "$N" "$DNS" or "$WEB" > "$current_directory/data/$FILE"
```

### Requisição Exemplo:
``` 172.253.230.3.38803 > 200.130.11.91.53: [udp sum ok] 40502% [1au] A? correio.fiocruz.br. ar: . OPT UDPsize=4096 DO (47) ```

Neste exemplo, além dos campos previstos no padrão do TCPDump, temos o [udp sum ok], que nos diz que o pacote não foi corrompido.

No request do exemplo, 172.253.230.3 pergunta pelo IPV4 de correio.fiocruz.br, no id nota-se o "%" que é DNS CHECKING DISABLED, temos também a flag [1au].

### Resposta Exemplo:
``` 200.130.11.91.53 > 172.253.230.3.38803: [udp sum ok] 40502*- q: A? correio.fiocruz.br. 3/3/4 correio.fiocruz.br. [1h] A 157.86.151.22, correio.fiocruz.br. [1h] A 157.86.151.20, correio.fiocruz.br. [1h] A 157.86.151.21 ns: fiocruz.br. [1h] NS ns3.fiocruz.br., fiocruz.br. [1h] NS ns2.fiocruz.br., fiocruz.br. [1h] NS ns1.fiocruz.br. ar: ns1.fiocruz.br. [1h] A 157.86.18.17, ns2.fiocruz.br. [1h] A 157.86.18.10, ns3.fiocruz.br. [1h] A 200.130.11.91, . OPT UDPsize=4096 DO (197) ```

Neste exemplo, temos novamente o [udp sum ok], no response de exemplo, o id está acompanhado de "*" e "-", que diz que o servidor que enviou a resposta é um servidor autoritativo e que DNS recursivo está disponível.

O campo com valor 3/3/4 significa que a resposta contém 3 registros de resposta para a pergunta "A? correio.fiocruz.br.", 3 registros de name server que conseguem responder a essa pergunta e 4 registros adicionais. Em seguida temos, na ordem, os 3 ips de resposta para a pergunta, cada um precedido pela validade daquela resposta, no exemplo [1h] que significa 1 hora, em seguida o nome dos 3 servidores conhecidos capazes de responder a pergunta e após o "ar:"(additional records) o nome e o ip dos 3 servidores que também respondem a pergunta.

Note que, o servidor que respondeu a pergunta é o ns3.fiocruz.br, ele colocou a si mesmo na lista.
