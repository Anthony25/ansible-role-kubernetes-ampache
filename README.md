Ansible Role: Ampache for Kubernetes
====================================

Ansible role to install Ampache on Kubernetes.

Role Variables
--------------

```yaml
kubernetes_ampache_namespace: "default"
# App name (used as selector)
kubernetes_ampache_app: "ampache"
# Deployment name
kubernetes_ampache_deployment: "ampache-deployment"
# Configmap name
kubernetes_ampache_configmap: "ampache"
# Service name
kubernetes_ampache_service: "ampache"

# Number of replicas
kubernetes_ampache_replicas: 1
kubernetes_ampache_revision_history: 1

# Node selector
kubernetes_ampache_node_selector: {}

# Add custom labels in the deployment metadata section
kubernetes_ampache_deployment_labels: {}
# Add custom annotations in the deployment metadata section
kubernetes_ampache_deployment_annotations: {}

kubernetes_ampache_resources:
  limits:
    memory: "1Gi"
  requests:
    memory: "256Mi"

kubernetes_ampache_nginx_resources:
  limits:
    memory: "256Mi"
  requests:
    memory: "128Mi"

# Quick cron scans for new albums and gathers arts
kubernetes_ampache_quick_cron_period: "0 */6 * * *"

# Complete cron scans for new albums, cleans removed ones, gathers arts and
# verify the catalog
kubernetes_ampache_complete_cron_period: "0 5 */7 * *"


# Music volumes.
kubernetes_ampache_music_volumes:
  data-volume:
    # Indicates and uncomment the volume subPath if wanted
    # subPath:

    # Where to mount the volume
    mountPath:

    # Volume definition
    definition:


# Volumes for music.
kubernetes_ampache_config_volume:
  # Indicates and uncomment the volume subPath if wanted
  # subPath:

  # Volume definition
  definition:


# Setup ingress for ampache
kubernetes_ampache_setup_ingress: false
kubernetes_ampache_ingress:
  name: "ampache-ingress"
  host: "ampache.example.com"
  annotations:
  tls:

# ampache.cfg.php content. It is also possible to configure ampache through the
# webui, and use `kubectl exec` to cat
# `/var/www/ampache/config/ampache.cfg.php` to save it here.
kubernetes_ampache_config:
```

Dependencies
------------

Kubectl needs to be installed on the host targeted by the role.


Example Playbook
----------------

```yaml

- hosts: kube-master
  run_once: true
  vars:
    kubernetes_ampache_music_volumes:
      main-music-volume:
        definition:
          glusterfs:
            endpoints: gluster-example-cluster
            path: some-volume
            readOnly: false
        subPath: "Music"
        mountpath: "/mnt/music"

    kubernetes_ampache_setup_ingress: true
    kubernetes_ampache_ingress:
      name: "ampache-ingress"
      host: "ampache.example.com"
      annotations:
        kubernetes.io/tls-acme: "true"
      tls:
        - secretName: "ampache-ingress-tls"
          hosts:
            - "ampache.example.com"
  roles:
    - role: Anthony25.kubernetes-ampache
```

Use `run_once` to run the role on only one available master in the cluster.  Do
not forget to fill the `kubernetes_ampache_config` variable with your
`ampache.cfg.php` content to save your configuration if the pod is destroyed.

License
-------

Tool under the BSD license. Do not hesitate to report bugs, ask me some
questions or do some pull request if you want to!
