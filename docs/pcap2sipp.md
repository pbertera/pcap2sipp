# pcap2sipp

The purpose of this tool is to generate the sipp scenario, injection file and RTP packets that are needed to run a sipp test that simulates the behavior from a given pcap trace. For now it only works on cleartext SIP packets and the pcap trace must not contained fragmented packets.

## Usage

```
Usage: pcap2sipp [options]
 Available options:
  -o,--option                   specify what action to do (listips, listcallids or simulate)
  -f,--file                     pcap file
  -c,--call_id                  CallID to simulate (use -o listcallids to get a list of available CallIDs)
  -i,--ip                       IP which corresponds the DUT in the pcap file (use -o listips to get a list of available IPs)
  -p,--port                     port which corresponds the DUT in the pcap file
  -a,--remote_ip                remote IP - IP of DUT
  -d,--remote_port              remote port - port of DUT
  -b,--local_ip                 local IP
  -j,--local_port               local port
  -r,--remote_nr                phone number of DUT
  -e,--remote_name              caller ID name of DUT
  -l,--local_nr                 local phone number file
  -g,--local_name               local caller ID name file
  -s,--local_password           password for local phone number - only needed if DUT sends an authentication request
```

Depending on what you enter as -o , you can use one of the following functions:

### listips
```
--listips		lists the IP addresses found in the given pcap trace which have been detected to send SIP messages
				Example:
				# ./pcap2sipp -o listips -f ../trace22.pcap
				******************* Available IP addresses **********************
				202.79.217.195
				10.10.12.157
				******************************************************************
```

### listcallids
```
--listcallids	lists the call IDs of the calls found in the given pcap trace
				Example:
				# ./pcap2sipp -o listcallids -f ../trace22.pcap
				********************** Available Call IDs ************************
				CXC-246-642f8670-fcd94fca-13c4-4fd5d306-e43630cd-1a83fd52@test.com
				2a00000000a0-jjnfo7on9cmq
				CXC-408-642f8d70-fcd94fca-13c4-4fd5d30e-e4364ea7-1cf85647@test.com
				******************************************************************
```

### simulate
```
--simulate		this is the most complex function - generating the sipp scenario, injection file and RTP packets needed to run a sipp test that simulates
				the behavior from the given pcap trace
				Example:
				First, we must tell the problem which call and which IP:port we want to simulate. In this example we are interested in simulating call CXC-246-642f8670-fcd94fca-13c4-4fd5d306-e43630cd-1a83fd52@test.com ,
				and in the trace the DUT (devide under test) has IP 10.10.12.157 and port 5060.
				Then, we tell the program the addresses of our current test participants: --remote_ip 10.10.12.15 and --remote_port 5060 means that in
				our test the DUT will be 10.10.12.15:5060 .
				Also, --local_ip is the IP of the device on which we are running the test.

				# ./pcap2sipp -o simulate -f ../trace22.pcap -c CXC-246-642f8670-fcd94fca-13c4-4fd5d306-e43630cd-1a83fd52@test.com
				--ip 10.10.12.157 --port 5060 --remote_port 5060 --remote_ip 10.10.12.15 --local_ip 10.10.21.219
				********************** Generating simulation files *************************
				The RTP file was generated. Path: /tmp/rtp.pcap
				All necessary data was succesfully generated. You can now run sipp with command:
				rm -f /tmp/*.log; ./sipp -sf /tmp/sipp_scenario.xml -inf /tmp/sipp_injection.csv -i 10.10.21.219 -p 5060  10.10.12.15:5060 -m 1 -trace_msg -d 2000
```
