FROM registry.access.redhat.com/ubi8/ubi-minimal:8.1

ENV OPERATOR=/usr/local/bin/knative-eventing-operator \
    USER_UID=1001 \
    USER_NAME=knative-eventing-operator
    
# install operator binary
COPY build/_output/bin/knative-eventing-operator ${OPERATOR}

COPY build/bin /usr/local/bin
RUN  /usr/local/bin/user_setup

# install manifest[s]
COPY deploy /deploy

ENTRYPOINT ["/usr/local/bin/entrypoint"]

USER ${USER_UID}
