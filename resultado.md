# Construindo aplicações de larga escala com frameworks de programação paralela/distribuída

### PSPD – Programação p Sistemas Paralelos e Distribuídos
Prof.: Fernando W. Cruz

#### Alunos
 - Caio Martins 180074741
 - Yuri Alves Bacarias 180078640

# Introdução

O Jogo da Vida, criado por John H. Conway em 1970, é um autômato celular que simula a evolução de uma sociedade de organismos vivos em gerações sucessivas. Essa simulação ocorre em um tabuleiro bidimensional, que é infinito em todas as direções, e é composto por células idênticas. Cada célula possui oito vizinhas, que são todas aquelas que compartilham uma aresta ou um vértice com a célula original.

Cada célula pode existir em um de dois estados: viva ou morta. A evolução da sociedade acontece quando regras são aplicadas simultaneamente a todas as células do tabuleiro, determinando o próximo estado de cada uma delas. As regras do Jogo da Vida são as seguintes:

 - Células vivas com menos de duas vizinhas vivas morrem por abandono.
 - Células vivas com mais de três vizinhas vivas morrem de superpopulação.
 - Células mortas com exatamente três vizinhas vivas se tornam vivas.
 - As demais células mantêm o seu estado anterior.

O Jogo da Vida é um exemplo notável de um sistema que exibe emergência e complexidade a partir de regras simples. A sua natureza intrigante e a capacidade de gerar padrões surpreendentes fazem dele um objeto de estudo fascinante para diversas áreas, incluindo a ciência da computação.

O objetivo deste projeto é desenvolver uma aplicação que atenda aos rigorosos requisitos de performance e elasticidade, permitindo que ela possa ser dimensionada para funcionar como uma aplicação de larga escala.

Sobre a Aplicação e os Requisitos:
Para alcançar a performance desejada, serão exploradas diversas soluções, envolvendo computação paralela e distribuída. As abordagens principais serão testadas:
 - Utilização do Apache Hadoop/Spark, que possibilitará o processamento distribuído dos dados do algoritmo "jogo da vida".
 - Implementação das bibliotecas e pragmas OpenMP e MPI no código original do jogo da vida, permitindo que o paralelismo seja explorado em nível de código.
 - Além disso, será adotado o orquestrador Kubernetes para garantir a elasticidade da aplicação em um ambiente de cluster.


# Requisito de performance 

### I. Apache Spark:

O Apache Spark é uma poderosa plataforma de computação distribuída projetada para lidar com grandes volumes de dados e executar tarefas em paralelo. Nesta seção, exploraremos como o Spark pode ser aplicado no contexto do "Jogo da Vida" para melhorar a performance e otimizar o processamento das gerações sucessivas do tabuleiro.

 - 1.1 Arquitetura do Apache Spark:
Exploramos a arquitetura do Spark, incluindo seus principais componentes, como o Spark Core, Spark SQL, Spark Streaming.

- 1.2 Paralelismo no Spark:
Observamos a forma como o Spark permite a divisão das tarefas em unidades menores para serem executadas em diferentes nós do cluster, tentando distribuir o algoritmo do "Jogo da Vida" e como isso pode melhorar sua performance.

 - 1.3 Desenvolvimento da Aplicação com Spark:
Durante o processo de adaptação do código do "Jogo da Vida" para tirar proveito do ambiente distribuído do Spark, buscamos utilizar suas APIs e estruturas de dados para otimizar o processamento e aproveitar a escalabilidade. No entanto, enfrentamos alguns desafios que resultaram em problemas significativos.

Overhead de Tráfego entre os RDDs:
Ao utilizar o Spark para processar o "Jogo da Vida", notamos um grande overhead de tráfego entre os RDDs (Resilient Distributed Datasets). Os RDDs são a principal abstração de dados do Spark e são fundamentais para permitir a distribuição e paralelismo das operações. Entretanto, o alto volume de comunicação entre os RDDs acabou gerando um gargalo na performance da aplicação. Essa comunicação excessiva entre os nós do cluster impactou negativamente o desempenho, levando a um tempo de processamento de 118 segundos para apenas o primeiro nivel do tabuleiro.

Problemas com a Stack de Recursão no Python:
Outro desafio que enfrentamos foi relacionado à utilização do Python como linguagem de programação no ambiente Spark. Ao tentar aplicar uma abordagem alternativa para lidar com o "Jogo da Vida", nos deparamos com problemas relacionados à stack de recursão. O Python possui um limite na profundidade da pilha de recursão, e como o "Jogo da Vida" pode envolver muitas iterações e chamadas recursivas, isso acabou resultando em erros e travamentos da aplicação.

Diante desses resultados, decidimos explorar uma nova abordagem utilizando as bibliotecas OpenMP e MPI. Acreditamos que essas tecnologias podem ser mais eficientes para lidar com o paralelismo em nível de código e, possivelmente, superar as limitações encontradas com o Spark. A seguir, descreveremos como as bibliotecas OpenMP e MPI foram utilizadas e como elas podem contribuir para atender aos requisitos de performance.

### II. OpenMP e MPI:

Nesta seção, exploraremos uma abordagem de paralelismo em nível de código, utilizando as bibliotecas OpenMP e MPI. Ambas as tecnologias permitem a exploração do paralelismo em um único sistema, possibilitando uma execução mais eficiente do "Jogo da Vida".

2.1 OpenMP:
No código, podemos ver o uso do OpenMP na função "UmaVida", que é responsável por aplicar as regras do "Jogo da Vida" a cada célula do tabuleiro. A diretiva "pragma omp parallel for" é aplicada ao loop for que itera sobre as células do tabuleiro. Dessa forma, o processamento de cada célula pode ser executado em paralelo por diferentes threads, aproveitando o poder de processamento dos processadores multicore.

Além disso, a diretiva "private" é usada para definir as variáveis que serão privadas para cada thread, garantindo que cada thread tenha sua própria cópia dessas variáveis durante a execução do loop.

2.2 MPI:
O MPI (Message Passing Interface) é utilizado para explorar o paralelismo em nível de processo, que podem trocar informações através de trocas de mensagens, possibilitando a execução paralela distribuída.

No código, podemos ver o uso do MPI na função principal "main". A função "MPI_Init" é chamada para inicializar o ambiente MPI, e as funções "MPI_Comm_rank" e "MPI_Comm_size" são usadas para obter o número do processo e o tamanho do cluster, respectivamente.

Em seguida, um loop é executado para diferentes tamanhos do tabuleiro, e cada processo é responsável por processar um tamanho específico do tabuleiro. A variável "rank" obtida pela função "MPI_Comm_rank" é usada para determinar o tamanho do tabuleiro que cada processo irá processar, dividindo o processamento entre os processos disponíveis no cluster.

2.3 Adaptação do "Jogo da Vida" com OpenMP e MPI:
o OpenMP e o MPI trabalham em conjunto para proporcionar um alto nível de paralelismo tanto em nível de thread quanto em nível de processo, permitindo que o algoritmo "Jogo da Vida" seja executado de forma mais eficiente em ambientes distribuídos e paralelos. Assim conseguimos diminuir o tempo de execussao de um tabuleiro completo para algo em torno de 10 segundos, contra 60 segundos da implementação original.

# Requisito de elasticidade 
Kubernetes é uma poderosa plataforma de código aberto para orquestração de contêineres. Ele oferece um ambiente de gerenciamento altamente flexível e automatizado para implantar, dimensionar e gerenciar aplicativos em contêineres de forma eficiente. Através do Kubernetes, podemos definir como os aplicativos devem ser implantados e especificar os recursos necessários, enquanto a plataforma cuida da distribuição, monitoramento, recuperação de falhas e escalabilidade horizontal dos contêineres. O Kubernetes se baseia em conceitos como pods, que são unidades básicas de implantação, e serviços, que permitem a comunicação entre as diferentes partes dos aplicativos.

Com base na arquitetura descrita, foi criado um pod utilizando a imagem do Kafka para desempenhar o papel de broker. Esse pod tem a importante função de atuar como um intermediário entre os clientes e as engines que executam o "Jogo da Vida". Ele é responsável por receber as requisições dos clientes, encaminhá-las para as engines apropriadas para processamento e, posteriormente, enviar os dados coletados para o ElasticSearch.

O uso do Kafka como broker traz benefícios significativos para a arquitetura do sistema. Ele oferece um sistema de mensagens distribuído altamente confiável e tolerante a falhas, permitindo que as requisições dos clientes sejam gerenciadas de forma assíncrona, sem sobrecarregar as engines. Além disso, o Kafka suporta escalabilidade horizontal, o que possibilita a expansão do sistema conforme a demanda aumenta. Entretando tivemos problemas com a configuracao do ambiente e nao conseguimos utilizar o kafka da maneira desejada.


# Análise dos resultados

### resultados com openMP e MPI local
```bash
$ mpicc openMPi.c -fopenmp
$ ./a.out -lmpi -4 -fopenmo -4
```
**Ok, RESULTADO CORRETO**
tam=8; tempos: init=0.0000091, comp=0.0009840, fim=0.0000401, tot=0.0010331 

**Ok, RESULTADO CORRETO**
tam=16; tempos: init=0.0000169, comp=0.0005250, fim=0.0000091, tot=0.0005510 

**Ok, RESULTADO CORRETO**
tam=32; tempos: init=0.0000188, comp=0.0023541, fim=0.0002260, tot=0.0025990 

**Ok, RESULTADO CORRETO**
tam=64; tempos: init=0.0000889, comp=0.0034461, fim=0.0000439, tot=0.0035789 

**Ok, RESULTADO CORRETO**
tam=128; tempos: init=0.0000689, comp=0.0154240, fim=0.0000651, tot=0.0155580 

**Ok, RESULTADO CORRETO**
tam=256; tempos: init=0.0002649, comp=0.1177499, fim=0.0001581, tot=0.1181729 

**Ok, RESULTADO CORRETO**
tam=512; tempos: init=0.0012188, comp=0.9294012, fim=0.0005100, tot=0.9311299 

**Ok, RESULTADO CORRETO**
tam=1024; tempos: init=0.0048599, comp=7.5617781, fim=0.0018840, tot=7.5685220

### resultados com openMP e MPI kubernetes
```bash
$ sudo kubectl logs mpi-program-qckp2
```

**Ok, RESULTADO CORRETO**
tam=8; tempos: init=0.0000010, comp=1.4898779, fim=0.0000191, tot=1.4898980 

**Ok, RESULTADO CORRETO**
tam=16; tempos: init=0.0000019, comp=2.7056329, fim=0.0000181, tot=2.7056530 

**Ok, RESULTADO CORRETO**
tam=32; tempos: init=0.0000060, comp=7.0106790, fim=0.0001011, tot=7.0107861 

**Ok, RESULTADO CORRETO**
tam=64; tempos: init=0.0000160, comp=13.5254800, fim=0.0000408, tot=13.5255368 

**Ok, RESULTADO CORRETO**
tam=128; tempos: init=0.0000820, comp=31.6027999, fim=0.0000331, tot=31.6029150 

**Ok, RESULTADO CORRETO**
tam=256; tempos: init=0.0002320, comp=52.7071290, fim=0.0000761, tot=52.7074370 

**Ok, RESULTADO CORRETO**
tam=512; tempos: init=0.0008821, comp=74.7925398, fim=0.0002751, tot=74.7936971 

**Ok, RESULTADO CORRETO**
tam=1024; tempos: init=0.0054889, comp=76.3642101, fim=0.0010989, tot=76.3707979

# Conclusão
A exploração das bibliotecas OpenMP e MPI mostrou-se promissora para o aprimoramento do desempenho do "Jogo da Vida" em ambientes distribuídos e paralelos. A utilização dessas tecnologias permitiu lidar com o paralelismo em nível de código, superando as limitações encontradas com o Apache Spark. O tempo de execução de um tabuleiro completo foi significativamente reduzido em comparação com a implementação original.

Apesar das vantagens significativas proporcionadas pelo Kubernetes como orquestrador e pelo Kafka como broker na gestão das requisições dos clientes e na implementação da elasticidade, o projeto enfrentou desafios relacionados à configuração do ambiente. Esses obstáculos resultaram em limitações e problemas que impactaram a plena utilização dessas tecnologias conforme inicialmente planejado.

O Kubernetes é reconhecido por sua capacidade de gerenciar a implantação e escalabilidade de contêineres de forma automatizada e eficiente. Entretanto, a sua configuração e a adaptação da aplicação para trabalhar em conjunto com o orquestrador exigiram um esforço adicional. Algumas dificuldades surgiram na integração com outros componentes da arquitetura, o que levou a uma menor exploração dos recursos avançados que o Kubernetes pode oferecer.

Similarmente, embora o Kafka tenha demonstrado ser uma solução sólida para lidar com a comunicação assíncrona e distribuída das requisições dos clientes, houve desafios em relação à configuração e ao uso adequado da ferramenta.

Em conclusão, embora o projeto tenha enfrentado desafios em relação à configuração do ambiente para a utilização do Kubernetes e do Kafka, foi possível alcançar resultados promissores com a adoção das tecnologias OpenMP e MPI.