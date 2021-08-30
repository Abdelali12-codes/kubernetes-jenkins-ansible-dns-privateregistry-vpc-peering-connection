FROM fluent/fluentd:v1.12.0-debian-1.0

USER root
RUN mkdir /var/log/fluentd
RUN chown -R fluent:fluent /var/log/fluentd
RUN chmod 660  /var/log/fluentd
RUN ["gem", "install", "fluent-plugin-elasticsearch", "--no-document", "--version", "5.0.3"]
COPY fluent.conf /fluentd/etc
USER fluent