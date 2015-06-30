## Download the latest version of bosh

On the jump box, upload the latest stemcell available from (http://bosh_artifacts.cfapps.io/file_collections?type=stemcells). For instance: 

```
bosh upload stemcell https://s3.amazonaws.com/bosh-jenkins-artifacts/bosh-stemcell/aws/bosh-stemcell-2778-aws-xen-ubuntu-trusty-go_agent.tgz
```

Update `/home/ubuntu/workspace/deployments/cf-boshworkspace/deployments/cf-aws-vpc.yml` to match that version:

```
stemcells:
  - name: bosh-aws-xen-ubuntu-trusty-go_agent
    version: 2778
```

Download the stemcell again for use by microbosh:

```
cd /home/ubuntu/workspace/deployments/cf-boshworkspace/.stemcells
wget https://s3.amazonaws.com/bosh-jenkins-artifacts/bosh-stemcell/aws/bosh-stemcell-2778-aws-xen-ubuntu-trusty-go_agent.tgz
```

Deploy that stemcell to the microbosh:
```
cd /home/ubuntu/workspace/deployments/cf-boshworkspace/microbosh
bosh micro deploy ../.stemcells/bosh-stemcell-2778-aws-xen-ubuntu-trusty-go_agent.tgz --update
```

##Update the Cloud Foundry release
```
cd /home/ubuntu/workspace/cloudfoundry/cf-release/
./update
git fetch
git checkout <VERSION>
```
Until our patched version of <I>dea_next</I> is merged in, you'll need to do the following:
```
cd /home/ubuntu/workspace/cloudfoundry/dea_next/
git checkout remotes/origin/extra-ports-no-tests
git pull cf master
cd /home/ubuntu/workspace/cloudfoundry/cf-release/src/
mv dea_next dea_next.bak
cp -r /home/ubuntu/workspace/cloudfoundry/dea_next/ .
```

Make sure it merges cleanly.

Read the release notes, generally linked to from https://github.com/cloudfoundry-community/cf-docs-contrib/wiki and available with the version number https://github.com/cloudfoundry-community/cf-docs-contrib/wiki/v194. Watch for anything that looks like it will require changes to the bosh manifest for deployment.

### Deploy the update

```
cd /home/ubuntu/workspace/deployments/cf-boshworkspace
bosh create release --force
bosh upload release
bosh prepare deployment
bosh deploy
```
