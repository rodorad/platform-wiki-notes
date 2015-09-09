This document is a work in progress! It should get filled in fairly fast though.

## Platform deployment problems
## Platform operational problems
### Error listing app instance numbers
**Problem**

When listing apps with `cf a`, app instance numbers show up as `?/1`, for example:

    user-management           started           ?/1         512M     1G     user-management.example.com
    cdh-broker                started           ?/1         128M     1G     cdh-broker.example.com
    hdfs-broker               started           ?/1         1G       1G     hdfs-broker.example.com
    ipython-broker            started           ?/1         256M     1G     ipython-broker.example.com

**Resolution**

The resolution can be found [here](https://docs.cloudfoundry.org/running/troubleshooting.html), under the `Recovering from HM9000 Failure` section. You can additionaly stop all hm9000 processes beforehand and start them in the following order: `etcd1 -> hm1 -> etcd2 -> hm2`.

### Error parsing JSON
**Problem**

Can't list services in `cf service-access` when using the cf cli client:

    Error parsing JSON: invalid syntax

**Resolution**

This is a bug in cf-cli 6.12.3. Downgrade do 6.12. Users of trustedanalytics/cloudfoundry-mkappstack need to set cfbinver in appstack.mk to 6.12

## End-user problems
### Error dialing loggregator

**Problem**

User can't tail logs of an application using `cf logs app`, gets the following error:

    Error dialing loggregator server: Get https://loggregator.X.X.X.X.xip.io:443/recent?app=APPID: x509: certificate is valid for , not loggregator.X.X.X.X.xip.io.

**Resolution**

The api should be targeted with the `--skip-ssl-validation` flag, for example `cf api api.X.X.X.X.xip.io --skip-ssl-validation`.

**Explanation**

The root cause of this problem is an invalid or self-signed certificate for the domain the environment uses. This is common for testing instances using the *xip.io* domain.
