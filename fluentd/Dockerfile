FROM fluentd:v1.19-1

USER root
RUN apt-get update \
    && apt-get install -y --no-install-recommends build-essential ruby-dev \
    && fluent-gem install fluent-plugin-elasticsearch \
    && fluent-gem install fluent-plugin-kubernetes_metadata_filter \
    && fluent-gem install fluent-plugin-multi-format-parser \
    && apt-get purge -y --auto-remove build-essential ruby-dev \
    && rm -rf /var/lib/apt/lists/*

USER fluent
