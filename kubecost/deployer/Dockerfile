FROM debian:latest AS build

RUN apt-get update \
    && apt-get install -y --no-install-recommends gettext

ADD chart/kubecost /tmp/chart
RUN cd /tmp \
    && tar -czvf /tmp/kubecost.tar.gz chart
ADD apptest/deployer/kubecost /tmp/test/chart
RUN cd /tmp/test \
    && tar -czvf /tmp/test/kubecost.tar.gz chart/

ADD schema.yaml /tmp/schema.yaml

ADD apptest/deployer/schema.yaml /tmp/apptest/schema.yaml

FROM gcr.io/cloud-marketplace-tools/k8s/deployer_helm/
COPY --from=build /tmp/kubecost.tar.gz /data/chart/
COPY --from=build /tmp/test/kubecost.tar.gz /data-test/chart/
COPY --from=build /tmp/apptest/schema.yaml /data-test/
COPY --from=build /tmp/schema.yaml /data/

ENV WAIT_FOR_READY_TIMEOUT 800
ENV TESTER_TIMEOUT 800