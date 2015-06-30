# Installation on Cloud Foundry

1. Download your certificate. For Digicet, the command is: 

    ``Single pem file containing all the certs`` 
9. Edit the file by adding the contents of the private key file after the first cert. An example:

    ```
-----BEGIN CERTIFICATE-----
MIIFMjCCBBqgAwIBAgIQB7Jgh0VUcg2M0OatAmDtYzANBgkqhkiG9w0BAQsFADBN
XELsSOf3U535nNiXCQ2vnLUk3un+ngWO98vBcKniQ7WSPDTIqqvFbAbAg5jQipKd
60+kigTkFY9XTvuDrkYiO4tBmS3CC9l1k1rhDNkEtpnsItFUQPk=
-----END CERTIFICATE-----
PRIVATE KEY CONTENTS GO HERE
-----BEGIN CERTIFICATE-----
MIIElDCCA3ygAwIBAgIQAf2j627KdciIQ4tyS8+8kTANBgkqhkiG9w0BAQsFADBh
c+LJMto4JQtV05od8GiG7S5BNO98pVAdvzr508EIDObtHopYJeS4d60tbvVS3bR0
j6tJLp07kzQoH3jOlOrHvdPJbRzeXDLz
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
MIIDrzCCApegAwIBAgIQCDvgVpBCRrGhdWrJWZHHSjANBgkqhkiG9w0BAQUFADBh
YSEY1QSteDwsOoBrp+uvFRTp2InBuThs4pFsiv9kuXclVzDAGySj4dzp30d8tbQk
CAUw7C29C79Fv1C5qfPrmAESrciIxpg0X40KPMbp1ZWVbd4=
-----END CERTIFICATE-----
    ```

9. Go to the jumpbox and edit the manifest, adding the following directive:

    ```
ha_proxy:
    ssl_pem: |
      (contents of the modified certificate file go here, example below)
      -----BEGIN CERTIFICATE-----
      MIIFMjCCBBqgAwIBAgIQB7Jgh0VUcg2M0OatAmDtYzANBgkqhkiG9w0BAQsFADBN
    ```
9. Run a _bosh deploy_ command. 
9. Verify that a correct certificate is presented: 

    ``
    echo | openssl s_client -connect api.your-instance.com:443 2>/dev/null | openssl x509 -noout -text
    ``