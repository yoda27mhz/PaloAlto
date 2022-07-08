# Palo Alto - VPN RESET S2S
#### 2022-06-28 - RAUL PEREZ
## SOURCE
http://api-lab.paloaltonetworks.com/introduction.html
https://github.com/kevinsteves/pan-python

## PURPOSE
Reset VPN ipsec tunnel, of VPN Site2Site (execute commands from remote client without login/password.)

## REQUIREMENTS
* Install WSL
* pip install --user pan-python
* wget https://raw.githubusercontent.com/kevinsteves/pan-python/master/bin/panxapi.py

## HELP

```bash=
yoda27mhz@LAPTOP /home/yoda27mhz $ ./panxapi.py --help
panxapi.py [options] [xpath]
    -d                    delete object at xpath
    -e element            edit XML element at xpath
    -g                    get candidate config at xpath
    -k                    generate API key
    -s                    show active config at xpath
    -S element            set XML element at xpath
    -U cmd                execute dynamic update command
    -C cmd                commit candidate configuration
    --validate            validate candidate configuration
    --force               force commit when conflict
    --partial part        commit specified part
    --sync                synchronous commit
    -A cmd                commit-all (Panorama)
    --ad-hoc query        perform ad hoc request
    --modify              insert known fields in ad hoc query
    -o cmd                execute operational command
    --export category     export files
    --log log-type        retrieve log files
    --report report-type  retrieve reports (dynamic|predefined|custom)
    --name report-name    report name
    --src src             clone source node xpath
                          export source file/path/directory
    --dst dst             move/clone destination node name
                          rename new name
                          export destination file/path/directory
    --move where          move after, before, bottom or top
    --rename              rename object at xpath to dst
    --clone               clone object at xpath, src xpath
    --override element    override template object at xpath
    -M element            multi-config XML element
    --strict yes|no       multi-config strict-transactional
    --vsys vsys           VSYS for dynamic update/partial commit/
                          operational command/report
    -l api_username[:api_password]
    -h hostname
    -P port               URL port number
    --serial number       serial number for Panorama redirection/
                          commit-all/threat-pcap
    --group name          device group for commit-all
    --merge               merge with candidate for commit-all
    --nlogs num           retrieve num logs
    --skip num            skip num logs
    --filter filter       log selection filter
    --interval seconds    log/commit/report job query interval
    --timeout seconds     log/commit/report job query timeout
    --stime time          search time for threat-pcap
    --pcapid id           threat-pcap ID
    -K api_key
    -x                    print XML response to stdout
    -p                    print XML response in Python to stdout
    -j                    print XML response in JSON to stdout
    -r                    print result content when printing response
    --text                print text response to stdout
    -X                    convert text command to XML
    --ls                  print formatted PCAP listing to stdout
    --recursive           recursive export
    -H                    use http URL scheme (default https)
    -G                    use HTTP GET method (default POST)
    -D                    enable debug (multiple up to -DDD)
    -t tag                .panrc tagname
    -T seconds            urlopen() timeout
    --cafile path         file containing CA certificates
    --capath path         directory of hashed certificate files
    --version             display version
    --help                display usage
yoda27mhz@LAPTOP /home/yoda27mhz $
```
## GENERATE API KEY
```bash=
yoda27mhz@LAPTOP /home/yoda27mhz $ ./panxapi.py -h 192.168.1.1 -l yoda27mhz -k
Password:
keygen: success
API key:  "LUFRPT0blablablablablablablablablablablablablablablablablalbalablaZLajU2KzlYVDBIOA=="
```
:warning: For reset the API KEY, you can execute the command.:warning: 


## TESTING API KEY
```bash=
panxapi.py -h 192.168.1.1 -t xapilab -Xxo 'show clock' -K "LUFRPT0blablablablablablablablablablablablablablablablablalbalablaZLajU2KzlYVDBIOA=="
```
## GET THE OPERATIONAL COMMAND
Login to PA firewall, and do:
```bash=
yoda27mhz@FW-PA-01(active)> debug cli on
yoda27mhz@FW-PA-01(active)> test vpn ike-sa gateway vpn_TEST
(container-tag: vpn container-tag: ike-sa leaf-tag: gateway value: vpn_TEST pop-tag: pop-tag:)
((eol-matched: . #t) (context-inserted-at-end-p: . #f))


<request cmd="op" cookie="9269646798652701" uid="1004"><operations><test><vpn><ike-sa><gateway>vpn_TEST</gateway></ike-sa></vpn></test></operations></request>


2022-06-28 09:43:26
<response status="success"><result><![CDATA[Start time: Jun.28 09:43:26
Initiate 1 IKE SA.
]]></result></response>

Start time: Jun.28 09:43:26
Initiate 1 IKE SA.

yoda27mhz@FW-PA-01(active)> test vpn ipsec-sa tunnel vpn_TEST:Red_1
(container-tag: vpn container-tag: ipsec-sa leaf-tag: tunnel value: vpn_TEST:Red_1 pop-tag: pop-tag:)
((eol-matched: . #t) (context-inserted-at-end-p: . #f))


<request cmd="op" cookie="9269646798652701" uid="1004"><operations><test><vpn><ipsec-sa><tunnel>vpn_TEST:Red_1</tunnel></ipsec-sa></vpn></test></operations></request>


2022-06-28 09:52:47
<response status="success"><result><![CDATA[Start time: Jun.28 09:52:47
Initiate 1 IPSec SA for tunnel vpn_TEST:Red_1.
]]></result></response>

Start time: Jun.28 09:52:47
Initiate 1 IPSec SA for tunnel vpn_TEST:Red_1.

```
From this lines the bold text will be copied.

<request cmd="op" cookie="9269646798652701" uid="1004"><operations>***<test><vpn><ike-sa><gateway>vpn_TEST</gateway></ike-sa></vpn></test>***</operations></request>
And the first command for reset Phase1:
```bash=
/home/yoda27mhz/panxapi.py -xo '<test><vpn><ike-sa><gateway>vpn_TEST</gateway></ike-sa></vpn></test>' -h 192.168.1.1 -K "LUFRPT0blablablablablablablablablablablablablablablablablalbalablaZLajU2KzlYVDBIOA=="
```

<request cmd="op" cookie="9269646798652701" uid="1004"><operations>***<test><vpn><ipsec-sa><tunnel>vpn_TEST:Red_1</tunnel></ipsec-sa></vpn></test>***</operations></request>

The second command for reset Phase2:
```bash=
/home/yoda27mhz/panxapi.py -xo '<test><vpn><ipsec-sa><tunnel>vpn_TEST:Red_1</tunnel></ipsec-sa></vpn></test>' -h 192.168.1.1 -K "LUFRPT0blablablablablablablablablablablablablablablablablalbalablaZLajU2KzlYVDBIOA=="
```
## THE BASH SCRIPT
```bash=
#!/bin/bash
# 2022-06-27 - RAUL PEREZ - Reset Tunnel
greenColour="\e[0;32m\033[1m"
endColour="\033[0m\e[0m"
redColour="\e[0;31m\033[1m"


/home/yoda27mhz/panxapi.py -xo '<test><vpn><ike-sa><gateway>vpn_TEST</gateway></ike-sa></vpn></test>' -h 192.168.1.1 -K "LUFRPT0blablablablablablablablablablablablablablablablablalbalablaZLajU2KzlYVDBIOA=="
echo -e "${greenColour}[*] Reset Phase 1 ${endColour}"
/home/yoda27mhz/panxapi.py -xo '<test><vpn><ipsec-sa><tunnel>vpn_TEST:Red_1</tunnel></ipsec-sa></vpn></test>' -h 192.168.1.1 -K "LUFRPT0blablablablablablablablablablablablablablablablablalbalablaZLajU2KzlYVDBIOA=="
/home/yoda27mhz/panxapi.py -xo '<test><vpn><ipsec-sa><tunnel>vpn_TEST:Red_2</tunnel></ipsec-sa></vpn></test>' -h 192.168.1.1 -K "LUFRPT0blablablablablablablablablablablablablablablablablalbalablaZLajU2KzlYVDBIOA=="
/home/yoda27mhz/panxapi.py -xo '<test><vpn><ipsec-sa><tunnel>vpn_TEST:Red_3</tunnel></ipsec-sa></vpn></test>' -h 192.168.1.1 -K "LUFRPT0blablablablablablablablablablablablablablablablablalbalablaZLajU2KzlYVDBIOA=="
echo -e "${greenColour}[*] Reset Phase 2 ${endColour}"
```
