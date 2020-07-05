FROM centos:centos7.7.1908
LABEL maintainer="dagoncal@gmx.com"
LABEL description="Deployment tool for Cloud environments."

# ENV variables
ENV USER=deployer
ENV ANSIBLE_VER=2.9.10
ENV AZCLI_VER=2.8.0
ENV HELM_VER=2.14.3
ENV KUBECTL_VER=1.18.5

# Install Packages
RUN rpm --import https://packages.microsoft.com/keys/microsoft.asc && \
    sh -c 'echo -e "[azure-cli]\nname=Azure CLI\nbaseurl=https://packages.microsoft.com/yumrepos/azure-cli\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/azure-cli.repo' && \
    sh -c 'echo -e "[kubernetes]\nname=Kubernetes\nbaseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64\nenabled=1\ngpgcheck=1\nrepo_gpgcheck=1\ngpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg" > /etc/yum.repos.d/kubernetes.repo' && \
    yum -y install centos-release-scl epel-release && \
    yum -y install rh-python36 python36-pip && \
    yum -y install azure-cli-${AZCLI_VER}-1.el7 sudo git kubectl bind-utils openssl jq wget java-1.8.0-openjdk

RUN yum clean all && rm -rf /var/cache/yum

# Helm client install
RUN wget -q https://get.helm.sh/helm-v${HELM_VER}-linux-amd64.tar.gz -O /tmp/helm.tar.gz && \
    tar -zxvf /tmp/helm.tar.gz -C /tmp && \
    mv /tmp/linux-amd64/helm /usr/bin/helm && \
    rm -rf /tmp/*

RUN pip3 install ansible[azure]==${ANSIBLE_VER} msrestazure dnspython netaddr psycopg2-binary jinja2 docker packaging pyOpenSSL openshift datadog pexpect

# Create new user
RUN groupadd ${USER} && useradd -s /bin/bash -g ${USER} ${USER} && \
    echo "${USER} ALL=(root) NOPASSWD:ALL" > /etc/sudoers.d/user && \
    chmod 0440 /etc/sudoers.d/user

# Switch workdir
WORKDIR /home/${USER}

# Switch user
USER ${USER}

# The the playbook install these for now - 
# aks does not pr
# RUN az extension add --name aks-preview && \
#     az extension add --name azure-firewall
