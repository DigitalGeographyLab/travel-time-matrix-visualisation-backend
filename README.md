# TTM-backend
A backend to serve travel time matrices as geodata.
Matrices are served as catchment polygons in geojson format,
precompressed with gzip.
There is one file for every grid cell / travel mode combination.

This repository holds only what is necessary to serve the ready-made data.
See [preprocessing](https://github.com/DigitalGeographyLab/travel-time-matrix-visualisation-preprocessing)
for producing the data.

## Overview
### Technology
- Nginx as a server
- Kubernetes (OpenShift) for deploying to csc's rahti container cloud

### Server
The approach is to serve geojsons directly from the filesystem as static files.
A modified nginx image is used for this (See `Dockerfile`).
It uses a custom config file
and makes some additional adjustments to run nginx without root.
This is needed to make the image run on OpenShift.

### Deployment
The kubernetes configuration is in `manifest.yml`.
It defines:
- A Deployment that creates and runs a ReplicaSet of server pods
(each running the custom nginx image)
- A PersistentVolumeClaim for persistent data storage
(mounted to all replicas)
- A Service for exposing the deployment network
- A Route for routing traffic from the internet to the Service

## Developing / deploying the backend
### Locally
A container engine such as podman or docker is required.

Run
```console
docker compose up
```
to start the service.

The server serves files from the `geojson/` directory on http://localhost:8080/.
You can copy the contents of `geojson-example/` to `geojson/`
in order to get example data for a single grid cell.

### Deploying to rahti
The command line tool `oc` is required for interacting with rahti.

After authenticating you can create a deployment with:
```console
oc create -f manifest.yml
```

To get data to rahti, run:
```console
oc rsync </local/dir> <POD-name>:/usr/share/nginx/geojson
```

You can get the pod name with `oc get pods`.
Confusingly, it does not matter which pod you clone to,
since all replicas share the persistent volume.

You can delete everything except the PersistentVolume with:
```console
oc delete all --selector app=ttm
```

The volume stays intact, and all data is ready for next deploy.
