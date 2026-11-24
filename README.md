# solidarytech-iac

Infraestrutura da **SolidaryTech** em Terraform. Tudo o que existe na AWS nasce daqui: nenhum recurso é criado pelo console.

Os módulos vêm do [`terraform-aws-modules`](https://github.com/fiap-hackathon-dclt/terraform-aws-modules), por tag. Este repositório decide **o quê** e **onde**; os módulos decidem **como**.

## O que vai ser provisionado

| Bloco | Recursos |
|---|---|
| **Fundação** | Estado remoto em S3, repositórios ECR, role de CI por OIDC |
| **Rede** | VPC, subnets, NAT e security groups |
| **Cluster** | Amazon EKS e os pré-requisitos dos seus controladores |
| **Dados** | PostgreSQL gerenciado para o `ngo-service` e o `donation-service`, tabela DynamoDB do `volunteer-service` |
| **Mensageria** | Fila SQS dos eventos de doação |
| **FinOps** | Tags obrigatórias, budgets e alertas de custo, ativação das tags de alocação de custo |
| **Disaster Recovery** | A estratégia escolhida para manter o processamento de doações diante da perda da região principal |
| **Operação** | Monitores, alertas e integrações de incidente, declarados como código |

A divisão em stages, isto é, quantos estados independentes e o que cada um contém, ainda vai ser decidida e registrada no [registro de decisões](https://github.com/fiap-hackathon-dclt/.github/tree/main/docs/decisoes). O ponto de partida é o modelo do ToggleMaster: `bootstrap`, `infra`, `addons`, `datadog` e `pagerduty`, separados pelo ciclo de vida dos recursos.

## Tags obrigatórias

O enunciado exige estas três tags em **todos** os recursos de nuvem:

| Tag | Valor |
|---|---|
| `Project` | `SolidaryTech` |
| `Environment` | `Production` |
| `CostCenter` | `NGO-Core` |

A intenção é declará-las uma vez, no provider, para que cheguem a tudo o que o Terraform cria. O que os controladores do cluster criam depois (nós do Karpenter, volumes EBS, load balancers) precisa receber as mesmas tags pela configuração dos próprios controladores, porque o provider não alcança esses recursos.

## Regras

- **Nada no console.** Inclui o que costuma escapar: budgets, ativação de tags de custo no Billing, monitores do APM.
- **Nenhuma chave estática.** A esteira assume uma role na AWS por OIDC; segredos de terceiros chegam por variável de esteira ou pelo Secrets Manager.
- **O ambiente é destruído quando não está em uso.** O crédito é limitado, então subir e derrubar tudo por esteira precisa ser rotina, não exceção.

## Estado

Repositório recém-criado, ainda sem código Terraform.
