---

copyright:
  years: 2018
lastupdated: "2018-08-08"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:note: .deprecated}
{:tip: .tip} 

# Edificação com segurança avançada
{: #security}

Os desenvolvedores do Swift podem facilmente alavancar serviços de segurança avançados oferecidos pelo {{site.data.keyword.cloud}} para proteger suas chaves e dados em repouso, em uso ou em trânsito no nível de segurança mais alto da indústria.
{:shortdesc}

Como uma abordagem fácil, é possível usar diretamente o {{site.data.keyword.cloud_notm}} HyperSecure Platform Services para todos os serviços de segurança avançada no {{site.data.keyword.cloud}}. Para obter mais informações, consulte [Introdução ao {{site.data.keyword.cloud_notm}} HyperSecure Platform Services](/docs/services/hypersecure-platform/index.html){:new_window}.

## Usando o  {{site.data.keyword.cloud_notm}}  {{site.data.keyword.hscrypto}}

O {{site.data.keyword.hscrypto}} é um serviço experimental que oferece criptografia em suas chaves e dados com o nível de segurança mais alto da indústria. Ele traz a segurança e a integridade do System z para a nuvem. A mesma tecnologia criptográfica de ponta com a qual os bancos e os serviços financeiros contam é oferecida agora aos usuários de nuvem via {{site.data.keyword.cloud_notm}}.

O {{site.data.keyword.hscrypto}} oferece módulos de segurança de hardware (HSMs) endereçáveis de rede para fornecer criptografia segura por meio de interfaces de programação de aplicativos (APIs) PKCS#11. É possível acessar o nível mais alto possível de segurança para o hardware de criptografia, ou seja, FIPS 140-2 Nível 4. O {{site.data.keyword.hscrypto}} também alavanca a solução IBM Advanced Crypto Service Provider (ACSP) que permite acesso remoto aos coprocessadores criptográficos da IBM.

O {{site.data.keyword.hscrypto}} é também o armazenamento de chaves para o serviço {{site.data.keyword.keymanagementserviceshort}}.

Para obter mais informações sobre o {{site.data.keyword.hscrypto}}, consulte [Introdução ao {{site.data.keyword.cloud_notm}} {{site.data.keyword.hscrypto}}](/docs/services/hs-crypto/index.html){:new_window}.

## Usando o {{site.data.keyword.cloud_notm}} {{site.data.keyword.keymanagementserviceshort}}

O {{site.data.keyword.keymanagementserviceshort}} ajuda a provisionar chaves criptografadas para apps em serviços {{site.data.keyword.cloud_notm}}. Conforme você gerencia o ciclo de vida de suas chaves, é possível se beneficiar de saber que as suas chaves estão protegidas pelos módulos de segurança de hardware (HSMs) baseados em nuvem que protegem contra roubo de informações. Junto ao {{site.data.keyword.hscrypto}}, suas chaves são protegidas no nível de segurança mais alto do certificado FIPS 140-2 Nível 4.

Para obter mais informações sobre o {{site.data.keyword.keymanagementserviceshort}}, consulte [Introdução ao {{site.data.keyword.keymanagementserviceshort}}](/docs/services/keymgmt/index.html){:new_window}.

## Usando o  {{site.data.keyword.cloud_notm}}  Hyper Protect DBaaS
{: #hypersecure-dbaas}

O {{site.data.keyword.cloud_notm}} Hyper Protect DBaaS é um serviço experimental do {{site.data.keyword.cloud_notm}} que fornece bancos de dados on demand seguros. Ele oferece uma plataforma flexível e escalável para provisionar e gerenciar de forma rápida e fácil o banco de dados MongoDB.

Com esse serviço, é possível criar clusters de banco de dados no {{site.data.keyword.cloud_notm}}. Cada cluster de banco de dados criado é composto por uma instância de banco de dados principal e duas instâncias de banco de dados secundárias que servem como réplicas para o principal. A lógica do {{site.data.keyword.cloud_notm}} Hyper Protect DBaaS cria e acessa bancos de dados MongoDB nos clusters de banco de dados.

### Protegendo dados e privacidade

A {{site.data.keyword.IBM_notm}} hospeda seus bancos de dados em um ambiente altamente disponível e seguro:
 * Os dados são criptografados em repouso e em andamento.
 * O hardware do sistema, a configuração do sistema e a configuração do banco de dados asseguram alta disponibilidade.
 * As tecnologias subjacentes evitam que a {{site.data.keyword.IBM_notm}} ou um terceiro possa acessar seus dados.

### Incluindo a lógica do {{site.data.keyword.cloud_notm}} Hyper Protect DBaaS em seu aplicativo

Para usar um banco de dados MongoDB em seu aplicativo, consulte
[Introdução ao uso de um banco de dados](../hypersecure_dbaas/database-cluster.html).  

### Aprendendo mais sobre o  {{site.data.keyword.cloud_notm}}  Hyper Protect DBaaS

Para saber mais sobre o {{site.data.keyword.cloud_notm}} Hyper Protect DBaaS, consulte [Introdução ao IBM Cloud Hyper Protect DBaaS](/docs/services/hyper-protect-dbaas/index.html).

## Usando o {{site.data.keyword.cloud_notm}} {{site.data.keyword.hscontainers}}

O {{site.data.keyword.hscontainers}} fornece ferramentas poderosas, combinando s tecnologias Docker e Kubernetes, uma experiência intuitiva do usuário e a segurança e o isolamento integrados para automatizar a implementação, a operação, o ajuste de escala e o monitoramento de apps conteinerizados em um cluster de hosts de cálculo.

Observe que o {{site.data.keyword.hscontainers}} está disponível apenas para usuários patrocinados. Se você espera suporte de segurança dedicado, registre-se como um usuário patrocinado com o [IBM Z Client Feedback Program](https://www-01.ibm.com/marketing/iwm/iwmdocs/web/cc/earlyprograms/zcustomer.shtml) para implementar seu aplicativo no cluster do {{site.data.keyword.hscontainers}}.
{: tip}

Antes de se tornar um usuário patrocinado, é possível usar o {{site.data.keyword.containershort_notm}} para proteger seu aplicativo. Para obter mais informações, consulte
[Introdução ao {{site.data.keyword.containershort_notm}}](/docs/containers/container_index.html#container_index).