FROM ubuntu:20.04

RUN apt-get update && apt-get install -y \
    openssh-server \
    sudo \
    vim \
    iputils-ping \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir /var/run/sshd

RUN echo "root:ThisIsAVeryHardPasswordToCrack123!" | chpasswd
RUN echo "shurayz{Vim_Sudo_Privilege_Escalation}" > /root/flag.txt

RUN useradd -m -s /bin/bash developer

RUN passwd -d developer

RUN echo "developer ALL=(root) NOPASSWD: /usr/bin/vim" >> /etc/sudoers

RUN sed -i 's/#PermitEmptyPasswords no/PermitEmptyPasswords yes/' /etc/ssh/sshd_config
RUN sed -i 's/UsePAM yes/UsePAM no/' /etc/ssh/sshd_config

EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]