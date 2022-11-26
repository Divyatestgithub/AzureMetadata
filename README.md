# AzureMetadata

# Retrieving instance metadata

Instance metadata is available for running VMs created/managed using Azure Resource Manager. Access all data categories for an instance use the following URI
```
$ curl -s -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2021-02-01"

```

The default output for all instance metadata is of json format(content type Application/JSON).If you want to have it in the text format use the below URI

```

curl -H Metadata:true http://169.254.169.254/metadata/instance/?api-version=2021-02-01\&format=text

```


# Versioning

Instance metadata service is versioned. Versions are mandatory and the current  version is 2021-02-01.


# Security

Instance metadata endpoint is accessible only from within the running instance on a non-routable 169.254.169.254 IP address. We also require requests to contain Metadata:true header being sent to ensure that the actual request was directly intended and not a part of unintentional redirection.

# Error

If there is a data element not found or malformed requests Metadata Service returns standard HTTP Error, following are the few examples of return codes

|HTTP Return Code| Reason|
|:---------------:|--------:|
|200|OK|
|400|Bad Request, missing header, pass -H Metadata:true|
|404|Not Found, requested element does’t exist|
|405|Method not supported|
|429|Too many requests, currently we only support maximum of 5 queries per second|

# Examples

# Retrieving the network information

```
curl -H Metadata:true http://169.254.169.254/metadata/instance/network?api-version=2021-02-01

{
  "interface": [
    {
      "ipv4": {
        "ipaddress": [
          {
            "ipaddress": "10.0.0.4",
            "publicip": "<>.<>.<>.<>"
          }
        ],
        "subnet": [
          {
            "address": "10.0.0.0",
            "dnsservers": [
              {
                "ipaddress": "10.0.0.2"
              },
              {
                "ipaddress": "10.0.0.3"
              }
            ],
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipaddress": []
      },
      "mac": "000D3A00FA89"
    }
  ]
}

```

# Retrieving public IP address

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipaddress/0/publicip?api-version=2021-02-01&format=text"

```

# Retrieving all instance metadata

```

curl -H Metadata:true http://169.254.169.254/metadata/instance?api-version=2021-02-01



{
"compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipaddress": [
            {
              "ipaddress": "10.0.0.4",
              "publicip": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.0.0",
              "dnsservers": [
                {
                  "ipaddress": "10.0.0.2"
                },
                {
                  "ipaddress": "10.0.0.3"
                }
              ],
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipaddress": []
        },
        "mac": "000D3A00FA89"
      }
      
      ```
    ]
  }
}
