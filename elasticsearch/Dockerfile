FROM elasticsearch:7.16.3

RUN rm /usr/share/elasticsearch/config/elasticsearch.yml
COPY ./config/elasticsearch.yml /usr/share/elasticsearch/config/

Entrypoint ["/bin/tini", "--", "/usr/local/bin/docker-entrypoint.sh" ]