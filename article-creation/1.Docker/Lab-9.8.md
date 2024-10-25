### Lab 9.8: Monitoring and Logging in Docker with ELK Stack

In this lab, you will learn how to set up monitoring and logging for Docker containers using the **ELK Stack** (Elasticsearch, Logstash, and Kibana). The ELK Stack provides powerful tools for aggregating logs, analyzing them, and visualizing them in a meaningful way. You will also configure **Metricbeat** to collect metrics and monitor the performance of your Docker containers.

By the end of this lab, you will:
1. Set up Elasticsearch, Logstash, and Kibana (ELK Stack) using Docker.
2. Collect logs from Docker containers using Logstash.
3. Visualize logs and metrics in Kibana.
4. Monitor Docker container metrics using Metricbeat.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
   1. Set Up the ELK Stack in Docker
   2. Collect Docker Logs Using Logstash
   3. Visualize Logs and Metrics in Kibana
   4. Monitor Docker Metrics with Metricbeat
3. **Verification**
4. **Conclusion**

---

## Introduction

Monitoring and logging are critical components in managing a production environment. The **ELK Stack** provides an efficient way to collect, store, and analyze logs and performance metrics from Docker containers. In this lab, you will deploy the ELK Stack inside Docker containers and configure it to collect and visualize logs and metrics from other running containers.

In this lab, we will:
1. Set up Elasticsearch to store logs and metrics.
2. Use Logstash to collect and process Docker logs.
3. Visualize the logs and metrics in Kibana.
4. Monitor Docker container performance using Metricbeat.

---

## Step-by-Step Instructions

### Step 1: Set Up the ELK Stack in Docker

#### 1.1 Create a Docker Compose File for the ELK Stack

We will deploy the ELK Stack using Docker Compose. Create a `docker-compose.yml` file to configure **Elasticsearch**, **Logstash**, and **Kibana**:

```yaml
version: '3'

services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.10.1
    container_name: elasticsearch
    environment:
      - discovery.type=single-node
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ports:
      - "9200:9200"
    networks:
      - elk

  logstash:
    image: docker.elastic.co/logstash/logstash:7.10.1
    container_name: logstash
    volumes:
      - ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf
    ports:
      - "5000:5000"
    networks:
      - elk
    depends_on:
      - elasticsearch

  kibana:
    image: docker.elastic.co/kibana/kibana:7.10.1
    container_name: kibana
    ports:
      - "5601:5601"
    networks:
      - elk
    depends_on:
      - elasticsearch

networks:
  elk:
    driver: bridge
```

Explanation:
- `elasticsearch`: Stores logs and metrics.
- `logstash`: Collects and processes logs.
- `kibana`: Provides a web interface for visualizing logs and metrics.
- All services are connected via the `elk` network.

#### 1.2 Start the ELK Stack

To start the ELK Stack, run the following command:

```bash
docker-compose up -d
```

Once the services are running, you can access **Kibana** at `http://localhost:5601`.

---

### Step 2: Collect Docker Logs Using Logstash

#### 2.1 Configure Logstash to Collect Docker Logs

Create a `logstash.conf` file to define the pipeline for Logstash. This configuration will tell Logstash to collect Docker container logs:

```plaintext
input {
  tcp {
    port => 5000
    codec => "json"
  }
}

filter {
  if [docker][container][name] {
    mutate {
      add_field => { "container_name" => "%{[docker][container][name]}" }
    }
  }
}

output {
  elasticsearch {
    hosts => ["http://elasticsearch:9200"]
    index => "docker-logs-%{+YYYY.MM.dd}"
  }
}
```

Explanation:
- The input is set to receive logs over TCP on port 5000, using the `json` codec.
- The filter extracts the Docker container name and adds it as a field.
- The output sends logs to Elasticsearch, with logs stored in an index named `docker-logs-YYYY.MM.dd`.

#### 2.2 Send Logs from Docker Containers to Logstash

To forward logs from a Docker container to Logstash, you can use Docker’s `log-driver` feature. Start a new container and configure it to send logs to Logstash:

```bash
docker run -d --name nginx --log-driver=syslog --log-opt syslog-address=tcp://<LOGSTASH_IP>:5000 nginx:latest
```

Explanation:
- `--log-driver=syslog`: Configures Docker to use the syslog logging driver.
- `--log-opt syslog-address=tcp://<LOGSTASH_IP>:5000`: Sends logs to Logstash over TCP on port 5000.

You can replace `<LOGSTASH_IP>` with the IP address of your Logstash container.

---

### Step 3: Visualize Logs and Metrics in Kibana

#### 3.1 Access Kibana

Open your browser and navigate to `http://localhost:5601` to access the Kibana dashboard. On the first login, you will need to configure Kibana to index the logs stored in Elasticsearch.

#### 3.2 Create an Index Pattern in Kibana

To visualize the Docker logs, create an index pattern for the logs in Kibana:
1. Go to **Management** -> **Stack Management** -> **Index Patterns**.
2. Click on **Create Index Pattern**.
3. Enter `docker-logs-*` as the index pattern, and select the default time filter.

Now, you can visualize the logs in the **Discover** section of Kibana.

---

### Step 4: Monitor Docker Metrics with Metricbeat

#### 4.1 Set Up Metricbeat

**Metricbeat** is an Elasticsearch Beat that can collect metrics from Docker containers. Create a `docker-compose.yml` file for Metricbeat:

```yaml
version: '3'

services:
  metricbeat:
    image: docker.elastic.co/beats/metricbeat:7.10.1
    container_name: metricbeat
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./metricbeat.yml:/usr/share/metricbeat/metricbeat.yml
    networks:
      - elk
    depends_on:
      - elasticsearch
```

Explanation:
- `metricbeat`: Collects metrics from Docker containers and forwards them to Elasticsearch.
- The Docker socket is mounted to allow Metricbeat to gather metrics.

#### 4.2 Configure Metricbeat

Create a `metricbeat.yml` configuration file for Metricbeat:

```yaml
metricbeat.modules:
  - module: docker
    metricsets:
      - container
      - cpu
      - diskio
      - memory
      - network
    hosts: ["unix:///var/run/docker.sock"]
    period: 10s
    enabled: true

output.elasticsearch:
  hosts: ["http://elasticsearch:9200"]
  index: "docker-metrics-%{+yyyy.MM.dd}"
```

Explanation:
- `metricsets`: The metrics you want to collect (e.g., container CPU, memory, network).
- `output.elasticsearch`: Configures Metricbeat to send metrics to Elasticsearch.

#### 4.3 Start Metricbeat

Start Metricbeat using Docker Compose:

```bash
docker-compose up -d metricbeat
```

#### 4.4 Visualize Metrics in Kibana

In Kibana, create a new index pattern for the metrics:
1. Go to **Management** -> **Stack Management** -> **Index Patterns**.
2. Create an index pattern with `docker-metrics-*`.
3. Visualize the metrics in the **Metrics** section of Kibana.

---

## Verification

To verify that the lab has been completed successfully, check the following:
1. Logstash is collecting logs from Docker containers and storing them in Elasticsearch.
2. You can visualize the logs in Kibana using the `docker-logs-*` index pattern.
3. Metricbeat is collecting metrics from Docker containers and sending them to Elasticsearch.
4. You can visualize container metrics in Kibana using the `docker-metrics-*` index pattern.

---

## Conclusion

In this lab, you learned how to:
1. Set up the ELK Stack (Elasticsearch, Logstash, and Kibana) using Docker.
2. Collect and visualize logs from Docker containers using Logstash and Kibana.
3. Monitor Docker container performance with Metricbeat and Elasticsearch.

By integrating the ELK Stack into your Docker environment, you gain powerful logging and monitoring capabilities that provide insight into both the logs and metrics of your containers.

---

### Example Output of `docker-compose up`:

```plaintext
Creating elasticsearch ... done
Creating logstash      ... done
Creating kibana        ... done
Creating metricbeat    ... done
```

Congratulations on completing **Lab 9.8: Monitoring and Logging in Docker with ELK Stack**! You are now capable of setting up a complete logging and monitoring solution for your Dockerized applications.

Happy Dockering!