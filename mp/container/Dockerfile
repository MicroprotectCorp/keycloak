# MP build of Keycloak 26.5.7 on Amazon Corretto 21 (CVE-2026-114938 "Java4Shell" fixed JDK).
# Copies /opt/keycloak from the upstream image onto Corretto 21.0.13.1.2. Remove when the upstream
# image ships a fixed JDK. Build: docker buildx build --platform linux/amd64 -f mp/container/Dockerfile mp/container
ARG KEYCLOAK_IMAGE=quay.io/keycloak/keycloak:26.5.7@sha256:45ae20191531eb608ddb0b775d012b40d3e4f942697f3214694887dd7c108d13
ARG CORRETTO_IMAGE=public.ecr.aws/amazoncorretto/amazoncorretto:21-al2023-headless
ARG EXPECTED_JDK=21.0.13.1.2

FROM ${KEYCLOAK_IMAGE} AS kc

FROM ${CORRETTO_IMAGE}
ARG EXPECTED_JDK
RUN dnf install -y findutils glibc-langpack-en shadow-utils && dnf clean all && \
    groupadd -g 1001 keycloak && useradd -u 1000 -g 0 -d /opt/keycloak -s /sbin/nologin keycloak && \
    java -version 2>&1 | grep -q "Corretto-${EXPECTED_JDK}" || { java -version; echo "JDK is not Corretto-${EXPECTED_JDK}"; exit 1; }
ENV LANG=en_US.UTF-8 KC_RUN_IN_CONTAINER=true
COPY --from=kc --chown=1000:0 /opt/keycloak /opt/keycloak
USER 1000
EXPOSE 8080 8443 9000
ENTRYPOINT ["/opt/keycloak/bin/kc.sh"]
