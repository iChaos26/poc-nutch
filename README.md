# Apache Nutch powered by ElasticSearch and Solr

![Nutch](https://user-images.githubusercontent.com/26482626/76356646-0d6d5500-62f5-11ea-9799-cc4dd7c66820.png)

Apache Nutch é uma ferramenta de web crawling **robusta e escalável**. Pode ser integrado com indexadores tais como Solr e ElasticSearch. Seu uso gira em torno de aplicações que dependem de massa de dados e lidam com fluxos desses dados em outros endpoints. O framework contém regras de controle que agilizam o processo de web crawling e evita problemas como duplicidade e reduz a complexidade do serviço.

- Open Source
- Dockerizável
- Escalável

## Dependências

Algumas regras sobre versão precisam ser respeitadas, **pois cada versão do Apache Nutch possui seu respectivo indexador**. É comum usar-se a versão 1x por ser mais estável. Para mais informações sobre compatibilidade, clicar [aqui](https://cwiki.apache.org/confluence/display/NUTCH/NutchTutorial).

| Nutch | Solr | Elastic |
| ------ | ------ | ------ |
| 1.16 | 7.3.1 | 5.3 |

- Java 8 ou mais atual (recomenda-se JDK8 por ser estável);

- Apache Nutch 1.16;

- Docker e Docker-Compose;

- ElasticSearch 5.3;

- Solr 7.3.1 ou superior;

## Instalação

A instalação pode ser feita em um ambiente local baixando-se o binário disponibilizado pela Apache, ou via dockerhub, a partir das imagens oficiais. Para efeitos de demonstração, fora disponibilizado **dois arquivos** "yml", contendo Solr e Elastic respectivamente em cada folder:

```sh
$ docker-compose up -d # para iniciar a configuração padrão
$ docker-compose run --entrypoint bash nutch #inicia o bash do nutch
$ docker-compose run --entrypoint bash elastic #inicia o bash do elasticsearch
```

## Crawleamento

O processo de captura e indexação do Apache Nutch se divide em etapas. Há comandos para executar de uma vez ou step by step:

![Arquitetura Nutch](https://user-images.githubusercontent.com/26482626/76442975-7d84e500-63a0-11ea-984a-55bc8d0a5e83.png)


**Comando de execução** 
Este comando irá executar todas as estapas e indexar o resultado à algum endpoint. Para especificar, basta passar o endpoint conector.

>     Usage: crawl [-i|--index] [-D "key=value"] [-s <Seed Dir>] <Crawl Dir> <Num Rounds>
>[-i|--index] -> Indexes crawl results into a configured indexer
>[-D] -> A Java property to pass to Nutch calls
>[-s] -> Seed Directory in which to look for a seeds file
>[Crawl Dir] -> Directory where the crawl/link/segments dirs are saved
>Num Rounds -> The number of rounds to run this crawl for


**Solr**

```sh
$ /root/nutch/bin/crawl -i -D solr.server.url=http://solr:8983/solr/core -s urls crawl 5
```

**Elastic**

```sh
$ /root/nutch/bin/crawl -i -D elastic.server.url=http://localhost:9200 -s urls crawl 5
```

**Step-by-Step**

- Injector 

> O seed das urls é feito no estágio inicial, a partir do arquivo **seed.txt** no diretório */nutch/urls*. Portanto, com o arquivo pronto, é realizado a injeção dessas urls no processo de crawleamento.
> Caso o arquivo **regex-urlfilter.txt** esteja configurado, é nessa etapa que será feito o filtro de url's.

```sh
bin/nutch inject crawl/crawldb urls
Injector: starting at 2020-03-11 13:51:28
Injector: crawlDb: crawl/crawldb
Injector: urlDir: urls
Injector: Converting injected urls to crawl db entries.
Injecting seed URL file file:/root/nutch_source/runtime/local/urls/seed.txt
Injector: overwrite: false
Injector: update: false
Injector: Total urls rejected by filters: 8
Injector: Total urls injected after normalization and filtering: 1
Injector: Total urls injected but already in CrawlDb: 0
Injector: Total new urls injected: 1
Injector: finished at 2020-03-11 13:51:30, elapsed: 00:00:02
```

- Generator

> A fase de crawleamento é acompanhada pelos **segments**. A fase de geração desses segmentos são executadas pelo generator job e alocadas no diretório *crawl/segments*

```sh
bin/nutch generate crawl/crawldb crawl/segments
Generator: starting at 2020-03-11 13:53:49
Generator: Selecting best-scoring urls due for fetch.
Generator: filtering: true
Generator: normalizing: true
Generator: running in local mode, generating exactly one partition.
Generator: number of items rejected during selection:
Generator: Partitioning selected urls for politeness.
Generator: segment: crawl/segments/20200311135352
Generator: finished at 2020-03-11 13:53:53, elapsed: 00:00:03
```

- Fetch

> O fetch portanto é a captura real dos dados cru (**raw**). Nesse estágio ainda não é feito nenhuma mineração ou filtro dos dados capturados.

```sh
bin/nutch fetch crawl/segments/*
Fetcher: starting at 2020-03-11 13:55:42
Fetcher: segment: crawl/segments/20200311135352
Fetcher: threads: 10
Fetcher: time-out divisor: 2
QueueFeeder finished: total 1 records hit by time limit : 0
FetcherThread 36 Using queue mode : byHost
FetcherThread 36 Using queue mode : byHost
FetcherThread 36 Using queue mode : byHost
FetcherThread 43 fetching https://semantix.com.br/ (queue crawl delay=5000ms)
FetcherThread 42 has no more work available
FetcherThread 42 -finishing thread FetcherThread, activeThreads=1
FetcherThread 44 has no more work available
FetcherThread 44 -finishing thread FetcherThread, activeThreads=1
robots.txt whitelist not configured.
FetcherThread 36 Using queue mode : byHost
FetcherThread 36 Using queue mode : byHost
FetcherThread 45 has no more work available
FetcherThread 45 -finishing thread FetcherThread, activeThreads=1
FetcherThread 36 Using queue mode : byHost
FetcherThread 46 has no more work available
FetcherThread 46 -finishing thread FetcherThread, activeThreads=1
FetcherThread 47 has no more work available
FetcherThread 47 -finishing thread FetcherThread, activeThreads=1
FetcherThread 36 Using queue mode : byHost
FetcherThread 48 has no more work available
FetcherThread 48 -finishing thread FetcherThread, activeThreads=1
FetcherThread 36 Using queue mode : byHost
FetcherThread 36 Using queue mode : byHost
FetcherThread 50 has no more work available
FetcherThread 50 -finishing thread FetcherThread, activeThreads=1
FetcherThread 51 has no more work available
FetcherThread 51 -finishing thread FetcherThread, activeThreads=1
FetcherThread 36 Using queue mode : byHost
Fetcher: throughput threshold: -1
Fetcher: throughput threshold retries: 5
FetcherThread 53 has no more work available
FetcherThread 53 -finishing thread FetcherThread, activeThreads=1
-activeThreads=1, spinWaiting=0, fetchQueues.totalSize=0, fetchQueues.getQueueCount=1
-activeThreads=1, spinWaiting=0, fetchQueues.totalSize=0, fetchQueues.getQueueCount=1
FetcherThread 43 has no more work available
FetcherThread 43 -finishing thread FetcherThread, activeThreads=0
-activeThreads=0, spinWaiting=0, fetchQueues.totalSize=0, fetchQueues.getQueueCount=0
-activeThreads=0
Fetcher: finished at 2020-03-11 13:55:47, elapsed: 00:00:04
```

- Parser

> O parse dos segments processados é feito pelo Parser Job. Também é nesta etapa que acontece o cruzamento de informações, caso tenha algum plugin configurado (**ver configuração**).

```sh
bin/nutch parse crawl/segments/*
ParseSegment: starting at 2020-03-11 14:03:41
ParseSegment: segment: crawl/segments/20200311135352
Parsed (235ms):https://semantix.com.br/
ParseSegment: finished at 2020-03-11 14:03:43, elapsed: 00:00:01
```

- UpdateDB

> No updatedb é feito a atualização dos links que foram processados e capturados corretamente. Conforme a arquitetura do Nutch, o processo de ingestão e atualização é tratado como um Database. Isso ajuda a identificar jobs que tiveram problemas e precisam ser reprocessados.

```sh
bin/nutch updatedb crawl/crawldb crawl/segments/*
CrawlDb update: starting at 2020-03-11 14:05:34
CrawlDb update: db: crawl/crawldb
CrawlDb update: segments: [crawl/segments/20200311135352]
CrawlDb update: additions allowed: true
CrawlDb update: URL normalizing: false
CrawlDb update: URL filtering: false
CrawlDb update: 404 purging: false
CrawlDb update: Merging segment data into db.
CrawlDb update: finished at 2020-03-11 14:05:36, elapsed: 00:00:01
```

- Invertlinks

> Por padrão, o nutch agrupa os links de forma invertida, pois ajuda a identificar links que possuem os domínio/protocolo.

```sh
bin/nutch invertlinks crawl/linkdb -dir crawl/segments
LinkDb: starting at 2020-03-11 14:29:36
LinkDb: linkdb: crawl/linkdb
LinkDb: URL normalize: true
LinkDb: URL filter: true
LinkDb: internal links will be ignored.
LinkDb: adding segment: file:/root/nutch_source/runtime/local/crawl/segments/20200311135352
LinkDb: finished at 2020-03-11 14:29:38, elapsed: 00:00:01
```

- Index

> Por final, a parte de indexação dos conteúdos à algum endpoint. Há a opção de deletar duplicatas, filtrar e normalizar os resultados.
> **Importante**: Dependendo do endpoint, há configurações diferentes no nutch-site.xml. Há possibilidades de mapear(mapping) e inclusive indexações customizadas. Esse assunto é abordado nas configurações.

```sh
bin/nutch index crawl/crawldb/ -linkdb crawl/linkdb/ crawl/segments/* -filter -normalize -deleteGone
Segment dir is complete: crawl/segments/20200311135352.
Indexer: starting at 2020-03-11 14:36:04
Indexer: deleting gone documents: true
Indexer: URL filtering: true
Indexer: URL normalizing: true
Indexing 1/1 documents
Deleting 0 documents
Indexer: number of documents indexed, deleted, or skipped:
Indexer:      1  indexed (add/update)
Indexer: finished at 2020-03-11 14:36:08, elapsed: 00:00:03
```

- Exemplo de indexação:

```sh
{
  "responseHeader":{
    "status":0,
    "QTime":42,
    "params":{
      "q":"*:*",
      "_":"1583937374144"}},
  "response":{"numFound":1,"start":0,"docs":[
      {
        "tstamp":["2020-03-11T13:55:46.847Z"],
        "digest":["ea3546ba8ed95a0f7d72d05f556aa57d"],
        "host":["semantix.com.br"],
        "boost":[1.0],
        "id":"https://semantix.com.br/",
        "title":["Semantix - Big Data, Inteligência Artificial e Analytics - Semantix"],
        "url":["https://semantix.com.br/"],
        "content":["Semantix - Big Data, Inteligência Artificial e Analytics - Semantix\n[email protected]\n+55 (11) 5082-2656\nHome\nSobre nós\nSoluções\nAIJUS\nOpenGalaxy\nSemantix ID\nSmarter Sales\nTreinamentos\nNegócios\nBlog\nImprensa\nContato\nGET SMARTER\nCONHEÇA A PLATAFORMA\nSoluções. Conheça as plataformas que estão acelerando a revolução digital com big data e Inteligência Artificial. AIJUS\nOPENGALAXY Conheça as plataformas da Semantix.\nSemantix ID Smarter Sales\nSMARTER GET\nSeja um semântico. JUNTE-SE A NÓS Olá mundo, venha fazer parte dessa equipe.\nO seu negócio ainda mais inteligente com Big Data, IA e IoT.\nSOBRE NÓS\nSOLUÇÕES\nOpenGalaxy\nPlataforma One Stop Shop de Big Data Analytics para o seu negócio.\nAcelere a transformação digital da sua empresa.\nReduza o tempo dos seus projetos de meses para dias.\nIntegre as melhores soluções do mercado sem nenhuma complexidade.\nReduza em até 50% o investimento para implementação de soluções em Inteligência Artificial.\nSAIBA MAIS\nSOLUÇÕES\nAIJUS\nPrimeira solução de IA Jurídica que entrega o que existe de mais avançado em Jurimetria.\nIdentifique anomalias na empresa por meio de análise de dados.\nAutomatize o cadastro de processos, eliminando erros humanos.\nCompare seus processos com ao menos 2 concorrentes.\nOtimize o provisionamento para redução da contingência.\nSAIBA MAIS\nSOLUÇÕES\nSemantix ID\nA plataforma completa para validação de identidade Omnichannel utilizando inteligência artificial.\nImplante a autenticação de documentos através do Face Match.\nComprove a presença física do usuário através do Liveness.\nGaranta a consistência e padronização dos dados captados.\nIdentifique anomalias em real-time para a detecção de fraudes.\nAumente a segurança e controle do seu processo de autenticação de pessoas.\nSAIBA MAIS\nSOLUÇÕES\nSmarter Sales\nSoluções omnichannel para e-commerce e loja física. Infraestrutura tecnológica completa, capaz de gerenciar sistemas, integrações e processos para varejistas e atacadistas.\nTenha acesso a análises mais precisas e detalhadas.\nProporcione aos seus usuários uma experiência intuitiva e amigável.\nReduza seus custos financeiros e operacionais.\nSAIBA MAIS\nDADOS\nTransforme seu negócio usando ciência e análise de dados\n3\nEscritórios na América Latina\n24/7\nData Operation Center\n400\nSemânticos engajados\nPARCEIROS\nIMPRENSA\n18 de fevereiro de 2020\nSolução aumenta produtividade jurídica com uso de inteligência artificial\nLEIA MAIS\n17 de fevereiro de 2020\nSemantix estrutura vertical de negócios para a área de saúde\nLEIA MAIS\n17 de fevereiro de 2020\nSolução aumenta produtividade jurídica com uso de inteligência artificial\nLEIA MAIS\nAssine nossa newsletter\nFale conosco\n+55 (11) 5082-2656\n[email protected]\nAv. Eusébio Matoso, 1375 | 10º Andar Pinheiros | São Paulo – SP\nCopyright © 2020 Semantix. Todos os direitos reservados. Desenvolvido por lab212\nEsse site utiliza cookies para aprimorar a sua experiência como usuário. Por favor, clique ao lado em \"Aceitar\" e saiba mais conhecendo a nossa política\nACEITAR\nPolítica de Privacidade & Cookies\nClose\nPrivacy Overview\nThis website uses cookies to improve your experience while you navigate through the website. Out of these cookies, the cookies that are categorized as necessary are stored on your browser as they are essential for the working of basic functionalities of the website. We also use third-party cookies that help us analyze and understand how you use this website. These cookies will be stored in your browser only with your consent. You also have the option to opt-out of these cookies. But opting out of some of these cookies may have an effect on your browsing experience.\nNecessary\nAlways Enabled\nNecessary cookies are absolutely essential for the website to function properly. This category only includes cookies that ensures basic functionalities and security features of the website. These cookies do not store any personal information.\nNon-necessary\nNon-necessary\nAny cookies that may not be particularly necessary for the website to function and is used specifically to collect user personal data via analytics, ads, other embedded contents are termed as non-necessary cookies. It is mandatory to procure user consent prior to running these cookies on your website.\n"],
        "_version_":1660878708171341824}]
  }}
```


## Configuração

O Nutch possui dois ambientes, um local e um developer. É indicado que os testes fiquem no "runtime" local. Além disso, ele apresenta arquivos de configuração importantes para cada operação. Isso inclui indexação de resultados crawleados, filtros de url's, proxy, "whitelist" de urls, stopwords, entre outros.

**Arquivos de configuração**:

Os arquivos de configuração do Nutch são XML's que definem as preferências de execução. Entre eles, há o **nutch-site.xml** e o **nutch-default.xml**. A boa prática indica que alterações nos plugins **devem ser feitas apenas no nutch-site**. Há também arquivos como o **index-writers.xml** que alteram as propriedades de indexação de conteúdo. Todas as configurações necessárias para subir o serviço estão nas respectivas pastas.

- **Atenção**: Conforme a issue aberta no [Jira](https://issues.apache.org/jira/browse/NUTCH-2745), a versão 1.16 do Nutch não builda o schema.xml, utilizado para indexação dos resultados no Solr. É necessário subir o docker com o volume compartilhado.

##### Nutch-site.xml:

```sh
<!-- HTTP properties -->

<property>
  <name>http.agent.name</name>
  <value>NutchDev</value>
  <description>HTTP 'User-Agent' request header. MUST NOT be empty -
  please set this to a single word uniquely related to your organizat$
  NOTE: You should also check other related properties:

    http.robots.agents
    http.agent.description
    http.agent.url
    http.agent.email
    http.agent.version

  and set their values appropriately.

  </description>
</property>

<!-- Elasticsearch properties -->

<property>
  <name>elastic.host</name>
  <value>elastic</value>
  <description>The hostname to send documents to using TransportClient. Either host
  and port must be defined or cluster.</description>
</property>

<property> 
  <name>elastic.port</name>
  <value>9300</value>The port to connect to using TransportClient.<description>
  </description>
</property>

<property> 
  <name>elastic.cluster</name>
  <value>docker-elastic</value>
  <description>The cluster name to discover. Either host and potr must be defined
  or cluster.</description>
</property>

<property> 
  <name>elastic.index</name>
  <value>nutch</value> 
  <description>Default index to send documents to.</description>
</property>

<!-- Applicable plugins-->
 <property>
  <name>plugin.includes</name>
  <value>protocol-http|urlfilter-regex|parse-(html|tika)|index-(basic|anchor)|indexer-elastic|scoring-opic|urlnormalizer-(pass|regex|basic)</value>
  <description>Regular expression naming plugin directory names to
  include.  Any plugin not matching this expression is excluded.
  In any case you need at least include the nutch-extensionpoints plugin. By
  default Nutch includes crawling just HTML and plain text via HTTP,
  and basic indexing and search plugins. In order to use HTTPS please enable 
  protocol-httpclient, but be aware of possible intermittent problems with the 
  underlying commons-httpclient library.
  </description>
</property>
</configuration>

```
##### Index-writers.xml:

```sh
<writers xmlns="http://lucene.apache.org/nutch"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://lucene.apache.org/nutch index-writers.xsd">

  <writer id="indexer_elastic_1" class="org.apache.nutch.indexwriter.elastic.ElasticIndexWriter">
    <parameters>
      <param name="host" value="elastic"/>
      <param name="port" value="9300"/>
      <param name="cluster" value="docker-elastic"/>
      <param name="index" value="nutch"/>
      <param name="max.bulk.docs" value="250"/>
      <param name="max.bulk.size" value="2500500"/>
      <param name="exponential.backoff.millis" value="100"/>
      <param name="exponential.backoff.retries" value="10"/>
      <param name="bulk.close.timeout" value="600"/>
      <!--<param name="options" value="key1=value1,key2=value2"/>-->
    </parameters>
    <mapping>
      <copy>
        <field source="title" dest="title,search"/>
      </copy>
      <rename />
      <remove />
    </mapping>
  </writer>
  <writer id="indexer_elastic_rest_1" class="org.apache.nutch.indexwriter.elasticrest.ElasticRestIndexWriter">
    <parameters>
      <param name="host" value="elastic"/>
      <param name="port" value="9200"/>
      <param name="index" value="nutch"/>
      <param name="max.bulk.docs" value="250"/>
      <param name="max.bulk.size" value="2500500"/>
      <param name="user" value="user"/>
      <param name="password" value="password"/>
      <param name="type" value="doc"/>
      <param name="https" value="false"/>
      <param name="trustallhostnames" value="false"/>
      <param name="languages" value=""/>
      <param name="separator" value="_"/>
      <param name="sink" value="others"/>
    </parameters>
    <mapping>
      <copy>
        <field source="title" dest="search"/>
      </copy>
      <rename />
      <remove />
    </mapping>
  </writer>
  <writer id="indexer_cloud_search_1" class="org.apache.nutch.indexwriter.cloudsearch.CloudSearchIndexWriter">
    <parameters>
      <param name="endpoint" value=""/>
      <param name="region" value=""/>
      <param name="batch.dump" value="false"/>
      <param name="batch.maxSize" value="-1"/>
    </parameters>
    <mapping>
      <copy />
      <rename />
      <remove />
    </mapping>
  </writer>
  <writer id="indexer_kafka_1" class="org.apache.nutch.indexwriter.kafka.KafkaIndexWriter">
    <parameters>
      <param name="host" value=""/>
      <param name="port" value="9092"/>
      <param name="topic" value=""/>
      <param name="key.serializer" value="org.apache.kafka.common.serialization.ByteArraySerializer"/>
      <param name="value.serializer" value="org.apache.kafka.connect.json.JsonSerializer"/>
      <param name="max.doc.count" value="100"/>
    </parameters>
    <mapping>
      <copy>
        <field source="title" dest="search"/>
      </copy>
      <rename />
      <remove />
    </mapping>
  </writer>
</writers>
```

- Verificando se o Elastic está integrado com o Nutch (curl bash nutch)

```sh
curl -X GET $elastic_endpoint 
{
  "name" : "MwjsR6h",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "BFH8U4uIQ62MG4FKyuwUWQ",
  "version" : {
    "number" : "5.3.2",
    "build_hash" : "3068195",
    "build_date" : "2017-04-24T16:15:59.481Z",
    "build_snapshot" : false,
    "lucene_version" : "6.4.2"
  },
  "tagline" : "You Know, for Search"
}
```

- Verificando Solr

```sh
curl -X GET http://localhost:8983/solr/semantix/select\?q\=\*%3A\*
{
  "responseHeader":{
    "status":0,
    "QTime":0,
    "params":{
      "q":"*:*"}},
  "response":{"numFound":0,"start":0,"docs":[]
  }}
  ```

### On-loading

Ainda está em desenvolvimento a possibilidade de implementar quebra de captcha e um gerenciador de proxies.

### Links Úteis

- [Intranet Doc Nutch](https://cwiki.apache.org/confluence/display/NUTCH/IntranetDocumentSearch): Necessárias para configurar clusters.
- [Tutorial do Apache Nutch](https://cwiki.apache.org/confluence/display/NUTCH/NutchTutorial)
- [Componentes e workflow do Nutch](https://qbox.io/blog/scraping-the-web-with-nutch-for-elasticsearch): Aprofundamento na arquitetura
- [White List de Robots.txt](https://cwiki.apache.org/confluence/display/NUTCH/WhiteListRobots): Como configurar.
- [Integrando Elastic com Nutch](https://www.mind-it.info/2013/09/26/integrating-nutch-1-7-elasticsearch/)
- [Repositório Oficial do Nutch](https://github.com/apache/nutch)
