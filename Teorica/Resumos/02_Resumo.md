# Network Layer

## Index

- [Private Addresing](#private-addresing)
- [DHCP](#dhcp)
- [IPv6](#ipv6)

## Private Addressing

Para comunicar com a Internet, os endereços privados tem de ser **traduzidos** para endereços **públicos**

### NAT: Networl Address Translation

**Todos**  os datagramas **que saiam** de uma rede local  teem o **mesmo** **NAT IP address**, mas números de porta diferentes

#### Exemplo
- Host: 10.0.0.1
- Porta no host: 3345
- Router (LAN side): 10.0.04
- Router (WAN side): 138.76.29.7

Sequência de eventos:

1. **Host** envia um datagrama para **128.119.40.186, 80**
2. O **NAT Router** muda o **endereço origem** de 10.0.0.1 para **138.76.29.7, 5001** e coloca na coluna WAN da **NAT translation table** esse endereço associado ao 10.0.0.1, 3345 na coluna LAN
3. A resposta chega ao Router com destino de **138.76.29.7, 5001**
4. O Router procura na NAT Translation Table o endereço LAN correspondente e envia para ele a resposta

> continua no slide 68