YQ ?= $(TOOLS_DIR)/yq
YQ_VERSION = v4.44.2
OLM_VERSION = v0.28.0
CRD_CHART_VERSION = 0.3.0-dev
OLM_FILE = templates/olm.yaml

## Location to install dependencies to
TOOLS_DIR ?= $(shell pwd)/tools
$(TOOLS_DIR):
	mkdir -p $(TOOLS_DIR)

.PHONY: yq $(YQ)
yq: $(YQ) ## Download yq locally if necessary. If wrong version is installed, it will be overwritten.
$(YQ): $(TOOLS_DIR)
	test -s $(TOOLS_DIR)/yq && $(TOOLS_DIR)/yq --version | grep -q $(YQ_VERSION) || \
	GOBIN=$(TOOLS_DIR) go install github.com/mikefarah/yq/v4@$(YQ_VERSION)

.PHONY: bump-olm
bump-olm: ## Bump the OLM CRDs to the specified version
	curl -sSL -o $(OLM_FILE) https://github.com/operator-framework/operator-lifecycle-manager/releases/download/$(OLM_VERSION)/olm.yaml

.PHONY: bump-helm
bump-helm: yq ## Bump the helm chart version
	$(YQ) eval '.appVersion = "$(OLM_VERSION)"' -i Chart.yaml

## Cleanup tool binaries.
.PHONY: clean
clean:
	rm -rf $(TOOLS_DIR)


.PHONY: patch-olm
patch-olm:
	sed -i 's|quay.io/operator-framework/olm.*|quay.io/operator-framework/olm:$(OLM_VERSION)|g' $(OLM_FILE)

## Build the helm package.
.PHONY: build-package
build-package:
	helm dep build
	helm package .

.PHONY: bump
bump: bump-olm patch-olm clean
