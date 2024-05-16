# Tuto: deploy geOrchestra with docker, on production

Here, we are goinig to explain how to adapt the docker composition offered by the georchestra community, to run it on production.
This is probably still not really a production-ready deployment, but will work on a remote server, with a custom domain name and SSL certificate.

## Step 1: clone the official repo
```bash
git clone --recurse-submodules https://github.com/georchestra/docker.git
cd docker 
git checkout 23.0 && git submodule update
```

## Step 2: 
