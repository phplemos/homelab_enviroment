# homelab_enviroment

### Pre instalação

1. Destrói o shim antigo (com config errada)
```sudo ip link delete macvlan-shim```

2. Cria o novo shim
```sudo ip link add macvlan-shim link eno1 type macvlan mode bridge```

3. Dá um IP para a ponte (Vamos usar o .11, já que o Pihole é .10) O .11 está dentro daquele range /29 que calculamos, então é seguro.
```sudo ip addr add 192.168.1.11/32 dev macvlan-shim```

4. Levanta a interface
```sudo ip link set macvlan-shim up```

5. Cria a rota para o NOVO IP do Pi-hole
```
sudo ip route add 192.168.1.10/32 dev macvlan-shim
```

TODO: Parei tentando apontar o