# TTM-backend
This is a minimal backend to serve travel time matrices as geodata.
Currently the matrix is served as catchment polygons in geojson fromat,
one file per grid cell / travel mode / year combination.

See [preprocessing](https://github.com/DigitalGeographyLab/travel-time-matrix-visualisation-preprocessing)
for producing the data.

## Technology
- Nginx to serve the files

## Local development
The backend service is containerized,
so the only requirement for running it is a container engine such as podman or docker.

Run
```console
docker compose up
```
to start the service.

The server serves files from the `geojson/` directory on http://localhost:8080/.
You can copy the contents of `geojson-example/` to `geojson/`
in order to get example data for a single grid cell.

## Deploying to rahti
Deoploying is done with OpenShift / Kubernetes.
The command line tool `oc` is needed to communicate with openshift.

After authenticating you can create a deployment with:
```console
oc create -f manifest.yml
```
And delete it with:
```console
oc delete all --selector app=ttm
```
