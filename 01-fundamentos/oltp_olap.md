# OLTP 

Online transaction process, sistema focado no dia a dia da operação de um negócio.

mas o que signigica cada parte?

Online: precisamos garantir que toda vez que formos acessar o sistema/banco, esteja disponivel

Transactions: Fazer com que nosso sistema, possa escrever, ler e atualizar dados nas nossas tabelas.


## OLTP

OLTP é voltado para a operação do negócio.

É onde acontecem constantemente operações como:

INSERT
UPDATE
DELETE
consultas pontuais

O banco precisa suportar muitas transações simultâneas, mantendo velocidade e consistência.

Exemplo

Imagine uma empresa de tags para pedágio.

O sistema operacional registra as operações do negócio:

Sistema Operacional
      ↓
Banco OLTP

---

O banco pode possuir tabelas como:

CLIENTE

id_cliente
nome
cpf
data_cadastro

----
TAG

id_tag
id_cliente
status
data_ativacao

---


Quando um cliente faz uma recarga:

Cliente
   ↓
Sistema Operacional
   ↓
INSERT em RECARGA
   ↓
UPDATE do saldo

# OLAP

OOnline Analytical Processing, onde os dados sao organizados para permitir consultas analíticas, como:

A diferença principal:


OLTP = registrar o que aconteceu

"O cliente 101 acabou de fazer uma recarga de R$ 50."

OLAP = analisar o que aconteceu

"Quanto os clientes recarregaram nos últimos 12 meses?"

