FROM eclipse-temurin:21-jdk

SHELL ["/bin/bash", "-eo", "pipefail", "-c"]

RUN apt-get update \
 && apt-get install -y --no-install-recommends curl ca-certificates git unzip jq \
 && rm -rf /var/lib/apt/lists/*

# The /home/runner bind-mount in copilot-setup-steps.yml causes a UID mismatch
# between the host runner (UID 1001) and this container (root). Git refuses
# to operate on repos with "dubious ownership" — pre-trust any directory so
# the Copilot harness can clone + run git commands against the workspace.
RUN git config --system --add safe.directory '*'

# The Copilot agent session's bootstrap script hardcodes host paths like
# /home/runner/work/_temp/ghcca-node/node/bin/node, but inside a container job
# the runner's temp dir is mounted at /__w/_temp. setup-steps.yml's
# --volume /home/runner:/home/runner only applies to that workflow; the agent
# session is launched separately and doesn't inherit those options. Symlinking
# /home/runner/work/_temp -> /__w/_temp at image-build time makes the host-style
# path resolve to the real temp dir whether the bind-mount is active or not.
RUN mkdir -p /home/runner/work && ln -sfn /__w/_temp /home/runner/work/_temp

RUN curl -fsSL https://dev.moderne.io/cli | bash

ENV PATH="/root/.moderne/cli/bin:${PATH}"

# The CLI bundle ships with its own Java 25 JRE; the wrapper's MIN_JAVA_VERSION
# is 25 but find_java picks the system $JAVA_HOME (Java 21 from this image's
# base) before checking the bundled JRE, leading to ClassNotFoundException at
# runtime. Pin the wrapper to the bundled JRE explicitly.
ENV MODERNE_JAVA_HOME=/root/.moderne/cli/dist/jre

# Track the latest snapshot from Maven Central Snapshots. Requires `unzip` in
# the base image: modw reads Implementation-Version from the JAR manifest via
# `unzip -p` to compute the classpath extraction dir, and falls back to
# version.txt without it. For SNAPSHOT installs those differ
# (4.3.0-SNAPSHOT in manifest vs. timestamped form in version.txt), and the
# resulting path mismatch leaves the JVM with an empty classpath →
# ClassNotFoundException: io.moderne.cli.commands.Mod.
RUN mod wrapper --global --auto-update-snapshot
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
