FROM centos

EXPOSE 3000 9090

WORKDIR /opt
ADD grafana.repo /etc/yum.repos.d/
RUN yum -y install git python-setuptools grafana
RUN easy_install supervisor
RUN curl -L -o prometheus.tar.gz https://github.com/prometheus/prometheus/releases/download/0.16.2/prometheus-0.16.2.linux-amd64.tar.gz
RUN mkdir prometheus
RUN tar zxf prometheus.tar.gz --strip-components=1 -C prometheus
RUN git clone https://github.com/percona/grafana-dashboards.git
ADD grafana.ini /etc/grafana/
ADD supervisord.conf /etc/
ADD prometheus.yml /opt/prometheus/

VOLUME ["/var/lib/grafana", "/opt/prometheus/data"]

CMD ["supervisord", "-c", "/etc/supervisord.conf"]
