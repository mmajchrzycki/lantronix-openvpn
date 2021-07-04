


## Generate Public Key Infrastructure with EasyRSA:
Help: https://community.openvpn.net/openvpn/wiki/EasyRSA3-OpenVPN-Howto

1. Get the `easy-rsa` tool

    ```bash
    cd
    git clone https://github.com/OpenVPN/easy-rsa.git
    cd easy-rsa/easyrsa3/
    cp vars.example vars
    ```

1. Edit `vars` file, especially set

    `EASYRSA_DN "org"`

    And assign values to:
    * `EASYRSA_REQ_COUNTRY`
    * `EASYRSA_REQ_PROVINCE`
    * `EASYRSA_REQ_CITY`
    * `EASYRSA_REQ_ORG`
    * `EASYRSA_REQ_EMAIL`

    ---
    **NOTE**

    DON'T LEAVE ANY OF THE FIELDS EMPTY!!!

    ---

1. Init PKI

    ```bash
    ./easyrsa init-pki
    ```

1. Generate Diffie-Hellman parameters

    ```bash
    ./easyrsa gen-dh
    ```

1. Create CA (Certificate Authority)

    ```bash
    ./easyrsa build-ca
    ```
    You have to set some password. That password will be required on the next steps.

1. Build certificate and key for server

    ```bash
    ./easyrsa build-server-full <SERVER_NAME> nopass
    ```

1. Build certificate for client

    ```bash
    ./easyrsa build-client-full <CLIENT_NAME> nopass
    ```

1. After all, you should get a files structure like

    ![PKI files structure](./img/files.png)


## Setup OpenVPN server

1. Login to router and go to Services -> VPN -> OpenVPN

    ![Services -> VPN](./img/router-server-1.png)

    ![OpenVPN menu](./img/router-server-2.png)

1. Create a new `OpenVPN` instance, choose a name and select `Server configuration for a routed multi-client VPN`

    ![OpenVPN new instance](./img/router-server-3.png)

1. After creating, switch to advanced configuration

    ![Switch to advanced configuration](./img/router-server-4.png)

1. On the `Networking` tab add

    * `dev` and set to `tun0`

    ![Switch to advanced configuration](./img/router-server-5.png)

1. On the `Cryptography`

    * select `tls_server`
    * add `ca` and upload proper file
    * add `dh` and upload proper file
    * add `cert` and upload proper file
    * add `key` and upload proper file
    (see `PKI files structure` above)

    ![Cryptography configuration](./img/router-server-6.png)

1. Go back to `Overview` and enable the server

    ![Back to overview](./img/router-server-7.png)

    Enable server (1), `Save & Apply` (2) and `Start` the server (3).

    ![Enabling OpenVPN server](./img/router-server-8.png)

1. If everything went fine, you should get the server `Started` and `Uptime` timer should count the time

    ![OpenVPN server running](./img/router-server-9.png)

1. Open `UDP` port `1194` on the firewall

    Go to `Firewall`

    ![Go to firewall](./img/router-server-10.png)

    Go to `Traffic Rules`

    ![Go to Traffic rules](./img/router-server-11.png)

    In the `Open ports on router` section, enter port number `1194`, protocol `UDP` and choose a name (or left default) and click `Add`:

    ![Open port](./img/router-server-12.png)

    Then select `Save & Apply` on the bottom.

## Setup OpenVPN client

1. Login to router and go to Services -> VPN -> OpenVPN

    ![Services -> VPN](./img/router-server-1.png)

    ![OpenVPN menu](./img/router-server-2.png)

1. Create a new `OpenVPN` instance, choose a name and select `Client configuration for a routed multi-client VPN` and click `Add`

    ![OpenVPN new instance](./img/router-client-1.png)

1. After creating, switch to advanced configuration:

    ![OpenVPN new instance](./img/router-client-2.png)

1. On the `Networking` tab add

    * `dev` and set to `tun0`

    ![Switch to advanced configuration](./img/router-client-3.png)

1. On the `VPN` tab

    * enable `client`
    * enable `pull` (will be visible after enabling `client`)
    * add `remote` and enter `VPN Server` IP address and port `1194`

    ![Switch to advanced configuration](./img/router-client-4.png)

1. On the `Cryptography` tab

    * add `ca` and upload proper file
    * add `dh` and upload proper file
    * add `cert` and upload proper file
    * add `key` and upload proper file
    (see `PKI files structure` above)

    ![Switch to advanced configuration](./img/router-client-5.png)

1. Click `Save` and go back to `Overview`, then `Enable` and `Start` service.

    ![Switch to advanced configuration](./img/router-client-6.png)

## Troubleshooting

If something is not working (whether on `server` or `client`) you can add to VPN instance `verb` paramter with value `11`. After stopping and starting server in `Status -> System Log` you shoulkd get more detailed inofrmation about the problem with OpenVPN instance.