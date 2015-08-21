This document is a work in progress! It should get filled in fairly fast though.

## Platform deployment problems
## Platform operational problems
## End-user problems
### Error dialing loggregator

**Problem**

User can't tail logs of an application using `cf logs app`, gets the following error:

    Error dialing loggregator server: Get https://loggregator.X.X.X.X.xip.io:443/recent?app=APPID: x509: certificate is valid for , not loggregator.X.X.X.X.xip.io.

**Resolution**

The api should be targeted with the `--skip-ssl-validation` flag, for example `cf api api.X.X.X.X.xip.io --skip-ssl-validation`.

**Explanation**

The root cause of this problem is an invalid or self-signed certificate for the domain the environment uses. This is common for testing instances using the *xip.io* domain.
