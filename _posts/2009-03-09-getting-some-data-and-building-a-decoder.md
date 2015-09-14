---
ID: 25
post_title: Getting some data and building a decoder
author: Daniel Watford
post_date: 2009-03-09 04:24:00
post_excerpt: ""
layout: post
permalink: >
  http://www.foomoo.com/2009/03/getting-some-data-and-building-a-decoder/
published: true
blogger_blog:
  - www.foomoo.com
blogger_author:
  - >
    Dan
    Watfordhttp://www.blogger.com/profile/12858969159201603290noreply@blogger.com
blogger_permalink:
  - >
    /2009/03/getting-some-data-and-building-decoder.html
---
If you've followed my recent posts so far we've built a little hibernate app in eclipse to write amateur radio (ham) call signs to a mysql database. Next it would be nice to get a source of data.  
  
The [APRS-IS][1] (Automatic Packet Reporting System-Internet Service) channels data from APRS networks from around the world and gives us a access to a wealth of location reports from around the world.  
  
From the APRS-IS website, follow the link Services->APRS Service->APRS-IS Servers to get a list of all servers listed by region. Don't use any of the core servers. After browsing through the list of servers I found that I couldn't connect to several, and some others required you to have already registered with them.  
  
One server I found was aprsdcsp.kc7zru.net on port 10152. If you use this server, don't overdo it. To see the type of data returned from the server telnet to it:  
<pre>telnet aprsdcsp.kc7zru.net 10152</pre>

  
  
This is some output I captured:  
<pre><br />/home/user> ./telnet aprsdcsp.kc7zru.net 10152<br />Trying 67.40.120.251...<br />Connected to kc7zru.net.<br />Escape character is '^]'.<br /># aprsd 2.2.5-15 Oct 2002 aprsd group<br />aprsdCSP>APD225,TCPIP*:!4249.72N/10624.04WIPHG7130/A=005156 Casper, WY aprsd server & I-gate<br /># **PLEASE EDIT THIS TO SUIT YOUR LOCAL REQUIRMENTS. Start each comment line with a "#"   **<br />Welcome to the KC7ZRU Rocky Mnt Regional APRS server.<br />Direct comments to 'kc7zru "at" arrl "dot" net'<br />Type ctrl-D to disconnect.<br />apdCSP-2>JAVA::javaTITLE:Live, local APRS data from Casper, Wyoming [DN62]<br />W8FSM-15>APVR30,TCPIP*,qAC,T2NUENGLD:;IRLP-4924*090535z4233.22NI08420.47W0444575+107IDLE     <br />WX4BB-1>APW285,KR4XN-2*,WIDE2-1,qAR,KE4TTA-2:>061701 Warrenville, SC  WX observations<br />W5NRU-13>APVR30,TCPIP*,qAC,T2MIDWEST:;IRLP-5980*090535z3131.14NI08907.23W0145.230MHz T136 R40m IDLE   <br />KA1MZY-15>APVR30,TCPIP*,qAC,T2MCI:;IRLP-3221*090535z2932.95NI09819.71W0147.515MHz T225 R20m IDLE     <br />KC6VVT>W9MKS-15,qAR,W9MKS-15:;147.120IL*111111z4111.41N/08858.88WrT103 R30m NetW7pm Mtg1M7pm<br />KA1EKS-8>APNU19,W1PIG*,WIDE4-2,qAS,N1ZRL-1:!4537.12NS06852.97W#PHG2260 W4,MEn SouthTwinLake Maine 145.25-<br />N4HAJ>APU25N,TCPIP*,qAC,T2MIDWEST:=3517.73N/07739.83WI {UIV32N}<br />DB0RDT-13>APRS,TCPIP*,qAC,T2NZAA:;IRLP5822 *090535z4849.95NC01117.57E0439.150MHz, -7600 offset : LINK CLEAR<br />N3KTX-7>APN383,qAR,N3UJJ:!3856.34NS07603.76W#PHG7580 W3.MDn   MasonDixon Group<br />DL1GKR>BEACON,TCPIP*,qAS,DL1GKR-5:;IRE4222  *090534z4809.36N/00948.81E=000/000/A=001712 IRE4222 km125<br />K5LLL-13>APU25N,WIDE2-2,qAR,KE5C-15:=3014.77N/09709.61W#W-R-T Digi/WX for McDade, TX {UIV32N}<br />IW9BGV-9>SX0Y47,IR9AK*,WIDE2-1,qAr,IT9OBK*:`)/Ul >/<br />HS6VW>APU25N,TCPIP*,qAC,T2THAPR-2:=1841.40N/09856.52E-BanTawai  HangDong  ChiangMai Thailand<br />VE2SSG>TV4R7Y,WIDE1-1,WIDE2-1,qAO,VE2MGJ:`c&lt;{l!Lu/"43} F6BIG-9>APZ16,WIDE,WIDE,qAS,HB3YNV:!4547.30N/00606.00E#PHG1960/Mt.Semnoz TEST APRS DIGIPEATER (UIDIGI 1.6)<br />HG3PMF-1>APZ186,WIDE7-5,qAR,HG1DFB-7:!4606.53N/01812.18E#PHG3230/APRS DIGIPEATER PECS-TUBES SysOp:HG3SK<br />W2LV>APNPXX,qAR,N0IGD-1:!4111.55N/07445.19W#PHG4730/11,22,21,33 Sussex County ARC DIGI<br />N5SKU-15>APRS,TCPIP*,qAC,T2NZAA:;IRLP3382 *090535z3259.52NO09643.74W&446.125MHz, - offset PL tone 123.0: NODE OFFLINE<br />K4GIG-7>APZ19,qAR,N8DEU-5:!3424.30NS08648.02W#PHG5670/W2,ALn    Wilson Mtn AL 1360ft<br />KA8ZGE>APU25N,TCPIP*,qAC,T2APRSFL:=4052.85NS08435.96W#PHG51304/Wn,BCn/Van Wert {UIV32N}<br />KB9OQF>APAGW,WIDE3-2,qAR,KA8ZGE:=4031.63N/08530.25W-PHG7180Upland, Indiana - kb9oqf@aol.com<br />DB0XIK>APU25N,TCPIP*,qAC,T2SOLO:=4900.73N/00824.76ErAPRS E-Lnk D-STAR   Projekt<br />UA5ERC-15>APNU19,EA5RCI-14*,TRACE4-4,qAS,EA5CJA:!3931.13N/00059.27W#PHG2110/RCE Resuena. ea5e�@ea5erc.myvoc.com<br />WLNK-1>APRS,TCPIP*,qAC,T2MCI:=3853.75N/07702.50W?APRSLink - www.winlink.org/aprslink<br />G7HEJ>APU25N,WIDE2-2,qAR,G1EUH:=5349.81N/00302.47W-If you hear me email me. g7hej@yahoo.com<br />KE5GLK-1>APRS,TCPIP*,qAC,FIRST:@090534z2902.60N/09524.97W_125/001g...t070r...p...P000h97b10220.DsVP<br />VE6TVN>APOT21,EDMNTN*,WIDE2-1,qAR,VE6GPS:!5332.57N/11325.59W><br />GB7DXC>APZS05,TCPIP*,qAC,T2IRELAND:/090535z5201.8 N/00206.4 W%Bredon [IO82WA]<br /></pre>

  
  
Lets convert the existing eclipse project to grab data from this server, decode it and generate call sign records. Alternatively, grab a zipped copy of the eclipse project [here][2].  
  
Create package com.foomoo.aprs.decoder. Add the following 4 files to this package.  
  
DecodedItem.java  
<pre>package com.foomoo.aprs.decoder;<br /><br />public abstract class DecodedItem {<br /> public abstract String toString();<br />}</pre>

  
  
Decoder.java  
<pre>package com.foomoo.aprs.decoder;<br /><br />import java.io.BufferedReader;<br />import java.io.IOException;<br />import java.io.InputStream;<br />import java.io.InputStreamReader;<br />import java.util.ArrayList;<br />import java.util.Collection;<br /><br />public abstract class Decoder extends Thread {<br /><br /> public static int DEFAULT_SLEEP_TIME = 10;<br /><br /> BufferedReader br;<br /> int sleepTime = DEFAULT_SLEEP_TIME;<br /> private boolean keepRunning = true;<br /><br /> ArrayList&lt;DecodedItem> decodedItems = new ArrayList&lt;DecodedItem>();<br /> <br /> public Decoder(InputStream is) {<br />  br = new BufferedReader(new InputStreamReader(is));<br /> }<br /><br /> public void run() {<br />  while (keepRunning) {<br />   try {<br />    String searchString = br.readLine();<br />    while(null != searchString && keepRunning) {<br />     Collection&lt;DecodedItem> items = decode(searchString);<br />     decodedItems.addAll(items);<br />     searchString = br.readLine();<br />    }<br />    <br />    try {<br />     sleep(sleepTime);<br />    } catch (InterruptedException e) {<br />     e.printStackTrace();<br />    }<br />   } catch (IOException e) {<br />    e.printStackTrace();<br />    keepRunning = false;<br />   }<br />  }<br /> }<br /><br /> protected abstract Collection&lt;DecodedItem> decode(String searchString);<br /> <br /> public boolean hasItems() {<br />  return !decodedItems.isEmpty();<br /> }<br /> <br /> public DecodedItem getItem() {<br />  if (!decodedItems.isEmpty()) {<br />   return decodedItems.remove(0);<br />  }<br />  return null;<br /> }<br /><br /> public void stopRunning() {<br />  keepRunning = false;<br /> }<br />}</pre>

  
  
CallsignDecodedItem.java  
<pre>package com.foomoo.aprs.decoder;<br /><br />public class CallsignDecodedItem extends DecodedItem {<br /><br /> String callsign;<br /><br /> @Override<br /> public String toString() {<br />  return callsign;<br /> }<br /><br /> public String getCallsign() {<br />  return callsign;<br /> }<br /><br /> public void setCallsign(String callsign) {<br />  this.callsign = callsign;<br /> }<br />}</pre>

  
  
AprsisCallsignDecoder.java  
<pre>package com.foomoo.aprs.decoder;<br /><br />import java.io.InputStream;<br />import java.util.ArrayList;<br />import java.util.Collection;<br /><br />public class AprsisCallsignDecoder extends Decoder {<br /><br /> public AprsisCallsignDecoder(InputStream is) {<br />  super(is);<br /> }<br /><br /> @Override<br /> protected Collection&lt;decodedItem> decode(String searchString) {<br />     ArrayList&lt;decodedItem> retList = new ArrayList&lt;decodedItem>();<br />  String splitString [] = searchString.split (">");<br />        if (splitString.length > 1) {<br />         CallsignDecodedItem di = new CallsignDecodedItem();<br />         di.setCallsign(splitString[0]);<br />         <br />         retList.add(di);<br />        }<br />  return retList;<br /> }<br /><br />}</pre>

  
  
Change Aprs.java to contain the following:  
<pre>package com.foomoo.aprs;<br /><br />import java.io.IOException;<br />import java.io.InputStream;<br />import java.net.Socket;<br />import java.net.UnknownHostException;<br /><br />import org.hibernate.Session;<br /><br />import com.foomoo.aprs.data.Callsign;<br />import com.foomoo.aprs.data.util.HibernateUtil;<br />import com.foomoo.aprs.decoder.AprsisCallsignDecoder;<br />import com.foomoo.aprs.decoder.CallsignDecodedItem;<br /><br />public class Aprs {<br /><br />    private static int MAX_CALLSIGNS = 20;<br /><br />    public static void main(String[] args) throws UnknownHostException,<br />            IOException {<br />        System.out.println("Hello, hibernate!");<br /><br />        Socket socket = new Socket("aprsdcsp.kc7zru.net", 10152);<br />        InputStream is = socket.getInputStream();<br /><br />        AprsisCallsignDecoder decoder = new AprsisCallsignDecoder(is);<br />        decoder.start();<br /><br />        int count = 0;<br />        while (count &lt; MAX_CALLSIGNS) {<br />            while (decoder.hasItems() && count &lt; MAX_CALLSIGNS) {<br />                CallsignDecodedItem di = (CallsignDecodedItem) decoder<br />                        .getItem();<br /><br />                Callsign c = new Callsign();<br />                c.setCallsign(di.getCallsign());<br />                System.out.println(di.getCallsign());<br />                Session session = HibernateUtil.getSession();<br />                session.beginTransaction();<br />                session.save(c);<br />                session.getTransaction().commit();<br /><br />                count++;<br />            }<br /><br />            try {<br />                Thread.sleep(10);<br />            } catch (InterruptedException e) {<br />                e.printStackTrace();<br />            }<br />        }<br />        decoder.stopRunning();<br />    }<br />}</pre>

  
  
Run the program, if a connection is made to the server, the program should run until 20 callsign records are created. Check the contents of the Callsign table after running to observe that 20 callsigns have been captured.

 [1]: http://www.aprs-is.net/
 [2]: http://www.foomoo.co.uk/aprscallsigndecoder.zip