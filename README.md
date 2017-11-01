Ansible Role: Ampache for Kubernetes
====================================

Ansible role to install Ampache on Kubernetes.

Role Variables
--------------

```yaml
ampache_namespace: "default"
# App name (used as selector)
ampache_app: "ampache"
# Deployment name
ampache_deployment: "ampache-deployment"
# Configmap name
ampache_configmap: "ampache"
# Service name
ampache_service: "ampache"

# Number of replicas
ampache_replicas: 1
ampache_revision_history: 1

# Node selector
ampache_node_selector: {}

ampache_resources:
  limits:
    memory: "1Gi"
  requests:
    memory: "256Mi"

ampache_nginx_resources:
  limits:
    memory: "256Mi"
  requests:
    memory: "128Mi"

ampache_quick_cron_period: "0 */6 * * *"
ampache_complete_cron_period: "0 5 */7 * *"


# Music volumes.
ampache_music_volumes:
  data-volume:
    # Indicates and uncomment the volume subPath if wanted
    # subPath:

    # Where to mount the volume
    mountPath:

    # Volume definition
    definition:


# Volumes for music.
ampache_config_volume:
  # Indicates and uncomment the volume subPath if wanted
  # subPath:

  # Volume definition
  definition:


# Setup ingress for ampache
ampache_setup_ingress: false
ampache_ingress:
  name: "ampache-ingress"
  host: "ampache.example.com"
  annotations:
  tls:

# ampache.cfg.php content. It is also possible to configure ampache through the
# webui, and use `kubectl exec` to cat
# `/var/www/ampache/config/ampache.cfg.php` to save it here.
ampache_config:
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
    ampache_music_volumes:
      main-music-volume:
        definition:
          glusterfs:
            endpoints: gluster-example-cluster
            path: some-volume
            readOnly: false
        subPath: "Music"
        mountpath: "/mnt/music"

    ampache_setup_ingress: true
    ampache_ingress:
      name: "ampache-ingress"
      host: "ampache.example.com"
      annotations:
        kubernetes.io/tls-acme: "true"
      tls:
        - secretName: "ampache-ingress-tls"
          hosts:
            - "ampache.example.com"
  roles:
    - role: ampache
```

Use `run_once` to run the role on only one available master in the cluster.  Do
not forget to fill the `ampache_config` variable with your `ampache.cfg.php`
content to save your configuration if the pod is destroyed.

License
-------

Tool under the BSD license. Do not hesitate to report bugs, ask me some
questions or do some pull request if you want to!
