ARG BUILD_FROM

# Development build: compile govee2mqtt from source on the Home Assistant
# host, rather than pulling the release image from ghcr.io/wez.
# GOVEE2MQTT_REF is this branch, which carries the feature commits
# being tested on top of upstream.
# BUILD_VERSION is the add-on version from config.yaml; bump it to make
# the Supervisor fetch and compile the latest commit on GOVEE2MQTT_REF.
FROM rust:1-bookworm AS build
ARG BUILD_VERSION
ARG GOVEE2MQTT_REPO=https://github.com/snoack/govee2mqtt.git
ARG GOVEE2MQTT_REF=ha-addon-dev
RUN apt-get update && apt-get install -y --no-install-recommends cmake \
    && rm -rf /var/lib/apt/lists/*
RUN echo "Building ${GOVEE2MQTT_REF} for add-on version ${BUILD_VERSION}" \
    && git clone --depth 1 --branch "${GOVEE2MQTT_REF}" "${GOVEE2MQTT_REPO}" /src
WORKDIR /src
RUN cargo build --release --locked

FROM $BUILD_FROM
COPY run.sh /run.sh
COPY --from=build /src/target/release/govee /app/
COPY --from=build /src/assets /app/assets/
COPY --from=build /src/AmazonRootCA1.pem /app/
CMD [ "/run.sh" ]
