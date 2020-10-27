# OpenShift Demos Ansible Roles and Playbooks
[![Build Status](https://travis-ci.org/siamaksade/openshift-demos-ansible.svg?branch=master)](https://travis-ci.org/siamaksade/openshift-demos-ansible)

### Deploy CoolStore Microservices with CI/CD on OCP 4.x 
In order to deploy the complete demo infrastructure for demonstrating Microservices, CI/CD, 
agile integrations and more, order an OCP 4.5 demo environment "OCP Cluster for ER-Demo (self-install )" via RHPDS and then use the following script to provision the demo
on the OpenShift environment:

#### Prerequisites

The following imagestreams should be installed on OpenShift:

  ```
  oc login -u <cluster_admin_username>:<password> <ocp_url>
  oc import-image postgresql:9.5 --from=registry.redhat.io/rhscl/postgresql-95-rhel7:latest -n openshift
  oc import-image mongodb:3.2 --from=registry.redhat.io/rhscl/mongodb-32-rhel7 -n openshift
  oc import-image nodejs:4 --from=image-registry.openshift-image-registry.svc:5000/openshift/nodejs@sha256:94f29eed99e9053b916e50df94db3d1fa875f5307fa6bc19d5d516eb5e468d6f -n openshift
  ```


### Run Playbooks on OpenShift (with cluster admin)

The [provided templates](helpers/coolstore-ansible-installer.yaml) creates an OpenShift Job to run 
the Ansible playbooks. Check out the template for the complete list of parameters available.

  ```
  $ oc login -u system:admin
  $ oc new-project demo-installer
  $ oc adm policy add-cluster-role-to-user cluster-admin system:serviceaccount:demo-installer:default
  
  $ oc new-app -f https://raw.githubusercontent.com/radarlui/openshift-demos-ansible/master/helpers/coolstore-ansible-installer.yaml \
    --param=DEMO_NAME=msa-cicd-eap-min \
    --param=COOLSTORE_GITHUB_ACCOUNT=radarlui \
    --param=COOLSTORE_GITHUB_REF=ocp-4.x \
    --param=ANSIBLE_PLAYBOOKS_VERSION=ocp-3.11 \
    --param=PROJECT_ADMIN=opentlc-mgr \
    --param=DEPLOY_GUIDES=false

  $ oc logs -f jobs/coolstore-ansible-installer -c coolstore-ansible-installer-job
  ```



### Variables

You can modify the playbooks behavior by specifying extra variables

```
$ ansible-playbook playbooks/coolstore/msa-min.yml -e "github_ref=ocp-3.11 ephemeral=true project_suffix=demo1"
```

| Variable             | Default   | Description                                                                                                            |
|----------------------|-----------|------------------------------------------------------------------------------------------------------------------------|
| `openshift_master`   | *none*    | OpenShift master url. Not required if playbooks run on a host that is already authenticated to OpenShift               |
| `oc_token`           | *none*    | Authentication token for OpenShift. Not required if playbooks run on a host that is already authenticated to OpenShift |
| `oc_kube_config`     | *none*    | Path to .kube config if not using the default                                                                          |
| `project_suffix`     | demo      | A suffix to be added to all project names e.g. cicd-demo                                                               |
| `ephemeral`          | false     | If set to true, all pods will be deployed without persistent storage                                                   |
| `maven_mirror_url`   | false     | Maven repository for Java S2I builds. If empty, Sonatype Nexus gets deployed and used                                  |
| `github_account`     | master    | GitHub account to deploy from in forked: https://github.com/[github-account]/coolstore-microservice                    |
| `github_ref`         | master    | GitHub branch to deploy from: https://github.com/jbossdemocentral/coolstore-microservice                               |
| `project_admin`      | none      | OpenShift user to be assigned as the project admin. Default is the logged-in user                                      |
| `deploy_guides`      | true      | Deploy demo guides as a pod in the CI/CD project                                                                       |


For a list of all options, check [demo variables](playbooks/coolstore/group_vars/all)

### Demo Guide
http://guides-rlui.b542.starter-us-east-2a.openshiftapps.com
