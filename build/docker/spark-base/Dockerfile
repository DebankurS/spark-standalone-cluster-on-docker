FROM base
LABEL manteiner="Andre Perez <andre.marcos.perez@gmail.com>"

# -- Layer: Image Metadata

ARG build_date

LABEL org.label-schema.build-date=${build_date}
LABEL org.label-schema.name="Apache Spark Standalone Cluster on Docker - Spark Base Image"
LABEL org.label-schema.description="Spark base image shipped Spark"
LABEL org.label-schema.url="https://github.com/andre-marcos-perez/spark-cluster-on-docker"
LABEL org.label-schema.schema-version="1.0"

# -- Layer: Apache Spark

ARG spark_version
ARG hadoop_version

RUN curl https://archive.apache.org/dist/spark/spark-${spark_version}/spark-${spark_version}-bin-hadoop${hadoop_version}.tgz -o spark.tgz && \
    tar -xf spark.tgz && \
    mv spark-${spark_version}-bin-hadoop${hadoop_version} /usr/bin/ && \
    echo "alias pyspark=/usr/bin/spark-${spark_version}-bin-hadoop${hadoop_version}/bin/pyspark" >> ~/.bashrc && \
    echo "alias spark-shell=/usr/bin/spark-${spark_version}-bin-hadoop${hadoop_version}/bin/spark-shell" >> ~/.bashrc && \
    mkdir /usr/bin/spark-${spark_version}-bin-hadoop${hadoop_version}/logs && \
    rm spark.tgz

RUN python3 -m pip install -U pip && \
 python3 -m pip install wget mlflow hyperopt scikit-learn matplotlib seaborn jupyterlab-sparkmonitor ipywidgets sparksql-magic xgboost \
 lightgbm sparkdl tensorflow keras tensorframes horovod delta delta-spark


ENV SPARK_HOME /usr/bin/spark-${spark_version}-bin-hadoop${hadoop_version}
ENV SPARK_MASTER_HOST spark-master
ENV SPARK_MASTER_PORT 7077
ENV PYSPARK_PYTHON python3


# RUN cp $SPARK_HOME/conf/metrics.properties.template $SPARK_HOME/conf/metrics.properties

# RUN echo "*.sink.prometheusServlet.class=org.apache.spark.metrics.sink.PrometheusServlet" >> $SPARK_HOME/conf/metrics.properties
# RUN echo "*.sink.prometheusServlet.path=/metrics/prometheus" >> $SPARK_HOME/conf/metrics.properties
# RUN echo "master.sink.prometheusServlet.path=/metrics/master/prometheus" >> $SPARK_HOME/conf/metrics.properties
# RUN echo "applications.sink.prometheusServlet.path=/metrics/applications/prometheus" >> $SPARK_HOME/conf/metrics.properties


# RUN echo "*.sink.servlet.class" = "org.apache.spark.metrics.sink.MetricsServlet"  >> $SPARK_HOME/conf/metrics.properties
# RUN echo "*.sink.servlet.path" = "/metrics/json"  >> $SPARK_HOME/conf/metrics.properties
# RUN echo "master.sink.servlet.path" = "/metrics/master/json"  >> $SPARK_HOME/conf/metrics.properties
# RUN echo "applications.sink.servlet.path" = "/metrics/applications/json"  >> $SPARK_HOME/conf/metrics.properties

# RUN cp $SPARK_HOME/conf/spark-defaults.conf.template $SPARK_HOME/conf/spark-defaults.conf

# RUN echo "spark.ui.prometheus.enabled                           true" >> $SPARK_HOME/conf/spark-defaults.conf

# -- Runtime

WORKDIR ${SPARK_HOME}