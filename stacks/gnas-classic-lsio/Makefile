.PHONY: help
.DEFAULT_GOAL := help

# Self-documenting Makefile based on code written by [François Zaninotto](http://bit.ly/2PYuVj1)

help:
	@echo "Make targets for GNAS Classic Media Server Portainer/Docker-Compose Stack"
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

clean: ## Sanitize the workspace by removing containers
	@echo "Sanitize the workspace by removing containers"
	-docker-compose rm -sfv

get-deps: ## Retrieve dependencies locally
	@echo "Retrieve dependencies locally"
	@docker-compose pull -q

build-stack: test-docker-compose ## Build an image from the Docker-Compose file
	@echo "Build an image from the Docker-Compose file"
	@docker-compose build --parallel

test-docker-compose: ## Lint-test the Docker-Compose file
	@echo "Lint-test the Docker-Compose file"
	@docker-compose config -q

ci: test-docker-compose  ## Run all tests (but just lint for now)
