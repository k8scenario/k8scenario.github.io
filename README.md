# k8scenario.github.io

![](images/kube-fixit.PNG)

k8scenario/k8scenario is an open source tool for running Kubernetes Troubleshooting Workshops.

A Private Repository of scripts and tools for running Kubernetes troubleshooting sessions

## TODO:
- Create scenario YAML manifests (and optionally bash scripts) to
  - Check cluster functionality - OK with regards scenario
  - Install scenario
  - Check cluster functionality - broken
  - Uninstall scenario
  - Check cluster functionality - OK with regards scenario

- Create a Go binary with encrypted embedded scripts which
  - Installs scenario by name
    scenario -i <scenario>
  - Would be nice that this is a reuasable Open Source tool which we run with an compiled in encryption key
  - Regression tests 1 or more scenarii
    scenario -rt <group>


# Instructions for using k8scenario tool

# Instructions for creating scenarii for the k8scenario tool

