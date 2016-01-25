FROM centos

EXPOSE 9104 9204 9304 9100

WORKDIR /opt
RUN yum -y install python-setuptools
RUN easy_install supervisor
ADD node_exporter /opt/
ADD mysqld_exporter /opt/
ADD supervisord.conf /etc/

CMD ["supervisord", "-c", "/etc/supervisord.conf"]
