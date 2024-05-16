# Tuto: deploy geOrchestra with docker, on production

Here, we are goinig to explain how to adapt the docker composition offered by the georchestra community, to run it on production.
This is probably still not really a production-ready deployment, but will work on a remote server, with a custom domain name and SSL certificate.

## Step 1: clone the official repo
```bash
git clone --recurse-submodules https://github.com/georchestra/docker.git
cd docker 
git checkout 23.0 && git submodule update
```

## Step 2: change traefik configuration file
We will add a CertificateResolver:

*resources/traefik_custom.yaml:*
```yaml
global: 
  sendAnonymousUsage: false
  checkNewVersion: false

entryPoints:
  web:
    address: ":80"
    http:
      redirections:
        entryPoint:
          to: websecure
          scheme: https

  websecure:
    address: ":443"

providers:
  docker:
    watch: true
    exposedByDefault: false
    endpoint: unix:///var/run/docker.sock

api:
  dashboard: true

log:
  level: INFO

certificatesResolvers:
  letsEncrypt:
    acme:
      # comment this line when going to production
      caServer: https://acme-staging-v02.api.letsencrypt.org/directory
      email: jean.pommier@pi-geosolutions.fr
      storage: /acme/acme.json
      httpChallenge:
        entryPoint: web

ping: {}
```

## Step 3: update accordingly traefik config in docker-compose-override.yml
In docker-compose-override.yml, 
- we can comment the `traefik-me-certificate-downloader` block
- for `georchestra-127-0-1-1.traefik.me` service:
    - comment the `depends_on` section
    - change the `volumes` section for:
```
    - /var/run/docker.sock:/var/run/docker.sock:ro
    - ./resources/traefik_custom.yml:/etc/traefik/traefik.yml:ro
    - acme:/acme 
```  
- TODO: document changing FQDN and labels  