```terminal:execute
command: kapp delete -a educates
```

```terminal:execute
command: imgpkg push -b {{registry_host}}/educates:21.05.10.1 -f packages/educates/bundle
```

```terminal:execute
command: imgpkg copy -b {{registry_host}}/educates:21.05.10.1 --to-repo {{registry_host}}/educates-copy
```

```terminal:execute
command: imgpkg pull -b {{registry_host}}/educates-copy:21.05.10.1 -o educates-copy
```

```terminal:execute
command: |
  ytt -f educates-copy/config -v ingressDomain={{ingress_domain}} \
    -v imageRegistry.host=$REGISTRY_HOST \
    -v imageRegistry.username=$REGISTRY_USERNAME \
    -v imageRegistry.password=$REGISTRY_PASSWORD | \
    kbld -f educates-copy/kbld-bundle.yml -f educates-copy/.imgpkg/images.yml -f - | \
    kapp deploy -a educates -f - -y
```

```terminal:execute
command: |
  kubectl apply -f https://raw.githubusercontent.com/eduk8s/lab-markdown-sample/master/resources/workshop.yaml
  kubectl apply -f https://raw.githubusercontent.com/eduk8s/lab-markdown-sample/master/resources/training-portal.yaml
```