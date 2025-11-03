The Elastic stack, created by Elastic, is an open-source collection of mainly three applications (Elasticsearch, Logstash, and Kibana) that work in harmony to offer users comprehensive search and visualization capabilities for real-time analysis and exploration of log file sources.

![Elastic Stack diagram showing components: Solutions (Application Search, Site Search, Enterprise Search, Logging, Future), Visualize (Kibana), Store Search & Analyze (Elasticsearch), Ingest (Beats, Logstash), Deployment (SaaS: Elastic Cloud, Self Managed: Elastic Cloud Enterprise, Standalone).](https://academy.hackthebox.com/storage/modules/211/elastic.png)

The high-level architecture of the Elastic stack can be enhanced in resource-intensive environments with the addition of Kafka, RabbitMQ, and Redis for buffering and resiliency, and nginx for security.

![Beats collect data from sources like Filebeat and Metricbeat, send to Logstash or Messaging Queue (Kafka, Redis), then to Elasticsearch for processing (Master, Ingest, Data nodes), and finally to Kibana for visualization.](https://academy.hackthebox.com/storage/modules/211/elastic1.png)

Let's delve into each component of the Elastic stack.

`Elasticsearch` is a distributed and JSON-based search engine, designed with RESTful APIs. As the core component of the Elastic stack, it handles indexing, storing, and querying. Elasticsearch empowers users to conduct sophisticated queries and perform analytics operations on the log file records processed by Logstash.

`Logstash` is responsible for collecting, transforming, and transporting log file records. Its strength lies in its ability to consolidate data from various sources and normalize them. Logstash operates in three main areas:

1. `Process input`: Logstash ingests log file records from remote locations, converting them into a format that machines can understand. It can receive records through different [input methods](https://www.elastic.co/guide/en/logstash/current/input-plugins.html), such as reading from a flat file, a TCP socket, or directly from syslog messages. After processing the input, Logstash proceeds to the next function.
    
2. `Transform and enrich log records`: Logstash offers numerous ways to [modify a log record](https://www.elastic.co/guide/en/logstash/current/filter-plugins.html)'s format and even content. Specifically, filter plugins can perform intermediary processing on an event, often based on a predefined condition. Once a log record is transformed, Logstash processes it further.
    
3. `Send log records to Elasticsearch`: Logstash utilizes [output plugins](https://www.elastic.co/guide/en/logstash/current/output-plugins.html) to transmit log records to Elasticsearch.
    

`Kibana` serves as the visualization tool for Elasticsearch documents. Users can view the data stored in Elasticsearch and execute queries through Kibana. Additionally, Kibana simplifies the comprehension of query results using tables, charts, and custom dashboards.

Note: `Beats` is an additional component of the Elastic stack. These lightweight, single-purpose data shippers are designed to be installed on remote machines to forward logs and metrics to either Logstash or Elasticsearch directly. Beats simplify the process of collecting data from various sources and ensure that the Elastic Stack receives the necessary information for analysis and visualization.

`Beats` -> `Logstash` -> `Elasticsearch` -> `Kibana`

![Beats collect data (Filebeat, Metricbeat), send to Logstash with persistent queues, then to Elasticsearch (Master, Data, ML Nodes), and finally to Kibana for visualization.](https://academy.hackthebox.com/storage/modules/211/beats1.png)

`Beats` -> `Elasticsearch` -> `Kibana`

![Beats collect data (Filebeat, Metricbeat), send to Elasticsearch with uniform nodes, and finally to Kibana for visualization.](https://academy.hackthebox.com/storage/modules/211/beats2.png)
