For development and testing purposes, sometimes its useful to build images locally and push them directly to a Kubernetes cluster.

This can be achieved by running a docker registry in the cluster and using a special repo prefix such as "127.0.0.1:5000/" that will be seen as an insecure registry url.

# Start/Stop private registry in cluster

## start private registry<a id="sec-1-1" name="sec-1-1"></a>

    ./start-docker-private-registry

## stop private registry

    ./stop-docker-private-registry

# Registry access<a id="sec-2" name="sec-2"></a>

## Access the registry via port forwarding

Use the docker-private-registry pod to forward the port

    ./port-forward-registry

Then use the address/port

    127.0.0.1:5000

## Test the registry

Check if the registry catalog can be accessed and the ability to push an image.

    (set -x && curl -X GET http://127.0.0.1:5000/v2/_catalog && docker pull busybox && docker tag busybox 127.0.0.1:5000/busybox && docker push 127.0.0.1:5000/busybox)

# Example Build and Deployment

    cd example

    # build and push the image to the registry
    make build_image && make push_to_registry

    # create a simple deployment
    make create_deployment

    # check details of the deployed pod to ensure its using the built image
    make describe_pod

# Troubleshooting

-   Check the Insecure Registries list

    Log into each node in the cluster and ensure that the docker daemon is running with the right "Insecure Registries' list.

    ```sh
    docker info|grep -i -A5 'Insecure Registries'
    ```

- Docker for Mac issue  

    You may see an error like the following

    ```
    Get http://127.0.0.1:5000/v2/: dial tcp 127.0.0.1:5000: getsockopt: connection refused
    ```

    Since docker is running in a VM on the mac, its not possible to directly use "127.0.0.1:5000".  
    Use socat running in a container as a workaround.

    ```sh
    docker run --rm --privileged \
        --pid=host gsunner/ubuntu-socat \
        nsenter -t 1 -u -n -i socat TCP-LISTEN:5000,fork TCP:docker.for.mac.host.internal:5000
    ```

