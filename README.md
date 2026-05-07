# MATA59-TrabalhoSemestral20261
# 🚀 Checklist de Homologação: HackInSDN

Bem-vindos à etapa prática! A transição da arquitetura desenhada no papel para a linha de comando exige muita atenção aos detalhes e aos fundamentos de redes. 

Utilize este checklist para homologar a infraestrutura da sua equipe antes da apresentação oficial. Marque as tarefas concluídas (`[x]`) conforme avança.

---

## 🏗️ 1. Mapeamento da Topologia e Contexto (A Fundação)

A topologia do laboratório é a nossa infraestrutura física imutável. O trabalho de vocês é dar uma identidade lógica a ela, baseada no cenário escolhido.

- [ ] O escopo da rede faz sentido para o seu caso de uso? 
- [ ] O diagrama lógico está devidamente documentado com os IPs, nomes fictícios e as portas de serviço que cada nó vai utilizar?
- [ ] A topologia original do laboratório foi estritamente mantida (sem nós extras ou links removidos)?

---

## 🌐 2. Camada de Aplicação (Serviços e Resolução de Nomes)

A internet não funciona à base de IPs memorizados. A exigência aqui é a implementação de um **serviço real** de resolução de nomes na rede de vocês.

- [ ] **Serviço de DNS:** Configuramos um daemon de DNS (como o `dnsmasq`, `bind9` ou similar) em um dos servidores para responder pelas consultas da rede?
  <details>
  <summary>💡 Dica de Validação</summary>
  
  > Testem a resolução a partir dos clientes usando ferramentas de consulta, como `dig [nome-do-servidor]` ou `nslookup`. Garantam que o arquivo `/etc/resolv.conf` dos clientes esteja apontando para o IP do novo servidor DNS de vocês.
  </details>

- [ ] Os dois serviços que o seu caso de uso vai prover estão devidamente instalados e em estado de escuta (`LISTEN`) nas portas corretas?
  <details>
  <summary>💡 Dica de Comando</summary>
  
  > Usem `ss -tulnp` ou `netstat -tuln` nos servidores para confirmar se as portas estão abertas antes de tentarem acessar pelo cliente.
  </details>

---

## 🚚 3. Camada de Transporte (Análise de Comportamento)

Esta etapa exige comprovação empírica em tela das características do TCP e do UDP .

- [ ] A equipe tem domínio sobre o uso de analisadores de pacotes em modo texto?
  <details>
  <summary>💡 Dica de Comando</summary>
  
  > Para escutar o tráfego de uma porta específica, o comando básico é `tcpdump -i any port [NUMERO_DA_PORTA] -n`.
  </details>

- [ ] **A Prova do TCP:** Conseguimos capturar o início de uma conexão e mostrar claramente o *3-way handshake* (pacotes `SYN`, `SYN-ACK`, `ACK`) ocorrendo antes da transferência dos dados da aplicação?
- [ ] **A Prova do UDP:** Conseguimos capturar uma carga de tráfego contínuo da sua aplicação e demonstrar na captura que o servidor despacha datagramas em massa, sem aguardar respostas de confirmação do receptor?

---

## 🛡️ 4. Camada de Rede (Roteamento e Filtragem)

Aqui testamos a segurança do perímetro e a segmentação interna. As regras devem ser baseadas na política de menor privilégio.

- [ ] O roteamento básico está funcionando? (Os pacotes conseguem ir de uma rede para a outra passando pelo roteador/firewall central?)
- [ ] **A Regra Externa:** A política para bloquear ou limitar o acesso vindo da internet foi aplicada corretamente na cadeia lógica apropriada do firewall (`FORWARD` ou `INPUT`)?
- [ ] **A Regra Interna (Atenção ao Modelo OSI):** A regra para isolamento dentro da rede interna está funcionando?
- [ ] Validamos as regras visualizando os contadores de pacotes do firewall?
  <details>
  <summary>💡 Dica de Comando</summary>
  
  > O comando `iptables -nvL` (ou `watch -n 1 iptables -nvL` para monitoramento em tempo real) mostra exatamente se a sua regra está capturando os pacotes ou se o tráfego está passando direto por outra rota.
  </details>

---

## 🔗 5. Camada de Enlace (Comutação Local)

A ponte entre o endereço lógico (IP) e o endereço físico da placa de rede (MAC).

- [ ] Após realizar um ping ou acessar um serviço de uma máquina na mesma rede local, a equipe consegue exibir a tabela ARP e explicar o que aconteceu nos bastidores?
  <details>
  <summary>💡 Dica de Comando</summary>
  
  > O comando tradicional é o `arp -n`, ou `ip neigh show` para sistemas Linux mais modernos. A equipe deve ser capaz de explicar por que o MAC Address de destino muda quando o pacote vai para a internet, mas permanece o do destino final quando a comunicação é na rede local.
  </details>

---
*Testem a exaustão. Derrubem conexões, analisem os logs e observem o comportamento dos pacotes. Uma demonstração de redes bem-sucedida é aquela em que a equipe sabe exatamente o caminho que a informação fez, desde o fio até a aplicação.*
