YQ ?= $(TOOLS_DIR)/yq
YQ_VERSION = v4.44.2
OLM_VERSION = v0.28.0
CRDS_FILE = templates/crds.yaml

## Location to install dependencies to
TOOLS_DIR ?= $(shell pwd)/tools
$(TOOLS_DIR):
	mkdir -p $(TOOLS_DIR)

.PHONY: yq $(YQ)
yq: $(YQ) ## Download yq locally if necessary. If wrong version is installed, it will be overwritten.
$(YQ): $(TOOLS_DIR)
	test -s $(TOOLS_DIR)/yq && $(TOOLS_DIR)/yq --version | grep -q $(YQ_VERSION) || \
	GOBIN=$(TOOLS_DIR) go install github.com/mikefarah/yq/v4@$(YQ_VERSION)

.PHONY: bump-olm-crd
bump-olm-crd: ## Bump the OLM CRDs to the specified version
	curl -sSL -o $(CRDS_FILE) https://github.com/operator-framework/operator-lifecycle-manager/releases/download/$(OLM_VERSION)/crds.yaml
	$(MAKE) patch-olm-crds

.PHONY: patch-crds
patch-olm-crds: yq ## Patch the CRDs with the helm.sh/resource-policy annotation
	@$(YQ) eval 'select(.kind == "CustomResourceDefinition") | .metadata.annotations."helm.sh/resource-policy" = "keep"' -i $(CRDS_FILE)

.PHONY: bump-helm
bump-helm: yq ## Bump the helm chart version
	$(YQ) eval '.appVersion = "$(OLM_VERSION)"' -i Chart.yaml

## Cleanup tool binaries.
.PHONY: clean
clean:
	rm -rf $(TOOLS_DIR)

## Build the helm package.
.PHONY: build-package
build-package:
	helm package .

bump: bump-olm-crd bump-helm clean
