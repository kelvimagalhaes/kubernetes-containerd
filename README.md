<h1 align="center"> kubernetes-containerd</h1>
Implementação do cluster kubernetes com Containerd como container runtime

## Descrição do Projeto

Primeiro de tudo o que é o Kubernetes popularmente chamado de K8S ?

É uma plataforma desenvolvida pela Google para gerenciar aplicativos em contêineres através de múltiplos hosts de um cluster e assim, ampliar a estabilidade e controle do serviço. Com ele se tornou possível o que por muito tempo não era possível, nativamente, através do Docker: criar balanceamento de cargas e a migração de contêineres sem perda de dados. Além disto é possível automatizar estas ações para ter alta disponibilidade dos serviços.

Mesmo depois do Docker ter lançado o Swarm mode, porque o Kubernetes ainda é uma opção mais interessante?

Sem dúvida o Docker swarm é uma opção interessante para criarmos clusters de contêineres e gerenciarmos de forma simples, mas o Kubernetes traz uma proposta bem mais automatizada para fazermos isto. Enquanto através do Docker Swarm você criará manualmente um cluster, o Kubernetes fará isto tudo automaticamente para você. Ele seguirá suas regras predefinidas (de uso de recursos ou de quantidade de Pods) para manter a estabilidade do serviço. Um segundo ponto é o isolamento dos volumes que garante que os dados de um Pod estejam da forma que estavam quando ele precisou ser reiniciado. Não mencionei anteriormente , mas, pelo Kubernetes também é possível definir namespaces para criarmos ambientes isolados de produção e testes. Assim, podemos limitar os recursos de cada ambiente, para que um não interfira no outro. E para finalizar, um ponto que achei interessante é a flexibilidade dele trabalhar com outros modelos de contêiner que não seja o Docker, como o rockets, containerid, cri-o.

Abaixo está a arquitetura do K8S, falaremos de cada uma delas no decorrer da instalação.
