# Teste de captura para nova interface.

A interface antiga passa a ser somente de input e uma nova interface foi gerada para output.

## Interfaces disponíveis:

Adquiridas com `sudo ifconfig`
```
- enp6s0f0
- enp6s0f1
- ens2f0
- ens2f1
- lo
```
A interface antiga é `enp6s0f1`.
A partir disso é esperado que ` enp6s0f0` seja a nova interface de output.

## Comando de captura com tcpdump:
```
sudo tcpdump -i "$INTERFACE" -l -U -vvv -n -tttt -c 10000 "$DNS" or "$WEB" > "$current_directory/data/$FILE"
```
Onde:
1. "$INTERFACE" é o id da mesma.
2. "$DNS" e "$WEB" representam o tipo de mensagem capturada.
3. "$FILE" e o nome do arquivo de saída.
   - F0: t_f0_20210602_124437.txt
   - F1: t_f1_20210602_124748.txt

## Conferindo os arquivos obtidos:

Para conferir os arquivos foi feita uma busca por mensagens enviadas ou recebidas pelo IP `8.8.8.8` externo a rede.

O arquivo F1 possou duas ocorrências deste IP como remetente e nenhuma como destinatário.
```
8.8.8.8.53 > 200.130.15.191.53: [udp sum ok] 47646 q: A? star-mini.c10r.facebook.com. 1/0/1 star-mini.c10r.facebook.com. [59s] A 31.13.85.36 ar: . OPT UDPsize=512 DO (72)

8.8.8.8.53 > 200.130.15.191.53: [udp sum ok] 10977 q: A? scontent.fbsb9-1.fna.fbcdn.net. 1/0/1 scontent.fbsb9-1.fna.fbcdn.net. [50s] A 200.143.247.209 ar: . OPT UDPsize=512 DO (75)
```
Caracterizando-o como interface de input.

O arquivo F0 possou diversas ocorrências deste IP como destinatário e nenhuma como remetente.
```
200.130.1.38.15229 > 8.8.8.8.53: [udp sum ok] 54214+ Type65? media-gig2-1.cdn.whatsapp.net. (47)

200.130.1.38.42457 > 8.8.8.8.53: [udp sum ok] 56456+ A? media-gig2-1.cdn.whatsapp.net. (47)
```
Caracterizando-o como interface de output.
