FROM ubuntu:20.04

RUN apt-get update && apt-get install -y vsftpd && \
    mkdir -p /var/ftp/pub && \
    mkdir -p /var/run/vsftpd/empty

RUN echo "Camera_Log_2010.txt" > /var/ftp/pub/log.txt
RUN echo "Note_for_admin.txt" > /var/ftp/pub/readme.txt

RUN echo "shurayz{Hidden_Files_And_Rate_Limits_Are_Fun}" > /var/ftp/pub/.flag_secret.txt

RUN rm /etc/vsftpd.conf

RUN echo "listen=YES" >> /etc/vsftpd.conf && \
    echo "anonymous_enable=YES" >> /etc/vsftpd.conf && \
    echo "no_anon_password=YES" >> /etc/vsftpd.conf && \
    echo "local_enable=YES" >> /etc/vsftpd.conf && \
    echo "dirmessage_enable=YES" >> /etc/vsftpd.conf && \
    echo "use_localtime=YES" >> /etc/vsftpd.conf && \
    echo "xferlog_enable=YES" >> /etc/vsftpd.conf && \
    echo "connect_from_port_20=YES" >> /etc/vsftpd.conf && \
    echo "secure_chroot_dir=/var/run/vsftpd/empty" >> /etc/vsftpd.conf && \
    echo "pam_service_name=vsftpd" >> /etc/vsftpd.conf && \
    echo "anon_root=/var/ftp/pub" >> /etc/vsftpd.conf && \
    echo "max_clients=5" >> /etc/vsftpd.conf && \
    echo "max_per_ip=2" >> /etc/vsftpd.conf

EXPOSE 21 20

CMD ["/usr/sbin/vsftpd", "/etc/vsftpd.conf"]
