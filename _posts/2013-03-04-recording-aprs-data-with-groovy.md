---
ID: 176
post_title: Recording APRS data with Groovy
author: Daniel Watford
post_date: 2013-03-04 23:14:48
post_excerpt: ""
layout: post
permalink: >
  http://www.foomoo.com/2013/03/recording-aprs-data-with-groovy/
published: true
wpgplus_message:
  - ""
wpgplus_publish:
  - 'yes'
---
Post [APRSParser - A Spring Example][1] included a class, SocketAPRSDataSource, which when coupled with class DataSourceCapture would capture APRS data from and APRS-IS server to a file for playback in later development. Listing 1 contains a quick Groovy script to do the same thing. It can probably be refined a bit should demonstrate how powerful groovy is for quick prototyping. **Listing 1** <pre class="brush: groovy">/* Open a socket to an APRS-IS server, log in as guest and filter to only receive
 * messages from call signs prefixed with G, M or 2.
 * Write all messages to a file with a time stamp.
 */

def writeLine(writer, line) {
	def date = new Date()
	lineOutput = date.format("yyyy-MM-dd'T'HH:mm:ss,", TimeZone.getTimeZone("UTC")) + line
	println(lineOutput)
	writer.println(lineOutput)
	writer.flush()
}

new File("APRSData.txt").withPrintWriter { writer ->
	// Using a UK server. See http://www.aprs2.net/serverstats.php for other servers.
	s = new Socket("uk.aprs2.net", 14580);
	s.withStreams { input, output ->
		// Wait for the software version line from the server before logging in.
		writeLine(writer, input.newReader().readLine())
		
		// Identifying software as UI-View32. Would rather not do this but using other
		// strings seems to prevent successful login.
		output &lt;&lt; "user Guest pass -1 vers UI-View32 V2.03 filter p/G/M/2\n"
		
		// Record all lines until program killed or socket closed.
		input.newReader().eachLine { 
			writeLine(writer, it)
		}
	}
}</pre> .

 [1]: http://www.foomoo.com/2010/03/aprsparser-spring-example/ "APRSParser – A Spring Example"