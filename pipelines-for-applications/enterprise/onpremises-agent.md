---
layout: default
title: "On-premises Pipelines agent"
---

## Installing the Pipelines agent

When you use the Enterprise edition of Pipelines with SAML or LDAP enabled, you will have to use a distelli.yml to authenticate the Pipelines agent when installing the agent.

A distelli.yml file looks like this:

~~~
DistelliAccessToken: '12345678901234567890123456'
DistelliSecretKey: '1234567890123456789012345678901234567'
~~~

<h3>Create distelli.yml</h3>

On the server that you wish to install the Pipelines agent, create a distelli.yml file. The values that are placed in the file must come from the Pipelines account you wish to connect the server to. You can retrieve the values by:

<ol>
  <li>Ensure you are actively in the account (team) you wish to connect the server/agent to.</li>
  <li>Ensure you are on the <b>Pipelines for Applications</b> web UI.</li>
  <li>Click the <b>gear</b> at the top right.</li>
  <li>Click <b>Agent</b> on the left menu.</li>
  <li>Click <b>Show</b> to see the Secret Key.</li>
</ol>

Here you can see the DistelliAccessToken (Access Token) and the DistelliSecretKey (Secret Key) to include in the distelli.yml.

Put those values in the distelli.yml and save the file. Ensure you have a space after the colon and the values are wrapped in single quotes like the above example.

<h3>Get the Agent</h3>

If you are unsure where to get the agent, follow these steps. This must be done on your Enterprise on-premise install of Pipelines.

<ol>
  <li>Ensure you are on the <b>Pipelines for Applications</b> web UI.</li>
  <li>Click <b>Servers</b> from the top.</li>
  <li>Click <b>Add Server</b> button on the top right.</li>
  <li>Click <b>Add Existing Server</b>.</li>
</ol>

Here you can see the command to download the agent. Choose the appropriate command for your system.

<h3>Install the Agent</h3>

After you have downloaded the agent to the server, you will want to install it with the following command:

~~~
sudo /usr/local/bin/distelli agent install -conf /path/to/file/distelli.yml
~~~

The -conf option instructs the agent to look in the /path/to/file/distelli.yml file for the credentials the authenticate the agent.

## Retrieve agent endpoints

The Pipelines agent can be unzipped with the <a href="http://www.7-zip.org/" target="_blank">unzip</a> utility.

<h3>Find Pipelines Agent Endpoints</h3>

Only in an on premisess install will the Pipelines agent have embedded endpoints. To retrieve them:

<ol>
  <li>Copy the Pipelines agent to a temporary directory.</li>
  <li>Unzip the agent.</li>
  <li>Parse (view) the contents of <code>defaults.json</code></li>
</ol>

The below example uses <a href="https://stedolan.github.io/jq/download/" target="_blank">jq</a> to parse the json for viewing.

~~~
$ mkdir distelli_tmp
$ cd distelli_tmp
$ cp /usr/local/bin/distelli .
$ unzip distelli
$ jq --slurp . defaults.json
[
  {
    "Endpoints": {
      "app_launcher": "https://agent-distelli.example.com",
      "app_management": "https://app-distelli.example.com",
      "login": "https://login-distelli.example.com",
      "agent_service": "dumps://ags-distelli.example.com:443",
      "web UI": "https://www-distelli.example.com",
    }
  }
]
~~~

## Add a custom CA to the agent

The Pipelines agent can use SSL to securely communicate with an on-premises Puppet Pipelines installation. The agent is pre-configured with trusted Certificate Authorities (CA).

You can add a custom CA to an on-premises Pipelines agent. To do so, you will need to know the [existing endpoints](./onpremises-agent.html#retrieve-agent-endpoints) the agent is configured with. You will also need the custom CA's public CA certificate.

<h3>Add a custom CA to the Pipelines agent</h3>

To add a custom CA to the Pipelines agent you use the <code>distelli embed</code> command. First you must build a <code>defaults.yml</code> file. The format of the file is:

~~~
Endpoints:
    app_launcher: "https://agent-distelli.example.com"
    app_management: "https://app-distelli.example.com"
    login: "https://login-distelli.example.com"
    agent_service: "dumps://ags-distelli.example.com:443"
    web UI: "https://www-distelli.example.com"
CACertificates:
    - |
      -----BEGIN CERTIFICATE-----
      MITCHjCCAaSgAwIBAgIRYFlJ4CYuu1X5CneKcflK2GwwCgYIKoZIzj0EAwMwUDEkMCIGA1UECxMb
      R2xvYmFsU2lnbiBFQ0MgUm9vdCBDQSAtIFI1MRMwEQYDVQQKEwpHbG9iYWxTaWduMRMwEQYDVQQD
      EwpHbG9iYWxTaWduMB4XDTEyMTExMzAwMDAwMFoXDTM4MDExOTAzMTQwN1owUDEkMCIGA1UECxMb
      R2xvYmFsU2lnbiBFQ0MgUm9vdAlexIsCool1MRMwEQYDVQQKEwpHbG9iYWxTaWduMRMwEQYDVQQD
      EwpHbG9iYWxTaWduMHYwEAYHKoZIzj0CAQYFK4EEACIDYgAER0UOlvt9Xb/pOdEh+J8LttV7HpI6
      SFkc8GIxLcB6KP4ap1yztsyX50XUWPrRd21DosCHZTQKH3rd6zwzocWdTaRvQZU4f8kehOvRnkmS
      h5SHDDqFSmafnVmTTZdhB0ZKo0IwQDAOBgNVHQ8BAf8EBAMCAQYwDwYDVR0TAQH/BAUwAwEB/zAd
      BgNVHQ4EFgQUPeYpSJvqB8ohREom3m7e0oPQn1kwCgYIKoZIzj0EAwMDaAAwZQIxAOVpEslu28Yx
      uglB4Zf4+/2a4n0Sye18ZNPLBSWLVtmg515dTguDnFt2KaAJJiFqYgIwcdK1j1zqO+F4CYWodZI7
      yFz9SO8NdCKoCOJuxUnOxwy8p2Fp8fc74SrL+SvzZPa3
      -----END CERTIFICATE-----
~~~


>**Note:** This CA certificate will replace ALL existing certificates in the Pipelines agent.

If you need more than the one certificate, you can add multiples. Realize that the Pipelines agent will also need to talk to the large object store, whether this is S3 or Artifactory, and will need the certificate for that service also. This will also be true for any other services, for example; software repositories, notification services, webhooks, etc... 

Any and all required CA Certificates will need to be included in `defaults.yml`.

The following exemplifies the `defaults.yml` with multiple CA certificates.

~~~
Endpoints:
    app_launcher: "https://agent-distelli.example.com"
    app_management: "https://app-distelli.example.com"
    login: "https://login-distelli.example.com"
    agent_service: "dumps://ags-distelli.example.com:443"
    web UI: "https://www-distelli.example.com"
CACertificates:
    - |
      -----BEGIN CERTIFICATE-----
      MITCHjCCAaSgAwIBAgIRYFlJ4CYuu1X5CneKcflK2GwwCgYIKoZIzj0EAwMwUDEkMCIGA1UECxMb
      R2xvYmFsU2lnbiBFQ0MgUm9vdCBDQSAtIFI1MRMwEQYDVQQKEwpHbG9iYWxTaWduMRMwEQYDVQQD
      EwpHbG9iYWxTaWduMB4XDTEyMTExMzAwMDAwMFoXDTM4MDExOTAzMTQwN1owUDEkMCIGA1UECxMb
      R2xvYmFsU2lnbiBFQ0MgUm9vdAlexIsCool1MRMwEQYDVQQKEwpHbG9iYWxTaWduMRMwEQYDVQQD
      EwpHbG9iYWxTaWduMHYwEAYHKoZIzj0CAQYFK4EEACIDYgAER0UOlvt9Xb/pOdEh+J8LttV7HpI6
      SFkc8GIxLcB6KP4ap1yztsyX50XUWPrRd21DosCHZTQKH3rd6zwzocWdTaRvQZU4f8kehOvRnkmS
      h5SHDDqFSmafnVmTTZdhB0ZKo0IwQDAOBgNVHQ8BAf8EBAMCAQYwDwYDVR0TAQH/BAUwAwEB/zAd
      BgNVHQ4EFgQUPeYpSJvqB8ohREom3m7e0oPQn1kwCgYIKoZIzj0EAwMDaAAwZQIxAOVpEslu28Yx
      uglB4Zf4+/2a4n0Sye18ZNPLBSWLVtmg515dTguDnFt2KaAJJiFqYgIwcdK1j1zqO+F4CYWodZI7
      yFz9SO8NdCKoCOJuxUnOxwy8p2Fp8fc74SrL+SvzZPa3
      -----END CERTIFICATE-----
    - |
      -----BEGIN CERTIFICATE-----
      MIICPDCCAaUCEDyRMcsf9tAbDpq40ES/Er4wDQYJKoZIhvcNAQEFBQAwXzELMAkG
      A1UEBhMCVVMxFzAVBgNVBAoTDlZlcmlTaWduLCBJbmMuMTcwNQYDVQQLEy5DbGFz
      cyAzIFB1YmxpYyBQcmltYXJ5IENlcnRpZmljYXRpb24gQXV0aG9yaXR5MB4XDTk2
      MDEyOTAwMDAwMFoXDTI4MDgwMjIzNTk1OVowXzELMAkGA1UEBhMCVVMxFzAVBgNV
      BAoTDlZlcmlTaWduLCBJbmMuMTcwNQYDVQQLEy5DbGFzcyAzIFB1YmxpYyBQcmlt
      YXJ5IENlcnRpZmljYXRpb24gQXV0aG9yaXR5MIGfMA0GCSqGSIb3DQEBAQUAA4GN
      ADCBiQKBgQDJXFme8huKARS0EN8EQNvjV69qRUCPhAwL0TPZ2RHP7gJYHyX3KqhE
      BarsAx94f56TuZoAqiN91qyFomNFx3InzPRMxnVx0jnvT0Lwdd8KkMaOIG+YD/is
      I19wKTakyYbnsZogy1Olhec9vn2a/iRFM9x2Fe0PonFkTGUugWhFpwIDAQABMA0G
      CSqGSIb3DQEBBQUAA4GBABByUqkFFBkyCEHwxWsKzH4PIRnN5GfcX6kb5sroc50i
      2JhucwNhkcV8sEVAbkSdjbCxlnRhLQ2pRdKkkirWmnWXbj9T/UWZYB2oK0z5XqcJ
      2HUw19JlYD1n1khVdWk/kfVIC0dpImmClr7JyDiGSnoscxlIaU5rfGW/D/xwzoiQ
      -----END CERTIFICATE-----
~~~

When you are ready to embed this information in the Pipelines agent, issue the following command:

~~~
distelli embed-config -distelli /path/to/distelli/distelli -conf defaults.yml -out custom-distelli
~~~

Usage: `distelli embed-config -out <fname> -conf <filename> -distelli <distelli.exe>`

* `-out <fname>` - Location to put the new distelli binary.

* `-conf <filename>` - Location of new default distelli yml configuration.

* `-distelli </path/to/distelli/distelli.exe>` - Location of input distelli executable. Defaults to the current distelli executable running this command.

## Troubleshooting the agent

When installing the Pipelines agent in an Enterprise on-premise there are special considerations.

If using single sign-on (SSO) with either SAML or LDAP, then users must authenticate the agent with a distelli.yml. Instructions for doing so are [here](./onpremises-agent.html#installing-the-pipelines-agent).

More importantly this document discusses the endpoints that the agent uses.

<h2>Agent Endpoints</h2>

Agent endpoints are the Pipelines endpoints that your on-premise agents communicate with. These are local to your network and are typically specified when doing the initial install of Pipelines. These can be a DNS or an IP address. These also include TCP port specifications.

Agent endpoints are internally configured in the agent you download from your Enterprise on-premise and typically there is nothing to specify. You simply install the agent and it knows exactly where to communicate. You can see what these endpoints are that are embedded in your on-premise agent, instructions are [here](./onpremises-agent.html#retrieve-agent-endpoints).

Where trouble comes in is if a user grabs the wrong agent. It is not uncommon for users to unknowingly download the Pipelines agent from the Pipelines SaaS at https://pipelines.puppet.com/download/client. This may create cached endpoints and cause conflict when trying to install the correct agent.


<h2>Inspect Cached Agent Endpoints</h2>

The cached endpoints are in an sqlite3 database. To inspect them, download an sqlite3 client. Clients can be found <a href="https://sqlite.org/download.html">here</a>.

The following sqlite databases may exist. These differ for the Pipelines agent and Pipelines CLI.

<table>
  <tr><th>Type</th><th>OS</th><th>DB File</th></tr>
  <tr><td>Agent</td><td>Linux</td><td>/distelli/.dtk/app_state.sqlite</td></tr>
  <tr><td>CLI</td><td>Linux</td><td>~/.dtk/cli_state.sqlite</td></tr>
  <tr><td>Agent</td><td>Windows</td><td>c:\distelli\.dtk\app_state.sqlite</td></tr>
  <tr><td>CLI</td><td>Windows</td><td>c:\Users\USER\.dtk\cli_state.sqlite</td></tr>
</table>

The value of USER will be any Windows user that has run the "distelli login" command. This could be more than one.

This is also true on linux that there could be more than one cli_state.sqlite db if multiple users ran "distelli login".

Of note, the Pipelines CLI is required for builds to be successful.

To use sqlite3 to inspect endpoints in any of the above listed files, do the following:

~~~
sqlite3(.exe)
.open DIR/.dtk/DB_FILE.sqlite
select * from endpoints;
~~~

<h2>Scrub Pipelines Agent</h2>

Due to the cached endpoints, it may be difficult to get new credentials to work and is best to scrub the Pipelines agent from the server.

To uninstall the Pipelines agent you can run the command:

~~~
sudo distelli agent uninstall
~~~

If the agent never authenticated to Pipelines or can't reach Pipelines, this may fail.

To truly clean the agent, even after an uninstall, you should remove the following directories. The directories below are based on a default installation. Realize you can always search the disk for the directory <code>.dtk</code>, and remove any instances of that.

<h4>Linux</h4>

<ul>
  <li>/distelli</li>
  <li>/home/distelli</li>
  <li>/home/*/.dtk</li>
  <li>/usr/local/bin/distelli</li>
</ul>

<h4>Windows</h4>

<ul>
  <li>c:/distelli</li>
  <li>c:/users/*/.dtk</li>
  <li>c:/Program Files/Distelli</li>
</ul>


<h2>Specifying Different Endpoints</h2>

In an Enterprise on-premise install of Pipelines, there may be a need to have some agents communicate through different load-balancers than the default load-balancer endpoints.

This can be accomplished by specifying endpoints in the distelli.yml.

~~~
DistelliAccessToken: '0123456789ABCDEFGHIJKLMNOP'
DistelliSecretKey: '0123456789abcdefghijklmnopqr'
Endpoints:
    agent_service: "dumps://ags-proxy1-east.example.com/"
    app_launcher: "https://agent-proxy1-east.example.com/"
    app_management: "https://app-proxy1-east.example.com/"
    login: "https://login-proxy1-east.example.com/"
    web UI: "https://web UI-proxy1-east.example.com/"  
~~~

