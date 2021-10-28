# Redes e IP

## Classes de endereços

| Classes \ bits | 0 | 7 | 15 | 23 | 31 |
| --- | :--- | :--- | :--- | :--- | :--- |
| **A** | 0  netid | >hostid |||>
| **B** | 10 netid || > hostid ||>
| **C** | 110 netid |||> hostid | >
| **D** | 1110 endereço multicast |||| >
| **E** | 1111 reservado para utilização futura |||| >

- Endereços IPv4 são de 4 bytes
- Classes **A**, **B** e **C** podem ser atribuidas a terminais, são **unicast**
- **netid**: identifica a subnet de IP
- **hostid**: identifica o host nessa subnet de IP
- **Unicast**: apenas 1 host
- **Multicast**: multiplos hosts

### O número máximo de redes é é dado pela gama total do **netid** desse IP

| Classes | Nº bits netid | Gama total |
| --- | --- | --- |
| **A** | 7 | 128 |
| **B** | 14 | 16.384 |
| **C** | 21 | 2.097.152 |

### O número máximo de hosts é é dado pela gama total do **hostid** desse IP

| Classes | Nº bits hostid | Gama total |
| --- | --- | --- |
| **A** | 24 | 16.777.216 |
| **B** | 16 | 65.536 |
| **C** | 8 | 256 |
---

## Endereços IP reservados

| Classes | 0 | 7 | 15 | 31
| --- | --- | --- | --- | --- |
| **Este Host**[^1] | 0 |||>
| **host NESTA REDE**[^1] | 0 ||>host|>
| **BROADCAST LOCAL**[^2] | 1 |||>
| **BROADCAST DIRIGIDO para net**[^2] | net ||> 1|>
| **LOOPBACK**[^3] | 127 |> qualquer (em geral 1)||>
| **ESTA net**[^4] | net ||> 0 |>

[^1]: Permitido apenas na inicialização; nunca é endereço destion válido
[^2]: Nunca é endereço origem válido
[^3]: Nunca deve aparecer na rede
[^4]: Reservado para designar a rede
---

## Notação em decimal e gamas

| Classes | limite inferior | limite superior |
| --- | --- | --- |
| **A** | 1.0.0.0 | 126.0.0.0 |
| **B** | 127.0.0.0 | 191.255.0.0 |
| **C** | 192.0.0.0 | 223.255.255.0 |
| **D** | 224.0.0.0 | 239.255.255.255 |
| **E** | 240.0.0.0 | 255.255.255.254 |
---

## Máscaras

- Inicialmente os tamanhos do netid e do hostid tinham tamanho fixo correspondente a classe a que pertenciam
- Chegousse á conclusão que o numero de endereços da subnet da classe A era demasiado grande e da classe C demasiado pequeno
- A **netmask** é composta por **4 bytes** e é sempre constituida por uma sequência de 1s e depois uma sequência de 0s
- Os 1s definem o **netid** e os 0s o **hostid**
- Para alem de dar possibilidade de escolher o tamanho do netid e do hostid, cada host usa a máscara para determinar o IP da subnet onde está ligado, fazendo um **bitwise AND entre o IP e a máscara**.

---

## Atribuição de endereços IP

### Regras

- Todas as interfaces ligadas na **mesma rede** têm de ter **netid igual** e **hostid diferente**
- Interfaces em redes **diferentes** têm de ter **netid diferente**

### Tipos de hosts

- **Single-homed host**: host com uma única interface;
- **Multi-homed host**: host com mais que uma inteface, mas estas não são capazes de trocar informação entre si;
- **Router**: São como os multi-homed hosts, mas estas conseguem trocar informação entre interfaces.

---

## Encapsulamento de IP Datagrams

- O protocolo IP envia informação em forma de datagrams.
- Cada bloco de bytes, o protocolo IP concatena um header, é esta concatenação que forma o **IP Datagram**.
- Cada IP Datagram é entre á camada **MAC** de baixo para ser enviado para a rede.
- Em cada rede a **MAC layer frame** é composta por um campo **header** e por um campo **data**.
- No campo **data** do MAC é encapsulado o **IP Datagram**.

> Continuar na página 10
