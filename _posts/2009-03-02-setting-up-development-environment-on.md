---
title: Setting up a development environment on the Eee PC 901 Linux variant
layout: post
---
A couple of months ago I purchased an Eee PC 901. A couple of days ago I decided it was about time I set it up for a bit of Java development.

All discussions about whether the 901 is suitable for active development aside, it should be possible.

My Eee PC is still in its stock configuration. A lot of people will install a different OS, but I'm still using the ASUS Xandros Linux variant that came pre-installed.

First tool to be installed was eclipse. I downloaded package '<a href="http://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/ganymedesr1">Eclipse IDE for Java EE Developers</a>' from <a href="http://www.eclipse.org/">www.eclipse.org </a>to /home/user/eclipse-jee-ganymede-SR1-linux-gtk.tar.gz. To extract this file use Ctrl+Alt+t to open up a terminal and enter the following command:

~~~
tar zxfv eclipse-jee-ganymede-SR1-linux-gtk.tar.gz
~~~
This will extract the eclipse distribution to an eclipse directory. To launch eclipse from the same terminal window enter the command:

~~~
eclipse/eclipse
~~~

The JRE installed on the eeePC is 1.5.0.10. Eclipse will detect the available JRE and will setup code compliance to 1.5. Right now I don't have any need to go to 1.6 so won't worry about installing a new JRE.
