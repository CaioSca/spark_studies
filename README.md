# Spark Studies

OVERVIEW
- Possui um driver/master node e os worker nodes (algo parecido com o Kubernetes)
- Master node recebe a demanda e divide-a com os worker nodes, da forma mais otimizada possível
- Normalmente, por boas práticas, temos 1 executor por worker node
- Executors possuem quantidades de slots variáveis, em função da quantidade de núcleos de CPU disponíveis
- Demanda é dividida pelos slots, que conversam entre si durante a execução. Quando terminado o processamento, um slot de executor fica responsável por sistematizar os resultados, devolvendo eles para o driver ou fazendo o envio para um outro local (ex.: bucket)
- Tasks são as menores unidades de trabalho do spark. Elas que são atribuídas aos slots
- Driver pode escolher não dividir demanda entre mais de um ou muitos nós, em função da carga de trabalho identificada
- Se alguma tarefa, num executor, falha, o driver se encarrega de enviá-la para outro executor
- Todos os processos rodam numa JVM (java virtual machine)

DEPLOYMENT MODES



