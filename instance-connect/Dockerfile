FROM alpine

#RUN apk add openrc

RUN apk add openssh
RUN ssh-keygen -A; ssh-keygen -q -t rsa -N '' -f ~/.ssh/id_rsa
RUN cat /root/.ssh/id_rsa.pub > /key.pub && chmod +r /key.pub
RUN echo '#!/bin/ash' > /test.sh
RUN echo 'cat /key.pub' >> /test.sh
RUN echo '#head -c 100 /key.pub' >> /test.sh

RUN chmod 755 /test.sh
RUN echo 'AuthorizedKeysCommand /test.sh' >> /etc/ssh/sshd_config
RUN echo 'AuthorizedKeysCommandUser nobody' >> /etc/ssh/sshd_config
CMD /usr/sbin/sshd; ash
#RUN rc-update add sshd

