#REPO_HOST=docker.io
REPO_HOST=127.0.0.1:5000
IMAGE_NAME:=${REPO_HOST}/seldonio/nginx_test

build_image:
	docker build --force-rm=true -t $(IMAGE_NAME) .

push_to_registry:
	@docker push $(IMAGE_NAME)

create_deployment:
	@kubectl apply -f nginx-test-deployment.json -n default

delete_deployment:
	@kubectl delete -f nginx-test-deployment.json --ignore-not-found -n default

port-forward-docker-registry:
	@POD_NAME=$$(kubectl get pods -l app=docker-private-registry -n default | sed -e '1d'|awk '{print $$1}') && kubectl port-forward $${POD_NAME} 5000:5000 -n default

describe_pod:
	@POD_NAME=$$(kubectl get pods -l app=nginx-test -n default | sed -e '1d'|awk '{print $$1}') && kubectl describe pod $${POD_NAME} -n default

