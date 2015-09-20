---
title: Getting some data and building a decoder
layout: post
---
If you've followed my recent posts so far we've built a little hibernate app in eclipse to write amateur radio (ham) call signs to a mysql database. Next it would be nice to get a source of data.  
  
The [APRS-IS][1] (Automatic Packet Reporting System-Internet Service) channels data from APRS networks from around the world and gives us a access to a wealth of location reports from around the world.  
  
From the APRS-IS website, follow the link Services->APRS Service->APRS-IS Servers to get a list of all servers listed by region. Don't use any of the core servers. After browsing through the list of servers I found that I couldn't connect to several, and some others required you to have already registered with them.  
  
One server I found was aprsdcsp.kc7zru.net on port 10152. If you use this server, don't overdo it. To see the type of data returned from the server telnet to it:  

```
telnet aprsdcsp.kc7zru.net 10152
```
  
This is some output I captured:

```
/home/user> ./telnet aprsdcsp.kc7zru.net 10152
Trying 67.40.120.251...
Connected to kc7zru.net.
Escape character is '^]'.
# aprsd 2.2.5-15 Oct 2002 aprsd group
aprsdCSP>APD225,TCPIP*:!4249.72N/10624.04WIPHG7130/A=005156 Casper, WY aprsd server & I-gate
# **PLEASE EDIT THIS TO SUIT YOUR LOCAL REQUIRMENTS. Start each comment line with a "#"   **
Welcome to the KC7ZRU Rocky Mnt Regional APRS server.
Direct comments to 'kc7zru "at" arrl "dot" net'
Type ctrl-D to disconnect.
apdCSP-2>JAVA::javaTITLE:Live, local APRS data from Casper, Wyoming [DN62]
W8FSM-15>APVR30,TCPIP*,qAC,T2NUENGLD:;IRLP-4924*090535z4233.22NI08420.47W0444575+107IDLE     
WX4BB-1>APW285,KR4XN-2*,WIDE2-1,qAR,KE4TTA-2:>061701 Warrenville, SC  WX observations
W5NRU-13>APVR30,TCPIP*,qAC,T2MIDWEST:;IRLP-5980*090535z3131.14NI08907.23W0145.230MHz T136 R40m IDLE   
KA1MZY-15>APVR30,TCPIP*,qAC,T2MCI:;IRLP-3221*090535z2932.95NI09819.71W0147.515MHz T225 R20m IDLE     
KC6VVT>W9MKS-15,qAR,W9MKS-15:;147.120IL*111111z4111.41N/08858.88WrT103 R30m NetW7pm Mtg1M7pm
KA1EKS-8>APNU19,W1PIG*,WIDE4-2,qAS,N1ZRL-1:!4537.12NS06852.97W#PHG2260 W4,MEn SouthTwinLake Maine 145.25-
N4HAJ>APU25N,TCPIP*,qAC,T2MIDWEST:=3517.73N/07739.83WI {UIV32N}
DB0RDT-13>APRS,TCPIP*,qAC,T2NZAA:;IRLP5822 *090535z4849.95NC01117.57E0439.150MHz, -7600 offset : LINK CLEAR
N3KTX-7>APN383,qAR,N3UJJ:!3856.34NS07603.76W#PHG7580 W3.MDn   MasonDixon Group
DL1GKR>BEACON,TCPIP*,qAS,DL1GKR-5:;IRE4222  *090534z4809.36N/00948.81E=000/000/A=001712 IRE4222 km125
K5LLL-13>APU25N,WIDE2-2,qAR,KE5C-15:=3014.77N/09709.61W#W-R-T Digi/WX for McDade, TX {UIV32N}
IW9BGV-9>SX0Y47,IR9AK*,WIDE2-1,qAr,IT9OBK*:`)/Ul >/
HS6VW>APU25N,TCPIP*,qAC,T2THAPR-2:=1841.40N/09856.52E-BanTawai  HangDong  ChiangMai Thailand
VE2SSG>TV4R7Y,WIDE1-1,WIDE2-1,qAO,VE2MGJ:`c<{l!Lu/"43} F6BIG-9>APZ16,WIDE,WIDE,qAS,HB3YNV:!4547.30N/00606.00E#PHG1960/Mt.Semnoz TEST APRS DIGIPEATER (UIDIGI 1.6)
HG3PMF-1>APZ186,WIDE7-5,qAR,HG1DFB-7:!4606.53N/01812.18E#PHG3230/APRS DIGIPEATER PECS-TUBES SysOp:HG3SK
W2LV>APNPXX,qAR,N0IGD-1:!4111.55N/07445.19W#PHG4730/11,22,21,33 Sussex County ARC DIGI
N5SKU-15>APRS,TCPIP*,qAC,T2NZAA:;IRLP3382 *090535z3259.52NO09643.74W&446.125MHz, - offset PL tone 123.0: NODE OFFLINE
K4GIG-7>APZ19,qAR,N8DEU-5:!3424.30NS08648.02W#PHG5670/W2,ALn    Wilson Mtn AL 1360ft
KA8ZGE>APU25N,TCPIP*,qAC,T2APRSFL:=4052.85NS08435.96W#PHG51304/Wn,BCn/Van Wert {UIV32N}
KB9OQF>APAGW,WIDE3-2,qAR,KA8ZGE:=4031.63N/08530.25W-PHG7180Upland, Indiana - kb9oqf@aol.com
DB0XIK>APU25N,TCPIP*,qAC,T2SOLO:=4900.73N/00824.76ErAPRS E-Lnk D-STAR   Projekt
UA5ERC-15>APNU19,EA5RCI-14*,TRACE4-4,qAS,EA5CJA:!3931.13N/00059.27W#PHG2110/RCE Resuena. ea5e�@ea5erc.myvoc.com
WLNK-1>APRS,TCPIP*,qAC,T2MCI:=3853.75N/07702.50W?APRSLink - www.winlink.org/aprslink
G7HEJ>APU25N,WIDE2-2,qAR,G1EUH:=5349.81N/00302.47W-If you hear me email me. g7hej@yahoo.com
KE5GLK-1>APRS,TCPIP*,qAC,FIRST:@090534z2902.60N/09524.97W_125/001g...t070r...p...P000h97b10220.DsVP
VE6TVN>APOT21,EDMNTN*,WIDE2-1,qAR,VE6GPS:!5332.57N/11325.59W>
GB7DXC>APZS05,TCPIP*,qAC,T2IRELAND:/090535z5201.8 N/00206.4 W%Bredon [IO82WA]
```
  
Lets convert the existing eclipse project to grab data from this server, decode it and generate call sign records. Alternatively, grab a zipped copy of the eclipse project [here][2].  
  
Create package com.foomoo.aprs.decoder. Add the following 4 files to this package.  
  
DecodedItem.java

```java
package com.foomoo.aprs.decoder;

public abstract class DecodedItem {
 public abstract String toString();
}
```
  
Decoder.java

```java
package com.foomoo.aprs.decoder;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collection;

public abstract class Decoder extends Thread {

 public static int DEFAULT_SLEEP_TIME = 10;

 BufferedReader br;
 int sleepTime = DEFAULT_SLEEP_TIME;
 private boolean keepRunning = true;

 ArrayList decodedItems = new ArrayList();
 
 public Decoder(InputStream is) {
  br = new BufferedReader(new InputStreamReader(is));
 }

 public void run() {
  while (keepRunning) {
   try {
    String searchString = br.readLine();
    while(null != searchString && keepRunning) {
     Collection items = decode(searchString);
     decodedItems.addAll(items);
     searchString = br.readLine();
    }
    
    try {
     sleep(sleepTime);
    } catch (InterruptedException e) {
     e.printStackTrace();
    }
   } catch (IOException e) {
    e.printStackTrace();
    keepRunning = false;
   }
  }
 }

 protected abstract Collection decode(String searchString);
 
 public boolean hasItems() {
  return !decodedItems.isEmpty();
 }
 
 public DecodedItem getItem() {
  if (!decodedItems.isEmpty()) {
   return decodedItems.remove(0);
  }
  return null;
 }

 public void stopRunning() {
  keepRunning = false;
 }
}
```

CallsignDecodedItem.java

```java
package com.foomoo.aprs.decoder;

public class CallsignDecodedItem extends DecodedItem {

 String callsign;

 @Override
 public String toString() {
  return callsign;
 }

 public String getCallsign() {
  return callsign;
 }

 public void setCallsign(String callsign) {
  this.callsign = callsign;
 }
}
```

AprsisCallsignDecoder.java  

```java
package com.foomoo.aprs.decoder;

import java.io.InputStream;
import java.util.ArrayList;
import java.util.Collection;

public class AprsisCallsignDecoder extends Decoder {

 public AprsisCallsignDecoder(InputStream is) {
  super(is);
 }

 @Override
 protected Collection decode(String searchString) {
     ArrayList retList = new ArrayList();
  String splitString [] = searchString.split (">");
        if (splitString.length > 1) {
         CallsignDecodedItem di = new CallsignDecodedItem();
         di.setCallsign(splitString[0]);
         
         retList.add(di);
        }
  return retList;
 }

}
```

Change Aprs.java to contain the following:

```java
package com.foomoo.aprs;

import java.io.IOException;
import java.io.InputStream;
import java.net.Socket;
import java.net.UnknownHostException;

import org.hibernate.Session;

import com.foomoo.aprs.data.Callsign;
import com.foomoo.aprs.data.util.HibernateUtil;
import com.foomoo.aprs.decoder.AprsisCallsignDecoder;
import com.foomoo.aprs.decoder.CallsignDecodedItem;

public class Aprs {

    private static int MAX_CALLSIGNS = 20;

    public static void main(String[] args) throws UnknownHostException,
            IOException {
        System.out.println("Hello, hibernate!");

        Socket socket = new Socket("aprsdcsp.kc7zru.net", 10152);
        InputStream is = socket.getInputStream();

        AprsisCallsignDecoder decoder = new AprsisCallsignDecoder(is);
        decoder.start();

        int count = 0;
        while (count < MAX_CALLSIGNS) {
            while (decoder.hasItems() && count < MAX_CALLSIGNS) {
                CallsignDecodedItem di = (CallsignDecodedItem) decoder
                        .getItem();

                Callsign c = new Callsign();
                c.setCallsign(di.getCallsign());
                System.out.println(di.getCallsign());
                Session session = HibernateUtil.getSession();
                session.beginTransaction();
                session.save(c);
                session.getTransaction().commit();

                count++;
            }

            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        decoder.stopRunning();
    }
}
```

Run the program, if a connection is made to the server, the program should run until 20 callsign records are created. Check the contents of the Callsign table after running to observe that 20 callsigns have been captured.

 [1]: http://www.aprs-is.net/
 [2]: http://www.foomoo.co.uk/aprscallsigndecoder.zip