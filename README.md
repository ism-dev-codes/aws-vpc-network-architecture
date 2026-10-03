# ☁️ Amazon VPC Architecture: Custom Network, Routing, NAT Gateway & Bastion Host

> ℹ️ **NOTE:** Este é o repositório desenvolvido por **Ismael Santos de Medeiros**.

Projeto com o objetivo de construir do zero uma infraestrutura de rede virtual privada e isolada (Amazon VPC) na nuvem AWS. O projeto abrange a criação de sub-redes públicas e privadas, configuração de tabelas de roteamento dedicadas, alocação de Internet Gateway para saída direta à internet, provisionamento de NAT Gateway com IP Elástico para acesso seguro à internet por recursos isolados, e a implementação de um servidor de salto (Bastion Host) para acesso SSH administrativo seguro.

---

## 💻 Tecnologias utilizadas no projeto

- [Amazon VPC](https://aws.amazon.com/vpc/) — Segmentação de rede privada com bloco IPv4 CIDR `10.0.0.0/16`
- [Sub-redes (Subnets)](https://docs.aws.amazon.com/vpc/latest/userguide/configure-subnets.html) — Divisão de rede entre `Subnet-Ismael-Public` (`10.0.0.0/24`) e `Subnet-Ismael-Private` (`10.0.2.0/23`)
- [Internet Gateway (IGW)](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) — Ponto de entrada/saída de tráfego público (`Ismael-Internet-Gateway`)
- [NAT Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) — Saída de internet para instâncias na sub-rede privada com IP Elástico alocado (`Ismael-NGW`)
- [Route Tables](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html) — Gerenciamento de rotas internas e rotas padrão (`0.0.0.0/0`) para IGW e NAT Gateway
- [Amazon EC2](https://aws.amazon.com/ec2/) — Lançamento do Bastion Host (`Ismael Bation Server`) e da instância de testes privada (`Ismael Private Instance`)
- [Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html) — Firewall virtual com regras de acesso SSH restritas por IP e por CIDR interno

---

## ✨ Como foi feito ?

- Provisionamento da `VPC-Ismael` utilizando o bloco CIDR manual `10.0.0.0/16` e habilitação das configurações de nomes de host DNS.
- Criação e segmentação das sub-redes: `Subnet-Ismael-Public` (`10.0.0.0/24`) com auto-atribuição de IP público habilitada e `Subnet-Ismael-Private` (`10.0.2.0/23`).
- Criação do `Ismael-Internet-Gateway` e associação explícita à `VPC-Ismael`.
- Criação e estruturação de duas tabelas de roteamento: `RT-Ismael-Public` (com rota `0.0.0.0/0` para o IGW) e `RT-Ismael-Private`.
- Associação das sub-redes às suas respectivas tabelas de rotas públicas e privadas.
- Provisionamento do `Ismael Bation Server` em sub-rede pública com Security Group autorizando SSH (porta 22) de qualquer origem (`0.0.0.0/0`).
- Lançamento do `Ismael-NGW` (NAT Gateway) em sub-rede pública com alocação e associação de um IP Elástico (EIP).
- Atualização da tabela `RT-Ismael-Private` adicionando a rota padrão `0.0.0.0/0` apontando para o NAT Gateway.
- Lançamento da `Ismael Private Instance` isolada em sub-rede privada sem IP público, com Security Group liberando SSH apenas para o bloco da VPC (`10.0.0.0/16`) e script `User Data` habilitando autenticação via senha.
- Acesso ao Bastion Host via EC2 Instance Connect e conexão SSH secundária em direção ao IP privado (`10.0.2.x`) da instância isolada.
- Validação da conectividade externa da instância privada enviando pacotes ICMP (`ping`) para a internet através do NAT Gateway.

---

## 📚 Materiais

- Documentação Amazon VPC: [Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)
- Guia de Roteamento AWS: [VPC Route Tables Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html)
- Guia de Bastion Hosts: [Securely Connect to Private Instances](https://aws.amazon.com/blogs/security/how-to-record-ssh-sessions-established-through-a-bastion-host/)

---

## 🛠️ Instruções de execução

Para replicar esta arquitetura de rede na AWS, siga a sequência de etapas abaixo.

- 🤖 1. Crie a VPC personalizada `VPC-Ismael` especificando o bloco IPv4 CIDR `10.0.0.0/16`.

![Criação da VPC-Ismael](VPC%20\(2\).png)

- 🤖 2. Verifique as propriedades da VPC criada e certifique-se de que a resolução de DNS está ativa.

![Detalhes da VPC Criada](VPC%20\(1\).png)

- 🤖 3. Crie a sub-rede pública `Subnet-Ismael-Public` atribuindo o bloco `10.0.0.0/24` na zona `us-west-2a`.

![Criação do Bloco da Sub-rede Pública](VPC%20\(9\).png)

- 🤖 4. Confirme a criação do ID da sub-rede pública no console da AWS.

![Confirmação da Sub-rede Pública](VPC%20\(5\).png)

- 🤖 5. Crie a sub-rede privada `Subnet-Ismael-Private` utilizando o bloco CIDR `10.0.2.0/23`.

![Confirmação da Sub-rede Privada](VPC%20\(7\).png)

- 🤖 6. Valide a listagem geral das sub-redes vinculadas à `VPC-Ismael`.

![Listagem de Sub-redes na VPC](VPC%20\(8\).png)

- 🤖 7. Crie o Internet Gateway `Ismael-Internet-Gateway`.

![Criação do Internet Gateway](VPC%20\(10\).png)

- 🤖 8. Associe (Attach) o Internet Gateway à `VPC-Ismael`.

![Associação do Internet Gateway](VPC%20\(12\).png)

- 🤖 9. Verifique o status `Attached` no painel do Internet Gateway.

![Status Attached do Internet Gateway](VPC%20\(13\).png)

- 🤖 10. Crie a tabela de roteamento `RT-Ismael-Public` vinculada à VPC.

![Criação da Tabela de Roteamento Pública](VPC%20\(14\).png)

- 🤖 11. Crie a tabela de roteamento `RT-Ismael-Private` vinculada à VPC.

![Criação da Tabela de Roteamento Privada](VPC%20\(15\).png)

- 🤖 12. Edite as rotas da tabela `RT-Ismael-Public` adicionando a rota `0.0.0.0/0` direcionada ao `Ismael-Internet-Gateway`.

![Adicionando Rota do Internet Gateway](VPC%20\(18\).png)

- 🤖 13. Valide a rota de saída do Internet Gateway em estado `Ativo`.

![Validação das Rotas Públicas](VPC%20\(19\).png)

- 🤖 14. Associe explicitamente a `Subnet-Ismael-Public` à tabela `RT-Ismael-Public`.

![Associação da Sub-rede Pública à Tabela de Rotas](VPC%20\(20\).png)

- 🤖 15. Associe explicitamente a `Subnet-Ismael-Private` à tabela `RT-Ismael-Private`.

![Associação da Sub-rede Privada à Tabela de Rotas](VPC%20\(22\).png)

- 🤖 16. Confirme as associações de rede e tabela de rotas da sub-rede privada.

![Detalhes de Associação da Sub-rede Privada](VPC%20\(24\).png)

- 🤖 17. Inicie o lançamento do `Ismael Bation Server` selecionando a AMI Amazon Linux 2023.

![Assistente do Bastion Server](VPC%20\(25\).png)

- 🤖 18. Configure a rede do Bastion Server selecionando a sub-rede pública e ativando a atribuição de IP público.

![Configuração de Rede do Bastion Server](VPC%20\(27\).png)

- 🤖 19. Crie o Security Group `Bastion Security Group` permitindo acesso SSH na porta 22.

![Security Group do Bastion Server](VPC%20\(28\).png)

- 🤖 20. Confirme o lançamento do servidor Bastion no console EC2.

![Confirmação do Lançamento do Bastion Server](VPC%20\(30\).png)

- 🤖 21. Verifique no console EC2 o Bastion Server ativo com seu respectivo IP público.

![Bastion Server em Execução](VPC%20\(31\).png)

- 🤖 22. Crie o `Ismael-NGW` (NAT Gateway) na sub-rede pública e aloque um IP Elástico (EIP).

![Criação do NAT Gateway](VPC%20\(33\).png)

- 🤖 23. Verifique a criação do NAT Gateway no console da VPC.

![Status do NAT Gateway](VPC%20\(34\).png)

- 🤖 24. Adicione a rota default `0.0.0.0/0` na tabela `RT-Ismael-Private` apontando para o NAT Gateway.

![Adicionando Rota do NAT Gateway na Tabela Privada](VPC%20\(35\).png)

- 🤖 25. Valide no painel a rota ativa para o NAT Gateway na sub-rede privada.

![Confirmação das Rotas Privadas](VPC%20\(36\).png)

- 🤖 26. Inicie o lançamento da `Ismael Private Instance` em Amazon Linux 2023.

![Lançamento da Instância Privada](VPC%20\(38\).png)

- 🤖 27. Configure a instância na sub-rede privada desabilitando o IP público automático.

![Configuração de Rede da Instância Privada](VPC%20\(40\).png)

- 🤖 28. Crie o grupo `Private Instance SG` permitindo SSH vindo apenas da rede interna (`10.0.0.0/16`).

![Security Group da Instância Privada](VPC%20\(42\).png)

- 🤖 29. Adicione o script de User Data para habilitar a autenticação de login SSH por senha.

![Inserção de User Data na Instância Privada](VPC%20\(43\).png)

- 🤖 30. Verifique no console EC2 ambas as instâncias (Bastion pública e Instância privada) ativas.

![Instâncias Ativas no Console EC2](VPC%20\(45\).png)

- 🤖 31. Conecte-se ao Bastion Server via EC2 Instance Connect e realize o salto SSH para o IP privado da instância isolada.

![Salto SSH via Terminal](VPC%20\(46\).png)

- 🤖 32. Teste a conectividade de saída da instância privada com a internet enviando requisições via NAT Gateway.

![Teste de Conectividade de Saída via NAT Gateway](VPC%20\(48\).png)

---

## 👨‍‍💻 Expert

<p>
    <img 
      align=left 
      margin=10 
      width=80 
      src="https://avatars.githubusercontent.com/u/105826184?v=4"
    />
    <p>&nbsp&nbsp&nbspIsmael Medeiros<br>
    &nbsp&nbsp&nbsp
    <a 
        href="https://github.com/ism-dev-codes">
        GitHub
    </a>
    &nbsp;|&nbsp;
    <a 
        href="https://www.linkedin.com/in/ismael-medeiros">
        LinkedIn
    </a>
    &nbsp;|&nbsp;
    <a 
        href="https://www.instagram.com/ismaelsmedeiros?igsh=YXA1OW1mNXhkNmVy">
        Instagram
    </a>
    &nbsp;|&nbsp;</p>
</p>
<br/><br/>
<p>
