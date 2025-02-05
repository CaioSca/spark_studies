# Spark Studies

source: https://www.youtube.com/watch?v=iXVIPQEGZ9Y 
Thank's to Databricks For Professionals channel! <3 Pozdrawiam


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
- São 3:
  - local, com tudo na sua máquina (drivers e executors) - (to play around)
  - client, apenas com driver na sua máquina. Executors na cloud (testing/developing)
  - cluster, com tudo na cloud (production requirement)
 

RDDS, DATAFRAMES AND DATASETS
- RDDs não são otimizadas por default e sua api nao é tão intuitiva (low level - otimização só por meio de configs), diferentemente dos dataframes (api high level - catalyst optimizer por trás dos panos) e datasets (scala - fortemente tipado)
- datasets exigem o case class, pra definir o schema dos dados que serão lidos e processados (grande diferença pro dataframe)
- RDDs representam uma coleção de objetos imutáveis, que podem ser processadas em paralelo. Garante tolerância a falhas, linhagem de dados e tb uma série de transformações possíveis
- DataFrames, inspirado no pandas python, é abstração high-level dos RDDs, se asemelhando com tabelas de bancos relacionais. Ofere otimizações pelo catalyst optimizer
- DataSets, combina benefícios dos RDDs e DataFrames, oferece API fortemente tipada, que só pode ser usada em scala

TIPOS DE OPERAÇÕES COM SPARK
- Transformações:
  - aplica-se regras de negócio sobre os dados, fazendo com que os RDDs/DataFrames/DataSets tornem-se em algo novo
  - podem ser transformações do tipo narrow ou wide
  - transformações narrow não exigem que os executors troquem informações entre si, pq eles podem fazer a computação de maneira independente (FILTER, SELECT, CAST, Create new column, etc.)
  - transformações wide exigem data shuffling (troca entre executors), pq pressupõe agragações/ordenações de informações, considerando o todo (mesmo que particionado) -  GROUP BY, SORT, JOIN, etc. 
- Ações:
  - demanda por resultados (show, count, save, take)
- Enquanto transformações não demandam algum tipo de ação, o spark só "entende" o que ele precisa fazer, a estratégia... mas a execução em si se da com algum comando de ação
- Transformações sem comandos de ação são extremamente velozes

![image](https://github.com/user-attachments/assets/30e86509-5b9e-42e2-b40d-b7aa4f10cd5b)

- Estágios começam um depois do outro. Normalmente, a barreira entre um e outro é o data shuffling. Primeiro Spark entende o que precisa fazer e depois de fato o faz (paralelizando computação e etc.)

JOB x DAG

LAZINESS OF SPARK
- tenta aplicar narrow transformations antes das wide, otimizando
- usar .explain() mostra o plano "físico" do que será feito na computação
- explain em parâmetro (True) que te dá mais "planos" sobre os dados que serão processados


DATA SHUFFLING
- Depende também do tipo de dado! Agrupamentos/ordenação em colunas com alta cardinalidade tendem a demorar muito mais do que as mesmas operações em colunas com baixa cardinalidade!
- Salva dados que estão em memória em disco, envia pela internet para outro executor, que por sua vez precisa ler o conteúdo para seguir com a agregação... esta etapa pode ser MUITO lenta. Evitar e otimizar ao máximo!


OPTIMIZATION
- What you can do to optimize? Use parquet or data, instead of csv
- Avoid sorts
- minimize volume of data
- cache/persist dataframes
- repartition / coalesce
- avoid user defined functions - prefer built-in
- partition and/or index data
- bucketing
- optmize cluster
- ...

![image](https://github.com/user-attachments/assets/64fc331c-7eba-40e8-a4fb-7a635950ba7d)

- Catalyst Optmizer do a lot, as we've seen
- 
- But there is Tungsten, with code optimization (tungsten gera código otimizado para cada task que está rodando)
![image](https://github.com/user-attachments/assets/5493dcf8-a612-4168-98fc-4bce1fe523fb)

- Theres Adaptative Query Executor too (AQE): query re-optimization that occurs during query execution (otimiza número de partições durante o shuffling, por exemplo)
![image](https://github.com/user-attachments/assets/e9b59212-9d75-4a71-8bdf-683ecef0a6a0)





