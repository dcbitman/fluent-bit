From amazon/aws-for-fluent-bit:latest

RUN yum -y install ruby-devel gcc -y  && \
    amazon-linux-extras install ruby2.6 -y   && \
    yum groupinstall "Development Tools" -y  && \
    gem install fluent-plugin-s3 --no-document
