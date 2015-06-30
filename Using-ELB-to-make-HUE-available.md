1. To add a HUE service to the Cloudera cluster, note on which machines it was placed.

9. Create a new ELB instance in Amazon:
    * Should be forwarding port 443 HTTPS to port 8888 HTTPS .
    * Should be in the same subnet as the NAT instance. (One is sufficient.)
    * Fill in the private key, certificate, and the chain.
    * Create a new security group allowing access to all on port 443.
    * A simple TCP health check will do.
9. Modify the security group of Cloudera to allow access from the ELB security group to _all_ ports. (This is suggested by Amazon support; using only one port doesn't work).

9. Modify HUE instance settings on the Cloudera cluster:
    * Enable HTTPS.
    * Put the private key and certificate on the HUE server in a place reachable by HUE and chown hue:hue the files.
    * Fill in the required paths in HUE configuration.

9. Add a CNAME domain to route53 for the created ELB.


