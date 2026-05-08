FROM eclipse-temurin:21-jdk

SHELL ["/bin/bash", "-eo", "pipefail", "-c"]

RUN apt-get update \
 && apt-get install -y --no-install-recommends curl ca-certificates git \
 && rm -rf /var/lib/apt/lists/*

# Pin the wrapper to LATEST so `mod` self-updates on first runtime invocation if needed.
RUN curl -fsSL https://dev.moderne.io/cli | bash \
 && echo "version=LATEST" > /root/.moderne/cli/dist/moderne-wrapper.properties

ENV PATH="/root/.moderne/cli/bin:${PATH}"

RUN mod --version
RUN mod config http trust-store edit file "$JAVA_HOME/lib/security/cacerts"
RUN mod config recipes artifacts maven add https://repo.maven.apache.org/maven2

# Pre-install all recipe jars
RUN mod config recipes jar install \
      io.moderne.recipe:rewrite-cryptography \
      io.moderne.recipe:rewrite-dropwizard \
      io.moderne.recipe:rewrite-elastic \
      io.moderne.recipe:rewrite-hibernate \
      io.moderne.recipe:rewrite-jasperreports \
      io.moderne.recipe:rewrite-java-application-server \
      io.moderne.recipe:rewrite-kafka \
      io.moderne.recipe:rewrite-spring \
      io.moderne.recipe:rewrite-tapestry \
      io.moderne.recipe:rewrite-vulncheck \
      org.openrewrite:rewrite-core \
      org.openrewrite:rewrite-docker \
      org.openrewrite:rewrite-gradle \
      org.openrewrite:rewrite-groovy \
      org.openrewrite:rewrite-hcl \
      org.openrewrite:rewrite-java \
      org.openrewrite:rewrite-json \
      org.openrewrite:rewrite-maven \
      org.openrewrite:rewrite-properties \
      org.openrewrite:rewrite-protobuf \
      org.openrewrite:rewrite-templating \
      org.openrewrite:rewrite-toml \
      org.openrewrite:rewrite-xml \
      org.openrewrite:rewrite-yaml \
      org.openrewrite.meta:rewrite-analysis \
      org.openrewrite.recipe:rewrite-ai-search \
      org.openrewrite.recipe:rewrite-all \
      org.openrewrite.recipe:rewrite-apache \
      org.openrewrite.recipe:rewrite-circleci \
      org.openrewrite.recipe:rewrite-codemods \
      org.openrewrite.recipe:rewrite-codemods-ng \
      org.openrewrite.recipe:rewrite-compiled-analysis \
      org.openrewrite.recipe:rewrite-concourse \
      org.openrewrite.recipe:rewrite-cucumber-jvm \
      org.openrewrite.recipe:rewrite-dotnet \
      org.openrewrite.recipe:rewrite-feature-flags \
      org.openrewrite.recipe:rewrite-github-actions \
      org.openrewrite.recipe:rewrite-gitlab \
      org.openrewrite.recipe:rewrite-hibernate \
      org.openrewrite.recipe:rewrite-jackson \
      org.openrewrite.recipe:rewrite-java-dependencies \
      org.openrewrite.recipe:rewrite-java-security \
      org.openrewrite.recipe:rewrite-jenkins \
      org.openrewrite.recipe:rewrite-joda \
      org.openrewrite.recipe:rewrite-kubernetes \
      org.openrewrite.recipe:rewrite-liberty \
      org.openrewrite.recipe:rewrite-logging-frameworks \
      org.openrewrite.recipe:rewrite-micrometer \
      org.openrewrite.recipe:rewrite-micronaut \
      org.openrewrite.recipe:rewrite-migrate-java \
      org.openrewrite.recipe:rewrite-migrate-kotlin \
      org.openrewrite.recipe:rewrite-netty \
      org.openrewrite.recipe:rewrite-okhttp \
      org.openrewrite.recipe:rewrite-openapi \
      org.openrewrite.recipe:rewrite-prethink \
      org.openrewrite.recipe:rewrite-quarkus \
      org.openrewrite.recipe:rewrite-reactive-streams \
      org.openrewrite.recipe:rewrite-rewrite \
      org.openrewrite.recipe:rewrite-spring \
      org.openrewrite.recipe:rewrite-spring-to-quarkus \
      org.openrewrite.recipe:rewrite-sql \
      org.openrewrite.recipe:rewrite-static-analysis \
      org.openrewrite.recipe:rewrite-struts \
      org.openrewrite.recipe:rewrite-terraform \
      org.openrewrite.recipe:rewrite-testing-frameworks \
      org.openrewrite.recipe:rewrite-third-party \
 && mod config recipes list | head -20
