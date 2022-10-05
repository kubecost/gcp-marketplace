include ./crd.Makefile
include ./gcloud.Makefile
include ./var.Makefile
include ./images.Makefile

CHART_NAME := kubecost
APP_ID ?= $(CHART_NAME)

TRACK ?= prod-1.97.0
TRACK_PROMETHEUS ?= v2.35.0

SOURCE_REGISTRY ?= gcr.io/kubecost1
IMAGE_PROMETHEUS ?= quay.io/prometheus/prometheus:$(TRACK_PROMETHEUS)
IMAGE_COSTMODEL ?= $(SOURCE_REGISTRY)/cost-model:$(TRACK)
IMAGE_FRONTEND ?= $(SOURCE_REGISTRY)/frontend:$(TRACK)

# Main image
# image-$(CHART_NAME) := $(call get_sha256,$(IMAGE_PROMETHEUS))

# List of images used in application
#ADDITIONAL_IMAGES := alertmanager nodeexporter kubestatemetrics grafana init-container

# Additional images variable names should correspond with ADDITIONAL_IMAGES list
image-prometheus := $(call get_sha256,$(IMAGE_PROMETHEUS))
image-costmodel := $(call get_sha256,$(IMAGE_COSTMODEL))
image-frontend := $(call get_sha256,$(IMAGE_FRONTEND))


C2D_CONTAINER_RELEASE := $(call get_c2d_release,$(image-$(CHART_NAME)))

BUILD_ID := $(shell date --utc +%Y%m%d-%H%M%S)
RELEASE ?= $(C2D_CONTAINER_RELEASE)-$(BUILD_ID)
VERIFY_WAIT_TIMEOUT := 800
NAME ?= $(APP_ID)-1

APP_PARAMETERS ?= { \
  "name": "$(NAME)", \
  "namespace": "$(NAMESPACE)" \
}

# c2d_deployer.Makefile provides the main targets for installing the application.
# It requires several APP_* variables defined above, and thus must be included after.
include ../c2d_deployer.Makefile


# Build tester image
app/build:: .build/$(CHART_NAME)/tester
