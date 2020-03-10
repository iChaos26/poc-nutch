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

**Comando de execução** 
Este comando irá executar todas as estapas e indexar o resultado à algum endpoint. Para especificar, basta passar o endpoint conector.

```sh
$ /root/nutch/bin/crawl -i -D -s urls crawl 5 #este comando possui 5 iterações sobre os resultados crawleados. Quanto maior, mais parse irá fazer sobre o fetch dos dados
```

**Solr**

```sh
$ /root/nutch/bin/crawl -i -D solr.server.url=http://solr:8983/solr/core -s urls crawl 5
```

**Elastic**

```sh
$ /root/nutch/bin/crawl -i -D elastic.server.url=http://localhost:9200 -s urls crawl 5
```

**Step-by-Step**

- Seed

> O seed das urls é feito no estágio inicial, a partir do arquivo **seed.txt** no diretório */nutch/urls*.
> Caso o arquivo **regex-urlfilter.txt** esteja configurado, é nessa etapa que será feito o filtro de url's.

- I



## Configuração

O Nutch possui dois ambientes, um local e um developer. É indicado que os testes fiquem no "runtime" local. Além disso, ele apresenta arquivos de configuração importantes para cada operação. Isso inclui indexação de resultados crawleados, filtros de url's, proxy, white list de urls, entre outros.

### Solr:

