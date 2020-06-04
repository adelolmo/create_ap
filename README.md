## Features
* Create an AP (Access Point) at any channel.
* Choose one of the following encryptions: WPA, WPA2, WPA/WPA2, Open (no encryption).
* Hide your SSID.
* Disable communication between clients (client isolation).
* IEEE 802.11n & 802.11ac support
* Internet sharing methods: NATed or Bridged or None (no Internet sharing).
* Choose the AP Gateway IP (only for 'NATed' and 'None' Internet sharing methods).
* You can create an AP with the same interface you are getting your Internet connection.
* You can pass your SSID and password through pipe or through arguments (see examples).
* Support for IEEE 802.11r configuration (Fast BSS Transition).

## Dependencies
### General
* bash (to run this script)
* util-linux (for getopt)
* procps or procps-ng
* hostapd
* iproute2
* iw
* iwconfig (you only need this if 'iw' can not recognize your adapter)
* haveged (optional)

### For 'NATed' or 'None' Internet sharing method
* dnsmasq
* iptables


## Installation

    git clone https://github.com/adelolmo/create_ap
    cd create_ap

You can build the package for a specific architecture. Possible values are `armhf`, `arm64`, `amd64` and `i386`. 

    dpkg-buildpackage -a armhf -us -uc -b
    dpkg -i createap_0.4.6~ado3_armhf.deb

## Systemd service
### Start service immediately:
    systemctl start createap

### Start on boot:
    systemctl enable createap


## Examples
### No passphrase (open network):
    createap wlan0 eth0 MyAccessPoint

### WPA + WPA2 passphrase:
    createap wlan0 eth0 MyAccessPoint MyPassPhrase

### AP without Internet sharing:
    createap -n wlan0 MyAccessPoint MyPassPhrase

### Bridged Internet sharing:
    createap -m bridge wlan0 eth0 MyAccessPoint MyPassPhrase

### Bridged Internet sharing (pre-configured bridge interface):
    createap -m bridge wlan0 br0 MyAccessPoint MyPassPhrase

### Internet sharing from the same WiFi interface:
    createap wlan0 wlan0 MyAccessPoint MyPassPhrase

### Choose a different WiFi adapter driver
    createap --driver rtl871xdrv wlan0 eth0 MyAccessPoint MyPassPhrase

### No passphrase (open network) using pipe:
    echo -e "MyAccessPoint" | createap wlan0 eth0

### WPA + WPA2 passphrase using pipe:
    echo -e "MyAccessPoint\nMyPassPhrase" | createap wlan0 eth0

### Enable IEEE 802.11n
    createap --ieee80211n --ht_capab '[HT40+]' wlan0 eth0 MyAccessPoint MyPassPhrase

### Client Isolation:
    createap --isolate-clients wlan0 eth0 MyAccessPoint MyPassPhrase

### Enable IEEE 802.11r
Note that `WIFI_FT_KEY` and `MOBILITY_DOMAIN` values **have to match** the values from the other Access Points in the network.

You most probably need to use a `bridge` connection so that the client's IP doesn't change.

Enable the feature via cli:

    createap -m bridge --ieee80211r --mobility_domain 2e32 --wifi_ft_key 638b46bfff0eefbe0c460edaa403440d wlan0 eth0 MyAccessPoint MyPassPhrase

Enable the feature via the configuration file `/etc/createap.conf`.

| Name | Type | Default | Description |
|:---|:---|:---|:---|
| IEEE80211R | 0-1 | 0 | *(required)* Disable/Enable IEEE 802.11r. Default is 0.|
| WIFI_FT_KEY | 256-bit hex string | | *(required)* Key used during the Initial Mobility Domain Association.|
| NAS_IDENTIFIER | 1 to 48 octets string | | *(optional)* NAS-Identifier string for RADIUS messages. When used, this should be unique to the NAS within the scope of the RADIUS server.|
| MOBILITY_DOMAIN | 2-octet hex string | | *(required)* Mobility Domain identifier is used to indicate a group of APs (within an ESS, i.e., sharing the same SSID) between which a client can use Fast BSS Transition.|

## License
FreeBSD
