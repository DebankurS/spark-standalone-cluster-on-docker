FROM base
LABEL manteiner="Andre Perez <andre.marcos.perez@gmail.com>"

# -- Layer: Image Metadata

ARG build_date

LABEL org.label-schema.build-date=${build_date}
LABEL org.label-schema.name="Apache Spark Standalone Cluster on Docker - JupyterLab Image"
LABEL org.label-schema.description="JupyterLab image"
LABEL org.label-schema.url="https://github.com/andre-marcos-perez/spark-cluster-on-docker"
LABEL org.label-schema.schema-version="1.0"

# -- Layer: Notebooks and data

COPY workspace/ ${SHARED_WORKSPACE}/

# -- Layer: JupyterLab + Python kernel for PySpark

ARG spark_version
ARG jupyterlab_version
ARG scala_kernel_version

RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y python3-pip python3-dev wget && \
    pip3 install --upgrade pip && \
    pip3 install wget pyspark==${spark_version} jupyterlab==${jupyterlab_version} mlflow hyperopt scikit-learn matplotlib seaborn jupyterlab-sparkmonitor ipywidgets sparksql-magic  xgboost lightgbm \
    sparkdl tensorflow keras tensorframes horovod delta delta-spark

RUN rm -rf ~/.ipython
RUN ipython profile create
RUN echo "c.InteractiveShellApp.extensions.append('sparkmonitor.kernelextension')" >>  ~/.ipython/profile_default/ipython_config.py
# RUN jupyter nbextension enable --py --sys-prefix widgetsnbextension 
# RUN jupyter labextension install "@jupyter-widgets/jupyterlab-manager"

# RUN jupyter notebook --generate-config
# COPY ./jupyter ~/.jupyter

ARG spark_version
ARG hadoop_version

RUN curl https://archive.apache.org/dist/spark/spark-${spark_version}/spark-${spark_version}-bin-hadoop${hadoop_version}.tgz -o spark.tgz && \
    tar -xf spark.tgz && \
    mv spark-${spark_version}-bin-hadoop${hadoop_version} /usr/bin/ && \
    echo "alias pyspark=/usr/bin/spark-${spark_version}-bin-hadoop${hadoop_version}/bin/pyspark" >> ~/.bashrc && \
    echo "alias spark-shell=/usr/bin/spark-${spark_version}-bin-hadoop${hadoop_version}/bin/spark-shell" >> ~/.bashrc && \
    mkdir /usr/bin/spark-${spark_version}-bin-hadoop${hadoop_version}/logs && \
    rm spark.tgz

ENV SPARK_HOME /usr/bin/spark-${spark_version}-bin-hadoop${hadoop_version}


# -- Layer: Scala kernel for Spark

ARG scala_version

# RUN apt-get install -y ca-certificates-java --no-install-recommends && \
#     curl -Lo coursier https://git.io/coursier-cli && \
#     chmod +x coursier && \
#     ./coursier launch --fork almond:${scala_kernel_version} --scala ${scala_version} -- --display-name "Scala ${scala_version}" --install && \
#     rm -f coursier

COPY ./docker/jupyterlab/coursier coursier

RUN apt-get install -y ca-certificates-java --no-install-recommends && \
    chmod +x coursier && \
    ./coursier launch --fork almond:${scala_kernel_version} --scala ${scala_version} -- --display-name "Scala ${scala_version}" --install && \
    rm -f coursier


# -- Layer: R kernel for SparkR

RUN apt-get install -y r-base-dev && \
    R -e "install.packages('IRkernel')" && \
    R -e "IRkernel::installspec(displayname = 'R 3.5', user = FALSE)" && \
    curl https://archive.apache.org/dist/spark/spark-${spark_version}/SparkR_${spark_version}.tar.gz -k -o sparkr.tar.gz && \
    R CMD INSTALL sparkr.tar.gz && \
    rm -f sparkr.tar.gz

# -- Runtime

EXPOSE 8888

WORKDIR ${SHARED_WORKSPACE}
CMD jupyter lab --ip=0.0.0.0 --port=8888 --no-browser --allow-root --NotebookApp.token=
