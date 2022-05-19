# Tips and tricks


### RPC XDAQ Links

**Procces Control:**
http://vmepc-s2g17-29-01.cms:1002/urn:xdaq-application:lid=12

**Supervisor:**
http://l1ts-rpc-05.cms:2974/urn:xdaq-application:lid=13/#!/Operations/Configuration

**FEB Publisher**
http://l1ts-rpc-02.cms:1979/urn:xdaq-application:lid=1979/

**Conf DB GUI:**
http://pcrpct04.cms:8080/rpct-masking/

**DCC XDAQ:**
http://pcrpct03.cms:1974/urn:xdaq-application:lid=8002

### Muon Processors  SWATCH Cells
 
**CPPF** - http://l1ts-cppf.cms:3333/urn:xdaq-application:lid=13/#!/Control%20Panels/4.%20Masking
**OMTF** - http://l1ts-omtf.cms:3333/urn:xdaq-application:lid=13/#!/Control%20Panels/4.%20Masking
**TwinMux** - http://l1ts-twinmux.cms:3333/urn:xdaq-application:lid=13/#!/Control%20Panels/4.%20Masking
**TwinMux** (TCDS ICI Cell) - http://l1ts-twinmux.cms:4500/urn:xdaq-application:lid=13/#!/Operations/Run%20Control
**EMTF** - http://l1ts-emtf.cms:3333/urn:xdaq-application:service=hyperdaq
**BMTF** - http://l1ts-bmtf.cms:3333/urn:xdaq-application:service=hyperdaq

---
### Usefull refenrences
https://twiki.cern.ch/twiki/bin/view/CMS/RpcPacT1
https://twiki.cern.ch/twiki/bin/view/CMS/TroubleShooting
https://twiki.cern.ch/twiki/bin/view/CMS/MuonRPC
https://twiki.cern.ch/twiki/bin/view/CMS/RPComm

---

### Procedure to check the RPC x TwinMux Links

- DT Official instructions: https://twiki.cern.ch/twiki/bin/viewauth/CMS/DTexpertFAQ2020x10x16

**RPC Instructions (old but useful)**
- turn on the RPC barrel Link System.
- configure the RPC link system (i.e. put the trigger supervisor cell into Configure state).
- turn on/powercycle TwinMux.
- at TwinMux, in the Halted State, in the FED Map field on the Run Control panel, put 793&3%.
- configure TwinMux listening to CPM.
- on the RPC Supervisor execute SynchronizeLinksCommand: http://l1ts-rpc-05.cms:2974/urn:xdaq-application:lid=13/#!/Commands/SynchronizeLinksCommand .
- go to the Running state in the TwinMux and check the RPC links status (http://l1ts-twinmux.cms:3333/urn:xdaq-application:lid=13/#!/Control%20Panels/1.%20Summary).

---
### RPC vmepcs

```
vmepc-s2g17-25-01 #pcrpct01
vmepc-s2g17-26-01 #pcrpct02
vmepc-s2g17-27-01 #pcrpct03
vmepc-s2g17-28-01 #pcrpct04
vmepc-s2g17-29-01 #pcrpct05
```

---
### TCDS CLI

To configure TCDS from the cli (`rpcpro@pcrpct03`):

```

# ICI
python /nfshome0/ftorresd/tcds_cli/tcds/simpleTcdsControlWrapper.py --host tcds-control-rpc-pri.cms --port 2103 --id 301 --hwconfig /nfshome0/rpcpro/TCDS/TCDS-RPC-CPM-HardReset.txt -c Configure --user ftorresd

# PI1
python /nfshome0/ftorresd/tcds_cli/tcds/simpleTcdsControlWrapper.py --host tcds-control-rpc-pri.cms --port 2103 --id 501 --hwconfig /nfshome0/rpcpro/TCDS/TCDS-RPC-pi.txt -c Configure  --user ftorresd

# PI2
python /nfshome0/ftorresd/tcds_cli/tcds/simpleTcdsControlWrapper.py --host tcds-control-rpc-pri.cms --port 2103 --id 511 --hwconfig /nfshome0/rpcpro/TCDS/TCDS-RPC-pi.txt -c Configure  --user ftorresd

```

Their status can be check at:

http://tcds-control-rpc-pri.cms:2103/urn:xdaq-application:service=hyperdaq

<!-- ---

### XdaqLBoxAccess problem @ Configuration step
 
Probably you are not patient enough.
Maybe the RpctDbService in pcrpct01 is not responding.

```
ssh pcrpct01
sudo -u rpcdev -H bash -l #could be rpcpro (?)
~/opt/tomcat/bin/shutdown.sh

wait that no process are called tomcat (ps -ef | grep -i tomcat)

~/opt/tomcat/bin/startup.sh 
``` -->
---


### Load the DCC's firmware

```
ssh pcrpct03
sudo -u rpcpro -H bash -l
cd /nfshome0/rpcpro/rpctdata/crates_boot/DCC_pcrpct03/dcc_2012_06_26_SLC5/
./RPC_DCC.sh
```

This might take a while.

Then start the DCC XDAQ:

```
 $XDAQ_ROOT/bin/xdaq.sh -h pcrpct03 -p 1974 -c /nfshome0/rpcpro/bin/XdaqDccAccess.xml
```

Configure from the GUI:
http://pcrpct03.cms:1974/


---

### RPC RCMS GUI:

rpcpro - http://cmsrc-rpc.cms:14000/rcms

rpcdev - http://cmsrc-rpc.cms:34000/rcms

---

### Configuration key to be used:

```LHC10```

---

### TTC Hard Reset:

```enable Hard Reset by TTC``` should be used only when in Global.


---

### RPC MASKING TOMCAT

http://l1ts-rpc-04.cms:8080/rpct-masking/

```
ssh pcrpct04
sudo -u rpcpro -H bash -l
cd ~rpcpro/opt/tomcat_masking/bin
./shutdown.sh # This is only a precaution to terminate a Tomcat that may be running
./startup.sh
ps aux | grep tomcat # Make sure it is running

```

### How to restart DB Service tomcat

```
ssh pcrpct01
sudo -u rpcpro -H bash -l
cd ~rpcpro
./opt/tomcat/bin/shutdown.sh  # this is only a precaution to terminate a Tomcat that may be running
./opt/tomcat/bin/startup.sh 
ps aux | grep tomcat  # to make sure it is running
```
Check logfiles in ```~rpcpro/opt/tomcat/logs/catalina.out```.

---

### Get a dump of the RPC CONF COND DB

- Open a ssh connection to `cmsusr`.
- Open an X2GO session to the CMS P5 Cluster
  - Typical configurarition: [ClusterUsersGuide](https://twiki.cern.ch/twiki/bin/viewauth/CMS/ClusterUsersGuide)
![](https://codimd.web.cern.ch/uploads/upload_b6cb240bc1ee75d840c40b59082bddf2.png)
- From the X2GO session terminal, sudo to `rpcpro` and open the **sql developer**.
  - `cd ; ../tbdev/opt/sqldeveloper/sqldeveloper.sh` 
  - The password can be found at `/nfshome0/rpcpro/hibernate.cfg.xml` 
  - Menu: Tools --> Export Database

---

### Fixes in the RPC XDAQ configuration xmls needed for CC7

-  **Commands to start/stop the workers:**

`sudo systemctl {start/stop/restart/status} rpc.target`

`systemctl list-dependencies trigger.target`

 

`sudo systemctl start rpc.rpc-worker-lb-w0-far@vmepc-s2g17-27-01.service`

`sudo systemctl start rpc.target`

 

- **Make link from** `/opt/xdaq/htdocs/rpct/` to `/nfshome0/rpcpro/TriDAS/rpct/`

 `sudo ln -s /nfshome0/rpcpro/TriDAS/rpct /opt/xdaq/htdocs/rpct `

 
- **At `/usr/lib/systemd/system`**

`sudo sed -i 's/-e ${XDAQ_PROFILE}/-e ${XDAQ_PROFILE} -c ${XDAQ_CONFIG}/' /usr/lib/systemd/system/rpc.*.service
`

`sudo sed -i '17i Environment=XDAQ_PLATFORM=x86_64_centos7' /usr/lib/systemd/system/rpc.*.service`

 

The bellow is a try to fix a problem of timeout when the workers connects to the tomcat DB service. The hypothesis was that is due too many parallel accesses when the workers are started. But I am not sure if this is the reason and if the following helps:

 

```

sudo sed -i '29i ExecStartPre=/bin/sleep 3' /usr/lib/systemd/system/rpc.rpc-worker-tc-1@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 6' /usr/lib/systemd/system/rpc.rpc-worker-tc-0@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 3' /usr/lib/systemd/system/rpc.rpc-worker-tc-4@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 6' /usr/lib/systemd/system/rpc.rpc-worker-tc-3@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 3' /usr/lib/systemd/system/rpc.rpc-worker-tc-10@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 6' /usr/lib/systemd/system/rpc.rpc-worker-tc-9@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 3' /usr/lib/systemd/system/rpc.rpc-worker-tc-7@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 6' /usr/lib/systemd/system/rpc.rpc-worker-tc-6@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 1' /usr/lib/systemd/system/rpc.rpc-worker-lb-w0-far@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 1' /usr/lib/systemd/system/rpc.rpc-worker-lb-w0-near@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 2' /usr/lib/systemd/system/rpc.rpc-worker-lb-wm1-far@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 2' /usr/lib/systemd/system/rpc.rpc-worker-lb-wm1-near@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 3' /usr/lib/systemd/system/rpc.rpc-worker-lb-wm2-far@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 3' /usr/lib/systemd/system/rpc.rpc-worker-lb-wm2-near@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 4' /usr/lib/systemd/system/rpc.rpc-worker-lb-wp1-far@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 4' /usr/lib/systemd/system/rpc.rpc-worker-lb-wp1-near@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 5' /usr/lib/systemd/system/rpc.rpc-worker-lb-wp2-far@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 5' /usr/lib/systemd/system/rpc.rpc-worker-lb-wp2-near@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 6' /usr/lib/systemd/system/rpc.rpc-worker-lb-yen1-far@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 6' /usr/lib/systemd/system/rpc.rpc-worker-lb-yen1-near@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 7' /usr/lib/systemd/system/rpc.rpc-worker-lb-yen3-far@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 7' /usr/lib/systemd/system/rpc.rpc-worker-lb-yen3-near@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 8' /usr/lib/systemd/system/rpc.rpc-worker-lb-yep1-far@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 8' /usr/lib/systemd/system/rpc.rpc-worker-lb-yep1-near@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 9' /usr/lib/systemd/system/rpc.rpc-worker-lb-yep3-far@.service
sudo sed -i '29i ExecStartPre=/bin/sleep 9' /usr/lib/systemd/system/rpc.rpc-worker-lb-yep3-near@.service


```

To reload to memory.

```
sudo systemctl daemon-reload
sudo systemctl restart rpc.target
```

- **If one gets:**

```
18 Feb 2018 12:13:43.111 [140496742418624] ERROR rpc.cms.l1ts-rpc-03.p:1955.executive::Application.lid(0) <> - Caught exception: xdaq::exception::ConfigurationError 'Cannot load module from file:${BUILD_HOME}/${XDAQ_PLATFORM}/lib/librpcttsxworker.so' raised at downloadModules(/usr/local/src/xdaq/baseline14/daq/executive/src/common/Application.cc:1308);

originated by xdaq::exception::Exception 'Cannot load module /nfshome0/rpcpro/TriDAS/x86_64_centos7/lib/librpcttsxworker.so' raised at loadModule(/usr/local/src/xdaq/baseline14/trunk/daq/xdaq/src/common/ApplicationContextImpl.cc:1570);

originated by xdaq::exception::LoadFailed 'libASImageGui.so: cannot open shared object file: No such file or directory' raised at load(/usr/local/src/xdaq/baseline14/trunk/daq/xdaq/src/common/SharedObjectRegistry.cc:196)
```
It means ROOT is not avaialble, should be set in the LD_LIBRARY_PATH.

- **At `/opt/xdaq/share/rpc/profile/`:**
```
sudo sed -i 's/tcds-control-rpc/tcds-control-rpc-pri/' rpc*configure
sudo sed -i 's/TCDS_ICI/TCDS_ICI_PRI/' rpc-worker-tcds-ici.xhannel
sudo sed -i 's/TCDS_PI/TCDS_PI_PRI/' rpc-worker-tcds-pi1.xhannel
sudo sed -i 's/TCDS_PI/TCDS_PI_PRI/' rpc-worker-tcds-pi2.xhannel
 ```
 
 ```
# tcdststcdscell::Cell --> tcdscell::Cell
# libtcdststcdscell.so --> libtcdscell.so
 
 sudo sed -i 's/tcdststcdscell/tcdscell/' rpc*configure
 sudo sed -i 's/tcdststcdscell/tcdscell/' rpc-supervisor.xhannel
 
```

- **To fix the issue with the Connectivity test GUI  (@pcrpct05)**

Add`Environment=LD_LIBRARY_PATH=/opt/xdaq/lib` to `/usr/lib/systemd/system/rpc.rpc-febconnectivitytestweb.service` (right before `ExecStart`).

Then: 
`sudo ln -s /opt/xdaq/lib/liblog4cplus.so /opt/xdaq/lib/liblog4cplus.so.7`

Finally:

```
sudo systemctl daemon-reload
sudo systemctl stop rpc.rpc-febconnectivitytestweb.service
sudo systemctl start rpc.rpc-febconnectivitytestweb.service
```

- **ROOT**

To get ROOT compiling and working, one has to install it in all machines:

```
sudo yum -y install libXpm-devel
sudo yum -y install libjpeg*
```

Also, when compiling ROOT 5, `lz4` will try to download something from github. Will  not work. You should download by yourself and modify the `Makefile`.




### Link Boards mapping

![](https://codimd.web.cern.ch/uploads/upload_7bd62626b6630c7621df1f7f60b24858.png)




### Optical Links Map

<details>
    
```
TC_0
TBn3_0
0 0
0 1
0 2
3 0   LB_RE-3_S1_EN32_CH0    RE-3/2/1;    TBn3_0 3, 
3 1   LB_RE-3_S1_EN32_CH1    RE-3/2/36;  
3 2   LB_RE-3_S1_EN32_CH2    RE-3/2/2;  
4 0   LB_RE-3_S1_EN33_CH0    RE-3/3/1;    TBn3_0 4, 
4 1   LB_RE-3_S1_EN33_CH1    RE-3/3/36;  
4 2   LB_RE-3_S1_EN33_CH2    RE-3/3/2;  
5 0   LB_RE-1_S1_EN22_CH0    RE-2/2/3;    TBn3_0 5, 
5 1   LB_RE-1_S1_EN22_CH1    RE-2/2/2;  
5 2   LB_RE-1_S1_EN22_CH2    RE-2/2/4;  
6 0   LB_RE-1_S1_EN23_CH0    RE-2/3/3;    TBn3_0 6, TBn2_0 6, 
6 1   LB_RE-1_S1_EN23_CH1    RE-2/3/2;  
6 2   LB_RE-1_S1_EN23_CH2    RE-2/3/4;  
7 0   LB_RE-4_S1_EN42_CH0    RE-4/2/1;    TBn3_0 7, 
7 1   LB_RE-4_S1_EN42_CH1    RE-4/2/36;  
7 2   LB_RE-4_S1_EN42_CH2    RE-4/2/2;  
8 0   LB_RE-4_S1_EN43_CH0    RE-4/3/1;    TBn3_0 8, TBn3_11 11, 
8 1   LB_RE-4_S1_EN43_CH1    RE-4/3/36;  
8 2   LB_RE-4_S1_EN43_CH2    RE-4/3/2;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE-4_S2_EN43_CH0    RE-4/3/4;    TBn3_0 11, TBn3_1 8, 
11 1   LB_RE-4_S2_EN43_CH1    RE-4/3/3;  
11 2   LB_RE-4_S2_EN43_CH2    RE-4/3/5;  
12 0   LB_RE-4_S2_EN42_CH0    RE-4/2/4;    TBn3_0 12, TBn3_1 7, 
12 1   LB_RE-4_S2_EN42_CH1    RE-4/2/3;  
12 2   LB_RE-4_S2_EN42_CH2    RE-4/2/5;  
13 0   LB_RE-3_S2_EN33_CH0    RE-3/3/4;    TBn3_0 13, TBn3_1 4, 
13 1   LB_RE-3_S2_EN33_CH1    RE-3/3/3;  
13 2   LB_RE-3_S2_EN33_CH2    RE-3/3/5;  
14 0   LB_RE-3_S2_EN32_CH0    RE-3/2/4;    TBn3_0 14, TBn3_1 3, 
14 1   LB_RE-3_S2_EN32_CH1    RE-3/2/3;  
14 2   LB_RE-3_S2_EN32_CH2    RE-3/2/5;  
15 0   LB_RE-1_S2_EN12_CH0    RE-1/2/4;    TBn3_0 15, TBn3_1 2, 
15 1   LB_RE-1_S2_EN12_CH1    RE-1/2/3;  
15 2   LB_RE-1_S2_EN12_CH2    RE-1/2/5;  
16 0   LB_RB-2_S2_BN2B_CH0    W-2/RB1out/2 Forward;    TBn3_0 16, TBn2_0 16, TBn3_1 1, TBn2_1 1, 
16 1   LB_RB-2_S2_BN2B_CH1    W-2/RB2in/2 Forward;  
16 2   LB_RB-2_S2_BN2B_CH2    W-2/RB1in/2 Forward;  
17 0
17 1
17 2

TBn2_0
3 0   LB_RB-2_S1_BN2D_CH0    W-2/RB4/1+ Backward;  W-2/RB4/1- Backward;    TBn2_11 14, TBn1_11 12, TBn2_0 3, 
3 1
3 2   LB_RB-2_S1_BN2D_CH2    W-2/RB3/1+ Backward;  W-2/RB3/1- Backward;  
5 0   LB_RB-1_S1_BN1B_CH0    W-1/RB1out/1 Forward;    TBn1_11 16, TBn2_0 5, TBn2_11 12, 
5 1   LB_RB-1_S1_BN1B_CH1    W-1/RB2out/1 Forward;  
5 2   LB_RB-1_S1_BN1B_CH2    W-1/RB1in/1 Forward;  
6 0   LB_RE-1_S1_EN23_CH0    RE-2/3/3;    TBn3_0 6, TBn2_0 6, 
6 1   LB_RE-1_S1_EN23_CH1    RE-2/3/2;  
6 2   LB_RE-1_S1_EN23_CH2    RE-2/3/4;  
7 0   LB_RE-1_S1_EN13_CH0    RE-1/3/1;    TBn2_11 10, TBn2_0 7, 
7 1   LB_RE-1_S1_EN13_CH1    RE-1/3/36;  
7 2   LB_RE-1_S1_EN13_CH2    RE-1/3/2;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE-1_S2_EN13_CH0    RE-1/3/4;    TBn2_0 10, TBn2_1 7, 
10 1   LB_RE-1_S2_EN13_CH1    RE-1/3/3;  
10 2   LB_RE-1_S2_EN13_CH2    RE-1/3/5;  
11 0
11 1
11 2
12 0   LB_RB-1_S2_BN1B_CH0    W-1/RB1out/2 Forward;    TBn2_0 12, TBn2_1 5, TBn1_0 16, 
12 1   LB_RB-1_S2_BN1B_CH1    W-1/RB2out/2 Forward;  
12 2   LB_RB-1_S2_BN1B_CH2    W-1/RB1in/2 Forward;  
13 0   LB_RB-2_S2_BN2E_CH0    W-2/RB4/2+ Forward;  W-2/RB4/2- Forward;    TBn2_0 13, TBn1_0 11, TBn2_1 4, TBn1_1 6, 
13 1
13 2   LB_RB-2_S2_BN2E_CH2    W-2/RB3/2+ Forward;  W-2/RB3/2- Forward;  
14 0   LB_RB-2_S2_BN2D_CH0    W-2/RB4/2+ Backward;  W-2/RB4/2- Backward;    TBn2_0 14, TBn1_0 12, TBn2_1 3, 
14 1
14 2   LB_RB-2_S2_BN2D_CH2    W-2/RB3/2+ Backward;  W-2/RB3/2- Backward;  
15 0   LB_RB-2_S2_BN2C_CH0    W-2/RB2out/2 Central;    TBn2_0 15, TBn2_1 2, 
15 1   LB_RB-2_S2_BN2C_CH1    W-2/RB2out/2 Forward;  
15 2   LB_RB-2_S2_BN2C_CH2    W-2/RB2out/2 Backward;  
16 0   LB_RB-2_S2_BN2B_CH0    W-2/RB1out/2 Forward;    TBn3_0 16, TBn2_0 16, TBn3_1 1, TBn2_1 1, 
16 1   LB_RB-2_S2_BN2B_CH1    W-2/RB2in/2 Forward;  
16 2   LB_RB-2_S2_BN2B_CH2    W-2/RB1in/2 Forward;  
17 0   LB_RB-2_S2_BN2A_CH0    W-2/RB1out/2 Backward;    TBn2_0 17, TBn2_1 0, 
17 1   LB_RB-2_S2_BN2A_CH1    W-2/RB2in/2 Backward;  
17 2   LB_RB-2_S2_BN2A_CH2    W-2/RB1in/2 Backward;  

TBn1_0
3 0   LB_RB-1_S1_BN1D_CH0    W-1/RB4/1+ Backward;  W-1/RB4/1- Backward;    TBn1_11 14, TB0_11 12, TBn1_0 3, 
3 1
3 2   LB_RB-1_S1_BN1D_CH2    W-1/RB3/1+ Backward;  W-1/RB3/1- Backward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S2_BP0B_CH0    W0/RB1out/2 Backward;    TB0_0 16, TB0_1 1, TBn1_0 10, 
10 1   LB_RB0_S2_BP0B_CH1    W0/RB2out/2 Backward;  
10 2   LB_RB0_S2_BP0B_CH2    W0/RB1in/2 Backward;  
11 0   LB_RB-2_S2_BN2E_CH0    W-2/RB4/2+ Forward;  W-2/RB4/2- Forward;    TBn2_0 13, TBn1_0 11, TBn2_1 4, TBn1_1 6, 
11 1
11 2   LB_RB-2_S2_BN2E_CH2    W-2/RB3/2+ Forward;  W-2/RB3/2- Forward;  
12 0   LB_RB-2_S2_BN2D_CH0    W-2/RB4/2+ Backward;  W-2/RB4/2- Backward;    TBn2_0 14, TBn1_0 12, TBn2_1 3, 
12 1
12 2   LB_RB-2_S2_BN2D_CH2    W-2/RB3/2+ Backward;  W-2/RB3/2- Backward;  
13 0   LB_RB-1_S2_BN1E_CH0    W-1/RB4/2+ Forward;  W-1/RB4/2- Forward;    TBn1_0 13, 
13 1
13 2   LB_RB-1_S2_BN1E_CH2    W-1/RB3/2+ Forward;  W-1/RB3/2- Forward;  
14 0   LB_RB-1_S2_BN1D_CH0    W-1/RB4/2+ Backward;  W-1/RB4/2- Backward;    TBn1_0 14, TB0_0 12, TBn1_1 3, 
14 1
14 2   LB_RB-1_S2_BN1D_CH2    W-1/RB3/2+ Backward;  W-1/RB3/2- Backward;  
15 0   LB_RB-1_S2_BN1C_CH0    W-1/RB2in/2 Central;    TBn1_0 15, 
15 1   LB_RB-1_S2_BN1C_CH1    W-1/RB2in/2 Forward;  
15 2   LB_RB-1_S2_BN1C_CH2    W-1/RB2in/2 Backward;  
16 0   LB_RB-1_S2_BN1B_CH0    W-1/RB1out/2 Forward;    TBn2_0 12, TBn2_1 5, TBn1_0 16, 
16 1   LB_RB-1_S2_BN1B_CH1    W-1/RB2out/2 Forward;  
16 2   LB_RB-1_S2_BN1B_CH2    W-1/RB1in/2 Forward;  
17 0   LB_RB-1_S2_BN1A_CH0    W-1/RB1out/2 Backward;    TBn1_0 17, 
17 1   LB_RB-1_S2_BN1A_CH1    W-1/RB2out/2 Backward;  
17 2   LB_RB-1_S2_BN1A_CH2    W-1/RB1in/2 Backward;  

TB0_0
0 0   LB_RB0_S1_BN0B_CH0    W0/RB1out/1 Forward;    TB0_11 17, TB0_0 0, TBn1_11 10, 
0 1   LB_RB0_S1_BN0B_CH1    W0/RB2out/1 Forward;  
0 2   LB_RB0_S1_BN0B_CH2    W0/RB1in/1 Forward;  
1 0   LB_RB0_S1_BP0B_CH0    W0/RB1out/1 Backward;    TB0_11 16, TB0_0 1, TBp1_11 10, 
1 1   LB_RB0_S1_BP0B_CH1    W0/RB2out/1 Backward;  
1 2   LB_RB0_S1_BP0B_CH2    W0/RB1in/1 Backward;  
6 0   LB_RB+1_S1_BP1D_CH0    W+1/RB4/1+ Backward;  W+1/RB4/1- Backward;    TB0_11 11, TBp1_11 14, TB0_0 6, 
6 1
6 2   LB_RB+1_S1_BP1D_CH2    W+1/RB3/1+ Backward;  W+1/RB3/1- Backward;  
7 0
7 1
7 2
8 0
8 1
8 2
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RB+1_S2_BP1D_CH0    W+1/RB4/2+ Backward;  W+1/RB4/2- Backward;    TB0_0 11, TBp1_0 14, TB0_1 6, 
11 1
11 2   LB_RB+1_S2_BP1D_CH2    W+1/RB3/2+ Backward;  W+1/RB3/2- Backward;  
12 0   LB_RB-1_S2_BN1D_CH0    W-1/RB4/2+ Backward;  W-1/RB4/2- Backward;    TBn1_0 14, TB0_0 12, TBn1_1 3, 
12 1
12 2   LB_RB-1_S2_BN1D_CH2    W-1/RB3/2+ Backward;  W-1/RB3/2- Backward;  
13 0   LB_RB0_S2_BP0E_CH0    W0/RB4/2+ Backward;  W0/RB4/2- Backward;    TB0_0 13, 
13 1
13 2   LB_RB0_S2_BP0E_CH2    W0/RB3/2+ Backward;  W0/RB3/2- Backward;  
14 0   LB_RB0_S2_BN0E_CH0    W0/RB4/2+ Forward;  W0/RB4/2- Forward;    TB0_0 14, 
14 1
14 2   LB_RB0_S2_BN0E_CH2    W0/RB3/2+ Forward;  W0/RB3/2- Forward;  
15 0   LB_RB0_S2_BM0C_CH0    W0/RB2in/2 Central;    TB0_0 15, 
15 1   LB_RB0_S2_BM0C_CH1    W0/RB2in/2 Forward;  
15 2   LB_RB0_S2_BM0C_CH2    W0/RB2in/2 Backward;  
16 0   LB_RB0_S2_BP0B_CH0    W0/RB1out/2 Backward;    TB0_0 16, TB0_1 1, TBn1_0 10, 
16 1   LB_RB0_S2_BP0B_CH1    W0/RB2out/2 Backward;  
16 2   LB_RB0_S2_BP0B_CH2    W0/RB1in/2 Backward;  
17 0   LB_RB0_S2_BN0B_CH0    W0/RB1out/2 Forward;    TB0_0 17, TB0_1 0, TBp1_0 10, 
17 1   LB_RB0_S2_BN0B_CH1    W0/RB2out/2 Forward;  
17 2   LB_RB0_S2_BN0B_CH2    W0/RB1in/2 Forward;  

TBp1_0
1 0   LB_RB+1_S1_BP1B_CH0    W+1/RB1out/1 Forward;    TBp1_11 16, TBp2_11 12, TBp1_0 1, 
1 1   LB_RB+1_S1_BP1B_CH1    W+1/RB2out/1 Forward;  
1 2   LB_RB+1_S1_BP1B_CH2    W+1/RB1in/1 Forward;  
5 0   LB_RB+2_S1_BP2D_CH0    W+2/RB4/1+ Backward;  W+2/RB4/1- Backward;    TBp1_11 12, TBp2_11 14, TBp1_0 5, 
5 1
5 2   LB_RB+2_S1_BP2D_CH2    W+2/RB3/1+ Backward;  W+2/RB3/1- Backward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S2_BN0B_CH0    W0/RB1out/2 Forward;    TB0_0 17, TB0_1 0, TBp1_0 10, 
10 1   LB_RB0_S2_BN0B_CH1    W0/RB2out/2 Forward;  
10 2   LB_RB0_S2_BN0B_CH2    W0/RB1in/2 Forward;  
11 0   LB_RB+2_S2_BP2E_CH0    W+2/RB4/2+ Forward;  W+2/RB4/2- Forward;    TBp1_0 11, TBp2_0 13, TBp1_1 6, TBp2_1 4, 
11 1
11 2   LB_RB+2_S2_BP2E_CH2    W+2/RB3/2+ Forward;  W+2/RB3/2- Forward;  
12 0   LB_RB+2_S2_BP2D_CH0    W+2/RB4/2+ Backward;  W+2/RB4/2- Backward;    TBp1_0 12, TBp2_0 14, TBp1_1 5, 
12 1
12 2   LB_RB+2_S2_BP2D_CH2    W+2/RB3/2+ Backward;  W+2/RB3/2- Backward;  
13 0   LB_RB+1_S2_BP1E_CH0    W+1/RB4/2+ Forward;  W+1/RB4/2- Forward;    TBp1_0 13, 
13 1
13 2   LB_RB+1_S2_BP1E_CH2    W+1/RB3/2+ Forward;  W+1/RB3/2- Forward;  
14 0   LB_RB+1_S2_BP1D_CH0    W+1/RB4/2+ Backward;  W+1/RB4/2- Backward;    TB0_0 11, TBp1_0 14, TB0_1 6, 
14 1
14 2   LB_RB+1_S2_BP1D_CH2    W+1/RB3/2+ Backward;  W+1/RB3/2- Backward;  
15 0   LB_RB+1_S2_BP1C_CH0    W+1/RB2in/2 Central;    TBp1_0 15, 
15 1   LB_RB+1_S2_BP1C_CH1    W+1/RB2in/2 Forward;  
15 2   LB_RB+1_S2_BP1C_CH2    W+1/RB2in/2 Backward;  
16 0   LB_RB+1_S2_BP1B_CH0    W+1/RB1out/2 Forward;    TBp1_0 16, TBp2_0 12, TBp1_1 1, 
16 1   LB_RB+1_S2_BP1B_CH1    W+1/RB2out/2 Forward;  
16 2   LB_RB+1_S2_BP1B_CH2    W+1/RB1in/2 Forward;  
17 0   LB_RB+1_S2_BP1A_CH0    W+1/RB1out/2 Backward;    TBp1_0 17, 
17 1   LB_RB+1_S2_BP1A_CH1    W+1/RB2out/2 Backward;  
17 2   LB_RB+1_S2_BP1A_CH2    W+1/RB1in/2 Backward;  

TBp2_0
6 0   LB_RE+1_S1_EP23_CH0    RE+2/3/3;    TBp2_0 6, TBp3_0 6, 
6 1   LB_RE+1_S1_EP23_CH1    RE+2/3/2;  
6 2   LB_RE+1_S1_EP23_CH2    RE+2/3/4;  
7 0   LB_RE+1_S1_EP13_CH0    RE+1/3/1;    TBp2_11 10, TBp2_0 7, 
7 1   LB_RE+1_S1_EP13_CH1    RE+1/3/36;  
7 2   LB_RE+1_S1_EP13_CH2    RE+1/3/2;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE+1_S2_EP13_CH0    RE+1/3/4;    TBp2_0 10, TBp2_1 7, 
10 1   LB_RE+1_S2_EP13_CH1    RE+1/3/3;  
10 2   LB_RE+1_S2_EP13_CH2    RE+1/3/5;  
11 0
11 1
11 2
12 0   LB_RB+1_S2_BP1B_CH0    W+1/RB1out/2 Forward;    TBp1_0 16, TBp2_0 12, TBp1_1 1, 
12 1   LB_RB+1_S2_BP1B_CH1    W+1/RB2out/2 Forward;  
12 2   LB_RB+1_S2_BP1B_CH2    W+1/RB1in/2 Forward;  
13 0   LB_RB+2_S2_BP2E_CH0    W+2/RB4/2+ Forward;  W+2/RB4/2- Forward;    TBp1_0 11, TBp2_0 13, TBp1_1 6, TBp2_1 4, 
13 1
13 2   LB_RB+2_S2_BP2E_CH2    W+2/RB3/2+ Forward;  W+2/RB3/2- Forward;  
14 0   LB_RB+2_S2_BP2D_CH0    W+2/RB4/2+ Backward;  W+2/RB4/2- Backward;    TBp1_0 12, TBp2_0 14, TBp1_1 5, 
14 1
14 2   LB_RB+2_S2_BP2D_CH2    W+2/RB3/2+ Backward;  W+2/RB3/2- Backward;  
15 0   LB_RB+2_S2_BP2C_CH0    W+2/RB2out/2 Central;    TBp2_0 15, TBp2_1 2, 
15 1   LB_RB+2_S2_BP2C_CH1    W+2/RB2out/2 Forward;  
15 2   LB_RB+2_S2_BP2C_CH2    W+2/RB2out/2 Backward;  
16 0   LB_RB+2_S2_BP2B_CH0    W+2/RB1out/2 Forward;    TBp2_0 16, TBp3_0 16, TBp2_1 1, 
16 1   LB_RB+2_S2_BP2B_CH1    W+2/RB2in/2 Forward;  
16 2   LB_RB+2_S2_BP2B_CH2    W+2/RB1in/2 Forward;  
17 0   LB_RB+2_S2_BP2A_CH0    W+2/RB1out/2 Backward;    TBp2_0 17, TBp2_1 0, 
17 1   LB_RB+2_S2_BP2A_CH1    W+2/RB2in/2 Backward;  
17 2   LB_RB+2_S2_BP2A_CH2    W+2/RB1in/2 Backward;  

TBp3_0
0 0
0 1
0 2
3 0   LB_RE+3_S1_EP32_CH0    RE+3/2/1;    TBp3_0 3, 
3 1   LB_RE+3_S1_EP32_CH1    RE+3/2/36;  
3 2   LB_RE+3_S1_EP32_CH2    RE+3/2/2;  
4 0   LB_RE+3_S1_EP33_CH0    RE+3/3/1;    TBp3_0 4, 
4 1   LB_RE+3_S1_EP33_CH1    RE+3/3/36;  
4 2   LB_RE+3_S1_EP33_CH2    RE+3/3/2;  
5 0   LB_RE+1_S1_EP22_CH0    RE+2/2/3;    TBp3_0 5, 
5 1   LB_RE+1_S1_EP22_CH1    RE+2/2/2;  
5 2   LB_RE+1_S1_EP22_CH2    RE+2/2/4;  
6 0   LB_RE+1_S1_EP23_CH0    RE+2/3/3;    TBp2_0 6, TBp3_0 6, 
6 1   LB_RE+1_S1_EP23_CH1    RE+2/3/2;  
6 2   LB_RE+1_S1_EP23_CH2    RE+2/3/4;  
7 0   LB_RE+4_S1_EP42_CH0    RE+4/2/1;    TBp3_0 7, 
7 1   LB_RE+4_S1_EP42_CH1    RE+4/2/36;  
7 2   LB_RE+4_S1_EP42_CH2    RE+4/2/2;  
8 0   LB_RE+4_S1_EP43_CH0    RE+4/3/1;    TBp3_0 8, TBp3_11 11, 
8 1   LB_RE+4_S1_EP43_CH1    RE+4/3/36;  
8 2   LB_RE+4_S1_EP43_CH2    RE+4/3/2;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE+4_S2_EP43_CH0    RE+4/3/4;    TBp3_0 11, TBp3_1 8, 
11 1   LB_RE+4_S2_EP43_CH1    RE+4/3/3;  
11 2   LB_RE+4_S2_EP43_CH2    RE+4/3/5;  
12 0   LB_RE+4_S2_EP42_CH0    RE+4/2/4;    TBp3_0 12, TBp3_1 7, 
12 1   LB_RE+4_S2_EP42_CH1    RE+4/2/3;  
12 2   LB_RE+4_S2_EP42_CH2    RE+4/2/5;  
13 0   LB_RE+3_S2_EP33_CH0    RE+3/3/4;    TBp3_0 13, TBp3_1 4, 
13 1   LB_RE+3_S2_EP33_CH1    RE+3/3/3;  
13 2   LB_RE+3_S2_EP33_CH2    RE+3/3/5;  
14 0   LB_RE+3_S2_EP32_CH0    RE+3/2/4;    TBp3_0 14, TBp3_1 3, 
14 1   LB_RE+3_S2_EP32_CH1    RE+3/2/3;  
14 2   LB_RE+3_S2_EP32_CH2    RE+3/2/5;  
15 0   LB_RE+1_S2_EP12_CH0    RE+1/2/4;    TBp3_0 15, TBp3_1 2, 
15 1   LB_RE+1_S2_EP12_CH1    RE+1/2/3;  
15 2   LB_RE+1_S2_EP12_CH2    RE+1/2/5;  
16 0   LB_RB+2_S2_BP2B_CH0    W+2/RB1out/2 Forward;    TBp2_0 16, TBp3_0 16, TBp2_1 1, 
16 1   LB_RB+2_S2_BP2B_CH1    W+2/RB2in/2 Forward;  
16 2   LB_RB+2_S2_BP2B_CH2    W+2/RB1in/2 Forward;  
17 0
17 1
17 2

TC_1
TBn3_1
0 0
0 1
0 2
1 0   LB_RB-2_S2_BN2B_CH0    W-2/RB1out/2 Forward;    TBn3_0 16, TBn2_0 16, TBn3_1 1, TBn2_1 1, 
1 1   LB_RB-2_S2_BN2B_CH1    W-2/RB2in/2 Forward;  
1 2   LB_RB-2_S2_BN2B_CH2    W-2/RB1in/2 Forward;  
2 0   LB_RE-1_S2_EN12_CH0    RE-1/2/4;    TBn3_0 15, TBn3_1 2, 
2 1   LB_RE-1_S2_EN12_CH1    RE-1/2/3;  
2 2   LB_RE-1_S2_EN12_CH2    RE-1/2/5;  
3 0   LB_RE-3_S2_EN32_CH0    RE-3/2/4;    TBn3_0 14, TBn3_1 3, 
3 1   LB_RE-3_S2_EN32_CH1    RE-3/2/3;  
3 2   LB_RE-3_S2_EN32_CH2    RE-3/2/5;  
4 0   LB_RE-3_S2_EN33_CH0    RE-3/3/4;    TBn3_0 13, TBn3_1 4, 
4 1   LB_RE-3_S2_EN33_CH1    RE-3/3/3;  
4 2   LB_RE-3_S2_EN33_CH2    RE-3/3/5;  
5 0   LB_RE-1_S2_EN22_CH0    RE-2/2/6;    TBn3_1 5, 
5 1   LB_RE-1_S2_EN22_CH1    RE-2/2/5;  
5 2   LB_RE-1_S2_EN22_CH2    RE-2/2/7;  
6 0   LB_RE-1_S2_EN23_CH0    RE-2/3/6;    TBn3_1 6, TBn2_1 6, 
6 1   LB_RE-1_S2_EN23_CH1    RE-2/3/5;  
6 2   LB_RE-1_S2_EN23_CH2    RE-2/3/7;  
7 0   LB_RE-4_S2_EN42_CH0    RE-4/2/4;    TBn3_0 12, TBn3_1 7, 
7 1   LB_RE-4_S2_EN42_CH1    RE-4/2/3;  
7 2   LB_RE-4_S2_EN42_CH2    RE-4/2/5;  
8 0   LB_RE-4_S2_EN43_CH0    RE-4/3/4;    TBn3_0 11, TBn3_1 8, 
8 1   LB_RE-4_S2_EN43_CH1    RE-4/3/3;  
8 2   LB_RE-4_S2_EN43_CH2    RE-4/3/5;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE-4_S3_EN43_CH0    RE-4/3/7;    TBn3_1 11, TBn3_2 8, 
11 1   LB_RE-4_S3_EN43_CH1    RE-4/3/6;  
11 2   LB_RE-4_S3_EN43_CH2    RE-4/3/8;  
12 0   LB_RE-4_S3_EN42_CH0    RE-4/2/7;    TBn3_1 12, 
12 1   LB_RE-4_S3_EN42_CH1    RE-4/2/6;  
12 2   LB_RE-4_S3_EN42_CH2    RE-4/2/8;  
13 0   LB_RE-3_S3_EN33_CH0    RE-3/3/7;    TBn3_1 13, 
13 1   LB_RE-3_S3_EN33_CH1    RE-3/3/6;  
13 2   LB_RE-3_S3_EN33_CH2    RE-3/3/8;  
14 0   LB_RE-3_S3_EN32_CH0    RE-3/2/7;    TBn3_1 14, 
14 1   LB_RE-3_S3_EN32_CH1    RE-3/2/6;  
14 2   LB_RE-3_S3_EN32_CH2    RE-3/2/8;  
15 0   LB_RE-1_S3_EN12_CH0    RE-1/2/7;    TBn3_1 15, 
15 1   LB_RE-1_S3_EN12_CH1    RE-1/2/6;  
15 2   LB_RE-1_S3_EN12_CH2    RE-1/2/8;  
16 0   LB_RB-2_S3_BN2B_CH0    W-2/RB1out/3 Forward;    TBn3_1 16, TBn2_1 16, 
16 1   LB_RB-2_S3_BN2B_CH1    W-2/RB2in/3 Forward;  
16 2   LB_RB-2_S3_BN2B_CH2    W-2/RB1in/3 Forward;  
17 0
17 1
17 2

TBn2_1
0 0   LB_RB-2_S2_BN2A_CH0    W-2/RB1out/2 Backward;    TBn2_0 17, TBn2_1 0, 
0 1   LB_RB-2_S2_BN2A_CH1    W-2/RB2in/2 Backward;  
0 2   LB_RB-2_S2_BN2A_CH2    W-2/RB1in/2 Backward;  
1 0   LB_RB-2_S2_BN2B_CH0    W-2/RB1out/2 Forward;    TBn3_0 16, TBn2_0 16, TBn3_1 1, TBn2_1 1, 
1 1   LB_RB-2_S2_BN2B_CH1    W-2/RB2in/2 Forward;  
1 2   LB_RB-2_S2_BN2B_CH2    W-2/RB1in/2 Forward;  
2 0   LB_RB-2_S2_BN2C_CH0    W-2/RB2out/2 Central;    TBn2_0 15, TBn2_1 2, 
2 1   LB_RB-2_S2_BN2C_CH1    W-2/RB2out/2 Forward;  
2 2   LB_RB-2_S2_BN2C_CH2    W-2/RB2out/2 Backward;  
3 0   LB_RB-2_S2_BN2D_CH0    W-2/RB4/2+ Backward;  W-2/RB4/2- Backward;    TBn2_0 14, TBn1_0 12, TBn2_1 3, 
3 1
3 2   LB_RB-2_S2_BN2D_CH2    W-2/RB3/2+ Backward;  W-2/RB3/2- Backward;  
4 0   LB_RB-2_S2_BN2E_CH0    W-2/RB4/2+ Forward;  W-2/RB4/2- Forward;    TBn2_0 13, TBn1_0 11, TBn2_1 4, TBn1_1 6, 
4 1
4 2   LB_RB-2_S2_BN2E_CH2    W-2/RB3/2+ Forward;  W-2/RB3/2- Forward;  
5 0   LB_RB-1_S2_BN1B_CH0    W-1/RB1out/2 Forward;    TBn2_0 12, TBn2_1 5, TBn1_0 16, 
5 1   LB_RB-1_S2_BN1B_CH1    W-1/RB2out/2 Forward;  
5 2   LB_RB-1_S2_BN1B_CH2    W-1/RB1in/2 Forward;  
6 0   LB_RE-1_S2_EN23_CH0    RE-2/3/6;    TBn3_1 6, TBn2_1 6, 
6 1   LB_RE-1_S2_EN23_CH1    RE-2/3/5;  
6 2   LB_RE-1_S2_EN23_CH2    RE-2/3/7;  
7 0   LB_RE-1_S2_EN13_CH0    RE-1/3/4;    TBn2_0 10, TBn2_1 7, 
7 1   LB_RE-1_S2_EN13_CH1    RE-1/3/3;  
7 2   LB_RE-1_S2_EN13_CH2    RE-1/3/5;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE-1_S3_EN13_CH0    RE-1/3/7;    TBn2_1 10, TBn2_2 7, 
10 1   LB_RE-1_S3_EN13_CH1    RE-1/3/6;  
10 2   LB_RE-1_S3_EN13_CH2    RE-1/3/8;  
11 0
11 1
11 2
12 0   LB_RB-1_S3_BN1B_CH0    W-1/RB1out/3 Forward;    TBn2_1 12, TBn2_2 5, TBn1_1 16, 
12 1   LB_RB-1_S3_BN1B_CH1    W-1/RB2out/3 Forward;  
12 2   LB_RB-1_S3_BN1B_CH2    W-1/RB1in/3 Forward;  
13 0   LB_RB-2_S3_BN2E_CH0    W-2/RB4/3+ Forward;  W-2/RB4/3- Forward;    TBn2_1 13, TBn1_1 11, 
13 1
13 2   LB_RB-2_S3_BN2E_CH2    W-2/RB3/3+ Forward;  W-2/RB3/3- Forward;  
14 0   LB_RB-2_S3_BN2D_CH0    W-2/RB4/3+ Backward;  W-2/RB4/3- Backward;    TBn2_1 14, TBn1_1 12, TBn2_2 3, 
14 1
14 2   LB_RB-2_S3_BN2D_CH2    W-2/RB3/3+ Backward;  W-2/RB3/3- Backward;  
15 0   LB_RB-2_S3_BN2C_CH0    W-2/RB2out/3 Central;    TBn2_1 15, 
15 1   LB_RB-2_S3_BN2C_CH1    W-2/RB2out/3 Forward;  
15 2   LB_RB-2_S3_BN2C_CH2    W-2/RB2out/3 Backward;  
16 0   LB_RB-2_S3_BN2B_CH0    W-2/RB1out/3 Forward;    TBn3_1 16, TBn2_1 16, 
16 1   LB_RB-2_S3_BN2B_CH1    W-2/RB2in/3 Forward;  
16 2   LB_RB-2_S3_BN2B_CH2    W-2/RB1in/3 Forward;  
17 0   LB_RB-2_S3_BN2A_CH0    W-2/RB1out/3 Backward;    TBn2_1 17, 
17 1   LB_RB-2_S3_BN2A_CH1    W-2/RB2in/3 Backward;  
17 2   LB_RB-2_S3_BN2A_CH2    W-2/RB1in/3 Backward;  

TBn1_1
3 0   LB_RB-1_S2_BN1D_CH0    W-1/RB4/2+ Backward;  W-1/RB4/2- Backward;    TBn1_0 14, TB0_0 12, TBn1_1 3, 
3 1
3 2   LB_RB-1_S2_BN1D_CH2    W-1/RB3/2+ Backward;  W-1/RB3/2- Backward;  
6 0   LB_RB-2_S2_BN2E_CH0    W-2/RB4/2+ Forward;  W-2/RB4/2- Forward;    TBn2_0 13, TBn1_0 11, TBn2_1 4, TBn1_1 6, 
6 1
6 2   LB_RB-2_S2_BN2E_CH2    W-2/RB3/2+ Forward;  W-2/RB3/2- Forward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S3_BP0B_CH0    W0/RB1out/3 Backward;    TB0_1 16, TB0_2 1, TBn1_1 10, 
10 1   LB_RB0_S3_BP0B_CH1    W0/RB2out/3 Backward;  
10 2   LB_RB0_S3_BP0B_CH2    W0/RB1in/3 Backward;  
11 0   LB_RB-2_S3_BN2E_CH0    W-2/RB4/3+ Forward;  W-2/RB4/3- Forward;    TBn2_1 13, TBn1_1 11, 
11 1
11 2   LB_RB-2_S3_BN2E_CH2    W-2/RB3/3+ Forward;  W-2/RB3/3- Forward;  
12 0   LB_RB-2_S3_BN2D_CH0    W-2/RB4/3+ Backward;  W-2/RB4/3- Backward;    TBn2_1 14, TBn1_1 12, TBn2_2 3, 
12 1
12 2   LB_RB-2_S3_BN2D_CH2    W-2/RB3/3+ Backward;  W-2/RB3/3- Backward;  
13 0   LB_RB-1_S3_BN1E_CH0    W-1/RB4/3+ Forward;  W-1/RB4/3- Forward;    TBn1_1 13, 
13 1
13 2   LB_RB-1_S3_BN1E_CH2    W-1/RB3/3+ Forward;  W-1/RB3/3- Forward;  
14 0   LB_RB-1_S3_BN1D_CH0    W-1/RB4/3+ Backward;  W-1/RB4/3- Backward;    TBn1_1 14, TB0_1 12, TBn1_2 3, 
14 1
14 2   LB_RB-1_S3_BN1D_CH2    W-1/RB3/3+ Backward;  W-1/RB3/3- Backward;  
15 0   LB_RB-1_S3_BN1C_CH0    W-1/RB2in/3 Central;    TBn1_1 15, 
15 1   LB_RB-1_S3_BN1C_CH1    W-1/RB2in/3 Forward;  
15 2   LB_RB-1_S3_BN1C_CH2    W-1/RB2in/3 Backward;  
16 0   LB_RB-1_S3_BN1B_CH0    W-1/RB1out/3 Forward;    TBn2_1 12, TBn2_2 5, TBn1_1 16, 
16 1   LB_RB-1_S3_BN1B_CH1    W-1/RB2out/3 Forward;  
16 2   LB_RB-1_S3_BN1B_CH2    W-1/RB1in/3 Forward;  
17 0   LB_RB-1_S3_BN1A_CH0    W-1/RB1out/3 Backward;    TBn1_1 17, 
17 1   LB_RB-1_S3_BN1A_CH1    W-1/RB2out/3 Backward;  
17 2   LB_RB-1_S3_BN1A_CH2    W-1/RB1in/3 Backward;  

TB0_1
0 0   LB_RB0_S2_BN0B_CH0    W0/RB1out/2 Forward;    TB0_0 17, TB0_1 0, TBp1_0 10, 
0 1   LB_RB0_S2_BN0B_CH1    W0/RB2out/2 Forward;  
0 2   LB_RB0_S2_BN0B_CH2    W0/RB1in/2 Forward;  
1 0   LB_RB0_S2_BP0B_CH0    W0/RB1out/2 Backward;    TB0_0 16, TB0_1 1, TBn1_0 10, 
1 1   LB_RB0_S2_BP0B_CH1    W0/RB2out/2 Backward;  
1 2   LB_RB0_S2_BP0B_CH2    W0/RB1in/2 Backward;  
6 0   LB_RB+1_S2_BP1D_CH0    W+1/RB4/2+ Backward;  W+1/RB4/2- Backward;    TB0_0 11, TBp1_0 14, TB0_1 6, 
6 1
6 2   LB_RB+1_S2_BP1D_CH2    W+1/RB3/2+ Backward;  W+1/RB3/2- Backward;  
7 0
7 1
7 2
8 0
8 1
8 2
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RB+1_S3_BP1D_CH0    W+1/RB4/3+ Backward;  W+1/RB4/3- Backward;    TB0_1 11, TBp1_1 14, TB0_2 6, 
11 1
11 2   LB_RB+1_S3_BP1D_CH2    W+1/RB3/3+ Backward;  W+1/RB3/3- Backward;  
12 0   LB_RB-1_S3_BN1D_CH0    W-1/RB4/3+ Backward;  W-1/RB4/3- Backward;    TBn1_1 14, TB0_1 12, TBn1_2 3, 
12 1
12 2   LB_RB-1_S3_BN1D_CH2    W-1/RB3/3+ Backward;  W-1/RB3/3- Backward;  
13 0   LB_RB0_S3_BP0E_CH0    W0/RB4/3+ Backward;  W0/RB4/3- Backward;    TB0_1 13, 
13 1
13 2   LB_RB0_S3_BP0E_CH2    W0/RB3/3+ Backward;  W0/RB3/3- Backward;  
14 0   LB_RB0_S3_BN0E_CH0    W0/RB4/3+ Forward;  W0/RB4/3- Forward;    TB0_1 14, 
14 1
14 2   LB_RB0_S3_BN0E_CH2    W0/RB3/3+ Forward;  W0/RB3/3- Forward;  
15 0   LB_RB0_S3_BM0C_CH0    W0/RB2in/3 Central;    TB0_1 15, 
15 1   LB_RB0_S3_BM0C_CH1    W0/RB2in/3 Forward;  
15 2   LB_RB0_S3_BM0C_CH2    W0/RB2in/3 Backward;  
16 0   LB_RB0_S3_BP0B_CH0    W0/RB1out/3 Backward;    TB0_1 16, TB0_2 1, TBn1_1 10, 
16 1   LB_RB0_S3_BP0B_CH1    W0/RB2out/3 Backward;  
16 2   LB_RB0_S3_BP0B_CH2    W0/RB1in/3 Backward;  
17 0   LB_RB0_S3_BN0B_CH0    W0/RB1out/3 Forward;    TB0_1 17, TB0_2 0, TBp1_1 10, 
17 1   LB_RB0_S3_BN0B_CH1    W0/RB2out/3 Forward;  
17 2   LB_RB0_S3_BN0B_CH2    W0/RB1in/3 Forward;  

TBp1_1
1 0   LB_RB+1_S2_BP1B_CH0    W+1/RB1out/2 Forward;    TBp1_0 16, TBp2_0 12, TBp1_1 1, 
1 1   LB_RB+1_S2_BP1B_CH1    W+1/RB2out/2 Forward;  
1 2   LB_RB+1_S2_BP1B_CH2    W+1/RB1in/2 Forward;  
5 0   LB_RB+2_S2_BP2D_CH0    W+2/RB4/2+ Backward;  W+2/RB4/2- Backward;    TBp1_0 12, TBp2_0 14, TBp1_1 5, 
5 1
5 2   LB_RB+2_S2_BP2D_CH2    W+2/RB3/2+ Backward;  W+2/RB3/2- Backward;  
6 0   LB_RB+2_S2_BP2E_CH0    W+2/RB4/2+ Forward;  W+2/RB4/2- Forward;    TBp1_0 11, TBp2_0 13, TBp1_1 6, TBp2_1 4, 
6 1
6 2   LB_RB+2_S2_BP2E_CH2    W+2/RB3/2+ Forward;  W+2/RB3/2- Forward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S3_BN0B_CH0    W0/RB1out/3 Forward;    TB0_1 17, TB0_2 0, TBp1_1 10, 
10 1   LB_RB0_S3_BN0B_CH1    W0/RB2out/3 Forward;  
10 2   LB_RB0_S3_BN0B_CH2    W0/RB1in/3 Forward;  
11 0   LB_RB+2_S3_BP2E_CH0    W+2/RB4/3+ Forward;  W+2/RB4/3- Forward;    TBp1_1 11, TBp2_1 13, 
11 1
11 2   LB_RB+2_S3_BP2E_CH2    W+2/RB3/3+ Forward;  W+2/RB3/3- Forward;  
12 0   LB_RB+2_S3_BP2D_CH0    W+2/RB4/3+ Backward;  W+2/RB4/3- Backward;    TBp1_1 12, TBp2_1 14, TBp1_2 5, 
12 1
12 2   LB_RB+2_S3_BP2D_CH2    W+2/RB3/3+ Backward;  W+2/RB3/3- Backward;  
14 0   LB_RB+1_S3_BP1D_CH0    W+1/RB4/3+ Backward;  W+1/RB4/3- Backward;    TB0_1 11, TBp1_1 14, TB0_2 6, 
14 1
14 2   LB_RB+1_S3_BP1D_CH2    W+1/RB3/3+ Backward;  W+1/RB3/3- Backward;  
16 0   LB_RB+1_S3_BP1B_CH0    W+1/RB1out/3 Forward;    TBp1_1 16, TBp2_1 12, TBp1_2 1, 
16 1   LB_RB+1_S3_BP1B_CH1    W+1/RB2out/3 Forward;  
16 2   LB_RB+1_S3_BP1B_CH2    W+1/RB1in/3 Forward;  

TBp2_1
0 0   LB_RB+2_S2_BP2A_CH0    W+2/RB1out/2 Backward;    TBp2_0 17, TBp2_1 0, 
0 1   LB_RB+2_S2_BP2A_CH1    W+2/RB2in/2 Backward;  
0 2   LB_RB+2_S2_BP2A_CH2    W+2/RB1in/2 Backward;  
1 0   LB_RB+2_S2_BP2B_CH0    W+2/RB1out/2 Forward;    TBp2_0 16, TBp3_0 16, TBp2_1 1, 
1 1   LB_RB+2_S2_BP2B_CH1    W+2/RB2in/2 Forward;  
1 2   LB_RB+2_S2_BP2B_CH2    W+2/RB1in/2 Forward;  
2 0   LB_RB+2_S2_BP2C_CH0    W+2/RB2out/2 Central;    TBp2_0 15, TBp2_1 2, 
2 1   LB_RB+2_S2_BP2C_CH1    W+2/RB2out/2 Forward;  
2 2   LB_RB+2_S2_BP2C_CH2    W+2/RB2out/2 Backward;  
4 0   LB_RB+2_S2_BP2E_CH0    W+2/RB4/2+ Forward;  W+2/RB4/2- Forward;    TBp1_0 11, TBp2_0 13, TBp1_1 6, TBp2_1 4, 
4 1
4 2   LB_RB+2_S2_BP2E_CH2    W+2/RB3/2+ Forward;  W+2/RB3/2- Forward;  
6 0   LB_RE+1_S2_EP23_CH0    RE+2/3/6;    TBp2_1 6, TBp3_1 6, 
6 1   LB_RE+1_S2_EP23_CH1    RE+2/3/5;  
6 2   LB_RE+1_S2_EP23_CH2    RE+2/3/7;  
7 0   LB_RE+1_S2_EP13_CH0    RE+1/3/4;    TBp2_0 10, TBp2_1 7, 
7 1   LB_RE+1_S2_EP13_CH1    RE+1/3/3;  
7 2   LB_RE+1_S2_EP13_CH2    RE+1/3/5;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE+1_S3_EP13_CH0    RE+1/3/7;    TBp2_1 10, TBp2_2 7, 
10 1   LB_RE+1_S3_EP13_CH1    RE+1/3/6;  
10 2   LB_RE+1_S3_EP13_CH2    RE+1/3/8;  
11 0
11 1
11 2
12 0   LB_RB+1_S3_BP1B_CH0    W+1/RB1out/3 Forward;    TBp1_1 16, TBp2_1 12, TBp1_2 1, 
12 1   LB_RB+1_S3_BP1B_CH1    W+1/RB2out/3 Forward;  
12 2   LB_RB+1_S3_BP1B_CH2    W+1/RB1in/3 Forward;  
13 0   LB_RB+2_S3_BP2E_CH0    W+2/RB4/3+ Forward;  W+2/RB4/3- Forward;    TBp1_1 11, TBp2_1 13, 
13 1
13 2   LB_RB+2_S3_BP2E_CH2    W+2/RB3/3+ Forward;  W+2/RB3/3- Forward;  
14 0   LB_RB+2_S3_BP2D_CH0    W+2/RB4/3+ Backward;  W+2/RB4/3- Backward;    TBp1_1 12, TBp2_1 14, TBp1_2 5, 
14 1
14 2   LB_RB+2_S3_BP2D_CH2    W+2/RB3/3+ Backward;  W+2/RB3/3- Backward;  
15 0   LB_RB+2_S3_BP2C_CH0    W+2/RB2out/3 Central;    TBp2_1 15, 
15 1   LB_RB+2_S3_BP2C_CH1    W+2/RB2out/3 Forward;  
15 2   LB_RB+2_S3_BP2C_CH2    W+2/RB2out/3 Backward;  
16 0   LB_RB+2_S3_BP2B_CH0    W+2/RB1out/3 Forward;    TBp2_1 16, TBp3_1 16, 
16 1   LB_RB+2_S3_BP2B_CH1    W+2/RB2in/3 Forward;  
16 2   LB_RB+2_S3_BP2B_CH2    W+2/RB1in/3 Forward;  
17 0   LB_RB+2_S3_BP2A_CH0    W+2/RB1out/3 Backward;    TBp2_1 17, 
17 1   LB_RB+2_S3_BP2A_CH1    W+2/RB2in/3 Backward;  
17 2   LB_RB+2_S3_BP2A_CH2    W+2/RB1in/3 Backward;  

TBp3_1
0 0   LB_RE+1_S2_EP11_CH0    RE+1/1/3;    TBp3_1 0, 
0 1
0 2   LB_RE+1_S2_EP11_CH2    RE+1/1/4;  
2 0   LB_RE+1_S2_EP12_CH0    RE+1/2/4;    TBp3_0 15, TBp3_1 2, 
2 1   LB_RE+1_S2_EP12_CH1    RE+1/2/3;  
2 2   LB_RE+1_S2_EP12_CH2    RE+1/2/5;  
3 0   LB_RE+3_S2_EP32_CH0    RE+3/2/4;    TBp3_0 14, TBp3_1 3, 
3 1   LB_RE+3_S2_EP32_CH1    RE+3/2/3;  
3 2   LB_RE+3_S2_EP32_CH2    RE+3/2/5;  
4 0   LB_RE+3_S2_EP33_CH0    RE+3/3/4;    TBp3_0 13, TBp3_1 4, 
4 1   LB_RE+3_S2_EP33_CH1    RE+3/3/3;  
4 2   LB_RE+3_S2_EP33_CH2    RE+3/3/5;  
5 0   LB_RE+1_S2_EP22_CH0    RE+2/2/6;    TBp3_1 5, 
5 1   LB_RE+1_S2_EP22_CH1    RE+2/2/5;  
5 2   LB_RE+1_S2_EP22_CH2    RE+2/2/7;  
6 0   LB_RE+1_S2_EP23_CH0    RE+2/3/6;    TBp2_1 6, TBp3_1 6, 
6 1   LB_RE+1_S2_EP23_CH1    RE+2/3/5;  
6 2   LB_RE+1_S2_EP23_CH2    RE+2/3/7;  
7 0   LB_RE+4_S2_EP42_CH0    RE+4/2/4;    TBp3_0 12, TBp3_1 7, 
7 1   LB_RE+4_S2_EP42_CH1    RE+4/2/3;  
7 2   LB_RE+4_S2_EP42_CH2    RE+4/2/5;  
8 0   LB_RE+4_S2_EP43_CH0    RE+4/3/4;    TBp3_0 11, TBp3_1 8, 
8 1   LB_RE+4_S2_EP43_CH1    RE+4/3/3;  
8 2   LB_RE+4_S2_EP43_CH2    RE+4/3/5;  
9 0   LB_RE+1_S1_EP11_CH0    RE+1/1/1;    TBp3_1 9, 
9 1
9 2   LB_RE+1_S1_EP11_CH2    RE+1/1/2;  
10 0
10 1
10 2
11 0   LB_RE+4_S3_EP43_CH0    RE+4/3/7;    TBp3_1 11, TBp3_2 8, 
11 1   LB_RE+4_S3_EP43_CH1    RE+4/3/6;  
11 2   LB_RE+4_S3_EP43_CH2    RE+4/3/8;  
12 0   LB_RE+4_S3_EP42_CH0    RE+4/2/7;    TBp3_1 12, 
12 1   LB_RE+4_S3_EP42_CH1    RE+4/2/6;  
12 2   LB_RE+4_S3_EP42_CH2    RE+4/2/8;  
13 0   LB_RE+3_S3_EP33_CH0    RE+3/3/7;    TBp3_1 13, 
13 1   LB_RE+3_S3_EP33_CH1    RE+3/3/6;  
13 2   LB_RE+3_S3_EP33_CH2    RE+3/3/8;  
14 0   LB_RE+3_S3_EP32_CH0    RE+3/2/7;    TBp3_1 14, 
14 1   LB_RE+3_S3_EP32_CH1    RE+3/2/6;  
14 2   LB_RE+3_S3_EP32_CH2    RE+3/2/8;  
15 0   LB_RE+1_S3_EP12_CH0    RE+1/2/7;    TBp3_1 15, 
15 1   LB_RE+1_S3_EP12_CH1    RE+1/2/6;  
15 2   LB_RE+1_S3_EP12_CH2    RE+1/2/8;  
16 0   LB_RB+2_S3_BP2B_CH0    W+2/RB1out/3 Forward;    TBp2_1 16, TBp3_1 16, 
16 1   LB_RB+2_S3_BP2B_CH1    W+2/RB2in/3 Forward;  
16 2   LB_RB+2_S3_BP2B_CH2    W+2/RB1in/3 Forward;  
17 0
17 1
17 2

TC_2
TBn3_2
0 0
0 1
0 2
5 0   LB_RE-1_S3_EN22_CH0    RE-2/2/9;    TBn3_2 5, 
5 1   LB_RE-1_S3_EN22_CH1    RE-2/2/8;  
5 2   LB_RE-1_S3_EN22_CH2    RE-2/2/10;  
6 0   LB_RE-1_S3_EN23_CH0    RE-2/3/9;    TBn3_2 6, TBn2_2 6, 
6 1   LB_RE-1_S3_EN23_CH1    RE-2/3/8;  
6 2   LB_RE-1_S3_EN23_CH2    RE-2/3/10;  
8 0   LB_RE-4_S3_EN43_CH0    RE-4/3/7;    TBn3_1 11, TBn3_2 8, 
8 1   LB_RE-4_S3_EN43_CH1    RE-4/3/6;  
8 2   LB_RE-4_S3_EN43_CH2    RE-4/3/8;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE-4_S4_EN43_CH0    RE-4/3/10;    TBn3_2 11, TBn3_3 8, 
11 1   LB_RE-4_S4_EN43_CH1    RE-4/3/9;  
11 2   LB_RE-4_S4_EN43_CH2    RE-4/3/11;  
12 0   LB_RE-4_S4_EN42_CH0    RE-4/2/10;    TBn3_2 12, TBn3_3 7, 
12 1   LB_RE-4_S4_EN42_CH1    RE-4/2/9;  
12 2   LB_RE-4_S4_EN42_CH2    RE-4/2/11;  
13 0   LB_RE-3_S4_EN33_CH0    RE-3/3/10;    TBn3_2 13, TBn3_3 4, 
13 1   LB_RE-3_S4_EN33_CH1    RE-3/3/9;  
13 2   LB_RE-3_S4_EN33_CH2    RE-3/3/11;  
14 0   LB_RE-3_S4_EN32_CH0    RE-3/2/10;    TBn3_2 14, TBn3_3 3, 
14 1   LB_RE-3_S4_EN32_CH1    RE-3/2/9;  
14 2   LB_RE-3_S4_EN32_CH2    RE-3/2/11;  
15 0   LB_RE-1_S4_EN12_CH0    RE-1/2/10;    TBn3_2 15, TBn3_3 2, 
15 1   LB_RE-1_S4_EN12_CH1    RE-1/2/9;  
15 2   LB_RE-1_S4_EN12_CH2    RE-1/2/11;  
16 0   LB_RB-2_S4_BN2B_CH0    W-2/RB1out/4 Forward;    TBn3_2 16, TBn2_2 16, TBn3_3 1, TBn2_3 1, 
16 1   LB_RB-2_S4_BN2B_CH1    W-2/RB2in/4 Forward;  
16 2   LB_RB-2_S4_BN2B_CH2    W-2/RB1in/4 Forward;  
17 0
17 1
17 2

TBn2_2
3 0   LB_RB-2_S3_BN2D_CH0    W-2/RB4/3+ Backward;  W-2/RB4/3- Backward;    TBn2_1 14, TBn1_1 12, TBn2_2 3, 
3 1
3 2   LB_RB-2_S3_BN2D_CH2    W-2/RB3/3+ Backward;  W-2/RB3/3- Backward;  
5 0   LB_RB-1_S3_BN1B_CH0    W-1/RB1out/3 Forward;    TBn2_1 12, TBn2_2 5, TBn1_1 16, 
5 1   LB_RB-1_S3_BN1B_CH1    W-1/RB2out/3 Forward;  
5 2   LB_RB-1_S3_BN1B_CH2    W-1/RB1in/3 Forward;  
6 0   LB_RE-1_S3_EN23_CH0    RE-2/3/9;    TBn3_2 6, TBn2_2 6, 
6 1   LB_RE-1_S3_EN23_CH1    RE-2/3/8;  
6 2   LB_RE-1_S3_EN23_CH2    RE-2/3/10;  
7 0   LB_RE-1_S3_EN13_CH0    RE-1/3/7;    TBn2_1 10, TBn2_2 7, 
7 1   LB_RE-1_S3_EN13_CH1    RE-1/3/6;  
7 2   LB_RE-1_S3_EN13_CH2    RE-1/3/8;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE-1_S4_EN13_CH0    RE-1/3/10;    TBn2_2 10, TBn2_3 7, 
10 1   LB_RE-1_S4_EN13_CH1    RE-1/3/9;  
10 2   LB_RE-1_S4_EN13_CH2    RE-1/3/11;  
11 0
11 1
11 2
12 0   LB_RB-1_S4_BN1B_CH0    W-1/RB1out/4 Forward;    TBn2_2 12, TBn2_3 5, TBn1_2 16, 
12 1   LB_RB-1_S4_BN1B_CH1    W-1/RB2out/4 Forward;  
12 2   LB_RB-1_S4_BN1B_CH2    W-1/RB1in/4 Forward;  
13 0   LB_RB-2_S4_BN2E_CH0    W-2/RB4/4- Forward;  W-2/RB4/4-- Forward;    TBn2_2 13, TBn1_2 11, TBn2_3 4, TBn1_3 6, 
13 1   LB_RB-2_S4_BN2E_CH1    W-2/RB4/4+ Forward;  W-2/RB4/4++ Forward;  
13 2   LB_RB-2_S4_BN2E_CH2    W-2/RB3/4+ Forward;  W-2/RB3/4- Forward;  
14 0   LB_RB-2_S4_BN2D_CH0    W-2/RB4/4- Backward;  W-2/RB4/4-- Backward;    TBn2_2 14, TBn1_2 12, TBn2_3 3, 
14 1   LB_RB-2_S4_BN2D_CH1    W-2/RB4/4+ Backward;  W-2/RB4/4++ Backward;  
14 2   LB_RB-2_S4_BN2D_CH2    W-2/RB3/4+ Backward;  W-2/RB3/4- Backward;  
15 0   LB_RB-2_S4_BN2C_CH0    W-2/RB2out/4 Central;    TBn2_2 15, TBn2_3 2, 
15 1   LB_RB-2_S4_BN2C_CH1    W-2/RB2out/4 Forward;  
15 2   LB_RB-2_S4_BN2C_CH2    W-2/RB2out/4 Backward;  
16 0   LB_RB-2_S4_BN2B_CH0    W-2/RB1out/4 Forward;    TBn3_2 16, TBn2_2 16, TBn3_3 1, TBn2_3 1, 
16 1   LB_RB-2_S4_BN2B_CH1    W-2/RB2in/4 Forward;  
16 2   LB_RB-2_S4_BN2B_CH2    W-2/RB1in/4 Forward;  
17 0   LB_RB-2_S4_BN2A_CH0    W-2/RB1out/4 Backward;    TBn2_2 17, TBn2_3 0, 
17 1   LB_RB-2_S4_BN2A_CH1    W-2/RB2in/4 Backward;  
17 2   LB_RB-2_S4_BN2A_CH2    W-2/RB1in/4 Backward;  

TBn1_2
3 0   LB_RB-1_S3_BN1D_CH0    W-1/RB4/3+ Backward;  W-1/RB4/3- Backward;    TBn1_1 14, TB0_1 12, TBn1_2 3, 
3 1
3 2   LB_RB-1_S3_BN1D_CH2    W-1/RB3/3+ Backward;  W-1/RB3/3- Backward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S4_BN0B_CH0    W0/RB1out/4 Forward;    TB0_2 17, TB0_3 0, TBn1_2 10, 
10 1   LB_RB0_S4_BN0B_CH1    W0/RB2out/4 Forward;  
10 2   LB_RB0_S4_BN0B_CH2    W0/RB1in/4 Forward;  
11 0   LB_RB-2_S4_BN2E_CH0    W-2/RB4/4- Forward;  W-2/RB4/4-- Forward;    TBn2_2 13, TBn1_2 11, TBn2_3 4, TBn1_3 6, 
11 1   LB_RB-2_S4_BN2E_CH1    W-2/RB4/4+ Forward;  W-2/RB4/4++ Forward;  
11 2   LB_RB-2_S4_BN2E_CH2    W-2/RB3/4+ Forward;  W-2/RB3/4- Forward;  
12 0   LB_RB-2_S4_BN2D_CH0    W-2/RB4/4- Backward;  W-2/RB4/4-- Backward;    TBn2_2 14, TBn1_2 12, TBn2_3 3, 
12 1   LB_RB-2_S4_BN2D_CH1    W-2/RB4/4+ Backward;  W-2/RB4/4++ Backward;  
12 2   LB_RB-2_S4_BN2D_CH2    W-2/RB3/4+ Backward;  W-2/RB3/4- Backward;  
13 0   LB_RB-1_S4_BN1E_CH0    W-1/RB4/4- Forward;  W-1/RB4/4-- Forward;    TBn1_2 13, 
13 1   LB_RB-1_S4_BN1E_CH1    W-1/RB4/4+ Forward;  W-1/RB4/4++ Forward;  
13 2   LB_RB-1_S4_BN1E_CH2    W-1/RB3/4+ Forward;  W-1/RB3/4- Forward;  
14 0   LB_RB-1_S4_BN1D_CH0    W-1/RB4/4- Backward;  W-1/RB4/4-- Backward;    TBn1_2 14, TB0_2 12, TBn1_3 3, 
14 1   LB_RB-1_S4_BN1D_CH1    W-1/RB4/4+ Backward;  W-1/RB4/4++ Backward;  
14 2   LB_RB-1_S4_BN1D_CH2    W-1/RB3/4+ Backward;  W-1/RB3/4- Backward;  
15 0   LB_RB-1_S4_BN1C_CH0    W-1/RB2in/4 Central;    TBn1_2 15, 
15 1   LB_RB-1_S4_BN1C_CH1    W-1/RB2in/4 Forward;  
15 2   LB_RB-1_S4_BN1C_CH2    W-1/RB2in/4 Backward;  
16 0   LB_RB-1_S4_BN1B_CH0    W-1/RB1out/4 Forward;    TBn2_2 12, TBn2_3 5, TBn1_2 16, 
16 1   LB_RB-1_S4_BN1B_CH1    W-1/RB2out/4 Forward;  
16 2   LB_RB-1_S4_BN1B_CH2    W-1/RB1in/4 Forward;  
17 0   LB_RB-1_S4_BN1A_CH0    W-1/RB1out/4 Backward;    TBn1_2 17, 
17 1   LB_RB-1_S4_BN1A_CH1    W-1/RB2out/4 Backward;  
17 2   LB_RB-1_S4_BN1A_CH2    W-1/RB1in/4 Backward;  

TB0_2
0 0   LB_RB0_S3_BN0B_CH0    W0/RB1out/3 Forward;    TB0_1 17, TB0_2 0, TBp1_1 10, 
0 1   LB_RB0_S3_BN0B_CH1    W0/RB2out/3 Forward;  
0 2   LB_RB0_S3_BN0B_CH2    W0/RB1in/3 Forward;  
1 0   LB_RB0_S3_BP0B_CH0    W0/RB1out/3 Backward;    TB0_1 16, TB0_2 1, TBn1_1 10, 
1 1   LB_RB0_S3_BP0B_CH1    W0/RB2out/3 Backward;  
1 2   LB_RB0_S3_BP0B_CH2    W0/RB1in/3 Backward;  
6 0   LB_RB+1_S3_BP1D_CH0    W+1/RB4/3+ Backward;  W+1/RB4/3- Backward;    TB0_1 11, TBp1_1 14, TB0_2 6, 
6 1
6 2   LB_RB+1_S3_BP1D_CH2    W+1/RB3/3+ Backward;  W+1/RB3/3- Backward;  
7 0
7 1
7 2
8 0
8 1
8 2
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RB+1_S4_BP1D_CH0    W+1/RB4/4+ Backward;  W+1/RB4/4++ Backward;    TB0_2 11, TBp1_2 14, TB0_3 6, 
11 1   LB_RB+1_S4_BP1D_CH1    W+1/RB4/4- Backward;  W+1/RB4/4-- Backward;  
11 2   LB_RB+1_S4_BP1D_CH2    W+1/RB3/4+ Backward;  W+1/RB3/4- Backward;  
12 0   LB_RB-1_S4_BN1D_CH0    W-1/RB4/4- Backward;  W-1/RB4/4-- Backward;    TBn1_2 14, TB0_2 12, TBn1_3 3, 
12 1   LB_RB-1_S4_BN1D_CH1    W-1/RB4/4+ Backward;  W-1/RB4/4++ Backward;  
12 2   LB_RB-1_S4_BN1D_CH2    W-1/RB3/4+ Backward;  W-1/RB3/4- Backward;  
13 0   LB_RB0_S4_BP0E_CH0    W0/RB4/4- Backward;  W0/RB4/4-- Backward;    TB0_2 13, 
13 1   LB_RB0_S4_BP0E_CH1    W0/RB4/4+ Backward;  W0/RB4/4++ Backward;  
13 2   LB_RB0_S4_BP0E_CH2    W0/RB3/4+ Backward;  W0/RB3/4- Backward;  
14 0   LB_RB0_S4_BN0E_CH0    W0/RB4/4- Forward;  W0/RB4/4-- Forward;    TB0_2 14, 
14 1   LB_RB0_S4_BN0E_CH1    W0/RB4/4+ Forward;  W0/RB4/4++ Forward;  
14 2   LB_RB0_S4_BN0E_CH2    W0/RB3/4+ Forward;  W0/RB3/4- Forward;  
15 0   LB_RB0_S4_BM0C_CH0    W0/RB2in/4 Central;    TB0_2 15, 
15 1   LB_RB0_S4_BM0C_CH1    W0/RB2in/4 Forward;  
15 2   LB_RB0_S4_BM0C_CH2    W0/RB2in/4 Backward;  
16 0   LB_RB0_S4_BP0B_CH0    W0/RB1out/4 Backward;    TB0_2 16, TB0_3 1, TBp1_2 10, 
16 1   LB_RB0_S4_BP0B_CH1    W0/RB2out/4 Backward;  
16 2   LB_RB0_S4_BP0B_CH2    W0/RB1in/4 Backward;  
17 0   LB_RB0_S4_BN0B_CH0    W0/RB1out/4 Forward;    TB0_2 17, TB0_3 0, TBn1_2 10, 
17 1   LB_RB0_S4_BN0B_CH1    W0/RB2out/4 Forward;  
17 2   LB_RB0_S4_BN0B_CH2    W0/RB1in/4 Forward;  

TBp1_2
0 0   LB_RB+1_S3_BP1A_CH0    W+1/RB1out/3 Backward;    TBp1_2 0, 
0 1   LB_RB+1_S3_BP1A_CH1    W+1/RB2out/3 Backward;  
0 2   LB_RB+1_S3_BP1A_CH2    W+1/RB1in/3 Backward;  
1 0   LB_RB+1_S3_BP1B_CH0    W+1/RB1out/3 Forward;    TBp1_1 16, TBp2_1 12, TBp1_2 1, 
1 1   LB_RB+1_S3_BP1B_CH1    W+1/RB2out/3 Forward;  
1 2   LB_RB+1_S3_BP1B_CH2    W+1/RB1in/3 Forward;  
2 0   LB_RB+1_S3_BP1C_CH0    W+1/RB2in/3 Central;    TBp1_2 2, 
2 1   LB_RB+1_S3_BP1C_CH1    W+1/RB2in/3 Forward;  
2 2   LB_RB+1_S3_BP1C_CH2    W+1/RB2in/3 Backward;  
4 0   LB_RB+1_S3_BP1E_CH0    W+1/RB4/3+ Forward;  W+1/RB4/3- Forward;    TBp1_2 4, 
4 1
4 2   LB_RB+1_S3_BP1E_CH2    W+1/RB3/3+ Forward;  W+1/RB3/3- Forward;  
5 0   LB_RB+2_S3_BP2D_CH0    W+2/RB4/3+ Backward;  W+2/RB4/3- Backward;    TBp1_1 12, TBp2_1 14, TBp1_2 5, 
5 1
5 2   LB_RB+2_S3_BP2D_CH2    W+2/RB3/3+ Backward;  W+2/RB3/3- Backward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S4_BP0B_CH0    W0/RB1out/4 Backward;    TB0_2 16, TB0_3 1, TBp1_2 10, 
10 1   LB_RB0_S4_BP0B_CH1    W0/RB2out/4 Backward;  
10 2   LB_RB0_S4_BP0B_CH2    W0/RB1in/4 Backward;  
11 0   LB_RB+2_S4_BP2E_CH0    W+2/RB4/4+ Forward;  W+2/RB4/4++ Forward;    TBp1_2 11, TBp2_2 13, TBp1_3 6, TBp2_3 4, 
11 1   LB_RB+2_S4_BP2E_CH1    W+2/RB4/4- Forward;  W+2/RB4/4-- Forward;  
11 2   LB_RB+2_S4_BP2E_CH2    W+2/RB3/4+ Forward;  W+2/RB3/4- Forward;  
12 0   LB_RB+2_S4_BP2D_CH0    W+2/RB4/4+ Backward;  W+2/RB4/4++ Backward;    TBp1_2 12, TBp2_2 14, TBp1_3 5, 
12 1   LB_RB+2_S4_BP2D_CH1    W+2/RB4/4- Backward;  W+2/RB4/4-- Backward;  
12 2   LB_RB+2_S4_BP2D_CH2    W+2/RB3/4+ Backward;  W+2/RB3/4- Backward;  
13 0   LB_RB+1_S4_BP1E_CH0    W+1/RB4/4+ Forward;  W+1/RB4/4++ Forward;    TBp1_2 13, 
13 1   LB_RB+1_S4_BP1E_CH1    W+1/RB4/4- Forward;  W+1/RB4/4-- Forward;  
13 2   LB_RB+1_S4_BP1E_CH2    W+1/RB3/4+ Forward;  W+1/RB3/4- Forward;  
14 0   LB_RB+1_S4_BP1D_CH0    W+1/RB4/4+ Backward;  W+1/RB4/4++ Backward;    TB0_2 11, TBp1_2 14, TB0_3 6, 
14 1   LB_RB+1_S4_BP1D_CH1    W+1/RB4/4- Backward;  W+1/RB4/4-- Backward;  
14 2   LB_RB+1_S4_BP1D_CH2    W+1/RB3/4+ Backward;  W+1/RB3/4- Backward;  
15 0   LB_RB+1_S4_BP1C_CH0    W+1/RB2in/4 Central;    TBp1_2 15, 
15 1   LB_RB+1_S4_BP1C_CH1    W+1/RB2in/4 Forward;  
15 2   LB_RB+1_S4_BP1C_CH2    W+1/RB2in/4 Backward;  
16 0   LB_RB+1_S4_BP1B_CH0    W+1/RB1out/4 Forward;    TBp1_2 16, TBp2_2 12, TBp1_3 1, 
16 1   LB_RB+1_S4_BP1B_CH1    W+1/RB2out/4 Forward;  
16 2   LB_RB+1_S4_BP1B_CH2    W+1/RB1in/4 Forward;  
17 0   LB_RB+1_S4_BP1A_CH0    W+1/RB1out/4 Backward;    TBp1_2 17, 
17 1   LB_RB+1_S4_BP1A_CH1    W+1/RB2out/4 Backward;  
17 2   LB_RB+1_S4_BP1A_CH2    W+1/RB1in/4 Backward;  

TBp2_2
6 0   LB_RE+1_S3_EP23_CH0    RE+2/3/9;    TBp2_2 6, TBp3_2 6, 
6 1   LB_RE+1_S3_EP23_CH1    RE+2/3/8;  
6 2   LB_RE+1_S3_EP23_CH2    RE+2/3/10;  
7 0   LB_RE+1_S3_EP13_CH0    RE+1/3/7;    TBp2_1 10, TBp2_2 7, 
7 1   LB_RE+1_S3_EP13_CH1    RE+1/3/6;  
7 2   LB_RE+1_S3_EP13_CH2    RE+1/3/8;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE+1_S4_EP13_CH0    RE+1/3/10;    TBp2_2 10, TBp2_3 7, 
10 1   LB_RE+1_S4_EP13_CH1    RE+1/3/9;  
10 2   LB_RE+1_S4_EP13_CH2    RE+1/3/11;  
11 0
11 1
11 2
12 0   LB_RB+1_S4_BP1B_CH0    W+1/RB1out/4 Forward;    TBp1_2 16, TBp2_2 12, TBp1_3 1, 
12 1   LB_RB+1_S4_BP1B_CH1    W+1/RB2out/4 Forward;  
12 2   LB_RB+1_S4_BP1B_CH2    W+1/RB1in/4 Forward;  
13 0   LB_RB+2_S4_BP2E_CH0    W+2/RB4/4+ Forward;  W+2/RB4/4++ Forward;    TBp1_2 11, TBp2_2 13, TBp1_3 6, TBp2_3 4, 
13 1   LB_RB+2_S4_BP2E_CH1    W+2/RB4/4- Forward;  W+2/RB4/4-- Forward;  
13 2   LB_RB+2_S4_BP2E_CH2    W+2/RB3/4+ Forward;  W+2/RB3/4- Forward;  
14 0   LB_RB+2_S4_BP2D_CH0    W+2/RB4/4+ Backward;  W+2/RB4/4++ Backward;    TBp1_2 12, TBp2_2 14, TBp1_3 5, 
14 1   LB_RB+2_S4_BP2D_CH1    W+2/RB4/4- Backward;  W+2/RB4/4-- Backward;  
14 2   LB_RB+2_S4_BP2D_CH2    W+2/RB3/4+ Backward;  W+2/RB3/4- Backward;  
15 0   LB_RB+2_S4_BP2C_CH0    W+2/RB2out/4 Central;    TBp2_2 15, TBp2_3 2, 
15 1   LB_RB+2_S4_BP2C_CH1    W+2/RB2out/4 Forward;  
15 2   LB_RB+2_S4_BP2C_CH2    W+2/RB2out/4 Backward;  
16 0   LB_RB+2_S4_BP2B_CH0    W+2/RB1out/4 Forward;    TBp2_2 16, TBp3_2 16, TBp2_3 1, TBp3_3 1, 
16 1   LB_RB+2_S4_BP2B_CH1    W+2/RB2in/4 Forward;  
16 2   LB_RB+2_S4_BP2B_CH2    W+2/RB1in/4 Forward;  
17 0   LB_RB+2_S4_BP2A_CH0    W+2/RB1out/4 Backward;    TBp2_2 17, TBp2_3 0, 
17 1   LB_RB+2_S4_BP2A_CH1    W+2/RB2in/4 Backward;  
17 2   LB_RB+2_S4_BP2A_CH2    W+2/RB1in/4 Backward;  

TBp3_2
0 0
0 1
0 2
5 0   LB_RE+1_S3_EP22_CH0    RE+2/2/9;    TBp3_2 5, 
5 1   LB_RE+1_S3_EP22_CH1    RE+2/2/8;  
5 2   LB_RE+1_S3_EP22_CH2    RE+2/2/10;  
6 0   LB_RE+1_S3_EP23_CH0    RE+2/3/9;    TBp2_2 6, TBp3_2 6, 
6 1   LB_RE+1_S3_EP23_CH1    RE+2/3/8;  
6 2   LB_RE+1_S3_EP23_CH2    RE+2/3/10;  
8 0   LB_RE+4_S3_EP43_CH0    RE+4/3/7;    TBp3_1 11, TBp3_2 8, 
8 1   LB_RE+4_S3_EP43_CH1    RE+4/3/6;  
8 2   LB_RE+4_S3_EP43_CH2    RE+4/3/8;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE+4_S4_EP43_CH0    RE+4/3/10;    TBp3_2 11, TBp3_3 8, 
11 1   LB_RE+4_S4_EP43_CH1    RE+4/3/9;  
11 2   LB_RE+4_S4_EP43_CH2    RE+4/3/11;  
12 0   LB_RE+4_S4_EP42_CH0    RE+4/2/10;    TBp3_2 12, TBp3_3 7, 
12 1   LB_RE+4_S4_EP42_CH1    RE+4/2/9;  
12 2   LB_RE+4_S4_EP42_CH2    RE+4/2/11;  
13 0   LB_RE+3_S4_EP33_CH0    RE+3/3/10;    TBp3_2 13, TBp3_3 4, 
13 1   LB_RE+3_S4_EP33_CH1    RE+3/3/9;  
13 2   LB_RE+3_S4_EP33_CH2    RE+3/3/11;  
14 0   LB_RE+3_S4_EP32_CH0    RE+3/2/10;    TBp3_2 14, TBp3_3 3, 
14 1   LB_RE+3_S4_EP32_CH1    RE+3/2/9;  
14 2   LB_RE+3_S4_EP32_CH2    RE+3/2/11;  
15 0   LB_RE+1_S4_EP12_CH0    RE+1/2/10;    TBp3_2 15, TBp3_3 2, 
15 1   LB_RE+1_S4_EP12_CH1    RE+1/2/9;  
15 2   LB_RE+1_S4_EP12_CH2    RE+1/2/11;  
16 0   LB_RB+2_S4_BP2B_CH0    W+2/RB1out/4 Forward;    TBp2_2 16, TBp3_2 16, TBp2_3 1, TBp3_3 1, 
16 1   LB_RB+2_S4_BP2B_CH1    W+2/RB2in/4 Forward;  
16 2   LB_RB+2_S4_BP2B_CH2    W+2/RB1in/4 Forward;  
17 0
17 1
17 2

TC_3
TBn3_3
0 0
0 1
0 2
1 0   LB_RB-2_S4_BN2B_CH0    W-2/RB1out/4 Forward;    TBn3_2 16, TBn2_2 16, TBn3_3 1, TBn2_3 1, 
1 1   LB_RB-2_S4_BN2B_CH1    W-2/RB2in/4 Forward;  
1 2   LB_RB-2_S4_BN2B_CH2    W-2/RB1in/4 Forward;  
2 0   LB_RE-1_S4_EN12_CH0    RE-1/2/10;    TBn3_2 15, TBn3_3 2, 
2 1   LB_RE-1_S4_EN12_CH1    RE-1/2/9;  
2 2   LB_RE-1_S4_EN12_CH2    RE-1/2/11;  
3 0   LB_RE-3_S4_EN32_CH0    RE-3/2/10;    TBn3_2 14, TBn3_3 3, 
3 1   LB_RE-3_S4_EN32_CH1    RE-3/2/9;  
3 2   LB_RE-3_S4_EN32_CH2    RE-3/2/11;  
4 0   LB_RE-3_S4_EN33_CH0    RE-3/3/10;    TBn3_2 13, TBn3_3 4, 
4 1   LB_RE-3_S4_EN33_CH1    RE-3/3/9;  
4 2   LB_RE-3_S4_EN33_CH2    RE-3/3/11;  
5 0   LB_RE-1_S4_EN22_CH0    RE-2/2/12;    TBn3_3 5, 
5 1   LB_RE-1_S4_EN22_CH1    RE-2/2/11;  
5 2   LB_RE-1_S4_EN22_CH2    RE-2/2/13;  
6 0   LB_RE-1_S4_EN23_CH0    RE-2/3/12;    TBn3_3 6, TBn2_3 6, 
6 1   LB_RE-1_S4_EN23_CH1    RE-2/3/11;  
6 2   LB_RE-1_S4_EN23_CH2    RE-2/3/13;  
7 0   LB_RE-4_S4_EN42_CH0    RE-4/2/10;    TBn3_2 12, TBn3_3 7, 
7 1   LB_RE-4_S4_EN42_CH1    RE-4/2/9;  
7 2   LB_RE-4_S4_EN42_CH2    RE-4/2/11;  
8 0   LB_RE-4_S4_EN43_CH0    RE-4/3/10;    TBn3_2 11, TBn3_3 8, 
8 1   LB_RE-4_S4_EN43_CH1    RE-4/3/9;  
8 2   LB_RE-4_S4_EN43_CH2    RE-4/3/11;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE-4_S5_EN43_CH0    RE-4/3/13;    TBn3_3 11, TBn3_4 8, 
11 1   LB_RE-4_S5_EN43_CH1    RE-4/3/12;  
11 2   LB_RE-4_S5_EN43_CH2    RE-4/3/14;  
12 0   LB_RE-4_S5_EN42_CH0    RE-4/2/13;    TBn3_3 12, 
12 1   LB_RE-4_S5_EN42_CH1    RE-4/2/12;  
12 2   LB_RE-4_S5_EN42_CH2    RE-4/2/14;  
13 0   LB_RE-3_S5_EN33_CH0    RE-3/3/13;    TBn3_3 13, 
13 1   LB_RE-3_S5_EN33_CH1    RE-3/3/12;  
13 2   LB_RE-3_S5_EN33_CH2    RE-3/3/14;  
14 0   LB_RE-3_S5_EN32_CH0    RE-3/2/13;    TBn3_3 14, 
14 1   LB_RE-3_S5_EN32_CH1    RE-3/2/12;  
14 2   LB_RE-3_S5_EN32_CH2    RE-3/2/14;  
15 0   LB_RE-1_S5_EN12_CH0    RE-1/2/13;    TBn3_3 15, 
15 1   LB_RE-1_S5_EN12_CH1    RE-1/2/12;  
15 2   LB_RE-1_S5_EN12_CH2    RE-1/2/14;  
16 0   LB_RB-2_S5_BN2B_CH0    W-2/RB1out/5 Forward;    TBn3_3 16, TBn2_3 16, 
16 1   LB_RB-2_S5_BN2B_CH1    W-2/RB2in/5 Forward;  
16 2   LB_RB-2_S5_BN2B_CH2    W-2/RB1in/5 Forward;  
17 0
17 1
17 2

TBn2_3
0 0   LB_RB-2_S4_BN2A_CH0    W-2/RB1out/4 Backward;    TBn2_2 17, TBn2_3 0, 
0 1   LB_RB-2_S4_BN2A_CH1    W-2/RB2in/4 Backward;  
0 2   LB_RB-2_S4_BN2A_CH2    W-2/RB1in/4 Backward;  
1 0   LB_RB-2_S4_BN2B_CH0    W-2/RB1out/4 Forward;    TBn3_2 16, TBn2_2 16, TBn3_3 1, TBn2_3 1, 
1 1   LB_RB-2_S4_BN2B_CH1    W-2/RB2in/4 Forward;  
1 2   LB_RB-2_S4_BN2B_CH2    W-2/RB1in/4 Forward;  
2 0   LB_RB-2_S4_BN2C_CH0    W-2/RB2out/4 Central;    TBn2_2 15, TBn2_3 2, 
2 1   LB_RB-2_S4_BN2C_CH1    W-2/RB2out/4 Forward;  
2 2   LB_RB-2_S4_BN2C_CH2    W-2/RB2out/4 Backward;  
3 0   LB_RB-2_S4_BN2D_CH0    W-2/RB4/4- Backward;  W-2/RB4/4-- Backward;    TBn2_2 14, TBn1_2 12, TBn2_3 3, 
3 1   LB_RB-2_S4_BN2D_CH1    W-2/RB4/4+ Backward;  W-2/RB4/4++ Backward;  
3 2   LB_RB-2_S4_BN2D_CH2    W-2/RB3/4+ Backward;  W-2/RB3/4- Backward;  
4 0   LB_RB-2_S4_BN2E_CH0    W-2/RB4/4- Forward;  W-2/RB4/4-- Forward;    TBn2_2 13, TBn1_2 11, TBn2_3 4, TBn1_3 6, 
4 1   LB_RB-2_S4_BN2E_CH1    W-2/RB4/4+ Forward;  W-2/RB4/4++ Forward;  
4 2   LB_RB-2_S4_BN2E_CH2    W-2/RB3/4+ Forward;  W-2/RB3/4- Forward;  
5 0   LB_RB-1_S4_BN1B_CH0    W-1/RB1out/4 Forward;    TBn2_2 12, TBn2_3 5, TBn1_2 16, 
5 1   LB_RB-1_S4_BN1B_CH1    W-1/RB2out/4 Forward;  
5 2   LB_RB-1_S4_BN1B_CH2    W-1/RB1in/4 Forward;  
6 0   LB_RE-1_S4_EN23_CH0    RE-2/3/12;    TBn3_3 6, TBn2_3 6, 
6 1   LB_RE-1_S4_EN23_CH1    RE-2/3/11;  
6 2   LB_RE-1_S4_EN23_CH2    RE-2/3/13;  
7 0   LB_RE-1_S4_EN13_CH0    RE-1/3/10;    TBn2_2 10, TBn2_3 7, 
7 1   LB_RE-1_S4_EN13_CH1    RE-1/3/9;  
7 2   LB_RE-1_S4_EN13_CH2    RE-1/3/11;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE-1_S5_EN13_CH0    RE-1/3/13;    TBn2_3 10, TBn2_4 7, 
10 1   LB_RE-1_S5_EN13_CH1    RE-1/3/12;  
10 2   LB_RE-1_S5_EN13_CH2    RE-1/3/14;  
11 0
11 1
11 2
12 0   LB_RB-1_S5_BN1B_CH0    W-1/RB1out/5 Forward;    TBn2_3 12, TBn2_4 5, TBn1_3 16, 
12 1   LB_RB-1_S5_BN1B_CH1    W-1/RB2out/5 Forward;  
12 2   LB_RB-1_S5_BN1B_CH2    W-1/RB1in/5 Forward;  
13 0   LB_RB-2_S5_BN2E_CH0    W-2/RB4/5+ Forward;  W-2/RB4/5- Forward;    TBn2_3 13, TBn1_3 11, 
13 1
13 2   LB_RB-2_S5_BN2E_CH2    W-2/RB3/5+ Forward;  W-2/RB3/5- Forward;  
14 0   LB_RB-2_S5_BN2D_CH0    W-2/RB4/5+ Backward;  W-2/RB4/5- Backward;    TBn2_3 14, TBn1_3 12, TBn2_4 3, 
14 1
14 2   LB_RB-2_S5_BN2D_CH2    W-2/RB3/5+ Backward;  W-2/RB3/5- Backward;  
15 0   LB_RB-2_S5_BN2C_CH0    W-2/RB2out/5 Central;    TBn2_3 15, 
15 1   LB_RB-2_S5_BN2C_CH1    W-2/RB2out/5 Forward;  
15 2   LB_RB-2_S5_BN2C_CH2    W-2/RB2out/5 Backward;  
16 0   LB_RB-2_S5_BN2B_CH0    W-2/RB1out/5 Forward;    TBn3_3 16, TBn2_3 16, 
16 1   LB_RB-2_S5_BN2B_CH1    W-2/RB2in/5 Forward;  
16 2   LB_RB-2_S5_BN2B_CH2    W-2/RB1in/5 Forward;  
17 0   LB_RB-2_S5_BN2A_CH0    W-2/RB1out/5 Backward;    TBn2_3 17, 
17 1   LB_RB-2_S5_BN2A_CH1    W-2/RB2in/5 Backward;  
17 2   LB_RB-2_S5_BN2A_CH2    W-2/RB1in/5 Backward;  

TBn1_3
3 0   LB_RB-1_S4_BN1D_CH0    W-1/RB4/4- Backward;  W-1/RB4/4-- Backward;    TBn1_2 14, TB0_2 12, TBn1_3 3, 
3 1   LB_RB-1_S4_BN1D_CH1    W-1/RB4/4+ Backward;  W-1/RB4/4++ Backward;  
3 2   LB_RB-1_S4_BN1D_CH2    W-1/RB3/4+ Backward;  W-1/RB3/4- Backward;  
6 0   LB_RB-2_S4_BN2E_CH0    W-2/RB4/4- Forward;  W-2/RB4/4-- Forward;    TBn2_2 13, TBn1_2 11, TBn2_3 4, TBn1_3 6, 
6 1   LB_RB-2_S4_BN2E_CH1    W-2/RB4/4+ Forward;  W-2/RB4/4++ Forward;  
6 2   LB_RB-2_S4_BN2E_CH2    W-2/RB3/4+ Forward;  W-2/RB3/4- Forward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S5_BN0B_CH0    W0/RB1out/5 Forward;    TB0_3 17, TB0_4 0, TBn1_3 10, 
10 1   LB_RB0_S5_BN0B_CH1    W0/RB2out/5 Forward;  
10 2   LB_RB0_S5_BN0B_CH2    W0/RB1in/5 Forward;  
11 0   LB_RB-2_S5_BN2E_CH0    W-2/RB4/5+ Forward;  W-2/RB4/5- Forward;    TBn2_3 13, TBn1_3 11, 
11 1
11 2   LB_RB-2_S5_BN2E_CH2    W-2/RB3/5+ Forward;  W-2/RB3/5- Forward;  
12 0   LB_RB-2_S5_BN2D_CH0    W-2/RB4/5+ Backward;  W-2/RB4/5- Backward;    TBn2_3 14, TBn1_3 12, TBn2_4 3, 
12 1
12 2   LB_RB-2_S5_BN2D_CH2    W-2/RB3/5+ Backward;  W-2/RB3/5- Backward;  
13 0   LB_RB-1_S5_BN1E_CH0    W-1/RB4/5+ Forward;  W-1/RB4/5- Forward;    TBn1_3 13, 
13 1
13 2   LB_RB-1_S5_BN1E_CH2    W-1/RB3/5+ Forward;  W-1/RB3/5- Forward;  
14 0   LB_RB-1_S5_BN1D_CH0    W-1/RB4/5+ Backward;  W-1/RB4/5- Backward;    TBn1_3 14, TB0_3 12, TBn1_4 3, 
14 1
14 2   LB_RB-1_S5_BN1D_CH2    W-1/RB3/5+ Backward;  W-1/RB3/5- Backward;  
15 0   LB_RB-1_S5_BN1C_CH0    W-1/RB2in/5 Central;    TBn1_3 15, 
15 1   LB_RB-1_S5_BN1C_CH1    W-1/RB2in/5 Forward;  
15 2   LB_RB-1_S5_BN1C_CH2    W-1/RB2in/5 Backward;  
16 0   LB_RB-1_S5_BN1B_CH0    W-1/RB1out/5 Forward;    TBn2_3 12, TBn2_4 5, TBn1_3 16, 
16 1   LB_RB-1_S5_BN1B_CH1    W-1/RB2out/5 Forward;  
16 2   LB_RB-1_S5_BN1B_CH2    W-1/RB1in/5 Forward;  
17 0   LB_RB-1_S5_BN1A_CH0    W-1/RB1out/5 Backward;    TBn1_3 17, 
17 1   LB_RB-1_S5_BN1A_CH1    W-1/RB2out/5 Backward;  
17 2   LB_RB-1_S5_BN1A_CH2    W-1/RB1in/5 Backward;  

TB0_3
0 0   LB_RB0_S4_BN0B_CH0    W0/RB1out/4 Forward;    TB0_2 17, TB0_3 0, TBn1_2 10, 
0 1   LB_RB0_S4_BN0B_CH1    W0/RB2out/4 Forward;  
0 2   LB_RB0_S4_BN0B_CH2    W0/RB1in/4 Forward;  
1 0   LB_RB0_S4_BP0B_CH0    W0/RB1out/4 Backward;    TB0_2 16, TB0_3 1, TBp1_2 10, 
1 1   LB_RB0_S4_BP0B_CH1    W0/RB2out/4 Backward;  
1 2   LB_RB0_S4_BP0B_CH2    W0/RB1in/4 Backward;  
6 0   LB_RB+1_S4_BP1D_CH0    W+1/RB4/4+ Backward;  W+1/RB4/4++ Backward;    TB0_2 11, TBp1_2 14, TB0_3 6, 
6 1   LB_RB+1_S4_BP1D_CH1    W+1/RB4/4- Backward;  W+1/RB4/4-- Backward;  
6 2   LB_RB+1_S4_BP1D_CH2    W+1/RB3/4+ Backward;  W+1/RB3/4- Backward;  
7 0
7 1
7 2
8 0
8 1
8 2
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RB+1_S5_BP1D_CH0    W+1/RB4/5+ Backward;  W+1/RB4/5- Backward;    TB0_3 11, TBp1_3 14, TB0_4 6, 
11 1
11 2   LB_RB+1_S5_BP1D_CH2    W+1/RB3/5+ Backward;  W+1/RB3/5- Backward;  
12 0   LB_RB-1_S5_BN1D_CH0    W-1/RB4/5+ Backward;  W-1/RB4/5- Backward;    TBn1_3 14, TB0_3 12, TBn1_4 3, 
12 1
12 2   LB_RB-1_S5_BN1D_CH2    W-1/RB3/5+ Backward;  W-1/RB3/5- Backward;  
13 0   LB_RB0_S5_BP0E_CH0    W0/RB4/5+ Backward;  W0/RB4/5- Backward;    TB0_3 13, 
13 1
13 2   LB_RB0_S5_BP0E_CH2    W0/RB3/5+ Backward;  W0/RB3/5- Backward;  
14 0   LB_RB0_S5_BN0E_CH0    W0/RB4/5+ Forward;  W0/RB4/5- Forward;    TB0_3 14, 
14 1
14 2   LB_RB0_S5_BN0E_CH2    W0/RB3/5+ Forward;  W0/RB3/5- Forward;  
15 0   LB_RB0_S5_BM0C_CH0    W0/RB2in/5 Central;    TB0_3 15, 
15 1   LB_RB0_S5_BM0C_CH1    W0/RB2in/5 Forward;  
15 2   LB_RB0_S5_BM0C_CH2    W0/RB2in/5 Backward;  
16 0   LB_RB0_S5_BP0B_CH0    W0/RB1out/5 Backward;    TB0_3 16, TB0_4 1, TBp1_3 10, 
16 1   LB_RB0_S5_BP0B_CH1    W0/RB2out/5 Backward;  
16 2   LB_RB0_S5_BP0B_CH2    W0/RB1in/5 Backward;  
17 0   LB_RB0_S5_BN0B_CH0    W0/RB1out/5 Forward;    TB0_3 17, TB0_4 0, TBn1_3 10, 
17 1   LB_RB0_S5_BN0B_CH1    W0/RB2out/5 Forward;  
17 2   LB_RB0_S5_BN0B_CH2    W0/RB1in/5 Forward;  

TBp1_3
1 0   LB_RB+1_S4_BP1B_CH0    W+1/RB1out/4 Forward;    TBp1_2 16, TBp2_2 12, TBp1_3 1, 
1 1   LB_RB+1_S4_BP1B_CH1    W+1/RB2out/4 Forward;  
1 2   LB_RB+1_S4_BP1B_CH2    W+1/RB1in/4 Forward;  
5 0   LB_RB+2_S4_BP2D_CH0    W+2/RB4/4+ Backward;  W+2/RB4/4++ Backward;    TBp1_2 12, TBp2_2 14, TBp1_3 5, 
5 1   LB_RB+2_S4_BP2D_CH1    W+2/RB4/4- Backward;  W+2/RB4/4-- Backward;  
5 2   LB_RB+2_S4_BP2D_CH2    W+2/RB3/4+ Backward;  W+2/RB3/4- Backward;  
6 0   LB_RB+2_S4_BP2E_CH0    W+2/RB4/4+ Forward;  W+2/RB4/4++ Forward;    TBp1_2 11, TBp2_2 13, TBp1_3 6, TBp2_3 4, 
6 1   LB_RB+2_S4_BP2E_CH1    W+2/RB4/4- Forward;  W+2/RB4/4-- Forward;  
6 2   LB_RB+2_S4_BP2E_CH2    W+2/RB3/4+ Forward;  W+2/RB3/4- Forward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S5_BP0B_CH0    W0/RB1out/5 Backward;    TB0_3 16, TB0_4 1, TBp1_3 10, 
10 1   LB_RB0_S5_BP0B_CH1    W0/RB2out/5 Backward;  
10 2   LB_RB0_S5_BP0B_CH2    W0/RB1in/5 Backward;  
11 0   LB_RB+2_S5_BP2E_CH0    W+2/RB4/5+ Forward;  W+2/RB4/5- Forward;    TBp1_3 11, TBp2_3 13, 
11 1
11 2   LB_RB+2_S5_BP2E_CH2    W+2/RB3/5+ Forward;  W+2/RB3/5- Forward;  
12 0   LB_RB+2_S5_BP2D_CH0    W+2/RB4/5+ Backward;  W+2/RB4/5- Backward;    TBp1_3 12, TBp2_3 14, TBp1_4 5, 
12 1
12 2   LB_RB+2_S5_BP2D_CH2    W+2/RB3/5+ Backward;  W+2/RB3/5- Backward;  
13 0   LB_RB+1_S5_BP1E_CH0    W+1/RB4/5+ Forward;  W+1/RB4/5- Forward;    TBp1_3 13, 
13 1
13 2   LB_RB+1_S5_BP1E_CH2    W+1/RB3/5+ Forward;  W+1/RB3/5- Forward;  
14 0   LB_RB+1_S5_BP1D_CH0    W+1/RB4/5+ Backward;  W+1/RB4/5- Backward;    TB0_3 11, TBp1_3 14, TB0_4 6, 
14 1
14 2   LB_RB+1_S5_BP1D_CH2    W+1/RB3/5+ Backward;  W+1/RB3/5- Backward;  
15 0   LB_RB+1_S5_BP1C_CH0    W+1/RB2in/5 Central;    TBp1_3 15, 
15 1   LB_RB+1_S5_BP1C_CH1    W+1/RB2in/5 Forward;  
15 2   LB_RB+1_S5_BP1C_CH2    W+1/RB2in/5 Backward;  
16 0   LB_RB+1_S5_BP1B_CH0    W+1/RB1out/5 Forward;    TBp1_3 16, TBp2_3 12, TBp1_4 1, 
16 1   LB_RB+1_S5_BP1B_CH1    W+1/RB2out/5 Forward;  
16 2   LB_RB+1_S5_BP1B_CH2    W+1/RB1in/5 Forward;  
17 0   LB_RB+1_S5_BP1A_CH0    W+1/RB1out/5 Backward;    TBp1_3 17, 
17 1   LB_RB+1_S5_BP1A_CH1    W+1/RB2out/5 Backward;  
17 2   LB_RB+1_S5_BP1A_CH2    W+1/RB1in/5 Backward;  

TBp2_3
0 0   LB_RB+2_S4_BP2A_CH0    W+2/RB1out/4 Backward;    TBp2_2 17, TBp2_3 0, 
0 1   LB_RB+2_S4_BP2A_CH1    W+2/RB2in/4 Backward;  
0 2   LB_RB+2_S4_BP2A_CH2    W+2/RB1in/4 Backward;  
1 0   LB_RB+2_S4_BP2B_CH0    W+2/RB1out/4 Forward;    TBp2_2 16, TBp3_2 16, TBp2_3 1, TBp3_3 1, 
1 1   LB_RB+2_S4_BP2B_CH1    W+2/RB2in/4 Forward;  
1 2   LB_RB+2_S4_BP2B_CH2    W+2/RB1in/4 Forward;  
2 0   LB_RB+2_S4_BP2C_CH0    W+2/RB2out/4 Central;    TBp2_2 15, TBp2_3 2, 
2 1   LB_RB+2_S4_BP2C_CH1    W+2/RB2out/4 Forward;  
2 2   LB_RB+2_S4_BP2C_CH2    W+2/RB2out/4 Backward;  
4 0   LB_RB+2_S4_BP2E_CH0    W+2/RB4/4+ Forward;  W+2/RB4/4++ Forward;    TBp1_2 11, TBp2_2 13, TBp1_3 6, TBp2_3 4, 
4 1   LB_RB+2_S4_BP2E_CH1    W+2/RB4/4- Forward;  W+2/RB4/4-- Forward;  
4 2   LB_RB+2_S4_BP2E_CH2    W+2/RB3/4+ Forward;  W+2/RB3/4- Forward;  
6 0   LB_RE+1_S4_EP23_CH0    RE+2/3/12;    TBp2_3 6, TBp3_3 6, 
6 1   LB_RE+1_S4_EP23_CH1    RE+2/3/11;  
6 2   LB_RE+1_S4_EP23_CH2    RE+2/3/13;  
7 0   LB_RE+1_S4_EP13_CH0    RE+1/3/10;    TBp2_2 10, TBp2_3 7, 
7 1   LB_RE+1_S4_EP13_CH1    RE+1/3/9;  
7 2   LB_RE+1_S4_EP13_CH2    RE+1/3/11;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE+1_S5_EP13_CH0    RE+1/3/13;    TBp2_3 10, TBp2_4 7, 
10 1   LB_RE+1_S5_EP13_CH1    RE+1/3/12;  
10 2   LB_RE+1_S5_EP13_CH2    RE+1/3/14;  
11 0
11 1
11 2
12 0   LB_RB+1_S5_BP1B_CH0    W+1/RB1out/5 Forward;    TBp1_3 16, TBp2_3 12, TBp1_4 1, 
12 1   LB_RB+1_S5_BP1B_CH1    W+1/RB2out/5 Forward;  
12 2   LB_RB+1_S5_BP1B_CH2    W+1/RB1in/5 Forward;  
13 0   LB_RB+2_S5_BP2E_CH0    W+2/RB4/5+ Forward;  W+2/RB4/5- Forward;    TBp1_3 11, TBp2_3 13, 
13 1
13 2   LB_RB+2_S5_BP2E_CH2    W+2/RB3/5+ Forward;  W+2/RB3/5- Forward;  
14 0   LB_RB+2_S5_BP2D_CH0    W+2/RB4/5+ Backward;  W+2/RB4/5- Backward;    TBp1_3 12, TBp2_3 14, TBp1_4 5, 
14 1
14 2   LB_RB+2_S5_BP2D_CH2    W+2/RB3/5+ Backward;  W+2/RB3/5- Backward;  
15 0   LB_RB+2_S5_BP2C_CH0    W+2/RB2out/5 Central;    TBp2_3 15, 
15 1   LB_RB+2_S5_BP2C_CH1    W+2/RB2out/5 Forward;  
15 2   LB_RB+2_S5_BP2C_CH2    W+2/RB2out/5 Backward;  
16 0   LB_RB+2_S5_BP2B_CH0    W+2/RB1out/5 Forward;    TBp2_3 16, TBp3_3 16, 
16 1   LB_RB+2_S5_BP2B_CH1    W+2/RB2in/5 Forward;  
16 2   LB_RB+2_S5_BP2B_CH2    W+2/RB1in/5 Forward;  
17 0   LB_RB+2_S5_BP2A_CH0    W+2/RB1out/5 Backward;    TBp2_3 17, 
17 1   LB_RB+2_S5_BP2A_CH1    W+2/RB2in/5 Backward;  
17 2   LB_RB+2_S5_BP2A_CH2    W+2/RB1in/5 Backward;  

TBp3_3
0 0
0 1
0 2
1 0   LB_RB+2_S4_BP2B_CH0    W+2/RB1out/4 Forward;    TBp2_2 16, TBp3_2 16, TBp2_3 1, TBp3_3 1, 
1 1   LB_RB+2_S4_BP2B_CH1    W+2/RB2in/4 Forward;  
1 2   LB_RB+2_S4_BP2B_CH2    W+2/RB1in/4 Forward;  
2 0   LB_RE+1_S4_EP12_CH0    RE+1/2/10;    TBp3_2 15, TBp3_3 2, 
2 1   LB_RE+1_S4_EP12_CH1    RE+1/2/9;  
2 2   LB_RE+1_S4_EP12_CH2    RE+1/2/11;  
3 0   LB_RE+3_S4_EP32_CH0    RE+3/2/10;    TBp3_2 14, TBp3_3 3, 
3 1   LB_RE+3_S4_EP32_CH1    RE+3/2/9;  
3 2   LB_RE+3_S4_EP32_CH2    RE+3/2/11;  
4 0   LB_RE+3_S4_EP33_CH0    RE+3/3/10;    TBp3_2 13, TBp3_3 4, 
4 1   LB_RE+3_S4_EP33_CH1    RE+3/3/9;  
4 2   LB_RE+3_S4_EP33_CH2    RE+3/3/11;  
5 0   LB_RE+1_S4_EP22_CH0    RE+2/2/12;    TBp3_3 5, 
5 1   LB_RE+1_S4_EP22_CH1    RE+2/2/11;  
5 2   LB_RE+1_S4_EP22_CH2    RE+2/2/13;  
6 0   LB_RE+1_S4_EP23_CH0    RE+2/3/12;    TBp2_3 6, TBp3_3 6, 
6 1   LB_RE+1_S4_EP23_CH1    RE+2/3/11;  
6 2   LB_RE+1_S4_EP23_CH2    RE+2/3/13;  
7 0   LB_RE+4_S4_EP42_CH0    RE+4/2/10;    TBp3_2 12, TBp3_3 7, 
7 1   LB_RE+4_S4_EP42_CH1    RE+4/2/9;  
7 2   LB_RE+4_S4_EP42_CH2    RE+4/2/11;  
8 0   LB_RE+4_S4_EP43_CH0    RE+4/3/10;    TBp3_2 11, TBp3_3 8, 
8 1   LB_RE+4_S4_EP43_CH1    RE+4/3/9;  
8 2   LB_RE+4_S4_EP43_CH2    RE+4/3/11;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE+4_S5_EP43_CH0    RE+4/3/13;    TBp3_3 11, TBp3_4 8, 
11 1   LB_RE+4_S5_EP43_CH1    RE+4/3/12;  
11 2   LB_RE+4_S5_EP43_CH2    RE+4/3/14;  
12 0   LB_RE+4_S5_EP42_CH0    RE+4/2/13;    TBp3_3 12, 
12 1   LB_RE+4_S5_EP42_CH1    RE+4/2/12;  
12 2   LB_RE+4_S5_EP42_CH2    RE+4/2/14;  
13 0   LB_RE+3_S5_EP33_CH0    RE+3/3/13;    TBp3_3 13, 
13 1   LB_RE+3_S5_EP33_CH1    RE+3/3/12;  
13 2   LB_RE+3_S5_EP33_CH2    RE+3/3/14;  
14 0   LB_RE+3_S5_EP32_CH0    RE+3/2/13;    TBp3_3 14, 
14 1   LB_RE+3_S5_EP32_CH1    RE+3/2/12;  
14 2   LB_RE+3_S5_EP32_CH2    RE+3/2/14;  
15 0   LB_RE+1_S5_EP12_CH0    RE+1/2/13;    TBp3_3 15, 
15 1   LB_RE+1_S5_EP12_CH1    RE+1/2/12;  
15 2   LB_RE+1_S5_EP12_CH2    RE+1/2/14;  
16 0   LB_RB+2_S5_BP2B_CH0    W+2/RB1out/5 Forward;    TBp2_3 16, TBp3_3 16, 
16 1   LB_RB+2_S5_BP2B_CH1    W+2/RB2in/5 Forward;  
16 2   LB_RB+2_S5_BP2B_CH2    W+2/RB1in/5 Forward;  
17 0
17 1
17 2

TC_4
TBn3_4
0 0
0 1
0 2
5 0   LB_RE-1_S5_EN22_CH0    RE-2/2/15;    TBn3_4 5, 
5 1   LB_RE-1_S5_EN22_CH1    RE-2/2/14;  
5 2   LB_RE-1_S5_EN22_CH2    RE-2/2/16;  
6 0   LB_RE-1_S5_EN23_CH0    RE-2/3/15;    TBn3_4 6, TBn2_4 6, 
6 1   LB_RE-1_S5_EN23_CH1    RE-2/3/14;  
6 2   LB_RE-1_S5_EN23_CH2    RE-2/3/16;  
8 0   LB_RE-4_S5_EN43_CH0    RE-4/3/13;    TBn3_3 11, TBn3_4 8, 
8 1   LB_RE-4_S5_EN43_CH1    RE-4/3/12;  
8 2   LB_RE-4_S5_EN43_CH2    RE-4/3/14;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE-4_S6_EN43_CH0    RE-4/3/16;    TBn3_4 11, TBn3_5 8, 
11 1   LB_RE-4_S6_EN43_CH1    RE-4/3/15;  
11 2   LB_RE-4_S6_EN43_CH2    RE-4/3/17;  
12 0   LB_RE-4_S6_EN42_CH0    RE-4/2/16;    TBn3_4 12, TBn3_5 7, 
12 1   LB_RE-4_S6_EN42_CH1    RE-4/2/15;  
12 2   LB_RE-4_S6_EN42_CH2    RE-4/2/17;  
13 0   LB_RE-3_S6_EN33_CH0    RE-3/3/16;    TBn3_4 13, TBn3_5 4, 
13 1   LB_RE-3_S6_EN33_CH1    RE-3/3/15;  
13 2   LB_RE-3_S6_EN33_CH2    RE-3/3/17;  
14 0   LB_RE-3_S6_EN32_CH0    RE-3/2/16;    TBn3_4 14, TBn3_5 3, 
14 1   LB_RE-3_S6_EN32_CH1    RE-3/2/15;  
14 2   LB_RE-3_S6_EN32_CH2    RE-3/2/17;  
15 0   LB_RE-1_S6_EN12_CH0    RE-1/2/16;    TBn3_4 15, TBn3_5 2, 
15 1   LB_RE-1_S6_EN12_CH1    RE-1/2/15;  
15 2   LB_RE-1_S6_EN12_CH2    RE-1/2/17;  
16 0   LB_RB-2_S6_BN2B_CH0    W-2/RB1out/6 Forward;    TBn3_4 16, TBn2_4 16, TBn3_5 1, TBn2_5 1, 
16 1   LB_RB-2_S6_BN2B_CH1    W-2/RB2in/6 Forward;  
16 2   LB_RB-2_S6_BN2B_CH2    W-2/RB1in/6 Forward;  
17 0
17 1
17 2

TBn2_4
3 0   LB_RB-2_S5_BN2D_CH0    W-2/RB4/5+ Backward;  W-2/RB4/5- Backward;    TBn2_3 14, TBn1_3 12, TBn2_4 3, 
3 1
3 2   LB_RB-2_S5_BN2D_CH2    W-2/RB3/5+ Backward;  W-2/RB3/5- Backward;  
5 0   LB_RB-1_S5_BN1B_CH0    W-1/RB1out/5 Forward;    TBn2_3 12, TBn2_4 5, TBn1_3 16, 
5 1   LB_RB-1_S5_BN1B_CH1    W-1/RB2out/5 Forward;  
5 2   LB_RB-1_S5_BN1B_CH2    W-1/RB1in/5 Forward;  
6 0   LB_RE-1_S5_EN23_CH0    RE-2/3/15;    TBn3_4 6, TBn2_4 6, 
6 1   LB_RE-1_S5_EN23_CH1    RE-2/3/14;  
6 2   LB_RE-1_S5_EN23_CH2    RE-2/3/16;  
7 0   LB_RE-1_S5_EN13_CH0    RE-1/3/13;    TBn2_3 10, TBn2_4 7, 
7 1   LB_RE-1_S5_EN13_CH1    RE-1/3/12;  
7 2   LB_RE-1_S5_EN13_CH2    RE-1/3/14;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE-1_S6_EN13_CH0    RE-1/3/16;    TBn2_4 10, TBn2_5 7, 
10 1   LB_RE-1_S6_EN13_CH1    RE-1/3/15;  
10 2   LB_RE-1_S6_EN13_CH2    RE-1/3/17;  
11 0
11 1
11 2
12 0   LB_RB-1_S6_BN1B_CH0    W-1/RB1out/6 Forward;    TBn2_4 12, TBn2_5 5, TBn1_4 16, 
12 1   LB_RB-1_S6_BN1B_CH1    W-1/RB2out/6 Forward;  
12 2   LB_RB-1_S6_BN1B_CH2    W-1/RB1in/6 Forward;  
13 0   LB_RB-2_S6_BN2E_CH0    W-2/RB4/6+ Forward;  W-2/RB4/6- Forward;    TBn2_4 13, TBn1_4 11, TBn2_5 4, TBn1_5 6, 
13 1
13 2   LB_RB-2_S6_BN2E_CH2    W-2/RB3/6+ Forward;  W-2/RB3/6- Forward;  
14 0   LB_RB-2_S6_BN2D_CH0    W-2/RB4/6+ Backward;  W-2/RB4/6- Backward;    TBn2_4 14, TBn1_4 12, TBn2_5 3, 
14 1
14 2   LB_RB-2_S6_BN2D_CH2    W-2/RB3/6+ Backward;  W-2/RB3/6- Backward;  
15 0   LB_RB-2_S6_BN2C_CH0    W-2/RB2out/6 Central;    TBn2_4 15, TBn2_5 2, 
15 1   LB_RB-2_S6_BN2C_CH1    W-2/RB2out/6 Forward;  
15 2   LB_RB-2_S6_BN2C_CH2    W-2/RB2out/6 Backward;  
16 0   LB_RB-2_S6_BN2B_CH0    W-2/RB1out/6 Forward;    TBn3_4 16, TBn2_4 16, TBn3_5 1, TBn2_5 1, 
16 1   LB_RB-2_S6_BN2B_CH1    W-2/RB2in/6 Forward;  
16 2   LB_RB-2_S6_BN2B_CH2    W-2/RB1in/6 Forward;  
17 0   LB_RB-2_S6_BN2A_CH0    W-2/RB1out/6 Backward;    TBn2_4 17, TBn2_5 0, 
17 1   LB_RB-2_S6_BN2A_CH1    W-2/RB2in/6 Backward;  
17 2   LB_RB-2_S6_BN2A_CH2    W-2/RB1in/6 Backward;  

TBn1_4
3 0   LB_RB-1_S5_BN1D_CH0    W-1/RB4/5+ Backward;  W-1/RB4/5- Backward;    TBn1_3 14, TB0_3 12, TBn1_4 3, 
3 1
3 2   LB_RB-1_S5_BN1D_CH2    W-1/RB3/5+ Backward;  W-1/RB3/5- Backward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S6_BP0B_CH0    W0/RB1out/6 Backward;    TB0_4 16, TB0_5 1, TBn1_4 10, 
10 1   LB_RB0_S6_BP0B_CH1    W0/RB2out/6 Backward;  
10 2   LB_RB0_S6_BP0B_CH2    W0/RB1in/6 Backward;  
11 0   LB_RB-2_S6_BN2E_CH0    W-2/RB4/6+ Forward;  W-2/RB4/6- Forward;    TBn2_4 13, TBn1_4 11, TBn2_5 4, TBn1_5 6, 
11 1
11 2   LB_RB-2_S6_BN2E_CH2    W-2/RB3/6+ Forward;  W-2/RB3/6- Forward;  
12 0   LB_RB-2_S6_BN2D_CH0    W-2/RB4/6+ Backward;  W-2/RB4/6- Backward;    TBn2_4 14, TBn1_4 12, TBn2_5 3, 
12 1
12 2   LB_RB-2_S6_BN2D_CH2    W-2/RB3/6+ Backward;  W-2/RB3/6- Backward;  
13 0   LB_RB-1_S6_BN1E_CH0    W-1/RB4/6+ Forward;  W-1/RB4/6- Forward;    TBn1_4 13, 
13 1
13 2   LB_RB-1_S6_BN1E_CH2    W-1/RB3/6+ Forward;  W-1/RB3/6- Forward;  
14 0   LB_RB-1_S6_BN1D_CH0    W-1/RB4/6+ Backward;  W-1/RB4/6- Backward;    TBn1_4 14, TB0_4 12, TBn1_5 3, 
14 1
14 2   LB_RB-1_S6_BN1D_CH2    W-1/RB3/6+ Backward;  W-1/RB3/6- Backward;  
15 0   LB_RB-1_S6_BN1C_CH0    W-1/RB2in/6 Central;    TBn1_4 15, 
15 1   LB_RB-1_S6_BN1C_CH1    W-1/RB2in/6 Forward;  
15 2   LB_RB-1_S6_BN1C_CH2    W-1/RB2in/6 Backward;  
16 0   LB_RB-1_S6_BN1B_CH0    W-1/RB1out/6 Forward;    TBn2_4 12, TBn2_5 5, TBn1_4 16, 
16 1   LB_RB-1_S6_BN1B_CH1    W-1/RB2out/6 Forward;  
16 2   LB_RB-1_S6_BN1B_CH2    W-1/RB1in/6 Forward;  
17 0   LB_RB-1_S6_BN1A_CH0    W-1/RB1out/6 Backward;    TBn1_4 17, 
17 1   LB_RB-1_S6_BN1A_CH1    W-1/RB2out/6 Backward;  
17 2   LB_RB-1_S6_BN1A_CH2    W-1/RB1in/6 Backward;  

TB0_4
0 0   LB_RB0_S5_BN0B_CH0    W0/RB1out/5 Forward;    TB0_3 17, TB0_4 0, TBn1_3 10, 
0 1   LB_RB0_S5_BN0B_CH1    W0/RB2out/5 Forward;  
0 2   LB_RB0_S5_BN0B_CH2    W0/RB1in/5 Forward;  
1 0   LB_RB0_S5_BP0B_CH0    W0/RB1out/5 Backward;    TB0_3 16, TB0_4 1, TBp1_3 10, 
1 1   LB_RB0_S5_BP0B_CH1    W0/RB2out/5 Backward;  
1 2   LB_RB0_S5_BP0B_CH2    W0/RB1in/5 Backward;  
6 0   LB_RB+1_S5_BP1D_CH0    W+1/RB4/5+ Backward;  W+1/RB4/5- Backward;    TB0_3 11, TBp1_3 14, TB0_4 6, 
6 1
6 2   LB_RB+1_S5_BP1D_CH2    W+1/RB3/5+ Backward;  W+1/RB3/5- Backward;  
7 0
7 1
7 2
8 0
8 1
8 2
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RB+1_S6_BP1D_CH0    W+1/RB4/6+ Backward;  W+1/RB4/6- Backward;    TB0_4 11, TBp1_4 14, TB0_5 6, 
11 1
11 2   LB_RB+1_S6_BP1D_CH2    W+1/RB3/6+ Backward;  W+1/RB3/6- Backward;  
12 0   LB_RB-1_S6_BN1D_CH0    W-1/RB4/6+ Backward;  W-1/RB4/6- Backward;    TBn1_4 14, TB0_4 12, TBn1_5 3, 
12 1
12 2   LB_RB-1_S6_BN1D_CH2    W-1/RB3/6+ Backward;  W-1/RB3/6- Backward;  
13 0   LB_RB0_S6_BP0E_CH0    W0/RB4/6+ Backward;  W0/RB4/6- Backward;    TB0_4 13, 
13 1
13 2   LB_RB0_S6_BP0E_CH2    W0/RB3/6+ Backward;  W0/RB3/6- Backward;  
14 0   LB_RB0_S6_BN0E_CH0    W0/RB4/6+ Forward;  W0/RB4/6- Forward;    TB0_4 14, 
14 1
14 2   LB_RB0_S6_BN0E_CH2    W0/RB3/6+ Forward;  W0/RB3/6- Forward;  
15 0   LB_RB0_S6_BM0C_CH0    W0/RB2in/6 Central;    TB0_4 15, 
15 1   LB_RB0_S6_BM0C_CH1    W0/RB2in/6 Forward;  
15 2   LB_RB0_S6_BM0C_CH2    W0/RB2in/6 Backward;  
16 0   LB_RB0_S6_BP0B_CH0    W0/RB1out/6 Backward;    TB0_4 16, TB0_5 1, TBn1_4 10, 
16 1   LB_RB0_S6_BP0B_CH1    W0/RB2out/6 Backward;  
16 2   LB_RB0_S6_BP0B_CH2    W0/RB1in/6 Backward;  
17 0   LB_RB0_S6_BN0B_CH0    W0/RB1out/6 Forward;    TB0_4 17, TB0_5 0, TBp1_4 10, 
17 1   LB_RB0_S6_BN0B_CH1    W0/RB2out/6 Forward;  
17 2   LB_RB0_S6_BN0B_CH2    W0/RB1in/6 Forward;  

TBp1_4
1 0   LB_RB+1_S5_BP1B_CH0    W+1/RB1out/5 Forward;    TBp1_3 16, TBp2_3 12, TBp1_4 1, 
1 1   LB_RB+1_S5_BP1B_CH1    W+1/RB2out/5 Forward;  
1 2   LB_RB+1_S5_BP1B_CH2    W+1/RB1in/5 Forward;  
5 0   LB_RB+2_S5_BP2D_CH0    W+2/RB4/5+ Backward;  W+2/RB4/5- Backward;    TBp1_3 12, TBp2_3 14, TBp1_4 5, 
5 1
5 2   LB_RB+2_S5_BP2D_CH2    W+2/RB3/5+ Backward;  W+2/RB3/5- Backward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S6_BN0B_CH0    W0/RB1out/6 Forward;    TB0_4 17, TB0_5 0, TBp1_4 10, 
10 1   LB_RB0_S6_BN0B_CH1    W0/RB2out/6 Forward;  
10 2   LB_RB0_S6_BN0B_CH2    W0/RB1in/6 Forward;  
11 0   LB_RB+2_S6_BP2E_CH0    W+2/RB4/6+ Forward;  W+2/RB4/6- Forward;    TBp1_4 11, TBp2_4 13, TBp1_5 6, TBp2_5 4, 
11 1
11 2   LB_RB+2_S6_BP2E_CH2    W+2/RB3/6+ Forward;  W+2/RB3/6- Forward;  
12 0   LB_RB+2_S6_BP2D_CH0    W+2/RB4/6+ Backward;  W+2/RB4/6- Backward;    TBp1_4 12, TBp2_4 14, TBp1_5 5, TBp2_5 3, 
12 1
12 2   LB_RB+2_S6_BP2D_CH2    W+2/RB3/6+ Backward;  W+2/RB3/6- Backward;  
13 0   LB_RB+1_S6_BP1E_CH0    W+1/RB4/6+ Forward;  W+1/RB4/6- Forward;    TBp1_4 13, 
13 1
13 2   LB_RB+1_S6_BP1E_CH2    W+1/RB3/6+ Forward;  W+1/RB3/6- Forward;  
14 0   LB_RB+1_S6_BP1D_CH0    W+1/RB4/6+ Backward;  W+1/RB4/6- Backward;    TB0_4 11, TBp1_4 14, TB0_5 6, 
14 1
14 2   LB_RB+1_S6_BP1D_CH2    W+1/RB3/6+ Backward;  W+1/RB3/6- Backward;  
15 0   LB_RB+1_S6_BP1C_CH0    W+1/RB2in/6 Central;    TBp1_4 15, 
15 1   LB_RB+1_S6_BP1C_CH1    W+1/RB2in/6 Forward;  
15 2   LB_RB+1_S6_BP1C_CH2    W+1/RB2in/6 Backward;  
16 0   LB_RB+1_S6_BP1B_CH0    W+1/RB1out/6 Forward;    TBp1_4 16, TBp2_4 12, TBp1_5 1, 
16 1   LB_RB+1_S6_BP1B_CH1    W+1/RB2out/6 Forward;  
16 2   LB_RB+1_S6_BP1B_CH2    W+1/RB1in/6 Forward;  
17 0   LB_RB+1_S6_BP1A_CH0    W+1/RB1out/6 Backward;    TBp1_4 17, 
17 1   LB_RB+1_S6_BP1A_CH1    W+1/RB2out/6 Backward;  
17 2   LB_RB+1_S6_BP1A_CH2    W+1/RB1in/6 Backward;  

TBp2_4
6 0   LB_RE+1_S5_EP23_CH0    RE+2/3/15;    TBp2_4 6, TBp3_4 6, 
6 1   LB_RE+1_S5_EP23_CH1    RE+2/3/14;  
6 2   LB_RE+1_S5_EP23_CH2    RE+2/3/16;  
7 0   LB_RE+1_S5_EP13_CH0    RE+1/3/13;    TBp2_3 10, TBp2_4 7, 
7 1   LB_RE+1_S5_EP13_CH1    RE+1/3/12;  
7 2   LB_RE+1_S5_EP13_CH2    RE+1/3/14;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE+1_S6_EP13_CH0    RE+1/3/16;    TBp2_4 10, TBp2_5 7, 
10 1   LB_RE+1_S6_EP13_CH1    RE+1/3/15;  
10 2   LB_RE+1_S6_EP13_CH2    RE+1/3/17;  
11 0
11 1
11 2
12 0   LB_RB+1_S6_BP1B_CH0    W+1/RB1out/6 Forward;    TBp1_4 16, TBp2_4 12, TBp1_5 1, 
12 1   LB_RB+1_S6_BP1B_CH1    W+1/RB2out/6 Forward;  
12 2   LB_RB+1_S6_BP1B_CH2    W+1/RB1in/6 Forward;  
13 0   LB_RB+2_S6_BP2E_CH0    W+2/RB4/6+ Forward;  W+2/RB4/6- Forward;    TBp1_4 11, TBp2_4 13, TBp1_5 6, TBp2_5 4, 
13 1
13 2   LB_RB+2_S6_BP2E_CH2    W+2/RB3/6+ Forward;  W+2/RB3/6- Forward;  
14 0   LB_RB+2_S6_BP2D_CH0    W+2/RB4/6+ Backward;  W+2/RB4/6- Backward;    TBp1_4 12, TBp2_4 14, TBp1_5 5, TBp2_5 3, 
14 1
14 2   LB_RB+2_S6_BP2D_CH2    W+2/RB3/6+ Backward;  W+2/RB3/6- Backward;  
15 0   LB_RB+2_S6_BP2C_CH0    W+2/RB2out/6 Central;    TBp2_4 15, TBp2_5 2, 
15 1   LB_RB+2_S6_BP2C_CH1    W+2/RB2out/6 Forward;  
15 2   LB_RB+2_S6_BP2C_CH2    W+2/RB2out/6 Backward;  
16 0   LB_RB+2_S6_BP2B_CH0    W+2/RB1out/6 Forward;    TBp2_4 16, TBp3_4 16, TBp2_5 1, TBp3_5 1, 
16 1   LB_RB+2_S6_BP2B_CH1    W+2/RB2in/6 Forward;  
16 2   LB_RB+2_S6_BP2B_CH2    W+2/RB1in/6 Forward;  
17 0   LB_RB+2_S6_BP2A_CH0    W+2/RB1out/6 Backward;    TBp2_4 17, TBp2_5 0, 
17 1   LB_RB+2_S6_BP2A_CH1    W+2/RB2in/6 Backward;  
17 2   LB_RB+2_S6_BP2A_CH2    W+2/RB1in/6 Backward;  

TBp3_4
0 0
0 1
0 2
5 0   LB_RE+1_S5_EP22_CH0    RE+2/2/15;    TBp3_4 5, 
5 1   LB_RE+1_S5_EP22_CH1    RE+2/2/14;  
5 2   LB_RE+1_S5_EP22_CH2    RE+2/2/16;  
6 0   LB_RE+1_S5_EP23_CH0    RE+2/3/15;    TBp2_4 6, TBp3_4 6, 
6 1   LB_RE+1_S5_EP23_CH1    RE+2/3/14;  
6 2   LB_RE+1_S5_EP23_CH2    RE+2/3/16;  
8 0   LB_RE+4_S5_EP43_CH0    RE+4/3/13;    TBp3_3 11, TBp3_4 8, 
8 1   LB_RE+4_S5_EP43_CH1    RE+4/3/12;  
8 2   LB_RE+4_S5_EP43_CH2    RE+4/3/14;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE+4_S6_EP43_CH0    RE+4/3/16;    TBp3_4 11, TBp3_5 8, 
11 1   LB_RE+4_S6_EP43_CH1    RE+4/3/15;  
11 2   LB_RE+4_S6_EP43_CH2    RE+4/3/17;  
12 0   LB_RE+4_S6_EP42_CH0    RE+4/2/16;    TBp3_4 12, TBp3_5 7, 
12 1   LB_RE+4_S6_EP42_CH1    RE+4/2/15;  
12 2   LB_RE+4_S6_EP42_CH2    RE+4/2/17;  
13 0   LB_RE+3_S6_EP33_CH0    RE+3/3/16;    TBp3_4 13, TBp3_5 4, 
13 1   LB_RE+3_S6_EP33_CH1    RE+3/3/15;  
13 2   LB_RE+3_S6_EP33_CH2    RE+3/3/17;  
14 0   LB_RE+3_S6_EP32_CH0    RE+3/2/16;    TBp3_4 14, TBp3_5 3, 
14 1   LB_RE+3_S6_EP32_CH1    RE+3/2/15;  
14 2   LB_RE+3_S6_EP32_CH2    RE+3/2/17;  
15 0   LB_RE+1_S6_EP12_CH0    RE+1/2/16;    TBp3_4 15, TBp3_5 2, 
15 1   LB_RE+1_S6_EP12_CH1    RE+1/2/15;  
15 2   LB_RE+1_S6_EP12_CH2    RE+1/2/17;  
16 0   LB_RB+2_S6_BP2B_CH0    W+2/RB1out/6 Forward;    TBp2_4 16, TBp3_4 16, TBp2_5 1, TBp3_5 1, 
16 1   LB_RB+2_S6_BP2B_CH1    W+2/RB2in/6 Forward;  
16 2   LB_RB+2_S6_BP2B_CH2    W+2/RB1in/6 Forward;  
17 0
17 1
17 2

TC_5
TBn3_5
0 0
0 1
0 2
1 0   LB_RB-2_S6_BN2B_CH0    W-2/RB1out/6 Forward;    TBn3_4 16, TBn2_4 16, TBn3_5 1, TBn2_5 1, 
1 1   LB_RB-2_S6_BN2B_CH1    W-2/RB2in/6 Forward;  
1 2   LB_RB-2_S6_BN2B_CH2    W-2/RB1in/6 Forward;  
2 0   LB_RE-1_S6_EN12_CH0    RE-1/2/16;    TBn3_4 15, TBn3_5 2, 
2 1   LB_RE-1_S6_EN12_CH1    RE-1/2/15;  
2 2   LB_RE-1_S6_EN12_CH2    RE-1/2/17;  
3 0   LB_RE-3_S6_EN32_CH0    RE-3/2/16;    TBn3_4 14, TBn3_5 3, 
3 1   LB_RE-3_S6_EN32_CH1    RE-3/2/15;  
3 2   LB_RE-3_S6_EN32_CH2    RE-3/2/17;  
4 0   LB_RE-3_S6_EN33_CH0    RE-3/3/16;    TBn3_4 13, TBn3_5 4, 
4 1   LB_RE-3_S6_EN33_CH1    RE-3/3/15;  
4 2   LB_RE-3_S6_EN33_CH2    RE-3/3/17;  
5 0   LB_RE-1_S6_EN22_CH0    RE-2/2/18;    TBn3_5 5, 
5 1   LB_RE-1_S6_EN22_CH1    RE-2/2/17;  
5 2   LB_RE-1_S6_EN22_CH2    RE-2/2/19;  
6 0   LB_RE-1_S6_EN23_CH0    RE-2/3/18;    TBn3_5 6, TBn2_5 6, 
6 1   LB_RE-1_S6_EN23_CH1    RE-2/3/17;  
6 2   LB_RE-1_S6_EN23_CH2    RE-2/3/19;  
7 0   LB_RE-4_S6_EN42_CH0    RE-4/2/16;    TBn3_4 12, TBn3_5 7, 
7 1   LB_RE-4_S6_EN42_CH1    RE-4/2/15;  
7 2   LB_RE-4_S6_EN42_CH2    RE-4/2/17;  
8 0   LB_RE-4_S6_EN43_CH0    RE-4/3/16;    TBn3_4 11, TBn3_5 8, 
8 1   LB_RE-4_S6_EN43_CH1    RE-4/3/15;  
8 2   LB_RE-4_S6_EN43_CH2    RE-4/3/17;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE-4_S7_EN43_CH0    RE-4/3/19;    TBn3_5 11, TBn3_6 8, 
11 1   LB_RE-4_S7_EN43_CH1    RE-4/3/18;  
11 2   LB_RE-4_S7_EN43_CH2    RE-4/3/20;  
12 0   LB_RE-4_S7_EN42_CH0    RE-4/2/19;    TBn3_5 12, 
12 1   LB_RE-4_S7_EN42_CH1    RE-4/2/18;  
12 2   LB_RE-4_S7_EN42_CH2    RE-4/2/20;  
13 0   LB_RE-3_S7_EN33_CH0    RE-3/3/19;    TBn3_5 13, 
13 1   LB_RE-3_S7_EN33_CH1    RE-3/3/18;  
13 2   LB_RE-3_S7_EN33_CH2    RE-3/3/20;  
14 0   LB_RE-3_S7_EN32_CH0    RE-3/2/19;    TBn3_5 14, 
14 1   LB_RE-3_S7_EN32_CH1    RE-3/2/18;  
14 2   LB_RE-3_S7_EN32_CH2    RE-3/2/20;  
15 0   LB_RE-1_S7_EN12_CH0    RE-1/2/19;    TBn3_5 15, 
15 1   LB_RE-1_S7_EN12_CH1    RE-1/2/18;  
15 2   LB_RE-1_S7_EN12_CH2    RE-1/2/20;  
16 0   LB_RB-2_S7_BN2B_CH0    W-2/RB1out/7 Forward;    TBn3_5 16, TBn2_5 16, 
16 1   LB_RB-2_S7_BN2B_CH1    W-2/RB2in/7 Forward;  
16 2   LB_RB-2_S7_BN2B_CH2    W-2/RB1in/7 Forward;  
17 0
17 1
17 2

TBn2_5
0 0   LB_RB-2_S6_BN2A_CH0    W-2/RB1out/6 Backward;    TBn2_4 17, TBn2_5 0, 
0 1   LB_RB-2_S6_BN2A_CH1    W-2/RB2in/6 Backward;  
0 2   LB_RB-2_S6_BN2A_CH2    W-2/RB1in/6 Backward;  
1 0   LB_RB-2_S6_BN2B_CH0    W-2/RB1out/6 Forward;    TBn3_4 16, TBn2_4 16, TBn3_5 1, TBn2_5 1, 
1 1   LB_RB-2_S6_BN2B_CH1    W-2/RB2in/6 Forward;  
1 2   LB_RB-2_S6_BN2B_CH2    W-2/RB1in/6 Forward;  
2 0   LB_RB-2_S6_BN2C_CH0    W-2/RB2out/6 Central;    TBn2_4 15, TBn2_5 2, 
2 1   LB_RB-2_S6_BN2C_CH1    W-2/RB2out/6 Forward;  
2 2   LB_RB-2_S6_BN2C_CH2    W-2/RB2out/6 Backward;  
3 0   LB_RB-2_S6_BN2D_CH0    W-2/RB4/6+ Backward;  W-2/RB4/6- Backward;    TBn2_4 14, TBn1_4 12, TBn2_5 3, 
3 1
3 2   LB_RB-2_S6_BN2D_CH2    W-2/RB3/6+ Backward;  W-2/RB3/6- Backward;  
4 0   LB_RB-2_S6_BN2E_CH0    W-2/RB4/6+ Forward;  W-2/RB4/6- Forward;    TBn2_4 13, TBn1_4 11, TBn2_5 4, TBn1_5 6, 
4 1
4 2   LB_RB-2_S6_BN2E_CH2    W-2/RB3/6+ Forward;  W-2/RB3/6- Forward;  
5 0   LB_RB-1_S6_BN1B_CH0    W-1/RB1out/6 Forward;    TBn2_4 12, TBn2_5 5, TBn1_4 16, 
5 1   LB_RB-1_S6_BN1B_CH1    W-1/RB2out/6 Forward;  
5 2   LB_RB-1_S6_BN1B_CH2    W-1/RB1in/6 Forward;  
6 0   LB_RE-1_S6_EN23_CH0    RE-2/3/18;    TBn3_5 6, TBn2_5 6, 
6 1   LB_RE-1_S6_EN23_CH1    RE-2/3/17;  
6 2   LB_RE-1_S6_EN23_CH2    RE-2/3/19;  
7 0   LB_RE-1_S6_EN13_CH0    RE-1/3/16;    TBn2_4 10, TBn2_5 7, 
7 1   LB_RE-1_S6_EN13_CH1    RE-1/3/15;  
7 2   LB_RE-1_S6_EN13_CH2    RE-1/3/17;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE-1_S7_EN13_CH0    RE-1/3/19;    TBn2_5 10, TBn2_6 7, 
10 1   LB_RE-1_S7_EN13_CH1    RE-1/3/18;  
10 2   LB_RE-1_S7_EN13_CH2    RE-1/3/20;  
11 0
11 1
11 2
12 0   LB_RB-1_S7_BN1B_CH0    W-1/RB1out/7 Forward;    TBn2_5 12, TBn2_6 5, TBn1_5 16, 
12 1   LB_RB-1_S7_BN1B_CH1    W-1/RB2out/7 Forward;  
12 2   LB_RB-1_S7_BN1B_CH2    W-1/RB1in/7 Forward;  
13 0   LB_RB-2_S7_BN2E_CH0    W-2/RB4/7+ Forward;  W-2/RB4/7- Forward;    TBn2_5 13, TBn1_5 11, 
13 1
13 2   LB_RB-2_S7_BN2E_CH2    W-2/RB3/7+ Forward;  W-2/RB3/7- Forward;  
14 0   LB_RB-2_S7_BN2D_CH0    W-2/RB4/7+ Backward;  W-2/RB4/7- Backward;    TBn2_5 14, TBn1_5 12, TBn2_6 3, 
14 1
14 2   LB_RB-2_S7_BN2D_CH2    W-2/RB3/7+ Backward;  W-2/RB3/7- Backward;  
15 0   LB_RB-2_S7_BN2C_CH0    W-2/RB2out/7 Central;    TBn2_5 15, 
15 1   LB_RB-2_S7_BN2C_CH1    W-2/RB2out/7 Forward;  
15 2   LB_RB-2_S7_BN2C_CH2    W-2/RB2out/7 Backward;  
16 0   LB_RB-2_S7_BN2B_CH0    W-2/RB1out/7 Forward;    TBn3_5 16, TBn2_5 16, 
16 1   LB_RB-2_S7_BN2B_CH1    W-2/RB2in/7 Forward;  
16 2   LB_RB-2_S7_BN2B_CH2    W-2/RB1in/7 Forward;  
17 0   LB_RB-2_S7_BN2A_CH0    W-2/RB1out/7 Backward;    TBn2_5 17, 
17 1   LB_RB-2_S7_BN2A_CH1    W-2/RB2in/7 Backward;  
17 2   LB_RB-2_S7_BN2A_CH2    W-2/RB1in/7 Backward;  

TBn1_5
3 0   LB_RB-1_S6_BN1D_CH0    W-1/RB4/6+ Backward;  W-1/RB4/6- Backward;    TBn1_4 14, TB0_4 12, TBn1_5 3, 
3 1
3 2   LB_RB-1_S6_BN1D_CH2    W-1/RB3/6+ Backward;  W-1/RB3/6- Backward;  
6 0   LB_RB-2_S6_BN2E_CH0    W-2/RB4/6+ Forward;  W-2/RB4/6- Forward;    TBn2_4 13, TBn1_4 11, TBn2_5 4, TBn1_5 6, 
6 1
6 2   LB_RB-2_S6_BN2E_CH2    W-2/RB3/6+ Forward;  W-2/RB3/6- Forward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S7_BP0B_CH0    W0/RB1out/7 Backward;    TB0_5 16, TB0_6 1, TBn1_5 10, 
10 1   LB_RB0_S7_BP0B_CH1    W0/RB2out/7 Backward;  
10 2   LB_RB0_S7_BP0B_CH2    W0/RB1in/7 Backward;  
11 0   LB_RB-2_S7_BN2E_CH0    W-2/RB4/7+ Forward;  W-2/RB4/7- Forward;    TBn2_5 13, TBn1_5 11, 
11 1
11 2   LB_RB-2_S7_BN2E_CH2    W-2/RB3/7+ Forward;  W-2/RB3/7- Forward;  
12 0   LB_RB-2_S7_BN2D_CH0    W-2/RB4/7+ Backward;  W-2/RB4/7- Backward;    TBn2_5 14, TBn1_5 12, TBn2_6 3, 
12 1
12 2   LB_RB-2_S7_BN2D_CH2    W-2/RB3/7+ Backward;  W-2/RB3/7- Backward;  
13 0   LB_RB-1_S7_BN1E_CH0    W-1/RB4/7+ Forward;  W-1/RB4/7- Forward;    TBn1_5 13, 
13 1
13 2   LB_RB-1_S7_BN1E_CH2    W-1/RB3/7+ Forward;  W-1/RB3/7- Forward;  
14 0   LB_RB-1_S7_BN1D_CH0    W-1/RB4/7+ Backward;  W-1/RB4/7- Backward;    TBn1_5 14, TB0_5 12, TBn1_6 3, 
14 1
14 2   LB_RB-1_S7_BN1D_CH2    W-1/RB3/7+ Backward;  W-1/RB3/7- Backward;  
15 0   LB_RB-1_S7_BN1C_CH0    W-1/RB2in/7 Central;    TBn1_5 15, 
15 1   LB_RB-1_S7_BN1C_CH1    W-1/RB2in/7 Forward;  
15 2   LB_RB-1_S7_BN1C_CH2    W-1/RB2in/7 Backward;  
16 0   LB_RB-1_S7_BN1B_CH0    W-1/RB1out/7 Forward;    TBn2_5 12, TBn2_6 5, TBn1_5 16, 
16 1   LB_RB-1_S7_BN1B_CH1    W-1/RB2out/7 Forward;  
16 2   LB_RB-1_S7_BN1B_CH2    W-1/RB1in/7 Forward;  
17 0   LB_RB-1_S7_BN1A_CH0    W-1/RB1out/7 Backward;    TBn1_5 17, 
17 1   LB_RB-1_S7_BN1A_CH1    W-1/RB2out/7 Backward;  
17 2   LB_RB-1_S7_BN1A_CH2    W-1/RB1in/7 Backward;  

TB0_5
0 0   LB_RB0_S6_BN0B_CH0    W0/RB1out/6 Forward;    TB0_4 17, TB0_5 0, TBp1_4 10, 
0 1   LB_RB0_S6_BN0B_CH1    W0/RB2out/6 Forward;  
0 2   LB_RB0_S6_BN0B_CH2    W0/RB1in/6 Forward;  
1 0   LB_RB0_S6_BP0B_CH0    W0/RB1out/6 Backward;    TB0_4 16, TB0_5 1, TBn1_4 10, 
1 1   LB_RB0_S6_BP0B_CH1    W0/RB2out/6 Backward;  
1 2   LB_RB0_S6_BP0B_CH2    W0/RB1in/6 Backward;  
6 0   LB_RB+1_S6_BP1D_CH0    W+1/RB4/6+ Backward;  W+1/RB4/6- Backward;    TB0_4 11, TBp1_4 14, TB0_5 6, 
6 1
6 2   LB_RB+1_S6_BP1D_CH2    W+1/RB3/6+ Backward;  W+1/RB3/6- Backward;  
7 0
7 1
7 2
8 0
8 1
8 2
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RB+1_S7_BP1D_CH0    W+1/RB4/7+ Backward;  W+1/RB4/7- Backward;    TB0_5 11, TBp1_5 14, TB0_6 6, 
11 1
11 2   LB_RB+1_S7_BP1D_CH2    W+1/RB3/7+ Backward;  W+1/RB3/7- Backward;  
12 0   LB_RB-1_S7_BN1D_CH0    W-1/RB4/7+ Backward;  W-1/RB4/7- Backward;    TBn1_5 14, TB0_5 12, TBn1_6 3, 
12 1
12 2   LB_RB-1_S7_BN1D_CH2    W-1/RB3/7+ Backward;  W-1/RB3/7- Backward;  
13 0   LB_RB0_S7_BP0E_CH0    W0/RB4/7+ Backward;  W0/RB4/7- Backward;    TB0_5 13, 
13 1
13 2   LB_RB0_S7_BP0E_CH2    W0/RB3/7+ Backward;  W0/RB3/7- Backward;  
14 0   LB_RB0_S7_BN0E_CH0    W0/RB4/7+ Forward;  W0/RB4/7- Forward;    TB0_5 14, 
14 1
14 2   LB_RB0_S7_BN0E_CH2    W0/RB3/7+ Forward;  W0/RB3/7- Forward;  
15 0   LB_RB0_S7_BM0C_CH0    W0/RB2in/7 Central;    TB0_5 15, 
15 1   LB_RB0_S7_BM0C_CH1    W0/RB2in/7 Forward;  
15 2   LB_RB0_S7_BM0C_CH2    W0/RB2in/7 Backward;  
16 0   LB_RB0_S7_BP0B_CH0    W0/RB1out/7 Backward;    TB0_5 16, TB0_6 1, TBn1_5 10, 
16 1   LB_RB0_S7_BP0B_CH1    W0/RB2out/7 Backward;  
16 2   LB_RB0_S7_BP0B_CH2    W0/RB1in/7 Backward;  
17 0   LB_RB0_S7_BN0B_CH0    W0/RB1out/7 Forward;    TB0_5 17, TB0_6 0, TBp1_5 10, 
17 1   LB_RB0_S7_BN0B_CH1    W0/RB2out/7 Forward;  
17 2   LB_RB0_S7_BN0B_CH2    W0/RB1in/7 Forward;  

TBp1_5
1 0   LB_RB+1_S6_BP1B_CH0    W+1/RB1out/6 Forward;    TBp1_4 16, TBp2_4 12, TBp1_5 1, 
1 1   LB_RB+1_S6_BP1B_CH1    W+1/RB2out/6 Forward;  
1 2   LB_RB+1_S6_BP1B_CH2    W+1/RB1in/6 Forward;  
5 0   LB_RB+2_S6_BP2D_CH0    W+2/RB4/6+ Backward;  W+2/RB4/6- Backward;    TBp1_4 12, TBp2_4 14, TBp1_5 5, TBp2_5 3, 
5 1
5 2   LB_RB+2_S6_BP2D_CH2    W+2/RB3/6+ Backward;  W+2/RB3/6- Backward;  
6 0   LB_RB+2_S6_BP2E_CH0    W+2/RB4/6+ Forward;  W+2/RB4/6- Forward;    TBp1_4 11, TBp2_4 13, TBp1_5 6, TBp2_5 4, 
6 1
6 2   LB_RB+2_S6_BP2E_CH2    W+2/RB3/6+ Forward;  W+2/RB3/6- Forward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S7_BN0B_CH0    W0/RB1out/7 Forward;    TB0_5 17, TB0_6 0, TBp1_5 10, 
10 1   LB_RB0_S7_BN0B_CH1    W0/RB2out/7 Forward;  
10 2   LB_RB0_S7_BN0B_CH2    W0/RB1in/7 Forward;  
11 0   LB_RB+2_S7_BP2E_CH0    W+2/RB4/7+ Forward;  W+2/RB4/7- Forward;    TBp1_5 11, TBp2_5 13, TBp1_6 6, 
11 1
11 2   LB_RB+2_S7_BP2E_CH2    W+2/RB3/7+ Forward;  W+2/RB3/7- Forward;  
12 0   LB_RB+2_S7_BP2D_CH0    W+2/RB4/7+ Backward;  W+2/RB4/7- Backward;    TBp1_5 12, TBp2_5 14, TBp1_6 5, TBp2_6 3, 
12 1
12 2   LB_RB+2_S7_BP2D_CH2    W+2/RB3/7+ Backward;  W+2/RB3/7- Backward;  
13 0   LB_RB+1_S7_BP1E_CH0    W+1/RB4/7+ Forward;  W+1/RB4/7- Forward;    TBp1_5 13, 
13 1
13 2   LB_RB+1_S7_BP1E_CH2    W+1/RB3/7+ Forward;  W+1/RB3/7- Forward;  
14 0   LB_RB+1_S7_BP1D_CH0    W+1/RB4/7+ Backward;  W+1/RB4/7- Backward;    TB0_5 11, TBp1_5 14, TB0_6 6, 
14 1
14 2   LB_RB+1_S7_BP1D_CH2    W+1/RB3/7+ Backward;  W+1/RB3/7- Backward;  
15 0   LB_RB+1_S7_BP1C_CH0    W+1/RB2in/7 Central;    TBp1_5 15, 
15 1   LB_RB+1_S7_BP1C_CH1    W+1/RB2in/7 Forward;  
15 2   LB_RB+1_S7_BP1C_CH2    W+1/RB2in/7 Backward;  
16 0   LB_RB+1_S7_BP1B_CH0    W+1/RB1out/7 Forward;    TBp1_5 16, TBp2_5 12, TBp1_6 1, 
16 1   LB_RB+1_S7_BP1B_CH1    W+1/RB2out/7 Forward;  
16 2   LB_RB+1_S7_BP1B_CH2    W+1/RB1in/7 Forward;  
17 0   LB_RB+1_S7_BP1A_CH0    W+1/RB1out/7 Backward;    TBp1_5 17, 
17 1   LB_RB+1_S7_BP1A_CH1    W+1/RB2out/7 Backward;  
17 2   LB_RB+1_S7_BP1A_CH2    W+1/RB1in/7 Backward;  

TBp2_5
0 0   LB_RB+2_S6_BP2A_CH0    W+2/RB1out/6 Backward;    TBp2_4 17, TBp2_5 0, 
0 1   LB_RB+2_S6_BP2A_CH1    W+2/RB2in/6 Backward;  
0 2   LB_RB+2_S6_BP2A_CH2    W+2/RB1in/6 Backward;  
1 0   LB_RB+2_S6_BP2B_CH0    W+2/RB1out/6 Forward;    TBp2_4 16, TBp3_4 16, TBp2_5 1, TBp3_5 1, 
1 1   LB_RB+2_S6_BP2B_CH1    W+2/RB2in/6 Forward;  
1 2   LB_RB+2_S6_BP2B_CH2    W+2/RB1in/6 Forward;  
2 0   LB_RB+2_S6_BP2C_CH0    W+2/RB2out/6 Central;    TBp2_4 15, TBp2_5 2, 
2 1   LB_RB+2_S6_BP2C_CH1    W+2/RB2out/6 Forward;  
2 2   LB_RB+2_S6_BP2C_CH2    W+2/RB2out/6 Backward;  
3 0   LB_RB+2_S6_BP2D_CH0    W+2/RB4/6+ Backward;  W+2/RB4/6- Backward;    TBp1_4 12, TBp2_4 14, TBp1_5 5, TBp2_5 3, 
3 1
3 2   LB_RB+2_S6_BP2D_CH2    W+2/RB3/6+ Backward;  W+2/RB3/6- Backward;  
4 0   LB_RB+2_S6_BP2E_CH0    W+2/RB4/6+ Forward;  W+2/RB4/6- Forward;    TBp1_4 11, TBp2_4 13, TBp1_5 6, TBp2_5 4, 
4 1
4 2   LB_RB+2_S6_BP2E_CH2    W+2/RB3/6+ Forward;  W+2/RB3/6- Forward;  
6 0   LB_RE+1_S6_EP23_CH0    RE+2/3/18;    TBp2_5 6, TBp3_5 6, 
6 1   LB_RE+1_S6_EP23_CH1    RE+2/3/17;  
6 2   LB_RE+1_S6_EP23_CH2    RE+2/3/19;  
7 0   LB_RE+1_S6_EP13_CH0    RE+1/3/16;    TBp2_4 10, TBp2_5 7, 
7 1   LB_RE+1_S6_EP13_CH1    RE+1/3/15;  
7 2   LB_RE+1_S6_EP13_CH2    RE+1/3/17;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE+1_S7_EP13_CH0    RE+1/3/19;    TBp2_5 10, TBp2_6 7, 
10 1   LB_RE+1_S7_EP13_CH1    RE+1/3/18;  
10 2   LB_RE+1_S7_EP13_CH2    RE+1/3/20;  
11 0
11 1
11 2
12 0   LB_RB+1_S7_BP1B_CH0    W+1/RB1out/7 Forward;    TBp1_5 16, TBp2_5 12, TBp1_6 1, 
12 1   LB_RB+1_S7_BP1B_CH1    W+1/RB2out/7 Forward;  
12 2   LB_RB+1_S7_BP1B_CH2    W+1/RB1in/7 Forward;  
13 0   LB_RB+2_S7_BP2E_CH0    W+2/RB4/7+ Forward;  W+2/RB4/7- Forward;    TBp1_5 11, TBp2_5 13, TBp1_6 6, 
13 1
13 2   LB_RB+2_S7_BP2E_CH2    W+2/RB3/7+ Forward;  W+2/RB3/7- Forward;  
14 0   LB_RB+2_S7_BP2D_CH0    W+2/RB4/7+ Backward;  W+2/RB4/7- Backward;    TBp1_5 12, TBp2_5 14, TBp1_6 5, TBp2_6 3, 
14 1
14 2   LB_RB+2_S7_BP2D_CH2    W+2/RB3/7+ Backward;  W+2/RB3/7- Backward;  
15 0   LB_RB+2_S7_BP2C_CH0    W+2/RB2out/7 Central;    TBp2_5 15, 
15 1   LB_RB+2_S7_BP2C_CH1    W+2/RB2out/7 Forward;  
15 2   LB_RB+2_S7_BP2C_CH2    W+2/RB2out/7 Backward;  
16 0   LB_RB+2_S7_BP2B_CH0    W+2/RB1out/7 Forward;    TBp2_5 16, TBp3_5 16, TBp2_6 1, 
16 1   LB_RB+2_S7_BP2B_CH1    W+2/RB2in/7 Forward;  
16 2   LB_RB+2_S7_BP2B_CH2    W+2/RB1in/7 Forward;  
17 0   LB_RB+2_S7_BP2A_CH0    W+2/RB1out/7 Backward;    TBp2_5 17, 
17 1   LB_RB+2_S7_BP2A_CH1    W+2/RB2in/7 Backward;  
17 2   LB_RB+2_S7_BP2A_CH2    W+2/RB1in/7 Backward;  

TBp3_5
0 0
0 1
0 2
1 0   LB_RB+2_S6_BP2B_CH0    W+2/RB1out/6 Forward;    TBp2_4 16, TBp3_4 16, TBp2_5 1, TBp3_5 1, 
1 1   LB_RB+2_S6_BP2B_CH1    W+2/RB2in/6 Forward;  
1 2   LB_RB+2_S6_BP2B_CH2    W+2/RB1in/6 Forward;  
2 0   LB_RE+1_S6_EP12_CH0    RE+1/2/16;    TBp3_4 15, TBp3_5 2, 
2 1   LB_RE+1_S6_EP12_CH1    RE+1/2/15;  
2 2   LB_RE+1_S6_EP12_CH2    RE+1/2/17;  
3 0   LB_RE+3_S6_EP32_CH0    RE+3/2/16;    TBp3_4 14, TBp3_5 3, 
3 1   LB_RE+3_S6_EP32_CH1    RE+3/2/15;  
3 2   LB_RE+3_S6_EP32_CH2    RE+3/2/17;  
4 0   LB_RE+3_S6_EP33_CH0    RE+3/3/16;    TBp3_4 13, TBp3_5 4, 
4 1   LB_RE+3_S6_EP33_CH1    RE+3/3/15;  
4 2   LB_RE+3_S6_EP33_CH2    RE+3/3/17;  
5 0   LB_RE+1_S6_EP22_CH0    RE+2/2/18;    TBp3_5 5, 
5 1   LB_RE+1_S6_EP22_CH1    RE+2/2/17;  
5 2   LB_RE+1_S6_EP22_CH2    RE+2/2/19;  
6 0   LB_RE+1_S6_EP23_CH0    RE+2/3/18;    TBp2_5 6, TBp3_5 6, 
6 1   LB_RE+1_S6_EP23_CH1    RE+2/3/17;  
6 2   LB_RE+1_S6_EP23_CH2    RE+2/3/19;  
7 0   LB_RE+4_S6_EP42_CH0    RE+4/2/16;    TBp3_4 12, TBp3_5 7, 
7 1   LB_RE+4_S6_EP42_CH1    RE+4/2/15;  
7 2   LB_RE+4_S6_EP42_CH2    RE+4/2/17;  
8 0   LB_RE+4_S6_EP43_CH0    RE+4/3/16;    TBp3_4 11, TBp3_5 8, 
8 1   LB_RE+4_S6_EP43_CH1    RE+4/3/15;  
8 2   LB_RE+4_S6_EP43_CH2    RE+4/3/17;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE+4_S7_EP43_CH0    RE+4/3/19;    TBp3_5 11, TBp3_6 8, 
11 1   LB_RE+4_S7_EP43_CH1    RE+4/3/18;  
11 2   LB_RE+4_S7_EP43_CH2    RE+4/3/20;  
12 0   LB_RE+4_S7_EP42_CH0    RE+4/2/19;    TBp3_5 12, 
12 1   LB_RE+4_S7_EP42_CH1    RE+4/2/18;  
12 2   LB_RE+4_S7_EP42_CH2    RE+4/2/20;  
13 0   LB_RE+3_S7_EP33_CH0    RE+3/3/19;    TBp3_5 13, 
13 1   LB_RE+3_S7_EP33_CH1    RE+3/3/18;  
13 2   LB_RE+3_S7_EP33_CH2    RE+3/3/20;  
14 0   LB_RE+3_S7_EP32_CH0    RE+3/2/19;    TBp3_5 14, 
14 1   LB_RE+3_S7_EP32_CH1    RE+3/2/18;  
14 2   LB_RE+3_S7_EP32_CH2    RE+3/2/20;  
15 0   LB_RE+1_S7_EP12_CH0    RE+1/2/19;    TBp3_5 15, 
15 1   LB_RE+1_S7_EP12_CH1    RE+1/2/18;  
15 2   LB_RE+1_S7_EP12_CH2    RE+1/2/20;  
16 0   LB_RB+2_S7_BP2B_CH0    W+2/RB1out/7 Forward;    TBp2_5 16, TBp3_5 16, TBp2_6 1, 
16 1   LB_RB+2_S7_BP2B_CH1    W+2/RB2in/7 Forward;  
16 2   LB_RB+2_S7_BP2B_CH2    W+2/RB1in/7 Forward;  
17 0
17 1
17 2

TC_6
TBn3_6
0 0
0 1
0 2
5 0   LB_RE-1_S7_EN22_CH0    RE-2/2/21;    TBn3_6 5, 
5 1   LB_RE-1_S7_EN22_CH1    RE-2/2/20;  
5 2   LB_RE-1_S7_EN22_CH2    RE-2/2/22;  
6 0   LB_RE-1_S7_EN23_CH0    RE-2/3/21;    TBn3_6 6, TBn2_6 6, 
6 1   LB_RE-1_S7_EN23_CH1    RE-2/3/20;  
6 2   LB_RE-1_S7_EN23_CH2    RE-2/3/22;  
8 0   LB_RE-4_S7_EN43_CH0    RE-4/3/19;    TBn3_5 11, TBn3_6 8, 
8 1   LB_RE-4_S7_EN43_CH1    RE-4/3/18;  
8 2   LB_RE-4_S7_EN43_CH2    RE-4/3/20;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE-4_S8_EN43_CH0    RE-4/3/22;    TBn3_6 11, TBn3_7 8, 
11 1   LB_RE-4_S8_EN43_CH1    RE-4/3/21;  
11 2   LB_RE-4_S8_EN43_CH2    RE-4/3/23;  
12 0   LB_RE-4_S8_EN42_CH0    RE-4/2/22;    TBn3_6 12, TBn3_7 7, 
12 1   LB_RE-4_S8_EN42_CH1    RE-4/2/21;  
12 2   LB_RE-4_S8_EN42_CH2    RE-4/2/23;  
13 0   LB_RE-3_S8_EN33_CH0    RE-3/3/22;    TBn3_6 13, TBn3_7 4, 
13 1   LB_RE-3_S8_EN33_CH1    RE-3/3/21;  
13 2   LB_RE-3_S8_EN33_CH2    RE-3/3/23;  
14 0   LB_RE-3_S8_EN32_CH0    RE-3/2/22;    TBn3_6 14, TBn3_7 3, 
14 1   LB_RE-3_S8_EN32_CH1    RE-3/2/21;  
14 2   LB_RE-3_S8_EN32_CH2    RE-3/2/23;  
15 0   LB_RE-1_S8_EN12_CH0    RE-1/2/22;    TBn3_6 15, TBn3_7 2, 
15 1   LB_RE-1_S8_EN12_CH1    RE-1/2/21;  
15 2   LB_RE-1_S8_EN12_CH2    RE-1/2/23;  
16 0   LB_RB-2_S8_BN2B_CH0    W-2/RB1out/8 Forward;    TBn3_6 16, TBn2_6 16, TBn3_7 1, TBn2_7 1, 
16 1   LB_RB-2_S8_BN2B_CH1    W-2/RB2in/8 Forward;  
16 2   LB_RB-2_S8_BN2B_CH2    W-2/RB1in/8 Forward;  
17 0
17 1
17 2

TBn2_6
3 0   LB_RB-2_S7_BN2D_CH0    W-2/RB4/7+ Backward;  W-2/RB4/7- Backward;    TBn2_5 14, TBn1_5 12, TBn2_6 3, 
3 1
3 2   LB_RB-2_S7_BN2D_CH2    W-2/RB3/7+ Backward;  W-2/RB3/7- Backward;  
5 0   LB_RB-1_S7_BN1B_CH0    W-1/RB1out/7 Forward;    TBn2_5 12, TBn2_6 5, TBn1_5 16, 
5 1   LB_RB-1_S7_BN1B_CH1    W-1/RB2out/7 Forward;  
5 2   LB_RB-1_S7_BN1B_CH2    W-1/RB1in/7 Forward;  
6 0   LB_RE-1_S7_EN23_CH0    RE-2/3/21;    TBn3_6 6, TBn2_6 6, 
6 1   LB_RE-1_S7_EN23_CH1    RE-2/3/20;  
6 2   LB_RE-1_S7_EN23_CH2    RE-2/3/22;  
7 0   LB_RE-1_S7_EN13_CH0    RE-1/3/19;    TBn2_5 10, TBn2_6 7, 
7 1   LB_RE-1_S7_EN13_CH1    RE-1/3/18;  
7 2   LB_RE-1_S7_EN13_CH2    RE-1/3/20;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE-1_S8_EN13_CH0    RE-1/3/22;    TBn2_6 10, TBn2_7 7, 
10 1   LB_RE-1_S8_EN13_CH1    RE-1/3/21;  
10 2   LB_RE-1_S8_EN13_CH2    RE-1/3/23;  
11 0
11 1
11 2
12 0   LB_RB-1_S8_BN1B_CH0    W-1/RB1out/8 Forward;    TBn2_6 12, TBn2_7 5, TBn1_6 16, 
12 1   LB_RB-1_S8_BN1B_CH1    W-1/RB2out/8 Forward;  
12 2   LB_RB-1_S8_BN1B_CH2    W-1/RB1in/8 Forward;  
14 0   LB_RB-2_S8_BN2D_CH0    W-2/RB4/8+ Backward;  W-2/RB4/8- Backward;    TBn2_6 14, TBn1_6 12, TBn2_7 3, 
14 1
14 2   LB_RB-2_S8_BN2D_CH2    W-2/RB3/8+ Backward;  W-2/RB3/8- Backward;  
15 0   LB_RB-2_S8_BN2C_CH0    W-2/RB2out/8 Central;    TBn2_6 15, TBn2_7 2, 
15 1   LB_RB-2_S8_BN2C_CH1    W-2/RB2out/8 Forward;  
15 2   LB_RB-2_S8_BN2C_CH2    W-2/RB2out/8 Backward;  
16 0   LB_RB-2_S8_BN2B_CH0    W-2/RB1out/8 Forward;    TBn3_6 16, TBn2_6 16, TBn3_7 1, TBn2_7 1, 
16 1   LB_RB-2_S8_BN2B_CH1    W-2/RB2in/8 Forward;  
16 2   LB_RB-2_S8_BN2B_CH2    W-2/RB1in/8 Forward;  
17 0   LB_RB-2_S8_BN2A_CH0    W-2/RB1out/8 Backward;    TBn2_6 17, TBn2_7 0, 
17 1   LB_RB-2_S8_BN2A_CH1    W-2/RB2in/8 Backward;  
17 2   LB_RB-2_S8_BN2A_CH2    W-2/RB1in/8 Backward;  

TBn1_6
3 0   LB_RB-1_S7_BN1D_CH0    W-1/RB4/7+ Backward;  W-1/RB4/7- Backward;    TBn1_5 14, TB0_5 12, TBn1_6 3, 
3 1
3 2   LB_RB-1_S7_BN1D_CH2    W-1/RB3/7+ Backward;  W-1/RB3/7- Backward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S8_BN0B_CH0    W0/RB1out/8 Forward;    TB0_6 17, TB0_7 0, TBn1_6 10, 
10 1   LB_RB0_S8_BN0B_CH1    W0/RB2out/8 Forward;  
10 2   LB_RB0_S8_BN0B_CH2    W0/RB1in/8 Forward;  
12 0   LB_RB-2_S8_BN2D_CH0    W-2/RB4/8+ Backward;  W-2/RB4/8- Backward;    TBn2_6 14, TBn1_6 12, TBn2_7 3, 
12 1
12 2   LB_RB-2_S8_BN2D_CH2    W-2/RB3/8+ Backward;  W-2/RB3/8- Backward;  
13 0   LB_RB-1_S8_BN1E_CH0    W-1/RB4/8+ Forward;  W-1/RB4/8- Forward;    TBn1_6 13, 
13 1
13 2   LB_RB-1_S8_BN1E_CH2    W-1/RB3/8+ Forward;  W-1/RB3/8- Forward;  
14 0   LB_RB-1_S8_BN1D_CH0    W-1/RB4/8+ Backward;  W-1/RB4/8- Backward;    TBn1_6 14, TB0_6 12, TBn1_7 3, 
14 1
14 2   LB_RB-1_S8_BN1D_CH2    W-1/RB3/8+ Backward;  W-1/RB3/8- Backward;  
15 0   LB_RB-1_S8_BN1C_CH0    W-1/RB2in/8 Central;    TBn1_6 15, 
15 1   LB_RB-1_S8_BN1C_CH1    W-1/RB2in/8 Forward;  
15 2   LB_RB-1_S8_BN1C_CH2    W-1/RB2in/8 Backward;  
16 0   LB_RB-1_S8_BN1B_CH0    W-1/RB1out/8 Forward;    TBn2_6 12, TBn2_7 5, TBn1_6 16, 
16 1   LB_RB-1_S8_BN1B_CH1    W-1/RB2out/8 Forward;  
16 2   LB_RB-1_S8_BN1B_CH2    W-1/RB1in/8 Forward;  
17 0   LB_RB-1_S8_BN1A_CH0    W-1/RB1out/8 Backward;    TBn1_6 17, 
17 1   LB_RB-1_S8_BN1A_CH1    W-1/RB2out/8 Backward;  
17 2   LB_RB-1_S8_BN1A_CH2    W-1/RB1in/8 Backward;  

TB0_6
0 0   LB_RB0_S7_BN0B_CH0    W0/RB1out/7 Forward;    TB0_5 17, TB0_6 0, TBp1_5 10, 
0 1   LB_RB0_S7_BN0B_CH1    W0/RB2out/7 Forward;  
0 2   LB_RB0_S7_BN0B_CH2    W0/RB1in/7 Forward;  
1 0   LB_RB0_S7_BP0B_CH0    W0/RB1out/7 Backward;    TB0_5 16, TB0_6 1, TBn1_5 10, 
1 1   LB_RB0_S7_BP0B_CH1    W0/RB2out/7 Backward;  
1 2   LB_RB0_S7_BP0B_CH2    W0/RB1in/7 Backward;  
6 0   LB_RB+1_S7_BP1D_CH0    W+1/RB4/7+ Backward;  W+1/RB4/7- Backward;    TB0_5 11, TBp1_5 14, TB0_6 6, 
6 1
6 2   LB_RB+1_S7_BP1D_CH2    W+1/RB3/7+ Backward;  W+1/RB3/7- Backward;  
7 0
7 1
7 2
8 0
8 1
8 2
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RB+1_S8_BP1D_CH0    W+1/RB4/8+ Backward;  W+1/RB4/8- Backward;    TB0_6 11, TBp1_6 14, TB0_7 6, 
11 1
11 2   LB_RB+1_S8_BP1D_CH2    W+1/RB3/8+ Backward;  W+1/RB3/8- Backward;  
12 0   LB_RB-1_S8_BN1D_CH0    W-1/RB4/8+ Backward;  W-1/RB4/8- Backward;    TBn1_6 14, TB0_6 12, TBn1_7 3, 
12 1
12 2   LB_RB-1_S8_BN1D_CH2    W-1/RB3/8+ Backward;  W-1/RB3/8- Backward;  
13 0   LB_RB0_S8_BP0E_CH0    W0/RB4/8+ Backward;  W0/RB4/8- Backward;    TB0_6 13, 
13 1
13 2   LB_RB0_S8_BP0E_CH2    W0/RB3/8+ Backward;  W0/RB3/8- Backward;  
14 0   LB_RB0_S8_BN0E_CH0    W0/RB4/8+ Forward;  W0/RB4/8- Forward;    TB0_6 14, 
14 1
14 2   LB_RB0_S8_BN0E_CH2    W0/RB3/8+ Forward;  W0/RB3/8- Forward;  
15 0   LB_RB0_S8_BM0C_CH0    W0/RB2in/8 Central;    TB0_6 15, 
15 1   LB_RB0_S8_BM0C_CH1    W0/RB2in/8 Forward;  
15 2   LB_RB0_S8_BM0C_CH2    W0/RB2in/8 Backward;  
16 0   LB_RB0_S8_BP0B_CH0    W0/RB1out/8 Backward;    TB0_6 16, TB0_7 1, TBp1_6 10, 
16 1   LB_RB0_S8_BP0B_CH1    W0/RB2out/8 Backward;  
16 2   LB_RB0_S8_BP0B_CH2    W0/RB1in/8 Backward;  
17 0   LB_RB0_S8_BN0B_CH0    W0/RB1out/8 Forward;    TB0_6 17, TB0_7 0, TBn1_6 10, 
17 1   LB_RB0_S8_BN0B_CH1    W0/RB2out/8 Forward;  
17 2   LB_RB0_S8_BN0B_CH2    W0/RB1in/8 Forward;  

TBp1_6
1 0   LB_RB+1_S7_BP1B_CH0    W+1/RB1out/7 Forward;    TBp1_5 16, TBp2_5 12, TBp1_6 1, 
1 1   LB_RB+1_S7_BP1B_CH1    W+1/RB2out/7 Forward;  
1 2   LB_RB+1_S7_BP1B_CH2    W+1/RB1in/7 Forward;  
5 0   LB_RB+2_S7_BP2D_CH0    W+2/RB4/7+ Backward;  W+2/RB4/7- Backward;    TBp1_5 12, TBp2_5 14, TBp1_6 5, TBp2_6 3, 
5 1
5 2   LB_RB+2_S7_BP2D_CH2    W+2/RB3/7+ Backward;  W+2/RB3/7- Backward;  
6 0   LB_RB+2_S7_BP2E_CH0    W+2/RB4/7+ Forward;  W+2/RB4/7- Forward;    TBp1_5 11, TBp2_5 13, TBp1_6 6, 
6 1
6 2   LB_RB+2_S7_BP2E_CH2    W+2/RB3/7+ Forward;  W+2/RB3/7- Forward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S8_BP0B_CH0    W0/RB1out/8 Backward;    TB0_6 16, TB0_7 1, TBp1_6 10, 
10 1   LB_RB0_S8_BP0B_CH1    W0/RB2out/8 Backward;  
10 2   LB_RB0_S8_BP0B_CH2    W0/RB1in/8 Backward;  
11 0   LB_RB+2_S8_BP2E_CH0    W+2/RB4/8+ Forward;  W+2/RB4/8- Forward;    TBp1_6 11, TBp2_6 13, TBp1_7 6, TBp2_7 4, 
11 1
11 2   LB_RB+2_S8_BP2E_CH2    W+2/RB3/8+ Forward;  W+2/RB3/8- Forward;  
12 0   LB_RB+2_S8_BP2D_CH0    W+2/RB4/8+ Backward;  W+2/RB4/8- Backward;    TBp1_6 12, TBp2_6 14, TBp1_7 5, TBp2_7 3, 
12 1
12 2   LB_RB+2_S8_BP2D_CH2    W+2/RB3/8+ Backward;  W+2/RB3/8- Backward;  
13 0   LB_RB+1_S8_BP1E_CH0    W+1/RB4/8+ Forward;  W+1/RB4/8- Forward;    TBp1_6 13, 
13 1
13 2   LB_RB+1_S8_BP1E_CH2    W+1/RB3/8+ Forward;  W+1/RB3/8- Forward;  
14 0   LB_RB+1_S8_BP1D_CH0    W+1/RB4/8+ Backward;  W+1/RB4/8- Backward;    TB0_6 11, TBp1_6 14, TB0_7 6, 
14 1
14 2   LB_RB+1_S8_BP1D_CH2    W+1/RB3/8+ Backward;  W+1/RB3/8- Backward;  
15 0   LB_RB+1_S8_BP1C_CH0    W+1/RB2in/8 Central;    TBp1_6 15, 
15 1   LB_RB+1_S8_BP1C_CH1    W+1/RB2in/8 Forward;  
15 2   LB_RB+1_S8_BP1C_CH2    W+1/RB2in/8 Backward;  
16 0   LB_RB+1_S8_BP1B_CH0    W+1/RB1out/8 Forward;    TBp1_6 16, TBp2_6 12, TBp1_7 1, 
16 1   LB_RB+1_S8_BP1B_CH1    W+1/RB2out/8 Forward;  
16 2   LB_RB+1_S8_BP1B_CH2    W+1/RB1in/8 Forward;  
17 0   LB_RB+1_S8_BP1A_CH0    W+1/RB1out/8 Backward;    TBp1_6 17, 
17 1   LB_RB+1_S8_BP1A_CH1    W+1/RB2out/8 Backward;  
17 2   LB_RB+1_S8_BP1A_CH2    W+1/RB1in/8 Backward;  

TBp2_6
1 0   LB_RB+2_S7_BP2B_CH0    W+2/RB1out/7 Forward;    TBp2_5 16, TBp3_5 16, TBp2_6 1, 
1 1   LB_RB+2_S7_BP2B_CH1    W+2/RB2in/7 Forward;  
1 2   LB_RB+2_S7_BP2B_CH2    W+2/RB1in/7 Forward;  
3 0   LB_RB+2_S7_BP2D_CH0    W+2/RB4/7+ Backward;  W+2/RB4/7- Backward;    TBp1_5 12, TBp2_5 14, TBp1_6 5, TBp2_6 3, 
3 1
3 2   LB_RB+2_S7_BP2D_CH2    W+2/RB3/7+ Backward;  W+2/RB3/7- Backward;  
6 0   LB_RE+1_S7_EP23_CH0    RE+2/3/21;    TBp2_6 6, TBp3_6 6, 
6 1   LB_RE+1_S7_EP23_CH1    RE+2/3/20;  
6 2   LB_RE+1_S7_EP23_CH2    RE+2/3/22;  
7 0   LB_RE+1_S7_EP13_CH0    RE+1/3/19;    TBp2_5 10, TBp2_6 7, 
7 1   LB_RE+1_S7_EP13_CH1    RE+1/3/18;  
7 2   LB_RE+1_S7_EP13_CH2    RE+1/3/20;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE+1_S8_EP13_CH0    RE+1/3/22;    TBp2_6 10, TBp2_7 7, 
10 1   LB_RE+1_S8_EP13_CH1    RE+1/3/21;  
10 2   LB_RE+1_S8_EP13_CH2    RE+1/3/23;  
11 0
11 1
11 2
12 0   LB_RB+1_S8_BP1B_CH0    W+1/RB1out/8 Forward;    TBp1_6 16, TBp2_6 12, TBp1_7 1, 
12 1   LB_RB+1_S8_BP1B_CH1    W+1/RB2out/8 Forward;  
12 2   LB_RB+1_S8_BP1B_CH2    W+1/RB1in/8 Forward;  
13 0   LB_RB+2_S8_BP2E_CH0    W+2/RB4/8+ Forward;  W+2/RB4/8- Forward;    TBp1_6 11, TBp2_6 13, TBp1_7 6, TBp2_7 4, 
13 1
13 2   LB_RB+2_S8_BP2E_CH2    W+2/RB3/8+ Forward;  W+2/RB3/8- Forward;  
14 0   LB_RB+2_S8_BP2D_CH0    W+2/RB4/8+ Backward;  W+2/RB4/8- Backward;    TBp1_6 12, TBp2_6 14, TBp1_7 5, TBp2_7 3, 
14 1
14 2   LB_RB+2_S8_BP2D_CH2    W+2/RB3/8+ Backward;  W+2/RB3/8- Backward;  
15 0   LB_RB+2_S8_BP2C_CH0    W+2/RB2out/8 Central;    TBp2_6 15, TBp2_7 2, 
15 1   LB_RB+2_S8_BP2C_CH1    W+2/RB2out/8 Forward;  
15 2   LB_RB+2_S8_BP2C_CH2    W+2/RB2out/8 Backward;  
16 0   LB_RB+2_S8_BP2B_CH0    W+2/RB1out/8 Forward;    TBp2_6 16, TBp3_6 16, TBp2_7 1, TBp3_7 1, 
16 1   LB_RB+2_S8_BP2B_CH1    W+2/RB2in/8 Forward;  
16 2   LB_RB+2_S8_BP2B_CH2    W+2/RB1in/8 Forward;  
17 0   LB_RB+2_S8_BP2A_CH0    W+2/RB1out/8 Backward;    TBp2_6 17, TBp2_7 0, 
17 1   LB_RB+2_S8_BP2A_CH1    W+2/RB2in/8 Backward;  
17 2   LB_RB+2_S8_BP2A_CH2    W+2/RB1in/8 Backward;  

TBp3_6
0 0
0 1
0 2
5 0   LB_RE+1_S7_EP22_CH0    RE+2/2/21;    TBp3_6 5, 
5 1   LB_RE+1_S7_EP22_CH1    RE+2/2/20;  
5 2   LB_RE+1_S7_EP22_CH2    RE+2/2/22;  
6 0   LB_RE+1_S7_EP23_CH0    RE+2/3/21;    TBp2_6 6, TBp3_6 6, 
6 1   LB_RE+1_S7_EP23_CH1    RE+2/3/20;  
6 2   LB_RE+1_S7_EP23_CH2    RE+2/3/22;  
8 0   LB_RE+4_S7_EP43_CH0    RE+4/3/19;    TBp3_5 11, TBp3_6 8, 
8 1   LB_RE+4_S7_EP43_CH1    RE+4/3/18;  
8 2   LB_RE+4_S7_EP43_CH2    RE+4/3/20;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE+4_S8_EP43_CH0    RE+4/3/22;    TBp3_6 11, TBp3_7 8, 
11 1   LB_RE+4_S8_EP43_CH1    RE+4/3/21;  
11 2   LB_RE+4_S8_EP43_CH2    RE+4/3/23;  
12 0   LB_RE+4_S8_EP42_CH0    RE+4/2/22;    TBp3_6 12, TBp3_7 7, 
12 1   LB_RE+4_S8_EP42_CH1    RE+4/2/21;  
12 2   LB_RE+4_S8_EP42_CH2    RE+4/2/23;  
13 0   LB_RE+3_S8_EP33_CH0    RE+3/3/22;    TBp3_6 13, TBp3_7 4, 
13 1   LB_RE+3_S8_EP33_CH1    RE+3/3/21;  
13 2   LB_RE+3_S8_EP33_CH2    RE+3/3/23;  
14 0   LB_RE+3_S8_EP32_CH0    RE+3/2/22;    TBp3_6 14, TBp3_7 3, 
14 1   LB_RE+3_S8_EP32_CH1    RE+3/2/21;  
14 2   LB_RE+3_S8_EP32_CH2    RE+3/2/23;  
15 0   LB_RE+1_S8_EP12_CH0    RE+1/2/22;    TBp3_6 15, TBp3_7 2, 
15 1   LB_RE+1_S8_EP12_CH1    RE+1/2/21;  
15 2   LB_RE+1_S8_EP12_CH2    RE+1/2/23;  
16 0   LB_RB+2_S8_BP2B_CH0    W+2/RB1out/8 Forward;    TBp2_6 16, TBp3_6 16, TBp2_7 1, TBp3_7 1, 
16 1   LB_RB+2_S8_BP2B_CH1    W+2/RB2in/8 Forward;  
16 2   LB_RB+2_S8_BP2B_CH2    W+2/RB1in/8 Forward;  
17 0
17 1
17 2

TC_7
TBn3_7
0 0
0 1
0 2
1 0   LB_RB-2_S8_BN2B_CH0    W-2/RB1out/8 Forward;    TBn3_6 16, TBn2_6 16, TBn3_7 1, TBn2_7 1, 
1 1   LB_RB-2_S8_BN2B_CH1    W-2/RB2in/8 Forward;  
1 2   LB_RB-2_S8_BN2B_CH2    W-2/RB1in/8 Forward;  
2 0   LB_RE-1_S8_EN12_CH0    RE-1/2/22;    TBn3_6 15, TBn3_7 2, 
2 1   LB_RE-1_S8_EN12_CH1    RE-1/2/21;  
2 2   LB_RE-1_S8_EN12_CH2    RE-1/2/23;  
3 0   LB_RE-3_S8_EN32_CH0    RE-3/2/22;    TBn3_6 14, TBn3_7 3, 
3 1   LB_RE-3_S8_EN32_CH1    RE-3/2/21;  
3 2   LB_RE-3_S8_EN32_CH2    RE-3/2/23;  
4 0   LB_RE-3_S8_EN33_CH0    RE-3/3/22;    TBn3_6 13, TBn3_7 4, 
4 1   LB_RE-3_S8_EN33_CH1    RE-3/3/21;  
4 2   LB_RE-3_S8_EN33_CH2    RE-3/3/23;  
5 0   LB_RE-1_S8_EN22_CH0    RE-2/2/24;    TBn3_7 5, 
5 1   LB_RE-1_S8_EN22_CH1    RE-2/2/23;  
5 2   LB_RE-1_S8_EN22_CH2    RE-2/2/25;  
6 0   LB_RE-1_S8_EN23_CH0    RE-2/3/24;    TBn3_7 6, TBn2_7 6, 
6 1   LB_RE-1_S8_EN23_CH1    RE-2/3/23;  
6 2   LB_RE-1_S8_EN23_CH2    RE-2/3/25;  
7 0   LB_RE-4_S8_EN42_CH0    RE-4/2/22;    TBn3_6 12, TBn3_7 7, 
7 1   LB_RE-4_S8_EN42_CH1    RE-4/2/21;  
7 2   LB_RE-4_S8_EN42_CH2    RE-4/2/23;  
8 0   LB_RE-4_S8_EN43_CH0    RE-4/3/22;    TBn3_6 11, TBn3_7 8, 
8 1   LB_RE-4_S8_EN43_CH1    RE-4/3/21;  
8 2   LB_RE-4_S8_EN43_CH2    RE-4/3/23;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE-4_S9_EN43_CH0    RE-4/3/25;    TBn3_7 11, TBn3_8 8, 
11 1   LB_RE-4_S9_EN43_CH1    RE-4/3/24;  
11 2   LB_RE-4_S9_EN43_CH2    RE-4/3/26;  
12 0   LB_RE-4_S9_EN42_CH0    RE-4/2/25;    TBn3_7 12, 
12 1   LB_RE-4_S9_EN42_CH1    RE-4/2/24;  
12 2   LB_RE-4_S9_EN42_CH2    RE-4/2/26;  
13 0   LB_RE-3_S9_EN33_CH0    RE-3/3/25;    TBn3_7 13, 
13 1   LB_RE-3_S9_EN33_CH1    RE-3/3/24;  
13 2   LB_RE-3_S9_EN33_CH2    RE-3/3/26;  
14 0   LB_RE-3_S9_EN32_CH0    RE-3/2/25;    TBn3_7 14, 
14 1   LB_RE-3_S9_EN32_CH1    RE-3/2/24;  
14 2   LB_RE-3_S9_EN32_CH2    RE-3/2/26;  
15 0   LB_RE-1_S9_EN12_CH0    RE-1/2/25;    TBn3_7 15, 
15 1   LB_RE-1_S9_EN12_CH1    RE-1/2/24;  
15 2   LB_RE-1_S9_EN12_CH2    RE-1/2/26;  
16 0   LB_RB-2_S9_BN2B_CH0    W-2/RB1out/9 Forward;    TBn3_7 16, TBn2_7 16, TBn3_8 1, 
16 1   LB_RB-2_S9_BN2B_CH1    W-2/RB2in/9 Forward;  
16 2   LB_RB-2_S9_BN2B_CH2    W-2/RB1in/9 Forward;  
17 0
17 1
17 2

TBn2_7
0 0   LB_RB-2_S8_BN2A_CH0    W-2/RB1out/8 Backward;    TBn2_6 17, TBn2_7 0, 
0 1   LB_RB-2_S8_BN2A_CH1    W-2/RB2in/8 Backward;  
0 2   LB_RB-2_S8_BN2A_CH2    W-2/RB1in/8 Backward;  
1 0   LB_RB-2_S8_BN2B_CH0    W-2/RB1out/8 Forward;    TBn3_6 16, TBn2_6 16, TBn3_7 1, TBn2_7 1, 
1 1   LB_RB-2_S8_BN2B_CH1    W-2/RB2in/8 Forward;  
1 2   LB_RB-2_S8_BN2B_CH2    W-2/RB1in/8 Forward;  
2 0   LB_RB-2_S8_BN2C_CH0    W-2/RB2out/8 Central;    TBn2_6 15, TBn2_7 2, 
2 1   LB_RB-2_S8_BN2C_CH1    W-2/RB2out/8 Forward;  
2 2   LB_RB-2_S8_BN2C_CH2    W-2/RB2out/8 Backward;  
3 0   LB_RB-2_S8_BN2D_CH0    W-2/RB4/8+ Backward;  W-2/RB4/8- Backward;    TBn2_6 14, TBn1_6 12, TBn2_7 3, 
3 1
3 2   LB_RB-2_S8_BN2D_CH2    W-2/RB3/8+ Backward;  W-2/RB3/8- Backward;  
4 0   LB_RB-2_S8_BN2E_CH0    W-2/RB4/8+ Forward;  W-2/RB4/8- Forward;    TBn2_7 4, TBn1_7 6, 
4 1
4 2   LB_RB-2_S8_BN2E_CH2    W-2/RB3/8+ Forward;  W-2/RB3/8- Forward;  
5 0   LB_RB-1_S8_BN1B_CH0    W-1/RB1out/8 Forward;    TBn2_6 12, TBn2_7 5, TBn1_6 16, 
5 1   LB_RB-1_S8_BN1B_CH1    W-1/RB2out/8 Forward;  
5 2   LB_RB-1_S8_BN1B_CH2    W-1/RB1in/8 Forward;  
6 0   LB_RE-1_S8_EN23_CH0    RE-2/3/24;    TBn3_7 6, TBn2_7 6, 
6 1   LB_RE-1_S8_EN23_CH1    RE-2/3/23;  
6 2   LB_RE-1_S8_EN23_CH2    RE-2/3/25;  
7 0   LB_RE-1_S8_EN13_CH0    RE-1/3/22;    TBn2_6 10, TBn2_7 7, 
7 1   LB_RE-1_S8_EN13_CH1    RE-1/3/21;  
7 2   LB_RE-1_S8_EN13_CH2    RE-1/3/23;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE-1_S9_EN13_CH0    RE-1/3/25;    TBn2_7 10, TBn2_8 7, 
10 1   LB_RE-1_S9_EN13_CH1    RE-1/3/24;  
10 2   LB_RE-1_S9_EN13_CH2    RE-1/3/26;  
11 0
11 1
11 2
12 0   LB_RB-1_S9_BN1B_CH0    W-1/RB1out/9 Forward;    TBn2_7 12, TBn2_8 5, TBn1_7 16, TBn1_8 1, 
12 1   LB_RB-1_S9_BN1B_CH1    W-1/RB2out/9 Forward;  
12 2   LB_RB-1_S9_BN1B_CH2    W-1/RB1in/9 Forward;  
13 0   LB_RB-2_S9_BN2E_CH0    W-2/RB4/9 Forward;    TBn2_7 13, TBn1_7 11, TBn2_8 4, 
13 1
13 2   LB_RB-2_S9_BN2E_CH2    W-2/RB3/9+ Forward;  W-2/RB3/9- Forward;  
14 0   LB_RB-2_S9_BN2D_CH0    W-2/RB4/9 Backward;    TBn2_7 14, TBn1_7 12, TBn2_8 3, TBn1_8 5, 
14 1
14 2   LB_RB-2_S9_BN2D_CH2    W-2/RB3/9+ Backward;  W-2/RB3/9- Backward;  
15 0   LB_RB-2_S9_BN2C_CH0    W-2/RB2out/9 Central;    TBn2_7 15, TBn2_8 2, 
15 1   LB_RB-2_S9_BN2C_CH1    W-2/RB2out/9 Forward;  
15 2   LB_RB-2_S9_BN2C_CH2    W-2/RB2out/9 Backward;  
16 0   LB_RB-2_S9_BN2B_CH0    W-2/RB1out/9 Forward;    TBn3_7 16, TBn2_7 16, TBn3_8 1, 
16 1   LB_RB-2_S9_BN2B_CH1    W-2/RB2in/9 Forward;  
16 2   LB_RB-2_S9_BN2B_CH2    W-2/RB1in/9 Forward;  
17 0   LB_RB-2_S9_BN2A_CH0    W-2/RB1out/9 Backward;    TBn2_7 17, TBn2_8 0, 
17 1   LB_RB-2_S9_BN2A_CH1    W-2/RB2in/9 Backward;  
17 2   LB_RB-2_S9_BN2A_CH2    W-2/RB1in/9 Backward;  

TBn1_7
3 0   LB_RB-1_S8_BN1D_CH0    W-1/RB4/8+ Backward;  W-1/RB4/8- Backward;    TBn1_6 14, TB0_6 12, TBn1_7 3, 
3 1
3 2   LB_RB-1_S8_BN1D_CH2    W-1/RB3/8+ Backward;  W-1/RB3/8- Backward;  
6 0   LB_RB-2_S8_BN2E_CH0    W-2/RB4/8+ Forward;  W-2/RB4/8- Forward;    TBn2_7 4, TBn1_7 6, 
6 1
6 2   LB_RB-2_S8_BN2E_CH2    W-2/RB3/8+ Forward;  W-2/RB3/8- Forward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S9_BN0B_CH0    W0/RB1out/9 Forward;    TB0_7 17, TB0_8 0, TBn1_7 10, 
10 1   LB_RB0_S9_BN0B_CH1    W0/RB2out/9 Forward;  
10 2   LB_RB0_S9_BN0B_CH2    W0/RB1in/9 Forward;  
11 0   LB_RB-2_S9_BN2E_CH0    W-2/RB4/9 Forward;    TBn2_7 13, TBn1_7 11, TBn2_8 4, 
11 1
11 2   LB_RB-2_S9_BN2E_CH2    W-2/RB3/9+ Forward;  W-2/RB3/9- Forward;  
12 0   LB_RB-2_S9_BN2D_CH0    W-2/RB4/9 Backward;    TBn2_7 14, TBn1_7 12, TBn2_8 3, TBn1_8 5, 
12 1
12 2   LB_RB-2_S9_BN2D_CH2    W-2/RB3/9+ Backward;  W-2/RB3/9- Backward;  
13 0   LB_RB-1_S9_BN1E_CH0    W-1/RB4/9 Forward;    TBn1_7 13, TBn1_8 4, 
13 1
13 2   LB_RB-1_S9_BN1E_CH2    W-1/RB3/9+ Forward;  W-1/RB3/9- Forward;  
14 0   LB_RB-1_S9_BN1D_CH0    W-1/RB4/9 Backward;    TBn1_7 14, TB0_7 12, TBn1_8 3, TB0_8 5, 
14 1
14 2   LB_RB-1_S9_BN1D_CH2    W-1/RB3/9+ Backward;  W-1/RB3/9- Backward;  
15 0   LB_RB-1_S9_BN1C_CH0    W-1/RB2in/9 Central;    TBn1_7 15, TBn1_8 2, 
15 1   LB_RB-1_S9_BN1C_CH1    W-1/RB2in/9 Forward;  
15 2   LB_RB-1_S9_BN1C_CH2    W-1/RB2in/9 Backward;  
16 0   LB_RB-1_S9_BN1B_CH0    W-1/RB1out/9 Forward;    TBn2_7 12, TBn2_8 5, TBn1_7 16, TBn1_8 1, 
16 1   LB_RB-1_S9_BN1B_CH1    W-1/RB2out/9 Forward;  
16 2   LB_RB-1_S9_BN1B_CH2    W-1/RB1in/9 Forward;  
17 0   LB_RB-1_S9_BN1A_CH0    W-1/RB1out/9 Backward;    TBn1_7 17, TBn1_8 0, 
17 1   LB_RB-1_S9_BN1A_CH1    W-1/RB2out/9 Backward;  
17 2   LB_RB-1_S9_BN1A_CH2    W-1/RB1in/9 Backward;  

TB0_7
0 0   LB_RB0_S8_BN0B_CH0    W0/RB1out/8 Forward;    TB0_6 17, TB0_7 0, TBn1_6 10, 
0 1   LB_RB0_S8_BN0B_CH1    W0/RB2out/8 Forward;  
0 2   LB_RB0_S8_BN0B_CH2    W0/RB1in/8 Forward;  
1 0   LB_RB0_S8_BP0B_CH0    W0/RB1out/8 Backward;    TB0_6 16, TB0_7 1, TBp1_6 10, 
1 1   LB_RB0_S8_BP0B_CH1    W0/RB2out/8 Backward;  
1 2   LB_RB0_S8_BP0B_CH2    W0/RB1in/8 Backward;  
6 0   LB_RB+1_S8_BP1D_CH0    W+1/RB4/8+ Backward;  W+1/RB4/8- Backward;    TB0_6 11, TBp1_6 14, TB0_7 6, 
6 1
6 2   LB_RB+1_S8_BP1D_CH2    W+1/RB3/8+ Backward;  W+1/RB3/8- Backward;  
7 0
7 1
7 2
8 0
8 1
8 2
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RB+1_S9_BP1D_CH0    W+1/RB4/9 Backward;    TBp1_8 3, TB0_7 11, TBp1_7 14, 
11 1
11 2   LB_RB+1_S9_BP1D_CH2    W+1/RB3/9+ Backward;  W+1/RB3/9- Backward;  
12 0   LB_RB-1_S9_BN1D_CH0    W-1/RB4/9 Backward;    TBn1_7 14, TB0_7 12, TBn1_8 3, TB0_8 5, 
12 1
12 2   LB_RB-1_S9_BN1D_CH2    W-1/RB3/9+ Backward;  W-1/RB3/9- Backward;  
13 0   LB_RB0_S9_BP0E_CH0    W0/RB4/9 Backward;    TB0_7 13, 
13 1
13 2   LB_RB0_S9_BP0E_CH2    W0/RB3/9+ Backward;  W0/RB3/9- Backward;  
14 0   LB_RB0_S9_BN0E_CH0    W0/RB4/9 Forward;    TB0_7 14, 
14 1
14 2   LB_RB0_S9_BN0E_CH2    W0/RB3/9+ Forward;  W0/RB3/9- Forward;  
15 0   LB_RB0_S9_BM0C_CH0    W0/RB2in/9 Central;    TB0_7 15, 
15 1   LB_RB0_S9_BM0C_CH1    W0/RB2in/9 Forward;  
15 2   LB_RB0_S9_BM0C_CH2    W0/RB2in/9 Backward;  
16 0   LB_RB0_S9_BP0B_CH0    W0/RB1out/9 Backward;    TB0_7 16, TB0_8 1, TBp1_7 10, 
16 1   LB_RB0_S9_BP0B_CH1    W0/RB2out/9 Backward;  
16 2   LB_RB0_S9_BP0B_CH2    W0/RB1in/9 Backward;  
17 0   LB_RB0_S9_BN0B_CH0    W0/RB1out/9 Forward;    TB0_7 17, TB0_8 0, TBn1_7 10, 
17 1   LB_RB0_S9_BN0B_CH1    W0/RB2out/9 Forward;  
17 2   LB_RB0_S9_BN0B_CH2    W0/RB1in/9 Forward;  

TBp1_7
1 0   LB_RB+1_S8_BP1B_CH0    W+1/RB1out/8 Forward;    TBp1_6 16, TBp2_6 12, TBp1_7 1, 
1 1   LB_RB+1_S8_BP1B_CH1    W+1/RB2out/8 Forward;  
1 2   LB_RB+1_S8_BP1B_CH2    W+1/RB1in/8 Forward;  
5 0   LB_RB+2_S8_BP2D_CH0    W+2/RB4/8+ Backward;  W+2/RB4/8- Backward;    TBp1_6 12, TBp2_6 14, TBp1_7 5, TBp2_7 3, 
5 1
5 2   LB_RB+2_S8_BP2D_CH2    W+2/RB3/8+ Backward;  W+2/RB3/8- Backward;  
6 0   LB_RB+2_S8_BP2E_CH0    W+2/RB4/8+ Forward;  W+2/RB4/8- Forward;    TBp1_6 11, TBp2_6 13, TBp1_7 6, TBp2_7 4, 
6 1
6 2   LB_RB+2_S8_BP2E_CH2    W+2/RB3/8+ Forward;  W+2/RB3/8- Forward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S9_BP0B_CH0    W0/RB1out/9 Backward;    TB0_7 16, TB0_8 1, TBp1_7 10, 
10 1   LB_RB0_S9_BP0B_CH1    W0/RB2out/9 Backward;  
10 2   LB_RB0_S9_BP0B_CH2    W0/RB1in/9 Backward;  
12 0   LB_RB+2_S9_BP2D_CH0    W+2/RB4/9 Backward;    TBp1_8 5, TBp2_8 3, TBp1_7 12, 
12 1
12 2   LB_RB+2_S9_BP2D_CH2    W+2/RB3/9+ Backward;  W+2/RB3/9- Backward;  
14 0   LB_RB+1_S9_BP1D_CH0    W+1/RB4/9 Backward;    TBp1_8 3, TB0_7 11, TBp1_7 14, 
14 1
14 2   LB_RB+1_S9_BP1D_CH2    W+1/RB3/9+ Backward;  W+1/RB3/9- Backward;  
16 0   LB_RB+1_S9_BP1B_CH0    W+1/RB1out/9 Forward;    TBp1_8 1, TBp2_8 5, TBp1_7 16, 
16 1   LB_RB+1_S9_BP1B_CH1    W+1/RB2out/9 Forward;  
16 2   LB_RB+1_S9_BP1B_CH2    W+1/RB1in/9 Forward;  

TBp2_7
0 0   LB_RB+2_S8_BP2A_CH0    W+2/RB1out/8 Backward;    TBp2_6 17, TBp2_7 0, 
0 1   LB_RB+2_S8_BP2A_CH1    W+2/RB2in/8 Backward;  
0 2   LB_RB+2_S8_BP2A_CH2    W+2/RB1in/8 Backward;  
1 0   LB_RB+2_S8_BP2B_CH0    W+2/RB1out/8 Forward;    TBp2_6 16, TBp3_6 16, TBp2_7 1, TBp3_7 1, 
1 1   LB_RB+2_S8_BP2B_CH1    W+2/RB2in/8 Forward;  
1 2   LB_RB+2_S8_BP2B_CH2    W+2/RB1in/8 Forward;  
2 0   LB_RB+2_S8_BP2C_CH0    W+2/RB2out/8 Central;    TBp2_6 15, TBp2_7 2, 
2 1   LB_RB+2_S8_BP2C_CH1    W+2/RB2out/8 Forward;  
2 2   LB_RB+2_S8_BP2C_CH2    W+2/RB2out/8 Backward;  
3 0   LB_RB+2_S8_BP2D_CH0    W+2/RB4/8+ Backward;  W+2/RB4/8- Backward;    TBp1_6 12, TBp2_6 14, TBp1_7 5, TBp2_7 3, 
3 1
3 2   LB_RB+2_S8_BP2D_CH2    W+2/RB3/8+ Backward;  W+2/RB3/8- Backward;  
4 0   LB_RB+2_S8_BP2E_CH0    W+2/RB4/8+ Forward;  W+2/RB4/8- Forward;    TBp1_6 11, TBp2_6 13, TBp1_7 6, TBp2_7 4, 
4 1
4 2   LB_RB+2_S8_BP2E_CH2    W+2/RB3/8+ Forward;  W+2/RB3/8- Forward;  
6 0   LB_RE+1_S8_EP23_CH0    RE+2/3/24;    TBp3_7 6, TBp2_7 6, 
6 1   LB_RE+1_S8_EP23_CH1    RE+2/3/23;  
6 2   LB_RE+1_S8_EP23_CH2    RE+2/3/25;  
7 0   LB_RE+1_S8_EP13_CH0    RE+1/3/22;    TBp2_6 10, TBp2_7 7, 
7 1   LB_RE+1_S8_EP13_CH1    RE+1/3/21;  
7 2   LB_RE+1_S8_EP13_CH2    RE+1/3/23;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE+1_S9_EP13_CH0    RE+1/3/25;    TBp2_8 7, TBp2_7 10, 
10 1   LB_RE+1_S9_EP13_CH1    RE+1/3/24;  
10 2   LB_RE+1_S9_EP13_CH2    RE+1/3/26;  
11 0
11 1
11 2

TBp3_7
0 0
0 1
0 2
1 0   LB_RB+2_S8_BP2B_CH0    W+2/RB1out/8 Forward;    TBp2_6 16, TBp3_6 16, TBp2_7 1, TBp3_7 1, 
1 1   LB_RB+2_S8_BP2B_CH1    W+2/RB2in/8 Forward;  
1 2   LB_RB+2_S8_BP2B_CH2    W+2/RB1in/8 Forward;  
2 0   LB_RE+1_S8_EP12_CH0    RE+1/2/22;    TBp3_6 15, TBp3_7 2, 
2 1   LB_RE+1_S8_EP12_CH1    RE+1/2/21;  
2 2   LB_RE+1_S8_EP12_CH2    RE+1/2/23;  
3 0   LB_RE+3_S8_EP32_CH0    RE+3/2/22;    TBp3_6 14, TBp3_7 3, 
3 1   LB_RE+3_S8_EP32_CH1    RE+3/2/21;  
3 2   LB_RE+3_S8_EP32_CH2    RE+3/2/23;  
4 0   LB_RE+3_S8_EP33_CH0    RE+3/3/22;    TBp3_6 13, TBp3_7 4, 
4 1   LB_RE+3_S8_EP33_CH1    RE+3/3/21;  
4 2   LB_RE+3_S8_EP33_CH2    RE+3/3/23;  
5 0   LB_RE+1_S8_EP22_CH0    RE+2/2/24;    TBp3_7 5, 
5 1   LB_RE+1_S8_EP22_CH1    RE+2/2/23;  
5 2   LB_RE+1_S8_EP22_CH2    RE+2/2/25;  
6 0   LB_RE+1_S8_EP23_CH0    RE+2/3/24;    TBp3_7 6, TBp2_7 6, 
6 1   LB_RE+1_S8_EP23_CH1    RE+2/3/23;  
6 2   LB_RE+1_S8_EP23_CH2    RE+2/3/25;  
7 0   LB_RE+4_S8_EP42_CH0    RE+4/2/22;    TBp3_6 12, TBp3_7 7, 
7 1   LB_RE+4_S8_EP42_CH1    RE+4/2/21;  
7 2   LB_RE+4_S8_EP42_CH2    RE+4/2/23;  
8 0   LB_RE+4_S8_EP43_CH0    RE+4/3/22;    TBp3_6 11, TBp3_7 8, 
8 1   LB_RE+4_S8_EP43_CH1    RE+4/3/21;  
8 2   LB_RE+4_S8_EP43_CH2    RE+4/3/23;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE+4_S9_EP43_CH0    RE+4/3/25;    TBp3_7 11, TBp3_8 8, 
11 1   LB_RE+4_S9_EP43_CH1    RE+4/3/24;  
11 2   LB_RE+4_S9_EP43_CH2    RE+4/3/26;  
12 0   LB_RE+4_S9_EP42_CH0    RE+4/2/25;    TBp3_7 12, 
12 1   LB_RE+4_S9_EP42_CH1    RE+4/2/24;  
12 2   LB_RE+4_S9_EP42_CH2    RE+4/2/26;  
13 0   LB_RE+3_S9_EP33_CH0    RE+3/3/25;    TBp3_7 13, 
13 1   LB_RE+3_S9_EP33_CH1    RE+3/3/24;  
13 2   LB_RE+3_S9_EP33_CH2    RE+3/3/26;  
14 0   LB_RE+3_S9_EP32_CH0    RE+3/2/25;    TBp3_7 14, 
14 1   LB_RE+3_S9_EP32_CH1    RE+3/2/24;  
14 2   LB_RE+3_S9_EP32_CH2    RE+3/2/26;  
17 0
17 1
17 2

TC_8
TBn3_8
0 0
0 1
0 2
1 0   LB_RB-2_S9_BN2B_CH0    W-2/RB1out/9 Forward;    TBn3_7 16, TBn2_7 16, TBn3_8 1, 
1 1   LB_RB-2_S9_BN2B_CH1    W-2/RB2in/9 Forward;  
1 2   LB_RB-2_S9_BN2B_CH2    W-2/RB1in/9 Forward;  
5 0   LB_RE-1_S9_EN22_CH0    RE-2/2/27;    TBn3_8 5, 
5 1   LB_RE-1_S9_EN22_CH1    RE-2/2/26;  
5 2   LB_RE-1_S9_EN22_CH2    RE-2/2/28;  
6 0   LB_RE-1_S9_EN23_CH0    RE-2/3/27;    TBn3_8 6, TBn2_8 6, 
6 1   LB_RE-1_S9_EN23_CH1    RE-2/3/26;  
6 2   LB_RE-1_S9_EN23_CH2    RE-2/3/28;  
8 0   LB_RE-4_S9_EN43_CH0    RE-4/3/25;    TBn3_7 11, TBn3_8 8, 
8 1   LB_RE-4_S9_EN43_CH1    RE-4/3/24;  
8 2   LB_RE-4_S9_EN43_CH2    RE-4/3/26;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE-4_S10_EN43_CH0    RE-4/3/28;    TBn3_8 11, TBn3_9 8, 
11 1   LB_RE-4_S10_EN43_CH1    RE-4/3/27;  
11 2   LB_RE-4_S10_EN43_CH2    RE-4/3/29;  
12 0   LB_RE-4_S10_EN42_CH0    RE-4/2/28;    TBn3_8 12, TBn3_9 7, 
12 1   LB_RE-4_S10_EN42_CH1    RE-4/2/27;  
12 2   LB_RE-4_S10_EN42_CH2    RE-4/2/29;  
13 0   LB_RE-3_S10_EN33_CH0    RE-3/3/28;    TBn3_8 13, TBn3_9 4, 
13 1   LB_RE-3_S10_EN33_CH1    RE-3/3/27;  
13 2   LB_RE-3_S10_EN33_CH2    RE-3/3/29;  
14 0   LB_RE-3_S10_EN32_CH0    RE-3/2/28;    TBn3_8 14, TBn3_9 3, 
14 1   LB_RE-3_S10_EN32_CH1    RE-3/2/27;  
14 2   LB_RE-3_S10_EN32_CH2    RE-3/2/29;  
15 0   LB_RE-1_S10_EN12_CH0    RE-1/2/28;    TBn3_9 2, TBn3_8 15, 
15 1   LB_RE-1_S10_EN12_CH1    RE-1/2/27;  
15 2   LB_RE-1_S10_EN12_CH2    RE-1/2/29;  
16 0   LB_RB-2_S10_BN2B_CH0    W-2/RB1out/10 Forward;    TBn3_9 1, TBn2_9 1, TBn3_8 16, TBn2_8 16, 
16 1   LB_RB-2_S10_BN2B_CH1    W-2/RB2in/10 Forward;  
16 2   LB_RB-2_S10_BN2B_CH2    W-2/RB1in/10 Forward;  
17 0
17 1
17 2

TBn2_8
0 0   LB_RB-2_S9_BN2A_CH0    W-2/RB1out/9 Backward;    TBn2_7 17, TBn2_8 0, 
0 1   LB_RB-2_S9_BN2A_CH1    W-2/RB2in/9 Backward;  
0 2   LB_RB-2_S9_BN2A_CH2    W-2/RB1in/9 Backward;  
2 0   LB_RB-2_S9_BN2C_CH0    W-2/RB2out/9 Central;    TBn2_7 15, TBn2_8 2, 
2 1   LB_RB-2_S9_BN2C_CH1    W-2/RB2out/9 Forward;  
2 2   LB_RB-2_S9_BN2C_CH2    W-2/RB2out/9 Backward;  
3 0   LB_RB-2_S9_BN2D_CH0    W-2/RB4/9 Backward;    TBn2_7 14, TBn1_7 12, TBn2_8 3, TBn1_8 5, 
3 1
3 2   LB_RB-2_S9_BN2D_CH2    W-2/RB3/9+ Backward;  W-2/RB3/9- Backward;  
4 0   LB_RB-2_S9_BN2E_CH0    W-2/RB4/9 Forward;    TBn2_7 13, TBn1_7 11, TBn2_8 4, 
4 1
4 2   LB_RB-2_S9_BN2E_CH2    W-2/RB3/9+ Forward;  W-2/RB3/9- Forward;  
5 0   LB_RB-1_S9_BN1B_CH0    W-1/RB1out/9 Forward;    TBn2_7 12, TBn2_8 5, TBn1_7 16, TBn1_8 1, 
5 1   LB_RB-1_S9_BN1B_CH1    W-1/RB2out/9 Forward;  
5 2   LB_RB-1_S9_BN1B_CH2    W-1/RB1in/9 Forward;  
6 0   LB_RE-1_S9_EN23_CH0    RE-2/3/27;    TBn3_8 6, TBn2_8 6, 
6 1   LB_RE-1_S9_EN23_CH1    RE-2/3/26;  
6 2   LB_RE-1_S9_EN23_CH2    RE-2/3/28;  
7 0   LB_RE-1_S9_EN13_CH0    RE-1/3/25;    TBn2_7 10, TBn2_8 7, 
7 1   LB_RE-1_S9_EN13_CH1    RE-1/3/24;  
7 2   LB_RE-1_S9_EN13_CH2    RE-1/3/26;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE-1_S10_EN13_CH0    RE-1/3/28;    TBn2_9 7, TBn2_8 10, 
10 1   LB_RE-1_S10_EN13_CH1    RE-1/3/27;  
10 2   LB_RE-1_S10_EN13_CH2    RE-1/3/29;  
11 0
11 1
11 2
12 0   LB_RB-1_S10_BN1B_CH0    W-1/RB1out/10 Forward;    TBn1_9 1, TBn2_8 12, TBn2_9 5, TBn1_8 16, 
12 1   LB_RB-1_S10_BN1B_CH1    W-1/RB2out/10 Forward;  
12 2   LB_RB-1_S10_BN1B_CH2    W-1/RB1in/10 Forward;  
13 0   LB_RB-2_S10_BN2E_CH0    W-2/RB4/10- Forward;    TBn2_9 4, TBn1_9 6, TBn2_8 13, TBn1_8 11, 
13 1   LB_RB-2_S10_BN2E_CH1    W-2/RB4/10+ Forward;  
13 2   LB_RB-2_S10_BN2E_CH2    W-2/RB3/10+ Forward;  W-2/RB3/10- Forward;  
14 0   LB_RB-2_S10_BN2D_CH0    W-2/RB4/10- Backward;    TBn2_9 3, TBn1_9 5, TBn2_8 14, TBn1_8 12, 
14 1   LB_RB-2_S10_BN2D_CH1    W-2/RB4/10+ Backward;  
14 2   LB_RB-2_S10_BN2D_CH2    W-2/RB3/10+ Backward;  W-2/RB3/10- Backward;  
15 0   LB_RB-2_S10_BN2C_CH0    W-2/RB2out/10 Central;    TBn2_9 2, TBn2_8 15, 
15 1   LB_RB-2_S10_BN2C_CH1    W-2/RB2out/10 Forward;  
15 2   LB_RB-2_S10_BN2C_CH2    W-2/RB2out/10 Backward;  
16 0   LB_RB-2_S10_BN2B_CH0    W-2/RB1out/10 Forward;    TBn3_9 1, TBn2_9 1, TBn3_8 16, TBn2_8 16, 
16 1   LB_RB-2_S10_BN2B_CH1    W-2/RB2in/10 Forward;  
16 2   LB_RB-2_S10_BN2B_CH2    W-2/RB1in/10 Forward;  
17 0   LB_RB-2_S10_BN2A_CH0    W-2/RB1out/10 Backward;    TBn2_9 0, TBn2_8 17, 
17 1   LB_RB-2_S10_BN2A_CH1    W-2/RB2in/10 Backward;  
17 2   LB_RB-2_S10_BN2A_CH2    W-2/RB1in/10 Backward;  

TBn1_8
0 0   LB_RB-1_S9_BN1A_CH0    W-1/RB1out/9 Backward;    TBn1_7 17, TBn1_8 0, 
0 1   LB_RB-1_S9_BN1A_CH1    W-1/RB2out/9 Backward;  
0 2   LB_RB-1_S9_BN1A_CH2    W-1/RB1in/9 Backward;  
1 0   LB_RB-1_S9_BN1B_CH0    W-1/RB1out/9 Forward;    TBn2_7 12, TBn2_8 5, TBn1_7 16, TBn1_8 1, 
1 1   LB_RB-1_S9_BN1B_CH1    W-1/RB2out/9 Forward;  
1 2   LB_RB-1_S9_BN1B_CH2    W-1/RB1in/9 Forward;  
2 0   LB_RB-1_S9_BN1C_CH0    W-1/RB2in/9 Central;    TBn1_7 15, TBn1_8 2, 
2 1   LB_RB-1_S9_BN1C_CH1    W-1/RB2in/9 Forward;  
2 2   LB_RB-1_S9_BN1C_CH2    W-1/RB2in/9 Backward;  
3 0   LB_RB-1_S9_BN1D_CH0    W-1/RB4/9 Backward;    TBn1_7 14, TB0_7 12, TBn1_8 3, TB0_8 5, 
3 1
3 2   LB_RB-1_S9_BN1D_CH2    W-1/RB3/9+ Backward;  W-1/RB3/9- Backward;  
4 0   LB_RB-1_S9_BN1E_CH0    W-1/RB4/9 Forward;    TBn1_7 13, TBn1_8 4, 
4 1
4 2   LB_RB-1_S9_BN1E_CH2    W-1/RB3/9+ Forward;  W-1/RB3/9- Forward;  
5 0   LB_RB-2_S9_BN2D_CH0    W-2/RB4/9 Backward;    TBn2_7 14, TBn1_7 12, TBn2_8 3, TBn1_8 5, 
5 1
5 2   LB_RB-2_S9_BN2D_CH2    W-2/RB3/9+ Backward;  W-2/RB3/9- Backward;  
8 0
8 1
8 2
9 0
9 1
9 2
11 0   LB_RB-2_S10_BN2E_CH0    W-2/RB4/10- Forward;    TBn2_9 4, TBn1_9 6, TBn2_8 13, TBn1_8 11, 
11 1   LB_RB-2_S10_BN2E_CH1    W-2/RB4/10+ Forward;  
11 2   LB_RB-2_S10_BN2E_CH2    W-2/RB3/10+ Forward;  W-2/RB3/10- Forward;  
12 0   LB_RB-2_S10_BN2D_CH0    W-2/RB4/10- Backward;    TBn2_9 3, TBn1_9 5, TBn2_8 14, TBn1_8 12, 
12 1   LB_RB-2_S10_BN2D_CH1    W-2/RB4/10+ Backward;  
12 2   LB_RB-2_S10_BN2D_CH2    W-2/RB3/10+ Backward;  W-2/RB3/10- Backward;  
13 0   LB_RB-1_S10_BN1E_CH0    W-1/RB4/10- Forward;    TBn1_9 4, TBn1_8 13, 
13 1   LB_RB-1_S10_BN1E_CH1    W-1/RB4/10+ Forward;  
13 2   LB_RB-1_S10_BN1E_CH2    W-1/RB3/10+ Forward;  W-1/RB3/10- Forward;  
14 0   LB_RB-1_S10_BN1D_CH0    W-1/RB4/10- Backward;    TBn1_9 3, TB0_9 5, TBn1_8 14, TB0_8 12, 
14 1   LB_RB-1_S10_BN1D_CH1    W-1/RB4/10+ Backward;  
14 2   LB_RB-1_S10_BN1D_CH2    W-1/RB3/10+ Backward;  W-1/RB3/10- Backward;  
15 0   LB_RB-1_S10_BN1C_CH0    W-1/RB2in/10 Central;    TBn1_9 2, TBn1_8 15, 
15 1   LB_RB-1_S10_BN1C_CH1    W-1/RB2in/10 Forward;  
15 2   LB_RB-1_S10_BN1C_CH2    W-1/RB2in/10 Backward;  
16 0   LB_RB-1_S10_BN1B_CH0    W-1/RB1out/10 Forward;    TBn1_9 1, TBn2_8 12, TBn2_9 5, TBn1_8 16, 
16 1   LB_RB-1_S10_BN1B_CH1    W-1/RB2out/10 Forward;  
16 2   LB_RB-1_S10_BN1B_CH2    W-1/RB1in/10 Forward;  
17 0   LB_RB-1_S10_BN1A_CH0    W-1/RB1out/10 Backward;    TBn1_9 0, TBn1_8 17, 
17 1   LB_RB-1_S10_BN1A_CH1    W-1/RB2out/10 Backward;  
17 2   LB_RB-1_S10_BN1A_CH2    W-1/RB1in/10 Backward;  

TB0_8
0 0   LB_RB0_S9_BN0B_CH0    W0/RB1out/9 Forward;    TB0_7 17, TB0_8 0, TBn1_7 10, 
0 1   LB_RB0_S9_BN0B_CH1    W0/RB2out/9 Forward;  
0 2   LB_RB0_S9_BN0B_CH2    W0/RB1in/9 Forward;  
1 0   LB_RB0_S9_BP0B_CH0    W0/RB1out/9 Backward;    TB0_7 16, TB0_8 1, TBp1_7 10, 
1 1   LB_RB0_S9_BP0B_CH1    W0/RB2out/9 Backward;  
1 2   LB_RB0_S9_BP0B_CH2    W0/RB1in/9 Backward;  
5 0   LB_RB-1_S9_BN1D_CH0    W-1/RB4/9 Backward;    TBn1_7 14, TB0_7 12, TBn1_8 3, TB0_8 5, 
5 1
5 2   LB_RB-1_S9_BN1D_CH2    W-1/RB3/9+ Backward;  W-1/RB3/9- Backward;  
7 0
7 1
7 2
8 0
8 1
8 2
9 0
9 1
9 2
10 0
10 1
10 2
12 0   LB_RB-1_S10_BN1D_CH0    W-1/RB4/10- Backward;    TBn1_9 3, TB0_9 5, TBn1_8 14, TB0_8 12, 
12 1   LB_RB-1_S10_BN1D_CH1    W-1/RB4/10+ Backward;  
12 2   LB_RB-1_S10_BN1D_CH2    W-1/RB3/10+ Backward;  W-1/RB3/10- Backward;  
15 0   LB_RB0_S10_BM0C_CH0    W0/RB2in/10 Central;    TB0_8 15, 
15 1   LB_RB0_S10_BM0C_CH1    W0/RB2in/10 Forward;  
15 2   LB_RB0_S10_BM0C_CH2    W0/RB2in/10 Backward;  
16 0   LB_RB0_S10_BP0B_CH0    W0/RB1out/10 Backward;    TB0_8 16, TB0_9 1, TBn1_9 7, 
16 1   LB_RB0_S10_BP0B_CH1    W0/RB2out/10 Backward;  
16 2   LB_RB0_S10_BP0B_CH2    W0/RB1in/10 Backward;  
17 0   LB_RB0_S10_BN0B_CH0    W0/RB1out/10 Forward;    TB0_8 17, TB0_9 0, TBp1_9 7, 
17 1   LB_RB0_S10_BN0B_CH1    W0/RB2out/10 Forward;  
17 2   LB_RB0_S10_BN0B_CH2    W0/RB1in/10 Forward;  

TBp1_8
0 0   LB_RB+1_S9_BP1A_CH0    W+1/RB1out/9 Backward;    TBp1_8 0, 
0 1   LB_RB+1_S9_BP1A_CH1    W+1/RB2out/9 Backward;  
0 2   LB_RB+1_S9_BP1A_CH2    W+1/RB1in/9 Backward;  
1 0   LB_RB+1_S9_BP1B_CH0    W+1/RB1out/9 Forward;    TBp1_8 1, TBp2_8 5, TBp1_7 16, 
1 1   LB_RB+1_S9_BP1B_CH1    W+1/RB2out/9 Forward;  
1 2   LB_RB+1_S9_BP1B_CH2    W+1/RB1in/9 Forward;  
2 0   LB_RB+1_S9_BP1C_CH0    W+1/RB2in/9 Central;    TBp1_8 2, 
2 1   LB_RB+1_S9_BP1C_CH1    W+1/RB2in/9 Forward;  
2 2   LB_RB+1_S9_BP1C_CH2    W+1/RB2in/9 Backward;  
3 0   LB_RB+1_S9_BP1D_CH0    W+1/RB4/9 Backward;    TBp1_8 3, TB0_7 11, TBp1_7 14, 
3 1
3 2   LB_RB+1_S9_BP1D_CH2    W+1/RB3/9+ Backward;  W+1/RB3/9- Backward;  
4 0   LB_RB+1_S9_BP1E_CH0    W+1/RB4/9 Forward;    TBp1_8 4, 
4 1
4 2   LB_RB+1_S9_BP1E_CH2    W+1/RB3/9+ Forward;  W+1/RB3/9- Forward;  
5 0   LB_RB+2_S9_BP2D_CH0    W+2/RB4/9 Backward;    TBp1_8 5, TBp2_8 3, TBp1_7 12, 
5 1
5 2   LB_RB+2_S9_BP2D_CH2    W+2/RB3/9+ Backward;  W+2/RB3/9- Backward;  
6 0   LB_RB+2_S9_BP2E_CH0    W+2/RB4/9 Forward;    TBp1_8 6, TBp2_8 4, 
6 1
6 2   LB_RB+2_S9_BP2E_CH2    W+2/RB3/9+ Forward;  W+2/RB3/9- Forward;  
8 0
8 1
8 2
9 0
9 1
9 2
11 0   LB_RB+2_S10_BP2E_CH0    W+2/RB4/10+ Forward;    TBp1_8 11, TBp2_8 13, TBp1_9 6, TBp2_9 4, 
11 1   LB_RB+2_S10_BP2E_CH1    W+2/RB4/10- Forward;  
11 2   LB_RB+2_S10_BP2E_CH2    W+2/RB3/10+ Forward;  W+2/RB3/10- Forward;  
12 0   LB_RB+2_S10_BP2D_CH0    W+2/RB4/10+ Backward;    TBp1_8 12, TBp1_9 5, TBp2_9 3, 
12 1   LB_RB+2_S10_BP2D_CH1    W+2/RB4/10- Backward;  
12 2   LB_RB+2_S10_BP2D_CH2    W+2/RB3/10+ Backward;  W+2/RB3/10- Backward;  
13 0   LB_RB+1_S10_BP1E_CH0    W+1/RB4/10+ Forward;    TBp1_8 13, 
13 1   LB_RB+1_S10_BP1E_CH1    W+1/RB4/10- Forward;  
13 2   LB_RB+1_S10_BP1E_CH2    W+1/RB3/10+ Forward;  W+1/RB3/10- Forward;  
14 0   LB_RB+1_S10_BP1D_CH0    W+1/RB4/10+ Backward;    TBp1_8 14, TB0_9 6, TBp1_9 3, 
14 1   LB_RB+1_S10_BP1D_CH1    W+1/RB4/10- Backward;  
14 2   LB_RB+1_S10_BP1D_CH2    W+1/RB3/10+ Backward;  W+1/RB3/10- Backward;  
15 0   LB_RB+1_S10_BP1C_CH0    W+1/RB2in/10 Central;    TBp1_8 15, 
15 1   LB_RB+1_S10_BP1C_CH1    W+1/RB2in/10 Forward;  
15 2   LB_RB+1_S10_BP1C_CH2    W+1/RB2in/10 Backward;  
16 0   LB_RB+1_S10_BP1B_CH0    W+1/RB1out/10 Forward;    TBp1_8 16, TBp1_9 1, TBp2_9 5, 
16 1   LB_RB+1_S10_BP1B_CH1    W+1/RB2out/10 Forward;  
16 2   LB_RB+1_S10_BP1B_CH2    W+1/RB1in/10 Forward;  
17 0   LB_RB+1_S10_BP1A_CH0    W+1/RB1out/10 Backward;    TBp1_8 17, 
17 1   LB_RB+1_S10_BP1A_CH1    W+1/RB2out/10 Backward;  
17 2   LB_RB+1_S10_BP1A_CH2    W+1/RB1in/10 Backward;  

TBp2_8
0 0   LB_RB+2_S9_BP2A_CH0    W+2/RB1out/9 Backward;    TBp2_8 0, 
0 1   LB_RB+2_S9_BP2A_CH1    W+2/RB2in/9 Backward;  
0 2   LB_RB+2_S9_BP2A_CH2    W+2/RB1in/9 Backward;  
1 0   LB_RB+2_S9_BP2B_CH0    W+2/RB1out/9 Forward;    TBp2_8 1, TBp3_8 1, 
1 1   LB_RB+2_S9_BP2B_CH1    W+2/RB2in/9 Forward;  
1 2   LB_RB+2_S9_BP2B_CH2    W+2/RB1in/9 Forward;  
2 0   LB_RB+2_S9_BP2C_CH0    W+2/RB2out/9 Central;    TBp2_8 2, 
2 1   LB_RB+2_S9_BP2C_CH1    W+2/RB2out/9 Forward;  
2 2   LB_RB+2_S9_BP2C_CH2    W+2/RB2out/9 Backward;  
3 0   LB_RB+2_S9_BP2D_CH0    W+2/RB4/9 Backward;    TBp1_8 5, TBp2_8 3, TBp1_7 12, 
3 1
3 2   LB_RB+2_S9_BP2D_CH2    W+2/RB3/9+ Backward;  W+2/RB3/9- Backward;  
4 0   LB_RB+2_S9_BP2E_CH0    W+2/RB4/9 Forward;    TBp1_8 6, TBp2_8 4, 
4 1
4 2   LB_RB+2_S9_BP2E_CH2    W+2/RB3/9+ Forward;  W+2/RB3/9- Forward;  
5 0   LB_RB+1_S9_BP1B_CH0    W+1/RB1out/9 Forward;    TBp1_8 1, TBp2_8 5, TBp1_7 16, 
5 1   LB_RB+1_S9_BP1B_CH1    W+1/RB2out/9 Forward;  
5 2   LB_RB+1_S9_BP1B_CH2    W+1/RB1in/9 Forward;  
6 0   LB_RE+1_S9_EP23_CH0    RE+2/3/27;    TBp2_8 6, TBp3_8 6, 
6 1   LB_RE+1_S9_EP23_CH1    RE+2/3/26;  
6 2   LB_RE+1_S9_EP23_CH2    RE+2/3/28;  
7 0   LB_RE+1_S9_EP13_CH0    RE+1/3/25;    TBp2_8 7, TBp2_7 10, 
7 1   LB_RE+1_S9_EP13_CH1    RE+1/3/24;  
7 2   LB_RE+1_S9_EP13_CH2    RE+1/3/26;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE+1_S10_EP13_CH0    RE+1/3/28;    TBp2_8 10, TBp2_9 7, 
10 1   LB_RE+1_S10_EP13_CH1    RE+1/3/27;  
10 2   LB_RE+1_S10_EP13_CH2    RE+1/3/29;  
11 0
11 1
11 2
13 0   LB_RB+2_S10_BP2E_CH0    W+2/RB4/10+ Forward;    TBp1_8 11, TBp2_8 13, TBp1_9 6, TBp2_9 4, 
13 1   LB_RB+2_S10_BP2E_CH1    W+2/RB4/10- Forward;  
13 2   LB_RB+2_S10_BP2E_CH2    W+2/RB3/10+ Forward;  W+2/RB3/10- Forward;  
15 0   LB_RB+2_S10_BP2C_CH0    W+2/RB2out/10 Central;    TBp2_8 15, TBp2_9 2, 
15 1   LB_RB+2_S10_BP2C_CH1    W+2/RB2out/10 Forward;  
15 2   LB_RB+2_S10_BP2C_CH2    W+2/RB2out/10 Backward;  
16 0   LB_RB+2_S10_BP2B_CH0    W+2/RB1out/10 Forward;    TBp2_8 16, TBp3_8 16, TBp2_9 1, TBp3_9 1, 
16 1   LB_RB+2_S10_BP2B_CH1    W+2/RB2in/10 Forward;  
16 2   LB_RB+2_S10_BP2B_CH2    W+2/RB1in/10 Forward;  
17 0   LB_RB+2_S10_BP2A_CH0    W+2/RB1out/10 Backward;    TBp2_8 17, TBp2_9 0, 
17 1   LB_RB+2_S10_BP2A_CH1    W+2/RB2in/10 Backward;  
17 2   LB_RB+2_S10_BP2A_CH2    W+2/RB1in/10 Backward;  

TBp3_8
0 0
0 1
0 2
1 0   LB_RB+2_S9_BP2B_CH0    W+2/RB1out/9 Forward;    TBp2_8 1, TBp3_8 1, 
1 1   LB_RB+2_S9_BP2B_CH1    W+2/RB2in/9 Forward;  
1 2   LB_RB+2_S9_BP2B_CH2    W+2/RB1in/9 Forward;  
2 0   LB_RE+1_S9_EP12_CH0    RE+1/2/25;    TBp3_8 2, 
2 1   LB_RE+1_S9_EP12_CH1    RE+1/2/24;  
2 2   LB_RE+1_S9_EP12_CH2    RE+1/2/26;  
5 0   LB_RE+1_S9_EP22_CH0    RE+2/2/27;    TBp3_8 5, 
5 1   LB_RE+1_S9_EP22_CH1    RE+2/2/26;  
5 2   LB_RE+1_S9_EP22_CH2    RE+2/2/28;  
6 0   LB_RE+1_S9_EP23_CH0    RE+2/3/27;    TBp2_8 6, TBp3_8 6, 
6 1   LB_RE+1_S9_EP23_CH1    RE+2/3/26;  
6 2   LB_RE+1_S9_EP23_CH2    RE+2/3/28;  
8 0   LB_RE+4_S9_EP43_CH0    RE+4/3/25;    TBp3_7 11, TBp3_8 8, 
8 1   LB_RE+4_S9_EP43_CH1    RE+4/3/24;  
8 2   LB_RE+4_S9_EP43_CH2    RE+4/3/26;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE+4_S10_EP43_CH0    RE+4/3/28;    TBp3_8 11, TBp3_9 8, 
11 1   LB_RE+4_S10_EP43_CH1    RE+4/3/27;  
11 2   LB_RE+4_S10_EP43_CH2    RE+4/3/29;  
12 0   LB_RE+4_S10_EP42_CH0    RE+4/2/28;    TBp3_8 12, TBp3_9 7, 
12 1   LB_RE+4_S10_EP42_CH1    RE+4/2/27;  
12 2   LB_RE+4_S10_EP42_CH2    RE+4/2/29;  
13 0   LB_RE+3_S10_EP33_CH0    RE+3/3/28;    TBp3_8 13, TBp3_9 4, 
13 1   LB_RE+3_S10_EP33_CH1    RE+3/3/27;  
13 2   LB_RE+3_S10_EP33_CH2    RE+3/3/29;  
14 0   LB_RE+3_S10_EP32_CH0    RE+3/2/28;    TBp3_8 14, TBp3_9 3, 
14 1   LB_RE+3_S10_EP32_CH1    RE+3/2/27;  
14 2   LB_RE+3_S10_EP32_CH2    RE+3/2/29;  
15 0   LB_RE+1_S10_EP12_CH0    RE+1/2/28;    TBp3_8 15, TBp3_9 2, 
15 1   LB_RE+1_S10_EP12_CH1    RE+1/2/27;  
15 2   LB_RE+1_S10_EP12_CH2    RE+1/2/29;  
16 0   LB_RB+2_S10_BP2B_CH0    W+2/RB1out/10 Forward;    TBp2_8 16, TBp3_8 16, TBp2_9 1, TBp3_9 1, 
16 1   LB_RB+2_S10_BP2B_CH1    W+2/RB2in/10 Forward;  
16 2   LB_RB+2_S10_BP2B_CH2    W+2/RB1in/10 Forward;  
17 0
17 1
17 2

TC_9
TBn3_9
0 0
0 1
0 2
1 0   LB_RB-2_S10_BN2B_CH0    W-2/RB1out/10 Forward;    TBn3_9 1, TBn2_9 1, TBn3_8 16, TBn2_8 16, 
1 1   LB_RB-2_S10_BN2B_CH1    W-2/RB2in/10 Forward;  
1 2   LB_RB-2_S10_BN2B_CH2    W-2/RB1in/10 Forward;  
2 0   LB_RE-1_S10_EN12_CH0    RE-1/2/28;    TBn3_9 2, TBn3_8 15, 
2 1   LB_RE-1_S10_EN12_CH1    RE-1/2/27;  
2 2   LB_RE-1_S10_EN12_CH2    RE-1/2/29;  
3 0   LB_RE-3_S10_EN32_CH0    RE-3/2/28;    TBn3_8 14, TBn3_9 3, 
3 1   LB_RE-3_S10_EN32_CH1    RE-3/2/27;  
3 2   LB_RE-3_S10_EN32_CH2    RE-3/2/29;  
4 0   LB_RE-3_S10_EN33_CH0    RE-3/3/28;    TBn3_8 13, TBn3_9 4, 
4 1   LB_RE-3_S10_EN33_CH1    RE-3/3/27;  
4 2   LB_RE-3_S10_EN33_CH2    RE-3/3/29;  
5 0   LB_RE-1_S10_EN22_CH0    RE-2/2/30;    TBn3_9 5, 
5 1   LB_RE-1_S10_EN22_CH1    RE-2/2/29;  
5 2   LB_RE-1_S10_EN22_CH2    RE-2/2/31;  
6 0   LB_RE-1_S10_EN23_CH0    RE-2/3/30;    TBn3_9 6, TBn2_9 6, 
6 1   LB_RE-1_S10_EN23_CH1    RE-2/3/29;  
6 2   LB_RE-1_S10_EN23_CH2    RE-2/3/31;  
7 0   LB_RE-4_S10_EN42_CH0    RE-4/2/28;    TBn3_8 12, TBn3_9 7, 
7 1   LB_RE-4_S10_EN42_CH1    RE-4/2/27;  
7 2   LB_RE-4_S10_EN42_CH2    RE-4/2/29;  
8 0   LB_RE-4_S10_EN43_CH0    RE-4/3/28;    TBn3_8 11, TBn3_9 8, 
8 1   LB_RE-4_S10_EN43_CH1    RE-4/3/27;  
8 2   LB_RE-4_S10_EN43_CH2    RE-4/3/29;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE-4_S11_EN43_CH0    RE-4/3/31;    TBn3_9 11, TBn3_10 8, 
11 1   LB_RE-4_S11_EN43_CH1    RE-4/3/30;  
11 2   LB_RE-4_S11_EN43_CH2    RE-4/3/32;  
12 0   LB_RE-4_S11_EN42_CH0    RE-4/2/31;    TBn3_9 12, 
12 1   LB_RE-4_S11_EN42_CH1    RE-4/2/30;  
12 2   LB_RE-4_S11_EN42_CH2    RE-4/2/32;  
13 0   LB_RE-3_S11_EN33_CH0    RE-3/3/31;    TBn3_9 13, 
13 1   LB_RE-3_S11_EN33_CH1    RE-3/3/30;  
13 2   LB_RE-3_S11_EN33_CH2    RE-3/3/32;  
14 0   LB_RE-3_S11_EN32_CH0    RE-3/2/31;    TBn3_9 14, 
14 1   LB_RE-3_S11_EN32_CH1    RE-3/2/30;  
14 2   LB_RE-3_S11_EN32_CH2    RE-3/2/32;  
15 0   LB_RE-1_S11_EN12_CH0    RE-1/2/31;    TBn3_9 15, 
15 1   LB_RE-1_S11_EN12_CH1    RE-1/2/30;  
15 2   LB_RE-1_S11_EN12_CH2    RE-1/2/32;  
16 0   LB_RB-2_S11_BN2B_CH0    W-2/RB1out/11 Forward;    TBn3_9 16, TBn2_9 16, TBn3_10 1, 
16 1   LB_RB-2_S11_BN2B_CH1    W-2/RB2in/11 Forward;  
16 2   LB_RB-2_S11_BN2B_CH2    W-2/RB1in/11 Forward;  
17 0
17 1
17 2

TBn2_9
0 0   LB_RB-2_S10_BN2A_CH0    W-2/RB1out/10 Backward;    TBn2_9 0, TBn2_8 17, 
0 1   LB_RB-2_S10_BN2A_CH1    W-2/RB2in/10 Backward;  
0 2   LB_RB-2_S10_BN2A_CH2    W-2/RB1in/10 Backward;  
1 0   LB_RB-2_S10_BN2B_CH0    W-2/RB1out/10 Forward;    TBn3_9 1, TBn2_9 1, TBn3_8 16, TBn2_8 16, 
1 1   LB_RB-2_S10_BN2B_CH1    W-2/RB2in/10 Forward;  
1 2   LB_RB-2_S10_BN2B_CH2    W-2/RB1in/10 Forward;  
2 0   LB_RB-2_S10_BN2C_CH0    W-2/RB2out/10 Central;    TBn2_9 2, TBn2_8 15, 
2 1   LB_RB-2_S10_BN2C_CH1    W-2/RB2out/10 Forward;  
2 2   LB_RB-2_S10_BN2C_CH2    W-2/RB2out/10 Backward;  
3 0   LB_RB-2_S10_BN2D_CH0    W-2/RB4/10- Backward;    TBn2_9 3, TBn1_9 5, TBn2_8 14, TBn1_8 12, 
3 1   LB_RB-2_S10_BN2D_CH1    W-2/RB4/10+ Backward;  
3 2   LB_RB-2_S10_BN2D_CH2    W-2/RB3/10+ Backward;  W-2/RB3/10- Backward;  
4 0   LB_RB-2_S10_BN2E_CH0    W-2/RB4/10- Forward;    TBn2_9 4, TBn1_9 6, TBn2_8 13, TBn1_8 11, 
4 1   LB_RB-2_S10_BN2E_CH1    W-2/RB4/10+ Forward;  
4 2   LB_RB-2_S10_BN2E_CH2    W-2/RB3/10+ Forward;  W-2/RB3/10- Forward;  
5 0   LB_RB-1_S10_BN1B_CH0    W-1/RB1out/10 Forward;    TBn1_9 1, TBn2_8 12, TBn2_9 5, TBn1_8 16, 
5 1   LB_RB-1_S10_BN1B_CH1    W-1/RB2out/10 Forward;  
5 2   LB_RB-1_S10_BN1B_CH2    W-1/RB1in/10 Forward;  
6 0   LB_RE-1_S10_EN23_CH0    RE-2/3/30;    TBn3_9 6, TBn2_9 6, 
6 1   LB_RE-1_S10_EN23_CH1    RE-2/3/29;  
6 2   LB_RE-1_S10_EN23_CH2    RE-2/3/31;  
7 0   LB_RE-1_S10_EN13_CH0    RE-1/3/28;    TBn2_9 7, TBn2_8 10, 
7 1   LB_RE-1_S10_EN13_CH1    RE-1/3/27;  
7 2   LB_RE-1_S10_EN13_CH2    RE-1/3/29;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE-1_S11_EN13_CH0    RE-1/3/31;    TBn2_9 10, TBn2_10 7, 
10 1   LB_RE-1_S11_EN13_CH1    RE-1/3/30;  
10 2   LB_RE-1_S11_EN13_CH2    RE-1/3/32;  
11 0
11 1
11 2
12 0   LB_RB-1_S11_BN1B_CH0    W-1/RB1out/11 Forward;    TBn1_9 16, TBn1_10 1, TBn2_9 12, TBn2_10 5, 
12 1   LB_RB-1_S11_BN1B_CH1    W-1/RB2out/11 Forward;  
12 2   LB_RB-1_S11_BN1B_CH2    W-1/RB1in/11 Forward;  
13 0   LB_RB-2_S11_BN2E_CH0    W-2/RB4/11 Forward;    TBn2_9 13, TBn1_9 11, TBn2_10 4, 
13 1
13 2   LB_RB-2_S11_BN2E_CH2    W-2/RB3/11+ Forward;  W-2/RB3/11- Forward;  
14 0   LB_RB-2_S11_BN2D_CH0    W-2/RB4/11 Backward;    TBn2_9 14, TBn1_9 12, TBn2_10 3, TBn1_10 5, 
14 1
14 2   LB_RB-2_S11_BN2D_CH2    W-2/RB3/11+ Backward;  W-2/RB3/11- Backward;  
15 0   LB_RB-2_S11_BN2C_CH0    W-2/RB2out/11 Central;    TBn2_9 15, TBn2_10 2, 
15 1   LB_RB-2_S11_BN2C_CH1    W-2/RB2out/11 Forward;  
15 2   LB_RB-2_S11_BN2C_CH2    W-2/RB2out/11 Backward;  
16 0   LB_RB-2_S11_BN2B_CH0    W-2/RB1out/11 Forward;    TBn3_9 16, TBn2_9 16, TBn3_10 1, 
16 1   LB_RB-2_S11_BN2B_CH1    W-2/RB2in/11 Forward;  
16 2   LB_RB-2_S11_BN2B_CH2    W-2/RB1in/11 Forward;  
17 0   LB_RB-2_S11_BN2A_CH0    W-2/RB1out/11 Backward;    TBn2_9 17, TBn2_10 0, 
17 1   LB_RB-2_S11_BN2A_CH1    W-2/RB2in/11 Backward;  
17 2   LB_RB-2_S11_BN2A_CH2    W-2/RB1in/11 Backward;  

TBn1_9
0 0   LB_RB-1_S10_BN1A_CH0    W-1/RB1out/10 Backward;    TBn1_9 0, TBn1_8 17, 
0 1   LB_RB-1_S10_BN1A_CH1    W-1/RB2out/10 Backward;  
0 2   LB_RB-1_S10_BN1A_CH2    W-1/RB1in/10 Backward;  
1 0   LB_RB-1_S10_BN1B_CH0    W-1/RB1out/10 Forward;    TBn1_9 1, TBn2_8 12, TBn2_9 5, TBn1_8 16, 
1 1   LB_RB-1_S10_BN1B_CH1    W-1/RB2out/10 Forward;  
1 2   LB_RB-1_S10_BN1B_CH2    W-1/RB1in/10 Forward;  
2 0   LB_RB-1_S10_BN1C_CH0    W-1/RB2in/10 Central;    TBn1_9 2, TBn1_8 15, 
2 1   LB_RB-1_S10_BN1C_CH1    W-1/RB2in/10 Forward;  
2 2   LB_RB-1_S10_BN1C_CH2    W-1/RB2in/10 Backward;  
3 0   LB_RB-1_S10_BN1D_CH0    W-1/RB4/10- Backward;    TBn1_9 3, TB0_9 5, TBn1_8 14, TB0_8 12, 
3 1   LB_RB-1_S10_BN1D_CH1    W-1/RB4/10+ Backward;  
3 2   LB_RB-1_S10_BN1D_CH2    W-1/RB3/10+ Backward;  W-1/RB3/10- Backward;  
4 0   LB_RB-1_S10_BN1E_CH0    W-1/RB4/10- Forward;    TBn1_9 4, TBn1_8 13, 
4 1   LB_RB-1_S10_BN1E_CH1    W-1/RB4/10+ Forward;  
4 2   LB_RB-1_S10_BN1E_CH2    W-1/RB3/10+ Forward;  W-1/RB3/10- Forward;  
5 0   LB_RB-2_S10_BN2D_CH0    W-2/RB4/10- Backward;    TBn2_9 3, TBn1_9 5, TBn2_8 14, TBn1_8 12, 
5 1   LB_RB-2_S10_BN2D_CH1    W-2/RB4/10+ Backward;  
5 2   LB_RB-2_S10_BN2D_CH2    W-2/RB3/10+ Backward;  W-2/RB3/10- Backward;  
6 0   LB_RB-2_S10_BN2E_CH0    W-2/RB4/10- Forward;    TBn2_9 4, TBn1_9 6, TBn2_8 13, TBn1_8 11, 
6 1   LB_RB-2_S10_BN2E_CH1    W-2/RB4/10+ Forward;  
6 2   LB_RB-2_S10_BN2E_CH2    W-2/RB3/10+ Forward;  W-2/RB3/10- Forward;  
7 0   LB_RB0_S10_BP0B_CH0    W0/RB1out/10 Backward;    TB0_8 16, TB0_9 1, TBn1_9 7, 
7 1   LB_RB0_S10_BP0B_CH1    W0/RB2out/10 Backward;  
7 2   LB_RB0_S10_BP0B_CH2    W0/RB1in/10 Backward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S11_BP0B_CH0    W0/RB1out/11 Backward;    TB0_9 16, TB0_10 1, TBn1_9 10, 
10 1   LB_RB0_S11_BP0B_CH1    W0/RB2out/11 Backward;  
10 2   LB_RB0_S11_BP0B_CH2    W0/RB1in/11 Backward;  
11 0   LB_RB-2_S11_BN2E_CH0    W-2/RB4/11 Forward;    TBn2_9 13, TBn1_9 11, TBn2_10 4, 
11 1
11 2   LB_RB-2_S11_BN2E_CH2    W-2/RB3/11+ Forward;  W-2/RB3/11- Forward;  
12 0   LB_RB-2_S11_BN2D_CH0    W-2/RB4/11 Backward;    TBn2_9 14, TBn1_9 12, TBn2_10 3, TBn1_10 5, 
12 1
12 2   LB_RB-2_S11_BN2D_CH2    W-2/RB3/11+ Backward;  W-2/RB3/11- Backward;  
13 0   LB_RB-1_S11_BN1E_CH0    W-1/RB4/11 Forward;    TBn1_9 13, TBn1_10 4, 
13 1
13 2   LB_RB-1_S11_BN1E_CH2    W-1/RB3/11+ Forward;  W-1/RB3/11- Forward;  
14 0   LB_RB-1_S11_BN1D_CH0    W-1/RB4/11 Backward;    TBn1_9 14, TB0_9 12, TBn1_10 3, TB0_10 5, 
14 1
14 2   LB_RB-1_S11_BN1D_CH2    W-1/RB3/11+ Backward;  W-1/RB3/11- Backward;  
15 0   LB_RB-1_S11_BN1C_CH0    W-1/RB2in/11 Central;    TBn1_9 15, TBn1_10 2, 
15 1   LB_RB-1_S11_BN1C_CH1    W-1/RB2in/11 Forward;  
15 2   LB_RB-1_S11_BN1C_CH2    W-1/RB2in/11 Backward;  
16 0   LB_RB-1_S11_BN1B_CH0    W-1/RB1out/11 Forward;    TBn1_9 16, TBn1_10 1, TBn2_9 12, TBn2_10 5, 
16 1   LB_RB-1_S11_BN1B_CH1    W-1/RB2out/11 Forward;  
16 2   LB_RB-1_S11_BN1B_CH2    W-1/RB1in/11 Forward;  
17 0   LB_RB-1_S11_BN1A_CH0    W-1/RB1out/11 Backward;    TBn1_9 17, TBn1_10 0, 
17 1   LB_RB-1_S11_BN1A_CH1    W-1/RB2out/11 Backward;  
17 2   LB_RB-1_S11_BN1A_CH2    W-1/RB1in/11 Backward;  

TB0_9
0 0   LB_RB0_S10_BN0B_CH0    W0/RB1out/10 Forward;    TB0_8 17, TB0_9 0, TBp1_9 7, 
0 1   LB_RB0_S10_BN0B_CH1    W0/RB2out/10 Forward;  
0 2   LB_RB0_S10_BN0B_CH2    W0/RB1in/10 Forward;  
1 0   LB_RB0_S10_BP0B_CH0    W0/RB1out/10 Backward;    TB0_8 16, TB0_9 1, TBn1_9 7, 
1 1   LB_RB0_S10_BP0B_CH1    W0/RB2out/10 Backward;  
1 2   LB_RB0_S10_BP0B_CH2    W0/RB1in/10 Backward;  
3 0   LB_RB0_S10_BN0E_CH0    W0/RB4/10+ Forward;    TB0_9 3, 
3 1   LB_RB0_S10_BN0E_CH1    W0/RB4/10- Forward;  
3 2   LB_RB0_S10_BN0E_CH2    W0/RB3/10+ Forward;  W0/RB3/10- Forward;  
4 0   LB_RB0_S10_BP0E_CH0    W0/RB4/10+ Backward;    TB0_9 4, 
4 1   LB_RB0_S10_BP0E_CH1    W0/RB4/10- Backward;  
4 2   LB_RB0_S10_BP0E_CH2    W0/RB3/10+ Backward;  W0/RB3/10- Backward;  
5 0   LB_RB-1_S10_BN1D_CH0    W-1/RB4/10- Backward;    TBn1_9 3, TB0_9 5, TBn1_8 14, TB0_8 12, 
5 1   LB_RB-1_S10_BN1D_CH1    W-1/RB4/10+ Backward;  
5 2   LB_RB-1_S10_BN1D_CH2    W-1/RB3/10+ Backward;  W-1/RB3/10- Backward;  
6 0   LB_RB+1_S10_BP1D_CH0    W+1/RB4/10+ Backward;    TBp1_8 14, TB0_9 6, TBp1_9 3, 
6 1   LB_RB+1_S10_BP1D_CH1    W+1/RB4/10- Backward;  
6 2   LB_RB+1_S10_BP1D_CH2    W+1/RB3/10+ Backward;  W+1/RB3/10- Backward;  
7 0
7 1
7 2
8 0
8 1
8 2
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RB+1_S11_BP1D_CH0    W+1/RB4/11 Backward;    TB0_9 11, TBp1_9 14, TB0_10 6, 
11 1
11 2   LB_RB+1_S11_BP1D_CH2    W+1/RB3/11+ Backward;  W+1/RB3/11- Backward;  
12 0   LB_RB-1_S11_BN1D_CH0    W-1/RB4/11 Backward;    TBn1_9 14, TB0_9 12, TBn1_10 3, TB0_10 5, 
12 1
12 2   LB_RB-1_S11_BN1D_CH2    W-1/RB3/11+ Backward;  W-1/RB3/11- Backward;  
13 0   LB_RB0_S11_BP0E_CH0    W0/RB4/11 Backward;    TB0_9 13, 
13 1
13 2   LB_RB0_S11_BP0E_CH2    W0/RB3/11+ Backward;  W0/RB3/11- Backward;  
14 0   LB_RB0_S11_BN0E_CH0    W0/RB4/11 Forward;    TB0_9 14, 
14 1
14 2   LB_RB0_S11_BN0E_CH2    W0/RB3/11+ Forward;  W0/RB3/11- Forward;  
15 0   LB_RB0_S11_BM0C_CH0    W0/RB2in/11 Central;    TB0_9 15, 
15 1   LB_RB0_S11_BM0C_CH1    W0/RB2in/11 Forward;  
15 2   LB_RB0_S11_BM0C_CH2    W0/RB2in/11 Backward;  
16 0   LB_RB0_S11_BP0B_CH0    W0/RB1out/11 Backward;    TB0_9 16, TB0_10 1, TBn1_9 10, 
16 1   LB_RB0_S11_BP0B_CH1    W0/RB2out/11 Backward;  
16 2   LB_RB0_S11_BP0B_CH2    W0/RB1in/11 Backward;  
17 0   LB_RB0_S11_BN0B_CH0    W0/RB1out/11 Forward;    TB0_9 17, TB0_10 0, TBp1_9 10, 
17 1   LB_RB0_S11_BN0B_CH1    W0/RB2out/11 Forward;  
17 2   LB_RB0_S11_BN0B_CH2    W0/RB1in/11 Forward;  

TBp1_9
1 0   LB_RB+1_S10_BP1B_CH0    W+1/RB1out/10 Forward;    TBp1_8 16, TBp1_9 1, TBp2_9 5, 
1 1   LB_RB+1_S10_BP1B_CH1    W+1/RB2out/10 Forward;  
1 2   LB_RB+1_S10_BP1B_CH2    W+1/RB1in/10 Forward;  
3 0   LB_RB+1_S10_BP1D_CH0    W+1/RB4/10+ Backward;    TBp1_8 14, TB0_9 6, TBp1_9 3, 
3 1   LB_RB+1_S10_BP1D_CH1    W+1/RB4/10- Backward;  
3 2   LB_RB+1_S10_BP1D_CH2    W+1/RB3/10+ Backward;  W+1/RB3/10- Backward;  
5 0   LB_RB+2_S10_BP2D_CH0    W+2/RB4/10+ Backward;    TBp1_8 12, TBp1_9 5, TBp2_9 3, 
5 1   LB_RB+2_S10_BP2D_CH1    W+2/RB4/10- Backward;  
5 2   LB_RB+2_S10_BP2D_CH2    W+2/RB3/10+ Backward;  W+2/RB3/10- Backward;  
6 0   LB_RB+2_S10_BP2E_CH0    W+2/RB4/10+ Forward;    TBp1_8 11, TBp2_8 13, TBp1_9 6, TBp2_9 4, 
6 1   LB_RB+2_S10_BP2E_CH1    W+2/RB4/10- Forward;  
6 2   LB_RB+2_S10_BP2E_CH2    W+2/RB3/10+ Forward;  W+2/RB3/10- Forward;  
7 0   LB_RB0_S10_BN0B_CH0    W0/RB1out/10 Forward;    TB0_8 17, TB0_9 0, TBp1_9 7, 
7 1   LB_RB0_S10_BN0B_CH1    W0/RB2out/10 Forward;  
7 2   LB_RB0_S10_BN0B_CH2    W0/RB1in/10 Forward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S11_BN0B_CH0    W0/RB1out/11 Forward;    TB0_9 17, TB0_10 0, TBp1_9 10, 
10 1   LB_RB0_S11_BN0B_CH1    W0/RB2out/11 Forward;  
10 2   LB_RB0_S11_BN0B_CH2    W0/RB1in/11 Forward;  
11 0   LB_RB+2_S11_BP2E_CH0    W+2/RB4/11 Forward;    TBp1_9 11, TBp2_9 13, 
11 1
11 2   LB_RB+2_S11_BP2E_CH2    W+2/RB3/11+ Forward;  W+2/RB3/11- Forward;  
12 0   LB_RB+2_S11_BP2D_CH0    W+2/RB4/11 Backward;    TBp1_9 12, TBp2_9 14, TBp1_10 5, 
12 1
12 2   LB_RB+2_S11_BP2D_CH2    W+2/RB3/11+ Backward;  W+2/RB3/11- Backward;  
13 0   LB_RB+1_S11_BP1E_CH0    W+1/RB4/11 Forward;    TBp1_9 13, 
13 1
13 2   LB_RB+1_S11_BP1E_CH2    W+1/RB3/11+ Forward;  W+1/RB3/11- Forward;  
14 0   LB_RB+1_S11_BP1D_CH0    W+1/RB4/11 Backward;    TB0_9 11, TBp1_9 14, TB0_10 6, 
14 1
14 2   LB_RB+1_S11_BP1D_CH2    W+1/RB3/11+ Backward;  W+1/RB3/11- Backward;  
15 0   LB_RB+1_S11_BP1C_CH0    W+1/RB2in/11 Central;    TBp1_9 15, 
15 1   LB_RB+1_S11_BP1C_CH1    W+1/RB2in/11 Forward;  
15 2   LB_RB+1_S11_BP1C_CH2    W+1/RB2in/11 Backward;  
16 0   LB_RB+1_S11_BP1B_CH0    W+1/RB1out/11 Forward;    TBp1_9 16, TBp2_9 12, TBp1_10 1, 
16 1   LB_RB+1_S11_BP1B_CH1    W+1/RB2out/11 Forward;  
16 2   LB_RB+1_S11_BP1B_CH2    W+1/RB1in/11 Forward;  
17 0   LB_RB+1_S11_BP1A_CH0    W+1/RB1out/11 Backward;    TBp1_9 17, 
17 1   LB_RB+1_S11_BP1A_CH1    W+1/RB2out/11 Backward;  
17 2   LB_RB+1_S11_BP1A_CH2    W+1/RB1in/11 Backward;  

TBp2_9
0 0   LB_RB+2_S10_BP2A_CH0    W+2/RB1out/10 Backward;    TBp2_8 17, TBp2_9 0, 
0 1   LB_RB+2_S10_BP2A_CH1    W+2/RB2in/10 Backward;  
0 2   LB_RB+2_S10_BP2A_CH2    W+2/RB1in/10 Backward;  
1 0   LB_RB+2_S10_BP2B_CH0    W+2/RB1out/10 Forward;    TBp2_8 16, TBp3_8 16, TBp2_9 1, TBp3_9 1, 
1 1   LB_RB+2_S10_BP2B_CH1    W+2/RB2in/10 Forward;  
1 2   LB_RB+2_S10_BP2B_CH2    W+2/RB1in/10 Forward;  
2 0   LB_RB+2_S10_BP2C_CH0    W+2/RB2out/10 Central;    TBp2_8 15, TBp2_9 2, 
2 1   LB_RB+2_S10_BP2C_CH1    W+2/RB2out/10 Forward;  
2 2   LB_RB+2_S10_BP2C_CH2    W+2/RB2out/10 Backward;  
3 0   LB_RB+2_S10_BP2D_CH0    W+2/RB4/10+ Backward;    TBp1_8 12, TBp1_9 5, TBp2_9 3, 
3 1   LB_RB+2_S10_BP2D_CH1    W+2/RB4/10- Backward;  
3 2   LB_RB+2_S10_BP2D_CH2    W+2/RB3/10+ Backward;  W+2/RB3/10- Backward;  
4 0   LB_RB+2_S10_BP2E_CH0    W+2/RB4/10+ Forward;    TBp1_8 11, TBp2_8 13, TBp1_9 6, TBp2_9 4, 
4 1   LB_RB+2_S10_BP2E_CH1    W+2/RB4/10- Forward;  
4 2   LB_RB+2_S10_BP2E_CH2    W+2/RB3/10+ Forward;  W+2/RB3/10- Forward;  
5 0   LB_RB+1_S10_BP1B_CH0    W+1/RB1out/10 Forward;    TBp1_8 16, TBp1_9 1, TBp2_9 5, 
5 1   LB_RB+1_S10_BP1B_CH1    W+1/RB2out/10 Forward;  
5 2   LB_RB+1_S10_BP1B_CH2    W+1/RB1in/10 Forward;  
6 0   LB_RE+1_S10_EP23_CH0    RE+2/3/30;    TBp2_9 6, TBp3_9 6, 
6 1   LB_RE+1_S10_EP23_CH1    RE+2/3/29;  
6 2   LB_RE+1_S10_EP23_CH2    RE+2/3/31;  
7 0   LB_RE+1_S10_EP13_CH0    RE+1/3/28;    TBp2_8 10, TBp2_9 7, 
7 1   LB_RE+1_S10_EP13_CH1    RE+1/3/27;  
7 2   LB_RE+1_S10_EP13_CH2    RE+1/3/29;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE+1_S11_EP13_CH0    RE+1/3/31;    TBp2_9 10, TBp2_10 7, 
10 1   LB_RE+1_S11_EP13_CH1    RE+1/3/30;  
10 2   LB_RE+1_S11_EP13_CH2    RE+1/3/32;  
11 0
11 1
11 2
12 0   LB_RB+1_S11_BP1B_CH0    W+1/RB1out/11 Forward;    TBp1_9 16, TBp2_9 12, TBp1_10 1, 
12 1   LB_RB+1_S11_BP1B_CH1    W+1/RB2out/11 Forward;  
12 2   LB_RB+1_S11_BP1B_CH2    W+1/RB1in/11 Forward;  
13 0   LB_RB+2_S11_BP2E_CH0    W+2/RB4/11 Forward;    TBp1_9 11, TBp2_9 13, 
13 1
13 2   LB_RB+2_S11_BP2E_CH2    W+2/RB3/11+ Forward;  W+2/RB3/11- Forward;  
14 0   LB_RB+2_S11_BP2D_CH0    W+2/RB4/11 Backward;    TBp1_9 12, TBp2_9 14, TBp1_10 5, 
14 1
14 2   LB_RB+2_S11_BP2D_CH2    W+2/RB3/11+ Backward;  W+2/RB3/11- Backward;  
15 0   LB_RB+2_S11_BP2C_CH0    W+2/RB2out/11 Central;    TBp2_9 15, 
15 1   LB_RB+2_S11_BP2C_CH1    W+2/RB2out/11 Forward;  
15 2   LB_RB+2_S11_BP2C_CH2    W+2/RB2out/11 Backward;  
16 0   LB_RB+2_S11_BP2B_CH0    W+2/RB1out/11 Forward;    TBp2_9 16, TBp3_9 16, 
16 1   LB_RB+2_S11_BP2B_CH1    W+2/RB2in/11 Forward;  
16 2   LB_RB+2_S11_BP2B_CH2    W+2/RB1in/11 Forward;  
17 0   LB_RB+2_S11_BP2A_CH0    W+2/RB1out/11 Backward;    TBp2_9 17, 
17 1   LB_RB+2_S11_BP2A_CH1    W+2/RB2in/11 Backward;  
17 2   LB_RB+2_S11_BP2A_CH2    W+2/RB1in/11 Backward;  

TBp3_9
0 0
0 1
0 2
1 0   LB_RB+2_S10_BP2B_CH0    W+2/RB1out/10 Forward;    TBp2_8 16, TBp3_8 16, TBp2_9 1, TBp3_9 1, 
1 1   LB_RB+2_S10_BP2B_CH1    W+2/RB2in/10 Forward;  
1 2   LB_RB+2_S10_BP2B_CH2    W+2/RB1in/10 Forward;  
2 0   LB_RE+1_S10_EP12_CH0    RE+1/2/28;    TBp3_8 15, TBp3_9 2, 
2 1   LB_RE+1_S10_EP12_CH1    RE+1/2/27;  
2 2   LB_RE+1_S10_EP12_CH2    RE+1/2/29;  
3 0   LB_RE+3_S10_EP32_CH0    RE+3/2/28;    TBp3_8 14, TBp3_9 3, 
3 1   LB_RE+3_S10_EP32_CH1    RE+3/2/27;  
3 2   LB_RE+3_S10_EP32_CH2    RE+3/2/29;  
4 0   LB_RE+3_S10_EP33_CH0    RE+3/3/28;    TBp3_8 13, TBp3_9 4, 
4 1   LB_RE+3_S10_EP33_CH1    RE+3/3/27;  
4 2   LB_RE+3_S10_EP33_CH2    RE+3/3/29;  
5 0   LB_RE+1_S10_EP22_CH0    RE+2/2/30;    TBp3_9 5, 
5 1   LB_RE+1_S10_EP22_CH1    RE+2/2/29;  
5 2   LB_RE+1_S10_EP22_CH2    RE+2/2/31;  
6 0   LB_RE+1_S10_EP23_CH0    RE+2/3/30;    TBp2_9 6, TBp3_9 6, 
6 1   LB_RE+1_S10_EP23_CH1    RE+2/3/29;  
6 2   LB_RE+1_S10_EP23_CH2    RE+2/3/31;  
7 0   LB_RE+4_S10_EP42_CH0    RE+4/2/28;    TBp3_8 12, TBp3_9 7, 
7 1   LB_RE+4_S10_EP42_CH1    RE+4/2/27;  
7 2   LB_RE+4_S10_EP42_CH2    RE+4/2/29;  
8 0   LB_RE+4_S10_EP43_CH0    RE+4/3/28;    TBp3_8 11, TBp3_9 8, 
8 1   LB_RE+4_S10_EP43_CH1    RE+4/3/27;  
8 2   LB_RE+4_S10_EP43_CH2    RE+4/3/29;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE+4_S11_EP43_CH0    RE+4/3/31;    TBp3_9 11, TBp3_10 8, 
11 1   LB_RE+4_S11_EP43_CH1    RE+4/3/30;  
11 2   LB_RE+4_S11_EP43_CH2    RE+4/3/32;  
12 0   LB_RE+4_S11_EP42_CH0    RE+4/2/31;    TBp3_9 12, 
12 1   LB_RE+4_S11_EP42_CH1    RE+4/2/30;  
12 2   LB_RE+4_S11_EP42_CH2    RE+4/2/32;  
13 0   LB_RE+3_S11_EP33_CH0    RE+3/3/31;    TBp3_9 13, 
13 1   LB_RE+3_S11_EP33_CH1    RE+3/3/30;  
13 2   LB_RE+3_S11_EP33_CH2    RE+3/3/32;  
14 0   LB_RE+3_S11_EP32_CH0    RE+3/2/31;    TBp3_9 14, 
14 1   LB_RE+3_S11_EP32_CH1    RE+3/2/30;  
14 2   LB_RE+3_S11_EP32_CH2    RE+3/2/32;  
15 0   LB_RE+1_S11_EP12_CH0    RE+1/2/31;    TBp3_9 15, 
15 1   LB_RE+1_S11_EP12_CH1    RE+1/2/30;  
15 2   LB_RE+1_S11_EP12_CH2    RE+1/2/32;  
16 0   LB_RB+2_S11_BP2B_CH0    W+2/RB1out/11 Forward;    TBp2_9 16, TBp3_9 16, 
16 1   LB_RB+2_S11_BP2B_CH1    W+2/RB2in/11 Forward;  
16 2   LB_RB+2_S11_BP2B_CH2    W+2/RB1in/11 Forward;  
17 0
17 1
17 2

TC_10
TBn3_10
0 0
0 1
0 2
1 0   LB_RB-2_S11_BN2B_CH0    W-2/RB1out/11 Forward;    TBn3_9 16, TBn2_9 16, TBn3_10 1, 
1 1   LB_RB-2_S11_BN2B_CH1    W-2/RB2in/11 Forward;  
1 2   LB_RB-2_S11_BN2B_CH2    W-2/RB1in/11 Forward;  
5 0   LB_RE-1_S11_EN22_CH0    RE-2/2/33;    TBn3_10 5, 
5 1   LB_RE-1_S11_EN22_CH1    RE-2/2/32;  
5 2   LB_RE-1_S11_EN22_CH2    RE-2/2/34;  
6 0   LB_RE-1_S11_EN23_CH0    RE-2/3/33;    TBn3_10 6, TBn2_10 6, 
6 1   LB_RE-1_S11_EN23_CH1    RE-2/3/32;  
6 2   LB_RE-1_S11_EN23_CH2    RE-2/3/34;  
8 0   LB_RE-4_S11_EN43_CH0    RE-4/3/31;    TBn3_9 11, TBn3_10 8, 
8 1   LB_RE-4_S11_EN43_CH1    RE-4/3/30;  
8 2   LB_RE-4_S11_EN43_CH2    RE-4/3/32;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE-4_S12_EN43_CH0    RE-4/3/34;    TBn3_10 11, TBn3_11 8, 
11 1   LB_RE-4_S12_EN43_CH1    RE-4/3/33;  
11 2   LB_RE-4_S12_EN43_CH2    RE-4/3/35;  
12 0   LB_RE-4_S12_EN42_CH0    RE-4/2/34;    TBn3_10 12, TBn3_11 7, 
12 1   LB_RE-4_S12_EN42_CH1    RE-4/2/33;  
12 2   LB_RE-4_S12_EN42_CH2    RE-4/2/35;  
13 0   LB_RE-3_S12_EN33_CH0    RE-3/3/34;    TBn3_10 13, TBn3_11 4, 
13 1   LB_RE-3_S12_EN33_CH1    RE-3/3/33;  
13 2   LB_RE-3_S12_EN33_CH2    RE-3/3/35;  
14 0   LB_RE-3_S12_EN32_CH0    RE-3/2/34;    TBn3_10 14, TBn3_11 3, 
14 1   LB_RE-3_S12_EN32_CH1    RE-3/2/33;  
14 2   LB_RE-3_S12_EN32_CH2    RE-3/2/35;  
15 0   LB_RE-1_S12_EN12_CH0    RE-1/2/34;    TBn3_10 15, TBn3_11 2, 
15 1   LB_RE-1_S12_EN12_CH1    RE-1/2/33;  
15 2   LB_RE-1_S12_EN12_CH2    RE-1/2/35;  
16 0   LB_RB-2_S12_BN2B_CH0    W-2/RB1out/12 Forward;    TBn3_10 16, TBn2_10 16, TBn3_11 1, TBn2_11 1, 
16 1   LB_RB-2_S12_BN2B_CH1    W-2/RB2in/12 Forward;  
16 2   LB_RB-2_S12_BN2B_CH2    W-2/RB1in/12 Forward;  
17 0
17 1
17 2

TBn2_10
0 0   LB_RB-2_S11_BN2A_CH0    W-2/RB1out/11 Backward;    TBn2_9 17, TBn2_10 0, 
0 1   LB_RB-2_S11_BN2A_CH1    W-2/RB2in/11 Backward;  
0 2   LB_RB-2_S11_BN2A_CH2    W-2/RB1in/11 Backward;  
2 0   LB_RB-2_S11_BN2C_CH0    W-2/RB2out/11 Central;    TBn2_9 15, TBn2_10 2, 
2 1   LB_RB-2_S11_BN2C_CH1    W-2/RB2out/11 Forward;  
2 2   LB_RB-2_S11_BN2C_CH2    W-2/RB2out/11 Backward;  
3 0   LB_RB-2_S11_BN2D_CH0    W-2/RB4/11 Backward;    TBn2_9 14, TBn1_9 12, TBn2_10 3, TBn1_10 5, 
3 1
3 2   LB_RB-2_S11_BN2D_CH2    W-2/RB3/11+ Backward;  W-2/RB3/11- Backward;  
4 0   LB_RB-2_S11_BN2E_CH0    W-2/RB4/11 Forward;    TBn2_9 13, TBn1_9 11, TBn2_10 4, 
4 1
4 2   LB_RB-2_S11_BN2E_CH2    W-2/RB3/11+ Forward;  W-2/RB3/11- Forward;  
5 0   LB_RB-1_S11_BN1B_CH0    W-1/RB1out/11 Forward;    TBn1_9 16, TBn1_10 1, TBn2_9 12, TBn2_10 5, 
5 1   LB_RB-1_S11_BN1B_CH1    W-1/RB2out/11 Forward;  
5 2   LB_RB-1_S11_BN1B_CH2    W-1/RB1in/11 Forward;  
6 0   LB_RE-1_S11_EN23_CH0    RE-2/3/33;    TBn3_10 6, TBn2_10 6, 
6 1   LB_RE-1_S11_EN23_CH1    RE-2/3/32;  
6 2   LB_RE-1_S11_EN23_CH2    RE-2/3/34;  
7 0   LB_RE-1_S11_EN13_CH0    RE-1/3/31;    TBn2_9 10, TBn2_10 7, 
7 1   LB_RE-1_S11_EN13_CH1    RE-1/3/30;  
7 2   LB_RE-1_S11_EN13_CH2    RE-1/3/32;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE-1_S12_EN13_CH0    RE-1/3/34;    TBn2_10 10, TBn2_11 7, 
10 1   LB_RE-1_S12_EN13_CH1    RE-1/3/33;  
10 2   LB_RE-1_S12_EN13_CH2    RE-1/3/35;  
11 0
11 1
11 2
12 0   LB_RB-1_S12_BN1B_CH0    W-1/RB1out/12 Forward;    TBn1_10 16, TBn1_11 1, TBn2_10 12, 
12 1   LB_RB-1_S12_BN1B_CH1    W-1/RB2out/12 Forward;  
12 2   LB_RB-1_S12_BN1B_CH2    W-1/RB1in/12 Forward;  
13 0   LB_RB-2_S12_BN2E_CH0    W-2/RB4/12+ Forward;  W-2/RB4/12- Forward;    TBn2_10 13, TBn1_10 11, TBn2_11 4, TBn1_11 6, 
13 1
13 2   LB_RB-2_S12_BN2E_CH2    W-2/RB3/12+ Forward;  W-2/RB3/12- Forward;  
14 0   LB_RB-2_S12_BN2D_CH0    W-2/RB4/12+ Backward;  W-2/RB4/12- Backward;    TBn2_10 14, TBn1_10 12, TBn2_11 3, 
14 1
14 2   LB_RB-2_S12_BN2D_CH2    W-2/RB3/12+ Backward;  W-2/RB3/12- Backward;  
15 0   LB_RB-2_S12_BN2C_CH0    W-2/RB2out/12 Central;    TBn2_10 15, TBn2_11 2, 
15 1   LB_RB-2_S12_BN2C_CH1    W-2/RB2out/12 Forward;  
15 2   LB_RB-2_S12_BN2C_CH2    W-2/RB2out/12 Backward;  
16 0   LB_RB-2_S12_BN2B_CH0    W-2/RB1out/12 Forward;    TBn3_10 16, TBn2_10 16, TBn3_11 1, TBn2_11 1, 
16 1   LB_RB-2_S12_BN2B_CH1    W-2/RB2in/12 Forward;  
16 2   LB_RB-2_S12_BN2B_CH2    W-2/RB1in/12 Forward;  
17 0   LB_RB-2_S12_BN2A_CH0    W-2/RB1out/12 Backward;    TBn2_10 17, TBn2_11 0, 
17 1   LB_RB-2_S12_BN2A_CH1    W-2/RB2in/12 Backward;  
17 2   LB_RB-2_S12_BN2A_CH2    W-2/RB1in/12 Backward;  

TBn1_10
0 0   LB_RB-1_S11_BN1A_CH0    W-1/RB1out/11 Backward;    TBn1_9 17, TBn1_10 0, 
0 1   LB_RB-1_S11_BN1A_CH1    W-1/RB2out/11 Backward;  
0 2   LB_RB-1_S11_BN1A_CH2    W-1/RB1in/11 Backward;  
1 0   LB_RB-1_S11_BN1B_CH0    W-1/RB1out/11 Forward;    TBn1_9 16, TBn1_10 1, TBn2_9 12, TBn2_10 5, 
1 1   LB_RB-1_S11_BN1B_CH1    W-1/RB2out/11 Forward;  
1 2   LB_RB-1_S11_BN1B_CH2    W-1/RB1in/11 Forward;  
2 0   LB_RB-1_S11_BN1C_CH0    W-1/RB2in/11 Central;    TBn1_9 15, TBn1_10 2, 
2 1   LB_RB-1_S11_BN1C_CH1    W-1/RB2in/11 Forward;  
2 2   LB_RB-1_S11_BN1C_CH2    W-1/RB2in/11 Backward;  
3 0   LB_RB-1_S11_BN1D_CH0    W-1/RB4/11 Backward;    TBn1_9 14, TB0_9 12, TBn1_10 3, TB0_10 5, 
3 1
3 2   LB_RB-1_S11_BN1D_CH2    W-1/RB3/11+ Backward;  W-1/RB3/11- Backward;  
4 0   LB_RB-1_S11_BN1E_CH0    W-1/RB4/11 Forward;    TBn1_9 13, TBn1_10 4, 
4 1
4 2   LB_RB-1_S11_BN1E_CH2    W-1/RB3/11+ Forward;  W-1/RB3/11- Forward;  
5 0   LB_RB-2_S11_BN2D_CH0    W-2/RB4/11 Backward;    TBn2_9 14, TBn1_9 12, TBn2_10 3, TBn1_10 5, 
5 1
5 2   LB_RB-2_S11_BN2D_CH2    W-2/RB3/11+ Backward;  W-2/RB3/11- Backward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S12_BN0B_CH0    W0/RB1out/12 Forward;    TB0_10 17, TB0_11 0, TBn1_10 10, TBn1_11 7, 
10 1   LB_RB0_S12_BN0B_CH1    W0/RB2out/12 Forward;  
10 2   LB_RB0_S12_BN0B_CH2    W0/RB1in/12 Forward;  
11 0   LB_RB-2_S12_BN2E_CH0    W-2/RB4/12+ Forward;  W-2/RB4/12- Forward;    TBn2_10 13, TBn1_10 11, TBn2_11 4, TBn1_11 6, 
11 1
11 2   LB_RB-2_S12_BN2E_CH2    W-2/RB3/12+ Forward;  W-2/RB3/12- Forward;  
12 0   LB_RB-2_S12_BN2D_CH0    W-2/RB4/12+ Backward;  W-2/RB4/12- Backward;    TBn2_10 14, TBn1_10 12, TBn2_11 3, 
12 1
12 2   LB_RB-2_S12_BN2D_CH2    W-2/RB3/12+ Backward;  W-2/RB3/12- Backward;  
13 0   LB_RB-1_S12_BN1E_CH0    W-1/RB4/12+ Forward;  W-1/RB4/12- Forward;    TBn1_10 13, 
13 1
13 2   LB_RB-1_S12_BN1E_CH2    W-1/RB3/12+ Forward;  W-1/RB3/12- Forward;  
14 0   LB_RB-1_S12_BN1D_CH0    W-1/RB4/12+ Backward;  W-1/RB4/12- Backward;    TBn1_10 14, TB0_10 12, TBn1_11 3, 
14 1
14 2   LB_RB-1_S12_BN1D_CH2    W-1/RB3/12+ Backward;  W-1/RB3/12- Backward;  
15 0   LB_RB-1_S12_BN1C_CH0    W-1/RB2in/12 Central;    TBn1_10 15, 
15 1   LB_RB-1_S12_BN1C_CH1    W-1/RB2in/12 Forward;  
15 2   LB_RB-1_S12_BN1C_CH2    W-1/RB2in/12 Backward;  
16 0   LB_RB-1_S12_BN1B_CH0    W-1/RB1out/12 Forward;    TBn1_10 16, TBn1_11 1, TBn2_10 12, 
16 1   LB_RB-1_S12_BN1B_CH1    W-1/RB2out/12 Forward;  
16 2   LB_RB-1_S12_BN1B_CH2    W-1/RB1in/12 Forward;  
17 0   LB_RB-1_S12_BN1A_CH0    W-1/RB1out/12 Backward;    TBn1_10 17, 
17 1   LB_RB-1_S12_BN1A_CH1    W-1/RB2out/12 Backward;  
17 2   LB_RB-1_S12_BN1A_CH2    W-1/RB1in/12 Backward;  

TB0_10
0 0   LB_RB0_S11_BN0B_CH0    W0/RB1out/11 Forward;    TB0_9 17, TB0_10 0, TBp1_9 10, 
0 1   LB_RB0_S11_BN0B_CH1    W0/RB2out/11 Forward;  
0 2   LB_RB0_S11_BN0B_CH2    W0/RB1in/11 Forward;  
1 0   LB_RB0_S11_BP0B_CH0    W0/RB1out/11 Backward;    TB0_9 16, TB0_10 1, TBn1_9 10, 
1 1   LB_RB0_S11_BP0B_CH1    W0/RB2out/11 Backward;  
1 2   LB_RB0_S11_BP0B_CH2    W0/RB1in/11 Backward;  
5 0   LB_RB-1_S11_BN1D_CH0    W-1/RB4/11 Backward;    TBn1_9 14, TB0_9 12, TBn1_10 3, TB0_10 5, 
5 1
5 2   LB_RB-1_S11_BN1D_CH2    W-1/RB3/11+ Backward;  W-1/RB3/11- Backward;  
6 0   LB_RB+1_S11_BP1D_CH0    W+1/RB4/11 Backward;    TB0_9 11, TBp1_9 14, TB0_10 6, 
6 1
6 2   LB_RB+1_S11_BP1D_CH2    W+1/RB3/11+ Backward;  W+1/RB3/11- Backward;  
7 0
7 1
7 2
8 0
8 1
8 2
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RB+1_S12_BP1D_CH0    W+1/RB4/12+ Backward;  W+1/RB4/12- Backward;    TB0_10 11, TBp1_10 14, TB0_11 6, 
11 1
11 2   LB_RB+1_S12_BP1D_CH2    W+1/RB3/12+ Backward;  W+1/RB3/12- Backward;  
12 0   LB_RB-1_S12_BN1D_CH0    W-1/RB4/12+ Backward;  W-1/RB4/12- Backward;    TBn1_10 14, TB0_10 12, TBn1_11 3, 
12 1
12 2   LB_RB-1_S12_BN1D_CH2    W-1/RB3/12+ Backward;  W-1/RB3/12- Backward;  
13 0   LB_RB0_S12_BP0E_CH0    W0/RB4/12+ Backward;  W0/RB4/12- Backward;    TB0_10 13, 
13 1
13 2   LB_RB0_S12_BP0E_CH2    W0/RB3/12+ Backward;  W0/RB3/12- Backward;  
14 0   LB_RB0_S12_BN0E_CH0    W0/RB4/12+ Forward;  W0/RB4/12- Forward;    TB0_10 14, 
14 1
14 2   LB_RB0_S12_BN0E_CH2    W0/RB3/12+ Forward;  W0/RB3/12- Forward;  
15 0   LB_RB0_S12_BM0C_CH0    W0/RB2in/12 Central;    TB0_10 15, 
15 1   LB_RB0_S12_BM0C_CH1    W0/RB2in/12 Forward;  
15 2   LB_RB0_S12_BM0C_CH2    W0/RB2in/12 Backward;  
16 0   LB_RB0_S12_BP0B_CH0    W0/RB1out/12 Backward;    TB0_10 16, TB0_11 1, TBp1_10 10, 
16 1   LB_RB0_S12_BP0B_CH1    W0/RB2out/12 Backward;  
16 2   LB_RB0_S12_BP0B_CH2    W0/RB1in/12 Backward;  
17 0   LB_RB0_S12_BN0B_CH0    W0/RB1out/12 Forward;    TB0_10 17, TB0_11 0, TBn1_10 10, TBn1_11 7, 
17 1   LB_RB0_S12_BN0B_CH1    W0/RB2out/12 Forward;  
17 2   LB_RB0_S12_BN0B_CH2    W0/RB1in/12 Forward;  

TBp1_10
1 0   LB_RB+1_S11_BP1B_CH0    W+1/RB1out/11 Forward;    TBp1_9 16, TBp2_9 12, TBp1_10 1, 
1 1   LB_RB+1_S11_BP1B_CH1    W+1/RB2out/11 Forward;  
1 2   LB_RB+1_S11_BP1B_CH2    W+1/RB1in/11 Forward;  
5 0   LB_RB+2_S11_BP2D_CH0    W+2/RB4/11 Backward;    TBp1_9 12, TBp2_9 14, TBp1_10 5, 
5 1
5 2   LB_RB+2_S11_BP2D_CH2    W+2/RB3/11+ Backward;  W+2/RB3/11- Backward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S12_BP0B_CH0    W0/RB1out/12 Backward;    TB0_10 16, TB0_11 1, TBp1_10 10, 
10 1   LB_RB0_S12_BP0B_CH1    W0/RB2out/12 Backward;  
10 2   LB_RB0_S12_BP0B_CH2    W0/RB1in/12 Backward;  
11 0   LB_RB+2_S12_BP2E_CH0    W+2/RB4/12+ Forward;  W+2/RB4/12- Forward;    TBp1_10 11, TBp2_10 13, TBp1_11 6, TBp2_11 4, 
11 1
11 2   LB_RB+2_S12_BP2E_CH2    W+2/RB3/12+ Forward;  W+2/RB3/12- Forward;  
12 0   LB_RB+2_S12_BP2D_CH0    W+2/RB4/12+ Backward;  W+2/RB4/12- Backward;    TBp1_10 12, TBp2_10 14, TBp1_11 5, 
12 1
12 2   LB_RB+2_S12_BP2D_CH2    W+2/RB3/12+ Backward;  W+2/RB3/12- Backward;  
13 0   LB_RB+1_S12_BP1E_CH0    W+1/RB4/12+ Forward;  W+1/RB4/12- Forward;    TBp1_10 13, 
13 1
13 2   LB_RB+1_S12_BP1E_CH2    W+1/RB3/12+ Forward;  W+1/RB3/12- Forward;  
14 0   LB_RB+1_S12_BP1D_CH0    W+1/RB4/12+ Backward;  W+1/RB4/12- Backward;    TB0_10 11, TBp1_10 14, TB0_11 6, 
14 1
14 2   LB_RB+1_S12_BP1D_CH2    W+1/RB3/12+ Backward;  W+1/RB3/12- Backward;  
15 0   LB_RB+1_S12_BP1C_CH0    W+1/RB2in/12 Central;    TBp1_10 15, 
15 1   LB_RB+1_S12_BP1C_CH1    W+1/RB2in/12 Forward;  
15 2   LB_RB+1_S12_BP1C_CH2    W+1/RB2in/12 Backward;  
16 0   LB_RB+1_S12_BP1B_CH0    W+1/RB1out/12 Forward;    TBp1_10 16, TBp2_10 12, TBp1_11 1, 
16 1   LB_RB+1_S12_BP1B_CH1    W+1/RB2out/12 Forward;  
16 2   LB_RB+1_S12_BP1B_CH2    W+1/RB1in/12 Forward;  
17 0   LB_RB+1_S12_BP1A_CH0    W+1/RB1out/12 Backward;    TBp1_10 17, 
17 1   LB_RB+1_S12_BP1A_CH1    W+1/RB2out/12 Backward;  
17 2   LB_RB+1_S12_BP1A_CH2    W+1/RB1in/12 Backward;  

TBp2_10
6 0   LB_RE+1_S11_EP23_CH0    RE+2/3/33;    TBp2_10 6, TBp3_10 6, 
6 1   LB_RE+1_S11_EP23_CH1    RE+2/3/32;  
6 2   LB_RE+1_S11_EP23_CH2    RE+2/3/34;  
7 0   LB_RE+1_S11_EP13_CH0    RE+1/3/31;    TBp2_9 10, TBp2_10 7, 
7 1   LB_RE+1_S11_EP13_CH1    RE+1/3/30;  
7 2   LB_RE+1_S11_EP13_CH2    RE+1/3/32;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE+1_S12_EP13_CH0    RE+1/3/34;    TBp2_10 10, TBp2_11 7, 
10 1   LB_RE+1_S12_EP13_CH1    RE+1/3/33;  
10 2   LB_RE+1_S12_EP13_CH2    RE+1/3/35;  
11 0
11 1
11 2
12 0   LB_RB+1_S12_BP1B_CH0    W+1/RB1out/12 Forward;    TBp1_10 16, TBp2_10 12, TBp1_11 1, 
12 1   LB_RB+1_S12_BP1B_CH1    W+1/RB2out/12 Forward;  
12 2   LB_RB+1_S12_BP1B_CH2    W+1/RB1in/12 Forward;  
13 0   LB_RB+2_S12_BP2E_CH0    W+2/RB4/12+ Forward;  W+2/RB4/12- Forward;    TBp1_10 11, TBp2_10 13, TBp1_11 6, TBp2_11 4, 
13 1
13 2   LB_RB+2_S12_BP2E_CH2    W+2/RB3/12+ Forward;  W+2/RB3/12- Forward;  
14 0   LB_RB+2_S12_BP2D_CH0    W+2/RB4/12+ Backward;  W+2/RB4/12- Backward;    TBp1_10 12, TBp2_10 14, TBp1_11 5, 
14 1
14 2   LB_RB+2_S12_BP2D_CH2    W+2/RB3/12+ Backward;  W+2/RB3/12- Backward;  
15 0   LB_RB+2_S12_BP2C_CH0    W+2/RB2out/12 Central;    TBp2_10 15, TBp2_11 2, 
15 1   LB_RB+2_S12_BP2C_CH1    W+2/RB2out/12 Forward;  
15 2   LB_RB+2_S12_BP2C_CH2    W+2/RB2out/12 Backward;  
16 0   LB_RB+2_S12_BP2B_CH0    W+2/RB1out/12 Forward;    TBp2_10 16, TBp3_10 16, TBp2_11 1, TBp3_11 1, 
16 1   LB_RB+2_S12_BP2B_CH1    W+2/RB2in/12 Forward;  
16 2   LB_RB+2_S12_BP2B_CH2    W+2/RB1in/12 Forward;  
17 0   LB_RB+2_S12_BP2A_CH0    W+2/RB1out/12 Backward;    TBp2_10 17, TBp2_11 0, 
17 1   LB_RB+2_S12_BP2A_CH1    W+2/RB2in/12 Backward;  
17 2   LB_RB+2_S12_BP2A_CH2    W+2/RB1in/12 Backward;  

TBp3_10
0 0
0 1
0 2
5 0   LB_RE+1_S11_EP22_CH0    RE+2/2/33;    TBp3_10 5, 
5 1   LB_RE+1_S11_EP22_CH1    RE+2/2/32;  
5 2   LB_RE+1_S11_EP22_CH2    RE+2/2/34;  
6 0   LB_RE+1_S11_EP23_CH0    RE+2/3/33;    TBp2_10 6, TBp3_10 6, 
6 1   LB_RE+1_S11_EP23_CH1    RE+2/3/32;  
6 2   LB_RE+1_S11_EP23_CH2    RE+2/3/34;  
8 0   LB_RE+4_S11_EP43_CH0    RE+4/3/31;    TBp3_9 11, TBp3_10 8, 
8 1   LB_RE+4_S11_EP43_CH1    RE+4/3/30;  
8 2   LB_RE+4_S11_EP43_CH2    RE+4/3/32;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE+4_S12_EP43_CH0    RE+4/3/34;    TBp3_10 11, TBp3_11 8, 
11 1   LB_RE+4_S12_EP43_CH1    RE+4/3/33;  
11 2   LB_RE+4_S12_EP43_CH2    RE+4/3/35;  
12 0   LB_RE+4_S12_EP42_CH0    RE+4/2/34;    TBp3_10 12, TBp3_11 7, 
12 1   LB_RE+4_S12_EP42_CH1    RE+4/2/33;  
12 2   LB_RE+4_S12_EP42_CH2    RE+4/2/35;  
13 0   LB_RE+3_S12_EP33_CH0    RE+3/3/34;    TBp3_10 13, TBp3_11 4, 
13 1   LB_RE+3_S12_EP33_CH1    RE+3/3/33;  
13 2   LB_RE+3_S12_EP33_CH2    RE+3/3/35;  
14 0   LB_RE+3_S12_EP32_CH0    RE+3/2/34;    TBp3_10 14, TBp3_11 3, 
14 1   LB_RE+3_S12_EP32_CH1    RE+3/2/33;  
14 2   LB_RE+3_S12_EP32_CH2    RE+3/2/35;  
15 0   LB_RE+1_S12_EP12_CH0    RE+1/2/34;    TBp3_10 15, TBp3_11 2, 
15 1   LB_RE+1_S12_EP12_CH1    RE+1/2/33;  
15 2   LB_RE+1_S12_EP12_CH2    RE+1/2/35;  
16 0   LB_RB+2_S12_BP2B_CH0    W+2/RB1out/12 Forward;    TBp2_10 16, TBp3_10 16, TBp2_11 1, TBp3_11 1, 
16 1   LB_RB+2_S12_BP2B_CH1    W+2/RB2in/12 Forward;  
16 2   LB_RB+2_S12_BP2B_CH2    W+2/RB1in/12 Forward;  
17 0
17 1
17 2

TC_11
TBn3_11
0 0
0 1
0 2
1 0   LB_RB-2_S12_BN2B_CH0    W-2/RB1out/12 Forward;    TBn3_10 16, TBn2_10 16, TBn3_11 1, TBn2_11 1, 
1 1   LB_RB-2_S12_BN2B_CH1    W-2/RB2in/12 Forward;  
1 2   LB_RB-2_S12_BN2B_CH2    W-2/RB1in/12 Forward;  
2 0   LB_RE-1_S12_EN12_CH0    RE-1/2/34;    TBn3_10 15, TBn3_11 2, 
2 1   LB_RE-1_S12_EN12_CH1    RE-1/2/33;  
2 2   LB_RE-1_S12_EN12_CH2    RE-1/2/35;  
3 0   LB_RE-3_S12_EN32_CH0    RE-3/2/34;    TBn3_10 14, TBn3_11 3, 
3 1   LB_RE-3_S12_EN32_CH1    RE-3/2/33;  
3 2   LB_RE-3_S12_EN32_CH2    RE-3/2/35;  
4 0   LB_RE-3_S12_EN33_CH0    RE-3/3/34;    TBn3_10 13, TBn3_11 4, 
4 1   LB_RE-3_S12_EN33_CH1    RE-3/3/33;  
4 2   LB_RE-3_S12_EN33_CH2    RE-3/3/35;  
5 0   LB_RE-1_S12_EN22_CH0    RE-2/2/36;    TBn3_11 5, 
5 1   LB_RE-1_S12_EN22_CH1    RE-2/2/35;  
5 2   LB_RE-1_S12_EN22_CH2    RE-2/2/1;  
6 0   LB_RE-1_S12_EN23_CH0    RE-2/3/36;    TBn3_11 6, TBn2_11 6, 
6 1   LB_RE-1_S12_EN23_CH1    RE-2/3/35;  
6 2   LB_RE-1_S12_EN23_CH2    RE-2/3/1;  
7 0   LB_RE-4_S12_EN42_CH0    RE-4/2/34;    TBn3_10 12, TBn3_11 7, 
7 1   LB_RE-4_S12_EN42_CH1    RE-4/2/33;  
7 2   LB_RE-4_S12_EN42_CH2    RE-4/2/35;  
8 0   LB_RE-4_S12_EN43_CH0    RE-4/3/34;    TBn3_10 11, TBn3_11 8, 
8 1   LB_RE-4_S12_EN43_CH1    RE-4/3/33;  
8 2   LB_RE-4_S12_EN43_CH2    RE-4/3/35;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE-4_S1_EN43_CH0    RE-4/3/1;    TBn3_0 8, TBn3_11 11, 
11 1   LB_RE-4_S1_EN43_CH1    RE-4/3/36;  
11 2   LB_RE-4_S1_EN43_CH2    RE-4/3/2;  
15 0   LB_RE-1_S1_EN12_CH0    RE-1/2/1;    TBn3_11 15, 
15 1   LB_RE-1_S1_EN12_CH1    RE-1/2/36;  
15 2   LB_RE-1_S1_EN12_CH2    RE-1/2/2;  
16 0   LB_RB-2_S1_BN2B_CH0    W-2/RB1out/1 Forward;    TBn3_11 16, TBn2_11 16, 
16 1   LB_RB-2_S1_BN2B_CH1    W-2/RB2in/1 Forward;  
16 2   LB_RB-2_S1_BN2B_CH2    W-2/RB1in/1 Forward;  
17 0
17 1
17 2

TBn2_11
0 0   LB_RB-2_S12_BN2A_CH0    W-2/RB1out/12 Backward;    TBn2_10 17, TBn2_11 0, 
0 1   LB_RB-2_S12_BN2A_CH1    W-2/RB2in/12 Backward;  
0 2   LB_RB-2_S12_BN2A_CH2    W-2/RB1in/12 Backward;  
1 0   LB_RB-2_S12_BN2B_CH0    W-2/RB1out/12 Forward;    TBn3_10 16, TBn2_10 16, TBn3_11 1, TBn2_11 1, 
1 1   LB_RB-2_S12_BN2B_CH1    W-2/RB2in/12 Forward;  
1 2   LB_RB-2_S12_BN2B_CH2    W-2/RB1in/12 Forward;  
2 0   LB_RB-2_S12_BN2C_CH0    W-2/RB2out/12 Central;    TBn2_10 15, TBn2_11 2, 
2 1   LB_RB-2_S12_BN2C_CH1    W-2/RB2out/12 Forward;  
2 2   LB_RB-2_S12_BN2C_CH2    W-2/RB2out/12 Backward;  
3 0   LB_RB-2_S12_BN2D_CH0    W-2/RB4/12+ Backward;  W-2/RB4/12- Backward;    TBn2_10 14, TBn1_10 12, TBn2_11 3, 
3 1
3 2   LB_RB-2_S12_BN2D_CH2    W-2/RB3/12+ Backward;  W-2/RB3/12- Backward;  
4 0   LB_RB-2_S12_BN2E_CH0    W-2/RB4/12+ Forward;  W-2/RB4/12- Forward;    TBn2_10 13, TBn1_10 11, TBn2_11 4, TBn1_11 6, 
4 1
4 2   LB_RB-2_S12_BN2E_CH2    W-2/RB3/12+ Forward;  W-2/RB3/12- Forward;  
6 0   LB_RE-1_S12_EN23_CH0    RE-2/3/36;    TBn3_11 6, TBn2_11 6, 
6 1   LB_RE-1_S12_EN23_CH1    RE-2/3/35;  
6 2   LB_RE-1_S12_EN23_CH2    RE-2/3/1;  
7 0   LB_RE-1_S12_EN13_CH0    RE-1/3/34;    TBn2_10 10, TBn2_11 7, 
7 1   LB_RE-1_S12_EN13_CH1    RE-1/3/33;  
7 2   LB_RE-1_S12_EN13_CH2    RE-1/3/35;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE-1_S1_EN13_CH0    RE-1/3/1;    TBn2_11 10, TBn2_0 7, 
10 1   LB_RE-1_S1_EN13_CH1    RE-1/3/36;  
10 2   LB_RE-1_S1_EN13_CH2    RE-1/3/2;  
11 0
11 1
11 2
12 0   LB_RB-1_S1_BN1B_CH0    W-1/RB1out/1 Forward;    TBn1_11 16, TBn2_0 5, TBn2_11 12, 
12 1   LB_RB-1_S1_BN1B_CH1    W-1/RB2out/1 Forward;  
12 2   LB_RB-1_S1_BN1B_CH2    W-1/RB1in/1 Forward;  
13 0   LB_RB-2_S1_BN2E_CH0    W-2/RB4/1+ Forward;  W-2/RB4/1- Forward;    TBn2_11 13, TBn1_11 11, 
13 1
13 2   LB_RB-2_S1_BN2E_CH2    W-2/RB3/1+ Forward;  W-2/RB3/1- Forward;  
14 0   LB_RB-2_S1_BN2D_CH0    W-2/RB4/1+ Backward;  W-2/RB4/1- Backward;    TBn2_11 14, TBn1_11 12, TBn2_0 3, 
14 1
14 2   LB_RB-2_S1_BN2D_CH2    W-2/RB3/1+ Backward;  W-2/RB3/1- Backward;  
15 0   LB_RB-2_S1_BN2C_CH0    W-2/RB2out/1 Central;    TBn2_11 15, 
15 1   LB_RB-2_S1_BN2C_CH1    W-2/RB2out/1 Forward;  
15 2   LB_RB-2_S1_BN2C_CH2    W-2/RB2out/1 Backward;  
16 0   LB_RB-2_S1_BN2B_CH0    W-2/RB1out/1 Forward;    TBn3_11 16, TBn2_11 16, 
16 1   LB_RB-2_S1_BN2B_CH1    W-2/RB2in/1 Forward;  
16 2   LB_RB-2_S1_BN2B_CH2    W-2/RB1in/1 Forward;  
17 0   LB_RB-2_S1_BN2A_CH0    W-2/RB1out/1 Backward;    TBn2_11 17, 
17 1   LB_RB-2_S1_BN2A_CH1    W-2/RB2in/1 Backward;  
17 2   LB_RB-2_S1_BN2A_CH2    W-2/RB1in/1 Backward;  

TBn1_11
1 0   LB_RB-1_S12_BN1B_CH0    W-1/RB1out/12 Forward;    TBn1_10 16, TBn1_11 1, TBn2_10 12, 
1 1   LB_RB-1_S12_BN1B_CH1    W-1/RB2out/12 Forward;  
1 2   LB_RB-1_S12_BN1B_CH2    W-1/RB1in/12 Forward;  
3 0   LB_RB-1_S12_BN1D_CH0    W-1/RB4/12+ Backward;  W-1/RB4/12- Backward;    TBn1_10 14, TB0_10 12, TBn1_11 3, 
3 1
3 2   LB_RB-1_S12_BN1D_CH2    W-1/RB3/12+ Backward;  W-1/RB3/12- Backward;  
6 0   LB_RB-2_S12_BN2E_CH0    W-2/RB4/12+ Forward;  W-2/RB4/12- Forward;    TBn2_10 13, TBn1_10 11, TBn2_11 4, TBn1_11 6, 
6 1
6 2   LB_RB-2_S12_BN2E_CH2    W-2/RB3/12+ Forward;  W-2/RB3/12- Forward;  
7 0   LB_RB0_S12_BN0B_CH0    W0/RB1out/12 Forward;    TB0_10 17, TB0_11 0, TBn1_10 10, TBn1_11 7, 
7 1   LB_RB0_S12_BN0B_CH1    W0/RB2out/12 Forward;  
7 2   LB_RB0_S12_BN0B_CH2    W0/RB1in/12 Forward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S1_BN0B_CH0    W0/RB1out/1 Forward;    TB0_11 17, TB0_0 0, TBn1_11 10, 
10 1   LB_RB0_S1_BN0B_CH1    W0/RB2out/1 Forward;  
10 2   LB_RB0_S1_BN0B_CH2    W0/RB1in/1 Forward;  
11 0   LB_RB-2_S1_BN2E_CH0    W-2/RB4/1+ Forward;  W-2/RB4/1- Forward;    TBn2_11 13, TBn1_11 11, 
11 1
11 2   LB_RB-2_S1_BN2E_CH2    W-2/RB3/1+ Forward;  W-2/RB3/1- Forward;  
12 0   LB_RB-2_S1_BN2D_CH0    W-2/RB4/1+ Backward;  W-2/RB4/1- Backward;    TBn2_11 14, TBn1_11 12, TBn2_0 3, 
12 1
12 2   LB_RB-2_S1_BN2D_CH2    W-2/RB3/1+ Backward;  W-2/RB3/1- Backward;  
13 0   LB_RB-1_S1_BN1E_CH0    W-1/RB4/1+ Forward;  W-1/RB4/1- Forward;    TBn1_11 13, 
13 1
13 2   LB_RB-1_S1_BN1E_CH2    W-1/RB3/1+ Forward;  W-1/RB3/1- Forward;  
14 0   LB_RB-1_S1_BN1D_CH0    W-1/RB4/1+ Backward;  W-1/RB4/1- Backward;    TBn1_11 14, TB0_11 12, TBn1_0 3, 
14 1
14 2   LB_RB-1_S1_BN1D_CH2    W-1/RB3/1+ Backward;  W-1/RB3/1- Backward;  
15 0   LB_RB-1_S1_BN1C_CH0    W-1/RB2in/1 Central;    TBn1_11 15, 
15 1   LB_RB-1_S1_BN1C_CH1    W-1/RB2in/1 Forward;  
15 2   LB_RB-1_S1_BN1C_CH2    W-1/RB2in/1 Backward;  
16 0   LB_RB-1_S1_BN1B_CH0    W-1/RB1out/1 Forward;    TBn1_11 16, TBn2_0 5, TBn2_11 12, 
16 1   LB_RB-1_S1_BN1B_CH1    W-1/RB2out/1 Forward;  
16 2   LB_RB-1_S1_BN1B_CH2    W-1/RB1in/1 Forward;  
17 0   LB_RB-1_S1_BN1A_CH0    W-1/RB1out/1 Backward;    TBn1_11 17, 
17 1   LB_RB-1_S1_BN1A_CH1    W-1/RB2out/1 Backward;  
17 2   LB_RB-1_S1_BN1A_CH2    W-1/RB1in/1 Backward;  

TB0_11
0 0   LB_RB0_S12_BN0B_CH0    W0/RB1out/12 Forward;    TB0_10 17, TB0_11 0, TBn1_10 10, TBn1_11 7, 
0 1   LB_RB0_S12_BN0B_CH1    W0/RB2out/12 Forward;  
0 2   LB_RB0_S12_BN0B_CH2    W0/RB1in/12 Forward;  
1 0   LB_RB0_S12_BP0B_CH0    W0/RB1out/12 Backward;    TB0_10 16, TB0_11 1, TBp1_10 10, 
1 1   LB_RB0_S12_BP0B_CH1    W0/RB2out/12 Backward;  
1 2   LB_RB0_S12_BP0B_CH2    W0/RB1in/12 Backward;  
6 0   LB_RB+1_S12_BP1D_CH0    W+1/RB4/12+ Backward;  W+1/RB4/12- Backward;    TB0_10 11, TBp1_10 14, TB0_11 6, 
6 1
6 2   LB_RB+1_S12_BP1D_CH2    W+1/RB3/12+ Backward;  W+1/RB3/12- Backward;  
7 0
7 1
7 2
8 0
8 1
8 2
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RB+1_S1_BP1D_CH0    W+1/RB4/1+ Backward;  W+1/RB4/1- Backward;    TB0_11 11, TBp1_11 14, TB0_0 6, 
11 1
11 2   LB_RB+1_S1_BP1D_CH2    W+1/RB3/1+ Backward;  W+1/RB3/1- Backward;  
12 0   LB_RB-1_S1_BN1D_CH0    W-1/RB4/1+ Backward;  W-1/RB4/1- Backward;    TBn1_11 14, TB0_11 12, TBn1_0 3, 
12 1
12 2   LB_RB-1_S1_BN1D_CH2    W-1/RB3/1+ Backward;  W-1/RB3/1- Backward;  
13 0   LB_RB0_S1_BP0E_CH0    W0/RB4/1+ Backward;  W0/RB4/1- Backward;    TB0_11 13, 
13 1
13 2   LB_RB0_S1_BP0E_CH2    W0/RB3/1+ Backward;  W0/RB3/1- Backward;  
14 0   LB_RB0_S1_BN0E_CH0    W0/RB4/1+ Forward;  W0/RB4/1- Forward;    TB0_11 14, 
14 1
14 2   LB_RB0_S1_BN0E_CH2    W0/RB3/1+ Forward;  W0/RB3/1- Forward;  
15 0   LB_RB0_S1_BM0C_CH0    W0/RB2in/1 Central;    TB0_11 15, 
15 1   LB_RB0_S1_BM0C_CH1    W0/RB2in/1 Forward;  
15 2   LB_RB0_S1_BM0C_CH2    W0/RB2in/1 Backward;  
16 0   LB_RB0_S1_BP0B_CH0    W0/RB1out/1 Backward;    TB0_11 16, TB0_0 1, TBp1_11 10, 
16 1   LB_RB0_S1_BP0B_CH1    W0/RB2out/1 Backward;  
16 2   LB_RB0_S1_BP0B_CH2    W0/RB1in/1 Backward;  
17 0   LB_RB0_S1_BN0B_CH0    W0/RB1out/1 Forward;    TB0_11 17, TB0_0 0, TBn1_11 10, 
17 1   LB_RB0_S1_BN0B_CH1    W0/RB2out/1 Forward;  
17 2   LB_RB0_S1_BN0B_CH2    W0/RB1in/1 Forward;  

TBp1_11
1 0   LB_RB+1_S12_BP1B_CH0    W+1/RB1out/12 Forward;    TBp1_10 16, TBp2_10 12, TBp1_11 1, 
1 1   LB_RB+1_S12_BP1B_CH1    W+1/RB2out/12 Forward;  
1 2   LB_RB+1_S12_BP1B_CH2    W+1/RB1in/12 Forward;  
5 0   LB_RB+2_S12_BP2D_CH0    W+2/RB4/12+ Backward;  W+2/RB4/12- Backward;    TBp1_10 12, TBp2_10 14, TBp1_11 5, 
5 1
5 2   LB_RB+2_S12_BP2D_CH2    W+2/RB3/12+ Backward;  W+2/RB3/12- Backward;  
6 0   LB_RB+2_S12_BP2E_CH0    W+2/RB4/12+ Forward;  W+2/RB4/12- Forward;    TBp1_10 11, TBp2_10 13, TBp1_11 6, TBp2_11 4, 
6 1
6 2   LB_RB+2_S12_BP2E_CH2    W+2/RB3/12+ Forward;  W+2/RB3/12- Forward;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RB0_S1_BP0B_CH0    W0/RB1out/1 Backward;    TB0_11 16, TB0_0 1, TBp1_11 10, 
10 1   LB_RB0_S1_BP0B_CH1    W0/RB2out/1 Backward;  
10 2   LB_RB0_S1_BP0B_CH2    W0/RB1in/1 Backward;  
11 0   LB_RB+2_S1_BP2E_CH0    W+2/RB4/1+ Forward;  W+2/RB4/1- Forward;    TBp1_11 11, TBp2_11 13, 
11 1
11 2   LB_RB+2_S1_BP2E_CH2    W+2/RB3/1+ Forward;  W+2/RB3/1- Forward;  
12 0   LB_RB+2_S1_BP2D_CH0    W+2/RB4/1+ Backward;  W+2/RB4/1- Backward;    TBp1_11 12, TBp2_11 14, TBp1_0 5, 
12 1
12 2   LB_RB+2_S1_BP2D_CH2    W+2/RB3/1+ Backward;  W+2/RB3/1- Backward;  
13 0   LB_RB+1_S1_BP1E_CH0    W+1/RB4/1+ Forward;  W+1/RB4/1- Forward;    TBp1_11 13, 
13 1
13 2   LB_RB+1_S1_BP1E_CH2    W+1/RB3/1+ Forward;  W+1/RB3/1- Forward;  
14 0   LB_RB+1_S1_BP1D_CH0    W+1/RB4/1+ Backward;  W+1/RB4/1- Backward;    TB0_11 11, TBp1_11 14, TB0_0 6, 
14 1
14 2   LB_RB+1_S1_BP1D_CH2    W+1/RB3/1+ Backward;  W+1/RB3/1- Backward;  
15 0   LB_RB+1_S1_BP1C_CH0    W+1/RB2in/1 Central;    TBp1_11 15, 
15 1   LB_RB+1_S1_BP1C_CH1    W+1/RB2in/1 Forward;  
15 2   LB_RB+1_S1_BP1C_CH2    W+1/RB2in/1 Backward;  
16 0   LB_RB+1_S1_BP1B_CH0    W+1/RB1out/1 Forward;    TBp1_11 16, TBp2_11 12, TBp1_0 1, 
16 1   LB_RB+1_S1_BP1B_CH1    W+1/RB2out/1 Forward;  
16 2   LB_RB+1_S1_BP1B_CH2    W+1/RB1in/1 Forward;  
17 0   LB_RB+1_S1_BP1A_CH0    W+1/RB1out/1 Backward;    TBp1_11 17, 
17 1   LB_RB+1_S1_BP1A_CH1    W+1/RB2out/1 Backward;  
17 2   LB_RB+1_S1_BP1A_CH2    W+1/RB1in/1 Backward;  

TBp2_11
0 0   LB_RB+2_S12_BP2A_CH0    W+2/RB1out/12 Backward;    TBp2_10 17, TBp2_11 0, 
0 1   LB_RB+2_S12_BP2A_CH1    W+2/RB2in/12 Backward;  
0 2   LB_RB+2_S12_BP2A_CH2    W+2/RB1in/12 Backward;  
1 0   LB_RB+2_S12_BP2B_CH0    W+2/RB1out/12 Forward;    TBp2_10 16, TBp3_10 16, TBp2_11 1, TBp3_11 1, 
1 1   LB_RB+2_S12_BP2B_CH1    W+2/RB2in/12 Forward;  
1 2   LB_RB+2_S12_BP2B_CH2    W+2/RB1in/12 Forward;  
2 0   LB_RB+2_S12_BP2C_CH0    W+2/RB2out/12 Central;    TBp2_10 15, TBp2_11 2, 
2 1   LB_RB+2_S12_BP2C_CH1    W+2/RB2out/12 Forward;  
2 2   LB_RB+2_S12_BP2C_CH2    W+2/RB2out/12 Backward;  
4 0   LB_RB+2_S12_BP2E_CH0    W+2/RB4/12+ Forward;  W+2/RB4/12- Forward;    TBp1_10 11, TBp2_10 13, TBp1_11 6, TBp2_11 4, 
4 1
4 2   LB_RB+2_S12_BP2E_CH2    W+2/RB3/12+ Forward;  W+2/RB3/12- Forward;  
6 0   LB_RE+1_S12_EP23_CH0    RE+2/3/36;    TBp2_11 6, TBp3_11 6, 
6 1   LB_RE+1_S12_EP23_CH1    RE+2/3/35;  
6 2   LB_RE+1_S12_EP23_CH2    RE+2/3/1;  
7 0   LB_RE+1_S12_EP13_CH0    RE+1/3/34;    TBp2_10 10, TBp2_11 7, 
7 1   LB_RE+1_S12_EP13_CH1    RE+1/3/33;  
7 2   LB_RE+1_S12_EP13_CH2    RE+1/3/35;  
8 0
8 1
8 2
9 0
9 1
9 2
10 0   LB_RE+1_S1_EP13_CH0    RE+1/3/1;    TBp2_11 10, TBp2_0 7, 
10 1   LB_RE+1_S1_EP13_CH1    RE+1/3/36;  
10 2   LB_RE+1_S1_EP13_CH2    RE+1/3/2;  
11 0
11 1
11 2
12 0   LB_RB+1_S1_BP1B_CH0    W+1/RB1out/1 Forward;    TBp1_11 16, TBp2_11 12, TBp1_0 1, 
12 1   LB_RB+1_S1_BP1B_CH1    W+1/RB2out/1 Forward;  
12 2   LB_RB+1_S1_BP1B_CH2    W+1/RB1in/1 Forward;  
13 0   LB_RB+2_S1_BP2E_CH0    W+2/RB4/1+ Forward;  W+2/RB4/1- Forward;    TBp1_11 11, TBp2_11 13, 
13 1
13 2   LB_RB+2_S1_BP2E_CH2    W+2/RB3/1+ Forward;  W+2/RB3/1- Forward;  
14 0   LB_RB+2_S1_BP2D_CH0    W+2/RB4/1+ Backward;  W+2/RB4/1- Backward;    TBp1_11 12, TBp2_11 14, TBp1_0 5, 
14 1
14 2   LB_RB+2_S1_BP2D_CH2    W+2/RB3/1+ Backward;  W+2/RB3/1- Backward;  
15 0   LB_RB+2_S1_BP2C_CH0    W+2/RB2out/1 Central;    TBp2_11 15, 
15 1   LB_RB+2_S1_BP2C_CH1    W+2/RB2out/1 Forward;  
15 2   LB_RB+2_S1_BP2C_CH2    W+2/RB2out/1 Backward;  
16 0   LB_RB+2_S1_BP2B_CH0    W+2/RB1out/1 Forward;    TBp2_11 16, TBp3_11 16, 
16 1   LB_RB+2_S1_BP2B_CH1    W+2/RB2in/1 Forward;  
16 2   LB_RB+2_S1_BP2B_CH2    W+2/RB1in/1 Forward;  
17 0   LB_RB+2_S1_BP2A_CH0    W+2/RB1out/1 Backward;    TBp2_11 17, 
17 1   LB_RB+2_S1_BP2A_CH1    W+2/RB2in/1 Backward;  
17 2   LB_RB+2_S1_BP2A_CH2    W+2/RB1in/1 Backward;  

TBp3_11
0 0
0 1
0 2
1 0   LB_RB+2_S12_BP2B_CH0    W+2/RB1out/12 Forward;    TBp2_10 16, TBp3_10 16, TBp2_11 1, TBp3_11 1, 
1 1   LB_RB+2_S12_BP2B_CH1    W+2/RB2in/12 Forward;  
1 2   LB_RB+2_S12_BP2B_CH2    W+2/RB1in/12 Forward;  
2 0   LB_RE+1_S12_EP12_CH0    RE+1/2/34;    TBp3_10 15, TBp3_11 2, 
2 1   LB_RE+1_S12_EP12_CH1    RE+1/2/33;  
2 2   LB_RE+1_S12_EP12_CH2    RE+1/2/35;  
3 0   LB_RE+3_S12_EP32_CH0    RE+3/2/34;    TBp3_10 14, TBp3_11 3, 
3 1   LB_RE+3_S12_EP32_CH1    RE+3/2/33;  
3 2   LB_RE+3_S12_EP32_CH2    RE+3/2/35;  
4 0   LB_RE+3_S12_EP33_CH0    RE+3/3/34;    TBp3_10 13, TBp3_11 4, 
4 1   LB_RE+3_S12_EP33_CH1    RE+3/3/33;  
4 2   LB_RE+3_S12_EP33_CH2    RE+3/3/35;  
5 0   LB_RE+1_S12_EP22_CH0    RE+2/2/36;    TBp3_11 5, 
5 1   LB_RE+1_S12_EP22_CH1    RE+2/2/35;  
5 2   LB_RE+1_S12_EP22_CH2    RE+2/2/1;  
6 0   LB_RE+1_S12_EP23_CH0    RE+2/3/36;    TBp2_11 6, TBp3_11 6, 
6 1   LB_RE+1_S12_EP23_CH1    RE+2/3/35;  
6 2   LB_RE+1_S12_EP23_CH2    RE+2/3/1;  
7 0   LB_RE+4_S12_EP42_CH0    RE+4/2/34;    TBp3_10 12, TBp3_11 7, 
7 1   LB_RE+4_S12_EP42_CH1    RE+4/2/33;  
7 2   LB_RE+4_S12_EP42_CH2    RE+4/2/35;  
8 0   LB_RE+4_S12_EP43_CH0    RE+4/3/34;    TBp3_10 11, TBp3_11 8, 
8 1   LB_RE+4_S12_EP43_CH1    RE+4/3/33;  
8 2   LB_RE+4_S12_EP43_CH2    RE+4/3/35;  
9 0
9 1
9 2
10 0
10 1
10 2
11 0   LB_RE+4_S1_EP43_CH0    RE+4/3/1;    TBp3_0 8, TBp3_11 11, 
11 1   LB_RE+4_S1_EP43_CH1    RE+4/3/36;  
11 2   LB_RE+4_S1_EP43_CH2    RE+4/3/2;  
15 0   LB_RE+1_S1_EP12_CH0    RE+1/2/1;    TBp3_11 15, 
15 1   LB_RE+1_S1_EP12_CH1    RE+1/2/36;  
15 2   LB_RE+1_S1_EP12_CH2    RE+1/2/2;  
16 0   LB_RB+2_S1_BP2B_CH0    W+2/RB1out/1 Forward;    TBp2_11 16, TBp3_11 16, 
16 1   LB_RB+2_S1_BP2B_CH1    W+2/RB2in/1 Forward;  
16 2   LB_RB+2_S1_BP2B_CH2    W+2/RB1in/1 Forward;  
17 0
17 1
17 2

```
    
</details> 